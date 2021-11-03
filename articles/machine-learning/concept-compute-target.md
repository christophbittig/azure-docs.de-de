---
title: Was sind Computziele?
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie eine Computeressource oder Umgebung für das Training oder die Bereitstellung Ihres Modells mit Azure Machine Learning bestimmen können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 96f0f011382fb631416665cad58ca7d7324fcb24
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076610"
---
# <a name="what-are-compute-targets-in-azure-machine-learning"></a>Was sind Computeziele in Azure Machine Learning?

Ein *Computeziel* ist eine festgelegte Computeressource oder -Umgebung, in der Ihr Trainingsskript ausgeführt oder Ihre Dienstbereitstellung gehostet wird. Hierbei kann es sich um Ihren lokalen Computer oder eine cloudbasierte Computeressource handeln. Mithilfe von Computezielen können Sie Ihre Compute-Umgebung später problemlos ändern, ohne den Code anpassen zu müssen.

In einem typischen Modellentwicklungslebenszyklus gehen Sie unter Umständen wie folgt vor:

1. Sie entwickeln und experimentieren zunächst mit einer kleinen Datenmenge. In dieser Phase verwenden Sie Ihre lokale Umgebung, z. B. einen lokalen Computer oder einen cloudbasierten virtuellen Computer (VM), als Ihr Computeziel.
1. Skalieren Sie Ihre Umgebung für größere Datenmengen hoch, oder führen Sie ein [verteiltes Training](how-to-train-distributed-gpu.md) mithilfe eines dieser [Trainingscomputeziele](#train) durch.
1. Wenn Ihr Modell bereit ist, stellen Sie es in einer Webhostingumgebung mit einem [dieser Bereitstellungscomputeziele](#deploy) bereit.

Die Computeressourcen, die Sie für Ihre Computeziele verwenden, werden an einen [Arbeitsbereich](concept-workspace.md) angefügt. Andere Computeressourcen als der lokale Computer werden von Benutzern des Arbeitsbereichs gemeinsam genutzt.

## <a name="training-compute-targets"></a><a name="train"></a> Trainieren von Computezielen

Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeziele. Ein typischer Lebenszyklus der Modellentwicklung beginnt mit der Entwicklung oder dem Experimentieren mit einer kleinen Menge von Daten. In dieser Phase verwenden Sie eine lokale Umgebung wie Ihren lokalen Computer oder eine cloudbasierte VM. Wenn Sie Ihr Training zur Verwendung größerer Datasets hochskalieren oder sich für ein [verteiltes Training](how-to-train-distributed-gpu.md) entscheiden, verwenden Sie Azure Machine Learning Compute, um einen Cluster mit einem einzelnen oder mehreren Knoten zu erstellen, der bei jeder Übermittlung einer Ausführung automatisch skaliert wird. Sie können auch Ihre eigene Computeressource anfügen. Die Unterstützung für verschiedene Szenarien kann jedoch variieren.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Erfahren Sie mehr darüber, wie Sie [eine Trainingsausführung an ein Computeziel übermitteln](how-to-set-up-training-targets.md).

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> Computeziele für Rückschlüsse

Beim Durchführen von Rückschlüssen erstellt Azure Machine Learning einen Docker-Container, der das Modell und zugehörige Ressourcen hostet, die für die Verwendung erforderlich sind. Dieser Container wird dann in einem Computeziel verwendet.

[!INCLUDE [aml-deploy-target](../../includes/aml-compute-target-deploy.md)]

Erfahren Sie, [wo und wie Sie Ihr Modell auf einem Computeziel bereitstellen](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning-Computeressource (verwaltet)

Eine verwaltete Computeressource wird von Azure Machine Learning erstellt und verwaltet. Diese Computeressource ist für Machine Learning-Workloads optimiert. Azure Machine Learning-Computecluster und -[Compute-Instanzen](concept-compute-instance.md) sind die einzigen verwalteten Computeressourcen.

Sie können Azure Machine Learning-Compute-Instanzen oder -Computecluster erstellen mithilfe von:

* [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md)
* Das Python SDK und die Azure CLI:
    * [Compute-Instanz](how-to-create-manage-compute-instance.md)
    * [Computecluster](how-to-create-attach-compute-cluster.md)
* Azure Resource Manager-Vorlage Eine Beispielvorlage finden Sie unter [Erstellen eines Azure Machine Learning-Computeclusters](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-amlcompute).
* Eine [Machine-Learning-Erweiterung für die Azure CLI](reference-azure-machine-learning-cli.md#resource-management)

Nach der Erstellung sind diese Computeressourcen im Gegensatz zu anderen Arten von Computezielen automatisch Teil Ihres Arbeitsbereichs.


|Funktion  |Computecluster  |Compute-Instanz  |
|---------|---------|---------|
|Cluster mit einem oder mehreren Knoten     |    **&check;**       |    Einzelknotencluster     |
|Automatische Skalierung bei jedem Übermitteln einer Ausführung     |     **&check;**      |         |
|Automatische Clusterverwaltung und Auftragsplanung     |   **&check;**        |     **&check;**      |
|Unterstützt CPU- und GPU-Ressourcen     |  **&check;**         |    **&check;**       |


> [!NOTE]
> Wenn sich ein *Computecluster* im Leerlauf befindet, wird er automatisch auf 0 Knoten skaliert. Es entstehen also keine Kosten, wenn der Cluster nicht verwendet wird. Eine *Compute-Instanz* ist immer aktiviert und wird nicht automatisch skaliert. Sie sollten [Compute-Instanzen anhalten](how-to-create-manage-compute-instance.md#manage), wenn Sie sie nicht verwenden, um unnötige Kosten zu vermeiden.

### <a name="supported-vm-series-and-sizes"></a>Unterstützte VM-Serien und -Größen

Wenn Sie eine Knotengröße für eine verwaltete Computeressource in Azure Machine Learning auswählen, können Sie unter den in Azure verfügbaren VM-Größen auswählen. Azure bietet eine Reihe von Größen für Linux und Windows für verschiedene Workloads. Weitere Informationen finden Sie unter [VM-Typen und -Größen](../virtual-machines/sizes.md).

Bei der Auswahl einer VM-Größe gelten einige Ausnahmen und Einschränkungen:

* Einige VM-Serien werden in Azure Machine Learning nicht unterstützt.
* Einige VM-Serien sind eingeschränkt. Um eine eingeschränkte Serie zu verwenden, wenden Sie sich an den Support, und fordern Sie eine Kontingenterhöhung für die Serie an. Informationen zur Kontaktaufnahme mit dem Support finden Sie unter [Azure-Supportoptionen](https://azure.microsoft.com/support/options/).

In der folgenden Tabelle finden Sie weitere Informationen zu unterstützten Serien und Einschränkungen.

| **Unterstützte VM-Serie**  | **Einschränkungen** | **Kategorie** | **Unterstützt von** |
|------------|------------|------------|------------|
| [DDSv4](../virtual-machines/ddv4-ddsv4-series.md#ddsv4-series) | Keine. | Allgemeiner Zweck | Computecluster und -instanzen |
| [Dv2](../virtual-machines/dv2-dsv2-series.md#dv2-series) | Keine. | Allgemeiner Zweck | Computecluster und -instanzen |
| [Dv3](../virtual-machines/dv3-dsv3-series.md#dv3-series) | Keine.| Allgemeiner Zweck | Computecluster und -instanzen |
| [DSv2](../virtual-machines/dv2-dsv2-series.md#dsv2-series) | Keine. | Allgemeiner Zweck | Computecluster und -instanzen |
| [DSv3](../virtual-machines/dv3-dsv3-series.md#dsv3-series) | Keine.| Allgemeiner Zweck | Computecluster und -instanzen |
| [EAv4](../virtual-machines/eav4-easv4-series.md) | Keine. | Arbeitsspeicheroptimiert | Computecluster und -instanzen |
| [Ev3](../virtual-machines/ev3-esv3-series.md) | Keine. | Arbeitsspeicheroptimiert | Computecluster und -instanzen |
| [FSv2](../virtual-machines/fsv2-series.md) | Keine. | Computeoptimiert | Computecluster und -instanzen |
| [FX](../virtual-machines/fx-series.md) | Genehmigung erforderlich. | Für Compute optimiert | Computecluster |
| [H](../virtual-machines/h-series.md) | Keine. | High Performance Computing | Computecluster und -instanzen |
| [HB](../virtual-machines/hb-series.md) | Genehmigung erforderlich. | High Performance Computing | Computecluster und -instanzen |
| [HBv2](../virtual-machines/hbv2-series.md) | Genehmigung erforderlich. |  High Performance Computing | Computecluster und -instanzen |
| [HBv3](../virtual-machines/hbv3-series.md) | Genehmigung erforderlich. |  High Performance Computing | Computecluster und -instanzen |
| [HC](../virtual-machines/hc-series.md) | Genehmigung erforderlich. |  High Performance Computing | Computecluster und -instanzen |
| [LSv2](../virtual-machines/lsv2-series.md) | Keine. |  Speicheroptimiert | Computecluster und -instanzen |
| [M](../virtual-machines/m-series.md) | Genehmigung erforderlich. | Arbeitsspeicheroptimiert | Computecluster und -instanzen |
| [NC](../virtual-machines/nc-series.md) | Keine. |  GPU | Computecluster und -instanzen |
| [NC Promo](../virtual-machines/nc-series.md) | Keine. | GPU | Computecluster und -instanzen |
| [NCv2](../virtual-machines/ncv2-series.md) | Genehmigung erforderlich. | GPU | Computecluster und -instanzen |
| [NCv3](../virtual-machines/ncv3-series.md) | Genehmigung erforderlich. | GPU | Computecluster und -instanzen |
| [ND](../virtual-machines/nd-series.md) | Genehmigung erforderlich. | GPU | Computecluster und -instanzen |
| [NDv2](../virtual-machines/ndv2-series.md) | Genehmigung erforderlich. | GPU | Computecluster und -instanzen |
| [SH](../virtual-machines/nv-series.md) | Keine. | GPU | Computecluster und -instanzen |
| [NVv3](../virtual-machines/nvv3-series.md) | Genehmigung erforderlich. | GPU | Computecluster und -instanzen |
| [NCasT4_v3](../virtual-machines/nct4-v3-series.md) | Genehmigung erforderlich. | GPU | Computecluster und -instanzen |
| [NDasrA100_v4](../virtual-machines/nda100-v4-series.md) | Genehmigung erforderlich. | GPU | Computecluster und -instanzen |


Obwohl Azure Machine Learning diese VM-Serien unterstützt, sind sie möglicherweise nicht in allen Azure-Regionen verfügbar. Informationen zum Überprüfen, ob VM-Serien verfügbar sind, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

> [!NOTE]
> Azure Machine Learning unterstützt nicht alle VM-Größen, die von Azure Compute unterstützt werden. Um alle verfügbaren VM-Größen aufzulisten, verwenden Sie eine der folgenden Methoden:
> * [REST-API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable/2020-08-01/examples/ListVMSizesResult.json)
> * [Python SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
>

Bei Verwendung der GPU-fähigen Computeziele ist es wichtig sicherzustellen, dass die richtigen CUDA-Treiber in der Trainingsumgebung installiert sind. Bestimmen Sie anhand der folgenden Tabelle die richtige CUDA-Version, die verwendet werden soll:

| **GPU-Architektur**  | **Azure-VM-Serie** | **Unterstützte CUDA-Versionen** |
|------------|------------|------------|
| Ampere | NDA100_v4 | 11.0+ |
| Turing | NCT4_v3 | 10.0 und höher |
| Volta | NCv3, NDv2 | 9.0+ |
| Pascal | NCv2, ND | 9.0+ |
| Maxwell | NV, NVv3 | 9.0+ |
| Kepler | NC, NC Promo| 9.0+ |

Stellen Sie nicht nur sicher, dass die CUDA-Version und die Hardware kompatibel sind, sondern auch, dass die CUDA-Version mit der Version des verwendeten Machine Learning-Frameworks kompatibel ist: 

- Für PyTorch können Sie die Kompatibilität [hier](https://pytorch.org/get-started/previous-versions/) überprüfen. 
- Für Tensorflow können Sie die Kompatibilität [hier](https://www.tensorflow.org/install/source#gpu) überprüfen.

### <a name="compute-isolation"></a>Computeisolation

Azure Machine Learning Compute bietet VM-Größen, die für einen bestimmten Hardwaretyp isoliert und für einen einzelnen Kunden bestimmt sind. Die Größen isolierter VMs eignen sich am besten für Workloads, die ein hohes Maß an Isolation von den Workloads anderer Kunden erfordern, beispielsweise um Compliance zu erzielen und gesetzliche Anforderungen zu erfüllen. Durch die Verwendung isolierter Größen wird sichergestellt, dass Ihre VM als einzige in der jeweiligen Serverinstanz ausgeführt wird.

Die aktuellen Angebote für isolierte VMs umfassen Folgendes:

* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3*

*RDMA-fähig

Weitere Informationen zur Isolation finden Sie unter [Isolation in der öffentlichen Azure-Cloud](../security/fundamentals/isolation-choices.md).

## <a name="unmanaged-compute"></a>Nicht verwaltete Computeressourcen

Ein nicht verwaltetes Computeziel wird *nicht* von Azure Machine Learning verwaltet. Sie erstellen diese Art von Computeziel außerhalb von Azure Machine Learning und fügen es anschließend an Ihren Arbeitsbereich an. Für nicht verwaltete Computeressourcen sind möglicherweise weitere Schritte erforderlich, um die Leistung von Machine Learning-Workloads beizubehalten oder zu verbessern. 

Azure Machine Learning unterstützt die folgenden nicht verwalteten Computetypen:

* Ihr lokaler Computer
* Virtuelle Remotecomputer
* Azure HDInsight
* Azure Batch
* Azure Databricks
* Azure Data Lake Analytics
* Azure Container Instances
* Azure Kubernetes Service & Azure Arc-aktiviertes Kubernetes (Vorschau)

Weitere Informationen finden Sie unter [Einrichten von Computezielen für das Trainieren und Bereitstellen von Modellen](how-to-attach-compute-targets.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden folgende Themen erläutert:
* [Einrichten und Verwenden von Computezielen für das Modelltraining](how-to-set-up-training-targets.md)
* [Bereitstellen Ihres Modells auf einem Computeziel](how-to-deploy-and-where.md)
