---
title: Szenen, Aufnahmen und Keyframes in Azure Video Analyzer for Media (früher Video Indexer)
description: Dieses Thema bietet eine Übersicht über die Szenen, Aufnahmen und Keyframes in Azure Video Analyzer for Media (früher Video Indexer).
ms.topic: how-to
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: b6825b5a50793589a07c16ef274d1e4231a2d4e0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655941"
---
# <a name="scenes-shots-and-keyframes"></a>Szenen, Aufnahmen und Keyframes

Azure Video Analyzer for Media (früher Video Indexer) unterstützt die Segmentierung von Videos in zeitliche Einheiten basierend auf strukturellen und semantischen Eigenschaften. Diese Funktion ermöglicht es Kunden, ihre Videoinhalte auf der Grundlage unterschiedlicher Granularitäten einfach zu durchsuchen, zu verwalten und zu bearbeiten. Beispielsweise basierend auf Szenen, Aufnahmen und Keyframes, die in diesem Thema beschrieben werden.   

![Szenen, Aufnahmen und Keyframes](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Szenenerkennung  
 
Video Analyzer for Media bestimmt basierend auf visuellen Hinweisen, wann sich eine Szene im Video ändert. Eine Szene stellt ein einzelnes Ereignis dar und besteht aus einer Reihe von aufeinanderfolgenden Aufnahmen, die semantisch zusammenhängen. Ein Szenenminiaturbild ist das erste Keyframe der zugrunde liegenden Aufnahme. Video Analyzer for Media segmentiert ein Video in Szenen, die auf Farbkohärenz bei aufeinanderfolgenden Aufnahmen basieren, und ruft die Anfangs- und Endzeit jeder Szene ab. Die Szenenerkennung stellt eine anspruchsvolle Aufgabe dar, da sie die Quantifizierung semantischer Aspekte von Videos beinhaltet.

> [!NOTE]
> Sie kann für Videos mit mindestens drei Szenen angewendet werden.

## <a name="shot-detection"></a>Szenenwechselerkennung

Video Analyzer for Media bestimmt, wann sich eine Aufnahme im Video aufgrund visueller Signale ändert, indem er sowohl abrupte als auch allmähliche Übergänge im Farbschema benachbarter Frames verfolgt. Die Metadaten der Aufnahme beinhalten eine Start- und Endzeit sowie die Liste der Keyframes, die darin enthalten sind. Die Aufnahmen sind aufeinanderfolgende Bilder, die gleichzeitig von derselben Kamera aufgenommen werden.

## <a name="keyframe-detection"></a>Keyframe-Erkennung

Video Analyzer for Media wählt die Frames aus, die die jeweilige Aufnahme am besten darstellen. Keyframes sind die repräsentativen Einzelframes, die basierend auf ästhetischen Eigenschaften (z.B. Kontrast und Stabilität) aus dem gesamten Video ausgewählt werden. Video Analyzer for Media ruft eine Liste von Keyframe-IDs als Teil der Metadaten der Aufnahme ab, anhand derer Kunden den Keyframe als hochauflösendes Bild extrahieren können.  

### <a name="extracting-keyframes"></a>Extrahieren von Keyframes

Um Keyframes mit hoher Auflösung für das Video zu extrahieren, müssen Sie das Video zunächst hochladen und indizieren.

![Keyframes](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-analyzer-for-media-website"></a>Mit der Video Analyzer for Media-Website

Wenn Sie Keyframes über die Video Analyzer for Media-Website extrahieren möchten, laden Sie das Video hoch, und indizieren Sie es. Klicken Sie nach Abschluss des Indizierungsauftrags auf die Schaltfläche **Herunterladen**, und wählen Sie **Artefakte (ZIP)** aus. Dadurch wird der Ordner mit den Artefakten auf Ihren Computer heruntergeladen. 

![Screenshot des Dropdownmenüs „Herunterladen“ mit ausgewählter Option „Artefakte“](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Entzippen und öffnen Sie den Ordner. Im Ordner *_KeyframeThumbnail* finden Sie alle Keyframes, die aus dem Video extrahiert wurden. 

#### <a name="with-the-video-analyzer-for-media-api"></a>Mit der Video Analyzer for Media-API

Wenn Sie Keyframes über die Video Indexer-API abrufen möchten, können Sie das Video mithilfe des Aufrufs zum Hochladen des Videos ([Upload Video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)) hochladen und indizieren. Rufen Sie [Get Video Index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Index) (Videoindex abrufen) auf, nachdem der Indizierungsauftrag abgeschlossen wurde. Dadurch erhalten Sie alle Erkenntnisse, die mit Video Indexer aus den Inhalten in eine JSON-Datei extrahiert wurden.  

Sie erhalten eine Liste der Keyframe-IDs als Teil der Metadaten der einzelnen Aufnahmen. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

Sie müssen nun jede dieser Keyframe-IDs für den Aufruf zum Abrufen von Miniaturansichten ([Get Thumbnails](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail)) ausführen. Dadurch werden alle Keyframe-Bilder auf Ihren Computer heruntergeladen. 

## <a name="editorial-shot-type-detection"></a>Erkennung von redaktionellen Aufnahmetypen

Keyframes sind mit Aufnahmen in der Ausgabe-JSON verknüpft. 

Der einer einzelnen Aufnahme im Erkenntnis-JSON zugeordnete Aufnahmetyp stellt deren redaktionellen Typ dar. Diese Art Aufnahmetyp-Charakterisierung kann beim Bearbeiten von Videos zu Clips oder Trailern oder bei der Suche nach einem bestimmten Stil oder Keyframe zu künstlerischen Zwecken nützlich sein. Die verschiedenen Typen werden auf der Grundlage einer Analyse des ersten Keyframes jeder Aufnahme bestimmt. Aufnahmen werden anhand des Maßstabs, der Größe und der Position der Gesichter identifiziert, die in ihrem ersten Keyframe abgebildet sind. 

Größe und Maßstab der Aufnahme werden auf der Grundlage des Abstands zwischen der Kamera und den Gesichtern bestimmt, die im Frame dargestellt sind. Mithilfe dieser Eigenschaften erkennt Video Analyzer for Media die folgenden Aufnahmetypen:

* Wide (Totale): Zeigt den gesamten Körper einer Person.
* Medium (Halbtotale): Zeigt Oberkörper und Gesicht einer Person.
* Close up (Nahaufnahme): Zeigt in der Hauptsache das Gesicht einer Person.
* Extreme close-up (Extreme Nahaufnahme): Zeigt das Gesicht einer Person bildschirmfüllend. 

Aufnahmetypen können auch anhand der Position der Hauptfiguren im Verhältnis zum Mittelpunkt des Frames bestimmt werden. Diese Eigenschaft definiert die folgenden Aufnahmetypen in Video Analyzer for Media:

* Left face (Gesicht links): Eine Person ist auf der linken Seite des Frames dargestellt.
* Center face (Gesicht Mitte): Eine Person ist im mittleren Bereich des Frames dargestellt.
* Right face (Gesicht rechts): Eine Person ist auf der rechten Seite des Frames dargestellt.
* Outdoor (Außen): Eine Person ist in einer Außenumgebung dargestellt.
* Indoor (Innen): Eine Person ist in einer Innenumgebung dargestellt.

Zusätzliche Merkmale:

* Two shots (Zwei Aufnahmen): Zeigt die Gesichter von zwei Personen mit mittlerer Größe.
* Multiple faces (Mehrere Gesichter): mehr als zwei Personen.


## <a name="next-steps"></a>Nächste Schritte

[Untersuchen der von der API erzeugten Video Analyzer for Media-Ausgabe](video-indexer-output-json-v2.md#scenes)
