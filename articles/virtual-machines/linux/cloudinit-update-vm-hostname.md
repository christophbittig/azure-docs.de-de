---
title: Verwenden von cloud-init zum Festlegen eines Hostnamens für eine Linux-VM
description: Erfahren Sie, wie Sie cloud-init zum Anpassen einer Linux-VM während der Erstellung mithilfe der Azure CLI verwenden.
author: mimckitt
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: mimckitt
ms.subservice: cloud-init
ms.openlocfilehash: b3944edfae6fe1fa98ee7adc0ed20575fe0deedc
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696198"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Verwenden von cloud-init zum Festlegen eines Hostnamens für eine Linux-VM in Azure

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

In diesem Artikel wird gezeigt, wie Sie [cloud-init](https://cloudinit.readthedocs.io) zum Festlegen eines Hostnamens für einen virtuellen Computer (VM) oder eine VM-Skalierungsgruppe (VMSS) während der Bereitstellung in Azure verwenden. Diese cloud-init-Skripts werden beim erstmaligen Starten ausgeführt, nachdem die Ressourcen von Azure bereitgestellt wurden. Weitere Informationen zur nativen Funktionsweise von „cloud-init“ in Azure und zu den unterstützten Linux-Distributionen finden Sie in der [Übersicht zu „cloud-init“](using-cloud-init.md).

## <a name="set-the-hostname-with-cloud-init"></a>Festlegen des Hostnamens mit cloud-init
Standardmäßig ist der Hostname mit dem VM-Namen identisch, wenn Sie einen neuen virtuellen Computer in Azure erstellen.  Um mit [az vm create](/cli/azure/vm) beim Erstellen einer VM in Azure ein cloud-init-Skript zum Ändern des Standardhostnamens auszuführen, geben Sie die cloud-init-Datei mit dem Schalter `--custom-data` an.  

Um den Upgradevorgang in Aktion zu sehen, erstellen Sie in Ihrer aktuellen Shell eine Datei mit dem Namen *cloud_init_hostname.txt* und fügen die unten angegebene Konfiguration ein. Erstellen Sie für dieses Beispiel die Datei in der Cloud Shell, nicht auf dem lokalen Computer. Dazu können Sie einen beliebigen Editor verwenden. Geben Sie `sensible-editor cloud_init_hostname.txt` ein, um die Datei zu erstellen und eine Liste der verfügbaren Editoren anzuzeigen. Wählen Sie #1 aus, um den Editor **nano** zu verwenden. Stellen Sie sicher, dass die gesamte cloud-init-Datei ordnungsgemäß kopiert wird, insbesondere die erste Zeile.  

```yaml
#cloud-config
fqdn: myhostname
```

Vor der Bereitstellung dieses Images müssen Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe erstellen. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Erstellen Sie nun mit dem Befehl [az vm create](/cli/azure/vm) eine VM, und geben Sie mit `--custom-data cloud_init_hostname.txt` die cloud-init-Datei an, wie im Folgenden gezeigt wird:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Nach der Erstellung zeigt die Azure CLI Informationen zur VM an. Verwenden Sie `publicIpAddress`, um eine SSH-Verbindung mit Ihrer VM herzustellen. Geben Sie Ihre eigene Adresse wie folgt ein:

```bash
ssh <publicIpAddress>
```

Um den Namen der VM anzuzeigen, verwenden Sie den Befehl `hostname` wie folgt:

```bash
hostname
```

Die VM sollte den Hostnamen als den in der cloud-init-Datei festgelegten Wert melden, wie in der folgenden Beispielausgabe gezeigt wird:

```bash
myhostname
```

## <a name="next-steps"></a>Nächste Schritte
Weitere cloud-init-Beispiele für Änderungen an der Konfiguration finden Sie in den folgenden Themen:
 
- [Hinzufügen weiterer Linux-Benutzer zu virtuellen Computern](cloudinit-add-user.md)
- [Ausführen eines Paket-Managers zum Aktualisieren vorhandener Pakete beim ersten Start](cloudinit-update-vm.md)
- [Ändern des lokalen Hostnamens virtueller Computer](cloudinit-update-vm-hostname.md) 
- [Installieren von Anwendungspaketen, Aktualisieren von Konfigurationsdateien und Einfügen von Schlüsseln](tutorial-automate-vm-deployment.md)
