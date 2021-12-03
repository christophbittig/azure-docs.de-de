---
title: Übermitteln von Aufgaben für die benutzerdefinierte Klassifizierung
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie mehr über das Senden einer Anforderung für die benutzerdefinierte Textklassifizierung.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 9ba640d5d0177c812ad25a76353bd89f2e696d81
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054712"
---
# <a name="deploy-a-model-and-classify-text-using-the-runtime-api"></a>Bereitstellen eines Modells und Klassifizieren von Text mithilfe der Runtime-API

Wenn Sie mit Ihrem Modell zufrieden sind und alle erforderlichen Verbesserungen vorgenommen haben, können Sie es bereitstellen und mit der Klassifizierung von Text beginnen. Durch die Bereitstellung eines Modells wird es für die Verwendung über die Runtime-API verfügbar gemacht.

## <a name="prerequisites"></a>Voraussetzungen

* [Ein Projekt für die benutzerdefinierte Klassifizierung](create-project.md) mit einem konfigurierten Azure Blob Storage-Konto 
* Textdaten, die in Ihr Speicherkonto [hochgeladen wurden](create-project.md#prepare-training-data).
* [Markierte Daten](tag-data.md) und ein erfolgreich [trainiertes Modell](train-model.md)
* Überprüfen Sie die [Modellauswertungsdetails](view-model-evaluation.md), um die Leistung Ihres Modells zu bestimmen.
* (Optional) [Es wurden Verbesserungen am Modell vorgenommen](improve-model.md), wenn die Leistung nicht zufriedenstellend ist. 

Weitere Informationen finden Sie im [Lebenszyklus der Anwendungsentwicklung](../overview.md#project-development-lifecycle).

## <a name="deploy-your-model"></a>Bereitstellen Ihres Modells

1. Wechseln Sie in [Language Studio](https://aka.ms/custom-classification) zu Ihrem Projekt.

2. Wählen Sie im Menü auf der linken Seite **Modell bereitstellen** aus.

3. Wählen Sie das Modell aus, das Sie bereitstellen möchten, und wählen Sie dann **Modell bereitstellen** aus.

> [!TIP]
> Sie können Ihr Modell in Language Studio testen, indem Sie Textbeispiele für die Klassifizierung senden. 
> 1. Wählen Sie im Menü auf der linken Seite Ihres Projekts in Language Studio die Option **Testmodell** aus.
> 2. Wählen Sie das Modell aus, das Sie testen möchten.
> 3. Fügen Sie ihren Text in das Textfeld ein. Alternativ können Sie eine `.txt`-Datei hochladen. 
> 4. Klicken Sie auf **Test ausführen**.
> 5. Auf der Registerkarte **Ergebnis** werden die vorhergesagten Klassen für Ihren Text angezeigt. Auf der Registerkarte **JSON** können Sie außerdem die JSON-Antwort anzeigen.

## <a name="send-a-text-classification-request-to-your-model"></a>Senden einer Textklassifizierungsanforderung an Ihr Modell

# <a name="using-language-studio"></a>[Verwenden von Language Studio](#tab/language-studio)

### <a name="using-language-studio"></a>Verwenden von Language Studio

1. Wählen Sie nach Abschluss der Bereitstellung das Modell aus, das Sie verwenden möchten, und klicken Sie im oberen Menü auf **Vorhersage-URL abrufen**, und kopieren Sie die URL und den Text.

    :::image type="content" source="../media/get-prediction-url-1.png" alt-text="run-inference" lightbox="../media/get-prediction-url-1.png":::

2. Kopieren Sie im angezeigten Fenster unter dem Pivot **Senden** die Beispielanforderung in die Befehlszeile.

3. Ersetzen Sie `<YOUR_DOCUMENT_HERE>` durch den eigentlichen Text, den Sie klassifizieren möchten.

    :::image type="content" source="../media/get-prediction-url-2.png" alt-text="run-inference-2" lightbox="../media/get-prediction-url-2.png":::

4. Senden Sie die Anforderung.

5. Extrahieren Sie im Antwortheader, den Sie erhalten, `jobId` aus `operation-location`, das im folgenden Format vorliegt: `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId}>`.

6. Kopieren Sie die Abrufanforderung, ersetzen Sie `<OPERATION-ID>` durch die im letzten Schritt abgerufene Auftrags-ID (`jobId`), und übermitteln Sie die Anforderung.

    :::image type="content" source="../media/get-prediction-url-3.png" alt-text="run-inference-3" lightbox="../media/get-prediction-url-3.png":::

 Weitere Informationen zu den Ergebnissen finden Sie im nächsten Abschnitt.

# <a name="using-the-api"></a>[Verwenden der API](#tab/api)

### <a name="using-the-api"></a>Verwenden der API

Zuerst müssen Sie Ihren Ressourcenschlüssel und Endpunkt abrufen.

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/#home) zur Übersichtsseite Ihrer Ressource.

2. Wählen Sie im Menü auf der linken Seite **Schlüssel und Endpunkt** aus. Verwenden Sie den Endpunkt für die API-Anforderungen, außerdem benötigen Sie den Schlüssel für den `Ocp-Apim-Subscription-Key`-Header.

    :::image type="content" source="../media/get-endpoint-azure.png" alt-text="Abrufen des Azure-Endpunkts" lightbox="../media/get-endpoint-azure.png":::

### <a name="submit-text-classification-task"></a>Übermitteln der Textklassifizierungsaufgabe

> [!NOTE]
> Bei Projektnamen wird die Groß-/Kleinschreibung beachtet.

Verwenden Sie diese **POST**-Anforderung, um eine Entitätsextraktionsaufgabe zu starten.

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

Weitere Informationen zu den Ergebnissen finden Sie im nächsten Abschnitt.

---


#### <a name="text-classification-task-results"></a>Ergebnisse der Textklassifizierungsaufgabe

Die Antwort, die vom Aufruf „Get result“ (Ergebnis abrufen) zurückgegeben wird, ist ein JSON-Dokument mit den folgenden Parametern:

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
