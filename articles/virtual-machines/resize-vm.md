---
title: Ändern der Größe eines virtuellen Computers
description: Ändern der VM-Größe für einen virtuellen Azure-Computer
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/13/2021
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a95331f1b5cd0110cc38e60870ad9bfd5e4a16bd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466806"
---
# <a name="change-the-size-of-a-virtual-machine"></a>Ändern der Größe eines virtuellen Computers 

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

In diesem Artikel erfahren Sie, wie Sie einen virtuellen Computer auf eine andere [VM-Größe](sizes.md) verschieben.

Nachdem Sie einen virtuellen Computer (virtual machine; VM) erstellen, können Sie den virtuellen Computer zentral hoch- oder herunterskalieren, indem Sie die VM-Größe ändern. In einigen Fällen müssen Sie zuerst die Zuordnung des virtuellen Computers aufheben. Dies ist möglicherweise der Fall, falls die neue Größe auf dem Hardwarecluster nicht verfügbar ist, auf dem die VM aktuell gehostet wird.

Wenn Ihr virtueller Computer Storage Premium verwendet, stellen Sie sicher, dass Sie für die Größe eine **s**-Version auswählen, damit Storage Premium unterstützt wird. Wählen Sie z. B. Standard_E4 **s** _v3 statt Standard_E4_v3 aus.

## <a name="change-the-vm-size"></a>Ändern der VM-Größe

### <a name="portal"></a>[Portal](#tab/portal)

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Öffnen Sie die Seite für den virtuellen Computer.
1. Wählen Sie im linken Menü die Option **Größe** aus.
1. Wählen Sie in der Liste der verfügbaren Größen eine neue Größe und dann **Größe ändern** aus.


Wenn der virtuelle Computer zurzeit ausgeführt wird, führt eine Änderung seiner Größe zu einem Neustart. 

Wenn Ihr virtueller Computer noch ausgeführt wird und die von Ihnen gewünschte Größe in der Liste nicht angezeigt wird, werden beim Beenden des virtuellen Computers möglicherweise weitere Größen angezeigt.

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Zum Ändern der Größe eines virtuellen Computers muss die neueste [Azure CLI](/cli/azure/install-az-cli2) installiert sein, und Sie müssen über [az login](/cli/azure/reference-index) bei einem Azure-Konto angemeldet sein.

1. Zeigen Sie mit [az vm list-vm-resize-options](/cli/azure/vm) die Liste der verfügbaren VM-Größen in dem Hardwarecluster an, in dem der virtuelle Computer gehostet wird. Das folgende Beispiel listet VM-Größen für den virtuellen Computer `myVM` in der Region der Ressourcengruppe `myResourceGroup` auf:
   
    ```azurecli-interactive
    az vm list-vm-resize-options \
    --resource-group myResourceGroup \
    --name myVM --output table
    ```

2. Wenn die gewünschte VM-Größe aufgeführt wird, ändern Sie mithilfe von [az vm resize](/cli/azure/vm) die Größe des virtuellen Computers. Das folgende Beispiel ändert den virtuellen Computer `myVM` in die Größe `Standard_DS3_v2`:
   
    ```azurecli-interactive
    az vm resize \
    --resource-group myResourceGroup \
    --name myVM \
    --size Standard_DS3_v2
    ```
   
    Der virtuelle Computer wird während dieses Vorgangs neu gestartet. Nach dem Neustart werden Ihr vorhandenes Betriebssystem und die Datenträger neu zugeordnet. Alle Daten auf dem temporären Datenträger gehen verloren.

3. Wenn die gewünschte VM-Größe nicht aufgeführt wird, müssen Sie zuerst mit [az vm deallocate](/cli/azure/vm) die Zuordnung des virtuellen Computers aufheben. Nach diesem Vorgang kann der virtuelle Computer auf eine beliebige, in der Region unterstützte Größe geändert und dann gestartet werden. Mit den folgenden Schritten wird für den virtuellen Computer `myVM` in der Ressourcengruppe `myResourceGroup` die Zuordnung aufgehoben und die Größe geändert. Anschließend wird der Computer gestartet:
   
    ```azurecli-interactive
    # Variables will make this easier. Replace the values with your own.
    resourceGroup=myResourceGroup
    vm=myVM
    size=Standard_DS3_v2

    az vm deallocate \
    --resource-group $resourceGroup \
    --name myVM
    az vm resize \
    --resource-group $resourceGroup \
    --name $vm \
    --size $size
    az vm start \
    --resource-group $resourceGroup \
    --name $vm
    ```
   
   > [!WARNING]
   > Die Aufhebung der Zuordnung der VM gibt auch jegliche dynamische IP-Adressen frei, die der VM zugewiesen sind. Das Betriebssystem und die Datenträger sind nicht betroffen.

### <a name="powershell"></a>[PowerShell](#tab/powershell)

**Verwenden von PowerShell zum Ändern der Größe einer VM, die sich nicht in einer Verfügbarkeitsgruppe befindet**

Legen Sie einige Variablen fest. Ersetzen Sie die Werte durch Ihre eigenen Informationen.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Liste der VM-Größen, die in der Region, in der die VM gehostet wird, verfügbar sind. 
   
```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Führen Sie die folgenden Befehle aus, um die Größe des virtuellen Computers zu ändern, wenn die gewünschte Größe aufgelistet ist. Wenn die gewünschte Größe nicht aufgeführt ist, fahren Sie mit Schritt 3 fort.
   
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Führen Sie die folgenden Befehle aus, wenn die gewünschte Größe nicht aufgelistet ist. Damit heben Sie die Zuordnung des virtuellen Computers auf, ändern seine Größe, und starten ihn neu. Ersetzen Sie **\<newVMsize>** durch die gewünschte Größe.
   
```azurepowershell-interactive
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Die Aufhebung der Zuordnung der VM gibt alle dynamischen IP-Adressen frei, die der VM zugewiesen sind. Das Betriebssystem und die Datenträger sind nicht betroffen. 
> 
> 

**Verwenden von PowerShell zum Ändern der Größe einer VM in einer Verfügbarkeitsgruppe**

Wenn die neue Größe für einen virtuellen Computer in einer Verfügbarkeitsgruppe nicht im Hardwarecluster verfügbar ist, auf dem der virtuelle Computer derzeit gehostet wird, muss bei allen virtuellen Computern in der Verfügbarkeitsgruppe die Zuordnung aufgehoben werden, um die Größe des virtuellen Computers ändern zu können. Sie müssen möglicherweise auch die Größe der anderen virtuellen Computer in der Verfügbarkeitsgruppe aktualisieren, nachdem die Größe eines virtuellen Computers geändert wurde. Führen Sie die folgenden Schritte zum Ändern der Größe eines virtuellen Computers in einer Verfügbarkeitsgruppe aus.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Listet die VM-Größen auf, die auf dem Hardwarecluster verfügbar sind, auf dem die VM gehostet wird. 
   
```azurepowershell-interactive
Get-AzVMSize `
-ResourceGroupName $resourceGroup `
-VMName $vmName 
```

Führen Sie die folgenden Befehle aus, um die Größe der VM zu ändern, sofern die gewünschte Größe aufgelistet ist. Wenn sie nicht aufgelistet ist, fahren Sie mit dem nächsten Abschnitt fort.
   
```azurepowershell-interactive
$vm = Get-AzVM `
-ResourceGroupName $resourceGroup `
-VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM `
-VM $vm `
-ResourceGroupName $resourceGroup
```
    
Wenn die gewünschte Größe nicht aufgelistet ist, fahren Sie mit den folgenden Schritten fort. Damit heben Sie die Zuordnung aller virtuellen Computer in der Verfügbarkeitsgruppe auf, skalieren die VMs, und starten sie neu.

Beenden Sie alle virtuellen Computer in der Verfügbarkeitsgruppe.
   
```azurepowershell-interactive
$availabilitySetName = "<availabilitySetName>"
$as = Get-AzAvailabilitySet `
-ResourceGroupName $resourceGroup `
-Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines |  Stop-AzVM -Force -NoWait  
```

Skalieren Sie alle virtuellen Computer in der Verfügbarkeitsgruppe und starten sie neu.
   
```azurepowershell-interactive
$availabilitySetName = "<availabilitySetName>"
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines | Foreach-Object { $_.HardwareProfile.VmSize = $newSize }
$virtualMachines | Update-AzVM
$virtualMachines | Start-AzVM
```

---

## <a name="next-steps"></a>Nächste Schritte

Führen Sie mehrere VM-Instanzen aus, und skalieren Sie diese auf, um zusätzliche Skalierbarkeit zu erhalten. Weitere Informationen finden Sie unter [Automatisches Skalieren von Computern in einer VM-Skalierungsgruppe](../virtual-machine-scale-sets/tutorial-autoscale-powershell.md).
