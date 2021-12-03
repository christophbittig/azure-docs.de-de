---
title: Wiederherstellen von SAP HANA-Datenbanken auf virtuellen Azure-Computern
description: In diesem Artikel erfahren Sie, wie Sie SAP HANA-Datenbanken wiederherstellen, die in Azure Virtual Machines ausgeführt werden. Zum Wiederherstellen von Datenbanken in einer sekundären Region können Sie auch die regionsübergreifende Wiederherstellung verwenden.
ms.topic: conceptual
ms.date: 11/02/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 427dc637a0aa44ab6a0627b7a844ad6c7d3bb46e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131431455"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Wiederherstellen von SAP HANA-Datenbanken auf virtuellen Azure-Computern

In diesem Artikel wird die Wiederherstellung von SAP HANA-Datenbanken beschrieben, die auf einem virtuellen Azure-Computer (VM) ausgeführt werden und mithilfe des Diensts Azure Backup in einem Recovery Services-Tresor gesichert wurden. Mit Wiederherstellungen können Kopien der Daten für Entwicklungs-/Testszenarien erstellt oder Rücksetzungen auf einen früheren Zustand ausgeführt werden.

Weitere Informationen zum Sichern von SAP HANA-Datenbanken finden Sie unter [Sichern von SAP HANA-Datenbanken auf virtuellen Azure-Computern](./backup-azure-sap-hana-database.md).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Wiederherstellen eines Zeitpunkts oder eines Wiederherstellungspunkts

Azure Backup kann auf virtuellen Azure-Computern ausgeführte SAP HANA-Datenbanken wie folgt wiederherstellen:

* Wiederherstellung eines bestimmten Datums oder einer bestimmten Uhrzeit (sekundengenau) mithilfe von Protokollsicherungen. Azure Backup ermittelt automatisch die geeigneten vollständige differenziellen Sicherungen und die Kette von Protokollsicherungen, die für die Wiederherstellung Ihrer Daten basierend auf dem ausgewählten Zeitpunkt benötigt werden.

* Wiederherstellung einer bestimmten vollständigen oder differenziellen Sicherung, um die Daten eines bestimmten Wiederherstellungspunkts wiederherzustellen.

## <a name="prerequisites"></a>Voraussetzungen

Beachten Sie vor dem Wiederherstellen einer Datenbank Folgendes:

* Sie können die Datenbank nur in einer SAP HANA-Instanz wiederherstellen, die sich in derselben Region befindet.

* Die Zielinstanz muss beim gleichen Tresor wie die Quelle registriert sein.

* Mit Azure Backup ist es nicht möglich, zwei verschiedene SAP HANA-Instanzen auf derselben VM zu identifizieren. Deshalb ist das Wiederherstellen von Daten einer Instanz auf einer anderen auf derselben VM nicht möglich.

* Um sicherzustellen, dass die SAP HANA-Zielinstanz bereit für eine Wiederherstellung ist, überprüfen Sie ihren Status **Sicherungsbereitschaft**:

  1. Wechseln Sie im Azure-Portal zu **Backup Center**, und klicken Sie auf **+Sicherung**.

     :::image type="content" source="./media/sap-hana-db-restore/backup-center-configure-inline.png" alt-text="Screenshot, der zeigt, wie der Prozess gestartet wird, um zu überprüfen, ob die SAP HANA-Instanz für die Wiederherstellung bereit ist." lightbox="./media/sap-hana-db-restore/backup-center-configure-expanded.png":::

  1. Wählen Sie **SAP HANA in Azure-VM** als Datenquellentyp und anschließend den Tresor aus, bei dem die SAP HANA-Instanz registriert ist, und klicken Sie dann auf **Weiter**.

     :::image type="content" source="./media/sap-hana-db-restore/hana-select-vault.png" alt-text="Screenshot: Auswählen von SAP HANA auf einem virtuellen Azure-Computer.":::

  1. Wählen Sie unter **DBs in VMs ermitteln** die Option **Details anzeigen** aus.

     :::image type="content" source="./media/sap-hana-db-restore/hana-discover-databases.png" alt-text="Screenshot: Anzeigen der Datenbankdetails.":::

  1. Überprüfen Sie die **Sicherungsbereitschaft** der Ziel-VM.

     :::image type="content" source="./media/sap-hana-db-restore/hana-select-virtual-machines-inline.png" alt-text="Screenshot: Geschützte Server." lightbox="./media/sap-hana-db-restore/hana-select-virtual-machines-expanded.png":::

* Weitere Informationen zu den von SAP HANA unterstützten Wiederherstellungstypen finden Sie in SAP HANA-Hinweis [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Wiederherstellen einer Datenbank

Zum Wiederherstellen benötigen Sie folgende Berechtigungen:

* Berechtigungen vom Typ **Sicherungsoperator** in dem Tresor, in dem Sie die Wiederherstellung durchführen.
* Zugriff **Mitwirkender (Schreiben)** auf die gesicherte Quell-VM.
* Zugriff **Mitwirkender (Schreiben)** auf die Ziel-VM:
  * Wenn Sie die Daten auf dem gleichen virtuellen Computer wiederherstellen, handelt es sich hierbei um den virtuellen Quellcomputer.
  * Wenn Sie als Ziel für die Wiederherstellung einen anderen Speicherort verwenden, handelt es sich hierbei um den neuen virtuellen Zielcomputer.

1. Wechseln Sie im Azure-Portal zu **Backup Center**, und klicken Sie auf **Wiederherstellen**.

   :::image type="content" source="./media/sap-hana-db-restore/backup-center-restore-inline.png" alt-text="Screenshot: Starten der Wiederherstellung einer SAP HANA-Datenbank." lightbox="./media/sap-hana-db-restore/backup-center-restore-expanded.png":::

1. Wählen **SAP HANA in Azure-VM** als Datenquellentyp und anschließend die Datenbank aus, die Sie wiederherstellen möchten, und klicken Sie dann auf **Weiter**.

   :::image type="content" source="./media/sap-hana-db-restore/hana-restore-select-database.png" alt-text="Screenshot: Wiederherstellen der Sicherungselemente.":::

1. Geben Sie unter **Wiederherstellungskonfiguration** an, wo (oder wie) die Daten wiederhergestellt werden sollen:

   * **Alternativer Standort:** Die Datenbank wird an einem alternativen Speicherort wiederhergestellt, und die ursprüngliche Quelldatenbank bleibt erhalten.

   * **Datenbank überschreiben:** Die Daten werden auf derselben SAP HANA-Instanz wiederhergestellt, auf der sich auch die ursprüngliche Quelle befunden hat. Bei dieser Option wird die ursprüngliche Datenbank überschrieben.

   :::image type="content" source="./media/sap-hana-db-restore/hana-restore-configuration.png" alt-text="Screenshot: Wiederherstellen der Konfiguration.":::

### <a name="restore-to-alternate-location"></a>Wiederherstellen an einem alternativen Speicherort

1. Wählen Sie im Menü **Wiederherstellungskonfiguration** unter **Zielort der Wiederherstellung** die Option **Alternativer Speicherort** aus.

   :::image type="content" source="./media/sap-hana-db-restore/hana-alternate-location-recovery.png" alt-text="Screenshot: Wiederherstellung an einem alternativen Speicherort.":::

1. Wählen Sie den SAP HANA-Hostnamen und die Instanz aus, in der Sie die Datenbank wiederherstellen möchten.
1. Überprüfen Sie, ob die SAP HANA-Zielinstanz bereit für die Wiederherstellung ist, indem Sie ihre **Sicherungsbereitschaft** sicherstellen. Weitere Informationen finden Sie im Abschnitt zu [Voraussetzungen](#prerequisites).
1. Geben Sie im Feld **Name der wiederhergestellten Datenbank** den Namen der Zieldatenbank ein.

    > [!NOTE]
    > SDC-Wiederherstellungen (Single Database Container, Einzeldatenbank-Container) müssen diesen [Überprüfungen](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore) folgen.

1. Aktivieren Sie ggf. **Überschreiben, wenn die gleichnamige Datenbank bereits in der ausgewählten HANA-Instanz vorhanden ist**.

1. Wählen Sie unter **Wiederherstellungspunkt auswählen** die Option **Protokolle (Zeitpunkt)** aus, um [einen bestimmten Zeitpunkt wiederherzustellen](#restore-to-a-specific-point-in-time). Oder wählen Sie **Vollständig und differenziell** aus, um einen [bestimmten Wiederherstellungspunkt](#restore-to-a-specific-recovery-point) wiederherzustellen.

### <a name="restore-and-overwrite"></a>Wiederherstellen und Überschreiben

1. Wählen Sie im Menü **Wiederherstellungskonfiguration** unter **Zielort der Wiederherstellung** die Option **Datenbank überschreiben** > **OK** aus.

   :::image type="content" source="./media/sap-hana-db-restore/hana-overwrite-database.png" alt-text="Screenshot: Außerkraftsetzen der Datenbank.":::

1. Wählen Sie unter **Wiederherstellungspunkt auswählen** die Option **Protokolle (Zeitpunkt)** aus, um [einen bestimmten Zeitpunkt wiederherzustellen](#restore-to-a-specific-point-in-time). Oder wählen Sie **Vollständig und differenziell** aus, um einen [bestimmten Wiederherstellungspunkt](#restore-to-a-specific-recovery-point) wiederherzustellen.

### <a name="restore-as-files"></a>Wiederherstellen als Dateien

>[!Note]
>Wiederherstellen als Dateien funktioniert nicht bei CIFS-Freigaben, aber bei NFS.

Um die Sicherungsdaten als Dateien und nicht als Datenbank wiederherzustellen, wählen Sie **Als Dateien wiederherstellen** aus. Nachdem die Dateien in einem angegebenen Pfad gesichert wurden, können Sie diese Dateien auf jeden SAP HANA-Computer verschieben, auf dem sie als Datenbank wiederhergestellt werden sollen. Da Sie diese Dateien auf einen beliebigen Computer verschieben können, können Sie nun die Daten über Abonnements und Regionen hinweg wiederherstellen.

1. Wählen Sie im Menü **Wiederherstellungskonfiguration** unter **Ziel und Art der Wiederherstellung** die Option **Als Dateien wiederherstellen** aus.
1. Wählen Sie den Namen des **Hosts**/HANA-Servers aus, auf dem die Sicherungsdateien wiederhergestellt werden sollen.
1. Geben Sie unter **Zielpfad auf dem Server** den Ordnerpfad auf dem in Schritt 2 ausgewählten Server ein. Dies ist der Speicherort, an dem der Dienst alle erforderlichen Sicherungsdateien sichert.

    Gesicherte Dateien:

    * Datenbanksicherungsdateien
    * Katalogdateien
    * JSON-Metadatendateien (für jede betroffene Sicherungsdatei)

    Normalerweise kann über einen Netzwerkfreigabepfad oder den Pfad einer eingebundenen Azure-Dateifreigabe, wenn dieser als Zielpfad angegeben ist, über andere Computer im selben Netzwerk oder mit derselben eingebundenen Azure-Dateifreigabe einfacher auf diese Dateien zugegriffen werden.

    >[!NOTE]
    >Um die Datenbank-Sicherungsdateien auf einer Azure-Dateifreigabe wiederherzustellen, die auf der registrierten Ziel-VM bereitgestellt ist, stellen Sie sicher, dass das root-Konto über Lese-/Schreibberechtigungen für die Azure-Dateifreigabe verfügt.

   :::image type="content" source="./media/sap-hana-db-restore/hana-restore-as-files.png" alt-text="Screenshot: Auswählen des Zielpfads.":::

1. Wählen Sie den **Wiederherstellungspunkt** aus, an dem alle Sicherungsdateien und -ordner wiederhergestellt werden.

   :::image type="content" source="./media/sap-hana-db-restore/hana-select-recovery-point-inline.png" alt-text="Screenshot: Auswählen des Wiederherstellungspunkts." lightbox="./media/sap-hana-db-restore/hana-select-recovery-point-expanded.png":::

1. Alle Sicherungsdateien, die dem ausgewählten Wiederherstellungspunkt zugeordnet sind, werden im Zielpfad gesichert.
1. Basierend auf dem ausgewählten Typ von Wiederherstellungspunkt (**Point-in-Time** oder **Vollständig und differenziell**) sehen Sie einen oder mehrere im Zielpfad erstellte Ordner. Einer der Ordner mit dem Namen `Data_<date and time of restore>` enthält die vollständigen Sicherungen, während der andere Ordner mit dem Namen `Log` die Protokollsicherungen und sonstigen Sicherungen (z. B. differenziell und inkrementell) enthält.
1. Verschieben Sie diese wiederhergestellten Dateien auf den SAP HANA-Server, auf dem Sie sie als-Datenbank wiederherstellen möchten.
1. Führen Sie dann die folgenden Schritte durch:
    1. Legen Sie Berechtigungen für den Ordner/das Verzeichnis fest, indem die Sicherungsdateien gespeichert sind. Führen Sie dazu folgenden Befehl aus:

        ```bash
        chown -R <SID>adm:sapsys <directory>
        ```

    1. Führen Sie den nächsten Satz als `<SID>adm` aus.

        ```bash
        su - <sid>adm
        ```

    1. Generieren Sie die Katalogdatei für die Wiederherstellung. Extrahieren Sie die **BackupId** aus der JSON-Metadatendatei für die vollständige Sicherung, die später im Wiederherstellungsvorgang verwendet wird. Stellen Sie sicher, dass sich die vollständigen Sicherungen und die Protokollsicherungen in unterschiedlichen Ordnern befinden, und löschen Sie die Katalogdateien und die JSON-Metadatendateien in diesen Ordnern.

        ```bash
        hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
        ```

        Im obigen Befehl:

        * `<DataFileDir>` ist der Ordner mit den vollständigen Sicherungen.
        * `<LogFilesDir>` ist der Ordner, der die Protokollsicherungen, differenziellen und inkrementellen Sicherungen (falls vorhanden) enthält.
        * `<PathToPlaceCatalogFile>` ist der Ordner, in dem die generierte Katalogdatei platziert werden muss.

    1. Führen Sie die Wiederherstellung mit der neu generierten Katalogdatei über HANA Studio aus, oder führen Sie die HDBSQL-Wiederherstellungsabfrage mit diesem neu generierten Katalog aus. HDBSQL-Abfragen sind unten aufgelistet:

    * Wiederherstellen eines bestimmten Zeitpunkts:

        Wenn Sie eine neue wiederhergestellte Datenbank erstellen, führen Sie den HDBSQL-Befehl aus, um eine neue Datenbank `<DatabaseName>` zu erstellen, und beenden Sie die Datenbank anschließend für die Wiederherstellung. Wenn Sie jedoch nur eine vorhandene Datenbank wiederherstellen, führen Sie den HDBSQL-Befehl aus, um die Datenbank zu beenden.

        Führen Sie dann den folgenden Befehl aus, um die Datenbank wiederherzustellen:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` ist der Name der neuen oder vorhandenen Datenbank, die Sie wiederherstellen möchten.
        * `<Timestamp>` ist der genaue Zeitstempel der Point-in-Time-Wiederherstellung.
        * `<DatabaseName@HostName>` ist der Name der Datenbank, deren Sicherung für die Wiederherstellung verwendet wird, und der Name des **Hosts**/SAP HANA-Servers, auf dem sich diese Datenbank befindet. Die Option `USING SOURCE <DatabaseName@HostName>` gibt an, dass sich die Datensicherung (die für die Wiederherstellung verwendet wird) auf eine Datenbank mit einer anderen SID oder einem anderen Namen als der Ziel-SAP HANA-Computer bezieht. Daher muss sie nicht für Wiederherstellungen angegeben werden, die auf dem HANA-Server ausgeführt werden, auf dem die Sicherung aufgezeichnet wurde.
        * `<PathToGeneratedCatalogInStep3>` ist der Pfad zur Katalogdatei, die in **Schritt C** generiert wurde.
        * `<DataFileDir>` ist der Ordner mit den vollständigen Sicherungen.
        * `<LogFilesDir>` ist der Ordner, der die Protokollsicherungen, differenziellen und inkrementellen Sicherungen (falls vorhanden) enthält.
        * `<BackupIdFromJsonFile>` ist die **BackupId**, die in **Schritt C** extrahiert wurde.

    * Wiederherstellen einer bestimmten vollständigen oder differenziellen Sicherung:

        Wenn Sie eine neue wiederhergestellte Datenbank erstellen, führen Sie den HDBSQL-Befehl aus, um eine neue Datenbank `<DatabaseName>` zu erstellen, und beenden Sie die Datenbank anschließend für die Wiederherstellung. Wenn Sie jedoch nur eine vorhandene Datenbank wiederherstellen, führen Sie den HDBSQL-Befehl aus, um die Datenbank zu beenden:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` ist der Name der neuen oder vorhandenen Datenbank, die Sie wiederherstellen möchten.
        * `<Timestamp>` ist der genaue Zeitstempel der Point-in-Time-Wiederherstellung.
        * `<DatabaseName@HostName>` ist der Name der Datenbank, deren Sicherung für die Wiederherstellung verwendet wird, und der Name des **Hosts**/SAP HANA-Servers, auf dem sich diese Datenbank befindet. Die Option `USING SOURCE <DatabaseName@HostName>` gibt an, dass sich die Datensicherung (die für die Wiederherstellung verwendet wird) auf eine Datenbank mit einer anderen SID oder einem anderen Namen als der Ziel-SAP HANA-Computer bezieht. Daher muss sie nicht für Wiederherstellungen angegeben werden, die auf dem HANA-Server ausgeführt werden, auf dem die Sicherung aufgezeichnet wurde.
        * `<PathToGeneratedCatalogInStep3>` ist der Pfad zur Katalogdatei, die in **Schritt C** generiert wurde.
        * `<DataFileDir>` ist der Ordner mit den vollständigen Sicherungen.
        * `<LogFilesDir>` ist der Ordner, der die Protokollsicherungen, differenziellen und inkrementellen Sicherungen (falls vorhanden) enthält.
        * `<BackupIdFromJsonFile>` ist die **BackupId**, die in **Schritt C** extrahiert wurde.

### <a name="restore-to-a-specific-point-in-time"></a>Wiederherstellen eines bestimmten Zeitpunkts

Wenn Sie **Protokolle (Zeitpunkt)** als Wiederherstellungstyp ausgewählt haben, gehen Sie folgendermaßen vor:

1. Wählen Sie einen Wiederherstellungspunkt aus dem Protokolldiagramm aus, und klicken Sie auf **OK**, um den Wiederherstellungspunkt auszuwählen.

    ![Wiederherstellungspunkt](media/sap-hana-db-restore/restore-point.png)

1. Wählen Sie im Menü **Wiederherstellen** **Wiederherstellen** aus, um den Wiederherstellungsauftrag zu starten.

    ![Auswählen von „Wiederherstellen“](media/sap-hana-db-restore/restore-restore.png)

1. Verfolgen Sie den Fortschritt der Wiederherstellung im Bereich **Benachrichtigungen**, oder wählen Sie im Datenbankmenü die Option **Wiederherstellungsaufträge** aus.

    ![Wiederherstellung wurde erfolgreich ausgelöst](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Wiederherstellen eines bestimmten Wiederherstellungspunkts

Wenn Sie **Vollständig und differenziell** als Wiederherstellungstyp ausgewählt haben, gehen Sie folgendermaßen vor:

1. Wählen Sie einen Wiederherstellungspunkt aus der Liste aus, und klicken Sie auf **OK**, um den Wiederherstellungspunkt auszuwählen.

    ![Wiederherstellen eines bestimmten Wiederherstellungspunkts](media/sap-hana-db-restore/specific-recovery-point.png)

1. Wählen Sie im Menü **Wiederherstellen** **Wiederherstellen** aus, um den Wiederherstellungsauftrag zu starten.

    ![Starten des Wiederherstellungsauftrags](media/sap-hana-db-restore/restore-specific.png)

1. Verfolgen Sie den Fortschritt der Wiederherstellung im Bereich **Benachrichtigungen**, oder wählen Sie im Datenbankmenü die Option **Wiederherstellungsaufträge** aus.

    ![Status der Wiederherstellung](media/sap-hana-db-restore/restore-progress.png)

    > [!NOTE]
    > Bei MDC-Wiederherstellungen (Multiple Database Container, Container mit mehreren Datenbanken) muss das Vorregistrierungsskript erneut ausgeführt werden, nachdem die Systemdatenbank auf einer Zielinstanz wiederhergestellt wurde. Nur dann ist die nachfolgende Wiederherstellung der Mandantendatenbanken erfolgreich. Weitere Informationen finden Sie unter [Problembehandlung – MDC-Wiederherstellungen](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="cross-region-restore"></a>Regionsübergreifende Wiederherstellung

Als eine der Wiederherstellungsoptionen ermöglicht die regionsübergreifende Wiederherstellung die Wiederherstellung von auf virtuellen Azure-Computern gehosteten SAP HANA-Datenbanken in einer sekundären Region, bei der es sich um eine gekoppelte Azure-Region handelt.

Lesen Sie den Abschnitt [Bevor Sie beginnen](./backup-create-rs-vault.md#set-cross-region-restore), um das Onboarding durchzuführen.

Um festzustellen, ob CRR aktiviert ist, befolgen Sie die Anweisungen unter [Konfigurieren der regionsübergreifenden Wiederherstellung](backup-create-rs-vault.md#configure-cross-region-restore).

### <a name="view-backup-items-in-secondary-region"></a>Anzeigen von Sicherungselementen in der sekundären Region

Wenn CRR aktiviert ist, können Sie die Sicherungselemente in der sekundären Region anzeigen.

1. Navigieren Sie im Portal zu **Recovery Services-Tresor** > **Sicherungselemente**.
1. Wählen Sie **Sekundäre Region** aus, um die Elemente in der sekundären Region anzuzeigen.

>[!NOTE]
>In der Liste werden nur Sicherungsverwaltungstypen angezeigt, die das CRR-Feature unterstützen. Derzeit ist nur die Unterstützung der Wiederherstellung von Daten aus sekundären Regionen in einer sekundären Region zulässig.

![Sicherungselemente in einer sekundären Region](./media/sap-hana-db-restore/backup-items-secondary-region.png)

![Datenbanken in einer sekundären Region](./media/sap-hana-db-restore/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>Wiederherstellen in der sekundären Region

Die Benutzeroberfläche zur Wiederherstellung in der sekundären Region ähnelt der Benutzerumgebung für die Wiederherstellung in der primären Region. Wenn Sie Details im Bereich „Wiederherstellungskonfiguration“ festlegen, um Ihre Wiederherstellung zu konfigurieren, werden Sie aufgefordert, nur die Parameter der sekundären Region anzugeben. In der sekundären Region sollte ein Tresor vorhanden sein, und der SAP HANA-Server sollte beim Tresor in der sekundären Region registriert werden.

![Ziel und Art der Wiederherstellung](./media/sap-hana-db-restore/restore-secondary-region.png)

![Auslösen der Benachrichtigung „Wiederherstellung wird ausgeführt“](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>* Nachdem die Wiederherstellung ausgelöst wurde und sich in der Datenübertragungsphase befindet, kann der Wiederherstellungsauftrag nicht abgebrochen werden.
>* Für die regionsübergreifende Durchführung von Wiederherstellungsvorgängen sind die folgende Rolle und Zugriffsebene erforderlich: die Rolle _Sicherungsoperator_ im Abonnement und die Zugriffsberechtigung _Mitwirkender (Schreiben)_ für die virtuellen Quell- und Zielcomputer. Zum Anzeigen von Sicherungsaufträgen ist „Sicherungsleseberechtigter“ die minimale Berechtigung, die im Abonnement erforderlich ist.

### <a name="monitoring-secondary-region-restore-jobs"></a>Überwachen von Wiederherstellungsaufträgen für die sekundäre Regionen

1. Wechseln Sie im Azure-Portal zu **Backup Center** > **Sicherungsaufträge**.
1. Filtern Sie **Vorgang** nach dem Wert **CrossRegionRestore**, um die Aufträge in der sekundären Region anzuzeigen.

   :::image type="content" source="./media/sap-hana-db-restore/hana-view-jobs-inline.png" alt-text="Screenshot: Gefilterte Sicherungsaufträge." lightbox="./media/sap-hana-db-restore/hana-view-jobs-expanded.png":::

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie ](sap-hana-db-manage.md)mit Azure Backup gesicherte SAP HANA-Datenbanken verwalten.
