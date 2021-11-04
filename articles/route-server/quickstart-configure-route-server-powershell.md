---
title: 'Schnellstart: Erstellen und Konfigurieren einer Route Server-Instanz mithilfe von Azure PowerShell'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Azure PowerShell eine Route Server-Instanz erstellen und konfigurieren.
services: route-server
author: duongau
ms.author: duau
ms.date: 09/01/2021
ms.topic: quickstart
ms.service: route-server
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: a9467e2c876cdb324a85c9be3f42cd3867a24709
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131023593"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>Schnellstart: Erstellen und Konfigurieren einer Route Server-Instanz mithilfe von Azure PowerShell

In diesem Artikel wird beschrieben, wie Sie Azure Route Server mithilfe von Azure PowerShell für das Peering mit einem virtuellen Netzwerkgerät (Network Virtual Appliance, NVA) in Ihrem virtuellen Netzwerk konfigurieren. Route Server lernt Routen von Ihrem NVA und programmiert sie auf den VMs im virtuellen Netzwerk. Azure Route Server kündigt die virtuellen Netzwerkrouten auch für das NVA an. Weitere Informationen finden Sie unter [Azure Route Server](overview.md).

:::image type="content" source="media/quickstart-configure-route-server-portal/environment-diagram.png" alt-text="Diagramm: Route Server-Bereitstellungsumgebung mit Azure PowerShell" border="false":::

> [!IMPORTANT]
> Wenn Sie vor dem 1. September eine Azure Route Server-Instanz erstellt haben und ihr keine öffentliche IP-Adresse zugeordnet ist, müssen Sie die Route Server-Instanz neu erstellen, damit sie zu Verwaltungszwecken eine IP-Adresse abrufen kann.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Stellen Sie sicher, dass Sie über die aktuellen PowerShell-Module verfügen, oder verwenden Sie Azure Cloud Shell im Portal.
* Beachten Sie die [Diensteinschränkungen für Azure Route Server](route-server-faq.md#limitations).
* Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-resource-group-and-a-virtual-network"></a>Erstellen einer Ressourcengruppe und eines virtuellen Netzwerks

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe 

Bevor Sie einen Azure Route Server erstellen können, müssen Sie eine Ressourcengruppe zum Hosten der Route Server-Instanz erstellen. Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) eine Ressourcengruppe. In diesem Beispiel wird eine Ressourcengruppe namens **myRouteServerRG** am Standort **WestUS** erstellt:

```azurepowershell-interactive
$rg = @{
    Name = 'myRouteServerRG'
    Location = 'WestUS'
}
New-AzResourceGroup @rg
```

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) ein virtuelles Netzwerk. In diesem Beispiel wird ein virtuelles Standardnetzwerk mit dem Namen **myVirtualNetwork** am Standort **WestUS** erstellt: Wenn Sie bereits über ein virtuelles Netzwerk verfügen, können Sie zum nächsten Abschnitt wechseln.

```azurepowershell-interactive
$vnet = @{
    Name = 'myVirtualNetwork'
    ResourceGroupName = 'myRouteServerRG'
    Location = 'WestUS'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetwork = New-AzVirtualNetwork @vnet
```

### <a name="add-a-dedicated-subnet"></a>Hinzufügen eines dedizierten Subnetzes

Azure Route Server erfordert ein dediziertes Subnetz namens *RouteServerSubnet*. Das Subnetz muss die Größe /27 oder ein kürzeres Präfix (z. B. /26 oder /25) aufweisen. Andernfalls erhalten Sie eine Fehlermeldung, wenn Sie die Route Server-Instanz hinzufügen. Erstellen Sie mit [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) eine Subnetzkonfiguration namens **RouteServerSubnet**:

```azurepowershell-interactive
$subnet = @{
    Name = 'RouteServerSubnet'
    VirtualNetwork = $virtualNetwork
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = Add-AzVirtualNetworkSubnetConfig @subnet

$virtualnetwork | Set-AzVirtualNetwork
```

## <a name="create-the-route-server"></a>Erstellen der Route Server-Instanz

1. Um die Konnektivität mit dem Back-End-Dienst sicherzustellen, der die Route Server-Konfiguration verwaltet, ist das Zuweisen einer öffentlichen IP-Adresse erforderlich. Erstellen Sie mit [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) eine öffentliche Standard-IP-Adresse namens **RouteServerIP**:

    ```azurepowershell-interactive
    $ip = @{
        Name = 'myRouteServerIP'
        ResourceGroupName = 'myRouteServerRG'
        Location = 'WestUS'
        AllocationMethod = 'Static'
        IpAddressVersion = 'Ipv4'
        Sku = 'Standard'
    }
    $publicIp = New-AzPublicIpAddress @ip
    ```
    
2. Erstellen Sie mit [New-AzRouteServer](/powershell/module/az.network/new-azrouteserver) die Azure Route Server-Instanz. In diesem Beispiel wird eine Azure Route Server-Instanz namens **myRouteServer** am Standort **WestUS** erstellt. Bei *HostedSubnet* handelt es sich um die Ressourcen-ID des im vorherigen Abschnitt erstellten Subnetzes „RouteServerSubnet“.

    ```azurepowershell-interactive
    $rs = @{
        RouteServerName = 'myRouteServer'
        ResourceGroupName = 'myRouteServerRG'
        Location = 'WestUS'
        HostedSubnet = $subnetConfig.Id
        PublicIP = $publicIp
    }
    New-AzRouteServer @rs 
    ```

## <a name="create-bgp-peering-with-an-nva"></a>Erstellen eines BGP-Peerings mit einem NVA

Verwenden Sie [Add-AzRouteServerPeer](/powershell/module/az.network/add-azrouteserverpeer), um das BGP-Peering zwischen der Route Server-Instanz und Ihrem NVA einzurichten:

„your_nva_ip“ ist die dem NVA zugewiesene IP-Adresse des virtuellen Netzwerks. „your_nva_asn“ ist die im NVA konfigurierte ASN (Autonomous System Number, autonome Systemnummer). Die ASN kann eine beliebige 16-Bit-Zahl sein, die nicht im Bereich von 65515-65520 liegt. Die ASNs in diesem Bereich sind von Microsoft reserviert.

```azurepowershell-interactive
$peer = @{
    PeerName = 'myNVA"
    PeerIp = '192.168.0.1'
    PeerAsn = '65501'
    RouteServerName = 'myRouteServer'
    ResourceGroupName = myRouteServerRG'
}
Add-AzRouteServerPeer @peer
```

Wenn Sie das Peering zur Bereitstellung von Redundanz mit einem anderen NVA oder einer anderen Instanz desselben NVA einrichten möchten, verwenden Sie den gleichen Befehl wie oben. Geben Sie jedoch andere Werte für *PeerName*, *PeerIp* und *PeerAsn* an.

## <a name="complete-the-configuration-on-the-nva"></a>Abschließen der Konfiguration auf dem NVA

Um die Konfiguration auf dem NVA abzuschließen und die BGP-Sitzungen zu aktivieren, benötigen Sie die IP-Adresse und die ASN von Azure Route Server. Diese Informationen können über [Get-AzRouteServer](/powershell/module/az.network/get-azrouteserver) abgerufen werden:

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
} 
Get-AzRouteServer @routeserver
```

Die Ausgabe sieht dann wie folgt aus:

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>Konfigurieren des Routenaustauschs

Wenn Sie über ein ExpressRoute- und Azure VPN-Gateway in demselben virtuellen Netzwerk verfügen und diese die Routen austauschen sollen, können Sie den Routenaustausch für die Azure Route Server-Instanz aktivieren.

1. Um den Routenaustausch zwischen Azure Route Server und den Gateways zu aktivieren, verwenden Sie [Update-AzRouteServer](/powershell/module/az.network/update-azrouteserver) mit dem Flag *-AllowBranchToBranchTraffic*:

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
    AllowBranchToBranchTraffic
}  
Update-AzRouteServer @routeserver 
```

2. Um den Routenaustausch zwischen Azure Route Server und den Gateways zu deaktivieren, verwenden Sie [Update-AzRouteServer](/powershell/module/az.network/update-azrouteserver) ohne das Flag *-AllowBranchToBranchTraffic*:

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
}  
Update-AzRouteServer @routeserver 
```

## <a name="troubleshooting"></a>Problembehandlung

Verwenden Sie [Get-AzRouteServerPeerAdvertisedRoute](/powershell/module/az.network/get-azrouteserverpeeradvertisedroute), um die von Azure Route Server angekündigte Routen anzuzeigen.

```azurepowershell-interactive
$remotepeer = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
    PeerName = 'myNVA'
}
Get-AzRouteServerPeerAdvertisedRoute @routeserver
```

Über [Get-AzRouteServerPeerLearnedRoute](/powershell/module/az.network/get-azrouteserverpeerlearnedroute) können Sie die von Azure Route Server gelernten Routen anzeigen.

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
    AllowBranchToBranchTraffic
}  
Get-AzRouteServerPeerLearnedRoute @routeserver
```
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure Route Server-Instanz nicht mehr benötigen, verwenden Sie den ersten Befehl, um das BGP-Peering zu entfernen, und dann den zweiten Befehl, um die Route Server-Instanz zu entfernen. 

1. Entfernen Sie das BGP-Peering zwischen Azure Route Server und einem NVA mit [Remove-AzRouteServerPeer](/powershell/module/az.network/remove-azrouteserverpeer):

```azurepowershell-interactive
$peer = @{
    PeerName = 'myNVA'
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
} 
Remove-AzRouteServerPeer @peer
```

2. Entfernen Sie die Azure Route Server-Instanz mit [Remove-AzRouteServer](/powershell/module/az.network/remove-azrouteserver):

```azurepowershell-interactive 
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
} 
Remove-AzRouteServer @routeserver
```

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich nach dem Erstellen der Azure Route Server-Instanz darüber, wie Azure Route Server mit ExpressRoute- und VPN-Gateways interagiert: 

> [!div class="nextstepaction"]
> [Unterstützung von Azure ExpressRoute und Azure VPN](expressroute-vpn-support.md)
