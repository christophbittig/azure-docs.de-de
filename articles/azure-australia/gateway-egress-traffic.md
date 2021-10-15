---
title: Steuern von ausgehendem Datenverkehr für Azure Australien
description: Es werden die wichtigen Elemente der Steuerung von ausgehendem Datenverkehr in Azure beschrieben, um Anforderungen der australischen Behörden in Bezug auf sichere Internetgateways zu erfüllen.
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: yvettep
ms.openlocfilehash: 58fccb77131aac8311029cb06372201f2dd4b3bb
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359558"
---
# <a name="controlling-egress-traffic-in-azure-australia"></a>Steuern von ausgehendem Datenverkehr für Azure Australien

Eine grundlegende Komponente beim Schützen von ICT-Systemen ist die Steuerung des Netzwerkdatenverkehrs. Durch die Beschränkung der Kommunikation ausschließlich auf den Datenverkehr, der für den Betrieb des Systems erforderlich ist, wird das Risiko einer Kompromittierung gemindert. Transparenz und die Steuerung von externen Systemen, mit denen Ihre Anwendungen und Dienste kommunizieren, ermöglicht die Erkennung von kompromittierten Systemen und von versuchten oder erfolgreichen Datenexfiltrationen. Dieser Artikel enthält Informationen zu ausgehendem Netzwerkdatenverkehr in Azure und Empfehlungen zur Implementierung von Netzwerksicherheitskontrollen für ein mit dem Internet verbundenes System, das am Verbraucherleitfaden des Australian Cyber Security Centre (ACSC) und am Handbuch für Informationssicherheit (Information Security Manual, ISM) des ACSC ausgerichtet ist.

## <a name="requirements"></a>Requirements (Anforderungen)

Die allgemeinen Sicherheitsanforderungen für Systeme im Commonwealth sind im ISM festgelegt. Um Commonwealth-Entitäten bei der Implementierung der Netzwerksicherheit zu unterstützen, hat das ACSC _ACSC Protect: Implementieren der Netzwerksegmentierung und -trennung (Implementing Network Segmentation and Segregation)_ veröffentlicht, und als Hilfestellung beim Schützen von Systemen in Cloudumgebungen hat das ACSC das Dokument _Cloud Computing Security for Tenants (Cloud Computing-Sicherheit für Mandanten)_ veröffentlicht.

In den Dokumenten des ACSC wird der Kontext für die Implementierung der Netzwerksicherheit und die Steuerung des Datenverkehrs beschrieben, und sie enthalten praktische Empfehlungen für den Entwurf und die Konfiguration von Netzwerken.

In den ACSC-Dokumenten sind die unten angegebenen wichtigen Anforderungen zum Steuern des ausgehenden Datenverkehrs in Azure aufgeführt.

BESCHREIBUNG|`Source`
--------------- |------------------
**Implementieren der Netzwerksegmentierung und -trennung**, z. B. Verwenden einer n-schichtigen Architektur und Nutzen von hostbasierten Firewalls und Netzwerkzugriffssteuerungen zum Beschränken der ein- und ausgehenden Netzwerkkonnektivität auf die erforderlichen Ports und Protokolle.| [Cloud Computing for Tenants](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm) (Cloud Computing für Mandanten)
**Implementieren von ausreichend hoher Bandbreite, geringer Latenz und zuverlässiger Netzwerkkonnektivität** zwischen dem Mandanten (einschließlich der Remotebenutzer des Mandanten) und dem Clouddienst, um die Verfügbarkeitsanforderungen des Mandanten zu erfüllen.  | [ACSC Protect: Implementing Network Segmentation and Segregation (ACSC Protect: Implementieren der Netzwerksegmentierung und -trennung)](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Anwenden von Technologien nicht nur auf Netzwerkebene**. Jeder Host und jedes Netzwerk sollten nach Möglichkeit auf der niedrigsten Ebene, die eine gute Verwaltung ermöglicht, segmentiert und getrennt werden. In den meisten Fällen gilt dies von der Sicherungsschicht bis hinauf zur Anwendungsschicht (einschließlich). In empfindlichen Umgebungen kann eine physische Isolation ratsam sein. Hostbasierte und netzwerkweite Maßnahmen sollten sich gegenseitig ergänzen und zentral überwacht werden. Es reicht nicht aus, als Sicherheitsmaßnahme nur eine Firewall oder Sicherheitsappliance zu implementieren. |[ACSC Protect: Implementing Network Segmentation and Segregation (ACSC Protect: Implementieren der Netzwerksegmentierung und -trennung)](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Nutzen der Prinzipien der geringsten Rechte und der unerlässlichen Informationen**. Wenn ein Host, Dienst oder Netzwerk nicht unbedingt mit einem anderen Host, Dienst oder Netzwerk kommunizieren muss, sollte dies auch nicht zugelassen werden. Falls ein Host, Dienst oder Netzwerk über einen bestimmten Port oder ein Protokoll mit einem anderen Host, Dienst oder Netzwerk kommunizieren muss, sollte dies ausschließlich auf den jeweiligen Port bzw. das Protokoll beschränkt bleiben. Die Anwendung dieser Prinzipien in einem gesamten Netzwerk passt zur Beschränkung der Benutzerrechte und sorgt für eine erhebliche Verbesserung des gesamten Sicherheitsstatus der Umgebung. |[ACSC Protect: Implementing Network Segmentation and Segregation (ACSC Protect: Implementieren der Netzwerksegmentierung und -trennung)](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Trennen von Hosts und Netzwerken basierend auf ihrer Empfindlichkeit oder Wichtigkeit in Bezug auf Geschäftsvorgänge**. Dies kann die Verwendung verschiedener Hardware oder Plattformen umfassen, je nach den jeweiligen Sicherheitsklassifizierungen, Sicherheitsdomänen oder Verfügbarkeits- bzw. Integritätsanforderungen für bestimmte Hosts oder Netzwerke. Achten Sie vor allem darauf, dass Sie Verwaltungsnetzwerke trennen, und erwägen Sie die physische Isolation von Out-of-band-Verwaltungsnetzwerken für empfindliche Umgebungen. |[ACSC Protect: Implementing Network Segmentation and Segregation (ACSC Protect: Implementieren der Netzwerksegmentierung und -trennung)](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Identifizieren, Authentifizieren und Autorisieren des Zugriffs aller Entitäten auf alle anderen Entitäten**. Für alle Benutzer, Hosts und Dienste sollte der Zugriff auf alle anderen Benutzer, Hosts und Dienste nur auf die Elemente beschränkt werden, die zur Durchführung der zugeteilten Aufgaben oder Funktionen erforderlich sind. Alle Legacy- oder lokalen Dienste, durch die die Sicherheit von Identifizierungs-, Authentifizierungs- und Autorisierungsdiensten umgangen oder herabgestuft wird, sollten nach Möglichkeit deaktiviert werden, und die Nutzung sollte genau überwacht werden. |[ACSC Protect: Implementing Network Segmentation and Segregation (ACSC Protect: Implementieren der Netzwerksegmentierung und -trennung)](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Implementieren einer Positivliste für Netzwerkdatenverkehr anstelle einer Negativliste** Lassen Sie den Zugriff nur für unbedenklichen Netzwerkdatenverkehr zu (Datenverkehr, der identifiziert, authentifiziert und autorisiert wurde), anstatt den Zugriff für als schädlich bekannten Netzwerkdatenverkehr zu verweigern (z. B. per Blockade einer bestimmten Adresse oder eines Diensts). Positivlisten führen zu einer besseren Sicherheitsrichtlinie als Negativlistenlisten und ermöglichen eine erheblich bessere Erkennung und Bewertung von potenziellen Netzwerkangriffen. |[ACSC Protect: Implementing Network Segmentation and Segregation (ACSC Protect: Implementieren der Netzwerksegmentierung und -trennung)](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Definieren einer Positivlisten mit zulässigen Websites und Blockieren aller nicht aufgeführten Websites** Hierdurch wird eines der häufigsten Verfahren zur Datenübermittlung und -exfiltration, das von Angreifern genutzt wird, effektiv ausgeschlossen. Wenn für Benutzer die legitime Anforderung besteht, auf viele verschiedene Websites oder auf sich häufig ändernde Websites zugreifen zu müssen, sollten Sie den Aufwand für diese Implementierung berücksichtigen. Auch eine relativ tolerante Positivliste bietet eine bessere Sicherheit als die Verwendung von Negativlisten oder eine Vorgehensweise ganz ohne Einschränkungen, und gleichzeitig liegen die Implementierungskosten trotzdem niedriger. Ein Beispiel für eine tolerante Positivliste ist das Zulassen der gesamten Unterdomäne für Australien (*.au) oder der ersten 1.000 Websites der Alexa-Websiteliste (nach dem Herausfiltern von DDNS-Domänen (Dynamic Domain Name System) und anderen nicht geeigneten Domänen).| [Australian Government Information Security Manual (ISM)](https://www.cyber.gov.au/ism) (Handbuch zur Informationssicherheit der australischen Regierung)
|

Dieser Artikel enthält Informationen und Empfehlungen dazu, wie Netzwerkdatenverkehr gesteuert wird, der Ihre Azure-Umgebung verlässt. Es werden Systeme beschrieben, die per Infrastructure-as-a-Service (IaaS) und Platform-as-a-Service (PaaS) in Azure bereitgestellt werden.

Im Artikel zu [eingehendem Datenverkehr über das Gateway](gateway-ingress-traffic.md) wird Netzwerkdatenverkehr beschrieben, der in Ihre Azure-Umgebung gelangt. Dies ist der Begleitartikel zu diesem Artikel, mit dem die Steuerung von Netzwerken vollständig abgedeckt wird.

## <a name="architecture"></a>Aufbau

Zur richtigen Steuerung des ausgehenden Datenverkehrs müssen Sie sich beim Entwerfen und Implementieren der Netzwerksicherheit zuerst damit vertraut machen, wie ausgehender Datenverkehr in Azure für IaaS und PaaS funktioniert. Dieser Abschnitt enthält eine Übersicht darüber, wie die Verarbeitung von ausgehendem Datenverkehr erfolgt, der von einer in Azure gehosteten Ressource generiert wird. Außerdem werden die Sicherheitskontrollen beschrieben, die zur Beschränkung und Steuerung dieses Datenverkehrs verfügbar sind.

### <a name="architecture-components"></a>Komponenten der Architektur

In diesem Architekturdiagramm sind die möglichen Pfade dargestellt, die für Netzwerkdatenverkehr beim Verlassen eines Systems verwendet werden können, das in einem Subnetz eines virtuellen Netzwerks bereitgestellt wurde. Der Datenverkehr in einem virtuellen Netzwerk wird auf der Subnetzebene verwaltet und gesteuert, die die Routing- und Sicherheitsregeln für die entsprechenden Ressourcen enthält. Die Komponenten für ausgehenden Datenverkehr teilen sich in Systeme, effektive Routen, Typen des nächsten Hops, Sicherheitskontrollen und PaaS-Ausgang auf.

![Aufbau](media/egress-traffic.png)

### <a name="systems"></a>Systeme

Systeme sind die Azure-Ressourcen und zugehörigen Komponenten, die ausgehenden Datenverkehr in einem IP-Subnetz generieren, das Teil eines virtuellen Netzwerks ist.

| Komponente | BESCHREIBUNG |
| --- | ---|
|Virtual Network (VNet) | Ein VNET ist in Azure eine grundlegende Ressource, die als Plattform und Grenze für die Bereitstellung von Ressourcen und die Aktivierung der Kommunikation dient. Das VNET befindet sich in einer Azure-Region und definiert den IP-Adressraum und die Routinggrenzen für in das VNET integrierte Ressourcen, z. B. virtuelle Computer.|
|Subnet | Ein Subnetz ist ein IP-Adressbereich, der in einem VNET erstellt wird. Mehrere Subnetze können innerhalb eines VNET erstellt werden, um eine Netzwerksegmentierung zu erzielen.|
|Netzwerkschnittstelle| Eine Netzwerkschnittstelle ist eine Ressource, die in Azure vorhanden ist. Sie wird an einen virtuellen Computer angefügt. Außerdem wird ihr aus dem Subnetz, dem sie zugeordnet ist, eine private IP-Adresse zugewiesen, die nicht über das Internet geroutet werden kann. Diese IP-Adresse wird über den Azure Resource Manager dynamisch oder statisch zugewiesen.|
|Öffentliche IP-Adressen| Eine öffentliche IP-Adresse ist eine Ressource, bei der eine im Besitz von Microsoft befindliche öffentliche IP-Adresse, die über das Internet geroutet werden kann, aus der angegebenen Region für die Verwendung im virtuellen Netzwerk reserviert wird. Die IP-Adresse kann einer bestimmten Netzwerkschnittstelle oder PaaS-Ressource zugeordnet werden, damit die Ressource mit dem Internet, ExpressRoute und anderen PaaS-Systemen kommunizieren kann.|
|

### <a name="routes"></a>Routen

Der Pfad, der von ausgehendem Datenverkehr genutzt wird, hängt von den effektiven Routen für diese Ressource ab. Dies ist der sich ergebende Routensatz, der durch die Kombination der Routen aller möglichen Quellen und der Anwendung der Azure-Routinglogik bestimmt wird.

| Komponente | BESCHREIBUNG |
|--- | ---|
|Systemrouten| Azure erstellt automatisch Systemrouten und weist die Routen allen Subnetzen eines virtuellen Netzwerks zu. Systemrouten können nicht erstellt oder entfernt werden, aber einige können durch benutzerdefinierte Routen überschrieben werden. Azure erstellt Standardsystemrouten für jedes Subnetz und fügt zusätzliche optionale Standardrouten für spezifische Subnetze hinzu (bzw. jedes Subnetz, wenn bestimmte Azure-Funktionen genutzt werden).|
|Dienstendpunkte| Dienstendpunkte stellen eine direkte private ausgehende Verbindung von einem Subnetz zu einer bestimmten PaaS-Funktion dar. Dienstendpunkte, die nur für einen Teil der PaaS-Funktionen verfügbar sind, ermöglichen eine bessere Leistung und Sicherheit für Ressourcen in einem VNET mit Zugriff auf PaaS.|
|Routingtabellen| Eine Routingtabelle ist eine Ressource in Azure, die erstellt werden kann, um benutzerdefinierte Routen (User-Defined Routes, UDRs) anzugeben. Hiermit können Systemrouten oder per Border Gateway Protocol ermittelte Routen erweitert oder überschrieben werden. Mit jeder UDR werden ein Netzwerk, eine Netzwerkmaske und ein nächster Hop angegeben. Eine Routingtabelle kann einem Subnetz zugeordnet werden. Es ist auch möglich, dieselbe Routingtabelle mehreren Subnetzen zuzuordnen, aber ein Subnetz kann nur über maximal eine Routingtabelle verfügen.|
|Border Gateway Protocol (BGP)| BGP ist ein interautonomes Systemroutingprotokoll. Ein autonomes System ist ein Netzwerk oder eine Gruppe von Netzwerken unter gemeinsamer Verwaltung und mit gemeinsamen Routingrichtlinien. BGP wird genutzt, um Routinginformationen zwischen autonomen Systemen auszutauschen. BGP kann über Gateways für virtuelle Netzwerke in virtuelle Netzwerke integriert werden.|
|

### <a name="next-hop-types-defined"></a>Definition: Typen des nächsten Hops

Jede Route in Azure umfasst den Netzwerkbereich sowie die zugeordnete Subnetzmaske und den nächsten Hop, um zu bestimmen, wie der Datenverkehr verarbeitet wird.

Typ des nächsten Hops | BESCHREIBUNG
---- | ----
**Virtual Network** | Leitet Datenverkehr zwischen Adressbereichen im Adressraum eines virtuellen Netzwerks weiter. Azure erstellt eine Route mit einem Adresspräfix, das jeweils dem Adressbereich entspricht, der im Adressraum eines virtuellen Netzwerks definiert ist. Wenn für den Adressraum des virtuellen Netzwerks mehrere Adressbereiche definiert sind, erstellt Azure für jeden Adressbereich eine gesonderte Route. Azure leitet Datenverkehr automatisch zwischen Subnetzen eines VNET weiter, indem die Routen verwendet werden, die für jeden Adressbereich erstellt wurden.
**VNet-Peering** | Wenn ein VNET-Peering zwischen zwei virtuellen Netzwerken erstellt wird, wird für jeden Adressbereich jedes virtuellen Netzwerks eine Route zu dem virtuellen Netzwerk hinzugefügt, mit dem das Peering besteht. Der Datenverkehr wird zwischen den per Peering verbundenen virtuellen Netzwerken genauso wie für Subnetze eines virtuellen Netzwerks weitergeleitet.
**Gateway für virtuelle Netzwerke** | Mindestens eine Route mit Angabe eines Gateways für virtuelle Netzwerke als Typ des nächsten Hops wird hinzugefügt, wenn einem virtuellen Netzwerk ein Gateway dieser Art hinzugefügt wird. Es sind die Routen enthalten, die in der Gatewayressource des lokalen Netzwerks konfiguriert sind, sowie alle per BGP ermittelten Routen.
**Virtuelles Gerät** | Ein virtuelles Gerät führt normalerweise eine Netzwerkanwendung aus, z. B. eine Firewall. Das virtuelle Gerät ermöglicht eine zusätzliche Verarbeitung des Datenverkehrs, z. B. Filterung, Untersuchung oder Adressübersetzung. Für jede Route mit einem virtuellen Gerät als Typ des Hops muss auch eine IP-Adresse für den nächsten Hop angegeben werden.
**VirtualNetworkServiceEndpoint** | Die öffentlichen IP-Adressen für einen bestimmten Dienst werden einem Subnetz mit „VirtualNetworkServiceEndpoint“ als nächstem Hop als Routen hinzugefügt, wenn ein Dienstendpunkt aktiviert wird. Dienstendpunkte werden für einzelne Dienste in den einzelnen Subnetzen eines virtuellen Netzwerks aktiviert. Die öffentlichen IP-Adressen von Azure-Diensten ändern sich regelmäßig. Azure verwaltet die Adressen in der Routentabelle automatisch, wenn sich die Adressen ändern.
**Internet** | Datenverkehr mit „Internet“ als Typ des nächsten Hops verlässt das virtuelle Netzwerk und wird automatisch in eine öffentliche IP-Adresse übersetzt – entweder über einen dynamischen Pool, der in der zugeordneten Azure-Region verfügbar ist, oder mit einer öffentlichen IP-Adresse, die für diese Ressource konfiguriert ist. Wenn die Zieladresse für einen der Azure-Dienste gilt, wird der Datenverkehr über das Azure-Backbonenetzwerk direkt an den Dienst weitergeleitet (und nicht ins Internet). Der Datenverkehr zwischen Azure-Diensten wird nicht über das Internet übertragen. Dies gilt unabhängig davon, in welcher Azure-Region das virtuelle Netzwerk vorhanden ist oder in welcher Azure-Region eine Instanz des Azure-Diensts bereitgestellt wird.
**None** | Datenverkehr mit „Keine“ als nächstem Hop wird gelöscht. Azure erstellt Standardrouten des Systems für reservierte Adresspräfixe mit „Keine“ als Typ des nächsten Hops. Routen mit „Keine“ als nächstem Hop können auch über Routingtabellen hinzugefügt werden, um zu verhindern, dass Datenverkehr an bestimmte Netzwerke geleitet wird.
|

### <a name="security-controls"></a>Sicherheitskontrollen

Control | BESCHREIBUNG
----- | -----
**Netzwerksicherheitsgruppen (NSGs)** | Mit NSGs wird der Datenverkehr gesteuert, der in Azure in und aus VNET-Ressourcen fließt. NSGs wenden Regeln für die Datenverkehrsflüsse an, die darüber zugelassen oder verweigert werden. Dies umfasst auch Datenverkehr in Azure und zwischen Azure und externen Netzwerken, z. B. lokal oder im Internet. NSGs werden auf Subnetze in einem virtuellen Netzwerk oder auf einzelne Netzwerkschnittstellen angewendet.
**Azure Firewall** | Azure Firewall ist ein verwalteter, cloudbasierter Netzwerksicherheitsdienst zum Schützen Ihrer Ressourcen des virtuellen Azure-Netzwerks. Es ist eine vollständig zustandsbehaftete Firewall-as-a-Service-Lösung mit integrierter Hochverfügbarkeit und uneingeschränkter Cloudskalierbarkeit. Azure Firewall kann mit herkömmlichen Netzwerkfilterregeln basierend auf IP-Adressen, Protokollen und Ports konfiguriert werden, aber es werden auch die Filterung basierend auf vollqualifizierten Domänennamen (FQDNs), Diensttags und integrierte Threat Intelligence unterstützt.
**Virtuelles Netzwerkgerät** | Virtuelle Netzwerkgeräte sind VM-Medien, mit denen für Azure Netzwerk-, Sicherheits- und andere Funktionen bereitgestellt werden können. Virtuelle Netzwerkgeräte unterstützen Netzwerkfunktionen und -dienste in Form von virtuellen Computern (VMs) in virtuellen Netzwerken und Bereitstellungen. Virtuelle Netzwerkgeräte können zur Erfüllung bestimmter Anforderungen, zur Integration in Verwaltungs- und betriebsbezogene Tools oder zur Erzielung von Einheitlichkeit mit vorhandenen Produkten verwendet werden. Azure unterstützt eine umfangreiche Palette an virtuellen Netzwerkgeräten anderer Anbieter, z. B. Webanwendungsfirewalls (WAF), Firewalls, Gateways/Router, Application Delivery Controller (ADC) und WAN-Optimierer.
**Dienstendpunkt-Richtlinien (Vorschauversion)** | Richtlinien für VNET-Dienstendpunkte ermöglichen es Ihnen, virtuellen Netzwerkdatenverkehr über Dienstendpunkte für Azure-Dienste zu filtern, sodass nur bestimmte Azure-Dienstressourcen zugelassen werden. Endpunktrichtlinien bieten eine differenzierte Zugriffssteuerung für virtuellen Netzwerkdatenverkehr zu Azure-Diensten.
**Azure Policy** | Azure Policy ist ein Dienst zum Erstellen, Zuweisen und Verwalten von Richtlinien in Azure. Für diese Richtlinien werden Regeln zum Steuern der Arten von Ressourcen, die bereitgestellt werden können, und der Konfiguration dieser Ressourcen verwendet. Richtlinien können zur Erzwingung von Konformität eingesetzt werden, indem die Bereitstellung von Ressourcen verhindert wird, falls sie die Anforderungen nicht erfüllen, oder zur Überwachung, um den Konformitätsstatus melden zu können.
|

### <a name="paas-egress"></a>Ausgehender PaaS-Datenverkehr

Die meisten PaaS-Ressourcen generieren keinen ausgehenden Datenverkehr, sondern reagieren entweder auf eingehende Anforderungen (z. B. Application Gateway, Storage, SQL-Datenbank usw.) oder leiten Daten von anderen Ressourcen weiter (z. B. Service Bus und Azure Relay). Die Netzwerkkommunikation zwischen PaaS-Ressourcen, z. B. von App Services zu Storage oder SQL-Datenbanken, wird von Azure gesteuert und ist darin enthalten. Sie wird mit Identitätskontrollen und anderen Kontrollen der Ressourcenkonfiguration geschützt, anstatt per Netzwerksegmentierung oder -trennung.

In einem virtuellen Netzwerk bereitgestellte PaaS-Ressourcen erhalten dedizierte IP-Adressen und unterliegen den Routingkontrollen und NSGs, wie dies auch für andere Ressourcen im virtuellen Netzwerk der Fall ist. Für PaaS-Ressourcen, die nicht in einem virtuellen Netzwerk vorhanden sind, wird ein Pool mit IP-Adressen verwendet, die für alle Instanzen der Ressource genutzt werden. Sie werden entweder über die Microsoft-Dokumentation veröffentlicht oder können per Azure Resource Manager ermittelt werden.

## <a name="general-guidance"></a>Allgemeine Hinweise

Für das Entwerfen und Erstellen sicherer Lösungen in Azure ist es wichtig, dass Sie mit dem Netzwerkdatenverkehr vertraut sind und ihn kontrollieren, damit nur die identifizierte und autorisierte Kommunikation möglich ist. In dieser Anleitung und den Anleitungen zu den einzelnen Komponenten in den Abschnitten weiter unten werden die Tools und Dienste beschrieben, die zum Anwenden der Prinzipien unter [ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) über Azure-Workloads hinweg genutzt werden können. Es wird auch beschrieben, wie Sie eine virtuelle Architektur zum Schützen von Ressourcen erstellen, wenn nicht die gleichen herkömmlichen physischen und Netzwerkkontrollen angewendet werden können, die in einer lokalen Umgebung möglich sind.

### <a name="guidance"></a>Leitfaden

* Beschränken der Anzahl von Ausgangspunkten für virtuelle Netzwerke
* Außerkraftsetzen der Standardroute des Systems für alle Subnetze, für die keine direkte ausgehende Kommunikation mit dem Internet erforderlich ist
* Entwerfen und Implementieren einer vollständigen Netzwerkarchitektur zum Identifizieren und Steuern aller Eingangs- und Ausgangspunkte für Azure-Ressourcen
* Verwenden eines Hub-and-Spoke-Netzwerkentwurfs für virtuelle Netzwerke (wie in der Microsoft VDC-Dokumentation (Virtual Data Center) beschrieben)
* Nutzen von Produkten mit integrierten Sicherheitsfunktionen für ausgehende Verbindungen mit dem Internet (z. B. Azure Firewall, virtuelle Netzwerkgeräte oder Webproxys)
* Verwenden von Identitätskontrollen, z. B. rollenbasierte Zugriffssteuerung von Azure, bedingter Zugriff und Multi-Factor Authentication (MFA, mehrstufige Authentifizierung) zur Beschränkung von Berechtigungen für die Netzwerkkonfiguration
* Implementieren von Sperren zur Verhinderung von Änderungen oder Löschungen wichtiger Elemente der Netzwerkkonfiguration
* Bereitstellen von PaaS in einer integrierten VNET-Konfiguration zur Erzielung einer besseren Trennung und Kontrolle
* Implementieren einer ExpressRoute-Leitung für die Konnektivität mit lokalen Netzwerken
* Implementieren von VPNs für die Integration in externe Netzwerke
* Nutzen von Azure Policy zum Beschränken auf die Regionen und Ressourcen, die für die Funktionalität des Systems benötigt werden
* Nutzen von Azure Policy zur Erzwingung der Baseline-Sicherheitskonfiguration für Ressourcen
* Nutzen von Network Watcher und Azure Monitor zum Protokollieren, Überprüfen und Sicherstellen der Sichtbarkeit von Netzwerkdatenverkehr in Azure

### <a name="resources"></a>Ressourcen

Element | Link
-----------| ---------
_Dokumente zur Konformität mit Bestimmungen und Richtlinien für Australien mit Verbraucherleitfaden_ | [https://aka.ms/au-irap](https://aka.ms/au-irap)
_Virtuelles Azure-Rechenzentrum_ | [https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](/azure/architecture/vdc/networking-virtual-datacenter)
_ACSC-Netzwerksegmentierung_ | [https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
_ACSC-Cloudsicherheit für Mandanten_ | [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
_ACSC Information Security Manual (ACSC-Handbuch zur Informationssicherheit)_ | [https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)
|

## <a name="component-guidance"></a>Leitfaden zu den Komponenten

Dieser Abschnitt enthält weitere Informationen zu den einzelnen Komponenten, die für ausgehenden Datenverkehr von in Azure bereitgestellten Systemen relevant sind. In jedem Abschnitt wird der Zweck der jeweiligen Komponente beschrieben, und es sind Links zur Dokumentation und zu Konfigurationsanleitungen vorhanden, die als Hilfe beim Entwerfen und Erstellen von Aktivitäten dienen können.

### <a name="systems-security"></a>Sicherheit von Systemen

Die gesamte Kommunikation mit Ressourcen in Azure verläuft über die von Microsoft verwaltete Netzwerkinfrastruktur, über die Konnektivität und Sicherheitsfunktionen bereitgestellt werden. Von Microsoft werden automatisch einige Schutzmaßnahmen getroffen, um die Azure Platform und die Netzwerkinfrastruktur zu schützen. Weitere Funktionen sind in Azure als Dienste verfügbar, mit denen der Netzwerkdatenverkehr kontrolliert und die Netzwerksegmentierung und -trennung ermöglicht werden kann.

### <a name="virtual-network-vnet"></a>Virtual Network (VNet)

Virtuelle Netzwerke sind einer der grundlegenden Bausteine für Netzwerke in Azure. Mit virtuellen Netzwerken werden ein IP-Adressraum und eine Routinggrenze definiert, die in verschiedenen Systemen genutzt werden können. Virtuelle Netzwerke sind in Subnetze unterteilt, und alle Subnetze eines virtuellen Netzwerks verfügen untereinander über eine gemeinsame direkte Netzwerkroute. Bei Nutzung von Gateways für virtuelle Netzwerke (ExpressRoute oder VPN) können Systeme in einem virtuellen Netzwerk in lokale und externe Umgebungen integriert werden, und über die von Azure bereitgestellte Netzwerkadressenübersetzung (Network Address Translation, NAT) und die Zuteilung öffentlicher IP-Adressen können Systeme eine Verbindung mit dem Internet oder anderen Azure-Regionen und -Diensten herstellen. Die Vertrautheit mit virtuellen Netzwerken und der zugehörigen Konfigurationsparameter und des Routings ist von entscheidender Bedeutung, wenn es darum geht, ausgehenden Netzwerkdatenverkehr zu verstehen und zu steuern.

Da virtuelle Netzwerke den grundlegenden Adressraum und die Routinggrenze in Azure darstellen, können sie als primärer Baustein für die Netzwerksegmentierung und -trennung verwendet werden.

| Resource | Link |
| --- | --- |
| *Übersicht über virtuelle Netzwerke* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](../virtual-network/virtual-networks-overview.md) |
| *Planen virtueller Netzwerke: Schrittanleitung*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](../virtual-network/virtual-network-vnet-plan-design-arm.md) |
| *Erstellen eines virtuellen Netzwerks: Schnellstart* | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](../virtual-network/quick-create-portal.md)
|

### <a name="subnet"></a>Subnet

Subnetze sind für die Netzwerksegmentierung und -trennung in Azure eine wichtige Komponente. Subnetze können verwendet werden, um die Trennung zwischen Systemen zu ermöglichen. Ein Subnetz ist die Ressource innerhalb eines virtuellen Netzwerks, über die Netzwerksicherheitsgruppen, Routingtabellen und Dienstendpunkte angewendet werden. Subnetze können sowohl als Quell- als auch als Zieladressen für Firewallregeln und Zugriffssteuerungslisten verwendet werden.

Die Subnetze in einem virtuellen Netzwerk sollten geplant werden, um die Anforderungen von Workloads und Systemen zu erfüllen. Personen, die am Entwurf und der Implementierung von Subnetzen beteiligt sind, sollten die ACSC-Richtlinien für die Netzwerksegmentierung lesen, um zu erfahren, wie Systeme in einem Subnetz gruppiert werden sollten.

|Resource|Link|
|---|---|
|*Hinzufügen, Ändern oder Löschen von Subnetzen virtueller Netzwerke* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](../virtual-network/virtual-network-manage-subnet.md)
|

### <a name="network-interface"></a>Netzwerkschnittstelle

Netzwerkschnittstellen sind die Quelle des gesamten ausgehenden Datenverkehrs eines virtuellen Computers. Netzwerkschnittstellen ermöglichen die Konfiguration von IP-Adressen und können genutzt werden, um Netzwerksicherheitsgruppen anzuwenden oder Datenverkehr über ein virtuelles Netzwerkgerät zu leiten. Die Netzwerkschnittstellen für virtuelle Maschinen sollten so geplant und konfiguriert werden, dass sie an allgemeinen Zielen für die Netzwerksegmentierung und -trennung ausgerichtet sind.

|Resource|Link|
|---|---|
|*Erstellen, Ändern oder Löschen von Netzwerkschnittstellen* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](../virtual-network/virtual-network-network-interface.md) |
|*IP-Adressierung für Netzwerkschnittstellen*               | [https://docs.microsoft.com/azure/virtual-network/private-ip-addresses](../virtual-network/private-ip-addresses.md)
|

### <a name="vnet-integrated-paas"></a>PaaS mit VNET-Integration

Mit PaaS kann eine bessere Funktionalität und Verfügbarkeit erzielt und der Verwaltungsaufwand reduziert werden, aber es müssen angemessene Schutzmaßnahmen getroffen werden. Verstärken Sie zu Erhöhung der Kontrolle die Netzwerksegmentierung. Es können auch viele PaaS-Funktionen in ein virtuelles Netzwerk integriert werden, um einen sicheren Ausgangspunkt für Anwendungen und Dienste zu schaffen.

Durch die Integration von PaaS in eine System- oder Anwendungsarchitektur können von Microsoft mehrere Mechanismen zum Bereitstellen von PaaS in einem virtuellen Netzwerk angeboten werden. Die Bereitstellungsmethodik kann zum Beschränken des Zugriffs beitragen und sorgt für Konnektivität und Integration in interne Systeme und Anwendungen. Beispiele hierfür sind App Service-Umgebungen, verwaltete SQL-Instanzen usw.

Beim Bereitstellen von PaaS in einem virtuellen Netzwerk, für das Routing- und NSG-Kontrollen implementiert wurden, ist es entscheidend, dass Sie mit den spezifischen Kommunikationsanforderungen der Ressource vertraut sind. Hierzu gehören auch der Verwaltungszugriff von Microsoft-Diensten und der Pfad, der vom Kommunikationsdatenverkehr genutzt wird, wenn auf eingehende Anforderungen dieser Dienste geantwortet wird.

| Resource  | Link  |
| --- | --- |
| *Integration virtueller Netzwerke für Azure-Dienste* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](../virtual-network/virtual-network-for-azure-services.md) |
| *Integrieren Ihrer App in ein virtuelles Azure-Netzwerk: Schrittanleitung* | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](../app-service/web-sites-integrate-with-vnet.md)
|

### <a name="public-ip"></a>Öffentliche IP-Adresse

Öffentliche IP-Adressen werden bei der Kommunikation außerhalb eines virtuellen Netzwerks verwendet. Dies umfasst auch PaaS-Ressourcen und alle Routen mit „Internet“ als nächstem Hop. Commonwealth-Entitäten sollten die Zuordnung von öffentlichen IP-Adressen sorgfältig planen und diese nur Ressourcen zuordnen, wenn dies wirklich erforderlich ist. Die allgemeine Vorgehensweise beim Entwerfen besteht darin, öffentliche IP-Adressen kontrollierten Ausgangspunkten für das virtuelle Netzwerk zuzuordnen, z. B. Azure Firewall, VPN Gateway oder virtuelle Netzwerkgeräte.

|Resource|Link|
|---|---|
|*Öffentliche IP-Adressen: Übersicht*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](../virtual-network/public-ip-addresses.md#public-ip-addresses) |
|*Erstellen, Ändern oder Löschen einer öffentlichen IP-Adresse* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](../virtual-network/virtual-network-public-ip-address.md)
|

## <a name="effective-routes"></a>Effektive Routen

Die effektiven Routen sind der sich ergebende Routensatz, der anhand der Kombination aus Systemrouten, Dienstendpunkten, Routingtabellen und BGP sowie der Anwendung der Routinglogik bestimmt wird. Wenn Datenverkehr in ausgehender Richtung aus einem Subnetz gesendet wird, wählt Azure eine Route basierend auf der IP-Zieladresse aus, indem der längste Präfixübereinstimmungsalgorithmus verwendet wird. Wenn mehrere Routen das gleiche Präfix enthalten, wählt Azure den Routentyp nach den folgenden Prioritätsregeln aus:

1. Benutzerdefinierte Route
2. BGP-Route
3. Systemroute

Es ist wichtig, dass Sie Folgendes beachten: Systemrouten für Datenverkehr im Zusammenhang mit virtuellen Netzwerken, VNET-Peerings oder VNET-Dienstendpunkten sind die bevorzugten Routen, auch wenn BGP-Routen spezifischer sind.

Personen, die am Entwurf oder der Implementierung von Routingtopologien in Azure beteiligt sind, sollten damit vertraut sein, wie Datenverkehr von Azure weitergeleitet wird. Sie sollten eine Architektur entwickeln, in der eine gute Balance zwischen den erforderlichen Funktionen der Systeme und der erforderlichen Sicherheit und Transparenz besteht. Die Umgebung sollte sorgfältig geplant werden, um übermäßige Eingriffe und Ausnahmen in Bezug auf das Routingverhalten zu vermeiden, da dies zu einer erhöhten Komplexität führt und ggf. die Problembehandlung und Fehlerermittlung erschwert und zeitaufwändiger macht.

| Resource | 
| --- | 
| [Anzeigen effektiver Routen](../virtual-network/manage-route-table.md#view-effective-routes)

### <a name="system-routes"></a>Systemrouten

Im Hinblick auf [Systemrouten](../virtual-network/virtual-networks-udr-overview.md#system-routes) sollten Personen, die am Entwurf oder der Implementierung virtueller Netzwerke beteiligt sind, mit den Standardsystemrouten und den verfügbaren Optionen vertraut sein, mit denen diese Routen ergänzt oder überschrieben werden können.

### <a name="service-endpoints"></a>Dienstendpunkte

Die Aktivierung von [Dienstendpunkten](../virtual-network/virtual-network-service-endpoints-overview.md) in einem Subnetz ergibt einen direkten Kommunikationspfad zur zugeordneten PaaS-Ressource. Dies kann zu einer erhöhten Leistung und Sicherheit führen, indem der verfügbare Kommunikationspfad nur auf diesen Dienst beschränkt wird. Bei Verwendung von Dienstendpunkten ergibt sich ein potenzieller Pfad für die Datenexfiltration, da in der Standardkonfiguration Zugriff auf alle Instanzen des PaaS-Diensts besteht, und nicht nur auf die spezifischen Instanzen, die für eine Anwendung oder ein System erforderlich sind.

Commonwealth-Entitäten sollten eine Evaluierung des Risikos durchführen, das mit der Bereitstellung des direkten Zugriffs auf die PaaS-Ressource verbunden ist, einschließlich der Wahrscheinlichkeit und der Konsequenzen einer missbräuchlichen Nutzung des Pfads.

Zur Reduzierung potenzieller Risiken, die für Dienstendpunkte bestehen können, sollten Sie nach Möglichkeit Richtlinien für Dienstendpunkte implementieren. Sie können auch erwägen, Dienstendpunkte für eine Azure Firewall oder ein NVA-Subnetz zu aktivieren und Datenverkehr aus bestimmten Subnetzen über diese Punkte zu leiten, um zusätzliche Optionen für Filterung, Überwachung oder Untersuchung anwenden zu können.

|Resource|Link|
|---|---|
|*Tutorial: Einschränken des Netzwerkzugriffs auf PaaS-Ressourcen mit VNET-Dienstendpunkten mithilfe des Azure-Portals* |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](../virtual-network/tutorial-restrict-network-access-to-resources.md)|
|

### <a name="route-tables"></a>Routentabellen

Mit Routingtabellen wird ein vom Administrator konfigurierter Mechanismus zum Steuern des Netzwerkdatenverkehrs in Azure bereitgestellt. Routingtabellen können verwendet werden, um Datenverkehr über eine Azure Firewall oder ein virtuelles Netzwerkgerät weiterzuleiten, eine direkte Verbindung mit externen Ressourcen herzustellen oder Azure-Systemrouten zu überschreiben. Außerdem kann mit Routingtabellen verhindert werden, dass Netzwerke, die über ein Gateway für virtuelle Netzwerke ermittelt wurden, für Ressourcen in einem Subnetz verfügbar gemacht werden. Zu diesem Zweck wird die Routenverteilung über das Gateway für virtuelle Netzwerke deaktiviert.

|Resource|Link|
|---|---|
|*Routingkonzepte: Benutzerdefinierte Routen* |[https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](../virtual-network/virtual-networks-udr-overview.md#custom-routes)|
|*Tutorial: Weiterleiten von Netzwerkdatenverkehr* |[https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal](../virtual-network/tutorial-create-route-table-portal.md)|
|

### <a name="border-gateway-protocol-bgp"></a>Border Gateway Protocol (BGP)

BGP kann von Gateways für virtuelle Netzwerke genutzt werden, um Routinginformationen auf dynamische Weise mit lokalen oder anderen externen Netzwerken auszutauschen. BGP gilt für ein virtuelles Netzwerk, wenn es über das Gateway für virtuelle Netzwerke einer ExpressRoute-Leitung per privatem ExpressRoute-Peering konfiguriert und auf einem Azure VPN Gateway aktiviert wurde.

Personen, die am Entwurf oder der Implementierung virtueller Netzwerke und von Gateways für virtuelle Netzwerke in Azure beteiligt sind, sollten sich ausreichend Zeit nehmen, um sich mit dem Verhalten und den verfügbaren Konfigurationsoptionen für BGP in Azure vertraut zu machen.

|Resource|Link|
|---|---|
|*Routingkonzepte: BGP* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools)|
|*ExpressRoute-Routinganforderungen* | [https://docs.microsoft.com/azure/expressroute/expressroute-routing](../expressroute/expressroute-routing.md)|
|*Übersicht über BGP mit Azure VPN Gateway* |[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview](../vpn-gateway/vpn-gateway-bgp-overview.md)|
|*Tutorial: Konfigurieren eines Site-to-Site-VPN über ExpressRoute-/Microsoft-Peering* |[https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering](../expressroute/site-to-site-vpn-over-microsoft-peering.md)|
|

## <a name="next-hop-types"></a>Typen des nächsten Hops

### <a name="virtual-network"></a>Virtual Network

Routen mit „Virtual Network“ als nächstem Hop werden automatisch als Systemrouten hinzugefügt. Sie können aber auch benutzerdefinierten Routen hinzugefügt werden, um Datenverkehr zurück an das virtuelle Netzwerk auf Instanzen zu leiten, für die die Systemroute überschrieben wurde.

### <a name="vnet-peering"></a>VNet-Peering

Das VNET-Peering ermöglicht die Kommunikation zwischen zwei unterschiedlichen virtuellen Netzwerken. Die Konfiguration des VNET-Peerings muss in allen virtuellen Netzwerken aktiviert werden, aber die virtuellen Netzwerke müssen sich nicht in derselben Region oder unter demselben Abonnement befinden oder demselben Azure AD-Mandanten (Azure Active Directory) zugeordnet sein.

Beim Konfigurieren des VNET-Peerings sollten Personen, die am Entwurf oder der Implementierung des VNET-Peerings beteiligt sind, unbedingt mit den vier zugeordneten Konfigurationsparametern vertraut sein und wissen, wie diese auf beiden Seiten des Peerings angewendet werden:

1. **Zugriff auf virtuelles Netzwerk zulassen**: Wählen Sie **Aktiviert** (Standard) aus, um die Kommunikation zwischen den beiden virtuellen Netzwerken zu aktivieren. Durch die Aktivierung der Kommunikation zwischen virtuellen Netzwerken können Ressourcen, die mit einem der beiden virtuellen Netzwerke verbunden sind, mit derselben Bandbreite und Latenz kommunizieren, als ob sie mit demselben virtuellen Netzwerk verbunden wären.
2. **Weitergeleiteten Datenverkehr zulassen**: Aktivieren Sie dieses Kontrollkästchen, um zuzulassen, dass der von einem virtuellen Netzwerk *weitergeleitete* Datenverkehr, also nicht aus dem virtuellen Netzwerk stammender Datenverkehr, mittels Peering über dieses virtuelle Netzwerk erfolgt. Diese Einstellung ist für die Implementierung einer Hub-and-Spoke-Netzwerktopologie von grundlegender Bedeutung.
3. **Gatewaytransit zulassen**: Aktivieren Sie diese Kontrollkästchen, um zuzulassen, dass das per Peering verbundene virtuelle Netzwerk das Gateway für virtuelle Netzwerke nutzt, das an dieses virtuelle Netzwerk angefügt ist. *Gatewaytransit zulassen* ist im virtuellen Netzwerk mit der VNET-Gatewayressource aktiviert, aber die Option gilt nur, wenn im anderen virtuellen Netzwerk *Remotegateways verwenden* aktiviert ist.
4. **Remotegateways verwenden**: Aktivieren Sie dieses Kontrollkästchen, wenn Sie zulassen möchten, dass Datenverkehr von diesen virtuellen Netzwerken über ein Gateway für virtuelle Netzwerke geleitet werden soll, das mit dem mittels Peering verknüpften virtuellen Netzwerk verbunden ist. *Remotegateways* ist im virtuellen Netzwerk ohne Gateway für virtuelle Netzwerke aktiviert und gilt nur, wenn die Option *Gatewaytransit zulassen* im anderen virtuellen Netzwerk aktiviert ist.

|Resource|Link|
|---|---|
| Konzepte: Peering virtueller Netzwerke                   | [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](../virtual-network/virtual-network-peering-overview.md) |
| Erstellen, Ändern oder Löschen eines Peerings virtueller Netzwerke | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](../virtual-network/virtual-network-manage-peering.md)|
|

### <a name="virtual-network-gateway"></a>Gateway des virtuellen Netzwerks

Gateways für virtuelle Netzwerke verfügen über einen Mechanismus zum Integrieren von virtuellen Netzwerken in externe Netzwerke, z. B. lokale Umgebungen, Partnerumgebungen und andere Cloudbereitstellungen. Die beiden Arten von Gateways für virtuelle Netzwerke sind „ExpressRoute“ und „VPN“.

#### <a name="expressroute-gateway"></a>ExpressRoute-Gateway

ExpressRoute-Gateways stellen einen Ausgangspunkt aus dem virtuellen Netzwerk in eine lokale Umgebung dar und sollten bereitgestellt werden, um die Anforderungen in Bezug auf Sicherheit, Verfügbarkeit, Finanzen und Leistung zu erfüllen. ExpressRoute-Gateways verfügen über eine definierte Netzwerkbandbreite, und nach der Bereitstellung fallen Nutzungskosten an. Virtuelle Netzwerke können nur über ein ExpressRoute-Gateway verfügen. Es kann aber mit mehreren ExpressRoute-Leitungen verbunden und von mehreren virtuellen Netzwerken per VNET-Peering verwendet werden, damit Bandbreite und Konnektivität gemeinsam genutzt werden können. Beim Konfigurieren des Routings zwischen lokalen Umgebungen und virtuellen Netzwerken mit ExpressRoute-Gateways sollte die End-to-End-Konnektivität sichergestellt werden, indem bekannte kontrollierte Netzwerkausgangspunkte verwendet werden. Commonwealth-Entitäten, die ein ExpressRoute-Gateway per privatem ExpressRoute-Peering nutzen, müssen auch virtuelle Netzwerkgeräte (Network Virtual Appliances, NVAs) bereitstellen, um für VPN-Konnektivität mit der lokalen Umgebung zu sorgen. So können die Vorgaben des ACSC-Verbraucherleitfadens eingehalten werden.

Es ist wichtig zu beachten, dass für ExpressRoute-Gateways Einschränkungen in Bezug auf Adressbereiche, Communitys und andere spezifische Konfigurationselemente gelten, die per BGP ausgetauscht werden.

| Resource  | Link  |
|---|---|
| ExpressRoute-Gateway: Übersicht | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](../expressroute/expressroute-about-virtual-network-gateways.md) |
| Konfigurieren eines Gateways für ein virtuelles Netzwerk für ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
|

#### <a name="vpn-gateway"></a>VPN Gateway

Azure VPN Gateway stellt einen Netzwerkausgangspunkt aus dem virtuellen Netzwerk in ein externes Netzwerk dar, um die sichere Site-to-Site-Konnektivität zu ermöglichen. VPN Gateway-Instanzen verfügen über eine definierte Netzwerkbandbreite, und nach der Bereitstellung fallen Nutzungskosten an. Commonwealth-Entitäten, die VPN Gateway nutzen, sollten sicherstellen, dass die Konfiguration gemäß dem ACSC-Verbraucherleitfaden erfolgt. Virtuelle Netzwerke können nur über eine VPN Gateway-Instanz verfügen. Es können aber mehrere Tunnel konfiguriert werden, und die Nutzung durch mehrere virtuelle Netzwerke per VNET-Peering ist möglich, sodass mehrere virtuelle Netzwerke die Bandbreite und Konnektivität gemeinsam nutzen können. VPN Gateway-Instanzen können über das Internet oder ExpressRoute per Microsoft-Peering eingerichtet werden.

| Resource  | Link |
| --- | --- |
| VPN Gateway: Übersicht| [https://docs.microsoft.com/azure/vpn-gateway](../vpn-gateway/index.yml)|
| Planung und Entwurf für VPN Gateway | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](../vpn-gateway/vpn-gateway-about-vpngateways.md)|
| Azure VPN Gateway in Azure Australien | [Azure VPN Gateway in Azure Australien](vpn-gateway.md)
|

### <a name="next-hop-of-virtual-appliance"></a>„Virtuelles Gerät“ als nächster Hop

Bei „Virtuelles Gerät“ als nächster Hop kann Netzwerkdatenverkehr außerhalb des Azure-Netzwerks verarbeitet werden, und die Routingtopologie kann auf virtuelle Netzwerke angewendet werden. Virtuelle Geräte können Sicherheitsregeln anwenden, Adressen übersetzen, VPNs einrichten, Datenverkehr über einen Proxy leiten und verschiedene andere Funktionen ausführen. „Virtuelles Gerät“ als nächster Hop wird über UDRs in einer Routingtabelle angewendet und kann genutzt werden, um Datenverkehr an eine Azure Firewall, ein einzelnes virtuelles Netzwerkgerät oder Azure Load Balancer zu leiten und die Verfügbarkeit für mehrere NVAs sicherzustellen. Zur Verwendung eines virtuellen Geräts für das Routing muss für die zugeordneten Netzwerkschnittstellen die IP-Weiterleitung aktiviert sein.

| Resource  | Link |
| --- | ---|
| Routingkonzepte: Benutzerdefinierte Routen | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](../virtual-network/virtual-networks-udr-overview.md#custom-routes) |
| Aktivieren oder Deaktivieren der IP-Weiterleitung | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding](../virtual-network/virtual-network-network-interface.md#enable-or-disable-ip-forwarding)
|

### <a name="next-hop-of-virtualnetworkserviceendpoint"></a>„VirtualNetworkServiceEndpoint“ als nächster Hop

Routen mit „VirtualNetworkServiceEndpoint“ als Typ des nächsten Hops werden nur hinzugefügt, wenn in einem Subnetz ein Dienstendpunkt konfiguriert wird. Sie können nicht manuell über Routingtabellen konfiguriert werden.

### <a name="next-hop-of-internet"></a>„Internet“ als nächster Hop

„Internet“ als nächster Hop wird verwendet, um Ressourcen zu erreichen, für die eine öffentliche IP-Adresse verwendet wird. Dies umfasst das Internet sowie PaaS- und Azure-Dienste in den Azure-Regionen. Bei „Internet“ als nächstem Hop ist keine Standardroute (0.0.0.0/0) erforderlich, mit der alle Netzwerke abgedeckt werden. Mit dieser Option können aber Routingpfade zu bestimmten öffentlichen Diensten aktiviert werden. „Internet“ als nächster Hop sollte verwendet werden, um Routen für autorisierte Dienste und Funktionen hinzuzufügen, die für den Betrieb des Systems benötigt werden, z. B. Microsoft-Verwaltungsadressen.

Beispiele für Dienste, die mit „Internet“ als nächstem Hop hinzugefügt werden können, sind:

1. Schlüsselverwaltungsdienste für die Windows-Aktivierung
2. Verwaltung der App Service-Umgebung

|Resource|Link|
|---|---|
| Ausgehende Verbindungen in Azure | [https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections](../load-balancer/load-balancer-outbound-connections.md) |
| Verwenden benutzerdefinierter Azure-Routen für die KMS-Aktivierung | [https://docs.microsoft.com/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation](/troubleshoot/azure/virtual-machines/custom-routes-enable-kms-activation) |
| Sperren einer App Service-Umgebung  | [https://docs.microsoft.com/azure/app-service/environment/firewall-integration](../app-service/environment/firewall-integration.md) |
|

### <a name="next-hop-of-none"></a>„Keine“ als nächster Hop

Mit „Keine“ als nächstem Hop kann die Kommunikation mit einem bestimmten Netzwerk verhindert werden. Im Gegensatz zu einer NSG, mit der gesteuert wird, ob für Datenverkehr das Durchlaufen eines verfügbaren Netzwerkpfads zugelassen oder verweigert wird, wird der Netzwerkpfad bei „Keine“ als nächstem Hop vollständig entfernt. Beim Erstellen von Routen mit „Keine“ als nächstem Hop sollte mit Bedacht vorgegangen werden. Dies gilt besonders bei Anwendung auf die Standardroute 0.0.0.0/0, da dies zu unerwünschten Konsequenzen führen und die Behandlung von Systemproblemen komplex und zeitaufwändig machen kann.

## <a name="security"></a>Sicherheit

Die Implementierung von Kontrollen zur Netzwerksegmentierung und -trennung für IaaS- und PaaS-Funktionen wird durch das direkte Schützen der Funktionen und das Implementieren von kontrollierten Kommunikationspfaden aus den Systemen erreicht, die mit der Funktion kommunizieren.

Das Entwerfen und Erstellen von Lösungen in Azure umfasst den Prozess der Erstellung einer logischen Architektur, um Netzwerkressourcen in der gesamten Azure-Umgebung verinnerlichen, steuern und überwachen zu können. Diese logische Architektur ist auf der Azure Platform softwaredefiniert und nimmt den Platz einer physischen Netzwerktopologie ein, die in herkömmlichen Netzwerkumgebungen implementiert wird.

Mit der erstellten logischen Architektur müssen die Funktionen bereitgestellt werden, die für die Nutzung benötigt werden, aber es muss auch für die erforderliche Transparenz und Kontrolle in Bezug auf die Sicherheit und Integrität gesorgt sein.

Die Erreichung dieses Ziels basiert auf der Implementierung der benötigten Tools für die Netzwerksegmentierung und -trennung, aber auch auf dem Schutz und der Erzwingung der Netzwerktopologie und der Implementierung dieser Tools.

### <a name="network-security-groups-nsgs"></a>Netzwerksicherheitsgruppen

Netzwerksicherheitsgruppen werden verwendet, um den ein- und ausgehenden Datenverkehr anzugeben, der für ein Subnetz oder eine bestimmte Netzwerkschnittstelle zulässig sein soll. Beim Konfigurieren von Netzwerksicherheitsgruppen sollten Commonwealth-Entitäten einen Positivlistenansatz verfolgen, bei dem Regeln konfiguriert werden. Hierbei kann der erforderliche Datenverkehr mit einer Standardregel zugelassen werden, während der gesamte Datenverkehr verweigert wird, der nicht einer bestimmten Zulassungsanweisung entspricht. Gehen Sie beim Planen und Konfigurieren von Netzwerksicherheitsgruppen mit Bedacht vor, um sicherzustellen, dass der gesamte erforderliche ein- und ausgehende Datenverkehr richtig erfasst wird. Dies umfasst auch das Identifizieren und Verinnerlichen aller privaten IP-Adressbereiche, die in virtuellen Netzwerken und der lokalen Umgebung genutzt werden, sowie bestimmte Microsoft-Dienste, z. B. Azure Load Balancer, und PaaS-Verwaltungsanforderungen. Personen, die am Entwurf und der Implementierung von Netzwerksicherheitsgruppen beteiligt sind, sollten auch mit der Nutzung von Diensttags und Anwendungssicherheitsgruppen vertraut sein, um differenzierte, dienstbezogene und anwendungsspezifische Sicherheitsregeln zu erstellen.

Es ist wichtig zu beachten, dass in der Standardkonfiguration für eine NSG ausgehender Datenverkehr an alle Adressen im virtuellen Netzwerk und an alle öffentlichen IP-Adressen zugelassen ist.

|Resource|Link|
|---|---|
|Übersicht über die Netzwerksicherheit | [https://docs.microsoft.com/azure/virtual-network/security-overview](../virtual-network/network-security-groups-overview.md)|
|Erstellen, Ändern oder Löschen einer Netzwerksicherheitsgruppe | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](../virtual-network/manage-network-security-group.md)|
|

### <a name="azure-firewall"></a>Azure Firewall

Azure Firewall kann zum Erstellen einer Hub-and-Spoke-Netzwerktopologie und Erzwingen von zentralen Netzwerksicherheitskontrollen genutzt werden. Azure Firewall kann verwendet werden, um die erforderlichen Anforderungen des ISM für ausgehenden Datenverkehr zu erfüllen, indem ein Ansatz mit einer Positivliste implementiert wird, bei dem nur die für die Systemfunktionalität benötigten IP-Adressen, Protokolle, Ports und FQDNs autorisiert werden. Commonwealth-Entitäten sollten einen risikobasierten Ansatz verfolgen, um zu ermitteln, ob die Sicherheitsfunktionen von Azure Firewall für ihre Anforderungen ausreichen. Für Szenarien, in denen zusätzliche Sicherheitsfunktionen über die Azure Firewall-Funktionen hinaus benötigt werden, sollten Commonwealth-Entitäten die Implementierung von virtuellen Netzwerkgeräten erwägen.

|Resource|Link|
|---|---|
|*Azure Firewall-Dokumentation* | [https://docs.microsoft.com/azure/firewall](../firewall/index.yml)|
|*Tutorial: Bereitstellen und Konfigurieren von Azure Firewall in einem Hybridnetzwerk mit Azure PowerShell* | [https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps](../firewall/tutorial-hybrid-ps.md)|
|

### <a name="network-virtual-appliances-nvas"></a>Virtuelle Netzwerkgeräte (NVAs)

Mit NVAs kann eine Hub-and-Spoke-Netzwerktopologie erstellt werden, und es können erweiterte oder ergänzende Netzwerkfunktionen bereitgestellt werden. Sie können auch als Alternative zu Azure-Netzwerkmechanismen dienen, um für Vertrautheit und einheitliche Unterstützung zu sorgen und die Verwaltung mit lokalen Netzwerkdiensten zu ermöglichen. Virtuelle Netzwerkgeräte können bereitgestellt werden, um bestimmte Sicherheitsanforderungen zu erfüllen. Ein Beispiel hierfür sind Szenarien, in denen für Netzwerkdatenverkehr, HTTPS-Entschlüsselung, Inhaltsuntersuchung, Filterung oder anderweitige Sicherheitsfunktionen Informationen zur Identität vorliegen müssen. Virtuelle Netzwerkgeräte sollten mit einer hochverfügbaren Konfiguration bereitgestellt werden. Personen, die am Entwurf oder der Implementierung von virtuellen Netzwerkgeräten beteiligt sind, sollten die Richtlinien zur Bereitstellung in Azure befolgen, die in der entsprechenden Anbieterdokumentation angegeben sind.

|Resource|Link|
|---|---|
|*Bereitstellen hochverfügbarer virtueller Netzwerkgeräte* | [https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha](/azure/architecture/reference-architectures/dmz/nva-ha)|
|

### <a name="service-endpoint-policies-preview"></a>Dienstendpunkt-Richtlinien (Vorschauversion)

Konfigurieren Sie die Dienstendpunkt-Richtlinien basierend auf der Verfügbarkeit des Diensts und einer Sicherheitsrisikobewertung in Bezug auf die Wahrscheinlichkeit und die Auswirkungen einer Datenexfiltration. Dienstendpunkt-Richtlinien sollten für Azure Storage in Erwägung gezogen werden und für andere Dienste von Fall zu Fall basierend auf dem jeweiligen Risikoprofil verwaltet werden.

| Resource | Link  |
| --- | --- |
| *Dienstendpunkt-Richtlinien: Übersicht* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview](../virtual-network/virtual-network-service-endpoint-policies-overview.md) |
| *Erstellen, Ändern oder Löschen einer Dienstendpunktrichtlinie über das Azure-Portal* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal](../virtual-network/virtual-network-service-endpoint-policies-portal.md)
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy ist eine wichtige Komponente zur Erzwingung und Aufrechterhaltung der Integrität der logischen Architektur einer Azure-Umgebung. Für Azure-Dienste sind verschiedene Dienste und Pfade für ausgehenden Netzwerkdatenverkehr verfügbar. Es ist sehr wichtig, dass Commonwealth-Entitäten mit den Ressourcen, die in ihrer Umgebung vorhanden sind, und mit den verfügbaren Ausgangspunkten des Netzwerks vertraut sind. Um sicherzustellen, dass in der Azure-Umgebung keine unberechtigten Ausgangspunkte des Netzwerks erstellt werden, sollten Commonwealth-Entitäten Azure Policy verwenden, um die bereitstellbaren Ressourcentypen und die Konfiguration dieser Ressourcen zu steuern. Praktische Beispiele sind die Beschränkung auf autorisierte und genehmigte Ressourcen und die Durchsetzung der Regel, dass Netzwerksicherheitsgruppen Subnetzen hinzugefügt werden müssen.

|Resource | Link|
|---|---|
|*Azure Policy – Übersicht* | [https://docs.microsoft.com/azure/governance/policy/overview](../governance/policy/overview.md)|
|*Zulässige Ressourcentypen: Beispiel für Richtlinie* | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](../governance/policy/samples/index.md)|
|*Erzwingen der Nutzung eines Subnetzes für Netzwerksicherheitsgruppen: Beispiel für Richtlinie*| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](../governance/policy/samples/index.md)|
|

## <a name="paas-egress-capabilities"></a>Funktionen für PaaS-Ausgang

PaaS-Funktionen ermöglichen eine bessere Funktionalität und eine vereinfachte Verwaltung, erhöhen aber auch die Komplexität bei der Erfüllung der Anforderungen in Bezug auf die Netzwerksegmentierung und -trennung. PaaS-Funktionen werden normalerweise mit öffentlichen IP-Adressen konfiguriert und sind über das Internet zugänglich.  Bei Verwendung von PaaS-Funktionen in Ihren Systemen und Lösungen sollten Sie mit Bedacht vorgehen, um die Kommunikationsabläufe zwischen Komponenten zu ermitteln und Netzwerksicherheitsregeln zu erstellen, um nur diese Kommunikation zuzulassen. Im Rahmen eines tiefgreifenden Sicherheitsansatzes sollten PaaS-Funktionen mit Verschlüsselung, Authentifizierung und geeigneten Zugriffssteuerungen und -berechtigungen konfiguriert werden.  

### <a name="public-ip-for-paas"></a>Öffentliche IP-Adresse für PaaS

Öffentliche IP-Adressen für PaaS-Funktionen werden basierend auf der Region zugeordnet, in der der Dienst gehostet oder bereitgestellt wird. Sie müssen mit der Zuordnung von öffentlichen IP-Adressen und den Regionen vertraut sein, wenn Sie geeignete Netzwerksicherheitsregeln und eine Routingtopologie für die Netzwerksegmentierung und -trennung erstellen möchten, um virtuelle Azure-Netzwerke, PaaS und die ExpressRoute- und Internetkonnektivität abzudecken. Azure ordnet IP-Adressen dem Pool zu, über den jede Azure-Region verfügt. Microsoft stellt die in den Regionen jeweils verwendeten Adressen als Download bereit, der regelmäßig kontrolliert und aktualisiert wird. Die in den einzelnen Regionen verfügbaren Dienste ändern sich zudem häufig, wenn neue Dienste veröffentlicht oder Dienste in größerem Umfang bereitgestellt werden. Commonwealth-Entitäten sollten dieses Material regelmäßig prüfen und können die Automatisierung nutzen, um die Systeme auf dem erforderlichen Stand zu halten. Die bestimmten IP-Adressen für einige Dienste, die in den einzelnen Regionen gehostet werden, erhalten Sie beim Microsoft-Support.

| Resource | Link |
| --- | --- |
| *IP-Bereiche für Microsoft Azure-Rechenzentren*                   | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653) |
| *Azure-Dienste nach Region*                              | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all) |
| *Ein- und ausgehende IP-Adressen in Azure App Service* | [https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips](../app-service/overview-inbound-outbound-ips.md)
|

## <a name="next-steps"></a>Nächste Schritte

Vergleichen Sie Ihre gesamte Architektur und Ihren Entwurf mit den veröffentlichten [PROTECTED-Blueprints für IaaS- und PaaS-Webanwendungen](https://aka.ms/au-protected).
