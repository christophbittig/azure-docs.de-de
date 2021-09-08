---
title: Konvertieren von verwaltetem Datenträgerspeicher zwischen verschiedenen Datenträgertypen mithilfe von Azure PowerShell
description: Vorgehensweisen beim Konvertieren von verwalteten Datenträgern zwischen den verschiedenen Datenträgertypen mithilfe von Azure PowerShell.
author: roygara
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 994b24110399057741b9f62f7ecf30a8a505fd6d
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122350592"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Aktualisieren des Speichertyps eines verwalteten Datenträgers

Es gibt vier Typen verwalteter Azure-Datenträger: Azure Disk Ultra, SSD Premium, SSD Standard und HDD Standard. Sie können basierend auf Ihren Leistungsanforderungen zwischen den Datenträgertypen SSD Premium, SSD Standard und HDD Standard wechseln. Derzeit ist noch kein Wechsel von oder zu Disk Ultra möglich. Sie müssen in diesem Fall einen neuen Datenträger bereitstellen.

Diese Funktion wird für nicht verwaltete Datenträger nicht unterstützt. Sie können jedoch problemlos [nicht verwaltete Datenträger in verwaltete Datenträger konvertieren](convert-unmanaged-to-managed-disks.md), um zwischen den Datenträgertypen wechseln zu können.



## <a name="before-you-begin"></a>Vorbereitung

* Da für die Konvertierung ein Neustart des virtuellen Computers (Virtual Machine, VM) erforderlich ist, sollten Sie die Migration Ihres Datenträgerspeichers während eines bereits vorhandenen Wartungsfensters durchführen.
* Handelt es sich bei Ihrem Datenträger um einen nicht verwalteten Datenträger, müssen Sie ihn zunächst [in einen verwalteten Datenträger konvertieren](convert-unmanaged-to-managed-disks.md), um zwischen den Speicheroptionen wechseln zu können.

## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Umstellen aller verwalteten Datenträger einer VM von einem Konto auf ein anderes

Dieses Beispiel zeigt, wie alle Datenträger einer VM in Storage Premium konvertiert werden. Durch Ändern der Variable $storageType in diesem Beispiel können Sie jedoch den Datenträgertyp der VM in SSD Standard oder HDD Standard konvertieren. Zur Verwendung von verwalteten Premium-Datenträgern muss Ihre VM eine [VM-Größe](../sizes.md) verwenden, die Premium Storage unterstützt. In diesem Beispiel erfolgt auch ein Wechsel zu einer Größe, die Storage Premium unterstützt:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Umstellen einzelner verwalteter Datenträger auf Standard oder Premium

Für Ihre Entwicklungs-/Testworkload sollten Sie eine Kombination aus Standard- und Premium-Datenträgern verwenden, um Kosten zu sparen. Sie haben auch die Möglichkeit, nur die Datenträger upzugraden, die eine höhere Leistung erfordern. Dieses Beispiel zeigt, wie Sie einen einzelnen VM-Datenträger von Standard in Premium Storage konvertieren. Durch Ändern der Variable $storageType in diesem Beispiel können Sie jedoch den Datenträgertyp der VM in SSD Standard oder HDD Standard konvertieren. Zur Verwendung von verwalteten Premium-Datenträgern muss Ihre VM eine [VM-Größe](../sizes.md) verwenden, die Premium Storage unterstützt. In diesem Beispiel wird auch gezeigt, wie Sie zu einer Größe wechseln, die Storage Premium unterstützt:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Umstellen von verwalteten Datenträgern von einem Datenträgertyp in einen anderen

Folgen Sie diesen Schritten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie den virtuellen Computer in der Liste der **VMs** aus.
3. Wenn der virtuelle Computer nicht beendet ist, wählen Sie im oberen Bereich unter **Übersicht** für den virtuellen Computer **Beenden** aus, und warten Sie, bis der virtuellen Computer beendet wurde.
4. Wählen Sie im Bereich für den virtuellen Computer im Menü die Option **Datenträger** aus.
5. Wählen Sie den Datenträger aus, den Sie konvertieren möchten.
6. Wählen Sie im Menü **Größe und Leistung** aus.
7. Ändern Sie den **Kontotyp** aus dem ursprünglichen Datenträgertyp in den gewünschten Datenträgertyp.
8. Wählen Sie **Speichern** aus, und schließen Sie den Datenträgerbereich.

Der Datenträgertyp wird umgehend konvertiert. Sie können Ihren virtuellen Computer nach der Konvertierung starten.

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine schreibgeschützte Kopie eines virtuellen Computers mithilfe einer [Momentaufnahme](snapshot-copy-managed-disk.md).
