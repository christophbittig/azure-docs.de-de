---
title: 'Kafka Connect für Azure Cosmos DB: Senkenconnector'
description: Mit dem Azure Cosmos DB-Senkenconnector können Sie Daten aus Apache Kafka-Themen in eine Azure Cosmos DB-Datenbank exportieren. Der Connector ruft Daten aus Kafka ab, um – basierend auf dem Themenabonnement – in Container in der Datenbank zu schreiben.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: aa4b870a76d9d346116a48633bf3baddc7c44ef4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056689"
---
# <a name="kafka-connect-for-azure-cosmos-db---sink-connector"></a>Kafka Connect für Azure Cosmos DB: Senkenconnector
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Kafka Connect für Azure Cosmos DB ist ein Connector zum Lesen und Schreiben von Daten in Azure Cosmos DB. Mit dem Azure Cosmos DB-Senkenconnector können Sie Daten aus Apache Kafka-Themen in eine Azure Cosmos DB-Datenbank exportieren. Der Connector ruft Daten aus Kafka ab, um – basierend auf dem Themenabonnement – in Container in der Datenbank zu schreiben.

## <a name="prerequisites"></a>Voraussetzungen

* Beginnen Sie mit dem [Confluent Platform-Setup](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md), weil Ihnen diese Plattform eine vollständige Umgebung für Ihre Arbeit bietet. Wenn Sie Confluent Platform nicht verwenden möchten, müssen Sie Zookeeper, Apache Kafka und Kafka Connect selbst installieren und konfigurieren. Außerdem müssen Sie die Azure Cosmos DB-Connectors manuell installieren und konfigurieren.
* Erstellen eines Azure Cosmos DB-Kontos; siehe den [Einrichtungsleitfaden](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md) für Container
* Bash-Shell, die in GitHub Codespaces bzw. unter Mac, Ubuntu und Windows mit WSL2 getestet wird. Diese Shell funktioniert nicht in Cloud Shell oder WSL1.
* Herunterladen von [Java 11 oder höher](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)
* Herunterladen von [Maven](https://maven.apache.org/download.cgi)

## <a name="install-sink-connector"></a>Installieren des Senkenconnectors

Wenn Sie das empfohlene [Confluent Platform-Setup](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md) verwenden, ist der Azure Cosmos DB-Senkenconnector in der Installation enthalten, und Sie können diesen Schritt überspringen.  

Andernfalls können Sie die JAR-Datei aus dem neuesten [Release](https://github.com/microsoft/kafka-connect-cosmosdb/releases) herunterladen oder dieses Repository packen, um eine neue JAR-Datei zu erstellen. Informationen zum manuellen Installieren des Connectors mithilfe der JAR-Datei finden Sie in diesen [Anleitungen](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually). Sie können auch eine neue JAR-Datei aus dem Quellcode packen.

```bash
# clone the kafka-connect-cosmosdb repo if you haven't done so already
git clone https://github.com/microsoft/kafka-connect-cosmosdb.git
cd kafka-connect-cosmosdb

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Kafka Connect installation
ls target/*dependencies.jar
```

## <a name="create-a-kafka-topic-and-write-data"></a>Erstellen eines Kafka-Themas und Schreiben von Daten

Wenn Sie Confluent Platform verwenden, ist die einfachste Möglichkeit zum Erstellen eines Kafka-Themas die Nutzung der bereitgestellten Control Center-Benutzeroberfläche. Andernfalls können Sie ein Kafka-Thema mithilfe der folgenden Syntax manuell erstellen:

```bash
./kafka-topics.sh --create --zookeeper <ZOOKEEPER_URL:PORT> --replication-factor <NO_OF_REPLICATIONS> --partitions <NO_OF_PARTITIONS> --topic <TOPIC_NAME>
```

In diesem Szenario erstellen Sie ein Kafka-Thema namens „hotels“ und schreiben in das Thema JSON-Daten, die nicht in Schemas eingebettet sind. Informationen zum Erstellen eines Themas in Control Center finden Sie im [Confluent-Leitfaden](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-2-create-ak-topics).

Starten Sie als Nächstes den Kafka-Konsolenproducer, um einige Zeilen in das Thema „hotels“ zu schreiben.

```powershell
# Option 1: If using Codespaces, use the built-in CLI utility
kafka-console-producer --broker-list localhost:9092 --topic hotels

# Option 2: Using this repo's Confluent Platform setup, first exec into the broker container
docker exec -it broker /bin/bash
kafka-console-producer --broker-list localhost:9092 --topic hotels

# Option 3: Using your Confluent Platform setup and CLI install
<path-to-confluent>/bin/kafka-console-producer --broker-list <kafka broker hostname> --topic hotels
```

Geben Sie im Konsolenproducer Folgendes ein:

```json
{"id": "h1", "HotelName": "Marriott", "Description": "Marriott description"}
{"id": "h2", "HotelName": "HolidayInn", "Description": "HolidayInn description"}
{"id": "h3", "HotelName": "Motel8", "Description": "Motel8 description"}
```

Die drei eingegebenen Zeilen werden im Kafka-Thema „hotels“ im JSON-Format ausgegeben.

## <a name="create-the-sink-connector"></a>Erstellen des Senkenconnectors

Erstellen Sie den Azure Cosmos DB-Senkenconnector in Kafka Connect. Der folgende JSON-Text definiert die Konfiguration für den Senkenconnector. Sie müssen die Werte für die Eigenschaften `connect.cosmos.connection.endpoint` und `connect.cosmos.master.key` ersetzen, die Sie in den Voraussetzungen im Azure Cosmos DB-Setupleitfaden gespeichert haben sollten.

Weitere Informationen zu den einzelnen Konfigurationseigenschaften finden Sie im Abschnitt zu den [Senkeneigenschaften](#sink-configuration-properties).

```json
{
  "name": "cosmosdb-sink-connector",
  "config": {
    "connector.class": "com.azure.cosmos.kafka.connect.sink.CosmosDBSinkConnector",
    "tasks.max": "1",
    "topics": [
      "hotels"
    ],
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter.schemas.enable": "false",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "key.converter.schemas.enable": "false",
    "connect.cosmos.connection.endpoint": "https://<cosmosinstance-name>.documents.azure.com:443/",
    "connect.cosmos.master.key": "<cosmosdbprimarykey>",
    "connect.cosmos.databasename": "kafkaconnect",
    "connect.cosmos.containers.topicmap": "hotels#kafka"
  }
}
```

Nachdem Sie alle Werte ausgefüllt haben, speichern Sie die JSON-Datei lokal. Sie können diese Datei zum Erstellen des Connectors mithilfe der REST-API verwenden.

### <a name="create-connector-using-control-center"></a>Erstellen eines Connectors mithilfe von Control Center

Eine einfache Möglichkeit zum Erstellen des Connectors bietet die Verwendung der Control Center-Webseite. Folgen Sie diesem [Installationsleitfaden](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-3-install-a-ak-connector-and-generate-sample-data), um einen Connector über Control Center zu erstellen. Verwenden Sie statt der Option `DatagenConnector` die Kachel `CosmosDBSinkConnector`. Geben Sie beim Konfigurieren des Senkenconnectors die Werte so wie in der JSON-Datei ein.

Alternativ können Sie auf der Connectorseite mithilfe der Option **Upload connector config file** (Konfigurationsdatei für den Connector hochladen) die zuvor erstellte JSON-Datei hochladen.

:::image type="content" source="./media/kafka-connector-sink/upload-connector-config.png" alt-text="„Upload connector config file“ (Konfigurationsdatei für den Connector hochladen).":::

### <a name="create-connector-using-rest-api"></a>Erstellen des Connectors mithilfe der REST-API

Erstellen Sie den Senkenconnector mithilfe der Connect-REST-API:

```bash
# Curl to Kafka connect service
curl -H "Content-Type: application/json" -X POST -d @<path-to-JSON-config-file> http://localhost:8083/connectors

```

## <a name="confirm-data-written-to-cosmos-db"></a>Bestätigen von in Cosmos DB geschriebenen Daten

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/learn.docs.microsoft.com) an, und navigieren Sie zu Ihrem Azure Cosmos DB-Konto. Überprüfen Sie, ob die drei Zeilen aus dem Thema „hotels“ in Ihrem Konto erstellt werden.

## <a name="cleanup"></a>Bereinigen

Navigieren Sie zu dem von Ihnen erstellten Senkenconnector, und klicken Sie auf das Symbol **Löschen**, um den Connector aus dem Control Center zu löschen.

:::image type="content" source="./media/kafka-connector-sink/delete-connector.png" alt-text="Löschen des Connectors.":::

Alternativ können Sie zum Löschen die Connect-REST-API verwenden:

```bash
# Curl to Kafka connect service
curl -X DELETE http://localhost:8083/connectors/cosmosdb-sink-connector
```

Führen Sie diese [Schritte](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md#cleanup) aus, um den erstellten Azure Cosmos DB-Dienst und die zugehörige Ressourcengruppe über die Azure CLI zu löschen.

## <a name="sink-configuration-properties"></a><a id="sink-configuration-properties"></a>Konfigurationseigenschaften für den Senkenconnector

Die folgenden Einstellungen werden zum Konfigurieren des Cosmos DB Kafka-Senkenconnectors verwendet. Mit diesen Konfigurationswerten wird festgelegt, welche Kafka-Themendaten verarbeitet werden, in welche Azure Cosmos DB-Container Daten geschrieben werden und welche Formate zum Serialisieren der Daten verwendet werden. Ein Beispiel für eine Konfigurationsdatei mit den Standardwerten finden Sie in [dieser Konfiguration]( https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/docker/resources/sink.example.json).

| Name | type | BESCHREIBUNG | Erforderlich/Optional |
| :--- | :--- | :--- | :--- |
| Themen | list | Dies ist eine Liste der zu beobachtenden Kafka-Themen. | Erforderlich |
| connector.class | Zeichenfolge | Dies ist der Klassenname der Azure Cosmos DB-Senke. Er sollte auf `com.azure.cosmos.kafka.connect.sink.CosmosDBSinkConnector` festgelegt werden. | Erforderlich |
| connect.cosmos.connection.endpoint | uri | Dies ist eine URI-Zeichenfolge für den Azure Cosmos DB-Endpunkt. | Erforderlich |
| connect.cosmos.master.key | Zeichenfolge | Dies ist der Azure Cosmos DB-Primärschlüssel, mit dem die Senke eine Verbindung herstellt. | Erforderlich |
| connect.cosmos.databasename | Zeichenfolge | Dies ist der Name der Azure Cosmos DB-Datenbank, in die die Senke schreibt. | Erforderlich |
| connect.cosmos.containers.topicmap | Zeichenfolge | Dies ist eine Zuordnung zwischen Kafka-Themen und Azure Cosmos DB-Containern, die wie gezeigt mit CSV formatiert wird: `topic#container,topic2#container2`. | Erforderlich |
| key.converter | Zeichenfolge | Dies ist das Serialisierungsformat für die in das Kafka-Thema geschriebenen Schlüsseldaten. | Erforderlich |
| value.converter | Zeichenfolge | Dies ist das Serialisierungsformat für die In das Kafka-Thema geschriebenen Wertdaten. | Erforderlich |
| key.converter.schemas.enable | Zeichenfolge | Legen Sie diese Angabe auf „true“ fest, wenn die Schlüsseldaten ein eingebettetes Schema haben. | Optional |
| value.converter.schemas.enable | Zeichenfolge | Legen Sie diese Angabe auf „true“ fest, wenn die Schlüsseldaten ein eingebettetes Schema haben. | Optional |
| tasks.max | INT | Dies ist die maximale Anzahl von Senkenconnectoraufgaben. Die Standardeinstellung ist `1`. | Optional |

Daten werden immer als JSON ohne Schema in die Azure Cosmos DB-Instanz geschrieben.

## <a name="supported-data-types"></a>Unterstützte Datentypen

Der Azure Cosmos DB-Senkenconnector konvertiert den Senkendatensatz in ein JSON-Dokument, das die folgenden Schematypen unterstützt:

| Schematyp | JSON-Datentyp |
| :--- | :--- |
| Array | Array |
| Boolean | Boolean |
| Float32 | Number |
| Float64 | Number |
| Int8 | Number |
| Int16 | Number |
| Int32 | Number |
| Int64 | Number|
| Zuordnung | Objekt (JSON)|
| String | Zeichenfolge<br> Null |
| Struktur | Objekt (JSON) |

Der Senkenconnector unterstützt auch die folgenden logischen AVRO-Typen:

| Schematyp | JSON-Datentyp |
| :--- | :--- |
| Date | Number |
| Zeit | Number |
| Timestamp | Number |

> [!NOTE]
> Die Byte-Deserialisierung wird vom Azure Cosmos DB-Senkenconnector zurzeit nicht unterstützt.

## <a name="single-message-transformssmt"></a>Single Message Transforms (SMT)

Zusammen mit den Senkenconnectoreinstellungen können Sie die Verwendung von Single Message Transformations (SMTs) zum Ändern von Nachrichten angeben, die die Kafka Connect-Plattform durchlaufen. Weitere Informationen finden Sie in der [Confluent-Dokumentation zu SMTs](https://docs.confluent.io/platform/current/connect/transforms/overview.html).

### <a name="using-the-insertuuid-smt"></a>Verwenden der SMT „InsertUUID“

Sie können mithilfe der SMT „InsertUUID“ Element-IDs automatisch hinzufügen lassen. Mithilfe der benutzerdefinierten SMT `InsertUUID` können Sie das Feld `id` mit einem zufälligen UUID-Wert für jede Nachricht einfügen, bevor sie in Azure Cosmos DB geschrieben wird.

> [!WARNING]
> Verwenden Sie diese SMT nur, wenn Nachrichten das Feld `id` nicht enthalten. Andernfalls werden die `id`-Werte überschrieben, und Ihre Datenbank enthält möglicherweise doppelte Elemente. Die Verwendung von UUIDs als Nachrichten-ID kann eine schnelle und einfache Methode sein, sie sind aber [keine idealen Partitionsschlüssel](https://stackoverflow.com/questions/49031461/would-using-a-substring-of-a-guid-in-cosmosdb-as-partitionkey-be-a-bad-idea) für die Verwendung in Azure Cosmos DB.

### <a name="install-the-smt"></a>Installieren der SMT

Bevor Sie die SMT `InsertUUID` verwenden können, müssen Sie diese Transformation in Ihrem Confluent Platform-Setup installieren. Wenn Sie das Confluent Platform-Setup aus diesem Repository verwenden, ist die Transformation in der Installation bereits enthalten, und Sie können diesen Schritt überspringen.

Alternativ können Sie die [InsertUUID-Quelle](https://github.com/confluentinc/kafka-connect-insert-uuid) packen, um eine neue JAR-Datei zu erstellen. Informationen zum manuellen Installieren des Connectors mithilfe der JAR-Datei finden Sie in diesen [Anleitungen](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually).

```bash
# clone the kafka-connect-insert-uuid repo
https://github.com/confluentinc/kafka-connect-insert-uuid.git
cd kafka-connect-insert-uuid

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Confluent Platform installation
ls target/*.jar
```

### <a name="configure-the-smt"></a>Konfigurieren der SMT

Fügen Sie in Ihrer Senkenconnectorkonfiguration die folgenden Eigenschaften hinzu, um die Eigenschaft `id` festzulegen.

```json
"transforms": "insertID",
"transforms.insertID.type": "com.github.cjmatta.kafka.connect.smt.InsertUuid$Value",
"transforms.insertID.uuid.field.name": "id"
```

Weitere Informationen zur Verwendung dieser SMT finden Sie im [InsertUUID-Repository](https://github.com/confluentinc/kafka-connect-insert-uuid).

### <a name="using-smts-to-configure-time-to-live-ttl"></a>Verwenden von SMTs für die TTL-Konfiguration (Time to live)

Mit den SMTs `InsertField` und `Cast` können Sie TTL bei jedem in Azure Cosmos DB erstellten Element konfigurieren. Aktivieren Sie TTL für den Container, bevor Sie TTL auf Elementebene aktivieren. Weitere Informationen finden Sie in der [TTL-Dokumentation](how-to-time-to-live.md#enable-time-to-live-on-a-container-using-azure-portal).

Fügen Sie in Ihrer Senkenconnectorkonfiguration die folgenden Eigenschaften hinzu, um den TTL-Wert in Sekunden festzulegen. Im folgenden Beispiel wird der TTL-Wert auf 100 Sekunden festgelegt. Wenn die Nachricht bereits das Feld `TTL`enthält, wird der Wert `TTL` von diesen SMTs überschrieben.

```json
"transforms": "insertTTL,castTTLInt",
"transforms.insertTTL.type": "org.apache.kafka.connect.transforms.InsertField$Value",
"transforms.insertTTL.static.field": "ttl",
"transforms.insertTTL.static.value": "100",
"transforms.castTTLInt.type": "org.apache.kafka.connect.transforms.Cast$Value",
"transforms.castTTLInt.spec": "ttl:int32"
```

Weitere Informationen zur Verwendung dieser SMTs finden Sie in der Dokumentation zu [InsertField](https://docs.confluent.io/platform/current/connect/transforms/insertfield.html) und [Cast](https://docs.confluent.io/platform/current/connect/transforms/cast.html).

## <a name="troubleshooting-common-issues"></a>Behandeln allgemeiner Probleme

Hier finden Sie Lösungen für einige allgemeine Probleme, die bei der Arbeit mit dem Kafka-Senkenconnector auftreten können.

### <a name="read-non-json-data-with-jsonconverter"></a>Lesen von Nicht-JSON-Daten mit „JsonConverter“

Wenn Ihr Quellthema in Kafka Nicht-JSON-Daten enthält und Sie versuchen, diese Daten mithilfe des `JsonConverter` zu lesen, wird die folgende Ausnahme angezeigt:

```console
org.apache.kafka.connect.errors.DataException: Converting byte[] to Kafka Connect data failed due to serialization error:
…
org.apache.kafka.common.errors.SerializationException: java.io.CharConversionException: Invalid UTF-32 character 0x1cfa7e2 (above 0x0010ffff) at char #1, byte #7)

```

Dieser Fehler wird wahrscheinlich dadurch verursacht, dass Daten im Quellthema entweder im Avro-Format oder in einem anderen Format, z. B. einer CSV-Zeichenfolge, serialisiert werden.

**Lösung:** Wenn die Themendaten im Avro-Format vorliegen, ändern Sie Ihren Kafka Connect-Senkenconnector so, dass `AvroConverter` wie unten gezeigt verwendet wird.

```json
"value.converter": "io.confluent.connect.avro.AvroConverter",
"value.converter.schema.registry.url": "http://schema-registry:8081",
```

### <a name="read-non-avro-data-with-avroconverter"></a>Lesen von Nicht-Avro-Daten mit „AvroConverter“

Dieses Szenario trifft zu, wenn Sie versuchen, den Avro-Konverter zum Lesen von Daten aus einem Thema zu verwenden, das nicht im Avro-Format vorliegt. Dabei werden Daten von einem anderen Avro-Serialisierungsmodul als dem Avro-Serialisierungsmodul von „Confluent Schema Registry“ geschrieben, das ein eigenes Sendeformat hat.

```console
org.apache.kafka.connect.errors.DataException: my-topic-name
at io.confluent.connect.avro.AvroConverter.toConnectData(AvroConverter.java:97)
…
org.apache.kafka.common.errors.SerializationException: Error deserializing Avro message for id -1
org.apache.kafka.common.errors.SerializationException: Unknown magic byte!

```

**Lösung**: Überprüfen Sie das Serialisierungsformat des Quellthemas. Ändern Sie dann den Kafka Connect-Senkenconnector entweder so, dass der richtige Konverter verwendet wird, oder ändern Sie das Upstreamformat in „Avro“.

### <a name="read-a-json-message-without-the-expected-schemapayload-structure"></a>Lesen einer JSON-Nachricht ohne die erwartete Schema- bzw. Nutzdatenstruktur

Kafka Connect unterstützt eine spezielle Struktur von JSON-Nachrichten, die – wie im Folgenden gezeigt – sowohl die Nutzdaten als auch das Schema enthalten.

```json
{
  "schema": {
    "type": "struct",
    "fields": [
      {
        "type": "int32",
        "optional": false,
        "field": "userid"
      },
      {
        "type": "string",
        "optional": false,
        "field": "name"
      }
    ]
  },
  "payload": {
    "userid": 123,
    "name": "Sam"
  }
}
```

Wenn Sie JSON-Daten zu lesen versuchen, in denen die Daten in dieser Struktur nicht enthalten sind, wird der folgende Fehler angezeigt:

```none
org.apache.kafka.connect.errors.DataException: JsonConverter with schemas.enable requires "schema" and "payload" fields and may not contain additional fields. If you are trying to deserialize plain JSON data, set schemas.enable=false in your converter configuration.
```

Die einzige für `schemas.enable=true` gültige JSON-Struktur enthält also – wie oben gezeigt – Schema- und Nutzdatenfelder als Elemente der obersten Ebene. Wie in der Fehlermeldung beschrieben, sollten Sie die Konfiguration Ihres Connectors wie folgt ändern, wenn es nur einfache JSON-Daten gibt:

```json
"value.converter": "org.apache.kafka.connect.json.JsonConverter",
"value.converter.schemas.enable": "false",
```

## <a name="limitations"></a>Einschränkungen

* Das automatische Erstellen von Datenbanken und Containern in Azure Cosmos DB wird nicht unterstützt. Die Datenbanken und Container müssen bereits vorhanden und ordnungsgemäß konfiguriert sein.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Änderungsfeed in Azure Cosmo DB finden Sie in den folgenden Dokumentationen:

* [Einführung in den Änderungsfeed](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
* [Lesen des Azure Cosmos DB-Änderungsfeeds](read-change-feed.md)