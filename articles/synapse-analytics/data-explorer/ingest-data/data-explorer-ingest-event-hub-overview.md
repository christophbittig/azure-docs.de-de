---
title: Event-Grid-Datenverbindung für Azure Synapse Daten-Explorer (Vorschau)
description: Dieser Artikel bietet eine Übersicht über das Erfassen (Laden) von Daten in Azure Synapse Daten-Explorer aus Event Grid.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: aaada2bd1a778fd6e5bed4ea6a42e847b3e9d983
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478773"
---
# <a name="event-hub-data-connection-preview"></a>Event Hub-Datenverbindung (Vorschau)

Bei [Azure Event Hubs](/azure/event-hubs/event-hubs-about) handelt es sich um eine Big Data-Streamingplattform und einen Ereigniserfassungsdienst. Azure Synapse Daten-Explorer bietet eine kontinuierliche Erfassung von Daten aus kundenseitig verwalteten Event Hubs.

Die Event Hub-Erfassungspipeline überträgt Ereignisse in mehreren Schritten an den Azure Synapse Daten-Explorer. Zunächst erstellen Sie im Azure-Portal einen Event Hub. Anschließend erstellen Sie eine Zieltabelle im Azure Synapse Daten-Explorer, in der die [Daten in einem bestimmten Format](#data-format) anhand der [Erfassungseigenschaften](#ingestion-properties) erfasst werden. Die Event Hub-Verbindung muss über Informationen zum [Ereignisrouting](#events-routing) verfügen. Daten werden mit ausgewählten Eigenschaften gemäß der [Eigenschaftenzuordnung des Ereignissystems](#event-system-properties-mapping) eingebettet. [Erstellen Sie eine Verbindung](#event-hub-connection) mit Event Hub, um [eine Event Hub-Instanz zu erstellen](#create-an-event-hub) und [Ereignisse](#send-events) zu senden. Dieser Prozess kann über das [Azure-Portal](data-explorer-ingest-event-hub-portal.md), programmgesteuert mit [C#](data-explorer-ingest-event-hub-csharp.md) oder [Python](data-explorer-ingest-event-hub-python.md) oder mit der [Azure Resource Manager-Vorlage](data-explorer-ingest-event-hub-resource-manager.md) verwaltet werden.

Allgemeine Informationen zur Datenerfassung im Azure Synapse Daten-Explorer finden Sie unter [Übersicht über die Datenerfassung in Azure Daten-Explorer](data-explorer-ingest-data-overview.md).

## <a name="data-format"></a>Datenformat

* Daten werden in Form von [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata)-Objekten aus dem Event Hub gelesen.
* Siehe [Unterstützte Formate](data-explorer-ingest-data-supported-formats.md).
    > [!NOTE]
    > Das RAW-Format wird von Event Hub nicht unterstützt.

* Daten können mit dem `GZip`-Komprimierungsalgorithmus komprimiert werden. Geben Sie `Compression` in den [Erfassungseigenschaften](#ingestion-properties) an.
   * Für komprimierte Formate (Avro, Parquet, ORC) wird die Datenkomprimierung nicht unterstützt.
   * Eine benutzerdefinierte Codierung und eingebettete [Systemeigenschaften](#event-system-properties-mapping) werden für komprimierte Daten nicht unterstützt.
  
## <a name="ingestion-properties"></a>Erfassungseigenschaften

Erfassungseigenschaften weisen den Erfassungsprozess an, wohin die Daten weitergeleitet und wie sie verarbeitet werden sollen. Sie können [Erfassungseigenschaften](data-explorer-ingest-data-properties.md) der Ereigniserfassung mithilfe von [EventData.Properties](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties#Microsoft_ServiceBus_Messaging_EventData_Properties) angeben. Sie können die folgenden Eigenschaften festlegen:

|Eigenschaft |Beschreibung|
|---|---|
| Tabelle | Name (unter Berücksichtigung der Groß/Kleinschreibung) der vorhandenen Zieltabelle. Überschreibt das `Table`-Element, das im Bereich `Data Connection` festgelegt ist. |
| Format | Datenformat. Überschreibt das `Data format`-Element, das im Bereich `Data Connection` festgelegt ist. |
| IngestionMappingReference | Name der zu verwendenden vorhandenen [Erfassungszuordnung](/azure/data-explorer/kusto/management/create-ingestion-mapping-command?context=/azure/synapse-analytics/context/context). Überschreibt das `Column mapping`-Element, das im Bereich `Data Connection` festgelegt ist.|
| Komprimierung | Datenkomprimierung. `None` (Standardeinstellung) oder `GZip`-Komprimierung.|
| Codierung | Datencodierung. Die Standardeinstellung ist UTF8. Alle [von .NET unterstützten Codierungen](/dotnet/api/system.text.encoding#remarks) können verwendet werden. |
| Tags | Eine Liste der [Tags](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context#extent-tagging), die den erfassten Daten zugeordnet werden sollen (formatiert als JSON-Arrayzeichenfolge). Die Verwendung von Tags hat [Auswirkungen auf die Leistung](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context#ingest-by-extent-tags). |

> [!NOTE]
> Nur Ereignisse, die nach dem Erstellen der Datenverbindung in die Warteschlange eingereiht werden, werden erfasst.

## <a name="events-routing"></a>Ereignisrouting

Beim Einrichten einer Event Hub-Verbindung mit einem Azure Synapse Daten-Explorer-Cluster geben Sie die Zieltabelleneigenschaften an (Tabellenname, Datenformat, Komprimierung und Zuordnung). Das Standardrouting für Ihre Daten wird auch als `static routing` bezeichnet.
Sie können mithilfe von Ereigniseigenschaften auch Zieltabelleneigenschaften für jedes Ereignis angeben. Die Verbindung leitet die Daten wie in [EventData.Properties](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties#Microsoft_ServiceBus_Messaging_EventData_Properties) festgelegt dynamisch weiter und setzt dabei die statischen Eigenschaften für dieses Ereignis außer Kraft.

Im folgenden Beispiel werden Event Hub-Details festgelegt und Metrikdaten zum Wetter an die Tabelle `WeatherMetrics` gesendet.
Die Daten liegen im `json`-Format vor. `mapping1` ist für die Tabelle `WeatherMetrics` vordefiniert.

```csharp
var eventHubNamespaceConnectionString=<connection_string>;
var eventHubName=<event_hub>;

// Create the data
var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = 32 }; 
var data = JsonConvert.SerializeObject(metric);

// Create the event and add optional "dynamic routing" properties
var eventData = new EventData(Encoding.UTF8.GetBytes(data));
eventData.Properties.Add("Table", "WeatherMetrics");
eventData.Properties.Add("Format", "json");
eventData.Properties.Add("IngestionMappingReference", "mapping1");
eventData.Properties.Add("Tags", "['mydatatag']");

// Send events
var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubNamespaceConnectionString, eventHubName);
eventHubClient.Send(eventData);
eventHubClient.Close();
```

## <a name="event-system-properties-mapping"></a>Zuordnung von Ereignissystemeigenschaften

Systemeigenschaften speichern vom Event Hubs-Dienst festgelegte Eigenschaften, wenn das Ereignis in die Warteschlange eingereiht wird. Die Event Hub-Verbindung mit dem Azure Synapse Daten-Explorer bettet die ausgewählten Eigenschaften in die Daten ein, die in Ihre Tabelle gelangen.

[!INCLUDE [event-hub-system-mapping](../includes/data-explorer-event-hub-system-mapping.md)]

### <a name="system-properties"></a>Systemeigenschaften

Event Hub macht die folgenden Systemeigenschaften verfügbar:

|Eigenschaft |Datentyp |Beschreibung|
|---|---|---|
| x-opt-enqueued-time |datetime | UTC-Zeit, zu der das Ereignis in die Warteschlange eingereiht wurde. |
| x-opt-sequence-number |long | Die logische Folgenummer des Ereignisses innerhalb des Partitionsdatenstroms des Event Hubs.
| x-opt-offset |Zeichenfolge | Der Offset des Ereignisses vom Event Hub-Partitionsdatenstrom. Der Offsetbezeichner ist innerhalb einer Partition des Event Hub-Datenstroms eindeutig. |
| x-opt-publisher |Zeichenfolge | Der Name des Herausgebers, wenn die Nachricht an einen Herausgeberendpunkt gesendet wurde. |
| x-opt-partition-key |Zeichenfolge |Der Partitionsschlüssel der entsprechenden Partition, in der das Ereignis gespeichert wurde. |

<!-- When you work with [IoT Central](https://azure.microsoft.com/services/iot-central/) event hubs, you can also embed IoT Hub system properties in the payload. For the complete list, see [IoT Hub system properties](ingest-data-iot-hub-overview.md#event-system-properties-mapping). -->

Wenn Sie **Ereignissystemeigenschaften** im Abschnitt **Datenquelle** der Tabelle ausgewählt haben, müssen Sie die Eigenschaften in das Tabellenschema und die Zuordnung einschließen.

[!INCLUDE [data-explorer-container-system-properties](../includes/data-explorer-container-system-properties.md)]

## <a name="event-hub-connection"></a>Event Hub-Verbindung

> [!Note]
> Die beste Leistung erzielen Sie, wenn Sie alle Ressourcen in der gleichen Region wie den Azure Synapse Daten-Explorer-Cluster erstellen.

### <a name="create-an-event-hub"></a>Erstellen eines Event Hubs

[Erstellen Sie einen Event Hub](/azure/event-hubs/event-hubs-create), sofern Sie noch keinen haben. Die Verbindungsherstellung mit Event Hub kann über das [Azure-Portal](data-explorer-ingest-event-hub-portal.md), programmgesteuert mit [C#](data-explorer-ingest-event-hub-csharp.md) oder [Python](data-explorer-ingest-event-hub-python.md) oder mit der [Azure Resource Manager-Vorlage](data-explorer-ingest-event-hub-resource-manager.md) verwaltet werden.

> [!Note]
> * Die Partitionenanzahl kann nicht geändert werden. Behalten Sie daher beim Festlegen der Partitionenanzahl die langfristige Skalierung im Hinterkopf.
> * Die Consumergruppe *muss* pro Consumer eindeutig sein. Erstellen Sie eine dedizierte Verbrauchergruppe für die Azure Synapse Daten-Explorer-Verbindung.

## <a name="send-events"></a>Senden von Ereignissen

Sehen Sie sich die [Beispiel-App](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) an, die Daten generiert und an einen Event Hub sendet.

Ein Beispiel zum Generieren von Beispieldaten finden Sie unter [Erfassen von Daten aus Event Hub in Azure Synapse Daten-Explorer](data-explorer-ingest-event-hub-portal.md#generate-sample-data)

## <a name="set-up-geo-disaster-recovery-solution"></a>Einrichten einer Lösung für die georedundante Notfallwiederherstellung

Event Hub bietet eine Lösung für die [georedundante Notfallwiederherstellung](/azure/event-hubs/event-hubs-geo-dr). Der Azure Synapse Daten-Explorer unterstützt keine Event Hub-Namespaces vom Typ `Alias`. Erstellen Sie zum Implementieren der georedundanten Notfallwiederherstellung in Ihrer Lösung zwei Event Hub-Datenverbindungen: eine für den primären Namespace und eine für den sekundären Namespace. Der Azure Synapse Daten-Explorer beobachtet beide Event Hub-Verbindungen.

> [!NOTE]
> Es liegt in der Verantwortung des Benutzers, ein Failover vom primären Namespace auf den sekundären Namespace zu implementieren.

## <a name="next-steps"></a>Nächste Schritte

- [Erfassen von Daten aus dem Event Hub in den Azure Synapse Daten-Explorer](data-explorer-ingest-event-hub-portal.md)
- [Erstellen einer Event Hub-Datenverbindung für Azure Synapse Daten-Explorer mithilfe von C#](data-explorer-ingest-event-hub-csharp.md)
- [Erstellen einer Event Hub-Datenverbindung für den Azure Synapse Daten-Explorer mithilfe von Python](data-explorer-ingest-event-hub-python.md)
- [Erstellen einer Event Hub-Datenverbindung für den Azure Synapse Daten-Explorer mithilfe einer Azure Resource Manager-Vorlage](data-explorer-ingest-event-hub-resource-manager.md)
