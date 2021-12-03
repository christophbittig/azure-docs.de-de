---
title: Integration von Bedrohungsdaten in Microsoft Sentinel | Microsoft Dokumentation
description: Informieren Sie sich über die verschiedenen Möglichkeiten, wie Bedrohungsdaten in Microsoft Sentinel integriert und von diesem genutzt werden.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: b24320bf3efcf5b29f1ad8d5847aabac6983b4da
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518487"
---
# <a name="threat-intelligence-integration-in-microsoft-sentinel"></a>Integration von Bedrohungsdaten in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel bietet Ihnen verschiedene Möglichkeiten, [ Bedrohungsdaten-Feeds](work-with-threat-indicators.md) zu nutzen, um die Fähigkeit Ihrer Sicherheitsanalysten zu verbessern, bekannte Bedrohungen zu erkennen und zu priorisieren. 

Sie können eines von vielen verfügbaren Produkten der integrierten [Threat Intelligence Platform (TIP)](connect-threat-intelligence-tip.md) verwenden, eine [Verbindung mit TAXII-Servern herstellen](connect-threat-intelligence-taxii.md), um von STIX-kompatiblen Threat Intelligence-Quellen zu profitieren, sowie benutzerdefinierte Lösungen nutzen, die direkt mit der [Microsoft Graph-Sicherheits-API „tiIndicators“](/graph/api/resources/tiindicator) kommunizieren können. 

Sie können auch über Playbooks eine Verbindung mit Threat Intelligence-Quellen herstellen, um Incidents mit TI-Informationen anzureichern, die direkte Untersuchungs- und Antwortaktionen unterstützen können.

> [!TIP]
> Wenn Sie über mehrere Arbeitsbereiche im selben Mandanten verfügen, z. B. für [Managed Service Providers (MSSPs)](mssp-protect-intellectual-property.md), kann es kostengünstiger sein, Bedrohungsindikatoren nur mit dem zentralisierten Arbeitsbereich zu verbinden.
>
> Wenn Sie in jeden separaten Arbeitsbereich denselben Satz von Bedrohungsindikatoren importiert haben, können Sie arbeitsbereichsübergreifende Abfragen ausführen, um Bedrohungsindikatoren in Ihren Arbeitsbereichen zu aggregieren. Korrelieren Sie sie innerhalb Ihrer MSSP-Incidenterkennung, -untersuchung und -suche.
>

## <a name="taxii-threat-intelligence-feeds"></a>TAXII-Threat Intelligence-Feeds

Um eine Verbindung zu TAXII-Bedrohungsdaten-Feeds herzustellen, befolgen Sie die Anweisungen [ zur Verbindung von Microsoft Sentinel mit STIX/TAXII-Bedrohungsdaten-Feeds](connect-threat-intelligence-taxii.md), zusammen mit den Daten, die von den unten verlinkten Anbietern bereitgestellt werden. Möglicherweise müssen Sie sich direkt an den Anbieter wenden, um die erforderlichen Daten für die Verwendung mit dem Connector zu erhalten.

### <a name="anomali-limo"></a>Anomali Limo

- [Erfahren Sie, was Sie benötigen, um eine Verbindung mit dem Anomali Limo-Feed herzustellen.](https://www.anomali.com/resources/limo)

### <a name="cybersixgill-darkfeed"></a>Cybersixgill Darkfeed

- [Erfahren Sie mehr über die Integration von Cybersixgill mit Microsoft Sentinel @Cybersixgill](https://www.cybersixgill.com/partners/azure-sentinel/)
- Um Microsoft Sentinel mit dem Cybersixgill TAXII Server zu verbinden und Zugang zu Darkfeed zu erhalten, [kontaktieren Sie Cybersixgill](mailto://azuresentinel@cybersixgill.com), um API Root, Collection ID, Benutzername und Passwort zu erhalten.

### <a name="financial-services-information-sharing-and-analysis-center-fs-isac"></a>Financial Services Information Sharing and Analysis Center (FS-ISAC)

- Treten Sie [FS-ISAC](https://www.fsisac.com/membership?utm_campaign=ThirdParty&utm_source=MSFT&utm_medium=ThreatFeed-Join) bei, um die Anmeldeinformationen für den Zugriff auf diesen Feed zu erhalten.

### <a name="health-intelligence-sharing-community-h-isac"></a>Health Intelligence Sharing Community (H-ISAC)

- [Treten Sie H-ISAC bei](https://h-isac.org/soltra/), um die Anmeldeinformationen für den Zugriff auf diesen Feed zu erhalten.

### <a name="ibm-x-force"></a>IBM X-Force

- [Weitere Informationen zur IBM X-Force-Integration](https://www.ibm.com/security/xforce)

### <a name="intsights"></a>IntSights

- [Erfahren Sie mehr über die IntSights-Integration mit Microsoft Sentinel @IntSights](https://intsights.com/resources/intsights-microsoft-azure-sentinel)
- Um Microsoft Sentinel mit dem IntSights TAXII Server zu verbinden, erhalten Sie API Root, Collection ID, Benutzername und Passwort vom IntSights Portal, nachdem Sie eine Richtlinie für die Daten konfiguriert haben, die Sie an Microsoft Sentinel senden möchten.

### <a name="threatconnect"></a>ThreatConnect

- [Weitere Informationen zu STIX und TAXII @ThreatConnect](https://threatconnect.com/stix-taxii/)
- [Dokumentation zu TAXII-Diensten @ThreatConnect](https://docs.threatconnect.com/en/latest/rest_api/v2/taxii/taxii.html)

## <a name="integrated-threat-intelligence-platform-products"></a>Produkte der integrierten Threat Intelligence Platform

Um eine Verbindung zu Threat Intelligence Platform (TIP)-Feeds herzustellen, folgen Sie den Anweisungen zur [Verbindung von Threat Intelligence Platforms mit Microsoft Sentinel](connect-threat-intelligence-tip.md). Im zweiten Teil dieser Anweisungen müssen Sie Informationen in Ihre TIP-Lösung eingeben. Unter den folgenden Links finden Sie weitere Informationen.

### <a name="agari-phishing-defense-and-brand-protection"></a>Agari Phishing Defense und Brand Protection

- Um [Agari Phishing Defense and Brand Protection](https://agari.com/products/phishing-defense/) zu verbinden, verwenden Sie den integrierten [Agari-Datenkonnektor](./data-connectors-reference.md#agari-phishing-defense-and-brand-protection-preview) in Microsoft Sentinel.

### <a name="anomali-threatstream"></a>Anomali ThreatStream

- Auf der Seite für [ThreatStream-Downloads](https://ui.threatstream.com/downloads) können Sie [ThreatStream Integrator und Erweiterungen](https://www.anomali.com/products/threatstream) herunterladen. Außerdem finden Sie dort Anweisungen dazu, wie Sie ThreatStream-Daten mit der Microsoft Graph-Sicherheits-API verbinden.

### <a name="alienvault-open-threat-exchange-otx-from-att-cybersecurity"></a>AlienVault Open Threat Exchange (OTX) von AT&T Cybersecurity

- [AlienVault OTX](https://otx.alienvault.com/) nutzt Azure Logic Apps (Playbooks) für die Verbindung mit Microsoft Sentinel. Sehen Sie sich die [speziellen Anweisungen](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) an, die erforderlich sind, um das gesamte Angebot in vollem Umfang zu nutzen.

### <a name="eclecticiq-platform"></a>EclecticIQ Platform

- Die EclecticIQ-Plattform lässt sich in Microsoft Sentinel integrieren, um die Erkennung, Verfolgung und Reaktion auf Bedrohungen zu verbessern. Erfahren Sie mehr über die [Vorteile und Anwendungsfälle](https://www.eclecticiq.com/resources/azure-sentinel-and-eclecticiq-intelligence-center) dieser bidirektionalen Integration.

### <a name="groupib-threat-intelligence-and-attribution"></a>GroupIB Threat Intelligence and Attribution

- Für die Verbindung von [GroupIB Threat Intelligence und Attribution](https://www.group-ib.com/intelligence-attribution.html) mit Microsoft Sentinel nutzt GroupIB Azure Logic Apps. Sehen Sie sich die [speziellen Anweisungen](https://techcommunity.microsoft.com/t5/azure-sentinel/group-ib-threat-intelligence-and-attribution-connector-azure/ba-p/2252904) an, die erforderlich sind, um das gesamte Angebot in vollem Umfang zu nutzen.

### <a name="misp-open-source-threat-intelligence-platform"></a>MISP-Open-Source-Threat Intelligence Platform

- Ein Beispielskript, das Clients mit MISP-Instanzen zur Migration von Bedrohungsindikatoren in die Microsoft Graph-Sicherheits-API bereitstellt, finden Sie unter [MISP zu Microsoft Graph-Sicherheitsskript](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).
- [Erfahren Sie mehr über das MISP-Projekt](https://www.misp-project.org/).

### <a name="palo-alto-networks-minemeld"></a>Palo Alto Networks MineMeld

- Um [Palo Alto MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld) mit den Verbindungsinformationen zu Microsoft Sentinel zu konfigurieren, siehe [Senden von IOCs an die Microsoft Graph Security API mit MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540) und springen Sie zur Überschrift **MineMeld Konfiguration**.

### <a name="recorded-future-security-intelligence-platform"></a>Recorded Future Security Intelligence Platform

- [Recorded Future](https://www.recordedfuture.com/integrations/microsoft-azure/) nutzt Azure Logic Apps (Playbooks) für die Verbindung mit Microsoft Sentinel. Sehen Sie sich die [speziellen Anweisungen](https://go.recordedfuture.com/hubfs/partners/microsoft-azure-installation-guide.pdf) an, die erforderlich sind, um das gesamte Angebot in vollem Umfang zu nutzen.

### <a name="threatconnect-platform"></a>ThreatConnect-Plattform

- Anweisungen zur Verbindung von [ThreatConnect](https://threatconnect.com/solution/) mit Microsoft Sentinel finden Sie im Konfigurationshandbuch [Microsoft Graph Security Threat Indicators Integration](https://training.threatconnect.com/learn/article/microsoft-graph-security-threat-indicators-integration-configuration-guide-kb-article).

### <a name="threatquotient-threat-intelligence-platform"></a>ThreatQuotient Threat Intelligence Platform

- Unter [Microsoft Sentinel Connector für die ThreatQ-Integration](https://azuremarketplace.microsoft.com/marketplace/apps/threatquotientinc1595345895602.microsoft-sentinel-connector-threatq?tab=overview) finden Sie Support-Informationen und Anweisungen zur Verbindung von [ThreatQuotient TIP](https://www.threatq.com/) mit Microsoft Sentinel.

## <a name="incident-enrichment-sources"></a>Quellen für die Incidentanreicherung

Neben dem Import von Bedrohungsindikatoren können Threat-Intelligence-Feeds auch als Quelle zur Anreicherung der Informationen in Ihren Incidents verwendet werden und Ihren Untersuchungen mehr Kontext verleihen. Die folgenden Feeds dienen diesem Zweck und stellen Azure Logic Apps-Playbooks zur Verwendung in Ihrer [automatisierten Reaktion auf Incidents ](automate-responses-with-playbooks.md) zur Verfügung.

### <a name="hyas-insight"></a>HYAS Insight

- Finden und aktivieren Sie Incident Enrichment Playbooks für [HYAS Insight](https://www.hyas.com/hyas-insight) im [Microsoft Sentinel GitHub Repository](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks). Suchen Sie nach Unterordnern, die mit „Enrich-Sentinel-Incident-HYAS-Insight-“ beginnen.
- Weitere Informationen finden Sie in der [Dokumentation zum HYAS Insight Logic App-Connector](/connectors/hyasinsight/).

### <a name="recorded-future-security-intelligence-platform"></a>Recorded Future Security Intelligence Platform

- Suchen und aktivieren Sie Incident Enrichment Playbooks für [Recorded Future](https://www.recordedfuture.com/integrations/microsoft-azure/) im [Microsoft Sentinel GitHub Repository](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks). Suchen Sie nach Unterordnern, die mit „RecordedFuture_“ beginnen.
- Weitere Informationen finden Sie in der [Dokumentation zum Recorded Future Logic App-Connector](/connectors/recordedfuture/).

### <a name="reversinglabs-titaniumcloud"></a>ReversingLabs TitaniumCloud

- Suchen Sie im [Microsoft Sentinel GitHub-Repository](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/ReversingLabs/Playbooks/Enrich-SentinelIncident-ReversingLabs-File-Information) nach Playbooks zur Anreicherung von Vorfällen für [ReversingLabs](https://www.reversinglabs.com/products/file-reputation-service) und aktivieren Sie sie.
- Weitere Informationen finden Sie in der [Dokumentation zum ReversingLabs Intelligence Logic App-Connector](/connectors/reversinglabsintelligence/).

### <a name="riskiq-passive-total"></a>RiskIQ Passive Total

- Suchen und aktivieren Sie Playbooks zur Anreicherung von Vorfällen für [RiskIQ Passive Total](https://www.riskiq.com/products/passivetotal/) im [Microsoft Sentinel GitHub-Repository](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks). Suchen Sie nach Unterordnern, die mit „Enrich-SentinelIncident-RiskIQ-“ beginnen.
- Weitere Informationen zum Arbeiten mit RiskIQ-Playbooks finden Sie [hier](https://techcommunity.microsoft.com/t5/azure-sentinel/enrich-azure-sentinel-security-incidents-with-the-riskiq/ba-p/1534412).
- Weitere Informationen finden Sie in der [Dokumentation zum RiskIQ PassiveTotal Logic App-Connector](/connectors/riskiqpassivetotal/).

### <a name="virus-total"></a>Virus Total

- Suchen und aktivieren Sie Playbooks zur Anreicherung von Vorfällen für [Virus Total](https://developers.virustotal.com/v3.0/reference) im [Microsoft Sentinel GitHub-Repository](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks). Suchen Sie nach Unterordnern, die mit „Get-VirusTotal“ und „Get-VTURL“ beginnen.
- Weitere Informationen finden Sie in der [Dokumentation zum Virus Total Logic App-Connector](/connectors/virustotal/).

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie gelernt, wie Sie Ihren Threat Intelligence Provider mit Microsoft Sentinel verbinden. Weitere Informationen über Microsoft Sentinel finden Sie in den folgenden Artikeln.

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mit Microsoft Sentinel](./detect-threats-built-in.md).
