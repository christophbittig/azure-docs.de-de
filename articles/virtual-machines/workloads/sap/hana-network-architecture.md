---
title: Netzwerkarchitektur von SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Lernen Sie die Netzwerkarchitektur für die Bereitstellung von SAP HANA in Azure (große Instanzen) kennen.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/21/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5add931f71dfdb5034e614b3d6c3ddc8703293a2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461570"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA-Netzwerkarchitektur (große Instanzen)

In diesem Artikel wird die Netzwerkarchitektur für die Bereitstellung von SAP HANA in Azure (große Instanzen) (auch als BareMetal-Infrastruktur bezeichnet) betrachtet. 

Die Architektur von Azure-Netzwerkdiensten ist eine wichtige Komponente der erfolgreichen Bereitstellung von SAP-Anwendungen unter HANA (große Instanz). Typischerweise weisen Bereitstellungen von SAP HANA in Azure (große Instanzen) eine größere SAP-Landschaft auf. Sie umfassen wahrscheinlich mehrere SAP-Lösungen mit unterschiedlichen Größen von Datenbanken, CPU-Ressourcenverbrauch und Arbeitsspeichernutzung. 

Die Wahrscheinlichkeit ist hoch, dass nicht alle IT-Systeme bereits unter Azure angeordnet sind. Ihre SAP-Landschaft kann auch hybrid sein. Ihr Datenbankverwaltungssystem (DBMS) und Ihre SAP-Anwendung können eine Mischung aus NetWeaver, S/4HANA und SAP HANA verwenden. Ihre SAP-Anwendung kann sogar ein anderes DBMS verwenden.

Azure verfügt über verschiedene Dienste, mit denen Sie die DBMS-, NetWeaver- und S/4HANA-System in Azure ausführen können. Azure verfügt über Netzwerktechnologie, damit Azure für Ihre lokalen Softwarebereitstellungen wie ein virtuelles Rechenzentrum aussieht. Die Azure-Netzwerkfunktionalität umfasst Folgendes: 

- Virtuelle Azure-Netzwerke, die mit der [ExpressRoute](https://azure.microsoft.com/services/expressroute/)-Leitung verbunden sind, die als Verbindung mit Ihren lokalen Netzwerkressourcen dient.
- Eine ExpressRoute-Leitung, die lokale Systeme mit Azure über eine Mindestbandbreite von [1 GBit/s](https://azure.microsoft.com/pricing/details/expressroute/) verbindet. Diese Leitung ist ausreichend für die Übertragung von Daten zwischen lokalen Systemen und auf virtuellen Computern (VMs) ausgeführten Systemen. Sie bietet auch eine angemessene Bandbreite für die Verbindung von lokalen Benutzern mit Azure-Systemen.
- Alle SAP-Systeme in Azure werden in virtuellen Netzwerken eingerichtet, um miteinander kommunizieren zu können.
- Die lokal gehostete Active Directory-Instanz und der lokale DNS-Dienst werden über ExpressRoute von der lokalen Umgebung auf Azure ausgedehnt. Sie können auch vollständig in Azure ausgeführt werden.

Wenn HANA (große Instanzen) in das Netzwerkfabric des Azure-Rechenzentrums integriert wird, wird auch Azure ExpressRoute-Technologie genutzt.


> [!NOTE] 
> Ein Azure-Abonnement kann nur mit einem einzelnen Mandanten in einem Umfeld von HANA (große Instanz) in einer bestimmten Azure-Region verknüpft werden. Umgekehrt kann ein einziger Mandant im Umfeld von HANA (große Instanz) nur mit einem Azure-Abonnement verknüpft werden. Diese Anforderung gilt auch für andere abrechenbare Objekte in Azure.

Wenn SAP HANA in Azure (große Instanzen) in mehreren Azure-Regionen bereitgestellt wird, wird ein separater Mandant im Umfeld von HANA (große Instanz) bereitgestellt. Sie können beide Mandanten im gleichen Azure-Abonnement ausführen, sofern diese Instanzen zur gleichen SAP-Landschaft gehören. 

> [!IMPORTANT] 
> Für SAP HANA in Azure (große Instanzen) wird nur die Azure Resource Manager-Bereitstellungsmethode unterstützt.

## <a name="extra-virtual-network-information"></a>Zusätzliche Informationen zum virtuellen Netzwerk

Zum Verbinden eines virtuellen Netzwerks mit ExpressRoute muss ein Azure ExpressRoute-Gateway erstellt werden. Weitere Informationen finden Sie unter [Informationen zu Gateways für virtuelle Netzwerke für ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md). 

Ein Azure ExpressRoute-Gateway wird mit einer ExpressRoute-Verbindung mit einer Infrastruktur außerhalb von Azure oder mit einem Azure-Umfeld der großen Instanz verwendet. Sie können das Azure ExpressRoute-Gateway mit bis zu vier ExpressRoute-Verbindungen verbinden, solange diese von unterschiedlichen Microsoft Enterprise Edge-Routern (MSEEs) stammen. Weitere Informationen finden Sie unter [Infrastruktur und Verbindungen mit SAP HANA in Azure (große Instanzen)](hana-overview-infrastructure-connectivity.md). 

> [!NOTE] 
> Der maximale Durchsatz, den Sie über eine ExpressRoute-Verbindung mit einem ExpressRoute-Gateway erreichen können, beträgt 10 GBit/s. Beim Kopieren von Dateien zwischen einer VM in einem virtuellen Netzwerk und einem lokalen System (als einzelner Kopierdatenstrom) wird nicht der volle Durchsatz der anderen Gateway-SKUs erzielt. Um die vollständige Bandbreite des ExpressRoute-Gateways zu nutzen, verwenden Sie mehrere Datenströme oder kopieren verschiedene Dateien in parallelen Datenströmen einer einzelnen Datei.


## <a name="networking-architecture-for-hana-large-instance"></a>Netzwerkarchitektur für HANA (große Instanz)
Die Netzwerkarchitektur für HANA (große Instanz) kann in vier Teile unterteilt werden:

- Lokales Netzwerk und ExpressRoute-Verbindung mit Azure. Dieser Teil ist Ihre Domäne (des Kunden) und wird über ExpressRoute mit Azure verbunden. Für diese ExpressRoute-Leitung übernehmen Sie die gesamten Kosten. Die Bandbreite sollte hoch genug sein, um den Netzwerkdatenverkehr zwischen Ihren lokalen Ressourcen und der Azure-Region, mit der Sie die Verbindung herstellen, verarbeiten zu können. Er ist unten rechts in der folgenden Abbildung dargestellt.
- Azure-Netzwerkdienste (wie zuvor erläutert) mit virtuellen Netzwerken, denen wiederum ExpressRoute-Gateways hinzugefügt werden müssen. Für diesen Teil müssen Sie die entsprechenden Entwürfe erstellen, um Ihre Anforderungen an Anwendung, Sicherheit und Compliance zu erfüllen. Überlegen Sie, ob Sie HANA (große Instanzen) angesichts der Anzahl der zur Auswahl stehenden virtuellen Netzwerke und Azure-Gateway-SKUs verwenden sollen. Dieser Teil ist oben rechts in der Abbildung dargestellt.
- Konnektivität von HANA (große Instanz) über ExpressRoute in Azure. Diesem Teil wird von Microsoft bereitgestellt und gehandhabt. Sie müssen lediglich nach der Bereitstellung Ihrer Ressourcen in HANA (große Instanz) einige IP-Adressbereiche angeben und die ExpressRoute-Leitung mit den virtuellen Netzwerken verbinden. Weitere Informationen finden Sie unter [Infrastruktur und Verbindungen mit SAP HANA in Azure (große Instanzen)](hana-overview-infrastructure-connectivity.md). Für Sie fällt keine zusätzliche Gebühr für die Konnektivität zwischen dem Netzwerkfabric des Azure-Rechenzentrums und HANA-Einheiten (große Instanzen) an.
- Netzwerk im Umfeld von HANA (große Instanz), das für Sie weitestgehend transparent ist.

![Mit SAP HANA in Azure (große Instanzen) und lokal verbundenes virtuelles Netzwerk](./media/hana-overview-architecture/image1-architecture.png)

Die folgenden beiden Anforderungen gelten weiterhin, obwohl Sie Hana (große Instanzen) verwenden:
- Ihre lokalen Ressourcen müssen über ExpressRoute eine Verbindung mit Azure herstellen.
- Sie benötigen mindestens ein virtuelles Netzwerk, in dem Ihre virtuellen Computer ausgeführt werden. Diese virtuellen Computer hosten die Anwendungsschicht, die eine Verbindung mit den HANA-Instanzen herstellt, die in HANA (große Instanzen) gehostet werden.

Für SAP-Bereitstellungen in Azure gelten folgende Unterschiede:

- HANA (große Instanzen) für Ihren Mandanten ist über eine andere ExpressRoute-Leitung mit Ihren virtuellen Netzwerken verbunden. Für die ExpressRoute-Leitungen von der lokalen Umgebung zum virtuellen Azure-Netzwerk und für die Leitungen zwischen virtuellen Azure-Netzwerken und HANA (große Instanzen) werden nicht dieselben Router gemeinsam genutzt. Die Lastbedingungen bleiben getrennt.
- Das Workloadprofil zwischen der SAP-Anwendungsschicht und HANA (große Instanzen) ist von anderer Natur. SAP HANA generiert viele kleine Anforderungen und Bursts wie Datenübertragungen (Resultsets) in die Anwendungsschicht.
- Die SAP-Anwendungsarchitektur ist empfindlicher gegenüber der Netzwerklatenz als typische Szenarien, in denen Daten zwischen lokalen Systemen und Azure ausgetauscht werden.
- Das Azure ExpressRoute-Gateway verfügt mindestens über zwei ExpressRoute-Verbindungen. Eine Verbindung der Leitung wird lokal und eine von HANA (große Instanzen) aus hergestellt. Diese Konfiguration lässt nur Platz für zwei weitere Leitungen von verschiedenen MSEEs, die eine Verbindung mit dem ExpressRoute-Gateway herstellen können. Diese Einschränkung gilt unabhängig von der Nutzung von ExpressRoute FastPath. Für alle verbundenen Leitungen wird die maximale Bandbreite für eingehende Daten des ExpressRoute-Gateways gemeinsam genutzt.

Mit Revision 3 der Stamps von SAP HANA (große Instanzen) kann die Netzwerklatenz zwischen VMs und Einheiten von SAP HANA (große Instanzen) höher sein als die typische Roundtriplatenzen zwischen VMs in einem Netzwerk. Je nach Azure-Region können die Werte die Roundtriplatenz von 0,7 ms übersteigen, die im [SAP-Hinweis 1100926 – Häufig gestellte Fragen: Netzwerkleistung](https://launchpad.support.sap.com/#/notes/1100926/E) als unterdurchschnittlich klassifiziert ist. Je nach Azure-Region und Tool zum Messen der Roundtriplatzen des Netzwerks zwischen einem virtuellen Azure-Computer und HANA (große Instanzen) kann die Latenz bis zu 2 Millisekunden betragen. Dennoch stellen Kunden SAP HANA-basierte SAP-Produktionsanwendungen erfolgreich auf SAP HANA (große Instanzen) bereit. Sie sollten Ihre Geschäftsprozesse unbedingt gründlich mit SAP HANA in Azure (große Instanzen) testen. Die neue Funktionalität „ExpressRoute FastPath“ kann die Netzwerklatenz zwischen HANA (große Instanzen) und VMs der Anwendungsschicht in Azure erheblich reduzieren (siehe unten). 

Revision 4 für Stamps von HANA (große Instanzen) verbessert die Netzwerklatenz zwischen Azure VMs, die in der Nähe des Stamps von HANA (große Instanzen) bereitgestellt werden. Die Latenz entspricht der Klassifizierung „durchschnittlich“ oder „überdurchschnittlich“, wie im [SAP-Hinweis 1100926 – Häufig gestellte Fragen: Netzwerkleistung](https://launchpad.support.sap.com/#/notes/1100926/E) dokumentiert, wenn Azure ExpressRoute FastPath konfiguriert ist (siehe unten). 

Um Azure-VMs in der Nähe zu Revision 4 von SAP HANA (große Instanzen) bereitzustellen, müssen Sie [Azure-Näherungsplatzierungsgruppen](../../co-location.md) anwenden. Näherungsplatzierungsgruppen können verwendet werden, um die SAP-Anwendungsschicht im selben Azure-Rechenzentrum zu platzieren wie die in Revision 4 gehosteten HANA-Einheiten (große Instanzen). Weitere Informationen finden Sie unter [Azure-Näherungsplatzierungsgruppen für optimale Netzwerklatenz bei SAP-Anwendungen](sap-proximity-placement-scenarios.md).

Die Verwendung der ExpressRoute-Gateway-SKU ist entscheidend, um eine deterministische Netzwerklatenz zwischen VMs und HANA (große Instanz) bereitzustellen. Im Gegensatz zu den Datenverkehrsmustern zwischen lokalen Systemen und VMs können die Datenverkehrsmuster zwischen VMs und HANA (große Instanzen) kleine, aber hohe Bursts von Anforderungen und Datenvolumen hervorbringen. Damit solche Bursts verarbeitet werden, empfehlen wir dringend die Verwendung der UltraPerformance-Gateway-SKU. Für die Typ-II-Klasse-SKUs von HANA (große Instanz) muss die UltraPerformance-Gateway-SKU als ExpressRoute-Gateway verwendet werden.

> [!IMPORTANT] 
> Beim gesamten Netzwerkdatenverkehr zwischen den SAP-Anwendungs- und Datenbankschichten werden zum Herstellen einer Verbindung mit SAP HANA in Azure (große Instanzen) nur die HighPerformance- oder UltraPerformance-Gateway-SKUs für virtuelle Netzwerke unterstützt. Für Typ II-SKUs von HANA (große Instanzen) wird nur die UltraPerformance-Gateway-SKU als ExpressRoute-Gateway unterstützt. Ausnahmen gelten bei Verwendung von ExpressRoute FastPath (siehe unten).

### <a name="expressroute-fastpath"></a>ExpressRoute FastPath
Im Mai 2019 haben wir ExpressRoute FastPath veröffentlicht. FastPath verringert die Latenz zwischen HANA (große Instanzen) und virtuellen Azure-Netzwerken, die die SAP-Anwendungs-VMs hosten. Mit FastPath werden die Datenflüsse zwischen VMs und HANA (große Instanzen) nicht über das ExpressRoute-Gateway geleitet. Die VMs, die in den Subnetzen des virtuellen Azure-Netzwerks zugewiesen werden, kommunizieren direkt mit dem dedizierten Unternehmensedgerouter. 

> [!IMPORTANT] 
> Für ExpressRoute FastPath ist es erforderlich, dass sich die Subnetze, in denen die VMs der SAP-Anwendungen ausgeführt werden, im selben virtuellen Azure-Netzwerk befinden, für das eine Verbindung mit HANA (große Instanzen) hergestellt wurde. VMs in virtuellen Azure-Netzwerken, für die ein Peering mit dem virtuellen Azure-Netzwerk besteht, das mit den HANA-Einheiten (große Instanz) verbunden ist, profitieren nicht von ExpressRoute FastPath. Für typische virtuelle Hub-and-Spoke-Netzwerke, in denen die ExpressRoute-Leitungen eine Verbindung mit einem virtuellen Hub-Netzwerk herstellen und für virtuelle Netzwerke mit der SAP-Anwendungsschicht ein Peering eingerichtet wird, funktioniert die Optimierung mit ExpressRoute FastPath daher nicht. ExpressRoute FastPath unterstützt derzeit auch keine benutzerdefinierten Routingregeln (UDR). Weitere Informationen finden Sie unter [ExpressRoute-Gateway für virtuelle Netzwerke und FastPath](../../../expressroute/expressroute-about-virtual-network-gateways.md). 


Weitere Informationen zur Konfiguration von ExpressRoute FastPath finden Sie unter [Verbinden eines virtuellen Netzwerks mit HANA (große Instanzen)](./hana-connect-vnet-express-route.md).    

> [!NOTE]
> Für die Verwendung von ExpressRoute FastPath ist ein ExpressRoute-Gateway vom Typ „UltraPerformance“ erforderlich.


## <a name="single-sap-system"></a>Einzelnes SAP-System

Die zuvor gezeigte lokale Infrastruktur ist über ExpressRoute mit Azure verbunden. Die ExpressRoute-Leitung stellt eine Verbindung mit MSEE her. Weitere Informationen finden Sie in der [technischen Übersicht über ExpressRoute](../../../expressroute/expressroute-introduction.md). Nachdem die Route eingerichtet wurde, wird eine Verbindung mit dem Azure-Backbone hergestellt.

> [!NOTE] 
> Stellen Sie zum Ausführen von SAP-Landschaften in Azure eine Verbindung mit dem Unternehmensedgerouter her, der der Azure-Region in der SAP-Landschaft am nächsten ist. Umfelder von HANA (große Instanz) werden über dedizierte Unternehmensedgerouter verbunden, um die Netzwerklatenz zwischen VMs in Azure-IaaS und HANA-Umfeldern (große Instanz) zu minimieren.

Das ExpressRoute-Gateway für die VMs, auf denen SAP-Anwendungsinstanzen gehostet werden, ist mit einer ExpressRoute-Leitung verbunden, über die eine Verbindung mit der lokalen Umgebung hergestellt wird. Das gleiche virtuelle Netzwerk ist mit einem separaten Unternehmensedgerouter verbunden. Dieser Edgerouter ist für das Herstellen einer Verbindung mit Stamps für große Instanzen dediziert. Auch bei FastPath wird der Datenfluss von HANA (große Instanzen) zu den VMs der SAP-Anwendungsschicht nicht über das ExpressRoute-Gateway geleitet. Diese Konfiguration reduziert die Roundtriplatenz für das Netzwerk.

Dieses System ist ein einfaches Beispiel für ein einzelnes SAP-System. Die SAP-Anwendungsschicht wird in Azure gehostet. Die SAP HANA-Datenbank wird in SAP HANA in Azure (große Instanzen) ausgeführt. Hierbei wird davon ausgegangen, dass die Bandbreite des ExpressRoute-Gateways mit einem Durchsatz von 2 GBit/s oder 10 GBit/s keinen Engpass darstellt.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Mehrere SAP-Systeme oder große SAP-Systeme

Wenn Sie mehrere SAP-Systeme oder große SAP-Systeme für die Verbindung mit SAP HANA (große Instanzen) bereitstellen, kann sich der Durchsatz des ExpressRoute-Gateways als Engpass erweisen. Teilen Sie in diesem Fall die Anwendungsschichten auf mehrere virtuelle Netzwerke auf. Sie können die Anwendungsschichten auch aufteilen, wenn Sie Produktions- und Nicht-Produktionssysteme in verschiedenen virtuellen Azure-Netzwerken isolieren möchten. 

In Fällen wie den folgenden können Sie ein spezielles virtuelles Netzwerk erstellen, das mit HANA (große Instanzen) verbunden wird:

- Direktes Ausführen von Sicherungen über die HANA-Instanzen in HANA (große Instanzen) auf einer VM, die NFS-Freigaben hostet.
- Kopieren großer Sicherungen oder anderer Dateien aus HANA (große Instanzen) auf Speicherplatz auf dem Datenträger, der in Azure verwaltet wird.

Verwenden Sie ein separates virtuelles Netzwerk zum Hosten von VMs, mit denen die Speicherung für die Massenübertragung von Daten zwischen HANA (große Instanzen) und Azure verwaltet wird. Diese Anordnung vermeidet große Datei- oder Datenübertragungen von HANA (große Instanzen) nach Azure über das ExpressRoute-Gateway, das die VMs bedient, auf denen die SAP-Anwendungsschicht ausgeführt wird. 

Gehen Sie für eine stärker erweiterbare Netzwerkarchitektur folgendermaßen vor:

- Verwenden Sie mehrere virtuelle Netzwerke für eine einzelne, größere SAP-Anwendungsebene.
- Stellen Sie für jedes bereitgestellte SAP-System ein separates virtuelles Netzwerk bereit, anstatt diese SAP-Systeme in separaten Subnetzen unter demselben virtuellen Netzwerk zu kombinieren.

    Das folgende Diagramm zeigt eine stärker erweiterbare Netzwerkarchitektur für SAP HANA in Azure (große Instanzen):

![Bereitstellen einer SAP-Anwendungsschicht über mehrere virtuelle Netzwerke](./media/hana-overview-architecture/image4-networking-architecture.png)

Je nach den Regeln und Einschränkungen, die Sie zwischen den verschiedenen virtuellen Netzwerken, in denen VMs unterschiedlicher SAP-Systeme gehostet werden, anwenden möchten, sollten Sie für diese virtuellen Netzwerke ein Peering einrichten. Weitere Informationen zum Peering virtueller Netzwerke finden Sie unter [Peering in virtuellen Netzwerken](../../../virtual-network/virtual-network-peering-overview.md).


## <a name="routing-in-azure"></a>Routing in Azure

Basierend auf der Standardbereitstellung gibt es drei wichtige Überlegungen zum Netzwerkrouting für SAP HANA in Azure (große Instanzen):

- SAP HANA in Azure (große Instanzen) ist nur über Azure-VMs und die dedizierte ExpressRoute-Verbindung und nicht direkt vom lokalen System aus zugänglich. Der direkte Zugriff vom lokalen System auf die Einheiten von HANA (große Instanz) ist in der von Microsoft bereitgestellten Konfiguration nicht sofort möglich. Die Einschränkungen beim transitiven Routing sind auf die aktuelle Azure-Netzwerkarchitektur zurückzuführen, die für SAP HANA (große Instanzen) verwendet wird. Manche Verwaltungsclients und alle Anwendungen, die direkten Zugriff erfordern (beispielsweise eine lokale Instanz von SAP Solution Manager), können keine Verbindung mit der SAP HANA-Datenbank herstellen. Informationen zu Ausnahmen finden Sie im Abschnitt [Direktes Routing zu HANA (große Instanzen)](#direct-routing-to-hana-large-instances).

- Wenn Sie für die Notfallwiederherstellung Einheiten von HANA (große Instanz) in zwei verschiedenen Azure-Regionen bereitgestellt haben, gelten die gleichen vorübergehenden Routingeinschränkungen wie in der Vergangenheit. Anders ausgedrückt: Die IP-Adressen einer Einheit von HANA (große Instanzen) in einer Region (z. B. „USA, Westen“) wurden nicht an HANA (große Instanzen) in einer anderen Region (z. B. „USA, Osten“) weitergeleitet. Diese Einschränkung gilt unabhängig von der Verwendung des Azure-Netzwerkpeerings zwischen Regionen oder der Querverbindung von ExpressRoute-Leitungen, die HANA (große Instanzen) mit virtuellen Netzwerken verbinden. Eine grafische Darstellung finden Sie in der Abbildung im Abschnitt [Verwenden von Einheiten von HANA (große Instanz) in mehreren Regionen](#use-hana-large-instance-units-in-multiple-regions). Diese Einschränkung, die auf die bereitgestellte Architektur zurückzuführen war, verhinderte die unmittelbare Verwendung der HANA-Systemreplikation zur Notfallwiederherstellung. Informationen zu kürzlich durchgeführten Änderungen finden Sie unter [Verwenden von Einheiten von HANA (große Instanz) in mehreren Regionen](#use-hana-large-instance-units-in-multiple-regions). 

- SAP HANA in Azure (große Instanzen) verfügt über eine zugewiesene IP-Adresse aus dem IP-Adressbereich des Serverpools, den Sie beim Anfordern der Bereitstellung von HANA (große Instanz) angegeben haben. Weitere Informationen finden Sie unter [Infrastruktur und Verbindungen mit SAP HANA in Azure (große Instanzen)](hana-overview-infrastructure-connectivity.md). Der Zugriff auf diese IP-Adresse ist über die Azure-Abonnements und die Leitung möglich, über die virtuelle Azure-Netzwerke mit HANA (große Instanzen) verbunden werden. Die zugewiesene IP-Adresse aus dem IP-Adressbereich des Serverpools wird der Hardwareeinheit direkt zugewiesen. Anders als bei den ersten Bereitstellungen dieser Lösung wird sie *nicht* mehr über Netzwerkadressenübersetzung (NAT) zugewiesen. 

### <a name="direct-routing-to-hana-large-instances"></a>Direktes Routing zu HANA (große Instanzen)

Standardmäßig funktioniert das transitive Routing in folgenden Szenarien nicht:

- Zwischen HANA-Einheiten (große Instanz) und einer lokalen Bereitstellung

- Zwischen HANA-Einheiten (große Instanz), die in verschiedenen Regionen bereitgestellt werden.

Es gibt drei Möglichkeiten, um transitives Routing in diesen Szenarien zu aktivieren:

- Ein Reverseproxy zum Weiterleiten von Daten in beide Richtungen. Beispiel: F5 BIG-IP, NGINX mit einer Bereitstellung von Traffic Manager im virtuellen Azure-Netzwerk, über das eine Verbindung mit HANA (große Instanzen) und mit der lokalen Umgebung als Lösung für eine virtuelle Firewall oder für das Datenverkehrsrouting hergestellt wird.
- IPTables-Regeln auf einer Linux-VM, um das Routing zwischen lokalen Standorten und Einheiten von HANA (große Instanz) oder zwischen Einheiten von HANA (große Instanz) in unterschiedlichen Regionen zu ermöglichen. Die VM, auf der IPTables ausgeführt wird, muss im virtuellen Azure-Netzwerk bereitgestellt werden, über das eine Verbindung mit HANA (große Instanzen) und der lokalen Umgebung hergestellt wird. Die Größe der VM muss angepasst werden, damit der Netzwerkdurchsatz der VM für den erwarteten Netzwerkdatenverkehr ausreicht. Weitere Informationen zur VM-Netzwerkbandbreite finden Sie im Artikel [Größen für virtuelle Linux-Computer in Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) ist eine weitere Lösung zur Aktivierung von direktem Datenverkehr zwischen der lokalen Umgebung und HANA-Einheiten (große Instanz). 

Der gesamte Datenverkehr dieser Lösungen wird über ein virtuelles Azure-Netzwerk geleitet. Daher kann der Datenverkehr auch durch die verwendeten Softwareappliances oder Azure-Netzwerksicherheitsgruppen eingeschränkt werden. Auf diese Weise kann der Zugriff auf HANA (große Instanzen) für bestimmte IP-Adressen oder IP-Adressbereiche lokal entweder gesperrt oder explizit gestattet werden. 

> [!NOTE]  
> Beachten Sie, dass Microsoft keine Implementierung und keinen Support für benutzerdefinierte Lösungen mit Netzwerkgeräten von Drittanbietern oder IPTables bietet. Der Support muss durch den Hersteller der verwendeten Komponente oder den Integrator bereitgestellt werden. 

#### <a name="express-route-global-reach"></a>Express Route Global Reach
Microsoft hat eine neue Funktionalität mit dem Namen [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) eingeführt. Global Reach kann für HANA (große Instanzen) in zwei Szenarien verwendet werden:

- Ermöglichen des direkten Zugriffs aus der lokalen Umgebung auf Ihre HANA-Einheiten (große Instanz) in unterschiedlichen Regionen.
- Ermöglichen der direkten Kommunikation zwischen Ihren HANA-Einheiten (große Instanz) in unterschiedlichen Regionen.


##### <a name="direct-access-from-on-premises"></a>Direktzugriff aus der lokalen Umgebung
In Azure-Regionen, in denen Global Reach angeboten wird, können Sie die Aktivierung von Global Reach für Ihre ExpressRoute-Leitung anfordern. Diese Leitung verbindet Ihr lokales Netzwerk mit dem virtuellen Azure-Netzwerk, das eine Verbindung mit HANA (große Instanzen) herstellt. Auf der lokalen Seite Ihrer ExpressRoute-Leitung fallen Kosten an. Weitere Informationen zu den Preisen für das Global Reach-Add-On finden Sie unter [Azure ExpressRoute – Preise](https://azure.microsoft.com/pricing/details/expressroute/). Sie zahlen keine zusätzlichen Kosten für die Leitung, die HANA (große Instanzen) mit Azure verbindet. 

> [!IMPORTANT]  
> Wenn Sie Global Reach verwenden, um den direkten Zugriff zwischen Ihren Einheiten von HANA (große Instanzen) und lokalen Ressourcen zu ermöglichen, werden die Netzwerkdaten und die Ablaufsteuerung **nicht über virtuelle Azure-Netzwerke weitergeleitet**. Stattdessen werden Netzwerkdaten und Ablaufsteuerung direkt zwischen den Microsoft Enterprise Exchange-Routern weitergeleitet. Dies führt dazu, dass alle NSG- oder ASG-Regeln und alle Arten von Firewall, NVA oder Proxy, die Sie in einem virtuellen Azure-Netzwerk bereitgestellt haben, nicht genutzt werden. **Wenn Sie ExpressRoute Global Reach zum Ermöglichen des Direktzugriffs aus der lokalen Umgebung auf HANA-Einheiten (große Instanz) verwenden, müssen Einschränkungen und Berechtigungen zum Zugreifen auf HANA-Einheiten (große Instanz) in Firewalls auf lokaler Seite definiert werden**. 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Verbinden von HANA (große Instanzen) in unterschiedlichen Azure-Regionen
Ähnlich kann ExpressRoute Global Reach zum Verbinden von zwei HANA-Mandanten (große Instanzen) in verschiedenen Regionen verwendet werden. Die Isolation erfolgt über die ExpressRoute-Leitungen, die von Ihren HANA-Mandanten (große Instanz) verwendet werden, um in beiden Regionen eine Verbindung mit Azure herzustellen. Es werden keine zusätzlichen Gebühren berechnet, um zwei HANA-Mandanten (große Instanz) zu verbinden, die in unterschiedlichen Regionen bereitgestellt werden. 

> [!IMPORTANT]  
> Der Datenfluss und die Ablaufsteuerung des Netzwerkdatenverkehrs zwischen den HANA-Mandanten (große Instanz) verlaufen nicht über Azure-Netzwerke. Sie können daher keine Azure-Funktionalität oder virtuelle Netzwerkgeräte (NVAs) nutzen, um zwischen Ihren HANA-Mandanten (große Instanzen) Einschränkungen der Kommunikation zu erzwingen. 

Weitere Informationen zum Aktivieren von ExpressRoute Global Reach finden Sie unter [Verbinden eines virtuellen Netzwerks mit HANA (große Instanzen)](./hana-connect-vnet-express-route.md).


## <a name="internet-connectivity-of-hana-large-instance"></a>Internetkonnektivität von HANA (große Instanz)
HANA (große Instanzen) besitzt *keine* direkte Internetkonnektivität. Daher können Sie das Betriebssystemimage möglicherweise nicht direkt beim Hersteller des Betriebssystems registrieren. Gegebenenfalls müssen Sie dazu den lokalen SUSE Linux Enterprise Server Subscription Management Tool-Server oder Red Hat Enterprise Linux Subscription Manager verwenden.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Datenverschlüsselung zwischen VMs und HANA (große Instanz)
Zwischen HANA (große Instanzen) und VMs übertragene Daten werden nicht verschlüsselt. Sie können für den Austausch zwischen der HANA DBMS-Seite und JDBC-/ODBC-basierten Anwendungen jedoch die Verschlüsselung des Datenverkehrs aktivieren. Weitere Informationen finden Sie unter [Sichere Kommunikation zwischen SAP HANA und JDBC-/ODBC-Clients.](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html)

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Verwenden von Einheiten von HANA (große Instanz) in mehreren Regionen

Für die Notfallwiederherstellung müssen Sie über Einheiten von HANA (große Instanzen) in mehreren Azure-Regionen verfügen. Mithilfe des [globalen VNet-Peerings](../../../virtual-network/virtual-network-peering-overview.md) von Azure funktioniert das transitive Routing zwischen HANA-Mandanten (große Instanzen) in verschiedenen Regionen standardmäßig nicht. Global Reach hingegen eröffnet die Kommunikation zwischen Einheiten von HANA (große Instanzen) in verschiedenen Regionen. Dieses Szenario ermöglicht Folgendes mithilfe von ExpressRoute Global Reach:

 - HANA-Systemreplikation ohne weitere Proxys oder Firewalls.
 - Kopieren von Sicherungen zwischen HANA-Einheiten (große Instanzen) in verschiedenen Regionen zum Erstellen von Systemkopien oder Durchführen von Systemaktualisierungen.


![Mit Azure-Umfeldern der großen Instanz in verschiedenen Azure-Regionen verbundene virtuelle Netzwerke](./media/hana-overview-architecture/image8-multiple-regions.png)

Die vorangehende Abbildung zeigt, wie die virtuellen Netzwerke in beiden Regionen mit zwei ExpressRoute-Leitungen verbunden sind. Die Leitungen werden für die Verbindung zu SAP HANA in Azure (große Instanzen) in beiden Azure-Regionen verwendet (graue Linien). Der Grund für die beiden Querverbindungen ist, dass ein Schutz vor dem Ausfall der MSEEs auf beiden Seiten vorhanden sein soll. Für den Kommunikationsfluss zwischen den beiden virtuellen Netzwerken in den beiden Azure-Regionen wird erwartet, dass er über das [globale Peering](/archive/blogs/azureedu/how-to-setup-global-vnet-peering-in-azure) der beiden virtuellen Netzwerke in den beiden unterschiedlichen Regionen verarbeitet wird (blaue gepunktete Linie). Die dichte rote Linie beschreibt die ExpressRoute Global Reach-Verbindung. Diese Verbindung ermöglicht es den Einheiten von HANA (große Instanzen) Ihrer Mandanten in verschiedenen Regionen, miteinander zu kommunizieren. 

> [!IMPORTANT] 
> Falls Sie mehrere ExpressRoute-Leitungen verwendet haben, verwenden Sie das Voranstellen von AS Path und lokale BGP-Einstellungen, um ein ordnungsgemäßes Routing des Datenverkehrs sicherzustellen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Speicherarchitektur von SAP HANA (große Instanzen).

> [!div class="nextstepaction"]
> [Speicherarchitektur von SAP HANA (große Instanzen)](hana-storage-architecture.md)
