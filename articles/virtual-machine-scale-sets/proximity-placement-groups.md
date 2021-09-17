---
title: Näherungsplatzierungsgruppen für VM-Skalierungsgruppen
description: Erfahren Sie mehr über das Erstellen und Verwenden von Näherungsplatzierungsgruppen für Windows-VM-Skalierungsgruppen in Azure.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: proximity-placement-groups
ms.date: 07/01/2019
ms.reviewer: zivr
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: b5533f14a239e3e2d01c597d57ab2b2726e930b9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693264"
---
# <a name="creating-and-using-proximity-placement-groups-using-powershell"></a>Erstellen und Verwenden von Näherungsplatzierungsgruppen mit PowerShell

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

Um den Abstand zwischen den VMs so stark wie möglich zu verringern und somit die geringstmögliche Latenz zu erzielen, sollten Sie Ihre Skalierungsgruppe in einer [Näherungsplatzierungsgruppe](../virtual-machines/co-location.md#proximity-placement-groups) bereitstellen.

Eine Näherungsplatzierungsgruppe ist eine logische Gruppierung, mit der ein möglichst geringer Abstand zwischen Azure-Compute-Ressourcen sichergestellt wird. Näherungsplatzierungsgruppen sind für Workloads hilfreich, die eine geringe Latenz erfordern.


## <a name="create-a-proximity-placement-group"></a>Erstellen einer Näherungsplatzierungsgruppe
Erstellen Sie mithilfe des Cmdlets [New-AzProximityPlacementGroup ](/powershell/module/az.compute/new-azproximityplacementgroup) eine Näherungsplatzierungsgruppe. 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Auflisten von Näherungsplatzierungsgruppen

Mit dem Cmdlet [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) können Sie alle Näherungsplatzierungsgruppen auflisten.

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>Erstellen einer Skalierungsgruppe

Erstellen Sie mit `-ProximityPlacementGroup $ppg.Id` eine Skalierung in der Näherungsplatzierungsgruppe, um auf die Näherungsplatzierungsgruppen-ID zu verweisen, wenn Sie mit [New-AzVMSS](/powershell/module/az.compute/new-azvmss) die Skalierungsgruppe erstellen.

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

Sie können die Instanz in der Platzierungsgruppe mit [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) anzeigen.

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Nächste Schritte

Sie können auch die [Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) zum Erstellen von Näherungsplatzierungsgruppen verwenden.
