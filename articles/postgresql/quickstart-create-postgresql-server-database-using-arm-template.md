---
title: 'Schnellstart: Erstellen eines Azure DB for PostgreSQL-Servers – ARM-Vorlage'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe einer Azure Resource Manager-Vorlage einen Azure Database for PostgreSQL-Einzelserver erstellen.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.date: 02/11/2021
ms.openlocfilehash: 20022df3080b18a93746b61bafc3bcbc0e9e44b7
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424886"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---single-server"></a>Schnellstart: Verwenden einer ARM-Vorlage zum Erstellen eines Azure Database for PostgreSQL-Einzelservers

Azure-Datenbank für PostgreSQL ist ein verwalteter Dienst, mit dem Sie hochverfügbare PostgreSQL-Datenbanken in der Cloud ausführen, verwalten und skalieren können. In diesem Schnellstart verwenden Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) zum Erstellen eines Azure Database for PostgreSQL-Einzelservers im Azure-Portal, in PowerShell oder in der Azure CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Bereitstellen in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.dbforpostgresql%2Fmanaged-postgresql-with-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/)
* Wenn Sie den Code lokal ausführen möchten, verwenden Sie [Azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/)
* Wenn Sie den Code lokal ausführen möchten, verwenden Sie die [Azure CLI](/cli/azure/).

---

## <a name="review-the-template"></a>Überprüfen der Vorlage

Sie erstellen einen Azure Database for PostgreSQL-Server mit einer konfigurierten Gruppe von Compute- und Speicherressourcen. Weitere Informationen finden Sie unter [Tarife in Azure Database for PostgreSQL – Einzelserver](concepts-pricing-tiers.md) Der Server wird in einer [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md) erstellt.

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/managed-postgresql-with-vnet/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.dbforpostgresql/managed-postgresql-with-vnet/azuredeploy.json":::

Die Vorlage definiert fünf Azure-Ressourcen:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft.DBforPostgreSQL/servers**](/azure/templates/microsoft.dbforpostgresql/servers)
* [**Microsoft.DBforPostgreSQL/servers/virtualNetworkRules**](/azure/templates/microsoft.dbforpostgresql/servers/virtualnetworkrules)
* [**Microsoft.DBforPostgreSQL/servers/firewallRules**](/azure/templates/microsoft.dbforpostgresql/servers/firewallrules)

Weitere Vorlagenbeispiele für Azure Database for PostgreSQL finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbforpostgresql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie den folgenden Link aus, um die Azure Database for PostgreSQL-Servervorlage im Azure-Portal bereitzustellen:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Bereitstellen in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.dbforpostgresql%2Fmanaged-postgresql-with-vnet%2Fazuredeploy.json)

Führen Sie auf der Seite **Deploy Azure Database for PostgreSQL with VNet** (Azure Database for PostgreSQL mit VNET bereitstellen) die folgenden Schritte aus:

1. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, geben Sie einen Namen für die neue Ressourcengruppe ein, und wählen Sie **OK** aus.

2. Wenn Sie eine neue Ressourcengruppe erstellt haben, wählen Sie für die Ressourcengruppe und den neuen Server eine Angabe unter **Standort** aus.

3. Geben Sie Werte für **Servername**, **Administratoranmeldung** und **Kennwort für die Administratoranmeldung** ein.

    :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-arm-template/deploy-azure-database-for-postgresql-with-vnet.png" alt-text="Fenster „Deploy Azure Database for PostgreSQL with VNet“ (Azure Database for PostgreSQL mit VNET bereitstellen) unter „Azure-Schnellstartvorlage“ im Azure-Portal":::

4. Ändern Sie ggf. die anderen Standardeinstellungen:

    * **Abonnement**: das für den Server zu verwendende Azure-Abonnement
    * **SKU-Kapazität**: die Kapazität des virtuellen Kerns. Mögliche Werte: *2* (Standardeinstellung), *4*, *8*, *16*, *32* oder *64*
    * **SKU-Name**: Präfix des SKU-Tarifs, SKU-Familie und SKU-Kapazität, verbunden durch Unterstriche. Beispiele: *B_Gen5_1*, *GP_Gen5_2* (Standardeinstellung) oder *MO_Gen5_32*
    * **Sku Size MB** (SKU-Größe (MB)): die Speichergröße des Azure Database for PostgreSQL-Servers in Megabyte (Standardeinstellung: *51200*)
    * **SKU-Tarif**: der Bereitstellungstarif, etwa *Basic*, *GeneralPurpose* (Standardeinstellung) oder *MemoryOptimized*
    * **SKU-Familie**: *Gen4* oder *Gen5* (Standardeinstellung). Mit dieser Option wird die Hardwaregeneration für die Serverbereitstellung angegeben.
    * **PostgreSQL-Version**: die Version des bereitzustellenden PostgreSQL-Servers, z. B. *9.5*, *9.6*, *10* oder *11* (Standardeinstellung)
    * **Backup Retention Days** (Sicherungsaufbewahrung in Tagen): der gewünschte Zeitraum für die Aufbewahrung georedundanter Sicherungen in Tagen (Standardeinstellung: *7*)
    * **Georedundante Sicherung**: *Aktiviert* oder *Deaktiviert* (Standardeinstellung), abhängig von den Anforderungen an die georedundante Notfallwiederherstellung (Geo-Disaster Recovery, Geo-DR)
    * **Name des virtuellen Netzwerks**: der Name des virtuellen Netzwerks (Standardeinstellung: *azure_postgresql_vnet*)
    * **Subnetzname**: der Name des Subnetzes (Standardeinstellung: *azure_postgresql_subnet*)
    * **Virtual Network Rule Name** (Name der VNET-Regel): der Name der VNET-Regel, die das Subnetz zulässt (Standardeinstellung: *AllowSubnet*)
    * **Vnet Address Prefix** (VNET-Adresspräfix): das Adresspräfix für das virtuelle Netzwerk (Standardeinstellung: *10.0.0.0/16*)
    * **Subnetzpräfix**: das Adresspräfix für das Subnetz (Standardeinstellung: *10.0.0.0/16*)

5. Lesen Sie die Geschäftsbedingungen, und wählen Sie anschließend die Option **Ich stimme den oben genannten Geschäftsbedingungen zu** aus.

6. Wählen Sie die Option **Kaufen**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Verwenden Sie den folgenden interaktiven Code, um mithilfe der Vorlage einen neuen Azure Database for PostgreSQL-Server zu erstellen. Vom Code werden Sie zur Eingabe des neuen Servernamens, des Namens und des Standorts einer neuen Ressourcengruppe sowie eines Administratorkontonamens und Kennworts aufgefordert.

Wählen Sie zum Ausführen des Codes in Azure Cloud Shell in der oberen Ecke eines beliebigen Codeblocks **Ausprobieren** aus.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.dbforpostgresql/managed-postgresql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

Verwenden Sie den folgenden interaktiven Code, um mithilfe der Vorlage einen neuen Azure Database for PostgreSQL-Server zu erstellen. Vom Code werden Sie zur Eingabe des neuen Servernamens, des Namens und des Standorts einer neuen Ressourcengruppe sowie eines Administratorkontonamens und Kennworts aufgefordert.

Wählen Sie zum Ausführen des Codes in Azure Cloud Shell in der oberen Ecke eines beliebigen Codeblocks **Ausprobieren** aus.

```azurecli-interactive
read -p "Enter a name for the new Azure Database for PostgreSQL server:" serverName &&
read -p "Enter a name for the new resource group where the server will exist:" resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group:" location &&
read -p "Enter the Azure Database for PostgreSQL server's administrator account name:" adminUser &&
read -p "Enter the administrator password:" adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.dbforpostgresql/managed-postgresql-with-vnet/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um eine Übersicht über Ihren neuen Azure Database for PostgreSQL-Server anzuzeigen:

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **Azure Database for PostgreSQL-Server**, und wählen Sie die Option aus.

2. Wählen Sie in der Datenbankliste den neuen Server aus. Die Seite **Übersicht** für Ihren neuen Azure Database for PostgreSQL-Server wird angezeigt.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Führen Sie den folgenden interaktiven Code aus, um Details zu Ihrem Azure Database for PostgreSQL-Server anzuzeigen. Sie müssen den Namen des neuen Servers eingeben.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for PostgreSQL server"
Get-AzResource -ResourceType "Microsoft.DbForPostgreSQL/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

Führen Sie den folgenden interaktiven Code aus, um Details zu Ihrem Azure Database for PostgreSQL-Server anzuzeigen. Sie müssen den Namen und die Ressourcengruppe des neuen Servers eingeben.

```azurecli-interactive
read -p "Enter your Azure Database for PostgreSQL server name: " serverName &&
read -p "Enter the resource group where the Azure Database for PostgreSQL server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForPostgreSQL/servers" &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="exporting-arm-template-from-the-portal"></a>Exportieren einer ARM-Vorlage über das Portal
Sie können über das Azure-Portal [eine ARM-Vorlage exportieren](../azure-resource-manager/templates/export-template-portal.md). Eine Vorlage kann auf zwei Arten exportiert werden:

- [Export aus Ressourcengruppe oder Ressource](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource). Diese Option generiert eine neue Vorlage aus vorhandenen Ressourcen. Die exportierte Vorlage ist eine „Momentaufnahme“ des aktuellen Zustands der Ressourcengruppe. Sie können eine gesamte Ressourcengruppe oder bestimmte Ressourcen innerhalb dieser Ressourcengruppe exportieren.
- [Export vor der Bereitstellung oder über den Verlauf](../azure-resource-manager/templates/export-template-portal.md#download-template-before-deployment). Diese Option ruft eine exakte Kopie einer für die Bereitstellung verwendeten Vorlage ab.

Beim Exportieren der Vorlage sehen Sie im Abschnitt ```"properties":{ }``` der PostgreSQL-Serverressource, dass ```administratorLogin``` und ```administratorLoginPassword``` aus Sicherheitsgründen nicht enthalten sind. Diese Parameter **MÜSSEN** Ihrer Vorlage vor der Bereitstellung hinzugefügt werden. Andernfalls tritt bei der Vorlage ein Fehler auf.

```
"resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
      "apiVersion": "2017-12-01",
      "name": "[parameters('servers_name')]",
      "location": "southcentralus",
      "sku": {
                "name": "B_Gen5_1",
                "tier": "Basic",
                "family": "Gen5",
                "capacity": 1
            },
      "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
```



## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Ressourcen in der Ressourcengruppe gelöscht.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **Ressourcengruppen**, und wählen Sie die entsprechende Option aus.

2. Wählen Sie in der Liste der Ressourcengruppen den Namen Ihrer Ressourcengruppe aus.

3. Wählen Sie auf der Seite **Übersicht** der Ressourcengruppe die Option **Ressourcengruppe löschen** aus.

4. Geben Sie im Bestätigungsdialogfeld den Namen Ihrer Ressourcengruppe ein, und wählen Sie **Löschen** aus.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli-interactive
read -p "Enter the Resource Group name: " resourceGroupName &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Nächste Schritte

Ein Schritt-für-Schritt-Tutorial mit Anleitungen zum Erstellen einer Vorlage finden Sie unter folgendem Link:

> [!div class="nextstepaction"]
> [Tutorial: Erstellen und Bereitstellen Ihrer ersten ARM-Vorlage](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
