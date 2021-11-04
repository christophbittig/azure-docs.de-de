---
title: 'Batchvorgänge für die Speech-Befehlszeilenschnittstelle: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie mithilfe der Speech-Befehlszeilenschnittstelle Batchvorgänge für die Spracherkennung (Sprache-in-Text) und für die Sprachsynthese (Text-zu-Sprache) ausführen.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: eur
ms.openlocfilehash: 1049720604ba4ba3a0be1874e0364e2e92c321e9
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505211"
---
# <a name="speech-cli-batch-operations"></a>Batchvorgänge für die Speech-Befehlszeilenschnittstelle

Bei der Verwendung der Azure Speech-Dienste werden nicht selten Batchvorgänge verwendet. In diesem Artikel erfahren Sie, wie Sie mithilfe der Speech-Befehlszeilenschnittstelle Batchvorgänge für die Spracherkennung (Sprache-in-Text) und für die Sprachsynthese (Text-zu-Sprache) ausführen. Insbesondere erfahren Sie, wie Sie:

* Ausführen der Spracherkennung für ein Verzeichnis mit Audiodateien
* Ausführen einer Batch-Sprachsynthese mittels Durchlaufen einer Datei vom Typ `.tsv`

## <a name="batch-speech-to-text-speech-recognition"></a>Batch-Spracherkennung (Sprache-in-Text)

Der Speech-Dienst wird häufig zur Erkennung von Sprache aus Audiodateien verwendet. In diesem Beispiel erfahren Sie, wie Sie mithilfe der Speech-Befehlszeilenschnittstelle ein Verzeichnis durchlaufen, um die Erkennungsausgabe für jede Datei vom Typ `.wav` zu erfassen. Mit dem Flag `--files` wird auf das Verzeichnis verwiesen, in dem Audiodateien gespeichert sind, und mithilfe des Platzhalters `*.wav` wird die Speech-Befehlszeilenschnittstelle angewiesen, die Erkennung für jede Datei mit der Erweiterung `.wav` auszuführen. Die Ausgabe für die jeweilige Erkennungsdatei wird als durch Tabstopps getrennter Wert in `speech_output.tsv` geschrieben.

> [!NOTE]
> Das `--threads`-Argument kann außerdem im nächsten Abschnitt für `spx synthesize`-Befehle verwendet werden, und die verfügbaren Threads hängen von der CPU und dem aktuellen Prozentsatz ihrer Auslastung ab.

```console
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

Es folgt ein Strukturbeispiel für die Ausgabedatei.

```output
audio.input.id  recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-speech-synthesis"></a>Batch-Sprachsynthese (Text-zu-Sprache)

Die einfachste Möglichkeit zum Ausführen einer Batch-Sprachsynthese besteht darin, eine neue Datei vom Typ `.tsv` (durch Tabstopps getrennte Werte) zu erstellen und den Befehl `--foreach` an der Speech-Befehlszeilenschnittstelle zu verwenden. Eine Datei vom Typ `.tsv` kann mit einem beliebigen Text-Editor erstellt werden. In diesem Beispiel heißt die Datei `text_synthesis.tsv`:

>[!IMPORTANT]
> Achten Sie beim Kopieren des Inhalts dieser Textdatei darauf, dass sich Ihrer Datei zwischen dem Dateispeicherort und dem Text keine Leerzeichen befinden, sondern **Tabstopps**. Manchmal werden Tabstopps beim Kopieren des Inhalts aus diesem Beispiel in Leerzeichen konvertiert. Ist dies der Fall, kann der Befehl `spx` nicht erfolgreich ausgeführt werden.

```Input
audio.output    text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Führen Sie als nächstes einen Befehl aus, um auf `text_synthesis.tsv` zu verweisen, Synthese für jedes `text`-Feld auszuführen und das Ergebnis unter dem entsprechenden `audio.output`-Pfad als `.wav`-Datei zu schreiben.

```console
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Dieser Befehl ist das Äquivalent zur Ausführung von `spx synthesize --text "Sample text to synthesize" --audio output C:\batch_wav_output\wav_1.wav` **für jeden** Datensatz in der `.tsv`-Datei.

Ein paar Dinge sind zu beachten:

* Die Spaltenüberschriften `audio.output` und `text` entsprechen dem Befehlszeilenargument `--audio output` bzw. `--text`. Mehrteilige Befehlszeilenargumente wie `--audio output` müssen in der Datei ohne Leerzeichen, ohne führende Bindestriche und ohne Punkte zur Trennung von Zeichenfolgen formatiert werden (Beispiel: `audio.output`). Alle anderen vorhandenen Befehlszeilenargumente können der Datei unter Verwendung dieses Musters als zusätzliche Spalten hinzugefügt werden.
* Wenn die Datei auf diese Weise formatiert wird, müssen keine zusätzlichen Argumente an `--foreach` übergeben werden.
* Achten Sie darauf, jeden Wert in der `.tsv`-Datei mit einem **Tabstopp** zu trennen.

Das folgende Beispiel zeigt dagegen eine Datei vom Typ `.tsv`, deren Spaltenüberschriften **nicht** den Befehlszeilenargumenten entsprechen:

```Input
wav_path    str_text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Sie können diese Feldnamen mit den richtigen Argumenten überschreiben, sofern Sie dazu im `--foreach`-Aufruf die folgende Syntax verwenden. Dies ist derselbe Aufruf wie oben.

```console
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über die Speech-Befehlszeilenschnittstelle](./spx-overview.md)
* [Schnellstartanleitung zur Speech-Befehlszeilenschnittstelle](./spx-basics.md)
