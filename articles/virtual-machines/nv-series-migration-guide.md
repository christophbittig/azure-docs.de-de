---
title: 'NV-Serie: Migrationsleitfaden'
description: 'NV-Serie: Migrationsleitfaden'
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: 3fc9607a6882ddae42c2606abb60903dbcb05108
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436462"
---
# <a name="nv-series-migration-guide"></a>NV-Serie: Migrationsleitfaden

Da immer leistungsfähigere GPU-VM-Größen in Azure-Rechenzentren verfügbar werden, sollten Sie Ihre Workloads neu bewerten und die virtuellen Computer (VMs) der NV- und NV_Promo-Serie migrieren. Legacy-VMs können zu neuen VM-Serien (z. B. NVsv3 und NVasv4) migriert werden und so eine bessere Leistung bei geringeren Kosten erzielen. Die NVsv3-VM-Serie wird von Nvidia M60-GPUs unterstützt. Die NVasv4-Serie wird von AMD Radeon Instinct MI25-GPUs unterstützt.

Die Hauptunterschiede zwischen der NV- und NV_Promo-Serie und der neueren NVsv3- und NVasv4-Serie sind:
- Diese Ebene bietet eine verbesserte Leistung.
- Unterstützung für Storage Premium.
- Option zur Auswahl zwischen einer kleinen GPU-Größe und Konfigurationen mit mehreren GPUs.

Sowohl die NVsv3- als auch die NVasv4-Serie verfügen über modernere Kerne und eine höhere Kapazität.

Im folgenden Abschnitt werden die Unterschiede zwischen der älteren NV-Serie und der NVsv3- und NVv4-Serie zusammengefasst.
 
 ## <a name="nvsv3-series"></a>NVSv3-Serie 

Die VMs der NVv3-Serie verfügen über GPUs vom Typ NVIDIA Tesla M60 sowie NVIDIA GRID-Technologie mit Intel E5-2690 v4-CPUs (Broadwell) und Hyperthreadingtechnologie von Intel. Diese VMs sind für GPU-beschleunigte Grafikanwendungen und virtuelle Desktops gedacht, bei denen Kunden Folgendes wünschen:
- Visualisieren von Daten.
- Simulieren der anzuzeigenden Ergebnisse.
- Arbeiten mit CAD.
- Rendern und Streamen von Inhalten. 

Außerdem können diese VMs Workloads mit einfacher Genauigkeit wie Codierung und Rendering ausführen.

NVv3-VMs unterstützen Storage Premium und verfügen im Vergleich zur NV-Serie über doppelt so viel Systemarbeitsspeicher (RAM). Die aktuellsten Spezifikationen finden Sie unter [GPU-beschleunigte Compute-VM-Größen: NVsv3-Serie](nvv3-series.md).

| Aktuelle VM-Größe | Größe der Ziel-VM | Unterschied bei der Spezifikation  |
|---|---|---|
|Standard_NV6 <br> Standard_NV6_Promo |Standard_NV12s_v3  | vCPU: 12 (+6) <br> Arbeitsspeicher: GiB 112 (+56) <br> Temporärer Speicher (SSD) GiB: 320 (-20) <br> Max. Anzahl Datenträger: 12 (-12) <br> Beschleunigter Netzwerkbetrieb: Ja <br> Storage Premium: Ja  |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV24s_v3  | vCPU: 24 (+12) <br>Arbeitsspeicher: GiB 224 (+112) <br>Temporärer Speicher (SSD) GiB: 640 (-40)<br>Max. Anzahl Datenträger: 24 (-24)<br>Beschleunigter Netzwerkbetrieb: Ja <br>Storage Premium: Ja   |
|Standard_NV24 <br> Standard_NV24_Promo |Standard_NV48s_v3  | vCPU: 48 (+24) <br>Arbeitsspeicher: GiB 448 (+224) <br>Temporärer Speicher (SSD) GiB: 1280 (-160) <br>Max. Anzahl Datenträger: 32 (-32) <br>Beschleunigter Netzwerkbetrieb: Ja <br>Storage Premium: Ja   |

## <a name="nvv4-series"></a>NVv4-Serie 

VMs der NVv4-Serie basieren auf GPUs vom Typ AMD Radeon Instinct MI25 sowie auf CPUs vom Typ AMD EPYC 7V12 (Rome). Mit der NVv4-Serie führt Azure VMs mit partiellen GPUs ein. Wählen Sie die VM mit der passenden Größe für GPU-beschleunigte Grafikanwendungen und virtuelle Desktops aus – angefangen bei einer Achtel-GPU mit 2 GiB Framepuffer bis hin zu einer vollständigen GPU mit 16 GiB Framepuffer.

NVv4-VMs unterstützen derzeit nur das Windows-Gastbetriebssystem. Die aktuellsten Spezifikationen finden Sie unter [GPU-beschleunigte Compute-VM-Größen: NVv4-Serie](nvv4-series.md).

| Aktuelle VM-Größe | Größe der Ziel-VM | Unterschied bei der Spezifikation  |
|---|---|---|
|Standard_NV6 <br> Standard_NV6_Promo |Standard_NV16as_v4  | vCPU: 16 (+10) <br>Arbeitsspeicher: GiB 56  <br>Temporärer Speicher (SSD) GiB: 352 (+12) <br>Max. Anzahl Datenträger: 16 (-8) <br>Beschleunigter Netzwerkbetrieb: Ja <br>Storage Premium: Ja   |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV32as_v4  | vCPU: 32 (+20) <br>Arbeitsspeicher: GiB 112 <br>Temporärer Speicher (SSD) GiB: 704 (+24) <br>Max. Anzahl Datenträger: 32 (+16)<br>Beschleunigter Netzwerkbetrieb: Ja <br>Storage Premium: Ja   |
|Standard_NV24 <br> Standard_NV24_Promo |–  | –  |

## <a name="migration-steps-for-general-changes"></a>Migrationsschritte für allgemeine Änderungen

So behandeln Sie allgemeine Änderungen:

1. Wählen Sie eine Reihe und eine Größe für die Migration aus. 

1. Erhalten Sie ein Kontingent für die Ziel-VM-Serie.

1. Ändern Sie die Größe der aktuellen VM der NV-Serie auf die Zielgröße.

  Wenn die Zielgröße NVv4 ist, stellen Sie sicher, dass Sie den Nvidia-GPU-Treiber entfernen und den AMD-GPU-Treiber installieren.

## <a name="migration-steps-for-breaking-changes"></a>Migrationsschritte für wichtige Änderungen

Führen Sie die in den nächsten Abschnitten beschriebenen Schritte aus, um Breaking Changes zu behandeln.

### <a name="select-a-target-size-for-migration"></a>Auswählen einer Zielgröße für die Migration

Nachdem Sie Ihre aktuelle Nutzungsweise bewertet haben, können Sie entscheiden, welchen Typ von GPU-VM Sie benötigen. Abhängig von den Workloadanforderungen stehen Ihnen unterschiedliche Optionen zur Verfügung. So treffen Sie Ihre Auswahl:

- Wenn es sich um eine Grafik- oder Visualisierungsworkload handelt und eine harte Abhängigkeit zu der Nvidia-GPU besteht, empfehlen wir die Migration zur NVsv3-Serie.
- Wenn es sich um eine Grafik- oder Visualisierungsworkload handelt und leine harte Abhängigkeit zu einem bestimmten GPU-Typ besteht, wird eine Migration zur NVsv3- oder NVVasv4-Serie empfohlen.
 
> [!Note]
>Eine bewährte Methode besteht in der Auswahl einer VM-Größe basierend auf Kosten und Leistung. Die Empfehlungen in diesem Artikel basieren auf einem 1:1-Vergleich der Leistungsmetriken von den Größen NV und NV_Promo und der nächstmöglichen Übereinstimmung in einer anderen VM-Serie.
>Bevor Sie sich für die richtige Größe entscheiden, können Sie mithilfe des Azure-Preisrechners einen Kostenvergleich durchführen.

### <a name="get-a-quota-for-the-target-vm-family"></a>Erhalten eines Kontingents für die VM-Zielfamilie 

Befolgen Sie die Anleitung zum [Anfordern einer Erhöhung des vCPU-Kontingents nach VM-Familie](../azure-portal/supportability/per-vm-quota-requests.md). Wählen Sie die NVSv3-Serie oder die NVv4-Serie als VM-Familiennamen aus. Dies ist abhängig von der Größe der Ziel-VM, die Sie für die Migration ausgewählt haben.

### <a name="resize-the-current-vm"></a>Ändern der Größe der aktuellen VM

Sie können [die Größe der VM über das Azure-Portal oder mit PowerShell ändern](./windows/resize-vm.md). Sie können [die Größe der VM auch mithilfe der Azure CLI ändern](./linux/change-vm-size.md). 

## <a name="faq"></a>Häufig gestellte Fragen
**F:** Welchen GPU-Treiber sollte ich für die Größe des virtuellen Zielcomputers verwenden? 

**A:** Verwenden Sie den [Nvidia GRID-Treiber](./windows/n-series-driver-setup.md) für die NVsv3-Serie. Verwenden Sie die [AMD GPU-Treiber](./windows/n-series-amd-driver-setup.md) für die NVv4-Serie. 

**F:** Ich verwende derzeit die NVIDIA-GPU-Treibererweiterung. Funktioniert dies auch für die Größe der Ziel-VM? 

**A:** Die aktuelle [Nvidia-Treibererweiterung](./extensions/hpccompute-gpu-windows.md) funktioniert für die NVsv3-Serie. Verwenden Sie die [AMD GPU-Treibererweiterung](./extensions/hpccompute-amd-gpu-windows.md) für Ziel-VMs der Größe NVv4. 
 
**F:** Welche VM-Serie sollte ich verwenden, wenn eine Abhängigkeit zu CUDA besteht? 

 **A:** NVv3 unterstützt CUDA. Von der NVv4-VM-Serie mit den AMD-GPUs wird CUDA nicht unterstützt.
