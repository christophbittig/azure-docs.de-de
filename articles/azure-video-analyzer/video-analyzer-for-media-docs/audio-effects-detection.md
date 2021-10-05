---
title: Erkennung von Audioeffekten
description: Die Erkennung von Audioeffekten ist eine der KI-Funktionen von Azure Video Analyzer for Media. Sie erkennt verschiedene akustische Ereignisse und klassifiziert sie in verschiedene Akustikkategorien (z. B. Schüsse, Schreie und Reaktionen einer Menschenmenge).
ms.topic: conceptual
ms.date: 05/12/2021
ms.author: juliako
ms.openlocfilehash: 689abf9c61c0b1a462cfafe02c461906626e516b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632536"
---
#  <a name="audio-effects-detection-preview"></a>Erkennung von Audioeffekten (Vorschau)

Die **Erkennung von Audioeffekten** ist eine der KI-Funktionen von Azure Video Analyzer for Media. Sie erkennt verschiedene akustische Ereignisse und klassifiziert sie in verschiedene Akustikkategorien (z. B. Schüsse, Schreie und Reaktionen einer Menschenmenge).
 
Die Erkennung von Audioeffekten kann in vielen Domänen verwendet werden. Zwei Beispiele:

* Die Verwendung der Erkennung von Audioeffekten fällt in den Bereich **Öffentliche Sicherheit und Justiz**. Die Erkennung von Audioeffekten kann Schüsse, Explosionen und Glasbruch erkennen und klassifizieren. Daher kann es in einem Smart-City-System oder in anderen öffentlichen Umgebungen mit Kameras und Mikrofonen implementiert werden. Sie bietet eine schnelle und genaue Erkennung von Gewaltakten. 
* Im Bereich **Medien und Unterhaltung** können Unternehmen mit großen Videoarchiven ihre Barrierefreiheitsszenarien leicht optimieren, indem sie ihre Videotranskription auch mit nicht sprachlichen Effekten versehen, um Schwerhörigen mehr Kontext zu geben.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/audio-effects-detection/audio-effects.jpg" alt-text="Bild zu Audioeffekten":::
<br/>*Beispiel für die Ausgabe der Erkennung von Audioeffekten von Video Analyzer for Media*

## <a name="supported-audio-categories"></a>Unterstützte Audiokategorien  

Die **Erkennung von Audioeffekten** kann acht verschiedene Kategorien erkennen und klassifizieren. In der nächsten Tabelle finden Sie die verschiedenen Kategorien, unterteilt in die verschiedenen VI-Voreinstellungen und in **Standard** und **Erweitert**. Weitere Informationen finden Sie unter [Azure Data Lake Storage – Preise](https://azure.microsoft.com/pricing/details/media-services/).

|Indizierungstyp |Standardindizierung| Erweiterte Indizierung|
|---|---|---|
|**Name der Voreinstellung** |**„Nur Audio“** <br/>**„Video + Audio“** |**„Erweitertes Audio“**<br/> **„Erweitertes Video + Audio“**|
|**Im Erkenntnisbereich angezeigt**|| V|
|Reaktion einer Menschenmenge |V| V|
| Stille| V| V|
| Schuss ||V |
| Glasbruch ||V|
| Alarm|| V |
| Sirene|| V |
| Gelächter|| V |
| Hundebellen|| V|

## <a name="result-formats"></a>Ergebnisformate

Die Audioeffekte werden im Erkenntnis-JSON-Code abgerufen, der die Kategorie-ID, den Typ, den Namen und die Menge von Instanzen pro Kategorie zusammen mit ihrem spezifischen Zeitrahmen und ihrer Zuverlässigkeitsbewertung enthält.

Der `name`-Parameter wird in der Sprache angezeigt, in der der JSON-Code indiziert wurde, während der Typ immer gleich bleibt.

```json
audioEffects: [{
        id: 0,
        type: "Gunshot",
        name: "Gunshot",
        instances: [{
                confidence: 0.649,
                adjustedStart: "0:00:13.9",
                adjustedEnd: "0:00:14.7",
                start: "0:00:13.9",
                end: "0:00:14.7"
            }, {
                confidence: 0.7706,
                adjustedStart: "0:01:54.3",
                adjustedEnd: "0:01:55",
                start: "0:01:54.3",
                end: "0:01:55"
            }
        ]
    }, {
        id: 1,
        type: "CrowdReactions",
        name: "Crowd Reactions",
        instances: [{
                confidence: 0.6816,
                adjustedStart: "0:00:47.9",
                adjustedEnd: "0:00:52.5",
                start: "0:00:47.9",
                end: "0:00:52.5"
            },
            {
                confidence: 0.7314,
                adjustedStart: "0:04:57.67",
                adjustedEnd: "0:05:01.57",
                start: "0:04:57.67",
                end: "0:05:01.57"
            }
        ]
    }
],
```

## <a name="how-to-index-audio-effects"></a>Indizieren von Audioeffekten

Um den Indexprozess so zu festlegen, dass er die Erkennung von Audioeffekten umfasst, sollte der Benutzer im Menü „Video- und Audioindizierung“ eine der erweiterten Voreinstellungen auswählen, wie unten dargestellt.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/audio-effects-detection/index-audio-effect.png" alt-text="Bild zur Indizierung von Audioeffekten":::

## <a name="closed-caption"></a>Untertitel für Hörgeschädigte

Wenn Audioeffekte in den Untertiteldateien abgerufen werden, werden sie in der folgenden Struktur in eckigen Klammern abgerufen:

|type| Beispiel|
|---|---|
|SRT |00:00:00,000  00:00:03,671<br/>[Schuss]|
|VTT |00:00:00.000  00:00:03.671<br/>[Schuss]|
|TTML|Vertrauen: 0.9047 <br/> `<p begin="00:00:00.000" end="00:00:03.671">[Gunshot]</p>`|
|TXT |[Schuss]|
|CSV |0.9047,00:00:00.000,00:00:03.671, [Schuss]|

Audioeffekte in Untertiteldateien werden unter Anwendung der folgenden Logik abgerufen:

* Der Ereignistyp `Silence` wird Untertiteln für Hörgeschädigte nicht hinzugefügt.
* Die maximale Dauer zum Anzeigen eines Ereignisses beträgt 5 Sekunden.
* Die minimale Timerdauer zum Anzeigen eines Ereignisses beträgt 700 Millisekunden.

## <a name="adding-audio-effects-in-closed-caption-files"></a>Hinzufügen von Audioeffekten in Untertiteldateien

Audioeffekte können den von Azure Video Analyzer unterstützten Untertiteldateien über die [API zum Abrufen von Videountertiteln](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Captions) hinzugefügt werden, indem Sie den `includeAudioEffects`-Parameter auf „true“ festlegen oder die Oberfläche des video.ai-Portals verwenden, wobei Sie **Download** -> **Closed Captions** -> **Include Audio Effects** (Herunterladen > Untertitel > Audioeffekte einschließen) auswählen.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/audio-effects-detection/close-caption.jpg" alt-text="Audioeffekte in Untertiteln":::

> [!NOTE]
> Wenn Sie aus Untertiteldateien [Transkript aktualisieren](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Transcript) oder [Benutzerdefiniertes Sprachmodell aktualisieren](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model) verwenden, werden in diesen Dateien enthaltene Audioeffekte ignoriert.

## <a name="limitations-and-assumptions"></a>Einschränkungen und Voraussetzungen

* Das Modell funktioniert nur bei Segmenten ohne Sprache.
* Das Modell verarbeitet derzeit immer nur eine Kategorie gleichzeitig. Beispielsweise werden Weinen und Sprache im Hintergrund oder ein Schuss bei gleichzeitiger Explosion derzeit nicht unterstützt.
* Das Modell unterstützt derzeit keine Fälle, bei denen im Hintergrund laute Musik zu hören ist.
* Minimale Segmentlänge: 2 Sekunden.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht](video-indexer-overview.md)
