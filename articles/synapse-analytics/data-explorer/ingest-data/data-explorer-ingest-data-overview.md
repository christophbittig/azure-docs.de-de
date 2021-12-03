---
title: Übersicht über die Azure Synapse Data Explorer-Datenerfassung (Vorschau)
description: Hier erfahren Sie mehr über die verschiedenen Möglichkeiten, wie Sie Daten in Azure Synapse Data Explorer erfassen (laden) können.
ms.topic: overview
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 739867e66bf111d19226ea99198d4d5efec0eeb5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479722"
---
# <a name="azure-synapse-data-explorer-data-ingestion-overview-preview"></a>Übersicht über die Azure Synapse Data Explorer-Datenerfassung (Vorschau)

Die Datenerfassung ist der Prozess, mit dem Datensätze aus einer oder mehreren Quellen geladen werden, um Daten in eine Tabelle in einen Azure Synapse Data Explorer-Pool zu importieren. Nach der Erfassung sind die Daten für Abfragen verfügbar.

Der Datenverwaltungsdienst von Azure Synapse Data Explorer, der für die Datenerfassung zuständig ist, implementiert die folgenden Prozesse:

- Daten werden in Batches oder Datenströmen aus einer externen Quelle gepullt, und Anforderungen werden aus einer ausstehenden Azure-Warteschlange gelesen.
- Batchdaten, die an dieselbe Datenbank und Tabelle gesendet werden, sind für den Durchsatz der Datenerfassung optimiert.
- Anfängliche Daten werden überprüft, und das Format wird bei Bedarf konvertiert.
- Die weitere Datenbearbeitung umfasst den Schemaabgleich sowie die Organisation, Indizierung, Codierung und Komprimierung der Daten.
- Die Daten werden gemäß der festgelegten Aufbewahrungsrichtlinie im Speicher gespeichert.
- Für erfasste Daten erfolgt ein Commit in der Engine, wo sie dann für Abfragen zur Verfügung stehen.

## <a name="supported-data-formats-properties-and-permissions"></a>Unterstützte Datenformate, Eigenschaften und Berechtigungen

* **[Unterstützte Datenformate](data-explorer-ingest-data-supported-formats.md)**

* **[Erfassungseigenschaften:](data-explorer-ingest-data-properties.md)** Eigenschaften, die Einfluss auf die Datenerfassung haben (z. B. Tagging, Zuordnung, Erstellungszeit).

* **Berechtigungen**: Zum Erfassen von Daten benötigt der Prozess [Berechtigungen auf Ebene der Datenbankerfassung](/azure/data-explorer/kusto/management/access-control/role-based-authorization?context=/azure/synapse-analytics/context/context). Für andere Aktionen wie z. B. Abfragen sind möglicherweise die Berechtigungen „Datenbankadministrator“, „Datenbankbenutzer“ oder „Tabellenadministrator“ erforderlich.

## <a name="batching-vs-streaming-ingestions"></a>Batcherfassung und Streamingerfassung

* Bei der Batcherfassung werden die Daten in Batches zusammengefasst und für einen hohen Erfassungsdurchsatz optimiert. Diese Methode stellt die bevorzugte und leistungsfähigste Art der Erfassung dar. Die Daten werden gemäß den Erfassungseigenschaften in Batches zusammengefasst. Kleine Datenbatches werden zusammengeführt und für schnelle Abfrageergebnisse optimiert. Die Richtlinie für die [Batcherfassung](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context) kann für Datenbanken oder Tabellen festgelegt werden. Standardmäßig beträgt der Maximalwert für Batches 5 Minuten, 1.000 Elemente oder eine Gesamtgröße von 1 GB.  Das Datengröße für einen Batcherfassungsbefehl ist auf 4 GB beschränkt.

* Die [Streamingerfassung](data-explorer-ingest-data-streaming.md) erfolgt durch eine fortlaufende Datenerfassung aus einer Streamingquelle. Durch die Streamingerfassung sinkt die Latenz bei kleinen Datensätzen pro Tabelle auf nahezu Echtzeit. Die Daten werden anfänglich im Zeilenspeicher erfasst und dann in Spaltenspeichererweiterungen verschoben.

## <a name="ingestion-methods-and-tools"></a>Erfassungsmethoden und -tools

Azure Synapse Data Explorer unterstützt mehrere Erfassungsmethoden mit jeweils eigenen Zielszenarien. Diese Methoden umfassen Erfassungstools, Connectors und Plug-Ins für verschiedene Dienste, verwaltete Pipelines, die programmgesteuerte Erfassung mithilfe von SDKs und Direktzugriff auf die Erfassung.

### <a name="ingestion-using-managed-pipelines"></a>Erfassung mit verwalteten Pipelines

Für Organisationen, die eine Verwaltung (Drosselung, Wiederholungen, Überwachungen, Warnungen usw.) über einen externen Dienst durchführen möchten, stellt die Verwendung eines Connectors wahrscheinlich die geeignetste Lösung dar. Die Erfassung in der Warteschlange eignet sich für große Datenmengen. Azure Synapse Data Explorer unterstützt die folgenden Azure Pipelines:

<!-- * **[Event Grid](https://azure.microsoft.com/services/event-grid/)**: A pipeline that listens to Azure storage, and updates Azure Synapse Data Explorer to pull information when subscribed events occur. For more information, see [Ingest Azure Blobs into Azure Synapse Data Explorer](ingest-data-event-grid.md). -->

* **[Event Hub:](https://azure.microsoft.com/services/event-hubs/)** Eine Pipeline, die Ereignisse von Diensten an Azure Synapse Data Explorer überträgt. Weitere Informationen finden Sie unter [Event Hub-Datenverbindung (Vorschau)](data-explorer-ingest-event-hub-overview.md).

<!-- * **[IoT Hub](https://azure.microsoft.com/services/iot-hub/)**: A pipeline that is used for the transfer of data from supported IoT devices to Azure Synapse Data Explorer. For more information, see [Ingest from IoT Hub](ingest-data-iot-hub.md). -->

* **Synapse-Pipelines:** Ein vollständig verwalteter Datenintegrationsdienst für Analyseworkloads in [Synapse-Pipelines](/azure/data-factory/copy-activity-overview?context=/azure/synapse-analytics/context/context&tabs=synapse-analytics) stellt eine Verbindung mit über 90 unterstützten Quellen her, um eine effiziente und resiliente Datenübertragung zu ermöglichen. Synapse-Pipelines führen die Vorbereitung, Transformation und Anreicherung der Daten durch, um Erkenntnisse zu erhalten, die auf unterschiedliche Art und Weise überwacht werden können. Dieser Dienst kann als einmalige Lösung verwendet, periodisch ausgeführt oder durch bestimmte Ereignisse ausgelöst werden.

<!-- ### Ingestion using connectors and plugins

* **Logstash plugin**, see [Ingest data from Logstash to Azure Synapse Data Explorer](ingest-data-logstash.md).

* **Kafka connector**, see [Ingest data from Kafka into Azure Synapse Data Explorer](ingest-data-kafka.md).

* **[:::no-loc text="Power Automate":::](https://flow.microsoft.com/)**: An automated workflow pipeline to Azure Synapse Data Explorer. :::no-loc text="Power Automate"::: can be used to execute a query and do preset actions using the query results as a trigger. See [Azure Synapse Data Explorer connector to :::no-loc text="Power Automate"::: (Preview)](flow.md).

* **Apache Spark connector**: An open-source project that can run on any Spark cluster. It implements data source and data sink for moving data across Azure Synapse Data Explorer and Spark clusters. You can build fast and scalable applications targeting data-driven scenarios. See [Azure Synapse Data Explorer Connector for Apache Spark](spark-connector.md). -->

### <a name="programmatic-ingestion-using-sdks"></a>Programmgesteuerte Erfassung mit SDKs

Azure Synapse Data Explorer bietet SDKs, die für Abfragen und die Datenerfassung verwendet werden können. Die programmgesteuerte Erfassung ist dafür optimiert, die Erfassungskosten (COGs) zu senken, indem Speichertransaktionen während und nach dem Erfassungsprozess minimiert werden.

Führen Sie zunächst die folgenden Schritte aus, um die Data Explorer-Poolendpunkte zum Konfigurieren der programmgesteuerten Erfassung abzurufen:

[!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]

**Verfügbare SDKs und Open-Source-Projekte**

* [Python SDK](/azure/data-explorer/kusto/api/python/kusto-python-client-library?context=/azure/synapse-analytics/context/context)

* [.NET SDK](/azure/data-explorer/kusto/api/netfx/about-the-sdk?context=/azure/synapse-analytics/context/context)

* [Java SDK](/azure/data-explorer/kusto/api/java/kusto-java-client-library?context=/azure/synapse-analytics/context/context)

* [Node SDK](/azure/data-explorer/kusto/api/node/kusto-node-client-library?context=/azure/synapse-analytics/context/context)

* [REST-API](/azure/data-explorer/kusto/api/netfx/kusto-ingest-client-rest?context=/azure/synapse-analytics/context/context)

* [GO SDK](/azure/data-explorer/kusto/api/golang/kusto-golang-client-library?context=/azure/synapse-analytics/context/context)

### <a name="tools"></a>Tools

* **[1-Klick-Erfassung:](data-explorer-ingest-data-one-click.md)** Ermöglicht das schnelle Erfassen von Daten durch Erstellen und Anpassen von Tabellen aus einer breiten Palette von Quelltypen. Bei der 1-Klick-Erfassung werden basierend auf der Datenquelle in Azure Synapse Data Explorer automatisch Tabellen und Zuordnungsstrukturen vorschlagen. Sie können die 1-Klick-Erfassung für die einmalige Erfassung verwenden oder zum Definieren einer kontinuierlichen Erfassung über Event Grid in dem Container, in dem die Daten erfasst wurden.

<!-- * **[LightIngest](lightingest.md)**: A command-line utility for ad-hoc data ingestion into Azure Synapse Data Explorer. The utility can pull source data from a local folder or from an Azure blob storage container. -->

### <a name="kusto-query-language-ingest-control-commands"></a>Befehle zur Steuerung der Erfassung in der Kusto-Abfragesprache

Es gibt eine Reihe von Methoden, mit denen Daten mithilfe von KQL-Befehlen (Kusto Query Language) direkt in der Engine erfasst werden können. Da diese Methode die Datenverwaltungsdienste umgeht, eignet sie sich nur für die Erkundung und Prototyperstellung. Verwenden Sie diese Methode nicht in Produktionsszenarien oder Szenarien mit hohem Volumen.

  * **Inlineerfassung:**  An die Engine wird ein Steuerungsbefehl ([.ingest inline](/azure/data-explorer/kusto/management/data-ingestion/ingest-inline?context=/azure/synapse-analytics/context/context)) gesendet, und die zu erfassenden Daten werden direkt im Befehlstext angegeben. Diese Methode ist für improvisierte Testzwecke vorgesehen.

  * **Erfassung aus einer Abfrage:** An die Engine wird ein Steuerungsbefehl ([„.set“, „.append“, „.set-or-append“ oder „.set-or-replace“](/azure/data-explorer/kusto/management/data-ingestion/ingest-from-query?context=/azure/synapse-analytics/context/context)) gesendet, und die Daten werden indirekt als die Ergebnisse einer Abfrage oder eines Befehls angegeben.

  * **Erfassung aus dem Speicher (Pull):** An die Engine wird ein Steuerungsbefehl ([.ingest into](/data-explorer/kusto/management/data-ingestion/ingest-from-storage?context=/azure/synapse-analytics/context/context)) gesendet, und die Daten sind in einem externen Speicher (beispielsweise in Azure Blob Storage) gespeichert, auf den die Engine zugreifen kann und auf den durch den Befehl verwiesen wird.

Ein Beispiel für die Verwendung von Erfassungssteuerungsbefehlen finden Sie unter [Schnellstart: Analysieren mit Data Explorer (Vorschau)](../../get-started-analyze-data-explorer.md).

<!-- ## Comparing ingestion methods and tools

| Ingestion name | Data type | Maximum file size | Streaming, batching, direct | Most common scenarios | Considerations |
| --- | --- | --- | --- | --- | --- |
| [**One click ingestion**](ingest-data-one-click.md) | *sv, JSON | 1 GB uncompressed (see note)| Batching to container, local file and blob in direct ingestion | One-off, create table schema, definition of continuous ingestion with event grid, bulk ingestion with container (up to 10,000 blobs) | 10,000 blobs are randomly selected from container|
| [**LightIngest**](lightingest.md) | All formats supported | 1 GB uncompressed (see note) | Batching via DM or direct ingestion to engine |  Data migration, historical data with adjusted ingestion timestamps, bulk ingestion (no size restriction)| Case-sensitive, space-sensitive |
| [**ADX Kafka**](ingest-data-kafka.md) | | | | |
| [**ADX to Apache Spark**](spark-connector.md) | | | | |
| [**LogStash**](ingest-data-logstash.md) | | | | |
| [**Azure Data Factory (ADF)**](./data-factory-integration.md) | [Supported data formats](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | unlimited *(per ADF restrictions) | Batching or per ADF trigger | Supports formats that are usually unsupported, large files, can copy from over 90 sources, from on perm to cloud | This method takes relatively more time until data is ingested. ADF uploads all data to memory and then begins ingestion. |
|[ **Power Automate**](./flow.md) | | | | Ingestion commands as part of flow| Must have high-performing response time |
| [**IoT Hub**](ingest-data-iot-hub-overview.md) | [Supported data formats](ingest-data-iot-hub-overview.md#data-format)  | N/A | Batching, streaming | IoT messages, IoT events, IoT properties | |
| [**Event Hub**](ingest-data-event-hub-overview.md) | [Supported data formats](ingest-data-event-hub-overview.md#data-format) | N/A | Batching, streaming | Messages, events | |
| [**Event Grid**](ingest-data-event-grid-overview.md) | [Supported data formats](ingest-data-event-grid-overview.md#data-format) | 1 GB uncompressed | Batching | Continuous ingestion from Azure storage, external data in Azure storage | Ingestion can be triggered by blob renaming or blob creation actions. |
| [**.NET SDK**](./net-sdk-ingest-data.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |
| [**Python**](python-ingest-data.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |
| [**Node.js**](node-ingest-data.md) | All formats supported | 1 GB uncompressed (see note | Batching, streaming, direct | Write your own code according to organizational needs |
| [**Java**](kusto/api/java/kusto-java-client-library.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |
| [**REST**](kusto/api/netfx/kusto-ingest-client-rest.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct| Write your own code according to organizational needs |
| [**Go**](kusto/api/golang/kusto-golang-client-library.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |

> [!Note]
> When referenced in the above table, ingestion supports a maximum file size of 4 GB. The recommendation is to ingest files between 100 MB and 1 GB. -->

## <a name="ingestion-process"></a>Erfassungsprozess

Nachdem Sie die am besten geeignete Erfassungsmethode für Ihre Anforderungen ausgewählt haben, führen Sie die folgenden Schritte aus:

1. **Festlegen der Aufbewahrungsrichtlinie**

    Für Daten, die in einer Tabelle in Azure Synapse Data Explorer erfasst werden, gilt die effektive Aufbewahrungsrichtlinie der Tabelle. Wurde für eine Tabelle nicht explizit eine effektive Aufbewahrungsrichtlinie festgelegt, wird sie von der Aufbewahrungsrichtlinie der Datenbank abgeleitet. Die heiße Speicherebene für die Aufbewahrung ist eine Funktion aus der Clustergröße und Ihrer Aufbewahrungsrichtlinie. Wenn Sie mehr Daten erfassen, als Speicherplatz verfügbar ist, werden die zuerst erfassten Daten in die kalte Aufbewahrung überführt.

    Stellen Sie sicher, dass die Aufbewahrungsrichtlinie der Datenbank Ihren Anforderungen entspricht. Ist dies nicht der Fall, überschreiben Sie sie explizit auf Tabellenebene. Weitere Informationen finden Sie unter [Aufbewahrungsrichtlinie](/azure/data-explorer/kusto/management/retentionpolicy?context=/azure/synapse-analytics/context/context).

1. **Erstellen einer Tabelle**

    Um Daten zu erfassen, muss vorab eine Tabelle erstellt werden. Nutzen Sie eine der folgenden Optionen:

    * Erstellen einer Tabelle mit einem Befehl Ein Beispiel für die Verwendung des Befehls für die Tabellenerstellung finden Sie unter [Schnellstart: Analysieren mit Data Explorer (Vorschau)](../../get-started-analyze-data-explorer.md).

    * Erstellen Sie eine Tabelle mit der [1-Klick-Erfassung](data-explorer-ingest-data-one-click.md).

    > [!Note]
    > Wenn ein Datensatz unvollständig ist oder ein Feld nicht als erforderlicher Datentyp analysiert werden kann, werden die entsprechenden Tabellenspalten mit Nullwerten gefüllt.

1. **Erstellen einer Schemazuordnung**

    Die [Schemazuordnung](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context) hilft dabei, Quelldatenfelder an Spalten der Zieltabelle zu binden. Die Zuordnung ermöglicht Ihnen, Daten aus verschiedenen Quellen basierend auf den definierten Attributen in dieselbe Tabelle zu übernehmen. Es werden unterschiedliche Zuordnungstypen unterstützt – sowohl zeilenorientiert (CSV, JSON und Avro) als auch spaltenorientiert (Parquet). Bei den meisten Methoden können Zuordnungen auch [vorab in der Tabelle erstellt](/azure/data-explorer/kusto/management/create-ingestion-mapping-command?context=/azure/synapse-analytics/context/context) und vom Erfassungsbefehlsparameter referenziert werden.

1. **Einrichten einer Updaterichtlinie** (optional)

   Einige der Datenformatzuordnungen (Parquet, JSON und Avro) unterstützen einfache und nützliche Transformationen während der Erfassung. Wenn für das Szenario eine komplexere Verarbeitung zur Erfassungszeit erforderlich ist, verwenden Sie eine Updaterichtlinie, die eine einfache Verarbeitung mithilfe von Befehlen der Kusto-Abfragesprache ermöglicht. Die Updaterichtlinie führt automatisch Extraktionen und Transformationen der erfassten Daten in der ursprünglichen Tabelle aus und erfasst die resultierenden Daten in einer oder mehreren Zieltabellen. Legen Sie Ihre [Updaterichtlinie](/azure/data-explorer/kusto/management/update-policy?context=/azure/synapse-analytics/context/context) fest.



## <a name="next-steps"></a>Nächste Schritte

- [Unterstützte Datenformate](data-explorer-ingest-data-supported-formats.md)
- [Unterstützte Erfassungseigenschaften](data-explorer-ingest-data-properties.md)
