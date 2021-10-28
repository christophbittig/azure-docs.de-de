---
title: 'Tutorial: Azure ExpressRoute: Hinzufügen eines Gateways zu einem virtuellen Netzwerk – Azure PowerShell'
description: In diesem Tutorial wird beschrieben, wie Sie einem bereits erstellten virtuellen Resource Manager-Netzwerk für ExpressRoute mithilfe von Azure PowerShell ein Gateway für virtuelle Netzwerke hinzufügen.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: b69ed57ec67b7f08ef7beec8df73f1da20c06a0b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254879"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Tutorial: Konfigurieren eines Gateways für ein virtuelles Netzwerk für ExpressRoute mit PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager – Azure-Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassisch – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure-Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

In diesem Tutorial wird beschrieben, wie Sie ein Gateway für virtuelle Netzwerke für ein vorhandenes virtuelles Netzwerk (VNet) hinzufügen und entfernen bzw. dessen Größe ändern. Die Schritte für diese Konfiguration gelten für VNETs, die mit dem Resource Manager-Bereitstellungsmodell für eine ExpressRoute-Konfiguration erstellt wurden. Weitere Informationen finden Sie unter [Informationen zu Gateways für virtuelle Netzwerke für ExpressRoute](expressroute-about-virtual-network-gateways.md).

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:
> [!div class="checklist"]
> - Erstellen Sie ein Gatewaysubnetz.
> - Erstellen eines Gateways für virtuelle Netzwerke

## <a name="prerequisites"></a>Voraussetzungen

### <a name="configuration-reference-list"></a>Referenzliste für Konfiguration

Bei den Schritten für diese Aufgabe wird ein VNet basierend auf den Werten verwendet, die in der folgenden Referenzliste für die Konfiguration enthalten sind. Zusätzliche Einstellungen und Namen werden ebenfalls in dieser Liste beschrieben. Wir verwenden diese Liste nicht direkt in einem der Schritte, obwohl wir Variablen basierend auf den Werten in dieser Liste hinzufügen. Sie können diese Liste als Referenz verwenden und die Werte durch Ihre eigenen Werte ersetzen.

| Einstellung                   | Wert                                              |
| ---                       | ---                                                |
| Name des virtuellen Netzwerks | *TestVNet* |    
| Adressraum des virtuellen Netzwerks | *192.168.0.0/16* |
| Ressourcengruppe | *TestRG* |
| Name von Subnet1 | *FrontEnd* |   
| Subnet1-Adressraum | *192.168.1.0/24* |
| Name von Subnet1 | *FrontEnd* |
| Name des Gatewaysubnetzes | *GatewaySubnet* |    
| Adressraum des Gatewaysubnetzes | *192.168.200.0/26* |
| Region | *USA, Osten* |
| Gatewayname | *GW* |   
| Name der Gateway-IP | *GWIP* |
| Name der Gateway-IP-Konfiguration | *gwipconf* |
| type | *ExpressRoute* |
| Name der öffentlichen Gateway-IP  | *gwpip* |

## <a name="add-a-gateway"></a>Hinzufügen eines Gateways

1. Führen Sie `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

1. Definieren Sie Ihre Variablen für diese Übung. Achten Sie darauf, das Beispiel so zu bearbeiten, dass es den gewünschten Einstellungen entspricht.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
1. Speichern Sie das virtuelle Netzwerkobjekt als Variable.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
1. Fügen Sie Ihrem virtuellen Netzwerk ein Gatewaysubnetz hinzu. Das Gatewaysubnetz muss den Namen GatewaySubnet tragen. Das Gatewaysubnetz muss /27 oder größer sein (/26, /25 usw.). Wenn Sie 16 ExpressRoute-Leitungen mit Ihrem Gateway verbinden möchten, **müssen** Sie ein Gatewaysubnetz mit einer Größe von mindestens /26 erstellen.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
    Wenn Sie ein virtuelles Dual-Stack-Netzwerk verwenden und beabsichtigen, IPv6-basiertes privates Peering über ExpressRoute einzusetzen, sollten Sie stattdessen ein Dual-Stack-Gatewaysubnetz erstellen.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26","ace:daa:daaa:deaa::/64"
   ```
1. Legen Sie die Konfiguration fest.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
1. Speichern Sie das Gatewaysubnetz als Variable.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
1. Fordern Sie eine öffentliche IP-Adresse an. Die IP-Adresse wird angefordert, bevor das Gateway erstellt wird. Sie können die IP-Adresse, die Sie verwenden möchten, nicht selbst angeben. Sie wird dynamisch zugewiesen. Diese IP-Adresse wird im nächsten Konfigurationsabschnitt verwendet. Die AllocationMethod muss dynamisch sein.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
      
   Wenn Sie IPv6-basiertes privates Peering über ExpressRoute verwenden möchten, legen Sie die IP-SKU auf „Standard“ und die AllocationMethod auf „Statisch“ fest:
   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Static -SKU Standard
   ```
   
1. Erstellen Sie die Konfiguration für Ihr Gateway. Die Gatewaykonfiguration definiert das zu verwendende Subnetz und die zu verwendende öffentliche IP-Adresse. In diesem Schritt geben Sie die Konfiguration an, die beim Erstellen des Gateways verwendet wird. Verwenden Sie das folgende Beispiel, um Ihre Gatewaykonfiguration zu erstellen.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
1. Erstellen Sie das Gateway. In diesem Schritt ist **-GatewayType** besonders wichtig. Sie müssen den Wert **ExpressRoute** verwenden. Das Erstellen des Gateways kann nach der Ausführung dieser Cmdlets 45 Minuten oder länger dauern.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```
> [!IMPORTANT]
> Wenn Sie IPv6-basiertes privates Peering über ExpressRoute verwenden möchten, stellen Sie sicher, dass Sie eine AZ-SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) als **-GatewaySku** auswählen oder die Nicht-AZ-SKU (Standard, HighPerformance, UltraPerformance) als -GatewaySKU mit Standard-IP und statischer öffentlicher IP verwenden.
> 
> 

## <a name="verify-the-gateway-was-created"></a>Erstellung des Gateways überprüfen
Verwenden Sie die folgenden Befehle, um zu überprüfen, ob das Gateway erstellt wurde:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Ändern der Größe eines Gateways
Es gibt eine Reihe von [Gateway-SKUs](expressroute-about-virtual-network-gateways.md). Sie können jederzeit den folgenden Befehl verwenden, um die Gateway-SKU zu ändern.

> [!IMPORTANT]
> Dieser Befehl kann nicht für das UltraPerformance-Gateway verwendet werden. Wenn Sie das Gateway in ein UltraPerformance-Gateway ändern möchten, entfernen Sie zunächst das vorhandene ExpressRoute-Gateway, und erstellen Sie anschließend ein neues UltraPerformance-Gateway. Wenn Sie das Gateway von einem UltraPerformance-Gateway herabstufen möchten, entfernen Sie zunächst das UltraPerformance-Gateway, und erstellen Sie anschließend ein neues Gateway.
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Verwenden Sie den folgenden Befehl zum Entfernen des Gateways:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie das VNET-Gateway erstellt haben, können Sie Ihr VNET mit einer ExpressRoute-Leitung verknüpfen. 

> [!div class="nextstepaction"]
> [Verbinden eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung mithilfe des Portals](expressroute-howto-linkvnet-arm.md)
