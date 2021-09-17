---
title: 'Überprüfen des Verschlüsselungsstatus unter Linux: Azure Disk Encryption'
description: In diesem Artikel finden Sie Anweisungen zum Überprüfen des Verschlüsselungsstatus auf der Plattform- und Betriebssystemebene.
author: kailashmsft
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 6808d7dc2c2addcf6639c75dfd870ac7fe62d262
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696968"
---
# <a name="verify-encryption-status-for-linux"></a>Überprüfen des Verschlüsselungsstatus unter Linux 

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

In diesem Artikel wird beschrieben, wie der Verschlüsselungsstatus eines virtuellen Computers mit verschiedenen Methoden überprüft wird: Azure-Portal, PowerShell, Azure CLI oder Betriebssystem des virtuellen Computers (VM). 

Sie können den Verschlüsselungsstatus entweder während oder nach der Verschlüsselung überprüfen, indem Sie wie folgt vorgehen:

- Überprüfen der Datenträger, die an einen bestimmten virtuellen Computer angefügt sind 
- Abfragen der Verschlüsselungseinstellungen auf jedem einzelnen Datenträger, um zu ermitteln, ob er angefügt oder nicht angefügt ist

Dieses Szenario gilt für Azure Disk Encryption-Erweiterungen mit einem und zwei Durchläufen. Linux-Distributionen sind die einzig mögliche Umgebung für dieses Szenario.

>[!NOTE] 
>Im gesamten Artikel werden Variablen verwendet. Ersetzen Sie die Werte entsprechend.

## <a name="portal"></a>Portal

Wählen Sie im Azure-Portal im Abschnitt **Erweiterungen** in der Liste die Azure Disk Encryption-Erweiterung aus. In den Informationen der **Statusmeldung** ist der aktuelle Verschlüsselungsstatus angegeben:

![Überprüfung im Portal mit Hervorhebung von Status, Version und Statusmeldung](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

In der Liste mit den Erweiterungen wird die entsprechende Version der Azure Disk Encryption-Erweiterung angezeigt. Version 0.x entspricht Azure Disk Encryption mit zwei Durchläufen, und Version 1.x entspricht Azure Disk Encryption mit einem Durchlauf.

Weitere Details erhalten Sie, indem Sie die Erweiterung und dann die Option **Detaillierten Status anzeigen** auswählen. Der ausführliche Status des Verschlüsselungsprozesses wird im JSON-Format angezeigt.

![Überprüfung im Portal mit Hervorhebung des Links „Detaillierten Status anzeigen“](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Detaillierter Status im JSON-Format](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Eine weitere Möglichkeit zum Überprüfen des Verschlüsselungsstatus ist das Anzeigen des Abschnitts mit den **Datenträgereinstellungen**.

![Verschlüsselungsstatus für Betriebssystemdatenträger und Datenträger](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Mit diesem Status wird angegeben, dass die Datenträger über gestempelte Verschlüsselungseinstellungen verfügen. Es bedeutet nicht, dass sie tatsächlich auf der Betriebssystemebene verschlüsselt wurden.
>
> Die Datenträger werden standardmäßig erst gestempelt und dann später verschlüsselt. Wenn der Verschlüsselungsprozess fehlschlägt, werden die Datenträger womöglich gestempelt, aber nicht verschlüsselt. 
>
> Sie können die Verschlüsselung der Datenträger auf der Betriebssystemebene wiederholt prüfen, um sicherzustellen, dass die Datenträger wirklich verschlüsselt wurden.

## <a name="powershell"></a>PowerShell

Sie können den *allgemeinen* Verschlüsselungsstatus einer verschlüsselten VM mit den folgenden PowerShell-Befehlen überprüfen:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![Allgemeiner Verschlüsselungsstatus in PowerShell](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Sie können die Verschlüsselungseinstellungen jedes Datenträgers mit den folgenden PowerShell-Befehlen erfassen.

### <a name="single-pass"></a>Einzeldurchlauf
Bei nur einem Durchlauf werden die Verschlüsselungseinstellungen auf jeden Datenträger (Betriebssystem und Daten) gestempelt. Sie können die Verschlüsselungseinstellungen für einen Betriebssystemdatenträger wie folgt mit einem einzelnen Durchlauf erfassen:

```powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![Verschlüsselungseinstellungen für einen Betriebssystemdatenträger](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Wenn der Datenträger über keine gestempelten Verschlüsselungseinstellungen verfügt, ist die Ausgabe leer:

![Leere Ausgabe](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Verwenden Sie die folgenden Befehle, um die Verschlüsselungseinstellungen für reguläre Datenträger zu erfassen:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![Verschlüsselungseinstellungen für reguläre Datenträger](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Zwei Durchläufe
Bei einem Vorgang mit zwei Durchläufen werden die Verschlüsselungseinstellungen nicht auf jedem einzelnen Datenträger, sondern im VM-Modell gestempelt.

Sie können mit den folgenden Befehlen überprüfen, ob die Verschlüsselungseinstellungen bei einem Vorgang mit zwei Durchläufen gestempelt wurden:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![Verschlüsselungseinstellungen bei einem Vorgang mit zwei Durchläufen](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Nicht angefügte Datenträger

Überprüfen Sie die Verschlüsselungseinstellungen von Datenträgern, die nicht an eine VM angefügt sind.

### <a name="managed-disks"></a>Verwaltete Datenträger
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="azure-cli"></a>Azure CLI

Sie können den *allgemeinen* Verschlüsselungsstatus einer verschlüsselten VM mit den folgenden Azure CLI-Befehlen überprüfen:

```azurecli
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Allgemeiner Verschlüsselungsstatus per Azure CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Einzeldurchlauf
Sie können die Verschlüsselungseinstellungen für jeden Datenträger überprüfen, indem Sie die folgenden Azure CLI-Befehle verwenden:

```azurecli
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Datenverschlüsselungseinstellungen](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Falls der Datenträger nicht über gestempelte Verschlüsselungseinstellungen verfügt, wird der Text **Datenträger ist nicht verschlüsselt** angezeigt.

Verwenden Sie die folgenden Befehle, um den detaillierten Status und die Verschlüsselungseinstellungen abzurufen.

Betriebssystemdatenträger:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Detaillierter Status und Verschlüsselungseinstellungen für den Betriebssystemdatenträger](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Reguläre Datenträger:

```azurecli
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Detaillierter Status und Verschlüsselungseinstellungen für die regulären Datenträger](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Zwei Durchläufe

```azurecli
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Allgemeine Verschlüsselungseinstellungen für Vorgang mit zwei Durchläufen per Azure CLI](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png)

Sie können die Verschlüsselungseinstellungen auch im Speicherprofil des VM-Modells auf dem Betriebssystemdatenträger überprüfen:

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![VM-Profil für zwei Durchläufe per Azure CLI](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Nicht angefügte Datenträger

Überprüfen Sie die Verschlüsselungseinstellungen von Datenträgern, die nicht an eine VM angefügt sind.

### <a name="managed-disks"></a>Verwaltete Datenträger

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>Nicht verwaltete Datenträger

Nicht verwaltete Datenträger sind VHD-Dateien, die als Seitenblobs in Azure-Speicherkonten gespeichert sind.

Sie müssen Folgendes angeben, um die Details zu einem spezifischen Datenträger abzurufen:

- Die ID des Speicherkontos, das den Datenträger enthält
- Eine Verbindungszeichenfolge für das spezifische Speicherkonto
- Der Name des Containers, in dem der Datenträger gespeichert wird
- Der Name des Datenträgers

Mit dem folgenden Befehl werden alle IDs für all Ihre Speicherkonten aufgeführt:

```azurecli
az storage account list --query [].[id] -o tsv
```
Die Speicherkonto-IDs werden im folgenden Format aufgeführt:

/subscriptions/\<subscription id>/resourceGroups/\<resource group name>/providers/Microsoft.Storage/storageAccounts/\<storage account name>

Wählen Sie die entsprechende ID aus, und speichern Sie sie in einer Variable:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```

Mit dem folgenden Befehl wird die Verbindungszeichenfolge für ein bestimmtes Speicherkonto abgerufen und in einer Variable gespeichert:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Mit dem folgenden Befehl werden alle Container in einem Speicherkonto aufgeführt:
```azurecli
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
Der Container, der für Datenträger verwendet wird, hat normalerweise den Namen „vhds“.

Speichern Sie den Containernamen in einer Variablen: 
```bash
ContainerName="name of the container"
```

Verwenden Sie den folgenden Befehl, um alle Blobs eines bestimmten Containers aufzuführen:
```azurecli 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Wählen Sie den abzufragenden Datenträger aus, und speichern Sie seinen Namen in einer Variablen:
```bash
DiskName="diskname.vhd"
```
Fragen Sie die Verschlüsselungseinstellungen des Datenträgers ab:
```azurecli
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="operating-system"></a>Betriebssystem
Überprüfen Sie, ob die Datenträgerpartitionen verschlüsselt sind (und der Betriebssystemdatenträger nicht).

Wenn eine Partition oder ein Datenträger verschlüsselt ist, wird als Typ **crypt** angezeigt. Wenn keine Verschlüsselung durchgeführt wurde, wird als Typ **part/disk** angezeigt.

```bash
lsblk
```

![Betriebssystemebene „crypt“ für eine Partition](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Mit der folgenden Variante des Befehls **lsblk** können Sie weitere Details abrufen. 

Daraufhin wird die Ebene des Typs **crypt** angezeigt, die von der Erweiterung eingebunden wird. Im folgenden Beispiel werden logische Volumes und reguläre Datenträger mit **crypto\_LUKS FSTYPE** veranschaulicht.

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Betriebssystemebene „crypt“ für logische Volumes und reguläre Datenträger](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Sie können in einem zusätzlichen Schritt überprüfen, ob der Datenträger über geladene Schlüssel verfügt:

```bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

```bash
cryptsetup luksDump /dev/sdd1
```

Darüber hinaus können Sie überprüfen, welche **dm**-Geräte als **crypt** aufgeführt sind:

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Nächste Schritte

- [Azure Disk Encryption-Problembehandlung](disk-encryption-troubleshooting.md)
