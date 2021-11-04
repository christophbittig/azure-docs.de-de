---
title: Verwalten eines Azure-Datenträgerpools (Vorschau)
description: In diesem Artikel erfahren Sie, wie Sie einem Azure-Datenträgerpool verwaltete Datenträger hinzufügen oder die iSCSI-Unterstützung auf einem Datenträger deaktivieren.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 59f03b2484bed39a3c562efc6cfb4176b5173964
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083201"
---
# <a name="manage-an-azure-disk-pool-preview"></a>Verwalten eines Azure-Datenträgerpools (Vorschau)

In diesem Artikel wird beschrieben, wie Sie einem Azure-Datenträgerpool (Vorschau) einen verwalteten Datenträger hinzufügen und die iSCSI-Unterstützung auf einem Datenträger deaktivieren, der einem Datenträgerpool hinzugefügt wurde.

## <a name="add-a-disk-to-a-pool"></a>Hinzufügen eines Datenträgers zu einem Pool

Ihr Datenträger muss die folgenden Anforderungen erfüllen, um dem Datenträgerpool hinzugefügt zu werden:
- Es muss sich entweder um einen SSD Premium-, einen SSD Standard- oder einen Ultra-Datenträger in derselben Region und Verfügbarkeitszone wie der Datenträgerpool handeln.
    - Bei Ultra-Datenträgern muss die Sektorgröße des Datenträgers 512 Byte betragen.
- Es muss sich um einen freigegebenen Datenträger mit einem maxShares-Wert von mindestens 2 handeln.
- Sie müssen [die RBAC-Berechtigungen des StoragePool-Ressourcenanbieters für die Datenträger bereitstellen, die dem Datenträgerpool hinzugefügt werden](disks-pools-deploy.md#assign-storagepool-resource-provider-permissions).

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) an.
1. Navigieren Sie zu Ihrem Datenträgerpool, und klicken Sie unter **Einstellungen** auf die Option **Datenträger**.
1. Klicken Sie auf **Vorhandenen Datenträger anfügen**, und wählen Sie Ihre Datenträger aus.
1. Wenn Sie alle Datenträger ausgewählt haben, die Sie anfügen möchten, klicken Sie auf **Speichern**.

    :::image type="content" source="media/disk-pools-manage/manage-disk-pool-add.png" alt-text="Screenshot des Blatts „Datenträger“ für Ihren Datenträgerpool":::

    Nachdem Sie die Datenträger angefügt haben, müssen Sie nun deren LUNs aktivieren.

1. Wählen Sie unter **Einstellungen** die Option **iSCSI** aus.
1. Klicken Sie unter **Für iSCSI aktivierte Datenträger** auf die Option **LUN hinzufügen**.
1. Wählen Sie den Datenträger aus, den Sie zuvor angefügt haben.
1. Wählen Sie **Speichern** aus.

    :::image type="content" source="media/disk-pools-manage/enable-disk-luns.png" alt-text="Screenshot des Blatts „iSCSI“ mit aktivierten LUNs der hinzugefügten Datenträger":::

Nachdem Sie Ihren Datenträger angefügt und die LUN aktiviert haben, müssen Sie ihn als iSCSI-Datenspeicher erstellen und an Ihre private Azure VMware Solution-Cloud anfügen. Weitere Informationen finden Sie unter [Anfügen der iSCSI-LUN](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md#attach-the-iscsi-lun).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="prerequisites"></a>Voraussetzungen

Installieren Sie das Azure PowerShell-Modul [Version 6.1.0 oder höher](/powershell/module/az.diskpool/?view=azps-6.1.0&preserve-view=true).

Verwenden Sie den folgenden Befehl, um das Modul für den Datenträgerpool zu installieren:

```azurepowershell
Install-Module -Name Az.DiskPool -RequiredVersion 0.3.0 -Repository PSGallery
```

### <a name="add-a-disk-pool"></a>Hinzufügen eines Datenträgerpools

Das folgende Skript fügt dem Datenträgerpool einen zusätzlichen Datenträger hinzu, und macht ihn über iSCSI verfügbar. Die vorhandenen Datenträger bleiben im Datenträgerpool unverändert.

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<yourDiskName>" #Provide the name of the disk you want to add
$lunName ='<LunName>' #Provide the Lun name of the added disk
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

Nachdem Sie Ihren Datenträger angefügt und die LUN aktiviert haben, müssen Sie ihn als iSCSI-Datenspeicher erstellen und an Ihre private Azure VMware Solution-Cloud anfügen. Weitere Informationen finden Sie unter [Anfügen der iSCSI-LUN](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md#attach-the-iscsi-lun).

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

### <a name="prerequisites"></a>Voraussetzungen

Installieren Sie die [aktuelle Version](/cli/azure/disk-pool) der Azure CLI.

Falls Sie dies noch nicht getan haben, installieren Sie die Erweiterung für den Datenträgerpool mithilfe des folgenden Befehls:

```azurecli
az extension add -n diskpool
```

### <a name="add-a-disk-pool---cli"></a>Hinzufügen eines Datenträgerpools mit der CLI

Das folgende Skript fügt dem Datenträgerpool einen zusätzlichen Datenträger hinzu, und macht ihn über iSCSI verfügbar. Die vorhandenen Datenträger bleiben im Datenträgerpool unverändert.

```azurecli
# Add a disk to a disk pool

# Initialize parameters
resourceGroupName="<yourResourceGroupName>"
diskPoolName="<yourDiskPoolName>"
iscsiTargetName="<youriSCSITargetName>"
diskName="<yourDiskName>"
lunName="<LunName>"

diskPoolUpdateArgs=("$@")
diskPoolUpdateArgs+=(--resource-group $resourceGroupName --Name $diskPoolName)

diskIds=$(echo $(az disk-pool show --name $diskPoolName --resource-group $resourceGroupName --query disks[].id -o json) | sed -e 's/\[ //g' -e 's/\ ]//g' -e 's/\,//g')
for disk in $diskIds; do
    diskPoolUpdateArgs+=(--disks $(echo $disk | sed 's/"//g'))
done

diskId=$(az disk show --resource-group $resourceGroupName --name $diskName --query id | sed 's/"//g')
diskPoolUpdateArgs+=(--disks $diskId)

az disk-pool update "${diskPoolUpdateArgs[@]}"

# Get existing iSCSI LUNs and expose added disk as a new LUN
targetUpdateArgs=("$@")
targetUpdateArgs+=(--resource-group $resourceGroupName --disk-pool-name $diskPoolName --name $iscsiTargetName)

luns=$(az disk-pool iscsi-target show --name $iscsiTargetName --disk-pool-name $diskPoolName --resource-group $resourceGroupName --query luns)
lunsCounts=$(echo $luns | jq length)

for (( i=0; i < $lunCounts; i++ )); do
    tmpLunName=$(echo $luns | jq .[$i].name | sed 's/"//g')
    tmpLunId=$(echo $luns | jq .[$i].managedDiskAzureResourceId | sed 's/"//g')
    targetUpdateArgs+=(--luns name=$tmpLunName managed-disk-azure-resource-id=$tmpLunId)
done

targetUpdateArgs+=(--luns name=$lunName managed-disk-azure-resource-id=$diskId)

az disk-pool iscsi-target update "${targetUpdateArgs[@]}"
```

Nachdem Sie Ihren Datenträger angefügt und die LUN aktiviert haben, müssen Sie ihn als iSCSI-Datenspeicher erstellen und an Ihre private Azure VMware Solution-Cloud anfügen. Weitere Informationen finden Sie unter [Anfügen der iSCSI-LUN](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md#attach-the-iscsi-lun).

---

## <a name="disable-iscsi-on-a-disk-and-remove-it-from-the-pool"></a>Deaktivieren von iSCSI auf einem Datenträger und Entfernen aus dem Pool

Bevor Sie die iSCSI-Unterstützung auf einem Datenträger deaktivieren, vergewissern Sie sich, dass keine ausstehenden iSCSI-Verbindungen mit der iSCSI-LUN bestehen, unter der der Datenträger verfügbar gemacht wird. Wenn ein Datenträger aus dem Datenträgerpool entfernt wird, wird er nicht automatisch gelöscht. Dies verhindert jeglichen Datenverlust, aber Ihnen wird weiterhin die Speicherung von Daten in Rechnung gestellt. Wenn Sie die auf einem Datenträger gespeicherten Daten nicht benötigen, können Sie den Datenträger manuell löschen. Dadurch werden der Datenträger und alle darauf gespeicherten Daten gelöscht, und weitere Gebühren werden verhindert.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) an.
1. Navigieren Sie zu Ihrem Datenträgerpool, und klicken Sie unter **Einstellungen** auf die Option **iSCSI**.
1. Wählen Sie unter **Für iSCSI aktivierte Datenträger** die Datenträger aus, die Sie entfernen möchten, und klicken Sie auf **LUN entfernen**.
1. Klicken Sie auf **Speichern**, und warten Sie, bis der Vorgang abgeschlossen ist.

    :::image type="content" source="media/disk-pools-manage/remove-disk-lun.png" alt-text="Screenshot des Blatts „Für iSCSI aktivierte Datenträger“ mit den entfernten Datenträger-LUNs":::

    Nachdem Sie die LUN deaktiviert haben, können Sie Ihre Datenträger aus dem Datenträgerpool entfernen.

1. Klicken Sie unter **Einstellungen** auf die Option **Datenträger**.
1. Klicken Sie auf **Datenträger aus Datenträgerpool entfernen**, und wählen Sie Ihre Datenträger aus.
1. Wählen Sie **Speichern** aus.

Nach Abschluss des Vorgangs ist Ihr Datenträger vollständig aus dem Datenträgerpool entfernt.

:::image type="content" source="media/disk-pools-manage/remove-disks-from-pool.png" alt-text="Screenshot des Blatts „Datenträger aus Datenträgerpool entfernen“ mit den aus dem Pool entfernten Datenträgern":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<NameOfDiskYouWantToRemove>" #Provide the name of the disk you want to remove
$lunName ='<LunForDiskYouWantToRemove>' #Provide the Lun name of the disk you want to remove
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

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
# Disable iSCSI on a disk and remove it from the pool

# Initialize parameters
resourceGroupName="<yourResourceGroupName>"
diskPoolName="<yourDiskPoolName>"
iscsiTargetName="<youriSCSITargetName>"
diskName="<yourDiskName>"
lunName="<LunName>"

# Get existing iSCSI LUNs and remove it from iSCSI target
targetUpdateArgs=("$@")
targetUpdateArgs+=(--resource-group $resourceGroupName --disk-pool-name $diskPoolName --name $iscsiTargetName)

luns=$(az disk-pool iscsi-target show --name $iscsiTargetName --disk-pool-name $diskPoolName --resource-group $resourceGroupName --query luns)
lunCounts=$(echo $luns | jq length)

for (( i=0; i < $lunCounts; i++ )); do
    tmpLunName=$(echo $luns | jq .[$i].name | sed 's/"//g')
    if [ $tmpLunName != $lunName ]; then
        tmpLunId=$(echo $luns | jq .[$i].managedDiskAzureResourceId | sed 's/"//g')
        targetUpdateArgs+=(--luns name=$tmpLunName managed-disk-azure-resource-id=$tmpLunId)
    fi
done

az disk-pool iscsi-target update "${targetUpdateArgs[@]}"

# Remove disk from pool
diskId=$(az disk show --resource-group $resourceGroupName --name $diskName -- query id | sed 's/"//g')

diskPoolUpdateArgs=("$@")
diskPoolUpdateArgs+=(--resource-group $resourceGroupName --name $diskPoolName)

diskIds=$(az disk-pool show --name $diskPoolName --resource-group $resourceGroupName --query disks[].id -o json)
diskLength=$(echo diskIds | jq length)

for (( i=0; i < $diskLength; i++ )); do
    tmpDiskId=$(echo $diskIds | jq .[$i] | sed 's/"//g')

    if [ $tmpDiskId != $diskId ]; then
        diskPoolUpdateArgs+=(--disks $tmpDiskId)
    fi
done

az disk-pool update "${diskPoolUpdateArgs[@]}"
```

---
## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Verschieben eines Datenträgerpools in ein anderes Abonnement finden Sie unter [Verschieben eines Datenträgerpools in ein anderes Abonnement](disks-pools-move-resource.md).
- Informationen zum Deaktivieren der Bereitstellung eines Datenträgerpools finden Sie unter [Bereitstellung eines Azure-Datenträgerpools deaktivieren](disks-pools-deprovision.md).
