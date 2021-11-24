---
title: Kurzlebige Betriebssystemdatenträger
description: Erfahren Sie mehr über kurzlebige Betriebssystemdatenträger für virtuelle Azure-Computer.
author: Aarthi-Vijayaraghavan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: aarthiv
ms.subservice: disks
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 845bb5f4830c53b4fc4f6688851775afa908af73
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135296"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Kurzlebige Betriebssystemdatenträger für virtuelle Azure-Computer

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Kurzlebige Betriebssystem-Datenträger werden auf dem lokalen Speicher des virtuellen Computers (VM) erstellt und nicht auf dem externen Azure Storage gespeichert. Kurzlebige Betriebssystem-Datenträger eignen sich gut für zustandslose Workloads, bei denen Anwendungen einzelne VM-Ausfälle tolerieren, sind aber stärker durch den Zeitpunkt der VM-Bereitstellung oder das Reimaging der einzelnen VM-Instanzen betroffen. Mit einem kurzlebigen Betriebssystem-Datenträger erzielen Sie eine geringere Latenzzeit für das Lesen/Schreiben auf dem Betriebssystem-Datenträger und schnelleres VM-Reimaging. 
 
Die wichtigsten Features von kurzlebigen Datenträgern sind: 
- Ideal für zustandslose Anwendungen geeignet.
- Wird von Marketplace, benutzerdefinierten Images und von [Azure Compute Gallery](./shared-image-galleries.md) (früher als „Shared Image Gallery“ bezeichnet) unterstützt.
- Möglichkeit, VMs und Skalierungsgruppeninstanzen schnell zurückzusetzen oder in den ursprünglichen Startzustand zu versetzen.  
- Geringere Latenz ähnlich wie bei einem temporären Datenträger. 
- Kurzlebige Betriebssystem-Datenträger sind kostenlos, es fallen keine Speicherkosten für den Betriebssystem-Datenträger an.
- In allen Azure-Regionen verfügbar.  

 
Die Hauptunterschiede zwischen permanenten und kurzlebigen Betriebssystem-Datenträgern:

|   | Permanenter Betriebssystem-Datenträger | Kurzlebiger Betriebssystemdatenträger |
|---|---|---|
| **Maximale Größe für Betriebssystem-Datenträger** | 2 TiB | Cachegröße für die VM-Größe oder 2TiB, je nachdem, was kleiner ist. Informationen zur **Cachegröße in GiB** finden Sie unter [DS](sizes-general.md), [ES](sizes-memory.md), [M](sizes-memory.md), [FS](sizes-compute.md) und [GS](sizes-previous-gen.md#gs-series). |
| **Unterstützte VM-Größen** | All | VM-Größen, die Storage Premium unterstützen, etwa DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M, Mdsv2,Bs, Dav4, Eav4 |
| **Unterstützung für Datenträgertyp**| Verwalteter und nicht verwalteter Betriebssystem-Datenträger| Nur verwalteter Betriebssystem-Datenträger|
| **Unterstützung für Regionen**| Alle Regionen| Alle Regionen|
| **Datenpersistenz**| Auf Betriebssystem-Datenträger geschriebene Daten von einem Betriebssystem-Datenträger werden in Azure Storage gespeichert.| Auf den Betriebssystem-Datenträger geschriebene Daten werden auf dem lokalen VM-Speicher gespeichert und nicht in Azure Storage gespeichert. |
| **Status „Zuordnung aufgehoben“**| Die Zuordnung von VMs und Skalierungsgruppeninstanzen kann aufgehoben und sie können aus diesem Zustand neu gestartet werden. | Die Zuordnung für VMS und Skalierungsgruppeninstanzen kann nicht aufgehoben werden.|
| **Unterstützung der spezialisierte Betriebssystem-Datenträger** | Ja| Nein|
| **Änderung der Betriebssystem-Datenträgergröße**| Wird während der VM-Erstellung und nach dem Aufheben der VM-Zuordnung unterstützt.| Wird nur während der VM-Erstellung unterstützt.|
| **Ändern der Größe in eine neue VM-Größe**| Daten des Betriebssystem-Datenträgers werden beibehalten.| Daten auf dem Betriebssystem-Datenträger werden gelöscht, Betriebssystem wird erneut bereitgestellt |
| **Erneute Bereitstellung** | Daten des Betriebssystem-Datenträgers werden beibehalten. | Daten auf dem Betriebssystem-Datenträger werden gelöscht, Betriebssystem wird erneut bereitgestellt | 
| **Beenden/Starten des VM** | Daten des Betriebssystem-Datenträgers werden beibehalten. | Daten auf dem Betriebssystem-Datenträger werden gelöscht, Betriebssystem wird erneut bereitgestellt | 
| **Platzierung der Auslagerungsdatei**| Unter Windows ist die Auslagerungsdatei auf dem Ressourcendatenträger gespeichert| Für Windows wird die Seitendatei auf dem Betriebssystemdatenträger gespeichert (sowohl für die Platzierung des Betriebssystemcaches als auch für die Platzierung temporärer Datenträger).|


## <a name="size-requirements"></a>Größenanforderungen

Sie können einen kurzlebigen Betriebssystemdatenträger im VM-Cache oder temporären VM-Datenträger bereitstellen.
Die Größe des Betriebssystemdatenträgers des Bildes sollte kleiner oder gleich der temp/cache-Größe der ausgewählten VM-Größe sein.

Zum Beispiel, wenn Sie sich für **OS-Cache-Platzierung** entscheiden: Standard-Windows-Server-Images vom Markt sind etwa 127 GiB groß, was bedeutet, dass Sie eine VM-Größe benötigen, deren Cache gleich oder größer als 127 GiB ist. Standard_DS3_v2 weist eine Cachegröße von 127 GiB auf und ist damit groß genug. In diesem Fall ist Standard_DS3_v2 die kleinste Größe in der DSv2-Serie, die Sie mit diesem Image verwenden können. 

Wenn Sie sich für die **Platzierung temporärer Datenträger** entscheiden möchten: Das Ubuntu-Standardserverimage aus dem Marketplace beträgt etwa 30 GiB. Um kurzlebigen Betriebssystemdatenträger als temporär zu aktivieren, muss die Größe des temporären Datenträgers mindestens 30 GiB betragen. Standard_B4ms hat eine temporäre Größe von 32 GiB, die für den Betriebssystemdatenträger mit 30 GiB geeignet ist. Bei der Erstellung des virtuellen Computers beträgt der temporäre Speicherplatz 2 GiB. 
> [!Important] 
> Wenn Sie sich für die Platzierung temporärer Datenträger entscheiden, wird die Endgültige temporäre Datenträgergröße = (Anfangsgröße des temporären Datenträgers - Größe des Betriebssystemimages) verwendet.

Einfache Linux- und Windows Server-Images im Marketplace, die durch `[smallsize]` bezeichnet werden, sind in der Regel etwa 30 GiB groß und können die meisten der verfügbaren VM-Größen verwenden.
Kurzlebige Datenträger erfordern außerdem, dass die VM-Größe **Premium-Speicher** unterstützt. Die Größen weisen in der Regel (aber nicht immer) ein `s` im Namen auf, z.B. DSv2- und EsV3. Ausführliche Informationen dazu, welche Größen Storage Premium unterstützen, finden Sie unter den [Größen für virtuelle Azure-Computer](sizes.md).

## <a name="ephemeral-os-disks-can-now-be-stored-on-tempresource-disks"></a>Vorschau: kurzlebige Betriebssystem-Datenträger können jetzt auf temporären Datenträgern gespeichert werden
Kurzlebige Betriebssystemdatenträger können jetzt entweder auf dem VM-Cachedatenträger oder auf dem temporären/Ressourcendatenträger des virtuellen Computers gespeichert werden. Mit diesem Feature können kurzlebige Betriebssystemdatenträger für alle VMs erstellt werden, die nicht über einen Cache oder über unzureichenden Cache verfügen (z. B. Dav3, Dav4, Eav4 und Eav3), aber über genügend temporären Datenträger zum Hosten des kurzlebigen Betriebssystemdatenträgers verfügen.
[DiffDiskPlacement](/rest/api/compute/virtualmachines/list#diffdiskplacement) ist die neue Eigenschaft, die verwendet werden kann, um anzugeben, wo Sie den kurzlebigen Betriebssystemdatenträger platzieren möchten. Bei dieser Funktion wird bei der Bereitstellung eines virtuellen Windows-Computers die Auslagerungsdatei so konfiguriert, dass sie sich auf dem Betriebssystemdatenträger befindet.

## <a name="portal"></a>Portal

Im Azure-Portal können Sie bei der Bereitstellung einer virtuellen Maschine oder von Skalierungssets für virtuelle Maschinen die Verwendung von kurzlebigen Festplatten wählen, indem Sie den Abschnitt **Erweitert** auf der Registerkarte **Festplatten** öffnen. Wählen Sie für die Platzierung der kurzlebigen OS-Festplatte die Optionen **OS-Cache-Platzierung** oder **Temporäre Festplattenplatzierung**.

![Screenshot des Optionsfelds für die Verwendung eines kurzlebigen Betriebssystem-Datenträgers](./media/virtual-machines-common-ephemeral/ephemeral-portal-temp.png)
 

Wenn die Option für die Verwendung eines kurzlebigen Datenträgers abgeblendet ist, haben Sie möglicherweise eine VM-Größe gewählt, deren Cachegröße nicht größer als das Betriebssystemimage ist oder die Premium-Speicher nicht unterstützt. Kehren Sie zur Seite **Grundeinstellungen** zurück, und wählen Sie eine andere VM-Größe aus.

## <a name="scale-set-template-deployment"></a>Vorlagenbereitstellung einer Skalierungsgruppe  
Der Prozess zum Erstellen einer Skalierungsgruppe, die einen kurzlebigen Betriebssystem-Datenträger verwendet, besteht darin, die Eigenschaft `diffDiskSettings` zum Ressourcentyp `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` in der Vorlage hinzuzufügen. Außerdem muss die Cachingrichtlinie für den kurzlebigen Betriebssystem-Datenträger auf `ReadOnly` gesetzt werden. Die Platzierung kann für die Platzierung von Betriebssystemcachedatenträgern in `CacheDisk` geändert werden.

```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2019-12-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
            "option": "Local" ,
            "placement": "ResourceDisk"
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>Vorlagenbereitstellung einer VM 
Sie können eine VM mit einem kurzlebigen Betriebssystem-Datenträger mithilfe einer Vorlage bereitstellen. Der Prozess zum Erstellen einer VM, die kurzlebige Betriebssystem-Datenträger verwendet, besteht darin, die Eigenschaft `diffDiskSettings` zum Ressourcentyp „Microsoft.Compute/virtualMachines“ in der Vorlage hinzuzufügen. Außerdem muss die Cachingrichtlinie für den kurzlebigen Betriebssystem-Datenträger auf `ReadOnly` gesetzt werden. Die Platzierung kann für die Platzierung von Betriebssystemcachedatenträgern in `CacheDisk` geändert werden.

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2019-12-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" ,
                "placement": "ResourceDisk"
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```

> [!NOTE] 
> Die Kurzlebige Platzierungsoption für Betriebssystemdatenträger (VM-Cachedatenträger oder temporärer VM-/Ressourcendatenträger) ist in Kürze in PowerShell und der CLI verfügbar.

## <a name="powershell"></a>PowerShell
Wenn Sie einen kurzlebigen Datenträger für eine PowerShell-VM-Bereitstellung verwenden möchten, verwenden Sie [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) in Ihrer VM-Konfiguration. Legen Sie `-DiffDiskSetting` auf `Local` und `-Caching` auf `ReadOnly` fest.     
```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```
Für Bereitstellungen von Skalierungsgruppen verwenden Sie das Cmdlet [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) in Ihrer Konfiguration. Legen Sie `-DiffDiskSetting` auf `Local` und `-Caching` auf `ReadOnly` fest.

```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

Wenn Sie einen kurzlebigen Datenträger für eine CLI-VM-Bereitstellung verwenden möchten, legen Sie den Parameter `--ephemeral-os-disk` in [az vm create](/cli/azure/vm#az_vm_create) auf `true` und den Parameter `--os-disk-caching` auf `ReadOnly` fest.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

Für Skalierungsgruppen verwenden Sie denselben Parameter `--ephemeral-os-disk true` für [az-vmss-create](/cli/azure/vmss#az_vmss_create) und legen den Parameter `--os-disk-caching` auf `ReadOnly` fest.

## <a name="reimage-a-vm-using-rest"></a>Reimaging einer VM mithilfe von REST
Sie können ein Reimaging für eine VM-Instanz mit einem kurzlebigen Betriebssystemdatenträger über die REST-API, wie weiter unten beschrieben, und über das Azure-Portal durchführen. Für Skalierungsgruppen ist das Reimaging bereits über Powershell, CLI, und das Portal verfügbar.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2019-12-01" 
```
 
## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**F: Wie groß sind lokale Betriebssystem-Datenträger?**

A: Wir unterstützen Plattform-, Shared Image Gallery- und benutzerdefinierte Images bis zur Größe des VM-Caches mit Platzierung des Betriebssystemcaches und bis zur Temporären Datenträgergröße mit temporärer Datenträgerplatzierung, bei der alle Lese-/Schreibvorgänge auf dem Betriebssystemdatenträger auf demselben Knoten wie der virtuelle Computer lokal sind. 

**F: Kann die Größe des kurzlebigen Betriebssystem-Datenträgers geändert werden?**

A: Nein, sobald der kurzlebige Betriebssystem-Datenträger bereitgestellt wurde, kann dessen Größe nicht mehr geändert werden. 

**F: Kann die kurzlebigen Platzierung des Betriebssystemdatenträgers nach der Erstellung des virtuellen Computers geändert werden?**

A: Nein, sobald der kurzlebige Betriebssystem-Datenträger bereitgestellt wurde, kann dessen Größe nicht mehr geändert werden. Der virtuelle Computer kann jedoch über ARM template deployment/PowerShell/CLI neu erstellt werden, indem die Platzierung des gewählten Betriebssystemdatenträgers aktualisiert wird. Dies würde dazu führen, dass die VM neu erstellt wird, wobei die Daten auf dem Betriebssystemdatenträger gelöscht und das Betriebssystem neu bereitgestellt wird.

**F: Wird ein temporärer Datenträger erstellt, wenn die Imagegröße der Größe des temporären Datenträgers der ausgewählten VM-Größe entspricht?**

A: Nein. In diesem Fall wird kein temporäres Laufwerk erstellt.

**F: Werden kurzlebige Betriebssystemdatenträger auf VMs mit niedriger Priorität und Spot-VMs unterstützt?**

A: Ja. Es gibt keine Möglichkeit, Stop-Deallocate für kurzlebige VMs zu verwenden. Stattdessen müssen Benutzer löschen, anstatt deren Löschung zu entfernen.

**F: Kann ich einen verwalteten Datenträger an eine kurzlebigen VM anfügen?**

A: Ja, können Sie einen verwalteten Datenträger an eine VM anfügen, die einen kurzlebigen Betriebssystem-Datenträger verwendet. 

**F: Werden alle VM-Größen für kurzlebige Betriebssystem-Datenträger unterstützt?**

A: Nein, die meisten Storage Premium-VM-Größen werden unterstützt (DS, ES, FS, GS, M usw.). Um zu ermitteln, ob eine bestimmte VM-Größe kurzlebige Betriebssystemdatenträger unterstützt, können Sie wie folgt vorgehen:

Aufrufen des `Get-AzComputeResourceSku` PowerShell-Cmdlets
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
**F: Kann der kurzlebige Betriebssystem-Datenträger für bestehende VMs und Skalierungsgruppen angewendet werden?**

A: Nein, kurzlebigen Betriebssystem-Datenträger kann nur während Erstellung der VM und der Skalierungsgruppe verwendet werden. 

**F: Können kurzlebige und normale Betriebssystem-Datenträger in einer Skalierungsgruppe kombiniert werden?**

A: Nein, Sie können kurzlebigen und persistenten Betriebssystem-Datenträgerinstanzen nicht innerhalb der gleichen Skalierungsgruppe kombinieren. 

**F: Kann der kurzlebiger Betriebssystem-Datenträger mithilfe von Powershell oder CLI erstellt werden?**

A: Ja, Sie können VMs mit kurzlebigen Betriebssystemdatenträgern mit REST, Vorlagen, PowerShell und CLI erstellen.

**F: Welche Features werden nicht mit dem kurzlebigen Betriebssystem-Datenträger unterstützt?**

A: Kurzlebige Datenträger unterstützen nicht:
- Erfassen von VM-Images
- Momentaufnahmen von Datenträgern 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Austausch des Betriebssystem-Datenträgers 

> [!NOTE]
> 
> Auf kurzlebige Datenträger kann nicht über das Portal zugegriffen werden. Beim Zugriff auf einen kurzlebigen Datenträger wird die Fehlermeldung „Ressource nicht gefunden“ oder „404“ angezeigt.
> 
 
## <a name="next-steps"></a>Nächste Schritte
Sie können einen virtuellen Computer mit einem kurzlebigen Betriebssystemdatenträger über die [Azure-Befehlszeilenschnittstelle](/cli/azure/vm#az_vm_create) erstellen.
