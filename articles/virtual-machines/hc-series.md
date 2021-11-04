---
title: 'HC-Serie: Azure Virtual Machines'
description: Spezifikationen für die VMs der HC-Serie
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: c8eab88d1c3bfb29dda12d077c2ac841a09e98a6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082624"
---
# <a name="hc-series"></a>HC-Serie

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Die VMs der HC-Serie sind für Anwendungen mit hohen Anforderungen an die Rechenleistung optimiert, z. B. implizite Finite-Element-Analysen, Molekulardynamik oder Chemoinformatik. HC-VMs bieten 44 Intel-Prozessorkerne des Modells Xeon Platinum 8168, 8 GB RAM pro CPU-Kern und kein Hyperthreading. Die Intel Xeon Platinum-Plattform unterstützt Intels umfangreiches Ökosystem von Softwaretools, etwa die Intel Math Kernel Library, und erweiterte Vektorverarbeitungsfunktionen, z. B. AVX-512.

VMs der HC-Serie unterstützen Mellanox EDR InfiniBand mit 100 Gbit/s. Diese VMs sind für eine optimierte und konsistente RDMA-Leistung in einer FAT-Struktur ohne Blocks verbunden. Diese VMs unterstützen adaptives Routing und DCT (Dynamic Connected Transport, zusätzlich zum standardmäßigen RC- und UD-Transport). Diese Features verbessern die Anwendungsleistung, Skalierbarkeit und Konsistenz, und ihre Verwendung wird empfohlen. 

[ACU:](acu.md) 297-315<br>
[Storage Premium:](premium-storage-performance.md) Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Ultra Disks](disks-types.md#ultra-disks): Unterstützt ([Weitere Informationen](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312) zur Verfügbarkeit, Verwendung und Leistung) <br>
[Livemigration:](maintenance-and-updates.md) Nicht unterstützt<br>
[Updates mit Speicherbeibehaltung:](maintenance-and-updates.md) Nicht unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Unterstützt ([Weitere Informationen](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965) zu Leistung und potenziellen Problemen)<br>
[Kurzlebige Betriebssystemdatenträger](ephemeral-os-disks.md): Unterstützt<br>
<br>

| Size | vCPU | Prozessor | Arbeitsspeicher (GiB) | Speicherbandbreite GB/s | Basis-CPU-Frequenz (GHz) | Frequenz für alle Kerne (GHz, Spitze) | Frequenz für Einzelkern (GHz, Spitze) | RDMA-Leistung (Gbit/s) | MPI-Unterstützung | Temporärer Speicher (GiB) | Max. Anzahl Datenträger | Max. virtuelle Ethernet-Netzwerkkarten (vNICs) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3,7 | 100 | All | 700 | 4 | 8 |

Weitere Informationen:
- [Architektur und VM-Topologie](./workloads/hpc/hc-series-overview.md)
- Unterstützter [Softwarestapel](./workloads/hpc/hc-series-overview.md#software-specifications) einschließlich unterstützter Betriebssysteme
- Erwartete [Leistung](./workloads/hpc/hc-series-performance.md) bei VMs der HC-Serie

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
- Eine allgemeine Übersicht über die Architektur für die Ausführung von HPC-Workloads finden Sie unter [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
- Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.
