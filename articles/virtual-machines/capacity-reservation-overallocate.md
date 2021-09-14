---
title: Überlasten der Kapazitätsreservierung in Azure (Vorschau)
description: Erfahren Sie, wie die Überzuordnung funktioniert, wenn es um Kapazitätsreservierung geht.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 0486263551246a794f90867621be0e87d42747c6
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273342"
---
# <a name="overallocating-capacity-reservation-preview"></a>Überlasten der Kapazitätsreservierung (Vorschau)

Azure ermöglicht die Zuordnung zusätzlicher VMs über die reservierte Anzahl einer Kapazitätsreservierung hinaus, um Burst- und andere Aufskalierungsszenarien zu ermöglichen, ohne den Mehraufwand für die Verwaltung der Grenzen der reservierten Kapazität zu bewältigen. Der einzige Unterschied besteht darin, dass die Anzahl der VMs, die über die reservierte Menge hinausgehen, nicht den SLA-Vorteil für die Kapazitätsverfügbarkeit erhält. Solange Azure über Kapazität verfügt, die die Anforderungen des VM erfüllt, sind die zusätzlichen Zuordnungen erfolgreich. 

Die Instanzansicht einer Kapazitätsreservierungsgruppe stellt eine Momentaufnahme der Nutzung für die einzelnen Mitgliedskapazitätsreservierungen bereit. Sie können die Instanzansicht verwenden, um zu sehen, wie die Überzuordnung funktioniert. 

In diesem Artikel wird davon ausgegangen, dass Sie eine Kapazitätsreservierungsgruppe (`myCapacityReservationGroup`), ein Mitglied Kapazitätsreservierung (`myCapacityReservation`) und eine VM (*myVM1*), die der Gruppe zugeordnet ist, erstellt haben. Weitere Informationen finden Sie unter [Erstellen einer Kapazitätsreservierung](capacity-reservation-create.md) und [Zuordnen einer VM zu einer Kapazitätsreservierung](capacity-reservation-associate-vm.md).

> [!IMPORTANT]
> Die Kapazitätsreservierung befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="register-for-capacity-reservation"></a>Registrieren Sie sich für die Kapazitätsreservierung 

Bevor Sie die Funktion zur Kapazitätsreservierung verwenden können, müssen Sie [Ihr Abonnement für die Vorschauversion registrieren](capacity-reservation-overview.md#register-for-capacity-reservation). Die Registrierung kann mehrere Minuten dauern. Sie können die Azure CLI oder PowerShell verwenden, um die Funktionsregistrierung abzuschließen.

## <a name="instance-view-for-capacity-reservation-group"></a>Instanzansicht für Kapazitätsreservierungsgruppe 

Die Instanzansicht für eine Kapazitätsreservierungsgruppe sieht wie folgt aus: 

```rest
GET 
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/myCapacityReservationGroup?$expand=instanceview&api-version=2021-04-01
```

```json
{ 
    "name": "myCapacityReservationGroup", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/myCapacityReservationGroup", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/MYCAPACITYRESERVATIONGROUP/capacityReservations/MYCAPACITYRESERVATION" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "myCapacityReservation", 
"utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
                            } 
                        ] 
                    }, 
                    "statuses": [ 
                        { 
                            "code": "ProvisioningState/succeeded", 
                            "level": "Info", 
                            "displayStatus": "Provisioning succeeded", 
                            "time": "<time>" 
                        } 
                    ] 
                } 
            ] 
        } 
    } 
} 
```

Nehmen wir an, wir erstellen eine weitere VM mit dem Namen *myVM2* und verknüpfen sie mit der oben genannten Kapazitätsreservierungsgruppe. 

Die Instanzansicht für die Kapazitätsreservierungsgruppe sieht nun wie folgt aus: 

```json
{ 
    "name": "myCapacityReservationGroup", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/myCapacityReservationGroup", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/MYCAPACITYRESERVATIONGROUP/capacityReservations/MYCAPACITYRESERVATION" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
            }, 
 { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM2" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "myCapacityReservation", 
"utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
                            }, 
{ 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM2" 
            } 
                        ] 
                    }, 
                    "statuses": [ 
                        { 
                            "code": "ProvisioningState/succeeded", 
                            "level": "Info", 
                            "displayStatus": "Provisioning succeeded", 
                            "time": "<time>" 
                        } 
                    ] 
                } 
            ] 
        } 
    } 
} 
``` 

Beachten Sie, dass die Länge von `virtualMachinesAllocated` (2) größer als `capacity` (1) ist. Dieser gültige Zustand wird als *überbelegt* bezeichnet. 

> [!IMPORTANT]
> Azure beendet Zuordnungen nicht nur, weil eine Kapazitätsreservierung vollständig genutzt wird. Regeln für die automatische Skalierung, temporäre horizontale Skalierung und zugehörige Anforderungen funktionieren über die Menge der reservierten Kapazität hinaus, solange Azure über verfügbare Kapazität verfügt.  


## <a name="states-and-considerations"></a>Zustände und Überlegungen  

Es gibt drei gültige Zustände für eine bestimmte Kapazitätsreservierung: 

| State  | Status  | Überlegungen  |
|---|---|---|
| Verfügbare reservierte Kapazität  | Länge von `virtualMachinesAllocated` < `capacity`  | Ist die gesamte reservierte Kapazität erforderlich? Verringern Sie optional die Kapazität, um Kosten zu senken.  |
| Genutzte Reservierung  | Länge von `virtualMachinesAllocated` == `capacity`  | Zusätzliche VMs erhalten die Kapazitäts-SLA nur, wenn die Zuordnung einiger vorhandener VMs freigegeben wird. Versuchen Sie optional, die Kapazität zu erhöhen, damit zusätzliche geplante VMs eine SLA erhalten.  |
| Reservierung überbelegt  | Länge von `virtualMachinesAllocated` > `capacity`  | Zusätzliche VMs erhalten die Kapazitäts-SLA nicht. Außerdem erhält die Menge der VMs (Länge von `virtualMachinesAllocated` – `capacity`) keine Kapazitäts-SLA, wenn die Zuordnung freigegeben wird. Erhöhen Sie optional die Kapazität, um mehr vorhandenen VMs eine Kapazitäts-SLA hinzuzufügen.  |


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfahren Sie, wie Sie VMs aus einer Kapazitätsreservierung entfernen.](capacity-reservation-remove-vm.md)