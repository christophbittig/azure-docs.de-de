---
title: Abrufen eines Tokens zum Aufrufen einer Web-API mithilfe von Web Account Manager (Desktop-App) | Azure
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie, wie Sie mithilfe von Web Account Manager eine Desktop-App erstellen, die Web-APIs aufruft, um ein Token für die App abzurufen.
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
ms.openlocfilehash: b06c771d14d0257ac69db9d3a9a40d3eadeba3e7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131436258"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-using-wam"></a>Web-APIs aufrufende Desktop-App – Abrufen eines Tokens mithilfe von WAM

MSAL kann die Windows 10-Komponente Web Account Manager aufrufen, die im Betriebssystems enthalten ist. Diese Komponente fungiert als Authentifizierungsbroker, und Benutzer*innen Ihrer App profitieren von der Integration in Konten, die aus Windows bekannt sind (z. B. das Konto, mit dem Sie sich in Ihrer Windows-Sitzung angemeldet haben).

## <a name="availability"></a>Verfügbarkeit

MSAL 4.25 und höher unterstützt WAM für die UWP, .NET Classic, .NET Core 3.x und .NET 5.

Für .NET Classic und .NET Core 3.x wird die WAM-Funktionalität vollständig unterstützt. Sie müssen jedoch neben MSAL einen Verweis auf das [Microsoft.Identity.Client.Desktop](https://www.nuget.org/packages/Microsoft.Identity.Client.Desktop/)-Paket hinzufügen und anstelle von `WithBroker()` `.WithWindowsBroker()` aufrufen.

Wählen Sie für .NET 5 `net5.0-windows10.0.17763.0` (oder höher) und nicht nur `net5.0` als Ziel aus. Ihre App wird weiterhin unter älteren Versionen von Windows ausgeführt, wenn Sie `<SupportedOSPlatformVersion>7</SupportedOSPlatformVersion>` in der CSPROJ-Datei hinzufügen. MSAL verwendet einen Browser, wenn WAM nicht verfügbar ist.

## <a name="wam-value-proposition"></a>WAM-Wertversprechen

Die Verwendung eines Authentifizierungsbrokers wie WAM hat zahlreiche Vorteile.

- Erhöhte Sicherheit (Ihre App muss das leistungsstarke Aktualisierungstoken nicht verwalten)
- Bessere Unterstützung für Windows Hello, bedingten Zugriff und FIDO-Schlüssel
- Integration in die Ansicht „E-Mail und Konten“ unter Windows
- Bessere Funktionalität des einmaligen Anmeldens (Benutzer*innen müssen Kennwörter nicht erneut eingeben)
- Die meisten Fehlerbehebungen und Verbesserungen sind im Lieferumfang von Windows enthalten

## <a name="wam-limitations"></a>WAM-Einschränkungen

- B2C-Autoritäten werden nicht unterstützt.
- Web Account Manager ist unter Windows 10, Windows Server 2016 und Windows Server 2019 verfügbar. Unter Mac, Linux und früheren Windows-Versionen führt MSAL ein Fallback auf einen Browser durch.

## <a name="wam-calling-pattern"></a>WAM-Aufrufmuster

Sie können das folgende Muster verwenden, um WAM zu nutzen.

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

Rufen Sie `.WithBroker(true)` auf. Wenn kein Broker vorhanden ist (z. B. Win8.1, Mac oder Linux), führt MSAL ein Fallback auf einen Browser durch. Umleitungs-URI-Regeln gelten für den Browser.

## <a name="redirect-uri"></a>Umleitungs-URI

WAM-Umleitungs-URIs müssen nicht in MSAL, jedoch in der App-Registrierung konfiguriert werden.

### <a name="win32-net-framework--net-5"></a>Win32 (.NET Framework/.NET 5)

```
ms-appx-web://microsoft.aad.brokerplugin/{client_id}
```

### <a name="uwp"></a>UWP
```csharp
            // returns smth like S-1-15-2-2601115387-131721061-1180486061-1362788748-631273777-3164314714-2766189824
            string sid = WebAuthenticationBroker.GetCurrentApplicationCallbackUri().Host.ToUpper();

            // the redirect uri you need to register
            string redirectUri = $"ms-appx-web://microsoft.aad.brokerplugin/{sid}";
```

## <a name="token-cache-persistence"></a>Tokencachepersistenz

Es ist wichtig, den Tokencache von MSAL zu speichern, da MSAL interne WAM-Konto-IDs dort speichern muss. Ohne den Tokencache lässt die `GetAccounts`-API bei einem Neustart der App einige Konten aus. Beachten Sie, dass MSAL im Zusammenhang mit der UWP weiß, wo der Tokencache gespeichert werden soll.

## <a name="getaccounts"></a>GetAccounts

`GetAccounts` gibt Konten von Benutzer*innen zurück, die sich zuvor interaktiv bei der App angemeldet haben.

Darüber hinaus kann WAM die betriebssystemweiten Arbeits- und Schulkonten auflisten, die unter Windows konfiguriert sind (für Win32-Apps, aber nicht für UWP-Apps). Legen Sie `ListWindowsWorkAndSchoolAccounts` in `WindowsBrokerOptions` auf **true** fest, um dieses Feature zu aktivieren. Sie können es wie im Folgenden dargestellt aktivieren.

```csharp
.WithWindowsBrokerOptions(new WindowsBrokerOptions()
{
    // GetAccounts will return Work and School accounts from Windows
    ListWindowsWorkAndSchoolAccounts = true,

    // Legacy support for 1st party apps only
    MsaPassthrough = true
})
```

>[!NOTE]
> Microsoft-Konten (z. B. outlook.com usw.) werden aus Datenschutzgründen nicht unter Win32 oder auf der UWP aufgeführt.

Anwendungen können keine Konten aus Windows entfernen. 

## <a name="removeasync"></a>RemoveAsync

- Hiermit werden alle Kontoinformationen aus dem Tokencache von MSAL entfernt (einschließlich MSA-Kontoinformationen zu persönlichen Konten und anderen Kontoinformationen, die von MSAL in den Cache kopiert werden).
- Entfernt Nur-App-Konten (nicht betriebssystemweit).

>[!NOTE]
> Apps können keine Betriebssystemkonten entfernen. Nur Benutzer*innen können dies tun. Wenn ein Betriebssystemkonto an `RemoveAsync` übergeben wird und dann `GetAccounts` mit der aktivierten Option `ListWindowsWorkAndSchoolAccounts` aufgerufen wird, wird weiterhin das gleiche Betriebssystemkonto zurückgegeben.

## <a name="other-considerations"></a>Andere Aspekte

- Für die interaktiven WAM-Vorgänge muss der UI-Thread verwendet werden. MSAL löst eine aussagekräftige Ausnahme aus, wenn sie sich nicht im UI-Thread befinden. Dies gilt NICHT für Konsolen-Apps.
- `WithAccount` bietet eine beschleunigte Authentifizierung, wenn das MSAL-Konto ursprünglich über WAM abgerufen wurde oder WAM unter Windows ein Arbeits- und Schulkonto finden kann.
- WAM kann das Feld für den Benutzernamen nicht vorab mit einem Anmeldehinweis auffüllen, es sei denn, ein Arbeits- und Schulkonto mit demselben Benutzernamen ist unter Windows verfügbar.
- Wenn WAM keine beschleunigte Authentifizierung bieten kann, wird eine Kontoauswahl angezeigt. Benutzer*innen können neue Konten hinzufügen.

![„WAM-Kontoauswahl“](media/scenario-desktop-acquire-token-wam/wam-account-picker.png)

- Neue Konten werden von Windows automatisch gespeichert. Arbeits- und Schulkonten bieten die Option, dem Verzeichnis der Organisation beizutreten oder sich vollständig abzumelden. In diesem Fall wird das Konto nicht unter „E-Mail und Konten“ angezeigt. Microsoft-Konten werden Windows automatisch hinzugefügt. Apps können diese Konten nicht programmgesteuert auflisten (nur über die Kontoauswahl).

## <a name="troubleshooting"></a>Problembehandlung

Wenn eine App, die MSAL verwendet, als Prozess mit erhöhten Rechten ausgeführt wird, kann bei einigen dieser Aufrufe in WAM aufgrund unterschiedlicher Prozesssicherheitsstufen ein Fehler auftreten. Intern verwendet MSAL.NET native Windows-Methoden ([COM](/windows/win32/com/the-component-object-model)) für die Integration mit WAM. Ab Version 4.32.0 zeigt MSAL eine beschreibende Fehlermeldung an, wenn erkannt wird, dass der App-Prozess mit erhöhten Rechten erfolgt und WAM keine Konten zurückgegeben hat.

Eine Lösung besteht darin, die App nach Möglichkeit nicht mit erhöhten Rechten auszuführen. Eine weitere mögliche Problemumgehung besteht darin, die `WindowsNativeUtils.InitializeProcessSecurity`-Methode aufzurufen, wenn die App gestartet wird. Dadurch wird die Sicherheit der von WAM verwendeten Prozesse auf die gleichen Ebenen festgelegt. Ein Beispiel finden Sie in [dieser Beispiel-App](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/master/tests/devapps/WAM/NetCoreWinFormsWam/Program.cs#L18-L21). Beachten Sie jedoch, dass diese Problemumgehung aufgrund externer Faktoren wie dem zugrunde liegenden CLR-Verhalten nicht garantiert erfolgreich ist. In diesem Fall wird eine `MsalClientException` ausgelöst. Weitere Informationen finden Sie unter [Issue 2560](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/2560).

## <a name="sample"></a>Beispiel

[WPF-Beispiel, das WAM verwendet](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2)

[UWP-Beispiel, das WAM zusammen mit Xamarin verwendet](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/2-With-broker)

---
## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Aufrufen einer Web-API von der Desktop-App](scenario-desktop-call-api.md).
