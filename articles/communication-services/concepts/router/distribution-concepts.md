---
title: Auftragsverteilungskonzepte für Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Erfahren Sie mehr über die Verteilungskonzepte für Azure Communication Services Auftragsrouter.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: fdc64b5d789d017e6fcb8aab2cd48103235535ec
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319242"
---
# <a name="job-distribution-concepts"></a>Konzepte für die Auftragsverteilung

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Azure Communication Services Auftragsrouter verwendet einen flexiblen Verteilungsprozess, der die Verwendung einer Richtlinie und eines Auftragsangebotslebenszyklus zum Zuweisen von Workern umfasst. In diesem Artikel werden die verschiedenen Möglichkeiten zur Verteilung eines Auftrags, der Lebenszyklus des Auftragsangebots und die Auswirkungen dieses Prozesses auf Worker beschrieben.

## <a name="job-distribution-overview"></a>Übersicht über die Auftragsverteilung

Die Entscheidung, wie Aufträge an Worker verteilt werden sollen, ist ein wichtiges Feature des Auftragsrouters, und das SDK bietet ein ähnlich flexibles und erweiterbares Modell, mit dem Sie Ihre Umgebung anpassen können. Wie im Leitfaden zu [Klassifizierungskonzepten](classification-concepts.md) beschrieben, sucht der Auftragsrouter nach einem geeigneten Worker basierend auf den Merkmalen des Auftrags und der Verteilungsrichtlinie, nachdem ein Auftrag klassifiziert wurde. Wenn Worker ausgelastet sind, sucht der Auftragsrouter alternativ nach einem geeigneten Auftrag, wenn ein Worker verfügbar wird. Die Eignung von Workern wird anhand von drei Merkmalen bestimmt: [einem verfügbaren Kanal,](#channel-configurations)ihren [Fähigkeiten](#worker-abilities) und [den Status](#worker-status). Sobald ein geeigneter Worker gefunden wurde, wird eine Überprüfung durchgeführt, um sicherzustellen, dass er über einen offenen Kanal verfügt, dem der Auftrag zugewiesen werden kann.

Diese beiden Ansätze sind Schlüsselbegriffe dafür, wie der Auftragsrouter die Ermittlung von Aufträgen oder Workern initiiert.

### <a name="finding-workers-for-a-job"></a>Suchen von Workern für einen Auftrag

Nachdem ein Auftrag den [Klassifizierungsprozess](classification-concepts.md)abgeschlossen hat, wendet der Auftragsrouter die für die Warteschlange konfigurierte Verteilungsrichtlinie an, um mindestens einen Worker auszuwählen, der den Workerselektoren für den Auftrag entspricht, und generiert Angebote für diese Worker, die den Auftrag übernehmen sollen. 

### <a name="finding-a-job-for-a-worker"></a>Suchen eines Auftrags für einen Worker

Es gibt mehrere Szenarien, die den Auftragsrouter auslösen, um einen Auftrag für einen Worker zu finden:

- Wenn sich ein Worker beim Auftragsrouter registriert
- Wenn ein Auftrag geschlossen und der Kanal freigegeben wird
- Wenn ein Auftragsangebot abgelehnt oder widerrufen wird

Der Verteilungsprozess entspricht dem Suchen von Workern für einen Auftrag. Wenn ein Worker gefunden wird, wird ein [Angebot](#job-offer-overview) generiert.

## <a name="worker-overview"></a>Worker-Übersicht

Worker **registrieren sich** mithilfe des SDK beim Auftragsrouter und stellen die folgenden grundlegenden Informationen zur Verfügung:

- Worker-ID und -Name
- Warteschlangen-IDs
- Gesamtkapazität (Anzahl)
- Eine Liste der **Kanalkonfigurationen**
- Einen Satz von Bezeichnungen 

Der Auftragsrouter enthält immer einen Verweis auf einen registrierten Worker, auch wenn die Registrierung manuell oder automatisch **aufgehoben wird**.

### <a name="channel-configurations"></a>Kanalkonfigurationen

Jeder Auftrag erfordert eine Kanal-ID-Eigenschaft, die einen vorkonfigurierten Auftragsrouterkanal oder einen benutzerdefinierten Kanal darstellt. Eine Kanalkonfiguration besteht aus einer `channelId` Zeichenfolge und einer `capacityCostPerJob` Zahl. Zusammen stellen sie einen abstrakten Kommunikationsmodus und die Kosten dieses Modus dar. Beispielsweise können die meisten Personen nur auf einem Telefonanruf gleichzeitig sein, daher kann ein `Voice` Kanal hohe Kosten von `100` haben. Alternativ können bestimmte Workloads, z. B. Chat, eine höhere Parallelität aufweisen, was bedeutet, dass sie niedrigere Kosten haben. Sie können sich Kanalkonfigurationen als offene Slots vorstellen, in denen ein Auftrag zugewiesen oder angefügt werden kann. Dies wird im folgenden Beispiel veranschaulicht:

```csharp
await client.RegisterWorkerAsync(
    id: "EdmontonWorker",
    queueIds: new[] { "XBOX_Queue", "XBOX_Escalation_Queue" },
    totalCapacity: 100,
    labels: new LabelCollection
    {
        { "Location", "Edmonton" },
        { "XBOX_Hardware", 7 },
    },
    channelConfigurations: new List<ChannelConfiguration>
    {
        new (
            channelId: ManagedChannels.AcsVoiceChannel,
            capacityCostPerJob: 100
        ),
        new (
            channelId: ManagedChannels.AcsChatChannel,
            capacityCostPerJob: 33
        )
    }
);
```

Der obige Worker wird mit zwei Kanalkonfigurationen registriert, die jeweils eindeutige Kosten pro Kanal haben. Das Ergebnis ist, dass `EdmontonWorker` drei gleichzeitige `ManagedChannels.AcsChatChannel`-Aufträge oder einen `ManagedChannels.AcsVoiceChannel`-Auftrag verarbeiten kann.

Der Auftragsrouter enthält die folgenden vorkonfigurierten Kanal-IDs, die Sie verwenden können:

- ManagedChannels.AcsChatChannel
- ManagedChannels.AcsVoiceChannel
- ManagedChannels.AcsSMSChannel

Neue abstrakte Kanäle können wie folgt mit dem Job Router SDK erstellt werden:

```csharp
await client.SetChannelAsync(
    id: "MakePizza",
    name: "Make a pizza"
);

await client.SetChannelAsync(
    id: "MakeDonairs",
    name: "Make a donair"
);

await client.SetChannelAsync(
    id: "MakeBurgers",
    name: "Make a burger"
);
```

Sie können dann den Kanal verwenden, wenn Sie den Worker registrieren, um die Fähigkeit darzustellen, einen Auftrag zu übernehmen, der dieser Kanal-ID entspricht:

```csharp
await client.RegisterWorkerAsync(
    id: "PizzaCook",
    queueIds: new[] { "PizzaOrders", "DonairOrders", "BurgerOrders" },
    totalCapacity: 100,
    labels: new LabelCollection
    {
        { "Location", "New Jersey" },
        { "Language", "English" },
        { "PizzaMaker", 7 },
        { "DonairMaker", 10},
        { "BurgerMaker", 5}
    },
    channelConfigurations: new List<ChannelConfiguration>
    {
        new (
            channelId: MakePizza,
            capacityCostPerJob: 50
        ),
        new (
            channelId: MakeDonair,
            capacityCostPerJob: 33
        ),
        new (
            channelId: MakeBurger,
            capacityCostPerJob: 25        
        )
    }
);
```

Im obigen Beispiel werden drei abstrakte Kanäle mit jeweils eigenen Kosten pro Auftrag veranschaulicht. Daher sind die folgenden Auftragsparallelitätsbeispiele für den `PizzaCook` Worker möglich:

| MakePizza | MakeDonair | MakeBurger | Bewertung |
|--|--|--|--|
| 2         |            |            | 100   |
|           | 3          |            | 99    |
| 1         | 1          |            | 83    |
|           | 2          | 1          | 91    |
|           |            | 4          | 100   |
|           | 1          | 2          | 83    |

### <a name="worker-abilities"></a>Workerfähigkeiten

Abgesehen von den verfügbaren Kanälen, über die ein Worker verfügen kann, verwendet der Verteilungsprozess die Bezeichnungsauflistung des registrierten Workers, um seine Eignung für einen Auftrag zu bestimmen. Im obigen Pizzabäckerbeispiel verfügt der Worker über eine Bezeichnungsauflistung, die Folgendes umfasst:

```csharp
new LabelCollection
    {
        { "Location", "New Jersey" },
        { "Language", "English" },
        { "PizzaMaker", 7 },
        { "DonairMaker", 10},
        { "BurgerMaker", 5}
    }
```

Wenn ein Auftrag übermittelt wird, werden die **Workerselektoren** verwendet, um die Anforderungen für diese bestimmte Arbeitseinheit zu definieren. Wenn für einen Auftrag eine englischsprachige Person erforderlich ist, die gut in der Arbeit ist, lautet der SDK-Aufruf wie folgt:

```csharp
await client.CreateJobAsync(
    channelId: "MakeDonair",
    channelReference: "ReceiptNumber_555123",
    queueId: "DonairOrders",
    priority: 1,
    workerSelectors: new List<LabelSelector>
    {
        new (
            key: "DonairMaker",
            @operator: LabelOperator.GreaterThanEqual,
            value: 8),
        new (
            key: "English",
            @operator: LabelOperator.GreaterThan,
            value: 5)
    });
```

### <a name="worker-status"></a>Workerstatus

Da der Auftragsrouter abhängig von den Kanalkonfigurationen gleichzeitige Aufträge für einen Worker verarbeiten kann, wird das Konzept der Verfügbarkeit durch drei Zustände dargestellt:

**Aktiv:** Ein Worker ist beim Auftragsrouter registriert und bereit, einen Auftrag zu akzeptieren.

**Entleerung:** Ein Worker hat die Registrierung beim Auftragsrouter aufgehoben, ihm wird jedoch derzeit mindestens ein aktiver Auftrag zugewiesen.

**Inaktiv:** Ein Worker hat die Registrierung beim Auftragsrouter aufgehoben und verfügt nicht über aktive Aufträge.

## <a name="job-offer-overview"></a>Übersicht über Auftragsangebote

Wenn der Verteilungsprozess einen geeigneten Worker findet, der über einen geöffneten Kanal und den richtigen Status verfügt, wird ein Auftragsangebot generiert und ein Ereignis gesendet. Die Verteilungsrichtlinie enthält die folgenden konfigurierbaren Eigenschaften für das Angebot:

**OfferTTL:** Die Laufzeit für jedes generierte Angebot.

**Modus:** Die **Verteilungsmodi,** die sowohl die Eigenschaften `minConcurrentOffers` und `maxConcurrentOffers` enthalten.

> [!Important]
> Wenn ein Auftragsangebot für einen Worker generiert wird, wird eine der Kanalkonfigurationen verwendet, die mit der Kanal-ID des Auftrags übereinstimmen. Der Verbrauch dieses Kanals bedeutet, dass der Worker kein anderes Angebot erhält, es sei denn, auf dem Worker ist zusätzliche Kapazität für diesen Kanal verfügbar. Wenn der Worker das Angebot ablehnt oder das Angebot abläuft, wird der Kanal freigegeben.

### <a name="job-offer-lifecycle"></a>Lebenszyklus des Auftragsangebots

Die folgenden Lebenszyklusereignisse für Auftragsangebote können über Ihr Event Grid-Abonnement beobachtet werden:

- RouterWorkerOfferIssued
- RouterWorkerOfferAccepted
- RouterWorkerOfferDeclined
- RouterWorkerOfferExpired
- RouterWorkerOfferRevoked

> [!NOTE]
> Ein Angebot kann von einem Worker mit dem SDK akzeptiert oder abgelehnt werden, während alle anderen Ereignisse intern generiert werden.

## <a name="distribution-modes"></a>Verteilungsmodi

Der Auftragsrouter umfasst die folgenden Verteilungsmodi:

**LongestIdleMode:** Generiert ein Angebot für den längsten Worker im Leerlauf in einer Warteschlange.

**RoundRobinMode:** Wählen Sie bei einer Sammlung von Workern den nächsten Worker nach dem letzten Worker aus, der nach ID sortiert ausgewählt wurde.

**BestWorkerMode:** Verwenden Sie [ruleEngine](router-rule-concepts.md) des Auftragsrouters, um einen Worker basierend auf seinen Bezeichnungen auszuwählen.

## <a name="distribution-summary"></a>Verteilungszusammenfassung

Abhängig von mehreren Faktoren wie dem Status eines Workers, der Kanalkonfiguration/-kapazität, dem Modus der Verteilungsrichtlinie und der Angebotsparallelität kann sich die Art und Weise beeinflussen, wie Auftragsangebote generiert werden. Es wird empfohlen, mit einer einfachen Implementierung zu beginnen und die Komplexität gemäß Ihren Anforderungen zu erhöhen.
