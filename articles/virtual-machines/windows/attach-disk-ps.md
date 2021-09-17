---
title: Anfügen eines Datenträgers an einen virtuellen Windows-Computer in Azure mithilfe von PowerShell
description: Hier erhalten Sie Informationen zum Anfügen eines neuen oder vorhandenen Datenträgers an einen virtuellen Windows-Computer mithilfe von PowerShell und dem Resource Manager-Bereitstellungsmodell.
author: roygara
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f8fbcbb70ae73c9fdfe0aa388c07d23d6c4f8e20
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692618"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Anfügen eines Datenträgers an einen virtuellen Windows-Computer mithilfe von PowerShell

**Gilt für**: :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

In diesem Artikel wird beschrieben, wie Sie mithilfe von PowerShell sowohl neue als auch vorhandene Datenträger an einen virtuellen Windows-Computer anfügen. 

Lesen Sie zunächst diese Tipps:

* Die Größe des virtuellen Computers bestimmt, wie viele Datenträger Sie anfügen können. Weitere Informationen finden Sie unter [Größen für virtuelle Computer](../sizes.md).
* Für die Verwendung von SSD Premium-Datenträgern benötigen Sie einen [virtuellen Computer mit einem für Storage Premium geeigneten Typ](../sizes-memory.md), z. B. die DS- oder GS-Serie.

In diesem Artikel wird PowerShell innerhalb von [Azure Cloud Shell](../../cloud-shell/overview.md) verwendet. Azure Cloud Shell wird kontinuierlich auf die neueste Version aktualisiert. Wählen Sie zum Öffnen von Cloud Shell oben in einem Codeblock die Option **Ausprobieren** aus.

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Hinzufügen eines leeren Datenträgers zu einem virtuellen Computer

Dieses Beispiel zeigt, wie Sie einem vorhandenen virtuellen Computer einen leeren Datenträger hinzufügen.

### <a name="using-managed-disks"></a>Verwenden verwalteter Datenträger

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Verwenden von verwalteten Datenträgern in einer Verfügbarkeitszone

Verwenden Sie zum Erstellen eines Datenträgers in einer Verfügbarkeitszone [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) mit dem `-Zone`-Parameter. Im folgenden Beispiel wird ein Datenträger in Zone *1* erstellt.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>Datenträger initialisieren

Nachdem Sie einen leeren Datenträger hinzugefügt haben, müssen Sie ihn initialisieren. Um den Datenträger zu initialisieren, können Sie sich bei einem virtuellen Computer anmelden und die Datenträgerverwaltung verwenden. Wenn Sie bei der Erstellung [WinRM](/windows/desktop/winrm/portal) aktiviert und ein Zertifikat auf dem virtuellen Computer installiert haben, können Sie den Datenträger mit einem PowerShell-Remoteaufruf initialisieren. Alternativ können Sie auch eine benutzerdefinierte Skripterweiterung verwenden:

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

Die Skriptdatei kann Code für die Initialisierung der Datenträger enthalten. Beispiel:

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk |
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Hinzufügen eines vorhandenen Datenträgers zu einem virtuellen Computer

Sie können einen vorhandenen verwalteten Datenträger als Datenträger für Daten an einen virtuellen Computer anfügen.

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Nächste Schritte

Sie können verwaltete Datenträger auch über Vorlagen bereitstellen. Weitere Informationen finden Sie unter [Verwenden verwalteter Datenträger mit Resource Manager-Vorlagen](../using-managed-disks-template-deployments.md) oder in der [Schnellstartvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-multiple-data-disk) (quickstart-template) zum Bereitstellen mehrerer Datenträger.
