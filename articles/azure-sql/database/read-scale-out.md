---
title: Lesen von Abfragen auf Replikaten
description: Azure SQL bietet die Möglichkeit, Workloads mithilfe der Kapazität von schreibgeschützten Replikaten zu lesen. Dies wird als horizontale Leseskalierung bezeichnet.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 09/23/2021
ms.openlocfilehash: f5acdf621c04ba48664004bbb1f28ae1c0914fcb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447035"
---
# <a name="use-read-only-replicas-to-offload-read-only-query-workloads"></a>Verwenden von schreibgeschützten Replikaten zum Lesen schreibgeschützter Abfrageworkloads
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Als Teil der [Hochverfügbarkeitsarchitektur](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) wird jede Datenbank, jeder Pool für elastische Datenbanken und jede verwaltete Instanz der Dienstebenen „Premium“ und „Unternehmenskritisch“ automatisch mit einem primären Replikat mit Lese-/Schreibzugriff und mehreren sekundären schreibgeschützten Replikaten bereitgestellt. Die sekundären Replikate werden mit derselben Computegröße wie das primäre Replikat bereitgestellt. Die *horizontale Leseskalierung* ermöglicht es Ihnen, schreibgeschützte Workloads mithilfe der Computekapazität eines der schreibgeschützten Replikate auszulagern, anstatt sie auf dem Replikat mit Lese-/Schreibzugriff auszuführen. Auf diese Weise können einige schreibgeschützte Workloads von der Hauptworkload mit Lesen- und Schreibvorgängen isoliert werden, ohne ihre Leistung zu beeinträchtigen. Die Funktion ist für Anwendungen vorgesehen, die logisch getrennte, schreibgeschützte Workloads (z. B. zur Analyse) enthalten. In den Diensttarifen Premium und Unternehmenskritisch können Anwendungen Leistungsvorteile erzielen, die diese zusätzliche Kapazität ohne zusätzliche Kosten nutzen.

Die *horizontale Leseskalierung* ist auch auf der Dienstebene „Hyperscale“ verfügbar, wenn mindestens ein [sekundäres Replikat](service-tier-hyperscale-replicas.md) hinzugefügt wird. Sekundäre [benannte Hyperscale-Replikate](service-tier-hyperscale-replicas.md#named-replica-in-preview) bieten unabhängige Skalierung, Zugriffsisolation, Workloadisolation, umfassende horizontale Leseskalierung und weitere Vorteile. Mehrere sekundäre [Hochverfügbarkeitsreplikate](service-tier-hyperscale-replicas.md#high-availability-replica) können für einen Lastenausgleich schreibgeschützter Workloads verwendet werden, für die mehr Ressourcen erforderlich sind, als auf einem sekundären Hochverfügbarkeitsreplikat zur Verfügung stehen. 

Die Hochverfügbarkeitsarchitektur der Dienstebenen „Basic“, „Standard“ und „Universell“ enthält keine Replikate. Die *horizontale Leseskalierung* ist auf diesen Dienstebenen nicht verfügbar.

Im folgenden Diagramm wird die Funktion für Datenbanken und verwaltete Instanzen in den Tarifen „Premium“ und „Unternehmenskritisch“ veranschaulicht.

![Schreibgeschützte Replikate](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

Die *horizontale Leseskalierung* ist bei Datenbanken in den Tarifen Premium, Unternehmenskritisch und Hyperscale standardmäßig aktiviert.

> [!NOTE]
> Die horizontale Leseskalierung ist auf der Dienstebene „Unternehmenskritisch“ von Managed Instance und für Hyperscale-Datenbanken mit mindestens einem sekundären Replikat immer aktiviert.

Wenn Ihre SQL-Verbindungszeichenfolge mit `ApplicationIntent=ReadOnly` konfiguriert wurde, wird die Anwendung zu einem schreibgeschützten Replikat dieser Datenbank oder verwalteten Instanz umgeleitet. Informationen zur Verwendung der `ApplicationIntent`-Eigenschaft finden Sie unter [Angeben der Anwendungsabsicht](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Wenn Sie sicherstellen möchten, dass die Anwendung unabhängig von der Einstellung `ApplicationIntent` in der SQL-Verbindungszeichenfolge eine Verbindung mit dem primären Replikat herstellt, müssen Sie die horizontale Leseskalierung beim Erstellen der Datenbank oder beim Ändern ihrer Konfiguration explizit deaktivieren. Wenn Sie Ihre Datenbank z. B. vom Tarif „Standard“ oder „Universell“ auf den Tarif „Premium“ oder „Unternehmenskritisch“ upgraden und sicherstellen möchten, dass weiterhin alle Verbindungen zum primären Replikat führen, deaktivieren Sie die horizontale Leseskalierung. Weitere Informationen zum Deaktivieren dieser Funktion finden Sie unter [Aktivieren und Deaktivieren der horizontalen Leseskalierung](#enable-and-disable-read-scale-out).

> [!NOTE]
> Abfragespeicher und SQL Profiler werden in schreibgeschützten Replikaten nicht unterstützt. 

## <a name="data-consistency"></a>Datenkonsistenz

Am primären Replikat vorgenommene Datenänderungen werden asynchron an schreibgeschützte Replikate weitergegeben. Innerhalb einer Sitzung, die mit einem schreibgeschützten Replikat verbunden ist, sind Lesevorgänge immer transaktionskonsistent. Da die Latenzzeit für die Datenweitergabe jedoch variabel ist, können verschiedene Replikate Daten zu geringfügig unterschiedlichen Zeitpunkten im Vergleich zum primären Replikat und zueinander zurückgeben. Wenn ein schreibgeschütztes Replikat nicht mehr verfügbar ist und die Sitzung erneut verbunden wird, kann sie sich mit einem Replikat verbinden, das sich an einem anderen Zeitpunkt befindet als das ursprüngliche Replikat. Ebenso ist es möglich, dass, wenn eine Anwendung Daten in einer Sitzung mit Lese-/Schreibzugriff ändert und sie sofort in einer schreibgeschützten Sitzung liest, die letzten Änderungen im schreibgeschützten Replikat nicht sofort sichtbar sind.

Die typische Latenz bei der Datenweitergabe zwischen dem primären Replikat und schreibgeschützten Replikaten liegt im Bereich von einigen Dutzend Millisekunden bis zu einstelligen Sekunden. Es gibt jedoch für die Datenweitergabelatenz keine feste Obergrenze. Bedingungen wie eine hohe Ressourcenauslastung im Replikat können die Latenz erheblich erhöhen. Anwendungen, die sitzungsübergreifende Datenkonsistenz erfordern oder bei denen committete Daten sofort lesbar sein müssen, müssen das primäre Replikat verwenden.

> [!NOTE]
> Informationen zum Überwachen der Datenweitergabelatenz finden Sie unter [Überwachung und Problembehandlung bei schreibgeschützten Replikaten](#monitoring-and-troubleshooting-read-only-replicas).

## <a name="connect-to-a-read-only-replica"></a>Herstellen einer Verbindung mit einem schreibgeschützten Replikat

Wenn Sie die horizontale Leseskalierung für eine Datenbank aktivieren, bestimmt die Option `ApplicationIntent` in der vom Client bereitgestellten Verbindungszeichenfolge, ob eine Verbindung an das Replikat mit Schreibzugriff oder an ein schreibgeschütztes Replikat weitergeleitet wird. Wenn `ApplicationIntent` den Wert `ReadWrite` aufweist (Standardwert), wird die Verbindung an das Replikat mit Lese-/Schreibzugriff weitergeleitet. Dies ist identisch mit dem Verhalten, wenn `ApplicationIntent` nicht in der Verbindungszeichenfolge enthalten ist. Wenn der `ApplicationIntent`-Wert `ReadOnly` lautet, wird die Verbindung an ein schreibgeschütztes Replikat weitergeleitet.

Ein Beispiel: Die folgende Verbindungszeichenfolge verbindet den Client mit einem schreibgeschützten Replikat (ersetzen Sie die Elemente in spitzen Klammern durch die richtigen Werte für Ihre Umgebung, und löschen Sie die Klammern):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Jede der folgenden Verbindungszeichenfolgen verbindet den Client mit einem Replikat mit Lese-/Schreibzugriff (ersetzen Sie die Elemente in spitzen Klammern durch die richtigen Werte für Ihre Umgebung, und löschen Sie die Klammern):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Sicherstellen einer Verbindung mit einem schreibgeschützten Replikat

Sie können durch Ausführen der folgenden Abfrage im Kontext Ihrer Datenbank überprüfen, ob Sie mit einem schreibgeschützten Replikat verbunden sind. Bei einer Verbindung mit einem schreibgeschützten Replikat wird READ_ONLY zurückgegeben.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability');
```

> [!NOTE]
> Auf den Dienstebenen Premium und Unternehmenskritisch ist jeweils nur eines der schreibgeschützten Replikate verfügbar. Bei Hyperskalierung werden mehrere schreibgeschützte Replikate unterstützt.

## <a name="monitoring-and-troubleshooting-read-only-replicas"></a>Überwachung und Problembehandlung bei schreibgeschützten Replikaten

Wenn eine Verbindung mit einem schreibgeschützten Replikat besteht, spiegeln dynamische Verwaltungssichten (Dynamic Management Views, DMVs) den Status des Replikats wider und können zu Überwachungs- und Problembehandlungszwecken abgefragt werden. Die Datenbank-Engine stellt mehrere Sichten bereit, über die eine Vielzahl von Überwachungsdaten verfügbar gemacht werden kann. 

Die folgenden Sichten werden häufig für die Überwachung und Problembehandlung von Replikaten verwendet:

| Name | Zweck |
|:---|:---|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Bietet Metriken zur Ressourcennutzung für die letzte Stunde, einschließlich CPU-, Daten-E/A- und Protokollschreibauslastung bezogen auf Dienstziellimits.|
|[sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)| Stellt Statistiken zu aggregierten Wartevorgängen für die Datenbank-Engine-Instanz bereit. |
|[sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database)| Bietet Statistiken zum Replikatintegritätsstatus und zur Synchronisierung. Die Größe der Wiederholungswarteschlange und die Wiederholungsrate dienen als Hinweise auf die Datenweitergabelatenz im schreibgeschützten Replikat. |
|[sys.dm_os_performance_counters](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)| Bietet Leistungsindikatoren für die Datenbank-Engine.|
|[sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)| Bietet Ausführungsstatistiken pro Abfrage, z. B. Anzahl von Ausführungen, verwendete CPU-Zeit usw.|
|[sys.dm_exec_query_plan()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql)| Bietet zwischengespeicherte Abfragepläne. |
|[sys.dm_exec_sql_text()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)| Stellt Abfragetext für einen zwischengespeicherten Abfrageplan bereit.|
|[sys.dm_exec_query_profiles](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| Stellt den Abfragefortschritt einer ausgeführten Abfrage in Echtzeit bereit.|
|[sys.dm_exec_query_plan_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| Stellt den letzten bekannten tatsächlichen Ausführungsplan einschließlich Laufzeitstatistiken für eine Abfrage bereit.|
|[sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)| Bietet Statistiken zu IOPS, Durchsatz und Latenz der Speicherung für alle Datenbankdateien. |

> [!NOTE]
> Die DMVs `sys.resource_stats` und `sys.elastic_pool_resource_stats` in der logischen Masterdatenbank geben Ressourcennutzungsdaten des primären Replikats zurück.

### <a name="monitoring-read-only-replicas-with-extended-events"></a>Überwachen von schreibgeschützten Replikaten mit erweiterten Ereignissen

Eine Sitzung für erweiterte Ereignisse kann nicht über eine Verbindung mit einem schreibgeschützten Replikat erstellt werden. In Azure SQL-Datenbank werden die Definitionen von datenbankweit gültigen [erweiterten Sitzungen](xevent-db-diff-from-svr.md), die auf dem primären Replikat erstellt und geändert werden, in schreibgeschützte Replikate repliziert, darunter Georeplikate, und Ereignisse werden in schreibgeschützten Replikaten repliziert.

Eine Sitzung für erweiterte Ereignisse auf einem schreibgeschützten Replikat, die auf einer Sitzungsdefinition aus dem primären Replikat basiert, kann unabhängig vom primären Replikat gestartet und beendet werden. Wenn eine Sitzung für erweiterte Ereignisse auf dem primären Replikat gelöscht wird, wird sie auch in allen schreibgeschützten Replikaten gelöscht.

### <a name="transaction-isolation-level-on-read-only-replicas"></a>Transaktionsisolationsebene auf schreibgeschützten Replikaten

Abfragen, die auf schreibgeschützten Replikaten ausgeführt werden, werden immer der Transaktionsisolationsebene [Momentaufnahme](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server) zugeordnet. Die Momentaufnahmeisolation verwendet Zeilenversionsverwaltung, um Blockierungsszenarien zu vermeiden, in denen Reader Writer blockieren.

Wenn eine Transaktion mit Momentaufnahmeisolation auf Objektmetadaten zugreift, die in einer anderen gleichzeitig stattfindenden Transaktion geändert wurden, kann in seltenen Fällen der Fehler [3961](/sql/relational-databases/errors-events/mssqlserver-3961-database-engine-error) auftreten: „Fehler bei der Momentaufnahme-Isolationstransaktion in der %.*ls-Datenbank, weil das von der Anweisung zugegriffene Objekt durch eine DDL-Anweisung in einer anderen gleichzeitigen Transaktion seit dem Beginn dieser Transaktion geändert wurde. Der Vorgang ist nicht zulässig, weil für die Metadaten keine Versionsverwaltung durchgeführt wird. Das gleichzeitige Update von Metadaten kann in Kombination mit der Momentaufnahmeisolation zu Inkonsistenzen führen.“

### <a name="long-running-queries-on-read-only-replicas"></a>Abfragen mit langer Ausführungszeit für schreibgeschützte Replikate

Abfragen, die für schreibgeschützte Replikate ausgeführt werden, müssen auf die Metadaten für die in der Abfrage referenzierten Objekte (Tabellen, Indizes, Statistiken usw.) zugreifen. Wenn Objektmetadaten im primären Replikat geändert werden, während eine Abfrage eine Sperre für das entsprechende Objekt im schreibgeschützten Replikat enthält, kann die Abfrage in seltenen Fällen den Prozess [blockieren](/sql/database-engine/availability-groups/windows/troubleshoot-primary-changes-not-reflected-on-secondary#BKMK_REDOBLOCK), der Änderungen vom primären Replikat auf das schreibgeschützte Replikat anwendet. Wenn eine solche Abfrage über einen längeren Zeitraum ausgeführt wird, führt dies dazu, dass das schreibgeschützte Replikat mit dem primären Replikat nicht mehr synchronisiert ist. Bei Replikaten, die potenzielle Failoverziele sind (sekundäre Replikate auf den Dienstebenen „Premium“ und „Unternehmenskritisch“, Hyperscale-Hochverfügbarkeitsreplikate und alle Georeplikate), würde dies im Falle eines Failovers auch die Datenbankwiederherstellung verzögern, was zu längeren Ausfallzeiten als erwartet führen würde.

Wenn eine Abfrage mit langer Ausführungszeit für ein schreibgeschützten Replikat diese Art von Blockierung direkt oder indirekt verursacht, wird sie möglicherweise automatisch beendet, um übermäßige Datenlatenz und potenzielle Auswirkungen auf die Datenbankverfügbarkeit zu vermeiden. Die Sitzung erhält den Fehler 1219, „Die Sitzung wurde aufgrund eines DDL-Vorgangs hoher Priorität getrennt“ oder den Fehler 3947, „Die Transaktion wurde abgebrochen, da die sekundäre Compute-Instanz die Wiederholung nicht aufholen konnte. Wiederholen Sie die Transaktion.“

> [!NOTE]
> Wenn beim Ausführen von Abfragen für ein schreibgeschütztes Replikat einer der Fehler 3961, 1219 oder 3947 angezeigt wird, wiederholen Sie die Abfrage. Vermeiden Sie alternativ Vorgänge, bei denen Objektmetadaten (Schemaänderungen, Indexwartung, Statistikupdates usw.) im primären Replikat geändert werden, während Abfragen mit langer Ausführungszeit für sekundäre Replikate ausgeführt werden.

> [!TIP]
> Wenn eine Verbindung mit einem schreibgeschützten Replikat besteht, können auf den Dienstebenen „Premium“ und „Unternehmenskritisch“ die Spalten `redo_queue_size` und `redo_rate` in der DMV [sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database) zum Überwachen der Datensynchronisierung verwendet werden und dadurch als Indikatoren für die Datenweitergabelatenz beim schreibgeschützten Replikat herangezogen werden.
> 

## <a name="enable-and-disable-read-scale-out"></a>Aktivieren und Deaktivieren der horizontalen Leseskalierung

Die horizontale Leseskalierung ist bei den Dienstebenen Premium, Unternehmenskritisch und Hyperscale standardmäßig aktiviert. Für die Dienstebenen Basic, Standard oder Universell kann die horizontale Leseskalierung nicht aktiviert werden. Für Hyperscale-Datenbanken, die ohne sekundäre Replikate konfiguriert sind, wird die horizontale Leseskalierung automatisch deaktiviert.

Sie können die horizontale Leseskalierung für Singletons und Pools für elastische Datenbanken auf den Dienstebenen Premium oder Unternehmenskritisch mithilfe der folgenden Methoden deaktivieren und erneut aktivieren.

> [!NOTE]
> Für Singletons und Pools für elastische Datenbanken wird die Möglichkeit, die horizontale Leseskalierung zu deaktivieren, aus Gründen der Abwärtskompatibilität bereitgestellt. Für verwaltete Instanzen der Dienstebene Unternehmenskritisch kann die horizontale Leseskalierung nicht deaktiviert werden.

### <a name="azure-portal"></a>Azure-Portal

Sie verwalten die Einstellung für die horizontale Leseskalierung auf dem Datenbankblatt **Konfigurieren**.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> Das Azure Resource Manager-Modul von PowerShell wird weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Für das Azure Resource Manager-Modul werden mindestens bis Dezember 2020 weiterhin Fehlerbehebungen veröffentlicht.  Die Argumente für die Befehle im Az-Modul und den Azure Resource Manager-Modulen sind im Wesentlichen identisch. Weitere Informationen zur Kompatibilität finden Sie in der [Einführung in das neue Azure PowerShell Az-Modul](/powershell/azure/new-azureps-module-az).

Für die Verwaltung der horizontalen Leseskalierung in Azure PowerShell ist das Azure PowerShell-Release von Dezember 2016 oder eine neuere Version erforderlich. Das neueste PowerShell-Release finden Sie unter [Azure PowerShell](/powershell/azure/install-az-ps).

Sie aktivieren oder deaktivieren die horizontale Leseskalierung in Azure PowerShell, indem Sie das Cmdlet [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) aufrufen und für den Parameter `-ReadScale` den gewünschten Wert übergeben: `Enabled` oder `Disabled`.

So deaktivieren Sie die horizontale Leseskalierung für eine vorhandene Datenbank (ersetzen Sie die Elemente in spitzen Klammern durch die richtigen Werte für Ihre Umgebung, und löschen Sie die Klammern)

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

So deaktivieren Sie die horizontale Leseskalierung für eine neue Datenbank (ersetzen Sie die Elemente in spitzen Klammern durch die richtigen Werte für Ihre Umgebung, und löschen Sie die Klammern)

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

So aktivieren Sie die horizontale Leseskalierung für eine vorhandene Datenbank erneut (ersetzen Sie die Elemente in spitzen Klammern durch die richtigen Werte für Ihre Umgebung, und löschen Sie die Klammern)

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST-API

Verwenden Sie zum Erstellen einer Datenbank mit deaktivierter horizontaler Leseskalierung oder zum Ändern der Einstellung für eine vorhandene Datenbank die folgende Methode. Legen Sie dabei die `readScale`-Eigenschaft wie in der folgenden Beispielanforderung gezeigt auf `Enabled` oder `Disabled` fest.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Weitere Informationen finden Sie unter [Databanken – Erstellen oder Aktualisieren](/rest/api/sql/databases/createorupdate).

## <a name="using-the-tempdb-database-on-a-read-only-replica"></a>Verwenden der Datenbank `tempdb` auf einem schreibgeschützten Replikat

Die Datenbank `tempdb` auf dem primären Replikat wird nicht in die schreibgeschützten Replikate repliziert. Jedes Replikat verfügt über eine eigene Datenbank `tempdb`, die beim Erstellen des Replikats erstellt wird. Dadurch wird sichergestellt, dass `tempdb` aktualisiert und während der Abfrageausführung geändert werden kann. Wenn Ihre schreibgeschützte Workload von der Verwendung von `tempdb`-Objekten abhängig ist, sollten Sie diese Objekte als Teil derselben Workload erstellen, während eine Verbindung mit einem schreibgeschützten Replikat besteht.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Verwenden der horizontalen Leseskalierung mit georeplizierten Datenbanken

Georeplizierte sekundäre Datenbanken weisen dieselbe Hochverfügbarkeitsarchitektur wie primäre Datenbanken auf. Wenn Sie eine Verbindung mit der georeplizierten sekundären Datenbank mit aktivierter horizontaler Leseskalierung herstellen, werden Ihre Sitzungen mit `ApplicationIntent=ReadOnly` ebenso an eines der Replikate mit Hochverfügbarkeit weitergeleitet wie an die primäre Datenbank mit Lese-/Schreibzugriff. Die Sitzungen ohne `ApplicationIntent=ReadOnly` werden an das primäre Replikat der georeplizierten sekundären Datenbank weitergeleitet, das ebenfalls schreibgeschützt ist. 

Auf diese Weise können durch die Erstellung eines Georeplikats mehrere zusätzliche schreibgeschützte Replikate für eine primäre Datenbank mit Lese-/Schreibzugriff bereitgestellt werden. Jedes zusätzliche Georeplikat stellt einen weiteren Satz schreibgeschützter Replikate bereit. Georeplikate können in jeder Azure-Region erstellt werden, einschließlich der Region der primären Datenbank.

> [!NOTE]
> Es gibt kein automatisches Roundrobin oder ein anderes Lastenausgleichsrouting zwischen den Replikaten einer georeplizierten sekundären Datenbank, mit Ausnahme eines Hyperscale-Georeplikats mit mehr als einem Hochverfügbarkeitsreplikat. In diesem Fall werden Sitzungen mit schreibgeschützter Absicht auf alle Hochverfügbarkeitsreplikate eines Georeplikats verteilt.

## <a name="feature-support-on-read-only-replicas"></a>Featureunterstützung für schreibgeschützte Replikate

Im Folgenden finden Sie eine Liste von Verhaltensweisen einiger Features für schreibgeschützte Replikate:
* Die Überwachung von schreibgeschützten Replikaten wird automatisch aktiviert. Weitere Informationen zur Hierarchie der Speicherordner, zu Namenskonventionen und zum Protokollformat finden Sie unter [Format für SQL-Datenbank-Überwachungsprotokolle](audit-log-format.md).
* [Query Performance Insight](query-performance-insight-use.md) basiert auf Daten aus dem [Abfragespeicher](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store), der derzeit keine Aktivitäten auf dem schreibgeschützten Replikat nachverfolgt. Von Query Performance Insight werden keine Abfragen gezeigt, die auf dem schreibgeschützten Replikat ausgeführt werden.
* Die automatische Optimierung basiert auf dem Abfragespeicher, wie im Dokument [Automatische Optimierung](https://www.microsoft.com/en-us/research/uploads/prod/2019/02/autoindexing_azuredb.pdf) ausführlich angegeben. Die automatische Optimierung funktioniert nur für Workloads, die auf dem primären Replikat ausgeführt werden.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Hyperscale-Angebot für SQL-Datenbank finden Sie unter [Dienstebene „Hyperscale“](service-tier-hyperscale.md).
