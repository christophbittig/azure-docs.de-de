---
title: Größen von Azure-VMs – für Compute optimiert | Microsoft-Dokumentation
description: Auflistung der verschiedenen für Compute optimierten Größen, die für virtuelle Computer in Azure verfügbar sind Dieser Artikel listet Informationen zur Anzahl von vCPUs, Datenträgern und Netzwerkschnittstellenkarten sowie zum Speicherdurchsatz und zur Netzwerkbandbreite für Größen dieser Serie auf.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: edc1dbd3d0c2250bf2dd4db870a9813602477073
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688645"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Compute-optimierte VM-Größen

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

> [!TIP]
> Probieren Sie das **[Auswahltool für virtuelle Computer](https://aka.ms/vm-selector)** aus, um andere Größen zu ermitteln, die für Ihre Workload optimal sind.

Für Compute optimierte VM-Größen verfügen über ein hohes Verhältnis zwischen CPU und Arbeitsspeicher. Diese Größen sind ideal für Webserver, Netzwerkappliances, Batchvorgänge und Anwendungsserver mit mittlerer Auslastung. Dieser Artikel enthält Informationen über die Anzahl von vCPUs, Datenträgern und NICs. Er umfasst zudem Informationen zum Speicherdurchsatz und zur Netzwerkbandbreite für die jeweiligen Größen in dieser Gruppe.

- Die [Fsv2-Serie](fsv2-series.md) wird auf dem Intel® Xeon® Platin 8272CL-Prozessor (Cascade Lake) der zweiten Generation und auf dem Intel® Xeon® Platinum 8168-Prozessor (Skylake) ausgeführt. Dieser verfügt über eine kontinuierliche Turbo-Taktfrequenz von 3,4 GHz für alle Kerne und eine maximale Turbofrequenz von 3,7 GHz für Einzelkerne. Intel® AVX-512-Anweisungen sind neu auf von Intel skalierbaren Prozessoren. Diese Anweisungen können bei Gleitkommaoperationen mit einfacher und doppelter Genauigkeit mit Vektorverarbeitungsworkloads die Leistung verdoppeln. Sie bewältigen demnach alle Rechenworkloads wirklich schnell. Die Fsv2-Serie hat einen niedrigeren Listenpreis pro Stunde und bietet auf Basis der Azure-Compute-Einheit (Azure Compute Unit, ACU) das beste Preis-Leistungs-Verhältnis pro vCPU im Azure-Portfolio.

- Die [FX-Serie](fx-series.md) wird auf den Prozessoren Intel® Xeon® Gold 6246R (Cascade Lake) ausgeführt. Sie verfügt über eine Turbofrequenz von 4,0 GHz auf allen Kernen, 21 GB RAM pro vCPU sowie bis zu 1 TB RAM und lokalen temporären Speicher. Davon profitieren Workloads, die eine hohe CPU-Taktgeschwindigkeit und einen hohen Arbeitsspeicheranteil pro CPU erfordern, Workloads mit hohen Lizenzierungskosten pro Kern sowie Anwendungen, die eine hohe Einzelkernleistung erfordern. Ein typischer Anwendungsfall für die FX-Serie stellt die Workload „Elektronische Entwurfsautomatisierung“ (Electronic Design Automation, EDA) dar.

## <a name="other-sizes"></a>Andere Größen

- [Allgemeiner Zweck](sizes-general.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [GPU-optimiert](sizes-gpu.md)
- [High Performance Computing](sizes-hpc.md)
- [Vorherige Generationen](sizes-previous-gen.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.

Weitere Informationen dazu, wie VM-Namen in Azure zugewiesen werden, finden Sie unter [Namenskonventionen für Azure-VM-Größen](./vm-naming-conventions.md).
