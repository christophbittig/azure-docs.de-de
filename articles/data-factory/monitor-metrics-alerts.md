---
title: Data Factory-Metriken und -Warnungen
description: Hier erhalten Sie Informationen über die verfügbaren Metriken für die Überwachung von Azure Data Factory.
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 3b8351c94fb7c9ace6dbf0e31a50f2ed3d8789a3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666626"
---
# <a name="data-factory-metrics-and-alerts"></a>Data Factory-Metriken und -Warnungen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory stellt die folgenden Metriken und Warnungen zur Verfügung, um die Überwachung des Diensts zu ermöglichen.

## <a name="data-factory-metrics"></a>Data Factory-Metriken

Mit Monitor erhalten Sie Einblicke in die Leistung und Integrität ihrer Azure-Workloads. Metriken sind die wichtigsten Monitor-Datentypen. Sie werden auch als Leistungsindikatoren bezeichnet. Metriken werden von den meisten Azure-Ressourcen ausgegeben. Azure Monitor bietet Ihnen verschiedene Möglichkeiten, diese Metriken für die Überwachung und Problembehandlung zu konfigurieren und zu nutzen.

Hier sind einige Metriken aufgeführt, die von Azure Data Factory Version 2 ausgegeben werden:

| **Metrik**                           | **Anzeigename der Metrik**                  | **Einheit** | **Aggregationstyp** | **Beschreibung**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | Metriken zu abgebrochenen Aktivitätsausführungen           | Anzahl    | Gesamt                | Die Gesamtanzahl von Aktivitätsausführungen, die innerhalb eines Minutenfensters abgebrochen wurden. |
| ActivityFailedRuns                   | Metriken zu fehlerhaften Aktivitätsausführungen             | Anzahl    | Gesamt                | Die Gesamtanzahl der fehlgeschlagenen Aktivitätsausführungen innerhalb eines Minutenfensters. |
| ActivitySucceededRuns                | Metriken zu erfolgreichen Aktivitätsausführungen          | Anzahl    | Gesamt                | Die Gesamtanzahl der erfolgreichen Aktivitätsausführungen innerhalb eines Minutenfensters. |
| PipelineCancelledRuns                 | Metriken zu abgebrochenen Pipelineausführungen           | Anzahl    | Gesamt                | Die Gesamtanzahl von Pipelineausführungen, die innerhalb eines Minutenfensters abgebrochen wurden. |
| PipelineFailedRuns                   | Metriken zu fehlerhaften Pipelineausführungen             | Anzahl    | Gesamt                | Die Gesamtanzahl der fehlgeschlagenen Pipelineausführungen innerhalb eines Minutenfensters. |
| PipelineSucceededRuns                | Metriken zu erfolgreichen Pipelineausführungen          | Anzahl    | Gesamt                | Die Gesamtanzahl der erfolgreichen Pipelineausführungen innerhalb eines Minutenfensters. |
| TriggerCancelledRuns                  | Metriken zu abgebrochenen Triggerausführungen            | Anzahl    | Gesamt                | Die Gesamtanzahl von Triggerausführungen, die innerhalb eines Minutenfensters abgebrochen wurden. |
| TriggerFailedRuns                    | Metriken zu fehlerhaften Triggerausführungen              | Anzahl    | Gesamt                | Die Gesamtanzahl der fehlgeschlagenen Triggerausführungen innerhalb eines Minutenfensters. |
| TriggerSucceededRuns                 | Metriken zu erfolgreichen Triggerausführungen           | Anzahl    | Gesamt                | Die Gesamtanzahl der erfolgreichen Triggerausführungen innerhalb eines Minutenfensters. |
| SSISIntegrationRuntimeStartCancelled  | Metriken zu abgebrochenen SSIS-Integration Runtime-Startvorgängen           | Anzahl    | Gesamt                | Die Gesamtanzahl von SSIS-Integration Runtime-Startvorgängen, die innerhalb eines Fensters von einer Minute abgebrochen wurden. |
| SSISIntegrationRuntimeStartFailed    | Metriken zu fehlerhaften SSIS-Integration Runtime-Startvorgängen             | Anzahl    | Gesamt                | Die Gesamtanzahl von SSIS-Integration Runtime-Startvorgängen, die innerhalb eines Fensters von einer Minute zu einem Fehler führten. |
| SSISIntegrationRuntimeStartSucceeded | Metriken zu erfolgreichen SSIS-Integration Runtime-Startvorgängen          | Anzahl    | Gesamt                | Die Gesamtanzahl von SSIS-Integration Runtime-Startvorgängen, die innerhalb eines Fensters von einer Minute erfolgreich abgeschlossen wurden. |
| SSISIntegrationRuntimeStopStuck      | Metriken zu unterbrochenen SSIS-Integration Runtime-Beendigungsvorgängen               | Anzahl    | Gesamt                | Die Gesamtanzahl von SSIS-Integration Runtime-Beendigungsvorgängen, die innerhalb eines Fensters von einer Minute unterbrochen wurden. |
| SSISIntegrationRuntimeStopSucceeded  | Metriken zu erfolgreichen SSIS-Integration Runtime-Beendigungsvorgängen           | Anzahl    | Gesamt                | Die Gesamtanzahl von SSIS-Integration Runtime-Beendigungsvorgängen, die innerhalb eines Fensters von einer Minute erfolgreich abgeschlossen wurden. |
| SSISPackageExecutionCancelled         | Metriken zu abgebrochenen SSIS-Paketausführungen  | Anzahl    | Gesamt                | Die Gesamtanzahl von SSIS-Paketausführungen, die innerhalb eines Minutenfensters abgebrochen wurden. |
| SSISPackageExecutionFailed           | Metriken zu fehlgeschlagenen SSIS-Paketausführungen    | Anzahl    | Gesamt                | Die Gesamtanzahl von SSIS-Paketausführungen, die innerhalb eines Minutenfensters fehlgeschlagen sind. |
| SSISPackageExecutionSucceeded        | Metriken zu erfolgreichen SSIS-Paketausführungen | Anzahl    | Gesamt                | Die Gesamtanzahl von SSIS-Paketausführungen, die innerhalb eines Minutenfensters erfolgreich waren. |
| PipelineElapsedTimeRuns | Metriken zur für Pipelineausführungen verstrichenen Zeit | Anzahl | Gesamt | Gibt an, wie oft eine Pipeline innerhalb eines Minutenfensters länger als die benutzerdefinierte erwartete Dauer ausgeführt wird. [(weitere Informationen)](tutorial-operationalize-pipelines.md) |

Folgen Sie den Anweisungen unter [Azure Monitor-Datenplattform](../azure-monitor/data-platform.md), um auf die Metriken zuzugreifen.

> [!NOTE]
> Mit Ausnahme von _PipelineElapsedTimeRuns_ werden nur Ereignisse von abgeschlossenen, ausgelösten Aktivitäts- und Pipelineausführungen ausgegeben. Aktive Ausführungen und Debugausführungen werden **nicht** ausgegeben. Andererseits werden unabhängig von den Aufrufmethoden Ereignisse für **alle** SSIS-Paketausführungen ausgegeben, einschließlich abgeschlossener und in Bearbeitung befindlicher. Beispielsweise können Sie Paketausführungen in den für Azure aktivierten SQL Server Data Tools (SSDT), über T-SQL für SSMS, SQL Server-Agent oder andere festgelegte Tools und als ausgelöste oder Debugausführungen von SSIS-Paketausführungsaktivitäten in ADF-Pipelines aufrufen.

## <a name="data-factory-alerts"></a>Data Factory-Warnungen

Melden Sie sich beim Azure-Portal an, und wählen Sie **Überwachen** > **Warnungen** aus, um Warnungen zu erstellen.

:::image type="content" source="media/monitor-using-azure-monitor/alerts_image3.png" alt-text="Warnungen im Portalmenü":::

### <a name="create-alerts"></a>Erstellen von Warnungen

1. Wählen Sie **+ Neue Warnungsregel** aus, um eine neue Warnung zu erstellen.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image4.png" alt-text="Neue Warnungsregel":::

1. Definieren Sie die Warnungsbedingung.

    > [!NOTE]
    > Achten Sie darauf, dass in der Dropdownliste **Nach Ressourcentyp filtern** die Option **Alle** ausgewählt ist.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image5.png" alt-text="&quot;Warnungsbedingung definieren&quot; > &quot;Ziel auswählen&quot; öffnet den Bereich &quot;Ressource auswählen&quot; ":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image6.png" alt-text="&quot;Warnungsbedingung definieren&quot; > &quot;Kriterien hinzufügen&quot; öffnet den Bereich &quot;Signallogik konfigurieren&quot; ":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image7.png" alt-text="Bereich &quot;Configure signal type&quot; (Signaltyp konfigurieren)":::

1. Definieren Sie die Warnungsdetails.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image8.png" alt-text="Warnungsdetails":::

1. Definieren Sie die Aktionsgruppe.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image9.png" alt-text="Erstellen einer Regel, mit hervorgehobener Option &quot;Neue Aktionsgruppe&quot; ":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image10.png" alt-text="Erstellen einer neuen Aktionsgruppe":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image11.png" alt-text="Konfigurieren von E-Mail, SMS, Push und Voice":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image12.png" alt-text="Definieren einer Aktionsgruppe":::

## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren von Diagnoseeinstellungen und Arbeitsbereich](monitor-configure-diagnostics.md)
