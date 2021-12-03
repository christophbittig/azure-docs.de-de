---
title: 'Unterstützte Vorgänge zur räumlichen Analyse: Azure'
description: In diesem Referenzartikel werden die verschiedenen Eigenschaften der Vorgänge zur räumlichen Analyse beschrieben, die von Azure Video Analyzer unterstützt werden.
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 11062ebbe48e8c7e2451ff448770055073d7e792
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560141"
---
# <a name="supported-spatial-analysis-operations"></a>Unterstützte Vorgänge zur räumlichen Analyse

Die räumliche Analyse ermöglicht die Analyse von in Echtzeit gestreamten Videodaten von Kamerageräten. Für jedes von Ihnen konfigurierte Kameragerät generieren die Vorgänge einen Ausgabestream mit JSON-Nachrichten, die an Azure Video Analyzer gesendet werden. 

Video Analyzer implementiert die folgenden Vorgänge zur räumlichen Analyse: 

| Vorgangsbezeichner| Beschreibung|
|---------|---------|
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonZoneCrossingOperation | Hiermit wird ein _personZoneEnterExitEvent_-Ereignis ausgegeben, wenn eine Person die Zone betritt oder verlässt. Außerdem werden direktionale Informationen wie die nummerierte Seite der überschrittenen Zone bereitstellt. Hiermit wird ein _personZoneDwellTimeEvent_-Ereignis ausgegeben, wenn eine Person die Zone verlässt. Außerdem wird neben direktionalen Informationen auch die Zeitspanne in Millisekunden angegeben, die die Person in der Zone verbracht hat. |
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonLineCrossingOperation | Führt eine Nachverfolgung durch, wenn eine Person im Blickfeld der Kamera eine festgelegte Linie überschreitet.  |
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonDistanceOperation | Führt eine Nachverfolgung durch, wenn Personen gegen eine Abstandsregel verstoßen.  |
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonCountOperation | Zählt Personen, die sich innerhalb einer festgelegten Zone im Blickfeld der Kamera befinden. Die Zone muss vollständig von einer einzelnen Kamera abgedeckt werden, damit PersonCount eine exakte Gesamtzahl aufzeichnen kann.  |
| Microsoft.VideoAnalyzer.SpatialAnalysisCustomOperation | Generischer Vorgang, der für alle oben erwähnten Szenarien verwendet werden kann. Diese Option ist insbesondere hilfreich, wenn Sie die gleiche Kamera für mehrere Szenarien verwenden oder Systemressourcen (beispielsweise die GPU) effizienter nutzen möchten. |



## <a name="person-zone-crossing"></a>Zonenüberquerung von Personen

**Vorgangsbezeichner**: `Microsoft.VideoAnalyzer.SpatialAnalysisPersonZoneCrossingOperation`

Ein Beispiel für den [Vorgang „Zonenüberquerung von Personen“ finden](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-zone-crossing-operation-topology.json) Sie in unserem GitHub-Beispiel.

#### <a name="parameters"></a>Parameter:

| Name                      | type    | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `zones`                     | list    | Die Liste mit den Zonen.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `name`                     | Zeichenfolge  | Der Anzeigename für diese Zone.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `polygon`                   | Zeichenfolge  | Jedes Wertpaar stellt den x/y-Wert für Scheitelpunkte eines Polygons dar. Mit dem Polygon werden die Bereiche dargestellt, in denen Personen nachverfolgt oder gezählt werden. Die Gleitkommawerte stellen die Position des Scheitelpunkts relativ zur oberen linken Ecke dar. Zum Berechnen der absoluten x/y-Werte multiplizieren Sie diese Werte mit der Framegröße. Gleitkommaereignisse für Schwellenwerte werden ausgegeben, wenn der Personenwert diese Anzahl von Pixeln innerhalb der Zone übersteigt. Der Standardwert ist 48, wenn der Typ „zonecrossing“ und 16, wenn die Zeit DwellTime ist. Dies sind die empfohlenen Werte, um eine maximale Genauigkeit zu erzielen. |
| `eventType`                 | Zeichenfolge  | Für cognitiveservices.vision.spatialanalysis-personcrossingpolygon sollte dies `zonecrossing` oder `zonedwelltime` lauten.                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `trigger`                   | Zeichenfolge  | Der Typ des Triggers für das Senden eines Ereignisses. Unterstützte Werte: „event“ (Ereignis). Wird ausgelöst, wenn eine Person die Zone betritt oder verlässt.                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `focus`                     | Zeichenfolge  | Dies ist die Punktposition innerhalb des Begrenzungsrahmens der Person, die zum Berechnen von Ereignissen verwendet wird. Der Wert für den Fokus kann „footprint“ (Fußabdruck der Person), „bottom_center“ (Mitte der Unterkante des Begrenzungsrahmens der Person) oder „center“ (Mitte des Begrenzungsrahmens der Person) sein. Der Standardwert ist „footprint“.                                                                                                                                                                                                                                                                                               |
| `threshold`                 | float   | Ereignisse werden ausgegeben, wenn der Personenwert größer als diese Anzahl von Pixeln innerhalb der Zone ist. Der Standardwert ist 16. Dies ist der empfohlene Wert, um eine maximale Genauigkeit zu erzielen.                                                                                                |
| `enableFaceMaskClassifier`  | boolean | Mit „true“ wird die Erkennung von Personen mit Gesichtsmaske im Videostream aktiviert. Mit „false“ wird diese Option deaktiviert. Diese Einstellung ist standardmäßig deaktiviert. Für die Gesichtsmaskenerkennung muss der Parameter „width“ für das Eingabevideo auf „1920“ festgelegt sein ("INPUT_VIDEO_WIDTH": 1920). Das Gesichtsmaskenattribut wird nicht zurückgegeben.                                                                                                                                                                                                                                                                                                        |
| `detectorNodeConfiguration` | Zeichenfolge  | Die Parameter vom Typ „DETECTOR_NODE_CONFIG“ für alle Vorgänge der räumlichen Analyse.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `trackerNodeConfiguration` | Zeichenfolge  | Die Parameter des Typs DETECTOR_NODE_CONFIG für alle Vorgänge zur räumlichen Analyse.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

### <a name="orientation-parameter-settings"></a>Einstellungen des Ausrichtungsparameters

Sie können die Ausrichtungsberechnung über Einstellungen des Parameters DETECTOR_NODE_CONFIG konfigurieren.
```json
{
    "enable_orientation": true,
}

```
| Name                      | type    | Beschreibung                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `enable_orientation`                     | bool    | Gibt an, ob Sie die Ausrichtung für die erkannten Personen berechnen möchten. `enable_orientation` ist standardmäßig auf TRUE festgelegt.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
### <a name="speed-parameter-settings"></a>Einstellungen für Geschwindigkeitsparameter

Sie können die Geschwindigkeitsberechnung über Einstellungen des Parameters TRACKER_NODE_CONFIG konfigurieren.
```json
{
    "enable_speed": true,
}

```
| Name                      | type    | Beschreibung                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `enable_speed`                     | bool    | Gibt an, ob Sie die Geschwindigkeit für die erkannten Personen berechnen möchten. `enable_speed` ist standardmäßig auf TRUE festgelegt. Es wird dringend empfohlen, sowohl Geschwindigkeit als auch Ausrichtung zu aktivieren, um die besten Schätzwerte zu erhalten.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |

#### <a name="output"></a>Ausgabe:
```json
{
  "body": {
    "timestamp": 147026846756730,
    "inferences": [
      {
        "type": "entity",
        "inferenceId": "8e8269c1a9584b3a8f16a3cd7a2cd45a",
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.9511422
          },
          "box": {
            "l": 0.59845686,
            "t": 0.35958588,
            "w": 0.11951797,
            "h": 0.50172085
          }
        },
        "extensions": {
          "centerGroundPointY": "0.0",
          "footprintY": "inf",
          "centerGroundPointX": "0.0",
          "mappedImageOrientation": "inf",
          "groundOrientationAngle": "inf",
          "footprintX": "inf",
          "trackingId": "f54d4c8fb4f345a9afb944303b0f3b40",
          "speed": "0.0"
        }
      },
      {
        "type": "entity",
        "inferenceId": "c54c9f92dd0d442b8d1840756715a5c7",
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.92762595
          },
          "box": {
            "l": 0.8098704,
            "t": 0.47707137,
            "w": 0.18019487,
            "h": 0.48659682
          }
        },
        "extensions": {
          "footprintY": "inf",
          "groundOrientationAngle": "inf",
          "trackingId": "a226eda9226e4ec9b39ebceb7c8c1f61",
          "mappedImageOrientation": "inf",
          "speed": "0.0",
          "centerGroundPointX": "0.0",
          "centerGroundPointY": "0.0",
          "footprintX": "inf"
        }
      },
      {
        "type": "event",
        "inferenceId": "aad2778756a94afd9055fdbb3a370d62",
        "relatedInferences": [
          "8e8269c1a9584b3a8f16a3cd7a2cd45a"
        ],
        "event": {
          "name": "personZoneEnterExitEvent",
          "properties": {
            "trackingId": "f54d4c8fb4f345a9afb944303b0f3b40",
            "zone": "retailstore",
            "status": "Enter"
          }
        }
      },
      {
        "type": "event",
        "inferenceId": "e30103d3af28485688d7c77bbe10b5b5",
        "relatedInferences": [
          "c54c9f92dd0d442b8d1840756715a5c7"
        ],
        "event": {
          "name": "personZoneEnterExitEvent",
          "properties": {
            "trackingId": "a226eda9226e4ec9b39ebceb7c8c1f61",
            "status": "Enter",
            "zone": "retailstore"
          }
        }
      }
    ]
  }
```

## <a name="person-line-crossing"></a>Linienüberquerung von Personen

**Vorgangsbezeichner**: `Microsoft.VideoAnalyzer.SpatialAnalysisPersonLineCrossingOperation`

Ein Beispiel für den [Vorgang „Linienüberquerung von Personen“ finden](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-line-crossing-operation-topology.json) Sie in unserem GitHub-Beispiel.

#### <a name="parameters"></a>Parameter:

| Name                      | type    | BESCHREIBUNG                                                                                                                                                                                                                                                                   |
| ------------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `lines`                     | list    | Die Liste mit den Linien.                                                                                                                                                                                                                                                                |
| `name`                      | Zeichenfolge  | Der Anzeigename für diese Linie.                                                                                                                                                                                                                                                  |
| `line`                      | Zeichenfolge  | Die Wertpaare stellen jeweils den Start- und Endpunkt der Linie dar. Die Gleitkommawerte stellen die Position des Scheitelpunkts relativ zur oberen linken Ecke dar. Zum Berechnen der absoluten x/y-Werte multiplizieren Sie diese Werte mit der Framegröße.                            |
| `start`                      | Wertpaar  | x/y-Koordinaten für den Startpunkt der Linie. Die Gleitkommawerte stellen die Position des Scheitelpunkts relativ zur oberen linken Ecke dar. Zum Berechnen der absoluten x/y-Werte multiplizieren Sie diese Werte mit der Framegröße.                            |
| `end`                      | Wertpaar  | x/y-Koordinaten für den Endpunkt der Linie. Die Gleitkommawerte stellen die Position des Scheitelpunkts relativ zur oberen linken Ecke dar. Zum Berechnen der absoluten x/y-Werte multiplizieren Sie diese Werte mit der Framegröße.                            |
| `type`                     | Zeichenfolge  | Muss `linecrossing` sein. |
| `trigger`                     | Zeichenfolge  | Der Typ des Triggers für das Senden eines Ereignisses. Unterstützte Werte: „event“ (Ereignis). Wird ausgelöst, wenn eine Person die Linie überschreitet.|
| `outputFrequency`           | INT     | Die Rate, mit der Ereignisse ausgegeben werden. Bei „outputFrequency = X“ wird jedes x-te Ereignis ausgegeben. „outputFrequency = 2“ bedeutet beispielsweise, dass jedes zweite Ereignis ausgegeben wird. Die Ausgabehäufigkeit (output_frequency) gilt für Ereignis und Intervall.                                                       |
| `focus`                     | Zeichenfolge  | Dies ist die Punktposition innerhalb des Begrenzungsrahmens der Person, die zum Berechnen von Ereignissen verwendet wird. Der Wert für den Fokus kann „footprint“ (Fußabdruck der Person), „bottom_center“ (Mitte der Unterkante des Begrenzungsrahmens der Person) oder „center“ (Mitte des Begrenzungsrahmens der Person) sein. Der Standardwert ist „footprint“. |
| `threshold`                 | float   | Ereignisse werden ausgegeben, wenn der Personenwert größer als diese Anzahl von Pixeln innerhalb der Zone ist. Der Standardwert ist 16. Dies ist der empfohlene Wert, um eine maximale Genauigkeit zu erzielen.                                                                                                |
| `enableFaceMaskClassifier`  | boolean | Mit „true“ wird die Erkennung von Personen mit Gesichtsmaske im Videostream aktiviert. Mit „false“ wird diese Option deaktiviert. Diese Einstellung ist standardmäßig deaktiviert. Für die Gesichtsmaskenerkennung muss der Parameter „width“ für das Eingabevideo auf „1920“ festgelegt sein ("INPUT_VIDEO_WIDTH": 1920). Das Gesichtsmaskenattribut wird nicht zurückgegeben.          |
| `detectorNodeConfiguration` | Zeichenfolge  | Die Parameter vom Typ „DETECTOR_NODE_CONFIG“ für alle Vorgänge der räumlichen Analyse.                                                                                                                                                                                                      |
| `trackerNodeConfiguration` | Zeichenfolge  | Die Parameter des Typs DETECTOR_NODE_CONFIG für alle Vorgänge zur räumlichen Analyse.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
#### <a name="output"></a>Ausgabe:

```json
{
  "timestamp": 145666620394490,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "2d3c7c7d6c0f4af7916eb50944523bdf",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.38330078
        },
        "box": {
          "l": 0.5316645,
          "t": 0.28169397,
          "w": 0.045862257,
          "h": 0.1594377
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "trackingId": "ac4a79a29a67402ba447b7da95907453",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "2206088c80eb4990801f62c7050d142f",
      "relatedInferences": ["2d3c7c7d6c0f4af7916eb50944523bdf"],
      "event": {
        "name": "personLineEvent",
        "properties": {
          "trackingId": "ac4a79a29a67402ba447b7da95907453",
          "status": "CrossLeft",
          "zone": "door"
        }
      }
    }
  ]
}
```

## <a name="person-distance"></a>Personenabstand

**Vorgangsbezeichner**: `Microsoft.VideoAnalyzer.SpatialAnalysisPersonDistanceOperation`

Ein Beispiel für den [Vorgang „Personenabstand“ finden](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-distance-operation-topology.json) Sie in unserem GitHub-Beispiel.

#### <a name="parameters"></a>Parameter:

| Name                      | type    | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `zones`                     | list    | Die Liste mit den Zonen.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `name`                      | Zeichenfolge  | Der Anzeigename für diese Zone.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `polygon`                   | Zeichenfolge  | Jedes Wertpaar stellt den x/y-Wert für Scheitelpunkte eines Polygons dar. Mit dem Polygon werden die Bereiche dargestellt, in denen Personen nachverfolgt oder gezählt werden. Die Gleitkommawerte stellen die Position des Scheitelpunkts relativ zur oberen linken Ecke dar. Zum Berechnen der absoluten x/y-Werte multiplizieren Sie diese Werte mit der Framegröße. Gleitkommaereignisse für Schwellenwerte werden ausgegeben, wenn der Personenwert diese Anzahl von Pixeln innerhalb der Zone übersteigt. Der Standardwert ist 48, wenn der Typ „zonecrossing“ und 16, wenn die Zeit DwellTime ist. Dies sind die empfohlenen Werte, um eine maximale Genauigkeit zu erzielen. |
| `trigger`           | Zeichenfolge     | Der Typ des Triggers für das Senden eines Ereignisses. Unterstützte Werte sind „event“ (Ereignis) zum Senden von Ereignissen, wenn sich die Anzahl ändert, und „interval“ (Intervall) zum regelmäßigen Senden von Ereignissen unabhängig davon, ob sich die Anzahl geändert hat.                                                                                                                                                                                                                                                                                                                                                     |
| `focus`                     | Zeichenfolge  | Dies ist die Punktposition innerhalb des Begrenzungsrahmens der Person, die zum Berechnen von Ereignissen verwendet wird. Der Wert für den Fokus kann „footprint“ (Fußabdruck der Person), „bottom_center“ (Mitte der Unterkante des Begrenzungsrahmens der Person) oder „center“ (Mitte des Begrenzungsrahmens der Person) sein. Der Standardwert ist „footprint“.                                                                                                                                                                                                                                                                                               |
| `threshold`                | float   | Ereignisse werden ausgegeben, wenn der Personenwert größer als diese Anzahl von Pixeln innerhalb der Zone ist.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `outputFrequency`           | INT     | Die Rate, mit der Ereignisse ausgegeben werden. Bei „outputFrequency = X“ wird jedes x-te Ereignis ausgegeben. „outputFrequency = 2“ bedeutet beispielsweise, dass jedes zweite Ereignis ausgegeben wird. Die Ausgabehäufigkeit (output_frequency) gilt für Ereignis und Intervall.                                                                                                                                                                                                                                                                                                                                                     |
| `minimumDistanceThreshold`  | float   | Ein Abstand in Fuß, bei dem das Ereignis „TooClose“ ausgelöst wird, wenn Personen weniger als den angegebenen Abstand voneinander aufweisen.                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `maximumDistanceThreshold`  | float   | Ein Abstand in Fuß, bei dem das Ereignis „TooFar“ ausgelöst wird, wenn Personen mehr als den angegebenen Abstand voneinander aufweisen.                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `aggregationMethod`         | Zeichenfolge  | Die Methode für das aggregierte persondistance-Ergebnis. Die Aggregationsmethode (aggregationMethod) gilt für Modus und Durchschnitt.                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `enableFaceMaskClassifier`  | boolean | Mit „true“ wird die Erkennung von Personen mit Gesichtsmaske im Videostream aktiviert. Mit „false“ wird diese Option deaktiviert. Diese Einstellung ist standardmäßig deaktiviert. Für die Gesichtsmaskenerkennung muss der Parameter „width“ für das Eingabevideo auf „1920“ festgelegt sein ("INPUT_VIDEO_WIDTH": 1920). Das Gesichtsmaskenattribut wird nicht zurückgegeben.                                                                                                                                                                                                                                                                                                        |
| `detectorNodeConfiguration` | Zeichenfolge  | Die Parameter vom Typ „DETECTOR_NODE_CONFIG“ für alle Vorgänge der räumlichen Analyse.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `trackerNodeConfiguration` | Zeichenfolge  | Die Parameter des Typs DETECTOR_NODE_CONFIG für alle Vorgänge zur räumlichen Analyse.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
#### <a name="output"></a>Ausgabe:

```json
{
  "timestamp": 145666613610297,
  "inferences": [
    {
      "type": "event",
      "inferenceId": "85a5fc4936294a3bac90b9c43876741a",
      "event": {
        "name": "personDistanceEvent",
        "properties": {
          "maximumDistanceThreshold": "14.5",
          "personCount": "0.0",
          "eventName": "Unknown",
          "zone": "door",
          "averageDistance": "0.0",
          "minimumDistanceThreshold": "1.5",
          "distanceViolationPersonCount": "0.0"
        }
      }
    }
  ]
}
```

## <a name="person-count"></a>Personenanzahl

**Vorgangsbezeichner**: `Microsoft.VideoAnalyzer.SpatialAnalysisPersonCountOperation`

Ein Beispiel für den [Vorgang „Personenanzahl“ finden](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json) Sie in unserem GitHub-Beispiel.

#### <a name="parameters"></a>Parameter:

| Name                      | type    | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `zones`                     | list    | Die Liste mit den Zonen.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `name`                      | Zeichenfolge  | Der Anzeigename für diese Zone.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `polygon`                   | Zeichenfolge  | Jedes Wertpaar stellt den x/y-Wert für Scheitelpunkte eines Polygons dar. Mit dem Polygon werden die Bereiche dargestellt, in denen Personen nachverfolgt oder gezählt werden. Die Gleitkommawerte stellen die Position des Scheitelpunkts relativ zur oberen linken Ecke dar. Zum Berechnen der absoluten x/y-Werte multiplizieren Sie diese Werte mit der Framegröße. Gleitkommaereignisse für Schwellenwerte werden ausgegeben, wenn der Personenwert diese Anzahl von Pixeln innerhalb der Zone übersteigt. Der Standardwert ist 48, wenn der Typ „zonecrossing“ und 16, wenn die Zeit DwellTime ist. Dies sind die empfohlenen Werte, um eine maximale Genauigkeit zu erzielen. |
| `outputFrequency`           | INT     | Die Rate, mit der Ereignisse ausgegeben werden. Bei „outputFrequency = X“ wird jedes x-te Ereignis ausgegeben. „outputFrequency = 2“ bedeutet beispielsweise, dass jedes zweite Ereignis ausgegeben wird. Die Ausgabehäufigkeit (output_frequency) gilt für Ereignis und Intervall.                                                                                                                                                                                                                                                                                                                                                     |
| `trigger`                   | Zeichenfolge  | Der Typ des Triggers für das Senden eines Ereignisses. Unterstützte Werte sind „event“ (Ereignis) zum Senden von Ereignissen, wenn sich die Anzahl ändert, und „interval“ (Intervall) zum regelmäßigen Senden von Ereignissen unabhängig davon, ob sich die Anzahl geändert hat.                                                                                                                                                                                                                                                                                                                                                           |
| `focus`                     | Zeichenfolge  | Dies ist die Punktposition innerhalb des Begrenzungsrahmens der Person, die zum Berechnen von Ereignissen verwendet wird. Der Wert für den Fokus kann „footprint“ (Fußabdruck der Person), „bottom_center“ (Mitte der Unterkante des Begrenzungsrahmens der Person) oder „center“ (Mitte des Begrenzungsrahmens der Person) sein. Der Standardwert ist „footprint“.                                                                                                                                                                                                                                                                                               |
| `threshold`                 | float   | Ereignisse werden ausgegeben, wenn der Personenwert größer als diese Anzahl von Pixeln innerhalb der Zone ist.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `enableFaceMaskClassifier`  | boolean | Mit „true“ wird die Erkennung von Personen mit Gesichtsmaske im Videostream aktiviert. Mit „false“ wird diese Option deaktiviert. Diese Einstellung ist standardmäßig deaktiviert. Für die Gesichtsmaskenerkennung muss der Parameter „width“ für das Eingabevideo auf „1920“ festgelegt sein ("INPUT_VIDEO_WIDTH": 1920). Das Gesichtsmaskenattribut wird nicht zurückgegeben.                                                                                                                                                                                                                                                                                                        |
| `detectorNodeConfiguration` | Zeichenfolge  | Die Parameter vom Typ „DETECTOR_NODE_CONFIG“ für alle Vorgänge der räumlichen Analyse.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `trackerNodeConfiguration` | Zeichenfolge  | Die Parameter des Typs DETECTOR_NODE_CONFIG für alle Vorgänge zur räumlichen Analyse.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
#### <a name="output"></a>Ausgabe:

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```

## <a name="custom-operation"></a>Benutzerdefinierter Vorgang

**Vorgangsbezeichner**: `Microsoft.VideoAnalyzer.SpatialAnalysisCustomOperation`

Ein Beispiel eines [benutzerdefinierten Vorgangs](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/custom-operation-topology.json) finden Sie in unserem GitHub Beispiel.

#### <a name="parameters"></a>Parameter:

| Name                   | type   | BESCHREIBUNG                           |
| ---------------------- | ------ | ------------------------------------- |
| extensionConfiguration | Zeichenfolge | JSON-Darstellung des Vorgangs |

#### <a name="output"></a>Ausgabe:

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```
