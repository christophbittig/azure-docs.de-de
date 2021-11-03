---
title: 'Gewusst wie: Migrieren Ihrer Anwendung von der Formularerkennung v2.1 auf v3.0.'
titleSuffix: Azure Applied AI Services
description: In dieser Schrittanleitung erfahren Sie mehr über die Unterschiede zwischen der Formularerkennungs-API v2.1 und v3.0. Außerdem erfahren Sie, welche Änderungen Sie vornehmen müssen, um zur neueren Version der API zu wechseln.
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 10/07/2021
ms.author: vikurpad
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: fd0c9c1ebaf177f6f10698631b96e2c27825603f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020823"
---
# <a name="form-recognizer-v30-migration--preview"></a>Migration der Formularerkennung v3.0 | Vorschau

> [!IMPORTANT]
>
> Die Formularerkennungs-REST-API v3.0 führt Breaking Changes in der REST-API-Anforderung ein und analysiert den JSON-Code der Antwort.

Formularerkennung v3.0 (Vorschau) bietet eine Reihe neuer Features und Möglichkeiten:

* Die [Formularerkennungs-REST-API](quickstarts/try-v3-rest-api.md) wurde überarbeitet, um die Benutzerfreundlichkeit zu verbessern.
* [**Allgemeines Dokumentenmodell (v3.0)**](concept-general-document.md) ist eine neue API, die Text, Tabellen, Strukturen, Schlüssel-Wert-Paare und benannte Entitäten aus Formularen und Dokumenten extrahiert.
* Das Modell [**Receipt (v3.0)**](concept-receipt.md) unterstützt die Verarbeitung von einseitigen Hotelbelegen.
* Das Modell [**ID-Dokument (v3.0)**](concept-id-document.md) unterstützt Vermerke, Einschränkungen und Fahrzeugklassifizierungen aus US-Führerscheinen.
* [**Die benutzerdefinierte Modell-API (v3.0)**](concept-custom.md) unterstützt die Erkennung von Signaturen für benutzerdefinierte Formulare.

In diesem Artikel erfahren Sie mehr über die Unterschiede zwischen Formularerkennung v2.1 und v3.0 und wie Sie zur neueren Version der API wechseln.

## <a name="changes-to-the-rest-api-endpoints"></a>Änderungen an den REST-API-Endpunkten

 Die REST-API v3.0 kombiniert die Analysevorgänge für die Layoutanalyse, vordefinierte Modelle und benutzerdefinierte Modelle zu einem einzelnen Paar von Vorgängen, indem **`documentModels`** und **`modelId`** der Layoutanalyse (vordefiniertes Layout) und vordefinierten Modellen zugewiesen werden.

### <a name="post-request"></a>POST-Anforderung

```http
https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-07-30-preview

```

### <a name="get-request"></a>GET-Anforderung

```http
https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}/AnalyzeResult/{resultId}?api-version=2021-07-30-preview
```

### <a name="analyze-operation"></a>Analysevorgang

* Die Anforderungsnutzdaten und das Aufrufmuster bleiben unverändert.
* Der Analysevorgang gibt das Eingabedokument und inhaltsspezifische Konfigurationen an. Er gibt die Analyseergebnis-URL über den Operation-Location-Header in der Antwort zurück.
* Fordern Sie diese Analyseergebnis-URL über eine GET-Anforderung an, um den Status des Analysevorgangs zu überprüfen (das empfohlene Mindestintervall zwischen Anforderungen beträgt 1 Sekunde).
* Bei Erfolg wird der Status auf „Erfolgreich“ festgelegt und [analyzeResult](#changes-to-analyze-result) im Antworttext zurückgegeben. Treten Fehler auf, wird der Status auf „Fehler“ festgelegt und ein Fehler zurückgegeben.

| Modell | v2.1 | v3.0 |
|:--| :--| :--|
| **Anforderungs-URL-Präfix**| **https://{your-form-recognizer-endpoint}/formrecognizer/v2.1**  | **https://{your-form-recognizer-endpoint}/formrecognizer** |
|🆕 **Allgemeines Dokument**|–|/documentModels/prebuilt-document:analyze |
| **Layout**| /layout/analyze |/documentModels/prebuilt-layout:analyze|
|**Benutzerdefiniert**| /custom/{modelId}/analyze    |/documentModels/{modelId}:analyze |
| **Rechnung** | /prebuilt/invoice/analyze    | /documentModels/prebuilt-invoice:analyze |
| **Rechnung** | /prebuilt/receipt/analyze    | /documentModels/prebuilt-receipt:analyze |
| **ID-Dokument** | /prebuilt/idDocument/analyze |  /documentModels/prebuilt-idDocument:analyze |
|**Visitenkarte**| /prebuilt/businessCard/analyze| /documentModels/prebuilt-businessCard:analyze|

### <a name="analyze-request-body"></a>Analysieren des Anforderungstexts

Der zu analysierende Inhalt wird über den Anforderungstext bereitgestellt. Entweder die URL- oder Base64-codierten Daten können Benutzer sein, um die Anforderung zu erstellen.

  Um eine öffentlich zugängliche Web-URL anzugeben, legen Sie „Content-Type“ auf **application/json** fest und senden den folgenden JSON-Text:

  ```json
  {
    "urlSource": "{urlPath}"
  }
  ```

Die Base64-Codierung wird auch in der Formularerkennung v3.0 unterstützt:

```json
{
  "base64Source": "{base64EncodedContent}"
}
```

### <a name="additional-parameters"></a>Zusätzliche Parameter

Parameter, die weiterhin unterstützt werden:

* Seiten
* locale

Parameter, die nicht mehr unterstützt werden: 

* includeTextDetails

Das neue Antwortformat ist kompakter, und die vollständige Ausgabe wird immer zurückgegeben.

## <a name="changes-to-analyze-result"></a>Änderungen zum Analysieren des Ergebnisses

Die Analyseantwort wurde auf die folgenden Ergebnisse der obersten Ebene umgestaltet, um mehrseitige Elemente zu unterstützen.

* Seiten
* Tabellen
* keyValuePairs
* entities
* Formate
* Dokumente

> [!NOTE]
>
> Die analyzeResult-Antwortänderungen umfassen eine Reihe von Änderungen, z. B. der Wechsel von einer Eigenschaft von Seiten zu einer Eigenschaft auf oberster Ebene in analyzeResult.

```json

{
// Basic analyze result metadata
"apiVersion": "2021-07-30-preview", // REST API version used
"modelId": "prebuilt-invoice", // ModelId used
"stringIndexType": "textElements", // Character unit used for string offsets and lengths:
// textElements, unicodeCodePoint, utf16CodeUnit // Concatenated content in global reading order across pages.
// Words are generally delimited by space, except CJK (Chinese, Japanese, Korean) characters.
// Lines and selection marks are generally delimited by newline character.
// Selection marks are represented in Markdown emoji syntax (:selected:, :unselected:).
"content": "CONTOSO LTD.\nINVOICE\nContoso Headquarters...", "pages": [ // List of pages analyzed
{
// Basic page metadata
"pageNumber": 1, // 1-indexed page number
"angle": 0, // Orientation of content in clockwise direction (degree)
"width": 0, // Page width
"height": 0, // Page height
"unit": "pixel", // Unit for width, height, and bounding box coordinates
"spans": [ // Parts of top-level content covered by page
{
"offset": 0, // Offset in content
"length": 7 // Length in content
}
], // List of words in page
"words": [
{
"text": "CONTOSO", // Equivalent to $.content.Substring(span.offset, span.length)
"boundingBox": [ ... ], // Position in page
"confidence": 0.99, // Extraction confidence
"span": { ... } // Part of top-level content covered by word
}, ...
], // List of selectionMarks in page
"selectionMarks": [
{
"state": "selected", // Selection state: selected, unselected
"boundingBox": [ ... ], // Position in page
"confidence": 0.95, // Extraction confidence
"span": { ... } // Part of top-level content covered by selection mark
}, ...
], // List of lines in page
"lines": [
{
"content": "CONTOSO LTD.", // Concatenated content of line (may contain both words and selectionMarks)
"boundingBox": [ ... ], // Position in page
"spans": [ ... ], // Parts of top-level content covered by line
}, ...
]
}, ...
], // List of extracted tables
"tables": [
{
"rowCount": 1, // Number of rows in table
"columnCount": 1, // Number of columns in table
"boundingRegions": [ // Bounding boxes potentially across pages covered by table
{
"pageNumber": 1, // 1-indexed page number
"boundingBox": [ ... ], // Bounding box
}
],
"spans": [ ... ], // Parts of top-level content covered by table // List of cells in table
"cells": [
{
"kind": "stub", // Cell kind: content (default), rowHeader, columnHeader, stub, description
"rowIndex": 0, // 0-indexed row position of cell
"columnIndex": 0, // 0-indexed column position of cell
"rowSpan": 1, // Number of rows spanned by cell (default=1)
"columnSpan": 1, // Number of columns spanned by cell (default=1)
"content": "SALESPERSON", // Concatenated content of cell
"boundingRegions": [ ... ], // Bounding regions covered by cell
"spans": [ ... ] // Parts of top-level content covered by cell
}, ...
]
}, ...
], // List of extracted key-value pairs
"keyValuePairs": [
{
"key": { // Extracted key
"content": "INVOICE:", // Key content
"boundingRegions": [ ... ], // Key bounding regions
"spans": [ ... ] // Key spans
},
"value": { // Extracted value corresponding to key, if any
"content": "INV-100", // Value content
"boundingRegions": [ ... ], // Value bounding regions
"spans": [ ... ] // Value spans
},
"confidence": 0.95 // Extraction confidence
}, ...
], // List of extracted entities
"entities": [
{
"category": "DateTime", // Primary entity category
"subCategory": "Date", // Secondary entity category
"content": "11/15/2019", // Entity content
"boundingRegions": [ ... ], // Entity bounding regions
"spans": [ ... ], // Entity spans
"confidence": 0.99 // Extraction confidence
}, ...
], // List of extracted styles
"styles": [
{
"isHandwritten": true, // Is content in this style handwritten?
"spans": [ ... ], // Spans covered by this style
"confidence": 0.95 // Detection confidence
}, ...
], // List of extracted documents
"documents": [
{
"docType": "prebuilt-invoice", // Classified document type (model dependent)
"boundingRegions": [ ... ], // Document bounding regions
"spans": [ ... ], // Document spans
"confidence": 0.99, // Document splitting/classification confidence // List of extracted fields
"fields": {
"VendorName": { // Field name (docType dependent)
"type": "string", // Field value type: string, number, array, object, ...
"valueString": "CONTOSO LTD.",// Normalized field value
"content": "CONTOSO LTD.", // Raw extracted field content
"boundingRegions": [ ... ], // Field bounding regions
"spans": [ ... ], // Field spans
"confidence": 0.99 // Extraction confidence
}, ...
}
}, ...
]
}



```

## <a name="build-or-train-model"></a>Erstellen oder Trainieren eines Modells

Das Modellobjekt verfügt über zwei Updates in der neuen API.

* ```modelId``` ist jetzt eine Eigenschaft, die für ein Modell für einen lesbaren Namen festgelegt werden kann.
* ```modelName``` wurde in ```description``` umbenannt.

Der ```build```-Vorgang wird aufgerufen, um ein Modell zu trainieren. Die Anforderungsnutzdaten und das Aufrufmuster bleiben unverändert. Der Erstellungsvorgang gibt das Modell und das Trainingsdataset an und gibt das Ergebnis über den Operation-Location-Header in der Antwort zurück. Fordern Sie diese Modellvorgangs-URL über eine GET-Anforderung an, um den Status des Erstellungsvorgangs zu überprüfen (das empfohlene Mindestintervall zwischen Anforderungen beträgt 1 Sekunde). Im Gegensatz zu v2.1 ist diese URL nicht der Ressourcenspeicherort des Modells. Stattdessen kann die Modell-URL aus der angegebenen Modell-ID konstruiert werden, die auch aus der resourceLocation-Eigenschaft in der Antwort abgerufen wird. Bei Erfolg wird der Status auf ```succeeded``` festgelegt, und das Ergebnis enthält die benutzerdefinierten Modellinformationen. Wenn Fehler auftreten, wird der Status auf ```failed``` festgelegt und der Fehler zurückgegeben.

Der folgende Code ist eine Beispielbuildanforderung mit einem SAS-Token. Beachten Sie beim Festlegen des Präfix- oder Ordnerpfads den nachgestellten Schrägstrich.

```json
POST https://{your-form-recognizer-endpoint}/formrecognizer/documentModels:build?api-version=2021-09-30-preview

{
  "modelId": {modelId},
  "description": "Sample model",
  "azureBlobSource": {
    "containerUrl": "https://{storageAccount}.blob.core.windows.net/{containerName}?{sasToken}",
    "prefix": "{folderName/}"
  }
}
```

## <a name="changes-to-compose-model"></a>Änderungen beim Zusammenstellen des Modells

Das Zusammensetzen von Modellen ist jetzt auf eine einzelne Schachtelungsebene beschränkt. Zusammengestellte Modelle sind jetzt mit benutzerdefinierten Modellen durch das Hinzufügen von ```modelId```- und ```description```-Eigenschaften konsistent.

```json
POST https://{your-form-recognizer-endpoint}/formrecognizer/documentModels:compose?api-version=2021-09-30-preview
{
  "modelId": "{composedModelId}",
  "description": "{composedModelDescription}",
  "componentModels": [
    { "modelId": "{modelId1}" },
    { "modelId": "{modelId2}" },
  ]
}

```

## <a name="changes-to-copy-model"></a>Änderungen am Kopiermodell

Das Aufrufmuster für das Kopiermodell bleibt unverändert:

* Autorisieren Sie den Kopiervorgang mit der Zielressource, die ```authorizeCopy``` aufruft. Jetzt eine POST-Anforderung.
* Übermitteln Sie die Autorisierung an die Quellressource, um das Modell zu kopieren, das ```copy-to``` aufruft.
* Rufen Sie den zurückgegebenen Vorgang ab, um den erfolgreichen Abschluss des Vorgangs zu überprüfen.

Die einzigen Änderungen an der Kopiermodellfunktion sind:

* Die HTTP-Aktion für ```authorizeCopy``` ist jetzt eine POST-Anforderung.
* Die Autorisierungsnutzdaten enthalten alle Informationen, die zum Übermitteln der Kopieranforderung erforderlich sind.

***Autorisieren Sie die Kopie***

```json
POST https://{targetHost}/formrecognizer/documentModels:authorizeCopy?api-version=2021-09-30-preview
{
  "modelId": "{targetModelId}",
  "description": "{targetModelDescription}",
}
```

Verwenden Sie den Antworttext der Autorisierungsaktion, um die Anforderung für die Kopie zu erstellen.

```json
POST https://{sourceHost}/formrecognizer/documentModels/{sourceModelId}:copy-to?api-version=2021-09-30-preview
{
  "targetResourceId": "{targetResourceId}",
  "targetResourceRegion": "{targetResourceRegion}",
  "targetModelId": "{targetModelId}",
  "targetModelLocation": "https://{targetHost}/formrecognizer/documentModels/{targetModelId}",
  "accessToken": "{accessToken}",
  "expirationDateTime": "2021-08-02T03:56:11Z"
}
```

## <a name="changes-to-list-models"></a>Änderungen an Listenmodellen

Listenmodelle wurden erweitert, um jetzt vordefinierte und benutzerdefinierte Modelle zurückzugeben. Alle vordefinierten Modellnamen beginnen mit ```prebuilt-```. Nur Modelle mit dem Status „Erfolgreich“ werden zurückgegeben. Informationen zum Auflisten von Modellen, die entweder fehlerhaft oder in Bearbeitung sind, finden Sie unter [Auflisten von Vorgängen](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetModels).

***Beispielanforderung für Listenmodelle***

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/documentModels?api-version=2021-09-30-preview
```

## <a name="change-to-get-model"></a>Änderung zum Abrufen des Modells

Da „get model“ jetzt auch vordefinierte Modelle enthält, gibt der GET-Vorgang ein ```docTypes```-Wörterbuch zurück. Jeder Dokumenttyp wird durch seinen Namen, eine optionale Beschreibung, ein Feldschema und eine optionale Feldkonfidenz beschrieben. Das Feldschema beschreibt die Liste der Felder, die möglicherweise mit dem Dokumenttyp zurückgegeben werden.

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-09-30-preview
```

## <a name="new-get-info-operation"></a>Neuer Vorgang zum Abrufen von Informationen

Der ```info```-Vorgang für den Dienst gibt die Anzahl der benutzerdefinierten Modelle und den Grenzwert für benutzerdefinierte Modelle zurück.

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/info? api-version=2021-09-30-preview
```

***Beispiel für eine Antwort***

```json
{
  "customDocumentModels": {
    "count": 5,
    "limit": 100
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Migrationshandbuch haben Sie erfahren, wie Sie ein Upgrade für Ihre vorhandene Anwendung zur Formularerkennung für die Verwendung der v3.0-APIs durchführen. Verwenden Sie weiterhin die 2.1-API für alle GA-Features (allgemeine Verfügbarkeit) und die 3.0-API für alle Previewfunktionen.

* [Überprüfen der neuen REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)
* [Was ist die Formularerkennung?](overview.md)
* [Formularerkennung: Schnellstart](./quickstarts/try-sdk-rest-api.md)
