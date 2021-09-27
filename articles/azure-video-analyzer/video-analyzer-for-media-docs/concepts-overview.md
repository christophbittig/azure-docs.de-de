---
title: Konzepte für Azure Video Analyzer for Media (früher Video Indexer) – Azure
titleSuffix: Azure Video Analyzer for Media (formerly Video Indexer)
description: Dieser Artikel bietet eine kurze Übersicht über Terminologie und Konzepte von Azure Video Analyzer for Media (früher Video Indexer).
ms.topic: conceptual
ms.date: 01/19/2021
ms.author: juliako
ms.openlocfilehash: 6b68ee08d6569bd22709e5b9ee25cbbf745c0470
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630165"
---
# <a name="video-analyzer-for-media-concepts"></a>Video Analyzer for Media-Konzepte

Dieser Artikel bietet eine kurze Übersicht über Terminologie und Konzepte von Azure Video Analyzer for Media (früher Video Indexer).

## <a name="audiovideocombined-insights"></a>Audiodaten/Video/kombinierte Erkenntnisse

Wenn Sie Ihre Videos zu Video Analyzer for Media hochladen, analysiert es durch Ausführen verschiedener KI-Modelle sowohl visuelle als auch akustische Informationen. Während Video Analyzer for Media Ihr Video analysiert, werden die Erkenntnisse von den KI-Modellen extrahiert. Weitere Informationen finden Sie in der [Übersicht](video-indexer-overview.md).

## <a name="confidence-scores"></a>Zuverlässigkeitsbewertungen 

Die Zuverlässigkeitsbewertung gibt das Vertrauen in eine Erkenntnis an. Dies ist eine Zahl zwischen 0,0 und 1,0. Je höher die Bewertung, desto größer die Zuverlässigkeit der Antwort. Beispiel: 

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
```

## <a name="content-moderation"></a>Inhaltsmoderation

Verwenden Sie textbasierte und visuelle Moderationsmodelle, um Ihre Benutzer vor unangemessenen Inhalten zu schützen und zu überprüfen, ob die von Ihnen veröffentlichten Inhalte den Werten Ihrer Organisation entsprechen. Sie können bestimmte Videos automatisch blockieren oder Ihre Benutzer vor dem Inhalt warnen. Weitere Informationen finden Sie unter [visualContentModeration](video-indexer-output-json-v2.md#visualcontentmoderation). 

## <a name="blocks"></a>Blöcke   

Die Blöcke sollen das Durchgehen der Daten vereinfachen. Die Unterteilung in Blöcke kann beispielsweise darauf basieren, dass sich der Sprecher ändert oder dass es zu einer längeren Pause kommt.  

## <a name="project-and-editor"></a>Projekt und Editor

Die [Video Analyzer for Media](https://www.videoindexer.ai/)-Website ermöglicht Ihnen, die umfassenden Erkenntnisse Ihres Videos für Folgendes zu nutzen: die richtigen Medieninhalte zu finden, die für Sie interessanten Teile zu finden und die Ergebnisse zum Erstellen eines völlig neuen Projekts zu nutzen. Nach der Erstellung kann das Projekt gerendert und von Video Analyzer for Media heruntergeladen sowie in eigenen Bearbeitungsanwendungen oder Downstream-Workflows verwendet werden.

Einige Szenarien, in denen Sie dieses Feature als hilfreich empfinden können, sind: 

* Erstellung von Filmhighlights für Trailer.
* Verwendung alter Clips von Videos in Nachrichtensendungen.
* Erstellung von kürzeren Inhalten für soziale Medien.

Weitere Informationen finden Sie unter [Verwenden des Video Indexer-Editors zum Erstellen von Projekten](use-editor-create-project.md).

## <a name="keyframes"></a>Keyframes

Video Analyzer for Media wählt die Frames aus, die die jeweilige Aufnahme am besten darstellen. Keyframes sind die repräsentativen Einzelframes, die basierend auf ästhetischen Eigenschaften (z.B. Kontrast und Stabilität) aus dem gesamten Video ausgewählt werden. Weitere Informationen finden Sie unter [Szenen, Aufnahmen und Keyframes](scenes-shots-keyframes.md).

## <a name="time-range-vs-adjusted-time-range"></a>Vergleich: Zeitbereich und angepasster Zeitbereich   

„TimeRange“ ist der Zeitbereich im Originalvideo. „adjustedTimeRange“ ist der Zeitbereich im Verhältnis zur aktuellen Wiedergabeliste. Da Sie eine Wiedergabeliste aus verschiedenen Zeilen verschiedener Videos erstellen können, haben Sie die Möglichkeit, nur eine Zeile aus einem einstündigen Video zu verwenden, z.B. 10:00 - 10:15. In diesem Fall erhalten Sie eine Wiedergabeliste mit einer Zeile, in der der Zeitbereich 10:00 - 10: 15, der Wert für adjustedTimeRange aber 00:00 - 00:15 ist. 

## <a name="widgets"></a>Widgets

Video Analyzer for Media unterstützt das Einbetten von Widgets in Ihre Apps. Weitere Informationen finden Sie im Artikel zum [Einbetten von Video Analyzer for Media-Widgets in Ihre Apps](video-indexer-embed-widgets.md).

## <a name="summarized-insights"></a>Zusammenfassung der Erkenntnisse  

Die Zusammenfassung der Erkenntnisse enthält eine aggregierte Ansicht der Daten: Gesichter, Themen, Emotionen. Beispielsweise müssen nicht Tausende von Zeitbereichen durchlaufen werden, um zu überprüfen, welche Gesichter darin enthalten sind. Die zusammengefassten Erkenntnisse enthalten alle Gesichter und dazu jeweils die Zeitbereiche und den prozentualen Anteil der Sichtbarkeit.  

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht](video-indexer-overview.md)
- [Erkenntnisse](video-indexer-output-json-v2.md)
