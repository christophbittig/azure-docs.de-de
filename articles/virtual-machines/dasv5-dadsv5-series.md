---
title: 'Dasv5- und Dadsv5-Serie: Azure Virtual Machines'
description: Hier finden Sie Spezifikationen für die VMs der Dasv5- und Dadsv5-Serie.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 10/8/2021
ms.openlocfilehash: c9f2c270f8794effccd8439edd57422db15b71b6
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989924"
---
# <a name="dasv5-and-dadsv5-series"></a>Dasv5- und Dadsv5-Serie

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Die Dasv5-Serie und die Dadsv5-Serie nutzen den AMD-Prozessor EPYC<sup>TM</sup> 7763v der 3. Generation in einer Multithreadkonfiguration mit bis zu 256 MB L3-Cache und erweitern damit die Kundenoptionen für die Ausführung allgemeiner Workloads. Diese VMs bieten eine Kombination aus vCPUs und Arbeitsspeicher, um die Anforderungen der meisten Unternehmensworkloads zu erfüllen, z. B. kleine bis mittlere Datenbanken, Webserver mit geringem bis mittlerem Datenverkehr, Anwendungsserver und vieles mehr.

## <a name="dasv5-series"></a>Dasv5-Serie

VMs der Dasv5-Serie nutzen die AMD-Prozessoren EPYC<sup>TM</sup> 7763v der 3. Generation, die mittels Bursting eine maximale Frequenz von 3,5 GHz erreichen können. Die Größen der Dasv5-Serie bieten eine Kombination aus vCPU und Arbeitsspeicher für die meisten Produktionsworkloads. Die neuen VMs ohne lokale Datenträger bieten ein besseres Wertversprechen für Workloads, die keinen lokalen temporären Datenträger erfordern.

> [!NOTE]
> Häufig gestellte Fragen finden Sie unter [Azure-VM-Größen ohne lokale temporäre Datenträger](azure-vms-no-temp-disk.yml).

VMs der Dasv5-Serie unterstützen die Datenträgertypen SSD Standard, HDD Standard und SSD Premium. Sie können je nach regionaler Verfügbarkeit auch Disk Ultra-Speicher anfügen. Datenträgerspeicher wird separat von VMs abgerechnet. [Weitere Informationen finden Sie unter den Preisen für Datenträger.](https://azure.microsoft.com/pricing/details/managed-disks/)

[Storage Premium](premium-storage-performance.md): Unterstützt <br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt <br>
[Livemigration](maintenance-and-updates.md): Unterstützt <br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt <br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2 <br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Unterstützt <br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Nicht unterstützt <br><br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s | Durchsatz des Datenträgers mit maximalem Burst ohne Cache: IOPS/MBit/s<sup>1</sup> | Maximale Anzahl NICs | Max. Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2as_v5  | 2  | 8   | Nur Remotespeicher | 4  | 3\.750/82    | 10.000/600   | 2 | 12500  |
| Standard_D4as_v5  | 4  | 16  | Nur Remotespeicher | 8  | 6\.400/144   | 20.000/600   | 2 | 12500  |
| Standard_D8as_v5  | 8  | 32  | Nur Remotespeicher | 16 | 12.800/200  | 20.000/600   | 4 | 12500  |
| Standard_D16as_v5 | 16 | 64  | Nur Remotespeicher | 32 | 25600/384  | 40.000/800   | 8 | 12500 |
| Standard_D32as_v5 | 32 | 128 | Nur Remotespeicher | 32 | 51200/768  | 80.000/1.600  | 8 | 16000 |
| Standard_D48as_v5 | 48 | 192 | Nur Remotespeicher | 32 | 76800/1152 | 80.000/2.000  | 8 | 24.000 |
| Standard_D64as_v5 | 64 | 256 | Nur Remotespeicher | 32 | 80000/1200 | 80.000/2.000  | 8 | 32000 |
| Standard_D96as_v5 | 96 | 384 | Nur Remotespeicher | 32 | 80.000/1.600 | 80.000/2.000  | 8 | 40.000 |


<sup>1</sup> VMs der Dasv5-Serie können mit einem [Burst](disk-bursting.md) ihre Datenträgerleistung für jeweils bis zu 30 Minuten auf das maximale Bursting verbessern.


## <a name="dadsv5-series"></a>Dadsv5-Serie

VMs der Dadsv5-Serie nutzen die AMD-Prozessoren EPYC<sup>TM</sup> 7763v der 3. Generation, die mittels Bursting eine maximale Frequenz von 3,5 GHz erreichen können. Die Größen der Dadsv5-Serie bieten eine Kombination aus vCPU, Arbeitsspeicher und temporärem Speicher, die für die meisten Produktionsworkloads geeignet ist. Diese neuen VM-Größen bieten 50 % mehr lokalen Speicher sowie bessere IOPS auf lokalen Datenträgern für Lese- und Schreibvorgänge im Vergleich zu den Größen [Dav4/Dasv4](dav4-dasv4-series.md) mit [Gen2](generation-2.md)-VMs.

VMs der Dadsv5-Serie unterstützen die Datenträgertypen SSD Standard, HDD Standard und SSD Premium. Sie können je nach regionaler Verfügbarkeit auch Disk Ultra-Speicher anfügen. Datenträgerspeicher wird separat von VMs abgerechnet. [Weitere Informationen finden Sie unter den Preisen für Datenträger.](https://azure.microsoft.com/pricing/details/managed-disks/)


[Storage Premium](premium-storage-performance.md): Unterstützt <br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt <br>
[Livemigration](maintenance-and-updates.md): Unterstützt <br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt <br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2 <br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Unterstützt <br>
[Kurzlebige Betriebssystemdatenträger](ephemeral-os-disks.md): Unterstützt <br><br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler temporärer Speicherdurchsatz: IOPS/MB/s | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s | Durchsatz des Datenträgers mit maximalem Burst ohne Cache: IOPS/MBit/s<sup>1</sup> | Maximale Anzahl NICs | Max. Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2ads_v5  | 2  | 8   | 75   | 4  | 9\.000/125    | 3\.750/82    | 10.000/600  | 2 | 12500  |
| Standard_D4ads_v5  | 4  | 16  | 150  | 8  | 19.000/250   | 6\.400/144   | 20.000/600  | 2 | 12500  |
| Standard_D8ads_v5  | 8  | 32  | 300  | 16 | 38.000/500   | 12.800/200  | 20.000/600  | 4 | 12500  |
| Standard_D16ads_v5 | 16 | 64  | 600  | 32 | 75.000/1.000  | 25600/384  | 40.000/800  | 8 | 12500 |
| Standard_D32ads_v5 | 32 | 128 | 1200 | 32 | 150.000/2.000 | 51200/768  | 80.000/1.000 | 8 | 16000 |
| Standard_D48ads_v5 | 48 | 192 | 1800 | 32 | 225.000/3.000 | 76800/1152 | 80.000/200 | 8 | 24.000 |
| Standard_D64ads_v5 | 64 | 256 | 2400 | 32 | 300.000/4.000 | 80000/1200 | 80.000/2.000 | 8 | 32000 |
| Standard_D96ads_v5 | 96 | 384 | 3600 | 32 | 450.000/4.000 | 80.000/1.600 | 80.000/2.000 | 8 | 40.000 |

* Diese IOPS-Werte können mithilfe von Gen2-VMs erzielt werden.<br>
<sup>1</sup> VMs der Dadsv5-Serie können mit einem [Burst](disk-bursting.md) ihre Datenträgerleistung für jeweils bis zu 30 Minuten auf das maximale Bursting verbessern.


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
