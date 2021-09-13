---
title: Verwalten eines Azure-Datenträgerpools (Vorschau)
description: In diesem Artikel erfahren Sie, wie Sie einem Azure-Datenträgerpool verwaltete Datenträger hinzufügen oder die iSCSI-Unterstützung auf einem Datenträger deaktivieren.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4811894a3483bbfce1724b744d904fa328c51ded
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114436985"
---
# <a name="manage-an-azure-disk-pool-preview"></a>Verwalten eines Azure-Datenträgerpools (Vorschau)

In diesem Artikel wird beschrieben, wie Sie einem Azure-Datenträgerpool (Vorschau) einen verwalteten Datenträger hinzufügen und die iSCSI-Unterstützung auf einem Datenträger deaktivieren, der einem Datenträgerpool hinzugefügt wurde.

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie das Azure PowerShell-Modul [Version 6.1.0 oder höher](/powershell/module/az.diskpool/?view=azps-6.1.0&preserve-view=true).

## <a name="add-a-disk-to-a-pool"></a>Hinzufügen eines Datenträgers zu einem Pool

Ihr Datenträger muss die folgenden Anforderungen erfüllen, um dem Datenträgerpool hinzugefügt zu werden:
- Es muss sich entweder um einen SSD Premium-Datenträger oder einen Ultra-Datenträger in derselben Region und Verfügbarkeitszone wie der Datenträgerpool handeln.
    - Bei Ultra-Datenträgern muss die Sektorgröße des Datenträgers 512 Byte betragen.
- Es muss sich um einen freigegebenen Datenträger mit einem maxShares-Wert von mindestens 2 handeln.
- Sie müssen [die RBAC-Berechtigungen des StoragePool-Ressourcenanbieters für die Datenträger bereitstellen, die dem Datenträgerpool hinzugefügt werden](disks-pools-deploy.md#assign-storagepool-resource-provider-permissions).

Das folgende Skript fügt dem Datenträgerpool einen zusätzlichen Datenträger hinzu, und macht ihn über iSCSI verfügbar. Die vorhandenen Datenträger bleiben im Datenträgerpool unverändert.

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<yourDiskName>" #Provide the name of the disk you want to add
$lunName ='LunName>' #Provide the Lun name of the added disk
$diskIds = @()

#Add the disk to disk pool
$DiskPool = Get-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName
$DiskPoolDiskIDs = $DiskPool.Disk.Id
foreach ($Id in $DiskPoolDiskIDs)
{
$diskIds += ($Id)
}

$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName
$diskIds += ,($disk.Id)
Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskIds

#Get the existing iSCSI LUNs and add the new disk
$target = Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName
$existingLuns = $target.Lun
$luns = @()
foreach ($lun in $existingLuns)
{
$tmpLunName = $lun.Name
$tmpId = $lun.ManagedDiskAzureResourceId
$tmplun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $tmpId -Name $tmpLunName
$luns += ,($tmplun)
}

$newlun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $disk.Id -Name $lunName
$luns += ,($newlun)
Update-AzDiskPoolIscsiTarget -Name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName -Lun $luns
```

## <a name="disable-iscsi-on-a-disk-and-remove-it-from-the-pool"></a>Deaktivieren von iSCSI auf einem Datenträger und Entfernen aus dem Pool

Bevor Sie die iSCSI-Unterstützung auf einem Datenträger deaktivieren, vergewissern Sie sich, dass keine ausstehenden iSCSI-Verbindungen mit der iSCSI-LUN bestehen, unter der der Datenträger verfügbar gemacht wird. Wenn ein Datenträger aus dem Datenträgerpool entfernt wird, wird er nicht automatisch gelöscht. Dies verhindert jeglichen Datenverlust, aber Ihnen wird weiterhin die Speicherung von Daten in Rechnung gestellt. Wenn Sie die auf einem Datenträger gespeicherten Daten nicht benötigen, können Sie den Datenträger manuell löschen. Dadurch werden der Datenträger und alle darauf gespeicherten Daten gelöscht, und weitere Gebühren werden verhindert.

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<NameOfDiskYouWantToRemove>" #Provide the name of the disk you want to remove
$lunName ='<LUNForDiskYouWantToRemove>' #Provide the Lun name of the disk you want to remove
$diskIds = @()

#Get the existing iSCSI LUNs and remove it from iSCS target
$target = Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName
$existingLuns = $target.Lun
$luns = @()
foreach ($lun in $existingLuns)
{
if ($lun.Name -notlike $lunName)
{
$tmpLunName = $lun.Name
$tmpId = $lun.ManagedDiskAzureResourceId
$tmplun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $tmpId -Name $tmpLunName
$luns += ,($tmplun)
}
}

Update-AzDiskPoolIscsiTarget -Name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName -Lun $luns

#Remove the disk from disk pool
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName
$DiskPool = Get-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName
$DiskPoolDiskIDs = $DiskPool.Disk.Id
foreach ($Id in $DiskPoolDiskIDs)
{
if ($Id -notlike $disk.Id)
{
$diskIds += ($Id)
}
}

Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskIds
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Verschieben eines Datenträgerpools in ein anderes Abonnement finden Sie unter [Verschieben eines Datenträgerpools in ein anderes Abonnement](disks-pools-move-resource.md).
- Informationen zum Deaktivieren der Bereitstellung eines Datenträgerpools finden Sie unter [Bereitstellung eines Azure-Datenträgerpools deaktivieren](disks-pools-deprovision.md).