---
title: Wiederherstellen von SQL Server-Datenbanken auf einem virtuellen Azure-Computer
description: In diesem Artikel erfahren Sie, wie Sie SQL Server-Datenbanken wiederherstellen, die auf einem virtuellen Azure-Computer ausgeführt und mit Azure Backup gesichert werden. Zum Wiederherstellen von Datenbanken in einer sekundären Region können Sie auch die regionsübergreifende Wiederherstellung verwenden.
ms.topic: conceptual
ms.date: 11/02/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 0e687cbae798a480eaf417de04b653b88d2e2c94
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131431110"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Wiederherstellen von SQL Server-Datenbanken auf virtuellen Azure-Computern

In diesem Artikel wird die Wiederherstellung einer SQL Server-Datenbank beschrieben, die auf einem virtuellen Azure-Computer ausgeführt wird und mithilfe des Diensts [Azure Backup](backup-overview.md) in einem Azure Backup Recovery Services-Tresor gesichert wurde.

Dieser Artikel beschreibt die Wiederherstellung von SQL Server-Datenbanken. Weitere Informationen finden Sie unter [Informationen zur SQL Server-Sicherung auf virtuellen Azure-Computern](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Wiederherstellen eines Zeitpunkts oder eines Wiederherstellungspunkts

Azure Backup kann auf virtuellen Azure-Computern ausgeführte SQL Server-Datenbanken wie folgt wiederherstellen:

- Wiederherstellung eines bestimmten Datums oder einer bestimmten Uhrzeit (sekundengenau) mithilfe von Transaktionsprotokollsicherungen. Azure Backup ermittelt automatisch die geeignete vollständige differenzielle Sicherung und die Kette von Protokollsicherungen, die für die Wiederherstellung Ihrer Daten basierend auf dem ausgewählten Zeitpunkt benötigt werden.
- Wiederherstellung einer bestimmten vollständigen oder differenziellen Sicherung, um die Daten eines bestimmten Wiederherstellungspunkts wiederherzustellen.

## <a name="restore-prerequisites"></a>Voraussetzungen für die Wiederherstellung

Beachten Sie vor dem Wiederherstellen einer Datenbank Folgendes:

- Sie können die Datenbank auf einer SQL Server-Instanz in derselben Azure-Region wiederherstellen.
- Der Zielserver muss bei demselben Tresor wie die Quelle registriert werden.
- Wenn auf einem Server mehrere Instanzen ausgeführt werden, sollten alle Instanzen aktiv sein und ausgeführt werden. Andernfalls wird der Server nicht in der Liste der Zielserver angezeigt, auf denen Sie die Datenbank wiederherstellen können. Weitere Informationen finden Sie in den [Schritten zur Problembehandlung](backup-sql-server-azure-troubleshoot.md#faulty-instance-in-a-vm-with-multiple-sql-server-instances).
- Wenn Sie eine mit TDE verschlüsselte Datenbank in einer anderen SQL Server-Instanz wiederherstellen möchten, müssen Sie zuerst [das Zertifikat auf dem Zielserver wiederherstellen](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server).
- [CDC](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server)-fähige Datenbanken sollten mithilfe der Option [Als Dateien wiederherstellen](#restore-as-files) wiederhergestellt werden.
- Starten Sie vor der Wiederherstellung der Masterdatenbank die SQL Server-Instanz mit der Startoption **-m AzureWorkloadBackup** im Einzelbenutzermodus.
  - Der Wert für **-m** ist der Name des Clients.
  - Die Verbindung kann nur vom angegebenen Clientnamen geöffnet werden.
- Beenden Sie für alle Systemdatenbanken („model“, „master“, „msdb“) vor dem Auslösen der Wiederherstellung den SQL Server-Agent-Dienst.
- Schließen Sie alle Anwendungen, die ggf. versuchen, eine Verbindung mit einer dieser Datenbanken zu verwenden.

## <a name="restore-a-database"></a>Wiederherstellen einer Datenbank

Zum Wiederherstellen benötigen Sie folgende Berechtigungen:

- Berechtigungen vom Typ **Sicherungsoperator** in dem Tresor, in dem Sie die Wiederherstellung durchführen.
- Zugriff **Mitwirkender (Schreiben)** auf die gesicherte Quell-VM.
- Zugriff **Mitwirkender (Schreiben)** auf die Ziel-VM:
  - Wenn Sie die Daten auf dem gleichen virtuellen Computer wiederherstellen, handelt es sich hierbei um den virtuellen Quellcomputer.
  - Wenn Sie als Ziel für die Wiederherstellung einen anderen Speicherort verwenden, handelt es sich hierbei um den neuen virtuellen Zielcomputer.

Die Wiederherstellung wird wie folgt durchgeführt:

1. Wechseln Sie im Azure-Portal zu **Backup Center**, und klicken Sie auf **Wiederherstellen**.

   :::image type="content" source="./media/backup-azure-sql-database/backup-center-restore-inline.png" alt-text="Screenshot des Startens des Wiederherstellungsvorgangs" lightbox="./media/backup-azure-sql-database/backup-center-restore-expanded.png":::

1. Wählen Sie **SQL in Azure-VM** als Datenquellentyp und anschließend eine Datenbank aus, die Sie wiederherstellen möchten, und klicken Sie dann auf **Weiter**.

   :::image type="content" source="./media/backup-azure-sql-database/sql-restore.png" alt-text="Screenshot der Auswahl des Datenquellentyps":::

1. Geben Sie unter **Wiederherstellungskonfiguration** an, wo (oder wie) die Daten wiederhergestellt werden sollen:
   
   - **Alternativer Standort:** Die Datenbank wird an einem alternativen Speicherort wiederhergestellt, und die ursprüngliche Quelldatenbank bleibt erhalten.
   - **Datenbank überschreiben:** Die Daten werden auf derselben SQL Server-Instanz wiederhergestellt, auf der sich auch die ursprüngliche Quelle befunden hat. Bei dieser Option wird die ursprüngliche Datenbank überschrieben.

        > [!IMPORTANT]
        > Wenn die ausgewählte Datenbank zu einer Always On-Verfügbarkeitsgruppe gehört, lässt SQL Server das Überschreiben der Datenbank nicht zu. Nur **Alternativer Speicherort** ist verfügbar.
        >
   - **Als Dateien wiederherstellen:** Anstelle einer Datenbank werden die Sicherungsdateien wiederhergestellt, die später mithilfe von SQL Server Management Studio als Datenbank auf jedem Computer wiederhergestellt werden können, auf dem die Dateien vorhanden sind.

### <a name="restore-to-an-alternate-location"></a>Wiederherstellen an einem alternativen Speicherort

1. Wählen Sie im Menü **Wiederherstellungskonfiguration** unter **Zielort der Wiederherstellung** die Option **Alternativer Speicherort** aus.
1. Wählen Sie den SQL Server-Namen und die Instanz aus, in der Sie die Datenbank wiederherstellen möchten.
1. Geben Sie im Feld **Name der wiederhergestellten Datenbank** den Namen der Zieldatenbank ein.
1. Aktivieren Sie ggf. **Überschreiben, wenn die gleichnamige Datenbank bereits in der ausgewählten SQL-Instanz vorhanden ist**.
1. Wählen Sie unter **Wiederherstellungspunkt** aus, ob Sie [einen bestimmten Zeitpunkt](#restore-to-a-specific-point-in-time) oder einen [bestimmten Wiederherstellungspunkt](#restore-to-a-specific-restore-point) wiederherstellen möchten.

   :::image type="content" source="./media/backup-azure-sql-database/sql-alternate-location-recovery.png" alt-text="Screenshot der Auswahl des Wiederherstellungspunkts":::

   :::image type="content" source="./media/backup-azure-sql-database/restore-points-sql-inline.png" alt-text="Screenshot der Wiederherstellung zu einem Zeitpunkt" lightbox="./media/backup-azure-sql-database/restore-points-sql-expanded.png":::

1. Gehen Sie im Menü **Erweiterte Konfiguration** wie folgt vor:

    - Aktivieren Sie **Mit NORECOVERY wiederherstellen**, wenn die Datenbank nach der Wiederherstellung nicht in Betrieb gehen soll.
    - Wenn Sie den Speicherort für die Wiederherstellung auf dem Zielserver ändern möchten, geben Sie neue Zielpfade ein.

        ![Zielpfade eingeben](./media/backup-azure-sql-database/target-paths.png)

1. Wählen Sie **OK** aus, um die Wiederherstellung auszulösen. Verfolgen Sie den Fortschritt der Wiederherstellung im Bereich **Benachrichtigungen** oder im Tresor in der Ansicht **Wiederherstellungsaufträge**.

    > [!NOTE]
    > Die Point-in-Time-Wiederherstellung ist nur für Transaktionsprotokollsicherungen für Datenbanken in einem vollständigen und massenprotokollierten Wiederherstellungsmodus verfügbar.

### <a name="restore-and-overwrite"></a>Wiederherstellen und Überschreiben

1. Wählen Sie im Menü **Wiederherstellungskonfiguration** unter **Zielort der Wiederherstellung** die Option **Datenbank überschreiben** > **OK** aus.

    ![Auswählen von „Datenbank überschreiben“](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. Wählen Sie unter **Wiederherstellungspunkt auswählen** die Option **Protokolle (Zeitpunkt)** aus, um [einen bestimmten Zeitpunkt wiederherzustellen](#restore-to-a-specific-point-in-time). Oder wählen Sie **Vollständig und differenziell** aus, um einen [bestimmten Wiederherstellungspunkt](#restore-to-a-specific-restore-point) wiederherzustellen.

    > [!NOTE]
    > Die Point-in-Time-Wiederherstellung ist nur für Transaktionsprotokollsicherungen für Datenbanken in einem vollständigen und massenprotokollierten Wiederherstellungsmodus verfügbar.

### <a name="restore-as-files"></a>Wiederherstellen als Dateien

Um die Sicherungsdaten als BAK-Dateien und nicht als Datenbank wiederherzustellen, wählen Sie **Als Dateien wiederherstellen** aus. Nachdem die Dateien in einem angegebenen Pfad gesichert wurden, können Sie diese Dateien auf jeden Computer verschieben, auf dem sie als Datenbank wiederhergestellt werden sollen. Da Sie diese Dateien auf einen beliebigen Computer verschieben können, können Sie nun die Daten abonnement- und regionsübergreifend wiederherstellen.

1. Wählen Sie unter **Ziel und Art der Wiederherstellung** die Option **Als Dateien wiederherstellen** aus.
1. Wählen Sie den Namen der SQL Server-Instanz aus, in der Sie die Sicherungsdateien wiederherstellen möchten.
1. Geben Sie unter **Zielpfad auf dem Server** den Ordnerpfad auf dem in Schritt 2 ausgewählten Server ein. Dies ist der Speicherort, an dem der Dienst alle erforderlichen Sicherungsdateien sichert. Normalerweise kann über einen Netzwerkfreigabepfad oder den Pfad einer eingebundenen Azure-Dateifreigabe, wenn dieser als Zielpfad angegeben ist, über andere Computer im selben Netzwerk oder mit derselben eingebundenen Azure-Dateifreigabe einfacher auf diese Dateien zugegriffen werden.<BR>

   >[!Note]
   >Um die Datenbank-Sicherungsdateien auf einer Azure-Dateifreigabe wiederherzustellen, die auf der registrierten Ziel-VM bereitgestellt ist. stellen Sie sicher, dass „NT AUTHORITY\SYSTEM“ auf die Dateifreigabe zugreifen kann. Sie können die unten aufgeführten Schritte ausführen, um die Lese-/Schreibberechtigungen für die auf dem virtuellen Computer bereitgestellten AFS zu erteilen:
   >
   >- Führen Sie `PsExec -s cmd` aus, um in die NT AUTHORITY\SYSTEM-Shell zu gelangen.
   >   - Führen Sie `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>` aus.
   >   - Überprüfen Sie den Zugriff mit `dir \\<storageacct>.file.core.windows.net\<filesharename>`.
   >- Starten Sie eine Wiederherstellung als Dateien aus dem Sicherungstresor unter dem Pfad `\\<storageacct>.file.core.windows.net\<filesharename>`.<BR>
   >„PsExec“ können Sie von der Seite [Sysinternals](/sysinternals/downloads/psexec) herunterladen.

1. Klicken Sie auf **OK**.

    ![Auswählen von „Wiederherstellen als Dateien“](./media/backup-azure-sql-database/restore-as-files.png)

1. Wählen Sie unter **Wiederherstellungspunkt** aus, ob Sie [einen bestimmten Zeitpunkt](#restore-to-a-specific-point-in-time) oder einen [bestimmten Wiederherstellungspunkt](#restore-to-a-specific-restore-point) wiederherstellen möchten.

1. Alle Sicherungsdateien, die dem ausgewählten Wiederherstellungspunkt zugeordnet sind, werden im Zielpfad gesichert. Sie können die Dateien mithilfe von SQL Server Management Studio als Datenbank auf jedem Computer wiederherstellen, auf dem sie vorhanden sind.

    ![Wiederhergestellte Sicherungsdateien im Zielpfad](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Wiederherstellen eines bestimmten Zeitpunkts

Wenn Sie **Protokolle (Zeitpunkt)** als Wiederherstellungstyp ausgewählt haben, gehen Sie folgendermaßen vor:

1. Öffnen Sie unter **Datum/Uhrzeit für Wiederherstellung** den Kalender. Im Kalender sind Tage, für die Wiederherstellungspunkte vorhanden sind, fett formatiert, und das aktuelle Datum ist hervorgehoben.
1. Wählen Sie ein Datum mit Wiederherstellungspunkten aus. Tage ohne Wiederherstellungspunkte können nicht ausgewählt werden.

    ![Öffnen des Kalenders](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Wenn Sie ein Datum ausgewählt haben, zeigt das Zeitachsendiagramm die verfügbaren Wiederherstellungspunkte in einem fortlaufenden Bereich an.
1. Geben Sie auf der Zeitleiste eine Uhrzeit für die Wiederherstellung an, oder wählen Sie eine Uhrzeit aus. Klicken Sie anschließend auf **OK**.

### <a name="restore-to-a-specific-restore-point"></a>Wiederherstellen eines bestimmten Wiederherstellungspunkts

Wenn Sie **Vollständig und differenziell** als Wiederherstellungstyp ausgewählt haben, gehen Sie folgendermaßen vor:

1. Wählen Sie aus der Liste einen Wiederherstellungspunkt aus, und wählen Sie **OK** aus, um die Prozedur der Wiederherstellungspunkte abzuschließen.

    ![Auswählen eines vollständigen Wiederherstellungspunkts](./media/backup-azure-sql-database/choose-full-recovery-point.png)

    >[!NOTE]
    > Standardmäßig werden die Wiederherstellungspunkte der letzten 30 Tage angezeigt. Sie können Wiederherstellungspunkte anzeigen, die älter als 30 Tage sind, indem Sie **Filter** und dann einen benutzerdefinierten Bereich auswählen.

### <a name="restore-databases-with-large-number-of-files"></a>Wiederherstellen von Datenbanken mit zahlreichen Dateien

Wenn die gesamte Zeichenfolgegröße von Dateien in einer Datenbank einen [bestimmten Grenzwert](backup-sql-server-azure-troubleshoot.md#size-limit-for-files) überschreitet, speichert Azure Backup die Liste mit den Datenbankdateien in einer anderen PIT-Komponente, sodass Sie den Wiederherstellungszielpfad während des Wiederherstellungsvorgangs nicht festlegen können. Die Dateien werden stattdessen am SQL-Standardpfad wiederhergestellt.

  ![Wiederherstellen einer Datenbank mit großer Datei](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="cross-region-restore"></a>Regionsübergreifende Wiederherstellung

Als eine der Wiederherstellungsoptionen ermöglicht die regionsübergreifende Wiederherstellung die Wiederherstellung von auf virtuellen Azure-Computern gehosteten SQL-Datenbanken in einer sekundären Region, bei der es sich um eine gekoppelte Azure-Region handelt.

Lesen Sie den Abschnitt [Bevor Sie beginnen](./backup-create-rs-vault.md#set-cross-region-restore), um das Onboarding für das Feature durchzuführen.

Um festzustellen, ob CRR aktiviert ist, befolgen Sie die Anweisungen unter [Konfigurieren der regionsübergreifenden Wiederherstellung](backup-create-rs-vault.md#configure-cross-region-restore).

### <a name="view-backup-items-in-secondary-region"></a>Anzeigen von Sicherungselementen in der sekundären Region

Wenn CRR aktiviert ist, können Sie die Sicherungselemente in der sekundären Region anzeigen.

1. Navigieren Sie im Portal zu **Recovery Services-Tresor** > **Sicherungselemente**.
1. Wählen Sie **Sekundäre Region** aus, um die Elemente in der sekundären Region anzuzeigen.

>[!NOTE]
>In der Liste werden nur Sicherungsverwaltungstypen angezeigt, die das CRR-Feature unterstützen. Derzeit ist nur die Unterstützung der Wiederherstellung von Daten aus sekundären Regionen in einer sekundären Region zulässig.

![Sicherungselemente in einer sekundären Region](./media/backup-azure-sql-database/backup-items-secondary-region.png)

![Datenbanken in einer sekundären Region](./media/backup-azure-sql-database/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>Wiederherstellen in der sekundären Region

Die Benutzeroberfläche zur Wiederherstellung in der sekundären Region ähnelt der Benutzerumgebung für die Wiederherstellung in der primären Region. Wenn Sie Details im Bereich „Wiederherstellungskonfiguration“ festlegen, um Ihre Wiederherstellung zu konfigurieren, werden Sie aufgefordert, nur die Parameter der sekundären Region anzugeben. In der sekundären Region sollte ein Tresor vorhanden sein, und die SQL Server-Instanz sollte beim Tresor in der sekundären Region registriert werden.

![Ziel und Art der Wiederherstellung](./media/backup-azure-sql-database/restore-secondary-region.png)

![Auslösen der Benachrichtigung „Wiederherstellung wird ausgeführt“](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>- Nachdem die Wiederherstellung ausgelöst wurde und sich in der Datenübertragungsphase befindet, kann der Wiederherstellungsauftrag nicht abgebrochen werden.
>- Für die regionsübergreifende Durchführung von Wiederherstellungsvorgängen sind die folgende Rolle und Zugriffsebene erforderlich: die Rolle _Sicherungsoperator_ im Abonnement und die Zugriffsberechtigung _Mitwirkender (Schreiben)_ für die virtuellen Quell- und Zielcomputer. Zum Anzeigen von Sicherungsaufträgen ist „Sicherungsleseberechtigter“ die minimale Berechtigung, die im Abonnement erforderlich ist.

### <a name="monitoring-secondary-region-restore-jobs"></a>Überwachen von Wiederherstellungsaufträgen für die sekundäre Regionen

1. Wechseln Sie im Azure-Portal zu **Backup Center** > **Sicherungsaufträge**.
1. Filtern Sie den Vorgang nach **CrossRegionRestore**, um die Aufträge in der sekundären Region anzuzeigen.

   :::image type="content" source="./media/backup-azure-sql-database/backup-center-jobs-inline.png" alt-text="Screenshot der gefilterten Sicherungsaufträge" lightbox="./media/backup-azure-sql-database/backup-center-jobs-expanded.png":::

## <a name="next-steps"></a>Nächste Schritte

[Verwalten und überwachen](manage-monitor-sql-database-backup.md) Sie SQL Server-Datenbanken, die mit Azure Backup gesichert wurden.
