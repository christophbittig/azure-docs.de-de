---
title: Azure Virtual WAN – Partner und Standorte | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Liste der Standorte von Azure Virtual WAN-Partnern und -Hubs.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.custom: references_regions
ms.openlocfilehash: c5d548105b5fd749865132ae0629eaac17462da2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818061"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Standorte von Virtual WAN-Partnern und -Hubs

Dieser Artikel enthält Informationen über die von Virtual WAN unterstützten Regionen und Partner für Konnektivität mit Virtual Hub.

Der Netzwerkdienst Azure Virtual WAN bietet optimierte und automatisierte Konnektivität zwischen Branches über Azure. Per Virtual WAN können Sie Branchgeräte für die Kommunikation mit Azure verbinden und konfigurieren. Verbindung und Konfiguration können entweder manuell oder mit Anbietergeräten über einen Virtual WAN-Partner hergestellt werden. Die Nutzung von Geräten eines Partners ermöglicht eine einfache Verwendung, die Vereinfachung der Verbindungsherstellung und die Konfigurationsverwaltung.

Konnektivität auf dem lokalen Gerät wird auf automatisierte Weise mit dem virtuellen Hub hergestellt. Ein virtueller Hub ist ein von Microsoft verwaltetes virtuelles Netzwerk. Der Hub enthält verschiedene Dienstendpunkte zum Aktivieren der Konnektivität über Ihr lokales Netzwerk (vpnsite). 

## <a name="branch-ipsec-connectivity-automation-from-partners"></a><a name="automation"></a>Branches: Automatisierung der IPsec-Konnektivität durch Partner

Geräte, die sich mit Azure Virtual WAN verbinden, verfügen für die Verbindungsherstellung über eine integrierte Automatisierung. Diese wird typischerweise auf der Geräteverwaltungsoberfläche (o.ä.) eingerichtet. Dadurch wird die Verbindungs- und Konfigurationsverwaltung zwischen dem VPN-Zweigstellengerät und einem Azure Virtual Hub VPN-Endpunkt (VPN Gateway) eingerichtet.

Die folgende allgemeine Automatisierung wird in der Gerätekonsole bzw. im Verwaltungscenter eingerichtet:

* Entsprechende Berechtigungen für das Gerät, um auf die Azure Virtual WAN-Ressourcengruppe zugreifen zu können
* Hochladen des Zweigstellengeräts in Azure Virtual WAN
* Automatischer Download der Azure-Konnektivitätsinformationen
* Konfiguration des lokalen Zweigstellengeräts 

Einige Konnektivitätspartner können die Automatisierung erweitern, indem sie das Azure Virtual Hub-VNET und das VPN-Gateway erstellen. Mehr über Automatisierung erfahren Sie unter [Automatisierungsrichtlinien für Virtual WAN-Partner](virtual-wan-configure-automation-providers.md).

## <a name="branch-ipsec-connectivity-partners"></a><a name="partners"></a>Branches: Partner für IPsec-Konnektivität

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Die folgenden Partner sind gemäß einer zwischen den Firmen unterzeichneten Vereinbarung über den Auftragsumfang zum Automatisieren der IPsec-Konnektivität zwischen dem Partnergerät und Azure Virtual WAN-VPN-Gateways vorgesehen: 128 Technologies, Arista, F5 Networks, Oracle SD-WAN (Talari) und SharpLink.

## <a name="partners-with-integrated-virtual-hub-offerings"></a>Partner mit integrierten Virtual Hub-Angeboten

Zusätzlich zur Verwendung von automatisierter IPsec-Konnektivität für Branches bieten einige Partner auch **virtuelle Netzwerkgeräte (Network Virtual Appliances, NVAs)** an, die direkt in den Azure Virtual WAN-Hub integriert werden können.  Kunden haben hierbei die Option, ihre Branchverbindungen auf einer kompatiblen Appliance eines Drittanbieters im virtuellen Hub enden zu lassen.  

Partner, die NVAs im Virtual WAN-Hub anbieten, müssen folgende Voraussetzungen erfüllen:

* Implementierung einer Automatisierung der IPsec-Konnektivität über ihr Branchgerät und Onboarding ihres NVA-Angebots für den Azure Virtual WAN-Hub.
* Vorhandenes Angebot für ein virtuelles Netzwerkgerät auf dem Azure Marketplace.

Wenn Sie Partner sind und Fragen zum Angebot von verwalteten NVAs im Virtual WAN-Hub haben, können Sie eine E-Mail an vwannvaonboarding@microsoft.com senden.

## <a name="integrated-virtual-hub-nva-partners"></a>Partner für integrierte Virtual WAN-Hub-NVA

Diese Partner verfügen über Angebote für **verwaltete Anwendungen**, die jetzt für die Bereitstellung im Virtual WAN-Hub verfügbar sind.

|Partner|Konfiguration/Anleitung/Bereitstellungsleitfaden|
|---|---|
|[Barracuda Networks](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overviewus/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overview)| [Bereitstellungshandbuch für Barracuda CloudGen WAN](https://campus.barracuda.com/product/cloudgenwan/doc/91980640/deployment/)|
|[Cisco Cloud Service Router (CSR) vWAN](https://aka.ms/ciscoMarketPlaceOffer)| Die Integration der Cisco SD-WAN-Lösung in Azure Virtual WAN erweitert Cloud OnRamp für Multicloudbereitstellungen und ermöglicht die Konfiguration von Cisco Catalyst 8000V Edge Software (Cisco Catalyst 8000V) als virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) in Azure Virtual WAN-Hubs. Sehen Sie sich den [Konfigurationsleitfaden für Cisco SD-WAN Cloud OnRamp, Cisco IOS XE Release 17.x](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) an. 
|[VMware SD-WAN im Virtual WAN-Hub](https://sdwan.vmware.com/partners/microsoft) | Anweisungen zur Wiedergabe der VMware SD-WAN-Appliance im Virtual WAN-Hub finden Sie im [Bereitstellungshandbuch für VMware SD-WAN](https://kb.vmware.com/s/article/82746). Um die Bereitstellung zu erstellen, greifen Sie [hier](https://azuremarketplace.microsoft.com/marketplace/apps/velocloud.vmware_sdwan_in_vwan) auf die verwaltete Anwendung zu.|

Es ist vorgesehen, dass die folgenden Partner in naher Zukunft Angebote für NVAs im virtuellen Hub bereitstellen: Aviatrix, Citrix und Versa Networks.

## <a name="locations"></a><a name="locations"></a>Standorte

[!INCLUDE [Virtual WAN regions file](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Virtual WAN finden Sie unter [Virtual WAN – Häufig gestellte Fragen](virtual-wan-faq.md).

* Weitere Informationen zur Automatisierung der Konnektivität mit Azure Virtual WAN finden Sie unter [Automatisierungsrichtlinien für Virtual WAN-Partner](virtual-wan-configure-automation-providers.md).
