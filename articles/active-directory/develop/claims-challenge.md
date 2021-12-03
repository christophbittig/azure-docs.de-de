---
title: Anspruchsherausforderungen, Anspruchsanforderungen und Clientfunktionen
titleSuffix: Microsoft identity platform
description: Die Erklärung der Anspruchsherausforderungen, Anspruchsanforderungen und Clientfunktionen auf der Microsoft Identity-Plattform.
services: active-directory
author: knicholasa
manager: martinco
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 05/11/2021
ms.author: nichola
ms.reviewer: kkrishna, kylemar
ms.openlocfilehash: 6d1998673b3295129c494b4568e1ba6313984898
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067251"
---
# <a name="claims-challenges-claims-requests-and-client-capabilities"></a>Anspruchsherausforderungen, Anspruchsanforderungen und Clientfunktionen

Eine *Anspruchsaufforderung* ist eine Antwort, die von einer API gesendet wird und angibt, dass ein von einer Clientanwendung gesendetes Zugriffstoken über unzureichende Ansprüche verfügt. Dies kann daran liegen, dass das Token die für diese API festgelegten Richtlinien für bedingten Zugriff nicht erfüllt oder das Zugriffstoken widerrufen wurde.

Von der Clientanwendung wird eine *Anspruchsanforderung* gestellt, um den Benutzer zurück an den Identitätsanbieter umzuleiten, um ein neues Token mit Ansprüchen abzurufen, die die zusätzlichen Anforderungen erfüllen, die nicht erfüllt wurden.

Die Anwendungen, die erweiterte Sicherheitsfunktionen wie [Fortlaufende Zugriffsevaluierung (CAE)](../conditional-access/concept-continuous-access-evaluation.md) und den [Authentifizierungskontext für bedingten Zugriff](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/granular-conditional-access-for-sensitive-data-and-actions/ba-p/1751775) verwenden, müssen darauf vorbereitet sein, die Anspruchsherausforderungen zu bewältigen.

Ihre Anwendung wird nur Anspruchsherausforderungen von beliebten Diensten wie [Microsoft Graph](/graph/overview) erhalten, wenn sie ihre [Clientfunktionen ](#client-capabilities) in ihren Aufrufen an den Dienst deklariert.

## <a name="claims-challenge-header-format"></a>Anspruchsausforderung: Headerformat

Die Anspruchsherausforderung ist eine Anweisung, die als ein `www-authenticate`-Header von einer API zurückgegeben wird, wenn ein [Zugriffstoken](access-tokens.md), das ihr präsentiert wird, nicht autorisiert ist und stattdessen ein neues Zugriffstoken mit den richtigen Funktionen erforderlich ist. Die Anspruchsherausforderung besteht aus mehreren Teilen. Ein Teil ist der HTTP-Statuscode der Antwort und der andere ist der `www-authenticate`-Header, der selbst aus mehreren Teilen besteht und eine Anspruchsanweisung enthalten muss.

Hier sehen Sie ein Beispiel:

```https
HTTP 401; Unauthorized

www-authenticate =Bearer realm="", authorization_uri="https://login.microsoftonline.com/common/oauth2/authorize", error="insufficient_claims", claims="eyJhY2Nlc3NfdG9rZW4iOnsiYWNycyI6eyJlc3NlbnRpYWwiOnRydWUsInZhbHVlIjoiYzEifX19"
```

 **HTTP-Statuscode**: Muss **401 Nicht autorisiert** lauten.

**www-authenticate-Antwortheader** mit:

| Parameter    | Erforderlich/optional | Beschreibung |
|--------------|-------------|--------------|
| Authentifizierungsart | Erforderlich | Muss **Bearer** sein.|
| Realm | Optional | Die Mandanten-ID oder der Domänenname des Mandanten (z. B. microsoft.com), auf die zugegriffen wird. MUSS eine leere Zeichenfolge sein, wenn die Authentifizierung den gemeinsamen [Endpunkt](howto-convert-app-to-be-multi-tenant.md#update-your-code-to-send-requests-to-common) durchläuft. |
| `authorization_uri` | Erforderlich | Der URI des Autorisierungsendpunkts, an dem bei Bedarf eine interaktive Authentifizierung durchgeführt werden kann. Wenn im Bereich angegeben, MÜSSEN die Mandanteninformationen im authorization_uri enthalten sein. Wenn realm eine leere Zeichenfolge ist, MUSS die authorization_uri für den [allgemeinen Endpunkt](howto-convert-app-to-be-multi-tenant.md#update-your-code-to-send-requests-to-common) gelten. |
| `error` | Erforderlich | Muss „insufficient_claims“ sein, wenn eine Anspruchsaufforderung generiert werden soll. | 
| `claims` | Erforderlich, wenn der Fehler „insufficient_claims“ lautet. | Eine Zeichenfolge in Anführungszeichen, die eine Base64-codierte [Anspruchsanforderung](https://openid.net/specs/openid-connect-core-1_0.html#ClaimsParameter) enthält. Die Anspruchsanforderung sollte Ansprüche für den „access_token“ auf der obersten Ebene des JSON-Objekts anfordern. Der Wert (angeforderte Ansprüche) ist kontextabhängig und wird später in diesem Dokument angegeben. Aus Größengründen SOLLTEN Anwendungen der vertrauenden Seite den JSON-Code vor der Base64-Codierung minimieren. Der unformatierte JSON-Code des obigen Beispiels lautet {"access_token":{"acrs":{"essential":true,"value":"cp1"}}}. |

Die Antwort **401** kann mehr als einen `www-authenticate`-Header enthalten. Alle Felder in der vorangehenden Tabelle müssen in demselben `www-authenticate`-Header enthalten sein. Der `www-authenticate`-Header, der die Anspruchsherausforderung enthält, *kann* andere Felder enthalten. Felder im Header sind ungeordnet. Gemäß RFC 7235 darf jeder Parametername nur einmal pro Abfrage des Authentifizierungsschemas auftreten.

## <a name="claims-request"></a>Anspruchsanforderung

Wenn eine Anwendung eine Anspruchsherausforderung erhält, zeigt sie an, dass das vorherige Zugriffstoken nicht mehr als gültig angesehen wird. In diesem Szenario sollte die Anwendung das Token aus jedem lokalen Cache oder jeder Benutzersitzung löschen. Anschließend sollte der angemeldete Benutzer zurück an das Azure Active Directory umgeleitet werden. Hier kann er ein neues Token unter Verwendung von [OAuth 2.0-Autorisierungscode-Flow](v2-oauth2-auth-code-flow.md) mit einem *Anspruchsparameter* abrufen, der die zusätzlichen nicht erfüllten Anforderungen erfüllt.

Hier sehen Sie ein Beispiel:

```https
GET https://login.microsoftonline.com/14c2f153-90a7-4689-9db7-9543bf084dad/oauth2/v2.0/authorize
?client_id=2810aca2-a927-4d26-8bca-5b32c1ef5ea9
&redirect_uri=https%3A%2F%contoso.com%3A44321%2Fsignin-oidc
&response_type=code
&scope=openid%20profile%20offline_access%20user.read%20Sites.Read.All
&response_mode=form_post
&login_hint=kalyan%ccontoso.onmicrosoft.com
&domain_hint=organizations
claims=%7B%22access_token%22%3A%7B%22acrs%22%3A%7B%22essential%22%3Atrue%2C%22value%22%3A%22c1%22%7D%7D%7D
```

Die Anspruchsaufforderung sollte als Teil aller Aufrufe des [/authorize](v2-oauth2-auth-code-flow.md#request-an-authorization-code)-Endpunkts von Azure AD übergeben werden, bis ein Token erfolgreich abgerufen wurde und danach nicht mehr benötigt wird.

Zum Auffüllen des Anspruchsparameters muss der Entwickler Folgendes unternehmen:

1. Decodieren Sie die zuvor empfangene Base64-Zeichenfolge.
2. URL-kodieren Sie die Zeichenfolge und fügen Sie sie erneut dem Parameter **claims** hinzu.

Nach Abschluss dieses Ablaufs erhält die Anwendung ein Zugriffstoken mit den zusätzlichen Ansprüchen, die belegen, dass der Benutzer die erforderlichen Bedingungen erfüllt hat.

## <a name="client-capabilities"></a>Clientfunktionen

Die Clientfunktionen helfen den Ressourcenanbietern wie der Web-API, zu erkennen, ob die aufrufende Clientanwendung die Anspruchsherausforderung versteht und dann ihre Antwort entsprechend anpassen kann. Diese Funktion kann nützlich sein, wenn nicht alle API-Clients die Anspruchsherausforderungen bewältigen können und einige frühere Versionen weiterhin eine andere Antwort erwarten.

Einige beliebte Anwendungen wie [Microsoft Graph](/graph/overview) senden Anspruchsherausforderungen nur, wenn die aufrufende Client-App deklariert, dass sie sie mithilfe von *Clientfunktionen* verarbeiten kann.

Um zusätzlichen Datenverkehr oder Auswirkungen auf die Benutzerfreundlichkeit zu vermeiden, geht Azure AD davon aus, dass Ihre App Ansprüche verarbeiten kann, es sei denn, Sie entscheiden sich explizit dafür. Eine Anwendung erhält keine Anspruchsherausforderungen (und kann die zugehörigen Features wie CAE-Token nicht verwenden), es sei denn, sie deklariert, dass sie bereit ist, sie mit der „cp1“-Funktion zu behandeln.

### <a name="how-to-communicate-client-capabilities-to-azure-ad"></a>Wie Clientfunktionen an Azure AD kommuniziert werden

Der folgende Beispiel-Anspruchsparameter zeigt, wie eine Clientanwendung ihre Funktion zum Azure AD in einem [OAuth 2.0-Autorisierungscodefluss](v2-oauth2-auth-code-flow.md) kommuniziert.

```json
Claims: {"access_token":{"xms_cc":{"values":["cp1"]}}}
```

Diejenigen, die die MSAL-Bibliothek verwenden, verwenden den folgenden Code:

```c#
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
 .WithDefaultRedirectUri()
 .WithAuthority(authority)
 .WithClientCapabilities(new [] {"cp1"})
 .Build();*
```

Benutzer, die Microsoft.Identity.Web verwenden, können der Konfigurationsdatei den folgenden Code hinzufügen:

```c#
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    // the remaining settings
    // ... 
    "ClientCapabilities": [ "cp1" ]
},
```

Ein Beispiel dafür, wie die Anforderung an Azure AD aussieht:

```https
GET https://login.microsoftonline.com/14c2f153-90a7-4689-9db7-9543bf084dad/oauth2/v2.0/authorize
?client_id=2810aca2-a927-4d26-8bca-5b32c1ef5ea9
&redirect_uri=https%3A%2F%contoso.com%3A44321%2Fsignin-oidc
&response_type=code
&scope=openid%20profile%20offline_access%20user.read%20Sites.Read.All
&response_mode=form_post
&login_hint=kalyan%ccontoso.onmicrosoft.com
&domain_hint=organizations
&claims=%7B%22access_token%22%3A%7B%22xms_cc%22%3A%7B%22values%22%3A%5B%22cp1%22%5D%7D%7D%7D
```

Wenn Sie bereits über eine Nutzlast für den Anspruchsparameter verfügen, fügen Sie diese dem vorhandenen Satz hinzu.

Beispiel: Sie verfügen bereits über die folgende Antwort von einem Authentifizierungskontextvorgang für den Bedingungszugriff

```json
{"access_token":{"acrs":{"essential":true,"value":"c25"}}}
```

Sie würden die Clientfunktion in der vorhandenen **Anspruchsnutzlast** vorausbeschreiten.

```json
{"access_token":{"xms_cc":{"values":["cp1"]},"acrs":{"essential":true,"value":"c25"}}}
```

## <a name="receiving-xms_cc-claim-in-an-access-token"></a>Empfangen xms_cc Anspruchs in einem Zugriffstoken

Um Informationen darüber zu erhalten, ob Clientanwendungen Anspruchsherausforderungen bewältigen können, muss ein API-Implementator **xms_cc** als optionalen Anspruch im Anwendungsmanifest anfordern.

Der **xms_cc** Anspruch mit dem Wert „cp1“ im Zugriffstoken ist die autoritative Methode, um zu bestimmen, dass eine Clientanwendung eine Anspruchsforderung behandeln kann. **xms_cc** ist ein optionaler Anspruch, der nicht immer im Zugriffstoken ausgestellt wird, selbst wenn der Client eine Anspruchsanforderung mit „xms_cc“ sendet. Damit ein Zugriffstoken den Anspruch **xms_cc** enthalten kann, muss die Ressourcenanwendung (d. h. die API-Implementierung) xms_cc als [optionalen Anspruch](active-directory-optional-claims.md) im Anwendungsmanifest anfordern. Bei einer Anforderung als optionaler Anspruch wird **xms_cc** nur dann dem Zugriffstoken hinzugefügt, wenn die Clientanwendung **xms_cc** in der Anspruchsanforderung sendet. Der Wert der **xms_cc** Anspruchsanforderung wird als Wert des **xms_cc** Anspruchs in das Zugriffstoken eingeschlossen, wenn es sich um einen bekannten Wert handelt. Der einzige derzeit bekannte Wert ist **cp1**.

Bei den ungeordneten Werten wird nicht zwischen Groß- und Kleinschreibung unterschieden. Wenn in der **xms_cc** Anspruchsanforderung mehr als ein Wert angegeben ist, handelt es sich bei diesen Werten um eine mehrwertige Sammlung als Wert des **xms_cc**-Anspruchs.

Eine Anforderung von :

```json
{ "access_token": { "xms_cc":{"values":["cp1","foo", "bar"] } }}
```

führt zu einem Anspruch von

```json
"xms_cc": ["cp1", "foo", "bar"]
```

im Zugriffstoken, wenn **cp1**, **foo** und **bar** bekannte Funktionen sind.

So sieht das Manifest der App aus, nachdem der **xms_cc** [optionale Anspruch](active-directory-optional-claims.md) angefordert wurde

```c#
"optionalClaims":
{
    "accessToken": [
    {
        "additionalProperties": [],
        "essential": false,
        "name": "xms_cc",
        "source": null
    }],
    "idToken": [],
    "saml2Token": []
}
```

Die API kann dann ihre Antworten basierend darauf anpassen, ob der Client Anspruchsaufforderungen verarbeiten kann oder nicht.

Beispiel in C#

```c#
Claim ccClaim = context.User.FindAll(clientCapabilitiesClaim).FirstOrDefault(x => x.Type == "xms_cc");
if (ccClaim != null && ccClaim.Value == "cp1")
{
    // Return formatted claims challenge as this client understands this
}
else
{
    // Throw generic exception
    throw new UnauthorizedAccessException("The caller does not meet the authentication bar to carry our this operation. The service cannot allow this operation");
}
```

## <a name="next-steps"></a>Nächste Schritte

- [Microsoft Identity Platform und der OAuth 2.0-Autorisierungscodeflow](v2-oauth2-auth-code-flow.md#request-an-authorization-code)
- [Verwenden von CAE-fähigen APIs in Ihren Anwendungen](app-resilience-continuous-access-evaluation.md)
- [Granularer bedingter Zugriff für sensible Daten und Aktionen](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/granular-conditional-access-for-sensitive-data-and-actions/ba-p/1751775)
