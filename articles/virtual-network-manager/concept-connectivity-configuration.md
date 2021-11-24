---
title: Konfiguration der Konnektivität in Azure Virtual Network Manager (Vorschau)
description: Erfahren Sie mehr über die verschiedenen Arten von Netzwerktopologien, die Sie mit einer Konnektivitätskonfiguration in Azure Virtual Network Manager erstellen können.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: face052462909e755771f12ff19fed8139675183
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484596"
---
# <a name="connectivity-configuration-in-azure-virtual-network-manager-preview"></a>Konfiguration der Konnektivität in Azure Virtual Network Manager (Vorschau)

In diesem Artikel erfahren Sie mehr über die verschiedenen Arten von Konfigurationen, die Sie mit Azure Virtual Network Manager erstellen und bereitstellen können. Derzeit sind zwei Arten von Konfigurationen verfügbar: *Konnektivität* und *Sicherheitsadmins*. 

> [!IMPORTANT]
> Azure Virtual Network Manager befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connectivity-configuration"></a>Konfiguration der Konnektivität

Die *Connectivity*-Konfigurationen ermöglichen es Ihnen, verschiedene Netzwerktopologien zu erstellen, die auf Ihren Netzanforderungen basieren. Sie haben zwei Topologien zur Auswahl, ein *Maschennetz* und ein *Hub and Spoke*. Die Verbindungen zwischen den virtuellen Netzen werden in den Konfigurationseinstellungen festgelegt.

## <a name="mesh-network-topology"></a>Topologie des Maschennetzes

Ein Mesh-Netz ist eine Topologie, in der alle virtuellen Netze in der Netzgruppe [ miteinander verbunden sind ](concept-network-groups.md). Alle virtuellen Netze sind miteinander verbunden und können den Datenverkehr bidirektional weiterleiten. Standardmäßig ist das Netz ein regionales Netz, so dass nur virtuelle Netze in derselben Region miteinander kommunizieren können. **Global Mesh** kann aktiviert werden, um Konnektivität von virtuellen Netzwerken über alle Azure-Regionen hinweg herzustellen. Ein virtuelles Netz kann Teil von bis zu fünf verbundenen Gruppen sein. Virtuelle Netzwerkadressräume dürfen sich in einer Konfiguration nicht überschneiden, sonst können Ihre Ressourcen aufgrund von Netzwerkkonflikten nicht miteinander kommunizieren.

:::image type="content" source="./media/concept-configuration-types/mesh-topology.png" alt-text="Diagramm einer Mesh-Netz-Topologie.":::

### <a name="connected-group"></a><a name="connectedgroup"></a> Verbundene Gruppe

Wenn Sie eine Mesh-Topologie erstellen, wird ein neues Konnektivitätskonstrukt namens *Verbundene Gruppe* erstellt. Virtuelle Netze in einer verbundenen Gruppe können miteinander kommunizieren, genauso wie wenn Sie virtuelle Netze manuell miteinander verbinden würden. Wenn Sie sich die effektiven Routen für eine Netzwerkschnittstelle ansehen, werden Sie einen Next-Hop-Typ von **Verbundene Gruppe** sehen. Für virtuelle Netze, die in einer verbundenen Gruppe zusammengeschaltet sind, ist unter *Peerings* keine Peering-Konfiguration für das virtuelle Netz aufgeführt.

> [!NOTE]
> * Wenn Sie konkurrierende Subnetze in zwei oder mehr virtuellen Netzwerken haben, können die Ressourcen in diesen Subnetzen *nicht miteinander kommunizieren, selbst wenn sie Teil desselben Mesh-Netzwerks sind* .
> * Ein virtuelles Netzwerk kann Teil von bis zu **zwei** Meshkonfigurationen sein.

## <a name="hub-and-spoke-topology"></a>Hub-Spoke-Topologie

Eine Hub-and-Spoke-Topologie ist eine Netzwerktopologie, bei der Sie ein virtuelles Netzwerk als virtuelles Hub-Netzwerk ausgewählt haben. Dieses virtuelle Netz wird bidirektional mit jedem virtuellen Speichennetz in der Konfiguration abgeglichen. Diese Topologie ist nützlich, wenn Sie ein virtuelles Netzwerk isolieren möchten, aber dennoch eine Konnektivität zu gemeinsamen Ressourcen im virtuellen Hub-Netzwerk wünschen. 

In dieser Konfiguration können Sie Einstellungen wie die *direkte Konnektivität* zwischen virtuellen Speichennetzen aktivieren. Standardmäßig gilt diese Konnektivität nur für virtuelle Netze in derselben Region. Um Konnektivität über verschiedene Azure-Regionen hinweg zu ermöglichen, müssen Sie *Globales Netz* aktivieren. Sie können auch den *Gateway*-Transit aktivieren, damit virtuelle Spoke-Netzwerke das im Hub installierte VPN- oder ExpressRoute-Gateway verwenden können.

### <a name="direct-connectivity"></a>Direkte Konnektivität

Durch die Aktivierung der *Direktverbindung* wird eine [*verbundene Gruppe*](#connectedgroup) erstellt, die virtuelle Speichen-Netzwerke mit der gleichen Region enthält. Diese Verbindung wird nur für virtuelle Netze in derselben Netzgruppe hergestellt. 

Sie erstellen zum Beispiel zwei Netzwerkgruppen. Sie aktivieren die direkte Konnektivität für die Netzwerkgruppe *Production*, aber nicht für die Netzwerkgruppe *Test*. Bei dieser Einrichtung können nur die virtuellen Netzwerke der Netzwerkgruppe *Production* miteinander kommunizieren, nicht aber die der Netzwerkgruppe *Test*. 

Siehe nachstehendes Beispieldiagramm:

:::image type="content" source="./media/concept-configuration-types/hub-and-spoke.png" alt-text="Diagramm einer Hub-and-Spoke-Topologie mit zwei Netzgruppen ":::

Wenn Sie sich die effektiven Routen auf einer VM ansehen, hat die Route zwischen dem Hub und den virtuellen Spoke-Netzwerken den nächsten Hop-Typ *VNetPeering* oder *GlobalVNetPeering*. Routen zwischen virtuellen Speichen-Netzwerken werden mit dem nächsten Sprungtyp *ConnectedGroup* angezeigt. Im obigen Beispiel würde nur die Netzwerkgruppe *Production* eine *Verbundenen Gruppe* haben, da sie *Direct Connectivity* aktiviert hat.

> [!NOTE]
> Der Hub-Netzwerkadressraum wird der *Verbundenen Gruppe* hinzugefügt, wenn die *direkte Verbindung* **aktiviert** ist. Wenn also das virtuelle Netzwerk-Peering zwischen dem Hub und dem virtuellen Spoke-Netzwerk ausfällt, können sie immer noch über *Verbundenen Gruppe* kommunizieren.

#### <a name="use-cases"></a>Anwendungsfälle

Die Aktivierung der direkten Konnektivität zwischen virtuellen Speichen-Netzwerken kann hilfreich sein, wenn Sie eine NVA oder einen gemeinsamen Dienst im virtuellen Hub-Netzwerk haben möchten, aber nicht immer auf den Hub zugegriffen werden muss. Vielmehr müssen Ihre virtuellen Speichen-Netzwerke in der Netzwerkgruppe miteinander kommunizieren. Im Vergleich zu herkömmlichen Hub-and-Spoke-Netzwerken verbessert diese Topologie die Leistung, da der zusätzliche Hop durch das virtuelle Hub-Netzwerk entfällt.

### <a name="global-mesh"></a>Global Mesh

Global Mesh ist erforderlich, wenn Sie möchten, dass Ihre virtuellen Speichen-Netzwerke über Regionen hinweg miteinander kommunizieren. Diese Konnektivität ist auf virtuelle Netzwerke in derselben Netzwerkgruppe beschränkt. Um Konnektivität für virtuelle Netzwerke über Regionen hinweg zu aktivieren, müssen Sie **Mesh-Konnektivität über Regionen hinweg aktivieren** für die Netzwerkgruppe. Verbindungen, die zwischen virtuellen Speichen-Netzwerken erstellt werden, befinden sich in einer [*Verbundenen Gruppe*](#connectedgroup). 

### <a name="use-hub-as-a-gateway"></a>Hub als Gateway verwenden

Eine weitere Option, die Sie in einer Hub-and-Spoke-Konfiguration aktivieren können, ist die Verwendung des Hubs als Gateway. Mit dieser Einstellung können alle virtuellen Netzwerke in der Netzwerkgruppe das VPN- oder ExpressRoute-Gateway im virtuellen Netzwerk des Hubs zur Weiterleitung des Datenverkehrs verwenden. Siehe [Gateways und ortsgebundene Konnektivität](/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity).

Wenn Sie eine Hub- und Spoke-Topologie über das Azure-Portal bereitstellen, ist die Option **Hub als Gateway verwenden** standardmäßig für die virtuellen Spoke-Netzwerke in der Netzwerkgruppe aktiviert. Azure Virtual Network Manager wird versuchen, eine virtuelle Netzwerk-Peering-Verbindung zwischen dem Hub und dem virtuellen Speichen-Netzwerk in der Ressourcengruppe zu erstellen. Wenn das Gateway im virtuellen Netzwerk des Hubs nicht vorhanden ist, schlägt die Erstellung des Peering vom virtuellen Spoke-Netzwerk zum Hub fehl. Die Peering-Verbindung vom Hub zur Speiche wird auch ohne bestehende Verbindung hergestellt. 

> [!NOTE]
> Wenn Sie *Direkte Konnektivität* für die Speichen-Netzwerkgruppe aktivieren, wird der Hub ebenfalls in die verbundene Gruppe aufgenommen. Selbst wenn die Peering-Verbindung von der Speiche zum Hub nicht hergestellt wird, können die virtuellen Speichen-Netzwerke mit dem virtuellen Hub-Netzwerk über die verbundene Gruppe kommunizieren.
>

## <a name="next-steps"></a>Nächste Schritte

- Erstellen Sie eine [Azure Virtual Network Manager](create-virtual-network-manager-portal.md)-Instanz.
- Erfahren Sie mehr über [Konfigurationsbereitstellungen](concept-deployments.md) in Azure Virtual Network Manager.
- Erfahren Sie, wie Sie den Netzwerkverkehr mit einer [SecurityAdmin-Konfiguration](how-to-block-network-traffic-portal.md) blockieren können.
