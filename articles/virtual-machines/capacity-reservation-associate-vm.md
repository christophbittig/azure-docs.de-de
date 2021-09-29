---
title: Zuordnen eines virtuellen Computers zu einer Kapazitätsreservierungsgruppe (Vorschau)
description: Hier erfahren Sie, wie Sie einer Kapazitätsreservierungsgruppe einen neuen oder vorhandenen virtuellen Computer zuordnen.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 6253039c2c256e421815cad30f06780bc6c89694
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774346"
---
# <a name="associate-a-vm-to-a-capacity-reservation-group-preview"></a>Zuordnen einer VM zu einer Kapazitätsreservierungsgruppe (Vorschau) 

Dieser Artikel führt Sie durch die Schritte zum Zuordnen eines neuen oder vorhandenen virtuellen Computers zu einer Kapazitätsreservierungsgruppe. Weitere Informationen zu Kapazitätsreservierungen finden Sie in dem [Übersichtsartikel](capacity-reservation-overview.md). 

> [!IMPORTANT]
> Die Kapazitätsreservierung befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-for-capacity-reservation"></a>Registrieren für Kapazitätsreservierung 

Bevor Sie das Feature zur Kapazitätsreservierung verwenden können, müssen Sie [Ihr Abonnement für die Vorschauversion registrieren](capacity-reservation-overview.md#register-for-capacity-reservation). Die Registrierung kann mehrere Minuten dauern. Sie können die Azure CLI oder PowerShell verwenden, um die Featureregistrierung abzuschließen.

## <a name="associate-a-new-vm"></a>Zuordnen eines neuen virtuellen Computers

Sie müssen explizit als Eigenschaft des virtuellen Computers auf die Gruppe verweisen, um der Kapazitätsreservierungsgruppe einen neuen virtuellen Computer zuzuordnen. Dieser Verweis schützt die übereinstimmende Reservierung in der Gruppe vor versehentlicher Nutzung durch weniger kritische Anwendungen und Workloads, die sie nicht verwenden sollen.  

### <a name="api"></a>[API](#tab/api1)

Erstellen Sie die folgende PUT-Anforderung an den *Microsoft.Compute*-Anbieter, um die `capacityReservationGroup`-Eigenschaft zu einer VM hinzuzufügen.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}?api-version=2021-04-01
```

Fügen Sie im Anforderungstext die `capacityReservationGroup`-Eigenschaft wie unten dargestellt ein:

```json
{ 
  "location": "eastus", 
  "properties": { 
    "hardwareProfile": { 
      "vmSize": "Standard_D2s_v3" 
    }, 
    … 
   "CapacityReservation":{ 
    "capacityReservationGroup":{ 
        "id":"subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}" 
    } 
    "storageProfile": { 
    … 
    }, 
    "osProfile": { 
    … 
    }, 
    "networkProfile": { 
     …     
    } 
  } 
} 
```

### <a name="portal"></a>[Portal](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Geben Sie in die Suchleiste **virtuelle Computer** ein.
1. Wählen Sie unter *Dienste* die Option **Virtuelle Computer** aus.
1. Wählen Sie auf der Seite *Virtuelle Computer* die Option **Erstellen** und dann **Virtueller Computer** aus.
1. Stellen Sie auf der Registerkarte *Grundlagen* unter *Projektdetails* sicher, dass das richtige **Abonnement** ausgewählt ist, und wählen Sie dann eine neue **Ressourcengruppe** erstellen oder eine bestehende Gruppe aus.
1. Geben Sie unter *Instanzdetails* den **Namen** des virtuellen Computers ein, und wählen Sie Ihre **Region** aus.
1. Wählen Sie ein **Image** und die **Größe des virtuellen Computers** aus.
1. Geben Sie unter *Administratorkonto* einen **Benutzernamen** und ein **Kennwort** an.
    1. Das Kennwort muss mindestens zwölf Zeichen lang sein und die definierten Komplexitätsanforderungen erfüllen.
1. Wählen Sie unter *Regeln für eingehende Ports* die Option **Ausgewählte Ports zulassen** aus, und wählen Sie dann **RDP** (3389) und **HTTP** (80) aus der Dropdownliste aus.
1. Navigieren Sie zum Abschnitt *Erweitert*.
1. Wählen Sie in der Dropdownliste **Kapazitätsreservierungen** die Kapazitätsreservierungsgruppe aus, der die VM zugeordnet werden soll.
1. Wählen Sie die Schaltfläche **Überprüfen + erstellen** aus. 
1. Wählen Sie nach der Überprüfung die Schaltfläche **Erstellen** aus. 
1. Wenn Ihre Bereitstellung abgeschlossen ist, wählen Sie **Zu Ressource wechseln** aus.


### <a name="powershell"></a>[PowerShell](#tab/powershell1)

Verwenden Sie `New-AzVM`, um einen neuen virtuellen Computer zu erstellen und die Eigenschaft `CapacityReservationGroupId` hinzuzufügen, um sie einer vorhandenen Kapazitätsreservierungsgruppe zuzuordnen. Im folgenden Beispiel wird eine Standard_D2s_v3 VM am Standort USA, Osten erstellt und der VM einer Kapazitätsreservierungsgruppe zugeordnet.

```powershell-interactive
New-AzVm
-ResourceGroupName "myResourceGroup"
-Name "myVM"
-Location "eastus"
-VirtualNetworkName "myVnet"
-SubnetName "mySubnet"
-SecurityGroupName "myNetworkSecurityGroup"
-PublicIpAddressName "myPublicIpAddress"
-OpenPorts 80,3389
-Size "Standard_D2s_v3"
-CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
```

Weitere Informationen finden Sie unter dem Azure PowerShell-Befehl [New-AzVM](/powershell/module/az.compute/new-azvm).

### <a name="arm-template"></a>[ARM-Vorlage](#tab/arm1)

Eine  [ARM-Vorlage](../azure-resource-manager/templates/overview.md)  ist eine JSON-Datei (JavaScript Object Notation), die die Infrastruktur und Konfiguration für Ihr Projekt definiert. Die Vorlage verwendet eine deklarative Syntax. In deklarativer Syntax beschreiben Sie Ihre beabsichtigte Bereitstellung, ohne die Reihenfolge der Programmierbefehle zur Erstellung der Bereitstellung zu schreiben. 

Mit ARM-Vorlagen können Sie Gruppen mit verwandten Ressourcen bereitstellen. Sie können in einer einzelnen Vorlage eine Kapazitätsreservierungsgruppe und Kapazitätsreservierungen erstellen. Sie können Vorlagen über das Azure-Portal, die Azure CLI oder Azure PowerShell oder über CI/CD-Pipelines (Continuous Integration/Continuous Delivery) bereitstellen. 

Wenn Ihre Umgebung die Voraussetzungen erfüllt, und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, benutzen Sie diese Vorlage: [VM mit Kapazitätsreservierung erstellen](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineWithReservation.json). 


--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="associate-an-existing-vm"></a>Zuordnen einer vorhandenen VM 

Während sich die Kapazitätsreservierung in der Vorschau befindet, ist es zum Zuordnen eines vorhandenen virtuellen Computers zu einer Kapazitätsreservierungsgruppe erforderlich, zuerst die Zuordnung des virtuellen Computers aufzuheben und dann die Zuweisung zum Zeitpunkt der Neuzuordnung zu erledigen. Dadurch wird sichergestellt, dass der virtuelle Computer einen der leeren Slots in der Reservierung nutzt. 

### <a name="api"></a>[API](#tab/api2)

1. Heben Sie die Zuordnung der VM auf. 

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}/deallocate?api-version=2021-04-01
    ```

1. Legen Sie die `capacityReservationGroup`-Eigenschaft auf die VM fest. Erstellen Sie die folgende PUT-Anforderung an den *Microsoft.Compute*-Anbieter:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}?api-version=2021-04-01
    ```

    Fügen Sie die `capacityReservationGroup`-Eigenschaft in den Anforderungstext ein: 
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
            }
        }
    }
    }
    ```


### <a name="portal"></a>[Portal](#tab/portal2)

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Navigieren Sie zu Ihrer VM.
1. Wählen Sie **Übersicht** auf der linken Seite.
1. Wählen Sie **Beenden** oben auf der Seite aus, um die Zuordnung der VM aufzuheben. 
1. Wechseln Sie links zu **Konfigurationen**.
1. Wählen Sie in der Dropdownliste **Kapazitätsreservierungsgruppe** die Gruppe aus, der die VM zugeordnet werden soll. 


### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. Aufheben der Zuordnung der VM

    ```powershell-interactive
    Stop-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    ```

1. Zuordnen der VM zu einer Kapazitätsreservierungsgruppe

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId "subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}"
    ```

Weitere Informationen finden Sie unter Azure PowerShell-Befehle: [Stop-AzVm](/powershell/module/az.compute/stop-azvm), [Get-AzVM](/powershell/module/az.compute/get-azvm) und [Update-AzVM](/powershell/module/az.compute/update-azvm).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="view-vm-association-with-instance-view"></a>Anzeigen der VM-Zuordnung mit der Instanzansicht 

Nachdem die `capacityReservationGroup`-Eigenschaft festgelegt wurde, besteht nun eine Zuordnung zwischen dem virtuellen Computer und der Gruppe. Azure findet automatisch die entsprechende Kapazitätsreservierung in der Gruppe und nutzt einen reservierten Slot. Die *Instanzansicht* der Kapazitätsreservierung spiegelt die neue VM in der `virtualMachinesAllocated`-Eigenschaft wider, wie unten dargestellt: 

### <a name="api"></a>[API](#tab/api3)

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{capacityReservationGroupName}?$expand=instanceView&api-version=2021-04-01 
```

```json
{
   "name":"{CapacityReservationGroupName}",
   "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}",
   "type":"Microsoft.Compute/capacityReservationGroups",
   "location":"eastus",
   "properties":{
      "capacityReservations":[
         {
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/ {CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}"
         }
      ],
      "virtualMachinesAssociated":[
         {
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{myVM}"
         }
      ],
      "instanceView":{
         "capacityReservations":[
            {
               "name":"{CapacityReservationName}",
               "utilizationInfo":{
                  "virtualMachinesAllocated":[
                     {
                        "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{myVM}"
                     }
                  ]
               },
               "statuses":[
                  {
                     "code":"ProvisioningState/succeeded",
                     "level":"Info",
                     "displayStatus":"Provisioning succeeded",
                     "time":"2021-05-25T15:12:10.4165243+00:00"
                  }
               ]
            }
         ]
      }
   }
} 
``` 
### <a name="powershell"></a>[PowerShell](#tab/powershell3)

```powershell-interactive
$CapRes=
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName] <"CapacityReservationGroupName">
-Name <"CapacityReservationName">
-InstanceView

$CapRes.InstanceView.Utilizationinfo.VirtualMachinesAllocated
```

Weitere Informationen finden Sie unter den Azure PowerShell-Befehlen [Get AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation).


### <a name="portal"></a>[Portal](#tab/portal3)

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Gehen Sie zu Ihrer Kapazitätsreservierungsgruppe.
1. Wählen Sie links unter **Einstellungen** die Option **Ressourcen** aus.
1. Sehen Sie sich die Tabelle an, um alle VMs anzuzeigen, die der Kapazitätsreservierungsgruppe zugeordnet sind.  

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Aufheben einer Zuordnung virtueller Computer zu einer Kapazitätsreservierungsgruppe](capacity-reservation-remove-vm.md)