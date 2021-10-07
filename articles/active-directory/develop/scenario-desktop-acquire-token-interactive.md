---
title: Abrufen eines Tokens zum interaktiven Aufrufen einer Web-API (Desktop-App) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine Desktop-App erstellen, die Web-APIs aufruft, um ein Token interaktiv für die App abzurufen.
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/25/2021
ms.author: sahmalik
ms.custom: aaddev, devx-track-python, has-adal-ref
ms.openlocfilehash: 6cd9e4ff795e1051267aa20e244e03c37c15f831
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838187"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-interactively"></a>Web-APIs aufrufende Desktop-App – Interaktiver Aufruf eines Tokens

Das folgende Beispiel enthält den mindestens erforderlichen Code zum interaktiven Abrufen eines Tokens zum Lesen des Benutzerprofils mit Microsoft Graph.

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>In MSAL.NET

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>Erforderliche Parameter

`AcquireTokenInteractive` verfügt über nur einen obligatorischen Parameter (``scopes``) mit einer Enumeration von Zeichenfolgen, die die Bereiche definieren, für die ein Token erforderlich ist. Wenn das Token für Microsoft Graph bestimmt ist, finden Sie die erforderlichen Bereiche in der API-Referenz der einzelnen Microsoft Graph-APIs im Abschnitt „Berechtigungen“. Zum [Auflisten der Kontakte des Benutzers](/graph/api/user-list-contacts) muss beispielsweise der Bereich „User.Read“, „Contacts.Read“ verwendet werden. Weitere Informationen finden Sie in der [Microsoft Graph-Referenz zu Berechtigungen](/graph/permissions-reference).

Bei Android müssen Sie außerdem mit `.WithParentActivityOrWindow` die übergeordnete Aktivität angeben (siehe Abbildung), sodass das Token nach der Interaktion wieder an die betreffende übergeordnete Aktivität zurückgegeben wird. Wenn Sie diese nicht angeben, wird beim Aufrufen von `.ExecuteAsync()` eine Ausnahme ausgelöst.

### <a name="specific-optional-parameters-in-msalnet"></a>Spezifische optionale Parameter in MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Die Benutzeroberfläche ist wichtig, da sie interaktiv ist. `AcquireTokenInteractive` hat einen speziellen optionalen Parameter, der für die unterstützenden Plattformen die übergeordnete Benutzeroberfläche angeben kann. Bei Verwendung in einer Desktopanwendung weist `.WithParentActivityOrWindow` je nach Plattform einen anderen Typ auf. Alternativ können Sie beim Erstellen eines Fensters auf den optionalen Parameter für das übergeordnete Fenster verzichten, wenn Sie nicht steuern möchten, wo das Anmeldedialogfeld auf dem Bildschirm angezeigt wird. Diese Vorgehensweise würde sich für befehlszeilenbasierte Anwendungen eignen und zum Weiterleiten von Aufrufen an andere Back-End-Dienste verwendet werden. Dabei werden keine Fenster für die Benutzerinteraktion benötigt.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .NET Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Anmerkungen:

- In .NET Standard ist das erwartete `object` `Activity` für Android, `UIViewController` für iOS, `NSWindow` für Mac und `IWin32Window` oder `IntPr` für Windows.
- Unter Windows müssen Sie `AcquireTokenInteractive` aus dem UI-Thread aufrufen, sodass der eingebettete Browser den entsprechenden UI-Synchronisierungskontext abruft. Erfolgt kein Aufruf aus dem UI-Thread, werden Meldungen möglicherweise nicht fehlerfrei übertragen, oder es treten Deadlock-Szenarien in der Benutzeroberfläche auf. Sie können Microsoft-Authentifizierungsbibliotheken (MSALs) aus dem UI-Thread aufrufen, wenn Sie sich nicht bereits im UI-Thread befinden, indem Sie `Dispatcher` in WPF verwenden.
- Wenn Sie mit WPF ein Fenster aus einem WPF-Steuerelement abrufen, können Sie die `WindowInteropHelper.Handle`-Klasse verwenden. Dann erfolgt der Aufruf aus einem WPF-Steuerelement (`this`):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

Mit `WithPrompt()` wird durch Angabe einer Eingabeaufforderung die Interaktivität mit dem Benutzer gesteuert. Das genaue Verhalten kann mithilfe der Struktur [Microsoft.Identity.Client.Prompt](/dotnet/api/microsoft.identity.client.prompt) gesteuert werden.

Die Struktur definiert die folgenden Konstanten:

- `SelectAccount`: Hiermit wird STS gezwungen, das Dialogfeld zur Kontoauswahl mit Konten anzuzeigen, für die der Benutzer über eine Sitzung verfügt. Diese Option ist nützlich, wenn Anwendungsentwickler Benutzern die Auswahl unter verschiedenen Identitäten ermöglichen möchten. Diese Option bewirkt, dass MSAL `prompt=select_account` an den Identitätsanbieter sendet. Diese Option ist die Standardeinstellung. Damit wird die bestmögliche Benutzererfahrung auf Grundlage der verfügbaren Informationen bereitgestellt (z. B. Konto, Vorhandensein einer Sitzung für den Benutzer). Ändern Sie diese Option nicht ohne triftigen Grund.
- `Consent`: Hiermit kann der Anwendungsentwickler erzwingen, dass der Benutzer zur Zustimmung aufgefordert wird, selbst wenn die Zustimmung zuvor erteilt wurde. In diesem Fall sendet MSAL `prompt=consent` an den Identitätsanbieter. Diese Option kann in bestimmten sicherheitsorientierten Anwendungen verwendet werden, wenn die Governance des Unternehmens vorschreibt, dass für den Benutzer bei jeder Verwendung der Anwendung das Zustimmungsdialogfeld angezeigt wird.
- `ForceLogin`: Hiermit kann der Anwendungsentwickler angeben, dass der Dienst den Benutzer zur Eingabe von Anmeldeinformationen auffordert, selbst wenn eine solche Benutzerabfrage nicht erforderlich ist. Diese Option kann nützlich sein, um dem Benutzer das erneute Anmelden zu ermöglichen, wenn das Abrufen eines Tokens fehlschlägt. In diesem Fall sendet MSAL `prompt=login` an den Identitätsanbieter. Diese Option wird gelegentlich in sicherheitsorientierten Anwendungen verwendet, wenn die Governance des Unternehmens vorschreibt, dass sich Benutzer beim Zugriff auf bestimmte Teile einer Anwendung stets erneut anmelden müssen.
- `Create` löst eine Anmeldeumgebung aus, die durch Senden von `prompt=create` an den Identitätsanbieter für External Identities verwendet wird. Diese Eingabeaufforderung sollte nicht für Azure AD B2C-Apps gesendet werden. Weitere Informationen finden Sie unter [Hinzufügen eines Benutzerflows für die Self-Service-Registrierung zu einer App](../external-identities/self-service-sign-up-user-flow.md).
- `Never` (nur für .NET 4.5 und WinRT) fragt den Benutzer nicht ab, sondern versucht stattdessen, das in der ausgeblendeten eingebetteten Webansicht gespeicherte Cookie zu verwenden. Weitere Informationen finden Sie unter „Webansichten“ in MSAL.NET. Die Verwendung dieser Option schlägt möglicherweise fehl. In diesem Fall löst `AcquireTokenInteractive` eine Ausnahme aus mit der Meldung, dass eine Interaktion auf der Benutzeroberfläche erforderlich ist. Sie müssen einen anderen `Prompt`-Parameter verwenden.
- `NoPrompt` sendet keine Aufforderung an den Identitätsanbieter. Dieser entscheidet daher, dem Benutzer die beste Anmeldeerfahrung bereitzustellen (einmaliges Anmelden oder Kontoauswahl). Diese Option empfiehlt sich nur für Richtlinien zur Bearbeitung von Azure Active Directory (Azure AD) B2C-Profilen. Weitere Informationen finden Sie unter [Spezifische Informationen zu Azure AD B2C](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withuseembeddedwebview"></a>WithUseEmbeddedWebView

Mit dieser Methode können Sie angeben, ob Sie die Verwendung einer eingebetteten Webansicht oder der Systemwebansicht (sofern verfügbar) erzwingen möchten. Weitere Informationen finden Sie unter [Verwenden von Webbrowsern](msal-net-web-browsers.md).

```csharp
var result = await app.AcquireTokenInteractive(scopes)
                    .WithUseEmbeddedWebView(true)
                    .ExecuteAsync();
```

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Dieser Modifizierer wird in einem erweiterten Szenario verwendet, in dem der Benutzer vorab seine Einwilligung für mehrere Ressourcen erteilen soll (und nicht die schrittweise Einwilligung, die normalerweise bei MSAL.NET/der Microsoft Identity Platform verwendet wird). Weitere Informationen finden Sie unter [Einholen der Vorauseinwilligung des Benutzers für verschiedene Ressourcen](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Eine Webbenutzeroberfläche ist ein Mechanismus zum Aufrufen eines Browsers. Dieser Mechanismus kann ein dediziertes Benutzeroberflächen-Webbrowser-Steuerelement oder eine Möglichkeit sein, das Öffnen des Browsers zu delegieren.
MSAL stellt zwar für die meisten Plattformen Implementierungen von Webbenutzeroberflächen bereit, dennoch gibt es Fälle, in denen Sie den Browser möglicherweise selbst hosten möchten:

- Plattformen, die nicht explizit von MSAL abgedeckt werden, z. B. Blazor, Unity, Mono auf Desktops.
- Sie möchten die Benutzeroberfläche Ihrer Anwendung testen und einen automatisierten Browser nutzen, der mit Selenium verwendet werden kann.
- Der Browser und die App, die MSAL ausführen, befinden sich in separaten Prozessen.

##### <a name="at-a-glance"></a>Auf einen Blick

Um dies zu erreichen, übergeben Sie eine `start Url` an MSAL, die in einem Browser Ihrer Wahl angezeigt werden muss, damit der Endbenutzer Eingaben (z. B. seinen Benutzernamen) vornehmen kann.
Nach Abschluss der Authentifizierung muss Ihre App die `end Url`, die einen von Azure AD bereitgestellten Code enthält, an MSAL zurückgeben.
Der Host der `end Url` ist immer der `redirectUri`. Zum Abfangen der `end Url` können Sie folgende Aktionen ausführen:

- Überwachen von Browserumleitungen, bis die `redirect Url` erreicht wird.
- Umleiten des Browsers zu einer URL, die Sie überwachen.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi ist ein Erweiterungspunkt

`WithCustomWebUi` ist ein Erweiterungspunkt, mit dem Sie Ihre eigene Benutzeroberfläche in öffentlichen Clientanwendungen bereitstellen können. Sie können auch angeben, dass der Benutzer den /Authorize-Endpunkt des Identitätsanbieters durchlaufen, sich anmelden und einwilligen muss. MSAL.NET kann dann den Authentifizierungscode einlösen und ein Token abrufen. Er wird beispielsweise in Visual Studio verwendet, damit Electron-Anwendungen (z. B. Visual Studio-Feedback) die Webinteraktion bereitstellen, während der Großteil der Arbeiten von MSAL.NET ausgeführt wird. Die Verwendung empfiehlt sich auch, wenn Sie UI-Automatisierung bereitstellen möchten. In öffentlichen Clientanwendungen verwendet MSAL.NET den PKCE-Standard (Proof Key for Code Exchange), um die Einhaltung der Sicherheit zu gewährleisten. Der Code kann nur von MSAL.NET eingelöst werden. Weitere Informationen finden Sie unter [RFC 7636 – Proof Key for Code Exchange by OAuth Public Clients](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Verwenden von WithCustomWebUi

Führen Sie folgende Schritte aus, um `.WithCustomWebUI` zu verwenden.

  1. Implementieren Sie die `ICustomWebUi`-Schnittstelle. Weitere Informationen finden Sie auf [dieser Website](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Implementieren Sie eine `AcquireAuthorizationCodeAsync`-Methode, und übernehmen Sie die von MSAL.NET berechnete URL für den Autorisierungscode. Lassen Sie dann den Benutzer die Interaktion mit dem Identitätsanbieter durchlaufen und die URL zurückgeben, über die der Identitätsanbieter Ihre Implementierung zusammen mit dem Autorisierungscode wieder aufgerufen hätte. Bei auftretenden Problemen löst Ihre Implementierung eine `MsalExtensionException`-Ausnahme aus, um ordnungsgemäß mit MSAL zusammenzuarbeiten.
  2. Verwenden Sie in Ihrem `AcquireTokenInteractive`-Aufruf den `.WithCustomUI()`-Modifizierer, der die Instanz Ihrer benutzerdefinierten Webbenutzeroberfläche übergibt.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Beispiele für die Implementierung von „ICustomWebUi“ in einer Testautomatisierung: SeleniumWebUI

Das MSAL.NET-Team hat die Benutzeroberflächentests so umgeschrieben, dass Sie diesen Erweiterungsmechanismus verwenden können. Schauen Sie sich bei Interesse die [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160)-Klasse im MSAL.NET-Quellcode an.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Mehr Benutzerfreundlichkeit mit SystemWebViewOptions

Von MSAL.NET 4.1 [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions) können Sie Folgendes angeben:

- URI, zu dem navigiert werden soll (`BrowserRedirectError`), oder das anzuzeigende HTML-Fragment (`HtmlMessageError`), wenn Anmelde- oder Einwilligungsfehler im Systemwebbrowser auftreten.
- URI, zu dem navigiert werden soll (`BrowserRedirectSuccess`), oder das bei erfolgreicher Anmeldung oder Einwilligung anzuzeigende HTML-Fragment (`HtmlMessageSuccess`).
- Aktion, die zum Starten des Systembrowsers ausgeführt werden soll. Sie können Ihre eigene Implementierung bereitstellen, indem Sie den `OpenBrowserAsync`-Delegaten festlegen. Die Klasse stellt außerdem eine Standardimplementierung für zwei Browser bereit: `OpenWithEdgeBrowserAsync` und `OpenWithChromeEdgeBrowserAsync` für Microsoft Edge bzw. [Microsoft Edge unter Chromium](https://www.windowscentral.com/faq-edge-chromium).

Zum Verwenden dieser Struktur können Sie etwas Ähnliches wie im folgenden Beispiel schreiben:

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Andere optionale Parameter

Informationen zu allen anderen optionalen Parametern für `AcquireTokenInteractive` finden Sie unter [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

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

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
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

### <a name="in-msal-for-ios-and-macos"></a>In MSAL für iOS und macOS

Objective-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

Swift:

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Im MSAL Node rufen Sie Token über den Autorisierungscodefluss mit einem Prüfschlüssel für den Codeaustausch (Proof Key for Code Exchange, PKCE) ab. Der Prozess umfasst zwei Schritte: Zuerst erhält die Anwendung eine URL, die verwendet werden kann, um einen Autorisierungscode zu generieren. Diese URL kann in einem Browser Ihrer Wahl geöffnet werden, in dem der Benutzer seine Anmeldeinformationen eingeben kann und mit einem Autorisierungscode an den (während der App-Registrierung registrierten) `redirectUri` zurückgeleitet wird. Zweitens übergibt die Anwendung den empfangenen Autorisierungscode an die `acquireTokenByCode()`-Methode, die ihn gegen ein Zugriffstoken austauscht.

```javascript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

const authCodeUrlParameters = {
    scopes: ["User.Read"],
    redirectUri: "your_redirect_uri",
    codeChallenge: challenge, // PKCE Code Challenge
    codeChallengeMethod: "S256" // PKCE Code Challenge Method 
};

// get url to sign user in and consent to scopes needed for application
pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
    console.log(response);

    const tokenRequest = {
        code: response["authorization_code"],
        codeVerifier: verifier // PKCE Code Verifier 
        redirectUri: "your_redirect_uri",
        scopes: ["User.Read"],
    };

    // acquire a token by exchanging the code
    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("\nResponse: \n:", response);
    }).catch((error) => {
        console.log(error);
    });
}).catch((error) => console.log(JSON.stringify(error)));
```

# <a name="python"></a>[Python](#tab/python)

Die MSAL für Python ab Version 1.7 bietet eine interaktive Methode zum Abrufen von Token.

```python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_interactive(  # It automatically provides PKCE protection
         scopes=config["scope"])
```

---
### <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Aufrufen einer Web-API von der Desktop-App](scenario-desktop-call-api.md).
