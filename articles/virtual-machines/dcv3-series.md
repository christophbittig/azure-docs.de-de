---
title: 'DCsv3- und DCdsv3-Serie: Azure-VMs'
description: Spezifikationen für VMs der DCsv3- und DCdsv3-Serie.
author: mmcrey
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: mmcrey
ms.custom: ignite-fall-2021
ms.openlocfilehash: 09d4c88bec94c881fd9895557aa35264ea4ffab4
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561212"
---
# <a name="dcsv3-and-dcdsv3-series"></a>DCsv3- und DCdsv3-Serie

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

> [!NOTE] 
> DCsv3 und DCdsv3 befinden sich ab dem 1. November 2021 in der öffentlichen Vorschau.

VMs der DCsv3- und DCdsv3-Serie können Ihnen beim Schutz der Vertraulichkeit und Integrität Ihres Codes und Ihrer Daten während ihrer Verarbeitung in der öffentlichen Cloud helfen. Durch die Nutzung von Intel® Software Guard Extensions und Intel® Total Memory Encryption – Multi Key können Kunden sicherstellen, dass ihre Daten stets verschlüsselt und geschützt sind. 

Diese Computer werden von den neuesten skalierbaren Intel® Xeon-Prozessoren der 3. Generation unterstützt und nutzen Intel® Turbo Boost Max Technology 3.0, um 3,5 GHz zu erreichen. 

Bei dieser Generation wurde die Anzahl der CPU-Kerne um das 6-fache erhöht (bis zu einem Maximum von 48 physischen Kernen). Der verschlüsselte Arbeitsspeicher (Encrypted Memory, EPC) wurde um das 1.500-fache auf 256 GB erhöht, der reguläre Arbeitsspeicher um das 12-fache auf 384 GB. Durch all diese Änderungen wird die Leistung erheblich verbessert, und es werden völlig neue Szenarien möglich. 

> [!NOTE]
> Hyperthreading ist deaktiviert, um den Sicherheitsstatus zu verbessern. Die Preise basieren auf der im Vergleich zu virtuellen Kernen überlegenen Leistung physischer Kerne sowie auf den einzigartigen Sicherheitsfunktionen der DC-Serie.

Wir bieten zwei Varianten, die davon abhängen, ob die Workload von einem lokalen Datenträger profitiert oder nicht. Unabhängig davon, ob Sie eine VM mit einem lokalen Datenträger wählen oder nicht, können Sie an alle VMs dauerhaften Remote-Datenträgerspeicher anfügen. Remotedatenträgeroptionen (z. B. für den VM-Startdatenträger) werden wie immer separat von den VMs in Rechnung gestellt. 

## <a name="configuration"></a>Konfiguration

CPU: Skalierbarer Intel® Xeon-Prozessor der 3. Generation 8370C<br>
Basisfrequenz aller Kerne: 2,8 GHz<br>
[Turbo Boost Max 3.0:](https://www.intel.com/content/www/us/en/gaming/resources/turbo-boost.html)Aktiviert, maximale Frequenz 3,5 GHz<br>
[Hyperthreading](https://www.intel.com/content/www/us/en/gaming/resources/hyper-threading.html): Nicht unterstützt<br>
[Verschlüsselung des gesamten Arbeitsspeichers – Multi Key](https://itpeernetwork.intel.com/memory-encryption/): Aktiviert<br>
[Storage Premium](premium-storage-performance.md): Unterstützt<br>
[Disk Storage Ultra](disks-enable-ultra-ssd.md): Unterstützt<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Unterstützt (nur CLI-Bereitstellung oder ARM-Vorlage)<br>
[Azure Kubernetes Service](../aks/intro-kubernetes.md): Unterstützt (CLI-Bereitstellung nur anfänglich)<br>
[Livemigration:](maintenance-and-updates.md) Nicht unterstützt<br>
[Updates mit Speicherbeibehaltung:](maintenance-and-updates.md) Nicht unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 2<br>
[Kurzlebige Betriebssystemdatenträger](ephemeral-os-disks.md): Unterstützt <br>
[Dedicated Host](dedicated-hosts.md): In Kürze verfügbar<br>

## <a name="dcsv3-series-technical-specifications"></a>Technische Spezifikationen der DCsv3-Serie

| Size             | Physische Kerne | Arbeitsspeicher (GB) | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs |  EPC-Speicher (GB) |
|------------------|----------------|-------------|------------------------|----------------|---------|---------------------|
| Standard_DC1s_v3 | 1              | 8           | N/V                    | 4              | 2     |  4                 |
| Standard_DC2s_v3 | 2              | 16          | –                    | 8              | 2     |  8                 |
| Standard_DC4s_v3 | 4              | 32          | –                    | 16             | 4     |  16                |
| Standard_DC8s_v3 | 8              | 64          | –                    | 32             | 8     |  32                |
| Standard_DC16s_v3  | 16           | 128         | –                    | 32             | 8     |  64                |
| Standard_DC24s_v3  | 24           | 192         | –                    | 32             | 8     |  128               |
| Standard_DC32s_v3  | 32           | 256         | –                    | 32             | 8     |  192               |
| Standard_DC48s_v3  | 48           | 384         | –                    | 32             | 8     |  256               |

## <a name="dcdsv3-series-technical-specifications"></a>Technische Spezifikationen der DCdsv3-Serie

| Size             | Physische Kerne | Arbeitsspeicher (GB) | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs |  EPC-Speicher (GB) |
|------------------|----------------|-------------|------------------------|----------------|---------|---------------------|
| Standard_DC1ds_v3 | 1              | 8           | 75                    | 4              | 2     |  4                 |
| Standard_DC2ds_v3 | 2              | 16          | 150                    | 8              | 2     |  8                 |
| Standard_DC4ds_v3 | 4              | 32          | 300                    | 16             | 4     |  16                |
| Standard_DC8ds_v3 | 8              | 64          | 600                    | 32             | 8     |  32                |
| Standard_DC16ds_v3  | 16           | 128         | 1200                    | 32             | 8     |  64                |
| Standard_DC24ds_v3  | 24           | 192         | 1800                    | 32             | 8     |  128               |
| Standard_DC32ds_v3  | 32           | 256         | 2400                    | 32             | 8     |  192               |
| Standard_DC48ds_v3  | 48           | 384         | 2400                    | 32             | 8     |  256               |

## <a name="get-started"></a>Erste Schritte

- Erstellen von VMs der DCsv3- und DCdsv3-Serie im [Azure-Portal](./linux/quick-create-portal.md)
- VMs der DCsv3- und DCdsv3-Serie sind [VMs der 2. Generation](./generation-2.md#creating-a-generation-2-vm), die nur `Gen2`-Images unterstützen.
- Derzeit verfügbar in den Regionen, die unter [Azure-Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all) aufgeführt sind.

## <a name="more-sizes-and-information"></a>Weitere Größen und Informationen

- [Allgemeiner Zweck](sizes-general.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [GPU-optimiert](sizes-gpu.md)
- [High Performance Computing](sizes-hpc.md)
- [Vorherige Generationen](sizes-previous-gen.md)
- [Preisrechner](https://azure.microsoft.com/pricing/calculator/)

Preisrechner: [Preisrechner](https://azure.microsoft.com/pricing/calculator/)

Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.
