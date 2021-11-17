---
title: 'Schnellstart: REST-API für Formularerkennung | Vorschau'
titleSuffix: Azure Applied AI Services
description: Formular- und Dokumentverarbeitung, Datenextraktion und Analyse mit REST-API für Formularerkennung v3.0 (Vorschau)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: b73f742054962e6d598b8313d565d1f15739fca4
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554121"
---
# <a name="quickstart-rest-api---preview"></a>Schnellstart: REST-API | Vorschau

>[!NOTE]
> Formularerkennung v3.0 befindet sich derzeit in der öffentlichen Vorschauphase. Dies bedeutet, dass einige Features unter Umständen nicht unterstützt werden oder nur eingeschränkt verwendbar sind.

| [Formularerkennungs-REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) | [Formularerkennungs-REST-API](/rest/api/azure/) |

Informieren Sie sich über die ersten Schritte mit der Azure-Formularerkennung mit der Programmiersprache C#. Die Azure-Formularerkennung ist ein cloudbasierter Dienst von Azure Applied AI Services, bei dem maschinelles Lernen genutzt wird, um Formularfelder, Text und Tabellen aus Ihren Dokumenten zu extrahieren und zu analysieren. Sie können Formularerkennungsmodelle leicht aufrufen, indem Sie unsere Clientbibliothek-SDKs in Ihre Workflows und Anwendungen integrieren. Sie sollten den kostenlosen Dienst nutzen, wenn Sie die Technologie erlernen. Bedenken Sie, dass die Anzahl der kostenlosen Seiten auf 500 pro Monat beschränkt ist.

Weitere Informationen zu Funktionen der Formularerkennung und zu Entwicklungsoptionen finden Sie auf unserer [Übersichtsseite](../overview.md#form-recognizer-features-and-development-options).
## <a name="form-recognizer-models"></a>Formularerkennungsmodelle

 Die REST-API unterstützt die folgenden Modelle und Funktionen:

* 🆕Allgemeines Dokument: Analysieren und Extrahieren von Text, Tabellen, Struktur, Schlüssel-Wert-Paaren und benannten Entitäten.|
* Layout: Analysieren und Extrahieren von Tabellen, Zeilen, Wörtern und Auswahlmarkierungen, z. B. Optionsfelder und Kontrollkästchen in Formulardokumenten, ohne ein Modell trainieren zu müssen.
* Benutzerdefiniert: Analysieren und Extrahieren von Formularfeldern und anderen Inhalten aus Ihren benutzerdefinierten Formularen mit Modellen, die Sie mit Ihren eigenen Formulartypen trainiert haben.
* Rechnungen: Analysieren und Extrahieren häufig verwendeter Felder aus Rechnungen über ein vortrainiertes Rechnungsmodell.
* Belege: Analysieren und Extrahieren häufig verwendeter Felder aus Belegen über ein vortrainiertes Belegmodell.
* Ausweisdokumente: Analysieren und Extrahieren häufig verwendeter Felder aus Ausweisdokumenten, z. B. Pässen oder Führerscheinen, mit einem vortrainierten Modell für Ausweisdokumente.
* Visitenkarten: Analysieren und Extrahieren häufig verwendeter Felder aus Visitenkarten mit einem vortrainierten Modell für Visitenkarten.

## <a name="analyze-document"></a>Analysieren eines Dokuments

Mit Formularerkennung v3.0 werden das Analysedokument und GET-Vorgänge (Get Analyze Result) für das Layout, vordefinierte Modelle und benutzerdefinierte Modelle zu einem zentralen Vorgangspaar zusammengefasst, indem den POST- und GET-Vorgängen  `modelIds` zugewiesen werden:

```http
POST /documentModels/{modelId}:analyze

GET /documentModels/{modelId}/analyzeResults/{resultId}
```

In der folgenden Tabelle sind die Aktualisierungen der REST-API-Aufrufe dargestellt.

|Funktion| v2.1 | v3.0|
|-----|-----|----|
|Allgemeines Dokument | – |`/documentModels/prebuilt-document:analyze` |
|Layout |`/layout/analyze` | ``/documentModels/prebuilt-layout:analyze``|
|Rechnung | `/prebuilt/invoice/analyze` | `/documentModels/prebuilt-invoice:analyze` |
|Rechnung | `/prebuilt/receipt/analyze` | `/documentModels/prebuilt-receipt:analyze` |
|ID-Dokument| `/prebuilt/idDocument/analyze` | `/documentModels/prebuilt-idDocument:analyze`|
|Visitenkarte| `/prebuilt/businessCard/analyze`  | `/documentModels/prebuilt-businessCard:analyze` |
|Benutzerdefiniert| `/custom/{modelId}/analyze` |`/documentModels/{modelId}:analyze`|

In dieser Schnellstartanleitung verwenden Sie die folgenden Funktionen, um Daten und Werte aus Formularen und Dokumenten zu analysieren und zu extrahieren:

* [🆕 **Allgemeines Dokument:**](#try-it-general-document-model) Analysieren und Extrahieren von Text, Tabellen, Strukturen, Schlüssel-Wert-Paaren und benannten Entitäten

* [**Layout:**](#try-it-layout-model) Analysieren und Extrahieren von Tabellen, Zeilen, Wörtern und Auswahlmarkierungen, z. B. Optionsfeldern und Kontrollkästchen in Formulardokumenten, ohne ein Modell trainieren zu müssen

* [**Vordefiniertes Modell:**](#try-it-prebuilt-model) Analysieren und Extrahieren von Daten aus gängigen Dokumenttypen mit einem vorab trainierten Modell

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)

* [cURL](https://curl.haxx.se/windows/) muss installiert sein.

* [PowerShell-Version 6.0 oder höher](/powershell/scripting/install/installing-powershell-core-on-windows) oder eine ähnliche Befehlszeilenanwendung

* Eine Cognitive Services- oder Formularerkennungsressource. Wenn Sie über Ihr Azure-Abonnement verfügen, können Sie im Azure-Portal eine Formularerkennungsressource mit [einem Dienst](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oder [mehreren Diensten](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) erstellen, um Ihren Schlüssel und Endpunkt zu erhalten. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

> [!TIP]
> Erstellen Sie eine Cognitive Services-Ressource, wenn Sie planen, über einen einzelnen Endpunkt bzw. Schlüssel auf mehrere Cognitive Services-Instanzen zuzugreifen. Erstellen Sie eine Formularerkennungsressource, falls nur auf die Formularerkennung zugegriffen werden soll. Beachten Sie hierbei, dass Sie eine Ressource mit einem einzelnen Dienst benötigen, falls Sie die [Azure Active Directory-Authentifizierung](../../../active-directory/authentication/overview-authentication.md) nutzen möchten.

* Wählen Sie nach der Bereitstellung der Ressource **Zu Ressource wechseln** aus. Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt:

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal.":::

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>Wählen Sie ein Codebeispiel zum Kopieren und Einfügen in Ihre Anwendung aus:

* [**Allgemeines Dokument**](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [**Vordefiniertes Modell**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. Verwenden Sie in der Produktionsumgebung sichere Methoden, um Ihre Anmeldeinformationen zu speichern und darauf zuzugreifen. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../cognitive-services/cognitive-services-security.md).

## <a name="try-it-general-document-model"></a>**Try it** (Ausprobieren): Allgemeines Dokumentmodell

> [!div class="checklist"]
>
> * Für dieses Beispiel benötigen Sie eine **Formulardokumentdatei unter einem URI**. Sie können für diese Schnellstartanleitung unser [Beispielformulardokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) verwenden. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{your-document-url}` durch die URL eines Beispielformulardokuments.

#### <a name="request"></a>Anforderung

```bash
 curl -v -i POST "https://{endpoint}/formrecognizer/documentModels/prebuilt-document:analyze?api-version=2021-09-30-preview HTTP/1.1" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

Sie erhalten die Antwort `202 (Success)` mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Ergebnis-ID, mit der Sie den Status des asynchronen Vorgangs abfragen und die Ergebnisse abrufen können:

https://{host}/formrecognizer/documentModels/{modelId}/analyzeResults/**{resultId}**?api-version=2021-07-30-preview

### <a name="get-general-document-results"></a>Abrufen von Ergebnissen für allgemeine Dokumente

Rufen Sie nach dem Aufrufen der **[Analyze Document](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)**-API die **[Get Analyze Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetAnalyzeDocumentResult)**-API auf, um den Status des Vorgangs und die extrahierten Daten abzurufen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{resultId}` durch die Ergebnis-ID aus dem vorherigen Schritt.
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>Anforderung

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/prebuilt-document/analyzeResults/{resultId}?api-version=2021-09-30-preview"
```

### <a name="examine-the-response"></a>Untersuchen der Antwort

Sie erhalten die Antwort `200 (Success)` mit der folgenden JSON-Ausgabe: Das erste Feld, `"status"`, gibt den Status des Vorgangs an. Wenn der Vorgang nicht abgeschlossen ist, ist der Wert von `"status"``"running"` oder `"notStarted"`, und Sie sollten die API Aufrufen entweder manuell oder über ein Skript erneut aufrufen. Ein Intervall von mindestens einer Sekunde zwischen den Aufrufen wird empfohlen.

Der Knoten `"analyzeResults"` enthält den gesamten erkannten Text. Text ist nach Seiten, Zeilen, Tabellen, Schlüssel-Wert-Paaren und Entitäten strukturiert.

#### <a name="sample-response"></a>Beispiel für eine Antwort

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-09-28T16:52:51Z",
    "lastUpdatedDateTime": "2021-09-28T16:53:08Z",
    "analyzeResult": {
        "apiVersion": "2021-09-30-preview",
        "modelId": "prebuilt-document",
        "stringIndexType": "textElements",
        "content": "content extracted",
        "pages": [
            {
                "pageNumber": 1,
                "angle": 0,
                "width": 8.4722,
                "height": 11,
                "unit": "inch",
                "words": [
                    {
                        "content": "Case",
                        "boundingBox": [
                            1.3578,
                            0.2244,
                            1.7328,
                            0.2244,
                            1.7328,
                            0.3502,
                            1.3578,
                            0.3502
                        ],
                        "confidence": 1,
                        "span": {
                            "offset": 0,
                            "length": 4
                        }
                    }

                ],
                "lines": [
                    {
                        "content": "Case",
                        "boundingBox": [
                            1.3578,
                            0.2244,
                            3.2879,
                            0.2244,
                            3.2879,
                            0.3502,
                            1.3578,
                            0.3502
                        ],
                        "spans": [
                            {
                                "offset": 0,
                                "length": 22
                            }
                        ]
                    }
                ]
            }
        ],
        "tables": [
            {
                "rowCount": 8,
                "columnCount": 3,
                "cells": [
                    {
                        "kind": "columnHeader",
                        "rowIndex": 0,
                        "columnIndex": 0,
                        "rowSpan": 1,
                        "columnSpan": 1,
                        "content": "Applicant's Name:",
                        "boundingRegions": [
                            {
                                "pageNumber": 1,
                                "boundingBox": [
                                    1.9198,
                                    4.277,
                                    3.3621,
                                    4.2715,
                                    3.3621,
                                    4.5034,
                                    1.9198,
                                    4.5089
                                ]
                            }
                        ],
                        "spans": [
                            {
                                "offset": 578,
                                "length": 17
                            }
                        ]
                    }
                ],
                "spans": [
                    {
                        "offset": 578,
                        "length": 300
                    },
                    {
                        "offset": 1358,
                        "length": 10
                    }
                ]
            }
        ],
        "keyValuePairs": [
            {
                "key": {
                    "content": "Case",
                    "boundingRegions": [
                        {
                            "pageNumber": 1,
                            "boundingBox": [
                                1.3578,
                                0.2244,
                                1.7328,
                                0.2244,
                                1.7328,
                                0.3502,
                                1.3578,
                                0.3502
                            ]
                        }
                    ],
                    "spans": [
                        {
                            "offset": 0,
                            "length": 4
                        }
                    ]
                },
                "value": {
                    "content": "A Case",
                    "boundingRegions": [
                        {
                            "pageNumber": 1,
                            "boundingBox": [
                                1.8026,
                                0.2276,
                                3.2879,
                                0.2276,
                                3.2879,
                                0.3502,
                                1.8026,
                                0.3502
                            ]
                        }
                    ],
                    "spans": [
                        {
                            "offset": 5,
                            "length": 17
                        }
                    ]
                },
                "confidence": 0.867
            }
        ],
        "entities": [
            {
                "category": "Person",
                "content": "Jim Smith",
                "boundingRegions": [
                    {
                        "pageNumber": 1,
                        "boundingBox": [
                            3.4672,
                            4.3255,
                            5.7118,
                            4.3255,
                            5.7118,
                            4.4783,
                            3.4672,
                            4.4783
                        ]
                    }
                ],
                "confidence": 0.93,
                "spans": [
                    {
                        "offset": 596,
                        "length": 21
                    }
                ]
            }
        ],
        "styles": [
            {
                "isHandwritten": true,
                "confidence": 0.95,
                "spans": [
                    {
                        "offset": 565,
                        "length": 12
                    },
                    {
                        "offset": 3493,
                        "length": 1
                    }
                ]
            }
        ]
    }
}

```

## <a name="try-it-layout-model"></a>**Try it** (Ausprobieren): Layoutmodell

> [!div class="checklist"]
>
> * Für dieses Beispiel benötigen Sie eine **Formulardokumentdatei unter einem URI**. Sie können für diese Schnellstartanleitung unser [Beispielformulardokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) verwenden.

 Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `"{your-document-url}` durch eine der Beispiel-URLs.

#### <a name="request"></a>Anforderung

```bash
bash
 curl -v -i POST "https://{endpoint}/formrecognizer/documentModels/prebuilt-layout:analyze?api-version=2021-09-30-preview HTTP/1.1" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

Sie erhalten die Antwort `202 (Success)` mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Ergebnis-ID, mit der Sie den Status des asynchronen Vorgangs abfragen und die Ergebnisse abrufen können:

`https://{host}/formrecognizer/documentModels/{modelId}/analyzeResults/**{resultId}**?api-version=2021-07-30-preview`

### <a name="get-layout-results"></a>Abrufen von Layoutergebnissen

Rufen Sie nach dem Aufrufen der **[Analyze Document](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-layout:analyze?api-version=2021-09-30-preview&stringIndexType=textElements)**-API die **[Get Analyze Result](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-layout/analyzeResults/{resultId}?api-version=2021-09-30-preview)**-API auf, um den Status des Vorgangs und die extrahierten Daten abzurufen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{resultId}` durch die Ergebnis-ID aus dem vorherigen Schritt.
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>Anforderung

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/prebuilt-layout/analyzeResults/{resultId}?api-version=2021-09-30-preview"
```

### <a name="examine-the-response"></a>Untersuchen der Antwort

Sie erhalten die Antwort `200 (Success)` mit der folgenden JSON-Ausgabe: Das erste Feld, `"status"`, gibt den Status des Vorgangs an. Wenn der Vorgang nicht abgeschlossen ist, ist der Wert von `"status"``"running"` oder `"notStarted"`, und Sie sollten die API Aufrufen entweder manuell oder über ein Skript erneut aufrufen. Ein Intervall von mindestens einer Sekunde zwischen den Aufrufen wird empfohlen.

## <a name="try-it-prebuilt-model"></a>**Probieren Sie es aus:** Vordefiniertes Modell

In diesem Beispiel wird veranschaulicht, wie Sie Daten aus bestimmten Arten von häufig verwendeten Dokumenttypen mit vortrainierten Modellen analysieren, wobei eine Rechnung als Beispiel verwendet wird.

> [!div class="checklist"]
>
> * In diesem Beispiel analysieren Sie ein Rechnungsdokument mithilfe eines vordefinierten Modells. Für diese Schnellstartanleitung können Sie unser [Beispiel für ein Rechnungsdokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) verwenden.

### <a name="choose-the-invoice-prebuilt-model-id"></a>Auswählen der ID für das vordefinierte Rechnungsmodell

Sie sind hierbei nicht auf Rechnungen beschränkt, sondern können zwischen mehreren vordefinierten Modellen wählen, von denen jedes über eine Gruppe unterstützter Felder verfügt. Welches Modell für den Analysevorgang verwendet wird, hängt vom Typ des zu analysierenden Dokuments ab. Hier sind die Modell-IDs für die vordefinierten Modelle angegeben, die vom Dienst „Formularerkennung“ derzeit unterstützt werden:

* **prebuilt-invoice**: Dient zum Extrahieren von Text, Auswahlmarkierungen, Tabellen, Schlüssel-Wert-Paaren und wichtigen Informationen aus Rechnungen.
* **prebuilt-businessCard**: Dient zum Extrahieren von Text und wichtigen Informationen aus Visitenkarten.
* **prebuilt-idDocument**: Dient zum Extrahieren von Text und wichtigen Informationen aus Führerscheinen und Reisepässen.
* **prebuilt-receipt**: Dient zum Extrahieren von Text und wichtigen Informationen aus Belegen.

Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `\"{your-document-url}` durch eine URL für ein Rechnungsbeispiel:

    ```http
    https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf
    ```

#### <a name="request"></a>Anforderung

```bash
 curl -v -i POST "https://{endpoint}/formrecognizer/documentModels/prebuilt-invoice:analyze?api-version=2021-09-30-preview HTTP/1.1" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

Sie erhalten die Antwort `202 (Success)` mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Ergebnis-ID, mit der Sie den Status des asynchronen Vorgangs abfragen und die Ergebnisse abrufen können:

https://{host}/formrecognizer/documentModels/{modelId}/analyzeResults/**{resultId}**?api-version=2021-07-30-preview

### <a name="get-invoice-results"></a>Abrufen von Rechnungsergebnissen

Rufen Sie nach dem Aufrufen der **[Analyze Document](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-invoice:analyze?api-version=2021-09-30-preview&stringIndexType=textElements)**-API die **[Get Analyze Result](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-invoice/analyzeResults/{resultId}?api-version=2021-09-30-preview)**-API auf, um den Status des Vorgangs und die extrahierten Daten abzurufen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{resultId}` durch die Ergebnis-ID aus dem vorherigen Schritt.
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>Anforderung

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/prebuilt-invoice/analyzeResults/{resultId}?api-version=2021-09-30-preview"
```

### <a name="examine-the-response"></a>Untersuchen der Antwort

Sie erhalten die Antwort `200 (Success)` mit der folgenden JSON-Ausgabe: Das erste Feld, `"status"`, gibt den Status des Vorgangs an. Wenn der Vorgang nicht abgeschlossen ist, ist der Wert von `"status"``"running"` oder `"notStarted"`, und Sie sollten die API Aufrufen entweder manuell oder über ein Skript erneut aufrufen. Ein Intervall von mindestens einer Sekunde zwischen den Aufrufen wird empfohlen.

### <a name="improve-results"></a>Verbessern der Ergebnisse

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="manage-custom-models"></a>Verwalten benutzerdefinierter Modelle

### <a name="get-a-list-of-models"></a>Abrufen einer Liste mit Modellen

Bei der   [List Models](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetModels)-Anforderung der Vorschauversion 3.0 wird eine paginierte Liste mit vordefinierten und benutzerdefinierten Modellen zurückgegeben. In dieser Liste sind nur Modelle mit dem Status „Erfolgreich“ enthalten. Modelle mit dem Status „In Bearbeitung“ oder „Fehler“ können über die [List Operations](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetOperations)-Anforderung enumeriert werden. Verwenden Sie die „nextLink“-Eigenschaft, um auf die nächste Seite mit Modellen zuzugreifen (falls zutreffend). Um weitere Informationen zu den einzelnen zurückgegebenen Modellen zu erhalten, z. B. die Liste mit unterstützten Dokumenten und den zugehörigen Feldern, übergeben Sie die „modelId“ für die   [Get Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetOperations)-Anforderung.

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels?api-version=2021-07-30-preview"
```

### <a name="get-a-specific-model"></a>Abrufen eines bestimmten Modells

Mit der [Get Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetModel)-Anforderung der Vorschauversion 3.0 werden Informationen zu einem bestimmten Modell mit dem Status „Erfolgreich“ abgerufen. Verwenden Sie für Modelle mit dem Status „Fehler“ und „In Bearbeitung“ die [Get Operation](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetOperation)-Anforderung, um den Status der Vorgänge für die Modellerstellung und die damit verbundenen Fehler nachzuverfolgen.

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-07-30-preview"
```

### <a name="delete-a-model"></a>Löschen eines Modells

Mit der [Delete Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/DeleteModel)-Anforderung der Vorschauversion 3.0 wird das benutzerdefinierte Modell entfernt, und von weiteren Vorgängen kann dann nicht mehr auf die „modelId“ zugegriffen werden.  Neue Modelle können mit der gleichen „modelId“ erstellt werden, ohne dass es zu einem Konflikt kommt.

```bash
curl -v -X DELETE "https://{endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-07-30-preview"
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie die Vorschauversion der REST-API für die Formularerkennung (v3.0) verwendet, um auf unterschiedliche Weise Formulare zu analysieren. Lesen Sie als Nächstes die Referenzdokumentation, um die Formularerkennungs-API eingehender kennenzulernen.

> [!div class="nextstepaction"]
> [Referenzdokumentation zur Vorschauversion (v3.0) der REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)
