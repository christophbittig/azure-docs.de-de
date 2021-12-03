---
title: 'Ddv5- und Ddsv5-Serie: Azure Virtual Machines'
description: Hier finden Sie Spezifikationen für die VMs der Ddv5- und Ddsv5-Serie.
author: styli365
ms.author: joelpell
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: f2f4cb3a9d0af5bdf5dd873151b7adc899a24295
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401989"
---
# <a name="ddv5-and-ddsv5-series"></a>Ddv5- und Ddsv5-Serie

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Die VMs der Ddv5- und Ddsv5-Serie werden auf dem Prozessor Intel&reg; Xeon&reg; Platinum 8370C (Ice Lake) der 3. Generation in einer [Hyperthreadkonfiguration](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) ausgeführt, die ein besseres Preis-Leistungs-Verhältnis für die meisten Allzweckworkloads bereitstellt. Dieser neue Prozessor verfügt über eine Turbotaktfrequenz von 3,5 GHz für alle Kerne mit [Intel&reg; Turbo Boost Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Advanced-Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) und [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Diese VMs bieten eine Kombination aus vCPUs, Arbeitsspeicher und temporärem Speicher, um die Anforderungen der meisten Unternehmensworkloads zu erfüllen, z. B. kleine bis mittlere Datenbanken, Webserver mit geringem bis mittlerem Datenverkehr, Anwendungsserver und vieles mehr.


## <a name="ddv5-series"></a>Ddv5-Serie
VMs der Ddv5-Serie werden auf dem Prozessor Intel® Xeon® Platinum 8370C (Ice Lake) der 3. Generation ausgeführt und erreichen eine Turbotaktfrequenz aller Kerne von bis zu 3,5 GHz. Diese VMs bieten bis zu 96 vCPUs und 384 GiB RAM sowie schnellen lokalen SSD-Speicher mit bis zu 3.600 GiB. VMs der Ddv5-Serie bieten ein besseres Wertversprechen für die meisten allgemeinen Workloads im Vergleich zur vorherigen Generation (z. B. höhere Skalierbarkeit und eine verbesserte CPU-Klasse). Diese VMs verfügen auch über schnellen und großen lokalen SSD-Speicher (bis zu 3.600 GiB).

VMs der Ddv5-Serie unterstützen die Datenträgertypen SSD Standard und HDD Standard. Wählen Sie VMs der Ddsv5-Serie aus, um SSD Premium- oder Disk Ultra-Speicher zu verwenden. Datenträgerspeicher wird separat von VMs abgerechnet. [Weitere Informationen zu Preisen für Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/)


[Storage Premium](premium-storage-performance.md): Nicht unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Nicht unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Erforderlich <br>
[Kurzlebige Betriebssystemdatenträger](ephemeral-os-disks.md): Unterstützt <br>
[Geschachtelte Virtualisierung](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): Unterstützt <br>
<br> 

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MB/s<sup>*</sup> | Maximale Anzahl NICs|Max. Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v5<sup>1,2</sup> | 2  | 8   | 75   | 4  | 9\.000/125    | 2 | 12500 |
| Standard_D4d_v5               | 4  | 16  | 150  | 8  | 19.000/250   | 2 | 12500 |
| Standard_D8d_v5               | 8  | 32  | 300  | 16 | 38.000/500   | 4 | 12500 |
| Standard_D16d_v5              | 16 | 64  | 600  | 32 | 75.000/1.000  | 8 | 12500 |
| Standard_D32d_v5              | 32 | 128 | 1200 | 32 | 150.000/2.000 | 8 | 16000 |
| Standard_D48d_v5              | 48 | 192 | 1800 | 32 | 225.000/3.000 | 8 | 24.000 |
| Standard_D64d_v5              | 64 | 256 | 2400 | 32 | 300.000/4.000 | 8 | 30.000 |
| Standard_D96d_v5              | 96 | 384 | 3600 | 32 | 450.000/4.000 | 8 | 35000 |

<sup>*</sup> Diese IOPS-Werte können mithilfe von [Gen2-VMs](generation-2.md) garantiert werden.<br>
<sup>1</sup> Beschleunigter Netzwerkbetrieb ist auf allen Ddv5-VMs erforderlich und standardmäßig aktiviert.<br>
<sup>2</sup> Beschleunigter Netzwerkbetrieb kann auf zwei Netzwerkkarten angewandt werden.

## <a name="ddsv5-series"></a>Ddsv5-Serie

VMs der Ddsv5-Serie werden auf dem Prozessor Intel® Xeon® Platinum 8370C (Ice Lake) der 3. Generation ausgeführt und erreichen eine Turbotaktfrequenz aller Kerne von bis zu 3,5 GHz.  Diese VMs bieten bis zu 96 vCPUs und 384 GiB RAM sowie schnellen lokalen SSD-Speicher mit bis zu 3.600 GiB. VMs der Ddsv5-Serie bieten ein besseres Wertversprechen für die meisten allgemeinen Workloads im Vergleich zur vorherigen Generation (z. B. höhere Skalierbarkeit und eine verbesserte CPU-Klasse).

VMs der Ddsv5-Serie unterstützen die Datenträgertypen SSD Standard, HDD Standard und SSD Premium. Sie können je nach regionaler Verfügbarkeit auch Disk Ultra-Speicher anfügen. Der Datenträgerspeicher wird separat von den virtuellen Computern in Rechnung gestellt. [Weitere Informationen finden Sie bei den Preisen für Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/).

[Storage Premium](premium-storage-performance.md): Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Erforderlich <br>
[Kurzlebige Betriebssystemdatenträger](ephemeral-os-disks.md): Unterstützt <br>
[Geschachtelte Virtualisierung](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): Unterstützt <br>
<br> 


| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MB/s<sup>*</sup> | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s | Maximaler Burst-Datenträgerdurchsatz ohne Cache: (IOPS/MB/s)<sup>3</sup> | Maximale Anzahl NICs | Max. Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2ds_v5<sup>1,2</sup> | 2  | 8   | 75   | 4  | 9\.000/125    | 3\.750/85     | 10000/1200 | 2 | 12500 |
| Standard_D4ds_v5               | 4  | 16  | 150  | 8  | 19.000/250   | 6\.400/145    | 20000/1200 | 2 | 12500 |
| Standard_D8ds_v5               | 8  | 32  | 300  | 16 | 38.000/500   | 12.800/290   | 20000/1200 | 4 | 12500 |
| Standard_D16ds_v5              | 16 | 64  | 600  | 32 | 75.000/1.000  | 25.600/600   | 40000/1200 | 8 | 12500 |
| Standard_D32ds_v5              | 32 | 128 | 1200 | 32 | 150.000/2.000 | 51.200/865   | 80.000/2.000 | 8 | 16000 |
| Standard_D48ds_v5              | 48 | 192 | 1800 | 32 | 225.000/3.000 | 76.800/1.315  | 80000/3000 | 8 | 24.000 |
| Standard_D64ds_v5              | 64 | 256 | 2400 | 32 | 375.000/4.000 | 80.000/1.735  | 80000/3000 | 8 | 30.000 |
| Standard_D96ds_v5              | 96 | 256 | 3600 | 32 | 450.000/4.000 | 80.000/2.600  | 80000/4000 | 8 | 35000 |

<sup>*</sup> Diese IOPS-Werte können mithilfe von [Gen2-VMs](generation-2.md) garantiert werden.<br>
<sup>1</sup> Beschleunigter Netzwerkbetrieb ist auf allen Ddsv5-VMs erforderlich und standardmäßig aktiviert.<br>
<sup>2</sup> Beschleunigter Netzwerkbetrieb kann auf zwei Netzwerkkarten angewandt werden.<br>
<sup>3</sup> VMs der Ddsv5-Serie können mit einem [Burst](disk-bursting.md) ihre Datenträgerleistung für jeweils bis zu 30 Minuten auf das maximale Bursting verbessern.

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
