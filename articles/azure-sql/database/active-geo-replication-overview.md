---
title: Aktive Georeplikation
description: Verwenden Sie die aktive Georeplikation, um lesbare sekundäre Datenbanken von einzelnen Datenbanken in Azure SQL Database in derselben oder in verschiedenen Regionen zu erstellen.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 10/25/2021
ms.openlocfilehash: ca958a3e7a43864caa673cd31736b1e661e7f608
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465362"
---
# <a name="active-geo-replication"></a>Aktive Georeplikation
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Aktive Georeplikation ist eine Funktion, mit der Sie eine kontinuierlich synchronisierte, lesbare Sekundärdatenbank für eine Primärdatenbank erstellen können. Die lesbare sekundäre Datenbank kann sich in derselben Azure-Region befinden wie die primäre, oder, was häufiger der Fall ist, in einer anderen Region. Diese Art von lesbaren Sekundärdatenbanken werden auch als Geo-Sekundärdatenbanken oder Geo-Replikate bezeichnet.

Die aktive Georeplikation ist als Business-Continuity-Lösung konzipiert, mit der Sie im Falle einer regionalen Katastrophe oder eines großflächigen Ausfalls eine schnelle Wiederherstellung einzelner Datenbanken durchführen können. Sobald die Geo-Replikation eingerichtet ist, können Sie einen Geo-Failover zu einer Geo-Sekundärstelle in einer anderen Azure-Region initiieren. Der Geo-Failover wird programmatisch durch die Anwendung oder manuell durch den Benutzer ausgelöst.

> [!NOTE]
> Die aktive Georeplikation für Azure SQL Hyperscale befindet sich [jetzt in der öffentlichen Vorschau](https://aka.ms/hsgeodr). Die aktuellen Einschränkungen umfassen: nur eine sekundäre Geodatenbank in derselben oder einer anderen Region; erzwungene und geplante Failover werden derzeit nicht unterstützt; Wiederherstellung einer Datenbank aus sekundärer Geodatenbank wird nicht unterstützt; Verwendung einer sekundären Geodatenbank als Quelldatenbank für Datenbankkopie oder als primäre Datenbank für eine andere sekundäre Geodatenbank wird nicht unterstützt.
> 
> Führen Sie die folgenden Schritte aus, falls Sie die sekundäre Geodatenbank zu einer primären (nicht schreibgeschützten) Datenbank machen müssen:
> 1. Heben Sie die Verknüpfung für die Georeplikation in PowerShell mithilfe des Cmdlets [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) bzw. bei der Azure CLI mit [az sql db replica delete-link](/cli/azure/sql/db/replica#az_sql_db_replica_delete_link) auf. Dadurch wird die sekundäre Datenbank zu einer eigenständigen Datenbank mit Lese-/Schreibzugriff. Dabei gehen alle Datenänderungen verloren, die zwar in der primären Datenbank committet, aber noch nicht in die sekundäre Datenbank repliziert wurden. Diese Änderungen können wiederhergestellt werden, wenn die alte primäre Datenbank verfügbar ist, oder (in einigen Fällen) durch Wiederherstellen der alten primären Datenbank auf den letzten verfügbaren Zeitpunkt.
> 2. Wenn die alte primäre Datenbank verfügbar ist, löschen Sie sie, und richten Sie dann die Georeplikation für die neue primäre Datenbank ein (es wird eine neue sekundäre Datenbank erstellt). 
> 3. Aktualisieren Sie die Verbindungszeichenfolgen in Ihrer Anwendung entsprechend.

> [!NOTE]
> Die aktive Georeplikation wird von Azure SQL Managed Instance nicht unterstützt. Für ein geografisches Failover von Instanzen von Azure SQL Managed Instance können Sie [Autofailover-Gruppen](auto-failover-group-overview.md) verwenden.

> [!NOTE]
> Informationen zum Migrieren von SQL-Datenbanken aus Azure Deutschland mithilfe der aktiven Georeplikation finden Sie unter [Migrieren einer SQL-Datenbank mithilfe der aktiven Georeplikation](../../germany/germany-migration-databases.md#migrate-sql-database-using-active-geo-replication).

Wenn Ihre Anwendung zusätzlich zur Georeplikation einen stabilen Verbindungsendpunkt und automatische Geo-Failover-Unterstützung benötigt, verwenden Sie [Auto-Failover-Gruppen](auto-failover-group-overview.md).

Das folgende Diagramm zeigt eine typische Konfiguration einer georedundanten Cloudanwendung mit aktiver Georeplikation.

![Aktive Georeplikation](./media/active-geo-replication-overview/geo-replication.png)

Wenn Ihre primäre Datenbank aus irgendeinem Grund ausfällt, können Sie einen Geo-Failover auf eine Ihrer sekundären Datenbanken veranlassen. Wenn ein Sekundärteilnehmer zum Primärteilnehmer befördert wird, werden alle anderen Sekundärteilnehmer automatisch mit dem neuen Primärteilnehmer verbunden.

Sie können die Georeplikation verwalten und einen Geo-Failover wie folgt initiieren:

- Das [Azure-Portal](active-geo-replication-configure-portal.md)
- [PowerShell: Einzelne Datenbank](scripts/setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Pool für elastische Datenbanken](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- [Transact-SQL: Einzelne Datenbank oder Pool für elastische Datenbanken](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST-API: Einzelne Datenbank](/rest/api/sql/replicationlinks)

Die aktive Georeplikation nutzt die Technologie der [Allzeitverfügbarkeitsgruppe](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server), um das auf dem primären Replikat erzeugte Transaktionsprotokoll asynchron auf alle Georeplikate zu replizieren. Auch wenn eine sekundäre Datenbank zu einem bestimmten Zeitpunkt etwas hinter der primären Datenbank zurückbleiben kann, ist die Konsistenz der Daten in einer sekundären Datenbank garantiert. Mit anderen Worten: Änderungen, die durch nicht bestätigte Transaktionen vorgenommen werden, sind nicht sichtbar. 

> [!NOTE]
> Bei der aktiven Georeplikation werden Änderungen durch Streaming des Datenbanktransaktionsprotokolls vom primären Replikat auf sekundäre Replikate repliziert. Sie hat nichts mit der [transaktionalen Replikation](/sql/relational-databases/replication/transactional/transactional-replication) zu tun, die Änderungen durch die Ausführung von DML-Befehlen (INSERT, UPDATE, DELETE) auf Abonnenten repliziert.

Regionale Redundanz durch Georeplikation ermöglicht es Anwendungen, sich schnell von einem dauerhaften Verlust einer ganzen Azure-Region oder von Teilen einer Region zu erholen, der durch Naturkatastrophen, katastrophale menschliche Fehler oder bösartige Handlungen verursacht wurde. Geo-Replikations-RPO finden Sie in [Überblick über Business Continuity](business-continuity-high-availability-disaster-recover-hadr-overview.md).

Die folgende Abbildung zeigt ein Beispiel für eine aktive Georeplikation mit einem primären Standort in der Region North Central US und einem sekundären Standort in der Region South Central US.

![Georeplikationsbeziehung](./media/active-geo-replication-overview/geo-replication-relationship.png)

Neben der Notfallwiederherstellung kann die aktive Georeplikation in den folgenden Szenarien eingesetzt werden:

- **Datenbankmigration**: Sie können die aktive Georeplikation verwenden, um eine Datenbank mit minimaler Ausfallzeit von einem Server auf einen anderen zu migrieren.
- **Anwendungsupgrades**: Sie können bei Anwendungsupgrades eine zusätzliche sekundäre Datenbank als Failbackkopie erstellen.

Um eine vollständige Geschäftskontinuität zu erreichen, ist das Hinzufügen einer regionalen Datenbankredundanz nur ein Teil der Lösung. Für die komplette Wiederherstellung einer Anwendung bzw. eines Diensts nach einem schwerwiegenden Fehler ist das Wiederherstellen aller Komponenten erforderlich, aus denen sich der Dienst und alle abhängigen Dienste zusammensetzen. Beispiele dieser Komponenten sind die Clientsoftware (z. B. ein Browser mit benutzerdefiniertem JavaScript), Web-Front-Ends, Speicher und DNS. Es ist wichtig, dass alle Komponenten hinsichtlich derselben Fehler gegen Ausfälle geschützt und innerhalb des RTO (Recovery Time Objective) der Anwendung wieder verfügbar sind. Daher müssen Sie alle abhängigen Dienste bestimmen und mit dem Leistungsumfang und den Funktionen vertraut sein, die sie bieten. Dann müssen Sie entsprechende Maßnahmen ergreifen, um sicherzustellen, dass Ihr Dienst während des Failovers der Dienste funktioniert, von denen er abhängig ist. Weitere Informationen zum Entwerfen von Lösungen für die Notfallwiederherstellung finden Sie unter [Entwerfen von Cloudlösungen für die Notfallwiederherstellung mithilfe der aktiven Georeplikation](designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Aktive Georeplikation – Terminologie und Funktionen

- **Automatische asynchrone Replikation**

  Sie können eine Geo-Sekundärdatenbank nur für eine bestehende Datenbank erstellen. Die geo-sekundäre Datenbank kann auf einem beliebigen logischen Server erstellt werden, der nicht der Server mit der primären Datenbank ist. Nach der Erstellung wird die geo-sekundäre Replik mit den Daten der primären Datenbank befüllt. Dieser Prozess wird als Seeding bezeichnet. Nachdem eine Geo-Sekundärdatenbank erstellt und geimpft wurde, werden Aktualisierungen der primären Datenbank automatisch und asynchron auf die Geo-Sekundärdatenbank repliziert. Asynchrone Replikation bedeutet, dass die Transaktionen in der primären Datenbank bestätigt werden, bevor sie repliziert werden.

- **Lesbare geo-sekundäre Replikate**

  Eine Anwendung kann auf ein geo-sekundäres Replikat zugreifen, um schreibgeschützte Abfragen mit denselben oder anderen Sicherheitsprinzipien auszuführen, die für den Zugriff auf die primäre Datenbank verwendet werden. Weitere Informationen finden Sie unter [Verwenden Sie schreibgeschützte Replikate, um schreibgeschützte Abfragen zu entlasten](read-scale-out.md).

   > [!IMPORTANT]
   > Mit der Georeplikation können Sie sekundäre Replikate in derselben Region wie das primäre erstellen. Sie können diese Secondaries verwenden, um Read-Scale-Out-Szenarien in derselben Region zu erfüllen. Ein sekundäres Replikat in derselben Region bietet jedoch keine zusätzliche Ausfallsicherheit bei katastrophalen Fehlern oder großflächigen Ausfällen und ist daher kein geeignetes Failover-Ziel für Disaster Recovery-Zwecke. Es garantiert auch keine Isolierung der Verfügbarkeitszonen. Verwenden Sie für die Isolierung von Verfügbarkeitszonen die Service-Tiers "Business Critical" oder "Premium" [Zonenredundanzkonfiguration](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) oder das Service-Tier "General Purpose" [Zonenredundanzkonfiguration](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview).
   >

- **Geplanter Geo-Failover**

  Der geplante Geo-Failover tauscht die Rollen der primären und der geosekundären Datenbank nach Abschluss der vollständigen Datensynchronisierung. Ein geplanter Failover führt nicht zu einem Datenverlust. Die Dauer des geplanten Geo-Failover hängt von der Größe des Transaktionsprotokolls auf dem primären System ab, das mit dem geo-sekundären System synchronisiert werden muss. Der geplante Geo-Failover ist für die folgenden Szenarien konzipiert:

  - Führen Sie DR-Drills in der Produktion durch, wenn der Datenverlust nicht akzeptabel ist; 
  - Verlegen Sie die Datenbank in eine andere Region; 
  - Rückführung der Datenbank in die primäre Region nach Behebung des Ausfalls (sog. Failback).

- **Ungeplanter Geo-Failover**

  Bei einem ungeplanten oder erzwungenen Geo-Failover wechselt der Geo-Sekundärserver sofort in die Primärrolle, ohne dass eine Synchronisierung mit dem Primärserver erfolgt. Alle Transaktionen, die auf dem primären System übertragen, aber noch nicht auf dem sekundären System repliziert wurden, gehen verloren. Dieser Vorgang ist als Wiederherstellungsmethode bei Ausfällen gedacht, wenn die Primärdatenbank nicht erreichbar ist, die Verfügbarkeit der Datenbank aber schnell wiederhergestellt werden muss. Wenn die ursprüngliche Primärdatenbank wieder online ist, wird sie automatisch wieder verbunden, mit den aktuellen Primärdaten neu bestückt und zu einer neuen Geo-Sekundärdatenbank.

  > [!IMPORTANT]
  > Nach einem geplanten oder ungeplanten Geo-Failover ändert sich der Verbindungsendpunkt für den neuen Primärserver, da sich dieser nun auf einem anderen logischen Server befindet.

- **Mehrere lesbare Geo-Sekundärdaten**

  Für eine Primärseite können bis zu vier Geosekundärseiten erstellt werden. Wenn nur ein Sekundärspeicher vorhanden ist und dieser ausfällt, ist die Anwendung einem höheren Risiko ausgesetzt, bis ein neuer Sekundärspeicher erstellt wird. Wenn mehrere Sekundärsysteme vorhanden sind, bleibt die Anwendung auch dann geschützt, wenn eines der Sekundärsysteme ausfällt. Zusätzliche Secondaries können auch zur Skalierung von Nur-Lese-Workloads verwendet werden.

  > [!TIP]
  > Wenn Sie die aktive Georeplikation zum Aufbau einer global verteilten Anwendung verwenden und einen Nur-Lese-Zugriff auf Daten in mehr als vier Regionen benötigen, können Sie einen Sekundärteil eines Sekundärteils erstellen (ein Prozess, der als Verkettung bekannt ist), um zusätzliche Georeplikate zu erstellen. Die Replikationsverzögerung bei verketteten Geo-Replikaten kann höher sein als bei Geo-Replikaten, die direkt mit dem Primärsystem verbunden sind. Das Einrichten von verketteten Georeplikations-Topologien wird nur programmatisch und nicht über das Azure-Portal unterstützt.

- **Georeplikation von Datenbanken in einem Pool für elastische Datenbanken**

  Jede Geosekundärdatenbank kann eine einzelne Datenbank oder eine Datenbank in einem elastischen Pool sein. Die Auswahl des elastischen Pools für jede geo-sekundäre Datenbank ist separat und hängt nicht von der Konfiguration einer anderen Replik in der Topologie ab (weder primär noch sekundär). Jeder elastische Pool ist in einem einzigen logischen Server enthalten. Da die Namen der Datenbanken auf einem logischen Server eindeutig sein müssen, können sich niemals mehrere Geo-Sekundärdatenbanken desselben Primärservers einen elastischen Pool teilen.

- **Benutzergesteuertes Geo-Failover und Failback**

  Ein Geo-Sekundärdienst, der seine erste Aussaat abgeschlossen hat, kann jederzeit von der Anwendung oder dem Benutzer ausdrücklich in die primäre Rolle umgeschaltet werden (failed over). Während eines Ausfalls, bei dem die Hauptleitung nicht erreichbar ist, kann nur ein ungeplanter Geo-Failover verwendet werden. Damit wird eine Geosekundärstufe sofort zur neuen Primärstufe. Wenn der Ausfall behoben ist, macht das System die wiederhergestellte Primärdatenquelle automatisch zu einer Geo-Sekundärdatenquelle und bringt sie mit der neuen Primärdatenquelle auf den neuesten Stand. Aufgrund des asynchronen Charakters der Georeplikation können aktuelle Transaktionen bei ungeplanten Geo-Failovern verloren gehen, wenn die Primärseite ausfällt, bevor diese Transaktionen auf eine Geo-Sekundärseite repliziert werden. Fällt eine Primärdatei mit mehreren Geo-Sekundärdateien aus, konfiguriert das System automatisch die Replikationsbeziehungen neu und verbindet die verbleibenden Geo-Sekundärdateien mit der neuen Primärdatei, ohne dass ein Benutzereingriff erforderlich ist. Nachdem der Ausfall, der den Geo-Failover verursacht hat, entschärft wurde, kann es wünschenswert sein, die Primäranlage in ihre ursprüngliche Region zurückzubringen. Rufen Sie dazu einen geplanten Geo-Failover auf.

## <a name="prepare-for-geo-failover"></a><a name="preparing-secondary-database-for-failover"></a> Vorbereitungen für Geo-Failover

Um sicherzustellen, dass Ihre Anwendung nach einem Geo-Failover sofort auf den neuen primären Server zugreifen kann, müssen Sie sicherstellen, dass die Authentifizierung und der Netzwerkzugriff für Ihren sekundären Server ordnungsgemäß konfiguriert sind. Weitere Informationen finden Sie unter [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](active-geo-replication-security-configure.md). Stellen Sie außerdem sicher, dass die Sicherungsaufbewahrungsrichtlinie der sekundären Datenbank mit der primären Datenbank übereinstimmt. Diese Einstellung ist nicht Teil der Datenbank und wird nicht von der Primärdatenbank repliziert. Standardmäßig ist die Geo-Sekundärdatei mit einer PITR-Aufbewahrungsfrist von sieben Tagen konfiguriert. Weitere Informationen finden Sie unter [Übersicht: Automatisierte SQL-Datenbanksicherungen](automated-backups-overview.md).

> [!IMPORTANT]
> Wenn Ihre Datenbank Mitglied einer Failovergruppe ist, können Sie das Failover nicht mit dem Befehl für ein Failover mit Georeplikation initiieren. Verwenden Sie den Failoverbefehl für die Gruppe. Wenn Sie ein Failover für eine einzelne Datenbank durchführen müssen, müssen Sie sie zuerst aus der Failovergruppe entfernen. Siehe [Auto-Failover-Gruppen](auto-failover-group-overview.md) für Einzelheiten.

## <a name="configure-geo-secondary"></a><a name="configuring-secondary-database"></a> Geo-sekundär konfigurieren

Sowohl die Primär- als auch die Geosekundärdienste müssen dieselbe Dienstebene haben. Es wird außerdem dringend empfohlen, die geo-sekundäre Einheit mit der gleichen Backup-Speicherredundanz und Rechengröße (DTUs oder vCores) zu konfigurieren wie die primäre Einheit. Wenn das primäre System eine hohe Schreiblast aufweist, kann ein sekundäres System mit einer geringeren Rechenleistung möglicherweise nicht mithalten. Dies führt zu einer Verzögerung der Replikation auf der Geo-Sekundärstation und kann schließlich zur Nichtverfügbarkeit der Geo-Sekundärstation führen. Um diese Risiken abzuschwächen, wird bei der aktiven Georeplikation die Transaktionsprotokollierungsrate des primären Systems bei Bedarf reduziert (gedrosselt), damit die sekundären Systeme aufholen können.

Eine weitere Folge einer unausgewogenen Geo-Sekundärkonfiguration ist, dass die Anwendungsleistung nach einem Failover aufgrund der unzureichenden Rechenkapazität des neuen Primärrechners leiden kann. In diesem Fall muss die Datenbank aufgestockt werden, um über ausreichende Ressourcen zu verfügen, was viel Zeit in Anspruch nehmen kann und eine [hochverfügbare](high-availability-sla.md) Ausfallsicherung am Ende des Aufstockungsprozesses erfordert, was zu einer Unterbrechung der Anwendungsarbeitslasten führen kann.

Wenn Sie sich dafür entscheiden, das Geo-Sekundärsystem mit einer geringeren Rechenleistung zu erstellen, sollten Sie die Protokoll-IO-Rate auf dem Primärsystem im Laufe der Zeit überwachen. So können Sie die minimale Rechengröße der Geo-Sekundärstation abschätzen, die erforderlich ist, um die Replikationslast aufrechtzuerhalten. Wenn Ihre primäre Datenbank beispielsweise P6 (1000 DTU) ist und ihr Log IO zu 50 % aufrechterhalten wird, muss die geo-sekundäre Datenbank mindestens P4 (500 DTU) sein. Verwenden Sie zum Abrufen von Protokoll-E/A-Verlaufsdaten die Sicht [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database). Um aktuelle IO-Protokolldaten mit höherer Granularität abzurufen, die kurzfristige Spitzen besser widerspiegeln, verwenden Sie die Ansicht [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database).

> [!TIP]
> Die IO-Drosselung des Transaktionsprotokolls auf der primären Ebene aufgrund einer geringeren Rechenleistung auf einer geo-sekundären Ebene wird mit dem Wartentyp HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO gemeldet, der in den Datenbankansichten [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) und [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) sichtbar ist.
>
> Die IO des Transaktionsprotokolls auf der Primärseite kann aus Gründen gedrosselt werden, die nicht mit der geringeren Rechenleistung auf einer geo-sekundären Seite zusammenhängen. Diese Art der Drosselung kann selbst dann auftreten, wenn die geo-sekundäre Stelle die gleiche oder eine höhere Rechenleistung hat als die primäre Stelle. Einzelheiten, einschließlich der Wartetypen für verschiedene Arten der Protokoll-IO-Drosselung, finden Sie unter [Transaktionsprotokollratensteuerung](resource-limits-logical-server.md#transaction-log-rate-governance).

Standardmäßig ist die Redundanz des Sicherungsspeichers für die geo-sekundäre Datenbank dieselbe wie für die primäre Datenbank. Sie können eine Geo-Sekundärdatei mit einer anderen Backup-Speicherredundanz konfigurieren. Sicherungen werden stets in der primären Datenbank erstellt. Wenn der Sekundärspeicher mit einer anderen Backup-Speicherredundanz konfiguriert ist, werden nach einem Geo-Failover, wenn der Geo-Sekundärspeicher zum Primärspeicher aufsteigt, neue Backups gespeichert und entsprechend der auf dem neuen Primärspeicher (vorheriger Sekundärspeicher) gewählten Speicherart (RA-GRS, ZRS, LRS) abgerechnet. 

## <a name="cross-subscription-geo-replication"></a>Abonnementübergreifende Georeplikation

Um ein geo-sekundäres Abonnement in einem anderen Abonnement als dem des primären Abonnements zu erstellen (unabhängig davon, ob es sich um denselben Azure Active Directory-Tenant handelt oder nicht), führen Sie die Schritte in diesem Abschnitt aus.

1. Fügen Sie die IP-Adresse des Client-Rechners, der die folgenden T-SQL-Befehle ausführt, zu den Server-Firewalls von **beiden**  primären und sekundären Servern hinzu. Sie können diese IP-Adresse bestätigen, indem Sie die folgende Abfrage ausführen, während Sie von demselben Client-Rechner aus mit dem Primärserver verbunden sind.
  
   ```sql
   select client_net_address from sys.dm_exec_connections where session_id = @@SPID;
   ``` 

   Für weitere Informationen siehe [Firewall konfigurieren](firewall-configure.md).

2. Erstellen Sie in der Master-Datenbank auf dem **primären** Server ein SQL-Authentifizierungs-Login für die aktive Einrichtung der Georeplikation. Passen Sie den Anmeldenamen und das Passwort nach Bedarf an.

   ```sql
   create login geodrsetup with password = 'ComplexPassword01';
   ```

3. Erstellen Sie in derselben Datenbank einen Benutzer für die Anmeldung, und fügen Sie ihn der Rolle `dbmanager` hinzu:

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup;
   ```

4. Notieren Sie sich den SID-Wert der neuen Anmeldung. Ermitteln Sie den SID-Wert mit der folgenden Abfrage.

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup';
   ```

5. Verbinden Sie sich mit der **primären** Datenbank (nicht mit der Master-Datenbank), und erstellen Sie einen Benutzer mit demselben Login.

   ```sql
   create user geodrsetup for login geodrsetup;
   ```

6. Fügen Sie in derselben Datenbank den Benutzer zur Rolle `db_owner` hinzu.

   ```sql
   alter role db_owner add member geodrsetup;
   ```

7. Erstellen Sie in der Master-Datenbank auf dem **Sekundärserver** das gleiche Login wie auf dem Primärserver, mit dem gleichen Namen, Passwort und SID. Ersetzen Sie den hexadezimalen SID-Wert im nachfolgenden Beispielbefehl durch den in Schritt 4 erhaltenen Wert.

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E;
   ```

8. Erstellen Sie in derselben Datenbank einen Benutzer für die Anmeldung, und fügen Sie ihn der Rolle `dbmanager` hinzu.

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup;
   ```

9. Verbinden Sie sich mit der Master-Datenbank auf dem **primären** Server unter Verwendung des neuen `geodrsetup` Logins und initiieren Sie die geo-sekundäre Erstellung auf dem sekundären Server. Passen Sie den Datenbanknamen und den Namen des Sekundärservers nach Bedarf an. Sobald der Befehl ausgeführt wurde, können Sie die Erstellung von Geo-Sekundärdateien überwachen, indem Sie die Ansicht [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) in der Datenbank **primary** und die Ansicht [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) in der Master-Datenbank auf dem Server **primary** abfragen. Die für die Erstellung einer Geo-Sekundärdatenbank benötigte Zeit hängt von der Größe der Primärdatenbank ab.

   ```sql
   alter database [dbrep] add secondary on server [servername];
   ```

10. Nachdem die Geo-Sekundärstation erfolgreich erstellt wurde, können die Benutzer, Anmeldungen und Firewall-Regeln, die mit diesem Verfahren erstellt wurden, entfernt werden.

> [!NOTE]
> Abonnementübergreifende Geo-Replikationsvorgänge, einschließlich Einrichtung und Geo-Failover, werden nur über T-SQL-Befehle unterstützt.
> 
> Das Hinzufügen einer Geo-Sekundärstation mithilfe von T-SQL wird nicht unterstützt, wenn für die primären und/oder sekundären Server ein [privater Endpunkt](private-endpoint-overview.md) konfiguriert ist und der [Zugriff über das öffentliche Netzwerk verweigert wird](connectivity-settings.md#deny-public-network-access). Wenn ein privater Endpunkt konfiguriert wurde, aber der Zugriff über das öffentliche Netzwerk zulässig ist, kann nach der Verbindungsherstellung mit dem primären Server über eine öffentliche IP-Adresse erfolgreich eine Geo-Sekundärstation hinzugefügt werden. Sobald eine Geo-Sekundärstation hinzugefügt wurde, kann der öffentliche Zugriff verweigert werden.
> 
> Das Erstellen eines geo-sekundären Servers auf einem logischen Server in einem anderen Azure-Tenant wird nicht unterstützt, wenn [nur Azure Active Directory](https://techcommunity.microsoft.com/t5/azure-sql/azure-active-directory-only-authentication-for-azure-sql/ba-p/2417673)-Authentifizierung für Azure SQL entweder auf dem primären oder sekundären logischen Server aktiv (aktiviert) ist.

## <a name="keep-credentials-and-firewall-rules-in-sync"></a><a name="keeping-credentials-and-firewall-rules-in-sync"></a> Anmeldeinformationen und Firewall-Regeln auf dem neuesten Stand halten

Wenn Sie für die Verbindung zur Datenbank einen öffentlichen Netzwerkzugang verwenden, empfehlen wir für georeplizierte Datenbanken IP-Firewall-Regeln [ auf Datenbankebene ](firewall-configure.md) zu verwenden. Diese Regeln werden mit der Datenbank repliziert, wodurch sichergestellt wird, dass alle Geo-Sekundärstellen dieselben IP-Firewall-Regeln haben wie die Primärstelle. Mit diesem Ansatz entfällt für die Kunden die Notwendigkeit, Firewall-Regeln auf den Servern, die die primären und sekundären Datenbanken hosten, manuell zu konfigurieren und zu pflegen. In ähnlicher Weise wird durch die Verwendung von Datenbankbenutzern[ mit ](logins-create-manage.md) für den Datenzugriff sichergestellt, dass sowohl die primäre als auch die sekundäre Datenbank immer über die gleichen Authentifizierungsdaten verfügen. Auf diese Weise kommt es nach einem Geo-Failover nicht zu Unterbrechungen aufgrund von Unstimmigkeiten bei den Authentifizierungsdaten.

## <a name="scale-primary-database"></a><a name="upgrading-or-downgrading-primary-database"></a> Skalierung der primären Datenbank

Sie können die primäre Datenbank auf eine andere Rechengröße vergrößern oder verkleinern (innerhalb derselben Dienstebene), ohne die Verbindung zu den sekundären Datenbanken zu unterbrechen. Bei der Skalierung wird empfohlen, zuerst die geo-sekundäre und dann die primäre Ebene zu vergrößern. Bei der Verkleinerung kehren Sie die Reihenfolge um: Verkleinern Sie zuerst die Primärseite und dann die Sekundärseite.

> [!NOTE]
> Wenn Sie eine geo-sekundäre Gruppe als Teil der Failover-Gruppenkonfiguration erstellt haben, ist es nicht empfehlenswert, diese zu verkleinern. Damit soll sichergestellt werden, dass Ihre Datenebene über genügend Kapazität verfügt, um die reguläre Arbeitslast nach einem Geo-Failover zu verarbeiten.

> [!IMPORTANT]
> Die primäre Datenbank in einer Failover-Gruppe kann nur dann auf eine höhere Serviceebene (Edition) skaliert werden, wenn die sekundäre Datenbank zuerst auf die höhere Ebene skaliert wird. Wenn Sie z. B. den primären Bereich von "General Purpose" auf "Business Critical" skalieren wollen, müssen Sie zuerst den geo-sekundären Bereich auf "Business Critical" skalieren. Wenn Sie versuchen, die Primär- oder Geo-Sekundärdaten in einer Weise zu skalieren, die gegen diese Regel verstößt, erhalten Sie die folgende Fehlermeldung:
>
> `The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="prevent-loss-of-critical-data"></a><a name="preventing-the-loss-of-critical-data"></a> Verhinderung des Verlusts kritischer Daten

Aufgrund der hohen Latenzzeit von WANs verwendet die Georeplikation einen asynchronen Replikationsmechanismus. Bei der asynchronen Replikation ist die Möglichkeit eines Datenverlusts unvermeidbar, wenn die primäre Datenbank ausfällt. Zum Schutz kritischer Transaktionen vor Datenverlust, kann ein Anwendungsentwickler die gespeicherte Prozedur [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) unmittelbar nach dem Commit der Transaktion aufrufen. Der Aufruf von `sp_wait_for_database_copy_sync` blockiert den aufrufenden Thread, bis die letzte committete Transaktion übertragen und im Transaktionsprotokoll der sekundären Datenbank gehärtet wurde. Er wartet jedoch nicht darauf, dass die übertragenen Transaktionen in der sekundären Datenbank wiedergegeben (wiederholt) werden. `sp_wait_for_database_copy_sync` ist auf eine bestimmte Georeplikationslink beschränkt. Jeder Benutzer mit den Rechten zum Herstellen der Verbindung mit der primären Datenbank kann diese Prozedur aufrufen.

> [!NOTE]
> `sp_wait_for_database_copy_sync` verhindert Datenverluste nach einem Geofailover für bestimmte Transaktionen, garantiert aber keine vollständige Synchronisierung für Lesezugriffe. Die durch einen `sp_wait_for_database_copy_sync`-Prozeduraufruf verursachte Verzögerung kann beträchtlich sein und hängt von der Größe des noch nicht übertragenen Transaktionsprotokolls auf der Primärseite zum Zeitpunkt des Aufrufs ab.

## <a name="monitor-geo-replication-lag"></a><a name="monitoring-geo-replication-lag"></a> Verzögerung bei der Georeplikation überwachen

Verwenden Sie zum Überwachen der Verzögerung in Bezug auf RPO in der primären Datenbank die Spalte *replication_lag_sec* von [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database). Sie zeigt die Verzögerung in Sekunden zwischen den Transaktionen, die auf dem primären System durchgeführt wurden, und den gehärteten Transaktionen im Transaktionsprotokoll des sekundären Systems. Beträgt die Verzögerung beispielsweise eine Sekunde, bedeutet dies, dass Transaktionen, die in der letzten Sekunde durchgeführt wurden, verloren gehen, wenn die Primärseite in diesem Moment von einem Ausfall betroffen ist und ein Geo-Failover eingeleitet wird.

Um die Verzögerung bei Änderungen in der primären Datenbank zu messen, die in der geo-sekundären Datenbank gehärtet wurden, vergleichen Sie die *last_commit*-Zeit in der geo-sekundären Datenbank mit demselben Wert in der primären Datenbank.

> [!TIP]
> Wenn *replication_lag_sec* auf der Primärseite NULL ist, bedeutet dies, dass die Primärseite derzeit nicht weiß, wie weit eine Geosekundärseite zurückliegt. Dieser Fall tritt normalerweise ein, nachdem der Prozess neu gestartet wurde, und es sollte sich um einen vorübergehenden Zustand handeln. Erwägen Sie das Senden einer Warnung, wenn *replication_lag_sec* über einen längeren Zeitraum NULL zurückgibt. Es kann darauf hinweisen, dass der Geo-Sekundärserver aufgrund eines Verbindungsfehlers nicht mit dem Primärserver kommunizieren kann.
> 
> Es gibt auch Bedingungen, die dazu führen können, dass der Unterschied zwischen der *last_commit* Zeit auf der Geosekundärseite und der Primärseite groß wird. Wenn z. B. nach einer langen Zeit ohne Änderungen ein Commit auf dem primären System durchgeführt wird, springt die Differenz auf einen großen Wert, bevor sie schnell wieder auf Null zurückgeht. Erwägen Sie, eine Warnmeldung zu senden, wenn die Differenz zwischen diesen beiden Werten über einen längeren Zeitraum groß bleibt.

## <a name="programmatically-manage-active-geo-replication"></a><a name="programmatically-managing-active-geo-replication"></a> Aktive Georeplikation programmatisch verwalten

Wie bereits erwähnt, kann die aktive Georeplikation auch programmatisch mit T-SQL, Azure PowerShell und REST API verwaltet werden. Die folgenden Tabellen beschreiben den verfügbaren Satz von Befehlen. Die aktive Georeplikation umfasst eine Reihe von Azure Resource Manager-APIs für die Verwaltung. Hierzu zählen unter anderem die [Azure SQL-Datenbank-REST-API](/rest/api/sql/) und [Azure PowerShell-Cmdlets](/powershell/azure/). Diese APIs unterstützen die rollenbasierte Zugriffskontrolle von Azure (Azure RBAC). Weitere Informationen zur Implementierung von Zugriffsrollen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="t-sql-manage-geo-failover-of-single-and-pooled-databases"></a><a name="t-sql-manage-failover-of-single-and-pooled-databases"></a> T-SQL: Verwalten von Geo-Failover von einzelnen und gepoolten Datenbanken

> [!IMPORTANT]
> Diese T-SQL-Befehle gelten nur für die aktive Georeplikation und nicht für Failover-Gruppen. Daher gelten sie auch nicht für SQL Managed Instance, das nur Failover-Gruppen unterstützt.

| Befehl | BESCHREIBUNG |
| --- | --- |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Verwenden Sie das Argument **ADD SECONDARY ON SERVER**, um eine sekundäre Datenbank für eine bestehende Datenbank zu erstellen und die Datenreplikation zu starten |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Verwenden Sie **FAILOVER** oder **FORCE_FAILOVER_ALLOW_DATA_LOSS**, um eine sekundäre Datenbank zur primären zu machen und ein Failover einzuleiten. |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Verwenden Sie **REMOVE SECONDARY ON SERVER**, um eine Datenreplikation zwischen einer SQL-Datenbank und der angegebenen sekundären Datenbank zu beenden. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Gibt Informationen über alle vorhandenen Replikationsverknüpfungen für alle Datenbanken auf einem Server zurück |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Ruft den Zeitpunkt der letzten Replikation, die Verzögerung der letzten Replikation und andere Informationen über die Replikationsverknüpfung für eine angegebene Datenbank ab. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Zeigt den Status aller Datenbankoperationen an, einschließlich Änderungen an Replikationsverbindungen. |
| [sys.sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |Bewirkt, dass die Anwendung wartet, bis alle bestätigten Transaktionen im Transaktionsprotokoll einer Geo-Sekundärdatei gehärtet sind. |
|  | |

### <a name="powershell-manage-geo-failover-of-single-and-pooled-databases"></a><a name="powershell-manage-failover-of-single-and-pooled-databases"></a> PowerShell: Verwalten von Geo-Failover von einzelnen und gepoolten Datenbanken

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

| Cmdlet | BESCHREIBUNG |
| --- | --- |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Ruft mindestens eine Datenbank ab. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary) |Erstellt eine sekundäre Datenbank für eine vorhandene Datenbank und startet die Datenreplikation. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary) |Erklärt die sekundäre Datenbank zur primären und wechselt zu ihr – dadurch wird das Failover gestartet. |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) |Beendet die Datenreplikation zwischen einer SQL-Datenbank und der angegebenen sekundären Datenbank. |
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Ruft die Geo-Replikationslinks für eine Datenbank ab. |
|  | |

> [!TIP]
> Beispielskripts finden Sie unter [Verwenden von PowerShell zum Konfigurieren der aktiven Georeplikation für eine einzelne Azure SQL-Datenbank](scripts/setup-geodr-and-failover-database-powershell.md) und [Verwenden von PowerShell zum Konfigurieren der aktiven Georeplikation für eine in einem Pool enthaltene Azure SQL-Datenbank](scripts/setup-geodr-and-failover-elastic-pool-powershell.md).

### <a name="rest-api-manage-geo-failover-of-single-and-pooled-databases"></a><a name="rest-api-manage-failover-of-single-and-pooled-databases"></a> REST-API: Verwalten von Geo-Failover von einzelnen und gepoolten Datenbanken

| API | BESCHREIBUNG |
| --- | --- |
| [Create or Update Database (createMode=Restore)](/rest/api/sql/databases/createorupdate) |Erstellt oder aktualisiert eine primäre oder sekundäre Datenbank oder stellt diese wieder her. |
| [Get Create or Update Database Status](/rest/api/sql/databases/createorupdate) |Ruft den Status während eines Erstellungsvorgangs ab. |
| [Set Secondary Database as Primary (Planned Failover)](/rest/api/sql/replicationlinks/failover) |Legt fest, welche sekundäre Datenbank als primäre Datenbank verwendet wird, indem ein Failover von der aktuellen primären Datenbank durchgeführt wird. **Diese Option wird für SQL Managed Instance nicht unterstützt.**|
| [Set Secondary Database as Primary (Unplanned Failover)](/rest/api/sql/replicationlinks/failoverallowdataloss) |Legt fest, welche sekundäre Datenbank als primäre Datenbank verwendet wird, indem ein Failover von der aktuellen primären Datenbank durchgeführt wird. Bei diesem Vorgang können Daten verloren gehen. **Diese Option wird für SQL Managed Instance nicht unterstützt.**|
| [Get Replication Link](/rest/api/sql/replicationlinks/get) |Ruft eine spezifische Replikationsverknüpfung für eine angegebene Datenbank in einer Georeplikationspartnerschaft ab. Es werden die Informationen abgerufen, die in der Katalogsicht „sys.geo_replication_links“ sichtbar sind. **Diese Option wird für SQL Managed Instance nicht unterstützt.**|
| [Replication Links - List By Database](/rest/api/sql/replicationlinks/listbydatabase) | Ruft alle Replikationsverknüpfungen für eine angegebene Datenbank in einer Georeplikationspartnerschaft ab. Es werden die Informationen abgerufen, die in der Katalogsicht „sys.geo_replication_links“ sichtbar sind. |
| [Delete Replication Link](/rest/api/sql/replicationlinks/delete) | Löscht einen Datenbankreplikationslink. Kann nicht während eines Failovers verwendet werden. |
|  | |

## <a name="next-steps"></a>Nächste Schritte

- Beispielskripts:
  - [Konfiguration und Failover einer einzelnen Datenbank mit aktiver Geo-Replikation](scripts/setup-geodr-and-failover-database-powershell.md).
  - [Konfiguration und Failover einer gepoolten Datenbank mit aktiver Geo-Replikation](scripts/setup-geodr-and-failover-elastic-pool-powershell.md).
- SQL-Datenbank unterstützt auch Autofailover-Gruppen. Weitere Informationen finden Sie unter [Autofailover-Gruppen](auto-failover-group-overview.md).
- Eine Übersicht und verschiedene Szenarien zum Thema Geschäftskontinuität finden Sie unter [Übersicht über die Geschäftskontinuität](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- Informationen über automatisierte Sicherungen von Azure SQL-Datenbanken finden Sie unter [Automatisierte SQL-Datenbanksicherungen](automated-backups-overview.md).
- Informationen zum Verwenden automatisierter Sicherungen für die Wiederherstellung finden Sie unter [Wiederherstellen einer Datenbank aus vom Dienst initiierten Sicherungen](recovery-using-backups.md).
- Weitere Informationen zu Authentifizierungsanforderungen für einen neuen primären Server und die Datenbank finden Sie unter [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](active-geo-replication-security-configure.md).
