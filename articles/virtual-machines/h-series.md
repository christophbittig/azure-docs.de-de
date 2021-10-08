---
title: 'H-Serie: Azure Virtual Machines'
description: Spezifikationen für die VMs der H-Serie
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 09/11/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 75e3dc21c5cb5e8590b63c3382260331bb0bd844
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754641"
---
# <a name="h-series"></a>H-Reihe

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Virtuelle Computer der H-Serie sind für Anwendungen optimiert, für die Anforderungen hinsichtlich hoher CPU-Frequenzen oder sehr viel Arbeitsspeicher pro Kern gelten. Virtuelle Computer der H-Serie bieten 8 oder 16 Intel-Prozessorkerne des Modells Xeon E5 2667 v3, bis zu 14 GB RAM pro CPU-Kern und kein Hyperthreading. Die H-Serie unterstützt Mellanox EDR InfiniBand mit 56 GBit/s in einer FAT-Strukturkonfiguration ohne Blocks für einheitliche RDMA-Leistung. Virtuelle Computer der H-Serie sind derzeit nicht SR-IOV-fähig und unterstützen Intel MPI 5.x und MS-MPI.

[ACU](acu.md): 290-300<br>
[Storage Premium](premium-storage-performance.md): Nicht unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Nicht unterstützt<br>
[Livemigration:](maintenance-and-updates.md) Nicht unterstützt<br>
[Updates mit Speicherbeibehaltung:](maintenance-and-updates.md) Nicht unterstützt<br>
[Unterstützung von VM-Generationen](generation-2.md): Generation 1<br>
[Beschleunigter Netzwerkbetrieb:](../virtual-network/create-vm-accelerated-networking-cli.md) Nicht unterstützt<br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Nicht unterstützt <br>
<br>

| Size | vCPU | Prozessor | Arbeitsspeicher (GiB) | Speicherbandbreite GB/s | Basis-CPU-Frequenz (GHz) | Frequenz für alle Kerne (GHz, Spitze) | Frequenz für Einzelkern (GHz, Spitze) | RDMA-Leistung (Gbit/s) | MPI-Unterstützung | Temporärer Speicher (GiB) | Max. Anzahl Datenträger | Max. Datenträger-Datendurchsatz: IOPS | Max. virtuelle Ethernet-Netzwerkkarten (vNICs) |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> Für MPI-Anwendungen ist ein dediziertes RDMA-Back-End-Netzwerk durch ein FDR InfiniBand-Netzwerk aktiviert.

> [!NOTE]
> Im Rahmen der [RDMA-fähigen VMs](sizes-hpc.md#rdma-capable-instances) ist die H-Serie nicht SR-IOV-fähig. Daher unterscheiden sich die unterstützten [VM-Images](./workloads/hpc/configure.md#vm-images), Anforderungen an [InfiniBand-Treiber](./workloads/hpc/enable-infiniband.md) und unterstützten [MPI-Bibliotheken](./workloads/hpc/setup-mpi.md) von den SR-IOV-fähigen VMs.

## <a name="software-specifications"></a>Softwarespezifikationen

| Softwarespezifikationen     |H-Serie-VM           |
|-----------------------------|-----------------------|
| Maximale MPI-Auftragsgröße            | 4800 Kerne (300 VMs in einer einzelnen VM-Skalierungsgruppe mit singlePlacementGroup=true)  |
| MPI-Unterstützung                 | Intel MPI 5.x, MS-MPI  |
| Betriebssystemunterstützung für RDMA ohne SR-IOV   | CentOS/RHEL 6.5–7.4, SLES 12 SP4+, WinServer 2012–2016  |
| Orchestratorunterstützung        | CycleCloud, Batch, AKS  |

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Weitere Größen und Informationen

- [Allgemeiner Zweck](sizes-general.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [GPU-optimiert](sizes-gpu.md)
- [High Performance Computing](sizes-hpc.md)
- [Vorherige Generationen](sizes-previous-gen.md)

Preisrechner: [Preisrechner](https://azure.microsoft.com/pricing/calculator/)

Weitere Informationen zu Datenträgertypen finden Sie unter [Welche Datenträgertypen stehen in Azure zur Verfügung?](disks-types.md)


## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die neuesten Ankündigungen, HPC-Workloadbeispiele und Leistungsergebnisse in den [Tech Community-Blogs zu Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Eine allgemeinere Übersicht über die Architektur für die Ausführung von HPC-Workloads finden Sie unter [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
- Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.
