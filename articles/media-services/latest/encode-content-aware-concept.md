---
title: Die Voreinstellung für die inhaltsbezogene Codierung
description: In diesem Artikel wird die inhaltsbezogene Codierung in Microsoft Azure Media Services v3 erläutert.
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 09/16/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a34e2d16edb4a8ec9ba40a4426fcbbd9b2127b16
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590402"
---
# <a name="content-aware-encoding"></a>Inhaltsbezogene Codierung

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview-of-the-content-aware-encoding-preset"></a>Übersicht zur Voreinstellung für die inhaltsbezogene Codierung

Um Inhalte für die Übermittlung mithilfe von [Adaptive Bitrate Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) vorzubereiten, muss Video mit mehreren Bitraten (hoch bis niedrig) und in mehreren Auflösungen codiert werden. Diese Technik ermöglicht es den heutigen modernen Videoplayern unter Apple iOS, Android, Windows und Mac, Streamingprotokolle zu verwenden, die Inhalte ohne Pufferung reibungslos streamen. Diese in Anzeigegröße (Auflösung) und Qualität (Bitrate) verschiedenen Darstellungen ermöglichen es dem Player, die beste Version des Videos auszuwählen, die bei den aktuellen Netzwerkbedingungen unterstützt werden kann. Das Netzwerk kann zwischen LTE, 4G, 5G, öffentlichem WLAN oder Heimnetzwerk große Unterschiede aufweisen.

Der Prozess der Codierung von Inhalten in mehrere Darstellungen erfordert die Generierung einer „Codierungsleiter“. Dies ist eine Tabelle mit Auflösungen und Bitraten, die den Encoder anweist, was generiert werden soll. Ein Beispiel für eine solche Leiter finden Sie in den [integrierten Codierungsvoreinstellungen](/rest/api/media/transforms/createorupdate#encodernamedpreset) von Media Services.

Unter idealen Bedingungen sollten Sie die Art des Inhalts kennen, den Sie codieren. Mithilfe dieser Informationen können Sie die Codierungsleiter so optimieren, dass sie zur Komplexität und dem Maß an Bewegung in Ihrem Quellvideo passt. Dies bedeutet, dass für jede Anzeigegröße (Auflösung) in der Leiter eine Bitrate enthalten sein sollte, ab der ein Qualitätsanstieg nicht mehr wahrnehmbar ist – der Encoder arbeitet mit diesem optimalen Wert für die Bitrate.

Die nächste Stufe der erreichbaren Optimierung besteht in der Auswahl der Auflösungen auf der Grundlage des Inhalts. Beispielsweise würde ein Video einer PowerPoint-Präsentation mit kleinem Text unscharf aussehen, wenn es unterhalb von 720 Pixellinien in der Höhe codiert wird. Außerdem können sich Bewegung und Komplexität Ihres Videos im zeitlichen Verlauf ändern, je nachdem, wie es aufgenommen und bearbeitet wurde.  Dies gibt Ihnen die Möglichkeit, die Codierungseinstellungen für jede Szene oder an jeder Einstellungsgrenze zu optimieren und anzupassen. Ein intelligenter Encoder kann damit beauftragt werden, die Codierungseinstellungen für jede Einstellung innerhalb eines Videos zu optimieren.

Azure Media Services enthält eine Voreinstellung [Adaptive Streaming](encode-autogen-bitrate-ladder.md), die eine teilweise Lösung für das Problem der wechselnden Bitrate und Auflösung der Quellvideos darstellt. Diese Voreinstellung analysiert jedoch nicht die Quellinhalte, um zu sehen, wie komplex sie sind oder wie viel Bewegung sie enthalten. 

Die Voreinstellung für inhaltsbezogene Codierung stellt gegenüber der eher statischen Codierungsvoreinstellung Adaptive Bitrate einen Fortschritt dar. Hierzu wird Logik integriert, die es dem Encoder ermöglicht, den optimalen Bitratenwert für eine bestimmte Auflösung zu bestimmen, ohne aufwendige Berechnungsanalyse zu erfordern. Diese Voreinstellung gibt ausgehend von der Quelldatei eine einmalige „Leiter“ von MP4-Dateien mit Orientierung an den GOPs aus. Bei einem Quellvideo führt die Voreinstellung eine erste schnelle Analyse der Eingabeinhalte durch und verwendet die Ergebnisse, um die optimale Anzahl von Stufen, Bitraten und Auflösungen zu bestimmen, die erforderlich sind, um das Streaming mit adaptiver Bitrate mit der höchsten Qualität zu ermöglichen. Diese Voreinstellung eignet sich insbesondere für Videos mit geringer bis mittlerer Komplexität, bei denen die Ausgabedateien zwar eine niedrigere Bitrate als bei der weniger flexiblen Adaptive Streaming-Voreinstellung aufweisen, Zuschauern aber immer noch den Eindruck guter Qualität vermitteln. Der Ausgabeordner enthält mehrere MP4-Dateien mit Video und Audio, die für das Streaming bereit sind.

## <a name="configure-output-settings"></a>Konfigurieren von Ausgabeeinstellungen

Darüber hinaus können Entwickler auch die Bandbreite der Ausgaben steuern, die von der inhaltsbezogenen Codierungsvoreinstellung verwendet werden, wenn sie in der Streamingleiter mit adaptiver Bitrate die optimalen Einstellungen für die Codierung festlegen.

Mithilfe der **PresetConfigurations**-Klasse können Entwickler eine Reihe von Einschränkungen und Optionen an die inhaltsbezogene Codierungsvoreinstellung übergeben, um die vom Encoder generierten resultierenden Dateien zu steuern. Die Eigenschaften sind besonders nützlich in Situationen, in denen Sie die gesamte Codierung auf eine bestimmte maximale Auflösung beschränken möchten, um den vermittelten Eindruck oder die Kosten Ihrer Codierungsaufträge zu steuern.  Es ist außerdem nützlich, die maximale und minimale Bitrate steuern zu können, die Ihre Zielgruppe in einem Mobilfunknetz oder in einer globalen Region mit eingeschränkter Bandbreite unterstützen kann.

## <a name="supported-codecs"></a>Unterstützte Codecs

Die inhaltsbezogene Codierungsvoreinstellung steht für die Verwendung mit den folgenden Codecs zur Verfügung:
-  H.264
-  HEVC (H.265)

## <a name="how-to-use"></a>Vorgehensweise

Details zur Verwendung der Voreinstellung in Ihrem Code und Links zu vollständigen Beispielen finden Sie in der [Anleitung zur inhaltsbezogenen Codierung](./encode-content-aware-How-to.md).

## <a name="technical-details-on-content-aware-preset"></a>Technische Details zur inhaltsbezogenen Voreinstellung

Sehen wir uns nun etwas mehr im Detail an, wie die inhaltsbezogene Codierungsvoreinstellung funktioniert.  Die folgenden Beispieldiagramme enthalten eine Gegenüberstellung mit Qualitätsmetriken wie [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) und [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). Die Quelle wurde durch das Verketten kurzer Clips mit hochkomplexen Einstellungen aus Filmen und Fernsehsendungen erstellt, die den Encoder auf eine harte Probe stellen sollten. Per Definition liefert diese Voreinstellung Ergebnisse, die je nach Inhalt variieren. Das bedeutet auch, dass es bei einigen Inhalten möglicherweise keine signifikante Reduzierung der Bitrate oder Verbesserung der Qualität gibt.

![Rate-Distortion-Kurve (RD) unter Verwendung von PSNR](media/encode-content-aware-concept/msrv1.png)

**Abbildung 1: Rate-Distortion-Kurve (RD) unter Verwendung der Metrik PSNR für Quelle mit hoher Komplexität**

![Rate-Distortion-Kurve (RD) unter Verwendung von VMAF](media/encode-content-aware-concept/msrv2.png)

**Abbildung 2: Rate-Distortion-Kurve (RD) unter Verwendung der Metrik VMAF für Quelle mit hoher Komplexität**

Im Anschluss finden Sie die Ergebnisse für eine weitere Kategorie von Quellinhalten, bei denen der Encoder feststellen konnte, dass die Eingabe von schlechter Qualität war (viele Kompressionsartefakte aufgrund der niedrigen Bitrate). Mit der inhaltsbezogenen Voreinstellung hat sich der Encoder dazu entschieden, nur eine einzelne Ausgabestufe zu erzeugen – mit einer ausreichend niedrigen Bitrate, sodass die meisten Clients den Stream ohne Verzögerung wiedergeben können.

![RD-Kurve mit PSNR](media/encode-content-aware-concept/msrv3.png)

**Abbildung 3: RD-Kurve mit PSNR für Eingabe mit niedriger Qualität (bei 1080p)**

![RD-Kurve mit VMAF](media/encode-content-aware-concept/msrv4.png)

**Abbildung 4: RD-Kurve mit VMAF für Eingabe mit niedriger Qualität (bei 1080p)**

  
## <a name="next-steps"></a>Nächste Schritte
* [Verwenden der Voreinstellungen für die inhaltsbezogene Codierung](encode-content-aware-how-to.md)
* [Tutorial: Hochladen, Codieren und Streamen von Videos mit Media Services v3](stream-files-tutorial-with-api.md)