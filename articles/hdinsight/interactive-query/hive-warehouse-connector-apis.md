---
title: Hive Warehouse Connector-APIs in Azure HDInsight
description: Enthält eine Beschreibung der verschiedenen APIs von Hive Warehouse Connector.
author: adesh-rao
ms.author: adrao
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/29/2021
ms.openlocfilehash: 4816830feac9aceb34ecfaa701ea0b7aec9c728b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347147"
---
# <a name="hive-warehouse-connector-apis-in-azure-hdinsight"></a>Hive Warehouse Connector-APIs in Azure HDInsight

In diesem Artikel sind alle APIs aufgeführt, die von Hive Warehouse Connector unterstützt werden. Alle unten angegebenen Beispiele werden per spark-shell und Hive Warehouse Connector-Sitzung ausgeführt.

Gehen Sie wie folgt vor, um eine Hive Warehouse Connector-Sitzung zu erstellen:

```scala
import com.hortonworks.hwc.HiveWarehouseSession
val hive = HiveWarehouseSession.session(spark).build()
```

## <a name="prerequisite"></a>Voraussetzung

Schließen Sie die Schritte für das [Hive Warehouse Connector-Setup](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) ab.


## <a name="supported-apis"></a>Unterstützte APIs

- Festlegen der Datenbank:
    ```scala
    hive.setDatabase("<database-name>")
    ```

- Auflisten aller Datenbanken:
    ```scala
    hive.showDatabases()
    ```

- Auflisten aller Tabellen in der aktuellen Datenbank
    ```scala
    hive.showTables()
    ```

- Beschreiben einer Tabelle
    
    ```scala
   // Describes the table <table-name> in the current database
    hive.describeTable("<table-name>")
    ```
    
    ```scala
   // Describes the table <table-name> in <database-name>
    hive.describeTable("<database-name>.<table-name>")
    ```

- Löschen einer Datenbank
    
    ```scala
   // ifExists and cascade are boolean variables
    hive.dropDatabase("<database-name>", ifExists, cascade)
    ```

- Auflisten einer Tabelle in der aktuellen Datenbank
    
    ```scala
    // ifExists and purge are boolean variables
    hive.dropTable("<table-name>", ifExists, purge)
    ```

- Erstellen einer Datenbank
    ```scala
   // ifNotExists is boolean variable
    hive.createDatabase("<database-name>", ifNotExists)
    ```

- Erstellen einer Tabelle in der aktuellen Datenbank
    ```scala
   // Returns a builder to create table
    val createTableBuilder = hive.createTable("<table-name>")
    ```
    
    Für den create-table-Generator werden nur die folgenden Vorgänge unterstützt: 
    
    ```scala
   // Create only if table does not exists already
    createTableBuilder = createTableBuilder.ifNotExists()
    ```
    
    ```scala
   // Add columns
    createTableBuilder = createTableBuilder.column("<column-name>", "<datatype>")
    ```
    
    ```scala
    // Add partition column
    createTableBuilder = createTableBuilder.partition("<partition-column-name>", "<datatype>")
    ```
    ```scala
   // Add table properties
    createTableBuilder = createTableBuilder.prop("<key>", "<value>")
    ```
    ```scala
    // Creates a bucketed table,
    // Parameters are numOfBuckets (integer) followed by column names for bucketing
    createTableBuilder = createTableBuilder.clusterBy(numOfBuckets, "<column1>", .... , "<columnN>")
    ```
    
    ```scala
    // Creates the table
    createTableBuilder.create()
    ```

    > [!NOTE]
    > Bei dieser API wird eine Tabelle mit ORC-Formatierung an einem Standardspeicherort erstellt. Verwenden Sie die `executeUpdate`-API für andere Features bzw. Optionen oder zum Erstellen einer Tabelle mit Hive-Abfragen.


- Lesen einer Tabelle

    ```scala
   // Returns a Dataset<Row> that contains data of <table-name> in the current database
    hive.table("<table-name>")
    ```

- Ausführen von DDL-Befehlen auf HiveServer2 

    ```scala
    // Executes the <hive-query> against HiveServer2
    // Returns true or false if the query succeeded or failed respectively
    hive.executeUpdate("<hive-query>")
    ```
    
    ```scala
    // Executes the <hive-query> against HiveServer2
    // Throws exception, if propagateException is true and query threw excpetion in HiveServer2
    // Returns true or false if the query succeeded or failed respectively
    hive.executeUpdate("<hive-query>", propagateException) // propagate exception is boolean value
    ```

- Ausführen einer Hive-Abfrage und Laden des Ergebnisses in das Dataset
    
  - Ausführen einer Abfrage über LLAP-Daemons **[Empfohlen]**
    ```scala
    // <hive-query> should be a hive query 
    hive.executeQuery("<hive-query>")
    ```
  - Ausführen einer Abfrage über HiveServer2 per JDBC

    Legen Sie `spark.datasource.hive.warehouse.smartExecution` in der Spark-Konfiguration auf `false` fest, bevor Sie die Spark-Sitzung für die Verwendung dieser API starten.
    ```scala
    hive.execute("<hive-query>")
    ```

- Schließen der Hive Warehouse Connector-Sitzung
    ```scala
    // Closes all the open connections and
    // release resources/locks from HiveServer2
    hive.close()
    ```

- Ausführen einer Hive-MERGE-Abfrage
    
    Bei dieser API wird eine Hive-MERGE-Abfrage im unten angegebenen Format erstellt:
    
    ```
    MERGE INTO <current-db>.<target-table> AS <targetAlias> USING <source expression/table> AS <sourceAlias>
    ON <onExpr>
    WHEN MATCHED [AND <updateExpr>] THEN UPDATE SET <nameValuePair1> ... <nameValuePairN>
    WHEN MATCHED [AND <deleteExpr>] THEN DELETE
    WHEN NOT MATCHED [AND <insertExpr>] THEN INSERT VALUES <value1> ... <valueN>
    ```

    ```scala
    val mergeBuilder = hive.mergeBuilder() // Returns a builder for merge query
    ```
    Der Generator unterstützt die folgenden Vorgänge:
    
    ```scala
    mergeBuilder.mergeInto("<taget-table>", "<targetAlias>")
    ```
    
    ```scala
    mergeBuilder.using("<source-expression/table>", "<sourceAlias>")
    ```
    
    ```scala
    mergeBuilder.on("<onExpr>")
    ```
    
    ```scala
    mergeBuilder.whenMatchedThenUpdate("<updateExpr>", "<nameValuePair1>", ... , "<nameValuePairN>")
    ```
    
    ```scala
    mergeBuilder.whenMatchedThenDelete("<deleteExpr>")
    ```
    
    ```scala
    mergeBuilder.whenNotMatchedInsert("<insertExpr>", "<value1>", ... , "<valueN>");
    ```

    ```scala
    // Executes the merge query
    mergeBuilder.merge()
    ```

- Schreiben eines Datasets in eine Hive-Tabelle per Batchvorgang
    ```scala
    df.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector")
       .option("table", tableName)
       .mode(SaveMode.Type)
       .save()
    ```
   - „TableName“ sollte das Format `<db>.<table>` oder `<table>` haben. Wenn kein Datenbankname angegeben wird, wird die Suche bzw. Erstellung der Tabelle in der aktuellen Datenbank durchgeführt.
    
   - Die SaveMode-Typen lauten:
    
     - Append: Fügt das Dataset an die jeweilige Tabelle an.
    
     - Overwrite: Überschreibt die Daten in der jeweiligen Tabelle durch das Dataset.
    
     - Ignore: Überspringt den Schreibvorgang ohne Fehlerauslösung, falls die Tabelle bereits vorhanden ist.
    
     - ErrorIfExists: Löst einen Fehler aus, falls die Tabelle bereits vorhanden ist.


- Schreiben eines Datasets in eine Hive-Tabelle per HiveStreaming
    ```scala
    df.write.format("com.hortonworks.spark.sql.hive.llap.HiveStreamingDataSource")
       .option("database", databaseName)
       .option("table", tableName)
       .option("metastoreUri", "<HMS_URI>")
    // .option("metastoreKrbPrincipal", principal), add if executing in ESP cluster
       .save()
    
     // To write to static partition
     df.write.format("com.hortonworks.spark.sql.hive.llap.HiveStreamingDataSource")
       .option("database", databaseName)
       .option("table", tableName)
       .option("partition", partition)
       .option("metastoreUri", "<HMS URI>")
    // .option("metastoreKrbPrincipal", principal), add if executing in ESP cluster
       .save()
    ```
    > [!NOTE]
    > Bei Streamschreibvorgängen werden immer Daten angefügt.


- Schreiben eines Spark-Streams in eine Hive-Tabelle
    ```scala
    stream.writeStream
        .format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource")
        .option("metastoreUri", "<HMS_URI>")
        .option("database", databaseName)
        .option("table", tableName)
      //.option("partition", partition) , add if inserting data in partition
      //.option("metastoreKrbPrincipal", principal), add if executing in ESP cluster
        .start()
    ```
