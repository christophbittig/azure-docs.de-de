---
title: Analysieren von Schemas mit Arrays und geschachtelten Strukturen
description: Erfahren Sie, wie Sie Arrays und geschachtelte Strukturen mit Apache Spark und SQL analysieren.
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: spark
ms.date: 06/15/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: 0130379b5c9225329bde1d49c119eaec446d17de
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112622"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Analysieren komplexer Datentypen in Azure Synapse Analytics

Dieser Artikel bezieht sich auf Parquet-Dateien und Container in [Azure Synapse Link für Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md). Sie können Spark oder SQL verwenden, um Daten mit komplexen Schemas wie Arrays oder geschachtelten Strukturen zu lesen oder zu transformieren. Das folgende Beispiel wird mit einem einzigen Dokument durchgeführt, kann aber mit Spark oder SQL problemlos auf Milliarden von Dokumente skaliert werden. Der in diesem Artikel enthaltene Code verwendet PySpark (Python).

## <a name="use-case"></a>Anwendungsfall

Komplexe Datentypen treten immer häufiger auf und stellen eine Herausforderung für technische Fachkräfte für Daten dar. Für die Analyse von geschachtelten Schemas und Arrays können zeitaufwändige und komplexe SQL-Abfragen erforderlich sein. Darüber hinaus kann es schwierig sein, den Datentyp geschachtelter Spalten umzubenennen oder umzuwandeln. Wenn Sie mit tief geschachtelten Objekten arbeiten, können außerdem Leistungsprobleme auftreten.

Technische Fachkräfte für Daten müssen wissen, wie komplexe Datentypen effizient verarbeitet und für alle leicht zugänglich gemacht werden können. Im folgenden Beispiel wird gezeigt, wie Objekte mithilfe von Spark in Azure Synapse Analytics gelesen und über Datenrahmen in eine flache Struktur transformiert werden. Sie werden solche Objekte mithilfe des serverlosen Modells in Azure Synapse Analytics direkt abfragen und die Ergebnisse als reguläre Tabelle zurückgeben.

## <a name="what-are-arrays-and-nested-structures"></a>Was sind Arrays und geschachtelte Strukturen?

Das folgende Objekt stammt aus [Application Insights](../azure-monitor/app/app-insights-overview.md). Dieses Objekt enthält geschachtelte Strukturen und Arrays, die geschachtelte Strukturen enthalten.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>Schemabeispiel für Arrays und geschachtelte Strukturen
Bei der Ausgabe des Schemas für den Datenrahmen (namens **df**) des Objekts über den Befehl `df.printschema` wird die folgende Darstellung angezeigt:

* Geschachtelte Strukturen sind gelb hervorgehoben.
* Die grüne Markierung kennzeichnet ein Array mit zwei Elementen.

[![Code mit gelber und grüner Hervorhebung, die den Schemaursprung anzeigt](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

`_rid`, `_ts` und `_etag` wurden dem System beim Erfassen des Dokuments im Transaktionsspeicher von Azure Cosmos DB hinzugefügt.

Der vorherige Datenrahmen umfasst 5 Spalten und 1 Zeile. Nach der Transformation verfügt der erstellte Datenrahmen über 13 Spalten und 2 Zeilen in einem Tabellenformat.

## <a name="flatten-nested-structures-and-explode-arrays"></a>Vereinfachen von geschachtelten Strukturen und Auflösen von Arrays

Mit Spark in Azure Synapse Analytics ist das Transformieren von geschachtelten Strukturen in Spalten und Arrayelemente in mehreren Zeilen ganz einfach. Verwenden Sie die folgenden Schritte für die Implementierung.

[![Flussdiagramm: Schritte für Spark-Transformationen](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

### <a name="define-a-function-to-flatten-the-nested-schema"></a>Definieren einer Funktion zum Vereinfachen des geschachtelten Schemas

Diese Funktion kann ohne Änderungen verwendet werden. Erstellen Sie eine Zelle in einem [PySpark-Notebook](quickstart-apache-spark-notebook.md) mit der folgenden Funktion:

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

### <a name="use-the-function-to-flatten-the-nested-schema"></a>Verwenden der Funktion zum Vereinfachen des geschachtelten Schemas

In diesem Schritt werden Sie das geschachtelte Schema des Datenrahmens (**df**) in einen neuen Datenrahmen (`df_flat`) vereinfachen:

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

Die Anzeigefunktion sollte 10 Spalten und 1 Zeile zurückgeben. Das Array und seine geschachtelten Elemente sind immer noch vorhanden.

### <a name="transform-the-array"></a>Transformieren des Arrays

Hier transformieren Sie das Array `context_custom_dimensions` im Datenrahmen `df_flat` in einen neuen Datenrahmen `df_flat_explode`. Im folgenden Code definieren Sie auch, welche Spalte ausgewählt werden soll:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

Die Anzeigefunktion sollte 10 Spalten und 2 Zeilen zurückgeben. Der nächste Schritt ist das Vereinfachen von geschachtelten Schemas mit der in Schritt 1 definierten Funktion.

### <a name="use-the-function-to-flatten-the-nested-schema"></a>Verwenden der Funktion zum Vereinfachen des geschachtelten Schemas

Zuletzt vereinfachen Sie mithilfe der Funktion das geschachtelte Schema des Datenrahmens `df_flat_explode` in einen neuen Datenrahmen `df_flat_explode_flat`:
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

Die Anzeigefunktion sollte 13 Spalten und 2 Zeilen zurückgeben.

Die Funktion `printSchema` des Datenrahmens `df_flat_explode_flat` gibt das folgende Ergebnis zurück:

[![Code mit dem endgültigen Schema](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly"></a>Direktes Lesen von Arrays und geschachtelten Strukturen

Mit dem serverlosen SQL-Modell können Sie Ansichten und Tabellen über solche Objekte abfragen und erstellen.

Zuerst sollten die Benutzer, je nachdem, wie die Daten gespeichert wurden, die folgende Taxonomie verwenden. Alle in Großbuchstaben geschriebenen Wörter und Phrasen müssen durch einen der Werte für Ihren Anwendungsfall ersetzt werden:

| Massenvorgang | Format |
| ------ | ------ |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |'Parquet' (ADLSg2)|
| N'endpoint=https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/;account=ACCOUNTNAME;database=DATABASENAME;collection=COLLECTIONNAME;region=REGIONTOQUERY', SECRET='YOURSECRET' |'CosmosDB' (Azure Synapse Link)|


Ersetzen Sie jedes Feld wie folgt:
* 'YOUR BULK ABOVE' ist die Verbindungszeichenfolge der Datenquelle, mit der Sie eine Verbindung herstellen.
* 'YOUR TYPE ABOVE' ist das Format, das Sie zum Herstellen einer Verbindung mit der Quelle verwenden.

```sql
select *
FROM
openrowset(
    BULK 'YOUR BULK ABOVE',
    FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

Es gibt zwei verschiedene Arten von Vorgängen:

- Der erste Vorgangstyp ist in der folgenden Codezeile angegeben, die die Spalte mit dem Namen `contextdataeventTime` definiert, die auf das geschachtelte Element `Context.Data.eventTime` verweist: 
  ```sql
  contextdataeventTime varchar(50) '$.context.data.eventTime'
  ```

  In dieser Zeile wird die Spalte namens `contextdataeventTime` definiert, die auf das geschachtelte Element `Context>Data>eventTime` verweist.

- Der zweite Vorgangstyp verwendet `cross apply`, um für jedes Element unter dem Array neue Zeilen zu erstellen. Anschließend definiert er die einzelnen Objekte. 
  ```sql
  cross apply openjson (contextcustomdimensions) 
  with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
  ```

  Wenn das Array 5 Elemente mit 4 geschachtelten Strukturen enthält, gibt das serverlose Modell von SQL 5 Zeilen und 4 Spalten zurück. Das serverlose Modell von SQL kann direkte Abfragen ausführen, das Array 2 Zeilen zuordnen und alle geschachtelten Strukturen in Spalten anzeigen.

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Synapse Link für Azure Cosmos DB mit Spark 3 abfragen.](./synapse-link/how-to-query-analytical-store-spark-3.md)
* [Erfahren Sie, wie Sie Synapse Link für Azure Cosmos DB mit Spark 2 abfragen.](./synapse-link/how-to-query-analytical-store-spark.md)
* [Abfragen von geschachtelten Parquet-Typen](./sql/query-parquet-nested-types.md)
