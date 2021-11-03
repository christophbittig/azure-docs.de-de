---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 243e9842274e932d67e208b0e2bced225f8c349f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029780"
---
[Referenzdokumentation](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/)


## <a name="prerequisites"></a>Voraussetzungen

* Aktuelle Version von [cURL](https://curl.haxx.se/).
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie im Azure-Portal <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Erstellen einer Sprachressource"  target="_blank">eine Sprachressource erstellen</a>, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

> [!NOTE]
> * In den folgenden Bash-Beispielen wird das Zeilenfortsetzungszeichen `\` verwendet. Verwenden Sie dieses Zeichen, falls in Ihrer Konsole oder Ihrem Terminal ein anderes Zeilenfortsetzungszeichen genutzt wird.
> * Sprachspezifische Beispiele finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code).
> * Navigieren Sie zum Azure-Portal, und suchen Sie den Schlüssel und den Endpunkt für die in den Voraussetzungen erstellte Sprachressource. Sie befinden sich auf der Seite mit dem **Schlüssel und Endpunkt** der Ressource unter **Ressourcenverwaltung**. Ersetzen Sie dann die Zeichenfolgen im unten angegebenen Code durch Ihren Schlüssel und Endpunkt.
Sie benötigen die folgenden Informationen, um die API aufzurufen:


|parameter  |BESCHREIBUNG  |
|---------|---------|
|`-X POST <endpoint>`     | Gibt den Endpunkt für den Zugriff auf die API an        |
|`-H Content-Type: application/json`     | Der Inhaltstyp zum Senden von JSON-Daten          |
|`-H "Ocp-Apim-Subscription-Key:<key>`    | Gibt den Schlüssel für den Zugriff auf die API an        |
|`-d <documents>`     | Die JSON-Datei, die die zu sendenden Dokumente enthält         |

Die folgenden cURL-Befehle werden über eine Bash-Shell ausgeführt. Fügen Sie in diese Befehle Ihren Ressourcennamen und Ressourcenschlüssel sowie Ihre JSON-Werte ein.


## <a name="sentiment-analysis-and-opinion-mining"></a>Standpunktanalyse und Opinion Mining

[!INCLUDE [REST API quickstart instructions](../../../includes/rest-api-instructions.md)]

> [!NOTE]
> Die folgenden Beispiele enthalten eine Anfrage für die Opinion Mining-Funktion der Stimmungsanalyse mit dem Parameter `opinionMining=true`, die granulare Informationen über Bewertungen (Adjektive) in Bezug auf Ziele (Substantive) im Text liefert.

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.2-preview.1/sentiment?opinionMining=true \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "The customer service here is really good."}]}'
```

### <a name="json-response"></a>JSON-Antwort

```json
{
   "documents":[
      {
         "id":"1",
         "sentiment":"positive",
         "confidenceScores":{
            "positive":1.0,
            "neutral":0.0,
            "negative":0.0
         },
         "sentences":[
            {
               "sentiment":"positive",
               "confidenceScores":{
                  "positive":1.0,
                  "neutral":0.0,
                  "negative":0.0
               },
               "offset":0,
               "length":41,
               "text":"The customer service here is really good.",
               "targets":[
                  {
                     "sentiment":"positive",
                     "confidenceScores":{
                        "positive":1.0,
                        "negative":0.0
                     },
                     "offset":4,
                     "length":16,
                     "text":"customer service",
                     "relations":[
                        {
                           "relationType":"assessment",
                           "ref":"#/documents/0/sentences/0/assessments/0"
                        }
                     ]
                  }
               ],
               "assessments":[
                  {
                     "sentiment":"positive",
                     "confidenceScores":{
                        "positive":1.0,
                        "negative":0.0
                     },
                     "offset":36,
                     "length":4,
                     "text":"good",
                     "isNegated":false
                  }
               ]
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-04-01"
}
```
