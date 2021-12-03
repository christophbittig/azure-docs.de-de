---
title: Flow der OAuth 2.0-Clientanmeldeinformationen in Microsoft Identity Platform | Azure
description: Erstellen von Webanwendungen mit der Microsoft Identity Platform-Implementierung des OAuth 2.0-Authentifizierungsprotokolls.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: hirsin
ms.reviewer: marsma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 62e4557b003c0347c6ecbf8a39102260abb66386
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050240"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Microsoft Identity Platform und der Fluss von OAuth 2.0-Clientanmeldeinformationen

Sie können die in [RFC 6749](https://tools.ietf.org/html/rfc6749#section-4.4) spezifizierte OAuth 2.0 Client Credentials Grant, manchmal auch *zweistufiges OAuth* genannt, für den Zugriff auf web-gehostete Ressourcen unter Verwendung der Identität einer Anwendung verwenden. Diese Art der Gewährung wird häufig für Interaktionen zwischen Servern verwendet, die ohne Benutzereingriff im Hintergrund ausgeführt werden müssen. Diese Anwendungstypen werden oft als *Daemons* oder *Dienstkonten* bezeichnet.

In diesem Artikel wird beschrieben, wie Sie direkt mit dem Protokoll in Ihrer Anwendung programmieren. Es wird stattdessen empfohlen, ggf. die unterstützten Microsoft Authentication Libraries (MSAL) zu verwenden, um [Token zu erhalten und gesicherte Web-APIs aufzurufen](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Sehen Sie sich auch die [Beispiel-Apps an, die MSAL verwenden](sample-v2-code.md).

Beim Fluss zur Gewährung von OAuth 2.0-Clientanmeldeinformationen kann ein Webdienst (ein vertraulicher Client) seine eigenen Anmeldeinformationen zum Authentifizieren verwenden, wenn ein anderer Webdienst aufgerufen wird, anstatt die Identität eines Benutzers anzunehmen. Für ein höheres Maß an Sicherheit erlaubt die Microsoft-Identitätsplattform dem aufrufenden Dienst auch die Authentifizierung mit einem [Zertifikat](#second-case-access-token-request-with-a-certificate) oder einem föderierten Berechtigungsnachweis anstelle eines gemeinsamen Geheimnisses.  Da die eigenen Anmelde Informationen für die Anwendung verwendet werden, müssen diese Anmeldeinformationen sicher aufbewahrt werden. veröffentlichen Sie diese Anmeldeinformationen _niemals_ im Quellcode, Betten Sie Sie in Webseiten ein, oder verwenden Sie sie in einer weit verbreiteten, systemeigenen Anwendung. 

Im Flow zum Gewähren von Clientanmeldeinformationen werden Berechtigungen von einem Administrator direkt an die eigentliche Anwendung erteilt. Wenn die App einer Ressource ein Token präsentiert, macht die Ressource geltend, dass die App selbst für die Durchführung einer Aktion autorisiert ist, da an der Authentifizierung kein Benutzer beteiligt ist.  In diesem Artikel werden die Schritte beschrieben, die zum [Autorisieren einer Anwendung zum Aufrufen einer API](#application-permissions) erforderlich sind. Es wird aber auch die [Vorgehensweise zum Abrufen der Token, die zum Aufrufen dieser API erforderlich sind](#get-a-token) erläutert.

[!INCLUDE [try-in-postman-link](includes/try-in-postman-link.md)]

## <a name="protocol-diagram"></a>Protokolldiagramm

Der gesamte Clientanmeldeinformations-Flow ist im folgenden Diagramm dargestellt. Wir beschreiben die einzelnen Schritte weiter unten in diesem Artikel.

![Diagramm des Clientanmeldeinformations-Flows](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Erhalten der direkten Autorisierung

Eine App empfängt die direkte Autorisierung für den Zugriff auf eine Ressource in der Regel auf zwei Arten:

* [Über eine Zugriffssteuerungsliste (Access Control List, ACL) der Ressource](#access-control-lists)
* [Durch Zuweisung einer Anwendungsberechtigung in Azure AD](#application-permissions)

Diese beiden Methoden sind in Azure AD am gebräuchlichsten und werden von uns für Clients und Ressourcen empfohlen, die den Clientanmeldeinformations-Flow ausführen. Eine Ressource kann auch wählen, dass die Clients auf andere Weise autorisiert werden sollen. Jeder Ressourcenserver kann die Methode auswählen, die am sinnvollsten für seine Anwendung ist.

### <a name="access-control-lists"></a>Zugriffssteuerungslisten

Ein Ressourcenanbieter erzwingt möglicherweise eine Autorisierungsprüfung anhand einer Liste von Anwendungs-IDs (Client-IDs), die ihm bekannt sind, und erteilt eine bestimmte Zugriffsebene. Empfängt die Ressource ein Token von der Microsoft Identity Platform, kann sie das Token decodieren und die Anwendungs-ID des Clients aus den Ansprüchen `appid` und `iss` extrahieren. Anschließend vergleicht sie die Anwendung mit der von ihr verwalteten Zugriffssteuerungsliste. Granularität der Zugriffssteuerungsliste und Methode können sich zwischen Ressourcen erheblich unterscheiden.

Ein allgemeiner Anwendungsfall ist die Verwendung einer Zugriffssteuerungsliste zum Ausführen von Tests für eine Webanwendung oder eine Web-API. Die Web-API gewährt einem bestimmten Client möglicherweise nur eine Teilmenge der vollständigen Berechtigungen. Zum Ausführen von End-to-End-Tests auf der API wird aber ein Testclient erstellt, der Token von der Microsoft Identity Platform erhält und diese anschließend an die API sendet. Die API überprüft die Anwendungs-ID des Testclients anschließend anhand der Zugriffssteuerungsliste, um Vollzugriff auf die gesamte Funktionalität der API zu gewähren. Wenn Sie eine solche Zugriffssteuerungsliste verwenden, müssen Sie nicht nur den `appid`-Wert des Aufrufers überprüfen, sondern auch sicherstellen, dass der `iss`-Wert des Tokens vertrauenswürdig ist.

Diese Art der Autorisierung wird häufig für Daemons und Dienstkonten eingesetzt, die auf Daten zugreifen müssen, die sich im Besitz von Privatnutzern mit persönlichen Microsoft-Konten befinden. Für Daten, die Organisationen gehören, empfiehlt es sich, die erforderliche Autorisierung über Anwendungsberechtigungen zu erhalten.

#### <a name="controlling-tokens-without-the-roles-claim"></a>Steuern von Token ohne den `roles`-Anspruch

Azure AD setzt nicht voraus, dass Anwendungen zum Abrufen von Token für eine andere Anwendung autorisiert sind, um dieses ACL-basierte Autorisierungsmuster zu aktivieren. Daher können nur für Apps gültige Token ohne einen `roles`-Anspruch ausgestellt werden. Bei Anwendungen, die APIs verfügbar machen, müssen zum Akzeptieren von Token Berechtigungsüberprüfungen implementiert werden.

Wenn Sie verhindern möchten, dass Anwendungen nur für Apps gültige Zugriffstoken ohne Rollen abrufen, [sollten Sie sicherstellen, dass die Benutzerzuweisungsanforderungen für Ihre App aktiviert sind](../manage-apps/assign-user-or-group-access-portal.md). Dadurch wird verhindert, dass Benutzer und Anwendungen ohne zugewiesene Rollen ein Token für diese Anwendung abrufen können. 

### <a name="application-permissions"></a>Anwendungsberechtigungen

Anstelle von Zugriffssteuerungslisten können Sie APIs verwenden, um einen Satz von **Anwendungsberechtigungen** verfügbar zu machen. Eine Anwendungsberechtigung wird einer Anwendung von einem Administrator einer Organisation erteilt und kann nur für den Zugriff auf Daten verwendet werden, die sich im Besitz der jeweiligen Organisation und deren Mitarbeiter befinden. Microsoft Graph macht beispielsweise verschiedene Anwendungsberechtigungen für Folgendes verfügbar:

* Lesen von E-Mails in allen Postfächern
* Lesen und Schreiben von E-Mails in allen Postfächern
* Senden von E-Mails als beliebiger Benutzer
* Verzeichnisdaten lesen

Wenn Sie Anwendungsberechtigungen mit Ihrer eigenen API (und nicht mit Microsoft Graph) verwenden möchten, müssen Sie zunächst die [API verfügbar machen](howto-add-app-roles-in-azure-ad-apps.md), indem Sie im Azure-Portal in der App-Registrierung der API Bereiche definieren. [Konfigurieren Sie dann den Zugriff auf die API](howto-add-app-roles-in-azure-ad-apps.md#assign-app-roles-to-applications), indem Sie diese Berechtigungen in der App-Registrierung Ihrer Clientanwendung auswählen. Wenn Sie in der App-Registrierung Ihrer API keine Bereiche verfügbar gemacht haben, können Sie im Azure-Portal in der App-Registrierung Ihrer Clientanwendung keine Anwendungsberechtigungen für diese API angeben.

Wenn Sie sich als Anwendung authentifizieren (im Gegensatz zu einem Benutzer), können Sie keine *delegierten Berechtigungen* - Bereiche verwenden, die von einem Benutzer gewährt werden - weil es keinen Benutzer gibt, in dessen Namen die Anwendung agieren kann. Sie müssen Anwendungsberechtigungen verwenden, die auch als „Rollen“ bezeichnet und von einem Administrator (oder über die Vorautorisierung durch die Web-API) für die Anwendung gewährt werden.

Weitere Informationen zu Anwendungsberechtigungen finden Sie unter [Berechtigungen und Einwilligung](v2-permissions-and-consent.md#permission-types).

#### <a name="recommended-sign-the-admin-into-your-app-to-have-app-roles-assigned"></a>Empfohlen: Melden Sie den Administrator bei Ihrer App an, um App-Rollen zugewiesen zu bekommen

Wenn Sie eine Anwendung erstellen, die Anwendungsberechtigungen verwendet, erfordert die App in der Regel eine Seite oder eine Sicht, auf der der Administrator Berechtigungen für die App genehmigt. Diese Seite kann Teil des Anmelde-Flows der App, Teil der App-Einstellungen oder ein dedizierter Flow „Verbinden“ sein. In vielen Fällen ist es sinnvoll, wenn die App diese Ansicht „Verbinden“ erst anzeigt, wenn ein Benutzer sich mit einem Geschäfts-, Schul- oder Unikonto von Microsoft angemeldet hat.

Durch das Anmelden des Benutzers bei Ihrer App können Sie die Organisation identifizieren, der der Benutzer angehört, bevor Sie ihn zur Genehmigung der Anwendungsberechtigungen auffordern. Auch wenn es nicht unbedingt erforderlich ist, können Sie für Ihre Benutzer auf diese Weise eine intuitivere Benutzeroberfläche erstellen. Gehen Sie gemäß den [Tutorials zum Microsoft Identity Platform-Protokoll](active-directory-v2-protocols.md) vor, um den Benutzer anzumelden.

#### <a name="request-the-permissions-from-a-directory-admin"></a>Anfordern der Berechtigungen von einem Verzeichnisadministrator

Wenn Sie dazu bereit sind, vom Administrator der Organisation Berechtigungen anzufordern, können Sie den Benutzer zum *Endpunkt für die Administratorzustimmung* von Microsoft Identity Platform umleiten.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

Profitipp: Versuchen Sie, die folgende Anforderung in einem Browser einzufügen.

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parameter | Bedingung | BESCHREIBUNG |
| --- | --- | --- |
| `tenant` | Erforderlich | Der Verzeichnismandant, von dem Sie die Berechtigung anfordern möchten. Kann als GUID oder als Anzeigename bereitgestellt werden. Wenn Sie nicht wissen, zu welchem Mandanten der Benutzer gehört, und wenn der Benutzer sich bei jedem Mandanten anmelden können soll, verwenden Sie `common`. |
| `client_id` | Erforderlich | Die **Anwendungs-ID (Client-ID)** , die Ihrer App im [Azure-Portal auf der Seite „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) zugewiesen wurde. |
| `redirect_uri` | Erforderlich | Der Umleitungs-URI, an den die Antwort zur Verarbeitung durch die App gesendet werden soll. Er muss genau mit einem der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit der Ausnahme, dass er URL-kodiert sein muss und zusätzliche Pfadsegmente enthalten kann. |
| `state` | Empfohlen | Ein in der Anforderung enthaltener Wert, der ebenfalls in der Tokenantwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Der Status wird verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z.B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |

An diesem Punkt erzwingt Azure AD, dass sich nur ein Mandantenadministrator anmelden kann, um die Anforderung abzuschließen. Der Administrator wird aufgefordert, alle direkten Anwendungsberechtigungen zu genehmigen, die Sie für Ihre App im App-Registrierungsportal angefordert haben.

##### <a name="successful-response"></a>Erfolgreiche Antwort

Wenn der Administrator die Berechtigungen für Ihre Anwendung genehmigt, lautet die erfolgreiche Antwort wie folgt:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `tenant` | Der Verzeichnismandant, der Ihrer Anwendung die angeforderten Berechtigungen erteilt hat, im GUID-Format |
| `state` | Ein in der Anforderung enthaltener Wert, der auch in der Tokenantwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Der Status wird verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z.B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| `admin_consent` | Legen Sie den Wert auf **True** fest. |

##### <a name="error-response"></a>Fehlerantwort

Wenn der Administrator die Berechtigungen für Ihre Anwendung nicht genehmigt, lautet die Fehlerantwort wie folgt:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `error` | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| `error_description` | Eine spezifische Fehlermeldung, mit der Sie die Grundursache eines Fehlers identifizieren können. |

Nachdem Sie eine erfolgreiche Antwort vom App-Bereitstellungsendpunkt erhalten haben, erhält Ihre App die direkten Anwendungsberechtigungen, die sie angefordert hat. Als Nächstes können Sie ein Token für eine gewünschte Ressource anfordern.

## <a name="get-a-token"></a>Abrufen von Token

Sobald Sie die notwendige Autorisierung für Ihre Anwendung erhalten haben, können Sie mit dem Abrufen von Zugriffstoken für APIs fortfahren. Um ein Token über die Gewährung von Clientanmeldeinformationen zu erhalten, senden Sie eine POST-Anforderung an die `/token`-Microsoft Identity Platform:

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Erster Fall: Zugriffstokenanforderung mit einem gemeinsamen Geheimnis

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=sampleCredentia1s
&grant_type=client_credentials
```

```Bash
# Replace {tenant} with your tenant!
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| Parameter | Bedingung | BESCHREIBUNG |
| --- | --- | --- |
| `tenant` | Erforderlich | Der Verzeichnismandant, der von der Anwendung für den Betrieb verwendet werden soll, im GUID- oder Domänennamensformat. |
| `client_id` | Erforderlich | Die Anwendungs-ID, die Ihrer App zugewiesen ist. Diese Informationen finden Sie in dem Portal, in dem Sie Ihre App registriert haben. |
| `scope` | Erforderlich | Bei dem Wert, der in dieser Anforderung für den `scope`-Parameter übergeben wird, muss es sich um den Ressourcenbezeichner (Anwendungs-ID-URI) der gewünschten Ressource mit dem Suffix `.default` handeln. Für das angegebene Microsoft Graph-Beispiel ist der Wert `https://graph.microsoft.com/.default`. <br/>Dieser Wert weist die Microsoft Identity Platform an, von allen direkten Anwendungsberechtigungen, die Sie für Ihre App konfiguriert haben, ein Token für diejenigen auszustellen, die zur gewünschten Ressource gehören. Weitere Informationen zum `/.default`-Bereich finden Sie in der [Dokumentation zu Zustimmungen](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Erforderlich | Der geheime Clientschlüssel, den Sie im App-Registrierungsportal für Ihre App generiert haben. Der geheime Clientschlüssel muss vor dem Senden URL-codiert werden. Das Standardauthentifizierungsmuster der Bereitstellung von Anmeldeinformationen im Autorisierungsheader gemäß [RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749#section-2.3.1) wird ebenfalls unterstützt. |
| `grant_type` | Erforderlich | Muss auf `client_credentials` festgelegt sein. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Zweiter Fall: Zugriffstokenanforderung mit einem Zertifikat

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parameter | Bedingung | BESCHREIBUNG |
| --- | --- | --- |
| `tenant` | Erforderlich | Der Verzeichnismandant, der von der Anwendung für den Betrieb verwendet werden soll, im GUID- oder Domänennamensformat. |
| `client_id` | Erforderlich |Die Anwendungs-ID (Client-ID), die Ihrer App zugewiesen ist. |
| `scope` | Erforderlich | Bei dem Wert, der in dieser Anforderung für den `scope`-Parameter übergeben wird, muss es sich um den Ressourcenbezeichner (Anwendungs-ID-URI) der gewünschten Ressource mit dem Suffix `.default` handeln. Für das angegebene Microsoft Graph-Beispiel ist der Wert `https://graph.microsoft.com/.default`. <br/>Dieser Wert teilt der Microsoft Identity Platform mit, dass von allen direkten Anwendungsberechtigungen, die Sie für Ihre App konfiguriert haben, ein Token für diejenigen ausgestellt werden soll, die zur gewünschten Ressource gehören. Weitere Informationen zum `/.default`-Bereich finden Sie in der [Dokumentation zu Zustimmungen](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Erforderlich | Der Wert muss auf `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` festgelegt werden. |
| `client_assertion` | Erforderlich | Eine Assertion (ein JSON-Webtoken), die Sie benötigen, um das Zertifikat, das Sie als Anmeldeinformationen für Ihre Anwendung registriert haben, zu erstellen und sich damit anzumelden. Informationen zum Registrieren Ihres Zertifikats sowie zum Format der Assertion finden Sie im Abschnitt [Zertifikatanmeldeinformationen](active-directory-certificate-credentials.md).|
| `grant_type` | Erforderlich | Muss auf `client_credentials` festgelegt sein. |

Die Parameter für die zertifikatbasierte Anforderung stimmen mit nur einer Ausnahme mit den Parametern für die Anforderung basierend auf einem gemeinsamen Geheimnis überein: der `client_secret`-Parameter wird durch die Parameter `client_assertion_type` und `client_assertion` ersetzt.

### <a name="third-case-access-token-request-with-a-federated-credential"></a>Dritter Fall: Anforderung eines Zugriffstokens mit einem föderierten Berechtigungsnachweis

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parameter | Bedingung | BESCHREIBUNG |
| --- | --- | --- |
| `client_assertion` | Erforderlich | Eine Assertion (ein JWT oder JSON-Web-Token), die Ihre Anwendung von einem anderen Identitätsanbieter außerhalb der Microsoft-Identitätsplattform, wie Kubernetes, erhält. Die Spezifika dieses JWT müssen in Ihrer Anwendung als [federated identity credential](workload-identity-federation-create-trust.md) registriert werden. Lesen Sie über [workload identity federation](workload-identity-federation.md), um zu erfahren, wie Sie von anderen Identitätsanbietern generierte Assertions einrichten und verwenden können.|

Alles in der Anfrage entspricht dem obigen zertifikatsbasierten Fluss, mit einer entscheidenden Ausnahme - der Quelle der `client_assertion`. Bei diesem Ablauf erstellt Ihre Anwendung die JWT-Assertion nicht selbst.  Stattdessen verwendet Ihre Anwendung ein JWT, das von einem anderen Identitätsanbieter erstellt wurde.  Dies wird als "[workload identity federation](workload-identity-federation.md)" bezeichnet, bei der die Identität Ihrer Anwendungen in einer anderen Identitätsplattform verwendet wird, um Token innerhalb der Microsoft-Identitätsplattform zu erwerben.  Dies eignet sich am besten für Cloud-übergreifende Szenarien, z. B. wenn Sie Ihre Rechenleistung außerhalb von Azure hosten, aber auf APIs zugreifen, die durch die Microsoft Identitätsplattform geschützt sind. 

### <a name="successful-response"></a>Erfolgreiche Antwort

Eine erfolgreiche Antwort von einer beliebigen Methode sieht wie folgt aus:

```json
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `access_token` | Das angeforderte Zugriffstoken. Die App kann dieses Token zur Authentifizierung bei geschützten Ressourcen wie einer Web-API verwenden. |
| `token_type` | Gibt den Wert des Tokentyps an. Die Microsoft Identity Platform unterstützt nur den Typ `bearer`. |
| `expires_in` | Die Gültigkeitsdauer eines Zugriffstokens (in Sekunden). |

[!INCLUDE [remind-not-to-validate-access-tokens](includes/remind-not-to-validate-access-tokens.md)]

### <a name="error-response"></a>Fehlerantwort

Eine Fehlerantwort sieht wie folgt aus:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `error` | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| `error_description` | Eine spezifische Fehlermeldung, mit der Sie die Hauptursache eines Authentifizierungsfehlers identifizieren können |
| `error_codes` | Eine Liste der STS-spezifischen Fehlercodes, die bei der Diagnose helfen können |
| `timestamp` | Die Uhrzeit, zu der der Fehler aufgetreten ist |
| `trace_id` | Ein eindeutiger Bezeichner für die Anforderung, der bei der Diagnose hilfreich sein kann |
| `correlation_id` | Ein eindeutiger Bezeichner für die Anforderung, der bei der komponentenübergreifenden Diagnose hilfreich sein kann |

## <a name="use-a-token"></a>Verwenden eines Tokens

Da Sie jetzt ein Token abgerufen haben, können Sie dieses Token verwenden, um Anforderungen an die Ressource zu stellen. Wenn das Token abläuft, wiederholen Sie die Anforderung an den `/token`-Endpunkt, um ein neues Zugriffstoken abzurufen.

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```bash
# Pro tip: Try the following command! (Replace the token with your own.)

curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Codebeispiele und anderes Dokumentationsmaterial

Lesen Sie die [Übersichtsdokumentation zu Clientanmeldeinformationen](https://aka.ms/msal-net-client-credentials) aus der Microsoft-Authentifizierungsbibliothek.

| Beispiel | Plattform |BESCHREIBUNG |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET Core 2.1-Konsole | Eine einfache .NET Core-Anwendung, die die Benutzer eines Mandanten anzeigt, der Microsoft Graph unter Verwendung der Anwendungsidentität (statt im Auftrag eines Benutzers) abfragt. Das Beispiel veranschaulicht außerdem die Variante, bei der Zertifikate für die Authentifizierung verwendet werden. |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Eine Webanwendung, die Daten aus Microsoft Graph unter Verwendung der Anwendungsidentität (statt im Auftrag eines Benutzers) synchronisiert. |
