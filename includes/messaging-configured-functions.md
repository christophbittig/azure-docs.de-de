---
title: Datei einfügen
description: include file
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f4d7b9d272f097f5be332c8ff0acdf7cb30395ea
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131571026"
---
Azure Functions ermöglicht das Erstellen von auf Konfigurationen beschränkten Replikationsaufgaben, die auf einem vordefinierten Einstiegspunkt basieren. Die [konfigurationsbasierten Replikationsbeispiele für Azure Functions](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config) veranschaulichen, wie [vorgefertigte Hilfsprogramme](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) in Ihrem eigenen Code genutzt werden können, oder wie damit vermieden werden kann, Code überhaupt anfassen zu müssen und nur die Konfiguration zu verwenden.

## <a name="create-a-replication-task"></a>Erstellen eines Replikationsauftrags
Um eine solche Replikationsaufgabe zu erstellen, erstellen Sie zunächst einen neuen Ordner unterhalb des Projektordners. Der Name des neuen Ordners ist der Name der Funktion, beispielsweise `europeToAsia` oder `telemetry`. Der Name hat keinen direkten Bezug zu den verwendeten Messaging-Entitäten und dient nur zur Identifizierung. Sie können Dutzende von Funktionen im selben Projekt erstellen.

Erstellen Sie als Nächstes im Ordner eine Datei `function.json`. Die Datei konfiguriert die Funktion. Beginnen Sie mit folgendem Inhalt:

```json
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "name": "input" 
        },
        {
            "direction": "out",
            "name": "output"
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": -1,
        "minimumInterval": "00:00:05",
        "maximumInterval": "00:05:00"
    },
    "disabled": false,
    "scriptFile": "../bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.*"
}
```

In dieser Datei müssen Sie drei Konfigurationsschritte ausführen, die von den Entitäten abhängen, mit denen Sie eine Verbindung herstellen möchten:

1. [Konfigurieren des Eingabeverzeichnisses](#configure-the-input-direction)
2. [Konfigurieren des Ausgabeverzeichnisses](#configure-the-output-direction)
3. [Konfigurieren des Einstiegspunkts](#configure-the-entry-point)

### <a name="configure-the-input-direction"></a>Konfigurieren des Eingabeverzeichnisses

#### <a name="event-hub-input"></a>Event Hub-Eingabe

Wenn Sie Ereignisse von einem Event Hub empfangen möchten, fügen Sie dem obersten Abschnitt in „Bindungen“ Konfigurationsinformationen hinzu, die Folgendes festlegen:

* **type**: Der Typ „eventHubTrigger“.
* **connection**: Der Name des Werts für die App-Einstellungen für die Event Hub-Verbindungszeichenfolge. 
* **eventHubName**: Der Name des Event Hubs innerhalb des Namespace, der durch die Verbindungszeichenfolge identifiziert wird.
* **consumerGroup**: Der Name der Consumergruppe. Beachten Sie, dass der Name in Prozentzeichen eingeschlossen ist und daher auch auf einen App-Einstellungswert verweist.

```json
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "eventHubTrigger",
            "connection": "functionname-source-connection",
            "eventHubName": "eventHubA",
            "consumerGroup" : "%functionname-source-consumergroup%",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-queue-input"></a>Service Bus-Warteschlangeneingabe

Wenn Sie Ereignisse von einer Service Bus-Warteschlange empfangen möchten, fügen Sie dem obersten Abschnitt in „Bindungen“ Konfigurationsinformationen hinzu, die Folgendes festlegen:

* **type**: Der Typ „serviceBusTrigger“.
* **connection**: Der Name des Werts für die App-Einstellungen für die Service Bus-Verbindungszeichenfolge.
* **queueName**: Der Name der Service Bus-Warteschlange innerhalb des Namespace, der durch die Verbindungszeichenfolge identifiziert wird.

```json
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "queueName": "queue-a",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-topic-input"></a>Service Bus-Themaeingabe

Wenn Sie Ereignisse von einem Service Bus-Thema empfangen möchten, fügen Sie dem obersten Abschnitt in „Bindungen“ Konfigurationsinformationen hinzu, die Folgendes festlegen:

* **type**: Der Typ „serviceBusTrigger“.
* **connection**: Der Name des Werts für die App-Einstellungen für die Service Bus-Verbindungszeichenfolge. Dieser Wert muss `{FunctionName}-source-connection` sein, wenn Sie die bereitgestellten Skripts verwenden möchten.
* **topicName**: Der Name des Service Bus-Themas innerhalb des Namespace, der durch die Verbindungszeichenfolge identifiziert wird.
* **subscriptionName**: Der Name des Service Bus-Abonnements in dem angegebenen Thema innerhalb des Namespace, der durch die Verbindungszeichenfolge identifiziert wird.

```json
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "topicName": "topic-x",
            "subscriptionName" : "sub-y",
            "name": "input" 
        }
    ...
```

### <a name="configure-the-output-direction"></a>Konfigurieren des Ausgabeverzeichnisses

#### <a name="event-hub-output"></a>Event Hub-Ausgabe

Wenn Sie Ereignisse an einen Event Hub weiterleiten möchten, fügen Sie dem untersten Abschnitt in „Bindungen“ Konfigurationsinformationen hinzu, die Folgendes festlegen:

* **type**: Der Typ „eventHub“.
* **connection**: Der Name des Werts für die App-Einstellungen für die Event Hub-Verbindungszeichenfolge. 
* **eventHubName**: Der Name des Event Hubs innerhalb des Namespace, der durch die Verbindungszeichenfolge identifiziert wird.

```json
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "functionname-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-queue-output"></a>Service Bus-Warteschlangenausgabe

Wenn Sie Ereignisse an eine Service Bus-Warteschlange weiterleiten möchten, fügen Sie dem untersten Abschnitt in „Bindungen“ Konfigurationsinformationen hinzu, die Folgendes festlegen:

* **type**: Der Typ „serviceBus“.
* **connection**: Der Name des Werts für die App-Einstellungen für die Service Bus-Verbindungszeichenfolge. 
* **queueName**: Der Name der Service Bus-Warteschlange innerhalb des Namespace, der durch die Verbindungszeichenfolge identifiziert wird.

```json
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "queueName": "queue-b",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-topic-output"></a>Service Bus-Themaausgabe

Wenn Sie Ereignisse an ein Service Bus-Thema weiterleiten möchten, fügen Sie dem untersten Abschnitt in „Bindungen“ Konfigurationsinformationen hinzu, die Folgendes festlegen:

* **type**: Der Typ „serviceBus“.
* **connection**: Der Name des Werts für die App-Einstellungen für die Service Bus-Verbindungszeichenfolge. 
* **topicName**: Der Name des Service Bus-Themas innerhalb des Namespace, der durch die Verbindungszeichenfolge identifiziert wird.

```json
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "topicName": "topic-b",
            "name": "output" 
        }
    ...
```

### <a name="configure-the-entry-point"></a>Konfigurieren des Einstiegspunkts

Die Konfiguration des Einstiegspunkts wählt eine der Standardreplikationsaufgaben aus. Wenn Sie das `Azure.Messaging.Replication`-Projekt ändern, können Sie auch Aufgaben hinzufügen und hier darauf verweisen. Beispiel:

```json
    ...
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub"
    ...
```

In der folgenden Tabelle finden Sie die richtigen Werte für Kombinationen aus Quellen und Zielen:

| `Source`      | Ziel      | Einstiegspunkt 
|-------------|-------------|------------------------------------------------------------------------
| Event Hub   | Event Hub   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Event Hub   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Event Hub   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="retry-policy"></a>Wiederholungsrichtlinie

Informationen zum Konfigurieren der Wiederholungsrichtlinie finden Sie in der [Azure Functions-Dokumentation zu Wiederholungen](../articles/azure-functions/functions-bindings-error-pages.md). Die in allen Projekten in diesem Repository gewählten Richtlinieneinstellungen konfigurieren eine exponentielle Backoff-Strategie mit Wiederholungsintervallen zwischen 5 Sekunden und 5 Minuten mit unendlichen Wiederholungen, um Datenverluste zu vermeiden.

Um die Interaktion zwischen Triggern und der für die Warteschlange definierten maximalen Zustellungsanzahl in Service Bus zu verstehen, lesen Sie den Abschnitt [„Verwenden der Wiederholungsunterstützung zusätzlich zur Ausfallsicherheit des Auslösers“](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience).

### <a name="build-deploy-and-configure"></a>Erstellen, Bereitstellen und Konfigurieren

Während Sie sich auf die Konfiguration konzentrieren, erfordern die Aufgaben weiterhin das Erstellen einer bereitstellbaren Anwendung und das Konfigurieren der Azure Functions-Hosts in einer Form, dass sie über alle erforderlichen Informationen zum Herstellen einer Verbindung mit den angegebenen Endpunkten verfügen. 

Dies wird in Verbindung mit wiederverwendbaren Skripts in den [konfigurationsbasierten Replikationsbeispielen für Azure Functions](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config) veranschaulicht.
