---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 1ec5ed80111d7d0665d42b20dd725769b13de4b9
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520666"
---
## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)

## <a name="create-a-new-azure-resource-and-azure-blob-storage-account"></a>Erstellen einer neuen Azure-Ressource und eines Azure Blob Storage-Kontos

Bevor Sie die benutzerdefinierte Textklassifizierung verwenden können, müssen Sie eine Sprachressource erstellen, durch die Sie in den Besitz des Abonnements und der Anmeldeinformationen gelangen, die Sie zum Erstellen eines Projekts und zum Trainieren eines Modells benötigen. Außerdem benötigen Sie ein Azure Blob Storage-Konto. Dabei handelt es sich um den erforderlichen Onlinedatenspeicher zum Speichern von Text für die Analyse. 

> [!IMPORTANT]
> Für einen schnellen Einstieg empfiehlt es sich, mithilfe der unten angegebenen Schritte eine neue Azure-Sprachressource zu erstellen, mit der Sie die Ressource erstellen und gleichzeitig ein Speicherkonto konfigurieren können. Dies ist in einem Durchgang einfacher zu erledigen als später. 
>
> Wenn Sie eine bereits vorhandene Ressource verwenden möchten, müssen Sie sie und ein Speicherkonto separat konfigurieren. Weitere Informationen finden Sie in den [**Projektanforderungen**](../../how-to/create-project.md#using-a-pre-existing-azure-resource).

1. Wechseln Sie zum [Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics), um eine neue Azure-Sprachressource zu erstellen. Wenn Sie aufgefordert werden, zusätzliche Features auszuwählen, wählen Sie **Diesen Schritt überspringen** aus. Stellen Sie beim Erstellen Ihrer Ressource sicher, dass sie die folgenden Parameter aufweist.  

    |Azure-Ressourcenanforderung  |Erforderlicher Wert  |
    |---------|---------|
    |Ort | „USA, Westen 2“ oder „Europa, Westen“         |
    |Tarif     | Tarif „Standard“ (**S**)        |

2. Wählen Sie im Abschnitt **Benutzerdefinierte Erkennung benannter Entitäten (NER) & Benutzerdefinierte Klassifizierung (Vorschau)** **Neues Speicherkonto erstellen** aus. Diese Werte gelten im Rahmen dieser Schnellstartanleitung, stellen aber nicht unbedingt [Speicherkontowerte](/azure/storage/common/storage-account-overview) dar, die in Produktionsumgebungen sinnvoll sind. 

    |Speicherkontowert  |Empfohlener Wert  |
    |---------|---------|
    | Name | Beliebiger Name |
    | Leistung | Standard | 
    | Kontoart| Storage (Universell V1) |
    | Replikation | Lokal redundanter Speicher (LRS)
    |Ort | Ein beliebiger Standort, der möglichst nahe zu Ihnen liegt, um die beste Latenz zu erhalten.        |


## <a name="upload-sample-data-to-blob-container"></a>Hochladen von Beispieldaten in den Blobcontainer

Nachdem Sie ein Azure-Speicherkonto erstellt und es mit Ihrer Sprachressource verknüpft haben, müssen Sie die Beispieldateien für diese Schnellstartanleitung hochladen. Diese Dateien werden später zum Trainieren Ihres Modells verwendet.

1. [Laden Sie die Beispieldaten](https://go.microsoft.com/fwlink/?linkid=2175083) für diesen Schnellstart von GitHub herunter.

2. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com) zu Ihrem Azure-Speicherkonto. Navigieren Sie zu Ihrem Konto, und laden Sie die Beispieldaten in das Konto hoch.

Das bereitgestellte Beispieldataset enthält etwa 200 Filmzusammenfassungen, die zu einer oder mehreren der folgenden Klassen gehören: „Mystery“, „Drama“, „Thriller“, „Comedy“, „Action“.

### <a name="get-your-resource-keys-and-endpoint"></a>Abrufen Ihrer Ressourcenschlüssel und Endpunkte

* Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/#home) zur Übersichtsseite Ihrer Ressource.

* Wählen Sie im Menü auf der linken Seite **Schlüssel und Endpunkt** aus. Sie verwenden den Endpunkt und Schlüssel für die API-Anforderungen. 

:::image type="content" source="../../media/get-endpoint-azure.png" alt-text="Screenshot der Schlüssel- und Endpunktseite im Azure-Portal" lightbox="../../media/get-endpoint-azure.png":::

## <a name="create-project"></a>Projekt erstellen

Um mit der Erstellung eines benutzerdefinierten Klassifizierungsmodells zu beginnen, müssen Sie ein Projekt erstellen. Durch das Erstellen eines Projekts können Sie Daten kennzeichnen sowie Ihre Modelle trainieren, auswerten, verbessern und bereitstellen. 

> [!NOTE]
> Beim Projektnamen wird bei allen Vorgängen die Groß-/Kleinschreibung beachtet.

Erstellen Sie eine **POST**-Anforderung mithilfe der folgenden URL, der Header und des JSON-Texts, um Ihr Projekt zu erstellen und die Tagdatei zu importieren.

### <a name="request-url"></a>Anfrage-URL

Verwenden Sie die folgende URL, um ein Projekt zu erstellen und Ihre Tagdatei zu importieren. Ersetzen Sie die folgenden Platzhalter durch Ihre eigenen Werte. 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{projectName}/:import. 
```

|Platzhalter  |Wert  | Beispiel |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Der Endpunkt für die Authentifizierung Ihrer API-Anforderung.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="parameters"></a>Parameter

Übergeben Sie in Ihrer Anforderung den folgenden Parameter. 

|Key|Erklärung|Wert|
|--|--|--|
|`api-version`| Die verwendete API-Version.| `2021-11-01-preview` |

Fügen Sie am Ende Ihrer Anforderungs-URL `?api-version=2021-11-01-preview` hinzu, um den Parameter zu übergeben.

### <a name="headers"></a>Header

Verwenden Sie den folgenden Header, um Ihre Anforderung zu authentifizieren. 

|Schlüssel|Wert|
|--|--|
|`Ocp-Apim-Subscription-Key`| Der Schlüssel für Ihre Ressource. Wird für die Authentifizierung Ihrer API-Anforderungen verwendet.|

### <a name="body"></a>Body

Verwenden Sie den folgenden JSON-Code in Ihrer Anforderung. Ersetzen Sie die folgenden Platzhalter durch Ihre eigenen Werte. Verwenden der Tagdatei, die auf der Registerkarte [Beispieldaten](https://github.com/Azure-Samples/cognitive-services-sample-data-files) verfügbar ist 

```json
{
    "api-version": "2021-11-01-preview",
    "metadata": {
        "name": "MyProject",
        "multiLingual": true,
        "description": "Tryong out custom text classification",
        "modelType": "multiClassification",
        "language": "string",
        "storageInputContainerName": "YOUR-CONTAINER-NAME",
        "settings": {}
    },
    "assets": {
        "classifiers": [
            {
                "name": "Class1"
            }
        ],
        "documents": [
            {
                "location": "doc1.txt",
                "language": "en-us",
                "classifiers": [
                    {
                        "classifierName": "Class1"
                    }
                ]
            }
        ]
    }
}
```
Für den Metadatenschlüssel: 

|Schlüssel  |Wert  | Beispiel |
|---------|---------|---------|
| `modelType  `    | Der Typ Ihres Modells, verwenden Sie für eine einzelne Klassifizierung `singleClassification`.   | multiClassification |
|`storageInputContainerName`   | Der Name Ihres Azure Blob Storage-Containers.   | `myContainer` |

Diese Anforderung gibt in diesem Fall einen Fehler zurück:

* Die ausgewählte Ressource verfügt nicht über die richtige Berechtigung für das Speicherkonto. 

## <a name="start-training-your-model"></a>Beginnen mit dem Trainieren Ihres Modells

Nachdem das Projekt erstellt wurde, können Sie mit dem Trainieren eines Textklassifizierungsmodells beginnen. Erstellen Sie eine **POST**-Anforderung mithilfe der folgenden URL, der Header und des JSON-Texts, um mit dem Training eines Textklassifizierungsmodells zu beginnen.

### <a name="request-url"></a>Anfrage-URL

Verwenden Sie zum Erstellen Ihrer API-Anforderung die folgende URL. Ersetzen Sie die folgenden Platzhalter durch Ihre eigenen Werte. 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{PROJECT-NAME}/:train
```

|Platzhalter  |Wert  | Beispiel |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Der Endpunkt für die Authentifizierung Ihrer API-Anforderung.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | Der Name für Ihr Projekt. Bei diesem Wert wird die Groß-/Kleinschreibung beachtet.  | `myProject` |

### <a name="parameters"></a>Parameter

Übergeben Sie in Ihrer Anforderung den folgenden Parameter. 

|Schlüssel|Erklärung|Wert|
|--|--|--|
|`api-version`| Die verwendete API-Version.| `2021-11-01-preview` |

Fügen Sie am Ende Ihrer Anforderungs-URL `?api-version=2021-11-01-preview` hinzu, um den Parameter zu übergeben.

### <a name="headers"></a>Header

Verwenden Sie den folgenden Header, um Ihre Anforderung zu authentifizieren. 

|Schlüssel|Wert|
|--|--|
|`Ocp-Apim-Subscription-Key`| Der Schlüssel für Ihre Ressource. Wird für die Authentifizierung Ihrer API-Anforderungen verwendet.|

### <a name="request-body"></a>Anforderungstext

Verwenden Sie den folgenden JSON-Code in Ihrer Anforderung. Das Modell wird `MyModel` benannt, nachdem das Training abgeschlossen ist.  

```json
{
  "modelLabel": "MyModel",
  "runValidation": true
}
```

|Schlüssel  |Wert  | Beispiel |
|---------|---------|---------|
|`modelLabel  `    | Ihr Modellname.   | MyModel |
|`runValidation`     | Boolescher Wert zum Ausführen der Validierung für den Testsatz.   | Richtig |

Nachdem Sie Ihre API-Anforderung gesendet haben, erhalten Sie eine `202`-Antwort, die angibt, dass sie erfolgreich war. Extrahieren Sie in den Antwortheadern den `location`-Wert. Er weist das folgende Format auf: 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/train/jobs/{JOB-ID}?api-version=xxxx-xx-xx-xxxxxxx
``` 

`JOB-ID` wird verwendet, um Ihre Anforderung zu identifizieren, da es sich um einen asynchronen Vorgang handelt. Sie verwenden diese URL im nächsten Schritt, um den Trainingsstatus abzurufen. 

## <a name="get-training-status"></a>Trainingsstatus abrufen

Verwenden Sie die folgende **GET**-Anforderung, um den Status des Trainingsprozesses Ihres Modells abzufragen. Sie können die URL verwenden, die Sie im vorherigen Schritt erhalten haben, oder die Platzhalterwerte unten durch Ihre eigenen Werte ersetzen. 


```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/train/jobs/{JOB-ID}
```

|Platzhalter  |Wert  | Beispiel |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Der Endpunkt für die Authentifizierung Ihrer API-Anforderung.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | Der Name für Ihr Projekt. Bei diesem Wert wird die Groß-/Kleinschreibung beachtet.  | `myProject` |
|`{JOB-ID}`     | Die ID zum Ermitteln des Trainingsstatus Ihres Modells. Diese befindet sich im `location`-Headerwert, den Sie im vorherigen Schritt erhalten haben.  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="parameters"></a>Parameter

Übergeben Sie in Ihrer Anforderung den folgenden Parameter. 

|Key|Erklärung|Wert|
|--|--|--|
|`api-version`| Die verwendete API-Version.| `2021-11-01-preview` |

Fügen Sie am Ende Ihrer Anforderungs-URL `?api-version=2021-11-01-preview` hinzu, um den Parameter zu übergeben.

### <a name="headers"></a>Header

Verwenden Sie den folgenden Header, um Ihre Anforderung zu authentifizieren. 

|Schlüssel|Wert|
|--|--|
|`Ocp-Apim-Subscription-Key`| Der Schlüssel für Ihre Ressource. Wird für die Authentifizierung Ihrer API-Anforderungen verwendet.|

### <a name="response-body"></a>Antworttext

Nachdem Sie die Anforderung gesendet haben, erhalten Sie die folgende Antwort. 

```json
{
  "jobs": [
    {
      "result": {
        "trainedModelLabel": "MyModel",
        "trainStatus": {
          "percentComplete": 0,
          "elapsedTime": "string"
        },
        "evaluationStatus": {
          "percentComplete": 0,
          "elapsedTime": "string"
        }
      },
      "jobId": "string",
      "createdDateTime": "2021-10-19T23:24:41.572Z",
      "lastUpdatedDateTime": "2021-10-19T23:24:41.572Z",
      "expirationDateTime": "2021-10-19T23:24:41.572Z",
      "status": "unknown",
      "errors": [
        {
          "code": "unknown",
          "message": "string"
        }
      ]
    }
  ],
  "nextLink": "string"
}
```

## <a name="deploy-your-model"></a>Bereitstellen Ihres Modells

Erstellen Sie eine **PUT**-Anforderung mithilfe der folgenden URL, der Header und des JSON-Texts, um mit dem Bereitstellen eines Textklassifizierungsmodells zu beginnen.

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}
```

|Platzhalter  |Wert  | Beispiel |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Der Endpunkt für die Authentifizierung Ihrer API-Anforderung.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | Der Name für Ihr Projekt. Bei diesem Wert wird die Groß-/Kleinschreibung beachtet.  | `myProject` |
|`{DEPLOYMENT-NAME}`     | Der Name Ihrer Bereitstellung. Bei diesem Wert wird die Groß-/Kleinschreibung beachtet.  | `prod` |

### <a name="parameters"></a>Parameter

Übergeben Sie in Ihrer Anforderung den folgenden Parameter. 

|Key|Erklärung|Wert|
|--|--|--|
|`api-version`| Die verwendete API-Version.| `2021-11-01-preview` |

Fügen Sie am Ende Ihrer Anforderungs-URL `?api-version=2021-11-01-preview` hinzu, um den Parameter zu übergeben.

### <a name="headers"></a>Header

Verwenden Sie den folgenden Header, um Ihre Anforderung zu authentifizieren. 

|Schlüssel|Wert|
|--|--|
|`Ocp-Apim-Subscription-Key`| Der Schlüssel für Ihre Ressource. Wird für die Authentifizierung Ihrer API-Anforderungen verwendet.|

### <a name="request-body"></a>Anforderungstext

Verwenden Sie den folgenden JSON-Code in Ihrer Anforderung. Das Modell wird `MyModel` benannt, nachdem das Training abgeschlossen ist.  

```json
{
{
  "trainedModelLabel": "MyModel"
}
```

Nachdem Sie Ihre API-Anforderung gesendet haben, erhalten Sie eine `202`-Antwort, die angibt, dass sie erfolgreich war. Extrahieren Sie in den Antwortheadern den `location`-Wert. Er weist das folgende Format auf: 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}/jobs/{JOB-ID}?api-version=xxxx-xx-xx-xxxxxxx
``` 

`JOB-ID` wird verwendet, um Ihre Anforderung zu identifizieren, da es sich um einen asynchronen Vorgang handelt. Sie verwenden diese URL im nächsten Schritt, um den Veröffentlichungsstatus abzurufen.

## <a name="get-the-deployment-status"></a>Abrufen des Bereitstellungsstatus

Verwenden Sie die folgende **GET**-Anforderung, um den Status des Veröffentlichungsprozesses Ihres Modells abzufragen. Sie können die URL verwenden, die Sie im vorherigen Schritt erhalten haben, oder die Platzhalterwerte unten durch Ihre eigenen Werte ersetzen. 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}/jobs/{JOB-ID}
```

|Platzhalter  |Wert  | Beispiel |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Der Endpunkt für die Authentifizierung Ihrer API-Anforderung.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | Der Name für Ihr Projekt. Bei diesem Wert wird die Groß-/Kleinschreibung beachtet.  | `myProject` |
|`{DEPLOYMENT-NAME}`     | Der Name Ihrer Bereitstellung. Bei diesem Wert wird die Groß-/Kleinschreibung beachtet.  | `prod` |
|`{JOB-ID}`     | Die ID zum Ermitteln des Trainingsstatus Ihres Modells. Diese befindet sich im `location`-Headerwert, den Sie im vorherigen Schritt erhalten haben.  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="parameters"></a>Parameter

Übergeben Sie in Ihrer Anforderung den folgenden Parameter. 

|Schlüssel|Erklärung|Wert|
|--|--|--|
|`api-version`| Die verwendete API-Version.| `2021-11-01-preview` |

Fügen Sie am Ende Ihrer Anforderungs-URL `?api-version=2021-11-01-preview` hinzu, um den Parameter zu übergeben.

### <a name="headers"></a>Header

Verwenden Sie den folgenden Header, um Ihre Anforderung zu authentifizieren. 

|Schlüssel|Wert|
|--|--|
|`Ocp-Apim-Subscription-Key`| Der Schlüssel für Ihre Ressource. Wird für die Authentifizierung Ihrer API-Anforderungen verwendet.|

### <a name="submit-text-classification-task"></a>Übermitteln der Textklassifizierungsaufgabe

> [!NOTE]
> Bei Projektnamen wird die Groß-/Kleinschreibung beachtet.

Verwenden Sie diese **POST**-Anforderung, um eine Entitätsextraktionsaufgabe zu starten. Ersetzen Sie `{projectName}` durch den Projektnamen für das Modell, das Sie verwenden möchten.

`{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze`

#### <a name="headers"></a>Header

|Schlüssel|Wert|
|--|--|
|Ocp-Apim-Subscription-Key| Ihr Abonnementschlüssel, der den Zugriff auf diese API ermöglicht.|

#### <a name="body"></a>Body

```json
{
    "displayName": "MyJobName",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1", 
                "text": "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc tempus, felis sed vehicula lobortis, lectus ligula facilisis quam, quis aliquet lectus diam id erat. Vivamus eu semper tellus. Integer placerat sem vel eros iaculis dictum. Sed vel congue urna."
            },
            {
                "id": "doc2",
                "text": "Mauris dui dui, ultricies vel ligula ultricies, elementum viverra odio. Donec tempor odio nunc, quis fermentum lorem egestas commodo. Class aptent taciti sociosqu ad litora torquent per conubia nostra, per inceptos himenaeos."
            }
        ]
    },
    "tasks": {
        "customMultiClassificationTasks": [      
            {
                "parameters": {
                      "project-name": "MyProject",
                      "deployment-name": "MyDeploymentName"
                      "stringIndexType": "TextElements_v8"
                }
            }
        ]
    }
}
```

|Schlüssel|Beispielwert|BESCHREIBUNG|
|--|--|--|
|displayName|„MyJobName“|Der Name Ihres Auftrags|
|Dokumente|[{},{}]|Liste der Dokumente, für die Aufgaben ausgeführt werden sollen|
|id|„doc1“|Ein Zeichenfolgenbezeichner für Dokumente|
|text|„Lorem ipsum dolor sit amet“| Ihr Dokument im Zeichenfolgenformat|
|„tasks“|[]| Liste der Aufgaben, die ausgeführt werden sollen.|
|--|customMultiClassificationTasks|Aufgaben-ID für die Aufgabe, die wir ausführen möchten. Verwenden Sie `customClassificationTasks` für einzelne Klassifizierungsaufgaben und `customMultiClassificationTasks` für Aufgaben mit mehreren Klassifizierungen. |
|parameters|[]|Liste der an die Aufgabe zu übergebenden Parameter|
|project-name| „MyProject“| Der Name Ihres Projekts. Beim Projektnamen wird die Groß-/Kleinschreibung beachtet.|
|deployment-name| „MyDeploymentName“| Der Name Ihrer Bereitstellung|

Ersetzen Sie den Text des Dokuments durch Filmzusammenfassungen, die klassifiziert werden sollen.

#### <a name="response"></a>Antwort

Sie erhalten eine 202-Antwort, die den Erfolg anzeigt. Extrahieren Sie `operation-location` in den **Antwortheadern**.
`operation-location` weist dieses Format auf:

 `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`

Sie verwenden diesen Endpunkt im nächsten Schritt, um die Ergebnisse der benutzerdefinierten Klassifizierungsaufgabe abzurufen.

### <a name="get-the-classification-task-status-and-results"></a>Abrufen von Status und Ergebnissen der Klassifizierungsaufgabe

Verwenden Sie die folgende **GET**-Anforderung, um den Status/die Ergebnisse der benutzerdefinierten Klassifizierungsaufgabe abzufragen. Sie können den Endpunkt verwenden, den Sie im vorherigen Schritt erhalten haben.

`{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`.

#### <a name="headers"></a>Header

|Schlüssel|Wert|
|--|--|
|Ocp-Apim-Subscription-Key| Ihr Abonnementschlüssel, der den Zugriff auf diese API ermöglicht.|

### <a name="response-body"></a>Antworttext

Die Antwort ist ein JSON-Dokument mit den folgenden Parametern.

```json
{
    "createdDateTime": "2021-05-19T14:32:25.578Z",
    "displayName": "MyJobName",
    "expirationDateTime": "2021-05-19T14:32:25.578Z",
    "jobId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
    "lastUpdateDateTime": "2021-05-19T14:32:25.578Z",
    "status": "completed",
    "errors": [],
    "tasks": {
        "details": {
            "name": "MyJobName",
            "lastUpdateDateTime": "2021-03-29T19:50:23Z",
            "status": "completed"
        },
        "completed": 1,
        "failed": 0,
        "inProgress": 0,
        "total": 1,
        "tasks": {
    "customMultiClassificationTasks": [
        {
            "lastUpdateDateTime": "2021-05-19T14:32:25.579Z",
            "name": "MyJobName",
            "status": "completed",
            "results": {
                "documents": [
                    {
                        "id": "doc1",
                        "classes": [
                            {
                                "category": "Class_1",
                                "confidenceScore": 0.0551877357
                            }
                        ],
                        "warnings": []
                    },
                    {
                        "id": "doc2",
                        "classes": [
                            {
                                "category": "Class_1",
                                "confidenceScore": 0.0551877357
                            },
                                                        {
                                "category": "Class_2",
                                "confidenceScore": 0.0551877357
                            }
                        ],
                        "warnings": []
                    }
                ],
                "errors": [],
                "statistics": {
                    "documentsCount":0,
                    "erroneousDocumentsCount":0,
                    "transactionsCount":0
                }
                    }
                }
            ]
        }
    }
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie Ihr Projekt nicht mehr benötigen, können Sie es mit der folgenden **DELETE**-Anforderung löschen. Ersetzen Sie die Platzhalterwerte durch eigene Werte.   

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{PROJECT-NAME}
```

|Platzhalter  |Wert  | Beispiel |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Der Endpunkt für die Authentifizierung Ihrer API-Anforderung.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | Der Name für Ihr Projekt. Bei diesem Wert wird die Groß-/Kleinschreibung beachtet.  | `myProject` |

### <a name="headers"></a>Header

Verwenden Sie den folgenden Header, um Ihre Anforderung zu authentifizieren. 

|Schlüssel|Wert|
|--|--|
|Ocp-Apim-Subscription-Key| Der Schlüssel für Ihre Ressource. Wird für die Authentifizierung Ihrer API-Anforderungen verwendet.|
