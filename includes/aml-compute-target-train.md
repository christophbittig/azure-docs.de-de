---
title: include file
description: include file
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 06/18/2021
ms.openlocfilehash: e4157e3a80bd0a7ec7eac2399c71bda2d6d03032
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287490"
---
**Computeziele können für mehrere Trainingsaufträge wiederverwendet werden.** Beispielsweise können Sie eine Remote-VM, die Sie an Ihren Arbeitsbereich angefügt haben, für mehrere Aufträge wiederverwenden. Verwenden Sie für Machine Learning-Pipelines den entsprechenden [Pipelineschritt](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) für jedes Computeziel.

Sie können für die meisten Aufträge eine beliebige der folgenden Ressourcen als Trainingscomputeziel verwenden. Allerdings können nicht alle Ressourcen für automatisiertes maschinelles Lernen, Machine Learning-Pipelines oder den Machine Learning-Designer verwendet werden. Azure Databricks kann als Trainingsressource für lokale Ausführungen und Machine Learning-Pipelines verwendet werden, jedoch nicht als Remoteziel für andere Trainings.

|Trainings&nbsp;ziele|[Automatisiertes maschinelles Lernen](../articles/machine-learning/concept-automated-ml.md) | [Machine Learning-Pipelines](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning-Designer](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Lokaler Computer](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Ja | &nbsp; | &nbsp; |
|[Azure Machine Learning Compute-Cluster](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Ja | Ja | Ja |
|[Azure Machine Learning-Computeinstanz](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Ja (über SDK)  | Ja | Ja |
|[Remote-VM](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Ja  | Ja | &nbsp; |
|[Apache Spark-Pools (Vorschauversion)](../articles/machine-learning/how-to-attach-compute-targets.md#synapse)| Ja (nur lokaler SDK-Modus) | Ja | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Ja (nur lokaler SDK-Modus) | Ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Ja | &nbsp; |
|[Azure Kubernetes Service](../articles/machine-learning/how-to-attach-compute-targets.md#kubernetes) (Vorschau) | Ja | Ja | Ja |
|[Kubernetes mit Azure Arc-Unterstützung](../articles/machine-learning/how-to-attach-compute-targets.md#kubernetes) (Vorschau) | Ja | Ja | Ja |

> [!TIP]
> Die Compute-Instanz verfügt über einen 120 GB Betriebssystemdatenträger. Wenn Ihnen der Speicherplatz ausgeht, [verwenden Sie das Terminal](../articles/machine-learning/how-to-access-terminal.md), um mindestens 1–2 GB zu löschen, bevor Sie die Compute-Instanz [beenden oder neu starten](../articles/machine-learning/how-to-create-manage-compute-instance.md#manage).
