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
ms.openlocfilehash: 9fd4d2171aa54da469aacfaa7090025c296dd0c1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124804076"
---
# <a name="preview-orchestration-modes-api-comparison"></a>Preview: Vergleich der Orchestrierungsmodi-API 

In diesem Artikel werden die API-Unterschiede zwischen dem einheitlichen und [dem flexiblen Orchestrierungsmodus](..\virtual-machines\flexible-virtual-machine-scale-sets.md) für Skalierungsgruppen für VMs verglichen. Weitere Informationen zu einheitlichen und flexiblen Skalierungsgruppen für VMs finden Sie unter [Orchestrierungsmodi](virtual-machine-scale-sets-orchestration-modes.md).

> [!IMPORTANT]
> Das Feature für VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“ befindet sich derzeit in der öffentlichen Vorschauphase. Es ist ein Opt-in-Verfahren erforderlich, um die unten beschriebenen Funktionen der öffentlichen Vorschauversion zu nutzen.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="instance-view"></a>Instanzansicht

| Einheitliche API | Flexible Alternative |
|-|-|
| Ansicht der Instanz einer VM-Skalierungsgruppe | Instanzansicht auf einzelnen VMs; Verwenden von Resource Graph zum Abfragen des Energiezustands |


## <a name="scale-set-lifecycle-batch-operations"></a>Batchvorgänge für den Skalierungsgruppenlebenszyklus  

| Einheitliche API | Flexible Alternative |
|-|-|
| VM-Skalierungsgruppen; VM-Batchlebenszyklusvorgänge:  | Aufrufen der API für eine einzelne VM auf bestimmten Instanzen: |
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

### <a name="uniform-api"></a>Einheitliche API
VM-Skalierungssätze; VM-Get- oder Update-Instanz:
- [Get](/rest/api/compute/virtualmachinescalesetvms/get) 
- [Aktualisieren](/rest/api/compute/virtualmachinescalesetvms/update)

### <a name="flexible-alternative"></a>Flexible Alternative 
Aufrufen der API für einen einzelnen VM:
- [ARM Lock Ressource](../azure-resource-manager/management/lock-resources.md?tabs=json) für den Verhaltenstyp des Instanzschutzes 


## <a name="list-instances"></a>Instanzen auflisten 

### <a name="uniform-api"></a>Einheitliche API
`VMSS List Instances`: 
- Gibt die Skalierungssatz-ID zurück, die jeder Instanz während dieser Preview zugeordnet ist 

### <a name="flexible-alternative"></a>Flexible Alternative
Azure Resource Graph: 

```armasm
resources 
| where type == "microsoft.compute/virtualmachines" 
| where properties.virtualMachineScaleSet.id contains "portalbb01" 
```

## <a name="scale-set-operations"></a>Skalierungsgruppenvorgänge 

### <a name="uniform-api"></a>Einheitliche API
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

### <a name="flexible-alternative"></a>Flexible Alternative
Aufrufen von Vorgängen auf einzelnen VMs.


## <a name="vm-extension"></a>VM-Erweiterung

### <a name="uniform-api"></a>Einheitliche API
VM-Skalierungsgruppen/VM-Erweiterungen:
- [Erstellen oder Aktualisieren](/rest/api/compute/virtual-machine-scale-set-vm-extensions/create-or-update)
- [Löschen](/rest/api/compute/virtual-machine-scale-set-vm-extensions/delete)
- [Get](/rest/api/compute/virtual-machine-scale-set-vm-extensions/get)
- [Liste](/rest/api/compute/virtual-machine-scale-set-vm-extensions/list)
- [Aktualisieren](/rest/api/compute/virtual-machine-scale-set-vm-extensions/update) 

### <a name="flexible-alternative"></a>Flexible Alternative
Aufrufen von Vorgängen auf einzelnen VMs.


## <a name="networking"></a>Netzwerk 

### <a name="uniform-api"></a>Einheitliche API
- NAT-Pool/Portweiterleitung 
- NAT-Pool wird in flexiblen Skalierungsgruppen nicht unterstützt  

### <a name="flexible-alternative"></a>Flexible Alternative
- Einrichten einzelner NAT-Regeln auf jedem VM


## <a name="scale-set-apis"></a>Skalierungsgruppen-APIs

### <a name="uniform-api"></a>Einheitliche API
Einheitliche VM-Skalierungsgruppen-APIs
- [Konvertieren in eine einzelne Platzierungsgruppe](/rest/api/compute/virtual-machine-scale-sets/convert-to-single-placement-group)
- [Wiederherstellung erzwingen; Service Fabric-Platform; Updatedomäne](/rest/api/compute/virtual-machine-scale-sets/force-recovery-service-fabric-platform-update-domain-walk)

### <a name="flexible-alternative"></a>Flexible Alternative
Wird in flexiblen Skalierungsgruppen für VMs nicht unterstützt.


## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Informationen zu den verschiedenen Orchestrierungsmodi](virtual-machine-scale-sets-orchestration-modes.md)