---
title: Überwachen der Kopieraktivität
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie die Ausführung der Kopieraktivität in Azure Data Factory und Azure Synapse Analytics überwachen können.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 14607231fd68cf78f33a21abdb26dd68e2ef6871
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131062186"
---
# <a name="monitor-copy-activity"></a>Überwachen der Kopieraktivität

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Ausführung der Kopieraktivität in Azure Data Factory- und Synapse-Pipelines überwachen können. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.  Sie können auch Kopieraktivitäten, die mit dem [Kopierdatentool](copy-data-tool.md) erzeugt wurden, sowie [Löschaktivitäten](delete-activity.md) mit demselben Ansatz überwachen.

## <a name="monitor-visually"></a>Visuelle Überwachung

Nachdem Sie eine Pipeline erstellt und veröffentlicht haben, können Sie ihr einen Trigger zuordnen oder manuell eine Ad-hoc-Ausführung starten. Sie können Ihre gesamten Pipeline-Ausführungen nativ über die Benutzeroberfläche überwachen. Informieren Sie sich in [Visuelles Überwachen von Azure Data Factory](monitor-visually.md) zur Überwachung im Allgemeinen.

Um die Ausführung der Kopieraktivität zu überwachen, wechseln Sie zur **Data Factory Studio**- oder **Azure Synapse Studio**-Benutzeroberfläche für Ihre Dienstinstanz. Auf der Registerkarte **Überwachen** wird eine Liste der Pipelineausführungen angezeigt. Klicken Sie auf den Link **Pipelinename**, um auf die Liste von Aktivitätsausführungen in der Pipelineausführung zuzugreifen.

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="./media/copy-activity-overview/monitor-pipeline-run.png" alt-text="Überwachen der Pipelineausführung":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

:::image type="content" source="./media/copy-activity-overview/monitor-pipeline-run-synapse.png" alt-text="Überwachen der Pipelineausführung":::

---

Auf dieser Ebene können Sie Links zu Eingabe und Ausgabe der Kopieraktivität sowie Fehler (wenn die Ausführung der Kopieraktivität fehlschlägt) und außerdem Statistiken wie Dauer/Status anzeigen. Wenn Sie neben dem Namen der Kopieraktivität auf die Schaltfläche **Details** (Brille) klicken, erhalten Sie detaillierte Informationen zur Ausführung Ihrer Kopieraktivität. 

:::image type="content" source="./media/copy-activity-overview/monitor-copy-activity-run.png" alt-text="Überwachen der Ausführung der Kopieraktivität":::

In dieser grafischen Überwachungsansicht zeigt Ihnen der Dienst die Informationen zur Ausführung der Kopieraktivität – darunter die gelesene/geschriebene Datenmenge, die Anzahl der Dateien/Zeilen von Daten, die aus der Quelle in die Senke kopiert wurden, den Durchsatz, die für Ihr Kopierszenario angewendeten Konfigurationen, die Schritte, die die Kopieraktivität durchläuft, mit den entsprechenden Dauern und Details und mehr. Unter [dieser Tabelle](#monitor-programmatically) finden Sie jede mögliche Metrik und eine detaillierte Beschreibung dazu. 

In einigen Szenarien werden bei der Ausführung einer Kopieraktivität am Anfang der Überwachungsansicht der Kopieraktivität **Tipps zur Leistungsoptimierung** angezeigt, wie im Beispiel zu sehen ist. Die Tipps geben Aufschluss über den Engpass, der vom Dienst für den jeweiligen Kopiervorgang identifiziert wurde, und bieten Änderungsvorschläge zur Erhöhung des Kopierdurchsatzes. Informieren Sie sich ausführlicher über [Tipps zur automatischen Leistungsoptimierung](copy-activity-performance-troubleshooting.md#performance-tuning-tips).

Am Ende von **Ausführungsdetails und Dauern** werden die wichtigsten Schritte beschrieben, die Ihre Kopieraktivität durchläuft. Dies ist besonders nützlich für die Problembehandlung der Kopierleistung. Der Engpass Ihrer Kopierausführung ist die Ausführung mit der längsten Dauer. Informationen zu den einzelnen Phasen sowie ausführliche Anleitungen zur Problembehandlung finden Sie in [Troubleshoot copy activity performance](copy-activity-performance-troubleshooting.md) (Problembehandlung bei der Leistung der Kopieraktivität).

**Beispiel: Kopieren aus Amazon S3 in Azure Data Lake Storage Gen2**

:::image type="content" source="./media/copy-activity-overview/monitor-copy-activity-run-details.png" alt-text="Details zum Überwachen der Ausführung der Kopieraktivität":::

## <a name="monitor-programmatically"></a>Programmgesteuerte Überwachung

Ausführungsdetails und Leistungsmerkmale zur Kopieraktivität werden auch im Abschnitt **Ausführungsergebnis der Kopieraktivität** > **Ausgabe** zurückgegeben, mit der die Überwachungsansicht der Benutzeroberfläche gerendert wird. Im Folgenden eine vollständige Liste der Eigenschaften, die zurückgegeben werden können. Es werden nur die Eigenschaften angezeigt, die auf Ihr Kopierszenario anwendbar sind. Informationen zum programmgesteuerten Überwachen von Aktivitätsausführungen finden Sie unter [Programmgesteuertes Überwachen einer Azure Data Factory-Instanz](monitor-programmatically.md).

| Eigenschaftenname  | BESCHREIBUNG | Einheit in Ausgabe |
|:--- |:--- |:--- |
| dataRead | Die tatsächliche Menge der Daten, die aus der Quelle gelesen wurden. | Int64-Wert in Bytes |
| dataWritten | Die tatsächliche Menge der Daten, die in die Senke geschrieben/committet wurden. Die Größe kann sich von der `dataRead`-Größe unterscheiden, da sie sich darauf bezieht, wie die Daten in den jeweiligen Datenspeichern gespeichert werden. | Int64-Wert in Bytes |
| filesRead | Die Anzahl von Dateien, die aus der dateibasierten Quelle gelesen wurden. | Int64-Wert (ohne Einheit) |
| filesWritten | Die Anzahl von Dateien, die in die dateibasierte Senke geschrieben/committet wurden. | Int64-Wert (ohne Einheit) |
| filesSkipped | Die Anzahl von Dateien, die in der dateibasierten Quelle übersprungen wurden. | Int64-Wert (ohne Einheit) |
| dataConsistencyVerification | Details zur Überprüfung der Datenkonsistenz. Hier können Sie sehen, ob die Konsistenz Ihrer kopierten Daten zwischen Quell- und Zielspeicher überprüft wurde. Mehr dazu erfahren Sie in [diesem Artikel](copy-activity-data-consistency.md#monitoring). | Array |
| sourcePeakConnections | Die maximale Anzahl gleichzeitiger Verbindungen mit dem Quelldatenspeicher während der Ausführung der Kopieraktivität. | Int64-Wert (ohne Einheit) |
| sinkPeakConnections | Die maximale Anzahl gleichzeitiger Verbindungen mit dem Senkendatenspeicher während der Ausführung der Kopieraktivität. | Int64-Wert (ohne Einheit) |
| rowsRead | Anzahl der aus der Quelle gelesenen Datensätze. Diese Metrik wird nicht angewendet, wenn Dateien im aktuellen Zustand kopiert werden, ohne sie zu parsen, z. B. wenn die Quellen- und Senkendatasets den Binärformattyp aufweisen oder einen anderen Formattyp mit identischen Einstellungen. | Int64-Wert (ohne Einheit) |
| rowsCopied | Anzahl der in die Senke kopierten Datensätze. Diese Metrik wird nicht angewendet, wenn Dateien im aktuellen Zustand kopiert werden, ohne sie zu parsen, z. B. wenn die Quellen- und Senkendatasets den Binärformattyp aufweisen oder einen anderen Formattyp mit identischen Einstellungen.  | Int64-Wert (ohne Einheit) |
| rowsSkipped | Die Anzahl der übersprungenen, nicht kompatiblen Zeilen. Nicht kompatible Zeilen können übersprungen werden, indem Sie `enableSkipIncompatibleRow` auf „true“ festlegen. | Int64-Wert (ohne Einheit) |
| copyDuration | Die Dauer des Kopiervorgangs. | Int32-Wert in Sekunden |
| throughput | Dies ist die Datenübertragungsrate, die durch die Division von `dataRead` durch `copyDuration` berechnet wird. | Gleitkommazahl in KB/s |
| sourcePeakConnections | Die maximale Anzahl gleichzeitiger Verbindungen mit dem Quelldatenspeicher während der Ausführung der Kopieraktivität. | Int32-Wert (ohne Einheit) |
| sinkPeakConnections| Die maximale Anzahl gleichzeitiger Verbindungen mit dem Senkendatenspeicher während der Ausführung der Kopieraktivität.| Int32-Wert (ohne Einheit) |
| sqlDwPolyBase | Gibt an, ob PolyBase beim Kopieren von Daten in Azure Synapse Analytics verwendet wird. | Boolean |
| redshiftUnload | Gibt an, ob UNLOAD beim Kopieren von Daten aus Redshift verwendet wird. | Boolean |
| hdfsDistcp | Gibt an, ob DistCp beim Kopieren von Daten aus HDFS verwendet wird. | Boolean |
| effectiveIntegrationRuntime | Die Integration Runtimes (IR), die zur Unterstützung der Aktivitätsausführung verwendet werden, im Format `<IR name> (<region if it's Azure IR>)`. | Text (Zeichenfolge) |
| usedDataIntegrationUnits | Die effektiven Datenintegrationseinheiten während des Kopiervorgangs. | Int32-Wert |
| usedParallelCopies | Die effektiven parallelen Kopien während des Kopiervorgangs. | Int32-Wert |
| logPath | Pfad zum Sitzungsprotokoll für übersprungene Daten im Blobspeicher. Siehe [Fehlertoleranz](copy-activity-overview.md#fault-tolerance). | Text (Zeichenfolge) |
| executionDetails | Ausführlichere Informationen zu den Phasen, die die Kopieraktivität durchläuft, sowie zu den entsprechenden Schritten, zur Dauer, zu den Konfigurationen usw. Es wird davon abgeraten, diesen Abschnitt zu analysieren, da er sich möglicherweise ändert. Wenn Sie besser verstehen möchten, wie er Ihnen beim Verstehen der Kopierleistung und der eventuell erforderlichen Problembehandlung hilft, lesen Sie den Abschnitt [Visuelles Überwachen](#monitor-visually). | Array |
| perfRecommendation | Tipps zur Leistungsoptimierung beim Kopieren. Ausführliche Informationen finden Sie unter [Tipps zur Leistungsoptimierung](copy-activity-performance-troubleshooting.md#performance-tuning-tips). | Array |
| billingReference | Die abrechnungsrelevante Nutzung für die angegebene Ausführung. Mehr dazu erfahren Sie unter [Überwachen der Nutzung auf Aktivitätsausführungsebene](plan-manage-costs.md#monitor-consumption-at-activity-run-level). | Object |
| durationInQueue | Dauer der Warteschlange in Sekunden, bevor die Ausführung der Kopieraktivität beginnt. | Object |

**Beispiel:**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "filesSkipped": 0,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "dataConsistencyVerification": 
    { 
        "VerificationResult": "Verified", 
        "InconsistentData": "None" 
    },
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den anderen Artikeln zur Kopieraktivität:

\- [Kopieraktivität – Übersicht](copy-activity-overview.md)

\- [Leistung der Kopieraktivität](copy-activity-performance.md)
