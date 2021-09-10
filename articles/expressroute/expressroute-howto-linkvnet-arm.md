---
title: 'Tutorial: Verknüpfen eines virtuellen Netzwerks mit einer ExpressRoute-Leitung: Azure PowerShell'
description: Dieses Tutorial bietet eine Übersicht über das Verknüpfen virtueller Netzwerke (VNETs) mit ExpressRoute-Leitungen über das Resource Manager-Bereitstellungsmodell und Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 08/10/2021
ms.author: duau
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 0c93edf2d7d017ea857e4e8ac915818a3b7f1d02
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123311375"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit"></a>Tutorial: Verbinden eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung
> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure-Befehlszeilenschnittstelle](howto-linkvnet-cli.md)
> * [Video – Azure-Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisch)](expressroute-howto-linkvnet-classic.md)
>

Dieser Artikel unterstützt Sie beim Verknüpfen virtueller Netzwerke (VNETs) mit Azure ExpressRoute-Verbindungen über das Resource Manager-Bereitstellungsmodell und PowerShell. Virtuelle Netzwerke können Teil desselben Abonnements sein oder zu einem anderen Abonnement gehören. In diesem Artikel erfahren Sie auch, wie eine Verknüpfung mit einem virtuellen Netzwerk aktualisiert wird.

* Sie können bis zu zehn virtuelle Netzwerke mit einer ExpressRoute-Standardverbindung verknüpfen. Alle virtuellen Netzwerke müssen sich in der gleichen geopolitischen Region befinden, wenn Sie eine ExpressRoute-Standardverbindung verwenden. 

* Ein einzelnes VNet kann mit bis zu 16 ExpressRoute-Leitungen verknüpft werden. Erstellen Sie anhand der Schritte in diesem Artikel ein neues Verbindungsobjekt für jede ExpressRoute-Leitung, mit der Sie eine Verbindung herstellen. Die ExpressRoute-Leitungen können sich im gleichen Abonnement, in verschiedenen Abonnements oder in einer Kombination aus beidem befinden.

* Wenn Sie das ExpressRoute-Premium-Add-On aktivieren, können Sie virtuelle Netzwerke außerhalb der geopolitischen Region der ExpressRoute-Leitung verknüpfen. Über das Premium-Add-On können Sie – je nach ausgewählter Bandbreite – auch mehr als 10 virtuelle Netzwerke mit Ihrer ExpressRoute-Leitung verbinden. Weitere Informationen zum Premium-Add-On finden Sie in den [häufig gestellten Fragen](expressroute-faqs.md) .

* Um die Verbindung von der ExpressRoute-Verbindung zum Zielgateway des virtuellen ExpressRoute-Netzwerks herzustellen, muss die Anzahl der von den lokalen oder virtuellen Netzwerken mit Peering angekündigten Adressräume gleich oder kleiner als **200** sein. Nachdem die Verbindung erfolgreich erstellt wurde, können Sie den lokalen oder virtuellen Netzwerken mit Peering zusätzliche Adressräume hinzufügen (bis zu 1.000).

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:
> [!div class="checklist"]
> - Herstellen einer Verbindung zwischen einem virtuellen Netzwerk in demselben Abonnement und einer Verbindung
> - Herstellen einer Verbindung zwischen einem virtuellen Netzwerk in einem anderen Abonnement und einer Verbindung
> - Ändern einer Verbindung mit einem virtuellen Netzwerk
> - Konfigurieren von ExpressRoute FastPath

## <a name="prerequisites"></a>Voraussetzungen

* Lesen Sie, bevor Sie mit der Konfiguration beginnen, die Seiten [Voraussetzungen](expressroute-prerequisites.md), [Routinganforderungen](expressroute-routing.md) und [Workflows](expressroute-workflows.md).

* Sie benötigen eine aktive ExpressRoute-Verbindung. 
  * Führen Sie die Schritte zum [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-arm.md) aus, und lassen Sie sie vom Konnektivitätsanbieter aktivieren. 
  * Stellen Sie sicher, dass privates Azure-Peering für die Verbindung konfiguriert ist. Informationen zum Routing finden Sie unter [Konfigurieren des Routings](expressroute-howto-routing-arm.md) . 
  * Stellen Sie sicher, dass das private Azure-Peering konfiguriert wird und das BGP-Peering zwischen Ihrem Netzwerk und Microsoft einrichtet, damit End-to-End-Konnektivität bereitgestellt werden kann.
  * Vergewissern Sie sich, dass ein virtuelles Netzwerk und ein virtuelles Netzwerkgateway erstellt und vollständig bereitgestellt wurden. Befolgen Sie die Anweisungen zum [Erstellen eines virtuellen Netzwerkgateways für ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Für ein virtuelles Netzwerkgateway für ExpressRoute wird der Gatewaytyp „ExpressRoute“ und nicht „VPN“ verwendet.

### <a name="working-with-azure-powershell"></a>Arbeiten mit Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Herstellen einer Verbindung zwischen einem virtuellen Netzwerk in demselben Abonnement und einer Verbindung
Sie können das folgende Cmdlet verwenden, um ein virtuelles Netzwerkgateway mit einer ExpressRoute-Verbindung zu verbinden. Stellen Sie sicher, dass das Gateway für das virtuelle Netzwerk erstellt wurde und für das Erstellen von Verknüpfungen bereit ist, bevor Sie das Cmdlet ausführen.

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Herstellen einer Verbindung zwischen einem virtuellen Netzwerk in einem anderen Abonnement und einer Verbindung
Sie können eine ExpressRoute-Verbindung für mehrere Abonnements freigeben. Die folgende Abbildung zeigt eine einfache schematische Darstellung der Freigabe von Lasten für ExpressRoute-Verbindungen für mehrere Abonnements.

Jede der kleineren Clouds innerhalb der großen Cloud stellt Abonnements dar, die zu verschiedenen Abteilungen innerhalb einer Organisation gehören. Jede Abteilung in der Organisation verwendet ihr eigenes Abonnement zum Bereitstellen von Diensten. Für die Verbindung mit dem lokalen Netzwerk kann jedoch eine einzelne ExpressRoute-Leitung gemeinsam genutzt werden. Eine einzelne Abteilung (in diesem Beispiel: IT) kann die ExpressRoute-Verbindung besitzen. Andere Abonnements innerhalb der Organisation können die ExpressRoute-Leitung ebenfalls nutzen.

> [!NOTE]
> Konnektivitäts- und Bandbreitengebühren für die ExpressRoute-Verbindung werden dem Abonnementbesitzer in Rechnung gestellt. Alle virtuellen Netzwerke verwenden gemeinsam dieselbe Bandbreite.
> 

:::image type="content" source="./media/expressroute-howto-linkvnet-classic/cross-subscription.png" alt-text="Abonnementübergreifende Konnektivität":::

### <a name="administration---circuit-owners-and-circuit-users"></a>Verwaltung – Leitungsbesitzer und Leitungsbenutzer

Der „Leitungsbesitzer“ (Verbindungsbesitzer) ist ein autorisierter Poweruser der ExpressRoute-Leitungsressource. Der Verbindungsbesitzer kann Autorisierungen erstellen, die durch „Verbindungsbenutzer“ eingelöst werden können. Leitungsbenutzer sind Besitzer virtueller Netzwerkgateways, die sich nicht im selben Abonnement wie die ExpressRoute-Leitung befinden. Verbindungsbenutzer können Autorisierungen einlösen (eine Autorisierung für jedes virtuelle Netzwerk).

Der Besitzer der Verbindung hat die Möglichkeit, Autorisierungen jederzeit zu ändern und aufzuheben. Das Aufheben einer Autorisierung führt dazu, dass alle Verbindungen aus dem Abonnement gelöscht werden, dessen Zugriff aufgehoben wurde.

### <a name="circuit-owner-operations"></a>Aktionen als Verbindungsbesitzer

**So erstellen Sie eine Autorisierung**

Der Leitungsbesitzer erstellt eine Autorisierung, die wiederum einen Autorisierungsschlüssel erstellt. Mit diesem kann ein Leitungsbenutzer seine virtuellen Netzwerkgateways mit der ExpressRoute-Leitung verbinden. Eine Autorisierung gilt nur für jeweils eine Verbindung.

Der folgende Cmdlet-Ausschnitt veranschaulicht das Erstellen einer Autorisierung:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```

Die Antwort auf die oben genannten Befehle enthält Autorisierungsschlüssel und -status:

```azurepowershell
Name                   : MyAuthorization1
Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
AuthorizationKey       : ####################################
AuthorizationUseStatus : Available
ProvisioningState      : Succeeded
```

**So überprüfen Sie Autorisierungen**

Mit dem folgenden Cmdlet kann der Besitzer einer Verbindung alle für eine bestimmte Verbindung ausgestellten Autorisierungen überprüfen:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**So fügen Sie Autorisierung hinzu**

Mit dem folgenden Cmdlet kann der Verbindungsbesitzer Autorisierungen hinzufügen:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**So löschen Sie Autorisierungen**

Mit dem folgenden Cmdlet kann der Besitzer einer Verbindung Autorisierungen für einen Benutzer widerrufen oder löschen:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Aktionen als Verbindungsbenutzer

Der Verbindungsbenutzer benötigt die Peer-ID und einen Autorisierungsschlüssel vom Verbindungsbesitzer. Der Autorisierungsschlüssel ist eine GUID.

Die Peer-ID kann mithilfe des folgenden Befehls überprüft werden:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**So lösen Sie eine Verbindungsautorisierung ein**

Mit dem folgenden Cmdlet können Benutzer einer Verbindung eine Verknüpfungsautorisierung abrufen:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**So geben Sie eine Verbindungsautorisierung frei**

Sie können eine Autorisierung durch das Löschen der Verbindung freigeben, die die ExpressRoute-Verbindung mit dem virtuellen Netzwerk verknüpft.

## <a name="modify-a-virtual-network-connection"></a>Ändern einer Verbindung mit einem virtuellen Netzwerk
Sie können bestimmte Eigenschaften einer Verbindung mit einem virtuellen Netzwerk aktualisieren. 

**So aktualisieren Sie die Verbindungsgewichtung**

Das virtuelle Netzwerk kann mit mehreren ExpressRoute-Verbindungen verbunden werden. Möglicherweise erhalten Sie von mehreren ExpressRoute-Verbindungen dasselbe Präfix. Um auszuwählen, an welche Verbindung Datenverkehr für dieses Präfix gesendet werden soll, ändern Sie die Eigenschaft *RoutingWeight* einer Verbindung. Datenverkehr wird an die Verbindung mit dem höchsten *RoutingWeight* gesendet.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Der Bereich von *RoutingWeight* liegt zwischen 0 und 32.000. Der Standardwert ist 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurieren von ExpressRoute FastPath 
Sie können [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) aktivieren, wenn Ihr virtuelles Netzwerkgateway den Typ „Höchstleistung“ oder „ErGw3AZ“ hat. FastPath verbessert die Leistung von Datenpfaden (Pakete pro Sekunde und Verbindungen pro Sekunde) zwischen Ihrem lokalen und Ihrem virtuellen Netzwerk. 

**Konfigurieren von FastPath für eine neue Verbindung**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**Aktualisieren einer vorhandenen Verbindung zum Aktivieren von FastPath**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

> [!NOTE]
> Sie können den [Verbindungsmonitor](how-to-configure-connection-monitor.md) verwenden, um mithilfe von FastPath zu überprüfen, ob Ihr Datenverkehr das Ziel erreicht.
>

## <a name="enroll-in-expressroute-fastpath-features-preview"></a>Registrieren für FastPath-Features in ExpressRoute (Vorschau)

FastPath-Unterstützung für das Peering virtueller Netzwerke befindet sich jetzt in der öffentlichen Vorschauphase.

### <a name="fastpath-and-virtual-network-peering"></a>FastPath und Peering virtueller Netzwerke

Mit FastPath und Peering virtueller Netzwerke können Sie ExpressRoute-Konnektivität direkt mit VMs in einem lokalen oder gepeerten virtuellen Netzwerk aktivieren und dabei das virtuelle ExpressRoute-Netzwerkgateway im Datenpfad umgehen.

Um sich für diese Vorschauversion zu registrieren, führen Sie den folgenden Azure PowerShell-Befehl im Azure-Zielabonnement aus:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName ExpressRouteVnetPeeringGatewayBypass -ProviderNamespace Microsoft.Network
```

> [!NOTE] 
> Alle Verbindungen, die für FastPath im Zielabonnement konfiguriert sind, werden in dieser Vorschauversion registriert. Wir raten davon ab, diese Vorschauversion in Produktionsabonnements zu aktivieren.
> Wenn Sie FastPath bereits konfiguriert haben und sich für die Previewfunktionen registrieren möchten, müssen Sie folgende Schritte ausführen:
> 1. Registrieren Sie sich mit dem obigen Azure PowerShell-Befehl für die FastPath-Previewfunktion.
> 1. Deaktivieren Sie FastPath für die Zielverbindung, und aktivieren Sie es dann erneut.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die ExpressRoute-Verbindung nicht mehr benötigen, entfernen Sie die Verknüpfung zwischen dem Gateway und der Leitung, indem Sie in dem Abonnement, in dem sich das Gateway befindet, den Befehl `Remove-AzVirtualNetworkGatewayConnection` verwenden.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection "MyConnection" -ResourceGroupName "MyRG"
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen über ExpressRoute finden Sie unter ExpressRoute – FAQ.

> [!div class="nextstepaction"]
> [ExpressRoute – FAQ](expressroute-faqs.md)
