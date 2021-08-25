---
title: Azure Event Grid auf Kubernetes - Überblick
description: Dieser Artikel vermittelt einen Überblick über Event Grid auf Kubernetes mit Azure Arc.
author: jfggdl
ms.author: jafernan
ms.subservice: kubernetes
ms.date: 05/25/2021
ms.topic: overview
ms.openlocfilehash: 807d337988993c8be0fee76b9c12e1fc6dc75c7d
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770566"
---
# <a name="event-grid-on-kubernetes-with-azure-arc-preview---overview"></a>Event Grid auf Kubernetes mit Azure Arc (Vorschau) - Übersicht
Dieser Artikel vermittelt einen Überblick über Event Grid auf Kubernetes, Anwendungsfälle für dessen Einsatz, verfügbare Funktionen und wie es sich von Azure Event Grid unterscheidet.

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]

## <a name="what-is-event-grid"></a>Was ist Event Grid?
Event Grid ist ein Event-Broker, der zur Integration von Arbeitslasten verwendet wird, die Event-gesteuerte Architekturen nutzen. Eine Event-gesteuerte Architektur verwendet Events, um das Auftreten von Systemzustandsänderungen zu übermitteln, und ist ein gängiger Integrationsansatz in entkoppelten Architekturen, wie z. B. solche, die Microservices verwenden. Event Grid bietet ein Pub-Sub-, das auch als Push-Push-Kommunikationsmodell beschrieben wird, bei dem Abonnenten Events gesendet (gepusht) werden und diese Abonnenten nicht unbedingt wissen, welcher Publisher die Events sendet. Dieses Modell steht im Gegensatz zu klassischen Push-Pull-Modellen, wie sie z. B. von Azure Service Bus oder Azure Event Hubs verwendet werden, bei denen Clients Nachrichten von Message Brokern beziehen und infolgedessen eine stärkere Kopplung zwischen Message-Brokern und konsumierenden Clients besteht.

Event Grid wird in zwei Versionen angeboten: **Azure Event Grid**, ein vollständig verwalteter PaaS-Dienst auf Azure, und Event Grid auf Kubernetes mit Azure Arc, mit dem Sie Event Grid auf Ihrem Kubernetes-Cluster verwenden können, egal wo dieser eingesetzt wird, lokal oder in der Cloud. 

Aus Gründen der Übersichtlichkeit verwenden wir in diesem Artikel den Begriff **Event Grid**, wenn wir uns unabhängig von der verwendeten Edition auf die allgemeinen Dienstfunktionen beziehen. Wir beziehen uns auf **Azure Event Grid**, um den auf Azure gehosteten und verwalteten Dienst zu bezeichnen. Aus Gründen der Prägnanz beziehen wir uns auch auf **Event Grid in Kubernetes mit Azure Arc** als **Event Grid in Kubernetes**.

Unabhängig davon, welche Version des Event Grid Sie verwenden, ist ein **Ereignisherausgeber** vorhanden, der Ereignisse an Event Grid sendet, und einen oder mehrere **Ereignisabonnenten**, die Endpunkte bereitstellen, an denen sie die vom Event Grid gelieferten Ereignisse empfangen. Nicht alle im Event Grid veröffentlichten Ereignisse müssen an alle Ereignisabonnenten geliefert werden. Mit Event Grid können Sie die Ereignisse auswählen, die über eine Reihe von Konfigurationseinstellungen, die in einem **Ereignisabonnement** definiert sind, an bestimmte Ziele weitergeleitet werden sollen. Sie können Filter in Ereignisabonnements verwenden, um bestimmte Ereignisse an einen Endpunkt oder per Multicast an mehrere Endpunkte weiterzuleiten. Event Grid bietet einen zuverlässigen Liefer-Mechanismus mit Wiederholungslogik. Event Grid basiert auch auf offenen Standards und unterstützt die [Cloud Events 1.0-Schemaspezifikation](https://github.com/cloudevents/spec/blob/master/spec.md).


## <a name="event-grid-on-kubernetes-with-azure-arc"></a>Event Grid in Kubernetes mit Azure Arc
Bei Event Grid in Kubernetes mit Azure Arc handelt es sich um ein Angebot, mit dem Sie Event Grid in Ihrem eigenen Kubernetes-Cluster ausführen können. Diese Funktion wird durch die Verwendung von [Azure Arc-fähigen Kubernetes](../../azure-arc/kubernetes/overview.md) aktiviert. Ein [unterstützter Kubernetes-Cluster](install-k8s-extension.md#supported-kubernetes-distributions) wird über Azure Arc-fähige Kubernetes mit Azure verbunden. Sobald die Verbindung besteht, können Sie [Event Grid installieren](install-k8s-extension.md). 

### <a name="use-case"></a>Anwendungsfall
Event Grid in Kubernetes unterstützt verschiedene Ereignis-gesteuerte Integrationsszenarien. Das wichtigste übergreifende Szenario, das unterstützt und als User Story ausgedrückt wird, ist jedoch:

„Als Eigentümer eines Systems, das in einem Kubernetes-Cluster bereitgestellt wird, möchte ich die Zustandsänderungen meines Systems mitteilen, indem ich Ereignisse veröffentliche und das Routing dieser Ereignisse so konfiguriere, dass Ereignishandler, ob unter meiner Kontrolle oder anderweitig, die Ereignisse meines Systems so verarbeiten können, wie sie es für richtig halten.“

Eine **Funktion**, die Sie dabei unterstützt, die obengenannten Anforderungen zu erfüllen: [Event Grid-Themen](/rest/api/eventgrid/version2021-06-01-preview/topics).

### <a name="event-grid-on-kubernetes-at-a-glance"></a>Event Grid in Kubernetes auf einen Blick
Aus Benutzersicht besteht Event Grid in Kubernetes aus den folgenden in blau angezeigten Ressourcen:

:::image type="content" source="./media/overview/event-grid-topics.png" alt-text="Ressourcen" lightbox="./media/overview/event-grid-topics.png":::

* Ein **Thema** ist eine Art Eingabekanal, der einen Endpunkt verfügbar macht, an den Herausgeber Events an Event Grid senden.
* Beim **Ereignisabonnementen** handelt es sich um eine Ressource, die Konfigurationseinstellungen zum Filtern und Weiterleiten von Ereignissen an ein Ziel enthält, an das die Ereignisse geliefert werden.
* Ein **Ereignis** ist die Ankündigung einer Zustandsänderung.
* Beim **Ereignishandler** handelt es sich um eine Anwendung oder einen Dienst, der Ereignisse empfängt und auf die Ereignisse in irgendeiner Weise reagiert oder sie verarbeitet. Manchmal bezeichnen wir Ereignishandler auch als **Ereignisabonnenten**. Im obigen Diagramm sind die Event-Handler die in einem Kubernetes-Cluster (K8s) bereitgestellte API und der Azure Service Bus-Dienst.

Weitere Informationen zu diesen Konzepten finden Sie unter [Concepts in Azure Event Grid](concepts.md) (Konzepte in Azure Event Grid).

### <a name="sample-workload-integration-scenarios-and-destinations"></a>Beispielszenarien und Ziele der Workload-Integration

Sie können Workloads integrieren, die in Ihrem Cluster ausgeführt werden. Ihr Publisher kann ein beliebiger Dienst sein, der auf Ihrem Kubernetes-Cluster läuft, oder eine beliebige Workload, die Zugriff auf den Topic-Endpunkt hat (der vom Event Grid-Broker gehostet wird), an den Ihr Publisher Events sendet.

:::image type="content" source="./media/overview/event-grid-intra-cluster-integration.png" alt-text="Cluster-interne Integration" lightbox="./media/overview/event-grid-intra-cluster-integration.png":::


Sie können auch einen Publisher an einer anderen Stelle in Ihrem Netzwerk bereitgestellt haben, der Events an Event Grid in einem Ihrer Kubernetes-Cluster sendet:

:::image type="content" source="./media/overview/event-grid-in-network-integration.png" alt-text="Netzwerkintegration" lightbox="./media/overview/event-grid-in-network-integration.png":::

Mit Event Grid in Kubernetes können Sie Ereignisse zur weiteren Verarbeitung, Speicherung oder Visualisierung an Azure weiterleiten:

:::image type="content" source="./media/overview/event-grid-forward-events.png" alt-text="Weiterleiten von Ereignissen an Azure":::

#### <a name="destinations"></a>Destinations
Ereignishandler-Ziele können beliebige HTTPS- oder HTTP-Endpunkte sein, die Event Grid über das Netzwerk erreichen kann, öffentlich oder privat, und auf die es Zugriff hat (nicht durch einen Authentifizierungsmechanismus geschützt). Sie definieren Ereignis-Lieferziele, wenn Sie ein Event-Abonnement erstellen. Weitere Informationen finden Sie unter [Ereignishandler](event-handlers.md). 

## <a name="features"></a>Funktionen
Event Grid in Kubernetes unterstützt [Event Grid Themen.](/rest/api/eventgrid/version2021-06-01-preview/topics)Es handelt sich hierbei um eine Funktion, die auch von [Azure Event Grid](../custom-topics.md)angeboten wird. Event Grid-Themen unterstützt Sie dabei, den [primären Integrationsanwendungsfall](#use-case) zu verwirklichen, bei dem Ihre Anforderungen die Integration Ihres Systems mit einem anderen Workload erfordern, den Sie besitzen oder der auf andere Weise für Ihr System zugänglich gemacht wird.

Einige der Funktionen, die Sie mit Azure Event Grid in Kubernetes erhalten, sind wie folgt:

* **[Ereignisfilterung:](filter-events.md)** Filtern Sie nach Ereignistyp oder Ereignisthema, der Ereignisdaten, um sicherzustellen, dass Ereignishandler nur relevante Ereignisse empfangen.
* **Auffächern**: Abonnieren Sie mehrere Endpunkte für das gleiche Ereignis, um Kopien des Ereignisses an mehrere Stellen zu senden.
* **Basierend auf offenen Standards**: Definieren Sie Ihre Ereignisse anhand der [Cloud Events 1.0-Schemaspezifikation](https://github.com/cloudevents/spec/blob/master/spec.md).
* **Zuverlässigkeit**: Event Grid verfügt über eine Logik zur erneuten Zustellung von Ereignissen, die sicherstellt, dass die Ereignisse ihr Ziel erreichen.

Weitere Informationen finden Sie unter [Funktionen, die von Event Grid unter Kubernetes unterstützt werden.](features.md)

## <a name="pricing"></a>Preise 
Event Grid in Kubernetes mit Azure Arc wird während der Vorschauversion kostenlos angeboten.

## <a name="next-steps"></a>Nächste Schritte
Befolgen Sie diese Schritte in der Reihenfolge, in der Sie das Routing von Ereignissen mit Event Grid auf Kubernetes starten.

1. [Verbinden Sie Ihren Cluster mit Azure Arc](../../azure-arc/kubernetes/quickstart-connect-cluster.md).
1. [Installieren Sie eine Event Grid-Erweiterung](install-k8s-extension.md), welche die eigentliche Ressource ist, die Event Grid einem Kubernetes-Cluster bereitstellt. Weitere Informationen zur Erweiterung finden Sie im Abschnitt [Event Grid-Erweiterung](install-k8s-extension.md#event-grid-extension). 
1. [Erstellen eines benutzerdefinierten Speicherorts](../../azure-arc/kubernetes/custom-locations.md). Ein benutzerdefinierter Speicherort stellt einen Namespace im Cluster dar und ist die Stelle, an der Themen und Ereignisabonnements bereitgestellt werden.
1. [Erstellen eines Themas und einer oder mehrerer Ereignisabonnements](create-topic-subscription.md).
1. [Veröffentlichen von Ereignissen](create-topic-subscription.md).

Hier sind weitere Ressourcen, die Sie verwenden können:

* [SDKs für Datenebenen](../sdk-overview.md#data-plane-sdks).
* [Beispiele für das Veröffentlichen von Ereignissen anhand der SDKs für die Datenebene](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/).
* [Event Grid-CLI](/cli/azure/eventgrid).
* [Verwaltungs-SDKs](../sdk-overview.md#management-sdks).
