---
title: Überwachen Ihrer SQL-Bereitstellungen mit SQL Insights (Vorschau)
description: Übersicht über SQL Insights in Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2021
ms.openlocfilehash: 2b66fe7d268fbb2ad044d0ecc4a5bf0fff5cd17e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297789"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>Überwachen Ihrer SQL-Bereitstellungen mit SQL Insights (Vorschau)
Bei SQL Insights handelt es sich um eine umfassende Lösung zur Überwachung jeglichem Produkt der [Azure SQ- Familie](../../azure-sql/index.yml). SQL Insights verwendet [dynamische Verwaltungssichten](../../azure-sql/database/monitoring-with-dmvs.md), um die Daten offenzulegen, die Sie zur Überwachung der Integrität, zur Diagnose von Problemen und zur Leistungsoptimierung benötigen.  

SQL Insights führt die gesamte Überwachung aus der Ferne durch. Überwachungsagenten auf dedizierten virtuellen Maschinen stellen eine Verbindung zu Ihren SQL-Ressourcen her und sammeln Daten aus der Ferne. Die gesammelten Daten werden in [Azure Monitor Protokollen](../logs/data-platform-logs.md)gespeichert, sodass eine einfache Aggregation, Filterung und Trendanalyse ermöglicht wird. Sie können die gesammelten Daten aus der SQL Insights-[Arbeitsmappenvorlage](../visualize/workbooks-overview.md) anzeigen, oder Sie können direkt in die Daten eintauchen, indem Sie [Protokollabfragen verwenden](../logs/get-started-queries.md).

## <a name="pricing"></a>Preise
Es gibt keine direkten Kosten für SQL Insights. Alle Kosten werden durch die virtuellen Computer, von denen die Daten gesammelt werden, durch die Log Analytics Arbeitsbereiche, in denen die Daten gespeichert werden, sowie für alle für die Daten konfigurierten Warnungs Regeln anfallen. 

### <a name="virtual-machines"></a>Virtuelle Computer

Im Falle virtueller Maschinen werden Sie auf der Grundlage der auf der Preisseite für [virtuelle Maschinen](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) veröffentlichten Preise belastet. Die Anzahl erforderlicher virtueller Computer variiert je nach der Anzahl der Verbindungszeichenfolgen, die Sie überwachen möchten. Es wird empfohlen, für jeweils 100 Verbindungszeichenfolgen einen virtuellen Computer der Größe Standard_B2s zuzuweisen. Weitere Informationen finden Sie unter [Anforderungen an virtuelle Azure-Computer](sql-insights-enable.md#azure-virtual-machine-requirements).

### <a name="log-analytics-workspaces"></a>Log Analytics-Arbeitsbereiche

Für die Log Analytics-Arbeitsbereiche werden Sie auf der Grundlage der auf der Azure Monitor-Preisseite veröffentlichten Preise belastet, die auf der [Seite Azure Monitor Preise](https://azure.microsoft.com/pricing/details/monitor/)veröffentlicht werden. Die von SQL Insights verwendeten Log Analytics-Arbeitsbereiche verursachen Kosten für die Datenerfassung, Datenaufbewahrung und (optional) den Datenexport. 

Die genauen Kosten variieren je nach der erfassten, beibehaltenen und exportierten Datenmenge. Die Menge dieser Daten hängt von Ihrer Datenbankaktivität und den in Ihren [Überwachungsprofilen](sql-insights-enable.md#create-sql-monitoring-profile) definierten Erfassungseinstellungen ab.

### <a name="alert-rules"></a>Warnregeln

Für die Log Analytics-Arbeitsbereiche werden Sie auf der Grundlage der auf der Azure Monitor-Preisseite veröffentlichten Preise belastet, die auf der [Seite Azure Monitor-Preisgestaltung](https://azure.microsoft.com/pricing/details/monitor/) veröffentlicht werden. Wenn Sie [Warnungen mit SQL Insights erstellen](sql-insights-alerts.md), werden Ihnen alle erstellten Warnungsregeln und gesendeten Benachrichtigungen in Rechnung gestellt.

## <a name="supported-versions"></a>Unterstützte Versionen 
SQL Insights unterstützt die folgenden Versionen von SQL Server:
- SQL Server 2012 und höher

SQL Insights unterstützt SQL Server in den folgenden Umgebungen:
- Azure SQL-Datenbank
- Verwaltete Azure SQL-Instanz
- SQL Server auf virtuellen Azure-Maschinen (SQL Server wird auf virtuellen Maschinen ausgeführt, die beim Anbieter von [virtuellen SQL-Maschinen](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) registriert sind)
- Azure-VMS (SQL Server, die auf virtuellen Computern ausgeführt werden, und die nicht beim [SQL-VM](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)-Anbieter registriert sind)

SQL Insights bietet keine oder nur eingeschränkte Unterstützung für Folgendes:
- **Nicht-Azure-Instanzen**: SQL Server-Instanzen, die auf VMs außerhalb von Azure ausgeführt werden, werden nicht unterstützt.
- **Pools für elastische Datenbanken von Azure SQL-Datenbank**: Metriken können nicht für Pools für elastische Datenbanken oder Datenbanken in Pools für elastische Datenbanken erfasst werden.
- **Niedrige Dienstebenen von Azure SQL-Datenbank**: Metriken können für Datenbanken auf den [Dienstebenen](../../azure-sql/database/resource-limits-dtu-single-databases.md) Basic, S0, S1 und S2 nicht erfasst werden.
- **Serverlose Ebene von Azure SQL-Datenbank**: Metriken können für Datenbanken über die serverlose Computeebene erfasst werden. Durch das Erfassen von Metriken wird jedoch der Verzögerungstimer für automatische Pausierung zurückgesetzt, wodurch verhindert wird, dass die Datenbank in einen automatischen Pausenzustand übergeht.
- **Sekundäre Replikate**: Metriken können nur für ein einzelnes sekundäres Replikat pro Datenbank erfasst werden. Wenn eine Datenbank über mehrere sekundäre Replikate verfügt, kann nur ein Replikat überwacht werden.
- **Authentifizierung mit Azure Active Directory**: Die einzige unterstützte [Authentifizierungs-](../../azure-sql/database/logins-create-manage.md#authentication-and-authorization)Methode für die Überwachung ist die SQL-Authentifizierung. Für SQL Server unter Azure Virtual Machines wird Authentifizierung über Active Directory auf einem benutzerdefinierten Domänencontroller nicht unterstützt.  

## <a name="regional-availability"></a>Regionale Verfügbarkeit

SQL Einblicke sind in allen Azure-Regionen verfügbar, in denen Azure Monitor [verfügbar](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) ist, mit Ausnahme von Azure Government und nationalen Clouds.

## <a name="opening-sql-insights"></a>Öffnen von SQL Insights

So öffnen Sie SQL Insights:

1. Navigieren Sie im Azure-Portal zum Menü **Azure Monitor**.
1. Wählen Sie im Abschnitt **Insights** die Option **SQL (Vorschau)** aus. 
1. Wählen Sie eine Kachel aus, um die Benutzeroberfläche für die SQL-Ressource zu laden, die Sie überwachen.

:::image type="content" source="media/sql-insights/portal.png" alt-text="Screenshot: SQL Insights im Azure-Portal.":::

Weitere Anweisungen finden Sie unter [Aktivieren von SQL Insights](sql-insights-enable.md) und [Problembehandlung SQL Insights](sql-insights-troubleshoot.md).

## <a name="collected-data"></a>Gesammelte Daten
SQL Insights führt die gesamte Überwachung aus der Ferne durch. Auf den virtuellen Computern, auf denen SQL Server ausgeführt wird, sind keine Agents installiert. 

SQL Insights verwendet dedizierte virtuelle Maschinen zur Überwachung, um Daten von Ihren SQL-Ressourcen aus der Ferne zu sammeln. Auf jedem überwachenden virtuellen Computer werden der [Azure Monitor-Agent](../agents/azure-monitor-agent-overview.md) und die Workload Insights-Erweiterung (WLI) installiert. 

Die WLI-Erweiterung beinhaltet den Open-Source-[Telegraf-Agent](https://www.influxdata.com/time-series-platform/telegraf/). SQL Insights verwendet [Datenerfassungsregeln](../agents/data-collection-rule-overview.md), um die Datenerfassungseinstellungen für das [SQL Server-Plug-In](https://www.influxdata.com/integration/microsoft-sql-server/) von Telegraf festzulegen.

Für Azure SQL Database, Azure SQL Managed Instance und den SQL Server sind unterschiedliche Datensätze verfügbar. In den folgenden Tabellen werden die verfügbaren Daten beschrieben. Bei der [Erstellung eines Überwachungsprofils](sql-insights-enable.md#create-sql-monitoring-profile) können Sie festlegen, welche Datasets erfasst werden sollen und wie häufig dies geschehen soll.

Die Tabellen weisen die folgenden Spalten auf:
- **Anzeigename**: Name der Abfrage, wie im Azure-Portal beim Erstellen eines Überwachungsprofils angezeigt.
- **Konfigurationsname**: Name der Abfrage, wie im Azure-Portal beim Bearbeiten eines Überwachungsprofils angezeigt.
- **Namespace**: Namen der Abfrage, wie er in einem Log Analytics-Arbeitsbereich zu finden ist. Dieser Bezeichner wird in der Tabelle **InsightsMetrics** für die `Namespace`-Eigenschaft in der `Tags`-Spalte angezeigt.
- **DMVs**: Die dynamisch verwalteten Sichten, die zum Generieren des Datasets verwendet werden.
- **Standardmäßig aktiviert**: Gibt an, ob die Daten standardmäßig erfasst werden.
- **Standarderfassungshäufigkeit**: Gibt an, wie häufig die Daten standardmäßig erfasst werden.

### <a name="data-for-azure-sql-database"></a>Daten für die Azure SQL Datenbank 
| Angezeigter Name | Konfigurationsname | Namespace | DMVs | Standardmäßig aktiviert | Standardmäßige Erfassungshäufigkeit |
|:---|:---|:---|:---|:---|:---|
| Standard-Sammelhäufigkeit | AzureSQLDBWaitStats | sqlserver_azuredb_waitstats | sys.dm_db_wait_stats | Nein | Nicht zutreffend |
| DBO-Wartestatistiken | AzureSQLDBOsWaitstats | sqlserver_waitstats |sys.dm_os_wait_stats | Ja | 60 Sekunden |
| Arbeitsspeicherclerks | AzureSQLDBMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Ja | 60 Sekunden |
| Datenbank-E/A | AzureSQLDBDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys.database_files | Ja | 60 Sekunden |
| Servereigenschaften | AzureSQLDBServerProperties | sqlserver_server_properties | sys.dm_os_job_object<br>sys.database_files<br>sys.[databases]<br>sys.[database_service_objectives] | Ja | 60 Sekunden |
| Leistungsindikatoren | AzureSQLDBPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases | Ja | 60 Sekunden |
| Ressourcenstatistiken | AzureSQLDBResourceStats | sqlserver_azure_db_resource_stats | sys.dm_db_resource_stats | Ja | 60 Sekunden |
| Ressourcengovernance | AzureSQLDBResourceGovernance | sqlserver_db_resource_governance | sys.dm_user_db_resource_governance | Ja | 60 Sekunden |
| Requests | AzureSQLDBRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | Nein | Nicht zutreffend |
| Zeitplaner| AzureSQLDBSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | Nein | Nicht zutreffend  |

### <a name="data-for-azure-sql-managed-instance"></a>Daten für die Azure SQL Verwaltete Instanz 

| Angezeigter Name | Konfigurationsname | Namespace | DMVs | Standardmäßig aktiviert | Standardmäßige Erfassungshäufigkeit |
|:---|:---|:---|:---|:---|:---|
| Wartestatistik | AzureSQLMIOsWaitstats | sqlserver_waitstats | sys.dm_os_wait_stats | Ja | 60 Sekunden |
| Arbeitsspeicherclerks | AzureSQLMIMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Ja | 60 Sekunden |
| Datenbank-E/A | AzureSQLMIDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Ja | 60 Sekunden |
| Servereigenschaften | AzureSQLMIServerProperties | sqlserver_server_properties | sys.server_resource_stats | Ja | 60 Sekunden |
| Leistungsindikatoren | AzureSQLMIPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases| Ja | 60 Sekunden |
| Ressourcenstatistiken | AzureSQLMIResourceStats | sqlserver_azure_db_resource_stats | sys.server_resource_stats | Ja | 60 Sekunden |
| Ressourcengovernance | AzureSQLMIResourceGovernance | sqlserver_instance_resource_governance | sys.dm_instance_resource_governance | Ja | 60 Sekunden |
| Requests | AzureSQLMIRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | Nein | Nicht verfügbar |
| Zeitplaner | AzureSQLMISchedulers | sqlserver_schedulers | sys.dm_os_schedulers | Nein | Nicht zutreffend |

### <a name="data-for-sql-server"></a>Daten für den SQL Server

| Angezeigter Name | Konfigurationsname | Namespace | DMVs | Standardmäßig aktiviert | Standardmäßige Erfassungshäufigkeit |
|:---|:---|:---|:---|:---|:---|
| Wartestatistik | SQLServerWaitStatsCategorized | sqlserver_waitstats | sys.dm_os_wait_stats | Ja | 60 Sekunden | 
| Arbeitsspeicherclerks | SQLServerMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Ja | 60 Sekunden |
| Datenbank-E/A | SQLServerDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Ja | 60 Sekunden |
| Servereigenschaften | SQLServerProperties | sqlserver_server_properties | sys.dm_os_sys_info | Ja | 60 Sekunden |
| Leistungsindikatoren | SQLServerPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters | Ja | 60 Sekunden |
| Volumenraum | SQLServerVolumeSpace | sqlserver_volume_space | sys.master_files | Ja | 60 Sekunden |
| SQL-Server-CPU | SQLServerCpu | sqlserver_cpu | sys.dm_os_ring_buffers | Ja | 60 Sekunden |
| Zeitplaner | SQLServerSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | Nein | Nicht zutreffend |
| Requests | SQLServerRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | Nein | Nicht zutreffend |
| Verfügbarkeit Replikatstatus | SQLServerAvailabilityReplicaStates | sqlserver_hadr_replica_states | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | Nein | 60 Sekunden |
| Verfügbarkeit Datenbankreplikate | SQLServerDatabaseReplicaStates | sqlserver_hadr_dbreplica_states | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | Nein | 60 Sekunden |

## <a name="next-steps"></a>Nächste Schritte

- Unter [Häufig gestellte Fragen](../faq.yml) finden Sie häufig gestellte Fragen zu SQL Insights.
