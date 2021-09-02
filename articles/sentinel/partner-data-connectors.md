---
title: Partnerdatenconnectors für Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie mehr über alle Partnerdatenconnectors für Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 06/17/2021
ms.author: bagol
ms.openlocfilehash: 25b09b496ff505bb37452b80355cae705af8936d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122338939"
---
# <a name="azure-sentinel-partner-data-connectors"></a>Partnerdatenconnectors für Azure Sentinel

In diesem Artikel werden die Datenconnectors in alphabetischer Reihenfolge aufgeführt, die Azure Sentinel für Nicht-Microsoft-Partnerorganisationen unterstützt. Wenn Sie den gewünschten Connector kennen, scrollen Sie auf dieser Seite zu seinem Namen oder suchen nach ihm, oder verwenden Sie die Links rechts oder oben im Artikel.

- Um die aufgeführten Connectors zu aktivieren und zu konfigurieren, finden Sie weitere Informationen unter [Verbinden von Datenquellen](connect-data-sources.md) und in den Links in den einzelnen Auflistungen.
- Informationen zu von Microsoft verfassten Datenconnectors und Dienst-zu-Dienst-Datenconnectors finden Sie unter [Dienst-zu-Dienst-Integration](connect-data-sources.md#service-to-service-integration).
- Informationen zu Azure Sentinel-Supportmodellen und zum Feld **Unterstützt von** finden Sie unter [Datenconnectorunterstützung](connect-data-sources.md#data-connector-support).

> [!IMPORTANT]
> Viele der folgenden Azure Sentinel-Partnerdatenconnectors befinden sich derzeit in der **Vorschau**. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="agari-phishing-defense-and-brand-protection-preview"></a>Agari Phishing Defense and Brand Protection (Vorschau)

Der Agari Phishing Defense und Brand Protection-Connector verbindet die Protokolle der Lösung „Brand Protection“ und „Phishing Defense“ mit Azure Sentinel.

Weitere Informationen finden Sie auf der [Agari-Entwicklerwebsite](https://developers.agari.com/agari-platform) und unter [Verbinden von Agari Phishing Defense und Brand Protection mit Azure Sentinel](connect-agari-phishing-defense.md).

**Datenerfassungsmethode:** [Azure Functions und REST-API](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Unterstützt von:** [Agari](https://support.agari.com/hc/en-us/articles/360000645632-How-to-access-Agari-Support)

## <a name="ai-analyst-aia-by-darktrace-preview"></a>AI Analyst (AIA) von Darktrace (Vorschau)

Der Darktrace-Connector sendet Modellverletzungen und AIA-Incidents an Azure Sentinel. Untersuchen Sie die Daten interaktiv durch Visualisierungen in der Darktrace AIA-Arbeitsmappe, die Übersichtsdiagramme mit Zeitverlauf enthält. Sie können detaillierte Drilldowns zu bestimmten Sicherheitsverletzungen und Incidents anzeigen und dann die Incidents auf der Darktrace-Benutzeroberfläche anzeigen, um sie weiter zu untersuchen.

AIA sendet Daten zu Sicherheitsverletzungen an die CommonSecurityLog-Tabelle von Azure Sentinel in Azure Log Analytics. Die CommonSecurityLog-Tabelle unterstützt die einfache Abfragesortierung und -aggregation über verschiedene Partnerconnectors hinweg.

**Datenerfassungsmethode:** [Common Event Format (CEF)](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [Darktrace](https://customerportal.darktrace.com/)

## <a name="ai-vectra-detect-preview"></a>AI Vectra Detect (Vorschau)

Der AI Vectra Detect-Datenconnector verbindet Ihre AI Vectra Detect-Daten mit Azure Sentinel. Mit der AI Vectra-Arbeitsmappe können Sie mit der Untersuchung von Netzwerkangriffen direkt über Sentinel beginnen. Sie können wichtige Hosts, Konten, Kampagnen und Erkennungen anzeigen, die Systemintegrität von Vectra überwachen und Protokolle überprüfen.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von AI Vectra Detect mit Azure Sentinel](connect-ai-vectra-detect.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [Vectra](https://www.vectra.ai/support)

## <a name="akamai-security-events-preview"></a>Akamai Security Events (Vorschau)

Der Akamai Security Events-Datenconnector erfasst [Akamai Security Events](https://www.akamai.com/us/en/products/security/) in Azure Sentinel. Weitere Informationen finden Sie unter [Akamai SIEM-Integration für Splunk and CEF Syslog](https://developer.akamai.com/tools/integrations/siem).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Akamai Security Events mit Azure Sentinel](connect-akamai-security-events.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog. Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** [Akamai](https://www.akamai.com/us/en/support/)

## <a name="alcide-kaudit-preview"></a>Alcide kAudit (Vorschau)

Der Alcide kAudit-Connector exportiert Ihre Kubernetes-Clusterüberwachungsprotokolle automatisch in Echtzeit in Azure Sentinel. Der kAudit-Connector bietet verbesserte Sichtbarkeit und Beobachtbarkeit in Ihren Kubernetes-Überwachungsprotokollen. Alcide kAudit bietet stabile Sicherheits- und Überwachungsfunktionen für forensische Zwecke.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Alcide kAudit mit Azure Sentinel](connect-alcide-kaudit.md).

**Datenerfassungsmethode:** [Azure Functions und REST-API](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Unterstützt von:** [Alcide](https://www.alcide.io/company/contact-us/)

## <a name="alsid-for-active-directory-preview"></a>Alsid für Active Directory (Vorschau)

Der Alsid für Active Directory-Connector exportiert Protokolle zu Alsid Indicators of Exposures, Trail Flows und Indicators of Attacks in Echtzeit in Azure Sentinel. Der Connector stellt auch einen Datenparser bereit, um die Protokolle zu bearbeiten. Arbeitsmappen bieten Active Directory-Überwachung und Datenvisualisierungen. Analytische Vorlagen helfen bei der Automatisierung von Reaktionen auf Ereignisse, Gefährdungen und Angriffe.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Alsid für Active Directory mit Azure Sentinel](connect-alsid-active-directory.md).

**Datenerfassungsmethode:** [Benutzerdefinierte Protokolle des Log Analytics-Agents](connect-data-sources.md#custom-logs). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** [Alsid](https://www.alsid.com/contact-us/)

## <a name="amazon-web-services"></a>Amazon Web Services

Der AWS-Datenconnector importiert AWS CloudTrail-Verwaltungsereignisse in Azure Sentinel. Informationen zum Aktivieren dieses Connectors finden Sie unter [Verbinden von Azure Sentinel mit AWS CloudTrail](connect-aws.md).

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** Microsoft

## <a name="apache-http-server-preview"></a>Apache HTTP Server (Vorschau)

Der Apache HTTP Server-Datenconnector erfasst Apache HTTP Server-Ereignisse in Azure Sentinel. Weitere Informationen finden Sie in der [Apache Logs-Dokumentation](https://httpd.apache.org/docs/2.4/logs.html).

**Datenerfassungsmethode:** [Benutzerdefinierte Protokolle des Log Analytics-Agents](connect-data-sources.md#custom-logs). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="apache-tomcat-preview"></a>Apache Tomcat (Vorschau)

Der Apache Tomcat-Datenconnector erfasst [Apache Tomcat](http://tomcat.apache.org/)-Ereignisse in Azure Sentinel. Weitere Informationen finden Sie in der [Apache Tomcat-Dokumentation](http://tomcat.apache.org/tomcat-10.0-doc/logging.html).

**Datenerfassungsmethode:** [Benutzerdefinierte Protokolle des Log Analytics-Agents](connect-data-sources.md#custom-logs). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="aruba-clearpass-preview"></a>Aruba ClearPass (Vorschau)

Der Aruba ClearPass-Connector verbindet Aruba ClearPass Audit-, Session-, System- und Insight-Protokolle mit Azure Sentinel. Weitere Informationen zum Konfigurieren der ClearPass-Lösung für die Weiterleitung an Syslog finden Sie unter [Hinzufügen eines Syslog-Exportfilters](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Aruba ClearPass mit Azure Sentinel](connect-aruba-clearpass.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog. Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="atlassian-confluence-audit-preview"></a>Atlassian Confluence Audit (Vorschau)

Der [Atlassian Confluence](https://www.atlassian.com/software/confluence) Audit-Datenconnector erfasst Confluence Audit-Datensätze. Der Connector kann Ereignisse abrufen, um potenzielle Sicherheitsrisiken zu untersuchen, die Zusammenarbeit Ihres Teams zu analysieren, Konfigurationsprobleme zu diagnostizieren und vieles mehr. Weitere Informationen finden Sie unter [Anzeigen des Überwachungsprotokolls](https://support.atlassian.com/confluence-cloud/docs/view-the-audit-log/).

**Datenerfassungsmethode:** [Azure Functions und REST-API](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Unterstützt von:** Microsoft

## <a name="atlassian-jira-audit-preview"></a>Atlassian Jira Audit (Vorschau)

Der Atlassian Jira Audit-Datenconnector erfasst Jira Audit Records-Ereignisse in Azure Sentinel. Der Connector kann Ereignisse abrufen, um potenzielle Sicherheitsrisiken zu untersuchen, die Zusammenarbeit Ihres Teams zu analysieren, Konfigurationsprobleme zu diagnostizieren und vieles mehr. Weitere Informationen finden Sie unter [Überwachen von Datensätzen](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-audit-records/).

**Datenerfassungsmethode:** [Azure Functions und REST-API](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Unterstützt von:** Microsoft

## <a name="barracuda-cloudgen-firewall-cgfw"></a>Barracuda CloudGen Firewall (CGFW)

Der Barracuda CGFW-Connector verbindet Ihre Barracuda CGFW-Protokolle mit Azure Sentinel. Informationen zum Konfigurieren von Syslog-Streaming für Barracuda CGFW finden Sie unter [Konfigurieren von Syslog-Streaming](https://aka.ms/sentinel-barracudacloudfirewall-connector).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Barracuda CloudGen Firewall mit Azure Sentinel](connect-barracuda-cloudgen-firewall.md).

**Datenerfassungsmethode:** [Syslog](connect-syslog.md). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** [Barracuda](https://www.barracuda.com/support)

## <a name="barracuda-web-application-firewall-waf"></a>Barracuda Web Application Firewall (WAF)

Der Barracuda WAF-Connector verbindet Ihre Barracuda WAF-Protokolle mit Azure Sentinel. Weitere Informationen finden Sie unter [Konfigurieren der Barracuda Web Application Firewall](https://aka.ms/asi-barracuda-connector).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Barracuda WAF mit Azure Sentinel](connect-barracuda.md).

**Datenerfassungsmethode:** [Benutzerdefinierte Protokolle des Log Analytics-Agents](connect-data-sources.md#custom-logs).

**Unterstützt von:** [Barracuda](https://www.barracuda.com/support)

## <a name="better-mobile-threat-defense-mtd-preview"></a>BETTER Mobile Threat Defense (MTD) (Vorschau)

Mit dem BETTER MTD-Connector können Unternehmen ihre Better MTD-Instanzen mit Azure Sentinel verbinden. Dieser Connector bietet Einblicke in die mobilen Geräte einer Organisation sowie den aktuellen Sicherheitsstatus mobiler Geräte und verbessert die allgemeinen SecOps-Funktionen. Weitere Informationen finden Sie in der [Azure Sentinel-Setupdokumentation](https://mtd-docs.bmobi.net/integrations/how-to-setup-azure-sentinel-integration#mtd-integration-configuration) zu BETTER Mobile.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von BETTER Mobile Threat Defense mit Azure Sentinel](connect-better-mtd.md).

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** BETTER Mobile

## <a name="beyond-security-besecure-preview"></a>Beyond Security beSECURE (Vorschau)

Der Beyond Security beSECURE-Connector verbindet Ihre Beyond Security beSECURE-Überprüfungsereignisse, Überprüfungsergebnisse und Überwachungspfade mit Azure Sentinel. Weitere Informationen finden Sie auf der beSECURE-[Übersichtsseite](https://beyondsecurity.com/solutions/besecure.html).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Beyond Security beSECURE mit Azure Sentinel](connect-besecure.md).

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** [Beyond Security](https://beyondsecurity.freshdesk.com/support/home)

## <a name="blackberry-cylanceprotect-preview"></a>BlackBerry CylancePROTECT (Vorschau)

Der Blackberry CylancePROTECT-Connector verbindet Überwachungs-, Bedrohungs-, Anwendungssteuerungs-, Geräte-, Speicherschutz- und Bedrohungsklassifizierungsprotokolle von CylancePROTECT mit Azure Sentinel. Informationen zum Konfigurieren von CylancePROTECT für die Weiterleitung über Syslog finden Sie im [Cylance Syslog-Leitfaden](https://docs.blackberry.com/content/dam/docs-blackberry-com/release-pdfs/en/cylance-products/syslog-guides/Cylance%20Syslog%20Guide%20v2.0%20rev12.pdf).

**Datenerfassungsmethode:** [Syslog](connect-syslog.md). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="broadcom-symantec-data-loss-prevention-dlp-preview"></a>Broadcom Symantec Data Loss Prevention (DLP) (Vorschau)

Der Broadcom Symantec DLP-Connector verbindet Symantec DLP-Richtlinien-/Antwortregeltrigger mit Azure Sentinel. Mit diesem Connector können Sie benutzerdefinierte Dashboards und Warnungen erstellen, um Untersuchungen zu unterstützen. Informationen zum Konfigurieren von Symantec DLP zur Weiterleitung über Syslog finden Sie unter [Konfigurieren des Protokolls für eine Syslog Server-Aktion](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Broadcom Symantec DLP mit Azure Sentinel](connect-broadcom-symantec-dlp.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog. Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="check-point"></a>Check Point

Der Check Point-Firewallconnector verbindet Ihre Check Point-Protokolle mit Azure Sentinel. Informationen zum Konfigurieren ihres Check Point-Produkts zum Exportieren von Protokollen finden Sie unter [Protokollexporter – Check Point-Protokollexport](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk122323).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Check Point mit Azure Sentinel](connect-checkpoint.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [Check Point](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.checkpoint.com%2Fsupport-services%2Fcontact-support%2F&data=04%7C01%7CNayef.Yassin%40microsoft.com%7C9965f53402ed44988a6c08d913fc51df%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637562796697084967%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=Av1vTkeYoEXzRKO%2FotZLtMMexIQI%2FIKjJGnPQsbhqmE%3D&reserved=0)

## <a name="cisco-asa"></a>Cisco ASA

Der Cisco ASA-Firewallconnector verbindet Ihre Cisco ASA-Protokolle mit Azure Sentinel. Befolgen Sie zum Einrichten der Verbindung die Anweisungen im [Cisco ASA Series CLI-Konfigurationshandbuch](https://www.cisco.com/c/en/us/support/docs/security/pix-500-series-security-appliances/63884-config-asa-00.html).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Cisco ASA mit Azure Sentinel](connect-cisco.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** Microsoft

## <a name="cisco-firepower-estreamer-preview"></a>Cisco Firepower eStreamer (Vorschau)

Der Cisco Firepower eStreamer-Connector verbindet Ihre eStreamer-Protokolle mit Azure Sentinel. Cisco Firepower eStreamer ist eine Client-Server-API, die für die Cisco Firepower NGFW-Lösung entwickelt wurde. Weitere Informationen finden Sie im [eStreamer eNcore für Sentinel-Betriebshandbuch](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [Cisco](https://www.cisco.com/c/en/us/support/index.html)

## <a name="cisco-meraki-preview"></a>Cisco Meraki (Vorschau)

Der Cisco Meraki-Connector verbindet Cisco Meraki-Ereignisprotokolle (MX/MR/MS), URLs, Flows, IDS_Alerts, Sicherheitsereignisse und Airmarshal-Ereignisse mit Azure Sentinel. Um Meraki-Geräte für die Weiterleitung über Syslog zu konfigurieren, befolgen Sie die Anweisungen unter [Meraki-Geräteberichte: Syslog, SNMP und API](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Cisco Meraki mit Azure Sentinel](connect-cisco-meraki.md).

**Datenerfassungsmethode:** [Syslog](connect-syslog.md). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="cisco-unified-computing-system-ucs-preview"></a>Cisco Unified Computing System (UCS) (Vorschau)

Der Cisco UCS-Connector verbindet Cisco UCS-Überwachungs-, Ereignis- und Fehlerprotokolle mit Azure Sentinel. Befolgen Sie die Anweisungen unter [Schritte zum Konfigurieren von Syslog für einen Syslog-Remoteserver](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog), um Cisco UCS für die Weiterleitung über Syslog zu konfigurieren.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Cisco Unified Computing System (UCS) mit Azure Sentinel](connect-cisco-ucs.md).

**Datenerfassungsmethode:** [Syslog](connect-syslog.md). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="cisco-umbrella-preview"></a>Cisco Umbrella (Vorschau)

Der Cisco Umbrella-Datenconnector erfasst in Amazon S3 gespeicherte Cisco Umbrella-Ereignisse wie DNS-, Proxy- und IP-Protokolle in Azure Sentinel. Der Cisco Umbrella-Datenconnector verwendet die Amazon S3-REST-API. Informationen zum Einrichten der Protokollierung und zum Abrufen von Anmeldeinformationen finden Sie unter [Protokollierung in Amazon S3](https://docs.umbrella.com/deployment-umbrella/docs/log-management#section-logging-to-amazon-s-3).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Cisco Umbrella mit Azure Sentinel](connect-cisco-umbrella.md).

**Datenerfassungsmethode:** [Azure Functions und REST-API](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Unterstützt von:** Microsoft

## <a name="citrix-analytics-security"></a>Citrix Analytics (Security)

Der Citrix Analytics (Security)-Datenconnector exportiert Risikoereignisdaten aus Citrix Analytics (Security) in Azure Sentinel. Sie können benutzerdefinierte Dashboards erstellen, Daten aus anderen Quellen zusammen mit Daten aus Citrix Analytics (Security) analysieren und benutzerdefinierte Logic Apps-Workflows erstellen, um Sicherheitsereignisse zu überwachen und zu entschärfen. Weitere Informationen finden Sie unter [Microsoft Azure Sentinel-Integration](https://docs.citrix.com/en-us/security-analytics/getting-started-security/azure-sentinel-integration.html).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Citrix Analytics (Security) mit Azure Sentinel](connect-citrix-analytics.md).

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** [Citrix](https://www.citrix.com/support/)

## <a name="citrix-web-app-firewall-waf-preview"></a>Citrix Web App Firewall (WAF) (Vorschau)

Der Citrix WAF-Datenconnector verbindet Ihre Citrix WAF-Protokolle mit Azure Sentinel. Citrix WAF entschärft Bedrohungen für Ihre öffentlichen Ressourcen, einschließlich Websites, Apps und APIs.
- Informationen zum Konfigurieren von WAF finden Sie unter [Support-WIKI: WAF-Konfiguration mit NetScaler](https://support.citrix.com/article/CTX234174).
- Weitere Informationen zum Konfigurieren von CEF-Protokollen finden Sie unter [Unterstützung für CEF-Protokollierung (Common Event Format) in der Anwendungsfirewall](https://support.citrix.com/article/CTX136146).
- Informationen zum Weiterleiten der Protokolle an den Proxy finden Sie unter [Konfigurieren der Citrix ADC-Appliance für Überwachungsprotokollierung](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Citrix WAF mit Azure Sentinel](connect-citrix-waf.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [Citrix](https://www.citrix.com/support/)

## <a name="cognni-preview"></a>Cognni (Vorschau)

Der Cognni-Datenconnector bietet eine schnelle und einfache Integration in Azure Sentinel. Sie können Cognni verwenden, um zuvor nicht klassifizierte wichtige Informationen autonom zuzuordnen und in Beziehung stehende Incidents zu erkennen. Cognni hilft Ihnen, Risiken für Ihre wichtigen Informationen zu erkennen, den Schweregrad der Vorfälle zu verstehen und die Details zu untersuchen, die Sie benötigen, um Abhilfe zu schaffen – und zwar schnell genug, um etwas zu bewirken.

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** [Cognni](https://cognni.ai/contact-support/)

## <a name="cyberark-enterprise-password-vault-epv-events-preview"></a>CyberArk Enterprise Password Vault (EPV) Events (Vorschau)

Der CyberArk-Datenconnector erfasst CyberArk EPV XML-Syslog-Nachrichten für Aktionen, die für den Tresor durchgeführt werden. EPV konvertiert die XML-Nachrichten über den *Sentinel.xsl*-Translator in das CEF-Standardformat und sendet sie an einen von Ihnen angegebenen Syslog-Stagingserver (syslog-ng, rsyslog). Der Log Analytics-Agent auf dem Syslog-Stagingserver importiert die Nachrichten in Log Analytics. Weitere Informationen finden Sie unter [SIEM-Anwendungen (Security Information and Event Management)](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm) in der CyberArk-Dokumentation.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von CyberArk Enterprise Password Vault mit Azure Sentinel](connect-cyberark.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [CyberArk](https://www.cyberark.com/customer-support/)

## <a name="cyberpion-security-logs-preview"></a>Cyberpion Security Logs (Vorschau)

Der Cyberpion Security Logs-Datenconnector erfasst Protokolle aus dem Cyberpion-System direkt in Azure Sentinel. Weitere Informationen finden Sie unter [Azure Sentinel](https://www.cyberpion.com/resource-center/integrations/azure-sentinel/) in der Cyberpion-Dokumentation.

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** Cyberpion

## <a name="eset-enterprise-inspector-preview"></a>ESET Enterprise Inspector (Vorschau)

Der ESET Enterprise Inspector-Datenconnector erfasst Erkennungen von ESET Enterprise Inspector mithilfe der REST-API, die in ESET Enterprise Inspector Version 1.4 und höher bereitgestellt wird. Weitere Informationen finden Sie unter [REST-API](https://help.eset.com/eei/1.5/en-US/api.html) in der ESET Enterprise Inspector-Dokumentation.

**Datenerfassungsmethode:** [Azure Functions und REST-API](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Unterstützt von:** [ESET](https://support.eset.com/en)

## <a name="eset-security-management-center-smc-preview"></a>ESET Security Management Center (SMC) (Vorschau)

Der ESET SMC-Datenconnector erfasst ESET SMC-Bedrohungsereignisse, Überwachungsprotokolle, Firewallereignisse und Websitefilter in Azure Sentinel. Weitere Informationen finden Sie unter [Syslog-Server](https://help.eset.com/esmc_admin/70/en-US/admin_server_settings_syslog.html) in der ESET SMC-Dokumentation.

**Datenerfassungsmethode:** [Benutzerdefinierte Protokolle des Log Analytics-Agents](connect-data-sources.md#custom-logs).

**Unterstützt von:** [ESET](https://support.eset.com/en)

## <a name="exabeam-advanced-analytics-preview"></a>Exabeam Advanced Analytics (Vorschau)

Der Exabeam Advanced Analytics-Datenconnector erfasst Exabeam Advanced Analytics-Ereignisse wie Systemintegrität, wichtige Sitzungen/Anomalien, erweiterte Analysen und den Auftragsstatus in Azure Sentinel. Um Protokolle aus Exabeam Advanced Analytics über Syslog zu senden, befolgen Sie die Anweisungen unter [Konfigurieren von Advanced Analytics-Systemaktivitätsbenachrichtigungen](https://docs.exabeam.com/en/advanced-analytics/i54/advanced-analytics-administration-guide/113254-configure-advanced-analytics.html#UUID-7ce5ff9d-56aa-93f0-65de-c5255b682a08).

**Datenerfassungsmethode:** [Syslog](connect-syslog.md). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="extrahop-revealx"></a>ExtraHop Reveal(x)

Der ExtraHop Reveal(x)-Datenconnector verbindet Ihr Reveal(x)-System mit Azure Sentinel. Azure Sentinel-Integration erfordert den ExtraHop Detection SIEM-Connector. Um den SIEM-Connector auf Ihrem Reveal(x)-System zu installieren, befolgen Sie die Anweisungen unter [ExtraHop Detection SIEM-Connector](https://aka.ms/asi-syslog-extrahop-forwarding).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von ExtraHop Reveal(x) mit Azure Sentinel](connect-extrahop.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [ExtraHop](https://www.extrahop.com/support/)

## <a name="f5-big-ip"></a>F5 BIG-IP 

Der F5 BIG-IP-Connector verbindet Ihre F5 LTM-, System- und ASM-Protokolle mit Azure Sentinel. Weitere Informationen finden Sie unter [Integrieren von F5 BIGIP in Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von F5 BIG-IP mit Azure Sentinel](connect-f5-big-ip.md).

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** Microsoft

## <a name="f5-networks"></a>F5 Networks

Der F5-Firewallconnector verbindet Ihre F5-Anwendungssicherheitsereignisse mit Azure Sentinel. Informationen zum Einrichten von Remoteprotokollierung finden Sie unter [Konfigurieren von Ereignisprotokollierung für Anwendungssicherheit](https://aka.ms/asi-syslog-f5-forwarding).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von F5 ASM mit Azure Sentinel](connect-f5.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** Microsoft

## <a name="forcepoint-cloud-access-security-broker-casb-preview"></a>Forcepoint Cloud Access Security Broker (CASB) (Vorschau)

Der Forcepoint CASB-Datenconnector exportiert CASB-Protokolle und -Ereignisse automatisch in Echtzeit in Azure Sentinel. Weitere Informationen finden Sie unter [Forcepoint CASB und Azure Sentinel](https://forcepoint.github.io/docs/casb_and_azure_sentinel/).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Forcepoint-Produkten mit Azure Sentinel](connect-forcepoint-casb-ngfw.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-cloud-security-gateway-csg-preview"></a>Forcepoint Cloud Security Gateway (CSG) (Vorschau)

Der Forcepoint CSG-Datenconnector exportiert CSG-Protokolle automatisch in Azure Sentinel. CSG ist ein zusammengeführter Cloudsicherheitsdienst, der Sichtbarkeit, Kontrolle und Bedrohungsschutz für Benutzer und Daten unabhängig davon bereitstellt, wo sie sich befinden. Weitere Informationen finden Sie unter [Forcepoint Cloud Security Gateway und Azure Sentinel](https://forcepoint.github.io/docs/csg_and_sentinel/).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Forcepoint-Produkten mit Azure Sentinel](connect-forcepoint-casb-ngfw.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-data-loss-prevention-dlp-preview"></a>Forcepoint Data Loss Prevention (DLP) (Vorschau)

Der Forcepoint DLP-Datenconnector exportiert automatisch DLP-Incidentdaten aus Forcepoint DLP in Echtzeit in Azure Sentinel. Weitere Informationen finden Sie unter [Forcepoint Data Loss Prevention und Azure Sentinel](https://forcepoint.github.io/docs/dlp_and_azure_sentinel/).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Forcepoint DLP mit Azure Sentinel](connect-forcepoint-dlp.md).

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** [Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-next-generation-firewall-ngfw-preview"></a>Forcepoint Next Generation Firewall (NGFW) (Vorschau)

Der Forcepoint NGFW-Datenconnector exportiert benutzerdefinierte Forcepoint NGFW-Protokolle automatisch in Echtzeit in Azure Sentinel. Weitere Informationen finden Sie unter [Forcepoint Next-Gen Firewall und Azure Sentinel](https://forcepoint.github.io/docs/ngfw_and_azure_sentinel/).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Forcepoint-Produkten mit Azure Sentinel](connect-forcepoint-casb-ngfw.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [Forcepoint](https://support.forcepoint.com/)

## <a name="forgerock-common-audit-caud-for-cef-preview"></a>ForgeRock Common Audit (CAUD) für CEF (Vorschau)

Die ForgeRock-Identitätsplattform bietet ein einzelnes allgemeines Überwachungsframework, mit dem Sie Protokolldaten ganzheitlich nachverfolgen können. Sie können Protokolldaten über die gesamte Plattform mit CAUD-Ereignishandlern und eindeutigen IDs extrahieren und aggregieren. Der CAUD für CEF-Connector ist offen und erweiterbar, und Sie können die Überwachungsprotokollierungs- und Berichterstellungsfunktionen für die Integration in Azure Sentinel verwenden. Weitere Informationen finden Sie unter [ForgeRock Common Audit (CAUD) für Azure Sentinel](https://github.com/javaservlets/SentinelAuditEventHandler).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [ForgeRock](https://www.forgerock.com/support)

## <a name="fortinet"></a>Fortinet 

Der Fortinet-Firewallconnector verbindet Fortinet-Protokolle mit Azure Sentinel. Um weitere Informationen zu erhalten, navigieren Sie zur [Fortinet-Dokumentbibliothek](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary), wählen Sie Ihre Version aus, und verwenden Sie das *Handbuch* und die *Protokollmeldungsreferenz* (PDF-Dateien).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Fortinet mit Azure Sentinel](connect-fortinet.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [Fortinet](https://support.fortinet.com/)

## <a name="google-workspace-g-suite-preview"></a>Google Workspace (G-Suite) (Vorschau)

Der Google Workspace-Datenconnector erfasst über die REST API Google Workspace Activity-Ereignisse in Azure Sentinel. Die Möglichkeit, Ereignisse zu erfassen, hilft bei den folgenden Aufgaben:
- Untersuchen potenzieller Sicherheitsrisiken.
- Analysieren der Zusammenarbeit Ihres Teams.
- Diagnostizieren von Konfigurationsproblemen.
- Nachverfolgen, wer sich wann anmeldet.
- Analysieren der Administratoraktivität.
- Verstehen, wie Benutzer Inhalte erstellen und freigeben.
- Überprüfen von Organisationsereignissen.

Befolgen Sie zum Abrufen von Anmeldeinformationen die Anweisungen unter [Ausführen von domänenweiter Google Workspace-Delegierung der Autorität](https://developers.google.com/admin-sdk/reports/v1/guides/delegation).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Google Workspace (ehemals G Suite) mit Azure Sentinel](connect-google-workspace.md).

**Datenerfassungsmethode:** [Azure Functions und REST-API](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Unterstützt von:** Microsoft

## <a name="illusive-attack-management-system-ams-preview"></a>Illusive Attack Management System (AMS) (Vorschau)

Der Illusive AMS-Connector gibt Illusive-Angriffsoberflächen-Analysedaten und Incidentprotokolle für Azure Sentinel frei. Sie können diese Informationen in dedizierten Dashboards anzeigen. Die ASM-Arbeitsmappe bietet Einblicke in das Angriffsflächenrisiko Ihrer Organisation, und die ADS-Arbeitsmappe verfolgt nicht autorisierte laterale Bewegungen im Netzwerk Ihrer Organisation nach. Weitere Informationen finden Sie im [Illusive Networks-Administratorhandbuch](https://support.illusivenetworks.com/hc/en-us/sections/360002292119-Documentation-by-Version).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Illusive Networks AMS mit Azure Sentinel](connect-illusive-attack-management-system.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [Illusive Networks](https://www.illusivenetworks.com/technical-support/)

## <a name="imperva-waf-gateway-preview"></a>Imperva WAF Gateway (Vorschau)

Der Imperva-Connector verbindet Ihre Imperva WAF Gateway-Warnungen mit Azure Sentinel. Weitere Informationen finden Sie unter [Schritte zum Aktivieren von Imperva WAF Gateway-Warnungsprotokollierung in Azure Sentinel](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Imperva WAF Gateway mit Azure Sentinel](connect-imperva-waf-gateway.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [Imperva](https://www.imperva.com/support/technical-support/)

## <a name="infoblox-network-identity-operating-system-nios-preview"></a>Infoblox Network Identity Operating System (NIOS) (Vorschau)

Der Infoblox NIOS-Connector verbindet Ihre Infoblox NIOS-Protokolle mit Azure Sentinel. Informationen zum Aktivieren der Syslog-Weiterleitung von Infoblox-NIOS-Protokollen finden Sie im [NIOS SNMP- und Syslog-Bereitstellungshandbuch](https://www.infoblox.com/wp-content/uploads/infoblox-deployment-guide-slog-and-snmp-configuration-for-nios.pdf).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Infoblox NIOS mit Azure Sentinel](connect-infoblox.md).

**Datenerfassungsmethode:** [Syslog](connect-syslog.md). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="juniper-srx-preview"></a>Juniper SRX (Vorschau)

Der Juniper SRX-Connector verbindet Juniper SRX-Protokolle mit Azure Sentinel. Informationen zum Weiterleiten von Datenverkehrsprotokollen finden Sie unter [Konfigurieren der Datenverkehrsprotokollierung (Sicherheitsrichtlinienprotokolle) für SRX Branch-Geräte](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA). Weitere Informationen zur Weiterleitung von Systemprotokollen finden Sie unter [Konfigurieren von Systemprotokollierung](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Juniper SRX mit Azure Sentinel](connect-juniper-srx.md).

**Datenerfassungsmethode:** [Syslog](connect-syslog.md). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** [Juniper Networks](https://support.juniper.net/support/)

## <a name="morphisec-utpp-preview"></a>Morphisec UTPP (Vorschau)

Der Morphisec-Datenconnector für Azure Sentinel integriert wichtige Erkenntnisse aus Ihren Sicherheitsprodukten. Sie können Ihre Analysefunktionen mit Such- und Korrelationsfunktionen, Threat Intelligence und benutzerdefinierten Warnungen erweitern. Der Morphisec-Datenconnector bietet Einblick in erweiterte Bedrohungen wie komplexe dateilose Angriffe, In-Memory- und Zero-Day-Exploits. Mit einer einzelnen produktübergreifenden Ansicht können Sie datenbasierte Echtzeitentscheidungen treffen, um Ihre wichtigsten Ressourcen zu schützen.

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog. Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Morphisec

## <a name="netskope-preview"></a>Netskope (Vorschau)

Der Netskope Cloud Security Platform-Connector erfasst Netskope-Protokolle und -Ereignisse in Azure Sentinel. Weitere Informationen finden Sie unter [Netskope Cloud Security Platform](https://www.netskope.com/platform).

**Datenerfassungsmethode:** [Azure Functions und REST-API](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Unterstützt von:** Microsoft

## <a name="nginx-http-server-preview"></a>NGINX HTTP Server (Vorschau)

Der NGINX HTTP Server-Datenconnector erfasst NGINX HTTP Server-Ereignisse in Azure Sentinel. Weitere Informationen finden Sie unter [Modul „ngx_http_log_module“](https://nginx.org/en/docs/http/ngx_http_log_module.html).

**Datenerfassungsmethode:** [Benutzerdefinierte Protokolle des Log Analytics-Agents](connect-data-sources.md#custom-logs). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="nxlog-basic-security-module-bsm-macos-preview"></a>NXLog Basic Security Module (BSM) macOS (Vorschau)

Der NXLog BSM macOS-Datenconnector verwendet die Sun BSM-Überwachungs-API, um Überwachungsereignisse direkt aus dem Kernel auf der macOS-Plattform zu erfassen. Dieser Datenconnector kann macOS-Überwachungsereignisse effizient in Echtzeit in Azure Sentinel exportieren. Weitere Informationen finden Sie im [NXLog Azure Sentinel-Benutzerhandbuch](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html).

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** [NXLog](https://nxlog.co/community-forum)

## <a name="nxlog-dns-logs-preview"></a>NXLog DNS Logs (Vorschau)

Der NXLog DNS Logs-Datenconnector verwendet die Ereignisablaufverfolgung für Windows (ETW), um sowohl Überwachungs- als auch analytische DNS-Serverereignisse zu erfassen. Um maximale Effizienz zu erzielen, liest das NXLog-Modul „im_etw“ Ereignisablaufverfolgungsdaten direkt, ohne die Ereignisablaufverfolgung in einer *ETL-Datei* erfassen zu müssen. Dieser REST-API-Connector kann DNS-Serverereignisse in Echtzeit an Azure Sentinel weiterleiten. Weitere Informationen finden Sie im [NXLog Azure Sentinel-Benutzerhandbuch](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von NXLog (Windows) DNS Logs mit Azure Sentinel](connect-nxlog-dns.md).

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** [NXLog](https://nxlog.co/community-forum)

## <a name="nxlog-linuxaudit-preview"></a>NXLog LinuxAudit (Vorschau)

Der NXLog LinuxAudit-Datenconnector unterstützt benutzerdefinierte Überwachungsregeln und erfasst Protokolle ohne Verwendung von AuditD oder anderer Benutzersoftware. Der Connector löst IP-Adressen und Gruppen-/Benutzer-IDs in ihre jeweiligen Namen auf, wodurch Linux-Überwachungsprotokolle besser lesbar werden. Dieser REST-API-Connector kann Linux-Sicherheitsereignisse in Echtzeit in Azure Sentinel exportieren. Weitere Informationen finden Sie im [NXLog Azure Sentinel-Benutzerhandbuch](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von NXLog LinuxAudit mit Azure Sentinel](connect-nxlog-linuxaudit.md).

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** [NXLog](https://nxlog.co/community-forum)

## <a name="okta-single-sign-on-preview"></a>Okta Single Sign-On (Vorschau)

Der Okta SSO-Datenconnector (Single Sign-On) erfasst Überwachungs- und Ereignisprotokolle aus der Okta-API in Azure Sentinel. Befolgen Sie die Anweisungen unter [Erstellen des Tokens](https://developer.okta.com/docs/guides/create-an-api-token/create-the-token/), um ein API-Token zu erstellen.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Okta SSO mit Azure Sentinel](connect-okta-single-sign-on.md).

**Datenerfassungsmethode:** [Azure Functions und REST-API](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Unterstützt von:** Microsoft

## <a name="onapsis-platform-preview"></a>Onapsis Platform (Vorschau)

Der Onapsis-Datenconnector exportiert die Alarme, die auf der Onapsis-Plattform ausgelöst werden, in Echtzeit in Azure Sentinel.

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [Onapsis](https://onapsis.force.com/s/login/)

## <a name="one-identity-safeguard-preview"></a>One Identity Safeguard (Vorschau)

Der One Identity Safeguard CEF Sentinel-Datenconnector erweitert den CEF-Standardconnector um Dashboards, die für Safeguard for Privileged Sessions spezifisch sind. Dieser Connector verwendet Geräteereignisse für Visualisierungen, Warnungen, Untersuchungen und vieles mehr. Weitere Informationen finden Sie im [One Identity Safeguard for Privileged Sessions-Verwaltungshandbuch](https://aka.ms/sentinel-cef-oneidentity-forwarding).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von One Identity Safeguard mit Azure Sentinel](connect-one-identity.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [One Identity](https://support.oneidentity.com/)

## <a name="oracle-weblogic-server-preview"></a>Oracle WebLogic Server (Vorschau)

Der OracleWebLogicServer-Datenconnector erfasst OracleWebLogicServer-Ereignisse in Azure Sentinel. Weitere Informationen finden Sie in der [Dokumentation zu Oracle WebLogic Server](https://docs.oracle.com/en/middleware/standalone/weblogic-server/14.1.1.0/index.html).

**Datenerfassungsmethode:** [Benutzerdefinierte Protokolle des Log Analytics-Agents](connect-data-sources.md#custom-logs). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="orca-security-alert-preview"></a>Orca Security Alert (Vorschau)

Der Orca Security Alerts-Connector exportiert automatisch Warnungsprotokolle in Azure Sentinel. Weitere Informationen finden Sie unter [Azure Sentinel-Integration](https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-configuration).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Orca Security mit Azure Sentinel](connect-orca-security-alerts.md).

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** [Orca Security](http://support.orca.security/)

## <a name="ossec-preview"></a>OSSEC (Vorschau)

Der OSSEC-Datenconnector erfasst OSSEC-Ereignisse in Azure Sentinel. Weitere Informationen finden Sie in der [OSSEC-Dokumentation](https://www.ossec.net/docs/). Befolgen Sie zum Konfigurieren des Sendens von OSSEC-Warnungen über Syslog die Anweisungen unter [Senden von Warnungen über syslog](https://www.ossec.net/docs/docs/manual/output/syslog-output.html).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** Microsoft

## <a name="palo-alto-networks"></a>Palo Alto Networks

Der Palo Alto Networks-Firewalldatenconnector verbindet Palo Alto Networks-Protokolle mit Azure Sentinel. Weitere Informationen finden Sie in den [CEF-Konfigurationshandbüchern (Common Event Format)](https://aka.ms/asi-syslog-paloalto-forwarding) und unter [Konfigurieren der Syslog-Überwachung](https://aka.ms/asi-syslog-paloalto-configure).

Der Palo Alto Networks-Datenconnector erfasst Protokolle in Azure Sentinel [CEF](connect-common-event-format.md) über Syslog.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Palo Alto Networks mit Azure Sentinel](connect-paloalto.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [Palo Alto Networks](https://www.paloaltonetworks.com/company/contact-support)

## <a name="perimeter-81-activity-logs-preview"></a>Perimeter 81 Activity Logs (Vorschau)

Der Perimeter 81 Activity Logs-Datenconnector verbindet Perimeter 81-Aktivitätsprotokolle mit Azure Sentinel. Weitere Informationen finden Sie in der [Azure Sentinel-Dokumentation](https://support.perimeter81.com/docs/360012680780) zu Perimeter 81.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Perimeter 81-Protokollen mit Azure Sentinel](connect-perimeter-81-logs.md).

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** [Perimeter 81](https://support.perimeter81.com/)

## <a name="proofpoint-on-demand-pod-email-security-preview"></a>Proofpoint On Demand (POD) Email Security (Vorschau)

Der POD Email Security-Datenconnector ruft POD Email Protection-Daten ab. Mit diesem Connector können Sie die Nachverfolgbarkeit von Nachrichten überprüfen und E-Mail-Aktivitäten, Bedrohungen und Datenexfiltration durch Angreifer und böswillige Insider überwachen. Sie können Organisationsereignisse beschleunigt überprüfen und Ereignisprotokolle für aktuelle Aktivitäten in stündlichen Schritten erhalten. Informationen zum Aktivieren und Überprüfen der POD Log-API finden Sie [in der Proofpoint-Community](https://proofpointcommunities.force.com/community/s/article/How-to-request-a-Community-account-and-gain-full-customer-access?utm_source=login&utm_medium=recommended&utm_campaign=public) und unter [Proofpoint-on-Demand-Pod-Log-API](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Proofpoint On Demand (POD) Email Security mit Azure Sentinel](connect-proofpoint-pod.md).

**Datenerfassungsmethode:** [Azure Functions und REST-API](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Unterstützt von:** Microsoft

## <a name="proofpoint-targeted-attack-protection-tap-preview"></a>Proofpoint Targeted Attack Protection (TAP) (Vorschau)

Der Proofpoint TAP-Connector erfasst Proofpoint TAP-Protokolle und -Ereignisse in Azure Sentinel. Der Connector bietet Einblick in Nachrichten- und Klickereignisse in Azure Sentinel.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Proofpoint TAP mit Azure Sentinel](connect-proofpoint-tap.md).

**Datenerfassungsmethode:** [Azure Functions und REST-API](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Unterstützt von:** Microsoft

## <a name="pulse-connect-secure-preview"></a>Pulse Connect Secure (Vorschau)

Der Pulse Connect Secure-Connector verbindet Ihre Pulse Connect Secure-Protokolle mit Azure Sentinel. Um Syslog-Streaming von Pulse Connect Secure-Protokollen zu aktivieren, befolgen Sie die Anweisungen unter [Konfigurieren von Syslog](https://docs.pulsesecure.net/WebHelp/Content/PCS/PCS_AdminGuide_8.2/Configuring%20Syslog.htm).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Pulse Connect Secure mit Azure Sentinel](connect-pulse-connect-secure.md).

**Datenerfassungsmethode:** [Syslog](connect-syslog.md). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="qualys-vulnerability-management-vm-knowledgebase-kb-preview"></a>Qualys Vulnerability Management (VM) KnowledgeBase (KB) (Vorschau)

Der Qualys VM KB-Datenconnector erfasst die neuesten Sicherheitsrisikodaten aus der Qualys KB in Azure Sentinel. Sie können diese Daten verwenden, um Sicherheitsrisikoerkennungen durch den Qualys VM-Datenconnector zu korrelieren und anzureichern.

**Datenerfassungsmethode:** [Syslog](connect-syslog.md). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="qualys-vm-preview"></a>Qualys VM (Vorschau)

Der Qualys VM-Datenconnector verwendet die Qualys-API, um Sicherheitsrisiko-Hosterkennungsdaten in Azure Sentinel zu erfassen. Der Connector bietet Einblick in Hosterkennungsdaten aus Sicherheitsrisikoscans.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Qualys VM mit Azure Sentinel](connect-qualys-vm.md).

**Datenerfassungsmethode:** [Azure Functions und REST-API](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Unterstützt von:** Microsoft

## <a name="salesforce-service-cloud-preview"></a>Salesforce Service Cloud (Vorschau)

Der Salesforce Service Cloud-Datenconnector erfasst Informationen zu Salesforce-Betriebsereignissen über die REST-API von Azure Sentinel. Mit diesem Connector können Sie Organisationsereignisse beschleunigt überprüfen und Ereignisprotokolle für aktuelle Aktivitäten in stündlichen Schritten erhalten. Weitere Informationen und Anmeldeinformationen finden Sie im [REST-API-Entwicklerhandbuch von Salesforce](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Salesforce Service Cloud mit Azure Sentinel](connect-salesforce-service-cloud.md).

**Datenerfassungsmethode:** [Syslog](connect-syslog.md). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="sentinelone-preview"></a>SentinelOne (Vorschau)

Der SentinelOne-Datenconnector erfasst SentinelOne-Ereignisse in Azure Sentinel. Zu den Ereignissen gehören Serverobjekte wie Bedrohungen, Agents, Anwendungen, Aktivitäten, Richtlinien, Gruppen und vieles mehr. Der Connector kann Ereignisse abrufen, um potenzielle Sicherheitsrisiken zu untersuchen, die Zusammenarbeit Ihres Teams zu analysieren, Konfigurationsprobleme zu diagnostizieren und vieles mehr.

**Datenerfassungsmethode:** [Azure Functions und REST-API](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Unterstützt von:** Microsoft

## <a name="sonicwall-firewall-preview"></a>SonicWall Firewall (Vorschau)

Der SonicWall Firewall-Datenconnector verbindet Firewallprotokolle mit Azure Sentinel. Weitere Informationen finden Sie unter [Protokoll > Syslog](http://help.sonicwall.com/help/sw/eng/7020/26/2/3/content/Log_Syslog.120.2.htm).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [SonicWall](https://www.sonicwall.com/support/)

## <a name="sophos-cloud-optix-preview"></a>Sophos Cloud Optix (Vorschau)

Der Sophos Cloud Optix-Datenconnector verbindet Ihre Sophos Cloud Optix-Protokolle mit Azure Sentinel. Weitere Informationen finden Sie in den Cloud Optix-Einstellungen auf der Seite [Integrationen](https://optix.sophos.com/#/integrations/sentinel) von Azure Sentinel.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Sophos Cloud Optix mit Azure Sentinel](connect-sophos-cloud-optix.md).

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** [Sophos](https://secure2.sophos.com/en-us/support.aspx)

## <a name="sophos-xg-firewall-preview"></a>Sophos XG Firewall (Vorschau)

Sophos XG Firewall verbindet Sophos XG Firewall-Protokolle mit Azure Sentinel. Weitere Informationen finden Sie unter [Hinzufügen eines Syslog-Servers](https://docs.sophos.com/nsg/sophos-firewall/18.0/Help/en-us/webhelp/onlinehelp/nsg/tasks/SyslogServerAdd.html).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Sophos XG mit Azure Sentinel](connect-sophos-xg-firewall.md).

**Datenerfassungsmethode:** [Syslog](connect-syslog.md). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="squadra-technologies-secrmm"></a>Squadra Technologies secRMM

Der Squadra Technologies secRMM-Datenconnector pusht Sicherheitsereignisdaten von USB-Wechselmedien in Azure Sentinel. Weitere Informationen finden Sie im [secRMM-Administratorhandbuch von Azure Sentinel](https://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Squadra Technologies secRMM mit Azure Sentinel](connect-squadra-secrmm.md).

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** [Squadra Technologies](https://www.squadratechnologies.com/Contact.aspx)

## <a name="squid-proxy-preview"></a>Squid Proxy (Vorschau)

Der [Squid Proxy](http://www.squid-cache.org/)-Datenconnector verbindet Squid Proxy-Protokolle mit Azure Sentinel.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Squid Proxy mit Azure Sentinel](connect-squid-proxy.md).

**Datenerfassungsmethode:** [Benutzerdefinierte Protokolle des Log Analytics-Agents](connect-data-sources.md#custom-logs). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="symantec-integrated-cyber-defense-exchange-icdx"></a>Symantec Integrated Cyber Defense Exchange (ICDx)

Der Symantec ICDx-Datenconnector verbindet Protokolle von Symantec-Sicherheitslösungen mit Azure Sentinel. Weitere Informationen finden Sie unter [Verbinden einer Symantec ICDx-Appliance](connect-symantec.md).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Symantec ICDx mit Azure Sentinel](connect-symantec.md).

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** [Broadcom Symantec](https://support.broadcom.com/security)

## <a name="symantec-proxysg-preview"></a>Symantec ProxySG (Vorschau)

Der Symantec ProxySG-Datenconnector verbindet Symantec ProxySG-Protokolle mit Azure Sentinel. Weitere Informationen finden Sie unter [Senden von Zugriffsprotokollen an einen Syslog-Server](https://knowledge.broadcom.com/external/article/166529/sending-access-logs-to-a-syslog-server.html).

Der ProxySG-Datenconnector von Symantec erfasst Protokolle in Azure Sentinel über [Syslog](connect-syslog.md). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Symantec Proxy SG mit Azure Sentinel](connect-symantec-proxy-sg.md).

**Datenerfassungsmethode:** [Syslog](connect-syslog.md). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="symantec-vip-preview"></a>Symantec VIP (Vorschau)

Der Symantec VIP-Datenconnector verbindet Symantec VIP-Protokolle mit Azure Sentinel. Weitere Informationen finden Sie unter [Konfigurieren von Ssylog](https://help.symantec.com/cs/VIP_EG_INSTALL_CONFIG/VIP/v134652108_v128483142/Configuring-syslog?locale=EN_US).

Der VIP-Datenconnector von Symantec erfasst Protokolle in Azure Sentinel über [Syslog](connect-syslog.md). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Symantec VIP mit Azure Sentinel](connect-symantec-vip.md).

**Datenerfassungsmethode:** [Syslog](connect-syslog.md). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="thycotic-secret-server-preview"></a>Thycotic Secret Server (Vorschau)

Der Thycotic Secret Server-Datenconnector verbindet Secret Server-Protokolle mit Azure Sentinel. Weitere Informationen finden Sie unter [Sichere Syslog-/CEF-Protokollierung](https://docs.thycotic.com/ss/10.9.0/events-and-alerts/secure-syslog-cef).

Der Thycotic-Datenconnector erfasst Protokolle in Azure Sentinel [CEF](connect-common-event-format.md) über Syslog.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Thycotic Secret Server mit Azure Sentinel](connect-thycotic-secret-server.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [Thycotic](https://thycotic.force.com/support/s/)

## <a name="trend-micro-deep-security"></a>Trend Micro Deep Security

Der Trend Micro Deep Security-Datenconnector verbindet Deep Security-Protokolle mit Azure Sentinel. Weitere Informationen finden Sie unter [Weiterleiten von Deep Security-Ereignissen an einen Syslog- oder SIEM-Server](https://help.deepsecurity.trendmicro.com/12_0/on-premise/siem-syslog-forwarding-secure.html?redirected=true&Highlight=Configure%20Syslog#Protection_modules_DSM).

Der Trend Micro Deep Security-Datenconnector erfasst Protokolle in Azure Sentinel [CEF](connect-common-event-format.md) über Syslog. Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Trend Micro Deep Security mit Azure Sentinel](connect-trend-micro.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog. Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** [Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="trend-micro-tippingpoint-preview"></a>Trend Micro TippingPoint (Vorschau)

Der Trend Micro TippingPoint-Datenconnector verbindet TippingPoint SMS IPS-Ereignisse mit Azure Sentinel.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Trend Micro TippingPoint mit Azure Sentinel](connect-trend-micro-tippingpoint.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog. Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** [Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="trend-micro-xdr-preview"></a>Trend Micro XDR (Vorschau)

Der Trend Micro XDR-Datenconnector erfasst Workbenchwarnungen aus der Trend Micro XDR-API in Azure Sentinel. Um ein Konto und ein API-Authentifizierungstoken zu erstellen, befolgen Sie die Anweisungen unter [Abrufen von API-Schlüsseln für Drittanbieterzugriff](https://docs.trendmicro.com/en-us/enterprise/trend-micro-xdr-help/ObtainingAPIKeys).

**Datenerfassungsmethode:** [Azure Functions und REST-API](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Unterstützt von:** [Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="vmware-carbon-black-endpoint-standard-preview"></a>VMware Carbon Black Endpoint Standard (Vorschau)

Der VMware Carbon Black Endpoint Standard-Datenconnector erfasst Carbon Black Endpoint Standard-Daten in Azure Sentinel. Befolgen Sie die Anweisungen unter [Erstellen eines API-Schlüssels](https://developer.carbonblack.com/reference/carbon-black-cloud/authentication/#creating-an-api-key), um einen API-Schlüssel zu erstellen.

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von VMware Carbon Black Cloud Endpoint Standard mit Azure Sentinel](connect-vmware-carbon-black.md).

**Datenerfassungsmethode:** [Azure Functions und REST-API](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Unterstützt von:** Microsoft

## <a name="vmware-esxi-preview"></a>VMware ESXi (Vorschau)

Der VMware ESXi-Datenconnector verbindet VMware ESXi-Protokolle mit Azure Sentinel. Informationen zur Konfiguration des VMware ESXi-Connectors für die Weiterleitung über Syslog finden Sie unter [Aktivieren von Syslog für ESXi 3.5 und 4.x](https://kb.vmware.com/s/article/1016621) und [Konfigurieren von Syslog auf ESXi-Hosts](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von VMware ESXi mit Azure Sentinel](connect-vmware-esxi.md).

**Datenerfassungsmethode:** [Syslog](connect-syslog.md). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft

## <a name="watchguard-firebox-preview"></a>WatchGuard Firebox (Vorschau)

Der WatchGuard Firebox-Datenconnector erfasst Firewallprotokolle in Azure Sentinel. Weitere Informationen finden Sie im [Microsoft Azure Sentinel-Integrationshandbuch](https://www.watchguard.com/help/docs/help-center/en-US/Content/Integration-Guides/General/Microsoft%20Azure%20Sentinel.html).

**Datenerfassungsmethode:** [Syslog](connect-syslog.md). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** [WatchGuard Technologies](https://www.watchguard.com/wgrd-support/overview)

## <a name="wirex-network-forensics-platform-preview"></a>WireX Network Forensics Platform (Vorschau)

Der WireX Systems-Datenconnector ermöglicht Sicherheitsexperten die Integration in Azure Sentinel, um forensische Untersuchungen anzureichern. Der Connector umfasst nicht nur den kontextbezogenen Inhalt von WireX, sondern kann auch Daten aus anderen Quellen analysieren. Sie können benutzerdefinierte Dashboards und Workflows erstellen, um während einer forensischen Untersuchung ein möglichst vollständiges Bild zu erhalten. Um weitere Informationen und Konfigurationsunterstützung zu erhalten, wenden Sie sich an den [WireX-Support](https://wirexsystems.com/contact-us/).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von WireX Network Forensics Platform mit Azure Sentinel](connect-wirex-systems.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** WireX

## <a name="workplace-from-facebook-preview"></a>Workplace from Facebook (Vorschau)

Der Workplace-Datenconnector erfasst allgemeine Workplace-Ereignisse in Azure Sentinel über Webhooks. Webhooks ermöglichen es benutzerdefinierten Integrations-Apps, Ereignisse in Workplace zu abonnieren und Updates in Echtzeit zu erhalten. Wenn eine Änderung auftritt, sendet Workplace eine HTTPS POST-Anforderung mit Ereignisinformationen an eine Rückrufdatenconnector-URL. Weitere Informationen finden Sie in der Dokumentation zu Webhooks. Der Connector kann Ereignisse abrufen, um potenzielle Sicherheitsrisiken zu untersuchen, die Zusammenarbeit Ihres Teams zu analysieren, Konfigurationsprobleme zu diagnostizieren und vieles mehr.

**Datenerfassungsmethode:** [Azure Functions und REST-API](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Unterstützt von:** Microsoft

## <a name="zimperium-mobile-thread-defense-preview"></a>Zimperium Mobile Thread Defense (Vorschau)

Der Zimperium Mobile Threat Defense-Datenconnector verbindet das Zimperium Threat-Protokoll mit Azure Sentinel, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und Untersuchungen zu verbessern. Durch diesen Connector erhalten Sie einen besseren Einblick in die Bedrohungslage mobiler Geräte in Ihrer Organisation und bessere Möglichkeiten für Sicherheitsvorgänge. Weitere Anweisungen finden Sie im [Zimperium-Kundensupportportal](https://support.zimperium.com/).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Zimperium mit Azure Sentinel](connect-zimperium-mtd.md).

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** [Zimperium](https://www.zimperium.com/support)

## <a name="zoom-reports-preview"></a>Zoom Reports (Vorschau)

Der Zoom Reports-Datenconnector erfasst Zoom Reports-Ereignisse in Azure Sentinel. Der Connector kann Ereignisse abrufen, um potenzielle Sicherheitsrisiken zu untersuchen, die Zusammenarbeit Ihres Teams zu analysieren, Konfigurationsprobleme zu diagnostizieren und vieles mehr. Um Anmeldeinformationen zu erhalten, befolgen Sie die Anweisungen unter [JWT With Zoom](https://marketplace.zoom.us/docs/guides/auth/jwt).

**Datenerfassungsmethode:** [REST-API](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Unterstützt von:** Microsoft

## <a name="zscaler"></a>Zscaler 

Der Zscaler-Datenconnector verbindet ZIA-Protokolle (Zscaler Internet Access) mit Azure Sentinel. Die Verwendung von Zscaler in Azure Sentinel bietet Ihnen Einblicke in die Internetnutzung Ihrer Organisation und erweitert Ihre Funktionen für Sicherheitsvorgänge. Weitere Informationen finden Sie im [Leitfaden zur Bereitstellung von Zscaler und Microsoft Azure Sentinel](https://aka.ms/ZscalerCEFInstructions).

Weitere Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Verbinden von Zscaler mit Azure Sentinel](connect-zscaler.md).

**Datenerfassungsmethode:** [CEF](connect-common-event-format.md) über Syslog.

**Unterstützt von:** [Zscaler](https://help.zscaler.com/submit-ticket-links)

## <a name="zscaler-private-access-zpa-preview"></a>Zscaler Private Access (ZPA) (Vorschau)

Der ZPA-Datenconnector erfasst Zscaler Private Access-Ereignisse in Azure Sentinel. Weitere Informationen finden Sie in der [Dokumentation zu Zscaler Private Access](https://help.zscaler.com/zpa).

**Datenerfassungsmethode:** [Benutzerdefinierte Protokolle des Log Analytics-Agents](connect-data-sources.md#custom-logs). Der Connector verwendet außerdem einen benutzerdefinierten Protokollparser, der auf einer Kusto-Funktion basiert.

**Unterstützt von:** Microsoft
