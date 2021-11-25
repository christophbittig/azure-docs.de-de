---
title: Includedatei
description: Includedatei
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 72c98f01224e70febb5ae6189682f1275ca811f6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132353858"
---
## <a name="what-is-a-replication-task"></a>Was ist eine Replikationsaufgabe?

Eine Replikationsaufgabe empfängt Ereignisse von einer Quelle und leitet Sie an ein Ziel weiter. Die meisten Replikationsaufgaben leiten Ereignisse unverändert weiter und führen höchstens eine Zuordnung zwischen Metadatenstrukturen aus, wenn sich die Quell- und Zielprotokolle unterscheiden.

Replikationsaufgaben sind in der Regel zustandslos, was bedeutet, dass sie keinen Status oder andere Nebeneffekte über sequenzielle oder parallele Ausführungen einer Aufgabe hinweg weitergeben. Dies trifft auch auf Batchverarbeitung und Verkettung zu, die beide auf Grundlage des vorhandenen Zustands eines Datenstroms implementiert werden können.

Dadurch unterscheiden sich Replikationsaufgaben von Aggregationsaufgaben, die im allgemeinen zustandsbehaftet sind und die Domäne von Analyseframeworks und -diensten wie [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) sind.

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Replikationsanwendungen und -aufgaben in Azure Functions

In Azure Functions wird eine Replikationsaufgabe mit einem [Trigger](../articles/azure-functions/functions-triggers-bindings.md) implementiert, der Eingabemeldungen von einer konfigurierten Quelle erfasst, und eine [Ausgabebindung](../articles/azure-functions/functions-triggers-bindings.md#binding-direction), die von der Quelle kopierte Meldungen an ein konfiguriertes Ziel weiterleitet.

| Trigger  | Output |
|----------|--------|
| [Azure Event Hubs-Trigger](../articles/azure-functions/functions-bindings-event-hubs-trigger.md?tabs=csharp) | [Azure Event Hubs-Ausgabebindung](../articles/azure-functions/functions-bindings-event-hubs-output.md?tabs=csharp) |
| [Azure Service Bus-Trigger](../articles/azure-functions/functions-bindings-service-bus-trigger.md?tabs=csharp) | [Azure Service Bus-Ausgabebindung](../articles/azure-functions/functions-bindings-service-bus-output.md?tabs=csharp) |
| [Azure IoT Hub-Trigger](../articles/azure-functions/functions-bindings-event-iot-trigger.md?tabs=csharp) | [Azure IoT Hub-Ausgabebindung](../articles/azure-functions/functions-bindings-event-iot-output.md?tabs=csharp) |
| [Azure Event Grid-Trigger](../articles/azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp) | [Azure Event Grid-Ausgabebindung](../articles/azure-functions/functions-bindings-event-grid-output.md?tabs=csharp) |
| [Azure Queue Storage-Trigger](../articles/azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp) | [Azure Queue Storage-Ausgabebindung](../articles/azure-functions/functions-bindings-storage-queue-output.md?tabs=csharp) |
| [Apache Kafka-Trigger](https://github.com/azure/azure-functions-kafka-extension) | [Apache Kafka-Ausgabebindung](https://github.com/azure/azure-functions-kafka-extension) |
| [RabbitMQ-Trigger](https://github.com/azure/azure-functions-rabbitmq-extension) | [RabbitMQ-Ausgabebindung](https://github.com/azure/azure-functions-rabbitmq-extension) |
| | [Azure Notification Hubs-Ausgabebindung](../articles/azure-functions/functions-bindings-notification-hubs.md) |
| | [Azure SignalR-Dienstausgabebindung](../articles/azure-functions/functions-bindings-signalr-service-output.md?tabs=csharp) |
| | [Twilio SendGrid-Ausgabebindung](../articles/azure-functions/functions-bindings-sendgrid.md?tabs=csharp) |

Replikationsaufgaben werden mithilfe derselben Bereitstellungsmethoden in der Replikationsanwendung bereitgestellt wie jede andere Azure Functions-Anwendung. Sie können mehrere Aufgaben in derselben Anwendung konfigurieren.

Mit Azure Functions Premium können mehrere Replikationsanwendungen denselben zugrunde liegenden Ressourcenpool gemeinsam nutzen, der als App Service Plan bezeichnet wird. Dies bedeutet, dass Sie in .NET geschriebene Replikationsaufgaben problemlos mit Replikationsaufgaben zusammenstellen können, die beispielsweise in Java geschrieben sind. Dies ist wichtig, wenn Sie bestimmte Bibliotheken, wie z. B. Apache Camel, nutzen möchten, die nur für Java verfügbar sind, und wenn diese die beste Option für einen bestimmten Integrationspfad darstellen, auch wenn Sie prinzipiell eine andere Sprache und Laufzeit für Ihre anderen Replikationsaufgaben bevorzugen würden.

Wann immer möglich, sollten Sie die batchorientierten Trigger den Triggern vorziehen, die einzelne Ereignisse oder Nachrichten übermitteln, und Sie sollten immer die gesamte Ereignis- oder Nachrichtenstruktur abrufen, anstatt sich auf die [Parameterbindungsausdrücke](../articles/azure-functions/functions-bindings-expressions-patterns.md) der Azure-Funktion zu verlassen.

Der Name der Funktion sollte das Paar aus Quelle und Ziel widerspiegeln, mit dem Sie eine Verbindung herstellen, und Sie sollten Verweise auf Verbindungszeichenfolgen oder andere Konfigurationselemente in den Anwendungskonfigurationsdateien mit diesem Namen als Präfix angeben.

### <a name="data-and-metadata-mapping"></a>Zuordnung von Daten und Metadaten

Nachdem Sie sich für ein Paar aus Eingabetrigger und Ausgabebindung entschieden haben, müssen Sie eine Zuordnung zwischen den verschiedenen Ereignis- oder Nachrichtentypen durchführen, es sei denn, der Typ Ihres Triggers und die Ausgabe sind identisch.

Bei einfachen Replikationsaufgaben, die Nachrichten zwischen Event Hubs und Service Bus kopieren, müssen Sie keinen eigenen Code schreiben, sondern können eine [Hilfsprogrammbibliothek verwenden, die ](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) zusammen mit den Replikationsbeispielen bereitgestellt wird.

### <a name="retry-policy"></a>Wiederholungsrichtlinie

Um Datenverluste während eines Verfügbarkeitsereignisses auf beiden Seiten einer Replikationsfunktion zu vermeiden, müssen Sie die Wiederholungsrichtlinie so konfigurieren, dass sie robust ist. Informationen zum Konfigurieren der Wiederholungsrichtlinie finden Sie in der [Azure Functions-Dokumentation zu Wiederholungen](../articles/azure-functions/functions-bindings-error-pages.md).

Die für die Beispielprojekte im [Beispielrepository](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) gewählten Richtlinieneinstellungen konfigurieren eine exponentielle Backoff-Strategie mit Wiederholungsintervallen zwischen 5 Sekunden und 15 Minuten mit unendlichen Wiederholungen, um Datenverluste zu vermeiden.

Um die Interaktion zwischen Triggern und der für die Warteschlange definierten maximalen Zustellungsanzahl in Service Bus zu verstehen, lesen Sie den Abschnitt [„Verwenden der Wiederholungsunterstützung zusätzlich zur Ausfallsicherheit des Auslösers“](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience).

### <a name="setting-up-a-replication-application-host"></a>Einrichten eines Replikationsanwendungshosts

Eine Replikationsanwendung ist ein Ausführungshost für eine oder mehrere Replikationsaufgaben.

Es handelt sich um eine Azure Functions-Anwendung, die so konfiguriert ist, dass sie entweder im Verbrauchsplan oder in einem Premium-Plan für Azure Functions (empfohlen) ausgeführt wird. Alle Replikationsanwendungen müssen unter einer [systemseitig oder benutzerseitig zugewiesenen verwalteten Identität](../articles/app-service/overview-managed-identity.md) ausgeführt werden.

Die verknüpften Azure Resource Manager-Vorlagen (ARM) erstellen und konfigurieren eine Replikationsanwendung mit:

* Einem Azure-Speicherkonto für die Nachverfolgung des Replikationsfortschritts und für Protokolle.
* Eine systemseitig zugewiesene verwaltete Identität.
* Azure Monitor und Application Insights-Integration zur Überwachung.

Replikationsanwendungen, die auf Event Hubs zugreifen müssen, die an ein virtuelles Azure-Netzwerk (VNet) gebunden sind, müssen den Premium-Plan für Azure Functions verwenden und so konfiguriert sein, dass sie demselben VNet angefügt werden, das ebenfalls eine der verfügbaren Optionen ist.

| | Bereitstellen | Visualisieren |
|--|--|--|
| **Azure Functions-Nutzungsplan** | [![In Azure bereitstellen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)|[![Visualisieren](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json) |
| **Premium-Plan** |[![In Azure bereitstellen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![Visualisieren](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) |
| **Premium-Plan für Azure Functions mit VNet** | [![In Azure bereitstellen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![Visualisieren](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json) |

### <a name="examples"></a>Beispiele

Das [Repository mit Beispielen](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/) enthält mehrere Beispiele für Replikationsaufgaben, die Ereignisse zwischen Event Hubs und/oder zwischen Service Bus-Entitäten kopieren.

Zum Kopieren eines Ereignisses zwischen Event Hubs verwenden Sie einen Event Hub-Trigger mit einer Event Hub-Ausgabebindung:

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

Zum Kopieren von Nachrichten zwischen Service Bus-Entitäten verwenden Sie den Service Bus-Trigger und eine Service Bus-Ausgabebindung:

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

Die Hilfsmethoden können die Replikation zwischen Event Hubs und Service Bus vereinfachen:

| `Source`      | Ziel      | Einstiegspunkt |
|-------------|-------------|------------------------------------------------------------------------
| Event Hub   | Event Hub   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Event Hub   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Event Hub   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="monitoring"></a>Überwachung

Informationen dazu, wie Sie Ihre Replikations-App überwachen können, finden Sie im [Abschnitt „Überwachung“](../articles/azure-functions/configure-monitoring.md) der Azure Functions-Dokumentation.

Ein besonders nützliches visuelles Tool für die Überwachung von Replikationsaufgaben ist die [Anwendungsübersicht](../articles/azure-monitor/app/app-map.md) von Application Insights, die automatisch aus den erfassten Überwachungsinformationen generiert wird und das Untersuchen der Zuverlässigkeit und Leistung der Übertragungen zwischen Replikationsaufgabenquelle und -Ziel ermöglicht.

Für sofortige Diagnoseerkenntnise können Sie mit dem Portaltool [Livemetriken](../articles/azure-monitor/app/live-stream.md) arbeiten, das Visualisierung von Protokolldetails mit geringen Wartezeiten bereitstellt.

## <a name="next-steps"></a>Nächste Schritte

* [Azure Functions-Bereitstellungen](../articles/azure-functions/functions-deployment-technologies.md)
* [Azure Functions-Diagnose](../articles/azure-functions/functions-diagnostics.md)
* [Azure Functions-Netzwerkoptionen](../articles/azure-functions/functions-networking-options.md)
* [Azure Application Insights](../articles/azure-monitor/app/app-insights-overview.md)
