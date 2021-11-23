---
title: Was ist Azure Firewall?
description: Azure Firewall ist ein verwalteter, cloudbasierter Netzwerksicherheitsdienst, der Ihre Azure Virtual Network-Ressourcen schützt.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperf-fy21q1
ms.date: 11/10/2021
ms.author: victorh
ms.openlocfilehash: 54f5de052c6fed4729a41e9f54c614480efc1e7e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309921"
---
# <a name="what-is-azure-firewall"></a>Was ist Azure Firewall?

<!--- ![ICSA certification](media/overview/icsa-cert-firewall-small.png) --->

Azure Firewall ist ein cloudnativer, intelligenter Netzwerkfirewall-Sicherheitsdienst, der erstklassigen Bedrohungsschutz für Ihre cloudbasierten, in Azure ausgeführten Workloads bietet. Es handelt sich hierbei um eine vollständig zustandsbehaftete Firewall-as-a-Service-Lösung mit integrierter Hochverfügbarkeit und uneingeschränkter Cloudskalierbarkeit. Sie ermöglicht die Untersuchung von Ost-West- und Nord-Süd-Datenverkehr.

Azure Firewall wird in zwei SKUs angeboten: Standard und Premium.

## <a name="azure-firewall-standard"></a>Azure Firewall Standard

   Azure Firewall Standard bietet L3- bis L7-Filterung und Threat Intelligence-Feeds direkt von Microsoft Cyber Security. Die auf Threat Intelligence basierende Filterung kann Warnungen generieren und ein- bzw. ausgehenden Datenverkehr für als schädlich bekannte IP-Adressen und Domänen verweigern. Diese werden in Echtzeit aktualisiert, um vor neuen und sich entwickelnden Angriffen zu schützen.

   ![Übersicht über Firewall Standard](media/overview/firewall-standard.png)

Weitere Informationen zu Firewall Standard-Features finden Sie unter [Azure Firewall Standard-Features](features.md).


## <a name="azure-firewall-premium"></a>Azure Firewall Premium

   Azure Firewall Premium bietet erweiterte Funktionen wie signaturbasiertes IDPS, um eine schnelle Erkennung von Angriffen zu ermöglichen, indem nach bestimmten Mustern gesucht wird. Bei diesen Mustern kann es sich um Bytesequenzen im Netzwerkdatenverkehr oder um bekannte schädliche Anweisungssequenzen handeln, die von Schadsoftware verwendet werden. Es gibt über 58.000 Signaturen in über 50 Kategorien. Diese werden in Echtzeit aktualisiert, um vor neuen und sich entwickelnden Exploits zu schützen. Zu den Exploitkategorien zählen unter anderem Schadsoftware, Phishing, Coin Mining und Trojaner.

   ![Übersicht über Firewall Premium](media/overview/firewall-premium.png)


Weitere Informationen zu Firewall Premium-Features finden Sie unter [Azure Firewall Premium-Features](premium-features.md).


## <a name="azure-firewall-manager"></a>Azure Firewall Manager

Azure Firewall Manager ermöglicht die zentrale Verwaltung von Azure Firewall-Instanzen für mehrere Abonnements. Firewall Manager nutzt die Firewallrichtlinie, um einen allgemeinen Satz von Netzwerk-/Anwendungsregeln und -konfigurationen auf die Firewalls in Ihrem Mandanten anzuwenden.
 
Firewall Manager unterstützt Firewalls in VNet- und Virtual WAN-Umgebungen (sicherer virtueller Hub). Sichere virtuelle Hubs verwenden die Virtual WAN-Routenautomatisierungslösung, um die Weiterleitung von Datenverkehr an die Firewall mit wenigen Klicks zu vereinfachen.

Weitere Informationen zu Azure Firewall Manager finden Sie unter [Was ist Azure Firewall Manager?](../firewall-manager/overview.md).

## <a name="pricing-and-sla"></a>Preise und SLA

Preisinformationen zu Azure Firewall finden Sie unter [Azure Firewall – Preise](https://azure.microsoft.com/pricing/details/azure-firewall/).

SLA-Informationen zu Azure Firewall finden Sie unter [Azure Firewall – SLA](https://azure.microsoft.com/support/legal/sla/azure-firewall/).

## <a name="whats-new"></a>Neues

Informationen zu den Neuerungen in Azure Firewall finden Sie unter [Azure-Updates](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Firewall).


## <a name="known-issues"></a>Bekannte Probleme

Azure Firewall weist die folgenden bekannten Probleme auf:

|Problem  |BESCHREIBUNG  |Minderung  |
|---------|---------|---------|
|Netzwerkfilterregeln für andere Protokolle als TCP/UDP (z.B. ICMP) funktionieren nicht für den Internetdatenverkehr|Netzwerkfilterregeln für andere Protokolle als TCP/UDP funktionieren nicht mit SNAT für Ihre öffentliche IP-Adresse. Nicht-TCP/UDP-Protokolle werden zwischen Spoke-Subnetzen und VNets unterstützt.|Azure Firewall verwendet Standard Load Balancer, [das SNAT für IP-Protokolle derzeit nicht unterstützt](../load-balancer/load-balancer-overview.md). Wir prüfen Möglichkeiten, um dieses Szenario in einer zukünftigen Version zu unterstützen.|
|Fehlende PowerShell- und CLI-Unterstützung für ICMP|Azure PowerShell und CLI weisen keine Unterstützung von ICMP als gültiges Protokoll in Netzwerkregeln auf.|Es ist weiterhin möglich, ICMP über das Portal und die REST-API als Protokoll zu verwenden. Wir arbeiten daran, ICMP in Kürze in PowerShell und CLI hinzuzufügen.|
|Für FQDN-Tags muss ein Protokoll/Port festgelegt werden|Für Anwendungsregeln mit FQDN-Tags ist eine „Port: Protokoll“-Definition erforderlich.|Sie können **https** als port:-Protokollwert verwenden. Wir arbeiten daran, dieses Feld optional zu machen, wenn FQDN-Tags verwendet werden.|
|Das Verlagern einer Firewall in eine andere Ressourcengruppe oder ein anderes Abonnement wird nicht unterstützt|Das Verlagern einer Firewall in eine andere Ressourcengruppe oder ein anderes Abonnement wird nicht unterstützt.|Die Unterstützung dieser Funktionalität ist Teil unserer Roadmap. Um eine Firewall in eine andere Ressourcengruppe oder ein anderes Abonnement zu verschieben, müssen Sie die aktuelle Instanz löschen und in der neuen Ressourcengruppe bzw. im Abonnement neu erstellen.|
|Threat Intelligence-Warnungen sind möglicherweise maskiert.|Netzwerkregeln mit Ziel 80/443 für ausgehende Filterung maskieren Threat Intelligence-Warnungen, wenn diese für den Modus „Alert only“ (Nur Warnen) konfiguriert sind.|Erstellen Sie die ausgehende Filterung für 80/443 mithilfe von Anwendungsregeln. Oder ändern Sie den Threat Intelligence-Modus zu **Alert and Deny** (Warnen und Verweigern).|
|Azure Firewall-DNAT funktioniert für private IP-Ziele nicht.|Die Azure Firewall-DNAT-Unterstützung ist auf eingehenden/ausgehenden Internetdatenverkehr beschränkt. DNAT funktioniert derzeit nicht für private IP-Ziele. Beispiel: Spoke zu Spoke.|Dies ist eine aktuelle Beschränkung.|
|Erste Konfiguration der öffentlichen IP-Adresse kann nicht entfernt werden|Jede öffentliche Azure Firewall-IP-Adresse ist einer *IP-Konfiguration* zugewiesen.  Die erste IP-Konfiguration wird während der Bereitstellung der Firewall zugewiesen und enthält in der Regel auch einen Verweis auf das Firewallsubnetz (sofern dies nicht über eine Vorlagenbereitstellung explizit anders konfiguriert wurde). Sie können diese IP-Konfiguration nicht löschen, weil damit die Zuordnung der Firewall aufgehoben würde. Sie können die öffentliche IP-Adresse, die dieser IP-Konfiguration zugeordnet ist, weiterhin ändern oder entfernen, wenn der Firewall mindestens eine andere öffentliche IP-Adresse zur Verwendung zur Verfügung steht.|Dies ist beabsichtigt.|
|Verfügbarkeitszonen können nur während der Bereitstellung konfiguriert werden.|Verfügbarkeitszonen können nur während der Bereitstellung konfiguriert werden. Sie können keine Verfügbarkeitszonen konfigurieren, nachdem eine Firewall bereitgestellt wurde.|Dies ist beabsichtigt.|
|SNAT für eingehende Verbindungen|Zusätzlich zu DNAT werden Verbindungen über die öffentliche IP-Adresse der Firewall (eingehend) per SNAT in eine der privaten öffentlichen IP-Adressen übersetzt. Diese Anforderung gilt derzeit (auch für Aktiv/Aktiv-NVAs), um symmetrisches Routing sicherzustellen.|Um die ursprüngliche Quelle für HTTP/S beizubehalten, können Sie [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For)-Header verwenden. Verwenden Sie beispielsweise einen Dienst wie [Azure Front Door](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) oder [Azure Application Gateway](../application-gateway/rewrite-http-headers-url.md) vor der Firewall. Sie können auch WAF als Teil von Azure Front Door verwenden und mit der Firewall verketten.
|SQL-FQDN-Filterung wird nur im Proxymodus unterstützt (Port 1433)|Für Azure SQL-Datenbank, Azure Synapse Analytics und Azure SQL Managed Instance:<br><br>Die SQL-FQDN-Filterung wird nur im Proxymodus unterstützt (Port 1433).<br><br>Für Azure SQL-IaaS gilt:<br><br>Wenn Sie keine Standardports verwenden, können Sie diese Ports in den Anwendungsregeln angeben.|Für SQL im Umleitungsmodus (die Standardeinstellung beim Herstellen von Verbindungen innerhalb von Azure) können Sie den Zugriff stattdessen mit dem SQL-Diensttag in den Azure Firewall-Netzwerkregeln filtern.
|Ausgehender SMTP-Datenverkehr an TCP-Port 25 ist blockiert.|Ausgehende E-Mail-Nachrichten, die direkt an externe Domänen (wie `outlook.com` und `gmail.com`) an TCP-Port 25 gesendet werden, werden von Azure Firewall blockiert. Dies ist das Standardverhalten der Plattform in Azure. |Verwenden Sie authentifizierte SMTP-Relaydienste, die in der Regel über TCP-Port 587 eine Verbindung herstellen, aber auch andere Ports unterstützen.  Weitere Informationen finden Sie unter [Behandeln von Problemen mit ausgehenden SMTP-Verbindungen in Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Derzeit kann Azure Firewall möglicherweise über den ausgehenden TCP-Port 25 mit öffentlichen IP-Adressen kommunizieren, aber es ist weder garantiert, dass dies funktioniert, noch wird es für alle Abonnementtypen unterstützt. Für private IP-Adressen wie virtuelle Netzwerke, VPNs und Azure ExpressRoute unterstützt Azure Firewall eine ausgehende Verbindung an TCP-Port 25.
|SNAT-Portauslastung|Azure Firewall unterstützt derzeit 1.024 Ports pro öffentlicher IP-Adresse und Back-End-VM-Skalierungsgruppeninstanz. Standardmäßig gibt es zwei VM-Skalierungsgruppeninstanzen.|Dies ist eine SLB-Einschränkung, und wir suchen ständig nach Möglichkeiten, die Grenzwerte zu erhöhen. In der Zwischenzeit wird empfohlen, Azure Firewall-Bereitstellungen mit mindestens fünf öffentlichen IP-Adressen für Bereitstellungen zu konfigurieren, die für SNAT-Auslastung anfällig sind. Dadurch werden die verfügbaren SNAT-Ports um das Fünffache erhöht. Verwenden Sie als Grundlage für die Zuordnung ein IP-Adresspräfix, um Downstreamberechtigungen zu vereinfachen.|
|DNAT wird bei aktivierter Tunnelerzwingung nicht unterstützt.|Mit aktivierter Tunnelerzwingung bereitgestellte Firewalls können aufgrund des asymmetrischen Routings keinen eingehenden Zugriff über das Internet unterstützen.|Dies ist systembedingt und auf das asymmetrische Routing zurückzuführen. Der Rückgabepfad für eingehende Verbindungen verläuft über die lokale Firewall, der noch nicht bekannt ist, dass die Verbindung hergestellt wurde.
|Abhängig von Ihrer FTP-Serverkonfiguration kann ausgehendes passives FTP für Firewalls mit mehreren öffentlichen IP-Adressen unter Umständen nicht verwendet werden.|Durch passives FTP werden unterschiedliche Verbindungen für Steuerungs- und Datenkanäle hergestellt. Wenn eine Firewall mit mehreren öffentlichen IP-Adressen ausgehende Daten sendet, wird nach dem Zufallsprinzip eine der öffentlichen IP-Adressen als Quell-IP-Adresse ausgewählt. Abhängig von Ihrer FTP-Serverkonfiguration funktioniert FTP möglicherweise nicht, wenn für Daten- und Steuerungskanäle unterschiedliche Quell-IP-Adressen verwendet werden.|Eine explizite SNAT-Konfiguration ist geplant. In der Zwischenzeit können Sie den FTP-Server so konfigurieren, dass er Daten- und Steuerungskanäle von verschiedenen Quell-IP-Adressen akzeptiert (siehe ein [Beispiel für IIS](/iis/configuration/system.applicationhost/sites/sitedefaults/ftpserver/security/datachannelsecurity)). Erwägen Sie in einem solchen Fall alternativ die Verwendung einer einzelnen IP-Adresse.|
|Abhängig von Ihrer FTP-Serverkonfiguration kann eingehendes passives FTP möglicherweise nicht verwendet werden. |Durch passives FTP werden unterschiedliche Verbindungen für Steuerungs- und Datenkanäle hergestellt. Eingehende Verbindungen an Azure Firewall werden per SNAT in eine der privaten Firewall-IP-Adressen übersetzt, um symmetrisches Routing sicherzustellen. Abhängig von Ihrer FTP-Serverkonfiguration funktioniert FTP möglicherweise nicht, wenn für Daten- und Steuerungskanäle unterschiedliche Quell-IP-Adressen verwendet werden.|Die Beibehaltung der ursprünglichen Quell-IP-Adresse wird zurzeit untersucht. In der Zwischenzeit können Sie den FTP-Server so konfigurieren, dass er Daten- und Steuerungskanäle von verschiedenen Quell-IP-Adressen akzeptiert.|
|Aktives FTP funktioniert nicht, wenn der FTP-Client einen FTP-Server über das Internet erreichen muss.|Aktives FTP verwendet einen PORT-Befehl vom FTP-Client, der dem FTP-Server mitteilt, welche IP-Adresse und welcher Port für den Datenkanal verwendet werden soll. Dieser PORT-Befehl verwendet die private IP-Adresse des Clients, die nicht geändert werden kann. Clientseitiger Datenverkehr, der die Azure Firewall passiert, wird für die internetbasierte Kommunikation per NAT übersetzt, sodass der PORT-Befehl vom FTP-Server als ungültig angesehen wird.|Dies ist eine allgemeine Einschränkung von aktivem FTP bei Verwendung in Kombination mit einer clientseitigen Netzwerkadressenübersetzung (Network Address Translation, NAT).|
|Für die Metrik „NetworkRuleHit“ fehlt eine Protokolldimension.|Die Metrik „ApplicationRuleHit“ ermöglicht protokollbasiertes Filtern, diese Funktion fehlt jedoch in der entsprechenden Metrik vom Typ „NetworkRuleHit“.|Es wird bereits nach einer Lösung gesucht.|
|NAT-Regeln mit Ports zwischen 64000 und 65535 werden nicht unterstützt.|Azure Firewall lässt beliebige Ports im Bereich 1-65535 in Netzwerk- und Anwendungsregeln zu. NAT-Regeln unterstützen jedoch nur Ports im Bereich 1-63999.|Dies ist eine aktuelle Beschränkung.
|Konfigurationsaktualisierungen können durchschnittlich fünf Minuten dauern.|Eine Aktualisierung der Azure Firewall-Konfiguration kann durchschnittlich zwischen drei und fünf Minuten dauern, und parallele Aktualisierungen werden nicht unterstützt.|Es wird bereits nach einer Lösung gesucht.|
|Azure Firewall verwendet SNI-TLS-Header zum Filtern von HTTPS- und MSSQL-Datenverkehr.|Wenn die Browser- oder Serversoftware die Erweiterung für die Servernamensanzeige (Server Name Indication, SNI) nicht unterstützt, kann keine Verbindung über Azure Firewall hergestellt werden.|Falls SNI von der Browser- oder Serversoftware nicht unterstützt wird, kann die Verbindung ggf. mithilfe einer Netzwerkregel (anstelle einer Anwendungsregel) gesteuert werden. Eine Liste der Software, die SNI unterstützt, finden Sie unter [Server Name Indication](https://wikipedia.org/wiki/Server_Name_Indication).|
|Benutzerdefiniertes DNS kann nicht mit Tunnelerzwingung verwendet werden.|Ist Tunnelerzwingung aktiviert, funktioniert benutzerdefiniertes DNS nicht.|Es wird bereits nach einer Lösung gesucht.|
|Starten/Beenden funktioniert nicht bei einer Firewall, für die der Tunnelerzwingungsmodus konfiguriert ist.|Starten/Beenden funktioniert nicht bei einer Azure Firewall-Instanz, für die der Tunnelerzwingungsmodus konfiguriert ist. Der Versuch, Azure Firewall mit konfigurierter Tunnelerzwingung zu starten, führt zu folgendem Fehler:<br><br>*Set-AzFirewall: Es ist nicht möglich, die Verwaltungs-IP-Konfiguration für AzureFirewall "FW-xx" einer vorhandenen Firewall hinzuzufügen. Wiederholen Sie die Bereitstellung mit einer Verwaltungs-IP-Konfiguration, wenn Sie eine Tunnelerzwingung unterstützen möchten.<br>Statuscode: 400<br>ReasonPhrase: Ungültige Anforderung*|Wird untersucht<br><br>Um das Problem zu umgehen, können Sie die vorhandene Firewall löschen und eine neue mit denselben Parametern erstellen.|
|Firewallrichtlinientags können nicht über das Portal oder ARM-Vorlagen (Azure Resource Manager) hinzugefügt werden.|Für die Azure Firewall-Richtlinie gilt eine Patchunterstützungseinschränkung, die verhindert, dass Sie über das Azure-Portal oder ARM-Vorlagen ein Tag hinzufügen können. Der folgende Fehler wird generiert: *Die Tags für die Ressource konnten nicht gespeichert werden*.|Es wird bereits nach einer Lösung gesucht. Alternativ können Sie das Azure PowerShell-Cmdlet `Set-AzFirewallPolicy` verwenden, um Tags zu aktualisieren.|
|IPv6 wird derzeit nicht unterstützt|Wenn Sie einer Regel eine IPv6-Adresse hinzufügen, tritt ein Firewallfehler auf.|Verwenden Sie nur IPv4-Adressen. Die IPv6-Unterstützung wird geprüft.|
|Das Aktualisieren mehrerer IP-Gruppen schlägt mit einem Konfliktfehler fehl.|Wenn Sie zwei oder mehr IP-Gruppen aktualisieren, die an dieselbe Firewall angefügt sind, wechselt eine der Ressourcen in den Fehlerzustand.|Dies ist ein bekanntes Problem/eine bekannte Einschränkung. <br><br>Wenn Sie eine IP-Gruppe aktualisieren, wird für alle Firewalls, an die die IP-Gruppe angefügt ist, ein Update ausgelöst. Wenn ein Update zu einer zweiten IP-Gruppe gestartet wird, während sich die Firewall noch im Status *Aktualisieren* befindet, schlägt die Aktualisierung der IP-Gruppe fehl.<br><br>Um den Fehler zu vermeiden, müssen IP-Gruppe n, die an dieselbe Firewall angefügt sind, nacheinander aktualisiert werden. Lassen Sie zwischen den Updates genügend Zeit, damit die Firewall den Status *Aktualisieren* beenden kann.|
|Das Entfernen von RuleCollectionGroup-Elementen mit ARM-Vorlagen wird nicht unterstützt.|Das Entfernen eines RuleCollectionGroup-Elements mit ARM-Vorlagen wird nicht unterstützt und führt zu einem Fehler.|Dies ist kein unterstützter Vorgang.|
|Wenn die DNAT-Regel *allen* (*) Datenverkehr zulässt, erfolgt auch eine Quellnetzwerk-Adressübersetzung (Source Network Address Translation, SNAT).|Wenn eine DNAT-Regel *alle* (*) Quell-IP-Adressen zulässt, stimmt eine implizite Netzwerkregel mit dem VNet-VNet-Datenverkehr überein, sodass immer eine SNAT erfolgt.|Dies ist eine aktuelle Beschränkung.|
|Das Hinzufügen einer DNAT-Regel zu einem geschützten virtuellen Hub mit einem Sicherheitsanbieter wird nicht unterstützt.|Dies führt zu einer asynchronen Route für den an den Sicherheitsanbieter zurückgegebenen DNAT-Datenverkehr.|Wird nicht unterstützt.|
| Fehler, die beim Erstellen von mehr als 2.000 Regelsammlungen auftreten können. | Die maximale Anzahl von Regelsammlungen für NAT/Anwendung oder Netzwerk beträgt 2.000 (Resource Manager-Begrenzung). | Dies ist eine aktuelle Beschränkung. |
|Netzwerkregelname wird in Azure Firewall-Protokollen nicht angezeigt|Der Regelname für Netzwerkdatenverkehr wird in den Netzwerkregelprotokolldaten von Azure Firewall nicht angezeigt.|Ein Feature wird geprüft, um dies zu unterstützen.|

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Erstellen einer Azure Firewall-Instanz und einer Firewallrichtlinie: ARM-Vorlage](../firewall-manager/quick-firewall-policy.md)
- [Schnellstart: Bereitstellen von Azure Firewall mit Verfügbarkeitszonen – ARM-Vorlage](deploy-template.md)
- [Tutorial: Bereitstellen und Konfigurieren von Azure Firewall über das Azure-Portal](tutorial-firewall-deploy-portal.md)
- [Learn-Modul: Einführung in die Azure Firewall](/learn/modules/introduction-azure-firewall/)
