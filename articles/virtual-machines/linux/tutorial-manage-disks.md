---
title: 'Tutorial: Verwalten von Azure-Datenträgern mit der Azure-CLI'
description: In diesem Tutorial erfahren Sie, wie Sie die Azure CLI zum Erstellen und Verwalten von Azure-Datenträgern für virtuelle Computer verwenden.
author: roygara
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 08/20/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 37f5e0163d7c2ed57866f8c4eda49458a969ad87
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687760"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Tutorial: Verwalten von Azure-Datenträgern mit der Azure-CLI

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Virtuelle Azure-Computer verwenden Datenträger zum Speichern des Betriebssystems, der Anwendungen und der Daten. Beim Erstellen eines virtuellen Computers muss darauf geachtet werden, eine für die erwartete Workload geeignete Datenträgergröße und -konfiguration auszuwählen. In diesem Tutorial wird gezeigt, wie Sie VM-Datenträger bereitstellen und verwalten. Sie erhalten Informationen zu folgenden Themen:

> [!div class="checklist"]
> * Betriebssystemdatenträger und temporäre Datenträger
> * Datenträger
> * Standard- und Premium-Datenträger
> * Datenträgerleistung
> * Anfügen und Vorbereiten von Datenträgern für Daten
> * Momentaufnahmen von Datenträgern


## <a name="default-azure-disks"></a>Azure-Standarddatenträger

Beim Erstellen eines virtuellen Azure-Computers werden zwei Datenträger automatisch an den virtuellen Computer angefügt.

**Betriebssystem-Datenträger**: Betriebssystem-Datenträger können in der Größe auf bis zu 2 TB angepasst werden. Sie hosten das Betriebssystem des virtuellen Computers. Der Betriebssystem-Datenträger wird standardmäßig mit */dev/sda* bezeichnet. Die Konfiguration der Datenträgerzwischenspeicherung des Betriebssystem-Datenträgers ist für die Leistung des Betriebssystems optimiert. Aufgrund dieser Konfiguration sollte der Betriebssystem-Datenträger **nicht** für Anwendungen oder Daten verwendet werden. Verwenden Sie für Anwendungen und Daten einen Datenträger. Dies wird weiter unten in diesem Tutorial ausführlich erläutert.

**Temporärer Datenträger**: Temporäre Datenträger verwenden ein Solid State Drive, das sich auf dem gleichen Azure-Host wie der virtuelle Computer befindet. Temporäre Datenträger sind äußerst leistungsfähig und können für Vorgänge wie die temporäre Datenverarbeitung verwendet werden. Wenn der virtuelle Computer jedoch auf einen neuen Host verschoben wird, werden alle auf einem temporären Datenträger gespeicherten Daten entfernt. Die Größe des temporären Datenträgers richtet sich nach der Größe des virtuellen Computers. Temporäre Datenträger werden mit bezeichnet */dev/sdb* und haben den Bereitstellungspunkt */mnt*.

## <a name="azure-data-disks"></a>Azure-Datenträger

Zusätzliche Datenträger können hinzugefügt werden, wenn Sie Anwendungen installieren und Daten speichern möchten. Datenträger sollten in allen Fällen verwendet werden, in denen eine dauerhafte und dynamische Datenspeicherung erwünscht ist. Die Größe eines virtuellen Computers bestimmt die Anzahl der Datenträger, die an den virtuellen Computer angefügt werden können.

## <a name="vm-disk-types"></a>VM-Datenträgertypen

In Azure stehen zwei Arten von Datenträgern zur Verfügung.

**Standarddatenträger:** Basieren auf Festplatten und stellen eine kostengünstige, leistungsstarke Speicherlösung dar. Standarddatenträger sind ideal für eine kostengünstige Entwicklungs- und Testworkload.

**Premium-Datenträger:** Zeichnen sich durch SSD-basierte hohe Leistung und geringe Wartezeit aus. Sie eignen sich hervorragend für virtuelle Computer, auf denen die Produktionsworkload ausgeführt wird. VM-Größen mit dem Buchstaben **S** im [Namen der Größe](../vm-naming-conventions.md) unterstützen in der Regel Storage Premium. Virtuelle Computer der DS-, DSv2-, GS- und Fs-Serie etwa unterstützen Storage Premium. Bei Auswahl einer Datenträgergröße wird der Wert auf den nächsten Datenträgertyp aufgerundet. Liegt die Größe des Datenträgers beispielsweise über 64 GB, aber unter 128 GB, ist der Datenträgertyp P10. 

<br>


[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Im Gegensatz zu einem Standard-Speicherdatenträger sind bei der Bereitstellung eines Storage Premium-Datenträgers die Kapazität, die IOPS und der Durchsatz dieses Datenträgers garantiert. Wenn Sie beispielsweise einen P50-Datenträger erstellen, stellt Azure eine Speicherkapazität von 4.095 GB, 7.500 IOPS und einen Durchsatz von 250 MB/s für diesen Datenträger bereit. Die Anwendung kann die Kapazität und Leistung ganz oder teilweise nutzen. SSD Premium-Datenträger sind dafür ausgelegt, Wartezeiten im niedrigen einstelligen Millisekundenbereich sowie die in der vorherigen Tabelle beschriebenen IOPS und Durchsätze 99,9 % der Zeit bereitzustellen.

In dieser Tabelle ist zwar die maximale IOPS-Anzahl pro Datenträger angegeben, eine höhere Leistung kann aber durch Striping mehrerer Datenträger erreicht werden. An einen virtuellen Standard_GS5-Computer können beispielsweise 64 Datenträger angefügt werden. Wenn jeder dieser Datenträger die Größe P30 hat, kann eine maximale Größe von 80.000 IOPS erreicht werden. Ausführliche Informationen zur maximalen IOPS-Anzahl pro VM finden Sie unter [Größen für virtuelle Windows-Computer in Azure](../sizes.md).

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert.

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock die Option **Ausprobieren** aus. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/powershell](https://shell.azure.com/bash) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.

## <a name="create-and-attach-disks"></a>Erstellen und Anfügen von Datenträgern

Datenträger für Daten können zum Zeitpunkt der VM-Erstellung erstellt und angefügt oder später erstellt und einer vorhandenen VM angefügt werden.

### <a name="attach-disk-at-vm-creation"></a>Anfügen eines Datenträgers bei der VM-Erstellung

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe.

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

Erstellen Sie mit dem Befehl [az vm create](/cli/azure/vm#az_vm_create) einen virtuellen Computer. Das folgende Beispiel erstellt einen virtuellen Computer namens *myVM*, fügt ein Benutzerkonto mit dem Namen *azureuser* hinzu und generiert SSH-Schlüssel, sofern noch nicht vorhanden. Das `--datadisk-sizes-gb`-Argument gibt an, dass ein weiterer Datenträger erstellt und dem virtuellen Computer angefügt werden sollte. Verwenden Sie zum Erstellen und Anfügen mehrerer Datenträger eine durch Leerzeichen getrennte Liste der Datenträger-Größenwerte. Im folgenden Beispiel wird ein virtueller Computer mit zwei Datenträgern von jeweils 128GB erstellt. Da die Größe der Datenträger jeweils 128GB beträgt, werden beide Datenträger als P10 konfiguriert, was maximal 500IOPS pro Datenträger bereitstellt.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>Anfügen eines Datenträgers an einen vorhandenen virtuellen Computer

Verwenden Sie zum Erstellen eines neuen Datenträgers und dessen Anfügen an einen vorhandenen virtuellen Computer den [az vm disk attach](/cli/azure/vm/disk#az_vm_disk_attach)-Befehl. Im folgenden Beispiel wird ein Premium-Datenträger von 128GB erstellt und dem im letzten Schritt erstellten virtuellen Computer angefügt.

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --name myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Vorbereiten von Datenträgern

Nach dem Anfügen eines Datenträgers an den virtuellen Computer muss das Betriebssystem zur Verwendung des Datenträgers konfiguriert werden. Das folgende Beispiel zeigt das manuelle Konfigurieren eines Datenträgers. Dieser Prozess kann auch mit cloud-init automatisiert werden, was in einem [späteren Tutorial](./tutorial-automate-vm-deployment.md) veranschaulicht wird.


Stellen Sie eine SSH-Verbindung mit dem virtuellen Computer her. Ersetzen Sie die IP-Beispieladresse durch die öffentliche IP-Adresse des virtuellen Computers.

```console
ssh azureuser@10.101.10.10
```

Partitionieren Sie den Datenträger mit `parted`.

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
```

Schreiben Sie mit dem Befehl `mkfs` ein Dateisystem auf die Partition: Verwenden Sie `partprobe`, um das Betriebssystem über die Änderung zu informieren.

```bash
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Binden Sie den neuen Datenträger ein, damit im Betriebssystem darauf zugegriffen werden kann.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

Auf den Datenträger kann jetzt über den Bereitstellungspunkt `/datadrive` zugegriffen werden. Dies kann durch Ausführung des Befehls `df -h` überprüft werden.

```bash
df -h | grep -i "sd"
```

Die Ausgabe zeigt das neue, auf `/datadrive` bereitgestellte Laufwerk.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        29G  2.0G   27G   7% /
/dev/sda15      105M  3.6M  101M   4% /boot/efi
/dev/sdb1        14G   41M   13G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Um sicherzustellen, dass das Laufwerk nach einem Neustart automatisch wieder eingebunden wird, muss es der Datei */etc/fstab* hinzugefügt werden. Rufen Sie hierzu die UUID des Datenträgers mit dem `blkid`-Hilfsprogramm ab.

```bash
sudo -i blkid
```

Die Ausgabe zeigt die UUID des Laufwerks an, in diesem Fall `/dev/sdc1`.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs"
```

> [!NOTE]
> Eine falsche Bearbeitung der Datei **/etc/fstab** könnte zu einem nicht startfähigen System führen. Wenn Sie sich nicht sicher sind, helfen Ihnen die Informationen zur richtigen Bearbeitung dieser Datei in der Dokumentation weiter. Außerdem wird empfohlen, ein Backup der Datei /etc/fstab zu erstellen, bevor Sie sie bearbeiten.

Öffnen Sie die Datei `/etc/fstab` wie folgt in einem Text-Editor:

```bash
sudo nano /etc/fstab
```

Fügen Sie der Datei */etc/fstab* eine Zeile ähnlich der folgenden hinzu. Ersetzen Sie dabei den UUID-Wert durch Ihren eigenen Wert.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  xfs    defaults,nofail   1  2
```

Wenn die Bearbeitung der Datei abgeschlossen ist, verwenden Sie `Ctrl+O`, um die Datei zu schreiben, und `Ctrl+X`, um den Editor zu beenden.

Jetzt ist der Datenträger konfiguriert, und Sie können die SSH-Sitzung schließen.

```bash
exit
```

## <a name="take-a-disk-snapshot"></a>Erstellen einer Datenträgermomentaufnahme

Bei einer Momentaufnahme des Datenträger erstellt Azure eine schreibgeschützte Point-in-Time-Kopie des Datenträgers. Mit Azure-VM-Momentaufnahmen können Sie schnell den Status eines virtuellen Computers speichern, bevor Sie Änderungen an der Konfiguration vornehmen. Bei einem Problem oder Fehler kann der virtuelle Computer mithilfe einer Momentaufnahme wiederhergestellt werden. Wenn ein virtueller Computer über mehrere Datenträger verfügt, wird von jedem einzelnen Datenträger eine separate Momentaufnahme erstellt. Um anwendungskonsistente Sicherungen zu erstellen, erwägen Sie, den virtuellen Computer vor dem Erstellen von Momentaufnahmen des Datenträgers zu beenden. Verwenden Sie alternativ den [Azure Backup-Dienst](../../backup/index.yml), mit dem Sie automatisierte Sicherungen ausführen können, während die VM ausgeführt wird.

### <a name="create-snapshot"></a>Erstellen einer Momentaufnahme

Damit Sie eine Momentaufnahme erstellen können, benötigen Sie die ID oder den Namen des Datenträgers. Zeigen Sie die Datenträger-ID mithilfe von [az vm show](/cli/azure/vm#az_vm_show) an. In diesem Beispiel wird die Datenträger-ID in einer Variablen gespeichert und kann in einem späteren Schritt verwendet werden.

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Sie haben nun die ID und können mithilfe von [az snapshot create](/cli/azure/snapshot#az_snapshot_create) eine Momentaufnahme des Datenträgers erstellen.

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Erstellen eines Datenträgers aus der Momentaufnahme

Diese Momentaufnahme kann dann mithilfe von [az disk create](/cli/azure/disk#az_disk_create) in einen Datenträger konvertiert werden, mit dem wiederum der virtuelle Computer neu erstellt werden kann.

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Wiederherstellen des virtuellen Computers aus der Momentaufnahme

Löschen Sie mithilfe von [az vm delete](/cli/azure/vm#az_vm_delete) den vorhandenen virtuellen Computer, um die Wiederherstellung des virtuellen Computers durchzuführen.

```azurecli-interactive
az vm delete \
--resource-group myResourceGroupDisk \
--name myVM
```

Erstellen Sie einen neuen virtuellen Computer aus dem Momentaufnahmendatenträger.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>Erneutes Anfügen des Datenträgers

Alle Datenträger müssen dem virtuellen Computer erneut angefügt werden.

Ermitteln Sie mithilfe des Befehls [az disk list](/cli/azure/disk#az_disk_list) den Namen des Datenträgers. In diesem Beispiel wird der Name des Datenträgers in eine Variable namens `datadisk` eingefügt, die im nächsten Schritt verwendet wird.

```azurecli-interactive
datadisk=$(az disk list \
   -g myResourceGroupDisk \
   --query "[?contains(name,'myVM')].[id]" \
   -o tsv)
```

Verwenden Sie den Befehl [az vm disk attach](/cli/azure/vm/disk#az_vm_disk_attach), um den Datenträger anzufügen.

```azurecli-interactive
az vm disk attach \
   –g myResourceGroupDisk \
   --vm-name myVM \
   --name $datadisk
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu VM-Datenträgern erhalten, darunter die folgenden:

> [!div class="checklist"]
> * Betriebssystemdatenträger und temporäre Datenträger
> * Datenträger
> * Standard- und Premium-Datenträger
> * Datenträgerleistung
> * Anfügen und Vorbereiten von Datenträgern für Daten
> * Momentaufnahmen von Datenträgern

Im nächsten Tutorial erfahren Sie, wie die VM-Konfiguration automatisiert werden kann.

> [!div class="nextstepaction"]
> [Automatisieren der VM-Konfiguration](./tutorial-automate-vm-deployment.md)
