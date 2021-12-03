---
title: Suchen Ihres Microsoft Sentinel-Daten-Konnektors | Microsoft-Dokumentation
description: Erfahren Sie mehr über bestimmte Konfigurationsschritte für Microsoft Sentinel-Daten-Konnektor.
services: sentinel
documentationcenter: na
author: batamig
ms.service: microsoft-sentinel
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: c5ba2be13c48c85786f2ab916eaf9a37c649a5e9
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518563"
---
# <a name="find-your-microsoft-sentinel-data-connector"></a>Suchen Ihres Microsoft Sentinel-Daten-Konnektors

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel wird die Bereitstellung von Daten-Konnektoren in Microsoft Sentinel beschrieben, wobei alle unterstützten, sofort einsatzbereiten Daten-Konnektoren sowie Links zu allgemeinen Bereitstellungsverfahren und zusätzlichen Schritten, die für bestimmte Konnektoren erforderlich sind, aufgeführt werden.

> [!TIP]
> Einige Datenconnectors werden nur über Lösungen bereitgestellt. Weitere Informationen finden Sie im [Microsoft Sentinel-Lösungskatalog.](sentinel-solutions-catalog.md) Sie finden auch andere, in der Community integrierte Daten-Konnektors im [Microsoft Sentinel-GitHub Repository](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors).
>

## <a name="how-to-use-this-guide"></a>Verwendung dieses Leitfadens

1. Suchen Sie zunächst den Connector für Ihr Produkt, Ihren Dienst oder Ihr Gerät im Menü mit den Überschriften auf der rechten Seite, und wählen Sie ihn aus.

    Die erste Information, die zu jedem Connector angezeigt wird, ist die **Datenerfassungsmethode**. Die dort angezeigte Methode enthält einen Link zu einem der folgenden generischen Bereitstellungsverfahren, die die meisten Informationen enthalten, die Sie benötigen, um Ihre Datenquellen mit Microsoft Sentinel zu verbinden:

    | Datenerfassungsmethode | Verknüpfter Artikel mit Anweisungen |
    | --- | --- |
    | **Azure-Dienst-zu-Dienst-Integration** | [Verbinden mit Azure-, Windows-, Microsoft- und Amazon-Diensten](connect-azure-windows-microsoft-services.md) |
    | **Common Event Format (CEF) über Syslog** | [Einlesen von CEF-formatierten Protokollen von Ihrem Gerät oder Ihrer Anwendung in Microsoft Sentinel](connect-common-event-format.md) |
    | **Microsoft Sentinel-Datensammler-API** | [Verbinden Ihrer Datenquelle an die Microsoft Sentinel-Datensammler-API, um Daten zu erfassen](connect-rest-api-template.md) |
    | **Azure Functions und die REST-API** | [Verwenden Azure Functions, um Microsoft Sentinel mit Ihrer Datenquelle zu verbinden](connect-azure-functions-template.md) |
    | **Syslog** | [Sammeln von Daten aus Linux-basierten Quellen mithilfe von Syslog](connect-syslog.md) |
    | **Benutzerdefinierte Protokolle** | [Sammeln von Daten in benutzerdefinierten Protokollformaten für Microsoft Sentinel mit dem Log Analytics-Agent](connect-custom-logs.md) |
    |

    > [!NOTE]
    > Die Datenerfassungsmethode **Azure-Dienst-zu-Dienst-Integration** steht je nach Connectortyp mit drei verschiedenen Abschnitten in diesem Artikel in Zusammenhang. Der Abschnitt unten für jeden Connector gibt den Abschnitt in diesem Artikel an, mit dem er verknüpft ist.

1. Wenn Sie einen bestimmten Connector bereitstellen, wählen Sie den Artikel zur entsprechenden **Datenerfassungsmethode** aus und verwenden die Informationen und zusätzlichen Leitfäden im entsprechenden Abschnitt unten, als Ergänzung zu den Informationen in diesem Artikel.

> [!TIP]
>
> - Viele Daten-Konnektoren können auch als Teil einer [Microsoft Sentinel-Lösung](sentinel-solutions.md) eingesetzt werden, zusammen mit den zugehörigen Analyseregeln, Arbeitsmappen und Playbooks. Weitere Informationen finden Sie im [Microsoft Sentinel-Lösungskatalog.](sentinel-solutions-catalog.md)
>
> - Weitere Daten-Konnektors werden von der Microsoft Sentinel-Community bereitgestellt und finden Sie im Azure Marketplace. Die Dokumentation dieser Communitydatenconnectors ist Verantwortung der Organisation, die den Connector erstellt hat.
>
> - Wenn Ihre Datenquelle nicht aufgeführt ist oder derzeit nicht unterstützt wird, können Sie auch einen eigenen, benutzerdefinierten Connector erstellen. Weitere Informationen finden Sie unter [Ressourcen zum Erstellen von benutzerdefinierten Microsoft Sentinel-Konnektors.](create-custom-connector.md)

> [!IMPORTANT]
> Notierte Microsoft Sentinel-Daten-Konnektors befinden sich derzeit in der **Vorschauversion.** In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="agari-phishing-defense-and-brand-protection-preview"></a>Agari Phishing Defense and Brand Protection (Vorschau)

| Connectorattribut | BESCHREIBUNG|
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md) <br><br>**Vor der Bereitstellung:** [Aktivieren der Security Graph-API (optional)](#enable-the-security-graph-api-optional) <br>**Nach der Bereitstellung:** [Zuweisen der erforderlichen Berechtigungen zu Ihrer Funktions-App](#assign-necessary-permissions-to-your-function-app)|
| **Log Analytics-Tabellen** | agari_bpalerts_log_CL<br>agari_apdtc_log_CL<br>agari_apdpolicy_log_CL |
| **Azure Functions-App-Code** | https://aka.ms/Sentinel-agari-functionapp |
| **API-Anmeldeinformationen** | <li>Client-ID<li>Geheimer Clientschlüssel<li>(Optional: Graph-Mandanten-ID, Graph-Client-ID, Graph-Clientgeheimnis) |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>[Schnellstart](https://developers.agari.com/agari-platform/docs/quick-start)<li>[Agari-Website für Entwickler](https://developers.agari.com/agari-platform) |
| **Anweisungen zur Connectorbereitstellung** | <li>[Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager)<li>[Manuelle Bereitstellung](connect-azure-functions-template.md?tabs=MPS) |
| **Anwendungseinstellungen** | <li>clientID<li>clientSecret<li>workspaceID<li>workspaceKey<li>enableBrandProtectionAPI (TRUE/FALSE)<li>enablePhishingResponseAPI (TRUE/FALSE)<li>enablePhishingDefenseAPI (TRUE/FALSE)<li>resGroup (Ressourcengruppe eingeben)<li>functionName<li>subId (Abonnement-ID eingeben)<li>enableSecurityGraphSharing (TRUE/FALSE; siehe unten)<br>Erforderlich, wenn enableSecurityGraphSharing auf TRUE festgelegt ist (siehe unten):<li>GraphTenantId<li>GraphClientId<li>GraphClientSecret<li>logAnalyticsUri (optional) |
| **Unterstützt von** | [Agari](https://support.agari.com/hc/en-us/articles/360000645632-How-to-access-Agari-Support) |
| | |

### <a name="enable-the-security-graph-api-optional"></a>Aktivieren der Security Graph-API (optional)

> [!IMPORTANT]
> Wenn Sie diesen Schritt ausführen, sollte dies vor der Bereitstellung Ihres Datenconnectors erfolgen.
>
Mit der Funktionsapp von Agari können Sie Informationen zu Bedrohungen mit Microsoft Sentinel über die Security Graph-API teilen. Hierfür muss der [Sentinel Threat Intelligence Platforms-Connector](./connect-threat-intelligence-tip.md) aktiviert werden. Außerdem muss [eine Anwendung in Azure Active Directory registriert werden](/graph/auth-register-app-v2).

Dadurch erhalten Sie drei Informationen, die Sie beim [Bereitstellen der Funktions-App](connect-azure-functions-template.md) verwenden: die **Graph-Mandanten-ID**, die **Graph-Client-ID** und das **Graph-Clientgeheimnis** (siehe *Anwendungseinstellungen* in der Tabelle unten).

### <a name="assign-necessary-permissions-to-your-function-app"></a>Zuweisen der erforderlichen Berechtigungen zu Ihrer Funktions-App

Der Agari-Connector speichert Zeitstempel für Protokollzugriffe in einer Umgebungsvariablen. Damit die Anwendung in diese Variable schreiben kann, müssen der vom System zugewiesenen Identität Berechtigungen zugewiesen werden.

1. Navigieren Sie im Azure-Portal zu **Funktions-App**.
1. Wählen Sie auf der Seite **Funktions-App** in der Liste Ihre Funktions-App und dann im Navigationsmenü der Funktions-App **Identität** unter **Einstellungen** aus.
1. Legen Sie auf der Registerkarte **Systemseitig zugewiesen** den **Status** auf **Ein** fest.
1. Wählen Sie **Speichern** aus, woraufhin die Schaltfläche **Azure-Rollenzuweisungen** angezeigt wird. Wählen Sie ihn aus.
1. Wählen Sie auf dem Bildschirm **Azure-Rollenzuweisungen** die Option **Rollenzuweisung hinzufügen** aus. Legen Sie **Bereich** auf **Abonnement** fest. Wählen Sie in der Dropdownliste **Abonnement** Ihr Abonnement aus, und legen Sie **Rolle** auf **App Configuration-Datenbesitzer** fest.
1. Wählen Sie **Speichern** aus.


## <a name="ai-analyst-aia-by-darktrace-preview"></a>AI Analyst (AIA) von Darktrace (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** <br><br>[Konfigurieren der CEF-Protokollweiterleitung für AI Analyst](#configure-cef-log-forwarding-for-ai-analyst) |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Unterstützt von** | [Darktrace](https://customerportal.darktrace.com/) |
| | |

### <a name="configure-cef-log-forwarding-for-ai-analyst"></a>Konfigurieren der CEF-Protokollweiterleitung für AI Analyst

Konfigurieren Sie Darktrace, um Syslog-Nachrichten im CEF-Format über den Log Analytics-Agent an Ihren Azure-Arbeitsbereich weiterzuleiten.

1. Navigieren Sie in Darktrace Threat Visualizer im Hauptmenü unter **Admin** zur Seite **System Config** (Systemkonfiguration).
1. Wählen Sie im Menü auf der linken Seite **Module** aus, und wählen Sie **Microsoft Sentinel** aus den **verfügbaren Workflow- Integrationen** aus.
1. Ein Konfigurationsfenster wird geöffnet. Suchen Sie **Microsoft Sentinel Syslog CEF,** und wählen Sie **Neu** aus, um die Konfigurationseinstellungen offenzulegen, sofern sie nicht bereits verfügbar gemacht wurden.
1. Geben Sie im Feld **Server configuration** (Serverkonfiguration) den Speicherort der Protokollweiterleitung ein, und ändern Sie optional den Kommunikationsport. Stellen Sie sicher, dass der Port auf 514 festgelegt ist und von allen zwischengeschalteten Firewalls zugelassen wird.
1. Konfigurieren Sie ggf. Warnungsschwellenwerte, Zeitoffsets oder zusätzliche Einstellungen.
1. Überprüfen Sie alle zusätzlichen Konfigurationsoptionen, die Sie möglicherweise aktivieren möchten, um die Syslog-Syntax anzupassen.
1. Aktivieren Sie **Warnungen senden**, und speichern Sie Ihre Änderungen.

## <a name="ai-vectra-detect-preview"></a>AI Vectra Detect (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** <br><br>[Konfigurieren der CEF-Protokollweiterleitung für AI Vectra Detect](#configure-cef-log-forwarding-for-ai-vectra-detect)|
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Unterstützt von** | [Vectra AI](https://www.vectra.ai/support) |
| | |

### <a name="configure-cef-log-forwarding-for-ai-vectra-detect"></a>Konfigurieren der CEF-Protokollweiterleitung für AI Vectra Detect

Konfigurieren Sie den Vectra-Agent (X-Serie), um Syslog-Nachrichten im CEF-Format über den Log Analytics-Agent an Ihren Microsoft Sentinel-Arbeitsbereich weiterzuleiten.

Navigieren Sie von der Vectra-Schnittstelle zu „Einstellungen > Benachrichtigungen“, und wählen Sie „Syslog-Konfiguration bearbeiten“ aus. Folgen Sie den unten stehenden Anweisungen, um die Verbindung herzustellen:

- Neues Ziel hinzufügen (der Hostname von Protokollweiterleitung)
- Port als **514** festlegen
- Protokoll als **UDP** festlegen
- Format als **CEF** festlegen
- Protokolltypen festlegen (alle verfügbaren Protokolltypen auswählen)
- Wählen Sie **Speichern** aus.

Sie können die Schaltfläche **Test** auswählen, um das Senden einiger Testereignisse an die Protokollweiterleitung zu erzwingen.

Weitere Informationen finden Sie im Syslog-Leitfaden zu Cognito Detect, den Sie auf der Ressourcenseite der Benutzeroberfläche von Detect herunterladen können.

## <a name="akamai-security-events-preview"></a>Akamai Security Events (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** mit einem Kusto-Funktionsparser |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Kusto-Funktionsalias:** | AkamaiSIEMEvent |
| **Kusto-Funktions-URL:** | https://aka.ms/Sentinel-akamaisecurityevents-parser |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [SIEM-Integration (Security Information & Event Management) konfigurieren](https://developer.akamai.com/tools/integrations/siem)<br>[Einrichten eines CEF-Connectors](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector) |
| **Unterstützt von** | [Akamai](https://www.akamai.com/us/en/support/) |
| | |

## <a name="alcide-kaudit"></a>Alcide kAudit

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Microsoft Sentinel-Datensammler-API**](connect-rest-api-template.md) |
| **Log Analytics-Tabellen** | alcide_kaudit_activity_1_CL: Alcide kAudit-Aktivitätsprotokolle<br>alcide_kaudit_detections_1_CL: Alcide kAudit-Erkennungen<br>alcide_kaudit_selections_count_1_CL: Alcide kAudit-Aktivitätsanzahl<br>alcide_kaudit_selections_details_1_CL: Alcide kAudit-Aktivitätsdetails |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Installationsleitfaden für Alcide kAudit](https://awesomeopensource.com/project/alcideio/kaudit?categoryPage=29#before-installing-alcide-kaudit) |
| **Unterstützt von** | [Alcide](https://www.alcide.io/company/contact-us/) |
| | |

## <a name="alsid-for-active-directory"></a>Alsid for Active Directory

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Log Analytics-Agent: benutzerdefinierte Protokolle**](connect-custom-logs.md) <br><br>[Zusätzliche Konfiguration für Alsid](#extra-configuration-for-alsid)|
| **Log Analytics-Tabellen** | AlsidForADLog_CL |
| **Kusto-Funktionsalias:** | afad_parser |
| **Kusto-Funktions-URL:** | https://aka.ms/Sentinel-alsidforad-parser |
| **Unterstützt von** | [Alsid](https://www.alsid.com/contact-us/) |
| | |

### <a name="extra-configuration-for-alsid"></a>Zusätzliche Konfiguration für Alsid

1. **Konfigurieren des Syslog-Servers**

    Sie benötigen zunächst einen **Linux-Syslog-Server**, an den Alsid for AD Protokolle sendet. Unter **Ubuntu** können Sie in der Regel **rsyslog** ausführen. 

    Sie können diesen Server dann wie gewünscht konfigurieren. Es wird jedoch empfohlen, AFAD-Protokolle in eine separate Datei auszugeben. Alternativ können Sie eine [Schnellstartvorlage](https://azure.microsoft.com/resources/templates/alsid-syslog-proxy/) verwenden, um den Syslog-Server und den Microsoft-Agent bereitzustellen. Wenn Sie die Vorlage verwenden, können Sie die [Installationsanweisungen für den Agent](connect-custom-logs.md#install-the-log-analytics-agent) überspringen.

1. **Konfigurieren von Alsid zum Senden von Protokollen an Ihren Syslog-Server**

    Wechseln Sie im **Alsid for AD**-Portal zu **System**, **Konfiguration** und dann **Syslog**. Dort können Sie eine neue Syslog-Warnung für Ihren Syslog-Server erstellen.

    Nachdem Sie eine neue Syslog-Warnung erstellt haben, überprüfen Sie, ob die Protokolle ordnungsgemäß auf Ihrem Server in einer separaten Datei erfasst wurden. Um beispielsweise Ihre Protokolle zu prüfen, können Sie die Schaltfläche *Konfiguration testen* in der Syslog-Warnungskonfiguration in AFAD verwenden. Wenn Sie die Schnellstartvorlage verwendet haben, lauscht der Syslog-Server standardmäßig an Port 514 bei UDP und 1514 bei TCP ohne TLS.

## <a name="amazon-web-services---cloudtrail"></a>Amazon Web Services – CloudTrail

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration: <br>[Verbinden AWS CloudTrail zu Microsoft Sentinel](connect-aws.md)** (Artikel zum wichtigsten Konnektor) |
| **Log Analytics-Tabellen** | AWSCloudTrail |
| **Unterstützt von** | Microsoft |
| | |

## <a name="apache-http-server"></a>Apache HTTP Server

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Log Analytics-Agent: benutzerdefinierte Protokolle**](connect-custom-logs.md) |
| **Log Analytics-Tabellen** | ApacheHTTPServer_CL |
| **Kusto-Funktionsalias:** | ApacheHTTPServer |
| **Kusto-Funktions-URL:** | https://aka.ms/Sentinel-apachehttpserver-parser |
| **Beispieldatei für benutzerdefinierte Protokolle:** | „access.log“ oder „error.log“ |
| | |

## <a name="apache-tomcat"></a>Apache Tomcat

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Log Analytics-Agent: benutzerdefinierte Protokolle**](connect-custom-logs.md) |
| **Log Analytics-Tabellen** | Tomcat_CL |
| **Kusto-Funktionsalias:** | TomcatEvent |
| **Kusto-Funktions-URL:** | https://aka.ms/Sentinel-ApacheTomcat-parser |
| **Beispieldatei für benutzerdefinierte Protokolle:** | „access.log“ oder „error.log“ |
| | |

## <a name="aruba-clearpass-preview"></a>Aruba ClearPass (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** mit einem Kusto-Funktionsparser |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Kusto-Funktionsalias:** | ArubaClearPass |
| **Kusto-Funktions-URL:** | https://aka.ms/Sentinel-arubaclearpass-parser |
| **Herstellerdokumentation/<br>Installationsanweisungen** | Befolgen Sie die Anweisungen von Aruba, um [ClearPass zu konfigurieren](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm). |
| **Unterstützt von** | Microsoft |
| | |

## <a name="atlassian-confluence-audit-preview"></a>Atlassian Confluence Audit (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md) |
| **Log Analytics-Tabellen** | Confluence_Audit_CL |
| **Azure Functions-App-Code** | https://aka.ms/Sentinel-confluenceauditapi-functionapp |
| **API-Anmeldeinformationen** | <li>ConfluenceAccessToken<li>ConfluenceUsername<li>ConfluenceHomeSiteName |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>[API-Dokumentation](https://developer.atlassian.com/cloud/confluence/rest/api-group-audit/)<li>[Anforderungen und Anweisungen zum Abrufen von Anmeldeinformationen](https://developer.atlassian.com/cloud/confluence/rest/intro/#auth)<li>[Anzeigen des Überwachungsprotokolls](https://support.atlassian.com/confluence-cloud/docs/view-the-audit-log/) |
| **Anweisungen zur Connectorbereitstellung** | <li>[Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager)<li>[Manuelle Bereitstellung](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto-Funktionsalias** | ConfluenceAudit |
| **Kusto-Funktions-URL/<br>Anweisungen zur Parserkonfiguration** | https://aka.ms/Sentinel-confluenceauditapi-parser |
| **Anwendungseinstellungen** | <li>ConfluenceUsername<li>ConfluenceAccessToken<li>ConfluenceHomeSiteName<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (optional) |
| **Unterstützt von** | Microsoft |
| | |

## <a name="atlassian-jira-audit-preview"></a>Atlassian Jira Audit (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md) |
| **Log Analytics-Tabellen** | Jira_Audit_CL |
| **Azure Functions-App-Code** | https://aka.ms/Sentinel-jiraauditapi-functionapp |
| **API-Anmeldeinformationen** | <li>JiraAccessToken<li>JiraUsername<li>JiraHomeSiteName |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>[API-Dokumentation: Überwachungsdatensätze](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-audit-records/)<li>[Anforderungen und Anweisungen zum Abrufen von Anmeldeinformationen](https://developer.atlassian.com/cloud/jira/platform/rest/v3/intro/#authentication) |
| **Anweisungen zur Connectorbereitstellung** | <li>[Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager)<li>[Manuelle Bereitstellung](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto-Funktionsalias** | JiraAudit |
| **Kusto-Funktions-URL/<br>Anweisungen zur Parserkonfiguration** | https://aka.ms/Sentinel-jiraauditapi-parser |
| **Anwendungseinstellungen** | <li>JiraUsername<li>JiraAccessToken<li>JiraHomeSiteName<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (optional) |
| **Unterstützt von** | Microsoft |
| | |

## <a name="azure-active-directory"></a>Azure Active Directory

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration: <br>[Verbinden Azure Active Directory daten in Microsoft Sentinel](connect-azure-active-directory.md)** (Artikel zum Top- Konnektor) |
| **Lizenzvoraussetzungen/<br>Preisinformationen** | <li>Azure Active Directory P1- oder P2-Lizenz für Anmeldeprotokolle<li>Eine beliebige Azure AD-Lizenz (Free/O365/P1/P2) für andere Protokolltypen<br>Möglicherweise fallen weitere Gebühren an. |
| **Log Analytics-Tabellen** | SigninLogs<br>AuditLogs<br>AADNonInteractiveUserSignInLogs<br>AADServicePrincipalSignInLogs<br>AADManagedIdentitySignInLogs<br>AADProvisioningLogs<br>ADFSSignInLogs |
| **Unterstützt von** | Microsoft |
| | |

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[API-basierte Verbindungen](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Lizenzvoraussetzungen/<br>Preisinformationen** | [Azure AD Premium P2-Abonnement](https://azure.microsoft.com/pricing/details/active-directory/)<br>Möglicherweise fallen weitere Gebühren an. |
| **Log Analytics-Tabellen** | SecurityAlert |
| **Unterstützt von** | Microsoft |
| | |

## <a name="azure-activity"></a>Azure-Aktivität

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[Auf Diagnoseeinstellungen basierende Verbindungen (von Azure Policy verwaltet)](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)**<br><br>[Aktualisieren auf den neuen Azure-Aktivitätsconnector](#upgrade-to-the-new-azure-activity-connector) |
| **Log Analytics-Tabellen** | AzureActivity |
| **Unterstützt von** | Microsoft |
| | |

### <a name="upgrade-to-the-new-azure-activity-connector"></a>Aktualisieren auf den neuen Azure-Aktivitätsconnector

#### <a name="data-structure-changes"></a>Änderungen der Datenstruktur

Bei diesem Connector wurde kürzlich der Back-End-Mechanismus für das Sammeln von Aktivitätsprotokollereignissen geändert. Er nutzt nun die **Diagnoseeinstellungspipeline**. Wenn Sie weiterhin die Legacymethode für diesen Connector verwenden, wird *dringend empfohlen, ein Upgrade* auf die neue Version durchzuführen, die eine bessere Funktionalität und höhere Konsistenz mit Ressourcenprotokollen bietet. Weitere Informationen finden Sie in den nachstehenden Anweisungen.

Die Methode **Diagnoseeinstellungen** sendet dieselben Daten, die die Legacymethode aus dem Aktivitätsprotokolldienst gesendet hat, obwohl es einige [Änderungen an der Struktur](../azure-monitor/essentials/activity-log.md#data-structure-changes) der **AzureActivity**-Tabelle gegeben hat.

Im Folgenden finden Sie einige der wichtigsten Verbesserungen, die sich aus dem Umstieg auf die Diagnoseeinstellungspipeline ergeben:
- Verbesserte Erfassungslatenz (Ereigniserfassung innerhalb von 2 bis 3 Minuten nach dem Auftreten anstelle von 15 bis 20 Minuten).
- Verbesserte Zuverlässigkeit.
- Diese Ebene bietet eine verbesserte Leistung.
- Unterstützung für alle Kategorien von Ereignissen, die vom Aktivitätsprotokolldienst protokolliert werden (der Legacymechanismus unterstützt nur eine Teilmenge, z. B. gibt es keine Unterstützung für Service Health-Ereignisse).
- Verwaltung im großen Stil mit Azure Policy.

In der [Azure Monitor-Dokumentation](../azure-monitor/logs/data-platform-logs.md) finden Sie eine ausführlichere Behandlung des [Azure-Aktivitätsprotokolls](../azure-monitor/essentials/activity-log.md) und der [Diagnoseeinstellungspipeline](../azure-monitor/essentials/diagnostic-settings.md).

#### <a name="disconnect-from-old-pipeline"></a>Trennen der Verbindung mit der alten Pipeline

Bevor Sie den neuen Connector für das Azure-Aktivitätsprotokoll einrichten, müssen Sie die vorhandenen Abonnements von der Legacymethode trennen.

1. Wählen Sie im Navigationsmenü von Microsoft Sentinel die Option **Daten-Konnektor aus.** Klicken Sie in der Liste mit Connectoren auf **Azure-Aktivität** und dann unten rechts auf die Schaltfläche **Connectorseite öffnen**.

1. Überprüfen Sie unter der Registerkarte **Anweisungen** im Abschnitt **Konfiguration** in Schritt 1 die Liste Ihrer vorhandenen Abonnements, die mit der alten Methode verbunden sind (damit Sie wissen, welche Abonnements Sie der neuen Methode hinzufügen müssen), und trennen Sie diese alle auf einmal, indem Sie unten auf die Schaltfläche **Alle trennen** klicken.

1. Setzen Sie die Einrichtung des neuen Connectors mit den [Anweisungen in der obigen Tabelle](connect-azure-windows-microsoft-services.md#diagnostic-settings-based-connections) fort.

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[Auf Diagnoseeinstellungen basierende Verbindungen](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **Lizenzvoraussetzungen/<br>Preisinformationen** | <li>Sie müssen über einen konfigurierten [Azure DDoS Protection-Standardplan](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan) verfügen.<li>Sie müssen über ein konfiguriertes [virtuelles Netzwerk mit Aktivierung von Azure DDoS Standard](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network) verfügen.<br>Möglicherweise fallen weitere Gebühren an. |
| **Log Analytics-Tabellen** | AzureDiagnostics |
| **Empfohlene Diagnose** | DDoSProtectionNotifications<br>DDoSMitigationFlowLogs<br>DDoSMitigationReports |
| **Unterstützt von** | Microsoft |
| | |

## <a name="microsoft-defender-for-cloud"></a>Microsoft Defender für Cloud

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration: <br>[Verbinden von Microsoft Defender for Cloud](connect-azure-security-center.md)** gesendete Sicherheitswarnungen (Artikel zum Top-Konnektor) |
| **Log Analytics-Tabellen** | SecurityAlert |
| **Unterstützt von** | Microsoft |
| | |

<a name="azure-defender-for-iot"></a>

## <a name="microsoft-defender-for-iot"></a>Microsoft Defender für IoT

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[API-basierte Verbindungen](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Log Analytics-Tabellen** | SecurityAlert |
| **Unterstützt von** | Microsoft |
| | |

## <a name="azure-firewall"></a>Azure Firewall

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[Auf Diagnoseeinstellungen basierende Verbindungen](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **Log Analytics-Tabellen** | AzureDiagnostics |
| **Empfohlene Diagnose** | AzureFirewallApplicationRule<br>AzureFirewallNetworkRule<br>AzureFirewallDnsProxy |
| **Unterstützt von** | Microsoft |
| | |

## <a name="azure-information-protection"></a>Azure Information Protection

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure-Dienst-zu-Dienst-Integration**](connect-azure-windows-microsoft-services.md) |
| **Log Analytics-Tabellen** | InformationProtectionLogs_CL |
| **Unterstützt von** | Microsoft |
| | |

Weitere Informationen finden Sie in der [Dokumentation zu Azure Information Protection](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries).

## <a name="azure-key-vault"></a>Azure-Schlüsseltresor

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[Auf Diagnoseeinstellungen basierende Verbindungen (von Azure Policy verwaltet)](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** |
| **Log Analytics-Tabellen** | KeyVaultData |
| **Unterstützt von** | Microsoft |
| | |

## <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[Auf Diagnoseeinstellungen basierende Verbindungen (von Azure Policy verwaltet)](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** |
| **Log Analytics-Tabellen** | kube-apiserver<br>kube-audit<br>kube-audit-admin<br>kube-controller-manager<br>kube-scheduler<br>cluster-autoscaler<br>guard |
| **Unterstützt von** | Microsoft |
| | |

## <a name="azure-sql-databases"></a>Azure SQL-Datenbank-Instanzen

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[Auf Diagnoseeinstellungen basierende Verbindungen (von Azure Policy verwaltet)](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** <br><br>Auch in Azure SQL [und Microsoft Sentinel für SQL PaaS-Lösungen verfügbar](sentinel-solutions-catalog.md#azure)|
| **Log Analytics-Tabellen** | SQLSecurityAuditEvents<br>SQLInsights<br>AutomaticTuning<br>QueryStoreWaitStatistics<br>Errors<br>DatabaseWaitStatistics<br>Timeouts<br>Blöcke<br>Deadlocks<br>Basic<br>InstanceAndAppAdvanced<br>WorkloadManagement<br>DevOpsOperationsAudit |
| **Unterstützt von** | Microsoft |
| | |

## <a name="azure-storage-account"></a>Azure Storage-Konto

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[Auf Diagnoseeinstellungen basierende Verbindungen](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)**<br><br>[Hinweise zur Konfiguration von Diagnoseeinstellungen für Speicherkonten](#notes-about-storage-account-diagnostic-settings-configuration) |
| **Log Analytics-Tabellen** | StorageBlobLogs<br>StorageQueueLogs<br>StorageTableLogs<br>StorageFileLogs |
| **Empfohlene Diagnose** | **Kontoressource**<li>Transaktion<br>**Blob-/Warteschlangen-/Tabellen-/Dateiressourcen**<br><li>StorageRead<li>StorageWrite<li>StorageDelete<li>Transaktion |
| **Unterstützt von** | Microsoft |
| | |

### <a name="notes-about-storage-account-diagnostic-settings-configuration"></a>Hinweise zur Konfiguration von Diagnoseeinstellungen für Speicherkonten

Die (übergeordnete) Speicherkontoressource enthält andere (untergeordnete) Ressourcen für jeden Speichertyp: Dateien, Tabellen, Warteschlangen und Blobs.

Beim Konfigurieren von Diagnosen für ein Speicherkonto müssen Sie wiederum Folgendes auswählen und konfigurieren:
- Die übergeordnete Kontoressource, die die **Transaktions** metrik exportiert.
- Alle untergeordneten Speichertypressourcen, die alle Protokolle und Metriken exportieren (siehe Tabelle oben).

Es werden nur die Speichertypen angezeigt, für die Sie tatsächlich Ressourcen definiert haben.

## <a name="azure-web-application-firewall-waf"></a>Azure Web Application Firewall (WAF)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[Auf Diagnoseeinstellungen basierende Verbindungen](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **Log Analytics-Tabellen** | AzureDiagnostics |
| **Empfohlene Diagnose** | **Application Gateway**<br><li>ApplicationGatewayAccessLog<li>ApplicationGatewayFirewallLog<br>**Front Door**<li>FrontdoorAccessLog<li>FrontdoorWebApplicationFirewallLog<br>**CDN-WAF-Richtlinie**<li>WebApplicationFirewallLogs |
| **Unterstützt von** | Microsoft |
| | |


## <a name="barracuda-cloudgen-firewall"></a>Barracuda CloudGen Firewall

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**syslog**](connect-syslog.md) |
| **Log Analytics-Tabellen** | syslog |
| **Kusto-Funktionsalias:** | CGFWFirewallActivity |
| **Kusto-Funktions-URL:** | https://aka.ms/Sentinel-barracudacloudfirewall-function |
| **Herstellerdokumentation/<br>Installationsanweisungen** | https://aka.ms/Sentinel-barracudacloudfirewall-connector |
| **Unterstützt von** | [Barracuda](https://www.barracuda.com/support) |
| | |

## <a name="barracuda-waf"></a>Barracuda WAF

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Log Analytics-Agent: benutzerdefinierte Protokolle**](connect-custom-logs.md) |
| **Log Analytics-Tabellen** | syslog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | https://aka.ms/asi-barracuda-connector |
| **Unterstützt von** | [Barracuda](https://www.barracuda.com/support) |
| | |

## <a name="better-mobile-threat-defense-mtd-preview"></a>BETTER Mobile Threat Defense (MTD) (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Microsoft Sentinel-Datensammler-API**](connect-rest-api-template.md) |
| **Log Analytics-Tabellen** | BetterMTDDeviceLog_CL<br>BetterMTDIncidentLog_CL<br>BetterMTDAppLog_CL<br>BetterMTDNetflowLog_CL |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Dokumentation zu BETTER MTD](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration)<br><br>Einrichtung der Bedrohungsrichtlinie, die die Vorfälle definiert, die an Microsoft Sentinel gemeldet werden:<br><ol><li>Wählen Sie in der **BETTER MTD-Konsole** auf der Seitenleiste **Policies** (Richtlinien) aus.<li>Wählen Sie die Schaltfläche **Edit** (Bearbeiten) für die von Ihnen verwendete Richtlinie aus.<li>Wechseln Sie für jeden Vorfallstyp, den Sie protokollieren möchten, zum Feld **Send to Integrations** (An Integrationen senden), und wählen Sie **Sentinel** aus. |
| **Unterstützt von** | [Better Mobile](mailto:support@better.mobi) |
| | |


## <a name="beyond-security-besecure"></a>Beyond Security beSECURE

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Microsoft Sentinel-Datensammler-API**](connect-rest-api-template.md) |
| **Log Analytics-Tabellen** | beSECURE_ScanResults_CL<br>beSECURE_ScanEvents_CL<br>beSECURE_Audit_CL |
| **Herstellerdokumentation/<br>Installationsanweisungen** | Zugreifen auf das Menü **Integration**:<br><ol><li>Wählen Sie die Menüoption **More** (Weitere) aus.<li>Wählen Sie **Server** aus.<li>Wählen Sie **Integration** aus.<li>Aktivieren von Microsoft Sentinel<li>Fügen Sie die Werte für die **Arbeitsbereichs-ID** und den **Primärschlüssel** aus der beSECURE-Konfiguration ein.<li>Wählen Sie **Ändern** aus. |
| **Unterstützt von** | [Beyond Security](https://beyondsecurity.freshdesk.com/support/home) |
| | |


## <a name="blackberry-cylanceprotect-preview"></a>BlackBerry CylancePROTECT (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**syslog**](connect-syslog.md) |
| **Log Analytics-Tabellen** | syslog |
| **Kusto-Funktionsalias:** | CylancePROTECT |
| **Kusto-Funktions-URL:** | https://aka.ms/Sentinel-cylanceprotect-parser |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Syslog-Leitfaden für Cylance](https://docs.blackberry.com/content/dam/docs-blackberry-com/release-pdfs/en/cylance-products/syslog-guides/Cylance%20Syslog%20Guide%20v2.0%20rev12.pdf) |
| **Unterstützt von** | Microsoft |
| | |

## <a name="broadcom-symantec-data-loss-prevention-dlp-preview"></a>Broadcom Symantec Data Loss Prevention (DLP) (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** mit einem Kusto-Funktionsparser |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Kusto-Funktionsalias:** | SymantecDLP |
| **Kusto-Funktions-URL:** | https://aka.ms/Sentinel-symantecdlp-parser |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Konfigurieren der Aktion zur Protokollierung auf einem Syslog-Server](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US) |
| **Unterstützt von** | Microsoft |
| | |

## <a name="check-point"></a>Check Point

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** <br><br>Verfügbar von der [Check Point Lösung](sentinel-solutions-catalog.md#check-point)|
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Log Exporter: Check Point-Protokollexport](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk122323) |
| **Unterstützt von** | [Check Point](https://www.checkpoint.com/support-services/contact-support/) |
| | |


## <a name="cisco-asa"></a>Cisco ASA

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** <br><br>Verfügbar in der [Cisco ASA Lösung](sentinel-solutions-catalog.md#cisco)|
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [CLI-Konfigurationshandbuch für die Cisco ASA-Serie](https://www.cisco.com/c/en/us/support/docs/security/pix-500-series-security-appliances/63884-config-asa-00.html) |
| **Unterstützt von** | Microsoft |
| | |


## <a name="cisco-firepower-estreamer-preview"></a>Cisco Firepower eStreamer (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** <br><br>[Zusätzliche Konfiguration für Cisco Firepower eStreamer](#extra-configuration-for-cisco-firepower-estreamer)|
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Betriebsleitfaden für eStreamer eNcore für Sentinel](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html) |
| **Unterstützt von** | [Cisco](https://www.cisco.com/c/en/us/support/index.html)
| | |

### <a name="extra-configuration-for-cisco-firepower-estreamer"></a>Zusätzliche Konfiguration für Cisco Firepower eStreamer

1. **Installieren des Firepower eNcore-Clients**  
Installieren und konfigurieren Sie den Firepower eNcore eStreamer-Client. Weitere Informationen finden Sie im [vollständigen Installationsleitfaden von Cisco](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html).

1. **Herunterladen des Firepower-Connectors von GitHub**  
Laden Sie die neueste Version des Firepower eNcore-Konnektors für Microsoft Sentinel aus dem [Cisco GitHub-Repository herunter.](https://github.com/CiscoSecurity/fp-05-microsoft-sentinel-connector) Wenn Sie Python3 verwenden möchten, nutzen Sie den [eStreamer-Connector für Python3](https://github.com/CiscoSecurity/fp-05-microsoft-sentinel-connector/tree/python3).

1. **Erstellen einer PKCS12-Datei mithilfe der Azure-/VM-IP-Adresse**  
Erstellen Sie unter Verwendung der öffentlichen IP-Adresse der VM-Instanz ein PKCS12-Zertifikat. Wechseln Sie hierzu in FirePower zu **System > Integration > eStreamer**. Weitere Informationen finden Sie im [Installationsleitfaden](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049443).

1. **Testen der Konnektivität zwischen Azure/VM-Client und dem FMC**  
Kopieren Sie die PKCS12-Datei aus dem FMC auf die Azure/VM-Instanz, und stellen Sie mithilfe des Testhilfsprogramms (./encore.sh test) sicher, dass eine Verbindung hergestellt werden kann. Weitere Informationen finden Sie im [Einrichtungsleitfaden](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049430).

1. **Konfigurieren von eNcore zum Streamen von Daten an den Agent**  
Konfigurieren Sie eNcore für das Streamen von Daten über TCP an den Log Analytics-Agent. Diese Konfiguration sollte standardmäßig aktiviert sein, aber abhängig von Ihrem Netzwerksicherheitsstatus können zusätzliche Ports und Streamingprotokolle konfiguriert werden. Es ist auch möglich, die Daten im Dateisystem zu speichern. Weitere Informationen finden Sie unter [Konfigurieren von eNcore](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049433).


## <a name="cisco-meraki-preview"></a>Cisco Meraki (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**syslog**](connect-syslog.md)<br><br> Verfügbar in der [Cisco ISE Lösung](sentinel-solutions-catalog.md#cisco)|
| **Log Analytics-Tabellen** | syslog |
| **Kusto-Funktionsalias:** | CiscoMeraki |
| **Kusto-Funktions-URL:** | https://aka.ms/Sentinel-ciscomeraki-parser |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Dokumentation zu Meraki Device Reporting](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) |
| **Unterstützt von** | Microsoft |
| | |


## <a name="cisco-umbrella-preview"></a>Cisco Umbrella (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md) <br><br> Verfügbar in der [Cisco Umbrella Lösung](sentinel-solutions-catalog.md#cisco)|
| **Log Analytics-Tabellen** | Cisco_Umbrella_dns_CL<br>Cisco_Umbrella_proxy_CL<br>Cisco_Umbrella_ip_CL<br>Cisco_Umbrella_cloudfirewall_CL |
| **Azure Functions-App-Code** | https://aka.ms/Sentinel-CiscoUmbrellaConn-functionapp |
| **API-Anmeldeinformationen** | <li>ID des AWS-Zugriffsschlüssels<li>Geheimnis für den AWS-Zugriffsschlüssel<li>Name des AWS S3-Buckets |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>[Protokollierung bei Amazon S3](https://docs.umbrella.com/deployment-umbrella/docs/log-management#section-logging-to-amazon-s-3) |
| **Anweisungen zur Connectorbereitstellung** | <li>[Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager)<li>[Manuelle Bereitstellung](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto-Funktionsalias** | Cisco_Umbrella |
| **Kusto-Funktions-URL/<br>Anweisungen zur Parserkonfiguration** | https://aka.ms/Sentinel-ciscoumbrella-function |
| **Anwendungseinstellungen** | <li>WorkspaceID<li>WorkspaceKey<li>S3Bucket<li>AWSAccessKeyId<li>AWSSecretAccessKey<li>logAnalyticsUri (optional) |
| **Unterstützt von** | Microsoft |
| | |

## <a name="cisco-unified-computing-system-ucs-preview"></a>Cisco Unified Computing System (UCS) (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**syslog**](connect-syslog.md) |
| **Log Analytics-Tabellen** | syslog |
| **Kusto-Funktionsalias:** | CiscoUCS |
| **Kusto-Funktions-URL:** | https://aka.ms/Sentinel-ciscoucs-function |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Einrichten von Syslog für Cisco UCS – Cisco](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog) |
| **Unterstützt von** | Microsoft |
| | |


## <a name="citrix-analytics-security"></a>Citrix Analytics (Security)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Microsoft Sentinel-Datensammler-API**](connect-rest-api-template.md) |
| **Log Analytics-Tabellen** | CitrixAnalytics_SAlerts_CL |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Verbinden Citrix zu Microsoft Sentinel](https://aka.ms/Sentinel-Citrix-Connector) |
| **Unterstützt von** | [Citrix Systems](https://www.citrix.com/support/) |
| | |

## <a name="citrix-web-app-firewall-waf-preview"></a>Citrix Web App Firewall (WAF) (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | Informationen zum Konfigurieren von WAF finden Sie unter [Support-WIKI: WAF-Konfiguration mit NetScaler](https://support.citrix.com/article/CTX234174).<br><br>Weitere Informationen zum Konfigurieren von CEF-Protokollen finden Sie unter [Unterstützung für CEF-Protokollierung (Common Event Format) in der Anwendungsfirewall](https://support.citrix.com/article/CTX136146).<br><br>Informationen zum Weiterleiten der Protokolle an den Proxy finden Sie unter [Konfigurieren der Citrix ADC-Appliance für Überwachungsprotokollierung](https://docs.citrix.com/en-us/citrix-adc/current-release/system/audit-logging/configuring-audit-logging.html). |
| **Unterstützt von** | [Citrix Systems](https://www.citrix.com/support/) |
| | |


## <a name="cognni-preview"></a>Cognni (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Microsoft Sentinel-Datensammler-API**](connect-rest-api-template.md) |
| **Log Analytics-Tabellen** | CognniIncidents_CL |
| **Herstellerdokumentation/<br>Installationsanweisungen** | **Herstellen einer Verbindung mit Cognni**<br><ol><li>Navigieren Sie zur [Integrationsseite von Cognni](https://intelligence.cognni.ai/integrations).<li>Wählen Sie **Verbinden** im Feld Microsoft Sentinel aus.<li>Fügen Sie auf der Integrationsseite von Cognni die **workspaceId** und den **sharedKey** (Primärschlüssel) in die entsprechenden Felder ein.<li>Wählen Sie die Schaltfläche **Connect** (Verbinden) aus, um die Konfiguration abzuschließen. |
| **Unterstützt von** | [Cognni](https://cognni.ai/contact-support/)
| | |

## <a name="continuous-threat-monitoring-for-sap-preview"></a>Kontinuierliche Bedrohungsüberwachung für SAP (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | Nur verfügbar nach der Installation der [kontinuierlichen Bedrohungsüberwachung für SAP-Lösung](sentinel-solutions-catalog.md#sap)|
| **Log Analytics-Tabellen** | Weitere Informationen finden Sie in der Referenz zu [SAP-Lösungsprotokollen in Microsoft Sentinel.](sap-solution-log-reference.md) |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Bereitstellen der kontinuierlichen Bedrohungsüberwachung in SAP](sap-deploy-solution.md) |
| **Unterstützt von** | Microsoft |
| | |


## <a name="cyberark-enterprise-password-vault-epv-events-preview"></a>CyberArk Enterprise Password Vault (EPV) Events (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [SIEM-Anwendungen (Security Information & Event Management)](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm) |
| **Unterstützt von** | [CyberArk](https://www.cyberark.com/customer-support/) |
| | |


## <a name="cyberpion-security-logs-preview"></a>Cyberpion Security Logs (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Microsoft Sentinel-Datensammler-API**](connect-rest-api-template.md) |
| **Log Analytics-Tabellen** | CyberpionActionItems_CL |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Erwerben eines Cyberpion-Abonnements](https://azuremarketplace.microsoft.com/en/marketplace/apps/cyberpion1597832716616.cyberpion)<br>[Integrieren von Cyberpion-Sicherheitswarnungen in Microsoft Sentinel](https://www.cyberpion.com/resource-center/integrations/azure-sentinel/) |
| **Unterstützt von** | [Cyberpion](https://www.cyberpion.com/) |
| | |



## <a name="dns-preview"></a>DNS (Vorschau)

**Siehe [Windows DNS Server (Vorschau)](#windows-dns-server-preview).**

## <a name="dynamics-365"></a>Dynamics 365

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[API-basierte Verbindungen](connect-azure-windows-microsoft-services.md#api-based-connections)** <br><br> Auch als Teil der [Microsoft Sentinel 4 Dynamics 365-Lösung](sentinel-solutions-catalog.md#azure) verfügbar|
| **Lizenzvoraussetzungen/<br>Preisinformationen** | <li>[Microsoft Dynamics 365-Produktionslizenz](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description). Nicht für Sandboxumgebungen verfügbar.<li>Für die Aktivitätsprotokollierung ist ein Microsoft 365 Enterprise [E3- oder E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements)-Abonnement erforderlich.<br>Möglicherweise fallen weitere Gebühren an. |
| **Log Analytics-Tabellen** | Dynamics365Activity |
| **Unterstützt von** | Microsoft |
| | |

## <a name="eset-enterprise-inspector-preview"></a>ESET Enterprise Inspector (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md)<br><br>[Erstellen eines API-Benutzers](#create-an-api-user) |
| **Log Analytics-Tabellen** | ESETEnterpriseInspector_CL |
| **API-Anmeldeinformationen** | <li>EEI-Benutzername<li>EEI-Kennwort<li>Basis-URL |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>[REST-API-Dokumentation für ESET Enterprise Inspector](https://help.eset.com/eei/1.5/en-US/api.html) |
| **Anweisungen zur Connectorbereitstellung** | [Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager) |
| **Unterstützt von** | [ESET](https://support.eset.com/en) |
| | |
### <a name="create-an-api-user"></a>Erstellen eines API-Benutzers

1. Melden Sie sich im ESET Security Management Center mit einem Administratorkonto an der ESET PROTECT-Konsole an, und wählen Sie die Registerkarte **More** (Mehr) und die Unterregisterkarte **Users** (Benutzer) aus.
1. Wählen Sie die Schaltfläche **ADD NEW** (NEU HINZUFÜGEN) aus, um einen **nativen Benutzer** hinzuzufügen.
1. Erstellen Sie einen neuen Benutzer für das API-Konto. **Optional:** Wählen Sie eine andere **Home group** (Startgruppe) als **All** (Alle) aus, um die erfassten Erkennungen zu begrenzen.
1. Weisen Sie auf der Registerkarte **Permission Sets** (Berechtigungssätze) den Berechtigungssatz **Enterprise Inspector reviewer** (Enterprise Inspector-Prüfer) zu.
1. Melden Sie sich beim Administratorkonto ab, und melden Sie sich zur Überprüfung mit den neuen API-Anmeldeinformationen an der Konsole an. Melden Sie sich nach der Überprüfung beim API-Konto ab.

## <a name="eset-security-management-center-smc-preview"></a>ESET Security Management Center (SMC) (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**syslog**](connect-syslog.md)<br><br>[Konfigurieren der zu erfassenden ESET SMC-Protokolle](#configure-the-eset-smc-logs-to-be-collected) <br>[Konfigurieren des OMS-Agents, sodass ESET SMC-Daten im API-Format übergeben werden](#configure-oms-agent-to-pass-eset-smc-data-in-api-format)<br>[Ändern der OMS-Agent-Konfiguration für das Abfangen des Tags „oms.api.eset“ und das Analysieren strukturierter Daten](#change-oms-agent-configuration-to-catch-tag-omsapieset-and-parse-structured-data)<br>[Deaktivieren der automatische Konfiguration und Neustarten des Agents](#disable-automatic-configuration-and-restart-agent)|
| **Log Analytics-Tabellen** | eset_CL |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Dokumentation zum ESET-Syslog-Server](https://help.eset.com/esmc_admin/70/en-US/admin_server_settings_syslog.html) |
| **Unterstützt von** | [ESET](https://support.eset.com/en) |
| | |

### <a name="configure-the-eset-smc-logs-to-be-collected"></a>Konfigurieren der zu erfassenden ESET SMC-Protokolle

Konfigurieren Sie rsyslog so, dass Protokolle von Ihrer ESET-SMC-IP-Adresse akzeptiert werden.

```bash
    sudo -i
    # Set ESET SMC source IP address
    export ESETIP={Enter your IP address}

    # Create rsyslog configuration file
    cat > /etc/rsyslog.d/80-remote.conf << EOF
    \$ModLoad imudp
    \$UDPServerRun 514
    \$ModLoad imtcp
    \$InputTCPServerRun 514
    \$AllowedSender TCP, 127.0.0.1, $ESETIP
    \$AllowedSender UDP, 127.0.0.1, $ESETIP user.=alert;user.=crit;user.=debug;user.=emerg;user.=err;user.=info;user.=notice;user.=warning  @127.0.0.1:25224
    EOF

    # Restart rsyslog
    systemctl restart rsyslog
```

### <a name="configure-oms-agent-to-pass-eset-smc-data-in-api-format"></a>Konfigurieren des OMS-Agents, sodass ESET SMC-Daten im API-Format übergeben werden

Um Eset-Daten leicht erkennen zu können, pushen Sie sie in eine separate Tabelle, und analysieren Sie sie beim Agent, um Ihre Microsoft Sentinel-Abfrage zu vereinfachen und zu beschleunigen. 

Ändern Sie in der Datei **/etc/opt/microsoft/omsagent/{IhreArbeitsbereichsID}/conf/omsagent.conf** den Abschnitt `match oms.**` so, dass Daten als API-Objekte gesendet werden. Ändern Sie dazu den Typ in `out_oms_api`.
    
Der folgende Code ist ein Beispiel des vollständigen `match oms.**` Abschnitts:

```bash
    <match oms.** docker.**>
      type out_oms_api
      log_level info
      num_threads 5
      run_in_background false

      omsadmin_conf_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/conf/omsadmin.conf
      cert_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/certs/oms.crt
      key_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/certs/oms.key

      buffer_chunk_limit 15m
      buffer_type file
      buffer_path /var/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/state/out_oms_common*.buffer

      buffer_queue_limit 10
      buffer_queue_full_action drop_oldest_chunk
      flush_interval 20s
      retry_limit 10
      retry_wait 30s
      max_retry_wait 9m
    </match>
```

### <a name="change-oms-agent-configuration-to-catch-tag-omsapieset-and-parse-structured-data"></a>Ändern der OMS-Agent-Konfiguration für das Abfangen des Tags „oms.api.eset“ und das Analysieren strukturierter Daten

Ändern Sie die Datei **/etc/opt/microsoft/omsagent/{IhreArbeitsbereichsID}/conf/omsagent.d/syslog.conf**. 

Beispiel:

```bash
    <source>
      type syslog
      port 25224
      bind 127.0.0.1
      protocol_type udp
      tag oms.api.eset
    </source>

    <filter oms.api.**>
      @type parser
      key_name message
      format /(?<message>.*?{.*})/
    </filter>

    <filter oms.api.**>
      @type parser
      key_name message
      format json
    </filter>
```
### <a name="disable-automatic-configuration-and-restart-agent"></a>Deaktivieren der automatische Konfiguration und Neustarten des Agents

Beispiel: 

```bash
    # Disable changes to configuration files from Portal
    sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'

    # Restart agent
    sudo /opt/microsoft/omsagent/bin/service_control restart

    # Check agent logs
    tail -f /var/opt/microsoft/omsagent/log/omsagent.log
```

### <a name="configure-eset-smc-to-send-logs-to-connector"></a>Konfigurieren von ESET SMC zum Senden von Protokollen an den Connector

Konfigurieren Sie ESET-Protokolle im BSD-Stil und im JSON-Format.

- Wechseln Sie zur Syslog-Serverkonfiguration, konfigurieren Sie den Host (Ihr Connector), ändern Sie das Format in „BSD“ und legen Sie als Übertragungsprotokoll „TCP“ fest.
- Wechseln Sie zum Abschnitt „Logging“ (Protokollierung), und aktivieren Sie „JSON“.

Weitere Informationen finden Sie in der ESET-Dokumentation.

## <a name="exabeam-advanced-analytics-preview"></a>Exabeam Advanced Analytics (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**syslog**](connect-syslog.md) |
| **Log Analytics-Tabellen** | syslog |
| **Kusto-Funktionsalias:** | ExabeamEvent |
| **Kusto-Funktions-URL:** | https://aka.ms/Sentinel-Exabeam-parser |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Konfigurieren von Advanced Analytics-Systemaktivitätsbenachrichtigungen](https://docs.exabeam.com/en/advanced-analytics/i54/advanced-analytics-administration-guide/113254-configure-advanced-analytics.html#UUID-7ce5ff9d-56aa-93f0-65de-c5255b682a08) |
| **Unterstützt von** | Microsoft |
| | |

## <a name="extrahop-revealx"></a>ExtraHop Reveal(x)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [ExtraHop Detection-SIEM-Connector](https://aka.ms/asi-syslog-extrahop-forwarding) |
| **Unterstützt von** | [ExtraHop](https://www.extrahop.com/support/) |
| | |

## <a name="f5-big-ip"></a>F5 BIG-IP

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Microsoft Sentinel-Datensammler-API**](connect-rest-api-template.md) |
| **Log Analytics-Tabellen** | F5Telemetry_LTM_CL<br>F5Telemetry_system_CL<br>F5Telemetry_ASM_CL |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Integrieren von F5 BIG-IP in Microsoft Sentinel](https://aka.ms/F5BigIp-Integrate) |
| **Unterstützt von** | [F5 Networks](https://support.f5.com/csp/home) |
| | |
## <a name="f5-networks-asm"></a>F5 Networks (ASM)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Konfigurieren der Ereignisprotokollierung zur Anwendungssicherheit](https://aka.ms/asi-syslog-f5-forwarding) |
| **Unterstützt von** | [F5 Networks](https://support.f5.com/csp/home) |
| | |



## <a name="forcepoint-cloud-access-security-broker-casb-preview"></a>Forcepoint Cloud Access Security Broker (CASB) (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Forcepoint CASB und Microsoft Sentinel](https://forcepoint.github.io/docs/casb_and_azure_sentinel/) |
| **Unterstützt von** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forcepoint-cloud-security-gateway-csg-preview"></a>Forcepoint Cloud Security Gateway (CSG) (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Forcepoint Cloud Security Gateway und Microsoft Sentinel](https://forcepoint.github.io/docs/csg_and_sentinel/) |
| **Unterstützt von** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forcepoint-data-loss-prevention-dlp-preview"></a>Forcepoint Data Loss Prevention (DLP) (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Microsoft Sentinel-Datensammler-API**](connect-rest-api-template.md) |
| **Log Analytics-Tabellen** | ForcepointDLPEvents_CL |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Verhinderung von Datenverlust durch Forcepoint und Microsoft Sentinel](https://forcepoint.github.io/docs/dlp_and_azure_sentinel/) |
| **Unterstützt von** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forcepoint-next-generation-firewall-ngfw-preview"></a>Forcepoint Next Generation Firewall (NGFW) (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Forcepoint Next-Gen-Firewall und Microsoft Sentinel](https://forcepoint.github.io/docs/ngfw_and_azure_sentinel/) |
| **Unterstützt von** | [Forcepoint](https://support.forcepoint.com/) |
| | |



## <a name="forgerock-common-audit-caud-for-cef-preview"></a>ForgeRock Common Audit (CAUD) für CEF (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Installieren Sie dies zuerst! ForgeRock Common Audit (CAUD) für Microsoft Sentinel](https://github.com/javaservlets/SentinelAuditEventHandler) |
| **Unterstützt von** | [ForgeRock](https://www.forgerock.com/support) |
| | |

## <a name="fortinet"></a>Fortinet

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** <br><br>[Senden von Fortinet-Protokollen an die Protokollweiterleitung](#send-fortinet-logs-to-the-log-forwarder) <br<br>Verfügbar in der [Fortinet Fortigate-Lösung](sentinel-solutions-catalog.md#fortinet-fortigate)|
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Fortinet-Dokumentbibliothek](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)<br>Wählen Sie Ihre Version aus, und nutzen Sie die PDFs mit dem Handbuch (*Handbook*) und der Referenz für Protokollmeldungen (*Log Message Reference*). |
| **Unterstützt von** | [Fortinet](https://support.fortinet.com/) |
| | |

### <a name="send-fortinet-logs-to-the-log-forwarder"></a>Senden von Fortinet-Protokollen an die Protokollweiterleitung

Öffnen Sie die Befehlszeilenschnittstelle für Ihre Fortinet-Appliance, und führen Sie die folgenden Befehle aus:

```Console
config log syslogd setting
set status enable
set format cef
set port 514
set server <ip_address_of_Forwarder>
end
```

- Ersetzen Sie die **IP-Adresse** des Servers durch die IP-Adresse der Protokollweiterleitung.
- Legen Sie den **Syslog-Port** auf **514** fest oder auf den Port, den Sie für den Syslog-Daemon in der Weiterleitung festgelegt haben.
- In frühen Versionen von FortiOS muss zum Aktivieren des CEF-Formats ggf. der Befehl **set csv disable** ausgeführt werden.



## <a name="google-workspace-g-suite-preview"></a>Google Workspace (G-Suite) (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md)<br><br>[Zusätzliche Konfiguration für die Google Reports-API](#extra-configuration-for-the-google-reports-api) |
| **Log Analytics-Tabellen** | GWorkspace_ReportsAPI_admin_CL<br>GWorkspace_ReportsAPI_calendar_CL<br>GWorkspace_ReportsAPI_drive_CL<br>GWorkspace_ReportsAPI_login_CL<br>GWorkspace_ReportsAPI_mobile_CL<br>GWorkspace_ReportsAPI_token_CL<br>GWorkspace_ReportsAPI_user_accounts_CL<br> |
| **Azure Functions-App-Code** | https://aka.ms/Sentinel-GWorkspaceReportsAPI-functionapp |
| **API-Anmeldeinformationen** | <li>GooglePickleString |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>[API-Dokumentation](https://developers.google.com/admin-sdk/reports/v1/reference/activities)<li>Rufen Sie die Anmeldeinformationen unter [Ausführen von domänenweiter Google Workspace-Delegierung der Autorität](https://developers.google.com/admin-sdk/reports/v1/guides/delegation) ab.<li>[Konvertieren der Datei „token.pickle“ in eine pickle-Zeichenfolge](https://aka.ms/sentinel-GWorkspaceReportsAPI-functioncode) |
| **Anweisungen zur Connectorbereitstellung** | <li>[Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager)<li>[Manuelle Bereitstellung](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto-Funktionsalias** | GWorkspaceActivityReports |
| **Kusto-Funktions-URL/<br>Anweisungen zur Parserkonfiguration** | https://aka.ms/Sentinel-GWorkspaceReportsAPI-parser |
| **Anwendungseinstellungen** | <li>GooglePickleString<li>WorkspaceID<li>workspaceKey<li>logAnalyticsUri (optional) |
| **Unterstützt von** | Microsoft |
| | |

### <a name="extra-configuration-for-the-google-reports-api"></a>Zusätzliche Konfiguration für die Google Reports-API

Fügen Sie unter **Authorized redirect URIs** (Autorisierte Umleitungs-URIs) den Eintrag http://localhost:8081/ hinzu, während Sie die [Anmeldeinformationen für Webanwendungen](https://developers.google.com/workspace/guides/create-credentials#web) erstellen.

1. [Befolgen Sie die Anweisungen](https://developers.google.com/admin-sdk/reports/v1/quickstart/python) zum Abrufen der Datei „credentials.json“.
1. Um die pickle-Zeichenfolge von Google abzurufen, führen Sie [dieses Python-Skript](https://aka.ms/sentinel-GWorkspaceReportsAPI-functioncode) (im gleichen Pfad wie „credentials.json“) aus.
1. Kopieren Sie die Ausgabe der pickle-Zeichenfolge in einfache Anführungszeichen, und speichern Sie sie. Sie wird für die Bereitstellung der Funktions-App benötigt.


## <a name="illusive-attack-management-system-ams-preview"></a>Illusive Attack Management System (AMS) (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Administratorleitfaden für Illusive Networks](https://support.illusivenetworks.com/hc/en-us/sections/360002292119-Documentation-by-Version) |
| **Unterstützt von** | [Illusive Networks](https://www.illusivenetworks.com/technical-support/) |
| | |

## <a name="imperva-waf-gateway-preview"></a>Imperva WAF Gateway (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** <br><br>Verfügbar in der [Imperva Cloud WAF Lösung](sentinel-solutions-catalog.md#imperva)|
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Schritte zum Aktivieren der Imperva WAF Gateway-Alarm-Protokollierung in Microsoft Sentinel](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert) |
| **Unterstützt von** | [Imperva](https://www.imperva.com/support/technical-support/) |
| | |


## <a name="infoblox-network-identity-operating-system-nios-preview"></a>Infoblox Network Identity Operating System (NIOS) (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**syslog**](connect-syslog.md)<br><br> verfügbar in der [InfoBlox Bedrohungsabwehr-Lösung](sentinel-solutions-catalog.md#infoblox) |
| **Log Analytics-Tabellen** | syslog |
| **Kusto-Funktionsalias:** | InfobloxNIOS |
| **Kusto-Funktions-URL:** | https://aka.ms/sentinelgithubparsersinfoblox |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Syslog-Bereitstellungsleitfaden für NIOS SNMP](https://www.infoblox.com/wp-content/uploads/infoblox-deployment-guide-slog-and-snmp-configuration-for-nios.pdf) |
| **Unterstützt von** | Microsoft |
| | |




## <a name="juniper-srx-preview"></a>Juniper SRX (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**syslog**](connect-syslog.md) |
| **Log Analytics-Tabellen** | syslog |
| **Kusto-Funktionsalias:** | JuniperSRX |
| **Kusto-Funktions-URL:** | https://aka.ms/Sentinel-junipersrx-parser |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Konfigurieren der Datenverkehrsprotokollierung (Sicherheitsrichtlinienprotokolle) für SRX Branch-Geräte](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)<br>[Konfigurieren der Systemprotokollierung](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502) |
| **Unterstützt von** | [Juniper Networks](https://support.juniper.net/support/) |
| | |

## <a name="lookout-mobile-threat-defense-preview"></a>Lookout Mobile Bedrohungsabwehr (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md) <br><br>Nur verfügbar nach Installation der [Lookout Mobile Threat Defense für Microsoft Sentinel](sentinel-solutions-catalog.md#lookout) Lösung |
| **Log Analytics-Tabellen** | Lookout_CL |
| **API-Anmeldeinformationen** | <li>Lookout-Anwendungsschlüssel |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>[Installationshandbuch](https://esupport.lookout.com/s/article/Lookout-with-Azure-Sentinel) (Anmeldung erforderlich)<li>[API-Dokumentation](https://esupport.lookout.com/s/article/Mobile-Risk-API-Guide) (Anmeldung erforderlich)<li>[Lookout Mobile Endpoint-Sicherheit](https://www.lookout.com/products/mobile-endpoint-security) |
| **Unterstützt von** | [Lookout](https://www.lookout.com/support) |
| | |



## <a name="microsoft-365-defender"></a>Microsoft 365 Defender

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration: <br>[Verbinden der Daten von Microsoft 365 Defender zu Microsoft Sentinel](connect-microsoft-365-defender.md)** (Artikel zum Top-Konnektor) |
| **Lizenzvoraussetzungen/<br>Preisinformationen** | [Gültige Lizenz für Microsoft 365 Defender](/microsoft-365/security/mtp/prerequisites)
| **Log Analytics-Tabellen** | **Alerts:**<br>SecurityAlert<br>SecurityIncident<br>**Defender für Endpunktereignisse:**<br>DeviceEvents<br>DeviceFileEvents<br>DeviceImageLoadEvents<br>DeviceInfo<br>DeviceLogonEvents<br>DeviceNetworkEvents<br>DeviceNetworkInfo<br>DeviceProcessEvents<br>DeviceRegistryEvents<br>DeviceFileCertificateInfo<br>**Defender für Office 365-Ereignisse:**<br>EmailAttachmentInfo<br>EmailUrlInfo<br>EmailEvents<br>EmailPostDeliveryEvents |
| **Unterstützt von** | Microsoft |
| | |

## <a name="microsoft-365-insider-risk-management-irm-preview"></a>Microsoft 365-Insider-Risikomanagement (IRM) (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[API-basierte Verbindungen](connect-azure-windows-microsoft-services.md#api-based-connections)**<br><br>Auch verfügbar in der [Microsoft 365-Insider Risiko-Management-Lösung](sentinel-solutions-catalog.md#domain-solutions) |
| **Lizenz und weitere Voraussetzungen** | <ul><li>Gültiges Abonnement für Microsoft 365 E5/A5/G5 oder die zugehörigen Compliance- oder IRM-Add-Ons.<li>[Microsoft 365-Insider Risikomanagement](/microsoft-365/compliance/insider-risk-management) vollständig eingerichtet und [IRM-Policies](/microsoft-365/compliance/insider-risk-management-policies) festgelegt und Warnungen ausgebend.<li>[Microsoft 365 IRM konfiguriert](/microsoft-365/compliance/insider-risk-management-settings#export-alerts-preview), um den Export von IRM-Warnungen an die Office 365 Management Activity API zu aktivieren, um die Warnmeldungen über den Microsoft Sentinel Konnektor zu empfangen).
| **Log Analytics-Tabellen** | SecurityAlert |
| **Datenabfragefilter** | `SecurityAlert`<br>`\| where ProductName == "Microsoft 365 Insider Risk Management"` |
| **Unterstützt von** | Microsoft |
| | |

<a name="microsoft-cloud-app-security-mcas"></a>

## <a name="microsoft-defender-for-cloud-apps"></a>Microsoft Defender für Cloud-Apps

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[API-basierte Verbindungen](connect-azure-windows-microsoft-services.md#api-based-connections)**<br><br>Aktivieren Cloud Discovery Microsoft [Sentinel als SIEM in Microsoft Defender für Cloud-Apps.](/cloud-app-security/siem-sentinel) |
| **Log Analytics-Tabellen** | SecurityAlert: für Warnungen<br>McasShadowItReporting: für Cloud Discovery-Protokolle |
| **Unterstützt von** | Microsoft |
| | |

## <a name="microsoft-defender-for-endpoint"></a>Microsoft Defender für den Endpunkt

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[API-basierte Verbindungen](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Lizenzvoraussetzungen/<br>Preisinformationen** | [Gültige Lizenz für die Bereitstellung von Microsoft Defender für Endpunkt](/microsoft-365/security/defender-endpoint/production-deployment)
| **Log Analytics-Tabellen** | SecurityAlert |
| **Unterstützt von** | Microsoft |
| | |

## <a name="microsoft-defender-for-identity"></a>Microsoft Defender for Identity

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[API-basierte Verbindungen](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Log Analytics-Tabellen** | SecurityAlert |
| **Unterstützt von** | Microsoft |
| | |

## <a name="microsoft-defender-for-office-365"></a>Microsoft Defender für Office 365

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[API-basierte Verbindungen](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Lizenzvoraussetzungen/<br>Preisinformationen** | Sie benötigen eine gültige Lizenz für [Office 365 ATP-Plan 2](/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2).
| **Log Analytics-Tabellen** | SecurityAlert |
| **Unterstützt von** | Microsoft |
| | |

## <a name="microsoft-office-365"></a>Microsoft Office 365

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[API-basierte Verbindungen](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Lizenzvoraussetzungen/<br>Preisinformationen** | Ihre Office-365-Bereitstellung muss sich auf demselben Mandanten befinden wie Ihr Microsoft Sentinel-Arbeitsbereich.<br>Möglicherweise fallen weitere Gebühren an. |
| **Log Analytics-Tabellen** | OfficeActivity |
| **Unterstützt von** | Microsoft |
| | |



## <a name="morphisec-utpp-preview"></a>Morphisec UTPP (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** mit einem Kusto-Funktionsparser |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Kusto-Funktionsalias:** | Morphisec |
| **Kusto-Funktions-URL** | https://aka.ms/Sentinel-Morphiescutpp-parser |
| **Unterstützt von** | [Morphisec](https://support.morphisec.com/support/home) |
| | |


## <a name="netskope-preview"></a>Netskope (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md) |
| **Log Analytics-Tabellen** | Netskope_CL |
| **Azure Functions-App-Code** | https://aka.ms/Sentinel-netskope-functioncode |
| **API-Anmeldeinformationen** | <li>Netskope-API-Token |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>[Netskope Cloud Security Platform](https://www.netskope.com/platform)<li>[Dokumentation zur Netskope-API](https://innovatechcloud.goskope.com/docs/Netskope_Help/en/rest-api-v1-overview.html)<li>[Abrufen eines API-Tokens](https://innovatechcloud.goskope.com/docs/Netskope_Help/en/rest-api-v2-overview.html) |
| **Anweisungen zur Connectorbereitstellung** | <li>[Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager)<li>[Manuelle Bereitstellung](connect-azure-functions-template.md?tabs=MPS) |
| **Kusto-Funktionsalias** | Netskope |
| **Kusto-Funktions-URL/<br>Anweisungen zur Parserkonfiguration** | https://aka.ms/Sentinel-netskope-parser |
| **Anwendungseinstellungen** | <li>apikey<li>workspaceID<li>workspaceKey<li>URI (hängt von der Region ab, hat das Schema: `https://<Tenant Name>.goskope.com`) <li>timeInterval (auf 5 festgelegt)<li>logTypes<li>logAnalyticsUri (optional) |
| **Unterstützt von** | Microsoft |
| | |

## <a name="nginx-http-server-preview"></a>NGINX HTTP Server (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Log Analytics-Agent: benutzerdefinierte Protokolle**](connect-custom-logs.md) |
| **Log Analytics-Tabellen** | NGINX_CL |
| **Kusto-Funktionsalias:** | NGINXHTTPServer |
| **Kusto-Funktions-URL** | https://aka.ms/Sentinel-NGINXHTTP-parser |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Module ngx_http_log_module](https://nginx.org/en/docs/http/ngx_http_log_module.html) |
| **Beispieldatei für benutzerdefinierte Protokolle:** | „access.log“ oder „error.log“ |
| **Unterstützt von** | Microsoft |
| | |

## <a name="nxlog-basic-security-module-bsm-macos-preview"></a>NXLog Basic Security Module (BSM) macOS (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Microsoft Sentinel-Datensammler-API**](connect-rest-api-template.md) |
| **Log Analytics-Tabellen** | BSMmacOS_CL |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [NXLog Microsoft Sentinel-Benutzerhandbuch](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **Unterstützt von** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="nxlog-dns-logs-preview"></a>NXLog DNS Logs (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Microsoft Sentinel-Datensammler-API**](connect-rest-api-template.md) |
| **Log Analytics-Tabellen** | DNS_Logs_CL |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [NXLog Microsoft Sentinel-Benutzerhandbuch](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **Unterstützt von** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="nxlog-linuxaudit-preview"></a>NXLog LinuxAudit (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Microsoft Sentinel-Datensammler-API**](connect-rest-api-template.md) |
| **Log Analytics-Tabellen** | LinuxAudit_CL |
| **Herstellerdokumentation/<br>Installationsanweisungen** |  [NXLog Microsoft Sentinel-Benutzerhandbuch](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **Unterstützt von** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="okta-single-sign-on-preview"></a>Okta Single Sign-On (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md) |
| **Log Analytics-Tabellen** | Okta_CL |
| **Azure Functions-App-Code** | https://aka.ms/sentineloktaazurefunctioncodev2 |
| **API-Anmeldeinformationen** | <li>API-Token |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>[Dokumentation zur Okta-Systemprotokoll-API](https://developer.okta.com/docs/reference/api/system-log/)<li>[Erstellen eines API-Tokens](https://developer.okta.com/docs/guides/create-an-api-token/create-the-token/)<li>[Verbinden des einmaligen Anmeldens von Okta bei Microsoft Sentinel](#okta-single-sign-on-preview) |
| **Anweisungen zur Connectorbereitstellung** | <li>[Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager)<li>[Manuelle Bereitstellung](connect-azure-functions-template.md?tabs=MPS) |
| **Anwendungseinstellungen** | <li>apiToken<li>workspaceID<li>workspaceKey<li>uri (hat das Schema: `https://<OktaDomain>/api/v1/logs?since=`. [Ermitteln Sie Ihren Domänennamespace](https://developer.okta.com/docs/reference/api-overview/#url-namespace).) <li>logAnalyticsUri (optional) |
| **Unterstützt von** | Microsoft |
| | |


## <a name="onapsis-platform-preview"></a>Onapsis Platform (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** mit Kusto-Funktionen für Lookup und Anreicherung<br><br>[Konfigurieren von Onapsis zum Senden von CEF-Protokollen an die Protokollweiterleitung](#configure-onapsis-to-send-cef-logs-to-the-log-forwarder) |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Kusto-Funktionsalias:** | incident_lookup |
| **Kusto-Funktions-URL** | https://aka.ms/Sentinel-Onapsis-parser |
| **Unterstützt von** | [Onapsis](https://onapsis.force.com/s/login/) |
| | |

### <a name="configure-onapsis-to-send-cef-logs-to-the-log-forwarder"></a>Konfigurieren von Onapsis zum Senden von CEF-Protokollen an die Protokollweiterleitung

Informationen zum Einrichten der Protokollweiterleitung an den Log Analytics-Agent finden Sie in der internen Onapsis-Produkthilfe.

1. Wechseln Sie zu **Setup > Drittanbieterintegrationen > Schützen von Alarmen** und befolgen Sie die Anweisungen für Microsoft Sentinel.
1. Stellen Sie sicher, dass die Onapsis-Konsole den Protokollweiterleitungscomputer erreichen kann, auf dem der Agent installiert ist. Protokolle sollten per TCP über Port 514 gesendet werden.

## <a name="one-identity-safeguard-preview"></a>One Identity Safeguard (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Administratorleitfaden für One Identity Safeguard for Privileged Sessions](https://aka.ms/sentinel-cef-oneidentity-forwarding) |
| **Unterstützt von** | [One Identity](https://support.oneidentity.com/) |
| | |



## <a name="oracle-weblogic-server-preview"></a>Oracle WebLogic Server (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Log Analytics-Agent: benutzerdefinierte Protokolle**](connect-custom-logs.md) |
| **Log Analytics-Tabellen** | OracleWebLogicServer_CL |
| **Kusto-Funktionsalias:** | OracleWebLogicServerEvent |
| **Kusto-Funktions-URL:** | https://aka.ms/Sentinel-OracleWebLogicServer-parser |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Dokumentation zu Oracle WebLogic Server](https://docs.oracle.com/en/middleware/standalone/weblogic-server/14.1.1.0/index.html) |
| **Beispieldatei für benutzerdefinierte Protokolle:** | server.log |
| **Unterstützt von** | Microsoft |
| | |

## <a name="orca-security-preview"></a>Orca Security (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Microsoft Sentinel-Datensammler-API**](connect-rest-api-template.md) |
| **Log Analytics-Tabellen** | OrcaAlerts_CL |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Microsoft Sentinel-Integration](https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-configuration) |
| **Unterstützt von** | [Orca Security](http://support.orca.security/) |
| | |


## <a name="ossec-preview"></a>OSSEC (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** mit einem Kusto-Funktionsparser |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Kusto-Funktionsalias:** | OSSECEvent |
| **Kusto-Funktions-URL:** | https://aka.ms/Sentinel-OSSEC-parser |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [OSSEC-Dokumentation](https://www.ossec.net/docs/)<br>[Senden von Warnungen über Syslog](https://www.ossec.net/docs/docs/manual/output/syslog-output.html) |
| **Unterstützt von** | Microsoft |
| | |


## <a name="palo-alto-networks"></a>Palo Alto Networks

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** <br><br>Auch in den [Palo Alto PAN-OS- und Prisma-Lösungen](sentinel-solutions-catalog.md#palo-alto) verfügbar|
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Common Event Format (CEF): Konfigurationsleitfäden](https://aka.ms/asi-syslog-paloalto-forwarding)<br>[Konfigurieren der Syslog-Überwachung](https://aka.ms/asi-syslog-paloalto-configure) |
| **Unterstützt von** | [Palo Alto Networks](https://www.paloaltonetworks.com/company/contact-support) |
| | |


## <a name="perimeter-81-activity-logs-preview"></a>Perimeter 81 Activity Logs (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Microsoft Sentinel-Datensammler-API**](connect-rest-api-template.md) |
| **Log Analytics-Tabellen** | Perimeter81_CL |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Dokumentation zu Perimeter 81](https://support.perimeter81.com/docs/360012680780) |
| **Unterstützt von** | [Perimeter 81](https://support.perimeter81.com/) |
| | |



## <a name="proofpoint-on-demand-pod-email-security-preview"></a>Proofpoint On Demand (POD) Email Security (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md) <br><br>Auch in der [Proofpoint POD-Lösung](sentinel-solutions-catalog.md#proofpoint) verfügbar |
| **Log Analytics-Tabellen** | ProofpointPOD_message_CL<br>ProofpointPOD_maillog_CL |
| **Azure Functions-App-Code** | https://aka.ms/Sentinel-proofpointpod-functionapp |
| **API-Anmeldeinformationen** | <li>ProofpointClusterID<li>ProofpointToken |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>[Anmelden bei der Proofpoint-Community](https://proofpointcommunities.force.com/community/s/article/How-to-request-a-Community-account-and-gain-full-customer-access?utm_source=login&utm_medium=recommended&utm_campaign=public)<li>[Dokumentation und Anweisungen zur Proofpoint-API](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API) |
| **Anweisungen zur Connectorbereitstellung** | <li>[Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager)<li>[Manuelle Bereitstellung](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto-Funktionsalias** | ProofpointPOD |
| **Kusto-Funktions-URL/<br>Anweisungen zur Parserkonfiguration** | https://aka.ms/Sentinel-proofpointpod-parser |
| **Anwendungseinstellungen** | <li>ProofpointClusterID<li>ProofpointToken<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (optional) |
| **Unterstützt von** | Microsoft |
| | |

## <a name="proofpoint-targeted-attack-protection-tap-preview"></a>Proofpoint Targeted Attack Protection (TAP) (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md) <br><br>Auch in der [Proofpoint TAP-Lösung](sentinel-solutions-catalog.md#proofpoint) verfügbar |
| **Log Analytics-Tabellen** | ProofPointTAPClicksPermitted_CL<br>ProofPointTAPClicksBlocked_CL<br>ProofPointTAPMessagesDelivered_CL<br>ProofPointTAPMessagesBlocked_CL |
| **Azure Functions-App-Code** | https://aka.ms/sentinelproofpointtapazurefunctioncode |
| **API-Anmeldeinformationen** | <li>API-Benutzername<li>API-Kennwort |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>[Dokumentation zur Proofpoint-SIEM-API](https://help.proofpoint.com/Threat_Insight_Dashboard/API_Documentation/SIEM_API) |
| **Anweisungen zur Connectorbereitstellung** | <li>[Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager)<li>[Manuelle Bereitstellung](connect-azure-functions-template.md?tabs=MPS) |
| **Anwendungseinstellungen** | <li>apiUsername<li>apiUsername<li>uri (festgelegt auf `https://tap-api-v2.proofpoint.com/v2/siem/all?format=json&sinceSeconds=300`)<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (optional) |
| **Unterstützt von** | Microsoft |
| | |

## <a name="pulse-connect-secure-preview"></a>Pulse Connect Secure (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**syslog**](connect-syslog.md) |
| **Log Analytics-Tabellen** | syslog |
| **Kusto-Funktionsalias:** | PulseConnectSecure |
| **Kusto-Funktions-URL:** | https://aka.ms/sentinelgithubparserspulsesecurevpn |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Konfigurieren von Syslog](https://docs.pulsesecure.net/WebHelp/Content/PCS/PCS_AdminGuide_8.2/Configuring%20Syslog.htm) |
| **Unterstützt von** | Microsoft |
| | |


## <a name="qualys-vm-knowledgebase-kb-preview"></a>Qualys VM KnowledgeBase (KB) (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md)<br><br>[Zusätzliche Konfiguration für Qualys VM KB](#extra-configuration-for-the-qualys-vm-kb) <br><br>Auch in der [Qualys VM-Lösung](sentinel-solutions-catalog.md#qualys) verfügbar|
| **Log Analytics-Tabellen** | QualysKB_CL |
| **Azure Functions-App-Code** | https://aka.ms/Sentinel-qualyskb-functioncode |
| **API-Anmeldeinformationen** | <li>API-Benutzername<li>API-Kennwort |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>[Benutzerleitfaden für die QualysVM-API](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf) |
| **Anweisungen zur Connectorbereitstellung** | <li>[Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager)<li>[Manuelle Bereitstellung](connect-azure-functions-template.md?tabs=MPS) |
| **Kusto-Funktionsalias** | QualysKB |
| **Kusto-Funktions-URL/<br>Anweisungen zur Parserkonfiguration** | https://aka.ms/Sentinel-qualyskb-parser |
| **Anwendungseinstellungen** | <li>apiUsername<li>apiUsername<li>uri (nach Region; siehe [API-Serverliste](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf#G4.735348). Hat das Schema: `https://<API Server>/api/2.0`.<li>WorkspaceID<li>WorkspaceKey<li>filterParameters (am Ende des URI anfügen, getrennt durch `&`. Keine Leerzeichen)<li>logAnalyticsUri (optional) |
| **Unterstützt von** | Microsoft |
| | |

### <a name="extra-configuration-for-the-qualys-vm-kb"></a>Zusätzliche Konfiguration für Qualys VM KB

1. Melden Sie sich mit einem Administratorkonto an der Qualys Vulnerability Management-Konsole an, und wählen Sie die Registerkarte **Users** (Benutzer) und die Unterregisterkarte **Users** (Benutzer) aus.
1. Wählen Sie das Dropdownmenü **New** (Neu) und dann **Users** (Benutzer) aus.
1. Erstellen Sie einen Benutzernamen und ein Kennwort für das API-Konto.
1. Stellen Sie auf der Registerkarte **Benutzerrollen** sicher, dass die Kontorolle auf **Manager** festgelegt ist und dass der Zugriff auf **GUI** und **API** zulässig ist.
1. Melden Sie sich beim Administratorkonto ab, und melden Sie sich zur Überprüfung mit den neuen API-Anmeldeinformationen an der Konsole an. Melden Sie sich nach der Überprüfung beim API-Konto ab.
1. Melden Sie sich wieder mit einem Administratorkonto an der Konsole an, und ändern Sie die Benutzerrollen der API-Konten, indem Sie den Zugriff auf die **GUI** (grafische Benutzeroberfläche) entfernen.
1. Speichern Sie alle Änderungen.

## <a name="qualys-vulnerability-management-vm-preview"></a>Qualys Vulnerability Management (VM) (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md)<br><br>[Zusätzliche Konfiguration für Qualys VM](#extra-configuration-for-the-qualys-vm) <br>[Manuelle Bereitstellung – nach dem Konfigurieren der Funktions-App](#manual-deployment---after-configuring-the-function-app)|
| **Log Analytics-Tabellen** | QualysHostDetection_CL |
| **Azure Functions-App-Code** | https://aka.ms/sentinelqualysvmazurefunctioncode |
| **API-Anmeldeinformationen** | <li>API-Benutzername<li>API-Kennwort |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>[Benutzerleitfaden für die QualysVM-API](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf) |
| **Anweisungen zur Connectorbereitstellung** | <li>[Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager)<li>[Manuelle Bereitstellung](connect-azure-functions-template.md?tabs=MPS) |
| **Anwendungseinstellungen** | <li>apiUsername<li>apiUsername<li>uri (nach Region; siehe [API-Serverliste](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf#G4.735348). Hat das Schema: `https://<API Server>/api/2.0/fo/asset/host/vm/detection/?action=list&vm_processed_after=`.<li>WorkspaceID<li>WorkspaceKey<li>filterParameters (am Ende des URI anfügen, getrennt durch `&`. Keine Leerzeichen)<li>timeInterval (auf 5 festgelegt. Passen Sie bei einer Änderung auch den Timertrigger der Funktions-App entsprechend an.)<li>logAnalyticsUri (optional) |
| **Unterstützt von** | Microsoft |
| | |

### <a name="extra-configuration-for-the-qualys-vm"></a>Zusätzliche Konfiguration für Qualys VM

1. Melden Sie sich mit einem Administratorkonto an der Qualys Vulnerability Management-Konsole an, und wählen Sie die Registerkarte **Users** (Benutzer) und die Unterregisterkarte **Users** (Benutzer) aus.
1. Wählen Sie das Dropdownmenü **New** (Neu) und dann **Users** (Benutzer) aus.
1. Erstellen Sie einen Benutzernamen und ein Kennwort für das API-Konto.
1. Stellen Sie auf der Registerkarte **Benutzerrollen** sicher, dass die Kontorolle auf **Manager** festgelegt ist und dass der Zugriff auf **GUI** und **API** zulässig ist.
1. Melden Sie sich beim Administratorkonto ab, und melden Sie sich zur Überprüfung mit den neuen API-Anmeldeinformationen an der Konsole an. Melden Sie sich nach der Überprüfung beim API-Konto ab.
1. Melden Sie sich wieder mit einem Administratorkonto an der Konsole an, und ändern Sie die Benutzerrollen der API-Konten, indem Sie den Zugriff auf die **GUI** (grafische Benutzeroberfläche) entfernen.
1. Speichern Sie alle Änderungen.

### <a name="manual-deployment---after-configuring-the-function-app"></a>Manuelle Bereitstellung – nach dem Konfigurieren der Funktions-App

**Konfigurieren der Datei „host.json“**

Aufgrund der potenziell großen Menge an erfassten Qualys-Hosterkennungsdaten kann dies dazu führen, dass die Ausführungszeit das Standardtimeout für Funktions-Apps von fünf Minuten überschreitet. Erhöhen Sie das Standardtimeout unter dem Verbrauchstarif auf maximal 10 Minuten, um mehr Zeit für die Ausführung der Funktions-App zuzulassen.

1. Wählen Sie in der Funktions-App den Namen der Funktions-App und dann die Seite **App Service-Editor** aus.
1. Wählen Sie **Los** aus, um den Editor zu öffnen, und wählen Sie dann die Datei **host.json** unter dem Verzeichnis **wwwroot** aus.
1. Fügen Sie die Zeile `"functionTimeout": "00:10:00",` über der Zeile `managedDependancy` hinzu.
1. Stellen Sie sicher, dass in der rechten oberen Ecke des Editors **GESPEICHERT** angezeigt wird, und beenden Sie dann den Editor.

Wenn eine noch längere Timeoutdauer erforderlich ist, sollten Sie ein Upgrade auf einen [App Service-Plan](../azure-functions/functions-scale.md) in Betracht ziehen.



## <a name="salesforce-service-cloud-preview"></a>Salesforce Service Cloud (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md) |
| **Log Analytics-Tabellen** | SalesforceServiceCloud_CL |
| **Azure Functions-App-Code** | https://aka.ms/Sentinel-SalesforceServiceCloud-functionapp |
| **API-Anmeldeinformationen** | <li>Salesforce-API-Benutzername<li>Salesforce-API-Kennwort<li>Salesforce-Sicherheitstoken<li>Salesforce-Consumerschlüssel<li>Salesforce-Consumergeheimnis |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Entwicklerleitfaden zur Salesforce-REST-API](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm)<br>Verwenden Sie unter **Set up authorization** (Autorisierung einrichten) die Methode **Session ID** (Sitzungs-ID) anstelle von OAuth. |
| **Anweisungen zur Connectorbereitstellung** | <li>[Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager)<li>[Manuelle Bereitstellung](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto-Funktionsalias** | SalesforceServiceCloud |
| **Kusto-Funktions-URL/<br>Anweisungen zur Parserkonfiguration** | https://aka.ms/Sentinel-SalesforceServiceCloud-parser |
| **Anwendungseinstellungen** | <li>SalesforceUser<li>SalesforcePass<li>SalesforceSecurityToken<li>SalesforceConsumerKey<li>SalesforceConsumerSecret<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (optional) |
| **Unterstützt von** | Microsoft |
| | |


## <a name="security-events-via-legacy-agent-windows"></a>Sicherheitsereignisse über Legacy-Agent (Windows)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[Auf dem Log Analytics-Agent basierende Verbindungen](connect-azure-windows-microsoft-services.md?tabs=LAA#windows-agent-based-connections)** |
| **Log Analytics-Tabellen** | SecurityEvents |
| **Unterstützt von** | Microsoft |
| | |


Weitere Informationen finden Sie unter [Einrichtung der Arbeitsmappe für unsichere Protokolle](./get-visibility.md#use-built-in-workbooks).

Siehe auch: [**Windows-Sicherheitsereignisse über AMA**](#windows-security-events-via-ama)-Connector basierend auf Azure Monitor-Agent (AMA)

[Konfigurieren des **Connectors für Sicherheitsereignisse/Windows-Sicherheitsereignisse** zur **Erkennung ungewöhnlicher RDP-Anmeldungen**](#configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection)

## <a name="sentinelone-preview"></a>SentinelOne (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md) <br><br>[Zusätzliche Konfiguration für SentinelOne](#extra-configuration-for-sentinelone)|
| **Log Analytics-Tabellen** | SentinelOne_CL |
| **Azure Functions-App-Code** | https://aka.ms/Sentinel-SentinelOneAPI-functionapp |
| **API-Anmeldeinformationen** | <li>SentinelOneAPIToken<li>SentinelOneUrl (`https://<SOneInstanceDomain>.sentinelone.net`) |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>https://`<SOneInstanceDomain>`.sentinelone.net/api-doc/overview<li>Weitere Informationen finden Sie in den Anweisungen unten. |
| **Anweisungen zur Connectorbereitstellung** | <li>[Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager)<li>[Manuelle Bereitstellung](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto-Funktionsalias** | SentinelOne |
| **Kusto-Funktions-URL/<br>Anweisungen zur Parserkonfiguration** | https://aka.ms/Sentinel-SentinelOneAPI-parser |
| **Anwendungseinstellungen** | <li>SentinelOneAPIToken<li>SentinelOneUrl<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (optional) |
| **Unterstützt von** | Microsoft |
| | |

### <a name="extra-configuration-for-sentinelone"></a>Zusätzliche Konfiguration für SentinelOne

Befolgen Sie die Anweisungen zum Abrufen der Anmeldeinformationen.

1. Melden Sie sich mit Anmeldeinformationen eines Administratorbenutzers an der SentinelOne-Verwaltungskonsole an.
1. Wählen Sie an der Verwaltungskonsole **Settings** (Einstellungen) aus.
1. Wählen Sie in der Ansicht **SETTINGS** (EINSTELLUNGEN) die Option **USERS** (BENUTZER) aus.
1. Wählen Sie **Neuer Benutzer** aus.
1. Geben Sie die Informationen zum neuen Konsolenbenutzer ein.
1. Wählen Sie als Rolle **Admin** aus.
1. Wählen Sie **SAVE** (SPEICHERN) aus.
1. Speichern Sie die Anmeldeinformationen des neuen Benutzers für die Verwendung im Datenconnector.



## <a name="sonicwall-firewall-preview"></a>SonicWall Firewall (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Protokoll > Syslog](http://help.sonicwall.com/help/sw/eng/7020/26/2/3/content/Log_Syslog.120.2.htm)<br>Wählen Sie die Einrichtung „local4“ und als Syslog-Format „ArcSight“ aus.  |
| **Unterstützt von** | [SonicWall](https://www.sonicwall.com/support/) |
| | | 


## <a name="sophos-cloud-optix-preview"></a>Sophos Cloud Optix (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Microsoft Sentinel-Datensammler-API**](connect-rest-api-template.md) |
| **Log Analytics-Tabellen** | SophosCloudOptix_CL |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Integrieren Sie in Microsoft Sentinel,](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html)und überspringen Sie den ersten Schritt.<br>[Sophos-Abfragebeispiele](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html) |
| **Unterstützt von** | [Sophos](https://secure2.sophos.com/en-us/support.aspx) |
| | |




## <a name="sophos-xg-firewall-preview"></a>Sophos XG Firewall (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**syslog**](connect-syslog.md) |
| **Log Analytics-Tabellen** | syslog |
| **Kusto-Funktionsalias:** | SophosXGFirewall |
| **Kusto-Funktions-URL:** | https://aka.ms/sentinelgithubparserssophosfirewallxg |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Hinzufügen eines Syslog-Servers](https://docs.sophos.com/nsg/sophos-firewall/18.5/Help/en-us/webhelp/onlinehelp/nsg/tasks/SyslogServerAdd.html) |
| **Unterstützt von** | Microsoft |
| | |

## <a name="squadra-technologies-secrmm"></a>Squadra Technologies secRMM

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Microsoft Sentinel-Datensammler-API**](connect-rest-api-template.md) |
| **Log Analytics-Tabellen** | secRMM_CL |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [secRMM Microsoft Sentinel Administrator Guide (SecRMM Microsoft Sentinel-Administratorhandbuch)](https://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) |
| **Unterstützt von** | [Squadra Technologies](https://www.squadratechnologies.com/Contact.aspx) |
| | |


## <a name="squid-proxy-preview"></a>Squid Proxy (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Log Analytics-Agent: benutzerdefinierte Protokolle**](connect-custom-logs.md) |
| **Log Analytics-Tabellen** | SquidProxy_CL |
| **Kusto-Funktionsalias:** | SquidProxy |
| **Kusto-Funktions-URL** | https://aka.ms/Sentinel-squidproxy-parser |
| **Beispieldatei für benutzerdefinierte Protokolle:** | „access.log“ oder „cache.log“ |
| **Unterstützt von** | Microsoft |
| | |

## <a name="symantec-integrated-cyber-defense-exchange-icdx"></a>Symantec Integrated Cyber Defense Exchange (ICDx)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Microsoft Sentinel-Datensammler-API**](connect-rest-api-template.md) |
| **Log Analytics-Tabellen** | SymantecICDx_CL |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [secRMM Microsoft Sentinel Administrator Guide (SecRMM Microsoft Sentinel-Administratorhandbuch)](https://techdocs.broadcom.com/us/en/symantec-security-software/integrated-cyber-defense/integrated-cyber-defense-exchange/1-4-3/Forwarders/configuring-forwarders-v131944722-d2707e17438.html) |
| **Unterstützt von** | [Broadcom Symantec](https://support.broadcom.com/security) |
| | |


## <a name="symantec-proxysg-preview"></a>Symantec ProxySG (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**syslog**](connect-syslog.md) |
| **Log Analytics-Tabellen** | syslog |
| **Kusto-Funktionsalias:** | SymantecProxySG |
| **Kusto-Funktions-URL:** | https://aka.ms/sentinelgithubparserssymantecproxysg |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Senden von Zugriffsprotokollen an einen Syslog-Server](https://knowledge.broadcom.com/external/article/166529/sending-access-logs-to-a-syslog-server.html) |
| **Unterstützt von** | Microsoft |
| | |


## <a name="symantec-vip-preview"></a>Symantec VIP (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**syslog**](connect-syslog.md) |
| **Log Analytics-Tabellen** | syslog |
| **Kusto-Funktionsalias:** | SymantecVIP |
| **Kusto-Funktions-URL:** | https://aka.ms/sentinelgithubparserssymantecvip |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Konfigurieren von Syslog](https://help.symantec.com/cs/VIP_EG_INSTALL_CONFIG/VIP/v134652108_v128483142/Configuring-syslog?locale=EN_US) |
| **Unterstützt von** | Microsoft |
| | |



## <a name="thycotic-secret-server-preview"></a>Thycotic Secret Server (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Sichere Syslog-/CEF-Protokollierung](https://thy.center/ss/link/syslog) |
| **Unterstützt von** | [Thycotic](https://thycotic.force.com/support/s/) |
| | |


## <a name="trend-micro-deep-security"></a>Trend Micro Deep Security

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** mit einem Kusto-Funktionsparser |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Kusto-Funktionsalias:** | TrendMicroDeepSecurity |
| **Kusto-Funktions-URL** | https://aka.ms/TrendMicroDeepSecurityFunction |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Weiterleiten von Deep Security-Ereignissen an einen Syslog- oder SIEM-Server](https://aka.ms/Sentinel-trendMicro-connectorInstructions) |
| **Unterstützt von** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |

## <a name="trend-micro-tippingpoint-preview"></a>Trend Micro TippingPoint (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** mit einem Kusto-Funktionsparser |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Kusto-Funktionsalias:** | TrendMicroTippingPoint |
| **Kusto-Funktions-URL** | https://aka.ms/Sentinel-trendmicrotippingpoint-function |
| **Herstellerdokumentation/<br>Installationsanweisungen** | Senden von Syslog-Nachrichten im ArcSight-CEF-Format v4.2 |
| **Unterstützt von** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |

## <a name="trend-micro-vision-one-xdr-preview"></a>Trend Micro Vision One (XDR) (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md) |
| **Log Analytics-Tabellen** | TrendMicro_XDR_CL |
| **API-Anmeldeinformationen** | <li>API-Token |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>[Trend Micro Vision One-API](https://automation.trendmicro.com/xdr/home)<li>[Abrufen von API-Schlüsseln für den Zugriff durch Drittanbieter](https://docs.trendmicro.com/en-us/enterprise/trend-micro-xdr-help/ObtainingAPIKeys) |
| **Anweisungen zur Connectorbereitstellung** | [Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager) |
| **Unterstützt von** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |



## <a name="vmware-carbon-black-endpoint-standard-preview"></a>VMware Carbon Black Endpoint Standard (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md) |
| **Log Analytics-Tabellen** | CarbonBlackEvents_CL<br>CarbonBlackAuditLogs_CL<br>CarbonBlackNotifications_CL |
| **Azure Functions-App-Code** | https://aka.ms/sentinelcarbonblackazurefunctioncode |
| **API-Anmeldeinformationen** | **API-Zugriffsebene** (für *Überwachungs-* und *Ereignisprotokolle*):<li>API-ID<li>API-Schlüssel<br><br>**SIEM-Zugriffsebene** (für *Benachrichtigungsereignisse*):<li>SIEM-API-ID<li>SIEM-API-Schlüssel |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>[Dokumentation zur Carbon Black-API](https://developer.carbonblack.com/reference/carbon-black-cloud/cb-defense/latest/rest-api/)<li>[Erstellen eines API-Schlüssels](https://developer.carbonblack.com/reference/carbon-black-cloud/authentication/#creating-an-api-key) |
| **Anweisungen zur Connectorbereitstellung** | <li>[Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager)<li>[Manuelle Bereitstellung](connect-azure-functions-template.md?tabs=MPS) |
| **Anwendungseinstellungen** | <li>apiId<li>apiKey<li>WorkspaceID<li>WorkspaceKey<li>uri (nach Region; siehe [Liste der Optionen](https://community.carbonblack.com/t5/Knowledge-Base/PSC-What-URLs-are-used-to-access-the-APIs/ta-p/67346). Hat das Schema: `https://<API URL>.conferdeploy.net`.)<li>timeInterval (auf 5 festgelegt)<li>SIEMapiId (für das Erfassen von *Benachrichtigungsereignissen*)<li>SIEMapiKey (für das Erfassen von *Benachrichtigungsereignissen*)<li>logAnalyticsUri (optional) |
| **Unterstützt von** | Microsoft |
| | |

## <a name="vmware-esxi-preview"></a>VMware ESXi (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**syslog**](connect-syslog.md) |
| **Log Analytics-Tabellen** | syslog |
| **Kusto-Funktionsalias:** | VMwareESXi |
| **Kusto-Funktions-URL:** | https://aka.ms/Sentinel-vmwareesxi-parser |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Aktivieren von Syslog unter ESXi 3.5 und 4.x](https://kb.vmware.com/s/article/1016621)<br>[Konfigurieren von Syslog auf ESXi-Hosts](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html) |
| **Unterstützt von** | Microsoft |
| | |

## <a name="watchguard-firebox-preview"></a>WatchGuard Firebox (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**syslog**](connect-syslog.md) |
| **Log Analytics-Tabellen** | syslog |
| **Kusto-Funktionsalias:** | WatchGuardFirebox |
| **Kusto-Funktions-URL:** | https://aka.ms/Sentinel-watchguardfirebox-parser |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Microsoft Sentinel-Integrationshandbuch](https://www.watchguard.com/help/docs/help-center/en-US/Content/Integration-Guides/General/Microsoft%20Azure%20Sentinel.html) |
| **Unterstützt von** | [WatchGuard Technologies](https://www.watchguard.com/wgrd-support/overview) |
| | |

## <a name="wirex-network-forensics-platform-preview"></a>WireX Network Forensics Platform (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | Wenden Sie sich an den [WireX-Support](https://wirexsystems.com/contact-us/), um Ihre NFP-Lösung für das Senden von Syslog-Nachrichten im CEF-Format zu konfigurieren. |
| **Unterstützt von** | [WireX Systems](mailto:support@wirexsystems.com) |
| | |


## <a name="windows-dns-server-preview"></a>Windows DNS Server (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[Auf dem Log Analytics-Agent basierende Verbindungen](connect-azure-windows-microsoft-services.md?tabs=LAA#windows-agent-based-connections)** |
| **Log Analytics-Tabellen** | DnsEvents<br>DnsInventory |
| **Unterstützt von** | Microsoft |
| | |

## <a name="windows-forwarded-events-preview"></a>Weitergeleitete Windows-Ereignisse (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[Auf dem Azure Monitor Agent basierende Verbindungen](connect-azure-windows-microsoft-services.md?tabs=AMA#windows-agent-based-connections)**<br><br>[Zusätzliche Anweisungen zum Bereitstellen des Connectors für Windows Weitergeleitete Ereignisse](#additional-instructions-for-deploying-the-windows-forwarded-events-connector) |
| **Voraussetzungen** | Sie müssen Windows Event Collection (WEC) aktiviert und ausgeführt haben.<br>Installieren Sie Azure Monitor-Agent auf dem WEC-Computer. |
| **Präfix für xPath-Abfragen** | "ForwardedEvents!*" |
| **Log Analytics-Tabellen** | WindowsEvents |
| **Unterstützt von** | Microsoft |
| | |

### <a name="additional-instructions-for-deploying-the-windows-forwarded-events-connector"></a>Zusätzliche Anweisungen zum Bereitstellen des Connectors für Windows Weitergeleitete Ereignisse

Es wird empfohlen, die [ASIM-Parser (Advanced SIEM Information Model)](normalization.md) zu installieren, um die vollständige Unterstützung für die Datennormalisierung sicherzustellen. Sie können diese Parser aus dem [`Azure-Sentinel` GitHub-Repository](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASim%20WindowsEvent) über die dortige Schaltfläche **In Azure** bereitstellen.

## <a name="windows-firewall"></a>Windows-Firewall

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[Auf dem Log Analytics-Agent basierende Verbindungen](connect-azure-windows-microsoft-services.md?tabs=LAA#windows-agent-based-connections)** |
| **Log Analytics-Tabellen** | WindowsFirewall |
| **Unterstützt von** | Microsoft |
| | |

## <a name="windows-security-events-via-ama"></a>Windows-Sicherheitsereignisse über AMA

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **Azure-Dienst-zu-Dienst-Integration:<br>[Auf dem Azure Monitor Agent basierende Verbindungen](connect-azure-windows-microsoft-services.md?tabs=AMA#windows-agent-based-connections)** |
| **Präfix für xPath-Abfragen** | "Security!*" |
| **Log Analytics-Tabellen** | SecurityEvents |
| **Unterstützt von** | Microsoft |
| | |

Siehe auch: [**Sicherheitsereignisse über Agenten**](#security-events-via-legacy-agent-windows)-Connector einer Vorgängerversion.

### <a name="configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection"></a>Konfigurieren des Connectors für Sicherheitsereignisse/Windows-Sicherheitsereignisse zur Erkennung ungewöhnlicher RDP-Anmeldungen

> [!IMPORTANT]
> Die Erkennung ungewöhnlicher RDP-Anmeldungen befindet sich derzeit in der öffentlichen Vorschauphase.
> Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Microsoft Sentinel kann maschinelles Lernen (ML) auf Sicherheitsereignisdaten anwenden, um anomale RDP-Anmeldeaktivitäten (Remote Desktop Protocol) zu identifizieren. Mögliche Szenarien:

- **Ungewöhnliche IP-Adresse**: Die IP-Adresse ist in den letzten 30 Tagen nur selten oder gar nicht aufgetreten.

- **Ungewöhnliche Geolocation**: IP-Adresse, Ort, Land und ASN sind in den letzten 30 Tagen nur selten oder gar nicht aufgetreten.

- **Neuer Benutzer**: Ein neuer Benutzer meldet sich über eine IP-Adresse oder Geolocation an, mit deren Auftreten (einzeln oder gemeinsam) anhand der Daten der letzten 30 Tage nicht gerechnet wurde.

**Konfigurationsanweisungen**

1. Sie müssen RDP-Anmeldedaten (Ereignis-ID 4624) über den Datenconnector für **Sicherheitsereignisse** oder für **Windows-Sicherheitsereignisse** erfassen. Stellen Sie sicher, dass Sie neben "Keiner" einen [Ereignissatz](windows-security-event-id-reference.md) ausgewählt oder eine Datensammlungsregel erstellt haben, die diese Ereignis-ID enthält, um sie an Microsoft Sentinel zu streamen.

1. Wählen Sie im Microsoft Sentinel-Portal **Analytics** und dann die Registerkarte **Regelvorlagen** aus. Wählen Sie die Regel **(Vorschau) Anomale RDP-Anmeldeerkennung** aus, und verschieben Sie den **Schieberegler Status** auf **Aktiviert.**

    > [!NOTE]
    > Da für den Machine Learning-Algorithmus zum Erstellen eines Basisprofils für das Benutzerverhalten die Daten von 30 Tagen benötigt werden, müssen Sie die Erfassung von Windows-Sicherheitsereignissen für 30 Tage zulassen, bevor Incidents erkannt werden können.

## <a name="workplace-from-facebook-preview"></a>Workplace from Facebook (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md)<br><br>[Konfigurieren von Webhooks](#configure-webhooks) <br>[Hinzufügen einer Rückruf-URL zur Webhookkonfiguration](#add-callback-url-to-webhook-configuration)|
| **Log Analytics-Tabellen** | Workplace_Facebook_CL |
| **Azure Functions-App-Code** | https://aka.ms/Sentinel-WorkplaceFacebook-functionapp |
| **API-Anmeldeinformationen** | <li>WorkplaceAppSecret<li>WorkplaceVerifyToken |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>[Konfigurieren von Webhooks](https://developers.facebook.com/docs/workplace/reference/webhooks)<li>[Konfigurieren von Berechtigungen](https://developers.facebook.com/docs/workplace/reference/permissions) |
| **Anweisungen zur Connectorbereitstellung** | <li>[Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager)<li>[Manuelle Bereitstellung](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto-Funktionsalias** | Workplace_Facebook |
| **Kusto-Funktions-URL/<br>Anweisungen zur Parserkonfiguration** | https://aka.ms/Sentinel-WorkplaceFacebook-parser |
| **Anwendungseinstellungen** | <li>WorkplaceAppSecret<li>WorkplaceVerifyToken<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (optional) |
| **Unterstützt von** | Microsoft |
| | |

### <a name="configure-webhooks"></a>Konfigurieren von Webhooks

1. Melden Sie sich bei Workplace mit den Anmeldeinformationen eines Administratorbenutzers an.
1. Wählen Sie im Administratorbereich **Integrations** aus.
1. Wählen Sie in der Ansicht **All Integrations** (Alle Integrationen) die Option **Create custom integration** (Benutzerdefinierte Integration erstellen) aus.
1. Geben Sie den Namen und eine Beschreibung ein, und wählen Sie **Create** (Erstellen) aus.
1. Zeigen Sie im Bereich **Integration details** (Integrationsdetails) das **App secret** (App-Geheimnis) an, und kopieren Sie es.
1. Legen Sie im Bereich **Integration permission** (Integrationsberechtigungen) alle Leseberechtigungen fest. Details dazu finden Sie auf der [Berechtigungsseite](https://developers.facebook.com/docs/workplace/reference/permissions).

### <a name="add-callback-url-to-webhook-configuration"></a>Hinzufügen einer Rückruf-URL zur Webhookkonfiguration

1. Öffnen Sie die Seite Ihrer Funktions-App, wechseln Sie zur Liste **Funktionen**, wählen Sie **Funktions-URL abrufen** aus, und kopieren Sie diese.
1. Wechseln Sie zurück zu **Workplace from Facebook**. Legen Sie im Bereich **Configure webhooks** (Webhooks konfigurieren) auf jeder Registerkarte die **Rückruf-URL**, die Sie im letzten Schritt kopiert haben, als Funktions-URL fest. Legen Sie außerdem das **Verify token** (Überprüfungstoken) auf den gleichen Wert fest, den Sie bei der automatischen Bereitstellung erhalten oder während der manuellen Bereitstellung eingegeben haben.
1. Wählen Sie **Speichern** aus.



## <a name="zimperium-mobile-thread-defense-preview"></a>Zimperium Mobile Thread Defense (Vorschau)

Der Zimperium Mobile Threat Defense-Datenkonnektor verbindet das Zimperium-Bedrohungsprotokoll mit Microsoft Sentinel, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und die Untersuchung zu verbessern. Durch diesen Connector erhalten Sie einen besseren Einblick in die Bedrohungslage mobiler Geräte in Ihrer Organisation und bessere Möglichkeiten für Sicherheitsvorgänge.

Weitere Informationen finden Sie unter [Verbinden Sie Zimperium mit Microsoft Sentinel.](#zimperium-mobile-thread-defense-preview)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Microsoft Sentinel-Datensammler-API**](connect-rest-api-template.md)<br><br>[Konfigurieren und Verbinden von Zimperium MTD](#configure-and-connect-zimperium-mtd) |
| **Log Analytics-Tabellen** | ZimperiumThreatLog_CL<br>ZimperiumMitigationLog_CL |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Zimperium-Portal für den Kundensupport](https://support.zimperium.com/) (Anmeldung erforderlich) |
| **Unterstützt von** | [Zimperium](https://www.zimperium.com/support) |
| | |

### <a name="configure-and-connect-zimperium-mtd"></a>Konfigurieren und Verbinden von Zimperium MTD

1. Wählen Sie in zConsole auf der Navigationsleiste **Manage** (Verwalten) aus.
1. Wählen Sie die Registerkarte **Integrations** aus.
1. Wählen Sie die Schaltfläche **Threat Reporting** (Bedrohungsberichte) und dann die Schaltfläche **Add Integrations** (Integrationen hinzufügen) aus.
1. Erstellen Sie die Integration:
    1. Wählen Sie unter den verfügbaren Integrationen **Microsoft Sentinel**.
    1. Geben Sie als Nächstes Ihre *Arbeitsbereichs-ID* und den *Primärschlüssel* ein, und wählen Sie dann **Weiter** aus.
    1. Geben Sie einen Namen für Ihre Microsoft Sentinel-Integration ein.
    1. Wählen Sie eine **Filterstufe** für die Bedrohungsdaten, die Sie an Microsoft Sentinel senden möchten.
    1. Wählen Sie **Fertig stellen** aus.

## <a name="zoom-reports-preview"></a>Zoom Reports (Vorschau)

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Azure Functions und die REST-API**](connect-azure-functions-template.md) |
| **Log Analytics-Tabellen** | Zoom_CL |
| **Azure Functions-App-Code** | https://aka.ms/Sentinel-ZoomAPI-functionapp |
| **API-Anmeldeinformationen** | <li>ZoomApiKey<li>ZoomApiSecret |
| **Herstellerdokumentation/<br>Installationsanweisungen** | <li>[Abrufen von Anmeldeinformationen mithilfe von JWT With Zoom](https://marketplace.zoom.us/docs/guides/auth/jwt) |
| **Anweisungen zur Connectorbereitstellung** | <li>[Bereitstellung mit nur einem Klick](connect-azure-functions-template.md?tabs=ARM) mithilfe von ARM-Vorlagen (Azure Resource Manager)<li>[Manuelle Bereitstellung](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto-Funktionsalias** | Zoom |
| **Kusto-Funktions-URL/<br>Anweisungen zur Parserkonfiguration** | https://aka.ms/Sentinel-ZoomAPI-parser |
| **Anwendungseinstellungen** | <li>ZoomApiKey<li>ZoomApiSecret<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (optional) |
| **Unterstützt von** | Microsoft |
| | |

## <a name="zscaler"></a>Zscaler

| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | **[Common Event Format (CEF)](connect-common-event-format.md) über Syslog** |
| **Log Analytics-Tabellen** | CommonSecurityLog |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Bereitstellungshandbuch für Zscaler und Microsoft Sentinel](https://aka.ms/ZscalerCEFInstructions) |
| **Unterstützt von** | [Zscaler](https://help.zscaler.com/submit-ticket-links) |
| | |


## <a name="zscaler-private-access-zpa-preview"></a>Zscaler Private Access (ZPA) (Vorschau)


| Connectorattribut | BESCHREIBUNG |
| --- | --- |
| **Datenerfassungsmethode** | [**Log Analytics-Agent: benutzerdefinierte Protokolle**](connect-custom-logs.md)<br><br>[Zusätzliche Konfiguration für Zscaler Private Access](#extra-configuration-for-zscaler-private-access) |
| **Log Analytics-Tabellen** | ZPA_CL |
| **Kusto-Funktionsalias:** | ZPAEvent |
| **Kusto-Funktions-URL** | https://aka.ms/Sentinel-zscalerprivateaccess-parser |
| **Herstellerdokumentation/<br>Installationsanweisungen** | [Dokumentation zu Zscaler Private Access](https://help.zscaler.com/zpa)<br>Weitere Informationen finden Sie auch unten. |
| **Unterstützt von** | Microsoft |
| | |

### <a name="extra-configuration-for-zscaler-private-access"></a>Zusätzliche Konfiguration für Zscaler Private Access

Führen Sie die folgenden Konfigurationsschritte aus, um Zscaler Private Access-Protokolle in Microsoft Sentinel abzurufen. Weitere Informationen finden Sie in der [Azure Monitor DoKumentation](../azure-monitor/agents/data-sources-json.md). Zscaler Private Access-Protokolle werden über LSS (Log Streaming Service, Protokollstreamingdienst) übermittelt. Ausführliche Informationen finden Sie in der [Dokumentation zu LSS](https://help.zscaler.com/zpa/about-log-streaming-service).

1. Konfigurieren Sie [Protokollempfänger](https://help.zscaler.com/zpa/configuring-log-receiver). Wählen Sie beim Konfigurieren eines Protokollempfängers **JSON** als **Protokollvorlage** aus.
1. Laden Sie die Konfigurationsdatei [zpa.conf](https://aka.ms/sentinel-zscalerprivateaccess-conf) herunter.

    ```bash
    wget -v https://aka.ms/sentinel-zscalerprivateaccess-conf -O zpa.conf
    ```

1. Melden Sie sich auf dem Server an, auf dem Sie den Azure Log Analytics-Agent installiert haben.
1. Kopieren Sie die Datei „zpa.conf“ in den Ordner „/etc/opt/microsoft/omsagent/`workspace_id`/conf/omsagent.d/“.
1. Bearbeiten Sie die Datei „zpa.conf“ wie folgt:
    1. Geben Sie den Port an, den Sie als Ziel für die Protokollweiterleitung durch Ihre Zscaler-Protokollempfänger festgelegt haben (Zeile 4)
    1. Ersetzen Sie `workspace_id` durch den tatsächlichen Wert Ihrer Arbeitsbereichs-ID (Zeilen 14, 15, 16, 19).
1. Speichern Sie Ihre Änderungen, und starten Sie den Azure Log Analytics-Agent für den Linux-Dienst mit dem folgenden Befehl neu:

    ```bash
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```
Den Wert Ihrer Arbeitsbereichs-ID finden Sie auf der Seite des ZScaler Private Access-Connectors oder auf der Verwaltungsseite für Agents Ihres Log Analytics-Arbeitsbereichs.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

- [Microsoft Sentinel-Lösungskatalog](sentinel-solutions-catalog.md)
- [ Integration von Bedrohungsdaten in Microsoft Sentinel](threat-intelligence-integration.md)
