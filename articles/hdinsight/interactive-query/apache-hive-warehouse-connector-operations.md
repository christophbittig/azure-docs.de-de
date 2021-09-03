---
title: Vom Hive Warehouse Connector unterstützte Apache Spark-Vorgänge in Azure HDInsight
description: Hier werden die verschiedenen Funktionen des Hive Warehouse Connector in Azure HDInsight vorgestellt.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: b876cd702a2398e8dcb0e1a0be5fffce0615e58d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340247"
---
# <a name="apache-spark-operations-supported-by-hive-warehouse-connector-in-azure-hdinsight"></a>Vom Hive Warehouse Connector unterstützte Apache Spark-Vorgänge in Azure HDInsight

In diesem Artikel werden Spark-basierte Vorgänge erläutert, die vom Hive Warehouse Connector (HWC) unterstützt werden. Alle unten gezeigten Beispiele werden über die Apache Spark-Shell ausgeführt.

## <a name="prerequisite"></a>Voraussetzung

Schließen Sie die Schritte für das [Hive Warehouse Connector-Setup](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) ab.

## <a name="getting-started"></a>Erste Schritte

Führen Sie die folgenden Schritte aus, um eine spark-shell-Sitzung zu starten:

1. Verwenden Sie den Befehl [ssh](../hdinsight-hadoop-linux-use-ssh-unix.md), um eine Verbindung mit Ihrem Apache Spark-Cluster herzustellen. Bearbeiten Sie den folgenden Befehl, indem Sie CLUSTERNAME durch den Namen Ihres Clusters ersetzen, und geben Sie den Befehl dann ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Führen Sie in Ihrer SSH-Sitzung den folgenden Befehl aus, und notieren Sie sich die Version von `hive-warehouse-connector-assembly`:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Bearbeiten Sie den unten angegebenen Code, indem Sie die oben ermittelte Version für `hive-warehouse-connector-assembly` verwenden. Führen Sie anschließend den Befehl aus, um die Spark-Shell zu starten:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Nachdem spark-shell gestartet wurde, kann mit den folgenden Befehlen eine Hive Warehouse Connector-Instanz gestartet werden:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

## <a name="creating-spark-dataframes-using-hive-queries"></a>Erstellen von Spark-Datenrahmen mithilfe von Hive-Abfragen

Die Ergebnisse aller Abfragen mit Verwendung der HWC-Bibliothek werden als Datenrahmen zurückgegeben. In den folgenden Beispielen wird veranschaulicht, wie Sie eine einfache Hive-Abfrage erstellen.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Bei den Ergebnissen der Abfrage handelt es sich um Spark-Datenrahmen, die mit Spark-Bibliotheken wie MLIB und SparkSQL verwendet werden können.

## <a name="writing-out-spark-dataframes-to-hive-tables"></a>Schreiben von Spark-Datenrahmen in Hive-Tabellen

Spark bietet keine native Unterstützung für das Schreiben in verwaltete ACID-Tabellen von Hive. Mithilfe von HWC können Sie jedoch alle Datenrahmen in eine Hive-Tabelle schreiben. Diese Funktionalität wird im folgenden Beispiel veranschaulicht:

1. Erstellen Sie eine Tabelle mit dem Namen `sampletable_colorado`, und geben Sie Spalten dafür an, indem Sie den folgenden Befehl verwenden:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filtern Sie die Tabelle `hivesampletable`, in der die Spalte `state` den Eintrag `Colorado` enthält. Diese Hive-Abfrage gibt einen Spark-Datenrahmen zurück, und das Ergebnis wird mit der Funktion `write` in der Hive-Tabelle `sampletable_colorado` gespeichert.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table","sampletable_colorado").save()
    ```

1. Zeigen Sie die Ergebnisse mit dem folgenden Befehl an:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png" alt-text="Hive Warehouse Connector – Anzeigen der Hive-Tabelle" border="true":::


## <a name="structured-streaming-writes"></a>Schreibvorgänge per strukturiertem Stream

Mit Hive Warehouse Connector können Sie das Spark-Streaming nutzen, um Daten in Hive-Tabellen zu schreiben.

> [!IMPORTANT]
> Strukturierte Streamingschreibvorgänge werden in ESP-fähigen Spark 4.0-Clustern nicht unterstützt.

Mithilfe der folgenden Schritte können Sie Daten aus einem Spark-Stream vom localhost-Port 9999 abrufen und als Hive-Tabelle formatieren (über den Hive Warehouse Connector).

1. Starten Sie in der geöffneten Spark-Shell mit dem folgenden Befehl einen Spark-Stream:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Generieren Sie Daten für den von Ihnen erstellten Spark-Stream, indem Sie die folgenden Schritte ausführen:
    1. Richten Sie im selben Spark-Cluster eine zweite SSH-Sitzung ein.
    1. Geben Sie an der Eingabeaufforderung `nc -lk 9999` ein: Bei diesem Befehl wird das Hilfsprogramm `netcat` verwendet, um Daten über die Befehlszeile an den angegebenen Port zu senden.

1. Kehren Sie zur ersten SSH-Sitzung zurück, und erstellen Sie für die Streamingdaten eine neue Hive-Tabelle. Geben Sie in spark-shell den folgenden Befehl ein:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Schreiben Sie anschließend die Streamingdaten mit dem folgenden Befehl in die neu erstellte Tabelle:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource").option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > Aufgrund eines bekannten Problems in Apache Spark müssen die Optionen `metastoreUri` und `database` derzeit manuell festgelegt werden. Weitere Informationen zu diesem Problem finden Sie unter [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Kehren Sie zur zweiten SSH-Sitzung zurück, und geben Sie die folgenden Werte ein:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Kehren Sie zur ersten SSH-Sitzung zurück, und beachten Sie die kurze Aktivität. Verwenden Sie zum Anzeigen der Daten den folgenden Befehl:

    ```scala
    hive.table("stream_table").show()
    ```

Drücken Sie **STRG+C**, um `netcat` in der zweiten SSH-Sitzung zu beenden. Verwenden Sie `:q`, um spark-shell in der ersten SSH-Sitzung zu beenden.

## <a name="next-steps"></a>Nächste Schritte

* [HWC-Integration mit Apache Spark und Apache Hive](./apache-hive-warehouse-connector.md)
* [Verwenden von Interactive Query mit HDInsight](./apache-interactive-query-get-started.md)
* [HWC-Integration mit Apache Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)
* [APIs mit HWC-Unterstützung](./hive-warehouse-connector-apis.md)
