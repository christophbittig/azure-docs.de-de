---
title: 'Schema für Rückschlussmetadaten: Azure'
description: Im Azure Video Analyzer folgt jedes Inferenzobjekt, unabhängig von der Verwendung eines HTTP-basierten Vertrags oder eines gRPC-basierten Vertrags, dem in diesem Thema beschriebenen Objektmodell.
ms.topic: reference
ms.date: 06/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9d22e4b05a19b7d0c120f02f4a97017865563314
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095039"
---
# <a name="inference-metadata-schema"></a>Schema für Rückschlussmetadaten 

[!INCLUDE [header](includes/edge-env.md)]

Im Azure Video Analyzer folgt jedes Inferenzobjekt, unabhängig von der Verwendung eines HTTP-basierten Vertrags oder eines gRPC-basierten Vertrags, dem in diesem Thema beschriebenen Objektmodell.

## <a name="object-model"></a>Objektmodell

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/inference-metadata-schema/object-model.png" alt-text="Azure Video Analyzer Oot-Modell" lightbox="./media/inference-metadata-schema/object-model.png":::
 

|Typendefinition|Beschreibung|
|---|---|
|Tag|Tag oder Etikett, das dem Ergebnis zugeordnet ist. Zusammen mit der Kennzeichnung erhalten Sie auch den mit der Kennzeichnung verbundenen Vertrauenswert.|
|attribute|Zusätzliche Attribute, die dem Ergebnis zugeordnet sind. Sie können neue Attribute hinzufügen, die Sie von der Rückschlussengine zusammen mit dem Zuverlässigkeitswert erhalten.|
|Attributliste|Liste optionaler Attribute.|
|Rechteck|Rechteckiger Bereich relativ zum linken oberen Winkel der Abbildung. Die erforderlichen Eigenschaften sind `left`, `width`, `height` und `top` von origin". <br/>Der linke und die obere Seite sowie die Breite und Höhe des umgebenden Felds werden in einer Skala von 0,0 bis 1,0 normalisiert. `left` (x-Koordinate) und `top` (y-Koordinate) sind Koordinaten, die die obere und linke Seite des umgebenden Felds darstellen. Bei den zurückgegebenen Werten `left` und `top` handelt es sich um Verhältnisse der gesamten Videodimensionen. Wenn die Videodimensionen beispielsweise 1920 x 1080 Pixel betragen, und die linke obere Koordinate des Begrenzungsfelds (350,50) ist, hat das Begrenzungsfeld einen `left`-Wert von 0,1822 (350/1920) und einen `top`-Wert von 0,0462 (50/1080). <br/>Die Werte `width` und `height` stellen die Dimensionen des umgebenden Felds als Verhältnis der insgesamten Videodimension dar. Wenn die Weite des umgebenden Felds eines Videos mit den Dimensionen von 1920 × 1080 70 Pixel beträgt, beträgt die zurückgegebene Weite 0,0364 (70/1920).|
|Klassifizierung|Klassifizierungsbeschriftung, die häufig für die gesamte Stichprobe gilt. Anhand von „Tag“ können Sie das Ergebnis klassifizieren.|
|Entität|In der Stichprobe erkannte oder identifizierte Entität. Wenn eine Entität von der Inferenz-Maschine erkannt wird, erhält sie einen Tag. Zudem werden zusätzliche Attribute, die abgeleitet wurden, sowie die Koordinaten eines rechteckigen Felds um die gefundene Entität zurückgegeben.|
|Ereignis|In der Stichprobe erkanntes Ereignis. Wenn in der Stichprobe ein Ereignis erkannt wird, werden der Name des Ereignisses und die Ereignis-spezifischen Eigenschaften zurückgegeben.|
|Bewegung|In der Stichprobe erkannte Bewegung. Wenn eine Bewegung in der Probe erkannt wird, werden die Koordinaten eines rechteckigen Begrenzungsrahmens (Bounding Box) zurückgegeben, in dem die Bewegung erkannt wurde.|
|Text|Mit der Stichprobe verknüpfter Text sowie die Zeitstempel für Textbeginn und Textende werden zurückgegeben.|
|Sonstiges|Gibt weitere generische Nutzlastinformationen zurück.|

Das folgende Beispiel enthält ein einzelnes Ereignis mit einigen unterstützten Rückschlusstypen:

```
[ 
  // Light Detection 
  { 
    "type": "classification", 
    "subtype": "lightDetection", 
    "classification": { 
      "tag": { "value": "daylight", "confidence": 0.86 }, 
      "attributes": [ 
          { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 } 
      ] 
    } 
  }, 
 
  // Motion Detection 
  { 
    "type": "motion", 
    "subtype": "motionDetection", 
    "motion": 
    { 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Yolo V3 
  { 
    "type": "entity", 
    "subtype": "objectDetection",     
    "entity": 
    { 
      "tag": { "value": "dog", "confidence": 0.97 }, 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Vehicle Identification 
  { 
    "type": "entity", 
    "subtype": "vehicleIdentification",     
    "entity": 
    { 
      "tag": { "value": "007-SPY", "confidence": 0.82 }, 
      "attributes":[   
        { "name": "color", "value": "black", "confidence": 0.90 }, 
        { "name": "body", "value": "coupe", "confidence": 0.87 }, 
        { "name": "make", "value": "Aston Martin", "confidence": 0.35 }, 
        { "name": "model", "value": "DBS V12", "confidence": 0.33 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // People Identification 
  { 
    "type": "entity", 
    "subtype": "peopleIdentification",     
    "entity": 
    { 
      "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 }, 
      "attributes":[   
        { "name": "age", "value": "73", "confidence": 0.87 }, 
        { "name": "glasses", "value": "yes", "confidence": 0.94 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    }, 
 
    // Open type coming from the gRPC Map 
    "extensions":  
    { 
      "vector": "e1xkaXNwbGF5c3R5bGUgaVxoYmFyIHtcZnJhYyB7ZH17ZHR9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSA9e1xoYXQge0h9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSB9KQ==", 
      "skeleton": "p1,p2,p3,p4" 
    } 
  }, 
 
  // Captions 
  {     
    "type": "text", 
    "subtype": "speechToText",   
    "text": 
    { 
      "value": "Humor 75%. Confirmed. Self-destruct sequence in T minus 10… 9…", 
      "language": "en-US", 
      "startTimestamp": 12000, 
      "endTimestamp": 13000 
    } 
]
```

## <a name="next-steps"></a>Nächste Schritte

- [gRPC-Datenvertrag](./grpc-extension-protocol.md)
- [HTTP-Datenvertrag](./http-extension-protocol.md)
