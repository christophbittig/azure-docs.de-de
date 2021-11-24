---
title: SOAR-Inhaltskatalog für Azure Sentinel | Microsoft-Dokumentation
description: In diesem Artikel werden die von Azure Sentinel bereitgestellten Inhalte für Sicherheitsorchestrierung, Automatisierung und Reaktion (Security Orchestration, Automation, and Response, SOAR), einschließlich Playbooks und Logic Apps-Connectors, gezeigt und ausführlich erläutert.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/18/2021
ms.author: yelevin
ms.openlocfilehash: 5f9c68a63c63c3b4cc27b44d697f7577e4dafc3c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517974"
---
# <a name="azure-sentinel-soar-content-catalog"></a>SOAR-Inhaltskatalog für Azure Sentinel

Azure Sentinel bietet eine Vielzahl von Playbooks und Connectors für Sicherheitsorchestrierung, Automatisierung und Reaktion (Security Orchestration, Automation, and Response, SOAR), sodass Sie Azure Sentinel problemlos in jedes Produkt oder jeden Dienst in Ihrer Umgebung integrieren können.

Die nachfolgend aufgeführten Integrationen können einige oder alle der folgenden Komponenten umfassen:

| Komponententyp | Zweck | Anwendungsfall und verknüpfte Anweisungen |
| -------------- | ------- | -------------------------------- |
| **Playbookvorlagen** | Automatisierter Workflow | Verwenden Sie Playbookvorlagen, um vorgefertigte Playbooks für die automatische Reaktion auf Bedrohungen bereitzustellen.<br><br>[Automatisieren der Bedrohungsabwehr mit Playbooks in Azure Sentinel](automate-responses-with-playbooks.md) |
| **Verwalteter Azure Logic Apps-Connector** | Bausteine zum Erstellen von Playbooks | Playbooks verwenden verwaltete Connectors für die Kommunikation mit hunderten von Microsoft- und Nicht-Microsoft-Diensten.<br><br>[Liste der Logic Apps-Connectors und der zugehörigen Dokumentation](/connectors/connector-reference/) |
| **Benutzerdefinierter Azure Logic Apps-Connector** | Bausteine zum Erstellen von Playbooks | Möglicherweise möchten Sie mit Diensten kommunizieren, die nicht als vorgefertigte Connectors verfügbar sind. Benutzerdefinierte Konnektoren adressieren diesen Bedarf, indem Sie einen Konnektor erstellen (und sogar freigeben) und seine eigenen Auslöser und Aktionen definieren können.<br><ul><li>[Übersicht über benutzerdefinierte Connectors](/connectors/custom-connectors/)<li>[Erstellen eigener benutzerdefinierter Logic Apps Konnektoren](/connectors/custom-connectors/create-logic-apps-connector) |
|

SOAR-Integrationen und deren Komponenten finden Sie an folgenden Orten:

- Azure Sentinel-Lösungen
- Azure Sentinel-Blatt „Automation“, Registerkarte „Playbookvorlagen“
- Logic Apps-Designer (für verwaltete Logic Apps-Connectors)
- Azure Sentinel-GitHub-Repository

> [!TIP]
> - Viele SOAR-Integrationen können als Teil einer [Azure Sentinel-Lösung](sentinel-solutions.md) zusammen mit zugehörigen Datenconnectors, Analyseregeln und Arbeitsmappen bereitgestellt werden. Weitere Informationen finden Sie im [Azure Sentinel-Lösungskatalog](sentinel-solutions-catalog.md).
> - Die Azure Sentinel-Community stellt noch weitere Integrationen bereit, die Sie im GitHub-Repository finden.
> - Wenn Sie über ein Produkt oder einen Dienst verfügen, das bzw. der nicht aufgelistet ist oder derzeit nicht unterstützt wird, senden Sie eine Featureanforderung.  
> Mithilfe der folgenden Tools können Sie auch eigene erstellen:
>    - Benutzerdefinierter Logic Apps-Connector
>    - Azure-Funktionen
>    - Logic Apps-HTTP-Aufrufe


## <a name="atlassian"></a>Atlassian

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Jira** | [Verwalteter Logic Apps-Connector](/connectors/jira/)<br><br>Playbooks | Microsoft<br><br>Community | Synchronisieren von Incidents |
|

## <a name="check-point"></a>Check Point

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Check Point NGFW**<br>([Als Lösung verfügbar](sentinel-solutions-catalog.md#check-point)) | Benutzerdefinierter Logic Apps-Connector<br><br>Playbooks | CheckPoint |  |
|

## <a name="cisco"></a>Cisco

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Cisco ASA,<br>Cisco Meraki** | Benutzerdefinierter Logic Apps-Connector<br><br>Playbooks | Community | Blockieren von IP-Adressen |
| **Cisco FirePower** | Benutzerdefinierter Logic Apps-Connector<br><br>Playbooks | Community | Blockieren von IP-Adressen und URLs |
| **Cisco ISE**<br>([Als Lösung verfügbar](sentinel-solutions-catalog.md#cisco)) | Benutzerdefinierter Logic Apps-Connector<br><br>Playbooks | Microsoft |  |
| **Cisco Umbrella**<br>([Als Lösung verfügbar](sentinel-solutions-catalog.md#cisco)) | Benutzerdefinierter Logic Apps-Connector<br><br>Playbooks | Microsoft | Blockieren von Domänen, <br>Richtlinienverwaltung, <br>Ziellistenverwaltung, <br>Anreicherung und Untersuchung |
|

## <a name="crowdstrike"></a>Crowdstrike

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Falcon-Endpunktschutz**<br>([Als Lösung verfügbar](sentinel-solutions-catalog.md#crowdstrike)) | Playbooks | Microsoft | Endpunktanreicherung,<br>Isolieren von Endpunkten |
|

## <a name="f5"></a>F5

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Big-IP** | Playbooks | Community | Blockieren von IP-Adressen und URLs |
|

## <a name="forcepoint"></a>Forcepoint

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Forcepoint NGFW** | Benutzerdefinierter Logic Apps-Connector<br><br>Playbooks | Community | Blockieren von IP-Adressen und URLs |
|

## <a name="fortinet"></a>Fortinet

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **FortiGate**<br>([Als Lösung verfügbar](sentinel-solutions-catalog.md#fortinet-fortigate)) | Benutzerdefinierter Logic Apps-Connector<br><br>Azure Function<br><br>Playbooks | Microsoft | Blockieren von IP-Adressen und URLs |
|

## <a name="freshdesk"></a>Freshdesk

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Freshdesk** | [Verwalteter Logic Apps-Connector](/connectors/freshdesk/) |  | Synchronisieren von Incidents |
|


## <a name="have-i-been-pwned"></a>have i been pwned

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **have i been pwned** | Benutzerdefinierter Logic Apps-Connector<br><br>Playbooks | Community |  |
|

## <a name="hyas"></a>HYAS

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **HYAS Insight**<br>([Als Lösung verfügbar](sentinel-solutions-catalog.md#hyas)) | [Verwalteter Logic Apps-Connector](/connectors/hyasinsight/)<br><br>Playbooks | HYAS |  |
|

## <a name="ibm"></a>IBM

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Resilient** | Benutzerdefinierter Logic Apps-Connector<br><br>Playbooks | Community | Synchronisieren von Incidents |
|

## <a name="microsoft"></a>Microsoft

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Azure DevOps** | Verwalteter Logic Apps-Connector<br><br>Playbooks | Microsoft<br><br>Community | Synchronisieren von Incidents |
| **Azure Firewall**<br>([Als Lösung verfügbar](sentinel-solutions-catalog.md#azure)) | Benutzerdefinierter Logic Apps-Connector<br><br>Playbooks | Microsoft | Blockieren von IP-Adressen |
| **Azure AD Identity Protection** | [Verwalteter Logic Apps-Connector](/connectors/azureadip/)<br><br>Playbooks | Microsoft<br><br>Community | Benutzerergänzung, <br>Benutzerkorrektur |
| **Azure AD** | [Verwalteter Logic Apps-Connector](/connectors/azuread/)<br><br>Playbooks | Microsoft<br><br>Community | Benutzerergänzung, <br>Benutzerkorrektur |
| **Azure Data Explorer** | [Verwalteter Logic Apps-Connector](/connectors/kusto/) | Microsoft | Abfragen und Untersuchen |
| **Datensammler von Azure Log Analytics** | [Verwalteter Logic Apps-Connector](/connectors/azureloganalyticsdatacollector/) | Microsoft<br><br>Community | Abfragen und Untersuchen |
| **Microsoft Defender für den Endpunkt** | [Verwalteter Logic Apps-Connector](/connectors/wdatp/)<br><br>Playbooks | Microsoft<br><br>Community | Endpunktanreicherung, <br>Isolieren von Endpunkten |
| **Microsoft Teams** | [Verwalteter Logic Apps-Connector](/connectors/teams/)<br><br>Playbooks | Microsoft<br><br>Community | Benachrichtigungen, <br>Zusammenarbeit, <br>Erstellen von Antworten, an denen Menschen beteiligt sind |
|

## <a name="okta"></a>Okta

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Okta** | Verwalteter Logic Apps-Connector<br><br>Playbooks | Community | Benutzerergänzung, <br>Benutzerkorrektur |
|

## <a name="palo-alto"></a>Garmisch-Partenkirchen

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Palo Alto PAN-OS**<br>([Als Lösung verfügbar](sentinel-solutions-catalog.md#palo-alto)) | Benutzerdefinierter Logic Apps-Connector<br><br>Playbooks | Community | Blockieren von IP-Adressen und URLs |
| **Wildfire** | Benutzerdefinierter Logic Apps-Connector<br><br>Playbooks | Community | FileHash-Anreicherung und -Antwort |
|

## <a name="proofpoint"></a>Proofpoint

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Proofpoint TAP**<br>([Als Lösung verfügbar](sentinel-solutions-catalog.md#proofpoint)) | Benutzerdefinierter Logic Apps-Connector<br><br>Playbooks | Microsoft | Kontoanreicherung |
|

## <a name="recorded-future"></a>Recorded Future

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Recorded Future Intelligence** | [Verwalteter Logic Apps-Connector](/connectors/recordedfuture/)<br><br>Playbooks | Recorded Future | Entitätsanreicherung |
|

## <a name="reversinglabs"></a>ReversingLabs

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **TitaniumCloud File Enrichment**<br>([Als Lösung verfügbar](sentinel-solutions-catalog.md#reversinglabs)) | [Verwalteter Logic Apps-Connector](/connectors/reversinglabsintelligence/)<br><br>Playbooks | ReversingLabs | FileHash-Anreicherung |
|

## <a name="riskiq"></a>RiskIQ

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **RiskIQ Digital Footprint**<br>([Als Lösung verfügbar](sentinel-solutions-catalog.md#riskiq)) | [Verwalteter Logic Apps-Connector](/connectors/riskiqdigitalfootprint/)<br><br>Playbooks | RiskIQ | Entitätsanreicherung |
| **RiskIQ Passive Total** | [Verwalteter Logic Apps-Connector](/connectors/riskiqpassivetotal/)<br><br>Playbooks | RiskIQ | Entitätsanreicherung |
| **RiskIQ Security Intelligence**<br>([Als Lösung verfügbar](sentinel-solutions-catalog.md#riskiq)) | [Verwalteter Logic Apps-Connector](/connectors/riskiqintelligence/)<br><br>Playbooks | RiskIQ | Entitätsanreicherung |
|

## <a name="servicenow"></a>ServiceNow

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **ServiceNow** | [Verwalteter Logic Apps-Connector](/connectors/service-now/)<br><br>Playbooks | Microsoft<br><br>Community | Synchronisieren von Incidents |
|

## <a name="slack"></a>Slack

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Puffer** | [Verwalteter Logic Apps-Connector](/connectors/slack/)<br><br>Playbooks | Microsoft<br><br>Community | Benachrichtigung, <br>Zusammenarbeit |
|

## <a name="virus-total"></a>Virus Total

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Virus Total** | [Verwalteter Logic Apps-Connector](/connectors/virustotal/)<br><br>Playbooks | Microsoft<br><br>Community | Entitätsanreicherung |
|

## <a name="vmware"></a>VMware

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Carbon Black Cloud**<br>([Als Lösung verfügbar](sentinel-solutions-catalog.md#vmware)) | Benutzerdefinierter Logic Apps-Connector<br><br>Playbooks | Community | Endpunktanreicherung, <br>Isolieren von Endpunkten |
|

## <a name="zendesk"></a>Zendesk

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Zendesk** | [Verwalteter Logic Apps-Connector](/connectors/zendesk/)<br><br>Playbooks | Microsoft<br><br>Community | Synchronisieren von Incidents |
|

## <a name="zscaler"></a>Zscaler

| Produkt | Integrationskomponenten | Unterstützt von | Szenarien |
| --- | --- | --- | --- |
| **Zscaler** | Playbooks | Microsoft | URL-Korrektur, <br>Incidentanreicherung |
|

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie mehr über Azure Sentinel-Lösungen gelernt und erfahren, wie Sie diese suchen und bereitstellen.

- [Weitere Informationen zu Azure Sentinel-Lösungen](sentinel-solutions.md)
- [Suchen und Bereitstellen von Azure Sentinel-Lösungen](sentinel-solutions-deploy.md)
