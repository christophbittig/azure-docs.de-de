---
title: 'Edv5- und Edsv5-Serie: Azure Virtual Machines'
description: Spezifikationen für die VMs der Edv5- und Edsv5-Serie.
author: styli365
ms.author: joelpell
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 496dc7271b3468a106bd6c1dbe25e00bc147c26a
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401952"
---
# <a name="edv5-and-edsv5-series"></a>Edv5- und Edsv5-Serie

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Die virtuellen Computer der Edv5- und Edsv5-Serie werden auf dem Prozessor Intel&reg; Xeon&reg; Platinum 8370C (Ice Lake) der 3. Generation in einer [Hyperthreadkonfiguration](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) ausgeführt, die ein besseres Preis-Leistungs-Verhältnis für die meisten Allzweckworkloads bereitstellt. Dieser neue Prozessor verfügt über eine Turbo-Taktfrequenz von 3,5 GHz für alle Kerne mit [Intel&reg; Turbo Boost Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Advanced-Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) und [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Mit bis zu 672 GiB RAM eignen sich diese virtuellen Computer ideal für speicherintensive Unternehmensanwendungen, relationale Datenbankserver und In-Memory-Analyseworkloads. Diese VMs verfügen auch über schnellen und großen lokalen SSD-Speicher (bis zu 3.900 GiB).

## <a name="edv5-series"></a>Edv5-Serie

Virtuelle Computer der Edv5-Serie werden auf dem Prozessor Intel® Xeon® Platinum 8370C (Ice Lake) der 3. Generation ausgeführt und erreichen eine Turbotaktgeschwindigkeit aller Kerne von bis zu 3,5 GHz.  Diese virtuellen Computer bieten bis zu 104 vCPU und 672 GiB RAM sowie schnellen lokalen SSD-Speicher mit bis zu 3.800 GiB. Virtuelle Computer der Edv5-Serie eignen sich ideal für arbeitsspeicherintensive Unternehmensanwendungen und Anwendungen, die von lokalem Hochgeschwindigkeitsspeicher mit geringer Latenz profitieren.

Virtuelle Computer der Edv5-Serie unterstützen SSD Standard- und HDD Standard-Datenträgertypen. Wählen Sie virtuelle Computer der Edsv5-Serie aus, um SSD Premium- oder Disk Ultra-Speicher zu verwenden. Datenträgerspeicher wird separat von virtuellen Computern abgerechnet. [Weitere Informationen zu Preisen für Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/)

[Storage Premium](premium-storage-performance.md): Nicht unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Nicht unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Erforderlich <br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Nicht unterstützt <br>
[Geschachtelte Virtualisierung](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): Unterstützt <br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MB/s<sup>*</sup>  | Maximale Anzahl NICs|Max. Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v5<sup>1,2</sup>  | 2   | 16  | 75   | 4  | 9\.000/125    | 2 | 12500 |
| Standard_E4d_v5                | 4   | 32  | 150  | 8  | 19.000/250   | 2 | 12500 |
| Standard_E8d_v5                | 8   | 64  | 300  | 16 | 38.000/500   | 4 | 12500 |
| Standard_E16d_v5               | 16  | 128 | 600  | 32 | 75.000/1.000  | 8 | 12500 |
| Standard_E20d_v5               | 20  | 160 | 750  | 32 | 94.000/1.250  | 8 | 12500  |
| Standard_E32d_v5               | 32  | 256 | 1200 | 32 | 150.000/2.000 | 8 | 16000  |
| Standard_E48d_v5               | 48  | 384 | 1800 | 32 | 225.000/3.000 | 8 | 24.000  |
| Standard_E64d_v5               | 64  | 512 | 2400 | 32 | 300.000/4.000 | 8 | 30.000  |
| Standard_E96d_v5               | 96  | 672 | 3600 | 32 | 450.000/4.000 | 8 | 35000  |
| Standard_E104id_v5<sup>3</sup> | 104 | 672 | 3800 | 64 | 450.000/4.000 | 8 | 100.000 |

<sup>*</sup> Diese IOPS-Werte können mithilfe von [Gen2-VMs](generation-2.md) garantiert werden.<br>
<sup>1</sup> Beschleunigter Netzwerkbetrieb ist auf allen virtuellen Edv5-Computern erforderlich und standardmäßig aktiviert.<br>
<sup>2</sup> Beschleunigter Netzwerkbetrieb kann auf zwei Netzwerkkarten angewendet werden.<br>
<sup>3</sup> Instanz wird [isoliert](../security/fundamentals/isolation-choices.md#compute-isolation) auf dedizierter Hardware ausgeführt, die für einen einzigen Kunden bereitgestellt wird.


## <a name="edsv5-series"></a>Edsv5-Serie

Virtuelle Computer der Edsv5-Serie werden auf dem Prozessor Intel® Xeon® Platinum 8370C (Ice Lake) der 3. Generation ausgeführt und erreichen eine Turbotaktgeschwindigkeit aller Kerne von bis zu 3,5 GHz.  Diese virtuellen Computer bieten bis zu 104 vCPU und 672 GiB RAM sowie schnellen lokalen SSD-Speicher mit bis zu 3.800 GiB. Virtuelle Computer der Edsv5-Serie eignen sich ideal für arbeitsspeicherintensive Unternehmensanwendungen und Anwendungen, die von lokalem Hochgeschwindigkeitsspeicher mit geringer Latenz profitieren.

Virtuelle Computer der Edsv5-Serie unterstützen SSD Standard- und HDD Standard-Datenträgertypen. Sie können an diese virtuellen Computer SSD Standard-, HDD Standard- und SSDs Premium-Datenträger anfügen. Sie können auch Disk Ultra-Speicher basierend auf der regionalen Verfügbarkeit anfügen. Der Datenträgerspeicher wird separat von den virtuellen Computern in Rechnung gestellt. [Weitere Informationen finden Sie bei den Preisen für Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/).

[Storage Premium](premium-storage-performance.md): Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Erforderlich <br>
[Kurzlebige Betriebssystemdatenträger](ephemeral-os-disks.md): Unterstützt <br>
[Geschachtelte Virtualisierung](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): Unterstützt <br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MB/s<sup>*</sup> | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s | Maximaler Burst-Datenträgerdurchsatz ohne Cache: IOPS/MBit/s<sup>5</sup> | Maximale Anzahl NICs | Max. Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v5<sup>1,2</sup>  | 2   | 16  | 75   | 4  | 9\.000/125    | 3\.750/85      | 10000/1200 | 2 | 12500 |
| Standard_E4ds_v5                | 4   | 32  | 150  | 8  | 19.000/250   | 6\.400/145     | 20000/1200 | 2 | 12500 |
| Standard_E8ds_v5                | 8   | 64  | 300  | 16 | 38.000/500   | 12.800/290    | 20000/1200 | 4 | 12500 |
| Standard_E16ds_v5               | 16  | 128 | 600  | 32 | 75.000/1.000  | 25.600/600    | 40000/1200 | 8 | 12500 |
| Standard_E20ds_v5               | 20  | 160 | 750  | 32 | 94.000/1250  | 32.000/750    | 64000/1600 | 8 | 12500  |
| Standard_E32ds_v5               | 32  | 256 | 1200 | 32 | 150.000/2.000 | 51.200/865    | 80.000/2.000 | 8 | 16000  |
| Standard_E48ds_v5               | 48  | 384 | 1800 | 32 | 225.000/3.000 | 76.800/1.315   | 80000/3000 | 8 | 24.000  |
| Standard_E64ds_v5               | 64  | 512 | 2400 | 32 | 375.000/4.000 | 80.000/1.735   | 80000/3000 | 8 | 30.000  |
| Standard_E96ds_v5<sup>3</sup>   | 96  | 672 | 3600 | 32 | 450.000/4.000 | 80.000/2.600   | 80000/4000 | 8 | 35000  |
| Standard_E104ids_v5<sup>4</sup> | 104 | 672 | 3800 | 64 | 450.000/4.000 | 120.000/4.000  | 120000/4000 | 8 | 100.000 |

<sup>*</sup> Diese IOPS-Werte können mithilfe von [Gen2-VMs](generation-2.md) garantiert werden.<br>
<sup>1</sup> Beschleunigter Netzwerkbetrieb ist auf allen virtuellen Edsv5-Computern erforderlich und standardmäßig aktiviert.<br>
<sup>2</sup> Beschleunigter Netzwerkbetrieb kann auf zwei Netzwerkkarten angewendet werden.<br>
<sup>3</sup> [Eingeschränkte Kerngrößen](constrained-vcpu.md) verfügbar.<br>
<sup>4</sup> Instanz wird [isoliert](../security/fundamentals/isolation-choices.md#compute-isolation) auf dedizierter Hardware ausgeführt, die für einen einzigen Kunden bereitgestellt wird.<br>
<sup>5</sup> VMs der Edsv5-Serie können mit einem [Burst](disk-bursting.md) ihre Datenträgerleistung für jeweils bis zu 30 Minuten auf das maximale Bursting verbessern.

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
