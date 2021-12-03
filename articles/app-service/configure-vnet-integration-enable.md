---
title: Aktivieren der Integration in ein virtuelles Azure-Netzwerk
description: In diesem Schrittanleitungsartikel werden Sie durch die Aktivierung der Integration virtueller Netzwerke in eine Azure App Service-Web-App geführt.
keywords: VNet-Integration
author: madsd
ms.author: madsd
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: 73a148f205a36091f1df3b35a2d89ac51bf352ef
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894277"
---
# <a name="enable-virtual-network-integration-in-azure-app-service"></a>Aktivieren der Integration virtueller Netzwerke in Azure App Service

Durch die Integration in ein virtuelles Azure-Netzwerk (VNet) aus Ihrer [App Service-App](./overview.md) können Sie aus Ihrer App heraus private Ressourcen innerhalb des virtuellen Netzwerks erreichen. Das Feature für die VNet-Integration umfasst zwei Varianten:

* **Integration regionaler virtueller Netzwerke**: Verbinden mit virtuellen Azure-Netzwerken in derselben Region. Sie benötigen ein dediziertes Subnetz in dem virtuellen Netzwerk, in das Sie integrieren möchten.
* **Integration virtueller Netzwerke mit erforderlichem Gateway**: Wenn Sie eine Verbindung mit einem virtuellen Netzerk in anderen Regionen oder mit einem klassischen virtuellen Netzwerk in derselben Region direkt herstellen, müssen Sie die Integration virtueller Netzwerke mit erforderlichem Gateway verwenden.

In diesem Artikel wird beschrieben, wie Sie die Integration regionaler virtueller Netzwerke einrichten.

## <a name="prerequisites"></a>Voraussetzungen

Folgendes ist für die VNet-Integrationsfunktion erforderlich:

- Ein App Service-Tarif, der die [Integration virtueller Netzwerke unterstützt](./overview-vnet-integration.md).
- Ein virtuelles Netzwerk in derselben Region mit einem leeren Subnetz.

Das Subnetz muss an „Microsoft.Web/serverFarms“ delegiert sein. Wenn die Delegierung nicht vor der Integration erfolgt, wird diese Delegierung durch den Bereitstellungsprozess konfiguriert. Dem Subnetz muss ein IPv4-`/28`-Block (16 Adressen) zugeordnet sein. Es wird empfohlen, mindestens 64 Adressen (IPv4-`/26`-Block) vorzuhalten, um eine maximale horizontale Skalierung zu ermöglichen.

## <a name="configure-in-the-azure-portal"></a>Konfigurieren im Azure-Portal

1. Wechseln Sie im App Service-Portal zu **Netzwerk**. Wählen Sie unter **Ausgehender Datenverkehr** die Option **VNet-Integration** aus.

1. Wählen Sie **VNET hinzufügen** aus.

    :::image type="content" source="./media/configure-vnet-integration-enable/vnetint-app.png" alt-text="Screenshot des Auswählens der VNet-Integration.":::

1. Die Dropdownliste enthält alle virtuellen Netzwerke in Ihrem Abonnement in derselben Region. Wählen Sie ein leeres, bereits vorhandenes Subnetz aus, oder erstellen Sie ein neues Subnetz.

    :::image type="content" source="./media/configure-vnet-integration-enable/vnetint-add-vnet.png" alt-text="Screenshot des Auswählens des virtuellen Netzwerks.":::

Während der Integration wird Ihre App neu gestartet. Wenn die Integration abgeschlossen ist, werden Details zu dem virtuellen Netzwerk angezeigt, in das Sie integriert sind.

## <a name="configure-with-the-azure-cli"></a>Konfigurieren mit der Azure CLI

Sie können die Integration virtueller Netzwerke auch mithilfe der Azure CLI konfigurieren:

```azurecli-interactive
az webapp vnet-integration add --resource-group <group-name> --name <app-name> --vnet <vnet-name> --subnet <subnet-name>
```

> [!NOTE]
> Der Befehl überprüft, ob das Subnetz an „Microsoft.Web/serverFarms“ delegiert ist, und wendet die erforderliche Delegierung an, wenn sie nicht konfiguriert ist. Wenn das Subnetz bereits konfiguriert war und Sie nicht über Berechtigungen zu dessen Überprüfung verfügen, oder wenn sich das virtuelle Netzwerk in einem anderen Abonnement befindet, können Sie den Parameter *--skip-delegation-check* verwenden, um die Überprüfung zu umgehen.

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

- [Konfigurieren des Routings für die Integration für virtuelle Netzwerke](./configure-vnet-integration-routing.md)
- [Allgemeine Übersicht über Netzwerke](./networking-features.md)