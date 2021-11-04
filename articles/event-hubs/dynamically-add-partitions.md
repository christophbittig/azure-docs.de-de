---
title: Dynamisches Hinzufügen von Partitionen zu einem Event Hub in Azure Event Hubs
description: In diesem Artikel erfahren Sie, wie Sie einem Event Hub in Azure Event Hubs dynamisch Partitionen hinzufügen.
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fdd87e281bb4df98d118f7964eaa98925cef0947
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070677"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>Dynamisches Hinzufügen von Partitionen zu einem Event Hub (Apache Kafka-Thema) in Azure Event Hubs
Event Hubs bietet Nachrichtenstreaming über ein partitioniertes Consumermuster, in dem jeder Consumer nur eine bestimmte Teilmenge oder Partition des Nachrichtenstreams liest. Dieses Muster ermöglicht eine horizontale Skalierung für die Ereignisverarbeitung und bietet andere datenstrombezogene Features, die in Warteschlangen und Themen nicht verfügbar sind. Eine Partition ist eine geordnete Sequenz von Ereignissen, die in einem Event Hub besteht. Neu eingehende Ereignisse werden am Ende dieser Sequenz hinzugefügt. Weitere Informationen zu Partitionen im Allgemeinen finden Sie unter [Partitionen](event-hubs-scalability.md#partitions).

Sie können die Anzahl der Partitionen zum Zeitpunkt der Erstellung eines Event Hub angeben. In einigen Szenarien müssen Sie Partitionen möglicherweise nach der Erstellung des Event Hub hinzufügen. In diesem Artikel wird beschrieben, wie Sie einem vorhandenen Event Hub dynamisch Partitionen hinzufügen. 

> [!IMPORTANT]
> Dynamische Hinzufügungen von Partitionen sind nur in **Premium**- und **dedizierten** Tarifen von Event Hubs verfügbar. 

> [!NOTE]
> Bei Apache Kafka-Clients wird ein **Event Hub** einem **Kafka-Thema** zugeordnet. Weitere Zuordnungen zwischen Azure Event Hubs und Apache Kafka finden Sie unter [Konzeptionelle Zuordnung zwischen Kafka und Event Hubs](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping).


## <a name="update-the-partition-count"></a>Aktualisieren der Partitionsanzahl
In diesem Abschnitt wird erläutert, wie Sie die Partitionsanzahl eines Event Hub auf unterschiedliche Weise (PowerShell, Befehlszeilenschnittstelle usw.) aktualisieren.

### <a name="powershell"></a>PowerShell
Verwenden Sie den PowerShell-Befehl [Set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub), um Partitionen in einem Event Hub zu aktualisieren. 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>Befehlszeilenschnittstelle (CLI)
Verwenden Sie den CLI-Befehl [`az eventhubs eventhub update`](/cli/azure/eventhubs/eventhub#az_eventhubs_eventhub_update), um Partitionen in einem Event Hub zu aktualisieren. 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Resource Manager-Vorlage
Aktualisieren Sie den Wert der Eigenschaft `partitionCount` in der Resource Manager-Vorlage, und stellen Sie die Vorlage erneut bereit, um die Ressource zu aktualisieren. 

```json
    {
        "apiVersion": "2017-04-01",
        "type": "Microsoft.EventHub/namespaces/eventhubs",
        "name": "[concat(parameters('namespaceName'), '/', parameters('eventHubName'))]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[resourceId('Microsoft.EventHub/namespaces', parameters('namespaceName'))]"
        ],
        "properties": {
            "messageRetentionInDays": 7,
            "partitionCount": 12
        }
    }
```

### <a name="apache-kafka"></a>Apache Kafka
Verwenden Sie die API `AlterTopics` (z. B. über das CLI-Tool **kafka-topics**), um die Partitionsanzahl zu erhöhen. Weitere Informationen finden Sie unter [Modifying Kafka topics](http://kafka.apache.org/documentation/#basic_ops_modify_topic) (Ändern von Kafka-Themen). 

## <a name="event-hubs-clients"></a>Event Hubs-Clients
Sehen wir uns das Verhalten von Event Hubs-Clients an, wenn die Partitionsanzahl für einen Event Hub aktualisiert wird. 

Wenn Sie einem vorhandenen Event Hub eine Partition hinzufügen, empfängt der Event Hub-Client eine `MessagingException` vom Dienst, mit der die Clients informiert werden, dass Entitätsmetadaten geändert wurden. (Ihr Event Hub ist die Entität, und die Partitionsinformationen sind die Metadaten.) Die AMQP-Links werden von den Clients automatisch erneut geöffnet, sodass die geänderten Metadateninformationen abgerufen werden. Die Clients werden dann normal ausgeführt.

### <a name="senderproducer-clients"></a>Sender-/Producerclients
Event Hubs umfasst drei Senderoptionen:

- **Partitionssender:** In diesem Szenario senden Clients Ereignisse direkt an eine Partition. Obwohl Partitionen identifizierbar sind und Ereignisse direkt an sie gesendet werden können, wird von der Verwendung dieses Musters abgeraten. Das Hinzufügen von Partitionen hat keine Auswirkung auf dieses Szenario. Es wird empfohlen, Anwendungen neu zu starten, damit neu hinzugefügte Partitionen erkannt werden können. 
- **Partitionsschlüsselsender:** In diesem Szenario senden Clients die Ereignisse mit einem Schlüssel, sodass sich alle zu dem Schlüssel gehörenden Ereignisse in derselben Partition befinden. In diesem Fall erstellt der Dienst einen Hashwert für den Schlüssel und leitet ihn an die entsprechende Partition weiter. Die Aktualisierung der Partitionsanzahl kann durch Hashänderungen zu Problemen wegen falscher Reihenfolge führen. Wenn Ihnen die Reihenfolge wichtig ist, sollten Sie daher sicherstellen, dass in der Anwendung alle Ereignisse aus vorhandenen Partitionen verarbeitet werden, bevor Sie die Partitionsanzahl erhöhen.
- **Roundrobinsender (Standard):** In diesem Szenario werden die Ereignisse im Event Hubs-Dienst mittels Roundrobinverfahren partitionsübergreifend gesendet. Zusätzlich wird ein Lastenausgleichsalgorithmus verwendet. Der Event Hubs-Dienst erkennt Änderungen der Partitionsanzahl und sendet Ereignisse innerhalb von wenigen Sekunden nach einer Änderung der Partitionsanzahl an die neuen Partitionen.

### <a name="receiverconsumer-clients"></a>Empfänger-/Consumerclients
Event Hubs umfasst direkte Empfänger und eine einfache Consumerbibliothek, die als [Ereignisprozessorhost (altes SDK)](event-hubs-event-processor-host.md) oder [Ereignisprozessor (neues SDK)](event-processor-balance-partition-load.md) bezeichnet wird.

- **Direkte Empfänger:** Die direkten Empfänger lauschen auf spezifische Partitionen. Das zugehörige Laufzeitverhalten ist nicht betroffen, wenn Partitionen für einen Event Hub aufskaliert werden. Die Anwendung, in der direkte Empfänger verwendet werden, muss die neuen Partitionen abrufen und die Empfänger entsprechend zuweisen.
- **Ereignisprozessorhost:** Dieser Client aktualisiert die Entitätsmetadaten nicht automatisch. Daher wird eine Erhöhung der Partitionsanzahl nicht abgerufen. Die Neuerstellung einer Ereignisprozessorinstanz führt zum Abruf der Entitätsmetadaten, der wiederum zur Erstellung neuer Blobs für die neu hinzugefügten Partitionen führt. Dies wirkt sich nicht auf bereits vorhandene Blobs aus. Es wird empfohlen, alle Ereignisprozessorinstanzen neu zu starten, um sicherzustellen, dass alle Instanzen die neu hinzugefügten Partitionen erkennen und der Lastenausgleich für die Consumer ordnungsgemäß erfolgt.

    Wenn Sie die alte Version des .NET SDK ([WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)) verwenden, entfernt der Ereignisprozessorhost einen vorhandenen Prüfpunkt nach dem Neustart, wenn die Partitionsanzahl im Prüfpunkt nicht mit der vom Dienst abgerufenen Partitionsanzahl übereinstimmt. Dieses Verhalten kann sich auf Ihre Anwendung auswirken. 

## <a name="apache-kafka-clients"></a>Apache Kafka-Clients
In diesem Abschnitt wird das Verhalten von Apache Kafka-Clients beschrieben, die den Kafka-Endpunkt von Azure Event Hubs verwenden, wenn die Partitionsanzahl für einen Event Hub aktualisiert wird. 

Das Verhalten von Kafka-Clients, die Event Hubs mit dem Apache Kafka-Protokoll verwenden, unterscheidet sich von dem von Event Hubs-Clients, die das AMQP-Protokoll verwenden. Kafka-Clients aktualisieren die zugehörigen Metadaten alle `metadata.max.age.ms` Millisekunden. Diesen Wert geben Sie in den Clientkonfigurationen an. Auch die `librdkafka`-Bibliotheken verwenden die gleiche Konfiguration. Bei Aktualisierungen der Metadaten werden die Clients über Dienständerungen, darunter auch über Erhöhungen der Partitionsanzahl, informiert. Eine Liste der Konfigurationen finden Sie in den [Apache Kafka-Konfigurationen für Event Hubs](apache-kafka-configurations.md).

### <a name="senderproducer-clients"></a>Sender-/Producerclients
Producer legen immer fest, dass Sendeanforderungen das Partitionsziel für jede Gruppe von erstellten Datensätzen enthalten. Daher wird die gesamte Producerpartitionierung auf Clientseite mit der Produceransicht der Brokermetadaten erstellt. Nachdem die neuen Partitionen der Metadatenansicht des Producers hinzugefügt wurden, stehen sie für Produceranforderungen zur Verfügung.

### <a name="consumerreceiver-clients"></a>Consumer-/Empfängerclients
Wenn ein Mitglied einer Consumergruppe eine Aktualisierung der Metadaten durchführt und die neu erstellten Partitionen abruft, initiiert das Mitglied einen Gruppenausgleich. Anschließend werden die Consumermetadaten für alle Gruppenmitglieder aktualisiert und die neuen Partitionen über den zugeordneten Ausgleichsleader zugewiesen.

## <a name="recommendations"></a>Empfehlungen

- Wenn Sie einen Partitionsschlüssel mit den Produceranwendungen verwenden und Schlüsselhashwerte erforderlich sind, um die Reihenfolge in einer Partition sicherzustellen, wird vom dynamischen Hinzufügen von Partitionen abgeraten. 

    > [!IMPORTANT]
    > Obwohl die Reihenfolge der vorhandenen Daten beibehalten wird, treten beim Partitionenhashing für Nachrichten, für die nach Änderungen der Partitionsanzahl ein Hashwert erstellt wird, aufgrund der Hinzufügung von Partitionen Fehler auf.
- Das Hinzufügen von Partitionen zu einem vorhandenen Thema oder einer vorhandenen Event Hubs-Instanz wird in folgenden Fällen empfohlen:
    - Beim Verwenden der Standardmethode zum Senden von Ereignissen
     - Bei standardmäßigen Kafka-Partitionierungsstrategien, z. B. der StickyAssignor-Strategie


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Partitionen finden Sie unter [Partitionen](event-hubs-scalability.md#partitions).
