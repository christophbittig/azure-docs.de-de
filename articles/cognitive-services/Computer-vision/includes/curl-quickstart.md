---
title: 'Schnellstart: Optische Zeichenerkennung REST-API'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung erhalten Sie erste Schritte mit der optischen Zeichen Erkennungs-REST-API-API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ceeb1804c9332d9e0d3e11336ff92e8aacc8516c
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129584995"
---
Verwenden Sie die optische Zeichen Erkennungs-REST-API, um gedruckten und handschriftlichen Text zu lesen.

> [!NOTE]
> In dieser Schnellstartanleitung wird die REST-API mithilfe von cURL-Befehlen aufgerufen. Die REST-API kann aber auch mit einer Programmiersprache aufgerufen werden. Auf GitHub stehen Beispiele für [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST) und [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST) zur Verfügung.

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: [Kostenloses Azure-Konto](https://azure.microsoft.com/free/cognitive-services/) 
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Erstellen einer Ressource für maschinelles Sehen"  target="_blank"> im Azure-Portal eine Ressource für maschinelles Sehen </a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
  * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um eine Verbindung Ihrer Anwendung mit dem Dienst für maschinelles Sehen herzustellen. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
  * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* [cURL](https://curl.haxx.se/) muss installiert sein.


## <a name="extract-printed-and-handwritten-text"></a>Extrahieren von gedrucktem und handschriftlichem Text

Der OCR-Dienst kann sichtbaren Text in einem Bild oder Dokument extrahieren und in einen Zeichendatenstrom konvertieren. Weitere Informationen zur Textextraktion finden Sie in der Übersicht [Optische Zeichenerkennung (OCR)](../overview-ocr.md).


### <a name="call-the-read-api"></a>Aufrufen der Lese-API

Führen Sie zum Erstellen und Ausführen des Beispiels die folgenden Schritte aus:

1. Kopieren Sie den folgenden Befehl, und fügen Sie ihn in einen Text-Editor ein.
1. Nehmen Sie die folgenden Änderungen im Befehl vor, falls dies erforderlich ist:
    1. Ersetzen Sie den `<subscriptionKey>`-Wert durch Ihren Abonnementschlüssel.
    1. Ersetzen Sie den ersten Teil der Anforderungs-URL (`westcentralus`) durch den Text in Ihrer eigenen Endpunkt-URL.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Ändern Sie optional die Bild-URL im Anforderungstext (`https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\`) in die URL eines anderen Bilds, das analysiert werden soll.
1. Öffnen Sie ein Eingabeaufforderungsfenster.
1. Fügen Sie den Befehl aus dem Text-Editor in das Eingabeaufforderungsfenster ein, und führen Sie den Befehl aus.

```bash
curl -v -X POST "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

Die Antwort enthält einen Header vom Typ `Operation-Location`, bei dessen Wert es sich um eine eindeutige URL handelt. Diese URL wird verwendet, um die Ergebnisse des Lesevorgangs abzufragen. Die URL ist 48 Stunden lang gültig.

### <a name="how-to-use-preview-features"></a>Verwenden von Previewfunktionen
Informationen zu den Sprachen und Features während der Vorschau finden Sie unter [Angeben der Modellversion](../Vision-API-How-to-Topics/call-read-api.md#determine-how-to-process-the-data-optional) zur Verwendung der neuesten Vorschauversion. Das Vorschaumodell enthält alle Verbesserungen an den derzeit allgemein verfügbaren Sprachen und Features.

### <a name="get-read-results"></a>Abrufen der Leseergebnisse

1. Kopieren Sie den folgenden Befehl in Ihren Text-Editor.
1. Ersetzen Sie die URL durch den Wert `Operation-Location`, den Sie im vorherigen Schritt kopiert haben.
1. Nehmen Sie die folgenden Änderungen im Befehl vor, falls dies erforderlich ist:
    1. Ersetzen Sie den `<subscriptionKey>`-Wert durch Ihren Abonnementschlüssel.
1. Öffnen Sie ein Eingabeaufforderungsfenster.
1. Fügen Sie den Befehl aus dem Text-Editor in das Eingabeaufforderungsfenster ein, und führen Sie den Befehl aus.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{body}" 
```

### <a name="examine-the-response"></a>Untersuchen der Antwort

Eine erfolgreiche Antwort wird im JSON-Format zurückgegeben. Die Beispielanwendung analysiert eine Antwort und zeigt diese bei erfolgreicher Ausführung im Eingabeaufforderungsfenster an. Dies wird im folgenden Beispiel veranschaulicht:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-04-08T21:56:17.6819115+00:00",
  "lastUpdatedDateTime": "2021-04-08T21:56:18.4161316+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 338,
        "height": 479,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              25,
              14,
              318,
              14,
              318,
              59,
              25,
              59
            ],
            "text": "NOTHING",
            "appearance": {
              "style": {
                "name": "other",
                "confidence": 0.971
              }
            },
            "words": [
              {
                "boundingBox": [
                  27,
                  15,
                  294,
                  15,
                  294,
                  60,
                  27,
                  60
                ],
                "text": "NOTHING",
                "confidence": 0.994
              }
            ]
          }
        ]
      }
    ]
  }
}

```



## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie die Read-REST-API aufrufen. Informieren Sie sich als Nächstes ausführlicher über die Funktionen der Lese-API.

> [!div class="nextstepaction"]
>[Aufrufen der Lese-API](../Vision-API-How-to-Topics/call-read-api.md)

* [OCR-Übersicht](../overview-ocr.md)
