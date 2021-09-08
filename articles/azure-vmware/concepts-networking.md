---
title: Konzepte – Netzwerkinterkonnektivität
description: Erfahren Sie mehr über wichtige Aspekte und Anwendungsfälle für Netzwerke und Interkonnektivität in Azure VMware Solution.
ms.topic: conceptual
ms.date: 06/28/2021
ms.openlocfilehash: 91bd1bfc207a2fb08b644e4ecf54be7b254c39fa
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122350227"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware Solution – Netzwerk- und Interkonnektivitätskonzepte

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Es gibt zwei Möglichkeiten für die Interkonnektivität in der privaten Azure VMware Solution-Cloud:

- [**Die allgemeine reine Azure-Interkonnektivität**](#azure-virtual-network-interconnectivity) ermöglicht Ihnen die Verwaltung und Nutzung Ihrer privaten Cloud mit nur einem einzelnen virtuellen Netzwerk in Azure. Diese Implementierung eignet sich am besten für Azure VMware Solution-Evaluierungen oder -Implementierungen, die keinen Zugriff von lokalen Umgebungen erfordern.

- [**Vollständige Interkonnektivität zwischen lokalen und privaten Clouds**](#on-premises-interconnectivity) erweitert die allgemeine reine Azure-Implementierung um die Interkonnektivität zwischen lokalen und privaten Azure VMware Solution-Clouds.
 
In diesem Artikel werden die wichtigsten Konzepte für Netzwerke und Interkonnektivität (einschließlich Anforderungen und Beschränkungen) beschrieben. Darüber hinaus erhalten Sie in diesem Artikel die Informationen, die Sie für die Arbeit mit Azure VMware Solution zur Konfiguration Ihres Netzwerks benötigen.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Anwendungsfälle für private Azure VMware Solution-Clouds

Die Anwendungsfälle für private Azure VMware Solution-Clouds umfassen:
- Neue VMware-VM-Workloads in der Cloud
- Bursting von VM-Workloads in die Cloud (nur lokal zu Azure VMware Solution)
- Migration von VM-Workloads in die Cloud (nur lokal zu Azure VMware Solution)
- Notfallwiederherstellung (Azure VMware Solution zu Azure VMware Solution oder lokal zu Azure VMware Solution)
- Nutzung von Azure-Diensten

> [!TIP]
> Bei allen Anwendungsfällen für den Azure VMware Solution-Dienst bestehen Verbindungen zwischen lokalen Standorten und privaten Clouds.

## <a name="azure-virtual-network-interconnectivity"></a>Azure Virtual Network-Interkonnektivität

Sie können Ihr virtuelles Azure-Netzwerk mit der Implementierung der privaten Azure VMware Solution-Cloud verbinden. Sie können Ihre private Azure VMware Solution-Cloud verwalten, Workloads in Ihrer privaten Cloud nutzen und auf weitere Azure-Dienste zugreifen.

Im folgenden Diagramm ist die grundlegende Netzwerkinterkonnektivität dargestellt, die während der Bereitstellung einer privaten Cloud hergestellt wird. Die Abbildung zeigt logische Netzwerkverbindungen zwischen einem virtuellen Netzwerk in Azure und einer privaten Cloud. Diese Verbindung wird über ein ExpressRoute-Back-End hergestellt, das Teil des Azure VMware Solution-Diensts ist. Die Interkonnektivität wird den folgenden drei primären Anwendungsfällen gerecht:

- Eingehender Zugriff auf vCenter Server und NSX-T-Manager (bei denen der Zugriff über VMs in Ihrem Azure-Abonnement erfolgt).
- Ausgehender Zugriff von VMs in der privaten Cloud auf Azure-Dienste.
- Eingehender Zugriff von Workloads, die in der privaten Cloud ausgeführt werden.


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Diagramm: Grundlegende Netzwerkinterkonnektivität, die zum Zeitpunkt der Bereitstellung einer privaten Azure VMware Solution-Cloud eingerichtet wurde" border="false":::

## <a name="on-premises-interconnectivity"></a>Lokale Interkonnektivität

Im vollständig vernetzten Szenario können Sie über Ihre virtuellen Azure-Netzwerke und lokal auf Azure VMware Solution zugreifen. Diese Implementierung ist eine Erweiterung der grundlegenden Implementierung, die im vorherigen Abschnitt beschrieben wurde. Um aus lokalen Umgebungen eine Verbindung mit Ihrer privaten Azure VMware Solution-Cloud herzustellen, ist eine ExpressRoute-Verbindung erforderlich.

Das nachstehende Diagramm zeigt die Interkonnektivität von lokalen zu privaten Clouds, die die folgenden Anwendungsfälle ermöglicht:

- Heiße/Kalte Migration mit vCenter vMotion zwischen lokalen Umgebungen und Azure VMware Solution.
- Verwaltungszugriff von der lokalen Umgebung auf die private Azure VMware Solution-Cloud.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Diagramm: Interkonnektivität zwischen virtuellen und lokalen Netzwerken und privaten Clouds" border="false":::

Für eine vollständige Interkonnektivität mit Ihrer privaten Cloud aktivieren Sie ExpressRoute Global Reach und fordern dann einen Autorisierungsschlüssel und eine ID für privates Peering für Global Reach im Azure-Portal an. Der Autorisierungsschlüssel und die Peering-ID werden verwendet, um Global Reach zwischen einer ExpressRoute-Verbindung in Ihrem Abonnement und der ExpressRoute-Verbindung für Ihre private Cloud zu aktivieren. Sobald die ExpressRoute-Leitungen verknüpft wurden, wird der Netzwerkdatenverkehr zwischen lokalen Umgebungen und der privaten Cloud über die beiden Leitungen geroutet. Weitere Informationen zu diesen Verfahren finden Sie im [Tutorial zum Erstellen eines ExpressRoute Global Reach-Peerings mit einer privaten Cloud](tutorial-expressroute-global-reach-private-cloud.md).

## <a name="limitations"></a>Einschränkungen
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>Nächste Schritte 

Nachdem Sie sich mit den Netzwerk- und Konnektivitätskonzepten bei Azure VMware Solution vertraut gemacht haben, informieren Sie sich über die folgenden Themen:

- [Speicherkonzepte von Azure VMware Solution](concepts-storage.md)
- [Identitätskonzepte von Azure VMware Solution](concepts-identity.md)
- [Aktivieren des Azure VMware Solution-Ressourcenanbieters](deploy-azure-vmware-solution.md#register-the-microsoftavs-resource-provider)

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-private-clouds-clusters#host-maintenance-and-lifecycle-management
[concepts-storage]: ./concepts-storage.md
