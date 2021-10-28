---
title: Verstehen und Beheben von Problemen durch Blockierungen in Azure SQL
titleSuffix: Azure SQL Database
description: Hier finden Sie eine Übersicht über Themen zu Blockierungen und deren Problembehandlung speziell für Azure SQL-Datenbank.
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 3/02/2021
ms.openlocfilehash: faf2d60162a7cd39b901414aedb8cda1406173f2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226929"
---
# <a name="understand-and-resolve-azure-sql-database-blocking-problems"></a>Verstehen und Beheben von Problemen durch Blockierungen in Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="objective"></a>Ziel

Dieser Artikel beschreibt Blockierungen in Azure SQL-Datenbanken und erläutert, wie diese behoben und aufgelöst werden. 

In diesem Artikel bezieht sich der Begriff „Verbindung“ auf eine einzelne angemeldete Sitzung der Datenbank. Jede Verbindung wird als Sitzungs-ID (SPID) bzw. in vielen DMVs (Dynamic Management Views, dynamische Verwaltungssichten) als „session_id“ angezeigt. Jede einzelne dieser SPIDs wird häufig als Prozess bezeichnet, obwohl es sich nicht um einen separaten Prozesskontext im üblichen Sinn handelt. Stattdessen besteht jede SPID aus den Serverressourcen und Datenstrukturen, die zum Verarbeiten der Anforderungen einer einzelnen Verbindung von einem bestimmten Client benötigt werden. Ein einzelne Clientanwendung kann über eine oder mehrere Verbindungen verfügen. Aus Sicht von Azure SQL-Datenbank gibt es keinen Unterschied zwischen mehreren Verbindungen von einer einzelnen Clientanwendung auf einem einzelnen Clientcomputer und mehreren Verbindungen von mehreren Clientanwendungen auf mehreren Clientcomputern: Die Verbindungen sind atomisch. Eine Verbindung kann eine andere Verbindung blockieren, unabhängig vom Quellclient.

> [!NOTE]
> **Der folgende Inhalt konzentriert sich auf Azure SQL-Datenbank.** Azure SQL-Datenbank basiert auf der letzten stabilen Version der Microsoft SQL Server-Datenbank-Engine, daher sind die Inhalte größtenteils gleich. Allerdings kann es Unterschiede bei den Optionen und Tools für die Problembehandlung geben. Weitere Informationen zu Blockierungen in SQL Server finden Sie unter [Verstehen und Beheben von Problemen mit Blockierungen in SQL Server](/troubleshoot/sql/performance/understand-resolve-blocking).

## <a name="understand-blocking"></a>Grundlegendes zu Blockierungen 
 
Blockierungen sind ein unvermeidbares und entwurfsbedingtes Merkmal von Managementsystemen für relationale Datenbanken (Relational Database Management Systems, RDBMSs) mit sperrbasierter Parallelität. Wie an anderer Stelle bereits erwähnt, tritt in SQL Server eine Blockierung auf, wenn eine Sitzung eine bestimmte Ressource gesperrt hat und eine zweite SPID versucht, für dieselbe Ressource einen in Konflikt stehenden Sperrtyp anzufordern. In der Regel ist der Zeitrahmen, in dem die erste SPID die Ressource sperrt, sehr klein. Wenn die Sitzung, die Besitzer der Ressource ist, die Sperre aufhebt, kann die zweite Verbindung die Ressource ihrerseits sperren und die Verarbeitung fortsetzen. Dies ist ein ganz normales Verhalten und tritt im Verlauf eines Tages sehr häufig auf, ohne dass die Systemleistung merklich beeinträchtigt wird.

Dauer und Transaktionskontext einer Abfrage bestimmen, wie lange die Sperren aufrechterhalten werden. Daraus ergeben sich die Auswirkungen auf andere Abfragen. Wenn die Abfrage nicht innerhalb einer Transaktion ausgeführt wird (und keine Sperrhinweise verwendet werden), werden die Sperren für SELECT-Anweisungen für eine Ressource nur zum Zeitpunkt des tatsächlichen Lesevorgangs aufrechterhalten, nicht während der Abfrage. Bei INSERT-, UPDATE- und DELETE-Anweisungen bleiben die Sperren während der gesamten Abfrage erhalten. Dies geschieht aus Gründen der Datenkonsistenz und um bei Bedarf einen Rollback der Abfrage zu ermöglichen.

Bei Abfragen, die innerhalb einer Transaktion ausgeführt werden, wird die Sperrdauer durch den Abfragetyp, die Isolationsstufe der Transaktion und das Vorhandensein von Sperrhinweisen in der Abfrage bestimmt. Beschreibungen von Sperren, Sperrhinweisen und Isolationsstufen für Transaktionen finden Sie in den folgenden Artikeln:

* [Sperren in der Datenbank-Engine](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [Anpassen von Sperren und Zeilenversionsverwaltung](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#customizing-locking-and-row-versioning)
* [Sperrmodi](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_modes)
* [Kompatibilität von Sperren](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_compatibility)
* [Transaktionen](/sql/t-sql/language-elements/transactions-transact-sql)

Wenn die Anzahl der Sperr- und Blockierungsvorgänge sich dauerhaft auf einem so hohen Niveau bewegt, dass die Systemleistung erheblich beeinträchtigt wird, ist dies auf eine der folgenden Ursachen zurückzuführen:

* Eine SPID sperrt eine Reihe von Ressourcen für einen längeren Zeitraum, bevor die Ressourcen freigegeben werden. Diese Art von Blockierung löst sich mit der Zeit von selbst, kann aber zu Leistungsbeeinträchtigungen führen.

* Eine SPID sperrt eine Reihe von Ressourcen und gibt sie nie frei. Diese Art von Blockierung löst sich nicht von selbst und verhindert für unbegrenzte Zeit den Zugriff auf die betroffenen Ressourcen.

Im ersten Szenario kann sich die Situation sehr schnell ändern, weil verschiedene SPIDs im Lauf der Zeit Blockierungen verschiedener Ressourcen verursachen. Dies erschwert die Problemlösung. In solchen Situationen ist die Problembehandlung mit [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) schwierig, da sich ein Problem kaum auf einzelne Abfragen eingrenzen lässt. Im Gegensatz dazu führt die zweite Situation zu einem konsistenten Zustand, der einfacher zu diagnostizieren ist.

## <a name="applications-and-blocking"></a>Anwendungen und Blockierungen

Bei Blockierproblemen besteht die Tendenz, sich zur Problemlösung auf serverseitige Optimierungen und Plattformprobleme zu konzentrieren. Eine reine Fokussierung auf die Datenbank führt aber möglicherweise nicht zu einer Lösung und kann Zeit und Energie kosten, die besser in die Untersuchung der Clientanwendung und der von ihr gesendeten Abfragen investiert werden sollte. Unabhängig davon, welches Maß an Transparenz die Anwendung hinsichtlich der ausgeführten Datenbankaufrufe bietet: Bei einem Blockierproblem müssen häufig sowohl die genauen von der Anwendung gesendeten SQL-Anweisungen als auch das genaue Anwendungsverhalten in Bezug auf den Abbruch von Abfragen, die Verbindungsverwaltung, den Abruf aller Ergebniszeilen usw. untersucht werden. Wenn das verwendete Entwicklungstool keine explizite Steuerung für Verbindungsverwaltung, Abfrageabbruch, Abfragetimeout, Ergebnisabruf usw. ermöglicht, sind Blockierprobleme möglicherweise unlösbar. Dieses Potenzial muss vor der Auswahl eines Anwendungsentwicklungstools für Azure SQL-Datenbank genauestens geprüft werden, insbesondere für leistungsrelevante OLTP-Umgebungen. 

Achten Sie während der Entwurfs- und Erstellungsphase von Datenbank und Anwendung auf die Datenbankleistung. Insbesondere sollten Ressourcenverbrauch, Isolationsstufe und Länge des Transaktionspfads für jede Abfrage ausgewertet werden. Jede Abfrage und jede Transaktion sollte so schlank wie möglich sein. Die Verbindungsverwaltung muss sorgfältig geplant und eingerichtet werden. Andernfalls kann es passieren, dass eine Anwendung bei geringen Benutzerzahlen anscheinend eine akzeptable Leistung zeigt, die Leistung aber bei steigenden Zahlen massiv sinkt. 

Mit einem guten Anwendungs- und Abfrageentwurf kann Azure SQL-Datenbank Tausende gleichzeitiger Benutzer auf einem einzelnen Server mit nur geringfügigen Blockierungen unterstützen.

> [!Note]
> Weitere Anleitungen zur Anwendungsentwicklung finden Sie unter [Beheben von Konnektivitätsproblemen und anderen Fehlern mit Azure SQL-Datenbank und Azure SQL Managed Instance](troubleshoot-common-errors-issues.md) und [Beheben von vorübergehenden Fehlern](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling).

## <a name="troubleshoot-blocking"></a>Problembehandlung bei Blockierungen

Unabhängig von der Blockierungssituation ist die Methode für die Problembehandlung dieselbe. Diese logischen Trennungen bestimmen die verbleibenden Abschnitte dieses Artikels. Das Konzept besteht darin, den Anfang der Blockierung zu finden und zu ermitteln, was die Abfrage tut und warum sie blockiert wird. Sobald die problematische Abfrage (also die Abfrage, die Sperren über einen längeren Zeitraum aufrechterhält) identifiziert wurde, muss im nächsten Schritt analysiert und bestimmt werden, warum die Blockierung auftritt. Nachdem wir das Warum verstanden haben, können wir die Abfrage und die Transaktion neu entwerfen.

Schritte bei der Problembehandlung:

1. Identifizieren der wesentlichen blockierenden Sitzung (Anfang der Blockierung)

2. Suchen der Abfrage bzw. Transaktion, die die Blockierung verursacht (also Sperren über einen längeren Zeitraum aufrechterhält)

3. Analysieren der Ursache für die längere Blockierung

4. Lösen des Blockierproblems durch erneutes Entwerfen der Abfrage und Transaktion

Im Folgenden wird erläutert, wie Sie die wesentliche blockierende Sitzung mithilfe einer geeigneten Datenerfassung genau lokalisieren.

## <a name="gather-blocking-information"></a>Sammeln von Informationen zur Blockierung

Um die Behebung von Problemen durch Blockierungen zu vereinfachen, kann ein Datenbankadministrator SQL-Skripts verwenden, die den Zustand von Sperren und Blockierungen in der Azure SQL-Datenbank konstant überwachen. Zum Erfassen dieser Daten gibt es im Wesentlichen zwei Methoden. 

Die erste besteht darin, dynamische Verwaltungsobjekte (Dynamic Management Objects, DMOs) abzufragen und die Ergebnisse für einen Vergleich im Zeitverlauf zu speichern. Einige der in diesem Artikel beschriebenen Objekte sind dynamische Verwaltungssichten (Dynamic Management Views, DMVs), einige sind dynamische Verwaltungsfunktionen (Dynamic Management Functions, DMFs). In der zweiten Methode werden XEvents verwendet, um die ausgeführten Vorgänge aufzuzeichnen. 


## <a name="gather-information-from-dmvs"></a>Sammeln von Informationen aus DMVs

Durch Verweisen auf DMVs lassen sich die SPID am Anfang der blockierenden Kette und die SQL-Anweisung identifizieren. Suchen Sie nach SPIDs, die blockiert werden. Wenn eine SPID durch eine andere SPID blockiert wird, untersuchen Sie die SPID, die Besitzer der Ressource ist (die blockierende SPID). Wird diese Besitzer-SPID ebenfalls blockiert? Durchlaufen Sie die Kette, um den Anfang der Blockierung zu finden, und untersuchen Sie, warum die Sperre beibehalten wird.

Denken Sie daran, jedes dieser Skripts in der Azure SQL-Zieldatenbank auszuführen.

* Die Befehle „sp_who“ und „sp_who2“ sind ältere Befehle, mit denen Sie alle aktuellen Sitzungen anzeigen. Die DMV `sys.dm_exec_sessions` gibt mehr Daten in einem Resultset zurück, das sich einfacher abfragen und filtern lässt. `sys.dm_exec_sessions` ist auch ein wesentlicher Bestandteil anderer Abfragen. 

* Wenn Sie bereits eine bestimmte Sitzung identifiziert haben, können Sie mithilfe von `DBCC INPUTBUFFER(<session_id>)` die letzte Anweisung suchen, die von dieser Sitzung übermittelt wurde. Ähnliche Ergebnisse können durch die DMF `sys.dm_exec_input_buffer` in einem Resultset zurückgegeben werden, das sich durch Angabe von „session_id“ und „request_id“ einfacher abfragen und filtern lässt. Mit folgender Anweisung können Sie beispielsweise die letzte von „session_id 66“ und „request_id 0“ übermittelte Abfrage zurückgeben:

```sql
SELECT * FROM sys.dm_exec_input_buffer (66,0);
```

* Weitere Informationen finden Sie in der `blocking_session_id`-Spalte in `sys.dm_exec_requests`. Wenn `blocking_session_id` = 0 wird eine Sitzung nicht blockiert. Während `sys.dm_exec_requests` nur aktuell ausgeführte Anforderungen auflistet, wird in `sys.dm_exec_sessions` keine Verbindung (aktiv oder nicht) aufgelistet. In der nächsten Abfrage können wir auf dieser allgemeinen Verknüpfung zwischen `sys.dm_exec_requests` und `sys.dm_exec_sessions` aufbauen.

* Führen Sie diese Beispielabfrage aus, um mithilfe der DMVs [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) oder [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) die aktiv ausgeführten Abfragen und ihren zugehörigen aktuellen SQL-Batchtext oder Eingabepuffertext zu finden. Wenn die vom `text`-Feld von `sys.dm_exec_sql_text` zurückgegebenen Daten NULL lauten, wird die Abfrage derzeit nicht ausgeführt. In diesem Fall enthält das `event_info`-Feld von `sys.dm_exec_input_buffer` die letzte an die SQL-Engine übergebene Befehlszeichenfolge. Mit dieser Abfrage können darüber hinaus Sitzungen identifiziert werden, die andere Sitzungen blockieren. Dabei wird eine nach Sitzungs-ID (session_id) aufgeschlüsselte Liste mit blockierten Sitzungs-IDs (session_ids) zurückgegeben. 

```sql
WITH cteBL (session_id, blocking_these) AS 
(SELECT s.session_id, blocking_these = x.blocking_these FROM sys.dm_exec_sessions s 
CROSS APPLY    (SELECT isnull(convert(varchar(6), er.session_id),'') + ', '  
                FROM sys.dm_exec_requests as er
                WHERE er.blocking_session_id = isnull(s.session_id ,0)
                AND er.blocking_session_id <> 0
                FOR XML PATH('') ) AS x (blocking_these)
)
SELECT s.session_id, blocked_by = r.blocking_session_id, bl.blocking_these
, batch_text = t.text, input_buffer = ib.event_info, * 
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
INNER JOIN cteBL as bl on s.session_id = bl.session_id
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) t
OUTER APPLY sys.dm_exec_input_buffer(s.session_id, NULL) AS ib
WHERE blocking_these is not null or r.blocking_session_id > 0
ORDER BY len(bl.blocking_these) desc, r.blocking_session_id desc, r.session_id;
```

* Führen Sie diese vom Microsoft-Support bereitgestellte ausführlichere Beispielabfrage aus, um den Kopfteil einer blockierenden Kette mit mehreren Sitzungen zu identifizieren, einschließlich des Abfragetexts der Sitzungen, die an einer blockierenden Kette beteiligt sind.

```sql
WITH cteHead ( session_id,request_id,wait_type,wait_resource,last_wait_type,is_user_process,request_cpu_time
,request_logical_reads,request_reads,request_writes,wait_time,blocking_session_id,memory_usage
,session_cpu_time,session_reads,session_writes,session_logical_reads
,percent_complete,est_completion_time,request_start_time,request_status,command
,plan_handle,sql_handle,statement_start_offset,statement_end_offset,most_recent_sql_handle
,session_status,group_id,query_hash,query_plan_hash) 
AS ( SELECT sess.session_id, req.request_id, LEFT (ISNULL (req.wait_type, ''), 50) AS 'wait_type'
    , LEFT (ISNULL (req.wait_resource, ''), 40) AS 'wait_resource', LEFT (req.last_wait_type, 50) AS 'last_wait_type'
    , sess.is_user_process, req.cpu_time AS 'request_cpu_time', req.logical_reads AS 'request_logical_reads'
    , req.reads AS 'request_reads', req.writes AS 'request_writes', req.wait_time, req.blocking_session_id,sess.memory_usage
    , sess.cpu_time AS 'session_cpu_time', sess.reads AS 'session_reads', sess.writes AS 'session_writes', sess.logical_reads AS 'session_logical_reads'
    , CONVERT (decimal(5,2), req.percent_complete) AS 'percent_complete', req.estimated_completion_time AS 'est_completion_time'
    , req.start_time AS 'request_start_time', LEFT (req.status, 15) AS 'request_status', req.command
    , req.plan_handle, req.[sql_handle], req.statement_start_offset, req.statement_end_offset, conn.most_recent_sql_handle
    , LEFT (sess.status, 15) AS 'session_status', sess.group_id, req.query_hash, req.query_plan_hash
    FROM sys.dm_exec_sessions AS sess
    LEFT OUTER JOIN sys.dm_exec_requests AS req ON sess.session_id = req.session_id
    LEFT OUTER JOIN sys.dm_exec_connections AS conn on conn.session_id = sess.session_id 
    )
, cteBlockingHierarchy (head_blocker_session_id, session_id, blocking_session_id, wait_type, wait_duration_ms,
wait_resource, statement_start_offset, statement_end_offset, plan_handle, sql_handle, most_recent_sql_handle, [Level])
AS ( SELECT head.session_id AS head_blocker_session_id, head.session_id AS session_id, head.blocking_session_id
    , head.wait_type, head.wait_time, head.wait_resource, head.statement_start_offset, head.statement_end_offset
    , head.plan_handle, head.sql_handle, head.most_recent_sql_handle, 0 AS [Level]
    FROM cteHead AS head
    WHERE (head.blocking_session_id IS NULL OR head.blocking_session_id = 0)
    AND head.session_id IN (SELECT DISTINCT blocking_session_id FROM cteHead WHERE blocking_session_id != 0)
    UNION ALL
    SELECT h.head_blocker_session_id, blocked.session_id, blocked.blocking_session_id, blocked.wait_type,
    blocked.wait_time, blocked.wait_resource, h.statement_start_offset, h.statement_end_offset,
    h.plan_handle, h.sql_handle, h.most_recent_sql_handle, [Level] + 1
    FROM cteHead AS blocked
    INNER JOIN cteBlockingHierarchy AS h ON h.session_id = blocked.blocking_session_id and h.session_id!=blocked.session_id --avoid infinite recursion for latch type of blocking
    WHERE h.wait_type COLLATE Latin1_General_BIN NOT IN ('EXCHANGE', 'CXPACKET') or h.wait_type is null
    )
SELECT bh.*, txt.text AS blocker_query_or_most_recent_query 
FROM cteBlockingHierarchy AS bh 
OUTER APPLY sys.dm_exec_sql_text (ISNULL ([sql_handle], most_recent_sql_handle)) AS txt;
```

* Zum Erfassen von Transaktionen mit langer Ausführungszeit oder ohne Commit verwenden Sie eine andere Gruppe von DMVs, mit denen Sie derzeit geöffnete Transaktionen anzeigen können: [sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql), [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql), [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql) und sys.dm_exec_sql_text. Es gibt eine Reihe von DMVs für die Nachverfolgung von Transaktionen. Weitere Informationen finden Sie unter [DMVs für Transaktionen](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql). 

```sql
SELECT [s_tst].[session_id],
[database_name] = DB_NAME (s_tdt.database_id),
[s_tdt].[database_transaction_begin_time], 
[sql_text] = [s_est].[text] 
FROM sys.dm_tran_database_transactions [s_tdt]
INNER JOIN sys.dm_tran_session_transactions [s_tst] ON [s_tst].[transaction_id] = [s_tdt].[transaction_id]
INNER JOIN sys.dm_exec_connections [s_ec] ON [s_ec].[session_id] = [s_tst].[session_id]
CROSS APPLY sys.dm_exec_sql_text ([s_ec].[most_recent_sql_handle]) AS [s_est];
```

* Verweisen Sie auf [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) auf der Thread-/Taskebene von SQL. Damit werden Informationen zum SQL-Wartetyp zurückgegeben, der aktuell für die Anforderung gilt. Ebenso wie `sys.dm_exec_requests` gibt `sys.dm_os_waiting_tasks` nur aktive Anforderungen zurück. 

> [!Note]
> Weitere Informationen zu den Wartetypen, einschließlich aggregierter Wartestatistiken im Zeitverlauf finden Sie in der DMV [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database). Diese DMV gibt aggregierte Wartestatistiken nur für die aktuelle Datenbank zurück.

* Verwenden Sie die DMV [sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql), um detaillierte Informationen zu den Sperren zu erhalten, die von Abfragen platziert wurden. Diese DMV kann auf einer SQL Server-Instanz in der Produktion große Datenmengen zurückgeben und hilft bei der Diagnose, welche Sperren zurzeit bestehen. 

Aufgrund des INNER JOIN-Vorgangs in `sys.dm_os_waiting_tasks`, beschränkt die folgende Abfrage die Ausgabe von `sys.dm_tran_locks` auf aktuell blockierte Anforderungen, ihren Wartestatus und ihre Sperren:

```sql
SELECT table_name = schema_name(o.schema_id) + '.' + o.name
, wt.wait_duration_ms, wt.wait_type, wt.blocking_session_id, wt.resource_description
, tm.resource_type, tm.request_status, tm.request_mode, tm.request_session_id
FROM sys.dm_tran_locks AS tm
INNER JOIN sys.dm_os_waiting_tasks as wt ON tm.lock_owner_address = wt.resource_address
LEFT OUTER JOIN sys.partitions AS p on p.hobt_id = tm.resource_associated_entity_id
LEFT OUTER JOIN sys.objects o on o.object_id = p.object_id or tm.resource_associated_entity_id = o.object_id
WHERE resource_database_id = DB_ID()
AND object_name(p.object_id) = '<table_name>';
```

* Mit DMVs werden durch Speichern der Abfrageergebnisse über einen längeren Zeitraum Datenpunkte bereitgestellt, mit denen Sie Blockierungen in einem bestimmten Zeitintervall überprüfen können, um persistente Blockierungen oder Trends zu ermitteln. 

## <a name="gather-information-from-extended-events"></a>Sammeln von Informationen aus erweiterten Ereignissen

Zusätzlich zu den vorherigen Informationen muss häufig eine Ablaufverfolgung der Aktivitäten auf dem Server erfasst werden, um ein Blockierproblem in Azure SQL-Datenbank gründlich untersuchen zu können. Ein Beispiel: Wenn eine Sitzung innerhalb einer Transaktion mehrere Anweisungen ausführt, wird nur die letzte übermittelte Anweisung dargestellt. Möglicherweise ist aber eine der vorherigen Anweisungen der Grund dafür, dass immer noch Sperren bestehen. Mit einer Ablaufverfolgung können Sie alle durch eine Sitzung ausgeführten Befehle innerhalb der aktuellen Transaktion anzeigen.

Es gibt zwei Möglichkeiten, Ablaufverfolgungen in SQL Server zu erfassen: erweiterte Ereignisse (Extended Events, XEvents) und Profiler-Ablaufverfolgungen. [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) ist allerdings eine veraltete Ablaufverfolgungstechnologie, die für Azure SQL-Datenbank nicht unterstützt wird. [Erweiterte Ereignisse](/sql/relational-databases/extended-events/extended-events) sind die neuere Ablaufverfolgungstechnologie, die mehr Flexibilität bietet und weniger Beeinträchtigungen für das beobachtete System verursacht. Die Schnittstelle ist in SQL Server Management Studio (SSMS) integriert. 

Lesen Sie das Dokument, in dem die Verwendung des [Assistenten für neue Sitzungen für erweiterte Ereignisse](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) in SSMS erläutert wird. Bei Azure SQL-Datenbanken bietet SSMS im Objekt-Explorer für jede Datenbank einen eigenen Unterordner für erweiterte Ereignisse. Verwenden Sie den Assistenten für neue Sitzungen für erweiterte Ereignisse, um die folgenden nützlichen Ereignisse zu erfassen: 

-   Kategorie „Fehler“:
    -   Attention
    -   Error_reported  
    -   Execution_warning

-   Kategorie „Warnungen“: 
    -   Missing_join_predicate

-   Kategorie „Ausführung“:
    -   Rpc_completed
    -   Rpc_starting 
    -   Sql_batch_completed
    -   Sql_batch_starting

-   Lock
    -   Lock_deadlock

-   Sitzung
    -   Existing_connection
    -   Anmelden
    -   Logout

## <a name="identify-and-resolve-common-blocking-scenarios"></a>Identifizieren und Beheben von häufigen Blockierungsszenarien

Anhand der vorherigen Informationen können Sie die Ursache der meisten Blockierprobleme ermitteln. Im Rest dieses Artikels wird erläutert, wie Sie diese Informationen zum Identifizieren und Beheben einiger häufiger Blockierungsszenarien verwenden. Hierbei wird vorausgesetzt, dass Sie die Informationen zu den blockierenden SPIDs mithilfe der oben erwähnten Skripts und die Anwendungsaktivitäten mithilfe einer XEvents-Sitzung erfasst haben.

## <a name="analyze-blocking-data"></a>Analysieren von Daten zur Blockierung 

* Untersuchen Sie die Ausgabe der DMVs `sys.dm_exec_requests` und `sys.dm_exec_sessions`, und verwenden Sie dabei `blocking_these` und `session_id`, um den Anfang der blockierenden Ketten zu finden. So lässt sich sehr deutlich bestimmen, welche Anforderungen blockieren und welche blockiert werden. Untersuchen Sie die blockierten und blockierenden Sitzungen genauer. Gibt es gemeinsame oder Stammelemente in der blockierenden Kette? Wahrscheinlich verwenden diese gemeinsam eine Tabelle, und mindestens eine der an einer blockierenden Kette beteiligten Sitzungen führt einen Schreibvorgang aus. 

* Suchen Sie in der Ausgabe der DMVs `sys.dm_exec_requests` und `sys.dm_exec_sessions` nach Informationen zu den SPIDs am Anfang der blockierenden Kette. Suchen Sie nach den folgenden Feldern: 

    -    `sys.dm_exec_requests.status`  
    Diese Spalte zeigt den Status einer bestimmten Anforderung an. In der Regel weist der Ruhestatus darauf hin, dass die SPID die Ausführung beendet hat und darauf wartet, dass die Anwendung eine weitere Abfrage oder einen weiteren Batch sendet. Der Status „Ausführbar“ oder „Wird ausgeführt“ weist darauf hin, dass die SPID zurzeit eine Abfrage ausführt. In der folgenden Tabelle finden Sie kurze Erläuterungen der verschiedenen Statuswerte.

    | Status | Bedeutung |
    |:-|:-|
    | Hintergrund | Die SPID führt einen Hintergrundtask aus, z. B. eine Deadlockerkennung, einen Protokollwriter oder einen Prüfpunkt. |
    | Ruhezustand | Die SPID wird zurzeit nicht ausgeführt. Dies weist in der Regel darauf hin, dass die SPID auf einen Befehl der Anwendung wartet. |
    | Wird ausgeführt | Die SPID wird derzeit in einem Planer ausgeführt. |
    | Ausführbar | Die SPID befindet sich in der ausführbaren Warteschlange eines Planers und wartet auf die Zuteilung von Ausführungszeit. |
    | Ausgesetzt | Die SPID wartet auf eine Ressource, z. B. eine Sperre oder einen Latch. | 
                       
    -    `sys.dm_exec_sessions.open_transaction_count`  
    Dieses Feld gibt Aufschluss über die Anzahl offener Transaktionen in dieser Sitzung. Wenn der Wert größer als 0 ist, befindet sich die SPID in einer offenen Transaktion und hält möglicherweise Sperren aufrecht, die von einer beliebigen Anweisung innerhalb der Transaktion abgerufen wurden.

    -    `sys.dm_exec_requests.open_transaction_count`  
    Dieses Feld gibt Aufschluss über die Anzahl offener Transaktionen in dieser Anforderung. Wenn der Wert größer als 0 ist, befindet sich die SPID in einer offenen Transaktion und hält möglicherweise Sperren aufrecht, die von einer beliebigen Anweisung innerhalb der Transaktion abgerufen wurden.

    -   `sys.dm_exec_requests.wait_type`, `wait_time` und `last_wait_type`  
    Wenn der `sys.dm_exec_requests.wait_type` NULL lautet, wartet die Anforderung derzeit auf nichts, und der Wert `last_wait_type` gibt den letzten von der Anforderung gefundenen `wait_type` an. Weitere Informationen zu `sys.dm_os_wait_stats` und eine Beschreibung der gängigsten Wartetypen finden Sie unter [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql). Mithilfe des Werts von `wait_time` lässt sich ermitteln, ob die Anforderung fortgesetzt wird. Wenn eine Abfrage der  `sys.dm_exec_requests` -Tabelle einen Wert in der Spalte `wait_time` zurückgibt, der niedriger ist als der Wert von `wait_time` aus einer vorherigen Abfrage von `sys.dm_exec_requests`, weist dies darauf hin, dass die vorherige Sperre abgerufen und freigegeben wurde und jetzt auf eine neue Sperre gewartet wird (vorausgesetzt, dass `wait_time` nicht 0 ist). Dies lässt sich durch einen Vergleich der `wait_resource` zwischen den Ausgaben von `sys.dm_exec_requests` ermitteln: Dies zeigt die Ressource an, auf die die Anforderung wartet.

    -   `sys.dm_exec_requests.wait_resource`: Dieses Feld gibt die Ressource an, auf die eine blockierte Anforderung wartet. In der folgenden Tabelle sind gängige Formate für `wait_resource` sowie ihre Bedeutung aufgeführt:

    | Resource | Format | Beispiel | Erklärung | 
    |:-|:-|:-|:-|
    | Tabelle | DatabaseID:ObjectID:IndexID | TAB: 5:261575970:1 | In diesem Fall ist die Datenbank-ID 5 die Beispieldatenbank „pubs“, die Objekt-ID 261575970 ist die Titeltabelle, und 1 ist der gruppierte Index. |
    | Seite | DatabaseID:FileID:PageID | PAGE: 5:1:104 | In diesem Fall ist die Datenbank-ID 5 „pubs“, die Datei-ID 1 ist die primäre Datendatei, und Seite 104 ist eine Seite, die zur Titeltabelle gehört. Zum Identifizieren der „object_id“, zu der die Seite gehört, verwenden Sie die DMF [sys.dm_db_page_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql), und übergeben Sie die DatabaseID, FileId und PageId aus `wait_resource`. | 
    | Schlüssel | DatabaseID:Hobt_id (Hashwert für Indexschlüssel) | KEY: 5:72057594044284928 (3300a4f361aa) | In diesem Fall ist die Datenbank-ID 5 „pubs“, und die Hobt_ID 72057594044284928 entspricht der „index_id“ 2 für die „object_id“ 261575970 (Titeltabelle). Verwenden Sie die `sys.partitions`-Katalogsicht, um die „hobt_id“ einer bestimmten `index_id` und `object_id` zuzuordnen. Es gibt keine Möglichkeit, den Indexschlüsselhash in einen bestimmten Schlüsselwert aufzulösen. |
    | Zeile | DatabaseID:FileID:PageID:Slot(row) | RID: 5:1:104:3 | In diesem Fall ist die Datenbank-ID 5 „pubs“, die Datei-ID 1 ist die primäre Datendatei, Seite 104 ist eine Seite, die zur Titeltabelle gehört, und Slot 3 gibt die Position der Zeile auf der Seite an. |
    | Compile  | DatabaseID:FileID:PageID:Slot(row) | RID: 5:1:104:3 | In diesem Fall ist die Datenbank-ID 5 „pubs“, die Datei-ID 1 ist die primäre Datendatei, Seite 104 ist eine Seite, die zur Titeltabelle gehört, und Slot 3 gibt die Position der Zeile auf der Seite an. |

    -    `sys.dm_tran_active_transactions`: Die DMV [sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql) enthält Daten zu offenen Transaktionen, die mit anderen DMVs verknüpft werden können, um ein vollständiges Bild der Transaktionen zu erhalten, die auf einen Commit oder Rollback warten. Verwenden Sie die folgende Abfrage, um Informationen zu offenen Transaktionen zurückzugeben, die mit anderen DMVs einschließlich [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql) verknüpft sind. Beachten Sie den aktuellen Zustand einer Transaktion, den Wert für `transaction_begin_time` sowie weitere situationsbezogene Daten, um zu bewerten, ob es sich möglicherweise um eine Blockierungsquelle handelt.

    ```sql
    SELECT tst.session_id, [database_name] = db_name(s.database_id)
    , tat.transaction_begin_time
    , transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime()) 
    , transaction_type = CASE tat.transaction_type  WHEN 1 THEN 'Read/write transaction'
                                                    WHEN 2 THEN 'Read-only transaction'
                                                    WHEN 3 THEN 'System transaction'
                                                    WHEN 4 THEN 'Distributed transaction' END
    , input_buffer = ib.event_info, tat.transaction_uow     
    , transaction_state  = CASE tat.transaction_state    
                WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                WHEN 1 THEN 'The transaction has been initialized but has not started.'
                WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                WHEN 4 THEN 'The commit process has been initiated on the distributed transaction.'
                WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                WHEN 6 THEN 'The transaction has been committed.'
                WHEN 7 THEN 'The transaction is being rolled back.'
                WHEN 8 THEN 'The transaction has been rolled back.' END 
    , transaction_name = tat.name, request_status = r.status
    , azure_dtc_state = CASE tat.dtc_state 
                        WHEN 1 THEN 'ACTIVE'
                        WHEN 2 THEN 'PREPARED'
                        WHEN 3 THEN 'COMMITTED'
                        WHEN 4 THEN 'ABORTED'
                        WHEN 5 THEN 'RECOVERED' END
    , tst.is_user_transaction, tst.is_local
    , session_open_transaction_count = tst.open_transaction_count  
    , s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
    FROM sys.dm_tran_active_transactions tat 
    INNER JOIN sys.dm_tran_session_transactions tst  on tat.transaction_id = tst.transaction_id
    INNER JOIN sys.dm_exec_sessions s on s.session_id = tst.session_id 
    LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
    CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib;
    ```

    -   Andere Spalten

        Die verbleibenden Spalten in [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) und [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) können ebenfalls Erkenntnisse zur Ursache des Problems bieten. Ihr Nutzen variiert je nach Art des Problems. Sie können beispielsweise ermitteln, ob das Problem nur bei bestimmten Clients (hostname) oder in bestimmten Netzwerkbibliotheken (net_library) auftritt, wann der letzte Batch in `sys.dm_exec_sessions` von einer SPID übermittelt wurde (`last_request_start_time`), wie lange eine Anforderung in `sys.dm_exec_requests` ausgeführt wurde (`start_time`) usw.


## <a name="common-blocking-scenarios"></a>Häufige Blockierungsszenarien

In der folgenden Tabelle werden häufige Symptome ihren möglichen Ursachen zugeordnet.  

Die Spalten Waittype, Open_Tran und Status beziehen sich auf Informationen, die von [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) zurückgegeben werden. Andere Spalten können durch [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) zurückgegeben werden. Die Spalte „Löst das Problem?“ gibt an, ob die Blockierung von selbst aufgelöst wird oder ob die Sitzung über den Befehl `KILL` beendet werden muss. Weitere Informationen finden Sie unter [KILL (Transact-SQL)](/sql/t-sql/language-elements/kill-transact-sql).

| Szenario | Waittype | Open_Tran | Status | Löst das Problem? | Andere Symptome |  
|:-|:-|:-|:-|:-|:-|--|
| 1 | NOT NULL | >= 0 | runnable | Ja, wenn die Abfrage beendet wird. | In `sys.dm_exec_sessions`, `reads`, `cpu_time` und/oder `memory_usage` wachsen die Spalten im Lauf der Zeit an. Bei Abschluss ist die Dauer der Abfrage hoch. |
| 2 | NULL | \>0 | sleeping | Nein, aber die SPID kann beendet werden. | In der Sitzung von „Erweiterte Ereignisse“ wird für diese SPID möglicherweise ein Signal „Achtung“ angezeigt, das angibt, dass ein Timeout- oder Abbruchereignis für die Abfrage aufgetreten ist. |
| 3 | NULL | \>= 0 | runnable | Nein. Wird erst aufgelöst, wenn der Client alle Zeilen abgerufen oder die Verbindung geschlossen hat. Die SPID kann beendet werden, dies kann aber bis zu 30 Sekunden dauern. | Wenn der Wert für „open_transaction_count“ 0 lautet und die SPID Sperren aufrechterhält, während die Isolationsstufe für die Transaktion „default (READ COMMMITTED)“ lautet, ist dies eine wahrscheinliche Ursache. |  
| 4 | Varies | \>= 0 | runnable | Nein. Wird erst aufgelöst, wenn der Client Abfragen abgebrochen oder Verbindungen geschlossen hat. Die SPIDs können beendet werden, dies kann aber bis zu 30 Sekunden dauern. | Die Spalte `hostname` in `sys.dm_exec_sessions` für die SPID am Anfang einer blockierenden Kette ist die gleiche wie eine der SPIDs, die blockiert wird. |  
| 5 | NULL | \>0 | rollback | Ja. | In der Sitzung von „Erweiterte Ereignisse“ wird für diese SPID möglicherweise ein Signal „Achtung“ angezeigt, das angibt, dass ein Timeout- oder Abbruchereignis für die Abfrage aufgetreten ist oder einfach eine Rollbackanweisung ausgegeben wurde. |  
| 6 | NULL | \>0 | sleeping | Irgendwann. Wenn Windows NT festlegt, dass die Sitzung nicht mehr aktiv ist, wird die Verbindung mit Azure SQL-Datenbank getrennt. | Der `last_request_start_time`-Wert in `sys.dm_exec_sessions` liegt viel früher als der aktuelle Zeitpunkt. |

## <a name="detailed-blocking-scenarios"></a>Ausführliche Blockierungsszenarien

1.  Blockierung wird durch normal ausgeführte Abfrage mit langer Ausführungszeit verursacht

    **Lösung:** Die Lösung für diese Art von Blockierproblem besteht darin, nach Möglichkeiten zum Optimieren der Abfrage zu suchen. Tatsächlich kann es sich hierbei einfach um ein Leistungsproblem handeln, das Sie als solches behandeln müssen. Informationen zur Problembehandlung einer bestimmten langsam ausgeführten Abfrage finden Sie unter [Problembehandlung bei langsam ausgeführten Abfragen in SQL Server](/troubleshoot/sql/performance/troubleshoot-slow-running-queries). Weitere Informationen finden Sie unter [Überwachen und Optimieren der Leistung](/sql/relational-databases/performance/monitor-and-tune-for-performance). 

    Berichte aus dem [Abfragespeicher](/sql/relational-databases/performance/best-practice-with-the-query-store) in SSMS sind ebenfalls eine sehr empfehlenswerte und nützliche Methode, um die ressourcenintensivsten Abfragen oder suboptimale Ausführungspläne zu identifizieren. Lesen Sie auch den Artikel [Intelligent Insights](intelligent-insights-overview.md) zum Überwachen der Leistung der Azure SQL-Datenbank im Azure-Portal sowie den Artikel [Query Performance Insight für Azure SQL-Datenbank](query-performance-insight-use.md).

    Wenn eine Abfrage mit langer Ausführungszeit andere Benutzer blockiert und nicht optimiert werden kann, sollten Sie erwägen, diese aus einer OLTP-Umgebung in ein dediziertes Berichtssystem, ein [synchrones schreibgeschütztes Replikat der Datenbank](read-scale-out.md) zu verschieben.

1.  Blockierung durch eine SPID im Ruhemodus mit einer Transaktion ohne Commit

    Diese Art von Blockierung kann häufig anhand einer SPID identifiziert werden, die sich im Ruhezustand befindet oder auf einen Befehl wartet, deren Transaktionsschachtelungsebene (`@@TRANCOUNT`, `open_transaction_count` von `sys.dm_exec_requests`) aber größer als null ist. Dies kann passieren, wenn für die Anwendung ein Abfragetimeout auftritt oder die Anwendung einen Abbruch ausgibt, ohne gleichzeitig die erforderliche Anzahl von ROLLBACK- und/oder COMMIT-Anweisungen auszugeben. Wenn eine SPID ein Abfragetimeout oder einen Abbruch empfängt, beendet sie die aktuelle Abfrage und den aktuellen Batch, führt aber nicht automatisch einen Rollback oder Commit der Transaktion aus. Dafür ist die Anwendung zuständig, weil Azure SQL-Datenbank nicht davon ausgehen kann, dass aufgrund des Abbruchs einer einzigen Abfrage ein Rollback der gesamten Transaktion ausgeführt werden muss. Das Timeout oder der Abbruch der Abfrage wird in der Sitzung von „Erweiterte Ereignisse“ als Signalereignis vom Typ ACHTUNG für die SPID angezeigt.

    Um eine explizite Transaktion ohne Commit zu veranschaulichen, geben Sie die folgende Abfrage aus:

    ```sql
    CREATE TABLE #test (col1 INT);
    INSERT INTO #test SELECT 1;
    BEGIN TRAN
    UPDATE #test SET col1 = 2 where col1 = 1;
    ```

    Führen Sie dann die folgende Abfrage im selben Fenster aus:
    ```sql
    SELECT @@TRANCOUNT;
    ROLLBACK TRAN
    DROP TABLE #test;
    ```

    Die Ausgabe der zweiten Abfrage weist darauf hin, dass die Schachtelungsebene der Transaktion „1“ lautet. Alle in der Transaktion erhaltenen Sperren werden aufrechterhalten, bis ein Rollback oder Commit der Transaktion ausgeführt wurde. Wenn Anwendungen Transaktionen explizit öffnen und committen, könnte ein Kommunikations- oder sonstiger Fehler dazu führen, dass die Sitzung und die zugehörige Transaktion in einem offenen Zustand verbleiben. 

    Verwenden Sie das auf `sys.dm_tran_active_transactions` basierende Skript weiter oben in diesem Artikel, um Transaktionen in der Instanz zu identifizieren, für die derzeit noch kein Commit ausgeführt wurde.

    **Lösungen**:

     -   Darüber hinaus kann es sich bei dieser Art von Blockierproblem auch um ein Leistungsproblem handeln, das Sie als solches behandeln müssen. Wenn die Ausführungszeit der Abfrage verringert werden kann, tritt kein Timeout- oder Abbruchereignis für die Abfrage auf. Es ist wichtig, dass die Anwendung Timeout- oder Abbruchszenarien verarbeiten kann, sollten diese auftreten. Eine Untersuchung der Leistung der Abfrage kann aber auch weitere Vorteile bieten. 
     
     -    Anwendungen müssen Transaktionsschachtelungsebenen ordnungsgemäß verwalten. Andernfalls können sie ein Blockierproblem verursachen, nachdem eine Abfrage auf diese Weise abgebrochen wurde. Beachten Sie Folgendes:  

            *    Führen Sie im Fehlerhandler der Clientanwendung nach jedem Fehler `IF @@TRANCOUNT > 0 ROLLBACK TRAN` aus, auch wenn die Clientanwendung nicht anzeigt, dass eine Transaktion offen ist. Eine Überprüfung auf offene Transaktionen ist erforderlich, weil eine während der Batchausführung gespeicherte Prozedur eine Transaktion gestartet haben könnte, ohne dass die Clientanwendung darüber informiert ist. Bestimmte Bedingungen – beispielsweise das Abbrechen einer Abfrage – verhindern die weitere Ausführung der Prozedur nach der aktuellen Anweisung. Daher wird dieser Rollbackcode in solchen Fällen auch dann nicht ausgeführt, wenn die Prozedur über eine Logik zum Überprüfen von `IF @@ERROR <> 0` und Abbrechen der Transaktion verfügt.  
            *    Wenn eine Anwendung, die eine Verbindung öffnet und eine kleine Anzahl von Abfragen ausführt, bevor die Verbindung wieder für den Pool freigegeben wird (dies kann z. B. bei webbasierten Anwendungen der Fall sein), Verbindungspooling verwendet, kann eine vorübergehende Deaktivierung des Poolings das Problem mindern, bis die Clientanwendung so geändert wurde, dass die Fehler ordnungsgemäß behandelt werden. Bei deaktiviertem Verbindungspooling sorgt die Freigabe der Verbindung für eine physische Trennung der Verbindung zur Azure SQL-Datenbank, und der Server führt einen Rollback aller offenen Transaktionen aus.  
            *    Verwenden Sie `SET XACT_ABORT ON` für die Verbindung oder in gespeicherten Prozeduren, die Transaktionen starten und nach einem Fehler nicht bereinigen. Im Fall eines Laufzeitfehlers bricht diese Einstellung alle offenen Transaktionen ab und gibt die Steuerung an den Client zurück. Weitere Informationen finden Sie unter [SET XACT_ABORT (Transact-SQL)](/sql/t-sql/statements/set-xact-abort-transact-sql).

    > [!NOTE]
    > Die Verbindung wird erst zurückgesetzt, wenn sie vom Verbindungspool wiederverwendet wird. Daher ist es möglich, dass ein Benutzer eine Transaktion öffnet und dann die Verbindung im Verbindungspool freigibt, die Verbindung aber einige Sekunden lang nicht wiederverwendet wird. In diesem Zeitraum bleibt die Transaktion offen. Falls die Verbindung nicht wiederverwendet wird, wird die Transaktion abgebrochen, wenn für die Verbindung ein Timeout auftritt und sie und aus dem Verbindungspool entfernt wird. Daher sollte die Clientanwendung optimalerweise Transaktionen im Fehlerhandler abbrechen oder `SET XACT_ABORT ON` verwenden, um diese potenzielle Verzögerung zu vermeiden.

    > [!CAUTION]
    > Gemäß `SET XACT_ABORT ON` werden T-SQL-Anweisungen nach einer Anweisung, die einen Fehler verursacht, nicht ausgeführt. Dies kann sich auf den gewünschten Flow des vorhandenen Codes auswirken.

1.  Blockierung durch eine SPID, deren zugehörige Clientanwendung nicht alle Ergebniszeilen vollständig abgerufen hat

    Nach dem Senden einer Abfrage an den Server müssen alle Anwendungen sofort alle Ergebniszeilen vollständig abrufen. Wenn eine Anwendung nicht alle Ergebniszeilen abruft, können Sperren auf den Tabellen verbleiben, die andere Benutzer blockieren. Sollten Sie eine Anwendung nutzen, die SQL-Anweisungen transparent an den Server übermittelt, muss die Anwendung sämtliche Ergebniszeilen abrufen. Andernfalls (oder wenn die Anwendung nicht dafür konfiguriert werden kann) lässt sich das Blockierproblem möglicherweise nicht lösen. Um dieses Problem zu vermeiden, können Sie entsprechende Anwendungen auf eine Berichtsdatenbank oder eine Datenbank zur Entscheidungsfindung, getrennt von der Haupt-OLTP-Datenbank, beschränken.
    
    > [!NOTE]
    > Weitere Informationen zu Anwendungen, die eine Verbindung mit Azure SQL-Datenbank herstellen, finden Sie unter [Wiederholungslogik für vorübergehende Fehler](./troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors). 
    
    **Lösung:** Die Anwendung muss so umgeschrieben werden, dass sie alle Zeilen des Ergebnisses vollständig abruft. Dies schließt das [Verwenden von OFFSET und FETCH in der ORDER BY-Klausel](/sql/t-sql/queries/select-order-by-clause-transact-sql#using-offset-and-fetch-to-limit-the-rows-returned) einer Abfrage nicht aus, um ein serverseitiges Paging auszuführen.

1.  Blockierung aufgrund einer Sitzung im Rollbackzustand

    Für eine Datenänderungsabfrage, die per KILL beendet oder außerhalb einer benutzerdefinierten Transaktion abgebrochen wird, wird ein Rollback ausgeführt. Dies kann auch als Nebeneffekt auftreten, wenn eine Clientnetzwerksitzung getrennt oder eine Anforderung als Deadlockopfer ausgewählt wird. Dieser Fehler lässt sich häufig in der Ausgabe von `sys.dm_exec_requests` identifizieren: Hier ist der ROLLBACK-Befehl zu finden, und die `percent_complete`-Spalte kann den Fortschritt anzeigen. 

    Dank des Features der [beschleunigten Datenbankwiederherstellung in Azure SQL](../accelerated-database-recovery.md), das 2019 eingeführt wurde, sollten lang andauernde Rollbacks nur selten auftreten.
    
    **Lösung:** Warten Sie, bis die SPID den Rollback der vorgenommenen Änderungen beendet hat. 

    Um diese Situation zu vermeiden, führen Sie in OLTP-Systemen in Zeiträumen mit sehr viel Aktivität keine umfangreichen Batchschreibvorgänge oder Indexerstellungs- oder Wartungsvorgänge aus. Führen Sie solche Vorgänge nach Möglichkeit in Zeiträumen mit geringer Aktivität aus.

1.  Blockierung durch eine verwaiste Verbindung

    Wenn die Clientanwendung Fehler abfängt oder die Clientarbeitsstation neu gestartet wird, wird die Netzwerksitzung beim Server unter bestimmten Bedingungen möglicherweise nicht sofort abgebrochen. Aus Sicht von Azure SQL-Datenbank ist der Client noch immer vorhanden, und möglicherweise werden alle Sperren beibehalten. Weitere Informationen finden Sie unter [Problembehandlung bei verwaisten Verbindungen in SQL Server](/sql/t-sql/language-elements/kill-transact-sql#remarks). 

    **Lösung:** Wenn die Clientanwendung die Verbindung getrennt hat, ohne die Ressourcen ordnungsgemäß zu bereinigen, können Sie die SPID mit dem Befehl `KILL` beenden. Der Befehl `KILL` akzeptiert den SPID-Wert als Eingabe. Um beispielsweise SPID 99 zu beenden, verwenden Sie den folgenden Befehl:

    ```sql
    KILL 99
    ```

## <a name="see-also"></a>Weitere Informationen

* [Überwachung und Leistungsoptimierung in Azure SQL-Datenbank und Azure SQL Managed Instance](./monitor-tune-overview.md)
* [Überwachen der Leistung mit dem Abfragespeicher](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [Handbuch zu Transaktionssperren und Zeilenversionsverwaltung](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [SET TRANSACTION ISOLATION LEVEL](/sql/t-sql/statements/set-transaction-isolation-level-transact-sql)
* [Schnellstart: Erweiterte Ereignisse in SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
* [Intelligent Insights mit KI für die Überwachung und Problembehandlung der Datenbankleistung](intelligent-insights-overview.md)

## <a name="learn-more"></a>Erfahren Sie mehr

* [Azure SQL-Datenbank: Verbessern der Leistungsoptimierung mithilfe der automatischen Optimierung](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Improving-Performance-Tuning-with-Automatic-Tuning)
* [Bereitstellen einer konsistenten Leistung mit Azure SQL](/learn/modules/azure-sql-performance/)
* [Beheben von Konnektivitätsproblemen und anderen Fehlern mit Azure SQL-Datenbank und Azure SQL Managed Instance](troubleshoot-common-errors-issues.md)
* [Behandeln vorübergehender Fehler](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)
* [Konfigurieren des maximalen Parallelitätsgrads (MAXDOP) in Azure SQL Database](configure-max-degree-of-parallelism.md)
