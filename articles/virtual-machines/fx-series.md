---
title: FX-Serie
description: Spezifikationen für die VMs der FX-Serie.
author: brbell
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: jushiman
ms.openlocfilehash: f287fddc2f7639f2ff61588c9d0fe8068cb675dc
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402160"
---
# <a name="fx-series"></a>FX-Serie

Die FX-Serie wird auf den Prozessoren Intel® Xeon® Gold 6246R (Cascade Lake) ausgeführt. Sie verfügt über eine Turbofrequenz von 4,0 GHz auf allen Kernen, 21 GB RAM pro vCPU sowie bis zu 1 TB RAM und lokalen temporären Speicher. Von der FX-Serie profitieren Workloads, die eine hohe CPU-Taktgeschwindigkeit und einen hohen Arbeitsspeicheranteil pro CPU erfordern, Workloads mit hohen Lizenzierungskosten pro Kern sowie Anwendungen, die eine hohe Einzelkernleistung erfordern. Ein typischer Anwendungsfall für die FX-Serie stellt die Workload „Elektronische Entwurfsautomatisierung“ (Electronic Design Automation, EDA) dar.

VMs der FX-Serie verfügen über [Turbo Boost Technology 2.0 von Intel®](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Hyperthreading-Technologie von Intel®](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) und [Erweiterte Vektorerweiterungen 512 von Intel® (Intel® AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html).

[ACU](acu.md): 310 bis 340<br>
[Storage Premium:](premium-storage-performance.md) Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 2<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Unterstützt<br>
[Kurzlebige Betriebssystemdatenträger](ephemeral-os-disks.md): Unterstützt <br>
[Geschachtelte Virtualisierung](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): Unterstützt <br>
<br>

| Size | vCPUs | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBit/s | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s | Maximale Anzahl NICs|Erwartete Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_FX4mds  | 4   | 84   | 168  | 8   | 40000/343     | 6700/104   | 2 | 4000  |
| Standard_FX12mds | 12  | 252  | 504  | 24  | 100000/1029   | 20000/314  | 4 | 8.000  |
| Standard_FX24mds | 24  | 504  | 1008 | 32  | 200000/2057   | 40000/629  | 4 | 16000 |
| Standard_FX36mds | 36  | 756  | 1512 | 32  | 300000/3086   | 60000/944  | 8 | 24.000 |
| Standard_FX48mds | 48  | 1008 | 2016 | 32  | 400000/3871   | 80000/1258 | 8 | 32000 |


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
