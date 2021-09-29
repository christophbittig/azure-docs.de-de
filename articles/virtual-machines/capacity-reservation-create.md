---
title: Erstellen einer Kapazitätsreservierung in Azure (Vorschau)
description: Learn, wie Sie Compute-Kapazität in einer Azure-Region oder einer Availability Zone reservieren können, indem Sie eine Kapazitätsreservierung erstellen.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: db9b8b71ddbdccb2cdc4893ce7ea041304bafba9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776612"
---
# <a name="create-a-capacity-reservation-preview"></a>Erstellen einer Kapazitätsreservierung (Vorschau)

Eine Kapazitätsreservierung wird immer als Teil einer Kapazitätsreservierungsgruppe erstellt. Der erste Schritt besteht darin, eine Gruppe zu erstellen, falls nicht bereits eine passende Gruppe existiert, und dann Reservierungen zu erstellen. Nach erfolgreicher Erstellung sind die Reservierungen sofort für die Nutzung mit virtuellen Computern verfügbar. Die Kapazität ist für Ihre Nutzung reserviert, solange die Reservierung nicht gelöscht wird.     

Eine gut formulierte Anforderung für eine Kapazitätsreservierungsgruppe sollte immer erfolgreich sein, da sie keine Kapazität reserviert. Sie fungiert lediglich als Container für Reservierungen. Eine Anforderung für eine Kapazitätsreservierung kann jedoch fehlschlagen, wenn Sie nicht über das erforderliche Kontingent für die VM-Serie verfügen oder wenn Azure nicht über genügend Kapazität verfügt, um die Anforderung zu erfüllen. Fordern Sie entweder mehr Kontingent an oder versuchen Sie eine andere VM-Größe, einen anderen Standort oder eine andere Zonenkombination. 

Die Erstellung einer Kapazitätsreservierung ist erfolgreich oder scheitert vollständig. Bei einer Anforderung zur Reservierung von 10 Instanzen wird nur dann ein Erfolg zurückgegeben, wenn alle 10 Instanzen zugewiesen werden konnten. Andernfalls schlägt die Erstellung der Kapazitätsreservierung fehl. 

> [!IMPORTANT]
> Die Kapazitätsreservierung befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-for-capacity-reservation"></a>Registrieren für Kapazitätsreservierung 

Bevor Sie das Feature zur Kapazitätsreservierung verwenden können, müssen Sie [Ihr Abonnement für die Vorschauversion registrieren](capacity-reservation-overview.md#register-for-capacity-reservation). Die Registrierung kann mehrere Minuten dauern. Sie können die Azure CLI oder PowerShell verwenden, um die Funktionsregistrierung abzuschließen.


## <a name="considerations"></a>Überlegungen

Die Kapazitätsreservierung muss die folgenden Regeln erfüllen: 
- Der Location-Parameter muss mit der Location-Eigenschaft für die übergeordnete Kapazitätsreservierungsgruppe übereinstimmen. Ein Konflikt führt zu einem Fehler. 
- Die VM muss in der Zielregion verfügbar sein. Andernfalls schlägt die Erstellung der Kapazitätsreservierung fehl. 
- Das Abonnement muss über ein ausreichendes genehmigtes Kontingent verfügen, das mindestens der Anzahl der für die VM-Serie und für die Region insgesamt reservierten VMs entspricht. Falls nötig, [fordern Sie mehr Kontingent](../azure-portal/supportability/per-vm-quota-requests.md) an.
- Jede Kapazitätsreservierungsgruppe kann genau eine Reservierung für eine bestimmte VM-Größe haben. Zum Beispiel kann nur eine Kapazitätsreservierung für die VM-Größe `Standard_D2s_v3` erstellt werden. Der Versuch, eine zweite Reservierung für `Standard_D2s_v3` in derselben Kapazitätsreservierungsgruppe zu erstellen, führt zu einem Fehler. Es kann jedoch eine weitere Reservierung in derselben Gruppe für andere VM-Größen erstellt werden, z. B. `Standard_D4s_v3`,`Standard_D8s_v3` usw.  
- Für eine Kapazitätsreservierungsgruppe, die Zonen unterstützt, wird jeder Reservierungstyp durch die Kombination von **VM Größe** und **Zone** definiert. Zum Beispiel wird eine Kapazitätsreservierung für `Standard_D2s_v3` in `Zone 1`, eine weitere Kapazitätsreservierung für `Standard_D2s_v3` in `Zone 2` und eine dritte Kapazitätsreservierung für `Standard_D2s_v3` in `Zone 3` unterstützt.


## <a name="create-a-capacity-reservation"></a>Erstellen Sie eine Kapazitätsreservierung 

### <a name="api"></a>[API](#tab/api1)

1. Erstellen einer Kapazitätsreservierungsgruppe (Vorschau) 

    Um eine Kapazitätsreservierungsgruppe zu erstellen, erstellen Sie die folgende PUT-Anforderung an den *Microsoft.Compute*-Anbieter: 
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}&api-version=2021-04-01
    ``` 
    
    Fügen Sie Folgendes in den Anforderungstext ein: 
    
    ```json
    { 
      "location":"eastus"
    } 
    ```
    
    Diese Gruppe wird erstellt, um Reservierungen für den Speicherort USA, Osten zu enthalten. 
    
    In diesem Beispiel wird die Gruppe nur regionale Reservierungen unterstützen, da zum Zeitpunkt der Erstellung keine Zonen angegeben wurden. Um eine zonale Gruppe zu erstellen, übermitteln Sie einen zusätzlichen Parameter *Zonen* im Anforderungstext (siehe unten): 
    
    ```json
    { 
      "location":"eastus",
      "zones": ["1", "2", "3"] 
    } 
    ```
 
1. Erstellen einer Kapazitätsreservierung 

    Um eine Reservierung zu erstellen, erstellen Sie die folgende PUT-Anforderung an den *Microsoft.Compute*-Anbieter: 
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01 
    ```
    
    Fügen Sie Folgendes in den Anforderungstext ein: 
    
    ```json
    { 
      "location": "eastus", 
      "sku": { 
        "name": "Standard_D2s_v3", 
        "capacity": 5 
      }, 
     "tags": { 
            "environment": "testing" 
    } 
    ```
    
    Mit der obigen Anforderung wird eine Reservierung am Standort "USA Osten" für 5 Mengen der D2s_v3 VM-Größe erstellt. 


### <a name="portal"></a>[Portal](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Geben Sie in der Suchleiste **Kapazitätsreservierungsgruppen** ein
1. Wählen Sie **Kapazitätsreservierungsgruppe** aus den Optionen
1. Klicken Sie auf **Erstellen**
1. Erstellen Sie unter der Registerkarte *Basic* eine Kapazitätsreservierungsgruppe:
    1. Wählen Sie ein **Abonnement**
    1. Wählen oder erstellen Sie eine **Ressourcengruppe**
    1. **Benennen Sie** Ihre Gruppe 
    1. Wählen Sie eine **Region** 
    1. Wählen Sie optional **Verfügbarkeitszonen** oder geben Sie keine Zonen an und überlassen Sie Azure die Auswahl
1. Wählen Sie **Weiter** aus.
1. Erstellen Sie auf der Registerkarte *Reservierungen* mindestens eine Kapazitätsreservierung:
    1. Geben Sie jeder Reservierung einen **Reservierungsnamen**, die Anzahl der VM **lnstanzen** und wählen Sie eine eindeutige **VM-Größe**
    1. In der Spalte *Kosten/Monat* werden die Abrechnungsinformationen auf der Grundlage Ihrer Auswahlen angezeigt
1. Wählen Sie **Weiter** aus.
1. Erstellen Sie optional Tags auf der Registerkarte *Tags*
1. Wählen Sie **Weiter** aus. 
1. Überprüfen Sie auf der Registerkarte *Bewerten + Erstellen* die Informationen zu Ihrer Kapazitätsreservierungsgruppe
1. Klicken Sie auf **Erstellen**


### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. Bevor Sie eine Kapazitätsreservierung erstellen können, erstellen Sie eine Ressourcengruppe mit `New-AzResourceGroup`. Das folgende Beispiel erstellt eine Ressourcengruppe *myResourceGroup* Speicherort USA Osten.

    ```powershell-interactive
    New-AzResourceGroup
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    ```

1. Erstellen Sie nun eine Kapazitätsreservierungsgruppe mit `New-AzCapacityReservationGroup`. Das folgende Beispiel erstellt eine Gruppe *myCapacityReservationGroup* am Speicherort USA Osten für alle 3 Verfügbarkeitszonen.

    ```powershell-interactive
    New-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    -Zone "1","2","3"
    -Name "myCapacityReservationGroup"
    ```

1. Sobald die Kapazitätsreservierungsgruppe erstellt ist, erstellen Sie eine neue Kapazitätsreservierung mit `New-AzCapacityReservation`. Das folgende Beispiel erstellt *myCapacityReservation* für 5 Mengen der VM-Größe Standard_D2s_v3 in Zone 1 des Speicherort USA Osten.

    ```powershell-interactive
    New-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    -Zone "1"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -Sku "Standard_D2s_v3"
    -CapacityToReserve 5
    ```

Weitere Informationen finden Sie unter Azure PowerShell-Befehle [New-AzResourceGroup](/powershell/module/az.Resources/new-azresourcegroup), [New-AzCapacityReservationGroup](/powershell/module/az.compute/new-azcapacityreservationgroup) und [New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation).


### <a name="arm-template"></a>[ARM-Vorlage](#tab/arm1)

Eine  [ARM-Vorlage](../azure-resource-manager/templates/overview.md)  ist eine JSON-Datei (JavaScript Object Notation), die die Infrastruktur und Konfiguration für Ihr Projekt definiert. Die Vorlage verwendet eine deklarative Syntax. In deklarativer Syntax beschreiben Sie Ihre beabsichtigte Bereitstellung, ohne die Reihenfolge der Programmierbefehle zur Erstellung der Bereitstellung zu schreiben. 

Mit ARM-Vorlagen können Sie Gruppen mit verwandten Ressourcen bereitstellen. Sie können in einer einzelnen Vorlage eine Kapazitätsreservierungsgruppe und Kapazitätsreservierungen erstellen. Sie können Vorlagen über das Azure-Portal, die Azure CLI oder Azure PowerShell oder über CI/CD-Pipelines (Continuous Integration/Continuous Delivery) bereitstellen. 

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, benutzen Sie irgendeine der folgenden Vorlagen: 

- [Erstellen einer zonalen Kapazitätsreservierung](https://github.com/Azure/on-demand-capacity-reservation/blob/main/ZonalCapacityReservation.json)
- [Erstellen eines virtuellen Computers mit Kapazitätsreservierung](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineWithReservation.json)
- [Erstellen von Virtual Machine Scale Sets mit Kapazitätsreservierung](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineScaleSetWithReservation.json)


--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="check-on-your-capacity-reservation"></a>Überprüfen Sie Ihre Kapazitätsreservierung 

Nachdem die Kapazitätsreservierung erfolgreich erstellt ist, steht sie sofort für die Verwendung mit VMs zur Verfügung. 

### <a name="api"></a>[API](#tab/api2)

```rest
GET  
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
```
 
```json
{ 
    "name": "<CapacityReservationName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}", 
    "type": "Microsoft.Compute/capacityReservationGroups/capacityReservations", 
    "location": "eastus", 
    "tags": { 
        "environment": "testing" 
    }, 
    "sku": { 
        "name": "Standard_D2s_v3", 
        "capacity": 5 
    }, 
    "properties": { 
        "reservationId": "<reservationId>", 
         "provisioningTime": "<provisioningTime>", 
         "provisioningState": "Updating" 
    } 
} 
```

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

Überprüfen Sie Ihre Kapazitätsreservierung:

```powershell-interactive
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName <"CapacityReservationGroupName">
-Name <"CapacityReservationName">
```

Um die VM-Größe und die reservierte Menge zu finden, verwenden Sie Folgendes: 

```powershell-interactive
$CapRes =
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName <"CapacityReservationGroupName">
-Name <"CapacityReservationName">

$CapRes.sku
```

Weitere Informationen finden Sie unter den Azure PowerShell-Befehlen [Get AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation).

### <a name="portal"></a>[Portal](#tab/portal3)

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Geben Sie in der Suchleiste **Kapazitätsreservierungsgruppen** ein
1. Wählen Sie **Kapazitätsreservierungsgruppe** aus den Optionen
1. Wählen Sie in der Liste den Namen der Kapazitätsreservierungsgruppe aus, die Sie gerade erstellt haben
1. Wählen Sie **Übersicht** auf der linken Seite
1. Wählen Sie **Reservierungen** aus
1. In dieser Ansicht können Sie alle Reservierungen in der Gruppe zusammen mit der VM-Größe und der reservierten Menge ansehen

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Lernen Sie, wie Sie eine Kapazitätsreservierung ändern](capacity-reservation-modify.md)
