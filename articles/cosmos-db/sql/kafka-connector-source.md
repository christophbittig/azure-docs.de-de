---
title: Kafka Connect für Azure Cosmos DB – Quellconnector
description: Der Azure Cosmos DB-Quellconnector ermöglicht das Lesen von Daten aus dem Azure Cosmos DB-Änderungsfeed und das Veröffentlichen dieser Daten in einem Kafka-Thema. Kafka Connect für Azure Cosmos DB ist ein Connector zum Lesen und Schreiben von Daten in Azure Cosmos DB.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: 99879c5fa14cc91e8a39b99ff1a428f31bfde744
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115281"
---
# <a name="kafka-connect-for-azure-cosmos-db---source-connector"></a>Kafka Connect für Azure Cosmos DB – Quellconnector
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Kafka Connect für Azure Cosmos DB ist ein Connector zum Lesen und Schreiben von Daten in Azure Cosmos DB. Der Azure Cosmos DB-Quellconnector ermöglicht das Lesen von Daten aus dem Azure Cosmos DB-Änderungsfeed und das Veröffentlichen dieser Daten in einem Kafka-Thema.

## <a name="prerequisites"></a>Voraussetzungen

* Beginnen Sie mit dem [Confluent Plattform-Einrichtung](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md), da Ihnen diese Plattform eine vollständige Umgebung für die Arbeit bietet. Wenn Sie Confluent Platform nicht verwenden möchten, müssen Sie Zookeeper, Apache Kafka und Kafka Connect selbst installieren und konfigurieren. Außerdem müssen Sie die Azure Cosmos DB-Connectoren manuell installieren und konfigurieren.
* Erstellen eines Azure Cosmos DB-Kontos, [Einrichtungsleitfaden](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md) für Container
* Bash-Shell, die in GitHub Codespaces bzw. unter Mac, Ubuntu und Windows mit WSL2 getestet sind. Diese Shell funktioniert nicht in Cloud Shell oder WSL1.
* [Java 11 oder höher](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) herunterladen
* [Maven](https://maven.apache.org/download.cgi) herunterladen

## <a name="install-the-source-connector"></a>Quellconnector installieren

Wenn Sie die empfohlene [Confluent Plattform-Einrichtung](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md) verwenden, ist der Azure Cosmos DB-Quellconnector in der Installation enthalten, und Sie können diesen Schritt überspringen.

Andernfalls können Sie die JAR-Datei aus dem neuesten [Release](https://github.com/microsoft/kafka-connect-cosmosdb/releases) verwenden und den Connector manuell installieren. Weitere Informationen finden Sie in diesen [Anweisungen](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually). Sie können auch eine neue JAR-Datei auf der Grundlage des Quellcodes packen:

```bash
# clone the kafka-connect-cosmosdb repo if you haven't done so already
git clone https://github.com/microsoft/kafka-connect-cosmosdb.git
cd kafka-connect-cosmosdb

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Confluent Platform installation
ls target/*dependencies.jar
```

## <a name="create-a-kafka-topic"></a>Erstellen eines Kafka-Themas

Erstellen Sie ein Kafka-Thema mithilfe von Confluent Control Center. In diesem Szenario wird ein Kafka-Thema namens „Apparels“ erstellt, und es werden nicht in Schemas eingebettete JSON-Daten in das Thema geschrieben. Informationen zum Erstellen eines Themas in Control Center finden Sie in der [Dokumentation zum Erstellen eines Kafka-Themas](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-2-create-ak-topics).

## <a name="create-the-source-connector"></a>Erstellen des Quellconnectors

### <a name="create-the-source-connector-in-kafka-connect"></a>Erstellen des Quellconnectors in Kafka Connect

Verwenden Sie die folgende JSON-Konfiguration, um den Azure Cosmos DB-Quellconnector in Kafka Connect zu erstellen. Ersetzen Sie die Platzhalterwerte für die Eigenschaften `connect.cosmos.connection.endpoint`, `connect.cosmos.master.key`, die Sie im Rahmen der Vorbereitung aus dem Azure Cosmos DB-Einrichtungsleitfaden gespeichert haben sollten.

```json
{
  "name": "cosmosdb-source-connector",
  "config": {
    "connector.class": "com.azure.cosmos.kafka.connect.source.CosmosDBSourceConnector",
    "tasks.max": "1",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "connect.cosmos.task.poll.interval": "100",
    "connect.cosmos.connection.endpoint": "https://<cosmosinstance-name>.documents.azure.com:443/",
    "connect.cosmos.master.key": "<cosmosdbprimarykey>",
    "connect.cosmos.databasename": "kafkaconnect",
    "connect.cosmos.containers.topicmap": "apparels#kafka",
    "connect.cosmos.offset.useLatest": false,
    "value.converter.schemas.enable": "false",
    "key.converter.schemas.enable": "false"
  }
}
```

Weitere Informationen zu den oben erwähnten Konfigurationseigenschaften finden Sie im [Abschnitt zu den Quelleneigenschaften](#source-configuration-properties). Speichern Sie die JSON-Datei lokal, nachdem Sie alle Werte eingegeben haben. Sie können diese Datei verwenden, um den Connector mithilfe der REST-API zu erstellen.

#### <a name="create-connector-using-control-center"></a>Erstellen eines Connectors mithilfe von Control Center

Eine einfache Möglichkeit zum Erstellen des Connectors ist das Confluent Control Center-Portal. Führen Sie die im [Confluent-Einrichtungsleitfaden](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-3-install-a-ak-connector-and-generate-sample-data) beschriebenen Schritte aus, um einen Connector über Control Center zu erstellen. Verwenden Sie bei der Einrichtung anstelle der Option `DatagenConnector` die Kachel `CosmosDBSourceConnector`. Geben Sie beim Konfigurieren des Quellconnectors die Werte so wie in der JSON-Datei ein.

Alternativ können Sie auf der Connectorseite mithilfe der Option **Upload connector config file** (Konfigurationsdatei für den Connector hochladen) die anhand des vorherigen Abschnitts erstellte JSON-Datei hochladen.

:::image type="content" source="./media/kafka-connector-source/upload-connector-config.png" alt-text="Hochladen der Connectorkonfiguration.":::

#### <a name="create-connector-using-rest-api"></a>Erstellen des Connectors mithilfe der REST-API

Erstellen des Quellconnectors mithilfe der Connect-REST-API

```bash
# Curl to Kafka connect service
curl -H "Content-Type: application/json" -X POST -d @<path-to-JSON-config-file> http://localhost:8083/connectors
```

## <a name="insert-document-into-azure-cosmos-db"></a>Einfügen des Dokuments in Azure Cosmos DB

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/learn.docs.microsoft.com) an, und navigieren Sie zu Ihrem Azure Cosmos DB-Konto.
1. Öffnen Sie die Registerkarte **Daten-Explorer**, und wählen Sie **Datenbanken**
1. Öffnen Sie die Datenbank „kafkaconnect“ und den zuvor erstellten Container „kafka“.
1. Klappen Sie zum Erstellen eines neuen JSON-Dokuments im Bereich der SQL-API den Container „kafka“ auf, und wählen Sie auf der Symbolleiste die Option **Elemente** und anschließend **Neues Element**.
1. Fügen Sie dem Container jetzt ein Dokument mit der folgenden Struktur hinzu. Fügen Sie auf der Registerkarte „Elemente“ als Beispiel den folgenden JSON-Block ein, und überschreiben Sie dabei den aktuellen Inhalt:

   ``` json
 
   {
     "id": "2",
     "productId": "33218897",
     "category": "Women's Outerwear",
     "manufacturer": "Contoso",
     "description": "Black wool pea-coat",
     "price": "49.99",
     "shipping": {
       "weight": 2,
       "dimensions": {
         "width": 8,
         "height": 11,
         "depth": 3
       }
     }
   }
 
   ```

1. Wählen Sie **Speichern** aus.
1. Vergewissern Sie sich, dass das Dokument gespeichert wurde, indem Sie sich die Elemente im linken Menü anzeigen.

### <a name="confirm-data-written-to-kafka-topic"></a>Vergewissern, dass Daten in das Kafka-Thema geschrieben wurden

1. Öffnen Sie unter `<http://localhost:9000>` die Benutzeroberfläche für Kafka-Themen.
1. Wählen Sie das von Ihnen erstellte Kafka-Thema „Apparels“ aus.
1. Vergewissern Sie sich, dass das Dokument, das Sie zuvor in Azure Cosmos DB eingefügt haben, im Kafka-Thema erscheint.

### <a name="cleanup"></a>Bereinigen

Um den Connector aus Confluent Control Center zu löschen, navigieren Sie zu dem von Ihnen erstellten Quellconnector, und wählen Sie das Symbol **Löschen**.

:::image type="content" source="./media/kafka-connector-source/delete-source-connector.png" alt-text="Löschen des Connectors aus Confluent Control Center":::

Verwenden Sie alternativ die REST-API des Connectors:

```bash
# Curl to Kafka connect service
curl -X DELETE http://localhost:8083/connectors/cosmosdb-source-connector
```

Führen Sie diese [Schritte](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md#cleanup) aus, um den erstellten Azure Cosmos DB-Dienst und die zugehörige Ressourcengruppe mithilfe der Azure CLI zu löschen.

## <a name="source-configuration-properties"></a>Eigenschaften der Quellkonfiguration

Die folgenden Einstellungen werden zum Konfigurieren des Kafka-Quellconnector verwendet. Diese Konfigurationswerte bestimmen den verwendeten Azure Cosmos DB-Container, die Daten, aus denen Kafka-Themen geschrieben werden, sowie Formate zum Serialisieren der Daten. Ein Beispiel mit Standardwerten finden Sie in dieser [Konfigurationsdatei](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/docker/resources/source.example.json).

| Name | type | BESCHREIBUNG | Erforderlich/optional |
| :--- | :--- | :--- | :--- |
| connector.class | String | Klassenname der Azure Cosmos DB-Quelle. Er muss auf `com.azure.cosmos.kafka.connect.source.CosmosDBSourceConnector` gesetzt werden | Erforderlich |
| connect.cosmos.databasename | String | Name der Datenbank, aus der gelesen werden soll. | Erforderlich |
| connect.cosmos.master.key | String | Der Azure Cosmos DB-Primärschlüssel. | Erforderlich |
| connect.cosmos.connection.endpoint | URI | Der Kontoendpunkt. | Erforderlich |
| connect.cosmos.containers.topicmap | String | Kommagetrennte Zuordnung von Themen zu Containern. Beispiel: Thema1#Sammlung1, Thema2#Sammlung2 | Erforderlich |
| connect.cosmos.messagekey.enabled | Boolesch | Dieser Wert gibt an, ob der Kafka-Nachrichtenschlüssel festgelegt werden soll. Standardwert: `true` | Erforderlich |
| connect.cosmos.messagekey.field | String | Verwenden Sie den Wert des Felds aus dem Dokument als Nachrichtenschlüssel. Der Standardwert ist `id`. | Erforderlich |
| connect.cosmos.offset.useLatest | Boolesch |  Auf `true` setzen, um den neueste Quellversatz zu verwenden. Auf `false` setzen, um den ältesten erfassten Quellversatz zu verwenden. Der Standardwert ist `false`. | Erforderlich |
| connect.cosmos.task.poll.interval | Int | Intervall zum Abfragen von Änderungen im Änderungsfeedcontainer. | Erforderlich |
| key.converter | String | Serialisierungsformat für die Schlüsseldaten, die in das Kafka-Thema geschrieben wurden. | Erforderlich |
| value.converter | String | Serialisierungsformat für die Wertedaten, die in das Kafka-Thema geschrieben werden. | Erforderlich |
| key.converter.schemas.enable | String | Setzen Sie diese Option auf `true`, wenn die Schlüsseldaten über ein eingebettetes Schema verfügen. | Optional |
| value.converter.schemas.enable | String | Setzen Sie diese Option auf `true`, wenn die Schlüsseldaten über ein eingebettetes Schema verfügen. | Optional |
| tasks.max | Int | Maximale Anzahl von Connectorquellenaufgaben. Der Standardwert ist `1`. | Optional |

## <a name="supported-data-types"></a>Unterstützte Datentypen

Der Azure Cosmos DB-Quellconnector konvertiert ein JSON-Dokument in ein Schema und unterstützt die folgenden JSON-Datentypen:

| JSON-Datentyp | Schematyp |
| :--- | :--- |
| Array | Array |
| Boolean | Boolean | 
| Number | Float32<br>Float64<br>Int8<br>Int16<br>Int32<br>Int64|
| Null | String |
| Objekt (JSON)| Struktur|
| String | String |

## <a name="next-steps"></a>Nächste Schritte

* Kafka Connect für Azure Cosmos DB: [Quellconnector](kafka-connector-sink.md)