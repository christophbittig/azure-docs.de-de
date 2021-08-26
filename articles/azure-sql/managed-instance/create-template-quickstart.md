---
title: 'Azure Resource Manager: Erstellen einer verwalteten Azure SQL-Instanz'
description: Erfahren Sie, wie Sie mit einer Azure Resource Manager-Vorlage eine verwaltete Azure SQL-Instanz erstellen.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: subject-armqs, devx-track-azurepowershell
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma
ms.date: 06/22/2020
ms.openlocfilehash: 882613ef5b27b7021c61cabfabe3f37c3c842bd6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722821"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-arm-template"></a>Schnellstart: Erstellen einer Azure SQL Managed Instance mit einer ARM-Vorlage

In dieser Schnellstartanleitung geht es um die Bereitstellung einer Azure Resource Manager-Vorlage (ARM-Vorlage) zum Erstellen einer Azure SQL Managed Instance und eines VNET. [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) ist eine intelligente, vollständig verwaltete und skalierbare Clouddatenbank mit fast 100%iger Featureparität mit der SQL Server-Datenbank-Engine.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.sql%2Fsqlmi-new-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/sqlmi-new-vnet/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.sql/sqlmi-new-vnet/azuredeploy.json":::

Diese Ressourcen sind in der Vorlage definiert:

- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.Network/networkSecurityGroups)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft.Sql/managedinstances**](/azure/templates/microsoft.sql/managedinstances)

Weitere Vorlagenbeispiele finden Sie in den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Wählen Sie **Ausprobieren** aus dem folgenden PowerShell-Codeblock, um die Azure Cloud Shell zu öffnen.

> [!IMPORTANT]
> Die Bereitstellung einer verwalteten Instanz ist ein Vorgang mit langer Ausführungsdauer. Die Bereitstellung der ersten Instanz im Subnetz dauert normalerweise deutlich länger als die Bereitstellung in einem Subnetz, das bereits verwaltete Instanzen enthält. Informationen zur durchschnittlichen Bereitstellungsdauer finden Sie unter [Verwaltungsvorgänge für verwaltete SQL-Instanzen](management-operations-overview.md#duration).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.sql/sqlmi-new-vnet/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.sql/sqlmi-new-vnet/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Rufen Sie das [Azure-Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) auf, und überprüfen Sie, ob sich die verwaltete Instanz in der ausgewählten Ressourcengruppe befindet. Da das Erstellen einer verwalteten Instanz einige Zeit in Anspruch nehmen kann, müssen Sie möglicherweise den Link **Bereitstellungen** auf der Seite **Übersicht** für die Ressourcengruppe überprüfen.

- Eine Schnellstartanleitung, die zeigt, wie Sie eine Verbindung zwischen einem virtuellen Azure-Computer und Azure SQL Managed Instance herstellen, finden Sie unter [Konfigurieren einer Verbindung zu einem virtuellen Azure-Computer](connect-vm-instance-configure.md).
- Eine Schnellstartanleitung, die zeigt, wie Sie von einem lokalen Clientcomputer über eine Point-to-Site-Verbindung eine Verbindung mit Azure SQL Managed Instance herstellen, finden Sie unter [Konfigurieren einer Point-to-Site-Verbindung](point-to-site-p2s-configure.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mit [Nächste Schritte](#next-steps) fortfahren möchten, behalten Sie die verwaltete Instanz bei, doch löschen Sie nach Beendigung der weiteren Tutorials die verwaltete Instanz und zugehörige Ressourcen. Nachdem Sie eine verwaltete Instanz gelöscht haben, sehen Sie sich die Informationen unter [Löschen eines Subnetzes nach dem Löschen einer verwalteten Instanz](virtual-cluster-delete.md) an.


So löschen Sie die Ressourcengruppe:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konfigurieren einer Azure-VM für das Herstellen einer Verbindung mit Azure SQL Managed Instance](connect-vm-instance-configure.md)
