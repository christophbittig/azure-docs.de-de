---
title: 'Tutorial: Verwalten von Azure-Datenträgern mit Azure PowerShell'
description: In diesem Tutorial erfahren Sie, wie Sie Azure PowerShell zum Erstellen und Verwalten von Azure-Datenträgern für VMs verwenden.
author: roygara
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 4432750e768a8da504145d01dc94472173b03ad6
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688635"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>Tutorial: Verwalten von Azure-Datenträgern mit Azure PowerShell
**Gilt für**: :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Virtuelle Azure-Computer verwenden Datenträger zum Speichern des Betriebssystems, der Anwendungen und der Daten der virtuellen Computer. Beim Erstellen eines virtuellen Computers ist es wichtig, eine für die erwartete Workload geeignete Datenträgergröße und -konfiguration auszuwählen. Dieses Tutorial behandelt die Bereitstellung und Verwaltung der Datenträger von virtuellen Computern. Sie erhalten Informationen zu folgenden Themen:

> [!div class="checklist"]
> * Betriebssystemdatenträger und temporäre Datenträger
> * Datenträger
> * Standard- und Premium-Datenträger
> * Datenträgerleistung
> * Anfügen und Vorbereiten von Datenträgern für Daten

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/powershell](https://shell.azure.com/powershell) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.

## <a name="default-azure-disks"></a>Azure-Standarddatenträger

Beim Erstellen eines virtuellen Azure-Computers werden zwei Datenträger automatisch an den virtuellen Computer angefügt. 

**Betriebssystem-Datenträger**: Betriebssystem-Datenträger können mit einer Größe von bis zu 4 TB konfiguriert werden und hosten das Betriebssystem des virtuellen Computers. Wenn Sie einen neuen virtuellen Computer auf der Grundlage eines [Azure Marketplace](https://azure.microsoft.com/marketplace/)-Images erstellen, hat der Betriebssystemdatenträger in der Regel eine Größe von 127 GB. (Bei manchen Images ist der Betriebssystemdatenträger allerdings kleiner.) Dem Betriebssystem-Datenträger wird standardmäßig der Laufwerkbuchstabe *C:* zugewiesen. Die Konfiguration der Datenträgerzwischenspeicherung des Betriebssystem-Datenträgers ist für die Leistung des Betriebssystems optimiert. Der Betriebssystem-Datenträger **sollte keine** Anwendungen oder Daten hosten. Verwenden Sie für Anwendungen und Daten einen Datenträger. Dies wird weiter unten in diesem Artikel ausführlich erläutert.

**Temporärer Datenträger**: Temporäre Datenträger verwenden ein Solid State Drive, das sich auf dem gleichen Azure-Host wie der virtuelle Computer befindet. Temporäre Datenträger sind äußerst leistungsfähig und können für Vorgänge wie die temporäre Datenverarbeitung verwendet werden. Wenn der virtuelle Computer jedoch auf einen neuen Host verschoben wird, werden alle auf einem temporären Datenträger gespeicherten Daten entfernt. Die Größe des temporären Datenträgers richtet sich nach der [Größe des virtuellen Computers](../sizes.md). Temporären Datenträgern wird standardmäßig der Laufwerkbuchstabe *D:* zugewiesen.

## <a name="azure-data-disks"></a>Azure-Datenträger

Zum Installieren von Anwendungen und zum Speichern von Daten können weitere Datenträger hinzugefügt werden. Datenträger sollten in allen Fällen verwendet werden, in denen eine dauerhafte und dynamische Datenspeicherung erforderlich ist. Die Größe eines virtuellen Computers bestimmt die Anzahl der Datenträger, die an den virtuellen Computer angefügt werden können.

## <a name="vm-disk-types"></a>VM-Datenträgertypen

In Azure stehen zwei Arten von Datenträgern zur Verfügung.

**Standarddatenträger:** Basieren auf Festplatten und stellen eine kostengünstige, leistungsstarke Speicherlösung dar. Standarddatenträger sind ideal für eine kostengünstige Entwicklungs- und Testworkload.

**Premium-Datenträger:** Zeichnen sich durch SSD-basierte hohe Leistung und geringe Wartezeit aus. Sie eignen sich hervorragend für virtuelle Computer, auf denen die Produktionsworkload ausgeführt wird. VM-Größen mit dem Buchstaben **S** im [Namen der Größe](../vm-naming-conventions.md) unterstützen in der Regel Storage Premium. Virtuelle Computer der DS-, DSv2-, GS- und Fs-Serie etwa unterstützen Storage Premium. Bei Auswahl einer Datenträgergröße wird der Wert auf den nächsten Datenträgertyp aufgerundet. Liegt die Größe des Datenträgers beispielsweise über 64 GB, aber unter 128 GB, ist der Datenträgertyp P10. 
<br>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Im Gegensatz zu einem Standard-Speicherdatenträger sind bei der Bereitstellung eines Storage Premium-Datenträgers die Kapazität, die IOPS und der Durchsatz dieses Datenträgers garantiert. Wenn Sie beispielsweise einen P50-Datenträger erstellen, stellt Azure eine Speicherkapazität von 4.095 GB, 7.500 IOPS und einen Durchsatz von 250 MB/s für diesen Datenträger bereit. Die Anwendung kann die Kapazität und Leistung ganz oder teilweise nutzen. SSD Premium-Datenträger sind dafür ausgelegt, Wartezeiten im niedrigen einstelligen Millisekundenbereich sowie die in der vorherigen Tabelle beschriebenen IOPS und Durchsätze 99,9 % der Zeit bereitzustellen.

In dieser Tabelle ist zwar die maximale IOPS-Anzahl pro Datenträger angegeben, eine höhere Leistung kann aber durch Striping mehrerer Datenträger erreicht werden. An einen virtuellen Standard_GS5-Computer können beispielsweise 64 Datenträger angefügt werden. Wenn jeder dieser Datenträger die Größe P30 hat, kann eine maximale Größe von 80.000 IOPS erreicht werden. Ausführliche Informationen zur maximalen IOPS-Anzahl pro VM finden Sie unter [Größen für virtuelle Windows-Computer in Azure](../sizes.md).

## <a name="create-and-attach-disks"></a>Erstellen und Anfügen von Datenträgern

Für das Beispiel in diesem Tutorial muss ein virtueller Computer vorhanden sein. Erstellen Sie, falls erforderlich, mit den folgenden Befehlen einen virtuellen Computer.

Legen Sie mit [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) den Benutzernamen und das Kennwort für das Administratorkonto auf dem virtuellen Computer fest:


Erstellen Sie mit [New-AzVM](/powershell/module/az.compute/new-azvm) den virtuellen Computer. Sie werden aufgefordert, einen Benutzernamen und ein Kennwort für das Administratorkonto des virtuellen Computers einzugeben.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


Erstellen Sie mit [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) die Erstkonfiguration. Im folgenden Beispiel wird ein Datenträger mit einer Größe von 128 GB erstellt.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Erstellen Sie den Datenträger mit dem Befehl [New-AzDisk](/powershell/module/az.compute/new-azdisk).

```azurepowershell-interactive
$dataDisk = New-AzDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Rufen Sie mit dem Befehl [Get-AzVM](/powershell/module/az.compute/get-azvm) die VM ab, der Sie den Datenträger hinzufügen möchten.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Fügen Sie den Datenträger mit dem Befehl [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk) zur VM-Konfiguration hinzu.

```azurepowershell-interactive
$vm = Add-AzVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Aktualisieren Sie die VM mit dem Befehl [Update-AzVM](/powershell/module/az.compute/add-azvmdatadisk).

```azurepowershell-interactive
Update-AzVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Vorbereiten von Datenträgern

Nach dem Anfügen eines Datenträgers an den virtuellen Computer muss das Betriebssystem zur Verwendung des Datenträgers konfiguriert werden. Im folgenden Beispiel wird gezeigt, wie der erste dem virtuellen Computer hinzugefügte Datenträger manuell konfiguriert wird. Dieser Vorgang kann auch mit der [benutzerdefinierten Skripterweiterung](./tutorial-automate-vm-deployment.md) automatisiert werden.

### <a name="manual-configuration"></a>Manuelle Konfiguration

Erstellen Sie eine RDP-Verbindung mit dem virtuellen Computer. Öffnen Sie PowerShell, und führen Sie das folgende Skript aus.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' |
    Initialize-Disk -PartitionStyle MBR -PassThru |
    New-Partition -AssignDriveLetter -UseMaximumSize |
    Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="verify-the-data-disk"></a>Überprüfen des Datenträgers

Sehen Sie sich das Speicherprofil (`StorageProfile`) für die angefügten Datenträger (`DataDisks`) an, um sich zu vergewissern, dass der Datenträger angefügt wurde.

```azurepowershell-interactive
$vm.StorageProfile.DataDisks
```

Die Ausgabe sollte in etwa wie das folgende Beispiel aussehen:

```
Name            : myDataDisk
DiskSizeGB      : 128
Lun             : 1
Caching         : None
CreateOption    : Attach
SourceImage     :
VirtualHardDisk :
```


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu VM-Datenträgern erhalten, darunter die folgenden:

> [!div class="checklist"]
> * Betriebssystemdatenträger und temporäre Datenträger
> * Datenträger
> * Standard- und Premium-Datenträger
> * Datenträgerleistung
> * Anfügen und Vorbereiten von Datenträgern für Daten

Im nächsten Tutorial erfahren Sie, wie die VM-Konfiguration automatisiert werden kann.

> [!div class="nextstepaction"]
> [Automatisieren der VM-Konfiguration](./tutorial-automate-vm-deployment.md)
