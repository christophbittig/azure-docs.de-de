---
title: 'Schnellstart: Schützen des virtuellen Hubs mit Azure Firewall Manager mithilfe einer Resource Manager-Vorlage'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Ihren virtuellen Hub mithilfe von Azure Firewall Manager schützen.
services: firewall-manager
author: vhorne
ms.author: victorh
ms.date: 08/28/2020
ms.topic: quickstart
ms.service: firewall-manager
ms.custom: devx-track-azurepowershell, subject-armqs, mode-arm
ms.openlocfilehash: eeb1750613f322c3360d746fd080e40df9e67b12
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068654"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---arm-template"></a>Schnellstart: Schützen Ihres virtuellen Hubs mit Azure Firewall Manager – ARM-Vorlage

In dieser Schnellstartanleitung verwenden Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage), um Ihren virtuellen Hub mithilfe von Azure Firewall Manager zu schützen. Die bereitgestellte Firewall verfügt über eine Anwendungsregel, die Verbindungen mit `www.microsoft.com` zulässt. Zum Testen der Firewall werden zwei virtuelle Computer unter Windows Server 2019 bereitgestellt. Ein Jumpserver wird zum Herstellen einer Verbindung mit dem Workloadserver verwendet. Auf dem Workloadserver können Sie nur eine Verbindung mit `www.microsoft.com` herstellen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Unter [Was ist Azure Firewall Manager?](overview.md) finden Sie weitere Informationen zu Azure Firewall Manager.

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Ffwm-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Überprüfen der Vorlage

Mithilfe dieser Vorlage erstellen Sie einen geschützten virtuellen Hub mit Azure Firewall Manager sowie die erforderlichen Ressourcen zur Unterstützung des Szenarios.

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/fwm-docs-qs/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/fwm-docs-qs/azuredeploy.json":::

In der Vorlage sind mehrere Azure-Ressourcen definiert:

- [**Microsoft.Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Stellen Sie die ARM-Vorlage in Azure bereit:

1. Wählen Sie **Bereitstellung in Azure** aus, um sich bei Azure anzumelden und die Vorlage zu öffnen. Mit der Vorlage werden eine Azure Firewall-Instanz, ein virtuelles WAN und ein virtueller Hub, die Netzwerkinfrastruktur sowie zwei virtuelle Computer erstellt.

   [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Ffwm-docs-qs%2Fazuredeploy.json)

2. Geben Sie im Portal auf der Seite **Geschützte virtuelle Hubs** die folgenden Werte ein, oder wählen Sie diese aus:
   - Abonnement: Wählen Sie aus den vorhandenen Abonnements aus. 
   - Ressourcengruppe:  Wählen Sie aus den vorhandenen Ressourcengruppen aus, oder wählen Sie **Neu erstellen** und anschließend **OK** aus.
   - Standort: Standort auswählen
   - Administratorbenutzername: Geben Sie den Benutzernamen für das Administratorbenutzerkonto ein. 
   - Administratorkennwort: Geben Sie ein Administratorkennwort oder einen Administratorschlüssel ein.

3. Wählen Sie **Überprüfen und erstellen** und anschließend **Erstellen** aus. Die Bereitstellung kann zehn Minuten oder länger dauern.

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

Testen Sie nun die Firewallregeln, um sicherzustellen, dass sie wie erwartet funktionieren.

1. Überprüfen Sie im Azure-Portal die Netzwerkeinstellungen für den virtuellen Computer **Workload-Srv**, und notieren Sie sich die private IP-Adresse.
2. Stellen Sie eine Remotedesktopverbindung mit dem virtuellen Computer **Jump-Srv** her, und melden Sie sich an. Stellen Sie von dort aus eine Remotedesktopverbindung mit der privaten IP-Adresse von **Workload-Srv** her.

3. Navigieren Sie in Internet Explorer zu `www.microsoft.com`.
4. Klicken Sie in den Sicherheitswarnungen von Internet Explorer auf **OK** > **Schließen**.

   Daraufhin sollte die Microsoft-Homepage angezeigt werden.

5. Navigieren Sie zu `www.google.com`.

   Sie sollten durch die Firewall blockiert werden.

Damit haben Sie sich vergewissert, dass die Firewallregeln funktionieren:

- Sie können zum einzigen zulässigen FQDN navigieren, aber nicht zu anderen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die mit der Firewall erstellten Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Firewall und alle zugehörigen Ressourcen entfernt.

Rufen Sie zum Löschen der Ressourcengruppe das Cmdlet `Remove-AzResourceGroup` auf:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu Sicherheitspartneranbietern](trusted-security-partners.md)
