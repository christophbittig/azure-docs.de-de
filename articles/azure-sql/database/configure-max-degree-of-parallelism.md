---
title: Konfigurieren des maximalen Grads an Parallelität (max degree of parallelism, MAXDOP)
titleSuffix: Azure SQL Database
description: Hier erfahren Sie etwas zum maximalen Grad an Parallelität (max degree of parallelism, MAXDOP).
ms.date: 04/12/2021
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: tsql
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.openlocfilehash: 64eaa7d70d32ce88a98a12a38a5c59e13de58059
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020880"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>Konfigurieren des maximalen Grads an Parallelität (max degree of parallelism, MAXDOP) in Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  In diesem Artikel wird die Konfigurationseinstellung **Max. Grad an Parallelität** (max degree of parallelism, MAXDOP) in Azure SQL-Datenbank beschrieben. 

> [!NOTE]
> **Der folgende Inhalt konzentriert sich auf Azure SQL-Datenbank.** Weil Azure SQL-Datenbank auf der letzten stabilen Version der Microsoft SQL Server-Datenbank-Engine basiert, sind die Inhalte größtenteils ähnlich. Allerdings kann es Unterschiede bei den Problembehandlungs- und Konfigurationsoptionen geben. Weitere Informationen zu MAXDOP in SQL Server finden Sie unter [Konfigurieren der Serverkonfigurationsoption „Max. Grad an Parallelität“](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

## <a name="overview"></a>Übersicht
  Durch MAXDOP wird die abfrageinterne Parallelität in der Datenbank-Engine gesteuert. Höhere MAXDOP-Werte führen in der Regel zu mehr parallelen Threads pro Abfrage und zu einer schnelleren Abfrageausführung. 

  In Azure SQL-Datenbank ist die Standardeinstellung „MAXDOP“ für jede neue Einzeldatenbank (Singleton) und jeden neuen Pool für elastische Datenbanken auf „8“ festgelegt. Diese Standardeinstellung verhindert eine unnötige Ressourcenverwendung und ermöglicht gleichzeitig die schnellere Ausführung von Abfragen durch die Datenbank-Engine dank paralleler Threads. Normalerweise ist es nicht erforderlich, den MAXDOP-Wert in Azure SQL-Datenbank-Workloads weiter zu konfigurieren, es kann jedoch Vorteile als erweiterte Leistungsoptimierung haben.

> [!Note]
>   Im September 2020 wurde nach jahrelanger Telemetrienutzung im Azure SQL-Datenbank-Dienst „MAXDOP 8“ als [Standardwert für neue Datenbanken](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528) festgelegt, da es sich hierbei um den optimalen Wert für verschiedenste Kundenworkloads handelt. Diese Standardeinstellung hat zur Vermeidung von Leistungsproblemen aufgrund von übermäßiger Parallelität beigetragen. Vorher war die Standardeinstellung für neue Datenbanken „MAXDOP 0“. MAXDOP wurde bei Datenbanken, die vor September 2020 erstellt wurden, nicht automatisch geändert.

  Im Allgemeinen ist die Ausführungszeit schneller, wenn die Datenbank-Engine die Ausführung einer Abfrage mit Parallelität auswählt. Allerdings kann übermäßige Parallelität zusätzliche Prozessorressourcen beanspruchen, ohne die Abfrageleistung zu verbessern. Im großen Stil kann sich übermäßige Parallelität negativ auf die Leistung aller Abfragen auswirken, die in der gleichen Datenbank-Engine-Instanz ausgeführt werden. Daher ist das Festlegen einer Obergrenze für die Parallelität eine gängige Leistungsoptimierung in SQL Server-Workloads.

  In der folgenden Tabelle wird das Verhalten der Datenbank-Engine bei der Ausführung von Abfragen mit verschiedenen MAXDOP-Werten beschrieben:

| MAXDOP | Verhalten | 
|--|--|
| = 1 | Abfragen werden von der Datenbank-Engine in einem einzelnen seriellen Thread ausgeführt. Es werden keine parallelen Threads verwendet. | 
| > 1 | Von der Datenbank-Engine wird die Anzahl zusätzlicher [Planer](/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling), die von parallelen Threads verwendet werden sollen, auf den MAXDOP-Wert oder auf die Gesamtanzahl logischer Prozessoren festgelegt (je nachdem, welcher Wert kleiner ist). |
| = 0 | Von der Datenbank-Engine wird die Anzahl zusätzlicher [Planer](/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling), die von parallelen Threads verwendet werden sollen, auf die Gesamtanzahl logischer Prozessoren oder auf „64“ festgelegt (je nachdem, welcher Wert kleiner ist). | 
| | |

> [!Note]
> Jede Abfrage wird mit mindestens einem Planer und einem Arbeitsthread für diesen Planer ausgeführt.
>
> Bei einer mit Parallelität ausgeführten Abfrage werden zusätzliche Planer und zusätzliche parallele Threads verwendet. Da für den gleichen Planer mehrere parallele Threads ausgeführt werden können, kann die Gesamtanzahl von Threads für die Abfrageausführung höher sein als der angegebene MAXDOP-Wert oder die Gesamtanzahl logischer Prozessoren. Weitere Informationen finden Sie unter [Planen von parallelen Tasks](/sql/relational-databases/thread-and-task-architecture-guide#scheduling-parallel-tasks).

##  <a name="considerations"></a><a name="Considerations"></a> Weitere Überlegungen  

-   In Azure SQL-Datenbank können Sie den MAXDOP-Standardwert ändern:
    -   Verwenden Sie auf Abfrageebene hierfür den [Abfragehinweis](/sql/t-sql/queries/hints-transact-sql-query) für **MAXDOP**.     
    -   Auf Datenbankebene verwenden Sie die [auf die Datenbank beschränkte Konfiguration](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) für **MAXDOP**.

-   Überlegungen und [Empfehlungen](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) für einen langfristigen SQL Server-MAXDOP-Wert gelten für Azure SQL-Datenbank. 

-   Indizierungsoperationen, bei denen ein Index erstellt oder neu aufgebaut wird bzw. an deren Ende ein gruppierter Index steht, können ressourcenintensiv sein. Sie können den MAXDOP-Wert der Datenbank für Indexvorgänge außer Kraft setzen, indem Sie die MAXDOP-Indexoption in der Anweisung `CREATE INDEX` oder `ALTER INDEX` angeben. Der Wert MAXDOP wird zur Ausführungszeit auf die Anweisung angewendet und wird nicht in den Metadaten für den Index gespeichert. Weitere Informationen finden Sie unter [Konfigurieren von Parallelindexvorgängen](/sql/relational-databases/indexes/configure-parallel-index-operations).  
  
-   Neben Abfragen und Indexvorgängen steuert die datenbankbezogene Konfigurationsoption auch die Parallelität anderer Anweisungen, bei denen ggf. eine parallele Ausführung verwendet wird. Hierzu zählen etwa „DBCC CHECKTABLE“, „DBCC CHECKDB“ und „DBCC CHECKFILEGROUP“. 

##  <a name="recommendations"></a><a name="Recommendations"></a> Empfehlungen  

  Das Ändern von MAXDOP für die Datenbank kann sich auf die Abfrageleistung und die Ressourcenverwendung erheblich auswirken – sowohl positiv als auch negativ. Es gibt jedoch keinen einzelnen MAXDOP-Wert, der für alle Workloads optimal ist. Die [Empfehlungen](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) zum Festlegen von MAXDOP sind differenziert und hängen von zahlreichen Faktoren ab. 

  Einige gleichzeitige Spitzenworkloads funktionieren mit einem anderen MAXDOP-Wert möglicherweise besser als andere. Ein ordnungsgemäß konfigurierter MAXDOP-Wert verringert das Risiko von Leistungs- und Verfügbarkeitsproblemen und kann in einigen Fällen zur Kostensenkung beitragen, da Sie eine unnötige Ressourcenverwendung vermeiden und somit auf ein niedrigeres Dienstziel herunterskalieren können.

### <a name="excessive-parallelism"></a>Übermäßige Parallelität

  Ein höherer MAXDOP-Wert verkürzt oft die Dauer für CPU-intensive Abfragen. Übermäßige Parallelität kann jedoch die Leistung anderer gleichzeitiger Workloads verschlechtern, indem andere Abfragen der CPU- und Arbeitsthreadressourcen „verhungern“. In Extremfällen kann eine übermäßige Parallelität sämtliche Ressourcen der Datenbank oder des Pools für elastische Datenbanken verbrauchen, wodurch Abfragetimeouts, Fehler und Anwendungsausfälle verursacht werden. 

> [!Tip]
> Kunden sollten den MAXDOP-Wert „0“ auch dann vermeiden, wenn er aktuell offenbar keine Probleme verursacht.

  Übermäßige Parallelität wird äußerst problematisch, wenn mehr gleichzeitige Anforderungen vorhanden sind, als von den durch das Dienstziel bereitgestellten CPU- und Arbeitsthreadressourcen bewältigt werden können. Vermeiden Sie „MAXDOP 0“, um das Risiko potenzieller zukünftiger Probleme aufgrund von übermäßiger Parallelität zu verringern, wenn eine Datenbank zentral hochskaliert wird oder wenn zukünftige Hardwaregenerationen in Azure SQL-Datenbank mehr Kerne für dasselbe Datenbankdienstziel bereitstellen.

### <a name="modifying-maxdop"></a>Ändern von MAXDOP 

  Falls für Ihre Azure SQL-Datenbank-Workload eine andere MAXDOP-Einstellung als die Standardeinstellung optimal ist, können Sie die T-SQL-Anweisung `ALTER DATABASE SCOPED CONFIGURATION` verwenden. Beispiele dazu finden Sie unten im Abschnitt [Beispiele zur Verwendung von Transact-SQL](#examples). Wenn MAXDOP für jede neu erstellte Datenbank auf einen anderen Wert als den Standardwert festgelegt werden soll, fügen Sie diesen Schritt Ihrem Datenbankbereitstellungsprozess hinzu.

  Falls der vom Standardwert abweichende MAXDOP-Wert nur für eine kleine Teilmenge der Abfragen in der Workload von Vorteil ist, können Sie MAXDOP auf Abfrageebene außer Kraft setzen, indem Sie den Hinweis „OPTION (MAXDOP)“ hinzufügen. Beispiele dazu finden Sie unten im Abschnitt [Beispiele zur Verwendung von Transact-SQL](#examples). 

  Testen Sie Ihre MAXDOP-Konfigurationsänderungen gründlich mit Auslastungstests, die realistische gleichzeitige Abfragelasten umfassen. 

  Der MAXDOP-Wert für das primäre und das sekundäre Replikat kann unabhängig konfiguriert werden, falls für Ihre Workloads mit Lese-/Schreibzugriff und Ihre schreibgeschützten Workloads jeweils unterschiedliche MAXDOP-Einstellungen optimal sind. Dies gilt für Azure SQL-Datenbank-Instanzen mit [horizontaler Leseskalierung](read-scale-out.md), [Georeplikation](active-geo-replication-overview.md) und sekundären Replikaten für [Hyperscale](service-tier-hyperscale.md). Standardmäßig erben alle sekundären Replikate die MAXDOP-Konfiguration des primären Replikats.

## <a name="security"></a><a name="Security"></a> Sicherheit  
  
###  <a name="permissions"></a><a name="Permissions"></a> Berechtigungen  
  Die Anweisung `ALTER DATABASE SCOPED CONFIGURATION` muss als Serveradministrator, als Mitglied der Datenbankrolle `db_owner` oder als Benutzer, dem die Berechtigung `ALTER ANY DATABASE SCOPED CONFIGURATION` erteilt wurde, ausgeführt werden.
 
## <a name="examples"></a>Beispiele   

  In diesen Beispielen wird die neueste **AdventureWorksLT**-Beispieldatenbank verwendet, wenn die Option `SAMPLE` für eine neue Einzeldatenbank (Singleton) von Azure SQL-Datenbank ausgewählt wird.

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>Datenbankbezogene MAXDOP-Konfiguration   

  In diesem Beispiel wird gezeigt, wie die Anweisung [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) verwendet wird, um die Konfiguration `MAXDOP` auf `2` festzulegen. Die Einstellung wird für neue Abfragen sofort wirksam. Das PowerShell-Cmdlet [Invoke-SqlCmd](/powershell/module/sqlserver/invoke-sqlcmd) führt die T-SQL-Abfragen aus, um die datenbankbezogene MAXDOP-Konfiguration festzulegen und dann zurückzugeben. 

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP = 8

$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
     SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

Dieses Beispiel ist zur Verwendung bei Azure SQL-Datenbanken mit [aktivierten Replikaten für horizontale Leseskalierung](read-scale-out.md), [Georeplikation](active-geo-replication-overview.md) und [sekundäre Replikate für Hyperscale in Azure SQL-Datenbank ](service-tier-hyperscale.md) vorgesehen. Beispielsweise wird das primäre Replikat auf einen anderen MAXDOP-Standardwert als das sekundäre Replikat festgelegt. Dabei wird erwartet, dass es Unterschiede zwischen einem Workload mit Lese-/Schreibzugriff und einem schreibgeschützten Workload gibt.

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP_primary = 8
$desiredMAXDOP_secondary_readonly = 1
 
$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP_primary + ';
    ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = ' + $desiredMAXDOP_secondary_readonly + ';
    SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

### <a name="transact-sql"></a>Transact-SQL
  
  Sie können mit dem [Azure-Portal-Abfrage-Editor](connect-query-portal.md), [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) oder [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) T-SQL-Abfragen für Ihre Azure SQL-Datenbank ausführen.

1.  Öffnen Sie ein neues Abfragefenster.

2.  Stellen Sie eine Verbindung mit der Datenbank her, in der Sie MAXDOP ändern möchten. Datenbankbezogene Konfigurationen können nicht in der Masterdatenbank geändert werden.
  
3.  Kopieren Sie das folgende Beispiel in das Abfragefenster, und klicken Sie dann auf **Ausführen**. 

#### <a name="maxdop-database-scoped-configuration"></a>Datenbankbezogene MAXDOP-Konfiguration

  In diesem Beispiel wird gezeigt, wie die aktuelle datenbankbezogene MAXDOP-Datenbankkonfiguration mithilfe der Systemkatalogsicht [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) ermittelt wird.

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  In diesem Beispiel wird gezeigt, wie die Anweisung [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) verwendet wird, um die Konfiguration `MAXDOP` auf `8` festzulegen. Die Einstellung wird sofort wirksam.  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

Dieses Beispiel ist für die Verwendung mit Azure SQL-Datenbank-Instanzen mit [aktivierten Replikaten für horizontale Leseskalierung](read-scale-out.md), [Georeplikation](active-geo-replication-overview.md) und sekundären Replikaten für [Hyperscale](service-tier-hyperscale.md) konzipiert. Für das Beispiel wird das primäre Replikat auf einen anderen MAXDOP-Wert festgelegt als das sekundäre Replikat, und es wird erwartet, dass es zwischen Workloads mit Lese-/Schreibzugriff und schreibgeschützten Workloads zu Unterschieden kommt. Alle Anweisungen werden für das primäre Replikat ausgeführt. Die Spalte `value_for_secondary` von `sys.database_scoped_configurations` enthält Einstellungen für das sekundäre Replikat.

```sql
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = 1;
SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

#### <a name="maxdop-query-hint"></a>MAXDOP-Abfragehinweis

  In diesem Beispiel wird gezeigt, wie eine Abfrage mithilfe des Abfragehinweises ausgeführt wird, um für `max degree of parallelism` den Wert `2` zu erzwingen.  

```sql 
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM SalesLT.SalesOrderDetail  
WHERE UnitPrice < 5  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (MAXDOP 2);    
GO
```
#### <a name="maxdop-index-option"></a>MAXDOP-Indexoption

  Dieses Beispiel zeigt, wie Sie einen Index mithilfe der Indexoption neu erstellen, um für `max degree of parallelism` den Wert `12` zu erzwingen.  

```sql 
ALTER INDEX ALL ON SalesLT.SalesOrderDetail 
REBUILD WITH 
   (     MAXDOP = 12
       , SORT_IN_TEMPDB = ON
       , ONLINE = ON);
```

## <a name="see-also"></a>Siehe auch  
* [ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)        
* [sys.database_scoped_configurations (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql)
* [Konfigurieren von Parallelindexvorgängen](/sql/relational-databases/indexes/configure-parallel-index-operations)    
* [Abfragehinweise &#40;Transact-SQL&#41;](/sql/t-sql/queries/hints-transact-sql-query)     
* [Festlegen von Indexoptionen](/sql/relational-databases/indexes/set-index-options)     
* [Verstehen und Beheben von Problemen durch Blockierungen in Azure SQL-Datenbank](understand-resolve-blocking.md)

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen und Optimieren der Leistung](/sql/relational-databases/performance/monitor-and-tune-for-performance)
