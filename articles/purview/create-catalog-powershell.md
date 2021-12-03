---
title: 'Schnellstart: Erstellen eines Purview-Kontos mit PowerShell/Azure CLI'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie unter Verwendung von Azure PowerShell/Azure CLI ein Azure Purview-Konto erstellen.
author: hophanms
ms.author: hophan
ms.date: 10/28/2021
ms.topic: quickstart
ms.service: purview
ms.custom: mode-api, devx-track-azurepowershell
ms.openlocfilehash: ac06f6641f60ec1775e161ec1e949522321fd5af
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990195"
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
    New-AzResourceGroup -Name myResourceGroup -Location 'East US'
    ```

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```

    ---

1. Erstellen oder Bereitstellen des Purview-Kontos

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    Verwenden Sie das Cmdlet [New-AzPurviewAccount](/powershell/module/az.purview/new-azpurviewaccount), um das Purview-Konto zu erstellen:

    ```azurepowershell
    New-AzPurviewAccount -Name yourPurviewAccountName -ResourceGroupName myResourceGroup -Location eastus -IdentityType SystemAssigned -SkuCapacity 4 -SkuName Standard -PublicNetworkAccess
    ```

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

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

        Für die Ausführung dieses Bereitstellungsbefehls müssen Sie über die [aktuelle Version](/cli/azure/install-azure-cli) der Azure CLI verfügen.

        ```azurecli
        az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
        ```

    ---

1. Der Bereitstellungsbefehl gibt Ergebnisse zurück. Suchen Sie nach `ProvisioningState`, um zu sehen, ob die Bereitstellung erfolgreich war.

1. Wenn Sie das Azure Purview-Konto mithilfe eines Dienstprinzipals anstelle eines Benutzerkontos bereitgestellt haben, müssen Sie zudem den folgenden Befehl in der Azure CLI ausführen:

    ```azurecli
    az purview account add-root-collection-admin --account-name --resource-group [--object-id]
    ```

    Mit diesem werden dem Benutzerkonto Berechtigungen vom Typ [Sammlungsadministrator](catalog-permissions.md#roles) für die Stammsammlung in Ihrem Azure Purview-Konto gewährt. Dies ermöglicht dem Benutzer das Zugreifen auf Purview Studio und das Hinzufügen von Berechtigungen für andere Benutzer. Weitere Informationen zu Berechtigungen in Azure Purview finden Sie in unserem [Leitfaden zu Berechtigungen](catalog-permissions.md). Weitere Informationen zu Sammlungen finden Sie im Artikel zum [Verwalten von Sammlungen](how-to-create-and-manage-collections.md).

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie ein Azure Purview-Konto erstellen.

Lesen Sie diese nächsten Artikel, um zu erfahren, wie Sie in Purview Studio navigieren, eine Sammlung erstellen und Zugriff auf Purview gewähren.

* [Verwenden von Purview Studio](use-purview-studio.md)
* [Rollenbasierte Zugriffssteuerung auf der Datenebene von Azure Purview](catalog-permissions.md)
* [Erstellen einer Sammlung](quickstart-create-collection.md)
