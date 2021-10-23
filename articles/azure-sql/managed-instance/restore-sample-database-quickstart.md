---
title: 'Schnellstart: Wiederherstellen einer Sicherung (SSMS)'
titleSuffix: Azure SQL Managed Instance
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie eine Datenbanksicherung mithilfe von SQL Server Management Studio (SSMS) in Azure SQL Managed Instance wiederherstellen.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: misliplavo
ms.author: mlazic
ms.reviewer: mathoma
ms.date: 09/13/2021
ms.openlocfilehash: 3c8df890cb8772c919d70e790f7a2c62eb757e87
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041278"
---
# <a name="quickstart-restore-a-database-to-azure-sql-managed-instance-with-ssms"></a>Schnellstart: Wiederherstellen einer Datenbank in Azure SQL Managed Instance mit SSMS
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In dieser Schnellstartanleitung verwenden Sie SQL Server Management Studio (SSMS), um eine Datenbank (die Sicherungsdatei „Wide World Importers – Standard“) aus Azure Blob Storage in [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) wiederherzustellen.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Weitere Informationen zur Migration unter Verwendung des Azure Database Migration Services finden Sie unter [Tutorial: Migrieren eines SQL Servers zu einer verwalteten Azure Instanz mithilfe des Database Migration Services](../../dms/tutorial-sql-server-to-managed-instance.md).
> Weitere Informationen zu den verschiedenen Migrationsmethoden finden Sie in dem Leitfaden [Microsoft SQL Server zur Azure SQL Managed Instance](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).

## <a name="prerequisites"></a>Voraussetzungen

In dieser Schnellstartanleitung gilt Folgendes:

- Es werden Ressourcen aus der Schnellstartanleitung [Erstellen einer verwalteten Azure SQL-Instanz](instance-create-quickstart.md) verwendet.
- Die aktuelle Version von [SSMS](/sql/ssms/sql-server-management-studio-ssms) muss installiert sein.
- Die Verbindungsherstellung mit SQL Managed Instance muss über SSMS erfolgen. Informationen zum Herstellen einer Verbindung finden Sie in den folgenden Schnellstartanleitungen:
  - [Aktivieren eines öffentlichen Endpunkts](public-endpoint-configure.md) für SQL Managed Instance (wird für dieses Tutorial empfohlen)
  - [Verbinden mit SQL Managed Instance über einen virtuellen Azure-Computer](connect-vm-instance-configure.md)
  - [Konfigurieren einer Point-to-Site-Verbindung mit SQL Managed Instance über einen lokalen Computer](point-to-site-p2s-configure.md)

> [!NOTE]
> Weitere Informationen zum Sichern und Wiederherstellen einer SQL Server-Datenbank mithilfe von Azure Blob Storage und einem [SAS-Schlüssel (Shared Access Signature)](../../storage/common/storage-sas-overview.md) finden Sie unter [SQL Server-Sicherung über URLs](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="restore-from-a-backup-file-using-the-restore-wizard"></a>Wiederherstellen aus einer Sicherungsdatei mithilfe des Wiederherstellungs-Assistenten

Führen Sie in SSMS die folgenden Schritte aus, um die Wide World Importers-Datenbank mithilfe des Wiederherstellungs-Assistenten in SQL Managed Instance wiederherzustellen. Die Datenbanksicherungsdatei ist in einem vorkonfigurierten Azure Blob Storage-Konto gespeichert.

1. Öffnen Sie SSMS, und stellen Sie eine Verbindung mit Ihrer verwalteten Instanz her.
2. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf die Datenbanken der verwalteten Instanz, und wählen Sie **Datenbank wiederherstellen** aus, um den Wiederherstellungs-Assistenten zu öffnen.

    :::image type="content" source="./media/restore-sample-database-quickstart/restore-wizard-start.png" alt-text="Screenshot: Öffnen des Wiederherstellungs-Assistenten":::

3. Wählen Sie im neuen Wiederherstellungs-Assistenten die Auslassungspunkte ( **...** ) aus, um die Quelle der zu verwendenden Sicherungsdatei auszuwählen.

    :::image type="content" source="./media/restore-sample-database-quickstart/new-restore-wizard.png" alt-text="Screenshot: Öffnen eines neuen Fensters des Wiederherstellungs-Assistenten":::

4. Wählen Sie unter **Sicherungsmedien auswählen** die Option **Hinzufügen** aus. Unter **Sicherungsmedientyp** wird nur die Option **URL** angezeigt, da dies der einzige unterstützte Quelltyp ist. Klicken Sie auf **OK**.

    :::image type="content" source="./media/restore-sample-database-quickstart/restore-wizard-select-device.png" alt-text="Screenshot: Auswählen des Geräts":::

5. Unter **Speicherort für Sicherungsdatei auswählen** können Sie zwischen drei Optionen wählen, um Informationen zum Speicherort von Sicherungsdateien anzugeben:
    - Wählen Sie in der Dropdownliste einen vorab registrierten Speichercontainer aus.
    - Geben Sie einen neuen Speichercontainer und eine SAS (Shared Access Signature) ein. (Neue SQL-Anmeldeinformationen werden für Sie registriert.) 
    - Wählen Sie **Hinzufügen** aus, um weitere Speichercontainer aus Ihrem Azure-Abonnement zu durchsuchen.

    :::image type="content" source="./media/restore-sample-database-quickstart/restore-wizard-backup-file-location.png" alt-text="Screenshot: Auswählen des Speicherorts der Sicherungsdatei":::

    Führen Sie die nächsten Schritte aus, wenn Sie die Schaltfläche **Hinzufügen** auswählen. Wenn Sie den Speicherort der Sicherungsdatei mithilfe einer anderen Methode angeben, fahren Sie mit Schritt 12 fort.
6. Wählen Sie unter **Verbindung mit einem Microsoft-Abonnement herstellen** die Option **Anmelden** aus, um sich bei Ihrem Azure-Abonnement anzumelden:

    :::image type="content" source="./media/restore-sample-database-quickstart/restore-wizard-connect-subscription-sign-in.png" alt-text="Screenshot: Anmeldung beim Azure-Abonnement":::

7. Melden Sie sich bei Ihrem Microsoft-Konto an, um die Sitzung in Azure zu initiieren:

    :::image type="content" source="./media/restore-sample-database-quickstart/restore-wizard-sign-in-session.png" alt-text="Screenshot: Anmelden bei der Azure-Sitzung":::

8. Wählen Sie das Abonnement aus, in dem sich das Speicherkonto mit den Sicherungsdateien befindet:

    :::image type="content" source="./media/restore-sample-database-quickstart/restore-wizard-select-subscription.png" alt-text="Screenshot: Auswählen des Abonnements":::

9. Wählen Sie das Speicherkonto aus, in dem sich die Sicherungsdateien befinden:

    :::image type="content" source="./media/restore-sample-database-quickstart/restore-wizard-select-storage-account.png" alt-text="Screenshot: Speicherkonto":::

10. Wählen Sie den Blobcontainer aus, in dem sich die Sicherungsdateien befinden:

    :::image type="content" source="./media/restore-sample-database-quickstart/restore-wizard-select-container.png" alt-text="Auswählen des Blobcontainers":::

11. Geben Sie das Ablaufdatum der SAS-Richtlinie an, und wählen Sie **Anmeldeinformationen erstellen** aus. Eine SAS mit den richtigen Berechtigungen wird erstellt. Klicken Sie auf **OK**.

    :::image type="content" source="./media/restore-sample-database-quickstart/restore-wizard-generate-shared-access-signature.png" alt-text="Screenshot: Generieren der SAS":::

12. Erweitern Sie im linken Bereich die Ordnerstruktur, um den Ordner anzuzeigen, in dem sich die Sicherungsdateien befinden. Wählen Sie alle Sicherungsdateien aus, die mit dem wiederherzustellenden Sicherungssatz verknüpft sind, und wählen Sie dann **OK** aus:

    :::image type="content" source="./media/restore-sample-database-quickstart/restore-wizard-backup-file-selection.png" alt-text="Screenshot: Auswahl der Sicherungsdatei":::

    SSMS überprüft den Sicherungssatz. Der Vorgang dauert abhängig von der Größe des Sicherungssatzes bis zu einigen Sekunden.

13. Wurde die Sicherung überprüft, geben Sie den Namen der Zieldatenbank an, oder übernehmen Sie den Datenbanknamen des Sicherungssatzes, und klicken Sie dann auf **OK**:

    :::image type="content" source="./media/restore-sample-database-quickstart/restore-wizard-start-restore.png" alt-text="Screenshot: Starten der Wiederherstellung":::

    Die Wiederherstellung wird gestartet. Die Dauer hängt von der Größe des Sicherungssatzes ab.

    :::image type="content" source="./media/restore-sample-database-quickstart/restore-wizard-running-restore.png" alt-text="Screenshot: Ausführen der Wiederherstellung":::

14. Nach Abschluss der Wiederherstellung wird in einem Dialogfeld angezeigt, dass sie erfolgreich war. Klicken Sie auf **OK**.

    :::image type="content" source="./media/restore-sample-database-quickstart/restore-wizard-finish-restore.png" alt-text="Screenshot: Abgeschlossene Wiederherstellung":::

15. Überprüfen Sie die wiederhergestellte Datenbank im Objekt-Explorer:

    :::image type="content" source="./media/restore-sample-database-quickstart/restore-wizard-restored-database.png" alt-text="Screenshot: Wiederhergestellte Datenbank":::


## <a name="restore-from-a-backup-file-using-t-sql"></a>Wiederherstellen aus einer Sicherungsdatei mithilfe von T-SQL

Führen Sie in SQL Server Management Studio die folgenden Schritte aus, um die Wide World Importers-Datenbank in SQL Managed Instance wiederherzustellen. Die Datenbanksicherungsdatei ist in einem vorkonfigurierten Azure Blob Storage-Konto gespeichert.

1. Öffnen Sie SSMS, und stellen Sie eine Verbindung mit Ihrer verwalteten Instanz her.
2. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf Ihre verwaltete Instanz, und wählen Sie **Neue Abfrage** aus, um ein neues Abfragefenster zu öffnen.
3. Führen Sie das folgende SQL-Skript aus. Dieses Skript verwendet ein vorkonfiguriertes Speicherkonto und einen SAS-Schlüssel zum [Erstellen von Anmeldeinformationen](/sql/t-sql/statements/create-credential-transact-sql) in Ihrer verwalteten Instanz.
 
   > [!IMPORTANT]
   > `CREDENTIAL` muss dem Containerpfad entsprechen, mit `https` beginnen und darf nicht mit einem Schrägstrich enden. `IDENTITY` muss den Wert `SHARED ACCESS SIGNATURE` haben. `SECRET` muss das Shared Access Signature-Token sein und darf kein vorangestelltes `?` enthalten.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    :::image type="content" source="./media/restore-sample-database-quickstart/credential.png" alt-text="Erstellen von Anmeldeinformationen":::

4. Führen Sie zum Überprüfen Ihrer Anmeldeinformationen das folgende Skript aus. Dieses Skript verwendet eine [Container](https://azure.microsoft.com/services/container-instances/)-URL, um eine Sicherungsdateiliste abzurufen.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    :::image type="content" source="./media/restore-sample-database-quickstart/file-list.png" alt-text="Dateiliste":::

5. Führen Sie das folgende Skript aus, um die Wide World Importers-Datenbank wiederherzustellen.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    :::image type="content" source="./media/restore-sample-database-quickstart/restore.png" alt-text="Screenshot zeigt das Skript, das in Objekt-Explorer ausgeführt wird, mit einer Erfolgsmeldung.":::

6. Führen Sie das folgende Skript aus, um den Status Ihrer Wiederherstellung nachzuverfolgen.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Nachdem die Wiederherstellung abgeschlossen wurde, zeigen Sie die Datenbank im Objekt-Explorer an. Mithilfe der Ansicht [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) können Sie überprüfen, ob die Datenbankwiederherstellung abgeschlossen wurde.

> [!NOTE]
> Der Vorgang der Datenbankwiederherstellung ist asynchron und wiederholbar. Möglicherweise erhalten Sie eine Fehlermeldung in SQL Server Management Studio, falls die Verbindung unterbrochen oder ein Timeout überschritten wird. Azure SQL Managed Instance versucht weiterhin, die Datenbank im Hintergrund wiederherzustellen, und Sie können den Wiederherstellungsfortschritt mithilfe der Ansichten [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) und [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) nachverfolgen.
> In einigen Phasen des Wiederherstellungsprozesses wird ein eindeutiger Bezeichner anstelle des tatsächlichen Datenbanknamens in den Systemansichten angezeigt. Informationen zum unterschiedlichen Verhalten bei der `RESTORE`-Anweisung finden Sie [hier](./transact-sql-tsql-differences-sql-server.md#restore-statement).

## <a name="next-steps"></a>Nächste Schritte

- Wenn in Schritt 5 eine Datenbankwiederherstellung mit der Nachrichten-ID 22003 beendet wird, erstellen Sie eine neue Sicherungsdatei mit Sicherungsprüfsummen, und führen Sie die Wiederherstellung erneut durch. Weitere Informationen finden Sie unter [Aktivieren oder Deaktivieren von Sicherungsprüfsummen während der Sicherung oder Wiederherstellung](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).
- Informationen zum Beheben von Problemen bei URL-basierten Sicherungen finden Sie unter [SQL Server-Sicherung über URLs – bewährte Methoden und Problembehandlung](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Eine Übersicht über App-Verbindungsoptionen finden Sie unter [Herstellen einer Verbindung zwischen einer Anwendung und einer verwalteten SQL-Instanz](connect-application-instance.md).
- Informationen zu Abfragen mit Ihren bevorzugten Tools oder Sprachen finden Sie unter [Schnellstarts: Artikel zum Verbinden mit und Abfragen von Azure SQL-Datenbank und Azure SQL Managed Instance](../database/connect-query-content-reference-guide.md).
