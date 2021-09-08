---
title: Erweiterungen – Hyperscale (Citus) – Azure Database for PostgreSQL
description: Beschreibt die Möglichkeit zum Erweitern der Funktionalität von Datenbanken mithilfe von Erweiterungen in Azure Database for PostgreSQL mit Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 27c7ae33dc889f7bbc29bb5e396fae3a6cdea67d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122345967"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL-Erweiterungen in Azure Database for PostgreSQL – Hyperscale (Citus)

PostgreSQL bietet die Möglichkeit, die Funktionalität Ihrer Datenbank mithilfe von Erweiterungen zu erweitern. Durch Erweiterungen können mehrere SQL-bezogene Objekte zu einem einzigen Paket gebündelt und über einen einzigen Befehl in die Datenbank geladen oder aus dieser entfernt werden. Nach dem Laden in die Datenbank können Erweiterungen ebenso wie integrierte Features funktionieren. Weitere Informationen zu PostgreSQL-Erweiterungen finden Sie unter [Packen von zugehörigen Objekten in einer Erweiterung](https://www.postgresql.org/docs/current/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Verwenden von PostgreSQL-Erweiterungen

Bevor Sie PostgreSQL-Erweiterungen verwenden können, müssen diese in Ihrer Datenbank installiert werden. Um eine bestimmte Erweiterung zu installieren, führen Sie zum Laden der gepackten Objekte in Ihrer Datenbank den Befehl [CREATE EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html) über das psql-Tool aus.

> [!NOTE]
> Wenn bei `CREATE EXTENSION` der Fehler „Berechtigung verweigert“ auftritt, probieren Sie stattdessen die `create_extension()`-Funktion aus. Zum Beispiel:
>
> ```sql
> SELECT create_extension('postgis');
> ```

Azure Database for PostgreSQL – Hyperscale (Citus) unterstützt eine Teilmenge der wichtigsten unten aufgeführten Erweiterungen. Andere Erweiterungen als die aufgelisteten Erweiterungen werden nicht unterstützt. Mit Azure Database for PostgreSQL können keine eigenen Erweiterungen erstellt werden.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Unterstützte Erweiterungen in Azure-Datenbank für PostgreSQL

In den folgenden Tabellen werden die standardmäßigen PostgreSQL-Erweiterungen aufgeführt, die derzeit von Azure-Datenbank für PostgreSQL unterstützt werden. Sie können diese Informationen auch abrufen, indem Sie `SELECT * FROM pg_available_extensions;` ausführen.

Die Versionen der einzelnen Erweiterungen, die in einer Servergruppe installiert sind, unterscheiden sich in manchen Fällen je nach PostgreSQL-Version (11, 12 oder 13). Die Tabellen enthalten Erweiterungsversionen pro Datenbankversion.

### <a name="citus-extension"></a>Citus-Erweiterung

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [citus](https://github.com/citusdata/citus) | Von Citus verteilte Datenbank. | 9.5-2 | 10.0-3 | 10.0-3 |

### <a name="data-types-extensions"></a>Datentypenerweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Gibt einen Zeichenfolgentyp an, bei dem Groß-/Kleinschreibung beachtet werden muss. | 1.5 | 1.6 | 1.6 |
> | [cube](https://www.postgresql.org/docs/current/static/cube.html) | Stellt einen Datentyp für mehrdimensionale Cubes bereit. | 1.4 | 1.4 | 1.4 |
> | [hll](https://github.com/citusdata/postgresql-hll) | Stellt eine HyperLogLog-Datenstruktur bereit. | 2.15 | 2.15 | 2.15 |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Gibt den Datentyp zum Speichern von Schlüssel-Wert-Paaren an. | 1.5 | 1.6 | 1.7 |
> | [isn](https://www.postgresql.org/docs/current/static/isn.html) | Stellt Datentypen für internationale Produktnummerierungsnormen bereit. | 1.2 | 1.2 | 1.2 |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Large Object-Wartung. | 1.1 | 1.1 | 1.1 |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Stellt einen Datentyp für hierarchische baumähnliche Strukturen bereit. | 1.1 | 1.1 | 1.2 |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Datentyp für die Darstellung von Liniensegmenten oder Gleitkommaintervallen. | 1.3 | 1.3 | 1.3 |
> | [tdigest](https://github.com/tvondra/tdigest) | Datentyp für die Onlineakkumulation von rangbasierten Statistiken wie Quantilen und gekürzten Mittelwerten | 1.0 | 1.0 | 1.0 |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Typ für Erste N-JSONB. | 2.3.1 | 2.3.1 | 2.3.1 |

### <a name="full-text-search-extensions"></a>Erweiterungen für die Volltextsuche

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | Bietet eine Textsuch-Wörterbuchvorlage für Integerwerte. | 1.0 | 1.0 | 1.0 |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Wörterbuchvorlage für die Textsuche für die erweiterte Verarbeitung von Synonymen. | 1.0 | 1.0 | 1.0 |
> | [unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | Ein Wörterbuch für die Textsuche, das Akzente (diakritische Zeichen) aus Lexemen entfernt | 1.1 | 1.1 | 1.1 |

### <a name="functions-extensions"></a>Funktionserweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funktionen für die automatische Inkrementierung von Feldern. | 1.0 | 1.0 | 1.0 |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Bietet eine Möglichkeit, Großkreisentferungen auf der Erdoberfläche zu berechnen. | 1.1 | 1.1 | 1.1 |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Stellt mehrere Funktionen bereit, um Ähnlichkeiten und den Abstand zwischen Zeichenfolgen zu ermitteln | 1.1 | 1.1 | 1.1 |
> | [insert\_username](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funktionen, um nachzuverfolgen, welcher Benutzer Änderungen in einer Tabelle vorgenommen hat. | 1.0 | 1.0 | 1.0 |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Integeraggregator und -enumerator (veraltet). | 1.1 | 1.1 | 1.1 |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Stellt Funktionen und Operatoren bereit, um Arrays mit Ganzzahlen ohne Nullen zu bearbeiten | 1.2 | 1.2 | 1.3 |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funktionen zur Verfolgung der letzten Änderungszeit. | 1.0 | 1.0 | 1.0 |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Verwaltet partitionierte Tabellen nach Zeit oder ID | 4.5.1 | 4.5.1 | 4.5.1 |
> | [pg\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Stellt Funktionen und Operatoren bereit, um die Ähnlichkeit von alphanumerischen Texten basierend auf übereinstimmenden Trigrammen zu ermitteln | 1.4 | 1.4 | 1.5 |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Stellt kryptografische Funktionen bereit | 1.3 | 1.3 | 1.3 |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funktionen für die Implementierung referentieller Integrität (veraltet). | 1.0 | 1.0 | 1.0 |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Stellt Funktionen bereit, um ganze Tabellen einschließlich einer Kreuztabelle zu bearbeiten. | 1.0 | 1.0 | 1.0 |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Ausgelöste Änderungsbenachrichtigungen. | 1.0 | 1.0 | 1.0 |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funktionen für die Implementierung von Zeitreisen. | 1.0 | | |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Generiert universell eindeutige Bezeichner (UUIDs) | 1.1 | 1.1 | 1.1 |

### <a name="index-types-extensions"></a>Indextypenerweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | Bloom-Zugriffsmethode: Index, der auf einer Signaturdatei basiert. | 1.0 | 1.0 | 1.0 |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Gibt Beispielklassen für den GIN-Operator an, der das B-Struktur-ähnliche Verhalten für bestimmte Datentypen implementiert. | 1.3 | 1.3 | 1.3 |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | Gibt Operatorklassen für den GiST-Index an, der die B-Struktur implementiert | 1.5 | 1.5 | 1.5 |

### <a name="language-extensions"></a>Spracherweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | Ladbare prozedurale Sprache, PL/pgSQL. | 1.0 | 1.0 | 1.0 |

### <a name="miscellaneous-extensions"></a>Verschiedene Erweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Verwaltungsfunktionen für PostgreSQL. | 2.0 | 2.0 | 2.1 |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funktionen zum Überprüfen der Beziehungsintegrität. | 1.1 | 1.2 | 1.2 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Ein Modul, das Verbindungen mit anderen PostgreSQL-Datenbanken innerhalb einer Datenbanksitzung unterstützt Weitere Informationen zu dieser Erweiterung finden Sie im Abschnitt „dblink und postgres_fdw“. | 1.2 | 1.2 | 1.2 |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Wrapper für programmfremde Daten für den Flatfile-Zugriff. | 1.0 | 1.0 | 1.0 |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Überprüfen Sie den Inhalt von Datenbankseiten auf niedriger Ebene. | 1.7 | 1.7 | 1.8 |
> | [pg\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Bietet eine Möglichkeit, um die Vorgänge im freigegebenen Puffercache in Echtzeit mitzuverfolgen | 1.3 | 1.3 | 1.3 |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | Auftragsplaner für PostgreSQL. | 1.3 | 1.3 | 1.3 |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Untersuchen von free space map (FSM). | 1.2 | 1.2 | 1.2 |
> | [pg\_prewarm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Bietet eine Möglichkeit, um Relationsdaten in den Puffercache zu laden | 1.2 | 1.2 | 1.2 |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Bietet eine Möglichkeit, um Ausführungsstatistiken zu allen SQL-Anweisungen nachzuverfolgen, die von einem Server ausgeführt werden Weitere Informationen zu dieser Erweiterung finden Sie im Abschnitt „pg_stat_statements“. | 1.6 | 1.7 | 1.8 |
> | [pg\_visibility](https://www.postgresql.org/docs/current/pgvisibility.html) | Überprüft die Informationen zur Zuordnung von Sichtbarkeiten (Visibility Map, VM) und Informationen zur Sichtbarkeit auf Seitenebene. | 1.2 | 1.2 | 1.2 |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Bietet eine Möglichkeit zum Anzeigen von Sperrinformationen auf Zeilenebene. | 1.2 | 1.2 | 1.2 |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Bietet eine Möglichkeit zum Anzeigen von Statistiken auf Tupelebene. | 1.5 | 1.5 | 1.5 |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Wrapper für programmfremde Daten, um auf in externen PostgreSQL-Servern gespeicherten Daten zuzugreifen Weitere Informationen zu dieser Erweiterung finden Sie im Abschnitt „dblink und postgres_fdw“.| 1.0 | 1.0 | 1.0 |
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informationen zu TLS-/SSL-Zertifikaten | 1.2 | 1.2 | 1.2 |
> | [tsm\_system\_rows](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE-Methode, die die Anzahl von Zeilen als Grenzwert akzeptiert. | 1.0 | 1.0 | 1.0 |
> | [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE-Methode, die die Zeit in Millisekunden als Grenzwert akzeptiert. | 1.0 | 1.0 | 1.0 |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath-Abfrage und XSLT. | 1.1 | 1.1 | 1.1 |


### <a name="postgis-extensions"></a>PostGIS-Erweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Räumliche und geografische Objekte für PostgreSQL | 2.5.5 | 3.0.3 | 3.0.3 |
> | address\_standardizer, address\_standardizer\_data\_us | Wird verwendet, um eine Adresse in dessen zugehörige Elemente zu analysieren. Wird als Unterstützung für den Normalisierungsschritt zur Geocodierung von Adressen verwendet. | 2.5.5 | 3.0.3 | 3.0.3 |
> | postgis\_sfcgal | PostGIS SFCGAL-Funktionen. | 2.5.5 | 3.0.3 | 3.0.3 |
> | postgis\_tiger\_geocoder | Geocoder und Reverse-Geocoder für PostGIS Tiger. | 2.5.5 | 3.0.3 | 3.0.3 |
> | postgis\_topology | Räumliche PostGIS-Topologietypen und -funktionen. | 2.5.5 | 3.0.3 | 3.0.3 |


## <a name="pg_stat_statements"></a>pg_stat_statements
Die [pg\_stat\_statements-Erweiterung](https://www.postgresql.org/docs/current/pgstatstatements.html) wird auf jedem Azure Database for PostgreSQL-Server vorab geladen,um Ihnen eine Möglichkeit zur Nachverfolgung von Ausführungsstatistiken von SQL-Anweisungen bereitzustellen.

Mit der `pg_stat_statements.track`-Einstellung wird gesteuert, welche Anweisungen von der Erweiterung gezählt werden. Der Standardwert ist `top`. Dies bedeutet, dass alle direkt von Clients ausgegebenen Anweisungen nachverfolgt werden. Die beiden anderen Nachverfolgungsebenen sind `none` und `all`. Diese Einstellung kann als Serverparameter über das [Azure-Portal](./howto-configure-server-parameters-using-portal.md) oder die [Azure CLI](./howto-configure-server-parameters-using-cli.md) konfiguriert werden.

Zwischen den von pg_stat_statements bereitgestellten Abfrageausführungsinformationen und der Auswirkung auf die Serverleistung besteht ein Kompromiss, da jede SQL-Anweisung protokolliert wird. Wenn Sie die pg_stat_statements-Erweiterung nicht aktiv verwenden, empfiehlt es sich, `pg_stat_statements.track` auf `none` festzulegen. Einige Überwachungsdienste von Drittanbietern können sich auf pg_stat_statements beziehen, um Statistiken zur Abfrageleistung zu liefern. Bestätigen Sie daher, ob dieser Fall auf Sie zutrifft.

## <a name="dblink-and-postgres_fdw"></a>„dblink“ und „postgres_fdw“

Sie können „dblink“ und „postgres\_fdw“ verwenden, um eine Verbindung von einem PostgreSQL-Server mit einem anderen PostgreSQL-Server oder mit einer anderen Datenbank auf demselben Server herzustellen.  Der empfangende Server muss Verbindungen vom sendenden Server über die Firewall zulassen.  Um diese Erweiterungen für Verbindungen zwischen Azure Database for PostgreSQL-Servern oder Hyperscale (Citus)-Servergruppen zu verwenden, aktivieren Sie **Azure-Diensten und -Ressourcen den Zugriff auf diese Servergruppe (oder diesen Server) gestatten**.  Sie müssen diese Einstellung auch aktivieren, wenn Sie die Erweiterungen für ein Loopback zum selben Server verwenden möchten.
Die Einstellung **Azure-Diensten und -Ressourcen den Zugriff auf diese Servergruppe gestatten** finden Sie auf der Azure-Portalseite für die Hyperscale (Citus)-Servergruppe unter **Netzwerk**.  Derzeit werden von einzelnen Azure Database for PostgreSQL-Servern und Hyperscale (Citus) ausgehende Verbindungen mit Ausnahme von Verbindungen mit anderen Azure Database for PostgreSQL-Servern und Hyperscale (Citus)-Servergruppen nicht unterstützt.
