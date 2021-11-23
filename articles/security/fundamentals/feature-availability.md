---
title: Verfügbarkeit des Azure-Dienstcloudfeatures für US-Regierungskunden
description: Auflistung der Featureverfügbarkeit für Azure-Sicherheitsdienste, z. B. Microsoft Sentinel für US-Regierungskunden
author: TerryLanfear
ms.author: terrylan
ms.service: security
ms.topic: reference
ms.date: 09/13/2021
ms.openlocfilehash: 9d2b984d78abd09e1fe4baf7e0c443e83ad8dc13
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523104"
---
# <a name="cloud-feature-availability-for-us-government-customers"></a>Verfügbarkeit von Cloudfeatures für US-Regierungskunden

In diesem Artikel wird die Featureverfügbarkeit in den Microsoft Azure und Azure Government Clouds für die folgenden Sicherheitsdienste beschrieben:

- [Azure Information Protection](#azure-information-protection)
- [Microsoft Defender für Cloud](#microsoft-defender-for-cloud)
- [Microsoft Sentinel](#microsoft-sentinel)
- [Microsoft Defender für IoT](#microsoft-defender-for-iot)
- [Azure Attestation](#azure-attestation)

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

## <a name="azure-information-protection"></a>Azure Information Protection

Azure Information Protection (AIP) ist eine cloudbasierte Lösung, die Organisationen das Erkennen, Klassifizieren und Schützen von Dokumenten und E-Mails durch Anwendung von Bezeichnungen auf Inhalte ermöglicht.

AIP gehört zur Microsoft Information Protection-Lösung (MIP) und erweitert die [Bezeichnungs](/microsoft-365/compliance/sensitivity-labels)- und [Klassifizierungsfunktionen](/microsoft-365/compliance/data-classification-overview) von Microsoft 365.

Weitere Informationen finden Sie in der [Produktdokumentation zu Azure Information Protection](/azure/information-protection/).

- Office 365 GCC ist mit Azure Active Directory (Azure AD) in Azure gekoppelt. Office 365 GCC High und Office 365 DoD sind mit Azure AD in Azure Government gekoppelt. Achten Sie unbedingt auf die Azure-Umgebung, um zu verstehen, wo [Interoperabilität möglich ist](#microsoft-365-integration). In der folgenden Tabelle wird die Interoperabilität, die *nicht* möglich ist, mit einem Bindestrich (-) markiert, um anzugeben, dass die Unterstützung nicht relevant ist.

- Für GCC High- und DoD-Kunden sind zusätzliche Konfigurationen erforderlich. Weitere Informationen finden Sie in der [Beschreibung der Azure Information Protection Premium Government-Dienste](/enterprise-mobility-security/solutions/ems-aip-premium-govt-service-description).

> [!NOTE]
> Weitere Informationen zur Unterstützung für Regierungskunden finden Sie in Fußnoten unterhalb der Tabelle.
>
> Zusätzliche Schritte sind erforderlich, um Azure Information Protection für GCC High- und DoD-Kunden zu konfigurieren. Weitere Informationen finden Sie in der [Beschreibung der Azure Information Protection Premium Government-Dienste](/enterprise-mobility-security/solutions/ems-aip-premium-govt-service-description).
>

|Funktion/Dienst  |Azure  |Azure Government  |
|---------|---------|---------|
|**[Azure Information Protection-Scanner](/azure/information-protection/deploy-aip-scanner)** <sup>[1](#aipnote1)</sup>       |         |         |
| - Office 365 GCC | Allgemein verfügbar | - |
| - Office 365 GCC High | - | Allgemein verfügbar |
| - Office 365 DoD | - | Allgemein verfügbar |
|**Verwaltung**     |         |         |
|[Azure Information Protection-Portal für die Scannerverwaltung](/azure/information-protection/deploy-aip-scanner-configure-install?tabs=azure-portal-only)     |         |         |
| - Office 365 GCC | Allgemein verfügbar | - |
| - Office 365 GCC High | - | Allgemein verfügbar |
| - Office 365 DoD | - | Allgemein verfügbar |
| **Klassifizierung und Bezeichnung** <sup>[2](#aipnote2)</sup>   |         |         |
| [AIP-Scanner zur Anwendung einer *Standardbezeichnung* auf alle Dateien in einem lokalen Dateiserver/Repository](/azure/information-protection/deploy-aip-scanner-configure-install?tabs=azure-portal-only)    |         |         |
| - Office 365 GCC | Allgemein verfügbar | - |
| - Office 365 GCC High | - | Allgemein verfügbar |
| - Office 365 DoD | - | Allgemein verfügbar |
| [AIP-Scanner für automatisierte Klassifizierung, Bezeichnung und Schutz unterstützter lokaler Dateien](/azure/information-protection/deploy-aip-scanner)    |         |         |
| - Office 365 GCC | Allgemein verfügbar | - |
| - Office 365 GCC High | - | Allgemein verfügbar |
| - Office 365 DoD | - | Allgemein verfügbar |
| |  |  |

<sup><a name="aipnote1"></a>1</sup> Der Scanner kann ohne Office 365 nur zum Scannen von Dateien verwendet werden. Der Scanner kann ohne Office 365 keine Bezeichnungen auf Dateien anwenden.

<sup><a name="aipnote2"></a>2</sup> Das Klassifizierungs- und Bezeichnungs-Add-In wird nur für Regierungskunden mit Microsoft 365 Apps (Version 9126.1001 oder höher) unterstützt, einschließlich der Versionen Professional Plus (ProPlus) und Klick-und-Los (C2R). Office 2010, Office 2013 und andere Office 2016-Versionen werden nicht unterstützt.

### <a name="office-365-features"></a>Office 365-Features

|Funktion/Dienst  |Office 365 GCC  |Office 365 GCC High |Office 365 DoD  |
|---------|---------|---------|---------|
|**Verwaltung**     |         |         | |
|- [PowerShell für die RMS-Dienstverwaltung](/powershell/module/aipservice/)      |  Allgemein verfügbar       |    Allgemein verfügbar     |   Allgemein verfügbar      |
|- [PowerShell für AIP UL-Client-Massenvorgänge](/powershell/module/azureinformationprotection/)      |         |         |         |
|**SDK**     |         |         |         |
|- [MIP und AIP Software Development Kit (SDK)](/information-protection/develop/)     |     Allgemein verfügbar       |    Allgemein verfügbar     |   Allgemein verfügbar  |
|**Anpassungen**     |         |         |         |
|- [Dokumentenverfolgung und -sperrung](/azure/information-protection/rms-client/track-and-revoke-admin)      |   Allgemein verfügbar      |  Nicht verfügbar       |     Nicht verfügbar    |
|**Schlüsselverwaltung**      |         |         |         |
|- [Bring Your Own Key (BYOK)](/azure/information-protection/byok-price-restrictions)      |   Allgemein verfügbar       |    Allgemein verfügbar     |   Allgemein verfügbar   |
|- [Verschlüsselung mit doppeltem Schlüssel (DKE)](/azure/information-protection/plan-implement-tenant-key)     |    Allgemein verfügbar       |    Allgemein verfügbar     |   Allgemein verfügbar    |
|**Office-Dateien** <sup>[3](#aipnote6)</sup>      |         |         |         |
|- [Schutz für Microsoft Exchange Online, Microsoft SharePoint Online und Microsoft OneDrive for Business](/azure/information-protection/requirements-applications)      |     Allgemein verfügbar    |  Allgemein verfügbar <sup>[4](#aipnote3)</sup>       |   Allgemein verfügbar <sup>[4](#aipnote3)</sup>      |
|- [Schutz für lokale Exchange- und SharePoint-Instanzen über den Rights Management Connector](/azure/information-protection/deploy-rms-connector)     |    Allgemein verfügbar <sup>[5](#aipnote5)</sup>      |  Nicht verfügbar       |     Nicht verfügbar         |
|- [Office 365-Nachrichtenverschlüsselung](/microsoft-365/compliance/set-up-new-message-encryption-capabilities)      |     Allgemein verfügbar       |    Allgemein verfügbar     |   Allgemein verfügbar        |
|- [Festlegen von Bezeichnungen zum automatischen Anwenden des vorkonfigurierten M/MIME-Schutzes in Outlook](/azure/information-protection/rms-client/clientv2-admin-guide-customizations)      |         Allgemein verfügbar       |    Allgemein verfügbar     |   Allgemein verfügbar        |
|- [Vermeiden übermäßiger Informationsfreigaben bei Verwendung von Outlook](/azure/information-protection/rms-client/clientv2-admin-guide-customizations)     |      Allgemein verfügbar   |  Allgemein verfügbar <sup>[6](#aipnote6)</sup>        |    Allgemein verfügbar <sup>[6](#aipnote6)</sup>      |
|**Klassifizierung und Bezeichnung** <sup>[2](#aipnote2) / [7](#aipnote7)</sup>      |         |         |         |
|– Benutzerdefinierte Vorlagen, einschließlich Abteilungsvorlagen     |     Allgemein verfügbar       |    Allgemein verfügbar     |   Allgemein verfügbar         |
|– Manuelle, Standard- und obligatorische Dokumentklassifizierung     |       Allgemein verfügbar       |    Allgemein verfügbar     |   Allgemein verfügbar       |
|– Konfigurieren von Bedingungen für die automatische und empfohlene allgemeine Klassifizierung      Allgemein verfügbar       |    Allgemein verfügbar     |   Allgemein verfügbar        |
|- [Schutz für Nicht-Microsoft Office-Dateiformate, einschließlich PTXT, PJPG und PFILE (generischer Schutz)](/azure/information-protection/rms-client/clientv2-admin-guide-file-types)     |        Allgemein verfügbar       |    Allgemein verfügbar     |   Allgemein verfügbar       |
|     |         |         |         |

<sup><a name="aipnote3"></a>3</sup> Die Mobilgeräteerweiterung für AD RMS ist derzeit nicht für Regierungskunden verfügbar.

<sup><a name="aipnote4"></a>4</sup> Informationen Rights Management mit SharePoint Online (IRM-geschützte Websites und Bibliotheken) sind derzeit nicht verfügbar.

<sup><a name="aipnote5"></a>5</sup> Information Rights Management (IRM) wird nur für Microsoft 365 Apps (Version 9126.1001 oder höher) unterstützt, einschließlich der Versionen Professional Plus (ProPlus) und Klick-und-Los (C2R). Office 2010, Office 2013 und andere Office 2016-Versionen werden nicht unterstützt.

<sup><a name="aipnote6"></a>6</sup> Die Freigabe geschützter Dokumente und E-Mails aus Government-Clouds für Benutzer in der kommerziellen Cloud ist derzeit nicht verfügbar. Gilt für Microsoft 365 Apps-Benutzer in der kommerziellen Cloud, Nicht-Microsoft 365 Apps Benutzer in der kommerziellen Cloud und Benutzer mit einer RMS for Individuals-Lizenz.

<sup><a name="aipnote7"></a>7</sup> Die Anzahl der [Typen vertraulicher Informationen](/microsoft-365/compliance/sensitive-information-type-entity-definitions) in Ihrem Microsoft 365 Security & Compliance Center kann je nach Region variieren.

## <a name="microsoft-defender-for-cloud"></a>Microsoft Defender für Cloud

Microsoft Defender für Cloud ist ein vereinheitlichtes Sicherheitsverwaltungssystem für Infrastrukturen, mit dem der Sicherheitsstatus Ihrer Rechenzentren gestärkt wird und ein erweiterter Schutz vor Bedrohungen für Ihre Hybridworkloads in der Cloud (in Azure oder anderswo) und in der lokalen Umgebung bereitgestellt wird.

Weitere Informationen finden Sie in der Dokumentation zu [Microsoft Defender für Cloud](../../defender-for-cloud/defender-for-cloud-introduction.md).

In der folgenden Tabelle wird die aktuelle Verfügbarkeit von Defender für Cloud-Features in Azure und Azure Government angezeigt.

| Funktion/Dienst                                                                                                                                                                      | Azure          | Azure Government               |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|--------------------------------|
| **Kostenlose Features von Microsoft Defender für Cloud**                                                                                                                                       |                |                                |
| <li> [Fortlaufendem Export](../../defender-for-cloud/continuous-export.md)                                                                                                                 | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Workflowautomatisierung (Vorschauversion)](../../defender-for-cloud/workflow-automation.md)                                                                                                               | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Empfehlungsausnahmeregeln](../../defender-for-cloud/exempt-resource.md)                                                                                                      | Öffentliche Vorschau | Nicht verfügbar                  |
| <li> [Warnungsunterdrückungsregeln](../../defender-for-cloud/alerts-suppression-rules.md)                                                                                                    | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [E-Mail-Benachrichtigungen für Sicherheitswarnungen](../../defender-for-cloud/configure-email-notifications.md)                                                            | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Automatische Bereitstellung für Agents und Erweiterungen](../../defender-for-cloud/enable-data-collection.md)                                                                  | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Ressourcenbestand](../../defender-for-cloud/asset-inventory.md)                                                                                                                     | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Azure Monitor Workbooks-Berichte im Workbookkatalog von Microsoft Defender für Cloud](../../defender-for-cloud/custom-dashboards-azure-workbooks.md)                               | Allgemein verfügbar             | Allgemein verfügbar                             |
| **Azure Defender-Pläne und -Erweiterungen**                                                                                                                                          |                |                                |
| <li> [Microsoft Defender für Server](../../defender-for-cloud/defender-for-servers-introduction.md)                                                                                    | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Microsoft Defender für App Service](../../defender-for-cloud/defender-for-app-service-introduction.md)                                                                            | Allgemein verfügbar             | Nicht verfügbar                  |
| <li> [Microsoft Defender für DNS](../../defender-for-cloud/defender-for-dns-introduction.md)                                                                                            | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Microsoft Defender für Containerregistrierungen](../../defender-for-cloud/defender-for-container-registries-introduction.md) <sup>[1](#footnote1)</sup>                               | Allgemein verfügbar             | Allgemein verfügbar <sup>[2](#footnote2)</sup> |
| <li> [Microsoft Defender für Containerregistrierungen zum Überprüfen von Images in CI/CD-Workflows](../../defender-for-cloud/defender-for-container-registries-cicd.md) <sup>[3](#footnote3)</sup> | Öffentliche Vorschau | Nicht verfügbar                  |
| <li> [Microsoft Defender für Kubernetes](../../defender-for-cloud/defender-for-kubernetes-introduction.md) <sup>[4](#footnote4)</sup>                                                   | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Defender-Erweiterung für Kubernetes-Cluster mit Azure Arc-Unterstützung](../../defender-for-cloud/defender-for-kubernetes-azure-arc.md) <sup>[5](#footnote5)</sup>                           | Öffentliche Vorschau | Nicht verfügbar                  |
| <li> [Microsoft Defender für Azure SQL-Datenbank-Server](../../defender-for-cloud/defender-for-sql-introduction.md)                                                                     | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Microsoft Defender für SQL Server-Instanzen auf Computern](../../defender-for-cloud/defender-for-sql-introduction.md)                                                                        | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Microsoft Defender für relationale Open-Source-Datenbanken](../../defender-for-cloud/defender-for-databases-introduction.md)                                                         | Allgemein verfügbar             | Nicht verfügbar                  |
| <li> [Microsoft Defender für Key Vault](../../defender-for-cloud/defender-for-key-vault-introduction.md)                                                                                | Allgemein verfügbar             | Nicht verfügbar                  |
| <li> [Microsoft Defender für Resource Manager](../../defender-for-cloud/defender-for-resource-manager-introduction.md)                                                                  | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Microsoft Defender für Speicher](../../defender-for-cloud/defender-for-storage-introduction.md) <sup>[6](#footnote6)</sup>                                                         | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Bedrohungsschutz für Cosmos DB](../../defender-for-cloud/other-threat-protections.md#threat-protection-for-azure-cosmos-db-preview)                                              | Öffentliche Vorschau | Nicht verfügbar                  |
| <li> [Kubernetes-Workloadschutz](../../defender-for-cloud/kubernetes-workload-protections.md)                                                                                      | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Bidirektionale Synchronisierung von Warnungen mit Sentinel](../../sentinel/connect-azure-security-center.md)                                                                           | Öffentliche Vorschau | Nicht verfügbar                  |
| **Microsoft Defender für Serverfeatures** <sup>[7](#footnote7)</sup>                                                                                                               |                |                                |
| <li> [Just-in-Time-VM-Zugriff](../../defender-for-cloud/just-in-time-access-overview.md)                                                                                                 | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Dateiintegritätsüberwachung](../../defender-for-cloud/file-integrity-monitoring-overview.md)                                                                                 | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Adaptive Anwendungssteuerungen](../../defender-for-cloud/adaptive-application-controls.md)                                                                                  | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Adaptives Erhöhen des Netzwerkschutzes](../../defender-for-cloud/adaptive-network-hardening.md)                                                                               | Allgemein verfügbar             | Nicht verfügbar                  |
| <li> [Docker-Hosthärtung](../../defender-for-cloud/harden-docker-hosts.md)                                                                                                           | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Integrierte Sicherheitsrisikobewertung für Computer](../../defender-for-cloud/deploy-vulnerability-assessment-vm.md)                                                                 | Allgemein verfügbar             | Nicht verfügbar                  |
| <li> [Dashboard und Berichte für die Einhaltung gesetzlicher Bestimmungen](../../defender-for-cloud/regulatory-compliance-dashboard.md) <sup>[8](#footnote8)</sup>                                           | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Microsoft Defender für Endpunkt: Bereitstellung und integrierte Lizenz](../../defender-for-cloud/integration-defender-for-endpoint.md)                                                             | Allgemein verfügbar             | Allgemein verfügbar                             |
| <li> [Verbinden von AWS-Konten](../../defender-for-cloud/quickstart-onboard-aws.md)                                                                                                          | Allgemein verfügbar             | Nicht verfügbar                  |
| <li> [Verbinden von GCP-Konten](../../defender-for-cloud/quickstart-onboard-gcp.md)                                                                                                          | Allgemein verfügbar             | Nicht verfügbar                  |
|                                                                                                                                                                                      |                |                                |

<sup><a name="footnote1"></a>1</sup> Teilweise allgemeine Verfügbarkeit: Die Möglichkeit, bestimmte Ergebnisse von Sicherheitsrisikoüberprüfungen zu deaktivieren, befindet sich in der öffentlichen Vorschau.

<sup><a name="footnote2"></a>2</sup> Sicherheitsrisikoüberprüfungen von Containerregistrierungen in Azure Gov können nur mit der Funktion zur Überprüfung beim Pushen durchgeführt werden.

<sup><a name="footnote3"></a>3</sup> Erfordert Microsoft Defender für Containerregistrierungen.

<sup><a name="footnote4"></a>4</sup> Teilweise allgemeine Verfügbarkeit: Die Unterstützung für Azure Arc-fähige Cluster befindet sich in der öffentlichen Vorschauphase und ist in Azure Government nicht verfügbar.

<sup><a name="footnote5"></a>5</sup> Erfordert Microsoft Defender für Kubernetes.

<sup><a name="footnote6"></a>6</sup> Teilweise allgemeine Verfügbarkeit: Einige Warnungen zum Bedrohungsschutz von Microsoft Defender für Speicher befinden sich in der öffentlichen Vorschau.

<sup><a name="footnote7"></a>7</sup> Diese Features erfordern alle [Microsoft Defender für Server](../../defender-for-cloud/defender-for-servers-introduction.md).

<sup><a name="footnote8"></a>8</sup> Es gibt möglicherweise Unterschiede in den Standards, die für die einzelnen Cloudtypen angeboten werden.

<a name="azure-sentinel"></a>

## <a name="microsoft-sentinel"></a>Microsoft Sentinel

Microsoft Sentinel ist eine skalierbare, cloudnative SIEM-Lösung (Security Information & Event Management) und SOAR-Lösung (Security Orchestration Automated Response, Sicherheitsorchestrierung mit automatisierter Reaktion). Microsoft Sentinel bietet intelligente Sicherheits- und Bedrohungsanalysen für das ganze Unternehmen und stellt eine zentrale Lösung für die Warnungs- und Bedrohungserkennung, die proaktive Suche sowie die Reaktion auf Bedrohungen dar.

Weitere Informationen finden Sie in der Dokumentation zu [Microsoft Sentinel](../../sentinel/overview.md).

In den folgenden Tabellen wird die aktuelle Microsoft Sentinel-Featureverfügbarkeit in Azure und Azure Government gezeigt.

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
| - [Threat Intelligence – TAXII-Datenconnector](../../sentinel/understand-threat-intelligence.md)  | Allgemein verfügbar | Allgemein verfügbar |
| - [Threat Intelligence-Plattform-Datenconnector](../../sentinel/understand-threat-intelligence.md)  | Öffentliche Vorschau | Nicht verfügbar |
| - [Blatt „Threat Intelligence-Forschung“](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-threat-intelligence-menu-item-in-public-preview/ba-p/1646597)  | Allgemein verfügbar | Allgemein verfügbar |
| - [URL-Detonation](https://techcommunity.microsoft.com/t5/azure-sentinel/using-the-new-built-in-url-detonation-in-azure-sentinel/ba-p/996229) | Öffentliche Vorschau | Nicht verfügbar |
| - [Arbeitsmappe „Threat Intelligence“](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)  | Allgemein verfügbar | Allgemein verfügbar |
|**Erkennungsunterstützung** | | |
| - [Erkennung anomaler Zugriffe auf Windows-Dateifreigaben](../../sentinel/fusion.md)  | Öffentliche Vorschau | Nicht verfügbar |
| - [Erkennung anomaler RDP-Anmeldungen](../../sentinel/connect-windows-security-events.md#configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection)<br>Integrierte ML-Erkennung | Öffentliche Vorschau | Nicht verfügbar |
| - [Erkennung anomaler SSH-Anmeldungen](../../sentinel/connect-syslog.md#configure-the-syslog-connector-for-anomalous-ssh-login-detection)<br>Integrierte ML-Erkennung | Öffentliche Vorschau | Nicht verfügbar |
| **Azure-Dienstconnectors** |  |  |
| - [Azure-Aktivitätsprotokolle](../../sentinel/data-connectors-reference.md#azure-activity) | Allgemein verfügbar | Allgemein verfügbar |
| - [Azure Active Directory](../../sentinel/connect-azure-active-directory.md) | Allgemein verfügbar | Allgemein verfügbar |
| - [Azure ADIP](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection) | Allgemein verfügbar | Allgemein verfügbar |
| - [Azure DDoS Protection](../../sentinel/data-connectors-reference.md#azure-ddos-protection) | Allgemein verfügbar | Allgemein verfügbar |
| - [Microsoft Defender für Cloud](../../sentinel/connect-azure-security-center.md) | Allgemein verfügbar | Allgemein verfügbar |
| - [Microsoft Defender für IoT](../../sentinel/data-connectors-reference.md#microsoft-defender-for-iot) | Öffentliche Vorschau | Nicht verfügbar |
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

<sup><a name="footnote1"></a>1</sup> SSH- und RDP-Erkennungen werden für Sovereign Clouds nicht unterstützt, da die Databricks ML-Plattform nicht verfügbar ist.

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
| **[Microsoft Defender für Cloud-Apps](../../sentinel/data-connectors-reference.md#microsoft-defender-for-cloud-apps)** |  |  |
| - Office 365 GCC | Allgemein verfügbar | - |
| - Office 365 GCC High | - | Allgemein verfügbar |
| - Office 365 DoD | - | Allgemein verfügbar |
| **[Microsoft Defender für Cloud-Apps](../../sentinel/data-connectors-reference.md#microsoft-defender-for-cloud-apps)** <br>Schatten-IT-Protokolle |  |  |
| - Office 365 GCC | Öffentliche Vorschau | - |
| - Office 365 GCC High | - | Öffentliche Vorschau |
| - Office 365 DoD | - | Öffentliche Vorschau |
| **[Microsoft Defender für Cloud-Apps](../../sentinel/data-connectors-reference.md#microsoft-defender-for-cloud-apps)**                  <br>Alerts |  |  |
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

<a name="azure-defender-for-iot"></a>

## <a name="microsoft-defender-for-iot"></a>Microsoft Defender für IoT

Mit Microsoft Defender für IoT können Sie IoT-/OT-Innovationen mit umfassender Sicherheit für alle Ihre IoT-/OT-Geräte beschleunigen.Für Endbenutzerorganisationen bietet Microsoft Defender für IoT Sicherheit auf Netzwerkebene ohne Agents, die schnell bereitgestellt werden kann, mit verschiedenen Branchensystemen funktioniert und mit Microsoft Sentinel und anderen SOC-Tools interagiert. Die Bereitstellung kann lokal oder in mit Azure verbundenen Umgebungen erfolgen.Mit den Microsoft Defender für IoT-Sicherheits-Agents können IoT-Geräteentwickler die Sicherheit direkt in Ihre neuen IoT-Geräte und Azure IoT-Projekte einbinden. Der Micro-Agent ist mit flexiblen Bereitstellungsoptionen ausgestattet. Dazu zählen auch die Bereitstellung als binäres Paket oder Änderungen des Quellcodes. Der Micro-Agent ist zudem für IoT-Standardbetriebssysteme wie Linux und Azure RTOS verfügbar. Weitere Informationen finden Sie in der Dokumentation zu [Microsoft Defender für IoT](../../defender-for-iot/index.yml).

In der folgenden Tabelle wird die aktuelle Verfügbarkeit von Microsoft Defender für IoT-Features in Azure und Azure Government angezeigt.

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
| [Konfigurieren von Sentinel mit Microsoft Defender für IoT](../../defender-for-iot/how-to-configure-with-sentinel.md) | Öffentliche Vorschau | Öffentliche Vorschau |
| **Eigenständiger Micro-Agent für Linux** |  |  |
| [Binäre Installation des eigenständigen Agents](../../defender-for-iot/quickstart-standalone-agent-binary-installation.md) | Öffentliche Vorschau | Öffentliche Vorschau |

## <a name="azure-attestation"></a>Azure Attestation

Microsoft Azure Attestation ist eine einheitliche Lösung zur Remoteüberprüfung der Vertrauenswürdigkeit einer Plattform und der Integrität der darin ausgeführten Binärdateien. Der Dienst empfängt Beweise von der Plattform, überprüft diese anhand von Sicherheitsstandards, wertet sie anhand konfigurierbarer Richtlinien aus und erstellt ein Nachweistoken für anspruchsbasierte Anwendungen (z. B. vertrauende Seiten, Überwachungsbehörden). 

Azure Attestation ist derzeit in mehreren Regionen über öffentliche Azure-Clouds und Government-Clouds hinweg verfügbar. In Azure Government ist der Dienst als Vorschauversion für US Gov Virginia und US Gov Arizona verfügbar. 

Weitere Informationen finden Sie in der [öffentlichen Dokumentation](/azure/attestation/overview) zu Azure Attestation. 

| Funktion | Azure | Azure Government |
|--|--|--|
| [Portalumgebung](/azure/attestation/quickstart-portal) zum Ausführen von Vorgängen auf Steuerungsebene und Datenebene | Allgemein verfügbar | - |
| [PowerShell-Umgebung](/azure/attestation/quickstart-powershell) zum Ausführen von Vorgängen auf Steuerungsebene und Datenebene  | Allgemein verfügbar | Allgemein verfügbar |
| Erzwingen von TLS 1.2   | Allgemein verfügbar | Allgemein verfügbar |
| BCDR-Unterstützung   | Allgemein verfügbar | - |
| [Integration von Diensttags](/azure/virtual-network/service-tags-overview) | Allgemein verfügbar | Allgemein verfügbar |
| [Unveränderlicher Protokollspeicher](/azure/attestation/audit-logs) | Allgemein verfügbar | Allgemein verfügbar |
| Netzwerkisolation mit privatem Link | Öffentliche Vorschau | - |
| [FedRamp High-Zertifizierung](/azure/azure-government/compliance/azure-services-in-fedramp-auditscope) | Allgemein verfügbar | - |
| Kunden-Lockbox | Allgemein verfügbar | - |

## <a name="next-steps"></a>Nächste Schritte

- Grundlegendes zum Modell [gemeinsamer Verantwortung](shared-responsibility.md) sowie welche Sicherheitsaufgaben vom Cloudanbieter und welche Aufgaben von Ihnen verarbeitet werden.
- Grundlegendes zu den Funktionen der [Azure Government Cloud](../../azure-government/documentation-government-welcome.md) und dem vertrauenswürdigen Design und der Sicherheit, die zur Unterstützung der Compliance für Bundes-, Staats- und lokale Behörden und deren Partner verwendet werden.
- Grundlegendes zum [Office 365 Government-Plan](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/office-365-us-government#about-office-365-government-environments).
- Grundlegendes zur [Compliance in Azure](../../compliance/index.yml) für rechtliche und gesetzliche Standards.
