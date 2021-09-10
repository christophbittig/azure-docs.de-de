---
title: Bereitstellen einer Azure Resource Manager-Vorlage in einem Azure Automation-PowerShell-Runbook
description: In diesem Artikel wird das Bereitstellen einer in Azure Storage gespeicherten Azure Resource Manager-Vorlage aus einem PowerShell-Runbook beschrieben.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
keywords: PowerShell, Runbook, JSON, Azure Automation
ms.openlocfilehash: 7ea0e8209d244ad2b95e201ec7eb1e84f2038887
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113587767"
---
# <a name="deploy-an-azure-resource-manager-template-in-a-powershell-runbook"></a>Bereitstellen einer Azure Resource Manager-Vorlage in einem PowerShell-Runbook

Sie können ein [Azure Automation-PowerShell-Runbook](./learn/automation-tutorial-runbook-textual-powershell.md), das eine Azure-Ressource bereitstellt, mithilfe einer [Azure Resource Manager-Vorlage](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) schreiben. Die Vorlagen ermöglichen Ihnen das Automatisieren der Bereitstellung Ihrer Azure-Ressourcen mithilfe von Azure Automation. Sie können Ihre Resource Manager-Vorlagen an einem zentralen, sicheren Ort verwalten, z. B. in Azure Storage.

In diesem Artikel erstellen wir ein PowerShell-Runbook, für das eine in [Azure Storage](../storage/common/storage-introduction.md) gespeicherte Resource Manager-Vorlage verwendet wird, um ein neues Azure Storage-Konto bereitzustellen.

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich für ein [kostenloses Konto registrieren](https://azure.microsoft.com/free/).
* [Automation-Konto](./automation-security-overview.md) dient zur Aufbewahrung des Runbooks und zur Authentifizierung gegenüber Azure-Ressourcen. Dieses Konto muss über die Berechtigung zum Starten und Beenden des virtuellen Computers verfügen.
* Ein [Azure Storage-Konto](../storage/common/storage-account-create.md), unter dem die Resource Manager-Vorlage gespeichert wird.
* Eine Installation von Azure PowerShell auf einem lokalen Computer. Weitere Informationen zum Abrufen von Azure PowerShell finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps).

## <a name="create-the-resource-manager-template"></a>Erstellen der Resource Manager-Vorlage

In diesem Beispiel verwenden wir eine Resource Manager-Vorlage, mit der ein neues Azure Storage-Konto bereitgestellt wird.

Kopieren Sie in einem Text-Editor den folgenden Text:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Speichern Sie die Datei lokal als **TemplateTest.json**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Speichern der Resource Manager-Vorlage in Azure Storage

Wir verwenden PowerShell jetzt zum Erstellen einer Azure Storage-Datei und zum Hochladen der Datei **TemplateTest.json**. Eine Anleitung zum Erstellen einer Dateifreigabe und Hochladen einer Datei im Azure-Portal finden Sie unter [Erste Schritte mit Azure File Storage unter Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Starten Sie PowerShell auf Ihrem lokalen Computer, und führen Sie die folgenden Befehle aus, um eine Dateifreigabe zu erstellen und die Resource Manager-Vorlage auf diese Dateifreigabe hochzuladen.

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Erstellen des PowerShell-Runbookskripts

Wir erstellen jetzt ein PowerShell-Skript, mit dem die Datei **TemplateTest.json** aus Azure Storage abgerufen und die Vorlage bereitgestellt wird, um ein neues Azure Storage-Konto zu erstellen.

Fügen Sie in einem Text-Editor den folgenden Text ein:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)

# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
```

Speichern Sie die Datei lokal als **DeployTemplate.ps1**.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importieren und Veröffentlichen des Runbooks im Azure Automation-Konto

Als Nächstes verwenden wir PowerShell, um das Runbook in Ihr Azure Automation-Konto zu importieren und es anschließend zu veröffentlichen. Informationen zum Importieren und Veröffentlichen eines Runbooks im Azure-Portal finden Sie unter [Verwalten von Runbooks in Azure Automation](manage-runbooks.md).

Führen Sie die folgenden PowerShell-Befehle aus, um **DeployTemplate.ps1** als PowerShell-Runbook in Ihr Automation-Konto zu importieren:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Starten des Runbooks

Wir starten jetzt das Runbook, indem wir das [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook)-Cmdlet aufrufen. Weitere Informationen zum Starten eines Runbooks im Azure-Portal finden Sie unter [Starten eines Runbooks in Azure Automation](./start-runbooks.md).

Führen Sie die folgenden Befehle in der PowerShell-Konsole aus:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json'
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

Nachdem das Runbook ausgeführt wurde, können Sie seinen Status überprüfen, indem Sie den Eigenschaftswert des Auftragsobjekts `$job.Status` abrufen.

Das Runbook ruft die Resource Manager-Vorlage ab und verwendet sie zum Bereitstellen eines neuen Azure Storage-Kontos. Sie können prüfen, ob das neue Speicherkonto erstellt wurde, indem Sie den folgenden Befehl ausführen:

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Resource Manager-Vorlagen finden Sie unter [Übersicht über Azure Resource Manager](../azure-resource-manager/management/overview.md).
* Informationen zu den ersten Schritten mit Azure Storage finden Sie unter [Einführung in Azure Storage](../storage/common/storage-introduction.md).
* Informationen zu weiteren nützlichen Azure Automation-Runbooks finden Sie unter [Verwenden von Runbooks und Modulen in Azure Automation](automation-runbook-gallery.md).
* Eine Referenz zu den PowerShell-Cmdlets finden Sie unter [Az.Automation](/powershell/module/az.automation#automation).
