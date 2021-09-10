---
title: Arbeiten mit der Azure Cosmos DB-Cassandra-API von Spark
description: Dieser Artikel ist die Hauptseite für die Integration der Cosmos DB-Cassandra-API von Spark.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/01/2019
ms.openlocfilehash: f0e695248ecddebb4e737465a639e6385f6d29f0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347193"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Herstellen einer Verbindung mit der Azure Cosmos DB-Cassandra-API von Spark
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Dieser Artikel stammt aus einer Reihe von Artikeln zur Integration der Cosmos DB-Cassandra-API von Spark. In den Artikeln werden die Konnektivität, DDL-Vorgänge (Data Definition Language), grundlegende DML-Vorgänge (Data Manipulation Language) und die erweiterte Integration der Azure Cosmos DB-Cassandra-API von Spark behandelt. 

## <a name="prerequisites"></a>Voraussetzungen
* [Ein Azure Cosmos DB-Cassandra-API-Konto muss vorhanden sein.](manage-data-dotnet.md#create-a-database-account)

* Stellen Sie eine Spark-Umgebung Ihrer Wahl [[Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) | [Azure HDInsight Spark](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md) | Sonstiges] bereit.

## <a name="dependencies-for-connectivity"></a>Abhängigkeiten für die Konnektivität
* **Spark-Connector für Cassandra**: Der Spark-Connector wird verwendet, um eine Verbindung mit der Azure Cosmos DB-Cassandra-API herzustellen.  Identifizieren und verwenden Sie die Version des Connectors unter [Maven > Central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector), die mit den Spark- und Scala-Versionen Ihrer Spark-Umgebung kompatibel ist. Wir empfehlen eine Umgebung, die Spark 3.0 oder höher unterstützt, und den Spark-Connector, der unter den Maven-Koordinaten `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` verfügbar ist. Bei Verwendung von Spark 2.x wird eine Umgebung mit Spark-Version 2.4.5 empfohlen, in der der Spark-Connector mit den Maven-Koordinaten `com.datastax.spark:spark-cassandra-connector_2.11:2.4.3` verwendet wird.


* **Azure Cosmos DB Hilfsbibliothek für Cassandra-API:** Wenn Sie eine Version von Spark 2.x verwenden, benötigen Sie zusätzlich zum Spark-Connector eine weitere Bibliothek namens [azure-cosmos-cassandra-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.2.0/jar) mit den Maven-Koordinaten `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.2.0` aus Azure Cosmos DB, um eine [Ratenbegrenzung](./scale-account-throughput.md#handling-rate-limiting-429-errors) zu bewerkstelligen. Diese Bibliothek enthält benutzerdefinierte Klassen für die Verbindungsfactory und Wiederholungsrichtlinie.

  Die Wiederholungsrichtlinie in Azure Cosmos DB ist für die Verarbeitung von Ausnahmen mit dem HTTP-Statuscode 429 („Anforderungsrate ist groß“) konfiguriert. Die Azure Cosmos DB-Cassandra-API übersetzt diese Ausnahmen in Überladungsfehler im nativen Cassandra-Protokoll, und Sie können mit Backoffs wiederholen. Da Azure Cosmos DB das bereitgestellte Durchsatzmodell verwendet, treten Ausnahmen in Bezug auf große Anforderungsraten auf, wenn die Rate der eingehenden/ausgehenden Daten steigt. Durch die Wiederholungsrichtlinie werden Ihre Spark-Aufträge vor Datenspitzen geschützt, die den für Ihren Container zugeordneten Durchsatz vorübergehend überschreiten. Wenn Sie den Spark 3.x-Connector verwenden, ist die Implementierung dieser Bibliothek nicht erforderlich. 

  > [!NOTE] 
  > Die Wiederholungsrichtlinie kann Ihre Spark-Aufträge nur vor temporären Spitzen schützen. Wenn Sie nicht genügend RUs konfiguriert haben, die zum Ausführen Ihrer Workloads erforderlich sind, ist die Wiederholungsrichtlinie nicht anwendbar, und die Richtlinienklasse löst erneut die Ausnahme aus.

* **Verbindungsdetails zum Azure Cosmos DB-Konto**: Der Name Ihres Azure Cassandra-API-Kontos, der Kontoendpunkt und der Schlüssel.

## <a name="optimizing-spark-connector-throughput-configuration"></a>Optimierten der Durchsatzkonfiguration für den Spark-Connector 

Im nächsten Abschnitt sind alle relevanten Parameter zum Steuern des Durchsatzes bei Verwendung des Spark-Connectors für Cassandra aufgeführt. Zum Optimieren der Parameter für einen maximalen Durchsatz von Spark-Aufträgen müssen die Konfigurationen `spark.cassandra.output.concurrent.writes`, `spark.cassandra.concurrent.reads` und `spark.cassandra.input.reads_per_sec` ordnungsgemäß konfiguriert werden, um zu hohe Drosselung und Backoff zu vermeiden (was wiederum zu einem geringeren Durchsatz führen kann).

Der optimale Wert dieser Konfigurationen hängt von vier Faktoren ab:

-   Die Größe des Durchsatzes (Anforderungseinheiten), der für die Tabelle konfiguriert ist, in der Daten erfasst werden.
- Die Anzahl der Worker im Spark-Cluster.
-   Die Anzahl der für den Spark-Auftrag konfigurierten Executors (die abhängig von der Spark-Version mit `spark.cassandra.connection.connections_per_executor_max` oder `spark.cassandra.connection.remoteConnectionsPerExecutor` gesteuert werden können).
-   Die durchschnittliche Latenz der einzelnen Anforderungen an Cosmos DB, wenn Sie sich im selben Rechenzentrum befinden. Als Wert dafür kann 10 ms für Schreibvorgänge und 3 ms für Lesevorgänge angenommen werden.

Wenn beispielsweise 5 Worker vorhanden sind und der Wert `spark.cassandra.output.concurrent.writes` = 1 sowie der Wert `spark.cassandra.connection.remoteConnectionsPerExecutor` = 1 ist, gibt es 5 Worker, die gleichzeitig in die Tabelle schreiben, mit jeweils einem Thread. Wenn ein einzelner Schreibvorgang 10 ms dauert, können pro Thread 100 Anforderungen (1000 Millisekunden geteilt durch 10) pro Sekunde gesendet werden. Bei fünf Workern wären dies 500 Schreibvorgänge pro Sekunde. Bei durchschnittlichen Kosten von 5 Anforderungseinheiten (Request Units, RUs) pro Schreibvorgang müssen für die Zieltabelle mindestens 2.500 Anforderungseinheiten (5 RUs x 500 Schreibvorgänge pro Sekunde) bereitgestellt werden.

Eine Erhöhung der Anzahl der Executors kann die Anzahl der Threads in einem bestimmten Auftrag erhöhen, was wiederum den Durchsatz erhöhen kann. Die genauen Auswirkungen können jedoch vom jeweiligen Auftrag abhängen. Demgegenüber ist die Steuerung des Durchsatzes über die Anzahl der Worker deterministischer. Sie können auch die genauen Kosten einer bestimmten Anforderung ermitteln, indem Sie ein Anfroderungsprofil erstellen, um die RU-Gebühr zu erhalten. Auf diese Weise können Sie bei der Bereitstellung des Durchsatzes für Ihre Tabelle oder Ihren Keyspace die Genauigkeit erhöhen. In diesem [Artikel](./find-request-unit-charge-cassandra.md) erfahren Sie, wie Sie RU-Gebühren auf Anforderungsebene ermitteln. 

### <a name="scaling-throughput-in-the-database"></a>Skalieren des Durchsatzes in der Datenbank

Der Cassandra Spark-Connector schöpft den Durchsatz in Azure Cosmos DB sehr effizient aus. Daher müssen Sie auch bei effektiven Wiederholungen sicherstellen, dass ausreichend Durchsatz (RUs) auf Tabellen- oder Keyspaceebene bereitgestellt wird, um Ratenbegrenzungsfehler zu vermeiden. Die Mindesteinstellung von 400 RUs in einer Tabelle oder einem Keyspace ist nicht ausreichend. Selbst bei der Mindestkonfigurationseinstellung für den Durchsatz kann der Spark-Connector mit einer Rate von ca. **6.000 Anforderungseinheiten** oder mehr schreiben.

Wenn für die Datenverschiebung mit Spark eine höhere RU-Einstellung benötigt wird als für Ihre Workload im aktiven Zustand erforderlich ist, können Sie den Durchsatz in Azure Cosmos DB problemlos systematisch auf- und abskalieren, um die Anforderungen Ihrer Workload für einen bestimmten Zeitraum zu erfüllen. Lesen Sie den Artikel zur [elastischen Skalierung in der Cassandra-API](scale-account-throughput.md), um die verschiedenen Optionen für die programmgesteuerte und dynamische Skalierung zu verstehen. 

> [!NOTE]
> In der obigen Anleitung wird von einer relativ einheitlichen Verteilung der Daten ausgegangen. Wenn die Daten stark abweichen (d. h. die Anzahl von Lese-/Schreibvorgängen für denselben Partitionsschlüsselwert übermäßig groß ist), treten möglicherweise auch dann, wenn eine große Anzahl von [Anforderungseinheiten](../request-units.md) in der Tabelle bereitgestellt wird, weiterhin Engpässe auf. Anforderungseinheiten werden gleichmäßig auf die physischen Partitionen verteilt werden und eine starke Datenschiefe kann einen Engpass bei Anforderungen an eine einzelne Partition verursachen.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Parameter zur Konfiguration des Durchsatzes für den Spark-Connector

Die folgende Tabelle enthält die für die Azure Cosmos DB-Cassandra-API spezifischen Durchsatzkonfigurationsparameter, die vom Connector bereitgestellt werden. Eine detaillierte Liste aller Konfigurationsparameter finden Sie im GitHub-Repository für den Cassandra-Connector für Spark auf der Seite [Konfigurationsreferenz](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md).

| **Eigenschaftenname** | **Standardwert** | **Beschreibung** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |Anzahl der Zeilen pro Batch. Legen Sie diesen Parameter auf „1“ fest. Dieser Parameter wird verwendet, um einen höheren Durchsatz für umfassende Workloads zu erzielen. |
| spark.cassandra.connection.connections_per_executor_max (Spark 2.x) spark.cassandra.connection.remoteConnectionsPerExecutor (Spark 3.x)  | Keine | Maximale Anzahl von Verbindungen pro Knoten pro Executor. 10 x n entspricht 10 Verbindungen pro Knoten in einem Cassandra-Cluster mit n Knoten. Wenn Sie also 5 Verbindungen pro Knoten pro Executor für einen Cassandra-Cluster mit 5 Knoten benötigen, sollten Sie diese Konfiguration auf „25“ festlegen. Ändern Sie diesen Wert basierend auf dem Parallelitätsgrad oder der Anzahl von Executors, für die Ihre Spark-Aufträge konfiguriert sind.   |
| spark.cassandra.output.concurrent.writes  |  100 | Definiert die Anzahl von parallelen Schreibvorgängen, die pro Executor auftreten können. Da Sie „batch.size.rows“ auf „1“ festlegen, skalieren Sie diesen Wert entsprechend hoch. Ändern Sie diesen Wert basierend auf dem Parallelitätsgrad oder dem Durchsatz, den Sie für Ihren Workload erzielen möchten. |
| spark.cassandra.concurrent.reads |  512 | Definiert die Anzahl der parallelen Lesevorgänge, die pro Executor auftreten können. Ändern Sie diesen Wert basierend auf dem Parallelitätsgrad oder dem Durchsatz, den Sie für Ihren Workload erzielen möchten.  |
| spark.cassandra.output.throughput_mb_per_sec  | Keine | Definiert den gesamten Schreibdurchsatz pro Executor. Dieser Parameter kann als oberer Grenzwert für den Durchsatz Ihres Spark-Auftrags verwendet werden und basiert auf dem bereitgestellten Durchsatz Ihres Cosmos-Containers.   |
| spark.cassandra.input.reads_per_sec| Keine   | Definiert den gesamten Lesedurchsatz pro Executor. Dieser Parameter kann als oberer Grenzwert für den Durchsatz Ihres Spark-Auftrags verwendet werden und basiert auf dem bereitgestellten Durchsatz Ihres Cosmos-Containers.  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | Definiert die Anzahl der Batches pro Spark-Task, die In-Memory gespeichert werden können, bevor diese an die Cassandra-API gesendet werden. |
| spark.cassandra.connection.keep_alive_ms | 60000 | Definiert den Zeitraum, in dem nicht verwendete Verbindungen zur Verfügung stehen. | 

Passen Sie den Durchsatz und den Parallelitätsgrad dieser Parameter basierend auf der Workload an, die Sie für Ihre Spark-Aufträge erwarten, sowie dem Durchsatz, den Sie für Ihr Cosmos DB-Konto bereitgestellt haben.


## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Herstellen einer Verbindung mit der Azure Cosmos DB-Cassandra-API von Spark

### <a name="cqlsh"></a>cqlsh
Die folgenden Befehle verdeutlichen, wie eine Verbindung mit der Azure Cosmos DB-Cassandra-API über cqlsh hergestellt werden kann.  Dies ist nützlich für die Validierung, während Sie die Beispiele in Spark durchgehen.<br>
**Auf Linux/Unix/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1.  Azure Datenziegel
Im folgenden Artikel werden die Azure Databricks-Clusterbereitstellung, die Clusterkonfiguration für die Verbindung mit der Azure Cosmos DB-Cassandra-API sowie einige Beispielnotebooks behandelt, die u.a. DDL- und DML-Vorgänge abdecken.<BR>
[Zugreifen auf Azure Cosmos DB-Cassandra-API-Daten von Azure Databricks](spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2. Azure HDInsight Spark
Im folgenden Artikel werden der HDinsight Spark-Dienst, die Bereitstellung, die Clusterkonfiguration für die Verbindung mit der Azure Cosmos DB-Cassandra-API sowie einige Beispielnotebooks behandelt, die u.a. DDL- und DML-Vorgänge abdecken.<BR>
[Zugreifen auf die Azure Cosmos DB-Cassandra-API über Spark in YARN mit HDInsight](spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3.  Spark-Umgebung im Allgemeinen
Die obigen Abschnitte gelten speziell für Azure Spark-basierte PaaS-Dienste. In diesem Abschnitt wird ein allgemeiner Überblick über die Spark-Umgebung vermittelt.  Nachfolgend werden Connectorabhängigkeiten, Importe und die Spark-Sitzungskonfiguration beschrieben. Der Abschnitt „Nächste Schritte“ enthält Codebeispiele für DDL- und DML-Vorgänge und vieles mehr.  

#### <a name="connector-dependencies"></a>Connectorabhängigkeiten:

1. Fügen Sie die Maven-Koordinaten zum Abrufen des [Cassandra-Connectors für Spark](connect-spark-configuration.md#dependencies-for-connectivity) hinzu.
2. Fügen Sie die Maven-Koordinaten für die [Bibliothek für das Azure Cosmos DB-Hilfsprogramm](connect-spark-configuration.md#dependencies-for-connectivity) für die Cassandra-API hinzu.

#### <a name="imports"></a>Importe:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Spark-Sitzungskonfiguration:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
//spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10") // Spark 2.x
spark.conf.set("spark.cassandra.connection.remoteConnectionsPerExecutor", "10") // Spark 3.x
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln wird die Spark-Integration mit der Azure Cosmos DB-Cassandra-API behandelt. 
 
* [DDL-Vorgänge](spark-ddl-operations.md)
* [Erstellungs-/Einfügevorgänge](spark-create-operations.md)
* [Dient zum Lesen von Vorgängen.](spark-read-operation.md)
* [Upsertvorgänge](spark-upsert-operations.md)
* [Löschvorgänge](spark-delete-operation.md)
* [Aggregationsvorgänge](spark-aggregation-operations.md)
* [Tabellenkopiervorgänge an der Azure Cosmos DB-Cassandra-API von Spark aus](spark-table-copy-operations.md)