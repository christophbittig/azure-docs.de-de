---
title: Azure Machine-Learning-Studio-(klassisch)-Modelle aktualisieren
description: Beschreibt die Erstellung von Vorhersage-Pipelines durch das Machine Learning Studio (klassisch) mit der Azure Data Factory oder Synapse Analytics
titleSuffix: Azure Data Factory & Azure Synapse
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: tutorials
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 0df985143dead0c86031e85f0a468d98e5ac8aba
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077779"
---
# <a name="update--machine-learning-studio-classic-models-by-using-update-resource-activity"></a>Aktualisieren von klassischen Azure Machine-Learning-Studio-Modellen mithilfe der Ressourcenaktualisierungsaktivität

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Abschnitt ergänzt den Hauptabschnitt zur Integration von Azure Machine Learning Studio (klassisch): [Erstellen von Vorhersage-Pipelines mit Azure Machine Learning Studio (klassich)](transform-data-using-machine-learning.md). Wenn Sie dies noch nicht getan haben, lesen Sie zunächst den Hauptartikel, bevor Sie diesen Artikel lesen.

## <a name="overview"></a>Übersicht
Im Rahmen des Operationalisierungsprozesses von klassischen Machine-Learning-Studio-Modellen wird Ihr Modell trainiert und gespeichert. Anschließend kann es dann zum Erstellen eines Vorhersagewebdiensts verwendet werden. Dieser Webdienst kann von Websites, Dashboards und mobilen Apps genutzt werden.

Mithilfe des klassischen Machine-Learning-Studios (Classic) erstellte Modelle sind in der Regel nicht statisch. Wenn neue Daten verfügbar sind oder der Endkunde der API über eigene Daten verfügt, muss das Modell erneut trainiert werden. 

Das erneute Training kann häufig durchgeführt werden. Mit der Aktivität „Batch-Ausführung“ und der Aktivität „Ressource aktualisieren“ können Sie das klassiche Azure Machine-Learning-Studio-Modell neu trainieren und den Vorhersage-Webdienst aktualisieren.

Die folgende Abbildung zeigt die Beziehung zwischen Trainings- und Vorhersagewebdiensten.

:::image type="content" source="./media/update-machine-learning-models/web-services.png" alt-text="WEB SERVICES":::

## <a name="machine-learning-studio-classic-update-resource-activity"></a>Update Resource-Aktivität für Machine Learning Studio (klassisch)

Der folgende JSON-Codeausschnitt definiert eine Batchausführungsaktivität des klassischen Machine-Learning-Studios.

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```

| Eigenschaft                      | BESCHREIBUNG                              | Erforderlich |
| :---------------------------- | :--------------------------------------- | :------- |
| name                          | Name der Aktivität in der Pipeline     | Ja      |
| description                   | Ein Text, der beschreibt, was mit der Aktivität ausgeführt wird.  | Nein       |
| type                          | Der Aktivitätstyp der Ressourcenaktualisierungsaktivität für Azure Machine Learning Studio (klassisch) lautet **AzureMLUpdateResource**. | Ja      |
| linkedServiceName             | Ein mit dem klassischen Machine Learning Studio verknüpfter Dienst, der die Eigenschaft „updateResourceEndpoint“ enthält. | Ja      |
| trainedModelName              | Name des „Trained Model“-Moduls im Webdienstexperiment an, das aktualisiert werden soll | Ja      |
| trainedModelLinkedServiceName | Name des mit Azure Storage verknüpften Diensts, der die „ilearner“-Datei enthält, die vom Aktualisierungsvorgang hochgeladen wird | Ja      |
| trainedModelFilePath          | Der relative Dateipfad in „trainedModelLinkedService“, der die „ilearner“-Datei darstellt, die beim Aktualisierungsvorgang hochgeladen wird | Ja      |

## <a name="end-to-end-workflow"></a>Kompletter Workflow

Der gesamte Prozess der Operationalisierung des erneuten Trainierens eines Modells und Aktualisieren der Vorhersagewebdienste umfasst die folgenden Schritte:

- Rufen Sie mithilfe der **Batchausführungsaktivität** den **Trainingswebdienst** auf. Der Aufruf eines Trainings-Webdienstes entspricht dem Aufruf eines Vorhersage-Webdienstes, der in [Erstellen von Vorhersage-Pipelines mit Azure Machine Learning Studio (klassisch) und der Aktivität Batch-Ausführung](transform-data-using-machine-learning.md) beschrieben wird. Die Ausgabe des Trainingswebdiensts ist eine „iLearner“-Datei, die Sie zum Aktualisieren des Vorhersagewebdiensts verwenden können.
- Rufen Sie mithilfe der **Ressourcenaktualisierungsaktivität** den **Ressourcenaktualisierungsendpunkt** des **Vorhersagewebdiensts** auf, um den Webdienst mit dem neu trainierten Modell zu aktualisieren.

## <a name="machine-learning-studio-classic-linked-service"></a>Verknüpfter Machine-Learning-Studio-Dienst (klassisch)

Damit der zuvor erwähnte End-to-End-Workflow funktioniert, müssen Sie zwei mit dem klassischen Machine Learning Studio verknüpfte Dienste erstellen:

1. Ein mit dem klassischen Machine Learning Studio verknüpfter Dienst, der mit dem Trainings-Webdienst verbunden ist. Dieser verknüpfte Dienst wird von der Aktivität „Batch-Ausführung“ auf die gleiche Weise verwendet, wie unter [Erstellen von Vorhersage-Pipelines mit dem klassischen Machine Learning Studio und der Aktivität „Batch-Ausführung“](transform-data-using-machine-learning.md) beschrieben. Der Unterschied bei der Ausgabe des Trainingswebdiensts ist eine iLearner-Datei, die dann von der Ressourcenaktualisierungsaktivität zum Aktualisieren des Vorhersagewebdiensts verwendet wird.
2. Ein mit dem klassischen Machine Learning Studio verknüpfter Dienst für den Ressourcenaktualisierungsendpunkt des Vorhersagewebdiensts. Dieser verknüpfte Dienst wird von der Ressourcenaktualisierungsaktivität zum Aktualisieren des Vorhersagewebdiensts mithilfe der im vorherigen Schritt zurückgegebenen „iLearner“-Datei verwendet.

Für den zweiten mit dem klassischen Machine Learning Studio verknüpften Dienst unterscheidet sich die Konfiguration, wenn Ihr klassisches-Machine-Learning-Studio-Webdienst ein klassischer oder neuer Webdienst ist. Die Unterschiede werden in den folgenden Abschnitten getrennt erläutert.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Webdienst ist ein Azure Resource Manager-Webdienst

Wenn der Webdienst der neue Webdiensttyp ist, der einen Azure Resource Manager-Endpunkt verfügbar macht, müssen Sie den zweiten **nicht standardmäßigen** Endpunkt nicht hinzufügen. Das **updateResourceEndpoint**-Element im verknüpften Dienst hat das Format:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Beim Abfragen des Webdiensts im [Portal des Machine-Learning-Studio-(klassisch)-Webdienstes](https://services.azureml.net/) können Sie Werte für Platzhalter in der URL abrufen.

Der neue Typ des Ressourcenaktualisierungsendpunkts erfordert die Dienstprinzipalauthentifizierung. Um die Dienstprinzipalauthentifizierung zu verwenden, registrieren Sie eine Anwendungsentität in Azure Active Directory (Azure AD), und teilen Sie ihr die Rolle **Mitwirkender** oder **Besitzer** des Abonnements oder der Ressourcengruppe zu, zu der der Webdienst gehört. Informationen dazu finden Sie im Artikel zum [Erstellen eines Dienstprinzipals und Zuweisen von Berechtigungen zum Verwalten einer Azure-Ressource](../active-directory/develop/howto-create-service-principal-portal.md). Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts verwenden:

- Anwendungs-ID
- Anwendungsschlüssel
- Mandanten-ID

Hier sehen Sie ein Beispiel der Definition eines verknüpften Diensts:

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
                "type": "SecureString",
                "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

Das folgende Szenario enthält weitere Details hierzu. Es enthält ein Beispiel für das Neutrainieren und Aktualisieren von klassischen Machine-Learning-Studio-Modellen aus einer Pipeline.


## <a name="sample-retraining-and-updating-an-machine-learning-studio-classic-model"></a>Beispiel: Erneutes Trainieren und Aktualisieren eines klassischen Machine-Learning-Studio-Modells

Dieser Abschnitt enthält eine Beispielpipeline, bei der die **Batchausführungsaktivität von Azure Machine Learning Studio (Classic)** zum erneuten Trainieren des Modells verwendet wird. Für die Pipeline wird außerdem die **Aktivität zur Ressourcenaktualisierung von Azure Machine Learning Studio (Classic)** verwendet, um das Modell im Bewertungswebdienst zu aktualisieren. Darüber hinaus enthält der Abschnitt JSON-Codeausschnitte für alle verknüpften Dienste, Datasets und Pipelines im Beispiel.

### <a name="azure-blob-storage-linked-service"></a>Mit Azure Blob Storage verknüpfter Dienst:
Der Azure-Speicher enthält die folgenden Daten:

* Trainingsdaten: Die Eingabedaten für den Machine-Learning-Studio-(klassisch)-Trainingswebdienst.
* iLearner-Datei: Die Eingabedaten für den Machine-Learning-Studio-(klassisch)-Trainingswebdienst. Die Datei dient darüber hinaus als Eingabe für die Aktivität zur Ressourcenaktualisierung.

Dies ist die JSON-Beispieldefinition des verknüpften-Diensts:

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="linked-service-for-machine-learning-studio-classic-training-endpoint"></a>Verknüpfter Dienst für den Machine-Learning-Studio-(Classic)-Trainingsendpunkt
Der folgende JSON-Codeausschnitt definiert einen mit einem klassischen Machine Learning Studio verknüpften Dienst, der auf den Standardendpunkt des Trainingswebdiensts verweist.

```JSON
{
    "name": "trainingEndpoint",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
            "apiKey": "myKey"
        }
    }
}
```

Führen Sie in **Azure Machine Learning Studio (klassisch)** folgende Schritte aus, um Werte für **mlEndpoint** und **apiKey** abzurufen:

1. Klicken Sie im linken Menü auf **WEB SERVICES** .
2. Klicken Sie in der Liste der Webdienste auf den **Trainingswebdienst** .
3. Klicken Sie neben dem Textfeld **API-Schlüssel** auf „Kopieren“. Fügen Sie den Schlüssel aus der Zwischenablage in den Data Factory-JSON-Editor ein.
4. Klicken Sie in **Azure Machine Learning Studio (klassisch)** auf den Link **BATCHAUSFÜHRUNG**.
5. Kopieren Sie die **Anforderungs-URI** aus dem Abschnitt **„Anforderungen“** und fügen Sie sie in den JSON-Editor ein.

### <a name="linked-service-for-azure-machine-learning-studio-classic-updatable-scoring-endpoint"></a>Verknüpfter Dienst für den aktualisierbaren Azure Machine Learning Studio (Classic)-Bewertungsendpunkt:
Der folgende JSON-Codeausschnitt definiert einen mit Azure Machine Learning Studio (Classic) verknüpften Dienst, der auf den aktualisierbaren Endpunkt des Bewertungswebdiensts verweist.

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
Die Pipeline enthält zwei Aktivitäten: **AzureMLBatchExecution** und **AzureMLUpdateResource**. Die Batchausführungsaktivität verwendet die Trainingsdaten als Eingabe und erzeugt eine „iLearner“-Datei als Ausgabe. Die Ressourcenaktualisierungsaktivität verwendet anschließend diese „iLearner“-Datei zum Aktualisieren des Vorhersagewebdiensts.

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        },
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        }
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/output"
                        }
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                        "type": "LinkedServiceReference",
                        "referenceName": "StorageLinkedService"
                    },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [
                    {
                        "activity": "amlbeGetilearner",
                        "dependencyConditions": [ "Succeeded" ]
                    }
                ]
            }
        ]
    }
}
```
## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln erfahren Sie, wie Daten auf andere Weisen transformiert werden:

* [U-SQL-Aktivität](transform-data-using-data-lake-analytics.md)
* [Hive-Aktivität](transform-data-using-hadoop-hive.md)
* [Pig-Aktivität](transform-data-using-hadoop-pig.md)
* [MapReduce-Aktivität](transform-data-using-hadoop-map-reduce.md)
* [Hadoop-Streamingaktivität](transform-data-using-hadoop-streaming.md)
* [Spark-Aktivität](transform-data-using-spark.md)
* [Benutzerdefinierte .NET-Aktivität](transform-data-using-dotnet-custom-activity.md)
* [Aktivität „Gespeicherte Prozedur“](transform-data-using-stored-procedure.md)
