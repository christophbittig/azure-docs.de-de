---
title: Erwerben Sie ein Token zum Aufrufen einer Web-API mithilfe des Gerätecodeflusses (Desktop-App) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine Desktop-Anwendung erstellen, die Web-APIs aufruft, um ein Token für die Anwendung mithilfe des Gerätecodeflusses zu erwerben.
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
ms.openlocfilehash: dad9c8f85c778e2b75320a8ce9605e484ae39cfa
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838194"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-using-device-code-flow"></a>Desktop-Anwendung, die Web-APIs aufruft: Erwerben Sie ein Token mit dem Gerätecodefluss 

Verwenden Sie den Gerätecodeflow, wenn Sie ein Befehlszeilentool schreiben, das keine Websteuerelemente enthält, und Sie dabei die obigen Flows nicht verwenden können oder möchten.

## <a name="device-code-flow"></a>Gerätecodeflow

Für die interaktive Authentifizierung mit Azure AD wird ein Webbrowser benötigt. Weitere Informationen finden Sie unter [Verwenden von Webbrowsern](https://aka.ms/msal-net-uses-web-browser). Für die Authentifizierung von Benutzern bei Geräten oder Betriebssystemen ohne Webbrowser ermöglicht der Gerätecodeflow dem Benutzer, ein anderes Gerät wie einen Computer oder ein Mobiltelefon zu verwenden, um sich interaktiv anzumelden. Mithilfe des Gerätecodeflows ruft die Anwendung Token in einem zweistufigen Prozess ab, der für diese Geräte oder Betriebssysteme entwickelt wurde. Beispiele für solche Anwendungen sind iOT-Anwendungen oder Befehlszeilentools (CLI). Dahinter steckt folgender Gedanke:

1. Wenn eine Benutzerauthentifizierung erforderlich ist, stellt die App einen Code für den Benutzer bereit. Der Benutzer wird aufgefordert, ein anderes Gerät (z. B. ein Smartphone mit Internetverbindung) zu verwenden, um zu einer URL (z. B. `https://microsoft.com/devicelogin`) zu wechseln. Anschließend wird der Benutzer aufgefordert, den Code einzugeben. Danach wird der Benutzer auf der Webseite durch einen normalen Authentifizierungsprozess geführt, der ggf. auch Zustimmungsaufforderungen und die mehrstufige Authentifizierung umfasst.

2. Nach erfolgreicher Authentifizierung empfängt die Befehlszeilen-App die erforderlichen Token über einen Backchannel und führt damit die benötigten Web-API-Aufrufe aus.

## <a name="use-it"></a>Nutzen Sie sie.

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication` enthält die Methode `AcquireTokenWithDeviceCode`.

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Diese Methode nimmt folgende Parameter an:

- `scopes`, für den ein Zugriffstoken angefordert werden soll.
- Ein Rückruf, der [`DeviceCodeResult`](/dotnet/api/microsoft.identity.client.devicecoderesult) empfängt.

Im folgenden Beispielcode wird eine Übersicht aktueller Fälle gezeigt. Außerdem erhalten Sie Erläuterungen zu möglicherweise ausgelösten Ausnahmen und den zugehörigen Problembehandlungen. Ein voll funktionsfähiges Codebeispiel finden Sie auf GitHub unter [active-directory-dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2).

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

# <a name="java"></a>[Java](#tab/java)

Dies ist ein Auszug aus den [MSAL Java-Entwicklungsbeispielen](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

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

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
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

Dies ist ein Auszug aus den [MSAL Node-Entwicklungsbeispielen](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/device-code).

```javascript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const deviceCodeRequest = {
    deviceCodeCallback: (response) => (console.log(response.message)),
    scopes: ["user.read"],
    timeout: 20,
};

pca.acquireTokenByDeviceCode(deviceCodeRequest).then((response) => {
    console.log(JSON.stringify(response));
}).catch((error) => {
    console.log(JSON.stringify(error));
});
```

# <a name="python"></a>[Python](#tab/python)

Dies ist ein Auszug aus den [MSAL Python-Entwicklungsbeispielen](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

---

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Aufrufen einer Web-API von der Desktop-App](scenario-desktop-call-api.md).
