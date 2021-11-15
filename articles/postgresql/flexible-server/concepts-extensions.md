---
title: 'Erweiterungen: Azure Database for PostgreSQL Flexible Server'
description: In diesem Artikel erhalten Sie Informationen zu den verfügbaren Postgres-Erweiterungen in Azure Database for PostgreSQL Flexible Server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: bc176567721b3c023afcb82e920b33fca7ca0a53
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061228"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>PostgreSQL-Erweiterungen in Azure Database for PostgreSQL Flexible Server



PostgreSQL bietet die Möglichkeit, die Funktionalität Ihrer Datenbank mithilfe von Erweiterungen zu erweitern. Bei Erweiterungen werden mehrere zusammengehörige SQL-Objekte zu einem Paket gebündelt und mit nur einem Befehl in die Datenbank geladen oder daraus entfernt. Nach dem Laden in die Datenbank funktionieren Erweiterungen genauso wie integrierte Features.

## <a name="how-to-use-postgresql-extensions"></a>Wie werden PostgreSQL-Erweiterungen verwendet?
Bevor Sie PostgreSQL-Erweiterungen verwenden können, müssen diese in Ihrer Datenbank installiert werden. Zur Installation einer bestimmten Erweiterung führen Sie den Befehl [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) aus. Dieser Befehl lädt die Objektpakete in Ihre Datenbank.

Azure Database for PostgreSQL unterstützt einen Teil der wichtigsten Erweiterungen, die unten aufgeführt sind. Sie können diese Informationen auch abrufen, indem Sie `SHOW azure.extensions;` ausführen. Erweiterungen, die in diesem Artikel nicht aufgeführt sind, werden für Azure Database for PostgreSQL Flexible Server nicht unterstützt. Sie können in Azure Database for PostgreSQL keine eigenen Erweiterungen erstellen oder laden.

## <a name="postgres-13-extensions"></a>Postgres 13-Erweiterungen

Die folgenden Erweiterungen sind für Azure Database for PostgreSQL Flexible Server mit Postgres-Version 13 verfügbar. 

> [!div class="mx-tableFixed"]
> | **Erweiterung**| **Erweiterungsversion** | **Beschreibung** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 3.1.1           | Wird verwendet, um eine Adresse in dessen zugehörige Elemente zu analysieren. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 3.1.1           | Adressenstandardisierung: US-Datasetbeispiel|
> |[amcheck](https://www.postgresql.org/docs/13/amcheck.html)                    | 1.2             | Funktionen zum Überprüfen der Beziehungsintegrität|
> |[bloom](https://www.postgresql.org/docs/13/bloom.html)                    | 1.0             | Bloom-Zugriffsmethode: Index, der auf einer Signaturdatei basiert|
> |[btree_gin](https://www.postgresql.org/docs/13/btree-gin.html)                    | 1.3             | Unterstützung für die Indizierung häufiger Datentypen in GIN|
> |[btree_gist](https://www.postgresql.org/docs/13/btree-gist.html)                   | 1.5             | Unterstützung für die Indizierung häufiger Datentypen in GIST|
> |[citext](https://www.postgresql.org/docs/13/citext.html)                       | 1.6             | Datentyp für Zeichenfolgen ohne Berücksichtigung der Groß-/Kleinschreibung|
> |[cube](https://www.postgresql.org/docs/13/cube.html)                         | 1.4             | Datentyp für mehrdimensionale Cubes|
> |[dblink](https://www.postgresql.org/docs/13/dblink.html)                       | 1.2             | Verbindungsherstellung mit anderen PostgreSQL-Datenbanken aus einer Datenbank|
> |[dict_int](https://www.postgresql.org/docs/13/dict-int.html)                     | 1.0             | Textsuche-Wörterbuchvorlage für Integerwerte|
> |[dict_xsyn](https://www.postgresql.org/docs/13/dict-xsyn.html)                     | 1.0             | Wörterbuchvorlage für die Textsuche für die erweiterte Verarbeitung von Synonymen|
> |[earthdistance](https://www.postgresql.org/docs/13/earthdistance.html)                | 1.1             | Berechnung von Großkreisentfernungen auf der Erdoberfläche|
> |[fuzzystrmatch](https://www.postgresql.org/docs/13/fuzzystrmatch.html)                | 1.1             | Ermittlung von Ähnlichkeiten und der Entfernung zwischen Zeichenfolgen|
> |[hstore](https://www.postgresql.org/docs/13/hstore.html)                       | 1.7             | Datentyp zum Speichern von Schlüssel-Wert-Paaren|
> |[intagg](https://www.postgresql.org/docs/13/intagg.html)                     | 1.1             | Integeraggregator und -enumerator. (Veraltet)|
> |[intarray](https://www.postgresql.org/docs/13/intarray.html)                     | 1.3             | Funktionen, Operatoren und Indexunterstützung für 1-D-Arrays mit Integerwerten|
> |[isn](https://www.postgresql.org/docs/13/isn.html)                          | 1.2             | Datentypen für internationale Produktnummerierungsnormen|
> |[lo](https://www.postgresql.org/docs/13/lo.html)                            | 1.1             | Wartung großer Objekte |
> |[ltree](https://www.postgresql.org/docs/13/ltree.html)                        | 1.2             | Datentyp für hierarchische baumähnliche Strukturen|
> |[pageinspect](https://www.postgresql.org/docs/13/pageinspect.html)                        | 1.8             | Überprüft den Inhalt von Datenbankseiten im Detail|
> |[pg_buffercache](https://www.postgresql.org/docs/13/pgbuffercache.html)               | 1.3             | Untersuchung des freigegebenen Puffercaches|
> |[pg_cron](https://github.com/citusdata/pg_cron)                        | 1.3             | Auftragsplaner für PostgreSQL|
> |[pg_freespacemap](https://www.postgresql.org/docs/13/pgfreespacemap.html)               | 1.2             | Untersucht Free Space Map (FSM)|
> |[pg_partman](https://github.com/pgpartman/pg_partman)         | 4.5.0           | Erweiterung für die Verwaltung partitionierter Tabellen nach Zeit oder ID |
> |[pg_prewarm](https://www.postgresql.org/docs/13/pgprewarm.html)                   | 1.2             | „Vorwärmung“ von Beziehungsdaten|
> |[pg_stat_statements](https://www.postgresql.org/docs/13/pgstatstatements.html)           | 1.8             | Nachverfolgung von Ausführungsstatistiken aller ausgeführten SQL-Anweisungen|
> |[pg_trgm](https://www.postgresql.org/docs/13/pgtrgm.html)                      | 1.5             | Messung von Textähnlichkeit und Indexsuche basierend auf Trigrammen|
> |[pg_visibility](https://www.postgresql.org/docs/13/pgvisibility.html)                      | 1.2             | Überprüft die Informationen zur Zuordnung von Sichtbarkeiten (VM) und Sichtbarkeit auf Seitenebene|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.5             | Überwachungsfunktionen|
> |[pgcrypto](https://www.postgresql.org/docs/13/pgcrypto.html)                     | 1.3             | Kryptografische Funktionen| 
> |[pglogisch](https://github.com/2ndQuadrant/pglogical)       | 2.3.2                | Logische Streamingreplikation |
> |[pgrowlocks](https://www.postgresql.org/docs/13/pgrowlocks.html)                   | 1.2             | Anzeige von Sperrinformationen auf Zeilenebene|
> |[pgstattuple](https://www.postgresql.org/docs/13/pgstattuple.html)                  | 1.5             | Anzeige von Statistiken auf Tupelebene|
> |[plpgsql](https://www.postgresql.org/docs/13/plpgsql.html)                      | 1.0             | PL/pgSQL: Prozedurale Sprache|
> |[postgis](https://www.postgis.net/)                      | 3.1.1           | PostGIS-Geometrie, -Geografie |
> |[postgis_raster](https://www.postgis.net/)               | 3.1.1           | PostGIS-Rastertypen und -Funktionen| 
> |[postgis_sfcgal](https://www.postgis.net/)               | 3.1.1           | PostGIS SFCGAL-Funktionen|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 3.1.1           | PostGIS Tiger: Geocoder und Reverse-Geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 3.1.1           | PostGIS-Topologie: Räumliche Typen und Funktionen|
> |[postgres_fdw](https://www.postgresql.org/docs/13/postgres-fdw.html)                 | 1.0             | Fremddaten-Wrapper für PostgreSQL-Remoteserver|
> |[sslinfo](https://www.postgresql.org/docs/13/sslinfo.html)                    | 1.2             | Informationen zu SSL-Zertifikaten|
> |[tsm_system_rows](https://www.postgresql.org/docs/13/tsm-system-rows.html)                    | 1.0             |  TABLESAMPLE-Methode, die die Anzahl von Zeilen als Grenzwert akzeptiert|
> |[tsm_system_time](https://www.postgresql.org/docs/13/tsm-system-time.html)                    | 1.0             |  TABLESAMPLE-Methode, die die Zeit in Millisekunden als Grenzwert akzeptiert|
> |[unaccent](https://www.postgresql.org/docs/13/unaccent.html)                     | 1.1             | Wörterbuch für Textsuche mit Entfernung von Akzenten|
> |[uuid-ossp](https://www.postgresql.org/docs/13/uuid-ossp.html)                    | 1.1             | Generierung von UUIDs (Universally Unique Identifiers)|

## <a name="postgres-12-extensions"></a>Postgres 12-Erweiterungen

Die folgenden Erweiterungen sind für Azure Database for PostgreSQL Flexible Server mit Postgres-Version 12 verfügbar. 

> [!div class="mx-tableFixed"]
> | **Erweiterung**| **Erweiterungsversion** | **Beschreibung** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 3.0.0           | Wird verwendet, um eine Adresse in dessen zugehörige Elemente zu analysieren. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 3.0.0           | Adressenstandardisierung: US-Datasetbeispiel|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1.2             | Funktionen zum Überprüfen der Beziehungsintegrität|
> |[bloom](https://www.postgresql.org/docs/12/bloom.html)                    | 1.0             | Bloom-Zugriffsmethode: Index, der auf einer Signaturdatei basiert|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1.3             | Unterstützung für die Indizierung häufiger Datentypen in GIN|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | Unterstützung für die Indizierung häufiger Datentypen in GIST|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | Datentyp für Zeichenfolgen ohne Berücksichtigung der Groß-/Kleinschreibung|
> |[cube](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | Datentyp für mehrdimensionale Cubes|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1.2             | Verbindungsherstellung mit anderen PostgreSQL-Datenbanken aus einer Datenbank|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1.0             | Textsuche-Wörterbuchvorlage für Integerwerte|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1.0             | Wörterbuchvorlage für die Textsuche für die erweiterte Verarbeitung von Synonymen|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1.1             | Berechnung von Großkreisentfernungen auf der Erdoberfläche|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1.1             | Ermittlung von Ähnlichkeiten und der Entfernung zwischen Zeichenfolgen|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | Datentyp zum Speichern von Schlüssel-Wert-Paaren|
> |[hypopg](https://github.com/HypoPG/hypopg)                                   |  1.2             | Erweiterung, die Unterstützung für hypothetische Indizes hinzufügt |
> |[intagg](https://www.postgresql.org/docs/12/intagg.html)                     | 1.1             | Integeraggregator und -enumerator. (Veraltet)|
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1.2             | Funktionen, Operatoren und Indexunterstützung für 1-D-Arrays mit Integerwerten|
> |[isn](https://www.postgresql.org/docs/12/isn.html)                          | 1.2             | Datentypen für internationale Produktnummerierungsnormen|
> |[lo](https://www.postgresql.org/docs/12/lo.html)                            | 1.1             | Wartung großer Objekte |
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1.1             | Datentyp für hierarchische baumähnliche Strukturen|
> |[pageinspect](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1.7             | Überprüft den Inhalt von Datenbankseiten im Detail|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1.3             | Untersuchung des freigegebenen Puffercaches|
> |[pg_cron](https://github.com/citusdata/pg_cron)                        | 1.3             | Auftragsplaner für PostgreSQL|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1.2             | Untersucht Free Space Map (FSM)|
> |[pg_partman](https://github.com/pgpartman/pg_partman)         | 4.5.0           | Erweiterung für die Verwaltung partitionierter Tabellen nach Zeit oder ID |
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1.2             | „Vorwärmung“ von Beziehungsdaten|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1.7             | Nachverfolgung von Ausführungsstatistiken aller ausgeführten SQL-Anweisungen|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | Messung von Textähnlichkeit und Indexsuche basierend auf Trigrammen|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1.2             | Überprüft die Informationen zur Zuordnung von Sichtbarkeiten (VM) und Sichtbarkeit auf Seitenebene|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | Überwachungsfunktionen|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | Kryptografische Funktionen|
>|[pglogisch](https://github.com/2ndQuadrant/pglogical)       | 2.3.2                | Logische Streamingreplikation |
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1.2             | Anzeige von Sperrinformationen auf Zeilenebene|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | Anzeige von Statistiken auf Tupelebene|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1.0             | PL/pgSQL: Prozedurale Sprache|
> |[postgis](https://www.postgis.net/)                      | 3.0.0           | PostGIS-Geometrie, -Geografie |
> |[postgis_raster](https://www.postgis.net/)               | 3.0.0           | PostGIS-Rastertypen und -Funktionen| 
> |[postgis_sfcgal](https://www.postgis.net/)               | 3.0.0           | PostGIS SFCGAL-Funktionen|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 3.0.0           | PostGIS Tiger: Geocoder und Reverse-Geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 3.0.0           | PostGIS-Topologie: Räumliche Typen und Funktionen|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1.0             | Fremddaten-Wrapper für PostgreSQL-Remoteserver|
> |[sslinfo](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1.2             | Informationen zu SSL-Zertifikaten|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1.0             |  TABLESAMPLE-Methode, die die Anzahl von Zeilen als Grenzwert akzeptiert|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1.0             |  TABLESAMPLE-Methode, die die Zeit in Millisekunden als Grenzwert akzeptiert|
> |[unaccent](https://www.postgresql.org/docs/12/unaccent.html)                     | 1.1             | Wörterbuch für Textsuche mit Entfernung von Akzenten|
> |[uuid-ossp](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1.1             | Generierung von UUIDs (Universally Unique Identifiers)|

## <a name="postgres-11-extensions"></a>Postgres 11-Erweiterungen

Die folgenden Erweiterungen sind für Azure Database for PostgreSQL Flexible Server mit Postgres-Version 11 verfügbar. 

> [!div class="mx-tableFixed"]
> | **Erweiterung**| **Erweiterungsversion** | **Beschreibung** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Wird verwendet, um eine Adresse in dessen zugehörige Elemente zu analysieren. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Adressenstandardisierung: US-Datasetbeispiel|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1.1             | Funktionen zum Überprüfen der Beziehungsintegrität|
> |[bloom](https://www.postgresql.org/docs/11/bloom.html)                    | 1.0             | Bloom-Zugriffsmethode: Index, der auf einer Signaturdatei basiert|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | Unterstützung für die Indizierung häufiger Datentypen in GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | Unterstützung für die Indizierung häufiger Datentypen in GIST|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | Datentyp für Zeichenfolgen ohne Berücksichtigung der Groß-/Kleinschreibung|
> |[cube](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | Datentyp für mehrdimensionale Cubes|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | Verbindungsherstellung mit anderen PostgreSQL-Datenbanken aus einer Datenbank|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | Textsuche-Wörterbuchvorlage für Integerwerte|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1.0             | Wörterbuchvorlage für die Textsuche für die erweiterte Verarbeitung von Synonymen|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | Berechnung von Großkreisentfernungen auf der Erdoberfläche|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | Ermittlung von Ähnlichkeiten und der Entfernung zwischen Zeichenfolgen|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | Datentyp zum Speichern von Schlüssel-Wert-Paaren|
> |[hypopg](https://github.com/HypoPG/hypopg)                                   |  1.1.2            | Erweiterung, die Unterstützung für hypothetische Indizes hinzufügt |
> |[intagg](https://www.postgresql.org/docs/11/intagg.html)                     | 1.1             | Integeraggregator und -enumerator. (Veraltet)|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | Funktionen, Operatoren und Indexunterstützung für 1-D-Arrays mit Integerwerten|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | Datentypen für internationale Produktnummerierungsnormen|
> |[lo](https://www.postgresql.org/docs/11/lo.html)                            | 1.1             | Wartung großer Objekte |
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | Datentyp für hierarchische baumähnliche Strukturen|
> |[pageinspect](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1.7             | Überprüft den Inhalt von Datenbankseiten im Detail|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | Untersuchung des freigegebenen Puffercaches|
> |[pg_cron](https://github.com/citusdata/pg_cron)                        | 1.3             | Auftragsplaner für PostgreSQL|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1.2             | Untersucht Free Space Map (FSM)|
> |[pg_partman](https://github.com/pgpartman/pg_partman)         | 4.5.0           | Erweiterung für die Verwaltung partitionierter Tabellen nach Zeit oder ID |
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | „Vorwärmung“ von Beziehungsdaten|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | Nachverfolgung von Ausführungsstatistiken aller ausgeführten SQL-Anweisungen|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | Messung von Textähnlichkeit und Indexsuche basierend auf Trigrammen|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1.2             | Überprüft die Informationen zur Zuordnung von Sichtbarkeiten (VM) und Sichtbarkeit auf Seitenebene|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | Überwachungsfunktionen|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | Kryptografische Funktionen|
>|[pglogisch](https://github.com/2ndQuadrant/pglogical)       | 2.3.2                | Logische Streamingreplikation |
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | Anzeige von Sperrinformationen auf Zeilenebene|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | Anzeige von Statistiken auf Tupelebene|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL: Prozedurale Sprache|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS: Geometrie, Geografie und räumliche Rastertypen und -funktionen|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL-Funktionen|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS Tiger: Geocoder und Reverse-Geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS-Topologie: Räumliche Typen und Funktionen|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | Fremddaten-Wrapper für PostgreSQL-Remoteserver|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1.2             | Informationen zu SSL-Zertifikaten|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | Funktionen zum Bearbeiten gesamter Tabellen, einschließlich Kreuztabelle|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1.0             |  TABLESAMPLE-Methode, die die Anzahl von Zeilen als Grenzwert akzeptiert|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1.0             |  TABLESAMPLE-Methode, die die Zeit in Millisekunden als Grenzwert akzeptiert|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | Wörterbuch für Textsuche mit Entfernung von Akzenten|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | Generierung von UUIDs (Universally Unique Identifiers)|


## <a name="dblink-and-postgres_fdw"></a>„dblink“ und „postgres_fdw“
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) und [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) ermöglichen Ihnen das Herstellen einer Verbindung von einem PostgreSQL-Server mit einem anderen oder mit einer anderen Datenbank auf demselben Server. Flexible Server unterstützen sowohl eingehende als auch ausgehende Verbindungen mit beliebigen PostgreSQL-Servern. Der sendende Server muss ausgehende Verbindungen vom empfangenden Server zulassen. Ebenso muss der empfangende Server Verbindungen vom sendenden Server zulassen. 

Es wird empfohlen, die Server mit [VNet-Integration](concepts-networking.md) bereitzustellen, wenn Sie beabsichtigen, diese beiden Erweiterungen zu verwenden. Standardmäßig lässt die VNet-Integration Verbindungen zwischen Servern im VNET zu. Sie können auch [VNet-Netzwerksicherheitsgruppen](../../virtual-network/manage-network-security-group.md) verwenden, um den Zugriff anzupassen.

## <a name="pg_prewarm"></a>pg_prewarm

Die pg_prewarm-Erweiterung lädt relationale Daten in den Cache. Durch das Vorwärmen der Caches weisen Ihre Abfragen bei der ersten Ausführung nach einem Neustart bessere Reaktionszeiten auf. Die automatische prewarm-Funktion ist in Azure Database for PostgreSQL Flexible Server aktuell nicht verfügbar.

## <a name="pg_cron"></a>pg_cron

[pg_cron](https://github.com/citusdata/pg_cron/) ist ein einfacher, cron-basierter Auftragsplaner für PostgreSQL, der in der Datenbank als Erweiterung ausgeführt wird. Mithilfe der „pg_cron“-Erweiterung können geplante Wartungstasks in einer PostgreSQL-Datenbank ausgeführt werden. Beispielsweise können Sie eine Tabelle regelmäßig mit „vacuum“ bereinigen oder alte Datenaufträge entfernen.

`pg_cron` kann mehrere Aufträge parallel ausführen, aber jeweils höchstens eine Instanz eines Auftrags. Wenn eine zweite Ausführung gestartet werden soll, bevor die erste Ausführung abgeschlossen wurde, wird die zweite Ausführung in die Warteschlange eingereiht und nach Abschluss der ersten Ausführung gestartet. Dadurch wird sichergestellt, dass Aufträge genau so oft wie geplant und nicht gleichzeitig ausgeführt werden.

Einige Beispiele:

So löschen Sie alte Daten am Samstag um 03:30 Uhr (GMT)
```
SELECT cron.schedule('30 3 * * 6', $$DELETE FROM events WHERE event_time < now() - interval '1 week'$$);
```
So führen Sie „vacuum“ (zum Bereinigen) täglich um 10:00 Uhr (GMT) aus
```
SELECT cron.schedule('0 10 * * *', 'VACUUM');
```

So heben Sie den Zeitplan für alle Tasks aus „pg_cron“ auf
```
SELECT cron.unschedule(jobid) FROM cron.job;
```
> [!NOTE]
> Die pg_cron-Erweiterung wird in jeder Azure Database for PostgreSQL Flexible Server-Instanz in der postgres-Datenbank vorab geladen, um Ihnen die Möglichkeit zu geben, Aufträge für die Ausführung in anderen Datenbanken in Ihrer PostgreSQL-Datenbankinstanz ohne Beeinträchtigung der Sicherheit zu planen.

## <a name="pg_stat_statements"></a>pg_stat_statements

Die [Erweiterung „pg_stat_statements“](https://www.postgresql.org/docs/current/pgstatstatements.html) wird auf jedem flexiblen Server von Azure Database for PostgreSQL vorab geladen, um für Sie eine Möglichkeit zur Nachverfolgung von Ausführungsstatistiken von SQL-Anweisungen zu schaffen.
Die Einstellung `pg_stat_statements.track`, die steuert, welche Anweisungen von der Erweiterung gezählt werden, ist standardmäßig auf `top` festgelegt, was bedeutet, dass alle Anweisungen, die direkt von Clients ausgestellt werden, nachverfolgt werden. Die beiden anderen Nachverfolgungsebenen sind `none` und `all`. Diese Einstellung kann als Serverparameter konfiguriert werden.

Zwischen den von pg_stat_statements bereitgestellten Abfrageausführungsinformationen und der Auswirkung auf die Serverleistung besteht ein Kompromiss, da jede SQL-Anweisung protokolliert wird. Wenn Sie die pg_stat_statements-Erweiterung nicht aktiv verwenden, empfiehlt es sich, `pg_stat_statements.track` auf `none` festzulegen. Beachten Sie, dass sich einige Überwachungsdienste von Drittanbietern auf pg_stat_statements beziehen können, um Statistiken zur Abfrageleistung zu liefern. Bestätigen Sie also, ob dieser Fall auf Sie zutrifft oder nicht.


## <a name="next-steps"></a>Nächste Schritte

Wenn Sie eine Erweiterung fehlt, die Sie verwenden möchten, lassen Sie es uns wissen. Stimmen Sie in unserem [Feedbackforum](https://feedback.azure.com/d365community/forum/c5e32b97-ee24-ec11-b6e6-000d3a4f0da0) für vorhandene Anfragen ab, oder geben Sie uns weiteres Feedback.
