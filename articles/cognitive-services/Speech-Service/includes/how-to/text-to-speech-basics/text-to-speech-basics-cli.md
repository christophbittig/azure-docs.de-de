---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: eur
ms.openlocfilehash: 0bc81935e597198ed1c65a732ae1feaee7aa6226
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510762"
---
In dieser Schnellstartanleitung werden gängige Entwurfsmuster für die Sprachsynthese per Speech SDK vermittelt. Hierzu werden zunächst eine grundlegende Konfiguration und eine einfache Synthese durchgeführt, gefolgt von komplexeren Beispielen für die Entwicklung benutzerdefinierter Anwendungen:

* Erhalten von Antworten als In-Memory-Datenstrom
* Anpassen der Abtast- und Bitrate der Ausgabe
* Übermitteln von Syntheseanforderungen mithilfe von SSML (Speech Synthesis Markup Language, Markupsprache für Sprachsynthese)
* Verwenden neuronaler Stimmen

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>Synthetisieren von Sprache über einen Lautsprecher

Sie sind jetzt bereit, die Speech-Befehlszeilenschnittstelle auszuführen, um aus Text Sprache zu synthetisieren. Wechseln Sie an der Befehlszeile in das Verzeichnis, das die Binärdatei der Speech-Befehlszeilenschnittstelle enthält. Führen Sie dann den folgenden Befehl aus:

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

Die Speech-Befehlszeilenschnittstelle gibt natürlichsprachliches Englisch über den Lautsprecher des Computers aus.

## <a name="synthesize-speech-to-a-file"></a>Synthetisieren von Sprache in eine Datei

Führen Sie den folgenden Befehl aus, um die Ausgabe von Ihrem Lautsprecher in eine Datei vom Typ `.wav` zu ändern.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Die Speech-Befehlszeilenschnittstelle gibt natürlichsprachliches Englisch in die Audiodatei `greetings.wav` aus.
Unter Windows können Sie die Audiodatei wiedergeben, indem Sie `start greetings.wav` eingeben.
