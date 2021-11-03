---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: a13a627cfe30121b84ecb18fb1af6b52a396bbcc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100316"
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


## <a name="text-analytics-for-health"></a>Textanalyse für Gesundheit

[!INCLUDE [REST API quickstart instructions](../../../includes/rest-api-instructions.md)]

```bash
curl -i -X POST https://your-text-analytics-endpoint-here/text/analytics/v3.1/entities/health/jobs \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: your-text-analytics-key-here" \
-d '{ documents: [{ id: "1", language:"en", text: "Subject was administered 100mg remdesivir intravenously over a period of 120 min"}]}'
```

Rufen Sie `operation-location` aus dem Antwortheader ab. Der Wert sieht etwa wie die folgende URL aus:

```rest
https://your-resource.cognitiveservices.azure.com/text/analytics/v3.1/entities/health/jobs/12345678-1234-1234-1234-12345678
```

Verwenden Sie den folgenden cURL-Befehl, um die Ergebnisse der Anforderung abzurufen. Ersetzen Sie `my-job-id` durch den numerischen ID-Wert, den Sie aus dem vorherigen `operation-location`-Antwortheader erhalten haben:

```bash
curl -X GET  https://your-text-analytics-endpoint-here/text/analytics/v3.1/entities/health/jobs/my-job-id \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: your-text-analytics-key-here"
```


### <a name="json-response"></a>JSON-Antwort

```json
{
   "jobId":"12345678-1234-1234-1234-12345678",
   "lastUpdateDateTime":"2021-08-02T21:32:43Z",
   "createdDateTime":"2021-08-02T21:32:04Z",
   "expirationDateTime":"2021-08-03T21:32:04Z",
   "status":"succeeded",
   "errors":[
      
   ],
   "results":{
      "documents":[
         {
            "id":"1",
            "entities":[
               {
                  "offset":25,
                  "length":5,
                  "text":"100mg",
                  "category":"Dosage",
                  "confidenceScore":1.0
               },
               {
                  "offset":31,
                  "length":10,
                  "text":"remdesivir",
                  "category":"MedicationName",
                  "confidenceScore":1.0,
                  "name":"remdesivir",
                  "links":[
                     {
                        "dataSource":"UMLS",
                        "id":"C4726677"
                     },
                     {
                        "dataSource":"DRUGBANK",
                        "id":"DB14761"
                     },
                     {
                        "dataSource":"GS",
                        "id":"6192"
                     },
                     {
                        "dataSource":"MEDCIN",
                        "id":"398132"
                     },
                     {
                        "dataSource":"MMSL",
                        "id":"d09540"
                     },
                     {
                        "dataSource":"MSH",
                        "id":"C000606551"
                     },
                     {
                        "dataSource":"MTHSPL",
                        "id":"3QKI37EEHE"
                     },
                     {
                        "dataSource":"NCI",
                        "id":"C152185"
                     },
                     {
                        "dataSource":"NCI_FDA",
                        "id":"3QKI37EEHE"
                     },
                     {
                        "dataSource":"NDDF",
                        "id":"018308"
                     },
                     {
                        "dataSource":"RXNORM",
                        "id":"2284718"
                     },
                     {
                        "dataSource":"SNOMEDCT_US",
                        "id":"870592005"
                     },
                     {
                        "dataSource":"VANDF",
                        "id":"4039395"
                     }
                  ]
               },
               {
                  "offset":42,
                  "length":13,
                  "text":"intravenously",
                  "category":"MedicationRoute",
                  "confidenceScore":0.99
               },
               {
                  "offset":73,
                  "length":7,
                  "text":"120 min",
                  "category":"Time",
                  "confidenceScore":0.98
               }
            ],
            "relations":[
               {
                  "relationType":"DosageOfMedication",
                  "entities":[
                     {
                        "ref":"#/results/documents/0/entities/0",
                        "role":"Dosage"
                     },
                     {
                        "ref":"#/results/documents/0/entities/1",
                        "role":"Medication"
                     }
                  ]
               },
               {
                  "relationType":"RouteOfMedication",
                  "entities":[
                     {
                        "ref":"#/results/documents/0/entities/1",
                        "role":"Medication"
                     },
                     {
                        "ref":"#/results/documents/0/entities/2",
                        "role":"Route"
                     }
                  ]
               },
               {
                  "relationType":"TimeOfMedication",
                  "entities":[
                     {
                        "ref":"#/results/documents/0/entities/1",
                        "role":"Medication"
                     },
                     {
                        "ref":"#/results/documents/0/entities/3",
                        "role":"Time"
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
      "modelVersion":"2021-05-15"
   }
}
```
