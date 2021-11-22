---
title: 'Gateway: Protokollierung, Überwachung und Sichtbarkeit in Azure Australien'
description: Hier erfahren Sie, wie Sie die Protokollierung, Überwachung und Sichtbarkeit in den australischen Regionen konfigurieren, um die spezifischen Anforderungen der Richtlinien, Bestimmungen und Gesetze der australischen Regierung zu erfüllen.
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: 95d744785d95d5040e5d82d56d1905145111b682
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132340274"
---
# <a name="gateway-logging-auditing-and-visibility-in-azure-australia"></a>Gateway: Protokollierung, Überwachung und Sichtbarkeit in Azure Australien

Das Erkennen von Cybersicherheitsbedrohungen und das Ergreifen entsprechender Maßnahmen hängen vom Generieren, Erfassen und Analysieren der Daten ab, die mit dem Betrieb eines Systems zusammenhängen.

Microsoft hat Azure mit Tools ausgestattet, mit denen Sie die Protokollierung, Überwachung und Sichtbarkeit implementieren können, um die Sicherheit der in Azure bereitgestellten Systeme zu verwalten. Zudem ist eine Referenzarchitektur vorhanden, die mit dem Verbraucherleitfaden des Australian Cyber Security Centre (ACSC) und dem Handbuch für Informationssicherheit (Information Security Manual, ISM) konform ist.

Gateways fungieren als Steuerungsmechanismen für den Informationsfluss auf der Netzwerkschicht und können auch Informationen in den höheren Schichten des OSI-Modells (Open Systems Interconnection Model) steuern. Gateways sind erforderlich, um den Datenfluss zwischen Sicherheitsdomänen zu steuern und nicht autorisierten Zugriff von externen Netzwerken zu verhindern. Aufgrund der Wichtigkeit von Gateways bei der Steuerung des Informationsflusses zwischen Sicherheitsdomänen können alle Fehler, insbesondere bei höheren Klassifizierungen, schwerwiegende Folgen haben. Daher sind robuste Mechanismen, die die Mitarbeiter auf Situationen aufmerksam machen, durch die Vorfälle bezüglich der Cybersicherheit entstehen können, für Gateways besonders wichtig.

Das Implementieren von Protokollierungs- und Warnungsfunktionen für Gateways kann bei der Erkennung von Cybersicherheitsvorfällen, versuchten Angriffen und ungewöhnlichen Verwendungsmustern hilfreich sein. Das Speichern von Ereignisprotokollen auf einem separaten sicheren Protokollserver macht es Angreifern zudem schwieriger, Protokollierungsinformationen zu löschen, um die Beweise eines gezielten Cyberangriffs zu vernichten.

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Anforderungen des Australian Cyber Security Centre (ACSC)

Die allgemeinen Sicherheitsanforderungen für Systeme im Commonwealth sind im Handbuch für Informationssicherheit des Australian Cyber Security Centre festgelegt. Damit Commonwealth-Entitäten diese Anforderungen in Azure erfüllen können, werden in den Publikationen *ACSC CONSUMER GUIDE – Microsoft Azure at PROTECTED (ACSC-VERBRAUCHERLEITFADEN – Microsoft Azure in der Klassifizierungsstufe „PROTECTED“)* und *ACSC CERTIFICATION REPORT – Microsoft Azure (ASCS-ZERTIFIZIERUNGSBERICHT – Microsoft Azure)* die folgenden Anforderungen, die speziell für die Protokollierung, Überwachung und Sichtbarkeit gelten, detailliert erläutert:

1. Zur Minderung der Risiken, die aus der Verwendung gemeinsam genutzter zugrunde liegender Cloudressourcen entstehen können, müssen Commonwealth-Entitäten die Microsoft Azure-Funktionen wie Azure Security Center, Azure Monitor, Azure Policy und Azure Advisor nutzen, um ihre Azure-Workloads in Echtzeit zu überwachen.

2. Das Australian Cyber Security Centre empfiehlt allen Mitgliedsstaaten des Commonwealth, sämtliche vorgeschriebenen Sicherheitsprotokolle an das ASCS zu schicken, damit diese durch die australische Regierung überwacht werden.

3. Zur verbesserten Risikominderung sollten Commonwealth-Entitäten Folgendes in ihren Azure-Abonnements durchführen:

    * Azure Security Center aktivieren
    * Upgrade auf den Standard-Tarif
    * die automatische Bereitstellung von Microsoft Monitoring Agent für unterstützte Azure-VMs aktivieren
    * Sicherheitsempfehlungen und -warnungen im Security Center-Dashboard regelmäßig überprüfen, priorisieren und beheben

4. Behördliche Entitäten müssen die Protokoll- und Ereignisweiterleitung aus ihrem Azure-Abonnement an das ACSC aktivieren, um dem ACSC Einblicke in die Nichtkonformität mit diesem Leitfaden zu ermöglichen. Azure Event Hubs bietet die Möglichkeit, Protokolle extern an das ACSC oder an lokale Systeme zu streamen, die sich im Besitz der Commonwealth-Entität befinden.

5. Commonwealth-Entitäten sollten die Protokollierung, die sie in Azure aktivieren, an den im ISM festgelegten Anforderungen ausrichten.

6. Microsoft behält Protokolle in Azure für 90 Tage. Kundenentitäten müssen ein Protokollarchivierungssystem einführen, um sicherzustellen, dass die Protokolle – wie in der Administrative Functions Disposal Authority (AFDA) der National Archives of Australia (NAA) festgelegt – sieben Jahre behalten werden können.

7. Commonwealth-Entitäten mit lokalen oder Azure-basierten SIEM-Funktionen (Security Information and Event Management) können auch Protokolle an diese Systeme weiterleiten.

8. Commonwealth-Entitäten sollten Network Watcher-Datenflussprotokolle für Netzwerksicherheitsgruppen (NSGs) und virtuelle Computer implementieren. Diese Protokolle sollten in einem dedizierten Speicherkonto gespeichert werden, das nur Sicherheitsprotokolle enthält, und der Zugriff auf das Speicherkonto sollte durch rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) geschützt werden.

9. Commonwealth-Entitäten müssen den ACSC-Verbraucherleitfaden implementieren, um sicherzustellen, dass Azure-Workloads in Bezug auf die Protokollierung und Überwachung mit dem ISM konform sind. Zudem müssen sie Azure-Funktionen aktivieren, die dem ASCS den Empfang von Echtzeitdaten zu Überwachung, Warnungen und Protokollen ermöglichen, die mit der Azure-Nutzung der australischen Regierung in Zusammenhang stehen.

## <a name="architecture"></a>Aufbau

Die erforderliche Protokollierung muss für die richtigen Komponenten aktiviert werden, um den eingehenden und ausgehenden Netzwerkdatenverkehr Ihrer Azure-Umgebung nachvollziehen zu können. Dadurch wird die vollständige Sichtbarkeit der Umgebung sichergestellt, und es werden die für Analysen erforderlichen Daten erfasst.

![Azure-Überwachungsarchitektur](media/visibility.png)

## <a name="components"></a>Komponenten

Die oben veranschaulichte Architektur besteht aus diskreten Komponenten, die die Protokollquellen bereitstellen und die Sammlung, Aufbewahrung und Analyse von Protokollen sowie die Reaktion auf Vorfälle ermöglichen. Diese Architektur umfasst einzelne Komponenten, die in der Regel in über das Internet zugänglichen Azure-Bereitstellungen enthalten sind.

|Funktionen|Komponenten|
|---|---|
|Protokollquellen|<ul><li>Application Gateway</li><li>VPN Gateway</li><li>Azure Firewall</li><li>Virtuelle Netzwerkgeräte</li><li>Azure Load Balancer</li><li>Virtual Machines</li><li>DNS-Server (Domain Name System)</li><li>Syslog-Server und/oder Protokollsammlungsserver</li><li>NSGs</li><li>Azure-Aktivitätsprotokoll</li><li>Azure-Diagnoseprotokoll</li><li>Azure Policy</li></ul>|
|Protokollsammlung|<ul><li>Event Hubs</li><li>Network Watcher</li><li>Log Analytics</li></ul>|
|Protokollaufbewahrung|<ul><li>Azure Storage</li></ul>|
| Protokollanalyse|<ul><li>Microsoft Defender für Cloud</li><li>Azure Advisor</li><li>Log Analytics-Lösungen<ul><li>Traffic Analytics</li><li>DNS Analytics (Vorschauversion)</li><li>Aktivitätsprotokollanalyse</li></ul></li><li>SIEM</li><li>Australian Cyber Security Centre (ACSC)</li></ul>|
|Reaktion auf Vorfälle|<ul><li>Azure-Warnungen</li><li>Azure Automation</li></ul>|
|

Die Architektur erstellt zuerst Protokolle aus den erforderlichen Quellen und sammelt diese dann in zentralisierten Depots. Sobald Protokolle gesammelt wurden, sind folgende Aktionen möglich:

* die Verwendung in Azure Analysis Services zur Gewinnung von Erkenntnissen
* die Weiterleitung an externe Systeme
* die Archivierung im Speicher zur Langzeitaufbewahrung

Zur Reaktion auf wichtige Ereignisse oder Vorfälle, die von den Analysetools ermittelt wurden, können Warnungen konfiguriert und eine Automatisierung entwickelt werden, um die notwendigen Maßnahmen zur proaktiven Verwaltung und Reaktion zu ergreifen.

## <a name="general-guidance"></a>Allgemeine Hinweise

Beim Implementieren der in diesem Artikel aufgeführten Komponenten gelten die folgenden allgemeinen Hinweise:

* Überprüfen Sie die regionale Verfügbarkeit von Diensten, stellen Sie dabei sicher, dass alle Daten innerhalb der genehmigten Standorte verbleiben, und stellen Sie Workloads der Klassifizierungsstufe „PROTECTED“ bevorzugt in „Australien, Mitte“ oder „Australien, Mitte 2“ bereit.

* Nutzen Sie die Publikation *Azure – ACSC Certification Report – Protected 2018 (Azure – ACSC-Zertifizierungsbericht – Klassifizierungsstufe „PROTECTED“ – 2018)*, um den Zertifizierungsstatus einzelner Dienste herauszufinden, und führen Sie für alle relevanten Komponenten, die nicht im Bericht enthalten sind, selbst eine Bewertung gemäß *ACSC CONSUMER GUIDE – Microsoft Azure at PROTECTED (ACSC-VERBRAUCHERLEITFADEN – Microsoft Azure in der Klassifizierungsstufe „PROTECTED“)* durch.

* Für Komponenten, auf die in diesem Artikel nicht verwiesen wird, sollten die Commonwealth-Entitäten die in Bezug auf das Erstellen, Erfassen, Analysieren und Behalten von Protokollen festgelegten Prinzipien einhalten.

* Identifizieren und priorisieren Sie die Protokollierung, Überwachung und Sichtbarkeit für wichtige Systeme sowie alle Netzwerkeingangspunkte und -ausgangspunkte für in Azure gehostete Systeme.

* Konsolidieren Sie Protokolle, und minimieren Sie die Anzahl der Instanzen von Protokollierungstools, z. B. Speicherkonten, Log Analytics-Arbeitsbereichen und Event Hubs.

* Schränken Sie Administratorberechtigungen über die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) ein.

* Verwenden Sie mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für die Kontoverwaltung und die Konfiguration von Ressourcen in Azure.

* Wenn Sie die Protokollsammlung für mehrere Abonnements zentralisieren, sollten Sie sicherstellen, dass die Administratoren die erforderlichen Berechtigungen für alle Abonnements besitzen.

* Stellen Sie sicher, dass die Netzwerkkonnektivität und die erforderliche Proxykonfiguration für die virtuellen Computer vorhanden sind (einschließlich für virtuelle Netzwerkgeräte, Protokollsammlungsserver und DNS-Server), damit eine Verbindung mit den benötigten Azure-Diensten wie Log Analytics-Arbeitsbereichen, Event Hubs und Azure Storage hergestellt werden kann.

* Konfigurieren Sie den Microsoft Monitoring Agent (MMA) so, dass TLS 1.2 verwendet wird.

* Verwenden Sie Azure Policy, um die Konformität mit Anforderungen zu überwachen und zu erzwingen.

* Erzwingen Sie die Verschlüsselung aller Datenrepositorys wie Speicher und Datenbanken.

* Verwenden Sie lokal redundanten Speicher und Momentaufnahmen für die Verfügbarkeit von Speicherkonten und der zugeordneten Daten.

* Verwenden Sie georedundanten Speicher oder externen Speicher, um die Strategien zur Notfallwiederherstellung anwenden zu können.

|Ressource|URL|
|---|---|
|Dokumente zur Konformität mit Bestimmungen und Richtlinien für Australien|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Azure-Produkte: Australische Regionen und andere|[https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Whitepaper „Microsoft Azure Security and Audit Log Management (Verwaltung von Sicherheits- und Überwachungsprotokollen in Microsoft Azure)“|[https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf)|
|Konfiguration von Microsoft Monitoring Agent|[https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent](../azure-monitor/agents/log-analytics-agent.md)|
|

## <a name="component-guidance"></a>Leitfaden zu den Komponenten

Dieser Abschnitt enthält Informationen zum Zweck der einzelnen Komponenten und deren Rolle in der gesamten Architektur für die Protokollierung, Überwachung und Sichtbarkeit. Für den Zugriff auf nützliche Ressourcen wie Referenzdokumentation, Anleitungen und Tutorials werden zusätzliche Links bereitgestellt.

## <a name="log-sources"></a>Protokollquellen

Bevor Analysen, Warnungen oder Berichte abgeschlossen werden können, müssen die erforderlichen Protokolle generiert werden. Azure-Protokolle werden in Steuerungs- bzw. Verwaltungsprotokolle, Datenebenenprotokolle und verarbeitete Ereignisse kategorisiert.

|type|BESCHREIBUNG|
|---|---|
|Steuerungs- und Verwaltungsprotokolle|Informationen zu den Azure Resource Manager-Vorgängen|
|Datenebenenprotokolle|Informationen zu Ereignissen, die im Rahmen der Azure-Ressourcennutzung ausgelöst wurden, z. B. in Form von Protokollen auf einem virtuellen Computer oder von Diagnoseprotokollen in Azure Monitor|
|Verarbeitete Ereignisse|Informationen zu analysierten Ereignissen und Warnungen, die von Azure verarbeitet wurden, z. B., ob Microsoft Defender für Cloud Abonnements verarbeitet und analysiert hat, um Sicherheitswarnungen zu generieren|
|

### <a name="application-gateway"></a>Application Gateway

Azure Application Gateway ist einer der möglichen Einstiegspunkte in eine Azure-Umgebung, sodass Sie Informationen im Zusammenhang mit eingehenden Verbindungen erfassen müssen, die mit Webanwendungen kommunizieren. Application Gateway kann wichtige Informationen zur Verwendung von Webanwendungen bereitstellen und die Erkennung von Cybersicherheitsvorfällen unterstützen. Application Gateway sendet Metadaten an das Aktivitätsprotokoll und die Diagnoseprotokolle in Azure Monitor, wo diese in Log Analytics verwendet oder an einen Event Hub oder ein Speicherkonto verteilt werden können.

|Ressourcen|Link|
|---|---|
|Dokumentation zu Application Gateway|[https://docs.microsoft.com/azure/application-gateway/](../application-gateway/index.yml)|
|Schnellstarthandbuch zu Application Gateway|[https://docs.microsoft.com/azure/application-gateway/quick-create-portal](../application-gateway/quick-create-portal.md)|
|

### <a name="vpn-gateway"></a>VPN Gateway

VPN Gateway ist ein potenzieller Einstiegspunkt für viele Kommunikationsmethoden mit der Azure-Umgebung, z. B. die Verbindung mit einer lokalen Umgebung und administrativer Datenverkehr. Die Protokollierung in VPN Gateway bietet Einblicke in Verbindungen mit der Azure-Umgebung und ermöglicht deren Nachverfolgbarkeit. Die Protokollierung ermöglicht Überwachung und Analysen und kann Sie bei der Erkennung und Untersuchung von schädlichen oder anomalen Verbindungen unterstützen. VPN Gateway-Protokolle werden an das Azure Monitor-Aktivitätsprotokoll gesendet, wo sie in Log Analytics verwendet oder an einen Event Hub oder ein Speicherkonto verteilt werden können.

|Ressourcen|Link|
|---|---|
|VPN Gateway-Dokumentation|[https://docs.microsoft.com/azure/vpn-gateway/](../vpn-gateway/index.yml)|
|Leitfaden zu VPN Gateway für die australische Regierung|[Azure VPN Gateway-Konfiguration](vpn-gateway.md)|
|

### <a name="azure-firewall"></a>Azure Firewall

Azure Firewall bietet einen kontrollierten Exitpunkt aus einer Azure-Umgebung und den generierten Protokollen. Diese enthalten Informationen zu versuchten und erfolgreichen ausgehenden Verbindungen und sind für die Protokollierungsstrategie essenziell. Mithilfe dieser Protokolle können Sie überprüfen, ob Systeme wie geplant funktionieren. Zudem können Sie erkennen, ob Angreifer über schädlichen Code oder andere Methoden versuchen, eine Verbindung mit nicht autorisierten externen Systemen herstellen. Azure Firewall schreibt Protokolle in das Aktivitätsprotokoll und die Diagnoseprotokolle in Azure Monitor, wo diese in Log Analytics verwendet oder an einen Event Hub oder ein Speicherkonto verteilt werden können.

|Ressourcen|Link|
|---|---|
|Azure Firewall-Dokumentation|[https://docs.microsoft.com/azure/firewall/](../firewall/index.yml)|
|Tutorial: Überwachen von Azure Firewall-Protokollen und -Metriken|[https://docs.microsoft.com/azure/firewall/tutorial-diagnostics](../firewall/firewall-diagnostics.md)|
|

### <a name="network-virtual-appliances-nva"></a>Virtuelle Netzwerkgeräte

Virtuelle Netzwerkgeräte können zur Ergänzung der nativen Azure-Sicherheitsfunktionen verwendet werden. Die Protokolle, die in virtuellen Netzwerkgeräten generiert werden, können für die Erkennung von Cybersicherheitsvorfällen nützlich sein und sind ein essenzieller Bestandteil der Protokollierungs-, Überwachungs- und Sichtbarkeitsstrategie. Nutzen Sie Microsoft Monitoring Agent, um Protokolle von virtuellen Netzwerkgeräten zu erfassen. Wenn ein virtuelles Netzwerkgerät die Installation von Microsoft Monitoring Agent nicht unterstützt, sollten Sie einen Syslog-Server oder einen anderen Protokollsammlungsserver verwenden, um die Protokolle weiterzuleiten.

|Ressourcen|Link|
|---|---|
|Übersicht über virtuelle Netzwerkgeräte|[https://azure.microsoft.com/solutions/network-appliances](https://azure.microsoft.com/solutions/network-appliances)|
|Dokumentation zu virtuellen Netzwerkgeräten|Informationen zur Implementierung der relevanten virtuellen Netzwerkgeräte in Azure finden Sie in der Dokumentation des Anbieters.|
|

### <a name="azure-load-balancer"></a>Azure Load Balancer

Azure Load Balancer-Protokolle werden verwendet, um wichtige Informationen zu den Verbindungen und der Verwendung von in Azure bereitgestellten Systemen zu erhalten. Diese können für die Überwachung der Integrität und Verfügbarkeit eingesetzt werden, stellen jedoch auch eine weitere wichtige Komponente dar, durch die die notwendigen Einblicke in den Kommunikationsdatenverkehr gewonnen und schädliche oder anomale Datenverkehrsmuster erkannt werden können. Azure Load Balancer schreibt Protokolle in das Aktivitätsprotokoll und die Diagnoseprotokolle in Azure Monitor, wo diese in Log Analytics verwendet oder an einen Event Hub oder ein Speicherkonto verteilt werden können.

|Ressourcen|Link|
|---|---|
|Dokumentation zu Azure Load Balancer|[https://docs.microsoft.com/azure/load-balancer](../load-balancer/index.yml)|
|Metriken und Integritätsdiagnosen für Standard Load Balancer|[https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics](../load-balancer/load-balancer-standard-diagnostics.md)|
|

### <a name="virtual-machines"></a>Virtuelle Computer

Virtual Computer sind Endpunkte, mit denen Netzwerkkommunikation gesendet und empfangen, Daten verarbeitet und Dienste bereitgestellt werden. Da virtuelle Computer Daten oder wichtige Systemdienste hosten können, muss sichergestellt werden, dass diese ordnungsgemäß funktionieren und Cybersicherheitsvorfälle erkennen. Virtuelle Computer sammeln verschiedene Ereignis-und Überwachungsprotokolle, die den Betrieb des Systems und die auf dem System ausgeführten Aktionen nachverfolgen. Protokolle, die auf virtuellen Computern gesammelt werden, können mithilfe des Log Analytics-Agents an einen Log Analytics-Arbeitsbereich weitergeleitet werden, wo sie von Microsoft Defender für Cloud analysiert werden können. Virtuelle Computer können auch direkt oder über einen Protokollsammlungsserver in Azure Event Hubs oder eine SIEM-Lösung integriert werden.

|Ressourcen|Link|
|---|---|
|Virtual Machines|[https://docs.microsoft.com/azure/virtual-machines](../virtual-machines/index.yml)|
|Datenerfassung in Virtual Machines|[https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm](../azure-monitor/vm/monitor-virtual-machine.md)|
|Streamen von Virtual Machines-Protokollen in Event Hubs|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md)|
|

### <a name="domain-name-services-dns-servers"></a>DNS-Server (Domain Name Service)

DNS-Serverprotokolle bieten wichtige Informationen zu den Diensten, auf die Systeme intern oder extern zugreifen. Das Erfassen von DNS-Protokollen kann bei der Erkennung von Cybersicherheitsvorfällen helfen und einen Einblick in die Art des Vorfalls und die betroffenen Systeme bieten. Der Microsoft-Verwaltungs-Agent kann auf DNS-Servern verwendet werden, um Protokolle über Log Analytics weiterzuleiten, damit diese in DNS Analytics (Vorschau) verwendet werden können.

|Ressourcen|Link|
|---|---|
|Azure-Namensauflösung für virtuelle Netzwerke|[https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)|
|

### <a name="syslog-and-log-collection-servers"></a>Syslog-Server und Protokollsammlungsserver

Dedizierte Server können innerhalb von Azure-VNETs bereitgestellt werden, um Protokolle von virtuellen Netzwerkgeräten oder benutzerdefinierten Sicherheitsprotokollen von anderen Systemen für die Verwendung in einem SIEM-System zu empfangen. Syslog-Protokolle können auf einem Syslog-Server gesammelt und zur Analyse an Log Analytics weitergeleitet werden. Protokollsammlungsserver ist ein allgemeiner Begriff für sämtliche Funktionen zur Protokollaggregation und -verteilung, die von zentralisierten Überwachungssystemen oder SIEM-Systemen verwendet werden. Diese können verwendet werden, um die Netzwerkarchitektur und -sicherheit zu vereinfachen und Protokolle vor der Verteilung an die zentralisierte Funktion zu filtern und zu aggregieren.

|Ressourcen|Link|
|---|---|
|Syslog-Datenquellen in Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog](../azure-monitor/agents/data-sources-syslog.md)|
|Protokollsammlungsserver|Weitere Informationen zur Überwachung und zur SIEM-Architektur finden Sie in der Dokumentation des Anbieters.|
|

### <a name="network-security-groups-nsgs"></a>Netzwerksicherheitsgruppen

Mit NSGs wird der eingehende und ausgehende Datenverkehr von virtuellen Netzwerken an Azure gesteuert. NSGs wenden Regeln für die Datenverkehrsflüsse an, die darüber zugelassen oder verweigert werden. Dies umfasst auch Datenverkehr in Azure und zwischen Azure und externen Netzwerken, z. B. lokal oder im Internet. NSGs werden auf Subnetze in einem virtuellen Netzwerk oder auf einzelne Netzwerkschnittstellen angewendet. NSGs können über das Feature „Network Watcher NSG Flow Logs“ (Network Watcher-NSG-Datenflussprotokolle) aktiviert werden, um Informationen zum Datenverkehr zu erfassen, der bei Systemen in Azure ein- und ausgeht. Diese Protokolle werden verwendet, um eine Baseline für den Standardbetrieb eines Systems zu erstellen. Sie sind zudem die Datenquelle für das Traffic Analytics-Feature, das detaillierte Einblicke in die Datenverkehrsmuster der in Azure gehosteten Systeme liefert.

|Ressourcen|Link|
|---|---|
|Dokumentation zu Netzwerksicherheitsgruppen|[https://docs.microsoft.com/azure/virtual-network/security-overview](../virtual-network/network-security-groups-overview.md)|
|Einführung in die Datenflussprotokollierung für Netzwerksicherheitsgruppen|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview](../network-watcher/network-watcher-nsg-flow-logging-overview.md)|
|Tutorial: Protokollieren des Netzwerkdatenverkehrs zu und von einer VM über das Azure-Portal|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal](../network-watcher/network-watcher-nsg-flow-logging-portal.md)|
|

### <a name="azure-activity-log"></a>Azure-Aktivitätsprotokoll

Das Azure-Aktivitätsprotokoll ist ein Abonnementprotokoll, das Bestandteil von Azure Monitor ist und Einblicke in Ereignisse auf Abonnementebene ermöglicht, die in Azure aufgetreten sind. Mithilfe des Aktivitätsprotokolls kann ermittelt werden, welcher Benutzer zu welchem Zeitpunkte welche Schreibvorgänge (PUT, POST, DELETE) ***in*** den Ressourcen eines Abonnements ausgeführt hat. Das Aktivitätsprotokoll ist für die Nachverfolgung der Konfigurationsänderungen in der Azure-Umgebung entscheidend. Azure-Aktivitätsprotokolle können automatisch in Log Analytics-Lösungen verwendet und zur Verarbeitung oder Aufbewahrung an Event Hubs oder Azure Storage gesendet werden.

|Ressourcen|Link|
|---|---|
|Dokumentation zum Azure-Aktivitätsprotokoll|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs](../azure-monitor/essentials/platform-logs-overview.md)|
|Streamen des Azure-Aktivitätsprotokolls an Event Hubs|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs](../azure-monitor/essentials/activity-log.md#legacy-collection-methods)|
|

### <a name="azure-diagnostic-log"></a>Azure-Diagnoseprotokoll

Azure Monitor-Diagnoseprotokolle sind von einem Azure-Dienst ausgegebene Protokolle mit umfangreichen und in kurzen Abständen erfassten Daten zu den Vorgängen innerhalb eines Diensts. Diagnoseprotokolle bieten einen ausführlichen Einblick in den Betrieb einer Ressource und können beispielsweise zur Überwachung oder Problembehandlung eingesetzt werden. Azure-Diagnoseprotokolle können automatisch in Log Analytics-Lösungen verwendet und zur Verarbeitung oder Aufbewahrung an Event Hubs oder Azure Storage gesendet werden.

|Ressourcen|Link|
|---|---|
|Dokumentation zu Azure-Diagnoseprotokollen|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs](../azure-monitor/essentials/platform-logs-overview.md)|
|Unterstützte Dienste für Diagnoseprotokolle|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema](../azure-monitor/essentials/resource-logs-schema.md)|
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy erzwingt Regeln für die Bereitstellung von Ressourcen, über die beispielsweise die Art, der Speicherort und die Konfiguration festgelegt werden. Azure Policy kann so konfiguriert werden, dass Ressourcen nur bereitgestellt werden können, wenn sie den Anforderungen entsprechen. Azure Policy ist eine Kernkomponente zur Aufrechterhaltung der Integrität einer Azure-Umgebung. Für Azure Policy relevante Ereignisse werden in Azure-Aktivitätsprotokollen erfasst und können automatisch in Log Analytics-Lösungen verwendet und zur Verarbeitung oder Aufbewahrung an Event Hubs oder Azure Storage gesendet werden.

|Ressourcen|Link|
|---|---|
|Dokumentation zu Azure Policy|[https://docs.microsoft.com/azure/governance/policy](../governance/policy/index.yml)|
|Verwendung von Azure Policy- und Resource Manager-Vorlagen mithilfe von Azure Blueprints|[https://docs.microsoft.com/azure/governance/blueprints/overview](../governance/blueprints/overview.md)|
|

## <a name="log-collection"></a>Protokollsammlung

Sobald Protokolle aus den Protokollquellen generiert wurden, müssen sie an einem zentralisierten Speicherort gespeichert werden, damit auf sie zugegriffen werden kann und sie analysiert werden können. Azure bietet mehrere Methoden und Optionen für die Protokollsammlung, die je nach Art des Protokolls und Anforderungen verwendet werden können.

### <a name="event-hubs"></a>Event Hubs

In einem Event Hub werden die Protokolldaten zur Verteilung an verschiedene Quellen aggregiert. Aus dem Event Hub können Protokolle an ein SIEM-System, an das ACSC zur Complianceprüfung und an Azure Storage für die Langzeitaufbewahrung gesendet werden.

|Ressourcen|Link|
|---|---|
|Event Hubs-Dokumentation|[https://docs.microsoft.com/azure/event-hubs](../event-hubs/index.yml)|
|Leitfaden für Event Hubs und externe Tools|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)|
|

### <a name="log-analytics"></a>Log Analytics

Log Analytics ist eine Komponente von Azure Monitor, die für die Protokollanalyse verwendet wird. In Log Analytics wird ein Arbeitsbereich als Speichermechanismus verwendet, in dem Protokolldaten für verschiedene Analysetools und -lösungen in Azure zur Verfügung gestellt werden können. Log Analytics ist mit vielen Azure-Komponenten sowie in Virtual Machines über Microsoft Monitoring Agent direkt integriert.

|Ressourcen|Link|
|---|---|
|Dokumentation zu Log Analytics|[https://docs.microsoft.com/azure/azure-monitor](../azure-monitor/index.yml)|
|Tutorial: Analysieren von Daten in Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata](../azure-monitor/logs/log-analytics-tutorial.md)|
|

### <a name="network-watcher"></a>Network Watcher

Das ACSC empfiehlt die Verwendung von Network Watcher zur Erfassung und Analyse des Netzwerkdatenverkehrs in einem Azure-Abonnement. NSG-Datenflussprotokolle senden Daten an die Traffic Analytics-Lösung in Log Analytics, damit die verbesserte native Sichtbarkeit, Berichterstellung sowie die verbesserten Analysen über Azure genutzt werden können. Mit Network Watcher können Pakete auch direkt über das Azure-Portal erfasst werden, sodass keine Anmeldung auf dem virtuellen Computer erforderlich ist. Mithilfe der Paketerfassung können Sie Sitzungen erfassen, um den eingehenden und ausgehenden Datenverkehr eines virtuellen Computers nachzuverfolgen.

|Ressourcen|Link|
|---|---|
|Network Watcher|[https://docs.microsoft.com/azure/network-watcher](../network-watcher/index.yml)|
|Übersicht zur Paketerfassung|[https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview](../network-watcher/network-watcher-packet-capture-overview.md)|
|

## <a name="log-retention"></a>Protokollaufbewahrung

Zur australischen Regierung gehörige Organisationen müssen die in Azure erfassten Protokolle gemäß der [Administrative Functions Disposal Authority (AFDA)](https://www.naa.gov.au/information-management/records-authorities/types-records-authorities/afda-express-version-2-functions) der National Archives of Australia aufbewahren. Dort ist eine Aufbewahrungsdauer von bis zu sieben Jahren festgelegt.

|Protokollspeicherort|Bindungsdauer|
|---|---|
|Azure-Aktivitätsprotokoll|bis zu 90 Tage|
|Log Analytics-Arbeitsbereich|bis zu zwei Jahre|
|Event Hub|bis zu sieben Tage|
|

Sie sind dafür verantwortlich, dass die Protokolle gemäß der AFDA und sonstigen gesetzlichen Regelungen entsprechend archiviert werden.

### <a name="azure-storage"></a>Azure Storage

Azure Storage ist ein Repository, das dafür verwendet wird, Protokolle langfristig in Azure aufzubewahren. Azure Storage kann zum Archivieren der Azure-Protokolle verwendet werden, einschließlich Event Hubs-Protokollen, Azure-Aktivitätsprotokollen und Azure-Diagnoseprotokollen. Die Aufbewahrungsdauer für Daten kann in Azure Storage kann auf „0“ oder eine bestimmte Anzahl von Tagen festgelegt werden. Eine Aufbewahrungsdauer von 0 Tagen bedeutet, dass die Protokolle unbegrenzt lang aufbewahrt werden. Andernfalls können die Protokolle von 1 bis 2147483647 Tagen aufbewahrt werden.

|Ressourcen|Link|
|---|---|
|Dokumentation für Azure Storage|[https://docs.microsoft.com/azure/storage](../storage/index.yml)|
|Erfassen von Ereignissen über Azure Event Hubs in Azure Blob Storage oder Azure Data Lake Storage|[https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview](../event-hubs/event-hubs-capture-overview.md)|
|Tutorial: Archivieren von Azure-Metriken und -Protokolldaten mithilfe von Azure Storage|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data](../azure-monitor/essentials/platform-logs-overview.md)|
|Azure Storage-Replikation|[https://docs.microsoft.com/azure/storage/common/storage-redundancy](../storage/common/storage-redundancy.md)|
|Erstellen einer Momentaufnahme eines Blobs|[https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob)|
|

## <a name="log-analysis"></a>Protokollanalyse

Sobald die Protokolle generiert und an einem zentralisierten Speicherort gespeichert wurden, müssen sie analysiert werden, um für die Erkennung von versuchten oder erfolgreichen Sicherheitsvorfällen eingesetzt werden zu können. Wenn Sicherheitsvorfälle erkannt werden, muss eine Agentur auf diese Vorfälle reagieren und Bedrohungen nachverfolgen, isolieren und beheben können.

### <a name="microsoft-defender-for-cloud"></a>Microsoft Defender für Cloud

Microsoft Defender for Cloud bietet eine einheitliche Sicherheitsverwaltung und erweiterten Bedrohungsschutz. In Microsoft Defender für Cloud können Sie Sicherheitsrichtlinien auf Ihre Workloads anwenden, die Angriffsfläche für Bedrohungen verringern sowie Angriffe erkennen und darauf reagieren. Microsoft Defender für Cloud bietet Dashboards und Analysen für viele Azure-Komponenten. Die Verwendung von Microsoft Defender für Cloud wird im ACSC-Leitfaden für Consumer als Anforderung angegeben.

|Ressourcen|Link|
|---|---|
|Dokumentation zu Microsoft Defender for Cloud|[https://docs.microsoft.com/azure/security-center](../security-center/index.yml)|
|Schnellstart: Aktivieren der erweiterten Sicherheitsfeatures von Microsoft Defender für Cloud|[https://docs.microsoft.com/azure/security-center/security-center-get-started](../security-center/enable-enhanced-security.md)|
|||

### <a name="traffic-analytics"></a>Traffic Analytics

Traffic Analytics ist eine cloudbasierte Lösung, die Einblick in Benutzer- und Anwendungsaktivitäten in Azure bietet. Traffic Analytics analysiert die Flussprotokolle von Network Watcher für NSGs, um Einblicke in den Datenfluss in Azure zu ermöglichen. Mit Traffic Analytics werden Dashboards, Berichte, Analysen und Antwortfunktionen für Ereignisse bereitgestellt, die sich auf den Netzwerkdatenverkehr in virtuellen Netzwerken beziehen. Traffic Analytics bietet bedeutende Einblicke und hilft bei der Erkennung und Behebung von Cybersicherheitsvorfällen.

|Ressourcen|Link|
|---|---|
|Dokumentation zu Traffic Analytics|[https://docs.microsoft.com/azure/network-watcher/traffic-analytics](../network-watcher/traffic-analytics.md)|
|

### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor analysiert Ressourcenkonfigurationen und andere Daten, um Lösungen zu empfehlen, mit denen die Leistung, Sicherheit und Hochverfügbarkeit der Ressourcen verbessert werden kann, während zudem versucht wird, die Gesamtkosten für Azure zu reduzieren. Das ACSC empfiehlt den Einsatz von Azure Advisor und stellt leicht zugängliche und ausführliche Empfehlungen zur Konfiguration der Azure-Umgebung bereit.

|Ressourcen|Link|
|---|---|
|Azure Advisor-Dokumentation|[https://docs.microsoft.com/azure/advisor](../advisor/index.yml)|
|Erste Schritte mit Azure Advisor|[https://docs.microsoft.com/azure/advisor/advisor-get-started](../advisor/advisor-get-started.md)|
|

### <a name="dns-analytics-preview"></a>DNS Analytics (Vorschauversion)

DNS Analytics ist eine Log Analytics-Lösung, die DNS-Analyse- und Überwachungsprotokolle und ähnliche Daten für Windows erfasst, analysiert und korreliert. DNS Analytics ermittelt Clients, die versuchen, schädliche Domänennamen, veraltete Ressourcendatensätze, häufig abgefragte Domänennamen und kommunikative DNS-Clients aufzulösen. DNS Analytics bietet zudem Einblicke in die Anforderungsauslastung auf DNS-Servern und Fehler bei der dynamischen DNS-Registrierung. Mit DNS Analytics werden Dashboards, Berichte, Analysen und Antwortfunktionen für Ereignisse bereitgestellt, die sich auf DNS-Abfragen in einer Azure-Umgebung beziehen. DNS Analytics bietet bedeutende Einblicke und hilft bei der Erkennung und Behebung von Cybersicherheitsvorfällen.

|Ressourcen|Link|
|---|---|
|DNS Analytics-Dokumentation|[https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics](../azure-monitor/insights/dns-analytics.md)|
|

### <a name="activity-log-analytics"></a>Aktivitätsprotokollanalyse

Die Aktivitätsprotokollanalyse ist eine Log Analytics-Lösung, mit der die Azure-Aktivitätsprotokoll aus mehreren Azure-Abonnements analysiert und durchsucht werden können. Sie wird verwendet, um zentralisierte Dashboards, Berichte, Analysen und Antwortfunktionen für Ereignisse bereitzustellen, die sich auf Aktionen beziehen, die für Ressourcen in der gesamten Azure-Umgebung ausgeführt werden. Die Aktivitätsprotokollanalyse kann für die Überwachung und Untersuchung eingesetzt werden.

|Ressourcen|Link|
|---|---|
|Erfassen und Analysieren von Azure-Aktivitätsprotokollen in Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs](../azure-monitor/essentials/activity-log.md)|
|

### <a name="security-information-and-event-management-siem"></a>Security Information & Event Management (SIEM)

Ein SIEM-System speichert, überwacht und analysiert Sicherheitsprotokolle zentralisiert. Dabei kommen festgelegte Mechanismen für die Erfassung unterschiedlicher Protokolldaten sowie intelligente Tools für die Analyse, Berichterstellung, die Ermittlung von Vorfällen und die entsprechende Reaktion auf Vorfälle zum Einsatz. Sie können SIEM-Funktionen nutzen, die Azure-Protokollierungsinformationen enthalten, um die nativen Sicherheitsfunktionen in Azure zu ergänzen. Commonwealth-Entitäten können ein in Azure Virtual Machines gehostetes SIEM-System je nach Anforderungen lokal oder als SaaS-Funktion (Software-as-a-Service) nutzen.

|Ressourcen|Link|
|---|---|
|Microsoft Sentinel (Vorschau)|[https://azure.microsoft.com/services/azure-Sentinel](https://azure.microsoft.com/services/azure-sentinel)|
|SIEM-Dokumentation|Weitere Informationen zur SIEM-Architektur finden Sie in der Dokumentation des Anbieters.|
|Verwenden von Azure Monitor zur Integration mit SIEM-Tools|[https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)|
|

### <a name="australian-cyber-security-centre"></a>Australian Cyber Security Centre

Das Australien Cyber Security Centre (ACSC) ist die führende Organisation für die nationale Cybersicherheit, die von der australischen Regierung beauftragt wurde. Sie vereint die Cybersicherheitsfunktionen der australischen Regierung, um die Cyberresilienz der australischen Gesellschaft zu verbessern und den wirtschaftlichen und sozialen Wohlstand Australiens im digitalen Zeitalter zu stützen. Das Australian Cyber Security Centre empfiehlt allen Commonwealth-Entitäten, sämtliche vorgeschriebenen und vom System generierten Protokolldateien, Ereignisse und Protokolle an das ASCS zu schicken, damit diese durch die australische Regierung überwacht werden.

|Ressourcen|Link|
|---|---|
|Website des Australian Cyber Security Centre|[https://www.acsc.gov.au](https://www.acsc.gov.au)|
|

## <a name="incident-response"></a>Reaktion auf Incidents

Durch das Generieren der entsprechenden Protokolle, das Sammeln in zentralisierten Repositorys und durch Analysen können Systeme besser überwacht und Mechanismen zur Erkennung von Cybersicherheitsvorfällen bereitgestellt werden. Nachdem Vorfälle oder Ereignisse erkannt wurden, besteht der nächste Schritt darin, auf diese Ereignisse zu reagieren und Aktionen auszuführen, um die Systemintegrität aufrechtzuerhalten und Dienste und Daten vor Kompromittierung zu schützen. Azure bietet eine Kombination aus Diensten, mit denen effektiv auf alle auftretenden Ereignisse zu reagiert werden kann.

### <a name="azure-alerts"></a>Azure-Warnungen

Azure-Warnungen können verwendet werden, um Support- und Sicherheitsmitarbeiter infolge bestimmter Ereignisse zu benachrichtigen. So können Commonwealth-Entitäten proaktiv auf erkannte relevante Ereignisse reagieren, die von den Analysediensten ausgelöst wurden, die in diesem Artikel aufgeführt wurden.

|Ressourcen|Link|
|---|---|
|Übersicht über Warnungen in Microsoft Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts](../azure-monitor/alerts/alerts-overview.md)|
|Verwalten von und Reagieren auf Sicherheitswarnungen in Microsoft Defender für Cloud|[https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts](../security-center/security-center-managing-and-responding-alerts.md)|
|Azure Monitor-Protokollwarnungen|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response](../azure-monitor/alerts/alerts-log.md)|
|

### <a name="azure-automation"></a>Azure Automation

Mit Azure Automation können Commonwealth-Entitäten Aktionen als Reaktion auf Ereignisse auslösen. So kann beispielsweise die Paketerfassung auf virtuellen Computern gestartet, ein Workload ausgeführt oder ein Dienst oder virtueller Computer beendet bzw. gestartet werden. Azure Automation ermöglicht schnelle Reaktionen auf Warnungen, für die kein manuelles Eingreifen erforderlich ist, sodass die Antwortzeit und der Schweregrad eines Vorfalls oder Ereignisses verringert werden können.

|Ressourcen|Link|
|---|---|
|Dokumentation zu Azure Automation|[https://docs.microsoft.com/azure/automation](../automation/index.yml)|
|Schrittanleitung: Verwenden einer Warnung zum Auslösen eines Azure Automation-Runbooks|[https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook](../automation/automation-create-alert-triggered-runbook.md)|
|

## <a name="next-steps"></a>Nächste Schritte

Im Artikel zur [sicheren Remoteverwaltung von Gateways](gateway-secure-remote-administration.md) finden Sie weitere Informationen zur sicheren Verwaltung Ihrer Gatewayumgebung in Azure.
