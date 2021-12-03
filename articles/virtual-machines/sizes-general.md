---
title: Größen von virtuellen Azure-Computern – allgemeiner Zweck | Microsoft-Dokumentation
description: Auflistung der verschiedenen verfügbaren allgemeinen Größen für virtuelle Computer in Azure. Dieser Artikel listet Informationen zur Anzahl von vCPUs, Datenträgern und Netzwerkschnittstellenkarten sowie zum Speicherdurchsatz und zur Netzwerkbandbreite für Größen dieser Serie auf.
author: brbell
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/20/2021
ms.author: brbell
ms.openlocfilehash: 2243f53be7e90644bd389659ea1a920ac8b75770
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466825"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Universelle VM-Größen

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

> [!TIP]
> Probieren Sie das **[Auswahltool für virtuelle Computer](https://aka.ms/vm-selector)** aus, um andere Größen zu ermitteln, die für Ihre Workload optimal sind.

Universelle VM-Größen zeichnen sich durch ein ausgewogenes Verhältnis zwischen CPU und Arbeitsspeicher aus. Ideal für Tests und Entwicklung, kleine bis mittlere Datenbanken sowie Webserver mit geringer bis mittlerer Auslastung. Dieser Artikel enthält Informationen zu den Angeboten für allgemeines Computing.

- Die virtuellen Computer der [Av2-Reihe](av2-series.md) können auf vielen verschiedenen Hardwaretypen und Prozessoren bereitgestellt werden. Die Konfigurationen für CPU-Leistung und Arbeitsspeicher bei virtuellen Computern der A-Serie eignen sich am besten für Workloads wie Entwicklung und Tests. Die Größe ist basierend auf der Hardware gedrosselt, um eine konsistente Prozessorleistung für die ausgeführte Instanz zu ermöglichen – unabhängig von der Hardware, die für die Bereitstellung gewählt wird. Fragen Sie die virtuelle Hardware über die virtuelle Maschine ab, um die physische Hardware zu ermitteln, auf der diese Größe bereitgestellt wird. Mögliche Anwendungsfälle: Entwicklungs- und Testserver, Webserver mit geringem Datenverkehr, kleine bis mittelgroße Datenbanken, Proof of Concept und Coderepositorys.

  > [!NOTE]
  > Virtuelle Computer vom Typ „A8“, „A9“, „A10“ und A11“ werden voraussichtlich im März 2021 eingestellt. Weitere Informationen finden Sie im [HPC-Migrationsleitfaden](https://azure.microsoft.com/resources/hpc-migration-guide/). Diese VM-Größen gehören zur ursprünglichen Serie „A_v1“, nicht zu „V2“.

- [Virtuelle Burst-fähige Computer der B-Serie](sizes-b-series-burstable.md) sind ideal für Workloads geeignet, die nicht kontinuierlich die volle Leistung der CPU benötigen. Hierzu zählen beispielsweise Webserver, kleine Datenbanken sowie Entwicklungs- und Testumgebungen. Diese Workloads haben in der Regel kurzfristige Leistungsanforderungen. Mit der B-Serie können diese Kunden eine VM-Größe mit einer preisgünstigen Grundleistung erwerben. Für die VM-Instanz können dann Gutschriften erlangt werden, wenn für die VM weniger als die Grundleistung genutzt wird. Wenn für den virtuellen Computer Guthaben gebildet wurde, kann Leistung genutzt werden, die über die Grundleistung der VM hinausgeht. Dabei kann die CPU zu 100 Prozent verwendet werden, wenn Ihre Anwendung eine solche Leistung benötigt.

- Die [DCv2-Serie](dcv2-series.md) kann Ihnen beim Schutz der Vertraulichkeit und Integrität Ihrer Daten und Ihres Codes während der Verarbeitung in der öffentlichen Cloud helfen. Diese Computer werden von der neuesten Generation von Intel XEON E-2288G-Prozessoren mit SGX-Technologie unterstützt. Mit der Intel Turbo Boost Technology können diese Computer bis zu 5,0 GHz erreichen. Instanzen der DCv2-Serie ermöglichen es Kunden, Secure Enclave-basierte Anwendungen zu erstellen, um ihren Code und Daten zu schützen, während sie verwendet werden.

- Virtuelle Computer der [Dv2-Serie und der Dsv2-Serie](dv2-dsv2-series.md), eine Nachfolgerin der ursprünglichen D-Serie, sind mit leistungsfähigeren CPUs und einer optimalen CPU-zu-Arbeitsspeicher-Konfiguration ausgestattet und eignen sich daher für die meisten Produktionsworkloads. Die Dv2-Serie ist ca. 35 % schneller als die D-Serie. Die Dv2-Serie wird auf dem Intel® Xeon® Platinum 8272CL-Prozessor der 2. Generation (Cascade Lake), dem Intel® Xeon® 8171M-Prozessor mit 2,1 GHz (Skylake), dem Intel® Xeon® E5-2673 v4-Prozessor 2,3 GHz (Broadwell) oder dem Intel® Xeon® E5-2673 v3-Prozessor mit 2,4 GHz (Haswell) mit Intel Turbo Boost Technology 2.0 ausgeführt. Der Dv2-Serie hat die gleichen Arbeitsspeicher- und Datenträgerkonfigurationen wie die D-Serie.

- Die [Dv3- und Dsv3-Serie](dv3-dsv3-series.md) laufen auf dem Intel® Xeon® Platinum 8272CL-Prozessor der 2. Generation (Cascade Lake), dem Intel® Xeon® 8171M-Prozessor mit 2,1 GHz (Skylake), dem Intel® Xeon® E5-2673 v4-Prozessor 2,3 GHz (Broadwell) oder dem Intel® Xeon® E5-2673 v3-Prozessor mit 2,4 GHz (Haswell) in einer Hyperthreadkonfiguration und bieten somit ein besseres Preis-Leistungs-Verhältnis für die meisten universellen Workloads. Der Speicher wurde erweitert (von etwa 3.5 GiB/vCPU auf 4 GiB/vCPU), während die Datenträger- und Netzwerkgrenzwerte pro Kern angepasst wurden, um sich für den Übergang zum Hyperthreading anzupassen. Die Dv3-Serie hat nicht mehr die virtuellen Computer mit hohen Arbeitsspeichergrößen der D/Dv2-Serie. Diese sind nun in den arbeitsspeicheroptimierten [Serien Ev3 und Esv3](ev3-esv3-series.md) verfügbar.

- Die [Dav4-Serie und die Dasv4-Serie](dav4-dasv4-series.md) sind neue Größen, bei denen der AMD-Prozessor EPYC<sup>TM</sup> 7452 mit 2,35 GHz in einer Multithreadkonfiguration mit bis zu 256 MB L3-Cache verwendet wird. Hierbei werden 8 MB des L3-Caches jeweils acht Kernen zur Verfügung gestellt, um Kunden mehr Möglichkeiten bei der Ausführung ihrer universellen Workloads zu bieten. Die Dav4-Serie und die Dasv4-Serie verfügen über die gleichen Arbeitsspeicher- und Datenträgerkonfigurationen wie die D- und die Dsv3-Serie.

- Die [Dv4- und Dsv4-Serie](dv4-dsv4-series.md) laufen auf Intel-Prozessoren des Typs Intel® Xeon® Platinum 8272CL (Cascade Lake) mit einer Hyperthreading-Konfiguration, die für die meisten Allzweck-Workloads ein besseres Preis-Leistungs-Verhältnis bieten. Sie verfügen über eine Turbo-Taktfrequenz von 3,4 GHz für alle Kerne.

- Die [Ddv4- und Ddsv4-Serie](ddv4-ddsv4-series.md) laufen auf Intel-Prozessoren des Typs Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) mit einer Hyperthreading-Konfiguration, die für die meisten Allzweck-Workloads ein besseres Preis-Leistungs-Verhältnis bieten. Sie verfügen über eine Turbo-Taktfrequenz von 3,4 GHz für alle Kerne, [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) und [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Außerdem unterstützen sie [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Diese neuen VM-Größen bieten 50  % mehr lokalen Speicher sowie bessere IOPS auf lokalen Datenträgern für Lese- und Schreibvorgänge im Vergleich zu den Größen [Dv3/Dsv3](./dv3-dsv3-series.md) mit [Gen2-VMs](./generation-2.md).

- Die [Dasv5- und Dadsv5-Serie](dasv5-dadsv5-series.md) nutzen den AMD-Prozessor EPYC<sup>TM</sup> 7763v der 3. Generation in einer Multithreadkonfiguration mit bis zu 256 MB L3-Cache und erweitern damit die Kundenoptionen für die Ausführung allgemeiner Workloads. Diese VMs bieten eine Kombination aus vCPUs und Arbeitsspeicher, um die Anforderungen der meisten Unternehmensworkloads zu erfüllen, z. B. kleine bis mittlere Datenbanken, Webserver mit geringem bis mittlerem Datenverkehr, Anwendungsserver und vieles mehr.

- Die [Dv5- und Dsv5-Serien](dv5-dsv5-series.md) laufen auf dem Prozessor Intel® Xeon® Platinum 8370C (Ice Lake) der 3. Generation in einer Hyperthreadkonfiguration. VMs in Größe der Dv5- und Dsv5-Serien verfügen nicht über temporären Speicher, wodurch der Preis für den Einstieg gesenkt wird. Die VM in Größe Dv5 bietet eine Kombination aus vCPUs und Arbeitsspeicher, um die Anforderungen der meisten Unternehmensworkloads erfüllen zu können, z. B. kleine bis mittlere Datenbanken, Webserver mit geringem bis mittlerem Datenverkehr, Anwendungsserver und vieles mehr. 

- Die [Ddv5- und Ddsv5-Serien](ddv5-ddsv5-series.md) laufen auf den Prozessoren Intel&reg; Xeon&reg; Platinum 8370C (Ice Lake) der 3. Generation in einer Hyperthreadkonfiguration, die ein besseres Preis-Leistungs-Verhältnis für die meisten Allzweckworkloads bereitstellt. Dieser neue Prozessor verfügt über eine Turbotaktfrequenz von 3,5 GHz, [Intel&reg; Hyperthreading-Technologie](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html), [Intel&reg; Turbo Boost Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Advanced-Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) und [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html).


## <a name="other-sizes"></a>Andere Größen

- [Computeoptimiert](sizes-compute.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [GPU-optimiert](sizes-gpu.md)
- [High Performance Computing](sizes-hpc.md)
- [Vorherige Generationen](sizes-previous-gen.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.

Weitere Informationen dazu, wie VM-Namen in Azure zugewiesen werden, finden Sie unter [Namenskonventionen für Azure-VM-Größen](./vm-naming-conventions.md).
