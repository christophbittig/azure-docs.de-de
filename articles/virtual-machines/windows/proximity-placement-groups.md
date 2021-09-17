---
title: Erstellen einer Näherungsplatzierungsgruppe mithilfe von Azure PowerShell
description: Erfahren Sie mehr über das Erstellen und Verwenden von Näherungsplatzierungsgruppen mit Azure PowerShell
services: virtual-machines
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.date: 3/8/2021
ms.author: cynthn
ms.reviewer: zivr
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cad5029c88c3444ab42a53af950d78a4167ea878
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697168"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-powershell"></a>Bereitstellen von VMs für Näherungsplatzierungsgruppen mithilfe von Azure PowerShell

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs 

Um den Abstand zwischen den VMs so stark wie möglich zu verringern und somit die geringstmögliche Latenz zu erzielen, sollten Sie sie in einer [Näherungsplatzierungsgruppe](../co-location.md#proximity-placement-groups) bereitstellen.

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


## <a name="create-a-vm"></a>Erstellen einer VM

Erstellen Sie mit `-ProximityPlacementGroup $ppg.Id` eine VM in der Näherungsplatzierungsgruppe, um auf die Näherungsplatzierungsgruppen-ID zu verweisen, wenn Sie mit [New-AzVM](/powershell/module/az.compute/new-azvm) die VM erstellen.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

Sie können die VM in der Platzierungsgruppe mit [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) anzeigen.

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Verschieben einer vorhandenen VM in eine Näherungsplatzierungsgruppe

Sie können auch eine vorhandene VM einer Näherungsplatzierungsgruppe hinzufügen. Sie müssen die VM zuerst beenden bzw. ihre Zuweisung aufheben, die VM dann aktualisieren und sie neu starten.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Verschieben einer vorhandenen VM aus einer Näherungsplatzierungsgruppe

Um eine VM aus einer Näherungsplatzierungsgruppe zu entfernen, müssen Sie die VM zuerst beenden bzw. ihre Zuweisung aufheben, die VM dann aktualisieren und sie neu starten.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>Verfügbarkeitsgruppen
Sie können auch eine Verfügbarkeitsgruppe in ihrer Näherungsplatzierungsgruppe erstellen. Verwenden Sie den gleichen `-ProximityPlacementGroup`-Parameter mit dem Cmdlet [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset), um eine Verfügbarkeitsgruppe zu erstellen, und alle in der Verfügbarkeitsgruppe erstellten virtuellen Computer werden ebenfalls in derselben Näherungsplatzierungsgruppe erstellt.

Um einer vorhandenen Näherungsplatzierungsgruppe eine vorhandene Verfügbarkeitsgruppe hinzuzufügen oder sie aus ihr zu entfernen, müssen Sie zuerst alle VMs in der Verfügbarkeitsgruppe beenden. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Verschieben einer vorhandenen Verfügbarkeitsgruppe in eine Näherungsplatzierungsgruppe

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Verschieben einer vorhandenen Verfügbarkeitsgruppe aus einer Näherungsplatzierungsgruppe

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>Skalierungsgruppen

Sie können auch eine Skalierungsgruppe in ihrer Näherungsplatzierungsgruppe erstellen. Verwenden Sie den gleichen `-ProximityPlacementGroup`-Parameter mit [New-AzVmss](/powershell/module/az.compute/new-azvmss), um eine Skalierungsgruppe zu erstellen, und alle Instanzen werden in derselben Näherungsplatzierungsgruppe erstellt.


Um einer Näherungsplatzierungsgruppe eine vorhandene Skalierungsgruppe hinzuzufügen oder sie aus ihr zu entfernen, müssen Sie die Skalierungsgruppe zuerst beenden. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Verschieben einer vorhandenen Skalierungsgruppe in eine Näherungsplatzierungsgruppe

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Verschieben einer vorhandenen Skalierungsgruppe aus einer Näherungsplatzierungsgruppe

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>Nächste Schritte

Sie können auch die [Azure CLI](../linux/proximity-placement-groups.md) zum Erstellen von Näherungsplatzierungsgruppen verwenden.
