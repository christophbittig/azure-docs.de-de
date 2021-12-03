---
title: Vergleich der Orchestrierungsmodi-API
description: Erfahren Sie mehr über die API-Unterschiede zwischen den Orchestrierungsmodi „Einheitlich“ und „Flexibel“.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 65f3ea7217930b680cfb197092533989a3206894
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054568"
---
# <a name="orchestration-modes-api-comparison"></a>Vergleich der Orchestrierungsmodi-API 

In diesem Artikel werden die API-Unterschiede zwischen dem einheitlichen und [dem flexiblen Orchestrierungsmodus](..\virtual-machines\flexible-virtual-machine-scale-sets.md) für Skalierungsgruppen für VMs verglichen. Weitere Informationen zu einheitlichen und flexiblen Skalierungsgruppen für VMs finden Sie unter [Orchestrierungsmodi](virtual-machine-scale-sets-orchestration-modes.md).


## <a name="instance-view"></a>Instanzansicht

| Einheitliche API | Flexible Alternative |
|-|-|
| Ansicht der Instanz einer VM-Skalierungsgruppe | Instanzansicht auf einzelnen VMs; Verwenden von Resource Graph zum Abfragen des Energiezustands |


## <a name="scale-set-lifecycle-batch-operations"></a>Batchvorgänge für den Skalierungsgruppenlebenszyklus  

| Einheitliche API | Flexible Alternative |
|-|-|
| [Zuordnung aufheben](/rest/api/compute/virtualmachinescalesetvms/deallocate)  | [Aufrufen der API für eine einzelne VM – Zuordnung aufheben](/rest/api/compute/virtualmachines/deallocate)   |
| [Löschen](/rest/api/compute/virtualmachinescalesetvms/delete)  | [Aufrufen der API für eine einzelne VM – Löschen](/rest/api/compute/virtualmachines/delete)  |
| [Instanzansicht abrufen](/rest/api/compute/virtualmachinescalesetvms/getinstanceview)  | [Aufrufen der API für eine einzelne VM – Instanzansicht](/rest/api/compute/virtualmachines/instanceview)  |
| [Wartung durchführen](/rest/api/compute/virtualmachinescalesetvms/performmaintenance)  | [Aufrufen der API für eine einzelne VM – Wartung durchführen](/rest/api/compute/virtualmachines/performmaintenance)  |
| [Ausschalten](/rest/api/compute/virtualmachinescalesetvms/poweroff)  | [Aufrufen der API für eine einzelne virtuelle VM  – Ausschalten](/rest/api/compute/virtualmachines/poweroff)  |
| [Erneute Bereitstellung](/rest/api/compute/virtualmachinescalesetvms/redeploy)  | [Aufrufen der API für eine einzelne VM – Erneut bereitstellen](/rest/api/compute/virtualmachines/redeploy)  |
| [Reimaging durchführen](/rest/api/compute/virtualmachinescalesetvms/reimage)  | [Aufrufen der API für eine einzelne VM – Reimaging durchführen](/rest/api/compute/virtualmachines/reimage)  |
| [Reimaging für alle durchführen](/rest/api/compute/virtualmachinescalesetvms/reimageall)  | Nicht zutreffend |
| [Neu starten](/rest/api/compute/virtualmachinescalesetvms/restart)  | [Aufrufen der API für eine einzelne VM – Neu starten](/rest/api/compute/virtualmachines/restart)  |
| [Simulieren einer Entfernung](/rest/api/compute/virtualmachinescalesetvms/simulateeviction) | [Aufrufen der API für eine einzelne VM – Simulieren einer Entfernung](/rest/api/compute/virtualmachines/simulateeviction)  |
| [Start](/rest/api/compute/virtualmachinescalesetvms/start) | [Aufrufen der API für eine einzelne VM – Starten](/rest/api/compute/virtualmachines/start) |


## <a name="get-or-update"></a>Erhalten oder aktualisieren 

**Einheitliche API:**

VM-Skalierungssätze; VM-Get- oder Update-Instanz:
- [Get](/rest/api/compute/virtualmachinescalesetvms/get) 
- [Aktualisieren](/rest/api/compute/virtualmachinescalesetvms/update)

**Flexible Alternative:** 

Aufrufen der API für einen einzelnen VM:
- [ARM Lock Ressource](../azure-resource-manager/management/lock-resources.md?tabs=json) für den Verhaltenstyp des Instanzschutzes 
    

## <a name="get-or-update-scale-set-vm-instances"></a>Abrufen oder Aktualisieren von VM-Instanzen von Skalierungsgruppen

| Einheitliche API | Flexible Alternative |
|-|-|
| [VM-Details zu Skalierungsgruppen](/rest/api/compute/virtualmachinescalesetvms/get) | [Abrufen des virtuellen Computers](/rest/api/compute/virtualmachines/get) |
| [Aktualisieren der VM-Instanz von Skalierungsgruppen](/rest/api/compute/virtualmachinescalesetvms/update) | [Aktualisieren des virtuellen Computers](/rest/api/compute/virtualmachines/update) |


## <a name="instance-protection"></a>Instanzschutz 

| Einheitliche API | Flexible Alternative |
|-|-|
| [Instanzschutz](virtual-machine-scale-sets-instance-protection.md) | [ARM Lock Ressource](../azure-resource-manager/management/lock-resources.md?tabs=json) für den Verhaltenstyp des Instanzschutzes | 


## <a name="list-instances"></a>Instanzen auflisten 

**Einheitliche API:**

`VMSS List Instances`: 
- Gibt die Skalierungsgruppen-ID zurück, die jeder Instanz zugeordnet ist.

**Flexible Alternative:**

Azure Resource Graph: 

```armasm
resources 
| where type == "microsoft.compute/virtualmachines" 
| where properties.virtualMachineScaleSet.id contains "portalbb01" 
```

## <a name="scale-set-instance-operations"></a>Instanzvorgänge von Skalierungsgruppen 

**Einheitliche API:**

Übersicht über VM-Skalierungsgruppen
- [Aktualisieren von Instanzen](/rest/api/compute/virtual-machine-scale-sets/update-instances)
- [Zuordnung aufheben](/rest/api/compute/virtual-machine-scale-sets/deallocate)
- [Wartung durchführen](/rest/api/compute/virtual-machine-scale-sets/perform-maintenance)
- [Ausschalten](/rest/api/compute/virtual-machine-scale-sets/power-off)
- [Erneute Bereitstellung](/rest/api/compute/virtual-machine-scale-sets/redeploy)
- [Reimaging durchführen](/rest/api/compute/virtual-machine-scale-sets/reimage)
- [Reimaging für alle durchführen](/rest/api/compute/virtual-machine-scale-sets/reimage-all)
- [Neu starten](/rest/api/compute/virtual-machine-scale-sets/restart)
- [Festlegen des Orchestrierungsdienststatus](/rest/api/compute/virtual-machine-scale-sets/set-orchestration-service-state)
- [Start](/rest/api/compute/virtual-machine-scale-sets/start)

**Flexible Alternative:**

Aufrufen von Vorgängen auf einzelnen VMs.

Vorgänge für virtuelle Computer:
- [Reimaging durchführen](/rest/api/compute/virtual-machines/reimage): Einzelne VM-API aufrufen – Reimaging durchführen nur auf kurzlebigen Betriebssystem-VMs

## <a name="vm-extension"></a>VM-Erweiterung

**Einheitliche API:**

VM-Skalierungsgruppen/VM-Erweiterungen:
- [Erstellen oder Aktualisieren](/rest/api/compute/virtual-machine-scale-set-vm-extensions/create-or-update)
- [Löschen](/rest/api/compute/virtual-machine-scale-set-vm-extensions/delete)
- [Get](/rest/api/compute/virtual-machine-scale-set-vm-extensions/get)
- [Liste](/rest/api/compute/virtual-machine-scale-set-vm-extensions/list)
- [Aktualisieren](/rest/api/compute/virtual-machine-scale-set-vm-extensions/update) 

**Flexible Alternative:**

Aufrufen von Vorgängen auf einzelnen VMs.


## <a name="networking"></a>Netzwerk 

| Einheitliche API | Flexible Alternative |
|-|-|
| NAT-Pool des Lastenausgleichs | Angeben einer NAT-Regel für bestimmte Instanzen | 

> [!IMPORTANT]
> Das Netzwerkverhalten hängt davon ab, wie Sie virtuelle Computer in Ihrer Skalierungsgruppe erstellen. **Manuell hinzugefügte VM-Instanzen** verfügen über einen ausgehenden Standardkonnektivitätszugriff. **Implizit erstellte VM-Instanzen** verfügen über keinen Standardzugriff.
>
> Weitere Informationen zu Netzwerken für flexible Skalierungsgruppen finden Sie unter [Skalierbare Netzwerkkonnektivität](../virtual-machines/flexible-virtual-machine-scale-sets-migration-resources.md#create-scalable-network-connectivity).


## <a name="scale-set-apis"></a>Skalierungsgruppen-APIs

**Einheitliche API:**

Einheitliche VM-Skalierungsgruppen-APIs
- [Konvertieren in eine einzelne Platzierungsgruppe](/rest/api/compute/virtual-machine-scale-sets/convert-to-single-placement-group)
- [Wiederherstellung erzwingen; Service Fabric-Platform; Updatedomäne](/rest/api/compute/virtual-machine-scale-sets/force-recovery-service-fabric-platform-update-domain-walk)

**Flexible Alternative:**

Wird in flexiblen Skalierungsgruppen für VMs nicht unterstützt.


## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Informationen zu den verschiedenen Orchestrierungsmodi](virtual-machine-scale-sets-orchestration-modes.md)