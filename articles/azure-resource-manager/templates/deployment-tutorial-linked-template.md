---
title: 'Tutorial: Bereitstellen einer verknüpften Vorlage'
description: Anleitung zum Bereitstellen einer verknüpften Vorlage
ms.date: 02/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9b9f8f594fbc85c69a546a82f2857802382eea28
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128556469"
---
# <a name="tutorial-deploy-a-linked-template"></a>Tutorial: Bereitstellen einer verknüpften Vorlage

In den [vorherigen Tutorials](./deployment-tutorial-local-template.md) haben Sie erfahren, wie Sie eine Vorlage bereitstellen, die auf dem lokalen Computer gespeichert ist. Zum Bereitstellen komplexer Lösungen können Sie eine Vorlage in mehrere Vorlagen unterteilen und diese über eine Hauptvorlage gemeinsam bereitstellen. In diesem Tutorial erfahren Sie, wie Sie eine Hauptvorlage bereitstellen, die den Verweis auf eine verknüpfte Vorlage enthält. Beim Bereitstellen der Hauptvorlage wird die Bereitstellung der verknüpften Vorlage ausgelöst. Außerdem erfahren Sie, wie Sie die Vorlagen mithilfe eines SAS-Tokens speichern und sichern. Dieser Schritt dauert ungefähr **12 Minuten**.

## <a name="prerequisites"></a>Voraussetzungen

Wir empfehlen, das vorherige Tutorial durchzuführen. Dies ist jedoch keine Voraussetzung.

## <a name="review-template"></a>Überprüfen der Vorlage

In den vorherigen Tutorials haben Sie eine Vorlage bereitgestellt, mit der ein Speicherkonto, ein App Service-Plan und eine Web-App erstellt werden. Dazu wurde folgende Vorlage verwendet:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Erstellen einer verknüpften Vorlage

Sie können die Speicherkontoressource in einer verknüpften Vorlage aufteilen:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

Die folgende Vorlage ist die Hauptvorlage. Das hervorgehobene Objekt `Microsoft.Resources/deployments` zeigt, wie eine verknüpfte Vorlage aufgerufen wird. Die verknüpfte Vorlage kann nicht als lokale Datei oder Datei gespeichert werden, die nur in Ihrem lokalen Netzwerk verfügbar ist. Sie können entweder einen URI-Wert der verknüpften Vorlage angeben, der HTTP oder HTTPS enthält, oder die Eigenschaft _relativePath_ verwenden, um eine remote verknüpfte Vorlage an einem Speicherort relativ zur übergeordneten Vorlage bereitzustellen. Eine Möglichkeit besteht darin, die Hauptvorlage und die verknüpfte Vorlage in einem Speicherkonto zu platzieren.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="34-52":::

## <a name="store-the-linked-template"></a>Speichern der verknüpften Vorlage

Sowohl die Hauptvorlage als auch die verknüpfte Vorlage werden in GitHub gespeichert:

Mit dem folgenden PowerShell-Skript werden ein Speicherkonto und ein Container erstellt und die beiden verknüpften Vorlagen aus einem GitHub-Repository in den Container kopiert. Dies sind die beiden Vorlagen:

- Die Hauptvorlage: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
- Die verknüpfte Vorlage: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Wählen Sie **Try-it** aus, um die Cloud Shell zu öffnen, wählen Sie **Kopieren** aus, um das PowerShell-Skript zu kopieren, und klicken Sie mit der rechten Maustaste auf den Shellbereich, um das Skript einzufügen:

> [!IMPORTANT]
> Speicherkontonamen müssen eindeutig und zwischen 3 und 24 Zeichen lang sein, und sie dürfen nur **Zahlen** und **Kleinbuchstaben** enthalten. Die Variable `storageAccountName` der Beispielvorlage kombiniert das Maximum des Parameters `projectName` von 11 Zeichen mit einem [uniqueString](./template-functions-string.md#uniquestring)-Wert von 13 Zeichen.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$mainTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json"
$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json"

$mainFileName = "azuredeploy.json" # A file name used for downloading and uploading the main template.Add-PSSnapin
$linkedFileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the templates
Invoke-WebRequest -Uri $mainTemplateURL -OutFile "$home/$mainFileName"
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$linkedFileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the templates
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$mainFileName" `
    -Blob $mainFileName `
    -Context $context

Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$linkedFileName" `
    -Blob $linkedFileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Bereitstellen der Vorlage

Generieren Sie zum Bereitstellen von Vorlagen in einem Speicherkonto ein SAS-Token, und geben Sie es im Parameter _-QueryString_ an. Legen Sie die Ablaufzeit so fest, dass ausreichend Zeit für die Bereitstellung bleibt. Auf die Blobs, die die Vorlagen enthalten, hat nur der Kontobesitzer Zugriff. Wenn Sie jedoch ein SAS-Token für ein Blob erstellen, kann jeder Benutzer mit einem SAS-Token auf das Blob zugreifen. Wenn ein anderer Benutzer den URI und das SAS-Token abfängt, hat dieser Benutzer Zugriff auf die Vorlage. Ein SAS-Token ist eine gute Möglichkeit zum Einschränken des Zugriffs auf Ihre Vorlagen. Sie sollten allerdings Kennwörter auf keinen Fall direkt in die Vorlage einschließen.

Falls Sie die Ressourcengruppe noch nicht erstellt haben, folgen Sie den Anweisungen unter [Erstellen einer Ressourcengruppe](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> Im folgenden Azure CLI-Code ist der `date`-Parameter `-d` ein ungültiges Argument in macOS. Daher müssen macOS-Benutzer `-v+2H` verwenden, um im Terminal unter macOS zwei Stunden zur aktuellen Zeit zu addieren.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive

$projectName = Read-Host -Prompt "Enter the same project name:"   # This name is used to generate names for Azure resources, such as storage account name.

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

$mainTemplateUri = $context.BlobEndPoint + "$containerName/azuredeploy.json"
$sasToken = New-AzStorageContainerSASToken `
    -Context $context `
    -Container $containerName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0)
$newSas = $sasToken.substring(1)


New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri $mainTemplateUri `
  -QueryString $newSas `
  -projectName $projectName `
  -verbose

Write-Host "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
echo "Enter a project name that is used to generate resource names:" &&
read projectName &&

resourceGroupName="${projectName}rg" &&
storageAccountName="${projectName}store" &&
containerName="templates" &&

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv) &&

sasToken=$(az storage container generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --name $containerName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'`) &&
sasToken=$(echo $sasToken | sed 's/"//g')&&

blobUri=$(az storage account show -n $storageAccountName -g $resourceGroupName -o tsv --query primaryEndpoints.blob) &&
templateUri="${blobUri}${containerName}/azuredeploy.json" &&

az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-uri $templateUri \
  --parameters projectName=$projectName \
  --query-string $sasToken \
  --verbose
```

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
2. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
3. Klicken Sie auf den Namen der Ressourcengruppe.
4. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, eine verknüpfte Vorlage bereitzustellen. Im nächsten Tutorial erfahren Sie, wie Sie eine DevOps-Pipeline zum Bereitstellen einer Vorlage erstellen.

> [!div class="nextstepaction"]
> [Erstellen einer Pipeline](./deployment-tutorial-pipeline.md)
