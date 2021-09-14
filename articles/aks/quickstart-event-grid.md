---
title: Abonnieren von Azure Kubernetes Service-Ereignissen mit Azure Event Grid (Vorschau)
description: Verwenden Sie Azure Event Grid, um Azure Kubernetes Service-Ereignisse zu abonnieren.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 07/12/2021
ms.author: zarhoads
ms.openlocfilehash: cc4801dc588216cbcfb2c6c2742643b2e0546e63
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113734006"
---
# <a name="quickstart-subscribe-to-azure-kubernetes-service-aks-events-with-azure-event-grid-preview"></a>Schnellstart: Abonnieren von Azure Kubernetes Service-Ereignissen (AKS) mit Azure Event Grid (Vorschau)

Azure Event Grid ist ein vollständig verwalteter Dienst für das Ereignisrouting, der eine einheitliche Ereignisnutzung mithilfe eines Veröffentlichen/Abonnieren-Modells bereitstellt.

In dieser Schnellstartanleitung erstellen Sie einen AKS-Cluster und abonnieren AKS-Ereignisse.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen.
* Die [Azure CLI](/cli/azure/install-azure-cli) muss installiert sein.

### <a name="register-the-eventgridpreview-preview-feature"></a>Registrieren der Previewfunktion `EventgridPreview`

Sie müssen darüber hinaus das Featureflag `EventgridPreview` in Ihrem Abonnement aktivieren, um dieses Feature zu verwenden.

Registrieren Sie das Featureflag `EventgridPreview` mithilfe des Befehls [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EventgridPreview"
```

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Überprüfen Sie den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EventgridPreview')].{Name:name,State:properties.state}"
```

Wenn der Vorgang abgeschlossen ist, können Sie die Registrierung des *Microsoft.ContainerService*-Ressourcenanbieters mit dem Befehl [az provider register][az-provider-register] aktualisieren:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="create-an-aks-cluster"></a>Erstellen eines AKS-Clusters

Erstellen Sie mit dem Befehl [az aks create][az-aks-create] einen AKS-Cluster. Im folgenden Beispiel werden eine Ressourcengruppe mit dem Namen *MyResourceGroup* und ein Cluster mit dem Namen *MyAKS* mit einem Knoten in der Ressourcengruppe *MyResourceGroup* erstellt:

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus  --node-count 1 --generate-ssh-keys
```

## <a name="subscribe-to-aks-events"></a>Abonnieren von AKS-Ereignissen

Erstellen Sie mit [az eventhubs namespace create][az-eventhubs-namespace-create] und [az eventhubs eventhub create][az-eventhubs-eventhub-create] einen Namespace und einen Event Hub. Im folgenden Beispiel wird ein Namespace mit dem Namen *MyNamespace* und ein Event-Hub mit dem Namen *MyEventGridHub* in *MyNamespace* erstellt, die beide in der Ressourcengruppe *MyResourceGroup* enthalten sind.

```azurecli
az eventhubs namespace create --location eastus --name MyNamespace -g MyResourceGroup
az eventhubs eventhub create --name MyEventGridHub --namespace-name MyNamespace -g MyResourceGroup
```

> [!NOTE]
> Der Name Ihres *Namespace* muss eindeutig sein.

Abonnieren Sie die AKS-Ereignisse mit [az eventgrid event-subscription create][az-eventgrid-event-subscription-create]:

```azurecli
SOURCE_RESOURCE_ID=$(az aks show -g MyResourceGroup -n MyAKS --query id --output tsv)
ENDPOINT=$(az eventhubs eventhub show -g MyResourceGroup -n MyEventGridHub --namespace-name MyNamespace --query id --output tsv)
az eventgrid event-subscription create --name MyEventGridSubscription \
--source-resource-id $SOURCE_RESOURCE_ID \
--endpoint-type eventhub \
--endpoint $ENDPOINT
```

Überprüfen Sie Ihr Abonnement für AKS-Ereignisse mit `az eventgrid event-subscription list`:

```azurecli
az eventgrid event-subscription list --source-resource-id $SOURCE_RESOURCE_ID
```

Die folgende Beispielausgabe zeigt, dass Sie Ereignisse aus dem *MyAKS*-Cluster abonniert haben und diese Ereignisse an den Event Hub *MyEventGridHub* übermittelt werden:

```output
$ az eventgrid event-subscription list --source-resource-id $SOURCE_RESOURCE_ID
[
  {
    "deadLetterDestination": null,
    "deadLetterWithResourceIdentity": null,
    "deliveryWithResourceIdentity": null,
    "destination": {
      "deliveryAttributeMappings": null,
      "endpointType": "EventHub",
      "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNamespace/eventhubs/MyEventGridHub"
    },
    "eventDeliverySchema": "EventGridSchema",
    "expirationTimeUtc": null,
    "filter": {
      "advancedFilters": null,
      "enableAdvancedFilteringOnArrays": null,
      "includedEventTypes": [
        "Microsoft.ContainerService.NewKubernetesVersionAvailable"
      ],
      "isSubjectCaseSensitive": null,
      "subjectBeginsWith": "",
      "subjectEndsWith": ""
    },
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/MyAKS/providers/Microsoft.EventGrid/eventSubscriptions/MyEventGridSubscription",
    "labels": null,
    "name": "MyEventGridSubscription",
    "provisioningState": "Succeeded",
    "resourceGroup": "MyResourceGroup",
    "retryPolicy": {
      "eventTimeToLiveInMinutes": 1440,
      "maxDeliveryAttempts": 30
    },
    "systemData": null,
    "topic": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/MyResourceGroup/providers/microsoft.containerservice/managedclusters/MyAKS",
    "type": "Microsoft.EventGrid/eventSubscriptions"
  }
]
```

Wenn AKS-Ereignisse auftreten, werden diese Ereignisse in Ihrem Event Hub angezeigt. Wenn sich beispielsweise die Liste der verfügbaren Kubernetes-Versionen für Ihre Cluster ändert, wird ein `Microsoft.ContainerService.NewKubernetesVersionAvailable`-Ereignis angezeigt. Weitere Informationen zu den Ereignissen, die AKS ausgibt, finden Sie unter [Azure Kubernetes Service (AKS) als Event Grid-Quelle][aks-events].

## <a name="delete-the-cluster-and-subscriptions"></a>Löschen des Clusters und der Abonnements

Führen Sie den Befehl [az group delete][az-group-delete] aus, um die Ressourcengruppe, den AKS-Cluster, den Namespace, den Event Hub und alle dazugehörigen Ressourcen zu entfernen.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Wenn Sie den Cluster löschen, wird der vom AKS-Cluster verwendete Azure Active Directory-Dienstprinzipal nicht entfernt. Schritte zum Entfernen des Dienstprinzipals finden Sie unter den [Überlegungen zum AKS-Dienstprinzipal und dessen Löschung][sp-delete].
> 
> Wenn Sie eine verwaltete Identität verwendet haben, wird die Identität von der Plattform verwaltet und muss nicht entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Kubernetes-Cluster bereitgestellt und dann AKS-Ereignisse in Azure Event Hub abonniert.

Weitere Informationen zu Azure Container Service sowie ein vollständiges Beispiel vom Code bis zur Bereitstellung finden Sie im Kubernetes-Clustertutorial.

> [!div class="nextstepaction"]
> [AKS-Tutorial][aks-tutorial]

[aks-events]: ../event-grid/event-schema-aks.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-eventhubs-namespace-create]: /cli/azure/eventhubs/namespace?view=azure-cli-latest&preserve-view=true#az-eventhubs-namespace-create
[az-eventhubs-eventhub-create]: /cli/azure/eventhubs/eventhub?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-create
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription?view=azure-cli-latest&preserve-view=true#az-eventgrid-event-subscription-create
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-group-delete]: /cli/azure/group#az_group_delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
