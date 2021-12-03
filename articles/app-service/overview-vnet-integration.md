---
title: Integrieren Ihrer App in ein Azure Virtual Network
description: Integrieren Sie Ihre App in Azure App Service in virtuelle Azure-Netzwerke.
author: madsd
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: madsd
ms.openlocfilehash: 2fab79439dd28a889d117bdce5bc4916f3d5b2f8
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892929"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrieren Ihrer App in ein Azure Virtual Network

In diesem Artikel wird die Azure App Service-Funktion für die VNet-Integration beschrieben, und Sie erfahren, wie Sie die Funktion mit Apps in [App Service](./overview.md) einrichten. Mit [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md) (VNets) können Sie viele Ihrer Azure-Ressourcen in einem Netzwerk platzieren, das nicht über das Internet geroutet werden kann. Die App Service-Funktion für die VNet-Integration ermöglicht Ihren Apps den Zugriff auf Ressourcen in einem virtuellen Netzwerk oder über ein virtuelles Netzwerk. Die VNet-Integration ermöglicht keinen privaten Zugriff auf Ihre Apps.

Es gibt zwei Varianten der Nutzung von App Service:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Erfahren Sie, wie Sie [die Integration virtueller Netzwerke aktivieren](./configure-vnet-integration-enable.md).

## <a name="regional-virtual-network-integration"></a>Regionale Integration des virtuellen Netzwerks

Die regionale Integration virtueller Netzwerke unterstützt das Herstellen einer Verbindung mit einem virtuellen Netzwerk in derselben Region und erfordert kein Gateway. Die Verwendung der regionalen Integration virtueller Netzwerke ermöglicht der App Zugriff auf Folgendes:

* Ressourcen im virtuellen Netzwerk, in das Sie integriert sind
* Ressourcen in virtuellen Netzwerken, die per Peering mit dem virtuellen Netzwerk verbunden sind, in das Ihre App integriert ist, einschließlich Verbindungen mit globalem Peering
* Ressourcen über Azure ExpressRoute-Verbindungen.
* Durch Dienstendpunkte geschützte Dienste
* Dienste, von denen private Endpunkte unterstützt werden

Wenn Sie die regionale Integration virtueller Netzwerke verwenden, können Sie die folgenden Azure-Netzwerkfunktionen nutzen:

* **Netzwerksicherheitsgruppen (NSGs)** : Sie können ausgehenden Datenverkehr mit einer NSG blockieren, die in Ihrem Integrationssubnetz platziert ist. Die Regeln für eingehenden Datenverkehr gelten nicht, da Sie die Integration virtueller Netzwerke nicht verwenden können, um eingehenden Zugriff auf Ihre App bereitzustellen.
* **Routingtabellen (UDRs)** : Sie können eine Routingtabelle im Integrationssubnetz platzieren, um ausgehenden Datenverkehr an beliebige gewünschte Ziele zu senden.

### <a name="how-regional-virtual-network-integration-works"></a>Funktionsweise der regionalen Integration virtueller Netzwerke

Apps in App Service werden auf Workerrollen gehostet. Die regionale Integration virtueller Netzwerke beruht auf der Einbindung virtueller Schnittstellen in die Workerrollen mit Adressen im delegierten Subnetz. Da sich die Von-Adresse in Ihrem virtuellen Netzwerk befindet, kann sie ähnlich wie eine VM in Ihrem virtuellen Netzwerk auf die meisten Ressourcen zugreifen, die in Ihrem oder über Ihr virtuelles Netzwerk verfügbar sind. Die Netzwerkimplementierung unterscheidet sich von der Ausführung eines virtuellen Computers in Ihrem virtuellen Netzwerk. Aus diesem Grund sind einige Netzwerkfunktionen für dieses Feature noch nicht verfügbar.

:::image type="content" source="./media/overview-vnet-integration/vnetint-how-regional-works.png" alt-text="Diagramm: Funktionsweise der regionalen Integration virtueller Netzwerke":::

Wenn die regionale Integration virtueller Netzwerke aktiviert ist, sendet Ihre App ausgehende Aufrufe über das virtuelle Netzwerk. Ihre App verwendet nach wie vor die ausgehenden Adressen, die im Portal für die App-Eigenschaften aufgeführt sind. Wenn Ihr ausgehender Aufruf jedoch an einen virtuellen Computer oder einen privaten Endpunkt im virtuellen Integrationsnetzwerk oder einem virtuellen Netzwerk mit Peering gerichtet ist, ist die ausgehende Adresse eine Adresse aus dem Integrationssubnetz. Die private IP-Adresse, die einer Instanz zugewiesen ist, wird über die Umgebungsvariable „WEBSITE_PRIVATE_IP“ verfügbar gemacht.

Wenn das gesamte Datenverkehrsrouting aktiviert ist, wird der gesamte ausgehende Datenverkehr an Ihr virtuelles Netzwerk gesendet. Ist das gesamte Datenverkehrsrouting nicht aktiviert, werden nur privater Datenverkehr (RFC1918) und Dienstendpunkte, die im Integrationssubnetz konfiguriert sind, an das virtuelle Netzwerk gesendet, und ausgehender Datenverkehr für das Internet wird über die üblichen Kanäle abgewickelt.

Die Funktion unterstützt nur eine virtuelle Schnittstelle pro Worker. Eine virtuelle Schnittstelle pro Worker bedeutet eine regionale Integration virtueller Netzwerke pro App Service-Plan. Alle Apps im gleichen App Service-Plan können die gleiche Integration virtueller Netzwerke verwenden. Wenn Sie eine App zum Herstellen einer Verbindung mit einem anderen virtuellen Netzwerk benötigen, müssen Sie einen weiteren App Service-Plan erstellen. Die verwendete virtuelle Schnittstelle ist keine Ressource, auf die Kunden direkten Zugriff haben.

Aufgrund der Funktionsweise dieser Technologie wird der Datenverkehr in Verbindung mit der Integration virtueller Netzwerke nicht in Azure Network Watcher- oder NSG-Flussprotokollen (Netzwerksicherheitsgruppe) aufgeführt.

### <a name="subnet-requirements"></a>Subnetzanforderungen

Die Integration virtueller Netzwerke hängt von der Verwendung eines dedizierten Subnetzes ab. Wenn Sie ein Subnetz erstellen, verliert das Azure-Subnetz von Anfang an fünf IP-Adressen. Für jede Instanz des Plans wird eine Adresse aus dem Integrationssubnetz verwendet. Wenn Sie Ihre App auf vier Instanzen skalieren, werden vier Adressen verwendet.

Wenn Sie die Größe hoch- oder herunterskaliert haben, wird der erforderliche Adressraum für einen kurzen Zeitraum verdoppelt. Diese Änderung wirkt sich auf die tatsächlichen, verfügbaren unterstützten Instanzen für eine bestimmte Subnetzgröße aus. Die folgende Tabelle zeigt sowohl die maximal verfügbaren Adressen pro CIDR-Block als auch die Auswirkungen auf die horizontale Skalierung:

| CIDR-Blockgröße | Maximal verfügbare Adressen | Maximale horizontale Skalierung (Instanzen)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>Geht davon aus, dass irgendwann ein Hoch- oder Herunterskalieren der Größe oder SKU erforderlich ist.

Da die Subnetzgröße nach der Zuweisung nicht mehr geändert werden kann, verwenden Sie ein Subnetz, das für die Aufnahme jedweder Skalierung Ihrer App groß genug ist. Um jegliche Probleme mit der Subnetzkapazität zu vermeiden, verwenden Sie `/26` mit 64 Adressen.

Wenn die Apps in Ihrem Plan ein virtuelles Netzwerk erreichen sollen, das bereits mit Apps in einem anderen Plan verbunden ist, müssen Sie ein anderes Subnetz als das von der bereits vorhandenen VNet-Integration verwendete auswählen.

### <a name="routes"></a>Routen

Es gibt zwei Arten von Routing, die Sie beim Konfigurieren der regionalen Integration virtueller Netzwerke berücksichtigen sollten. Mit Anwendungsrouting wird definiert, welcher Datenverkehr von Ihrer Anwendung an das virtuelle Netzwerk weitergeleitet wird. Mit Netzwerkrouting können Sie steuern, wie Datenverkehr von Ihrem virtuellen Netzwerk und nach draußen weitergeleitet wird.

#### <a name="application-routing"></a>Anwendungsrouting

Wenn Sie das Anwendungsrouting konfigurieren, können Sie entweder den gesamten oder nur privaten Datenverkehr (auch [RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3)-Datenverkehr genannt) an Ihr virtuelles Netzwerk weiterleiten. Dieses Verhalten konfigurieren Sie über die Einstellung **Route All** (Gesamten Datenverkehr weiterleiten). Ist **Route All** (Gesamten Datenverkehr weiterleiten) deaktiviert, wird von Ihrer App nur privater Datenverkehr an Ihr virtuelles Netzwerk weitergeleitet. Wenn Sie den gesamten ausgehenden Datenverkehr an Ihr virtuelles Netzwerk weiterleiten möchten, muss **Route All** (Gesamten Datenverkehr weiterleiten) aktiviert sein.

> [!NOTE]
> * Ist **Route All** (Gesamten Datenverkehr weiterleiten) aktiviert, unterliegt der gesamte Datenverkehr den NSGs und UDRs, die auf Ihr Integrationssubnetz angewendet werden. Wenn das gesamte Datenverkehrsrouting aktiviert ist, wird ausgehender Datenverkehr weiterhin von den Adressen gesendet, die in Ihren App-Eigenschaften aufgeführt sind – es sei denn, Sie stellen Routen bereit, durch die der Datenverkehr an einen anderen Ort weitergeleitet wird.
> * Windows-Container unterstützen weder das Routing von Key Vault-Referenzen von App Service noch das Pullen benutzerdefinierter Containerimages über die Integration virtueller Netzwerke.
> * Für die regionale Integration des virtuellen Netzwerks kann nicht Port 25 verwendet werden.

Hier erfahren Sie, wie Sie [das Anwendungsrouting konfigurieren](./configure-vnet-integration-routing.md).

Die Konfigurationseinstellung **Route All** (Gesamten Datenverkehr weiterleiten) ist die empfohlene Methode zum Aktivieren des gesamten Datenverkehrsrouting. Mithilfe der Konfigurationseinstellung können Sie das Verhalten mit einer [integrierten Richtlinie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33228571-70a4-4fa1-8ca1-26d0aba8d6ef) überwachen. Die vorhandene App-Einstellung „WEBSITE_VNET_ROUTE_ALL“ kann weiterhin verwendet werden, und Sie können das gesamte Datenverkehrsrouting mit beiden Einstellungen aktivieren.

#### <a name="network-routing"></a>Netzwerkrouting

Sie können mithilfe von Routingtabellen ausgehenden Datenverkehr von Ihrer App an beliebige Ziele weiterleiten. Routingtabellen wirken sich auf Ihren Zieldatenverkehr aus. Wenn **Route All** (Gesamten Datenverkehr weiterleiten) im [Anwendungsrouting](#application-routing) deaktiviert ist, ist nur privater Datenverkehr (RFC1918) von Ihren Routingtabellen betroffen. Gängige Ziele können Firewallgeräte oder Gateways beinhalten. Routen, die für Ihr Integrationssubnetz festgelegt werden, wirken sich nicht auf Antworten auf eingehende App-Anforderungen aus.

Wenn Sie den gesamten ausgehenden Datenverkehr lokal routen möchten, können Sie eine Routingtabelle verwenden, um den gesamten ausgehenden Datenverkehr an das Azure ExpressRoute-Gateway zu senden. Wenn Sie Datenverkehr an ein Gateway weiterleiten, legen Sie Routen im externen Netzwerk fest, über die Antworten zurückgesendet werden.

BGP-Routen (Border Gateway Protocol) wirken sich ebenfalls auf den App-Datenverkehr aus. Wenn Sie über BGP-Routen von einem ExpressRoute-Gateway verfügen, ist der ausgehende Datenverkehr Ihrer App betroffen. Ähnlich wie benutzerdefinierte Routen wirken sich BGP-Routen gemäß Ihrer Routingbereichseinstellung auf den Datenverkehr aus.

### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

Eine App mit regionaler VNet-Integration kann eine [Netzwerksicherheitsgruppe](../virtual-network/network-security-groups-overview.md) verwenden, um ausgehenden Datenverkehr an Ressourcen in Ihrem virtuellen Netzwerk oder im Internet zu blockieren. Wenn Sie Datenverkehr an öffentliche Adressen blockieren möchten, aktivieren Sie [Route All](#application-routing) (Gesamten Datenverkehr weiterleiten) für das virtuelle Netzwerk. Ist **Route All** (Gesamten Datenverkehr weiterleiten) nicht aktiviert, werden NSGs nur auf RFC1918-Datenverkehr angewendet.

Eine NSG, die auf Ihr Integrationssubnetz angewendet wird, ist unabhängig von den Routingtabellen wirksam, die ggf. auf Ihr Integrationssubnetz angewendet werden.

Die Regeln für eingehenden Datenverkehr in einer NSG gelten nicht für Ihre App, weil sich die VNet-Integration nur auf ausgehenden Datenverkehr von Ihrer App auswirkt. Um den eingehenden Datenverkehr für Ihre App zu steuern, verwenden Sie das Feature für Zugriffsbeschränkungen.

### <a name="service-endpoints"></a>Dienstendpunkte

Mit der regionalen Integration virtueller Netzwerke können Sie Azure-Dienste erreichen, die mit Dienstendpunkten geschützt sind. Führen Sie die folgenden Schritte aus, um auf einen durch einen Dienstendpunkt gesicherten Dienst zuzugreifen:

1. Konfigurieren Sie die regionale Integration virtueller Netzwerke in Ihrer Web-App, um eine Verbindung mit einem bestimmten Subnetz für die Integration herzustellen.
1. Wechseln Sie zum Zieldienst, und konfigurieren Sie Dienstendpunkte für das Integrationssubnetz.

### <a name="private-endpoints"></a>Private Endpunkte

Wenn Sie Aufrufe an [private Endpunkte](./networking/private-endpoint.md) senden möchten, stellen Sie sicher, dass Ihre DNS-Suchen zu dem privaten Endpunkt aufgelöst werden. Sie können dieses Verhalten auf eine der folgenden Arten erzwingen:

* Integrieren mit private Azure DNS-Zonen. Falls Ihr virtuelles Netzwerk nicht über einen benutzerdefinierten DNS-Server verfügt, erfolgt die Integration automatisch, wenn die Zonen mit dem virtuellen Netzwerk verknüpft werden.
* Verwalten des privaten Endpunkts im DNS-Server, der von Ihrer App verwendet wird. Zum Verwalten der Konfiguration müssen Sie die IP-Adresse des privaten Endpunkts kennen. Verweisen Sie dann mithilfe eines A-Eintrags den Endpunkt, den Sie erreichen möchten, auf diese Adresse.
* Konfigurieren Ihres eigenen DNS-Servers zur Weiterleitung an private Azure DNS-Zonen.

### <a name="azure-dns-private-zones"></a>Azure DNS Private Zones

Nachdem Ihre App in Ihr virtuelles Netzwerk integriert wurde, verwendet sie denselben DNS-Server, mit dem auch Ihr virtuelles Netzwerk konfiguriert ist. Ist kein benutzerdefiniertes DNS angegeben, werden Azure-Standard-DNS und alle privaten Zonen verwendet, die mit dem virtuellen Netzwerk verknüpft sind.

### <a name="limitations"></a>Einschränkungen

Es gibt einige Einschränkungen bei der Verwendung der regionalen Integration virtueller Netzwerke:

* Das Feature ist in allen App Service-Skalierungseinheiten in den Tarifen „Premium v2“ und „Premium v3“ verfügbar. Es ist außerdem im Tarif „Standard“ verfügbar, jedoch nur in neueren App Service-Skalierungseinheiten. Wenn Sie eine ältere Skalierungseinheit nutzen, können Sie das Feature nur in einem App Service-Plan vom Typ „Premium v2“ verwenden. Wenn Sie sicherstellen möchten, dass Sie das Feature in einem App Service-Plan vom Typ „Standard“ verwenden können, erstellen Sie Ihre App in einem App Service-Plan vom Typ „Premium V3“. Diese Pläne werden nur für auf unseren neuesten Skalierungseinheiten unterstützt. Sie können nach dem Erstellen des Plans nach Bedarf herunterskalieren.
* Die Funktion kann nicht für Apps im Plan „App Service (isoliert)“ in einer App Service-Umgebung verwendet werden.
* Sie können keine Ressourcen über Peeringverbindungen mit klassischen virtuellen Netzwerken erreichen.
* Für das Feature ist ein nicht genutztes Subnetz, das ein IPv4-`/28`-Block oder größer ist, in einem virtuellen Azure Resource Manager-Netzwerk erforderlich.
* Die App und das virtuelle Netzwerk müssen sich in derselben Region befinden.
* Im virtuellen Integrationsnetzwerk dürfen keine IPv6-Adressräume definiert sein.
* Das Integrationssubnetz kann nur von einem App Service-Plan verwendet werden.
* Ein virtuelles Netzwerk mit einer integrierten App kann nicht gelöscht werden. Entfernen Sie die Integration, bevor Sie das virtuelle Netzwerk löschen.
* Sie können nur eine regionale Integration virtueller Netzwerke pro App Service-Plan verwenden. Mehrere Apps im gleichen App Service-Plan können das gleiche virtuelle Netzwerk verwenden.
* Sie können das Abonnement einer App oder eines Plans nicht ändern, solange eine App mit regionaler VNet-Integration vorhanden ist.

## <a name="gateway-required-virtual-network-integration"></a>Integration des virtuellen Netzwerks mit erforderlichem Gateway

Die von einem Gateway abhängige Integration virtueller Netzwerke unterstützt das Herstellen einer Verbindung mit einem virtuellen Netzwerk in einer anderen Region oder mit einem klassischen virtuellen Netzwerk. Integration des virtuellen Netzwerks mit erforderlichem Gateway:

* Eine App kann nur jeweils mit einem virtuellen Netzwerk eine Verbindung herstellen.
* Bis zu fünf virtuelle Netzwerke können in einen App Service-Plan integriert werden.
* Ermöglicht die Verwendung desselben virtuellen Netzwerks durch mehrere Apps in einem App Service-Plan, ohne dass sich dies auf die für einen App Service-Plan zulässige Gesamtanzahl von VNets auswirkt. Wenn Sie sechs Apps verwenden, die dasselbe virtuelle Netzwerk im selben App Service-Plan nutzen, zählt dies als ein verwendetes virtuelles Netzwerk.
* Die SLA für das Gateway kann sich auf die allgemeine [SLA](https://azure.microsoft.com/support/legal/sla/) auswirken.
* Ermöglicht Ihren Apps die Verwendung des DNS, mit dem das virtuelle Netzwerk konfiguriert ist.
* Erfordert ein routenbasiertes Virtual Network-Gateway, das mit einem Point-to-Site-VPN für SSTP konfiguriert ist, bevor es mit einer App verbunden werden kann.

Sie können die Integration des virtuellen Netzwerks mit erforderlichem Gateway in den folgenden Fällen nicht verwenden:

* Mit einem virtuellen Netzwerk, das mit ExpressRoute verbunden ist
* Aus einer Linux-App.
* Aus einem [Windows-Container](./quickstart-custom-container.md).
* Für den Zugriff auf durch Dienstendpunkte geschützte Ressourcen
* Mit einem Koexistenzgateway, das sowohl ExpressRoute als auch Point-to-Site- oder Site-to-Site-VPNs unterstützt.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Einrichten eines Gateways in Ihrem virtuellen Azure-Netzwerk

So erstellen Sie ein Gateway

1. [Erstellen Sie das VPN-Gateway und das Subnetz](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw). Wählen Sie einen routenbasierten VPN-Typ aus.

1. [Legen Sie die Point-to-Site-Adressen fest](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool). Wenn sich das Gateway nicht in der Basic-SKU befindet, muss IKEV2 in der Point-to-Site-Konfiguration deaktiviert und SSTP ausgewählt werden. Der Point-to Site-Adressraum muss in den RFC 1918-Adressblöcken 10.0.0.0/8, 172.16.0.0/12 und 192.168.0.0/16 liegen.

Wenn Sie das Gateway für die Verwendung mit einer von einem Gateway abhängigen VNet-Integration erstellen, müssen Sie kein Zertifikat hochladen. Das Erstellen des Gateways kann 30 Minuten dauern. Sie können Ihre App erst in Ihr virtuelles Netzwerk integrieren, nachdem das Gateway erstellt wurde.

### <a name="how-gateway-required-virtual-network-integration-works"></a>Funktionsweise der Integration des virtuellen Netzwerks mit erforderlichem Gateway

Die von einem Gateway abhängige VNet-Integration basiert auf der Point-to-Site-VPN-Technologie. Point-to-Site-VPNs beschränken den Netzwerkzugriff auf den virtuellen Computer, auf dem die App gehostet wird. Apps sind darauf beschränkt, Datenverkehr an das Internet nur über Hybridverbindungen oder die Integration virtueller Netzwerke zu senden. Wenn Ihre App über das Portal so konfiguriert wurde, dass sie die von einem Gateway abhängige VNet-Integration verwendet, wird eine komplexe Aushandlung in Ihrem Namen verwaltet, um Zertifikate auf der Gateway- und der Anwendungsseite zu erstellen und zuzuweisen. Das Ergebnis ist, dass die Worker, die Ihre Apps hosten, in der Lage sind, eine direkte Verbindung mit dem VNet-Gateway im ausgewählten virtuellen Netzwerk herzustellen.

:::image type="content" source="./media/overview-vnet-integration/vnetint-how-gateway-works.png" alt-text="Diagramm: Funktionsweise der Integration des virtuellen Netzwerks mit erforderlichem Gateway":::

### <a name="access-on-premises-resources"></a>Zugriff auf lokale Ressourcen

Apps können durch Integration mit virtuellen Netzwerken, die Site-to-Site-Verbindungen aufweisen, auf lokale Ressourcen zugreifen. Wenn Sie die von einem Gateway abhängige VNet-Integration verwenden, aktualisieren Sie Ihre lokalen VPN-Gatewayrouten mit Ihren Point-to-Site-Adressblöcken. Verwenden Sie beim ersten Einrichten des Site-to-Site-VPN die Skripts für die Konfiguration. Die Routen sollten damit richtig eingerichtet werden. Wenn Sie die Point-to-Site-Adressen nach dem Erstellen des Site-to-Site-VPN hinzufügen, müssen Sie die Routen manuell aktualisieren. Die Details zur Vorgehensweise variieren je nach Gateway und sind hier nicht beschrieben. Das Border Gateway Protocol (BGP) kann nicht für eine Site-to-Site-VPN-Verbindung konfiguriert werden.

Für die regionale Integration virtueller Netzwerke ist keine zusätzliche Konfiguration erforderlich, um über Ihr virtuelles Netzwerk lokale Ressourcen zu erreichen. Sie müssen Ihr virtuelles Netzwerk lediglich über ExpressRoute oder ein Site-to-Site-VPN mit lokalen Ressourcen verbinden.

> [!NOTE]
> Bei der von einem Gateway abhängigen VNet-Integration wird eine App nicht in ein virtuelles Netzwerk integriert, das über ein ExpressRoute-Gateway verfügt. Auch wenn das ExpressRoute-Gateway im [Koexistenzmodus](../expressroute/expressroute-howto-coexist-resource-manager.md) konfiguriert ist, funktioniert die VNet-Integration nicht. Wenn Sie über eine ExpressRoute-Verbindung auf Ressourcen zugreifen müssen, verwenden Sie die Funktion für die regionale Integration virtueller Netzwerke oder eine in Ihrem virtuellen Netzwerk ausgeführte [App Service-Umgebung](./environment/intro.md).

### <a name="peering"></a>Peering

Wenn Sie Peering mit regionaler Integration virtueller Netzwerke verwenden, sind keine weiteren Konfigurationsschritte erforderlich.

Bei der von einem Gateway abhängigen VNet-Integration mit Peering müssen Sie einige weitere Elemente konfigurieren. So konfigurieren Sie Peering für Ihre App

1. Fügen Sie eine Peeringverbindung mit dem virtuellen Netzwerk hinzu, mit dem Ihre App eine Verbindung herstellt. Aktivieren Sie beim Hinzufügen der Peeringverbindung **Zugriff auf virtuelles Netzwerk zulassen** sowie **Weitergeleiteten Datenverkehr zulassen** und **Gatewaytransit zulassen**.
1. Fügen Sie in dem virtuellen Netzwerk, das mit dem virtuellen Netzwerk, mit dem Sie verbunden sind, mittels Peering verknüpft ist, eine Peeringverbindung hinzu. Aktivieren Sie beim Hinzufügen der Peeringverbindung im virtuellen Zielnetzwerk **Zugriff auf virtuelles Netzwerk zulassen**, und aktivieren Sie **Weitergeleiteten Datenverkehr zulassen** und **Allow remote gateways** (Remotegateways zulassen).
1. Wechseln Sie im Portal zu **App Service-Plan** > **Netzwerk** > **VNET-Integration**. Wählen Sie das virtuelle Netzwerk aus, mit dem Ihre App eine Verbindung herstellt. Fügen Sie den Adressbereich des virtuellen Netzwerks, das mit dem virtuellen Netzwerk, mit dem Ihre App verbunden ist, mittels Peering verknüpft ist, im Abschnitt „Routing“ hinzu.

## <a name="manage-virtual-network-integration"></a>Verwalten der Integration virtueller Netzwerke

Das Verbinden und Trennen der Verbindung mit einem virtuellen Netzwerk erfolgt auf App-Ebene. Vorgänge, die sich auf die VNet-Integration über mehrere Apps auswirken können, werden auf Ebene des App Service-Plans ausgeführt. Sie können im Portal unter „App“ > **Netzwerk** > **VNet-Integration** Details zu Ihrem virtuellen Netzwerk abrufen. Ähnliche Informationen finden Sie auf der App Service-Planebene im Portal unter **App Service-Plan** > **Netzwerk** > **VNET-Integration**.

Der einzige Vorgang, den Sie in der App-Ansicht Ihrer VNet-Integrationsinstanz durchführen können, ist das Trennen Ihrer App von dem virtuellen Netzwerk, mit dem derzeit eine Verbindung besteht. Um Ihre App von einem virtuellen Netzwerk zu trennen, wählen Sie **Verbindung trennen** aus. Wenn Sie die Verbindung mit einem virtuellen Netzwerk trennen, wird Ihre App neu gestartet. Das Trennen der Verbindung führt nicht zu Änderungen in Ihrem virtuellen Netzwerk. Das Subnetz oder Gateway wird nicht entfernt. Wenn Sie Ihr virtuelles Netzwerk löschen möchten, trennen Sie zuerst Ihre App vom virtuellen Netzwerk und löschen die darin enthaltenen Ressourcen (z. B. Gateways).

Auf der Benutzeroberfläche der VNet-Integration des App Service-Plans werden alle Integrationen virtueller Netzwerke angezeigt, die von den Apps in Ihrem App Service-Plan verwendet werden. Um Details zu jedem virtuellen Netzwerk anzuzeigen, wählen Sie das virtuelle Netzwerk aus, an dem Sie interessiert sind. Es gibt zwei Aktionen, die Sie hier für die VNet-Integration mit erforderlichem Gateway durchführen können:

* **Synchronisieren des Netzwerks:** Der Vorgang zum Synchronisieren des Netzwerks wird nur für die von einem Gateway abhängige VNet-Integration verwendet. Durch die Netzwerksynchronisierung wird sichergestellt, dass Ihre Zertifikate und Netzwerkinformationen synchronisiert sind. Wenn Sie das DNS Ihres virtuellen Netzwerks hinzufügen oder ändern, führen Sie eine Netzwerksynchronisierung durch. Mit diesem Vorgang werden alle Apps, die dieses virtuelle Netzwerk verwenden, neu gestartet. Dieser Vorgang funktioniert nicht, wenn Sie eine App und ein virtuelles Netzwerk verwenden, die zu verschiedenen Abonnements gehören.
* **Routen hinzufügen**: Durch das Hinzufügen von Routen wird ausgehender Datenverkehr in Ihr virtuelles Netzwerk geleitet.

Die private IP-Adresse, die der Instanz zugewiesen ist, wird über die Umgebungsvariable WEBSITE_PRIVATE_IP verfügbar gemacht. Auf der Benutzeroberfläche der Kudu-Konsole wird auch die Liste der Umgebungsvariablen angezeigt, die für die Web-App verfügbar sind. Diese IP-Adresse wird aus dem Adressbereich des integrierten Subnetzes zugewiesen. Bei der regionalen Integration virtueller Netzwerke ist der Wert von WEBSITE_PRIVATE_IP eine IP-Adresse aus dem Adressbereich des delegierten Subnetzes. Bei der von einem Gateway abhängigen VNet-Integration ist der Wert eine IP-Adresse aus dem Adressbereich des Point-to-Site-Adresspools, der für das VNet-Gateway konfiguriert ist. Diese IP-Adresse wird von der Web-App zum Herstellen einer Verbindung mit den Ressourcen über das virtuelle Azure-Netzwerk verwendet.

> [!NOTE]
> Der Wert von WEBSITE_PRIVATE_IP ist veränderlich. Es handelt sich aber immer um eine IP-Adresse im Adressbereich des Integrationssubnetzes oder im Point-to-Site-Adressbereich. Sie müssen daher den Zugriff vom gesamten Adressbereich aus zulassen.
>

### <a name="gateway-required-virtual-network-integration-routing"></a>Routing bei der Integration des virtuellen Netzwerks mit erforderlichem Gateway

Die in Ihrem virtuellen Netzwerk definierten Routen werden zum Leiten des Datenverkehrs aus der App in Ihr virtuelles Netzwerk verwendet. Wenn Sie weiteren ausgehenden Datenverkehr in das virtuelle Netzwerk senden möchten, fügen Sie diese Adressblöcke hier hinzu. Diese Funktion kann nur mit der von einem Gateway abhängigen VNet-Integration verwendet werden. Routingtabellen wirken sich nicht auf den App-Datenverkehr aus, wenn die von einem Gateway abhängige VNet-Integration so verwendet wird wie die regionale VNet-Integration.

### <a name="gateway-required-virtual-network-integration-certificates"></a>Zertifikate für die Integration des virtuellen Netzwerks mit erforderlichem Gateway

Wenn die von einem Gateway abhängige VNet-Integration aktiviert ist, müssen Zertifikate ausgetauscht werden, um die Sicherheit der Verbindung zu gewährleisten. Zusammen mit den Zertifikaten werden die DNS-Konfiguration, Routen und anderen Elemente, mit denen das Netzwerk beschrieben wird, ausgetauscht.

Wenn Zertifikate oder Netzwerkinformationen geändert werden, wählen Sie **Netzwerk synchronisieren** aus. Wenn Sie **Netzwerk synchronisieren** auswählen, bewirkt dies einen kurzen Ausfall der Verbindung zwischen der App und Ihrem virtuellen Netzwerk. Die App wird nicht neu gestartet, aber der Konnektivitätsverlust kann dazu führen, dass Ihre Website nicht richtig funktioniert.

## <a name="pricing-details"></a>Preisübersicht

Bei der Funktion für die regionale Integration virtueller Netzwerke fallen neben den Gebühren für den App Service-Plantarif keine zusätzlichen Gebühren an.

Bei der Verwendung der Funktion für die von einem Gateway abhängige VNet-Integration fallen drei Gebühren an:

* **Gebühren laut App Service-Plantarif:** Ihre Apps müssen in einem App Service-Plan mit dem Tarif „Standard“, „Premium“, „Premium v2“ oder „Premium v3“ enthalten sein. Weitere Informationen zu diesen Kosten finden Sie unter [App Service-Preise](https://azure.microsoft.com/pricing/details/app-service/).
* **Datenübertragungskosten**: Es gibt eine Gebühr für ausgehende Daten, auch wenn sich das virtuelle Netzwerk im selben Rechenzentrum befindet. Diese Gebühren werden unter [Bandbreite – Preise](https://azure.microsoft.com/pricing/details/data-transfers/) beschrieben.
* **Kosten für VPN Gateway**: Es gibt Kosten für das virtuelle Netzwerkgateway, das für das Point-to-Site-VPN erforderlich ist. Weitere Informationen finden Sie unter [VPN-Gateway: Preise](https://azure.microsoft.com/pricing/details/vpn-gateway/).

## <a name="troubleshooting"></a>Problembehandlung

> [!NOTE]
> Die Integration virtueller Netzwerke wird für Docker Compose-Szenarien in App Service nicht unterstützt.
> Zugriffsbeschränkungen werden ignoriert, wenn ein privater Endpunkt vorhanden ist.

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]
