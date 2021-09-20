---
title: Microsoft Identity Platform und der On-Behalf-Of-Fluss von OAuth2.0 | Azure
titleSuffix: Microsoft identity platform
description: In diesem Artikel wird beschrieben, wie Sie HTTP-Nachrichten zum Implementieren der Dienst-zu-Dienst-Authentifizierung über den Im-Auftrag-von-Fluss von OAuth 2.0 verwenden.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 15f62d7d0fa05a925878683af1b41ca3421d1765
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222673"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft Identity Platform und der On-Behalf-Of-Fluss von OAuth2.0

Der On-Behalf-Of-Fluss von OAuth 2.0 (OBO) wird verwendet, wenn eine Anwendung eine Dienst- oder Web-API aufruft, die wiederum einen andere Dienst- oder Web-API aufrufen muss. Die Idee dabei ist, die delegierte Benutzeridentität und Berechtigungen über die Anforderungskette weiterzugeben. Damit der Dienst auf der mittleren Ebene Authentifizierungsanforderungen an den Downstreamdienst stellen kann, muss für den Benutzer ein Zugriffstoken der Microsoft Identity Platform gesichert werden.

Der OBO-Fluss funktioniert zurzeit nur bei Benutzerprinzipalen. Ein Dienstprinzipal kann kein Nur-App-Token anfordern, es an eine API senden und diese API gegen diejenige für ein anderes Token austauschen lassen, das diesen ursprünglichen Dienstprinzipal darstellt. Darüber hinaus konzentriert sich der OBO-Fluss auf das Handeln im Auftrag einer anderen Partei, was als „delegiertes Szenario“ bezeichnet wird. Dies bedeutet, dass er nur delegierte *Bereiche* und nicht Anwendungs *rollen* verwendet, um Berechtigungen zu beurteilen. *Rollen* bleiben an den Prinzipal (dem Benutzer) im Fluss angefügt – nie der Anwendung, die im Auftrag der Benutzer ausgeführt wird.

In diesem Artikel wird beschrieben, wie Sie direkt mit dem Protokoll in Ihrer Anwendung programmieren. Es wird stattdessen empfohlen, ggf. die unterstützten Microsoft Authentication Libraries (MSAL) zu verwenden, um [Token zu erhalten und gesicherte Web-APIs aufzurufen](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Sehen Sie sich auch die [Beispiel-Apps an, die MSAL verwenden](sample-v2-code.md).

Ab Mai 2018 können einige implizit vom Fluss abgeleitete `id_token` nicht für den OBO-Fluss verwendet werden. Single-Page-Anwendungen (SPAs) müssen ein **Zugriffstoken** an einen vertraulichen Client der mittleren Ebene übergeben, um stattdessen OBO-Flüsse auszuführen. Weitere Informationen dazu, welche Clients OBO-Aufrufe ausführen können, finden Sie unter [Einschränkungen](#client-limitations).

[!INCLUDE [try-in-postman-link](includes/try-in-postman-link.md)]

## <a name="protocol-diagram"></a>Protokolldiagramm

Angenommen, der Benutzer wurde mit dem [OAuth 2.0-Flow zum Erteilen eines Autorisierungscodes](v2-oauth2-auth-code-flow.md) oder einem anderen Anmeldeflow in einer Anwendung authentifiziert. In diesem Fall verfügt die Anwendung über ein Zugriffstoken *für API A* (Token A), das die Benutzeransprüche und die Genehmigung für den Zugriff auf die Web-API der mittleren Ebene (API A) enthält. API A muss nun eine authentifizierte Anfrage an die nachgelagerte Web-API stellen (API B).

Die folgenden Schritte entsprechen dem OBO-Fluss und werden anhand des folgenden Diagramms erläutert.

![Zeigt den On-Behalf-Of-Fluss von OAuth 2.0](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Die Clientanwendung stellt mit Token A eine Anforderung an die API A (mit dem Anspruch `aud` von API A).
1. API A wird beim Tokenausstellungs-Endpunkt der Microsoft Identity Platform authentifiziert und fordert ein Token für den Zugriff auf API B an.
1. Der Tokenausstellungs-Endpunkt von Microsoft Identity Platform überprüft neben Token A die Anmeldeinformationen von API A und stellt das Zugriffstoken für API B (Token B) für API A aus.
1. Token B wird von API A im Autorisierungsheader der Anforderung auf API B festgelegt.
1. Daten von der sicheren Ressource werden von API B an API A und dann an den Client zurückgegeben.

In diesem Szenario interagiert der Dienst auf der mittleren Ebene nicht mit dem Benutzer, um dessen Zustimmung für den Zugriff auf die nachgelagerte API zu erlangen. Aus diesem Grund muss die Zugriffsgewährung auf die nachgelagerte API zuvor als Teil des Genehmigungsschrittes während der Authentifizierung als Option angezeigt werden. Um zu erfahren, wie Sie dies für Ihre App einrichten, lesen Sie [Erhalten der Zustimmung für die Anwendung der mittleren Ebene](#gaining-consent-for-the-middle-tier-application).

## <a name="middle-tier-access-token-request"></a>Zugriffstokenanforderung auf der mittleren Ebene

Verwenden Sie zum Anfordern eines Zugriffstokens einen HTTP POST-Aufruf an das mandantenspezifischen Token der Microsoft Identity Platform unter Verwendung der folgenden Parameter:

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Es sind zwei Fälle denkbar – je nachdem, ob die Clientanwendung durch ein gemeinsames Geheimnis oder durch ein Zertifikat geschützt wird.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Erster Fall: Zugriffstokenanforderung mit einem gemeinsamen Geheimnis

Bei Verwendung eines gemeinsamen Geheimnisses enthält eine Dienst-zu-Dienst-Zugriffstokenanforderung die folgenden Parameter:

| Parameter | type | BESCHREIBUNG |
| --- | --- | --- |
| `grant_type` | Erforderlich | Typ der Tokenanforderung. Bei einer Anforderung mit JWT muss der Wert `urn:ietf:params:oauth:grant-type:jwt-bearer` sein. |
| `client_id` | Erforderlich | Die Anwendungs-ID (Client-ID), die Ihrer App im [Azure-Portal auf der Seite „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) zugewiesen wurde. |
| `client_secret` | Erforderlich | Der geheime Clientschlüssel, den Sie im Azure-Portal auf der Seite „App-Registrierungen“ für Ihre App generiert haben.  Das Standardauthentifizierungsmuster der Bereitstellung von Anmeldeinformationen im Autorisierungsheader gemäß [RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749#section-2.3.1) wird ebenfalls unterstützt. |
| `assertion` | Erforderlich | Das Zugriffstoken, das an die API der mittleren Ebene gesendet wurde.  Dieses Token muss über einen Zielgruppenanspruch (`aud`) der App verfügen, von der diese OBO-Anforderung stammt (durch das Feld `client-id` gekennzeichnete App). Anwendungen können kein Token für eine andere App einlösen. (Wenn also beispielsweise ein Client ein für MS Graph vorgesehenes Token an eine API sendet, kann die API dieses nicht mit OBO einlösen.  Stattdessen muss das Token abgelehnt werden.)  |
| `scope` | Erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen für die Tokenanforderung. Weitere Informationen finden Sie unter [Bereiche](v2-permissions-and-consent.md). |
| `requested_token_use` | Erforderlich | Gibt an, wie die Anforderung verarbeitet werden soll. Im OBO-Fluss muss der Wert muss auf `on_behalf_of` festgelegt werden. |

#### <a name="example"></a>Beispiel

Mit dem folgenden HTTP POST-Element wird ein Zugriffstoken und Aktualisierungstoken mit dem Bereich `user.read` für die Web-API https://graph.microsoft.com angefordert.

```HTTP
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&client_secret=sampleCredentia1s
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyO{a lot of characters here}
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Zweiter Fall: Zugriffstokenanforderung mit einem Zertifikat

Eine Dienst-zu-Dienst-Zugriffstokenanforderung mit einem Zertifikat enthält die folgenden Parameter:

| Parameter | type | BESCHREIBUNG |
| --- | --- | --- |
| `grant_type` | Erforderlich | Typ der Tokenanforderung Bei einer Anforderung mit JWT muss der Wert `urn:ietf:params:oauth:grant-type:jwt-bearer` sein. |
| `client_id` | Erforderlich |  Die Anwendungs-ID (Client-ID), die Ihrer App im [Azure-Portal auf der Seite „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) zugewiesen wurde. |
| `client_assertion_type` | Erforderlich | Der Wert muss `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` sein. |
| `client_assertion` | Erforderlich | Eine Assertion (ein JSON-Webtoken), die Sie benötigen, um das Zertifikat, das Sie als Anmeldeinformationen für Ihre Anwendung registriert haben, zu erstellen und sich damit anzumelden. Informationen zum Registrieren Ihres Zertifikats sowie zum Format der Assertion finden Sie im Abschnitt [Zertifikatanmeldeinformationen](active-directory-certificate-credentials.md). |
| `assertion` | Erforderlich |  Das Zugriffstoken, das an die API der mittleren Ebene gesendet wurde.  Dieses Token muss über einen Zielgruppenanspruch (`aud`) der App verfügen, von der diese OBO-Anforderung stammt (durch das Feld `client-id` gekennzeichnete App). Anwendungen können kein Token für eine andere App einlösen. (Wenn also beispielsweise ein Client ein für MS Graph vorgesehenes Token an eine API sendet, kann die API dieses nicht mit OBO einlösen.  Stattdessen muss das Token abgelehnt werden.)  |
| `requested_token_use` | Erforderlich | Gibt an, wie die Anforderung verarbeitet werden soll. Im OBO-Fluss muss der Wert muss auf `on_behalf_of` festgelegt werden. |
| `scope` | Erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen für die Tokenanforderung. Weitere Informationen finden Sie unter [Bereiche](v2-permissions-and-consent.md).|

Beachten Sie, dass die Parameter nahezu identisch mit den Parametern der Anforderung mit dem gemeinsamen geheimen Schlüssel sind. Einziger Unterschied: Anstelle des Parameters `client_secret` werden die beiden Parameter `client_assertion_type` und `client_assertion` verwendet.

#### <a name="example"></a>Beispiel

Mit dem folgenden HTTP POST-Element wird ein Zugriffstoken mit dem Bereich `user.read` für die Web-API https://graph.microsoft.com mit einem Zertifikat angefordert.

```HTTP
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiO{a lot of characters here}
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="middle-tier-access-token-response"></a>Zugriffstokenantwort auf der mittleren Ebene

Eine erfolgreiche Antwort enthält eine JSON OAuth 2.0-Antwort mit den folgenden Parametern.

| Parameter | BESCHREIBUNG |
| --- | --- |
| `token_type` | Gibt den Wert des Tokentyps an. Microsoft Identity Platform unterstützt nur den Typ `Bearer`. Weitere Informationen zu Bearertoken finden Sie unter [OAuth 2.0-Autorisierungsframework: Verwendung von Bearertoken (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | Der durch das Token gewährte Zugriffsbereich. |
| `expires_in` | Gibt an, wie lange das Zugriffstoken in Sekunden gültig ist. |
| `access_token` | Das angeforderte Zugriffstoken. Der aufrufende Dienst kann dieses Token verwenden, um die Authentifizierung für den empfangenden Dienst durchzuführen. |
| `refresh_token` | Das Aktualisierungstoken für das angeforderte Zugriffstoken. Der aufrufende Dienst kann dieses Token verwenden, um nach Ablauf des aktuellen Zugriffstokens ein neues Zugriffstoken anzufordern. Das Aktualisierungstoken wird nur bereitgestellt, wenn der Bereich `offline_access` angefordert wurde. |

### <a name="success-response-example"></a>Beispiel für eine erfolgreiche Antwort

Das folgende Beispiel zeigt eine erfolgreiche Antwort auf eine Anforderung eines Zugriffstokens für die Web-API https://graph.microsoft.com.

```json
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4An{a lot of characters here}"
}
```

Das obige Zugriffstoken ist ein Token mit v1.0-Formatierung für Microsoft Graph. Dies liegt daran, dass das Tokenformat auf der **Ressource** basiert, auf die zugegriffen wird, und nicht mit den Endpunkten in Zusammenhang steht, die für deren Anforderung verwendet werden. Microsoft Graph akzeptiert gemäß seiner Einrichtung v1.0-Token. Microsoft Identity Platform erzeugt daher v1.0-Zugriffstoken, wenn ein Client Token für Microsoft Graph anfordert. Andere Apps können signalisieren, dass sie Token im v2.0- oder v1.0-Format oder sogar proprietäre oder verschlüsselte Tokenformate bevorzugen.  Die Endpunkte der Versionen 1.0 und 2.0 können jedes beliebige Tokenformat ausgeben. Dadurch kann die Ressource immer das richtige Tokenformat abrufen, unabhängig davon, wie oder wo das Token vom Client angefordert wurde. 

[!INCLUDE [remind-not-to-validate-access-tokens](includes/remind-not-to-validate-access-tokens.md)]

### <a name="error-response-example"></a>Beispiel für eine fehlerhafte Antwort

Beim Versuch, ein Zugriffstoken für die Downstream-API abzurufen, gibt der Tokenendpunkt einen Fehler zurück, wenn bei dieser API eine Richtlinie für bedingten Zugriff (z. B. [mehrstufige Authentifizierung](../authentication/concept-mfa-howitworks.md)) festgelegt wurde. Der Dienst der mittleren Ebene zeigt diesen Fehler der Clientanwendung an, damit die Clientanwendung die entsprechende Benutzerinteraktion bereitstellen kann, um die Richtlinie für bedingten Zugriff zu erfüllen.

```json
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multifactor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Verwenden des Zugriffstokens für den Zugriff auf die gesicherte Ressource

Der Dienst der mittleren Ebene kann nun mit dem oben erhaltenen Token bei der nachgelagerte Web-API authentifizierte Anforderungen stellen. Hierfür wird das Token in den `Authorization`-Header eingetragen.

### <a name="example"></a>Beispiel

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>SAML-Assertionen, die mit einem OAuth 2.0-OBO-Fluss abgerufen wurden

Einige OAuth-basierte Webdienste benötigen Zugriff auf andere Webdienst-APIs, die SAML-Assertionen in nicht interaktiven Flüssen akzeptieren. Azure Active Directory kann es eine SAML-Assertion als Antwort auf einen Im-Namen-von-Fluss mit einem SAML-basierten Webdienst als Zielressource bereitstellen.

Dies ist eine nicht standardmäßige Erweiterung für den OAuth 2.0-Im-Namen-von-Fluss, der einer OAuth2-basierten Anwendung Zugriff auf Webdienst-API-Endpunkte ermöglicht, die SAML-Token nutzen.

> [!TIP]
> Wenn Sie einen geschützten SAML-Webdienst über eine Front-End-Anwendung aufrufen, können Sie einfach die API aufrufen und einen normalen interaktiven Authentifizierungsfluss initiieren, der die vorhandene Sitzung eines Benutzers verwendet. Sie müssen lediglich einen OBO-Fluss verwenden, wenn ein Dienst-zu-Dienst-Aufruf ein SAML-Token für die Bereitstellung des Benutzerkontexts erfordert.
 
 ### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Abrufen eines SAML-Tokens mithilfe einer OBO-Anforderung mit einem gemeinsamen Geheimnis

Eine Dienst-zu-Dienst-Anforderung für eine SAML-Assertion weist die folgenden Parameter auf:

| Parameter | type | BESCHREIBUNG |
| --- | --- | --- |
| grant_type |required | Typ der Tokenanforderung Bei Anforderungen mit einem JWT muss der Wert **urn:ietf:params:oauth:grant-type:jwt-bearer** lauten. |
| assertion |required | Der Wert des bei der Anforderung verwendeten Zugriffstoken.|
| client_id |required | Die dem aufrufenden Dienst während der Registrierung bei Azure AD zugewiesene App-ID. Wählen Sie zum Ermitteln der App-ID im Azure-Portal zuerst **Active Directory**, dann das Verzeichnis und schließlich den Anwendungsnamen aus. |
| client_secret |required | Schlüssel, der für den aufrufenden Dienst in Azure AD registriert ist. Dieser Wert sollte zum Zeitpunkt der Registrierung notiert worden sein.  Das Standardauthentifizierungsmuster der Bereitstellung von Anmeldeinformationen im Autorisierungsheader gemäß [RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749#section-2.3.1) wird ebenfalls unterstützt. |
| scope |required | Eine durch Leerzeichen getrennte Liste von Bereichen für die Tokenanforderung. Weitere Informationen finden Sie unter [Bereiche](v2-permissions-and-consent.md). Beispiel: 'https://testapp.contoso.com/user_impersonation openid' |
| requested_token_use |Erforderlich | Gibt an, wie die Anforderung verarbeitet werden soll. Im Im-Auftrag-Fluss muss der Wert **on_behalf_of** lauten. |
| requested_token_type | required | Gibt den Typ des angeforderten Token an. Abhängig von den Anforderungen der Ressource, auf die zugegriffen wird, kann der Wert **urn:Ietf:params:oauth:token-type:saml2** oder **urn:Ietf:params:oauth:token-type:saml1** lauten. |

Die Antwort enthält ein UTF8- und Base64url-codiertes SAML-Token.

- **SubjectConfirmationData für eine SAML-Assertion von einem OBO-Aufruf:** Wenn die Zielanwendung einen Empfängerwert in **SubjectConfirmationData** erfordert, muss als Wert in der Konfiguration der Ressourcenanwendung die Antwort-URL ohne Platzhalter festgelegt werden.
- **Der SubjectConfirmationData-Knoten:** Der Knoten darf kein **InResponseTo**-Attribut enthalten, da er nicht Teil einer SAML-Antwort ist. Die Anwendung, die das SAML-Token empfängt, muss die SAML-Assertion ohne **InResponseTo**-Attribut akzeptieren können.

- **Zustimmung:** Um ein SAML-Token zu empfangen, das Benutzerdaten zu einem OAuth-Flow enthält, muss Zustimmung erteilt worden sein. Informationen zu Berechtigungen und zum Erhalt der Zustimmung des Administrators finden Sie unter [Berechtigungen und Zustimmung im Azure Active Directory v1.0-Endpunkt](../azuread-dev/v1-permissions-consent.md).

### <a name="response-with-saml-assertion"></a>Antwort mit SAML-Assertion

| Parameter | BESCHREIBUNG |
| --- | --- |
| token_type |Gibt den Wert des Tokentyps an. **Bearertoken** ist der einzige Typ, den Azure AD unterstützt. Weitere Informationen zu Bearertoken finden Sie unter [OAuth 2.0-Autorisierungsframework: Verwendung von Bearertoken (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Der durch das Token gewährte Zugriffsbereich. |
| expires_in |Gibt an, wie lange das Zugriffstoken gültig ist (in Sekunden). |
| expires_on |Die Uhrzeit, zu der das Zugriffstoken abläuft. Das Datum wird als Anzahl der Sekunden ab 1970-01-01T0:0:0Z UTC bis zur Ablaufzeit dargestellt. Dieser Wert wird verwendet, um die Lebensdauer von zwischengespeicherten Token zu bestimmen. |
| resource |Der App-ID-URI des empfangenden Diensts (geschützte Ressource). |
| access_token |Der Parameter, der die SAML-Assertion zurückgibt. |
| refresh_token |Das Aktualisierungstoken. Der aufrufende Dienst kann dieses Token verwenden, um nach Ablauf der aktuellen SAML-Assertion ein neues Zugriffstoken anzufordern. |

- token_type: Bearer
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- resource: `https://api.contoso.com`
- access_token: \<SAML assertion\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \<Refresh token\>


## <a name="gaining-consent-for-the-middle-tier-application"></a>Erhalten der Zustimmung für die Anwendung der mittleren Ebene

Abhängig von der Architektur oder der Verwendung Ihrer Anwendung können Sie verschiedene Strategien in Betracht ziehen, um einen erfolgreichen OBO-Flow sicherzustellen. In allen Fällen soll ultimativ die Erteilung einer ordnungsgemäßen Zustimmung sichergestellt werden, damit die Client-App die App der mittleren Ebene aufrufen kann und die App der mittleren Ebene über die Berechtigung zum Aufrufen der Back-End-Ressource verfügt.

> [!NOTE]
> Bisher wurde das Feld „Bekannte Clientanwendung“ vom Microsoft-Kontosystem (persönliche Konten) weder unterstützt noch konnte eine kombinierte Zustimmung angezeigt werden.  Dies wurde nun hinzugefügt. Alle Apps in Microsoft Identity Platform können jetzt bei OBO-Aufrufen für das Abrufen der Einwilligung diesen Ansatz („bekannte Clientanwendung“) verwenden.

### <a name="default-and-combined-consent"></a>/.default und kombinierten Einwilligung

Die Anwendung der mittleren Ebene fügt den Client in ihrem Manifest der Liste der bekannten Clientanwendungen hinzu, und dann kann der Client einen kombinierten Einwilligungsfluss für sich selbst und die Anwendung der mittleren Ebene auslösen. Für Microsoft Identity Platform erfolgt dies mithilfe des [`/.default`-Bereichs](v2-permissions-and-consent.md#the-default-scope). Wenn Sie einen Zustimmungsbildschirm mit bekannten Clientanwendungen und `/.default` auslösen, zeigt der Bildschirm die Berechtigungen des Client für die API der mittleren Ebene an **und** fordert auch die von der API der mittleren Ebene benötigten Berechtigungen an. Der Benutzer gibt die Einwilligung für beide Anwendungen, und dann funktioniert der OBO-Fluss.

### <a name="pre-authorized-applications"></a>Vorautorisierte Anwendungen

Ressourcen können angeben, dass eine bestimmte Anwendung immer die Berechtigung hat, bestimmte Bereiche zu empfangen. Dies ist vor allem nützlich, um die Verbindungen zwischen einem Front-End-Client und einer Back-End-Ressource reibungsloser zu gestalten. Eine Ressource kann mehrere vorautorisierte Anwendungen deklarieren – jede dieser Anwendungen kann diese Berechtigungen in einem OBO-Fluss anfordern und ohne Einwilligung des Benutzers empfangen.

### <a name="admin-consent"></a>Administratorzustimmung

Ein Mandantenadministrator kann garantieren, dass Anwendungen die Berechtigung haben, ihre erforderlichen APIs aufzurufen, indem er die Einwilligung des Administrators für die Anwendung der mittleren Ebene erteilt. Dazu kann der Administrator die Anwendung der mittleren Ebene in seinem Mandanten suchen, die erforderliche Berechtigungsseite öffnen und die Berechtigung für die App erteilen. Weitere Informationen zur Einwilligung des Administrators finden Sie in der [Dokumentation zu Einwilligungen und Berechtigungen](v2-permissions-and-consent.md).

### <a name="use-of-a-single-application"></a>Verwenden einer einzelnen Anwendung

In einigen Szenarios kann es vorkommen, dass Sie nur eine einzige Paarung eines Clients der mittleren Ebene und eines Front-End-Clients haben. In diesem Szenario ist es möglicherweise einfacher, eine einzige Anwendung zu erstellen, wodurch die Notwendigkeit einer Anwendung auf mittlerer Ebene ganz entfällt. Um zwischen der Front-End- und der Web-API zu authentifizieren, können Sie Cookies, ein "id_token" oder ein Zugriffstoken für die Anwendung selbst angefordert. Fordern Sie dann die Einwilligung dieser einzelnen Anwendung zur Back-End-Ressource an.

## <a name="client-limitations"></a>Clienteinschränkungen

Wenn ein Client den impliziten Fluss verwendet, um ein „id_token“ abzurufen, und dieser Client auch über Platzhalter in einer Antwort-URL verfügt, kann das „id_token“ nicht für einen OBO-Fluss verwendet werden.  Allerdings kann ein vertraulicher Client weiterhin Zugriffstoken einlösen, die über den Fluss für die implizite Gewährung erworben wurden, selbst wenn der initiierende Client eine Antwort-URL mit Platzhaltern registriert hat.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das OAuth 2.0-Protokoll und eine andere Möglichkeit, Dienst-zu-Dienst-Authentifizierungen mit Client-Anmeldedaten auszuführen.

* [Gewähren von OAuth 2.0-Clientanmeldeinformationen auf der Microsoft Identity Platform](v2-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0-Codeflow auf der Microsoft Identity Platform](v2-oauth2-auth-code-flow.md)
* [Verwenden des `/.default`-Bereichs](v2-permissions-and-consent.md#the-default-scope)
