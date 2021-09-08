---
title: Migrieren von MongoDB zur Azure Cosmos DB-API für MongoDB mithilfe von Databricks und Spark
description: Erfahren Sie, wie Sie mit Databricks Spark große Datasets von MongoDB-Instanzen zu Azure Cosmos DB migrieren.
author: nayakshweta
ms.author: shwetn
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 3af5168a664fe18f9fc57877aee864f89ffa4d03
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029910"
---
# <a name="migrate-data-from-mongodb-to-an-azure-cosmos-db-api-for-mongodb-account-by-using-azure-databricks"></a>Migrieren von Daten von MongoDB zu einem Azure Cosmos DB-API-Konto für MongoDB mithilfe von Azure Databricks
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Dieser Migrationsleitfaden ist Teil einer Reihe zum Migrieren von Datenbanken von MongoDB zur Azure Cosmos DB-API für MongoDB. Die wichtigen Migrationsschritte umfassen die [Migrationsvorbereitung](pre-migration-steps.md), die Migration und die [Migrationsnachbereitung](post-migration-optimization.md), wie nachfolgend dargestellt.

:::image type="content" source="./media/pre-migration-steps/overall-migration-steps.png" alt-text="Diagramm der Migrationsschritte":::


## <a name="data-migration-using-azure-databricks"></a>Datenmigration mithilfe von Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) ist ein PaaS-Angebot (Platform as a Service) für [Apache Spark](https://spark.apache.org/). Es bietet eine Möglichkeit, Offlinemigrationsvorgänge für ein umfangreiches Dataset durchzuführen. Sie können Azure Database für die Offlinemigration von Datenbanken in MongoDB zur Azure Cosmos DB-API für MongoDB durchzuführen.

In diesem Tutorial lernen Sie Folgendes:

- Bereitstellen eines Azure Databricks-Clusters

- Hinzufügen von Abhängigkeiten

- Erstellen und Ausführen eines Scala- oder Python-Notebooks

- Optimieren der Migrationsleistung

- Beheben von Problemen bei der Ratenbegrenzung, die während der Migration auftreten können

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- [Führen Sie die Schritte zur Migrationsvorbereitung](pre-migration-steps.md) aus. Schätzen Sie beispielsweise den Durchsatz, und wählen Sie einen Shardschlüssel aus.
- [Erstellen Sie ein Konto für die Azure Cosmos DB-API für MongoDB.](https://ms.portal.azure.com/#create/Microsoft.DocumentDB)

## <a name="provision-an-azure-databricks-cluster"></a>Bereitstellen eines Azure Databricks-Clusters

Nutzen Sie die Anweisungen zum [Bereitstellen eines Azure Databricks-Clusters](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Wir empfehlen die Auswahl der Version 7.6 der Databricks-Runtime, die Spark 3.0 unterstützt.

:::image type="content" source="./media/migrate-databricks/databricks-cluster-creation.png" alt-text="Diagramm der Erstellung eines neuen Databricks-Clusters":::


## <a name="add-dependencies"></a>Hinzufügen von Abhängigkeiten

Fügen Sie dem Cluster die MongoDB-Connectorbibliothek für Spark hinzu, um eine Verbindung mit nativen MongoDB-Endpunkten sowie mit Endpunkten der Azure Cosmos DB-API für MongoDB herzustellen. Wählen Sie in Ihrem Cluster **Bibliotheken** > **Neue installieren** > **Maven** aus, und fügen Sie dann `org.mongodb.spark:mongo-spark-connector_2.12:3.0.1` als Maven-Koordinaten hinzu.

:::image type="content" source="./media/migrate-databricks/databricks-cluster-dependencies.png" alt-text="Diagramm des Hinzufügens von Databricks-Clusterabhängigkeiten":::


Wählen Sie **Installieren** aus, und starten Sie den Cluster nach Abschluss der Installation neu.

> [!NOTE]
> Starten Sie den Databricks-Cluster neu, nachdem die MongoDB-Connectorbibliothek für Spark installiert wurde.

Danach können Sie ein Scala- oder Python-Notebook für die Migration erstellen.


## <a name="create-scala-notebook-for-migration"></a>Erstellen eines Scala-Notebooks für die Migration

Erstellen eines Scala Notebooks in Databricks. Stellen Sie sicher, die richtigen Werte für die Variablen einzugeben, bevor Sie den folgenden Code ausführen:


```scala
import com.mongodb.spark._
import com.mongodb.spark.config._
import org.apache.spark._
import org.apache.spark.sql._

var sourceConnectionString = "mongodb://<USERNAME>:<PASSWORD>@<HOST>:<PORT>/<AUTHDB>"
var sourceDb = "<DBNAME>"
var sourceCollection =  "<COLLECTIONNAME>"
var targetConnectionString = "mongodb://<ACCOUNTNAME>:<PASSWORD>@<ACCOUNTNAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@<ACCOUNTNAME>@"
var targetDb = "<DBNAME>"
var targetCollection =  "<COLLECTIONNAME>"

val readConfig = ReadConfig(Map(
  "spark.mongodb.input.uri" -> sourceConnectionString,
  "spark.mongodb.input.database" -> sourceDb,
  "spark.mongodb.input.collection" -> sourceCollection,
))

val writeConfig = WriteConfig(Map(
  "spark.mongodb.output.uri" -> targetConnectionString,
  "spark.mongodb.output.database" -> targetDb,
  "spark.mongodb.output.collection" -> targetCollection,
  "spark.mongodb.output.maxBatchSize" -> "8000"  
))

val sparkSession = SparkSession
  .builder()
  .appName("Data transfer using spark")
  .getOrCreate()

val customRdd = MongoSpark.load(sparkSession, readConfig)

MongoSpark.save(customRdd, writeConfig)
```

## <a name="create-python-notebook-for-migration"></a>Erstellen eines Python-Notebooks für die Migration

Erstellen Sie ein Python-Notebook in Databricks. Stellen Sie sicher, die richtigen Werte für die Variablen einzugeben, bevor Sie den folgenden Code ausführen:


```python
from pyspark.sql import SparkSession

sourceConnectionString = "mongodb://<USERNAME>:<PASSWORD>@<HOST>:<PORT>/<AUTHDB>"
sourceDb = "<DBNAME>"
sourceCollection =  "<COLLECTIONNAME>"
targetConnectionString = "mongodb://<ACCOUNTNAME>:<PASSWORD>@<ACCOUNTNAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@<ACCOUNTNAME>@"
targetDb = "<DBNAME>"
targetCollection =  "<COLLECTIONNAME>"

my_spark = SparkSession \
    .builder \
    .appName("myApp") \
    .getOrCreate()

df = my_spark.read.format("com.mongodb.spark.sql.DefaultSource").option("uri", sourceConnectionString).option("database", sourceDb).option("collection", sourceCollection).load()

df.write.format("mongo").mode("append").option("uri", targetConnectionString).option("maxBatchSize",2500).option("database", targetDb).option("collection", targetCollection).save()
```

## <a name="optimize-the-migration-performance"></a>Optimieren der Migrationsleistung

Die Migrationsleistung kann mithilfe der folgenden Konfigurationen angepasst werden:

- **Anzahl von Workern und Kernen im Spark-Cluster:** Mehr Worker bedeuten auch mehr Serverknoten zum Ausführen von Tasks.

- **maxBatchSize:** Der `maxBatchSize`-Wert steuert die Rate, mit der Daten in der Azure Cosmos DB-Zielsammlung gespeichert werden. Wenn maxBatchSize für den Sammlungsdurchsatz jedoch zu hoch ist, kann dies zu Fehlern durch [Ratenbegrenzungen](prevent-rate-limiting-errors.md) führen.

  Sie müssen die Anzahl der Worker und maxBatchSize an die Anzahl der Executors im Spark-Cluster, ggf. an die Größe (und damit die RU-Kosten) der einzelnen geschriebenen Dokumente und an die Durchsatzgrenzwerte der Zielsammlung anpassen.

  >[!TIP]
  >maxBatchSize = Sammlungsdurchsatz / (RU-Kosten für 1 Dokument \* Anzahl von Spark-Workern \* Anzahl von CPU-Kernen pro Worker)

- **MongoDB-Spark-Partitionierer und partitionKey:** Standardmäßig wird als Partitionierer MongoDefaultPartitioner verwendet, und der Standardwert für partitionKey lautet „_id“. Den Partitionierer können Sie ändern, indem Sie der Eingabekonfigurationseigenschaft `spark.mongodb.input.partitioner` den Wert `MongoSamplePartitioner` zuweisen. Ebenso kann partitionKey geändert werden, indem der Eingabekonfigurationseigenschaft `spark.mongodb.input.partitioner.partitionKey` der entsprechende Feldname zugewiesen wird. Ein geeigneter partitionKey kann dazu beitragen, Datenschiefe zu vermeiden (große Anzahl von geschriebenen Datensätzen für denselben Shardschlüsselwert).

- **Deaktivieren von Indizes während der Datenübertragung:** Erwägen Sie bei großen Datenmengen die Deaktivierung von Indizes während der Migration, insbesondere Platzhalterindizes für die Zielsammlung. Indizes erhöhen die RU-Kosten für das Schreiben der einzelnen Dokumente. Durch das Freigeben dieser RUs kann die Datenübertragungsrate verbessert werden. Sie können die Indizes aktivieren, nachdem die Daten migriert wurden.



## <a name="troubleshoot"></a>Problembehandlung

### <a name="timeout-error-error-code-50"></a>Timeoutfehler (Fehlercode 50)
Möglicherweise wird der Fehlercode 50 für Vorgänge mit der Cosmos DB-API für eine MongoDB-Datenbank angezeigt. Timeoutfehler können in folgenden Szenarien auftreten:

- **Der zugeordnete Durchsatz der Datenbank ist zu niedrig:** Stellen Sie sicher, dass der Zielsammlung ein ausreichender Durchsatz zugewiesen ist.
- **Übermäßige Datenschiefe bei großen Datenmengen**. Wenn Sie sehr viele Daten in eine bestimmte Tabelle migrieren müssen, aber eine erhebliche Schiefe in den Daten vorliegt, kann es trotzdem zu einer Ratenbegrenzung kommen, selbst wenn Sie mehrere [RUs](../request-units.md) (Anforderungseinheiten) in Ihrer Tabelle bereitgestellt haben. Anforderungseinheiten werden gleichmäßig auf die physischen Partitionen verteilt, und eine starke Datenschiefe kann einen Engpass bei Anforderungen an einen einzelnen Shard verursachen. Datenschiefe bedeutet, dass eine große Anzahl von Datensätzen denselben Shardschlüsselwert aufweist.

### <a name="rate-limiting-error-code-16500"></a>Ratenbegrenzung (Fehlercode 16500)

Möglicherweise wird der Fehlercode 16500 für Vorgänge mit der Cosmos DB-API für eine MongoDB-Datenbank angezeigt. Hierbei handelt es sich um Ratenbegrenzungsfehler, die bei älteren Konten auftreten können oder bei Konten, bei denen die Funktion für serverseitige Wiederholungen deaktiviert ist.
- **Aktivieren der serverseitigen Wiederholung:** Sie können die Funktion für serverseitige Wiederholungen (Server-Side Retry, SSR) aktivieren, damit der Server Vorgänge mit Ratenbegrenzungsfehlern automatisch wiederholt.



## <a name="post-migration-optimization"></a>Optimierung nach der Migration

Nach der Migration der Daten können Sie eine Verbindung mit Azure Cosmos DB herstellen und die Daten verwalten. Sie können nach der Migration auch weitere Schritte ausführen und beispielsweise die Indizierungsrichtlinie optimieren, die Standardkonsistenzebene aktualisieren oder die globale Verteilung für Ihr Azure Cosmos DB-Konto konfigurieren. Weitere Informationen finden Sie im Artikel zur [Optimierung nach der Migration](post-migration-optimization.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen?
    * Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
    * Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbankworkload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-capacity-planner.md)

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten der Indizierung in der Azure Cosmos DB-API für MongoDB](mongodb-indexing.md)
* [Ermitteln der Gebühr für Anforderungseinheiten für Vorgänge](find-request-unit-charge-mongodb.md)
