---
title: Erhalten von Wartungsbenachrichtigungen per CLI
description: Zeigen Sie mithilfe der Azure CLI Wartungsbenachrichtigungen für in Azure ausgeführte virtuelle Computer an, und starten Sie eine Self-Service-Wartung.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 8b6f40109993df4ecd41b6f17505882e4e840ad1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215527"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Behandeln von Benachrichtigungen zu geplanten Wartungen mit der Azure CLI

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Sie können mithilfe der CLI prüfen, wann die [Wartung](maintenance-notifications.md) Ihrer virtuellen Computer geplant ist. Informationen zu geplanten Wartungen können mithilfe von [azure vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view) angezeigt werden.
 
Wartungsinformationen werden nur zurückgegeben, wenn eine Wartung geplant ist. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

Ausgabe
```
      "maintenanceRedeployStatus": {
      "additionalProperties": {},
      "isCustomerInitiatedMaintenanceAllowed": true,
      "lastOperationMessage": null,
      "lastOperationResultCode": "None",
      "maintenanceWindowEndTime": "2018-06-04T16:30:00+00:00",
      "maintenanceWindowStartTime": "2018-05-21T16:30:00+00:00",
      "preMaintenanceWindowEndTime": "2018-05-19T12:30:00+00:00",
      "preMaintenanceWindowStartTime": "2018-05-14T12:30:00+00:00"
```

## <a name="start-maintenance"></a>Wartung starten

Der folgende Aufruf startet die Wartung für einen virtuellen Computer, wenn `IsCustomerInitiatedMaintenanceAllowed` auf „true“ festgelegt ist.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Klassische Bereitstellungen

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Wenn Sie ältere virtuelle Computer besitzen, die mit dem klassischen Bereitstellungsmodell bereitgestellt wurden, können Sie mit der klassischen Azure CLI virtuelle Computer abfragen und die Wartung initiieren.

Stellen Sie sicher, dass Sie den richtigen Modus für die Nutzung klassischer virtueller Computer verwenden. Geben Sie dazu Folgendes ein:

```
azure config mode asm
```

Geben Sie zum Abrufen des Wartungsstatus eines virtuellen Computers mit dem Namen *myVM* Folgendes ein:

```
azure vm show myVM 
``` 

Wenn Sie die Wartung auf dem klassischen virtuellen Computer *myVM* im Dienst *myService* und der Bereitstellung *myDeployment* starten möchten, geben Sie Folgendes ein:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Nächste Schritte

Sie können die geplante Wartung auch mithilfe von [Azure PowerShell](maintenance-notifications-powershell.md) oder über das [Portal](maintenance-notifications-portal.md) vornehmen.
