---
title: Größen von Azure-VMs – Arbeitsspeicher | Microsoft-Dokumentation
description: Liste der verschiedenen verfügbaren arbeitsspeicheroptimierten Größen für virtuelle Computer in Azure Dieser Artikel listet Informationen zur Anzahl von vCPUs, Datenträgern und Netzwerkschnittstellenkarten sowie zum Speicherdurchsatz und zur Netzwerkbandbreite für Größen dieser Serie auf.
services: virtual-machines
documentationcenter: ''
author: brbell
tags: azure-resource-manager,azure-service-management
keywords: VM-Isolation,isolierte VM,Isolation,isoliert
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/20/2021
ms.author: brbell
ms.openlocfilehash: 50a187435b1a47fe2559ce5ce2d36905570b8419
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456592"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Arbeitsspeicheroptimierte Größen virtueller Computer

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

> [!TIP]
> Probieren Sie das **[VM-Auswahltool](https://aka.ms/vm-selector)** aus, um andere Größen zu ermitteln, die für Ihre Workload optimal sind.

Arbeitsspeicheroptimierte VM-Größen bieten ein hohes Arbeitsspeicher-zu-CPU-Verhältnis und eignen sich hervorragend für relationale Datenbankserver, mittelgroße bis große Caches und In-Memory-Analysen. Dieser Artikel enthält Informationen zur Anzahl von vCPUs, Datenträgern und NICs sowie zum Speicherdurchsatz und zur Netzwerkbandbreite der einzelnen Größen in dieser Gruppe.

- Die [Serien Dv2 und DSv2](dv2-dsv2-series-memory.md), Nachfolger der ursprünglichen D-Serie, weisen eine leistungsfähigere CPU auf. Die Dv2-Serie ist ca. 35 % schneller als die D-Serie. Sie wird auf dem Intel&reg; Xeon&reg;-Prozessor 8171M mit 2,1 GHz (Skylake), dem Intel&reg; Xeon&reg;-Prozessor E5-2673 v4 mit 2,3 GHz (Broadwell) oder dem Intel&reg; Xeon&reg;-Prozessor E5-2673 v3 mit 2,4 GHz (Haswell) und mit Intel Turbo Boost Technology 2.0 ausgeführt. Der Dv2-Serie hat die gleichen Arbeitsspeicher- und Datenträgerkonfigurationen wie die D-Serie.

    Die Serien Dv2 und DSv2 eignen sich ideal für Clientanwendungen, die schnellere vCPUs und eine höhere Leistung des temporären Speichers erfordern oder einen höheren Arbeitsspeicherbedarf haben. Sie bieten eine leistungsfähige Kombination für viele Anwendungen für den Unternehmenseinsatz.

- Die [Serien Eav4 und Easv4](eav4-easv4-series.md) verwenden den AMD-Prozessor EPYC<sup>TM</sup> 7452 mit 2,35 GHz in einer Multithreadkonfiguration mit bis zu 256 MB L3-Cache, sodass bei der Ausführung der meisten arbeitsspeicheroptimierten Workloads mehr Optionen zur Verfügung stehen. Die Eav4-Serie und die Easv4-Serie verfügen über die gleichen Arbeitsspeicher- und Datenträgerkonfigurationen wie die Ev3- und die Esv3-Serie.

- Die [Serien Ev3 und Esv3](ev3-esv3-series.md) bieten den Intel&reg; Xeon&reg;-Prozessor 8171M mit 2,1 GHz (Skylake) oder den Intel&reg; Xeon&reg;-Prozessor E5-2673 v4 mit 2,3 GHz (Broadwell) in einer Hyperthreadkonfiguration und somit ein besseres Preis-Leistungs-Verhältnis für die meisten universellen Workloads und besseren Einklang von Ev3 mit den universellen VMs der meisten anderen Clouds. Der Speicher wurde erweitert (von 7 GiB/vCPU auf 8 GiB/vCPU), während die Datenträger- und Netzwerkgrenzwerte pro Kern angepasst wurden, um auf den Übergang zum Hyperthreading vorbereitet zu sein. Die Ev3-Serie ist der Nachfolger für die VMs mit großen Arbeitsspeichergrößen der D/Dv2-Familien.

- [Die Ev4- und die Esv4-Serie](ev4-esv4-series.md) werden auf Intel&reg; Xeon&reg; Platinum 8272CL-Prozessoren der zweiten Generation (Cascade Lake) in einer Hyperthreadkonfiguration ausgeführt, eignen sich optimal für verschiedene arbeitsspeicherintensive Unternehmensanwendungen und unterstützen zu bis zu 504 GiB RAM. Sie verfügen über [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) und [Intel&reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Die Ev4- und die Esv4-Serie enthalten keinen lokalen temporären Datenträger. Weitere Informationen finden Sie unter [Azure-VM-Größen ohne lokale temporäre Datenträger](azure-vms-no-temp-disk.yml).

- Die [Edv4- und die Edsv4-Serie](edv4-edsv4-series.md) werden auf Intel&reg; Xeon&reg; Platinum 8272CL-Prozessoren der zweiten Generation (Cascade Lake) ausgeführt und eignen sich optimal für besonders große Datenbanken oder andere Anwendungen, die von einer hohen vCPU-Anzahl und umfangreichem Speicher profitieren. Darüber hinaus umfassen diese VM-Größen einen schnellen, größeren lokalen SSD-Speicher für Anwendungen, die von lokalem Hochgeschwindigkeitsspeicher mit geringer Latenz profitieren. Sie verfügen über eine Turbo-Taktfrequenz von 3,4 GHz für alle Kerne, [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) und [Intel&reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html).

- Die [Easv5-Serie und die Eadsv5-Serie](easv5-eadsv5-series.md) nutzen den AMD-Prozessor EPYC<sup>TM</sup> 7763v der 3. Generation in einer Multithreadkonfiguration mit bis zu 256 MB L3-Cache und erweitern damit die Kundenoptionen für die Ausführung von arbeitsspeicheroptimierten Workloads. Diese virtuellen Computer bieten eine Kombination aus vCPUs und Arbeitsspeicher, um die Anforderungen zu erfüllen, die mit den meisten speicherintensiven Unternehmensanwendungen verbunden sind, z. B. relationale Datenbankserver und In-Memory-Analyseworkloads. 

- Die [Edv5- und Edsv5-Serie](edv5-edsv5-series.md) läuft auf Intel-Prozessoren des Typs &reg; Xeon&reg; Platinum 8272CL (Ice Lake) in einer Hyperthreadkonfiguration. Sie eignet sich ideal für verschiedene arbeitsspeicherintensive Unternehmensanwendungen und bietet bis zu 512 GiB RAM, [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) and [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Außerdem unterstützen Sie [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Diese neuen VM-Größen bieten 50 Prozent mehr lokalen Speicher sowie bessere IOPS auf lokalen Datenträgern für Lese- und Schreibvorgänge im Vergleich zu den Größen [Ev3/Esv3](./ev3-esv3-series.md) mit [Gen2-VMs](./generation-2.md). Sie verfügen über eine Turbo-Taktfrequenz von 3,4 GHz für alle Kerne.

- Die [Ev5- und Esv5-Serie](ev5-esv5-series.md) werden auf den Prozessoren Intel&reg; Xeon&reg; Platinum 8272CL (Ice Lake) in einer Hyperthreadingkonfiguration ausgeführt, eignen sich ideal für verschiedene arbeitsspeicherintensive Unternehmensanwendungen und unterstützen zu bis zu 512 GiB RAM. Sie verfügen über eine Turbo-Taktfrequenz von 3,4 GHz für alle Kerne.

- Die [M-Serie](m-series.md) verfügt über eine hohe vCPU-Anzahl (bis zu 128 vCPUs) und eine große Menge an Arbeitsspeicher (bis zu 3,8 TiB). Dies ist auch ideal für extrem große Datenbanken oder andere Anwendungen, für die eine hohe vCPU-Anzahl und große Mengen an Arbeitsspeicher benötigt werden.

- Die [Mv2-Serie](mv2-series.md) bietet die höchste vCPU-Anzahl (bis zu 416 vCPUs) und den größten Arbeitsspeicher (bis zu 11,4 TiB) für die virtuellen Computer in der Cloud. Dies ist ideal für extrem große Datenbanken oder andere Anwendungen, für die eine hohe vCPU-Anzahl und große Mengen an Arbeitsspeicher benötigt werden.

Azure Compute bietet VM-Größen, die für einen bestimmten Hardwaretyp isoliert und für einen einzelnen Kunden bestimmt sind. Diese VM-Größen eignen sich am besten für Workloads, die ein hohes Maß an Isolation von anderen Kunden erfordern, wenn es um Workloads mit Elementen wie Konformität und gesetzlichen Anforderungen geht. Kunden können auch die Ressourcen dieser isolierten virtuellen Computer weiter unterteilen, indem sie die [Azure-Unterstützung für geschachtelte virtuelle Computer](https://azure.microsoft.com/blog/nested-virtualization-in-azure/) verwenden. Auf den Seiten für die VM-Familien finden Sie Optionen für Ihre isolierten VMs.

## <a name="other-sizes"></a>Andere Größen

- [Allgemeiner Zweck](sizes-general.md)
- [Computeoptimiert](sizes-compute.md)
- [Speicheroptimiert](sizes-storage.md)
- [GPU-optimiert](sizes-gpu.md)
- [High Performance Computing](sizes-hpc.md)
- [Vorherige Generationen](sizes-previous-gen.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.

Weitere Informationen dazu, wie VM-Namen in Azure zugewiesen werden, finden Sie unter [Namenskonventionen für Azure-VM-Größen](./vm-naming-conventions.md).
