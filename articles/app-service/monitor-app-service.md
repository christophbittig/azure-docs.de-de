---
title: Überwachen von App Service mit Azure Monitor
description: Hier erfahren Sie mehr zur Überwachung von App Service.
author: msangapu-msft
ms.author: msangapu
ms.topic: article
ms.service: app-service
ms.custom: subject-monitoring
ms.date: 04/16/2021
ms.openlocfilehash: d0bef29fc9e91d847046bc5d8157a70bd2fdfd9a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470982"
---
# <a name="monitoring-app-service"></a>Überwachen von App Service

Wenn Sie über unternehmenskritische Anwendungen und Geschäftsprozesse verfügen, die auf Azure-Ressourcen beruhen, sollten Sie Verfügbarkeit, Leistung und Betrieb dieser Ressourcen überwachen. In diesem Artikel werden die Überwachungsdaten beschrieben, die von App Service generiert und an [Azure Monitor](../azure-monitor/overview.md) gesendet werden. Sie können auch die [integrierte Diagnose zur Ressourcenüberwachung](troubleshoot-diagnostic-logs.md) verwenden, um das Debuggen einer App Service-App zu unterstützen. Wenn Sie nicht mit den Features von Azure Monitor vertraut sind, die Bestandteil aller Azure-Dienste sind, lesen Sie den Artikel [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).

## <a name="monitoring-data"></a>Überwachungsdaten 

App Service erfasst dieselben Arten von Überwachungsdaten wie andere Azure-Ressourcen, die unter [Überwachungsdaten von Azure-Ressourcen](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) beschrieben werden. 

Ausführliche Informationen zu App Service-Metriken und -Protokollen finden Sie in der [Referenz für *App Service*-Überwachungsdaten](monitor-app-service-reference.md).

App Service bietet außerdem integrierte Diagnosefunktionen zur Unterstützung beim Debuggen von Apps. Weitere Informationen zum Aktivieren der integrierten Protokolle finden Sie unter [Aktivieren der Diagnoseprotokollierung](troubleshoot-diagnostic-logs.md). Informationen zum Überwachen von App Service-Instanzen finden Sie unter [Überwachen von App Service-Instanzen mit der Integritätsprüfung](monitor-instances-health-check.md).

## <a name="collection-and-routing"></a>Sammlung und Routing

Plattformmetriken und das Aktivitätsprotokoll werden automatisch erfasst und gespeichert, können jedoch mithilfe einer Diagnoseeinstellung an andere Speicherorte weitergeleitet werden.  

Ressourcenprotokolle werden erst erfasst und gespeichert, nachdem Sie eine Diagnoseeinstellung erstellt und die Protokolle an mindestens einen Speicherort weitergeleitet haben.

Ausführliche Informationen zum Erstellen einer Diagnoseeinstellung über das Azure-Portal, die Befehlszeilenschnittstelle oder PowerShell finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Plattformprotokollen und Metriken in Azure](../azure-monitor/essentials/diagnostic-settings.md). Wenn Sie eine Diagnoseeinstellung erstellen, legen Sie fest, welche Kategorien von Protokollen gesammelt werden sollen. Die Kategorien für *App Service* sind in der [Referenz für App Service-Überwachungsdaten](monitor-app-service-reference.md#resource-logs) aufgeführt.

In den folgenden Abschnitten werden die Metriken und Protokolle behandelt, die Sie erfassen können.

## <a name="analyzing-metrics"></a>Analysieren von Metriken

Sie können im Metrik-Explorer Metriken für *App Service* mit Metriken aus anderen Azure-Diensten analysieren, indem Sie im Menü **Azure Monitor** die Option **Metriken** öffnen. Ausführliche Informationen zur Verwendung dieses Tools finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](../azure-monitor/essentials/metrics-getting-started.md). 

Eine Liste der für App Service erfassten Plattformmetriken finden Sie in der Referenz für App Service-Überwachungsdaten unter [Metriken](monitor-app-service-reference.md#metrics).  

Sie können zur Referenz auf eine Liste [aller in Azure Monitor unterstützter Ressourcenmetriken](../azure-monitor/essentials/metrics-supported.md) anzeigen.

## <a name="analyzing-logs"></a>Analysieren von Protokollen

Daten in Azure Monitor-Protokollen werden in Tabellen gespeichert, wobei jede Tabelle ihren eigenen Satz an eindeutigen Eigenschaften hat.  

Alle Ressourcenprotokolle in Azure Monitor enthalten dieselben Felder, gefolgt von dienstspezifischen Feldern. Das allgemeine Schema wird in [Azure Monitor-Ressourcenprotokollschema](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor) beschrieben.

Das [Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md) ist eine Art von Plattformprotokoll, das Erkenntnisse zu Ereignissen auf Abonnementebene liefert. Sie können es unabhängig anzeigen oder an Azure Monitor-Protokolle weiterleiten. Das Weiterleiten an Azure Monitor-Protokolle bietet den Vorteil, dass mithilfe von Log Analytics komplexe Abfragen ausgeführt werden können.

Eine Liste der für App Service erfassten Arten von Ressourcenprotokollen finden Sie in der [Referenz für App Service-Überwachungsdaten](monitor-app-service-reference.md#resource-logs).  

Eine Liste der abfragbaren Tabellen, die von Azure Monitor-Protokollen und Log Analytics verwendet werden, finden Sie in der [Referenz für App Service-Überwachungsdaten](monitor-app-service-reference.md#azure-monitor-logs-tables).  

### <a name="sample-kusto-queries"></a>Kusto-Beispielabfragen

> [!IMPORTANT]
> Wenn Sie im App Service-Menü **Protokolle** auswählen, wird Log Analytics geöffnet, wobei der Abfragebereich auf die aktuelle Ressource festgelegt ist. Dies bedeutet, dass Protokollabfragen nur Daten aus dieser Ressource umfassen. Wenn Sie eine Abfrage ausführen möchten, die Daten aus einer anderen [Ressource] oder Daten aus anderen Azure-Diensten enthält, wählen Sie im Menü **Azure Monitor** die Option **Protokolle** aus. Ausführliche Informationen finden Sie unter [Protokollabfragebereich und Zeitbereich in Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/).

Die folgende Beispielabfrage kann Sie beim Überwachen von App-Protokollen mithilfe von `AppServiceAppLogs` unterstützen:

```Kusto
AppServiceAppLogs 
| project CustomLevel, _ResourceId
| summarize count() by CustomLevel, _ResourceId
```

Die folgende Beispielabfrage unterstützt Sie beim Überwachen von HTTP-Protokollen über `AppServiceHTTPLogs`, wenn `HTTP response code` mindestens `500` lautet:

```Kusto
AppServiceHTTPLogs 
//| where ResourceId = "MyResourceId" // Uncomment to get results for a specific resource Id when querying over a group of Apps
| where ScStatus >= 500
| reduce by strcat(CsMethod, ':\\', CsUriStem)
```

Mit der folgenden Beispielabfrage können Sie HTTP 500-Fehler überwachen, indem Sie `AppServiceConsoleLogs` und `AppserviceHTTPLogs` verknüpfen:

```Kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;   
```

Weitere Beispielabfragen finden Sie unter [Azure Monitor-Abfragen für App Service](https://github.com/microsoft/AzureMonitorCommunity/tree/master/Azure%20Services/App%20Services/Queries).

## <a name="alerts"></a>Alerts

Azure Monitor-Warnungen informieren Sie proaktiv, wenn wichtige Bedingungen in Ihren Überwachungsdaten gefunden werden. Sie ermöglichen Ihnen, Probleme in Ihrem System zu identifizieren und zu beheben, bevor Ihre Kunden sie bemerken. Sie können Warnungen für [Metriken](../azure-monitor/alerts/alerts-metric-overview.md), [Protokolle](../azure-monitor/alerts/alerts-unified-log.md) und das [Aktivitätsprotokoll](../azure-monitor/alerts/activity-log-alerts.md) festlegen.

Wenn Sie eine Anwendung in App Service ausführen, bietet [Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) weitere Arten von Warnungen.

In der folgenden Tabelle sind gängige und empfohlene Warnungsregeln für App Service aufgeführt.

| Warnungstyp | Bedingung | Beispiele  |
|:---|:---|:---|
| Metrik | Durchschnittliche Verbindungen| Wenn die Anzahl der Verbindungen einen festgelegten Wert überschreitet|
| Metrik | HTTP 404| Wenn HTTP 404-Antworten einen festgelegten Wert überschreiten|
| Metrik | HTTP-Serverfehler| Wenn HTTP 5xx-Fehler einen festgelegten Wert überschreiten|
| Aktivitätsprotokoll | Erstellen oder Aktualisieren einer Web-App | Wenn eine App erstellt oder aktualisiert wird|
| Aktivitätsprotokoll | Löschen der Web-App | Wenn die App gelöscht wird|
| Aktivitätsprotokoll | Neustarten einer Web-App| Wenn die App neu gestartet wird|
| Aktivitätsprotokoll | Beenden von Web-App| Wenn die App beendet wird|

## <a name="next-steps"></a>Nächste Schritte

- Eine Referenz zu den Metriken, Protokollen und anderen wichtigen Werten, die von App Service erstellt werden, finden Sie in der [Referenz für App Service-Überwachungsdaten](monitor-app-service-reference.md).

- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
