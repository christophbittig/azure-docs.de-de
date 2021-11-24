---
title: 'Ev5- und Esv5-Serie: Azure Virtual Machines'
description: Spezifikationen für die VMs der Ev5- und Esv5-Serie
author: styli365
ms.author: joelpell
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 897ae578ca1ff088e2f3f937bb9f00977ee233ad
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399350"
---
# <a name="ev5-and-esv5-series"></a>Ev5- und Esv5-Serie

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Virtual Machines der Ev5- und Esv5-Serie werden auf dem Intel&reg;Xeon&reg; Platinum 8370C (Ice Lake)-Prozessor der dritten Generation in einer [Hyperthreadkonfiguration](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) ausgeführt, wodurch für die meisten allgemeinen Workloads ein besseres Nutzenversprechen geboten wird. Dieser neue Prozessor verfügt über Turbotaktfrequenz für alle Kerne von 3,5 GH mit [Intel&reg;-Turbo-Boost-Technik](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) und [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Mit bis zu 672 Gib RAM eignen sich diese virtuellen Computer perfekt für speicherintensive Unternehmensanwendungen, relationale Datenbankserver und In-Memory-Analyseworkloads. Die Ev5- und Esv5-Serie bietet ein besseres Nutzenversprechen für Workloads, für die kein lokaler temporärer Datenträger erforderlich ist. Informationen zu ähnlichen virtuellen Computern mit lokalem Datenträger finden Sie unter [VMs der Edv5- und Edsv5-Serie](edv5-edsv5-series.md).

> [!NOTE]
> Häufig gestellte Fragen finden Sie unter [Azure-VM-Größen ohne lokale temporäre Datenträger](azure-vms-no-temp-disk.yml).

## <a name="ev5-series"></a>Die Ev5-Serie

Virtuelle Computer der Ev5-Serie werden auf dem Intel® Xeon® Platinum 8370C (Ice-Lake)-Prozessor der dritten Generation ausgeführt und erreichen eine Turbotaktfrequenz für alle Kerne von bis zu 3,5 GHz.  Diese virtuellen Computer bieten bis zu 104 vCPU und 672 GiB RAM. Virtuelle Computer der Ev5-Serie verfügen über keinen temporären Speicher, wodurch der Einstiegspreis gesenkt wird.

Die Ev5-Serie unterstützt SSD Standard- und HDD Standard-Datenträgertypen. Wählen Sie virtuelle Computer der Esv5-Serie aus, um den SSD Premium- oder Disk Ultra-Speicher zu verwenden. Der Datenträgerspeicher wird separat von den virtuellen Computern in Rechnung gestellt. [Weitere Informationen zu Preisen für Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/)

[Storage Premium](premium-storage-performance.md): Nicht unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Nicht unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Erforderlich <br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Nicht unterstützt <br>
[Geschachtelte Virtualisierung](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): Unterstützt <br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs|Maximale Bandbreite (MBit/s) |
|---|---|---|---|---|---|---|
| Standard_E2_v5<sup>1,2</sup>  | 2   | 16  | Nur Remotespeicher | 4  | 2 | 12500 |
| Standard_E4_v5                | 4   | 32  | Nur Remotespeicher | 8  | 2 | 12500 |
| Standard_E8_v5                | 8   | 64  | Nur Remotespeicher | 16 | 4 | 12500 |
| Standard E16_v5               | 16  | 128 | Nur Remotespeicher | 32 | 8 | 12500 |
| Standard_E20_v5               | 20  | 160 | Nur Remotespeicher | 32 | 8 | 12500  |
| Standard_E32_v5               | 32  | 256 | Nur Remotespeicher | 32 | 8 | 16000  |
| Standard_E48_v5               | 48  | 384 | Nur Remotespeicher | 32 | 8 | 24.000  |
| Standard_E64_v5               | 64  | 512 | Nur Remotespeicher | 32 | 8 | 30.000  |
| Standard_E96_v5               | 96  | 672 | Nur Remotespeicher | 32 | 8 | 30.000  |
| Standard_E104i_v5<sup>3</sup> | 104 | 672 | Nur Remotespeicher | 64 | 8 | 100.000 |

<sup>1</sup> Der beschleunigte Netzwerkbetrieb ist auf allen virtuellen Computern der Ev5-Serie erforderlich und standardmäßig aktiviert.<br>
<sup>2</sup> Der beschleunigter Netzwerkbetrieb kann auf zwei NICs angewendet werden.<br>
<sup>3</sup> Die Instanz ist auf Hardware [isoliert](../security/fundamentals/isolation-choices.md#compute-isolation), die für einen einzelnen Kunden bzw. eine einzelne Kundin bestimmt ist.<br>

## <a name="esv5-series"></a>Die Esv5-Serie

Virtuelle Computer der Esv5-Serie werden auf dem Intel® Xeon® Platinum 8370C (Ice Lake)-Prozessor der dritten Generation ausgeführt und erreichen eine Turbotaktfrequenz für alle Kerne von bis zu 3,5 GHz.  Diese virtuellen Computer bieten bis zu 104 vCPU und 672 GiB RAM. Virtuelle Computer der Esv5-Serie verfügen über keinen temporären Speicher, wodurch der Einstiegspreis gesenkt wird.

Die Esv5-Serie unterstützt SSD Standard-, HDD Standard- und Premium SSD-Datenträgertypen. Sie können auch einen Disk Ultra-Speicher basierend auf seiner regionalen Verfügbarkeit anfügen. Der Datenträgerspeicher wird separat von den virtuellen Computern in Rechnung gestellt. [Weitere Informationen finden Sie bei den Preisen für Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/).

[Storage Premium](premium-storage-performance.md): Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Erforderlich <br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Nicht unterstützt <br>
[Geschachtelte Virtualisierung](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): Unterstützt <br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s | Maximaler Burst-Datenträgerdurchsatz ohne Cache: IOPS/MBit/s<sup>5</sup> | Maximale Anzahl NICs | Maximale Bandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v5<sup>1,2</sup>  | 2   | 16  | Nur Remotespeicher | 4  | 3750/85     | 10000/1200 | 2 | 12500 |
| Standard_E4s_v5                | 4   | 32  | Nur Remotespeicher | 8  | 6400/145    | 20000/1200 | 2 | 12500 |
| Standard_E8s_v5                | 8   | 64  | Nur Remotespeicher | 16 | 12800/290   | 20000/1200 | 4 | 12500 |
| Standard_E16s_v5               | 16  | 128 | Nur Remotespeicher | 32 | 25600/600   | 40000/1200 | 8 | 12500 |
| Standard_E20s_v5               | 20  | 160 | Nur Remotespeicher | 32 | 32000/750   | 64000/1600 | 8 | 12500  |
| Standard_E32s_v5               | 32  | 256 | Nur Remotespeicher | 32 | 51200/865   | 80.000/2.000 | 8 | 16000  |
| Standard_E48s_v5               | 48  | 384 | Nur Remotespeicher | 32 | 76800/1315  | 80000/3000 | 8 | 24.000  |
| Standard_E64s_v5               | 64  | 512 | Nur Remotespeicher | 32 | 80000/1735  | 80000/3000 | 8 | 30.000  |
| Standard_E96s_v5<sup>3</sup>   | 96  | 672 | Nur Remotespeicher | 32 | 80000/2600  | 80000/4000 | 8 | 35000  |
| Standard_E104is_v5<sup>4</sup> | 104 | 672 | Nur Remotespeicher | 64 | 120000/4000 | 120000/4000 | 8 | 100.000 |

<sup>1</sup> Der beschleunigte Netzwerkbetrieb ist auf allen virtuellen Computern der Esv5-Serie erforderlich und standardmäßig aktiviert.<br>
<sup>2</sup> Der beschleunigter Netzwerkbetrieb kann auf zwei NICs angewendet werden.<br>
<sup>3</sup> [Eingeschränkte Kerngrößen](constrained-vcpu.md) verfügbar<br>
<sup>4</sup> Die Instanz ist auf Hardware [isoliert](../security/fundamentals/isolation-choices.md#compute-isolation), die für einen einzelnen Kunden bzw. eine einzelne Kundin bestimmt ist.<br>
<sup>5</sup> Virtuelle Computer der Esv5-Serie können ihre Datenträgerleistung für bis zu 30 Minuten am Stück auf das maximale Bursting [steigern](disk-bursting.md).

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
