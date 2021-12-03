---
title: Azure Service Bus-Tarife Premium und Standard
description: Dieser Artikel beschreibt die Tarife Standard und Premium von Azure Service Bus. Er vergleicht diese Tarife und erläutert technische Unterschiede.
ms.topic: conceptual
ms.date: 11/08/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0734e6d7d54966617e66a5ac5876df4a1da9264f
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028779"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Service Bus Premium- und Standard-Preisstufe für Messaging

Service Bus-Messaging, das Entitäten wie Warteschlangen und Themen umfasst, kombiniert Messagingfunktionen für Unternehmen mit umfangreicher Veröffentlichen/Abonnieren-Semantik auf Cloudebene. Service Bus-Messaging wird als Kommunikationsbackbone für zahlreiche komplexe Cloudlösungen verwendet.

Der *Premium*-Tarif von Service Bus-Messaging ist für allgemeine Kundenanfragen zur Skalierung, Leistung und Verfügbarkeit für unternehmenswichtige Anwendungen vorgesehen. Für Produktionsszenarien wird der Premium-Tarif empfohlen. Obwohl die Funktionssätze fast identisch sind, sind diese zwei Stufen von Service Bus-Messaging für verschiedene Anwendungsfälle vorgesehen.

In der folgenden Tabelle sind einige allgemeine Unterschiede hervorgehoben:

| Premium | Standard |
| --- | --- |
| Hoher Durchsatz |Variabler Durchsatz |
| Vorhersagbare Leistung |Variable Latenzzeit |
| Feste Preise |Variable Preisgestaltung (nutzungsbasierte Bezahlung) |
| Möglichkeit zur Herauf- und Herunterskalierung der Workload |– |
| Nachrichtengröße bis 100 MB. Weitere Informationen finden Sie unter [Unterstützung großer Nachrichten](#large-messages-support). |Nachrichtengröße bis 256 KB |

**Service Bus Premium-Messaging** bietet Ressourcenisolierung auf CPU- und Arbeitsspeicherebene, sodass die Workloads der einzelnen Kunden isoliert ausgeführt werden. Dieser Ressourcencontainer wird als *Messaging-Einheit* bezeichnet. Jedem Premium-Namespace wird mindestens eine Messaging-Einheit zugeordnet. Sie können 1, 2, 4, 8 oder 16 Messagingeinheiten für jeden Service Bus Premium-Namespace erwerben. Eine einzelne Workload oder Entität kann mehrere Messagingeinheiten umfassen, und die Anzahl der Einheiten kann beliebig geändert werden. Das Ergebnis ist eine vorhersehbare und wiederholbare Leistung Ihrer Service Bus-basierten Lösung.

Diese Leistung ist nicht nur besser vorhersehbar und verfügbar, sondern auch schneller. Mit Premium-Messaging wird bei Spitzenleistung eine viel höhere Geschwindigkeit als beim Standard-Tarif erzielt.

## <a name="premium-messaging-technical-differences"></a>Premium-Messaging – technische Unterschiede

In den folgenden Abschnitten werden einige Unterschiede zwischen der Premium- und der Standard-Messagingstufe erläutert.

### <a name="partitioned-queues-and-topics"></a>Partitionierte Warteschlangen und Themen

Partitionierte Warteschlangen und Themen werden bei Premium-Messaging nicht unterstützt. Weitere Informationen zur Partitionierung finden Sie unter [Partitionierte Warteschlangen und Themen](service-bus-partitioning.md).

### <a name="express-entities"></a>Expressentitäten

Da Premium-Messaging in einer isolierten Laufzeitumgebung ausgeführt wird, werden Expressentitäten in Premium-Namespaces nicht unterstützt. Bei einer Express-Entität wird eine Nachricht vorübergehend im Arbeitsspeicher abgelegt, bevor sie in den persistenten Speicher geschrieben wird. Wenn Code unter Standardmessaging ausgeführt wird, den Sie auf den Premium-Tarif portieren möchten, stellen Sie sicher, dass das Feature für Express-Entitäten deaktiviert ist.

## <a name="premium-messaging-resource-usage"></a>Premium-Messaging-Ressourcennutzung
Grundsätzlich kann jeder Vorgang für eine Entität CPU- und Arbeitsspeichernutzung verursachen. Nachstehend sind einige dieser Vorgänge aufgeführt: 

- Verwaltungsvorgänge, z. B. Erstell-, Abruf-, Aktualisier- und Löschvorgänge, für Warteschlangen, Themen und Abonnements
- Laufzeitvorgänge (Nachrichten senden und empfangen)
- Überwachen von Vorgängen und Warnungen

Die zusätzliche CPU- und Speichernutzung wird jedoch nicht zusätzlich berechnet. Im Premium-Messaging-Tarif gibt es einen einzigen Preis für die Nachrichteneinheit.

Die CPU- und die Arbeitsspeichernutzung werden aus folgenden Gründen nachverfolgt und für Sie angezeigt: 

- Bereitstellen von transparenter Einsicht in interne Systemabläufe
- Verstehen der Kapazität der erworbenen Ressourcen
- Kapazitätsplanung, damit Sie über Hoch-/Herunterskalieren entscheiden können

## <a name="messaging-unit---how-many-are-needed"></a>Messagingeinheit: Wie viele werden benötigt?

Wenn Sie einen Azure Service Bus-Namespace im Tarif „Premium“ bereitstellen, muss die Anzahl zugeordneter Messagingeinheiten angegeben werden. Bei diesen Messagingeinheiten handelt es sich um dedizierte Ressourcen, die dem Namespace zugeordnet werden.

Die Anzahl von Messagingeinheiten, die dem Service Bus-Namespace im Tarif „Premium“ zugeordnet werden, kann **dynamisch angepasst** werden, um auf Veränderungen (Zu- oder Abnahme) bei Workloads zu reagieren.

Bei der Entscheidung über die Anzahl der Messagingeinheiten für Ihre Architektur müssen einige Faktoren berücksichtigt werden:

- Beginnen Sie mit ***ein bis zwei Messagingeinheiten***, die Ihrem Namespace zugeordnet sind.
- Sehen Sie sich in den [Metriken zur Ressourcennutzung](monitor-service-bus-reference.md#resource-usage-metrics) für Ihren Namespace die Metriken zur CPU-Auslastung an.
    - Bei einer CPU-Auslastung von ***unter 20 Prozent** _ können Sie die Anzahl von Messagingeinheiten, die Ihrem Namespace zugeordnet sind, ggf. _ *_herunterskalieren_**.
    - Bei einer CPU-Auslastung von ***über 70 Prozent** _ verbessert sich die Leistung Ihrer Anwendung, wenn Sie die Anzahl von Messagingeinheiten, die Ihrem Namespace zugeordnet sind, _ *_hochskalieren_**.

Informationen zum Konfigurieren eines Service Bus-Namespace für automatisches Skalieren (Erhöhen oder Verringern von Messagingeinheiten) finden Sie unter [Automatisches Aktualisieren von Messagingeinheiten](automate-update-messaging-units.md).

> [!NOTE]
> Die Ressourcen, die dem Namespace zugeordnet werden, können präventiv oder reaktiv **skaliert** werden.
>
>  * **Präventiv:** Wenn zusätzliche Workloads erwartet werden (saisonbedingt oder aufgrund von Trends), können Sie dem Namespace weitere Messagingeinheiten zuordnen, bevor die Workloads auftreten.
>
>  * **Reaktiv:** Wenn bei der Betrachtung der Metriken zur Ressourcennutzung zusätzliche Workloads erkannt werden, können dem Namespace zusätzliche Ressourcen zugeordnet werden, um auf die steigende Nachfrage zu reagieren.
>
> Die Verbrauchseinheiten für die Service Bus-Abrechnung sind stundenbasiert. Wenn Sie zentral hochskalieren, werden die zusätzlichen Ressourcen nur für die Stunden berechnet, in denen sie verwendet wurden.
>

## <a name="get-started-with-premium-messaging"></a>Erste Schritte mit Premium-Messaging

Die ersten Schritte mit Premium-Messaging sind einfach, und der Prozess ähnelt der Vorgehensweise für Standard-Messaging. Beginnen Sie durch [Erstellung eines Namespace](service-bus-create-namespace-portal.md) im [Azure-Portal](https://portal.azure.com). Stellen Sie sicher, dass Sie unter **Tarif** die Option **Premium** wählen. Klicken Sie auf **Alle Preisinformationen anzeigen**, um weitere Informationen zu jedem Tarif anzuzeigen.

:::image type="content" source="./media/service-bus-premium-messaging/select-premium-tier.png" alt-text="Screenshot: Auswählen des Premium-Tarifs beim Erstellen eines Namespace":::

Sie können auch [Premium-Namespaces mit Azure Resource Manager-Vorlagen erstellen](https://azure.microsoft.com/resources/templates/servicebus-pn-ar/).

## <a name="large-messages-support"></a>Unterstützung für umfangreiche Nachrichten
Namespaces im Premium-Tarif in Azure Service Bus unterstützen die Möglichkeit, große Nachrichtennutzlasten von bis zu 100 MB zu senden. Dieses Feature ist hauptsächlich auf Legacyworkloads ausgelegt, die größere Nachrichtennutzlasten für andere Messagingbroker für Unternehmen verwendet haben und eine nahtlose Migration zu Azure Service Bus erfordern.

Dies sind einige Überlegungen zum Senden großer Nachrichten in Azure Service Bus:
   * Wird nur in Premium-Namespaces in Azure Service Bus unterstützt.
   * Wird nur bei Verwendung des AMQP-Protokolls unterstützt. Wird bei Verwendung des SBMP-Protokolls nicht unterstützt.
   * Wird unterstützt, wenn das [Java Message Service 2.0-Client-SDK (JMS)](how-to-use-java-message-service-20.md) und andere Sprachclient-SDKs verwendet werden.
   * Das Senden großer Nachrichten führt zu einem verringerten Durchsatz und einer höheren Latenz.
   * Obwohl Nachrichtennutzlasten von 100 MB unterstützt werden, wird empfohlen, die Nachrichtennutzlasten so klein wie möglich zu halten, um eine zuverlässige Leistung des Service Bus-Namespace sicherzustellen.
   * Die maximale Nachrichtengröße wird nur für Nachrichten erzwungen, die an die Warteschlange oder das Thema gesendet werden. Das Größenlimit wird für den Empfangsvorgang nicht erzwungen. Sie können die maximale Nachrichtengröße für eine bestimmte Warteschlange (oder ein bestimmtes Thema) aktualisieren.

### <a name="enabling-large-messages-support-for-a-new-queue-or-topic"></a>Aktivieren der Unterstützung großer Nachrichten für eine neue Warteschlange (oder ein neues Thema)

Legen Sie wie unten dargestellt die maximale Nachrichtengröße beim Erstellen einer neuen Warteschlange (oder eines Themas) fest, um die Unterstützung für große Nachrichten zu aktivieren. 

:::image type="content" source="./media/service-bus-premium-messaging/large-message-preview.png" alt-text="Screenshot: Aktivieren der Unterstützung großer Nachrichten für eine vorhandene Warteschlange":::

### <a name="enabling-large-messages-support-for-an-existing-queue-or-topic"></a>Aktivieren der Unterstützung großer Nachrichten für eine vorhandene Warteschlange (oder ein vorhandenes Thema)

Sie können auch die Unterstützung für große Nachrichten für vorhandene Warteschlangen (oder Themen) aktivieren, indem Sie unter **_Übersicht_** wie im Folgenden gezeigt **Maximale Nachrichtengröße** für diese bestimmte Warteschlange (oder für das bestimmte Thema) aktualisieren.

:::image type="content" source="./media/service-bus-premium-messaging/large-message-preview-update.png" alt-text="Screenshot: Seite „Warteschlange erstellen“ mit aktivierter Unterstützung für große Nachrichten":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Service Bus-Messaging finden Sie unter folgenden Links:

- [Automatisches Aktualisieren von Messagingeinheiten](automate-update-messaging-units.md)
- [Introducing Azure Service Bus Premium Messaging (Blogbeitrag)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/) (Einführung in Azure Service Bus Premium-Messaging)
- [Introducing Azure Service Bus Premium Messaging (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging) (Einführung in Azure Service Bus Premium-Messaging)
