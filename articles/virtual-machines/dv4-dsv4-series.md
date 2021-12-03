---
title: 'Dv4- und Dsv4-Serie: Azure-VMs'
description: Hier finden Sie Spezifikationen für die virtuellen Computer der Dv4- und Dsv4-Serie.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: b86da0ddee576e654433b70cbb902daa6916c776
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401438"
---
# <a name="dv4-and-dsv4-series"></a>Dv4- und Dsv4-Serie

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Die Dv4- und Dsv4-Serie läuft auf Intel-Prozessoren des Typs Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) mit einer Hyperthreading-Konfiguration ausgeführt, die für die meisten Allzweck-Workloads ein besseres Preis-Leistungs-Verhältnis bieten. Sie verfügen über eine Turbo-Taktfrequenz von 3,4 GHz für alle Kerne, [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) und [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Außerdem unterstützen sie [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). 

> [!NOTE]
> Häufig gestellte Fragen finden Sie unter [Azure-VM-Größen ohne lokale temporäre Datenträger](azure-vms-no-temp-disk.yml).

## <a name="dv4-series"></a>Dv4-Serie

Die Größen der Dv4-Serie laufen auf dem Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). Die Größen der Dv4-Serie bieten eine Kombination aus vCPU-, Arbeitsspeicher- und Remotespeicheroptionen, die für die meisten Produktionsworkloads geeignet ist. Virtuelle Computer der Dv4-Serie verfügen über Hyperthreading-Technologie von [Intel&reg;](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html).


Speicher für Remotedatenträger wird separat zu virtuellen Computern abgerechnet. Verwenden Sie die Dsv4-Größen, um Datenträger mit Premium-Speicher zu nutzen. Für Dsv4-Größen gelten die gleichen Preise und Verbrauchseinheiten für die Abrechnung wie bei der Dv4-Serie.

> [!NOTE]
> Nach einem Neustart wird neben Laufwerk C möglicherweise eine Datei mit dem Namen *Data_loss_warning.txt* angezeigt (der erste Datenträger, der über das Azure-Portal angefügt wurde). In diesem Szenario ist trotz des Dateinamens kein Datenverlust auf dem Datenträger aufgetreten. Im Allgemeinen wird die Datei *Data_loss_warning.txt* auf das temporäre Laufwerk kopiert. Wenn Sie einen virtuellen Computer ohne temporäres Laufwerk verwenden, wird die Datei von WindowsAzureGuestAgent fälschlicherweise auf das erste Laufwerk kopiert. Auf v4-VMs ist das erste Laufwerk ein Datenträger für Daten.
>
> Eine Lösung für dieses Problem wurde in der neuesten Version (Version 2.7.41491.999) des VM-Agents angewendet.

[ACU](acu.md): 195-210<br>
[Storage Premium:](premium-storage-performance.md) Nicht unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Nicht unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Unterstützt <br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Nicht unterstützt <br>
[Geschachtelte Virtualisierung](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): Unterstützt <br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs|Erwartete Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|
| Standard_D2_v4<sup>1</sup> | 2 | 8 | Nur Remotespeicher | 4 | 2|5.000 |
| Standard_D4_v4 | 4 | 16  | Nur Remotespeicher | 8 | 2|10000 |
| Standard_D8_v4 | 8 | 32 | Nur Remotespeicher | 16 | 4|12500 |
| Standard_D16_v4 | 16 | 64 | Nur Remotespeicher | 32 | 8|12500 |
| Standard_D32_v4 | 32 | 128 | Nur Remotespeicher | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | Nur Remotespeicher | 32 | 8|24.000 |
| Standard_D64_v4 | 64 | 256 | Nur Remotespeicher | 32 | 8|30.000 |

<sup>1</sup> Beschleunigter Netzwerkbetrieb kann nur auf eine einzelne NIC angewendet werden. 


## <a name="dsv4-series"></a>Dsv4-Serie

Die Größen der Dsv4-Serie laufen auf dem Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). Die Größen der Dv4-Serie bieten eine Kombination aus vCPU-, Arbeitsspeicher- und Remotespeicheroptionen, die für die meisten Produktionsworkloads geeignet ist. Virtuelle Computer der Dsv4-Serie verfügen über Hyperthreading-Technologie von [Intel&reg;](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html). Speicher für Remotedatenträger wird separat zu virtuellen Computern abgerechnet.

[ACU](acu.md): 195-210<br>
[Storage Premium](premium-storage-performance.md): Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Unterstützt<br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Nicht unterstützt <br>
[Geschachtelte Virtualisierung](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): Unterstützt <br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s | Durchsatz des Datenträgers mit maximalem Burst ohne Cache: IOPS/MBit/s<sup>1</sup> | Maximale Anzahl NICs|Erwartete Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v4<sup>2</sup> | 2 | 8  | Nur Remotespeicher | 4 | 3200/48 | 4000/200 |2|5.000 |
| Standard_D4s_v4 | 4 | 16 | Nur Remotespeicher | 8 | 6400/96 | 8000/200 |2|10000 |
| Standard_D8s_v4 | 8 | 32 | Nur Remotespeicher | 16 | 12800/192 | 16000/400 |4|12500 |
| Standard_D16s_v4 | 16 | 64  | Nur Remotespeicher | 32 | 25600/384 | 32000/800 |8|12500 |
| Standard_D32s_v4 | 32 | 128 | Nur Remotespeicher | 32 | 51200/768 | 64000/1600 |8|16000 |
| Standard_D48s_v4 | 48 | 192 | Nur Remotespeicher | 32 | 76800/1152 | 80.000/2.000 |8|24.000 |
| Standard_D64s_v4 | 64 | 256 | Nur Remotespeicher | 32 | 80000/1200 | 80.000/2.000 |8|30.000 |

<sup>1</sup> VMs der Dsv4-Serie können mit einem [Burst](./disk-bursting.md) ihre Datenträgerleistung für jeweils bis zu 30 Minuten auf das maximale Bursting verbessern.<br>
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

Weitere Informationen zu Datenträgertypen finden Sie unter [Welche Datenträgertypen stehen in Azure zur Verfügung?](disks-types.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.
