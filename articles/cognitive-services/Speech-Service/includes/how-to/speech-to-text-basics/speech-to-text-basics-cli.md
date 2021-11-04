---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: eur
ms.openlocfilehash: ae12aaf1fe15554246c9b05f9e4839305e1419b7
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501893"
---
Die Funktion zum Erkennen und Transkribieren von menschlicher Sprache (Spracherkennung) ist eines der zentralen Features des Speech-Diensts. In diesem Schnellstart erfahren Sie, wie Sie das Speech-CLI in Ihren Apps und Produkten verwenden, um hochwertige Spracherkennungen durchzuführen.

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="speech-to-text-from-microphone"></a>Spracherkennung über ein Mikrofon

Stecken Sie Ihr PC-Mikrofon ein, schalten Sie es ein, und deaktivieren Sie alle Apps, die möglicherweise ebenfalls das Mikrofon verwenden. Einige Computer verfügen über ein eingebautes Mikrofon, während für andere die Konfiguration eines Bluetooth-Geräts erforderlich ist.

Sie sind jetzt bereit, die Speech-Befehlszeilenschnittstelle auszuführen, um über Ihr Mikrofon eingegebene Sprache zu erkennen. Wechseln Sie an der Befehlszeile in das Verzeichnis, das die Binärdatei der Speech-Befehlszeilenschnittstelle enthält, und führen Sie den folgenden Befehl aus.

```bash
spx recognize --microphone
```

> [!NOTE]
> Die Speech-Befehlszeilenschnittstelle ist standardmäßig auf Englisch eingestellt. Sie können in der [Spracherkennungstabelle](../../../../language-support.md) eine andere Sprache auswählen.
> Fügen Sie beispielsweise `--source de-DE` hinzu, um deutsche Spracheingaben zu erkennen.

Sprechen Sie in das Mikrofon, und Sie sehen in Echtzeit die Transkription Ihrer Worte in Text. Die Speech-Befehlszeilenschnittstelle hält nach einem Zeitraum der Stille an, oder wenn Sie STRG+C drücken.

## <a name="speech-to-text-from-audio-file"></a>Spracherkennung über eine Audiodatei

Die Speech-Befehlszeilenschnittstelle kann Sprache in vielen Datenformaten und natürlichen Sprachen erkennen. Für dieses Beispiel können Sie eine beliebige WAV-Datei (16 kHz oder 8 kHz, 16-Bit und Mono-PCM) verwenden, die englische Sprache enthält. Oder wenn Sie ein schnelles Beispiel wünschen, laden Sie die Datei <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whattheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span></a> herunter, und kopieren Sie sie in dasselbe Verzeichnis wie die Binärdatei der Speech-Befehlszeilenschnittstelle.

Jetzt können Sie die Speech-Befehlszeilenschnittstelle ausführen, um die in der Audiodatei gefundene Sprache zu erkennen, indem Sie den folgenden Befehl ausführen.

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> Die Speech-Befehlszeilenschnittstelle ist standardmäßig auf Englisch eingestellt. Sie können in der [Spracherkennungstabelle](../../../../language-support.md) eine andere Sprache auswählen.
> Fügen Sie beispielsweise `--source de-DE` hinzu, um deutsche Spracheingaben zu erkennen.

Die Speech-Befehlszeilenschnittstelle zeigt eine Texttranskription der Sprache auf dem Bildschirm an.
