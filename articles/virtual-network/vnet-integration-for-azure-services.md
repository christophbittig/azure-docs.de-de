---
title: Integration virtueller Netzwerke von Azure-Diensten für die Netzwerkisolation
titlesuffix: Azure Virtual Network
description: In diesem Artikel werden verschiedene Methoden zur Integration eines Azure-Diensts in ein virtuelles Netzwerk beschrieben, die Ihnen den sicheren Zugriff auf den Azure-Dienst ermöglichen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: d420c535ba7d230c357337c1500d0e2b187ec25a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130262810"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>Integrieren von Azure-Diensten mit virtuellen Netzwerken zur Netzwerkisolation

Die VNet-Integration (virtuelles Netzwerk) für einen Azure-Dienst ermöglicht es Ihnen, den Zugriff auf den Dienst nur auf Ihre virtuelle Netzwerkinfrastruktur zu beschränken. Die VNet-Infrastruktur umfasst auch virtuelle Netzwerke mit Peering und lokale Netzwerke.

Die VNet-Integration bietet Azure-Diensten die Vorteile der Netzwerkisolation und kann durch eine oder mehrere der folgenden Methoden erreicht werden:
- [Bereitstellen dedizierter Instanzen des Diensts in einem virtuellen Netzwerk](virtual-network-for-azure-services.md). Auf die Dienste kann dann innerhalb des virtuellen Netzwerks und von lokalen Netzwerken aus privat zugegriffen werden.
- Verwenden von [Privaten Endpunkten](../private-link/private-endpoint-overview.md), die eine private und sichere Verbindung zwischen Ihnen und dem Dienst herstellen, der von [Azure Private Link](../private-link/private-link-overview.md) unterstützt wird. Ein privater Endpunkt verwendet eine private IP-Adresse in Ihrem VNet und bindet den Dienst so effektiv in das virtuelle Netzwerk ein.
- Zugreifen auf den Dienst unter Verwendung öffentlicher Endpunkte, indem ein virtuelles Netzwerk mithilfe von [Dienstendpunkten](virtual-network-service-endpoints-overview.md) auf den Dienst erweitert wird. Durch Dienstendpunkte können Dienstressourcen an das virtuelle Netzwerk gebunden werden.
- Verwenden von [Diensttags](service-tags-overview.md), um Datenverkehr zu Ihren Azure-Ressourcen von und zu öffentlichen IP-Endpunkten zuzulassen oder zu verweigern.

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Bereitstellen von dedizierten Azure-Diensten in virtuellen Netzwerken

Wenn Sie dedizierte Azure-Dienste in einem virtuellen Netzwerk bereitstellen, können Sie über private IP-Adressen privat mit den Dienstressourcen kommunizieren.

![Bereitstellen von dedizierten Azure-Diensten in virtuellen Netzwerken](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Das Bereitstellen eines dedizierten Azure-Diensts in Ihrem virtuellen Netzwerk bietet die folgenden Möglichkeiten:
- Ressourcen im virtuellen Netzwerk können über private IP-Adressen privat miteinander kommunizieren. Beispiel: Direktes Übertragen von Daten zwischen HDInsight und SQL Server auf einem virtuellen Computer im virtuellen Netzwerk.
- Lokale Ressourcen können mit privaten IP-Adressen über Site-to-Site-VPN (VPN Gateway) oder ExpressRoute auf Ressourcen in einem virtuellen Netzwerk zugreifen.
- Für virtuelle Netzwerke kann Peering eingerichtet werden, damit Ressourcen in den virtuellen Netzwerken mithilfe privater IP-Adressen miteinander kommunizieren können.
- Dienstinstanzen in einem virtuellen Netzwerk werden in der Regel vollständig vom Azure-Dienst verwaltet. Diese Verwaltung schließt Überwachung der Integrität der Ressourcen und Skalieren mit Last ein.
- Dienstinstanzen werden in einem Subnetz in einem virtuellen Netzwerk bereitgestellt. Eingehender und ausgehender Netzwerkzugriff auf das Subnetz muss gemäß der Anleitung durch den Dienst über Netzwerksicherheitsgruppen geöffnet sein.
- Bestimmte Dienste legen Einschränkungen für das Subnetz fest, in dem sie bereitgestellt werden. Diese Einschränkungen betreffen die Anwendung von Richtlinien, Routen oder die Kombination von VMs und Dienstressourcen innerhalb desselben Subnetzes. Überprüfen Sie für jeden Dienst die spezifischen Einschränkungen, da sie sich im Laufe der Zeit ändern können. Beispiele für solche Dienste sind Azure NetApp Files, Dedicated HSM, Azure Container Instances, App Service.
- Optional ist für Dienste möglicherweise ein delegiertes Subnetz als expliziter Bezeichner erforderlich, damit ein Subnetz einen bestimmten Diensts hosten kann. Durch Delegieren erhalten Dienste explizite Berechtigungen zum Erstellen dienstspezifischer Ressourcen im delegierten Subnetz.
- Betrachten Sie ein Beispiel für eine REST-API-Antwort in einem virtuellen Netzwerk mit einem delegierten Subnetz. Eine umfassende Liste der Dienste, die das Modell des delegierten Subnetzes verwenden, erhalten Sie über die API für verfügbare Delegierungen.

Eine Liste der Dienste, die in einem virtuellen Netzwerk bereitgestellt werden können, finden Sie unter [Bereitstellen von dedizierten Azure-Diensten in virtuellen Netzwerken](virtual-network-for-azure-services.md).

## <a name="private-link-and-private-endpoints"></a>Private Link und private Endpunkte

Private Endpunkte ermöglichen den sicheren Eingang von Datenverkehr aus Ihrem virtuellen Netzwerk zu einer Azure-Ressource. Diese private Verbindung wird ohne die Erfordernis öffentlicher IP-Adressen eingerichtet. Ein privater Endpunkt ist eine spezielle Netzwerkschnittstelle für einen Azure-Dienst in Ihrem virtuellen Netzwerk. Wenn Sie einen privaten Endpunkt für Ihre Ressource erstellen, wird eine sichere Verbindung zwischen Clients in Ihrem virtuellen Netzwerk und Ihrer Azure-Ressource bereitgestellt. Dem privaten Endpunkt wird eine IP-Adresse aus dem IP-Adressbereich Ihres virtuellen Netzwerks zugewiesen. Die Verbindung zwischen dem privaten Endpunkt und dem Azure-Dienst ist eine private Verbindung.

Im Diagramm wird auf der rechten Seite ein Azure SQL-Datenbank als der PaaS-Dienst angezeigt, der das Ziel ist. Das Ziel kann [jeder Dienst sein, der private Endpunkte unterstützt](../private-link/availability.md). Es gibt mehrere Instanzen des logischen SQL Server für mehrere Kunden, die alle über öffentliche IP-Adressen erreichbar sind.

In diesem Fall wird eine Instanz eines logischen SQL Server mit einem privaten Endpunkt verfügbar gemacht. Der Endpunkt macht den SQL Server über eine private IP-Adresse im virtuellen Netzwerk des Clients erreichbar. Aufgrund der Änderung der DNS-Konfiguration sendet die Clientanwendung ihren Datenverkehr jetzt direkt an diesen privaten Endpunkt. Beim Zieldienst kommt Datenverkehr an, der von einer privaten IP-Adresse des VNet stammt. 

Der private Link wird durch den grünen Pfeil gekennzeichnet. Neben dem privaten Endpunkt kann die Zielressource weiterhin eine öffentliche IP-Adresse _besitzen_. Die öffentliche IP-Adresse wird nicht mehr von der Clientanwendung verwendet. Die Firewall kann den Zugriff auf diese öffentliche IP-Adresse sperren, sodass _nur_ über private Endpunkte zugegriffen werden kann. Verbindungen mit einem SQL Server ohne privaten Endpunkt aus dem VNet stammen weiterhin von einer öffentlichen IP-Adresse. Dieser Datenfluss wird durch den blauen Pfeil dargestellt.

![Private Endpunkte](./media/network-isolation/architecture-private-endpoints.png)

Die Clientanwendung verwendet in der Regel einen DNS-Hostnamen, um den Zieldienst zu erreichen. An der Anwendung sind keine Änderungen erforderlich. Die [DNS-Auflösung im VNet muss so konfiguriert werden](../private-link/private-endpoint-dns.md), dass der gleiche Hostname nicht als die ursprüngliche öffentliche IP-Adresse, sondern als die private IP-Adresse der Zielressource aufgelöst wird. Durch die private Verbindung zwischen dem Client und dem Zieldienst ist der Client nicht auf die öffentliche IP-Adresse angewiesen. Der Zieldienst kann den öffentlichen Zugriff deaktivieren.

Durch diese Gefährdung einzelner Instanzen können Sie [Datendiebstahl verhindern](../private-link/private-endpoint-overview.md#network-security-of-private-endpoints). Böswillige Akteure können keine Informationen aus der Datenbank sammeln und in eine andere öffentliche Datenbank oder ein anderes Speicherkonto hochladen. Sie können den Zugriff auf die öffentlichen IP-Adressen _aller_ PaaS-Dienste verhindern. Sie können den Zugriff auf PaaS-Instanzen über ihre privaten Endpunkte weiterhin zulassen.

Weitere Informationen zu Private Link und eine Liste der unterstützten Azure-Dienste finden Sie unter [Was ist Private Link?](../private-link/private-link-overview.md).

## <a name="service-endpoints"></a>Dienstendpunkte

Dienstendpunkte bieten sichere und direkte Konnektivität mit Azure-Diensten über das Azure-Backbonenetzwerk. Endpunkte ermöglichen es Ihnen, Ihre Azure-Ressourcen auf Ihre virtuellen Netzwerke zu beschränken und so zu schützen. Mit Dienstendpunkten können private IP-Adressen im VNet einen Azure-Dienst erreichen, ohne dass eine ausgehende öffentliche IP-Adresse erforderlich ist.

Ohne Dienstendpunkte kann es schwierig sein, den Zugriff nur auf Ihr VNet zu beschränken. Die Quell-IP-Adresse kann sich ändern oder mit anderen Kunden geteilt werden. Ein Beispiel dafür sind PaaS-Dienste mit gemeinsam genutzten ausgehenden IP-Adressen. Bei Dienstendpunkten wird die Quell-IP-Adresse, die dem Zieldienst angezeigt wird, zu einer privaten IP-Adresse aus Ihrem VNet. Durch diese Änderung des eingehenden Datenverkehrs kann der Ursprung einfach identifiziert und zum Konfigurieren geeigneter Firewallregeln verwendet werden. Es kann etwa nur Datenverkehr aus einem bestimmten Subnetz innerhalb dieses VNet zugelassen werden.

Mit Dienstendpunkten bleiben DNS-Einträge für Azure-Dienste unverändert und werden weiterhin als öffentliche IP-Adressen aufgelöst, die dem Azure-Dienst zugewiesen werden.

Im folgenden Diagramm ist die rechte Seite der gleiche PaaS-Zieldienst. Auf der linken Seite befindet sich ein Kunden-VNet mit zwei Subnetzen: Subnetz A mit einem Dienstendpunkt für `Microsoft.Sql` und Subnetz B, für das keine Dienstendpunkte definiert sind. 

Wenn eine Ressource in Subnetz B versucht, einen beliebigen SQL Server zu erreichen, verwendet sie eine öffentliche IP-Adresse für die ausgehende Kommunikation. Dieser Datenverkehr wird durch den blauen Pfeil dargestellt. Die SQL Server-Firewall muss den Netzwerkdatenverkehr zu dieser öffentlichen IP-Adresse entweder zulassen oder blockieren. 

Wenn eine Ressource in Subnetz A versucht, einen Datenbankserver zu erreichen, wird sie als private IP-Adresse aus dem VNet angezeigt. Dieser Datenverkehr wird durch den blauen Pfeil dargestellt. Die SQL Server-Firewall kann jetzt Subnetz A explizit zulassen oder blockieren. Die Kenntnis der öffentlichen IP-Adresse des Quelldiensts ist nicht erforderlich.

![Dienstendpunkte](./media/network-isolation/architecture-service-endpoints.png)

Dienstendpunkte gelten für **alle** Instanzen des Zieldiensts. Beispielsweise **alle** SQL Server-Instanzen von Azure-Kunden, nicht nur die Instanz des Kunden.

Weitere Informationen finden Sie unter [Dienstendpunkte im virtuellen Netzwerk](virtual-network-service-endpoints-overview.md).

## <a name="service-tags"></a>Diensttags

Ein Diensttag steht für eine Gruppe von IP-Adresspräfixen eines bestimmten Azure-Diensts. Mit Diensttags können Sie Netzwerkzugriffssteuerungen in [Netzwerksicherheitsgruppen](./network-security-groups-overview.md#security-rules) oder in der [Azure Firewall](../firewall/service-tags.md) definieren. Sie können den Datenverkehr für den Dienst zulassen oder verweigern. Um den Datenverkehr zuzulassen oder zu verweigern, geben Sie den Diensttag im Quell- oder Zielfeld einer Regel an. 

![Zulassen oder Verweigern von Datenverkehr mithilfe von Diensttags](./media/network-isolation/service-tags.png)

So erreichen Sie Netzwerkisolation und Schutz Ihrer Azure-Ressourcen im Internet beim Zugriff auf Azure-Dienste mit öffentlichen Endpunkten. Erstellen Sie ein-/ausgehende Netzwerksicherheitsgruppen-Regeln, um Datenverkehr zum und aus dem **Internet** zu verweigern und Datenverkehr von/zu **AzureCloud** zuzulassen. Weitere Diensttags finden Sie unter [Verfügbare Diensttags](service-tags-overview.md#available-service-tags) bestimmter Azure-Dienste.

Weitere Informationen zu Diensttags und Azure-Diensten, die diese unterstützen, finden Sie unter [Übersicht über Diensttags](service-tags-overview.md).

## <a name="compare-private-endpoints-and-service-endpoints"></a>Private Endpunkte und VNet-Dienstendpunkte im Vergleich

>[!NOTE]
> Microsoft empfiehlt die Verwendung von Azure Private Link. Private Link bietet bessere Möglichkeiten im Hinblick auf den privaten Zugriff auf PaaS aus dem lokalen Netzwerk, integrierten Schutz vor Datenexfiltration und eine Zuordnung zwischen dem Dienst und der privaten IP-Adresse in Ihrem eigenen Netzwerk. Weitere Informationen hierzu finden Sie unter [Azure Private Link](../private-link/private-link-overview.md).

Anstatt nur die Unterschiede zu betrachten, sollte beachtet werden, dass Dienstendpunkte und private Endpunkte gemeinsame Merkmale aufweisen.

Beide Features werden für eine präzisere Steuerung der Firewall auf dem Zieldienst verwendet. Beispielsweise das Einschränken des Zugriffs auf SQL Server Datenbanken oder Speicherkonten. Wie in den Abschnitten oben ausgeführt wurde, unterscheidet sich dieser Vorgang jedoch bei den beiden Endpunktarten.

Beide Ansätze lösen das Problem der [Portauslastung bei der Quellnetzwerk-Adressenübersetzung](../load-balancer/load-balancer-outbound-connections.md#scenarios) (Source Network Address Translation, SNAT). Möglicherweise stellen Sie eine Auslastung fest, wenn Sie Datenverkehr über ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) oder einen Dienst mit limitierten SNAT-Ports tunneln. Wenn Sie Dienstendpunkte oder private Endpunkte verwenden, nimmt der Datenverkehr einen optimierten Pfad direkt zum Zieldienst. Von beiden Ansätze können bandbreitenintensive Anwendungen profitieren, da sowohl Latenz als auch Kosten reduziert werden.

In beiden Fällen können Sie dennoch sicherstellen, dass der Datenverkehr zum Zieldienst über eine Netzwerkfirewall oder ein NVA verläuft. Dieser Prozess unterscheidet sich bei den beiden Ansätzen. Wenn Sie Dienstendpunkte verwenden, sollten Sie den Dienstendpunkt im **Firewallsubnetz** konfigurieren und nicht im Subnetz, in dem der Quelldienst bereitgestellt wird. Wenn Sie private Endpunkte verwenden, legen Sie eine benutzerdefinierte Route (User Defined Route, UDR) für die IP-Adresse des privaten Endpunkts im **Quellsubnetz** an. Nicht im Subnetz des privaten Endpunkts.

Weitere Informationen zu den Unterschieden finden Sie in der folgenden Tabelle.

| Aspekt                                                                                                                                    | Dienstendpunkte                                                                                                           | Private Endpunkte                                                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Dienstumfang, auf dessen Ebene die Konfiguration gilt                                                                                                   | Gesamter Dienst (z. B. _alle_ SQL Server oder Speicherkonten _aller_ Kunden)                                      | Einzelne Instanz (z. B. eine bestimmte SQL Server-Instanz oder ein Speicherkonto, das _Sie_ besitzen)                                                                    |
|Integrierter Schutz vor Datenexfiltration: Möglichkeit des Verschiebens/ Kopierens von Daten aus einer geschützten PaaS-Ressource in eine andere ungeschützte PaaS-Ressource durch böswillige Insider| Nein | Ja|
|Privater Zugriff auf PaaS-Ressource in der lokalen Umgebung| Nein| Ja|
|Für Dienstzugriff erforderliche NSG-Konfiguration| Ja (mit Diensttags)| Nein|
| Der Dienst kann ohne öffentliche IP-Adresse erreicht werden.                                                                                       | Nein                                                                                                                          | Ja                                                                                                                                                               |
| Azure-zu-Azure-Datenverkehr verbleibt im Azure-Backbonenetzwerk.                                                                                       | Ja                                                                                                                         | Ja                                                                                                                                                               |
| Der Dienst kann seine öffentliche IP-Adresse deaktivieren.                                                                                                        | Nein                                                                                                                          | Ja                                                                                                                                                               |
| Sie können Datenverkehr aus einem Azure Virtual Network leicht einschränken.                                                                             | Ja (Zugriff aus bestimmten Subnetzen zulassen oder NSGs verwenden)                                                                   | Nein*                                                                                                                                                               |
| Sie können von lokalen Umgebungen (VPN/ExpressRoute) kommenden Datenverkehr einfach einschränken.                                                                           | Nicht verfügbar                                                                                                                       | Nein*                                                                                                                                                               |
| Erfordert DNS-Änderungen                                                                                                                             | Nein                                                                                                                          | Ja (siehe [DNS-Konfiguration](../private-link/private-endpoint-dns.md))                                                                 |
| Beeinflusst die Kosten Ihrer Lösung                                                                                                                | Nein                                                                                                                          | Ja (siehe [Preise für Private Link](https://azure.microsoft.com/pricing/details/private-link/))                                                                       |
| Wirkt sich auf die [zusammengesetzte SLA](/azure/architecture/framework/resiliency/business-metrics#composite-slas) Ihrer Lösung aus. | Nein                                                                                                                          | Ja (Der eigentliche Dienst der privaten Verbindung hat eine [SLA von 99,99 %](https://azure.microsoft.com/support/legal/sla/private-link/))                                                 |

*Alles, was über eine Netzwerk-Sichtverbindung zum privaten Endpunkt verfügt, hat Zugriff auf Netzwerkebene. Dieser Zugriff kann nicht von einer NSG auf dem privaten Endpunkt selbst gesteuert werden.

Azure-Dienstressourcen, die auf virtuelle Netzwerke beschränkt und so geschützt sind, sind über lokale Netzwerke nicht erreichbar. Wenn Sie Datenverkehr aus der lokalen Umgebung zulassen möchten, müssen Sie öffentliche IP-Adressen (meist NAT) aus der lokalen Umgebung bzw. per ExpressRoute zulassen. Diese IP-Adressen können über die Konfiguration der IP-Firewall für die Azure-Dienstressourcen hinzugefügt werden. Weitere Informationen finden Sie im [FAQ zu VNet](virtual-networks-faq.md#can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie Ihre [App in ein Azure-Netzwerk integrieren können](../app-service/overview-vnet-integration.md).
- Erfahren Sie, wie Sie den [Zugriff auf Ressourcen mithilfe von Diensttags einschränken](tutorial-restrict-network-access-to-resources.md) können.
- Erfahren Sie, wie Sie eine [private Verbindung mit einem Azure Cosmos-Konto mithilfe von Azure Private Link herstellen](../private-link/tutorial-private-endpoint-cosmosdb-portal.md).