---
title: Azure Event Grid auf Kubernetes – Webhook als Ereignishandler
description: Dieser Artikel beschreibt, wie Sie ein Event Grid-Thema auf einem Kubernetes-Cluster erstellen, das mit Azure Arc verbunden ist, und dann ein Abonnement für das Thema erstellen.
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 06/17/2021
ms.topic: quickstart
ms.openlocfilehash: aa4c83d5e702c8f39a88c285c716a84ba2716d45
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337564"
---
# <a name="route-cloud-events-to-webhooks-with-azure-event-grid-on-kubernetes"></a>Weiterleiten von Cloudereignissen an Webhooks mit Azure Event Grid auf Kubernetes
In diesem Schnellstart erstellen Sie ein Thema im Event Grid auf Kubernetes, ein Abonnement für das Thema und senden dann ein Beispielereignis an das Thema, um das Szenario zu testen. 

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]


## <a name="prerequisites"></a>Voraussetzungen

1. [Verbinden Sie Ihren Kubernetes-Cluster mit Azure Arc](../../azure-arc/kubernetes/quickstart-connect-cluster.md).
1. [Installieren der Azure Event-Grid-Erweiterung auf dem Kubernetes-Cluster](install-k8s-extension.md). Diese Erweiterung stellt Event Grid in einem Kubernetes-Cluster bereit. 


## <a name="create-a-custom-location"></a>Erstellen eines benutzerdefinierten Standorts
Als Azure-Standorterweiterung können Sie mit einem benutzerdefinierten Standort Ihren Kubernetes-Cluster mit Azure Arc-Unterstützung als Zielstandort für die Bereitstellung von Ressourcen wie Event Grid-Themen verwenden. Ein benutzerdefinierter Speicherort stellt einen Namespace im Cluster dar und ist die Stelle, an der Themen und Ereignisabonnements bereitgestellt werden. In diesem Abschnitt erstellen Sie einen benutzerdefinierten Standort. 

1. Deklarieren Sie die folgenden Variablen für die Namenswerte des Azure Arc-Clusters, der Ressourcengruppe und des benutzerdefinierten Standorts. Kopieren Sie diese Anweisungen in einen Editor, ersetzen Sie die Werte, kopieren Sie die Inhalte, und fügen Sie diese in das Bash-Fenster ein.  

    ```azurecli-interactive
    resourcegroupname="<AZURE RESOURCE GROUP NAME>"
    arcclustername="<AZURE ARC CLUSTER NAME>"
    customlocationname="<CUSTOM LOCATION NAME>"
    ```
1. Rufen Sie die Ressourcen-ID des mit Azure Arc verbundenen Clusters ab. Aktualisieren Sie die Werte für den Azure Arc-Clusternamen und Ressourcengruppenparameter, bevor Sie den Befehl ausführen. 

    ```azurecli-interactive
    hostresourceid=$(az connectedk8s show -n $arcclustername -g $resourcegroupname --query id -o tsv)    
    ```
1. Rufen Sie die Event Grid-Erweiterungsressourcen-ID ab. In diesem Schritt wird davon ausgegangen, dass Sie der Event Grid-Erweiterung den Namen **eventgrid-ext** zugewiesen haben. Aktualisieren Sie die Namen des Azure Arc-Clusters und der Ressourcengruppen, bevor Sie den Befehl ausführen. 

    ```azurecli-interactive
    clusterextensionid=$(az k8s-extension show --name eventgrid-ext --cluster-type connectedClusters -c $arcclustername -g $resourcegroupname  --query id -o tsv)    
    ```
1. Erstellen Sie einen benutzerdefinierten Standort mit den beiden oben genannten Werten. Aktualisieren Sie die Namen des benutzerdefinierten Standorts und der Ressourcengruppen, bevor Sie den Befehl ausführen. 

    ```azurecli-interactive
    az customlocation create -n $customlocationname -g $resourcegroupname --namespace arc --host-resource-id $hostresourceid --cluster-extension-ids $clusterextensionid    
    ```
1. Rufen Sie die Ressourcen-ID des benutzerdefinierten Standorts ab. Aktualisieren Sie den Namen des benutzerdefinierten Standorts, bevor Sie den Befehl ausführen. 

    ```azurecli-interactive
    customlocationid=$(az customlocation show -n $customlocationname -g $resourcegroupname --query id -o tsv)    
    ```

    Weitere Informationen zum Erstellen von benutzerdefinierten Standorten finden Sie unter [Erstellen und Verwalten von benutzerdefinierten Standorten in Kubernetes-Clustern mit Azure Arc-Unterstützung](../../azure-arc/kubernetes/custom-locations.md). 

## <a name="create-a-topic"></a>Erstellen eines Themas
In diesem Abschnitt erstellen Sie ein Thema am benutzerdefinierten Standort, den Sie im vorherigen Schritt erstellt haben. Aktualisieren Sie die Namen der Ressourcengruppen und Event Grid-Themen, bevor Sie den Befehl ausführen. Aktualisieren Sie den Standort, wenn Sie einen anderen Standort als „USA, Osten“ verwenden. 

1. Deklarieren Sie eine Variable für den Namen des Themas. 

    ```azurecli-interactive
    topicname="<TOPIC NAME>"
    ```
4. Führen Sie den folgenden Befehl aus, um das Thema zu erstellen. 

    ```azurecli-interactive
    az eventgrid topic create -g $resourcegroupname --name $topicname --kind azurearc --extended-location-name $customlocationid --extended-location-type customlocation --input-schema CloudEventSchemaV1_0 --location $region    
    ```

    Weitere Informationen zum CL-Befehl finden Sie unter [`az eventgrid topic create`](/cli/azure/eventgrid/topic#az_eventgrid_topic_create).

## <a name="create-a-message-endpoint"></a>Erstellen eines Nachrichtenendpunkts

Erstellen Sie zunächst einen Endpunkt für die Ereignisnachricht, bevor Sie ein Abonnement für das benutzerdefinierte Thema erstellen. Der Endpunkt führt in der Regel Aktionen auf der Grundlage der Ereignisdaten aus. Der Einfachheit halber stellen Sie in dieser Schnellstartanleitung eine [vorgefertigte Web-App](https://github.com/Azure-Samples/azure-event-grid-viewer) bereit, die die Ereignisnachrichten anzeigt. Die bereitgestellte Lösung umfasst einen App Service-Plan, eine App Service-Web-App und Quellcode von GitHub.

1. Wählen Sie auf der Artikelseite **Deploy to Azure** (In Azure bereitstellen) aus, um die Lösung für Ihr Abonnement bereitzustellen. Geben Sie im Azure-Portal Werte für die Parameter an.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to deploy to Azure."></a>
1. Die Bereitstellung kann einige Minuten dauern. Nach erfolgreichem Abschluss der Bereitstellung können Sie Ihre Web-App anzeigen und sich vergewissern, dass sie ausgeführt wird. Navigieren Sie hierzu in einem Webbrowser zu `https://<your-site-name>.azurewebsites.net`.

    Wenn die Bereitstellung fehlschlägt, überprüfen Sie die Fehlermeldung. Möglicherweise ist der Name der Website bereits vergeben. Stellen Sie die Vorlage noch mal bereit, und wählen Sie einen anderen Namen für die Site aus. 
1. Die Website wird angezeigt, aber es wurden noch keine Ereignisse bereitgestellt.

   ![Anzeigen der neuen Website](../media/custom-event-quickstart-portal/view-site.png)

## <a name="create-a-subscription"></a>Erstellen eines Abonnements
Abonnenten können sich für Ereignisse registrieren, die in einem Thema veröffentlicht werden. Zum Empfang von Ereignissen müssen Sie ein Event Grid-Abonnement für das gewünschte Thema erstellen. Ein Event-Abonnement definiert das Ziel, an das diese Ereignisse gesendet werden. Informationen zu allen unterstützten Zielen oder Handlern finden Sie unter [Ereignishandler](event-handlers.md).

Geben Sie zum Erstellen eines Ereignisabonnements mit einem Webhook-Ziel (HTTPS-Endpunkt) einen Namen für das Ereignisabonnement ein, aktualisieren Sie den Namen der Website, und führen Sie den folgenden Befehl aus.

```azurecli-interactive
topicid=$(az eventgrid topic show --name $topicname --resource-group $resourcegroupname --query id -o tsv)
az eventgrid event-subscription create --name <EVENT SUBSCRIPTION NAME> --source-resource-id $topicid --endpoint https://<SITE NAME>.azurewebsites.net/api/updates
```


Weitere Informationen zum CL-Befehl finden Sie unter [`az eventgrid event-subscription create`](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create).

## <a name="send-events-to-the-topic"></a>Ereignisse an das Thema senden
1. Führen Sie den folgenden Befehl aus, um den **Endpunkt** für das Thema abzurufen: Aktualisieren Sie nach dem Kopieren und Einfügen des Befehls den Namen des Themas (**topic name**) und den Namen der Ressourcengruppe (**resource group name**), bevor Sie den Befehl ausführen. Sie veröffentlichen Beispielereignisse in diesem Themenendpunkt. 

    ```azurecli
    az eventgrid topic show --name $topicname -g $resourcegroupname --query "endpoint" --output tsv
    ```
2. Führen Sie den folgenden Befehl aus, um den **Schlüssel** für das benutzerdefinierte Thema abzurufen: Aktualisieren Sie nach dem Kopieren und Einfügen des Befehls den Namen des Themas (**topic name**) und den Namen der Ressourcengruppe (**resource group name**), bevor Sie den Befehl ausführen. Das ist der Primärschlüssel des Themas. Um diesen Schlüssel aus dem Azure-Portal abzurufen, wechseln Sie zur Registerkarte **Zugriffsschlüssel** der Seite **Event Grid-Thema**. Um ein Ereignis in einem benutzerdefinierten Thema bereitstellen zu können, benötigen Sie den Zugriffsschlüssel. 

    ```azurecli
    az eventgrid topic key list --name $topicname -g $resourcegroupname --query "key1" --output tsv
    ```
1. Führen Sie den folgenden **Curl**-Befehl aus, um das Ereignis zu posten. Geben Sie die Endpunkt-URL und den Schlüssel aus Schritt 1 und 2 an, bevor Sie den Befehl ausführen. 

    ```bash
    curl  -k -X POST -H "Content-Type: application/cloudevents-batch+json" -H "aeg-sas-key: <KEY_FROM_STEP_2>" -g <ENDPOINT_URL_FROM_STEP_1> \
    -d  '[{ 
          "specversion": "1.0",
          "type" : "orderCreated",
          "source": "myCompanyName/us/webCommerceChannel/myOnlineCommerceSiteBrandName",
          "id" : "eventId-n",
          "time" : "2020-12-25T20:54:07+00:00",
          "subject" : "account/acct-123224/order/o-123456",
          "dataSchema" : "1.0",
          "data" : {
             "orderId" : "123",
             "orderType" : "PO",
             "reference" : "https://www.myCompanyName.com/orders/123"
          }
    }]'
    ```
    
    Wenn die Themenendpunkt-URL aus Schritt 1 eine private IP-Adresse ist, z. B. bei Verwendung des Diensttyps „ClusterIP“ für den Event Grid-Broker, können Sie **Curl** aus einem anderen Pod im Cluster ausführen, damit Zugriff auf diese IP-Adresse besteht. Sie können beispielsweise die folgenden Schritte ausführen:

    1. Erstellen Sie eine Manifestdatei mit der folgenden Konfiguration. Es kann ratsam sein, ``dnsPolicy`` gemäß Ihren Anforderungen anzupassen. Weitere Informationen finden Sie unter [DNS für Dienste und Pods](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/).
    
        ```yml
        apiVersion: v1
        kind: Pod
        metadata:
            name: test-pod2
        spec:
            containers:
              - name: nginx
                image: nginx
            hostNetwork: true
            dnsPolicy: ClusterFirstWithHostNet       
        ```
    1. Erstellen Sie den Pod.
        ```bash
            kubectl apply -f <name_of_your_yaml_manifest_file>
        ```
    1. Vergewissern Sie sich, dass der Pod ausgeführt wird.
        ```bash
            kubectl get pod test-pod
        ```
    1. Starten einer Shellsitzung über den Container
        ```bash
            kubectl exec --stdin --tty test-pod -- /bin/bash
        ```

    An diesem Punkt verfügen Sie über eine Shellsitzung aus einem ausgeführten Container im Cluster, über die Sie den **Curl**-Befehl ausführen können, der oben in einem der vorherigen Schritte beschrieben wurde.

    > [!NOTE]
    > Um zu erfahren, wie Sie Cloudereignisse mithilfe von Programmiersprachen senden können, sehen Sie sich die folgenden Beispiele an: 
    > - [C#](/samples/azure/azure-sdk-for-net/azure-event-grid-sdk-samples/).
    > - [Java](/samples/azure/azure-sdk-for-java/eventgrid-samples/)
    > - [JavaScript](/samples/azure/azure-sdk-for-js/eventgrid-javascript/) und [TypeScript](/samples/azure/azure-sdk-for-js/eventgrid-typescript/)
    > - [Python](/samples/azure/azure-sdk-for-python/eventgrid-samples/)

### <a name="verify-in-the-event-grid-viewer"></a>Überprüfen im Event Grid-Viewer
Sie haben das Ereignis ausgelöst, und Event Grid hat die Nachricht an den Endpunkt gesendet, den Sie beim Abonnieren konfiguriert haben. Zeigen Sie Ihre Web-App an, um das soeben gesendete Ereignis anzuzeigen.

:::image type="content" source="./media/create-topic-subscription/viewer-received-event.png" alt-text="Empfangenes Ereignis im Event Grid Viewer anzeigen":::

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln: 
- [Ereignishandler und -Ziele](event-handlers.md): stellt Informationen über alle Ereignishandler und Ziele bereit, die Event Grid auf Kubernetes unterstützt. 
- [Ereignisfilterung](filter-events.md): stellt Informationen bezüglich des Filterns von Ereignissen in Ereignisabonnements bereit. 
