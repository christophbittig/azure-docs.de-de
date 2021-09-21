---
title: Ereignisrouten
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie Ereignisse innerhalb von Azure Digital Twins und an andere Azure-Dienste weiterleiten.
author: baanders
ms.author: baanders
ms.date: 6/1/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 2718dd070cc12cb58f8033d5a2757d8aedf05e05
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771202"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Weiterleiten von Ereignissen innerhalb und außerhalb von Azure Digital Twins

Azure Digital Twins verwendet **Ereignisrouten** zum Senden von Daten an Consumer außerhalb des Diensts. 

Es gibt zwei Hauptfälle für das Senden von Azure Digital Twins-Daten:
* Der erste Fall ist das Senden von Daten von einem Zwilling im Azure Digital Twins-Graph an einen anderen. Wenn sich beispielsweise eine Eigenschaft in einem digitalen Zwilling ändert, sollten Sie einen anderen digitalen Zwilling im Hinblick auf die aktualisierten Daten benachrichtigen und aktualisieren.
* Der zweite Fall ist das Senden von Daten an Downstreamdatendienste zur weiteren Speicherung oder Verarbeitung (auch als *ausgehende Daten* bezeichnet). Beispiel:
  - Ein Krankenhaus möchte möglicherweise Azure Digital Twins-Ereignisdaten an [Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md) senden, um Zeitreihendaten von Ereignissen im Zusammenhang mit Händewaschen für eine Massenanalyse aufzuzeichnen.
  - Ein Unternehmen, das bereits [Azure Maps](../azure-maps/about-azure-maps.md) verwendet, möchte möglicherweise Azure Digital Twins verwenden, um seine Lösung zu verbessern. Nach dem Einrichten von Azure Digital Twins kann es schnell eine Azure Maps-Instanz aktivieren, Azure Maps-Entitäten in Azure Digital Twins als [digitale Zwillinge](concepts-twins-graph.md) in den Zwillingsgraphen aufnehmen oder leistungsstarke Abfragen ausführen, die sowohl die Daten aus Azure Maps als auch aus Azure Digital Twins nutzen.

Ereignisrouten werden in beiden Szenarios verwendet.

## <a name="about-event-routes"></a>Informationen zu Ereignisrouten

Ereignisrouten ermöglichen Ihnen das Senden von Ereignisdaten von digitalen Zwillingen in Azure Digital Twins an benutzerdefinierte Endpunkte in Ihren Abonnements. Für Endpunkte werden derzeit drei Azure-Dienste unterstützt: [Event Hub](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md) und [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Jeder dieser Azure-Dienste kann mit anderen Diensten verbunden werden und fungiert als Mittelsmann, der Daten für die von Ihnen benötigte Verarbeitung an endgültige Ziele wie TSI oder Azure Maps sendet.

Das folgende Diagramm veranschaulicht den Fluss von Ereignisdaten durch eine größere IoT-Lösung mit einem Azure Digital Twins-Aspekt:

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Diagramm der Weiterleitung von Daten aus Azure Digital Twins über Endpunkte an mehrere Downstreamdienste." border="false":::

Typische Downstreamziele für Ereignisrouten sind Ressourcen wie TSI und Azure Maps sowie Speicher- und Analyselösungen.

### <a name="event-routes-for-internal-digital-twin-events"></a>Ereignisrouten für interne Ereignisse in Bezug auf digitale Zwillinge

Ereignisrouten werden auch verwendet, um Ereignisse innerhalb des Zwillingsgraphen zu verarbeiten und Daten von einem digitalen Zwilling an einen anderen zu senden. Diese Art der Ereignisverarbeitung erfolgt durch Herstellen einer Verbindung zwischen Ereignisrouten und Computeressourcen wie [Azure Functions](../azure-functions/functions-overview.md) über Event Grid. Diese Funktionen definieren dann, wie Zwillinge Ereignisse empfangen und auf diese reagieren sollen. 

Wenn eine Computeressource den Zwillingsgraphen auf Grundlage eines über die Ereignisroute empfangenen Ereignisses ändern möchte, ist es hilfreich, wenn sie im Voraus weiß, welcher Zwilling geändert werden soll. 

Die Ereignisnachricht enthält auch die ID des Quellzwillings, der sie gesendet hat, sodass die Computeressource Abfragen verwenden oder Beziehungen durchlaufen kann, um einen Zielzwilling für den gewünschten Vorgang zu finden. 

Darüber hinaus muss die Computeressource Sicherheits -und Zugriffsberechtigungen unabhängig einrichten.

Eine Anleitung für das Einrichten einer Azure-Funktion zum Verarbeiten von Ereignissen für digitale Zwillinge finden Sie unter [Einrichten der Verarbeitung von Ereignissen zwischen Zwillingen](how-to-send-twin-to-twin-events.md).

## <a name="create-an-endpoint"></a>Erstellen eines Endpunkts

Entwickler müssen erst Endpunkte definieren, um eine Ereignisroute definieren zu können. Ein **Endpunkt** ist ein Ziel außerhalb von Azure Digital Twins, das eine Routenverbindung unterstützt. Unterstützte Ziele sind beispielsweise:
* Benutzerdefinierte Event Grid-Themen
* Event Hub
* Service Bus

Sie können die REST-APIs von Azure Digital Twins, CLI-Befehle oder das Azure-Portal verwenden, um einen [Endpunkt zu erstellen](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins).

Beim Definieren eines Endpunkts müssen Sie Folgendes angeben:
* Name des Endpunkts
* Endpunkttyp (Event Grid, Event Hub oder Service Bus)
* Primäre und sekundäre Verbindungszeichenfolge für die Authentifizierung 
* Themenpfad des Endpunkts, z. B. *Ihr-Thema.westus2.eventgrid.azure.net*

In der Steuerungsebene sind Endpunkt-APIs für Folgendes verfügbar:
* Endpunkt erstellen
* Abrufen einer Liste mit Endpunkten
* Abrufen von Endpunkten nach Name
* Löschen von Endpunkten nach Name

## <a name="create-an-event-route"></a>Erstellen einer Ereignisroute
 
Sie können zum [Erstellen einer Ereignisroute](how-to-manage-routes.md#create-an-event-route) die REST-APIs von Azure Digital Twins, CLI-Befehle oder das Azure-Portal verwenden.

Im Folgenden finden Sie ein Beispiel für das Erstellen einer Ereignisroute in einer Clientanwendung mithilfe des [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)-Aufrufs `CreateOrReplaceEventRouteAsync`: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::

1. Zunächst wird ein `DigitalTwinsEventRoute`-Objekt erstellt, und der-Konstruktor nimmt den Namen eines Endpunkts an. Dieses Feld `endpointName` identifiziert einen Endpunkt, z. B. einen Event Hub, ein Event Grid oder einen Service Bus. Diese Endpunkte müssen vor diesem Registrierungsaufruf in Ihrem Abonnement erstellt und mithilfe von Steuerungsebenen-APIs an Azure Digital Twins angehängt werden.

2. Das Ereignisroutenobjekt verwendet auch ein Feld [Filter](how-to-manage-routes.md#filter-events), das zum Einschränken der Arten von Ereignissen für diese Route verwendet werden kann. Ein Filter `true` aktiviert die Route ohne zusätzliche Filterung (ein Filter mit dem Wert `false` deaktiviert die Route). 

3. Dieses Ereignisroutenobjekt wird dann zusammen mit einem Namen für die Route an `CreateOrReplaceEventRouteAsync` übergeben.

> [!TIP]
> Alle SDK-Funktionen sind in synchronen und asynchronen Versionen enthalten.

## <a name="dead-letter-events"></a>„Unzustellbare Nachrichten“-Ereignisse

Wenn ein Endpunkt innerhalb eines bestimmten Zeitraums oder nach einer bestimmten Anzahl von Übermittlungsversuchen nicht übermittelt werden kann, kann Event Grid das nicht übermittelte Ereignis an ein Speicherkonto senden. Dieser Prozess wird als Speicherung **unzustellbarer Nachrichten** bezeichnet. Von Azure Digital Twins werden unzustellbare Nachrichten gespeichert, wenn **eine der folgenden** Bedingungen erfüllt ist. 

* Das Ereignis wird nicht innerhalb der Gültigkeitsdauer übermittelt.
* Die Anzahl der Übermittlungsversuche hat den Grenzwert überschritten.

Wenn eine der Bedingungen erfüllt ist, wird das Ereignis gelöscht oder als unzustellbare Nachricht gespeichert. Endpunkte aktivieren unzustellbare Nachrichten standardmäßig **nicht**. Wenn Sie das Feature aktivieren möchten, müssen Sie bei der Erstellung des Endpunkts ein Speicherkonto zum Speichern nicht übermittelter Ereignisse angeben. Ereignisse werden dann aus diesem Speicherkonto gepullt, um Übermittlungsprobleme zu beheben.

Wenn Sie den Speicherort für unzustellbare Nachrichten festlegen möchten, benötigen Sie ein Speicherkonto mit einem Container. Sie geben die URL für diesen Container an, wenn Sie den Endpunkt erstellen. Die unzustellbaren Nachrichten werden als Container-URL mit einem SAS-Token bereitgestellt. Dieses Token benötigt nur die `write`-Berechtigung für den Zielcontainer innerhalb des Speicherkontos. Die vollständig formatierte URL weist das folgende Format auf: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>`

Weitere Informationen zu SAS-Token finden Sie hier: [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](../storage/common/storage-sas-overview.md)

Informationen zum Einrichten eines Endpunkts mit unzustellbaren Nachrichten finden Sie unter [Verwalten von Endpunkten und Routen in Azure Digital Twins](how-to-manage-routes.md#create-an-endpoint-with-dead-lettering).

### <a name="types-of-event-messages"></a>Arten von Ereignisnachrichten

Verschiedene Arten von Ereignissen in IoT Hub und Azure Digital Twins führen zu verschiedenen Arten von Benachrichtigungsmeldungen, wie unten beschrieben.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Einrichten und Verwalten einer Ereignisroute finden Sie unter:
* [Verwalten von Endpunkten und Routen](how-to-manage-routes.md)

Weitere Informationen zur Verwendung von Azure Functions zum Weiterleiten von Ereignissen innerhalb von Azure Digital Twins finden Sie unter:
* [Einrichten der Verarbeitung von Ereignissen zwischen Zwillingen](how-to-send-twin-to-twin-events.md)