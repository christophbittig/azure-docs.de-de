---
title: Leistungsleitfaden für den Azure Web PubSub-Dienst
description: Dies ist eine Übersicht über die Leistung und Benchmark des Azure Web PubSub-Diensts. Schlüsselmetriken, die bei der Planung der Kapazität zu beachten sind
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: biqian
author: bjqian
ms.openlocfilehash: bfdba230408a25d7e463cf7c70ab84d0d5f8cf0f
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997550"
---
# <a name="performance-guide-for-azure-web-pubsub-service"></a>Leistungsleitfaden für den Azure Web PubSub-Dienst

Einer der wichtigsten Vorteile der Verwendung des Azure Web PubSub-Diensts ist die einfache Skalierbarkeit von Azure Web PubSub-Upstreamanwendungen. In einem Szenario mit großem Umfang ist die Leistung ein wichtiger Faktor. 

In diesem Leitfaden werden die Faktoren vorgestellt, die sich auf die Leistung der Web PubSub-Upstreamanwendung auswirken. Es werden typische Leistungen in verschiedenen Anwendungsszenarien beschrieben. 

## <a name="term-definitions"></a>Begriffsdefinitionen

*Eingehend:* Die eingehende Nachricht an den Azure Web PubSub-Dienst.

*Ausgehend:* Die ausgehende Nachricht vom Azure Web PubSub-Dienst.

*Bandbreite*: Die Gesamtgröße aller Nachrichten in einer Sekunde.

## <a name="overview"></a>Übersicht

Der Azure Web PubSub-Dienst definiert sieben Standard-Tarife für unterschiedliche Leistungskapazitäten. Dieser Leitfaden beantwortet die folgenden Fragen:

- Was ist die typische Leistung des Azure Web PubSub-Diensts für die einzelnen Tarife?

- Erfüllt der Azure Web PubSub-Dienst meine Anforderungen an den Nachrichtendurchsatz (z. B. Senden von 100.000 Nachrichten pro Sekunde)?

- Welcher Tarif ist für mein bestimmtes Szenario geeignet? Oder wie kann ich den richtigen Tarif auswählen?

In diesem Leitfaden werden zunächst die Faktoren, die sich auf die Leistung auswirken, auf hoher Ebene erläutert. Anschließend wird die maximale Anzahl von eingehenden und ausgehenden Nachrichten für jeden Tarif für typische Anwendungsfälle veranschaulicht: **Senden an Gruppen über das Web PubSub-Unterprotokoll**, **Upstream** und **REST-API**.

Dieser Leitfaden kann nicht alle Szenarien abdecken (und verschiedene Anwendungsfälle, Nachrichtengrößen, Nachrichtensendemuster usw.). Er enthält jedoch einige grundlegende Informationen, um die Leistungseinschränkung zu verstehen.

## <a name="performance-insight"></a>Leistungsübersicht

Dieser Abschnitt beschreibt die Methoden zur Leistungsauswertung und listet anschließend alle Faktoren auf, die sich auf die Leistung auswirken. Am Ende werden Methoden bereitgestellt, die Ihnen helfen, die Leistungsanforderungen auszuwerten.

### <a name="methodology"></a>Methodik

*Durchsatz* und *Latenz* sind zwei typische Aspekte der Leistungsüberprüfung. Für den Azure Web PubSub-Dienst verfügt jeder SKU-Tarif über eine eigene Richtlinie zur Durchsatzdrosselung. Die Richtlinie definiert *den maximal zulässigen Durchsatz (ein- und ausgehende Bandbreite)* als den maximal erreichten Durchsatz, wenn 99 Prozent der Nachrichten eine Latenzzeit von weniger als 1 Sekunde aufweisen.

### <a name="performance-factors"></a>Leistungsfaktoren

Theoretisch ist die Kapazität des Azure Web PubSub-Diensts durch Berechnungsressourcen begrenzt: CPU, Arbeitsspeicher und Netzwerk. Eine größere Anzahl von Verbindungen mit dem Azure Web PubSub-Dienst führt beispielsweise dazu, dass der Dienst mehr Arbeitsspeicher benötigt. Für umfangreicheren Nachrichtenverkehr (z. B. wenn jede Nachricht größer als 2.048 Byte ist) muss der Azure Web PubSub-Dienst mehr CPU-Zyklen für die Verarbeitung des Datenverkehrs aufwenden.

Die Kosten für das Nachrichtenrouting begrenzen auch die Leistung. Der Azure Web PubSub-Dienst dient als Nachrichtenbroker, der die Nachricht zwischen mehreren Clients weiterleitet. Ein anderes Szenario oder eine andere API erfordert eine andere Routingrichtlinie. 

Bei **echo** sendet der Client eine Nachricht an den Upstream, der die Nachricht an den Client zurückgibt. Dieses Muster weist die niedrigsten Routingkosten auf. Aber für **Broadcast**, **An Gruppe senden** und **An Verbindung senden** muss der Azure Web PubSub-Dienst die Zielverbindungen über die intern verteilte Datenstruktur suchen. Diese zusätzliche Verarbeitung beansprucht mehr CPU, Arbeitsspeicher und Netzwerkbandbreite. Infolgedessen ist die Leistung geringer.

Zusammenfassend lässt sich sagen, dass sich die folgenden Faktoren auf die ein- und ausgehenden Kapazitäten auswirken:

-   SKU-Tarif (CPU/Arbeitsspeicher)

-   Anzahl der Verbindungen

-   Nachrichtengröße

-   Nachrichtensendungsrate

-   Anwendungsfallszenario (Routingkosten)


### <a name="finding-a-proper-sku"></a>Suchen einer geeigneten SKU

Wie können Sie die ein- und ausgehende Kapazität bewerten oder herausfinden, welcher Tarif für einen bestimmten Anwendungsfall geeignet ist?

Jeder Tarif hat seine eigene maximale ein- und ausgehende Bandbreite. Nachdem der eingehende oder ausgehende Datenverkehr den Grenzwert überschritten hat, ist keine einwandfreie Nutzung gewährleistet. 

```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: Die Anzahl der Verbindungen, die die Nachricht senden.
- *outboundConnections*: Die Anzahl der Verbindungen, die die Nachricht empfangen.
- *messageSize*: Die Größe einer einzelnen Nachricht (Durchschnittswert). Eine kleine Nachricht mit weniger als 1.024 Bytes hat eine Leistungsauswirkung, die einer 1.024-Byte-Nachricht ähnlich ist.
- *sendInterval*: Dies ist das Intervall zum Senden von Nachrichten. Wenn beispielsweise eine Sekunde festgelegt ist, wird jede Sekunde eine Nachricht gesendet. Ein kürzeres Intervall bedeutet, dass mehr Nachrichten in einem Zeitraum gesendet werden. So bedeutet beispielsweise ein Wert von 0,5 Sekunden, dass pro Sekunde zwei Nachrichten gesendet werden.
- *Connections*: Dies ist der committete maximale Schwellenwert für den Azure Web PubSub-Dienst für die einzelnen Tarife. Verbindungen, die den Schwellenwert überschreiten, werden gedrosselt.

Angenommen, der Upstream ist leistungsstark genug und nicht der Leistungsengpass. Überprüfen Sie dann die maximale ein- und ausgehende Bandbreite für die einzelnen Tarife.

## <a name="case-study"></a>Fallstudie

In den folgenden Abschnitten werden drei typische Anwendungsfälle beschrieben: **Senden an Gruppen über das Web PubSub-Unterprotokoll**, **Auslösen von CloudEvent** und **Abrufen der REST-API**. Für jedes Szenario werden im Abschnitt die aktuelle ein- und ausgehende Kapazität für den Azure Web PubSub-Dienst aufgelistet. Darüber hinaus werden die wichtigsten Faktoren erläutert, die sich auf die Leistung auswirken.

In allen Anwendungsfällen beträgt die standardmäßige Nachrichtengröße 2.048 Byte und das Sendeintervall eine Sekunde.

### <a name="send-to-groups-through-web-pubsub-subprotocol"></a>Senden an Gruppen über das Web PubSub-Unterprotokoll
Der Dienst unterstützt das Unterprotokoll namens `json.webpubsub.azure.v1`, das es den Clients ermöglicht, Nachrichten direkt zu veröffentlichen bzw. zu abonnieren, ohne einen Roundtrip zum Upstreamserver durchführen zu müssen. Dieses Szenario ist effizient, da kein Server beteiligt ist und der gesamte Datenverkehr die WebSocket-Clientdienstverbindung durchläuft.

![Abbildung des Workflows „An Gruppe senden“](./media/concept-performance/group.png)

Anzahl der Gruppenmitglieder und Gruppen sind zwei Faktoren, die sich auf die Leistung auswirken. Es werden zwei Arten von Gruppen definiert, um die Analyse zu vereinfachen:

- **Große Gruppe**: Die Gruppenanzahl ist immer 10. Die Anzahl der Gruppenmitglieder entspricht: (maximale Verbindungszahl) / 10. Wenn es z. B. für Unit1 1.000 Verbindungen gibt, dann besteht jede Gruppe aus 1000 / 10 = 100 Mitgliedern.
- **Kleine Gruppe**: Jede Gruppe verfügt über 10 Verbindungen. Die Gruppenanzahl entspricht: (maximale Verbindungsanzahl) / 10. Wenn es z. B. für Unit1 1.000 Verbindungen gibt, dann sind das 1000 / 10 = 100 Gruppen.

**An Gruppe senden** verursacht Routingkosten für den Azure Web PubSub-Dienst, da die Zielverbindungen über eine verteilte Datenstruktur gefunden werden müssen. Mit zunehmender Anzahl der sendenden Verbindungen steigen die Kosten.

##### <a name="big-group"></a>Große Gruppe

Bei **An große Gruppe senden** wird die ausgehende Bandbreite zum Engpass, bevor die Routingkostengrenze erreicht wird. In der folgenden Tabelle ist die maximale Bandbreite für ausgehenden Datenverkehr aufgeführt.

|   An große Gruppe senden       | Unit1 | Unit2 | Unit5  | Unit10 | Unit20  | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|------- |--------|---------|
| Verbindungen               | 1\.000 | 2\.000 | 5\.000  | 10.000 | 20.000 | 50.000  | 100.000|
| Anzahl der Gruppenmitglieder        | 100   | 200   | 500    | 1\.000  | 2\.000  | 5\.000   | 10.000 |
| Gruppenanzahl               | 10    | 10    | 10     | 10     | 10     | 10      | 10|
| Eingehende Nachrichten pro Sekunde  | 30      | 30      | 30      | 30    | 30    | 30   | 30     |
| Eingehende Bandbreite  | 60 KBit/s | 60 KBit/s  | 60 KBit/s   | 60 KBit/s  | 60 KBit/s   | 60 KBit/s    | 60 KBit/s    |
| Ausgehende Nachrichten pro Sekunde | 3,000 | 6.000 | 15.000 | 30.000 | 60.000 | 150.000 | 300.000 |
| Ausgehende Bandbreite | **6 MBit/s** | **12 MBit/s** | **30 MBit/s** | **60 MBit/s** | **120 MBit/s** | **300 MBit/s** | **600 MBit/s** |

##### <a name="small-group"></a>Kleine Gruppe

Die Routingkosten sind für das Senden von Nachrichten an viele kleine Gruppen beträchtlich. Derzeit erreicht die Implementierung des Azure Web PubSub-Diensts die Routingkostengrenze bei Unit50. Das Erhöhen der Anzahl von CPUs und Arbeitsspeicher hilft in diesem Fall nicht, daher kann Unit100 vorsätzlich nicht verbessert werden. Wenn Sie eine höhere eingehende Bandbreite benötigen, wenden Sie sich an den Kundensupport.

|   An kleine Gruppe senden     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20  | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Verbindungen               | 1\.000 | 2\.000 | 5\.000  | 10.000 | 20.000 | 50.000 | 100.000 |
| Anzahl der Gruppenmitglieder        | 10    | 10    | 10     | 10     | 10     | 10     | 10 |
| Gruppenanzahl               | 100   | 200   | 500    | 1\.000  | 2\.000  | 5\.000  | 10.000 |
| Eingehende Nachrichten pro Sekunde  | 400 | 800  | 2\.000 | 4\.000 | 8\.000 | 15.000 | 15.000 |
| Eingehende Bandbreite  | 800 KBit/s | 1,6 MBit/s | 4 MBit/s    | 8 MBit/s    | 16 MBit/s   | 30 MBit/s  | 30 MBit/s   |
| Ausgehende Nachrichten pro Sekunde | 4\.000 | 8\.000 | 20.000 | 40.000 | 80.000 | 150.000 | 150.000 |
| Ausgehende Bandbreite | **8 MBit/s** | **16 MBit/s** | **40 MBit/s** | **80 MBit/s** | **160 MBit/s** | **300 MBit/s** | **300 MBit/s** |

### <a name="triggering-cloud-event"></a>Auslösen eines Cloudereignisses 
Der Dienst übergibt Clientereignisse mithilfe des [CloudEvents-HTTP-Protokolls](./reference-cloud-events.md) an den Upstreamwebhook.

![Upstreamwebhook](./media/concept-performance/upstream.png)

Für jedes Ereignis formuliert er eine HTTP-POST-Anforderung an den registrierten Upstream und erwartet eine HTTP-Antwort. 

> [!NOTE]
> Web PubSub unterstützt auch HTTP 2.0 für die Bereitstellung von Upstreamereignissen. Das folgende Ergebnis wird mit HTTP 1.1 getestet. Wenn Ihr App-Server HTTP 2.0 unterstützt, ist die Leistung besser.

#### <a name="echo"></a>Echo

In diesem Fall schreibt der App-Server die ursprüngliche Nachricht zurück in die HTTP-Antwort. Das Verhalten von **Echo** bestimmt, dass die maximale eingehende Bandbreite gleich der maximalen ausgehenden Bandbreite ist. Details finden Sie in der folgenden Tabelle.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindungen                       | 1\.000 | 2\.000 | 5\.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Ein-/Ausgehende Nachrichten pro Sekunde | 500 | 1\.000 | 2\.500 | 5\.000 | 10.000 | 25.000 | 50.000 |
| Ein-/Ausgehende Bandbreite | **1 MBit/s** | **2 MBit/s** | **5 MBit/s** | **10 MBit/s** | **20 MBit/s** | **50 MBit/s** | **100 MBit/s** |



### <a name="rest-api"></a>REST-API

Azure Web PubSub bietet leistungsstarke [APIs](/rest/api/webpubsub/) zum Verwalten von Clients und Senden von Echtzeitnachrichten.

![Abbildung des gesamten Workflows des Web PubSub-Diensts mithilfe von REST-APIs](./media/concept-performance/rest-api.png)

#### <a name="send-to-user-through-rest-api"></a>Über die REST-API an Benutzer senden
Die Benchmark weist allen Clients Benutzernamen zu, bevor diese mit dem Herstellen einer Verbindung mit dem Azure Web PubSub-Dienst beginnen. 

|   Über die REST-API an Benutzer senden | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Verbindungen               | 1\.000 | 2\.000 | 5\.000  | 10.000 | 20.000 | 50.000  | 100.000 |
| Ein-/Ausgehende Nachrichten pro Sekunde | 180 | 360 | 900    | 1\.800 | 3,600 | 9\.000 | 18.000  |
| Ein-/Ausgehende Bandbreite           | **360 KBit/s** | **720 KBit/s** | **1,8 MBit/s** | **3,6 MBit/s** | **7,2 MBit/s** | **18 MBit/s** | **36 MBit/s** |

#### <a name="broadcast-through-rest-api"></a>Übertragen über die REST-API
Das Bandbreitenlimit ist identisch mit dem Limit für **An große Gruppe senden**.

|   Übertragen über die REST-API     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Verbindungen               | 1\.000 | 2\.000 | 5\.000  | 10.000 | 20.000 | 50.000  | 100.000 |
| Eingehende Nachrichten pro Sekunde  | 3    | 3    | 3     | 3     | 3     | 3      | 3      |
| Ausgehende Nachrichten pro Sekunde | 3,000 | 6.000 | 15.000 | 30.000 | 60.000 | 150.000 | 300.000 |
| Eingehende Bandbreite  | 6 KBit/s   | 6 KBit/s   | 6 KBit/s    | 6 KBit/s    | 6 KBit/s    | 6 KBit/s     | 6 KBit/s     |
| Ausgehende Bandbreite | **6 MBit/s** | **12 MBit/s** | **30 MBit/s** | **60 MBit/s** | **120 MBit/s** | **300 MBit/s** | **600 MBit/s** |

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]