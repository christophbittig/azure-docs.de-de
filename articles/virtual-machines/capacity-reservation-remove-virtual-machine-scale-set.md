---
title: Entfernen einer Skalierungsgruppenzuordnung von einem virtuellen Computer aus einer Kapazitätsreservierungsgruppe (Vorschauversion)
description: Erfahren Sie, wie Sie eine Skalierungsgruppe von einem virtuellen Computer aus einer Kapazitätsreservierungsgruppe entfernen.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 03b89b1b8c0221795f58ff28addd4fdeaad5053e
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129532589"
---
# <a name="remove-a-virtual-machine-scale-set-association-from-a-capacity-reservation-group"></a>Entfernen einer Skalierungsgruppenzuordnung von einem virtuellen Computer aus einer Kapazitätsreservierungsgruppe 

Dieser Artikel führt Sie durch die Schritte zum Entfernen einer Skalierungsgruppenzuordnung von einem virtuellen Computer aus einer Kapazitätsreservierungsgruppe. Weitere Informationen zu Kapazitätsreservierungen finden Sie in dem [Übersichtsartikel](capacity-reservation-overview.md). 

Da sowohl der virtuelle Computer als auch die zugrunde liegende Kapazitätsreservierung logischerweise Kapazität belegen, erzwingt Azure einige Einschränkungen für diesen Prozess, um nicht eindeutige Zuordnungszustände und unerwartete Fehler zu vermeiden.  

Es gibt es zwei Möglichkeiten, eine Zuordnung zu ändern: 
- Option 1: Heben Sie die Zuordnung der Skalierungsgruppe für den virtuellen Computer auf, ändern Sie die Eigenschaft Kapazitätsreservierungsgruppe auf Skalierungsgruppenebene, und aktualisieren Sie dann die zugrunde liegenden virtuellen Computer
- Option 2: Aktualisieren Sie die reservierte Menge auf null und ändern Sie dann die Eigenschaft Kapazitätsreservierungsgruppe

> [!IMPORTANT]
> Die Kapazitätsreservierung befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deallocate-the-virtual-machine-scale-set"></a>Aufheben der Zuordnung einer Skalierungsgruppe für virtuelle Computer

Die erste Option ist das Aufheben der Zuordnung der Skalierungsgruppe für den virtuellen Computer. Ändern Sie dazu die Eigenschaft Kapazitätsreservierungsgruppe auf der Skalierungsgruppenebene und aktualisieren Sie dann die zugrunde liegenden virtuellen Computer. 

Navigieren Sie zu [Aktualisieren von Richtlinien](#upgrade-policies) um weitere zu automatischen, rollierenden und manuellen Aktualisierungen zu erhalten. 

### <a name="api"></a>[API](#tab/api1)

1. Aufheben der Zuordnung der VM-Skalierungsgruppe

    ```rest
    POST  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/deallocate?api-version=2021-04-01
    ```

1. Aktualisieren der Skalierungsgruppe für virtuelle Computer, um die Zuordnung zu der Kapazitätsreservierungsgruppe zu entfernen
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/update?api-version=2021-04-01
    ```
    Legen Sie im Anforderungstext die Eigenschaft `capacityReservationGroup` auf null fest, um die Gruppenzuordnung der Skalierungsgruppe für virtuelle Computer zu entfernen:

    ```json
    {
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
            "capacityReservation": {
                "capacityReservationGroup":{
                    "id":null    
                }
            }
        }
    }
    }
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. Aufheben der Zuordnung der Skalierungsgruppe für virtuelle Computer. Im Folgenden wird die Zuordnung aller virtuellen Computer innerhalb der Skalierungsgruppe freigegeben: 

    ```powershell-interactive
    Stop-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    ```

1. Aktualisieren der Skalierungsgruppe für virtuelle Computer, um die Zuordnung zu der Kapazitätsreservierungsgruppe zu entfernen. Durch Festlegen der Eigenschaft `CapacityReservationGroupId` auf null wird die Zuordnung der Skalierungsgruppe zur Kapazitätsreservierungsgruppe entfernt: 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myvmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId $null
    ```

Weitere Informationen finden Sie unter Azure PowerShell [Beenden von AzVmss,](/powershell/module/az.compute/stop-azvmss), [Abrufen von AzVmss](/powershell/module/az.compute/get-azvmss)und [Aktualisieren von AzVmss](/powershell/module/az.compute/update-azvmss).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="update-the-reserved-quantity-to-zero"></a>Aktualisieren der reservierten Menge auf null 

Die zweite Option umfasst das Aktualisieren der reservierten Menge auf null und das anschließende Ändern der Eigenschaft „Kapazitätsreservierungsgruppe“.

Diese Option funktioniert gut, wenn die Zuordnung der Skalierungsgruppe für virtuelle Computer nicht aufgehoben werden kann und wenn eine Reservierung nicht mehr benötigt wird. Beispielsweise können Sie eine Kapazitätsreservierung erstellen, um die Kapazität während einer großen Bereitstellung vorübergehend sicherzustellen. Nach dem Abschluss ist die Reservierung nicht mehr erforderlich. 

Navigieren Sie zu [Aktualisieren von Richtlinien](#upgrade-policies) um weitere zu automatischen, rollierenden und manuellen Aktualisierungen zu erhalten. 

### <a name="api"></a>[API](#tab/api2)

1. Aktualisieren der reservierten Menge auf null 

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/CapacityReservations/{CapacityReservationName}?api-version=2021-04-01
    ```

    Fügen Sie Folgendes in den Anforderungstext ein:
    
    ```json
    {
    "sku": 
        {
        "capacity": 0
        }
    } 
    ```
    
    Beachten Sie, dass die Eigenschaft `capacity` oben auf 0 festgelegt ist.

1. Aktualisieren der Skalierungsgruppe für virtuelle Computer, um die Zuordnung zu der Kapazitätsreservierungsgruppe zu entfernen

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/update?api-version=2021-04-01
    ```

    Legen Sie im Anforderungstext die Eigenschaft `capacityReservationGroup` auf null fest, um die Zuordnung zu entfernen:
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
            "capacityReservation": {
                "capacityReservationGroup":{
                    "id":null
                }
            }
        }
    }
    }
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. Aktualisieren Sie die reservierte Menge auf null:

    ```powershell-interactive
    Update-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -CapacityToReserve 0
    ```

2. Aktualisieren Sie die Skalierungsgruppe, um die Zuordnung zur Kapazitätsreservierungsgruppe zu entfernen, indem Sie die Eigenschaft `CapacityReservationGroupId` auf null festlegen: 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myvmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId $null
    ```

Weitere Informationen finden Sie unter den Azure PowerShell-Befehlen [Neue AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation), [Abrufen von AzVmss](/powershell/module/az.compute/get-azvmss)und [Aktualisieren von AzVmss](/powershell/module/az.compute/update-azvmss).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="upgrade-policies"></a>Upgrade-Richtlinien

- **Automatisches Upgrade** – In diesem Modus werden die VM-Instanzen der Skalierungsgruppe automatisch ohne weitere Maßnahmen von Ihnen von der Kapazitätsreservierungsgruppe getrennt.
- **Paralleles Upgrade** – In diesem Modus werden die VM-Instanzen der Skalierungsgruppe ohne weitere Maßnahmen von Ihnen von der Kapazitätsreservierungsgruppe getrennt. Sie werden jedoch in Batches mit einer optionalen Pausenzeit zwischen ihnen aktualisiert.
- **Manuelles Upgrade** – In diesem Modus geschieht nichts mit den VM-Instanzen der Skalierung gruppe, wenn die Skalierungsgruppe für virtuelle Computer aktualisiert wird. Sie müssen jede Skalierungsgruppe für virtuelle Computer einzeln entfernen, indem Sie [sie mit dem neuesten Skalierungsgruppenmodell aktualisieren](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfahren Sie mehr über das Überbelegen einer Kapazitätsreservierung](capacity-reservation-overallocate.md)
