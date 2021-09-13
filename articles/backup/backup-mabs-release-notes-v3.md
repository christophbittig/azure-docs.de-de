---
title: Anmerkungen zu dieser Version für Microsoft Azure Backup Server v3
description: Dieser Artikel enthält Informationen zu bekannten Problemen und Problemumgehungen für Microsoft Azure Backup Server (MABS) v3.
ms.topic: conceptual
ms.date: 07/27/2021
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: b570442fb439c73886b23b07b39ecefb553c0ecf
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/27/2021
ms.locfileid: "114720746"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Anmerkungen zu dieser Version für Microsoft Azure Backup Server

Dieser Artikel enthält Informationen über die bekannten Probleme und Problemumgehungen für Microsoft Azure Backup Server (MABS) V3.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Sicherung und Wiederherstellung ist für gruppierte Workloads nicht möglich

**Beschreibung:** Beim Sichern/Wiederherstellen gruppierter Datenquellen wie z.B. Hyper-V-Cluster oder SQL-Cluster (SQL AlwaysOn) oder Exchange in einer Datenbankverfügbarkeitsgruppe (Database Availability Group, DAG) tritt nach dem Upgrade von MABS V2 auf MABS V3 ein Fehler auf.

**Problemumgehung:** Um dies zu verhindern, öffnen Sie SQL Server Management Studio (SSMS), und führen Sie das folgende SQL-Skript für die DPM-Datenbank aus:

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Beim Upgrade auf MABS V3 tritt im russischen Gebietsschema ein Fehler auf

**Beschreibung:** Beim Upgrade von MABS V2 auf MABS V3 tritt im russischen Gebietsschema ein Fehler mit dem Fehlercode **4387** auf.

**Problemumgehung:** Führen Sie zum Durchführen des Upgrades auf MABS V3 mithilfe des russischen Installationspakets die folgenden Schritte aus:

1. [Sichern](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server#SSMSProcedure) Sie Ihre SQL-Datenbank, und deinstallieren Sie MABS V2 (wählen Sie das Beibehalten der geschützten Daten während der Deinstallation aus).
2. Führen Sie ein Upgrade auf SQL 2017 (Enterprise) durch, und deinstallieren Sie die Berichterstellung als Teil des Upgrades.
3. [Installieren](/sql/reporting-services/install-windows/install-reporting-services#install-your-report-server) Sie SQL Server Reporting Services (SSRS).
4. [Installieren](/sql/ssms/download-sql-server-management-studio-ssms) Sie SQL Server Management Studio (SSMS).
5. Konfigurieren Sie die Berichterstellung mit den Parametern, wie in [SSRS-Konfiguration mit SQL 2017](./backup-azure-microsoft-azure-backup.md#upgrade-mabs) dokumentiert.
6. [Installieren](backup-azure-microsoft-azure-backup.md) Sie MABS V3.
7. Führen Sie das [Wiederherstellen](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms) von SQL mithilfe von SSMS durch, und führen Sie anschließend das DPM-Synchronisierungstool wie [hier](/system-center/dpm/back-up-the-dpm-server#using-dpmsync) beschrieben aus.
8. Aktualisieren Sie die Eigenschaft „DataBaseVersion“ in der dbo.tbl_DLS_GlobalSetting-Tabelle mit folgendem Befehl:

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. Starten Sie den MSDPM-Dienst.

## <a name="after-installing-ur1-the-mabs-reports-arent-updated-with-new-rdl-files"></a>Nach der Installation von UR1 werden die MABS-Berichte nicht mit neuen RDL-Dateien aktualisiert.

**Beschreibung**: Mit UR1 ist das Problem mit der Formatierung von MABS-Berichten mit aktualisierten RDL-Dateien behoben. Die neuen RDL-Dateien werden nicht automatisch durch vorhandene Dateien ersetzt.

>[!NOTE]
>Das Problem wurde in MABS v3 UR2 behoben.

**Problemumgehung**: Führen Sie die folgenden Schritte aus, um die RDL-Dateien zu ersetzen:

1. Rufen Sie auf dem MABS-Computer die URL für das SQL Reporting Services-Webportal auf.
1. Unter der Webportal-URL ist der Ordner „DPMReports“ im Format **`DPMReports_<GUID>`** vorhanden.

    >[!NOTE]
    >Es gibt immer nur einen Ordner mit dieser Namenskonvention. Wenn MABS von einer früheren Version aktualisiert wird, ist möglicherweise auch ein anderer älterer Ordner vorhanden, den Sie aber nicht öffnen können.

    ![Ordner „DPMReports“](./media/backup-mabs-release-notes-v3/dpm-reports-folder.png)

1. Wählen Sie den Ordner **`DPMReports_<GUID>`** aus, und öffnen Sie ihn. Die einzelnen Berichtsdateien werden wie unten dargestellt aufgelistet.

    ![Liste der einzelnen Berichtsdateien](./media/backup-mabs-release-notes-v3/individual-report-files.png)

1. Wählen Sie die Berichtsdateien aus, deren Name nicht auf **Report** endet, klicken Sie mit der rechten Maustaste auf **Option**, und wählen Sie **Verwalten** aus.

    ![Auswählen von „Verwalten“ für Berichtsdateien](./media/backup-mabs-release-notes-v3/manage-files.png)

1. Wählen Sie auf der neuen Seite die Option **Ersetzen** aus, um die Dateien durch die aktuellen Berichtsdateien zu ersetzen.

    Die aktuellen Berichtsdateien befinden sich unter dem Pfad `<MABS Installation Directory>\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`.

    Beispiel: `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    ![Ersetzen der Dateien durch die aktuellen Berichtsdateien](./media/backup-mabs-release-notes-v3/replace-files.png)

    Nachdem die Dateien ersetzt wurden, stellen Sie sicher, dass **Name** und **Beschreibung** korrekt und nicht leer sind.

1. Starten Sie nach dem Ersetzen der Dateien die MABS-Dienste neu, und verwenden Sie die Berichtsdateien.

## <a name="next-steps"></a>Nächste Schritte

[Neuerungen in MABS](backup-mabs-whats-new-mabs.md)
