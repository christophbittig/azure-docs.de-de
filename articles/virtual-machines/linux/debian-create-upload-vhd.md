---
title: Vorbereiten einer Debian Linux-VHD
description: Hier erfahren Sie, wie Sie Debian-VHD-Images für VM-Bereitstellungen in Azure erstellen.
author: srijang
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/10/2021
ms.author: srijangupta
ms.openlocfilehash: e3d68e704ce91cddfabeb8518a60bba469b7adf7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301196"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Vorbereiten einer Debian-VHD für Azure

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

## <a name="prerequisites"></a>Voraussetzungen
In diesem Abschnitt wird davon ausgegangen, dass Sie bereits ein Debian Linux-Betriebssystem aus einer ISO-Datei von der [Debian-Website](https://www.debian.org/distrib/) auf einer virtuelle Festplatte installiert haben. Es gibt mehrere Tools zum Erstellen von VHD-Dateien. Hyper-V ist nur ein Beispiel. Anweisungen zur Verwendung von Hyper-V finden Sie unter [Installieren von Hyper-V und Erstellen eines virtuellen Computers](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="installation-notes"></a>Installationshinweise
* Beachten Sie auch den Artikelabschnitt [Allgemeine Linux-Systemanforderungen](create-upload-generic.md#general-linux-installation-notes) mit weiteren Tipps zur Vorbereitung von Linux für Azure.
* Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Sie können den Datenträger mit dem Hyper-V-Manager oder dem **Convert-VHD-Cmdlet** in das VHD-Format konvertieren.
* Beim Installieren des Linux-Systems empfiehlt es sich, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) oder [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) können wahlweise auf Datenträgern verwendet werden.
* Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Der Azure-Linux-Agent kann konfiguriert werden, eine Auslagerungsdatei auf dem temporären Ressourcendatenträger zu erstellen. Weitere Informationen finden Sie in den folgenden Schritten.
* Alle VHDs in Azure benötigen eine virtuelle Größe, die auf 1 MB ausgerichtet ist. Wenn Sie einen unformatierten Datenträger in VHD konvertieren, müssen Sie sicherstellen, dass die Größe des unformatierten Datenträgers vor der Konvertierung ein Vielfaches von 1 MB ist. Weitere Informationen finden Sie in den [Linux-Installationshinweisen](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Verwenden von azure-manage zum Erstellen von Debian-VHDs
Zum Generieren von Debian-VHDs für Azure sind Tools verfügbar (beispielsweise die [azure-manage](https://github.com/credativ/azure-manage)-Skripts von [Credativ](https://www.credativ.com/)). Dies ist der empfohlene Ansatz, mit dem vermieden werden kann, ein Image von Grund auf neu zu erstellen. Wenn Sie beispielsweise eine Debian 8-VHD erstellen möchten, führen Sie die folgenden Befehle aus, um das Hilfsprogramm `azure-manage` (einschließlich Abhängigkeiten) herunterzuladen und das Skript `azure_build_image` auszuführen:

```console
# sudo apt-get update
# sudo apt-get install git qemu-utils mbr kpartx debootstrap

# sudo apt-get install python3-pip python3-dateutil python3-cryptography
# sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
# git clone https://github.com/credativ/azure-manage.git
# cd azure-manage
# sudo pip3 install .

# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section
```


## <a name="prepare-a-debian-image-for-azure"></a>Vorbereiten eines Debian-Images für Azure

Sie können das Azure Debian Cloud-Basisimage mit dem [FAI-Cloudimage-Generator](https://salsa.debian.org/cloud-team/debian-cloud-images) erstellen.

(Die folgenden Git Clone- und apt-Installationsbefehle wurden aus dem Debian Cloud Images-Repository abgerufen.) Klonen Sie zunächst das Repository, und installieren Sie Abhängigkeiten:

```
$ git clone https://salsa.debian.org/cloud-team/debian-cloud-images.git
$ sudo apt install --no-install-recommends ca-certificates debsums dosfstools \
    fai-server fai-setup-storage make python3 python3-libcloud python3-marshmallow \
    python3-pytest python3-yaml qemu-utils udev
$ cd ./debian-cloud-images
```

(Optional) Passen Sie den Build an, indem Sie `./config_space/scripts/AZURE` Skripts (z. B. Shellskripts) hinzufügen.



## <a name="an-example-of-a-script-to-customize-the-image-is"></a>Beispiel für ein Skript zum Anpassen des Images:

```
$ mkdir -p ./config_space/scripts/AZURE
$ cat > ./config_space/scripts/AZURE/10-custom <<EOF
#!/bin/bash

\$ROOTCMD bash -c "echo test > /usr/local/share/testing"
EOF
$ sudo chmod 755 ./config_space/scripts/AZURE/10-custom
```

Beachten Sie, dass es wichtig ist, allen Befehlen, mit denen Sie das Image anpassen möchten, das Präfix `$ROOTCMD` voranzustellen, da dies als Alias `chroot $target` verwendet wird.


## <a name="build-the-azure-debian-10-image"></a>Erstellen des Azure Debian 10-Images:

```
$ make image_buster_azure_amd64
```


Dadurch werden einige Dateien im aktuellen Verzeichnis ausgegeben, insbesondere die Bilddatei `image_buster_azure_amd64.raw`.

Gehen Sie wie folgt vor, um das unformatierte Image in eine VHD für Azure zu konvertieren:

```
rawdisk="image_buster_azure_amd64.raw"
vhddisk="image_buster_azure_amd64.vhd"

MB=$((1024*1024))
size=$(qemu-img info -f raw --output json "$rawdisk" | \
gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

rounded_size=$(((($size+$MB-1)/$MB)*$MB))
rounded_size_adjusted=$(($rounded_size + 512))

echo "Rounded Size Adjusted = $rounded_size_adjusted"

sudo qemu-img resize "$rawdisk" $rounded_size
qemu-img convert -f raw -o subformat=fixed,force_size -O vpc "$rawdisk" "$vhddisk"
```


Dadurch wird eine VHD `image_buster_azure_amd64.vhd` mit gerundeter Größe erstellt, um sie erfolgreich auf einen Azure-Datenträger kopieren zu können.

Nun müssen die Azure-Ressourcen für dieses Image erstellt werden (dabei wird die Variable `$rounded_size_adjusted` verwendet; der Vorgang sollte also über denselben Shellprozess wie oben erfolgen).

```
az group create -l $LOCATION -n $RG

az disk create \
    -n $DISK \
    -g $RG \
    -l $LOCATION \
    --for-upload --upload-size-bytes "$rounded_size_adjusted" \
    --sku standard_lrs --hyper-v-generation V1

ACCESS=$(az disk grant-access \
    -n $DISK -g $RG \
    --access-level write \
    --duration-in-seconds 86400 \
    --query accessSas -o tsv)

azcopy copy "$vhddisk" "$ACCESS" --blob-type PageBlob

az disk revoke-access -n $DISK -g $RG
az image create \
    -g $RG \
    -n $IMAGE \
    --os-type linux \
    --source $(az disk show \
        -g $RG \
        -n $DISK \
        --query id -o tsv)
az vm create \
    -g $RG \
    -n $VM \
    --ssh-key-value $SSH_KEY_VALUE \
    --public-ip-address-dns-name $VM \
    --image $(az image show \
        -g $RG \
        -n $IMAGE \
        --query id -o tsv)
```


>[!Note]
> Wenn die Bandbreite von Ihrem lokalen Computer zum Azure-Datenträger zu einer langwierigen Verarbeitung des Uploads mit azcopy führt, können Sie eine Azure-VM-Jumpbox verwenden, um den Prozess zu beschleunigen. Hier wird gezeigt, wie es geht:
>
>1. Erstellen Sie einen Tarball der VHD auf Ihrem lokalen Computer: `tar -czvf ./image_buster_azure_amd64.vhd.tar.gz ./image_buster_azure_amd64.vhd`.
>2. Erstellen Sie eine Azure Linux-VM (Distribution Ihrer Wahl). Stellen Sie sicher, dass Sie sie mit einem ausreichend großen Datenträger für die extrahierte VHD erstellen!
>3. Laden Sie das Hilfsprogramm azcopy auf die Azure Linux-VM herunter. Es kann von [hier](../../storage/common/storage-use-azcopy-v10.md#download-azcopy) abgerufen werden.
>4. Kopieren Sie den Tarball auf die VM: `scp ./image_buster_azure_amd64.vhd.tar.gz <vm>:~`.
>5. Extrahieren Sie die VM auf der VHD: `tar -xf ./image_buster_azure_amd64.vhd.tar.gz` (dies dauert aufgrund der Größe der Datei etwas).
>6. Zum Schluss kopieren Sie die VHD auf der VM mit `azcopy` (oben stehender Befehl) auf den Azure-Datenträger.


**Nächste Schritte:** Sie können jetzt mit Ihrer virtuellen Debian-Linux-Festplatte neue virtuelle Computer in Azure erstellen. Wenn Sie zum ersten Mal die VHD-Datei in Azure hochladen, lesen Sie den Artikel [Erstellen eines virtuellen Linux-Computers aus einem benutzerdefinierten Datenträger mithilfe der Azure CLI 2.0](/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd).

