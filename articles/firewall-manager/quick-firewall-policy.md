---
title: 'Schnellstart: Erstellen einer Azure Firewall-Instanz und einer Firewallrichtlinie: Resource Manager-Vorlage'
description: In dieser Schnellstartanleitung werden eine Azure Firewall-Instanz und eine Firewallrichtlinie bereitgestellt.
services: firewall-manager
author: vhorne
ms.author: victorh
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: firewall-manager
ms.custom: devx-track-azurepowershell, subject-armqs, mode-arm
ms.openlocfilehash: 4754b2202b5c36d664ac77e4f5626ac9c88f3681
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057778"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>Schnellstart: Erstellen einer Azure Firewall-Instanz und einer Firewallrichtlinie: ARM-Vorlage

In dieser Schnellstartanleitung werden eine Azure Firewall-Instanz und eine Firewallrichtlinie mithilfe einer ARM-Vorlage (Azure Resource Manager) erstellt. Die Firewallrichtlinie enthält eine Anwendungsregel, die Verbindungen mit `www.microsoft.com` zulässt, sowie eine Regel, die Verbindungen mit Windows Update (unter Verwendung des FQDN-Tags **WindowsUpdate**) zulässt. Eine Netzwerkregel lässt UDP-Verbindungen mit einem Zeitserver unter 13.86.101.172 zu.

Außerdem werden in den Regeln IP-Adressgruppen verwendet, um die IP-Adressen für die **Quelle** zu definieren.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Informationen zu Azure Firewall Manager finden Sie unter [Was ist Azure Firewall Manager?](overview.md).

Informationen zu Azure Firewall finden Sie unter [Was ist Azure Firewall?](../firewall/overview.md).

Informationen zu IP-Adressgruppen finden Sie unter [IP-Adressgruppen in Azure Firewall](../firewall/ip-groups.md).

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fazurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Überprüfen der Vorlage

Mithilfe dieser Vorlage erstellen Sie ein virtuelles Hubnetzwerk sowie die erforderlichen Ressourcen zur Unterstützung des Szenarios.

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

In der Vorlage sind mehrere Azure-Ressourcen definiert:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Stellen Sie die ARM-Vorlage in Azure bereit:

1. Wählen Sie **Bereitstellung in Azure** aus, um sich bei Azure anzumelden und die Vorlage zu öffnen. Mit der Vorlage werden eine Azure Firewall-Instanz, ein virtuelles WAN und ein virtueller Hub, die Netzwerkinfrastruktur sowie zwei virtuelle Computer erstellt.

   [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fazurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. Geben Sie im Portal auf der Seite **Create a Firewall and FirewallPolicy with Rules and Ipgroups** (Erstellen einer Firewall und einer Firewallrichtlinie mit Regeln und IP-Adressgruppen) die folgenden Werte ein, oder wählen Sie sie aus:
   - Abonnement: Wählen Sie aus den vorhandenen Abonnements aus.
   - Ressourcengruppe:  Wählen Sie aus den vorhandenen Ressourcengruppen aus, oder wählen Sie **Neu erstellen** und anschließend **OK** aus.
   - Region: Wählen Sie eine Region aus.
   - Firewallname: Geben Sie einen Namen für die Firewall ein.

3. Wählen Sie **Überprüfen und erstellen** und anschließend **Erstellen** aus. Die Bereitstellung kann zehn Minuten oder länger dauern.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Nach Abschluss der Bereitstellung werden Ressourcen wie die folgenden angezeigt:

:::image type="content" source="media/quick-firewall-policy/qs-deployed-resources.png" alt-text="Bereitgestellte Ressourcen":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die mit der Firewall erstellten Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Firewall und alle zugehörigen Ressourcen entfernt.

Rufen Sie zum Löschen der Ressourcengruppe das Cmdlet `Remove-AzResourceGroup` auf:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Übersicht über die Azure Firewall Manager-Richtlinie](policy-overview.md)
