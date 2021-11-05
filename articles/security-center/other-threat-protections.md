---
title: Erhöhter Bedrohungsschutz durch Microsoft Defender für Cloud
description: Erfahren Sie mehr über die Schutzmaßnahmen von Microsoft Defender für Cloud gegen Bedrohungen.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 10/12/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2ed570448661ff9c39d963f35d4593d6ac35f10a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131055823"
---
# <a name="additional-threat-protections-in-microsoft-defender-for-cloud"></a>Zusätzlicher Bedrohungsschutz durch Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Neben den integrierten [erweiterten Schutzplänen von Microsoft Defender](defender-for-cloud-introduction.md) bietet Microsoft Defender für Cloud auch die folgenden Möglichkeiten zum Schutz vor Bedrohungen.

> [!TIP]
> Wenn Sie die Defender für Cloud-Features zum Schutz vor Bedrohungen aktivieren möchten, müssen Sie für das Abonnement mit den entsprechenden Workloads erweiterte Sicherheitsfeatures aktivieren.

## <a name="threat-protection-for-azure-network-layer"></a>Bedrohungsschutz für die Azure-Netzwerkebene <a name="network-layer"></a>
Die Analysen auf Netzwerkebene von Defender für Cloud basieren auf exemplarischen [IPFIX-Daten](https://en.wikipedia.org/wiki/IP_Flow_Information_Export) (von Azure-Kernroutern erfasste Paketheader). Auf Grundlage dieses Datenfeeds werden schädliche Aktivitäten im Datenverkehr von Defender für Cloud mittels Machine Learning-Modellen ermittelt und gekennzeichnet. Defender für Cloud nutzt zudem die Threat Intelligence-Datenbank von Microsoft zur Anreicherung von IP-Adressen.

Einige Netzwerkkonfigurationen schränken die Erstellung von Warnungen bei verdächtiger Netzwerkaktivität durch Defender für Cloud ein. Damit Defender für Cloud Netzwerkwarnungen generieren kann, stellen Sie Folgendes sicher:
- Ihr virtueller Computer verfügt über eine öffentliche IP-Adresse (oder befindet sich auf einem Load Balancer mit einer öffentlichen IP-Adresse).
- Der ausgehende Netzwerkdatenverkehr des virtuellen Computers wird nicht durch eine externe IDS-Lösung blockiert.

Eine Liste der Warnungen der Azure-Netzwerkebene finden Sie in der [Referenztabelle der Warnungen](alerts-reference.md#alerts-azurenetlayer).


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Bedrohungsschutz für Azure Cosmos DB (Vorschauversion)<a name="cosmos-db"></a>

Die Azure Cosmos DB-Warnungen werden bei ungewöhnlichen und potenziell schädlichen Zugriffsversuchen oder Exploit-Vorgängen für Azure Cosmos DB-Konten generiert.

Weitere Informationen finden Sie unter

* [Advanced Threat Protection für Azure Cosmos DB (Vorschau)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Liste der Bedrohungsschutzwarnungen für Azure Cosmos DB (Vorschau)](alerts-reference.md#alerts-azurecosmos)

## <a name="display-defender-for-cloud-recommendations-in-microsoft-defender-for-cloud-apps"></a>Anzeigen von Defender für Cloud-Empfehlungen in Microsoft Defender für Cloud-Apps <a name="azure-mcas"></a>

Microsoft Defender für Cloud-Apps (zuvor Microsoft Cloud App Security genannt) ist ein Cloud Access Security Broker (CASB), der verschiedene Bereitstellungsmodi unterstützt, darunter Protokollsammlung, API-Connectors und Reverseproxy. Er bietet umfassende Transparenz, Kontrolle über den Datenverkehr sowie anspruchsvolle Analysefunktionen zum Erkennen und Bekämpfen von Cyberbedrohungen für sämtliche Clouddienste von Microsoft und Drittanbietern.

Wenn Sie Microsoft Defender für Cloud-Apps aktiviert und die Integration in den Einstellungen von Defender für Cloud ausgewählt haben, werden Ihre Härtungsempfehlungen von Defender für Cloud in Microsoft Defender für Cloud-Apps angezeigt, ohne dass eine zusätzliche Konfiguration erforderlich ist.

> [!NOTE]
> Defender für Cloud speichert sicherheitsbezogene Kundendaten im gleichen geografischen Raum, in dem sich auch die Ressource befindet. Falls Defender für Cloud von Microsoft noch nicht im geografischen Raum der Ressource bereitgestellt wurde, werden die Daten in den USA gespeichert. Wenn Microsoft Defender für Cloud aktiviert ist, werden diese Informationen gemäß den Regeln für geografische Räume von Microsoft Defender für Cloud-Apps gespeichert. Weitere Informationen finden Sie unter [Data storage for non-regional services](https://azuredatacentermap.azurewebsites.net/) (Datenspeicherung für nicht regionale Dienste).


## <a name="stream-security-alerts-from-other-microsoft-services"></a>Streamen von Sicherheitswarnungen von anderen Microsoft-Diensten <a name="alerts-other"></a>

### <a name="display-azure-waf-alerts-in-defender-for-cloud"></a>Anzeigen von Azure Web Application Firewall-Warnungen in Defender für Cloud <a name="azure-waf"></a>

Azure Application Gateway verfügt über eine Web Application Firewall (WAF), die den zentralisierten Schutz Ihrer Webanwendungen vor allgemeinen Exploits und Sicherheitsrisiken ermöglicht.

Webanwendungen sind zunehmend Ziele böswilliger Angriffe, die allgemein bekannte Sicherheitslücken ausnutzen. Die Application Gateway-WAF basiert auf Version 3.0 oder 2.2.9 des Kernregelsatzes aus dem Open Web Application Security Project. Die WAF wird automatisch aktualisiert und bietet ganz Schutz vor neuen Sicherheitsrisiken. 

Wenn Sie über eine Lizenz für Azure Web Application Firewall (WAF) verfügen, werden Ihre WAF-Warnungen ganz ohne zusätzliche Konfiguration an Defender für Cloud gestreamt. Weitere Informationen zu den von WAF generierten Warnungen finden Sie unter [CRS-Regelgruppen und -Regeln der Web Application Firewall](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="display-azure-ddos-protection-alerts-in-defender-for-cloud"></a>Anzeigen von Azure DDoS Protection-Warnungen in Defender für Cloud <a name="azure-ddos"></a>

Verteilte Denial-of-Service-Angriffe (Distributed Denial of Service, DDoS) sind bekanntlich einfach durchführbar. Sie haben sich zu einem wichtigen Sicherheitsthema entwickelt, insbesondere wenn Sie Ihre Anwendungen zur Cloud migrieren. Ein DDoS-Angriff hat das Ziel, die Ressourcen einer Anwendung zu verbrauchen, damit sie für berechtigte Benutzer nicht mehr verfügbar ist. DDoS-Angriffe können jeden beliebigen Endpunkt ins Visier nehmen, der über das Internet erreichbar sind.

Um sich vor DDoS-Angriffe zu schützen, erwerben Sie eine Lizenz für Azure DDoS Protection, und befolgen Sie unbedingt die bewährten Methoden für den Anwendungsentwurf. Azure DDoS Protection bietet verschiedene Dienstebenen. Weitere Informationen finden Sie in der [Übersicht über Azure DDoS Protection Standard](../ddos-protection/ddos-protection-overview.md).

Wenn Sie Azure DDoS Protection aktiviert haben, werden Ihre DDoS-Warnungen ohne zusätzliche Konfiguration an Defender für Cloud gestreamt. Weitere Informationen zu den von DDoS Protection generierten Warnungen finden Sie in der [Referenztabelle der Warnungen](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu den Sicherheitswarnungen dieser Bedrohungsschutzfeatures finden Sie in den folgenden Artikeln:

* [Referenztabelle für alle Defender für Cloud-Warnungen](alerts-reference.md)
* [Sicherheitsbenachrichtigungen in Defender für die Cloud](alerts-overview.md)
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Defender für Cloud](managing-and-responding-alerts.md)
* [Fortlaufender Export von Defender für Cloud-Daten](continuous-export.md)
