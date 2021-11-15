---
title: Erstellen eines verwalteten Images in Azure
description: Erstellen Sie ein verwaltetes Image eines generalisierten virtuellen Computers oder einer VHD in Azure. Mit Images können mehrere virtuelle Computer erstellt werden, die verwaltete Datenträger verwenden.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/27/2018
ms.author: cynthn
ms.custom: legacy
ms.collection: windows
ms.openlocfilehash: 95f57b01f2c9e6bffd0cfc1f1d563605e320d6ba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437009"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Erstellen eines verwalteten Images eines generalisierten virtuellen Computers in Azure

**Gilt für:** :heavy_check_mark: Windows-VMs 


Eine verwaltete Imageressource kann aus einem generalisierten virtuellen Computer erstellt werden, der entweder als verwalteter Datenträger oder als nicht verwalteter Datenträger in einem Speicherkonto gespeichert ist. Mit diesem Image können dann mehrere virtuelle Computer erstellt werden. Weitere Informationen dazu, wie verwaltete Images abgerechnet werden, finden Sie unter [Verwaltete Datenträger – Preise](https://azure.microsoft.com/pricing/details/managed-disks/). 

Ein verwaltetes Image unterstützt bis zu 20 Bereitstellungen gleichzeitig. Wenn Sie versuchen, mehr als 20 VMs gleichzeitig aus demselben verwalteten Image zu erstellen, kann dies aufgrund der Einschränkungen bei der Speicherleistung einer einzelnen VHD zu Timeouts bei der Bereitstellung führen. Wenn Sie mehr als 20 virtuelle Computer gleichzeitig erstellen möchten, verwenden Sie ein Image für [Azure Compute Gallery](../shared-image-galleries.md) (früher Shared Image Gallery genannt), das mit jeweils einem Replikat pro 20 gleichzeitiger Bereitstellungen an virtuellen Computern konfiguriert wurde.

## <a name="prerequisites"></a>Voraussetzungen

Für die Imageerstellung wird ein [generalisierter](../generalize.md) virtueller Computer benötigt.

## <a name="create-a-managed-image-in-the-portal"></a>Erstellen eines verwalteten Images im Portal 

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um das VM-Image zu verwalten. Suchen Sie nach **Virtuelle Computer**, und wählen Sie diese Option aus.

2. Wählen Sie Ihren virtuellen Computer in der Liste aus.

3. Wählen Sie auf der Seite **Virtueller Computer** des virtuellen Computers im oberen Menü die Option **Erfassen** aus.

   Die Seite **Image erstellen** wird angezeigt.

4. Übernehmen Sie unter **Name** entweder den bereits angegebenen Wert, oder geben Sie einen Namen ein, der für das Image verwendet werden soll.

5. Wählen Sie unter **Ressourcengruppe** entweder **Neu erstellen** aus, und geben Sie einen Namen ein, oder wählen Sie in der Dropdownliste eine Ressourcengruppe aus.

6. Wenn Sie die Quell-VM nach der Erstellung des Image löschen möchten, wählen Sie **Diesen virtuellen Computer nach dem Erstellen des Images automatisch löschen** aus.

7. Wenn es möglich sein soll, das Image in jeder [Verfügbarkeitszone](../../availability-zones/az-overview.md) zu verwenden, wählen Sie für **Zonenresilienz** die Option **Ein**.

8. Wählen Sie **Erstellen** aus, um das Image zu erstellen.

Nachdem das Image erstellt wurde, wird es in der Ressourcengruppe in der Liste der Ressourcen als **Image** angezeigt.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Erstellen eines VM-Images mithilfe von PowerShell

 

Durch Erstellen eines Images direkt von einem virtuellen Computer lässt sich sicherstellen, dass das Image alle Datenträger umfasst, die dem virtuellen Computer zugeordnet sind, einschließlich des Betriebssystemdatenträgers und aller Datenträger für Daten. In diesem Beispiel wird gezeigt, wie ein verwaltetes Image von einer VM mit verwalteten Datenträgern erstellt wird.

Stellen Sie vor Beginn sicher, dass Sie die neueste Version des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` in PowerShell aus, um die entsprechende Version zu ermitteln. Informationen zum Ausführen eines Upgrades finden Sie unter [Installieren von Azure PowerShell unter Windows mit PowerShellGet](/powershell/azure/install-az-ps). Wenn Sie PowerShell lokal ausführen, führen Sie `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen.


> [!NOTE]
> Wenn Sie das Image in einem zonenredundanten Speicher speichern möchten, müssen Sie es in einer Region erstellen, die [Verfügbarkeitszonen](../../availability-zones/az-overview.md) unterstützt, und den `-ZoneResilient`-Parameter in die Imagekonfiguration einbeziehen (Befehl `New-AzImageConfig`).

Führen Sie die folgenden Schritte aus, um ein VM-Image zu erstellen:

1. Erstellen Sie einige Variablen.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Stellen Sie sicher, dass die Zuordnung des virtuellen Computers aufgehoben wurde.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Legen Sie den Status des virtuellen Computers auf **Generalisiert** fest. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Rufen Sie den virtuellen Computer ab. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Erstellen Sie die Imagekonfiguration.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Erstellen Sie das Image.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Erstellen eines Image von einem verwalteten Datenträger mithilfe von PowerShell

Wenn Sie nur ein Image des Betriebssystemdatenträgers erstellen möchten, geben Sie die ID des verwalteten Datenträger als Betriebssystemdatenträger an:

    
1. Erstellen Sie einige Variablen. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Rufen Sie die VM ab.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Rufen Sie die ID des verwalteten Datenträgers ab.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Erstellen Sie die Imagekonfiguration.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Erstellen Sie das Image.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Erstellen eines Images aus einer Momentaufnahme mithilfe von PowerShell

Sie können ein verwaltetes Image aus einer Momentaufnahme eines generalisierten virtuellen Computers erstellen. Führen Sie dazu die folgenden Schritte aus:

    
1. Erstellen Sie einige Variablen. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Rufen Sie die Momentaufnahme ab.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Erstellen Sie die Imagekonfiguration.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Erstellen Sie das Image.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Erstellen eines Images aus einer VM, für die ein Speicherkonto verwendet wird

Um ein verwaltetes Image aus einer VM zu erstellen, für die keine verwalteten Datenträger verwendet werden, benötigen Sie den URI der Betriebssystem-VHD im Speicherkonto im folgenden Format: „https://*meinspeicherkonto*.blob.core.windows.net/*vhdcontainer*/*vhddateiname.vhd*“. In diesem Beispiel befindet sich die VHD unter *mystorageaccount* in einem Container namens *vhdcontainer*, und der VHD-Dateiname lautet *vhdfilename.vhd*.


1.  Erstellen Sie einige Variablen.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Beenden Sie den virtuellen Computer, oder heben Sie seine Zuordnung auf.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Kennzeichnen Sie den virtuellen Computer als generalisiert.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Erstellen Sie das Image mithilfe Ihrer generalisierten Betriebssystem-VHD.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Nächste Schritte
- [Erstellen eines virtuellen Computers aus einem verwalteten Image](create-vm-generalized-managed.md) 
