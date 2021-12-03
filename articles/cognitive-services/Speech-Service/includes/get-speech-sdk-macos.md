---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: eur
ms.openlocfilehash: 642fa02cf8f40d7cc6a39a1951499cb7921ed100
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252274"
---
Bei der Entwicklung für macOS sind die folgenden Speech-SDKs verfügbar.

- Das Objective-C/Swift Speech SDK ist nativ als CocoaPod-Paket für Mac x64 und ARM-basierte Silikone verfügbar. Einzelheiten zur Verwendung des Objective-C Speech SDK mit Swift finden Sie unter <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Import von Objective-C in Swift </a>.
- Das .NET Speech SDK ist über das NuGet-Paket verfügbar und kann mit den Anwendungsframeworks **Xamarin.Mac** und **Unity** verwendet werden.
- Das Python Speech SDK ist als PyPI-Modul für die Python-Versionen 3.7 und höher verfügbar.
- Das Java Speech SDK ist über das Maven-Repository als JAR-Paket verfügbar.

### <a name="system-requirements"></a>Systemanforderungen

- Eine macOS-Version 10.14 oder höher

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        Das macOS-CocoaPod-Paket steht zum Download zur Verfügung und kann mit der integrierten Entwicklungsumgebung (Integrated Development Environment, IDE) <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (oder höher) </a> verwendet werden. Laden Sie zunächst die <a href="https://aka.ms/csspeech/macosbinary" target="_blank">Binärdatei für CocoaPod </a> herunter. Extrahieren Sie den Pod in dem Verzeichnis, in dem Sie ihn verwenden möchten, erstellen Sie eine *Podfile*, und listen Sie den `pod` als `target` auf.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :osx, 10.14
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.15.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac macht das vollständige macOS SDK für .NET-Entwickler verfügbar, mit dem Sie native Mac-Anwendungen mithilfe von C# erstellen können. Weitere Informationen finden Sie unter <a href="/xamarin/mac/" target="_blank">Xamarin.Mac </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>Zusätzliche Ressourcen

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">Quellcode zum Schnellstart für das macOS Speech SDK für Objective-C </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">Quellcode zum Schnellstart für das macOS Speech SDK für Swift </a>
