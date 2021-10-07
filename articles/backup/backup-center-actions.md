---
title: Ausführen von Aktionen mithilfe von Backup Center
description: In diesem Artikel erfahren Sie, wie Sie Aktionen mithilfe von Backup Center ausführen.
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: e8b357c19caeb20c2a50d9a05f9d8d583bd8f640
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083618"
---
# <a name="perform-actions-using-backup-center"></a>Ausführen von Aktionen mithilfe von Backup Center

Backup Center ermöglicht das Ausführen wichtiger sicherungsbezogener Aktionen über eine zentrale Oberfläche, ohne zu einem einzelnen Tresor navigieren zu müssen. Mit Backup Center können unter anderem folgende Aktionen ausgeführt werden:

* Konfigurieren der Sicherung für Ihre Datenquellen
* Wiederherstellen einer Sicherungsinstanz
* Erstellen eines neuen Tresors
* Erstellen einer neuen Sicherungsrichtlinie
* Auslösen einer bedarfsgesteuerten Sicherung für eine Sicherungsinstanz
* Beenden der Sicherung für eine Sicherungsinstanz

## <a name="supported-scenarios"></a>Unterstützte Szenarien

* Backup Center wird derzeit für Sicherungen von Azure-VMs, SQL auf Azure-VMs, SAP HANA auf Azure-VMs sowie für Azure Files-Sicherungen und für Sicherungen von Azure Database for PostgreSQL-Servern unterstützt.
* Eine ausführliche Liste der unterstützten und nicht unterstützten Szenarien finden Sie in der [Supportmatrix](backup-center-support-matrix.md).

## <a name="configure-backup"></a>Konfigurieren der Sicherung

Wenn Sie Azure-VMs, SQL auf Azure-VMs, SAP HANA auf Azure-VMs oder Azure Files sichern, sollten Sie einen Recovery Services-Tresor verwenden. Wenn Sie Azure Database for PostgreSQL-Server sichern, sollten Sie einen Sicherungstresor verwenden. 

Führen Sie die entsprechenden Schritte für die Art der zu sichernden Datenquelle aus:

### <a name="configure-backup-to-a-recovery-services-vault"></a>Konfigurieren der Sicherung in einem Recovery Services-Tresor

1. Navigieren Sie zu Backup Center, und wählen Sie oben auf der Registerkarte **Übersicht** die Option **+ Sichern** aus.

    ![Backup Center-Übersicht](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. Wählen Sie den Typ der Datenquelle aus, die Sie sichern möchten.

    ![Auswählen der Datenquelle zum Konfigurieren einer VM-Sicherung](./media/backup-center-actions/backup-select-datasource-vm.png)

3. Wählen Sie einen Recovery Services-Tresor und anschließend **Fortfahren** aus. Daraufhin gelangen Sie zur gleichen Sicherungskonfiguration wie über einen Recovery Services-Tresor. Weitere Informationen zum Konfigurieren einer Sicherung für virtuelle Azure-Computer mit einem Recovery Services-Tresor finden Sie [hier](tutorial-backup-vm-at-scale.md).

### <a name="configure-backup-to-a-backup-vault"></a>Konfigurieren der Sicherung in einen Sicherungstresor

1. Navigieren Sie zu Backup Center, und wählen Sie oben auf der Registerkarte **Übersicht** die Option **+ Sichern** aus.
2. Wählen Sie die Art der zu sichernden Datenquelle aus (in diesem Fall „Azure Database for PostgreSQL-Server“).

    ![Auswählen der Datenquelle zum Konfigurieren einer Sicherung für Azure Database for PostgreSQL-Server](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. Wählen Sie **Proceed** (Fortfahren) aus. Daraufhin gelangen Sie zur gleichen Sicherungskonfiguration wie über einen Backup-Tresor. Weitere Informationen zum Konfigurieren einer Sicherung für Azure Database for PostgreSQL-Server mit einem Backup-Tresor finden Sie [hier](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases).

## <a name="restore-a-backup-instance"></a>Wiederherstellen einer Sicherungsinstanz

Führen Sie die entsprechenden Schritte für die Art der wiederherzustellenden Datenquelle aus:

### <a name="if-youre-restoring-from-a-recovery-services-vault"></a>Bei der Wiederherstellung aus einem Recovery Services-Tresor

1. Navigieren Sie zu Backup Center, und wählen Sie oben auf der Registerkarte **Übersicht** die Option **Wiederherstellen** aus.

    ![Backup Center-Übersicht für die VM-Wiederherstellung](./media/backup-center-actions/backup-center-overview-restore.png)

2. Wählen Sie den Typ der Datenquelle aus, die Sie wiederherstellen möchten.

    ![Auswählen der Datenquelle für die VM-Wiederherstellung](./media/backup-center-actions/restore-select-datasource-vm.png)

3. Wählen Sie eine Sicherungsinstanz und anschließend **Fortsetzen** aus. Daraufhin gelangen Sie zu den gleichen Wiederherstellungseinstellungen wie über einen Recovery Services-Tresor. Weitere Informationen zum Wiederherstellen eines virtuellen Azure-Computers mit einem Recovery Services-Tresor finden Sie [hier](backup-azure-arm-restore-vms.md#before-you-start).

### <a name="if-youre-restoring-from-a-backup-vault"></a>Bei der Wiederherstellung aus einem Sicherungstresor

1. Navigieren Sie zu Backup Center, und wählen Sie oben auf der Registerkarte **Übersicht** die Option **Wiederherstellen** aus.
2. Wählen Sie den Typ der wiederherzustellenden Datenquelle aus (in diesem Fall „Azure Database for PostgreSQL-Server“).

    ![Auswählen der Datenquelle für die Wiederherstellung eines Azure Database for PostgreSQL-Servers](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. Wählen Sie eine Sicherungsinstanz und anschließend **Fortsetzen** aus. Daraufhin gelangen Sie zu den gleichen Wiederherstellungseinstellungen wie über einen Recovery Services-Tresor. Weitere Informationen zum Wiederherstellen eines Azure Database for PostgreSQL-Servers mit einem Backup-Tresor finden Sie [hier](restore-azure-database-postgresql.md).

## <a name="create-a-new-vault"></a>Erstellen eines neuen Tresors

Sie können einen neuen Tresor erstellen, indem Sie zu Backup Center navigieren und oben auf der Registerkarte **Übersicht** die Option **+ Tresor** auswählen.

![Tresor erstellen](./media/backup-center-actions/backup-center-create-vault.png)

* Weitere Informationen zum Erstellen eines Recovery Services-Tresors finden Sie [hier](backup-create-rs-vault.md).
* Weitere Informationen zum Erstellen eines Backup-Tresors finden Sie [hier](backup-vault-overview.md).

## <a name="create-a-new-backup-policy"></a>Erstellen einer neuen Sicherungsrichtlinie

Führen Sie die entsprechenden Schritte für die Art der zu sichernden Datenquelle aus:

### <a name="if-youre-backing-up-to-a-recovery-services-vault"></a>Bei der Sicherung in einen Recovery Services-Tresor

1. Navigieren Sie zu Backup Center, und wählen Sie oben auf der Registerkarte **Übersicht** die Option **+ Richtlinie** aus.

    ![Backup Center-Übersicht für die Sicherungsrichtlinie](./media/backup-center-actions/backup-center-overview-policy.png)

2. Wählen Sie den Typ der Datenquelle aus, die Sie sichern möchten.

    ![Auswählen der Datenquelle für die Richtlinie für eine VM-Sicherung](./media/backup-center-actions/policy-select-datasource-vm.png)

3. Wählen Sie einen Recovery Services-Tresor und anschließend **Fortfahren** aus. Daraufhin gelangen Sie zur gleichen Richtlinienerstellung wie über einen Recovery Services-Tresor. Weitere Informationen zum Erstellen einer neuen Sicherungsrichtlinie für einen virtuellen Azure-Computer mit einem Recovery Services-Tresor finden Sie [hier](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

### <a name="if-youre-backing-up-to-a-backup-vault"></a>Bei der Sicherung in einen Sicherungstresor

1. Navigieren Sie zu Backup Center, und wählen Sie oben auf der Registerkarte **Übersicht** die Option **+ Richtlinie** aus.
2. Wählen Sie die Art der zu sichernden Datenquelle aus (in diesem Fall „Azure Database for PostgreSQL-Server“).

    ![Auswählen der Datenquelle für die Richtlinie für eine Sicherung eines Azure Database for PostgreSQL-Servers](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. Wählen Sie **Proceed** (Fortfahren) aus. Daraufhin gelangen Sie zur gleichen Richtlinienerstellung wie über einen Backup-Tresor. Weitere Informationen zum Erstellen einer neuen Sicherungsrichtlinie mit einem Backup-Tresor finden Sie [hier](backup-azure-database-postgresql.md#create-backup-policy).

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>Ausführen einer bedarfsgesteuerten Sicherung für eine Sicherungsinstanz

Mit Backup Center können Sie nach Sicherungsinstanzen in Ihrem gesamten Sicherungsbestand suchen und Sicherungsvorgänge nach Bedarf ausführen.

Navigieren Sie zum Backup Center, und wählen Sie das Menüelement **Sicherungsinstanzen** aus, um den Schutz zu beenden. Dadurch können Sie Details zu allen Sicherungsinstanzen anzeigen, auf die Sie Zugriff haben. Sie können nach der Sicherungsinstanz suchen, die Sie sichern möchten. Wenn Sie im Raster mit der rechten Maustaste auf ein Element klicken, wird eine Liste mit verfügbaren Aktionen geöffnet. Wählen Sie die Option **Jetzt sichern** aus, um eine bedarfsgesteuerte Sicherung auszuführen.

![Bedarfsgesteuerte Sicherung](./media/backup-center-actions/backup-center-on-demand-backup.png)

Weitere Informationen zum Ausführen bedarfsgesteuerter Sicherungen für virtuelle Azure-Computer finden Sie [hier](backup-azure-manage-vms.md#run-an-on-demand-backup).

Weitere Informationen zum Ausführen bedarfsgesteuerter Sicherungen für Azure Database for PostgreSQL-Server finden Sie [hier](backup-azure-database-postgresql.md).

## <a name="stop-backup-for-a-backup-instance"></a>Beenden der Sicherung für eine Sicherungsinstanz

Manchmal möchten Sie möglicherweise die Sicherung für eine Sicherungsinstanz beenden – etwa, wenn die zugrunde liegende Ressource nicht mehr vorhanden ist.

Navigieren Sie zum Backup Center, und wählen Sie das Menüelement **Sicherungsinstanzen** aus, um den Schutz zu beenden. Dadurch können Sie Details zu allen Sicherungsinstanzen anzeigen, auf die Sie Zugriff haben. Sie können nach der Sicherungsinstanz suchen, die Sie sichern möchten. Wenn Sie im Raster mit der rechten Maustaste auf ein Element klicken, wird eine Liste mit verfügbaren Aktionen geöffnet. Wählen Sie die Option **Sicherung beenden** aus, um die Sicherung für die Sicherungsinstanz zu beenden.

![Schutz beenden](./media/backup-center-actions/backup-center-stop-protection.png)

- Weitere Informationen zum Beenden der Sicherung für virtuelle Azure-Computer finden Sie [hier](backup-azure-manage-vms.md#stop-protecting-a-vm).
- Weitere Informationen zum Beenden der Sicherung für einen Datenträger finden Sie [hier](manage-azure-managed-disks.md#stop-protection-preview).
- Weitere Informationen zum Beenden der Sicherung für Azure Database for PostgreSQL-Server finden Sie [hier](manage-azure-database-postgresql.md#stop-protection-preview).

## <a name="resume-backup"></a>Sicherung fortsetzen

Sie können die Sicherung für Ihre Workloads fortsetzen, wenn Sie Daten während der Funktion **Schutz Beenden** beibehalten haben.

- Weitere Informationen über das Fortsetzen des Schutzes für Ihren Datenträger finden Sie [hier](manage-azure-database-postgresql.md#resume-protection).
- Weitere Informationen zum Fortsetzen des Schutzes für Azure Database for PostgreSQL finden Sie [hier](manage-azure-managed-disks.md#resume-protection).

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen und Betreiben von Sicherungen](backup-center-monitor-operate.md)
* [Steuern Ihres Sicherungsbestands](backup-center-govern-environment.md)
* [Gewinnen von Einblicken in Ihre Sicherungen](backup-center-obtain-insights.md)
