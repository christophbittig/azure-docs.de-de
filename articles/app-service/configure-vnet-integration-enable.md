---
title: Aktivieren der Integration in das virtuelle Azure-Netzwerk.
description: In diesem Schrittanleitungsartikel werden Sie durch die Aktivierung der Integration virtueller Netzwerke in einer App Service-Web-App geführt.
keywords: VNet-Integration
author: madsd
ms.author: madsd
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: 549c92b932e0d589aec2490512fc18713fa71546
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270556"
---
# <a name="enable-virtual-network-integration-in-azure-app-service"></a>Aktivieren der Integration virtueller Netzwerke in Azure App Service

Durch die Integration in ein virtuelles Azure-Netzwerk (VNet) aus Ihrer [App Service-App](./overview.md) können Sie private Ressourcen aus Ihrer App innerhalb des virtuellen Netzwerks erreichen. Die VNET-Integrationsfunktion weist zwei Variationen auf:

* Regionale VNet-Integration: Verbinden mit virtuellen Azure-Netzwerken in derselben Region. Sie benötigen ein dediziertes Subnetz in dem VNet, in das Sie integrieren möchten.
* VNet-Integration mit erforderlichem Gateway: Wenn Sie eine Verbindung mit VNets in anderen Regionen oder mit einem klassischen virtuellen Netzwerk in derselben Region direkt herstellen, müssen Sie die VNet-Integration mit erforderlichem Gateway verwenden.

In diesem Schrittanleitungsartikel wird beschrieben, wie Sie die regionale VNet-Integration einrichten.

## <a name="prerequisites"></a>Voraussetzungen

Für die VNet-Integration ist Folgendes erforderlich:
- Ein App Service-Tarif, der die [VNet-Integration unterstützt](./overview-vnet-integration.md).
- Ein virtuelles Netzwerk in derselben Region mit einem leeren Subnetz.

Das Subnetz muss an „Microsoft.Web/serverFarms“ delegiert sein. Wenn die Delegierung nicht vor der Integration erfolgt, wird diese Delegierung durch den Bereitstellungsprozess konfiguriert. Dem Subnetz muss ein IPv4-`/28`-Block (16 Adressen) zugeordnet sein. Es wird tatsächlich empfohlen, mindestens 64 Adressen (IPv4-`/26`-Block) vorzuhalten, um eine maximale horizontale Skalierung zu ermöglichen.

## <a name="configure-in-the-azure-portal"></a>Konfigurieren im Azure-Portal

1. Navigieren Sie zur **Netzwerk** benutzeroberfläche im App Service-Portal. Wählen Sie unter **Ausgehender Datenverkehr** die Option **VNet-Integration** aus.

1. Wählen Sie **VNET hinzufügen** aus.

    :::image type="content" source="./media/configure-vnet-integration-enable/vnetint-app.png" alt-text="Auswählen der VNET-Integration":::

1. Die Dropdownliste enthält alle virtuellen Netzwerke in Ihrem Abonnement in derselben Region.

    :::image type="content" source="./media/configure-vnet-integration-enable/vnetint-add-vnet.png" alt-text="Auswählen des VNETs":::

    * Wählen Sie ein leeres, bereits vorhandenes Subnetz aus, oder erstellen Sie ein neues Subnetz.

Während der Integration wird Ihre App neu gestartet. Wenn die Integration abgeschlossen ist, werden Details zu dem VNET angezeigt, in das Sie integriert sind.

## <a name="configure-with-azure-cli"></a>Konfigurieren mit Azure CLI

Sie können die VNet-Integration auch mithilfe der Azure CLI konfigurieren:

```azurecli-interactive
az webapp vnet-integration add --resource-group <group-name> --name <app-name> --vnet <vnet-name> --subnet <subnet-name>
```

> [!NOTE]
> Der Befehl überprüft, ob das Subnetz an „Microsoft.Web/serverFarms“ delegiert ist, und wendet die erforderliche Delegierung an, wenn sie nicht konfiguriert ist. Wenn dies bereits konfiguriert wurde und Sie nicht über Berechtigungen zum Überprüfen verfügen oder sich das virtuelle Netzwerk in einem anderen Abonnement befindet, können Sie den Parameter `--skip-delegation-check` verwenden, um die Überprüfung zu umgehen.

## <a name="configure-with-azure-powershell"></a>Konfigurieren mit Azure PowerShell

```azurepowershell
# Parameters
$siteName = '<app-name>'
$resourceGroupName = '<group-name>'
$vNetName = '<vnet-name>'
$integrationSubnetName = '<subnet-name>'
$subscriptionId = '<subscription-guid>'

# Configure VNet Integration
$subnetResourceId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Network/virtualNetworks/$vNetName/subnets/$integrationSubnetName"
$webApp = Get-AzResource -ResourceType Microsoft.Web/sites -ResourceGroupName $resourceGroupName -ResourceName $siteName
$webApp.Properties.virtualNetworkSubnetId = $subnetResourceId
$webApp | Set-AzResource -Force
```

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren des VNet-Integrationsroutings](./configure-vnet-integration-routing.md)
- [Allgemeine Übersicht über Netzwerke](./networking-features.md)