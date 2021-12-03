---
title: Aktivieren der Sicherung beim Erstellen eines virtuellen Azure-Computers
description: Hier wird beschrieben, wie die Sicherung beim Erstellen eines virtuellen Azure-Computers mit Azure Backup aktiviert wird.
ms.topic: conceptual
ms.date: 11/09/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: d94faf113fb3d75c1c0f5c878369c1856366b1be
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332116"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Aktivieren der Sicherung beim Erstellen eines virtuellen Azure-Computers

Verwenden Sie den Azure Backup-Dienst zum Sichern von Azure-VMs (virtuelle Computer). VMs werden gemäß eines Zeitplans gesichert, der in einer Sicherungsrichtlinie festgelegt wird. Die Wiederherstellungspunkte werden aus Sicherungen erstellt. Die Wiederherstellungspunkte werden in Recovery Services-Tresoren gespeichert.

In diesem Artikel erfahren Sie, wie Sie die Sicherung beim Erstellen eines virtuellen Computers (Virtual Machine, VM) über das Azure-Portal aktivieren.  

## <a name="before-you-start"></a>Vorbereitung

- [Überprüfen Sie](backup-support-matrix-iaas.md#supported-backup-actions), welche Betriebssysteme unterstützt werden, wenn Sie beim Erstellen einer VM die Sicherung aktivieren.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Falls Sie noch nicht bei Ihrem Konto angemeldet sind, melden Sie beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-vm-with-backup-configured"></a>Erstellen einer VM mit konfigurierter Sicherung

1. Klicken Sie im Azure-Portal auf **Ressource erstellen**.

2. Wählen Sie im Azure Marketplace **Compute** und dann ein VM-Image aus.

   >[!Note]
   >Um einen VM aus einem Nicht-Marketplace-Bild zu erstellen oder die Betriebssystemplatte eines VM mit einem Nicht-Marketplace-Bild auszutauschen, entfernen Sie die Planinformationen aus dem VM. Dies hilft bei der nahtlosen VM-Wiederherstellung.

3. Richten Sie die VM gemäß den Anweisungen für [Windows](../virtual-machines/windows/quick-create-portal.md) oder [Linux](../virtual-machines/linux/quick-create-portal.md) ein.

4. Wählen Sie auf der Registerkarte **Verwaltung** unter **Backup aktivieren** die Option **Ein** aus.
5. Azure Backup führt Sicherungen in einen Recovery Services-Tresor durch. Wählen Sie **Neu erstellen** aus, wenn Sie über keinen vorhandenen Tresor verfügen.
6. Akzeptieren Sie den vorgeschlagenen Tresornamen, oder geben Sie einen eigenen an.
7. Geben Sie eine Ressourcengruppe an oder erstellen Sie sie, in der sich der Tresor befinden wird. Der Tresor der Ressourcengruppe kann sich von der VM-Ressourcengruppe unterscheiden.

    ![Aktivieren der Sicherung für einen virtuellen Computer](./media/backup-during-vm-creation/enable-backup.png)

8. Akzeptieren Sie die Standardsicherungsrichtlinie, oder ändern Sie die Einstellungen.
    - Eine Sicherungsrichtlinie gibt an, wie häufig Sicherungsmomentaufnahmen des virtuellen Computers erstellt und wie lang die Sicherungskopien aufbewahrt werden sollen.
    - Die Standardrichtlinie sichert den virtuellen Computer einmal täglich.
    - Sie können Ihre eigene Sicherungsrichtlinie für eine Azure-VM anpassen, um täglich oder wöchentlich Sicherungen zu erstellen.
    - [Weitere Informationen](backup-azure-vms-introduction.md#backup-and-restore-considerations) zu Überlegungen zur Sicherung für Azure-VMs.
    - [Weitere Informationen](backup-instant-restore-capability.md) zur Funktionalität der sofortigen Wiederherstellung.

      ![Standardsicherungsrichtlinie](./media/backup-during-vm-creation/daily-policy.png)

>[!NOTE]
> [SSE und PMK sind die Standardverschlüsselungsmethoden](backup-encryption.md) für Azure-VMs. Azure Backup unterstützt die Sicherung und Wiederherstellung dieser virtuellen Azure-Computer.

## <a name="azure-backup-resource-group-for-virtual-machines"></a>Azure Backup-Ressourcengruppe für virtuelle Computer

Der Backup-Dienst erstellt eine separate Ressourcengruppe (RG) neben der Ressourcengruppe des virtuellen Computers zum Speichern der Wiederherstellungspunktsammlung (RPC). Die RPC enthält die Wiederherstellungspunkte für die sofortige Wiederherstellung der verwalteten VMs. Das Standardnamensformat der vom Backup-Dienst erstellten Ressourcengruppe sieht folgendermaßen aus: `AzureBackupRG_<Geo>_<number>`. Beispiel: *AzureBackupRG_northeurope_1*. Sie können den von Azure Backup erstellten Ressourcengruppennamen jetzt anpassen.

Beachten Sie Folgendes:

1. Sie können entweder den Standardnamen der RG verwenden oder ihn entsprechend den Anforderungen Ihres Unternehmens bearbeiten.<br>Wenn Sie keine RG erstellt haben, führen Sie die folgenden Schritte aus, um eine RG für restorepointcollection anzugeben:
   1. Erstellen Sie eine RG für restorepointcollection, zum Beispiel „rpcrg“.
   1. Geben Sie den Namen der RG in der VM-Sicherungsrichtlinie an.
   >[!NOTE]
   >Dadurch wird eine RG mit dem angefügten Zahl erstellt und für restorepointcollection verwendet.
1. Beim Erstellen der VM-Sicherungsrichtlinie geben Sie das RG-Namensmuster als Eingabe an. Der RG-Name sollte das folgende Format aufweisen: `<alpha-numeric string>* n <alpha-numeric string>`. „n“ wird durch eine ganze Zahl (beginnend mit 1) ersetzt und zum horizontalen Hochskalieren verwendet, wenn die erste RG voll ist. Eine RG kann heute maximal 600 Remoteprozeduraufrufe umfassen.
              ![Wählen Sie den Namen beim Erstellen einer Richtlinie aus](./media/backup-during-vm-creation/create-policy.png)
1. Das Muster sollte den nachstehend aufgeführten RG-Benennungsregeln folgen, und die Gesamtlänge sollte die maximal zulässige Länge für RG-Namen nicht überschreiten.
    1. Ressourcengruppennamen dürfen nur alphanumerische Zeichen, Punkte, Unterstriche, Bindestriche und Klammern enthalten. Sie dürfen nicht mit einem Punkt enden.
    2. Ressourcengruppennamen können bis zu 74 Zeichen enthalten, einschließlich des Namens der RG und des Suffixes.
1. Die erste `<alpha-numeric-string>` ist obligatorisch, während die zweite nach dem „n“ optional ist. Dies gilt nur, wenn Sie einen benutzerdefinierten Namen vergeben. Wenn Sie beide Textfelder leer lassen, wird der Standardname verwendet.
1. Sie können den Namen der RG bearbeiten, indem Sie die Richtlinie ändern, wenn dies erforderlich ist. Wenn das Namensmuster geändert wird, werden in der neuen RG neue RPs erstellt. Die alten RPs befinden sich jedoch weiterhin in der alten RG und werden nicht verschoben, da die RP-Sammlung kein Verschieben von Ressourcen unterstützt. Schließlich erfolgt eine automatische Speicherbereinigung der RPs, wenn die Punkte ablaufen.
![Ändern des Namens beim Ändern der Richtlinie](./media/backup-during-vm-creation/modify-policy.png)
1. Die für die Verwendung durch den Backup-Dienst erstellte Ressourcengruppe sollte nicht gesperrt werden.

Informationen zum Konfigurieren der Azure Backup-Ressourcengruppe für Virtual Machines mithilfe von PowerShell finden Sie unter [Erstellen einer Azure Backup-Ressourcengruppe während der Aufbewahrung von Momentaufnahmen](backup-azure-vms-automation.md#creating-azure-backup-resource-group-during-snapshot-retention).

## <a name="start-a-backup-after-creating-the-vm"></a>Starten einer Sicherung nach dem Erstellen der VM

Ihre VM-Sicherung wird in Übereinstimmung mit Ihrer Sicherungsrichtlinie ausgeführt. Es wird jedoch empfohlen, eine erste Sicherung auszuführen.

Nachdem die VM erstellt wurde, gehen Sie wie folgt vor:

1. Wählen Sie unter den VM-Eigenschaften **Sicherung** aus. Der VM-Status lautet „Erste Sicherung ausstehend“, bis die erste Sicherung ausgeführt wird.
2. Wählen Sie **Jetzt sichern** aus, um eine bedarfsgesteuerte Sicherung auszuführen.

    ![Ausführen einer On-Demand-Sicherung](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Bereitstellen einer geschützten VM mithilfe einer Resource Manager-Vorlage

In den vorherigen Schritten wurde erläutert, wie Sie eine VM über das Azure-Portal erstellen und in einem Recovery Services-Tresor schützen. Informationen zur schnellen Bereitstellung und zum Schützen von VMs in einem Recovery Services-Tresor finden Sie unter [Deploy a Windows VM and enable backup (Bereitstellen einer Windows-VM und Aktivieren der Sicherung)](https://azure.microsoft.com/resources/templates/recovery-services-create-vm-and-configure-backup/).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre VM geschützt haben, erfahren Sie, wie Sie diese verwalten und wiederherstellen können.

- [Verwalten und Überwachen virtueller Computer](backup-azure-manage-vms.md)
- [Wiederherstellen eines virtuellen Computers](backup-azure-arm-restore-vms.md)

Wenn Sie auf Probleme stoßen, [lesen](backup-azure-vms-troubleshoot.md) Sie den Leitfaden zur Problembehandlung.
