---
title: Autorisierungscodeflow – Azure Active Directory B2C
description: Erfahren Sie, wie Sie Web-Apps mithilfe von Azure AD B2C und dem Authentifizierungsprotokoll OpenID Connect erstellen.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: c48474f5285f9a2cb4646b46dad901df4a0a37d8
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037964"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>OAuth 2.0-Autorisierungscodefluss in Azure Active Directory B2C

Durch Gewähren des OAuth 2.0-Autorisierungcodes in Apps, die auf einem Gerät installiert sind, können Sie auf geschützte Ressourcen wie Web-APIs zugreifen. Mithilfe der Azure Active Directory B2C (Azure AD B2C)-Implementierung von OAuth 2.0 können Sie Ihren Single-Page-Webanwendungen, mobilen Apps und Desktop-Apps Aufgaben zur Registrierung, Anmeldung und sonstiger Identitätsverwaltung hinzufügen. Dieser Artikel ist sprachunabhängig. In dem Artikel wird beschrieben, wie HTTP-Nachrichten ohne Verwendung von Open Source-Bibliotheken gesendet und empfangen werden. Wir empfehlen Ihnen, nach Möglichkeit die unterstützten Microsoft-Authentifizierungsbibliotheken (MSAL) zu verwenden. Sehen Sie sich dazu die [Beispiel-Apps, die MSAL verwenden](integrate-with-app-code-samples.md), an.

Der OAuth 2.0-Autorisierungscodefluss wird in [Abschnitt 4.1 der OAuth 2.0-Spezifikation](https://tools.ietf.org/html/rfc6749)beschrieben. Sie können ihn zum Ausführen der Authentifizierung und Autorisierung bei den meisten [Anwendungstypen](application-types.md) verwenden, einschließlich Webanwendungen, Single-Page-Webanwendungen und nativ installierter Anwendungen. Über den OAuth 2.0-Autorisierungscodeflow können Sie Zugriffstoken und Aktualisierungstoken für Ihre Anwendungen auf sichere Weise abrufen, die für den Zugriff auf mit einem [Autorisierungsserver](protocols-overview.md) geschützte Ressourcen verwendet werden können.  Mit dem Aktualisierungstoken kann der Client neue Zugriffstoken (und Aktualisierungstoken) beschaffen, nachdem das Zugriffstoken abgelaufen ist. Dies ist meist nach einer Stunde der Fall.

Dieser Artikel behandelt den OAuth 2.0-Autorisierungscodeflow von **öffentlichen Clients**. Ein öffentlicher Client ist jede Clientanwendung, der nicht bei der sicheren Verwaltung der Integrität von geheimen Kennwörtern vertraut werden kann. Dazu gehören Single-Page-Webanwendungen, mobile Apps, Desktopanwendungen und im Grunde genommen jede beliebige Anwendung, die auf einem Server ausgeführt werden kann.

> [!NOTE]
> Wenn Sie in einer Web-App mit Azure AD B2C eine Identitätsverwaltung hinzufügen möchten, verwenden Sie [OpenID Connect](openid-connect.md) anstelle von OAuth 2.0.

Azure AD B2C erweitert den OAuth 2.0-Standardfluss, sodass mehr als nur eine einfache Authentifizierung und Autorisierung erfolgt. Dabei wird der [Benutzerflow](user-flow-overview.md) eingeführt. Mit Benutzerflows können Sie übe OAuth 2.0 Benutzeroberflächenfunktionen zu Ihrer Anwendung hinzufügen, z.B. für die Registrierung Anmeldung und Profilverwaltung. Zu den Identitätsanbietern, die das OAuth 2.0-Protokoll verwenden, zählen [Amazon](identity-provider-amazon.md), [Azure Active Directory](identity-provider-azure-ad-single-tenant.md), [Facebook](identity-provider-facebook.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md) und [LinkedIn](identity-provider-linkedin.md).

So testen Sie die HTTP-Anforderungen in diesem Artikel:

1. Ersetzen Sie `{tenant}` durch den Namen des Azure AD B2C-Mandanten.
1. Ersetzen Sie `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` durch die App-ID einer Anwendung, die Sie zuvor in Ihrem Azure AD B2C-Mandanten registriert haben.
1. Ersetzen Sie `{policy}` durch den Namen einer Richtlinie, die Sie in Ihrem Mandanten erstellt haben, z. B. `b2c_1_sign_in`.

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>Für Single-Page-Webanwendungen erforderliche Einrichtung eines Umleitungs-URI

Der Autorisierungscodeflow für Single-Page-Webanwendungen erfordert einige zusätzliche Einrichtungsschritte.  Befolgen Sie die Anweisungen zum [Erstellen Ihrer Single-Page-Webanwendung](tutorial-register-spa.md), um den Umleitungs-URI ordnungsgemäß als für CORS aktiviert zu kennzeichnen. Wenn Sie zum Aktivieren von CORS einen vorhandenen Umleitungs-URI aktualisieren möchten, können Sie auf der Registerkarte **Authentifizierung** der **App-Registrierung** im Abschnitt „Web“ auf die Option „migrieren“ klicken. Alternativ können Sie den **Manifest-Editor für App-Registrierungen** öffnen und im Abschnitt `replyUrlsWithType` das Feld `type` für Ihren Umleitungs-URI auf `spa` festlegen.

Der Umleitungstyp `spa` ist abwärtskompatibel mit dem impliziten Flow. Apps, die derzeit den impliziten Flow zum Abrufen von Token verwenden, können ohne Probleme auf den Umleitungs-URI-Typ `spa` umgestellt werden und den impliziten Flow weiterhin verwenden.

## <a name="1-get-an-authorization-code"></a>1. Abrufen von Autorisierungscodes
Der Autorisierungscodefluss beginnt damit, dass der Client den Benutzer auf den `/authorize` -Endpunkt leitet. Dies ist der interaktive Teil des Flows, bei dem der Benutzer tatsächlich Aktionen durchführt. In dieser Anforderung gibt der Client im Parameter `scope` die Berechtigungen an, die er vom Benutzer abrufen muss. In den folgenden drei Beispielen (mit Zeilenumbrüchen für bessere Lesbarkeit) wird jeweils ein anderer Benutzerflow verwendet.


```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| Parameter | Erforderlich? | BESCHREIBUNG |
| --- | --- | --- |
|{tenant}| Erforderlich | Name des Azure AD B2C-Mandanten.|
| {policy} | Erforderlich | Der auszuführende Benutzerflow. Geben Sie den Namen eines Benutzerflows an, den Sie in Ihrem Azure AD B2C-Mandanten erstellt haben. Beispiel: `b2c_1_sign_in`, `b2c_1_sign_up` oder `b2c_1_edit_profile`. |
| client_id |Erforderlich |Die Anwendungs-ID, die Ihrer App im [Azure-Portal](https://portal.azure.com) zugewiesen wird. |
| response_type |Erforderlich |Der Antworttyp, der `code` für den Autorisierungscodefluss enthalten muss. |
| redirect_uri |Erforderlich |Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden. Er muss genau mit einem der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| scope |Erforderlich |Eine durch Leerzeichen getrennte Liste von Bereichen. Der `openid`-Bereich gibt eine Berechtigung für das Anmelden des Benutzers und das Abrufen von Daten über den Benutzer in Form von ID-Token an. Der `offline_access`-Bereich ist für Webanwendungen optional. Er gibt an, dass Ihre Anwendung ein *Aktualisierungstoken* für den dauerhaften Zugriff auf Ressourcen benötigt. `https://{tenant-name}/{app-id-uri}/{scope}` gibt eine Berechtigung für geschützte Ressourcen (etwa eine Web-API) an. Weitere Informationen finden Sie im Artikel zum Anfordern eines Zugriffstokens in Azure Active Directory B2C unter [Bereiche](access-tokens.md#scopes). |
| response_mode |Empfohlen |Die Methode, die zum Senden des resultierenden Autorisierungscodes zurück an Ihre App verwendet wird. Es kann sich um `query`, `form_post` oder `fragment` handeln. |
| state |Empfohlen |Ein Wert in der Anforderung, der eine Zeichenfolge mit beliebigem Inhalt Ihrer Wahl sein kann. Normalerweise wird ein zufällig generierter eindeutiger Wert verwendet, um eine websiteübergreifende Anforderungsfälschung zu verhindern. Der Status wird außerdem verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist. Dies kann z.B. die vom Benutzer besuchte Seite oder der ausgeführte Benutzerflow sein. |
| prompt |Optional |Der Typ der erforderlichen Benutzerinteraktion. Gegenwärtig ist der einzige gültige Wert `login`, der den Benutzer zur Eingabe seiner Anmeldeinformationen für diese Anforderung zwingt. Einmaliges Anmelden wird nicht verwendet. |
| code_challenge  | Empfohlen/erforderlich | Wird verwendet, um die Gewährung von Autorisierungscodes über Proof Key for Code Exchange (PKCE) zu sichern. Erforderlich, wenn `code_challenge_method` enthalten ist. Weitere Informationen finden Sie unter [PKCE RFC](https://tools.ietf.org/html/rfc7636). Dies wird jetzt für alle Anwendungstypen (native Apps, SPAs und vertrauliche Clients wie Web-Apps) empfohlen. | 
| `code_challenge_method` | Empfohlen/erforderlich | Die Methode wird zum Codieren von `code_verifier` für den `code_challenge`-Parameter verwendet. Dieser *SOLLTE* `S256` lauten, die Spezifikation ermöglicht jedoch die Verwendung von `plain`, wenn der Client aus irgendeinem Grund SHA256 nicht unterstützt. <br/><br/>Wenn ausgeschlossen, wird angenommen, dass `code_challenge` Klartext ist, wenn `code_challenge` enthalten ist. Microsoft Identity Platform unterstützt sowohl `plain` als auch `S256`. Weitere Informationen finden Sie unter [PKCE RFC](https://tools.ietf.org/html/rfc7636). Dies ist für [Single-Page-Webanwendungen erforderlich, die den Autorisierungscodeflow verwenden](tutorial-register-spa.md).|
| login_hint | Nein| Kann zum Vorabausfüllen des Felds für den Anmeldenamen auf der Anmeldeseite verwendet werden. Weitere Informationen finden Sie unter [Auffüllen des Anmeldenamens](direct-signin.md#prepopulate-the-sign-in-name).  |
| domain_hint | Nein| Bietet Hinweis für Azure AD B2C zu dem sozialen Netzwerk als Identitätsanbieter, das für die Anmeldung verwendet werden soll. Wenn ein gültiger Wert enthalten ist, wird der Benutzer direkt auf die Anmeldeseite des Identitätsanbieters geleitet.  Weitere Informationen finden Sie unter [Umleiten einer Anmeldung zu einem Anbieter sozialer Netzwerke](direct-signin.md#redirect-sign-in-to-a-social-provider). |
| Benutzerdefinierte Parameter | Nein| Benutzerdefinierte Parameter, die mit [benutzerdefinierten Richtlinien](custom-policy-overview.md) verwendet werden können. Beispiele: [URI für dynamischen Seiteninhalt](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri) oder [OAuth2-Schlüssel-Wert-Parameter](claim-resolver-overview.md#oauth2-key-value-parameters). |

An diesem Punkt wird der Benutzer aufgefordert, den Workflow des Benutzerflows abzuschließen. Dafür muss der Benutzer z.B. den Benutzernamen und das Kennwort angeben, sich mit der Identität eines sozialen Netzwerks anmelden, sich für das Verzeichnis registrieren oder andere Schritte ausführen. Die Benutzeraktionen hängen davon ab, wie der Benutzerflow definiert ist.

Nachdem der Benutzer den Benutzerflow abgeschlossen hat, gibt Azure AD über den für `redirect_uri` verwendeten Wert eine Antwort an Ihre App zurück. Hierzu wird die im Parameter `response_mode` angegebene Methode verwendet. Die Antwort ist in den Benutzeraktionsszenarien immer gleich, unabhängig vom ausgeführten Benutzerflow.

Eine erfolgreiche Antwort mit `response_mode=query` sieht wie folgt aus:

```http
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| code |Der Autorisierungscode, den die App angefordert hat. Die App kann den Autorisierungscode zum Anfordern eines Zugriffstokens für eine Zielressource verwenden. Autorisierungscodes sind von sehr kurzer Lebensdauer. In der Regel laufen sie nach etwa 10 Minuten ab. |
| state |Die vollständige Beschreibung finden Sie im vorangehenden Abschnitt. Wenn ein Parameter `state` in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die App sollte überprüfen, ob die `state`-Werte in der Anforderung und in der Antwort identisch sind. |

Fehlerantworten können auch an den Umleitungs-URI gesendet werden, damit die App diese entsprechend behandeln kann:

```http
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| error |Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren. Sie können mit der Zeichenfolge auch auf Fehler reagieren. |
| error_description |Eine spezifische Fehlermeldung, mit der Sie die Hauptursache eines Authentifizierungsfehlers identifizieren können. |
| state |Die vollständige Beschreibung finden Sie in der obigen Tabelle. Wenn ein Parameter `state` in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die App sollte überprüfen, ob die `state`-Werte in der Anforderung und in der Antwort identisch sind. |

## <a name="2-get-an-access-token"></a>2. Abrufen eines Zugriffstokens
Nachdem Sie einen Autorisierungscode abgerufen haben, können Sie den `code` für ein Token für die gewünschte Ressource einlösen, indem Sie eine POST-Anforderung an den `/token`-Endpunkt senden. In Azure AD B2C können Sie [Zugriffstoken für andere APIs](access-tokens.md#request-a-token) wie gewohnt anfordern, indem Sie ihre Bereiche in der Anforderung angeben.

Sie haben auch die Möglichkeit, ein Zugriffstoken für die Web-API Ihres App-Back-Ends anzufordern, indem Sie die Client-ID der App als angeforderten Bereich verwenden. Dies führt dazu, dass ein Zugriffstoken mit dieser Client-ID als Zielgruppe erstellt wird:

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
```

| Parameter | Erforderlich? | BESCHREIBUNG |
| --- | --- | --- |
|{tenant}| Erforderlich | Name des Azure AD B2C-Mandanten.|
|{policy}| Erforderlich| Der Benutzerflow, der zum Abrufen des Autorisierungscodes verwendet wurde. Sie können in dieser Anforderung keinen anderen Benutzerflow verwenden. |
| client_id |Erforderlich |Die Anwendungs-ID, die Ihrer App im [Azure-Portal](https://portal.azure.com) zugewiesen wird.|
| client_secret | Ja, in Web-Apps | Der geheime Schlüssel der Anwendung, der im [Azure-Portal](https://portal.azure.com/) generiert wurde. Geheime Client-Schlüssel werden in diesem Flow für Web-App-Szenarien verwendet, in denen der Client einen geheimen Client-Schlüssel sicher speichern kann. Bei nativen App-Szenarien (öffentlicher Client) können geheime Clientschlüssel nicht sicher gespeichert werden, und werden daher nicht in diesem Aufruf verwendet. Wenn Sie einen geheimen Clientschlüssel verwenden, ändern Sie ihn regelmäßig. |
| grant_type |Erforderlich |Der Gewährungstyp. Beim Autorisierungscodefluss muss der Gewährungstyp `authorization_code` sein. |
| scope |Erforderlich |Eine durch Leerzeichen getrennte Liste von Bereichen. Ein einzelner Bereichswert gibt Azure AD an, dass beide Berechtigungen angefordert werden. Mit der Verwendung der Client-ID als Bereich wird angegeben, dass für die App ein Zugriffstoken erforderlich ist, das für Ihren eigenen Dienst oder die Web-API mit derselben Client-ID verwendet werden kann.  Der Bereich `offline_access` gibt an, dass Ihre App ein Aktualisierungstoken für den langfristigen Zugriff auf Ressourcen benötigt.  Außerdem können Sie den Bereich `openid` verwenden, um ein ID-Token von Azure AD B2C anzufordern. |
| code |Erforderlich |Der Autorisierungscode, den Sie im ersten Abschnitt des Vorgangs erhalten haben. |
| redirect_uri |Erforderlich |Der Umleitungs-URI der Anwendung, bei der Sie den Autorisierungscode erhalten haben. |
| code_verifier | empfohlen | Derselbe code_verifier-Parameter, der auch zum Abrufen von „authorization_code“ verwendet wurde. Erforderlich, wenn PKCE bei der Anforderung für die Gewährung des Autorisierungscodes verwendet wurde. Weitere Informationen finden Sie unter [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

Eine erfolgreiche Tokenantwort sieht wie folgt aus:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| not_before |Der Zeitpunkt in Epochenzeit, ab dem das Token gültig ist. |
| token_type |Der Wert des Tokentyps. Bearertoken ist der einzige Typ, den Azure AD unterstützt. |
| access_token |Das signierte JWT-Token (JSON Web Token), das Sie angefordert haben. |
| scope |Die Bereiche, für die das Token gültig ist. Sie können in den Bereichen auch Token für die spätere Verwendung zwischenspeichern. |
| expires_in |Gibt an, wie lange das Token gültig ist (in Sekunden). |
| refresh_token |Ein Aktualisierungstoken von OAuth 2.0. Die App kann dieses Token verwenden, um nach Ablauf der aktuellen Token zusätzliche Token zu erhalten. Aktualisierungstoken sind langlebig. Sie können diese verwenden, um den Zugriff auf Ressourcen für längere Zeit beizubehalten. Weitere Informationen finden Sie unter [Azure AD B2C-Tokenverweise](tokens-overview.md). |

Fehlerantworten sehen wie folgt aus:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| error |Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren. Sie können mit der Zeichenfolge auch auf Fehler reagieren. |
| error_description |Eine spezifische Fehlermeldung, mit der Sie die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

## <a name="3-use-the-token"></a>3. Verwenden des Tokens
Nachdem Sie ein Zugriffstoken erhalten haben, können Sie das Token für Anforderungen an die Back-End-Web-APIs verwenden, indem Sie es in den -`Authorization`Header einfügen:

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Aktualisieren des Tokens
Zugriffs- und ID-Tokens sind kurzlebig. Nach ihrem Ablauf müssen Sie sie aktualisieren, um weiterhin auf Ressourcen zugreifen zu können. Übermitteln Sie zu diesem Zweck eine andere POST-Anforderung an den `/token`-Endpunkt. Geben Sie dieses Mal `refresh_token` anstelle von `code` an:

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Erforderlich? | BESCHREIBUNG |
| --- | --- | --- |
|{tenant}| Erforderlich | Name des Azure AD B2C-Mandanten.|
|{policy} |Erforderlich |Der Benutzerflow, der zum Abrufen des ursprünglichen Aktualisierungstokens verwendet wurde. Sie können in dieser Anforderung keinen anderen Benutzerflow verwenden. |
| client_id |Erforderlich |Die Anwendungs-ID, die Ihrer App im [Azure-Portal](https://portal.azure.com) zugewiesen wird. |
| client_secret | Ja, in Web-Apps | Der geheime Schlüssel der Anwendung, der im [Azure-Portal](https://portal.azure.com/) generiert wurde. Geheime Client-Schlüssel werden in diesem Flow für Web-App-Szenarien verwendet, in denen der Client einen geheimen Client-Schlüssel sicher speichern kann. Bei nativen App-Szenarien (öffentlicher Client) können geheime Clientschlüssel nicht sicher gespeichert werden, und werden daher nicht in diesem Aufruf verwendet. Wenn Sie einen geheimen Clientschlüssel verwenden, ändern Sie ihn regelmäßig. |
| grant_type |Erforderlich |Der Gewährungstyp. Bei diesem Abschnitt des Autorisierungscodeflows muss der Gewährungstyp `refresh_token` sein. |
| scope |Empfohlen |Eine durch Leerzeichen getrennte Liste von Bereichen. Ein einzelner Bereichswert gibt Azure AD an, dass beide Berechtigungen angefordert werden. Mit der Verwendung der Client-ID als Bereich wird angegeben, dass für die App ein Zugriffstoken erforderlich ist, das für Ihren eigenen Dienst oder die Web-API mit derselben Client-ID verwendet werden kann.  Der `offline_access`-Bereich gibt an, dass Ihre App ein Aktualisierungstoken für den dauerhaften Zugriff auf Ressourcen benötigt.  Außerdem können Sie den Bereich `openid` verwenden, um ein ID-Token von Azure AD B2C anzufordern. |
| redirect_uri |Optional |Der Umleitungs-URI der Anwendung, bei der Sie den Autorisierungscode erhalten haben. |
| refresh_token |Erforderlich |Das ursprüngliche Aktualisierungstoken, das Sie im zweiten Abschnitt des Vorgangs erhalten haben. |

Eine erfolgreiche Tokenantwort sieht wie folgt aus:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parameter | BESCHREIBUNG |
| --- | --- |
| not_before |Der Zeitpunkt in Epochenzeit, ab dem das Token gültig ist. |
| token_type |Der Wert des Tokentyps. Bearertoken ist der einzige Typ, den Azure AD unterstützt. |
| access_token |Das signierte JWT-Token, das Sie angefordert haben. |
| scope |Die Bereiche, für die das Token gültig ist. Sie können in den Bereichen auch Tokens für die spätere Verwendung zwischenspeichern. |
| expires_in |Gibt an, wie lange das Token gültig ist (in Sekunden). |
| refresh_token |Ein Aktualisierungstoken von OAuth 2.0. Die App kann dieses Token verwenden, um nach Ablauf der aktuellen Token zusätzliche Token zu erhalten. Aktualisierungstoken sind langlebig und können verwendet werden, um den Zugriff auf Ressourcen für längere Zeit beizubehalten. Weitere Informationen finden Sie unter [Azure AD B2C-Tokenverweise](tokens-overview.md). |

Fehlerantworten sehen wie folgt aus:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| error |Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren. Sie können mit der Zeichenfolge auch auf Fehler reagieren. |
| error_description |Eine spezifische Fehlermeldung, mit der Sie die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Verwenden Sie Ihr eigenes Azure AD B2C-Verzeichnis.
Führen Sie die folgenden Schritte durch, um diese Anforderungen selbst zu testen. Ersetzen Sie die in diesem Artikel verwendeten Beispielwerte durch Ihre eigenen Werte.

1. [Erstellen Sie ein Azure AD B2C-Verzeichnis](tutorial-create-tenant.md). Verwenden Sie den Namen Ihres Verzeichnisses in den Anforderungen.
2. [Erstellen Sie eine Anwendung](tutorial-register-applications.md) zum Abrufen einer Anwendungs-ID und eines Umleitungs-URI. Sie können Ihrer App einen nativen Client hinzufügen.
3. [Erstellen Sie Ihre Benutzerflows](user-flow-overview.md), um Ihre Benutzerflownamen abzurufen.
