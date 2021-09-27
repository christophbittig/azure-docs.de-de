---
title: 'Azure Service Bus: Nachrichtenanzahl'
description: Rufen Sie die Anzahl der Nachrichten, die in Warteschlangen und Abonnements gespeichert sind, mit Azure Resource Manager und den NamespaceManager-APIs von Service Bus ab.
ms.topic: article
ms.date: 09/20/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 74038f38f1faca90e4fab7a0d4f94229a1b44429
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576059"
---
# <a name="get-message-counters"></a>Abrufen von Nachrichtenzählern
In diesem Artikel werden verschiedene Möglichkeiten zum Abrufen der folgenden Nachrichtenanzahlen für eine Warteschlange oder ein Abonnement erläutert. Die Anzahl aktiver Nachrichten zu kennen ist nützlich, um festzustellen, ob eine Warteschlange einen Rückstand aufbaut, der mehr Ressourcen für die Verarbeitung benötigt als diejenigen, die derzeit bereitgestellt sind. 

| Leistungsindikator | BESCHREIBUNG |
| ----- | ---------- | 
| ActiveMessageCount | Anzahl von Nachrichten in der Warteschlange oder im Abonnement, die sich im aktiven Zustand befinden und zustellbereit sind. |
| ScheduledMessageCount | Anzahl von Nachrichten im Zustand „Geplant“. |
| DeadLetterMessageCount | Anzahl von Nachrichten in der Warteschlange für unzustellbare Nachrichten. |
| TransferMessageCount | Anzahl von Nachrichten, deren Übertragung in eine andere Warteschlange oder ein anderes Thema aussteht. |
| TransferDeadLetterMessageCount | Anzahl von Nachrichten, die nicht in eine andere Warteschlange oder ein anderes Thema übertragen und in die Warteschlange für unzustellbare Nachrichten verschoben wurden. |

Wenn eine Anwendung Ressourcen anhand der Länge der Warteschlange skalieren möchte, sollte sie dies langsam tun. Die Erfassung der Nachrichtenzählern ist ein aufwendiger Vorgang innerhalb des Nachrichtenbrokers, dessen Ausführung häufig direkte und negative Auswirkungen auf die Leistung der Entität hat.

> [!NOTE]
> Die an ein Service Bus-Thema gesendeten Nachrichten werden an Abonnements für dieses Thema weitergeleitet. Somit ist die Anzahl aktiver Nachrichten für das Thema selbst 0, da diese Nachrichten erfolgreich an das Abonnement weitergeleitet wurden. Ermitteln Sie die Anzahl der Nachrichten für das Abonnement, und überprüfen Sie, ob sie größer als 0 ist. Auch wenn Nachrichten im Abonnement angezeigt werden, befinden sie sich tatsächlich in einem Speicher, der im Besitz des Themas ist. Wenn Sie die Abonnements anzeigen, ist die Anzahl der Nachrichten im Abonnement ungleich 0 (null) (und beanspruchen einen Teil des Speicherplatzes von 323 MB für die gesamte Entität).


## <a name="using-azure-portal"></a>Verwenden des Azure-Portals
Navigieren Sie zu Ihrem Namespace, und wählen Sie die Warteschlange aus. Auf der Seite **Übersicht** für die Warteschlange werden Nachrichtenzähler angezeigt.

:::image type="content" source="./media/message-counters/queue-overview.png" alt-text="Nachrichtenzähler auf der Übersichtsseite für die Warteschlange":::

Navigieren Sie zu Ihrem Namespace, wählen Sie das Thema aus, und wählen Sie dann das Abonnement für das Thema aus. Auf der Seite **Übersicht** für die Warteschlange werden Nachrichtenzähler angezeigt.

:::image type="content" source="./media/message-counters/subscription-overview.png" alt-text="Nachrichtenzähler auf der Übersichtsseite für das Abonnement":::

## <a name="using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle
Verwenden Sie den Befehl [`az servicebus queue show`](/cli/azure/servicebus/queue#az_servicebus_queue_show), um die Details zur Nachrichtenanzahl für eine Warteschlange abzurufen, wie im folgenden Beispiel gezeigt. 

```azurecli-interactive
az servicebus queue show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --query countDetails
```

Hier ist eine Beispielausgabe:

```bash
ActiveMessageCount    DeadLetterMessageCount    ScheduledMessageCount    TransferMessageCount    TransferDeadLetterMessageCount
--------------------  ------------------------  -----------------------  ----------------------  --------------------------------
0                     0                         0                        0                       0
```

Verwenden Sie den Befehl [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show), um die Details zur Nachrichtenanzahl für ein Abonnement abzurufen, wie im folgenden Beispiel gezeigt. 

```azurecli-interactive
az servicebus topic subscription show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysub \
    --query countDetails
```

## <a name="using-azure-powershell"></a>Verwenden von Azure PowerShell
Mit PowerShell können Sie die Details zur Nachrichtenanzahl für eine Warteschlange wie folgt abrufen:

```azurepowershell-interactive
$queueObj=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
                    -NamespaceName mynamespace `
                    -QueueName myqueue 

$queueObj.CountDetails
```

Hier ist die Beispielausgabe:

```bash
ActiveMessageCount             : 7
DeadLetterMessageCount         : 1
ScheduledMessageCount          : 3
TransferMessageCount           : 0
TransferDeadLetterMessageCount : 0
```

Sie können die Details zur Nachrichtenanzahl für ein Abonnement wie folgt abrufen:

```azurepowershell-interactive
$topicObj= Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
                -NamespaceName mynamespace `
                -TopicName mytopic `
                -SubscriptionName mysub

$topicObj.CountDetails
```

Das zurückgegebene `MessageCountDetails`-Objekt weist die folgenden Eigenschaften auf: `ActiveMessageCount`, `DeadLetterMessageCount`, `ScheduledMessageCount`, `TransferDeadLetterMessageCount`, `TransferMessageCount`. 

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die Beispiele in der Sprache Ihrer Wahl an, um Azure Service Bus-Features zu untersuchen. 

- [Azure Service Bus-Clientbibliothekbeispiele für .NET (neueste Version)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) 
- [Azure Service Bus-Clientbibliothekbeispiele für Java (neueste Version)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus-Clientbibliothekbeispiele für Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus-Clientbibliothekbeispiele für JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus-Clientbibliothekbeispiele für TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

Hier finden Sie Beispiele für die älteren .NET- und Java-Clientbibliotheken:
- [Azure Service Bus-Clientbibliothekbeispiele für .NET (Legacy)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Azure Service Bus-Clientbibliothekbeispiele für Java (Legacy)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus)
