---
title: Erstellen einer Azure-Momentaufnahme einer virtuellen Festplatte
description: Erfahren Sie, wie Sie eine Kopie einer Azure-VM als Sicherung oder für die Behandlung von Problemen mithilfe des Portals, PowerShell oder CLI erstellen.
author: roygara
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/16/2021
ms.openlocfilehash: 0a63e0f346fedac9b7178f25a19177a9908bc6a4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223005"
---
# <a name="create-a-snapshot-of-a-virtual-hard-disk"></a>Erstellen einer Momentaufnahme einer virtuellen Festplatte

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen

Eine Momentaufnahme ist eine vollständige, schreibgeschützte Kopie einer virtuellen Festplatte. Sie können eine Momentaufnahme als Zeitpunktsicherung oder zur Behandlung von Problemen mit virtuellen Computern (VM) verwenden. Sie können eine Momentaufnahme von Betriebssystem-VHDs oder Datenträger-VHDs erstellen.

## <a name="create-a-snapshot-of-a-vhd"></a>Erstellen einer Momentaufnahme einer VHD

Wenn Sie eine Momentaufnahme zum Erstellen eines neuen virtuellen Computers verwenden möchten, müssen Sie die VM zuerst sauber herunterfahren. Mit dieser Aktion werden alle Prozesse, die gerade stattfinden, beendet/entfernt.

# <a name="portal"></a>[Portal](#tab/portal)

Um eine Momentaufnahme über das Azure-Portal zu erstellen, führen Sie folgende Schritte aus.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Ressource erstellen**.
1. Suchen Sie nach **Momentaufnahme**, und wählen Sie diese Option aus.
1. Wählen Sie im Fenster **Momentaufnahme** die Option **Erstellen** aus. Das Fenster **Momentaufnahme erstellen** wird angezeigt.
1. Für eine **Ressourcengruppe** wählen Sie eine bestehende [Ressourcengruppe](../azure-resource-manager/management/overview.md#resource-groups) oder geben Sie den Namen einer neuen ein.
1. Geben Sie einen **Namen** ein, und wählen Sie dann **Region** und **Art der Momentaufnahme** für die neue Momentaufnahme aus. Wenn Sie die Momentaufnahme in einem Speicher speichern möchten, der für Zonen unempfindlich ist, müssen Sie eine Region auswählen, die [Verfügbarkeitszonen](../availability-zones/az-overview.md) unterstützt. Eine Liste mit unterstützenden Regionen finden Sie ihn [Azure Regionen mit Verfügbarkeitszonen](../availability-zones/az-region.md#azure-regions-with-availability-zones).
1. Wählen Sie unter  **Quellabonnement** das Abonnement aus, das den verwalteten Datenträger enthält, den Sie sichern wollen.
1. Wählen Sie für **Quelldatenträger** den verwalteten Datenträger aus, für den eine Momentaufnahme erstellt werden soll.
1. Bei **Speicherart** wählen Sie **HDD Standard** aus, es sei denn, Sie benötigen zonenredundanten Speicher oder Hochleistungsspeicher für Ihre Momentaufnahme.
1. Konfigurieren Sie bei Bedarf Einstellungen auf den Registerkarten **Verschlüsselung**, **Netzwerk** und **Tags**. Andernfalls werden Standardeinstellungen für Ihre Momentaufnahme verwendet.
1. Klicken Sie auf **Überprüfen + erstellen**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Im folgenden Beispiel muss [Cloud Shell](https://shell.azure.com/bash) verwendet werden oder[Azure CLI](/cli/azure/)installiert sein.

Führen Sie diese Schritte aus, um eine Momentaufnahme mit den `New-AzSnapshotConfig`und `New-AzSnapshot` Cmdlets zu erstellen. Beim folgenden Beispiel wird davon ausgegangen, dass eine VM namens *myVM* in der Ressourcengruppe *myResourceGroup* vorhanden ist. Das bereitgestellte Codebeispiel erstellt eine Momentaufnahme in derselben Ressourcengruppe und in derselben Region wie Ihre Quell-VM.

Zunächst verwenden Sie das Cmdlet [New-AzSnapshotConfig](/powershell/module/az.compute/new-azsnapshotconfig), um ein konfigurierbares Objekt für eine Momentaufnahme zu erstellen. Sie können dann eine Momentaufnahme des Datenträgers mit dem Cmdlet [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) erstellen.

1. Legen Sie die erforderlichen Parameter fest. Aktualisieren Sie die Werte entsprechend Ihrer Umgebung.

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

1. Verwenden Sie das Cmdlet [Get-AzVM,](/powershell/module/az.compute/get-azvm) um den virtuellen Computer mit der VHD zu erhalten, die Sie kopieren möchten.

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

1. Erstellen Sie die Momentaufnahmekonfiguration. In diesem Beispiel wird die Momentaufnahme des Betriebssystem-Datenträgers erstellt. Standardmäßig verwendet die Momentaufnahme lokal redundanten Standardspeicher. Es wird empfohlen, Ihre Momentaufnahmen im Standardspeicher und nicht im Premium-Speicher zu speichern, gleichgültig, welche die Speicherart des übergeordneten Datenträgers oder des Zieldatenträgers ist. Für Premium-Momentaufnahmen entstehen zusätzliche Kosten.

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```

   Wenn Sie Ihre Momentaufnahme in einem Speicher speichern möchten, der für Zonen unempfindlich ist, müssen Sie die Momentaufnahme in einer Region erstellen, die [Verfügbarkeitszonen](/azure/availability-zones/az-overview) unterstützt und den Parameter`-SkuName Standard_ZRS` enthält. Eine Liste von Regionen, die Verfügbarkeitszonen unterstützen, finden Sie unter [Azure-Regionen mit Verfügbarkeitszonen](../availability-zones/az-region.md#azure-regions-with-availability-zones).

1. Erstellen Sie die Momentaufnahme.

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```

1. Verwenden Sie das Cmdlet [Get-AzSnapshot,](/powershell/module/az.compute/get-azsnapshot) um zu überprüfen, ob Ihre Momentaufnahme vorhanden ist.

    ```azurepowershell-interactive
    Get-AzSnapshot `
        -ResourceGroupName $resourceGroupName
    ```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Im folgenden Beispiel muss [Cloud Shell](https://shell.azure.com/bash) verwendet werden oder [Azure CLI](/cli/azure/)installiert sein.

Führen Sie diese Schritte aus, um eine Momentaufnahme mit dem `az snapshot create` Befehl und dem`--source-disk` Parameter zu erstellen. Beim folgenden Beispiel wird davon ausgegangen, dass eine VM namens *myVM* in der Ressourcengruppe *myResourceGroup* vorhanden ist. Das bereitgestellte Codebeispiel erstellt eine Momentaufnahme in derselben Ressourcengruppe und in derselben Region wie Ihre Quell-VM.

1. Rufen Sie mit [az vm show](/cli/azure/vm#az_vm_show) die Datenträger-ID ab.

    ```azurecli-interactive
    osDiskId=$(az vm show \
       -g myResourceGroup \
       -n myVM \
       --query "storageProfile.osDisk.managedDisk.id" \
       -o tsv)
    ```

1. Erstellen Sie mit [az snapshot create](/cli/azure/snapshot#az_snapshot_create) eine Momentaufnahme namens *osDisk-backup*. In diesem Beispiel wird die Momentaufnahme des Betriebssystem-Datenträgers erstellt. Standardmäßig verwendet die Momentaufnahme lokal redundanten Standardspeicher. Es wird empfohlen, Ihre Momentaufnahmen im Standardspeicher und nicht im Premium-Speicher zu speichern, gleichgültig, welche die Speicherart des übergeordneten Datenträgers oder des Zieldatenträgers ist. Für Premium-Momentaufnahmen entstehen zusätzliche Kosten.

    ```azurecli-interactive
    az snapshot create \
        -g myResourceGroup \
        --source "$osDiskId" \
        --name osDisk-backup
    ```

    Wenn Sie die Momentaufnahme in einem Speicher speichern möchten, der für Zonen unempfindlich ist, müssen Sie sie in einer Region erstellen, die [Verfügbarkeitszonen](../availability-zones/az-overview.md) unterstützt, und den optionalen Parameter `--sku Standard_ZRS` einbeziehen. Eine Liste der [Verfügbarkeitszonen](../availability-zones/az-region.md#azure-regions-with-availability-zones) finden Sie hier.
    
1. Verwenden Sie [az snapshot list](/cli/azure/snapshot#az_snapshot_list), um zu überprüfen, ob Ihre Momentaufnahme vorhanden ist.
    
    ```azurecli-interactive
    az snapshot list \
       -g myResourceGroup \
       - table
    ```

---

## <a name="next-steps"></a>Nächste Schritte

Erstellen eines virtuellen Computers aus einer Momentaufnahme. Erstellen Sie aus der Momentaufnahme einen verwalteten Datenträger und hängen Sie den neuen verwalteten Datenträger als Betriebssystem-Datenträger an.

# <a name="portal"></a>[Portal](#tab/portal)

Für weitere Informationen siehe das Beispiel in [Erstellen einer VM aus einer VHD mithilfe des Azure Portals](windows/create-vm-specialized-portal.md).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Für weitere Informationen siehe das Beispiel in [Erstellen einer Windows-VM aus einem spezialisierten Datenträger unter Verwendung von PowerShell](windows/create-vm-specialized.md).

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Für weitere Informationen siehe das Beispiel in [Erstellen einer vollständigen Linux-VM mit der Azure CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot?toc=%2fcli%2fmodule%2ftoc.json).

---