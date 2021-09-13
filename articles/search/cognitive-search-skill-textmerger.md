---
title: Der kognitive Skill „Text zusammenführen“
titleSuffix: Azure Cognitive Search
description: Führen Sie Text aus einer Sammlung von Feldern in einem konsolidierten Feld zusammen. Verwenden Sie diesen kognitiven Skill in einer KI-Anreicherungspipeline in der kognitiven Azure-Suche.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 4ea7681a28cf8f17c53e42e9ad05ddf12b5d2f9b
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122356089"
---
#   <a name="text-merge-cognitive-skill"></a>Der kognitive Skill „Text zusammenführen“

Der Skill **Text zusammenführen** konsolidiert Text aus einer Sammlung von Feldern in einem einzigen Feld. 

> [!NOTE]
> Dieser Skill ist nicht an Cognitive Services gebunden. Er ist nicht gebührenpflichtig und weist keine Cognitive Services-Schlüsselanforderungen auf.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Parametername     | BESCHREIBUNG |
|--------------------|-------------|
| `insertPreTag`    | Zeichenfolge, die vor jedem Einfügen hinzugefügt wird. Standardwert: `" "`. Um das Leerzeichen wegzulassen, setzen Sie den Wert auf `""`.  |
| `insertPostTag`   | Zeichenfolge, die nach jedem Einfügen hinzugefügt wird. Standardwert: `" "`. Um das Leerzeichen wegzulassen, setzen Sie den Wert auf `""`.  |


##  <a name="sample-input"></a>Beispieleingabe
So könnte ein JSON-Dokument aussehen, das hilfreiche Eingabewerte für diesen Skill enthält:

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28]
      }
    }
  ]
}
```

##  <a name="sample-output"></a>Beispielausgabe
Dieses Beispiel zeigt die Ausgabe der vorherigen Eingabe, vorausgesetzt, dass *insertPreTag* auf `" "` und *insertPostTag* auf `""` gesetzt ist. 

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "mergedText": "The quick brown fox jumps over the lazy dog"
      }
    }
  ]
}
```

## <a name="extended-sample-skillset-definition"></a>Erweiterte Beispiel für die Definition eines Skillsets

Ein gängiges Szenario für die Verwendung von „Text zusammenführen“ ist das Zusammenführen der Textdarstellung von Bildern (Text aus einem OCR-Skill oder der Titel eines Bildes) im Inhaltsfeld eines Dokuments. 

Im folgenden Beispiel für ein Skillset wird der OCR-Skill verwendet, um Text aus in das Dokument eingebetteten Bildern zu extrahieren. Als nächstes wird ein Feld *merged_text* erstellt, das sowohl Original- als auch OCR-Text aus jedem Bild enthält. Weitere Informationen zur OCR-Qualifikation finden Sie [hier](./cognitive-search-skill-ocr.md).

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
          "name":"text", 
          "source": "/document/content"
        },
        {
          "name": "itemsToInsert", 
          "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", 
          "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", 
          "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
Im oben gezeigten Beispiel wird davon ausgegangen, dass ein Feld mit normalisierten Bildern vorhanden ist. Um ein Feld mit normalisierten Bildern zu erhalten, legen Sie die Konfiguration *imageAction* in Ihrer Indexerdefinition auf *generateNormalizedImages* fest, wie unten gezeigt:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":{
    "configuration":{
        "dataToExtract":"contentAndMetadata",
        "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Weitere Informationen

+ [Integrierte Qualifikationen](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Erstellen eines Indexers (REST)](/rest/api/searchservice/create-indexer)