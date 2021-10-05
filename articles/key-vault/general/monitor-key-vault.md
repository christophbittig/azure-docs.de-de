---
title: Überwachen von Azure Key Vault
description: Beginnen Sie hier, um zu erfahren, wie Sie Azure Key Vault überwachen können.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 09/21/2021
ms.author: mbaldwin
ms.custom: subject-monitoring
ms.openlocfilehash: 753dbf0bdaa1e97a04422b567dcbc199717bbd71
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207303"
---
# <a name="monitoring-azure-key-vault"></a>Überwachen von Azure Key Vault

Wenn Sie über unternehmenskritische Anwendungen und Geschäftsprozesse verfügen, die auf Azure-Ressourcen beruhen, sollten Sie Verfügbarkeit, Leistung und Betrieb dieser Ressourcen überwachen. Für Azure Key Vault ist es wichtig, Ihren Dienst zu überwachen, wenn Sie mit der Skalierung beginnen, da die Anzahl der an Ihren Schlüsseltresor gesendeten Anforderungen steigt. Dies kann die Latenzzeit Ihrer Anforderungen erhöhen und in extremen Fällen zu einer Drosselung Ihrer Anforderungen führen – was sich auf die Leistung Ihres Diensts auswirkt.

In diesem Artikel werden die von Key Vault generierten Überwachungsdaten erläutert. Key Vault verwendet [Azure Monitor](/azure/azure-monitor/overview). Wenn Sie nicht mit den Features von Azure Monitor vertraut sind, die Bestandteil aller Azure-Dienste sind, für die Azure Monitor verwendet wird, lesen Sie den Artikel [Überwachen von Azure-Ressourcen mit Azure Monitor](/azure/azure-monitor/essentials/monitor-azure-resource).

## <a name="monitoring-overview-page-in-azure-portal"></a>Übersichtsseite zur Überwachung im Azure-Portal

Die Seite **Übersicht** im Azure-Portal für den jeweiligen Schlüsseltresor enthält die folgenden Metriken auf der Registerkarte „Überwachung“:

- Anforderungen gesamt
- Durchschnittliche Latenz
- Erfolgsquote

Sie können „Zusätzliche Metriken“ (oder die Registerkarte „Metriken“ auf der linken Randleiste unter „Überwachung“) auswählen, um auch die folgenden Metriken anzuzeigen:

- Wartezeit für Dienst-API gesamt
- Tresorverfügbarkeit gesamt
- Tresorauslastung gesamt
- Dienst-API-Treffer gesamt
- Dienst-API-Ergebnisse gesamt

## <a name="key-vault-insights"></a>Key Vault-Erkenntnisse

Für einige Dienste in Azure enthält das Azure-Portal ein spezielles, vorgefertigtes Überwachungsdashboard, das als Ausgangspunkt für die Überwachung Ihres Diensts dient. Diese speziellen Dashboards werden als „Insights“ (Erkenntnisse) bezeichnet.

Key Vault Insights ermöglicht eine umfassende Überwachung Ihrer Schlüsseltresore. Zu diesem Zweck wird eine einheitliche Darstellung der Anforderungen, Leistung, Fehler und Wartezeit im Zusammenhang mit Key Vault bereitgestellt. Ausführliche Informationen finden Sie unter [Überwachen Ihres Schlüsseltresordiensts mit Key Vault-Erkenntnissen](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="monitoring-data"></a>Überwachungsdaten

Key Vault erfasst dieselben Arten von Überwachungsdaten wie andere Azure-Ressourcen, die unter [Überwachen von Daten aus Azure-Ressourcen](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data-from-Azure-resources) beschrieben werden.

Ausführliche Informationen zu den von Key Vault erstellten Metriken und Protokollmetriken finden Sie in der [Referenz für *Key Vault*-Überwachungsdaten](monitor-key-vault-reference.md).

## <a name="collection-and-routing"></a>Sammlung und Routing

Plattformmetriken und das Aktivitätsprotokoll werden automatisch erfasst und gespeichert, können jedoch mithilfe einer Diagnoseeinstellung an andere Speicherorte weitergeleitet werden.  

Ressourcenprotokolle werden erst erfasst und gespeichert, sobald Sie eine Diagnoseeinstellung erstellt und an einen oder mehrere Standorte weitergeleitet haben.

Ausführliche Informationen zum Erstellen einer Diagnoseeinstellung über das Azure-Portal, die Befehlszeilenschnittstelle oder PowerShell finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Plattformprotokollen und Metriken in Azure](../../azure-monitor/platform/diagnostic-settings.md). Wenn Sie eine Diagnoseeinstellung erstellen, legen Sie fest, welche Kategorien von Protokollen gesammelt werden sollen. Die Kategorien für *Key Vault* werden in der [Referenz für Key Vault-Überwachungsdaten](monitor-key-vault-reference.md#resource-logs) aufgeführt.

Informationen zum Erstellen einer Diagnoseeinstellung für Ihren Schlüsseltresor finden Sie unter [Aktivieren von Key Vault Protokollierung](howto-logging.md).  In den folgenden Abschnitten werden die Metriken und Protokolle behandelt, die Sie erfassen können.

## <a name="analyzing-metrics"></a>Analysieren von Metriken

Sie können im Metrik-Explorer Metriken für Key Vault mit Metriken aus anderen Azure-Diensten analysieren, indem Sie im Menü **Azure Monitor** die Option **Metriken** öffnen. Ausführliche Informationen zur Verwendung dieses Tools finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](/azure/azure-monitor/platform/metrics-getting-started).

Eine Liste mit den für Key Vault erfassten Plattformmetriken finden Sie in der [Referenz der Metriken zur Überwachung von Key Vault-Daten](monitor-key-vault-reference.md#metrics).  

## <a name="analyzing-logs"></a>Analysieren von Protokollen

Daten in Azure Monitor-Protokollen werden in Tabellen gespeichert, wobei jede Tabelle ihren eigenen Satz an eindeutigen Eigenschaften hat.  

Alle Ressourcenprotokolle in Azure Monitor enthalten dieselben Felder, gefolgt von dienstspezifischen Feldern. Das allgemeine Schema wird unter [Azure Monitor-Ressourcenprotokollschema](../../azure-monitor/platform/diagnostic-logs-schema.md#top-level-resource-logs-schema) beschrieben. 

Das [Aktivitätsprotokoll](../../azure-monitor/platform/activity-log.md) ist ein Plattformprotokolltyp in Azure, das Erkenntnisse zu Ereignissen auf Abonnementebene liefert. Sie können es unabhängig anzeigen oder an Azure Monitor-Protokolle weiterleiten, in denen Sie mithilfe von Log Analytics viel komplexere Abfragen durchführen können.  

Eine Liste der Typen von Ressourcenprotokollen, die für Key Vault erfasst werden, finden Sie in der [Referenz zur Überwachung von Key Vault-Daten](monitor-key-vault-reference.md#resource-logs).  

Eine Liste der Tabellen, die von Azure Monitor-Protokollen verwendet und von Log Analytics abgefragt werden können, finden Sie in der [Referenz zur Überwachung von Key Vault-Daten](monitor-key-vault-reference.md#azure-monitor-logs-tables).  

### <a name="sample-kusto-queries"></a>Kusto-Beispielabfragen

> [!IMPORTANT]
> Wenn Sie im Menü von Key Vault **Protokolle** auswählen, wird Log Analytics geöffnet, wobei der Abfragebereich auf die aktuelle Key Vault-Instanz festgelegt ist. Dies bedeutet, dass Protokollabfragen nur Daten aus dieser Ressource umfassen. Wenn Sie eine Abfrage ausführen möchten, die Daten aus anderen Schlüsseltresoren oder Daten aus anderen Azure-Diensten enthält, wählen Sie im Menü **Azure Monitor** die Option **Protokolle** aus. Ausführliche Informationen finden Sie unter [Protokollabfragebereich und Zeitbereich in Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/).

Hier sind einige Abfragen, die Sie in die Suchleiste **Protokollsuche** eingeben können, um die Überwachung Ihrer Key Vault-Ressourcen zu vereinfachen. Diese Abfragen arbeiten mit der [neuen Sprache](/azure-monitor/logs/log-query-overview).

* Gibt es langsame Anforderungen?

    ```Kusto
    // List of KeyVault requests that took longer than 1sec. 
    // To create an alert for this query, click '+ New alert rule'
    let threshold=1000; // let operator defines a constant that can be further used in the query

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where DurationMs > threshold
    | summarize count() by OperationName, _ResourceId
    ```

* Gibt es Fehler?

    ```Kusto
    // Count of failed KeyVault requests by status code. 
    // To create an alert for this query, click '+ New alert rule'

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where httpStatusCode_d >= 300 and not(OperationName == "Authentication" and httpStatusCode_d == 401)
    | summarize count() by requestUri_s, ResultSignature, _ResourceId
    // ResultSignature contains HTTP status, e.g. "OK" or "Forbidden"
    // httpStatusCode_d contains HTTP status code returned
    ```

* Eingabefehler bei Deserialisierung

    ```Kusto
    // Shows errors caused due to malformed events that could not be deserialized by the job. 
    // To create an alert for this query, click '+ New alert rule'

    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.KEYVAULT" and parse_json(properties_s).DataErrorType in ("InputDeserializerError.InvalidData", "InputDeserializerError.TypeConversionError", "InputDeserializerError.MissingColumns", "InputDeserializerError.InvalidHeader", "InputDeserializerError.InvalidCompressionType")
    | project TimeGenerated, Resource, Region_s, OperationName, properties_s, Level, _ResourceId
    ```

* Wie aktiv war dieser KeyVault?

    ```Kusto
    //  
    // Line chart showing trend of KeyVault requests volume, per operation over time. 
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | summarize count() by bin(TimeGenerated, 1h), OperationName // Aggregate by hour
    | render timechart

    ```

* Wer ruft diesen KeyVault auf? 

    ```Kusto
    // List of callers identified by their IP address with their request count.  
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT"
    | summarize count() by CallerIPAddress
    ```

* Wie schnell verarbeitet dieser KeyVault Anforderungen? 

    ```Kusto
    // Line chart showing trend of request duration over time using different aggregations. 
 
    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | summarize avg(DurationMs) by requestUri_s, bin(TimeGenerated, 1h) // requestUri_s contains the URI of the request
    | render timechart
    ```

* Welche Änderungen sind im letzten Monat aufgetreten? 

    ```Kusto
    // Lists all update and patch requests from the last 30 days. 
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where TimeGenerated > ago(30d) // Time range specified in the query. Overrides time picker in portal.
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where OperationName == "VaultPut" or OperationName = "VaultPatch"
    | sort by TimeGenerated desc
    ```


## <a name="alerts"></a>Alerts

Azure Monitor-Warnungen informieren Sie proaktiv, wenn wichtige Bedingungen in Ihren Überwachungsdaten gefunden werden. Sie ermöglichen Ihnen, Probleme in Ihrem System zu identifizieren und zu beheben, bevor Ihre Kunden sie bemerken. Sie können Warnungen für [Metriken](../../azure-monitor/platform/alerts-metric-overview.md), [Protokolle](../../azure-monitor/platform/alerts-unified-log.md) und das [Aktivitätsprotokoll](../../azure-monitor/platform/activity-log-alerts.md) festlegen. Verschiedene Arten von Warnungen haben jeweils ihre Vor- und Nachteile.

Wenn Sie eine Anwendung erstellen oder ausführen, die Azure Key Vault verwendet, bietet [Azure Monitor Application Insights](/azure-monitor/overview#application-insights.md) möglicherweise zusätzliche Warnungen.

Hier sind einige allgemeine und empfohlene Warnungsregeln für Azure Key Vault.

- Key Vault-Verfügbarkeit fällt unter 100 % (statischer Schwellenwert)
- Key Vault-Wartezeit übersteigt 500 ms (statischer Schwellenwert)
- „Tresorauslastung insgesamt“ übersteigt 75 % (statischer Schwellenwert)
- „Tresorauslastung insgesamt“ überschreitet den Durchschnitt (dynamischer Schwellenwert)
- „Fehlercodes gesamt“ liegt über dem Durchschnitt (dynamischer Schwellenwert)

Weitere Informationen finden Sie unter [Warnungen für Azure Key Vault](alert.md).

## <a name="next-steps"></a>Nächste Schritte

Herzlichen Glückwunsch! Sie überwachen nun Azure Key Vault. Wenn Sie auch Warnungen einrichten möchten, finden Sie weitere Informationen unter [Warnungen für Azure Key Vault](alert.md).

- Eine Referenz zu den Metriken, Protokollen und anderen wichtigen Werten, die von Key Vault erstellt werden, finden Sie in der [Referenz zur Überwachung von Azure Key Vault-Daten](monitor-key-vault-reference.md).
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource).
