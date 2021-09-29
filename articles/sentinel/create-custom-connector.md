---
title: Ressourcen zum Erstellen benutzerdefinierter Azure Sentinel-Connectors | Microsoft-Dokumentation
description: Erfahren Sie mehr über die verfügbaren Ressourcen zum Erstellen benutzerdefinierter Connectors für Azure Sentinel. Zu den Methoden zählen Log Analytics-Agent und API, Logstash, Logic Apps, PowerShell und Azure Functions.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2021
ms.author: bagol
ms.openlocfilehash: 3b578ffbb734c738aa3f082d192eae8557f4e307
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755101"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>Ressourcen zum Erstellen benutzerdefinierter Azure Sentinel-Connectors

Azure Sentinel bietet eine Vielzahl [integrierter Connectors für Azure-Dienste und externe Lösungen](connect-data-sources.md) und unterstützt außerdem das Erfassen von Daten aus einigen Quellen ohne einen dedizierten Connector.

Wenn Sie Ihre Datenquelle mit keiner der verfügbaren vorhandenen Lösungen mit Azure Sentinel verbinden können, sollten Sie die Erstellung Ihres eigenen Datenquellenconnectors in Erwägung ziehen.

Eine vollständige Liste der unterstützten Connectors finden Sie in dem Blogbeitrag [Azure Sentinel: Die wichtigsten Connectors (CEF, Syslog, Direct, Agent, Benutzerdefiniert und mehr)](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891).

## <a name="compare-custom-connector-methods"></a>Vergleichen von benutzerdefinierten Connectormethoden

In der folgenden Tabelle werden wesentliche Details jeder Methode zum Erstellen von benutzerdefinierten Connectors verglichen, die in diesem Artikel beschrieben werden. Wählen Sie die Links in der Tabelle aus, um weitere Informationen zu den einzelnen Methoden zu erhalten.

|Methodenbeschreibung  |Funktion | Serverlos    |Komplexität  |
|---------|---------|---------|---------|
|**[Log Analytics-Agent](#connect-with-the-log-analytics-agent)** <br>Optimal zum Sammeln von Dateien aus lokalen und IaaS-Quellen.   | Nur Dateisammlung.  |   Nein      |Niedrig         |
|**[Logstash](#connect-with-logstash)** <br>Optimal für lokale und IaaS-Quellen, für jede Quelle, für die ein Plug-In verfügbar ist, und für Organisationen, die bereits mit Logstash vertraut sind.  | Funktionen verfügbarer Plug-Ins sowie benutzerdefinierter Plug-Ins bieten beträchtliche Flexibilität.   |   Nein; zur Ausführung ist eine VM oder ein VM-Cluster erforderlich.           |   Niedrig; unterstützt viele Szenarien mit Plug-Ins.      |
|**[Logic Apps](#connect-with-logic-apps)** <br>Hohe Kosten; bei hohen Datenvolumen zu vermeiden. <br>Optimal für Cloudquellen mit niedrigen Volumen.  | Codelose Programmierung ermöglicht eingeschränkt Flexibilität, ohne die Implementierung von Algorithmen zu unterstützen.<br><br> Wenn Ihre Anforderungen von keiner der verfügbaren Aktionen bereits unterstützt werden, kann das Erstellen einer benutzerdefinierten Aktion die Komplexität erhöhen.    |    Ja         |   Niedrig; einfache Entwicklung ohne Code.      |
|**[PowerShell](#connect-with-powershell)** <br>Optimal für Prototypen und regelmäßiges Hochladen von Dateien. | Direkte Unterstützung für Dateisammlung. <br><br>PowerShell kann verwendet werden, um weitere Quellen zu erfassen, erfordert jedoch das Programmieren und Konfigurieren des Skripts als Dienst.      |Nein               |  Niedrig       |
|**[Log Analytics-API](#connect-with-the-log-analytics-api)** <br>Optimal für ISVs, die Integration implementieren, und für sehr spezifische Sammlungsanforderungen.   | Unterstützt alle mit dem Code verfügbaren Funktionen.  | Von der Implementierung abhängig.           |     Hoch    |
|**[Azure Functions](#connect-with-azure-functions)** Optimal für Cloudquellen mit hohen Volumen sowie für sehr spezifische Sammlungsanforderungen.  | Unterstützt alle mit dem Code verfügbaren Funktionen.  |  Ja             |     Hoch; erfordert Programmierkenntnisse.    |
|     |         |                |

> [!TIP]
> Vergleiche der Verwendung von Logic Apps und Azure Functions mit demselben Connector finden Sie unter:
> 
> - [Schnelles Erfassen von Web Application Firewall-Protokollen in Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (Azure Sentinel-GitHub-Community): [Logic Apps-Connector](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data) | [Azure Functions-Connector](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>Verbinden mit dem Log Analytics-Agent

Wenn Ihre Datenquelle Ereignisse in Dateien liefert, wird empfohlen, dass Sie den Azure Monitor Log Analytics-Agent verwenden, um Ihren benutzerdefinierten Connector zu erstellen.

- Weitere Informationen finden Sie unter [Sammeln benutzerdefinierter Protokolle in Azure Monitor](../azure-monitor/agents/data-sources-custom-logs.md).

- Ein Beispiel für diese Methode finden Sie unter [Erfassen benutzerdefinierter JSON-Datenquellen mit dem Log Analytics-Agent für Linux in Azure Monitor](../azure-monitor/agents/data-sources-json.md).

## <a name="connect-with-logstash"></a>Verbinden mit Logstash

Wenn Sie mit [Logstash](https://www.elastic.co/logstash)vertraut sind, können Sie Logstash mit dem [Logstash-Ausgabe-Plug-In für Azure Sentinel](connect-logstash.md) verwenden, um Ihren benutzerdefinierten Connector zu erstellen.

Mit dem Azure Sentinel Logstash-Ausgabe-Plug-In können Sie beliebige Logstash-Plug-Ins für Eingabe und Filterung verwenden und Azure Sentinel als Ausgabe für eine Logstash-Pipeline konfigurieren. Logstash verfügt über eine große Bibliothek von Plug-Ins, die die Eingabe aus verschiedenen Quellen ermöglichen, z. B. Event Hubs, Apache Kafka, Dateien, Datenbanken und Clouddiensten. Verwenden Sie Filter-Plug-Ins, um Ereignisse zu analysieren, unnötige Ereignisse zu filtern, Werte zu verbergen und vieles mehr.

Beispiele für die Verwendung von Logstash als benutzerdefinierten Connector finden Sie unter:

- [Suchen nach Capital One Breach TTPs in AWS-Protokollen mithilfe von Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767) (Blog)
- [Leitfaden für die Azure Sentinel-Implementierung von Radware](https://support.radware.com/ci/okcsFattach/get/1025459_3)

Beispiele für nützliche Logstash-Plug-Ins finden Sie unter:

- [Cloudwatch-Eingabe-Plug-In](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Azure Event Hubs-Plug-In](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Google Cloud Storage-Eingabe-Plug-In](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Google_pubsub-Eingabe-Plug-In](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> Logstash ermöglicht außerdem die skalierte Datensammlung mithilfe eines Clusters. Weitere Informationen finden Sie unter [Verwenden eines virtuellen Logstash-Computers mit Lastenausgleich im großen Stil](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854).
>

## <a name="connect-with-logic-apps"></a>Verbinden mit Logic Apps

Verwenden Sie [Azure Logik-Apps](../logic-apps/index.yml), um einen serverlosen, benutzerdefinierten Connector für Azure Sentinel zu erstellen.

> [!NOTE]
> Zwar kann das Erstellen von serverlosen Connectors mit Logic Apps praktisch sein, doch kann die Verwendung von Logic Apps für ihre Connectors bei großen Datenmengen kostenintensiv sein.
>
> Es wird empfohlen, diese Methode nur für Datenquellen mit geringen Datenvolumen zu verwenden oder Ihre Datenuploads anzureichern.
>

1. **Verwenden Sie einen der folgenden Trigger, um Ihre Logic Apps zu starten**:

    |Trigger  |BESCHREIBUNG  |
    |---------|---------|
    |**Eine wiederkehrende Aufgabe**     |   Planen Sie Ihre Logik-App beispielsweise so, dass Daten regelmäßig aus bestimmten Dateien, Datenbanken oder externen APIs abgerufen werden. <br>Weitere Informationen finden Sie unter [Erstellen, Planen und Ausführen wiederkehrender Aufgaben und Workflows mit Azure Logic Apps](../connectors/connectors-native-recurrence.md).      |
    |**On-Demand-Trigger**     | Führen Sie Ihre Logik-App bei Bedarf für manuelle Datenerfassung und Tests aus. <br>Weitere Informationen finden Sie unter [Aufrufen, Auslösen oder Schachteln von Logik-Apps mithilfe von HTTP-Endpunkten](../logic-apps/logic-apps-http-endpoint.md).        |
    |**HTTP/S-Endpunkt**     |  Empfohlen für das Streaming und wenn das Quellsystem die Datenübertragung starten kann. <br>Weitere Informationen finden Sie unter [Aufrufen von Dienstendpunkten über HTTP oder HTTPS](../connectors/connectors-native-http.md).       |
    |     |         |

1. **Verwenden Sie einen der Logik-App-Connectors, die Informationen lesen, um Ihre Ereignisse abzurufen**. Beispiel:

    - [Herstellen einer Verbindung mit einer REST-API](/connectors/custom-connectors/)
    - [Herstellen einer Verbindung mit SQL Server](/connectors/sql/)
    - [Herstellen einer Verbindung mit einem Dateisystem](/connectors/filesystem/)

    > [!TIP]
    > Benutzerdefinierte Connectors für REST-APIs, SQL-Server und Dateisysteme unterstützen auch das Abrufen von Daten aus lokalen Datenquellen. Weitere Informationen finden Sie in der Dokumentation zum [Installieren eines lokalen Datengateways](/connectors/filesystem/).
    >

1. **Bereiten Sie die Informationen vor, die Sie abrufen möchten**.

    Verwenden Sie beispielsweise die [Aktion „JSON analysieren“](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action), um auf Eigenschaften in JSON-Inhalten zuzugreifen, was Ihnen ermöglicht, diese Eigenschaften aus der Liste dynamischer Inhalte auszuwählen, wenn Sie Eingaben für Ihre Logik-App angeben.

    Weitere Informationen finden Sie unter [Ausführen von Datenvorgängen in Azure Logic Apps](../logic-apps/logic-apps-perform-data-operations.md).

1. **Schreiben Sie die Daten in Log Analytics**.

    Weitere Informationen finden Sie in der Dokumentation zum [Azure Log Analytics-Datensammler](/connectors/azureloganalyticsdatacollector/).

Beispiele dazu, wie Sie einen benutzerdefinierten Connector für Azure Sentinel mithilfe von Logic Apps erstellen können, finden Sie unter:

- [Erstellen einer Datenpipeline mit der Datensammler-API](/connectors/azureloganalyticsdatacollector/)
- [Palo Alto Prisma Logik-App-Connector mithilfe eines Webhooks](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (Azure Sentinel-GitHub-Community)
- [Sichern Ihrer Microsoft Teams-Anrufe mit geplanter Aktivierung](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600) (Blog)
- [Erfassen von AlienVault OTX-Bedrohungsindikatoren in Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) (Blog)

## <a name="connect-with-powershell"></a>Verbinden mit PowerShell

Das [PowerShell-Skript „Upload-AzMonitorLog“](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) ermöglicht Ihnen die Verwendung von PowerShell, um Ereignisse oder Kontextinformationen von der Befehlszeile an Azure Sentinel zu streamen. Dieses Streaming erstellt tatsächlich einen benutzerdefinierten Connector zwischen Ihrer Datenquelle und Azure Sentinel.

Beispielsweise lädt das folgende Skript eine CSV-Datei in Azure Sentinel hoch:

``` PowerShell
Import-Csv .\testcsv.csv
| .\Upload-AzMonitorLog.ps1
-WorkspaceId '69f7ec3e-cae3-458d-b4ea-6975385-6e426'
-WorkspaceKey $WSKey
-LogTypeName 'MyNewCSV'
-AddComputerName
-AdditionalDataTaggingName "MyAdditionalField"
-AdditionalDataTaggingValue "Foo"
```

Das [PowerShell-Skript „Upload-AzMonitorLog“](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) verwendet die folgenden Parameter:

|Parameter  |BESCHREIBUNG  |
|---------|---------|
|**WorkspaceId**     |   Die ID Ihres Azure Sentinel-Arbeitsbereichs, in dem Sie Ihre Daten speichern möchten.  [Suchen Sie Ihre Arbeitsbereichs-ID und den Schlüssel](#find-your-workspace-id-and-key).  |
|**WorkspaceKey**     |   Der primäre oder sekundäre Schlüssel für den Azure Sentinel-Arbeitsbereich, in dem Sie Ihre Daten speichern möchten. [Suchen Sie Ihre Arbeitsbereichs-ID und den Schlüssel](#find-your-workspace-id-and-key).  |
|**LogTypeName**     |    Der Name der benutzerdefinierten Protokolltabelle, in der Sie die Daten speichern möchten. Das Suffix **_CL** wird automatisch am Ende des Tabellennamens hinzugefügt.  |
|**AddComputerName**     |   Wenn dieser Parameter vorhanden ist, fügt das Skript den aktuellen Computernamen jedem Protokolldatensatz in einem Feld namens **Computer** hinzu.      |
|**TaggedAzureResourceId**     | Wenn dieser Parameter vorhanden ist, ordnet das Skript alle hochgeladenen Protokolldatensätze der angegebenen Azure-Ressource zu. <br><br>Diese Zuordnung ermöglicht Ressourcenkontextabfragen an die hochgeladenen Protokolldatensätze und hält dabei die ressourcenzentrierte, rollenbasierte Zugriffssteuerung ein.       |
|**AdditionalDataTaggingName**     |      Wenn dieser Parameter vorhanden ist, fügt das Skript jedem Protokolldatensatz ein weiteres Feld mit dem konfigurierten Namen und dem Wert hinzu, der für den Parameter **AdditionalDataTaggingValue** konfiguriert ist. <br><br>In diesem Fall darf **AdditionalDataTaggingValue** nicht leer sein. |
|**AdditionalDataTaggingValue**     |   Wenn dieser Parameter vorhanden ist, fügt das Skript jedem Protokolldatensatz ein weiteres Feld mit dem konfigurierten Wert und dem Feldnamen hinzu, der für den Parameter **AdditionalDataTaggingName** konfiguriert ist. <br><br>Wenn der Parameter **AdditionalDataTaggingName** leer, aber ein Wert konfiguriert ist, lautet der Standardfeldname **DataTagging**.       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>Suchen Ihrer Arbeitsbereichs-ID und des Schlüssels

Suchen Sie die Details zu den Parametern **WorkspaceID** und **WorkspaceKey** in Azure Sentinel wie folgt:

1. Wählen Sie in Azure Sentinel auf der linken Seite **Einstellungen** und dann die Registerkarte **Arbeitsbereichseinstellungen** aus.

1. Wählen Sie unter **Erste Schritte mit Log Analytics** > **1 Datenquelle verbinden** die Option **Verwaltung von Windows- und Linux-Agents** aus.

1. Suchen Sie Ihre Arbeitsbereichs-ID, den Primärschlüssel und den sekundären Schlüssel auf den Registerkarten **Windows-Server**.
## <a name="connect-with-the-log-analytics-api"></a>Verbinden mit der Log Analytics-API

Sie können Ereignisse an Azure Sentinel streamen, indem Sie die Log Analytics-Datensammler-API verwenden, um einen RESTful-Endpunkt direkt aufzurufen.

Zwar erfordert das direkte Aufrufen eines RESTful-Endpunkts mehr Programmieraufwand, doch bietet es auch mehr Flexibilität.

Weitere Informationen finden Sie unter [Log Analytics-Datensammler-API](../azure-monitor/logs/data-collector-api.md), insbesondere in den folgenden Beispielen:

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python 2](../azure-monitor/logs/data-collector-api.md#python-2-sample)

## <a name="connect-with-azure-functions"></a>Verbinden mit Azure Functions

Verwenden Sie Azure Functions in Verbindung mit einer RESTful-API und verschiedenen Programmiersprachen, z. B. [PowerShell](../azure-functions/functions-reference-powershell.md), um einen serverlosen, benutzerdefinierten Connector zu erstellen.

Beispiele für diese Methode finden Sie unter:

- [Verbinden Ihres VMware Carbon Black Cloud Endpoint Standard mithilfe von Azure Functions mit Azure Sentinel](./data-connectors-reference.md#vmware-carbon-black-endpoint-standard-preview)
- [Verbinden von Okta Single Sign-On mit Azure Sentinel über Azure Functions](./data-connectors-reference.md#okta-single-sign-on-preview)
- [Verknüpfen von Proofpoint TAP mit Azure Sentinel per Azure-Funktion](./data-connectors-reference.md#proofpoint-targeted-attack-protection-tap-preview)
- [Verbinden Ihrer Qualys VM-Instanz mithilfe von Azure Function mit Azure Sentinel](./data-connectors-reference.md#qualys-vulnerability-management-vm-preview)
- [Erfassen von XML-, CSV- oder anderen Datenformaten](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [Überwachen von Zoom mit Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (Blog)
- [Bereitstellen einer Funktions-App zum Abrufen von Office 365-Verwaltungs-API-Daten in Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) (Azure Sentinel-GitHub-Community)

## <a name="parse-your-custom-connector-data"></a>Analysieren der Daten Ihres benutzerdefinierten Connectors

Sie können die in Ihrem benutzerdefinierten Connector integrierte Analysemethode verwenden, um die relevanten Informationen zu extrahieren und die relevanten Felder in Azure Sentinel aufzufüllen.

Beispiel:

- **Wenn Sie Logstash verwendet haben**, verwenden Sie das [Grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html)-Filter-Plug-In, um Ihre Daten zu analysieren.
- **Wenn Sie eine Azure-Funktion verwendet haben**, analysieren Sie Ihre Daten mit Code.

Azure Sentinel unterstützt die Analyse zur Abfragezeit. Die Analyse zur Abfragezeit ermöglicht es Ihnen, Daten im ursprünglichen Format per Push als Eingabe zu übergeben und dann bei Bedarf zu analysieren. 

Die Analyse zur Abfragezeit bedeutet ferner, dass Sie die genaue Struktur Ihrer Daten nicht im Voraus kennen müssen, wenn Sie Ihren benutzerdefinierten Connector erstellen, und sogar auch die Informationen, die Sie extrahieren müssen, nicht. Analysieren Sie stattdessen Ihre Daten jederzeit, auch während einer Untersuchung.

Weitere Informationen zur Analyse der Abfragezeit finden Sie unter [Parsers](normalization-about-parsers.md).

> [!NOTE]
> Eine Aktualisierung Ihres Parsers gilt auch für Daten, die Sie bereits in Azure Sentinel erfasst haben.
> 
## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die in Azure Sentinel erfassten Daten, um Ihre Umgebung mit einem der folgenden Prozesse zu sichern:

- [Einblick in Warnungen](get-visibility.md)
- [Visualisieren und Überwachen Ihrer Daten](monitor-your-data.md)
- [Untersuchen von Vorfällen](investigate-cases.md)
- [Erkennen von Bedrohungen](detect-threats-built-in.md)
- [Automatisieren des Bedrohungsschutzes](tutorial-respond-threats-playbook.md)
- [Suchen nach Bedrohungen](hunting.md)

Außerdem erfahren Sie mehr über ein Beispiel zum Erstellen eines benutzerdefinierten Connectors für die Überwachung von Zoom: [Überwachen von Zoom mit Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516).