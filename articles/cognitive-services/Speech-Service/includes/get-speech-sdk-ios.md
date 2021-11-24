---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: eur
ms.openlocfilehash: 942bf206454047f54765d096a2ac97fcabb895ab
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252441"
---
:::row:::
    :::column span="3":::
        Für die Entwicklung für iOS sind die folgenden Speech-SDKs verfügbar. Das Objective-C/Swift Speech SDK ist nativ als iOS CocoaPod-Paket verfügbar. Alternativ kann das .NET Speech SDK auch mit den Anwendungsframeworks Xamarin.iOS und Unity verwendet werden.
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
        Xamarin.iOS macht das vollständige iOS SDK für .NET-Entwickler verfügbar. Erstellen Sie vollständig native iOS-Anwendungen mit C# in Visual Studio. Weitere Informationen finden Sie unter <a href="/xamarin/ios/" target="_blank">Xamarin.iOS </a>.
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
