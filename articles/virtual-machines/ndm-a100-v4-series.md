---
title: NDm A100 v4-Serie
description: Spezifikationen für die VMs der NDm A100 v4-Serie.
author: sherrywangms
ms.author: sherrywang
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 10/26/2021
ms.openlocfilehash: 58070e351bd52be6ab1486a3f68bd544a86eaf4e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478935"
---
# <a name="ndm-a100-v4-series"></a>NDm A100 v4-Serie

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Die VM der NDm A100 v4-Serie ist ein neues Flagship-Mitglied der Azure-GPU-Familie, die für hoch anspruchsvolles Deep Learning-Training und eng gekoppelte, vertikal und horizontal skalierende HPC-Workloads konzipiert ist. 

Die NDm A100 v4-Serie beginnt mit einer einzelnen VM und acht NVIDIA Ampere A100 Tensor Core-GPUs mit 80 GB. NDm A100 v4-basierte Bereitstellungen können auf Tausende von GPUs mit einer Verbindungsbandbreite von 1,6 Tb/s pro VM hochskaliert werden. Jede GPU innerhalb der VM kommt mit einer eigenen dedizierten, topologieunabhängigen NVIDIA Mellanox HDR InfiniBand-Verbindung, die 200 Gbit/s Bandbreite bietet. Diese Verbindungen werden automatisch zwischen VMs in der gleichen VM-Skalierungsgruppe erstellt und unterstützen GPUDirect-RDMA.

Jede GPU verfügt über NVLINK 3.0-Konnektivität für die VM-interne Kommunikation, und die Instanz wird auch durch 96 physische AMD Epyc™ CPU-Kerne der zweiten Generation unterstützt.

Diese Instanzen bieten hervorragende Leistung für zahlreiche KI-, ML- und Analysetools mit integrierter Unterstützung für GPU-Beschleunigung. Dazu zählen beispielsweise TensorFlow, Pytorch, Caffe, RAPIDS und andere Frameworks. Darüber hinaus wird die horizontal skalierende InfiniBand-Verbindung von vielen vorhandenen KI- und HPC-Tools unterstützt, die auf den NCCL2-Kommunikationsbibliotheken von NVIDIA für das nahtlose GPU-Clustering aufbauen.

> [!IMPORTANT]
> Informationen zu den ersten Schritten mit NDm A100 v4-VMs, einschließlich der Treiber- und Netzwerkkonfiguration, finden Sie unter [Konfiguration und Optimierung von HPC-Workloads](./workloads/hpc/configure.md).
> Aufgrund des größeren GPU-Arbeitsspeicherbedarfs werden für die NDm A100_v4 [VMs der 2. Generation](./generation-2.md) und Marketplace-Images benötigt. Es werden dringend die [Azure HPC-Images](./workloads/hpc/configure.md) empfohlen. Azure HPC Ubuntu 18.04-, 20.04- und Azure HPC CentOS 7.9-Images werden unterstützt.
> 

<br>

[Storage Premium:](premium-storage-performance.md) Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Ultra Disks](disks-types.md#ultra-disks): Unterstützt ([Weitere Informationen](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312) zur Verfügbarkeit, Verwendung und Leistung) <br>
[Livemigration:](maintenance-and-updates.md) Nicht unterstützt<br>
[Updates mit Speicherbeibehaltung:](maintenance-and-updates.md) Nicht unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 2<br>
[Beschleunigter Netzwerkbetrieb:](../virtual-network/create-vm-accelerated-networking-cli.md) Nicht unterstützt<br>
[Kurzlebige Betriebssystemdatenträger](ephemeral-os-disks.md): Unterstützt<br>
InfiniBand: Unterstützt, GPUDirect-RDMA, 8 x 200 Gigabit HDR<br>
Nvidia NVLink Interconnect: Unterstützt<br>
<br>
Die NDm A100 v4-Serie unterstützt die folgenden Kernelversionen: <br>
CentOS 7.9 HPC: 3.10.0-1160.24.1.el7.x86_64 <br>
Ubuntu 18.04: 5.4.0-1043-azure <br>
Ubuntu 20.04: 5.4.0-1046-azure <br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Max. Netzwerkbandbreite | Maximale Anzahl NICs |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND96amsr_v4 | 96 | 1.900 | 6400 | 8 A100 80-GB-GPUs (NVLink 3.0) | 80 | 32 | 80.000/800 | 24.000 MBit/s | 8 |

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

Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.