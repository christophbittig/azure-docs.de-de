---
title: High Performance Computing auf InfiniBand-fähigen virtuellen Computern der H- und N-Serie – Azure Virtual Machines
description: Hier finden Sie Informationen zu den Features und Funktionen InfiniBand-fähiger virtueller Computer der H- und N-Serie, die für HPC optimiert sind.
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: overview
ms.date: 04/09/2021
ms.reviewer: cynthn
ms.openlocfilehash: 2b9f89126f49b75f5e232521f807f6db3ba9d4e9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693388"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>High Performance Computing auf InfiniBand-fähigen virtuellen Computern der H- und N-Serie

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

InfiniBand-fähige virtuelle Computer der H- und N-Serie von Azure bieten eine herausragende Leistung, MPI-Skalierbarkeit (Message Passing Interface) und Kosteneffizienz für verschiedenste HPC- und KI-Workloads in der Praxis. Diese für High Performance Computing (HPC) optimierten virtuellen Computer werden verwendet, um besonders rechenintensive Probleme in Wissenschaft und Technik zu lösen. Hierzu zählen beispielsweise Strömungssimulationen, Erdmodellierung und Wettersimulationen.

In diesen Artikeln werden die ersten Schritte mit den InfiniBand-fähigen virtuellen Computern der H- und N-Serie in Azure sowie die optimale Konfiguration der HPC- und KI-Workloads auf den virtuellen Computern beschrieben, um Skalierbarkeit zu gewährleisten.

## <a name="features-and-capabilities"></a>Features und Funktionen

Die InfiniBand-fähigen virtuellen Computer der H- und N-Serie sind so konzipiert, dass sie die beste HPC-Leistung, MPI-Skalierbarkeit und Kosteneffizienz für HPC-Workloads bieten. Weitere Informationen zu den Features und Funktionen der virtuellen Computer finden Sie [hier](../../sizes-hpc.md) (H-Serie) bzw. [hier](../../sizes-gpu.md) (N-Serie).

### <a name="rdma-and-infiniband"></a>RDMA und InfiniBand

[RDMA-fähige](../../sizes-hpc.md#rdma-capable-instances) virtuelle Computer der [H-Serie](../../sizes-hpc.md) und der [N-Serie](../../sizes-gpu.md) kommunizieren über das InfiniBand-Netzwerk, das sich durch seine geringe Wartezeit und seine hohe Bandbreite auszeichnet. Die RDMA-Funktion über eine solche Verbindung ist entscheidend, um die Skalierbarkeit und Leistung von HPC- und KI-Workloads mit verteilten Knoten zu steigern. Die InfiniBand-fähigen virtuellen Computer der H- und N-Serie sind über einen blockierungsfreien Fat Tree mit einem Design mit geringem Durchmesser verbunden, um eine optimierte und konsistente RDMA-Leistung zu erzielen.
Weitere Informationen zur Einrichtung von InfiniBand auf den InfiniBand-fähigen virtuellen Computern finden Sie unter [Aktivieren von InfiniBand](enable-infiniband.md).

### <a name="message-passing-interface"></a>Message Passing Interface

Von den SR-IOV-fähigen virtuellen Computern der H- und N-Serie werden nahezu alle MPI-Bibliotheken und -Versionen unterstützt. Einige der am häufigsten verwendeten MPI-Bibliotheken: Intel MPI, OpenMPI, HPC-X, MVAPICH2, MPICH, Platform MPI. Alle Verben für den Remotezugriff auf den direkten Speicher (Remote Direct Memory Access, RDMA) werden unterstützt.
Weitere Informationen zur Installation verschiedener unterstützter MPI-Bibliotheken sowie zur optimalen Konfiguration finden Sie unter [Einrichten von Message Passing Interface für HPC](setup-mpi.md).

## <a name="get-started"></a>Erste Schritte

Der erste Schritt besteht darin, auf der Grundlage der VM-Spezifikationen und der [RDMA-Fähigkeit](../../sizes-hpc.md#rdma-capable-instances) den optimalen VM-Typ der [H-Serie](../../sizes-hpc.md)/[N-Serie](../../sizes-gpu.md) für die Workload auszuwählen.
Konfigurieren Sie anschließend den virtuellen Computer, indem Sie InfiniBand aktivieren. Hierfür gibt es verschiedene Methoden – beispielsweise die Verwendung optimierter VM-Images mit integrierten Treibern. Ausführliche Informationen finden Sie unter [Konfigurieren und Optimieren virtueller Computer](configure.md) sowie unter [Aktivieren von InfiniBand](enable-infiniband.md).
Drittens: Für verteilte Knotenworkloads muss unbedingt MPI ausgewählt und ordnungsgemäß konfiguriert werden. Weitere Informationen finden Sie unter [Einrichten von Message Passing Interface für HPC](setup-mpi.md).
Viertens: Konfigurieren Sie die Workloads optimal, wie im spezifischen Leitfaden für die VM-Familie beschrieben, um die bestmögliche Leistung und Skalierbarkeit zu erzielen. Entsprechende Informationen finden Sie beispielsweise in der [Übersicht über virtuelle Computer der HBv3-Serie](hbv3-series-overview.md) sowie in der [Übersicht über virtuelle Computer der HC-Serie](hc-series-overview.md).

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über das [Konfigurieren und Optimieren](configure.md) der InfiniBand-fähigen virtuellen Computer der [H-Serie](../../sizes-hpc.md) und der [N-Serie](../../sizes-gpu.md).
- Sehen Sie sich die [Übersicht über virtuelle Computer der HBv3-Serie](hb-series-overview.md) und die [Übersicht über virtuelle Computer der HC-Serie](hc-series-overview.md) an, um zu erfahren, wie Sie Workloads optimal konfigurieren, um die bestmögliche Leistung und Skalierbarkeit zu erzielen.
- Informieren Sie sich über die neuesten Ankündigungen, HPC-Workloadbeispiele und Leistungsergebnisse in den [Tech Community-Blogs zu Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Testen Sie Ihr Wissen mit einem [Lernmodul zur Optimierung von HPC-Anwendungen in Azure](/learn/modules/optimize-tightly-coupled-hpc-apps/).
- Eine allgemeinere Übersicht über die Architektur für die Ausführung von HPC-Workloads finden Sie unter [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).