---
title: Erstellen einer Hub-Spoke-Topologie mit Azure Virtual Network Manager (Vorschauversion) - Azure PowerShell
description: Erfahren Sie, wie Sie Azure PowerShell verwenden, um eine Mesh-Netzwerktopologie mit Azure Virtual Network Manager zu erstellen.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 8762a43608c52ceec3f8cb92e08f88a94c244e30
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095854"
---
# <a name="create-a-hub-and-spoke-topology-with-azure-virtual-network-manager-preview---azure-powershell"></a>Erstellen einer Hub-Spoke-Topologie mit Azure Virtual Network Manager (Vorschauversion) - Azure PowerShell

In diesem Artikel erfahren Sie, wie Sie eine Hub-Spoke-Netzwerktopologie mit Azure Virtual Network Manager erstellen. Bei dieser Konfiguration wählen Sie ein virtuelles Netzwerk aus, das als Hub fungieren soll. Die virtuellen Spoke-Netzwerke verfügen dann standardmäßig über ein bidirektionales Peering ausschließlich mit dem Hub. Sie können auch eine direkte Konnektivität zwischen den virtuellen Spoke-Netzwerken aktivieren und es diesen Netzwerken ermöglichen, das virtuelle Netzwerkgateway im Hub zu verwenden.

> [!IMPORTANT]
> *Azure Virtual Network Manager* ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie in den [**zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

* Informieren Sie sich über die [Hub-and-Spoke](concept-connectivity-configuration.md#hub-and-spoke-topology)-Netzwerktopologie.
* Erstellen Sie eine [Azure Virtual Network Manager-Instanz](create-virtual-network-manager-powershell.md#create-virtual-network-manager).
* Identifizieren Sie virtuelle Netzwerke, die Sie in der Hub-and-Spoke-Konfiguration verwenden möchten, oder erstellen Sie neue [virtuelle Netzwerke](../virtual-network/quick-create-powershell.md). 

## <a name="create-a-network-group"></a>Erstellen einer Netzwerkgruppe

In diesem Abschnitt lernen Sie, wie Sie eine Netzwerkgruppe mit den virtuellen Netzwerken erstellen, die Sie für die Hub-and-Spoke-Netzwerktopologie verwenden werden.

### <a name="static-membership"></a>Statische Mitgliedschaft

1. Erstellen Sie ein statisches Mitglied im virtuellen Netzwerk mit [New-AzNetworkManagerGroupMembersItem](/powershell/module/az.network/new-aznetworkmanagergroupmembersitem).

    ```azurepowershell-interactive
    $member = New-AzNetworkManagerGroupMembersItem –ResourceId "/subscriptions/abcdef12-3456-7890-abcd-ef1234567890/resourceGroups/myAVNMResourceGroup/providers/Microsoft.Network/virtualNetworks/VNetA"
    ```

1. Nutzen Sie die folgenden Befehle, um das statische Mitglied zu der statischen Mitgliedschaftsgruppe hinzuzufügen:

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.NetworkManager.PSNetworkManagerGroupMembersItem]]$groupMembers = @()  
    $groupMembers.Add($member)
    ```

### <a name="dynamic-membership"></a>Dynamische Mitgliedschaft

1. Definieren Sie die Bedingungsanweisung und speichern Sie sie in einer Variable:

    ```azurepowershell-interactive
    $conditionalMembership = '{ 
        "allof":[ 
            { 
            "field": "name", 
            "contains": "VNet" 
            } 
        ] 
    }' 
    ```

1. Erstellen Sie die Netzwerkgruppe entweder mithilfe der statischen Mitgliedschaftsgruppe (GroupMember) oder der dynamischen Mitgliedschaftsgruppe (ConditionalMembership), die zuvor über [New-AzNetworkManagerGroup](/powershell/module/az.network/new-aznetworkmanagergroup) definiert wurde.

    ```azurepowershell-interactive
    $ng = @{
        Name = 'myNetworkGroup'
        ResourceGroupName = 'myAVNMResourceGroup'
        GroupMember = $groupMembers
        ConditionalMembership = $conditionalMembership
        NetworkManagerName = 'myAVNM'
        MemberType = 'Microsoft.Network/VirtualNetworks
    }
    $networkgroup = New-AzNetworkManagerGroup @ng
    ```

## <a name="create-a-hub-and-spoke-connectivity-configuration"></a>Erstellen einer Hub-Spoke-Konnektivitätskonfiguration

In diesem Abschnitt erfahren Sie, wie Sie eine Hub-Spoke-Konfiguration mit der im vorherigen Abschnitt erstellten Netzwerkgruppe erstellen.

1. Erstellen Sie mit [New-AzNetworkManagerConnectivityGroupItem](/powershell/module/az.network/new-aznetworkmanagerconnectivitygroupitem), ein Konnektivitätsgruppenelement, dem Sie eine Netzwerkgruppe hinzufügen können. Sie können die direkte Konnektivität mit dem `-GroupConnectivity` Flag, dem globalen Gitternetz mit `-IsGlobal` Flag oder `-UseHubGateway` dem Flag aktivieren, um das Gateway im virtuellen Hubnetzwerk zu verwenden:

    ```azurepowershell-interactive
    $spokes = @{
        NetworkGroupId = $networkgroup.Id
    }
    $spokesGroup = New-AzNetworkManagerConnectivityGroupItem @gi -UseHubGateway -GroupConnectivity 'None' -IsGlobal
    ```

1. Erstellen Sie eine Spokes-Konnektivitätsgruppe, und fügen Sie das Gruppenelement aus dem vorherigen Schritt hinzu.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.NetworkManager.PSNetworkManagerConnectivityGroupItem]]$configGroup = @()
    $configGroup.Add($spokesGroup) 
    ```

1. Erstellen Sie mit [New-AzNetworkManagerHub](/powershell/module/az.network/new-aznetworkmanagerhub)ein Hubkonnektivitätsgruppenelement, und definieren Sie das virtuelle Netzwerk, das Sie als Hub verwenden.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.NetworkManager.PSNetworkManagerHub]]$hubList = @()
    
    $hub = @{
        ResourceId = '/subscriptions/abcdef12-3456-7890-abcd-ef1234567890/resourceGroups/myAVNMResourceGroup/providers/Microsoft.Network/virtualNetworks/VNetA'
        ResourceType = 'Microsoft.Network/virtualNetworks'
    } 
    $hubvnet = New-AzNetworkManagerHub @hub

    $hubList.Add($hubvnet)
    ```

1. Erstellen Sie die Konnektivitätskonfiguration mit [New-AzNetworkManagerConnectivityConfiguration](/powershell/module/az.network/new-aznetworkmanagerconnectivityconfiguration).

    ```azurepowershell-interactive
    $config = @{
        Name = 'connectivityconfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
        ConnectivityTopology = 'HubAndSpoke'
        Hub = $hubList
        AppliesToGroup = $configGroup
    }
    $connectivityconfig = New-AzNetworkManagerConnectivityConfiguration @config -DeleteExistingPeering -IsGlobal
     ```

## <a name="deploy-the-hub-and-spoke-configuration"></a>Bereitstellen der Hub-Spoke-Konfiguration

Übergeben Sie die Konfiguration an die Zielregionen mit [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit).

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()  
$configIds.add($connectivityconfig.id) 
[System.Collections.Generic.List[string]]$regions = @()   
$regions.Add("westus")     

$deployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $regions
    CommitType = 'Connectivity'
}
Deploy-AzNetworkManagerCommit @deployment
```

## <a name="confirm-deployment"></a>Überprüfen der Bereitstellung

1. Wechseln Sie zu einem der virtuellen Netzwerke im Portal und wählen Sie unter *Einstellungen* **Network Manager** aus. Es sollte eine neue Peeringverbindung zwischen dem Hub und dem virtuellen Spokes-Netzwerk mit *AVNM* im Namen erstellt werden.

1. Um die *direkte Konnektivität* zwischen Spokes zu testen, stellen Sie in jedem virtuellen Spokes-Netzwerk einen virtuellen Computer bereit. Initiieren Sie dann eine ICMP-Anforderung von einem virtuellen Computer an einen anderen.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu [SecurityAdmin-Regeln](concept-security-admins.md)
- Erfahren Sie, wie Sie Netzwerkdatenverkehr mit einer [SecurityAdmin-Konfiguration](how-to-block-network-traffic-powershell.md) blockieren.
