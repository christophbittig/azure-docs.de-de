---
title: Archiv für Neuerungen in Azure Sentinel
description: Enthält eine Beschreibung der Neuerungen und Änderungen in Azure Sentinel, die älter als sechs Monate sind.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 06/15/2021
ms.openlocfilehash: 979855c0ad6a19c03c0c2c23cde935bc03cba157
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122356272"
---
# <a name="archive-for-whats-new-in-azure-sentinel"></a>Archiv für Neuerungen in Azure Sentinel

Die Hauptseite mit Versionshinweisen zu [Neuerungen in Azure Sentinel](whats-new.md) enthält Aktualisierungen aus den letzten sechs Monaten. Ältere Einträge finden Sie hingegen auf dieser Seite.

Informationen zu älteren bereitgestellten Features finden Sie in unseren [Tech Community-Blogs](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Entsprechend gekennzeichnete Features sind derzeit als VORSCHAUVERSION verfügbar. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.


> [!TIP]
> Die Microsoft-Teams für die Bedrohungssuche steuern Abfragen, Playbooks, Arbeitsmappen und Notebooks zur [Azure Sentinel-Community](https://github.com/Azure/Azure-Sentinel) bei, darunter auch spezifische [Hunting-Abfragen](https://github.com/Azure/Azure-Sentinel), die Ihre Teams anpassen und verwenden können.
>
> Und auch Sie selbst können sich beteiligen! Werden Sie Teil der [GitHub-Community für Azure Sentinel-Bedrohungsspezialisten](https://github.com/Azure/Azure-Sentinel/wiki).
>

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

- [Agari Phishing Defense und Brand Protection](connect-agari-phishing-defense.md)
- [Sicherheitsrelevante Akamai-Ereignisse](connect-akamai-security-events.md)
- [Alsid for Active Directory](connect-alsid-active-directory.md)
- [Apache HTTP Server](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [ESET Security Management Center](connect-data-sources.md)
- [Google Workspace (ehemals G Suite)](connect-google-workspace.md)
- [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [NXLog DNS Logs](connect-nxlog-dns.md)
- [NXLog Linux Audit](connect-nxlog-linuxaudit.md)
- [Onapsis Platform](connect-data-sources.md)
- [Proofpoint On Demand Email Security (POD)](connect-proofpoint-pod.md)
- [Qualys Vulnerability Management Knowledge Base](connect-data-sources.md)
- [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
- [SonicWall Firewall](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

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

Weitere Informationen finden Sie unter [Verbinden von Diagnose- und Überwachungsprotokollen von Azure SQL-Datenbank](connect-azure-sql-logs.md).

### <a name="dynamics-365-connector-public-preview"></a>Dynamics 365-Connector (Public Preview)

Azure Sentinel umfasst nun einen Connector für Microsoft Dynamics 365, über den Sie die Aktivitätsprotokolle für Benutzer, Administratoren und Unterstützung der Dynamics 365-Anwendungen in Azure Sentinel erfassen können. Anhand dieser Daten können Sie die gesamten Datenverarbeitungsaktionen überwachen und auf mögliche Sicherheitsverletzungen prüfen.

Weitere Informationen finden Sie unter [Verbinden von Dynamics 365-Aktivitätsprotokollen mit Azure Sentinel](connect-dynamics-365.md).

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