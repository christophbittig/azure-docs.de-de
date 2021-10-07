---
title: Bereitstellen eines verwalteten Service Fabric-Clusters über Verfügbarkeitszonen hinweg
description: Erfahren Sie, wie Sie einen verwalteten Service Fabric-Cluster über Verfügbarkeitszonen hinweg bereitstellen und in einer ARM-Vorlage konfigurieren.
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: b3c4e01ea66aa3a69b14806f2701020f1f529a5e
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544373"
---
# <a name="deploy-a-service-fabric-managed-cluster-across-availability-zones"></a>Bereitstellen eines verwalteten Service Fabric-Clusters über Verfügbarkeitszonen hinweg

Verfügbarkeitszonen in Azure sind ein Hochverfügbarkeitsangebot, das Anwendungen und Daten vor Ausfällen von Rechenzentren schützt. Eine Verfügbarkeitszone ist ein eindeutiger physischer Standort, der mit unabhängiger Stromversorgung, Kühlung und Netzwerk innerhalb einer Azure-Region ausgestattet ist.

Ein verwalteter Service Fabric-Cluster unterstützt Bereitstellungen, die sich über mehrere Verfügbarkeitszonen erstrecken, um Zonenresilienz bereitzustellen. Durch diese Konfiguration wird Hochverfügbarkeit der wichtigen Systemdienste und Ihrer Anwendungen sichergestellt, um Schutz vor Single Points of Failure zu gewährleisten. Azure-Verfügbarkeitszonen sind nur in ausgewählten Regionen verfügbar. Weitere Informationen finden Sie unter [Übersicht über Azure-Verfügbarkeitszonen](../availability-zones/az-overview.md).

>[!NOTE]
>Die verfügbarkeitszonenübergreifende Funktion ist nur in Standard-SKU-Clustern verfügbar.

Es sind Beispielvorlagen verfügbar: [Verfügbarkeitszonenübergreifende Service Fabric-Vorlage](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommendations-for-zone-resilient-azure-service-fabric-managed-clusters"></a>Empfehlungen für zonenresiliente verwaltete Azure Service Fabric-Cluster
Ein Service Fabric-Cluster, der über die Verfügbarkeitszonen verteilt ist, stellt die Hochverfügbarkeit des Clusterzustands sicher. 

Die empfohlene Topologie für verwaltete Cluster erfordert die nachfolgend beschriebenen Ressourcen:

* Die Cluster-SKU muss Standard sein.
* Der primäre Knotentyp sollte mindestens neun Knoten für optimale Resilienz aufweisen, unterstützt werden jedoch mindestens sechs Knoten.
* Die sekundären Knotentypen sollten mindestens sechs Knoten für optimale Resilienz aufweisen, unterstützt werden jedoch mindestens drei Knoten.

>[!NOTE]
>Es werden nur drei Bereitstellungen von Verfügbarkeitszonen unterstützt.

>[!NOTE]
> Es ist nicht möglich, einen verwalteten Cluster direkt von einem nicht übergreifenden Cluster in einen übergreifenden Cluster zu ändern.

Abbildung, die die Architektur der Azure Service Fabric-Verfügbarkeitszone zeigt ![Architektur der Azure Service Fabric-Verfügbarkeitszone][sf-multi-az-arch]

Beispiel Knotenliste mit FD/UD-Formaten in einer zonenübergreifenden VM-Skalierungsgruppe

 ![Beispiel Knotenliste mit FD/UD-Formaten in einer zonenübergreifenden VM-Skalierungsgruppe.][sfmc-multi-az-nodes]

**Zonenübergreifende Verteilung von Dienstreplikaten**: Wenn ein Dienst auf den zonenübergreifenden nodeTypes bereitgestellt wird, werden die Replikate so platziert, dass sichergestellt ist, dass sie in separaten Zonen angeordnet werden. Diese Trennung wird sichergestellt, da die Fehlerdomäne auf den Knoten, die in jedem dieser nodeTypes vorhanden sind, mit den Zoneninformationen konfiguriert ist (d. h. FD = fd:/zone1/1 usw.). Beispiel: für fünf Replikate oder Instanzen eines Diensts lautet die Verteilung 2-2-1, und die Runtime versucht, eine gleichmäßige Verteilung über Verfügbarkeitszonen hinweg sicherzustellen.

**Replikatkonfigurationfür den Benutzerdienst**: Zustandsbehaftete Benutzerdienste, die auf den nodeTypes für zonenübergreifende Verfügbarkeit bereitgestellt werden, müssen mit dieser Konfiguration konfiguriert werden: Replikatanzahl mit Ziel = 9, min = 5. Diese Konfiguration unterstützt den Dienst auch dann, wenn eine Zone ausfällt, da in den anderen beiden Zonen weiterhin 6 Replikate in Betrieb sind. Auch ein Anwendungsupgrade wird in einem solchen Szenario durchlaufen.

**Szenario bei Zonenausfall**: Wenn eine Zone ausfällt, werden alle Knoten in dieser Zone als ausgefallen angezeigt. Auch die Dienstreplikate auf diesen Knoten sind ausgefallen. Da in den anderen Zonen Replikate vorhanden sind, reagiert der Dienst weiterhin mit einem Failover der primären Replikate auf die Zonen, die funktionsfähig sind. Die Dienste werden mit dem Status „Warnung“ angezeigt, da die Anzahl der Zielreplikate nicht erreicht wurde und die VM-Anzahl immer noch über der definierten minimalen Zielreplikatgröße liegt. Daher startet der Service Fabric-Lastenausgleich in den Arbeitszonen so viele Replikate, dass die Anzahl mit der konfigurierten Zielreplikatanzahl übereinstimmt. An diesem Punkt erscheinen die Dienste fehlerfrei. Wenn die ausgefallene Zone erneut verfügbar ist, verteilt der Lastenausgleich alle Dienstreplikate gleichmäßig auf alle Zonen.

## <a name="networking-configuration"></a>Netzwerkkonfiguration
Weitere Informationen finden Sie unter [Konfigurieren von Netzwerkeinstellungen für verwaltete Service Fabric-Cluster](./how-to-managed-cluster-networking.md).

## <a name="enabling-a-zone-resilient-azure-service-fabric-managed-cluster"></a>Aktivieren eines zonenresilienten verwalteten Azure Service Fabric-Clusters
Um einen zonenresilienten verwalteten Azure Service Fabric-Cluster zu aktivieren, müssen Sie Folgendes in die Ressourcendefinition des verwalteten Clusters einschließen.

* Die **ZonalResiliency**-Eigenschaft, die angibt, ob der Cluster zonenresilient ist.

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters",
  "zonalResiliency": "true"
}
```
[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sfmc-multi-az-nodes]: ./media/how-to-managed-cluster-availability-zones/sfmc-multi-az-nodes.png
