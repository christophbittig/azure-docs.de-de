---
title: End-to-End-Sicherheit in Azure | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Übersicht über Azure-Dienste, die Ihnen helfen, Ihre Cloudressourcen zu sichern und zu schützen sowie Bedrohungen zu erkennen und zu untersuchen.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/07/2021
ms.author: terrylan
ms.openlocfilehash: eb96e21644f6318320ee8a5c7c33b83a73a59c94
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335497"
---
# <a name="end-to-end-security-in-azure"></a>End-to-End-Sicherheit in Azure
Eines der schlagkräftigsten Argumente dafür, Azure für Anwendungen und Dienste zu verwenden, ist die Vielzahl an Sicherheitstools und -funktionen. Diese Tools und Funktionen ermöglichen die Erstellung sicherer Lösungen auf der Grundlage der sicheren Azure-Plattform. Microsoft Azure bietet sowohl Vertraulichkeit, Integrität und Verfügbarkeit von Kundendaten als auch eine transparente Verantwortlichkeit.

Im folgenden Diagramm und der Dokumentation werden Ihnen die Sicherheitsdienste in Azure vorgestellt. Diese Sicherheitsdienste helfen Ihnen, die Sicherheitsanforderungen Ihres Unternehmens zu erfüllen sowie Ihre Benutzer, Geräte, Ressourcen, Daten und Anwendungen in der Cloud zu schützen.

## <a name="microsoft-security-services-map"></a>Übersicht über Microsoft-Sicherheitsdienste

In der Übersicht über Sicherheitsdienste werden Dienste nach den Ressourcen, die sie schützen, gruppiert (Spalten). Im Diagramm werden Dienste auch in die folgenden Kategorien gruppiert (Zeilen):

- Sichern und Schützen: Dienste, mit denen Sie eine schichtweise, tiefgehende Verteidigungsstrategie (Defense-in-Depth) für Identität, Hosts, Netzwerke und Daten implementieren können. Diese Sammlung von Sicherheitsdiensten und -funktionen bietet eine Möglichkeit, Ihren Sicherheitsstatus in Ihrer gesamten Azure-Umgebung zu verstehen und zu verbessern.
- Erkennen von Bedrohungen – Dienste, die verdächtige Aktivitäten identifizieren und die Entschärfung der Bedrohung erleichtern.
- Untersuchen und Reagieren – Dienste, die Protokollierungsdaten abrufen, damit Sie eine verdächtige Aktivität bewerten und darauf reagieren können.

Das Diagramm enthält das Programm „Azure-Sicherheitsvergleichstest“ – eine Sammlung von wirkungsvollen Sicherheitsempfehlungen, mit denen Sie die von Ihnen in Azure genutzten Dienste schützen können.

:::image type="content" source="media/end-to-end/security-diagram.svg" alt-text="Diagramm der End-to-End-Sicherheitsdienste in Azure" border="false":::

## <a name="security-controls-and-baselines"></a>Sicherheitskontrollelemente und -baselines
Das Programm [Azure-Sicherheitsvergleichstest](../benchmarks/introduction.md) enthält eine Sammlung von wirkungsvollen Sicherheitsempfehlungen, mit denen Sie die von Ihnen in Azure genutzten Dienste schützen können:

- Sicherheitskontrollelemente – Diese Empfehlungen gelten allgemein für Ihren Azure-Mandanten und die Azure-Dienste. Jede Empfehlung enthält eine Liste von Projektbeteiligter, die in der Regel an der Planung, Genehmigung oder Implementierung des Vergleichstests beteiligt sind.
- Dienstbaselines – Diese wenden die Kontrollelemente auf einzelne Azure-Dienste an, um Empfehlungen für die Sicherheitskonfiguration des jeweiligen Diensts zu geben.

## <a name="secure-and-protect"></a>Sichern und Schützen

:::image type="content" source="media/end-to-end/secure-and-protect.svg" alt-text="Diagramm der Azure-Dienste, die Ihnen beim Sichern und Schützen Ihrer Cloudressourcen helfen" border="false":::

| Dienst | BESCHREIBUNG |
|------|--------|
| [Microsoft Defender für Cloud](../../security-center/security-center-introduction.md)| Ein vereinheitlichtes Sicherheitsverwaltungssystem für Infrastrukturen, mit dem der Sicherheitsstatus Ihrer Rechenzentren gestärkt sowie ein erweiterter Schutz vor Bedrohungen für Ihre Hybridworkloads in der Cloud (in Azure oder anderswo) und der lokalen Umgebung bereitgestellt wird. |
| **Identity&nbsp;&&nbsp;Access&nbsp;Management** (Identitätszugriffsverwaltung) | |
| [Azure Active Directory (AD)](../../active-directory/fundamentals/active-directory-whatis.md)| Cloudbasierter Identitäts- und Zugriffsverwaltungsdienst von Microsoft.  |
|  | [Bedingter Zugriff](../../active-directory/conditional-access/overview.md) ist das von Azure AD verwendete Tool, mit dem Identitätssignale zusammengeführt, Entscheidungen getroffen und Organisationsrichtlinien erzwungen werden. |
|  | [Domain Services](../../active-directory-domain-services/overview.md) ist das von Azure AD verwendete Tool zur Bereitstellung von verwalteten Domänendiensten wie Domänenbeitritt, Gruppenrichtlinie, Lightweight Directory Access Protocol (LDAP) und Kerberos-/NTLM-Authentifizierung. |
|  | [Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) ist ein Dienst in Azure AD, mit dem Sie den Zugriff auf wichtige Ressourcen innerhalb Ihrer Organisation verwalten, steuern und überwachen können. |
|  | [Mehrstufige Authentifizierung](../../active-directory/authentication/concept-mfa-howitworks.md) ist das von Azure AD verwendete Tool zum Schutz des Zugriffs auf Daten und Anwendungen, indem eine zweite Form der Authentifizierung angefordert wird. |
| [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) | Ein Tool, mit dem Organisationen die Erkennung und Behebung identitätsbasierter Risiken automatisieren, Risiken anhand von Daten im Portal untersuchen und Risikoerkennungsdaten zur weiteren Analyse in Hilfsprogramme von Drittanbietern exportieren können. |
| **Infrastruktur&nbsp;&&nbsp;Netzwerk** |  |
| [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) | Ein Gateway für virtuelle Netzwerke, das zum Senden von verschlüsseltem Datenverkehr zwischen einem virtuellen Azure-Netzwerk und einem lokalen Standort über das öffentliche Internet sowie zum Senden von verschlüsseltem Datenverkehr zwischen virtuellen Azure-Netzwerken über das Microsoft-Netzwerk dient. |
| [Azure DDoS Protection: Standard](../../ddos-protection/ddos-protection-overview.md) | Bietet erweiterte DDoS-Risikominderungsfeatures zum Schutz vor DDoS-Angriffen. Er wird automatisch optimiert, um Ihre spezifischen Azure-Ressourcen in einem virtuellen Netzwerk zu schützen. |
| [Azure Front Door](../../frontdoor/front-door-overview.md) | Ein globaler und skalierbarer Einstiegspunkt, der es ermöglicht, über das globale Microsoft Edge-Netzwerk schnelle, sichere und umfassend skalierbare Webanwendungen zu erstellen. |
| [Azure Firewall](../../firewall/overview.md) | Ein verwalteter, cloudbasierter Netzwerksicherheitsdienst, der Ihre Azure Virtual Network-Ressourcen schützt. Es ist eine vollständig zustandsbehaftete Firewall-as-a-Service mit integrierter Hochverfügbarkeit und uneingeschränkter Cloudskalierbarkeit. |
| [Azure Key Vault](../../key-vault/general/overview.md) | Ein sicherer Geheimnisspeicher für Token, Kennwörter, Zertifikate, API-Schlüssel und andere Geheimnisse. Key Vault kann auch zum Erstellen und Kontrollieren der Verschlüsselungsschlüssel verwendet werden, mit denen Ihre Daten verschlüsselt werden. |
| [Verwaltetes HSM von Key Vault](../../key-vault/managed-hsm/overview.md) | Ein vollständig verwalteter, hochverfügbarer und standardkonformer Einzelmandanten-Clouddienst, der es Ihnen ermöglicht, kryptografische Schlüssel für Ihre Cloudanwendungen über HSMs zu schützen, die mit FIPS 140-2 Level 3 validiert wurden. |
| [Azure Private Link](../../private-link/private-link-overview.md) | Ermöglicht Ihnen den Zugriff auf Azure-PaaS-Dienste (z. B. Azure Storage und SQL Database) sowie auf in Azure gehostete kundeneigene Dienste/Partnerdienste über einen privaten Endpunkt in Ihrem virtuellen Netzwerk. |
| [Azure Application Gateway](../../application-gateway/overview.md) | Ein erweiterter Lastenausgleich für Webdatenverkehr, mit dem Sie Datenverkehr für Ihre Webanwendungen verwalten können. Application Gateway kann Routingentscheidungen auf der Grundlage zusätzlicher Attribute einer HTTP-Anforderung treffen. Beispiele für solche Attribute wären etwa der URI-Pfad oder Hostheader. |
| [Azure Service Bus](../../service-bus-messaging/service-bus-messaging-overview.md) | Ein vollständig verwalteter Nachrichtenbroker für Unternehmen mit Nachrichtenwarteschlangen und Veröffentlichen/Abonnieren-Themen. Mithilfe von Service Bus werden Anwendungen und Dienste voneinander entkoppelt. |
| [Web Application Firewall](../../web-application-firewall/overview.md) | Bietet zentralisierten Schutz Ihrer Webanwendungen vor allgemeinen Exploits und Sicherheitsrisiken. WAF kann mit Azure Application Gateway und Azure Front Door bereitgestellt werden. |
| **Daten und Anwendung** |  |
| [Azure Backup](../../backup/backup-overview.md) | Bietet einfache, sichere und kostengünstige Lösungen, um Ihre Daten zu sichern und aus der Microsoft Azure-Cloud wiederherzustellen. |
| [Azure Storage Service Encryption](../../storage/common/storage-service-encryption.md) | Verschlüsselt Daten automatisch, bevor sie gespeichert werden, und entschlüsselt die Daten automatisch, wenn Sie sie abrufen. |
| [Azure Information Protection](/azure/information-protection/what-is-information-protection) | Eine cloudbasierte Lösung, mit der Organisationen Dokumente und E-Mails durch das Anwenden von Bezeichnungen auf den Inhalt ermitteln, klassifizieren und schützen können. |
| [API Management](../../api-management/api-management-key-concepts.md) | Eine Möglichkeit zum Erstellen von konsistenten und modernen API-Gateways für vorhandene Back-End-Dienste. |
| [Confidential Computing in Azure](../../confidential-computing/overview.md) | Ermöglicht es Ihnen, Ihre vertraulichen Daten zu isolieren, während sie in der Cloud verarbeitet werden. |
| [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops) | Ihre Entwicklungsprojekte profitieren von mehreren Sicherheitsstufen und Governancetechnologien, betrieblichen Verfahren und Konformitätsrichtlinien, wenn sie in Azure DevOps gespeichert werden. |
| **Kundenzugriff** |  |
| [Azure Active Directory External Identities](../../active-directory/external-identities/compare-with-b2c.md) | Mit „Externe Identitäten“ in Azure AD können Sie Personen außerhalb Ihrer Organisation den Zugriff auf Ihre Apps und Ressourcen erlauben. Gleichzeitig können sie sich mit der von ihnen bevorzugten Identität anmelden. |
|  | Sie können Ihre Apps und Ressourcen für externe Benutzer über [Azure AD B2B](../../active-directory/external-identities/what-is-b2b.md)-Zusammenarbeit freigeben. |
|  | Mithilfe von [Azure AD B2C](../../active-directory-b2c/overview.md) können Sie Millionen von Benutzern und Milliarden von Authentifizierungen pro Tag unterstützen, indem Bedrohungen wie Denial-of-Service-, Kennwortspray- oder Brute-Force-Angriffe überwacht und automatisch behandelt werden. |

## <a name="detect-threats"></a>Erkennen von Bedrohungen

:::image type="content" source="media/end-to-end/detect-threats.svg" alt-text="Diagramm von Azure-Diensten, die Bedrohungen erkennen" border="false":::

| Dienst | BESCHREIBUNG |
|------|--------|
| [Microsoft Defender für Cloud](../../security-center/azure-defender.md) | Bietet erweiterten, intelligenten Schutz für Ihre Azure-Ressourcen und -Workloads sowie die Hybridressourcen und -workloads. Das Dashboard für den Workloadschutz in Defender für Cloud bietet Transparenz und Kontrolle der Cloudworkloadschutz-Funktionen für Ihre Umgebung. |
| [Microsoft Sentinel](../../sentinel/overview.md) | Eine skalierbare, cloudnative Lösung für die Verwaltung von Sicherheitsinformationen und -ereignissen (Security Information & Event Management, SIEM) sowie die Sicherheitsorchestrierung mit automatisierter Reaktion (Security Orchestration Automated Response, SOAR). Sentinel bietet intelligente Sicherheitsanalysen und Bedrohungsinformationen für das gesamte Unternehmen und stellt eine zentrale Lösung für die Warnungs- und Bedrohungserkennung, die proaktive Suche sowie die Reaktion auf Bedrohungen bereit. |
| **Identity&nbsp;&&nbsp;Access&nbsp;Management** (Identitätszugriffsverwaltung) |  |
| [Microsoft 365 Defender](/microsoft-365/security/defender/microsoft-365-defender) | Eine einheitliche Unternehmenssuite zur Verteidigung vor und nach Sicherheitsverletzungen, die nativ die Erkennung, Verhinderung, Untersuchung und Reaktion über Endpunkte, Identitäten, E-Mails und Anwendungen hinweg koordiniert, um einen integrierten Schutz vor komplexen Angriffen zu bieten. |
|  | [Microsoft Defender für Endpunkt](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint) (Microsoft Defender Advanced Threat Protection, MDATP) ist eine Endpunktsicherheitsplattform für Unternehmen, die helfen soll, komplexe Bedrohungen zu vermeiden, zu erkennen, zu untersuchen und darauf zu reagieren. |
|  | [Microsoft Defender for Identity](/defender-for-identity/what-is) ist eine cloudbasierte Sicherheitslösung, die Signale Ihrer lokalen Active Directory-Instanz nutzt, um komplexe Bedrohungen, gefährdete Identitäten sowie schädliche Insideraktionen gegen Ihre Organisation zu identifizieren, zu erkennen und zu untersuchen. |
| [Azure AD Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-notifications.md) | Sendet zwei Arten von automatisierten Benachrichtigungs-E-Mails, um Sie bei der Verwaltung von Benutzerrisiken und Risikoerkennungen zu unterstützen: „E-Mail für erkannte gefährdete Benutzer“ und „Wöchentliche E-Mail mit Übersicht“. |
| **Infrastruktur und Netzwerk** |  |
| [Microsoft Defender für IoT](../../defender-for-iot/overview.md) | Eine einheitliche Sicherheitslösung zum Identifizieren von IoT/OT-Geräten, Sicherheitsrisiken und Bedrohungen. Diese Lösung bietet Schutz für Ihre gesamte IoT/OT-Umgebung. Dabei spielt es keine Rolle, ob Sie vorhandene IoT/OT-Geräte schützen oder Sicherheit in IoT-Innovationen integrieren müssen. |
| [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) | Bietet Tools für die Überwachung, Diagnose, Metrikanzeige und Aktivierung oder Deaktivierung von Protokollen für Ressourcen in einem virtuellen Azure-Netzwerk. Network Watcher ist für die Überwachung und Wiederherstellung der Netzwerkintegrität von IaaS-Produkten konzipiert, darunter z. B. Virtual Machines, virtuelle Netzwerke, Anwendungsgateways und Lastenausgleichsmodule. |
| [Azure Policy-Überwachungsprotokollierung](../../governance/policy/overview.md) | Hilft bei der Durchsetzung von Organisationsstandards und der Bewertung der Compliance im großen Stil. Azure Policy verwendet automatisch aktivierte Aktivitätsprotokolle, um Elemente wie Ereignisquelle, Datum, Benutzer, Zeitstempel, Quelladressen, Zieladressen und andere nützliche Elemente einzubeziehen. |
| **Daten und Anwendung** |  |
| [Microsoft Defender für Containerregistrierungen](../../security-center/defender-for-container-registries-introduction.md) | Enthält eine Option für die Überprüfung auf Sicherheitsrisiken, mit der die Images in Ihren Azure Resource Manager-basierten Azure Container Registry-Registrierungen überprüft und die Sicherheitsrisiken Ihrer Images eingehender untersucht werden können. |
| [Microsoft Defender für Kubernetes](../../security-center/defender-for-kubernetes-introduction.md) | Bietet Bedrohungsschutz auf Clusterebene, indem Ihre von AKS verwalteten Dienste über die von Azure Kubernetes Service (AKS) abgerufenen Protokolle überwacht werden. |
| [Microsoft Defender für Cloud-Apps](/cloud-app-security/what-is-cloud-app-security) | Ein Cloud Access Security Broker (CASB), der in mehreren Clouds betrieben wird. Er bietet umfassende Transparenz, Kontrolle über den Datenverkehr sowie anspruchsvolle Analysefunktionen zum Erkennen und Bekämpfen von Cyberbedrohungen für sämtliche Clouddienste. |

## <a name="investigate-and-respond"></a>Untersuchen und Reagieren

:::image type="content" source="media/end-to-end/investigate-and-respond.svg" alt-text="Diagramm von Azure-Diensten, mit denen Sie Bedrohungen untersuchen und darauf reagieren können" border="false":::

| Dienst | Beschreibung |
|------|--------|
| [Microsoft Sentinel](../../sentinel/hunting.md) | Leistungsstarke Such- und Abfragetools zum Aufspüren von Sicherheitsbedrohungen in den Datenquellen Ihrer Organisation. |
| [Azure&nbsp;Monitor&nbsp;-Protokolle&nbsp; und &nbsp;-Metriken](../../azure-monitor/overview.md) | Bietet eine umfassende Lösung für das Sammeln, Analysieren und Behandeln von Telemetriedaten aus Ihren Cloud- und lokalen Umgebungen. Azure Monitor [sammelt und aggregiert Daten](../../azure-monitor/data-platform.md#observability-data-in-azure-monitor) aus einer Vielzahl von Quellen auf einer gemeinsamen Datenplattform, wo diese Daten zur Analyse, Visualisierung und Ausgabe von Warnungen verwendet werden können. |
| **Identity&nbsp;&&nbsp;Access&nbsp;Management** (Identitätszugriffsverwaltung) |  |
| [Azure&nbsp; &nbsp;AD-Berichte&nbsp; und &nbsp;-Überwachung](../../active-directory/reports-monitoring/index.yml) | [Azure AD-Berichte](../../active-directory/reports-monitoring/overview-reports.md) bieten einen umfassenden Überblick über Aktivitäten in Ihrer Umgebung. |
|  | Mithilfe von [Azure AD-Überwachung](../../active-directory/reports-monitoring/overview-monitoring.md) können Sie Ihre Azure AD-Aktivitätsprotokolle an verschiedene Endpunkte weiterleiten.|
| [Azure AD PIM-Überwachungsverlauf](../../active-directory/privileged-identity-management/pim-how-to-use-audit-log.md) (Privileged Identity Management) | Zeigt alle Rollenzuweisungen und -aktivierungen innerhalb der letzten 30 Tage für alle privilegierten Rollen. |
| **Daten und Anwendung** |  |
| [Microsoft Defender für Cloud-Apps](/cloud-app-security/investigate) | Stellt Tools bereit, damit Sie besser verstehen, was gerade in Ihrer Cloudumgebung geschieht. |

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [gemeinsame Verantwortung in der Cloud](shared-responsibility.md).

- Informieren Sie sich über die [Isolationsoptionen in der Azure-Cloud](isolation-choices.md) gegen böswillige und nicht böswillige Benutzer.
