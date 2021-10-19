---
title: Bereitstellen des FHIR-Diensts für Azure Healthcare-APIs mithilfe einer ARM-Vorlage
description: Erfahren Sie, wie Sie den FHIR-Dienst mithilfe einer Azure Resource Manager (ARM-Vorlage) bereitstellen.
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.author: zxue
ms.date: 08/06/2021
ms.openlocfilehash: 97322c917e12e451affc2fe8a67bb8c8f6200a74
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782420"
---
# <a name="deploy-a-fhir-service-within-azure-healthcare-apis---using-arm-template"></a>Bereitstellen eines FHIR-Diensts in Azure Healthcare-APIs mithilfe einer ARM-Vorlage

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel erfahren Sie, wie Sie den FHIR-Dienst in den Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) mithilfe der Azure Resource Manager-Vorlage (ARM-Vorlage) bereitstellen. Wir stellen Ihnen zwei Optionen zur Verfügung: PowerShell oder cli.

Eine [ARM-Vorlage](../../azure-resource-manager/templates/overview.md) ist eine JSON-Datei, die die Infrastruktur und Konfiguration für Ihr Projekt definiert. Die Vorlage verwendet eine deklarative Syntax. In deklarativer Syntax beschreiben Sie Ihre beabsichtigte Bereitstellung, ohne die Reihenfolge der Programmierbefehle zur Erstellung der Bereitstellung zu schreiben.

## <a name="prerequisites"></a>Voraussetzungen

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/)
* Wenn Sie den Code lokal ausführen möchten:
    * Mithilfe von [Azure PowerShell](/powershell/azure/install-az-ps):

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/)
* Wenn Sie den Code lokal ausführen möchten:
    * Eine Bash-Shell (z.B. Git Bash, in [Git für Windows](https://gitforwindows.org) enthalten).
    * [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

---

## <a name="review-the-arm-template"></a>Überprüfen der ARM-Vorlage

Die in diesem Artikel verwendete Vorlage stammt aus den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/azure-api-for-fhir/).

Die Vorlage definiert drei Azure-Ressourcen:
- Microsoft.HealthcareApis/workspaces
- Microsoft.HealthcareApis/workspaces/fhirservices      
- Microsoft.Storage/storageAccounts

Sie können die FHIR-Dienstressource bereitstellen, indem Sie die Arbeitsbereichsressource, die Speicherressource und die Eigenschaft in der Ressource  `dependsOn` "Microsoft.HealthcareApis/workspaces/fhirservices" entfernen.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "authorityurl": {
            "type": "string",
            "defaultValue": "https://login.microsoftonline.com"
        },
        "tagName": {
            "type": "string",
            "defaultValue": "My Deployment"
        },
        "region": {
            "type": "string",
                  "allowedValues": [
                "australiaeast",
                "canadacentral",
                "eastus",
                "eastus2",
                "germanywestcentral",
                "japaneast",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "switzerlandnorth",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus2"
            ]
        },
        "workspaceName": {
            "type": "string"
        },
        "fhirServiceName": {
            "type": "string"
        },
        "tenantid": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountConfirm": {
            "type": "bool",
            "defaultValue": true
        },
        "AccessPolicies": {
            "type": "array",
            "defaultValue": []
        },
        "smartProxyEnabled": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "variables": { 
        "authority": "[Concat(parameters('authorityurl'), '/', parameters('tenantid'))]",
        "createManagedIdentity": true,
        "managedIdentityType": {
            "type": "SystemAssigned"
        },
        "storageBlobDataContributerRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'ba92f5b4-2d11-453d-a403-e96b0029c9fe')]"
    },
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2020-11-01-preview",
            "location": "[parameters('region')]",
            "properties": {}
        },
       {          
            "type": "Microsoft.HealthcareApis/workspaces/fhirservices",
            "kind": "fhir-R4",
            "name": "[concat(parameters('workspaceName'), '/', parameters('fhirServiceName'))]",
            "apiVersion": "2020-11-01-preview",
            "location": "[parameters('region')]",
            "dependsOn": [
                "[resourceId('Microsoft.HealthcareApis/workspaces', parameters('workspaceName'))]"
            ],
            "tags": {
                "environmentName": "[parameters('tagName')]"
            },
            "properties": {
                "accessPolicies": "[parameters('AccessPolicies')]",
                "authenticationConfiguration": {
                    "authority": "[variables('Authority')]",
                    "audience": "[concat('https//', parameters('workspaceName'), '-', parameters('fhirServiceName'), '.fhir.azurehealthcareapis.com')]",
                    "smartProxyEnabled": "[parameters('smartProxyEnabled')]"
                },
                "corsConfiguration": {
                    "allowCredentials": false,
                    "headers": ["*"],
                    "maxAge": 1440,
                    "methods": ["DELETE", "GET", "OPTIONS", "PATCH", "POST", "PUT"],
                    "origins": ["https://localhost:6001"]
                },
                "exportConfiguration": {
                    "storageAccountName": "[parameters('storageAccountName')]"
                }
            },
            "identity": "[if(variables('createManagedIdentity'), variables('managedIdentityType'), json('null'))]"
        },
        {
            "name": "[parameters('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "supportsHttpsTrafficOnly": "true"
            },
            "condition": "[parameters('storageAccountConfirm')]",
            "dependsOn": [
                "[parameters('fhirServiceName')]"
            ],
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "tags": {
                "environmentName": "[parameters('tagName')]",
                "test-account-rg": "true"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="deploy-arm-template"></a>Bereitstellen einer ARM-Vorlage

Sie können die ARM-Vorlage mit zwei Optionen bereitstellen: PowerShell oder CLI.

Der unten bereitgestellte Beispielcode verwendet die Vorlage im Unterordner "templates" des Unterordners "src". Möglicherweise möchten Sie den Speicherortpfad ändern, um ordnungsgemäß auf die Vorlagendatei zu verweisen.

Der Bereitstellungsprozess dauert einige Minuten. Notieren Sie sich die Namen für den FHIR-Dienst und die Ressourcengruppe, die Sie später verwenden werden.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

### <a name="deploy-the-template-using-powershell"></a>Bereitstellen der Vorlage: Verwenden von PowerShell

Führen Sie den Code in PowerShell lokal, in Visual Studio Code oder in Azure Cloud Shell aus, um den FHIR-Dienst bereitzustellen. 

Wenn Sie sich noch nicht bei Azure angemeldet haben, melden Sie sich mit "Verbinden-AzAccount" an. Nachdem Sie sich angemeldet haben, verwenden Sie "Get-AzContext", um das Abonnement und den Mandanten zu überprüfen, die Sie verwenden möchten. Sie können das Abonnement und den Mandanten bei Bedarf ändern.

Sie können eine neue Ressourcengruppe erstellen oder eine vorhandene verwenden, indem Sie den Schritt überspringen oder die Zeile auskommentieren, die mit "New-AzResourceGroup" beginnt.

```powershell-interactive
### variables
$resourcegroupname="your resource group"
$location="South Central US"
$workspacename="your workspace name"
$servicename="your fhir service name"
$tenantid="xxx"
$subscriptionid="xxx"
$storageaccountname="storage account name"
$storageaccountconfirm=1

### login to azure
Connect-AzAccount 
#Connect-AzAccount SubscriptionId $subscriptionid
Set-AzContext -Subscription $subscriptionid
Connect-AzAccount -Tenant $tenantid -SubscriptionId $subscriptionid
#Get-AzContext 

### create resource group
New-AzResourceGroup -Name $resourcegroupname -Location $location

### deploy the resource
New-AzResourceGroupDeployment -ResourceGroupName $resourcegroupname -TemplateFile "src/templates/fhirtemplate.json" -region $location -workspaceName $workspacename -fhirServiceName $fhirservicename -tenantid $tenantid -storageAccountName $storageaccountname -storageAccountConfirm $storageaccountconfirm
```
# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

### <a name="deploy-the-template-using-cli"></a>Bereitstellen der Vorlage: Verwenden der CLI

Führen Sie den Code lokal, in Visual Studio Code oder in Azure Cloud Shell aus, um den FHIR-Dienst bereitzustellen. 

Wenn Sie sich noch nicht bei Azure angemeldet haben, verwenden Sie "az login", um sich anzumelden. Nachdem Sie sich angemeldet haben, überprüfen Sie mit "az account show --output table" das Abonnement und den Mandanten, die Sie verwenden möchten. Sie können das Abonnement und den Mandanten bei Bedarf ändern.

Sie können eine neue Ressourcengruppe erstellen oder eine vorhandene verwenden, indem Sie den Schritt überspringen oder die Zeile auskommentieren, die mit "az group create" beginnt.

```azurecli-interactive
### variables
resourcegroupname=your resource group name
location=southcentralus
workspacename=your workspace name
fhirservicename=your fhir service name
tenantid=xxx
subscriptionid=xxx
storageaccountname=your storage account name
storageaccountconfirm=true

### login to azure
az login
az account show --output table
az account set --subscription $subscriptionid

### create resource group
az group create --name $resourcegroupname --location $location

### deploy the resource
az deployment group create --resource-group $resourcegroupname --template-file 'src\\templates\\fhirtemplate.json' --parameters region=$location workspaceName=$workspacename fhirServiceName=$fhirservicename tenantid=$tenantid storageAccountName=$storageaccountname storageAccountConfirm=$storageaccountconfirm
```

---

## <a name="review-the-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Sie können überprüfen, ob der FHIR-Dienst ausgeführt wird, indem Sie den Browser öffnen und zu `https://<yourfhir servic>.azurehealthcareapis.com/metadata` navigieren. Wenn die Capability-Anweisung automatisch angezeigt oder heruntergeladen wird, ist Ihre Bereitstellung erfolgreich. 

## <a name="clean-up-the-resources"></a>Bereinigen der Ressourcen

Wenn die Ressource nicht mehr benötigt wird, führen Sie den folgenden Code aus, um die Ressourcengruppe zu löschen.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)
```powershell-interactive
$resourceGroupName = “your resource group name”
Remove-AzResourceGroup -Name $resourceGroupName
```
# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)
```azurecli-interactive
resourceGroupName = “your resource group name”
az group delete --name $resourceGroupName
```
---

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie den FHIR-Dienst in Azure Healthcare APis mithilfe einer ARM-Vorlage bereitgestellt. Weitere Informationen zu den vom FHIR-Dienst unterstützten Features finden Sie unter .

>[!div class="nextstepaction"]
>[Unterstützte FHIR-Features](fhir-features-supported.md)