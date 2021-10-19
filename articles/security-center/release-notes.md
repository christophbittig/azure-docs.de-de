---
title: Versionshinweise für Azure Security Center
description: Enthält eine Beschreibung der Neuerungen und Änderungen in Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 10/06/2021
ms.author: memildin
ms.openlocfilehash: bd8ebca221041684a47bb66bb01c176fd1b65ba6
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729518"
---
# <a name="whats-new-in-azure-security-center"></a>Neuerungen in Azure Security Center

Azure Security Center befindet sich in der aktiven Entwicklung und wird ständig verbessert. Damit Sie bezüglich der aktuellen Entwicklungen immer auf dem neuesten Stand sind, enthält diese Seite Informationen zu neuen Features, Fehlerbehebungen und veralteten Funktionen.

Es ist ratsam, diese Seite regelmäßig zu besuchen, da sie immer wieder aktualisiert wird. 

Weitere Informationen zu den *geplanten* Änderungen, die demnächst im Security Center anstehen, finden Sie unter [Wichtige bevorstehende Änderungen in Azure Security Center](upcoming-changes.md). 

> [!TIP]
> Elemente, die älter als sechs Monate sind, finden Sie im [Archiv zu den Neuerungen in Azure Security Center](release-notes-archive.md).


## <a name="october-2021"></a>Oktober 2021

Updates im Oktober:

- [ Microsoft Threat and Vulnerability Management als Lösung zur Schwachstellenbewertung hinzugefügt (in der Vorschau)](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview)
- [Schwachstellenbewertungslösungen können jetzt automatisch aktiviert werden (in der Vorschau)](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview)
- [Software-Inventarisierungsfilter zum Bestandsverzeichnis hinzugefügt (in Vorschau)](#software-inventory-filters-added-to-asset-inventory-in-preview)
- [Ändern des Präfix einiger Warnungstypen von „ARM_“ in „VM_“](#changed-prefix-of-some-alert-types-from-arm_-to-vm_)


### <a name="microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview"></a>Microsoft Threat and Vulnerability Management als Lösung zur Schwachstellenbewertung hinzugefügt (in der Vorschau)

Wir haben die Integration zwischen [Azure Defender für Server](defender-for-servers-introduction.md) und Microsoft Defender für Endpoint erweitert, um eine neue Schwachstellenbewertung für Ihre Rechner zu unterstützen: [Microsoft Bedrohungs- und Schwachstellenmanagement](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt). 

Verwenden Sie **Bedrohungs- und Schwachstellenmanagement**, um Schwachstellen und Fehlkonfigurationen nahezu in Echtzeit zu erkennen, wenn die [Integration mit Microsoft Defender für Endpoint](security-center-wdatp.md) aktiviert ist, ohne dass zusätzliche Agenten oder regelmäßige Scans erforderlich sind. Das Bedrohungs- und Schwachstellenmanagement priorisiert Schwachstellen auf der Grundlage der Bedrohungslandschaft und der Entdeckungen in Ihrem Unternehmen.

Verwenden Sie die Sicherheitsempfehlung "[Eine Lösung zur Bewertung von Schwachstellen sollte auf Ihren virtuellen Maschinen aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ffff0522-1e88-47fc-8382-2a80ba848f5d)", um die von der Bedrohungs- und Schwachstellenverwaltung erkannten Schwachstellen für Ihre [unterstützten Maschinen](/microsoft-365/security/defender-endpoint/tvm-supported-os?view=o365-worldwide&preserve-view=true) anzuzeigen. 

Um die Schwachstellen auf bestehenden und neuen Maschinen automatisch zu erkennen, ohne die Empfehlung manuell korrigieren zu müssen, siehe [Schwachstellenbewertungslösungen können jetzt automatisch aktiviert werden (in der Vorschau)](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview).

Weitere Informationen finden Sie unter [Untersuchen Sie Schwachstellen mit dem Bedrohungs- und Schwachstellenmanagement von Microsoft Defender for Endpoint](deploy-vulnerability-assessment-tvm.md).

### <a name="vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview"></a>Lösungen zur Schwachstellenanalyse können jetzt automatisch aktiviert werden (in der Vorschau)

Die Seite für die automatische Bereitstellung im Security Center enthält jetzt die Option zur automatischen Aktivierung einer Lösung zur Schwachstellenanalyse für virtuelle Azure-Maschinen und Azure Arc-Maschinen in Abonnements, die durch [Azure Defender für Server](defender-for-servers-introduction.md) geschützt sind.

Wenn die [Integration mit Microsoft Defender for Endpoint](security-center-wdatp.md) aktiviert ist, haben Sie außerdem eine Auswahl an Lösungen zur Schwachstellenbewertung:

- (**NEU**) Das Microsoft Bedrohungs- und Schwachstellenmanagement-Modul von Microsoft Defender für Endpoint (siehe [die Release Note](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview))
- Der integrierte Qualys-Agent

:::image type="content" source="media/deploy-vulnerability-assessment-tvm/auto-provision-vulnerability-assessment-agent.png" alt-text="Konfigurieren Sie die automatische Bereitstellung von Microsofts Bedrohungs- und Schwachstellenmanagement über das Azure Security Center.":::

Die von Ihnen gewählte Lösung wird automatisch auf unterstützten Maschinen aktiviert.

Erfahren Sie mehr unter [Automatische Konfiguration der Schwachstellenbewertung für Ihre Maschinen](auto-deploy-vulnerability-assessment.md).

### <a name="software-inventory-filters-added-to-asset-inventory-in-preview"></a>Software-Inventarisierungsfilter zum Anlageninventar hinzugefügt (in Vorschau)

Die Seite [Anlageninventar](asset-inventory.md) enthält jetzt einen Filter zur Auswahl von Rechnern, auf denen bestimmte Software läuft - und sogar zur Angabe der gewünschten Versionen. 

Außerdem können Sie die Softwareinventardaten im **Azure Resource Graph Explorer** abfragen.

Um diese neuen Funktionen nutzen zu können, müssen Sie die [Integration mit Microsoft Defender for Endpoint](security-center-wdatp.md) aktivieren. 

Ausführliche Informationen, einschließlich Beispielabfragen von Kusto für Azure Resource Graph, finden Sie unter [Zugriff auf ein Softwareinventar](asset-inventory.md#access-a-software-inventory).

:::image type="content" source="media/deploy-vulnerability-assessment-tvm/software-inventory.png" alt-text="Wenn Sie die Lösung für Bedrohungen und Schwachstellen aktiviert haben, bietet das Bestandsverzeichnis des Security Centers einen Filter zur Auswahl von Ressourcen anhand ihrer installierten Software.":::

### <a name="changed-prefix-of-some-alert-types-from-arm_-to-vm_"></a>Ändern des Präfix einiger Warnungstypen von „ARM_“ in „VM_“ 

Im Juli 2021 haben wir eine [logische Neuorganisation des Azure Defender für Resource Manager-Warnungen](release-notes.md#logical-reorganization-of-azure-defender-for-resource-manager-alerts) angekündigt. 

Im Rahmen einer logischen Umstrukturierung einiger Azure Defender-Pläne haben wir einundzwanzig Alarme von [Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md) nach [Azure Defender für Server](defender-for-servers-introduction.md) verschoben.

Mit diesem Update haben wir die Präfixe dieser Warnungen so geändert, dass sie mit dieser Neuzuordnung übereinstimmen, und „ARM_“ durch „VM_“ ersetzt, wie in der folgenden Tabelle gezeigt:

| Ursprünglicher Name                                  | Aus dieser Änderung                              |
|------------------------------------------------|-----------------------------------------------|
| ARM_AmBroadFilesExclusion                      | VM_AmBroadFilesExclusion                      |
| ARM_AmDisablementAndCodeExecution              | VM_AmDisablementAndCodeExecution              |
| ARM_AmDisablement                              | VM_AmDisablement                              |
| ARM_AmFileExclusionAndCodeExecution            | VM_AmFileExclusionAndCodeExecution            |
| ARM_AmTempFileExclusionAndCodeExecution        | VM_AmTempFileExclusionAndCodeExecution        |
| ARM_AmTempFileExclusion                        | VM_AmTempFileExclusion                        |
| ARM_AmRealtimeProtectionDisabled               | VM_AmRealtimeProtectionDisabled               |
| ARM_AmTempRealtimeProtectionDisablement        | VM_AmTempRealtimeProtectionDisablement        |
| ARM_AmRealtimeProtectionDisablementAndCodeExec | VM_AmRealtimeProtectionDisablementAndCodeExec |
| ARM_AmMalwareCampaignRelatedExclusion          | VM_AmMalwareCampaignRelatedExclusion          |
| ARM_AmTemporarilyDisablement                   | VM_AmTemporarilyDisablement                   |
| ARM_UnusualAmFileExclusion                     | VM_UnusualAmFileExclusion                     |
| ARM_CustomScriptExtensionSuspiciousCmd         | VM_CustomScriptExtensionSuspiciousCmd         |
| ARM_CustomScriptExtensionSuspiciousEntryPoint  | VM_CustomScriptExtensionSuspiciousEntryPoint  |
| ARM_CustomScriptExtensionSuspiciousPayload     | VM_CustomScriptExtensionSuspiciousPayload     |
| ARM_CustomScriptExtensionSuspiciousFailure     | VM_CustomScriptExtensionSuspiciousFailure     |
| ARM_CustomScriptExtensionUnusualDeletion       | VM_CustomScriptExtensionUnusualDeletion       |
| ARM_CustomScriptExtensionUnusualExecution      | VM_CustomScriptExtensionUnusualExecution      |
| ARM_VMAccessUnusualConfigReset                 | VM_VMAccessUnusualConfigReset                 |
| ARM_VMAccessUnusualPasswordReset               | VM_VMAccessUnusualPasswordReset               |
| ARM_VMAccessUnusualSSHReset                    | VM_VMAccessUnusualSSHReset                    |
|||

Hier finden Sie weitere Informationen zu den Plänen [Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md) und [Azure Defender für Server](defender-for-servers-introduction.md).

## <a name="september-2021"></a>September 2021

Im September wurde das folgende Update veröffentlicht:

### <a name="two-new-recommendations-to-audit-os-configurations-for-azure-security-baseline-compliance-in-preview"></a>Zwei neue Empfehlungen zur Prüfung von Betriebssystemkonfigurationen für die Einhaltung der Azure-Sicherheitsgrundlagen (in der Vorschau)

Die folgenden beiden Empfehlungen wurden veröffentlicht, um die Konformität Ihrer Computer mit der [Windows-Sicherheitsbaseline](../governance/policy/samples/guest-configuration-baseline-windows.md) und der [Linux-Sicherheitsbaseline](../governance/policy/samples/guest-configuration-baseline-linux.md) zu bewerten:

- Für Windows-Rechner sollten [Schwachstellen in der Sicherheitskonfiguration auf Ihren Windows-Rechnern behoben werden (mit Hilfe der Gastkonfiguration).](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6)
- Für Linux-Rechner sollten [Schwachstellen in der Sicherheitskonfiguration auf Ihren Linux-Rechnern behoben werden (mit Hilfe der Gastkonfiguration)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda).

Diese Empfehlungen verwenden die Gastkonfigurationsfeature von Azure Policy, um die Betriebssystemkonfiguration einer Maschine mit der im [Azure Security Benchmark](/security/benchmark/azure/overview) definierten Baseline zu vergleichen.

Weitere Informationen zur Verwendung dieser Empfehlungen finden Sie unter [Härten der Betriebssystemkonfiguration eines Computers mithilfe der Gastkonfiguration](apply-security-baseline.md).

## <a name="august-2021"></a>August 2021

Updates im August:

- [Microsoft Defender für Endpunkt für Linux wird jetzt von Azure Defender für Server (Vorschauversion) unterstützt](#microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview)
- [Zwei neue Empfehlungen für die Verwaltung von Endpoint Protection-Lösungen (Vorschauversion)](#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview)
- [Integrierte Problembehandlung und Anleitungen zum Beheben häufiger Probleme](#built-in-troubleshooting-and-guidance-for-solving-common-issues)
- [Azure Audit-Berichte des Dashboards für die Einhaltung gesetzlicher Bestimmungen, veröffentlicht zur allgemeinen Verfügbarkeit](#regulatory-compliance-dashboards-azure-audit-reports-released-for-general-availability-ga)
- [Empfehlung „Log Analytics-Agent-Integritätsprobleme müssen auf Computern gelöst werden“ veraltet](#deprecated-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines)
- [Azure Defender für Containerregistrierungen sucht jetzt mit Azure Private Link nach Sicherheitsrisiken in Registrierungen](#azure-defender-for-container-registries-now-scans-for-vulnerabilities-in-registries-protected-with-azure-private-link)
- [Security Center kann jetzt die Azure Policy-Erweiterung für Gastkonfigurationen (Vorschauversion) automatisch bereitstellen](#security-center-can-now-auto-provision-the-azure-policys-guest-configuration-extension-in-preview)
- [Empfehlungen zum Aktivieren von Azure Defender-Plänen unterstützen jetzt die Option „Erzwingen“](#recommendations-to-enable-azure-defender-plans-now-support-enforce)
- [CSV-Exporte von Empfehlungsdaten jetzt auf 20 MB beschränkt](#csv-exports-of-recommendation-data-now-limited-to-20-mb)
- [Die Seite „Empfehlungen“ enthält jetzt mehrere Ansichten](#recommendations-page-now-includes-multiple-views)

### <a name="microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview"></a>Microsoft Defender für Endpunkt für Linux wird jetzt von Azure Defender für Server (Vorschauversion) unterstützt

[Azure Defender für Server](defender-for-servers-introduction.md) beinhaltet eine integrierte Lizenz für [Microsoft Defender für Endpunkt](https://www.microsoft.com/microsoft-365/security/endpoint-defender). Dadurch stehen umfassende EDR-Funktionen (Endpoint Detection and Response; Endpunkterkennung und -reaktion) zur Verfügung.

Wenn Defender für Endpunkt eine Bedrohung erkennt, wird eine Warnung ausgelöst. Die Warnung wird in Security Center angezeigt. Über Security Center können Sie auch zur Defender für Endpunkt-Konsole wechseln und eine ausführliche Untersuchung durchführen, um das Ausmaß des Angriffs zu ermitteln.

Während des Vorschauzeitraums stellen Sie den [Defender für Endpunkt für Linux](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux)-Sensor auf unterstützten Linux-Computern auf eine von zwei Arten zur Verfügung, je nachdem, ob Sie ihn bereits auf Ihren Windows-Computern bereitgestellt haben:

- [Vorhandene Benutzer von Azure Defender und Microsoft Defender für Endpunkt für Windows](security-center-wdatp.md?tabs=linux#existing-users-of-azure-defender-and-microsoft-defender-for-endpoint-for-windows)
- [Neue Benutzer, die die Integration in Microsoft Defender für Endpunkt für Windows noch nie aktiviert haben](security-center-wdatp.md?tabs=linux#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows)

Weitere Informationen finden Sie unter [Schützen Sie Ihre Endpunkte mit der in Security Center integrierten EDR-Lösung: Microsoft Defender für den Endpunkt](security-center-wdatp.md).

### <a name="two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview"></a>Zwei neue Empfehlungen für die Verwaltung von Endpoint Protection-Lösungen (Vorschauversion)

Er wurden zwei **Vorschau**-Empfehlungen für die Bereitstellung und Wartung der Endpoint Protection-Lösungen auf Ihren Computern hinzugefügt. Beide Empfehlungen beinhalten Unterstützung für virtuelle Azure-Maschinen und Maschinen, die mit Azure Arc-aktivierten Servern verbunden sind.

|Empfehlung |BESCHREIBUNG |severity |
|---|---|---|
|[Endpoint Protection sollte auf Ihren Computern installiert sein](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/4fb67663-9ab9-475d-b026-8c544cced439) |Installieren Sie eine unterstützte Endpoint Protection-Lösung, um Ihre Computer vor Bedrohungen und Sicherheitsrisiken zu schützen.  <br> <a href="/azure/security-center/security-center-endpoint-protection">Informieren Sie sich über die Endpoint Protection-Evaluierung für Computer.</a><br />(Zugehörige Richtlinie: [Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |Hoch |
|[Endpoint Protection-Integritätsprobleme sollten auf Ihren Computern gelöst werden](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/37a3689a-818e-4a0e-82ac-b1392b9bb000) |Beheben Sie Endpoint Protection-Integritätsprobleme auf Ihren virtuellen Computern, um diese vor den neuesten Bedrohungen und Sicherheitsrisiken zu schützen. Von Azure Security Center unterstützte Endpoint Protection-Lösungen sind [hier](./security-center-services.md?tabs=features-windows) dokumentiert. Die Endpoint Protection-Bewertung ist <a href='/azure/security-center/security-center-endpoint-protection'>hier</a> dokumentiert.<br />(Zugehörige Richtlinie: [Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |Medium |
|||

> [!NOTE]
> Die Empfehlungen zeigen als Aktualisierungsintervall 8 Stunden an, aber es gibt einige Szenarien, in denen dies erheblich länger dauern kann. Wenn beispielsweise ein lokaler Computer gelöscht wird, dauert es 24 Stunden, bis Security Center die Löschung erkannt hat. Danach dauert es bis zu 8 Stunden, bis die Bewertungsinformationen zurückgegeben werden. In dieser speziellen Situation kann es daher 32 Stunden dauern, bis der Computer aus der Liste der betroffenen Ressourcen entfernt wird.
>
> :::image type="content" source="media/release-notes/freshness-interval.png" alt-text="Indikator für das Aktualisierungsintervall für diese beiden neuen Security Center Empfehlungen":::

### <a name="built-in-troubleshooting-and-guidance-for-solving-common-issues"></a>Integrierte Problembehandlung und Anleitungen zum Beheben häufiger Probleme

Ein neuer, dedizierter Bereich der Security Center-Seiten im Azure-Portal bietet einen sortierten, ständig wachsenden Satz von Selbsthilfematerialien zur Lösung gängiger Herausforderungen mit Security Center Azure Defender.

Wenn Sie ein Problem haben oder von unserem Supportteam Hilfe anfordern möchten, ist **Diagnose und Problemlösung** ein weiteres Tool, mit dem Sie die Lösung finden können:

:::image type="content" source="media/release-notes/solve-problems.png" alt-text="Security Center-Seite „Diagnose und Problembehandlung“":::
 

### <a name="regulatory-compliance-dashboards-azure-audit-reports-released-for-general-availability-ga"></a>Azure Audit-Berichte des Dashboards für die Einhaltung gesetzlicher Bestimmungen, veröffentlicht zur allgemeinen Verfügbarkeit

Die Symbolleiste des Dashboards für die Einhaltung gesetzlicher Bestimmungen bietet Azure- und Dynamics-Zertifizierungsberichte für die auf Ihre Abonnements angewendeten Standards. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Symbolleiste des Dashboards für die Einhaltung gesetzlicher Bestimmungen mit der Schaltfläche zum Generieren von Überwachungsberichten.":::

Sie können die Registerkarte für die relevanten Berichtstypen (PCI, SOC, ISO usw.) auswählen und Filter verwenden, um nach den benötigten spezifischen Berichten zu suchen.

Weitere Informationen finden Sie unter [Generieren von Konformitätsstatusberichten und -zertifikaten](security-center-compliance-dashboard.md#generate-compliance-status-reports-and-certificates).

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard-ga.png" alt-text="Listen mit Registerkarten der verfügbaren Azure Audit -Berichte. Es werden Registerkarten für ISO-Berichte, SOC-Berichte, PCI und mehr angezeigt.":::

### <a name="deprecated-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines"></a>Empfehlung „Log Analytics-Agent-Integritätsprobleme müssen auf Computern gelöst werden“ veraltet

Wir haben festgestellt, dass die Empfehlung **Log Analytics-Agent-Integritätsprobleme müssen auf Computern gelöst werden** Auswirkungen auf die Sicherheitsbewertungen haben, die dem Schwerpunkt von Security Center Cloud Security Posture Management (CSPM) nicht entsprechen. In der Regel bezieht sich CSPM auf die Ermittlung von Fehlkonfigurationen bei der Sicherheit. Probleme mit der Agent-Integrität gehören nicht in diese Problemkategorie.

Darüber hinaus handelt es sich bei der Empfehlung im Gegensatz zu den anderen Agents im Zusammenhang mit Security Center um eine Anomalie: Dies ist der einzige Agent mit einer Empfehlung im Zusammenhang mit Integritätsproblemen.

Die Empfehlung ist veraltet.

Daher haben wir auch geringfügige Änderungen an den Empfehlungen für die Installation des Log Analytics-Agents vorgenommen (**Der Log Analytics-Agent muss auf ... installiert sein**).

Diese Änderung wirkt sich mit hoher Wahrscheinlichkeit auf Ihre Sicherheitsbewertungen aus. Bei den meisten Abonnements erwarten wir, dass die Änderung zu einer höheren Bewertung führt. Es ist aber möglich, dass die Updates der Installationsempfehlung in einigen Fällen zu niedrigeren Bewertungen führen können.

> [!TIP]
> Diese Änderung wirkte sich auch auf die Seite [Ressourcenbestand](asset-inventory.md) aus, da diese den Überwachungsstatus eines Computers anzeigt (überwacht, nicht überwacht oder teilweise überwacht) – ein Zustand, der einen Agent mit Integritätsproblemen anzeigt.


### <a name="azure-defender-for-container-registries-now-scans-for-vulnerabilities-in-registries-protected-with-azure-private-link"></a>Azure Defender für Containerregistrierungen sucht jetzt mit Azure Private Link nach Sicherheitsrisiken in Registrierungen
Azure Defender für Containerregistrierungen enthält eine Überprüfungen auf Sicherheitsrisiken für Images in Ihren Azure Container Registry-Registrierungen. Informationen zum Überprüfen Ihrer Registrierungen und zum Beheben der Ergebnissen finden Sie unter [Verwenden von Azure Defender für Containerregistrierungen zum Überprüfen Ihrer Images auf Sicherheitsrisiken](defender-for-container-registries-usage.md).

Um den Zugriff auf eine in Azure Container Registry gehostete Registrierung zu beschränken, weisen Sie den Registrierungsendpunkten private IP-Adressen des virtuellen Netzwerks zu, und verwenden Sie Azure Private Link, wie in [Herstellen einer privaten Verbindung mit einer Azure-Containerregistrierung über Azure Private Link](../container-registry/container-registry-private-link.md) beschrieben.

Im Rahmen unserer kontinuierlichen Bemühungen zur Unterstützung zusätzlicher Umgebungen und Anwendungsfälle überprüft Azure Defender jetzt auch Containerregistrierungen, die mit [Azure Private Link](../private-link/private-link-overview.md) geschützt werden.


### <a name="security-center-can-now-auto-provision-the-azure-policys-guest-configuration-extension-in-preview"></a>Security Center kann jetzt die Azure Policy-Erweiterung für Gastkonfigurationen (Vorschauversion) automatisch bereitstellen
Mit Azure Policy können Einstellungen innerhalb eines Computers überwacht werden. Dies gilt für in Azure ausgeführte Computer sowie für über Arc verbundene Computer. Für die Überprüfung verwenden Sie die Erweiterung und den Client Guest Configuration. Weitere Informationen finden Sie in [Grundlegendes zur Gastkonfiguration von Azure Policy](../governance/policy/concepts/guest-configuration.md).

Mit diesem Update können Sie jetzt festlegen, dass Security Center diese Erweiterung automatisch für alle unterstützten Computer bereitstellt. 

:::image type="content" source="media/release-notes/auto-provisioning-guest-configuration.png" alt-text="Aktivieren Sie die automatische Bereitstellung der Erweiterung für Gastkonfigurationen.":::

Weitere Informationen zur Funktionsweise der automatischen Bereitstellung finden Sie unter [Konfigurieren der automatischen Bereitstellung für Agents und Erweiterungen](security-center-enable-data-collection.md).

### <a name="recommendations-to-enable-azure-defender-plans-now-support-enforce"></a>Empfehlungen zum Aktivieren von Azure Defender-Plänen unterstützen jetzt die Option „Erzwingen“
Security Center enthält zwei Features, die sicherstellen, dass neu erstellte Ressourcen auf sichere Weise bereitgestellt werden: **Erzwingen** und **Verweigern**. Wenn eine Empfehlung diese Optionen bietet, können Sie sicherstellen, dass Ihre Sicherheitsanforderungen erfüllt werden, sobald jemand versucht, eine Ressource zu erstellen:

- **Verweigern** verhindert, dass fehlerhafte Ressourcen erstellt werden.
- **Erzwingen** sorgt automatisch dafür, dass nicht konforme Ressourcen bei ihrer Erstellung korrigiert werden.

Mit diesem Update ist nun die Option „Erzwingen“ in den Empfehlungen zur Aktivierung von Azure Defender-Plänen verfügbar (z. B. **Azure Defender für App Service muss aktiviert sein**, **Azure Defender für Key Vault muss aktiviert sein**, **Azure Defender für Speicher muss aktiviert sein**).

Weitere Informationen zu diesen Optionen finden Sie unter [Verhindern von Fehlkonfigurationen mit den Optionen zum Erzwingen/Ablehnen für Empfehlungen](prevent-misconfigurations.md).

### <a name="csv-exports-of-recommendation-data-now-limited-to-20-mb"></a>CSV-Exporte von Empfehlungsdaten jetzt auf 20 MB beschränkt

Es wurde ein Grenzwert von 20 MB für das Exportieren von Security Center-Empfehlungsdaten eingerichtet.

:::image type="content" source="media/upcoming-changes/download-csv-report.png" alt-text="Schaltfläche „CSV-Bericht herunterladen“ zum Exportieren von Empfehlungsdaten in Security Center":::

Wenn Sie größere Datenmengen exportieren müssen, verwenden Sie vor der Auswahl die verfügbaren Filter, oder wählen Sie Teilmengen Ihrer Abonnements aus, und laden Sie die Daten in Batches herunter.

:::image type="content" source="media/upcoming-changes/filter-subscriptions.png" alt-text="Filtern von Abonnements im Azure-Portal":::

Erfahren Sie mehr über das [Ausführen eines CSV-Exports Ihrer Sicherheitsempfehlungen](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations).



### <a name="recommendations-page-now-includes-multiple-views"></a>Die Seite „Empfehlungen“ enthält jetzt mehrere Ansichten

Die Seite „Empfehlungen“ verfügt jetzt über zwei Registerkarten, um alternative Möglichkeiten zum Anzeigen der Empfehlungen zu bieten, die für Ihre Ressourcen relevant sind:

- **Empfehlungen zur Sicherheitsbewertung**: Auf dieser Registerkarte können Sie die Liste der Empfehlungen, gruppiert nach Sicherheitskontrollen, anzeigen. Weitere Informationen zu diesen Steuerelementen finden Sie unter [Sicherheitskontrollen und deren Empfehlungen](secure-score-security-controls.md#security-controls-and-their-recommendations).
- **Alle Empfehlungen**: Auf dieser Registerkarte können Sie die Liste der Empfehlungen als einfache Liste anzeigen. Diese Registerkarte verdeutlicht zudem, welche Initiative (einschließlich Standards zur Einhaltung gesetzlicher Bestimmungen) die Empfehlung generiert hat. Weitere Informationen zu Initiativen und deren Beziehung zu Empfehlungen finden Sie unter [Was sind Sicherheitsrichtlinien, Initiativen und Empfehlungen?](security-policy-concept.md)

:::image type="content" source="media/release-notes/recommendations-tabs.png" alt-text="Registerkarten zum Ändern der Ansicht der Empfehlungsliste in Azure Security Center.":::

## <a name="july-2021"></a>Juli 2021

Zu den Updates im Juli gehören:

- [Der Azure Sentinel-Connector enthält jetzt optionale bidirektionale Warnungssynchronisierung (Vorschauversion)](#azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview)
- [Logische Neuorganisation des Azure Defender für Resource Manager-Warnungen](#logical-reorganization-of-azure-defender-for-resource-manager-alerts)                                           
- [Erweiterungen der Empfehlung zum Aktivieren Azure Disk Encryption (ADE)](#enhancements-to-recommendation-to-enable-azure-disk-encryption-ade)                                     
- [Fortlaufender Export von Daten zur Sicherheitsbewertung und zur Einhaltung gesetzlicher Bestimmungen, veröffentlicht zur allgemeinen Verfügbarkeit](#continuous-export-of-secure-score-and-regulatory-compliance-data-released-for-general-availability-ga)
- [Workflowautomatisierungen können durch Änderungen an Bewertungen der Einhaltung gesetzlicher Bestimmungen ausgelöst wurden (GA)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-ga)
- [Bewertungs-API-Felder „FirstEvaluationDate“ und „StatusChangeDate“ jetzt in Arbeitsbereichsschemas und Logik-Apps verfügbar](#assessments-api-field-firstevaluationdate-and-statuschangedate-now-available-in-workspace-schemas-and-logic-apps)
- [Arbeitsmappenvorlage „Konformität im Zeitverlauf“ zum Azure Monitor Workbooks-Katalog hinzugefügt](#compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery)

### <a name="azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview"></a>Der Azure Sentinel-Connector enthält jetzt optionale bidirektionale Warnungssynchronisierung (Vorschauversion)

Security Center ist nativ in [Azure Sentinel](../sentinel/index.yml), der cloudnativen SIEM- und SOAR-Lösung von Azure, integriert. 

Azure Sentinel umfasst integrierte Connectors für Azure Security Center auf Abonnement- und Mandantenebene. Weitere Informationen finden Sie unter [Streamen von Warnungen in Azure Sentinel](export-to-siem.md#stream-alerts-to-azure-sentinel).

Wenn Sie Azure Defender mit Azure Sentinel verbinden, wird der Status der in den Azure Sentinel-Dienst übernommenen Azure Defender-Warnungen zwischen den beiden Diensten synchronisiert. Wenn also beispielsweise eine Warnung in einem Azure Defender geschlossen wird, wird diese Warnung auch in Azure Sentinel als geschlossen angezeigt. Das Ändern des Status einer Warnung in Azure Defender wirkt sich „nicht“ auf den Status von Azure Sentinel-**Incidents** aus, die die synchronisierte Azure Sentinel-Warnung enthalten, sondern nur auf den Status der synchronisierten Warnung selbst.

Wenn Sie die Previewfunktion **bidirektionale Warnungssynchronisierung** aktivieren, wird der Status der ursprünglichen Azure Defender-Warnungen automatisch mit Azure Sentinel-Incidents synchronisiert, die die Kopien dieser Azure Defender-Warnungen enthalten. Wenn also beispielsweise ein Azure Sentinel-Incident, der eine Azure Defender-Warnung enthält, geschlossen wird, schließt Azure Defender automatisch die entsprechende ursprüngliche Warnung.

Weitere Informationen finden Sie unter [Verbinden von Azure Defender-Benachrichtigungen aus Azure Security Center](../sentinel/connect-azure-security-center.md).

### <a name="logical-reorganization-of-azure-defender-for-resource-manager-alerts"></a>Logische Neuorganisation des Azure Defender für Resource Manager-Warnungen

Die unten aufgeführten Warnungen wurden im Rahmen des [Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md)-Plans bereitgestellt.

Im Rahmen einer logischen Neuorganisation für einige der Azure Defender-Pläne wurden einzelne Warnungen vom **Azure Defender für Resource Manager** in den **Azure Defender für Server** verschoben.

Die Warnungen sind nach zwei Hauptprinzipien organisiert:

- Warnungen, die Schutz auf der Steuerungsebene für viele Azure-Ressourcentypen bieten, werden ein Teil des Azure Defender für Resource Manager
- Warnungen, die bestimmte Workloads schützen, befinden sich im Azure Defender-Plan, der sich auf diese Workload bezieht

Dies sind die Warnungen, die Teil des Azure Defender für Resource Manager waren und aufgrund dieser Änderung jetzt Teil von Azure Defender für Server sind:

- ARM_AmBroadFilesExclusion
- ARM_AmDisablementAndCodeExecution
- ARM_AmDisablement
- ARM_AmFileExclusionAndCodeExecution
- ARM_AmTempFileExclusionAndCodeExecution
- ARM_AmTempFileExclusion
- ARM_AmRealtimeProtectionDisabled
- ARM_AmTempRealtimeProtectionDisablement
- ARM_AmRealtimeProtectionDisablementAndCodeExec
- ARM_AmMalwareCampaignRelatedExclusion
- ARM_AmTemporarilyDisablement
- ARM_UnusualAmFileExclusion
- ARM_CustomScriptExtensionSuspiciousCmd
- ARM_CustomScriptExtensionSuspiciousEntryPoint
- ARM_CustomScriptExtensionSuspiciousPayload
- ARM_CustomScriptExtensionSuspiciousFailure
- ARM_CustomScriptExtensionUnusualDeletion
- ARM_CustomScriptExtensionUnusualExecution
- ARM_VMAccessUnusualConfigReset
- ARM_VMAccessUnusualPasswordReset
- ARM_VMAccessUnusualSSHReset

Hier finden Sie weitere Informationen zu den Plänen [Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md) und [Azure Defender für Server](defender-for-servers-introduction.md).


### <a name="enhancements-to-recommendation-to-enable-azure-disk-encryption-ade"></a>Erweiterungen der Empfehlung zum Aktivieren Azure Disk Encryption (ADE)

Aufgrund von Benutzerfeedback haben wir die Empfehlung **Die Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden** umbenannt.

Die neue Empfehlung verwendet die gleiche Bewertungs-ID und heißt **Virtuelle Computer sollten temporäre Datenträger, Caches und Datenflüsse zwischen Compute- und Speicherressourcen verschlüsseln**.

Die Beschreibung wurde ebenfalls aktualisiert, um den Zweck dieser Empfehlung zur Härtung verständlicher zu machen:

| Empfehlung                                                                                               | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | severity |
|--------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------:|
| **Virtuelle Computer sollten temporäre Datenträger, Caches und Datenflüsse zwischen Compute- und Speicherressourcen verschlüsseln** | Standardmäßig werden die Datenträger für das Betriebssystem und für die Daten eines virtuellen Computers im Ruhespeicher mithilfe von plattformseitig verwalteten Schlüsseln verschlüsselt. Temporäre Datenträger und Datencaches werden nicht verschlüsselt, und auch während der Übertragung zwischen Compute- und Speicherressourcen werden Daten nicht verschlüsselt. Einen Vergleich der verschiedenen Datenträgerverschlüsselungstechnologien in Azure finden Sie unter https://aka.ms/diskencryptioncomparison.<br>Verwenden Sie Azure Disk Encryption, um sämtliche dieser Daten zu verschlüsseln. Ignorieren Sie diese Empfehlung, wenn (1) Sie die Verschlüsselung auf dem Host verwenden oder wenn die (2) serverseitige Verschlüsselung auf Managed Disks Ihre Sicherheitsanforderungen erfüllt. Weitere Informationen erhalten Sie unter „Serverseitige Verschlüsselung von Azure Disk Storage“. | Hoch     |
|                                                                                                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |          |


### <a name="continuous-export-of-secure-score-and-regulatory-compliance-data-released-for-general-availability-ga"></a>Fortlaufender Export von Daten zur Sicherheitsbewertung und zur Einhaltung gesetzlicher Bestimmungen, veröffentlicht zur allgemeinen Verfügbarkeit

Der [fortlaufende Export](continuous-export.md) stellt den Mechanismus zum Exportieren Ihrer Sicherheitswarnungen und Empfehlungen für die Nachverfolgung mit anderen Überwachungstools in Ihrer Umgebung bereit.

Wenn Sie den fortlaufenden Export einrichten, konfigurieren Sie, was exportiert wird und wohin die Daten übertragen werden. Weitere Informationen finden Sie unter [Übersicht über den fortlaufende Export](continuous-export.md).

Wir haben dieses Feature im Laufe der Zeit verbessert und erweitert:

- Im November 2020 haben wir die **Vorschauoption** hinzugefügt, um Änderungen an Ihre **Sicherheitsbewertung** zu streamen.<br/>Vollständige Informationen finden Sie unter [Die Sicherheitsbewertung ist jetzt im fortlaufenden Export (Vorschauversion) verfügbar](release-notes-archive.md#secure-score-is-now-available-in-continuous-export-preview).

- Im Dezember 2020 haben wir die **Vorschaufunktion** hinzugefügt, mit der Sie Änderungen an Ihren **Daten zur Bewertung der Einhaltung gesetzlicher Bestimmungen** streamen können.<br/>Ausführliche Informationen finden Sie unter [Fortlaufender Export ruft neue Datentypen ab (Vorschau)](release-notes-archive.md#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies).

Mit diesem Update werden diese beiden Optionen zur allgemeinen Verfügbarkeit veröffentlicht. 


### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-ga"></a>Workflowautomatisierungen können durch Änderungen an Bewertungen der Einhaltung gesetzlicher Bestimmungen ausgelöst wurden (GA)

Im Februar 2021 haben wir die **Vorschau** eines dritten Datentyps zu den Triggeroptionen für Ihre Workflowautomatisierungen hinzugefügt: Änderungen an Bewertungen der Einhaltung gesetzlicher Bestimmungen. Weitere Informationen finden Sie unter [Workflowautomatisierungen können durch Änderungen an Bewertungen der Einhaltung gesetzlicher Bestimmungen ausgelöst werden](release-notes-archive.md#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview).

Mit diesem Update wird diese Triggeroption zur allgemeinen Verfügbarkeit veröffentlicht.

Informieren Sie sich unter [Automatisieren der Reaktionen auf Security Center-Trigger](workflow-automation.md) über die Nutzung der Tools für die Workflowautomatisierung.

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Verwenden von Änderungen bei Bewertungen der Einhaltung gesetzlicher Bestimmungen zum Auslösen der Workflowautomatisierung" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

### <a name="assessments-api-field-firstevaluationdate-and-statuschangedate-now-available-in-workspace-schemas-and-logic-apps"></a>Bewertungs-API-Felder „FirstEvaluationDate“ und „StatusChangeDate“ jetzt in Arbeitsbereichsschemas und Logik-Apps verfügbar

Im Mai 2021 haben wir der Bewertungs-API zwei neue Felder hinzugefügt: **FirstEvaluationDate** und **StatusChangeDate**. Vollständige Informationen finden Sie unter [Die Bewertungs-API wurde um zwei neue Felder erweitert](#assessments-api-expanded-with-two-new-fields).

Auf diese Felder kann über die REST-API, Azure Resource Graph, den fortlaufenden Export und in CSV-Exporten zugegriffen werden.

Mit dieser Änderung stellen wir die Informationen im Log Analytics-Arbeitsbereichsschema und in Logik-Apps zur Verfügung.


### <a name="compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery"></a>Arbeitsmappenvorlage „Konformität im Zeitverlauf“ zum Azure Monitor Workbooks-Katalog hinzugefügt

Im März haben wir die integrierte Azure Monitor-Arbeitsmappenerfahrung in Security Center angekündigt (siehe [Integration von Azure Monitor-Arbeitsmappen in Security Center und Bereitstellung von drei Vorlagen](release-notes-archive.md#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)).

Das erste Release enthielt drei Vorlagen zum Erstellen dynamischer und visueller Berichte über den Sicherheitsstatus Ihrer Organisation.

Wir haben nun eine Arbeitsmappe hinzugefügt, die speziell für die Nachverfolgung der Konformität eines Abonnements mit den dafür geltenden gesetzlichen Vorschriften oder Branchenstandards bestimmt ist. 

Informieren Sie sich über die Nutzung dieser Berichte oder die Erstellung eigener Berichte unter [Erstellen umfassender interaktiver Berichte für Security Center-Daten](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-details.png" alt-text="Azure Security Center-Arbeitsmappe „Konformität im Zeitverlauf“":::


## <a name="june-2021"></a>Juni 2021

Zu den Updates im Juni gehören:

- [Neue Warnung für Azure Defender für Key Vault](#new-alert-for-azure-defender-for-key-vault)
- [Empfehlungen zur Verschlüsselung mit standardmäßig deaktivierten kundenseitig verwalteten Schlüsseln](#recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default)
- [Änderung des Präfix für Kubernetes-Warnungen von „AKS_“ in „K8S_“](#prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_)
- [Zwei Empfehlungen der Sicherheitskontrolle „Systemupdates anwenden“ wurden als veraltet eingestuft](#deprecated-two-recommendations-from-apply-system-updates-security-control)


### <a name="new-alert-for-azure-defender-for-key-vault"></a>Neue Warnung für Azure Defender für Key Vault

Wir haben die folgende Warnung hinzugefügt, um den Bedrohungsschutz von Azure Defender für Key Vault zu erweitern:

| Warnung (Warnungstyp)                                                                 | Beschreibung                                                                                                                                                                                                                                                                                                                                                      | MITRE-Taktik | severity |
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------:|----------|
| Zugriff auf einen Schlüsseltresor von einer verdächtigen IP-Adresse<br>(KV_SuspiciousIPAccess)  | Auf einen Schlüsseltresor ist ein erfolgreicher Zugriff von einer IP-Adresse erfolgt, die von Microsoft Threat Intelligence als verdächtige IP-Adresse identifiziert wurde. Dies kann ggf. ein Hinweis darauf sein, dass Ihre Infrastruktur kompromittiert wurde. Wir empfehlen Ihnen, dies eingehender zu untersuchen. Weitere Informationen finden Sie unter [Threat Intelligence-Funktionen von Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684). | Zugriff auf Anmeldeinformationen                            | Medium   |
|||

Weitere Informationen finden Sie unter
- [Einführung in Azure Defender für Key Vault](defender-for-resource-manager-introduction.md)
- [Reagieren auf Warnungen zu Azure Defender für Key Vault](defender-for-key-vault-usage.md)
- [Liste mit Warnungen von Azure Defender für Key Vault](alerts-reference.md#alerts-azurekv)


### <a name="recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default"></a>Empfehlungen zur Verschlüsselung mit standardmäßig deaktivierten kundenseitig verwalteten Schlüsseln

Security Center enthält mehrere Empfehlungen zur Verschlüsselung von ruhenden Daten mit kundenseitig verwalteten Schlüsseln, z. B.:

- Containerregistrierungen müssen mit einem kundenseitig verwalteten Schlüssel (CMK) verschlüsselt werden
- Azure Cosmos DB-Konten sollten kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden.
- Azure Machine Learning-Arbeitsbereiche müssen mit einem kundenseitig verwalteten Schlüssel (CMK) verschlüsselt werden

Daten werden in Azure automatisch mit plattformseitig verwalteten Schlüsseln verschlüsselt. Daher sollten kundenseitig verwaltete Schlüssel nur angewendet werden, wenn dies zur Einhaltung einer bestimmten Richtlinie erforderlich ist, die in Ihrer Organisation durchgesetzt werden soll.

Aufgrund dieser Änderung sind die Empfehlungen zur Nutzung von kundenseitig verwalteten Schlüsseln jetzt **standardmäßig deaktiviert**. Sie können sie wieder aktivieren, falls dies für Ihre Organisation relevant ist. Ändern Sie hierfür den Parameter *Effect* für die entsprechende Sicherheitsrichtlinie in **AuditIfNotExists** oder **Enforce**. Weitere Informationen finden Sie auf der Seite mit den Informationen zum [Aktivieren einer Sicherheitsrichtlinie](tutorial-security-policy.md#enable-a-security-policy).

Diese Änderung wird in den Namen der Empfehlung durch das neue Präfix **[Bei Bedarf aktivieren]** widergespiegelt. Dies wird in den folgenden Beispielen veranschaulicht:

- [Bei Bedarf aktivieren] Speicherkonten müssen für die Verschlüsselung von ruhenden Daten einen kundenseitig verwalteten Schlüssel verwenden
- [Bei Bedarf aktivieren] Containerregistrierungen müssen mit einem kundenseitig verwalteten Schlüssel verschlüsselt werden
- [Bei Bedarf aktivieren] Azure Cosmos DB-Konten müssen kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden

:::image type="content" source="media/upcoming-changes/customer-managed-keys-disabled.png" alt-text="CMK-Empfehlungen von Security Center sind standardmäßig deaktiviert" lightbox="media/upcoming-changes/customer-managed-keys-disabled.png":::


### <a name="prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_"></a>Änderung des Präfix für Kubernetes-Warnungen von „AKS_“ in „K8S_“

Azure Defender für Kubernetes wurde kürzlich erweitert, um Kubernetes-Cluster zu schützen, die lokal und in Umgebungen mit mehreren Clouds gehostet werden. Weitere Informationen finden Sie unter [Verwenden von Azure Defender für Kubernetes zum Schutz von Hybrid- und Multi-Cloud-Bereitstellungen von Kubernetes (Vorschau)](release-notes-archive.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview).

Um deutlich zu machen, dass die von Azure Defender für Kubernetes bereitgestellten Sicherheitswarnungen nicht mehr auf Cluster in Azure Kubernetes Service beschränkt sind, haben wir das Präfix für die Warnungstypen von „AKS_“ in „K8S_“ geändert. Bei Bedarf wurden auch die Namen und Beschreibungen aktualisiert. Ein Beispiel:

|Warnung (Warnungstyp)|Beschreibung|
|----|----|
|Erkannte Tools für Kubernetes-Penetrationstests<br>(**AKS** _PenTestToolsKubeHunter)|Die Analyse des Kubernetes-Überwachungsprotokolls hat die Verwendung von Kubernetes-Penetrationstesttools im **AKS**-Cluster erkannt. Dieses Verhalten kann zwar legitim sein, aber Angreifer können solche öffentlichen Tools für böswillige Zwecke nutzen.
|||

wurde geändert in:

|Warnung (Warnungstyp)|Beschreibung|
|----|----|
|Erkannte Tools für Kubernetes-Penetrationstests<br>(**K8S** _PenTestToolsKubeHunter)|Die Analyse des Kubernetes-Überwachungsprotokolls hat die Verwendung von Kubernetes-Penetrationstesttools im **Kubernetes**-Cluster erkannt. Dieses Verhalten kann zwar legitim sein, aber Angreifer können solche öffentlichen Tools für böswillige Zwecke nutzen.|
|||

Unterdrückungsregeln, in denen auf mit „AKS_“ beginnende Warnungen verwiesen wird, wurden automatisch konvertiert. Wenn Sie SIEM-Exporte oder benutzerdefinierte Automatisierungsskripts eingerichtet haben, in denen anhand des Warnungstyps auf Kubernetes-Warnungen verwiesen wird, müssen diese mit den neuen Warnungstypen aktualisiert werden.

Eine vollständige Liste der Kubernetes-Warnungen finden Sie unter [Warnungen für Container: Kubernetes-Cluster](alerts-reference.md#alerts-k8scluster).

### <a name="deprecated-two-recommendations-from-apply-system-updates-security-control"></a>Zwei Empfehlungen der Sicherheitskontrolle „Systemupdates anwenden“ wurden als veraltet eingestuft

Die beiden folgenden Empfehlungen wurden als veraltet eingestuft:

- **Die Betriebssystemversion sollte für Ihre Clouddienstrollen aktualisiert werden**: Azure aktualisiert Ihr Gastbetriebssystem standardmäßig in regelmäßigen Abständen auf das neueste Image innerhalb der BS-Familie, die Sie in der Dienstkonfiguration (CSCFG-Datei) angegeben haben (z B. Windows Server 2016).
- **Für Kubernetes Service muss ein Upgrade auf eine Kubernetes-Version ohne Sicherheitsrisiko durchgeführt werden**: Die Evaluierungen dieser Empfehlung sind uns nicht weitreichend genug. Wir planen, die Empfehlung durch eine erweiterte Version zu ersetzen, die besser auf Ihre Sicherheitsanforderungen abgestimmt ist.


## <a name="may-2021"></a>Mai 2021

Zu den Updates im Mai gehören:

- [Azure Defender für DNS und Azure Defender für Resource Manager, veröffentlicht zur allgemeinen Verfügbarkeit](#azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga)
- [Azure Defender für relationale Open-Source-Datenbanken, veröffentlicht zur allgemeinen Verfügbarkeit](#azure-defender-for-open-source-relational-databases-released-for-general-availability-ga)
- [Neue Warnungen für Azure Defender für Resource Manager](#new-alerts-for-azure-defender-for-resource-manager)
- [CI/CD-Sicherheitsrisikoüberprüfung von Containerimages mit GitHub-Workflows und Azure Defender (Vorschau)](#cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview)
- [Verfügbarkeit weiterer Resource Graph-Abfragen für einige Empfehlungen](#more-resource-graph-queries-available-for-some-recommendations)
- [Schweregrad der Empfehlung zur SQL-Datenklassifizierung geändert](#sql-data-classification-recommendation-severity-changed)
- [Neue Empfehlungen zur Aktivierung von Funktionen für den vertrauenswürdigen Start (Vorschau)](#new-recommendations-to-enable-trusted-launch-capabilities-in-preview)
- [Neue Empfehlungen für die Härtung von Kubernetes-Clustern (Vorschau)](#new-recommendations-for-hardening-kubernetes-clusters-in-preview)
- [Die Bewertungs-API wurde um zwei neue Felder erweitert.](#assessments-api-expanded-with-two-new-fields)
- [Cloudumgebungsfilter für Ressourcenbestand](#asset-inventory-gets-a-cloud-environment-filter)


### <a name="azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga"></a>Azure Defender für DNS und Azure Defender für Resource Manager, veröffentlicht zur allgemeinen Verfügbarkeit

Diese beiden breit gefächerten cloudnativen Bedrohungsschutzpläne befinden sich nun in der Phase der allgemeinen Verfügbarkeit.

Diese neuen Schutzmaßnahmen sorgen für eine erhebliche Verbesserung der Resilienz gegenüber Angriffen von Bedrohungsakteuren sowie für eine deutliche Erhöhung der Anzahl von Azure-Ressourcen, die durch Azure Defender geschützt werden.

- **Azure Defender für Resource Manager**: Überwacht automatisch alle in Ihrer Organisation ausgeführten Ressourcenverwaltungsvorgänge. Weitere Informationen finden Sie unter
    - [Einführung in Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md)
    - [Reagieren auf Warnungen von Azure Defender für Resource Manager](defender-for-resource-manager-usage.md)
    - [Warnungen für Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender für DNS**: Überwacht kontinuierlich alle DNS-Abfragen Ihrer Azure-Ressourcen. Weitere Informationen finden Sie unter
    - [Einführung in Azure Defender für DNS](defender-for-dns-introduction.md)
    - [Reagieren auf Warnungen von Azure Defender für DNS](defender-for-dns-usage.md)
    - [Warnungen für DNS](alerts-reference.md#alerts-dns)

Verwenden Sie die folgenden Empfehlungen, um den Prozess für die Aktivierung dieser Pläne zu vereinfachen:

- **Azure Defender für Resource Manager muss aktiviert sein**
- **Azure Defender für DNS muss aktiviert sein**

> [!NOTE]
> Bei Aktivierung dieser Azure Defender-Pläne fallen Gebühren an. Weitere Informationen zu den Preisen pro Region finden Sie in der Security Center-Preisübersicht unter https://aka.ms/pricing-security-center.


### <a name="azure-defender-for-open-source-relational-databases-released-for-general-availability-ga"></a>Azure Defender für relationale Open-Source-Datenbanken, veröffentlicht zur allgemeinen Verfügbarkeit

Das Angebot zum SQL-Schutz von Azure Security Center wird um ein neues Paket erweitert, das für Ihre relationalen Open-Source-Datenbanken gilt:

- **Azure Defender für Azure SQL-Datenbankserver** – schützt Ihre nativen Azure SQL Server.
- **Azure Defender für SQL Server auf Computern** – erweitert denselben Schutz auf Ihre SQL Server-Instanzen in Hybrid-, Multicloud- und lokalen Umgebungen
- **Azure Defender für relationale Open-Source-Datenbanken**: Dient zur Verteidigung Ihrer Single Server-Versionen von Azure Database for MySQL, PostgreSQL und MariaDB.

Mit Azure Defender für relationale Open-Source-Datenbanken werden Ihre Server ständig auf Sicherheitsbedrohungen überwacht, und es werden anomale Datenbankaktivitäten erkannt, die auf potenzielle Bedrohungen für Azure Database for MySQL, PostgreSQL und MariaDB hinweisen. Beispiele:

- **Präzise Erkennung von Brute-Force-Angriffen**: Azure Defender für relationale Open-Source-Datenbanken liefert ausführliche Informationen zu versuchten und erfolgreichen Brute-Force-Angriffen. So verfügen Sie über alle Informationen zur Art und zum Status des Angriffs auf Ihre Umgebung, die Sie benötigen, um die Untersuchung durchführen und entsprechend reagieren zu können.
- **Warnungen zur Erkennung von bestimmtem Verhalten**: Mit Azure Defender für relationale Open-Source-Datenbanken werden Sie vor verdächtigem und unerwartetem Verhalten auf Ihren Servern gewarnt, z. B. Änderungen beim Zugriffsmuster Ihrer Datenbank.
- **Auf Threat Intelligence basierende Erkennung**: Azure Defender wendet die Threat Intelligence-Informationen und eine umfangreiche Wissensdatenbank von Microsoft auf die Anzeige von Bedrohungswarnungen an, damit Sie entsprechende Maßnahmen ergreifen können.

Weitere Informationen finden Sie unter [Einführung in Azure Defender für relationale Open-Source-Datenbanken](defender-for-databases-introduction.md).

### <a name="new-alerts-for-azure-defender-for-resource-manager"></a>Neue Warnungen für Azure Defender für Resource Manager

Wir haben die folgenden Warnungen hinzugefügt, um den Bedrohungsschutz von Azure Defender für Resource Manager zu erweitern:

| Warnung (Warnungstyp)                                                                                                                                                | Beschreibung                                                                                                                                                                                                                                                                                                                                                                                                                              | MITRE-Taktiken | severity |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------:|----------|
|**In Ihrer Azure-Umgebung wurden für eine RBAC-Rolle auf ungewöhnliche Weise Berechtigungen gewährt (Vorschau)**<br>(ARM_AnomalousRBACRoleAssignment)|Azure Defender für Resource Manager hat die Zuweisung einer RBAC-Rolle entdeckt, die gegenüber anderen Zuweisungen derselben zuweisenden Person bzw. für dieselbe zuweisende Person oder auf Ihrem Mandanten ungewöhnlich ist, weil Anomalien in den folgenden Bereichen bestehen: Zuweisungszeitpunkt, Standort der zuweisenden Person, zuweisende Person, Authentifizierungsmethode, zugewiesene Entitäten, verwendete Clientsoftware, Umfang der Zuweisung. Dieser Vorgang wurde ggf. von einem legitimen Benutzer Ihrer Organisation durchgeführt. Unter Umständen kann dies auch ein Hinweis darauf sein, dass ein Konto Ihrer Organisation übernommen wurde und dass der Bedrohungsakteur versucht, Berechtigungen für ein weiteres Konto in seinem Besitz zu gewähren.|Seitwärtsbewegung, Umgehen von Verteidigungsmaßnahmen|Medium|
|**Für Ihr Abonnement wurde auf verdächtige Weise eine benutzerdefinierte privilegierte Rolle erstellt (Vorschau)**<br>(ARM_PrivilegedRoleDefinitionCreation)|Azure Defender für Resource Manager hat in Ihrem Abonnement eine verdächtige Erstellung der Definition einer benutzerdefinierten privilegierten Rolle erkannt. Dieser Vorgang wurde ggf. von einem legitimen Benutzer Ihrer Organisation durchgeführt. Unter Umständen kann dies auch ein Hinweis darauf sein, dass ein Konto in Ihrer Organisation übernommen wurde und der Bedrohungsakteur versucht, eine privilegierte Rolle für die zukünftige Verwendung zu erstellen, um eine Erkennung zu vermeiden.|Seitwärtsbewegung, Umgehen von Verteidigungsmaßnahmen|Niedrig|
|**Azure Resource Manager-Vorgang von einer verdächtigen IP-Adresse (Vorschau)**<br>(ARM_OperationFromSuspiciousIP)|Azure Defender für Resource Manager hat einen Vorgang über eine IP-Adresse erkannt, die in Threat Intelligence-Feeds als verdächtig gekennzeichnet wurde.|Ausführung|Medium|
|**Azure Resource Manager-Vorgang von einer verdächtigen Proxy-IP-Adresse (Vorschau)**<br>(ARM_OperationFromSuspiciousProxyIP)|Azure Defender für Resource Manager hat einen Ressourcenverwaltungsvorgang von einer IP-Adresse erkannt, die Proxydiensten zugeordnet ist, z. B. TOR. Dieses Verhalten kann legitim sein, aber es wird häufig mit bösartigen Aktivitäten in Verbindung gebracht, wenn Bedrohungsakteure versuchen, ihre Quell-IP-Adresse zu verbergen.|Umgehen von Verteidigungsmaßnahmen|Medium|
||||

Weitere Informationen finden Sie unter
- [Einführung in Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md)
- [Reagieren auf Warnungen von Azure Defender für Resource Manager](defender-for-resource-manager-usage.md)
- [Warnungen für Resource Manager](alerts-reference.md#alerts-resourcemanager)


### <a name="cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview"></a>CI/CD-Sicherheitsrisikoüberprüfung von Containerimages mit GitHub-Workflows und Azure Defender (Vorschau)

Azure Defender für Containerregistrierungen ermöglicht DevSecOps-Teams jetzt Einblicke in GitHub Actions-Workflows.

Das neue Feature für Sicherheitsrisikoüberprüfungen für Containerimages mit Nutzung von Trivy unterstützt Ihre Entwickler bei der Überprüfung auf häufige Sicherheitsrisiken in Containerimages, *bevor* für Images der Pushvorgang in Containerregistrierungen erfolgt.

Die Berichte zu Containerüberprüfungen sind in Azure Security Center zusammengefasst und ermöglichen Sicherheitsteams einen besseren Einblick und ein tieferes Verständnis der Ursache für anfällige Containerimages und die zugehörigen ursprünglichen Workflows und Repositorys.

Weitere Informationen finden Sie unter [Identifizieren von anfälligen Containerimages in Ihren CI/CD-Workflows](defender-for-container-registries-cicd.md).

### <a name="more-resource-graph-queries-available-for-some-recommendations"></a>Verfügbarkeit weiterer Resource Graph-Abfragen für einige Empfehlungen

Für alle Empfehlungen von Security Center gibt es die Option, die Informationen zum Status der betroffenen Ressourcen mit Azure Resource Graph über **Abfrage öffnen** anzuzeigen. Ausführliche Informationen zu diesem leistungsstarken Feature finden Sie unter [Überprüfen von Empfehlungsdaten im Azure Resource Graph-Explorer (ARG)](security-center-recommendations.md#review-recommendation-data-in-azure-resource-graph-explorer-arg).

Security Center umfasst integrierte Überprüfungen auf Sicherheitsrisiken, um Ihre VMs, SQL Server-Instanzen und zugehörigen Hosts sowie die Containerregistrierungen auf Sicherheitsrisiken zu überprüfen. Die Ergebnisse werden als Empfehlungen zurückgegeben, wobei alle Einzelergebnisse für die einzelnen Ressourcentypen jeweils in einer Ansicht zusammengefasst sind. Die Empfehlungen lauten wie folgt:

- Sicherheitsrisiken in Azure Container Registry-Images müssen behoben werden (unterstützt von Qualys).
- Sicherheitsrisiken für VMs müssen behoben werden
-  Bei SQL-Datenbanken sollten gefundene Sicherheitsrisiken behoben werden.
-  Bei SQL Servern auf Computern sollten gefundene Sicherheitsrisiken behoben werden.

Dank dieser Änderung können Sie die Schaltfläche **Abfrage öffnen** verwenden, um auch die Abfrage mit den Sicherheitsergebnissen zu öffnen.

:::image type="content" source="media/release-notes/open-query-menu-security-findings.png" alt-text="Die Schaltfläche &quot;Abfrage öffnen&quot; bietet nun Optionen für eine tiefergehende Abfrage, bei der die Sicherheitsergebnisse für Empfehlungen im Zusammenhang mit Schwachstellenscannern angezeigt werden.":::

Die Schaltfläche **Abfrage öffnen** bietet zusätzliche Optionen für einige andere Empfehlungen, sofern relevant.

Weitere Informationen zu den Sicherheitsrisikoüberprüfungen von Security Center:

- [Der in Azure Defender integrierte Qualys-Schwachstellen-Scanner für Azure- und Hybrid-Maschinen](deploy-vulnerability-assessment-vm.md)
- [Integrierte Azure Defender-Überprüfung zur Sicherheitsrisikobewertung für SQL Server-Instanzen](defender-for-sql-on-machines-vulnerability-assessment.md)
- [Integrierte Azure Defender-Überprüfung zur Sicherheitsrisikobewertung für Containerregistrierungen](defender-for-container-registries-usage.md)

### <a name="sql-data-classification-recommendation-severity-changed"></a>Schweregrad der Empfehlung zur SQL-Datenklassifizierung geändert

Der Schweregrad der Empfehlung **Vertrauliche Daten in Ihren SQL-Datenbanken sollten klassifiziert werden** wurde von **Hoch** in **Niedrig** geändert.

Dies ist Teil der unter [Verbesserungen bei der Empfehlung zur Klassifizierung vertraulicher Daten in den SQL Datenbanken](upcoming-changes.md#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases) angekündigten laufenden Änderungen an dieser Empfehlung.

### <a name="new-recommendations-to-enable-trusted-launch-capabilities-in-preview"></a>Neue Empfehlungen zur Aktivierung von Funktionen für den vertrauenswürdigen Start (Vorschau)

Azure bietet den vertrauenswürdigen Start als nahtlose Möglichkeit zur Verbesserung der Sicherheit von VMs der [Generation 2](../virtual-machines/generation-2.md) an. Der vertrauenswürdige Start bietet Schutz vor komplexen und permanenten Angriffstechniken. Der vertrauenswürdige Start besteht aus mehreren koordinierten Infrastrukturtechnologien, die unabhängig voneinander aktiviert werden können. Jede Technologie bietet eine eigene Schutzschicht gegen komplexe Bedrohungen. Weitere Informationen finden Sie unter [Vertrauenswürdiger Start für Azure-VMs](../virtual-machines/trusted-launch.md).

> [!IMPORTANT]
> Der vertrauenswürdige Start erfordert die Erstellung neuer VMs. Sie können den vertrauenswürdigen Start nicht für vorhandene VMs aktivieren, die ursprünglich ohne vertrauenswürdigen Start erstellt wurden.
> 
> Der vertrauenswürdige Start befindet sich derzeit in der öffentlichen Vorschau. Die Vorschau wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

Mit der Empfehlung **Für unterstützte VMs muss ein virtuelles TPM-Gerät aktiviert werden** von Security Center wird sichergestellt, dass für Ihre Azure-VMs ein virtuelles TPM-Gerät genutzt wird. Diese virtualisierte Version einer Trusted Platform Module-Hardwareeinheit ermöglicht die Nachweiserbringung, indem die gesamte Startkette Ihrer VM (UEFI, Betriebssystem, System und Treiber) gemessen wird.

Wenn das virtuelle TPM-Gerät aktiviert ist, kann der sichere Start von der **Erweiterung für den Gastnachweis** per Remotezugriff überprüft werden. Mit den folgenden Empfehlungen wird sichergestellt, dass diese Erweiterung bereitgestellt wird:

- **Für unterstützte Windows-VMs muss der sichere Start aktiviert werden**
- **Für unterstützte Windows-VMs muss die Erweiterung für den Gastnachweis installiert sein**
- **Für unterstützte Windows-VM-Skalierungsgruppen muss die Erweiterung für den Gastnachweis installiert sein**
- **Für unterstützte Linux-VMs muss die Erweiterung für den Gastnachweis installiert sein**
- **Für unterstützte Linux-VM-Skalierungsgruppen muss die Erweiterung für den Gastnachweis installiert sein**

Weitere Informationen finden Sie unter [Vertrauenswürdiger Start für Azure-VMs](../virtual-machines/trusted-launch.md). 

### <a name="new-recommendations-for-hardening-kubernetes-clusters-in-preview"></a>Neue Empfehlungen für die Härtung von Kubernetes-Clustern (Vorschau)

Mit den folgenden Empfehlungen können Sie die weitere Härtung Ihrer Kubernetes-Cluster durchführen.

- **Kubernetes-Cluster dürfen nicht den Standardnamespace verwenden**: Verhindern Sie die Verwendung des Standardnamespace in Kubernetes-Clustern, um ConfigMap-, Pod-, Geheimnis-, Dienst- und Dienstkontoressourcen vor einem nicht autorisierten Zugriff zu schützen.
- **Kubernetes-Cluster müssen die automatische Bereitstellung von API-Anmeldeinformationen deaktivieren**: Deaktivieren Sie die automatische Bereitstellung von API-Anmeldeinformationen, um für eine potenziell kompromittierte Podressource die Ausführung von API-Befehlen für Kubernetes-Cluster zu verhindern.
- **Kubernetes-Cluster sollten keine CAPSYSADMIN-Sicherheitsfunktionen gewähren**

Informationen dazu, wie Sie Ihre containerisierten Umgebungen mit Security Center schützen können, finden Sie unter [Containersicherheit in Security Center](container-security.md).

### <a name="assessments-api-expanded-with-two-new-fields"></a>Die Bewertungs-API wurde um zwei neue Felder erweitert.

Wir haben die folgenden beiden Felder zur [Bewertungs-REST-API](/rest/api/securitycenter/assessments) hinzugefügt:

- **FirstEvaluationDate**: Der Zeitpunkt, zu dem die Empfehlung erstellt und zum ersten Mal ausgewertet wurde. Wird als UTC-Zeit im ISO 8601-Format zurückgegeben.
- **StatusChangeDate**: Der Zeitpunkt, zu dem sich der Status der Empfehlung zuletzt geändert hat. Wird als UTC-Zeit im ISO 8601-Format zurückgegeben.

Der anfängliche Standardwert für diese Felder ist für alle Empfehlungen `2021-03-14T00:00:00+0000000Z`.

Um auf diese Informationen zuzugreifen, können Sie eine der Methoden in der folgenden Tabelle verwenden.

| Tool                 | Details                                                                                                                                                                |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| REST-API-Aufruf        | GET https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/providers/Microsoft.Security/assessments?api-version=2019-01-01-preview& $expand=statusEvaluationDates |
| Azure Resource Graph | `securityresources`<br>`where type == "microsoft.security/assessments"`                                                                                                |
| Fortlaufendem Export    | Die beiden dedizierten Felder sind für die Log Analytics-Arbeitsbereichsdaten verfügbar.                                                                                            |
| [CSV-Export](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations) | Die beiden Felder sind in den CSV-Dateien enthalten.                                                        |
|                      |                                                                                                                                                                        |


Erfahren Sie mehr zur [Bewertungs-REST-API](/rest/api/securitycenter/assessments).


### <a name="asset-inventory-gets-a-cloud-environment-filter"></a>Cloudumgebungsfilter für Ressourcenbestand

Die Security Center-Seite für den Ressourcenbestand bietet einige Filter, mit denen die Liste mit den angezeigten Ressourcen schnell eingegrenzt werden kann. Weitere Informationen finden Sie unter [Untersuchen und Verwalten Ihrer Ressourcen mit dem Ressourcenbestand und Verwaltungstools](asset-inventory.md).

Ein neuer Filter bietet die Möglichkeit, die Liste gemäß den Cloudkonten einzugrenzen, mit denen Sie über die Multi-Cloud-Features von Security Center eine Verbindung hergestellt haben:

:::image type="content" source="media/asset-inventory/filter-environment.png" alt-text="Umgebungsfilter für Bestand":::

Weitere Informationen zu den Multi-Cloud-Features:

- [Verbinden Ihrer AWS-Konten mit Azure Security Center](quickstart-onboard-aws.md)
- [Herstellen einer Verbindung zwischen Ihren GCP-Konten und Azure Security Center](quickstart-onboard-gcp.md)
