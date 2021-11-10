---
title: Überwachen von SSIS-Vorgängen mit Azure Monitor
description: Hier erfahren Sie, wie Sie mit Azure Monitor SSIS-Vorgänge in Azure Data Factory überwachen.
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 91e291b27301cee8b7ec8f65e248e09c4f79799c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842737"
---
# <a name="monitor-ssis-operations-with-azure-monitor"></a>Überwachen von SSIS-Vorgängen mit Azure Monitor

Um Ihre SSIS-Workloads per Lift & Shift zu verschieben, können Sie [die SSIS IR in ADF bereitstellen](./tutorial-deploy-ssis-packages-azure.md) und somit Folgendes unterstützen:

- Ausführen von Paketen, die im SSIS-Katalog (SSISDB) bereitgestellt werden, wobei zum Hosten ein Azure SQL-Datenbank-Server/eine verwaltete Instanz verwendet wird (Projektbereitstellungsmodell)
- Ausführen von Paketen, die im Dateisystem, in Azure Files oder in SQL Server-Datenbank (MSDB) bereitgestellt werden, wobei zum Hosten Azure SQL Managed Instance verwendet wird (Paketbereitstellungsmodell)

Nach der Bereitstellung können Sie den [SSIS IR-Betriebsstatus mit Azure PowerShell oder im **Monitor**-Hub des ADF-Portals überprüfen](./monitor-integration-runtime.md#azure-ssis-integration-runtime). Beim Projektbereitstellungsmodell werden Protokolle für SSIS-Paketausführungen in internen SSISDB-Tabellen oder -Sichten gespeichert, damit sie mit geeigneten Tools wie SSMS abgefragt, analysiert und visuell dargestellt werden können. Beim Paketbereitstellungsmodell können Protokolle für SSIS-Paketausführungen im Dateisystem oder in Azure Files als CSV-Dateien gespeichert werden, die noch mit anderen geeigneten Tools analysiert und verarbeitet werden müssen, bevor sie abgefragt, analysiert und visuell dargestellt werden können.

Mit der [Azure Monitor](../azure-monitor/data-platform.md)-Integration können nun alle Metriken und Protokolle, die bei SSIS IR-Vorgängen und SSIS-Paketausführungen generiert werden, über das Azure-Portal abgefragt, analysiert und visuell dargestellt werden. Darüber hinaus können Sie auch Warnungen für sie ausgeben.

## <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>Konfigurieren von Diagnoseeinstellungen und des Arbeitsbereichs für SSIS-Vorgänge

Um alle Metriken und Protokolle an Azure Monitor zu senden, die bei SSIS IR-Vorgängen und SSIS-Paketausführungen generiert werden, müssen Sie [Diagnoseeinstellungen und einen Arbeitsbereich für ADF konfigurieren](monitor-configure-diagnostics.md).

## <a name="ssis-operational-metrics"></a>Metriken zum SSIS-Betrieb

[Metriken](../azure-monitor/essentials/data-platform-metrics.md) zum SSIS-Betrieb sind Leistungsindikatoren oder numerische Werte, die den Status von SSIS IR-Start- und -Beendigungsvorgängen sowie SSIS-Paketausführungen zu einem bestimmten Zeitpunkt beschreiben. Sie sind Teil der [ADF-Metriken in Azure Monitor](monitor-metrics-alerts.md).

Beim Konfigurieren der Diagnoseeinstellungen und des Arbeitsbereichs für Ihre ADF-Instanz unter Azure Monitor können Sie das Kontrollkästchen _AllMetrics_ aktivieren, um die Metriken zum SSIS-Betrieb für die [interaktive Analyse per Azure-Metrik-Explorer](../azure-monitor/essentials/metrics-getting-started.md), die [Darstellung in Azure-Dashboards](../azure-monitor/app/tutorial-app-dashboards.md) und [Benachrichtigungen nahezu in Echtzeit](../azure-monitor/alerts/alerts-metric.md) verfügbar zu machen.

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="Benennen Ihrer Einstellungen und Auswählen eines Log Analytics-Arbeitsbereichs":::

## <a name="ssis-operational-alerts"></a>Warnungen zum SSIS-Betrieb

[Wählen Sie die Seite **Warnungen und Metriken** im **Monitor**-Hub von ADF aus, und befolgen Sie die angegebene Schritt-für-Schritt-Anleitung](./monitor-visually.md#alerts), um über das ADF-Portal Warnungen zu SSIS-Betriebsmetriken auszulösen.

:::image type="content" source="media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png" alt-text="Auslösen von Warnungen zum SSIS-Betrieb über das ADF-Portal":::

[Wählen Sie die Seite **Warnungen** im **Azure Monitor**-Hub aus, und befolgen Sie die angegebene Schritt-für-Schritt-Anleitung](monitor-metrics-alerts.md), um über das Azure-Portal Warnungen zu SSIS-Betriebsmetriken auszulösen.

:::image type="content" source="media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png" alt-text="Auslösen von Warnungen zum SSIS-Betrieb über das Azure-Portal":::

## <a name="ssis-operational-logs"></a>Protokolle zum SSIS-Betrieb

[Protokolle](../azure-monitor/logs/data-platform-logs.md) zum SSIS-Betrieb sind Ereignisse, die bei SSIS IR-Vorgängen und SSIS-Paketausführungen generiert werden. Sie enthalten genügend Kontext zu den identifizierten Problemen und sind für die Grundursachenanalyse nützlich. 

Beim Konfigurieren der Diagnoseeinstellungen und des Arbeitsbereichs für Ihre ADF-Instanz in Azure Monitor können Sie die relevanten Protokolle zum SSIS-Betrieb auswählen und an die Log Analytics-Instanz senden, die auf Azure Data Explorer basiert. Dort werden diese Daten für die [Analyse mit einer leistungsstarken Abfragesprache](../azure-monitor/logs/log-query-overview.md), die [Darstellung in Azure-Dashboards](../azure-monitor/app/tutorial-app-dashboards.md) und [Benachrichtigungen nahezu in Echtzeit](../azure-monitor/alerts/alerts-log.md) verfügbar gemacht.

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="Benennen Ihrer Einstellungen und Auswählen eines Log Analytics-Arbeitsbereichs":::

Die Schemas und der Inhalt von Protokollen für SSIS-Paketausführungen in Azure Monitor und Log Analytics ähneln den Schemas der internen Tabellen und Sichten der SSISDB.

| Azure Monitor-Protokollkategorien          | Log Analytics-Tabellen                     | Interne SSISDB-Tabellen/-Sichten              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

Weitere Informationen zu den Attributen bzw. Eigenschaften von SSIS-Betriebsprotokollen finden Sie unter [Schema von Protokollen und Ereignissen](monitor-schema-logs-events.md).

Die ausgewählten Protokolle zur SSIS-Paketausführung werden unabhängig von den Aufrufmethoden immer an Log Analytics gesendet. Beispielsweise können Sie Paketausführungen in den für Azure aktivierten SSDT, über T-SQL für SSMS, SQL Server-Agent oder andere spezielle Tools und als ausgelöste oder Debugausführungen von SSIS-Paketausführungsaktivitäten in ADF-Pipelines aufrufen.

Beim Abfragen von Protokollen für SSIS IR-Vorgänge in Log Analytics können Sie die Eigenschaften **OperationName** und **ResultType** verwenden, die auf `Start/Stop/Maintenance/Heartbeat` bzw. `Started/InProgress/Succeeded/Failed/Healthy/Unhealthy` festgelegt sind.

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query.png" alt-text="Abfragen von Protokollen zum SSIS IR-Betrieb in Log Analytics":::

Um den Status des SSIS IR-Knotens abzufragen, können Sie die Eigenschaft **OperationName** auf `Heartbeat` festlegen. Jeder Knoten sendet normalerweise pro Minute einen `Heartbeat`-Datensatz an Log Analytics, wobei die Eigenschaft **ResultType** den Knotenstatus widerspiegelt. Dieser lautet `Healthy`, wenn der Knoten für Paketausführungen verfügbar ist und `Unhealthy`, wenn der Knoten nicht verfügbar ist. Wenn Ihre SSIS IR beispielsweise zwei verfügbare Knoten umfasst, werden Ihnen in einem beliebigen Zeitraum von 1 Minute immer zwei `Heartbeat`-Datensätze angezeigt, bei denen die Eigenschaft **ResultType** den Wert `Healthy` aufweist.

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query-3.png" alt-text="Abfragen von SSIS IR-Heartbeats in Log Analytics":::

Sie können die folgenden Muster abfragen, um eine Nichtverfügbarkeit Ihrer SSIS IR-Knoten zu erkennen:

* Es fehlen in zahlreichen 1-Minuten-Zeiträumen `Heartbeat`-Datensätze, während Ihre SSIS IR noch ausgeführt wird.
* Es sind in zahlreichen 1-Minuten-Zeiträumen `Heartbeat`-Datensätze mit dem Wert `Unhealthy` für die **ResultType**-Eigenschaft vorhanden, während Ihre SSIS IR noch ausgeführt wird.

Sie können die obigen Abfragen in [Warnungen](../azure-monitor/alerts/alerts-unified-log.md) umwandeln und zu Ihrer [SSIS IR-Überwachungsseite](monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) wechseln, um den Erhalt dieser Warnungen zu bestätigen.

Beim Abfragen von Protokollen zu SSIS-Paketausführungen in Logs Analytics können Sie diese mit den Eigenschaften **OperationId**/**ExecutionId**/**CorrelationId** verknüpfen. **OperationId**/**ExecutionId** sind für alle Vorgänge bzw. Ausführungen im Zusammenhang mit Paketen, die **nicht** in der SSISDB gespeichert sind oder über T-SQL aufgerufen werden, immer auf `1` festgelegt.

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query2.png" alt-text="Abfragen von Protokollen zu SSIS-Paketausführungen in Log Analytics":::

## <a name="next-steps"></a>Nächste Schritte

[Schema von Protokollen und Ereignissen](monitor-schema-logs-events.md)
