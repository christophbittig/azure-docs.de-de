---
title: Übertragen von Artefakten
description: Übertragen von Sammlungen von Images oder anderen Artefakten aus einer Containerregistrierung in eine andere Registrierung durch Erstellen einer Übertragungspipeline mithilfe von Azure Storage-Konten
ms.topic: article
ms.date: 10/07/2020
ms.custom: ''
ms.openlocfilehash: fcec85f44b7538bfd741a6e6c890a4e92b5a9fb0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229463"
---
# <a name="transfer-artifacts-to-another-registry"></a>Übertragen von Artefakten in eine andere Registrierung

In diesem Artikel wird beschrieben, wie Sammlungen von Images oder anderen Registrierungsartefakten aus einer Azure Container Registry in eine andere Registrierung übertragen werden. Die Quell- und Zielregistrierungen können sich im selben Abonnement oder in unterschiedlichen Abonnements, Active Directory-Mandanten, Azure-Clouds oder physisch getrennten Clouds befinden. 

Zum Übertragen von Artefakten erstellen Sie eine *Übertragungspipeline*, von der Artefakte zwischen zwei Registrierungen mithilfe von [Blobspeicher](../storage/blobs/storage-blobs-introduction.md) repliziert werden:

* Artefakte aus einer Quellregistrierung werden in ein Blob in einem Quellspeicherkonto exportiert. 
* Das Blob wird aus dem Quellspeicherkonto in ein Zielspeicherkonto kopiert.
* Das Blob im Zielspeicherkonto wird als Artefakte in die Zielregistrierung importiert. Sie können die Importpipeline so einrichten, dass sie immer dann ausgelöst wird, wenn das Artefaktblob im Zielspeicher aktualisiert wird.

Übertragung eignet sich ideal zum Kopieren von Inhalten zwischen zwei Azure Container Registrys in physisch getrennten Clouds, wobei die Vermittlung durch Speicherkonten in jeder Cloud erfolgt. Wenn Sie stattdessen Images aus Containerregistrierungen in verbundenen Clouds wie Docker Hub und von anderen Cloudanbietern kopieren möchten, sollten Sie [Imageimport](container-registry-import-images.md) verwenden.

In diesem Artikel verwenden Sie Azure Resource Manager-Vorlagenbereitstellungen, um die Übertragungspipeline zu erstellen und auszuführen. Die Azure CLI wird verwendet, um die zugehörigen Ressourcen wie Speichergeheimnisse bereitzustellen. Azure CLI-Version 2.2.0 oder höher wird empfohlen. Informationen zum Installieren und Aktualisieren der Befehlszeilenschnittstelle finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli].

Diese Funktion ist auf der Dienstebene **Premium** der Containerregistrierung verfügbar. Weitere Informationen zu den Tarifen des Registrierungsdiensts und zu den Einschränkungen finden Sie unter [Azure Container Registry-Tarife](container-registry-skus.md).

> [!IMPORTANT]
> Diese Funktion steht derzeit als Vorschau zur Verfügung. Vorschauversionen werden Ihnen zur Verfügung gestellt, wenn Sie die [zusätzlichen Nutzungsbedingungen][terms-of-use] akzeptieren. Einige Aspekte dieses Features werden bis zur allgemeinen Verfügbarkeit unter Umständen noch geändert.

## <a name="prerequisites"></a>Voraussetzungen

* **Containerregistrierungen**: Sie benötigen eine vorhandene Quellregistrierung mit zu übertragenden Artefakten und eine Zielregistrierung. Die ACR-Übertragung ist für die Verschiebung über physisch getrennte Clouds hinweg vorgesehen. Zu Testzwecken können sich die Quell- und Zielregistrierungen in demselben oder einem anderen Azure-Abonnement, Active Directory-Mandanten bzw. in einer Cloud befinden. 

   Wenn Sie eine Registrierung erstellen müssen, finden Sie weitere Informationen unter [Schnellstart: Erstellen einer privaten Containerregistrierung mit der Azure CLI](container-registry-get-started-azure-cli.md). 
* **Speicherkonten**: Erstellen Sie Quell- und Zielspeicherkonten in einem Abonnement und an einem Speicherort Ihrer Wahl. Zu Testzwecken können Sie dasselbe Abonnement oder dieselben Abonnements wie Ihre Quell- und Zielregistrierungen verwenden. Für Cloudszenarien erstellen Sie in der Regel in jeder Cloud ein separates Speicherkonto. 

  Erstellen Sie bei Bedarf die Speicherkonten mit der [Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli) oder anderen Tools. 

  Erstellen Sie einen Blobcontainer für die Artefaktübertragung in jedem Konto.Erstellen Sie einen Blobcontainer für die Artefaktübertragung in jedem Konto. Erstellen Sie z. B. einen Container namens *transfer*. Mindestens zwei Übertragungspipelines können dasselbe Speicherkonto gemeinsam nutzen, sollten jedoch unterschiedliche Speichercontainerbereiche verwenden.
* **Schlüsseltresore**: Schlüsseltresore sind erforderlich, um SAS-Tokengeheimnisse für den Zugriff auf Quell- und Zielspeicherkonten zu speichern. Erstellen Sie die Quell- und Zielschlüsseltresore in demselben Azure-Abonnement oder in den gleichen Abonnements wie Ihre Quell- und Zielregistrierungen. Bei den in diesem Artikel zu Demonstrationszwecken verwendeten Vorlagen und Befehlen wird vorausgesetzt, dass sich die Quell- und Zielschlüsseltresore in denselben Ressourcengruppen wie die Quell- und Zielregistrierungen befinden. Diese Verwendung von gemeinsamen Ressourcengruppen ist nicht erforderlich. Dadurch werden jedoch die in diesem Artikel verwendeten Vorlagen und Befehle vereinfacht.

   Erstellen Sie Schlüsseltresore bei Bedarf mit der [Azure CLI](../key-vault/secrets/quick-create-cli.md) oder anderen Tools.

* **Umgebungsvariablen**: Legen Sie für die Beispielbefehle in diesem Artikel die folgenden Umgebungsvariablen für die Quell- und Zielumgebungen fest. Alle Beispiele sind für die Bash-Shell formatiert.
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>Übersicht über das Szenario

Sie erstellen die folgenden drei Pipelineressourcen für die Imageübertragung zwischen Registrierungen. Alle Ressourcen werden mithilfe von PUT-Vorgängen erstellt. Diese Ressourcen werden für Ihre *Quell*- und *Zielregistrierungen* und Speicherkonten verwendet. 

Die Speicherauthentifizierung verwendet SAS-Token, die als Geheimnisse in Schlüsseltresoren verwaltet werden. Die Pipelines verwenden verwaltete Identitäten, um die Geheimnisse in den Tresoren zu lesen.

* **[ExportPipeline](#create-exportpipeline-with-resource-manager)** : Dauerhafte Ressource, die allgemeine Informationen zur *Quellregistrierung* und zum -speicherkonto enthält. Diese Informationen umfassen den URI des Quellspeicher-Blobcontainers und den Schlüsseltresor, der das SAS-Quelltoken verwaltet. 
* **[ImportPipeline](#create-importpipeline-with-resource-manager)** : Dauerhafte Ressource, die allgemeine Informationen zur *Zielregistrierung* und zum -speicherkonto enthält. Diese Informationen umfassen den URI des Zielspeicher-Blobcontainers und den Schlüsseltresor, der das SAS-Zieltoken verwaltet. Ein Importtrigger ist standardmäßig aktiviert, sodass die Pipeline automatisch ausgeführt wird, wenn ein Artefaktblob in den Zielspeichercontainer gelangt. 
* **[PipelineRun](#create-pipelinerun-for-export-with-resource-manager)** : Ressource, die zum Aufrufen einer ExportPipeline- oder ImportPipeline-Ressource verwendet wird.  
  * Sie führen die ExportPipeline manuell aus, indem Sie eine PipelineRun-Ressource erstellen und die zu exportierenden Artefakte angeben.  
  * Wenn ein Importtrigger aktiviert ist, wird die ImportPipeline automatisch ausgeführt. Sie kann auch manuell mit einem PipelineRun-Vorgang ausgeführt werden. 
  * Derzeit können pro PipelineRun-Vorgang maximal **50 Artefakte** übertragen werden.

### <a name="things-to-know"></a>Wichtige Hinweise
* ExportPipeline und ImportPipeline befinden sich in der Regel in unterschiedlichen Active Directory-Mandanten, die mit den Quell- und Zielclouds verknüpft sind. Dieses Szenario erfordert separate verwaltete Identitäten und Schlüsseltresore für die Export- und Importressourcen. Zu Testzwecken können diese Ressourcen in derselben Cloud platziert werden und Identitäten gemeinsam nutzen.
* Mit den Vorlagen „ExportPipeline“ und „ImportPipeline“ wird für jeweils eine vom System zugewiesene verwaltete Identität der Zugriff auf Schlüsseltresorgeheimnisse aktiviert. Die Vorlagen „ExportPipeline“ und „ImportPipeline“ unterstützen darüber hinaus auch eine vom Benutzer zugewiesene Identität, die Sie bereitstellen. 

## <a name="create-and-store-sas-keys"></a>Erstellen und Speichern von SAS-Schlüsseln

Bei der Übertragung werden SAS-Token (Shared Access Signature) für den Zugriff auf die Speicherkonten in den Quell- und Zielumgebungen verwendet. Generieren und speichern Sie Token wie in den folgenden Abschnitten beschrieben.  

### <a name="generate-sas-token-for-export"></a>Generieren von SAS-Token für den Export

Führen Sie den Befehl [az storage container generate-sas][az-storage-container-generate-sas] aus, um ein SAS-Token für den Container im Quellspeicherkonto zu generieren, das für den Artefaktexport verwendet wird.

*Empfohlene Tokenberechtigungen*: Lesen, Schreiben, Auflisten, Hinzufügen. 

Im folgenden Beispiel wird die Befehlsausgabe der Umgebungsvariablen EXPORT_SAS zugewiesen, der das Zeichen „?“ vorangestellt wird. Aktualisieren Sie den `--expiry`-Wert für Ihre Umgebung:

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>Speichern von SAS-Token für den Export

Speichern Sie das SAS-Token in der Azure Key Vault-Quelle mithilfe von [az keyvault secret set][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>Generieren von SAS-Token für den Import

Führen Sie den Befehl [az storage container generate-sas][az-storage-container-generate-sas] aus, um ein SAS-Token für den Container im Zielspeicherkonto zu generieren, das für den Artefaktimport verwendet wird.

*Empfohlene Tokenberechtigungen*: Lesen, Löschen, Auflisten

Im folgenden Beispiel wird die Befehlsausgabe der Umgebungsvariablen IMPORT_SAS zugewiesen, der das Zeichen „?“ vorangestellt wird. Aktualisieren Sie den `--expiry`-Wert für Ihre Umgebung:

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>Speichern von SAS-Token für den Import

Speichern Sie das SAS-Token im Azure Key Vault-Ziel mithilfe von [az keyvault secret set][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>Erstellen von ExportPipeline mit Resource Manager

Erstellen Sie mithilfe der Azure Resource Manager-Vorlagenbereitstellung eine ExportPipeline-Ressource für Ihre Quellcontainerregistrierung.

Kopieren Sie die ExportPipeline-[Vorlagendateien](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines) von Resource Manager in einen lokalen Ordner.

Geben Sie die folgenden Parameterwerte in die Datei `azuredeploy.parameters.json` ein:

|Parameter  |Wert  |
|---------|---------|
|registryName     | Der Name der Quellcontainerregistrierung.      |
|exportPipelineName     |  Der Name, den Sie für die Exportpipeline auswählen.       |
|targetUri     |  Der URI des Speichercontainers in der Quellumgebung (das Ziel der Exportpipeline).<br/>Beispiel: `https://sourcestorage.blob.core.windows.net/transfer`       |
|keyVaultName     |  Der Name des Quellschlüsseltresors.  |
|sasTokenSecretName  | Der Name des SAS-Tokengeheimnisses im Quellschlüsseltresor. <br/>Beispiel: acrexportsas

### <a name="export-options"></a>Exportoptionen

Die `options`-Eigenschaft für die Exportpipelines unterstützt optionale boolesche Werte. Die folgenden Werte werden empfohlen:

|Parameter  |Wert  |
|---------|---------|
|Optionen | OverwriteBlobs: Überschreiben vorhandener Zielblobs<br/>ContinueOnErrors: Fortsetzen des Exports verbleibender Artefakte in der Quellregistrierung, wenn ein Artefaktexport fehlschlägt.

### <a name="create-the-resource"></a>Erstellen der Ressource

Führen Sie [az deployment group create][az-deployment-group-create] aus, um eine Ressource mit dem Namen *exportPipeline* wie in den folgenden Beispielen zu erstellen. Mit der ersten Option wird von der Beispielvorlage standardmäßig eine vom System zugewiesene Identität in der Ressource „ExportPipeline“ aktiviert. 

Mit der zweiten Option können Sie die Ressource mit einer vom Benutzer zugewiesenen Identität bereitstellen. (Die Erstellung der vom Benutzer zugewiesenen Identität ist nicht dargestellt.)

Mit beiden Optionen konfiguriert die Vorlage die Identität für den Zugriff auf das SAS-Token im Exportschlüsseltresor. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Option 1: Erstellen von Ressourcen und Aktivieren systemseitig zugewiesener Identitäten

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Option 2: Erstellen von Ressourcen und Bereitstellen benutzerseitig zugewiesener Identitäten

Geben Sie die Ressourcen-ID der vom Benutzer zugewiesenen Identität in diesem Befehl als zusätzlichen Parameter an.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Notieren Sie sich in der Befehlsausgabe die Ressourcen-ID (`id`) der Pipeline. Sie können diesen Wert zur späteren Verwendung in einer Umgebungsvariablen speichern, indem Sie [az deployment group show][az-deployment-group-show] ausführen. Beispiel:

```azurecli
EXPORT_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>Erstellen Sie mithilfe der Azure Resource Manager-Vorlagenbereitstellung eine ImportPipeline-Ressource in der Zielcontainerregistrierung. 

Erstellen Sie mithilfe der Azure Resource Manager-Vorlagenbereitstellung eine ImportPipeline-Ressource in der Zielcontainerregistrierung. Standardmäßig ist die Pipeline für automatischen Import aktiviert, wenn das Speicherkonto in der Zielumgebung über ein Artefaktblob verfügt.

Kopieren Sie die ImportPipeline-[Vorlagendateien](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines) von Resource Manager in einen lokalen Ordner.

Geben Sie die folgenden Parameterwerte in die Datei `azuredeploy.parameters.json` ein:

Parameter  |Wert  |
|---------|---------|
|registryName     | Der Name der Zielcontainerregistrierung.      |
|importPipelineName     |  Der Name, den Sie für die Importpipeline auswählen.       |
|sourceUri     |  Der URI des Speichercontainers in der Zielumgebung (die Quelle der Importpipeline).<br/>Beispiel: `https://targetstorage.blob.core.windows.net/transfer`|
|keyVaultName     |  Der Name des Zielschlüsseltresors. |
|sasTokenSecretName     |  Der Name des SAS-Tokengeheimnisses im Zielschlüsseltresor.<br/>Beispiel: acr importsas |

### <a name="import-options"></a>Importoptionen

Die `options`-Eigenschaft für die Importpipeline unterstützt optionale boolesche Werte. Die folgenden Werte werden empfohlen:

|Parameter  |Wert  |
|---------|---------|
|Optionen | OverwriteTags: Überschreiben vorhandener Zieltags<br/>DeleteSourceBlobOnSuccess: Löscht das Quellspeicherblob nach dem erfolgreichen Import in die Zielregistrierung.<br/>ContinueOnErrors: Fortsetzen des Imports verbleibender Artefakte in der Zielregistrierung, wenn ein Artefaktimport fehlschlägt.

### <a name="create-the-resource"></a>Erstellen der Ressource

Führen Sie [az deployment group create][az-deployment-group-create] aus, um eine Ressource mit dem Namen *importPipeline* wie in den folgenden Beispielen zu erstellen. Mit der ersten Option wird von der Beispielvorlage standardmäßig eine vom System zugewiesene Identität in der Ressource „ImportPipeline“ aktiviert. 

Mit der zweiten Option können Sie die Ressource mit einer vom Benutzer zugewiesenen Identität bereitstellen. (Die Erstellung der vom Benutzer zugewiesenen Identität ist nicht dargestellt.)

Mit beiden Optionen konfiguriert die Vorlage die Identität für den Zugriff auf das SAS-Token im Importschlüsseltresor. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Option 1: Erstellen von Ressourcen und Aktivieren systemseitig zugewiesener Identitäten

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json 
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Option 2: Erstellen von Ressourcen und Bereitstellen benutzerseitig zugewiesener Identitäten

Geben Sie die Ressourcen-ID der vom Benutzer zugewiesenen Identität in diesem Befehl als zusätzlichen Parameter an.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Wenn Sie beabsichtigen, den Import manuell auszuführen, notieren Sie sich die Ressourcen-ID (`id`) der Pipeline. Sie können diesen Wert zur späteren Verwendung in einer Umgebungsvariablen speichern, indem Sie den Befehl [az deployment group show][az-deployment-group-show] ausführen. Beispiel:

```azurecli
IMPORT_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>Erstellen von PipelineRun für den Export mit Resource Manager 

Erstellen Sie mithilfe der Azure Resource Manager-Vorlagenbereitstellung eine PipelineRun-Ressource für Ihre Quellcontainerregistrierung. Diese Ressource führt die ExportPipeline-Ressource aus, die Sie zuvor erstellt haben, und exportiert angegebene Artefakte als Blob aus Ihrer Containerregistrierung in Ihr Quellspeicherkonto.

Kopieren Sie die PipelineRun-[Vorlagendateien](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export) von Resource Manager in einen lokalen Ordner.

Geben Sie die folgenden Parameterwerte in die Datei `azuredeploy.parameters.json` ein:

|Parameter  |Wert  |
|---------|---------|
|registryName     | Der Name der Quellcontainerregistrierung.      |
|pipelineRunName     |  Der Name, den Sie für den Testlauf auswählen.       |
|pipelineResourceId     |  Die Ressourcen-ID der Exportpipeline.<br/>Beispiel: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|targetName     |  Der Name, den Sie für das in Ihr Quellspeicherkonto exportierte Artefaktblob auswählen, z. B. *myblob*.
|artifacts | Das Array der zu übertragenden Quellartefakte als Tags oder als Manifesthashes<br/>Beispiel: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

Wenn Sie eine PipelineRun-Ressource mit identischen Eigenschaften erneut bereitstellen, müssen Sie auch die Eigenschaft [forceUpdateTag](#redeploy-pipelinerun-resource) verwenden.

Führen Sie [az deployment group create][az-deployment-group-create] aus, um die PipelineRun-Ressource zu erstellen. Im folgenden Beispiel wird die Bereitstellung *exportPipelineRun* genannt.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

Speichern Sie die Ressourcen-ID der Pipelineausführung zur späteren Verwendung in einer Umgebungsvariablen:

```azurecli
EXPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Es kann einige Minuten dauern, bis Artefakte exportiert werden. Wenn die Bereitstellung erfolgreich abgeschlossen wurde, überprüfen Sie den Artefaktexport, indem Sie das exportierte Blob im Container *transfer* des Quellspeicherkontos auflisten. Führen Sie z. B. den Befehl [az storage blob list][az-storage-blob-list] Befehl aus:

```azurecli
az storage blob list \
  --account-name $SOURCE_SA \
  --container transfer \
  --output table
```

## <a name="transfer-blob-optional"></a>Übertragen des Blobs (optional) 

Verwenden Sie das AzCopy-Tool oder andere Methoden, um Blobdaten aus dem Quellspeicherkonto in das Zielspeicherkonto zu [übertragen](../storage/common/storage-use-azcopy-v10.md#transfer-data).

Beispielsweise kopiert der folgende [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md)-Befehl „myblob“ aus dem Container *transfer* des Quellkontos in den Container *transfer* im Zielkonto. Wenn das Blob im Zielkonto vorhanden ist, wird es überschrieben. Bei der Authentifizierung werden SAS-Token mit den entsprechenden Berechtigungen für die Quell- und Zielcontainer verwendet. (Die Schritte zum Erstellen von Token werden nicht gezeigt.)

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>Auslösen der ImportPipeline-Ressource

Wenn Sie den `sourceTriggerStatus`-Parameter von ImportPipeline (den Standardwert) aktiviert haben, wird die Pipeline ausgelöst, nachdem das Blob in das Zielspeicherkonto kopiert wurde. Es kann einige Minuten dauern, bis Artefakte importiert werden. Wenn der Import erfolgreich abgeschlossen wurde, überprüfen Sie den Artefaktimport, indem Sie die Repositorys in der Zielcontainerregistrierung auflisten. Führen Sie z. B. [az acr repository list][az-acr-repository-list] aus:

```azurecli
az acr repository list --name <target-registry-name>
```

Wenn Sie den `sourceTriggerStatus`-Parameter der Importpipeline nicht aktiviert haben, führen Sie die ImportPipeline-Ressource manuell aus, wie im folgenden Abschnitt gezeigt. 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>Erstellen von PipelineRun für den Import mit Resource Manager (optional) 
 
Sie können eine PipelineRun-Ressource auch verwenden, um eine ImportPipeline für den Artefaktimport in der Zielcontainerregistrierung auszulösen.

Kopieren Sie die PipelineRun-[Vorlagendateien](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import) von Resource Manager in einen lokalen Ordner.

Geben Sie die folgenden Parameterwerte in die Datei `azuredeploy.parameters.json` ein:

|Parameter  |Wert  |
|---------|---------|
|registryName     | Der Name der Zielcontainerregistrierung.      |
|pipelineRunName     |  Der Name, den Sie für den Testlauf auswählen.       |
|pipelineResourceId     |  Die Ressourcen-ID der Importpipeline.<br/>Beispiel: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|sourceName     |  Der Name des vorhandenen Blobs für exportierte Artefakte in Ihrem Speicherkonto, z. B. *myblob*

Wenn Sie eine PipelineRun-Ressource mit identischen Eigenschaften erneut bereitstellen, müssen Sie auch die Eigenschaft [forceUpdateTag](#redeploy-pipelinerun-resource) verwenden.

Führen Sie [az deployment group create][az-deployment-group-create] aus, um die Ressource auszuführen.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

Speichern Sie die Ressourcen-ID der Pipelineausführung zur späteren Verwendung in einer Umgebungsvariablen:

```azurecli
IMPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Wenn die Bereitstellung erfolgreich abgeschlossen wurde, überprüfen Sie den Artefaktimport, indem Sie die Repositorys in der Zielcontainerregistrierung auflisten. Führen Sie z. B. [az acr repository list][az-acr-repository-list] aus:

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="redeploy-pipelinerun-resource"></a>Erneutes Bereitstellen der PipelineRun-Ressource

Wenn Sie eine PipelineRun-Ressource mit *identischen Eigenschaften* erneut bereitstellen, müssen Sie die Eigenschaft **forceUpdateTag** nutzen. Diese Eigenschaft gibt an, dass die PipelineRun-Ressource neu erstellt werden soll, auch wenn sich die Konfiguration nicht geändert hat. forceUpdateTag muss bei jeder erneuten Bereitstellung der PipelineRun-Ressource eindeutig sein. Mit dem folgenden Beispiel wird eine PipelineRun-Ressource für den Export erstellt. Der aktuelle datetime-Wert wird zum Festlegen von forceUpdateTag verwendet. Dadurch wird sichergestellt, dass diese Eigenschaft immer eindeutig ist.

```console
CURRENT_DATETIME=`date +"%Y-%m-%d:%T"`
```

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json \
  --parameters forceUpdateTag=$CURRENT_DATETIME
```

## <a name="delete-pipeline-resources"></a>Löschen von Pipelineressourcen

In den folgenden Beispielbefehlen wird [az resource delete][az-resource-delete] verwendet, um die in diesem Artikel erstellten Pipelineressourcen zu löschen. Früher wurden die Ressourcen-IDs in Umgebungsvariablen gespeichert.

```
# Delete export resources
az resource delete \
--resource-group $SOURCE_RG \
--ids $EXPORT_RES_ID $EXPORT_RUN_RES_ID \
--api-version 2019-12-01-preview

# Delete import resources
az resource delete \
--resource-group $TARGET_RG \
--ids $IMPORT_RES_ID $IMPORT_RUN_RES_ID \
--api-version 2019-12-01-preview
```

## <a name="troubleshooting"></a>Problembehandlung

* **Vorlagenbereitstellungsprobleme oder -fehler**
  * Wenn eine Pipelineausführung fehlschlägt, überprüfen Sie die `pipelineRunErrorMessage`-Eigenschaft der Ausführungsressource.
  * Informationen zu häufigen Vorlagenbereitstellungsfehlern finden Sie unter [Beheben von Problemen mit ARM-Vorlagenbereitstellungen](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
* **Probleme beim Zugriff auf den Speicher**<a name="problems-accessing-storage"></a>
  * Wenn für den Speicher ein Fehler vom Typ `403 Forbidden` angezeigt wird, liegt wahrscheinlich ein Problem mit Ihrem SAS-Token vor.
  * Das SAS-Token ist möglicherweise nicht gültig. Vielleicht ist das SAS-Token abgelaufen, oder die Speicherkontoschlüssel haben sich seit der Erstellung des SAS-Tokens geändert. Überprüfen Sie, ob das SAS-Token gültig ist, indem Sie versuchen, das SAS-Token für die Authentifizierung zum Zugriff auf den Speicherkontocontainer zu verwenden. Geben Sie beispielsweise einen vorhandenen Blobendpunkt gefolgt vom SAS-Token in die Adressleiste eines neuen InPrivate-Fensters in Microsoft Edge ein, oder laden Sie mit dem SAS-Token unter Verwendung von `az storage blob upload` ein Blob in den Container hoch.
  * Das SAS-Token umfasst möglicherweise nicht genügend zulässige Ressourcentypen. Vergewissern Sie sich, dass dem SAS-Token unter „Zugelassene Ressourcentypen“ (`srt=sco` im SAS-Token) Berechtigungen für Dienst-, Container- und Objektressourcen gewährt wurden.
  * Das SAS-Token umfasst möglicherweise keine ausreichenden Berechtigungen. Für Exportpipelines werden die SAS-Tokenberechtigungen „Lesen“, „Schreiben“, „Auflisten“ und „Hinzufügen“ benötigt. Für Importpipelines werden die SAS-Tokenberechtigungen „Lesen“, „Löschen“ und „Auflisten“ benötigt. (Die Berechtigung „Löschen“ ist nur erforderlich, wenn für die Importpipeline die Option `DeleteSourceBlobOnSuccess` aktiviert wurde.)
  * Das SAS-Token ist möglicherweise nicht so konfiguriert, dass es nur mit HTTPS funktioniert. Stellen Sie sicher, dass das SAS-Token so konfiguriert ist, dass es nur mit HTTPS funktioniert (`spr=https` im SAS-Token).
* **Probleme beim Exportieren oder Importieren von Speicherblobs**
  * Das SAS-Token ist möglicherweise abgelaufen oder umfasst keine ausreichenden Berechtigungen für die angegebene Export- oder Importausführung. Siehe [Probleme beim Zugriff auf den Speicher](#problems-accessing-storage).
  * Ein vorhandenes Speicherblob im Quellspeicherkonto wird bei mehreren Exportausführungen möglicherweise nicht überschrieben. Vergewissern Sie sich, dass die Option OverwriteBlob in der Exportausführung festgelegt ist und das SAS-Token über ausreichende Berechtigungen verfügt.
  * Das Speicherblob in Zielspeicherkonto wird nach einer erfolgreichen Importausführung möglicherweise nicht gelöscht. Vergewissern Sie sich, dass die Option DeleteBlobOnSuccess in der Exportausführung festgelegt ist und das SAS-Token über ausreichende Berechtigungen verfügt.
  * Das Speicherblob wurde nicht erstellt oder gelöscht. Vergewissern Sie sich, dass der in der Export- oder Importausführung angegebene Container oder das angegebene Speicherblob für die manuelle Importausführung vorhanden ist. 
* **AzCopy-Probleme**
  * Weitere Informationen finden Sie unter [Beheben von Problemen mit AzCopy](../storage/common/storage-use-azcopy-configure.md).  
* **Artefaktübertragungsprobleme**
  * Nicht alle Artefakte (oder gar keine) werden übertragen. Überprüfen Sie die Schreibweise von Artefakten in der Exportausführung sowie den Namen des Blobs in Export- und Importausführungen. Vergewissern Sie sich, dass Sie maximal 50 Artefakte übertragen.
  * Die Pipelineausführung wurde möglicherweise nicht abgeschlossen. Die Export- oder Importausführung kann einige Zeit in Anspruch nehmen. 
  * Stellen Sie bei anderen Pipelineproblemen dem Azure Container Registry-Team die [Korrelations-ID](../azure-resource-manager/templates/deployment-history.md) der Bereitstellung der Export- oder Importausführung zur Verfügung.
* **Probleme beim Abrufen des Images in einer physisch isolierten Umgebung**
  * Wenn bei dem Versuch, ein Image in einer physisch isolierten Umgebung abzurufen, Fehler im Zusammenhang mit fremden Ebenen oder der Auflösung von mcr.microsoft.com auftreten, weist das Imagemanifest wahrscheinlich nicht verteilbare Ebenen auf. Diese Images können in einer physisch isolierten Umgebung naturgemäß häufig nicht abgerufen werden. Sie können bestätigen, ob dies der Fall ist, indem Sie das Imagemanifest auf Verweise auf externe Registrierungen überprüfen. Wenn dies der Fall ist, müssen Sie die nicht verteilbaren Ebenen vor dem Bereitstellen einer Exportpipelineausführung für das Image in die Public Cloud-ACR überführen. Anleitungen hierzu finden Sie unter [Wie kann ich nicht verteilbare Ebenen an eine Registrierung pushen?](./container-registry-faq.yml#how-do-i-push-non-distributable-layers-to-a-registry-)

## <a name="next-steps"></a>Nächste Schritte

* Wenn Sie einzelne Containerimages aus einer öffentlichen Registrierung oder einer anderen privaten Registrierung in eine Azure Container Registry importieren möchten, finden Sie weitere Informationen in der Befehlsreferenz zu [az acr import][az-acr-import].
* Erfahren Sie, wie [Sie die Erstellung von Exportpipelines aus](data-loss-prevention.md) einer netzwerkbeschränkten Containerregistrierung blockieren.

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/



<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-login]: /cli/azure/reference-index#az_login
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az_keyvault_secret_set
[az-keyvault-secret-show]: /cli/azure/keyvault/secret#az_keyvault_secret_show
[az-keyvault-set-policy]: /cli/azure/keyvault#az_keyvault_set_policy
[az-storage-container-generate-sas]: /cli/azure/storage/container#az_storage_container_generate_sas
[az-storage-blob-list]: /cli/azure/storage/blob#az_storage-blob-list
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-deployment-group-delete]: /cli/azure/deployment/group#az_deployment_group_delete
[az-deployment-group-show]: /cli/azure/deployment/group#az_deployment_group_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-import]: /cli/azure/acr#az_acr_import
[az-resource-delete]: /cli/azure/resource#az_resource_delete
