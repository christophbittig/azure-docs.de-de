---
title: Erfassen von Daten aus dem Event Hub im Azure Synapse Data Explorer (Vorschau) mithilfe der 1-Klick-Erfassung
description: In diesem Artikel erfahren Sie, wie Sie Daten aus dem Event Hub in Azure Synapse Data Explorer mit nur einem Klick erfassen (laden).
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: dbdf77da2434c71de29f45885d3e08ad304e54ea
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478914"
---
# <a name="use-one-click-ingestion-to-create-an-event-hub-data-connection-for-azure-synapse-data-explorer-preview"></a>Erstellen einer Event Hub-Datenverbindung für Azure Synapse Data Explorer mithilfe der 1-Klick-Erfassung (Vorschau)

> [!div class="op_single_selector"]
> * [Portal](data-explorer-ingest-event-hub-portal.md)
> * [1-Klick](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Azure Resource Manager-Vorlage](data-explorer-ingest-event-hub-resource-manager.md)

Azure Synapse Data Explorer ermöglicht die Datenerfassung (das Laden von Daten) aus Event Hubs. Dabei handelt es sich um eine Big Data-Streamingplattform und einen Ereigniserfassungsdienst. [Event Hubs](/azure/event-hubs/event-hubs-about) kann Millionen von Ereignissen pro Sekunde nahezu in Echtzeit verarbeiten. In diesem Artikel verbinden Sie mithilfe der [1-Klick-Erfassung](data-explorer-ingest-data-one-click.md) einen Event Hub mit einer Tabelle in Azure Synapse Data Explorer.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- [Event Hub mit Daten für die Erfassung](data-explorer-ingest-event-hub-portal.md#create-an-event-hub)

> [!NOTE]
> Das Erfassen von Daten aus einem Event Hub in Data Explorer-Pools funktioniert nicht, wenn Ihr Synapse-Arbeitsbereich ein verwaltetes virtuelles Netzwerk mit aktiviertem Datenexfiltrationsschutz verwendet.

## <a name="ingest-new-data"></a>Erfassen neuer Daten

1. Wählen Sie in Synapse Studio im linken Bereich **Daten** aus.

1. Klicken Sie unter **Azure Data Explorer-Datenbanken** mit der rechten Maustaste auf die relevante Datenbank, und wählen Sie dann **In Azure Data Explorer öffnen** aus.

    :::image type="content" source="../media/ingest-data-one-click/open-azure-data-explorer-synapse.png" alt-text="Screenshot von Azure Synapse Studio: Öffnen von Azure Data Explorer im Kontext eines spezifischen Pools":::

1. Wählen Sie im linken Menü auf der Webbenutzeroberfläche die Registerkarte **Daten** aus. 

    :::image type="content" source="../media/ingest-data-event-hub/one-click-ingestion-event-hub.png" alt-text="Wählen Sie die 1-Klick-Erfassung von Daten aus Event Hub auf der Webbenutzeroberfläche aus.":::

1. Wählen Sie auf der Karte **Ingest data from Event Hub** (Daten aus Event Hub erfassen) die Option **Erfassen** aus. 

Das Fenster **Neue Daten erfassen** wird mit der Registerkarte **Ziel** geöffnet.

### <a name="destination-tab"></a>Registerkarte „Ziel“

:::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-destination-table.png" alt-text="Screenshot: Registerkarte „Ziel“. Die Felder für Cluster, Datenbank und Tabelle müssen ausgefüllt werden, bevor mit „Weiter: Quelle“ fortgefahren wird.":::

1. Die Felder **Cluster** und **Datenbank** werden automatisch aufgefüllt. Sie können über die Dropdownmenüs einen anderen Cluster oder eine andere Datenbank auswählen.

1. Wählen Sie unter **Tabelle** die Option **Neue Tabelle erstellen** aus, und geben Sie einen Namen für die neue Tabelle ein. Alternativ können Sie auch eine vorhandene Tabelle verwenden. 

    > [!NOTE]
    > Tabellennamen müssen zwischen einem und 1024 Zeichen lang sein. Sie können alphanumerische Zeichen, Bindestriche und Unterstriche verwenden. Sonderzeichen werden nicht unterstützt.

1. Wählen Sie die Option **Weiter: Quelle** aus.

### <a name="source-tab"></a>Registerkarte „Quelle“

1. Wählen Sie unter **Quelltyp** die Option **Event Hub** aus. 

1. Füllen Sie unter **Datenverbindung** die folgenden Felder aus:

    :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-event-hub-details.png" alt-text="Screenshot: Registerkarte „Quelle“ mit auszufüllenden Feldern für Projektdetails. Erfassen neuer Daten in Azure Synapse Data Explorer mit Event Hub mit nur einem Klick":::

    |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Name der Datenverbindung | *ContosoDataConnection*  | Der Name, der Ihre Datenverbindung bezeichnet
    | Subscription |      | Die Abonnement-ID, unter der sich die Event Hub-Ressource befindet  |
    | Event Hub-Namespace |  | Der Name, der Ihren Namespace bezeichnet |
    | Event Hub |  | Der zu verwendende Event Hub |
    | Consumergruppe |  | Die in Ihrem Event Hub definierte Consumergruppe |
    | Ereignissystemeigenschaften | Auswählen relevanter Eigenschaften | Die [Event Hub-Systemeigenschaften](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations). Wenn pro Ereignisnachricht mehrere Datensätze vorhanden sind, werden die Systemeigenschaften dem ersten Datensatz hinzugefügt. Beim Hinzufügen von Systemeigenschaften [erstellen](/azure/data-explorer/kusto/management/create-table-command?context=/azure/synapse-analytics/context/context) oder [aktualisieren](/azure/data-explorer/kusto/management/alter-table-command?context=/azure/synapse-analytics/context/context) Sie das Tabellenschema und die [Zuordnung](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context), um die ausgewählten Eigenschaften einzubeziehen. |

1. Wählen Sie die Option **Weiter: Schema** aus.

## <a name="schema-tab"></a>Registerkarte „Schema“

Daten werden in Form von [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata)-Objekten aus dem Event Hub gelesen. Die folgenden Formate werden unterstützt: CSV, JSON, PSV, SCsv, SOHsv TSV, TXT und TSVE.

<!-- For information on schema mapping with JSON-formatted data, see [Edit the schema](one-click-ingestion-existing-table.md#edit-the-schema).
For information on schema mapping with CSV-formatted data, see [Edit the schema](one-click-ingestion-new-table.md#edit-the-schema). -->

:::image type="content" source="../media/ingest-data-event-hub/schema-tab.png" alt-text="Screenshot: Registerkarte „Schema“ beim Erfassen neuer Daten in Azure Synapse Data Explorer mit Event Hub mit nur einem Klick":::

1. Sind die im Vorschaufenster angezeigten Daten nicht vollständig, benötigen Sie möglicherweise mehr Daten, um eine Tabelle mit allen erforderlichen Datenfeldern zu erstellen. Verwenden Sie die folgenden Befehle, um neue Daten aus Ihrem Event Hub abzurufen:
    * **Verwerfen und neue Daten abrufen**: Die dargestellten Daten werden verworfen, und es wird nach neuen Ereignissen gesucht.
    * **Fetch more data** (Weitere Daten abrufen): Zusätzlich zu den bereits gefundenen Ereignissen wird nach weiteren Ereignissen gesucht. 
    
    > [!NOTE]
    > Damit Sie eine Vorschau Ihrer Daten anzeigen können, muss Ihr Event Hub Ereignisse senden.
        
1. Wählen Sie **Weiter: Zusammenfassung** aus.

## <a name="continuous-ingestion-from-event-hub"></a>Kontinuierliche Erfassung aus Event Hub

Im Fenster **Continuous ingestion from Event Hub established** (Kontinuierliche Erfassung aus Event Hub eingerichtet) werden alle Schritte mit grünen Häkchen markiert, wenn die Einrichtung erfolgreich abgeschlossen wurde. Die Karten unter diesen Schritten enthalten Optionen zum Untersuchen Ihrer Daten mit **Schnellabfragen**, zum Rückgängigmachen von Änderungen, die mithilfe von **Tools** vorgenommen wurden, oder zum **Überwachen** der Event Hub-Verbindungen und -Daten.

:::image type="content" source="../media/ingest-data-event-hub/data-ingestion-completed.png" alt-text="Screenshot: Letzter Bildschirm bei der 1-Klick-Erfassung in Azure Synapse Data Explorer aus Event Hub":::

## <a name="next-steps"></a>Nächste Schritte

- [Analyse mit Data Explorer](../../get-started-analyze-data-explorer.md)
- [Überwachen von Data Explorer-Pools](../data-explorer-monitor-pools.md)
