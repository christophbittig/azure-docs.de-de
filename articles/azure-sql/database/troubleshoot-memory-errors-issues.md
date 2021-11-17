---
title: Behandeln von Arbeitsspeicherproblemen
titleSuffix: Azure SQL Database
description: Enthält Schritte zur Behandlung von Problemen mit unzureichendem Arbeitsspeicher in Azure SQL-Datenbanken
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: troubleshooting
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 11/03/2021
ms.openlocfilehash: 07ff08303cc68934d5d7191952536b9101e12cf6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494708"
---
# <a name="troubleshoot-out-of-memory-errors-with-azure-sql-database"></a>Behandeln von Fehlern mit unzureichendem Arbeitsspeicher mit Azure SQL-Datenbanken  
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Möglicherweise werden Fehlermeldungen angezeigt, wenn die SQL Datenbank-Engine nicht genügend Arbeitsspeicher zum Ausführen der Abfrage zuteilen konnte. Dies kann verschiedene Ursachen haben, z. B. die Grenzwerte des ausgewählten Dienstziels, die aggregierten Arbeitsspeicheranforderungen der Workload und die Arbeitsspeicheranforderungen durch die Abfrage. Weitere Informationen zum Arbeitsspeicherressourcenlimit für Azure SQL-Datenbanken finden Sie unter [Ressourcenverwaltung in Azure SQL-Datenbank](resource-limits-logical-server.md#memory).

> [!NOTE]
> **Dieser Artikel konzentriert sich auf Azure SQL-Datenbank.** Weitere Informationen zur Behandlung von Problemen mit nicht genügend Arbeitsspeicher in SQL Server finden Sie unter [MSSQLSERVER_701](/sql/relational-databases/errors-events/mssqlserver-701-database-engine-error).

Probieren Sie die folgenden Untersuchungsmöglichkeiten aus, wenn die nachfolgenden Fehler auftreten:

- Fehlercode 701 mit der Fehlermeldung „Es ist nicht genügend Systemspeicher im Ressourcenpool '%ls' zum Ausführen dieser Abfrage verfügbar.“
- Fehlercode 802 mit der Fehlermeldung „Im Pufferpool ist nicht genügend Arbeitsspeicher verfügbar.“

## <a name="investigate-memory-allocation"></a>Untersuchen der Speicherbelegung

Wenn „Nicht genügend Speicher“-Fehler in Azure SQL-Datenbank auftreten, sollten Sie das Servicelevelziel der Datenbank im Azure-Portal zumindest vorübergehend erhöhen. Wenn weiterhin Fehler aufgrund von unzureichendem Arbeitsspeicher auftreten, verwenden Sie die folgenden Abfragen, um nach ungewöhnlich hohen Abfragespeicherzuweisungen zu suchen, die zu einer unzureichenden Arbeitsspeicherbedingung beitragen können. Führen Sie die folgenden Beispielabfragen in der Datenbank aus, bei denen der Fehler aufgetreten ist (nicht in der `master`-Datenbank des logischen Azure SQL Server).  

### <a name="use-dmvs-to-view-memory-clerks"></a>Verwenden von DMVs zum Anzeigen von Arbeitsspeicher-Clerks

Beginnen Sie mit einer umfassenden Untersuchung, wenn der Fehler „Nicht genügend Arbeitsspeicher“ kürzlich aufgetreten ist, indem Sie die Speicherbelegung für Arbeitsspeicher-Clerks anzeigen. Arbeitsspeicher-Clerks sind für diese Azure SQL-Datenbank intern im Datenbankmodul. Die wichtigsten Arbeitsspeicher-Clerks in Bezug auf die zugeordneten Seiten können Aufschluss geben, welche Art von Abfrage oder Feature von SQL Server am meisten Arbeitsspeicher verbraucht. 


```sql
SELECT [type], [name], pages_kb, virtual_memory_committed_kb
FROM sys.dm_os_memory_clerks
WHERE memory_node_id <> 64 -- ignore Dedicated Admin Connection (DAC) node
ORDER BY pages_kb DESC;
GO
SELECT [type], [name], pages_kb, virtual_memory_committed_kb
FROM sys.dm_os_memory_clerks
WHERE memory_node_id <> 64 -- ignore Dedicated Admin Connection (DAC) node
ORDER BY virtual_memory_committed_kb DESC;
```

 - Einige gängige Arbeitsspeicher-Clerks, z. B. MEMORYCLERK_SQLQERESERVATIONS, lassen sich am besten lösen, indem Abfragen mit großen Speicherzuweisungen identifiziert und deren Leistung durch bessere Indizierung und Indexoptimierung verbessert wird. 
 - Obwohl OBJECTSTORE_LOCK_MANAGER nicht mit Speicherzuweisungen zu tun hat, ist zu erwarten, dass dieser hoch ist, wenn Abfragen viele Sperren beanspruchen, z. B. aufgrund einer deaktivierten Sperreneskalierung oder sehr großer Transaktionen. 
 - Es ist zu erwarten, dass einige Clerks die höchste Auslastung haben: MEMORYCLERK_SQLBUFFERPOOL ist fast immer der oberste Clerk, während CACHESTORE_COLUMNSTOREOBJECTPOOL hoch ist, wenn Columnstore-Indizes verwendet werden. Die höchste Auslastung durch diese Clerks wird erwartet. 

 Weitere Informationen zu Arbeitsspeicherclerktypen fnden Sie unter [sys.dm_os_memory_clerks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql). 

### <a name="use-dmvs-to-investigate-active-queries"></a>Verwenden von DMVs zum Untersuchen aktiver Abfragen 

In den meisten Fällen ist die fehlgeschlagene Abfrage nicht die Ursache dieses Fehlers. 

Die folgende Beispielabfrage für Azure SQL-Datenbank gibt wichtige Informationen zu Transaktionen zurück, die derzeit Speicherzuweisungen enthalten oder darauf warten. Konzentrieren Sie sich auf die wichtigsten Abfragen, die für die Untersuchung und Leistungsoptimierung identifiziert wurden, und bewerten Sie, ob sie wie beabsichtigt ausgeführt werden. Berücksichtigen Sie die Termine von speicherintensiven Berichtsabfragen oder Wartungsvorgängen.

```sql
--Active requests with memory grants
SELECT
--Session data 
  s.[session_id], s.open_transaction_count
--Memory usage
, r.granted_query_memory, mg.grant_time, mg.requested_memory_kb, mg.granted_memory_kb, mg.required_memory_kb, mg.used_memory_kb, mg.max_used_memory_kb     
--Query 
, query_text = t.text, input_buffer = ib.event_info, query_plan_xml = qp.query_plan, request_row_count = r.row_count, session_row_count = s.row_count
--Session history and status
, s.last_request_start_time, s.last_request_end_time, s.reads, s.writes, s.logical_reads, session_status = s.[status], request_status = r.status
--Session connection information
, s.host_name, s.program_name, s.login_name, s.client_interface_name, s.is_user_process
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests AS r 
    ON r.[session_id] = s.[session_id]
LEFT OUTER JOIN sys.dm_exec_query_memory_grants AS mg 
    ON mg.[session_id] = s.[session_id]
OUTER APPLY sys.dm_exec_sql_text (r.[sql_handle]) AS t
OUTER APPLY sys.dm_exec_input_buffer(s.[session_id], NULL) AS ib 
OUTER APPLY sys.dm_exec_query_plan (r.[plan_handle]) AS qp 
WHERE mg.granted_memory_kb > 0
ORDER BY mg.granted_memory_kb desc, mg.requested_memory_kb desc;
```

Sie können die KILL-Anweisung verwenden, um eine aktuell ausgeführte Abfrage zu beenden, die eine große Speicherzuweisung enthält oder darauf wartet. Verwenden Sie diese Anweisung vorsichtig, besonders wenn gerade geschäftskritische Prozesse ausgeführt werden. Weitere Informationen finden Sie unter [KILL &#40;Transact-SQL&#41;](/sql/t-sql/language-elements/kill-transact-sql). 


### <a name="use-query-store-to-investigate-past-query-memory-usage"></a>Verwenden des Abfragedatenspeichers, um die bisherige Speicherauslastung von Abfragen zu untersuchen

Während die vorherige Beispielabfrage nur Liveabfrageergebnisse meldet, verwendet die folgende Abfrage den [Abfragedatenspeicher](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store), um Informationen zur vorherigen Abfrageausführung auszugeben. Dies kann bei der Untersuchung eines Fehlers wegen nicht genügend Arbeitsspeicher hilfreich sein, der in der Vergangenheit aufgetreten ist.

Die folgende Beispielabfrage für Azure SQL-Datenbank gibt wichtige Informationen zu Abfrageausführungen zurück, die vom Abfragedatenspeicher aufgezeichnet wurden. Konzentrieren Sie sich auf die wichtigsten Abfragen, die für die Untersuchung und Leistungsoptimierung identifiziert wurden, und bewerten Sie, ob sie wie beabsichtigt ausgeführt werden. Beachten Sie den Zeitfilter für `qsp.last_execution_time`, um die Ergebnisse auf den letzten Verlauf zu beschränken. Sie können die TOP-Klausel anpassen, um je nach Umgebung mehr oder weniger Ergebnisse zu erhalten.

```sql
SELECT TOP 10 PERCENT --limit results
  a.plan_id, query_id, plan_group_id, query_sql_text
, query_plan = TRY_CAST(query_plan as XML)
, avg_query_max_used_memory
, min_query_max_used_memory
, max_query_max_used_memory
, last_query_max_used_memory 
, last_execution_time  
, query_count_executions
    FROM (
    SELECT 
      qsp.plan_id, qsp.query_id, qsp.plan_group_id, qsp.query_plan, qsqt.query_sql_text
    , last_execution_time = MAX(qsp.last_execution_time)
    , query_count_executions = SUM(qsrs.count_executions) 
    , avg_query_max_used_memory = AVG(qsrs.avg_query_max_used_memory)
    , min_query_max_used_memory = MIN(qsrs.min_query_max_used_memory)
    , max_query_max_used_memory = MAX(qsrs.max_query_max_used_memory)
    , last_query_max_used_memory = MAX(qsrs_latest.last_query_max_used_memory) --only from latest result
    FROM sys.query_store_plan AS qsp 
    INNER JOIN sys.query_store_query AS qsq
        ON qsp.query_id = qsq.query_id
    INNER JOIN sys.query_store_query_text AS qsqt
        ON qsq.query_text_id = qsqt.query_text_id 
    INNER JOIN sys.query_store_runtime_stats AS qsrs
        ON qsp.plan_id = qsrs.plan_id 
    INNER JOIN (SELECT plan_id
            , last_query_max_used_memory 
            , rownum = ROW_NUMBER() OVER (PARTITION BY plan_id ORDER BY last_execution_time DESC)
            FROM sys.query_store_runtime_stats qsrs) AS qsrs_latest
        ON qsrs_latest.plan_id = qsp.plan_id
        AND qsrs_latest.rownum = 1 --use latest last_query_max_used_memory per plan_id
    WHERE DATEADD(hour, -24, sysdatetime()) < qsp.last_execution_time --past 24 hours only
    AND qsrs_latest.last_query_max_used_memory > 0
    GROUP BY qsp.plan_id, qsp.query_id, qsp.plan_group_id, qsp.query_plan, qsqt.query_sql_text
    ) AS a
ORDER BY max_query_max_used_memory DESC, avg_query_max_used_memory DESC;
```

### <a name="extended-events"></a>Erweiterte Ereignisse
Zusätzlich zu den vorherigen Informationen kann es helfen, eine Ablaufverfolgung der Aktivitäten auf dem Server zu erfassen, um ein Problem mit nicht genügend Arbeitsspeicher in Azure SQL-Datenbank gründlich untersuchen zu können. 

Es gibt zwei Möglichkeiten, Ablaufverfolgungen in SQL Server zu erfassen: erweiterte Ereignisse (Extended Events, XEvents) und Profiler-Ablaufverfolgungen. [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) ist allerdings eine veraltete Ablaufverfolgungstechnologie, die für Azure SQL-Datenbank nicht unterstützt wird. [Erweiterte Ereignisse](/sql/relational-databases/extended-events/extended-events) sind die neuere Ablaufverfolgungstechnologie, die mehr Flexibilität bietet und weniger Beeinträchtigungen für das beobachtete System verursacht. Die Schnittstelle ist in SQL Server Management Studio (SSMS) integriert. 

Lesen Sie das Dokument, in dem die Verwendung des [Assistenten für neue Sitzungen für erweiterte Ereignisse](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) in SSMS erläutert wird. Bei Azure SQL-Datenbanken bietet SSMS im Objekt-Explorer für jede Datenbank einen eigenen Unterordner für erweiterte Ereignisse. Verwenden Sie eine Sitzung für erweiterte Ereignisse, um diese nützlichen Ereignisse zu erfassen und die Abfragen zu identifizieren, die sie generieren: 

-   Kategorie „Fehler“:
    - error_reported
    - exchange_spill
    - hash_spill_details
    
-   Kategorie „Ausführung“:
    - excessive_non_grant_memory_used

-   Kategoriespeicher:
    - query_memory_grant_blocking
    - query_memory_grant_usage

Die Erfassung von Speicherzuweisungsblöcken, Speicherzuweisungsüberläufen oder übermäßige Speicherzuweisungen kann ein potenzieller Hinweis darauf sein, dass eine Abfrage plötzlich mehr Arbeitsspeicher als in der Vergangenheit benutzt, und eine mögliche Erklärung für einen aufgetretenen „Nicht genügend Arbeitsspeicher“-Fehler in einer vorhandenen Workload sein.

### <a name="in-memory-oltp-out-of-memory"></a>In-Memory-OLTP nicht genügend Arbeitsspeicher 

Bei Verwendung von In-Memory OLTP kann `Error code 41805: There is insufficient memory in the resource pool '%ls' to run this operation` auftreten. Reduzieren Sie die Datenmenge in speicheroptimierten Tabellen und speicheroptimierten Tabellenwertparametern, oder skalieren Sie die Datenbank auf ein höheres Dienstziel hoch, um mehr Arbeitsspeicher zu erhalten. Weitere Informationen zu Problemen mit nicht genügend Arbeitsspeicher bei SQL Server In-Memory OLTP finden Sie unter [Beheben von Problemen mit nicht genügend Arbeitsspeicher](/sql/relational-databases/in-memory-oltp/resolve-out-of-memory-issues).

### <a name="get-azure-sql-db-support"></a>Erhalten von Azure SQL DB-Unterstützung 

Wenn weiterhin Fehler wegen nicht genügend Arbeitsspeicher in Azure SQL-Datenbank auftreten, können Sie eine Azure-Supportanfrage stellen, indem Sie auf der Website des **Azure-Supports** die Option [Support erhalten](https://azure.microsoft.com/support/options) auswählen.

## <a name="next-steps"></a>Nächste Schritte

- [Intelligente Abfrageverarbeitung in SQL-Datenbanken](/sql/relational-databases/performance/intelligent-query-processing)
- [Leitfaden zur Architektur der Abfrageverarbeitung](/sql/relational-databases/query-processing-architecture-guide)    
- [Leistungscenter für SQL Server-Datenbank-Engine und Azure SQL-Datenbank](/sql/relational-databases/performance/performance-center-for-sql-server-database-engine-and-azure-sql-database)  
- [Beheben von Konnektivitätsproblemen und anderen Fehlern mit Azure SQL-Datenbank und Azure SQL Managed Instance](troubleshoot-common-errors-issues.md)
- [Behandeln vorübergehender Verbindungsfehler in SQL-Datenbank und SQL Managed Instance](troubleshoot-common-connectivity-issues.md)
- [Demo zur intelligenten Abfrageverarbeitung](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/intelligent-query-processing)   
- [Ressourcenverwaltung in Azure SQL-Datenbank](resource-limits-logical-server.md#memory)
