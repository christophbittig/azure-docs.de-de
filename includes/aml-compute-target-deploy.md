---
title: Datei einfügen
description: include file
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/21/2021
ms.openlocfilehash: 75b0a6d6f1cf00cab533c1e3d147634dbdb3b55c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076609"
---
Das Computeziel, das Sie zum Hosten Ihres Modells verwenden, wirkt sich auf die Kosten und Verfügbarkeit des bereitgestellten Endpunkts aus. Verwenden Sie die folgende Tabelle, um ein geeignetes Computeziel auszuwählen:

| Computeziel | Syntaxelemente | GPU-Unterstützung | FPGA-Unterstützung | BESCHREIBUNG |
| ----- | ----- | ----- | ----- | ----- |
| [Lokaler&nbsp;&nbsp;Webservice](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Testen/Debuggen | &nbsp; | &nbsp; | Für eingeschränkte Tests und Problembehandlung verwenden. Die Hardwarebeschleunigung hängt von der Verwendung von Bibliotheken im lokalen System ab.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Echtzeitrückschluss <br/><br/> Für Produktionsworkloads nicht empfohlen. |  [Ja](../articles/machine-learning/how-to-deploy-with-triton.md) (Webdienstbereitstellung) | [Ja](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Für hochgradig skalierbare Produktionsbereitstellungen verwenden. Bietet schnelle Antwortzeiten und die automatische Skalierung von bereitgestellten Diensten. Die automatische Skalierung von Clustern wird vom Azure Machine Learning SDK nicht unterstützt. Die Knoten in Ihrem AKS-Cluster können Sie über die entsprechende Benutzeroberfläche im Azure-Portal ändern. <br/><br/> Wird im Designer unterstützt. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Echtzeitrückschluss <br/><br/> Nur empfohlen für Entwicklungs-/Testzwecke.| &nbsp;  | &nbsp; | Für CPU-lastige Workloads im kleinen Maßstab verwenden, die weniger als 48 GB Arbeitsspeicher erfordern. Sie müssen keinen Cluster verwalten. <br/><br/> Wird im Designer unterstützt. |
| [Azure Machine Learning-Computecluster](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) | Batchrückschluss&nbsp; | [Ja](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) (Machine Learning-Pipeline) | &nbsp;  | Ausführen von Batchbewertungen auf serverlosen Computezielen. Unterstützt virtuelle Computer mit normaler und niedriger Priorität. Keine Unterstützung für Echtzeitrückschlüsse|
| [Kubernetes mit Azure Arc-Aktivierung](/azure/machine-learning/how-to-attach-compute-targets) | Echtzeitrückschluss <br/><br/> Batchrückschluss | Ja | – | Ausführen von Rückschlussworkloads in lokalen, cloudbasierten und Edge Kubernetes-Clustern, die in Azure Arc verwaltet werden |  

> [!NOTE]
> Zwar unterstützen Computeziele wie „Lokal“ und „Azure Machine Learning-Computecluster“ die GPU für Training und Experimente, jedoch wird die Verwendung von GPUs für Rückschlüsse nur _bei Bereitstellung als Webdienst_ in AKS unterstützt.
>
> Das Verwenden einer GPU für Rückschlüsse _bei der Bewertung mit einer Machine Learning-Pipeline_ wird nur in Azure Machine Learning Compute unterstützt.
> 
> Bei der Auswahl einer Cluster-SKU müssen Sie zuerst hochskalieren und dann aufskalieren. Beginnen Sie mit einem Computer, der über 150 % des für Ihr Modell erforderlichen RAM verfügt, erstellen Sie ein Profil für das Ergebnis, und suchen Sie nach einem Computer mit der benötigten Leistung. Nachdem Sie sich damit vertraut gemacht haben, erhöhen Sie die Anzahl der Computer, um Ihren Anforderungen an gleichzeitige Rückschlüsse zu genügen.

> [!NOTE]
> * Containerinstanzen eignen sich nur für kleine Modelle mit einer Größe von unter 1 GB.
> * Verwenden Sie AKS-Cluster mit einem Knoten für Dev/Test von größeren Modellen.