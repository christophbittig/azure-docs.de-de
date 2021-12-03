---
title: Logische Replikation und logische Decodierung – Azure Database for PostgreSQL – Flexible Server
description: Weitere Informationen zur logischen Replikation und Decodierung in Azure Database for PostgreSQL – Flexible Server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 13326622dae32c0ebd8fa86035967d51ab734c2a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130241590"
---
# <a name="logical-replication-and-logical-decoding-in-azure-database-for-postgresql---flexible-server"></a>Logische Replikation und Decodierung in Azure Database for PostgreSQL – Flexible Server



Azure Database für PostgreSQL - Flexible Server unterstützt die folgenden Methoden der logischen Datenextraktion und -replikation:
1. **Logische Replikation**
   1. Verwendung Sie PostgreSQL [native logische Replikation](https://www.postgresql.org/docs/12/logical-replication.html), um Datenobjekte zu replizieren. Die logische Replikation ermöglicht eine detaillierte Kontrolle über die Datenreplikation, einschließlich der Datenreplikation auf Tabellenebene.
   2. Verwenden Sie die Erweiterung [pglogical](https://github.com/2ndQuadrant/pglogical), die Ihnen logische Streaming-Replikation sowie zusätzliche Funktionen wie das Kopieren des Anfangsschemas der Datenbank, Unterstützung für TRUNCATE, die Möglichkeit, die DDL zu replizieren etc. bietet. 
2. **Logische Dekodierung**, die durch [Dekodierung](https://www.postgresql.org/docs/12/logicaldecoding-explanation.html) des Inhalts des Write-Ahead-Logs (WAL) realisiert wird. 

## <a name="comparing-logical-replication-and-logical-decoding"></a>Vergleich der logischen Replikation mit der logischen Decodierung
Die logische Replikation und die logische Decodierung weisen Ähnlichkeiten auf. Beide:
* Ermöglichen es Ihnen, Daten aus Postgres zu replizieren.
* Verwenden das [Write-Ahead-Protokoll](https://www.postgresql.org/docs/current/wal.html) (Write-Ahead Log, WAL) als Änderungsquelle.
* Verwenden [logische Replikationsslots](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS), um Daten zu senden. Ein Slot stellt einen Datenstrom von Änderungen dar.
* Bestimmen mithilfe der [Eigenschaft „REPLICA IDENTITY“](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-CREATETABLE-REPLICA-IDENTITY) einer Tabelle, welche Änderungen gesendet werden können.
* Replizieren keine DDL-Änderungen.


Es gibt auch Unterschiede zwischen den beiden Technologien: Die logische Replikation: 
* Ermöglicht es Ihnen, eine Tabelle oder Tabellengruppe anzugeben, die repliziert werden soll.
* Repliziert Daten zwischen PostgreSQL-Instanzen.

Die logische Decodierung:
* Extrahiert Änderungen aus allen Tabellen in einer Datenbank.
* Kann keine Daten direkt zwischen PostgreSQL-Instanzen senden.

>[!NOTE]
> Derzeit unterstützt der flexible Server keine regionsübergreifenden Lesereplikate. Je nach Art der Workload können Sie das Feature „Logische Replikation“ für die regionsübergreifende Notfallwiederherstellung verwenden.

## <a name="pre-requisites-for-logical-replication-and-logical-decoding"></a>Voraussetzungen für die logische Replikation und die logische Decodierung

1. Wechseln Sie im Portal zur Seite „Serverparameter“.
2. Legen Sie den Serverparameter `wal_level` auf `logical` fest.
3. Wenn Sie die pglogical-Erweiterung verwenden möchten, suchen Sie nach dem Parameter `shared_preload_libaries`, und wählen Sie im Dropdownfeld `pglogical` aus.
4. Ändern Sie den Wert des Parameters `max_worker_processes` mindestens in 16. Andernfalls können Probleme wie `WARNING: out of background worker slots` auftreten.
5. Speichern Sie die Änderungen, und starten Sie den Server neu, um die Änderung von `wal_level` zu übernehmen.
6. Vergewissern Sie sich, dass Ihre PostgreSQL-Instanz Netzwerkdatenverkehr von Ihrer Verbindungsressource zulässt.
7. Erteilen Sie dem Administratorbenutzer Replikationsberechtigungen.
   ```SQL
   ALTER ROLE <adminname> WITH REPLICATION;
   ```
8. Sie sollten sich vergewissern, dass die von Ihnen verwendete Rolle über [Berechtigungen](https://www.postgresql.org/docs/current/sql-grant.html) für das Schema verfügt, das Sie replizieren. Andernfalls können Fehler wie `Permission denied for schema` auftreten. 

## <a name="using-logical-replication-and-logical-decoding"></a>Verwenden der logischen Replikation und der logischen Decodierung

### <a name="native-logical-replication"></a>Native logische Replikation
Bei der logischen Replikation wird üblicherweise vom „Verleger“ und vom „Abonnenten“ gesprochen. 
* Beim Verleger handelt es sich um die PostgreSQL-Datenbank, **von** der die Daten gesendet werden. 
* Beim Abonnenten handelt es sich um die PostgreSQL-Datenbank, **an** die die Daten gesendet werden.

Nachstehend finden Sie einen Beispielcode, mit dem Sie die logische Replikation ausprobieren können.

1. Stellen Sie eine Verbindung mit der Verlegerdatenbank her. Erstellen Sie eine Tabelle, und fügen Sie Daten hinzu.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   INSERT INTO basic(name) VALUES ('apple');
   INSERT INTO basic(name) VALUES ('banana');
   ```

2. Erstellen Sie eine Veröffentlichung für die Tabelle.
   ```SQL
   CREATE PUBLICATION pub FOR TABLE basic;
   ```

3. Stellen Sie eine Verbindung mit der Abonnentendatenbank her. Erstellen Sie eine Tabelle mit demselben Schema wie für den Verleger.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   ```

4. Erstellen Sie ein Abonnement, das eine Verbindung mit der zuvor erstellten Veröffentlichung herstellt.
   ```SQL
   CREATE SUBSCRIPTION sub CONNECTION 'host=<server>.postgres.database.azure.com user=<admin> dbname=<dbname> password=<password>' PUBLICATION pub;
   ```

5. Sie können die Tabelle nun auf dem Abonnenten abfragen. Sie werden sehen, dass dieser Daten vom Verleger empfangen hat.
   ```SQL
   SELECT * FROM basic;
   ```
   Sie können der Tabelle des Verlegers weitere Zeilen hinzufügen und die Änderungen auf dem Abonnenten anzeigen.

   Wenn Sie die Daten nicht sehen können, aktivieren Sie die Anmeldeberechtigung für `azure_pg_admin`, und überprüfen Sie den Tabelleninhalt. 
   ```SQL 
   ALTER ROLE azure_pg_admin login;
   ```


Weitere Informationen über die [logische Replikation](https://www.postgresql.org/docs/current/logical-replication.html) finden Sie in der PostgreSQL-Dokumentation.

### <a name="pglogical-extension"></a>pglogische Erweiterung

Im Folgenden finden Sie ein Beispiel für die Konfiguration von pglogical auf dem Provider-Datenbankserver und dem Teilnehmer. Weitere Details finden Sie in der [Dokumentation zur pglogical-Erweiterung](https://github.com/2ndQuadrant/pglogical#usage). Stellen Sie außerdem sicher, dass die oben aufgeführten Voraussetzungen erfüllt sind.


1. Installieren Sie die pglogical-Erweiterung sowohl auf den Anbieter- als auch auf den Abonnentendatenbankservern in der Datenbank.
    ```SQL
   \C myDB
   CREATE EXTENSION pglogical;
   ```
2. Wenn es sich bei dem Replikationsbenutzer nicht um den Serveradministratorbenutzer handelt (der den Server erstellt hat), stellen Sie sicher, dass Sie dem Benutzer die Berechtigungen `azure_pg_admin` und `replication` zuweisen. Alternativ dazu können Sie dem Replikationsbenutzer den Administratorbenutzer zuweisen. Weitere Informationen finden Sie in der [Dokumentation zu pglogical](https://github.com/2ndQuadrant/pglogical#limitations-and-restrictions).
   ```SQL
   GRANT azure_pg_admin, replication to myUser;
   ```
   oder
   ```SQL
   GRANT myAdminUser to myUser;
   ```
2. Erstellen Sie auf dem Datenbankservers des **Anbieters** (Quelle/Verleger) den Anbieterknoten.
   ```SQL
   select pglogical.create_node( node_name := 'provider1', 
   dsn := ' host=myProviderServer.postgres.database.azure.com port=5432 dbname=myDB user=myUser password=myPassword');
   ```
3. Erstellen Sie eine Replikatgruppe.
   ```SQL
   select pglogical.create_replication_set('myreplicationset');
   ```
4. Fügen Sie der Replikatgruppe alle Tabellen in der Datenbank hinzu.
   ```SQL
   SELECT pglogical.replication_set_add_all_tables('myreplicationset', '{public}'::text[]);
   ```

   Alternativ können Sie auch Tabellen aus einem bestimmten Schema (z. B. testUser) einer Standardreplikatgruppe hinzufügen.
   ```SQL
   SELECT pglogical.replication_set_add_all_tables('default', ARRAY['testUser']);
   ```

5. Erstellen Sie auf dem Datenbankserver des **Abonnenten** einen Abonnentenknoten.
   ```SQL
   select pglogical.create_node( node_name := 'subscriber1', 
   dsn := ' host=mySubscriberServer.postgres.database.azure.com port=5432 dbname=myDB user=myUser password=myPasword' );
   ```
6. Erstellen Sie ein Abonnement, um den Synchronisierungs- und Replikationsprozess zu starten.
    ```SQL
   select pglogical.create_subscription (
   subscription_name := 'subscription1',
   replication_sets := array['myreplicationset'],
   provider_dsn := 'host=myProviderServer.postgres.database.azure.com port=5432 dbname=myDB user=myUser password=myPassword');
   ```
7. Anschließend können Sie den Abonnementstatus überprüfen.
   ```SQL
   SELECT subscription_name, status FROM pglogical.show_subscription_status();
   ```

### <a name="logical-decoding"></a>Logische Decodierung
Die logische Decodierung kann über das Streamingprotokoll oder die SQL-Schnittstelle genutzt werden. 

#### <a name="streaming-protocol"></a>Streamingprotokoll
Häufig ist das Verarbeiten von Änderungen mithilfe des Streamingprotokolls vorzuziehen. Sie können einen eigenen Consumer/Connector erstellen oder einen Drittanbieterdienst wie [Debezium](https://debezium.io/) verwenden. 

In der wal2json-Dokumentation finden Sie [ein Beispiel für die Verwendung des Streamingprotokolls mit pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).

#### <a name="sql-interface"></a>SQL-Schnittstelle 
Im folgenden Beispiel wird die SQL-Schnittstelle mit dem wal2json-Plug-In verwendet.
 
1. Erstellen Sie einen Slot.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. Geben Sie SQL-Befehle aus. Beispiel:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Verarbeiten Sie die Änderungen.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   Die Ausgabe sieht wie folgt aus:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Löschen Sie den Slot, wenn Sie ihn nicht mehr benötigen.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```

Weitere Informationen über die [logische Decodierung](https://www.postgresql.org/docs/current/logicaldecoding.html) finden Sie in der PostgreSQL-Dokumentation.


## <a name="monitoring"></a>Überwachung
Sie müssen die logische Decodierung überwachen. Alle nicht verwendeten Replikationsslots müssen gelöscht werden. Slots halten Verbindungen zu den Postgres-WAL-Protokollen und den relevanten Systemkatalogen aufrecht, bis Änderungen gelesen wurden. Wenn Ihr Abonnent oder Consumer ausfällt oder nicht ordnungsgemäß konfiguriert wurde, werden die nicht verarbeiteten Protokolle aufbewahrt und füllen den Speicher. Außerdem erhöhen nicht verarbeitete Protokolle das Risiko von Transaktions-ID-Umläufen. Beide Situationen können dazu führen, dass der Server nicht mehr verfügbar ist. Daher ist es wichtig, dass die logischen Replikationsslots kontinuierlich verarbeitet werden. Wenn ein logischer Replikationsslot nicht mehr verwendet wird, löschen Sie ihn sofort.

In der Spalte „active“ (aktiv) in der Ansicht „pg_replication_slots“ wird angegeben, ob ein Consumer mit einem Slot verbunden ist.
```SQL
SELECT * FROM pg_replication_slots;
```
[Legen Sie Benachrichtigungen](howto-alert-on-metrics.md) für die Servermetriken **Maximum Used Transaction IDs** (Maximal verwendete Transaktions-IDs) and **Verwendeter Speicher** fest, damit Sie informiert werden, wenn die normalen Schwellenwerte überschritten werden. 

## <a name="limitations"></a>Einschränkungen
* Es gelten **logische Replikationseinschränkungen**, wie [hier](https://www.postgresql.org/docs/12/logical-replication-restrictions.html) dokumentiert.
* **Lesereplikate:** Lesereplikate von Azure Database for PostgreSQL werden mit flexible Server derzeit nicht unterstützt.
* **Slots und Hochverfügbarkeitsfailover:** Logische Replikationsslots auf dem primären Server sind auf dem Standbyserver in der sekundären Verfügbarkeitszone nicht verfügbar. Dies gilt für Sie, wenn Ihr Server die Option für die zonenredundante Hochverfügbarkeit verwendet. Im Fall eines Failovers auf den Standbyserver sind logische Replikationsslots im Standbymodus nicht verfügbar.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zu [Netzwerkoptionen](concepts-networking.md)
* Weitere Informationen zu in flexiblen Servern verfügbaren [Erweiterungen](concepts-extensions.md)
* Weitere Informationen zu [Hochverfügbarkeit](concepts-high-availability.md)
