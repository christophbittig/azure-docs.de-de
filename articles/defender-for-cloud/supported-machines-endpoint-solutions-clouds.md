---
title: Features von Microsoft Defender für Cloud gemäß Betriebssystem, Computertyp und Cloud
description: Erfahren Sie mehr über die Verfügbarkeit von Defender für Cloud-Features je nach Betriebssystem, Computertyp und Cloudbereitstellung.
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: overview
ms.date: 11/09/2021
ms.custom: references_regions
ms.author: memildin
ms.openlocfilehash: b3a12bdbea997494448a3ff71b947399b28c070a
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526735"
---
# <a name="feature-coverage-for-machines"></a>Funktionsabdeckung für Computer

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Die beiden folgenden **Registerkarten** enthalten die Features von Defender für Cloud, die für Windows- und Linux-Computer verfügbar sind.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Unterstützte Funktionen für virtuelle Computer und Server <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows-Computer**](#tab/features-windows)

|**Feature**|**Dokumentation zu virtuellen Computern**|**Azure Virtual Machine Scale Sets**|**Computer mit Azure Arc-Unterstützung**|**Erweiterte Sicherheitsfeatures erforderlich**
|----|:----:|:----:|:----:|:----:|
|[Integration in Microsoft Defender für Endpunkt](integration-defender-for-endpoint.md)|✔</br>(für unterstützte Versionen)|✔</br>(für unterstützte Versionen)|✔|Ja|
|[Virtual Machine-Verhaltensanalysen (und Sicherheitswarnungen)](alerts-reference.md)|✔|✔|✔|Ja|
|[Dateilose Sicherheitswarnungen](alerts-reference.md#alerts-windows)|✔|✔|✔|Ja|
|[Netzwerkbasierte Sicherheitswarnungen](other-threat-protections.md#network-layer)|✔|✔|-|Ja|
|[Just-in-Time-VM-Zugriff](just-in-time-access-usage.md)|✔|-|-|Ja|
|[Integrierte Lösung zur Überprüfung auf Sicherheitsrisiken von Qualys](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Ja|
|[Dateiintegritätsüberwachung](file-integrity-monitoring-overview.md)|✔|✔|✔|Ja|
|[Adaptive Anwendungssteuerungen](adaptive-application-controls.md)|✔|-|✔|Ja|
|[Netzwerkübersicht](protect-network-resources.md#network-map)|✔|✔|-|Ja|
|[Adaptives Erhöhen des Netzwerkschutzes](adaptive-network-hardening.md)|✔|-|-|Ja|
|[Dashboard und Berichte für die Einhaltung gesetzlicher Bestimmungen](regulatory-compliance-dashboard.md)|✔|✔|✔|Ja|
|[Docker-Hosthärtung](./harden-docker-hosts.md)|-|-|-|Ja|
|Fehlende Bewertung von BS-Patches|✔|✔|✔|Azure: Nein<br><br>Mit Azure Arc-Unterstützung: Ja|
|Bewertung von Sicherheitsfehlkonfigurationen|✔|✔|✔|Azure: Nein<br><br>Mit Azure Arc-Unterstützung: Ja|
|[Endpoint Protection-Bewertung](supported-machines-endpoint-solutions-clouds.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: Nein<br><br>Mit Azure Arc-Unterstützung: Ja|
|Bewertung der Datenträgerverschlüsselung|✔</br>(für [unterstützte Szenarien](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Nein|
|Sicherheitsrisikobewertung durch Drittanbieter|✔|-|✔|Nein|
|[Netzwerksicherheitsbewertung](protect-network-resources.md)|✔|✔|-|Nein|
||||||

### <a name="linux-machines"></a>[**Linux-Computer**](#tab/features-linux)

| **Feature**                                                                                                               | **Dokumentation zu virtuellen Computern**                                                                                      | **Azure Virtual Machine Scale Sets** | **Computer mit Azure Arc-Unterstützung** | **Erweiterte Sicherheitsfeatures erforderlich**       |
|---------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------:|:------------------------------------:|:------------------------------:|:---------------------------------:|
| [Integration in Microsoft Defender für Endpunkt](integration-defender-for-endpoint.md)                                                   | ✔                                                                                                               | -                                    | ✔                              | Ja                              |
| [Virtual Machine-Verhaltensanalysen (und Sicherheitswarnungen)](./azure-defender.md)                                         | ✔</br>(für unterstützte Versionen)                                                                                  | ✔</br>(für unterstützte Versionen)        | ✔                             | Ja                               |
| [Dateilose Sicherheitswarnungen](alerts-reference.md#alerts-windows)                                                            | -                                                                                                               | -                                    | -                              | Ja                               |
| [Netzwerkbasierte Sicherheitswarnungen](other-threat-protections.md#network-layer)                                                | ✔                                                                                                              | ✔                                    | -                              | Ja                               |
| [Just-in-Time-VM-Zugriff](just-in-time-access-usage.md)                                                                 | ✔                                                                                                              | -                                    | -                              | Ja                               |
| [Integrierte Lösung zur Überprüfung auf Sicherheitsrisiken von Qualys](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner) | ✔                                                                                                              | -                                    | ✔                             | Ja                               |
| [Dateiintegritätsüberwachung](file-integrity-monitoring-overview.md)                                                 | ✔                                                                                                              | ✔                                    | ✔                             | Ja                               |
| [Adaptive Anwendungssteuerungen](adaptive-application-controls.md)                                                  | ✔                                                                                                              | -                                    | ✔                             | Ja                               |
| [Netzwerkübersicht](protect-network-resources.md#network-map)                                                     | ✔                                                                                                              | ✔                                    | -                              | Ja                               |
| [Adaptives Erhöhen des Netzwerkschutzes](adaptive-network-hardening.md)                                               | ✔                                                                                                              | -                                    | -                              | Ja                               |
| [Dashboard und Berichte für die Einhaltung gesetzlicher Bestimmungen](regulatory-compliance-dashboard.md)                                      | ✔                                                                                                              | ✔                                    | ✔                             | Ja                               |
| [Docker-Hosthärtung](./harden-docker-hosts.md)                                                                         | ✔                                                                                                              | ✔                                    | ✔                             | Ja                               |
| Fehlende Bewertung von BS-Patches                                                                                             | ✔                                                                                                              | ✔                                    | ✔                             | Azure: Nein<br><br>Mit Azure Arc-Unterstützung: Ja |
| Bewertung von Sicherheitsfehlkonfigurationen                                                                                     | ✔                                                                                                              | ✔                                    | ✔                             | Azure: Nein<br><br>Mit Azure Arc-Unterstützung: Ja |
| [Endpoint Protection-Bewertung](supported-machines-endpoint-solutions-clouds.md#supported-endpoint-protection-solutions-)                    | -                                                                                                               | -                                    | -                              | Nein                                |
| Bewertung der Datenträgerverschlüsselung                                                                                                | ✔</br>(für [unterstützte Szenarien](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)) | ✔                                    | -                              | Nein                                |
| Sicherheitsrisikobewertung durch Drittanbieter                                                                                      | ✔                                                                                                              | -                                    | ✔                             | Nein                                |
| [Netzwerksicherheitsbewertung](protect-network-resources.md)                                                 | ✔                                                                                                              | ✔                                    | -                              | Nein                                |
||||||

--- 

> [!TIP]
>Um mit Features zu experimentieren, die nur mit aktivierten erweiterten Sicherheitsfeatures verfügbar sind, können Sie sich für eine 30-tägige Testversion registrieren. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Unterstützte Endpoint Protection-Lösungen <a name="endpoint-supported"></a>

Die folgende Tabelle enthält eine Matrix der unterstützten Endpunktschutzlösungen und gibt an, ob Sie Microsoft Defender für Cloud verwenden können, um die einzelnen Lösungen für Sie zu installieren.

Informationen darüber, wann Empfehlungen für die einzelnen Lösungen generiert werden, finden Sie unter [Endpoint Protection: Bewertung und Empfehlungen](endpoint-protection-recommendations-technical.md).

| Projektmappe                                                            | Unterstützte Plattformen          | Installation von Defender für Cloud |
|---------------------------------------------------------------------|------------------------------|---------------------------------|
| Microsoft Defender Antivirus                                        | Windows Server 2016 oder höher | Nein (integriert im Betriebssystem)              |
| System Center Endpoint Protection (Microsoft Antimalware)           | Windows Server 2012 R2       | Per Erweiterung                   |
| Trend Micro – Deep Security                                         | Windows Server (alle)         | Nein                              |
| Symantec v12.1.1100+                                                | Windows Server (alle)         | Nein                              |
| McAfee v10+                                                         | Windows Server (alle)         | Nein                              |
| McAfee v10+                                                         | Linux (Vorschauversion)              | Nein                              |
| Microsoft Defender für Endpoint für Linux<sup>[1](#footnote1)</sup> | Linux (Vorschauversion)              | Per Erweiterung                   |
| Sophos V9+                                                          | Linux (Vorschauversion)              | Nein                              |
|                                                                     |                              |                                 |

<sup><a name="footnote1"></a>1</sup> Es reicht nicht aus, Microsoft Defender für Endpoint auf dem Linux-Computer zu verwenden. Der Computer wird nur als fehlerfrei angezeigt, wenn das Feature für die Always On-Überprüfung (auch als Echtzeitschutz [Real-Time Protection, RTP] bezeichnet) aktiv ist. Standardmäßig ist das RTP-Feature **deaktiviert**, um Konflikte mit anderer AV-Software zu vermeiden.




## <a name="feature-support-in-government-and-national-clouds"></a>Funktionsunterstützung in Government-Clouds und nationalen Clouds

| Funktion/Dienst                                                                                                                                           | Azure          | Azure Government               | Azure China 21Vianet   |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|--------------------------------|---------------|
| **Features von Defender für Cloud Free**                                                                                                                         |                |                                |               |
| - [Fortlaufender Export](./continuous-export.md)                                                                                                             | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Workflowautomatisierung](./workflow-automation.md)                                                                                                           | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Empfehlungsausnahmeregeln](./exempt-resource.md)                                                                                                  | Öffentliche Vorschau | Nicht verfügbar                  | Nicht verfügbar. |
| - [Warnungsunterdrückungsregeln](./alerts-suppression-rules.md)                                                                                                | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [E-Mail-Benachrichtigungen für Sicherheitswarnungen](./configure-email-notifications.md)                                                        | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Automatische Bereitstellung für Agents und Erweiterungen](./enable-data-collection.md)                                                              | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Ressourcenbestand](./asset-inventory.md)                                                                                                                 | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Azure Monitor Workbooks-Berichte im Workbookkatalog von Microsoft Defender für Cloud](./custom-dashboards-azure-workbooks.md)                                  | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| **Azure Defender-Pläne und -Erweiterungen**                                                                                                                   |                |                                |               |
| - [Microsoft Defender für Server](./defender-for-servers-introduction.md)                                                                                    | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Microsoft Defender für App Service](./defender-for-app-service-introduction.md)                                                                            | Allgemein verfügbar             | Nicht verfügbar                  | Nicht verfügbar. |
| - [Microsoft Defender für DNS](./defender-for-dns-introduction.md)                                                                                            | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Microsoft Defender für Containerregistrierungen](./defender-for-container-registries-introduction.md) <sup>[1](#footnote1)</sup>                               | Allgemein verfügbar             | Allgemein verfügbar <sup>[2](#footnote2)</sup> | Allgemein verfügbar <sup>[2](#footnote2)</sup> |
| - [Microsoft Defender für Containerregistrierungen zum Überprüfen von Images in CI/CD-Workflows](./defender-for-container-registries-cicd.md) <sup>[3](#footnote3)</sup> | Öffentliche Vorschau | Nicht verfügbar                  | Nicht verfügbar. |
| - [Microsoft Defender für Kubernetes](./defender-for-kubernetes-introduction.md) <sup>[4](#footnote4)</sup>                                                   | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Defender-Erweiterung für Azure Arc-fähige Kubernetes-Cluster ](./defender-for-kubernetes-azure-arc.md) <sup>[5](#footnote5)</sup>                 | Öffentliche Vorschau | Nicht verfügbar                  | Nicht verfügbar. |
| - [Microsoft Defender für Azure SQL-Datenbank-Server](./defender-for-sql-introduction.md)                                                                     | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar <sup>[9](#footnote9)</sup> |
| - [Microsoft Defender für SQL Server-Instanzen auf Computern](./defender-for-sql-introduction.md)                                                                        | Allgemein verfügbar             | Allgemein verfügbar                             | Nicht verfügbar |
| - [Microsoft Defender für relationale Open-Source-Datenbanken](./defender-for-databases-introduction.md)                                                         | Allgemein verfügbar             | Nicht verfügbar                  | Nicht verfügbar. |
| - [Microsoft Defender für Key Vault](./defender-for-key-vault-introduction.md)                                                                                | Allgemein verfügbar             | Nicht verfügbar                  | Nicht verfügbar. |
| - [Microsoft Defender für Resource Manager](./defender-for-resource-manager-introduction.md)                                                                  | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Microsoft Defender für Speicher](./defender-for-storage-introduction.md) <sup>[6](#footnote6)</sup>                                                         | Allgemein verfügbar             | Allgemein verfügbar                             | Nicht verfügbar |
| - [Bedrohungsschutz für Cosmos DB](./other-threat-protections.md#threat-protection-for-azure-cosmos-db-preview)                                          | Öffentliche Vorschau | Nicht verfügbar                  | Nicht verfügbar. |
| - [Kubernetes-Workloadschutz](./kubernetes-workload-protections.md)                                                                                  | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Bidirektionale Synchronisierung von Warnungen mit Sentinel](../sentinel/connect-azure-security-center.md)                                                      | Öffentliche Vorschau | Nicht verfügbar                  | Nicht verfügbar. |
| **Microsoft Defender für Serverfeatures** <sup>[7](#footnote7)</sup>                                                                                        |                |                                |               |
| - [Just-In-Time-VM-Zugriff](./just-in-time-access-usage.md)                                                                                             | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Dateiintegritätsüberwachung](./file-integrity-monitoring-overview.md)                                                                             | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Adaptive Anwendungssteuerungen](./adaptive-application-controls.md)                                                                              | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Adaptives Erhöhen des Netzwerkschutzes](./adaptive-network-hardening.md)                                                                           | Allgemein verfügbar             | Nicht verfügbar                  | Nicht verfügbar. |
| - [Docker-Hosthärtung](./harden-docker-hosts.md)                                                                                                       | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Integrierte Lösung zur Überprüfung auf Sicherheitsrisiken von Qualys](./deploy-vulnerability-assessment-vm.md)                                                             | Allgemein verfügbar             | Nicht verfügbar                  | Nicht verfügbar. |
| - [Dashboard und Berichte für die Einhaltung gesetzlicher Bestimmungen](./regulatory-compliance-dashboard.md) <sup>[8](#footnote8)</sup>                                       | Allgemein verfügbar             | Allgemein verfügbar                             | Allgemein verfügbar            |
| - [Microsoft Defender für Endpunkt: Bereitstellung und integrierte Lizenz](./integration-defender-for-endpoint.md)                                                         | Allgemein verfügbar             | Allgemein verfügbar                             | Nicht verfügbar |
| - [Verbinden von AWS-Konten](./quickstart-onboard-aws.md)                                                                                                      | Allgemein verfügbar             | Nicht verfügbar                  | Nicht verfügbar. |
| - [Verbinden von GCP-Konten](./quickstart-onboard-gcp.md)                                                                                                      | Allgemein verfügbar             | Nicht verfügbar                  | Nicht verfügbar. |
|                                                                                                                                                           |                |                                |               |

<sup><a name="footnote1"></a>1</sup> Teilweise allgemeine Verfügbarkeit: Die Möglichkeit, bestimmte Ergebnisse von Sicherheitsrisikoüberprüfungen zu deaktivieren, befindet sich in der öffentlichen Vorschau.

<sup><a name="footnote2"></a>2</sup> Sicherheitsrisikoüberprüfungen von Containerregistrierungen in der Azure Government-Cloud können nur mit der Funktion zur Überprüfung beim Pushen durchgeführt werden.

<sup><a name="footnote3"></a>3</sup> Erfordert Microsoft Defender für Containerregistrierungen.

<sup><a name="footnote4"></a>4</sup> Teilweise allgemeine Verfügbarkeit: Die Unterstützung für Azure Arc-fähige Cluster befindet sich in der öffentlichen Vorschauphase und ist in Azure Government nicht verfügbar.

<sup><a name="footnote5"></a>5</sup> Erfordert Microsoft Defender für Kubernetes.

<sup><a name="footnote6"></a>6</sup> Teilweise allgemeine Verfügbarkeit: Einige Warnungen zum Bedrohungsschutz von Microsoft Defender für Speicher befinden sich in der öffentlichen Vorschau.

<sup><a name="footnote7"></a>7</sup> Diese Features erfordern alle [Microsoft Defender für Server](./defender-for-servers-introduction.md).

<sup><a name="footnote8"></a>8</sup> Es gibt möglicherweise Unterschiede in den Standards, die für die einzelnen Cloudtypen angeboten werden.
 
<sup><a name="footnote9"></a>9</sup> Teilweise allgemeine Verfügbarkeit: Teilmenge von Warnungen und Sicherheitsrisikobewertungen für SQL-Server. Verhaltensbedingte Bedrohungsschutzmaßnahmen sind nicht verfügbar.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie [Defender für Cloud mit dem Log Analytics-Agent Daten sammelt](enable-data-collection.md).
- Erfahren Sie, wie [Defender für Cloud Daten verwaltet und schützt](data-security.md).
- Überprüfen Sie die [Plattformen, die Defender für Cloud unterstützen](security-center-os-coverage.md).
