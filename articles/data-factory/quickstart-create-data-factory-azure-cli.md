---
title: 'Schnellstart: Erstellen einer Azure Data Factory-Instanz mithilfe der Azure CLI'
description: In dieser Schnellstartanleitung wird eine Azure Data Factory-Instanz erstellt, einschließlich eines verknüpften Diensts, eines Datasets und einer Pipeline. Sie können die Pipeline für eine Aktion zum Kopieren von Dateien ausführen.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: quickstart
ms.date: 08/27/2021
ms.custom: template-quickstart, devx-track-azurecli
ms.openlocfilehash: 30f521567f06eab04aceee462ffc44b817b63333
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187701"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>Schnellstart: Erstellen einer Azure Data Factory-Instanz mithilfe der Azure CLI

Diese Schnellstartanleitung beschreibt, wie Sie die Azure CLI verwenden, um eine Azure Data Factory-Instanz zu erstellen. Die in dieser Data Factory erstellte Pipeline kopiert Daten aus einem Ordner in einen anderen Ordner in Azure Blob Storage. Informationen zum Transformieren von Daten mithilfe von Azure Data Factory finden Sie unter [Transformieren von Data in Azure Data Factory](transform-data.md).

Eine Einführung in den Azure Data Factory-Dienst finden Sie unter [Einführung in Azure Data Factory](introduction.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> Damit Sie Data Factory-Instanzen erstellen können, muss das Benutzerkonto, mit dem Sie sich bei Azure anmelden, ein Mitglied der Rolle „Mitwirkender“ oder „Besitzer“ oder ein Administrator des Azure-Abonnements sein. Weitere Informationen finden Sie unter [Azure-Rollen](quickstart-create-data-factory-powershell.md#azure-roles).

## <a name="prepare-a-container-and-test-file"></a>Vorbereiten eines Containers und einer Testdatei

In dieser Schnellstartanleitung wird ein Azure Storage-Konto verwendet, das einen Container mit einer Datei enthält.

1. Verwenden Sie zum Erstellen einer Ressourcengruppe namens `ADFQuickStartRG` den Befehl [az group create](/cli/azure/group#az_group_create):

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. Erstellen Sie ein Speicherkonto mithilfe des Befehls [az storage account create](/cli/azure/storage/container#az_storage_container_create):

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. Erstellen Sie mithilfe des Befehls [az storage container create](/cli/azure/storage/container#az_storage_container_create) einen Container namens `adftutorial`:

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. Erstellen Sie im lokalen Verzeichnis eine Datei namens `emp.txt` zum Hochladen. In Azure Cloud Shell können Sie das aktuelle Arbeitsverzeichnis mithilfe des Bash-Befehls `echo $PWD` ermitteln. Sie können Bash-Standardbefehle wie `cat` verwenden, um eine Datei zu erstellen:

   ```console
   cat > emp.txt
   This is text.
   ```

   Verwenden Sie **STRG+D**, um die neue Datei zu speichern.

1. Verwenden Sie zum Hochladen der neuen Datei in Ihren Azure-Speichercontainer den Befehl [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload):

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   Mit diesem Befehl erfolgt der Upload in einen neuen Ordner mit dem Namen `input`.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

Führen Sie zum Erstellen einer Azure Data Factory-Instanz den Befehl [az datafactory create](/cli/azure/datafactory#az_datafactory_create) aus:

```azurecli
az datafactory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> Ersetzen Sie `ADFTutorialFactory` durch einen global eindeutigen Data Factory-Namen, etwa „ADFTutorialFactorySP1127“.

Die von Ihnen erstellte Data Factory-Instanz können Sie mithilfe des Befehls [az datafactory show](/cli/azure/datafactory#az_datafactory_factory_show) anzeigen:

```azurecli
az datafactory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>Erstellen eines verknüpften Diensts und von Datasets

Erstellen Sie als Nächstes einen verknüpften Dienst und zwei Datasets.

1. Verwenden Sie den Befehl [az storage account show-connection-string](/cli/azure/datafactory#az_datafactory_factory_show), um die Verbindungszeichenfolge für Ihr Speicherkonto abzurufen:

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. Erstellen Sie in Ihrem Arbeitsverzeichnis eine JSON-Datei mit folgendem Inhalt, die ihre eigene Verbindungszeichenfolge aus dem vorherigen Schritt enthält. Geben Sie der Datei den Namen `AzureStorageLinkedService.json`:

    ```json
    {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
        }
    }
    ```

1. Erstellen Sie mithilfe des Befehls [az datafactory linked-service create](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_create) einen verknüpften Dienst namens `AzureStorageLinkedService`:

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. Erstellen Sie in Ihrem Arbeitsverzeichnis eine JSON-Datei namens `InputDataset.json` mit dem folgenden Inhalt:

    ```json
    {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "emp.txt",
                "folderPath": "input",
                "container": "adftutorial"
            }
        }
    }
    ```

1. Erstellen Sie mit dem Befehl [az datafactory dataset create](/cli/azure/datafactory/dataset#az_datafactory_dataset_create) ein Eingabedataset namens `InputDataset`:

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. Erstellen Sie in Ihrem Arbeitsverzeichnis eine JSON-Datei namens `OutputDataset.json` mit dem folgenden Inhalt:

    ```json
    {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "folderPath": "output",
                "container": "adftutorial"
            }
        }
    }
    ```

1. Erstellen Sie mit dem Befehl [az datafactory dataset create](/cli/azure/datafactory/dataset#az_datafactory_dataset_create) ein Ausgabedataset namens `OutputDataset`:

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>Erstellen und Ausführen der Pipeline

Erstellen Sie abschließend die Pipeline, und führen Sie sie aus.

1. Erstellen Sie in Ihrem Arbeitsverzeichnis eine JSON-Datei namens `Adfv2QuickStartPipeline.json` mit dem folgenden Inhalt:

    ```json
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
    ```

1. Erstellen Sie mit dem Befehl [az datafactory pipeline create](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create) eine Pipeline namens `Adfv2QuickStartPipeline`:

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. Führen Sie die Pipeline mithilfe des Befehls [az datafactory pipeline create-run](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create_run) aus:

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   Dieser Befehl gibt eine Ausführungs-ID zurück. Kopieren Sie diese zur Verwendung im nächsten Befehl.

1. Überprüfen Sie mithilfe des Befehls [az datafactory pipeline-run show](/cli/azure/datafactory/pipeline-run#az_datafactory_pipeline_run_show), ob die Pipelineausführung erfolgreich war:

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

Sie können auch mit dem [Azure-Portal](https://portal.azure.com/) überprüfen, ob die Pipeline erwartungsgemäß ausgeführt wurde. Weitere Informationen finden Sie unter [Überprüfen der bereitgestellten Ressourcen](quickstart-create-data-factory-powershell.md#review-deployed-resources).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Alle Ressourcen in dieser Schnellstartanleitung sind Teil derselben Ressourcengruppe. Wenn Sie sie alle entfernen möchten, verwenden Sie den Befehl [az group delete](/cli/azure/group#az_group_delete):

```azurecli
az group delete --name ADFQuickStartRG
```

Wenn Sie diese Ressourcengruppe für etwas anderes verwenden, löschen Sie stattdessen einzelne Ressourcen. Verwenden Sie beispielsweise den Befehl [az datafactory linked-service delete](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_delete), um den verknüpften Dienst zu entfernen.

In dieser Schnellstartanleitung haben Sie die folgenden JSON-Dateien erstellt:

- AzureStorageLinkedService.json
- InputDataset.json
- OutputDataset.json
- Adfv2QuickStartPipeline.json

Löschen Sie sie mithilfe von Bash-Standardbefehlen.

## <a name="next-steps"></a>Nächste Schritte

- [Pipelines und Aktivitäten in Azure Data Factory](concepts-pipelines-activities.md)
- [Verknüpfte Dienste in Azure Data Factory](concepts-linked-services.md)
- [Datasets in Azure Data Factory](concepts-datasets-linked-services.md)
- [Transformieren von Data in Azure Data Factory](transform-data.md)
