---
title: 'Azure ExpressRoute: Hinzufügen von IPv6-Unterstützung mithilfe der Azure CLI'
description: Hier erfahren Sie, wie Sie IPv6-Unterstützung zum Herstellen einer Verbindung zu Azure-Bereitstellungen mithilfe der Azure CLI hinzufügen können.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/27/2021
ms.author: duau
ms.openlocfilehash: 9341d61229ed5b28eed838271fd0108c1a45d8b5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228950"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-cli"></a>Hinzufügen der IPv6-Unterstützung für privates Peering mithilfe der Azure CLI

In diesem Artikel wird beschrieben, wie Sie IPv6-Unterstützung zum Herstellen einer Verbindung zu Ihren Ressourcen in Azure über ExpressRoute mithilfe der Azure CLI hinzufügen können.

## <a name="prerequisites"></a>Voraussetzungen

* Lesen Sie vor Beginn der Konfiguration die Seiten zu den [Voraussetzungen](expressroute-prerequisites.md) und [Workflows](expressroute-workflows.md).
* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installieren Sie die aktuelle Version der CLI-Befehle (2.0 oder höher). Informationen zum Installieren der CLI-Befehle finden Sie unter [Installieren von Azure CLI 2.0](/cli/azure/install-azure-cli) und [Erste Schritte mit Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Hinzufügen von privatem IPv6-Peering zu Ihrer ExpressRoute-Verbindung

1. [Erstellen Sie eine ExpressRoute-Verbindung](howto-circuit-cli.md), oder verwenden Sie eine vorhandene Verbindung. Zeigen Sie die Details der Verbindung an, indem Sie Folgendes ausführen:

    ```azurecli-interactive
    az network express-route show --resource-group "<ExpressRouteResourceGroup>" --name "<MyCircuit>"
    ```

2. Zeigen Sie die private Peeringkonfiguration für die Leitung an, indem Sie Folgendes ausführen:

    ```azurecli-interactive
    az network express-route peering show -g "<ExpressRouteResourceGroup>" --circuit-name "<MyCircuit>" --name AzurePrivatePeering
    ```

3. Fügen Sie Ihrer vorhandenen privaten IPv4-Peering-Konfiguration ein privates IPv6-Peering hinzu. Geben Sie zwei /126 IPv6-Subnetze an, die Sie für Ihre primäre bzw. sekundäre Verknüpfung besitzen. Über jedes dieser Subnetze weisen Sie die erste verwendbare IP-Adresse für Ihren Router zu, da die zweite verwendbare IP-Adresse von Microsoft für den eigenen Router genutzt wird.

    ```azurecli-interactive
    az network express-route peering update -g "<ExpressRouteResourceGroup>" --circuit-name "<MyCircuit>" --name AzurePrivatePeering --ip-version ipv6 --primary-peer-subnet "<X:X:X:X/126>" --secondary-peer-subnet "<Y:Y:Y:Y/126>"
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Aktualisieren Ihrer Verbindung zu einem vorhandenen virtuellen Netzwerk

Führen Sie die folgenden Schritte aus, wenn Sie eine vorhandene Umgebung von Azure-Ressourcen haben, bei der Sie Ihr privates IPv6-Peering verwenden möchten.

1. Fügen Sie einen IPv6-Adressraum zu dem virtuellen Netzwerk hinzu, mit dem Ihre ExpressRoute-Leitung verknüpft ist.

    ```azurecli-interactive
    az network vnet update -g "<MyResourceGroup>" -n "<MyVNet>" --address-prefixes "X:X:X:X::/64"
    ```

3. Fügen Sie den IPv6-Adressraum Ihrem Gatewaysubnetz hinzu. Das IPv6-Gatewaysubnetz sollte /64 oder größer sein.

    ```azurecli-interactive
    az network vnet subnet update -g "<MyResourceGroup>" -n "<MySubnet>" -vnet-name "<MyVNet>" --address-prefixes "10.0.0.0/26", "X:X:X:X::/64"
    ```

4. Wenn Sie über ein vorhandenes zonenredundantes Gateway verfügen, führen Sie Folgendes aus, um die IPv6-Konnektivität zu aktivieren. (Beachten Sie, dass es bis zu einer Stunde dauern kann, bis Änderungen widergespiegelt werden.) [Erstellen Sie das VNET-Gateway](expressroute-howto-add-gateway-resource-manager.md) andernfalls mit einer beliebigen SKU. Wenn Sie FastPath verwenden möchten, verwenden Sie UltraPerformance oder ErGw3AZ (beachten Sie, dass dies nur für Verbindungen mit ExpressRoute Direct verfügbar ist).

    ```azurecli-interactive
    az network vnet-gateway update --name "<GatewayName>" --resource-group "<MyResourceGroup>"
    ```
>[!NOTE]
> Wenn Sie über ein vorhandenes Gateway verfügen, das nicht zonenredundant ist (d. h. eine Standard-SKU, Hochleistungs-SKU oder Höchstleistungs-SKU) und eine öffentliche IP-Adresse der Basic-SKU verwendet, müssen Sie das Gateway löschen und mit einer beliebigen SKU und einer statischen öffentlichen Standard-IP-Adresse [neu erstellen](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway).

## <a name="create-a-connection-to-a-new-virtual-network"></a>Herstellen einer Verbindung mit einem neuen virtuellen Netzwerk

Führen Sie die folgenden Schritte aus, wenn Sie vorhaben, über Ihr privates IPv6-Peering eine Verbindung mit neuen Azure-Ressourcen herzustellen.

1. Erstellen Sie ein virtuelles Netzwerk mit dualem Stapel mit dem IPv4- und IPv6-Adressraum. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-cli.md).

2. [Erstellen des Gatewaysubnetzes mit dualem Stapel](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway)

3. [Erstellen Sie das VNET-Gateway](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway) mit einer beliebigen SKU. Wenn Sie FastPath verwenden möchten, verwenden Sie UltraPerformance oder ErGw3AZ (beachten Sie, dass dies nur für Verbindungen mit ExpressRoute Direct verfügbar ist).

4. [Verknüpfen Sie Ihr virtuelles Netzwerks mit Ihrer ExpressRoute-Verbindung.](howto-linkvnet-cli.md)

## <a name="limitations"></a>Einschränkungen
Obwohl IPv6-Unterstützung für Verbindungen mit Bereitstellungen in globalen Azure-Regionen zur Verfügung steht, werden die folgenden Anwendungsfälle nicht unterstützt:

* Verbindungen mit *vorhandenen* ExpressRoute-Gateways, die nicht zonenredundant sind. Beachten Sie, dass *neu* erstellte ExpressRoute-Gateways jeder SKU (sowohl zonenredundant als auch nicht) mit einer statischen Standard-IP-Adresse für Dual-Stack-ExpressRoute-Verbindungen verwendet werden können.
* Global Reach-Verbindungen zwischen ExpressRoute-Verbindungen
* Verwendung von ExpressRoute mit virtuellem WAN
* FastPath mit Verbindungen, die nicht ExpressRoute Direct-Verbindungen sind
* FastPath mit Verbindungen an den folgenden Peeringstandorten: Dubai
* Koexistenz mit VPN Gateway

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Problembehandlung bei ExpressRoute-Verbindungen finden Sie in den folgenden Artikeln:

* [Überprüfen der ExpressRoute-Konnektivität](expressroute-troubleshooting-expressroute-overview.md)
* [Beheben von Problemen bei der Netzwerkleistung](expressroute-troubleshooting-network-performance.md)
