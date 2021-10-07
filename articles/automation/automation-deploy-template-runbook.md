---
title: Bereitstellen einer Azure Resource Manager-Vorlage in einem Azure Automation-PowerShell-Runbook
description: In diesem Artikel wird das Bereitstellen einer in Azure Storage gespeicherten Azure Resource Manager-Vorlage aus einem PowerShell-Runbook beschrieben.
services: automation
ms.subservice: process-automation
ms.date: 09/23/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 61ff25cd9878ee94ce0ba6db7b2c4e4ac8e649de
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057755"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-automation-powershell-runbook"></a>Bereitstellen einer Azure Resource Manager-Vorlage in einem Automation-PowerShell-Runbook

Sie können ein [Automation-PowerShell-Runbook](./learn/automation-tutorial-runbook-textual-powershell.md), das eine Azure-Ressource bereitstellt, mithilfe einer [Azure Resource Manager-Vorlage](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) schreiben. Vorlagen ermöglichen Ihnen das Automatisieren der Bereitstellung Ihrer Azure-Ressourcen mithilfe von Azure Automation. Sie können Ihre Resource Manager-Vorlagen an einem zentralen, sicheren Ort verwalten, z. B. in Azure Storage.

In diesem Artikel erstellen wir ein PowerShell-Runbook, für das eine in [Azure Storage](../storage/common/storage-introduction.md) gespeicherte Resource Manager-Vorlage verwendet wird, um ein neues Azure Storage-Konto bereitzustellen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Automation-Konto mit mindestens einer benutzerseitig zugewiesenen verwalteten Identität. Weitere Informationen finden Sie unter [Verwenden einer benutzerseitig zugewiesenen verwalteten Identität für ein Azure Automation-Konto](./add-user-assigned-identity.md).

* Az-Module: `Az.Accounts`, `Az.ManagedServiceIdentity`, `Az.Resources` und `Az.Storage`. Importiert in das Automation-Konto. Weitere Informationen finden Sie unter [Importieren von Az-Modulen](./shared-resources/modules.md#import-az-modules).

* Ein [Azure Storage-Konto](../storage/common/storage-account-create.md), unter dem die Resource Manager-Vorlage gespeichert wird.

* Eine Installation von Azure PowerShell auf einem lokalen Computer. Weitere Informationen zum Abrufen von Azure PowerShell finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps). Außerdem benötigen Sie das Modul [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity). `Az.ManagedServiceIdentity` ist ein Vorschaumodul und wird nicht als Teil des Az-Moduls installiert. Um es zu installieren, führen Sie `Install-Module -Name Az.ManagedServiceIdentity` aus.

## <a name="assign-permissions-to-managed-identities"></a>Zuweisen von Berechtigungen zu verwalteten Identitäten

Weisen Sie den verwalteten Identitäten Berechtigungen zu, um die speicherbezogenen Aufgaben im Runbook auszuführen.

1. Melden Sie sich interaktiv mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei Azure an, und befolgen Sie die Anweisungen.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId <SUBSCRIPTIONID>
    ```

1. Geben Sie einen geeigneten Wert für die unten stehenden Variablen an, und führen Sie dann das Skript aus.

    ```powershell
    $resourceGroup = "resourceGroup"
    $automationAccount = "automationAccount"
    $storageAccount = "storageAccount"
    $userAssignedManagedIdentity = "userAssignedManagedIdentity"
    $storageTemplate = "path\storageTemplate.json"
    $runbookScript = "path\runbookScript.ps1"
    ```

1. Weisen Sie der systemseitig zugewiesenen verwalteten Identität die Rolle `reader` zu, um das Cmdlet `Get-AzUserAssignedIdentity` auszuführen.

    ```powershell
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. Weisen Sie der benutzerseitig zugewiesenen verwalteten Identität für Aktionen mit dem Speicherkonto die Rolle `Storage Account Contributor` zu.

    ```powershell
    $UAMI_ID = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedManagedIdentity).PrincipalId
    New-AzRoleAssignment `
        -ObjectId $UAMI_ID `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Storage Account Contributor"
    ```


## <a name="create-the-resource-manager-template"></a>Erstellen der Resource Manager-Vorlage

In diesem Beispiel verwenden Sie eine Resource Manager-Vorlage, mit der ein neues Azure Storage-Konto bereitgestellt wird. Erstellen Sie eine lokale Datei namens `storageTemplate.json`, und fügen Sie den folgenden Code in die Datei ein:

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

## <a name="save-the-resource-manager-template-in-azure-files"></a>Speichern der Resource Manager-Vorlage in Azure Files

Verwenden Sie PowerShell, um eine Azure-Dateifreigabe zu erstellen und `storageTemplate.json` hochzuladen. Eine Anleitung zum Erstellen einer Dateifreigabe und Hochladen einer Datei im Azure-Portal finden Sie unter [Erste Schritte mit Azure Files unter Windows](../storage/files/storage-files-quick-create-use-windows.md).

Führen Sie die folgenden Befehle aus, um eine Dateifreigabe zu erstellen und die Resource Manager-Vorlage auf diese Dateifreigabe hochzuladen.

```powershell
# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName $resourceGroup -Name $storageAccount

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName $storageAccount -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the storageTemplate.json file to the new file share
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $storageTemplate
```

## <a name="create-the-powershell-runbook-script"></a>Erstellen des PowerShell-Runbookskripts

Erstellen Sie ein PowerShell-Skript, mit dem die Datei `storageTemplate.json` aus Azure Storage abgerufen und die Vorlage bereitgestellt wird, um ein neues Azure Storage-Konto zu erstellen.  Erstellen Sie eine lokale Datei namens `runbookScript.ps1`, und fügen Sie den folgenden Code in die Datei ein:


```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $resourceGroup,

    [Parameter(Mandatory=$true)]
    [string]
    $storageAccount,

    [Parameter(Mandatory=$true)]
    [string]
    $storageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $storageFileName,

    [Parameter(Mandatory=$true)]
    [string]
    $userAssignedManagedIdentity
)

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with user-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context
$identity = Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup `
    -Name $userAssignedManagedIdentity `
    -DefaultProfile $AzureContext
$AzureContext = (Connect-AzAccount -Identity -AccountId $identity.ClientId).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
    -DefaultProfile $AzureContext

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageAccountKey

Get-AzStorageFileContent `
    -ShareName 'resource-templates' `
    -Context $Context `
    -path 'storageTemplate.json' `
    -Destination 'C:\Temp' -Force

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $storageFileName

# Deploy the storage account
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroup `
    -TemplateFile $TemplateFile `
    -TemplateParameterObject $Parameters 
```

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importieren und Veröffentlichen des Runbooks im Azure Automation-Konto

Verwenden Sie PowerShell, um das Runbook in Ihr Automation-Konto zu importieren und es anschließend zu veröffentlichen. Informationen zum Importieren und Veröffentlichen eines Runbooks im Azure-Portal finden Sie unter [Verwalten von Runbooks in Azure Automation](manage-runbooks.md).

Führen Sie die folgenden PowerShell-Befehle aus, um `runbookScript.ps1` als PowerShell-Runbook in Ihr Automation-Konto zu importieren:

```powershell
$importParams = @{
    Path = $runbookScript
    ResourceGroupName = $resourceGroup
    AutomationAccountName = $automationAccount
    Type = "PowerShell"
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = $resourceGroup
    AutomationAccountName = $automationAccount
    Name = "runbookScript"
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Starten des Runbooks

Wir starten jetzt das Runbook, indem wir das [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook)-Cmdlet aufrufen. Weitere Informationen zum Starten eines Runbooks im Azure-Portal finden Sie unter [Starten eines Runbooks in Azure Automation](./start-runbooks.md).

Führen Sie die folgenden Befehle in der PowerShell-Konsole aus:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    resourceGroup = $resourceGroup
    storageAccount = $storageAccount
    storageAccountKey = $key[0].Value # We got this key earlier
    storageFileName = "storageTemplate.json"
    userAssignedManagedIdentity = $userAssignedManagedIdentity
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    resourceGroup = $resourceGroup
    AutomationAccountName = $automationAccount
    Name = "runbookScript"
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
