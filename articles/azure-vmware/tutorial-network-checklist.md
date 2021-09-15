---
title: 'Tutorial: Checkliste für die Netzwerkplanung'
description: Hier erfahren Sie mehr über die Netzwerkanforderungen für Netzwerkkonnektivität und -ports in Azure VMware Solution.
ms.topic: tutorial
ms.date: 07/01/2021
ms.openlocfilehash: cee4768cbf6382b3cce229267f9b2483c353b464
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123314493"
---
# <a name="networking-planning-checklist-for-azure-vmware-solution"></a>Checkliste für die Netzwerkplanung für Azure VMware Solution 

Azure VMware Solution bietet eine private VMware-Cloudumgebung, auf die Benutzer und Anwendungen von lokalen und Azure-basierten Umgebungen oder Ressourcen aus zugreifen können. Die Konnektivität wird mithilfe von Netzwerkdiensten wie Azure ExpressRoute- und VPN-Verbindungen bereitgestellt. Dafür werden bestimmte Netzwerkadressbereiche und Firewallports für die Aktivierung der Dienste benötigt. In diesem Artikel erfahren Sie, wie Sie Ihr Netzwerk ordnungsgemäß für die Verwendung mit Azure VMware Solution konfigurieren.

In diesem Tutorial erhalten Sie Informationen zu Folgendem:

> [!div class="checklist"]
> * Überlegungen zum virtuellen Netzwerk und zur ExpressRoute-Leitung
> * Anforderungen an Routing und Subnetz
> * Erforderliche Netzwerkports für die Kommunikation mit den Diensten
> * Überlegungen zu DHCP und DNS in Azure VMware Solution

## <a name="prerequisite"></a>Voraussetzungen
Stellen Sie sicher, dass alle Gateways mit dem Dienst des ExpressRoute-Anbieters eine ASN (Autonomous System Number, autonome Systemnummer) mit 4 Bytes unterstützen. Azure VMware Solution verwendet öffentliche ASNs mit 4 Bytes zum Ankündigen von Routen.

## <a name="virtual-network-and-expressroute-circuit-considerations"></a>Überlegungen zum virtuellen Netzwerk und zur ExpressRoute-Leitung
Wenn Sie eine Verbindung virtueller Netzwerke in Ihrem Abonnement erstellen, wird die ExpressRoute-Leitung über Peering eingerichtet. Sie verwendet einen Autorisierungsschlüssel und eine Peering-ID, die Sie im Azure-Portal anfordern. Das Peering ist eine private 1:1-Verbindung zwischen Ihrer privaten Cloud und dem virtuellen Netzwerk.

> [!NOTE] 
> Die ExpressRoute-Leitung ist nicht Teil einer privaten Cloudbereitstellung. Die Erläuterung der lokalen ExpressRoute-Leitung geht über den Rahmen dieses Dokuments hinaus. Wenn Sie eine lokale Verbindung mit Ihrer privaten Cloud benötigen, können Sie eine Ihrer vorhandenen ExpressRoute-Leitungen verwenden oder eine im Azure-Portal erwerben.

Wenn Sie eine private Cloud bereitstellen, erhalten Sie IP-Adressen für vCenter und NSX-T Manager. Um auf diese Verwaltungsschnittstellen zuzugreifen, müssen Sie weitere Ressourcen im virtuellen Netzwerk Ihres Abonnements erstellen. Die Schritte zum Erstellen dieser Ressourcen und zum Einrichten des [privaten Peerings mit ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md) sind in den Tutorials beschrieben.

Das logische Netzwerk der privaten Cloud wird mit vorab bereitgestelltem NSX-T bereitgestellt. Ein Tier-0-Gateway und ein Tier-1-Gateway werden ebenfalls vorab für Sie bereitgestellt. Sie können ein Segment erstellen und an das vorhandene Tier-1-Gateway anfügen oder ein neues Tier-1-Gateway definieren und das Segment daran anfügen. Die logischen NSX-T-Netzwerkkomponenten unterstützen eine Ost-West-Konnektivität zwischen Workloads und eine Nord-Süd-Konnektivität mit dem Internet und Azure-Diensten.

>[!IMPORTANT]
>[!INCLUDE [disk-pool-planning-note](includes/disk-pool-planning-note.md)] 

## <a name="routing-and-subnet-considerations"></a>Überlegungen zu Routing und Subnetz
Die private Azure VMware Solution-Cloud wird über eine Azure ExpressRoute-Verbindung mit Ihrem virtuellen Azure-Netzwerk verbunden. Über diese Verbindung mit hoher Bandbreite und geringer Wartezeit können Sie von Ihrer privaten Cloudumgebung aus auf Dienste zugreifen, die in Ihrem Azure-Abonnement ausgeführt werden. Das Routing ist BGP-basiert (Border Gateway Protocol), das automatisch erfolgt und standardmäßig für jede Bereitstellung einer privaten Cloud aktiviert wird. 

Private Azure VMware Solution-Clouds benötigen mindestens einen `/22`-CIDR-Netzwerkadressblock für Subnetze, wie unten gezeigt. Dieses Netzwerk ergänzt Ihre lokalen Netzwerke. Der Adressblock darf sich daher nicht mit Adressblöcken überlappen, die in anderen virtuellen Netzwerken in Ihrem Abonnement und Ihren lokalen Netzwerken verwendet werden. Innerhalb dieses Adressblocks werden Verwaltungs-, Bereitstellungs- und vMotion-Netzwerke automatisch bereitgestellt.

>[!NOTE]
>Zulässige Bereiche für Ihren Adressblock sind die privaten RFC 1918-Adressräume (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) mit Ausnahme von 172.17.0.0/16.

Beispiel für einen CIDR-Netzwerkadressblock vom Typ `/22`: `10.10.0.0/22`

Subnetze:

| Verwendung des Netzwerks             | Subnet | Beispiel          |
| ------------------------- | ------ | ---------------- |
| Verwaltung von privaten Clouds  | `/26`  | `10.10.0.0/26`   |
| HCX-Verwaltungsmigrationen       | `/26`  | `10.10.0.64/26`  |
| Global Reach reserviert     | `/26`  | `10.10.0.128/26` |
| NSX-T-DNS-Dienst         | `/32`  | `10.10.0.192/32` |
| Reserviert                  | `/32`  | `10.10.0.193/32` |
| Reserviert                  | `/32`  | `10.10.0.194/32` |
| Reserviert                  | `/32`  | `10.10.0.195/32` |
| Reserviert                  | `/30`  | `10.10.0.196/30` |
| Reserviert                  | `/29`  | `10.10.0.200/29` |
| Reserviert                  | `/28`  | `10.10.0.208/28` |
| ExpressRoute-Peering      | `/27`  | `10.10.0.224/27` |
| ESXi-Verwaltung           | `/25`  | `10.10.1.0/25`   |
| vMotion-Netzwerk           | `/25`  | `10.10.1.128/25` |
| Replikationsnetzwerk       | `/25`  | `10.10.2.0/25`   |
| vSAN                      | `/25`  | `10.10.2.128/25` |
| HCX-Uplink                | `/26`  | `10.10.3.0/26`   |
| Reserviert                  | `/26`  | `10.10.3.64/26`  |
| Reserviert                  | `/26`  | `10.10.3.128/26` |
| Reserviert                  | `/26`  | `10.10.3.192/26` |



## <a name="required-network-ports"></a>Erforderliche Netzwerkports

| `Source` | Ziel | Protokoll | Port | Beschreibung  | 
| ------ | ----------- | :------: | :---:| ------------ | 
| DNS-Server für die private Cloud | Lokaler DNS-Server | UDP | 53 | DNS-Client: Weiterleitung von Anforderungen der PC-vCenter-Instanz für beliebige lokale DNS-Abfragen (siehe DNS-Abschnitt unten) |  
| Lokaler DNS-Server   | DNS-Server für die private Cloud | UDP | 53 | DNS-Client: Weiterleitung von Anforderungen lokaler Dienste an DNS-Server für die private Cloud (siehe DNS-Abschnitt unten) |  
| Lokales Netzwerk  | vCenter Server für die private Cloud  | TCP (HTTP)  | 80 | Von vCenter Server wird Port 80 für direkte HTTP-Verbindungen benötigt. Vom Port 80 werden Anforderungen an den HTTPS-Port 443 umgeleitet. Diese Umleitung ist hilfreich, wenn Sie `http://server` anstelle von `https://server` verwenden.  |  
| Verwaltungsnetzwerk für die private Cloud | Lokales Active Directory  | TCP  | 389/636 | Diese Ports sind offen, um die Kommunikation von Azure VMware Solution vCenter mit beliebigen lokalen Active Directory-/LDAP-Servern zu ermöglichen.  Diese Ports sind optional und dienen dazu, eine lokale AD-Instanz als Identitätsquelle für die vCenter-Instanz der privaten Cloud zu konfigurieren. Aus Sicherheitsgründen wird Port 636 empfohlen. |  
| Verwaltungsnetzwerk für die private Cloud | Globaler Katalog für lokales Active Directory  | TCP  | 3268/3269 | Diese Ports sind offen, um die Kommunikation von Azure VMware Solution vCenter mit beliebigen lokalen Active Directory-/globalen LDAP-Katalogservern zu ermöglichen.  Diese Ports sind optional und dienen dazu, eine lokale AD-Instanz als Identitätsquelle für die vCenter-Instanz der privaten Cloud zu konfigurieren. Aus Sicherheitsgründen wird Port 3269 empfohlen. |  
| Lokales Netzwerk  | vCenter Server für die private Cloud  | TCP (HTTPS)  | 443 | Dieser Port ermöglicht den Zugriff auf vCenter über ein lokales Netzwerk. Der Standardport, der vom vCenter Server-System verwendet wird, um auf Verbindungen des vSphere-Clients zu lauschen. Öffnen Sie den Port 443 in der Firewall, damit das vCenter Server-System Daten vom vSphere-Client empfangen kann. Das vCenter Server-System verwendet den Port 443 auch zur Überwachung der Datenübertragung von SDK-Clients. |  
| Lokales Netzwerk  | HCX Manager  | TCP (HTTPS) | 9443 | Hybrid Cloud Manager-Schnittstelle für die Verwaltung virtueller Geräte für die Hybrid Cloud Manager-Systemkonfiguration. |
| Administratornetzwerk  | Hybrid Cloud Manager | SSH | 22 | Administrator-SSH-Zugriff auf Hybrid Cloud Manager. |
| HCX Manager | Cloudgateway | TCP (HTTPS) | 8123 | Senden hostbasierter Replikationsdienstanweisungen an das Hybrid Cloud-Gateway. |
| HCX Manager | Cloudgateway | HTTP TCP (HTTPS) | 9443 | Senden von Verwaltungsanweisungen an das lokale Hybrid Cloud-Gateway über die REST-API. |
| Cloudgateway | L2C | TCP (HTTPS) | 443 | Senden von Verwaltungsanweisungen vom Cloudgateway an L2C, wenn L2C den gleichen Pfad verwendet wie das Hybrid Cloud-Gateway. |
| Cloudgateway | ESXi-Hosts | TCP | 80, 902 | Verwaltung und OVF-Bereitstellung |
| Cloudgateway (lokal)| Cloudgateway (remote) | UDP | 4500 | Erforderlich für IPsec<br>   Internetschlüsselaustausch (IKEv2) zur Kapselung von Workloads für den bidirektionalen Tunnel. NAT-Traversal (NAT-T) wird ebenfalls unterstützt. |
| Cloudgateway (lokal) | Cloudgateway (remote)  | UDP | 500 | Erforderlich für IPsec<br> Internetschlüsselaustausch (ISAKMP) für den bidirektionalen Tunnel. |
| Lokales vCenter-Netzwerk | Verwaltungsnetzwerk für die private Cloud | TCP | 8.000 |  vMotion für virtuelle Computer aus der lokalen vCenter-Instanz zur vCenter-Instanz in der privaten Cloud   |     

## <a name="dhcp-and-dns-resolution-considerations"></a>Aspekte zu DHCP- und DNS-Auflösung

[!INCLUDE [dhcp-dns-in-azure-vmware-solution-description](includes/dhcp-dns-in-azure-vmware-solution-description.md)]


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie mehr über die Überlegungen und Anforderungen im Zusammenhang mit der Bereitstellung einer privaten Azure VMware Solution-Cloud erfahren. Nach der ordnungsgemäßen Einrichtung des Netzwerks können Sie im nächsten Tutorial Ihre private Azure VMware Solution-Cloud erstellen.

> [!div class="nextstepaction"]
> [Erstellen einer privaten Cloud von Azure VMware Solution](tutorial-create-private-cloud.md)
