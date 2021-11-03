---
title: 'Schnellstart: Erstellen einer ExpressRoute-Leitung mithilfe einer ARM-Vorlage (Azure Resource Manager)'
description: In dieser Schnellstartanleitung wird veranschaulicht, wie Sie eine ExpressRoute-Leitung mit einer ARM-Vorlage (Azure Resource Manager) erstellen.
services: expressroute
author: duongau
ms.author: duau
manager: kumud
ms.date: 10/12/2020
ms.topic: quickstart
ms.service: expressroute
ms.custom: devx-track-azurepowershell, subject-armqs, mode-arm
ms.openlocfilehash: d2a627b5b9100e338912bbae1e18260f7466a48b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056583"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>Schnellstart: Erstellen einer ExpressRoute-Leitung mit privatem Peering mithilfe einer ARM-Vorlage

In dieser Schnellstartanleitung wird beschrieben, wie Sie mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) eine ExpressRoute-Leitung mit privatem Peering erstellen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fexpressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/expressroute-private-peering-vnet).

In dieser Schnellstartanleitung erstellen Sie mit *Equinix* als Dienstanbieter eine ExpressRoute-Leitung. Die Leitung nutzt eine *Premium-SKU* mit einer Bandbreite von *50 MBit/s* und dem Peeringstandort *Washington DC*. Privates Peering wird mit dem primären Subnetz *192.168.10.16/30* und dem sekundären Subnetz *192.168.10.20/30* aktiviert. Außerdem wird ein virtuelles Netzwerk zusammen mit einem *HighPerformance-ExpressRoute-Gateway* erstellt.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/expressroute-private-peering-vnet/azuredeploy.json":::

In der Vorlage wurden mehrere Azure-Ressourcen definiert:

* [**Microsoft.Network/expressRouteCircuits**](/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft.Network/expressRouteCircuits/peerings**](/azure/templates/microsoft.network/expressRouteCircuits/peerings) (zum Aktivieren des privaten Peerings für die Leitung)
* [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networkSecurityGroups) (Die Netzwerksicherheitsgruppe wird auf die Subnetze im virtuellen Netzwerk angewendet.)
* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicIPAddresses) (Die öffentliche IP-Adresse wird vom ExpressRoute-Gateway verwendet.)
* [**Microsoft.Network/virtualNetworkGateways**](/azure/templates/microsoft.network/virtualNetworkGateways) (Das ExpressRoute-Gateway wird zum Verknüpfen des VNET mit der Leitung verwendet.)

Weitere Vorlagen zu ExpressRoute finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Wählen Sie **Try it** (Ausprobieren) im folgenden Codeblock aus, um Azure Cloud Shell zu öffnen. Folgen Sie dann den Anweisungen, um sich bei Azure anzumelden.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/expressroute-private-peering-vnet/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Warten Sie, bis die Aufforderung in der Konsole angezeigt wird.

1. Wählen Sie **Copy** (Kopieren) im vorherigen Codeblock aus, um das PowerShell-Skript zu kopieren.

1. Klicken Sie mit der rechten Maustaste auf den Shellkonsolenbereich, und wählen Sie **Einfügen** aus.

1. Gehen Sie die Werte ein.

    Der Ressourcengruppenname ist der Projektname mit dem Zusatz **rg**.

    Das Bereitstellen der Vorlage dauert ungefähr 20 Minuten. Nach Abschluss des Vorgangs sieht die Ausgabe in etwa wie folgt aus:

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="ExpressRoute-Resource Manager-Vorlage: PowerShell-Bereitstellungsausgabe":::

Azure PowerShell wird verwendet, um die Vorlage bereitzustellen. Neben Azure PowerShell können Sie auch das Azure-Portal, die Azure-CLI und die REST-API verwenden. Informationen zu anderen Bereitstellungsmethoden finden Sie unter [Bereitstellen von Vorlagen](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Bereich **Ressourcengruppen** aus.

1. Wählen Sie die Ressourcengruppe aus, die Sie im vorherigen Abschnitt erstellt haben. Der Ressourcengruppenname entspricht standardmäßig dem Projektnamen mit dem Zusatz **rg**.

1. Die Ressourcengruppe sollte die folgenden Ressourcen enthalten:

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="ExpressRoute-Bereitstellungsressourcengruppe":::

1. Wählen Sie die ExpressRoute-Leitung **er-ck01** aus, um zu überprüfen, ob der Leitungsstatus **Aktiviert**, der Anbieterstatus **Nicht bereitgestellt** und der Status für privates Peering **Bereitgestellt** lautet.

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="ExpressRoute-Bereitstellungsleitung":::

> [!NOTE]
> Sie müssen den Anbieter anrufen, um den Bereitstellungsvorgang abzuschließen, damit Sie das virtuelle Netzwerk mit der Leitung verknüpfen können.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die mit der ExpressRoute-Leitung erstellten Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die ExpressRoute-Leitung und alle zugehörigen Ressourcen entfernt.

Rufen Sie zum Löschen der Ressourcengruppe das Cmdlet `Remove-AzResourceGroup` auf:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Folgendes erstellt:

* ExpressRoute-Verbindung
* Virtual Network
* VPN Gateway
* Öffentliche IP-Adresse
* Netzwerksicherheitsgruppen

Informationen zum Verknüpfen eines virtuellen Netzwerks mit einer Leitung finden Sie in den ExpressRoute-Tutorials.

> [!div class="nextstepaction"]
> [ExpressRoute-Tutorials](expressroute-howto-linkvnet-portal-resource-manager.md)
