---
title: Upgraden mithilfe von Sicherung und Wiederherstellung – Azure Database for PostgreSQL
description: Erfahren Sie, wie Offlineupgrades für Datenbanken mithilfe der Sicherungs- und Wiederherstellungsfunktionen durchgeführt werden, um zu einer höheren Version von Azure Database for PostgreSQL zu migrieren.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/21/2021
ms.openlocfilehash: b2216754cbdb6081a82f71392aee6e5f8ce2d3ba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128648409"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Upgrade einer PostgreSQL-Datenbank durch Sichern und Wiederherstellen

>[!NOTE]
> Die in dieser Dokumentation erläuterten Konzepte gelten sowohl für Azure Database for PostgreSQL – Einzelserver als auch für Azure Database for PostgreSQL – Flexible Server (Vorschau). 

Sie können einen in Azure Database for PostgreSQL bereitgestellten PostgreSQL-Server aktualisieren, indem Sie Ihre Datenbanken mithilfe der folgenden Methoden zu einem Server mit einer höheren Hauptversion migrieren.
* **Offlinemethode** mit [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) und [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) von PostgreSQL – die Datenmigration verursacht eine Downtime. Im vorliegenden Dokument wird diese Upgrade-/Migrationsmethode behandelt.
* **Onlinemethode** mit [Database Migration Service](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) (DMS). Bei dieser Methode erfolgt die Migration mit einer geringeren Downtime. Außerdem bleibt die Zieldatenbank mit der Quelldatenbank synchron, und Sie können wählen, wann der Übergang erfolgen soll. Allerdings müssen bei Verwendung von DMS einige Voraussetzungen und Einschränkungen beachtet werden. Ausführliche Informationen finden Sie in der [DMS-Dokumentation](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md). 

 In der folgenden Tabelle finden Sie Empfehlungen, die auf der Größe und dem Verwendungsszenario der Datenbank basieren.

| **Datenbank/Szenario** | **Sichern/Wiederherstellen (offline)** | **DMS (online)** |
| ------ | :------: | :-----: |
| Sie haben eine kleine Datenbank und können von einem Upgrade verursachte Ausfallzeiten verkraften  | X | |
| Kleine Datenbanken (< 10 GB)  | X | X | 
| Kleine bis mittelgroße Datenbanken (10 GB bis 100 GB) | X | X |
| Große Datenbanken (> 100 GB) |  | X |
| Können von einem Upgrade verursachte Ausfallzeiten (unabhängig von der Datenbankgröße) verkraftet werden? | X |  |
| Können [Voraussetzungen](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md#prerequisites) für DMS einschließlich Neustart erfüllt werden? |  | X |
| Können DDLs und nicht protokollierte Tabellen während des Upgradevorgangs vermieden werden? | |  X |

Dieser Leitfaden enthält einige Methoden und Beispiele für die Offlinemigration, um zu veranschaulichen, wie Sie vom Quellserver zum Zielserver migrieren können, auf dem eine höhere Version von PostgreSQL ausgeführt wird.

> [!NOTE]
> Die PostgreSQL-Sicherung und -Wiederherstellung kann auf verschiedene Weisen erfolgen. Sie können sich für eine Migration mit einer der in diesem Leitfaden angebotenen Methoden entscheiden oder alternative Möglichkeiten wählen, die Ihren Bedürfnissen entsprechen. Die detaillierte Syntax für Sicherung und Wiederherstellung mit zusätzlichen Parametern finden Sie in den Artikeln [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) und [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-database-for-postgresql"></a>Voraussetzungen für die Sicherung und Wiederherstellung mit Azure Database for PostgreSQL
 
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:

- Einen PostgreSQL-**Quelldatenbankserver**, auf dem eine niedrigere Version der Engine ausgeführt wird, die Sie aktualisieren möchten
- Einen PostgreSQL-**Zieldatenbankserver** mit der gewünschten Hauptversion von [Azure Database for PostgreSQL – Einzelserver](quickstart-create-server-database-portal.md) oder [Azure Database for PostgreSQL – Flexible Server](./flexible-server/quickstart-create-server-portal.md) 
- Ein PostgreSQL-Clientsystem zum Ausführen der Sicherungs- und Wiederherstellungsbefehle Es wird empfohlen, die höhere Datenbankversion zu verwenden. Wenn Sie beispielsweise ein Upgrade von PostgreSQL Version 9.6 auf 11 durchführen, sollten Sie die PostgreSQL-Client Version 11 verwenden. 
  - Dabei kann es sich um einen Linux- oder Windows-Client handeln, auf dem PostgreSQL sowie die Befehlszeilenprogramme [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) und [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) installiert sind. 
  - Alternativ können Sie [Azure Cloud Shell](https://shell.azure.com) verwenden, indem Sie auf der Menüleiste rechts oben im [Azure-Portal](https://portal.azure.com) auf Azure Cloud Shell klicken. Sie müssen sich bei Ihrem Konto `az login` anmelden, ehe Sie die Befehle zur Sicherung und Wiederherstellung ausführen können.
- Ihr PostgreSQL-Client, der vorzugsweise in derselben Region wie der Quell- und Zielserver ausgeführt wird 


## <a name="additional-details-and-considerations"></a>Weitere Details und Überlegungen
- Sie können die Verbindungszeichenfolge für die Quell- und Zieldatenbank finden, indem Sie im Portal auf „Verbindungszeichenfolgen“ klicken. 
- Möglicherweise führen Sie mehr als eine Datenbank auf dem Server aus. Sie finden die Liste der Datenbanken, indem Sie eine Verbindung mit dem Quellserver herstellen und `\l` ausführen.
- Erstellen Sie entsprechende Datenbanken auf dem Zieldatenbankserver, oder fügen Sie dem Befehl `pg_dump` die Option `-C` hinzu, mit der die Datenbanken erstellt werden.
- Sie dürfen keine Upgrades von `azure_maintenance` oder Vorlagendatenbanken durchführen. Wenn Sie Änderungen an Vorlagendatenbanken vorgenommen haben, können Sie die Änderungen migrieren oder in der Zieldatenbank vornehmen.
- Anhand der obigen Tabellen können Sie feststellen, ob die Datenbank für diesen Migrationsmodus geeignet ist.
- Wenn Sie Azure Cloud Shell verwenden möchten, sollten Sie beachten, dass die Sitzung nach 20 Minuten ein Timeout verursacht. Wenn die Datenbankgröße kleiner als 10 GB ist, können Sie das Upgrade möglicherweise ohne ein Timeout der Sitzung abschließen. Andernfalls müssen Sie die Sitzung möglicherweise auf andere Weise geöffnet halten, z. B. durch einmaliges Drücken einer beliebigen Taste alle 10-15 Minuten. 


## <a name="example-database-used-in-this-guide"></a>In dieser Anleitung verwendete Beispieldatenbank

In den Beispielen in diesem Leitfaden werden zur Veranschaulichung die folgenden Quellserver-, Zielserver- und Datenbanknamen verwendet.

 | **Beschreibung** | **Wert** |
 | ------- | ------- |
 | Quellserver (Version 9.5) | pg-95.postgres.database.azure.com |
 | Quelldatenbank | bench5gb |
 | Größe der Quelldatenbank | 5 GB |
 | Quellbenutzername | pg@pg-95 |
 | Zielserver (Version 11) | pg-11.postgres.database.azure.com |
 | Zieldatenbank | bench5gb |
 | Zielbenutzername | pg@pg-11 |

>[!NOTE]
> Flexible Server unterstützen PostgreSQL ab Version 11. Außerdem ist beim Benutzernamen für flexible Server @dbservername nicht erforderlich.

## <a name="upgrade-your-databases-using-offline-migration-methods"></a>Datenbankupgrade mithilfe von Offlinemigrationsmethoden
Sie können eine der in diesem Abschnitt beschriebenen Methoden für Ihre Upgrades verwenden. Sie können die folgenden Tipps bei der Durchführung der Aufgaben nutzen.

- Wenn Sie für Quell- und Zieldatenbank dasselbe Kennwort verwenden, können Sie die Umgebungsvariable `PGPASSWORD=yourPassword` festlegen.  Dann müssen Sie nicht jedes Mal ein Kennwort angeben, wenn Sie Befehle wie „psql“, „pg_dump“ und „pg_restore“ ausführen.  Ebenso können Sie zusätzliche Variablen wie `PGUSER`, `PGSSLMODE` usw. einrichten. Weitere Informationen finden Sie unter [PostgreSQL-Umgebungsvariablen](https://www.postgresql.org/docs/11/libpq-envars.html).
  
- Wenn Ihr PostgreSQL-Server TLS-/SSL-Verbindungen erfordert (auf Azure Database for PostgreSQL-Servern standardmäßig aktiviert), legen Sie eine Umgebungsvariable `PGSSLMODE=require` fest, damit das pg_restore-Tool über TLS eine Verbindung herstellt. Erfolgt die Verbindung nicht über TLS, kann der Fehler möglicherweise wie folgt lauten: `FATAL:  SSL connection is required. Please specify SSL options and retry.`

- Führen Sie in der Windows-Befehlszeile den Befehl `SET PGSSLMODE=require` aus, bevor Sie den Befehl „pg_restore“ ausführen. Führen Sie unter Linux oder Bash den Befehl `export PGSSLMODE=require` aus, bevor Sie den Befehl „pg_restore“ ausführen.

>[!Important]
> Die in diesem Dokument bereitgestellten Schritte und Methoden enthalten einige Beispiele für pg_dump/pg_restore-Befehle und stellen nicht alle möglichen Schritte zum Ausführen von Upgrades dar. Es wird empfohlen, die Befehle in einer Testumgebung zu testen und zu überprüfen, bevor Sie sie in der Produktionsumgebung verwenden.

### <a name="migrate-the-roles"></a>Migrieren von Rollen

Rollen (Benutzer) sind globale Objekte und müssen vor dem Wiederherstellen der Datenbank separat zum neuen Cluster migriert werden. Sie können `pg_dumpall` binär mit der Option „-r“ (--roles-only) verwenden, um sie zu sichern.
Beim Sichern aller Rollen des Quellservers

```azurecli-interactive
pg_dumpall -r --host=mySourceServer --port=5432 --username=myUser --database=mySourceDB > roles.sql
```

Bearbeiten Sie `roles.sql` und entfernen Sie Verweise von `NOSUPERUSER` und `NOBYPASSRLS`, bevor Sie den Inhalt mit psql auf dem Zielserver wiederherstellen:

```azurecli-interactive
psql -f roles.sql --host=myTargetServer --port=5432 --username=myUser
```

Es sollte nicht erwartet werden, dass die Sicherungsdatei vollständig fehlerfrei ausgeführt wird. Da das Skript CREATE ROLE für jede im Quellcluster existierende Rolle ausgibt, kann davon ausgegangen werden, dass es für den Bootstrap-Superuser wie azure_pg_admin oder azure_superuser den Fehler „role already exists“ (Rolle ist bereits vorhanden) anzeigt. Dieser Fehler ist harmlos und kann ignoriert werden. Die Verwendung der Option `--clean` erzeugt wahrscheinlich zusätzliche harmlose Fehlermeldungen zu nicht vorhandenen Objekten. Sie können diese jedoch minimieren, indem Sie `--if-exists` hinzufügen.


### <a name="method-1-using-pg_dump-and-psql"></a>Methode 1: Verwenden von pg_dump und psql

Diese Methode umfasst zwei Schritte. Zunächst müssen Sie eine SQL-Datei vom Quellserver mithilfe von `pg_dump` speichern. Der zweite Schritt besteht im Importieren der Datei auf den Zielserver mithilfe von `psql`. Weitere Informationen finden Sie in der Dokumentation [Migrieren mithilfe von Export und Import](howto-migrate-using-export-and-import.md).

### <a name="method-2-using-pg_dump-and-pg_restore"></a>Methode 2: Verwenden von pg_dump und psql

Bei dieser Upgrademethode erstellen Sie zunächst mithilfe von `pg_dump` eine Sicherungsdatei vom Quellserver. Anschließend stellen Sie diese Sicherungsdatei mithilfe von `pg_restore` auf dem Zielserver wieder her. Weitere Informationen finden Sie in der Dokumentation [Migrieren durch Sicherungen und Wiederherstellungen](howto-migrate-using-dump-and-restore.md). 

### <a name="method-3-using-streaming-the-dump-data-to-the-target-database"></a>Methode 3: Verwenden von Streaming für das Sichern der Datei in der Zieldatenbank

Wenn Sie keinen PostgreSQL-Client besitzen oder Azure Cloud Shell verwenden möchten, können Sie diese Methode nutzen. Die Datenbanksicherung wird direkt auf den Zieldatenbankserver gestreamt, ohne dass die Sicherung auf dem Client gespeichert wird. Folglich kann diese Methode bei einem Client mit begrenztem Speicherplatz verwendet und sogar über Azure Cloud Shell ausgeführt werden. 

1. Vergewissern Sie sich mit dem Befehl `\l`, dass die Datenbank auf dem Zielserver vorhanden ist. Wenn die Datenbank nicht vorhanden ist, erstellen Sie sie.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. Führen Sie die Sicherung und Wiederherstellung als einzelne Befehlszeile mithilfe einer Pipe aus. 
    ```azurecli-interactive
    pg_dump -Fc --host=mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore  --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    Beispiel:

    ```azurecli-interactive
    pg_dump -Fc --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. Nach Abschluss des Prozesses zum Upgrade (Migration) können Sie Ihre Anwendung auf dem Zielserver testen. 
4. Wiederholen Sie diesen Vorgang für alle Datenbanken auf dem Server.

 Die folgende Tabelle veranschaulicht beispielsweise, wie lange die Migration mit der Sicherungsmethode per Streaming gedauert hat. Die Beispieldaten werden mithilfe von [pgbench](https://www.postgresql.org/docs/10/pgbench.html) aufgefüllt. Da Ihre Datenbank eine andere Anzahl von Objekten mit unterschiedlichen Größen als die von pgbench generierten Tabellen und Indizes enthalten kann, wird dringend empfohlen, die Befehle zur Sicherung und Wiederherstellung Ihrer Datenbank zu testen, um die tatsächliche Dauer der Aktualisierung Ihrer Datenbank zu ermitteln. 

| **Datenbankgröße** | **Ca. benötigte Zeit** | 
| ----- | ------ |
| 1 GB  | 1-2 Minuten |
| 5 GB | 8-10 Minuten |
| 10 GB | 15-20 Minuten |
| 50 GB | 1-1,5 Stunden |
| 100 GB | 2,5-3 Stunden|
   
### <a name="method-4-using-parallel-dump-and-restore"></a>Methode 4: Verwenden von parallelen Sicherungen und Wiederherstellungen 

Diese Methode eignet sich, wenn die Datenbank nur wenige größere Tabellen enthält und Sie den Sicherungs- und Wiederherstellungsprozess für diese Datenbank parallel ausführen möchten. Außerdem benötigen Sie auf Ihrem Clientsystem ausreichend Speicherplatz für die Sicherungen. Dieser parallele Sicherungs- und Wiederherstellungsprozess verringert den Zeitaufwand für die gesamte Migration. Beispielsweise wurde der Vorgang für die 50 GB große pgbench-Datenbank, deren Migration mit Methode 1 und 2 zwischen 1 und 1,5 Stunden dauerte, mit dieser Methode in weniger als 30 Minuten abgeschlossen.

1. Erstellen Sie für jede Datenbank auf dem Quellserver eine entsprechende Datenbank auf dem Zielserver.

    ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    ```

    ```SQL
    postgres> create database myDB;
   ```

   Beispiel:
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"
    psql (12.3 (Ubuntu 12.3-1.pgdg18.04+1), server 13.3)
    SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres> create database bench5gb;
    postgres> \q
    ```

2. Führen Sie den Befehl pg_dump in einem Verzeichnisformat mit „Anzahl der Aufträge = 4 (Anzahl der Tabellen in der Datenbank)“ aus. Bei einem höheren Computetarif und mehr Tabellen können Sie diesen Wert steigern. Mit diesem Befehl pg_dump wird ein Verzeichnis erstellt, in dem komprimierte Dateien für jeden Auftrag gespeichert werden.

    ```azurecli-interactive
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    Beispiel:
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. Stellen Sie anschließend die Sicherung auf dem Zielserver wieder her.
    ```azurecli-interactive
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    Beispiel:
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> Der in diesem Dokument erwähnte Prozess kann auch zum Einsatz kommen, um Ihre Instanz von Azure Database for PostgreSQL – Flexibler Server (in der Vorschau) zu aktualisieren. Der Hauptunterschied besteht darin, dass die Verbindungszeichenfolge für das Ziel auf dem flexiblen Server `@dbName` nicht enthält.  Wenn der Benutzername beispielsweise `pg` ist, lautet der Benutzername des einzelnen Servers in der Verbindungszeichenfolge `pg@pg-95`, während Sie bei einem flexiblen Server einfach `pg` verwenden können.

## <a name="post-upgrademigrate"></a>Nach dem Upgrade/Migrieren
Nach Abschluss des Upgrades der Hauptversion wird empfohlen, den Befehl `ANALYZE` in jeder Datenbank auszuführen, um die Tabelle `pg_statistic` zu aktualisieren. Andernfalls können Leistungsprobleme auftreten.

```SQL
postgres=> analyze;
ANALYZE
```

## <a name="next-steps"></a>Nächste Schritte

- Wenn Sie mit der Funktionsweise der Zieldatenbank zufrieden sind, können Sie Ihren alten Datenbankserver löschen. 
- Nur für Azure Database for PostgreSQL – Einzelserver. Wenn Sie den gleichen Datenbankendpunkt wie der Quellserver verwenden möchten, können Sie, nachdem Sie Ihren alten Quelldatenbankserver gelöscht haben, ein Lesereplikat mit dem Namen des alten Datenbankservers erstellen. Sobald der Replikationszustand stabil ist, können Sie das Replikat beenden, wodurch der Replikatserver zu einem unabhängigen Server höher gestuft wird. Weitere Informationen finden Sie unter [Replikation](./concepts-read-replicas.md).

>[!Important] 
> Es wird dringend empfohlen, die neue aktualisierte PostgreSQL-Version zu testen, bevor Sie sie direkt für die Produktion verwenden. Dazu gehört der Vergleich von Serverparametern zwischen der älteren Quellversionsquelle und dem neueren Versionsziel. Stellen Sie sicher, dass sie identisch sind, und überprüfen Sie alle neuen Parameter, die in der neuen Version hinzugefügt wurden. Unterschiede zwischen den Versionen finden Sie [hier](https://www.postgresql.org/docs/release/).
