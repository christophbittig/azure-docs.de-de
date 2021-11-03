---
title: Migrieren zu Azure Managed Instance for Apache Cassandra mit Apache Spark
description: Erfahren Sie, wie Sie mit Apache Spark zu Azure Managed Instance for Apache Cassandra migrieren.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7c061f808dfbe6b93d9fd6a9266c152fa5aecf57
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005250"
---
# <a name="migrate-to-azure-managed-instance-for-apache-cassandra-using-apache-spark"></a>Migrieren zu Azure Managed Instance for Apache Cassandra mit Apache Spark

Es wird empfohlen, nach Möglichkeit die native Apache Cassandra-Replikation zu verwenden, um Daten aus Ihrem vorhandenen Cluster zu Azure Managed Instance for Apache Cassandra zu migrieren, indem Sie einen [Hybridcluster](configure-hybrid-cluster.md) konfigurieren. Bei diesem Ansatz wird das Gossip-Protokoll von Apache Cassandra verwendet, um Daten aus Ihrem Quellrechenzentrum in Ihr neues Rechenzentrum der verwalteten Instanz zu replizieren. Es kann jedoch einige Szenarien geben, in denen Ihre Quelldatenbankversion nicht kompatibel ist oder ein Hybridclustersetup andernfalls nicht möglich ist. 

In diesem Tutorial wird beschrieben, wie Sie Daten mithilfe des Cassandra Spark-Connectors und Azure Databricks für Apache Spark offline zu Azure Managed Instance for Apache Cassandra migrieren.

## <a name="prerequisites"></a>Voraussetzungen

* Stellen Sie über das [Azure-Portal](create-cluster-portal.md) oder die [Azure CLI](create-cluster-cli.md) einen Cluster für Azure Managed Instance for Apache Cassandra bereit, und stellen Sie sicher, dass Sie mit [CQLSH eine Verbindung mit Ihrem Cluster herstellen können](./create-cluster-portal.md#connecting-to-your-cluster).

* [Stellen Sie in Ihrem verwalteten Cassandra-VNET ein Azure Databricks-Konto bereit](deploy-cluster-databricks.md). Stellen Sie sicher, dass es auch Netzwerkzugriff auf Ihren Cassandra-Quellcluster hat.

* Vergewissern Sie sich, dass Sie das Keyspace-/Tabellenschema bereits von Ihrer Cassandra-Quelldatenbank in Ihre Zieldatenbank der verwalteten Cassandra-Instanz migriert haben.

## <a name="provision-an-azure-databricks-cluster"></a>Bereitstellen eines Azure Databricks-Clusters

Wir empfehlen die Auswahl der Version 7.5 der Databricks-Runtime, die Spark 3.0 unterstützt.

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-runtime.png" alt-text="Screenshot, der die Suche nach der Databricks-Laufzeitversion zeigt.":::

## <a name="add-dependencies"></a>Hinzufügen von Abhängigkeiten

Fügen Sie dem Cluster die Apache Spark-Cassandra-Connectorbibliothek hinzu, um eine Verbindung mit nativen und Azure Cosmos DB-Cassandra-Endpunkten herzustellen. Wählen Sie in Ihrem Cluster **Bibliotheken**  >  **Neue**  >  **Maven** installieren und fügen Sie dann `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` in Maven-Koordinaten hinzu.

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-search-packages.png" alt-text="Screenshot, der zeigt, wie Maven-Pakete in Databricks gesucht werden.":::

Wählen Sie **Installieren** aus, und starten Sie den Cluster nach Abschluss der Installation neu.

> [!NOTE]
> Stellen Sie sicher, dass Sie den Databricks-Cluster neu starten, nachdem die Cassandra-Connectorbibliothek installiert wurde.

## <a name="create-scala-notebook-for-migration"></a>Erstellen eines Scala-Notebooks für die Migration

Erstellen eines Scala Notebooks in Databricks. Ersetzen Sie Ihre Quell- und Zielkonfigurationen für Cassandra durch die entsprechenden Anmeldeinformationen und die Quell-und Zielkeyspaces und -tabellen. Führen Sie dann den folgenden Code aus:

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val sourceCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val targetCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.connection.remoteConnectionsPerExecutor" -> "10",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from source Cassandra
val DFfromSourceCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(sourceCassandra)
  .load
  
//Write to target Cassandra
DFfromSourceCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(targetCassandra)
  .mode(SaveMode.Append) // only required for Spark 3.x
  .save
```

> [!NOTE]
> Wenn Sie die `writetime` der einzelnen Zeilen beibehalten oder zurückdatieren müssen, finden Sie weitere Informationen im Artikel zur [Livemigration](dual-write-proxy-migration.md). 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwalten von Azure Managed Instance for Apache Cassandra-Ressourcen mit der Azure CLI](manage-resources-cli.md)
