---
title: Ändern einer Kapazitätsreservierung in Azure (Vorschau)
description: Erfahren Sie, wie Sie eine Kapazitätsreservierung ändern.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 25b6eaea3c639d39721bd80aaad08cf2c6f80380
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066469"
---
# <a name="modify-a-capacity-reservation-preview"></a>Ändern einer Kapazitätsreservierung (Vorschau)

Nachdem Sie eine Kapazitätsreservierungsgruppe und eine Kapazitätsreservierung erstellt haben, können Sie Ihre Reservierungen ändern. In diesem Artikel wird erläutert, wie Sie die folgenden Schritte mithilfe von API, Azure-Portal und PowerShell ausführen. 

> [!div class="checklist"]
> * Aktualisieren der Anzahl der in einer Kapazitätsreservierung reservierten Instanzen 
> * Ändern der Größe von VMs, die einer Kapazitätsreservierungsgruppe zugeordnet sind
> * Löschen der Kapazitätsreservierungsgruppe und der Kapazitätsreservierung

> [!IMPORTANT]
> Die Kapazitätsreservierung befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="update-the-number-of-instances-reserved"></a>Aktualisieren der Anzahl reservierter Instanzen 

Aktualisieren Sie die Anzahl der in einer Kapazitätsreservierung reservierten VM-Instanzen.  

> [!IMPORTANT]
> In seltenen Fällen, in denen Azure die Anforderung zur Erhöhung der für vorhandene Kapazitätsreservierungen reservierten Menge nicht erfüllen kann, ist es möglich, dass eine Reservierung in den Status *Fehler* wechselt und nicht mehr verfügbar ist, bis die [Menge auf den ursprünglichen Betrag wiederhergestellt wurde](#restore-instance-quantity).

### <a name="api"></a>[API](#tab/api1)

```rest
    PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
``` 
    
Aktualisieren Sie im Anforderungstext die Eigenschaft `capacity` auf die neue Anzahl, die Sie reservieren möchten: 
    
```json
{
    "sku":
    {
        "capacity": 5
    }
} 
```

Beachten Sie, dass die Eigenschaft `capacity` in diesem Beispiel jetzt auf 5 festgelegt ist.


### <a name="portal"></a>[Portal](#tab/portal1) 

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Gehen Sie zur Kapazitätsreservierungsgruppe.
1. Wählen Sie **Übersicht** aus. 
1. Wählen Sie **Reservierungen** aus. 
1. Wählen Sie oben **Reservierungen verwalten** aus. 
1. Geben Sie auf der Seite *Reservierungen verwalten* die neue Menge ein, die im Feld **Instanzen** reserviert werden soll. 
1. Wählen Sie **Speichern** aus. 

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli1)
Um die reservierte Menge zu aktualisieren, verwenden Sie `az capacity reservation update` mit der aktualisierten Eigenschaft `capacity `.

 ```azurecli-interactive
 az capacity reservation update 
 -c myCapacityReservationGroup 
 -n myCapacityReservation 
 -g myResourceGroup2 
 --capacity 5
 ```

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

Um die reservierte Menge zu aktualisieren, verwenden Sie `New-AzCapacityReservation` mit der aktualisierten Eigenschaft `capacityToReserve`.

```powershell-interactive
Update-AzCapacityReservation
-ResourceGroupName "myResourceGroup"
-ReservationGroupName "myCapacityReservationGroup"
-Name "myCapacityReservation"
-CapacityToReserve 5
```

Weitere Informationen finden Sie unter dem Azure PowerShell-Befehl [Aktualisieren von AzCapacityReservation](/powershell/module/az.compute/update-azcapacityreservation).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="resize-vms-associated-with-a-capacity-reservation-group"></a>Ändern der Größe von VMs, die einer Kapazitätsreservierungsgruppe zugeordnet sind 

Wenn die Größe der zu ändernden VM derzeit an eine Kapazitätsreservierungsgruppe angefügt ist und diese Gruppe keine Reservierung für die Zielgröße hat, erstellen Sie eine neue Reservierung für diese Größe, oder entfernen Sie die VM aus der Reservierungsgruppe, bevor Sie die Größe ändern. 

Überprüfen Sie, ob die Zielgröße Teil der Reservierungsgruppe ist: 

### <a name="api"></a>[API](#tab/api2)

1. Rufen Sie die Namen aller Kapazitätsreservierungen innerhalb der Gruppe ab.
 
    ```rest
        GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?api-version=2021-04-01
    ``` 
    
    ```json
    { 
        "name": "<CapacityReservationGroupName>", 
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}", 
        "type": "Microsoft.Compute/capacityReservationGroups", 
        "location": "eastUS", 
        "zones": [ 
            "1" 
        ], 
        "properties": { 
            "capacityReservations": [ 
                { 
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}" 
                }, 
    { 
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName2}" 
                } 
            ] 
        } 
    } 
    ```

1. Ermitteln Sie die für jede Reservierung reservierte VM-Größe. Das folgende Beispiel gilt für `capacityReservationName1`, aber Sie können diesen Schritt für andere Reservierungen wiederholen.

    ```rest
        GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}?api-version=2021-04-01
    ``` 
    
    ```json
    { 
        "name": "capacityReservationName1", 
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}", 
        "type": "Microsoft.Compute/capacityReservationGroups/capacityReservations", 
        "location": "eastUS", 
        "sku": { 
            "name": "Standard_D2s_v3", 
            "capacity": 3 
        }, 
        "zones": [ 
            "1" 
        ], 
        "properties": { 
            "reservationId": "<reservationId>", 
            "provisioningTime": "<provisioningTime>", 
            "provisioningState": "Succeeded" 
        } 
    }  
    ```

1. Beachten Sie Folgendes: 
    1. Wenn die Ziel-VM-Größe nicht Teil der Gruppe ist, [erstellen Sie eine neue Kapazitätsreservierung](capacity-reservation-create.md) für die Ziel-VM. 
    1. Wenn die Größe des virtuellen Zielcomputers bereits in der Gruppe vorhanden ist, [ändern Sie die Größe des virtuellen Computers](resize-vm.md). 

### <a name="portal"></a>[Portal](#tab/portal2)

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Gehen Sie zur Kapazitätsreservierungsgruppe.
1. Wählen Sie **Übersicht** aus. 
1. Wählen Sie **Reservierungen** aus. 
1. Schauen Sie sich die für jede Reservierung reservierte *VM-Größe* an. 
    1. Wenn die Ziel-VM-Größe nicht Teil der Gruppe ist, [erstellen Sie eine neue Kapazitätsreservierung](capacity-reservation-create.md) für die Ziel-VM. 
    1. Wenn die Größe des virtuellen Zielcomputers bereits in der Gruppe vorhanden ist, [ändern Sie die Größe des virtuellen Computers](resize-vm.md). 

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli2)

1. Rufen Sie die Namen aller Kapazitätsreservierungen innerhalb der Kapazitätsreservierungsgruppe mit `az capacity reservation group show` ab.

    ```azurecli-interactive
    az capacity reservation group show 
    -g myResourceGroup
    -n myCapacityReservationGroup 
    ```

1. Suchen Sie in der Antwort nach den Namen aller Kapazitätsreservierungen.

1. Führen Sie die folgenden Befehle aus, um die für jede Reservierung reservierten VM-Größen zu ermitteln.

    ```azurecli-interactive
    az capacity reservation show
    -g myResourceGroup
    -c myCapacityReservationGroup 
    -n myCapacityReservation 
    ```

1. Beachten Sie Folgendes: 
    1. Wenn die Ziel-VM-Größe nicht Teil der Gruppe ist, [erstellen Sie eine neue Kapazitätsreservierung](capacity-reservation-create.md) für die Ziel-VM. 
    1. Wenn die Größe des virtuellen Zielcomputers bereits in der Gruppe vorhanden ist, [ändern Sie die Größe des virtuellen Computers](resize-vm.md). 


### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. Rufen Sie die Namen aller Kapazitätsreservierungen innerhalb der Gruppe mit `Get-AzCapacityReservationGroup` ab.

    ```powershell-interactive
    Get-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

1. Suchen Sie in der Antwort nach den Namen aller Kapazitätsreservierungen.

1. Führen Sie die folgenden Befehle aus, um die für jede Reservierung reservierten VM-Größen zu ermitteln.

    ```powershell-interactive
    $CapRes =
    Get-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "mycapacityReservation"
    
    $CapRes.Sku
    ```

1. Beachten Sie Folgendes: 
    1. Wenn die Ziel-VM-Größe nicht Teil der Gruppe ist, [erstellen Sie eine neue Kapazitätsreservierung](capacity-reservation-create.md) für die Ziel-VM. 
    1. Wenn die Größe des virtuellen Zielcomputers bereits in der Gruppe vorhanden ist, [ändern Sie die Größe des virtuellen Computers](resize-vm.md). 


Weitere Informationen finden Sie unter den Azure PowerShell-Befehlen [Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationgroup) und [Get-AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="delete-a-capacity-reservation-group-and-capacity-reservation"></a>Löschen einer Kapazitätsreservierungsgruppe und der Kapazitätsreservierung 

Azure ermöglicht das Löschen einer Kapazitätsreservierungsgruppe, wenn alle Mitgliedskapazitätsreservierungen gelöscht wurden und der Gruppe keine VMs zugeordnet sind.  

Um eine Kapazitätsreservierung zu löschen, ermitteln Sie zunächst alle VMs, die ihr zugeordnet sind. Die Liste der VMs ist unter der Eigenschaft `virtualMachinesAssociated` verfügbar. 

### <a name="api"></a>[API](#tab/api3)

Suchen Sie zunächst alle VMs, die der Kapazitätsreservierungsgruppe zugeordnet sind, und heben Sie die Zuordnung auf.
 
```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?$expand=instanceView&api-version=2021-04-01
``` 

```json
{ 
    "name": "<capacityReservationGroupName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}/capacityReservations/{capacityReservationName}" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName1}" 
            }, 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName2}" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "{capacityReservationName}", 
                    "utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName1}" 
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
Suchen Sie in der obigen Antwort die Namen aller virtuellen Computer unter der Eigenschaft `virtualMachinesAssociated`, und entfernen Sie sie aus der Kapazitätsreservierungsgruppe, indem Sie die Schritte unter [Entfernen einer VM-Zuordnung zu einer Kapazitätsreservierung](capacity-reservation-remove-vm.md) ausführen. 

Nachdem alle VMs aus der Kapazitätsreservierungsgruppe entfernt wurden, löschen Sie die Kapazitätsreservierungen des Mitglieds:

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
```

Löschen Sie abschließend die übergeordnete Kapazitätsreservierungsgruppe.

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?api-version=2021-04-01
```


### <a name="portal"></a>[Portal](#tab/portal3)

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Gehen Sie zur Kapazitätsreservierungsgruppe.
1. Wählen Sie **Ressourcen** aus. 
1. Ermitteln Sie alle VMs, die der Gruppe zugeordnet sind.
1. [Heben Sie die Zuordnung jeder VM auf](capacity-reservation-remove-vm.md).
1. Löschen Sie jede Kapazitätsreservierung in der Gruppe.
    1. Navigieren Sie zu **Reservierungen**.
    1. Wählen Sie alle Reservierungen aus. 
    1. Wählen Sie **Löschen** aus.
1. Löschen Sie die Kapazitätsreservierungsgruppe.
    1. Navigieren Sie zur Kapazitätsreservierungsgruppe.
    1. Wählen Sie am oberen Rand der Seite die Option **Löschen** aus.

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli3)

Suchen Sie alle VMs, die der Kapazitätsreservierungsgruppe zugeordnet sind, und heben Sie die Zuordnung auf.

1. Führen Sie Folgendes aus: 
    
    ```azurecli-interactive
    az capacity reservation group show
    -g myResourceGroup
    -n myCapacityReservationGroup
    ```

1. Ermitteln Sie in der obigen Antwort die Namen aller virtuellen Computer unter der Eigenschaft `VirtualMachinesAssociated`, und entfernen Sie sie aus der Kapazitätsreservierungsgruppe, indem Sie die Schritte unter [Entfernen einer Zuordnung virtueller Computer aus einer Kapazitätsreservierungsgruppe](capacity-reservation-remove-vm.md) ausführen.

1. Nachdem alle VMs aus der Gruppe entfernt wurden, fahren Sie mit den nächsten Schritten fort. 

1. Löschen der Kapazitätsreservierung:

    ```azurecli-interactive
    az capacity reservation delete 
    -g myResourceGroup 
    -c myCapacityReservationGroup 
    -n myCapacityReservation 
    ```

1. Löschen der Kapazitätsreservierungsgruppe:

    ```azurecli-interactive
    az capacity reservation group delete 
    -g myResourceGroup 
    -n myCapacityReservationGroup
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell3)

Suchen Sie alle VMs, die der Kapazitätsreservierungsgruppe zugeordnet sind, und heben Sie die Zuordnung auf.

1. Führen Sie Folgendes aus: 
    
    ```powershell-interactive
    Get-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

1. Ermitteln Sie in der obigen Antwort die Namen aller virtuellen Computer unter der Eigenschaft `VirtualMachinesAssociated`, und entfernen Sie sie aus der Kapazitätsreservierungsgruppe, indem Sie die Schritte unter [Entfernen einer Zuordnung virtueller Computer aus einer Kapazitätsreservierungsgruppe](capacity-reservation-remove-vm.md) ausführen.

1. Nachdem alle VMs aus der Gruppe entfernt wurden, fahren Sie mit den nächsten Schritten fort. 

1. Löschen der Kapazitätsreservierung:

    ```powershell-interactive
    Remove-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    ```

1. Löschen der Kapazitätsreservierungsgruppe:

    ```powershell-interactive
    Remove-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

Weitere Informationen finden Sie unter den Azure PowerShell-Befehlen [Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationgroup), [Get-AzCapacityReservation](/powershell/module/az.compute/remove-azcapacityreservation) und [Remove-AzCapacityReservationGroup](/powershell/module/az.compute/remove-azcapacityreservationgroup).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="restore-instance-quantity"></a>Instanzenmenge wiederherstellen 

Eine wohlgeformte Anforderung zur Reduzierung der reservierten Menge sollte immer erfolgreich sein, unabhängig von der Anzahl der VMs, die der Reservierung zugeordnet sind. Die Erhöhung der reservierten Menge kann jedoch ein höheres Kontingent erfordern, damit Azure die zusätzliche Kapazitätsanforderung erfüllen kann. In einem seltenen Szenario, in dem Azure die Anforderung zur Erhöhung der für vorhandene Reservierungen reservierten Menge nicht erfüllen kann, ist es möglich, dass die Reservierung in den Status *Fehler* wechselt und nicht mehr verfügbar ist, bis die reservierte Menge auf die ursprüngliche Menge wiederhergestellt wurde.  

> [!NOTE]
> Wenn sich eine Reservierung im Status *Fehler* befindet, funktionieren alle virtuellen Computer, die der Reservierung zugeordnet sind, weiterhin wie gewohnt.  

Beispiel: `myCapacityReservation` hat eine reservierte Menge von 5. Sie fordern 5 zusätzliche Instanzen an, sodass die reservierte Gesamtmenge 10 beträgt. Aufgrund einer eingeschränkten Kapazität in der Region kann Azure jedoch die angeforderten zusätzlichen Menge von 5 nicht erfüllen. In diesem Fall erreicht `myCapacityReservation` den vorgesehenen Status einer reservierten Menge von 10 nicht und der Status *Fehlgeschlagen* wird angezeigt. 

Führen Sie die folgenden Schritte aus, um den alten reservierten Wert für die Menge zu ermitteln, um diesen Fehler zu beheben:  

1. Navigieren Sie zu [Anwendungsänderungsanalyse](https://ms.portal.azure.com/#blade/Microsoft_Azure_ChangeAnalysis/ChangeAnalysisBaseBlade) im Azure-Portal 
1. Wählen Sie entsprechend **Abonnement**, **Ressourcengruppe** und **Zeitbereich** in den Filtern aus.
    - Sie können im Filter **Zeitbereich** nur bis zu 14 Tage zurückgehen 
1. Suchen Sie nach dem Namen der Kapazitätsreservierung.
1. Suchen Sie nach der Änderung in der Eigenschaft `sku.capacity` für diese Reservierung. 
    - Die alte reservierte Menge ist der Wert in der Spalte **Alter Wert**. 

Aktualisieren Sie `myCapacityReservation` auf die alte reservierte Menge. Nach der Aktualisierung ist die Reservierung sofort für die Verwendung mit Ihren VMs verfügbar. 


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfahren Sie, wie Sie VMs aus einer Kapazitätsreservierung entfernen.](capacity-reservation-remove-vm.md)
