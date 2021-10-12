---
title: 'Schnellstart: Senden von benutzerdefinierten Ereignissen an Event Hubs – Event Grid, Azure CLI'
description: 'Schnellstart: Verwenden Sie Azure Event Grid und die Azure CLI, um ein Thema zu veröffentlichen und dieses Ereignis zu abonnieren. Ein Event Hub wird für den Endpunkt verwendet.'
ms.date: 09/28/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: f725af009f8c2b8f8c4595865d6d746ff9f55849
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129230157"
---
# <a name="quickstart-route-custom-events-to-azure-event-hubs-with-azure-cli-and-event-grid"></a>Schnellstart: Weiterleiten benutzerdefinierter Ereignisse an Azure Event Hubs mit der Azure CLI und Event Grid

Azure Event Grid ist ein Ereignisdienst für die Cloud. Azure Event Hubs ist einer der unterstützten Ereignishandler. In diesem Artikel erstellen Sie mithilfe der Azure-Befehlszeilenschnittstelle ein benutzerdefiniertes Thema, abonnieren dieses benutzerdefinierte Thema und lösen das Ereignis zum Anzeigen des Ergebnisses aus. Sie senden Ereignisse an einen Event Hub.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Event Grid-Themen sind Azure-Ressourcen und müssen in einer Azure-Ressourcengruppe platziert werden. Die Azure-Ressourcengruppe ist eine logische Sammlung, in der Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe. 

Das folgende Beispiel erstellt eine Ressourcengruppe namens *gridResourceGroup* am Standort *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="create-a-custom-topic"></a>Erstellen eines benutzerdefinierten Themas

Ein Event Grid-Thema verfügt über einen benutzerdefinierten Endpunkt für die Veröffentlichung Ihrer Ereignisse. Im folgenden Beispiel wird das benutzerdefinierte Thema in Ihrer Ressourcengruppe erstellt. Ersetzen Sie `<your-topic-name>` durch einen eindeutigen Namen für Ihr benutzerdefiniertes Thema. Der Name des benutzerdefinierten Themas muss eindeutig sein, da er durch einen DNS-Eintrag dargestellt wird.

```azurecli-interactive
topicname=<your-topic-name>
```

```azurecli-interactive
az eventgrid topic create --name $topicname -l westus2 -g gridResourceGroup
```

## <a name="create-event-hub"></a>Event Hub erstellen

Vor dem Abonnieren des benutzerdefinierten Themas erstellen wir zunächst den Endpunkt für die Ereignisnachricht. Sie erstellen einen Event Hub zum Sammeln der Ereignisse.

```azurecli-interactive
namespace=<unique-namespace-name>
```

```azurecli-interactive
hubname=demohub

az eventhubs namespace create --name $namespace --resource-group gridResourceGroup
az eventhubs eventhub create --name $hubname --namespace-name $namespace --resource-group gridResourceGroup
```

## <a name="subscribe-to-a-custom-topic"></a>Abonnieren eines benutzerdefinierten Themas

Sie abonnieren ein Event Grid-Thema, um Event Grid mitzuteilen, welche Ereignisse Sie nachverfolgen möchten. Im folgenden Beispiel wird das von Ihnen erstellte benutzerdefinierte Thema abonniert. Außerdem wird die Ressourcen-ID des Event Hubs für den Endpunkt übergeben. Der Endpunkt weist folgendes Format auf:

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.EventHub/namespaces/<namespace-name>/eventhubs/<hub-name>`

Das folgende Skript ruft die Ressourcen-ID für den Event Hub ab und abonniert ein Event Grid-Thema. Es legt den Endpunkttyp auf `eventhub` fest und verwendet die Event Hub-ID für den Endpunkt.

```azurecli-interactive
hubid=$(az eventhubs eventhub show --name $hubname --namespace-name $namespace --resource-group gridResourceGroup --query id --output tsv)
topicid=$(az eventgrid topic show --name $topicname -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name subtoeventhub \
  --endpoint-type eventhub \
  --endpoint $hubid
```

Das Konto, das das Ereignisabonnement erstellt, muss über Schreibzugriff auf den Event Hub verfügen.

## <a name="send-an-event-to-your-custom-topic"></a>Senden eines Ereignisses an Ihr benutzerdefiniertes Thema

Nun wird ein Ereignis ausgelöst, um zu sehen, wie Event Grid die Nachricht an Ihren Endpunkt weiterleitet. Zunächst rufen wir die URL und den Schlüssel für das benutzerdefinierte Thema ab.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name $topicname -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicname -g gridResourceGroup --query "key1" --output tsv)
```

Zur Vereinfachung dieses Artikels verwenden Sie Beispielereignisdaten, die an das benutzerdefinierte Thema gesendet werden können. Üblicherweise werden die Ereignisdaten von einer Anwendung oder einem Azure-Dienst gesendet. CURL ist ein Hilfsprogramm zum Senden von HTTP-Anforderungen. In diesem Artikel wird CURL verwendet, um das Ereignis an das benutzerdefinierte Thema zu senden.  Das folgende Beispiel sendet drei Ereignisse an das Event Grid-Thema:

```azurecli-interactive
for i in 1 2 3
do
   event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
   curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
done
```

Navigieren Sie zum Event Hub im Portal, und beachten Sie, dass Event Grid diese drei Ereignisse an den Event Hub gesendet hat.

:::image type="content" source="./media/custom-event-to-eventhub/show-result.png" lightbox="./media/custom-event-to-eventhub/show-result.png" alt-text="Abbildung: Portalseite mit 3 als Anzahl eingehender Nachrichten":::

In der Regel erstellen Sie eine Anwendung, die die Ereignisse vom Event Hub abruft. Wenn Sie eine Anwendung erstellen möchten, die Nachrichten von einem Event Hub empfängt, lesen Sie die Informationen in den folgenden Artikeln:

* [Erste Schritte zum Empfangen von Nachrichten mit dem Ereignisprozessorhost (EventProcessorHost) in .NET Standard](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
* [Empfangen von Ereignissen von Azure Event Hubs mithilfe von Java](../event-hubs/event-hubs-java-get-started-send.md)
* [Empfangen von Ereignissen von Event Hubs mithilfe von Apache Storm](../event-hubs/event-hubs-storm-getstarted-receive.md)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie dieses Ereignis weiterverwenden möchten, können Sie die Bereinigung der in diesem Artikel erstellten Ressourcen überspringen. Verwenden Sie andernfalls den folgenden Befehl, um die in diesem Artikel erstellten Ressourcen zu löschen.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, wie Sie Themen und Ereignisabonnements erstellen. Nun können Sie sich ausführlicher darüber informieren, welche Möglichkeiten Event Grid bietet:

- [Einführung in Azure Event Grid](overview.md)
- [Weiterleiten von Blob Storage-Ereignissen an einen benutzerdefinierten Webendpunkt](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Überwachen von Änderungen an virtuellen Computern mit Azure Event Grid und Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Streamen von Big Data in ein Data Warehouse](event-grid-event-hubs-integration.md)

In den folgenden Beispielen erfahren Sie, wie Sie Ereignisse in Event Grid mithilfe verschiedener Programmiersprachen veröffentlichen und nutzen: 

- [Azure Event Grid-Beispiele für .NET](/samples/azure/azure-sdk-for-net/azure-event-grid-sdk-samples/)
- [Azure Event Grid-Beispiele für Java](/samples/azure/azure-sdk-for-java/eventgrid-samples/)
- [Azure Event Grid-Beispiele für Python](/samples/azure/azure-sdk-for-python/eventgrid-samples/)
- [Azure Event Grid-Beispiele für JavaScript](/samples/azure/azure-sdk-for-js/eventgrid-javascript/)
- [Azure Event Grid-Beispiele für TypeScript](/samples/azure/azure-sdk-for-js/eventgrid-typescript/)
