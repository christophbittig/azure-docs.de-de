---
title: Größen von virtuellen Azure-Computern – Speicher | Microsoft-Dokumentation
description: Auflistung der verschiedenen verfügbaren speicheroptimierten Größen für virtuelle Computer in Azure. Dieser Artikel listet Informationen zur Anzahl von vCPUs, Datenträgern und Netzwerkschnittstellenkarten sowie zum Speicherdurchsatz und zur Netzwerkbandbreite für Größen dieser Serie auf.
ms.subservice: vm-sizes-storage
documentationcenter: ''
author: sasha-melamed
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: a6210583dc450fa31cd0e76c158cdab40e1c578b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695740"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Speicheroptimierte VM-Größen

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

> [!TIP]
> Probieren Sie das **[VM-Auswahltool](https://aka.ms/vm-selector)** aus, um andere Größen zu ermitteln, die für Ihre Workload optimal sind.

Datenspeicheroptimierte VM-Größen bieten hohen Datenträgerdurchsatz und E/A und eignen sich ideal für Big Data, SQL, NoSQL-Datenbanken, Datawarehousing und große transaktionale Datenbanken.  Beispiele bilden Cassandra, MongoDB, Cloudera und Redis. Dieser Artikel enthält Informationen zur Anzahl von vCPUs, Datenträgern und NICs sowie zum lokalen Speicherdurchsatz und zur Netzwerkbandbreite für die einzelnen optimierten Größen.

Die [Lsv2-Reihe](lsv2-series.md) bietet direkt zugeordneten lokalen NVMe-Speicher mit hohem Durchsatz und geringer Latenz auf dem [AMD EPYC<sup>TM</sup> 7551-Prozessor](https://www.amd.com/en/products/epyc-7000-series) mit einer Turbo-Taktfrequenz von 2,55 GHz für alle Kerne und einer maximalen Turbo-Taktfrequenz von 3,0 GHz. Die VMs der Lsv2-Reihe sind in Größen von 8 bis 80 vCPUs in einer Konfiguration mit simultanem Multithreading verfügbar.  Es stehen 8 GiB Arbeitsspeicher pro vCPU und ein NVMe-SSD-M.2-Gerät mit 1,92 TB pro 8 vCPUs zur Verfügung, mit bis zu 19,2 TB (10x1,92 TB) auf dem L80s v2.

## <a name="other-sizes"></a>Andere Größen

- [Allgemeiner Zweck](sizes-general.md)
- [Computeoptimiert](sizes-compute.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [GPU-optimiert](sizes-gpu.md)
- [High Performance Computing](sizes-hpc.md)
- [Vorherige Generationen](sizes-previous-gen.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.

Erfahren Sie, wie Sie die Leistung von virtuellen Computern der Lsv2-Reihe für [Windows](windows/storage-performance.md) oder [Linux](linux/storage-performance.md) optimieren.

Weitere Informationen dazu, wie VM-Namen in Azure zugewiesen werden, finden Sie unter [Namenskonventionen für Azure-VM-Größen](./vm-naming-conventions.md).