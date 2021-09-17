---
title: Konvertieren von verwaltetem Datenträgerspeicher zwischen verschiedenen Datenträgertypen mithilfe der Azure CLI
description: Vorgehensweisen beim Konvertieren von verwalteten Datenträgern zwischen den verschiedenen Datenträgertypen mithilfe der Azure CLI.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.subservice: disks
ms.openlocfilehash: 385d801d43a41bb836e04398427fff6c01b1e357
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692129"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Konvertieren verwalteter Azure-Datenträger zwischen Storage Standard und Storage Premium

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Es gibt vier Typen verwalteter Azure-Datenträger: Azure Disk Ultra, SSD Premium, SSD Standard und HDD Standard. Sie können basierend auf Ihren Leistungsanforderungen zwischen den Datenträgertypen SSD Premium, SSD Standard und HDD Standard wechseln. Derzeit ist noch kein Wechsel von oder zu Disk Ultra möglich. Sie müssen in diesem Fall einen neuen Datenträger bereitstellen.

Diese Funktion wird für nicht verwaltete Datenträger nicht unterstützt. Sie können jedoch problemlos [nicht verwaltete Datenträger in verwaltete Datenträger konvertieren](convert-unmanaged-to-managed-disks.md), um zwischen den Datenträgertypen wechseln zu können.

Dieser Artikel zeigt, wie Sie verwaltete Datenträger mithilfe der Azure-CLI aus einem Datenträgertyp in einen anderen konvertieren können. Informationen zum Ausführen einer Installation oder eines Upgrades für das Tool finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Voraussetzungen

* Da für die Datenträgerkonvertierung ein Neustart des virtuellen Computers (VM) erforderlich ist, sollten Sie die Migration Ihres Datenträgerspeichers während eines bereits bestehenden Wartungsfensters durchführen.
* Konvertieren Sie nicht verwaltete Datenträger zuerst [in verwaltete Datenträger](convert-unmanaged-to-managed-disks.md), damit Sie zwischen den Speicheroptionen wechseln können.


## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Umstellen aller verwalteten Datenträger einer VM von einem Konto auf ein anderes

Dieses Beispiel zeigt, wie alle Datenträger einer VM in Storage Premium konvertiert werden. Durch Ändern der Variable sku in diesem Beispiel können Sie jedoch den Datenträgertyp der VM in SSD Standard oder HDD Standard konvertieren. Hinweis: Zur Verwendung von verwalteten Premium-Datenträgern muss Ihr virtueller Computer eine [VM-Größe](../sizes.md) verwenden, die Storage Premium unterstützt. In diesem Beispiel erfolgt auch ein Wechsel zu einer Größe, die Storage Premium unterstützt.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-from-one-disk-type-to-another"></a>Umstellen einzelner verwalteter Datenträger aus einem Datenträgertyp in einen anderen

Für Ihre Entwicklungs-/Testworkload sollten Sie eine Kombination aus Standard- und Premium-Datenträgern verwenden, um Ihre Kosten zu senken. Sie haben auch die Möglichkeit, nur die Datenträger upzugraden, die eine höhere Leistung erfordern. Dieses Beispiel zeigt, wie Sie einen einzelnen VM-Datenträger von Standard in Premium Storage konvertieren. Durch Ändern der Variable sku in diesem Beispiel können Sie jedoch den Datenträgertyp der VM in SSD Standard oder HDD Standard konvertieren. Zur Verwendung von verwalteten Premium-Datenträgern muss Ihre VM eine [VM-Größe](../sizes.md) verwenden, die Premium Storage unterstützt. In diesem Beispiel erfolgt auch ein Wechsel zu einer Größe, die Storage Premium unterstützt.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Umstellen von verwalteten Datenträgern aus einem Datenträgertyp in einen anderen

Folgen Sie diesen Schritten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie den virtuellen Computer in der Liste der **VMs** aus.
3. Wenn der virtuelle Computer nicht beendet ist, wählen Sie im oberen Bereich unter **Übersicht** für den virtuellen Computer **Beenden** aus, und warten Sie, bis der virtuellen Computer beendet wurde.
4. Wählen Sie im Bereich für den virtuellen Computer im Menü die Option **Datenträger** aus.
5. Wählen Sie den Datenträger aus, den Sie konvertieren möchten.
6. Wählen Sie im Menü die Option **Konfiguration** aus.
7. Ändern Sie den **Kontotyp** aus dem ursprünglichen Datenträgertyp in den gewünschten Datenträgertyp.
8. Wählen Sie **Speichern** aus, und schließen Sie den Datenträgerbereich.

Das Update des Datenträgertyps erfolgt sofort. Sie können Ihren virtuellen Computer nach der Konvertierung neu starten.

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine schreibgeschützte Kopie eines virtuellen Computers mit [Momentaufnahmen](snapshot-copy-managed-disk.md).