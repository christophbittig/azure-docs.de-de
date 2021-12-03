---
title: Verbindungsarchitektur
titleSuffix: Azure SQL Managed Instance
description: Erfahren Sie etwas über die Kommunikations- und Konnektivitätsarchitektur für Azure SQL Managed Instance sowie die Übertragung des Datenverkehrs an eine verwaltete Instanz mithilfe der Komponenten.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma, bonova
ms.date: 04/29/2021
ms.openlocfilehash: 04a275c0e4bc9c3eb52fee8b02ea2606e52d4958
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554064"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Konnektivitätsarchitektur für Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In diesem Artikel wird die Kommunikation in Azure SQL Managed Instance erläutert. Darüber hinaus wird die Konnektivitätsarchitektur beschrieben, und es wird erläutert, wie die Komponenten Datenverkehr an eine verwaltete Instanz weiterleiten.  

SQL Managed Instance wird im virtuellen Azure-Netzwerk und dem für verwaltete Instanzen dedizierten Subnetz platziert. Diese Bereitstellung bietet:

- Eine sichere private IP-Adresse.
- Die Möglichkeit, eine Verbindung von einem lokalen Netzwerk mit SQL Managed Instance herzustellen.
- Die Möglichkeit, eine Verbindung von SQL Managed Instance mit einem Verbindungsserver oder einem anderen lokalen Datenspeicher herzustellen.
- Die Möglichkeit, eine Verbindung von SQL Managed Instance mit Azure-Ressourcen herzustellen.

> [!div class="nextstepaction"]
> [Umfrage zur Verbesserung von Azure SQL](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="communication-overview"></a>Kommunikationsübersicht

Die folgende Abbildung zeigt Entitäten, die eine Verbindung mit SQL Managed Instance herstellen. Sie zeigt auch die Ressourcen, die mit einer verwalteten Instanz kommunizieren müssen. Der Kommunikationsvorgang im unteren Diagrammbereich bezieht sich auf Kundenanwendungen und Tools, die eine Verbindung mit SQL Managed Instance als Datenquelle herstellen.  

![Entitäten in der Konnektivitätsarchitektur](./media/connectivity-architecture-overview/connectivityarch001.png)

SQL Managed Instance ist ein PaaS-Angebot (Platform as a Service). Azure verwendet automatisierte Agents (Verwaltung, Bereitstellung und Wartung) zum Verwalten dieses Diensts basierend auf Telemetriedatenströmen. Da Azure für die Verwaltung zuständig ist, können Kunden nicht über Remote Desktop Protocol (RDP) auf die virtuellen Clustercomputer von SQL Managed Instance zugreifen.

Bei einigen Vorgängen, die von Endbenutzern oder Anwendungen gestartet werden, muss SQL Managed Instance möglicherweise mit der Plattform interagieren. Ein solcher Fall ist die Erstellung einer SQL Managed Instance-Datenbank. Diese Ressource wird über das Azure-Portal, PowerShell, Azure-Befehlszeilenschnittstelle und REST-API verfügbar gemacht.

SQL Managed Instance hängt von Azure-Diensten wie Azure Storage für Sicherungen, Azure Event Hubs für Telemetriedaten, Azure Active Directory (Azure AD) für die Authentifizierung, Azure Key Vault für Transparent Data Encryption (TDE) und einer Reihe von Azure-Plattformdiensten ab, die Sicherheits- und Supportfunktionen bereitstellen. SQL Managed Instance stellt Verbindungen mit diesen Diensten her.

Jeglicher Datenaustausch ist verschlüsselt und mithilfe von Zertifikaten signiert. Um die Vertrauenswürdigkeit der Kommunikationspartner sicherzustellen, überprüft SQL Managed Instance diese Zertifikate regelmäßig anhand von Zertifikatsperrlisten. Wenn die Zertifikate widerrufen werden, trennt SQL Managed Instance die Verbindungen zum Schutz der Daten.

## <a name="high-level-connectivity-architecture"></a>Verbindungsarchitektur auf Makroebene

Auf hoher Ebene ist SQL Managed Instance eine Gruppe von Dienstkomponenten. Diese Komponenten werden auf einer dedizierten Gruppe isolierter virtueller Computer gehostet, die im virtuellen Subnetz des Kunden ausgeführt werden. Diese Computer bilden einen virtuellen Cluster.

Ein virtueller Cluster kann mehrere verwaltete Instanzen hosten. Der Cluster wird ggf. automatisch vergrößert oder verkleinert, wenn der Kunde die Anzahl der bereitgestellten Instanzen im Subnetz ändert.

Kundenanwendungen können eine Verbindung mit SQL Managed Instance herstellen und Datenbanken innerhalb des virtuellen Netzwerks, des mittels Peering verknüpften virtuellen Netzwerks oder des durch VPN oder Azure ExpressRoute verbundenen Netzwerks abfragen und aktualisieren. Dieses Netzwerk muss einen Endpunkt und eine private IP-Adresse verwenden.  

![Diagramm zur Konnektivitätsarchitektur](./media/connectivity-architecture-overview/connectivityarch002.png)

Die Verwaltungs- und Bereitstellungsdienste von Azure werden außerhalb des virtuellen Netzwerks ausgeführt. SQL Managed Instance und Azure-Dienste sind über die Endpunkte verbunden, die öffentliche IP-Adressen besitzen. Wenn SQL Managed Instance eine ausgehende Verbindung herstellt, sieht die Verbindung auf der Empfängerseite aufgrund der Netzwerkadressenübersetzung (Network Address Translation, NAT) so aus, als käme sie von dieser öffentlichen IP-Adresse.

Der Verwaltungsdatenverkehr wird über das virtuelle Kundennetzwerk übertragen. Das bedeutet, dass die Elemente der Infrastruktur des virtuellen Netzwerks den Verwaltungsdatenverkehr beeinträchtigen können, indem sie einen Fehler bei der Instanz verursachen, sodass sie nicht mehr verfügbar ist.

> [!IMPORTANT]
> Azure nutzt zur Verbesserung der Dienstverfügbarkeit und -qualität Netzwerkzielrichtlinien für Infrastrukturelemente virtueller Azure-Netzwerke. Die Richtlinie kann Auswirkungen auf die Funktionsweise von SQL Managed Instance haben. Dieser Plattformmechanismus kommuniziert transparent Netzwerkanforderungen für Benutzer. Das Hauptziel der Richtlinie ist, Fehlkonfigurationen des Netzwerks zu verhindern und den normalen Betrieb von SQL Managed Instance zu gewährleisten. Wenn Sie eine verwaltete Instanz löschen, wird die Netzwerkzielrichtlinie ebenfalls entfernt.

## <a name="virtual-cluster-connectivity-architecture"></a>Verbindungsarchitektur für virtuellen Cluster

Im Folgenden wird die Konnektivitätsarchitektur für SQL Managed Instance noch genauer untersucht. Im unten abgebildeten Diagramm wird das konzeptionelle Layout des virtuellen Clusters dargestellt.

![Konnektivitätsarchitektur des virtuellen Clusters](./media/connectivity-architecture-overview/connectivityarch003.png)

Clients stellen mithilfe des Hostnamens, der in der Form `<mi_name>.<dns_zone>.database.windows.net` vorliegen muss, eine Verbindung mit SQL Managed Instance her. Dieser Hostname wird in eine private IP-Adresse aufgelöst, obwohl er in einer öffentlichen DNS-Zone (Domain Name System) registriert ist und öffentlich aufgelöst werden kann. Die `zone-id` wird beim Erstellen des Clusters automatisch generiert. Wenn in einem neu erstellten Cluster eine sekundäre verwaltete Instanz gehostet wird, verwendet diese die Zonen-ID gemeinsam mit dem primären Cluster. Weitere Informationen finden Sie unter [Verwenden von Autofailover-Gruppen für ein transparentes und koordiniertes Failover mehrerer Datenbanken](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Diese private IP-Adresse gehört zum internen Lastenausgleich von SQL Managed Instance. Der Lastenausgleich leitet Datenverkehr an das SQL Managed Instance-Gateway weiter. Da mehrere verwaltete Instanzen in demselben Cluster ausgeführt werden können, nutzt das Gateway den Hostnamen von SQL Managed Instance, um den Datenverkehr an den richtigen SQL-Engine-Dienst weiterzuleiten.

Verwaltungs- und Bereitstellungsdienste stellen über einen [Verwaltungsendpunkt](#management-endpoint), der einem externen Lastenausgleich zugeordnet ist, eine Verbindung mit SQL Managed Instance her. Der Datenverkehr wird nur an die Knoten weitergeleitet, wenn er an einer vordefinierten Gruppe von Ports empfangen wird, die ausschließlich die Verwaltungskomponenten von SQL Managed Instance verwenden. Eine integrierte Firewall auf den Knoten ist so eingerichtet, dass nur Datenverkehr von Microsoft-IP-Adressbereichen zulässig ist. Zertifikate authentifizieren gegenseitig die gesamte Kommunikation zwischen den Verwaltungskomponenten und der Verwaltungsebene.

## <a name="management-endpoint"></a>Verwaltungsendpunkt

Azure verwaltet SQL Managed Instance über einen Verwaltungsendpunkt. Dieser Endpunkt befindet sich in einem virtuellen Cluster der Instanz. Der Endpunkt für die Verwaltung wird durch eine integrierte Firewall auf Netzwerkebene geschützt. Auf der Anwendungsebene wird er durch gegenseitige Zertifikatüberprüfung geschützt. Die IP-Adresse des Endpunkts finden Sie unter [Ermitteln der IP-Adresse des Verwaltungsendpunkts](management-endpoint-find-ip-address.md).

Wenn Verbindungen ihren Ausgangspunkt in SQL Managed Instance haben (wie bei Sicherungen und Überwachungsprotokollen), scheint der Datenverkehr bei der öffentlichen IP-Adresse des Verwaltungsendpunkts zu beginnen. Sie können den Zugriff von SQL Managed Instance auf öffentliche Dienste einschränken, indem Sie Firewallregeln festlegen, die nur die IP-Adresse für SQL Managed Instance zulassen. Weitere Informationen finden Sie unter [Überprüfen der integrierten Firewall von SQL Managed Instance](management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Datenverkehr, der an Azure-Dienste innerhalb der Region von SQL Managed Instance gerichtet ist, wird optimiert und aus diesem Grund nicht über NAT an die öffentliche IP-Adresse des Verwaltungsendpunkts übermittelt. Wenn Sie IP-basierte Firewallregeln einsetzen müssen, meistens ist das bei Speicher der Fall, muss sich der Dienst daher in einer anderen Region als SQL Managed Instance befinden.

## <a name="service-aided-subnet-configuration"></a>Dienstgestützte Subnetzkonfiguration

Um Kundensicherheit und Verwaltbarkeitsanforderungen zu berücksichtigen, wird SQL Managed Instance gerade von der manuellen auf die dienstgestützte Subnetzkonfiguration umgestellt.

Mit dienstgestützter Subnetzkonfiguration kann der Kunde den Datenverkehr (Tabular Data Stream, TDS) vollständig kontrollieren, während die Steuerungsebene von SQL Managed Instance dafür zuständig ist, den ununterbrochenen Fluss des Verwaltungsdatenverkehrs zu gewährleisten und so eine Vereinbarung zum Servicelevel (Service Level Agreement, SLA) zu erfüllen.

Die dienstgestützte Subnetzkonfiguration baut auf dem Feature [Subnetzdelegierung](../../virtual-network/subnet-delegation-overview.md) für virtuelle Netzwerke auf, um eine automatische Verwaltung der Netzwerkkonfiguration zu ermöglichen und Dienstendpunkte zu aktivieren. 

Dienstendpunkte können zum Konfigurieren von Firewallregeln für virtuelle Netzwerke für Speicherkonten verwendet werden, in denen Sicherungen und Überwachungsprotokolle gespeichert werden. Selbst bei aktivierten Dienstendpunkten wird Kunden empfohlen, [private Links](../../private-link/private-link-overview.md) zu verwenden, die zusätzliche Sicherheit über Dienstendpunkte bieten.

> [!IMPORTANT]
> Aufgrund der Besonderheiten der Konfiguration der Steuerungsebene würde eine dienstgestützte Subnetzkonfiguration keine Dienstendpunkte in nationalen Clouddiensten ermöglichen. 

### <a name="network-requirements"></a>Netzwerkanforderungen

Stellen Sie SQL Managed Instance in einem dedizierten Subnetz im virtuellen Netzwerk bereit. Das Subnetz muss diese Merkmale aufweisen:

- **Dediziertes Subnetz:** Das Subnetz von SQL Managed Instance darf keinen anderen Clouddienst enthalten, der damit verbunden ist (andere verwaltete Instanzen sind jedoch zulässig), und darf kein Gatewaysubnetz sein. Das Subnetz darf keine Ressourcen außer den verwalteten Instanzen enthalten, und Sie können später keine Arten von Ressourcen im Subnetz hinzufügen.
- **Subnetzdelegierung:** Das Subnetz von SQL Managed Instance muss an den Ressourcenanbieter `Microsoft.Sql/managedInstances` delegiert werden.
- **Netzwerksicherheitsgruppe (NSG)** : Eine NSG muss dem Subnetz von SQL Managed Instance zugeordnet werden. Sie können eine NSG verwenden, um den Zugriff auf den Datenendpunkt von SQL Managed Instance zu steuern, indem Sie Datenverkehr an Port 1433 und die Ports 11000 bis 11999 filtern, wenn SQL Managed Instance für Verbindungsumleitungen konfiguriert ist. Der Dienst stellt automatisch die [Regeln](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) zur Verfügung, die erforderlich sind, um einen ununterbrochenen Fluss des Verwaltungsdatenverkehrs zu ermöglichen, und hält sie auf dem neuesten Stand.
- **Benutzerdefinierte Routingtabelle (User Defined Route, UDR):** Eine UDR-Tabelle muss dem Subnetz von SQL Managed Instance zugeordnet werden. Sie können der Routingtabelle Einträge hinzufügen, um Datenverkehr mit lokalen privaten IP-Bereichen als Ziel über das virtuelle Netzwerkgateway oder das virtuelle Netzwerkgerät (Network Appliance, NVA) zu leiten. Der Dienst stellt automatisch die [Einträge](#mandatory-user-defined-routes-with-service-aided-subnet-configuration) zur Verfügung, die erforderlich sind, um einen ununterbrochenen Fluss des Verwaltungsdatenverkehrs zu ermöglichen, und hält sie auf dem neuesten Stand.
- **Ausreichende IP-Adressen**: Das Subnetz von SQL Managed Instance muss mindestens 32 IP-Adressen umfassen. Weitere Informationen finden Sie unter [Ermitteln der Größe des Subnetzes für SQL Managed Instance](vnet-subnet-determine-size.md). Sie können verwaltete Instanzen im [vorhandenen Netzwerk](vnet-existing-add-subnet.md) bereitstellen, nachdem Sie dieses entsprechend den [Netzwerkanforderungen für SQL Managed Instance](#network-requirements) konfiguriert haben. Erstellen Sie andernfalls ein [neues Netzwerk und Subnetz](virtual-network-subnet-create-arm-template.md).
- **Zulässig durch Azure-Richtlinien:** Wenn Sie [Azure Policy](../../governance/policy/overview.md) verwenden, um das Erstellen oder Ändern von Ressourcen in dem Geltungsbereich zu verweigern, der das Subnetz/virtuelle Netzwerk von SQL Managed Instance umfasst, sollte Managed Instance durch solche Richtlinien nicht an der Verwaltung seiner internen Ressourcen gehindert werden. Die folgenden Ressourcen müssen von Verweigerungen ausgeschlossen werden, um den normalen Betrieb zu ermöglichen:
  - Ressourcen vom Typ „Microsoft.Network/serviceEndpointPolicies“, wenn der Ressourcenname mit \_e41f87a2\_ beginnt
  - Alle Ressourcen vom Typ „Microsoft.Network/networkIntentPolicies“
  - Alle Ressourcen vom Typ „Microsoft.Network/virtualNetworks/subnets/contextualServiceEndpointPolicies“
- **Sperren für virtuelles Netzwerk:** [Sperren](../../azure-resource-manager/management/lock-resources.md) für das virtuelle Netzwerk, die übergeordnete Ressourcengruppe oder das Abonnement des dedizierten Subnetzes können gelegentlich die Verwaltungs- und Wartungsvorgänge von SQL Managed Instance beeinträchtigen. Seien Sie besonders vorsichtig, wenn Sie solche Sperren verwenden.

> [!IMPORTANT]
> Wenn Sie eine verwaltete Instanz erstellen, wird eine Netzwerkzielrichtlinie auf das Subnetz angewendet, um nicht konforme Änderungen am Netzwerksetup zu verhindern. Nachdem die letzte Instanz aus dem Subnetz entfernt wurde, wird auch die Netzwerkzielrichtlinie entfernt. Die folgenden Regeln dienen nur zu Informationszwecken und sollten nicht mit ARM-Vorlagen, PowerShell oder der Befehlszeilenschnittstelle bereitgestellt werden. Wenn Sie die neueste offizielle Vorlage verwenden möchten, können Sie diese jederzeit [aus dem Portal abrufen](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Obligatorische Eingangssicherheitsregeln mit dienstgestützter Subnetzkonfiguration
Diese Regeln sind erforderlich, um den eingehenden Management-Verkehrsfluss zu gewährleisten. Für weitere Informationen zur Konnektivitätsarchitektur und zum Verwaltungsdatenverkehr lesen Sie bitte den [Absatz oben](#high-level-connectivity-architecture).

| Name       |Port                        |Protocol|`Source`           |Destination|Aktion|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |MI-SUBNETZ  |Allow |
|            |9000, 9003                  |TCP     |CorpnetSaw       |MI-SUBNETZ  |Allow |
|            |9000, 9003                  |TCP     |CorpnetPublic    |MI-SUBNETZ  |Allow |
|mi_subnet   |Any                         |Any     |MI-SUBNETZ        |MI-SUBNETZ  |Allow |
|health_probe|Any                         |Any     |AzureLoadBalancer|MI-SUBNETZ  |Allow |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Obligatorische Ausgangssicherheitsregeln mit dienstgestützter Subnetzkonfiguration
Diese Regeln sind erforderlich, um den ausgehenden Management-Verkehrsfluss zu gewährleisten. Für weitere Informationen zur Konnektivitätsarchitektur und zum Verwaltungsdatenverkehr lesen Sie bitte den [Absatz oben](#high-level-connectivity-architecture).

| Name       |Port          |Protocol|`Source`           |Destination|Aktion|
|------------|--------------|--------|-----------------|-----------|------|
|management  |443, 12000    |TCP     |MI-SUBNETZ        |AzureCloud |Allow |
|mi_subnet   |Any           |Any     |MI-SUBNETZ        |MI-SUBNETZ  |Allow |

### <a name="mandatory-user-defined-routes-with-service-aided-subnet-configuration"></a>Erforderliche benutzerdefinierte Routen mit dienstgestützter Subnetz-Konfiguration
Diese Routen sind erforderlich, um sicherzustellen, dass der Management-Datenverkehr direkt an ein Ziel geleitet wird. Für weitere Informationen zur Konnektivitätsarchitektur und zum Verwaltungsdatenverkehr lesen Sie bitte den [Absatz oben](#high-level-connectivity-architecture).

|Name|Adresspräfix|Nächster Hop|
|----|--------------|-------|
|subnet-to-vnetlocal|MI-SUBNETZ|Virtuelles Netzwerk|
|mi-azurecloud-REGION-internet|AzureCloud.REGION|Internet|
|mi-azurecloud-REGION_PAIR-internet|AzureCloud.REGION_PAIR|Internet|
|mi-azuremonitor-internet|AzureMonitor|Internet|
|mi-corpnetpublic-internet|CorpNetPublic|Internet|
|mi-corpnetsaw-internet|CorpNetSaw|Internet|
|mi-eventhub-REGION-internet|EventHub.REGION|Internet|
|mi-eventhub-REGION_PAIR-internet|EventHub.REGION_PAIR|Internet|
|mi-sqlmanagement-internet|SqlManagement|Internet|
|mi-storage-internet|Storage|Internet|
|mi-storage-REGION-internet|Storage.REGION|Internet|
|mi-storage-REGION_PAIR-internet|Storage.REGION_PAIR|Internet|
|mi-azureactivedirectory-internet|AzureActiveDirectory|Internet|
||||

\* MI-SUBNETZ bezieht sich auf den IP-Adressbereich für das Subnetz in der Form „x.x.x.x/y“. Diese Informationen finden Sie im Azure-Portal in den Subnetzeigenschaften.

\** Wenn die Zieladresse die Adresse von einem der Azure-Dienste ist, leitet Azure den Datenverkehr über das Azure-Backbonenetzwerk direkt an den Dienst weiter, und nicht über das Internet. Der Datenverkehr zwischen Azure-Diensten wird nicht über das Internet übertragen. Dies gilt unabhängig davon, in welcher Azure-Region das virtuelle Netzwerk vorhanden ist oder in welcher Azure-Region eine Instanz des Azure-Diensts bereitgestellt wird. Weitere Informationen finden Sie auf der [UDR-Dokumentationsseite](../../virtual-network/virtual-networks-udr-overview.md).

Darüber hinaus können Sie der Routingtabelle Einträge hinzufügen, um Datenverkehr mit lokalen privaten IP-Bereichen als Ziel über ein virtuelles Netzwerkgateway oder ein virtuelles Netzwerkgerät (Network Appliance, NVA) zu leiten.

Wenn das virtuelle Netzwerk ein benutzerdefiniertes DNS enthält, muss der benutzerdefinierte DNS-Server öffentliche DNS-Einträge auflösen können. Die Verwendung zusätzlicher Funktionen wie Azure AD Authentication macht unter Umständen auch die Auflösung zusätzlicher FQDNs erforderlich. Weitere Informationen finden Sie unter [Konfigurieren eines benutzerdefinierten DNS für eine verwaltete Azure SQL-Datenbank-Instanz](custom-dns-configure.md).

### <a name="networking-constraints"></a>Netzwerkeinschränkungen

**TLS 1.2 wird für ausgehende Verbindungen erzwungen**: Seit Januar 2020 erzwingt Microsoft in sämtlichen Azure-Diensten TLS 1.2 für den dienstinternen Datenverkehr. Für Azure SQL Managed Instance führte dies dazu, dass TLS 1.2 für ausgehende Verbindungen, die für die Replikation verwendet werden, und für verknüpfte Serververbindungen mit SQL Server erzwungen wird. Wenn Sie ältere Versionen von SQL Server als Version 2016 mit SQL Managed Instance verwenden, stellen Sie sicher, dass [TLS 1.2-spezifische Updates](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) angewandt wurden.

Die folgenden Features von virtuellen Netzwerken werden derzeit von SQL Managed Instance *nicht unterstützt*:

- **Microsoft-Peering**: Die Aktivierung von [Microsoft-Peering](../../expressroute/expressroute-faqs.md#microsoft-peering) für ExpressRoute-Verbindungen, die direkt oder transitiv mit einem virtuellen Netzwerk verbunden sind, in dem sich SQL Managed Instance befindet, wirkt sich auf den Datenverkehrsfluss zwischen den Komponenten von SQL Managed Instance innerhalb des virtuellen Netzwerks und den Diensten aus, von denen sie abhängt, und verursacht dadurch Verfügbarkeitsprobleme. SQL Managed Instance-Bereitstellungen im virtuellen Netzwerk mit bereits aktiviertem Microsoft-Peering schlagen wahrscheinlich fehl.
- **Globales Peering virtueller Netzwerke**: Konnektivität per [Peering virtueller Netzwerke](../../virtual-network/virtual-network-peering-overview.md) über Azure-Regionen hinweg funktioniert nicht für SQL Managed Instances in Subnetzen, die vor dem 22.09.2020 erstellt wurden.
- **AzurePlatformDNS**: Die Verwendung des AzurePlatformDNS-[Diensttags](../../virtual-network/service-tags-overview.md) zur Blockierung der DNS-Auflösung der Plattform würde dazu führen, dass SQL Managed Instance nicht mehr verfügbar ist. Obwohl SQL Managed Instance kundenspezifisches DNS für die DNS-Auflösung innerhalb der Engine unterstützt, besteht eine Abhängigkeit vom Plattform-DNS für Plattformvorgänge.
- **NAT-Gateway**: Die Verwendung von [Azure Virtual Network NAT](../../virtual-network/nat-gateway/nat-overview.md) zur Steuerung der ausgehenden Konnektivität mit einer bestimmten öffentlichen IP-Adresse würde dazu führen, dass SQL Managed Instance nicht erreichbar ist. Der SQL Managed Instance-Dienst ist aktuell auf die Verwendung eines Basislastenausgleichs beschränkt, der keine parallelen eingehenden und ausgehenden Flows für Virtual Network NAT zulässt.
- **IPv6 für Azure Virtual Network:** Bei der Bereitstellung von SQL Managed Instance in [virtuellen Netzwerken mit dualem IPv4/IPv6-Stapel](../../virtual-network/ip-services/ipv6-overview.md) wird ein Fehler erwartet. Das Zuordnen von Netzwerksicherheitsgruppen (NSG) oder Routingtabellen (UDR) mit IPv6-Adresspräfixen zu einem SQL Managed Instance-Subnetz oder das Hinzufügen von IPv6-Adresspräfixen zu einer NSG oder UDR, die bereits dem Managed Instance-Subnetz zugeordnet ist, würden dazu führen, dass SQL Managed Instance nicht verfügbar ist. Bei der Bereitstellung von SQL Managed Instance in einem Subnetz mit einer NSG und UDR, die bereits über IPv6-Präfixe verfügen, wird ein Fehler erwartet.
- **Azure DNS private Zonen mit Namen, die für Microsoft-Dienste reserviert sind**: Es folgt die Liste der reservierten Namen: windows.net, database.windows.net, core.windows.net, blob.core.windows.net, table.core.windows.net, management.core.windows.net, monitoring.core.windows.net, queue.core.windows.net, graph.windows.net, login.microsoftonline.com, login.windows.net, servicebus.windows.net, vault.azure.net. Die Bereitstellung von SQL Managed Instance in einem virtuellen Netzwerk mit zugehöriger [Azure DNS Private Zone](../../dns/private-dns-privatednszone.md) mit einem für Microsoft-Dienste reservierten Namen würde fehlschlagen. Wenn Sie eine private Azure-DNS-Zone mit reserviertem Namen mit einem virtuellen Netzwerk verknüpfen, welches eine Managed Instance enthält, dann ist die SQL Managed Instance nicht mehr verfügbar. Bitte folgen Sie der [Azure Private Endpoint DNS Konfiguration](../../private-link/private-endpoint-dns.md) für die richtige Private Link Konfiguration.
- **Dienstendpunktrichtlinien für Azure Storage**: Das Bereitstellen von SQL Managed Instance in einem Subnetz, dem [Service-Endpunktrichtlinien](../../virtual-network/virtual-network-service-endpoint-policies-overview.md) zugeordnet sind, wird fehlschlagen. Dienstendpunktrichtlinien konnten nicht mit einem Subnetz verknüpft werden, das Managed Instance hostet.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht finden Sie unter  [Was ist Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md).
- Erfahren Sie, wie Sie ein [neues virtuelles Azure-Network](virtual-network-subnet-create-arm-template.md) oder ein [vorhandenes virtuelles Azure-Network](vnet-existing-add-subnet.md) einrichten, in dem Sie SQL Managed Instance bereitstellen können.
- [Berechnen Sie die Größe des Subnetzes](vnet-subnet-determine-size.md), in dem Sie SQL Managed Instance bereitstellen möchten.
- Erfahren Sie, wie Sie eine verwaltete Instanz erstellen:
  - Im [Azure-Portal](instance-create-quickstart.md).
  - Mit [PowerShell](scripts/create-configure-managed-instance-powershell.md).
  - Mit [einer Azure Resource Manager-Vorlage](https://azure.microsoft.com/resources/templates/sqlmi-new-vnet/).
  - Mit [einer Azure Resource Manager-Vorlage (mit JumpBox, in die SSMS integriert ist)](https://azure.microsoft.com/resources/templates/sqlmi-new-vnet-w-jumpbox/).