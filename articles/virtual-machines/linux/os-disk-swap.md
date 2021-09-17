---
title: Wechsel zwischen Betriebssystemdatenträgern unter Verwendung der Azure CLI
description: Ändern Sie den von einer Azure-VM verwendeten Betriebssystemdatenträger mithilfe der Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: f186052117f40167c9e4e72e350bf689f43bd8b1
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692839"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-azure-cli"></a>Ändern des von einer Azure-VM verwendeten Betriebssystemdatenträgers mithilfe der Azure CLI

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Wenn Sie über einen virtuellen Computer verfügen, aber den Datenträger für einen Sicherungsdatenträger oder einem anderen Betriebssystem-Datenträger austauschen möchten, können Sie die Betriebssystem-Datenträger mit der Azure CLI austauschen. Sie müssen den virtuellen Computer nicht löschen und neu erstellen. Sie können auch einen verwalteten Datenträger in einer anderen Ressourcengruppe verwenden, solange er nicht bereits in Gebrauch ist.

Der virtuelle Computer muss sich im Zustand „Beendet (Zuordnung aufgehoben)“ befinden, und dann kann die Ressourcen-ID des verwalteten Datenträgers durch die Ressourcen-ID eines anderen verwalteten Datenträgers ersetzt werden. 

Stellen Sie sicher, dass VM-Größe und Speichertyp mit dem Datenträger kompatibel sind, den Sie anfügen möchten. Wenn der Datenträger, den Sie verwenden möchten, sich z.B. im Storage Premium befindet, dann muss der virtuelle Computer für Storage Premium geeignet sein (z.B. eine Größe der DS-Serie).

Für diesen Artikel ist mindestens Version 2.0.25 der Azure CLI erforderlich. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli). 


Rufen Sie mit [az disk list](/cli/azure/disk) eine Liste der Datenträger in der Ressourcengruppe ab.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Beenden Sie mit [az vm stop](/cli/azure/vm) den virtuellen Computer (heben Sie seine Zuordnung auf), ehe Sie die Datenträger austauschen.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Verwenden Sie [az vm update](/cli/azure/vm#az_vm_update) mit der vollständigen Ressourcen-ID des neuen Datenträgers für den `--osdisk`-Parameter. 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Starten Sie den virtuellen Computer mit [az vm start](/cli/azure/vm) neu.

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Nächste Schritte**

Wie Sie eine Kopie eines Datenträgers erstellen, erfahren Sie unter [Erstellen einer Momentaufnahme](snapshot-copy-managed-disk.md).
