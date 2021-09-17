---
title: Erstellen einer Azure Data Factory-Instanz mithilfe der REST-API
description: Erstellen Sie eine Azure Data Factory-Pipeline, um Daten in einem Azure Blob Storage von einem Speicherort in einen anderen zu kopieren.
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 05/31/2021
ms.author: jingwang
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1fb0686d975c2342bcd562e75f739d83ee0a51f6
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681534"
---
# <a name="quickstart-create-an-azure-data-factory-and-pipeline-by-using-the-rest-api"></a>Schnellstart: Erstellen einer Azure Data Factory und einer Pipeline mithilfe der REST-API

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuelle Version](quickstart-create-data-factory-rest-api.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory ist ein cloudbasierter Datenintegrationsdienst, mit dem Sie datengesteuerte Workflows in der Cloud erstellen können, um Datenverschiebungen und Datentransformationen zu orchestrieren und zu automatisieren. Mit Azure Data Factory können Sie datengesteuerte Workflows (sogenannte Pipelines) erstellen und planen, die Daten aus unterschiedlichen Datenspeichern erfassen, diese Daten mithilfe von Compute Services wie Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics und Azure Machine Learning verarbeiten oder transformieren und die Ausgabedaten in Datenspeichern wie Azure Synapse Analytics veröffentlichen, damit diese von BI-Anwendungen (Business Intelligence) genutzt werden können.

Diese Schnellstartanleitung beschreibt, wie Sie die REST-API verwenden, um eine Azure Data Factory zu erstellen. Die Pipeline in diesem Beispiel kopiert Daten in einem Azure Blob Storage von einem Speicherort in einen anderen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure-Abonnement**. Falls Sie nicht über ein Abonnement verfügen, können Sie ein [kostenloses Testkonto](https://azure.microsoft.com/pricing/free-trial/) erstellen.
* **Azure Storage-Konto**. Sie verwenden den Blob Storage als **Quelldatenspeicher** und als **Senkendatenspeicher**. Wenn Sie kein Azure Storage-Konto besitzen, finden Sie im Artikel [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md) Schritte zum Erstellen eines solchen Kontos.
* Erstellen Sie einen **Blobcontainer** in Blob Storage, erstellen Sie einen **Eingabeordner** im Container, und laden Sie einige Dateien in den Ordner. Sie können mithilfe von Tools wie [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) eine Verbindung mit Azure Blob Storage herstellen, einen Blobcontainer erstellen, eine Eingabedatei hochladen und die Ausgabedatei überprüfen.
* Installieren Sie **Azure PowerShell**. Befolgen Sie die Anweisungen unter [Get started with Azure PowerShell cmdlets](/powershell/azure/install-Az-ps) (Erste Schritte mit Azure PowerShell-Cmdlets). Diese Schnellstartanleitung verwendet PowerShell, um REST-API-Aufrufe aufzurufen.
* **Erstellen Sie eine Anwendung in Azure Active Directory**, indem Sie [diese Anweisungen](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) befolgen. Notieren Sie sich die folgenden Werte, die Sie in späteren Schritten benötigen: **Anwendungs-ID**, **clientSecrets** und **Mandanten-ID**. Weisen Sie die Anwendung der Rolle „**Mitwirkender**“ auf Abonnement- oder Ressourcengruppenebene zu.
>[!NOTE]
>   Für Sovereign Clouds müssen Sie die entsprechenden cloudspezifischen Endpunkte für ActiveDirectoryAuthority und ResourceManagerUrl (BaseUri) verwenden. Sie können PowerShell verwenden, um problemlos die URLs der Endpunkte für verschiedene Clouds zu erhalten. Hierzu führen Sie „Get-AzEnvironment | Format-List“ aus. Hiermit wird eine Liste der Endpunkte für jede Cloudumgebung zurückgegeben.  
>    
## <a name="set-global-variables"></a>Festlegen von globalen Variablen

1. Starten Sie **PowerShell**. Lassen Sie Azure PowerShell bis zum Ende dieser Schnellstartanleitung geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen.

    Führen Sie den folgenden Befehl aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung beim Azure-Portal verwendet haben:

    ```powershell
    Connect-AzAccount
    ```
    Führen Sie den folgenden Befehl aus, um alle Abonnements für dieses Konto anzuzeigen:

    ```powershell
    Get-AzSubscription
    ```
    Führen Sie den folgenden Befehl aus, um das gewünschte Abonnement auszuwählen: Ersetzen Sie **SubscriptionId** durch die ID Ihres Azure-Abonnements:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Nachdem Sie die Platzhalter durch Ihre eigenen Werte ersetzt haben, führen Sie die folgenden Befehle aus, um globale Variablen festzulegen, die in späteren Schritten verwendet werden.

    ```powershell
    $tenantID = "<your tenant ID>"
    $appId = "<your application ID>"
    $clientSecrets = "<your clientSecrets for the application>"
    $subscriptionId = "<your subscription ID to create the factory>"
    $resourceGroupName = "<your resource group to create the factory>"
    $factoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $apiVersion = "2018-06-01"
    ```

## <a name="authenticate-with-azure-ad"></a>Authentifizierung über Azure AD

Führen Sie die folgenden Befehle aus, um die Authentifizierung bei Azure Active Directory (AAD) durchzuführen:

```powershell
$credentials = Get-Credential -UserName $appId
Connect-AzAccount -ServicePrincipal  -Credential $credentials -Tenant $tenantID
```
Sie werden zur Eingabe des Kennworts aufgefordert. Verwenden Sie den Wert in der Variablen „clientSecrets“.

Wenn Sie das Zugriffstoken abrufen müssen 

```powershell

GetToken

```

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

Führen Sie die folgenden Befehle aus, um eine Data Factory zu erstellen:

```powershell
$body = @"
{
    "location": "East US",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
"@

$response =   Invoke-AzRestMethod -SubscriptionId ${subscriptionId}  -ResourceGroupName ${resourceGroupName} -ResourceProviderName  Microsoft.DataFactory -ResourceType "factories" -Name  ${factoryName} -ApiVersion ${apiVersion} -Method PUT -Payload ${body}
$response.Content  

```

Beachten Sie folgende Punkte:

* Der Name der Azure Data Factory muss global eindeutig sein. Wenn die folgende Fehlermeldung angezeigt wird, ändern Sie den Namen, und wiederholen Sie den Vorgang.

    ```
    Data factory name "ADFv2QuickStartDataFactory" is not available.
    ```
* Eine Liste der Azure-Regionen, in denen Data Factory derzeit verfügbar ist, finden Sie, indem Sie die für Sie interessanten Regionen auf der folgenden Seite auswählen und dann **Analysen** erweitern, um **Data Factory** zu finden: [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Die von der Data Factory verwendeten Datenspeicher (Azure Storage, Azure SQL-Datenbank usw.) und Computedienste (HDInsight usw.) können sich in anderen Regionen befinden.

Hier ist der Inhalt der Beispielantwort:

```json

{  
    "name":"<dataFactoryName>",
    "identity":{  
        "type":"SystemAssigned",
        "principalId":"<service principal ID>",
        "tenantId":"<tenant ID>"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{  
        "provisioningState":"Succeeded",
        "createTime":"2019-09-03T02:10:27.056273Z",
        "version":"2018-06-01"
    },
    "eTag":"\"0200c876-0000-0100-0000-5d6dcb930000\"",
    "location":"East US",
    "tags":{  

    }
}
```
## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten

Um Ihre Datenspeicher und Compute Services mit der Data Factory zu verknüpfen, können Sie verknüpfte Dienste in einer Data Factory erstellen. In dieser Schnellstartanleitung müssen Sie nur einen verknüpften Azure Storage-Dienst für die Quell- und Senkendatenspeicher für den Kopiervorgang erstellen – in diesem Beispiel als „AzureStorageLinkedService“ bezeichnet.

Führen Sie die folgenden Befehle aus, um einen verknüpften Dienst mit dem Namen **AzureStorageLinkedService** zu erstellen:

Ersetzen Sie &lt;accountName&gt; und &lt;accountKey&gt; durch den Namen bzw. Schlüssel Ihres Azure-Speicherkontos, bevor Sie die Befehle ausführen.

```powershell
$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/linkedservices/AzureStorageLinkedService?api-version=${apiVersion}"

$body = @"
{  
    "name":"AzureStorageLinkedService",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response.content
```

Hier ist die Beispielausgabe:

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/linkedservices/AzureStorageLinkedService",
    "name":"AzureStorageLinkedService",
    "type":"Microsoft.DataFactory/factories/linkedservices",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"DefaultEndpointsProtocol=https;AccountName=<accountName>;"
        }
    },
    "etag":"07011a57-0000-0100-0000-5d6e14a20000"
}
```
## <a name="create-datasets"></a>Erstellen von Datasets

Sie definieren ein Dataset, das die Daten repräsentiert, die aus einer Quelle in eine Senke kopiert werden sollen. In diesem Beispiel erstellen Sie zwei Datasets: „InputDataset“ und „OutputDataset“. Sie verweisen auf den mit Azure Storage verknüpften Dienst, den Sie im vorherigen Abschnitt erstellt haben. Das Eingabedataset stellt die Quelldaten im Eingabeordner dar. In der Definition des Eingabedatasets geben Sie den Blobcontainer (adftutorial), den Ordner (input) und die Datei (emp.txt) mit den Quelldaten an. Das Ausgabedataset stellt die Daten dar, die zum Ziel kopiert werden. In der Definition des Ausgabedatasets geben Sie den Blobcontainer (adftutorial), den Ordner (output) und die Datei an, in die die Daten kopiert werden.

**Erstellen von „InputDataset“**

```powershell

$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/InputDataset?api-version=${apiVersion}"

$body = @"
{  
    "name":"InputDataset",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "fileName":"emp.txt",
                "folderPath":"input",
                "container":"adftutorial"
            }
        }
    }
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response  

```

Hier ist die Beispielausgabe:

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/InputDataset",
    "name":"InputDataset",
    "type":"Microsoft.DataFactory/factories/datasets",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":"@{type=AzureBlobStorageLocation; fileName=emp.txt; folderPath=input; container=adftutorial}"
        }
    },
    "etag":"07011c57-0000-0100-0000-5d6e14b40000"
}
```
**Erstellen von „OutputDataset“**

```powershell
$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/OutputDataset?api-version=${apiVersion}"

$body = @"
{  
    "name":"OutputDataset",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "folderPath":"output",
                "container":"adftutorial"
            }
        }
    }
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content
```

Hier ist die Beispielausgabe:

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/OutputDataset",
    "name":"OutputDataset",
    "type":"Microsoft.DataFactory/factories/datasets",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":"@{type=AzureBlobStorageLocation; folderPath=output; container=adftutorial}"
        }
    },
    "etag":"07013257-0000-0100-0000-5d6e18920000"
}
```
## <a name="create-a-pipeline"></a>Erstellen einer Pipeline

In diesem Beispiel enthält die Pipeline eine Copy-Aktivität. Die Copy-Aktivität verweist auf „InputDataset“ und „OutputDataset“, die im vorherigen Schritt als Eingabe und Ausgabe erstellt wurden.

```powershell
$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartPipeline?api-version=${apiVersion}"

$body = @"
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "BinarySource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        }
                    },
                    "sink": {
                        "type": "BinarySink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging": false
                },
                "inputs": [
                    {
                        "referenceName": "InputDataset",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "OutputDataset",
                        "type": "DatasetReference"
                    }
                ]
            }
        ],
        "annotations": []
    }
}
"@
$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content
```

Hier ist die Beispielausgabe:

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/pipelines/Adfv2QuickStartPipeline",
    "name":"Adfv2QuickStartPipeline",
    "type":"Microsoft.DataFactory/factories/pipelines",
    "properties":{  
        "activities":[  
            "@{name=CopyFromBlobToBlob; type=Copy; dependsOn=System.Object[]; policy=; userProperties=System.Object[]; typeProperties=; inputs=System.Object[]; outputs=System.Object[]}"
        ],
        "annotations":[  

        ]
    },
    "etag":"07012057-0000-0100-0000-5d6e14c00000"
}
```

## <a name="create-pipeline-run"></a>Erstellen einer Pipelineausführung

In diesem Schritt lösen Sie eine Pipelineausführung aus. Die im Antworttext zurückgegebene ID der Pipelineausführung wird später in der Überwachungs-API verwendet.

```powershell
$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartPipeline/createRun?api-version=${apiVersion}"

$response =  Invoke-AzRestMethod  -Path ${path}  -Method POST 
$response.content 
```

Hier ist die Beispielausgabe:

```json
{  
    "runId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc"
}
```

Sie können auch die „runId“ mit folgendem Befehl abrufen: 
```powershell

($response.content | ConvertFrom-Json).runId

```

## <a name="parameterize-your-pipeline"></a>Parametrisieren Ihrer Pipeline 

Sie können eine Pipeline mit Parametern erstellen. Im folgenden Beispiel erstellen wir ein Eingabedataset und ein Ausgabedataset, das Eingabe- und Ausgabedateinamen als Parameter für die Pipeline verwenden kann. 


## <a name="create-parameterized-input-dataset"></a>Erstellen eines parametrisierten Eingabedatasets 

Definieren Sie den Parameter „strInputFileName“, und verwenden Sie ihn als Dateiname für das Dataset. 

```powershell

$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/ParamInputDataset?api-version=${apiVersion}"

$body = @"
{
    "name": "ParamInputDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "strInputFileName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@dataset().strInputFileName",
                    "type": "Expression"
                },
                "folderPath": "input",
                "container": "adftutorial"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content

```

Hier ist die Beispielausgabe: 

```json
{
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/datasets/ParamInputDataset",
    "name": "ParamInputDataset",
    "type": "Microsoft.DataFactory/factories/datasets",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "strInputFileName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@dataset().strInputFileName",
                    "type": "Expression"
                },
                "folderPath": "input",
                "container": "adftutorial"
            }
        }
    },
    "etag": "00000000-0000-0000-0000-000000000000"
}

```


## <a name="create-parameterized-output-dataset"></a>Erstellen eines parametrisierten Ausgabedatasets 


Definieren Sie den Parameter „strOutputFileName“, und verwenden Sie ihn als Dateiname für das Dataset. 

```powershell


$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/ParamOutputDataset?api-version=${apiVersion}"
$body = @"
{
    "name": "ParamOutputDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "strOutPutFileName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@dataset().strOutPutFileName",
                    "type": "Expression"
                },
                "folderPath": "output",
                "container": "adftutorial"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}

"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content

```

Hier ist die Beispielausgabe: 

```json
{
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/datasets/ParamOutputDataset",
    "name": "ParamOutputDataset",
    "type": "Microsoft.DataFactory/factories/datasets",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "strOutPutFileName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@dataset().strOutPutFileName",
                    "type": "Expression"
                },
                "folderPath": "output",
                "container": "adftutorial"
            }
        }
    },
    "etag": "00000000-0000-0000-0000-000000000000"
}
```

## <a name="create-parameterized-pipeline"></a>Erstellen einer parametrisierten Pipeline 

Definieren Sie eine Pipeline mit zwei Parametern auf Pipelineebene: „strParamInputFileName“ und „strParamOutputFileName“. Verknüpfen Sie dann diese beiden Parameter mit den Parametern „strInputFileName“ und „strOutputFileName“ der Datasets. 

```powershell

$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartParamPipeline?api-version=${apiVersion}"

$body = @"
{
    "name": "Adfv2QuickStartParamPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "BinarySource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        }
                    },
                    "sink": {
                        "type": "BinarySink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging": false
                },
                "inputs": [
                    {
                        "referenceName": "ParamInputDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "strInputFileName": {
                                "value": "@pipeline().parameters.strParamInputFileName",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "ParamOutputDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "strOutPutFileName": {
                                "value": "@pipeline().parameters.strParamOutputFileName",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],   

        "parameters": {
            "strParamInputFileName": {
              "type": "String"
            },
            "strParamOutputFileName": {
              "type": "String"
            }
          }
    }
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content


```

Hier ist die Beispielausgabe: 

```json

{
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/pipelines/Adfv2QuickStartParamPipeline",
    "name": "Adfv2QuickStartParamPipeline",
    "type": "Microsoft.DataFactory/factories/pipelines",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "BinarySource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        }
                    },
                    "sink": {
                        "type": "BinarySink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging": false
                },
                "inputs": [
                    {
                        "referenceName": "ParamInputDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "strInputFileName": {
                                "value": "@pipeline().parameters.strParamInputFileName",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "ParamOutputDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "strOutPutFileName": {
                                "value": "@pipeline().parameters.strParamOutputFileName",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],
        "parameters": {
            "strParamInputFileName": {
                "type": "String"
            },
            "strParamOutputFileName": {
                "type": "String"
            }
        }
    },
    "etag": "5e01918d-0000-0100-0000-60d569a90000"
}

```

## <a name="create-pipeline-run-with-parameters"></a>Erstellen einer Pipelineausführung mit Parametern 

Sie können jetzt Werte des Parameters angeben, wenn Sie die Pipelineausführung erstellen. 

```powershell

$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartParamPipeline/createRun?api-version=${apiVersion}"

$body = @"
{  
        "strParamInputFileName": "emp2.txt",
        "strParamOutputFileName": "aloha.txt"
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method POST -Payload $body
$response.content
$runId  = ($response.content | ConvertFrom-Json).runId

```
Hier ist die Beispielausgabe: 

```json
{"runId":"ffc9c2a8-d86a-46d5-9208-28b3551007d8"}
```


## <a name="monitor-pipeline"></a>Überwachen der Pipeline

1. Führen Sie das folgende Skript aus, um den Status der Pipelineausführung kontinuierlich zu überwachen, bis das Kopieren der Daten beendet ist.

    ```powershell
        $path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
        
        
        while ($True) {
        
            $response =  Invoke-AzRestMethod  -Path ${path}  -Method GET 
            $response = $response.content | ConvertFrom-Json
        
            Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"
        
            if ( ($response.Status -eq "InProgress") -or ($response.Status -eq "Queued") -or ($response.Status -eq "In Progress") ) {
                Start-Sleep -Seconds 10
            }
            else {
                $response | ConvertTo-Json
                break
            }
        }
    ```

    Hier ist die Beispielausgabe:

    ```json
        {
          "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/pipelineruns/ffc9c2a8-d86a-46d5-9208-28b3551007d8",
          "runId": "ffc9c2a8-d86a-46d5-9208-28b3551007d8",
          "debugRunId": null,
          "runGroupId": "ffc9c2a8-d86a-46d5-9208-28b3551007d8",
          "pipelineName": "Adfv2QuickStartParamPipeline",
          "parameters": {
            "strParamInputFileName": "emp2.txt",
            "strParamOutputFileName": "aloha.txt"
          },
          "invokedBy": {
            "id": "9c0275ed99994c18932317a325276544",
            "name": "Manual",
            "invokedByType": "Manual"
          },
          "runStart": "2021-06-25T05:34:06.8424413Z",
          "runEnd": "2021-06-25T05:34:13.2936585Z",
          "durationInMs": 6451,
          "status": "Succeeded",
          "message": "",
          "lastUpdated": "2021-06-25T05:34:13.2936585Z",
          "annotations": [],
          "runDimension": {},
          "isLatest": true
        }
    ```

2. Führen Sie das folgende Skript aus, um Ausführungsdetails zur Kopieraktivität abzurufen, z.B. die Größe der gelesenen/geschriebenen Daten.

    ```powershell
         $path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelineruns/${runId}/queryActivityruns?api-version=${apiVersion}"
        
        
        while ($True) {
        
            $response =  Invoke-AzRestMethod  -Path ${path}  -Method POST 
            $responseContent = $response.content | ConvertFrom-Json
            $responseContentValue = $responseContent.value
        
            Write-Host  "Activity run status: " $responseContentValue.Status -foregroundcolor "Yellow"
        
            if ( ($responseContentValue.Status -eq "InProgress") -or ($responseContentValue.Status -eq "Queued") -or ($responseContentValue.Status -eq "In Progress") ) {
                Start-Sleep -Seconds 10
            }
            else {
                $responseContentValue | ConvertTo-Json
                break
            }
        }
    ```
    Hier ist die Beispielausgabe:

    ```json
        {
          "activityRunEnd": "2021-06-25T05:34:11.9536764Z",
          "activityName": "CopyFromBlobToBlob",
          "activityRunStart": "2021-06-25T05:34:07.5161151Z",
          "activityType": "Copy",
          "durationInMs": 4437,
          "retryAttempt": null,
          "error": {
            "errorCode": "",
            "message": "",
            "failureType": "",
            "target": "CopyFromBlobToBlob",
            "details": ""
          },
          "activityRunId": "40bab243-9bbf-4538-9336-b797a2f98e2b",
          "iterationHash": "",
          "input": {
            "source": {
              "type": "BinarySource",
              "storeSettings": "@{type=AzureBlobStorageReadSettings; recursive=True}"
            },
            "sink": {
              "type": "BinarySink",
              "storeSettings": "@{type=AzureBlobStorageWriteSettings}"
            },
            "enableStaging": false
          },
          "linkedServiceName": "",
          "output": {
            "dataRead": 134,
            "dataWritten": 134,
            "filesRead": 1,
            "filesWritten": 1,
            "sourcePeakConnections": 1,
            "sinkPeakConnections": 1,
            "copyDuration": 3,
            "throughput": 0.044,
            "errors": [],
            "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
            "usedDataIntegrationUnits": 4,
            "billingReference": {
              "activityType": "DataMovement",
              "billableDuration": ""
            },
            "usedParallelCopies": 1,
            "executionDetails": [
              "@{source=; sink=; status=Succeeded; start=06/25/2021 05:34:07; duration=3; usedDataIntegrationUnits=4; usedParallelCopies=1; profile=; detailedDurations=}"
            ],
            "dataConsistencyVerification": {
              "VerificationResult": "NotVerified"
            },
            "durationInQueue": {
              "integrationRuntimeQueue": 0
            }
          },
          "userProperties": {},
          "pipelineName": "Adfv2QuickStartParamPipeline",
          "pipelineRunId": "ffc9c2a8-d86a-46d5-9208-28b3551007d8",
          "status": "Succeeded",
          "recoveryStatus": "None",
          "integrationRuntimeNames": [
            "defaultintegrationruntime"
          ],
          "executionDetails": {
            "integrationRuntime": [
              "@{name=DefaultIntegrationRuntime; type=Managed; location=East US; nodes=}"
            ]
          },
          "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/pipelineruns/ffc9c2a8-d86a-46d5-9208-28b3551007d8/activityruns/40bab243-9bbf-4538-9336-b797a2f98e2b"
        }
    ```
## <a name="verify-the-output"></a>Überprüfen der Ausgabe

Verwenden Sie Azure Storage-Explorer, um zu überprüfen, ob die Datei wie von Ihnen beim Erstellen der Pipelineausführung angegeben von „inputPath“ nach „outputPath“ kopiert wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Die im Rahmen dieser Schnellstartanleitung erstellten Ressourcen können auf zwei Arten bereinigt werden. Sie können die [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md) einschließlich aller darin enthaltenen Ressourcen löschen. Falls die anderen Ressourcen erhalten bleiben sollen, löschen Sie nur die Data Factory, die Sie in diesem Tutorial erstellt haben.

Führen Sie den folgenden Befehl aus, um die gesamte Ressourcengruppe zu löschen:
```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Führen Sie den folgenden Befehl aus, um nur die Data Factory zu löschen:

```powershell
Remove-AzDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>Nächste Schritte
Die Pipeline in diesem Beispiel kopiert Daten in einem Azure Blob Storage von einem Speicherort in einen anderen. Arbeiten Sie die [Tutorials](tutorial-copy-data-dot-net.md) durch, um zu erfahren, wie Sie Data Factory in anderen Szenarien verwenden können.
