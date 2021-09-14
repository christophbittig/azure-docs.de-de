---
title: Automatisches Erweiterungsupgrade für VMs und Skalierungsgruppen in Azure
description: Erfahren Sie, wie Sie das automatische Erweiterungsupgrade für Ihre virtuellen Computer und VM-Skalierungsgruppen in Azure aktivieren.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-extension-upgrade
ms.workload: infrastructure
ms.topic: how-to
ms.date: 08/10/2021
ms.author: manayar
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6af80da8c05df4f56fe04ae45169c5e1a63fcd9c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340305"
---
# <a name="automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>Automatisches Erweiterungsupgrade für VMs und Skalierungsgruppen in Azure

Das automatische Erweiterungsupgrade ist für Azure-VMs und Azure Virtual Machine Scale Sets verfügbar. Wenn das automatische Erweiterungsupgrade für eine VM oder eine Skalierungsgruppe aktiviert ist, wird die Erweiterung automatisch aktualisiert, sobald der Erweiterungsherausgeber eine neue Version für diese Erweiterung freigibt.

 Das automatische Erweiterungsupgrade verfügt über die folgenden Features:
- Wird für Azure-VMs und Azure-Virtual Machine Scale Sets unterstützt.
- Upgrades werden in einem verfügbarkeitsbasierten Bereitstellungsmodell angewendet (siehe unten).
- Für eine VM-Skalierungsgruppe werden maximal 20 Prozent der virtuellen Computer in Skalierungsgruppen in einem einzelnen Batch aktualisiert. Die minimale Batchgröße ist ein virtueller Computer.
- Unterstützt alle VM-Größen und sowohl Windows- als auch Linux-Erweiterungen.
- Sie können automatische Upgrades jederzeit kündigen.
- Das automatische Erweiterungsupgrade kann für Virtual Machine Scale Sets beliebiger Größe aktiviert werden.
- Jede unterstützte Erweiterung wird einzeln registriert, und Sie können auswählen, welche Erweiterungen automatisch aktualisiert werden sollen.
- Wird in allen Regionen der öffentlichen Cloud unterstützt.

## <a name="how-does-automatic-extension-upgrade-work"></a>Wie funktioniert das automatische Erweiterungsupgrade?
Der Erweiterungsupgradeprozess ersetzt die vorhandene Erweiterungsversion auf einer VM bei Veröffentlichung durch den Erweiterungsherausgeber durch eine neue Version derselben Erweiterung. Die Integrität der VM wird überwacht, nachdem die neue Erweiterung installiert wurde. Wenn sich die VM innerhalb von 5 Minuten nach dem Upgrade nicht in einem fehlerfreien Zustand befindet, wird für die Erweiterungsversion ein Rollback auf die vorherige Version ausgeführt.

Ein fehlerhaftes Erweiterungsupdate wird automatisch wiederholt. Ein Wiederholungsversuch wird regelmäßig nach einigen Tagen ohne Benutzereingriff automatisch durchgeführt.

### <a name="availability-first-updates"></a>Verfügbarkeitsupdates
Mit dem Verfügbarkeitsmodell für plattformorchestrierte Updates wird sichergestellt, dass die Verfügbarkeitskonfigurationen in Azure über mehrere Verfügbarkeitsstufen hinweg berücksichtigt werden.

Für eine Gruppe von virtuellen Computern, die ein Update durchlaufen, orchestriert die Azure-Plattform Updates wie folgt:

**Über Regionen hinweg:**
- Ein Update wird in Phasen global in Azure ausgeführt, um Azure-weite Bereitstellungsausfälle zu vermeiden.
- Eine „Phase“ kann sich über eine oder mehrere Regionen erstrecken, und ein Update ist nur dann Phasen übergreifend, wenn die in Betracht kommenden VMs in der vorherigen Phase erfolgreich aktualisiert wurden.
- Georegionspaare werden nicht gleichzeitig aktualisiert und können sich nicht in der gleichen Regionsphase befinden.
- Der Erfolg eines Updates wird durch die Nachverfolgung der Integrität nach dem Update einer VM gemessen. Die VM-Integrität wird durch Integritätsindikatoren der Plattform für die VM nachverfolgt. Für Virtual Machine Scale Sets wird die Integrität der VM durch Anwendungsintegritätstests oder die Anwendungsintegritätserweiterung nachverfolgt, wenn diese auf die Skalierungsgruppe angewendet werden.

**Innerhalb einer Region:**
- VMs in verschiedenen Verfügbarkeitszonen werden nicht gleichzeitig mit demselben Update aktualisiert.
- Einzelne VMs, die nicht Teil einer Verfügbarkeitsgruppe sind, werden nach bestem Wissen in einem Batch zusammengefasst, um gleichzeitige Updates für alle VMs in einem Abonnement zu vermeiden.  

**Innerhalb einer „Gruppe“:**
- Alle VMs in einer gemeinsamen Verfügbarkeitsgruppe oder Skalierungsgruppe werden nicht gleichzeitig aktualisiert.  
- VMs in einer gemeinsamen Verfügbarkeitsgruppe werden innerhalb der Grenzen der Updatedomäne aktualisiert, und VMs in mehreren Updatedomänen werden nicht gleichzeitig aktualisiert.  
- VMs in einer gemeinsamen VM-Skalierungsgruppe werden in Batches gruppiert und innerhalb der Grenzen von Upgradedomänen aktualisiert.

### <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Upgradeprozess für Virtual Machine Scale Sets
1. Bevor mit dem Upgradeprozess begonnen wird, stellt der Orchestrator sicher, dass nicht mehr als 20 % der VMs in der gesamten Skalierungsgruppe (aus beliebigem Grund) fehlerhaft sind.

2. Der Upgradeorchestrator identifiziert den Batch der VM-Instanzen, die aktualisiert werden sollen. Ein Upgradebatch kann maximal 20 % der VM-Gesamtzahl umfassen, eine minimale Batchgröße von einer VM vorausgesetzt.

3. Für Skalierungsgruppen mit konfigurierten Anwendungsintegritätstests oder einer Anwendungsintegritätserweiterung wartet das Upgrade bis zu 5 Minuten (oder den konfigurierten Wert des Integritätstests) darauf, dass die VM fehlerfrei wird, bevor der nächste Batch aktualisiert wird. Wenn die Integrität einer VM nach einem Upgrade nicht wiederhergestellt wird, wird die vorherige Erweiterungsversion standardmäßig erneut auf der VM installiert.

4. Der Upgradeorchestrator verfolgt auch den Prozentsatz der VMs nach, die nach einem Upgrade fehlerhaft werden. Das Upgrade wird beendet, wenn mehr als 20% der aktualisierten Instanzen während des Upgradeprozesses fehlerhaft werden.

Das oben beschriebene Verfahren wird fortgesetzt, bis alle Instanzen in der Skalierungsgruppe aktualisiert sind.

Der Upgradeorchestrator für die Skalierungsgruppe überprüft die allgemeine Skalierungsgruppenintegrität, bevor die einzelnen Batches aktualisiert werden. Beim Aktualisieren eines Batches finden eventuell andere gleichzeitige geplante oder nicht geplante Wartungsaktivitäten statt, die die Integrität Ihrer Skalierungsgruppen-VMs beeinträchtigen könnten. Wenn in solchen Fällen mehr als 20 % der Instanzen der Skalierungsgruppe fehlerhaft werden, endet das Upgrade der Skalierungsgruppe am Ende des aktuellen Batches.

## <a name="supported-extensions"></a>Unterstützte Erweiterungen
Das automatische Erweiterungsupgrade unterstützt die folgenden Erweiterungen (weitere werden regelmäßig hinzugefügt):
- Dependency-Agent: [Linux](./extensions/agent-dependency-linux.md) und [Windows](./extensions/agent-dependency-windows.md)
- [Anwendungsintegritätserweiterung](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md): Linux und Windows
- [Gastkonfigurationserweiterung](./extensions/guest-configuration.md): Linux und Windows
- Key Vault: [Linux](./extensions/key-vault-linux.md) und [Windows](./extensions/key-vault-windows.md)


## <a name="enabling-automatic-extension-upgrade"></a>Aktivieren des automatischen Erweiterungsupgrades

Um das automatische Erweiterungsupgrade für eine Erweiterung zu aktivieren, müssen Sie sicherstellen, dass die Eigenschaft `enableAutomaticUpgrade` auf `true` festgelegt ist und jeder Erweiterungsdefinition einzeln hinzugefügt wird.

### <a name="rest-api-for-virtual-machines"></a>REST-API für Virtual Machines
Verwenden Sie Folgendes, um das automatische Erweiterungsupgrade für eine Erweiterung (in diesem Beispiel die Dependency-Agent-Erweiterung) auf einer Azure-VM zu aktivieren:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### <a name="rest-api-for-virtual-machine-scale-sets"></a>REST-API für Virtual Machine Scale Sets
Verwenden Sie Folgendes, um die Erweiterung dem Skalierungsgruppenmodell hinzuzufügen:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                {
                "name": "<extensionName>",
                  "properties": {
                        "autoUpgradeMinorVersion": true,
                        "enableAutomaticUpgrade": true,
                    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                    "type": "DependencyAgentWindows",
                    "typeHandlerVersion": "9.5"
                    }
                }
                ]
            }
        }
    }
}
```

### <a name="azure-powershell-for-virtual-machines"></a>Azure PowerShell für Virtual Machines
Verwenden Sie das Cmdlet [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension):

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Azure PowerShell für Virtual Machine Scale Sets
Verwenden Sie das Cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension), um die Erweiterung dem Skalierungsgruppenmodell hinzuzufügen:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

Aktualisieren Sie die Skalierungsgruppe nach dem Hinzufügen der Erweiterung mithilfe von [Update-AzVmss](/powershell/module/az.compute/update-azvmss).


### <a name="azure-cli-for-virtual-machines"></a>Azure-Befehlszeilenschnittstelle für virtuelle Computer
Verwenden Sie das Cmdlet [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set):

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>Azure CLI für Virtual Machine Scale Sets
Verwenden Sie das Cmdlet [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set), um die Erweiterung dem Skalierungsgruppenmodell hinzuzufügen:

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Erweiterungsupgrades mit mehreren Erweiterungen

Für eine VM oder ein Virtual Machine Scale Set können mehrere Erweiterungen mit automatischem Erweiterungsupgrade aktiviert sein. Dieselbe VM oder Skalierungsgruppe kann auch über andere Erweiterungen verfügen, ohne dass das automatische Erweiterungsupgrade aktiviert ist.  

Wenn für einen virtuellen Computer mehrere Erweiterungsupgrades verfügbar sind, werden die Upgrades möglicherweise in einem Batch zusammengefasst, aber jedes Erweiterungsupgrade wird einzeln auf einem virtuellen Computer angewendet. Ein Fehler bei einer Erweiterung hat keine Auswirkung auf die anderen Erweiterungen, die möglicherweise aktualisiert werden. Wenn z. B. zwei Erweiterungen für ein Upgrade geplant sind und das erste Erweiterungsupgrade fehlschlägt, wird die zweite Erweiterung trotzdem aktualisiert.

Automatische Erweiterungsupgrades können auch angewendet werden, wenn für eine VM oder eine VM-Skalierungsgruppe mehrere Erweiterungen mit [Erweiterungssequenzierung](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md) konfiguriert sind. Erweiterungssequenzierung gilt für die erstmalige Bereitstellung der VM, und alle zukünftigen Erweiterungsupgrades für eine Erweiterung werden unabhängig voneinander angewendet.


## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Weitere Informationen zur Anwendungsintegritätserweiterung](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
