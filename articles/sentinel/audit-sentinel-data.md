---
title: Überwachen von Microsoft Sentinel-Abfragen und -Aktivitäten | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie in Microsoft Sentinel ausgeführte Abfragen und Aktivitäten überwachen.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: d1524139049b77cfeeff58563904489ae49728ef
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518050"
---
# <a name="audit-microsoft-sentinel-queries-and-activities"></a>Überwachen von Microsoft Sentinel-Abfragen und -Aktivitäten

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel wird beschrieben, wie Sie Überwachungsdaten für ausgeführte Abfragen und Aktivitäten in Ihrem Microsoft Sentinel-Arbeitsbereich anzeigen können, z. B. für interne und externe Complianceanforderungen im SOC-Arbeitsbereich (Security Operations).

Microsoft Sentinel bietet Zugriff auf:

- Die Tabelle **AzureActivity**, die Details zu allen in Microsoft Sentinel ausgeführten Aktionen bereitstellt, z. B. zum Bearbeiten von Warnungsregeln. In der Tabelle **AzureActivity** werden bestimmte Abfragedaten nicht protokolliert. Weitere Informationen finden Sie unter [Überwachen mit Azure-Aktivitätsprotokollen](#auditing-with-azure-activity-logs).

- Die Tabelle **LAQueryLogs**, die Details zu den Abfragen bereitstellt, die in Log Analytics ausgeführt werden, einschließlich der von Microsoft Sentinel ausgeführten Abfragen. Weitere Informationen finden Sie unter [Überwachung mit LAQueryLogs](#auditing-with-laquerylogs).

> [!TIP]
> Zusätzlich zu den in diesem Artikel beschriebenen manuellen Abfragen bietet Microsoft Sentinel eine integrierte Arbeitsmappe, die Sie beim Überwachen der Aktivitäten in ihrer SOC-Umgebung unterstützt.
>
> Suchen Sie im Microsoft Sentinel-Bereich **Arbeitsmappen** nach der Arbeitsmappe **Arbeitsbereichsüberwachung**.

## <a name="auditing-with-azure-activity-logs"></a>Überwachen mit Azure-Aktivitätsprotokollen

Die Überwachungsprotokolle von Microsoft Sentinel werden in den [Azure-Aktivitätsprotokollen](../azure-monitor/essentials/platform-logs-overview.md) verwaltet, in denen die Tabelle **AzureActivity** alle Aktionen enthält, die in Ihrem Microsoft Sentinel-Arbeitsbereich ausgeführt werden.

Sie können die Tabelle **AzureActivity** verwenden, wenn Sie die Aktivität in Ihrer SOC-Umgebung mit Microsoft Sentinel überwachen.

**So fragen Sie die Tabelle AzureActivity ab**:

1. Verbinden Sie die [Azure-Aktivitäts](./data-connectors-reference.md#azure-activity)datenquelle, um das Streamen von Überwachungsereignissen in eine neue Tabelle auf dem Bildschirm **Protokolle** mit dem Namen AzureActivity zu starten.

1. Fragen Sie die Daten dann wie jede andere Tabelle mit KQL ab.

    Die Tabelle **AzureActivity** enthält Daten aus vielen Diensten, einschließlich Microsoft Sentinel. Wenn Sie nur Daten aus Microsoft Sentinel filtern möchten, starten Sie die Abfrage mit folgendem Code:

    ```kql
     AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS"
    ```

    Um beispielsweise herauszufinden, wer der letzte Benutzer war, der eine bestimmte Analyseregel bearbeitet hat, verwenden Sie die folgende Abfrage (ersetzen Sie `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` durch die Regel-ID der Regel, die Sie überprüfen möchten):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```

Fügen Sie Ihrer Abfrage weitere Parameter hinzu, um die Tabelle **AzureActivities** weiter zu untersuchen, je nachdem, was Sie melden müssen. In den folgenden Abschnitten finden Sie weitere Beispielabfragen für die Überwachung mit Daten der Tabelle **AzureActivity**.

Weitere Informationen finden Sie unter [In Azure-Aktivitätsprotokollen enthaltene Microsoft Sentinel-Daten](#microsoft-sentinel-data-included-in-azure-activity-logs).

### <a name="find-all-actions-taken-by-a-specific-user-in-the-last-24-hours"></a>Suchen aller Aktionen, die von einem bestimmten Benutzer in den letzten 24 Stunden ausgeführt wurden

Die folgende **AzureActivity**-Tabellenabfrage listet alle Aktionen auf, die von einem bestimmten Azure AD-Benutzer in den letzten 24 Stunden ausgeführt wurden.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where Caller == "[AzureAD username]"
| where TimeGenerated > ago(1d)
```

### <a name="find-all-delete-operations"></a>Suchen aller Löschvorgänge

Die folgende **AzureActivity**-Tabellenabfrage listet alle Löschvorgänge auf, die in Ihrem Microsoft Sentinel-Arbeitsbereich ausgeführt wurden.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where OperationName contains "Delete"
| where ActivityStatusValue contains "Succeeded"
| project TimeGenerated, Caller, OperationName
```

### <a name="microsoft-sentinel-data-included-in-azure-activity-logs"></a>In Azure-Aktivitätsprotokollen enthaltene Microsoft Sentinel-Daten

Die Überwachungsprotokolle von Microsoft Sentinel werden in den [Azure-Aktivitätsprotokollen](../azure-monitor/essentials/platform-logs-overview.md) verwaltet und enthalten die folgenden Informationstypen:

|Vorgang  |Informationstypen  |
|---------|---------|
|**Erstellt**     |Warnregeln <br> Case-Kommentare <br>Kommentare zum Incident <br>Gespeicherte Suchvorgänge<br>Watchlists    <br>Arbeitsmappen     |
|**Gelöscht**     |Warnregeln <br>Lesezeichen <br>Datenconnectors <br>Vorfälle <br>Gespeicherte Suchvorgänge <br>Einstellungen <br>Threat Intelligence-Berichte <br>Watchlists      <br>Arbeitsmappen <br>Workflow  |
|**Aktualisiert**     |  Warnregeln<br>Lesezeichen <br> Fälle <br> Datenconnectors <br>Vorfälle <br>Kommentare zum Incident <br>Threat Intelligence-Berichte <br> Arbeitsmappen <br>Workflow       |
|     |         |

Sie können die Azure-Aktivitätsprotokolle auch verwenden, um nach Benutzerautorisierungen und Lizenzen zu suchen.

In der folgenden Tabelle sind z. B. die in Azure-Aktivitätsprotokollen gefundenen ausgewählten Vorgänge mit der jeweiligen Ressource aufgelistet, aus der die Protokolldaten abgerufen werden.

|Vorgangsname| Ressourcentyp|
|----|----|
|Arbeitsmappe erstellen oder aktualisieren |Microsoft.Insights/workbooks|
|Arbeitsmappe löschen |Microsoft.Insights/workbooks|
|Workflow festlegen |Microsoft.Logic/workflows|
|Workflow löschen |Microsoft.Logic/workflows|
|Gespeicherten Suchvorgang erstellen |Microsoft.OperationalInsights/workspaces/savedSearches|
|Gespeicherte Suche löschen |Microsoft.OperationalInsights/workspaces/savedSearches|
|Warnungsregeln aktualisieren |Microsoft.SecurityInsights/alertRules|
|Warnungsregeln löschen |Microsoft.SecurityInsights/alertRules|
|Antwortaktionen für Warnungsregeln aktualisieren |Microsoft.SecurityInsights/alertRules/actions|
|Antwortaktionen für Warnungsregeln löschen |Microsoft.SecurityInsights/alertRules/actions|
|Lesezeichen aktualisieren |Microsoft.SecurityInsights/bookmarks|
|Lesezeichen löschen |Microsoft.SecurityInsights/bookmarks|
|Fälle aktualisieren |Microsoft.SecurityInsights/Cases|
|Untersuchung von Fällen aktualisieren |Microsoft.SecurityInsights/Cases/investigations|
|Kommentare zu Fällen erstellen |Microsoft.SecurityInsights/Cases/comments|
|Datenconnectors aktualisieren |Microsoft.SecurityInsights/dataConnectors|
|Datenconnectors löschen |Microsoft.SecurityInsights/dataConnectors|
|Aktualisieren der Einstellungen |Microsoft.SecurityInsights/settings|
| | |

Weitere Informationen finden Sie unter [Ereignisschema des Azure-Aktivitätsprotokolls](../azure-monitor/essentials/activity-log-schema.md).

## <a name="auditing-with-laquerylogs"></a>Überwachung mit LAQueryLogs

Die Tabelle **LAQueryLogs** enthält Details zu Protokollabfragen, die in Log Analytics ausgeführt werden. Da Log Analytics als zugrunde liegender Datenspeicher von Microsoft Sentinel verwendet wird, können Sie Ihr System so konfigurieren, dass LAQueryLogs-Daten in Ihrem Microsoft Sentinel-Arbeitsbereich erfasst werden.

LAQueryLogs-Daten enthalten beispielsweise folgende Informationen:

- Wann Abfragen ausgeführt wurden
- Wer Abfragen in Log Analytics ausführte
- Welches Tool zum Ausführen von Abfragen in Log Analytics verwendet wurde, z. B. Microsoft Sentinel
- Die Abfragetexte selbst
- Leistungsdaten bei jeder Abfrageausführung

> [!NOTE]
> - Die Tabelle **LAQueryLogs** enthält nur Abfragen, die auf dem Blatt „Protokolle“ von Microsoft Sentinel ausgeführt wurden. Sie enthält nicht die Abfragen, die von geplanten Analyseregeln mit dem **Untersuchungsdiagramm** oder auf der Seite **Hunting** von Microsoft Sentinel ausgeführt werden.
> - Zwischen dem Zeitpunkt, zu dem eine Abfrage ausgeführt wird, und dem Füllen der Daten in der Tabelle **LAQueryLogs** kann es zu einer kurzen Verzögerung kommen. Es wird empfohlen, ungefähr 5 Minuten auf die Abfrage der Tabelle **LAQueryLogs** für Überwachungsdaten zu warten.

**So fragen Sie die Tabelle LAQueryLogs ab**:

1. Die Tabelle **LAQueryLogs** ist in Ihrem Log Analytics-Arbeitsbereich standardmäßig nicht aktiviert. Wenn Sie bei der Überwachung in Microsoft Sentinel **LAQueryLogs**-Daten verwenden möchten, aktivieren Sie zuerst **LAQueryLogs** im Bereich **Diagnoseeinstellungen** in Ihrem Log Analytics-Arbeitsbereich.

    Weitere Informationen finden Sie unter [Überwachen von Abfragen in Azure Monitor-Protokollen](../azure-monitor/logs/query-audit.md).

1. Fragen Sie die Daten dann wie jede andere Tabelle mit KQL ab.

    Die folgende Abfrage zeigt z. B. an, wie viele Abfragen in der letzten Woche pro Tag ausgeführt wurden:

    ```kql
    LAQueryLogs
    | where TimeGenerated > ago(7d)
    | summarize events_count=count() by bin(TimeGenerated, 1d)
    ```

In den folgenden Abschnitten werden weitere Beispielabfragen gezeigt, die für die Tabelle **LAQueryLogs** ausgeführt werden können, wenn Sie Aktivitäten in Ihrer SOC-Umgebung mithilfe von Microsoft Sentinel überwachen.

### <a name="the-number-of-queries-run-where-the-response-wasnt-ok"></a>Die Anzahl der ausgeführten Abfragen, bei denen die Antwort nicht „OK“ war

Die folgende **LAQueryLogs**-Tabellenabfrage zeigt die Anzahl der ausgeführten Abfragen an, bei denen eine andere HTTP-Antwort als **200 OK** erhalten wurde. Diese Zahl enthält z. B. Abfragen, die nicht ausgeführt werden konnten.

```kql
LAQueryLogs
| where ResponseCode != 200 
| count 
```

### <a name="show-users-for-cpu-intensive-queries"></a>Anzeigen von Benutzern für CPU-intensive Abfragen

In der folgenden **LAQueryLogs**-Tabellenabfrage werden die Benutzer aufgeführt, die die meisten CPU-intensiven Abfragen ausgeführt haben, basierend auf der verwendeten CPU und der Länge der Abfragezeit.

```kql
LAQueryLogs
|summarize arg_max(StatsCPUTimeMs, *) by AADClientId
| extend User = AADEmail, QueryRunTime = StatsCPUTimeMs
| project User, QueryRunTime, QueryText
| order by QueryRunTime desc
```

### <a name="show-users-who-ran-the-most-queries-in-the-past-week"></a>Anzeigen von Benutzern, die die meisten Abfragen in der letzten Woche ausgeführt haben

In der folgenden **LAQueryLogs**-Tabellenabfrage werden die Benutzer aufgeführt, die die meisten Abfragen in der letzten Woche ausgeführt haben.

```kql
LAQueryLogs
| where TimeGenerated > ago(7d)
| summarize events_count=count() by AADEmail
| extend UserPrincipalName = AADEmail, Queries = events_count
| join kind= leftouter (
    SigninLogs)
    on UserPrincipalName
| project UserDisplayName, UserPrincipalName, Queries
| summarize arg_max(Queries, *) by UserPrincipalName
| sort by Queries desc
```

## <a name="configuring-alerts-for-microsoft-sentinel-activities"></a>Konfigurieren von Warnungen für Microsoft Sentinel-Aktivitäten

Möglicherweise möchten Sie Microsoft Sentinel-Überwachungsressourcen verwenden, um proaktive Warnungen zu erstellen.

Wenn Sie z. B. sensible Tabellen in Ihrem Microsoft Sentinel-Arbeitsbereich haben, können Sie die folgende Abfrage verwenden, damit Sie bei jedem Abfragen dieser Tabellen benachrichtigt werden:

```kql
LAQueryLogs
| where QueryText contains "[Name of sensitive table]"
| where TimeGenerated > ago(1d)
| extend User = AADEmail, Query = QueryText
| project User, Query
```

## <a name="monitor-microsoft-sentinel-with-workbooks-rules-and-playbooks"></a>Überwachen von Microsoft Sentinel mit Arbeitsmappen, Regeln und Playbooks

Verwenden Sie die eigenen Features von Microsoft Sentinel, um Ereignisse und Aktionen zu überwachen, die in Microsoft Sentinel auftreten.

- **Überwachen mit Arbeitsmappen**. Die folgenden Arbeitsmappen wurden zum Überwachen der Arbeitsbereichsaktivität erstellt:

  - **Arbeitsbereichsüberwachung**. Enthält Informationen darüber, welche Benutzer in der Umgebung Aktionen ausführen, welche Aktionen sie ausgeführt haben und mehr.
  - **Analyseeffizienz**. Bietet Erkenntnisse darüber, welche Analyseregeln verwendet, welche MITRE-Taktiken am häufigsten behandelt und welche Incidents aus den Regeln generiert werden.
  - **Effizienz von Sicherheitsvorgängen**. Zeigt Metriken zur Leistung des SOC-Teams, geöffneten Incidents, geschlossenen Incidents und mehr an. Diese Arbeitsmappe kann verwendet werden, um die Leistung des Teams zu zeigen und alle möglicherweise fehlenden, Aufmerksamkeit erfordernden Bereiche hervorzuheben.
  - **Überwachung der Datenerfassungsintegrität**. Hilft bei der Suche nach angehaltenen oder beendeten Erfassungen.

  Weitere Informationen finden Sie unter [Häufig verwendete Microsoft Sentinel-Arbeitsmappen](top-workbooks.md).

- **Achten auf Erfassungsverzögerungen**.  Wenn Sie Bedenken wegen der Erfassungsverzögerung haben, [legen Sie eine Variable in einer Analyseregel fest](ingestion-delay.md), um die Verzögerung darzustellen.

  Die folgende Analyseregel kann beispielsweise dazu beitragen, sicherzustellen, dass die Ergebnisse keine Duplikate enthalten, und dass Protokolle beim Ausführen der Regeln nicht ignoriert werden:

  ```kusto
  let ingestion_delay= 2min;let rule_look_back = 5min;CommonSecurityLog| where TimeGenerated >= ago(ingestion_delay + rule_look_back)| where ingestion_time() > (rule_look_back)
  - Calculating ingestion delay
    CommonSecurityLog| extend delay = ingestion_time() - TimeGenerated| summarize percentiles(delay,95,99) by DeviceVendor, DeviceProduct
  ```

  Weitere Informationen finden Sie unter [Automatisierung der Vorfallbehandlung in Microsoft Sentinel mit Automatisierungsregeln](automate-incident-handling-with-automation-rules.md).

- **Überwachen Sie die Integrität des Datenconnectors** mithilfe des Playbooks [Connector Health Push Notification Solution](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Send-ConnectorHealthStatus) (Pushbenachrichtigungslösung zur Connectorintegrität), um auf eine angehaltene oder beendete Erfassung zu achten und Benachrichtigungen zu senden, wenn ein Connector das Erfassen von Daten beendet hat oder Computer die Berichterstellung beendet haben.

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie in Microsoft Sentinel die Arbeitsmappe **Arbeitsbereichsüberwachung**, um die Aktivitäten in Ihrer SOC-Umgebung zu überwachen.

Weitere Informationen finden Sie unter [Visualisieren und Überwachen Ihrer Daten](monitor-your-data.md).
