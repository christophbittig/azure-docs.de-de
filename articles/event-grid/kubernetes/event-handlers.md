---
title: 'Azure Event Grid in Kubernetes: Ereignishandler und Ziele'
description: In diesem Artikel werden verschiedene Typen von Ereignishandlern und Zielen beschrieben, die von Event Grid in Kubernetes unterstützt werden.
author: jfggdl
ms.author: jafernan
ms.subservice: kubernetes
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: b1052b996fd9da8452d0f23d60fc7ea53676f713
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769822"
---
# <a name="event-handlers-destinations-in-event-grid-on-kubernetes"></a>Ereignishandler und Ziele in Event Grid in Kubernetes
Ein Ereignishandler ist ein beliebiges System, das einen Endpunkt verfügbar macht und das Ziel für Ereignisse ist, die von Event Grid gesendet werden. Ein Ereignishandler, der ein Ereignis empfängt, reagiert darauf und verwendet die Ereignisnutzdaten, um Logik auszuführen, was zum Auftreten neuer Ereignisse führen kann.

Die Methode zum Konfigurieren Event Grid zum Senden von Ereignissen an ein Ziel besteht in der Erstellung eines Ereignisabonnements. Dies kann über die [Azure CLI](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create), das [Verwaltungs-SDK](../sdk-overview.md#management-sdks) oder direkte HTTP(s)-Aufrufe mithilfe der API-Version [2020-10-15-preview](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update) erfolgen.

Im Allgemeinen kann Event Grid in Kubernetes Ereignisse über **Webhooks** an beliebige Ziele senden. Webhooks sind HTTP(s)-Endpunkte, die von einem Dienst oder einer Workload verfügbar gemacht werden, auf den bzw. die Event Grid Zugriff besitzt. Der Webhook kann eine Workload sein, die im gleichen Cluster, im gleichen Netzwerkbereich, in der Cloud, lokal oder an einem beliebigen Ort gehostet wird, den Event Grid erreichen kann. 

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]

Über Webhooks unterstützt Event Grid die folgenden Ziele, die **auf einem Kubernetes-Cluster gehostet werden**:

* Azure App Service in Kubernetes mit Azure Arc. 
* Azure Functions in Kubernetes mit Azure Arc. 
* Azure Logic Apps in Kubernetes mit Azure Arc.

Zusätzlich zu Webhooks kann Event Grid in Kubernetes Ereignisse an die folgenden Ziele senden, die **in Azure gehostet werden**:

- Azure Event Grid **mit Webhooks**
- Azure Functions **nur mit Webhooks**
- Azure Event Hubs mit der zugehörigen Azure Resource Manager-Ressourcen-ID
- Azure Service Bus-Themen oder -Warteschlangen mit der zugehörigen Azure Resource Manager-Ressourcen-ID
- Azure Storage-Warteschlange mit der zugehörigen Azure Resource Manager-Ressourcen-ID



## <a name="feature-parity"></a>Featureparität
Event Grid in Kubernetes bietet ein gutes Maß an Featureparität durch Azure Event Grid-Unterstützung für Ereignisabonnements. In der folgenden Liste werden die wichtigsten Unterschiede bei der Ereignisabonnementfunktionalität aufgeführt. Abgesehen von diesen Unterschieden können Sie die [REST-API-Version 2020-10-15-preview](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions) von Azure Event Grid als Referenz für die Verwaltung von Ereignisabonnements für Event Grid in Kubernetes verwenden.

1. Verwenden Sie die [REST-API-Version 2020-10-15-Preview](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions).
2. Der [Azure Event Grid-Trigger für Azure Functions](../../azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp%2Cconsole) wird nicht unterstützt. Sie können einen WebHook-Zieltyp verwenden, um Ereignisse an Azure Functions zu übermitteln.
3. Es gibt keine Unterstützung für den [Speicherort für unzustellbare Nachrichten](../manage-event-delivery.md#set-dead-letter-location). Dies bedeutet, dass Sie ``properties.deadLetterDestination`` nicht in Ihren Ereignisabonnementnutzdaten verwenden können.
4. Hybridverbindungen von Azure Relay als Ziel werden noch nicht unterstützt.
5. Es wird nur das CloudEvents-Schema unterstützt. Der unterstützte Schemawert ist „[CloudEventSchemaV1_0](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update#eventdeliveryschema)“. Das CloudEvents-Schema ist erweiterbar und basiert auf offenen Standards.  
6. Bezeichnungen ([properties.labels](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update#request-body)) gelten nicht für Event Grid in Kubernetes. Daher sind sie nicht verfügbar.
7. [Übermittlung mit Ressourcenidentität](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update#deliverywithresourceidentity) wird nicht unterstützt. Daher werden alle Eigenschaften für [Ereignisabonnementidentität](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update#eventsubscriptionidentity) nicht unterstützt.
8. Die [Überprüfung des Zielendpunkts](../webhook-event-delivery.md#endpoint-validation-with-event-grid-events) wird noch nicht unterstützt.

## <a name="event-filtering-in-event-subscriptions"></a>Ereignisfilterung in Ereignisabonnements
Der andere wichtige Aspekt beim Konfigurieren eines Ereignisabonnements ist die Auswahl der Ereignisse, die an ein Ziel übermittelt werden sollen. Weitere Informationen dazu finden Sie unter [Ereignisfilterung](filter-events.md).

## <a name="sample-destination-configurations"></a>Zielbeispielkonfigurationen

Im Folgenden finden Sie einige grundlegende Beispielkonfigurationen, die vom vorgesehenen Ziel abhängen.

## <a name="webhook"></a>WebHook
Legen Sie zum Veröffentlichen eines Webhook-Endpunkts `endpointType` auf `WebHook` fest, und geben Sie Folgendes an:

* **endpointUrl**: Die URL des Webhook-Endpunkts.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure Event Grid

Legen Sie zum Veröffentlichen auf einem Azure Event Grid-Cloudendpunkt `endpointType` auf `WebHook` fest, und geben Sie Folgendes an:

* **endpointUrl:** Azure Event Grid-Themen-URL in der Cloud, bei der der API-Versionsparameter auf **2018-01-01** und `aeg-sas-key` auf den URL-codierten SAS-Schlüssel festgelegt ist. 

   ```json
    {
        "properties": {
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01&aeg-sas-key=urlencoded(sas-key-value)"
                }
            }
        }
    }
   ```

## <a name="event-hubs"></a>Event Hubs

Legen Sie zum Veröffentlichen in einem Event Hub `endpointType` auf `eventHub` fest, und geben Sie Folgendes an:

* **resourceId**:Ressourcen-ID für den spezifischen Event Hub.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "resourceId": "<Azure Resource ID of your event hub>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Service Bus-Warteschlangen

Legen Sie zum Veröffentlichen in einer Service Bus-Warteschlange `endpointType` auf `serviceBusQueue` fest, und geben Sie Folgendes an:

* **resourceId**: Ressourcen-ID für die spezifische Service Bus-Warteschlange.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "resourceId": "<Azure Resource ID of your Service Bus queue>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Service Bus-Themen

Legen Sie zum Veröffentlichen in einem Service Bus-Thema `endpointType` auf `serviceBusTopic` fest, und geben Sie Folgendes an:

* **resourceId**: Ressourcen-ID für das spezifische Service Bus-Thema.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "resourceId": "<Azure Resource ID of your Service Bus topic>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>Storage-Warteschlangen

Legen Sie zum Veröffentlichen in einer Storage-Warteschlange `endpointType` auf `storageQueue` fest, und geben Sie Folgendes an:

* **queueName**: Der Name der Storage-Warteschlange, in der Sie veröffentlichen.
* **resourceID**: Azure-Ressourcen-ID des Speicherkontos, das die Warteschlange enthält.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "resourceId": "<Azure Resource ID of your Storage account>"
              }
            }
          }
        }
    ```

## <a name="next-steps"></a>Nächste Schritte
* Fügen Sie Ihrem [Ereignisabonnement](filter-events.md) eine Filterkonfiguration hinzu, um die zu übermittelnden Ereignisse auszuwählen. 
* Informationen zu Schemas, die von Event Grid in Azure Arc für Kubernetes unterstützt werden, finden Sie unter [Event Grid in Kubernetes – Ereignisschemas](event-schemas.md).
