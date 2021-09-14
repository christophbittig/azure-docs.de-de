---
title: Wiederherstellen verschlüsselter virtueller Azure-Computer
description: Beschreibt, wie verschlüsselte virtuelle Azure-Computer (VMs) mit dem Azure Backup-Dienst wiederhergestellt werden.
ms.topic: conceptual
ms.date: 08/20/2021
ms.openlocfilehash: 95d665fb2ae2e1dc0427090842cddbecfb3c3706
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187846"
---
# <a name="restore-encrypted-azure-virtual-machines"></a>Sichern und Wiederherstellen verschlüsselter virtueller Azure-Computer

In diesem Artikel wird beschrieben, wie Sie virtuelle Azure-Computer (VMs) unter Windows oder Linux mit verschlüsselten Datenträgern mithilfe des Diensts [Azure Backup](backup-overview.md) wiederherstellen. Weitere Informationen finden Sie unter [Verschlüsseln von Azure-VM-Sicherungen](backup-azure-vms-introduction.md#encryption-of-azure-vm-backups).


## <a name="before-you-start"></a>Vorbereitung

Überprüfen der bekannten Einschränkungen vor dem Beginn der Wiederherstellung eines verschlüsselten virtuellen Computers

- Mit ADE verschlüsselte VMs können innerhalb des gleichen Abonnements gesichert und wiederhergestellt werden.
- Azure Backup unterstützt mit eigenständigen Schlüsseln verschlüsselte VMs. Ein Schlüssel, der Teil eines Zertifikats ist, das zum Verschlüsseln eines virtuellen Computers verwendet wurde, wird derzeit nicht unterstützt.
- Mit ADE verschlüsselte virtuelle Computer können nicht auf Datei- oder Ordnerebene wiederhergestellt werden. Sie müssen den gesamten virtuellen Computer wiederherstellen, damit Dateien und Ordner wiederhergestellt werden.
- Beim Wiederherstellen eines virtuellen Computers kann die Option [Vorhandenen virtuellen Computer ersetzen](backup-azure-arm-restore-vms.md#restore-options) für mit ADE verschlüsselte virtuelle Computer nicht verwendet werden. Diese Option wird nur bei unverschlüsselten verwalteten Datenträgern unterstützt.


## <a name="restore-an-encrypted-vm"></a>Wiederherstellen eines verschlüsselten virtuellen Computers

Verschlüsselte virtuelle Computer können nur durch Wiederherstellen des VM-Datenträgers und Erstellen einer VM-Instanz wiederhergestellt werden, wie weiter unten erläutert. Das **Ersetzen des vorhandenen Datenträgers auf dem vorhandenen virtuellen Computer**, das **Erstellen eines virtuellen Computers aus Wiederherstellungspunkten** und das Wiederherstellen auf **Datei- oder Ordnerebene** werden derzeit nicht unterstützt.
 
Führen Sie die folgenden Schritte aus, um verschlüsselte virtuelle Computer wiederherzustellen:

### <a name="step-1-restore-the-vm-disk"></a>**Schritt 1**: Wiederherstellen des VM-Datenträgers

1. Wählen Sie unter **Konfiguration wiederherstellen** > **Neu erstellen** > **Wiederherstellungstyp** die Option **Datenträger wiederherstellen** aus.
1. Wählen Sie unter **Ressourcengruppe** eine vorhandene Ressourcengruppe für den wiederhergestellten Datenträger aus, oder erstellen Sie eine neue Ressourcengruppe mit einem global eindeutigen Namen.
1. Geben Sie unter **Stagingspeicherort** das Speicherkonto an, in das die virtuellen Festplatten kopiert werden sollen. [Weitere Informationen](backup-azure-arm-restore-vms.md#storage-accounts)

    ![Auswählen der Ressourcengruppe und des Stagingspeicherorts](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

1. Wählen Sie **Wiederherstellen** aus, um den Wiederherstellungsvorgang zu starten.

Wenn Ihr virtueller Computer verwaltete Datenträger verwendet und Sie die Option **Virtuellen Computer erstellen** auswählen, wird Azure Backup das angegebene Speicherkonto nicht verwenden. In den Fällen **Wiederherstellen von Datenträgern** und **Sofortige Wiederherstellung** wird das Speicherkonto nur zum Speichern der Vorlage verwendet. Verwaltete Datenträger werden in der angegebenen Ressourcengruppe erstellt.
Wenn Ihr virtueller Computer nicht verwaltete Datenträger verwendet, werden diese als Blobs für das Speicherkonto wiederhergestellt.

   > [!NOTE]
   > Nachdem Sie den VM-Datenträger wiederhergestellt haben, können Sie den Betriebssystemdatenträger des ursprünglichen virtuellen Computers manuell gegen den wiederhergestellten VM-Datenträger austauschen, ohne ihn neu zu erstellen. [Weitere Informationen](https://azure.microsoft.com/blog/os-disk-swap-managed-disks/)

### <a name="step-2-recreate-the-virtual-machine-instance"></a>**Schritt 2**: Neuerstellen der VM-Instanz 

Führen Sie eine der folgenden Aktionen aus:

- Verwenden Sie die Vorlage, die während des Wiederherstellungsvorgangs generiert wurde, um VM-Einstellungen anzupassen und die Bereitstellung der VM auszulösen. [Weitere Informationen](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)
  >[!NOTE]
   >Überprüfen Sie beim Bereitstellen der Vorlage die Speicherkontocontainer und die öffentlichen/privaten Einstellungen.
- Erstellen Sie mithilfe von PowerShell eine neue VM aus den wiederhergestellten Datenträgern. [Weitere Informationen](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)

### <a name="step-3-restore-an-encrypted-linux-vm"></a>**Schritt 3**: Wiederherstellen eines verschlüsselten virtuellen Linux-Computers

Installieren Sie die ADE-Erweiterung neu, damit die Datenträger offen und eingebunden sind.

## <a name="cross-region-restore-for-an-encrypted-azure-vm"></a>Regionsübergreifende Wiederherstellung für einen verschlüsselten virtuellen Azure-Computer

Azure Backup unterstützt die regionsübergreifende Wiederherstellung verschlüsselter Azure-VMs in den [Azure-Regionspaaren](../best-practices-availability-paired-regions.md). Erfahren Sie, wie Sie für einen verschlüsselten virtuellen Computer die [regionsübergreifende Wiederherstellung aktivieren](backup-create-rs-vault.md#configure-cross-region-restore).

## <a name="move-an-encrypted-azure-vm"></a>Verschieben eines verschlüsselten virtuellen Azure-Computers

Das Verschieben eines verschlüsselten virtuellen Computers in über einen Tresor oder eine Ressourcengruppe hinweg entspricht dem Verschieben eines gesicherten virtuellen Azure-Computers. Informationen hierzu finden Sie unter .

- [Schritte zum Verschieben eines virtuellen Azure-Computers in einen anderen Recovery Services-Tresor](backup-azure-move-recovery-services-vault.md#move-an-azure-virtual-machine-to-a-different-recovery-service-vault)
- [Schritte zum Verschieben eines virtuellen Azure-Computers in eine andere Ressourcengruppe](../azure-resource-manager/management/move-resource-group-and-subscription.md)


## <a name="next-steps"></a>Nächste Schritte

Sollten Probleme auftreten, sehen Sie sich die folgenden Artikel an:

- [Häufige Fehler](backup-azure-vms-troubleshoot.md) beim Sichern und Wiederherstellen von verschlüsselten virtuellen Azure-Computern.
- Probleme im Zusammenhang mit [Azure VM-Agent/Backup-Erweiterung](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md).



