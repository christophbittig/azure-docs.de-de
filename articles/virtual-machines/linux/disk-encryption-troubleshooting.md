---
title: Problembehandlung von Azure Disk Encryption für Linux
description: Dieser Artikel beinhaltet Tipps zur Problembehandlung für Microsoft Azure Disk Encryption für Linux VMs.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: acacae6020debf5084ffb99f89760c938324abac
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292867"
---
# <a name="azure-disk-encryption-for-linux-vms-troubleshooting-guide"></a>Leitfaden zur Problembehandlung für Azure Disk Encryption für Linux-VMs

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Dieser Leitfaden ist für IT-Experten, Informationssicherheitsanalysten und Cloudadministratoren bestimmt, in deren Organisationen Azure Disk Encryption verwendet wird. Dieser Artikel hilft beim Beheben von Problemen mit der Verschlüsselung von Datenträgern.

Bevor Sie einen der folgenden Schritte ausführen, vergewissern Sie sich zunächst, dass die zu verschlüsselnden VMs zu den[unterstützten VM-Größen und -Betriebssystemen](disk-encryption-overview.md#supported-vms-and-operating-systems) gehören und dass alle Voraussetzungen erfüllt sind:

- [Weitere Anforderungen für VMs](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Netzwerkanforderungen](disk-encryption-overview.md#networking-requirements)
- [Speicheranforderungen für Verschlüsselungsschlüssel](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Problembehandlung für die Verschlüsselung von Datenträgern mit Linux-Betriebssystem

Bei der Verschlüsselung von Datenträgern mit Linux-Betriebssystem muss die Bereitstellung des Betriebssystemlaufwerks aufgehoben werden, bevor die vollständige Datenträgerverschlüsselung erfolgt. Wenn das Aufheben der Bereitstellung nicht möglich ist, wird voraussichtlich eine Fehlermeldung der Art „Fehler beim Aufheben der Bereitstellung…“ angezeigt.

Dieser Fehler kann auftreten, wenn die Verschlüsselung von Betriebssystemdatenträgern für eine VM mit einer Umgebung durchgeführt wird, die gegenüber dem unterstützten normalen Katalogimage verändert wurde. Abweichungen vom unterstützten Image können die Fähigkeit der Erweiterung zum Aufheben der Bereitstellung des Betriebssystemlaufwerks beeinträchtigen. Beispiele für Abweichungen:
- Angepasste Images, die nicht mehr mit einem unterstützten Dateisystem oder Partitionierungsschema übereinstimmen.
- Große Anwendungen, z.B. SAP, MongoDB, Apache Cassandra oder Docker, werden nicht unterstützt, wenn sie vor der Verschlüsselung im Betriebssystem installiert und ausgeführt werden. Azure Disk Encryption kann diese Prozesse zur Vorbereitung des Betriebssystemlaufwerks für die Datenträgerverschlüsselung nicht sicher herunterfahren. Wenn es immer noch aktive Prozesse mit offenen Dateihandles auf dem Betriebssystemlaufwerk gibt, kann die Bereitstellung des Betriebssystemlaufwerks nicht aufgehoben werden, was zu einem Fehler bei der Verschlüsselung des Betriebssystemlaufwerks führt. 
- Wenn benutzerdefinierte Skripts in einem engen zeitlichen Abstand zur Aktivierung der Verschlüsselung ausgeführt werden oder während des Verschlüsselungsprozesses andere Änderungen an der VM vorgenommen werden. Dieser Konflikt kann auftreten, wenn eine Azure Resource Manager-Vorlage mehrere Erweiterungen für die gleichzeitige Ausführung definiert oder wenn eine benutzerdefinierte Skripterweiterung oder andere Aktion parallel zur Datenträgerverschlüsselung erfolgt. Durch eine Serialisierung und Isolierung dieser Schritte lässt sich das Problem ggf. beheben.
- Security Enhanced Linux (SELinux) wurde vor der Aktivierung der Verschlüsselung nicht deaktiviert, weshalb der Schritt zur Aufhebung der Bereitstellung zu einem Fehler führt. SELinux kann wieder aktiviert werden, nachdem die Verschlüsselung abgeschlossen ist.
- Der Betriebssystemdatenträger verwendet ein LVM-Schema (Logical Volume Manager). Wenngleich eingeschränkte Unterstützung für LVM-Datenträger geboten wird, gilt dies nicht für LVM-Betriebssystemdatenträger.
- Die Mindestanforderungen für den Arbeitsspeicher sind nicht erfüllt (für die Verschlüsselung des Betriebssystemdatenträgers werden 7 GB empfohlen).
- Datenlaufwerke sind rekursiv im Verzeichnis „/mnt/“ oder gegenseitig bereitgestellt worden (z.B. „/mnt/data1“, „/mnt/data2“, „/data3 + /data3/data4“).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>Aktualisieren des Standardkernels für Ubuntu 14.04 LTS

Das Ubuntu 14.04 LTS-Image wird mit der Standardkernelversion 4.4 ausgeliefert. Diese Kernelversion weist ein bekanntes Problem auf, bei dem Out of Memory Killer den dd-Befehl während des Betriebssystem-Verschlüsselungsvorgangs nicht ordnungsgemäß beendet. Dieser Fehler wurde im aktuellen, für Azure optimierten Linux-Kernel behoben. Um diesen Fehler zu vermeiden, führen Sie vor dem Aktivieren der Verschlüsselung im Image ein Update auf den [für Azure optimierten Kernel 4.15](https://packages.ubuntu.com/trusty/linux-azure) aus. Verwenden Sie dazu die folgenden Befehle:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Nach dem Neustart des virtuellen Computers mit dem neuen Kernel kann die neue Kernelversion folgendermaßen überprüft werden:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Aktualisieren des Azure VM-Agenten und der Erweiterungsversionen

Azure Disk Encryption-Vorgänge können auf VM-Images fehlschlagen, die nicht unterstützte Versionen des Azure-VM-Agents verwenden. Linux-Images mit Agent-Versionen vor 2.2.38 sollten vor der Aktivierung der Verschlüsselung aktualisiert werden. Weitere Informationen finden Sie unter [Aktualisieren des Azure Linux-Agent auf einer VM](../extensions/update-linux-agent.md) und [Unterstützte Mindestversion für VM-Agents in Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) aktualisieren können.

Die richtige Version der Gast-Agent-Erweiterung „Microsoft.Azure.Security.AzureDiskEncryption“ oder „Microsoft.Azure.Security.AzureDiskEncryptionForLinux“ ist auch erforderlich. Erweiterungsversionen werden von der Plattform automatisch verwaltet und aktualisiert, wenn die Voraussetzungen für den Azure VM-Agent erfüllt sind und eine unterstützte Version des VM-Agents verwendet wird.

Die Erweiterung „Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux“ ist veraltet und wird nicht mehr unterstützt.  

## <a name="unable-to-encrypt-linux-disks"></a>Verschlüsselung von Linux-Datenträgern nicht möglich

In einigen Fällen hängt die Verschlüsselung des Linux-Datenträgers scheinbar bei „OS disk encryption started“, und SSH ist deaktiviert. Dieser Prozess kann bei einem normalen Katalogimage 3-16 Stunden bis zum Abschluss dauern. Wenn Datenträger mit mehreren Terabyte Daten hinzugefügt werden, kann der Prozess Tage dauern.

Durch die Verschlüsselungssequenz für Linux-Betriebssystemdatenträger wird die Bereitstellung des Betriebssystemlaufwerks vorübergehend aufgehoben. Es erfolgt anschließend eine blockweise Verschlüsselung des gesamten Betriebssystemdatenträgers, ehe er im verschlüsselten Zustand wieder bereitgestellt wird. Mit Linux Disk Encryption ist die gleichzeitige Nutzung der VM während des Verschlüsselungsvorgangs nicht möglich. Die Leistungsmerkmale der VM können sich signifikant auf den Zeitaufwand auswirken, der bis zur Verschlüsselung anfällt. Zu diesen Merkmalen zählen die Größe des Datenträgers und das Speicherkonto (Standard oder Storage Premium).

Während das Betriebssystemlaufwerk verschlüsselt wird, befindet sich die VM in einem Wartungszustand, und SSH wird deaktiviert, um eine Störung des laufenden Prozesses zu verhindern.  Verwenden Sie den Azure PowerShell-Befehl [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus), und überprüfen Sie das Feld **ProgressMessage**, um den Verschlüsselungsstatus zu überprüfen. **ProgressMessage** meldet eine Reihe von Status, während die Daten- und Betriebssystemdatenträger verschlüsselt werden:

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings :
ProgressMessage            : Transitioning

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for data volumes

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

**ProgressMessage** verbleibt während des Großteils des Verschlüsselungsvorgangs im Status **Verschlüsselung des Betriebssystem-Datenträgers gestartet**.  Wenn die Verschlüsselung erfolgreich abgeschlossen wurde, gibt **ProgressMessage-** Folgendes zurück:

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for all volumes
```

Wenn diese Meldung angezeigt wird, sollte das verschlüsselte Betriebssystemlaufwerk betriebsbereit und die VM wieder verwendbar sein.

Wenn die Startinformationen, die Fortschrittsmeldung oder ein Fehler meldet, dass die Betriebssystemverschlüsselung während dieses Prozesses fehlgeschlagen ist, stellen Sie die VM mithilfe der Momentaufnahme bzw. der unmittelbar vor der Verschlüsselung erstellten Sicherung wieder her. Ein Beispiel einer Meldung ist der in dieser Anleitung beschriebene Fehler „Fehler beim Aufheben der Bereitstellung“.

Prüfen Sie vor einem neuerlichen Verschlüsselungsversuch die Merkmale der VM, und stellen Sie sicher, dass alle Voraussetzungen erfüllt sind.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Azure Disk Encryption-Problembehandlung hinter einer Firewall

Weitere Informationen finden Sie unter [Datenträgerverschlüsselung in einem isolierten Netzwerk](disk-encryption-isolated-network.md).

## <a name="troubleshooting-encryption-status"></a>Behandeln von Problemen mit dem Verschlüsselungsstatus 

Im Portal kann ein Datenträger als verschlüsselt angezeigt werden, auch nachdem er bereits auf dem virtuellen Computer entschlüsselt wurde.  Dies kann auftreten, wenn Befehle auf niedriger Ebene verwendet werden, um den Datenträger auf dem virtuellen Computer zu entschlüsseln, anstatt die Verwaltungsbefehle auf höherer Ebene von Azure Disk Encryption zu verwenden.  Die Befehle auf höherer Ebene entschlüsseln den Datenträger nicht nur auf dem virtuellen Computer, sondern sie aktualisieren auch außerhalb des virtuellen Computers wichtige Verschlüsselungs- und Erweiterungseinstellungen auf Plattformebene, die dem virtuellen Computer zugeordnet sind.  Wenn diese nicht einheitlich gehalten werden, kann die Plattform den Verschlüsselungsstatus nicht melden und den virtuellen Computer nicht ordnungsgemäß bereitstellen.

Verwenden Sie zum Deaktivieren von Azure Disk Encryption mit PowerShell [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption), gefolgt von [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Wenn Sie „Remove-AzVMDiskEncryptionExtension“ ausführen, bevor die Verschlüsselung deaktiviert wurde, tritt ein Fehler auf.

Verwenden Sie zum Deaktivieren von Azure Disk Encryption mit der CLI [az vm encryption disable](/cli/azure/vm/encryption).

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie weitere Informationen zu einigen häufigen Problemen in Azure Disk Encryption und deren Behandlung erhalten. Weitere Informationen zu diesem Dienst und seinen Funktionen finden Sie in den folgenden Artikeln:

- [Anwenden der Datenträgerverschlüsselung in Microsoft Defender für Cloud](../../security-center/asset-inventory.md)
- [Datenverschlüsselung ruhender Azure-Daten](../../security/fundamentals/encryption-atrest.md)
