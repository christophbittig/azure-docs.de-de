---
title: Der Skill „OCR“
titleSuffix: Azure Cognitive Search
description: Extrahieren von Text aus Bilddateien mithilfe der optischen Zeichenerkennung (OCR) in einer KI-Anreicherungspipeline in der kognitiven Azure-Suche.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: b0ed1860abe3d8a85e87e2bf2746812e1ca67d2e
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349832"
---
# <a name="ocr-cognitive-skill"></a>Der Skill „OCR“

Mit dem Skill für die **optische Zeichenerkennung (OCR)** wird gedruckter und handschriftlicher Text in Bilddateien erkannt. Dieser Skill verwendet die Machine Learning-Modelle, die durch [v3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) der [Maschinelles Sehen](../cognitive-services/computer-vision/overview.md)-API in Cognitive Services bereitgestellt werden. Die Qualifikation **OCR** ist den folgenden Funktionen zugeordnet:

+ Für Englisch, Spanisch, Deutsch Französisch, Italienisch, Portugiesisch und Niederländisch wird die neue [Lese](../cognitive-services/computer-vision/overview-ocr.md#read-api)-API verwendet.
+ Für alle anderen Sprachen wird die [Legacy-OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20d)-API verwendet.

Der Skill **OCR** extrahiert Text aus Bilddateien. Folgende Dateiformate werden unterstützt:

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF
+ .TIFF

> [!NOTE]
> Dieser Skill ist an Cognitive Services gebunden und erfordert [eine abrechenbare Ressource](cognitive-search-attach-cognitive-services.md) für Transaktionen, die 20 Dokumente pro Indexer und Tag überschreiten. Die Ausführung integrierter Qualifikationen wird nach dem bestehenden [nutzungsbasierten Preis für Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) berechnet.
> 
> Darüber hinaus wird die Bildextraktion [durch Azure Cognitive Search abgerechnet](https://azure.microsoft.com/pricing/details/search/).
>

## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Parametername     | BESCHREIBUNG |
|--------------------|-------------|
| `detectOrientation`    | Aktiviert die automatische Erkennung der Bildausrichtung. <br/> Gültige Werte: „true“ und „false“|
| `defaultLanguageCode` | <p>    Sprachcode des Eingabetexts. Unterstützte Sprachen: <br/> zh-Hans (Vereinfachtes Chinesisch) <br/> zh-Hant (Traditionelles Chinesisch) <br/>cs (Tschechisch) <br/>da (Dänisch) <br/>nl (Niederländisch) <br/>en (Englisch) <br/>fi (Finnisch)  <br/>fr (Französisch) <br/>  de (Deutsch) <br/>el (Griechisch) <br/> hu (Ungarisch) <br/> it (Italienisch) <br/>  ja (Japanisch) <br/> ko (Koreanisch) <br/> nb (Norwegisch) <br/>   pl (Polnisch) <br/> pt (Portugiesisch) <br/>  ru (Russisch) <br/>  es (Spanisch) <br/>  sv (Schwedisch) <br/>  tr (Türkisch) <br/> ar (Arabisch) <br/> ro (Rumänisch) <br/> sr-Cyrl (Serbisch, kyrillisch) <br/> sr-Latn (Serbisch, lateinisch) <br/>  sk (Slowakisch) <br/>  unk (Unbekannt) <br/><br/> Wenn der Sprachcode nicht angegeben oder Null ist, wird als Sprache automatisch Englisch festgelegt. Wenn die Sprache explizit auf „unk“ festgelegt ist, wird die Sprache automatisch erkannt. </p> |
| `lineEnding` | Der Wert, der zwischen jeder erkannten Zeile verwendet werden soll. Mögliche Werte: "Space", "CarriageReturn", "LineFeed".  Der Standardwert ist "Space". |

Bislang konnte mithilfe des Parameters „textExtractionAlgorithm“ angegeben werden, ob der Skill gedruckten Text (printed) oder handschriftlichen Text (handwritten) extrahieren soll.  Dieser Parameter ist inzwischen veraltet und nicht mehr erforderlich, da der neueste Algorithmus der Read-API beide Arten von Text gleichzeitig extrahieren kann.  Falls Ihr Skill diesen Parameter bereits enthält, müssen Sie ihn nicht entfernen. Er wird jedoch nicht mehr verwendet, und es werden beide Arten von Text extrahiert – unabhängig davon, auf welchen Wert der Parameter festgelegt ist.

## <a name="skill-inputs"></a>Skilleingaben

| Eingabename      | BESCHREIBUNG                                          |
|---------------|------------------------------------------------------|
| `image`         | Komplexer Typ. Arbeitet derzeit mit dem Feld „/document/normalized_images“, das vom Azure Blob-Indexer generiert wird, wenn ```imageAction``` auf einen anderen Wert als ```none``` gesetzt ist. Weitere Informationen finden Sie im [Beispiel](#sample-output).|


## <a name="skill-outputs"></a>Skillausgaben
| Ausgabename      | BESCHREIBUNG                   |
|---------------|-------------------------------|
| `text`             | Aus dem Bild extrahierter Nur-Text-Inhalt.   |
| `layoutText`    | Komplexer Typ, der den extrahierten Text und die Fundstelle beschreibt.|


## <a name="sample-definition"></a>Beispieldefinition

```json
{
  "skills": [
    {
      "description": "Extracts text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": null,
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text",
          "targetName": "myText"
        },
        {
          "name": "layoutText",
          "targetName": "myLayoutText"
        }
      ]
    }
  ]
}
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Beispieltext und „layoutText“-Ausgabe

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Beispiel: Text, der aus eingebetteten Bildern extrahiert wurde, wird mit dem Inhalt des Dokuments zusammengeführt.

Ein häufiger Anwendungsfall für die Textzusammenführung ist die Möglichkeit, die Textdarstellung von Bildern (Text aus einem OCR-Skill oder der Titel eines Bildes) in das Inhaltsfeld eines Dokuments einzubinden.

Mit der folgenden Beispielqualifikationsgruppe wird das Feld mit der Bezeichnung *merged_text* erstellt. Dieses Feld enthält den Textinhalt des Dokuments und den per OCR erkannten Text aus den einzelnen im Dokument eingebetteten Bildern.

#### <a name="request-body-syntax"></a>Syntax des Anforderungstexts
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
Im oben gezeigten Beispiel für das Skillset wird davon ausgegangen, dass ein Feld mit normalisierten Bildern vorhanden ist. Um ein Feld zu erhalten, legen Sie die Konfiguration *imageAction* in Ihrer Indexerdefinition auf *generateNormalizedImages* fest, wie unten gezeigt:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Weitere Informationen

+ [Was ist optische Zeichenerkennung?](../cognitive-services/computer-vision/overview-ocr.md)
+ [Integrierte Qualifikationen](cognitive-search-predefined-skills.md)
+ [Der Skill „Text zusammenführen“](cognitive-search-skill-textmerger.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Erstellen eines Indexers (REST)](/rest/api/searchservice/create-indexer)
