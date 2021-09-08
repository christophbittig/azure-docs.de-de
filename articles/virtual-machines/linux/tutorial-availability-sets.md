---
title: Bereitstellen von virtuellen Computern in einer Verfügbarkeitsgruppe mithilfe der Azure CLI
description: Hier erfahren Sie, wie Sie die Azure CLI zum Bereitstellen hoch verfügbarer virtueller Maschinen in Verfügbarkeitsgruppen verwenden.
documentationcenter: ''
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 784adc6829a66269fb863cce7e1e103a45fde7d9
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114455672"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-cli"></a>Erstellen und Bereitstellen virtueller Computer in einer Verfügbarkeitsgruppe mithilfe der Azure CLI

In diesem Tutorial erfahren Sie, wie Sie die Verfügbarkeit und Zuverlässigkeit Ihrer Lösungen für virtuelle Computer in Azure mithilfe von sogenannten Verfügbarkeitsgruppen erhöhen. Verfügbarkeitsgruppen sorgen dafür, dass die von Ihnen in Azure bereitgestellten virtuellen Computer auf mehrere isolierte Hardwarecluster verteilt werden. Hierdurch wird sichergestellt, dass sich Hardware- oder Softwarefehler in Azure nur auf einen Teil Ihrer VMs auswirken und die Lösung insgesamt verfügbar und betriebsbereit bleibt.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Verfügbarkeitsgruppe erstellen
> * Erstellen eines virtuellen Computers in einer Verfügbarkeitsgruppe
> * Überprüfen der verfügbaren VM-Größen

Dieses Tutorial verwendet die CLI innerhalb des Diensts [Azure Cloud Shell](../../cloud-shell/overview.md), der ständig auf die neueste Version aktualisiert wird. Wählen Sie zum Öffnen von Cloud Shell oben in einem Codeblock die Option **Ausprobieren** aus.

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial die Azure CLI-Version 2.0.30 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-an-availability-set"></a>Verfügbarkeitsgruppe erstellen

Eine Verfügbarkeitsgruppe können Sie mithilfe von [az vm availability-set create](/cli/azure/vm/availability-set) erstellen. In diesem Beispiel wird die Anzahl von Update- und Fehlerdomänen für die Verfügbarkeitsgruppe *myAvailabilitySet* in der Ressourcengruppe *myResourceGroupAvailability* auf *2* festgelegt.

Erstellen Sie zunächst mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe und anschließend die Verfügbarkeitsgruppe:

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Mithilfe von Verfügbarkeitsgruppen können Sie Ressourcen in Fehlerdomänen und Updatedomänen isolieren. Eine **Fehlerdomäne** stellt eine isolierte Sammlung von Server-, Netzwerk- und Speicherressourcen dar. Im vorherigen Beispiel wird die Verfügbarkeitsgruppe auf mindestens zwei Fehlerdomänen verteilt, wenn die virtuellen Computer bereitgestellt werden. Die Verfügbarkeitsgruppe wird auch auf zwei **Updatedomänen** verteilt. Mit zwei Updatedomänen wird sichergestellt, dass die VM-Ressourcen bei Softwareupdates in Azure isoliert sind. Hierdurch wird verhindert, dass die gesamte Software, die auf der VM ausgeführt wird, gleichzeitig aktualisiert wird.


## <a name="create-vms-inside-an-availability-set"></a>Erstellen von virtuellen Computern in einer Verfügbarkeitsgruppe

Virtuelle Computer müssen in der Verfügbarkeitsgruppe erstellt werden, um sicherzustellen, dass sie ordnungsgemäß auf die Hardwarekomponenten verteilt werden. Ein vorhandener virtueller Computer kann einer Verfügbarkeitsgruppe nach seiner Erstellung nicht mehr hinzugefügt werden.

Wenn ein virtueller Computer mit [az vm create](/cli/azure/vm) erstellt wird, können Sie den Parameter `--availability-set` verwenden, um den Namen der Verfügbarkeitsgruppe anzugeben.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

Die Verfügbarkeitsgruppe enthält nun zwei virtuelle Computer. Da sie sich in derselben Verfügbarkeitsgruppe befinden, stellt Azure sicher, dass die VMs und alle ihre Ressourcen (einschließlich Datenträger) auf isolierte physische Hardware verteilt werden. Mithilfe dieser Verteilung wird eine deutlich höhere Verfügbarkeit der gesamten VM-Lösung sichergestellt.

Die Verteilung der Verfügbarkeitsgruppen kann im Portal unter „Ressourcengruppen“ > „myResourceGroupAvailability“ > „myAvailabilitySet“ angezeigt werden. Die virtuellen Computer werden wie im folgenden Beispiel auf die beiden Fehler- und Updatedomänen verteilt:

![Verfügbarkeitsgruppe im Portal](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Prüfen der verfügbaren VM-Größen

Weitere virtuelle Computer können der Verfügbarkeitsgruppe später hinzugefügt werden, wenn auf der Hardware VM-Größen verfügbar sind. Verwenden Sie [az vm availability-set list-sizes](/cli/azure/vm/availability-set#az_vm_availability_set_list_sizes), um alle verfügbaren Größen im Hardwarecluster für die Verfügbarkeitsgruppe aufzulisten:

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Verfügbarkeitsgruppe erstellen
> * Erstellen eines virtuellen Computers in einer Verfügbarkeitsgruppe
> * Überprüfen der verfügbaren VM-Größen

Im nächsten Tutorial erhalten Sie Informationen zu VM-Skalierungsgruppen.

> [!div class="nextstepaction"]
> [Erstellen einer VM-Skalierungsgruppe](tutorial-create-vmss.md)

* Weitere Informationen zu Verfügbarkeitszonen finden Sie in der [Dokumentation zu Verfügbarkeitszonen](../../availability-zones/az-overview.md).
* Weitere Dokumentation zu Verfügbarkeitsgruppen und -zonen finden Sie auch unter [Verfügbarkeitsoptionen für Azure Virtual Machines](../availability.md).
* Wenn Sie Verfügbarkeitszonen ausprobieren möchten, sehen Sie sich den Artikel [Erstellen eines virtuellen Linux-Computers in einer Verfügbarkeitszone mit der Azure CLI](./create-cli-availability-zone.md) an.
