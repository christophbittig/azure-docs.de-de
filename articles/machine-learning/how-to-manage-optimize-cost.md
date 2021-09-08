---
title: Verwalten und Optimieren von Kosten
titleSuffix: Azure Machine Learning
description: Hier finden Sie Tipps zum Optimieren Ihrer Kosten beim Erstellen von Machine Learning-Modellen in Azure Machine Learning.
author: luisquintanilla
ms.author: luquinta
ms.custom: subject-cost-optimization
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: bf29f435c2d9439659abdcc76a7f8d85cf51c2af
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346674"
---
# <a name="manage-and-optimize-azure-machine-learning-costs"></a>Verwalten und Optimieren von Azure Machine Learning-Kosten

Erfahren Sie, wie Sie Kosten beim Trainieren und Bereitstellen von Machine Learning-Modellen für Azure Machine Learning verwalten und optimieren.

Verwenden Sie die folgenden Tipps, um Ihre Kosten für Computeressourcen zu verwalten und zu optimieren.

- Konfigurieren Sie Ihre Trainingscluster für automatische Skalierung
- Legen Sie Kontingente für Ihr Abonnement und Ihre Arbeitsbereiche fest
- Legen Sie Terminierungsrichtlinien für Ihre Trainingsläufe fest
- Verwenden Sie virtuelle Computer (VMs) mit niedriger Priorität
- Planen Sie das automatische Herunterfahren und Starten von Compute-Instanzen.
- Verwenden Sie eine reservierte Azure-VM-Instanz
- Trainieren Sie lokal
- Parallelisieren Sie das Training
- Legen Sie Richtlinien für die Aufbewahrung und das Löschen von Daten fest
- Stellen Sie Ressourcen in derselben Region bereit

Informationen zur Planung und Überwachung von Kosten finden Sie im Leitfaden zum [Planen der Kostenverwaltung für Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Verwenden des Azure Machine Learning Compute-Clusters (AmlCompute)

Bei sich ständig verändernden Daten benötigen Sie ein schnelles und optimiertes Training und Nachtraining von Modellen, um die Genauigkeit der Modelle aufrecht zu erhalten. Fortlaufendes Training hat aber seinen Preis, insbesondere bei Deep Learning-Modellen auf GPUs. 

Azure Machine Learning-Benutzer können den verwalteten Azure Machine Learning-Computecluster verwenden, der auch als „AmlCompute“ bezeichnet wird. AmlCompute unterstützt eine Vielzahl von GPU- und CPU-Optionen. Der AmlCompute-Cluster wird von Azure Machine Learning intern im Auftrag Ihres Abonnements gehostet. Er bietet die gewohnte Sicherheit, Compliance und Governance auf Unternehmensniveau im Umfang einer Azure-IaaS-Cloud.

Da sich diese Computepools innerhalb der Azure IaaS-Infrastruktur befinden, können Sie Ihr Training mit den gleichen Sicherheits- und Complianceanforderungen bereitstellen, skalieren und verwalten wie den Rest Ihrer Infrastruktur.  Diese Bereitstellungen erfolgen in Ihrem Abonnement und richten sich nach Ihren Governanceregeln. Weitere Informationen zu [Azure Machine Learning Compute](how-to-create-attach-compute-cluster.md).

## <a name="configure-training-clusters-for-autoscaling"></a>Konfigurieren von Trainingsclustern für automatische Skalierung

Cluster mit automatischer Skalierung auf der Grundlage Ihrer Workloadanforderungen helfen Ihnen, Ihre Kosten zu senken, indem Sie nur nutzen, was Sie benötigen.

AmlCompute-Cluster sind auf die workloadabhängige Skalierung ausgelegt. Der Cluster kann auf die maximale Anzahl der konfigurierten Knoten hochskaliert werden. Nach dem Abschluss der einzelnen Durchläufe gibt der Cluster Knoten frei und skaliert auf die von Ihnen konfigurierte minimale Knotenanzahl herunter.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Sie können auch festlegen, wie lange sich der Knoten im Leerlauf befindet, bevor er herunterskaliert wird. Standardmäßig ist die Leerlaufzeit vor dem Herunterskalieren auf 120 Sekunden festgelegt.

+ Wenn Sie weniger iterative Experimente ausführen, setzen Sie diese Zeit herab, um Kosten zu sparen.
+ Wenn Sie hochgradig iterative Dev/Test-Experimente ausführen, müssen Sie die Zeit möglicherweise erhöhen, damit Sie nicht für ständiges Hoch- und Herunterskalieren nach jeder Änderung Ihres Trainingsskripts oder Ihrer Umgebung zahlen müssen.

AmlCompute-Cluster können im Azure-Portal für Ihre wechselnden Workloadanforderungen konfiguriert werden, mithilfe der [AmlCompute SDK-Klasse](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute), des [AmlCompute-CLIs](/cli/azure/ml(v1)/computetarget/create#az_ml_v1__computetarget_create_amlcompute) und mit den [REST-APIs](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>Festlegen von Kontingenten für Ressourcen

AmlCompute bietet eine [Konfiguration für Kontingente (Limits)](how-to-manage-quotas.md#azure-machine-learning-compute). Dieses Kontingent ist für die VM-Familie spezifisch (z. B. Dv2-Serie, NCv3-Serie) und variiert für die einzelnen Abonnements nach Region. Abonnements beginnen mit kleinen Standardwerten für den Einstieg, aber mit dieser Einstellung können Sie die Menge der Amlcompute-Ressourcen bestimmen, die für das Hochfahren in Ihrem Abonnement verfügbar sind. 

Konfigurieren Sie außerdem für jeden Arbeitsbereich innerhalb eines Abonnements das [Kontingent auf Arbeitsbereichsebene pro VM-Familie](how-to-manage-quotas.md#workspace-level-quotas). Diese ermöglicht Ihnen eine präzisere Kontrolle der Kosten, die für die einzelnen Arbeitsbereiche anfallen können, und eine Einschränkung bestimmter VM-Familien. 

Beginnen Sie das Festlegen von Kontingenten auf Arbeitsbereichsebene im [Azure-Portal](https://portal.azure.com).  Wählen Sie einen beliebigen Arbeitsbereich in Ihrem Abonnement aus, und wählen Sie im linken Bereich **Nutzung + Kontingente** aus. Wählen Sie anschließend die Registerkarte **Kontingente konfigurieren** aus, um die Kontingente anzuzeigen. Sie benötigen Berechtigungen auf Abonnementebene, um dieses Kontingent festzulegen, da diese Einstellung mehrere Arbeitsbereiche betrifft.

## <a name="set-run-autotermination-policies"></a>Festlegen von Richtlinien für die automatische Beendigung von Durchläufen 

In einigen Fällen sollten Sie Ihre Trainingsdurchläufe so konfigurieren, dass ihre Dauer eingeschränkt wird oder sie vorzeitig beendet werden. Das gilt beispielsweise, wenn Sie die integrierte Hyperparameteroptimierung von Azure Machine Learning oder automatisiertes Machine Learning nutzen.

Dies sind einige der Optionen, die sich Ihnen bieten:
* Definieren Sie einen Parameter mit dem Namen `max_run_duration_seconds` in ihrer RunConfiguration, um die maximale Dauer zu steuern, auf die ein Lauf auf der von Ihnen gewählten Compute (lokale oder Remote-Cloudcompute) ausgedehnt werden kann.
* Definieren Sie für die [Hyperparameteroptimierung](how-to-tune-hyperparameters.md#early-termination) eine Richtlinie für vorzeitige Beendigung, eine Medianstopprichtlinie oder eine Kürzungsauswahlrichtlinie. Verwenden Sie Parameter wie `max_total_runs` oder `max_duration_minutes`, um Hyperparametersweeps präziser zu steuern.
* Legen Sie für [automatisiertes maschinelles Lernen](how-to-configure-auto-train.md#exit) ähnliche Beendigungsrichtlinien mithilfe des Flags `enable_early_stopping` fest. Verwenden Sie darüber hinaus Eigenschaften wie `iteration_timeout_minutes` und `experiment_timeout_minutes`, um die maximale Dauer eines Laufs für das gesamte Experiment zu steuern.

## <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Verwenden von virtuellen Computern mit niedriger Priorität

Azure erlaubt es Ihnen, nicht ausgelastete Überschusskapazität in Form von VMs mit niedriger Priorität für VM-Skalierungsgruppen, Batch und den Machine Learning-Dienst zu verwenden. Für diese Zuordnungen können Bevorrechtigungen festgelegt werden (pre-emptible), sie bieten jedoch den Vorteil eines niedrigeren Preises im Vergleich mit dedizierten VMs. Im Allgemeinen wird empfohlen, VMS mit niedriger Priorität für Batchworkloads zu verwenden. Sie sollten diese ebenfalls in Fällen verwenden, in denen eine Wiederherstellung nach Unterbrechungen erfolgen kann – entweder durch erneute Einleitung (bei Batchrückschlüssen) oder mithilfe eines Neustarts (bei Training mit Prüfpunktausführung in Deep-Learning-Szenarios).

VMs mit niedriger Priorität besitzen ein einzelnes Kontingent, das sich nach der VM-Familie richtet. Weitere Informationen über [AmlCompute-Kontingente](how-to-manage-quotas.md).

 VMs mit geringer Priorität funktionieren nicht als Computeinstanzen, da sie in der Lage sein müssen, interaktive Notebookumgebungen zu unterstützen.

## <a name="schedule-compute-instances"></a>Planen von Compute-Instanzen

Wenn Sie eine [Compute-Instanz](concept-compute-instance.md) erstellen, bleibt die VM eingeschaltet, damit sie für Ihre Arbeit verfügbar ist.  [Richten Sie einen Zeitplan ein](how-to-create-manage-compute-instance.md#schedule), um die Compute-Instanz automatisch zu starten und zu beenden (Vorschau), wenn Sie nicht planen, sie zu verwenden, und damit Kosten zu sparen.

## <a name="use-reserved-instances"></a>Verwenden von reservierten Instanzen

Reservierte Azure VM-Instanzen sind eine weitere Möglichkeit zur Kosteneinsparung bei Computeressourcen. Bei diesem Angebot stehen Verträge mit ein- oder dreijähriger Laufzeit zur Wahl. Diese Rabatte erreichen bis zu 72 % gegenüber den Preisen für nutzungsbasierte Bezahlung und werden direkt auf Ihre monatliche Azure-Rechnung angewendet.

Azure Machine Learning Compute unterstützt reservierte Instanzen nativ. Wenn Sie eine reservierte Instanz für ein Jahr oder drei Jahre kaufen, wird der Rabatt automatisch auf ihre verwalteten Azure Machine Learning-Computeressourcen angewendet.

## <a name="train-locally"></a>Trainieren Sie lokal

Erwägen Sie beim Erstellen von Prototypen und Ausführen von Trainingsaufträgen, die ausreichend klein sind, um auf Ihrem lokalen Computer ausgeführt zu werden, das lokale Training. Wenn Sie das Python SDK verwenden und Ihr Computeziel auf `local` festlegen, wird das Skript lokal ausgeführt. Weitere Informationen finden Sie unter [Konfigurieren und Übermitteln von Trainingsausführungen](how-to-set-up-training-targets.md#select-a-compute-target).

Visual Studio Code bietet eine Umgebung mit vollem Funktionsumfang für die Entwicklung Ihrer Machine Learning-Anwendungen. Mithilfe der visuellen Visual Studio Code-Erweiterung von Azure Machine Learning und Docker können Sie die Ausführung und das Debuggen lokal vornehmen. Weitere Informationen finden Sie unter [Interaktives Debuggen mit Visual Studio Code](how-to-debug-visual-studio-code.md).

## <a name="parallelize-training"></a>Parallelisieren Sie das Training

Eine der wichtigsten Methoden zur Optimierung von Kosten und Leistung ist die Parallelisierung der Workload mithilfe eines Schritts zur parallelen Ausführung in Azure Machine Learning. Mit diesem Schritt können Sie viele kleinere Knoten verwenden, um den Task parallel auszuführen, sodass Sie die horizontale Skalierung verwenden können. Für die Parallelisierung fällt ein Mehraufwand an. Abhängig von der Workload und dem Grad der Parallelität, der erreicht werden kann, stellt dies eine Möglichkeit dar. Weitere Informationen finden Sie in der Dokumentation zu [ParallelRunStep](xref:azureml.contrib.pipeline.steps.ParallelRunStep).

## <a name="set-data-retention--deletion-policies"></a>Festlegen von Richtlinien für Datenaufbewahrung und Löschen

Bei jeder Ausführung einer Pipeline werden bei jedem Schritt Zwischen-Datasets generiert. Im Laufe der Zeit nehmen diese Zwischen-Datasets Speicherplatz in Ihrem Speicherkonto in Anspruch. Erwägen Sie das Einrichten von Richtlinien zum Verwalten Ihrer Daten während des gesamten Lebenszyklus, um Ihre Datasets zu archivieren und zu löschen. Weitere Informationen finden Sie unter [Optimieren der Kosten durch Automatisieren der Azure Blob Storage-Zugriffsebenen](../storage/blobs/storage-lifecycle-management-concepts.md).

## <a name="deploy-resources-to-the-same-region"></a>Stellen Sie Ressourcen in derselben Region bereit

Bei Computes, die sich in verschiedenen Regionen befinden, können Netzwerklatenz und erhöhte Datenübertragungskosten auftreten. Azure-Netzwerkkosten entstehen durch die von Azure-Rechenzentren ausgehende Bandbreite. Um die Netzwerkkosten zu senken, stellen Sie alle Ihre Ressourcen in der Region bereit. Die Bereitstellung Ihres Azure Machine Learning-Arbeitsbereichs und der abhängigen Ressourcen in derselben Region wie Ihre Daten kann zur Kostensenkung und Leistungssteigerung beitragen.

Bei Hybrid Cloud-Szenarien wie ExpressRoute kann es manchmal kostengünstiger sein, alle Ressourcen nach Azure zu verschieben, um die Netzwerkkosten und die Wartezeit zu optimieren.

## <a name="next-steps"></a>Nächste Schritte

- [Planen der Verwaltung von Kosten für Azure Machine Learning](concept-plan-manage-cost.md)
- [Verwalten von Budgets, Kosten und Kontingenten für Azure Machine Learning auf Unternehmensebene](/azure/cloud-adoption-framework/ready/azure-best-practices/optimize-ai-machine-learning-cost)