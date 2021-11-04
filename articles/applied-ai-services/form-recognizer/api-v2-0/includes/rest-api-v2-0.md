---
title: 'Referenz: REST-API der Formularerkennung 2.0'
description: Verwenden Sie die REST-API der Formularerkennung, um eine Formularverarbeitungs-App zu erstellen, die Schlüssel-Wert-Paare und Tabellendaten aus Ihren benutzerdefinierten Dokumenten extrahiert.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 645ef6947afb52e1a76d859d72649b89e5db0614
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253244"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!NOTE]
> In diesem Leitfaden wird cURL genutzt, um REST-API-Aufrufe auszuführen.

|[Formularerkennungs-REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)|[Formularerkennungs-REST-API](/rest/api/azure/)|

## <a name="prerequisites"></a>Voraussetzungen

* [cURL](https://curl.haxx.se/windows/) muss installiert sein.
* [PowerShell-Version 6.0 oder höher](/powershell/scripting/install/installing-powershell-core-on-windows) oder eine ähnliche Befehlszeilenanwendung
* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Trainingsdaten in einem Azure Storage-Blob. Tipps und Optionen für das Zusammenstellen eines Trainingsdatasets finden Sie unter [Erstellen eines Trainingsdatasets für ein benutzerdefiniertes Modell](../../build-training-data-set.md). In dieser Schnellstartanleitung können Sie die Dateien im Ordner **Trainieren** des [Beispieldatasets](https://go.microsoft.com/fwlink/?linkid=2090451) verwenden (*sample_data.zip* herunterladen und extrahieren).
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Erstellen einer Formularerkennungsressource"  target="_blank"> im Azure-Portal eine Formularerkennungsressource </a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus.
  * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
  * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* Eine URL für ein Bild eines Verkaufsbelegs. Für diesen Schnellstart können Sie ein [Beispielbild](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) verwenden.
* Eine URL für ein Bild einer Visitenkarte. Für diesen Schnellstart können Sie ein [Beispielbild](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg) verwenden.
* Eine URL für ein Bild einer Rechnung. Für diese Schnellstartanleitung können Sie ein [Beispieldokument](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) verwenden.

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der REST-API der Formularerkennung ausgeführt werden:
<!-- markdownlint-disable MD001 -->

* [Analysieren des Layouts](#analyze-layout)
* [Analysieren von Belegen](#analyze-receipts)
* [Trainieren eines benutzerdefinierten Modells](#train-a-custom-model)
* [Analysieren von Formularen mit einem benutzerdefinierten Modell](#analyze-forms-with-a-custom-model)
* [Verwalten benutzerdefinierter Modelle](#manage-custom-models)



## <a name="analyze-layout"></a>Analysieren des Layouts

Mit der Formularerkennung können Sie Tabellen, Auswahlmarkierungen, Text und Struktur in Dokumenten analysieren und extrahieren, ohne ein Modell trainieren zu müssen. Weitere Informationen zur Layoutextraktion finden Sie im [Konzeptleitfaden zum Layout](../../concept-layout.md). Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `\"{your-document-url}` durch eine der Beispiel-URLs.

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

Sie erhalten die Antwort `202 (Success)` mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Vorgangs-ID, mit der Sie den Status des asynchronen Vorgangs abfragen und die Ergebnisse abrufen können. Im folgenden Beispiel ist die Zeichenfolge nach `analyzeResults/` die Vorgangs-ID.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Abrufen von Layoutergebnissen

Nachdem Sie die **[https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** -API aufgerufen haben, rufen Sie die **[https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** -API auf, um den Status des Vorgangs und die extrahierten Daten abzurufen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{resultId}` durch die Vorgangs-ID aus dem vorherigen Schritt.
<!-- markdownlint-disable MD024 -->

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-results"></a>Untersuchen der Ergebnisse

Sie erhalten die Antwort `200 (success)` mit dem JSON-Inhalt.

Vergleichen Sie das folgende Bild einer Rechnung mit der entsprechenden JSON-Ausgabe.

* Der Knoten `"readResults"` enthält jede Textzeile mit der Platzierung des zugehörigen Begrenzungsrahmens auf der Seite.
* Der Abschnitt `"pageResults"` enthält die extrahierten Tabellen. Für jede Tabelle werden der Text-, Zeilen- und Spaltenindex, die Zeilen- und Spaltenaufteilung, der Begrenzungsrahmen und mehr extrahiert.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Contoso-Projektangabedokument mit einer Tabelle":::

Die Ausgabe wurde zur Vereinfachung gekürzt. Auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json) finden Sie die vollständige Beispielausgabe.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:36:52Z",
    "lastUpdatedDateTime": "2020-08-20T20:36:58Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/14/words/0",
                                    "#/readResults/0/lines/14/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

## <a name="analyze-receipts"></a>Analysieren von Belegen

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe eines vorab trainierten Belegmodells gängige Felder in US-Belegen analysieren und extrahieren. Weitere Informationen zur Beleganalyse finden Sie im [Konzeptleitfaden zu Belegen](../../concept-receipt.md). Rufen Sie die **[Analyze Receipt](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)** -API mit dem unten angegebenen cURL-Befehl auf, um mit der Analyse eines Verkaufsbelegs zu beginnen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{your receipt URL}` mit der URL-Adresse eines Verkaufsbelegbilds.
1. Ersetzen Sie `{subscription key>` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

Sie erhalten die Antwort `202 (Success)` mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Vorgangs-ID, mit der Sie den Status des asynchronen Vorgangs abfragen und die Ergebnisse abrufen können. Im folgenden Beispiel ist die Zeichenfolge nach `operations/` die Vorgangs-ID.

```console
https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>Abrufen der Verkaufsbelegergebnisse

Nachdem Sie die **Analyze Receipt**-API aufgerufen haben, rufen Sie die **[Get Analyze Receipt Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult)** -API auf, um den Status des Vorgangs und die extrahierten Daten abzurufen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben. Sie finden ihn auf der Registerkarte **Übersicht** der Formularerkennungsressource.
1. Ersetzen Sie `{operationId}` durch die Vorgangs-ID aus dem vorherigen Schritt.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Untersuchen der Antwort

Sie erhalten die Antwort `200 (Success)` mit der folgenden JSON-Ausgabe: Das erste Feld, `"status"`, gibt den Status des Vorgangs an. Wenn der Vorgang nicht abgeschlossen ist, ist der Wert von `"status"``"running"` oder `"notStarted"`, und Sie sollten die API Aufrufen entweder manuell oder über ein Skript erneut aufrufen. Ein Intervall von mindestens einer Sekunde zwischen den Aufrufen wird empfohlen.

Der Knoten `"readResults"` enthält den gesamten erkannten Text (wenn Sie den optionalen Parameter *includeTextDetails* auf `true` festlegen). Der Text ist nach Seite, dann nach Zeile und dann nach einzelnen Wörtern sortiert. Der Knoten `"documentResults"` enthält die belegspezifischen Werte, die vom Modell erkannt wurden. Hier finden Sie nützliche Schlüssel-Wert-Paare wie Steuern, Gesamtsumme, Händleranschrift usw.

Vergleichen Sie das folgende Bild des Belegs mit der entsprechenden JSON-Ausgabe.

![Ein Verkaufsbeleg vom Contoso-Laden](../../media/contoso-allinone.jpg)

Die Ausgabe wurde zur besseren Lesbarkeit gekürzt. Auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json) finden Sie die vollständige Beispielausgabe.

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.1.0",
    "readResults":[
      {
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[
      {
        "docType":"prebuilt:receipt",
        "pageRange":[
          1,
          1
        ],
        "fields":{
          "ReceiptType":{
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{
            "type":"array",
            "valueArray":[
              {
                "type":"object",
                "valueObject":{
                  "Quantity":{
                    "type":"number",
                    "text":"1",
                    "boundingBox":[
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="train-a-custom-model"></a>Trainieren eines benutzerdefinierten Modells

Zum Trainieren eines benutzerdefinierten Modells benötigen Sie zunächst Trainingsdaten in einem Azure Storage-Blob. Sie benötigen mindestens fünf ausgefüllte Formulare (PDF-Dokumente und/oder Bilder) desselben Typs/mit der gleichen Struktur. Unter [Erstellen eines Schulungsdatensatzes für ein benutzerdefiniertes Modell](../../build-training-data-set.md) finden Sie Tipps und Optionen für das Zusammenstellen von Trainingsdaten.

Das Training ohne beschriftete Daten ist der Standardvorgang und einfacher. Alternativ können Sie einige oder alle Ihrer Trainingsdaten vorab manuell beschriften. Dies ist ein komplexerer Prozess, der jedoch ein besser trainiertes Modell hervorbringt.

> [!NOTE]
> Sie können Modelle auch mithilfe einer grafischen Benutzeroberfläche trainieren, z. B. dem [Formularerkennungstool für die Bezeichnung von Beispielen](../../label-tool.md).

### <a name="train-a-model-without-labels"></a>Trainieren eines Modells ohne Bezeichnungen

Um ein Formularerkennungsmodell mit den Dokumenten in Ihrem Azure-Blobcontainer zu trainieren, rufen Sie die **[Train Custom Model]https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** -API (Trainieren des benutzerdefinierten Modells) auf, indem Sie den folgenden cURL-Befehl ausführen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{SAS URL}` mit der Shared Access Signature-URL (SAS) des Azure Blob Storage-Containers. 

[!INCLUDE [get SAS URL](sas-instruction.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS-URL-Abruf":::

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

Sie erhalten eine `201 (Success)`-Antwort mit einem **Location**-Header. Der Wert dieses Headers ist die ID des neuen Modells, das trainiert wird.

### <a name="train-a-model-with-labels"></a>Trainieren eines Modells mit Bezeichnungen

Zum Training mit Bezeichnungen benötigen Sie zusätzlich zu den Trainingsdokumenten spezielle Informationsdateien mit Bezeichnungen (`\<filename\>.pdf.labels.json`) in Ihrem Blobspeichercontainer. Das [Formularerkennungstool für die Bezeichnung von Beispielen](../../label-tool.md) bietet eine Benutzeroberfläche, auf der Sie diese Bezeichnungsdateien erstellen können. Sobald Sie über diese Dateien verfügen, können Sie die **[Train Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** -API aufrufen. Legen Sie dabei im JSON-Text den Parameter `"useLabelFile"` auf `true` fest.

Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{SAS URL}` mit der Shared Access Signature-URL (SAS) des Azure Blob Storage-Containers. [!INCLUDE [get SAS URL](sas-instruction.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS-URL-Abruf":::

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true }"
```

Sie erhalten eine `201 (Success)`-Antwort mit einem **Location**-Header. Der Wert dieses Headers ist die ID des neuen Modells, das trainiert wird.

### <a name="get-training-results"></a>Abrufen der Trainingsergebnisse

Nachdem Sie das Training gestartet haben, verwenden Sie einen neuen Vorgang, **[Get Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** (Benutzerdefiniertes Modell abrufen), um den Trainingsstatus zu überprüfen. Übergeben Sie die Modell-ID an diesen API-Aufruf, um den Trainingsstatus zu überprüfen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.
1. Ersetzen Sie `{model ID}` durch die Modell-ID, die Sie im vorherigen Schritt erhalten haben.

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

Sie erhalten eine `200 (Success)`-Antwort mit einem JSON-Text im folgenden Format. Beachten Sie das `"status"`-Feld. Dies hat nach Abschluss des Trainings den Wert `"ready"`. Wenn das Training des Modells noch nicht abgeschlossen ist, müssen Sie den Dienst erneut abfragen, indem Sie den Befehl erneut ausführen. Ein Intervall von mindestens einer Sekunde zwischen den Aufrufen wird empfohlen.

Das `"modelId"`-Feld enthält die ID des Modells, das Sie trainieren. Sie benötigen sie im nächsten Schritt.

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Analysieren von Formularen mit einem benutzerdefinierten Modell

Als Nächstes verwenden Sie Ihr gerade trainiertes Modell, um ein Dokument zu analysieren und Schlüssel-Werte-Paare und Tabellen daraus zu extrahieren. Rufen Sie die **[Analyze Form](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** -API (Formular analysieren) auf, indem Sie den folgenden cURL-Befehl ausführen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben. Sie finden ihn auf der Registerkarte **Übersicht** der Formularerkennungsressource.
1. Ersetzen Sie `{model ID}` durch die Modell-ID, die Sie im vorherigen Abschnitt erhalten haben.
1. Ersetzen Sie `{SAS URL}` durch eine SAS-URL zu Ihrer Datei in Azure Storage. Befolgen Sie die Schritte im Abschnitt „Training“. Rufen Sie jedoch anstelle einer SAS-URL für den gesamten Blobcontainer eine URL für die zu analysierende Datei ab.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

Sie erhalten eine `202 (Success)`-Antwort mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Ergebnis-ID, mit der Sie die Ergebnisse des Analysevorgangs nachverfolgen können. Speichern Sie diese Ergebnis-ID für den nächsten Schritt.

### <a name="get-the-analyze-results"></a>Abrufen der Analyseergebnisse

Rufen Sie die **[Analyze Form Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeFormResult)** -API auf, um die Ergebnisse des Analysevorgangs abzufragen.

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben. Sie finden ihn auf der Registerkarte **Übersicht** der Formularerkennungsressource.
1. Ersetzen Sie `{result ID}` durch die ID, die Sie im vorherigen Abschnitt erhalten haben.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

Sie erhalten eine `200 (Success)`-Antwort mit einem JSON-Text im folgenden Format. Die Ausgabe wurde zur Vereinfachung gekürzt. Beachten Sie das `"status"`-Feld im unteren Bereich. Dies hat den Wert `"succeeded"`, wenn der Analysevorgang beendet ist. Wenn der Analysevorgang noch nicht abgeschlossen ist, müssen Sie den Dienst erneut abfragen, indem Sie den Befehl erneut ausführen. Ein Intervall von mindestens einer Sekunde zwischen den Aufrufen wird empfohlen.

In benutzerdefinierten Modellen, die ohne Bezeichnungen trainiert werden, befinden sich die Schlüssel-Wert-Paar-Zuordnungen und Tabellen im Knoten `"pageResults"` der JSON-Ausgabe. In benutzerdefinierten Modellen, die mit Bezeichnungen trainiert werden, befinden sich die Schlüssel-Wert-Paar-Zuordnungen im Knoten `"documentResults"`. Wenn Sie auch die Nur-Text-Extraktion über den *includeTextDetails*-URL-Parameter angegeben haben, zeigt der Knoten `"readResults"` den Inhalt und die Positionen des gesamten Texts im Dokument an.

Diese JSON-Beispielausgabe wurde zur Vereinfachung gekürzt. Auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json) finden Sie die vollständige Beispielausgabe.

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          },
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ],
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```

### <a name="improve-results"></a>Verbessern der Ergebnisse

[!INCLUDE [improve results](improve-result-unlabeled.md)]

## <a name="manage-custom-models"></a>Verwalten benutzerdefinierter Modelle

### <a name="get-a-list-of-custom-models"></a>Abrufen einer Liste mit benutzerdefinierten Modellen

Verwenden Sie die **[List Custom Models](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModels)** -API im folgenden Befehl, um eine Liste mit allen benutzerdefinierten Modellen zurückzugeben, die zu Ihrem Abonnement gehören.

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

Sie erhalten eine Antwort mit dem Hinweis zur erfolgreichen Durchführung (`200`) und den folgenden JSON-Daten. Das `"modelList"`-Element enthält Ihre gesamten erstellten Modelle und die zugehörigen Informationen.

```json
{
  "summary": {
    "count": 0,
    "limit": 0,
    "lastUpdatedDateTime": "string"
  },
  "modelList": [
    {
      "modelId": "string",
      "status": "creating",
      "createdDateTime": "string",
      "lastUpdatedDateTime": "string"
    }
  ],
  "nextLink": "string"
}
```

### <a name="get-a-specific-model"></a>Abrufen eines bestimmten Modells

Verwenden Sie die **[Get Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** -API im folgenden Befehl, um ausführliche Informationen zu einem bestimmten benutzerdefinierten Modell abzurufen.

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{modelId}` durch die ID des benutzerdefinierten Modells, nach dem Sie suchen möchten.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

Sie erhalten eine Antwort mit dem Hinweis zur erfolgreichen Durchführung (`200`) und den folgenden JSON-Daten.

```json
{
  "modelInfo": {
    "modelId": "string",
    "status": "creating",
    "createdDateTime": "string",
    "lastUpdatedDateTime": "string"
  },
  "keys": {
    "clusters": {}
  },
  "trainResult": {
    "trainingDocuments": [
      {
        "documentName": "string",
        "pages": 0,
        "errors": [
          "string"
        ],
        "status": "succeeded"
      }
    ],
    "fields": [
      {
        "fieldName": "string",
        "accuracy": 0.0
      }
    ],
    "averageModelAccuracy": 0.0,
    "errors": [
      {
        "message": "string"
      }
    ]
  }
}
```

### <a name="delete-a-model-from-the-resource-account"></a>Löschen eines Modells aus dem Ressourcenkonto

Sie können ein Modell auch aus Ihrem Konto löschen, indem Sie auf die ID des Modells verweisen. Mit diesem Befehl wird die **[Delete Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/DeleteCustomModel)** -API aufgerufen, um das im vorherigen Abschnitt verwendete Modell zu löschen.

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{modelId}` durch die ID des benutzerdefinierten Modells, nach dem Sie suchen möchten.

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

Sie erhalten eine `204`-Erfolgsmeldung, in der angegeben ist, dass Ihr Modell zum Löschen markiert wurde. Modellartefakte werden innerhalb von 48 Stunden gelöscht.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie die REST-API der Formularerkennung verwendet, um auf unterschiedliche Weise Modelle zu trainieren und Formulare zu analysieren. Lesen Sie als Nächstes die Referenzdokumentation, um die Formularerkennungs-API eingehender zu erkunden.

> [!div class="nextstepaction"]
> [Referenzdokumentation zur Rest-API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
