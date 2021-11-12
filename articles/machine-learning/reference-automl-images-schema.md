---
title: JSONL-Format für Aufgaben des maschinellen Sehens
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie Ihre JSONL-Dateien für die Datennutzung in automatisierten ML-Experimenten für Aufgaben des maschinellen Sehens formatieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: reference
ms.reviewer: nibaccam
ms.author: rvadthyavath
author: vadthyavath
ms.date: 10/13/2021
ms.custom: ''
ms.openlocfilehash: 725e8ae80d29a352bd2146906a782d4c8c2e6e38
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131442547"
---
# <a name="data-schemas-to-train-computer-vision-models-with-automated-machine-learning"></a>Datenschemas zum Trainieren von Modellen für maschinelles Sehen mit automatisiertem maschinellem Lernen

Hier erfahren Sie, wie Sie Ihre JSONL-Dateien in den Trainings- und Rückschlussphasen für die Datennutzung in automatisierten ML-Experimenten für Aufgaben des maschinellen Sehens formatieren.


## <a name="data-schema-for-training"></a>Datenschema für das Training 

Das automatisierte maschinelle Lernen von Azure Machine Learning für Bilder erfordert, dass Eingabebilddaten im [JSONL](https://jsonlines.org/)-Format (JSON Lines) vorbereitet werden. In diesem Abschnitt werden Eingabedatenformate bzw. -schemas für die Bildklassifizierung mit mehreren Klassen, die Bildklassifizierung mit mehreren Beschriftungen, die Objekterkennung und die Instanzsegmentierung beschrieben. Wir stellen auch eine JSON Lines-Beispieldatei für das abschließende Training bzw. die Validierung zur Verfügung.

### <a name="image-classification-binarymulti-class"></a>Bildklassifizierung (binär/mehrere Klassen)

**Eingabedatenformat/-schema in jeder JSON Lines-Datei**:
```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":"class_name",
}
```

| Schlüssel       | BESCHREIBUNG  | Beispiel |
| -------- |----------|-----|
| `image_url` | Bildspeicherort im AML-Datenspeicher<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| `image_details` | Bilddetails<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| `format`  | Bildtyp (alle verfügbaren Bildformate in der [Pillow](https://pillow.readthedocs.io/en/stable/releasenotes/8.0.1.html)-Bibliothek werden unterstützt)<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif","bmp", "tif", "tiff"}`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| `width` | Bildbreite<br>`Optional, String or Positive Integer`  | `"400px" or 400`|
| `height` | Bildhöhe<br>`Optional, String or Positive Integer` | `"200px" or 200` |
| `label` | Klasse/Beschriftung des Bilds<br>`Required, String` | `"cat"` |


Beispiel einer JSONL-Datei für die Bildklassifizierung mit mehreren Klassen:
```json
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details":{"format": "jpg", "width": "400px", "height": "258px"}, "label": "can"}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "397px", "height": "296px"}, "label": "milk_bottle"}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "1024px", "height": "768px"}, "label": "water_bottle"}
  ```

![Bildbeispiel für die Bildklassifizierung mit mehreren Klassen.](media/reference-automl-images-schema/multiclass-predictions.jpg)

### <a name="image-classification-multi-label"></a>Bildklassifizierung mit mehreren Beschriftungen

Im Folgenden finden Sie ein Beispiel für das Eingabedatenformat/-schema in jeder JSON Lines-Datei für die Bildklassifizierung.


```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":[
      "class_name_1",
      "class_name_2",
      "class_name_3",
      "...",
      "class_name_n"
        
   ]
}
```

| Schlüssel       | BESCHREIBUNG  | Beispiel |
| -------- |----------|-----|
| `image_url` | Bildspeicherort im AML-Datenspeicher<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| `image_details` | Bilddetails<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| `format`  | Bildtyp (alle verfügbaren Bildformate in der [Pillow](https://pillow.readthedocs.io/en/stable/releasenotes/8.0.1.html)-Bibliothek werden unterstützt)<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif", "bmp", "tif", "tiff"}`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| `width` | Bildbreite<br>`Optional, String or Positive Integer`  | `"400px" or 400`|
| `height` | Bildhöhe<br>`Optional, String or Positive Integer` | `"200px" or 200` |
| `label` | Liste der Klassen/Beschriftungen im Bild<br>`Required, List of Strings` | `["cat","dog"]` |


Beispiel für eine JSONL-Datei für die Bildklassifizierung mit mehreren Beschriftungen:

```json
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details":{"format": "jpg", "width": "400px", "height": "258px"}, "label": ["can"]}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "397px", "height": "296px"}, "label": ["can","milk_bottle"]}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "1024px", "height": "768px"}, "label": ["carton","milk_bottle","water_bottle"]}
  ```

![Bildbeispiel für die Bildklassifizierung mit mehreren Beschriftungen.](media/reference-automl-images-schema/multilabel-predictions.jpg)

### <a name="object-detection"></a>Objekterkennung

Im Folgenden finden Sie eine JSONL-Beispieldatei für die Objekterkennung.

```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":[
      {
         "label":"class_name_1",
         "topX":"xmin/width",
         "topY":"ymin/height",
         "bottomX":"xmax/width",
         "bottomY":"ymax/height",
         "isCrowd":"isCrowd"
      },
      {
         "label":"class_name_2",
         "topX":"xmin/width",
         "topY":"ymin/height",
         "bottomX":"xmax/width",
         "bottomY":"ymax/height",
         "isCrowd":"isCrowd"
      },
      "..."
   ]
}
```

Hier gilt Folgendes: 
- `xmin` = x-Koordinate der oberen linken Ecke des Begrenzungsrahmens
- `ymin` = y-Koordinate der oberen linken Ecke des Begrenzungsrahmens
- `xmax` = x-Koordinate der rechten unteren Ecke des Begrenzungsrahmens
- `ymax` = y-Koordinate der rechten unteren Ecke des Begrenzungsrahmens



| Key       | BESCHREIBUNG  | Beispiel |
| -------- |----------|-----|
| `image_url` | Bildspeicherort im AML-Datenspeicher<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| `image_details` | Bilddetails<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| `format`  | Bildtyp (alle verfügbaren Bildformate in der [Pillow](https://pillow.readthedocs.io/en/stable/releasenotes/8.0.1.html)-Bibliothek werden unterstützt, für YOLO werden jedoch nur durch [opencv](https://pypi.org/project/opencv-python/4.3.0.36/) erlaubte Bildformate unterstützt)<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif", "bmp", "tif", "tiff"}`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| `width` | Bildbreite<br>`Optional, String or Positive Integer`  | `"499px" or 499`|
| `height` | Bildhöhe<br>`Optional, String or Positive Integer` | `"665px" or 665` |
| `label` (äußerer Schlüssel) | Liste der Begrenzungsrahmen, wobei jeder Rahmen ein `label, topX, topY, bottomX, bottomY, isCrowd`-Wörterbuch der Koordinaten für die obere linke und die untere rechte Ecke ist<br>`Required, List of dictionaries` | `[{"label": "cat", "topX": 0.260, "topY": 0.406, "bottomX": 0.735, "bottomY": 0.701, "isCrowd": 0}]` |
| `label` (innerer Schlüssel)| Klasse/Beschriftung des Objekts im Begrenzungsrahmen<br>`Required, String` | `"cat"` |
| `topX` | Verhältnis zwischen der x-Koordinate der oberen linken Ecke des Begrenzungsrahmens und der Breite des Bilds<br>`Required, Float in the range [0,1]` | `0.260` |
| `topY` | Verhältnis zwischen der y-Koordinate der oberen linken Ecke des Begrenzungsrahmens und der Höhe des Bilds<br>`Required, Float in the range [0,1]` | `0.406` |
| `bottomX` | Verhältnis zwischen der x-Koordinate der unteren rechten Ecke des Begrenzungsrahmens und der Breite des Bilds<br>`Required, Float in the range [0,1]` | `0.735` |
| `bottomY` | Verhältnis zwischen der y-Koordinate der unteren rechten Ecke des Begrenzungsrahmens und der Höhe des Bilds<br>`Required, Float in the range [0,1]` | `0.701` |
| `isCrowd` | Gibt an, ob sich der Begrenzungsrahmen um die Hauptmenge an Objekten befindet; Wenn dieses spezielle Flag festgelegt ist, wird dieser Begrenzungsrahmen beim Berechnen der Metrik übersprungen.<br>`Optional, Bool` | `0` |


Beispiel für eine JSONL-Datei für die Objekterkennung:

```json
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "can", "topX": 0.260, "topY": 0.406, "bottomX": 0.735, "bottomY": 0.701, "isCrowd": 0}]}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "carton", "topX": 0.172, "topY": 0.153, "bottomX": 0.432, "bottomY": 0.659, "isCrowd": 0}, {"label": "milk_bottle", "topX": 0.300, "topY": 0.566, "bottomX": 0.891, "bottomY": 0.735, "isCrowd": 0}]}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "carton", "topX": 0.0180, "topY": 0.297, "bottomX": 0.380, "bottomY": 0.836, "isCrowd": 0}, {"label": "milk_bottle", "topX": 0.454, "topY": 0.348, "bottomX": 0.613, "bottomY": 0.683, "isCrowd": 0}, {"label": "water_bottle", "topX": 0.667, "topY": 0.279, "bottomX": 0.841, "bottomY": 0.615, "isCrowd": 0}]}
```

![Bildbeispiel für die Objekterkennung.](media/reference-automl-images-schema/object-detect-predictions.jpg)

### <a name="instance-segmentation"></a>Instanzsegmentierung

Zur Segmentierung von Instanzen unterstützt das automatisierte maschinelle Lernen nur Polygone als Ein- und Ausgabe, keine Masken.

Im Folgenden finden Sie eine JSONL-Beispieldatei für die Instanzsegmentierung.

```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":[
      {
         "label":"class_name",
         "isCrowd":"isCrowd",
         "polygon":[["x1", "y1", "x2", "y2", "x3", "y3", "...", "xn", "yn"]]
      }
   ]
}
```

| Schlüssel       | BESCHREIBUNG  | Beispiel |
| -------- |----------|-----|
| `image_url` | Bildspeicherort im AML-Datenspeicher<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| `image_details` | Bilddetails<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| `format`  | Imagetyp<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif", "bmp", "tif", "tiff" }`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| `width` | Bildbreite<br>`Optional, String or Positive Integer`  | `"499px" or 499`|
| `height` | Bildhöhe<br>`Optional, String or Positive Integer` | `"665px" or 665` |
| `label` (äußerer Schlüssel) | Liste der Masken, wobei jede Maske ein Wörterbuch aus `label, isCrowd, polygon coordinates` ist <br>`Required, List of dictionaries` | ` [{"label": "can", "isCrowd": 0, "polygon": [[0.577, 0.689,`<br> ` 0.562, 0.681,`<br> `0.559, 0.686]]}]` |
| `label` (innerer Schlüssel)| Klasse/Beschriftung des Objekts in der Maske<br>`Required, String` | `"cat"` |
| `isCrowd` | Gibt an, ob sich die Maske um die Hauptmenge an Objekten befindet<br>`Optional, Bool` | `0` |
| `polygon` | Polygonkoordinaten für das Objekt<br>`Required,  List of list for multiple segments of the same instance. Float values in the range [0,1]` | ` [[0.577, 0.689, 0.567, 0.689, 0.559, 0.686]]` |


Beispiel für eine JSONL-Datei für Instanzsegmentierung:

```python
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "can", "isCrowd": 0, "polygon": [[0.577, 0.689, 0.567, 0.689, 0.559, 0.686, 0.380, 0.593, 0.304, 0.555, 0.294, 0.545, 0.290, 0.534, 0.274, 0.512, 0.2705, 0.496, 0.270, 0.478, 0.284, 0.453, 0.308, 0.432, 0.326, 0.423, 0.356, 0.415, 0.418, 0.417, 0.635, 0.493, 0.683, 0.507, 0.701, 0.518, 0.709, 0.528, 0.713, 0.545, 0.719, 0.554, 0.719, 0.579, 0.713, 0.597, 0.697, 0.621, 0.695, 0.629, 0.631, 0.678, 0.619, 0.683, 0.595, 0.683, 0.577, 0.689]]}]}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "carton", "isCrowd": 0, "polygon": [[0.240, 0.65, 0.234, 0.654, 0.230, 0.647, 0.210, 0.512, 0.202, 0.403, 0.182, 0.267, 0.184, 0.243, 0.180, 0.166, 0.186, 0.159, 0.198, 0.156, 0.396, 0.162, 0.408, 0.169, 0.406, 0.217, 0.414, 0.249, 0.422, 0.262, 0.422, 0.569, 0.342, 0.569, 0.334, 0.572, 0.320, 0.585, 0.308, 0.624, 0.306, 0.648, 0.240, 0.657]]}, {"label": "milk_bottle",  "isCrowd": 0, "polygon": [[0.675, 0.732, 0.635, 0.731, 0.621, 0.725, 0.573, 0.717, 0.516, 0.717, 0.505, 0.720, 0.462, 0.722, 0.438, 0.719, 0.396, 0.719, 0.358, 0.714, 0.334, 0.714, 0.322, 0.711, 0.312, 0.701, 0.306, 0.687, 0.304, 0.663, 0.308, 0.630, 0.320, 0.596, 0.32, 0.588, 0.326, 0.579]]}]}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "water_bottle", "isCrowd": 0, "polygon": [[0.334, 0.626, 0.304, 0.621, 0.254, 0.603, 0.164, 0.605, 0.158, 0.602, 0.146, 0.602, 0.142, 0.608, 0.094, 0.612, 0.084, 0.599, 0.080, 0.585, 0.080, 0.539, 0.082, 0.536, 0.092, 0.533, 0.126, 0.530, 0.132, 0.533, 0.144, 0.533, 0.162, 0.525, 0.172, 0.525, 0.186, 0.521, 0.196, 0.521 ]]}, {"label": "milk_bottle", "isCrowd": 0, "polygon": [[0.392, 0.773, 0.380, 0.732, 0.379, 0.767, 0.367, 0.755, 0.362, 0.735, 0.362, 0.714, 0.352, 0.644, 0.352, 0.611, 0.362, 0.597, 0.40, 0.593, 0.444,  0.494, 0.588, 0.515, 0.585, 0.621, 0.588, 0.671, 0.582, 0.713, 0.572, 0.753 ]]}]}
```

![Bildbeispiel für die Instanzsegmentierung.](media/reference-automl-images-schema/instance-segmentation-predictions.jpg)

## <a name="data-format-for-inference"></a>Datenformat für Rückschlüsse

In diesem Abschnitt wird das Eingabedatenformat dokumentiert, das bei Verwendung eines bereitgestellten Modells für Vorhersagen erforderlich ist. Alle oben genannten Bildformate werden mit dem Inhaltstyp `application/octet-stream` akzeptiert.

### <a name="input-format"></a>Eingabeformat

Im Folgenden finden Sie das Eingabeformat, das zum Generieren von Vorhersagen für eine beliebige Aufgabe mithilfe eines aufgabenspezifischen Modellendpunkts erforderlich ist. Nach dem [Bereitstellen des Modells](how-to-auto-train-image-models.md#register-and-deploy-model) können wir den folgenden Codeausschnitt verwenden, um Vorhersagen für alle Aufgaben abzurufen.

```python
# input image for inference
sample_image = './test_image.jpg'
# load image data
data = open(sample_image, 'rb').read()
# set the content type
headers = {'Content-Type': 'application/octet-stream'}
# if authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'
# make the request and display the response
response = requests.post(scoring_uri, data, headers=headers)
```
### <a name="output-format"></a>Ausgabeformat

Vorhersagen für Modellendpunkte folgen je nach Aufgabentyp einer unterschiedlichen Struktur. In diesem Abschnitt werden die Ausgabedatenformate für folgende Aufgaben erläutert: Bildklassifizierungen mit mehreren Klassen, Bildklassifizierungen mit mehreren Beschriftungen, Objekterkennung und Instanzsegmentierung.  

#### <a name="image-classification"></a>Bildklassifizierung

Der Endpunkt für die Bildklassifizierung gibt alle Beschriftungen im Dataset und deren Wahrscheinlichkeitsbewertungen für das Eingabebild im folgenden Format zurück.

```json
{
   "filename":"/tmp/tmppjr4et28",
   "probs":[
      2.098e-06,
      4.783e-08,
      0.999,
      8.637e-06
   ],
   "labels":[
      "can",
      "carton",
      "milk_bottle",
      "water_bottle"
   ]
}
```

#### <a name="image-classification-multi-label"></a>Bildklassifizierung mit mehreren Beschriftungen

Für die Bildklassifizierung mit mehreren Beschriftungen gibt der Modellendpunkt Beschriftungen und deren Wahrscheinlichkeiten zurück.

```json
{
   "filename":"/tmp/tmpsdzxlmlm",
   "probs":[
      0.997,
      0.960,
      0.982,
      0.025
   ],
   "labels":[
      "can",
      "carton",
      "milk_bottle",
      "water_bottle"
   ]
}
```

#### <a name="object-detection"></a>Objekterkennung

Das Objekterkennungsmodell gibt mehrere Rahmen mit ihren skalierten Koordinaten für die obere linke und die untere rechte Ecke zusammen mit der Rahmenbeschriftung und einer Konfidenzbewertung zurück.

```json
{
   "filename":"/tmp/tmpdkg2wkdy",
   "boxes":[
      {
         "box":{
            "topX":0.224,
            "topY":0.285,
            "bottomX":0.399,
            "bottomY":0.620
         },
         "label":"milk_bottle",
         "score":0.937
      },
      {
         "box":{
            "topX":0.664,
            "topY":0.484,
            "bottomX":0.959,
            "bottomY":0.812
         },
         "label":"can",
         "score":0.891
      },
      {
         "box":{
            "topX":0.423,
            "topY":0.253,
            "bottomX":0.632,
            "bottomY":0.725
         },
         "label":"water_bottle",
         "score":0.876
      }
   ]
}
```
#### <a name="instance-segmentation"></a>Instanzsegmentierung

Bei der Instanzsegmentierung besteht die Ausgabe aus mehreren Rahmen mit skalierten Koordinaten für die obere linke und die untere rechte Ecke, Beschriftungen, Konfidenzbewertungen und Polygonen (keine Masken). Hier haben die Polygonwerte das gleiche Format, das im Abschnitt „Schema“ erläutert wurde.

```json
{
   "filename":"/tmp/tmpi8604s0h",
   "boxes":[
      {
         "box":{
            "topX":0.679,
            "topY":0.491,
            "bottomX":0.926,
            "bottomY":0.810
         },
         "label":"can",
         "score":0.992,
         "polygon":[
            [
               0.82, 0.811, 0.771, 0.810, 0.758, 0.805, 0.741, 0.797, 0.735, 0.791, 0.718, 0.785, 0.715, 0.778, 0.706, 0.775, 0.696, 0.758, 0.695, 0.717, 0.698, 0.567, 0.705, 0.552, 0.706, 0.540, 0.725, 0.520, 0.735, 0.505, 0.745, 0.502, 0.755, 0.493
            ]
         ]
      },
      {
         "box":{
            "topX":0.220,
            "topY":0.298,
            "bottomX":0.397,
            "bottomY":0.601
         },
         "label":"milk_bottle",
         "score":0.989,
         "polygon":[
            [
               0.365, 0.602, 0.273, 0.602, 0.26, 0.595, 0.263, 0.588, 0.251, 0.546, 0.248, 0.501, 0.25, 0.485, 0.246, 0.478, 0.245, 0.463, 0.233, 0.442, 0.231, 0.43, 0.226, 0.423, 0.226, 0.408, 0.234, 0.385, 0.241, 0.371, 0.238, 0.345, 0.234, 0.335, 0.233, 0.325, 0.24, 0.305, 0.586, 0.38, 0.592, 0.375, 0.598, 0.365
            ]
         ]
      },
      {
         "box":{
            "topX":0.433,
            "topY":0.280,
            "bottomX":0.621,
            "bottomY":0.679
         },
         "label":"water_bottle",
         "score":0.988,
         "polygon":[
            [
               0.576, 0.680, 0.501, 0.680, 0.475, 0.675, 0.460, 0.625, 0.445, 0.630, 0.443, 0.572, 0.440, 0.560, 0.435, 0.515, 0.431, 0.501, 0.431, 0.433, 0.433, 0.426, 0.445, 0.417, 0.456, 0.407, 0.465, 0.381, 0.468, 0.327, 0.471, 0.318
            ]
         ]
      }
   ]
}
```

> [!NOTE]
> Die in diesem Artikel verwendeten Bilder stammen aus dem Dataset „Fridge Objects“ (Copyright © Microsoft Corporation) und stehen über [computervision-recipes/01_training_introduction.ipynb](https://github.com/microsoft/computervision-recipes/blob/master/scenarios/detection/01_training_introduction.ipynb) unter der [MIT-Lizenz](https://github.com/microsoft/computervision-recipes/blob/master/LICENSE) zur Verfügung. 

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Daten für das Training von Modellen für maschinelles Sehen mit automatisiertem maschinellen Lernen vorbereiten](how-to-prepare-datasets-for-automl-images.md).
