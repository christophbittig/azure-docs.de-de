---
title: Migrieren von Azure-Datenbankressourcen – Azure Deutschland zu Azure weltweit
description: Dieser Artikel enthält Informationen zum Migrieren von Azure-Datenbankressourcen von Azure Deutschland zu Azure weltweit.
ms.topic: article
ms.date: 03/29/2021
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurepowershell
ms.openlocfilehash: 6fe6d653712e034c13f3b755e906de491b4dd49a
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2021
ms.locfileid: "110696907"
---
# <a name="migrate-database-resources-to-global-azure"></a>Migrieren von Datenbankressourcen zu Azure weltweit

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Dieser Artikel enthält Informationen dazu, wie Sie Azure-Datenbankressourcen von Azure Deutschland zu Azure weltweit migrieren können.

## <a name="sql-database"></a>SQL-Datenbank

Erstellen Sie mit der Exportfunktion eine BACPAC-Datei, um kleinere Azure SQL-Datenbank-Workloads zu migrieren, ohne dass die migrierte Datenbank online bleibt. Eine BACPAC-Datei ist eine komprimierte Datei (ZIP-Datei), die Metadaten und die Daten aus der SQL Server-Datenbank enthält. Nachdem Sie die BACPAC-Datei erstellt haben, können Sie die Datei in die Zielumgebung kopieren (z.B. mit AzCopy) und die Importfunktion verwenden, um die Datenbank erneut zu erstellen. Beachten Sie die folgenden Aspekte:

- Damit ein Export im Hinblick auf Transaktionen konsistent ist, müssen Sie sicherstellen, dass eine der folgenden Bedingungen zutrifft:
  - Während des Exports tritt keine Schreibaktivität auf.
  - Sie exportieren aus einer bezüglich Transaktionen konsistenten Kopie Ihrer Azure SQL-Datenbank.
- Für einen Export in Azure-Blobspeicher ist die Größe der BACPAC-Datei auf 200 GB beschränkt. Führen Sie für größere BACPAC-Datei einen Export in lokalen Speicher aus.
- Falls der Exportvorgang aus der SQL-Datenbank länger als 20 Stunden dauert, wird der Vorgang möglicherweise abgebrochen. Suchen Sie in den folgenden Artikeln nach Tipps, wie die Leistung gesteigert werden kann.

> [!NOTE]
> Die Verbindungszeichenfolge ist nach dem Exportvorgang geändert, da der DNS-Name des Servers während des Exports geändert wird.

Weitere Informationen finden Sie unter:

- Erfahren Sie, wie Sie [eine Datenbank in eine BACPAC-Datei exportieren](../azure-sql/database/database-export.md).
- Erfahren Sie, wie Sie [eine BACPAC-Datei in eine neue Datenbank importieren](../azure-sql/database/database-import.md).
- Lesen Sie die [Dokumentation zu Azure SQL-Datenbank](/azure/sql-database/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="migrate-sql-database-using-active-geo-replication"></a>Migrieren einer SQL-Datenbank mithilfe der aktiven Georeplikation

Für Datenbanken, die für BACPAC-Dateien zu groß sind oder von einer Cloud zu einer anderen migriert werden sollen und mit minimalen Ausfallzeiten online bleiben, können Sie die aktive Georeplikation von Azure Deutschland zur globalen Azure-Umgebung konfigurieren.

> [!IMPORTANT]
> Das Konfigurieren der aktiven Georeplikation zum Migrieren von Datenbanken zur globalen Azure-Umgebung wird nur mit Transact-SQL (T-SQL) unterstützt. Außerdem müssen Sie vor der Migration die Aktivierung Ihres Abonnements anfordern, damit die Migration zur globalen Azure-Umgebung unterstützt wird. Zum Übermitteln der Anforderung müssen Sie [diesen Supportanfragelink](#requesting-access) verwenden. 

> [!Note]
> Die globalen Azure-Cloudregionen Deutschland, Westen-Mitte und Deutschland, Norden sind die unterstützten Regionen für die aktive Georeplikation mit der Azure Deutschland Cloud. Wenn eine alternative globale Azure-Region als endgültiges Ziel der Datenbank(en) gewünscht wird, empfiehlt es sich nach Abschluss der Migration zu Azure, eine zusätzliche Georeplikationsverbindung von „Deutschland, Westen-Mitte“ oder „Deutschland, Norden“ zur erforderlichen globalen Azure-Cloudregion zu konfigurieren.

Ausführliche Informationen zu den Kosten für die aktive Georeplikation finden Sie in [Preise für Azure SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database/single/) im Abschnitt **Aktive Georeplikation**.

Zum Migrieren von Datenbanken mit aktiver Georeplikation ist ein logischer Azure SQL-Server in der globalen Azure-Umgebung erforderlich. Sie können den Server im Portal, mit Azure PowerShell, mit der Azure CLI usw. erstellen. Das Konfigurieren der aktiven Georeplikation für die Migration von Azure Deutschland zur globalen Azure-Umgebung wird jedoch nur mithilfe von Transact-SQL (T-SQL) unterstützt.

> [!IMPORTANT]
> Bei der Migration zwischen Clouds müssen sich das Namenspräfix des primären Servers (Azure Deutschland) und des sekundären Servers (globale Azure-Umgebung) unterscheiden. Wenn die Servernamen identisch sind, wird die Ausführung der ALTER DATABASE-Anweisung erfolgreich ausgeführt, die Migration schlägt jedoch fehl. Wenn das Präfix des Namenspräfix des primären Servers z. B. `myserver` (`myserver.database.cloudapi.de`) lautet, darf das Namenspräfix des sekundären Servers in der globalen Azure-Umgebung nicht ebenfalls `myserver` lauten.


Mit der `ALTER DATABASE`-Anweisung können Sie einen Zielserver in der globalen Azure-Umgebung angeben, indem Sie auf der Zielseite den vollqualifizierten DNS-Servernamen verwenden. 


```sql
ALTER DATABASE [sourcedb] add secondary on server [public-server.database.windows.net]
```


- *`sourcedb`* stellt den Datenbanknamen auf einem Azure SQL-Server in Azure Deutschland dar. 
- *`public-server.database.windows.net`* stellt den Namen des Azure SQL-Servers dar, der in der globalen Azure-Umgebung vorhanden ist, zu der die Datenbank migriert werden soll. Der Namespace „database.windows.net“ ist erforderlich. Ersetzen Sie *public-server* durch den Namen des logischen SQL-Servers in der globalen Azure-Umgebung. Der Server in der globalen Azure-Umgebung muss einen anderen Namen aufweisen als der primäre Server in Azure Deutschland.


Der Befehl wird in der Masterdatenbank auf dem Server in Azure Deutschland ausgeführt, der zum Host der zu migrierenden lokalen Datenbank wird. 
- Die T-SQL-API zum Starten des Kopiervorgangs authentifiziert den angemeldeten Benutzer auf dem Server in der öffentlichen Cloud. Dies erfolgt durch das Suchen nach einem Benutzer, der in der Masterdatenbank des Servers die gleiche SQL-Anmeldung / den gleichen SQL-Benutzernamen aufweist. Dieser Ansatz ist cloudagnostisch. Daher wird zum Starten cloudübergreifender Kopien die T-SQL-API verwendet. Informationen zu Berechtigungen und weitere Informationen zu diesem Thema finden Sie unter [Erstellen und Verwenden der aktiven Georeplikation](../azure-sql/database/active-geo-replication-overview.md) und [ALTER DATABASE (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql/).
- Mit Ausnahme der anfänglichen T-SQL-Befehlserweiterung zum Angeben eines logischen Azure SQL-Servers in der globalen Azure-Umgebung ist der Rest der aktiven Georeplikation mit der bestehenden Ausführung in der lokalen Cloud identisch. Ausführliche Schritte zum Erstellen einer aktiven Georeplikation finden Sie unter [Erstellen und Verwenden der aktiven Georeplikation](../azure-sql/database/active-geo-replication-overview.md). Es besteht jedoch der Unterschied, dass die sekundäre Datenbank auf dem sekundären logischen Server erstellt wird, der in der globalen Azure-Umgebung erstellt wurde. 
- Sobald die sekundäre Datenbank in der globalen Azure-Umgebung vorhanden ist (als Onlinekopie der Datenbank in Azure Deutschland), kann der Kunde mithilfe des T-SQL-Befehls ALTER DATABASE ein Datenbankfailover von Azure Deutschland zur globalen Azure-Umgebung initiieren (siehe die Tabelle unten).
- Sobald nach dem Failover aus der sekundären Datenbank eine primäre Datenbank in der globalen Azure-Umgebung geworden ist, können Sie die aktive Georeplikation jederzeit beenden und die sekundäre Datenbank in Azure Deutschland entfernen (siehe die folgende Tabelle und die Schritte im Diagramm). 
- Nach dem Failover fallen für die sekundäre Datenbank in Azure Deutschland weiterhin Kosten an, bis sie gelöscht wird.
      
- Die Verwendung des Befehls `ALTER DATABASE` ist die einzige Möglichkeit, die aktive Georeplikation für die Migration einer Datenbank in Azure Deutschland zur globalen Azure-Umgebung einzurichten. 
- Die aktive Georeplikation für diese Migration kann nicht mit dem Azure-Portal, mit Azure Resource Manager, PowerShell oder der CLI konfiguriert werden. 

So migrieren Sie eine Datenbank von Azure Deutschland zur globalen Azure-Umgebung:   

1.  Wählen Sie die Benutzerdatenbank in Azure Deutschland aus, z. B. `azuregermanydb`
2.  Erstellen Sie einen logischen Server in der globalen Azure-Umgebung (in der öffentlichen Cloud), z. B. `globalazureserver`. Sein vollqualifizierter Domänenname (Fully Qualified Domain Name, FQDN) lautet `globalazureserver.database.windows.net`.
3.  Starten Sie die aktive Georeplikation von Azure Deutschland zur globalen Azure-Umgebung, indem Sie auf dem Server in Azure Deutschland diesen T-SQL-Befehl ausführen. Beachten Sie, dass für den öffentlichen Server der vollqualifizierte DNS-Name `globalazureserver.database.windows.net` verwendet wird. Dadurch wird angegeben, dass sich der Zielserver in der globalen Azure-Umgebung und nicht in Azure Deutschland befindet.

    ```sql
    ALTER DATABASE [azuregermanydb] ADD SECONDARY ON SERVER [globalazureserver.database.windows.net];
    ```

4.  Um durch die Replikation die Workloads mit Lese-/Schreibzugriff auf den globalen Azure-Server zu verschieben, initiieren Sie ein geplantes Failover zur globalen Azure-Umgebung, indem Sie auf dem globalen Azure-Server den folgenden T-SQL-Befehl ausführen.

    ```sql
    ALTER DATABASE [azuregermanydb] FAILOVER;
    ```

5.  Der aktive Georeplikationslink kann vor oder nach dem Failoverprozess beendet werden. Durch Ausführen des folgenden T-SQL-Befehls nach dem geplanten Failover wird der Georeplikationslink zur Datenbank, die die Kopie mit Lese-/Schreibzugriff ist, in Azure global entfernt. Sie sollte auf dem logischen Server der aktuellen geo-primären Datenbank (d. h. auf dem globalen Azure-Server) ausgeführt werden. Dadurch wird der Migrationsprozess abgeschlossen.

    ```sql
    ALTER DATABASE [azuregermanydb] REMOVE SECONDARY ON SERVER [azuregermanyserver];
    ```

    Der folgende T-SQL-Befehl, wenn er vor dem geplanten Failover ausgeführt wird, beendet auch den Migrationsprozess. In diesem Fall bleibt die Datenbank in Azure Deutschland jedoch die Kopie mit Lese-/Schreibzugriff. Dieser T-SQL-Befehl sollte ebenfalls auf dem logischen Server der aktuellen geo-primären Datenbank, in diesem Fall auf dem Azure Deutschland-Server, ausgeführt werden.

    ```sql
    ALTER DATABASE [azuregermanydb] REMOVE SECONDARY ON SERVER [globalazureserver];
    ```

Diese Schritte zum Migrieren von Azure SQL-Datenbanken von Azure Deutschland zu einer globalen Azure-Umgebung können auch mithilfe der aktiven Georeplikation durchgeführt werden.


In den folgenden Tabellen sind T-SQL-Befehle zum Verwalten des Failovers aufgeführt. Die folgenden Befehle werden für die cloudübergreifende aktive Georeplikation zwischen Azure Deutschland und der globalen Azure-Umgebung unterstützt: 
 
|Get-Help |BESCHREIBUNG|
|:--|:--| 
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Verwenden Sie das Argument ADD SECONDARY ON SERVER, um eine sekundäre Datenbank für eine vorhandene Datenbank zu erstellen und die Datenreplikation zu starten.| 
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Verwenden Sie FAILOVER oder FORCE_FAILOVER_ALLOW_DATA_LOSS, um die sekundäre Datenbank zur primären zu erklären und zu ihr zu wechseln – damit starten Sie das Failover.  |
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Verwenden Sie REMOVE SECONDARY ON SERVER, um die Datenreplikation zwischen einer SQL-Datenbank und der angegebenen sekundären Datenbank zu beenden.  |
 
### <a name="active-geo-replication-monitoring-system-views"></a>Ansichten des Überwachungssystems für die aktive Georeplikation 
 
|Get-Help |BESCHREIBUNG|
|:--|:--| 
|[sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database?view=azuresqldb-current&preserve-view=true)|Gibt Informationen über alle vorhandenen Replikationsverknüpfungen für alle Datenbanken auf dem Azure SQL-Datenbank-Server zurück. |
|[sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database?view=azuresqldb-current&preserve-view=true) |Ruft den Zeitpunkt der letzten Replikation, die Verzögerung der letzten Replikation und andere Informationen über die Replikationsverknüpfung für eine angegebene SQL-Datenbank ab. |
|[sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?view=azuresqldb-current&preserve-view=true) | Zeigt den Status für alle Datenbankvorgänge an, einschließlich des Status der Replikationsverknüpfungen. | 
|[sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync?view=azuresqldb-current&preserve-view=true) | Bewirkt, dass die Anwendung wartet, bis alle Transaktionen mit erfolgtem Commit repliziert und von der aktiven sekundären Datenbank bestätigt wurden. |
 

## <a name="migrate-sql-database-long-term-retention-backups"></a>Migrieren von Azure SQL-Datenbanksicherungen zur Langzeitaufbewahrung

Beim Migrieren einer Datenbank mit Georeplikation oder BACPAC-Datei werden die Sicherungen zur langfristigen Aufbewahrung, die die Datenbank möglicherweise in Azure Deutschland hat, nicht kopiert. Sie können das Verfahren „Sicherungen zur langfristigen Aufbewahrung KOPIEREN“ verwenden, um derartige vorhandene Sicherungen in die globale Azure-Zielregion zu migrieren 

>[!Note]
>Die hier dokumentierten LTR-Sicherungskopiermethoden können nur die LTR-Sicherungen aus Azure Deutschland in Azure weltweit kopieren. Das Kopieren von PITR-Sicherungen mit diesen Methoden wird nicht unterstützt. 
>

### <a name="pre-requisites"></a>Voraussetzungen

1. Die Zieldatenbank, in die Sie die LTR-Sicherungen kopieren, muss in Azure weltweit vorhanden sein, bevor Sie mit dem Kopieren der Sicherungen beginnen. Es wird empfohlen, zuerst die Quelldatenbank mithilfe der [aktiven Georeplikation](#migrate-sql-database-using-active-geo-replication) zu migrieren und dann die LTR-Sicherungskopie zu initiieren. Dadurch wird sichergestellt, dass die Datenbanksicherungen in die richtige Zieldatenbank kopiert werden. Dieser Schritt ist nicht erforderlich, wenn Sie LTR-Sicherungen einer gelöschten Datenbank kopieren. Beim Kopieren von LTR-Sicherungen einer gelöschten Datenbank wird eine Platzhalterdatenbank-ID in der Zielregion erstellt. 
2. Installieren Sie dieses [PowerShell Az-Modul](https://www.powershellgallery.com/packages/Az.Sql/3.0.0-preview)
3. Stellen Sie vor dem Beginn sicher, dass die erforderlichen [Azure RBAC-Rollen](../azure-sql/database/long-term-backup-retention-configure.md#azure-roles-to-manage-long-term-retention) im Bereich **Abonnement** oder **Ressourcengruppe** erteilt werden. Hinweis: Für den Zugriff auf LTR-Sicherungen, die zu einem gelöschten Server gehören, muss die Berechtigung jedoch im Bereich Abonnement dieses Servers erteilt werden. . 


### <a name="limitations"></a>Einschränkungen  

- Failovergruppen werden nicht unterstützt. Dies bedeutet, dass Kunden, die Datenbanken aus Azure Deutschland migrieren, während des Failovers die Verbindungszeichenfolgen selbst verwalten müssen.
- Keine Unterstützung für das Azure-Portal, Azure Resource Manager-APIs, PowerShell oder die CLI. Dies bedeutet, dass bei jeder Migration aus Azure Deutschland das Einrichten der aktiven Georeplikation und das Failover über T-SQL erfolgen müssen.
- Kunden können in der globalen Azure-Umgebung nicht mehrere georeplizierte sekundäre Datenbanken für Datenbanken in Azure Deutschland erstellen.
- Die Erstellung einer georeplizierten sekundären Datenbank muss in der Region „Azure Deutschland“ initiiert werden.
- Kunden können Datenbanken aus Azure Deutschland nur zur globalen Azure-Umgebung migrieren. Zurzeit wird keine andere cloudübergreifende Migration unterstützt. 
- Azure AD Benutzer in Azure Deutschland-Benutzerdatenbanken werden migriert, sind jedoch im neuen Azure AD-Mandanten, auf dem sich die migrierte Datenbank befindet, nicht verfügbar. Um diese Benutzer zu aktivieren, müssen sie manuell gelöscht und unter Verwendung der aktuellen Azure AD-Benutzer, die in dem neuen Azure AD-Mandanten verfügbar sind, in dem sich die neu migrierte Datenbank befindet, neu erstellt werden.  


### <a name="copy-long-term-retention-backups-using-powershell"></a>Kopieren von Sicherungen mit langfristiger Aufbewahrung mit PowerShell

Ein neuer PowerShell-Befehl **Copy-AzSqlDatabaseLongTermRetentionBackup** wurde eingeführt, der zum Kopieren der von Sicherungen mit langfristiger Aufbewahrung aus Azure Deutschland in globale Azure-Regionen verwendet werden kann. 

1. **Kopieren der LTR-Sicherung mithilfe des Sicherungsnamens** Das folgende Beispiel zeigt, wie Sie eine LTR-Sicherung mithilfe des Sicherungsnamens aus Azure Deutschland in die globale Azure-Region kopieren können.  

```powershell
# Source database and target database info
$location = "<location>"
$sourceRGName = "<source resourcegroup name>"
$sourceServerName = "<source server name>"
$sourceDatabaseName = "<source database name>"
$backupName = "<backup name>"
$targetDatabaseName = "<target database name>"
$targetSubscriptionId = "<target subscriptionID>"
$targetRGName = "<target resource group name>"
$targetServerFQDN = "<targetservername.database.windows.net>"

Copy-AzSqlDatabaseLongTermRetentionBackup 
    -Location $location 
    -ResourceGroupName $sourceRGName 
    -ServerName $sourceServerName 
    -DatabaseName $sourceDatabaseName
    -BackupName $backupName
    -TargetDatabaseName $targetDatabaseName 
    -TargetSubscriptionId $targetSubscriptionId
    -TargetResourceGroupName $targetRGName
    -TargetServerFullyQualifiedDomainName $targetServerFQDN 
```

2. **Kopieren der LTR-Sicherung mithilfe der Sicherungsressourcen-ID** Das folgende Beispiel zeigt, wie Sie eine LTR-Sicherung mithilfe der Sicherungsressourcen-ID aus Azure Deutschland in die globale Azure-Region kopieren können. Dieses Beispiel kann auch zum Kopieren von Sicherungen einer gelöschten Datenbank verwendet werden. 

```powershell
$location = "<location>"
# list LTR backups for All databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $location -DatabaseState All

# select the LTR backup you want to copy
$ltrBackup = $ltrBackups[0]
$resourceID = $ltrBackup.ResourceId

# Source Database and target database info
$targetDatabaseName = "<target database name>"
$targetSubscriptionId = "<target subscriptionID>"
$targetRGName = "<target resource group name>"
$targetServerFQDN = "<targetservername.database.windows.net>"

Copy-AzSqlDatabaseLongTermRetentionBackup 
    -ResourceId $resourceID 
    -TargetDatabaseName $targetDatabaseName 
    -TargetSubscriptionId $targetSubscriptionId
    -TargetResourceGroupName $targetRGName
    -TargetServerFullyQualifiedDomainName $targetServerFQDN
```


### <a name="limitations"></a>Einschränkungen  

- [PITR-Sicherungen (Point-in-Time Restore)](../azure-sql/database/recovery-using-backups.md#point-in-time-restore) erfolgen nur in der primären Datenbank. Dies ist beabsichtigt. Beim Migrieren von Datenbanken aus Azure Deutschland mithilfe von georedundanter Notfallwiederherstellung werden nach dem Failover die PITR-Sicherungen in der neuen primären Datenbank ausgeführt. Die vorhandenen PITR-Sicherungen (in der vorherigen primären Datenbank in Azure Deutschland) werden jedoch nicht migriert. Wenn Sie zur Unterstützung von Zeitpunktwiederherstellungen PITR-Sicherungen benötigen, müssen Sie die Datenbank aus den PITR-Sicherungen in Azure Deutschland wiederherstellen und dann die wiederhergestellte Datenbank zu einer globalen Azure-Umgebung migrieren. 
- Richtlinien für die Langzeitaufbewahrung werden nicht mit der Datenbank migriert. Wenn Sie über eine Richtlinie für die [Langzeitaufbewahrung (Long-term retention, LTR)](../azure-sql/database/long-term-retention-overview.md) für Ihre Datenbank in Azure Deutschland verfügen, müssen Sie die LTR-Richtlinie nach der Migration manuell kopieren und in der neuen Datenbank neu erstellen. 


### <a name="requesting-access"></a>Anfordern des Zugriffs

Zum Migrieren einer Datenbank von Azure Deutschland zu einer globalen Azure-Umgebung mithilfe von Georeplikation muss Ihr Abonnement *in Azure Deutschland* aktiviert sein, damit die cloudübergreifenden Migration erfolgreich konfiguriert werden kann.

Zum Aktivieren Ihres Azure Deutschland-Abonnements müssen Sie über den folgenden Link eine Migrationssupportanfrage erstellen:   

1. Navigieren Sie zu der folgenden [Migrationssupportanfrage](https://portal.microsoftazure.de/#create/Microsoft.Support/Parameters/%7B%0D%0A++++%22pesId%22%3A+%22f3dc5421-79ef-1efa-41a5-42bf3cbb52c6%22%2C%0D%0A++++%22supportTopicId%22%3A+%229fc72ed5-805f-3894-eb2b-b1f1f6557d2d%22%2C%0D%0A++++%22contextInfo%22%3A+%22Migration+from+cloud+Germany+to+Azure+global+cloud+%28Azure+SQL+Database%29%22%2C%0D%0A++++%22caller%22%3A+%22NoSupportPlanCloudGermanyMigration%22%2C%0D%0A++++%22severity%22%3A+%223%22%0D%0A%7D).

2. Geben Sie auf der Registerkarte „Grundlagen“ *Migration für georedundante Notfallwiederherstellung* als **Zusammenfassung** ein, und klicken Sie dann auf **Weiter: Lösungen**.
 
   :::image type="content" source="media/germany-migration-databases/support-request-basics.png" alt-text="Neues Supportanfrageformular":::

3. Überprüfen Sie die **Empfohlenen Schritte**, und klicken Sie dann auf **Weiter: Details**. 

   :::image type="content" source="media/germany-migration-databases/support-request-solutions.png" alt-text="Erforderliche Informationen für Supportanfrage":::

4. Geben Sie auf der Seite „Details“ Folgendes an:

   1. Geben Sie im Feld „Beschreibung“ die Abonnement-ID für die globale Azure-Umgebung ein, zu der migriert werden soll. Um Datenbanken zu mehreren Abonnements zu migrieren, fügen Sie eine Liste der IDs der globalen Azure-Umgebungen hinzu, zu denen Sie Datenbanken migrieren möchten.
   1. Geben Sie Kontaktinformationen an: Name, Firmenname, E-Mail-Adresse oder Telefonnummer.
   1. Füllen Sie das Formular aus. Wählen Sie dann **Weiter: Überprüfen + erstellen** aus.

   :::image type="content" source="media/germany-migration-databases/support-request-details.png" alt-text="Details der Supportanfrage":::


5. Überprüfen Sie die Supportanfrage, und wählen Sie dann **Erstellen** aus. 


Sie werden benachrichtigt, sobald die Anfrage verarbeitet wurde.



## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Sie können das Azure Cosmos DB-Datenmigrationstool verwenden, um Daten zu Azure Cosmos DB zu migrieren. Das Azure Cosmos DB-Datenmigrationstool ist eine Open-Source-Lösung, mit der Daten aus verschiedenen Quellen in Azure Cosmos DB importiert werden können. Folgende Quellen können verwendet werden: JSON-Dateien, MongoDB, SQL Server, CSV-Dateien, Azure-Tabellenspeicher, Amazon DynamoDB, HBase und Azure Cosmos-Container.


Das Azure Cosmos DB-Datenmigrationstool steht als GUI-Tool oder als Befehlszeilentool zur Verfügung. Der Quellcode ist im GitHub-Repository [Azure Cosmos DB Data Migration Tool](https://github.com/azure/azure-documentdb-datamigrationtool) verfügbar. Eine [kompilierte Version des Tools](https://www.microsoft.com/download/details.aspx?id=46436) ist im Microsoft Download Center verfügbar.

Um Azure Cosmos DB-Ressourcen zu migrieren, sollten Sie die folgenden Schritte ausführen:

1. Überprüfen Sie die Anforderungen an die Anwendungsbetriebszeit und die Kontokonfigurationen, um den besten Aktionsplan zu bestimmen.
1. Klonen Sie die Kontokonfigurationen aus Azure Deutschland in die neue Region, indem Sie das Datenmigrationstool ausführen.
1. Kann ein Wartungsfenster verwendet werden, kopieren Sie die Daten aus der Quelle in das Ziel, indem Sie das Datenmigrationstool ausführen.
1. Ist es nicht möglich, ein Wartungsfenster zu verwenden, kopieren Sie die Daten aus der Quelle in das Ziel, indem Sie das Tool und dann die folgenden Schritte ausführen:
   1. Verwenden Sie einen konfigurationsgesteuerten Ansatz, um Änderungen an den Lese-/Schreibvorgängen in einer Anwendung vorzunehmen.
   1. Führen Sie eine erstmalige Synchronisierung aus.
   1. Richten Sie eine inkrementelle Synchronisierung ein, und binden Sie den Änderungsfeed ein.
   1. Verknüpfen Sie Lesevorgänge mit dem neuen Konto, und überprüfen Sie die Anwendung.
   1. Beenden Sie Schreibvorgänge in das alte Konto, überprüfen Sie, ob der Änderungsfeed berücksichtigt wird, und verknüpfen Sie dann Schreibvorgänge mit dem neuen Konto.
   1. Beenden Sie das Tool, und löschen Sie das alte Konto.
1. Führen Sie das Tool aus, um zu überprüfen, ob die Daten im alten und im neuen Konto konsistent sind.

Weitere Informationen finden Sie unter:

- Informationen zur Verwendung des Datenmigrationstools finden Sie im [Tutorial: Migrieren Ihrer Daten zu Azure Cosmos DB mithilfe des Datenmigrationstools](../cosmos-db/import-data.md).
- Informationen zu Cosmos DB finden Sie unter [Willkommen bei Azure Cosmos DB](../cosmos-db/introduction.md).


## <a name="azure-cache-for-redis"></a>Azure Cache for Redis

Sie haben einige Optionen, wenn Sie eine Azure Cache für Redis-Instanz von Azure Deutschland zu Azure weltweit migrieren möchten. Welche Option Sie auswählen, hängt von Ihren Anforderungen ab.

### <a name="option-1-accept-data-loss-create-a-new-instance"></a>Option 1: Akzeptieren von Datenverlust, Erstellen einer neuen Instanz

Dieser Ansatz ist am sinnvollsten, wenn die beiden folgenden Bedingungen zutreffen:

- Sie verwenden Azure Cache for Redis als transienten Datencache.
- Ihre Anwendung füllt die Cachedaten in der neuen Region automatisch erneut auf.

So migrieren Sie mit Datenverlust und erstellen Sie eine neue Instanz:

1. Erstellen Sie eine neue Azure Cache for Redis-Instanz in der neuen Zielregion.
1. Ändern Sie Ihre Anwendung so, dass sie die neue Instanz in der neuen Region verwendet.
1. Löschen Sie die alte Azure-Cache für Redis-Instanz in der Quellregion.

### <a name="option-2-copy-data-from-the-source-instance-to-the-target-instance"></a>Option 2: Kopieren von Daten aus der Quellinstanz in die Zielinstanz

Ein Mitglied des Azure Cache for Redis-Teams hat ein Open-Source-Tool geschrieben, das Daten aus einer Azure Cache for Redis-Instanz in eine andere kopiert, ohne Import- oder Exportfunktionalität zu erfordern. Informationen zu dem Tool finden Sie in Schritt 4 in den folgenden Schritten.

So kopieren Sie Daten aus der Quellinstanz in die Zielinstanz:

1. Erstellen Sie einen virtuellen Computer in der Quellregion. Wenn Ihr Dataset in Azure Cache for Redis groß ist, stellen Sie sicher, dass Sie eine relativ leistungsfähige VM-Größe auswählen, um die für den Kopiervorgang benötigte Zeit zu minimieren.
1. Erstellen Sie eine neue Azure Cache for Redis-Instanz in der Zielregion.
1. Leeren Sie die Daten aus der **Ziel**-Instanz. (Achten Sie darauf, *nicht* die Daten aus der **Quell**-Instanz zu leeren. Das Leeren ist erforderlich, da das Kopiertool vorhandene Schlüssel am Zielspeicherort *nicht überschreibt*.)
1. Verwenden Sie das folgende Tool, um Daten automatisch aus der Azure Cache for Redis-Quellinstanz in die Azure Cache for Redis-Zielinstanz zu kopieren: [Quelle des Tools](https://github.com/deepakverma/redis-copy) und [Tool herunterladen](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip).

> [!NOTE]
> Dieser Vorgang kann abhängig von der Größe des Datasets sehr lange dauern.

### <a name="option-3-export-from-the-source-instance-import-to-the-destination-instance"></a>Option 3: Exportieren aus der Quellinstanz, Importieren in die Zielinstanz

Bei diesem Ansatz werden die Vorteile von Features genutzt, die nur im Premium-Tarif verfügbar sind.

So exportieren Sie aus der Quellinstanz und importieren Sie in die Zielinstanz:

1. Erstellen Sie im Premium-Tarif eine neue Azure Cache for Redis-Instanz in der Zielregion. Verwenden Sie dieselbe Größe wie für die Azure Cache for Redis-Quellinstanz.
1. [Exportieren Sie Daten aus dem Quellcache](../azure-cache-for-redis/cache-how-to-import-export-data.md), oder verwenden Sie das [PowerShell-Cmdlet „Export-AzRedisCache“](/powershell/module/az.rediscache/export-azrediscache).

   > [!NOTE]
   > Das Azure Storage-Exportkonto muss sich in derselben Region wie die Cacheinstanz befinden.

1. Kopieren Sie die exportierten Blobs in ein Speicherkonto in der Zielregion (z. B. mit AzCopy).
1. [Importieren Sie Daten in den Zielcache](../azure-cache-for-redis/cache-how-to-import-export-data.md), oder verwenden Sie das [PowerShell-Cmdlet „Import-AzRedisCache“](/powershell/module/az.rediscache/import-azrediscache).
1. Konfigurieren Sie Ihre Anwendung so, dass sie die Azure Cache for Redis-Zielinstanz verwendet.

### <a name="option-4-write-data-to-two-azure-cache-for-redis-instances-read-from-one-instance"></a>Option 4: Schreiben von Daten in zwei Azure Cache for Redis-Instanzen, Lesen aus einer Instanz

Bei diesem Ansatz müssen Sie Ihre Anwendung ändern. Die Anwendung muss Daten in mehrere Cacheinstanzen schreiben, während sie aus einer der Cacheinstanzen liest. Dieser Ansatz ist sinnvoll, wenn die in Azure-Cache für Redis gespeicherten Daten die folgenden Kriterien erfüllen:
- Die Daten werden regelmäßig aktualisiert. 
- Alle Daten werden in die Azure Cache for Redis-Zielinstanz geschrieben.
- Es ist genügend Zeit für die Aktualisierung aller Daten verfügbar.

Weitere Informationen finden Sie unter:

- Lesen Sie die [Übersicht über Azure-Cache für Redis](../azure-cache-for-redis/cache-overview.md).

## <a name="postgresql-and-mysql"></a>PostgreSQL und MySQL

Weitere Informationen finden Sie in den Artikeln im Abschnitt „Sichern und Migrieren von Daten“ in [PostgreSQL](../postgresql/index.yml) und [MySQL](../mysql/index.yml).

![PostgreSQL und MySQL](./media/germany-migration-main/databases.png)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Tools, Techniken und Empfehlungen zum Migrieren von Ressourcen in den folgenden Dienstkategorien:

- [Compute](./germany-migration-compute.md)
- [Netzwerk](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integration](./germany-migration-integration.md)
- [Identität](./germany-migration-identity.md)
- [Security](./germany-migration-security.md)
- [Verwaltungstools](./germany-migration-management-tools.md)
- [Medien](./germany-migration-media.md)
