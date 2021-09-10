---
title: 'Azure ExpressRoute: Voraussetzungen'
description: Diese Seite enthält eine Liste der Anforderungen, die erfüllt sein müssen, bevor Sie eine Azure ExpressRoute-Verbindung anfordern können. Sie umfasst eine Checkliste.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 6f5b3cc109d3583defce78c3ae52848188fca900
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284542"
---
# <a name="expressroute-prerequisites--checklist"></a>Voraussetzungen und Checkliste für ExpressRoute
Zum Herstellen einer Verbindung mit den Microsoft-Clouddiensten über ExpressRoute müssen Sie sicherstellen, dass die unten in den Abschnitten genannten Voraussetzungen erfüllt sind.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure-Konto
* Ein gültiges und aktives Microsoft Azure-Konto. Dieses Konto ist zum Einrichten der ExpressRoute-Verbindung erforderlich. Bei ExpressRoute-Verbindungen handelt es sich um Ressourcen in Azure-Abonnements. Ein Azure-Abonnement ist auch dann erforderlich, wenn die Konnektivität auf andere Microsoft Cloud Services als Azure beschränkt ist, z. B. Microsoft 365.
* Darüber hinaus ist (bei Verwendung von Microsoft 365-Diensten) ein aktives Microsoft 365-Abonnement erforderlich. Weitere Informationen finden Sie im Abschnitt zu den spezifischen Anforderungen für Microsoft 365 in diesem Artikel.

## <a name="connectivity-provider"></a>Konnektivitätsanbieter

* Sie können mit einem [ExpressRoute-Konnektivitätspartner](expressroute-locations.md#partners) zusammenarbeiten, um eine Verbindung mit der Microsoft Cloud herzustellen. Zum Einrichten einer Verbindung zwischen Ihrem lokalen Netzwerk und Microsoft stehen Ihnen [drei Möglichkeiten](expressroute-introduction.md)zur Verfügung.
* Wenn Ihr Anbieter kein ExpressRoute-Konnektivitätspartner ist, können Sie die Verbindung zur Microsoft Cloud über einen [Cloud-Exchange-Anbieter](expressroute-locations.md#connectivity-through-exchange-providers)herstellen.

## <a name="network-requirements"></a>Netzwerkanforderungen
* **Redundanz an jedem Peeringstandort**: Für Microsoft ist es erforderlich, dass zwischen den Routern von Microsoft und den Peeringroutern redundante BGP-Sitzungen für jede ExpressRoute-Verbindung eingerichtet werden, (auch wenn [zum Cloud-Exchange nur eine physische Verbindung](expressroute-faqs.md#onep2plink) besteht).
* **Redundanz für die Notfallwiederherstellung**: Microsoft empfiehlt dringend, mindestens zwei ExpressRoute-Verbindungen an verschiedenen Peeringstandorten einzurichten, um einen Single Point of Failure zu vermeiden.
* **Routing**: Je nachdem, wie Sie die Verbindung mit Microsoft Cloud herstellen, müssen Sie oder Ihr Anbieter die BGP-Sitzungen für [Routingdomänen](expressroute-circuit-peerings.md)einrichten und verwalten. Einige Ethernet-Konnektivitätsanbieter oder Cloud-Exchange-Anbieter bieten BGP-Verwaltung als Dienst an, der einen Mehrwert schafft.
* **NAT**: Microsoft akzeptiert nur öffentliche IP-Adressen über Microsoft-Peering. Wenn Sie private IP-Adressen in Ihrem lokalen Netzwerk verwenden, müssen Sie oder Ihr Anbieter die privaten IP-Adressen [mithilfe der NAT](expressroute-nat.md)in öffentliche IP-Adressen übersetzen.
* **QoS**: Skype for Business umfasst verschiedene Dienste (z.B. Sprachanrufe, Videoanrufe, SMS) mit jeweils unterschiedlichen QoS-Anforderungen. Sie und Ihr Anbieter müssen daher die [QoS-Anforderungen](expressroute-qos.md)berücksichtigen.
* **Netzwerksicherheit**: Berücksichtigen Sie die [Netzwerksicherheit](/azure/cloud-adoption-framework/reference/networking-vdc) beim Herstellen der Verbindung mit der Microsoft Cloud über ExpressRoute.

## <a name="microsoft-365"></a>Microsoft 365
Wenn Sie Microsoft 365 für ExpressRoute aktivieren möchten, helfen Ihnen die Informationen in den folgenden Dokumenten zu den Anforderungen für Microsoft 365 weiter.

* [Azure ExpressRoute für Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* [Routing mit ExpressRoute für Microsoft 365](/microsoft-365/enterprise/routing-with-expressroute)
* [Hochverfügbarkeit und Failover mit ExpressRoute](/microsoft-365/enterprise/network-planning-with-expressroute)
* [Microsoft 365-URLs und -IP-Adressbereiche](/microsoft-365/enterprise/urls-and-ip-address-ranges)
* [Netzwerkplanung und Leistungsoptimierung für Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance)
* [Netzwerk- und Migrationsplanung für Microsoft 365](/microsoft-365/enterprise/network-and-migration-planning)
* [Microsoft 365-Integration mit lokalen Umgebungen](/microsoft-365/enterprise/microsoft-365-integration)
* [Über Office 365-IP-Adressänderungen auf dem neuesten Stand bleiben](/microsoft-365/enterprise/microsoft-365-ip-web-service?view=o365-worldwide)
* [ExpressRoute unter Office 365 – Schulungsvideos für Fortgeschrittene](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).
* Suchen Sie einen ExpressRoute-Konnektivitätsanbieter. Siehe [ExpressRoute-Partner und Peeringstandorte](expressroute-locations.md).
* Sehen Sie sich die Anforderungen für [Routing](expressroute-routing.md), [NAT](expressroute-nat.md) und [QoS](expressroute-qos.md) an.
* Konfigurieren Sie Ihre ExpressRoute-Verbindung.
  * [Erstellen Sie eine ExpressRoute-Verbindung.](expressroute-howto-circuit-arm.md)
  * [Konfigurieren des Routings](expressroute-howto-routing-arm.md)
  * [Verknüpfen eines VNet mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md)