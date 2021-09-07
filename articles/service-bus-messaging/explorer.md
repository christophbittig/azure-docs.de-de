---
title: Verwenden des Azure Service Bus-Explorers zum Ausführen von Datenvorgängen für Service Bus (Vorschauversion)
description: In diesem Artikel erhalten Sie Informationen, wie Sie den Azure Service Bus-Explorer verwenden können, um auf Azure Service Bus-Daten zuzugreifen.
ms.topic: conceptual
ms.date: 06/16/2021
ms.openlocfilehash: 3b561586e77940eda5647aa0ab333ab88937463f
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2021
ms.locfileid: "112294353"
---
# <a name="use-service-bus-explorer-to-perform-data-operations-on-service-bus-preview"></a>Verwenden des Service Bus-Explorers zum Ausführen von Datenvorgängen für Service Bus (Vorschauversion)

## <a name="overview"></a>Übersicht

Mithilfe von Azure Service Bus können sendende und empfangende Clientanwendungen ihre Geschäftslogik entkoppeln. Dazu wird die vertraute Point-to-Point-Semantik (Warteschlange) und Veröffentlichen/Abonnieren-Semantik (Thema/Abonnement) verwendet.

Es gibt zwei Arten von Vorgängen, die für Azure Service Bus-Namespaces ausgeführt werden können: 
   * Verwaltungsvorgänge: Erstellen, Aktualisieren und Löschen von Namespaces, Warteschlangen, Themen und Abonnements in Service Bus
   * Datenvorgänge: Senden an und Empfangen von Nachrichten aus Warteschlangen, Themen und Abonnements

Der Azure Service Bus-Explorer erweitert die Funktionen des Portals über die Verwaltungsvorgänge hinaus, um Datenvorgänge (Senden, Empfangen, Einsehen) für Warteschlangen, Themen und Abonnements und die jeweils dazugehörigen unzustellbaren untergeordneten Entitäten direkt im Azure-Portal selbst zu unterstützen.

> [!NOTE]
> In diesem Artikel werden die Funktionen des Azure Service Bus-Explorers erläutert, die über das Azure-Portal zur Verfügung stehen.
>
> Das Explorertool in Azure Service Bus ist ***nicht*** identisch mit dem OSS-Tool [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer), das sich im Besitz der Community befindet.
>

## <a name="prerequisites"></a>Voraussetzungen

Um das Service Bus Explorer-Tool zu verwenden, müssen Sie die folgenden Aufgaben ausführen: 

- Bereitstellen eines Azure Service Bus-Namespace.
- Erstellen einer Warteschlange, an die und von der Nachrichten gesendet und empfangen werden können oder Erstellen eines Themas mit einem Abonnement, um die Funktionen zu testen. Informationen zum Erstellen von Warteschlangen, Themen und Abonnements finden Sie in den folgenden Artikeln: 
    - [Schnellstart: Erstellen einer Service Bus-Warteschlange mithilfe des Azure-Portals](service-bus-quickstart-portal.md)
    - [Schnellstart: Erstellen eines Service Bus-Themas und eines oder mehrerer Abonnements dieses Themas im Azure-Portal](service-bus-quickstart-topics-subscriptions-portal.md)
- Stellen Sie sicher, dass Sie Mitglied einer dieser Rollen für den Namespace sind: 
    - [Service Bus-Datenbesitzer](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) 
    - [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor) 
    - [Besitzer](../role-based-access-control/built-in-roles.md#owner)


## <a name="using-the-service-bus-explorer"></a>Verwenden des Service Bus-Explorers

Zur Verwendung des Azure Service Bus-Explorers müssen Sie zum Service Bus-Namespace navigieren, in dem Sie Sende-, Einsicht- und Empfangsvorgänge durchführen möchten.

Wenn Sie Vorgänge für eine Warteschlange ausführen möchten, klicken Sie im Navigationsmenü auf die Option **Warteschlangen**. Wenn Sie Vorgänge für ein Thema und die dazugehörigen Abonnements ausführen möchten, klicken Sie auf die Option **Themen**. 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png" alt-text="Auswahl der Entität":::

Nachdem Sie die Option **Warteschlangen** oder **Themen** ausgewählt haben, können Sie eine bestimmte Warteschlange oder ein bestimmtes Thema auswählen.

Klicken Sie im linken Navigationsmenü auf **Service Bus-Explorer (Vorschau)** .

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="Linkes Navigationsmenü des Service Bus-Explorers":::

> [!NOTE]
> Für Service Bus Explorer werden Nachrichten mit einer Größe von bis zu 1 MB unterstützt. 

### <a name="sending-a-message-to-a-queue-or-topic"></a>Senden einer Nachricht an eine Warteschlange oder ein Thema

Zum Senden einer Nachricht an eine **Warteschlange** oder ein **Thema** klicken Sie in Service Bus Explorer auf die Registerkarte **_Senden_**.

Führen Sie die folgenden Schritte aus, um dort eine Nachricht zu erstellen: 

1. Wählen Sie den **Inhaltstyp** aus: entweder „Text/unformatiert“, „application/xml“ oder „application/json“.
2. Fügen Sie den **Nachrichteninhalt** hinzu. Achten Sie darauf, dass dieser dem zuvor festgelegten **Inhaltstyp** entspricht.
3. Legen Sie optional **Erweiterte Eigenschaften** fest: Korrelations-ID, Nachrichten-ID, ReplyTo, Gültigkeitsdauer (TTL) und geplante Zeit in Warteschlange (für geplante Nachrichten).
4. Legen Sie die **benutzerdefinierten Eigenschaften** fest: Das können alle Benutzereigenschaften sein, die für einen Wörterbuchschlüssel festgelegt wurden.

Sobald Sie das Erstellen der Nachricht abgeschlossen haben, können Sie auf „Senden“ klicken.

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="Erstellen einer Nachricht":::

Wenn der Sendevorgang erfolgreich ausgeführt wurde, geschieht Folgendes: 

* Wenn der Sendevorgang an eine Warteschlange gerichtet war, wird der Metrikzähler **Aktive Nachrichten** um eins inkrementiert.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="Warteschlangenmetrik nach dem Sendevorgang":::

* Wenn der Sendevorgang an ein Thema gerichtet war, wird der Metrikzähler **Aktive Nachrichten** für das Abonnement, an das die Nachricht weitergeleitet wurde, um eins inkrementiert.

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="Themenmetrik nach dem Sendevorgang":::

### <a name="receiving-a-message-from-a-queue"></a>Empfangen einer Nachricht von einer Warteschlange

Die Funktion zum Empfangen von Nachrichten im Service Bus-Explorer ermöglicht nur das Empfangen jeweils einer Nachricht. Der Empfangsvorgang wird mithilfe des Modus **ReceiveAndDelete** ausgeführt.

> [!IMPORTANT]
> Beachten Sie, dass es sich beim vom Service Bus-Explorer ausgeführten Empfangsvorgang um einen ***destruktiven Empfang*** handelt, d. h. die Nachricht wird aus der Warteschlange entfernt, sobald sie im Service Bus-Explorertool angezeigt wird.
>
> Wenn Sie sich Nachrichten ansehen möchten, ohne dass sie aus der Warteschlange entfernt werden, können Sie die Funktion zum ***Einsehen*** verwenden.
>

Führen Sie die folgenden Schritte aus, um eine Nachricht von einer Warteschlange (oder einer untergeordneten Warteschlange mit unzustellbaren Nachrichten) zu empfangen: 

1. Klicken Sie auf die Registerkarte ***Empfangen*** im Service Bus-Explorer.
2. Überprüfen Sie die Metriken, um zu sehen, ob es **aktive Nachrichten** oder **unzustellbaren Nachrichten** gibt, die empfangen werden können.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="Warteschlangenmetrik nach dem Sendevorgang":::

3. Wählen Sie entweder die ***Warteschlange** _ oder die untergeordnete Warteschlange für _ *_unzustellbare Nachrichten_** aus.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadLetter":::

4. Klicken Sie auf die Schaltfläche ***Empfangen** _ und anschließend auf _ *_Ja_**, um den Empfangs- und Löschvorgang zu bestätigen.


Sobald der Empfangsvorgang erfolgreich war, werden die Nachrichtendetails wie unten dargestellt auf dem Raster angezeigt. Sie können eine Nachricht im Raster auswählen, damit Details dazu angezeigt werden.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="Screenshot: Fenster „Warteschlangen“ im Azure Service Bus Explorer mit Nachrichtendetails, die für eine in der Warteschlange ausgewählte aktive Nachricht angezeigt werden":::


### <a name="peeking-a-message-from-a-queue"></a>Einsehen einer Nachricht aus einer Warteschlange

Mit der Funktion zum Einsehen können Sie den Service Bus Explorer verwenden, um die obersten 32 Nachrichten in einer Warteschlange oder einer Warteschlange mit unzustellbaren Nachrichten anzuzeigen.

1. Wenn Sie eine Nachricht in der Warteschlange einsehen möchten, klicken Sie auf die Registerkarte ***Einsehen*** im Service Bus-Explorer.

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="Registerkarte „Einsehen“":::

2. Überprüfen Sie die Metriken, um zu sehen, ob es **aktive Nachrichten** oder **unzustellbare Nachrichten** gibt, die Sie einsehen können.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="Warteschlangenmetrik nach dem Sendevorgang":::

3. Wählen Sie entweder die ***Warteschlange** _ oder die untergeordnete Warteschlange mit _ *_unzustellbaren Nachrichten_** aus.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadLetter":::

4. Klicken Sie auf die Schaltfläche ***Einsehen***. 

Sobald der Einsehvorgang abgeschlossen ist, werden auf dem Raster wie unten dargestellt bis zu 32 Nachrichten angezeigt. Wenn Sie sich die Details einer bestimmten Nachricht ansehen möchten, wählen Sie sie im Raster aus. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="Einsehen einer Nachricht in der Warteschlange":::

> [!NOTE]
>
> Da es sich beim Einsehen um keinen destruktiven Vorgang handelt, wird die Nachricht **nicht** aus der Warteschlange entfernt.
>

### <a name="receiving-a-message-from-a-subscription"></a>Empfangen einer Nachricht aus einem Abonnement

Wie bei einer Warteschlange kann der Vorgang ***Empfangen*** auch für ein Abonnement oder eine Entität mit unzustellbaren Nachrichten ausgeführt werden. Da ein Abonnement jedoch im Kontext eines Themas existiert, wird der Empfangsvorgang durchgeführt, indem für ein bestimmtes Thema zum Service Bus-Explorer navigiert wird.

> [!IMPORTANT]
> Beachten Sie, dass es sich beim vom Service Bus-Explorer ausgeführten Empfangsvorgang um einen ***destruktiven Empfang*** handelt, d. h. die Nachricht wird aus der Warteschlange entfernt, sobald sie im Service Bus-Explorertool angezeigt wird.
>
> Wenn Sie sich Nachrichten ansehen möchten, ohne dass sie aus der Warteschlange entfernt werden, können Sie die Funktion zum ***Einsehen*** verwenden.
>

1. Klicken Sie auf die Registerkarte ***Empfangen** _, und wählen Sie beim Dropdownselektor ein gewünschtes _ *_Abonnement_** aus.

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="Ausgewählte Registerkarte „Empfangen“":::

2. Wählen Sie entweder das ***Abonnement** _ oder die untergeordnete Entität _ *_Unzustellbar_** aus.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadLetter":::

3. Klicken Sie auf die Schaltfläche ***Empfangen** _ und anschließend auf _ *_Ja_**, um den Empfangs- und Löschvorgang zu bestätigen.

Sobald der Empfangsvorgang erfolgreich war, wird die empfangene Nachricht wie unten dargestellt auf dem Raster angezeigt. Wenn Sie sich die Nachrichtendetails ansehen möchten, klicken Sie auf die Nachricht.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="Screenshot: Registerkarte „Empfangen“ im Azure Service Bus Explorer mit Nachrichtendetails, die für eine empfangene aktive Nachricht angezeigt werden":::

### <a name="peeking-a-message-from-a-subscription"></a>Einsehen einer Nachricht aus einem Abonnement

Wenn Sie sich die Nachrichten in einem Abonnement oder einer untergeordneten Entität mit unzustellbaren Nachrichten einfach nur ansehen möchten, können Sie auch die Funktion ***Einsehen*** für ein Abonnement verwenden.

1. Klicken Sie auf die Registerkarte ***Einsehen** _, und wählen Sie beim Dropdownselektor ein gewünschtes _ *_Abonnement_** aus.

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="Ausgewählte Registerkarte „Einsehen“":::

2. Wählen Sie entweder das ***Abonnement** _ oder die untergeordnete Entität _ *_Unzustellbar_** aus.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadLetter":::

3. Klicken Sie auf die Schaltfläche ***Einsehen***.

Sobald der Einsehvorgang abgeschlossen ist, werden auf dem Raster wie unten dargestellt bis zu 32 Nachrichten angezeigt. Wenn Sie sich die Details einer bestimmten Nachricht ansehen möchten, wählen Sie sie im Raster aus. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="Einsehen der Nachricht im Abonnement":::

> [!NOTE]
>
> - Da es sich beim Einsehen um keinen destruktiven Vorgang handelt, wird die Nachricht **nicht** aus der Warteschlange entfernt.


## <a name="next-steps"></a>Nächste Schritte

   * Weitere Informationen zu Service Bus-[Warteschlangen](service-bus-queues-topics-subscriptions.md#queues) und -[Themen](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)
   * Weitere Informationen zum [Erstellen einer Service Bus-Warteschlange mithilfe des Azure-Portals](service-bus-quickstart-portal.md)
   * Weitere Informationen zum [Erstellen eines Service Bus-Themas und eines oder mehrerer Abonnements dieses Themas im Azure-Portal](service-bus-quickstart-topics-subscriptions-portal.md)
