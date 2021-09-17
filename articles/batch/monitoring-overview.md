---
title: Überwachen von Azure Batch
description: Hier finden Sie Informationen zu Azure-Überwachungsdiensten, Metriken Diagnoseprotokollen und weiteren Überwachungsfeatures für Azure Batch.
ms.topic: how-to
ms.date: 08/23/2021
ms.openlocfilehash: b2b73261b07a7e5eb269c1f58a45f97d99133ac4
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768886"
---
# <a name="monitor-batch-solutions"></a>Überwachen von Batch-Lösungen

[Azure Monitor](../azure-monitor/overview.md) und der Batch-Dienst stellen verschiedene Dienste, Tools und APIs zur Überwachung von Batch-Lösungen bereit. Dieser Übersichtsartikel hilft Ihnen bei der Wahl eines geeigneten Überwachungskonzepts für Ihre Anforderungen.

## <a name="subscription-level-monitoring"></a>Überwachung auf Abonnementebene

Auf der Abonnementebene – die auch Batch-Konten einschließt – werden im [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md) operative Ereignisdaten in verschiedenen Kategorien erfasst.

Speziell für Batch-Konten werden Ereignisse im Zusammenhang mit der Kontoerstellung/-löschung und der Schlüsselverwaltung erfasst.

Sie können das Aktivitätsprotokoll im Azure-Portal anzeigen oder Ereignisse über die Azure CLI, PowerShell-Cmdlets oder die Azure Monitor-REST-API abfragen. Sie können das Aktivitätsprotokoll auch exportieren oder [Aktivitätsprotokollwarnungen](../azure-monitor/alerts/alerts-activity-log.md) konfigurieren.

## <a name="batch-account-level-monitoring"></a>Überwachung auf Batch-Kontoebene

Überwachen Sie die einzelnen Batch-Konten mithilfe der Features von [Azure Monitor](../azure-monitor/overview.md). Azure Monitor erfasst [Metriken](../azure-monitor/essentials/data-platform-metrics.md) und optional [Ressourcenprotokolle](../azure-monitor/essentials/resource-logs.md) für Ressourcen in einem Batch-Konto, etwa für Pools, Aufträge und Aufgaben. Erfassen und nutzen Sie diese Daten manuell oder programmgesteuert, um Aktivitäten in Ihrem Batch-Konto zu überwachen und Probleme zu diagnostizieren. Ausführliche Informationen finden Sie unter [Batch-Metriken, -Warnungen und -Protokolle für die Diagnoseauswertung und -überwachung](batch-diagnostics.md).

> [!NOTE]
> Metriken sind standardmäßig ohne zusätzliche Konfiguration in Ihrem Batch-Konto verfügbar und decken jeweils die letzten 30 Tage ab. Sie müssen eine Diagnoseeinstellung für ein Batch-Konto erstellen, um dessen Ressourcenprotokolle an einen Log Analytics-Arbeitsbereich zu senden. Hierbei fallen unter Umständen zusätzliche Kosten für das Speichern oder Verarbeiten von Ressourcenprotokolldaten an.

## <a name="batch-resource-monitoring"></a>Überwachung von Batch-Ressourcen

Verwenden Sie in Ihren Batch-Anwendungen die Batch-APIs, um den Status von Ressourcen wie Aufträgen, Aufgaben, Knoten und Pools zu überwachen oder abzufragen. Beispiel:

- [Monitor Batch solutions by counting tasks and nodes by state (Überwachen von Batch-Lösungen durch das Zählen von Tasks und Knoten nach Bundesstaat)](batch-get-resource-counts.md)
- [Erstellen von Abfragen zum effizienten Auflisten von Batch-Ressourcen](batch-efficient-list-queries.md)
- [Erstellen von Auftragsabhängigkeiten](batch-task-dependencies.md)
- Verwenden einer [Auftrags-Manager-Aufgabe](/rest/api/batchservice/job/add#jobmanagertask)
- Überwachen des [Auftragsstatus](/rest/api/batchservice/task/list#taskstate)
- Überwachen des [Knotenstatus](/rest/api/batchservice/computenode/list#computenodestate)
- Überwachen des [Poolstatus](/rest/api/batchservice/pool/get#poolstate)
- Überwachen der [Poolverwendung im Konto](/rest/api/batchservice/pool/listusagemetrics)
- Zählen der [Poolknoten nach Status](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="additional-monitoring-solutions"></a>Zusätzliche Überwachungslösungen

[Application Insights](../azure-monitor/app/app-insights-overview.md) ermöglicht eine programmgesteuerte Überwachung der Verfügbarkeit, Leistung und Verwendung Ihrer Batch-Aufträge und -Aufgaben. Mit Application Insights können Sie Leistungsindikatoren von Computeknoten (VMs) überwachen und benutzerdefinierte Informationen für die Aufgaben abrufen, die für sie ausgeführt werden.

Ein Beispiel finden Sie unter [Überwachen und Debuggen einer Azure Batch-.NET-Anwendung mit Application Insights](monitor-application-insights.md) und im dazugehörigen [Codebeispiel](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

> [!NOTE]
> Durch die Verwendung von Application Insights können zusätzliche Kosten entstehen. Weitere Details finden Sie in den [Preisinformationen](https://azure.microsoft.com/pricing/details/application-insights/).

Der [Batch Explorer](https://github.com/Azure/BatchExplorer) ist ein kostenloses eigenständiges Clienttool mit zahlreichen Features, das Sie beim Erstellen, Debuggen und Überwachen von Azure Batch-Anwendungen unterstützt. Ein Installationspaket für Mac, Linux oder Windows können Sie [hier](https://azure.github.io/BatchExplorer/) herunterladen. Verwenden Sie optional [Azure Batch Insights](https://github.com/Azure/batch-insights), um Systemstatistiken für Ihre Batch-Knoten, z. B. VM-Leistungsindikatoren, im Batch Explorer abzurufen.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Batch-APIs und Tools](batch-apis-tools.md), die für die Erstellung von Batch-Lösungen verfügbar sind.
- Weitere Informationen zur Diagnoseprotokollierung mit Batch finden Sie [hier](batch-diagnostics.md).
