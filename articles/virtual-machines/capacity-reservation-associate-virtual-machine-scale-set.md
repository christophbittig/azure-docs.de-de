---
title: Zuordnen einer VM-Skalierungsgruppe zu einer Kapazitätsreservierungsgruppe (Vorschau)
description: Erfahren Sie, wie Sie einer Kapazitätsreservierungsgruppe eine neue oder vorhandene VM-Skalierungsgruppe zuordnen.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 39dc20f240ba051dd5b49ddbbcc99a20b732ec77
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273349"
---
# <a name="associate-a-virtual-machine-scale-set-to-a-capacity-reservation-group-preview"></a>Zuordnen einer VM-Skalierungsgruppe zu einer Kapazitätsreservierungsgruppe (Vorschau)

VM-Skalierungsgruppen weisen zwei Modi auf: 

- **Einheitlicher Orchestrierungsmodus**: In diesem Modus verwenden VM-Skalierungsgruppen ein VM-Profil oder eine Vorlage, um auf die gewünschte Kapazität hochskaliert zu werden. Einzelne VM-Instanzen können zwar bis zu einem gewissen Maße verwaltet oder angepasst werden, aber im Modus „Einheitlich“ werden identische VM-Instanzen verwendet. Diese Instanzen werden über die VM-APIs der VM-Skalierungsgruppen zur Verfügung gestellt und sind nicht mit den VM-API-Standardbefehlen von Azure IaaS kompatibel. Da die Skalierungsgruppe alle eigentlichen VM-Vorgänge ausführt, werden Reservierungen direkt der VM-Skalierungsgruppe zugeordnet. Sobald die Skalierungsgruppe der Reservierung zugeordnet ist, werden alle nachfolgenden VM-Zuordnungen anhand der Reservierung durchgeführt. 
- **Flexibler Orchestrierungsmodus**: In diesem Modus erhalten Sie mehr Flexibilität beim Verwalten der einzelnen VM-Instanzen der VM-Skalierungsgruppe, da diese die VM-Standard-APIs von Azure IaaS anstelle der Skalierungsgruppenschnittstelle verwenden können. Dieser Modus funktioniert während der öffentlichen Vorschau nicht mit der Kapazitätsreservierung.

Weitere Informationen zu diesen Modi finden Sie unter [Orchestrierungsmodi für VM-Skalierungsgruppen](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md). Im Rest dieses Artikels wird beschrieben, wie Sie eine einheitliche VM-Skalierungsgruppe einer Kapazitätsreservierungsgruppe zuordnen. 

> [!IMPORTANT]
> Die Kapazitätsreservierung befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-for-capacity-reservation"></a>Registrieren für Kapazitätsreservierung 

Bevor Sie das Feature zur Kapazitätsreservierung verwenden können, müssen Sie [Ihr Abonnement für die Vorschauversion registrieren](capacity-reservation-overview.md#register-for-capacity-reservation). Die Registrierung kann mehrere Minuten dauern. Sie können die Azure CLI oder PowerShell verwenden, um die Funktionsregistrierung abzuschließen.

> [!NOTE]
> Bedarfsbasierte Kapazitätsreservierung ist nur in ausgewählten Regionen für die Skalierungsgruppen virtueller Computer im einheitlichen Orchestrierungsmodus verfügbar. Um zu überprüfen, ob Ihre Region unterstützt wird, navigieren Sie zu [Bereitstellungsnachverfolgung für eine einheitliche VM-Skalierungsgruppe](https://aka.ms/vmssuniformdeploymenttracker).


## <a name="limitations-of-scale-sets-in-uniform-orchestration"></a>Einschränkungen von Skalierungsgruppen in einer einheitlichen Orchestrierung 

- Damit VM-Skalierungsgruppen in einer einheitlichen Orchestrierung mit der Kapazitätsreservierung kompatibel sind, muss die `singlePlacementGroup`-Eigenschaft auf *FALSE* festgelegt werden. 
- Die Verfügbarkeitsoption **Statische feste Verteilung** für einheitliche Skalierungsgruppen mit mehreren Zonen wird bei der Kapazitätsreservierung nicht unterstützt. Diese Option erfordert die Verwendung von 5 Fehlerdomänen, während die Reservierungen nur bis zu 3 Fehlerdomänen für universelle Größen unterstützen. Es wird empfohlen, die Option **Max. Verteilung** zu verwenden, die VMs innerhalb jeder Zone auf so viele FDs wie möglich verteilt. Konfigurieren Sie bei Bedarf eine benutzerdefinierte Fehlerdomänenkonfiguration von 3 oder weniger Fehlerdomänen. 

Bei Verwendung der Kapazitätsreservierung gelten einige weitere Einschränkungen. Die vollständige Liste finden Sie in der [Übersicht über Kapazitätsreservierungen](capacity-reservation-overview.md).  

## <a name="associate-a-new-virtual-machine-scale-set-to-a-capacity-reservation-group"></a>Zuordnen einer neuen VM-Skalierungsgruppe zu einer Kapazitätsreservierungsgruppe


### <a name="api"></a>[API](#tab/api1)  

Erstellen Sie die folgende PUT-Anforderung an den *Microsoft.Compute*-Anbieter, um einer Kapazitätsreservierungsgruppe eine neue einheitliche VM-Skalierungsgruppe zuzuordnen:

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}?api-version=2021-04-01
```

Fügen Sie die `capacityReservationGroup`-Eigenschaft in `virtualMachineProfile` wie unten gezeigt in hinzu: 

```json
{ 
    "name": "<VMScaleSetName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}", 
    "type": "Microsoft.Compute/virtualMachineScaleSets", 
    "location": "eastus", 
    "sku": { 
        "name": "Standard_D2s_v3", 
        "tier": "Standard", 
        "capacity": 3 
}, 
"properties": { 
    "virtualMachineProfile": { 
        "capacityReservation": { 
            "capacityReservationGroup":{ 
                "id":"subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroup/{CapacityReservationGroupName}" 
            } 
         }, 
        "osProfile": { 
            … 
        }, 
        "storageProfile": { 
            … 
        }, 
        "networkProfile": { 
            …,
            "extensionProfile": { 
                … 
            } 
        } 
    } 
```

### <a name="powershell"></a>[PowerShell](#tab/powershell1) 

Verwenden Sie `New-AzVmss`, um eine neue VM-Skalierungsgruppe zu erstellen und die `CapacityReservationGroupId`-Eigenschaft hinzuzufügen, um die Skalierungsgruppe einer vorhandenen Kapazitätsreservierungsgruppe zuzuordnen. Das folgende Beispiel erstellt eine einheitliche Skalierungsgruppe für eine Standard_Ds1_v2-VM am Standort „USA, Osten“ und ordnet die Skalierungsgruppe einer Kapazitätsreservierungsgruppe zu.

```powershell-interactive
$vmssName = <"VMSSNAME">
$vmPassword = ConvertTo-SecureString <"PASSWORD"> -AsPlainText -Force
$vmCred = New-Object System.Management.Automation.PSCredential(<"USERNAME">, $vmPassword)
New-AzVmss
–Credential $vmCred
-VMScaleSetName $vmssName
-ResourceGroupName "myResourceGroup"
-CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
-PlatformFaultDomainCount 2
```

Weitere Informationen finden Sie unter dem Azure PowerShell-Befehl [New-AzVmss](/powershell/module/az.compute/new-azvmss).

### <a name="arm-template"></a>[ARM-Vorlage](#tab/arm1)

Eine  [ARM-Vorlage](/azure/azure-resource-manager/templates/overview)  ist eine JSON-Datei (JavaScript Object Notation), die die Infrastruktur und Konfiguration für Ihr Projekt definiert. Die Vorlage verwendet eine deklarative Syntax. In deklarativer Syntax beschreiben Sie Ihre beabsichtigte Bereitstellung, ohne die Reihenfolge der Programmierbefehle zur Erstellung der Bereitstellung zu schreiben. 

Mit ARM-Vorlagen können Sie Gruppen mit verwandten Ressourcen bereitstellen. Sie können in einer einzelnen Vorlage eine Kapazitätsreservierungsgruppe und Kapazitätsreservierungen erstellen. Sie können Vorlagen über das Azure-Portal, die Azure CLI oder Azure PowerShell oder über CI/CD-Pipelines (Continuous Integration/Continuous Delivery) bereitstellen. 

Wenn Ihre Umgebung die Voraussetzungen erfüllt, und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, benutzen Sie diese Vorlage: [Erstellen von VM-Skalierungsgruppen mit Kapazitätsreservierung](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineScaleSetWithReservation.json). 

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="associate-an-existing-virtual-machine-scale-set-to-capacity-reservation-group"></a>Zuordnen einer vorhandenen VM-Skalierungsgruppe zu einer Kapazitätsreservierungsgruppe 

Um in der öffentlichen Vorschau eine vorhandene einheitliche VM-Skalierungsgruppe der Kapazitätsreservierungsgruppe zuzuordnen, muss die Zuordnung der Skalierungsgruppe zunächst aufgehoben und die Zuordnung dann bei der erneuten Zuordnung vorgenommen werden. Dadurch wird sichergestellt, dass alle VMs der Skalierungsgruppe die Kapazitätsreservierung zum Zeitpunkt der erneuten Zuordnung nutzen.

### <a name="important-notes-on-upgrade-policies"></a>Wichtige Hinweise zu Upgraderichtlinien 

- **Automatisches Upgrade**: In diesem Modus werden die VM-Instanzen der Skalierungsgruppe der Kapazitätsreservierungsgruppe ohne weitere Maßnahmen automatisch zugeordnet. Wenn die VMs der Skalierungsgruppe erneut zugewiesen werden, beginnen sie, die reservierte Kapazität zu nutzen.
- **Paralleles Upgrade**: In diesem Modus werden die VM-Instanzen der Skalierungsgruppe der Kapazitätsreservierungsgruppe ohne weitere Maßnahmen zugeordnet. Sie werden jedoch in Batches mit einer optionalen Pausenzeit zwischen ihnen aktualisiert. Wenn die VMs der Skalierungsgruppe erneut zugewiesen werden, beginnen sie, die reservierte Kapazität zu nutzen.
- **Manuelles Upgrade**: In diesem Modus geschieht nichts mit den VM-Instanzen der Skalierung gruppe, wenn die VM-Skalierungsgruppe an eine Kapazitätsreservierungsgruppe angefügt wird. Sie müssen jede VM-Skalierungsgruppe einzeln aktualisieren, indem Sie [sie mit dem neuesten Skalierungsgruppenmodell aktualisieren](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="api"></a>[API](#tab/api2)

1. Heben Sie die Zuordnung der VM-Skalierungsgruppe auf. 

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/deallocate?api-version=2021-04-01
    ```

1. Fügen Sie die `capacityReservationGroup`-Eigenschaft dem Skalierungsgruppenmodell hinzu. Erstellen Sie die folgende PUT-Anforderung an den *Microsoft.Compute*-Anbieter:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}?api-version=2021-04-01
    ```

    Fügen Sie die `capacityReservationGroup`-Eigenschaft in den Anforderungstext ein:

    ```json
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
             "capacityReservation": {
                      "capacityReservationGroup": {
                            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
                      }
                }
        }
    }
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell2) 

1. Heben Sie die Zuordnung der VM-Skalierungsgruppe auf. 

    ```powershell-interactive
    Stop-AzVmss
    -ResourceGroupName "myResourceGroup”
    -VMScaleSetName "myVmss”
    ```

1. Ordnen Sie die Skalierungsgruppe der Kapazitätsreservierungsgruppe zu. 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
    ```

Weitere Informationen finden Sie unter den Azure PowerShell-Befehlen [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss), [Get-AzVmss](/powershell/module/az.compute/get-azvmss) und [Update-AzVmss](/powershell/module/az.compute/update-azvmss).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="view-virtual-machine-scale-set-association-with-instance-view"></a>Anzeigen der Zuordnung der VM-Skalierungsgruppe mit der Instanzansicht 

Sobald die einheitliche VM-Skalierungsgruppe der Kapazitätsreservierungsgruppe zugeordnet wurde, werden alle nachfolgenden VM-Zuordnungen anhand der Kapazitätsreservierung ausgeführt. Azure findet automatisch die entsprechende Kapazitätsreservierung in der Gruppe und nutzt einen reservierten Slot. 

### <a name="api"></a>[API](#tab/api3) 

Die *Instanzansicht* der Kapazitätsreservierung spiegelt die neuen VMs der Skalierungsgruppe in den `virtualMachinesAssociated` & -Eigenschaften`virtualMachinesAllocated` wider, wie unten gezeigt: 

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?$expand=instanceview&api-version=2021-04-01 
```

```json
{ 
    "name": "<CapacityReservationGroupName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus" 
}, 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/virtualMachines/{VirtualMachineId}" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "<CapacityReservationName>", 
                    "utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/virtualMachines/{VirtualMachineId}" 
                            } 
                        ] 
                    },
                    "statuses": [ 
                        { 
                            "code": "ProvisioningState/succeeded", 
                            "level": "Info", 
                            "displayStatus": "Provisioning succeeded", 
                            "time": "2021-05-25T15:12:10.4165243+00:00" 
                        } 
                    ] 
                } 
            ] 
        } 
    } 
} 
```  

### <a name="powershell"></a>[PowerShell](#tab/powershell3) 

Zeigen Sie mithilfe von PowerShell die Zuordnung ihrer Skalierungsgruppe und Kapazitätsreservierungsgruppe in der Instanzansicht an. 

```powershell-interactive
$CapRes=
Get-AzCapacityReservationGroup
-ResourceGroupName <"ResourceGroupName">
-Name <"CapacityReservationGroupName">
-InstanceView

$CapRes.InstanceView.Utilizationinfo.VirtualMachinesAllocated
``` 

Weitere Informationen finden Sie unter dem Azure PowerShell-Befehle [Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationGroup).

### <a name="portal"></a>[Portal](#tab/portal3)

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Navigieren Sie zu Ihrer Kapazitätsreservierungsgruppe.
1. Wählen Sie links unter **Einstellung** die Option **Ressourcen** aus.
1. In der Tabelle sehen Sie alle VMs der Skalierungsgruppe, die der Kapazitätsreservierungsgruppe zugeordnet sind.
--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="region-and-availability-zones-considerations"></a>Überlegungen zur Region und zu Verfügbarkeitszonen 

VM-Skalierungsgruppen können regional oder in mindestens einer Verfügbarkeitszone erstellt werden, um sie vor Ausfällen auf Rechenzentrumsebene zu schützen. Weitere Informationen zu VM-Skalierungsgruppen mit mehreren Zonen finden Sie unter [VM-Skalierungsgruppen, die Verfügbarkeitszonen verwenden](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md).  

 
>[!IMPORTANT]
> Der Standort (Region und Verfügbarkeitszonen) der VM-Skalierungsgruppe und der Kapazitätsreservierungsgruppe muss übereinstimmen, damit die Zuordnung erfolgreich ist. Bei einer regionalen Skalierungsgruppe muss die Region der Skalierungsgruppe mit der Region der Kapazitätsreservierungsgruppe übereinstimmen. Bei einer zonalen Skalierungsgruppe müssen die Region und die Zonen der Skalierungsgruppe mit der Region und den Zonen der Kapazitätsreservierungsgruppe übereinstimmen. 


Wenn eine Skalierungsgruppe auf mehrere Zonen verteilt ist, wird immer versucht, eine gleichmäßige Bereitstellung über die enthaltenen Verfügbarkeitszonen zu erzielen. Aufgrund dieser gleichmäßigen Bereitstellung sollte eine Kapazitätsreservierungsgruppe immer die gleiche Anzahl reservierter VMs in jeder Zone haben. Um zu verdeutlichen, warum dies wichtig ist, sehen Sie sich das folgende Beispiel an.   

In diesem Beispiel ist für jede Zone eine andere Anzahl von VMs reserviert. Angenommen, die Skalierungsgruppe wird auf 75 Instanzen aufskaliert. Da die Skalierungsgruppe immer versucht, eine gleichmäßige Verteilung auf die Zonen vorzunehmen, sollte die VM-Verteilung wie folgt aussehen: 

| Zone  | Reservierte Anzahl  | Nein. Skalierungsgruppen-VMs in jeder Zone  | Nicht verwendete reservierte Anzahl  | Überlastet   |
|---|---|---|---|---|
| 1  | 40  | 25  | 15  | 0  |
| 2  | 20  | 25  | 0  | 5  |
| 3  | 15  | 25  | 0  | 10  |

In diesem Fall fallen für die Skalierungsgruppe zusätzliche Kosten für 15 nicht verwendete Instanzen in Zone 1 an. Die horizontale Skalierung verwendet ebenfalls 5 VMs in Zone 2 und 10 VMs in Zone 3, die nicht durch Kapazitätsreservierung geschützt sind. Wenn für jede Zone 25 Kapazitätsinstanzen reserviert sind, werden alle 75 VMs durch Kapazitätsreservierung geschützt, und für die Bereitstellung fallen keine zusätzlichen Kosten für nicht verwendete Instanzen an.  

Da die Reservierungen überlastet werden können, kann die Skalierungsgruppe weiterhin normal über die Grenzen der Reservierung hinaus skaliert werden. Der einzige Unterschied besteht darin, dass die VMs, die über der reservierten Menge liegen, nicht durch die SLA für Kapazitätsreservierungen abgedeckt werden. Weitere Informationen finden Sie unter [Überlasten der Kapazitätsreservierung](capacity-reservation-overallocate.md).


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfahren Sie, wie Sie eine Skalierungsgruppenzuordnung aus einer Kapazitätsreservierung entfernen](capacity-reservation-remove-virtual-machine-scale-set.md)