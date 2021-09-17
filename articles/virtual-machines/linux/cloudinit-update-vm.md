---
title: Verwenden von „cloud-init“ auf einer Linux-VM in Azure
description: Verwenden von cloud-init zum Aktualisieren und Installieren von Paketen auf einer Linux-VM während der Erstellung mit der Azure CLI
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 06/01/2021
ms.author: cynthn
ms.subservice: cloud-init
ms.openlocfilehash: a5bd15f19cd641ce7c10ea932084d36cca0d7901
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687900"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Verwenden von cloud-init zum Aktualisieren und Installieren von Paketen auf einer Linux-VM in Azure

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

In diesem Artikel wird gezeigt, wie Sie [cloud-init](https://cloudinit.readthedocs.io) zum Aktualisieren von Paketen auf einem virtuellen Linux-Computer (VM) oder in VM-Skalierungsgruppen während der Bereitstellung in Azure verwenden. Diese cloud-init-Skripts werden beim erstmaligen Starten ausgeführt, nachdem die Ressourcen von Azure bereitgestellt wurden. Weitere Informationen zur nativen Funktionsweise von „cloud-init“ in Azure und zu den unterstützten Linux-Distributionen finden Sie in der [Übersicht zu „cloud-init“](using-cloud-init.md).

## <a name="update-a-vm-with-cloud-init"></a>Aktualisieren einer VM mit cloud-init
Aus Sicherheitsgründen sollten Sie eine VM so konfigurieren, dass die neuesten Updates beim ersten Systemstart angewendet werden. Da cloud-init in verschiedenen Linux-Distributionen funktioniert, müssen weder `apt` oder `yum` für den Paket-Manager angegeben werden. Stattdessen definieren Sie `package_upgrade` und lassen die geeignete Methode für die verwendete Distribution vom cloud-init-Vorgang auswählen. 

In diesem Beispiel verwenden wir die Azure Cloud Shell. Um den Upgradevorgang in Aktion zu sehen, erstellen Sie eine Datei namens *cloud_init_upgrade.txt*, und fügen Sie die folgende Konfiguration ein: 

Wählen Sie die Schaltfläche **Ausprobieren** auf dem Codeblock unten aus, um die Cloud Shell zu öffnen. Um die Datei zu erstellen und eine Liste der verfügbaren Editoren in der Cloud Shell anzuzeigen, geben Sie Folgendes ein:

```azurecli-interactive
sensible-editor cloud_init_upgrade.txt 
```

Kopieren Sie den folgenden Text, und fügen Sie ihn in die Datei `cloud_init_upgrade.txt` ein. Stellen Sie sicher, dass die gesamte cloud-init-Datei ordnungsgemäß kopiert wird, insbesondere die erste Zeile.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Vor der Bereitstellung müssen Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe erstellen. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Erstellen Sie nun mit dem Befehl [az vm create](/cli/azure/vm) eine VM, und geben Sie mit dem Parameter `--custom-data` die cloud-init-Datei an, wie im Folgenden gezeigt wird:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

Stellen Sie eine SSH-Verbindung mit der öffentlichen IP-Adresse Ihrer VM her, die in der Ausgabe über den vorherigen Befehl gezeigt wird. Geben Sie Ihre eigene **publicIpAddress** wie folgt ein:

```bash
ssh <publicIpAddress>
```

Führen Sie das Paketverwaltungstool aus, und suchen Sie nach Updates.

```bash
sudo yum update
```

Da cloud-init beim Systemstart nach Updates gesucht und sie ggf. installiert hat, sollten keine zusätzlichen anzuwendenden Updates gefunden werden.  Sie sehen den Updatevorgang, die Anzahl der geänderten Pakete sowie die Installation von `httpd` durch Ausführung von `yum history` und erhalten eine Ausgabe ähnlich der folgenden.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Nächste Schritte
Weitere cloud-init-Beispiele für Änderungen an der Konfiguration finden Sie in den folgenden Themen:
 
- [Hinzufügen weiterer Linux-Benutzer zu virtuellen Computern](cloudinit-add-user.md)
- [Ausführen eines Paket-Managers zum Aktualisieren vorhandener Pakete beim ersten Start](cloudinit-update-vm.md)
- [Ändern des lokalen Hostnamens virtueller Computer](cloudinit-update-vm-hostname.md) 
- [Installieren von Anwendungspaketen, Aktualisieren von Konfigurationsdateien und Einfügen von Schlüsseln](tutorial-automate-vm-deployment.md)
