---
title: Dav4- und Dasv4-Serie
description: Hier finden Sie Spezifikationen für die virtuellen Computer der Dav4- und Dasv4-Serie.
author: mamccrea
ms.author: mamccrea
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.reviewer: jushiman
ms.openlocfilehash: c35f0958b8bfd5aa2695e3387710eb4afb895af0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131463124"
---
# <a name="dav4-and-dasv4-series"></a>Dav4- und Dasv4-Serie

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Die Dav4-Serie und die Dasv4-Serie sind neue Größen, bei denen der AMD-Prozessor EPYC<sup>TM</sup> 7452 mit 2,35 GHz in einer Multithreadkonfiguration mit bis zu 256 MB L3-Cache verwendet wird. Hierbei werden 8 MB des L3-Caches jeweils acht Kernen zur Verfügung gestellt, was Kunden bei der Ausführung ihrer universellen Workloads mehr Optionen gibt. Die Dav4-Serie und die Dasv4-Serie verfügen über die gleichen Arbeitsspeicher- und Datenträgerkonfigurationen wie die D- und die Dsv3-Serie.

## <a name="dav4-series"></a>Dav4-Serie

Die Größen der Dav4-Serie basieren auf dem AMD-Prozessor EPYC<sup>TM</sup> 7452 mit 2,35 GHz, der mittels Boosting einen maximalen Takt von 3,35 GHz erreichen kann. Die Größen der Dav4-Serie bieten eine Kombination aus vCPU, Arbeitsspeicher und temporärem Speicher, die für die meisten Produktionsworkloads geeignet ist. Datenträgerspeicher wird separat zu virtuellen Computern abgerechnet. Um SSD Premium zu verwenden, verwenden Sie die Größen der Dasv4-Serie. Für Dasv4-Größen gelten die gleichen Preise und Verbrauchseinheiten für die Abrechnung wie bei der Dav4-Serie.

[ACU:](acu.md) 230 – 260<br>
[Storage Premium:](premium-storage-performance.md) Nicht unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Nicht unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen](generation-2.md): Generation 1<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Unterstützt<br>
[Kurzlebige Betriebssystemdatenträger](ephemeral-os-disks.md): Unterstützt <br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (temporärer Speicher): IOPS/MBit/s Lesen/MBps Schreiben | Maximale Anzahl NICs | Erwartete Netzwerkbandbreite (MBit/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4<sup>1</sup> |  2  | 8  | 50  | 4  | 3000/46/23   | 2 | 2000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000/93/46   | 2 | 4000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000/187/93 | 4 | 8.000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000/375/187 |8 | 10000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000/750/375 |8 | 16000 |
| Standard_D48a_v4| 48 | 192| 1200 | 32 | 96000/1000/500 | 8 | 24.000 |
| Standard_D64a_v4| 64 | 256 | 1600 | 32 | 96000/1000/500 | 8 | 32000 |
| Standard_D96a_v4| 96 | 384 | 2400 | 32 | 96000/1000/500 | 8 | 40.000 |

<sup>1</sup> Beschleunigter Netzwerkbetrieb kann nur auf eine einzelne NIC angewendet werden. 

## <a name="dasv4-series"></a>Dasv4-Serie

Die Größen der Dasv4-Serie basieren auf dem AMD-Prozessor EPYC<sup>TM</sup> 7452 mit 2,35 GHz, der mittels Boosting einen maximalen Takt von 3,35 GHz erreichen und SSD Premium verwenden kann. Die Größen der Dasv4-Serie bieten eine Kombination aus vCPU, Arbeitsspeicher und temporärem Speicher, die für die meisten Produktionsworkloads geeignet ist.

[ACU:](acu.md) 230 – 260<br>
[Storage Premium:](premium-storage-performance.md) Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Unterstützt<br>
[Kurzlebige Betriebssystemdatenträger](ephemeral-os-disks.md): Unterstützt <br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBps (Cachegröße in GiB) | Maximaler Burst-Durchsatz für Cache- und temporären Speicher: IOPS / MBps <sup>1</sup> | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Durchsatz des Datenträgers mit maximalem Burst ohne Cache: IOPS/MBit/s<sup>1</sup> | Maximale Anzahl NICs | Erwartete Netzwerkbandbreite (MBit/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|----|
| Standard_D2as_v4<sup>2</sup>| 2  | 8   | 16  | 4  | 4000/32 (50)       | 4000/100    | 3200/48    | 4000/200   | 2 | 2000  |
| Standard_D4as_v4            | 4  | 16  | 32  | 8  | 8000/64 (100)      | 8000/200    | 6400/96    | 8000/200   | 2 | 4000  |
| Standard_D8as_v4            | 8  | 32  | 64  | 16 | 16000/128 (200)    | 16000/400   | 12800/192  | 16000/400  | 4 | 8.000  |
| Standard_D16as_v4           | 16 | 64  | 128 | 32 | 32.000/255 (400)    | 32000/800   | 25600/384  | 32000/800  | 8 | 10000 |
| Standard_D32as_v4           | 32 | 128 | 256 | 32 | 64.000/510 (800)    | 64000/1600  | 51200/768  | 64000/1600 | 8 | 16000 |
| Standard_D48as_v4           | 48 | 192 | 384 | 32 | 96.000/1.020 (1.200)  | 96000/2000  | 76.800/1.148 | 80.000/2.000 | 8 | 24.000 |
| Standard_D64as_v4           | 64 | 256 | 512 | 32 | 128.000/1.020 (1.600) | 128000/2000 | 80000/1200 | 80.000/2.000 | 8 | 32000 | 
| Standard_D96as_v4           | 96 | 384 | 768 | 32 | 192.000/1.020 (2.400) | 192.000/2.000 | 80000/1200 | 80.000/2.000 | 8 | 40.000 |

<sup>1</sup> VMs der Dasv4-Serie können mit einem [Burst](disk-bursting.md) ihre Datenträgerleistung für jeweils bis zu 30 Minuten auf das maximale Bursting verbessern.<br>
<sup>2</sup> Beschleunigter Netzwerkbetrieb kann nur auf eine einzelne NIC angewendet werden.



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Weitere Größen und Informationen

- [Allgemeiner Zweck](sizes-general.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [GPU-optimiert](sizes-gpu.md)
- [High Performance Computing](sizes-hpc.md)
- [Vorherige Generationen](sizes-previous-gen.md)

Preisrechner: [Preisrechner](https://azure.microsoft.com/pricing/calculator/)

Weitere Informationen zu Datenträgertypen: [Datenträgertypen](./disks-types.md#ultra-disks)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.
