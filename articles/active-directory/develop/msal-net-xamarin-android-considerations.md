---
title: Xamarin Android-Codekonfiguration und Problembehandlung (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über Überlegungen zur Verwendung von Xamarin Android mit der Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 04194dc73b7c91bd1e59140a032232246b72420e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052701"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Konfigurationsanforderungen und Tipps zur Problembehandlung für Xamarin Android mit MSAL.NET

Es gibt mehrere Konfigurationsänderungen, die Sie in Ihrem Code vornehmen müssen, wenn Sie Xamarin Android mit der Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET) verwenden. In den folgenden Abschnitten werden die erforderlichen Änderungen beschrieben, gefolgt vom Abschnitt [Problembehandlung](#troubleshooting), der Ihnen helfen soll, einige der häufigsten Probleme zu vermeiden.

## <a name="set-the-parent-activity"></a>Festlegen der übergeordneten Aktivität

Legen Sie in Xamarin Android die übergeordnete Aktivität fest, damit das Token nach der Interaktion Folgendes zurückgibt:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

In MSAL.NET 4.2 und höher können Sie diese Funktionalität auch auf der Ebene [PublicClientApplication][PublicClientApplication] festlegen. Verwenden Sie zu diesem Zweck einen Rückruf:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Wenn Sie [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin) verwenden, sollte Ihr Builder-Code [`PublicClientApplication`][PublicClientApplication]diesem Codeausschnitt ähneln:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Sicherstellen, dass die Steuerung wieder an MSAL übergeben wird

Wenn der interaktive Teil des Authentifizierungsablaufs endet, wird die Steuerung an MSAL zurückgegeben, indem die folgende Methode außer Kraft gesetzt wird: [`Activity`][Activity].[`OnActivityResult()`][OnActivityResult] -Methode.

Rufen Sie in Ihrer Außerkraftsetzung die MSAL.NET-Methode `AuthenticationContinuationHelper`.`SetAuthenticationContinuationEventArgs()` auf, damit die Steuerung am Ende des interaktiven Teils des Authentifizierungsablaufs zurückgegeben an MSAL wird.

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest-for-system-webview-support"></a>Aktualisieren des Android-Manifests für die System WebView-Unterstützung 

Damit System WebView unterstützt wird, sollte die Datei *AndroidManifest.xml* die folgenden Werte enthalten:

```xml
<activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="msal{Client Id}" android:host="auth" />
  </intent-filter>
</activity>
```

Der Wert `android:scheme` wird aus dem Umleitungs-URI erstellt, der im Anwendungsportal konfiguriert wurde. Wenn der Umleitungs-URI beispielsweise `msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842://auth` lautet, sieht der Eintrag `android:scheme` im Manifest folgendermaßen aus:

```xml
<data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
```

Erstellen Sie alternativ [die Aktivität in Code](/xamarin/android/platform/android-manifest#the-basics), statt *AndroidManifest.xml* manuell zu bearbeiten. Um die Aktivität im Code zu erstellen, erstellen Sie zuerst eine Klasse, die das `Activity`-Attribut und das `IntentFilter`-Attribut enthält.

Hier sehen Sie ein Beispiel für eine Klasse, die die Werte der XML-Datei darstellt:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="use-system-webview-in-brokered-authentication"></a>Verwenden von System WebView bei der Brokerauthentifizierung

Wenn Sie System WebView für den Fall, dass Sie für Ihre Anwendung die Brokerauthentifizierung konfiguriert haben und auf dem Gerät kein Broker installiert ist, als Fallback für die interaktive Authentifizierung verwenden möchten, aktivieren Sie MSAL zum Erfassen der Authentifizierungsantwort mithilfe des Umleitungs-URIs des Brokers. Wenn erkannt wird, dass der Broker nicht verfügbar ist, versucht MSAL die Authentifizierung mithilfe der Standardversion von System WebView auf dem Gerät. Bei der Authentifizierung mit dieser Standardversion tritt ein Fehler auf, da der Umleitungs-URI für die Verwendung eines Brokers konfiguriert ist und System WebView nicht weiß, wie er zur Rückkehr zu MSAL verwendet wird. Erstellen Sie zum Lösen dieses Problems einen _Absichtsfilter_, indem Sie den zuvor konfigurierten Umleitungs-URI des Brokers verwenden. Fügen Sie den Absichtsfilter hinzu, indem Sie das Anwendungsmanifest wie in diesem Beispiel ändern:

```xml
<!--Intent filter to capture System WebView or Authenticator calling back to our app after sign-in-->
<activity
      android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <data android:scheme="msauth"
              android:host="Enter_the_Package_Name"
              android:path="/Enter_the_Signature_Hash" />
    </intent-filter>
</activity>
```

Ersetzen Sie den Wert `android:host=` durch den Paketnamen, den Sie im Azure-Portal registriert haben. Ersetzen Sie den Wert `android:path=` durch den Schlüsselhash, den Sie im Azure-Portal registriert haben. Der Signaturhash darf **nicht** URL-codiert sein. Der Signaturhash muss mit einem führenden Schrägstrich (`/`) beginnen.

### <a name="xamarinforms-43x-manifest"></a>Xamarin.Forms 4.3.x-Manifest

Xamarin.Forms 4.3.x generiert Code, der das `package`-Attribut in *AndroidManifest.xml* auf `com.companyname.{appName}` festlegt. Wenn Sie `DataScheme` als `msal{client_id}` verwenden, kann es sinnvoll sein, den Wert zu ändern, damit er mit dem Namen des `MainActivity.cs`-Namespace übereinstimmt.

## <a name="android-11-support"></a>Unterstützung für Android 11

Wenn Sie den Systembrowser und die Brokerauthentifizierung unter Android 11 verwenden möchten, müssen Sie diese Pakete zunächst deklarieren, damit sie für die App sichtbar sind. Apps für Android 10 (API 29) und früher können das Betriebssystem abfragen, um eine Liste der Pakete abzurufen, die zu jedem Zeitpunkt auf dem Gerät verfügbar sind. Zur Unterstützung von Datenschutz und Sicherheit reduziert Android 11 die Paketsichtbarkeit, sodass nur eine Standardliste mit Betriebssystempaketen und den in der *AndroidManifest.xml*-Datei der App angegebenen Paketen angezeigt wird. 

Wenn Sie die Authentifizierung für die App sowohl über den Systembrowser als auch über den Broker ermöglichen möchten, fügen Sie der Datei *AndroidManifest.xml* den folgenden Abschnitt hinzu:

```xml
<!-- Required for API Level 30 to make sure the app can detect browsers and other apps where communication is needed.-->
<!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
<queries>
  <package android:name="com.azure.authenticator" />
  <package android:name="{Package Name}" />
  <package android:name="com.microsoft.windowsintune.companyportal" />
  <!-- Required for API Level 30 to make sure the app detect browsers
      (that don't support custom tabs) -->
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="https" />
  </intent>
  <!-- Required for API Level 30 to make sure the app can detect browsers that support custom tabs -->
  <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
  <intent>
    <action android:name="android.support.customtabs.action.CustomTabsService" />
  </intent>
</queries>
``` 

Ersetzen Sie `{Package Name}` durch den Namen des Anwendungspakets. 

Das aktualisierte Manifest, das nun die Unterstützung für den Systembrowser und die Brokerauthentifizierung umfasst, sollte in etwa wie in diesem Beispiel aussehen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.XamarinDev">
    <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="30" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <application android:theme="@android:style/Theme.NoTitleBar">
        <activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msauth" android:host="com.companyname.XamarinDev" android:path="/Fc4l/5I4mMvLnF+l+XopDuQ2gEM=" />
            </intent-filter>
        </activity>
    </application>
    <!-- Required for API Level 30 to make sure we can detect browsers and other apps we want to
     be able to talk to.-->
    <!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
    <queries>
        <package android:name="com.azure.authenticator" />
        <package android:name="com.companyname.xamarindev" />
        <package android:name="com.microsoft.windowsintune.companyportal" />
        <!-- Required for API Level 30 to make sure we can detect browsers
        (that don't support custom tabs) -->
        <intent>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="https" />
        </intent>
        <!-- Required for API Level 30 to make sure we can detect browsers that support custom tabs -->
        <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
        <intent>
            <action android:name="android.support.customtabs.action.CustomTabsService" />
        </intent>
    </queries>
</manifest>
```

## <a name="use-the-embedded-web-view-optional"></a>Verwenden der eingebetteten Webansicht (optional)

Standardmäßig verwendet MSAL.NET den Systemwebbrowser. Dieser Browser ermöglicht Ihnen, bei Verwendung von Webanwendungen und anderen Apps Einmaliges Anmelden (SSO) zu nutzen. In seltenen Fällen kann es sinnvoll sein, dass Ihr System eine eingebettete Webansicht verwendet.

Dieses Codebeispiel zeigt das Einrichten einer eingebetteten Webansicht:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Weitere Informationen finden Sie unter [Verwenden von Webbrowsern für MSAL.NET](msal-net-web-browsers.md) und [Überlegungen zu Systembrowsern für Xamarin Android](msal-net-system-browser-android-considerations.md).

## <a name="troubleshooting"></a>Problembehandlung

### <a name="general-tips"></a>Allgemeine Tipps

- Aktualisieren Sie das vorhandene MSAL.NET-NuGet-Paket auf die [neueste Version von MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/).
- Überprüfen Sie, ob Xamarin.Forms auf der neuesten Version basiert.
- Überprüfen Sie, ob Xamarin.Android.Support.v4 auf der neuesten Version basiert.
- Stellen Sie sicher, dass für alle Xamarin.Android.Support-Pakete die neueste Version festgelegt wird.
- Bereinigen Sie die Anwendung, oder erstellen Sie sie neu.
- Versuchen Sie in Visual Studio, die maximale Anzahl von parallelen Projektbuilds auf **1** festzulegen. Wählen Sie zu diesem Zweck **Optionen** > **Projekte und Projektmappen** > **Erstellen und Ausführen** > **Maximale Anzahl paralleler Projektbuilds** aus.
- Wenn Sie Builds an der Befehlszeile erstellen und in Ihrem Befehl `/m` verwenden, versuchen Sie, dieses Element aus dem Befehl zu entfernen.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Error: Der Name „AuthenticationContinuationHelper“ ist im aktuellen Kontext nicht vorhanden.

Wenn ein Fehler darauf hinweist, dass `AuthenticationContinuationHelper` im aktuellen Kontext nicht vorhanden ist, hat Visual Studio die Datei *Android.csproj\** möglicherweise falsch aktualisiert. Manchmal enthält der Dateipfad im Element `<HintPath>` fälschlicherweise `netstandard13` statt `monoandroid90`.

Dieses Beispiel enthält einen ordnungsgemäßen Dateipfad:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in dem Beispiel einer [mobilen Xamarin-Anwendung, die Microsoft Identity Platform verwendet](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). In der folgenden Tabelle sind die relevanten Informationen aus der Infodatei zusammengefasst.

| Beispiel | Plattform | BESCHREIBUNG |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, UWP | Eine einfache Xamarin Forms-App, die die Verwendung von MSAL zeigt, um persönliche Microsoft-Konten und Azure AD über den Azure AD 2.0-Endpunkt zu authentifizieren. Die App zeigt außerdem den Zugriff auf Microsoft Graph und das resultierende Token. <br>![Diagramm des Authentifizierungsablaufs](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
