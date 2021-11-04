---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 882355f0c4e6a44300182ffd27eb4cc422422f5f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100868"
---
## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Aktuelle Version von [cURL](https://curl.haxx.se/).
* Eine Sprachressource. Wenn Sie noch keine besitzen, können Sie mithilfe des [Azure-Portals](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) eine erstellen. Wenn Sie eine neue Ressource erstellen, klicken Sie auf den Link, befolgen Sie die Schritte und warten Sie auf die Bereitstellung der Ressource. Klicken Sie dann auf **Zu Ressource wechseln**.

## <a name="get-your-resource-keys-and-endpoint"></a>Abrufen Ihrer Ressourcenschlüssel und Endpunkte

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/#home) zur Übersichtsseite Ihrer Ressource.
2. Wählen Sie im Menü auf der linken Seite **Schlüssel und Endpunkt** aus. Sie verwenden den Endpunkt und Schlüssel für die API-Anforderungen. 

    :::image type="content" source="../../../media/azure-portal-resource-credentials.png" alt-text="Screenshot der Schlüssel- und Endpunktseite im Azure-Portal" lightbox="../../../media/azure-portal-resource-credentials.png":::

## <a name="import-a-project"></a>Importieren eines Projekts

Für die ersten Schritte können Sie einen CLU-JSON-Code in den Dienst importieren. Die Schnellstartanleitung enthält unten einen JSON-Beispielcode, der einige Absichten und Entitäten für eine E-Mail-Anwendung namens „EmailProject“ einrichtet. 

Erstellen Sie eine **POST**-Anforderung mithilfe der folgenden URL, der Header und des JSON-Texts, um Ihr Projekt zu erstellen.

### <a name="request-url"></a>Anfrage-URL

Verwenden Sie zum Erstellen Ihrer API-Anforderung die folgende URL. Ersetzen Sie die folgenden Platzhalter durch Ihre eigenen Werte. 

```rest
{YOUR-ENDPOINT}/language/analyze-conversation/projects/EmailProject/:import?api-version=2021-11-01-preview
```

|Platzhalter  |Wert  | Beispiel |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Der Endpunkt für die Authentifizierung Ihrer API-Anforderung.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="headers"></a>Header

Verwenden Sie den folgenden Header, um Ihre Anforderung zu authentifizieren. 

|Schlüssel|Wert|
|--|--|
|`Ocp-Apim-Subscription-Key`| Der Schlüssel für Ihre Ressource. Wird für die Authentifizierung Ihrer API-Anforderungen verwendet.|

### <a name="body"></a>Body

Verwenden Sie den folgenden JSON-Beispieltext.

```json
{
    "api-version": "2021-11-01-preview",
    "metadata": {
        "name": "EmailProject",
        "description": "",
        "type": "Conversation",
        "multilingual": true,
        "language": "en-us"
    },
    "assets": {
        "intents": [
            {
                "name": "Read"
            },
            {
                "name": "Delete"
            },
            {
                "name": "Attach"
            }
        ],
        "entities": [
            {
                "name": "Sender"
            },
            {
                "name": "FileName"
            },
            {
                "name": "FileType"
            }
        ],
        "examples": [
            {
                "text": "add the pdf file with the name signed contract",
                "language": "en-us",
                "intent": "Attach",
                "entities": [
                    {
                        "entity": "FileName",
                        "offset": 31,
                        "length": 15
                    },
                    {
                        "entity": "FileType",
                        "offset": 8,
                        "length": 3
                    }
                ]
            },
            {
                "text": "attach the powerpoint file",
                "language": "en-us",
                "intent": "Attach",
                "entities": [
                    {
                        "entity": "FileType",
                        "offset": 11,
                        "length": 10
                    }
                ]
            },
            {
                "text": "attach the excel file called reports q1",
                "language": "en-us",
                "intent": "Attach",
                "entities": [
                    {
                        "entity": "FileName",
                        "offset": 29,
                        "length": 10
                    },
                    {
                        "entity": "FileType",
                        "offset": 11,
                        "length": 5
                    }
                ]
            },
            {
                "text": "move this to the deleted folder",
                "language": "en-us",
                "intent": "Delete",
                "entities": []
            },
            {
                "text": "remove this one",
                "language": "en-us",
                "intent": "Delete",
                "entities": []
            },
            {
                "text": "delete this",
                "language": "en-us",
                "intent": "Delete",
                "entities": []
            },
            {
                "text": "delete my last email from martha",
                "language": "en-us",
                "intent": "Delete",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 26,
                        "length": 6
                    }
                ]
            },
            {
                "text": "what did the email from matt say",
                "language": "en-us",
                "intent": "Read",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 24,
                        "length": 4
                    }
                ]
            },
            {
                "text": "read john's email for me",
                "language": "en-us",
                "intent": "Read",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 5,
                        "length": 4
                    }
                ]
            },
            {
                "text": "read the email from carol",
                "language": "en-us",
                "intent": "Read",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 20,
                        "length": 5
                    }
                ]
            }
        ]
    }
}
```

## <a name="start-training-your-model"></a>Beginnen mit dem Trainieren Ihres Modells

Nachdem das Projekt importiert wurde, können Sie mit dem Trainieren eines Modells beginnen. Erstellen Sie eine **POST**-Anforderung mithilfe der folgenden URL, der Header und des JSON-Texts, um mit dem Training zu beginnen.

### <a name="request-url"></a>Anfrage-URL

Verwenden Sie zum Erstellen Ihrer API-Anforderung die folgende URL. Ersetzen Sie die folgenden Platzhalter durch Ihre eigenen Werte. 

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/:train?api-version=2021-11-01-preview
```

|Platzhalter  |Wert  | Beispiel |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Der Endpunkt für die Authentifizierung Ihrer API-Anforderung.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="headers"></a>Header

Verwenden Sie den folgenden Header, um Ihre Anforderung zu authentifizieren. 

|Schlüssel|Wert|
|--|--|
|`Ocp-Apim-Subscription-Key`| Der Schlüssel für Ihre Ressource. Wird für die Authentifizierung Ihrer API-Anforderungen verwendet.|

### <a name="request-body"></a>Anforderungstext

Verwenden Sie das folgende Objekt in Ihrer Anforderung. Das Modell wird `MyModel` benannt, nachdem das Training abgeschlossen ist.  

```json
{
  "modelLabel":"MyModel",
  "RunVerification":false
}
```

Nachdem Sie Ihre API-Anforderung gesendet haben, erhalten Sie eine `202`-Antwort, die angibt, dass sie erfolgreich war. Extrahieren Sie in den Antwortheadern den `location`-Wert. Er weist das folgende Format auf: 

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/train/jobs/{JOB-ID}?api-version=2021-11-01-preview
``` 

`JOB-ID` wird verwendet, um Ihre Anforderung zu identifizieren, da es sich um einen asynchronen Vorgang handelt. Sie verwenden diese URL im nächsten Schritt, um den Trainingsstatus abzurufen. 

## <a name="get-training-status"></a>Trainingsstatus abrufen

Verwenden Sie die folgende **GET**-Anforderung, um den Status des Trainingsprozesses Ihres Modells abzufragen. Sie können die URL verwenden, die Sie im vorherigen Schritt erhalten haben, oder die Platzhalterwerte unten durch Ihre eigenen Werte ersetzen. 

### <a name="request-url"></a>Anfrage-URL

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/train/jobs/{JOB-ID}?api-version=2021-11-01-preview
```

|Platzhalter  |Wert  | Beispiel |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Der Endpunkt für die Authentifizierung Ihrer API-Anforderung.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{JOB-ID}`     | Die ID zum Ermitteln des Trainingsstatus Ihres Modells. Diese befindet sich im `location`-Headerwert, den Sie im vorherigen Schritt erhalten haben.  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="headers"></a>Header

Verwenden Sie den folgenden Header, um Ihre Anforderung zu authentifizieren. 

|Schlüssel|Wert|
|--|--|
|`Ocp-Apim-Subscription-Key`| Der Schlüssel für Ihre Ressource. Wird für die Authentifizierung Ihrer API-Anforderungen verwendet.|


### <a name="response-body"></a>Antworttext

Nachdem Sie die Anforderung gesendet haben, erhalten Sie die folgende Antwort. Setzen Sie den Abruf dieses Endpunkts fort, bis der Parameter **status** zu „succeeded“ (erfolgreich) wechselt. 

```json
{
    "result":
    {
          "trainedModelLabel":"MyModel",
          "trainStatus":{"percentComplete":0,"elapsedTime":null},
          "evaluationStatus":{"percentComplete":0,"elapsedTime":null}
     },
    "jobId":"{JOB-ID}",
    "createdDateTime":"{CREATED-TIME}",
    "lastUpdatedDateTime":"{UPDATED-TIME}",
    "expirationDateTime":"{EXPIRATION-TIME}",
    "status":"running"
}
```

## <a name="deploy-your-model"></a>Bereitstellen Ihres Modells

Nach Abschluss des Trainings können Sie Ihr Modell für Vorhersagen bereitstellen. 

Erstellen Sie eine **POST-Anforderung** mithilfe der folgenden URL, der Header und des JSON-Texts, um mit der Bereitstellung eines Conversational Language Understanding-Modells zu beginnen.


### <a name="request-url"></a>Anfrage-URL

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/deployments/production?api-version=2021-11-01-preview
```

|Platzhalter  |Wert  | Beispiel |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Der Endpunkt für die Authentifizierung Ihrer API-Anforderung.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


### <a name="headers"></a>Header

Verwenden Sie den folgenden Header, um Ihre Anforderung zu authentifizieren. 

|Schlüssel|Wert|
|--|--|
|`Ocp-Apim-Subscription-Key`| Der Schlüssel für Ihre Ressource. Wird für die Authentifizierung Ihrer API-Anforderungen verwendet.|


### <a name="request-body"></a>Anforderungstext

```json
{
  "trainedModelLabel":"MyModel",
  "deploymentName":"production"
}
```

Nachdem Sie Ihre API-Anforderung gesendet haben, erhalten Sie eine `202`-Antwort, die angibt, dass sie erfolgreich war. Extrahieren Sie in den Antwortheadern den `location`-Wert. Er weist das folgende Format auf: 

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/deployments/production/jobs/{JOB-ID}?api-version=2021-11-01-preview
``` 

`JOB-ID` wird verwendet, um Ihre Anforderung zu identifizieren, da es sich um einen asynchronen Vorgang handelt. Sie verwenden diese URL im nächsten Schritt, um den Trainingsstatus abzurufen.

## <a name="get-deployment-status"></a>Abrufen des Bereitstellungsstatus

Verwenden Sie die folgende **GET**-Anforderung, um den Status des Bereitstellungsprozesses Ihres Modells abzufragen. Sie können die URL verwenden, die Sie im vorherigen Schritt erhalten haben, oder die Platzhalterwerte unten durch Ihre eigenen Werte ersetzen. 

### <a name="request-url"></a>Anfrage-URL

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/deployments/production/jobs/{JOB-ID}?api-version=2021-11-01-preview
```

|Platzhalter  |Wert  | Beispiel |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Der Endpunkt für die Authentifizierung Ihrer API-Anforderung.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{JOB-ID}`     | Die ID zum Ermitteln des Trainingsstatus Ihres Modells. Diese befindet sich im `location`-Headerwert, den Sie im vorherigen Schritt erhalten haben.  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="headers"></a>Header

Verwenden Sie den folgenden Header, um Ihre Anforderung zu authentifizieren. 

|Schlüssel|Wert|
|--|--|
|`Ocp-Apim-Subscription-Key`| Der Schlüssel für Ihre Ressource. Wird für die Authentifizierung Ihrer API-Anforderungen verwendet.|


### <a name="response-body"></a>Antworttext

Nachdem Sie die Anforderung gesendet haben, erhalten Sie die folgende Antwort. Setzen Sie den Abruf dieses Endpunkts fort, bis der Parameter **status** zu „succeeded“ (erfolgreich) wechselt. 

```json
{
    "jobId":"{JOB-ID}",
    "createdDateTime":"{CREATED-TIME}",
    "lastUpdatedDateTime":"{UPDATED-TIME}",
    "expirationDateTime":"{EXPIRATION-TIME}",
    "status":"running"
}
```

## <a name="query-model"></a>Abfragemodell

Sobald die Bereitstellung erfolgreich war, können Sie Ihr Projekt nach Vorhersagen abfragen. 

Erstellen Sie eine **POST-Anforderung** mithilfe der folgenden URL, der Header und des JSON-Texts, um mit der Bereitstellung eines Conversational Language Understanding-Modells zu beginnen.

### <a name="request-url"></a>Anfrage-URL

```rest
{YOUR-ENDPOINT}/language/:analyze-conversations?projectName=EmailProject&deploymentName=production&api-version=2021-11-01-preview
```

|Platzhalter  |Wert  | Beispiel |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Der Endpunkt für die Authentifizierung Ihrer API-Anforderung.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="headers"></a>Header

Verwenden Sie den folgenden Header, um Ihre Anforderung zu authentifizieren. 

|Schlüssel|Wert|
|--|--|
|`Ocp-Apim-Subscription-Key`| Der Schlüssel für Ihre Ressource. Wird für die Authentifizierung Ihrer API-Anforderungen verwendet.|

### <a name="request-body"></a>Anforderungstext

```json
{
  "query":"attach a docx file"
}
```

### <a name="response-body"></a>Antworttext

Nachdem Sie die Anforderung gesendet haben, erhalten Sie die folgende Antwort für die Vorhersage!

```json
{
    "query":"attach a docx file",
    "prediction": {
        "topIntent":"Attach",
        "projectKind":"conversation",
        "intents":[{"category":"Attach","confidenceScore":0.9998592},{"category":"Read","confidenceScore":0.00010551753},{"category":"Delete","confidenceScore":3.5209276E-05}],
        "entities":[{"category":"FileType","text":"docx","offset":9,"length":4,"confidenceScore":1}]
     }
}
```
