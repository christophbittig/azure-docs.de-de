---
title: Entfernen einer Zuordnung eines virtuellen Computers aus einer Kapazitätsreservierungsgruppe (Vorschauversion)
description: Hier erfahren Sie, wie Sie einen virtuellen Computer aus einer Kapazitätsreservierungsgruppe entfernen.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: f0c84e4b44218aa4f7659376251d1931ffc9516b
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063619"
---
# <a name="remove-a-vm-association-from-a-capacity-reservation-group-preview"></a>Entfernen einer VM-Zuordnung aus einer Kapazitätsreservierungsgruppe (Vorschauversion)

In diesem Artikel werden die Schritte zum Entfernen einer VM-Zuordnung zu einer Kapazitätsreservierungsgruppe beschrieben. Weitere Informationen zu Kapazitätsreservierungen finden Sie in dem [Übersichtsartikel](capacity-reservation-overview.md). 

Da sowohl der virtuelle Computer als auch die zugrunde liegende Kapazitätsreservierung logischerweise Kapazität belegen, erzwingt Azure einige Einschränkungen für diesen Prozess, um nicht eindeutige Zuordnungszustände und unerwartete Fehler zu vermeiden.  

Es gibt es zwei Möglichkeiten, eine Zuordnung zu ändern: 
- Option 1: Heben Sie die Zuordnung des virtuellen Computers auf, ändern Sie die Eigenschaft Kapazitätsreservierungsgruppe, und starten Sie den virtuellen Computer ggf. neu.
- Option 2: Aktualisieren Sie die reservierte Menge auf null und ändern Sie dann die Eigenschaft Kapazitätsreservierungsgruppe

> [!IMPORTANT]
> Die Kapazitätsreservierung befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deallocate-the-vm"></a>Aufheben der Zuordnung der VM

Als erste Option können Sie die Zuordnung des virtuellen Computers aufheben, die Eigenschaft Kapazitätsreservierungsgruppe ändern und den virtuellen Computer ggf. neu starten. 

### <a name="api"></a>[API](#tab/api1)

1. Aufheben der Zuordnung der VM

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/deallocate?api-version=2021-04-01
    ```

1. Aktualisieren der VM, um die Zuordnung zu der Kapazitätsreservierungsgruppe zu entfernen
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/update?api-version=2021-04-01
    ```
    Legen Sie im Anforderungstext die Eigenschaft `capacityReservationGroup` auf „Null“ fest, um die VM-Zuordnung zur Gruppe zu entfernen:

    ```json
     {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id":null
            }
        }
    }
    }
    ```

### <a name="portal"></a>[Portal](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Wählen Sie Ihren virtuellen Computer aus, und klicken Sie auf **Übersicht**.
1. Wählen Sie **Beenden** aus. 
    1. Sie werden wissen, dass die VM nicht mehr zugewiesen ist, wenn sich der Status in *Beendet (Nicht zugewiesen)* ändert.
    1. An diesem Punkt des Prozesses ist der virtuelle Computer weiterhin der Kapazitätsreservierungsgruppe zugeordnet, die in der `virtualMachinesAssociated`-Eigenschaft der Kapazitätsreservierung widergespiegelt wird. 
1. Wählen Sie **Konfiguration** aus.
1. Legen Sie den Wert der **Kapazitätsreservierungsgruppe** auf *Keiner* fest.
    - Der virtuelle Computer ist nicht mehr der Kapazitätsreservierungsgruppe zugeordnet. 

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli1)

1. Heben Sie die Zuordnung des virtuellen Computers auf.

    ```azurecli-interactive
    az vm deallocate 
    -g myResourceGroup 
    -n myVM
    ```

    Nachdem sich der Status in **Beendet (Nicht zugewiesen)** ändert, wissen Sie, dass die VM nicht mehr zugewiesen ist.

1. Aktualisieren Sie die VM, um die Zuordnung zur Kapazitätsreservierungsgruppe zu entfernen, indem Sie die Eigenschaft `capacity-reservation-group` auf „Keine“ festlegen:

    ```azurecli-interactive
    az vm update 
    -g myresourcegroup 
    -n myVM 
    --capacity-reservation-group None
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. Heben Sie die Zuordnung des virtuellen Computers auf.

    ```powershell-interactive
    Stop-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    ```

    Nachdem sich der Status in **Beendet (Nicht zugewiesen)** ändert, wissen Sie, dass die VM nicht mehr zugewiesen ist.

1. Aktualisieren Sie die VM, um die Zuordnung zur Kapazitätsreservierungsgruppe zu entfernen, indem Sie die Eigenschaft `CapacityReservationGroupId` auf „Null“ festlegen:

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId $null
    ```

Weitere Informationen finden Sie unter Azure PowerShell-Befehle: [Stop-AzVm](/powershell/module/az.compute/stop-azvm), [Get-AzVM](/powershell/module/az.compute/get-azvm) und [Update-AzVM](/powershell/module/az.compute/update-azvm).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="update-the-reserved-quantity-to-zero"></a>Aktualisieren der reservierten Menge auf null 

Die zweite Option umfasst das Aktualisieren der reservierten Menge auf null und das anschließende Ändern der Eigenschaft „Kapazitätsreservierungsgruppe“.

Diese Option funktioniert gut, wenn die Zuordnung des virtuellen Computers nicht aufgehoben werden kann und wenn eine Reservierung nicht mehr benötigt wird. Beispielsweise können Sie eine Kapazitätsreservierung erstellen, um die Kapazität während einer großen Bereitstellung vorübergehend sicherzustellen. Nach dem Abschluss ist die Reservierung nicht mehr erforderlich. 

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

1. Aktualisieren der VM, um die Zuordnung zu der Kapazitätsreservierungsgruppe zu entfernen

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}/update?api-version=2021-04-01
    ```

    Legen Sie im Anforderungstext die Eigenschaft `capacityReservationGroup` auf „Null“ fest, um die Zuordnung zu entfernen:
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id":null
            }
        }
    }
    } 
    ```

### <a name="portal"></a>[Portal](#tab/portal2)

<!-- no images necessary if steps are straightforward --> 

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Gehen Sie zur Kapazitätsreservierungsgruppe, und wählen Sie **Übersicht** aus.
1. Wählen Sie **Reservierungen** aus. 
1. Wählen Sie oben auf der Seite **Reservierungen verwalten** aus. 
1. Im Blatt *Reservierungen verwalten*:
    1. Geben Sie `0` in das Feld **Instanzen** ein.
    1. Wählen Sie **Speichern** aus. 
1. Wählen Sie Ihren virtuellen Computer aus, und klicken Sie auf **Konfiguration**.
1. Legen Sie den Wert der **Kapazitätsreservierungsgruppe** auf *Keiner* fest.
    - Beachten Sie: Der virtuelle Computer ist nicht mehr der Kapazitätsreservierungsgruppe zugeordnet.

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli2)

1. Aktualisieren der reservierten Menge auf null

   ```azurecli-interactive
   az capacity reservation update 
   -g myResourceGroup
   -c myCapacityReservationGroup 
   -n myCapacityReservation 
   --capacity 0
   ```

1. Aktualisieren Sie die VM, um die Zuordnung zur Kapazitätsreservierungsgruppe zu entfernen, indem Sie die Eigenschaft `capacity-reservation-group` auf „Keine“ festlegen:

    ```azurecli-interactive
    az vm update 
    -g myresourcegroup 
    -n myVM 
    --capacity-reservation-group None
    ```


### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. Aktualisieren der reservierten Menge auf null

    ```powershell-interactive
    Update-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -CapacityToReserve 0
    ```

1. Aktualisieren Sie die VM, um die Zuordnung zur Kapazitätsreservierungsgruppe zu entfernen, indem Sie die Eigenschaft `CapacityReservationGroupId` auf „Null“ festlegen:

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId $null
    ```

Weitere Informationen finden Sie unter den Azure PowerShell-Befehlen [New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation), [Get-AzVM](/powershell/module/az.compute/get-azvm)und [Update-AzVM](/powershell/module/az.compute/update-azvm).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hier erfahren Sie, wie Sie einer Kapazitätsreservierungsgruppe eine Skalierungsgruppe zuordnen.](capacity-reservation-associate-virtual-machine-scale-set.md)
