---
title: Blockieren von Netzwerkverkehr mit Azure Virtual Network Manager (Vorschau) - Azure PowerShell
description: Erfahren Sie, wie Sie Netzwerkverkehr mithilfe von Sicherheitsregeln in Azure Virtual Network Manager mit der Azure PowerShell blockieren können.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: d2e1a79891c0061dd49749fa2e27a2725a0b922f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096038"
---
# <a name="how-to-block-network-traffic-with-azure-virtual-network-manager-preview---azure-powershell"></a>Blockieren von Netzwerkverkehr mit Azure Virtual Network Manager (Vorschau) - Azure PowerShell

Dieser Artikel zeigt Ihnen, wie Sie eine Sicherheitsregel zum Blockieren von ausgehendem Netzwerkverkehr zu Port 80 und 443 erstellen, die Sie Ihren Regelsammlungen hinzufügen können. Weitere Informationen finden Sie unter [Sicherheitsverwaltungsregeln](concept-security-admins.md).

> [!IMPORTANT]
> Azure Virtual Network Manager befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit der Konfiguration von Sicherheitsregeln beginnen, sollten Sie die folgenden Schritte bestätigen:

* Sie verstehen jedes Element in einer [Sicherheitsverwaltungsregel](concept-security-admins.md).
* Sie haben eine [Azure Virtual Network Manager-Instanz](create-virtual-network-manager-powershell.md) erstellt.

## <a name="create-a-securityadmin-configuration"></a>Erstellen einer SecurityAdmin-Konfiguration

1. Erstellen Sie eine neue SecurityAdmin-Konfiguration mit [New-AzNetworkManagerSecurityAdminConfiguration](/powershell/module/az.network/new-aznetworkmanagersecurityadminconfiguration).

    ```azurepowershell-interactive
    $config = @{
        Name = 'SecurityConfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    $securityconfig = New-AzNetworkManagerSecurityAdminConfiguration @config 
    ```

1. Speichern der Netzwerkgruppe in einer Variablen mit [Get-AzNetworkManagerGroup](/powershell/module/az.network/get-aznetworkmanagergroup).

    ```azurepowershell-interactive
    $ng = @{
        Name = 'myNetworkGroup'
        ResoureceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    $networkgroup = Get-AzNetworkManagerGroup @ng   
    ```

1. Erstellen Sie mit [New-AzNetworkManagerSecurityGroupItem](/powershell/module/az.network/new-aznetworkmanagersecuritygroupitem) ein Konnektivitätsgruppenelement, dem Sie eine Netzwerkgruppe hinzufügen.

    ```azurepowershell-interactive
    $gi = @{
        NetworkGroupId = '$networkgroup.Id'
    }
    $groupItem = New-AzNetworkManagerSecurityGroupItem @gi
    ```

1. Erstellen Sie eine Konfigurationsgruppe und fügen Sie die im vorherigen Schritt erstellte Gruppe hinzu.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.PSNetworkManagerSecurityGroupItem]]$configGroup = @()  
    $configGroup.Add($groupItem) 
    ```

1. Erstellen Sie eine Sammlung von Sicherheitsverwaltungsregeln mit [New-AzNetworkManagerSecurityAdminRuleCollection](/powershell/module/az.network/new-aznetworkmanagersecurityadminrulecollection).

    ```azurepowershell-interactive
    $collection = @{
        Name = 'myRuleCollection'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManager = 'myAVNM'
        ConfigName = 'SecurityConfig'
        AppliesToGroup = $configGroup
    }
    $rulecollection = New-AzNetworkManagerSecurityAdminRuleCollection @collection
    ```

1. Definieren Sie die Variablen für die Quell- und Zieladress-Präfixe und Ports mit [New-AzNetworkManagerAddressPrefixItem](/powershell/module/az.network/new-aznetworkmanageraddressprefixitem).

    ```azurepowershell-interactive
    $sourceip = @{
        AddressPrefix = 'Internet'
        AddressPrefixType = 'ServiceTag'
    }
    $sourceprefix = New-AzNetworkManagerAddressPrefixItem @sourceip

    $destinationip = @{
        AddressPrefix = '10.0.0.0/24'
        AddressPrefixType = 'IPPrefix'
    }
    $destinationprefix = New-AzNetworkManagerAddressPrefixItem @destinationip

    [System.Collections.Generic.List[string]]$sourcePortList = @() 
    $sourcePortList.Add("65500”) 

    [System.Collections.Generic.List[string]]$destinationPortList = @() 
    $destinationPortList.Add("80”)
    $destinationPortList.Add("443”)
    ```

1. Erstellen Sie eine Sicherheitsregel mit [New-AzNetworkManagerSecurityAdminRule](/powershell/module/az.network/new-aznetworkmanagersecurityadminrule).

    ```azurepowershell-interactive
    $rule = @{
        Name = 'Block_HTTP_HTTPS'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
        SecurityAdminConfigurationName = 'SecurityConfig'
        RuleCollectionName = 'myRuleCollection'
        Protocol = 'TCP'
        Access = 'Deny'
        Priority = '100'
        Direction = 'Outbound'
        Source = $sourceprefix
        SourcePortRange = $sourcePortList
        Destination = $destinationprefix
        DestinationPortRange = $destinationPortList
    }
    $securityrule = New-AzNetworkManagerSecurityAdminRule @rule
    ```

## <a name="commit-deployment"></a>Bereitstellung zusagen

Bestätigen Sie die Sicherheitskonfiguration in den Zielregionen mit [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit).

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()  
$configIds.add($securityconfig.id) 
[System.Collections.Generic.List[string]]regions = @()   
$regions.Add("westus")     

$deployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $regions
    CommitType = 'Security'
}
Deploy-AzNetworkManagerCommit @deployment 
```

## <a name="delete-security-configuration"></a>Sicherheitskonfiguration löschen

Wenn Sie die Sicherheitskonfiguration nicht mehr benötigen, müssen Sie sicherstellen, dass die folgenden Kriterien erfüllt sind, bevor Sie die Sicherheitskonfiguration selbst löschen können:

* Es gibt keine Bereitstellung von Konfigurationen in irgendeiner Region.
* Löschen aller Sicherheitsregeln in einer Regelsammlung, die mit der Sicherheitskonfiguration verknüpft ist.

### <a name="remove-security-configuration-deployment"></a>Einsatz der Sicherheitskonfiguration entfernen

Entfernen Sie die Sicherheitsbereitstellung, indem Sie eine Konfiguration mit [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit) bereitstellen.

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()
[System.Collections.Generic.List[string]]$regions = @()   
$regions.Add("westus")     
$removedeployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $regions
    CommitType = 'Security'
}
Deploy-AzNetworkManagerCommit @removedeployment
```

### <a name="remove-security-rules"></a>Sicherheitsregeln entfernen

Entfernen Sie Sicherheitsregeln mit [Remove-AzNetworkManagerSecurityAdminRule](/powershell/module/az.network/remove-aznetworkmanagersecurityadminrule).

```azurepowershell-interactive
$removerule = @{
    Name = 'Block80'
    ResourceGroupName = 'myAVNMResourceGroup'
    NetworkManagerName = 'myAVNM'
    SecurityAdminConfigurationName = 'SecurityConfig'
}
Remove-AzNetworkManagerSecurityAdminRule @removerule
```

### <a name="remove-security-rule-collections"></a>Sammlungen von Sicherheitsregeln entfernen

```azurepowershell-interactive
$removecollection = @{
    Name = 'myRuleCollection'
    ResourceGroupName = 'myAVNMResourceGroup'
    NetworkManagerName = 'myAVNM'
    SecurityAdminConfigurationName = 'SecuritConfig'
}
Remove-AzNetworkManagerSecurityAdminRuleCollection @removecollection
```

### <a name="delete-configuration"></a>Löschen der Konfiguration

Löschen Sie die Sicherheitskonfiguration mit [Remove-AzNetworkManagerSecurityAdminConfiguration](/powershell/module/az.network/remove-aznetworkmanagersecurityadminconfiguration).

```azurepowershell-interactive
$removeconfig = @{
    Name = 'SecurityConfig'
    ResourceGroupName = 'myAVNMResourceGroup'
    NetworkManagerName = 'myAVNM'
}
Remove-AzNetworkManagerSecurityAdminConfiguration @removeconfig
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Sicherheitsverwaltungsregeln](concept-security-admins.md).
