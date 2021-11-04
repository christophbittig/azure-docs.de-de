---
title: 'HBv3-Serie: Azure Virtual Machines'
description: Spezifikationen für VMs der HBv3-Serie
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 208c1c16c07bdd32730d17e990e037f0fe7da015
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131060457"
---
# <a name="hbv3-series"></a>HBv3-Serie

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

VMs der HBv3-Serie sind für HPC-Anwendungen wie Strömungssimulationen, explizite und implizite Finite-Elemente-Analysen, Wettermodellierung, seismische Erkundung, Lagerstätten- und RTL-Simulationen optimiert. HBv3-VMs verfügen über bis zu 120 CPU-Kerne der AMD EPYC™ 7003-Serie (Milan), 448 GB RAM und kein Hyperthreading. Außerdem bieten VMs der HBv3-Serie eine Arbeitsspeicherbandbreite von 350 GB/s, bis zu 32 MB L3-Cache pro Kern, bis zu 7 GB/s der Blockgeräte-SSD-Leistung und Taktfrequenzen von bis zu 3,675 GHz. 

Alle VMs der HBv3-Serie bieten 200 GBit/s HDR InfiniBand aus dem NVIDIA-Netzwerk für MPI-Workloads wie bei Supercomputern. Diese VMs sind für eine optimierte und konsistente RDMA-Leistung in einer FAT-Struktur ohne Blocks verbunden. Das HDR InfiniBand-Fabric unterstützt auch adaptives Routing und DCT (Dynamic Connected Transport, zusätzlich zum standardmäßigen RC- und UD-Transport). Diese Funktionen verbessern die Anwendungsleistung, Skalierbarkeit und Konsistenz und ihre Verwendung wird dringend empfohlen.

[Storage Premium:](premium-storage-performance.md) Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Ultra Disks](disks-types.md#ultra-disks): Unterstützt ([Weitere Informationen](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312) zur Verfügbarkeit, Verwendung und Leistung) <br>
[Livemigration:](maintenance-and-updates.md) Nicht unterstützt<br>
[Updates mit Speicherbeibehaltung:](maintenance-and-updates.md) Nicht unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb:](../virtual-network/create-vm-accelerated-networking-cli.md) demnächst<br>
[Kurzlebige Betriebssystemdatenträger](ephemeral-os-disks.md): Unterstützt<br>
<br>

|Size |vCPU |Prozessor |Arbeitsspeicher (GiB) |Speicherbandbreite GB/s |Basis-CPU-Frequenz (GHz) |Frequenz für alle Kerne (GHz, Spitze) |Frequenz für Einzelkern (GHz, Spitze) |RDMA-Leistung (Gbit/s) |MPI-Unterstützung |Temporärer Speicher (GiB) |Max. Anzahl Datenträger |Max. virtuelle Ethernet-Netzwerkkarten (vNICs) |
|----|----|----|----|----|----|----|----|----|----|----|----|----|
|Standard_HB120rs_v3    |120 |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |All |2 × 960 |32 |8 |
|Standard_HB120-96rs_v3 |96  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |All |2 × 960 |32 |8 |
|Standard_HB120-64rs_v3 |64  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |All |2 × 960 |32 |8 |
|Standard_HB120-32rs_v3 |32  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |All |2 × 960 |32 |8 |
|Standard_HB120-16rs_v3 |16  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |All |2 × 960 |32 |8 |

Weitere Informationen:
- [Architektur und VM-Topologie](./workloads/hpc/hbv3-series-overview.md)
- Unterstützter [Softwarestapel](./workloads/hpc/hbv3-series-overview.md#software-specifications) einschließlich unterstützter Betriebssysteme
- Erwartete [Leistung](./workloads/hpc/hbv3-performance.md) der VM der HBv3-Serien

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
