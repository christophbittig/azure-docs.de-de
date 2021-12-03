---
title: Trainieren von Modellen per REST (Vorschau)
titleSuffix: Azure Machine Learning
description: Es wird beschrieben, wie Sie mit REST-APIs Modelle trainieren und Aufträge erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: wenxwei
ms.author: wenxwei
ms.date: 10/21/2021
ms.reviewer: peterlu
ms.custom: devplatv2
ms.openlocfilehash: f411d33baea7863b9139069f1eefd1282cf7b730
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562084"
---
# <a name="train-models-with-rest-preview"></a>Trainieren von Modellen per REST (Vorschau)

In diesem Artikel wird beschrieben, wie Sie die Azure Machine Learning-REST-API zum Erstellen und Verwalten von Trainingsaufträgen (Vorschau) verwenden.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

Von der REST-API werden HTTP-Standardverben genutzt, um Ressourcen zu erstellen, abzurufen, zu aktualisieren und zu löschen. Die REST-API funktioniert mit jeder Sprache oder jedem Tool, die bzw. das HTTP-Anforderungen ausführen kann. Durch die unkomplizierte Struktur von REST sind diese APIs eine gute Wahl in Skriptumgebungen und für die MLOps-Automatisierung.

In diesem Artikel wird beschrieben, wie Sie die neuen REST-APIs für folgende Zwecke verwenden:

> [!div class="checklist"]
> * Erstellen von Machine Learning-Ressourcen
> * Erstellen eines einfachen Trainingsauftrags 
> * Erstellen eines Sweep-Auftrags für die Hyperparameteroptimierung

## <a name="prerequisites"></a>Voraussetzungen

- Ein **Azure-Abonnement**, für das Sie über Administratorrechte verfügen. Testen Sie das [kostenlose oder kostenpflichtige persönliche Abonnement](https://azure.microsoft.com/free/), falls Sie nicht über ein Abonnement dieser Art verfügen.
- Ein [Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md).
- Ein Dienstprinzipal in Ihrem Arbeitsbereich. Für administrative REST-Anforderungen wird die [Dienstprinzipalauthentifizierung](how-to-setup-authentication.md#use-service-principal-authentication) verwendet.
- Ein Token für die Dienstprinzipalauthentifizierung. Führen Sie die Schritte unter [Abrufen eines Tokens für die Dienstprinzipalauthentifizierung](./how-to-manage-rest.md#retrieve-a-service-principal-authentication-token) aus, um dieses Token abzurufen. 
- Das **curl**-Hilfsprogramm. Das **curl**-Programm ist im [Windows-Subsystem für Linux](/windows/wsl/install-win10) oder jeder beliebigen UNIX-Distribution verfügbar. In PowerShell ist **curl** ein Alias für **Invoke-WebRequest**, und `curl -d "key=val" -X POST uri` wird zu `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`. 

## <a name="azure-machine-learning-jobs"></a>Azure Machine Learning-Aufträge
Ein Auftrag ist eine Ressource, mit der alle Aspekte eines Berechnungsauftrags angegeben werden. Hiermit werden drei Fragen beantwortet:

- Was soll ausgeführt werden?
- Wie soll die Ausführung erfolgen?
- Wo soll die Ausführung erfolgen?

Es gibt viele Möglichkeiten, einen Azure Machine Learning-Auftrag zu übermitteln, z. B. per SDK, Azure CLI oder über die grafische Studio-Benutzeroberfläche. Im folgenden Beispiel wird ein LightGBM-Trainingsauftrag mit der REST-API übermittelt.

## <a name="create-machine-learning-assets"></a>Erstellen von Machine Learning-Ressourcen

Richten Sie zunächst Ihre Azure Machine Learning-Ressourcen ein, um Ihren Auftrag zu konfigurieren.

In den folgenden REST-API-Aufrufen verwenden wir `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP`, `$LOCATION` und `$WORKSPACE` als Platzhalter. Ersetzen Sie die Platzhalter durch Ihre eigenen Werte. 

Für administrative REST-Anforderungen wird ein [Token für die Dienstprinzipalauthentifizierung](how-to-manage-rest.md#retrieve-a-service-principal-authentication-token) angefordert. Ersetzen Sie `$TOKEN` durch Ihren eigenen Wert. Sie können dieses Token mit dem folgenden Befehl abrufen:

```bash
TOKEN=$(az account get-access-token --query accessToken -o tsv)
```

Der Dienstanbieter verwendet das `api-version`-Argument, um Kompatibilität zu gewährleisten. Das `api-version`-Argument variiert von Dienst zu Dienst. Die aktuelle Version der Azure Machine Learning-API lautet `2021-03-01-preview`. Legen Sie die API-Version zur Vorbereitung für zukünftige Versionen als Variable fest:

```bash
API_VERSION="2021-03-01-preview"
```

### <a name="compute"></a>Compute

Für die Ausführung von Machine Learning-Aufträgen werden Computeressourcen benötigt. Sie können die Computeressourcen Ihres Arbeitsbereichs auflisten:

```bash
curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.MachineLearningServices/workspaces/$WORKSPACE/computes?api-version=$API_VERSION" \
--header "Authorization: Bearer $TOKEN"
```

In diesem Beispiel verwenden wir einen vorhandenen Computecluster mit dem Namen `cpu-cluster`. Wir legen den Computenamen als Variable fest, um eine Kapselung zu ermöglichen:

```bash
COMPUTE_NAME="cpu-cluster"
```

> [!TIP]
> Sie können eine [benannte Computeressource per PUT-Anforderung erstellen oder außer Kraft setzen](./how-to-manage-rest.md#create-and-modify-resources-using-put-and-post-requests). 

### <a name="environment"></a>Umgebung 

Das LightGBM-Beispiel muss in einer LightGBM-Umgebung ausgeführt werden. Erstellen Sie die Umgebung mit einer PUT-Anforderung. Verwenden Sie ein Docker-Image aus Microsoft Container Registry.

Sie können das Docker-Image mit `Docker` konfigurieren und Conda-Abhängigkeiten mit `condaFile` hinzufügen: 

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_environment":::

### <a name="datastore"></a>Datenspeicher

Da der Trainingsauftrag basierend auf Daten ausgeführt werden muss, müssen Sie einen Datenspeicher angeben. In diesem Beispiel rufen Sie den Standarddatenspeicher und das Azure Storage-Konto für Ihren Arbeitsbereich ab. Fragen Sie Ihren Arbeitsbereich mit einer GET-Anforderung ab, damit eine JSON-Datei mit den Informationen zurückgegeben wird.

Sie können das Tool [jq](https://stedolan.github.io/jq/) verwenden, um das JSON-Ergebnis zu analysieren und die erforderlichen Werte zu ermitteln. Sie können diese Informationen auch über das Azure-Portal ermitteln.

```bash
response=$(curl --location --request GET "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.MachineLearningServices/workspaces/$WORKSPACE/datastores?api-version=$API_VERSION&isDefault=true" \
--header "Authorization: Bearer $TOKEN")

AZURE_STORAGE_ACCOUNT=$(echo $response | jq '.value[0].properties.contents.accountName')
AZUREML_DEFAULT_DATASTORE=$(echo $response | jq '.value[0].name')
AZUREML_DEFAULT_CONTAINER=$(echo $response | jq '.value[0].properties.contents.containerName')
AZURE_STORAGE_KEY=$(az storage account keys list --account-name $AZURE_STORAGE_ACCOUNT | jq '.[0].value')
```

### <a name="data"></a>Daten

Nachdem Sie nun über den Datenspeicher verfügen, können Sie ein Dataset erstellen. Verwenden Sie für dieses Beispiel das gängige Dataset `iris.csv`, und verweisen Sie in `path` darauf. 

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_data":::

### <a name="code"></a>Code

Nachdem Sie nun über das Dataset und den Datenspeicher verfügen, können Sie das Trainingsskript hochladen, das für den Auftrag ausgeführt wird. Verwenden Sie die Azure Storage-CLI, um ein Blob in Ihren Standardcontainer hochzuladen. Sie können auch andere Uploadmethoden verwenden, z. B. das Azure-Portal oder Azure Storage-Explorer.


```bash
az storage blob upload-batch -d $AZUREML_DEFAULT_CONTAINER/src \
 -s jobs/train/lightgbm/iris/src --account-name $AZURE_STORAGE_ACCOUNT --account-key $AZURE_STORAGE_KEY
```

Nachdem Sie Ihren Code hochgeladen haben, können Sie ihn mit einer PUT-Anforderung angeben und mit `datastoreId` auf den Datenspeicher verweisen. 

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_code":::

## <a name="submit-a-training-job"></a>Übermitteln eines Trainingsauftrags

Da Ihre Ressourcen jetzt vorhanden sind, können Sie den LightGBM-Auftrag ausführen, bei dem ein trainiertes Modell und Metadaten ausgegeben werden. Sie benötigen die folgenden Informationen, um den Trainingsauftrag zu konfigurieren: 

- **run_id**: (Optional) Der Name des Auftrags. Dieser muss auftragsübergreifend eindeutig sein. Wird weder in der YAML-Datei über das Feld `name` noch mithilfe der Befehlszeile über `--name/-n` ein Name angegeben, wird automatisch eine GUID/UUID generiert und für den Namen verwendet.
- **jobType**: Der Auftragstyp. Verwenden Sie `Command` für einen einfachen Trainingsauftrag.
- **codeId**: Der Pfad zu Ihrem Trainingsskript.
- **command**: Der auszuführende Befehl. Eingabedaten können in den Befehl geschrieben werden, und per Datenbindung kann darauf verwiesen werden. 
- **environmentId**: Der Pfad zu Ihrer Umgebung.
- **inputDataBindings**: Per Datenbindung können Sie auf Eingabedaten verweisen. Erstellen Sie eine Umgebungsvariable. Der Name der Bindung wird dem „AZURE_ML_INPUT_“-Element hinzugefügt, auf das Sie in `command` verweisen können. Zum Erstellen einer Datenbindung müssen Sie den Pfad den von Ihnen erstellten Daten als `dataId` hinzufügen. 
- **experimentName**: (Optional) Versieht den Auftrag mit einem Tag, um Ihnen das Organisieren von Aufträgen in Azure Machine Learning Studio zu erleichtern. Die Ausführungsaufzeichnung jedes Auftrags wird in Studio unter dem entsprechenden Experiment auf der Registerkarte „Experiment“ angegeben. Ohne Angabe wird für Tags standardmäßig der Name des Arbeitsverzeichnisses verwendet, in dem der Auftrag erstellt wird.
- **compute**: Mit `target` wird das Computeziel angegeben. Hierbei kann es sich um den Pfad zu Ihrer Computeressource handeln. Mit `instanceCount` wird die Anzahl von Instanzen angegeben, die Sie für den Auftrag benötigen.

Verwenden Sie die folgenden Befehle, um den Trainingsauftrag zu übermitteln:

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_job":::

## <a name="submit-a-hyperparameter-sweep-job"></a>Übermitteln eines Sweep-Auftrags für Hyperparameter

Mit Azure Machine Learning haben Sie auch die Möglichkeit, Hyperparameter für das Training effizient zu optimieren. Sie können mit den REST-APIs eine Sammlung für die Hyperparameteroptimierung erstellen. Weitere Informationen zur Hyperparameteroptimierung mit Azure Machine Learning finden Sie unter [Optimierung der Hyperparameter eines Modells](how-to-tune-hyperparameters.md). Geben Sie die Parameter für die Hyperparameteroptimierung an, um den Sweep-Auftrag zu konfigurieren:

- **jobType**: Der Auftragstyp. Bei einem Sweep-Auftrag ist dies `Sweep`. 
- **algorithm**: Ein guter Ausgangspunkt ist häufig der Samplingalgorithmus „random“. Eine Aufzählung der Optionen finden Sie im [Schema](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json) des Sweep-Auftrags. 
- **trial**: Die Befehlsauftragskonfiguration für jeden auszuführenden Test. 
- **objective**: `primaryMetric` ist die Optimierungsmetrik. Sie muss dem Namen einer über den Trainingscode protokollierten Metrik entsprechen. `goal` gibt die Richtung an. Mögliche Optionen sind „minimize“ (Minimieren) und „maximize“ (Maximieren). Eine vollständige Aufzählung der Optionen finden Sie im [Schema](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json). 
- **searchSpace**: Ein Wörterbuch mit den Hyperparametern für den Sweep-Vorgang. Der Schlüssel ist ein Name für den Hyperparameter, z. B. `learning_rate`. Der Wert ist die Hyperparameterverteilung. Eine Aufzählung der Optionen finden Sie im [Schema](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json).
- **maxTotalTrials**: Die maximale Anzahl einzelner ausführbarer Tests.
- **maxConcurrentTrials**: (Optional) Die maximale Anzahl von Tests, die gleichzeitig in Ihrem Computecluster ausgeführt werden können.
- **timeout**: (Optional) Die maximale Ausführungsdauer des Sweep-Auftrags (in Minuten).

Verwenden Sie die folgenden Befehle, um einen Sweep-Auftrag mit demselben LightGBM-Beispiel zu erstellen: 

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_a_sweep_job":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun über ein trainiertes Modell verfügen, können Sie sich über die [Bereitstellung Ihres Modells](how-to-deploy-and-where.md) informieren.
