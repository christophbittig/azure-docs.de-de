---
title: Livemigration zu Azure Managed Instance for Apache Cassandra mithilfe von Apache Spark und eines Proxys für duales Schreiben
description: Hier erhalten Sie Informationen zur Migration zu Azure Managed Instance for Apache Cassandra mithilfe von Apache Spark und eines Proxys für duales Schreiben.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 061fd0f1244221aabb6fa37a84924be653fafbaa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032454"
---
# <a name="live-migration-to-azure-managed-instance-for-apache-cassandra-by-using-a-dual-write-proxy"></a>Livemigration zu Azure Managed Instance for Apache Cassandra mithilfe eines Proxys für duales Schreiben

Es wird empfohlen, nach Möglichkeit die native Apache Cassandra-Funktion zur Migration von Daten aus Ihrem vorhandenen Cluster zu Azure Managed Instance for Apache Cassandra zu verwenden, indem Sie einen [Hybridcluster](configure-hybrid-cluster.md) konfigurieren. Diese Funktion verwendet das Gossipprotokoll von Apache Cassandra, um Daten nahtlos aus Ihrem Quellrechenzentrum in Ihr neues Rechenzentrum der verwalteten Instanz zu replizieren. In manchen Szenarios kann es jedoch möglich sein, dass die Version Ihrer Quelldatenbank nicht kompatibel ist oder ein anderer Grund das Einrichten eines Hybridclusters verhindert. 

In diesem Tutorial wird beschrieben, wie Sie Daten live mithilfe eines [Proxys für duales Schreiben](https://github.com/Azure-Samples/cassandra-proxy) und Apache Spark zu Azure Managed Instance for Apache Cassandra migrieren. Diese Methode bietet folgende Vorteile:

- **Minimale Anwendungsänderungen**. Damit der Proxy Verbindungen von Ihrem Anwendungscode akzeptiert, sind wenig bis keine Konfigurationsänderungen nötig. Er wird alle Anforderungen an Ihre Quelldatenbank und Schreibvorgänge asynchron an ein sekundäres Ziel weiterleiten. 
- **Abhängigkeit vom Wire Protocol des Clients**. Da diese Methode nicht von Back-End-Ressourcen oder internen Protokollen abhängig ist, kann sie für jedes Cassandra-Quell- oder -Zielsystem verwendet werden, das das Apache Cassandra-Wire Protocol implementiert.

Die Vorgehensweise ist in der folgenden Abbildung dargestellt.

:::image type="content" source="./media/migration/live-migration.gif" alt-text="Animation, die die Livemigration von Daten zu Azure Managed Instance for Apache Cassandra zeigt." border="false":::

## <a name="prerequisites"></a>Voraussetzungen

* Stellen Sie mithilfe des [Azure-Portals](create-cluster-portal.md) oder der [Azure CLI](create-cluster-cli.md) ein Azure Managed Instance for Apache Cassandra-Cluster bereit. Stellen Sie sicher, dass Sie [über CQLSH eine Verbindung mit Ihrem Cluster herstellen](./create-cluster-portal.md#connecting-to-your-cluster) können.

* [Stellen Sie in Ihrem virtuellen verwalteten Cassandra-Netzwerk ein Azure Databricks-Konto bereit](deploy-cluster-databricks.md). Stellen Sie sicher, dass das Konto über einen Netzwerkzugriff auf Ihren Cassandra-Quellcluster verfügt. In diesem Konto erstellen Sie einen Spark-Cluster zum Laden der Verlaufsdaten.

* Vergewissern Sie sich, dass Sie das Keyspace-/Tabellenschema aus Ihrer Cassandra-Quelldatenbank in die Zieldatenbank Ihrer verwalteten Cassandra-Instanz migriert haben.

## <a name="provision-a-spark-cluster"></a>Bereitstellen eines Spark-Clusters

Es wird empfohlen, Version 7.5 der Azure Databricks-Runtime auszuwählen, die Spark 3.0 unterstützt.

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-runtime.png" alt-text="Screenshot, der die Suche nach der Azure Databricks-Laufzeitversion zeigt.":::

## <a name="add-spark-dependencies"></a>Hinzufügen von Spark-Abhängigkeiten

Sie müssen dem Cluster die Apache Spark-Cassandra-Connectorbibliothek hinzufügen, um eine Verbindung mit nativen und Azure Cosmos DB-Cassandra-Endpunkten herzustellen. Wählen Sie in Ihrem Cluster **Bibliotheken**  >  **Neue**  >  **Maven** installieren und fügen Sie dann `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` in Maven-Koordinaten hinzu.

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-search-packages.png" alt-text="Screenshot, der zeigt, wie Maven-Pakete in Azure Databricks gesucht werden.":::

Wählen Sie **Installieren** aus, und starten Sie den Cluster nach Abschluss der Installation neu.

> [!NOTE]
> Stellen Sie sicher, dass Sie den Azure Databricks-Cluster neu starten, nachdem die Cassandra-Connectorbibliothek installiert wurde.

## <a name="install-the-dual-write-proxy"></a>Installieren Sie den Proxy für duales Schreiben.

Für eine optimale Leistung bei dualen Schreibvorgängen wird empfohlen, den Proxy auf allen Knoten in Ihrem Cassandra-Quellcluster zu installieren.

```bash
#assuming you do not have git already installed
sudo apt-get install git 

#assuming you do not have maven already installed
sudo apt install maven

#clone repo for dual-write proxy
git clone https://github.com/Azure-Samples/cassandra-proxy.git

#change directory
cd cassandra-proxy

#compile the proxy
mvn package
```

## <a name="start-the-dual-write-proxy"></a>Starten des Proxys für duales Schreiben

Es wird empfohlen, den Proxy auf allen Knoten in Ihrem Cassandra-Quellcluster zu installieren. Führen Sie mindestens den folgenden Befehl aus, um den Proxy auf allen Knoten zu starten. Ersetzen Sie `<target-server>` durch eine IP- oder Serveradresse eines der Knoten im Zielcluster. Ersetzen Sie `<path to JKS file>` durch den Pfad zu einer lokalen .jks-Datei und `<keystore password>` durch das entsprechende Kennwort.  

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password>
```

Wenn Sie den Proxy auf diese Weise starten, muss Folgendes zutreffen:

- Quell- und Zielendpunkte besitzen denselben Benutzernamen und dasselbe Kennwort.
- Quell- und Zielendpunkte implementieren Secure Sockets Layer (SSL).

Wenn Ihre Quell- und Zielendpunkte diese Kriterien nicht erfüllen können, finden Sie nachfolgend weitere Konfigurationsoptionen.

### <a name="configure-ssl"></a>Konfigurieren von SSL

Für SSL können Sie entweder einen vorhandenen Keystore implementieren (z. B. den von Ihrem Quellcluster verwendeten), oder Sie können mit `keytool` ein selbstsigniertes Zertifikat erstellen:

```bash
keytool -genkey -keyalg RSA -alias selfsigned -keystore keystore.jks -storepass password -validity 360 -keysize 2048
```
Wenn die Quell- oder Zielendpunkte SSL nicht implementieren, können Sie dieses auch deaktivieren. Verwenden Sie hierzu die Flags `--disable-source-tls` oder `--disable-target-tls`:

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --source-port 9042 --target-port 10350 --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password> --disable-source-tls true  --disable-target-tls true 
```

> [!NOTE]
> Stellen Sie sicher, dass Ihre Clientanwendung beim Erstellen von SSL-Verbindungen mit der Datenbank über den Proxy denselben Keystore und dasselbe Kennwort wie für den Proxy für duales Schreiben verwendet.

### <a name="configure-the-credentials-and-port"></a>Konfigurieren der Anmeldeinformationen und des Ports

Standardmäßig werden die Anmeldeinformationen der Quelle von Ihrer Client-App übergeben. Der Proxy verwendet diese Anmeldeinformationen zum Herstellen einer Verbindung mit den Quell- und Zielclustern. Wie bereits erwähnt, wird bei diesem Prozess davon ausgegangen, dass die Quell- und Zielanmeldeinformationen identisch sind. Bei Bedarf können Sie einen anderen Benutzernamen und ein anderes Kennwort für den Cassandra-Zielendpunkt beim Starten des Proxys separat angeben:

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password>
```

Werden die Quell- und Zielports nicht angegeben, lautet der Standardwert 9042. Werden der Cassandra-Quell- oder Zielendpunkt an einem anderen Port ausgeführt, können Sie mit `--source-port` oder `--target-port` eine andere Portnummer angeben: 

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --source-port 9042 --target-port 10350 --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password>
```

### <a name="deploy-the-proxy-remotely"></a>Remote-Bereitstellung des Proxymoduls

Möglicherweise möchten Sie den Proxy nicht auf den Clusterknoten selbst, sondern auf einem separaten Computer installieren. Geben Sie in diesem Fall die IP-Adresse von `<source-server>` an:

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar <source-server> <destination-server>
```

> [!NOTE]
> Wenn Sie den Proxy in einem nativen Apache Cassandra-Cluster nicht auf allen Knoten installieren und ausführen, wirkt sich dies auf die Leistung Ihrer Anwendung aus. Der Clienttreiber ist nicht in der Lage, für alle Knoten innerhalb des Clusters eine Verbindung zu öffnen. 

### <a name="allow-zero-application-code-changes"></a>Keine Änderungen am Anwendungscode zulassen

Der Proxy lauscht standardmäßig an Port 29042. Der Anwendungscode muss so geändert werden, dass er auf diesen Port verweist. Sie können den Port, an dem der Proxy lauscht, jedoch auch ändern. Dies ist möglicherweise nötig, wenn Sie Codeänderungen auf Anwendungsebene vermeiden möchten, indem Sie:

- den Cassandra-Quellservers auf einem anderen Port ausführen.
- den Proxy auf dem Cassandra-Standardport 9042 ausführen.

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --proxy-port 9042
```

> [!NOTE]
> Für die Installation des Proxys auf den Clusterknoten ist kein Neustart der Knoten erforderlich. Wenn Sie jedoch über viele Anwendungsclients verfügen und den Proxy auf dem Cassandra-Standardport 9042 ausführen, müssen Sie, um Codeänderungen auf Anwendungsebene zu vermeiden, den [Apache Cassandra-Standardport](https://cassandra.apache.org/doc/latest/cassandra/faq/#what-ports-does-cassandra-use) ändern. Anschließend müssen Sie die Knoten in Ihrem Cluster neu starten und den Quellport als den neuen Port konfigurieren, den Sie für Ihren Cassandra-Quellcluster festgelegt haben. 
>
> Im folgenden Beispiel ändern wir den Cassandra-Quellcluster so, dass er auf dem Port 3074 ausgeführt wird, und wir starten den Cluster auf Port 9042:
>
>```bash
>java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --proxy-port 9042 --source-port 3074
>``` 

### <a name="force-protocols"></a>Erzwingen von Protokollen

Der Proxy kann Protokolle erzwingen, die möglicherweise erforderlich sind, wenn der Quellendpunkt moderner ist als der Zielendpunkt, oder wenn der Quellendpunkt anderweitig nicht unterstützt wird. In diesem Fall können Sie `--protocol-version` und `--cql-version` festlegen, um die Übereinstimmung des Protokolls mit dem Zielendpunkt zu erzwingen:

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --protocol-version 4 --cql-version 3.11
```

Wenn der Proxy für duales Schreiben ausgeführt wird, müssen Sie den Port auf Ihrem Anwendungsclient ändern und neu starten. (Oder ändern Sie den Cassandra-Port und starten Sie den Cluster neu, wenn Sie diesen Ansatz gewählt haben.) Der Proxy beginnt dann mit der Weiterleitung von Schreibvorgängen an den Zielendpunkt. Weitere Informationen zur [Überwachung und zu Metriken](https://github.com/Azure-Samples/cassandra-proxy#monitoring) im Proxytool finden Sie hier. 

## <a name="run-the-historical-data-load"></a>Laden der Verlaufsdaten

Erstellen Sie zum Laden der Daten ein Scala-Notebook in Ihrem Azure Databricks-Konto. Ersetzen Sie Ihre Quell- und Zielkonfigurationen für Cassandra durch die entsprechenden Anmeldeinformationen sowie die Quell-und Zielkeyspaces und -tabellen. Fügen Sie im folgenden Beispiel nach Bedarf für jede Tabelle weitere Variablen hinzu, und führen Sie den Code anschließend aus. Nachdem Ihre Anwendung mit dem Senden von Anforderungen an den Proxy für duales Schreiben begonnen hat, können Sie die Verlaufsdaten migrieren. 

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
    "spark.cassandra.connection.ssl.enabled" -> "true",
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
    "spark.cassandra.connection.remoteConnectionsPerExecutor" -> "1",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//set timestamp to ensure it is before read job starts
val timestamp: Long = System.currentTimeMillis / 1000

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
  .option("writetime", timestamp)
  .mode(SaveMode.Append)
  .save
```

> [!NOTE]
> Im vorherigen Scala-Beispiel wird `timestamp` vor dem Lesen der Daten in der Quelltabelle auf den aktuellen Zeitpunkt festgelegt. Anschließend wird `writetime` auf diesen früheren Zeitstempel festgelegt. Dadurch wird sichergestellt, dass Datensätze, die aus den Verlaufsdaten auf den Zielendpunkt geschrieben werden, keine Updates mit einem späteren Zeitstempel aus dem Proxy für duales Schreiben überschreiben können, während die Verlaufsdaten gelesen werden.
>
> Falls Sie jedoch die *genauen* Zeitstempel beibehalten müssen, sollten Sie die Verlaufsdaten auf eine Weise migrieren, bei der die Zeitstempel beibehalten werden, wie etwa in [diesem Beispiel](https://github.com/Azure-Samples/cassandra-migrator). 

## <a name="validate-the-source-and-target"></a>Überprüfen der Quelle und des Ziels

Nachdem das Laden der Verlaufsdaten abgeschlossen ist, sollten Ihre Datenbanken synchron und zur Übernahme bereit sein. Vor der endgültigen Übernahme wird jedoch eine Überprüfung der Quelle und des Ziels empfohlen, um sicherzustellen, dass die Anforderungsergebnisse übereinstimmen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwalten von Azure Managed Instance for Apache Cassandra-Ressourcen mit der Azure CLI](manage-resources-cli.md)
