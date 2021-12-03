---
title: Codieren von Videos mit dem Standard-Encoder in Media Services
description: In diesem Thema wird gezeigt, wie Sie in Media Services mit dem Standard-Encoder ein Eingabevideo mithilfe einer automatisch generierten Reihe von Bitraten-/Auflösungspaaren auf Basis der eingegebenen Auflösung und Bitrate codieren können.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 058d80b85a9a669612c64de6f0f705f6d605e419
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065017"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Codieren mit einer automatisch generierten Reihe von Bitraten-/Auflösungspaaren

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Übersicht

In diesem Artikel wird gezeigt, wie Sie in Media Services mit dem Standard-Encoder ein Eingabevideo in eine automatisch generierte Reihe von Bitraten-/Auflösungspaaren auf Basis der eingegebenen Auflösung und Bitrate codieren können. Diese Voreinstellung des Encoders übersteigt in keinem Fall die eingegebene Auflösung und Bitrate. Ist die Eingabe beispielsweise 720p bei 3 MBit/s, bleibt die Ausgabe bestenfalls bei 720p, und es beginnt mit niedrigeren Raten als 3 MBit/s.

### <a name="encoding-for-streaming"></a>Codierung für Streaming

Wenn Sie die Voreinstellung **AdaptiveStreaming** oder **H265AdaptiveStreaming** beim **Transformieren** verwenden, erhalten Sie eine Ausgabe, die für die Bereitstellung über Streamingprotokolle wie HLS und DASH geeignet ist. Bei Verwendung einer dieser zwei Voreinstellungen legt der Dienst intelligent fest, wie viele Videoebenen mit welcher Bitrate und Auflösung erzeugt werden sollen. Die Ausgabeinhalte enthalten MP4-Dateien, bei denen AAC-codierte Audiodaten und entweder H.264-codierte Videos (im Fall der Voreinstellung „AdaptiveStreaming“) oder H.265/HEVC (im Fall der Voreinstellung „H265AdaptiveStreaming“) verwendet werden. Die MP4-Ausgabedateien sind nicht im Zeilensprungverfahren codiert.

Ein Beispiel für die Verwendung dieser Voreinstellung finden Sie unter [Streamen einer Datei](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Output

Dieser Abschnitt zeigt drei Beispiele von Ausgabevideoebenen, die vom Media Services-Encoder als Ergebnis der Codierung mit der Voreinstellung **AdaptiveStreaming** (H.264) oder der Voreinstellung **H265AdaptiveStreaming** (HEVC) erzeugt wurden. In allen Fällen enthält die Ausgabe eine reine MP4-Audiodatei, die mit 128 KBit/s codiertes Audio in Stereo enthält.

### <a name="example-1"></a>Beispiel 1
Die Quelle mit der Höhe „1080“ und der Bildrate „29,970“ produziert 6 Videoebenen:

|Ebene|Höhe|Breite|Bitrate (KBit/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Beispiel 2
Die Quelle mit der Höhe „720“ und der Bildrate „23,970“ produziert 5 Videoebenen:

|Ebene|Höhe|Breite|Bitrate (KBit/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Beispiel 3
Die Quelle mit der Höhe „360“ und der Bildrate „29,970“ produziert 3 Videoebenen:

|Ebene|Höhe|Breite|Bitrate (KBit/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|


## <a name="content-aware-encoding-comparison"></a>Vergleich mit den inhaltsbezogenen Codierungsoptionen

Die [inhaltsbezogenen Codierungsvoreinstellungen](./encode-content-aware-concept.md) stellen gegenüber den Adaptive Streaming-Voreinstellungen die bessere Lösung dar, indem sie die Quellinhalte analysieren, bevor sie aus der Leiter der verfügbaren Optionen den richtigen Satz von Ausgabebitraten und Auflösungen auswählen.
Es empfiehlt sich, zuerst die [inhaltsbezogenen Codierungsvoreinstellungen](./encode-content-aware-concept.md) auszuprobieren, bevor die Wahl auf die eher statische und feste Optionsleiter der Voreinstellungen für Streamiing mit adaptiver Bitrate fällt.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Streamen einer Datei](stream-files-dotnet-quickstart.md)
> [Verwenden der inhaltsbezogenen Codierungsvoreinstellungen](./encode-content-aware-concept.md)
> [Verwenden der inhaltsbezogenen Codierung](./encode-content-aware-how-to.md)
