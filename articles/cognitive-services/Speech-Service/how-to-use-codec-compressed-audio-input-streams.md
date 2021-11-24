---
title: Streamen von per Codec komprimierten Audiodaten mit dem Speech SDK – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie mit dem Speech SDK komprimierte Audiodaten an den Speech-Dienst streamen. Verfügbar für C++, C# und Java für Linux, Java in Android und Objective-C in iOS.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-eight
ms.openlocfilehash: 1f04c8c525e094d5e0980c1683632d34f3372580
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551917"
---
# <a name="use-codec-compressed-audio-input"></a>Verwenden der per Codec komprimierten Audioeingabe

Das Speech SDK und die Speech CLI können komprimierte Audioformate mithilfe von GStreamer akzeptieren. Die Audiodaten werden von GStreamer dekomprimiert, bevor sie als unformatiertes PCM an den Speech-Dienst gesendet werden.

Plattform | Sprachen | Unterstützte GStreamer-Version
| :--- | ---: | :---:
Linux  | C++, C#, Java, Python, Go | [Unterstützte Linux-Distributionen und Zielarchitekturen](~/articles/cognitive-services/speech-service/speech-sdk.md) 
Windows (ohne universelle Windows-Plattform) | C++, C#, Java, Python | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/msvc/gstreamer-1.0-msvc-x86_64-1.18.3.msi) 
Android  | Java | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/android/1.18.3/) 

## <a name="installing-gstreamer-on-linux"></a>Installieren von GStreamer unter Linux

Weitere Informationen finden Sie in den [Linux-Installationshinweisen](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c).  

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```
## <a name="installing-gstreamer-on-windows"></a>Installieren von GStreamer unter Windows

Weitere Informationen finden Sie in den [Windows-Installationshinweisen](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). 

* Einen Ordner c:\gstreamer erstellen
* Den [Installer](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/msvc/gstreamer-1.0-msvc-x86_64-1.18.3.msi) herunterladen 
* Das Installationsprogramm nach c:\gstreamer kopieren
* Starten Sie PowerShell als Administrator.
* Führen Sie in PowerShell den folgenden Befehl aus:

```powershell
cd c:\gstreamer
msiexec /passive INSTALLLEVEL=1000 INSTALLDIR=C:\gstreamer /i gstreamer-1.0-msvc-x86_64-1.18.3.msi
```
* Die Systemvariablen GST_PLUGIN_PATH mit Wert C:\gstreamer\1.0\msvc_x86_64\lib\gstreamer-1.0 hinzufügen
* Die Systemvariablen GSTREAMER_ROOT_X86_64 mit Wert C:\gstreamer\1.0\msvc_x86_64 hinzufügen
* Der Pfadvariablen einen weiteren Eintrag als C:\gstreamer\1.0\msvc_x86_64\bin hinzufügen
* Neustart des Computers

## <a name="using-gstreamer-in-android"></a>Verwenden von GStreamer in Android
Auf der obigen Java-Registerkarte finden Sie Details zum Erstellen von libgstreamer_android.so 

Weitere Informationen finden Sie im Artikel zu [Android-Installationsanweisungen](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c). 

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>Erforderliche Speech SDK-Version für komprimierte Audioeingaben
* Das Speech SDK, Version 1.10.0 oder höher ist für RHEL 8 und CentOS 8 erforderlich.
* Speech SDK, Version 1.11.0 oder höher ist für Windows erforderlich.
* Speech SDK Version 1.16.0 oder höher für die neueste GStreamer-Instanz unter Windows und Android.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="gstreamer-required-to-handle-compressed-audio"></a>GStreamer ist zum Verarbeiten komprimierter Audiodaten erforderlich.

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/go/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Beispielcode für die Verwendung von per Codec komprimierter Audioeingabe

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/go/examples.md)]
::: zone-end

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Erkennen von Spracheingaben per Mikrofon](./get-started-speech-to-text.md)
