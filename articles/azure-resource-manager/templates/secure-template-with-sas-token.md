---
title: Bereitstellen einer ARM-Vorlage mit SAS-Token – Azure Resource Manager | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Azure CLI oder Azure PowerShell verwenden, um eine private ARM-Vorlage mit einem SAS-Token sicher bereitzustellen. Schützen und verwalten Sie den Zugriff auf Ihre Vorlagen.
ms.topic: conceptual
ms.date: 09/17/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli, seo-azure-cli
keywords: Private Vorlage, SAS-Tokenvorlage, Speicherkonto, Vorlagensicherheit, Azure ARM-Vorlage, Azure Resource Manager-Vorlage
ms.openlocfilehash: 0e6a680e0344ed5a03715c35e99394aead756501
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128584940"
---
# <a name="how-to-deploy-private-arm-template-with-sas-token"></a>Bereitstellen einer privaten ARM-Vorlage mit SAS-Token

Wenn sich Ihre ARM-Vorlage (Azure Resource Manager) in einem Speicherkonto befindet, können Sie den Zugriff auf die Vorlage beschränken, um deren öffentliche Bereitstellung zu vermeiden. Sie greifen auf eine gesicherte Vorlage zu, indem Sie ein SAS-Token (Shared Access Signature) für die Vorlage erstellen und dieses Token während der Bereitstellung bereitstellen. In diesem Artikel wird erläutert, wie Sie Azure PowerShell oder Azure CLI verwenden, um eine ARM-Vorlage mit einem SAS-Token sicher bereitzustellen.

Sie finden Informationen darüber, wie Sie den Zugriff auf Ihre privaten ARM-Vorlagen schützen und verwalten können, sowie Anleitungen zu den folgenden Schritten:

* Erstellen eines Speicherkontos mit einem gesicherten Container
* Hochladen der Vorlage in das Speicherkonto
* Bereitstellen eines SAS-Tokens während der Bereitstellung

> [!IMPORTANT]
> Anstatt Ihre private Vorlage mit einem SAS-Token zu sichern, sollten Sie die Verwendung von [Vorlagenspezifikationen](template-specs.md) in Erwägung ziehen. Mit Vorlagenspezifikationen können Sie Ihre Vorlagen mit anderen Benutzern in Ihrer Organisation teilen und den Zugriff auf die Vorlagen über Azure RBAC verwalten.

## <a name="create-storage-account-with-secured-container"></a>Erstellen eines Speicherkontos mit einem gesicherten Container

Das folgende Skript erstellt ein Speicherkonto und einen Container mit deaktiviertem öffentlichem Zugriff für die Vorlagensicherheit.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-private-template-to-storage-account"></a>Hochladen einer privaten Vorlage zum Speicherkonto

Jetzt können Sie Ihre Vorlage in das Speicherkonto hochladen. Geben Sie den Pfad zu der Vorlage an, die Sie verwenden möchten.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>Bereitstellen eines SAS-Tokens während der Bereitstellung

Generieren Sie zum Bereitstellen einer privaten Vorlage in einem Speicherkonto ein SAS-Token, und fügen Sie es dem URI für die Vorlage hinzu. Legen Sie die Ablaufzeit so fest, dass ausreichend Zeit für die Bereitstellung bleibt.

> [!IMPORTANT]
> Auf den Blob, der die private Vorlage enthält, hat nur der Kontobesitzer Zugriff. Wenn Sie jedoch ein SAS-Token für das Blob erstellen, können andere Benutzer über diesen URI auf das Blob zugreifen. Wenn ein anderer Benutzer den URI abfängt, hat dieser Benutzer Zugriff auf die Vorlage. Ein SAS-Token ist eine gute Möglichkeit zum Einschränken des Zugriffs auf Ihre Vorlagen. Sie sollten allerdings Kennwörter auf keinen Fall direkt in die Vorlage einschließen.
>

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Das folgende Beispiel arbeitet mit der Bash-Umgebung in Cloud Shell. In anderen Umgebungen ist möglicherweise eine andere Syntax erforderlich, um die Ablaufzeit für das SAS-Token zu erstellen.

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

Ein Beispiel der Verwendung eines SAS-Tokens mit verknüpften Vorlagen finden Sie unter [Verwenden von verknüpften Vorlagen mit Azure Resource Manager](linked-templates.md).


## <a name="next-steps"></a>Nächste Schritte
* Eine Einführung in die Bereitstellung von Vorlagen finden Sie unter [Bereitstellen von Ressourcen mit ARM-Vorlagen und Azure PowerShell](deploy-powershell.md).
* Informationen zum Definieren von Parametern in der Vorlage finden Sie unter [Erstellen von Vorlagen](./syntax.md#parameters).