---
title: Konfigurieren benutzerdefinierter BGP-Communitys für Privates Azure ExpressRoute Peering (Vorschau)
description: Erfahren Sie, wie Sie den BGP-Communitywert für ein neues oder vorhandenes virtuelles Netzwerk anwenden oder aktualisieren können.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/18/2021
ms.author: duau
ms.openlocfilehash: 448438fd5bde23d3518099715f41a4a9739f4815
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135850"
---
# <a name="configure-custom-bgp-communities-for-azure-expressroute-private-peering-preview"></a>Konfigurieren benutzerdefinierter BGP-Communitys für Privates Azure ExpressRoute Peering (Vorschau)

BGP-Communitys sind Gruppierungen von IP-Präfixen, die mit einem Communitywert gekennzeichnet sind. Dieser Wert kann verwendet werden, um Routingentscheidungen für die Infrastruktur des Routers zu treffen. Sie können Filter anwenden oder Routingeinstellungen für Datenverkehr angeben, der mit BGP-Communitytags von Azure an Ihre lokale Umgebung gesendet wird. In diesem Artikel wird erläutert, wie Sie einen benutzerdefinierten BGP-Communitywert für Ihre virtuellen Netzwerke mithilfe von Azure PowerShell anwenden. Nach der Konfiguration können Sie den regionalen BGP-Communitywert und den benutzerdefinierten Communitywert Ihres virtuellen Netzwerks anzeigen. Dieser Wert wird für ausgehenden Datenverkehr verwendet, der über ExpressRoute gesendet wird, wenn er aus diesem virtuellen Netzwerk stammt.

## <a name="prerequisites"></a>Voraussetzungen

* Lesen Sie, bevor Sie mit der Konfiguration beginnen, die Seiten [Voraussetzungen](expressroute-prerequisites.md), [Routinganforderungen](expressroute-routing.md) und [Workflows](expressroute-workflows.md).

* Sie benötigen eine aktive ExpressRoute-Verbindung. 
  * Führen Sie die Schritte zum [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-arm.md) aus, und lassen Sie sie vom Konnektivitätsanbieter aktivieren. 
  * Stellen Sie sicher, dass privates Azure-Peering für die Verbindung konfiguriert ist. Informationen zum Routing finden Sie unter [Konfigurieren des Routings](expressroute-howto-routing-arm.md) . 
  * Stellen Sie sicher, dass das private Azure-Peering konfiguriert wird und das BGP-Peering zwischen Ihrem Netzwerk und Microsoft einrichtet, damit End-to-End-Konnektivität bereitgestellt werden kann.
  
### <a name="working-with-azure-powershell"></a>Arbeiten mit Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="apply-a-custom-bgp-community-value-for-a-new-virtual-network"></a>Anwenden eines benutzerdefinierten BGP-Communitywerts für ein neues virtuelles Netzwerk

1. Um mit der Konfiguration zu beginnen, melden Sie sich bei Ihrem Azure-Konto an, und wählen Sie das zu verwendende Abonnement aus.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

1. Erstellen Sie eine Ressourcengruppe zum Speichern des neuen virtuellen Netzwerks.

    ```azurepowershell-interactive
    $rg = @{
        Name = 'myERRG'
        Location = 'WestUS'
    }
    New-AzResourceGroup @rg
    ```

1. Erstellen Sie ein neues virtuelles Netzwerk mit dem `-BgpCommunity`-Flag, um einen BGP-Communitywert anzuwenden.

    ```azurepowershell-interactive
    $vnet = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
        Location = 'WestUS'
        AddressPrefix = '10.0.0.0/16'
        BgpCommunity = '12076:20001'    
    }
    New-AzVirtualNetwork @vnet
    ```
    
    > [!NOTE]
    > `12076:` ist vor Ihrem benutzerdefinierten Communitywert erforderlich.
    >

1. Rufen Sie Ihr virtuelles Netzwerk ab und überprüfen Sie dessen Eigenschaften. Sie werden einen Abschnitt *BgpCommunities* sehen, der einen **RegionalCommunity**-Wert und einen **VirtualNetworkCommunity**-Wert enthält. Der *RegionalCommunity*-Wert ist basierend auf der Azure-Region des virtuellen Netzwerks vordefiniert. Der *VirtualNetworkCommunity*-Wert sollte mit Ihrer benutzerdefinierten Definition übereinstimmen.

    ```azurepowershell-interactive
    $virtualnetwork = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
    } 
    Get-AzVirtualNewtork @virtualnetwork
    ```

## <a name="applying-or-updating-the-custom-bgp-value-for-an-existing-virtual-network"></a>Anwenden oder Aktualisieren des benutzerdefinierten BGP-Werts für ein vorhandenes virtuelles Netzwerk

1. Rufen Sie das virtuelle Netzwerk ab, das Sie anwenden möchten, oder aktualisieren Sie den BGP-Communitywert und speichern Sie ihn in einer Variablen.

    ```azurepowershell-interactive
    $virtualnetwork = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
    } 
    $vnet = Get-AzVirtualNetwork @virtualnetwork
    ```

1. Aktualisieren Sie den `VirtualNetworkCommunity`-Wert für Ihr virtuelles Netzwerk.

    ```azurepowershell-interactive
    $vnet.BgpCommunities = @{VirtualNetworkCommunity = '12076:20002'}
    $vnet | Set-AzVirtualNetwork
    ```

    > [!NOTE]
    > `12076:` ist vor Ihrem benutzerdefinierten Communitywert erforderlich.
    >

1. Rufen Sie Ihr virtuelles Netzwerk ab und überprüfen Sie dessen aktualisierte Eigenschaften. Der **RegionalCommunity**-Wert ist basierend auf der Azure-Region des virtuellen Netzwerks vordefiniert. Der **VirtualNetworkCommunity**-Wert sollte mit Ihrer benutzerdefinierten Definition übereinstimmen.

    ```azurepowershell-interactive
    $virtualnetwork = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
    } 
    Get-AzVirtualNetwork @virtualnetwork
    ```

> [!IMPORTANT]
>  Wenn Ihr vorhandenes virtuelles Netzwerk bereits mit einer ExpressRoute-Verbindung verbunden ist, müssen Sie die ExpressRoute-Verbindung löschen und neu erstellen, nachdem Sie den benutzerdefinierten BGP-Communitywert angewendet haben. Weitere Informationen finden Sie unter [Verknüpfen eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md).
>

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen der ExpressRoute-Konnektivität](expressroute-troubleshooting-expressroute-overview.md)
- [Behandeln von Problemen mit der Netzwerkleistung](expressroute-troubleshooting-network-performance.md)
