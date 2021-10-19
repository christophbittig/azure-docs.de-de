---
title: Verfügbarkeit des Azure-Dienstcloudfeatures für US-Regierungskunden
description: Listet die Featureverfügbarkeit für Azure-Sicherheitsdienste auf, z. B. Azure Sentinel für US-Regierungskunden.
author: TerryLanfear
ms.author: terrylan
ms.service: security
ms.topic: reference
ms.date: 09/13/2021
ms.openlocfilehash: 918743544aeaf60932f14d1d1eed609775b29efd
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710562"
---
# <a name="cloud-feature-availability-for-us-government-customers"></a>Verfügbarkeit von Cloudfeatures für US-Regierungskunden

In diesem Artikel wird die Featureverfügbarkeit in den Microsoft Azure und Azure Government Clouds für die folgenden Sicherheitsdienste beschrieben:

- [Azure Security Center](#azure-security-center)
- [Azure Sentinel](#azure-sentinel)
- [Azure Defender für IoT](#azure-defender-for-iot)

> [!NOTE]
> Diesem Artikel werden in Kürze zusätzliche Sicherheitsdienste hinzugefügt.
> 

## <a name="azure-government"></a>Azure Government

Azure Government verwendet die gleichen zugrunde liegenden Technologien wie Azure (manchmal auch als Azure Commercial oder Azure Public bezeichnet), das die Kernkomponenten von Infrastructure-as-a-Service (IaaS), Platform-as-a-Service (PaaS) und Software-as-a-Service (SaaS) umfasst. Sowohl Azure als auch Azure Government verfügen über umfassende Sicherheitskontrollen und die Microsoft-Verpflichtung zum Schutz von Kundendaten.

Azure Government ist eine physisch isolierte Cloudumgebung, die dediziert für US-Behörden auf Bundes-, Staats-, lokaler und Stammesebene sowie für deren Partner bestimmt ist. Zwar sind beide Cloudumgebungen auf der FedRAMP High-Auswirkungsstufe bewertet und autorisiert, doch Azure Government bietet eine zusätzliche Schutzebene für Kunden durch vertragliche Verpflichtungen hinsichtlich der Speicherung von Kundendaten in den USA und der Beschränkung des potenziellen Zugriffs auf Systeme, die Kundendaten verarbeiten, auf sicherheitsüberprüfte US-Bürger. Diese Verpflichtungen können für Kunden von Interesse sein, die die Cloud verwenden, um Daten zu speichern oder zu verarbeiten, die den US-Exportkontrollbestimmungen wie EAR, ITAR und DoE 10 CFR Part 810 unterliegen.

Weitere Informationen zu Azure Government finden Sie unter [Was ist Azure Government?](../../azure-government/documentation-government-welcome.md).

## <a name="microsoft-365-integration"></a>Microsoft 365-Integration

Integrationen zwischen Produkten basieren auf der Interoperabilität zwischen Azure- und Office-Plattformen. Auf Angebote, die in der Azure-Umgebung gehostet werden, kann über die Plattformen Microsoft 365 Enterprise und Microsoft 365 Government zugegriffen werden. Office 365 und Office 365 GCC sind mit Azure Active Directory (Azure AD) in Azure gekoppelt. Office 365 GCC High und Office 365 DoD sind mit Azure AD in Azure Government gekoppelt.

Das folgende Diagramm zeigt die Hierarchie von Microsoft Clouds und deren Beziehungen zueinander.

:::image type="content" source="media/microsoft-365-cloud-integration.png" alt-text="Microsoft 365-Cloudintegration.":::

Die Office 365 GCC-Umgebung hilft Kunden, die Anforderungen der US-Regierung zu erfüllen, einschließlich FedRAMP High, CJIS und IRS 1075. Die Office 365 GCC High- und DoD-Umgebungen unterstützen Kunden, die Compliance mit DoD IL4/5, DFARS 7012, NIST 800-171 und ITAR benötigen.

Weitere Informationen zu Office 365 US Government-Umgebungen finden Sie unter:

- [Office 365 GCC](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc)
- [Office 365 GCC High und DoD](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)


In den folgenden Abschnitten wird beschrieben, wann ein Dienst über eine Integration in Microsoft 365 und die Featureverfügbarkeit für Office 365 GCC, Office 365 High und Office 365 DoD verfügt.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center ist ein vereinheitlichtes Sicherheitsverwaltungssystem für Infrastrukturen, mit dem der Sicherheitsstatus Ihrer Rechenzentren gestärkt wird und ein erweiterter Schutz vor Bedrohungen für Ihre Hybridworkloads in der Cloud (in Azure oder anderswo) und in der lokalen Umgebung bereitgestellt wird.

Weitere Informationen finden Sie in der [Azure Security Center-Produktdokumentation](../../security-center/security-center-introduction.md).

In der folgenden Tabelle wird die aktuelle Verfügbarkeit von Security Center-Features in Azure und Azure Government angezeigt.


| Funktion/Dienst                                                                                                                                                               | Azure          | Azure Government               |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|--------------------------------|
| **Security Center: kostenlose Funktionen**                                                                                                                                             |                |                                |
| - [Fortlaufender Export](../../security-center/continuous-export.md)                                                                                                             | Allgemein verfügbar             | Allgemein verfügbar                             |
| - [Workflowautomatisierung](../../security-center/continuous-export.md)                                                                                                           | Allgemein verfügbar             | Allgemein verfügbar                             |
| - [Empfehlungsausnahmeregeln](../../security-center/exempt-resource.md)                                                                                                  | Öffentliche Vorschau | Nicht verfügbar                  | 
| - [Warnungsunterdrückungsregeln](../../security-center/alerts-suppression-rules.md)                                                                                                | Allgemein verfügbar             | Allgemein verfügbar                             | 
| - [E-Mail-Benachrichtigungen für Sicherheitswarnungen](../../security-center/security-center-provide-security-contact-details.md)                                                        | Allgemein verfügbar             | Allgemein verfügbar                             | 
| - [Automatische Bereitstellung für Agents und Erweiterungen](../../security-center/security-center-enable-data-collection.md)                                                              | Allgemein verfügbar             | Allgemein verfügbar                             | 
| - [Ressourcenbestand](../../security-center/asset-inventory.md)                                                                                                                 | Allgemein verfügbar             | Allgemein verfügbar                             | 
| - [Azure Monitor-Arbeitsmappenberichte im Azure Security Center-Arbeitsmappenkatalog](../../security-center/custom-dashboards-azure-workbooks.md)                                  | Allgemein verfügbar             | Allgemein verfügbar                             | 
| **Azure Defender-Pläne und Erweiterungen**                                                                                                                                       |                |                                | 
| - [Azure Defender für Server](../../security-center/defender-for-servers-introduction.md)                                                                                    | Allgemein verfügbar             | Allgemein verfügbar                             | 
| - [Azure Defender für App Service](../../security-center/defender-for-app-service-introduction.md)                                                                            | Allgemein verfügbar             | Nicht verfügbar                  | 
| - [Azure Defender für DNS](../../security-center/defender-for-dns-introduction.md)                                                                                            | Allgemein verfügbar             | Allgemein verfügbar                             | 
| - [Azure Defender für Containerregistrierungen](../../security-center/defender-for-container-registries-introduction.md) <sup>[1](#footnote1)</sup>                               | Allgemein verfügbar             | Allgemein verfügbar <sup>[2](#footnote2)</sup> | 
| - [Azure Defender für Containerregistrierungen zum Überprüfen von Images in CI/CD-Workflows](../../security-center/defender-for-container-registries-cicd.md) <sup>[3](#footnote3)</sup> | Öffentliche Vorschau | Nicht verfügbar                  | 
| - [Azure Defender für Kubernetes](../../security-center/defender-for-kubernetes-introduction.md) <sup>[4](#footnote4)</sup>                                                   | Allgemein verfügbar             | Allgemein verfügbar                             | 
| - [Azure Defender-Erweiterung für Azure Arc-fähige Kubernetes-Cluster ](../../security-center/defender-for-kubernetes-azure-arc.md) <sup>[5](#footnote5)</sup>                 | Öffentliche Vorschau | Nicht verfügbar                  | 
| - [Azure Defender für Azure SQL-Datenbank-Server](../../security-center/defender-for-sql-introduction.md)                                                                     | Allgemein verfügbar             | Allgemein verfügbar                             | 
| - [Azure Defender für SQL-Server auf Computern](../../security-center/defender-for-sql-introduction.md)                                                                        | Allgemein verfügbar             | Allgemein verfügbar                             |
| - [Azure Defender für relationale Open-Source-Datenbanken](../../security-center/defender-for-databases-introduction.md)                                                         | Allgemein verfügbar             | Nicht verfügbar                  |
| - [Azure Defender für Key Vault](../../security-center/defender-for-key-vault-introduction.md)                                                                                | Allgemein verfügbar             | Nicht verfügbar                  |
| - [Azure Defender für Resource Manager](../../security-center/defender-for-resource-manager-introduction.md)                                                                  | Allgemein verfügbar             | Allgemein verfügbar                             |
| - [Azure Defender für Storage](../../security-center/defender-for-storage-introduction.md) <sup>[6](#footnote6)</sup>                                                         | Allgemein verfügbar             | Allgemein verfügbar                             |
| - [Bedrohungsschutz für Cosmos DB](../../security-center/other-threat-protections.md#threat-protection-for-azure-cosmos-db-preview)                                          | Öffentliche Vorschau | Nicht verfügbar                  |
| - [Kubernetes-Workloadschutz](../../security-center/kubernetes-workload-protections.md)                                                                                  | Allgemein verfügbar             | Allgemein verfügbar                             |
| - [Bidirektionale Synchronisierung von Warnungen mit Sentinel](../../sentinel/connect-azure-security-center.md)                                                                       | Öffentliche Vorschau | Nicht verfügbar                  | 
| **Azure Defender für Serverfunktionen** <sup>[7](#footnote7)</sup>                                                                                                            |                |                                |
| - [Just-In-Time-VM-Zugriff](../../security-center/security-center-just-in-time.md)                                                                                             | Allgemein verfügbar             | Allgemein verfügbar                             |
| - [Dateiintegritätsüberwachung](../../security-center/security-center-file-integrity-monitoring.md)                                                                             | Allgemein verfügbar             | Allgemein verfügbar                             |
| - [Adaptive Anwendungssteuerungen](../../security-center/security-center-adaptive-application.md)                                                                              | Allgemein verfügbar             | Allgemein verfügbar                             |
| - [Adaptives Erhöhen des Netzwerkschutzes](../../security-center/security-center-adaptive-network-hardening.md)                                                                           | Allgemein verfügbar             | Nicht verfügbar                  |
| - [Docker-Hosthärtung](../../security-center/harden-docker-hosts.md)                                                                                                       | Allgemein verfügbar             | Allgemein verfügbar                             |
| - [Integrierte Sicherheitsrisikobewertung für Computer](../../security-center/deploy-vulnerability-assessment-vm.md)                                                             | Allgemein verfügbar             | Nicht verfügbar                  |
| - [Dashboard und Berichte für die Einhaltung gesetzlicher Bestimmungen](../../security-center/security-center-compliance-dashboard.md) <sup>[8](#footnote8)</sup>                                       | Allgemein verfügbar             | Allgemein verfügbar                             |
| - [Microsoft Defender für Endpunkt: Bereitstellung und integrierte Lizenz](../../security-center/security-center-wdatp.md)                                                         | Allgemein verfügbar             | Allgemein verfügbar                             |
| - [Verbinden von AWS-Konten](../../security-center/quickstart-onboard-aws.md)                                                                                                      | Allgemein verfügbar             | Nicht verfügbar                  |
| - [Verbinden von GCP-Konten](../../security-center/quickstart-onboard-gcp.md)                                                                                                      | Allgemein verfügbar             | Nicht verfügbar                  |
|                                                                                                                                                                               |                |                                |

<sup><a name="footnote1" /></a>1</sup> Teilweise allgemeine Verfügbarkeit: Die Möglichkeit, bestimmte Ergebnisse von Sicherheitsrisikoüberprüfungen zu deaktivieren, befindet sich in der öffentlichen Vorschau.

<sup><a name="footnote2" /></a>2</sup> Sicherheitsrisikoüberprüfungen von Containerregistrierungen in Azure Gov können nur mit der Funktion zur Überprüfung beim Pushen durchgeführt werden.

<sup><a name="footnote3" /></a>3</sup> Erfordert Azure Defender für Containerregistrierungen

<sup><a name="footnote4" /></a>4</sup> Teilweise allgemeine Verfügbarkeit: Die Unterstützung für Azure Arc-fähige Cluster befindet sich in der öffentlichen Vorschauphase und ist in Azure Government nicht verfügbar.

<sup><a name="footnote5" /></a>5</sup> Erfordert Azure Defender für Kubernetes

<sup><a name="footnote6" /></a>6</sup> Teilweise allgemeine Verfügbarkeit: Einige Warnungen zum Bedrohungsschutz von Azure Defender für Storage befinden sich in der öffentlichen Vorschau.

<sup><a name="footnote7" /></a>7</sup> Diese Funktionen erfordern [Azure Defender für Server](../../security-center/defender-for-servers-introduction.md).

<sup><a name="footnote8" /></a>8</sup> Es gibt möglicherweise Unterschiede in den Standards, die für die einzelnen Cloudtypen angeboten werden.

## <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel ist eine skalierbare, cloudnative Lösung für Security Information & Event Management (SIEM) und die Sicherheitsorchestrierung mit automatisierter Reaktion (Security Orchestration Automated Response, SOAR). Azure Sentinel bietet intelligente Sicherheits- und Bedrohungsanalysen für das ganze Unternehmen und stellt eine zentrale Lösung für die Warnungs- und Bedrohungserkennung, die proaktive Suche sowie die Reaktion auf Bedrohungen bereit.

Weitere Informationen finden Sie in der [Azure Sentinel-Produktdokumentation](../../sentinel/overview.md).

In den folgenden Tabellen wird die aktuelle Azure Sentinel-Featureverfügbarkeit in Azure und Azure Government gezeigt.


| Funktion | Azure | Azure Government  |
| ----- | ----- | ---- |
|- [Automatisierungsregeln](../../sentinel/automate-incident-handling-with-automation-rules.md) | Öffentliche Vorschau | Öffentliche Vorschau |
|- [Bring Your Own ML (BYO-ML)](../../sentinel/bring-your-own-ml.md) | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Mandantenübergreifende/arbeitsbereichsübergreifende Incidentansicht](../../sentinel/multiple-workspace-view.md) |Öffentliche Vorschau | Öffentliche Vorschau |
| - [Erkenntnisse über Entitäten](../../sentinel/enable-entity-behavior-analytics.md) | Allgemein verfügbar | Öffentliche Vorschau |
| - [Fusion](../../sentinel/fusion.md)<br>Erweiterte Erkennung von mehrstufigen Angriffen<sup>[1](#footnote1)</sup> | Allgemein verfügbar | Allgemein verfügbar |
| - [Hunting](../../sentinel/hunting.md) | Allgemein verfügbar | Allgemein verfügbar |
|- [Notebooks](../../sentinel/notebooks.md) | Allgemein verfügbar | Allgemein verfügbar |
|- [Überwachungsmetriken für SOC-Incidents](../../sentinel/manage-soc-with-incident-metrics.md) | Allgemein verfügbar | Allgemein verfügbar |
|- [Watchlists](../../sentinel/watchlists.md) | Allgemein verfügbar | Allgemein verfügbar |
| **Threat Intelligence-Unterstützung** | | |
| - [Threat Intelligence – TAXII-Datenconnector](../../sentinel/understand-threat-intelligence.md)  | Allgemein verfügbar | Nicht verfügbar |
| - [Threat Intelligence-Plattform-Datenconnector](../../sentinel/understand-threat-intelligence.md)  | Öffentliche Vorschau | Nicht verfügbar |
| - [Blatt „Threat Intelligence-Forschung“](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-threat-intelligence-menu-item-in-public-preview/ba-p/1646597)  | Allgemein verfügbar | Nicht verfügbar |
| - [URL-Detonation](https://techcommunity.microsoft.com/t5/azure-sentinel/using-the-new-built-in-url-detonation-in-azure-sentinel/ba-p/996229) | Öffentliche Vorschau | Nicht verfügbar |
| - [Arbeitsmappe „Threat Intelligence“](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)  | Allgemein verfügbar | Nicht verfügbar |
|**Erkennungsunterstützung** | | |
| - [Erkennung anomaler Zugriffe auf Windows-Dateifreigaben](../../sentinel/fusion.md)  | Öffentliche Vorschau | Nicht verfügbar |
| - [Erkennung anomaler RDP-Anmeldungen](../../sentinel/connect-windows-security-events.md#configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection)<br>Integrierte ML-Erkennung | Öffentliche Vorschau | Nicht verfügbar |
| - [Erkennung anomaler SSH-Anmeldungen](../../sentinel/connect-syslog.md#configure-the-syslog-connector-for-anomalous-ssh-login-detection)<br>Integrierte ML-Erkennung | Öffentliche Vorschau | Nicht verfügbar |
| **Azure-Dienstconnectors** |  |  |
| - [Azure-Aktivitätsprotokolle](../../sentinel/data-connectors-reference.md#azure-activity) | Allgemein verfügbar | Allgemein verfügbar |
| - [Azure Active Directory](../../sentinel/connect-azure-active-directory.md) | Allgemein verfügbar | Allgemein verfügbar |
| - [Azure ADIP](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection) | Allgemein verfügbar | Allgemein verfügbar |
| - [Azure DDoS Protection](../../sentinel/data-connectors-reference.md#azure-ddos-protection) | Allgemein verfügbar | Allgemein verfügbar |
| - [Azure Defender](../../sentinel/connect-azure-security-center.md) | Allgemein verfügbar | Allgemein verfügbar |
| - [Azure Defender für IoT](../../sentinel/data-connectors-reference.md#azure-defender-for-iot) | Öffentliche Vorschau | Nicht verfügbar |
| - [Azure Firewall ](../../sentinel/data-connectors-reference.md#azure-firewall) | Allgemein verfügbar | Allgemein verfügbar |
| - [Azure Information Protection](../../sentinel/data-connectors-reference.md#azure-information-protection) | Öffentliche Vorschau | Nicht verfügbar |
| - [Azure Key Vault](../../sentinel/data-connectors-reference.md#azure-key-vault) | Öffentliche Vorschau | Nicht verfügbar |
| - [Azure Kubernetes Services (AKS)](../../sentinel/data-connectors-reference.md#azure-kubernetes-service-aks) | Öffentliche Vorschau | Nicht verfügbar |
| - [Azure SQL-Datenbanken](../../sentinel/data-connectors-reference.md#azure-sql-databases) | Allgemein verfügbar | Allgemein verfügbar |
| - [Azure WAF](../../sentinel/data-connectors-reference.md#azure-web-application-firewall-waf) | Allgemein verfügbar | Allgemein verfügbar |
| **Windows-Connectors** |  |  |
| - [Windows-Firewall](../../sentinel/data-connectors-reference.md#windows-firewall) | Allgemein verfügbar | Allgemein verfügbar |
| - [Windows-Sicherheitsereignisse](../../sentinel/connect-windows-security-events.md) | Allgemein verfügbar | Allgemein verfügbar |
| **Externe Connectors** |  |  |
| - [Agari Phishing Defense and Brand Protection](../../sentinel/data-connectors-reference.md#agari-phishing-defense-and-brand-protection-preview) | Öffentliche Vorschau | Öffentliche Vorschau |
| - [AI Analyst Darktrace](../../sentinel/connect-data-sources.md) | Öffentliche Vorschau | Öffentliche Vorschau |
| - [AI Vectra Detect](../../sentinel/data-connectors-reference.md#ai-vectra-detect-preview) | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Akamai Security Events](../../sentinel/data-connectors-reference.md#akamai-security-events-preview) | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Alcide kAudit](../../sentinel/data-connectors-reference.md#alcide-kaudit) | Öffentliche Vorschau | Nicht verfügbar |
| - [Alsid für Active Directory](../../sentinel/data-connectors-reference.md#alsid-for-active-directory) | Öffentliche Vorschau | Nicht verfügbar |
| - [Apache HTTP Server](../../sentinel/data-connectors-reference.md#apache-http-server) | Öffentliche Vorschau | Nicht verfügbar |
| - [Aruba ClearPass](../../sentinel/data-connectors-reference.md#aruba-clearpass-preview) | Öffentliche Vorschau | Öffentliche Vorschau |
| - [AWS](../../sentinel/connect-data-sources.md) | Allgemein verfügbar | Allgemein verfügbar |
| - [Barracuda CloudGen Firewall](../../sentinel/data-connectors-reference.md#barracuda-cloudgen-firewall) | Allgemein verfügbar | Allgemein verfügbar |
| - [Barracuda Web App Firewall](../../sentinel/data-connectors-reference.md#barracuda-waf) | Allgemein verfügbar | Allgemein verfügbar |
| - [BETTER Mobile Threat Defense MTD](../../sentinel/data-connectors-reference.md#better-mobile-threat-defense-mtd-preview) | Öffentliche Vorschau | Nicht verfügbar |
| - [Beyond Security beSECURE](../../sentinel/data-connectors-reference.md#beyond-security-besecure) | Öffentliche Vorschau | Nicht verfügbar |
| - [Blackberry CylancePROTECT](../../sentinel/connect-data-sources.md) | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Broadcom Symantec DLP](../../sentinel/data-connectors-reference.md#broadcom-symantec-data-loss-prevention-dlp-preview) | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Check Point](../../sentinel/data-connectors-reference.md#check-point) | Allgemein verfügbar | Allgemein verfügbar |
| - [Cisco ASA](../../sentinel/data-connectors-reference.md#cisco-asa) | Allgemein verfügbar | Allgemein verfügbar |
| - [Cisco Meraki](../../sentinel/data-connectors-reference.md#cisco-meraki-preview) | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Cisco Umbrella](../../sentinel/data-connectors-reference.md#cisco-umbrella-preview) | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Cisco UCS](../../sentinel/data-connectors-reference.md#cisco-unified-computing-system-ucs-preview) | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Cisco Firepower EStreamer](../../sentinel/connect-data-sources.md) | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Citrix Analytics WAF](../../sentinel/data-connectors-reference.md#citrix-web-app-firewall-waf-preview) | Allgemein verfügbar | Allgemein verfügbar |
| - [Common Event Format (CEF)](../../sentinel/connect-common-event-format.md) | Allgemein verfügbar | Allgemein verfügbar |
| - [CyberArk EPV-Ereignisse (Enterprise Password Vault)](../../sentinel/data-connectors-reference.md#cyberark-enterprise-password-vault-epv-events-preview) | Öffentliche Vorschau | Öffentliche Vorschau |
| - [ESET Enterprise Inspector](../../sentinel/connect-data-sources.md)                       | Öffentliche Vorschau | Nicht verfügbar      |
| - [Eset Security Management Center](../../sentinel/connect-data-sources.md)                  | Öffentliche Vorschau | Nicht verfügbar      |
| - [ExtraHop Reveal(x)](../../sentinel/data-connectors-reference.md#extrahop-revealx)                               | Allgemein verfügbar             | Allgemein verfügbar             |
| - [F5 BIG-IP ](../../sentinel/data-connectors-reference.md#f5-big-ip)                                       | Allgemein verfügbar             | Allgemein verfügbar             |
| - [F5 Networks](../../sentinel/data-connectors-reference.md#f5-networks-asm)                                     | Allgemein verfügbar             | Allgemein verfügbar             |
| - [Forcepoint NGFW](../../sentinel/data-connectors-reference.md#forcepoint-cloud-access-security-broker-casb-preview)                                  | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Forcepoint CASB](../../sentinel/data-connectors-reference.md#forcepoint-cloud-access-security-broker-casb-preview)                                  | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Forcepoint DLP](../../sentinel/data-connectors-reference.md#forcepoint-data-loss-prevention-dlp-preview)                                   | Öffentliche Vorschau | Nicht verfügbar      |
| - [ForgeRock Common Audit for CEF](../../sentinel/connect-data-sources.md)                  | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Fortinet](../../sentinel/data-connectors-reference.md#fortinet)                                         | Allgemein verfügbar             | Allgemein verfügbar             |
| - [Google Workspace (G Suite) ](../../sentinel/data-connectors-reference.md#google-workspace-g-suite-preview)                      | Öffentliche Vorschau | Nicht verfügbar      |
| - [Illusive Attack Management System](../../sentinel/data-connectors-reference.md#illusive-attack-management-system-ams-preview)                | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Imperva WAF Gateway](../../sentinel/data-connectors-reference.md#imperva-waf-gateway-preview)                             | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Infoblox NIOS](../../sentinel/data-connectors-reference.md#infoblox-network-identity-operating-system-nios-preview)                                    | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Juniper SRX](../../sentinel/data-connectors-reference.md#juniper-srx-preview)                                      | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Morphisec UTPP](../../sentinel/connect-data-sources.md)                                   | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Netskope](../../sentinel/connect-data-sources.md)                                         | Öffentliche Vorschau | Öffentliche Vorschau |
| - [NXLog Windows DNS](../../sentinel/data-connectors-reference.md#nxlog-dns-logs-preview)                                             | Öffentliche Vorschau | Nicht verfügbar      |
| - [NXLog LinuxAudit](../../sentinel/data-connectors-reference.md#nxlog-linuxaudit-preview)                                 | Öffentliche Vorschau | Nicht verfügbar      |
| - [Okta Single Sign On](../../sentinel/data-connectors-reference.md#okta-single-sign-on-preview)                              | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Onapsis Platform](../../sentinel/connect-data-sources.md)                                 | Öffentliche Vorschau | Öffentliche Vorschau |
| - [One Identity Safeguard](../../sentinel/data-connectors-reference.md#one-identity-safeguard-preview)                          | Allgemein verfügbar             | Allgemein verfügbar             |
| - [Orca Security Alerts](../../sentinel/data-connectors-reference.md#orca-security-preview)                            | Öffentliche Vorschau | Nicht verfügbar      |
| - [Palo Alto Networks](../../sentinel/data-connectors-reference.md#palo-alto-networks)                               | Allgemein verfügbar             | Allgemein verfügbar             |
| - [Perimeter 81 Activity Logs](../../sentinel/data-connectors-reference.md#perimeter-81-activity-logs-preview)                      | Allgemein verfügbar             | Nicht verfügbar      |
| - [Proofpoint On Demand Email Security](../../sentinel/data-connectors-reference.md#proofpoint-on-demand-pod-email-security-preview)             | Öffentliche Vorschau | Nicht verfügbar      |
| - [Proofpoint TAP](../../sentinel/data-connectors-reference.md#proofpoint-targeted-attack-protection-tap-preview)                                   | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Pulse Connect Secure](../../sentinel/data-connectors-reference.md#proofpoint-targeted-attack-protection-tap-preview)                             | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Qualys Vulnerability Management](../../sentinel/data-connectors-reference.md#qualys-vulnerability-management-vm-preview)                  | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Salesforce Service Cloud](../../sentinel/data-connectors-reference.md#salesforce-service-cloud-preview)                         | Öffentliche Vorschau | Nicht verfügbar      |
| - [SonicWall Firewall ](../../sentinel/data-connectors-reference.md#sophos-cloud-optix-preview)                              | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Sophos Cloud Optix](../../sentinel/data-connectors-reference.md#sophos-cloud-optix-preview)                               | Öffentliche Vorschau | Nicht verfügbar      |
| - [Sophos XG Firewall](../../sentinel/data-connectors-reference.md#sophos-xg-firewall-preview)                               | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Squadra Technologies secRMM](../../sentinel/data-connectors-reference.md#squadra-technologies-secrmm)               | Allgemein verfügbar             | Allgemein verfügbar             |
| - [Squid Proxy](../../sentinel/data-connectors-reference.md#squid-proxy-preview)                                      | Öffentliche Vorschau | Nicht verfügbar      |
| - [Symantec Integrated Cyber Defense Exchange](../../sentinel/data-connectors-reference.md#symantec-integrated-cyber-defense-exchange-icdx)       | Allgemein verfügbar             | Allgemein verfügbar             |
| - [Symantec ProxySG](../../sentinel/data-connectors-reference.md#symantec-proxysg-preview)                                | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Symantec VIP](../../sentinel/data-connectors-reference.md#symantec-vip-preview)                                     | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Syslog](../../sentinel/connect-syslog.md)                                           | Allgemein verfügbar             | Allgemein verfügbar             |
| - [Threat Intelligence-Plattform](../../sentinel/connect-threat-intelligence-tip.md)en                   | Öffentliche Vorschau | Nicht verfügbar      |
| - [Threat Intelligence TAXII](../../sentinel/connect-threat-intelligence-tip.md)                       | Öffentliche Vorschau | Nicht verfügbar      |
| - [Thycotic Secret Server](../../sentinel/data-connectors-reference.md#thycotic-secret-server-preview)                          | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Trend Micro Deep Security](../../sentinel/data-connectors-reference.md#trend-micro-deep-security)                       | Allgemein verfügbar             | Allgemein verfügbar             |
| - [Trend Micro TippingPoint](../../sentinel/data-connectors-reference.md#trend-micro-tippingpoint-preview)                         | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Trend Micro XDR](../../sentinel/connect-data-sources.md)                                  | Öffentliche Vorschau | Nicht verfügbar      |
| - [VMware Carbon Black Endpoint Standard](../../sentinel/data-connectors-reference.md#vmware-carbon-black-endpoint-standard-preview)           | Öffentliche Vorschau | Öffentliche Vorschau |
| - [VMware ESXi](../../sentinel/data-connectors-reference.md#vmware-esxi-preview)                                      | Öffentliche Vorschau | Öffentliche Vorschau |
| - [WireX Network Forensics Platform](../../sentinel/data-connectors-reference.md#wirex-network-forensics-platform-preview)                | Öffentliche Vorschau | Öffentliche Vorschau |
| - [Zimperium Mobile Threat Defense](../../sentinel/data-connectors-reference.md#zimperium-mobile-thread-defense-preview)                  | Öffentliche Vorschau | Nicht verfügbar      |
| - [Zscaler](../../sentinel/data-connectors-reference.md#zscaler)                                         | Allgemein verfügbar             | Allgemein verfügbar             |
| | | |


<sup><a name="footnote1" /></a>1</sup> SSH- und RDP-Erkennungen werden für Sovereign Clouds nicht unterstützt, da die Databricks ML-Plattform nicht verfügbar ist.

### <a name="microsoft-365-data-connectors"></a>Microsoft 365-Datenconnectors

Office 365 GCC ist mit Azure Active Directory (Azure AD) in Azure gekoppelt. Office 365 GCC High und Office 365 DoD sind mit Azure AD in Azure Government gekoppelt.

> [!TIP]
> Achten Sie unbedingt auf die Azure-Umgebung, um zu verstehen, wo [Interoperabilität möglich ist](#microsoft-365-integration). In der folgenden Tabelle wird die Interoperabilität, die *nicht* möglich ist, mit einem Bindestrich (-) markiert, um anzugeben, dass die Unterstützung nicht relevant ist.
>

| Connector | Azure | Azure Government |
|--|--|--|
| **[Dynamics365](../../sentinel/data-connectors-reference.md#dynamics-365)** |  |  |
| - Office 365 GCC | Öffentliche Vorschau | - |
| - Office 365 GCC High | - | Nicht verfügbar |
| - Office 365 DoD | - | Nicht verfügbar |
| **[Microsoft 365 Defender](../../sentinel/connect-microsoft-365-defender.md)** |  |  |
| - Office 365 GCC | Öffentliche Vorschau | - |
| - Office 365 GCC High | - | Nicht verfügbar |
| - Office 365 DoD | - | Nicht verfügbar |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/data-connectors-reference.md#microsoft-cloud-app-security-mcas)** |  |  |
| - Office 365 GCC | Allgemein verfügbar | - |
| - Office 365 GCC High | - | Allgemein verfügbar |
| - Office 365 DoD | - | Allgemein verfügbar |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/data-connectors-reference.md#microsoft-cloud-app-security-mcas)** <br>Schatten-IT-Protokolle |  |  |
| - Office 365 GCC | Öffentliche Vorschau | - |
| - Office 365 GCC High | - | Öffentliche Vorschau |
| - Office 365 DoD | - | Öffentliche Vorschau |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/data-connectors-reference.md#microsoft-cloud-app-security-mcas)**                  <br>Alerts |  |  |
| - Office 365 GCC | Öffentliche Vorschau | - |
| - Office 365 GCC High | - | Öffentliche Vorschau |
| - Office 365 DoD | - | Öffentliche Vorschau |
| **[Microsoft Defender für den Endpunkt](../../sentinel/data-connectors-reference.md#microsoft-defender-for-endpoint)** |  |  |
| - Office 365 GCC | Allgemein verfügbar | - |
| - Office 365 GCC High | - | Allgemein verfügbar |
| - Office 365 DoD | - | Allgemein verfügbar |
| **[Microsoft Defender for Identity](../../sentinel/data-connectors-reference.md#microsoft-defender-for-identity)** |  |  |
| - Office 365 GCC | Öffentliche Vorschau | - |
| - Office 365 GCC High | - | Nicht verfügbar |
| - Office 365 DoD | - | Nicht verfügbar |
| **[Microsoft Defender für Office 365](../../sentinel/data-connectors-reference.md#microsoft-defender-for-office-365)** |  |  |
| - Office 365 GCC | Öffentliche Vorschau | - |
| - Office 365 GCC High | - | Nicht verfügbar |
| - Office 365 DoD | - | Nicht verfügbar |
| **[Office 365](../../sentinel/data-connectors-reference.md#microsoft-office-365)** |  |  |
| - Office 365 GCC | Allgemein verfügbar | - |
| - Office 365 GCC High | - | Allgemein verfügbar |
| - Office 365 DoD | - | Allgemein verfügbar |
|  |  |

## <a name="azure-defender-for-iot"></a>Azure Defender für IoT

Mit Azure Defender für IoT können Sie IoT/OT-Innovationen mit umfassender Sicherheit für alle Ihre IoT/OT-Geräte beschleunigen.Für Endbenutzerorganisationen bietet Azure Defender für IoT Sicherheit auf Netzwerkebene ohne Agents, die schnell bereitgestellt werden kann, mit verschiedenen Branchensystemen funktioniert und mit Azure Sentinel und anderen SOC-Tools interagiert. Die Bereitstellung kann lokal oder in mit Azure verbundenen Umgebungen erfolgen.Mit den Azure Defender für IoT-Sicherheits-Agents können IoT-Geräteentwickler die Sicherheit direkt in Ihre neuen IoT-Geräte und Azure IoT-Projekte einbinden. Der Micro-Agent ist mit flexiblen Bereitstellungsoptionen ausgestattet. Dazu zählen auch die Bereitstellung als binäres Paket oder Änderungen des Quellcodes. Der Micro-Agent ist zudem für IoT-Standardbetriebssysteme wie Linux und Azure RTOS verfügbar. Weitere Informationen finden Sie in der [Azure Defender für IoT-Produktdokumentation](../../defender-for-iot/index.yml).

In der folgenden Tabelle wird die aktuelle Verfügbarkeit von Azure Defender für IoT-Features in Azure und Azure Government angezeigt.

### <a name="for-organizations"></a>Für Organisationen

| Funktion | Azure | Azure Government |
|--|--|--|
| [Ermittlung und Inventur von lokalen Geräten](../../defender-for-iot/how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md) | Allgemein verfügbar | Allgemein verfügbar |
| [Verwaltung von Sicherheitsrisiken](../../defender-for-iot/how-to-create-risk-assessment-reports.md) | Allgemein verfügbar | Allgemein verfügbar |
| [Bedrohungserkennung mit IoT und OT-Verhaltensanalysen](../../defender-for-iot/how-to-work-with-alerts-on-your-sensor.md) | Allgemein verfügbar | Allgemein verfügbar |
| [Manuelle und automatische Threat Intelligence-Updates](../../defender-for-iot/how-to-work-with-threat-intelligence-packages.md) | Allgemein verfügbar | Allgemein verfügbar |
| **Vereinheitlichen der IT- und OT-Sicherheit mit SIEM, SOAR und XDR** |  |  |
| [Active Directory](../../defender-for-iot/organizations/how-to-create-and-manage-users.md#integrate-with-active-directory-servers) | Allgemein verfügbar | Allgemein verfügbar |
| [ArcSight](../../defender-for-iot/organizations/how-to-accelerate-alert-incident-response.md#accelerate-incident-workflows-by-using-alert-groups) | Allgemein verfügbar | Allgemein verfügbar |
| [ClearPass (Warnungen & Bestand)](../../defender-for-iot/organizations/how-to-install-software.md#attach-a-span-virtual-interface-to-the-virtual-switch) | Allgemein verfügbar | Allgemein verfügbar |
| [CyberArk PSM](../../defender-for-iot/organizations/concept-key-concepts.md#integrations) | Allgemein verfügbar | Allgemein verfügbar |
| [E-Mail](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#email-address-action) | Allgemein verfügbar | Allgemein verfügbar |
| [FortiGate](../../defender-for-iot/organizations/tutorial-fortinet.md) | Allgemein verfügbar | Allgemein verfügbar |
| [FortiSIEM](../../defender-for-iot/organizations/tutorial-fortinet.md) | Allgemein verfügbar | Allgemein verfügbar |
| [Microsoft Sentinel](../../defender-for-iot/organizations/how-to-configure-with-sentinel.md) | Öffentliche Vorschau | Öffentliche Vorschau |
| [NetWitness](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#netwitness-action) | Allgemein verfügbar | Allgemein verfügbar |
| [Palo Alto NGFW](../../defender-for-iot/organizations/tutorial-palo-alto.md) | Allgemein verfügbar | Allgemein verfügbar |
| [Palo Alto Panorama](../../defender-for-iot/organizations/tutorial-palo-alto.md) | Allgemein verfügbar | Allgemein verfügbar |
| [ServiceNow (Warnungen & Bestand)](../../defender-for-iot/organizations/tutorial-servicenow.md) | Allgemein verfügbar | Allgemein verfügbar |
| [SNMP-MIB-Überwachung](../../defender-for-iot/organizations/how-to-set-up-snmp-mib-monitoring.md) | Allgemein verfügbar | Allgemein verfügbar |
| [Splunk](../../defender-for-iot/organizations/tutorial-splunk.md) | Allgemein verfügbar | Allgemein verfügbar |
| [SYSLOG Server (CEF-Format)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | Allgemein verfügbar | Allgemein verfügbar |
| [SYSLOG Server (LEEF-Format)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | Allgemein verfügbar | Allgemein verfügbar |
| [SYSLOG Server (Objekt)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | Allgemein verfügbar | Allgemein verfügbar |
| [SYSLOG Server (SMS)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | Allgemein verfügbar | Allgemein verfügbar |
| [Webrückruf (Webhook)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#webhook-server-action) | Allgemein verfügbar | Allgemein verfügbar |

### <a name="for-device-builders"></a>Für Geräteentwickler

| Funktion | Azure | Azure Government |
|--|--|--|
| [Micro-Agent für Azure RTOS](../../defender-for-iot/iot-security-azure-rtos.md) | Allgemein verfügbar | Allgemein verfügbar |
| [Konfigurieren von Sentinel mit Azure Defender für IoT](../../defender-for-iot/how-to-configure-with-sentinel.md) | Öffentliche Vorschau | Öffentliche Vorschau |
| **Eigenständiger Micro-Agent für Linux** |  |  |
| [Binäre Installation des eigenständigen Agents](../../defender-for-iot/quickstart-standalone-agent-binary-installation.md) | Öffentliche Vorschau | Öffentliche Vorschau |

## <a name="next-steps"></a>Nächste Schritte

- Grundlegendes zum Modell [gemeinsamer Verantwortung](shared-responsibility.md) sowie welche Sicherheitsaufgaben vom Cloudanbieter und welche Aufgaben von Ihnen verarbeitet werden.
- Grundlegendes zu den Funktionen der [Azure Government Cloud](../../azure-government/documentation-government-welcome.md) und dem vertrauenswürdigen Design und der Sicherheit, die zur Unterstützung der Compliance für Bundes-, Staats- und lokale Behörden und deren Partner verwendet werden.
- Grundlegendes zum [Office 365 Government-Plan](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/office-365-us-government#about-office-365-government-environments).
- Grundlegendes zur [Compliance in Azure](../../compliance/index.yml) für rechtliche und gesetzliche Standards.
