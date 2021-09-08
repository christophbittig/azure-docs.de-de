---
title: Versionshinweise für Azure Security Center
description: Enthält eine Beschreibung der Neuerungen und Änderungen in Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 08/15/2021
ms.author: memildin
ms.openlocfilehash: 9ebe158f1a047006b75eb519864152fc4bb396e8
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122419528"
---
# <a name="whats-new-in-azure-security-center"></a>Neuerungen in Azure Security Center

Azure Security Center befindet sich in der aktiven Entwicklung und wird ständig verbessert. Damit Sie bezüglich der aktuellen Entwicklungen immer auf dem neuesten Stand sind, enthält diese Seite Informationen zu neuen Features, Fehlerbehebungen und veralteten Funktionen.

Es ist ratsam, diese Seite regelmäßig zu besuchen, da sie immer wieder aktualisiert wird. 

Weitere Informationen zu den *geplanten* Änderungen, die demnächst im Security Center anstehen, finden Sie unter [Wichtige bevorstehende Änderungen in Azure Security Center](upcoming-changes.md). 

> [!TIP]
> Elemente, die älter als sechs Monate sind, finden Sie im [Archiv zu den Neuerungen in Azure Security Center](release-notes-archive.md).

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

Er wurden zwei **Vorschau**-Empfehlungen für die Bereitstellung und Wartung der Endpoint Protection-Lösungen auf Ihren Computern hinzugefügt. Beide Empfehlungen umfassen Unterstützung für virtuelle Azure-Computer und Computer, die mit Azure Arc verbunden sind.

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

Im März haben wir die integrierte Azure Monitor-Arbeitsmappenerfahrung in Security Center angekündigt (siehe [Integration von Azure Monitor-Arbeitsmappen in Security Center und Bereitstellung von drei Vorlagen](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)).

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

| Warnung (Warnungstyp)                                                                 | Beschreibung                                                                                                                                                                                                                                                                                                                                                      | MITRE-Taktik | Schweregrad |
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

Azure Defender für Kubernetes wurde kürzlich erweitert, um Kubernetes-Cluster zu schützen, die lokal und in Umgebungen mit mehreren Clouds gehostet werden. Weitere Informationen finden Sie unter [Verwenden von Azure Defender für Kubernetes zum Schutz von Hybrid- und Multi-Cloud-Bereitstellungen von Kubernetes (Vorschau)](release-notes.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview).

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

| Warnung (Warnungstyp)                                                                                                                                                | Beschreibung                                                                                                                                                                                                                                                                                                                                                                                                                              | MITRE-Taktiken | Schweregrad |
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

:::image type="content" source="media/release-notes/open-query-menu-security-findings.png" alt-text="Schaltfläche „Abfrage öffnen“ umfasst jetzt auch Optionen für eine tiefer gehende Abfrage, mit der die Sicherheitsergebnisse für Empfehlungen angezeigt werden, die auf Sicherheitsrisikoüberprüfungen basieren.":::

Die Schaltfläche **Abfrage öffnen** umfasst zudem noch weitere Optionen für einige andere Empfehlungen, die ebenfalls relevant sind.

Weitere Informationen zu den Sicherheitsrisikoüberprüfungen von Security Center:

- [Integrierte Azure Defender-Überprüfung zur Sicherheitsrisikobewertung für Azure- und Hybridcomputer](deploy-vulnerability-assessment-vm.md)
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


## <a name="april-2021"></a>April 2021

Zu den Updates im April gehören:
- [Aktualisierte Seite „Ressourcenintegrität“ (Vorschau)](#refreshed-resource-health-page-in-preview)
- [Containerregistrierungsimages, die vor Kurzem gepullt wurden, werden jetzt wöchentlich erneut überprüft (veröffentlicht zur allgemeinen Verfügbarkeit)](#container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga)
- [Verwenden von Azure Defender für Kubernetes zum Schutz von Hybrid- und Multi-Cloud-Bereitstellungen von Kubernetes (Vorschau)](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)
- [Microsoft Defender für Endpunkt-Integration mit Azure Defender unterstützt jetzt Windows Server 2019 und Windows 10 Virtual Desktop (WVD) (veröffentlicht zur allgemeinen Verfügbarkeit)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga)
- [Empfehlungen zum Aktivieren von Azure Defender für DNS und Resource Manager (Vorschau)](#recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview)
- [Drei Standards zur Einhaltung gesetzlicher Bestimmungen wurden hinzugefügt: „Azure CIS 1.3.0“, „CMMC Level 3“ und „Durch New Zealand ISM eingeschränkt“.](#three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted)
- [Vier neue Empfehlungen im Zusammenhang mit der Gastkonfiguration (Vorschau)](#four-new-recommendations-related-to-guest-configuration-in-preview)
- [CMK-Empfehlungen wurden in bewährte Methoden für die Sicherheitskontrolle verschoben](#cmk-recommendations-moved-to-best-practices-security-control)
- [Elf Azure Defender-Warnungen als veraltet eingestuft](#11-azure-defender-alerts-deprecated)
- [Zwei Empfehlungen der Sicherheitskontrolle „Systemupdates anwenden“ wurden als veraltet eingestuft](#two-recommendations-from-apply-system-updates-security-control-were-deprecated)
- [Kachel für Azure Defender für SQL auf Computern vom Azure Defender-Dashboard entfernt](#azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard)
- [21 Empfehlungen zwischen Sicherheitskontrollen verschoben](#21-recommendations-moved-between-security-controls)

### <a name="refreshed-resource-health-page-in-preview"></a>Aktualisierte Seite „Ressourcenintegrität“ (Vorschau)

Die Ressourcenintegrität von Security Center wurde erweitert und verbessert, um eine Momentaufnahmenansicht der Gesamtintegrität einer einzelnen Ressource zu erhalten. 

Sie können ausführliche Informationen zur Ressource und sich alle Empfehlungen im Zusammenhang mit der Ressource ansehen. Bei Verwendung von [Azure Defender](azure-defender.md) werden außerdem auch ausstehende Sicherheitswarnungen für die jeweilige Ressource angezeigt.

Wählen Sie auf der Seite [Ressourcenbestand](asset-inventory.md) eine beliebige Ressource aus, um die Seite „Ressourcenintegrität“ für eine Ressource zu öffnen.

Diese Vorschauseite auf Portalseiten im Security Center zeigt:

1. **Ressourceninformationen:** Zugehörige Ressourcengruppe, zugehöriges Abonnement, geografischer Standort und Ähnliches.
1. **Angewendetes Sicherheitsfeature:** Gibt an, ob Azure Defender für die Ressource aktiviert ist.
1. **Anzahl ausstehenden Empfehlungen und Warnungen:** Die Anzahl ausstehender Sicherheitsempfehlungen und Azure Defender-Warnungen.
1. **Umsetzbare Empfehlungen und handlungsrelevante Warnungen:** Auf zwei Registerkarten werden die Empfehlungen und Warnungen für die Ressource aufgeführt.

:::image type="content" source="media/investigate-resource-health/resource-health-page-virtual-machine.gif" alt-text="Ressourcenintegritätsseite von Azure Security Center mit den Integritätsinformationen für einen virtuellen Computer":::

Weitere Informationen finden Sie unter [Tutorial: Untersuchen der Integrität Ihrer Ressourcen](investigate-resource-health.md).


### <a name="container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga"></a>Containerregistrierungsimages, die vor Kurzem gepullt wurden, werden jetzt wöchentlich erneut überprüft (veröffentlicht zur allgemeinen Verfügbarkeit)

Azure Defender für Containerregistrierungen enthält eine integrierte Überprüfung auf Sicherheitsrisiken. Bei dieser Überprüfung werden alle Images, die Sie in Ihre Registrierung pushen oder per Pullvorgang innerhalb der letzten 30 Tage abgerufen haben, sofort überprüft.

Jeden Tag werden neue Sicherheitsrisiken entdeckt. Nach diesem Update werden Containerimages, die innerhalb der letzten 30 Tage per Pullvorgang aus Ihren Registrierungen abgerufen wurden, wöchentlich **erneut überprüft**. So wird sichergestellt, dass neu ermittelte Sicherheitsrisiken in Ihren Images erkannt werden.

Da die Kosten für die Überprüfung pro Image berechnet werden, fallen für diese erneuten Überprüfungen keine zusätzlichen Kosten an.

Weitere Informationen finden Sie unter [Verwenden von Azure Defender für Containerregistrierungen zum Überprüfen Ihrer Images auf Sicherheitsrisiken](defender-for-container-registries-usage.md).


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview"></a>Verwenden von Azure Defender für Kubernetes zum Schutz von Hybrid- und Multi-Cloud-Bereitstellungen von Kubernetes (Vorschau)

Azure Defender für Kubernetes erweitert seine Funktionen zum Schutz vor Bedrohungen, um Ihre Cluster unabhängig von Ihrem Bereitstellungsort zu schützen. Dies wurde durch die Integration von [Kubernetes mit Azure Arc-Aktivierung](../azure-arc/kubernetes/overview.md) und der neuen [Erweiterungsfunktionen](../azure-arc/kubernetes/extensions.md) ermöglicht. 

Wenn Sie Azure Arc in ihren Nicht-Azure Kubernetes-Clustern aktiviert haben, bietet eine neue Empfehlung von Azure Security Center an, die Azure Defender-Erweiterung mit nur wenigen Klicks für Sie bereitzustellen.

Verwenden Sie die Empfehlung (**für Kubernetes-Cluster mit Azure Arc-Aktivierung muss die Azure Defender-Erweiterung installiert sein**) und die Erweiterung, um Kubernetes-Clustern zu schützen, die in anderen Cloudanbietern, jedoch nicht in ihren verwalteten Kubernetes-Diensten bereitgestellt werden.

Diese Integration zwischen Azure Security Center, Azure Defender und Kubernetes mit Azure Arc-Aktivierung ermöglicht:

- Einfache Bereitstellung der Azure Defender-Erweiterung für ungeschützte Kubernetes-Cluster mit Azure Arc-Aktivierung (manuell und skalierbar)
- Überwachung der Azure Defender-Erweiterung und ihres Bereitstellungsstatus über das Azure Arc-Portal
- Sicherheitsempfehlungen von Security Center werden auf der neuen Seite "Sicherheit" des Azure Arc-Portals angezeigt
- Von Azure Defender erkannte Sicherheitsbedrohungen werden auf der neuen Seite "Sicherheit" des Azure Arc-Portals angezeigt
- Kubernetes-Cluster mit Azure Arc-Aktivierung werden in die Azure Security Center-Plattform und Benutzeroberfläche integriert

Weitere Informationen finden Sie unter [Verwenden von Azure Defender für Kubernetes mit Ihren lokalen und Multi-Cloud Kubernetes-Clustern](defender-for-kubernetes-azure-arc.md).

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Security Center-Empfehlung zur Bereitstellung der Azure Defender-Erweiterung für Azure Arc-fähige Kubernetes-Cluster" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga"></a>Microsoft Defender für Endpunkt-Integration mit Azure Defender unterstützt jetzt Windows Server 2019 und Windows 10 Virtual Desktop (WVD) (veröffentlicht zur allgemeinen Verfügbarkeit)

Microsoft Defender für den Endpunkt ist eine ganzheitliche, cloudbasierte Lösung für die Endpunktsicherheit. Sie ermöglicht die risikobasierte Verwaltung und Bewertung von Sicherheitsrisiken sowie Endpunkterkennung und -reaktion (Endpoint Detection and Response, EDR). Eine vollständige Liste mit den Vorteilen der gemeinsamen Nutzung von Defender für Endpunkt und Azure Security Center finden Sie unter [Schützen Sie Ihre Endpunkte mit der in Security Center integrierten EDR-Lösung: Microsoft Defender für Endpunkt](security-center-wdatp.md).

Wenn Sie Azure Defender für Server auf einem Windows-Server aktivieren, ist im Plan eine Lizenz für Defender für Endpunkt enthalten. Falls Sie Azure Defender für Server bereits aktiviert haben und unter Ihrem Abonnement Windows 2019-Server nutzen, wird Defender für Endpunkt mit diesem Update darauf automatisch bereitgestellt. Es ist keine manuelle Aktion erforderlich. 

Die Unterstützung wurde nun auf Windows Server 2019 und [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md) erweitert.

> [!NOTE]
> Stellen Sie beim Aktivieren von Defender für Endpunkt auf einem Windows Server 2019-Computer sicher, dass die unter [Aktivieren der Integration von Microsoft Defender für Endpunkt](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration) beschriebenen Voraussetzungen erfüllt sind.


### <a name="recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview"></a>Empfehlungen zum Aktivieren von Azure Defender für DNS und Resource Manager (Vorschau)

Zwei neue Empfehlungen wurden hinzugefügt, um den Prozess zum Aktivieren von [Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md) und [Azure Defender für DNS](defender-for-dns-introduction.md) zu vereinfachen:

- **Azure Defender für Resource Manager muss aktiviert sein**: Defender für Resource Manager überwacht automatisch die Ressourcenverwaltungsvorgänge in Ihrer Organisation. Azure Defender erkennt Bedrohungen und warnt Sie bei verdächtigen Aktivitäten.
- **Azure Defender für DNS muss aktiviert sein**: Defender für DNS bietet eine zusätzliche Schutzebene für Ihre Cloudressourcen, indem alle DNS-Abfragen aus Ihren Azure-Ressourcen fortlaufend überwacht werden. Azure Defender warnt Sie bei verdächtigen Aktivitäten auf der DNS-Ebene.

Bei Aktivierung dieser Azure Defender-Pläne fallen Gebühren an. Weitere Informationen zu den Preisen pro Region finden Sie in der Security Center-Preisübersicht unter https://aka.ms/pricing-security-center.

> [!TIP]
> Empfehlungen der Vorschau versetzen keine Ressourcen in einen fehlerhaften Zustand, und sie werden nicht in die Berechnungen Ihrer Sicherheitsbewertung einbezogen. Setzen Sie sie trotzdem um, wann immer möglich, damit sie nach Ablauf des Vorschauzeitraums zu Ihrer Bewertung beitragen. Informationen zum Umgang mit diesen Empfehlungen finden Sie unter [Umsetzen von Empfehlungen in Azure Security Center](security-center-remediate-recommendations.md).


### <a name="three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted"></a>Drei Standards zur Einhaltung gesetzlicher Bestimmungen wurden hinzugefügt: „Azure CIS 1.3.0“, „CMMC Level 3“ und „Durch New Zealand ISM eingeschränkt“.

Wir haben drei Standards für die Verwendung mit Azure Security Center hinzugefügt. Mithilfe des Dashboards zur Einhaltung gesetzlicher Bestimmungen können Sie jetzt Ihre Konformität mit Folgendem nachverfolgen:

- [CIS Microsoft Azure Foundations Benchmark 1.3.0](../governance/policy/samples/cis-azure-1-3-0.md)
- [CMMC Level 3](../governance/policy/samples/cmmc-l3.md)
- [Durch New Zealand ISM eingeschränkt](../governance/policy/samples/new-zealand-ism.md)

Sie können diese Standards Ihren Abonnements zuweisen, wie unter [Anpassen der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md) beschrieben.

:::image type="content" source="media/release-notes/additional-regulatory-compliance-standards.png" alt-text="Drei Standards für die Verwendung mit dem Azure Security Center-Dashboard zur Einhaltung gesetzlicher Bestimmungen wurden hinzugefügt." lightbox="media/release-notes/additional-regulatory-compliance-standards.png":::

Weitere Informationen finden Sie hier:
- [Anpassen der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md)
- [Tutorial: Verbessern der Einhaltung gesetzlicher Vorschriften](security-center-compliance-dashboard.md)
- [Häufig gestellte Fragen: Dashboard für die Einhaltung gesetzlicher Bestimmungen](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

### <a name="four-new-recommendations-related-to-guest-configuration-in-preview"></a>Vier neue Empfehlungen im Zusammenhang mit der Gastkonfiguration (Vorschau)

Die [Erweiterung für Gastkonfigurationen](../governance/policy/concepts/guest-configuration.md) von Azure sendet Meldungen an Security Center, um sicherzustellen, dass die In-Guest-Einstellungen Ihrer virtuellen Computer festgeschrieben werden. Die Erweiterung ist für Computer mit Arc-Unterstützung nicht erforderlich, da sie im Arc Connected Machine-Agenten enthalten ist. Die Erweiterung erfordert eine vom System verwaltete Identität auf dem Computer.

Wir haben vier neue Empfehlungen zum Security Center hinzugefügt, damit Sie diese Erweiterung optimal nutzen können.

- In zwei Empfehlungen werden Sie aufgefordert, die Erweiterung und die erforderliche vom System verwaltete Identität zu installieren:
    - **Die Erweiterung „Gastkonfiguration“ muss auf Ihren Computern installiert sein.**
    - **VM-Erweiterung „Gastkonfiguration“ muss mit einer systemseitig zugewiesenen verwalteten Identität bereitgestellt werden**

- Wenn die Erweiterung installiert ist und ausgeführt wird, beginnt Sie mit der Überprüfung ihrer Computer, und Sie werden aufgefordert, Einstellungen wie die Konfiguration der Betriebssystem- und Umgebungseinstellungen festzuschreiben. Mit diesen beiden Empfehlungen werden Sie dazu aufgefordert, Ihre Windows-und Linux-Computer wie beschrieben festzuschreiben:
    - **Windows Defender Exploit Guard muss auf Ihren Computern aktiviert sein**
    - **Für die Authentifizierung bei Linux-Computern muss ein SSH-Schlüssel erforderlich sein**

Weitere Informationen finden Sie in [Grundlegendes zur Gastkonfiguration von Azure Policy](../governance/policy/concepts/guest-configuration.md).

### <a name="cmk-recommendations-moved-to-best-practices-security-control"></a>CMK-Empfehlungen wurden in bewährte Methoden für die Sicherheitskontrolle verschoben

Das Sicherheitsprogramm jeder Organisation enthält Anforderungen an die Datenverschlüsselung. Die Daten von Azure-Kunden werden im Ruhezustand standardmäßig mit dienstseitig verwalteten Schlüsseln verschlüsselt. Kundenseitig verwaltete Schlüssel (CMK) sind aber häufig zur Einhaltung gesetzlicher Bestimmungen erforderlich. Mit CMKs können Sie Ihre Daten mit einem [Azure Key Vault](../key-vault/general/overview.md)-Schlüssel verschlüsseln, der von Ihnen erstellt wird und sich in Ihrem Besitz befindet. So haben Sie die volle Kontrolle über den Schlüssellebenszyklus, einschließlich der Rotation und Verwaltung, und sind dafür entsprechend verantwortlich.

Bei Sicherheitskontrollen von Azure Security Center handelt es sich um logische Gruppen mit verwandten Sicherheitsempfehlungen, die Ihren anfälligen Angriffsflächen entsprechen. Jede Sicherheitskontrolle verfügt über eine maximale Anzahl von Punkten, die Ihrer Sicherheitsbewertung hinzugefügt wird, wenn Sie alle in der Sicherheitskontrolle aufgeführten Empfehlungen für Ihre gesamten Ressourcen umsetzen. Die Sicherheitskontrolle **Best Practices für die Sicherheit implementieren** hat einen Wert von null Punkten. Daher wirken sich die Empfehlungen dieser Sicherheitskontrolle nicht auf Ihre Sicherheitsbewertung aus.

Die unten angegebenen Empfehlungen werden in die Sicherheitskontrolle **Best Practices für die Sicherheit implementieren** verschoben, um besser zu verdeutlichen, dass sie optional sind. Durch die Verschiebung wird sichergestellt, dass sich diese Empfehlungen in der Sicherheitskontrolle befinden, die zur Erreichung des Ziels am besten geeignet ist.

- Azure Cosmos DB-Konten sollten kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden.
- Azure Machine Learning-Arbeitsbereiche müssen mit einem kundenseitig verwalteten Schlüssel (CMK) verschlüsselt werden
- Cognitive Services-Konten müssen eine Datenverschlüsselung mit einem kundenseitig verwalteten Schlüssel (CMK) aktivieren
- Containerregistrierungen müssen mit einem kundenseitig verwalteten Schlüssel (CMK) verschlüsselt werden
- SQL Managed Instance-Instanzen müssen kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden
- SQL Server-Instanzen müssen kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden
- Speicherkonten sollten einen kundenseitig verwalteten Schlüssel (Customer-Managed Key, CMK) zur Verschlüsselung verwenden.

Informationen dazu, welche Empfehlungen in den einzelnen Sicherheitssteuerungen enthalten sind, finden Sie unter [Sicherheitskontrollen und deren Empfehlungen](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="11-azure-defender-alerts-deprecated"></a>Elf Azure Defender-Warnungen als veraltet eingestuft

Die 11 nachfolgend aufgeführten Azure Defender-Warnungen wurden als veraltet eingestuft.

- Neue Warnungen werden diese beiden Warnungen ersetzt und für eine bessere Abdeckung sorgen:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | PREVIEW – MicroBurst toolkit „Get-AzureDomainInfo“ function run detected (VORSCHAU – Ausführung der MicroBurst-Toolkitfunktion „Get-AzureDomainInfo“ erkannt) |
    | ARM_MicroBurstRunbook    | PREVIEW – MicroBurst toolkit „Get-AzurePasswords“ function run detected (VORSCHAU – Ausführung der MicroBurst-Funktion „Get-AzurePasswords“ erkannt)  |
    |                          |                                                                          |

- Die folgenden neun Warnungen beziehen sich auf einen Azure Active Directory Identity Protection-Connector (IPC), der bereits veraltet ist:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Ungewöhnliche Anmeldeeigenschaften |
    | AnonymousLogin      | Anonyme IP-Adresse          |
    | InfectedDeviceLogin | Mit Schadsoftware verknüpfte IP-Adresse     |
    | ImpossibleTravel    | Ungewöhnlicher Ortswechsel               |
    | MaliciousIP         | Schädliche IP-Adresse          |
    | LeakedCredentials   | Kompromittierte Anmeldeinformationen            |
    | PasswordSpray       | Kennwortspray                |
    | LeakedCredentials   | Azure AD Threat Intelligence  |
    | AADAI               | Azure AD-KI                   |
    |                     |                               |
 
    > [!TIP]
    > Bei diesen neun IPC-Warnungen hat es sich niemals um Security Center-Warnungen gehandelt. Sie sind Teil des Azure Active Directory (AAD) Identity Protection Connector (IPC), der Sie an Security Center gesendet hat. In den letzten zwei Jahren haben nur solche Kunden diese Warnungen gesehen, die den Export (vom Connector zu ASC) im Jahr 2019 oder früher konfiguriert haben. AAD IPC hat Sie weiterhin in den eigenen Warnsystemen angezeigt und waren weiterhin in Azure Sentinel verfügbar. Die einzige Veränderung besteht darin, dass sie nicht mehr in Security Center angezeigt werden.

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>Zwei Empfehlungen der Sicherheitskontrolle „Systemupdates anwenden“ wurden als veraltet eingestuft 

Die folgenden beiden Empfehlungen wurden als veraltet eingestuft und die Änderungen können geringfügige Auswirkungen auf Ihre Sicherheitsbewertung haben:

- **Ihre Computer sollten zur Anwendung von Systemupdates neu gestartet werden**
- **Der Überwachungs-Agent sollte auf Ihren Computern installiert werden.** Diese Empfehlung gilt nur für lokale Computer. Ein Teil der Logik wird in eine andere Empfehlung übertragen: **Log Analytics-Agent-Integritätsprobleme müssen auf Computern gelöst werden**

Wir empfehlen Ihnen, Ihre Konfigurationen für den fortlaufenden Export und die Workflowautomatisierung zu überprüfen, um zu ermitteln, ob diese Empfehlungen darin enthalten sind. Darüber hinaus sollten Sie alle Dashboards oder anderen Überwachungstools, für die diese ggf. genutzt werden, entsprechend aktualisieren.

Weitere Informationen zu diesen Empfehlungen finden Sie auf der [Referenzseite zu Sicherheitsempfehlungen](recommendations-reference.md).

### <a name="azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard"></a>Kachel für Azure Defender für SQL auf Computern vom Azure Defender-Dashboard entfernt

Der Abdeckungsbereich des Azure Defender-Dashboards enthält Kacheln für die relevanten Azure Defender-Pläne für Ihre Umgebung. Aufgrund eines Problems mit der Meldung der Anzahl geschützter und nicht geschützter Ressourcen haben wir uns entschieden, den Ressourcenabdeckungsstatus für **Azure Defender für SQL auf Computern** vorübergehend zu entfernen, bis das Problem behoben ist.


### <a name="21-recommendations-moved-between-security-controls"></a>21 Empfehlungen zwischen Sicherheitskontrollen verschoben 

Die folgenden Empfehlungen wurden in andere Sicherheitskontrollen verschoben. Bei Sicherheitskontrollen handelt es sich um logische Gruppen verwandter Sicherheitsempfehlungen, die anfällige Angriffsflächen widerspiegeln. Durch die Verschiebung wird sichergestellt, dass sich jede dieser Empfehlungen in der am besten geeigneten Kontrolle befindet, um das jeweilige Ziel zu erreichen.

Informationen dazu, welche Empfehlungen in den einzelnen Sicherheitssteuerungen enthalten sind, finden Sie unter [Sicherheitskontrollen und deren Empfehlungen](secure-score-security-controls.md#security-controls-and-their-recommendations).

|Empfehlung |Änderung und Auswirkung  |
|---------|---------|
|Für Ihre SQL Server-Instanzen muss eine Sicherheitsrisikobewertung aktiviert sein.<br>Bewertung von Sicherheitsrisiken für verwaltete SQL-Instanzen aktivieren<br>Sicherheitsrisiken in Ihren SQL-Datenbanken müssen entschärft werden (neu)<br>Die Sicherheitsrisiken für Ihre SQL-Datenbanken in VMs müssen entschärft werden.     |Verschiebung aus „Sicherheitsrisiken entschärfen“ (Wert: sechs Punkte)<br>in „Sicherheitskonfigurationen bereinigen“ (Wert: vier Punkte).<br>Diese Empfehlungen haben je nach Umgebung eine geringere Auswirkung auf Ihre Bewertung.|
|Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.<br>Automation-Kontovariablen sollten verschlüsselt werden.<br> IoT-Geräte: Überwachter Prozess hat das Senden von Ereignissen beendet<br> IoT-Geräte: Fehler bei Validierung von Baseline von Betriebssystem<br> IoT-Geräte:Upgrade der TLS-Verschlüsselungssammlung erforderlich<br> IoT-Geräte: offene Ports auf Gerät<br> IoT-Geräte: In einer der Ketten wurde eine zu wenig einschränkende Firewallrichtlinie gefunden<br> IoT-Geräte: In der Eingabekette wurde eine zu wenig einschränkende Firewallregel gefunden<br> IoT-Geräte: In der Ausgabekette wurde eine zu wenig einschränkende Firewallregel gefunden<br>In IoT Hub sollten Diagnoseprotokolle aktiviert sein.<br> IoT-Geräte: Agent sendet Nachrichten zu Unterauslastung<br>IoT-Geräte: Die Standard-IP-Filterrichtlinie sollte auf „Verweigern“ festgelegt werden<br>IoT-Geräte: Großer IP-Adressbereich für IP-Filterregel<br>IoT-Geräte: Agent-Nachrichtenintervalle und -größe müssen angepasst werden<br>IoT-Geräte: Identische Anmeldeinformationen für die Authentifizierung<br>IoT-Geräte – Auditd-Prozess hat das Senden von Ereignissen beendet<br>IoT-Geräte: Baselinekonfiguration des Betriebssystems (OS) muss korrigiert werden|Verschiebung in **Bewährte Sicherheitsmethoden implementieren**.<br>Wenn eine Empfehlung in die Sicherheitskontrolle „Bewährte Sicherheitsmethoden implementieren“ (Wert: null Punkte) verschoben wird, wirkt sie sich nicht mehr auf Ihre Sicherheitsbewertung aus.|
|||


## <a name="march-2021"></a>März 2021

Zu den Updates im März gehören:

- [In Security Center integrierte Azure Firewall-Verwaltung](#azure-firewall-management-integrated-into-security-center)
- [SQL-Sicherheitsrisikobewertung enthält jetzt die Option „Regel deaktivieren“ (Vorschauversion)](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [In Security Center integrierte Azure Monitor-Arbeitsmappen und drei verfügbare Vorlagen](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [Dashboard für die Einhaltung gesetzlicher Bestimmungen enthält jetzt Azure-Überwachungsberichte (Vorschauversion)](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [Empfehlungsdaten können mit „In ARG untersuchen“ in Azure Resource Graph angezeigt werden](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [Updates der Richtlinien für die Bereitstellung der Workflowautomatisierung](#updates-to-the-policies-for-deploying-workflow-automation)
- [Von zwei Legacyempfehlungen werden keine Daten mehr direkt in das Azure-Aktivitätsprotokoll geschrieben](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [Verbesserungen der Seite „Empfehlungen“](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>In Security Center integrierte Azure Firewall-Verwaltung

Wenn Sie Azure Security Center öffnen, wird als Erstes die Übersichtsseite angezeigt. 

Dieses interaktive Dashboard ermöglicht eine einheitliche Übersicht über den Sicherheitsstatus Ihrer Hybrid Cloud-Workloads. Darüber hinaus werden darauf Sicherheitswarnungen, Informationen zur Abdeckung und andere Infos angezeigt.

Um Sie beim Anzeigen Ihres Sicherheitsstatus über eine zentrale Benutzeroberfläche zu unterstützen, haben wir u. a. Azure Firewall Manager in dieses Dashboard integriert. Sie können den Firewall-Abdeckungsstatus jetzt für alle Netzwerke überprüfen und über Security Center die Azure Firewall-Richtlinien an einem zentralen Ort verwalten.

Weitere Informationen zu diesem Dashboard finden Sie auf der [Übersichtsseite für Azure Security Center](overview-page.md).

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Übersichtsdashboard von Security Center mit einer Kachel für Azure Firewall":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>SQL-Sicherheitsrisikobewertung enthält jetzt die Option „Regel deaktivieren“ (Vorschauversion)

Security Center enthält eine integrierte Überprüfung auf Sicherheitsrisiken, mit der Sie potenzielle Sicherheitsrisiken für Datenbanken ermitteln, nachverfolgen und beseitigen können. Die Ergebnisse Ihrer Überprüfungen für die Bewertung ermöglichen einen Überblick über den Sicherheitszustand Ihrer SQL-Computer und enthalten Einzelheiten zu allen Sicherheitsergebnissen.

Wenn in Ihrer Organisation eine Suche ignoriert werden muss, anstatt sie zu beheben, können Sie sie optional deaktivieren. Deaktivierte Ergebnisse haben keine Auswirkung auf Ihre Sicherheitsbewertung und erzeugen kein unerwünschtes Rauschen.

Weitere Informationen finden Sie unter [Deaktivieren bestimmter Ergebnisse](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview).



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>In Security Center integrierte Azure Monitor-Arbeitsmappen und drei verfügbare Vorlagen

Bei der Ignite im Frühjahr 2021 haben wir eine integrierte Benutzeroberfläche für Azure Monitor-Arbeitsmappen in Security Center angekündigt.

Sie können diese neue Integration verwenden, um mit der Verwendung der vordefinierten Vorlagen aus dem Security Center-Katalog zu beginnen. Mit den Arbeitsmappenvorlagen können Sie auf dynamische und grafische Berichte zugreifen bzw. diese erstellen, um den Sicherheitsstatus Ihrer Organisation nachzuverfolgen. Darüber hinaus können Sie auch neue Arbeitsmappen, die auf Security Center-Daten basieren, oder alle anderen unterstützten Datentypen erstellen und in kurzer Zeit Community-Arbeitsmappen aus der GitHub-Community für Security Center bereitstellen.

Es sind drei Vorlagenberichte verfügbar:

- **Sicherheitsbewertung im Zeitverlauf**: Nutzen Sie die Nachverfolgung der Bewertungen Ihrer Abonnements und der Änderungen von Empfehlungen für Ihre Ressourcen.
- **Systemupdates**: Zeigen Sie fehlende Systemupdates nach Ressource, Betriebssystem, Schweregrad usw. an.
- **Ergebnisse der Sicherheitsrisikobewertung**: Zeigen Sie die Ergebnisse der Sicherheitsrisikobewertungen Ihrer Azure-Ressourcen an.

Informieren Sie sich über die Nutzung dieser Berichte oder die Erstellung eigener Berichte unter [Erstellen umfassender interaktiver Berichte für Security Center-Daten](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Bericht „Sicherheitsbewertung im Zeitverlauf“":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>Dashboard für die Einhaltung gesetzlicher Bestimmungen enthält jetzt Azure-Überwachungsberichte (Vorschauversion)

In der Symbolleiste im Dashboard für die Einhaltung gesetzlicher Bestimmungen können Sie jetzt Zertifizierungsberichte für Azure und Dynamics herunterladen. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Symbolleiste des Dashboards für die Einhaltung gesetzlicher Bestimmungen":::

Sie können die Registerkarte für die relevanten Berichtstypen (PCI, SOC, ISO usw.) auswählen und Filter verwenden, um nach den benötigten spezifischen Berichten zu suchen.

Informieren Sie sich über das [Verwalten der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="Filtern der Liste mit den verfügbaren Azure-Überwachungsberichten":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>Empfehlungsdaten können mit „In ARG untersuchen“ in Azure Resource Graph angezeigt werden

Auf den Empfehlungsdetailseiten steht die Schaltfläche „In ARG untersuchen“ zur Verfügung. Verwenden Sie diese Schaltfläche, um eine Azure Resource Graph-Abfrage zu öffnen und die Daten der Empfehlung zu erkunden, zu exportieren und weiterzugeben.

Azure Resource Graph (ARG) bietet mit zuverlässigen Funktionen zum Filtern, Gruppieren und Sortieren sofortigen Zugriff auf Ressourceninformationen in Ihren Cloudumgebungen. Es ist eine schnelle und effiziente Möglichkeit, Informationen über Azure-Abonnements programmgesteuert oder aus dem Azure-Portal heraus abzufragen.

Weitere Informationen zu Azure Resource Graph (ARG) finden Sie [hier](../governance/resource-graph/index.yml).

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Erkunden Sie Empfehlungsdaten in Azure Resource Graph.":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>Updates der Richtlinien für die Bereitstellung der Workflowautomatisierung

Die Automatisierung der Prozesse Ihrer Organisation zur Überwachung und Reaktion auf Vorfälle kann die Zeit, die zum Untersuchen von Sicherheitsvorfällen und zur Durchführung entsprechender Gegenmaßnahmen benötigt wird, erheblich verkürzen.

Wir stellen drei Azure Policy-Richtlinien vom Typ „DeployIfNotExist“ bereit, mit denen Verfahren für die Workflowautomatisierung erstellt und konfiguriert werden, damit Sie Ihre Automatisierungen in Ihrer gesamten Organisation bereitstellen können:

|Zielsetzung  |Richtlinie  |Richtlinien-ID  |
|---------|---------|---------|
|Workflowautomatisierung für Sicherheitswarnungen|[Bereitstellen der Workflowautomatisierung für Azure Security Center-Warnungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Workflowautomatisierung für Sicherheitsempfehlungen|[Bereitstellen der Workflowautomatisierung für Azure Security Center-Empfehlungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|Workflowautomatisierung für Änderungen bei der Einhaltung gesetzlicher Bestimmungen|[Workflowautomatisierung für die Einhaltung gesetzlicher Bestimmungen in Azure Security Center bereitstellen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

Für die Features dieser Richtlinien wurden zwei Aktualisierungen durchgeführt:

- Wenn sie zugewiesen werden, wird erzwungen, dass sie aktiviert bleiben.
- Sie können diese Richtlinien jetzt anpassen und alle Parameter auch nach der Bereitstellung noch aktualisieren. Wenn ein Benutzer beispielsweise einen weiteren Bewertungsschlüssel hinzufügen oder einen vorhandenen Bewertungsschlüssel bearbeiten möchte, ist dies möglich.

Beginnen Sie mit [Vorlagen zur Workflowautomatisierung](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Informieren Sie sich über das [Automatisieren der Reaktionen auf Security Center-Trigger](workflow-automation.md).


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>Von zwei Legacyempfehlungen werden keine Daten mehr direkt in das Azure-Aktivitätsprotokoll geschrieben 

Von Security Center werden die Daten für nahezu alle Sicherheitsempfehlungen an Azure Advisor übergeben und anschließend von Azure Advisor in das [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md) geschrieben.

Bei zwei Empfehlungen werden die Daten gleichzeitig direkt in das Azure-Aktivitätsprotokoll geschrieben. Diese Änderung sorgt dafür, dass Daten für diese Legacysicherheitsempfehlungen von Security Center nicht mehr direkt in das Aktivitätsprotokoll geschrieben werden. Stattdessen werden die Daten genau wie bei allen anderen Empfehlungen nach Azure Advisor exportiert.

Die beiden Legacyempfehlungen sind:
-  Integritätsprobleme in Endpoint Protection sollten auf Ihren Computern behoben werden.
- Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden.

Wenn Sie auf Informationen für diese beiden Empfehlungen in der Kategorie „Empfehlung vom Typ "TaskDiscovery"“ zugegriffen haben, ist dies nicht mehr möglich.


### <a name="recommendations-page-enhancements"></a>Verbesserungen der Seite „Empfehlungen“ 

Wir haben eine verbesserte Version der Empfehlungsliste veröffentlicht, um mehr Informationen auf einen Blick zu präsentieren.

Auf der Seite sehen Sie nun Folgendes:

1. Die maximale Bewertung und die aktuelle Bewertung für jede Sicherheitskontrolle
1. Symbole, die Tags ersetzen, etwa **Fix** und **Vorschau**
1. Eine neue Spalte mit der [Richtlinieninitiative](security-policy-concept.md) für jede Empfehlung (sichtbar, wenn „Nach Kontrollen gruppieren“ deaktiviert ist)

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Verbesserungen der Seite „Empfehlungen“ für Azure Security Center: März 2021" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Verbesserungen der flachen Liste „Empfehlungen“ für Azure Security Center: März 2021" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

Weitere Informationen finden Sie unter [Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md).