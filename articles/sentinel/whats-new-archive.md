---
title: Archiv für Neuerungen in Azure Sentinel
description: Enthält eine Beschreibung der Neuerungen und Änderungen in Azure Sentinel, die älter als sechs Monate sind.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9a4203f053fc295c2dfeac2a5ad40184dcd9c3b6
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522439"
---
# <a name="archive-for-whats-new-in-azure-sentinel"></a>Archiv für Neuerungen in Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Die Hauptseite [Neuheiten in Sentinel](whats-new.md) enthält Aktualisierungen für die letzten sechs Monate, während diese Seite ältere Elemente enthält.

Informationen zu älteren bereitgestellten Features finden Sie in unseren [Tech Community-Blogs](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Entsprechend gekennzeichnete Features sind derzeit als VORSCHAUVERSION verfügbar. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.


> [!TIP]
> Die Microsoft-Teams für die Bedrohungssuche steuern Abfragen, Playbooks, Arbeitsmappen und Notebooks zur [Azure Sentinel-Community](https://github.com/Azure/Azure-Sentinel) bei, darunter auch spezifische [Hunting-Abfragen](https://github.com/Azure/Azure-Sentinel), die Ihre Teams anpassen und verwenden können.
>
> Und auch Sie selbst können sich beteiligen! Werden Sie Teil der [GitHub-Community für Azure Sentinel-Bedrohungsspezialisten](https://github.com/Azure/Azure-Sentinel/wiki).
>

## <a name="march-2021"></a>März 2021

- [Festlegen der automatischen Aktualisierung von Arbeitsmappen im Ansichtsmodus](#set-workbooks-to-automatically-refresh-while-in-view-mode)
- [Neue Erkennungen für Azure Firewall](#new-detections-for-azure-firewall)
- [Automatisierungsregeln und ereignisgesteuerte Playbooks (öffentliche Vorschau)](#automation-rules-and-incident-triggered-playbooks-public-preview) (einschließlich komplett neuer Playbook-Dokumentation)
- [Neue Alarm-Anreicherungen: erweiterte Entity-Zuordnung und benutzerdefinierte Details (öffentliche Vorschau)](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details-public-preview)
- [Drucken von Azure Sentinel-Arbeitsmappen oder Speichern von Arbeitsmappen als PDF](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [Speicherung von Incidentfiltern und Sortierungseinstellungen in der Sitzung (Public Preview)](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Integration von Microsoft 365 Defender-Incidents (Public Preview)](#microsoft-365-defender-incident-integration-public-preview)
- [Neue Microsoft-Dienstconnectors mit Azure Policy](#new-microsoft-service-connectors-using-azure-policy)

### <a name="set-workbooks-to-automatically-refresh-while-in-view-mode"></a>Festlegen der automatischen Aktualisierung von Arbeitsmappen im Ansichtsmodus

Azure Sentinel-Benutzer können jetzt die neue [Azure Monitor-Funktion](https://techcommunity.microsoft.com/t5/azure-monitor/azure-workbooks-set-it-to-auto-refresh/ba-p/2228555) verwenden, um Arbeitsmappendaten während einer Anzeigesitzung automatisch zu aktualisieren.

Wählen Sie in der Arbeitsmappe oder Arbeitsmappenvorlage die Option :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **Automatische Aktualisierung** aus, um die Intervalloptionen anzuzeigen. Wählen Sie die gewünschte Option für die aktuelle Anzeigesitzung aus, und klicken Sie auf **Anwenden**.

- Die unterstützten Aktualisierungsintervalle reichen von **5 Minuten** bis **1 Tag**.
- Die automatische Aktualisierung ist standardmäßig deaktiviert. Um die Leistung zu optimieren, wird die automatische Aktualisierung zudem beim Schließen einer Arbeitsmappe deaktiviert und nicht im Hintergrund ausgeführt. Aktivieren Sie die automatische Aktualisierung ggf. wieder, wenn Sie die Arbeitsmappe das nächste Mal öffnen.
- Die automatische Aktualisierung wird angehalten, während Sie eine Arbeitsmappe bearbeiten, und die Intervalle der automatischen Aktualisierung werden jedes Mal neu gestartet, wenn Sie vom Bearbeitungsmodus zurück in den Ansichtsmodus wechseln.

    Die Intervalle werden auch neu gestartet, wenn Sie die Arbeitsmappe manuell mithilfe der Schaltfläche :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: **Aktualisieren** aktualisieren.

Weitere Informationen finden Sie unter [Visualisieren und Überwachen Ihrer Daten](monitor-your-data.md) und in der [Azure Monitor-Dokumentation](../azure-monitor/visualize/workbooks-overview.md).

### <a name="new-detections-for-azure-firewall"></a>Neue Erkennungen für Azure Firewall

Dem Bereich [Analysen](./understand-threat-intelligence.md) in Azure Sentinel wurden mehrere sofort einsatzbereite Erkennungen für Azure Firewall hinzugefügt. Mithilfe dieser neuen Erkennungen können Sicherheitsteams Warnungen erhalten, wenn Computer im internen Netzwerk versuchen, Internetdomänennamen oder IP-Adressen, die gemäß der Erkennungsregelabfrage bekannten IOCs (Indicators of Compromise) zugeordnet sind, abzufragen oder eine Verbindung mit ihnen herzustellen.

Folgende neue Erkennungen sind verfügbar:

- [Solorigate Network Beacon (Solorigate-Netzwerkbeacon)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
- [Known GALLIUM domains and hashes (Bekannte GALLIUM-Domänen und -Hashes)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
- [Known IRIDIUM IP (Bekannte IRIDIUM-IP-Adresse)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
- [Known Phosphorus group domains/IP (Bekannte Phosphorus-Gruppendomänen/IP-Adressen)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
- [THALLIUM domains included in DCU takedown (Im DCU-Takedown enthaltene THALLIUM-Domänen)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
- [Known ZINC related malware hash (Bekannter mit ZINC zusammenhängender Malware-Hash)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)
- [Known STRONTIUM group domains (Bekannte STRONTIUM-Gruppendomänen)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
- [NOBELIUM - Domain and IP IOCs - March 2021 (NOBELIUM – Domänen- und IP-IOCs – März 2021)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)


Dem integrierten Vorlagenkatalog werden laufend Erkennungen für Azure Firewall hinzugefügt. Um die neuesten Erkennungen für Azure Firewall anzuzeigen, filtern Sie unter **Regelvorlagen** das Feld **Datenquellen** nach **Azure Firewall**:

:::image type="content" source="media/whats-new/new-detections-analytics-efficiency-workbook.jpg" alt-text="Neue Erkennungen in der Arbeitsmappe „Analyseeffizienz“":::

Weitere Informationen finden Sie unter [Neue Erkennungen für Azure Firewall in Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-network-security/new-detections-for-azure-firewall-in-azure-sentinel/ba-p/2244958).

### <a name="automation-rules-and-incident-triggered-playbooks-public-preview"></a>Automatisierungsregeln und ereignisgesteuerte Playbooks (Öffentliche Vorschau)

Automatisierungsregeln sind ein neues Konzept in Azure Sentinel, mit dem Sie die Automatisierung der Incidentbehandlung zentral verwalten können. Mit Automatisierungsregeln können Sie nicht nur Playbooks Incidents zuweisen (nicht nur Alerts wie bisher), sondern auch Antworten für mehrere Analyseregeln gleichzeitig automatisieren, Incidents automatisch kennzeichnen, zuweisen oder schließen, ohne dass Playbooks erforderlich sind, und die Reihenfolge der ausgeführten Aktionen steuern. Automatisierungsregeln optimieren den Einsatz von Automatisierungen in Azure Sentinel und ermöglichen es Ihnen, komplexe Workflows für Ihre Incident-Orchestrierungsprozesse zu vereinfachen.

Lesen Sie die [vollständige Erklärung der Automatisierungsregeln](automate-incident-handling-with-automation-rules.md).

Wie bereits erwähnt, können Playbooks nun nicht mehr nur mit dem Warnungstrigger, sondern auch mit dem Incidenttrigger aktiviert werden. Der Incidenttrigger erweitert die möglichen Eingaben für Ihre Playbooks, da der Incident sämtliche Warnungs- und Entitätsdaten beinhaltet. So gestalten Sie Ihre Reaktionsworkflows effizienter und flexibler. Durch Incidents ausgelöste Playbooks werden durch Aufrufe über die Automatisierungsregeln aktiviert.

Erfahren Sie mehr über die [erweiterten Playbookfeatures](automate-responses-with-playbooks.md) und das [Erstellen eines Reaktionsworkflows](tutorial-respond-threats-playbook.md) mithilfe von Playbooks und Automatisierungsregeln.

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details-public-preview"></a>Neue Alarm-Anreicherungen: erweiterte Entity-Zuordnung und benutzerdefinierte Details (öffentliche Vorschau)

Es gibt jetzt zwei neue Möglichkeiten, Ihre Warnungen benutzerfreundlicher und informativer zu gestalten.

Optimieren Sie zunächst die Entitätszuordnung. Sie können jetzt fast 20 verschiedene Arten von Entitäten (z. B. Benutzer, Hosts, IP-Adressen, Dateien oder Prozesse) zu Postfächern, Azure-Ressourcen und IoT-Geräten zuordnen. Sie können auch mehrere Bezeichner für jede Entität verwenden, damit sie eindeutig identifiziert werden können. Dadurch wird das Dataset für Ihre Incidents umfangreicher, sodass weitreichendere Korrelation ermittelt und effizientere Untersuchungen durchgeführt werden können. Lesen Sie mehr über die [neuen Möglichkeiten für die Entitätszuordnung](map-data-fields-to-entities.md) in Ihren Warnungen.

Lesen Sie mehr über [Entitäten](entities-in-azure-sentinel.md), und sehen Sie sich die [vollständige Liste der verfügbaren Entitäten und die zugehörigen Bezeichner](entities-reference.md) an.

Verbessern Sie Ihre Untersuchungs- und Reaktionsmöglichkeiten noch weiter, indem Sie Ihren Warnungen benutzerdefinierte Details aus Rohereignissen hinzufügen. Schaffen Sie mehr Inhaltstransparenz in Ihren Incidents, damit Sie Sicherheitsbedrohungen effizienter und flexibler untersuchen und beheben können. Lesen Sie mehr über das [Anzeigen benutzerdefinierter Details](surface-custom-details-in-alerts.md) in Ihren Warnungen.



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Drucken von Azure Sentinel-Arbeitsmappen oder Speichern von Arbeitsmappen als PDF

Sie können Azure Sentinel-Arbeitsmappen jetzt drucken und somit auch als PDF-Datei exportieren, um sie lokal zu speichern oder zu teilen.

Wählen Sie in Ihrer Arbeitsmappe im Optionsmenü :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Inhalt drucken** aus. Wählen Sie dann Ihren Drucker oder die Option **Als PDF speichern** aus.

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Arbeitsmappe drucken oder als PDF speichern":::

Weitere Informationen finden Sie unter [Visualisieren und Überwachen Ihrer Daten](monitor-your-data.md).

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>Speicherung von Incidentfiltern und Sortierungseinstellungen in der Sitzung (Public Preview)

Ihre Filter und Sortierung für Incidents werden jetzt während Ihrer Azure Sentinel-Sitzung gespeichert, auch wenn Sie in andere Produktbereiche wechseln.
Solange Sie sich in derselben Sitzung befinden, sind Ihre Filter und Sortierung unverändert, wenn Sie zurück zum Bereich [Incidents](investigate-cases.md) in Azure Sentinel wechseln.

> [!NOTE]
> Wenn Sie Azure Sentinel verlassen oder den Browser aktualisieren, werden die Filter und Sortierung für Incidents nicht gespeichert.

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Integration von Microsoft 365 Defender-Incidents (Public Preview)

Die Vorfallsintegration [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) von Azure Sentinel ermöglicht es Ihnen, alle M365D-Vorfälle in Azure Sentinel zu streamen und sie zwischen beiden Portalen synchron zu halten. Vorfälle aus M365D (früher bekannt als Microsoft Threat Protection oder MTP) enthalten alle zugehörigen Warnungen, Entitäten und relevanten Informationen, die Ihnen genügend Kontext bieten, um Triage und erste Untersuchungen in Azure Sentinel durchzuführen. Sobald sie in Sentinel enthalten sind, bleiben Vorfälle bidirektional mit M365D synchronisiert, sodass Sie die Vorteile beider Portale bei Ihrer Vorfallsuntersuchung nutzen können.

Die gemeinsame Nutzung von Azure Sentinel und Microsoft 365 Defender vereint die Vorteile beider Lösungen. Sie erhalten den Überblick einer SIEM-Lösung über den sämtliche Informationsressourcen in Ihrer Organisation und dazu die Detailtiefe der angepassten und zugeschnittenen Untersuchungsleistung eines XDR zum Schutz Ihrer Microsoft 365-Ressourcen – beide werden für einen nahtlosen SOC-Betrieb koordiniert und synchronisiert.

Weitere Informationen finden Sie unter [Microsoft 365 Defender-Integration in Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Neue Microsoft-Dienstconnectors mit Azure Policy

[Azure Policy](../governance/policy/overview.md) ist ein Azure-Dienst, mit dem Sie mithilfe von Richtlinien die Eigenschaften einer Ressource erzwingen und steuern können. Durch die Verwendung von Richtlinien wird sichergestellt, dass Ressourcen mit Ihren IT-Governancestandards konform bleiben.

Zu den über Richtlinien steuerbaren Eigenschaften gehören die Erstellung und Verarbeitung von Diagnose- und Überwachungsprotokollen. In Azure Sentinel wird jetzt Azure Policy verwendet, um eine allgemeine Gruppe von Diagnoseprotokolleinstellungen auf alle (aktuellen und zukünftigen) Ressourcen eines bestimmten Typs anzuwenden, deren Protokolle in Azure Sentinel erfasst werden sollen. Dank Azure Policy ist es nicht mehr erforderlich, die Diagnoseprotokolleinstellungen einzeln für jede Ressource festzulegen.

Azure Policy-basierte Connectors sind nun für die folgenden Azure-Dienste verfügbar:
- [Azure Key Vault](./data-connectors-reference.md#azure-key-vault) (öffentliche Vorschau)
- [Azure Kubernetes Service](./data-connectors-reference.md#azure-kubernetes-service-aks) (öffentliche Vorschau)
- [Azure SQL-Datenbanken/-Server (allgemeine Verfügbarkeit)](./data-connectors-reference.md#azure-sql-databases) (GA)

Kunden können die Protokolle weiterhin manuell für bestimmte Instanzen senden, ohne die Richtlinien-Engine zu verwenden.


## <a name="february-2021"></a>Februar 2021

- [Cybersecurity Maturity Model Certification-Arbeitsmappe (CMMC)](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Datenconnectors von Drittanbietern](#third-party-data-connectors)
- [UEBA-Insights auf der Entitätsseite (Public Preview)](#ueba-insights-in-the-entity-page-public-preview)
- [Verbesserte Incidentsuche (Public Preview)](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Cybersecurity Maturity Model Certification-Arbeitsmappe (CMMC)

Die Azure Sentinel-CMMC-Arbeitsmappe umfasst einen Mechanismus zum Anzeigen von Protokollabfragen, die auf CMMC-Steuerelemente im gesamten Microsoft-Portfolio ausgerichtet sind, einschließlich Microsoft-Sicherheitsangebote, Office 365, Teams, Intune, Windows Virtual Desktop und vieles mehr.

Anhand der CMMC-Arbeitsmappe können Sicherheitsarchitekten, Techniker, Security Operations-Analysten, Manager und IT-Experten ein Situationsbewusstsein zum Sicherheitsstatus von Cloudworkloads erhalten. Außerdem gibt es Empfehlungen zum Auswählen, Entwerfen, Bereitstellen und Konfigurieren von Microsoft-Angeboten zur Anpassung an die jeweiligen CMMC-Anforderungen und -Praktiken.

Auch wenn Sie nicht verpflichtet sind, CMMC einzuhalten, ist die CMMC-Arbeitsmappe nützlich für den Aufbau von Security Operations Centern, die Entwicklung von Warnungen, die Visualisierung von Bedrohungen und das Situationsbewusstsein von Workloads.

Auf die CMMC-Arbeitsmappe können Sie im Azure Sentinel-Bereich **Arbeitsmappen** zugreifen. Wählen Sie **Vorlage** aus, und suchen Sie dann nach **CMMC**.

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="GIF-Aufzeichnungen der C M M C-Arbeitsmappenanleitung im ein- und ausgeschalteten Zustand." lightbox="media/whats-new/cmmc-guide-toggle.gif":::


Weitere Informationen finden Sie in folgenden Quellen:

- [Azure Sentinel Cybersecurity Maturity Model Certification (CMMC) Workbook](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524) (Azure Sentinel-Cybersecurity Maturity Model Certification-Arbeitsmappe (CMMC))
- [Visualisieren und Überwachen Ihrer Daten](monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Datenconnectors von Drittanbietern

In unserer Sammlung werden kontinuierlich Connectors von Drittanbietern integriert. Allein in den letzten zwei Monaten wurden dreißig Connectors hinzugefügt. Hier die Liste:

- [Agari Phishing Defense und Brand Protection](./data-connectors-reference.md#agari-phishing-defense-and-brand-protection-preview)
- [Sicherheitsrelevante Akamai-Ereignisse](./data-connectors-reference.md#akamai-security-events-preview)
- [Alsid for Active Directory](./data-connectors-reference.md#alsid-for-active-directory)
- [Apache HTTP Server](./data-connectors-reference.md#apache-http-server)
- [Aruba ClearPass](./data-connectors-reference.md#aruba-clearpass-preview)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](./data-connectors-reference.md#broadcom-symantec-data-loss-prevention-dlp-preview)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](./data-connectors-reference.md#cisco-meraki-preview)
- [Cisco Umbrella](./data-connectors-reference.md#cisco-umbrella-preview)
- [Cisco Unified Computing System (UCS)](./data-connectors-reference.md#cisco-unified-computing-system-ucs-preview)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [ESET Security Management Center](connect-data-sources.md)
- [Google Workspace (ehemals G Suite)](./data-connectors-reference.md#google-workspace-g-suite-preview)
- [Imperva WAF Gateway](./data-connectors-reference.md#imperva-waf-gateway-preview)
- [Juniper SRX](./data-connectors-reference.md#juniper-srx-preview)
- [Netskope](connect-data-sources.md)
- [NXLog DNS Logs](./data-connectors-reference.md#nxlog-dns-logs-preview)
- [NXLog Linux Audit](./data-connectors-reference.md#nxlog-linuxaudit-preview)
- [Onapsis Platform](connect-data-sources.md)
- [Proofpoint On Demand Email Security (POD)](./data-connectors-reference.md#proofpoint-on-demand-pod-email-security-preview)
- [Qualys Vulnerability Management Knowledge Base](connect-data-sources.md)
- [Salesforce Service Cloud](./data-connectors-reference.md#salesforce-service-cloud-preview)
- [SonicWall Firewall](connect-data-sources.md)
- [Sophos Cloud Optix](./data-connectors-reference.md#sophos-cloud-optix-preview)
- [Squid Proxy](./data-connectors-reference.md#squid-proxy-preview)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](./data-connectors-reference.md#thycotic-secret-server-preview)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](./data-connectors-reference.md#vmware-esxi-preview)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>UEBA-Insights auf der Entitätsseite (Public Preview)

Die Entitätsdetailseiten in Azure Sentinel enthalten einen [Erkenntnisbereich](identify-threats-with-entity-behavior-analytics.md#entity-insights), in dem verhaltensrelevante Erkenntnisse zu der Entität angezeigt werden, sodass Sie schnell Anomalien und Sicherheitsbedrohungen ermitteln können.

Wenn Sie [UEBA aktiviert](ueba-enrichments.md) und einen Zeitraum von mindestens vier Tagen ausgewählt haben, enthält dieser Erkenntnisbereich jetzt auch die folgenden neuen Abschnitte für UEBA-Erkenntnisse:

|`Section`  |BESCHREIBUNG  |
|---------|---------|
|**UEBA Insights** (UEBA-Erkenntnisse)     | Zusammenfassung anomaler Benutzeraktivitäten: <br>- an verschiedenen geografischen Standorten, auf Geräten und in Umgebungen<br>- in verschiedenen Zeiträumen und mit unterschiedlicher Häufigkeit – im Vergleich zum Verlauf des Benutzer <br>- im Vergleich zum Verhalten von Peers <br>- im Vergleich zum Verhalten der Organisation     |
|**User Peers Based on Security Group Membership** (Benutzerpeers basierend auf Sicherheitsgruppenmitgliedschaft)     |   Auflistung der Peers des Benutzers basierend auf der Mitgliedschaft in Azure AD-Sicherheitsgruppen. Teams für Sicherheitsvorgänge erhalten so eine Liste anderer Benutzer mit ähnlichen Berechtigungen.  |
|**User Access Permissions to Azure Subscription** (Benutzerzugriffsberechtigungen für Azure-Abonnement)     |     Anzeige der Zugriffsberechtigungen des Benutzers für die Azure-Abonnements, die direkt oder über Azure AD-Gruppen oder -Dienstprinzipale zugänglich sind.   |
|**Threat Indicators Related to The User** (Bedrohungsindikatoren in Bezug auf den Benutzer)     |  Eine Sammlung bekannter Bedrohungen in Bezug auf IP-Adressen in den Aktivitäten des Benutzers. Die Bedrohungen sind nach Bedrohungsart und -familie aufgeführt und werden durch den Microsoft Threat Intelligence-Dienst angereichert.       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>Verbesserte Incidentsuche (Public Preview)

Die Suchfunktion für Azure Sentinel-Vorfälle wurde verbessert, sodass Sie beim Untersuchen einer spezifischen Bedrohung schneller durch Vorfälle navigieren können.

Bei der Suche nach Vorfällen in Azure Sentinel können Sie nun nach folgenden Vorfallsdetails suchen:

- ID
- Titel
- Produkt
- Besitzer
- Tag

## <a name="january-2021"></a>Januar 2021

- [Analyseregel-Assistent: Verbesserte Abfragebearbeitung (Public Preview)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [PowerShell-Modul „Az.SecurityInsights“ (Public Preview)](#azsecurityinsights-powershell-module-public-preview)
- [SQL-Datenbank-Connector](#sql-database-connector)
- [Dynamics 365-Connector (Public Preview)](#dynamics-365-connector-public-preview)
- [Verbesserte Incidentkommentare](#improved-incident-comments)
- [Dedizierte Log Analytics-Cluster](#dedicated-log-analytics-clusters)
- [Verwaltete Identitäten für Logik-Apps](#logic-apps-managed-identities)
- [Verbesserte Regeloptimierung mit den Vorschaudiagrammen für Analyseregeln](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Analyseregel-Assistent: Verbesserte Abfragebearbeitung (Public Preview)

Der Azure Sentinel-Assistent für geplante Analyseregeln bietet nun die folgenden Verbesserungen für das Erstellen und Bearbeiten von Abfragen:

-   Ein erweiterbares Bearbeitungsfenster mit mehr Platz zum Anzeigen Ihrer Abfrage auf dem Bildschirm
-   Hervorhebung von Schlüsselwörtern im Abfragecode
-   Erweiterte Unterstützung für AutoVervollständigen
-   Validierung von Echtzeitabfragen Fehler in Abfragen werden nun auf der Scrollleiste als roter Block und im Registerkartennamen **Regellogik festlegen** als roter Punkt angezeigt. Außerdem kann eine Abfrage mit Fehlern nicht gespeichert werden.

Weitere Informationen finden Sie unter [Erstellen von benutzerdefinierten Analyseregeln zum Erkennen von Bedrohungen](detect-threats-custom.md).
### <a name="azsecurityinsights-powershell-module-public-preview"></a>PowerShell-Modul „Az.SecurityInsights“ (Public Preview)

Azure Sentinel unterstützt jetzt das neue PowerShell-Modul [Az.SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/).

Das Modul **Az.SecurityInsights** unterstützt gängige Azure Sentinel-Anwendungsfälle wie etwa die Interaktion mit Incidents, um Dinge wie Status, Schweregrad oder Besitzer zu ändern, sowie das Hinzufügen von Kommentaren und Bezeichnungen zu Incidents und das Erstellen von Lesezeichen.

Wir empfehlen zwar die Verwendung von ARM-Vorlagen ([Azure Resource Manager](../azure-resource-manager/templates/index.yml)) für Ihre CI/CD-Pipeline, das Modul **Az.SecurityInsights** ist jedoch bei Aufgaben nach der Bereitstellung hilfreich und für die SOC-Automatisierung vorgesehen.  Ihre SOC-Automatisierung kann beispielsweise Schritte zum Konfigurieren von Datenconnectors, zum Erstellen von Analyseregeln oder zum Hinzufügen von Automatisierungsaktionen zu Analyseregeln enthalten.

Weitere Informationen, einschließlich einer vollständigen Liste und einer Beschreibung der verfügbaren Cmdlets, sowie Parameterbeschreibungen und Beispiele finden Sie in der [PowerShell-Dokumentation zu „Az.SecurityInsights“](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>SQL-Datenbank-Connector

Azure Sentinel bietet jetzt einen Azure SQL-Datenbank-Connector, mit dem Sie die Überwachungs- und Diagnoseprotokolle Ihrer Datenbanken an Azure Sentinel streamen und kontinuierlich die Aktivitäten in allen Ihren Instanzen überwachen können.

Azure SQL ist eine vollständig verwaltete PaaS-Datenbank-Engine (Platform-as-a-Service), bei der die meisten Funktionen für die Datenbankverwaltung ohne Benutzereingriff erfolgen, z. B. Upgrades, Patches, Sicherungen und Überwachung.

Weitere Informationen finden Sie unter [Verbinden von Diagnose- und Überwachungsprotokollen von Azure SQL-Datenbank](./data-connectors-reference.md#azure-sql-databases).

### <a name="dynamics-365-connector-public-preview"></a>Dynamics 365-Connector (Public Preview)

Azure Sentinel umfasst nun einen Connector für Microsoft Dynamics 365, über den Sie die Aktivitätsprotokolle für Benutzer, Administratoren und Unterstützung der Dynamics 365-Anwendungen in Azure Sentinel erfassen können. Anhand dieser Daten können Sie die gesamten Datenverarbeitungsaktionen überwachen und auf mögliche Sicherheitsverletzungen prüfen.

Weitere Informationen finden Sie unter [Verbinden von Dynamics 365-Aktivitätsprotokollen mit Azure Sentinel](./data-connectors-reference.md#dynamics-365).

### <a name="improved-incident-comments"></a>Verbesserte Incidentkommentare

Analysten verwenden Incidentkommentare, um bei Incidents zusammenzuarbeiten und Prozesse und Schritte manuell oder im Rahmen eines Playbooks zu dokumentieren. 

Dank unserer verbesserten Kommentierung können Sie nun Ihre Kommentare formatieren und vorhandene Kommentare bearbeiten oder löschen.

Weitere Informationen finden Sie unter [Automatisches Erstellen von Incidents aus Microsoft-Sicherheitswarnungen](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Dedizierte Log Analytics-Cluster

Azure Sentinel unterstützt jetzt dedizierte Log Analytics-Cluster als Bereitstellungsoption. Die Verwendung eines dedizierten Clusters empfiehlt sich in folgenden Fällen:

- **Erfassung von mehr als 1 TB pro Tag** in Ihrem Azure Sentinel-Arbeitsbereich
- **Mehrere Azure Sentinel-Arbeitsbereiche** in Ihrer Azure-Registrierung

Dedizierte Cluster ermöglichen die Verwendung von Features wie kundenseitig verwalteten Schlüsseln, Lockbox, Mehrfachverschlüsselung und schnelleren arbeitsbereichsübergreifenden Abfragen, wenn sich mehrere Arbeitsbereiche im gleichen Cluster befinden.

Weitere Informationen finden Sie unter [Dedizierte Azure Monitor-Protokollcluster](../azure-monitor/logs/logs-dedicated-clusters.md).

### <a name="logic-apps-managed-identities"></a>Verwaltete Identitäten für Logik-Apps

Azure Sentinel unterstützt jetzt verwaltete Identitäten für den Logic Apps-Connector für Azure Sentinel. Dadurch können Sie einem bestimmten Playbook direkt Berechtigungen für die Verwendung in Azure Sentinel erteilen, anstatt zusätzliche Identitäten zu erstellen.

- **Ohne verwaltete Identität** benötigt der Logic Apps-Connector eine separate Identität mit einer Azure Sentinel-RBAC-Rolle, damit er in Azure Sentinel verwendet werden kann. Bei der separaten Identität kann es sich um einen Azure AD-Benutzer oder um einen Dienstprinzipal (beispielsweise eine bei Azure AD registrierte Anwendung) handeln.

- **Wenn Sie die Unterstützung verwalteter Identitäten in Ihrer Logik-App aktivieren**, wird die Logik-App bei Azure AD registriert und eine Objekt-ID bereitgestellt. Verwenden Sie die Objekt-ID in Azure Sentinel, um die Logik-App mit einer Azure RBAC-Rolle in Ihrem Azure Sentinel-Arbeitsbereich zuzuweisen. 

Weitere Informationen finden Sie unter

- [Authentifizieren des Zugriffs auf Azure-Ressourcen mithilfe verwalteter Identitäten in Azure Logic Apps](../logic-apps/create-managed-service-identity.md)
- [Dokumentation zum Logic Apps-Connector für Azure Sentinel](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Verbesserte Regeloptimierung mit den Vorschaudiagrammen für Analyseregeln (Public Preview)

Azure Sentinel ermöglicht nun eine bessere Optimierung Ihrer Analyseregeln, um deren Genauigkeit zu erhöhen und Stördatenverkehr zu verringern.

Nachdem Sie eine Analyseregel auf der Registerkarte **Regellogik festlegen** bearbeitet haben, steht auf der rechten Seite der Bereich **Ergebnissimulation** zur Verfügung. 

Wählen Sie **Mit aktuellen Daten testen** aus. Daraufhin wird von Azure Sentinel eine Simulation der letzten 50 Ausführungen Ihrer Analyseregel durchgeführt. Im generierten Diagramm wird die durchschnittliche Anzahl von Warnungen angezeigt, die durch die Regel generiert worden wären (basierend auf den ausgewerteten Ereignisrohdaten). 

Weitere Informationen finden Sie unter [Definieren der Regelabfragelogik und Konfigurieren von Einstellungen](detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>Dezember 2020

- [80 neue integrierte Hunting-Abfragen](#80-new-built-in-hunting-queries)
- [Verbesserungen für den Log Analytics-Agent](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 neue integrierte Hunting-Abfragen
 
Mit den integrierten Hunting-Abfragen von Azure Sentinel können SOC-Analysten Lücken in der aktuellen Erkennungsabdeckung verringern und neue Hunting-Spuren initiieren.

Dieses Update für Azure Sentinel umfasst neue Hunting-Abfragen mit Abdeckung der MITRE ATT&CK-Frameworkmatrix:

- **Sammlung**
- **Command-and-Control**
- **Zugriff auf Anmeldeinformationen**
- **Ermittlung**
- **Ausführung**
- **Exfiltration**
- **Auswirkung**
- **Erstzugriff**
- **Persistenz**
- **Rechteausweitung**

Die zusätzlichen Hunting-Abfragen unterstützen Sie bei der Ermittlung verdächtiger Aktivitäten in Ihrer Umgebung. Sie geben zwar unter Umständen auch legitime Aktivitäten und potenziell schädliche Aktivitäten zurück, können aber eine gute Orientierungshilfe bei der Bedrohungssuche sein. 

Wenn Sie nach der Ausführung dieser Abfragen mit den Ergebnissen zufrieden sind, können Sie sie ggf. in Analyseregeln konvertieren oder vorhandenen oder neuen Incidents Hunting-Ergebnisse hinzufügen.

Alle hinzugefügten Abfragen sind über die Azure Sentinel-Hunting-Seite verfügbar. Weitere Informationen finden Sie unter [Suchen nach Bedrohungen mit Azure Sentinel](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Verbesserungen für den Log Analytics-Agent

Azure Sentinel-Benutzer profitieren von folgenden Verbesserungen für den Log Analytics-Agent:

- **Unterstützung weiterer Betriebssysteme**, einschließlich CentOS 8, RedHat 8 und SuSE Linux 15
- **Unterstützung von Python 3** (zusätzlich zu Python 2)

Azure Sentinel verwendet den Log Analytics-Agent, um Ereignisse wie Windows-Sicherheitsereignisse, Syslog-Ereignisse, CEF-Protokolle und Ähnliches an Ihren Arbeitsbereich zu senden.

> [!NOTE]
> Der Log Analytics-Agent wird manchmal auch als OMS-Agent oder als Microsoft Monitoring Agent (MMA) bezeichnet. 
> 

Weitere Informationen finden Sie in der [Log Analytics-Dokumentation](../azure-monitor/agents/log-analytics-agent.md) sowie in den [Versionshinweisen zum Log Analytics-Agent](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>November 2020

- [Überwachen der Integrität Ihrer Playbooks in Azure Sentinel](#monitor-your-playbooks-health-in-azure-sentinel)
- [Microsoft 365 Defender-Connector (Public Preview)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Überwachen der Integrität Ihrer Playbooks in Azure Sentinel

Azure Sentinel-Playbooks basieren auf in [Azure Logic Apps](../logic-apps/index.yml) integrierten Workflows. Mit diesem Clouddienst können Sie Aufgaben, Geschäftsprozesse und Workflows planen, automatisieren und orchestrieren. Playbooks können bei der Erstellung eines Vorfalls oder bei der Auswahl und Bearbeitung von Vorfällen automatisch aufgerufen werden. 

Für Erkenntnisse zur Integrität, Leistung und Nutzung Ihrer Playbooks wurde eine [Arbeitsmappe](../azure-monitor/visualize/workbooks-overview.md) zur **Überwachung der Playbookintegrität** hinzugefügt. 

Verwenden Sie die Arbeitsmappe **Überwachung der Playbookintegrität**, um die Integrität Ihrer Playbooks zu überwachen oder anhand der Menge der erfolgreichen oder nicht erfolgreichen Ausführungen nach Anomalien zu suchen. 

Die Arbeitsmappe **Überwachung der Playbookintegrität** ist ab sofort im Vorlagenkatalog von Azure Sentinel verfügbar:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Arbeitsmappe zur Überwachung der Integrität von Beispielplaybooks":::

Weitere Informationen finden Sie unter

- [Logic Apps-Dokumentation](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Azure Monitor-Dokumentation](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender-Connector (Public Preview)
 
Mithilfe des Microsoft 365 Defender-Connectors für Azure Sentinel können Sie erweiterte Hunting-Protokolle (eine Art von Ereignisrohdaten) aus Microsoft 365 Defender an Azure Sentinel streamen. 

Dank der Integration von [Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/) in den Schutzschirm von [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) können Sie jetzt Ihre erweiterten Hunting-Ereignisse aus Microsoft Defender for Endpoint mithilfe des Microsoft 365 Defender-Connectors erfassen und sie direkt an neue, zu diesem Zweck erstellte Tabellen in Ihrem Azure Sentinel-Arbeitsbereich streamen. 

Da die Azure Sentinel-Tabellen auf dem gleichen Schema basieren, das auch im Microsoft 365 Defender-Portal verwendet wird, haben Sie uneingeschränkten Zugriff auf sämtliche erweiterte Hunting-Protokolle. 

Weitere Informationen finden Sie unter [Verknüpfen von Daten aus Microsoft 365 Defender mit Azure Sentinel](connect-microsoft-365-defender.md).

> [!NOTE]
> Microsoft 365 Defender wurde bisher als Microsoft Threat Protection oder MTP bezeichnet. Microsoft Defender for Endpoint wurde bisher als Microsoft Defender Advanced Threat Protection oder MDATP bezeichnet.
> 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
>[Schnellstart: Ausführen des Onboardings für Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Einblick in Warnungen](get-visibility.md)
