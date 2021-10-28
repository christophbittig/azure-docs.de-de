---
title: Azure API Management mit einem virtuellen Azure-Netzwerk
description: Erfahren Sie mehr über Szenarien und Anforderungen für die Verbindung Ihrer API Management-Instanz mit einem virtuellen Azure-Netzwerk.
author: dlepow
ms.service: api-management
ms.topic: conceptual
ms.date: 08/19/2021
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 364487d697aee69215e9ca9f080e4aa6a1a83468
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130253362"
---
# <a name="use-a-virtual-network-with-azure-api-management"></a>Verwenden Sie ein virtuelles Netzwerk mit Azure API Management

Mit Azure Virtual Networks (VNETs) können Sie Ihre Azure-Ressourcen in einem nicht über das Internet routbaren Netzwerk platzieren, auf das Sie den Zugriff kontrollieren. Anschließend können VNets mithilfe verschiedener VPN-Technologien mit Ihren lokalen Netzwerken verbunden werden. Weitere Informationen zu Azure-VNets finden Sie unter [Was ist Azure Virtual Network?](../virtual-network/virtual-networks-overview.md).

Dieser Artikel erläutert die VNET-Konnektivitätsoptionen, Anforderungen und Überlegungen für Ihre API Management-Instanz. Sie können das Azure-Portal, Azure CLI, Azure Resource Manager-Vorlagen oder andere Tools für die Bereitstellung verwenden. Sie kontrollieren den ein- und ausgehenden Datenverkehr in das Subnetz, in dem API Management bereitgestellt wird, indem Sie [Netzwerksicherheitsgruppen][NetworkSecurityGroups] verwenden.

Detaillierte Schritte zur Bereitstellung und Netzwerkkonfiguration finden Sie unter:

* [Verbinden Sie sich mit einem externen virtuellen Netzwerk mit Azure API Management](./api-management-using-with-vnet.md).
* [Verbinden Sie sich mit einem internen virtuellen Netzwerk mit Azure API Management](./api-management-using-with-internal-vnet.md).

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="access-options"></a>Zugriffsoptionen

Standardmäßig muss eine API Management-Instanz über das Internet zugänglich sein. Mithilfe eines virtuellen Netzwerks können Sie das Entwicklerportal, den API-Gateway und andere API Management-Endpunkte so konfigurieren, dass sie entweder über das Internet (externer Modus) oder nur innerhalb des VNET (interner Modus) zugänglich sind. 

* **Extern** - Die Endpunkte der API Management sind vom öffentlichen Internet aus über einen externen Load Balancer zugänglich. Das Gateway kann auf Ressourcen innerhalb des VNet zugreifen.

    :::image type="content" source="media/virtual-network-concepts/api-management-vnet-external.png" alt-text="Verbindung mit externem VNET":::

    Verwenden Sie die API Management im externen Modus, um auf Backend-Dienste zuzugreifen, die im virtuellen Netzwerk bereitgestellt werden.

* **Intern** - Die API Management Endpunkte sind nur von innerhalb des VNET über einen internen Load Balancer zugänglich. Das Gateway kann auf Ressourcen innerhalb des VNet zugreifen.

    :::image type="content" source="media/virtual-network-concepts/api-management-vnet-internal.png" alt-text="Verbindung zum internen VNET":::

    Verwenden Sie API Management im internen Modus für folgende Aufgaben:

    * Machen Sie APIs, die in Ihrem privaten Rechenzentrum gehostet werden, für Dritte sicher zugänglich, indem Sie Azure VPN-Verbindungen oder Azure ExpressRoute verwenden.
    * Das Verfügbarmachen von cloudbasierten und lokalen APIs über ein gemeinsames Gateway ermöglicht Hybrid Cloud-Szenarien.
    * Verwalten von APIs, die in mehreren geografischen Standorten gehostet werden, über einen einzelnen Gatewayendpunkt.


## <a name="network-resource-requirements"></a>Anforderungen an die Netzwerkressourcen

Im Folgenden werden die Anforderungen an virtuelle Netzwerkressourcen für die API Management aufgeführt. Einige Anforderungen unterscheiden sich je nach Version (`stv2` oder `stv1`) der [Rechenplattform](compute-infrastructure.md), die Ihre API Management-Instanz hostet.

### <a name="stv2"></a>[stv2](#tab/stv2)

* Ein virtuelles Azure Resource Manager-Netzwerk ist erforderlich.
* Sie müssen eine Standard SKU [öffentliche IPv4-Adresse](../virtual-network/ip-services/public-ip-addresses.md#standard) zusätzlich zur Angabe eines virtuellen Netzwerks und Subnetzes bereitstellen.
* Das Teilnetz, das zur Verbindung mit der API Management-Instanz verwendet wird, kann andere Azure-Ressourcentypen enthalten.
* Der API Management-Dienst, das virtuelle Netzwerk und Subnetz sowie die öffentliche IP-Adressressressressressource müssen sich in derselben Region und im selben Abonnement befinden.
* Bei der Bereitstellung von API Management für mehrere Regionen konfigurieren Sie die virtuellen Netzwerkressourcen für jeden Standort separat.

### <a name="stv1"></a>[stv1](#tab/stv1)

* Ein virtuelles Azure Resource Manager-Netzwerk ist erforderlich.
* Das Teilnetz, das für die Verbindung mit der API Management-Instanz verwendet wird, muss für API Management dediziert sein. Es darf keine anderen Azure-Ressourcentypen enthalten.
* Der API Management-Dienst, das virtuelle Netzwerk und die Subnetzressourcen müssen sich in derselben Region und im selben Abonnement befinden.
* Bei der Bereitstellung von API Management für mehrere Regionen konfigurieren Sie die virtuellen Netzwerkressourcen für jeden Standort separat.

---

## <a name="subnet-size"></a>Subnetzgröße

Die Mindestgröße des Teilnetzes, in dem API Management eingesetzt werden kann, ist /29, was drei nutzbare IP-Adressen ergibt. Jede zusätzliche Skalierungseinheit von API Management erfordert zwei weitere IP-Adressen. Die Mindestgröße basiert auf den folgenden Überlegungen:

* Azure reserviert in jedem Subnetz einige IP-Adressen, die nicht verwendet werden können. Die erste und letzte IP-Adresse der Subnetze sind aus Gründen der Protokollkonformität reserviert. Für Azure-Dienste werden drei weitere Adressen verwendet. Weitere Informationen finden Sie unter [Unterliegen die in den Subnetzen verwendeten IP-Adressen bestimmten Beschränkungen?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

* Zusätzlich zu den von der Azure-VNet-Infrastruktur genutzten IP-Adressen verwendet jede API Management-Instanz im Subnetz folgende IP-Adressen:
    * Zwei IP-Adressen pro Einheit der Premium-SKU oder 
    * Eine IP-Adresse für die Entwickler-SKU 

* Jede Instanz reserviert eine zusätzliche IP-Adresse für den externen Lastenausgleich. Beim Einsatz in einem [internen VNET](./api-management-using-with-internal-vnet.md) benötigt die Instanz eine zusätzliche IP-Adresse für den internen Load Balancer.

## <a name="routing"></a>Routing

Siehe die Routing-Anleitung, wenn Sie Ihre API Management-Instanz in einem [externen VNET](./api-management-using-with-vnet.md#routing) oder [internen VNET](./api-management-using-with-internal-vnet.md#routing) einsetzen.

Erfahren Sie mehr über die [IP-Adressen von API Management](api-management-howto-ip-addresses.md).

## <a name="dns"></a>DNS

Im externen Modus aktiviert das VNET [ die von Azure bereitgestellte Namensauflösung ](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) für Ihre API Management-Endpunkte und andere Azure-Ressourcen. Für lokale Ressourcen wird die Namensauflösung nicht unterstützt. 

Im internen Modus müssen Sie Ihre eigene DNS-Lösung bereitstellen, um die Namensauflösung für API Management-Endpunkte und andere erforderliche Azure-Ressourcen sicherzustellen. Wir empfehlen die Konfiguration einer Azure [privaten DNS-Zone](../dns/private-dns-overview.md).

Weitere Informationen finden Sie unter: 
* [Namensauflösung für Ressourcen in virtuellen Azure-Netzwerken](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).  
* [Erstellen Sie eine private Azure DNS-Zone](../dns/private-dns-getstarted-portal.md)

> [!IMPORTANT]
> Wenn Sie planen, eine benutzerdefinierte DNS-Lösung für das VNET zu verwenden, richten Sie diese ein **, bevor Sie**  einen API Management-Dienst in diesem einrichten. Andernfalls müssen Sie den API Management-Dienst jedes Mal aktualisieren, wenn Sie den/die DNS-Server ändern, indem Sie den Vorgang [Netzwerkkonfiguration anwenden](/rest/api/apimanagement/2020-12-01/api-management-service/apply-network-configuration-updates) ausführen.

## <a name="limitations"></a>Einschränkungen

Einige Einschränkungen hängen von der Version (`stv2` oder `stv1`) der [Rechenplattform](compute-infrastructure.md) ab, die Ihre API Management-Instanz hostet.

### <a name="stv2"></a>[stv2](#tab/stv2)

* Ein Teilnetz, das API Management-Instanzen enthält, kann nicht über Abonnements hinweg verschoben werden.
* Bei API Management-Bereitstellungen in mehreren Regionen mit Konfiguration im Modus für interne VNets sind die Benutzer Besitzer des Routings und dafür verantwortlich, den Lastenausgleich über mehrere Regionen hinweg zu verwalten.
* Um eine API aus einer [OpenAPI-Spezifikation](import-and-publish.md) in API Management zu importieren, muss die Spezifikations-URL unter einer öffentlich zugänglichen Internetadresse gehostet werden.

### <a name="stv1"></a>[stv1](#tab/stv1)

* Ein Subnetz, das API Management-Instanzen enthält, kann nicht abonnementübergreifend bewegt werden.
* Bei API Management-Bereitstellungen in mehreren Regionen mit Konfiguration im Modus für interne VNets sind die Benutzer Besitzer des Routings und dafür verantwortlich, den Lastenausgleich über mehrere Regionen hinweg zu verwalten.
* Um eine API aus einer [OpenAPI-Spezifikation](import-and-publish.md) in API Management zu importieren, muss die Spezifikations-URL unter einer öffentlich zugänglichen Internetadresse gehostet werden.
* Aufgrund von Plattformbeschränkungen funktioniert die Konnektivität zwischen einer Ressource in einem VNet mit globalem Peering in einer anderen Region und dem API Management-Dienst im internen Modus nicht. Weitere Informationen finden Sie in der Dokumentation [virtuelles Netzwerk](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints).

---

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:

* [Verbindung eines virtuellen Netzes mit dem Backend über VPN Gateway](../vpn-gateway/design.md#s2smulti)
* [Verbindung eines virtuellen Netzwerks über verschiedene Bereitstellungsmodelle](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Virtuelles Netzwerk häufig gestellte Fragen](../virtual-network/virtual-networks-faq.md)

Verbinden mit einem virtuellen Netzwerk:
* [Verbinden Sie sich mit einem externen virtuellen Netzwerk mit Azure API Management](./api-management-using-with-vnet.md).
* [Verbinden Sie sich mit einem internen virtuellen Netzwerk mit Azure API Management](./api-management-using-with-internal-vnet.md).

Überprüfen Sie die folgenden Themen

* [Verbinden eines virtuellen Netzwerks mit dem Back-End über VPN Gateway](../vpn-gateway/design.md#s2smulti)
* [Herstellen einer Verbindung mit einem virtuellen Netzwerk in verschiedenen Bereitstellungsmodellen](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Verwenden des API-Inspektors zur Verfolgung von Aufrufen in Azure API Management](api-management-howto-api-inspector.md)
* [Häufig gestellte Fragen zu Virtual Network](../virtual-network/virtual-networks-faq.md)
* [Diensttags](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/virtual-network-concepts/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/virtual-network-concepts/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[NetworkSecurityGroups]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags