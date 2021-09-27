---
title: Migrieren von Azure HDInsight 3.6 Apache Storm zu HDInsight 4.0 Apache Spark
description: Die Unterschiede und der Ablauf beim Migrieren von Apache Storm-Workloads zu Spark Streaming oder Spark Structured Streaming
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/16/2019
ms.openlocfilehash: c4f4156f80fac0c9e5eaae360aa937544d88aa9e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656795"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Migrieren von Azure HDInsight 3.6 Apache Storm zu HDInsight 4.0 Apache Spark

In diesem Dokument wird beschrieben, wie Sie Apache Storm-Workloads in HDInsight 3.6 zu HDInsight 4.0 migrieren. Der Apache Storm-Clustertyp wird von HDInsight 4.0 nicht unterstützt, sodass Sie zu einer anderen Datenstreaming-Plattform wechseln müssen. Zwei geeignete Optionen sind Apache Spark Streaming und Spark Structured Streaming. In diesem Dokument werden die Unterschiede zwischen diesen Plattformen beschrieben. Außerdem wird ein Workflow zum Migrieren von Apache Storm-Workloads empfohlen.

## <a name="storm-migration-paths-in-hdinsight"></a>Storm-Migrationspfade in HDInsight

Wenn Sie von Apache Storm in HDInsight 3.6 migrieren möchten, haben Sie mehrere Möglichkeiten:

* Spark Streaming in HDInsight 4.0
* Spark Structured Streaming in HDInsight 4.0
* Azure Stream Analytics

Dieses Dokument enthält eine Anleitung für die Migration von Apache Storm zu Spark Streaming und zu Spark Structured Streaming.

:::image type="content" source="./media/migrate-storm-to-spark/storm-migration-path.png" alt-text="HDInsight Storm-Migrationspfad" border="false":::

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Vergleich zwischen Apache Storm und Spark Streaming, Spark Structured Streaming

Mit Apache Storm lassen sich verschiedene Stufen der garantierten Nachrichtenverarbeitung implementieren. Eine einfache Storm-Anwendung kann beispielsweise die Verarbeitung nach dem „At-Least-Once“-Prinzip garantieren, während [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) Nachrichten nach dem „Exactly-Once“-Prinzip garantiert. Spark Streaming und Spark Structured Streaming gewährleisten, dass alle Eingabeereignisse genau einmal verarbeitet werden, und zwar selbst dann, wenn ein Knotenfehler auftritt. Storm verfügt über ein Modell, durch das jedes einzelne Ereignis verarbeitet wird. Sie können auch das Microbatchmodell mit Trident verwenden. Spark Streaming und Spark Structured Streaming bieten das Microbatch-Verarbeitungsmodell.

|  |Storm |Spark-Streaming | Spark Structured Streaming|
|---|---|---|---|
|**Garantierte Ereignisverarbeitung**|Mindestens einmal <br> „Exactly Once“ (Trident) |[Exactly Once](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Exactly Once](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Verarbeitungsmodell**|Echtzeit <br> Microbatch (Trident) |Microbatch |Microbatch |
|**Unterstützung der Ereigniszeit**|[Ja](https://storm.apache.org/releases/2.0.0/Windowing.html)|Nein|[Ja](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Sprachen**|Java usw.|Scala, Java, Python|Python, R, Scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Spark Streaming und Spark Structured Streaming im Vergleich

Spark Structured Streaming löst Spark Streaming (DStreams) ab. Structured Streaming wird weiterhin erweitert und gewartet, während für DStreams nur der Wartungsmodus aktiviert ist. Structured Streaming bietet nicht so viele Features für die direkte Unterstützung von Quellen und Senken wie DStreams. Daher sollten Sie Ihre Anforderungen genau einschätzen, um die geeignete Option für die Spark-Streamverarbeitung auszuwählen.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Streamingverarbeitung (einzelnes Ereignis) und Microbatchverarbeitung im Vergleich

Storm stellt ein Modell bereit, bei dem jedes einzelne Ereignis verarbeitet wird. Das bedeutet, dass alle eingehenden Datensätze verarbeitet werden, sobald sie empfangen werden. Spark Streaming-Anwendungen müssen einige Sekundenbruchteile warten, bis jeder Microbatch von Ereignissen erfasst ist und bevor dieser Batch zur Verarbeitung übermittelt wird. Im Gegensatz dazu verarbeitet eine ereignisgesteuerte Anwendung jedes Ereignis sofort. Die Spark-Streaming-Latenzzeit liegt in der Regel unter wenigen Sekunden. Die Vorteile des Mikrobatchansatzes sind eine effizientere Datenverarbeitung und eine Vereinfachung aggregierter Berechnungen.

:::image type="content" source="./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png" alt-text="Streaming- und Microbatchverarbeitung" border="false":::

## <a name="storm-architecture-and-components"></a>Storm-Architektur und -Komponenten

Storm-Topologien setzen sich aus mehreren Komponenten zusammen, die in einem gerichteten azyklischen Graph (DAG) angeordnet sind. Daten werden zwischen den Komponenten im Diagramm übertragen. Jede Komponente nutzt einen oder mehrere Datenströme und kann optional einen oder mehrere Datenströme ausgeben kann.

|Komponente |BESCHREIBUNG |
|---|---|
|Spout|Überführt Daten in eine Topologie. Sie geben mindestens einen Datenstrom in die Topologie aus.|
|Bolt|Nutzt Streams, die von Spouts oder anderen Bolts ausgegeben werden. Bolts können optional auch Datenströme in die Topologie ausgeben. Außerdem sind Bolts für das Schreiben von Daten in externe Dienste oder externen Speicher (wie etwa HDFS, Kafka oder HBase) zuständig.|

:::image type="content" source="./media/migrate-storm-to-spark/apache-storm-components.png" alt-text="Interaktion von Storm-Komponenten" border="false":::

Storm umfasst die folgenden drei Daemons, die für die Funktionsfähigkeit des Storm-Clusters sorgen.

|Daemon |BESCHREIBUNG |
|---|---|
|Nimbus|Ist mit dem Hadoop JobTracker vergleichbar und für die Verteilung des Codes im Cluster und die Zuweisung von Tasks zu Computern und die Fehlerüberwachung zuständig.|
|Zookeeper|Wird für die Clusterkoordination verwendet.|
|Supervisor|Lauscht auf Arbeit, die dem zugehörigen Computer zugewiesen wird, und startet und beendet Workerprozesse, wenn er von Nimbus dazu angewiesen wird. Jeder Workerprozess ist für die Ausführung eines Teils der Topologie zuständig. Hier wird die Anwendungslogik des Benutzers (Spouts und Bolt) ausgeführt.|

:::image type="content" source="./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png" alt-text="Nimbus-, ZooKeeper- und Supervisor-Daemons" border="false":::

## <a name="spark-streaming-architecture-and-components"></a>Spark Streaming-Architektur und -Komponenten

In den folgenden Schritten wird zusammengefasst, wie die Komponenten in Spark Streaming (DStreams) und Spark Structured Streaming zusammenarbeiten:

* Beim Start von Spark Streaming startet der Treiber den Task im Executor.
* Der Executor empfängt einen Stream von einer Streamingdatenquelle.
* Wenn der Executor Datenstreams empfängt, teilt er den Stream in Blöcke auf und hält diese im Arbeitsspeicher vor.
* Datenblöcke werden auf andere Executors repliziert.
* Die verarbeiteten Daten werden dann im Zieldatenspeicher gespeichert.

:::image type="content" source="./media/migrate-storm-to-spark/spark-streaming-to-output.png" alt-text="Spark Streaming-Pfad zur Ausgabe" border="false":::

## <a name="spark-streaming-dstream-workflow"></a>Spark Streaming (DStream)-Workflow

Wenn ein Batchintervall verstreicht, wird ein neues RDD erzeugt, das alle Daten dieses Intervalls enthält. Die kontinuierlichen RDD-Sätze werden in einem DStream gesammelt. Wenn das Batchintervall z.B. eine Sekunde lang ist, gibt Ihr DStream jede Sekunde einen Batch aus, der ein RDD mit allen während dieser Sekunde erfassten Daten enthält. Bei der DStream-Verarbeitung tritt das Temperaturereignis in einem dieser Batches auf. Eine Spark-Streaming-Anwendung verarbeitet die Batches, die die Ereignisse enthalten, und führt die endgültigen Aktionen an den Daten durch, die in den einzelnen RDDs gespeichert werden.

:::image type="content" source="./media/migrate-storm-to-spark/spark-streaming-batches.png" alt-text="Spark Streaming-Verarbeitungsbatches" border="false":::

Ausführliche Informationen zu den verschiedenen Transformationen, die mit Spark Streaming verfügbar sind, finden Sie unter [Transformations on DStreams](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams) (DStreams-Transformationen).

## <a name="spark-structured-streaming"></a>Spark Structured Streaming

Bei Spark Structured Streaming wird ein Datenstream in Form einer Tabelle mit unbegrenzter Tiefe dargestellt. Sobald neue Daten eingehen, wird die Tabelle weiter vergrößert. Diese Eingabetabelle wird durch eine Abfrage mit langer Ausführungszeit kontinuierlich verarbeitet, und die Ergebnisse werden an eine Ausgabetabelle gesendet.

In Structured Streaming werden die im System eingehenden Daten sofort in einer Eingabetabelle erfasst. Sie schreiben Abfragen (mit Datenrahmen- und Dataset-API), mit denen Vorgänge für diese Eingabetabelle ausgeführt werden.

Aus der Ausgabe der Abfrage wird eine *Ergebnistabelle* erzeugt, die die Ergebnisse Ihrer Abfrage enthält. Aus der Ergebnistabelle können Daten für einen externen Datenspeicher, z. B. eine relationale Datenbank, abgerufen werden.

Wann Daten aus der Eingabetabelle verarbeitet werden, wird durch das Triggerintervall gesteuert. Standardmäßig ist das Triggerintervall null (0), also versucht Structured Streaming, die Daten sofort bei Eintreffen zu verarbeiten. In der Praxis bedeutet dies, dass Structured Streaming sofort nach Verarbeitung einer Abfrage mit einer weiteren Verarbeitung neu empfangener Daten beginnt. Sie können den Trigger zur Ausführung in einem Intervall konfigurieren, sodass die Streamingdaten in zeitbasierten Batches verarbeitet werden.

:::image type="content" source="./media/migrate-storm-to-spark/structured-streaming-data-processing.png" alt-text="Verarbeiten von Daten in Structured Streaming" border="false":::

:::image type="content" source="./media/migrate-storm-to-spark/structured-streaming-model.png" alt-text="Programmiermodell für Structured Streaming" border="false":::

## <a name="general-migration-flow"></a>Allgemeiner Ablauf der Migration

Bei der empfohlenen Migration von Storm zu Spark wird die folgende Anfangsarchitektur vorausgesetzt:

* Kafka wird als Streamingdatenquelle verwendet.
* Kafka und Storm werden im selben virtuellen Netzwerk bereitgestellt.
* Die von Storm verarbeiteten Daten werden in eine Datensenke geschrieben, z. B. Azure Storage oder Azure Data Lake Storage Gen2.

   :::image type="content" source="./media/migrate-storm-to-spark/presumed-current-environment.png" alt-text="Diagramm der angenommenen aktuellen Umgebung"  border="false":::

Gehen Sie folgendermaßen vor, um Ihre Anwendung von Storm zu einer der Spark Streaming-APIs zu migrieren:

1. **Stellen Sie einen neuen Cluster bereit**. Stellen Sie einen neuen HDInsight 4.0 Spark-Cluster im selben virtuellen Netzwerk bereit. Stellen Sie dann die Spark Streaming- oder Spark Structured Streaming-Anwendung auf dem Cluster bereit, und testen Sie diese gründlich.

   :::image type="content" source="./media/migrate-storm-to-spark/new-spark-deployment.png" alt-text="Neue Spark-Bereitstellung in HDInsight" border="false":::

1. **Stellen Sie die Datennutzung auf dem alten Storm-Cluster ein**. Sorgen Sie dafür, dass auf dem vorhandenen Storm-Cluster keine Daten mehr aus der Streamingdatenquelle genutzt werden, und warten Sie, bis die Daten vollständig in die Zielsenke geschrieben wurden.

   :::image type="content" source="./media/migrate-storm-to-spark/stop-consuming-current-cluster.png" alt-text="Datennutzung auf dem aktuellen Cluster einstellen" border="false":::

1. **Starten Sie die Datennutzung auf dem neuen Spark-Cluster**. Beginnen Sie, Daten von einem neu bereitgestellten HDInsight 4.0 Spark-Cluster zu streamen. Zu diesem Zeitpunkt geht die Datennutzung auf das aktuelle Kafka-Offset über.

   :::image type="content" source="./media/migrate-storm-to-spark/start-consuming-new-cluster.png" alt-text="Datennutzung auf dem neuen Cluster starten" border="false":::

1. **Entfernen Sie ggf. den alten Cluster**. Sobald die Umstellung abgeschlossen ist und alles ordnungsgemäß funktioniert, entfernen Sie ggf. den alten HDInsight 3.6 Storm-Cluster.

   :::image type="content" source="./media/migrate-storm-to-spark/remove-old-clusters1.png" alt-text="Alte HDInsight-Cluster nach Bedarf entfernen" border="false":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Storm, Spark Streaming und Spark Structured Streaming finden Sie in den folgenden Dokumenten:

* [Übersicht über Apache Spark Streaming](../spark/apache-spark-streaming-overview.md)
* [Übersicht über Apache Spark Structured Streaming](../spark/apache-spark-structured-streaming-overview.md)
