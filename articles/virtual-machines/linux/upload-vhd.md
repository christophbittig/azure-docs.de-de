---
title: Hochladen oder Kopieren einer benutzerdefinierten Linux-VM mithilfe der Azure CLI
description: Laden Sie mit dem Resource Manager-Bereitstellungsmodell und der Azure-Befehlszeilenschnittstelle einen benutzerdefinierten virtuellen Computer hoch, oder kopieren Sie ihn.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 4e4738f46b2d5f8d90f0d1fd79738270e01de64f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698924"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Erstellen eines virtuellen Linux-Computers auf der Grundlage eines benutzerdefinierten Datenträgers mithilfe der Azure-Befehlszeilenschnittstelle

**Gilt für:** :heavy_check_mark: Linux-VMs 

<!-- rename to create-vm-specialized -->

In diesem Artikel erfahren Sie, wie Sie eine benutzerdefinierte virtuelle Festplatte (virtual hard disk, VHD) hochladen und eine vorhandene virtuelle Festplatte in Azure kopieren. Die neu erstellte VHD wird dann verwendet, um neue virtuelle-Linux-Computer (virtual machines, VMs) zu erstellen. Sie können eine Linux-Distribution installieren und konfigurieren und die VHD dann zur Erstellung eines neuen virtuellen Azure-Computers verwenden.

Wenn Sie auf der Grundlage Ihres benutzerdefinierten Datenträgers mehrere virtuelle Computer erstellen möchten, erstellen Sie zunächst ein Image ihres virtuellen Computers oder Ihrer VHD. Weitere Informationen finden Sie in [Erstellen eines benutzerdefinierten Images eines virtuellen Azure-Computers mithilfe der Befehlszeilenschnittstelle](tutorial-custom-images.md).

Ein benutzerdefinierter Datenträger kann auf zwei Arten erstellt werden:
* Hochladen einer VHD-Datei
* Kopieren einer vorhandenen Azure-VM


## <a name="requirements"></a>Requirements (Anforderungen)
Für die folgenden Schritte benötigen Sie Folgendes:

- Einen virtuellen Linux-Computer, der für die Verwendung in Azure vorbereitet wurde. Im Abschnitt [Vorbereiten des virtuellen Computers](#prepare-the-vm) dieses Artikels erfahren Sie, wo Sie distributionsspezifische Informationen zur Installation des Azure Linux-Agents (waagent) finden. Dieser ist erforderlich, um eine SSH-Verbindung mit einem virtuellen Computer herstellen zu können.
- Die VHD-Datei einer vorhandenen [ von Azure unterstützten Linux-Distribution](endorsed-distros.md) (oder [Informationen zu nicht unterstützten Distributionen](create-upload-generic.md)) an einen virtuellen Datenträger im VHD-Format. Für die Erstellung virtueller Computer und Festplatten stehen verschiedene Tools zur Verfügung:
  - Installieren und konfigurieren Sie [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) oder [KVM](https://www.linux-kvm.org/page/RunningKVM), und verwenden Sie dabei „VHD“ als Imageformat. Bei Bedarf können Sie mit `qemu-img convert`[ein Image konvertieren](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats).
  - Unter [Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) und [Windows Server 2012/2012 R2](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)) können Sie auch Hyper-V verwenden.

> [!NOTE]
> Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Wenn Sie einen virtuellen Computer erstellen, geben Sie als Format VHD an. VHDX-Datenträger können bei Bedarf mit [qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) oder mit dem PowerShell-Cmdlet [Convert-VHD](/powershell/module/hyper-v/convert-vhd) in eine VHD konvertiert werden. Das Hochladen dynamischer VHDs wird von Azure nicht unterstützt. Daher müssen solche Datenträger vor dem Hochladen in statische VHDs konvertiert werden. Mit Tools wie den [Azure VHD-Hilfsprogrammen für Go](https://github.com/Microsoft/azure-vhd-utils-for-go) können Sie dynamische Datenträger beim Hochladen in Azure konvertieren.
> 
> 


- Vergewissern Sie sich, dass Sie die neueste Version der [Azure-Befehlszeilenschnittstelle](/cli/azure/install-az-cli2) installiert und sich mithilfe von [az login](/cli/azure/reference-index#az_login) bei einem Azure-Konto angemeldet haben.

Ersetzen Sie in den folgenden Beispielen die exemplarischen Parameternamen durch Ihre eigenen Werte (beispielsweise `myResourceGroup`, `mystorageaccount` und `mydisks`).

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Vorbereiten des virtuellen Computers

Azure unterstützt eine Vielzahl von Linux-Distributionen (siehe [Unterstützte Distributionen](endorsed-distros.md)). In den folgenden Artikeln wird die Vorbereitung der verschiedenen Linux-Distributionen beschrieben, die in Azure unterstützt werden:

* [CentOS-basierte Verteilungen](create-upload-centos.md)
* [Debian Linux](debian-create-upload-vhd.md)
* [Oracle Linux](oracle-create-upload-vhd.md)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
* [SLES &amp; openSUSE](suse-create-upload-vhd.md)
* [Ubuntu](create-upload-ubuntu.md)
* [Sonstige: nicht unterstützte Distributionen](create-upload-generic.md)

Beachten Sie auch die [Installationshinweise für Linux](create-upload-generic.md#general-linux-installation-notes). Diese enthalten allgemeine Tipps zur Vorbereitung von Linux-Images für Azure.

> [!NOTE]
> Die [Azure Platform-SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) gilt nur dann für virtuelle Computer unter Linux, wenn eine der unterstützten Distributionen mit Konfigurationsdetails verwendet wird, die im Abschnitt mit den unterstützten Versionen unter [Linux auf von Azure unterstützten Verteilungen](endorsed-distros.md) angegeben sind.
> 
> 

## <a name="option-1-upload-a-vhd"></a>Option 1: Hochladen einer VHD-Datei

Sie können nun eine VHD direkt in einen verwalteten Datenträger hochladen. Eine entsprechende Anleitung finden Sie unter [Hochladen einer VHD in Azure mithilfe der Azure CLI](disks-upload-vhd-to-managed-disk-cli.md).

## <a name="option-2-copy-an-existing-vm"></a>Option 2: Kopieren einer vorhandenen VM

Um eine weitere Kopie zu erstellen, können Sie auch einen benutzerdefinierten virtuellen Computer in Azure erstellen und dann den Betriebssystemdatenträger kopieren und an einen neuen virtuellen Computer anfügen. Dies ist für Testzwecke ausreichend. Wenn Sie allerdings einen vorhandenen virtuellen Azure-Computer als Modell für mehrere neue virtuelle Computer verwenden möchten, sollten Sie stattdessen ein *Image* erstellen. Weitere Informationen zum Erstellen eines Images eines vorhandenen virtuellen Azure-Computers finden Sie unter [Erstellen eines benutzerdefinierten Images eines virtuellen Azure-Computers mithilfe der Befehlszeilenschnittstelle](tutorial-custom-images.md).

Wenn Sie einen vorhandenen virtuellen Computer in eine andere Region kopieren möchten, können Sie unter Verwendung von AzCopy [eine Kopie eines Datenträgers in einer anderen Region erstellen](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk). 

Erstellen Sie andernfalls eine Momentaufnahme des virtuellen Computers und anschließend auf deren Grundlage eine neue Betriebssystem-VHD.

### <a name="create-a-snapshot"></a>Erstellen einer Momentaufnahme

Mit diesem Beispiel wird eine Momentaufnahme von einer VM namens *myVM* in der Ressourcengruppe *myResourceGroup* erstellt. Der Name der erstellten Momentaufnahme lautet *osDiskSnapshot*.

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Erstellen des verwalteten Datenträgers

Erstellen eines neuen verwalteten Datenträgers aus der Momentaufnahme.

Ermitteln Sie die ID der Momentaufnahme. In unserem Beispiel heißt die Momentaufnahme *osDiskSnapshot* und befindet sich in der Ressourcengruppe *myResourceGroup*.

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Erstellen Sie den verwalteten Datenträger. In diesem Beispiel erstellen wir einen verwalteten Datenträger namens *myManagedDisk* auf der Grundlage unserer Momentaufnahme. Der Datenträger befindet sich im Standardspeicher und hat eine Größe von 128 GB.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Erstellen Sie Ihren virtuellen Computer mit [az vm create](/cli/azure/vm#az_vm_create), und fügen Sie den verwalteten Datenträger als Betriebssystemdatenträger an (--attach-os-disk). Das folgende Beispiel erstellt den virtuellen Computer *myNewVM* unter Verwendung des verwalteten Datenträgers, der auf der Grundlage der hochgeladenen VHD erstellt wurde:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Mit den Anmeldeinformationen des virtuellen Quellcomputers sollten Sie eine SSH-Verbindung mit dem virtuellen Computer herstellen können. 

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie den benutzerdefinierten virtuellen Datenträger vorbereitet und hochgeladen haben, können Sie sich mit der [Verwendung von Resource Manager und Vorlagen](../../azure-resource-manager/management/overview.md)beschäftigen. Informationen zum Hinzufügen eines Datenträgers zu Ihren neuen virtuellen Computern finden Sie [hier](add-disk.md) . Falls auf Ihren virtuellen Computern Anwendungen ausgeführt werden, auf die Sie zugreifen müssen, müssen Sie [Ports und Endpunkte öffnen](nsg-quickstart.md).
