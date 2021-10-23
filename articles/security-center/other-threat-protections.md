---
title: Zusätzliche Bedrohungsschutzmaßnahmen von Azure Security Center
description: Erfahren Sie etwas über den in Azure Security Center verfügbaren Bedrohungsschutz neben Azure Defender.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 10/12/2021
ms.author: memildin
ms.openlocfilehash: c2bed032510e278663a1d1d9f10043eaa6e9b0db
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858194"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Zusätzliche Bedrohungsschutzmaßnahmen in Azure Security Center
Ebenso wie die integrierten [Azure Defender-Schutzmaßnahmen](azure-defender.md) bietet Azure Security Center auch die folgenden Funktionen zum Schutz vor Bedrohungen.

> [!TIP]
> Wenn Sie die Security Center-Features zum Schutz vor Bedrohungen aktivieren möchten, müssen Sie Azure Defender für das Abonnement mit den entsprechenden Workloads aktivieren.

## <a name="threat-protection-for-azure-network-layer"></a>Bedrohungsschutz für die Azure-Netzwerkebene <a name="network-layer"></a>
Die Netzwerkebenenanalysen von Security Center basieren auf exemplarischen [IPFIX-Daten](https://en.wikipedia.org/wiki/IP_Flow_Information_Export) (durch Azure-Kernrouter erfasste Paketheader). Auf der Grundlage dieses Datenfeeds werden schädliche Datenverkehrsaktivitäten von Security Center durch Machine Learning-Modelle identifiziert und gekennzeichnet. Security Center nutzt zudem die Threat Intelligence-Datenbank von Microsoft zur Anreicherung von IP-Adressen.

Einige Netzwerkkonfigurationen schränken Security Center in der Erstellung von Warnungen bei verdächtiger Netzwerkaktivität ein. Damit Security Center Netzwerkwarnungen generiert, stellen Sie Folgendes sicher:
- Ihr virtueller Computer verfügt über eine öffentliche IP-Adresse (oder befindet sich auf einem Load Balancer mit einer öffentlichen IP-Adresse).
- Der ausgehende Netzwerkdatenverkehr des virtuellen Computers wird nicht durch eine externe IDS-Lösung blockiert.

Eine Liste der Warnungen der Azure-Netzwerkebene finden Sie in der [Referenztabelle der Warnungen](alerts-reference.md#alerts-azurenetlayer).


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Bedrohungsschutz für Azure Cosmos DB (Vorschauversion)<a name="cosmos-db"></a>

Die Azure Cosmos DB-Warnungen werden bei ungewöhnlichen und potenziell schädlichen Zugriffsversuchen oder Exploit-Vorgängen für Azure Cosmos DB-Konten generiert.

Weitere Informationen finden Sie unter

* [Advanced Threat Protection für Azure Cosmos DB (Vorschau)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Liste der Bedrohungsschutzwarnungen für Azure Cosmos DB (Vorschau)](alerts-reference.md#alerts-azurecosmos)

## <a name="display-azure-security-center-recommendations-in-microsoft-cloud-app-security"></a>Anzeigen von Azure Security Center-Empfehlungen in Microsoft Cloud App Security <a name="azure-mcas"></a>

Microsoft Cloud App Security ist ein Broker für die Sicherheit von Cloudzugriffen (Cloud Access Security Broker, CASB), der verschiedene Bereitstellungsmodi unterstützt, darunter Protokollsammlung, API-Connectors und Reverseproxy. Er bietet umfassende Transparenz, Kontrolle über den Datenverkehr sowie anspruchsvolle Analysefunktionen zum Erkennen und Bekämpfen von Cyberbedrohungen für sämtliche Clouddienste von Microsoft und Drittanbietern.

Wenn Sie Microsoft Cloud App Security aktiviert und in den Einstellungen von Security Center die Integration ausgewählt haben, werden Ihre Security Center-Empfehlungen zur Härtung in Cloud App Security ohne zusätzliche Konfiguration angezeigt.

> [!NOTE]
> Security Center speichert sicherheitsbezogene Kundendaten im gleichen geografischen Raum, in dem sich auch die Ressource befindet. Falls Security Center von Microsoft noch nicht im geografischen Raum der Ressource bereitgestellt wurde, werden die Daten in den USA gespeichert. Wenn Microsoft Cloud App Security aktiviert ist, werden diese Informationen gemäß den Regeln für geografische Standorte von Microsoft Cloud App Security gespeichert. Weitere Informationen finden Sie unter [Data storage for non-regional services](https://azuredatacentermap.azurewebsites.net/) (Datenspeicherung für nicht regionale Dienste).


## <a name="stream-security-alerts-from-other-microsoft-services"></a>Streamen von Sicherheitswarnungen von anderen Microsoft-Diensten <a name="alerts-other"></a>

### <a name="display-azure-waf-alerts-in-security-center"></a>Anzeigen von Azure WAF-Warnungen in Security Center <a name="azure-waf"></a>

Azure Application Gateway verfügt über eine Web Application Firewall (WAF), die den zentralisierten Schutz Ihrer Webanwendungen vor allgemeinen Exploits und Sicherheitsrisiken ermöglicht.

Webanwendungen sind zunehmend Ziele böswilliger Angriffe, die allgemein bekannte Sicherheitslücken ausnutzen. Die Application Gateway-WAF basiert auf Version 3.0 oder 2.2.9 des Kernregelsatzes aus dem Open Web Application Security Project. Die WAF wird automatisch aktualisiert und bietet ganz Schutz vor neuen Sicherheitsrisiken. 

Wenn Sie über eine Lizenz für Azure WAF verfügen, werden Ihre WAF-Warnungen ganz ohne zusätzliche Konfiguration an Security Center gestreamt. Weitere Informationen zu den von WAF generierten Warnungen finden Sie unter [CRS-Regelgruppen und -Regeln der Web Application Firewall](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="display-azure-ddos-protection-alerts-in-security-center"></a>Anzeigen der Azure DDoS Protection-Schutzwarnungen in Security Center <a name="azure-ddos"></a>

Verteilte Denial-of-Service-Angriffe (Distributed Denial of Service, DDoS) sind bekanntlich einfach durchführbar. Sie haben sich zu einem wichtigen Sicherheitsthema entwickelt, insbesondere wenn Sie Ihre Anwendungen zur Cloud migrieren. Ein DDoS-Angriff hat das Ziel, die Ressourcen einer Anwendung zu verbrauchen, damit sie für berechtigte Benutzer nicht mehr verfügbar ist. DDoS-Angriffe können jeden beliebigen Endpunkt ins Visier nehmen, der über das Internet erreichbar sind.

Um sich vor DDoS-Angriffe zu schützen, erwerben Sie eine Lizenz für Azure DDoS Protection, und befolgen Sie unbedingt die bewährten Methoden für den Anwendungsentwurf. Azure DDoS Protection bietet verschiedene Dienstebenen. Weitere Informationen finden Sie in der [Übersicht über Azure DDoS Protection Standard](../ddos-protection/ddos-protection-overview.md).

Wenn Sie Azure DDoS Protection aktiviert haben, werden Ihre DDoS-Warnungen ohne zusätzliche Konfiguration an Security Center gestreamt. Weitere Informationen zu den von DDoS Protection generierten Warnungen finden Sie in der [Referenztabelle der Warnungen](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu den Sicherheitswarnungen dieser Bedrohungsschutzfeatures finden Sie in den folgenden Artikeln:

* [Referenztabelle für alle Azure Security Center-Warnungen](alerts-reference.md)
* [Sicherheitswarnungen in Azure Security Center](security-center-alerts-overview.md)
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md)
