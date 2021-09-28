---
title: Bereitstellen von Vorlagen mit Cloud Shell
description: Verwenden Sie Azure Resource Manager und Cloud Shell, um Ressourcen in Azure bereitzustellen. Die Ressourcen sind in einer Azure Resource Manager-Vorlage (ARM-Vorlage) definiert.
ms.topic: conceptual
ms.date: 09/03/2021
ms.openlocfilehash: 0228f4862cbdcda98bc577844bca9dad34feb96f
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123535106"
---
# <a name="deploy-arm-templates-from-azure-cloud-shell"></a>Bereitstellen von ARM-Vorlagen mit Azure Cloud Shell

Mit [Azure Cloud Shell](../../cloud-shell/overview.md) können Sie Azure Resource Manager-Vorlagen (ARM-Vorlagen) bereitstellen. Sie können entweder eine remote gespeicherte oder eine im lokalen Speicherkonto von Cloud Shell gespeicherte ARM-Vorlage bereitstellen.

Den Umfang der Bereitstellung legen Sie selbst fest. In diesem Artikel wird gezeigt, wie Sie eine Bereitstellung in einer Ressourcengruppe durchführen.

## <a name="deploy-remote-template"></a>Bereitstellen einer Remotevorlage

Um eine externe Vorlage bereitzustellen, geben Sie den URI der Vorlage genau wie bei jeder anderen externen Bereitstellung an. Die externe Vorlage kann sich in einem GitHub-Repository oder einem externen Speicherkonto befinden.

1. Öffnen Sie die Cloud Shell-Eingabeaufforderung.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Cloud Shell öffnen":::

1. Führen Sie die folgenden Befehle aus, um die Vorlage bereitzustellen:

   # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>Bereitstellen einer lokalen Vorlage

Zum Bereitstellen einer lokalen Vorlage müssen Sie zunächst die Vorlage in das Speicherkonto hochladen, das mit Ihrer Cloud Shell-Sitzung verbunden ist.

1. Melden Sie sich bei [Cloud Shell](https://shell.azure.com) an.

1. Wählen Sie entweder **PowerShell** oder **Bash** aus.

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-bash-powershell.png" alt-text="Bash oder PowerShell auswählen":::

1. Wählen Sie **Dateien hochladen/herunterladen** und dann **Hochladen** aus.

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-upload.png" alt-text="Hochladen einer Datei":::

1. Wählen Sie die ARM-Vorlage aus, die Sie hochladen möchten, und wählen Sie dann **Öffnen** aus.

1. Führen Sie die folgenden Befehle aus, um die Vorlage bereitzustellen:

   # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Bereitstellungsbefehlen finden Sie unter [Bereitstellen von Ressourcen mit ARM-Vorlagen und der Azure CLI](deploy-cli.md) und [Bereitstellen von Ressourcen mit ARM-Vorlagen und Azure PowerShell](deploy-powershell.md).
- Informationen zum Anzeigen einer Änderungsvorschau vor der Bereitstellung einer Vorlage finden Sie unter [Was-wäre-wenn-Vorgang bei der Bereitstellung von ARM-Vorlagen](./deploy-what-if.md).