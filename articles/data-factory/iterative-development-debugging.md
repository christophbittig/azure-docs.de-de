---
title: Iteratives Entwickeln und Debuggen
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie Data Factory- und Synapse Analytics-Pipelines iterativ über die Benutzeroberfläche des Diensts entwickeln und debuggen.
ms.date: 09/09/2021
ms.topic: conceptual
ms.service: data-factory
ms.subservice: authoring
ms.custom: synapse
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: kromerm
ms.author: makromer
ms.openlocfilehash: 343cdb521f3a984d7d80022940a75afbb7032b6e
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208065"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory-and-synapse-analytics-pipelines"></a>Iteratives Entwickeln und Debuggen mit Azure Data Factory- und Synapse Analytics-Pipelines
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory und Synapse Analytics unterstützen das iterative Entwickeln und Debuggen von Pipelines. Diese Features ermöglichen Ihnen, Ihre Änderungen zu testen, bevor Sie einen Pull Request erstellen oder sie im Dienst veröffentlichen. 

Das folgende Video enthält eine achtminütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="debugging-a-pipeline"></a>Debuggen einer Pipeline

Wenn Sie auf der Pipelinecanvas arbeiten, können Sie Ihre Aktivitäten mit der Funktion **Debuggen** testen. Beim Ausführen von Testläufen müssen Sie Ihre Änderungen nicht im Dienst veröffentlichen, bevor Sie **Debuggen** auswählen. Dieses Feature ist in Szenarien praktisch, in denen Sie sicherstellen möchten, dass die Änderungen wie erwartet funktionieren, bevor Sie den Workflow aktualisieren.

:::image type="content" source="media/iterative-development-debugging/iterative-development-1.png" alt-text="Funktion zum Debuggen auf der Pipelinecanvas":::

Während die Pipeline ausgeführt wird, können Sie die Ergebnisse der einzelnen Aktivitäten auf der Registerkarte **Ausgabe** der Pipelinecanvas sehen.

Zeigen Sie die Ergebnisse der Testläufe auf der Pipelinecanvas im Fenster **Ausgabe** an.

:::image type="content" source="media/iterative-development-debugging/iterative-development-2.png" alt-text="Ausgabefenster der Pipelinecanvas":::

Fügen Sie nach der erfolgreichen Ausführung eines Testlaufs weitere Aktivitäten zur Pipeline hinzu, und setzen Sie das iterative Debuggen fort. Sie können einen aktiven Testlauf auch über **Abbrechen** beenden.

> [!IMPORTANT]
> Durch Auswählen von **Debuggen** wird die Pipeline ausgeführt. Wenn die Pipeline beispielsweise die Kopieraktivität enthält, werden beim Testlauf Daten aus der Quelle in das Ziel kopiert. Daher wird empfohlen, beim Debuggen Testordner in Ihrer copy-Aktivität und Ihren anderen Aktivitäten zu verwenden. Wechseln Sie nach dem Debuggen der Pipeline zu den Ordnern, die Sie tatsächlich in normalen Vorgängen verwenden möchten.

### <a name="setting-breakpoints"></a>Setzen von Haltepunkten

Sie können mit dem Dienst eine Pipeline debuggen, bis Sie eine bestimmte Aktivität auf der Pipelinecanvas erreichen. Setzen Sie einen Haltepunkt für die Aktivität, bis zu dem der Test ausgeführt werden soll, und klicken Sie auf **Debuggen**. Der Dienst stellt sicher, dass die Testläufe nur bis zur Breakpointaktivität auf der Pipelinecanvas ausgeführt werden. Dieses Feature namens *Debug Until* (Debuggen bis) ist nützlich, wenn Sie nicht die gesamte Pipeline, sondern nur eine Teilmenge der Aktivitäten in der Pipeline testen möchten.

:::image type="content" source="media/iterative-development-debugging/iterative-development-3.png" alt-text="Breakpoints auf der Pipelinecanvas":::

Um einen Haltepunkt festzulegen, wählen Sie ein Element auf der Pipelinecanvas aus. Eine Option *Debug Until* wird als ein leerer roter Kreis in der oberen rechten Ecke des Elements angezeigt.

:::image type="content" source="media/iterative-development-debugging/iterative-development-4.png" alt-text="Vor dem Festlegen eines Haltepunkts für das ausgewählte Element":::

Nachdem Sie die Option *Debug Until* ausgewählt haben, ändert sich der leere Kreis in einen ausgefüllten roten Kreis, um anzugeben, dass der Breakpoint aktiviert ist.

:::image type="content" source="media/iterative-development-debugging/iterative-development-5.png" alt-text="Nach dem Festlegen eines Haltepunkts für das ausgewählte Element":::

## <a name="monitoring-debug-runs"></a>Überwachen von Debugausführungen

Bei einer Debugausführung für eine Pipeline werden die Ergebnisse im Fenster **Ausgabe** der Pipelinecanvas gezeigt. Die Registerkarte „Ausgabe“ enthält nur die jüngste Ausführung, die während der aktuellen Browsersitzung erfolgt ist. 

:::image type="content" source="media/iterative-development-debugging/iterative-development-2.png" alt-text="Ausgabefenster der Pipelinecanvas":::

Um eine Verlaufsansicht von Debugausführungen oder eine Liste aller aktiven Debugausführungen anzuzeigen, können Sie zur Oberfläche **Überwachung** wechseln. 

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
:::image type="content" source="media/iterative-development-debugging/view-debug-runs.png" alt-text="Auswählen des Symbols zum Anzeigen aktiver Debugausführungen":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)
:::image type="content" source="media/iterative-development-debugging/view-debug-runs-synapse.png" alt-text="Auswählen des Symbols zum Anzeigen aktiver Debugausführungen":::

---

> [!NOTE]
> Der Dienst speichert den Verlauf von Debugausführungen nur 15 Tage lang. 

## <a name="debugging-mapping-data-flows"></a>Debuggen von Zuordnungsdatenflüssen

Zuordnungsdatenflüsse ermöglichen Ihnen, codefreie Datentransformationslogik zu erstellen, die sich nach Maß ausführen lässt. Wenn Sie Ihre Logik entwickeln, können Sie eine Debugsitzung aktivieren, um mit Ihren Daten in einem aktiven Spark-Cluster interaktiv zu arbeiten. Weitere Informationen finden Sie unter [Debugmodus für den Zuordnungsdatenfluss](concepts-data-flow-debug-mode.md).

Auf der Oberfläche **Überwachung** können Sie aktive Debugsitzungen für den Datenfluss überwachen.

:::image type="content" source="media/iterative-development-debugging/view-dataflow-debug-sessions.png" alt-text="Anzeigen von Debugsitzungen für einen Datenfluss":::

Die Datenvorschau im Datenflussdesigner und das Pipelinedebuggen von Datenflüssen sollten am besten mit kleinen Datenstichproben funktionieren. Wenn Sie jedoch Ihre Logik in einer Pipeline oder einem Datenfluss anhand großer Datenmengen testen müssen, setzen Sie die Größe der Azure Integration Runtime herauf, die in der Debugsitzung verwendet wird, d. h. erhöhen Sie die Zahl der Kerne und erweitern Sie das minimale Compute für allgemeine Zwecke.
 
### <a name="debugging-a-pipeline-with-a-data-flow-activity"></a>Debuggen einer Pipeline mit einer Datenflussaktivität

Bei der Ausführung einer Debugpipeline in einem Datenfluss haben Sie für Compute die Wahl zwischen zwei Optionen. Sie können entweder einen vorhandenen Debugcluster verwenden oder einen neuen Just-in-Time-Cluster für Ihre Datenflüsse einrichten.

Bei Verwendung einer vorhandenen Debugsitzung wird die Uptime des Datenflusses erheblich verkürzt, da der Cluster bereits läuft. Dies wird jedoch bei komplexen oder parallelen Workloads nicht empfohlen, da bei gleichzeitiger Ausführung mehrerer Aufträge Fehler auftreten können.

Bei Verwenden der Aktivitätslaufzeit wird ein neuer Cluster mit den Einstellungen erstellt, die in der Integration Runtime jeder Datenflussaktivität angegeben sind. Dies ermöglicht die Isolierung jedes einzelnen Auftrags und sollte für komplexe Workloads oder Leistungstests verwendet werden. Außerdem können Sie die Gültigkeitsdauer (TTL) in der Azure IR steuern, damit die für das Debuggen verwendeten Clusterressourcen während dieses Zeitraums weiterhin für zusätzliche Auftragsanforderungen verfügbar sind.

> [!NOTE]
> Wenn Sie über eine Pipeline mit Datenflüssen verfügen, die parallel ausgeführt werden, oder mit Datenflüssen, die mit großen Datasets getestet werden müssen, wählen Sie „Use Activity Runtime“ (Aktivitätsruntime verwenden) aus, damit der Dienst die in der Datenflussaktivität ausgewählte Integration Runtime verwenden kann. Dies ermöglicht das Ausführen der Datenflüsse in mehreren Clustern und die Verarbeitung der parallelen Datenflussausführungen.

:::image type="content" source="media/iterative-development-debugging/iterative-development-dataflow.png" alt-text="Ausführen einer Pipeline mit einem Datenfluss":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Änderungen getestet haben, stufen Sie sie mithilfe von [Continuous Integration und Deployment](continuous-integration-delivery.md) in höhere Umgebungen hoch.
