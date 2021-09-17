---
title: Austauschen des Betriebssystem-Datenträgers für eine Azure-VM mit PowerShell
description: Ändern Sie den von einem virtuellen Azure-Computer verwendeten Betriebssystem-Datenträger mithilfe von PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2892ae3ff10110c2e45d29edd009a9116da0b166
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687506"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Ändern des von einem virtuellen Azure-Computer verwendeten Betriebssystem-Datenträgers mithilfe von PowerShell

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Wenn Sie über einen virtuellen Computer verfügen, aber den Datenträger für einen Sicherungsdatenträger oder einem anderen Betriebssystem-Datenträger austauschen möchten, können Sie die Betriebssystem-Datenträger mit Azure PowerShell austauschen. Sie müssen den virtuellen Computer nicht löschen und neu erstellen. Sie können auch einen verwalteten Datenträger in einer anderen Ressourcengruppe verwenden, solange er nicht bereits in Gebrauch ist.

 

Der virtuelle Computer muss sich im Zustand „Beendet (Zuordnung aufgehoben)“ befinden, und dann kann die Ressourcen-ID des verwalteten Datenträgers durch die Ressourcen-ID eines anderen verwalteten Datenträgers ersetzt werden.

Stellen Sie sicher, dass VM-Größe und Speichertyp mit dem Datenträger kompatibel sind, den Sie anfügen möchten. Wenn der Datenträger, den Sie verwenden möchten, sich z.B. im Storage Premium befindet, dann muss der virtuelle Computer für Storage Premium geeignet sein (z.B. eine Größe der DS-Serie). Beide Datenträger müssen ebenfalls dieselbe Größe haben.
Und achten Sie darauf, dass Sie nicht eine verschlüsselte VM mit einem verschlüsselten Betriebssystemdatenträger mischen. Dies wird nicht unterstützt. Wenn Azure Disk Encryption nicht für die VM verwendet wird, darf auf dem Betriebssystem-Datenträger, der als Austauschdatenträger fungiert, nicht Azure Disk Encryption verwendet werden. Wenn Datenträger Datenträgerverschlüsselungssätze verwenden, müssen beide Datenträger zu demselben Datenträgerverschlüsselungssatz gehören.

Abrufen einer Liste von Datenträgern in einer Ressourcengruppe mit [Get-AzDisk](/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Wenn Sie den Namen des Datenträgers kennen, den Sie verwenden möchten, legen Sie diesen als Betriebssystem-Datenträger für den virtuellen Computer fest. Dieses Beispiel beendet den virtuellen Computer mit dem Namen *myVM* (hebt seine Zuordnung auf) und weist den Datenträger namens *newDisk* als neuen Betriebssystem-Datenträger zu. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Nächste Schritte**

Wie Sie eine Kopie eines Datenträgers erstellen, erfahren Sie unter [Erstellen einer Momentaufnahme](snapshot-copy-managed-disk.md).
