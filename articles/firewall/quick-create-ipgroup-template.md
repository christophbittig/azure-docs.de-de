---
title: 'Schnellstart: Erstellen einer Azure Firewall-Instanz sowie von IP-Adressgruppen: Resource Manager-Vorlage'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mit einer Azure Resource Manager-Vorlage (ARM-Vorlage) eine Azure Firewall-Instanz und IP-Adressgruppen erstellen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.date: 05/10/2021
ms.author: victorh
ms.openlocfilehash: f74340192cc79db8cf238ea4776dee42edbe51e6
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967987"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---arm-template"></a>Schnellstart: Erstellen einer Azure Firewall-Instanz sowie von IP-Adressgruppen: ARM-Vorlage

In dieser Schnellstartanleitung wird mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) eine Azure Firewall-Instanz mit exemplarischen IP-Adressgruppen erstellt, die in einer Netzwerk- und in einer Anwendungsregel verwendet werden. Eine IP-Adressgruppe ist eine Ressource der obersten Ebene und ermöglicht es, IP-Adressen, Bereiche und Subnetze in einem einzelnen Objekt zu definieren und zu gruppieren. Dies ist bei der Verwaltung von IP-Adressen in Azure Firewall-Regeln hilfreich. IP-Adressen können entweder manuell eingegeben oder aus einer Datei importiert werden.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fazurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Überprüfen der Vorlage

Mit dieser Vorlage werden eine Azure Firewall-Instanz sowie IP-Adressgruppen und die erforderlichen unterstützenden Ressourcen für die Azure Firewall-Instanz erstellt.

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/azurefirewall-create-with-ipgroups-and-linux-jumpbox).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json":::

In der Vorlage sind mehrere Azure-Ressourcen definiert:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Stellen Sie die ARM-Vorlage in Azure bereit:

1. Wählen Sie **Bereitstellung in Azure** aus, um sich bei Azure anzumelden und die Vorlage zu öffnen. Durch die Vorlage werden eine Azure Firewall-Instanz, die Netzwerkinfrastruktur und zwei virtuelle Computer erstellt.

   [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fazurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

2. Geben Sie im Portal auf der Seite **Create an Azure Firewall with IpGroups** (Erstellen einer Azure Firewall-Instanz mit IP-Adressgruppen) die folgenden Werte ein, oder wählen Sie sie aus:
   - Abonnement: Wählen Sie aus den vorhandenen Abonnements aus.
   - Ressourcengruppe:  Wählen Sie aus den vorhandenen Ressourcengruppen aus, oder wählen Sie **Neu erstellen** und anschließend **OK** aus.
   - Standort: Standort auswählen
   - Name des virtuellen Netzwerks: Geben Sie einen Namen für das neue virtuelle Netzwerk (VNET) ein.
   - Name der IP-Adressgruppe 1: Geben Sie einen Namen für die IP-Adressgruppe 1 ein.
   - Name der IP-Adressgruppe 2: Geben Sie einen Namen für die IP-Adressgruppe 2 ein.
   - Administratorbenutzername: Geben Sie den Benutzernamen für das Administratorbenutzerkonto ein.
   - Authentifizierung: Wählen Sie „sshPublicKey“ oder „Kennwort“ aus.
   - Administratorkennwort: Geben Sie ein Administratorkennwort oder einen Administratorschlüssel ein.

3. Wählen Sie **Ich stimme den oben genannten Geschäftsbedingungen zu** und anschließend **Kaufen** aus. Die Bereitstellung kann zehn Minuten oder länger dauern.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Überprüfen Sie im Azure-Portal die bereitgestellten Ressourcen (insbesondere die Firewallregeln mit IP-Adressgruppen).

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="IP-Adressgruppen":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Netzwerkregeln":::

Informationen zur JSON-Syntax und zu den Eigenschaften für eine Firewall in einer Vorlage finden Sie in der Vorlagenreferenz für [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die mit der Firewall erstellten Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Firewall und alle zugehörigen Ressourcen entfernt.

Rufen Sie zum Löschen der Ressourcengruppe das Cmdlet `Remove-AzResourceGroup` auf:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Bereitstellen und Konfigurieren von Azure Firewall in einem Hybridnetzwerk über das Azure-Portal](tutorial-hybrid-portal.md)
