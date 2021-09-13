---
title: Erstellen von Vorhersagepipelines für Daten
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie mithilfe der Batch Execution-Aktivität von ML Studio (Classic) eine Vorhersagepipeline in Azure Data Factory erstellen.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: synapse
ms.date: 07/16/2020
ms.openlocfilehash: 340f12462a3d31b0f0da13afd43de2a24608d781
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122356548"
---
# <a name="create-a-predictive-pipeline-using-ml-studio-classic-and-azure-data-factory"></a>Erstellen einer Vorhersagepipeline mithilfe von ML Studio (Classic) und Azure Data Factory

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Aktuelle Version](transform-data-using-machine-learning.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[ML Studio (Classic)](https://azure.microsoft.com/documentation/services/machine-learning/) ermöglicht Ihnen das Erstellen, Testen und Bereitstellen von Predictive Analytics-Lösungen. Allgemein betrachtet, geschieht dies in drei Schritten:

1. **Erstellen eines Trainingsexperiments**. Für diesen Schritt verwenden Sie ML Studio (Classic). ML Studio (Classic) ist eine visuelle Entwicklungsumgebung für die Zusammenarbeit, mit der Sie ein Predictive Analytics-Modell mithilfe von Trainingsdaten trainieren und testen können.
2. **Konvertierten in ein Vorhersageexperiment**. Sobald Ihr Modell mit vorhandenen Daten trainiert wurde, können Sie es verwenden, um neue Daten zu bewerten. Sie bereiten das Experiment auf die Bewertung vor und optimieren es.
3. **Bereitstellen des Experiments als Webdienst**. Sie können das Bewertungsexperiment als Azure-Webdienst veröffentlichen. Sie können Daten über diesen Webdienstendpunkt an Ihr Modell senden und Ergebnisvorhersagen vom Modell empfangen.

### <a name="data-factory-and-ml-studio-classic-together"></a>Data Factory und ML Studio (Classic) im gemeinsamen Einsatz
Azure Data Factory ermöglicht Ihnen die einfache Erstellung von Pipelines, die einen veröffentlichten Webdienst von [ML Studio (Classic)](https://azure.microsoft.com/documentation/services/machine-learning) für Predictive Analytics nutzen. Mithilfe der **Batch Execution-Aktivität** in einer Azure Data Factory-Pipeline können Sie einen Webdienst von ML Studio (Classic) aufrufen, um Vorhersagen zu den Daten in einem Batch zu treffen.

Im Laufe der Zeit müssen die Vorhersagemodelle in den Bewertungsexperimenten von ML Studio (Classic) mit neuen Eingabedatasets neu trainiert werden. Sie können ein Modell über eine Data Factory-Pipeline neu trainieren, indem Sie die folgenden Schritte ausführen:

1. Veröffentlichen Sie das Trainingsexperiment (nicht das Vorhersageexperiment) als Webdienst. Für diesen Schritt verwenden Sie ML Studio (Classic), wie Sie es beim Veröffentlichen des Vorhersageexperiments als Webdienst im vorherigen Szenario getan haben.
2. Verwenden Sie die Batch Execution-Aktivität von ML Studio (Classic), um den Webdienst für das Trainingsexperiment aufzurufen. Grundsätzlich können Sie die Batch Execution-Aktivität von ML Studio (Classic) verwenden, um den Trainingswebdienst und den Bewertungswebdienst aufzurufen.

Nachdem Sie das erneute Training abgeschlossen haben, aktualisieren Sie den Bewertungswebdienst (das Vorhersageexperiment, das als Webdienst verfügbar gemacht wurde) mithilfe der **Update-Ressourcenaktivität von ML Studio (Classic)** mit dem neu trainierten Modell. Einzelheiten finden Sie im Artikel [Aktualisieren von Modellen mithilfe der Ressourcenaktualisierungsaktivität](update-machine-learning-models.md).

## <a name="ml-studio-classic-linked-service"></a>Verknüpfter Dienst von ML Studio (Classic)

Sie erstellen einen verknüpften Dienst in **ML Studio (Classic)** , um einen Webdienst von ML Studio (Classic) mit einer Azure Data Factory zu verknüpfen. Der verknüpfte Dienst wird von der Batch Execution-Aktivität und der [Update-Ressourcenaktivität](update-machine-learning-models.md) von ML Studio (Classic) verwendet.

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Im Artikel zu [von Azure Data Factory unterstützten Compute-Umgebungen](compute-linked-services.md) finden Sie Beschreibungen der Eigenschaften in der JSON-Definition.

ML Studio (Classic) unterstützt sowohl klassische als auch neue Webdienste für Ihr Vorhersageexperiment. Sie können denjenigen wählen, den Sie in Data Factory verwenden möchten. Um die zur Erstellung des verknüpften Diensts von ML Studio (Classic) erforderlichen Informationen zu erhalten, besuchen Sie https://services.azureml.net. Hier sind Ihre sämtlichen neuen und klassischen Webdienste aufgelistet. Klicken Sie auf den Webdienst, auf den Sie zugreifen möchten, und klicken Sie auf die Seite **Consume**. Kopieren Sie **Primary Key** für die **apiKey**-Eigenschaft und **Batch Requests** für die **mlEndpoint**-Eigenschaft.

![Webdienste von ML Studio (Classic)](./media/transform-data-using-machine-learning/web-services.png)

## <a name="ml-studio-classic-batch-execution-activity"></a>Batch Execution-Aktivität in ML Studio (Classic)

Der folgende JSON-Codeausschnitt definiert eine Batchausführungsaktivität für Azure Machine Learning Studio (Classic). Die Aktivitätsdefinition verwendet einen Verweis auf den zuvor erstellten verknüpften Dienst von ML Studio (Classic).

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| Eigenschaft          | BESCHREIBUNG                              | Erforderlich |
| :---------------- | :--------------------------------------- | :------- |
| name              | Name der Aktivität in der Pipeline     | Ja      |
| description       | Ein Text, der beschreibt, was mit der Aktivität ausgeführt wird.  | Nein       |
| type              | Für die Data Lake Analytics-U-SQL-Aktivität ist der Aktivitätstyp **AzureMLBatchExecution**. | Ja      |
| linkedServiceName | Mit dem verknüpften Dienst von ML Studio (Classic) verknüpfte Dienste. Weitere Informationen zu diesem verknüpften Dienst finden Sie im Artikel [Von Azure Data Factory unterstützten Compute-Umgebungen](compute-linked-services.md). | Ja      |
| webServiceInputs  | Schlüssel-Wert-Paare zur Zuordnung der Namen von Eingaben des Webdiensts von ML Studio (Classic). Der Schlüssel muss mit den Eingabeparametern übereinstimmen, die im veröffentlichten Webdienst von ML Studio (Classic) definiert sind. Der Wert ist ein Paar aus verknüpften Azure Storage-Diensten und „FilePath“-Eigenschaften zum Angeben der Speicherorte von Eingabeblobs. | Nein       |
| webServiceOutputs | Schlüssel-Wert-Paare zur Zuordnung der Namen von Ausgaben des Webdiensts von ML Studio (Classic). Der Schlüssel muss mit den Ausgabeparametern übereinstimmen, die im veröffentlichten Webdienst von ML Studio (Classic) definiert sind. Der Wert ist ein Paar aus verknüpften Azure Storage-Diensten und „FilePath“-Eigenschaften zum Angeben der Speicherorte von Ausgabeblobs. | Nein       |
| globalParameters  | Schlüssel-Wert-Paare, die an den Endpunkt des Batchausführungsdiensts von ML Studio (Classic) übergeben werden. Die Schlüssel müssen mit den Webdienstparametern übereinstimmen, die im veröffentlichten Webdienst von Azure ML (Classic) definiert sind. Die Werte werden in der GlobalParameters-Eigenschaft der Batchausführungsanforderung von ML Studio (Classic) übergeben. | Nein       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Szenario 1: Experimente mit Eingaben/Ausgaben für den Webdienst, die auf Daten in Azure Blob Storage verweisen

In diesem Szenario trifft der Webdienst von ML Studio (Classic) Vorhersagen anhand der Daten aus einer Datei in Azure Blob Storage und speichert die Vorhersageergebnisse in Blob Storage. Das folgende JSON-Skript definiert eine Data Factory-Pipeline mit einer AzureMLBatchExecution-Aktivität. Auf die Eingabe- und Ausgabedaten in Azure Blob Storage wird über ein Paar aus „LinkedName“ und „FilePath“ verwiesen. In dem Beispiel des verknüpften Diensts sind die Ein- und Ausgaben unterschiedlich. Sie können verschiedene verknüpfte Dienste für alle Ihre Ein- und Ausgaben verwenden. Data Factory ist in der Lage, die richtigen Dateien auszuwählen und an den Webdienst von ML Studio (Classic) zu senden.

> [!IMPORTANT]
> In Ihrem Experiment in ML Studio (Classic) haben Eingabe- und Ausgabeports von Webdiensten sowie globale Parameter Standardnamen („input1“, „input2“), die Sie anpassen können. Die Namen, die Sie für die Einstellungen webServiceInputs, webServiceOutputs und globalParameters verwenden, müssen den Namen in den Experimenten genau entsprechen. Sie können die Beispiel-Anforderungspayload auf der Hilfeseite für die Batchausführung für Ihren ML Studio (Classic)-Endpunkt anzeigen, um die erwartete Zuordnung zu überprüfen.


```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Szenario 2: Experimente mit Reader- und Writer-Modulen zum Verweisen auf Daten in verschiedenen Speichern
Ein weiteres gängiges Szenario beim Erstellen von Experimenten mit ML Studio (Classic) ist die Verwendung der Module „Daten importieren“ und „Daten ausgeben“. Das Modul „Import Data“ wird verwendet, um Daten in ein Experiment zu laden, während mit dem Modul „Output Data“ Daten aus Ihren Experimenten gespeichert werden. Einzelheiten zu den Modulen „Import Data“ und „Output Data“ finden Sie in der MSDN Library in den Themen [Import Data](/azure/machine-learning/studio-module-reference/import-data) und [Output Data](/azure/machine-learning/studio-module-reference/export-data).

Bei Verwendung der Module „Import Data“ und „Output Data“ empfiehlt es sich, einen Webdienstparameter für jede Eigenschaft dieser Module zu verwenden. Durch diese Webparameter können Sie die Werte zur Laufzeit konfigurieren. Sie können beispielsweise ein Experiment mit einem Import Data-Modul erstellen, das eine Azure SQL-Datenbank mit dem Namen „XXX.database.windows.net“ verwendet. Nach Bereitstellung des Webdiensts sollen die Nutzer des Webdiensts einen weiteren logischen SQL-Server mit dem Namen `YYY.database.windows.net` angeben können. Durch Verwendung eines Webdienstparameters wird ermöglicht, dass dieser Wert konfiguriert werden kann.

> [!NOTE]
> Eingaben und Ausgaben für den Webdienst unterscheiden sich von Webdienstparametern. Im ersten Szenario haben Sie gesehen, wie Eingaben und Ausgaben für einen Webdienst von ML Studio (Classic) angegeben werden können. In diesem Szenario übergeben Sie Parameter für einen Webdienst, die Eigenschaften der Module „Import Data“ und „Output Data“ entsprechen.

Betrachten wir nun ein Szenario für die Verwendung von Webdienstparametern. Sie haben einen ML Studio (Classic)-Webdienst bereitgestellt, bei dem Daten mithilfe eines Lesemoduls aus einer von ML Studio (Classic) unterstützten Datenquelle (z. B. Azure SQL-Datenbank) gelesen werden. Nach der Batchausführung werden die Ergebnisse mit einem Writer-Modul (Azure SQL-Datenbank) geschrieben.  In den Experimenten sind keine Eingaben und Ausgaben für den Webdienst definiert. In diesem Fall wird empfohlen, die relevanten Webdienstparameter für das Reader- und das Writer-Modul zu konfigurieren. Bei dieser Konfiguration können das Reader- und Writer-Modul bei Verwendung der AzureMLBatchExecution-Aktivität konfiguriert werden. Sie geben die Webdienstparameter wie folgt im Abschnitt **globalParameters** im JSON-Code der Aktivität an.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> Bei Webdienstparametern wird Groß-/Kleinschreibung unterschieden, weshalb Sie sicherstellen müssen, dass die Namen, die Sie im JSON-Code der Aktivität angeben, denjenigen entsprechen, die vom Webdienst verfügbar gemacht werden.

Nachdem Sie das erneute Training abgeschlossen haben, aktualisieren Sie den Bewertungswebdienst (das Vorhersageexperiment, das als Webdienst verfügbar gemacht wurde) mithilfe der **Update-Ressourcenaktivität von ML Studio (Classic)** mit dem neu trainierten Modell. Einzelheiten finden Sie im Artikel [Aktualisieren von Modellen mithilfe der Ressourcenaktualisierungsaktivität](update-machine-learning-models.md).

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