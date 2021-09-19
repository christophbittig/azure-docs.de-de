---
title: Verwenden von Kafka Connect für Azure Cosmos DB zum Lesen und Schreiben von Daten
description: Kafka Connect für Azure Cosmos DB ist ein Connector zum Lesen und Schreiben von Daten in Azure Cosmos DB. Kafka Connect ist ein Tool zum skalierbaren und zuverlässigen Streamen von Daten zwischen Apache Kafka und anderen Systemen.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: 02489a961edf3c9044209da64882d0ab60d20bb4
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115520"
---
# <a name="kafka-connect-for-azure-cosmos-db"></a>Kafka Connect für Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

[Kafka Connect](http://kafka.apache.org/documentation.html#connect) ist ein Tool zum skalierbaren und zuverlässigen Streamen von Daten zwischen Apache Kafka und anderen Systemen. Mithilfe von Kafka Connect können Sie Connectors definieren, die große Datasets in und aus Kafka verschieben. Kafka Connect für Azure Cosmos DB ist ein Connector zum Lesen und Schreiben von Daten in Azure Cosmos DB.

## <a name="source--sink-connectors-semantics"></a>Semantik von Quell- und Senkenconnectors

* **Quellconnector**: Dieser Connector unterstützt zurzeit „at-least-once“ (mindestens einmal) bei mehreren Aufgaben und „exactly-once“ (genau einmal) bei einzelnen Aufgaben.

* **Senkenconnector**: Dieser Connector unterstützt „exactly-once“-Semantiken vollständig.

## <a name="supported-data-formats"></a>Unterstützte Datenformate

Die Quell- und Senkenconnectors können so konfiguriert werden, dass sie die folgenden Datenformate unterstützen:

| Format | BESCHREIBUNG |
| :----------- | :---------- |
| Plain JSON (JSON (einfach))  | Dies ist eine JSON-Datensatzstruktur ohne angefügtes Schema. |
| JSON with schema (JSON mit Schema) | Dies ist eine JSON-Datensatzstruktur mit expliziten Schemainformationen, um sicherzustellen, dass die Daten dem erwarteten Format entsprechen. |
| AVRO | Dies ist ein zeilenorientiertes Framework für Remoteprozeduraufrufe und Datenserialisierung, das im Hadoop-Projekt von Apache entwickelt wurde. Es verwendet JSON zum Definieren von Datentypen sowie Protokollen und serialisiert Daten in einem kompakten Binärformat.

Die Schlüssel- und Werteinstellungen, einschließlich des Formats und der Serialisierung, können in Kafka unabhängig voneinander konfiguriert werden. Deshalb ist es möglich, bei Schlüsseln bzw. Werten mit unterschiedlichen Datenformaten zu arbeiten. Zur Berücksichtigung unterschiedlicher Datenformate gibt es Konverterkonfigurationen für `key.converter` und `value.converter`.

## <a name="converter-configuration-examples"></a>Beispiele für Konverterkonfigurationen

### <a name="plain-json"></a><a id="json-plain"></a>Plain JSON (JSON (einfach))

Wenn Sie JSON ohne Schemaregistrierung zum Verbinden von Daten verwenden müssen, nutzen Sie den bei Kafka unterstützten `JsonConverter`. Das folgende Beispiel zeigt die Schlüssel- und Werteigenschaften für `JsonConverter`, die der Konfiguration hinzugefügt werden:

  ```java
  key.converter=org.apache.kafka.connect.json.JsonConverter
  key.converter.schemas.enable=false
  value.converter=org.apache.kafka.connect.json.JsonConverter
  value.converter.schemas.enable=false
  ```

### <a name="json-with-schema"></a><a id="json-with-schema"></a>JSON with schema (JSON mit Schema)

Legen Sie die Eigenschaften `key.converter.schemas.enable` und `value.converter.schemas.enable` auf „true“ fest, damit der Schlüssel oder Wert als zusammengesetztes JSON-Objekt behandelt wird, das sowohl ein internes Schema als auch die Daten enthält. Ohne diese Eigenschaften wird der Schlüssel oder Wert als einfacher JSON-Code behandelt. 

  ```java
  key.converter=org.apache.kafka.connect.json.JsonConverter
  key.converter.schemas.enable=true
  value.converter=org.apache.kafka.connect.json.JsonConverter
  value.converter.schemas.enable=true
  ```

Die resultierende Nachricht an Kafka würde wie im folgenden Beispiel aussehen, wobei Schema und Nutzlast im JSON-Code als Elemente der obersten Ebene enthalten sind:

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
      ],
      "optional": false,
      "name": "ksql.users"
    },
    "payload": {
      "userid": 123,
      "name": "user's name"
    }
  }
  ```

> [!NOTE]
> Die in Azure Cosmos DB geschriebene Nachricht besteht aus dem Schema und der Nutzlast. Beachten Sie die Größe der Nachricht sowie das Verhältnis von Nutzlast und Schema. Das Schema wird in jeder Nachricht wiederholt, die Sie in Kafka schreiben. In solchen Szenarios sollten Sie ein Serialisierungsformat wie das JSON-Schema oder AVRO verwenden, bei dem das Schema separat gespeichert wird und die Nachricht nur die Nutzlast enthält.

### <a name="avro"></a><a id="avro"></a>AVRO

Der Kafka-Connector unterstützt das AVRO-Datenformat. Konfigurieren Sie für die Verwendung des AVRO-Formats einen `AvroConverter`, damit Kafka Connect AVRO-Daten verarbeiten kann. Azure Cosmos DB Kafka Connect wurde unter der Apache 2.0-Lizenz mit dem von Confluent bereitgestellten [AvroConverter](https://www.confluent.io/hub/confluentinc/kafka-connect-avro-converter) getestet. Bei Bedarf können Sie auch einen anderen benutzerdefinierten Konverter verwenden.

Kafka verarbeitet Schlüssel und Werte unabhängig voneinander. Geben Sie die Eigenschaften `key.converter` und `value.converter` nach Bedarf in der Workerkonfiguration an. Wenn Sie `AvroConverter` verwenden, fügen Sie eine zusätzliche Konvertereigenschaft hinzu, die die URL für die Schemaregistrierung bereitstellt. Das folgende Beispiel zeigt die Schlüssel- und Werteigenschaften für „AvroConverter“, die der Konfiguration hinzugefügt werden:

  ```java
  key.converter=io.confluent.connect.avro.AvroConverter
  key.converter.schema.registry.url=http://schema-registry:8081
  value.converter=io.confluent.connect.avro.AvroConverter
  value.converter.schema.registry.url=http://schema-registry:8081
  ```

## <a name="choose-a-conversion-format"></a>Auswählen eines Konvertierungsformats

Im Folgenden werden einige Überlegungen zur Auswahl eines Konvertierungsformats erläutert:

* Beim Konfigurieren eines **Quellconnectors**:

  * Wenn Kafka Connect einfachen JSON-Code in die Nachricht einbeziehen soll, die in Kafka geschrieben wird, legen Sie die Konfiguration [Plain JSON](#json-plain) (JSON (einfach)) fest.

  * Wenn Kafka Connect das Schema in die Nachricht einbeziehen soll, die in Kafka geschrieben wird, legen Sie die Konfiguration [JSON with Schema](#json-with-schema) (JSON mit Schema) fest.

  * Wenn Kafka Connect das AVRO-Format in die Nachricht einbeziehen soll, die in Kafka geschrieben wird, legen Sie die Konfiguration [AVRO](#avro) fest.

* Wenn Sie JSON-Daten aus einem Kafka-Thema für einen **Senkenconnector** verwenden, sollten Sie verstehen, wie die Daten beim Schreiben in das Kafka-Thema serialisiert wurden:

  * Wenn die Daten mit dem JSON-Serialisierungsmodul geschrieben wurden, legen Sie die Einstellung für Kafka Connect so fest, dass der JSON-Konverter `(org.apache.kafka.connect.json.JsonConverter)` verwendet wird.

    * Wenn die JSON-Daten als einfache Zeichenfolge geschrieben wurden, bestimmen Sie, ob die Daten ein geschachteltes Schema oder eine Nutzlast enthalten. Wenn dies zutrifft, legen Sie die Konfiguration [JSON with schema](#json-with-schema) (JSON mit Schema) fest.
    * Wenn Sie jedoch JSON-Daten ohne Schema- oder Nutzlastkonstrukt verwenden, müssen Sie Kafka Connect anweisen, **nicht** nach einem Schema zu suchen, indem Sie `schemas.enable=false` entsprechend der Konfiguration [Plain JSON](#json-plain) (JSON (einfach)) festlegen.

  * Wenn Daten mit dem AVRO-Serialisierungsmodul geschrieben wurden, legen Sie die Einstellung für Kafka Connect so fest, dass der AVRO-Konverter `(io.confluent.connect.avro.AvroConverter)` entsprechend der [AVRO](#avro)-Konfiguration verwendet wird.

## <a name="configuration"></a>Konfiguration

### <a name="common-configuration-properties"></a>Allgemeine Konfigurationseigenschaften

Die Quell- und Senkenconnectors nutzen die folgenden allgemeinen Konfigurationseigenschaften:

| Name | type | BESCHREIBUNG | Erforderlich/Optional |
| :--- | :--- | :--- | :--- |
| connect.cosmos.connection.endpoint | uri | Dies ist eine URI-Zeichenfolge für den Cosmos-Endpunkt. | Erforderlich |
| connect.cosmos.master.key | Zeichenfolge | Dies ist der Azure Cosmos DB-Primärschlüssel, mit dem die Senke eine Verbindung herstellt. | Erforderlich |
| connect.cosmos.databasename | Zeichenfolge | Dies ist der Name der Azure Cosmos DB-Datenbank, in die die Senke schreibt. | Erforderlich |
| connect.cosmos.containers.topicmap | Zeichenfolge | Dies ist eine Zuordnung zwischen Kafka-Themen und Azure Cosmos DB-Containern. Sie wird mithilfe von CSV als `topic#container,topic2#container2` formatiert. | Erforderlich |

Informationen zu einer senkenconnectorspezifischen Konfiguration finden Sie in der [Dokumentation zum Senkenconnector](kafka-connector-sink.md).

Informationen zu einer quellconnectorspezifischen Konfiguration finden Sie in der [Dokumentation zum Quellconnector](kafka-connector-source.md).

## <a name="common-configuration-errors"></a>Häufige Konfigurationsfehler

Wenn Sie die Konverter in Kafka Connect falsch konfigurieren, kann dies zu Fehlern führen. Diese Fehler werden in der Kafka Connector-Senke angezeigt, weil Sie versuchen werden, die in Kafka bereits gespeicherten Nachrichten zu deserialisieren. Konverterprobleme treten in der Quelle normalerweise nicht auf, weil Serialisierung darin festgelegt wird.

Weitere Informationen finden Sie in der Dokumentation zu [häufigen Konfigurationsfehlern](https://www.confluent.io/blog/kafka-connect-deep-dive-converters-serialization-explained/#common-errors).

## <a name="project-setup"></a>Projekteinrichtung

Anleitungen zur ersten Einrichtung finden Sie unter [Exemplarische Vorgehensweise für Entwickler und Einrichtung des Projekts](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Developer_Walkthrough.md).

## <a name="performance-testing"></a>Leistungstests

Weitere Informationen zur Ausführung von Leistungstests für die Senken- und Quellconnectors finden Sie im [Dokument zu Leistungstests](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Performance_Testing.md).

Die genauen Schritte zum Bereitstellen der Leistungsumgebung für die Connectors finden Sie unter [Einrichten der Leistungsumgebung](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/perf/README.md).

## <a name="resources"></a>Ressourcen

* [Kafka Connect](http://kafka.apache.org/documentation.html#connect)
* [Ausführliche Betrachtung von Kafka Connect: Erläuterung von Konvertern und der Serialisierung](https://www.confluent.io/blog/kafka-connect-deep-dive-converters-serialization-explained/)

## <a name="next-steps"></a>Nächste Schritte

* Kafka Connect für Azure Cosmos  DB [Quellconnector](kafka-connector-source.md)
* Kafka Connect für Azure Cosmos DB [Senkenconnector](kafka-connector-sink.md)