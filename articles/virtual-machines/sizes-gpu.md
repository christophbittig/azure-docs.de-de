---
title: 'Azure-VM-Größen: GPU | Microsoft-Dokumentation'
description: Liste der verschiedenen verfügbaren GPU-optimierten Größen für virtuelle Computer in Azure Dieser Artikel listet Informationen zur Anzahl von vCPUs, Datenträgern und Netzwerkschnittstellenkarten sowie zum Speicherdurchsatz und zur Netzwerkbandbreite für Größen dieser Serie auf.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 8a39809cf787237a38d5246c32e481fb82ac2629
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474551"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Für GPU optimierte VM-Größen

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

> [!TIP]
> Probieren Sie das **[Auswahltool für virtuelle Computer](https://aka.ms/vm-selector)** aus, um andere Größen zu ermitteln, die für Ihre Workload optimal sind.

GPU-optimierte VM-Größen sind für spezialisierte virtuelle Computer mit einzelnen, mehreren oder Teil-GPUs verfügbar. Diese Größen sind für rechenintensive, grafikintensive und visualisierungsorientierte Workloads vorgesehen. Dieser Artikel enthält Informationen über die Anzahlen und Typen von GPUs, vCPUs, Datenträgern und NICs. Der Speicherdurchsatz und die Netzwerkbandbreite sind für die jeweiligen Größen in dieser Gruppe ebenfalls enthalten.

- Die Größen der [NCv3-](ncv3-series.md) und [NC T4_v3-Reihe](nct4-v3-series.md) sind für computeintensive GPU-beschleunigte Anwendungen optimiert. Einige Beispiele sind CUDA- und OpenCL-basierte Anwendungen und Simulationen, KI und Deep Learning. Die NC T4 v3-Reihe ist speziell auf Rückschlussworkloads mit der Tesla T4-GPU von NVIDIA und dem AMD EPYC2 Rome-Prozessor ausgelegt. Die NCv3-Reihe ist außerdem für Hochleistungscomputing- und entsprechende KI-Workloads konzipiert, die mit der Tesla V100-GPU von NVIDIA ausgestattet sind.

- Der Fokus der Größe der [ND A100 v4-Reihe](nda100-v4-series.md) liegt auf dem Hochskalieren und Aufskalieren von Deep Learning- Trainingsanwendungen und beschleunigten HPC-Anwendungen. Die ND A100 v4-Serie verwendet acht NVIDIA A100 TensorCore-GPUs, die jeweils mit einer 200 Gigabit Mellanox InfiniBand-HDR-Verbindung und 40 GB GPU-Arbeitsspeicher verfügbar sind.

- Die Größen der [NV-Serie](nv-series.md) und der [NVv3-Serie](nvv3-series.md) sind für Remotevisualisierung, Streaming, Spiele, Codierung und VDI-Szenarien mit Frameworks wie OpenGL und DirectX konzipiert und optimiert. Diese VMs werden von der NVIDIA Tesla M60-GPU unterstützt.

- VM-Größen der [NVv4-Serie](nvv4-series.md) sind für VDI und Remotevisualisierung konzipiert und optimiert. Dank partitionierter GPUs bietet NVv4 die passende Größe für Workloads, die geringere GPU-Ressourcen erfordern. Diese virtuellen Computer basieren auf der AMD Radeon Instinct MI25-GPU. VMs der NVv4-Serie unterstützen derzeit nur das Windows-Gastbetriebssystem.

- Der virtuelle Computer der [NDm A100 v4-Serie](ndm-a100-v4-series.md) ist ein neues Flaggschiff der Azure-GPU-Familie, die für hoch anspruchsvolles Deep Learning-Training und eng gekoppelte, vertikal und horizontal skalierende HPC-Workloads konzipiert ist. Die NDm A100 v4-Serie beginnt mit einem einzelnen virtuellen Computer (VM) und acht NVIDIA Ampere A100 Tensor Core-GPUs mit 80 GB.


## <a name="supported-operating-systems-and-drivers"></a>Unterstützte Betriebssysteme und Treiber

Um die GPU-Funktionen von Azure-VMs der N-Serie nutzen zu können, müssen NVIDIA- oder AMD-GPU-Treiber installiert sein.

- Bei VMs mit NVIDIA-GPUs werden mit der [NVIDIA-GPU-Treibererweiterung](./extensions/hpccompute-gpu-windows.md) die entsprechenden NVIDIA-CUDA- oder -GRID-Treiber installiert. Installieren oder verwalten Sie die Erweiterung mithilfe des Azure-Portals oder mit Tools wie Azure PowerShell oder Azure Resource Manager-Vorlagen. Informationen zu unterstützten Betriebssystemen und Bereitstellungsschritten finden Sie in der [Dokumentation zur NVIDIA-GPU-Treibererweiterung](./extensions/hpccompute-gpu-windows.md). Allgemeine Informationen zu VM-Erweiterungen finden Sie unter [Erweiterungen und Features für virtuelle Azure-Computer](./extensions/overview.md).

   Alternativ dazu können Sie NVIDIA-GPU-Treiber manuell installieren. Informationen zu unterstützten Betriebssystemen, Treibern sowie Installations- und Überprüfungsschritten finden Sie unter [Installieren von NVIDIA GPU-Treibern für virtuelle Computer der Serie N mit Windows](./windows/n-series-driver-setup.md) bzw. [Installieren von NVIDIA GPU-Treibern für virtuelle Computer der Serie N mit Linux](./linux/n-series-driver-setup.md).

- Für VMS, die von AMD-GPUs unterstützt werden, installiert die [AMD-GPU-Treiber-Erweiterung](./extensions/hpccompute-amd-gpu-windows.md) geeignete AMD-Treiber. Installieren oder verwalten Sie die Erweiterung mithilfe des Azure-Portals oder mit Tools wie Azure PowerShell oder Azure Resource Manager-Vorlagen. Allgemeine Informationen zu VM-Erweiterungen finden Sie unter [Erweiterungen und Features für virtuelle Azure-Computer](./extensions/overview.md).

   Alternativ dazu können Sie NVIDIA-GPU-Treiber manuell installieren. Informationen finden Sie unter [Installieren von AMD-GPU-Treibern für virtuelle Computer der N-Serie unter Windows](./windows/n-series-amd-driver-setup.md).

## <a name="deployment-considerations"></a>Überlegungen zur Bereitstellung

- Informationen zur Verfügbarkeit von virtuellen Computern der N-Serie finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services/).

- Virtuelle Computer der N-Serie können nur im Resourcen Manager-Bereitstellungsmodell bereitgestellt werden.

- Virtuelle Computer der Serie N unterscheiden hinsichtlich des Typs von Azure Storage, den sie für ihre Datenträger unterstützen. Virtuelle NC- oder NV-Computer unterstützen nur VM-Datenträger, die durch Standard Disk Storage (HDD) gesichert sind. Alle anderen GPU-VMs unterstützen VM-Datenträger, die von Standard Disk Storage und Disk Storage Premium (SSD) unterstützt werden.

- Wenn Sie eine größere Anzahl von virtuellen Computern der N-Serie bereitstellen möchten, sollten Sie ein nutzungsbasiertes Abonnement oder andere Kaufoptionen in Erwägung ziehen. Bei Verwendung eines [kostenlosen Azure-Kontos](https://azure.microsoft.com/free/)können Sie nur eine begrenzte Anzahl von Azure-Compute-Kernen nutzen.

- Möglicherweise müssen Sie das Kernnutzungskontingent (pro Region) in Ihrem Azure-Abonnement sowie das separate Kontingent für NC-, NCv2-, NCv3-, ND-, NDv2-, NV- oder NVv2-Kerne erhöhen. In diesem Fall können Sie kostenlos [eine Anfrage an den Onlinekundensupport richten](../azure-portal/supportability/how-to-create-azure-support-request.md) und eine Erhöhung des Kontingents anfordern. Standardgrenzwerte variieren unter Umständen je nach Abonnementkategorie.

## <a name="other-sizes"></a>Andere Größen

- [Allgemeiner Zweck](sizes-general.md)
- [Computeoptimiert](sizes-compute.md)
- [High Performance Computing](sizes-hpc.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [Vorherige Generationen](sizes-previous-gen.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.
