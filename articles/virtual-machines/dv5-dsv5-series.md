---
title: 'Dv5- und Dsv5-Serie: Azure Virtual Machines'
description: Hier finden Sie Spezifikationen für die VMs der Dv5- und Dsv5-Serie.
author: styli365
ms.author: joelpell
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 35738fe541d237b6bf271ea2892a67049f820fbf
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402312"
---
# <a name="dv5-and-dsv5-series"></a>Dv5- und Dsv5-Serie

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Die virtuellen Computer der Dv5- und Dsv5-Serie werden auf dem Prozessor Intel&reg; Xeon&reg; Platinum 8370C (Ice Lake) der 3. Generation in einer [Hyperthreadkonfiguration](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) ausgeführt, die ein besseres Preis-Leistungs-Verhältnis für die meisten Allzweckworkloads bereitstellt. Dieser neue Prozessor verfügt über eine Turbo-Taktfrequenz von 3,5 GHz für alle Kerne mit [Intel&reg; Turbo Boost Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Advanced-Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) und [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Diese VMs bieten eine Kombination aus vCPUs und Arbeitsspeicher, um die Anforderungen der meisten Unternehmensworkloads zu erfüllen, z. B. kleine bis mittlere Datenbanken, Webserver mit geringem bis mittlerem Datenverkehr, Anwendungsserver und vieles mehr. Die Dv5- und Dsv5-Serie bietet ein besseres Wertversprechen für Workloads, die keinen lokalen temporären Datenträger erfordern. Informationen zu ähnlichen virtuellen Computern mit lokalem Datenträger finden Sie unter [VMs der Ddv5- und Ddsv5-Serie](ddv5-ddsv5-series.md).

> [!NOTE]
> Häufig gestellte Fragen finden Sie unter [Azure-VM-Größen ohne lokale temporäre Datenträger](azure-vms-no-temp-disk.yml).

## <a name="dv5-series"></a>Dv5-Serie

VMs der Dv5-Serie werden auf dem Prozessor Intel® Xeon® Platinum 8370C (Ice Lake) der 3. Generation ausgeführt und erreichen eine Turbotaktgeschwindigkeit aller Kerne von bis zu 3,5 GHz.  Diese VMs bieten bis zu 96 vCPU und 384 GiB RAM.  VMs der Dv5-Serie bieten ein besseres Wertversprechen für die meisten allgemeinen Workloads im Vergleich zur vorherigen Generation (z. B. höhere Skalierbarkeit und eine aktualisierte CPU-Klasse).

VMs der Dv5-Serie verfügen nicht über temporären Speicher, wodurch der Preis für den Einstieg gesenkt wird.  An diese VMs können Sie SSD Standard- und HDD Standard-Datenträger anfügen. Wählen Sie virtuelle Computer der Dsv5-Serie aus, um SSD Premium- oder Disk Ultra-Speicher zu verwenden. Datenträgerspeicher wird separat von VMs abgerechnet. [Weitere Informationen zu Preisen für Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/)

[Storage Premium](premium-storage-performance.md): Nicht unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Nicht unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Erforderlich <br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Nicht unterstützt <br>
[Geschachtelte Virtualisierung](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): Unterstützt <br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs|Max. Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|
| Standard_D2_v5<sup>1, 2</sup> | 2  | 8   | Nur Remotespeicher | 4  | 2 | 12500 |
| Standard_D4_v5                | 4  | 16  | Nur Remotespeicher | 8  | 2 | 12500 |
| Standard_D8_v5                | 8  | 32  | Nur Remotespeicher | 16 | 4 | 12500 |
| Standard_D16_v5               | 16 | 64  | Nur Remotespeicher | 32 | 8 | 12500 |
| Standard_D32_v5               | 32 | 128 | Nur Remotespeicher | 32 | 8 | 16000 |
| Standard_D48_v5               | 48 | 192 | Nur Remotespeicher | 32 | 8 | 24.000 |
| Standard_D64_v5               | 64 | 256 | Nur Remotespeicher | 32 | 8 | 30.000 |
| Standard_D96_v5               | 96 | 384 | Nur Remotespeicher | 32 | 8 | 35000 |

<sup>1</sup> Beschleunigter Netzwerkbetrieb ist auf allen Dv5-VMs erforderlich und standardmäßig aktiviert.<br>
<sup>2</sup> Beschleunigter Netzwerkbetrieb kann auf zwei Netzwerkkarten angewendet werden.

## <a name="dsv5-series"></a>Dsv5-Serie

VMs der Dsv5-Serie werden auf dem Prozessor Intel® Xeon® Platinum 8370C (Ice Lake) der 3. Generation ausgeführt und erreichen eine Turbotaktgeschwindigkeit aller Kerne von bis zu 3,5 GHz.  Diese VMs bieten bis zu 96 vCPU und 384 GiB RAM.  VMs der Dsv5-Serie bieten ein besseres Wertversprechen für die meisten allgemeinen Workloads im Vergleich zur vorherigen Generation (z. B. höhere Skalierbarkeit und eine aktualisierte CPU-Klasse).

VMs der Dsv5-Serie verfügen nicht über temporären Speicher, wodurch der Preis für den Einstieg gesenkt wird.  Sie können an diese VMs SSD Standard-, HDD Standard- und SSDs Premium-Datenträger anfügen. Sie können auch Disk Ultra-Speicher basierend auf der regionalen Verfügbarkeit anfügen. Der Datenträgerspeicher wird separat von den virtuellen Computern in Rechnung gestellt. [Weitere Informationen finden Sie bei den Preisen für Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/).

[Storage Premium](premium-storage-performance.md): Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Erforderlich <br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Nicht unterstützt <br>
[Geschachtelte Virtualisierung](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): Unterstützt <br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s | Maximaler Burst-Datenträgerdurchsatz ohne Cache: (IOPS/MB/s)<sup>3</sup> | Maximale Anzahl NICs | Max. Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v5<sup>1,2</sup> | 2  | 8   | Nur Remotespeicher | 4  | 3750/85    | 10000/1200 | 2 | 12500 |
| Standard_D4s_v5               | 4  | 16  | Nur Remotespeicher | 8  | 6400/145   | 20000/1200 | 2 | 12500 |
| Standard_D8s_v5               | 8  | 32  | Nur Remotespeicher | 16 | 12800/290  | 20000/1200 | 4 | 12500 |
| Standard_D16s_v5              | 16 | 64  | Nur Remotespeicher | 32 | 25600/600  | 40000/1200 | 8 | 12500 |
| Standard_D32s_v5              | 32 | 128 | Nur Remotespeicher | 32 | 51200/865  | 80.000/2.000 | 8 | 16000 |
| Standard_D48s_v5              | 48 | 192 | Nur Remotespeicher | 32 | 76800/1315 | 80000/3000 | 8 | 24.000 |
| Standard_D64s_v5              | 64 | 256 | Nur Remotespeicher | 32 | 80000/1735 | 80000/3000 | 8 | 30.000 |
| Standard_D96s_v5              | 96 | 384 | Nur Remotespeicher | 32 | 80000/2600 | 80000/4000 | 8 | 35000 |

<sup>1</sup> Beschleunigter Netzwerkbetrieb ist auf allen Dsv5-VMs erforderlich und standardmäßig aktiviert.<br>
<sup>2</sup> Beschleunigter Netzwerkbetrieb kann auf zwei Netzwerkkarten angewendet werden.<br>
<sup>3</sup> VMs der Dsv5-Serie können mit einem [Burst](disk-bursting.md) ihre Datenträgerleistung für jeweils bis zu 30 Minuten auf das maximale Bursting verbessern.

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
