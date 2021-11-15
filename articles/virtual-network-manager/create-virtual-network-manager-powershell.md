---
title: 'Schnellstart: Erstellen eines Cloudnetzwerks mit Azure Virtual Network Manager und Azure PowerShell'
description: In diesem Schnellstart erfahren Sie, wie Sie mit Virtual Network Manager und Azure PowerShell ein Cloudnetzwerk erstellen.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: template-quickstart, ignite-fall-2021
ms.openlocfilehash: effbdd0482e40535793e2c60dcdce3b4cdb518e6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466462"
---
# <a name="quickstart-create-a-mesh-network-with-azure-virtual-network-manager-using-azure-powershell"></a>Schnellstart: Erstellen eines Cloudnetzwerks mit Azure Virtual Network Manager und Azure PowerShell

Beginnen Sie mit der Nutzung von Azure Virtual Network Manager und Azure PowerShell, um die Konnektivität Ihrer virtuellen Netzwerke verwalten.

In diesem Schnellstart stellen Sie drei virtuelle Netzwerke bereit und erstellen mit Azure Virtual Network Manager eine Cloudnetzwerktopologie.

> [!IMPORTANT]
> Azure Virtual Network Manager ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Stellen Sie sicher, dass Sie über die aktuellen PowerShell-Module verfügen, oder verwenden Sie Azure Cloud Shell im Portal.
* Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="register-subscription-for-public-preview"></a>Registrieren eines Abonnements für die Public Preview

Verwenden Sie den folgenden Befehl, um Ihr Azure-Abonnement für die Public Preview von Azure Virtual Network Manager zu registrieren:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowAzureNetworkManager -ProviderNamespace Microsoft.Network
```

## <a name="install-azure-powershell-module"></a>Installieren des Azure PowerShell-Moduls

Installieren Sie mithilfe des folgenden Befehls das aktuelle Azure PowerShell-Modul *Az.Network*:

```azurepowershell-interactive
Install-Module -Name Az.Network -AllowPrerelease
```

## <a name="create-virtual-network-manager"></a>Erstellen einer Virtual Network Manager-Instanz

1. Definieren Sie den Bereich und Zugriffstyp dieser Azure Virtual Network Manager-Instanz. Sie können den Bereich mit einer Abonnementgruppe, Verwaltungsgruppe oder einer Kombination aus beidem erstellen. Erstellen Sie den Bereich mit [New-AzNetworkManagerScope](/powershell/module/az.network/new-aznetworkmanagerscope).

    ```azurepowershell-interactive
    [System.Collections.Generic.List[string]]$subGroup = @()  
    $group.Add("/subscriptions/abcdef12-3456-7890-abcd-ef1234567890")
    [System.Collections.Generic.List[string]]$mgGroup = @()  
    $group.Add("/managementGroups/abcdef12-3456-7890-abcd-ef1234567890")
    
    [System.Collections.Generic.List[String]]$access = @()  
    $access.Add("Connectivity");  
    $access.Add("Security"); 
 
    $scope = New-AzNetworkManagerScope -Subscription $subGroup  -ManagementGroup $mgGroup
    ```

1. Erstellen Sie die Virtual Network Manager-Instanz mit [New-AzNetworkManager](/powershell/module/az.network/new-aznetworkmanager). In diesem Beispiel wird eine Azure Virtual Network Manager-Instanz mit dem Namen **myAVNM** in der Region „USA, Westen“ erstellt.

    ```azurepowershell-interactive
    $avnm = @{
        Name = 'myAVNM'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerScope = $scope.id
        NetworkManagerScopeAccess = $access
        Location = 'West US'
    }
    $networkmanager = New-AzNetworkManager @avnm
    ```

## <a name="create-resource-group-and-virtual-networks"></a>Erstellen einer Ressourcengruppe und von virtuellen Netzwerken

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Bevor Sie eine Azure Virtual Network Manager-Instanz erstellen können, müssen Sie eine Ressourcengruppe zum Hosten dieser Instanz erstellen. Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) eine Ressourcengruppe. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen **myAVNMResourceGroup** in der Region **WestUS**.

```azurepowershell-interactive
$rg = @{
    Name = 'myAVNMResourceGroup'
    Location = 'WestUS'
}
New-AzResourceGroup @rg
```

### <a name="create-three-virtual-networks"></a>Erstellen von drei virtuellen Netzwerken

Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) drei virtuelle Netzwerke. In diesem Beispiel werden die virtuellen Netzwerke **VNetA**, **VNetB** and **VNetC** in der Region **USA, Westen** erstellt. Wenn Sie bereits über virtuelle Netzwerke verfügen, mit denen Sie ein Cloudnetzwerk erstellen möchten, können Sie zum nächsten Abschnitt springen.

```azurepowershell-interactive
$vnetA = @{
    Name = 'VNetA'
    ResourceGroupName = 'myAVNMResourceGroup'
    Location = 'West US'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetworkA = New-AzVirtualNetwork @vnetA

$vnetB = @{
    Name = 'VNetB'
    ResourceGroupName = 'myAVNMResourceGroup'
    Location = 'West US'
    AddressPrefix = '10.1.0.0/16'    
}
$virtualNetworkB = New-AzVirtualNetwork @vnetB

$vnetC = @{
    Name = 'VNetC'
    ResourceGroupName = 'myAVNMResourceGroup'
    Location = 'West US'
    AddressPrefix = '10.2.0.0/16'    
}
$virtualNetworkC = New-AzVirtualNetwork @vnetC
```

### <a name="add-a-subnet-to-each-virtual-network"></a>Hinzufügen eines Subnetzes zu den einzelnen virtuellen Netzwerken

Um die Konfiguration der virtuellen Netzwerke abschließen zu können, fügen Sie jedem ein /24-Subnetz hinzu. Erstellen Sie mit [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) eine Subnetzkonfiguration mit der Bezeichnung **default**.

```azurepowershell-interactive
$subnetA = @{
    Name = 'default'
    VirtualNetwork = $virtualNetworkA
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfigA = Add-AzVirtualNetworkSubnetConfig @subnetA
$virtualnetworkA | Set-AzVirtualNetwork

$subnetB = @{
    Name = 'default'
    VirtualNetwork = $virtualNetworkB
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfigC = Add-AzVirtualNetworkSubnetConfig @subnetB
$virtualnetworkB | Set-AzVirtualNetwork

$subnetC = @{
    Name = 'default'
    VirtualNetwork = $virtualNetworkC
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfigC = Add-AzVirtualNetworkSubnetConfig @subnetC
$virtualnetworkC | Set-AzVirtualNetwork
```

## <a name="create-a-network-group"></a>Erstellen einer Netzwerkgruppe

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

1. Definieren Sie die Bedingungsanweisung, und speichern Sie sie in einer Variablen:

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

1. Erstellen Sie die Netzwerkgruppe mithilfe der Bedingungsanweisung, die im letzten Schritt mit [New-AzNetworkManagerGroup](/powershell/module/az.network/new-aznetworkmanagergroup) festgelegt wurde.

    ```azurepowershell-interactive
    $ng = @{
        Name = 'myNetworkGroup'
        ResourceGroupName = 'myAVNMResourceGroup'
        GroupMember = $groupMembers
        ConditionalMembership = $conditionalMembership
        NetworkManagerName = 'myAVNM'
        MemberType = 'Microsoft.Network/VirtualNetwork'
    }
    $networkgroup = New-AzNetworkManagerGroup @ng
    ```

## <a name="create-a-configuration"></a>Erstellen einer Konfiguration

1. Erstellen Sie mit [New-AzNetworkManagerConnectivityGroupItem](/powershell/module/az.network/new-aznetworkmanagerconnectivitygroupitem) ein Konnektivitätsgruppenelement, dem Sie eine Netzwerkgruppe hinzufügen können.

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

1. Erstellen Sie mit [New-AzNetworkManagerConnectivityConfiguration](/powershell/module/az.network/new-aznetworkmanagerconnectivityconfiguration) die Konnektivitätskonfiguration.

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

## <a name="commit-deployment"></a>Committen der Bereitstellung

Committen Sie mit [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit) die Konfiguration für die Zielregionen.

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

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure Virtual Network Manager-Instanz nicht mehr benötigen, müssen Sie sicherstellen, dass alle der folgenden Bedingungen erfüllt sind, bevor Sie die Ressource löschen:

* In keiner Region sind Konfigurationen bereitgestellt.
* Alle Konfigurationen wurden gelöscht.
* Alle Netzwerkgruppen wurden gelöscht.

1. Entfernen Sie die Konnektivitätsbereitstellung, indem Sie mit [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit) eine leere Konfiguration bereitstellen.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[string]]$configIds = @()
    [System.Collections.Generic.List[string]]$target = @()   
    $target.Add("westus")     
    $removedeployment = @{
        Name = 'myAVNM'
        ResourceGroupName = 'myAVNMResourceGroup'
        ConfigurationId = $configIds
        Target = $target
        CommitType = 'Connectivity'
    }
    Deploy-AzNetworkManagerCommit @removedeployment
    ```

1. Entfernen Sie die Konnektivitätskonfiguration mit [Remove-AzNetworkManagerConnectivityConfiguration](/powershell/module/az.network/remove-aznetworkmanagerconnectivityconfiguration).

    ```azurepowershell-interactive
    $removeconfig = @{
        Name = 'connectivityconfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    Remove-AzNetworkManagerConnectivityConfiguration @removeconfig   
    ```

1. Entfernen Sie die Netzwerkgruppe mit [Remove-AzNetworkManagerGroup](/powershell/module/az.network/remove-aznetworkmanagergroup).

    ```azurepowershell-interactive
    $removegroup = @{
        Name = 'myNetworkGroup'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    Remove-AzNetworkManagerGroup @removegroup
    ```

1. Löschen Sie die Network Manager-Instanz mit [Remove-AzNetworkManager](/powershell/module/az.network/remove-aznetworkmanager).

    ```azurepowershell-interactive
    $removenetworkmanager = @{
        Name = 'myAVNM'
        ResourceGroupName = 'myAVNMResourceGroup'
    }
    Remove-AzNetworkManager @removenetworkmanager
    ```

1. Wenn Sie die erstellte Ressource nicht mehr benötigen, löschen Sie die Ressourcengruppe mit [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

    ```azurepowershell-interactive
    Remove-AzResourceGroup -Name 'myAVNMResourceGroup'
    ```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die Azure Virtual Network Manager-Instanz erstellt haben, informieren Sie sich, wie Sie den Netzwerkdatenverkehr mithilfe einer Sicherheitsadministratorkonfiguration blockieren:

> [!div class="nextstepaction"]
> [Blockieren von Netzwerkdatenverkehr mit Sicherheitsadministratorregeln](how-to-block-network-traffic-powershell.md)
