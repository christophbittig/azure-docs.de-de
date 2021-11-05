---
title: Verwenden der REST-API für die Azure-Formularerkennung
description: Verwenden Sie die REST-API für die Formularerkennung, um eine Formularverarbeitungsapp zu erstellen, die Schlüssel-Wert-Paare und Tabellendaten aus Ihren benutzerdefinierten Dokumenten extrahiert.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: d74e7e84461eadc40cd4d2940ae083a411f12f57
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131094928"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->

> [!NOTE]
>
> * Dieses Projekt behandelt die API-Version **2.1** für die Azure-Formularerkennung mit cURL zum Ausführen von REST-API-Aufrufen.

| [Formularerkennungs-REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) | [Formularerkennungs-REST-API](/rest/api/azure/) |

## <a name="prerequisites"></a>Voraussetzungen

* [cURL](https://curl.haxx.se/windows/) muss installiert sein.
* [PowerShell-Version 6.0 oder höher](/powershell/scripting/install/installing-powershell-core-on-windows) oder eine ähnliche Befehlszeilenanwendung
* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Trainingsdaten in einem Azure Storage-Blob. Tipps und Optionen für das Zusammenstellen eines Trainingsdatasets finden Sie unter [Erstellen eines Trainingsdatasets für ein benutzerdefiniertes Modell](../../build-training-data-set.md). Sie können die Dateien im Ordner **Trainieren** des [Beispieldataset](https://go.microsoft.com/fwlink/?linkid=2090451) verwenden (*sample_data.zip* herunterladen und extrahieren).
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Erstellen einer Formularerkennungsressource"  target="_blank"> im Azure-Portal eine Formularerkennungsressource </a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus.
  * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Sie fügen Ihren Schlüssel und Endpunkt später im Projekt in den folgenden Code ein.
  * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
<<<<<<< HEAD:articles/applied-ai-services/form-recognizer/includes/how-to-guides/rest-api.md
* Eine URL für ein **Bild eines Verkaufsbelegs**. Sie können ein [Beispielbild](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) verwenden.
* Eine URL für ein **Bild einer Visitenkarte**. Sie können ein [Beispielbild](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg) verwenden.
* Eine URL für ein **Bild einer Rechnung**. Sie können ein [Beispieldokument](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) verwenden.
* Eine URL für ein **Bild eines Ausweisdokuments**. Sie können ein [Beispielbild](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png) verwenden.
=======
* Eine URL für ein **Bild eines Verkaufsbelegs**. Für diesen Schnellstart können Sie ein [Beispielbild](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) verwenden.
* Eine URL für ein **Bild einer Visitenkarte**. Für diesen Schnellstart können Sie ein [Beispielbild](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg) verwenden.
* Eine URL für ein **Bild einer Rechnung**. Für diese Schnellstartanleitung können Sie ein [Beispieldokument](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) verwenden.
* Eine URL für ein **Bild eines Ausweisdokuments**. Sie können ein [Beispielbild](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png) verwenden.
>>>>>>> 103f7cf9752d7b4e4c9bf3da2c3649ad27ebfd2f:articles/applied-ai-services/form-recognizer/includes/quickstarts/javascript-sdk.md

## <a name="analyze-layout"></a>Analysieren des Layouts

Mit der Formularerkennung können Sie Tabellen, Auswahlmarkierungen, Text und Struktur in Dokumenten analysieren und extrahieren, ohne ein Modell trainieren zu müssen. Weitere Informationen zur Layoutextraktion finden Sie im [Konzeptleitfaden zum Layout](../../concept-layout.md). Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `\"{your-document-url}` durch eine der Beispiel-URLs.

#### <a name="request"></a>Anforderung

```bash
curl -v -i POST "https://{endpoint}/formrecognizer/v2.1/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

Sie erhalten die Antwort `202 (Success)` mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Ergebnis-ID, mit der Sie den Status des asynchronen Vorgangs abfragen und die Ergebnisse abrufen können:

https://<span></span>cognitiveservice/formrecognizer/v2.1/layout/analyzeResults/ **{resultId}** . 

Im folgenden Beispiel als Teil der URL ist die Zeichenfolge nach `analyzeResults/` die Ergebnis-ID.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Abrufen von Layoutergebnissen

Nachdem Sie die **[Analyze Layout](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)** -API aufgerufen haben, rufen Sie die **[Get Analyze Layout Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeLayoutResult)** -API auf, um den Status des Vorgangs und die extrahierten Daten abzurufen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{resultId}` durch die Ergebnis-ID aus dem vorherigen Schritt.
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>Anforderung

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-results"></a>Untersuchen der Ergebnisse

Sie erhalten die Antwort `200 (success)` mit dem JSON-Inhalt.

Vergleichen Sie das folgende Bild einer Rechnung mit der entsprechenden JSON-Ausgabe.

* Der Knoten `"readResults"` enthält jede Textzeile mit der Platzierung des zugehörigen Begrenzungsrahmens auf der Seite.
* Der Knoten `selectionMarks` zeigt jede Auswahlmarkierung (Kontrollkästchen, Optionsfeld) und ihren Status („ausgewählt“ oder „nicht ausgewählt“).
* Der Abschnitt `"pageResults"` enthält die extrahierten Tabellen. Für jede Tabelle werden der Text-, Zeilen- und Spaltenindex, die Zeilen- und Spaltenaufteilung, der Begrenzungsrahmen und mehr extrahiert.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Contoso-Projektangabedokument mit einer Tabelle":::

#### <a name="response-body"></a>Antworttext

Die Ausgabe wurde zur Vereinfachung gekürzt. Auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json) finden Sie die vollständige Beispielausgabe.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:40:50Z",
    "lastUpdatedDateTime": "2020-08-20T20:40:55Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
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
                "selectionMarks": [
                    {
                        "boundingBox": [
                            3.9737,
                            3.7475,
                            4.1693,
                            3.7475,
                            4.1693,
                            3.9428,
                            3.9737,
                            3.9428
                        ],
                        "confidence": 0.989,
                        "state": "selected"
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
                                    "#/readResults/0/lines/12/words/0",
                                    "#/readResults/0/lines/12/words/1"
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

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe eines vorab trainierten Belegmodells gängige Felder in US-Belegen analysieren und extrahieren. Weitere Informationen zur Beleganalyse finden Sie im [Konzeptleitfaden zu Belegen](../../concept-receipt.md). Rufen Sie die **[Analyze Receipt](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)** -API mit dem unten angegebenen cURL-Befehl auf, um mit der Analyse eines Verkaufsbelegs zu beginnen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{your receipt URL}` mit der URL-Adresse eines Verkaufsbelegbilds.
1. Ersetzen Sie `{subscription key>` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.

#### <a name="request"></a>Anforderung

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

Sie erhalten die Antwort `202 (Success)` mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Ergebnis-ID, mit der Sie den Status des asynchronen Vorgangs abfragen und die Ergebnisse abrufen können:

*https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/**{resultId}* **

Im folgenden Beispiel ist die Zeichenfolge nach `operations/` die Ergebnis-ID:

```console
https://cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-receipt-results"></a>Abrufen der Verkaufsbelegergebnisse

Nachdem Sie die **Analyze Receipt**-API aufgerufen haben, rufen Sie die **[Get Analyze Receipt Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeReceiptResult)** -API auf, um den Status des Vorgangs und die extrahierten Daten abzurufen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben. Sie finden ihn auf der Registerkarte **Übersicht** der Formularerkennungsressource.
1. Ersetzen Sie `{resultId}` durch die Ergebnis-ID aus dem vorherigen Schritt.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.

#### <a name="request"></a>Anforderung

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Untersuchen der Antwort

Sie erhalten die Antwort `200 (Success)` mit der folgenden JSON-Ausgabe: Das erste Feld, `"status"`, gibt den Status des Vorgangs an. Wenn der Vorgang nicht abgeschlossen ist, ist der Wert von `"status"``"running"` oder `"notStarted"`, und Sie sollten die API Aufrufen entweder manuell oder über ein Skript erneut aufrufen. Ein Intervall von mindestens einer Sekunde zwischen den Aufrufen wird empfohlen.

Der Knoten `"readResults"` enthält den gesamten erkannten Text (wenn Sie den optionalen Parameter *includeTextDetails* auf `true` festlegen). Der Text ist nach Seite, dann nach Zeile und dann nach einzelnen Wörtern sortiert. Der Knoten `"documentResults"` enthält die belegspezifischen Werte, die vom Modell erkannt wurden. Hier finden Sie nützliche Schlüssel-Wert-Paare wie Steuern, Gesamtsumme, Händleranschrift usw.

Vergleichen Sie das folgende Bild des Belegs mit der entsprechenden JSON-Ausgabe.

![Ein Verkaufsbeleg vom Contoso-Laden](../../media/contoso-allinone.jpg)

#### <a name="response-body"></a>Antworttext

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

## <a name="analyze-business-cards"></a>Analysieren von Visitenkarten

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe eines vorab trainierten Modells gängige Felder englischsprachiger Visitenkarten analysieren und extrahieren. Weitere Informationen zur Analyse von Visitenkarten finden Sie im [Konzeptleitfaden zu Visitenkarten](../../concept-business-card.md). Rufen Sie die API **[Analyze Business Card](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync)** mit dem unten angegebenen cURL-Befehl auf, um mit der Analyse einer Visitenkarte zu beginnen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{your business card URL}` mit der URL-Adresse eines Verkaufsbelegbilds.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.

#### <a name="request"></a>Anforderung

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

Sie erhalten die Antwort `202 (Success)` mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Ergebnis-ID, mit der Sie den Status des asynchronen Vorgangs abfragen und die Ergebnisse abrufen können:

_https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/businessCard/analyzeResults/ **{resultId}**_

Im folgenden Beispiel als Teil der URL ist die Zeichenfolge nach `analyzeResults/` die Ergebnis-ID.

```console
https://cognitiveservice/formrecognizer/v2.1/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>Abrufen der Ergebnisse für die Visitenkarte

Nachdem Sie die API **Analyze Business Card** aufgerufen haben, rufen Sie die API **[Get Analyze Business Card Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeBusinessCardResult)** auf, um den Status des Vorgangs und die extrahierten Daten abzurufen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben. Sie finden ihn auf der Registerkarte **Übersicht** der Formularerkennungsressource.
1. Ersetzen Sie `{resultId}` durch die Ergebnis-ID aus dem vorherigen Schritt.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.

```bash
curl -v -X GET https://{endpoint}/formrecognizer/v2.1/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Untersuchen der Antwort

Sie erhalten die Antwort `200 (Success)` mit der folgenden JSON-Ausgabe:

Der Knoten `"readResults"` enthält den gesamten erkannten Text. Der Text ist nach Seite, dann nach Zeile und dann nach einzelnen Wörtern sortiert. Der Knoten `"documentResults"` enthält die visitenkartenspezifischen Werte, die vom Modell erkannt wurden. Er enthält nützliche Kontaktinformationen wie Firmenname, Vorname, Nachname, Telefonnummer usw.

![Eine Visitenkarte des Unternehmens Contoso](../../media/business-card-english.jpg)

Diese JSON-Beispielausgabe wurde zur Vereinfachung gekürzt. Auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json) finden Sie die vollständige Beispielausgabe.

```json
{
    "status": "succeeded",
    "createdDateTime":"2021-02-09T18:14:05Z",
    "lastUpdatedDateTime":"2021-02-09T18:14:10Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
             "page":1,
             "angle":-16.6836,
             "width":4032,
             "height":3024,
             "unit":"pixel"
          }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Das Skript gibt Antworten in der Konsole aus, bis der **Analyze Business Card**-Vorgang abgeschlossen ist.

## <a name="analyze-invoices"></a>Analysieren von Rechnungen

Sie können Formularerkennung verwenden, um Feldtext und semantische Werte aus einem bestimmten Rechnungsdokument zu extrahieren.  Verwenden Sie den folgenden cURL-Befehl, um mit dem Analysieren einer Rechnung zu beginnen. Weitere Informationen zur Rechnungsanalyse finden Sie im [Konzeptleitfaden zu Rechnungen](../../concept-invoice.md). Rufen Sie die **[Analyze Invoice](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)** -API mit dem unten angegebenen cURL-Befehl auf, um mit der Analyse einer Rechnung zu beginnen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{your invoice URL}` durch die URL-Adresse eines Rechnungsdokuments.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.

#### <a name="request"></a>Anforderung

```bash
curl -v -i POST https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

Sie erhalten die Antwort `202 (Success)` mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Ergebnis-ID, mit der Sie den Status des asynchronen Vorgangs abfragen und die Ergebnisse abrufen können:

 _https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/ **{resultId}**_

Im folgenden Beispiel als Teil der URL ist die Zeichenfolge nach `analyzeResults/` die Ergebnis-ID:

```console
https://cognitiveservice/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Abrufen von Rechnungsergebnissen

Nachdem Sie die **Analyze Invoice**-API aufgerufen haben, rufen Sie die **[Get Analyze Invoice Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9acb78c40a2533aee83)** -API auf, um den Status des Vorgangs und die extrahierten Daten abzurufen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben. Sie finden ihn auf der Registerkarte **Übersicht** der Formularerkennungsressource.
1. Ersetzen Sie `{resultId}` durch die Ergebnis-ID aus dem vorherigen Schritt.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.

#### <a name="request"></a>Anforderung

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Untersuchen der Antwort

Sie erhalten die Antwort `200 (Success)` mit der folgenden JSON-Ausgabe:

* Das `"readResults"`-Feld enthält alle Textzeilen, die aus der Rechnung extrahiert wurden.
* `"pageResults"` beinhaltet die Tabellen und Informationen zu den Auswahlkästchen, die aus der Rechnung extrahiert wurden.
* Das `"documentResults"`-Feld enthält die Schlüssel-/Wertinformationen für die relevantesten Teile der Rechnung.

Sehen Sie sich das folgende Rechnungsdokument und die zugehörige JSON-Ausgabe an.

* [Beispielrechnung](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

#### <a name="response-body"></a>Antworttext

Dieser JSON-Inhalt wurde gekürzt, um die Lesbarkeit zu verbessern. Auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json) finden Sie die vollständige Beispielausgabe.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-11-06T23:32:11Z",
    "lastUpdatedDateTime": "2020-11-06T23:32:20Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [{
            "page": 1,
            "angle": 0,
            "width": 8.5,
            "height": 11,
            "unit": "inch"
        }],
        "pageResults": [{
            "page": 1,
            "tables": [{
                "rows": 3,
                "columns": 4,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "QUANTITY",
                    "boundingBox": [0.4953,
                    5.7306,
                    1.8097,
                    5.7306,
                    1.7942,
                    6.0122,
                    0.4953,
                    6.0122]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "DESCRIPTION",
                    "boundingBox": [1.8097,
                    5.7306,
                    5.7529,
                    5.7306,
                    5.7452,
                    6.0122,
                    1.7942,
                    6.0122]
                },
                ...
                ],
                "boundingBox": [0.4794,
                5.7132,
                8.0158,
                5.714,
                8.0118,
                6.5627,
                0.4757,
                6.5619]
            },
            {
                "rows": 2,
                "columns": 6,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "SALESPERSON",
                    "boundingBox": [0.4979,
                    4.963,
                    1.8051,
                    4.963,
                    1.7975,
                    5.2398,
                    0.5056,
                    5.2398]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "P.O. NUMBER",
                    "boundingBox": [1.8051,
                    4.963,
                    3.3047,
                    4.963,
                    3.3124,
                    5.2398,
                    1.7975,
                    5.2398]
                },
                ...
                ],
                "boundingBox": [0.4976,
                4.961,
                7.9959,
                4.9606,
                7.9959,
                5.5204,
                0.4972,
                5.5209]
            }]
        }],
        "documentResults": [{
            "docType": "prebuilt:invoice",
            "pageRange": [1,
            1],
            "fields": {
                "AmountDue": {
                    "type": "number",
                    "valueNumber": 610,
                    "text": "$610.00",
                    "boundingBox": [7.3809,
                    7.8153,
                    7.9167,
                    7.8153,
                    7.9167,
                    7.9591,
                    7.3809,
                    7.9591],
                    "page": 1,
                    "confidence": 0.875
                },
                "BillingAddress": {
                    "type": "string",
                    "valueString": "123 Bill St, Redmond WA, 98052",
                    "text": "123 Bill St, Redmond WA, 98052",
                    "boundingBox": [0.594,
                    4.3724,
                    2.0125,
                    4.3724,
                    2.0125,
                    4.7125,
                    0.594,
                    4.7125],
                    "page": 1,
                    "confidence": 0.997
                },
                "BillingAddressRecipient": {
                    "type": "string",
                    "valueString": "Microsoft Finance",
                    "text": "Microsoft Finance",
                    "boundingBox": [0.594,
                    4.1684,
                    1.7907,
                    4.1684,
                    1.7907,
                    4.2837,
                    0.594,
                    4.2837],
                    "page": 1,
                    "confidence": 0.998
                },
                ...
            }
        }]
    }
}
```

## <a name="analyze-identity-id-documents"></a>Analysieren von Ausweisdokumenten

Verwenden Sie den folgenden cURL-Befehl, um mit dem Analysieren eines Ausweisdokuments zu beginnen. Weitere Informationen zur Ausweisdokumentanalyse finden Sie im [Konzeptleitfaden für Ausweisdokumente](../../concept-id-document.md). Rufen Sie mit dem folgenden cURL-Befehl die API **[Analyze ID Document](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707)** auf, um mit dem Analysieren eines Ausweisdokuments zu beginnen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{your ID document URL}` mit der URL-Adresse eines Verkaufsbelegbilds.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.

#### <a name="request"></a>Anforderung

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/prebuilt/idDocument/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your ID document URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

Sie erhalten die Antwort `202 (Success)` mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Ergebnis-ID, mit der Sie den Status des asynchronen Vorgangs abfragen und die Ergebnisse abrufen können:

_https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/documentId/analyzeResults/ **{resultId}**_

Im folgenden Beispiel ist die Zeichenfolge nach `analyzeResults/` die Ergebnis-ID:

```console
https://westus.api.cognitive.microsoft.com/formrecognizer/v2.1/prebuilt/idDocument/analyzeResults/83d0137b-28e1-4051-98ce-42bd21f77ae0
```

### <a name="get-the-analyze-id-document-result"></a>Abrufen des Ergebnisses der Ausweisdokumentanalyse

Nachdem Sie die **Analyze ID Document**-API aufgerufen haben, rufen Sie die **[GET Analyze ID Document Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeBusinessCardResult)** -API auf, um den Status des Vorgangs und die extrahierten Daten abzurufen.  Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben. Sie finden ihn auf der Registerkarte **Übersicht** der Formularerkennungsressource.
1. Ersetzen Sie `{resultId}` durch die Ergebnis-ID aus dem vorherigen Schritt.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.

#### <a name="request"></a>Anforderung

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1/prebuilt/businessCard/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Untersuchen der Antwort

Sie erhalten die Antwort `200 (Success)` mit der folgenden JSON-Ausgabe: Das erste Feld, `"status"`, gibt den Status des Vorgangs an. Wenn der Vorgang nicht abgeschlossen ist, wird für `"status"` der Wert `"running"` oder `"notStarted"` angezeigt, und Sie sollten die API entweder manuell oder über ein Skript erneut aufrufen, bis der Wert `succeeded` zurückgegeben wird. Ein Intervall von mindestens einer Sekunde zwischen den Aufrufen wird empfohlen.

* Das Feld `"readResults"` enthält alle Textzeilen, die aus dem Ausweisdokument extrahiert wurden.
* Das Feld `"documentResults"` enthält ein Array von Objekten, die jeweils ein Ausweisdokument darstellen, das im Eingabedokument erkannt wurde.

Nachfolgend sehen Sie ein Beispiel für ein Ausweisdokument und die entsprechende JSON-Ausgabe:

* :::image type="content" source="https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png" alt-text="Beispiel eines Führerscheins":::

#### <a name="response-body"></a>Antworttext

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-04-13T17:24:52Z",
    "lastUpdatedDateTime": "2021-04-13T17:24:55Z",
    "analyzeResult": {
      "version": "2.1.0",
      "readResults": [
        {
          "page": 1,
          "angle": -0.2823,
          "width": 450,
          "height": 294,
          "unit": "pixel"
        }
      ],
      "documentResults": [
        {
          "docType": "prebuilt:idDocument:driverLicense",
          "docTypeConfidence": 0.995,
          "pageRange": [
            1,
            1
          ],
          "fields": {
            "Address": {
              "type": "string",
              "valueString": "123 STREET ADDRESS YOUR CITY WA 99999-1234",
              "text": "123 STREET ADDRESS YOUR CITY WA 99999-1234",
              "boundingBox": [
                158,
                151,
                326,
                151,
                326,
                177,
                158,
                177
              ],
              "page": 1,
              "confidence": 0.965
            },
            "CountryRegion": {
              "type": "countryRegion",
              "valueCountryRegion": "USA",
              "confidence": 0.99
            },
            "DateOfBirth": {
              "type": "date",
              "valueDate": "1958-01-06",
              "text": "01/06/1958",
              "boundingBox": [
                187,
                133,
                272,
                132,
                272,
                148,
                187,
                149
              ],
              "page": 1,
              "confidence": 0.99
            },
            "DateOfExpiration": {
              "type": "date",
              "valueDate": "2020-08-12",
              "text": "08/12/2020",
              "boundingBox": [
                332,
                230,
                414,
                228,
                414,
                244,
                332,
                245
              ],
              "page": 1,
              "confidence": 0.99
            },
            "DocumentNumber": {
              "type": "string",
              "valueString": "LICWDLACD5DG",
              "text": "LIC#WDLABCD456DG",
              "boundingBox": [
                162,
                70,
                307,
                68,
                307,
                84,
                163,
                85
              ],
              "page": 1,
              "confidence": 0.99
            },
            "FirstName": {
              "type": "string",
              "valueString": "LIAM R.",
              "text": "LIAM R.",
              "boundingBox": [
                158,
                102,
                216,
                102,
                216,
                116,
                158,
                116
              ],
              "page": 1,
              "confidence": 0.985
            },
            "LastName": {
              "type": "string",
              "valueString": "TALBOT",
              "text": "TALBOT",
              "boundingBox": [
                160,
                86,
                213,
                85,
                213,
                99,
                160,
                100
              ],
              "page": 1,
              "confidence": 0.987
            },
            "Region": {
              "type": "string",
              "valueString": "Washington",
              "confidence": 0.99
            },
            "Sex": {
              "type": "string",
              "valueString": "M",
              "text": "M",
              "boundingBox": [
                226,
                190,
                232,
                190,
                233,
                201,
                226,
                201
              ],
              "page": 1,
              "confidence": 0.99
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
> Sie können Modelle auch mithilfe einer grafischen Benutzeroberfläche trainieren, z. B. dem [Formularerkennungstool für die Beschriftung von Beispielen](../../label-tool.md).

### <a name="train-a-model-without-labels"></a>Trainieren eines Modells ohne Bezeichnungen

Um ein Formularerkennungsmodell mit den Dokumenten in Ihrem Azure-Blobcontainer zu trainieren, rufen Sie die **[Train Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync)** -API (Trainieren des benutzerdefinierten Modells) auf, indem Sie den folgenden cURL-Befehl ausführen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{SAS URL}` mit der Shared Access Signature-URL (SAS) des Azure Blob Storage-Containers. [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS-URL-Abruf":::

#### <a name="request"></a>Anforderung

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

#### <a name="location"></a>Standort

Sie erhalten eine `201 (Success)`-Antwort mit einem **Location**-Header. Der Wert dieses Headers enthält eine Modell-ID für das neu trainierte Modell, mit der Sie den Status des Vorgangs abfragen und die Ergebnisse abrufen können:

_https://{endpoint}/formrecognizer/v2.1/custom/models/ **{modelId}**_

Im folgenden Beispiel als Teil der URL ist die Zeichenfolge nach `models/` die Modell-ID.

```console
https://westus.api.cognitive.microsoft.com/formrecognizer/v2.1/custom/models/77d8ecad-b8c1-427e-ac20-a3fe4af503e9
```

### <a name="train-a-model-with-labels"></a>Trainieren eines Modells mit Bezeichnungen

Zum Training mit Bezeichnungen benötigen Sie zusätzlich zu den Trainingsdokumenten spezielle Informationsdateien mit Bezeichnungen (`\<filename\>.pdf.labels.json`) in Ihrem Blobspeichercontainer. Das [Formularerkennungstool für die Beschriftung von Beispielen](../../label-tool.md) bietet eine Benutzeroberfläche, auf der Sie diese Beschriftungsdateien erstellen können. Sobald Sie über diese Dateien verfügen, können Sie die **[Train Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync)** -API aufrufen. Legen Sie dabei im JSON-Text den Parameter `"useLabelFile"` auf `true` fest.

Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{SAS URL}` mit der Shared Access Signature-URL (SAS) des Azure Blob Storage-Containers. [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS-URL-Abruf":::

#### <a name="request"></a>Anforderung

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true}"
```

#### <a name="location"></a>Standort

Sie erhalten eine `201 (Success)`-Antwort mit einem **Location**-Header. Der Wert dieses Headers enthält eine Modell-ID für das neu trainierte Modell, mit der Sie den Status des Vorgangs abfragen und die Ergebnisse abrufen können:

_https://{endpoint}/formrecognizer/v2.1/custom/models/ **{modelId}**_

Im folgenden Beispiel als Teil der URL ist die Zeichenfolge nach `models/` die Modell-ID.

```console
https://westus.api.cognitive.microsoft.com/formrecognizer/v2.1/custom/models/4da0bf8e-5325-467c-93bb-9ff13d5f72a2
```

### <a name="get-training-results"></a>Abrufen der Trainingsergebnisse

Nachdem Sie das Training gestartet haben, überprüfen Sie mit **[Get Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModel)** den Trainingsstatus. Übergeben Sie die Modell-ID an den API-Aufruf, um den Trainingsstatus zu überprüfen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.
1. Ersetzen Sie `{model ID}` durch die Modell-ID, die Sie im vorherigen Schritt erhalten haben.

#### <a name="request"></a>Anforderung

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

## <a name="analyze-forms-with-a-custom-model"></a>Analysieren von Formularen mit einem benutzerdefinierten Modell

Als Nächstes verwenden Sie Ihr gerade trainiertes Modell, um ein Dokument zu analysieren und Felder und Tabellen daraus zu extrahieren. Rufen Sie die **[Analyze Form](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)** -API (Formular analysieren) auf, indem Sie den folgenden cURL-Befehl ausführen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben. Sie finden ihn auf der Registerkarte **Übersicht** der Formularerkennungsressource.
1. Ersetzen Sie `{model ID}` durch die Modell-ID, die Sie im vorherigen Abschnitt erhalten haben.
1. Ersetzen Sie `{SAS URL}` durch eine SAS-URL zu Ihrer Datei in Azure Storage. Befolgen Sie die Schritte im Abschnitt „Training“. Rufen Sie jedoch anstelle einer SAS-URL für den gesamten Blobcontainer eine URL für die zu analysierende Datei ab.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.

#### <a name="request"></a>Anforderung

```bash
curl -v "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

Sie erhalten eine `202 (Success)`-Antwort mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Ergebnis-ID, mit der Sie die Ergebnisse des Analysevorgangs nachverfolgen können:

_https://<span></span>cognitiveservice/formrecognizer/v2.1/custom/models/{modelId}/analyzeResults/ **{resultId}**_

Im folgenden Beispiel als Teil der URL ist die Zeichenfolge nach `analyzeResults/` die Ergebnis-ID.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

Speichern Sie diese Ergebnis-ID für den nächsten Schritt.

### <a name="get-the-analyze-results"></a>Abrufen der Analyseergebnisse

Rufen Sie die **[Analyze Form Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeFormResult)** -API auf, um die Ergebnisse des Analysevorgangs abzufragen.

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben. Sie finden ihn auf der Registerkarte **Übersicht** der Formularerkennungsressource.
1. Ersetzen Sie `{result ID}` durch die ID, die Sie im vorherigen Abschnitt erhalten haben.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.

#### <a name="request"></a>Anforderung

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

Sie erhalten eine `200 (Success)`-Antwort mit einem JSON-Text im folgenden Format. Die Ausgabe wurde zur Vereinfachung gekürzt. Beachten Sie das `"status"`-Feld im unteren Bereich. Dies hat den Wert `"succeeded"`, wenn der Analysevorgang beendet ist. Wenn der Analysevorgang noch nicht abgeschlossen ist, müssen Sie den Dienst erneut abfragen, indem Sie den Befehl erneut ausführen. Ein Intervall von mindestens einer Sekunde zwischen den Aufrufen wird empfohlen.

In benutzerdefinierten Modellen, die ohne Bezeichnungen trainiert werden, befinden sich die Schlüssel-Wert-Paar-Zuordnungen und Tabellen im Knoten `"pageResults"` der JSON-Ausgabe. In benutzerdefinierten Modellen, die mit Bezeichnungen trainiert werden, befinden sich die Schlüssel-Wert-Paar-Zuordnungen im Knoten `"documentResults"`. Wenn Sie auch die Nur-Text-Extraktion über den *includeTextDetails*-URL-Parameter angegeben haben, zeigt der Knoten `"readResults"` den Inhalt und die Positionen des gesamten Texts im Dokument an.

Diese JSON-Beispielausgabe wurde zur Vereinfachung gekürzt. Auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json) finden Sie die vollständige Beispielausgabe.

#### <a name="response-body"></a>Antworttext

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
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
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
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
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ],
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
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

[!INCLUDE [improve results](../../includes/improve-results-unlabeled.md)]

## <a name="manage-custom-models"></a>Verwalten benutzerdefinierter Modelle

### <a name="get-a-list-of-custom-models"></a>Abrufen einer Liste mit benutzerdefinierten Modellen

Verwenden Sie die **[List Custom Models](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModels)** -API im folgenden Befehl, um eine Liste mit allen benutzerdefinierten Modellen zurückzugeben, die zu Ihrem Abonnement gehören.

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.

#### <a name="request"></a>Anforderung

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

#### <a name="response-body"></a>Antworttext

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

Verwenden Sie die **[Get Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModel)** -API im folgenden Befehl, um ausführliche Informationen zu einem bestimmten benutzerdefinierten Modell abzurufen.

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{modelId}` durch die ID des benutzerdefinierten Modells, nach dem Sie suchen möchten.

### <a name="request"></a>Anforderung

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

#### <a name="request-body"></a>Anforderungstext

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

Sie können ein Modell auch aus Ihrem Konto löschen, indem Sie auf die ID des Modells verweisen. Mit diesem Befehl wird die **[Delete Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/DeleteCustomModel)** -API aufgerufen, um das im vorherigen Abschnitt verwendete Modell zu löschen.

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{modelId}` durch die ID des benutzerdefinierten Modells, nach dem Sie suchen möchten.

### <a name="request"></a>Anforderung

```bash
curl -v -X DELETE "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

Sie erhalten eine `204`-Erfolgsmeldung, in der angegeben ist, dass Ihr Modell zum Löschen markiert wurde. Modellartefakte werden innerhalb von 48 Stunden gelöscht.

## <a name="next-steps"></a>Nächste Schritte

Für dieses Projekt haben Sie die REST-API für die Formularerkennung verwendet, um auf unterschiedliche Weisen Formulare zu analysieren. Lesen Sie als Nächstes die Referenzdokumentation, um die Formularerkennungs-API eingehender kennenzulernen.

> [!div class="nextstepaction"]
> [Referenzdokumentation zur Rest-API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
