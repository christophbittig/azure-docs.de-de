---
title: Routingpräferenz in Azure
description: Erhalten Sie Informationen darüber, wie Sie Ihre Datenverkehrsrouten zwischen Azure und dem Internet mit Routingpräferenz auswählen können.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: allensu
ms.openlocfilehash: 73bb9748fa13b6b5209bbbcd8c4332c28249aecc
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233940"
---
# <a name="what-is-routing-preference"></a>Was ist Routingpräferenz?

Die Azure-Routingpräferenz ermöglicht es Ihnen zu wählen, wie Ihr Datenverkehr zwischen Azure und dem Internet geleitet wird. Sie können auswählen, ob der Datenverkehr entweder über das Microsoft-Netzwerk oder über das ISP-Netzwerk (öffentliches Internet) geleitet werden soll. Diese Optionen werden auch als *Cold Potato-Routing* bzw. *Hot Potato-Routing* bezeichnet. Der Preis für die Übertragung von ausgehenden Daten variiert je nach Routingauswahl. Sie können die Routingoption beim Erstellen einer öffentlichen IP-Adresse auswählen. Die öffentliche IP-Adresse kann Ressourcen zugeordnet werden, z. B. einem virtuellen Computer, einer VM-Skalierungsgruppe, einem Lastenausgleich mit Internetzugriff usw. Sie können auch die Routingpräferenz für Azure-Speicherressourcen wie Blobs, Dateien, Web und Azure DataLake festlegen. Standardmäßig wird der Datenverkehr für alle Azure-Dienste über das globale Microsoft-Netzwerk geleitet.

## <a name="routing-via-microsoft-global-network"></a>Routing über das globale Microsoft-Netzwerk

Wenn Sie den Datenverkehr über das *globale Microsoft-Netzwerk* weiterleiten, wird der Datenverkehr über eines der größten Netzwerke auf der Welt übermittelt, das über 257.000 Kilometer an Glasfaser und über 165 Edge-POP-Standorte (Point of Presence) umfasst. Das Netzwerk ist ausreichend mit mehreren redundanten Glasfaserpfaden ausgestattet, um eine außergewöhnlich hohe Zuverlässigkeit und Verfügbarkeit sicherzustellen. Die Datenverkehrstechnik wird von einem softwaredefinierten WAN-Controller verwaltet, der eine Pfadauswahl mit geringer Latenz für Ihren Datenverkehr sicherstellt und erstklassige Netzwerkleistung bietet.

![Routing über das globale Microsoft-Netzwerk](./media/routing-preference-overview/route-via-microsoft-global-network.png)

**Eingehender Datenverkehr:** Die globale BGP Anycast-Ankündigung stellt sicher, dass der eingehende Datenverkehr in das zum Benutzer nächstgelegene Microsoft-Netzwerk gelangt. Wenn z. B. ein Benutzer aus Singapur auf Azure-Ressourcen zugreift, die in Chicago, USA, gehostet werden, gelangt der Datenverkehr in das globale Microsoft-Netzwerk beim Edge-POP-Standort in Singapur und wird über das Microsoft-Netzwerk an den in Chicago gehosteten Dienst weitergeleitet.

**Ausgehender Datenverkehr:** Der ausgehende Datenverkehr folgt demselben Prinzip. Der Datenverkehr bewegt sich größtenteils im globalen Netzwerk von Microsoft und verlässt das Netzwerk in der Nähe des Benutzers. Wenn z. B. der Datenverkehr von Azure in Chicago für einen Benutzer aus Singapur bestimmt ist, dann wird der Datenverkehr über das Microsoft-Netzwerk von Chicago nach Singapur geleitet und verlässt das Microsoft-Netzwerk beim Edge-POP-Standort in Singapur.

Sowohl der ein- als auch der ausgehende Datenverkehr bleibt größtenteils des Weges im globalen Microsoft-Netzwerk. Dies wird auch als *Cold Potato-Routing* bezeichnet.


## <a name="routing-over-public-internet-isp-network"></a>Routing über öffentliches Internet (ISP-Netzwerk)

Die neue Routingoption *Internetrouting* minimiert den Weg im globalen Microsoft-Netzwerk und nutzt das ISP-Transitnetzwerk zur Weiterleitung Ihres Datenverkehrs. Diese kostenoptimierte Routingoption bietet eine Netzwerkleistung, die mit anderen Cloudanbietern vergleichbar ist.

![Routing über öffentliches Internet](./media/routing-preference-overview/route-via-isp-network.png)

**Eingehender Datenverkehr:** Der Pfad für die eingehenden Daten verwendet *Hot Potato-Routing*, was bedeutet, dass der Datenverkehr in das jeweilige Microsoft-Netzwerk gelangt, das der gehosteten Dienstregion am nächsten liegt. Wenn z. B. ein Benutzer aus Singapur auf Azure-Ressourcen zugreift, die in Chicago gehostet werden, dann verläuft der Datenverkehr über das öffentliche Internet und gelangt in das globale Microsoft-Netzwerk in Chicago.

**Ausgehender Datenverkehr:** Der ausgehende Datenverkehr folgt demselben Prinzip. Der Datenverkehr verlässt das Microsoft-Netzwerk in derselben Region, in der der Dienst gehostet wird. Wenn z. B. der Datenverkehr von Ihrem Dienst in Azure Chicago für einen Benutzer aus Singapur bestimmt ist, dann verlässt der Datenverkehr das Microsoft-Netzwerk in Chicago und gelangt über das öffentliche Internet zum Benutzer in Singapur.

## <a name="supported-services"></a>Unterstützte Dienste

Öffentliche IP-Adresse mit der Routingpräferenzoption „globales Microsoft-Network“ kann allen Azure-Diensten zugeordnet werden. Allerdings kann eine öffentliche IP-Adresse mit Routingpräferenzoption **Internet** den folgenden Azure-Ressourcen zugeordnet werden:

* Virtueller Computer
* VM-Skalierungsgruppe
* Azure Kubernetes Service (AKS)
* Lastenausgleich mit Internetzugriff
* Application Gateway
* Azure Firewall

Für die Speicherung verwenden primäre Endpunkte immer das **globale Microsoft-Netzwerk**. Sie können sekundäre Endpunkte mit der Option **Internet** für das Routing des Datenverkehrs aktivieren. Unterstützte Speicherdienste:

* BLOBs
* Dateien
* Web
* Azure DataLake

## <a name="pricing"></a>Preise
Der Preisunterschied zwischen beiden Optionen spiegelt sich in den Preisen für die Übertragung ausgehender Daten über das Internet wider. Das Routing über **das globale Microsoft-Netzwerk** mit entsprechendem Preis für die Datenübertragung entspricht dem aktuellen Internetausgangspreis. Aktuelle Preisinformationen finden Sie auf der [Seite mit Preisinformationen zur Azure-Bandbreite](https://azure.microsoft.com/pricing/details/bandwidth/).

## <a name="limitations"></a>Einschränkungen


* Die Routingpräferenz ist nur mit der zonenredundanten Standard-SKU der öffentlichen IP-Adresse kompatibel. Die Basic-SKU der öffentlichen IP-Adresse wird nicht unterstützt.
* Die Routingpräferenz unterstützt derzeit nur öffentliche IPv4-IP-Adressen. Öffentliche IPv6 IP-Adressen werden nicht unterstützt.


## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr über die Optimierung der Konnektivität zu Ihren Microsoft Azure-Diensten über das Internet - Video](https://www.youtube.com/watch?v=j6A_Mbpuh6s&list=PLLasX02E8BPA5V-waZPcelhg9l3IkeUQo&index=12) 
* [Konfigurieren der Routingpräferenz für einen virtuellen Computer mithilfe von Azure PowerShell](./configure-routing-preference-virtual-machine-powershell.md)
* [Konfigurieren der Routingpräferenz für einen virtuellen Computer mithilfe der Azure CLI](./configure-routing-preference-virtual-machine-cli.md)