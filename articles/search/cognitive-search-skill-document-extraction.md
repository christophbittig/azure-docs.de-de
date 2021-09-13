---
title: Kognitive Qualifikation „Dokumentextrahierung“
titleSuffix: Azure Cognitive Search
description: Extrahiert Inhalt aus einer Datei innerhalb der Anreicherungspipeline.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.author: chalton
ms.openlocfilehash: 02d3431ecc7a5c460be75885fd786b3b4c4d276f
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340353"
---
# <a name="document-extraction-cognitive-skill"></a>Kognitive Qualifikation „Dokumentextrahierung“

Die Qualifikation **Dokumentextrahierung** extrahiert Inhalt aus einer Datei innerhalb der Anreicherungspipeline. Auf diese Weise können Sie die Vorteile des Schritts der Dokumentextrahierung nutzen, der normalerweise vor der Ausführung des Skillsets mit Dateien erfolgt, die möglicherweise durch andere Qualifikationen generiert werden.

> [!NOTE]
> Dieser Skill ist nicht an Cognitive Services gebunden und verfügt über keine Cognitive Services-Schlüsselanforderung.
> Dieser Skill extrahiert Text und Bilder. Die Textextraktion ist kostenlos. Die Bildextraktion wird über [Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/) abgerechnet. Bei einem kostenlosen Suchdienst werden die Kosten für 20 Transaktionen pro Indexer am Tag übernommen, sodass Sie Schnellstarts, Tutorials und kleine Vorgänge kostenlos abschließen können. Bei den Tarifen „Basic“ und „Standard“ sowie bei höheren Tarifen ist die Bildextraktion gebührenpflichtig.
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="supported-document-formats"></a>Unterstützte Dokumentformate

DocumentExtractionSkill kann Text aus den folgenden Dokumentformaten extrahieren:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Eingaben | Zulässige Werte | BESCHREIBUNG |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Legen Sie diesen Parameter auf `default` fest, um die Dokumentextrahierung aus Dateien durchzuführen, die keine reinen Text- oder JSON-Dateien sind. Legen Sie diesen Parameter auf `text` fest, um die Leistung von Nur-Text-Dateien zu verbessern. Legen Sie diesen Parameter auf `json` fest, um strukturierte Inhalte aus JSON-Dateien zu extrahieren. Wenn der Parameter `parsingMode` nicht explizit definiert ist, wird er auf `default` festgelegt. |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Legen Sie diesen Parameter auf `contentAndMetadata` fest, um aus jeder Datei alle Metadaten und Textinhalte zu extrahieren. Legen Sie diesen Parameter auf `allMetadata` fest, um nur die [für den Inhaltstyp spezifischen Metadaten](search-blob-metadata-properties.md) zu extrahieren (z. B. Metadaten, die nur für PNG-Dateien eindeutig sind). Wenn der Parameter `dataToExtract` nicht explizit definiert ist, wird er auf `contentAndMetadata` festgelegt. |
| `configuration` | Siehe unten. | Ein Wörterbuch mit optionalen Parametern zur Anpassung der Durchführung der Dokumentextrahierung. In der folgenden Tabelle finden Sie Beschreibungen der unterstützten Konfigurationseigenschaften. |

| Konfigurationsparameter   | Zulässige Werte | BESCHREIBUNG |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Legen Sie diesen Parameter auf `none` fest, um eingebettete Bilder oder Bilddateien im Dataset zu ignorieren. Dies ist die Standardoption. <br/>Legen Sie diesen Parameter für die [Bildanalyse mithilfe von kognitiven Skills](cognitive-search-concept-image-scenarios.md) auf `generateNormalizedImages` fest, damit der Skill bei der [Dokumententschlüsselung](search-indexer-overview.md#document-cracking) ein Array von normalisierten Bildern erstellt. Für diese Aktion ist es erforderlich, `parsingMode` auf `default` und `dataToExtract` auf `contentAndMetadata` festzulegen. Ein normalisiertes Bild bezieht sich auf eine zusätzliche Verarbeitung, die zu einer einheitlichen Bildausgabe führt. Für die Ausgabe wird die Größe angepasst, und sie wird gedreht, um das einheitliche Rendern zu fördern, wenn Sie Bilder in visuelle Suchergebnisse einbinden (z.B. Fotos gleicher Größe für ein Graphsteuerelement wie in der [JFK-Demo](https://github.com/Microsoft/AzureSearch_JFK_Files)). Diese Informationen werden bei Verwendung dieser Option für jedes Bild generiert.  <br/>Wenn Sie diesen Parameter auf `generateNormalizedImagePerPage` festlegen, werden PDF-Dateien anders behandelt. Anstatt eingebettete Bilder zu extrahieren, wird jede Seite als Bild gerendert und entsprechend normalisiert.  Nicht-PDF-Dateitypen werden genauso behandelt, als ob der Parameter auf `generateNormalizedImages` festgelegt worden wäre.
| `normalizedImageMaxWidth` | Eine beliebige ganze Zahl zwischen 50-10000 | Die maximale Breite (in Pixel) für generierte normalisierte Bilder. Der Standardwert ist „2000“. | 
| `normalizedImageMaxHeight` | Eine beliebige ganze Zahl zwischen 50-10000 | Die maximale Höhe (in Pixel) für generierte normalisierte Bilder. Der Standardwert ist „2000“. |

> [!NOTE]
> Der Standardwert von 2.000 Pixeln für die maximale Breite und Höhe der normalisierten Bilder basiert auf der maximal unterstützten Größe der [OCR-Qualifikation](cognitive-search-skill-ocr.md) und der [Bildanalysequalifikation](cognitive-search-skill-image-analysis.md). Die [OCR-Qualifikation](cognitive-search-skill-ocr.md) unterstützt eine maximale Breite und Höhe von 4.200 für nicht englische Sprachen und 10.000 für Englisch.  Wenn Sie die maximalen Grenzwerte erhöhen, können bei größeren Images je nach Skillsetdefinition und Sprache der Dokumente Fehler bei der Verarbeitung auftreten. 
## <a name="skill-inputs"></a>Skilleingaben

| Eingabename     | BESCHREIBUNG |
|--------------------|-------------|
| `file_data` | Die Datei, aus der Inhalt extrahiert werden soll. |

Die Eingabe „file_data“ muss ein Objekt sein, das wie folgt definiert wurde:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

oder

```json
{
  "$type": "file",
  "url": "URL to download file",
  "sasToken": "OPTIONAL: SAS token for authentication if the URL provided is for a file in blob storage"
}
```

Dieses Dateiverweisobjekt kann auf eine von drei Arten generiert werden:

 - Indem Sie den `allowSkillsetToReadFileData`-Parameters in Ihrer Indexerdefinition auf „true“ festlegen.  Dadurch wird der Pfad `/document/file_data` erstellt, bei dem es sich um ein Objekt handelt, das die aus der Blobdatenquelle heruntergeladenen ursprünglichen Dateidaten darstellt. Dieser Parameter gilt nur für Daten in Blobspeicher.

 - Indem Sie den `imageAction`-Parameters in Ihrer Indexerdefinition auf einen anderen Wert als `none` festlegen.  Dadurch wird ein Array von Bildern erstellt, die die erforderliche Konvention für die Eingaben für diese Qualifikation erfüllen, wenn sie einzeln übermittelt werden (d. h. `/document/normalized_images/*`).

 - Indem Sie eine benutzerdefinierte Qualifikation ein JSON-Objekt zurückgeben lassen, das EXAKT wie oben definiert ist.  Der `$type` Parameter muss genau auf `file` festgelegt werden und der `data` Parameter muss die Base64-codierten Bytearraydaten des Dateiinhalts sein – oder der `url` Parameter muss eine ordnungsgemäß formatierte URL mit Zugriff sein, um die Datei an diesem Speicherort herunterzuladen.

## <a name="skill-outputs"></a>Skillausgaben

| Ausgabename    | BESCHREIBUNG |
|--------------|-------------|
| `content` | Der Textinhalt des Dokuments. |
| `normalized_images`   | Wenn `imageAction` auf einen anderen Wert als `none` festgelegt wird, enthält das neue Feld *normalized_images* ein Array von Bildern. Weitere Informationen zum Ausgabeformat der einzelnen Bilder finden Sie in der [Dokumentation für die Bildextraktion](cognitive-search-concept-image-scenarios.md). |

##  <a name="sample-definition"></a>Beispieldefinition

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "extracted_content"
      },
      {
        "name": "normalized_images",
        "targetName": "extracted_normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>Beispieleingabe

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
      }
    }
  ]
}
```


##  <a name="sample-output"></a>Beispielausgabe

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>Weitere Informationen

+ [Integrierte Qualifikationen](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Verarbeiten und Extrahieren von Text aus Bildern in kognitiven Suchszenarien](cognitive-search-concept-image-scenarios.md)