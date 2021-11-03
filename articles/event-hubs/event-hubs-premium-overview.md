---
title: Übersicht über Event Hubs Premium
description: Dieser Artikel bietet eine Übersicht über Azure Event Hubs Premium, das mehrinstanzenfähige Bereitstellungen von Event Hubs für anspruchsvolle Streaminganforderungen bietet.
ms.topic: article
ms.date: 10/20/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8822b44f5f2a6d5cbc367da9a8ac5d397f87e9f7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073014"
---
# <a name="overview-of-event-hubs-premium"></a>Übersicht über Event Hubs Premium

Die Dienstebene „Event Hubs Premium“ wurde für anspruchsvolle Streamingszenarien entwickelt, die eine elastische, überlegene Leistung mit vorhersehbarer Latenz benötigen. Die Leistung wird durch die Bereitstellung reservierter Compute-, Arbeitsspeicher- und Speicherressourcen erreicht, die in einer verwalteten mehrinstanzenfähigen PaaS-Umgebung Störungen unter Mandanten minimieren. 

Event Hubs Premium führt eine neue, zweistufige, nativ programmierte Protokollierungsfunktion ein, die weitaus vorhersehbarere und wesentlich geringere Sende- und Durchleitungslatenzen als die vorherige Generation bietet, ohne Kompromisse bei der Zuverlässigkeit einzugehen. Event Hubs Premium repliziert jedes Ereignis in drei Replikate, die nach Möglichkeit auf Azure-Verfügbarkeitszonen verteilt sind. Alle Replikate werden synchron in den zugrunde liegenden schnellen Speicher übertragen, bevor der Sendevorgang als abgeschlossen gemeldet wird. Ereignisse, die nicht sofort gelesen werden oder später erneut gelesen werden müssen, können bis zu 90 Tage aufbewahrt werden, wobei sie transparent auf einer redundanten Speicherebene in der Verfügbarkeitszone vorgehalten werden. Ereignisse auf den Schnellspeicher- und Aufbewahrungsspeicherebenen werden verschlüsselt. Die Verschlüsselungsschlüssel können in Event Hubs Premium von Ihnen bereitgestellt werden. 

Zusätzlich zu diesen speicherbezogenen Features, allen Funktionsmöglichkeiten und der Protokollunterstützung des Angebots „Event Hubs Standard“ ermöglicht das Isolationsmodell von Event Hubs Premium neue Features wie dynamisches Hochskalieren von Partitionen sowie noch hinzukommende künftige Funktionsmöglichkeiten. Außerdem erhalten Sie weitaus großzügigere Kontingentzuteilungen. Event Hubs Capture ist ohne Aufpreis inbegriffen.

Das Premium-Angebot wird nach [Verarbeitungseinheiten (Processing Units, PUs)](event-hubs-scalability.md#processing-units) abgerechnet, die einem Anteil isolierter Ressourcen (CPU, Arbeitsspeicher und Speicher) der zugrunde liegenden Infrastruktur entsprechen. 

Da Event Hubs Premium im Vergleich zu Event Hubs Dedicated eine Isolation innerhalb einer sehr großen mehrinstanzenfähigen Umgebung bietet, in der Ressourcen schnell verlagert werden können, ist wesentlich elastischeres und schnelleres Skalieren möglich, wobei PUs sich dynamisch anpassen lassen. Daher ist Event Hubs Premium im Vergleich zu Event Hubs Dedicated häufig eine kostengünstigere Option für mittlere Durchsatzanforderungen (<120 MB/s), insbesondere bei sich ändernden Lasten im Laufe des Tages oder der Woche. 
> [!NOTE]
> Beachten Sie, dass Event Hubs Premium nur TLS 1.2 oder höher unterstützt. 

Für die zusätzliche Stabilität dank Unterstützung von Verfügbarkeitszonen ist die minimale Bereitstellungsgröße für Event Hubs Dedicated 8 Kapazitätseinheiten (Capacity Units, CUs). Die Unterstützung von Verfügbarkeitszonen in Event Hubs Premium ist jedoch ab der ersten PU in allen Azure-Regionen verfügbar. 

Sie können für jeden Namespace 1, 2, 4, 8 und 16 Verarbeitungseinheiten erwerben. Da Event Hubs Premium ein kapazitätsbasiertes Angebot ist, wird der erreichbare Durchsatz nicht wie bei Event Hubs Standard über eine Drossel festgelegt, sondern hängt von der Leistung ab, die Sie Event Hubs abverlangen, ähnlich wie bei Event Hubs Dedicated. Der effektive Erfassungs- und Datenstromdurchsatz pro PU hängt von verschiedenen Faktoren ab, wie z. B:

* Anzahl von Producern und Consumern
* Größe der Nutzdaten 
* Partitionsanzahl
* Anforderungsrate für ausgehenden Datenverkehr 
* Nutzung von Event Hubs Capture, Schemaregistrierung und anderen erweiterten Features

Weitere Informationen finden Sie unter [Vergleich der Event Hubs-SKUs](event-hubs-quotas.md).


> [!NOTE]
> Alle Event Hubs-Namespaces sind standardmäßig für das RPC-Protokoll von Apache Kafka aktiviert und können von Ihren vorhandenen Kafka-basierten Anwendungen verwendet werden. Wenn Kafka auf Ihrem Cluster aktiviert ist, hat dies keine Auswirkungen auf Ihre nicht Kafka-basierten Anwendungsfälle. Es gibt weder eine Option noch die Notwendigkeit, Kafka in einem Cluster zu deaktivieren.

## <a name="why-premium"></a>Gründe für Event Hubs Premium

Event Hubs Premium bietet drei überzeugende Vorteile für Kunden, die eine bessere Isolation in einer mehrinstanzenfähigen Umgebung mit geringer Latenz und hohem Durchsatz bei der Datenerfassung benötigen.

#### <a name="superior-performance-with-the-new-two-tier-storage-engine"></a>Überlegene Leistung dank neuer zweistufiger Speicher-Engine

Event Hubs Premium arbeitet mit einer neuen zweistufigen Protokollspeicher-Engine, die die Leistung beim Dateneingang deutlich verbessert und Gesamtlatenz sowie die Schwankungsbreite bei der Latenz erheblich reduziert, und zwar ohne Dauerhaftigkeitsgarantien zu beeinträchtigen. 

#### <a name="better-isolation-and-predictability"></a>Bessere Isolation und Vorhersagbarkeit

Event Hubs Premium bietet eine isolierte Compute- und Arbeitsspeicherkapazität, um eine besser vorhersehbare Latenz und ein weitaus geringeres Risiko der Beeinflussung durch *andere Dienste* in einer mehrinstanzenfähigen Bereitstellung zu erreichen.

Event Hubs Premium implementiert ein *Cluster-in-Cluster*-Modell in seine mehrinstanzenfähigen Cluster, um Vorhersagbarkeit und Leistung zu gewährleisten und gleichzeitig alle Vorteile einer verwalteten mehrinstanzenfähigen PaaS-Umgebung zu erhalten. 


#### <a name="cost-savings-and-scalability"></a>Kosteneinsparungen und Skalierbarkeit
Da Event Hubs Premium ein mehrinstanzenfähiges Angebot ist, kann es flexibel und sehr schnell dynamisch skaliert werden. Die Kapazität wird in Verarbeitungseinheiten zugewiesen, die isolierte Pods von CPU/Arbeitsspeicher innerhalb des Clusters zuteilen. Die Anzahl dieser Pods kann pro Namespace hoch- oder herunterskaliert werden. Daher ist Event Hubs Premium eine kostengünstige Option für Messagingszenarien mit einem Gesamtdurchsatzbereich, der unter 120 MB/s, aber höher als der liegt, den Sie mit der Standard-SKU erreichen können.  

## <a name="quotas-and-limits"></a>Kontingente und Grenzwerte
Der Premium-Tarif bietet alle Funktionen des Standardtarifs, jedoch mit besserer Leistung, Isolation und großzügigeren Kontingenten. Weitere Informationen zu Kontingenten und Grenzwerten finden Sie unter [Kontingente und Grenzwerte in Event Hubs](event-hubs-quotas.md).


## <a name="faqs"></a>Häufig gestellte Fragen

[!INCLUDE [event-hubs-dedicated-clusters-faq](./includes/event-hubs-premium-faq.md)]

## <a name="next-steps"></a>Nächste Schritte

Im [Azure-Portal](https://portal.azure.com/#create/Microsoft.EventHub) können Sie mit der Nutzung von Event Hubs Premium (Vorschau) beginnen. Unter [Preise von Event Hubs Premium](https://azure.microsoft.com/pricing/details/event-hubs/) finden Sie weitere Informationen zu Preisen sowie [häufig gestellte Fragen zu Event Hubs](event-hubs-faq.yml) und Antworten. 
