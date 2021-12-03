---
title: 'Referenz: Überwachen von SAP in Azure-Daten'
description: Wichtige Referenzmaterialien für die Überwachung von SAP in Azure
author: mamccrea
ms.topic: reference
ms.author: mamccrea
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.custom: subject-monitoring
ms.date: 08/27/2021
ms.openlocfilehash: 36d90dc91a37d3881f7fb74637b74759904bf3c7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060639"
---
# <a name="monitor-sap-on-azure-preview-data-reference"></a>Referenz: Überwachen von SAP in Azure-Daten (Vorschau)

Dieser Artikel enthält eine Referenz von Protokolldaten, die gesammelt werden, um die Leistung und Verfügbarkeit von Azure Monitor für SAP-Lösungen. Ausführliche Informationen zum Erfassen und Analysieren von Überwachungsdaten für Azure in Azure finden Sie unter [Überwachen von SAP in Azure (Vorschau)](monitor-sap-on-azure.md).

## <a name="metrics"></a>Metriken

Azure Monitor für SAP-Lösungen unterstützt keine Metriken.

## <a name="azure-monitor-logs-tables"></a>Tabellen in Azure Monitor-Protokollen

Dieser Abschnitt bezieht sich auf alle Kusto-Tabellen in Azure Monitor-Protokollen, die für Azure Monitor für SAP-Lösungen relevant und für die Abfrage durch Log Analytics verfügbar sind. Azure Monitor für SAP-Lösungen verwendet benutzerdefinierte Protokolle. Die Schemas für einige Tabellen werden von Drittanbietern wie SAP definiert. Im Folgenden finden Sie die aktuellen benutzerdefinierten Protokolle für Azure Monitor für SAP-Lösungen mit Links zu Quellen für weitere Informationen.

### <a name="saphana_hostconfig_cl"></a>SapHana_HostConfig_CL

Weitere Informationen finden Sie unter [M_LANDSCAPE_HOST_CONFIGURATION Systemansicht](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.00/en-US/20b1d83875191014b028e076c874e9d7.html) in der Referenz: SAP HANA SQL und Systemansichten.

### <a name="saphana_hostinformation_cl"></a>SapHana_HostInformation_CL

Weitere Informationen finden Sie unter [M_HOST_INFOMATION Systemansicht](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20b10028751910148c1c9de602d771de.html) in der Referenz: SAP HANA SQL und Systemansichten.

### <a name="saphana_systemoverview_cl"></a>SapHana_SystemOverview_CL

Weitere Informationen finden Sie unter [M_SYSTEM_OVERVIEW Systemansicht](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20c61f0675191014a1d5f96d9668855b.html) in der Referenz: SAP HANA SQL und Systemansichten.

### <a name="saphana_loadhistory_cl"></a>SapHana_LoadHistory_CL

Weitere Informationen finden Sie unter [M_LOAD_HISTORY_HOST Systemansicht](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/3fa52abf1d854edbb7342a69364bcb0e.html) in der Referenz: SAP HANA SQL und Systemansichten.

### <a name="saphana_disks_cl"></a>SapHana_Disks_CL

Weitere Informationen finden Sie unter [M_DISKS Systemansicht](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20aef7a275191014b37acbc35b4f20a4.html) in der Referenz: SAP HANA SQL und Systemansichten.

### <a name="saphana_systemavailability_cl"></a>SapHana_SystemAvailability_CL

Weitere Informationen finden Sie unter [M_SYSTEM_AVAILABILITY Systemansicht](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.00/en-US/1ef9723a03214bd889c4fb8947765aa4.html) in der Referenz: SAP HANA SQL und Systemansichten.

### <a name="saphana_backupcatalog_cl"></a>SapHana_BackupCatalog_CL

Weitere Informationen finden Sie unter:
- [M_BACKUP_CATALOG_FILES Systemansicht](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20a8437d7519101495a3fa7ad9961cf6.html?q=M_BACKUP_CATALOG)
- [M_BACKUP_CATALOG Systemansicht](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/1.0.12/en-US/20a8100e75191014870ecf908b5d2abf.html)

### <a name="saphana_systemreplication_cl"></a>SapHana_SystemReplication_CL

Weitere Informationen finden Sie unter [M_SERVICE_REPLICATION Systemansicht](https://help.sap.com/viewer/c1d3f60099654ecfb3fe36ac93c121bb/2021_2_QRC/en-US/20c43fc975191014b0ece11b47a86c10.html) in der Referenz: SAP HANA SQL und Systemansichten.
 
### <a name="prometheus_osexporter_cl"></a>Prometheus_OSExporter_CL

Weitere Informationen finden Sie unter [prometheus/node_exporter auf GitHub](https://github.com/prometheus/node_exporter).

### <a name="prometheus_haclusterexporter_cl"></a>Prometheus_HaClusterExporter_CL

Weitere Informationen finden Sie unter [ClusterLabs/ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md).

### <a name="mssql_dbconnections_cl"></a>MSSQL_DBConnections_CL

Weitere Informationen finden Sie unter:
- [sys.dm_exec_sessions (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) 
- [sys.databases (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql)

### <a name="mssql_systemprops_cl"></a>MSSQL_SystemProps_CL

Weitere Informationen finden Sie unter: 
- [sys.dm_os_windows_info (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-windows-info-transact-sql) 
- [sys.database_files (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)
- [sys.dm_exec_sql_text (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)
- [sys.dm_exec_query_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)
- [sys.dm_io_virtual_file_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)
- [sys.dm_db_partition_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql)
- [sys.dm_os_performance_counters (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)
- [sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)
- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)
- [Dynamische Verwaltungssichten in Verbindung mit dem SQL Server-Betriebssystem (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql)
- [sys.availability_groups (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-availability-groups-transact-sql)
- [sys.dm_exec_requests (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)
- [sys.dm_xe_session_targets (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-xe-session-targets-transact-sql)
- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)
- [backupset (Transact-SQL)](/sql/relational-databases/system-tables/backupset-transact-sql)
- [sys.sysprocesses (Transact-SQL)](/sql/relational-databases/system-compatibility-views/sys-sysprocesses-transact-sql)

### <a name="mssql_fileoverview_cl"></a>MSSQL_FileOverview_CL

Weitere Informationen finden Sie unter [sys.database_files (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

### <a name="mssql_memoryoverview_cl"></a>MSSQL_MemoryOverview_CL

Weitere Informationen finden Sie unter [sys.dm_os_memory_clerks (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql).

### <a name="mssql_top10statements_cl"></a>MSSQL_Top10Statements_CL

Weitere Informationen finden Sie unter:
- [sys.dm_exec_sql_text (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)
- [sys.dm_exec_query_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)

### <a name="mssql_ioperformance_cl"></a>MSSQL_IOPerformance_CL

Weitere Informationen finden Sie unter [sys.dm_io_virtual_file_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql).

### <a name="mssql_tablesizes_cl"></a>MSSQL_TableSizes_CL

Weitere Informationen finden Sie unter [sys.dm_db_partition_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql).

### <a name="mssql_batchrequests_cl"></a>MSSQL_BatchRequests_CL

Weitere Informationen finden Sie unter [sys.dm_os_performance_counters (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql).

### <a name="mssql_waitpercs_cl"></a>MSSQL_WaitPercs_CL

Weitere Informationen finden Sie unter [sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="mssql_pagelifeexpectancy2_cl"></a>MSSQL_PageLifeExpectancy2_CL

Weitere Informationen finden Sie unter [sys.dm_os_performance_counters (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql).

### <a name="mssql_error_cl"></a>MSSQL_Error_CL

Weitere Informationen finden Sie unter [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql).

### <a name="mssql_cpuusage_cl"></a>MSSQL_CPUUsage_CL

Weitere Informationen finden Sie unter [Dynamische Verwaltungsansichten in Verbindung mit dem SQL Server-Betriebssystem (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql).

### <a name="mssql_aooverview_cl"></a>MSSQL_AOOverview_CL

Weitere Informationen finden Sie unter [sys.availability_groups (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-availability-groups-transact-sql).

### <a name="mssql_aowaiter_cl"></a>MSSQL_AOWaiter_CL

Weitere Informationen finden Sie unter [sys.dm_exec_requests (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql).

### <a name="mssql_aowaitstats_cl"></a>MSSQL_AOWaitstats_CL

Weitere Informationen finden Sie unter [sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="mssql_aofailovers_cl"></a>MSSQL_AOFailovers_CL

Weitere Informationen finden Sie unter:
- [sys.dm_xe_session_targets (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-xe-session-targets-transact-sql)
- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)

### <a name="mssql_bckbackups2_cl"></a>MSSQL_BckBackups2_CL

Weitere Informationen finden Sie unter [backupset (Transact-SQL)](/sql/relational-databases/system-tables/backupset-transact-sql).

### <a name="mssql_blockingprocesses_cl"></a>MSSQL_BlockingProcesses_CL

Weitere Informationen finden Sie unter [sys.sysprocesses (Transact-SQL)](/sql/relational-databases/system-compatibility-views/sys-sysprocesses-transact-sql).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Verwendung von Azure Monitor für SAP-Lösungen finden Sie unter [Überwachen von SAP in Azure](monitor-sap-on-azure.md).
- Weitere Informationen zu Azure Monitor finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../../../azure-monitor/essentials/monitor-azure-resource.md).
