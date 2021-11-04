---
title: Erstellen einer Mesh-Netzwerktopologie mit Azure Virtual Network Manager (Vorschauversion) – Azure PowerShell
description: Erfahren Sie, wie Sie Azure PowerShell verwenden, um eine Mesh-Netzwerktopologie mit Azure Virtual Network Manager zu erstellen.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5d763a0914a624d21d3845a05c1f2c0e6ef7e1ad
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095097"
---
# <a name="create-a-mesh-network-topology-with-azure-virtual-network-manager-preview---azure-powershell"></a>Erstellen einer Mesh-Netzwerktopologie mit Azure Virtual Network Manager (Vorschauversion) – Azure PowerShell

In diesem Artikel erfahren Sie, wie Sie Azure PowerShell verwenden, um eine Mesh-Netzwerktopologie mit Azure Virtual Network Manager zu erstellen. Mit dieser Konfiguration können alle virtuellen Netzwerke derselben Region in derselben Netzwerkgruppe miteinander kommunizieren. Sie können die regionsübergreifende Konnektivität aktivieren, indem Sie in der Konnektivitätskonfiguration die globale Mesh-Einstellung aktivieren.

> [!IMPORTANT]
> Azure Virtual Network Manager ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Erfahren Sie mehr über die [Mesh](concept-connectivity-configuration.md#mesh-network-topology)-Netzwerktopologie.
* Erstellen Sie eine [Azure Virtual Network Manager-Instanz](create-virtual-network-manager-powershell.md#create-virtual-network-manager).
* Identifizieren Sie virtuelle Netzwerke, die Sie in der Mesh-Konfiguration verwenden möchten, oder erstellen Sie neue [virtuelle Netzwerke](../virtual-network/quick-create-powershell.md).

## <a name="create-a-network-group"></a>Erstellen einer Netzwerkgruppe

In diesem Abschnitt lernen Sie, wie Sie eine Netzwerkgruppe mit den virtuellen Netzwerken erstellen, die Sie für die Hub-Spoke-Netzwerktopologie verwenden werden.

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

## <a name="create-a-mesh-connectivity-configuration"></a>Erstellen einer Mesh-Konnektivitätskonfiguration

In diesem Abschnitt erfahren Sie, wie Sie eine Mesh-Konfiguration mit der im vorherigen Abschnitt erstellten Netzwerkgruppe erstellen.

1. Erstellen Sie mit [New-AzNetworkManagerConnectivityGroupItem](/powershell/module/az.network/new-aznetworkmanagerconnectivitygroupitem), ein Konnektivitätsgruppenelement, dem Sie eine Netzwerkgruppe hinzufügen können.

    ```azurepowershell-interactive
    $gi = @{
        NetworkGroupId = $networkgroup.Id
    }
    $groupItem = New-AzNetworkManagerConnectivityGroupItem @gi
    ```

1. Erstellen Sie eine Konfigurationsgruppe, und fügen Sie ihr das Gruppenelement aus dem vorherigen Schritt hinzu.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.PSNetworkManagerConnectivityGroupItem]]$configGroup = @()
    $configGroup.Add($groupItem)
    ```

1. Erstellen Sie die Konnektivitätskonfiguration mit [New-AzNetworkManagerConnectivityConfiguration](/powershell/module/az.network/new-aznetworkmanagerconnectivityconfiguration).

    ```azurepowershell-interactive
    $config = @{
        Name = 'connectivityconfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
        ConnectivityTopology = 'Mesh'
        AppliesToGroup = $configGroup
    }
    $connectivityconfig = New-AzNetworkManagerConnectivityConfiguration @config
     ```

## <a name="deploy-the-mesh-configuration"></a>Bereitstellen der Mesh-Konfiguration

Übergeben Sie die Konfiguration an die Zielregionen mit [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit).

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()  
$configIds.add($connectivityconfig.id) 
[System.Collections.Generic.List[string]]$target = @()   
$target.Add("westus")     

$deployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $target
    CommitType = 'Connectivity'
}
Deploy-AzNetworkManagerCommit @deployment
```

## <a name="confirm-deployment"></a>Überprüfen der Bereitstellung

1. Wechseln Sie zu einem der virtuellen Netzwerke im Portal und wählen Sie unter *Einstellungen* **Network Manager** aus. Die Konfiguration sollte auf dieser Seite angezeigt werden.

1. Um die Konnektivität zwischen den virtuellen Netzwerken zu testen, stellen Sie in jedem virtuellen Netzwerk eine Test-VM zur Verfügung und initiieren Sie eine ICMP-Anforderung zwischen diesen VMs.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu [SecurityAdmin-Regeln](concept-security-admins.md)
- Erfahren Sie, wie Sie Netzwerkdatenverkehr mit einer [SecurityAdmin-Konfiguration](how-to-block-network-traffic-powershell.md) blockieren.
