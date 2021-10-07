---
title: Systemtabellen – Hyperscale (Citus) – Azure Database for PostgreSQL
description: Metadaten für die Ausführung verteilter Abfragen
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 8213ca39f85a50d7e5354948467f3fbd66cb4797
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094726"
---
# <a name="system-tables-and-views"></a>Systemtabellen und Sichten

Hyperscale (Citus) erstellt und verwaltet spezielle Tabellen, die Informationen zu verteilten Daten in der Servergruppe enthalten. Der Koordinatorknoten zieht diese Tabellen zu Rate, wenn er plant, wie Abfragen auf den Workerknoten ausgeführt werden sollen.

## <a name="coordinator-metadata"></a>Koordinatormetadaten

Hyperscale (Citus) teilt jede verteilte Tabelle anhand der Verteilungsspalte in mehrere logische Shards auf. Der Koordinator verwaltet dann Metadatentabellen, um Statistiken und Informationen zur Integrität und zum Speicherort dieser Shards nachzuverfolgen.

In diesem Abschnitt wird jede dieser Metadatentabellen sowie das jeweils zugehörige Schema beschrieben.
Sie können diese Tabellen nach der Anmeldung beim Koordinatorknoten mithilfe von SQL anzeigen und abfragen.

> [!NOTE]
>
> Servergruppen mit Hyperscale (Citus), auf denen ältere Versionen der Citus-Engine ausgeführt werden, bieten möglicherweise nicht alle unten aufgeführten Tabellen.

### <a name="partition-table"></a>Partitionstabelle

Die pg\_dist\_partition-Tabelle speichert Metadaten dazu, welche Tabellen in der Datenbank verteilte Tabellen sind. Für jede verteilte Tabelle speichert sie auch Informationen zur Verteilungsmethode und detaillierte Informationen zur Verteilungsspalte.

| Name         | Type     | BESCHREIBUNG                                                                                                                                                                                                                                           |
|--------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid | regclass | Verteilte Tabelle, der diese Zeile entspricht. Dieser Wert verweist auf die relfilenode-Spalte in der pg_class-Systemkatalogtabelle.                                                                                                                   |
| partmethod   | char     | Die Methode, die zum Partitionieren/Verteilen verwendet wird. Die Werte dieser Spalte, die verschiedenen Verteilungsmethoden entsprechen, sind „Anfügen: „a“, Hash: „h“, Verweistabelle: „n“.                                                                          |
| partkey      | text     | Ausführliche Informationen zur Verteilungsspalte, einschließlich der Spaltennummer, des Typs und anderer relevanter Informationen.                                                                                                                                      |
| colocationid | integer  | Die Colocationsgruppe, zu der diese Tabelle gehört. Tabellen in derselben Gruppe lassen neben anderen Optimierungen Joins mit Colocation und verteilte Rollups zu. Dieser Wert verweist auf die colocationid-Spalte in der pg_dist_colocation-Tabelle.                      |
| repmodel     | char     | Die Methode, die für Datenreplikation verwendet wird. Die Werte dieser Spalte entsprechen den verschiedenen Replikationsmethoden: auf Anweisungen basierende Citus-Replikation: „c“, PostgreSQL-Streamingreplikation: „s“, Zweiphasencommit (für Verweistabellen): „t“. |

```
SELECT * from pg_dist_partition;
 logicalrelid  | partmethod |                                                        partkey                                                         | colocationid | repmodel 
---------------+------------+------------------------------------------------------------------------------------------------------------------------+--------------+----------
 github_events | h          | {VAR :varno 1 :varattno 4 :vartype 20 :vartypmod -1 :varcollid 0 :varlevelsup 0 :varnoold 1 :varoattno 4 :location -1} |            2 | c
 (1 row)
```

### <a name="shard-table"></a>Shardtabelle

In der pg\_dist\_shard-Tabelle werden Metadaten zu einzelnen Shards einer Tabelle gespeichert. Pg_dist_shard enthält Informationen dazu, zu welcher verteilten Tabelle Sards gehören, sowie Statistiken zur Verteilungsspalte für Shards.
Für verteilte Tabellen vom Typ „Anfügen“ entsprechen diese Statistiken den Minimal-/Maximalwerten der Verteilungsspalte. Bei Tabellen mit Hashverteilung handelt es sich um Hashtokenbereiche, die diesem Shard zugewiesen sind. Diese Statistiken werden zum Bereinigen nicht verknüpfter Shards während SELECT-Abfragen verwendet.

| Name          | Type     | BESCHREIBUNG                                                                                                                                                                                  |
|---------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid  | regclass | Verteilte Tabelle, der diese Zeile entspricht. Dieser Wert verweist auf die relfilenode-Spalte in der pg_class-Systemkatalogtabelle.                                                          |
| shardid       | BIGINT   | Der global eindeutige Bezeichner, der diesem Shard zugewiesen ist.                                                                                                                                           |
| shardstorage  | char     | Der Typ des für diesen Shard verwendeten Speichers. In der folgenden Tabelle werden verschiedene Speichertypen erläutert.                                                                                               |
| shardminvalue | text     | Für verteilte Tabellen vom Typ „Anfügen“ der Mindestwert der Verteilungsspalte in diesem Shard (inklusiv). Bei Tabellen mit Hashverteilung der minimale Hashtokenwert, der diesem Shard zugewiesen ist (inklusiv). |
| shardmaxvalue | text     | Für verteilte Tabellen vom Typ „Anfügen“ der Höchstwert der Verteilungsspalte in diesem Shard (inklusiv). Bei Tabellen mit Hashverteilung der maximale Hashtokenwert, der diesem Shard zugewiesen ist (inklusiv). |

```
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

#### <a name="shard-storage-types"></a>Shardspeichertypen

Die shardstorage-Spalte in pg\_dist\_shard gibt den Typ des für den Shard verwendeten Speichers an. Eine kurze Übersicht über die verschiedenen Shardspeichertypen und deren Darstellung finden Sie unten.

| Speichertyp | shardstorage-Wert | BESCHREIBUNG                                                                        |
|--------------|--------------------|------------------------------------------------------------------------------------|
| TABLE        | „t“                | Gibt an, dass der Shard Daten speichert, die zu einer regulären verteilten Tabelle gehören.         |
| COLUMNAR     | „c“                | Gibt an, dass der Shard columnar-Daten speichert. (Wird von verteilten cstore_fdw-Tabellen verwendet) |
| FOREIGN      | „f“                | Gibt an, dass der Shard Fremddaten speichert. (Wird von verteilten file_fdw-Tabellen verwendet)    |

### <a name="shard-placement-table"></a>Shardplatzierungstabelle

Die pg\_dist\_placement-Tabelle verfolgt den Speicherort von Shardreplikaten auf Workerknoten nach. Jedes Replikat eines Shards, das einem bestimmten Knoten zugewiesen ist, wird als Shardplatzierung bezeichnet. In dieser Tabelle werden Informationen zur Integrität und zum Speicherort der einzelnen Shardplatzierungen gespeichert.

| Name        | Type   | BESCHREIBUNG                                                                                                                               |
|-------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------|
| shardid     | BIGINT | Der Shardbezeichner, der dieser Platzierung zugeordnet ist. Dieser Wert verweist auf die shardid-Spalte in der pg_dist_shard-Katalogtabelle.             |
| shardstate  | INT    | Beschreibt den Status dieser Platzierung. Verschiedene Shardzustände werden im Abschnitt unten erläutert.                                         |
| shardlength | BIGINT | Für verteilte Tabellen vom Typ „Anfügen“ die Größe der Shardplatzierung auf dem Workerknoten in Bytes. Tabellen mit Hashverteilung NULL.            |
| placementid | BIGINT | Eindeutiger automatisch generierter Bezeichner für jede einzelne Platzierung.                                                                           |
| groupid     | INT    | Bezeichnet eine Gruppe mit einem primären Server und null oder mehr sekundären Servern, wenn das Streamingreplikationsmodell verwendet wird. |

```
SELECT * from pg_dist_placement;
  shardid | shardstate | shardlength | placementid | groupid
 ---------+------------+-------------+-------------+---------
   102008 |          1 |           0 |           1 |       1
   102008 |          1 |           0 |           2 |       2
   102009 |          1 |           0 |           3 |       2
   102009 |          1 |           0 |           4 |       3
   102010 |          1 |           0 |           5 |       3
   102010 |          1 |           0 |           6 |       4
   102011 |          1 |           0 |           7 |       4
```

#### <a name="shard-placement-states"></a>Shardplatzierungszustände

Hyperscale (Citus) verwaltet die Shardintegrität pro Platzierung. Wenn eine Platzierung das System in einen inkonsistenten Zustand versetzt, markiert Citus ihn automatisch als nicht verfügbar. Der Platzierungszustand wird in der pg_dist_shard_placement-Tabelle innerhalb der shardstate-Spalte aufgezeichnet. Im Folgenden finden Sie eine kurze Übersicht über die verschiedenen Shardplatzierungszustände:

| Name des Zustands | Shardzustandswert | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                                                                                                         |
|------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| FINALIZED  | 1                | Der Zustand, in dem neue Shards erstellt werden. Shardplatzierungen in diesem Zustand gelten als aktuell und werden bei der Abfrageplanung und -ausführung verwendet.                                                                                                                                                                                                                                                                                 |
| INACTIVE   | 3                | Shardplatzierungen in diesem Status werden als inaktiv eingestuft, da Sie nicht mit anderen Replikaten desselben Shards synchronisiert sind. Der Zustand kann auftreten, wenn ein Anfüge-, Änderungs- (INSERT, UPDATE, DELETE) oder DDL-Vorgang für diese Platzierung fehlschlägt. Der Abfrageplaner ignoriert bei der Planung und Ausführung Platzierungen in diesem Zustand. Benutzer können die Daten in diesen Shards mit einem finalisierten Replikat als Hintergrundaktivität synchronisieren. |
| TO_DELETE  | 4                | Wenn Citus erfolglos versucht, eine Shardplatzierung als Reaktion auf einen master_apply_delete_command-Aufruf zu löschen, geht die Platzierung in diesen Zustand über. Benutzer können diese Shards dann als nachfolgende Hintergrundaktivität löschen.                                                                                                                                                                                                              |

### <a name="worker-node-table"></a>Workerknotentabelle

Die pg\_dist\_node-Tabelle enthält Informationen zu den Workerknoten im Cluster.

| Name             | Type    | BESCHREIBUNG                                                                                                                                                                                |
|------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nodeid           | INT     | Automatisch generierter Bezeichner für einen einzelnen Knoten.                                                                                                                                          |
| groupid          | INT     | Bezeichner zum Bezeichnen einer Gruppe mit einem primären Server und null oder mehr sekundären Servern, wenn das Streamingreplikationsmodell verwendet wird. Standardmäßig ist dieser Wert mit dem Wert für nodeid identisch.         |
| nodename         | text    | Der Hostname oder die IP-Adresse des PostgreSQL-Workerknotens.                                                                                                                                     |
| nodeport         | INT     | Die Portnummer, an der PostgreSQL-Workerknoten lauscht.                                                                                                                              |
| noderack         | text    | (Optional) Informationen zur Regalplatzierung für den Workerknoten.                                                                                                                                 |
| hasmetadata      | boolean | Für die interne Verwendung reserviert.                                                                                                                                                                 |
| isactive         | boolean | Gibt an, ob der Knoten aktiv Shardplatzierungen annimmt.                                                                                                                                     |
| noderole         | text    | Gibt an, ob der Knoten ein primärer oder sekundärer Knoten ist.                                                                                                                                                 |
| nodecluster      | text    | Der Name des Clusters, der diesen Knoten enthält.                                                                                                                                               |
| schuldhaveshards | boolean | Wenn FALSE, werden Shards beim erneuten Ausgleichenen vom Knoten verschoben (entleert). Es werden auch keine Shards von neuen verteilten Tabellen auf dem Knoten platziert, es sei denn, es erfolgt Colocation mit dort bereits vorhandenen Shards. |

```
SELECT * from pg_dist_node;
 nodeid | groupid | nodename  | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | shouldhaveshards
--------+---------+-----------+----------+----------+-------------+----------+----------+-------------+------------------
      1 |       1 | localhost |    12345 | default  | f           | t        | primary  | default     | t
      2 |       2 | localhost |    12346 | default  | f           | t        | primary  | default     | t
      3 |       3 | localhost |    12347 | default  | f           | t        | primary  | default     | t
(3 rows)
```

### <a name="distributed-object-table"></a>Tabelle verteilter Objekte

Die Citus.pg\_dist\_object-Tabelle enthält eine Liste von Objekten, z. B. von Typen und Funktionen, die auf dem Koordinatorknoten erstellt und an Workerknoten weitergegeben wurden. Wenn ein Administrator dem Cluster neue Workerknoten hinzufügt, erstellt Hyperscale (Citus) automatisch Kopien der verteilten Objekte auf den neuen Knoten (in der richtigen Reihenfolge, um Objektabhängigkeiten zu erfüllen).

| Name                        | Type    | BESCHREIBUNG                                          |
|-----------------------------|---------|------------------------------------------------------|
| classid                     | oid     | Klasse des verteilten Objekts                      |
| objid                       | oid     | Objekt-ID des verteilten Objekts                  |
| objsubid                    | integer | Objektunter-ID des verteilten Objekts, z. B. attnum |
| type                        | text    | Teil der bei pg-Upgrades verwendeten stabilen Adresse   |
| object_names                | text[]  | Teil der bei pg-Upgrades verwendeten stabilen Adresse   |
| object_args                 | text[]  | Teil der bei pg-Upgrades verwendeten stabilen Adresse   |
| distribution_argument_index | integer | Nur gültig für verteilte Funktionen/Prozeduren      |
| colocationid                | integer | Nur gültig für verteilte Funktionen/Prozeduren      |

\"Stabile Adressen\" identifizieren Objekte unabhängig von einem bestimmten Server eindeutig. Hyperscale (Citus) verfolgt Objekte während eines PostgreSQL-Upgrades mithilfe stabiler Adressen nach, die mit der [pg\_identify\_object\_as\_address()](https://www.postgresql.org/docs/current/functions-info.html#FUNCTIONS-INFO-OBJECT-TABLE)-Funktion erstellt werden.

Hier sehen Sie ein Beispiel dafür, wie `create_distributed_function()` der `citus.pg_dist_object`-Tabelle Einträge hinzufügt:

```psql
CREATE TYPE stoplight AS enum ('green', 'yellow', 'red');

CREATE OR REPLACE FUNCTION intersection()
RETURNS stoplight AS $$
DECLARE
        color stoplight;
BEGIN
        SELECT *
          FROM unnest(enum_range(NULL::stoplight)) INTO color
         ORDER BY random() LIMIT 1;
        RETURN color;
END;
$$ LANGUAGE plpgsql VOLATILE;

SELECT create_distributed_function('intersection()');

-- will have two rows, one for the TYPE and one for the FUNCTION
TABLE citus.pg_dist_object;
```

```
-[ RECORD 1 ]---------------+------
classid                     | 1247
objid                       | 16780
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
-[ RECORD 2 ]---------------+------
classid                     | 1255
objid                       | 16788
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
```

### <a name="colocation-group-table"></a>Tabelle der Colocationsgruppe

Die pg\_dist\_colocation-Tabelle enthält Informationen dazu, in welchen Tabellen\' Shards zusammen platziert werden sollen ([Colocation](concepts-hyperscale-colocation.md)).
Wenn sich zwei Tabellen in derselben Colocationsgruppe befinden, stellt Hyperscale (Citus) sicher, dass Shards mit denselben Partitionswerten auf denselben Workerknoten platziert werden.
Colocation ermöglicht Joinoptimierungen, bestimmte verteilte Rollups und Fremdschlüsselunterstützung. Shardcolocation wird eingeleitet, wenn die Shardanzahl, Replikationsfaktoren und Partitionsspaltentypen zwischen zwei Tabellen insgesamt übereinstimmen. Wenn dies gewünscht wird, kann jedoch eine benutzerdefinierte Colocationsgruppe angegeben werden, wenn eine verteilte Tabelle erstellt wird.

| Name                   | Type | BESCHREIBUNG                                                                   |
|------------------------|------|-------------------------------------------------------------------------------|
| colocationid           | INT  | Eindeutiger Bezeichner für die Colocationsgruppe, der diese Zeile entspricht.          |
| shardcount             | INT  | Die Shardsanzahl für alle Tabellen in dieser Colocationsgruppe                          |
| replicationfactor      | INT  | Der Replikationsfaktor für alle Tabellen in dieser Colocationsgruppe.                  |
| distributioncolumntype | oid  | Der Typ der Verteilungsspalte für alle Tabellen in dieser Colocationsgruppe. |

```
SELECT * from pg_dist_colocation;
  colocationid | shardcount | replicationfactor | distributioncolumntype 
 --------------+------------+-------------------+------------------------
             2 |         32 |                 2 |                     20
  (1 row)
```

### <a name="rebalancer-strategy-table"></a>Rebalancer-Strategietabelle

Diese Tabelle definiert Strategien, die [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) verwenden kann, um zu bestimmen, wohin Shards verschoben werden sollen.

| Name                           | Type    | BESCHREIBUNG                                                                                                                                       |
|--------------------------------|---------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| default_strategy               | boolean | Gibt an, ob rebalance_table_shards diese Strategie standardmäßig auswählen soll. Verwenden Sie citus_set_default_rebalance_strategy, um diese Spalte zu aktualisieren.             |
| shard_cost_function            | regproc | Ein Bezeichner für eine Kostenfunktion, die eine shardid als bigint-Typ annehmen muss und Kosten vom Typ „real“ zurückgibt.                                |
| node_capacity_function         | regproc | Ein Bezeichner für eine Kapazitätsfunktion, die eine nodeid als int-Typ annehmen muss und Knotenkapazität als Typ „real“ zurückgibt.                          |
| shard_allowed_on_node_function | regproc | Ein Bezeichner für eine Funktion, die shardid (Typ „bigint“) und nodeidarg (Typ „int“) annimmt und einen booleschen Wert zurückgibt, der besagt, ob Citus den Shard auf dem Knoten speichern soll. |
| default_threshold              | float4  | Schwellenwert, ab dem ein Knoten als zu voll oder zu leer angesehen wird, der bestimmt, wann rebalance_table_shards versuchen soll, Shards zu verschieben.                    |
| minimum_threshold              | float4  | Eine Sicherung, um zu verhindern, dass das Schwellenwertargument von rebalance_table_shards() zu niedrig festgelegt wird.                                                  |

Eine Hyperscale (Citus)-Installation umfasst diese Strategien in der Tabelle:

```postgresql
SELECT * FROM pg_dist_rebalance_strategy;
```

```
-[ RECORD 1 ]-------------------+-----------------------------------
Name                            | by_shard_count
default_strategy                | true
shard_cost_function             | citus_shard_cost_1
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0
minimum_threshold               | 0
-[ RECORD 2 ]-------------------+-----------------------------------
Name                            | by_disk_size
default_strategy                | false
shard_cost_function             | citus_shard_cost_by_disk_size
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0.1
minimum_threshold               | 0.01
```

Die Standardstrategie (`by_shard_count`) weist jedem Shard dieselben Kosten zu. Ihre Auswirkung besteht darin, die Anzahl der Shards knotenübergreifend auszugleichen. Mit der anderen vordefinierten Strategie (`by_disk_size`) werden jedem Shard entsprechend seiner Datenträgergröße in Bytes zuzüglich der Datenträgergröße der Shards, für die Colocation vorliegt, Kosten zugewiesen. Die Datenträgergröße wird mit `pg_total_relation_size`berechnet, sodass Indizes enthalten sind. Diese Strategie versucht, den gleichen Speicherplatz auf jedem Knoten zu erzielen. Beachten Sie den Schwellenwert von 0,1: Er verhindert unnötige Shardverschiebungen aufgrund unbedeutender Unterschiede im Speicherplatz.

#### <a name="creating-custom-rebalancer-strategies"></a>Erstellen von benutzerdefinierten Rebalancerstrategien

Im folgenden finden Sie Beispiele für Funktionen, die in neuen Shardrebalancerstrategien verwendet und in [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) mit der [citus_add_rebalance_strategy](reference-hyperscale-functions.md#citus_add_rebalance_strategy)-Funktion registriert werden.

-   Festlegen einer Knotenkapazitätsausnahme nach dem hostname-Muster:

    ```postgresql
    CREATE FUNCTION v2_node_double_capacity(nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename LIKE '%.v2.worker.citusdata.com' THEN 2 ELSE 1 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    ```

-   Neuzuordnung nach Anzahl von Abfragen für einen Shard, gemessen durch [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table):

    ```postgresql
    -- example of shard_cost_function

    CREATE FUNCTION cost_of_shard_by_number_of_queries(shardid bigint)
        RETURNS real AS $$
        SELECT coalesce(sum(calls)::real, 0.001) as shard_total_queries
        FROM citus_stat_statements
        WHERE partition_key is not null
            AND get_shard_id_for_distribution_column('tab', partition_key) = shardid;
    $$ LANGUAGE sql;
    ```

-   Isolieren eines bestimmten Shards (10000) auf einem Knoten (Adresse \'10.0.0.1\'):

    ```postgresql
    -- example of shard_allowed_on_node_function

    CREATE FUNCTION isolate_shard_10000_on_10_0_0_1(shardid bigint, nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN shardid = 10000 ELSE shardid != 10000 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;

    -- The next two definitions are recommended in combination with the above function.
    -- This way the average utilization of nodes is not impacted by the isolated shard.
    CREATE FUNCTION no_capacity_for_10_0_0_1(nodeidarg int)
        RETURNS real AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN 0 ELSE 1 END)::real
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    CREATE FUNCTION no_cost_for_10000(shardid bigint)
        RETURNS real AS $$
        SELECT
            (CASE WHEN shardid = 10000 THEN 0 ELSE 1 END)::real
        $$ LANGUAGE sql;
    ```

### <a name="query-statistics-table"></a>Abfragen der Statistiktabelle

Hyperscale (Citus) bietet `citus_stat_statements` für Statistiken zur Ausführung von Abfragen und zum Abfrageziel. Das Element ist analog zur [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html)-Sicht in PostgreSQL (und kann mit ihr verknüpft werden), die Statistiken zur Abfragegeschwindigkeit nachverfolgt.

In dieser Sicht können Abfragen von Ursprungsmandanten in einer mehrinstanzenfähigen Anwendung überwacht werden. Dies hilft bei der Entscheidung, wann Mandantenisolation ausgeführt werden sollte.

| Name          | Type   | BESCHREIBUNG                                                                      |
|---------------|--------|----------------------------------------------------------------------------------|
| queryid       | BIGINT | Bezeichner (gut geeignet für pg_stat_statements-Joins)                                   |
| userid        | oid    | Der Benutzer, der die Abfrage ausgeführt hat.                                                           |
| dbid          | oid    | Die Datenbankinstanz des Koordinators.                                                 |
| Abfrage         | text   | Die anonymisierte Abfragezeichenfolge.                                                          |
| Executor      | text   | Verwendeter Citus-Executor: adaptive, real-time, task-tracker, router oder insert-select. |
| partition_key | text   | Der Wert der Verteilungsspalte in Abfragen vom Typ „router“, andernfalls NULL               |
| calls         | BIGINT | Die Häufigkeit der Abfrageausführung.                                                |

```sql
-- create and populate distributed table
create table foo ( id int );
select create_distributed_table('foo', 'id');
insert into foo select generate_series(1,100);

-- enable stats
-- pg_stat_statements must be in shared_preload libraries
create extension pg_stat_statements;

select count(*) from foo;
select * from foo where id = 42;

select * from citus_stat_statements;
```

Ergebnisse:

```
-[ RECORD 1 ]-+----------------------------------------------
queryid       | -909556869173432820
userid        | 10
dbid          | 13340
query         | insert into foo select generate_series($1,$2)
executor      | insert-select
partition_key |
calls         | 1
-[ RECORD 2 ]-+----------------------------------------------
queryid       | 3919808845681956665
userid        | 10
dbid          | 13340
query         | select count(*) from foo;
executor      | adaptive
partition_key |
calls         | 1
-[ RECORD 3 ]-+----------------------------------------------
queryid       | 5351346905785208738
userid        | 10
dbid          | 13340
query         | select * from foo where id = $1
executor      | adaptive
partition_key | 42
calls         | 1
```

Zu beachten:

-   Die Statistikdaten werden nicht repliziert und überstehen keine Datenbankabstürze oder Failovervorgänge.
-   Verfolgt eine begrenzte Anzahl von Abfragen nach, die vom `pg_stat_statements.max`-GUC (Standardwert 5.000) festgelegt werden.
-   Verwenden Sie die `citus_stat_statements_reset()`-Funktion, um die Tabelle abzuschneiden.

### <a name="distributed-query-activity"></a>Aktivität verteilter Abfragen

Hyperscale (Citus) bietet spezielle Sichten zum Überwachen von Abfragen und Sperren im gesamten Cluster, einschließlich shardspezifischer Abfragen, die intern zum Erstellen von Ergebnissen für verteilte Abfragen verwendet werden.

-   **citus\_dist\_stat\_activity**: Zeigt die verteilten Abfragen an, die auf allen Knoten ausgeführt werden. Eine Obermenge von `pg_stat_activity`, die überall dort verwendet werden kann, wo sich dieses Element befindet.
-   **citus\_worker\_stat\_activity**: Zeigt Abfragen für Worker an, einschließlich Fragmentabfragen für einzelne Shards.
-   **citus\_lock\_waits**: Blockierte Abfragen im gesamten Cluster.

Die ersten beiden Sichten enthalten alle Spalten von [pg\_stat\_activity](https://www.postgresql.org/docs/current/static/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW) sowie den Host Host/Port des Workers, der die Abfrage initiiert hat, und den Host/Port des Koordinatorknotens des Clusters.

Zählen Sie beispielsweise die Zeilen in einer verteilten Tabelle:

```postgresql
-- run from worker on localhost:9701

SELECT count(*) FROM users_table;
```

Sie sehen, dass die Abfrage in `citus_dist_stat_activity` angezeigt wird:

```postgresql
SELECT * FROM citus_dist_stat_activity;

-[ RECORD 1 ]----------+----------------------------------
query_hostname         | localhost
query_hostport         | 9701
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23723
usesysid               | 10
usename                | citus
application\_name      | psql
client\_addr           | 
client\_hostname       | 
client\_port           | -1
backend\_start         | 2018-10-05 13:27:14.419905+03
xact\_start            | 2018-10-05 13:27:16.362887+03
query\_start           | 2018-10-05 13:27:20.682452+03
state\_change          | 2018-10-05 13:27:20.896546+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | SELECT count(*) FROM users_table;
backend\_type          | client backend
```

Diese Abfrage erfordert Informationen aus allen Shards. Einige der Informationen befinden sich in Shard `users_table_102038`, der in `localhost:9700` gespeichert wird. Eine Abfrage, die auf den Shard zugreift, wird in der `citus_worker_stat_activity`-Sicht angezeigt:

```postgresql
SELECT * FROM citus_worker_stat_activity;

-[ RECORD 1 ]----------+-----------------------------------------------------------------------------------------
query_hostname         | localhost
query_hostport         | 9700
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23781
usesysid               | 10
usename                | citus
application\_name      | citus
client\_addr           | ::1
client\_hostname       | 
client\_port           | 51773
backend\_start         | 2018-10-05 13:27:20.75839+03
xact\_start            | 2018-10-05 13:27:20.84112+03
query\_start           | 2018-10-05 13:27:20.867446+03
state\_change          | 2018-10-05 13:27:20.869889+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | COPY (SELECT count(*) AS count FROM users_table_102038 users_table WHERE true) TO STDOUT
backend\_type          | client backend
```

Im Feld `query` werden Daten angezeigt, die aus dem Shard kopiert werden, um gezählt zu werden.

> [!NOTE]
> Wenn eine Routerabfrage (z. B. ein einzelner Mandant in einer mehrinstanzenfähigen Anwendung) `SELECT
> * FROM table WHERE tenant_id = X`) ohne einen Transaktionsblock ausgeführt wird, sind die Spalten master\_query\_host\_name und master\_query\_host\_port in citus\_worker\_stat\_activity NULL.

Im Folgenden sind Beispiele für nützliche Abfragen aufgeführt, die Sie mit `citus_worker_stat_activity` erstellen können:

```postgresql
-- active queries' wait events on a certain node

SELECT query, wait_event_type, wait_event
  FROM citus_worker_stat_activity
 WHERE query_hostname = 'xxxx' and state='active';

-- active queries' top wait events

SELECT wait_event, wait_event_type, count(*)
  FROM citus_worker_stat_activity
 WHERE state='active'
 GROUP BY wait_event, wait_event_type
 ORDER BY count(*) desc;

-- total internal connections generated per node by Citus

SELECT query_hostname, count(*)
  FROM citus_worker_stat_activity
 GROUP BY query_hostname;

-- total internal active connections generated per node by Citus

SELECT query_hostname, count(*)
  FROM citus_worker_stat_activity
 WHERE state='active'
 GROUP BY query_hostname;
```

Die nächste Ansicht ist `citus_lock_waits`. Um zu sehen, wie diese Ansicht funktioniert, können Sie manuell eine Sperrsituation generieren. Hierzu richten Sie zunächst über den Koordinator eine Testtabelle ein:

```postgresql
CREATE TABLE numbers AS
  SELECT i, 0 AS j FROM generate_series(1,10) AS i;
SELECT create_distributed_table('numbers', 'i');
```

Anschließend können Sie mit zwei Sitzungen für den Koordinator diese Sequenz von Anweisungen ausführen:

```postgresql
-- session 1                           -- session 2
-------------------------------------  -------------------------------------
BEGIN;
UPDATE numbers SET j = 2 WHERE i = 1;
                                       BEGIN;
                                       UPDATE numbers SET j = 3 WHERE i = 1;
                                       -- (this blocks)
```

Die `citus_lock_waits`-Sicht zeigt die Situation.

```postgresql
SELECT * FROM citus_lock_waits;

-[ RECORD 1 ]-------------------------+----------------------------------------
waiting_pid                           | 88624
blocking_pid                          | 88615
blocked_statement                     | UPDATE numbers SET j = 3 WHERE i = 1;
current_statement_in_blocking_process | UPDATE numbers SET j = 2 WHERE i = 1;
waiting_node_id                       | 0
blocking_node_id                      | 0
waiting_node_name                     | coordinator_host
blocking_node_name                    | coordinator_host
waiting_node_port                     | 5432
blocking_node_port                    | 5432
```

In diesem Beispiel stammen die Abfragen vom Koordinator, aber die Sicht kann auch Sperren zwischen Abfragen auflisten, die von Workern stammen (z. B. mit Hyperscale (Citus) MX).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie einige [Funktionen von Hyperscale (Citus)](reference-hyperscale-functions.md) Systemtabellen ändern.
* Machen Sie sich mit den Konzepten von [Knoten und Tabellen](concepts-hyperscale-nodes.md) vertraut.
