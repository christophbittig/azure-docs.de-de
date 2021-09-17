---
title: Verwenden von „cloud-init“ zum Konfigurieren einer Swap-Partition auf einer Linux-VM
description: Es wird beschrieben, wie Sie „cloud-init“ zum Konfigurieren einer Swap-Partition auf einer Linux-VM während der Erstellung mit der Azure CLI verwenden.
author: mimckitt
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: mimckitt
ms.subservice: cloud-init
ms.openlocfilehash: 6ac16d84e71bbb59ee52b84f4a3e1e9fbcd80b32
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687918"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>Verwenden von „cloud-init“ zum Konfigurieren einer Swap-Partition auf einer Linux-VM

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

In diesem Artikel wird veranschaulicht, wie Sie [cloud-init](https://cloudinit.readthedocs.io) zum Konfigurieren der Swap-Partition auf verschiedenen Linux-Distributionen verwenden. Die Swap-Partition wurde traditionell vom Linux-Agent (WALA) konfiguriert – abhängig davon, für welche Distributionen eine Auslagerungsdatei benötigt wurde.  In diesem Dokument wird das Vorgehen für die bedarfsgesteuerte Erstellung der Swap-Partition zur Bereitstellungszeit unter Verwendung von „cloud-init“ beschrieben.  Weitere Informationen zur nativen Funktionsweise von „cloud-init“ in Azure und zu den unterstützten Linux-Distributionen finden Sie in der [Übersicht zu „cloud-init“](using-cloud-init.md).

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Erstellen einer Swap-Partition für Ubuntu-basierte Images
In Azure werden für Ubuntu-Katalogimages standardmäßig keine Swap-Partitionen erstellt. Informationen zum Aktivieren der Konfiguration einer Swap-Partition während der VM-Bereitstellung mit „cloud-init“ finden Sie im Ubuntu-Wiki im [AzureSwapPartitions-Dokument](https://wiki.ubuntu.com/AzureSwapPartitions).

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Erstellen einer Swap-Partition für Red Hat- und CentOS-basierte Images

Erstellen Sie in der aktuellen Shell eine Datei mit dem Namen *cloud_init_swappart.txt*, und fügen Sie die folgende Konfiguration ein. Erstellen Sie für dieses Beispiel die Datei in der Cloud Shell, nicht auf dem lokalen Computer. Dazu können Sie einen beliebigen Editor verwenden. Geben Sie `sensible-editor cloud_init_swappart.txt` ein, um die Datei zu erstellen und eine Liste der verfügbaren Editoren anzuzeigen. Wählen Sie #1 aus, um den Editor **nano** zu verwenden. Stellen Sie sicher, dass die gesamte cloud-init-Datei ordnungsgemäß kopiert wird, insbesondere die erste Zeile.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw,nofail,x-systemd.requires=cloud-init.service", "0", "0"]
```

Die Bereitstellung wird mit der `nofail`-Option erstellt, um sicherzustellen, dass der Start auch dann fortgesetzt wird, wenn die Bereitstellung nicht erfolgreich abgeschlossen wurde.

Vor der Bereitstellung dieses Images müssen Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe erstellen. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Erstellen Sie nun mit dem Befehl [az vm create](/cli/azure/vm) eine VM, und geben Sie mit `--custom-data cloud_init_swappart.txt` die cloud-init-Datei an, wie im Folgenden gezeigt wird:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>Überprüfen der Erstellung einer Swap-Partition
Stellen Sie eine SSH-Verbindung mit der öffentlichen IP-Adresse Ihrer VM her, die in der Ausgabe über den vorherigen Befehl gezeigt wird. Geben Sie Ihre eigene **publicIpAddress** wie folgt ein:

```bash
ssh <publicIpAddress>
```

Nachdem Sie eine SSH-Verbindung mit der VM hergestellt haben, können Sie überprüfen, ob die Swap-Partition erstellt wurde.

```bash
swapon -s
```

Die Ausgabe dieses Befehls sollte wie folgt aussehen:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Wenn für Ihr vorhandenes Azure-Image eine Swap-Partition konfiguriert wurde und Sie die Konfiguration der Swap-Partition für neue Images ändern möchten, müssen Sie die vorhandene Swap-Partition entfernen. Weitere Informationen finden Sie im Dokument „Anpassen von Images für die Bereitstellung mit cloud-init“.

## <a name="next-steps"></a>Nächste Schritte
Weitere cloud-init-Beispiele für Änderungen an der Konfiguration finden Sie in den folgenden Themen:
 
- [Hinzufügen weiterer Linux-Benutzer zu virtuellen Computern](cloudinit-add-user.md)
- [Ausführen eines Paket-Managers zum Aktualisieren vorhandener Pakete beim ersten Start](cloudinit-update-vm.md)
- [Ändern des lokalen Hostnamens virtueller Computer](cloudinit-update-vm-hostname.md) 
- [Installieren von Anwendungspaketen, Aktualisieren von Konfigurationsdateien und Einfügen von Schlüsseln](tutorial-automate-vm-deployment.md)
