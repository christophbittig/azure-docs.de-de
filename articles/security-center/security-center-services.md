---
title: Azure Security Center-Funktionen nach Betriebssystem, Computertyp und Cloud
description: Erfahren Sie mehr über die Verfügbarkeit von Azure Security Center-Features je nach Betriebssystem, Computertyp und Cloudbereitstellung.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 08/18/2021
ms.custom: references_regions
ms.author: memildin
ms.openlocfilehash: 4bb46ed8146067c44f2d4a602f3c8b1831340d7a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003532"
---
# <a name="feature-coverage-for-machines"></a>Funktionsabdeckung für Computer

Die beiden folgenden Registerkarten enthalten die Azure Security Center-Features, die für Windows- und Linux-Computer verfügbar sind.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Unterstützte Funktionen für virtuelle Computer und Server <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows-Computer**](#tab/features-windows)

|**Feature**|**Dokumentation zu virtuellen Computern**|**Azure Virtual Machine Scale Sets**|**Computer mit Azure Arc-Unterstützung**|**Azure Defender erforderlich**
|----|:----:|:----:|:----:|:----:|
|[Integration in Microsoft Defender für Endpunkt](security-center-wdatp.md)|✔</br>(für unterstützte Versionen)|✔</br>(für unterstützte Versionen)|✔|Ja|
|[Virtual Machine-Verhaltensanalysen (und Sicherheitswarnungen)](alerts-reference.md)|✔|✔|✔|Ja|
|[Dateilose Sicherheitswarnungen](alerts-reference.md#alerts-windows)|✔|✔|✔|Ja|
|[Netzwerkbasierte Sicherheitswarnungen](other-threat-protections.md#network-layer)|✔|✔|-|Ja|
|[Just-in-Time-VM-Zugriff](security-center-just-in-time.md)|✔|-|-|Ja|
|[Integrierte Lösung zur Überprüfung auf Sicherheitsrisiken von Qualys](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Ja|
|[Dateiintegritätsüberwachung](security-center-file-integrity-monitoring.md)|✔|✔|✔|Ja|
|[Adaptive Anwendungssteuerungen](security-center-adaptive-application.md)|✔|-|✔|Ja|
|[Netzwerkübersicht](security-center-network-recommendations.md#network-map)|✔|✔|-|Ja|
|[Adaptives Erhöhen des Netzwerkschutzes](security-center-adaptive-network-hardening.md)|✔|-|-|Ja|
|[Dashboard und Berichte für die Einhaltung gesetzlicher Bestimmungen](security-center-compliance-dashboard.md)|✔|✔|✔|Ja|
|[Docker-Hosthärtung](./harden-docker-hosts.md)|-|-|-|Ja|
|Fehlende Bewertung von BS-Patches|✔|✔|✔|Azure: Nein<br><br>Mit Azure Arc-Unterstützung: Ja|
|Bewertung von Sicherheitsfehlkonfigurationen|✔|✔|✔|Azure: Nein<br><br>Mit Azure Arc-Unterstützung: Ja|
|[Endpoint Protection-Bewertung](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: Nein<br><br>Mit Azure Arc-Unterstützung: Ja|
|Bewertung der Datenträgerverschlüsselung|✔</br>(für [unterstützte Szenarien](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Nein|
|Sicherheitsrisikobewertung durch Drittanbieter|✔|-|✔|Nein|
|[Netzwerksicherheitsbewertung](security-center-network-recommendations.md)|✔|✔|-|Nein|
||||||

### <a name="linux-machines"></a>[**Linux-Computer**](#tab/features-linux)

| **Feature**                                                                                                               | **Dokumentation zu virtuellen Computern**                                                                                      | **Azure Virtual Machine Scale Sets** | **Computer mit Azure Arc-Unterstützung** | **Azure Defender erforderlich**       |
|---------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------:|:------------------------------------:|:------------------------------:|:---------------------------------:|
| [Integration in Microsoft Defender für Endpunkt](security-center-wdatp.md)                                                   | ✔                                                                                                               | -                                    | ✔                              | Ja                              |
| [Virtual Machine-Verhaltensanalysen (und Sicherheitswarnungen)](./azure-defender.md)                                         | ✔</br>(für unterstützte Versionen)                                                                                  | ✔</br>(für unterstützte Versionen)        | ✔                             | Ja                               |
| [Dateilose Sicherheitswarnungen](alerts-reference.md#alerts-windows)                                                            | -                                                                                                               | -                                    | -                              | Ja                               |
| [Netzwerkbasierte Sicherheitswarnungen](other-threat-protections.md#network-layer)                                                | ✔                                                                                                              | ✔                                    | -                              | Ja                               |
| [Just-in-Time-VM-Zugriff](security-center-just-in-time.md)                                                                 | ✔                                                                                                              | -                                    | -                              | Ja                               |
| [Integrierte Lösung zur Überprüfung auf Sicherheitsrisiken von Qualys](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner) | ✔                                                                                                              | -                                    | ✔                             | Ja                               |
| [Dateiintegritätsüberwachung](security-center-file-integrity-monitoring.md)                                                 | ✔                                                                                                              | ✔                                    | ✔                             | Ja                               |
| [Adaptive Anwendungssteuerungen](security-center-adaptive-application.md)                                                  | ✔                                                                                                              | -                                    | ✔                             | Ja                               |
| [Netzwerkübersicht](security-center-network-recommendations.md#network-map)                                                     | ✔                                                                                                              | ✔                                    | -                              | Ja                               |
| [Adaptives Erhöhen des Netzwerkschutzes](security-center-adaptive-network-hardening.md)                                               | ✔                                                                                                              | -                                    | -                              | Ja                               |
| [Dashboard und Berichte für die Einhaltung gesetzlicher Bestimmungen](security-center-compliance-dashboard.md)                                      | ✔                                                                                                              | ✔                                    | ✔                             | Ja                               |
| [Docker-Hosthärtung](./harden-docker-hosts.md)                                                                         | ✔                                                                                                              | ✔                                    | ✔                             | Ja                               |
| Fehlende Bewertung von BS-Patches                                                                                             | ✔                                                                                                              | ✔                                    | ✔                             | Azure: Nein<br><br>Mit Azure Arc-Unterstützung: Ja |
| Bewertung von Sicherheitsfehlkonfigurationen                                                                                     | ✔                                                                                                              | ✔                                    | ✔                             | Azure: Nein<br><br>Mit Azure Arc-Unterstützung: Ja |
| [Endpoint Protection-Bewertung](security-center-services.md#supported-endpoint-protection-solutions-)                    | -                                                                                                               | -                                    | -                              | Nein                                |
| Bewertung der Datenträgerverschlüsselung                                                                                                | ✔</br>(für [unterstützte Szenarien](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)) | ✔                                    | -                              | Nein                                |
| Sicherheitsrisikobewertung durch Drittanbieter                                                                                      | ✔                                                                                                              | -                                    | ✔                             | Nein                                |
| [Netzwerksicherheitsbewertung](security-center-network-recommendations.md)                                                 | ✔                                                                                                              | ✔                                    | -                              | Nein                                |
||||||

--- 


> [!TIP]
>Wenn Sie mit Features experimentieren möchten, die nur in Azure Defender verfügbar sind, können Sie sich für eine 30-tägige Testversion registrieren. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Unterstützte Endpoint Protection-Lösungen <a name="endpoint-supported"></a>

In der folgenden Tabelle finden Sie eine Matrix zu folgenden Fragen:

 - Ob Sie mit Azure Security Center jede Lösung für sich installieren können.
 - Welche Endpunktschutz-Lösungen Security Center erkennen kann. Wenn eine Endpoint Protection-Lösungen aus dieser Liste ermittelt wird, empfiehlt Security Center nicht deren Installation.

Informationen darüber, wann Empfehlungen für die einzelnen Schutzfunktionen generiert werden, finden Sie unter [Endpoint Protection: Bewertung und Empfehlungen](security-center-endpoint-protection.md).

| Projektmappe                                                  | Unterstützte Plattformen                                    | Security Center-Installation | Security Center-Ermittlung |
|-----------------------------------------------------------|--------------------------------------------------------|------------------------------|---------------------------|
| Microsoft Defender Antivirus                              | Windows Server 2016 oder höher                           | Nein (integriert im Betriebssystem)           | Ja                       |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2                                 | Per Erweiterung                | Ja                       |
| Trend Micro – Deep Security                               | Windows Server (alle)                                   | Nein                           | Ja                       |
| Symantec v12.1.1100+                                      | Windows Server (alle)                                   | Nein                           | Ja                       |
| McAfee v10+                                               | Windows Server (alle)                                   | Nein                           | Ja                       |
| McAfee v10+                                               | Linux (Vorschauversion)                                        | Nein                           | Ja                       |
| Microsoft Defender für Endpoint für Linux<sup>[1](#footnote1)</sup>  | Linux (Vorschauversion)                                        | Per Erweiterung                | Nein                        |  
| Sophos V9+                                                | Linux (Vorschauversion)                                        | Nein                           | Ja                       |
|                                                           |                                                        |                              |                           |

<sup><a name="footnote1" /></a>1</sup> Microsoft Defender für Endpoint auf dem Linux-Computer reicht nicht aus: Der Computer wird nur als fehlerfrei angezeigt, wenn die AV-Komponente aktiv ist.
Standardmäßig ist die AV-Komponente **deaktiviert**, um Konflikte mit anderer AV-Software zu vermeiden.




## <a name="feature-support-in-government-and-sovereign-clouds"></a>Funktionsunterstützung in Government- und Sovereign Clouds

| Funktion/Dienst                                                                                                                                           | Azure          | Azure Government               | Azure China 21Vianet   |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|--------------------------------|---------------|
| **Security Center: kostenlose Funktionen**                                                                                                                         |                |                                |               |
| - [Fortlaufender Export](./continuous-export.md)                                                                                                             | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Workflowautomatisierung](./continuous-export.md)                                                                                                           | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Empfehlungsausnahmeregeln](./exempt-resource.md)                                                                                                  | Öffentliche Vorschau | Nicht verfügbar                  | Nicht verfügbar. |
| - [Warnungsunterdrückungsregeln](./alerts-suppression-rules.md)                                                                                                | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [E-Mail-Benachrichtigungen für Sicherheitswarnungen](./security-center-provide-security-contact-details.md)                                                        | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Automatische Bereitstellung für Agents und Erweiterungen](./security-center-enable-data-collection.md)                                                              | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Ressourcenbestand](./asset-inventory.md)                                                                                                                 | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Azure Monitor-Arbeitsmappenberichte im Azure Security Center-Arbeitsmappenkatalog](./custom-dashboards-azure-workbooks.md)                                  | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| **Azure Defender-Pläne und Erweiterungen**                                                                                                                   |                |                                |               |
| - [Azure Defender für Server](./defender-for-servers-introduction.md)                                                                                    | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Azure Defender für App Service](./defender-for-app-service-introduction.md)                                                                            | Allgemein verfügbar             | Nicht verfügbar                  | Nicht verfügbar. |
| - [Azure Defender für DNS](./defender-for-dns-introduction.md)                                                                                            | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Azure Defender für Containerregistrierungen](./defender-for-container-registries-introduction.md) <sup>[1](#footnote1)</sup>                               | Allgemein verfügbar             | Allgemein verfügbar <sup>[2](#footnote2)</sup> | Allgemein verfügbar <sup>[2](#footnote2)</sup> |
| - [Azure Defender für Containerregistrierungen zum Überprüfen von Images in CI/CD-Workflows](./defender-for-container-registries-cicd.md) <sup>[3](#footnote3)</sup> | Öffentliche Vorschau | Nicht verfügbar                  | Nicht verfügbar. |
| - [Azure Defender für Kubernetes](./defender-for-kubernetes-introduction.md) <sup>[4](#footnote4)</sup>                                                   | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Azure Defender-Erweiterung für Azure Arc-fähige Kubernetes-Cluster ](./defender-for-kubernetes-azure-arc.md) <sup>[5](#footnote5)</sup>                 | Öffentliche Vorschau | Nicht verfügbar                  | Nicht verfügbar. |
| - [Azure Defender für Azure SQL-Datenbank-Server](./defender-for-sql-introduction.md)                                                                     | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar <sup>[9](#footnote9)</sup> |
| - [Azure Defender für SQL-Server auf Computern](./defender-for-sql-introduction.md)                                                                        | Allgemein verfügbar             | Allgemein verfügbar                             | Nicht verfügbar |
| - [Azure Defender für relationale Open-Source-Datenbanken](./defender-for-databases-introduction.md)                                                         | Allgemein verfügbar             | Nicht verfügbar                  | Nicht verfügbar. |
| - [Azure Defender für Key Vault](./defender-for-key-vault-introduction.md)                                                                                | Allgemein verfügbar             | Nicht verfügbar                  | Nicht verfügbar. |
| - [Azure Defender für Resource Manager](./defender-for-resource-manager-introduction.md)                                                                  | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Azure Defender für Storage](./defender-for-storage-introduction.md) <sup>[6](#footnote6)</sup>                                                         | Allgemein verfügbar             | Allgemein verfügbar                             | Nicht verfügbar |
| - [Bedrohungsschutz für Cosmos DB](./other-threat-protections.md#threat-protection-for-azure-cosmos-db-preview)                                          | Öffentliche Vorschau | Nicht verfügbar                  | Nicht verfügbar. |
| - [Kubernetes-Workloadschutz](./kubernetes-workload-protections.md)                                                                                  | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Bidirektionale Synchronisierung von Warnungen mit Sentinel](../sentinel/connect-azure-security-center.md)                                                      | Öffentliche Vorschau | Nicht verfügbar                  | Nicht verfügbar. |
| **Azure Defender für Serverfunktionen** <sup>[7](#footnote7)</sup>                                                                                        |                |                                |               |
| - [Just-In-Time-VM-Zugriff](./security-center-just-in-time.md)                                                                                             | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Dateiintegritätsüberwachung](./security-center-file-integrity-monitoring.md)                                                                             | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Adaptive Anwendungssteuerungen](./security-center-adaptive-application.md)                                                                              | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Adaptives Erhöhen des Netzwerkschutzes](./security-center-adaptive-network-hardening.md)                                                                           | Allgemein verfügbar             | Nicht verfügbar                  | Nicht verfügbar. |
| - [Docker-Hosthärtung](./harden-docker-hosts.md)                                                                                                       | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Integrierte Lösung zur Überprüfung auf Sicherheitsrisiken von Qualys](./deploy-vulnerability-assessment-vm.md)                                                             | Allgemein verfügbar             | Nicht verfügbar                  | Nicht verfügbar. |
| - [Dashboard und Berichte für die Einhaltung gesetzlicher Bestimmungen](./security-center-compliance-dashboard.md) <sup>[8](#footnote8)</sup>                                       | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Microsoft Defender für Endpunkt: Bereitstellung und integrierte Lizenz](./security-center-wdatp.md)                                                         | Allgemein verfügbar             | Allgemein verfügbar                             | Nicht verfügbar |
| - [Verbinden von AWS-Konten](./quickstart-onboard-aws.md)                                                                                                      | Allgemein verfügbar             | Nicht verfügbar                  | Nicht verfügbar. |
| - [Verbinden von GCP-Konten](./quickstart-onboard-gcp.md)                                                                                                      | Allgemein verfügbar             | Nicht verfügbar                  | Nicht verfügbar. |
|                                                                                                                                                           |                |                                |               |

<sup><a name="footnote1" /></a>1</sup> Teilweise allgemeine Verfügbarkeit: Die Möglichkeit, bestimmte Ergebnisse von Sicherheitsrisikoüberprüfungen zu deaktivieren, befindet sich in der öffentlichen Vorschau.

<sup><a name="footnote2" /></a>2</sup> Sicherheitsrisikoüberprüfungen von Containerregistrierungen in der Azure Government-Cloud können nur mit der Funktion zur Überprüfung beim Pushen durchgeführt werden.

<sup><a name="footnote3" /></a>3</sup> Erfordert Azure Defender für Containerregistrierungen

<sup><a name="footnote4" /></a>4</sup> Teilweise allgemeine Verfügbarkeit: Die Unterstützung für Azure Arc-fähige Cluster befindet sich in der öffentlichen Vorschauphase und ist in Azure Government nicht verfügbar.

<sup><a name="footnote5" /></a>5</sup> Erfordert Azure Defender für Kubernetes

<sup><a name="footnote6" /></a>6</sup> Teilweise allgemeine Verfügbarkeit: Einige Warnungen zum Bedrohungsschutz von Azure Defender für Storage befinden sich in der öffentlichen Vorschau.

<sup><a name="footnote7" /></a>7</sup> Diese Funktionen erfordern [Azure Defender für Server](./defender-for-servers-introduction.md).

<sup><a name="footnote8" /></a>8</sup> Es gibt möglicherweise Unterschiede in den Standards, die für die einzelnen Cloudtypen angeboten werden.
 
<sup><a name="footnote9" /></a>9</sup> Teilweise allgemeine Verfügbarkeit: Teilmenge von Warnungen und Sicherheitsrisikobewertungen für SQL-Server. Verhaltensbedingte Bedrohungsschutzmaßnahmen sind nicht verfügbar.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Datenerfassung in Security Center mit dem Log Analytics-Agent](security-center-enable-data-collection.md).
- Erfahren Sie, wie [Daten von Security Center verwaltet und geschützt werden](security-center-data-security.md).
- Informieren Sie sich über die [Plattformen, die Security Center unterstützen](security-center-os-coverage.md).
