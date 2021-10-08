---
title: Verwenden der MSAL in einer nationalen Cloud-App | Azure
titleSuffix: Microsoft identity platform
description: Die Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) ermöglicht Anwendungsentwicklern das Abrufen von Token für den Aufruf von geschützten Web-APIs. Bei diesen Web-APIs kann es sich um Microsoft Graph, andere Microsoft-APIs, Partner-Web-APIs oder um Ihre eigene Web-API handeln. Die MSAL unterstützt mehrere Anwendungsarchitekturen und -plattformen.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/21/2021
ms.author: negoe
ms.reviewer: marsma, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8b8ae31aa2af84a6f8dfd4f93c90a09e58805f73
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598798"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Verwenden der MSAL in einer nationalen Cloudumgebung

[Nationale Clouds](authentication-national-cloud.md) (auch als Sovereign Clouds bezeichnet) sind physisch isolierte Azure-Instanzen. Diese Regionen von Azure tragen dazu bei, dass Anforderungen in puncto Datenresidenz, Datenhoheit und Compliance innerhalb geografischer Grenzen erfüllt werden.

Neben der weltweiten Microsoft-Cloud ermöglicht die Microsoft Authentication Library (MSAL) den Anwendungsentwicklern in nationalen Clouds das Abrufen von Token für die Authentifizierung und das Aufrufen geschützter Web-APIs. Bei diesen Web-APIs kann es sich um Microsoft Graph oder um andere Microsoft-APIs handeln.

Neben der globalen Azure-Cloud wird Azure Active Directory (Azure AD) in den folgenden nationalen Clouds bereitgestellt: 

- Azure Government
- Azure China 21Vianet
- Azure Deutschland ([Schließt am 29. Oktober 2021)](https://www.microsoft.com/cloud-platform/germany-cloud-regions)

Dieser Leitfaden veranschaulicht das Anmelden bei Geschäfts-, Schul- oder Unikonten, das Abrufen eines Zugriffstokens sowie das Aufrufen der Microsoft Graph-API in der [Azure Government](https://azure.microsoft.com/global-infrastructure/government/)-Cloudumgebung.

## <a name="azure-germany-microsoft-cloud-deutschland"></a>Azure Deutschland (Microsoft Cloud Deutschland)

> [!WARNING]
> Azure Deutschland (Microsoft Cloud Deutschland) wird am [29. Oktober 2021 geschlossen](https://www.microsoft.com/cloud-platform/germany-cloud-regions). Auf Dienste und Anwendungen, die Sie _nicht_ bis zu diesem Datum zu einer Region im globalen Azure migriert haben, kann nicht mehr zugegriffen werden.

Wenn Sie Ihre Anwendung noch nicht aus Azure Deutschland migriert haben, befolgen Sie die [Informationen zu Azure Active Directory für die Migration aus Azure Deutschland](/microsoft-365/enterprise/ms-cloud-germany-transition-azure-ad) für die ersten Schritte.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zu Beginn sicher, dass die folgenden Voraussetzungen erfüllt sind:

### <a name="choose-the-appropriate-identities"></a>Auswählen der geeigneten Identitäten

[Azure Government](../../azure-government/index.yml)-Anwendungen können zur Authentifizierung von Benutzern sowohl Azure AD Government-Identitäten als auch öffentliche Azure AD-Identitäten verwenden. Da Sie eine beliebige dieser Identitäten verwenden können, wählen Sie einen Autoritätsendpunkt für Ihr Szenario aus:

- Öffentliche Azure AD-Identität: Wird häufig verwendet, wenn Ihre Organisation bereits über einen öffentlichen Azure AD-Mandanten verfügt, um Microsoft 365 (öffentlich oder GCC) oder eine andere Anwendung zu unterstützen.
- Azure AD Government-Identität: Wird häufig verwendet, wenn Ihre Organisation bereits über einen Azure AD Government-Mandanten verfügt, um Office 365 (GCC High oder DoD) zu unterstützen, oder wenn ein neuer Mandant in Azure AD Government erstellt werden soll.

Nach dieser Entscheidung müssen Sie sich überlegen, wo die App-Registrierung erfolgen soll. Wenn Sie öffentliche Azure AD-Identitäten für Ihre Azure Government-Anwendung auswählen, müssen Sie die Anwendung in Ihrem öffentlichen Azure AD-Mandanten registrieren.

### <a name="get-an-azure-government-subscription"></a>Erhalten eines Azure Government-Abonnements

Informationen zum Beziehen eines Azure Government-Abonnements finden Sie unter [Verwalten und Herstellen einer Verbindung mit Ihrem Abonnement in Azure Government](../../azure-government/compare-azure-government-global-azure.md).

Wenn Sie kein Azure Government-Abonnement besitzen, können Sie zu Beginn ein [kostenloses Konto](https://azure.microsoft.com/global-infrastructure/government/request/) erstellen.

Weitere Informationen zur Verwendung einer nationalen Cloud mit einer bestimmten Programmiersprache erhalten Sie, indem Sie die Registerkarte entsprechend der gewünschten Sprache auswählen:

## <a name="net"></a>[.NET](#tab/dotnet)

Über MSAL.NET können Sie Benutzer anmelden, Token anfordern und die Microsoft Graph-API in nationalen Clouds aufrufen.

In den folgenden Tutorials erfahren Sie, wie Sie eine .NET Core 2.2-MVC-Web-App erstellen. Die App verwendet OpenID Connect, um Benutzer mit einem Geschäfts-, Schul- oder Unikonto in einer Organisation anzumelden, die zu einer nationalen Cloud gehört.

- Informationen zum Anmelden von Benutzern und Abrufen von Token finden Sie in diesem Tutorial: [Erstellen einer ASP.NET Core-Web-App, die Benutzer mit der Microsoft Identity Platform bei Sovereign Clouds anmeldet](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Informationen zum Aufrufen der Microsoft Graph-API finden Sie in diesem Tutorial: [Verwenden der Microsoft Identity Platform zum Abrufen der Microsoft Graph-API aus einer ASP.NET Core 2.x-Web-App im Namen eines Benutzers, der sich mit seinem Geschäfts-, Schul- oder Unikonto bei einer nationalen Microsoft-Cloud anmeldet](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascript"></a>[JavaScript](#tab/javascript)

So aktivieren Sie Ihre MSAL.js-Anwendung für Sovereign Clouds

- Registrieren Sie Ihre Anwendung abhängig von der Cloud in einem bestimmten Portal. Weitere Informationen zum Auswählen des Portals finden Sie unter [App-Registrierungsendpunkte](authentication-national-cloud.md#app-registration-endpoints).
- Verwenden Sie abhängig von der Cloud eines der [Beispiele](https://github.com/Azure-Samples/ms-identity-javascript-tutorial) aus dem Repository mit einigen Änderungen, die im Folgenden genannt werden.
- Verwenden Sie abhängig von der Cloud, in der Sie die Anwendung registriert haben, eine bestimmte Autorisierung. Weitere Informationen zu autoritativen Stellen für verschiedene Clouds finden Sie unter [Azure AD-Authentifizierungsendpunkte](authentication-national-cloud.md#azure-ad-authentication-endpoints).
- Zum Aufrufen der Microsoft Graph-API ist eine Endpunkt-URL erforderlich, die für die von Ihnen verwendete Cloud spezifisch ist. Informationen zum Ermitteln von Microsoft Graph-Endpunkten für alle nationalen Clouds finden Sie unter [Stammendpunkte für Microsoft Graph und Graph Explorer](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Hier ist ein Beispiel für eine Autorisierung:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

Hier finden Sie ein Beispiel für einen Microsoft Graph-Endpunkt mit einem Bereich:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

Im Folgenden finden Sie den minimalen Code zum Authentifizieren eines Benutzers bei einer Sovereign Cloud und zum Aufrufen von Microsoft Graph:

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        redirectUri: "/",
    }
};

// Initialize MSAL
const msalObj = new PublicClientApplication(msalConfig);

// Get token using popup experience
try {
    const graphToken = await msalObj.acquireTokenPopup({
        scopes: ["User.Read"]
    });
} catch(error) {
    console.log(error)
}

// Call the Graph API
const headers = new Headers();
const bearer = `Bearer ${graphToken}`;

headers.append("Authorization", bearer);

fetch("https://graph.microsoft.us/v1.0/me", {
    method: "GET",
    headers: headers
})
```


## <a name="python"></a>[Python](#tab/python)

So aktivieren Sie Ihre MSAL-Python-Anwendung für Sovereign Clouds

- Registrieren Sie Ihre Anwendung abhängig von der Cloud in einem bestimmten Portal. Weitere Informationen zum Auswählen des Portals finden Sie unter [App-Registrierungsendpunkte](authentication-national-cloud.md#app-registration-endpoints).
- Verwenden Sie abhängig von der Cloud eines der [Beispiele](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) aus dem Repository mit einigen Änderungen, die im Folgenden genannt werden.
- Verwenden Sie abhängig von der Cloud, in der Sie die Anwendung registriert haben, eine bestimmte Autorisierung. Weitere Informationen zu Autorisierungen für verschiedene Clouds finden Sie unter [Azure AD-Authentifizierungsendpunkte](authentication-national-cloud.md#azure-ad-authentication-endpoints).

    Hier ist ein Beispiel für eine Autorisierung:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- Zum Aufrufen der Microsoft Graph-API ist eine Endpunkt-URL erforderlich, die für die von Ihnen verwendete Cloud spezifisch ist. Informationen zum Ermitteln von Microsoft Graph-Endpunkten für alle nationalen Clouds finden Sie unter [Stammendpunkte für Microsoft Graph und Graph Explorer](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

    Hier finden Sie ein Beispiel für einen Microsoft Graph-Endpunkt mit einem Bereich:

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

So aktivieren Sie Ihre MSAL-Java-Anwendung für Sovereign Clouds

- Registrieren Sie Ihre Anwendung abhängig von der Cloud in einem bestimmten Portal. Weitere Informationen zum Auswählen des Portals finden Sie unter [App-Registrierungsendpunkte](authentication-national-cloud.md#app-registration-endpoints).
- Verwenden Sie abhängig von der Cloud eines der [Beispiele](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) aus dem Repository mit einigen Änderungen, die im Folgenden genannt werden.
- Verwenden Sie abhängig von der Cloud, in der Sie die Anwendung registriert haben, eine bestimmte Autorisierung. Weitere Informationen zu Autorisierungen für verschiedene Clouds finden Sie unter [Azure AD-Authentifizierungsendpunkte](authentication-national-cloud.md#azure-ad-authentication-endpoints).

Hier ist ein Beispiel für eine Autorisierung:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Zum Aufrufen der Microsoft Graph-API ist eine Endpunkt-URL erforderlich, die für die von Ihnen verwendete Cloud spezifisch ist. Informationen zum Ermitteln von Microsoft Graph-Endpunkten für alle nationalen Clouds finden Sie unter [Stammendpunkte für Microsoft Graph und Graph Explorer](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Hier ist ein Beispiel für einen Graph-Endpunkt mit einem Bereich:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

MSAL für iOS und macOS kann zum Abrufen von Token in nationalen Clouds verwendet werden. Für die Erstellung von `MSALPublicClientApplication` ist jedoch zusätzliche Konfiguration erforderlich.

Wenn Sie beispielsweise möchten, dass Ihre Anwendung eine mehrinstanzenfähige Anwendung in einer nationalen Cloud (in diesem Fall US Government) ist, könnten Sie Folgendes schreiben:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swift"></a>[Swift](#tab/swift)

MSAL für iOS und macOS kann zum Abrufen von Token in nationalen Clouds verwendet werden. Für die Erstellung von `MSALPublicClientApplication` ist jedoch zusätzliche Konfiguration erforderlich.

Wenn Sie beispielsweise möchten, dass Ihre Anwendung eine mehrinstanzenfähige Anwendung in einer nationalen Cloud (in diesem Fall US Government) ist, könnten Sie Folgendes schreiben:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Nächste Schritte

Unter [Authentifizierungsendpunkte der nationalen Cloud](authentication-national-cloud.md) finden Sie eine Liste der Azure-Portal-URLs und der Tokenendpunkte für jede Cloud.

Dokumentation zur nationalen Cloud:

- [Azure Government](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure Deutschland (Schließt am 29. Oktober 2021)](../../germany/index.yml)