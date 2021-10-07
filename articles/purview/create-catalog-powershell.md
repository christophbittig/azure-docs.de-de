---
title: 'Schnellstart: Erstellen eines Purview-Kontos mit PowerShell/Azure CLI'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie unter Verwendung von Azure PowerShell/Azure CLI ein Azure Purview-Konto erstellen.
author: hophanms
ms.author: hophan
ms.date: 09/27/2021
ms.topic: quickstart
ms.service: purview
ms.custom:
- mode-api
ms.openlocfilehash: 0e3cb8399e42dc651a48ada6018c58cb4f48e5d8
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217351"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>Schnellstart: Erstellen eines Azure Purview-Kontos unter Verwendung von Azure PowerShell/Azure CLI

In dieser Schnellstartanleitung erstellen Sie ein Azure Purview-Konto unter Verwendung von Azure PowerShell/Azure CLI. Die [PowerShell-Referenz für Purview](/powershell/module/az.purview/) ist verfügbar, in diesem Artikel werden aber alle Schritte erläutert, die zum Erstellen eines Kontos mit PowerShell erforderlich sind.

Azure Purview ist ein Data Governance-Dienst, der Sie bei der Verwaltung Ihrer Datenlandschaft unterstützt. Durch Herstellen einer Verbindung zu Daten in Ihren lokalen Quellen, mehreren Clouds und SaaS-Quellen (Software-as-a-Service) erstellt Purview eine aktuelle Zuordnung Ihrer Informationen. Purview identifiziert und klassifiziert sensible Daten und gewährleistet eine End-to-End-Herkunft. Datenconsumer können Daten in Ihrer Organisation auffinden, und Datenadministratoren können Ihre Daten überwachen, schützen und die richtige Verwendung ihrer Daten sicherstellen.

Weitere Informationen zu Purview [finden Sie auf unserer Übersichtsseite](overview.md). Weitere Informationen zum Bereitstellen von Purview in Ihrer Organisation [finden Sie in unseren bewährten Methoden für die Bereitstellung](deployment-best-practices.md).

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

### <a name="install-powershell"></a>Installieren von PowerShell

 Installieren Sie zum Bereitstellen der Vorlage entweder Azure PowerShell oder die Azure CLI auf Ihrem Clientcomputer: [Befehlszeilenbereitstellung](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment)

## <a name="create-an-azure-purview-account"></a>Erstellen eines Azure Purview-Kontos

1. Melden Sie sich mit Ihren Azure-Anmeldeinformationen an.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. Wenn Sie über mehrere Azure-Abonnements verfügen, wählen Sie dasjenige aus, das Sie verwenden möchten:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. Erstellen Sie eine Ressourcengruppe für Ihr Purview-Konto. Falls Sie bereits über eins verfügen, können Sie diesen Schritt überspringen:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. Erstellen Sie eine Purview-Vorlagendatei (beispielsweise `purviewtemplate.json`). Sie können `name`, `location` und `capacity` (`4` oder `16`) aktualisieren:

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. Bereitstellen der Purview-Vorlage

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    Für die Ausführung dieses Bereitstellungsbefehls müssen Sie über die [aktuelle Version](/cli/azure/install-azure-cli) der Azure CLI verfügen.
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. Der Bereitstellungsbefehl gibt Ergebnisse zurück. Suchen Sie nach `ProvisioningState`, um zu sehen, ob die Bereitstellung erfolgreich war.
    
## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie ein Azure Purview-Konto erstellen.

Lesen Sie diese nächsten Artikel, um zu erfahren, wie Sie in Purview Studio navigieren, eine Sammlung erstellen und Zugriff auf Purview gewähren.

* [Verwenden von Purview Studio](use-purview-studio.md)
* [Erstellen einer Sammlung](quickstart-create-collection.md)
* [Rollenbasierte Zugriffssteuerung auf der Datenebene von Azure Purview](catalog-permissions.md)
