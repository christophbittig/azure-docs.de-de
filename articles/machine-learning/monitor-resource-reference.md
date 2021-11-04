---
title: Referenz zum Überwachen von Azure Machine Learning-Daten | Microsoft-Dokumentation
description: Referenzdokumentation zur Überwachung von Azure Machine Learning Erfahren Sie mehr über die in Azure Monitor gesammelten und verfügbaren Daten und Ressourcen.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/21/2021
ms.openlocfilehash: a4af35e4eded2a715ada162f565ee265c0c2be74
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560132"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Überwachen von Azure Machine Learning-Daten – Referenz

Erfahren Sie mehr über die Daten und Ressourcen, die von Azure Monitor aus Ihrem Azure Machine Learning-Arbeitsbereich gesammelt werden. Ausführliche Informationen über das Sammeln und Analysieren von Überwachungsdaten finden Sie unter [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="metrics"></a>Metriken

In diesem Abschnitt werden alle automatisch erfassten Plattformmetriken aufgeführt, die für Azure Machine Learning gesammelt werden. Der Ressourcenanbieter für diese Metriken ist [Microsoft.MachineLearningServices/workspaces](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces).

**Modell**

| Metrik | Einheit | BESCHREIBUNG |
|--|--|--|
| Erfolgreiche Modellregistrierungen | Anzahl | Anzahl der erfolgreichen Modellregistrierungen in diesem Arbeitsbereich |
| Fehler bei der Modellregistrierung | Anzahl | Anzahl der fehlerhaften Modellregistrierungen in diesem Arbeitsbereich |
| Gestartete Modellimplementierungen | Anzahl | Anzahl der gestarteten Modellimplementierungen in diesem Arbeitsbereich |
| Erfolgreiche Modellimplementierungen | Anzahl | Anzahl der erfolgreichen Modellimplementierungen in diesem Arbeitsbereich |
| Model Deploy Failed (Fehler bei der Modellimplementierung) | Anzahl | Anzahl der fehlerhaften Modellimplementierungen in diesem Arbeitsbereich |

**Kontingent**

Kontingentinformationen gelten nur für Azure Machine Learning Compute.

| Metrik | Einheit | BESCHREIBUNG |
|--|--|--|
| Knoten insgesamt | Anzahl | Gesamtanzahl von Knoten. Diese Summe umfasst Teile von aktiven Knoten, Leerlaufknoten, nicht verwendbaren Knoten, vorzeitig entfernten Knoten, ausscheidenden Knoten. |
| Aktive Knoten | Anzahl | Anzahl der aktiven Knoten. Die Knoten, die einen Auftrag aktiv ausführen. |
| Knoten im Leerlauf | Anzahl | Anzahl von Knoten im Leerlauf. Knoten im Leerlauf sind die Knoten, auf denen keine Aufträge ausgeführt werden, die aber einen neuen Auftrag bei Verfügbarkeit akzeptieren können. |
| Unusable Nodes (Nicht verwendbare Knoten) | Anzahl | Anzahl nicht verwendbarer Knoten. Nicht verwendbare Knoten sind aufgrund eines nicht auflösbaren Problems nicht funktionsfähig. Azure recycelt diese Knoten. |
| Vorzeitig entfernte Knoten | Anzahl | Anzahl der vorzeitig entfernten Knoten. Dabei handelt es sich um die Knoten mit niedriger Priorität, die aus dem Pool verfügbarer Knoten entfernt werden. |
| Ausscheidende Knoten | Anzahl | Anzahl der ausscheidenden Knoten. Ausscheidende Knoten sind die Knoten, auf denen die Verarbeitung eines Auftrags gerade abgeschlossen wurde und die in den Leerlaufzustand wechseln. |
| Kerne insgesamt | Anzahl | Gesamtanzahl von Kernen |
| Aktive Kerne | Anzahl | Gesamtanzahl der aktiven Kerne |
| Kerne im Leerlauf | Anzahl | Gesamtanzahl der Kerne im Leerlauf |
| Nicht verwendbare Kerne | Anzahl | Anzahl der nicht verwendbaren Kerne |
| Vorzeitig entfernte Kerne | Anzahl | Anzahl der vorzeitig entfernten Kerne |
| Ausscheidende Kerne | Anzahl | Anzahl der ausscheidenden Kerne |
| Quota Utilization Percentage (Prozentsatz der Kontingentnutzung) | Anzahl | Prozentualer Anteil der genutzten Kontingente |

**Ressource**

| Metrik| Einheit | BESCHREIBUNG |
|--|--|--|
| CpuUtilization | Anzahl | Prozentsatz der Auslastung auf einem CPU-Knoten. Die Auslastung wird in Intervallen von einer Minute gemeldet. |
| GpuUtilization | Anzahl | Prozentsatz der Auslastung auf einem GPU-Knoten. Die Auslastung wird in Intervallen von einer Minute gemeldet. |
| GpuMemoryUtilization | Anzahl | Arbeitsspeicherauslastung auf einem GPU-Knoten in Prozent – Die Auslastung wird in Intervallen von einer Minute gemeldet. |
| GpuEnergyJoules | Anzahl | Intervallenergie in Joule auf einem GPU-Knoten. Energie wird in Intervallen von einer Minute gemeldet. |

**Run**

Informationen zu Trainingsausführungen für den Arbeitsbereich.

| Metrik | Einheit | BESCHREIBUNG |
|--|--|--|
| Abgebrochene Ausführungen | Anzahl | Anzahl abgebrochener Ausführungen für diesen Arbeitsbereich. Die Anzahl wird aktualisiert, wenn eine Ausführung erfolgreich abgebrochen wurde. |
| Ausführungen mit angefordertem Abbruch | Anzahl | Anzahl von Ausführungen, bei denen ein Abbruch für diesen Arbeitsbereich angefordert wurde – die Anzahl wird aktualisiert, wenn eine Abbruchanforderung für eine Ausführung empfangen wurde. |
| Abgeschlossene Ausführungen | Anzahl | Anzahl erfolgreich abgeschlossener Ausführungen für diesen Arbeitsbereich – die Anzahl wird aktualisiert, wenn eine Ausführung abgeschlossen und die Ausgabe erfasst wurde. |
| Fehlerhafte Ausführungen | Anzahl | Anzahl fehlerhafter Ausführungen für diesen Arbeitsbereich – die Anzahl wird aktualisiert, wenn bei einer Ausführung ein Fehler auftritt. |
| Ausführungen, die abgeschlossen werden | Anzahl | Anzahl von Ausführungen, die für diesen Arbeitsbereich abgeschlossen wurden – die Anzahl wird aktualisiert, wenn eine Ausführung abgeschlossen wurde, aber die Ausgabe noch erfasst wird. | 
| Ausführungen ohne Rückmeldung | Anzahl | Anzahl nicht reagierender Ausführungen für diesen Arbeitsbereich – die Anzahl wird aktualisiert, wenn eine Ausführung zum Status „Keine Rückmeldung“ übergeht. |
| Nicht gestartete Ausführungen | Anzahl | Anzahl von Ausführungen mit Status „Nicht gestartet“ für diesen Arbeitsbereich – die Anzahl wird aktualisiert, wenn eine Anforderung zur Initiierung einer Ausführung eingeht, aber die Ausführungsinformationen nicht aufgefüllt wurden. |
| Ausführungen in Vorbereitung | Anzahl | Anzahl von Ausführungen, die für diesen Arbeitsbereich vorbereitet werden – die Anzahl wird aktualisiert, wenn eine Ausführung in den Status „Wird vorbereitet“ übergeht, während die Ausführungsumgebung vorbereitet wird. |
| Ausführungen in Bereitstellung | Anzahl | Anzahl von Ausführungen, die sich für diesen Arbeitsbereich in Bereitstellung befinden – die Anzahl wird aktualisiert, wenn eine Ausführung darauf wartet, dass ein Computeziel erstellt oder bereitgestellt wird. |
| Ausführungen in Warteschlange | Anzahl | Anzahl von Ausführungen, die sich für diesen Arbeitsbereich in der Warteschlange befinden – die Anzahl wird aktualisiert, wenn eine Ausführung im Computeziel in die Warteschlange eingereiht wird. Dieser Fall kann eintreten, wenn darauf gewartet wird, dass erforderliche Computeknoten bereit sind. |
| Gestartete Ausführungen | Anzahl | Anzahl von Ausführungen für diesen Arbeitsbereich – die Anzahl wird aktualisiert, wenn eine Ausführung mit den erforderlichen Ressourcen gestartet wird. |
| Ausführungen, die gestartet werden | Anzahl | Anzahl gestarteter Ausführungen für diesen Arbeitsbereich – Die Anzahl wird aktualisiert, nachdem eine Anforderung zur Initiierung der Ausführung eingegangen ist und die Ausführungsinformationen (z. B. die Ausführungs-ID) aufgefüllt wurden. |
| Errors | Anzahl | Anzahl von Ausführungsfehlern in diesem Arbeitsbereich – die Anzahl wird aktualisiert, wenn bei der Ausführung ein Fehler auftritt. |
| Warnungen | Anzahl | Anzahl von Ausführungswarnungen in diesem Arbeitsbereich – die Anzahl wird aktualisiert, wenn bei einer Ausführung eine Warnung auftritt. |

## <a name="metric-dimensions"></a>Metrikdimensionen

Weitere Informationen zu Metrikdimensionen finden Sie unter [Mehrdimensionale Metriken](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Den Metriken in Azure Machine Learning sind die folgenden Dimensionen zugeordnet.

| Dimension | Beschreibung |
| ---- | ---- |
| Clustername | Der Name der Computeclusterressource. Diese Dimension ist für alle Kontingentmetriken verfügbar. |
| Vm Family Name (Name der VM-Familie) | Der Name der vom Cluster verwendeten VM-Familie Diese Dimension ist für die Metrik „Quota Utilization Percentage“ (Prozentsatz der Kontingentnutzung) verfügbar. |
| Vm Priority (VM-Priorität) | Die Priorität des virtuellen Computers (VM) Diese Dimension ist für die Metrik „Quota Utilization Percentage“ (Prozentsatz der Kontingentnutzung) verfügbar.
| CreatedTime | Diese Dimension ist nur für CpuUtilization und GpuUtilization verfügbar. |
| deviceId | ID des Geräts (GPU). Nur für GpuUtilization verfügbar. |
| NodeId | ID des erstellten Knotens, auf dem der Auftrag ausgeführt wird. Diese Dimension ist nur für CpuUtilization und GpuUtilization verfügbar. |
| RunId | ID des Laufs/Auftrags. Diese Dimension ist nur für CpuUtilization und GpuUtilization verfügbar. |
| ComputeType | Der für die Ausführung verwendete Computetyp Diese Dimension ist nur für die Metriken „Abgeschlossene Ausführungen“, „Failed Runs“ (Ausführungen mit Fehler) und „Started Runs“ (Begonnene Ausführungen) verfügbar. |
| PipelineStepType | Der Typ des [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep)-Objekts, das in der Ausführung verwendet wurde Diese Dimension ist nur für die Metriken „Abgeschlossene Ausführungen“, „Failed Runs“ (Ausführungen mit Fehler) und „Started Runs“ (Begonnene Ausführungen) verfügbar. |
| PublishedPipelineId | Die ID der veröffentlichten Pipeline, die in der Ausführung verwendet wurde Diese Dimension ist nur für die Metriken „Abgeschlossene Ausführungen“, „Failed Runs“ (Ausführungen mit Fehler) und „Started Runs“ (Begonnene Ausführungen) verfügbar. |
| RunType | Der Typ der Ausführung. Diese Dimension ist nur für die Metriken „Abgeschlossene Ausführungen“, „Failed Runs“ (Ausführungen mit Fehler) und „Started Runs“ (Begonnene Ausführungen) verfügbar. |

Gültige Werte für die RunType-Dimension sind:

| Wert | Beschreibung |
| ----- | ----- |
| Experiment | Nicht-Pipelineausführungen |
| PipelineRun | Eine Pipelineausführung, die das übergeordnete Element einer Schrittausführung (StepRun) ist |
| StepRun | Eine Ausführung für einen Pipelineschritt |
| ReusedStepRun | Eine Ausführung für einen Pipelineschritt, in dem eine vorherige Ausführung erneut verwendet wird |

## <a name="activity-log"></a>Aktivitätsprotokoll

In der folgenden Tabelle finden Sie die Vorgänge in Zusammenhang mit Azure Machine Learning, die im Aktivitätsprotokoll erstellt werden können.

| Vorgang | BESCHREIBUNG |
|:---|:---|
| Creates or updates a Machine Learning workspace (Machine Learning-Arbeitsbereich erstellen oder aktualisieren) | Ein Arbeitsbereich wurde erstellt oder aktualisiert. |
| CheckComputeNameAvailability | Hiermit wird überprüft, ob ein Computename bereits verwendet wird. |
| Creates or updates the compute resources (Computeressourcen erstellen oder aktualisieren) | Eine Computeressource wurde erstellt oder aktualisiert. |
| Deletes the compute resources (Computeressourcen löschen) | Eine Computeressource wurde gelöscht. |
| Auflisten geheimer Schlüssel | Hiermit werden Geheimnisse für einen Vorgang für einen Machine Learning-Arbeitsbereich aufgelistet. |

## <a name="resource-logs"></a>Ressourcenprotokolle

In diesem Abschnitt werden die Typen der Ressourcenprotokolle aufgeführt, die für einen Azure Machine Learning-Arbeitsbereich erfasst werden können.

Ressourcenanbieter und Typ: [Microsoft.MachineLearningServices/workspace](../azure-monitor/essentials/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces).

| Kategorie | Anzeigename |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |
| ModelsChangeEvent | ModelsChangeEvent |
| ModelsReadEvent | ModelsReadEvent |
| ModelsActionEvent | ModelsActionEvent |
| DeploymentReadEvent | DeploymentReadEvent |
| DeploymentEventACI | DeploymentEventACI |
| DeploymentEventAKS | DeploymentEventAKS |
| InferencingOperationAKS | InferencingOperationAKS |
| InferencingOperationACI | InferencingOperationACI |
| EnvironmentChangeEvent | EnvironmentChangeEvent |
| EnvironmentReadEvent | EnvironmentReadEvent |
| DataLabelChangeEvent | DataLabelChangeEvent |
| DataLabelReadEvent | DataLabelReadEvent |
| ComputeInstanceEvent | ComputeInstanceEvent |
| DataStoreChangeEvent | DataStoreChangeEvent |
| DataStoreReadEvent | DataStoreReadEvent |
| DataSetChangeEvent | DataSetChangeEvent |
| DataSetReadEvent | DataSetReadEvent |
| PipelineChangeEvent | PipelineChangeEvent |
| PipelineReadEvent | PipelineReadEvent |
| RunEvent | RunEvent |
| RunReadEvent | RunReadEvent |


## <a name="schemas"></a>Schemas

Die folgenden Schemas werden von Azure Machine Learning verwendet.

### <a name="amlcomputejobevent-table"></a>Tabelle AmlComputeJobEvent

| Eigenschaft | BESCHREIBUNG |
|:--- |:---|
| TimeGenerated | Zeitpunkt, zu dem der Protokolleintrag generiert wurde |
| Vorgangsname | Name des Vorgangs, der dem Protokollereignis zugeordnet ist |
| Kategorie | Name des Protokollereignisses, AmlComputeClusterNodeEvent |
| JobId | ID des übermittelten Auftrags |
| ExperimentId | ID des Experiments |
| ExperimentName | Name des Experiments |
| CustomerSubscriptionId | Abonnement-ID, unter der das Experiment und der Auftrag übermittelt wurden |
| WorkspaceName | Name des Machine Learning-Arbeitsbereichs |
| ClusterName | Name des Clusters |
| ProvisioningState | Status der Auftragsübermittlung |
| ResourceGroupName | Name der Ressourcengruppe |
| JobName | Name des Auftrags |
| ClusterId | ID des Clusters |
| EventType | Hierbei handelt es sich um den Typ des Auftragsereignisses. Beispiele: JobSubmitted, JobRunning, JobFailed oder JobSucceeded. |
| ExecutionState | Hierbei handelt es sich um den Status des Auftrags (der Ausführung). Beispiele: Queued, Running, Succeeded oder Failed. |
| ErrorDetails | Details zum Auftragsfehler |
| CreationApiVersion | API-Version, mit der der Auftrag erstellt wurde |
| ClusterResourceGroupName | Ressourcengruppenname des Clusters |
| TFWorkerCount | Anzahl der TF-Worker |
| TFParameterServerCount | Anzahl der TF-Parameterserver |
| ToolType | Typ des verwendeten Tools |
| RunInContainer | Flag, das beschreibt, ob der Auftrag in einem Container ausgeführt werden soll |
| JobErrorMessage | Ausführliche Meldung für den Auftragsfehler |
| NodeId | ID des erstellten Knotens, auf dem der Auftrag ausgeführt wird |

### <a name="amlcomputeclusterevent-table"></a>Tabelle AmlComputeClusterEvent

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| TimeGenerated | Zeitpunkt, zu dem der Protokolleintrag generiert wurde |
| Vorgangsname | Name des Vorgangs, der dem Protokollereignis zugeordnet ist |
| Kategorie | Name des Protokollereignisses, AmlComputeClusterNodeEvent |
| ProvisioningState | Bereitstellungsstatus des Clusters |
| ClusterName | Name des Clusters |
| ClusterType | Typ des Clusters |
| CreatedBy | Benutzer, der den Cluster erstellt hat |
| CoreCount | Anzahl der Kerne im Cluster |
| VmSize | VM-Größe des Clusters |
| VmPriority | Priorität der Knoten, die in einem Cluster erstellt wurden (Dedicated/LowPriority) |
| ScalingType | Typ der Clusterskalierung (manual/auto) |
| InitialNodeCount | Anfängliche Knotenanzahl des Clusters |
| MinimumNodeCount | Kleinste Knotenanzahl des Clusters |
| MaximumNodeCount | Größte Knotenanzahl des Clusters |
| NodeDeallocationOption | Art und Weise, wie die Zuordnung des Knotens aufgehoben werden soll |
| Herausgeber | Herausgeber des Clustertyps |
| Angebot | Angebot, mit dem der Cluster erstellt wird |
| Sku | SKU des im Cluster erstellten Knotens/virtuellen Computers |
| Version | Version des Images, das beim Erstellen des Knotens/virtuellen Computers verwendet wird |
| SubnetId | Subnetz-ID des Clusters |
| AllocationState | Zuweisungsstatus des Clusters |
| CurrentNodeCount | Aktuelle Knotenanzahl des Clusters |
| TargetNodeCount | Zielknotenanzahl des Clusters beim zentralen Hoch-/Herunterskalieren |
| EventType | Typ des Ereignisses während der Clustererstellung |
| NodeIdleTimeSecondsBeforeScaleDown | Leerlaufzeit in Sekunden vor horizontalem Herunterskalieren des Clusters |
| PreemptedNodeCount | Anzahl der vorzeitig entfernten Knoten des Clusters |
| IsResizeGrow | Flag, das angibt, dass der Cluster zentral hochskaliert wird |
| VmFamilyName | Name der VM-Familie der Knoten, die im Cluster erstellt werden können |
| LeavingNodeCount | Anzahl der verbliebenen Knoten des Clusters |
| UnusableNodeCount | Anzahl der nicht verwendbaren Knoten des Clusters |
| IdleNodeCount | Anzahl der nicht verwendbaren Knoten des Clusters |
| RunningNodeCount | Anzahl der ausführenden Knoten des Clusters |
| PreparingNodeCount | Anzahl der vorzubereitenden Knoten des Clusters |
| QuotaAllocated | Zugeordnetes Kontingent für den Cluster |
| QuotaUtilized | Genutztes Kontingent für den Cluster |
| AllocationStateTransitionTime | Übergangszeit von einem Zustand in einen anderen |
| ClusterErrorCodes | Fehlercode, der während der Clustererstellung oder Skalierung empfangen wurde |
| CreationApiVersion | API-Version, die beim Erstellen des Clusters verwendet wurde |

### <a name="amlcomputeclusternodeevent-table"></a>Tabelle AmlComputeClusterNodeEvent

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| TimeGenerated | Zeitpunkt, zu dem der Protokolleintrag generiert wurde |
| Vorgangsname | Name des Vorgangs, der dem Protokollereignis zugeordnet ist |
| Kategorie | Name des Protokollereignisses, AmlComputeClusterNodeEvent |
| ClusterName | Name des Clusters |
| NodeId | ID des erstellten Clusterknotens |
| VmSize | Größe des virtuellen Computers des Knotens |
| VmFamilyName | VM-Familie, zu der der Knoten gehört |
| VmPriority | Priorität des erstellten Knotens (Dedicated/LowPriority) |
| Herausgeber | Hierbei handelt es sich um den Herausgeber des VM-Image. Beispiel: microsoft-dsvm. |
| Angebot | Angebot, das mit der VM-Erstellung verknüpft ist |
| Sku | SKU des erstellten Knotens/virtuellen Computers |
| Version | Version des Images, das beim Erstellen des Knotens/virtuellen Computers verwendet wird |
| ClusterCreationTime | Zeitpunkt, zu dem der Cluster erstellt wurde |
| ResizeStartTime | Zeitpunkt, zu dem das zentrale Hoch-/Herunterskalieren des Clusters gestartet wurde |
| ResizeEndTime | Zeitpunkt, zu dem das zentrale Hoch-/Herunterskalieren des Clusters beendet wurde |
| NodeAllocationTime | Zeitpunkt, zu dem der Knoten zugeordnet wurde |
| NodeBootTime | Zeitpunkt, zu dem der Knoten hochgefahren wurde |
| StartTaskStartTime | Zeitpunkt, zu dem die Aufgabe einem Knoten zugewiesen und gestartet wurde |
| StartTaskEndTime | Zeitpunkt, zu dem die einem Knoten zugewiesene Aufgabe beendet wurde |
| TotalE2ETimeInSeconds | Gesamtzeit, die der Knoten aktiv war |

### <a name="amlcomputeinstanceevent-table"></a>Tabelle AmlComputeInstanceEvent

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| type | Name des Protokollereignisses, AmlComputeInstanceEvent |
| TimeGenerated | Zeitpunkt (UTC), zu dem der Protokolleintrag generiert wurde |
| Ebene | Der Schweregrad des Ereignisses. Er kann „Informational“ (Information), „Warning“ (Warnung), „Error“ (Fehler) oder „Critical“ (Kritisch) lauten. |
| ResultType | Der Status des Ereignisses. Häufige Werte sind „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“ und „Resolved“ (Gestartet, In Bearbeitung, Erfolgreich, Fehler, Aktiv, Gelöst). |
| CorrelationId | Eine GUID, die ggf. zum Gruppieren eines Satzes mit verwandten Ereignissen verwendet wird. |
| Vorgangsname | Der Name des Vorgangs, der dem Protokollereignis zugeordnet ist |
| Identity | Identität des Benutzers oder der Anwendung, der bzw. die den Vorgang durchgeführt hat |
| AadTenantId | Die AAD-Mandanten-ID, für die der Vorgang übermittelt wurde |
| AmlComputeInstanceName | Der Name der Compute-Instanz, die dem Protokolleintrag zugeordnet ist |

### <a name="amldatalabelevent-table"></a>Tabelle AmlDataLabelEvent

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| type | Name des Protokollereignisses, AmlDataLabelEvent |
| TimeGenerated | Zeitpunkt (UTC), zu dem der Protokolleintrag generiert wurde |
| Ebene | Der Schweregrad des Ereignisses. Er kann „Informational“ (Information), „Warning“ (Warnung), „Error“ (Fehler) oder „Critical“ (Kritisch) lauten. |
| ResultType | Der Status des Ereignisses. Häufige Werte sind „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“ und „Resolved“ (Gestartet, In Bearbeitung, Erfolgreich, Fehler, Aktiv, Gelöst). |
| CorrelationId | Eine GUID, die ggf. zum Gruppieren eines Satzes mit verwandten Ereignissen verwendet wird. |
| Vorgangsname | Der Name des Vorgangs, der dem Protokollereignis zugeordnet ist |
| Identity | Identität des Benutzers oder der Anwendung, der bzw. die den Vorgang durchgeführt hat |
| AadTenantId | Die AAD-Mandanten-ID, für die der Vorgang übermittelt wurde |
| AmlProjectId | Der eindeutige Bezeichner des AML-Projekts |
| AmlProjectName | Der Name des AML Projekts |
| AmlLabelNames | Die Bezeichnungsklassennamen, die für das Projekt erstellt werden |
| AmlDataStoreName | Der Name des Datenspeichers, in dem die Projektdaten gespeichert werden |

### <a name="amldatasetevent-table"></a>Tabelle AmlDataSetEvent

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| type | Der Name des Protokollereignisses, AmlDataSetEvent |
| TimeGenerated | Zeitpunkt (UTC), zu dem der Protokolleintrag generiert wurde |
| Ebene | Der Schweregrad des Ereignisses. Er kann „Informational“ (Information), „Warning“ (Warnung), „Error“ (Fehler) oder „Critical“ (Kritisch) lauten. |
| ResultType | Der Status des Ereignisses. Häufige Werte sind „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“ und „Resolved“ (Gestartet, In Bearbeitung, Erfolgreich, Fehler, Aktiv, Gelöst). |
| AmlWorkspaceId | Eine GUID und eine eindeutige ID des AML Arbeitsbereichs |
| Vorgangsname | Der Name des Vorgangs, der dem Protokollereignis zugeordnet ist |
| Identity | Identität des Benutzers oder der Anwendung, der bzw. die den Vorgang durchgeführt hat |
| AadTenantId | Die AAD-Mandanten-ID, für die der Vorgang übermittelt wurde |
| AmlDatasetId | Die ID des AML-Datasets |
| AmlDatasetName | Der Name des AML-Datasets |

### <a name="amldatastoreevent-table"></a>Tabelle AmlDataStoreEvent

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| type | Name des Protokollereignisses, AmlDataStoreEvent |
| TimeGenerated | Zeitpunkt (UTC), zu dem der Protokolleintrag generiert wurde |
| Ebene | Der Schweregrad des Ereignisses. Er kann „Informational“ (Information), „Warning“ (Warnung), „Error“ (Fehler) oder „Critical“ (Kritisch) lauten. |
| ResultType | Der Status des Ereignisses. Häufige Werte sind „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“ und „Resolved“ (Gestartet, In Bearbeitung, Erfolgreich, Fehler, Aktiv, Gelöst). |
| AmlWorkspaceId | Eine GUID und eine eindeutige ID des AML Arbeitsbereichs |
| Vorgangsname | Der Name des Vorgangs, der dem Protokollereignis zugeordnet ist |
| Identity | Identität des Benutzers oder der Anwendung, der bzw. die den Vorgang durchgeführt hat |
| AadTenantId | Die AAD-Mandanten-ID, für die der Vorgang übermittelt wurde |
| AmlDatastoreName | Der Name des AML-Datenspeichers |

### <a name="amldeploymentevent-table"></a>Tabelle AmlDeploymentEvent

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| type | Der Name des Protokollereignisses, AmlDeploymentEvent |
| TimeGenerated | Zeitpunkt (UTC), zu dem der Protokolleintrag generiert wurde |
| Ebene | Der Schweregrad des Ereignisses. Er kann „Informational“ (Information), „Warning“ (Warnung), „Error“ (Fehler) oder „Critical“ (Kritisch) lauten. |
| ResultType | Der Status des Ereignisses. Häufige Werte sind „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“ und „Resolved“ (Gestartet, In Bearbeitung, Erfolgreich, Fehler, Aktiv, Gelöst). |
| Vorgangsname | Der Name des Vorgangs, der dem Protokollereignis zugeordnet ist |
| Identity | Identität des Benutzers oder der Anwendung, der bzw. die den Vorgang durchgeführt hat |
| AadTenantId | Die AAD-Mandanten-ID, für die der Vorgang übermittelt wurde |
| AmlServiceName | Der Name des AML-Diensts |

### <a name="amlinferencingevent-table"></a>Tabelle AmlInferencingEvent

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| type | Der Name des Protokollereignisses, AmlInferencingEvent |
| TimeGenerated | Zeitpunkt (UTC), zu dem der Protokolleintrag generiert wurde |
| Ebene | Der Schweregrad des Ereignisses. Er kann „Informational“ (Information), „Warning“ (Warnung), „Error“ (Fehler) oder „Critical“ (Kritisch) lauten. |
| ResultType | Der Status des Ereignisses. Häufige Werte sind „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“ und „Resolved“ (Gestartet, In Bearbeitung, Erfolgreich, Fehler, Aktiv, Gelöst). |
| Vorgangsname | Der Name des Vorgangs, der dem Protokollereignis zugeordnet ist |
| Identity | Identität des Benutzers oder der Anwendung, der bzw. die den Vorgang durchgeführt hat |
| AadTenantId | Die AAD-Mandanten-ID, für die der Vorgang übermittelt wurde |
| AmlServiceName | Der Name des AML-Diensts |

### <a name="amlmodelsevent-table"></a>Tabelle AmlModelsEvent

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| type | Der Name des Protokollereignisses, AmlModelsEvent |
| TimeGenerated | Zeitpunkt (UTC), zu dem der Protokolleintrag generiert wurde |
| Ebene | Der Schweregrad des Ereignisses. Er kann „Informational“ (Information), „Warning“ (Warnung), „Error“ (Fehler) oder „Critical“ (Kritisch) lauten. |
| ResultType | Der Status des Ereignisses. Häufige Werte sind „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“ und „Resolved“ (Gestartet, In Bearbeitung, Erfolgreich, Fehler, Aktiv, Gelöst). |
| Vorgangsname | Der Name des Vorgangs, der dem Protokollereignis zugeordnet ist |
| Identity | Identität des Benutzers oder der Anwendung, der bzw. die den Vorgang durchgeführt hat |
| AadTenantId | Die AAD-Mandanten-ID, für die der Vorgang übermittelt wurde |
| ResultSignature | Der HTTP-Statuscode des Ereignisses. Typische Werte sind 200, 201, 202 usw. |
| AmlModelName | Der Name des AML-Modells |

### <a name="amlpipelineevent-table"></a>Tabelle AmlPipelineEvent

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| type | Der Name des Protokollereignisses, AmlPipelineEvent |
| TimeGenerated | Zeitpunkt (UTC), zu dem der Protokolleintrag generiert wurde |
| Ebene | Der Schweregrad des Ereignisses. Er kann „Informational“ (Information), „Warning“ (Warnung), „Error“ (Fehler) oder „Critical“ (Kritisch) lauten. |
| ResultType | Der Status des Ereignisses. Häufige Werte sind „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“ und „Resolved“ (Gestartet, In Bearbeitung, Erfolgreich, Fehler, Aktiv, Gelöst). |
| AmlWorkspaceId | Eine GUID und eine eindeutige ID des AML Arbeitsbereichs |
| AmlWorkspaceId | Der Name des AML-Arbeitsbereichs |
| Vorgangsname | Der Name des Vorgangs, der dem Protokollereignis zugeordnet ist |
| Identity | Identität des Benutzers oder der Anwendung, der bzw. die den Vorgang durchgeführt hat |
| AadTenantId | Die AAD-Mandanten-ID, für die der Vorgang übermittelt wurde |
| AmlModuleId | Eine GUID und eine eindeutige ID des Moduls|
| AmlModelName | Der Name des AML-Modells |
| AmlPipelineId | Die ID der AML-Pipeline |
| AmlParentPipelineId | Die ID der übergeordneten AML-Pipeline (beim Klonen) |
| AmlPipelineDraftId | Die ID des AML-Pipelineentwurfs |
| AmlPipelineDraftName | Der Name des AML-Pipelineentwurfs |
| AmlPipelineEndpointId | Die ID des AML-Pipelineendpunkts |
| AmlPipelineEndpointName | Der Name des AML-Pipelineendpunkts |


### <a name="amlrunevent-table"></a>Tabelle AmlRunEvent

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| type | Der Name des Protokollereignisses, AmlRunEvent |
| TimeGenerated | Zeitpunkt (UTC), zu dem der Protokolleintrag generiert wurde |
| Ebene | Der Schweregrad des Ereignisses. Er kann „Informational“ (Information), „Warning“ (Warnung), „Error“ (Fehler) oder „Critical“ (Kritisch) lauten. |
| ResultType | Der Status des Ereignisses. Häufige Werte sind „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“ und „Resolved“ (Gestartet, In Bearbeitung, Erfolgreich, Fehler, Aktiv, Gelöst). |
| Vorgangsname | Der Name des Vorgangs, der dem Protokollereignis zugeordnet ist |
| AmlWorkspaceId | Eine GUID und eine eindeutige ID des AML Arbeitsbereichs |
| Identity | Identität des Benutzers oder der Anwendung, der bzw. die den Vorgang durchgeführt hat |
| AadTenantId | Die AAD-Mandanten-ID, für die der Vorgang übermittelt wurde |
| RunId | Die eindeutige ID des Durchlaufs. |

### <a name="amlenvironmentevent--table"></a>Tabelle AmlEnvironmentEvent

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| type | Der Name des Protokollereignisses, AmlEnvironmentEvent |
| TimeGenerated | Zeitpunkt (UTC), zu dem der Protokolleintrag generiert wurde |
| Ebene | Der Schweregrad des Ereignisses. Er kann „Informational“ (Information), „Warning“ (Warnung), „Error“ (Fehler) oder „Critical“ (Kritisch) lauten. |
| Vorgangsname | Der Name des Vorgangs, der dem Protokollereignis zugeordnet ist |
| Identity | Identität des Benutzers oder der Anwendung, der bzw. die den Vorgang durchgeführt hat |
| AadTenantId | Die AAD-Mandanten-ID, für die der Vorgang übermittelt wurde |
| AmlEnvironmentName | Der Name der AML-Umgebungskonfiguration |
| AmlEnvironmentVersion | Der Name der Version der AML-Umgebungskonfiguration |


## <a name="see-also"></a>Weitere Informationen

- Eine Beschreibung der Überwachung von Azure Machine Learning finden Sie unter [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md).
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
