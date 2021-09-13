---
title: Problembehandlung bei Transaktionsprotokollen
titleSuffix: Azure SQL Database and Azure SQL Managed Instance
description: Hier finden Sie Schritte zur Problembehandlung bei Transaktionsprotokollen in Azure SQL-Datenbank oder Azure SQL Managed Instance
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 07/23/2021
ms.openlocfilehash: 0b9e47d23968c29a0c69a2da198bcf8d183f1bb0
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/26/2021
ms.locfileid: "114675110"
---
# <a name="troubleshooting-transaction-log-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>Problembehandlung von Transaktionsprotokollfehlern mit Azure SQL-Datenbank und Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Wenn das Transaktionsprotokoll voll ist und keine weiteren Transaktionen erfasst werden können, wird möglicherweise der Fehler 9002 oder 40552 angezeigt. Diese Fehler treten auf, wenn das Datenbanktransaktionsprotokoll, das von Azure SQL-Datenbank oder Azure SQL Managed Instance verwaltet wird, den Speicherplatzschwellenwert überschreitet und keine weiteren Transaktionen erfassen kann. 

Sie sind mit Problemen bei einem vollen Transaktionsprotokoll in SQL Server vergleichbar, müssen in Azure SQL-Datenbank oder Azure SQL Managed Instance jedoch anders behandelt werden.

> [!NOTE]
> **Dieser Artikel gilt für Azure SQL-Datenbank und Azure SQL Managed Instance.** Azure SQL-Datenbank und Azure SQL Managed Instance basieren auf der letzten stabilen Version der Microsoft SQL Server-Datenbank-Engine, weshalb sich die Inhalte größtenteils ähneln. Allerdings kann es Unterschiede bei den Optionen und Tools für die Problembehandlung geben. Weitere Informationen zur Problembehandlung beim Transaktionsprotokoll in SQL Server finden Sie unter [Problembehandlung bei vollen Transaktionsprotokollen (SQL Server-Fehler 9002)](/sql/relational-databases/logs/troubleshoot-a-full-transaction-log-sql-server-error-9002).

## <a name="automated-backups-and-the-transaction-log"></a>Automatisierte Sicherungen und das Transaktionsprotokoll

Bei Azure SQL-Datenbank und Azure SQL Managed Instance müssen einige wichtige Unterschiede in Bezug auf die Verwaltung des Dateispeicherplatzes der Datenbank berücksichtigt werden. 

- In Azure SQL-Datenbank und Azure SQL Managed Instance wird das Transaktionsprotokoll automatisch gesichert. Informationen zur Häufigkeit, Aufbewahrung und weitere Einzelheiten finden Sie unter [Automatisierte Sicherungen – Azure SQL-Datenbank und SQL Managed Instance](automated-backups-overview.md). 
- Da in Azure SQL-Datenbank auch freier Speicherplatz, die Datenbankdateivergrößerung und der Dateispeicherort verwaltet werden, unterscheiden sich die typischen Ursachen und Lösungen bei Problemen mit dem Transaktionsprotokoll von denen in SQL Server. 
- In Azure SQL Managed Instance können der Speicherort und der Name von Datenbankdateien nicht verwaltet werden. Administratoren können jedoch Einstellungen für Datenbankdateien und die automatische Vergrößerung von Dateien verwalten. Die typischen Ursachen und Lösungen bei Problemen mit dem Transaktionsprotokoll ähneln denen bei SQL Server. 

Ähnlich wie bei SQL Server wird das Transaktionsprotokoll für jede Datenbank abgeschnitten, wenn eine Protokollsicherung erstellt wird. Beim Abschneiden verbleibt leerer Speicherplatz in der Protokolldatei, der dann auf neue Transaktionen zugreifen kann. Wenn die Protokolldatei nicht durch Protokollsicherungen abgeschnitten werden kann, wird sie vergrößert, um neue Transaktionen erfassen zu können. Erreicht die Protokolldatei in Azure SQL-Datenbank oder Azure SQL Managed Instance die maximal zulässigen Grenzwerte, können keine neuen Transaktionen erfasst werden. Diese Situation tritt äußerst selten ein.

## <a name="prevented-transaction-log-truncation"></a>Verhindertes Abschneiden des Transaktionsprotokolls

Informationen dazu, was das Abschneiden des Protokolls in einem bestimmten Fall verhindert, finden Sie in `sys.databases` unter `log_reuse_wait_desc`. Der Wartevorgang für die Protokollwiederverwendung informiert Sie darüber, welche Bedingungen oder Ursachen verhindern, dass das Transaktionsprotokoll durch eine reguläre Protokollsicherung abgeschnitten wird. Weitere Informationen finden Sie unter [sys.databases &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql). 

```sql
SELECT [name], log_reuse_wait_desc FROM sys.databases;
```

Die folgenden Werte von `log_reuse_wait_desc` in `sys.databases` können Aufschluss über den Grund geben, weshalb das Abschneiden des Transaktionsprotokolls der Datenbank verhindert wird:

| log_reuse_wait_desc | Diagnosis | Reaktion erforderlich |
|--|--|--|
| **NOTHING** | Typischer Zustand. Das Abschneiden des Protokolls wird nicht verhindert. | Nein. |
| **CHECKPOINT** | Für das Abschneiden des Protokolls ist ein Prüfpunkt erforderlich. Selten. | Keine Reaktion erforderlich, es sei denn, der Zustand besteht dauerhaft. Hält der Zustand an, senden Sie eine Supportanfrage an den [Azure-Support](https://portal.azure.com/#create/Microsoft.Support). | 
| **LOG BACKUP** | Eine Protokollsicherung wird aktuell durchgeführt. | Keine Reaktion erforderlich, es sei denn, der Zustand besteht dauerhaft. Hält der Zustand an, senden Sie eine Supportanfrage an den [Azure-Support](https://portal.azure.com/#create/Microsoft.Support). | 
| **ACTIVE BACKUP OR RESTORE** | Eine Datenbanksicherung wird aktuell durchgeführt. | Keine Reaktion erforderlich, es sei denn, der Zustand besteht dauerhaft. Hält der Zustand an, senden Sie eine Supportanfrage an den [Azure-Support](https://portal.azure.com/#create/Microsoft.Support). | 
| **ACTIVE TRANSACTION** | Eine laufende Transaktion verhindert das Abschneiden des Protokolls. | Die Protokolldatei kann aufgrund von aktiven Transaktionen und/oder Transaktionen ohne Commit nicht abgeschnitten werden. Siehe nächsten Abschnitt.| 
| **REPLICATION** | In Azure SQL-Datenbank wahrscheinlich aufgrund des Features [Change Data Capture (CDC)](/sql/relational-databases/track-changes/about-change-data-capture-sql-server).<BR>In Azure SQL Managed Instance aufgrund einer [Replikation](../managed-instance/replication-transactional-overview.md) oder aufgrund von CDC. | In Azure SQL-Datenbank fragen Sie [sys.dm_cdc_errors](/sql/relational-databases/system-dynamic-management-views/change-data-capture-sys-dm-cdc-errors) ab, und behandeln Sie mögliche Fehler. Falls sich das Problem nicht lösen lässt, senden Sie eine Supportanfrage an den [Azure-Support](https://portal.azure.com/#create/Microsoft.Support).<BR>Falls der Zustand bei Azure SQL Managed Instance anhält, untersuchen Sie die Agents, die in CDC- oder Replikationsvorgänge involviert sind. Zur Problembehandlung bei CDC fragen Sie Aufträge in [msdb.dbo.cdc_jobs](/sql/relational-databases/system-tables/dbo-cdc-jobs-transact-sql) ab. Falls keine Aufträge vorhanden sind, fügen Sie sie über [sys.sp_cdc_add_job](/sql/relational-databases/system-stored-procedures/sys-sp-cdc-add-job-transact-sql) hinzu. Informationen zur Replikation finden Sie unter [Suchen nach Fehlern bei der Transaktionsreplikation](/sql/relational-databases/replication/troubleshoot-tran-repl-errors). Falls sich das Problem nicht lösen lässt, senden Sie eine Supportanfrage an den [Azure-Support](https://portal.azure.com/#create/Microsoft.Support). | 
| **AVAILABILITY_REPLICA** | Die Synchronisierung mit dem sekundären Replikat wird ausgeführt. | Keine Reaktion erforderlich, es sei denn, der Zustand besteht dauerhaft. Hält der Zustand an, senden Sie eine Supportanfrage an den [Azure-Support](https://portal.azure.com/#create/Microsoft.Support). | 

### <a name="log-truncation-prevented-by-an-active-transaction"></a>Abschneiden des Protokolls wird durch eine aktive Transaktion verhindert

Die häufigste Ursache für Transaktionsprotokolle, in denen keine neuen Transaktionen erfasst werden können, sind Transaktionen mit langer Ausführungszeit oder blockierte Transaktionen.

Führen Sie diese Beispielabfrage aus, um nach Transaktionen ohne Commit oder aktiven Transaktionen und deren Eigenschaften zu suchen.

- Gibt Informationen zu Transaktionseigenschaften aus [sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql) zurück.
- Gibt Sitzungsverbindungsinformationen aus [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) zurück.
- Gibt Anforderungsinformationen (für aktive Anforderungen) aus [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) zurück. Diese Abfrage kann auch verwendet werden, um blockierte Sitzungen zu ermitteln. Suchen Sie dazu nach `request_blocked_by`. Weitere Informationen zu Blockierungen finden Sie unter [Sammeln von Blockierungsinformationen](understand-resolve-blocking.md#gather-blocking-information). 
- Gibt den Text der aktuellen Anforderung oder den Eingabepuffertext unter Verwendung der DMV [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) oder [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) zurück. Wenn der im `text`-Feld von `sys.dm_exec_sql_text` zurückgegebene Wert NULL lautet, ist die Anforderung nicht aktiv, sondern weist eine ausstehende Transaktion auf. In diesem Fall enthält das `event_info`-Feld von `sys.dm_exec_input_buffer` die letzte an die Datenbank-Engine übergebene Befehlszeichenfolge. 

```sql
SELECT [database_name] = db_name(s.database_id)
, tat.transaction_id, tat.transaction_begin_time, tst.session_id 
, session_open_transaction_count = tst.open_transaction_count --uncommitted and unrolled back transactions open. 
, transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime())
, input_buffer = ib.event_info
, request_text = CASE  WHEN r.statement_start_offset = 0 and r.statement_end_offset= 0 THEN left(est.text, 4000)
                       ELSE    SUBSTRING ( est.[text],    r.statement_start_offset/2 + 1, 
                                           CASE WHEN r.statement_end_offset = -1 THEN LEN (CONVERT(nvarchar(max), est.[text])) 
                                                ELSE r.statement_end_offset/2 - r.statement_start_offset/2 + 1
                                           END  )  END
, request_status = r.status
, request_blocked_by = r.blocking_session_id
, transaction_state = CASE tat.transaction_state    
                     WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                     WHEN 1 THEN 'The transaction has been initialized but has not started.'
                     WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                     WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                     WHEN 4 THEN 'The commit process has been initiated on the distributed transaction. This is for distributed transactions only. The distributed transaction is still active but further processing cannot take place.'
                     WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                     WHEN 6 THEN 'The transaction has been committed.'
                     WHEN 7 THEN 'The transaction is being rolled back.'
                     WHEN 8 THEN 'The transaction has been rolled back.' END 
, transaction_name = tat.name
, azure_dtc_state    --Applies to: Azure SQL Database only
             =    CASE tat.dtc_state 
                 WHEN 1 THEN 'ACTIVE'
                 WHEN 2 THEN 'PREPARED'
                 WHEN 3 THEN 'COMMITTED'
                 WHEN 4 THEN 'ABORTED'
                 WHEN 5 THEN 'RECOVERED' END
, transaction_type = CASE tat.transaction_type    WHEN 1 THEN 'Read/write transaction'
                                             WHEN 2 THEN 'Read-only transaction'
                                             WHEN 3 THEN 'System transaction'
                                             WHEN 4 THEN 'Distributed transaction' END
, tst.is_user_transaction
, local_or_distributed = CASE tst.is_local WHEN 1 THEN 'Local transaction, not distributed' WHEN 0 THEN 'Distributed transaction or an enlisted bound session transaction.' END
, transaction_uow    --for distributed transactions. 
, s.login_time, s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
, session_cpu_time = s.cpu_time, session_logical_reads = s.logical_reads, session_reads = s.reads, session_writes = s.writes
, observed = sysdatetimeoffset()
FROM sys.dm_tran_active_transactions AS tat 
INNER JOIN sys.dm_tran_session_transactions AS tst  on tat.transaction_id = tst.transaction_id
INNER JOIN Sys.dm_exec_sessions AS s on s.session_id = tst.session_id 
LEFT OUTER JOIN sys.dm_exec_requests AS r on r.session_id = s.session_id
CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib 
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) AS est;
```


### <a name="file-management-to-free-more-space"></a>Dateiverwaltung zur Freigabe von Speicherplatz

Wenn das Transaktionsprotokoll nicht abgeschnitten werden kann, ist das Freigeben von zusätzlichem Speicherplatz in der Zuordnung von Datenbankdateien eine Möglichkeit, um Abhilfe zu schaffen. Allerdings muss trotzdem die Ursache behandelt werden, aufgrund derer das Abschneiden der Transaktionsprotokolldatei verhindert wird. 

In einigen Fällen kann eine Transaktion mit langer Ausführungszeit durch das vorübergehende Freigeben von zusätzlichem Speicherplatz abgeschlossen werden, sodass sich die Bedingung, aufgrund derer das Abschneiden der Transaktionsprotokolldatei verhindert wurde, mit einer normalen Transaktionsprotokollsicherung auflösen lässt. Das Freigeben von Speicherplatz in der Zuordnung schafft jedoch möglicherweise nur vorübergehend Abhilfe, bis das Transaktionsprotokoll wieder wächst. 

Weitere Informationen zum Verwalten des Dateispeicherplatzes von Datenbanken und Pools für elastische Datenbanken finden Sie unter [Verwalten von Dateispeicherplatz für Datenbanken in Azure SQL-Datenbank](file-space-manage.md).


### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Fehler 40552: Die Sitzung wurde aufgrund übermäßiger Belegung des Speicherplatzes für das Transaktionsprotokoll beendet

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Probieren Sie die folgenden Methoden aus, um dieses Problem zu beheben:

1. Das Problem kann aufgrund von Einfüge-, Aktualisierungs- oder Löschvorgängen auftreten. Überprüfen Sie die Transaktion, um unnötige Schreibvorgänge zu vermeiden. Versuchen Sie, die Anzahl der Zeilen zu verringern, die sofort ausgeführt werden, indem Sie Batchverarbeitung oder eine Aufteilung in mehrere kleinere Transaktionen implementieren. Weitere Informationen finden Sie unter [Gewusst wie: Verbessern der Leistung von SQL-Datenbankanwendungen mithilfe von Batchverarbeitung](../performance-improve-use-batching.md).
2. Das Problem kann aufgrund von Vorgängen zum Neuerstellen des Indexes auftreten. Es lässt sich vermeiden, indem Sie sicherstellen, dass die folgende Formel wahr ist: (Anzahl der in der Tabelle betroffenen Zeilen) multipliziert mit (durchschnittliche Größe des aktualisierten Felds in Byte + 80) < 2 GB. Bei großen Tabellen kann es sinnvoll sein, Partitionen zu erstellen und die Indexwartung nur für einige Partitionen der Tabelle durchzuführen. Weitere Informationen finden Sie unter [Erstellen partitionierter Tabellen und Indizes](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes?view=azuresqldb-current&preserve-view=true).
3. Versuchen Sie beim Durchführen von Masseneinfügungen mit dem Hilfsprogramm `bcp.exe` oder der `System.Data.SqlClient.SqlBulkCopy`-Klasse, die Option `-b batchsize` oder `BatchSize` zu verwenden, um die Anzahl der Zeilen zu beschränken, die bei jeder Transaktion auf den Server kopiert werden. Weitere Informationen finden Sie unter [bcp Utility](/sql/tools/bcp-utility).
4. Wenn Sie einen Index mit der `ALTER INDEX`-Anweisung neu erstellen, verwenden Sie die Optionen `SORT_IN_TEMPDB = ON` und `ONLINE = ON`. Weitere Informationen finden Sie unter [ALTER INDEX (Transact-SQL)](/sql/t-sql/statements/alter-index-transact-sql).

> [!NOTE]
> Weitere Informationen zu Ressource Governor-Fehlern finden Sie unter [Fehler bei der Ressourcenkontrolle](troubleshoot-common-errors-issues.md#resource-governance-errors).

## <a name="next-steps"></a>Nächste Schritte

- [Beheben von Konnektivitätsproblemen und anderen Fehlern mit Azure SQL-Datenbank und Azure SQL Managed Instance](troubleshoot-common-errors-issues.md)
- [Behandeln vorübergehender Verbindungsfehler in SQL-Datenbank und SQL Managed Instance](troubleshoot-common-connectivity-issues.md)


Informationen zur Größe des Transaktionsprotokolls finden Sie hier: 
- Informationen zu V-Kern-Ressourcenlimits für eine Einzeldatenbank finden Sie unter [Ressourcenlimits für Einzeldatenbanken, die das V-Kern-Kaufmodell verwenden](resource-limits-vcore-single-databases.md).
- Informationen zu V-Kern-Ressourcenlimits für Pools für elastische Datenbanken finden Sie unter [Ressourcenlimits für Pools für elastische Datenbanken, die das V-Kern-Kaufmodell verwenden](resource-limits-vcore-elastic-pools.md).
- Informationen zu DTU-Ressourcenlimits für einen Singleton finden Sie unter [Ressourcenlimits für Singletons, die das DTU-Kaufmodell verwenden](resource-limits-dtu-single-databases.md).
- Informationen zu DTU-Ressourcenlimits für Pools für elastische Datenbanken finden Sie unter [Ressourcenlimits für Pools für elastische Datenbanken, die das DTU-Kaufmodell verwenden](resource-limits-dtu-elastic-pools.md).
- Informationen zu Ressourcenlimits für SQL Managed Instance finden Sie unter [resource limits for SQL Managed Instance](../managed-instance/resource-limits.md) (Ressourcenlimits für SQL Managed Instance).

