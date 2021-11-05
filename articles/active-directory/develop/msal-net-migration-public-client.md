---
title: Migrieren von öffentlichen Clientanwendungen zu MSAL.NET
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine öffentliche Clientanwendung von der Azure Active Directory-Authentifizierungsbibliothek für .NET (ADAL.NET) zur Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET) migrieren.
services: active-directory
author: sahmalik
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/31/2021
ms.author: sahmalik
ms.reviewer: saeeda, shermanouko, jmprieur
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 0bb1dc80d9bebf0488bfa2a272f5f7fa5303b31b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018013"
---
# <a name="migrate-public-client-applications-from-adalnet-to-msalnet"></a>Migrieren öffentlicher Clientanwendungen von ADAL.NET zu MSAL.NET

In diesem Artikel wird beschrieben, wie Sie eine öffentliche Clientanwendung von der Azure Active Directory-Authentifizierungsbibliothek für .NET (ADAL.NET) zur Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET) migrieren. Öffentliche Clientanwendungen sind Desktop-Apps, einschließlich Win32-, WPF- und UWP-Apps sowie mobile Apps, die einen anderen Dienst im Namen des Benutzers/der Benutzerin aufrufen. Weitere Informationen zu öffentlichen Anwendungen finden Sie unter [Authentifizierungsflows und Anwendungsszenarios](authentication-flows-app-scenarios.md).

## <a name="migration-steps"></a>Schritte bei der Migration

1. Suchen Sie in Ihrer App nach Code, der ADAL.NET verwendet.

   Code in einer öffentlichen Clientanwendung, der ADAL nutzt, instanziiert `AuthenticationContext` und ruft eine Überschreibung von `AcquireTokenAsync` auf. Hierbei werden die folgenden Parameter verwendet:

   - Eine `resourceId`-Zeichenfolge. Diese Variable ist der App-ID-URI der Web-API, die Sie aufrufen möchten.
   - Ein `clientId`, der der Bezeichner für Ihre Anwendung ist, wird auch als App-ID bezeichnet.

2. Nachdem Sie festgestellt haben, dass Sie über Apps mit Verwendung von ADAL.NET verfügen, installieren Sie das MSAL.NET-NuGet-Paket [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) und aktualisieren Ihre Projektbibliotheksverweise. Weitere Informationen finden Sie unter [Installieren eines NuGet-Pakets](https://www.bing.com/search?q=install+nuget+package).

3. Aktualisieren Sie den Code entsprechend dem Szenario der öffentlichen Clientanwendung. Einige der erforderlichen Schritte gelten für alle Szenarios mit öffentlichen Clients. Andere Schritte gelten jeweils nur für ein einzelnes Szenario. 

   Die öffentlichen Clientszenarios sind:

   - [Webauthentifizierungs-Manager](scenario-desktop-acquire-token-wam.md), die bevorzugte brokerbasierte Authentifizierung auf Windows.
   - [Interaktive Authentifizierung](scenario-desktop-acquire-token-interactive.md), bei der dem Benutzer eine webbasierte Schnittstelle angezeigt wird, um den Anmeldevorgang abzuschließen.
   - [Integrierte Windows-Authentifizierung](scenario-desktop-acquire-token-integrated-windows-authentication.md), bei der sich ein Benutzer mit derselben Identität anmeldet, die er auch für die Anmeldung in einer Windows-Domäne verwendet (für domänenverbundene oder Azure AD-verbundene Computer).
   - [Benutzername/Passwort](scenario-desktop-acquire-token-username-password.md), bei dem die Anmeldung durch Angabe eines Benutzernamens/Passworts erfolgt.
   - [Gerätecodefluss](scenario-desktop-acquire-token-device-code-flow.md), bei dem ein Gerät mit eingeschränkter UX Ihnen einen Gerätecode anzeigt, um den Authentifizierungsfluss auf einem alternativen Gerät abzuschließen.


## <a name="interactive"></a>[Interactive](#tab/interactive)

In interaktiven Szenarios zeigt Ihre öffentliche Clientanwendung eine in einem Browser gehostete Anmeldebenutzeroberfläche an, und der*die Benutzer*in muss sich interaktiv anmelden.

#### <a name="find-out-if-your-code-uses-interactive-scenarios"></a>Finden Sie heraus, ob Ihr Code interaktive Szenarios verwendet.

Der ADAL-Code für Ihre App in einer öffentlichen Clientanwendung, die die interaktive Authentifizierung verwendet, instanziiert `AuthenticationContext` und enthält einen Aufruf von `AcquireTokenAsync` mit den folgenden Parametern.
 - Eine `clientId`, bei der es sich um eine GUID handelt, stellt Ihre Anwendungsregistrierung dar
 - Eine `resourceUrl`, die die Ressource angibt, für die Sie das Token anfordern
 - Einem URI, bei dem es sich um die Antwort-URL handelt
 - Ein `PlatformParameters`-Objekt. 

 #### <a name="update-the-code-for-interactive-scenarios"></a>Aktualisieren des Codes für interaktive Szenarios

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

In diesem Fall ersetzen wir den Aufruf von `AuthenticationContext.AcquireTokenAsync` durch einen Aufruf von `IPublicClientApplication.AcquireTokenInteractive`.

Im Folgenden sehen Sie eine Gegenüberstellung des ADAL.NET- und MSAL.NET-Codes für interaktive Szenarios:

:::row:::
:::column span="":::
    ADAL
:::column-end:::
:::column span="":::
    MSAL
:::column-end:::
:::row-end:::
:::row:::
:::column span="":::
      
```csharp
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await ac.AcquireTokenAsync("<clientId>",
                                    "https://resourceUrl",
                                    new Uri("https://ClientReplyUrl"),
                                    new PlatformParameters(PromptBehavior.Auto));
```
:::column-end:::   
:::column span="":::
```csharp
// 1. Configuration - read below about redirect URI
var pca = PublicClientApplicationBuilder.Create("client_id")
              .WithBroker()
              .Build();

// Add a token cache, see https://docs.microsoft.com/en-us/azure/active-directory/develop/msal-net-token-cache-serialization?tabs=desktop

// 2. GetAccounts
var accounts = await pca.GetAccountsAsync();
var accountToLogin = // choose an account, or null, or use PublicClientApplication.OperatingSystemAccount for the default OS account

try
{
    // 3. AcquireTokenSilent 
    var authResult = await pca.AcquireTokenSilent(new[] { "User.Read" }, accountToLogin)
                              .ExecuteAsync();
}
catch (MsalUiRequiredException) // no change in the pattern
{
    // 4. Specific: Switch to the UI thread for next call . Not required for console apps.
    await SwitchToUiThreadAsync(); // not actual code, this is different on each platform / tech

    // 5. AcquireTokenInteractive
    var authResult = await pca.AcquireTokenInteractive(new[] { "User.Read" })
                              .WithAccount(accountToLogin)  // this already exists in MSAL, but it is more important for WAM
                              .WithParentActivityOrWindow(myWindowHandle) // to be able to parent WAM's windows to your app (optional, but highly recommended; not needed on UWP)
                              .ExecuteAsync();
}
```
   :::column-end:::
:::row-end:::

Im oben angezeigten MSAL-Code wird WAM (Webauthentifizierungs-Manager) verwendet. Dies ist der empfohlene Ansatz. Wenn Sie die interaktive Authentifizierung ohne WAM verwenden möchten, finden Sie weitere Informationen unter [Interaktive Authentifizierung](scenario-desktop-acquire-token-interactive.md).

## <a name="integrated-windows-authentication"></a>[Integrierte Windows-Authentifizierung](#tab/iwa)

Bei der integrierten Windows-Authentifizierung meldet sich Ihre öffentliche Client-Anwendung mit derselben Identität an, mit der sie sich bei einer Windows-Domäne angemeldet hat (für domänenverbundene oder Azure AD-verbundene Computer).

#### <a name="find-out-if-your-code-uses-integrated-windows-authentication"></a>Finden Sie heraus, ob Ihr Code integrierte Windows-Authentifizierung verwendet

Der ADAL-Code für Ihre Anwendung verwendet integrierte Windows-Authentifizierungsszenarien, wenn er einen Aufruf an `AcquireTokenAsync` enthält, der als Erweiterungsmethode der Klasse `AuthenticationContextIntegratedAuthExtensions` mit den folgenden Parametern verfügbar ist:

- Eine `resource`, die die Ressource angibt, für die Sie das Token anfordern
- Eine `clientId`, bei der es sich um eine GUID handelt, stellt Ihre Anwendungsregistrierung dar
- Ein Objekt `UserCredential`, das den*die Benutzer*in darstellt, für den Sie das Token anfordern möchten

#### <a name="update-the-code-for-integrated-windows-authentication-scenarios"></a>Aktualisierung des Codes für integrierte Windows-Authentifizierungsszenarien

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

In diesem Fall ersetzen wir den Aufruf von `AuthenticationContext.AcquireTokenAsync` durch einen Aufruf von `IPublicClientApplication.AcquireTokenByIntegratedWindowsAuth`.

Hier ist ein Vergleich von ADAL.NET- und MSAL.NET-Code für integrierte Windows-Authentifizierungsszenarien:

:::row:::
:::column span="":::
    ADAL
:::column-end:::
:::column span="":::
    MSAL
:::column-end:::
:::row-end:::
:::row:::
:::column span="":::
      
```csharp
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await context.AcquireTokenAsync(resource, clientId,
                                         new UserCredential("john@contoso.com"));
```
:::column-end:::   
:::column span="":::
```csharp
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
   :::column-end:::
:::row-end:::

## <a name="username-password"></a>[Benutzernamenkennwort (Username Password)](#tab/up)

Authentifizierung via Benutzername/Kennwort, bei dem die Anmeldung erfolgt, indem die Benutzername/Kennwort-Anmeldeinformationen angegeben werden.
#### <a name="find-out-if-your-code-uses-username-password-authentication"></a>Ermitteln Sie, ob Ihr Code die Authentifizierung mit Benutzername und Kennwort verwendet.

Der ADAL-Code für Ihre App verwendet Benutzername/Kennwortauthentifizierungsszenarios, wenn er einen Aufruf von `AcquireTokenAsync` enthält, der als Erweiterungsmethode der Klasse`AuthenticationContextIntegratedAuthExtensions` mit den folgenden Parametern verfügbar ist:

- Eine `resource`, die die Ressource angibt, für die Sie das Token anfordern
- Eine `clientId`, bei der es sich um eine GUID handelt, stellt Ihre Anwendungsregistrierung dar
- Ein Objekt `UserPasswordCredential`, das den Benutzernamen und das Kennwort für den*die Benutzer*in enthält, für den Sie das Token anfordern möchten.

#### <a name="update-the-code-for-username-password-auth-scenarios"></a>Aktualisieren des Codes für Authentifizierungsszenarios für Benutzername-/Kennwortauthentifizierungsszenarios

In diesem Fall ersetzen wir den Aufruf von `AuthenticationContext.AcquireTokenAsync` durch einen Aufruf von `IPublicClientApplication.AcquireTokenByUsernamePassword`.

Im Folgenden sehen Sie eine Gegenüberstellung des ADAL.NET- und MSAL.NET-Codes für Benutzername-/Kennwortszenarios:

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

:::row:::
:::column span="":::
    ADAL
:::column-end:::
:::column span="":::
    MSAL
:::column-end:::
:::row-end:::
:::row:::
:::column span="":::
      
```csharp
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await context.AcquireTokenAsync(
   resource, clientId, 
   new UserPasswordCredential("john@contoso.com", johnsPassword));

```
:::column-end:::   
:::column span="":::
```csharp
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
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
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
```
   :::column-end:::
:::row-end:::

## <a name="device-code"></a>[Gerätecode](#tab/devicecode)

Gerätecodeflow-Authentifizierung, bei dem ein Gerät mit eingeschränkter Benutzeroberfläche einen Gerätecode anzeigt, um den Authentifizierungsablauf auf einem alternativen Gerät abzuschließen.

#### <a name="find-out-if-your-code-uses-device-code-flow-authentication"></a>Ermitteln Sie, ob Ihr Code die Gerätcodeflowauthentifizierung verwendet.

Der ADAL-Code für Ihre App verwendet Gerätcodeflowszenarios, wenn er einen Aufruf von `AuthenticationContext.AcquireTokenByDeviceCodeAsync` mit den folgenden Parametern enthält:
- Eine Objektinstanz `DeviceCodeResult`, die mit der der Ressource `resourceID` instanziiert wird, für die Sie ein Token fordern, und eine `clientId`, die der GUID entspricht, die Ihre Anwendung darstellt.

#### <a name="update-the-code-for-device-code-flow-scenarios"></a>Aktualisieren des Codes für Gerätecodeflowszenarios

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

In diesem Fall ersetzen wir den Aufruf von `AuthenticationContext.AcquireTokenAsync` durch einen Aufruf von `IPublicClientApplication.AcquireTokenWithDeviceCode`.

Im Folgenden sehen Sie eine Gegenüberstellung des ADAL.NET- und MSAL.NET-Codes für Gerätecodeflowszenarios:

:::row:::
:::column span="":::
    ADAL
:::column-end:::
:::column span="":::
    MSAL
:::column-end:::
:::row-end:::
:::row:::
:::column span="":::
      
```csharp
static async Task<AuthenticationResult> GetTokenViaCode(AuthenticationContext ctx)
{
 AuthenticationResult result = null;
 try
 {
  result = await ac.AcquireTokenSilentAsync(resource, clientId);
 }
 catch (AdalException adalException)
 {
  if (adalException.ErrorCode == AdalError.FailedToAcquireTokenSilently
   || adalException.ErrorCode == AdalError.InteractionRequired)
  {
  try
  {
   DeviceCodeResult codeResult = await ctx.AcquireDeviceCodeAsync(resource, clientId);
   Console.WriteLine("You need to sign in.");
   Console.WriteLine("Message: " + codeResult.Message + "\n");
   result = await ctx.AcquireTokenByDeviceCodeAsync(codeResult);
  }
  catch (Exception exc)
  {
   Console.WriteLine("Something went wrong.");
   Console.WriteLine("Message: " + exc.Message + "\n");
  }
 }
 return result;
}

```
:::column-end:::   
:::column span="":::
```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }
}

private static async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }

    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
   :::column-end:::
:::row-end:::

---
### <a name="msal-benefits"></a>Vorteile von MSAL

Dies sind die wichtigsten Vorteile von MSAL.NET für Ihre Anwendung:

- **Resilienz**. MSAL.NET verbessert in folgender Weise die Resilienz Ihrer App:

   - Vorteile durch Azure AD Cached Credential Service (CCS). CCS fungiert als Azure AD-Sicherung.
   - Token werden proaktiv erneuert, wenn die von Ihnen aufgerufene API über eine [fortlaufende Zugriffsevaluierung](app-resilience-continuous-access-evaluation.md) langlebige Token unterstützt.

### <a name="troubleshooting"></a>Problembehandlung

Die folgenden Informationen zur Problembehandlung gehen von zwei Annahmen aus: 

- Ihr ADAL.NET-Code hat funktioniert.
- Bei der Migration zu MSAL wurde die Client-ID beibehalten.

Es wird eine Ausnahme ausgelöst, und Ihnen wird eine der folgenden Meldungen angezeigt: 

> `AADSTS90002: Tenant 'cf61953b-e41a-46b3-b500-663d279ea744' not found. This may happen if there are no active`
> `subscriptions for the tenant. Check to make sure you have the correct tenant ID. Check with your subscription`
> `administrator.`

Sie können die Ausnahme mithilfe der folgenden Schritte beheben:

1. Stellen Sie sicher, dass Sie die neueste Version von MSAL.NET verwenden.
1. Vergewissern Sie sich, dass der Autoritätshost, den Sie beim Erstellen der vertraulichen Clientanwendung festgelegt haben, und der mit ADAL verwendete Autoritätshost übereinstimmen. Insbesondere sollte dieselbe [Cloud](msal-national-cloud.md) (Azure Government, Azure China 21Vianet oder Azure Deutschland) verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die [Unterschiede zwischen ADAL.NET- und MSAL.NET-Apps](msal-net-differences-adal-net.md).
Erfahren Sie mehr über die [Serialisierung des Tokencaches in MSAL.NET](msal-net-token-cache-serialization.md).
