---
title: Verwenden von PostgreSQL-Erweiterungen
description: Verwenden von PostgreSQL-Erweiterungen
titleSuffix: Azure Arc-enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 52e024043726c463c0bea5b9b16421a0674a2cd2
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558837"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Verwenden von PostgreSQL-Erweiterungen in Servergruppen mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung

PostgreSQL eignet sich am besten für die Verwendung mit Erweiterungen. Tatsächlich ist die von Microsoft bereitgestellte und standardmäßig installierte `citus`-Erweiterung ein wichtiges Element der Hyperscalefunktion, da sie Postgres das transparente und knotenübergreifende Konfigurieren von Datenshards ermöglicht.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="supported-extensions"></a>Unterstützte Erweiterungen
Die Standarderweiterungen vom Typ [`contrib`](https://www.postgresql.org/docs/12/contrib.html) sowie die folgenden Erweiterungen wurden bereits in den Containern Ihrer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung bereitgestellt:
- [`citus`](https://github.com/citusdata/citus), v: 10.2. Die Citus-Erweiterung von [Citus Data](https://www.citusdata.com/) wird standardmäßig geladen, da sie die Hyperscale-Funktion für die PostgreSQL-Engine bereitstellt. Das Löschen der Citus-Erweiterung aus Ihrer PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung wird nicht unterstützt.
- [`pg_cron`](https://github.com/citusdata/pg_cron), Version 1.3
- [`pgaudit`](https://www.pgaudit.org/), Version 1.4
- plpgsql, Version 1.0
- [`postgis`](https://postgis.net), Version 3.0.2
- [`plv8`](https://plv8.github.io/), Version 2.3.14
- [`pg_partman`](https://github.com/pgpartman/pg_partman), Version 4.4.1/
- [`tdigest`](https://github.com/tvondra/tdigest), Version 1.0.1

Wenn sich diese Liste im Laufe der Zeit verändert, werden entsprechende Aktualisierungen veröffentlicht.

> [!IMPORTANT]
> Sie können Ihre Servergruppe zwar auch mit einer Erweiterung versehen, die in der obigen Liste nicht enthalten ist, im Rahmen dieser Vorschau wird sie jedoch nicht in Ihrem System gespeichert. Das bedeutet, dass sie nach einem Neustart des Systems nicht mehr verfügbar ist und erneut integriert werden muss.

In diesem Leitfaden wird ein Szenario verwendet, in dem zwei dieser Erweiterungen verwendet werden:
- [`PostGIS`](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)

## <a name="which-extensions-need-to-be-added-to-the-shared_preload_libraries-and-created"></a>Welche Erweiterungen müssen „shared_preload_libraries“ hinzugefügt und erstellt werden?

|Erweiterungen   |Hinzufügen zu „shared_preload_libraries“ erforderlich?  |Erstellung erforderlich? |
|-------------|--------------------------------------------------|---------------------- |
|`pg_cron`      |Nein       |Ja        |
|`pg_audit`     |Ja       |Ja        |
|`plpgsql`      |Ja       |Ja        |
|`postgis`      |Nein       |Ja        |
|`plv8`      |Nein       |Ja        |

## <a name="add-extensions-to-the-shared_preload_libraries"></a>Hinzufügen von Erweiterungen zu `shared_preload_libraries`
Ausführliche Informationen zu `shared_preload_libraries` finden Sie in der [PostgreSQL-Dokumentation](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SHARED-PRELOAD-LIBRARIES):
- Dieser Schritt ist nicht erforderlich für Erweiterungen, die Teil von `contrib` sind.
- Dieser Schritt ist nicht erforderlich für Erweiterungen, die nicht vorab durch „shared_preload_libraries“ geladen werden müssen. Für diese Erweiterungen können Sie direkt zum Absatz [Erstellen von Erweiterungen](#create-extensions) springen.

### <a name="add-an-extension-at-the-creation-time-of-a-server-group"></a>Hinzufügen einer Erweiterung beim Erstellen einer Servergruppe
```azurecli
az postgres arc-server create -n <name of your postgresql server group> --extensions <extension names> --k8s-namespace <namespace> --use-k8s
```
### <a name="add-an-extension-to-an-instance-that-already-exists"></a>Hinzufügen einer Erweiterung zu einer bereits vorhandenen Instanz
```azurecli
az postgres arc-server server edit -n <name of your postgresql server group> --extensions <extension names> --k8s-namespace <namespace> --use-k8s
```




## <a name="show-the-list-of-extensions-added-to-shared_preload_libraries"></a>Anzeigen der Liste der Erweiterungen, die „shared_preload_libraries“ hinzugefügt wurden
Führen Sie einen der folgenden Befehlen aus.

### <a name="with-cli-command"></a>Mit Befehl an der Befehlszeilenschnittstelle
```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```
Scrollen Sie durch die Ausgabe, und beachten Sie die engine\extensions-Abschnitte in den Spezifikationen Ihrer Servergruppe. Zum Beispiel:
```console
  "spec": {
    "dev": false,
    "engine": {
      "extensions": [
        {
          "name": "citus"
        }
      ],
```
### <a name="with-kubectl"></a>Verwenden von kubectl
```console
kubectl describe postgresqls/<server group name> -n <namespace>
```
Scrollen Sie durch die Ausgabe, und beachten Sie die engine\extensions-Abschnitte in den Spezifikationen Ihrer Servergruppe. Zum Beispiel:
```console
Spec:
  Dev:  false
  Engine:
    Extensions:
      Name:   citus
```


## <a name="create-extensions"></a>Erstellen von Erweiterungen
Stellen Sie mit dem Clienttool Ihrer Wahl eine Verbindung mit Ihrer Servergruppe her, und führen Sie die PostgreSQL-Standardabfrage aus:
```console
CREATE EXTENSION <extension name>;
```

## <a name="show-the-list-of-extensions-created"></a>Anzeigen der Liste erstellter Erweiterungen
Stellen Sie mit dem Clienttool Ihrer Wahl eine Verbindung mit Ihrer Servergruppe her, und führen Sie die PostgreSQL-Standardabfrage aus:
```console
select * from pg_extension;
```

## <a name="drop-an-extension"></a>Löschen einer Erweiterung
Stellen Sie mit dem Clienttool Ihrer Wahl eine Verbindung mit Ihrer Servergruppe her, und führen Sie die PostgreSQL-Standardabfrage aus:
```console
drop extension <extension name>;
```

## <a name="the-postgis-extension"></a>Die Erweiterung `PostGIS`
Die Erweiterung `PostGIS` muss nicht `shared_preload_libraries` hinzugefügt werden.
Rufen Sie [Beispieldaten](http://duspviz.mit.edu/tutorials/intro-postgis/) vom Department of Urban Studies & Planning des Massachusetts Institute of Technology (MIT) ab. Führen Sie zum Installieren/Entzippen `apt-get install unzip` gemäß Ihren Anforderungen aus.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Stellen Sie eine Verbindung mit der Datenbank her, und erstellen Sie die Erweiterung `PostGIS`:

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> Wenn Sie eine der Erweiterungen im `postgis`-Paket (z. B. `postgis_raster`, `postgis_topology`, `postgis_sfcgal`, `fuzzystrmatch` usw.) verwenden möchten, müssen Sie zuerst die PostGIS-Erweiterung und dann die andere Erweiterung erstellen. Beispiel: `CREATE EXTENSION postgis`; `CREATE EXTENSION postgis_raster`;

Erstellen Sie dann das Schema:

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

Nun können Sie `PostGIS` mit der Funktion für die horizontale Skalierung kombinieren, indem Sie die Tabelle „coffee_shops“ verteilen:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Laden Sie nun einige Daten:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

Füllen Sie das Feld `geom` mit den ordnungsgemäß codierten Breiten- und Längengraden des `PostGIS`-Datentyps `geometry`:

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Nun können Sie die Cafés auflisten, die sich am nächsten des MIT befinden (77 Massachusetts Ave; 42.359055, -71.093500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


## <a name="the-pg_cron-extension"></a>Die Erweiterung `pg_cron`

Aktivieren Sie nun `pg_cron` in der PostgreSQL-Servergruppe, indem Sie sie „shared_preload_libraries“ hinzufügen:

```azurecli
az postgres arc-server update -n pg2 -ns arc --extensions pg_cron
```

Ihre Servergruppe wird neu gestartet, um die Installation der Erweiterungen abzuschließen. Das kann zwei bis drei Minuten dauern.

Nun können Sie noch mal eine Verbindung herstellen und die `pg_cron`-Erweiterung erstellen:

```sql
CREATE EXTENSION pg_cron;
```

Erstellen Sie zu Testzwecken die Tabelle `the_best_coffee_shop`, die einen zufälligen Namen aus der früheren Tabelle `coffee_shops` verwendet und den Tabelleninhalt einfügt:

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

Sie können `cron.schedule` und einige SQL-Anweisungen verwenden, um einen zufälligen Tabellennamen abzurufen und in `the_best_coffee_shop` zu speichern (dabei temporäre Tabelle zum Speichern eines verteilten Abfrageergebnisses verwenden):

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

Jetzt wird einmal pro Minute ein anderer Name angezeigt:

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

Ausführliche Informationen zur Syntax finden Sie in der [pg_cron-Infodatei](https://github.com/citusdata/pg_cron).


## <a name="next-steps"></a>Nächste Schritte
- Die zugehörige Dokumentation finden Sie unter [`plv8`](https://plv8.github.io/).
- Die zugehörige Dokumentation finden Sie unter [`PostGIS`](https://postgis.net/).
- Die zugehörige Dokumentation finden Sie unter [`pg_cron`](https://github.com/citusdata/pg_cron).
