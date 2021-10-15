---
title: 'Konzepte: Nachhaltige Softwareentwicklung in Azure Kubernetes Service (AKS)'
description: Hier finden Sie Informationen zur nachhaltigen Softwareentwicklung in Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/29/2021
ms.openlocfilehash: 3fb40b278a4107cb1cdba2230602b99e2bdfff85
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355482"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Prinzipien für eine nachhaltige Softwareentwicklung in Azure Kubernetes Service (AKS)

Bei den Prinzipien für eine nachhaltige Softwareentwicklung handelt es sich um eine Reihe von Kompetenzen, die Ihnen dabei helfen, nachhaltige Anwendungen zu definieren, zu erstellen und auszuführen. Das Ziel besteht darin, die CO2-Bilanz jedes Aspekts Ihrer Anwendung zu verbessern. Das Projekt [The Principles of Sustainable Software Engineering][principles-sse] enthält eine Übersicht über Prinzipien für eine nachhaltige Softwareentwicklung.

Eine nachhaltige Softwareentwicklung bedeutet eine Verlagerung der Prioritäten und des Schwerpunkts. Bei der Entwicklung und Ausführung eines Großteils der Software stehen zumeist hohe Geschwindigkeit und kurze Wartezeiten im Vordergrund. Bei der nachhaltigen Softwareentwicklung geht es hingegen darum, die CO2-Emissionen so weit wie möglich zu senken. Berücksichtigen Sie dabei Folgendes:

* Das Anwenden von Prinzipien für eine nachhaltige Softwareentwicklung kann zur Beschleunigung oder zur Verringerung von Wartezeiten beitragen – etwa durch die Verkürzung der zurückzulegenden Netzwerkstrecke. 
* Das Senken der CO2-Emissionen führt ggf. zur Verlangsamung oder zu längeren Wartezeiten – etwa aufgrund der Zurückstellung von Workloads mit niedriger Priorität. 

Überprüfen Sie die Prioritäten, Anforderungen und Nachteile für Ihre Anwendung, bevor Sie Prinzipien für eine nachhaltige Softwareentwicklung auf Ihre Anwendung anwenden.

## <a name="measure-and-optimize"></a>Messen und Optimieren

Wenn Sie die CO2-Bilanz Ihrer AKS-Cluster verbessern möchten, müssen Sie wissen, wie die Ressourcen des Clusters verwendet werden. [Azure Monitor][azure-monitor] liefert ausführliche Informationen zur Ressourcenauslastung Ihres Clusters (beispielsweise zur Auslastung von Arbeitsspeicher und CPU). Auf der Grundlage dieser Daten können Sie fundierte Entscheidungen zur Verbesserung der CO2-Bilanz Ihres Clusters treffen und die Wirkung Ihrer Änderungen überwachen. 

Sie können auch den [Microsoft Sustainability Calculator][sustainability-calculator] installieren, um die CO2-Bilanz Ihrer Azure-Ressourcen zu ermitteln.

## <a name="increase-resource-utilization"></a>Erhöhen der Ressourcenauslastung

Eine Möglichkeit zur Verbesserung Ihrer CO2-Bilanz ist eine Verringerung der Leerlaufzeit. Zur Verringerung der Leerlaufzeit muss die Auslastung Ihrer Computeressourcen erhöht werden. Beispiel:
1. Ihr Cluster enthält vier Knoten, die jeweils nur zur Hälfte ausgelastet sind. Somit bleibt bei allen vier Knoten 50 % der Kapazität ungenutzt und befindet sich im Leerlauf. 
1. Sie reduzieren nun Ihren Cluster auf drei Knoten, die dann bei gleicher Workload jeweils zu 67 % ausgelastet sind. Sie haben dadurch die ungenutzte Kapazität auf jedem Knoten erfolgreich auf 33 % verringert und die Auslastung erhöht.

> [!IMPORTANT]
> Wenn Sie Änderungen an den Ressourcen in Ihrem Cluster in Betracht ziehen, vergewissern Sie sich, dass in Ihren [Systempools][system-pools] genügend Ressourcen zur Verfügung stehen, um die Stabilität der Kernsystemkomponenten Ihres Clusters zu gewährleisten. Verringern Sie die Ressourcen Ihres Clusters **niemals** so weit, dass womöglich die Stabilität Ihres Clusters beeinträchtigt wird.

Nach der Überprüfung der Clusterauslastung empfiehlt sich ggf. die Verwendung der Features, die durch [mehrere Knotenpools][multiple-node-pools] bereitgestellt werden: 

* Knotendimensionierung

    Mittels [Knotendimensionierung][node-sizing] können Sie Knotenpools mit spezifischen CPU- und Arbeitsspeicherprofilen definieren und Ihre Knoten so an Ihre Workloadanforderungen anpassen. Durch die Anpassung der Knotengröße an Ihre Workloadanforderungen können einige Knoten mit höherer Auslastung betrieben werden. 

* Clusterskalierung

    Konfigurieren Sie die [Skalierung][scale] Ihres Clusters. Verwenden Sie die [horizontale automatische Podskalierung][scale-horizontal] und die [automatische Clusterskalierung][scale-auto], um Ihren Cluster automatisch gemäß Ihrer Konfiguration zu skalieren. Steuern Sie die Clusterskalierung so, dass alle Ihre Knoten mit hoher Auslastung betrieben werden und mit Änderungen der Clusterworkload synchron bleiben. 

* Spot-Pools

    Für Workloads, bei denen plötzliche Unterbrechungen oder Beendigungen kein Problem sind, können [Spot-Pools][spot-pools] verwendet werden. Spot-Pools nutzen Leerlaufkapazität in Azure. Spot-Pools eignen sich beispielsweise unter Umständen für Batchaufträge oder für Entwicklungsumgebungen.

> [!NOTE]
>Durch die Erhöhung der Auslastung lässt sich ggf. auch die Anzahl überschüssiger Knoten verringern, was wiederum zur Senkung des Energieverbrauchs durch [Ressourcenreservierungen für die einzelnen Knoten][resource-reservations] beiträgt.

Überprüfen Sie schließlich auch die *Anforderungen* und *Grenzwerte* für CPU und Arbeitsspeicher in den Kubernetes-Manifesten Ihrer Anwendungen. 
* Wenn Sie die Werte für Arbeitsspeicher und CPU senken, stehen dem Cluster mehr Arbeitsspeicher- und CPU-Ressourcen für die Ausführung anderer Workloads zur Verfügung. 
* Wenn Sie mehr Workloads mit weniger CPU- und Arbeitsspeicherressourcen ausführen, verdichtet sich Ihre Clusterzuordnung, und Ihre Auslastung erhöht sich. 

Die Verringerung der CPU- und Arbeitsspeicherressourcen für Ihre Anwendungen kann dazu führen, dass sich das Verhalten Ihrer Anwendungen verschlechtert oder dass sie instabil werden, wenn Sie die Werte für Arbeitsspeicher und CPU zu stark senken. Führen Sie vor dem Ändern der *Anforderungen* und *Grenzwerte* für CPU und Arbeitsspeicher einige Benchmarktests aus, um zu überprüfen, ob die Werte angemessen sind. Verringern Sie diese Werte niemals so weit, dass Ihre Anwendung instabil wird.

## <a name="reduce-network-travel"></a>Verkürzen der zurückzulegenden Netzwerkstrecke

Durch eine Verkürzung der Strecke, die von Anforderungen an sowie Antworten von Ihrem Cluster zurückgelegt werden muss, können Sie die CO2-Emissionen und den Stromverbrauch der Netzwerkgeräte senken. Nach der Überprüfung Ihres Netzwerkdatenverkehrs empfiehlt es sich gegebenenfalls, Cluster [in Regionen][regions] zu erstellen, die weniger weit von der Quelle Ihres Netzwerkdatenverkehrs entfernt sind. Sie können [Azure Traffic Manager][azure-traffic-manager] verwenden, um Datenverkehr an den nächstgelegenen Cluster weiterzuleiten, sowie [Näherungsplatzierungsgruppen][proiximity-placement-groups], um die Entfernung zwischen Azure-Ressourcen zu verringern.

> [!IMPORTANT]
> Wenn Sie Änderungen am Netzwerk Ihres Clusters in Erwägung ziehen, verkürzen Sie die zurückzulegende Netzwerkstrecke niemals auf Kosten der Erfüllung von Workloadanforderungen. Ein Beispiel: Bei Verwendung von [Verfügbarkeitszonen][availability-zones] müssen Daten im Netzwerk Ihres Clusters zwar eine längere Strecke zurücklegen, doch sind Verfügbarkeitszonen unter Umständen zur Erfüllung von Workloadanforderungen erforderlich.

## <a name="demand-shaping"></a>Strukturieren von Anforderungen

Versuchen Sie nach Möglichkeit, die Anforderungen für die Ressourcen Ihres Clusters auf Zeiten oder Regionen zu verlagern, in denen Sie Überkapazitäten nutzen können. Ziehen Sie z. B. Folgendes in Betracht:
* Ändern der Zeit oder Region für die Ausführung eines Batchauftrags
* Verwenden von [Spot-Pools][spot-pools] 
* Umgestalten Ihrer Anwendung zur Verwendung einer Warteschlange, um die Ausführung von Workloads zurückzustellen, die nicht sofort verarbeitet werden müssen

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den AKS-Features aus diesem Artikel finden Sie hier:

* [Mehrere Knotenpools][multiple-node-pools]
* [Knotendimensionierung][node-sizing]
* [Skalieren eines Clusters][scale]
* [Horizontale automatische Podskalierung][scale-horizontal]
* [Automatische Clusterskalierung ][scale-auto]
* [Spot-Pools][spot-pools]
* [Systempools][system-pools]
* [Ressourcenreservierungen][resource-reservations]
* [Näherungsplatzierungsgruppen][proiximity-placement-groups]
* [Verfügbarkeitszonen][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principles-sse]: /learn/modules/sustainable-software-engineering-overview/
