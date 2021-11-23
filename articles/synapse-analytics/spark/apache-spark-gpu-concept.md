---
title: Pools mit GPU-Beschleunigung
description: Einführung in GPUs in Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/10/2021
ms.author: midesa
ms.openlocfilehash: d4a4c38b61dcce01ec7fb73dc70605ce795b6412
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495532"
---
# <a name="gpu-accelerated-apache-spark-pools-in-azure-synapse-analytics"></a>Apache Spark-Pools mit GPU-Beschleunigung in Azure Synapse Analytics
Azure Synapse Analytics unterstützt jetzt Apache Spark-Pools mit Beschleunigung durch Grafikprozessoren (Graphics Processing Units, GPUs). 

Durch die Verwendung von NVIDIA-GPUs können wissenschaftliche und technische Fachkräfte für Daten unter anderem die Ausführung von Datenintegrationspipelines und die Bewertung von Machine Learning-Modellen beschleunigen. In diesem Artikel erfahren Sie, wie Sie Pools mit GPU-Beschleunigung erstellen und mit Azure Synapse Analytics verwenden können. Außerdem finden Sie hier Informationen zu den GPU-Treibern und Bibliotheken, die im Rahmen der GPU-beschleunigten Runtime vorinstalliert sind.

> [!NOTE]
> Azure Synapse GPU-fähige Pools befinden sich derzeit in Public Preview.

## <a name="create-a-gpu-accelerated-pool"></a>Erstellen eines Pools mit GPU-Beschleunigung
Azure Synapse nimmt Ihnen die Installation untergeordneter Bibliotheken und die Einrichtung aller komplexen Netzwerkanforderungen zwischen Computeknoten ab, um die Erstellung und Verwaltung von Pools zu vereinfachen. Dank dieser Integration können Benutzer innerhalb weniger Minuten mit der Verwendung von Pools mit GPU-Beschleunigung beginnen. Weitere Informationen zum Erstellen eines Pools mit GPU-Beschleunigung finden Sie in [dieser Schnellstartanleitung](../quickstart-create-apache-gpu-pool-portal.md).

> [!NOTE]
>  - Pools mit GPU-Beschleunigung können in Arbeitsbereichen in den Regionen „USA, Osten“, „Australien, Osten“ und „Europa, Norden“ erstellt werden.
>  - Pools mit GPU-Beschleunigung sind nur mit der Apache Spark 3-Runtime verfügbar.
>  - Unter Umständen ist eine [Erhöhung des Grenzwerts](./apache-spark-rapids-gpu.md#quotas-and-resource-constraints-in-azure-synapse-gpu-enabled-pools) erforderlich, um Cluster mit GPU-Unterstützung erstellen zu können. 
 
## <a name="gpu-accelerated-runtime"></a>GPU-beschleunigte Runtime

### <a name="nvidia-gpu-driver-cuda-and-cudnn"></a>NVIDIA-GPU-Treiber, CUDA und cuDNN
Azure Synapse Analytics bietet jetzt Apache Spark-Pools mit GPU-Beschleunigung, die verschiedene NVIDIA-Bibliotheken und -Konfigurationen enthalten. Standardmäßig werden von Azure Synapse Analytics der NVIDIA-Treiber und die Bibliotheken installiert, die für die Verwendung von GPUs in Spark-Treiber- und -Workerinstanzen erforderlich sind:
 - CUDA 11.2
 - libnccl2=2.8.4
 - libnccl-dev=2.8.4
 - libcudnn8=8.1.1 
 - libcudnn8-dev=8.1.1

> [!NOTE]
> Diese Software enthält Quellcode, der von der NVIDIA Corporation bereitgestellt wird. Zur Unterstützung der Pools mit GPU-Beschleunigung enthalten Azure Synapse Apache-Spark-Pools insbesondere Code aus [CUDA-Beispielen](https://docs.nvidia.com/cuda/eula/#nvidia-cuda-samples-preface).

### <a name="nvidia-end-user-license-agreement-eula"></a>Software-Lizenzbedingungen von NVIDIA
Wenn Sie in Synapse Spark eine Hardwareoption mit GPU-Beschleunigung auswählen, stimmen Sie implizit den Bestimmungen aus den Software-Lizenzbedingungen von NVIDIA in Bezug auf Folgendes zu:
  - CUDA 11.2: [Software-Lizenzbedingungen: Dokumentation zum CUDA-Toolkit (nvidia.com)](https://docs.nvidia.com/cuda/eula/index.html)
  - libnccl2=2.8.4: [„nccl/LICENSE.txt“ unter „master“ > „NVIDIA/nccl“ (github.com)](https://github.com/NVIDIA/nccl/blob/master/LICENSE.txt)
  - libnccl-dev=2.8.4: [„nccl/LICENSE.txt“ unter „master“ > „NVIDIA/nccl“ (github.com)](https://github.com/NVIDIA/nccl/blob/master/LICENSE.txt)
  - libcudnn8=8.1.1: [Softwarelizenzvereinbarung: Dokumentation zu NVIDIA cuDNN (Deep Learning)](https://docs.nvidia.com/deeplearning/cudnn/sla/index.html)
  - libcudnn8-dev=8.1.1: [Softwarelizenzvereinbarung: Dokumentation zu NVIDIA cuDNN (Deep Learning)](https://docs.nvidia.com/deeplearning/cudnn/sla/index.html)
  - Die CUDA-, NCCL- und cuDNN-Bibliotheken und die [Software-Lizenzbedingungen von NVIDIA (mit NCCL-Zusatz)](https://docs.nvidia.com/deeplearning/nccl/sla/index.html#overview) für die NCCL-Bibliothek

## <a name="accelerate-etl-workloads"></a>Beschleunigen von ETL-Workloads
Mit der integrierten Unterstützung des [RAPIDS-Accelerators für Apache Spark](https://nvidia.github.io/spark-rapids/) von NVIDIA lassen sich durch Spark-Pools mit GPU-Beschleunigung in Azure Synapse im Vergleich zu analytischen Standardbenchmarks ganz ohne Codeänderungen erhebliche Leistungsverbesserungen erreichen. Nvidia RAPIDS basiert auf NVIDIA CUDA und UCX und ermöglicht SQL, DataFrame-Vorgänge und Spark-Shuffle-Vorgänge mit GPU-Beschleunigung. Da diese Beschleunigungen ganz ohne Codeänderungen genutzt werden können, können Benutzer auch Datenpipelines beschleunigen, die auf Delta Lake der Linux Foundation oder auf der Hyperspace-Indizierung von Microsoft basieren. 

Weitere Informationen zur Verwendung des NVIDIA RAPIDS-Accelerators mit Ihrem GPU-beschleunigten Pool in Azure Synapse Analytics finden Sie in [diesem Leitfaden zur Verbesserung der Leistung mit RAPIDS](apache-spark-rapids-gpu.md).

## <a name="improve-machine-learning-scoring-workloads"></a>Verbessern von Machine Learning-Bewertungsworkloads
Viele Organisationen verwenden umfangreiche Batchbewertungsaufträge, die häufig in engen Zeitfenstern ausgeführt werden. Zur Verbesserung von Batchbewertungsaufträgen können GPU-beschleunigte Spark-Pools mit der [Hummingbird-Bibliothek](https://github.com/Microsoft/hummingbird) von Microsoft verwendet werden. Mit Hummingbird können Benutzer ihre herkömmlichen, baumstrukturbasierten ML-Modelle in Tensorberechnungen kompilieren. Anschließend können Benutzer mithilfe von Hummingbird nahtlos native Hardwarebeschleunigung und neuronale Netzwerkframeworks nutzen, um ihre ML-Modellbewertung zu beschleunigen, ohne ihre Modelle umschreiben zu müssen.  

## <a name="next-steps"></a>Nächste Schritte
- [Azure Synapse Analytics](../overview-what-is.md)
