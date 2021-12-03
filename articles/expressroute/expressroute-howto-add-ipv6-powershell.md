---
title: 'Azure ExpressRoute: Hinzufügen von IPv6-Unterstützung mithilfe von Azure PowerShell'
description: Hier erfahren Sie, wie Sie IPv6-Unterstützung zum Herstellen einer Verbindung zu Azure-Bereitstellungen mithilfe von Azure PowerShell hinzufügen können.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 04248f1cf0e0d9ecb659ca3271000a0a28a6a97b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130242310"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell"></a>Hinzufügen von IPv6-Unterstützung für privates Peering mithilfe von Azure PowerShell

In diesem Artikel wird beschrieben, wie Sie IPv6-Unterstützung zum Herstellen einer Verbindung zu Ihren Ressourcen in Azure über ExpressRoute mithilfe von Azure PowerShell hinzufügen können.

## <a name="working-with-azure-powershell"></a>Arbeiten mit Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Hinzufügen von privatem IPv6-Peering zu Ihrer ExpressRoute-Verbindung

1. [Erstellen Sie eine ExpressRoute-Verbindung](./expressroute-howto-circuit-arm.md), oder verwenden Sie eine vorhandene Verbindung. Rufen Sie die Verbindung durch Ausführen des Befehls **Get-AzExpressRouteCircuit** ab:

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Rufen Sie die private Peering-Konfiguration für die Verbindung durch Ausführen von **Get-AzExpressRouteCircuitPeeringConfig** ab:

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. Fügen Sie Ihrer vorhandenen privaten IPv4-Peering-Konfiguration ein privates IPv6-Peering hinzu. Geben Sie zwei /126 IPv6-Subnetze an, die Sie für Ihre primäre bzw. sekundäre Verknüpfung besitzen. Über jedes dieser Subnetze weisen Sie die erste verwendbare IP-Adresse für Ihren Router zu, da die zweite verwendbare IP-Adresse von Microsoft für den eigenen Router genutzt wird.

    > [!Note]
    > Die Peer-ASN und VLAN-ID sollten mit denen in Ihrer privaten IPv4-Peering-Konfiguration identisch sein.

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. Nachdem die Konfiguration erfolgreich gespeichert wurde, stellen Sie die Verbindung durch Ausführen des Befehls **Get-AzExpressRouteCircuit** erneut her. Die Antwort sollte ähnlich wie im folgenden Beispiel aussehen:

    ```azurepowershell
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : eastus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Washington DC",
                                         "BandwidthInMbps": 50
                                       }
    ExpressRoutePort                 : null
    BandwidthInGbps                  :
    Stag                             : 29
    ServiceKey                       : **************************************
    Peerings                         : [
                                         {
                                           "Name": "AzurePrivatePeering",
                                           "Etag": "W/\"facc8972-995c-4861-a18d-9a82aaa7167e\"",
                                           "Id": "/subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                           "PeeringType": "AzurePrivatePeering",
                                           "State": "Enabled",
                                           "AzureASN": 12076,
                                           "PeerASN": 100,
                                           "PrimaryPeerAddressPrefix": "192.168.15.16/30",
                                           "SecondaryPeerAddressPrefix": "192.168.15.20/30",
                                           "PrimaryAzurePort": "",
                                           "SecondaryAzurePort": "",
                                           "VlanId": 200,
                                           "ProvisioningState": "Succeeded",
                                           "GatewayManagerEtag": "",
                                           "LastModifiedBy": "Customer",
                                           "Ipv6PeeringConfig": {
                                             "State": "Enabled",
                                             "PrimaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::/126",
                                             "SecondaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::4/126"
                                           },
                                           "Connections": [],
                                           "PeeredConnections": []
                                         },
                                       ]
    Authorizations                   : []
    AllowClassicOperations           : False
    GatewayManagerEtag               :
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Aktualisieren Ihrer Verbindung zu einem vorhandenen virtuellen Netzwerk

Führen Sie die folgenden Schritte aus, wenn Sie eine vorhandene Umgebung von Azure-Ressourcen haben, bei der Sie Ihr privates IPv6-Peering verwenden möchten.

1. Rufen Sie das virtuelle Netzwerk ab, mit dem Ihre ExpressRoute-Verbindung verknüpft ist.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Fügen Sie Ihrem virtuellen Netzwerk einen IPv6-Adressraum hinzu.

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. Fügen Sie den IPv6-Adressraum Ihrem Gatewaysubnetz hinzu. Das IPv6-Gatewaysubnetz sollte /64 oder größer sein.

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. Wenn Sie über ein vorhandenes zonenredundantes Gateway verfügen, führen Sie Folgendes aus, um die IPv6-Konnektivität zu aktivieren. (Beachten Sie, dass es bis zu einer Stunde dauern kann, bis Änderungen widergespiegelt werden.) [Erstellen Sie das VNET-Gateway](./expressroute-howto-add-gateway-resource-manager.md) andernfalls mit einer beliebigen SKU. Wenn Sie FastPath verwenden möchten, verwenden Sie UltraPerformance oder ErGw3AZ (beachten Sie, dass dies nur für Verbindungen mit ExpressRoute Direct verfügbar ist).

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```
>[!NOTE]
> Wenn Sie über ein vorhandenes Gateway verfügen, das nicht zonenredundant ist (d. h. eine Standard-SKU, Hochleistungs-SKU oder Höchstleistungs-SKU), müssen Sie das Gateway löschen und mit einer beliebigen SKU und einer statischen öffentlichen Standard-IP-Adresse [neu erstellen](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway).

## <a name="create-a-connection-to-a-new-virtual-network"></a>Herstellen einer Verbindung mit einem neuen virtuellen Netzwerk

Führen Sie die folgenden Schritte aus, wenn Sie vorhaben, über Ihr privates IPv6-Peering eine Verbindung mit neuen Azure-Ressourcen herzustellen.

1. Erstellen Sie ein virtuelles Netzwerk mit dualem Stapel mit dem IPv4- und IPv6-Adressraum. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md#create-a-virtual-network).

2. [Erstellen des Gatewaysubnetzes mit dualem Stapel](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway)

3. [Erstellen Sie das VNET-Gateway](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway) mit einer beliebigen SKU. Wenn Sie FastPath verwenden möchten, verwenden Sie UltraPerformance oder ErGw3AZ (beachten Sie, dass dies nur für Verbindungen mit ExpressRoute Direct verfügbar ist).

4. [Verknüpfen Sie Ihr virtuelles Netzwerks mit Ihrer ExpressRoute-Verbindung.](./expressroute-howto-linkvnet-arm.md)

## <a name="limitations"></a>Einschränkungen
Obwohl IPv6-Unterstützung für Verbindungen mit Bereitstellungen in öffentlichen Azure-Regionen zur Verfügung steht, werden die folgenden Anwendungsfälle nicht unterstützt:

* Verbindungen zu *bestehenden* ExpressRoute-Gateways, die nicht zonenredundant sind. Beachten Sie, dass *neu* erstellte ExpressRoute-Gateways jeder SKU (sowohl zonenredundant als auch nicht) mit einer statischen Standard-IP-Adresse für Dual-Stack-ExpressRoute-Verbindungen verwendet werden können.
* Global Reach-Verbindungen zwischen ExpressRoute-Verbindungen
* Verwendung von ExpressRoute mit virtuellem WAN
* FastPath mit Verbindungen, die nicht ExpressRoute Direct-Verbindungen sind
* FastPath mit Verbindungen an den folgenden Peeringstandorten: Dubai
* Koexistenz mit VPN Gateway

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Problembehandlung bei ExpressRoute-Verbindungen finden Sie in den folgenden Artikeln:

* [Überprüfen der ExpressRoute-Konnektivität](expressroute-troubleshooting-expressroute-overview.md)
* [Beheben von Problemen bei der Netzwerkleistung](expressroute-troubleshooting-network-performance.md)
