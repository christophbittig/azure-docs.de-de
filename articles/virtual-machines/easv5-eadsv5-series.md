---
title: 'Easv5- und Eadsv5-Serie: Azure Virtual Machines'
description: Hier finden Sie Spezifikationen für die VMs der Easv5- und Eadsv5-Serie.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/8/2021
ms.openlocfilehash: 3642323efddfab0c791273169e2c0fb0ec76f0b9
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989905"
---
# <a name="easv5-and-eadsv5-series"></a>Easv5- und Eadsv5-Serie

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Die Easv5-Serie und die Eadsv5-Serie nutzen den EPYC<sup>TM</sup> 7763v-Prozessor der 3. Generation von AMD in einer Multithreadkonfiguration mit bis zu 256 MB L3-Cache und erweitern damit die Kundenoptionen für die Ausführung der meisten arbeitsspeicheroptimierten Workloads. Diese virtuellen Computer bieten eine Kombination aus vCPUs und Arbeitsspeicher, um die Anforderungen zu erfüllen, die mit den meisten speicherintensiven Unternehmensanwendungen verbunden sind. Beispiele sind relationale Datenbankserver und In-Memory-Analyseworkloads.

## <a name="easv5-series"></a>Easv5-Serie

VMs der Easv5-Serie nutzen die AMD-Prozessoren EPYC<sup>TM</sup> 7763v der 3. Generation, die mittels Bursting eine maximale Frequenz von 3,5 GHz erreichen können. Die Größen der Easv5-Serie bieten eine Kombination aus vCPU und Arbeitsspeicher, die sich ideal für speicherintensive Unternehmensanwendungen eignet. Die neuen VMs ohne lokale Datenträger bieten ein besseres Wertversprechen für Workloads, die keinen lokalen temporären Datenträger erfordern.

> [!NOTE]
> Häufig gestellte Fragen finden Sie unter [Azure-VM-Größen ohne lokale temporäre Datenträger](azure-vms-no-temp-disk.yml).

VMs der Easv5-Serie unterstützen die Datenträgertypen SSD Standard, HDD Standard und SSD Premium. Sie können auch basierend auf der regionalen Verfügbarkeit Disk Storage Ultra anfügen. Der Datenträgerspeicher wird separat von den virtuellen Computern in Rechnung gestellt. [Weitere Informationen finden Sie bei den Preisen für Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/).

[Storage Premium](premium-storage-performance.md): Unterstützt <br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt <br>
[Livemigration](maintenance-and-updates.md): Unterstützt <br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt <br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2 <br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Unterstützt <br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Nicht unterstützt <br><br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s | Durchsatz des Datenträgers mit maximalem Burst ohne Cache: IOPS/MBit/s<sup>1</sup> | Maximale Anzahl NICs | Maximale Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2as_v5              | 2  | 16  | Nur Remotespeicher | 4  | 3750/82    | 10000/600  | 2 | 12500  |
| Standard_E4as_v5<sup>2</sup>  | 4  | 32  | Nur Remotespeicher | 8  | 6400/144   | 20000/600  | 2 | 12500  |
| Standard_E8as_v5<sup>2</sup>  | 8  | 64  | Nur Remotespeicher | 16 | 12800/200  | 20000/600  | 4 | 12500  |
| Standard_E16as_v5<sup>2</sup> | 16 | 128 | Nur Remotespeicher | 32 | 25600/384  | 40000/800  | 8 | 12500 |
| Standard_E20as_v5             | 20 | 160 | Nur Remotespeicher | 32 | 32000/480  | 64000/1000 | 8 | 12500 |
| Standard_E32as_v5<sup>2</sup> | 32 | 256 | Nur Remotespeicher | 32 | 51200/768  | 80000/1600 | 8 | 16000 |
| Standard_E48as_v5             | 48 | 384 | Nur Remotespeicher | 32 | 76800/1152 | 80.000/2.000 | 8 | 24.000 |
| Standard_E64as_v5<sup>2</sup> | 64 | 512 | Nur Remotespeicher | 32 | 80000/1200 | 80.000/2.000 | 8 | 32000 |
| Standard_E96as_v5<sup>2</sup> | 96 | 672 | Nur Remotespeicher | 32 | 80000/1600 | 80.000/2.000 | 8 | 40.000 |

<sup>1</sup> VMs der Easv5-Serie können mit einem [Burst](disk-bursting.md) ihre Datenträgerleistung für jeweils bis zu 30 Minuten auf ihr maximales Bursting verbessern.<br>
<sup>2</sup> [Eingeschränkte Kerngrößen verfügbar](constrained-vcpu.md)



## <a name="eadsv5-series"></a>Eadsv5-Serie

VMs der Eadsv5-Serie nutzen die AMD-Prozessoren EPYC<sup>TM</sup> 7763v der 3. Generation, die mittels Bursting eine maximale Frequenz von 3,5 GHz erreichen können. Die Größen der Eadsv5-Serie bieten eine Kombination aus vCPU und Zwischenspeicher, die sich ideal für speicherintensive Unternehmensanwendungen eignet. Die neuen VM bieten einen 50 % größeren lokalen Speicher sowie bessere IOPS auf lokalen Datenträgern für Lese- und Schreibvorgänge im Vergleich zu den [Eav4/Easv4](eav4-easv4-series.md)-Größen mit [Gen2](generation-2.md)-VMs.

VMs der Eadsv5-Serie unterstützen die Datenträgertypen SSD Standard, HDD Standard und SSD Premium. Sie können auch basierend auf der regionalen Verfügbarkeit Disk Storage Ultra anfügen. Der Datenträgerspeicher wird separat von den virtuellen Computern in Rechnung gestellt. [Weitere Informationen finden Sie bei den Preisen für Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/).

[Storage Premium](premium-storage-performance.md): Unterstützt <br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt <br>
[Livemigration](maintenance-and-updates.md): Unterstützt <br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt <br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2 <br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Unterstützt <br>
[Kurzlebige Betriebssystemdatenträger](ephemeral-os-disks.md): Unterstützt <br><br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler temporärer Speicherdurchsatz: IOPS/MBit/s | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s | Durchsatz des Datenträgers mit maximalem Burst ohne Cache: IOPS/MBit/s<sup>1</sup> | Maximale Anzahl NICs | Maximale Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2ads_v5              | 2  | 16  | 75   | 4  | 9000 / 125    | 3750/82      | 10000/600  | 2 | 12500  |
| Standard_E4ads_v5<sup>2</sup>  | 4  | 32  | 150  | 8  | 19000 / 250   | 6400/144     | 20000/600  | 2 | 12500  |
| Standard_E8ads_v5<sup>2</sup>  | 8  | 64  | 300  | 16 | 38000 / 500   | 12800/200    | 20000/600  | 4 | 12500  |
| Standard_E16ads_v5<sup>2</sup> | 16 | 128 | 600  | 32 | 75000 / 1000  | 25600/384    | 40000/800  | 8 | 12500 |
| Standard_E20ads_v5             | 20 | 160 | 750  | 32 | 94000 / 1250  | 32000/480    | 64000/1000 | 8 | 12500 |
| Standard_E32ads_v5<sup>2</sup> | 32 | 256 | 1200 | 32 | 150000 / 2000 | 51200/768    | 80000/1600 | 8 | 16000 |
| Standard_E48ads_v5             | 48 | 384 | 1800 | 32 | 225000 / 3000 | 76800/1152   | 80.000/2.000 | 8 | 24.000 |
| Standard_E64ads_v5<sup>2</sup> | 64 | 512 | 2400 | 32 | 300000 / 4000 | 80000/1200   | 80.000/2.000 | 8 | 32000 |
| Standard_E96ads_v5<sup>2</sup> | 96 | 672 | 3600 | 32 | 450000 / 4000 | 80000/1600   | 80.000/2.000 | 8 | 40.000 |

* Diese IOPS-Werte können mithilfe von Gen2-VMs erzielt werden.<br>
<sup>1</sup> VMs der Eadsv5-Serie können mit einem [Burst](disk-bursting.md) ihre Datenträgerleistung für jeweils bis zu 30 Minuten auf das maximale Bursting verbessern.<br>
<sup>2</sup> [Eingeschränkte Kerngrößen verfügbar](constrained-vcpu.md)


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
