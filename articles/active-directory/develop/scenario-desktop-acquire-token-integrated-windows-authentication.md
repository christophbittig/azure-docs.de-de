---
title: Erwerben eines Tokens zum Aufrufen einer Web-API mit integrierter Windows-Authentifizierung (Desktop-App) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine Desktop-Anwendung erstellen, die Web-APIs aufruft, um ein Token für die Anwendung mit integrierter Windows-Authentifizierung zu erwerben
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/25/2021
ms.author: sahmalik
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: f589603fc5a7bb1b9e3a62d997cf9e64efebf5af
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017937"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-using-integrated-windows-authentication"></a>Desktop-Anwendung, die Web-APIs aufruft: Erwerben Sie ein Token mit integrierter Windows-Authentifizierung

Verwenden Sie die integrierte Windows-Authentifizierung (IWA), um einen Domänenbenutzer auf einer Domäne oder einem mit Azure AD verbundenen Computer anzumelden.

## <a name="constraints"></a>Einschränkungen

- Die integrierte Windows-Authentifizierung ist nur für *federated+* Benutzer verfügbar, d. h. für Benutzer, die in Active Directory erstellt wurden und von Azure AD unterstützt werden. Direkt in Azure AD erstellte Benutzer ohne Azure Active Directory-Unterstützung (d. h. *verwaltete* Benutzer) können diesen Authentifizierungsflow nicht verwenden. Diese Einschränkung wirkt sich nicht auf den Flow mit Benutzername und Kennwort aus.
- Die [mehrstufige Authentifizierung (Multi-Factor Authentication, MFA)](../authentication/concept-mfa-howitworks.md) wird von der IWA nicht umgangen. Wenn MFA konfiguriert ist, kann IWA fehlschlagen, wenn eine MFA-Abfrage erforderlich ist, da bei MFA eine Benutzerinteraktion benötigt wird.

    IWA ist nicht interaktiv, die mehrstufige Authentifizierung erfordert jedoch eine Benutzerinteraktion. Wann der Identitätsanbieter eine mehrstufige Authentifizierung anfordert, wird nicht von Ihnen gesteuert, sondern vom Mandantenadministrator. Nach unserer Erfahrung ist die mehrstufige Authentifizierung erforderlich, wenn Sie sich aus einem anderen Land/einer anderen Region anmelden, nicht über ein VPN mit einem Unternehmensnetzwerk verbunden sind und gelegentlich sogar dann, wenn eine VPN-Verbindung besteht. Erwarten Sie keinen deterministischen Satz von Regeln. Azure AD greift auf KI zurück, um kontinuierlich zu lernen, wann MFA erforderlich ist. Greifen Sie beim Fehlschlagen von IWA auf eine Eingabeaufforderung für Benutzer wie die interaktive Authentifizierung oder den Gerätecodeflow zurück.

- Für die in `PublicClientApplicationBuilder` übergebene Autorität gelten folgende Voraussetzungen:
  - Sie muss auf Mandanten beruhen (im Format `https://login.microsoftonline.com/{tenant}/`, wobei `tenant` entweder die GUID ist, die die Mandanten-ID darstellt, oder eine Domäne, die dem Mandanten zugeordnet ist).
  - Sie muss für ein Geschäfts-, Schul- oder Unikonto bestimmt sein: `https://login.microsoftonline.com/organizations/`.
  - Persönliche Microsoft-Konten werden nicht unterstützt. Sie können keine Mandanten vom Typ „/common“ oder „/consumers“ verwenden.

- Denn die integrierte Windows-Authentifizierung ist ein stiller Fluss:
  - Der Benutzer muss vorher in die Nutzung Ihrer Anwendung eingewilligt haben.
  - Oder der Mandantenadministrator muss zuvor für alle Benutzer im Mandanten zugestimmt haben, die Anwendung zu nutzen.
  - Anders gesagt:
    - Entweder haben Sie als Entwickler im Azure-Portal die Schaltfläche **Gewähren** für sich selbst ausgewählt.
    - Oder ein Mandantenadministrator hat bei der Anwendungsregistrierung auf der Registerkarte **API-Berechtigungen** die Schaltfläche **Administratoreinwilligung für {Mandantendomäne} erteilen/widerrufen** ausgewählt. Weitere Informationen finden Sie unter [Hinzufügen von Zugriffsberechtigungen für Ihre Web-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).
    - Oder Sie haben eine Möglichkeit für Benutzer eingeräumt, der Anwendung zuzustimmen. Weitere Informationen finden Sie unter [Anfordern der Zustimmung einzelner Benutzer](./v2-permissions-and-consent.md#requesting-individual-user-consent).
    - Oder Sie haben eine Möglichkeit für den Mandantenadministrator eingeräumt, der Anwendung zuzustimmen. Weitere Informationen finden Sie unter [Zustimmung des Administrators](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

- Dieser Flow ist aktiviert für .NET Desktop-, .NET Core- und UWP-Apps.

Weitere Informationen zur Einwilligung finden Sie unter [Berechtigungen und Einwilligung in Microsoft Identity Platform](./v2-permissions-and-consent.md).

## <a name="learn-how-to-use-it"></a>Informationen zur Verwendung

# <a name="net"></a>[.NET](#tab/dotnet)

Verwenden Sie in MSAL.NET Folgendes:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Normalerweise benötigen Sie nur einen Parameter (`scopes`). Je nach Einrichtung der Richtlinien durch Ihren Windows-Administrator kann es jedoch möglich sein, dass Anwendungen auf dem Windows-Computer den angemeldeten Benutzer nicht abfragen dürfen. Verwenden Sie in diesem Fall eine zweite Methode (`.WithUsername()`), und übergeben Sie den Benutzernamen des angemeldeten Benutzers im UPN-Format, z. B. `joe@contoso.com`.

Im folgenden Beispiel wird der aktuelle Fall veranschaulicht. Außerdem erhalten Sie Erläuterungen zu den Ausnahmearten und den entsprechenden Problembehandlungen.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }

 Console.WriteLine(result.Account.Username);
}
```

Die Liste der möglichen Modifizierer für AcquireTokenByIntegratedWindowsAuthentication finden Sie unter [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

Dies ist ein Auszug aus den [MSAL Java-Entwicklungsbeispielen](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Dieser Flow gilt nicht für macOS.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Dieser Flow wird in MSAL Node noch nicht unterstützt.

# <a name="python"></a>[Python](#tab/python)

Dieser Flow wird in MSAL Python noch nicht unterstützt.

---
## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Aufrufen einer Web-API von der Desktop-App](scenario-desktop-call-api.md).
