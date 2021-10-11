---
title: Worum handelt es sich bei Azure Virtual Network-IP-Diensten?
description: Übersicht über die Azure Virtual Network-IP-Dienste Erfahren Sie, wie IP-Dienste funktionieren und wie Sie IP-Ressourcen in Azure verwenden.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subService: ip-services
ms.topic: overview
ms.date: 10/01/2021
ms.custom: template-overview
ms.openlocfilehash: 4215fb4682e29a06a7003e30ae53656e09f6f246
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369644"
---
# <a name="what-is-azure-virtual-network-ip-services"></a>Worum handelt es sich bei Azure Virtual Network-IP-Diensten?

IP-Dienste sind eine Sammlung von Diensten im Zusammenhang mit IP-Adressen, die die Kommunikation in Azure Virtual Network ermöglichen. Für die Kommunikation zwischen Ressourcen werden in Azure öffentliche und private IP-Adressen verwendet. Die Kommunikation mit Ressourcen kann in einem privaten virtuellen Azure-Netzwerk und über das öffentliche Internet erfolgen.

IP-Dienste umfassen:

* Öffentliche IP-Adressen

* Präfixe öffentlicher IP-Adressen

* Private IP-Adressen

* Routingpräferenz

* Routingpräferenz „Unmetered“

## <a name="public-ip-addresses"></a>Öffentliche IP-Adressen

Öffentliche IP-Adresse werden von Internetressourcen für die eingehende Kommunikation mit Ressourcen in Azure verwendet. Öffentliche IP-Adressen können mit einer IPv4- oder IPv6-Adresse erstellt werden. Möglicherweise haben Sie die Möglichkeit, eine Dual-Stack-Bereitstellung mit einer IPv4- und IPv6-Adresse zu erstellen. Öffentliche IP-Adressen sind in zwei SKUs verfügbar. **Standard** und **Basic**. Öffentliche IP-Adressen können statisch oder dynamisch zugewiesen werden.

Eine öffentliche IP-Adresse ist eine Ressource mit eigenen Eigenschaften. Sie können eine öffentliche IP-Adresse beispielsweise den folgenden Ressourcen zuordnen:

* Netzwerkschnittstellen für virtuelle Computer
* Load Balancer mit Internetzugriff
* Gateways für virtuelle Netzwerke (VPN/ER)
* NAT-Gateways
* Anwendungsgateways
* Azure Firewall
* Bastionhost

Weitere Informationen zu öffentlichen IP-Adressen finden Sie unter [Öffentliche IP-Adressen](../../virtual-network/public-ip-addresses.md) und [Erstellen, Ändern oder Löschen einer öffentlichen Azure-IP-Adresse](../../virtual-network/virtual-network-public-ip-address.md).

## <a name="public-ip-address-prefixes"></a>Präfixe öffentlicher IP-Adressen

Präfixe für öffentliche IP-Adressen sind reservierte Bereiche von IP-Adressen in Azure. Präfixe für öffentliche IP-Adressen bestehen aus IPv4- oder IPv6-Adressen.  In Regionen mit Verfügbarkeitszonen können Präfixe für öffentliche IP-Adressen zonenredundant erstellt oder einer bestimmten Verfügbarkeitszone zugeordnet werden. Nachdem das Präfix für öffentliche IP-Adressen erstellt wurde, können Sie öffentliche IP-Adressen erstellen.

Die folgenden Präfixgrößen für öffentliche IP-Adressen sind verfügbar:

-  /28 (IPv4) oder /124 (IPv6) = 16 Adressen
-  /29 (IPv4) oder /125 (IPv6) = 8 Adressen
-  /30 (IPv4) oder /126 (IPv6) = 4 Adressen
-  /31 (IPv4) oder /127 (IPv6) = 2 Adressen

Die Präfixgröße wird als CIDR-Maskengröße (Classless Inter-Domain Routing) angegeben.

Es gibt keine Einschränkungen hinsichtlich der Anzahl von Präfixen, die in einem Abonnement erstellt wurden. Die Anzahl der erstellten Bereiche kann nicht mehr statische öffentliche IP-Adressen umfassen, als in Ihrem Abonnement zulässig sind. Weitere Informationen finden Sie unter [Azure-Grenzwerte](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Weitere Informationen zu Präfixen für öffentliche IP-Adressen finden Sie unter [Präfixe öffentlicher IP-Adressen](../../virtual-network/public-ip-address-prefix.md) und [Erstellen, Ändern oder Löschen eines Präfixes für öffentliche IP-Adressen](../../virtual-network/manage-public-ip-address-prefix.md).

## <a name="private-ip-addresses"></a>Private IP-Adressen

Private IP-Adressen ermöglichen eine Kommunikation zwischen Ressourcen in Azure. Azure weist Ressourcen private IP-Adressen aus dem Adressbereich des VNET-Subnetzes zu, in dem sich eine Ressource befindet. Private IP-Adressen in Azure werden statisch oder dynamisch zugewiesen.

Sie können eine private IP-Adresse beispielsweise den folgenden Ressourcen zuordnen:

* Virtuelle Computer
* Interner Lastenausgleich
* Anwendungsgateways
* Private Endpunkte

Weitere Informationen zu privaten IP-Adressen finden Sie unter [Private IP-Adressen](../../virtual-network/private-ip-addresses.md).

## <a name="routing-preference"></a>Routingpräferenz

Die Azure-Routingpräferenz ermöglicht es Ihnen zu wählen, wie Ihr Datenverkehr zwischen Azure und dem Internet geleitet wird. Sie können auswählen, ob der Datenverkehr entweder über das Microsoft-Netzwerk oder über das ISP-Netzwerk (öffentliches Internet) geleitet werden soll. Sie können die Routingoption beim Erstellen einer öffentlichen IP-Adresse auswählen. Standardmäßig wird der Datenverkehr für alle Azure-Dienste über das globale Microsoft-Netzwerk geleitet. 

Auswahlmöglichkeiten für die Routingpräferenz:

* **Microsoft-Netzwerk:** Sowohl der ein- als auch der ausgehende Datenverkehr bleibt den größten Teil des Weges im globalen Microsoft-Netzwerk. Dieses Routing wird auch als *Cold Potato-Routing* bezeichnet.

* **Öffentliches Internet (ISP-Netzwerk):** Die neue Routingoption „Internet“ minimiert den Weg im globalen Microsoft-Netzwerk und nutzt das ISP-Transitnetzwerk zur Weiterleitung Ihres Datenverkehrs. Dieses Routing wird auch als *Hot Potato-Routing* bezeichnet.

Weitere Informationen zu den Routingpräferenzen finden Sie unter [Was ist die Routingpräferenz?](../../virtual-network/routing-preference-overview.md).

## <a name="routing-preference-unmetered"></a>Routingpräferenz „Unmetered“

Die Routingpräferenz „Unmetered“ ist für CDN-Anbieter (Content Delivery Network) verfügbar, deren Kundschaft ihre Ursprungsinhalte in Azure hosten. Mit dem Dienst können CDN-Anbieter direkte Peeringverbindungen mit Edgeroutern im globalen Netzwerk von Microsoft an verschiedenen Standorten herstellen.

Der Netzwerkdatenverkehr vom Ursprung in Azure an den CDN-Anbieter profitiert von der direkten Verbindung.

* Die Datenübertragung für Datenverkehr von Ihren Azure-Ressourcen, der über diese direkten Verbindungen geleitet wird, ist nicht kostenpflichtig.

* Die direkte Verbindung zwischen dem CDN-Anbieter und dem Ursprung in Azure bietet eine optimale Leistung, da dazwischen keine Hops vorhanden sind. Von dieser Verbindung profitiert die CDN-Workload, die häufig Daten vom Ursprung abruft.

Weitere Informationen zur Routingpräferenz „Unmetered“ finden Sie unter [Was ist die Routingpräferenz „Unmetered“?](../../virtual-network/routing-preference-unmetered.md).

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte beim Erstellen von IP-Dienstressourcen:

- [Erstellen einer öffentlichen IP-Adresse über das Azure-Portal](../../virtual-network/create-public-ip-portal.md)
- [Erstellen eines Präfixes für eine öffentliche IP-Adresse mithilfe des Azure-Portals](../../virtual-network/create-public-ip-prefix-portal.md)
- [Konfigurieren einer privaten IP-Adresse für eine VM im Azure-Portal](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)
- [Konfigurieren der Routingpräferenz für eine öffentliche IP-Adresse über das Azure-Portal](../../virtual-network/routing-preference-portal.md)
