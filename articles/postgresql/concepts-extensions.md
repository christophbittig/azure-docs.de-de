---
title: Erweiterungen – Azure Database for PostgreSQL (Einzelserver)
description: Erfahren Sie etwas über die verfügbaren Postgres-Erweiterungen in Azure Database for PostgreSQL (Einzelserver).
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 1dfdab5c90c8a3acd574ec042f2cc8ad033cc592
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433496"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>PostgreSQL-Erweiterungen in Azure Database for PostgreSQL – Einzelserver
PostgreSQL bietet die Möglichkeit, die Funktionalität Ihrer Datenbank mithilfe von Erweiterungen zu erweitern. Bei Erweiterungen werden mehrere zusammengehörige SQL-Objekte zu einem Paket gebündelt und mit nur einem Befehl in die Datenbank geladen oder daraus entfernt. Nach dem Laden in die Datenbank funktionieren Erweiterungen genauso wie integrierte Features.

## <a name="how-to-use-postgresql-extensions"></a>Wie werden PostgreSQL-Erweiterungen verwendet?
Bevor Sie PostgreSQL-Erweiterungen verwenden können, müssen diese in Ihrer Datenbank installiert werden. Um eine bestimmte Erweiterung zu installieren, führen Sie zum Laden der gepackten Objekte in Ihrer Datenbank den Befehl [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) über das psql-Tool aus.

Azure Database for PostgreSQL unterstützt einen Teil der wichtigsten Erweiterungen, die unten aufgeführt sind. Sie können diese Informationen auch abrufen, indem Sie `SELECT * FROM pg_available_extensions;` ausführen. Andere Erweiterungen, die hier nicht aufgeführt sind, werden nicht unterstützt. Sie können in Azure Database for PostgreSQL keine eigenen Erweiterungen erstellen.

## <a name="postgres-11-extensions"></a>Postgres 11-Erweiterungen

Die folgenden Erweiterungen sind für Azure Database for PostgreSQL-Server verfügbar, die über Postgres Version 11 verfügen. 

> [!div class="mx-tableFixed"]
> | **Erweiterung**| **Erweiterungsversion** | **Beschreibung** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Wird verwendet, um eine Adresse in dessen zugehörige Elemente zu analysieren. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Adressenstandardisierung: US-Datasetbeispiel|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | Unterstützung für die Indizierung häufiger Datentypen in GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | Unterstützung für die Indizierung häufiger Datentypen in GIST|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | Datentyp für Zeichenfolgen ohne Berücksichtigung der Groß-/Kleinschreibung|
> |[cube](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | Datentyp für mehrdimensionale Cubes|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | Verbindungsherstellung mit anderen PostgreSQL-Datenbanken aus einer Datenbank|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | Textsuche-Wörterbuchvorlage für Integerwerte|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | Berechnung von Großkreisentfernungen auf der Erdoberfläche|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | Ermittlung von Ähnlichkeiten und der Entfernung zwischen Zeichenfolgen|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | Datentyp zum Speichern von Schlüssel-Wert-Paaren|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Hypothetische Indizes für PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | Funktionen, Operatoren und Indexunterstützung für 1-D-Arrays mit Integerwerten|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | Datentypen für internationale Produktnummerierungsnormen|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | Datentyp für hierarchische baumähnliche Strukturen|
> |[orafce](https://github.com/orafce/orafce)                       | 3,7             | Funktionen und Operatoren zum Emulieren einer Teilmenge mit Funktionen und Paketen aus einer kommerziellen RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | Überwachungsfunktionen|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | Kryptografische Funktionen|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting-Erweiterung|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | Anzeige von Sperrinformationen auf Zeilenebene|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | Anzeige von Statistiken auf Tupelebene|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | Untersuchung des freigegebenen Puffercaches|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Erweiterung für die Verwaltung partitionierter Tabellen nach Zeit oder ID|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | „Vorwärmung“ von Beziehungsdaten|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | Nachverfolgung von Ausführungsstatistiken aller ausgeführten SQL-Anweisungen|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | Messung von Textähnlichkeit und Indexsuche basierend auf Trigrammen|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL: Prozedurale Sprache|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (v8): Vertrauenswürdige prozedurale Sprache|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS: Geometrie, Geografie und räumliche Rastertypen und -funktionen|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL-Funktionen|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS Tiger: Geocoder und Reverse-Geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS-Topologie: Räumliche Typen und Funktionen|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | Fremddaten-Wrapper für PostgreSQL-Remoteserver|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | Funktionen zum Bearbeiten gesamter Tabellen, einschließlich Kreuztabelle|
> |[timescaledb](https://docs.timescale.com/latest)                    |1.7.4             | Ermöglichung von skalierbaren Einfügungen und komplexen Abfragen für Zeitreihendaten|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | Wörterbuch für Textsuche mit Entfernung von Akzenten|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | Generierung von UUIDs (Universally Unique Identifiers)|

## <a name="postgres-10-extensions"></a>Postgres 10-Erweiterungen 

Die folgenden Erweiterungen sind für Azure Database for PostgreSQL-Server verfügbar, die über Postgres Version 10 verfügen.

> [!div class="mx-tableFixed"]
> | **Erweiterung**| **Erweiterungsversion** | **Beschreibung** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Wird verwendet, um eine Adresse in dessen zugehörige Elemente zu analysieren. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Adressenstandardisierung: US-Datasetbeispiel|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | Unterstützung für die Indizierung häufiger Datentypen in GIN|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | Unterstützung für die Indizierung häufiger Datentypen in GIST|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | Datentyp für automatisch verschlüsselte Kennwörter|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | Datentyp für Zeichenfolgen ohne Berücksichtigung der Groß-/Kleinschreibung|
> |[cube](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | Datentyp für mehrdimensionale Cubes|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | Verbindungsherstellung mit anderen PostgreSQL-Datenbanken aus einer Datenbank|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | Textsuche-Wörterbuchvorlage für Integerwerte|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | Berechnung von Großkreisentfernungen auf der Erdoberfläche|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | Ermittlung von Ähnlichkeiten und der Entfernung zwischen Zeichenfolgen|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | Datentyp zum Speichern von Schlüssel-Wert-Paaren|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypothetische Indizes für PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | Funktionen, Operatoren und Indexunterstützung für 1-D-Arrays mit Integerwerten|
> |[isn](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | Datentypen für internationale Produktnummerierungsnormen|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | Datentyp für hierarchische baumähnliche Strukturen|
> |[orafce](https://github.com/orafce/orafce)                       | 3,7             | Funktionen und Operatoren zum Emulieren einer Teilmenge mit Funktionen und Paketen aus einer kommerziellen RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | Überwachungsfunktionen|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | Kryptografische Funktionen|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | pgRouting-Erweiterung|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | Anzeige von Sperrinformationen auf Zeilenebene|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | Anzeige von Statistiken auf Tupelebene|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | Untersuchung des freigegebenen Puffercaches|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Erweiterung für die Verwaltung partitionierter Tabellen nach Zeit oder ID|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | „Vorwärmung“ von Beziehungsdaten|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | Nachverfolgung von Ausführungsstatistiken aller ausgeführten SQL-Anweisungen|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | Messung von Textähnlichkeit und Indexsuche basierend auf Trigrammen|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | PL/pgSQL: Prozedurale Sprache|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8): Vertrauenswürdige prozedurale Sprache|
> |[postgis](https://www.postgis.net/)                      | 2.4.3           | PostGIS: Geometrie, Geografie und räumliche Rastertypen und -funktionen|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | PostGIS SFCGAL-Funktionen|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | PostGIS Tiger: Geocoder und Reverse-Geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | PostGIS-Topologie: Räumliche Typen und Funktionen|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | Fremddaten-Wrapper für PostgreSQL-Remoteserver|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | Funktionen zum Bearbeiten gesamter Tabellen, einschließlich Kreuztabelle|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.7.4             | Ermöglichung von skalierbaren Einfügungen und komplexen Abfragen für Zeitreihendaten|
> |[unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | Wörterbuch für Textsuche mit Entfernung von Akzenten|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | Generierung von UUIDs (Universally Unique Identifiers)|

## <a name="postgres-96-extensions"></a>Postgres 9.6-Erweiterungen 

Die folgenden Erweiterungen sind für Azure Database for PostgreSQL-Server verfügbar, die über Postgres Version 9.6 verfügen.

> [!div class="mx-tableFixed"]
> | **Erweiterung**| **Erweiterungsversion** | **Beschreibung** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Wird verwendet, um eine Adresse in dessen zugehörige Elemente zu analysieren. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Adressenstandardisierung: US-Datasetbeispiel|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | Unterstützung für die Indizierung häufiger Datentypen in GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | Unterstützung für die Indizierung häufiger Datentypen in GIST|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | Datentyp für automatisch verschlüsselte Kennwörter|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | Datentyp für Zeichenfolgen ohne Berücksichtigung der Groß-/Kleinschreibung|
> |[cube](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | Datentyp für mehrdimensionale Cubes|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | Verbindungsherstellung mit anderen PostgreSQL-Datenbanken aus einer Datenbank|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | Textsuche-Wörterbuchvorlage für Integerwerte|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | Berechnung von Großkreisentfernungen auf der Erdoberfläche|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | Ermittlung von Ähnlichkeiten und der Entfernung zwischen Zeichenfolgen|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | Datentyp zum Speichern von Schlüssel-Wert-Paaren|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypothetische Indizes für PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | Funktionen, Operatoren und Indexunterstützung für 1-D-Arrays mit Integerwerten|
> |[isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | Datentypen für internationale Produktnummerierungsnormen|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | Datentyp für hierarchische baumähnliche Strukturen|
> |[orafce](https://github.com/orafce/orafce)                       | 3,7             | Funktionen und Operatoren zum Emulieren einer Teilmenge mit Funktionen und Paketen aus einer kommerziellen RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | Überwachungsfunktionen|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | Kryptografische Funktionen|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | pgRouting-Erweiterung|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | Anzeige von Sperrinformationen auf Zeilenebene|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | Anzeige von Statistiken auf Tupelebene|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | Untersuchung des freigegebenen Puffercaches|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Erweiterung für die Verwaltung partitionierter Tabellen nach Zeit oder ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | „Vorwärmung“ von Beziehungsdaten|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | Nachverfolgung von Ausführungsstatistiken aller ausgeführten SQL-Anweisungen|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | Messung von Textähnlichkeit und Indexsuche basierend auf Trigrammen|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | PL/pgSQL: Prozedurale Sprache|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8): Vertrauenswürdige prozedurale Sprache|
> |[postgis](https://www.postgis.net/)                      | 2.3.2           | PostGIS: Geometrie, Geografie und räumliche Rastertypen und -funktionen|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS SFCGAL-Funktionen|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS Tiger: Geocoder und Reverse-Geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | PostGIS-Topologie: Räumliche Typen und Funktionen|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | Fremddaten-Wrapper für PostgreSQL-Remoteserver|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | Funktionen zum Bearbeiten gesamter Tabellen, einschließlich Kreuztabelle|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.7.4             | Ermöglichung von skalierbaren Einfügungen und komplexen Abfragen für Zeitreihendaten|
> |[unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | Wörterbuch für Textsuche mit Entfernung von Akzenten|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | Generierung von UUIDs (Universally Unique Identifiers)|

## <a name="postgres-95-extensions"></a>Postgres 9.5-Erweiterungen

>[!NOTE]
> PostgreSQL-Version 9.5 wurde außer Betrieb genommen.

Die folgenden Erweiterungen sind für Azure Database for PostgreSQL-Server verfügbar, die über Postgres Version 9.5 verfügen.

> [!div class="mx-tableFixed"]
> | **Erweiterung**| **Erweiterungsversion** | **Beschreibung** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Wird verwendet, um eine Adresse in dessen zugehörige Elemente zu analysieren. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Adressenstandardisierung: US-Datasetbeispiel|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | Unterstützung für die Indizierung häufiger Datentypen in GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | Unterstützung für die Indizierung häufiger Datentypen in GIST|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | Datentyp für automatisch verschlüsselte Kennwörter|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | Datentyp für Zeichenfolgen ohne Berücksichtigung der Groß-/Kleinschreibung|
> |[cube](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | Datentyp für mehrdimensionale Cubes|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | Verbindungsherstellung mit anderen PostgreSQL-Datenbanken aus einer Datenbank|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | Textsuche-Wörterbuchvorlage für Integerwerte|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | Berechnung von Großkreisentfernungen auf der Erdoberfläche|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | Ermittlung von Ähnlichkeiten und der Entfernung zwischen Zeichenfolgen|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | Datentyp zum Speichern von Schlüssel-Wert-Paaren|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypothetische Indizes für PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | Funktionen, Operatoren und Indexunterstützung für 1-D-Arrays mit Integerwerten|
> |[isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | Datentypen für internationale Produktnummerierungsnormen|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | Datentyp für hierarchische baumähnliche Strukturen|
> |[orafce](https://github.com/orafce/orafce)                       | 3,7             | Funktionen und Operatoren zum Emulieren einer Teilmenge mit Funktionen und Paketen aus einer kommerziellen RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | Überwachungsfunktionen|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | Kryptografische Funktionen|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | pgRouting-Erweiterung|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | Anzeige von Sperrinformationen auf Zeilenebene|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | Anzeige von Statistiken auf Tupelebene|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | Untersuchung des freigegebenen Puffercaches|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Erweiterung für die Verwaltung partitionierter Tabellen nach Zeit oder ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | „Vorwärmung“ von Beziehungsdaten|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | Nachverfolgung von Ausführungsstatistiken aller ausgeführten SQL-Anweisungen|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | Messung von Textähnlichkeit und Indexsuche basierend auf Trigrammen|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | PL/pgSQL: Prozedurale Sprache|
> |[postgis](https://www.postgis.net/)                      | 2.3.0           | PostGIS: Geometrie, Geografie und räumliche Rastertypen und -funktionen|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS SFCGAL-Funktionen|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS Tiger: Geocoder und Reverse-Geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | PostGIS-Topologie: Räumliche Typen und Funktionen|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | Fremddaten-Wrapper für PostgreSQL-Remoteserver|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | Funktionen zum Bearbeiten gesamter Tabellen, einschließlich Kreuztabelle|
> |[unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | Wörterbuch für Textsuche mit Entfernung von Akzenten|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | Generierung von UUIDs (Universally Unique Identifiers)|


## <a name="pg_stat_statements"></a>pg_stat_statements
Die [Erweiterung „pg_stat_statements“](https://www.postgresql.org/docs/current/pgstatstatements.html) wird auf jedem Azure Database for PostgreSQL-Server vorab geladen, um für Sie eine Möglichkeit zur Nachverfolgung von Ausführungsstatistiken von SQL-Anweisungen zu schaffen.
Die Einstellung `pg_stat_statements.track`, die steuert, welche Anweisungen von der Erweiterung gezählt werden, ist standardmäßig auf `top` festgelegt, was bedeutet, dass alle Anweisungen, die direkt von Clients ausgestellt werden, nachverfolgt werden. Die beiden anderen Nachverfolgungsebenen sind `none` und `all`. Diese Einstellung kann als Serverparameter über das [Azure-Portal](./howto-configure-server-parameters-using-portal.md) oder die [Azure CLI](./howto-configure-server-parameters-using-cli.md) konfiguriert werden.

Zwischen den von pg_stat_statements bereitgestellten Abfrageausführungsinformationen und der Auswirkung auf die Serverleistung besteht ein Kompromiss, da jede SQL-Anweisung protokolliert wird. Wenn Sie die pg_stat_statements-Erweiterung nicht aktiv verwenden, empfiehlt es sich, `pg_stat_statements.track` auf `none` festzulegen. Beachten Sie, dass sich einige Überwachungsdienste von Drittanbietern auf pg_stat_statements beziehen können, um Statistiken zur Abfrageleistung zu liefern. Bestätigen Sie also, ob dieser Fall auf Sie zutrifft oder nicht.

## <a name="dblink-and-postgres_fdw"></a>„dblink“ und „postgres_fdw“
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) und [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) ermöglichen Ihnen das Herstellen einer Verbindung von einem PostgreSQL-Server mit einem anderen oder mit einer anderen Datenbank auf demselben Server. Der empfangende Server muss Verbindungen vom sendenden Server über die Firewall zulassen. Bei Verwendung dieser Erweiterungen für die Verbindung zwischen Azure Database for PostgreSQL-Server kann dazu die Einstellung „Zugriff auf Azure-Dienste erlauben“ auf EIN gesetzt werden. Dies ist auch erforderlich, wenn Sie die Erweiterungen für ein Loopback zum selben Server verwenden möchten. Die Einstellung „Zugriff auf Azure-Dienste erlauben“ finden Sie auf der Azure-Portalseite für den Postgres-Server unter „Verbindungssicherheit“. Wenn Sie „Zugriff auf Azure-Dienste erlauben“ auf „EIN“ festlegen, werden alle Azure-IP-Adressen in die Zulassungsliste eingefügt.

Derzeit werden ausgehende Verbindungen von Azure Database for PostgreSQL nicht unterstützt – mit Ausnahme von Verbindungen mit anderen Azure Database for PostgreSQL-Servern in derselben Region.

## <a name="uuid"></a>uuid
Wenn Sie beabsichtigen, `uuid_generate_v4()` aus der [uuid-ossp-Erweiterung](https://www.postgresql.org/docs/current/uuid-ossp.html) zu verwenden, lassen sich durch einen Vergleich mit `gen_random_uuid()` aus der [pgcrypto-Erweiterung](https://www.postgresql.org/docs/current/pgcrypto.html) u. U. Leistungsvorteile erzielen.

## <a name="pgaudit"></a>pgAudit
Die [pgAudit-Erweiterung](https://github.com/pgaudit/pgaudit/blob/master/README.md) bietet Sitzungs- und Objektüberwachungsprotokollierung. Informationen zur Verwendung dieser Erweiterung in Azure Database for PostgreSQL finden Sie im [Artikel zu Überwachungskonzepten](concepts-audit.md). 

## <a name="pg_prewarm"></a>pg_prewarm
Die pg_prewarm-Erweiterung lädt relationale Daten in den Cache. Durch das Vorwärmen der Caches weisen Ihre Abfragen bei der ersten Ausführung nach einem Neustart bessere Reaktionszeiten auf. In Postgres 10 und älteren Versionen erfolgt das Vorwärmen manuell mithilfe der [prewarm-Funktion](https://www.postgresql.org/docs/10/pgprewarm.html).

In Postgres 11 und höheren Versionen können Sie konfigurieren, dass die Vorwärmung [automatisch](https://www.postgresql.org/docs/current/pgprewarm.html) erfolgt. Sie müssen pg_prewarm in die Liste des `shared_preload_libraries`-Parameters einschließen und den Server neu starten, um die Änderung zu übernehmen. Parameter können im [Azure-Portal](howto-configure-server-parameters-using-portal.md), mithilfe der [Befehlszeilenschnittstelle](howto-configure-server-parameters-using-cli.md), der REST-API oder der ARM-Vorlage festgelegt werden. 

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB ist eine Zeitreihendatenbank, die als eine Erweiterung für PostgreSQL verpackt wird. TimescaleDB bietet zeitlich orientierte Analysefunktionen, Optimierungen, und es skaliert Postgres für Zeitreihenworkloads.

[Weitere Informationen zu TimescaleDB](https://docs.timescale.com/latest), einer eingetragenen Marke von [Timescale, Inc.](https://www.timescale.com/) Azure Database for PostgreSQL stellt die [Apache 2-Edition](https://www.timescale.com/legal/licenses) von TimescaleDB bereit.

### <a name="installing-timescaledb"></a>Installieren von TimescaleDB
Um TimescaleDB installieren zu können, müssen Sie es in die freigegebenen im Voraus geladenen Bibliotheken des Servers einbeziehen. Damit eine Änderung des `shared_preload_libraries`-Parameters von PostgreSQL wirksam wird, ist ein **Serverneustart** erforderlich. Sie können Parameter mithilfe des [Microsoft Azure-Portals](howto-configure-server-parameters-using-portal.md) oder der [Azure-Befehlszeilenschnittstelle](howto-configure-server-parameters-using-cli.md) ändern.

Verwenden des [Azure-Portals](https://portal.azure.com/):

1. Wählen Sie Ihre Azure Database for PostgreSQL-Server aus.

2. Wählen Sie auf der Seitenleiste **Serverparameter** aus.

3. Suchen Sie nach dem Parameter `shared_preload_libraries`.

4. Wählen Sie **TimescaleDB** aus.

5. Wählen Sie **Speichern** aus, um Ihre Änderungen beizubehalten. Sobald die Änderung gespeichert wurde, erhalten Sie eine entsprechende Benachrichtigung. 

6. Nach Empfang der Benachrichtigung müssen Sie den Server **neu starten**, damit diese Änderungen übernommen werden. Die Vorgehensweise zum Neustart eines Servers finden Sie unter [Neustart eines Azure Database for PostgreSQL-Servers über das Azure-Portal](howto-restart-server-portal.md).


Sie können jetzt TimescaleDB in Ihrer Postgres-Datenbank aktivieren. Stellen Sie eine Verbindung mit der Datenbank her, und geben Sie den folgenden Befehl ein:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Wenn ein Fehler angezeigt wird, bestätigen Sie, dass Sie nach dem Speichern von „shared_preload_libraries“ [Ihren Server neu gestartet haben](howto-restart-server-portal.md). 

Sie können jetzt eine TimescaleDB-Hypertable [ganz neu erstellen](https://docs.timescale.com/getting-started/creating-hypertables) oder [vorhandene Zeitreihendaten in PostgreSQL migrieren](https://docs.timescale.com/getting-started/migrating-data).

### <a name="restoring-a-timescale-database"></a>Wiederherstellen einer Zeitskaladatenbank
Zum Wiederherstellen einer Zeitskaladatenbank mithilfe von pg_dump und pg_restore müssen Sie zwei Hilfsprozeduren in der Zieldatenbank ausführen: `timescaledb_pre_restore()` und `timescaledb_post restore()`.

Bereiten Sie zuerst die Zieldatenbank vor:

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

Nun können Sie pg_dump für die ursprüngliche Datenbank ausführen und dann pg_restore anwenden. Stellen Sie nach der Wiederherstellung sicher, dass Sie den folgenden Befehl in der wiederhergestellten Datenbank ausführen:

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>Nächste Schritte
Wenn Sie eine Erweiterung fehlt, die Sie verwenden möchten, lassen Sie es uns wissen. Stimmen Sie in unserem [Feedbackforum](https://feedback.azure.com/d365community/forum/c5e32b97-ee24-ec11-b6e6-000d3a4f0da0) für vorhandene Anfragen ab, oder geben Sie uns weiteres Feedback.
