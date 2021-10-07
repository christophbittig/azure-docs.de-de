---
title: Extrahieren von Text aus Bildern
titleSuffix: Azure Cognitive Search
description: Verarbeiten und Extrahieren von Text und anderen Informationen aus Bildern in Pipelines der kognitiven Azure-Suche.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: be0dc81d20bf62eb0033691e5d4eac5f406a7c0f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214616"
---
# <a name="extract-text-and-information-from-images-in-ai-enrichment-scenarios"></a>Extrahieren von Text und Informationen aus Bildern in KI-Anreicherungsszenarios

Die kognitive Azure-Suche umfasst mehrere Funktionen für die Verarbeitung von Bildern und Bilddateien. Im Rahmen der [Dokumententschlüsselung](search-indexer-overview.md#document-cracking) können Sie den Parameter *imageAction* verwenden, um alphanumerischen Text (beispielsweise das Wort „Stopp“ eines Stopp-Schilds) aus Fotos oder Bildern zu extrahieren. Ein anderes Szenario wäre etwa die Generierung einer Textdarstellung eines Bilds – etwa „Löwenzahn“ (oder die Farbe „Gelb“) für ein Foto eines Löwenzahns. Des Weiteren können Sie Metadaten des Bilds extrahieren (beispielsweise die Größe).

Dieser Artikel geht ausführlicher auf die Bildverarbeitung ein und enthält eine Anleitung für die Verwendung von Bildern in einer KI-Anreicherungspipeline.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Generieren normalisierter Bilder

Im Rahmen der Dokumentaufschlüsselung steht ein neuer Satz von Indexerkonfigurationsparametern für die Behandlung von Bilddateien oder von Bildern zur Verfügung, die in Dateien eingebettet sind. Diese Parameter dienen zur Normalisierung von Bildern für die Weiterverarbeitung. Die Normalisierung dient zur Generierung einheitlicherer Bilder. Große Bilder werden auf eine maximale Höhe und Breite festgelegt, um sie nutzbar zu machen. Bei Bildern mit Metadaten zur Ausrichtung wird die Drehung angepasst, um vertikales Laden zu ermöglichen. Metadatenanpassungen werden in einem komplexen Typ erfasst, der für jedes Bild erstellt wird. 

Die Bildnormalisierung kann nicht deaktiviert werden. Qualifikationen, die Bilder durchlaufen, erwarten normalisierte Bilder. Das Aktivieren der Imagenormalisierung für einen Indexer erfordert, dass diesem Indexer ein Skillset angefügt wird.

| Konfigurationsparameter | BESCHREIBUNG |
|--------------------|-------------|
| imageAction   | Legen Sie diese Eigenschaft auf „none“ fest, falls bei der Erkennung von eingebetteten Bildern oder Bilddateien keine Aktion erfolgen soll. <br/>Wenn Sie die Eigenschaft auf „generateNormalizedImages“ festlegen, wird im Rahmen der Dokumentaufschlüsselung ein Array mit normalisierten Bildern generiert.<br/>Legen Sie die Eigenschaft auf „generateNormalizedImagePerPage“ fest, um ein Array von normalisierten Bildern zu generieren, bei dem für PDFs in der Datenquelle jede Seite in ein Ausgabebild gerendert wird.  Die Funktionalität ist die gleiche wie bei „generateNormalizedImages“ für Nicht-PDF-Dateitypen.<br/>Für alle anderen Optionen als „none“ werden diese Bilder im Feld *normalized_images* verfügbar gemacht. <br/>Der Standardwert ist „none“. Diese Konfiguration ist nur für Blobdatenquellen relevant, wenn „dataToExtract" auf „contentAndMetadata“ festgelegt ist. <br/>Maximal 1000 Bilder werden aus einem angegebenen Dokument extrahiert. Wenn in einem Dokument mehr als 1000 Bilder vorhanden sind, werden die ersten 1000 extrahiert, und eine Warnung wird generiert. |
|  normalizedImageMaxWidth | Die maximale Breite (in Pixel) für generierte normalisierte Bilder. Der Standardwert ist „2000“. Der zulässige Höchstwert ist 10.000. | 
|  normalizedImageMaxHeight | Die maximale Höhe (in Pixel) für generierte normalisierte Bilder. Der Standardwert ist „2000“. Der zulässige Höchstwert ist 10.000.|

> [!NOTE]
> Wenn Sie die Eigenschaft *imageAction* auf einen anderen Wert als „none“ festlegen, kann die Eigenschaft *parsingMode* nur auf „default“ festgelegt werden.  In der Indexerkonfiguration kann immer nur eine dieser beiden Eigenschaften auf einen standardfremden Wert festgelegt werden.

Legen Sie den Parameter **parsingMode** auf `json` (zum Indizieren jedes Blob als einzelnes Dokument) oder `jsonArray` (wenn die Blobs JSON-Arrays enthalten und jedes Element eines Arrays als separates Dokument behandelt werden soll) fest.

Der Standardwert von 2.000 Pixeln für die maximale Breite und Höhe der normalisierten Bilder basiert auf der maximal unterstützten Größe der [OCR-Qualifikation](cognitive-search-skill-ocr.md) und der [Bildanalysequalifikation](cognitive-search-skill-image-analysis.md). Die [OCR-Qualifikation](cognitive-search-skill-ocr.md) unterstützt eine maximale Breite und Höhe von 4.200 für nicht englische Sprachen und 10.000 für Englisch.  Wenn Sie die maximalen Grenzwerte erhöhen, können bei größeren Images je nach Skillsetdefinition und Sprache der Dokumente Fehler bei der Verarbeitung auftreten. 

Die imageAction-Eigenschaft wird in der [Indexerdefinition](/rest/api/searchservice/create-indexer) wie folgt angegeben:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
    }
  }
}
```

Wenn *imageAction* auf einen anderen Wert als „none“ festgelegt wird, enthält das neue Feld *normalized_images* ein Array von Bildern. Jedes Bild ist ein komplexer Typ mit folgenden Elementen:

| Bildelement       | BESCHREIBUNG                             |
|--------------------|-----------------------------------------|
| data               | Base64-codierte Zeichenfolge mit dem normalisierten Bild im JPEG-Format.   |
| width              | Breite des normalisierten Bilds (in Pixel). |
| height             | Höhe des normalisierten Bilds (in Pixel). |
| originalWidth      | Ursprüngliche Breite des Bilds vor der Normalisierung. |
| originalHeight      | Ursprüngliche Höhe des Bilds vor der Normalisierung. |
| rotationFromOriginal |  Drehung gegen den Uhrzeigersinn zur Erstellung des normalisierten Bilds (in Grad). Der Wert muss zwischen 0 und 360 Grad liegen. In diesem Schritt werden die durch eine Kamera oder einen Scanner generierten Metadaten aus dem Bild gelesen. Der Wert ist in der Regel ein Vielfaches von 90 Grad. |
| contentOffset | Das Zeichenoffset in dem Inhaltsfeld, aus dem das Bild extrahiert wurde. Dieses Feld ist nur für Dateien mit eingebetteten Bildern relevant. |
| pageNumber | Wenn das Bild aus einer PDF-Datei extrahiert oder gerendert wurde, enthält dieses Feld die Seitenzahl in der PDF-Datei, aus der es extrahiert oder gerendert wurde, beginnend bei 1.  Wenn das Bild nicht aus einer PDF-Datei stammt, ist dieses Feld 0.  |

 Beispielwert von *normalized_images*:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500,
    "pageNumber": 2
  }
]
```

## <a name="image-related-skills"></a>Skills für Bilder

Wenn Sie Bilder als Eingaben akzeptieren möchten, können Sie einen benutzerdefinierten Skill oder integrierte Skills verwenden. Zu den integrierten Skills gehören [OCR](cognitive-search-skill-ocr.md) und [Bildanalyse](cognitive-search-skill-image-analysis.md). 

Momentan können diese Qualifikationen nur für Bilder verwendet werden, die durch den Dokumentaufschlüsselungsschritt generiert wurden. Daher wird als Eingabe ausschließlich `"/document/normalized_images"` unterstützt.

### <a name="image-analysis-skill"></a>Bildanalysequalifikation

Die [Bildanalysequalifikation](cognitive-search-skill-image-analysis.md) extrahiert anhand des Bildinhalts einen umfangreichen Satz von optischen Merkmalen. So können Sie beispielsweise anhand eines Bilds eine Beschriftung erstellen, Tags generieren oder Prominente und Wahrzeichen identifizieren.

### <a name="ocr-skill"></a>OCR-Qualifikation

Die [OCR-Qualifikation](cognitive-search-skill-ocr.md) extrahiert Text aus Bilddateien – etwa aus JPG-, PNG- und Bitmap-Dateien. Sie kann sowohl Text als auch Layoutinformationen extrahieren. Die Layoutinformationen bieten umgebende Rechtecke für die jeweils erkannten Zeichenfolgen.

### <a name="custom-skills"></a>Benutzerdefinierte Qualifikationen

Bilder können auch an benutzerdefinierte Skills übergeben und von benutzerdefinierten Skills zurückgegeben werden. Das Skillset codiert das Image, das an den benutzerdefinierten Skill übergeben wird, mit Base64. Um das Image innerhalb des benutzerdefinierten Skills zu verwenden, legen Sie `/document/normalized_images/*/data` als Eingabe für den benutzerdefinierten Skill fest. Decodieren Sie in dem Code des benutzerdefinierten Skills die Zeichenfolge mit Base64, bevor Sie sie in ein Bild konvertieren. Um ein Bild an das Skillset zurückzugeben, codieren Sie es mit Base64, bevor Sie es an das Skillset zurückgeben.

 Das Bild wird als Objekt mit den folgenden Eigenschaften zurückgegeben:

```json
 { 
  "$type": "file", 
  "data": "base64String" 
 }
```

Das Repository mit [Azure Search-Python-Beispielen](https://github.com/Azure-Samples/azure-search-python-samples) enthält ein vollständiges in Python implementiertes Beispiel für einen benutzerdefinierten Skill, der Bilder anreichert.

## <a name="embedded-image-scenario"></a>Szenario mit eingebettetem Bild

Ein gängiges Szenario ist die Erstellung einer einzelnen Zeichenfolge mit sämtlichen Dateiinhalten (sowohl Text als auch Bildursprungstext):  

1. [Extrahieren Sie „normalized_images“.](#get-normalized-images)
1. Führen Sie die OCR-Qualifikation mit `"/document/normalized_images"` als Eingabe aus.
1. Führen Sie die Textdarstellung dieser Bilder mit dem unformatierten Text aus der Datei zusammen. Die beiden Textblöcke können mithilfe der [Textzusammenführungsqualifikation](cognitive-search-skill-textmerger.md) in einer einzelnen umfangreichen Zeichenfolge konsolidiert werden.

Die folgende Beispielqualifikationsgruppe erstellt ein Feld namens *merged_text* mit dem Textinhalt Ihres Dokuments. Darüber hinaus enthält sie den per OCR erkannten Text aus den einzelnen eingebetteten Bildern. 

### <a name="request-body-syntax"></a>Syntax des Anforderungstexts

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "description": "Extract text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": "en",
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text"
          }
        ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```

Das Feld „merged_text“ kann in der Indexerdefinition als durchsuchbares Feld zugeordnet werden. Der gesamte Inhalt Ihrer Dateien – einschließlich des Texts der Bilder – wird somit durchsuchbar.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Visualisieren von umgebenden Rechtecken für extrahierten Text

Ein weiteres gängiges Szenario ist die Visualisierung der Layoutinformationen von Suchergebnissen. So können Sie beispielsweise in Ihren Suchergebnissen den Ort in einem Bild hervorheben, an dem ein Textelement gefunden wurde.

Da der OCR-Schritt auf der Grundlage der normalisierten Bilder erfolgt, befinden sich die Layoutkoordinaten im normalisierten Bildraum. Bei der Anzeige des normalisierten Bilds ist das Vorhandensein von Koordinaten in der Regel kein Problem. In bestimmten Situationen möchten Sie jedoch möglicherweise das Originalbild anzeigen. Konvertieren Sie in diesem Fall die einzelnen Koordinatenpunkte aus dem Layout in das Koordinatensystem des Originalbilds. 

Um die normalisierten Koordinaten auf den ursprünglichen Koordinatenraum zu übertragen, können Sie den folgenden Algorithmus verwenden:

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```

## <a name="passing-images-to-custom-skills"></a>Übergeben von Bildern an benutzerdefinierte Skills

Für Szenarios, in denen Sie einen benutzerdefinierten Skill für Bilder erfordern, können Sie Bilder an den benutzerdefinierten Skill übergeben, damit dieser Text oder Bilder zurückgibt. Die Bildverarbeitung des [Python-Beispiels](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Image-Processing) veranschaulicht den Workflow. Das folgende Skillset stammt aus dem Beispiel.

Das folgende Skillset erfasst das normalisierte Bild (das bei der Dokumententschlüsselung abgerufen wird) und gibt Segmente des Bilds aus.

### <a name="sample-skillset"></a>Beispielskillset

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
          "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
          "name": "ImageSkill",
          "description": "Segment Images",
          "context": "/document/normalized_images/*",
          "uri": "https://your.custom.skill.url",
          "httpMethod": "POST",
          "timeout": "PT30S",
          "batchSize": 100,
          "degreeOfParallelism": 1,
          "inputs": [
            {
              "name": "image",
              "source": "/document/normalized_images/*"
            }
          ],
          "outputs": [
            {
              "name": "slices",
              "targetName": "slices"
            }
          ],
          "httpHeaders": {}
        }
  ]
}
```

### <a name="custom-skill"></a>Benutzerdefinierter Skill

Der benutzerdefinierte Skill selbst ist extern vom Skillset. In diesem Fall durchläuft der Python-Code zunächst den Batch der Anforderungsaufzeichnung im Format für den benutzerdefinierten Skill und konvertiert dann die mit Base64 verschlüsselte Zeichenfolge in ein Bild.

```python
# deserialize the request, for each item in the batch
for value in values:
  data = value['data']
  base64String = data["image"]["data"]
  base64Bytes = base64String.encode('utf-8')
  inputBytes = base64.b64decode(base64Bytes)
  # Use numpy to convert the string to an image
  jpg_as_np = np.frombuffer(inputBytes, dtype=np.uint8)
  # you now have an image to work with
```

Ähnlich gilt, wenn Sie ein Bild zurückgeben möchten, geben Sie eine mit Base64 verschlüsselte Zeichenfolge in einem JSON-Objekt mit der `$type`-Eigenschaft `file` zurück.

```python
def base64EncodeImage(image):
    is_success, im_buf_arr = cv2.imencode(".jpg", image)
    byte_im = im_buf_arr.tobytes()
    base64Bytes = base64.b64encode(byte_im)
    base64String = base64Bytes.decode('utf-8')
    return base64String

 base64String = base64EncodeImage(jpg_as_np)
 result = { 
  "$type": "file", 
  "data": base64String 
}
```

## <a name="see-also"></a>Weitere Informationen

+ [Create Indexer (Azure Search Service REST api-version=2017-11-11-Preview)](/rest/api/searchservice/create-indexer) (Erstellen eines Indexers (REST-API für den Azure Search-Dienst: Version 2017-11-11-Preview))
+ [Skill für Bildanalyse](cognitive-search-skill-image-analysis.md)
+ [OCR-Qualifikation](cognitive-search-skill-ocr.md)
+ [Text Merge cognitive skill](cognitive-search-skill-textmerger.md) (Kognitive Qualifikation: Textzusammenführung)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Zuordnen angereicherter Felder](cognitive-search-output-field-mapping.md)
+ [Übergeben von Bildern an benutzerdefinierte Skills](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Image-Processing)
