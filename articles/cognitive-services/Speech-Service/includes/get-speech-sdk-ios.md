---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: pafarley
ms.openlocfilehash: 5a761794e8f66f55e3ea9cbb2e6df282b675ea4f
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123540028"
---
:::row:::
    :::column span="3":::
        Für die Entwicklung für iOS sind zwei Speech SDKs verfügbar. Das Objective-C Speech SDK ist nativ als iOS-CocoaPod-Paket verfügbar. Alternativ kann das .NET Speech SDK mit Xamarin.iOS verwendet werden, da es .NET Standard 2.0 implementiert.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Ausführliche Informationen zur Verwendung des Objective-C Speech SDK mit Swift finden Sie unter <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Importieren von Objective-C in Swift </a>.

### <a name="system-requirements"></a>Systemanforderungen

- macOS Version 10.3 oder höher
- Zielversion: iOS 9.3 oder höher

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        Das iOS-CocoaPod-Paket steht zum Download zur Verfügung und kann mit der integrierten Entwicklungsumgebung (Integrated Development Environment, IDE) <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (oder höher) </a> verwendet werden. Laden Sie zunächst die <a href="https://aka.ms/csspeech/iosbinary" target="_blank">Binärdatei für CocoaPod </a> herunter. Extrahieren Sie den Pod in dem Verzeichnis, in dem Sie ihn verwenden möchten, erstellen Sie eine *Podfile*, und listen Sie den `pod` als `target` auf.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.iOS macht das vollständige iOS SDK für .NET-Entwickler verfügbar. Erstellen Sie vollständig native iOS-Apps mit C# oder F# in Visual Studio. Weitere Informationen finden Sie unter <a href="/xamarin/ios/" target="_blank">Xamarin.iOS </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp; ❤️ &nbsp;         <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>Zusätzliche Ressourcen

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">Quellcode zum Schnellstart für das iOS Speech SDK für Objective-C </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">Quellcode zum Schnellstart für das iOS Speech SDK für Swift </a>