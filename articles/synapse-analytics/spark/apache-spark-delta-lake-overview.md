---
title: Übersicht über die Verwendung von Linux Foundation Delta Lake in Apache Spark für Azure Synapse Analytics
description: Hier erfahren Sie, wie Sie Delta Lake in Apache Spark für Azure Synapse Analytics verwenden, um Tabellen mit ACID-Eigenschaften zu erstellen und zu verwenden.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: euang
ms.topic: overview
ms.subservice: spark
ms.date: 07/28/2020
ms.author: euang
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 37ebef447a5636a1e84ec662e95b38ff90558df6
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254719"
---
# <a name="linux-foundation-delta-lake-overview"></a>Übersicht über Linux Foundation Delta Lake

Die ursprüngliche Version dieses Artikels finden Sie [hier](https://docs.delta.io/latest/quick-start.html). Sie wurde aus Gründen der Übersichtlichkeit überarbeitet. Anhand dieses Artikels können Sie sich schnell mit den wichtigsten Features von [Delta Lake](https://delta.io) vertraut machen. In den Codeausschnitten dieses Artikels wird gezeigt, wie Sie interaktive Abfragen, Batchabfragen und Streamingabfragen verwenden, um Lese- und Schreibvorgänge für Delta Lake-Tabellen auszuführen. Die Codeausschnitte sind auch in einer Reihe von Notebooks für [PySpark](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Python.ipynb), [Scala](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Scala/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Scala.ipynb) und [C#](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Spark.NET%20C%23/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20CSharp.ipynb) verfügbar.

In diesem Artikel wird Folgendes behandelt:

* Erstellen einer Tabelle
* Lesen von Daten
* Aktualisieren von Tabellendaten
* Überschreiben von Tabellendaten
* Bedingtes Aktualisieren ohne Überschreiben
* Lesen älterer Versionen von Daten mittels Zeitreise
* Schreiben eines Datenstroms in eine Tabelle
* Lesen eines Datenstrom mit Änderungen aus einer Tabelle
* SQL-Unterstützung

## <a name="configuration"></a>Konfiguration

Passen Sie Folgendes an Ihre Umgebung an:

:::zone pivot = "programming-language-python"

```python
import random

session_id = random.randint(0,1000000)
delta_table_path = "/delta/delta-table-{0}".format(session_id)

delta_table_path
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var sessionId = (new Random()).Next(10000000);
var deltaTablePath = $"/delta/delta-table-{sessionId}";

deltaTablePath
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val sessionId = scala.util.Random.nextInt(1000000)
val deltaTablePath = s"/delta/delta-table-$sessionId";
```

::: zone-end

Ergebnis:

'/delta/delta-table-335323'

## <a name="create-a-table"></a>Erstellen einer Tabelle

Schreiben Sie zum Erstellen einer Delta Lake-Tabelle einen Datenrahmen auf der Grundlage eines Datenrahmens im Deltaformat. Das Format kann von Parquet, CSV, JSON usw. in Delta geändert werden.

Der folgende Code zeigt, wie Sie mithilfe des von Ihrem Datenrahmen abgeleiteten Schemas eine neue Delta Lake-Tabelle erstellen:

:::zone pivot = "programming-language-python"

```python
data = spark.range(0,5)
data.show()
data.write.format("delta").save(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(0,5);
data.Show();
data.Write().Format("delta").Save(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(0, 5)
data.show
data.write.format("delta").save(deltaTablePath)
```

::: zone-end

Ergebnis:

| id|
|---|
|  0|
|  1|
|  2|
|  3|
|  4|

## <a name="read-data"></a>Lesen von Daten

Geben Sie zum Lesen von Daten aus Ihrer Delta Lake-Tabelle den Pfad der Dateien und das Deltaformat an:

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Load(deltaTablePath);
df.Show()
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").load(deltaTablePath)
df.show()
```

::: zone-end

Ergebnis:

| id|
|---|
|  1|
|  3|
|  4|
|  0|
|  2|

Die Ergebnisreihenfolge unterscheidet sich von der obigen Reihenfolge, da vor der Ausgabe der Ergebnisse keine explizite Reihenfolge angegeben wurde.

## <a name="update-table-data"></a>Aktualisieren von Tabellendaten

Delta Lake unterstützt mehrere Vorgänge zum Ändern von Tabellen mithilfe standardmäßiger Datenrahmen-APIs. Dies ist eine der bedeutenden Verbesserungen, die das Deltaformat mit sich bringt. Im folgenden Beispiel wird ein Batchauftrag ausgeführt, um die Daten in der Tabelle zu überschreiben:

:::zone pivot = "programming-language-python"

```python
data = spark.range(5,10)
data.write.format("delta").mode("overwrite").save(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(5,10);
data.Write().Format("delta").Mode("overwrite").Save(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(5, 10)
data.write.format("delta").mode("overwrite").save(deltaTablePath)
df.show()
```

::: zone-end

Ergebnis:

| id|
|---|
|  7|
|  8|
|  5|
|  9|
|  6|

Wie Sie sehen, wurden alle fünf Datensätze mit neuen Werten aktualisiert.

## <a name="save-as-catalog-tables"></a>Speichern als Katalogtabellen

Von Delta Lake kann in verwaltete oder externe Katalogtabellen geschrieben werden:

:::zone pivot = "programming-language-python"

```python
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql("CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{0}'".format(delta_table_path))
spark.sql("SHOW TABLES").show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
data.Write().Format("delta").SaveAsTable("ManagedDeltaTable");
spark.Sql($"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{deltaTablePath}'");
spark.Sql("SHOW TABLES").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql(s"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '$deltaTablePath'")
spark.sql("SHOW TABLES").show
```

::: zone-end

Ergebnis:

|database|         tableName|isTemporary|
|--------|------------------|-----------|
| default|externaldeltatable|      false|
| default| manageddeltatable|      false|

Mithilfe dieses Codes wurde auf der Grundlage eines vorhandenen Datenrahmens eine neue Tabelle im Katalog erstellt. Diese wird als verwaltete Tabelle bezeichnet. Anschließend wurde eine neue externe Tabelle im Katalog definiert, die einen vorhandenen Speicherort verwendet. Diese wird als externe Tabelle bezeichnet. In der Ausgabe sehen Sie, dass beide Tabellen im Katalog aufgeführt werden – unabhängig davon, wie sie erstellt wurden.

Nun können Sie sich die erweiterten Eigenschaften der beiden Tabellen ansehen:

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ManagedDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=false)
```

::: zone-end

Ergebnis:

|col_name                    |data_type                                                                                                    |comment|
|----------------------------|-------------------------------------------------------------------------------------------------------------|-------|
|id                          |BIGINT                                                                                                       |NULL   |
|                            |                                                                                                             |       |
|Ausführliche Tabelleninformationen  |                                                                                                             |       |
|Datenbank                    |default                                                                                                      |       |
|Tabelle                       |manageddeltatable                                                                                            |       |
|Besitzer                       |trusted-service-user                                                                                         |       |
|Erstellungszeit                |Sat Apr 25 00:35:34 UTC 2020                                                                                 |       |
|Letzter Zugriff                 |Thu Jan 01 00:00:00 UTC 1970                                                                                 |       |
|Erstellt von                  |Spark 2.4.4.2.6.99.201-11401300                                                                              |       |
|type                        |MANAGED                                                                                                      |       |
|Anbieter                    |delta                                                                                                        |       |
|Tabelleneigenschaften            |[transient_lastDdlTime=1587774934]                                                                           |       |
|Statistik                  |2\.407 Bytes                                                                                                   |       |
|Standort                    |abfss://data@\<data lake\>.dfs.core.windows.net/synapse/workspaces/\<workspace name\>/warehouse/manageddeltatable|       |
|SerDe-Bibliothek               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                                                           |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                                                             |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat                                                    |       |
|Speichereigenschaften          |[serialization.format=1]                                                                                     |       |

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ExternalDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=false)
```

::: zone-end

Ergebnis:

|col_name                    |data_type                                                             |comment|
|----------------------------|----------------------------------------------------------------------|-------|
|id                          |BIGINT                                                                |NULL   |
|                            |                                                                      |       |
|Ausführliche Tabelleninformationen  |                                                                      |       |
|Datenbank                    |default                                                               |       |
|Tabelle                       |externaldeltatable                                                    |       |
|Besitzer                       |trusted-service-user                                                  |       |
|Erstellungszeit                |Sat Apr 25 00:35:38 UTC 2020                                          |       |
|Letzter Zugriff                 |Thu Jan 01 00:00:00 UTC 1970                                          |       |
|Erstellt von                  |Spark 2.4.4.2.6.99.201-11401300                                       |       |
|type                        |EXTERNAL                                                              |       |
|Anbieter                    |DELTA                                                                 |       |
|Tabelleneigenschaften            |[transient_lastDdlTime=1587774938]                                    |       |
|Standort                    |abfss://data@\<data lake\>.dfs.core.windows.net/delta/delta-table-587152|       |
|SerDe-Bibliothek               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                    |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                      |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat             |       |
|Speichereigenschaften          |[serialization.format=1]                                              |       |

## <a name="conditional-update-without-overwrite"></a>Bedingtes Aktualisieren ohne Überschreiben

Delta Lake bietet programmgesteuerte APIs für die bedingte Aktualisierung, Löschung und Zusammenführung von Tabellendaten (häufig als Upsert bezeichnet):

:::zone pivot = "programming-language-python"

```python
from delta.tables import *
from pyspark.sql.functions import *

delta_table = DeltaTable.forPath(spark, delta_table_path)

delta_table.update(
  condition = expr("id % 2 == 0"),
  set = { "id": expr("id + 100") })
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Delta;
using Microsoft.Spark.Extensions.Delta.Tables;
using Microsoft.Spark.Sql;
using static Microsoft.Spark.Sql.Functions;

var deltaTable = DeltaTable.ForPath(deltaTablePath);

deltaTable.Update(
  condition: Expr("id % 2 == 0"),
  set: new Dictionary<string, Column>(){{ "id", Expr("id + 100") }});
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
import io.delta.tables._
import org.apache.spark.sql.functions._

val deltaTable = DeltaTable.forPath(deltaTablePath)

// Update every even value by adding 100 to it
deltaTable.update(
  condition = expr("id % 2 == 0"),
  set = Map("id" -> expr("id + 100")))
deltaTable.toDF.show
```

::: zone-end

Ergebnis:

| id|
|---|
|106|
|108|
|  5|
|  7|
|  9|

Hier wurde einfach 100 zu jeder geraden ID addiert.

:::zone pivot = "programming-language-python"

```python
delta_table.delete("id % 2 == 0")
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.Delete(condition: Expr("id % 2 == 0"));
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.delete(condition = expr("id % 2 == 0"))
deltaTable.toDF.show
```

::: zone-end

Ergebnis:

| id|
|---|
|  5|
|  7|
|  9|

Wie Sie sehen, wurde jede Zeile mit einem geraden Wert gelöscht.

:::zone pivot = "programming-language-python"

```python
new_data = spark.range(0,20).alias("newData")

delta_table.alias("oldData")\
    .merge(new_data.alias("newData"), "oldData.id = newData.id")\
    .whenMatchedUpdate(set = { "id": lit("-1")})\
    .whenNotMatchedInsert(values = { "id": col("newData.id") })\
    .execute()

delta_table.toDF().show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var newData = spark.Range(20).As("newData");

deltaTable
    .As("oldData")
    .Merge(newData, "oldData.id = newData.id")
    .WhenMatched()
        .Update(new Dictionary<string, Column>() {{"id", Lit("-1")}})
    .WhenNotMatched()
        .Insert(new Dictionary<string, Column>() {{"id", Col("newData.id")}})
    .Execute();

deltaTable.ToDF().Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val newData = spark.range(0, 20).toDF

deltaTable.as("oldData").
  merge(
    newData.as("newData"),
    "oldData.id = newData.id").
  whenMatched.
  update(Map("id" -> lit(-1))).
  whenNotMatched.
  insert(Map("id" -> col("newData.id"))).
  execute()

deltaTable.toDF.show()
```

::: zone-end

Ergebnis:

| id|
|---|
| 18|
| 15|
| 19|
|  2|
|  1|
|  6|
|  8|
|  3|
| -1|
| 10|
| 13|
|  0|
| 16|
|  4|
| -1|
| 12|
| 11|
| 14|
| -1|
| 17|

Hier sehen Sie eine Kombination der vorhandenen Daten. Den vorhandenen Daten wurde im update-Codepfad (WhenMatched) der Wert „-1“ zugewiesen. Die neuen Daten, die am Anfang des Codeausschnitts erstellt und über den insert-Codepfad (WhenNotMatched) hinzugefügt wurden, wurden ebenfalls hinzugefügt.

### <a name="history"></a>Verlauf

Mit Delta Lake haben Sie die Möglichkeit, sich den Verlauf einer Tabelle anzusehen. Sprich: Sie können sich die Änderungen ansehen, die an der zugrunde liegenden Deltatabelle vorgenommen wurden. Die folgende Zelle zeigt, wie einfach sich der Verlauf untersuchen lässt:

:::zone pivot = "programming-language-python"

```python
delta_table.history().show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show(false)
```

::: zone-end

Ergebnis:

|version|          timestamp|userId|userName|operation|                                                operationParameters| Auftrag|Notebook|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|---------|-------------------------------------------------------------------|----|--------|---------|-----------|--------------|-------------|
|      4|2020-04-25 00:36:27|  NULL|    NULL|    MERGE|                       [predicate -> (oldData.`ID` = newData.`ID`)]|NULL|    NULL|     NULL|          3|          NULL|        false|
|      3|2020-04-25 00:36:08|  NULL|    NULL|   Delete|[predicate -> ["((`ID` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|NULL|    NULL|     NULL|          2|          NULL|        false|
|      2|2020-04-25 00:35:51|  NULL|    NULL|   UPDATE| [predicate -> ((ID#744L % cast(2 as bigint)) = cast(0 as bigint))]|NULL|    NULL|     NULL|          1|          NULL|        false|
|      1|2020-04-25 00:35:05|  NULL|    NULL|    WRITE|                             [mode -> Overwrite, partitionBy -> []]|NULL|    NULL|     NULL|          0|          NULL|        false|
|      0|2020-04-25 00:34:34|  NULL|    NULL|    WRITE|                         [mode -> ErrorIfExists, partitionBy -> []]|NULL|    NULL|     NULL|       NULL|          NULL|         true|

Hier sehen Sie alle Änderungen, die über die obigen Codeausschnitte vorgenommen wurden.

## <a name="read-older-versions-of-data-using-time-travel"></a>Lesen älterer Versionen von Daten mittels Zeitreise

Mithilfe des Zeitreise-Features können Sie ältere Momentaufnahmen Ihrer Delta Lake-Tabelle abfragen. Wenn Sie auf die überschriebenen Daten zugreifen möchten, können Sie mithilfe der Option „versionAsOf“ eine Momentaufnahme der Tabelle abfragen, die erstellt wurde, bevor die ersten Daten überschrieben wurden.

Nach dem Ausführen der folgenden Zelle sollten die ersten Daten vor der Überschreibung angezeigt werden. Die Zeitreise ist ein äußerst praktisches Feature, das sich das Delta Lake-Transaktionsprotokoll zunutze macht, um auf Daten zuzugreifen, die sich nicht mehr in der Tabelle befinden. Wenn Sie die Option für die Version 0 entfernen (oder die Version 1 angeben), werden wieder die neueren Daten angezeigt. Weitere Informationen finden Sie unter [Abfragen einer älteren Momentaufnahme einer Tabelle (Zeitreise)](https://docs.delta.io/latest/delta-batch.html#deltatimetravel).

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Option("versionAsOf", 0).Load(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").option("versionAsOf", 0).load(deltaTablePath)
df.show()
```

::: zone-end

Ergebnis:

| id|
|---|
|  0|
|  1|
|  4|
|  3|
|  2|

Hier sehen Sie wieder die früheste Version der Daten.

## <a name="write-a-stream-of-data-to-a-table"></a>Schreiben eines Datenstroms in eine Tabelle

Schreibvorgänge für eine Delta Lake-Tabelle können auch mithilfe des strukturierten Streamings von Spark durchgeführt werden. Durch das Delta Lake-Transaktionsprotokoll wird eine Exactly Once-Verarbeitung garantiert, auch wenn parallel andere Datenströme oder Batchabfragen für die Tabelle aktiv sind. Datenströme werden standardmäßig im Anfügemodus ausgeführt, wodurch der Tabelle neue Datensätze hinzugefügt werden.

Weitere Informationen zur Delta Lake-Integration für strukturiertes Streaming finden Sie unter [Tabelle: Streaming für Lese- und Schreibvorgänge](https://docs.delta.io/latest/delta-streaming.html).

In den folgenden Zellen passiert Folgendes:

* Zelle 30: Anzeigen der neu angefügten Daten
* Zelle 31: Untersuchen des Verlaufs
* Zelle 32: Beenden des Auftrags für strukturiertes Streaming
* Zelle 33: Untersuchen des Verlaufs <--Sie werden sehen, dass das Anfügen beendet wurde.

Richten Sie zunächst einen einfachen Spark-Streamingauftrag ein, um eine Sequenz zu generieren und dafür zu sorgen, dass durch den Auftrag in die Deltatabelle geschrieben wird.

:::zone pivot = "programming-language-python"

```python
streaming_df = spark.readStream.format("rate").load()
stream = streaming_df\
    .selectExpr("value as id")\
    .writeStream\
    .format("delta")\
    .option("checkpointLocation", "/tmp/checkpoint-{0}".format(session_id))\
    .start(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var streamingDf = spark.ReadStream().Format("rate").Load();
var stream = streamingDf.SelectExpr("value as id").WriteStream().Format("delta").Option("checkpointLocation", $"/tmp/checkpoint-{sessionId}").Start(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val streamingDf = spark.readStream.format("rate").load()
val stream = streamingDf.select($"value" as "id").writeStream.format("delta").option("checkpointLocation", s"/tmp/checkpoint-$sessionId").start(deltaTablePath)
```

::: zone-end

## <a name="read-a-stream-of-changes-from-a-table"></a>Lesen eines Datenstrom mit Änderungen aus einer Tabelle

Während der Datenstrom in die Delta Lake-Tabelle schreibt, können Sie diese Tabelle auch als Streamingquelle verwenden und daraus lesen. So können Sie beispielsweise eine andere Streamingabfrage starten, die alle an der Delta Lake-Tabelle vorgenommenen Änderungen ausgibt.

:::zone pivot = "programming-language-python"

```python
delta_table.toDF().sort(col("id").desc()).show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.ToDF().Sort(Col("id").Desc()).Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.toDF.sort($"id".desc).show
```

::: zone-end

Ergebnis:

| id|
|---|
| 19|
| 18|
| 17|
| 16|
| 15|
| 14|
| 13|
| 12|
| 11|
| 10|
|  8|
|  6|
|  4|
|  3|
|  2|
|  1|
|  0|
| -1|
| -1|
| -1|

:::zone pivot = "programming-language-python"

```python
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show
```

::: zone-end

Ergebnis:

|version|          timestamp|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|STREAMING UPDATE|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId -> 0]|          4|
|      4|2020-04-25 00:36:27|           MERGE|                                         [predicate -> (oldData.`id` = newData.`id`)]|          3|
|      3|2020-04-25 00:36:08|          Delete|                  [predicate -> ["((`id` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predicate -> ((id#744L % cast(2 as bigint)) = cast(0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           WRITE|                                               [mode -> Overwrite, partitionBy -> []]|          0|
|      0|2020-04-25 00:34:34|           WRITE|                                           [mode -> ErrorIfExists, partitionBy -> []]|       NULL|

Hier werden einige der weniger interessanten Spalten gelöscht, um die Verlaufsansicht übersichtlicher zu machen:

:::zone pivot = "programming-language-python"

```python
stream.stop()
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(100, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
stream.Stop();
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(100, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
stream.stop
deltaTable.history.show
```

Ergebnis:

|version|          timestamp|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|STREAMING UPDATE|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId -> 0]|          4|
|      4|2020-04-25 00:36:27|           MERGE|                                         [predicate -> (oldData.`id` = newData.`id`)]|          3|
|      3|2020-04-25 00:36:08|          Delete|                  [predicate -> ["((`id` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predicate -> ((id#744L % cast(2 as bigint)) = cast(0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           WRITE|                                               [mode -> Overwrite, partitionBy -> []]|          0|
|      0|2020-04-25 00:34:34|           WRITE|                                           [mode -> ErrorIfExists, partitionBy -> []]|       NULL|

::: zone-end

## <a name="convert-parquet-to-delta"></a>Konvertieren von Parquet in Delta

Daten können direkt vom Parquet-Format in das Deltaformat konvertiert werden.

Hier wird getestet, ob die vorhandene Tabelle im Deltaformat vorliegt:
:::zone pivot = "programming-language-python"

```python
parquet_path = "/parquet/parquet-table-{0}".format(session_id)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetPath = $"/parquet/parquet-table-{sessionId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetPath = s"/parquet/parquet-table-$sessionId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Ergebnis:

Falsch

Hier werden Daten in das Deltaformat konvertiert, und es wird überprüft, ob der Vorgang erfolgreich war:

:::zone pivot = "programming-language-python"

```python
DeltaTable.convertToDelta(spark, "parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
DeltaTable.ConvertToDelta(spark, $"parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
DeltaTable.convertToDelta(spark, s"parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Ergebnis:

Richtig

## <a name="sql-support"></a>SQL-Unterstützung

Von Delta werden tabellenbezogene Hilfsprogrammbefehle über SQL unterstützt. SQL kann für Folgendes verwendet werden:

* Abrufen des Verlaufs einer Deltatabelle
* Bereinigen einer Deltatabelle
* Konvertieren einer Parquet-Datei in Delta

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE HISTORY delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"DESCRIBE HISTORY delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"DESCRIBE HISTORY delta.`$deltaTablePath`").show()
```

::: zone-end

Ergebnis:

|version|          timestamp|userId|userName|       operation| operationParameters| Auftrag|Notebook|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|----------------|--------------------|----|--------|---------|-----------|--------------|-------------|
|      5|2020-04-25 00:37:09|  NULL|    NULL|STREAMING UPDATE|[outputMode -> Ap...|NULL|    NULL|     NULL|          4|          NULL|         true|
|      4|2020-04-25 00:36:27|  NULL|    NULL|           MERGE|[predicate -> (ol...|NULL|    NULL|     NULL|          3|          NULL|        false|
|      3|2020-04-25 00:36:08|  NULL|    NULL|          Delete|[predicate -> ["(...|NULL|    NULL|     NULL|          2|          NULL|        false|
|      2|2020-04-25 00:35:51|  NULL|    NULL|          UPDATE|[predicate -> ((i...|NULL|    NULL|     NULL|          1|          NULL|        false|
|      1|2020-04-25 00:35:05|  NULL|    NULL|           WRITE|[mode -> Overwrit...|NULL|    NULL|     NULL|          0|          NULL|        false|
|      0|2020-04-25 00:34:34|  NULL|    NULL|           WRITE|[mode -> ErrorIfE...|NULL|    NULL|     NULL|       NULL|          NULL|         true|

:::zone pivot = "programming-language-python"

```python
spark.sql("VACUUM delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"VACUUM delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"VACUUM delta.`$deltaTablePath`").show()
```

::: zone-end

Ergebnis:

|                path|
|--------------------|
|abfss://data@arca...|

Als Nächstes wird die Tabelle überprüft, um sicherzugehen, dass sie nicht im Deltaformat vorliegt. Anschließend wird sie mithilfe von Spark SQL in das Deltaformat konvertiert, und es wird überprüft, ob sie ordnungsgemäß konvertiert wurde:

:::zone pivot = "programming-language-python"

```python
parquet_id = random.randint(0,1000)
parquet_path = "/parquet/parquet-table-{0}-{1}".format(session_id, parquet_path)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
spark.sql("CONVERT TO DELTA parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetId =  (new Random()).Next(10000000);
var parquetPath = $"/parquet/parquet-table-{sessionId}-{parquetId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath);
spark.Sql($"CONVERT TO DELTA parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetId = scala.util.Random.nextInt(1000)
val parquetPath = s"/parquet/parquet-table-$sessionId-$parquetId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
spark.sql(s"CONVERT TO DELTA parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Ergebnis:

Richtig

Eine vollständige Dokumentation finden Sie auf der [Delta Lake-Dokumentationsseite](https://docs.delta.io/latest/delta-intro.html).

Weitere Informationen finden Sie unter [Delta Lake-Projekt](https://github.com/delta-io/delta).

## <a name="next-steps"></a>Nächste Schritte

* [Dokumentation zu .NET für Apache Spark](/dotnet/spark)
* [Azure Synapse Analytics](../index.yml)