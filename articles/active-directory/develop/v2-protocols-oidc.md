---
title: Microsoft Identity Platform und das OpenID Connect-Protokoll | Azure
titleSuffix: Microsoft identity platform
description: Erstellen von Webanwendungen mit der Microsoft Identity Platform-Implementierung des OpenID Connect-Authentifizierungsprotokolls.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 7aab9abf57b94f07edda0e6f2c27c4720c747b20
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050202"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft Identity Platform und das OpenID Connect-Protokoll

OpenID Connect (OIDC) ist ein Authentifizierungsprotokoll auf Grundlage von OAuth 2.0, mit dem Benutzer sicher bei einer Anwendung angemeldet werden können. Die Implementierung von OpenID Connect in Microsoft Identity Platform ermöglicht es Ihnen, Anmeldungen und API-Zugriffe für Ihre Apps hinzuzufügen. In diesem Artikel wird beschrieben, wie dies sprachunabhängig funktioniert. Außerdem wird beschrieben, wie HTTP-Nachrichten gesendet und empfangen werden, ohne [Open Source-Bibliotheken von Microsoft](reference-v2-libraries.md) zu verwenden.

Mit [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) wird das OAuth 2.0-*Autorisierungsprotokoll* auf die Nutzung als *Authentifizierungsprotokoll* erweitert, damit einmaliges Anmelden per OAuth funktioniert. OpenID Connect führt das Konzept eines *ID-Tokens* ein. Hierbei handelt es sich um ein Sicherheitstoken, mit dem der Client die Identität des Benutzers überprüfen kann. Ferner ruft das ID-Token auch Basisprofilinformationen über den Benutzer ab. Darüber hinaus wird der [UserInfo-Endpunkt](userinfo.md) eingeführt. Hierbei handelt es sich um eine API, die Informationen zum Benutzer zurückgibt. 

[!INCLUDE [try-in-postman-link](includes/try-in-postman-link.md)]

## <a name="protocol-diagram-sign-in"></a>Protokolldiagramm: Anmeldung

Der grundlegende Anmeldefluss besteht aus den in der nächsten Abbildung gezeigten Schritten. Jeder Schritt wird in diesem Artikel detailliert beschrieben.

![OpenID Connect-Protokoll: Anmeldung](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Abrufen des OpenID Connect-Metadatendokuments

OpenID Connect beschreibt ein Metadatendokument [(RFC)](https://openid.net/specs/openid-connect-discovery-1_0.html), das den Großteil der Informationen enthält, die für eine App zum Durchführen einer Anmeldung erforderlich sind. Hierzu gehören Informationen wie z.B. die zu verwendenden URLs und der Speicherort der öffentlichen Signaturschlüssel des Dienstes. Sie können auf dieses Dokument zugreifen, indem Sie den Ermittlungsdokumentpfad an die Autoritäts-URL anfügen:

Ermittlungsdokumentpfad: `/.well-known/openid-configuration`

Autorität: `https://login.microsoftonline.com/{tenant}/v2.0`

Der `{tenant}` kann einen von vier möglichen Werten annehmen:

| Wert | BESCHREIBUNG |
| --- | --- |
| `common` |Benutzer mit einem persönlichen Microsoft-Konto und einem Geschäfts-, Schul- oder Unikonto aus Azure AD können sich bei der Anwendung anmelden. |
| `organizations` |Nur Benutzer mit Geschäfts-, Schul- oder Unikonten aus Azure AD können sich bei der Anwendung anmelden. |
| `consumers` |Nur Benutzer mit einem persönlich Microsoft-Konto können sich bei der Anwendung anmelden. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` oder `contoso.onmicrosoft.com` | Nur Benutzer eines bestimmten Azure AD-Mandanten können sich bei der Anwendung anmelden. (Dabei macht es keinen Unterschied, ob es sich um Mitglieder im Verzeichnis mit einem Geschäfts-, Schul- oder Unikonto oder um Gäste im Verzeichnis mit einem persönlichen Microsoft-Konto handelt.) Dabei kann entweder der Anzeigename der Domäne des Azure AD-Mandanten oder der GUID-Bezeichner des Mandanten verwendet werden. Sie können auch den Consumermandanten `9188040d-6c67-4c5b-b112-36a304b66dad` anstelle des Mandanten `consumers` verwenden.  |

Die Autorität variiert je nach der verwendeten nationalen Cloud, z. B. `https://login.microsoftonline.de` für die Instanz „Azure AD Deutschland“. Falls Sie nicht die öffentliche Cloud nutzen, helfen Ihnen die Informationen unter [Azure AD-Authentifizierungsendpunkte](authentication-national-cloud.md#azure-ad-authentication-endpoints) bei der Wahl einer passenden Cloud. Stellen Sie sicher, dass Ihre Anforderung den Mandanten und `/v2.0/` enthält, damit Sie Version 2.0 des Endpunkts verwenden können.

> [!TIP]
> Testen Klicken Sie auf [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration), um die `common` -Konfiguration anzuzeigen.

### <a name="sample-request"></a>Beispiel für eine Anforderung

Gehen Sie wie folgt vor, um den UserInfo-Endpunkt für die common-Autorität in der öffentlichen Cloud aufzurufen:

```http
GET /common/v2.0/.well-known/openid-configuration
Host: login.microsoftonline.com
```

### <a name="sample-response"></a>Beispiel für eine Antwort

Bei den Metadaten handelt es sich um ein einfaches JavaScript Object Notation-Dokument (JSON). Die folgenden Codeausschnitte zeigen Beispiele. Die vollständigen Inhalte sind in der [OpenID Connect-Spezifikation](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2) beschrieben.

```json
{
  "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize",
  "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys",
  "userinfo_endpoint": "https://graph.microsoft.com/oidc/userinfo",
  "subject_types_supported": [
      "pairwise"
  ],
  ...

}
```

Wenn Ihre App durch die Verwendung der Funktion [Anspruchszuordnung](active-directory-claims-mapping.md) über benutzerdefinierte Signaturschlüssel verfügt, müssen Sie einen `appid`-Abfrageparameter mit der App-ID anfügen, um einen `jwks_uri` abzurufen, der auf die Signaturschlüsselinformationen Ihrer App verweist. Beispiel: `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` enthält einen `jwks_uri` von `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

In der Regel verwenden Sie dieses Metadatendokument zum Konfigurieren einer OpenID Connect-Bibliothek oder eines SDKs; die Bibliothek führt ihre Aufgaben mithilfe der Metadaten durch. Wenn Sie jedoch keine vordefinierte OpenID Connect-Bibliothek verwenden, können Sie die Schritte weiter unten in diesem Artikel ausführen, um die Anmeldung bei einer Web-App über Microsoft Identity Platform durchzuführen.

## <a name="send-the-sign-in-request"></a>Senden der Anmeldeanforderung

Wenn die Web-App den Benutzer authentifizieren muss, kann sie ihn direkt an den `/authorize` -Endpunkt weiterleiten. Diese Anforderung ähnelt dem ersten Abschnitt des [OAuth 2.0-Autorisierungscodeflusses](v2-oauth2-auth-code-flow.md), allerdings mit einigen wichtigen Unterschieden:

* Die Anforderung muss im `scope`-Parameter den `openid`-Bereich enthalten.
* Der `response_type`-Parameter muss `id_token` enthalten.
* Die Anforderung muss den `nonce` -Parameter enthalten.

> [!IMPORTANT]
> Zur erfolgreichen Anforderung eines ID-Tokens vom Endpunkt „/authorization“ muss für die App-Registrierung im [Registrierungsportal](https://portal.azure.com) auf der Registerkarte „Authentifizierung“ die implizite Genehmigung von „id_tokens“ aktiviert sein (dadurch wird das Flag `oauth2AllowIdTokenImplicitFlow` im [Anwendungsmanifest ](reference-app-manifest.md) auf `true` festgelegt). Wenn sie nicht aktiviert ist, wird ein `unsupported_response`-Fehler zurückgegeben: „The provided value for the input parameter 'response_type' isn't allowed for this client. Expected value is ‚code‘“. (Der angegebene Wert für den Eingabeparameter ‚response_type‘ ist für diesen Client nicht zulässig. Erwarteter Wert: ‚code‘.)

Beispiel:

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

| Parameter | Bedingung | BESCHREIBUNG |
| --- | --- | --- |
| `tenant` | Erforderlich | Mit dem `{tenant}`-Wert im Pfad der Anforderung kann festgelegt werden, welche Benutzer sich bei der Anwendung anmelden können. Zulässige Werte sind `common`, `organizations`, `consumers` und Mandantenbezeichner. Weitere Informationen finden Sie in den [Protokollgrundlagen](active-directory-v2-protocols.md#endpoints). Wichtig: In Gastszenarien, in denen Sie einen Benutzer aus einem Mandanten bei einem anderen Mandanten anmelden, *müssen* Sie die Mandanten-ID angeben, um ihn beim Ressourcenmandanten ordnungsgemäß anmelden zu können.|
| `client_id` | Erforderlich | Die **Anwendungs-ID (Client-ID)** , die Ihrer App im [Azure-Portal auf der Seite „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) zugewiesen wurde. |
| `response_type` | Erforderlich | Muss das `id_token` für die OpenID Connect-Anmeldung enthalten. Es kann auch andere `response_type`-Werte enthalten, z.B. `code`. |
| `redirect_uri` | Empfohlen | Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einem der Umleitungs-URIs übereinstimmen, den Sie im Portal registriert haben – mit dem Unterschied, dass er URL-codiert sein muss. Wenn dieser Parameter nicht vorhanden ist, wählt der Endpunkt nach dem Zufallsprinzip einen registrierten `redirect_uri` aus, der an den Benutzer zurückgesendet wird. |
| `scope` | Erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Für OpenID Connect muss der Bereich `openid` enthalten sein, der auf der Zustimmungsbenutzeroberfläche die Anmeldeberechtigung **Anmelden** überträgt. Sie können in diese Anforderung auch andere Bereiche für das Anfordern der Zustimmung aufnehmen. |
| `nonce` | Erforderlich | Ein Wert in der Anforderung, der von der App erzeugt wird und im resultierenden ID-Tokenwert als Anspruch enthalten ist. Die App kann diesen Wert überprüfen, um die Gefahr von Tokenwiedergabeangriffen zu reduzieren. Der Wert ist in der Regel eine zufällige, eindeutige Zeichenfolge, die verwendet werden kann, um den Ursprung der Anforderung zu identifizieren. |
| `response_mode` | Empfohlen | Gibt die Methode an, die zum Senden des resultierenden Autorisierungscodes zurück an Ihre App verwendet werden soll. Kann `form_post` oder `fragment` sein. Bei Webanwendungen empfiehlt sich die Verwendung von `response_mode=form_post`, um eine möglichst sichere Tokenübertragung an die Anwendung zu gewährleisten. |
| `state` | Empfohlen | Ein in der Anforderung enthaltener Wert, der auch in der Tokenantwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Normalerweise wird ein zufällig generierter eindeutiger Wert verwendet, um [websiteübergreifende Anforderungsfälschungsangriffe zu verhindern](https://tools.ietf.org/html/rfc6749#section-10.12). Der Status wird außerdem verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z.B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| `prompt` | Optional | Gibt den Typ der erforderlichen Benutzerinteraktion an. Die einzigen gültigen Werte sind gegenwärtig `login`, `none`, `consent` und `select_account`. Der Anspruch `prompt=login` zwingt den Benutzer, seine Anmeldeinformationen bei dieser Anforderung einzugeben. Einmaliges Anmelden ist dadurch nicht möglich. Der Parameter `prompt=none` ist das Gegenstück, und ihm sollte `login_hint` zugeordnet werden, um anzugeben, welcher Benutzer angemeldet werden muss. Diese Parameter stellen sicher, dass dem Benutzer überhaupt keine interaktive Eingabeaufforderung angezeigt wird. Wenn die Anforderung nicht automatisch über einmaliges Anmelden abgeschlossen werden kann (da kein Benutzer angemeldet ist, der Benutzer aus dem Hinweis nicht angemeldet ist oder mehrere Benutzer angemeldet sind und kein Hinweis bereitgestellt wird), gibt Microsoft Identity Platform einen Fehler zurück. Der Anspruch `prompt=consent` löst das OAuth-Zustimmungsdialogfeld aus, sobald sich der Benutzer angemeldet hat. Das Dialogfeld fordert den Benutzer zum Erteilen von Berechtigungen für die App auf. Schließlich zeigt `select_account` dem Benutzer eine Kontoauswahl an, die das automatische SSO negiert, dem Benutzer jedoch ermöglicht, das Konto auszuwählen, mit dem er sich anmelden möchte, ohne dass Anmeldeinformationen eingegeben werden müssen. Sie können `login_hint` und `select_account` nicht zusammen verwenden.|
| `login_hint` | Optional | Sie können diesen Parameter verwenden, um das Feld für den Benutzernamen und die E-Mail-Adresse auf der Anmeldeseite vorab für den Benutzer auszufüllen, wenn dessen Benutzername im Vorfeld bekannt ist. Apps verwenden diesen Parameter oft während der erneuten Authentifizierung, nachdem sie den [optionalen Anspruch](active-directory-optional-claims.md) `login_hint`bereits aus einer vorherigen Anmeldung extrahiert haben. |
| `domain_hint` | Optional | Der Bereich des Benutzers in einem Verbundverzeichnis.  Mit diesem Parameter wird der E-Mail-basierte Ermittlungsvorgang übersprungen, den der Benutzer auf der Anmeldeseite durchläuft, was die Benutzerfreundlichkeit verbessert. Für Mandanten, die über ein lokales Verzeichnis wie z. B. AD FS verbunden sind, führt dies wegen der vorhandenen Anmeldesitzung häufig zu einer nahtlosen Anmeldung. |

An dieser Stelle wird der Benutzer dazu aufgefordert, seine Anmeldeinformationen einzugeben und die Authentifizierung abzuschließen. Microsoft Identity Platform überprüft, ob der Benutzer den Berechtigungen zugestimmt hat, die im Abfrageparameter `scope` angegeben sind. Wenn der Benutzer keiner dieser Berechtigungen zugestimmt hat, wird er von Microsoft Identity Platform aufgefordert, den erforderlichen Berechtigungen zuzustimmen. Lesen Sie mehr über [Berechtigungen, die Zustimmung und mehrinstanzenfähige Apps](v2-permissions-and-consent.md).

Sobald der Benutzer authentifiziert wurde und seine Zustimmung erteilt hat, gibt Microsoft Identity Platform mithilfe der im Parameter `response_mode` festgelegten Methode eine Antwort über den angegebenen Umleitungs-URI an Ihre App zurück.

### <a name="successful-response"></a>Erfolgreiche Antwort

Bei Verwendung von `response_mode=form_post` sieht eine erfolgreiche Antwort wie folgt aus:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `id_token` | Das ID-Token, das die App angefordert hat. Sie können mit dem Parameter `id_token` die Identität des Benutzers überprüfen und eine Sitzung mit dem Benutzer beginnen. Weitere Informationen zu ID-Token und deren Inhalt finden Sie in der [`id_tokens`-Referenz](id-tokens.md). |
| `state` | Wenn ein Parameter `state` in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |

### <a name="error-response"></a>Fehlerantwort

Fehlerantworten können auch an den Umleitungs-URI gesendet werden, damit die App diese angemessen behandeln kann. Eine Fehlerantwort sieht wie folgt aus:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `error` | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| `error_description` | Eine spezifische Fehlermeldung, mit der Sie die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Fehlercodes beim Autorisierungsendpunktfehler

Die folgende Tabelle beschreibt die Fehlercodes, die im Parameter `error` der Fehlerantwort zurückgegeben werden können:

| Fehlercode | BESCHREIBUNG | Clientaktion |
| --- | --- | --- |
| `invalid_request` | Protokollfehler, z.B. ein fehlender erforderlicher Parameter. |Korrigieren Sie die Anforderung, und senden Sie sie erneut. Dies ist ein Entwicklungsfehler, der in der Regel bei den Eingangstests festgestellt wird. |
| `unauthorized_client` | Die Clientanwendung darf keinen Autorisierungscode anfordern. |Dies tritt in der Regel auf, wenn die Clientanwendung nicht in Azure AD registriert ist oder dem Azure AD-Mandanten des Benutzers nicht hinzugefügt wird. Die Anwendung kann den Benutzer zum Installieren der Anwendung und zum Hinzufügen zu Azure AD auffordern. |
| `access_denied` | Der Ressourcenbesitzer hat die Zustimmung verweigert. |Die Clientanwendung kann den Benutzer benachrichtigen, dass sie ohne Zustimmung des Benutzers nicht fortfahren kann. |
| `unsupported_response_type` |Der Autorisierungsserver unterstützt den Antworttyp in der Anforderung nicht. |Korrigieren Sie die Anforderung, und senden Sie sie erneut. Dies ist ein Entwicklungsfehler, der in der Regel bei den Eingangstests festgestellt wird. |
| `server_error` | Der Server hat einen unerwarteten Fehler erkannt. |Wiederholen Sie die Anforderung. Diese Fehler werden durch temporäre Bedingungen verursacht. Die Clientanwendung kann dem Benutzer erklären, dass ihre Antwort aufgrund eines temporären Fehlers verzögert ist. |
| `temporarily_unavailable` | Der Server ist vorübergehend überlastet und kann die Anforderung nicht verarbeiten. |Wiederholen Sie die Anforderung. Die Clientanwendung kann dem Benutzer erklären, dass ihre Antwort aufgrund einer temporären Bedingung verzögert ist. |
| `invalid_resource` | Die Zielressource ist ungültig, da sie nicht vorhanden ist, Azure AD sie nicht findet oder sie nicht ordnungsgemäß konfiguriert wurde. |Dies zeigt an, dass die Ressource, sofern vorhanden, nicht im Mandanten konfiguriert wurde. Die Anwendung kann den Benutzer zum Installieren der Anwendung und zum Hinzufügen zu Azure AD auffordern. |

## <a name="validate-the-id-token"></a>Überprüfen des ID-Tokens

Das Empfangen eines ID-Tokens (id_token) allein reicht nicht immer aus, um den Benutzer zu authentifizieren. Unter Umständen müssen Sie auch die Signatur des ID-Tokens validieren und die Ansprüche im Token anhand der App-Anforderungen überprüfen. Wie bei allen OIDC-Plattformen gilt Folgendes: Microsoft Identity Platform verwendet [JSON-Webtoken (JWTs)](https://tools.ietf.org/html/rfc7519) und die Verschlüsselung mit öffentlichem Schlüssel, um ID-Token zu signieren und deren Gültigkeit zu überprüfen.

Nicht alle Apps profitieren von der Überprüfung des ID-Tokens. Für native Apps und Einzelseiten-Apps ergeben sich aus der Überprüfung des ID-Tokens nur selten Vorteile.  Personen mit physischem Zugang zum Gerät (oder Browser) können die Überprüfung auf unterschiedliche Arten umgehen. Beispiele hierfür sind die Bearbeitung des Webdatenverkehrs für das Gerät, um falsche Token und Schlüssel bereitzustellen, oder das simple Debuggen der Anwendung, um die Überprüfungslogik zu überspringen.  Andererseits muss das ID-Token von Web-Apps und APIs, die dieses für Autorisierungszwecke nutzen, sorgfältig überprüft werden, weil sie die Kontrolle des Zugriffs auf Daten durchführen müssen.

Nachdem Sie die Signatur des ID-Tokens validiert haben, müssen Sie noch einige Ansprüche überprüfen. Weitere Informationen finden Sie in der [`id_token`-Referenz](id-tokens.md). Dort finden Sie auch Einzelheiten zum [Überprüfen von Token](id-tokens.md#validating-an-id-token) und [Wichtige Informationen zum Rollover von Signaturschlüsseln](active-directory-signing-key-rollover.md). Wir empfehlen, zum Analysieren und Überprüfen von Token eine Bibliothek zu verwenden. Für die meisten Sprachen und Plattformen ist mindestens eine Bibliothek verfügbar.

Sie können je nach Szenario auch zusätzliche Ansprüche überprüfen. Einige allgemeinen Überprüfungen umfassen:

* Sicherstellen, dass sich der Benutzer/die Organisation für die App angemeldet hat.
* Sicherstellen, dass der Benutzer über eine ordnungsgemäße Autorisierung und Berechtigungen verfügt.
* Sicherstellen, dass eine bestimmte Authentifizierungsmethode verwendet wird, z. B. die [mehrstufige Authentifizierung](../authentication/concept-mfa-howitworks.md).

Nachdem Sie das ID-Token überprüft haben, können Sie mit dem Benutzer eine Sitzung beginnen und die Ansprüche im ID-Token zum Abrufen von Informationen über den Benutzer in der App verwenden. Diese Informationen können für die Anzeige, für Datensätze, für die Personalisierung usw. verwendet werden.

## <a name="protocol-diagram-access-token-acquisition"></a>Protokolldiagramm: Zugriffstokenbeschaffung

Viele Web-Apps müssen nicht nur den Benutzer anmelden, sondern auch mithilfe von OAuth im Namen dieses Benutzers auf einen Webdienst zugreifen. Dieses Szenario kombiniert OpenID Connect für die Benutzerauthentifizierung und ruft gleichzeitig einen Autorisierungscode ab, der mithilfe des OAuth-Autorisierungscodeflusses Zugriffstoken abrufen kann.

Der vollständige Ablauf für die Anmeldung mit OpenID Connect und den Abruf von Token sieht etwa wie folgt aus. Die einzelnen Schritte werden in den nächsten Abschnitten dieses Artikels im Detail beschrieben.

![OpenID Connect-Protokoll: Tokenbeschaffung](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-an-access-token-to-call-userinfo"></a>Abrufen eines Zugriffstokens zum Aufrufen von UserInfo

Ändern Sie die Anmeldeanforderung, um ein Token für den OIDC-UserInfo-Endpunkt abzurufen:

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20token                       // this will return both an id_token and an access token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid+profile+email                           // `openid` is required.  `profile` and `email` provide additional information in the UserInfo endpoint the same way they do in an ID token. 
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Sie können auch den [Autorisierungscodefluss](v2-oauth2-auth-code-flow.md), den [Gerätecodefluss](v2-oauth2-device-code.md) oder ein [Aktualisierungstoken](v2-oauth2-auth-code-flow.md#refresh-the-access-token) anstelle von `response_type=token` verwenden, um ein Token für Ihre App abzurufen.

> [!TIP]
> Klicken Sie auf den folgenden Link, um diese Anforderung auszuführen. Nachdem Sie sich angemeldet haben, wird Ihr Browser mit einem ID-Token und einem Token in der Adressleiste an `https://localhost/myapp/` weitergeleitet. Beachten Sie, dass bei dieser Anforderung `response_mode=fragment` nur zu Demonstrationszwecken verwendet wird. Für eine Web-App empfehlen wir Ihnen nach Möglichkeit die Nutzung von `form_post`, um die Sicherheit zu erhöhen. 
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid+profile+email&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

### <a name="successful-token-response"></a>Erfolgreiche Tokenantwort

Eine erfolgreiche Antwort mit `response_mode=form_post` sieht wie folgt aus:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
 access_token=eyJ0eXAiOiJKV1QiLCJub25jZSI6I....
 &token_type=Bearer
 &expires_in=3598
 &scope=email+openid+profile
 &id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI....
 &state=12345
```

Unabhängig vom Datenfluss beim Abrufen haben Antwortparameter immer die gleiche Bedeutung.

| Parameter | BESCHREIBUNG |
| --- | --- |
| `access_token` | Das Token, das zum Aufrufen des UserInfo-Endpunkts verwendet wird.|
| `token_type` | Immer „Bearer“ |
| `expires_in`| Gibt die Dauer bis zum Ablauf des Zugriffstokens an (in Sekunden). |
| `scope` | Die Berechtigungen, die für das Zugriffstoken gewährt wurden.  Beachten Sie Folgendes: Da der UserInfo-Endpunkt unter MS Graph gehostet wird, sind hier ggf. weitere Graph-Bereiche aufgelistet (z. B. „user.read“), falls diese für die App zuvor gewährt wurden.  Der Grund ist, dass ein Token für eine bestimmte Ressource immer alle gewährten Berechtigungen umfasst, über die der Client derzeit verfügt.  |
| `id_token` | Das ID-Token, das die App angefordert hat. Sie können mit dem ID-Token die Identität des Benutzers überprüfen und eine Sitzung mit dem Benutzer beginnen. Weitere Informationen zu ID-Token und deren Inhalt finden Sie in der [`id_tokens`-Referenz](id-tokens.md). |
| `state` | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |

[!INCLUDE [remind-not-to-validate-access-tokens](includes/remind-not-to-validate-access-tokens.md)]

### <a name="error-response"></a>Fehlerantwort

Fehlerantworten können auch an den Umleitungs-URI gesendet werden, damit die App diese angemessen behandeln kann. Eine Fehlerantwort sieht wie folgt aus:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `error` | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| `error_description` | Eine spezifische Fehlermeldung, mit der Sie die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

Eine Beschreibung der möglichen Fehlercodes und der jeweils empfohlenen Clientantwort finden Sie unter [Fehlercodes beim Autorisierungsendpunktfehler](#error-codes-for-authorization-endpoint-errors).

Nachdem Sie einen Autorisierungscode und ein ID-Token erhalten haben, können Sie den Benutzer anmelden und Zugriffstoken in seinem Namen abrufen. Zum Anmelden des Benutzers müssen Sie das ID-Token [genau wie beschrieben](id-tokens.md#validating-an-id-token) überprüfen. Führen Sie zum Abrufen von Zugriffstoken die in der [Dokumentation zum OAuth-Codefluss](v2-oauth2-auth-code-flow.md#redeem-a-code-for-an-access-token) beschriebenen Schritte aus.

### <a name="calling-the-userinfo-endpoint"></a>Aufrufen des UserInfo-Endpunkts

Die [UserInfo-Dokumentation](userinfo.md#calling-the-api) enthält Informationen dazu, wie der UserInfo-Endpunkt mit diesem Token aufgerufen wird.

## <a name="send-a-sign-out-request"></a>Senden einer Abmeldeanforderung

Wenn Sie den Benutzer bei der App abmelden möchten, reicht es nicht, die Cookies der App zu löschen oder die Sitzung des Benutzers auf andere Weise zu beenden. Sie müssen den Benutzer für die Abmeldung außerdem an Microsoft Identity Platform umleiten. Andernfalls kann sich der Benutzer erneut für Ihre App authentifizieren, ohne die Anmeldeinformationen erneut einzugeben, da der Benutzer nach wie vor über eine gültige SSO-Sitzung (Single Sign-On, Einmaliges Anmelden) bei Microsoft Identity Platform verfügt.

Sie können den Benutzer an den `end_session_endpoint` umleiten, der im OpenID Connect-Metadatendokument aufgeführt wird:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | Bedingung | BESCHREIBUNG |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Empfohlen | Die URL, an die der Benutzer nach erfolgreicher Abmeldung umgeleitet wird. Wenn der Parameter nicht enthalten ist, wird dem Benutzer eine generische Meldung angezeigt, die von Microsoft Identity Platform generiert wird. Diese URL muss mit einem der Umleitungs-URIs übereinstimmen, die im App-Registrierungsportal für Ihre Anwendung registriert wurden. |

## <a name="single-sign-out"></a>Einmaliges Abmelden

Wenn Sie den Benutzer an `end_session_endpoint` umleiten, löscht Microsoft Identity Platform die Sitzung des Benutzers aus dem Browser. Allerdings kann der Benutzer weiterhin bei anderen Anwendungen angemeldet sein, die Microsoft-Konten für die Authentifizierung verwenden. Damit diese Anwendungen den Benutzer gleichzeitig abmelden können, sendet Microsoft Identity Platform eine HTTP GET-Anforderung an die registrierte `LogoutUrl` aller Anwendungen, bei denen der Benutzer derzeit angemeldet ist. Anwendungen müssen auf diese Anforderung antworten, indem sie alle Sitzungen löschen, mit denen der Benutzer identifiziert wird, und eine `200`-Antwort zurückgeben. Wenn Sie das einmalige Abmelden in Ihrer Anwendung unterstützen möchten, müssen Sie eine solche `LogoutUrl` im Code Ihrer Anwendung implementieren. Sie können die `LogoutUrl` über das App-Registrierungsportal festlegen.

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie die [UserInfo-Dokumentation](userinfo.md).
* Informieren Sie sich darüber, wie Sie [die Werte eines Tokens mit Daten aus Ihren lokalen Systemen anpassen](active-directory-claims-mapping.md). 
* Informieren Sie sich darüber, wie Sie [weitere Standardansprüche in Token einfügen](active-directory-optional-claims.md).  
