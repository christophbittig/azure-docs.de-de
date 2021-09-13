---
title: 'NV-Serie: Migrationsleitfaden'
description: 'NV-Serie: Migrationsleitfaden'
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: e91da8b052ba9ecce4a345c610b2299de22de1b3
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122350923"
---
#  <a name="nv-series-migration-guide"></a>NV-Serie: Migrationsleitfaden 

Da immer leistungsfähigere GPU-VM-Größen in Microsoft Azure-Rechenzentren verfügbar werden, empfehlen wir Ihnen, Ihre Workloads neu zu bewerten und die virtuellen Computer (VMs) der NV- und NV_Promo-Serie zu migrieren. Legacy-VMs können in neue VM-Serien (z. B. NVsv3 und NVasv4) migriert werden und so eine bessere Leistung bei geringeren Kosten erzielen. Die NVsv3-VM-Serie basiert auf den Nvidia M60-GPUs und die NVasv4-Serie auf den AMD Radeon Instinct MI25-GPUs.  Der Hauptunterschied zwischen der NV, NV_Promo-Serie und den neueren Serien NVsv3 und NVasv4 besteht in der verbesserten Leistung, der Unterstützung von Storage Premium und der Option, zwischen einer Bruchteil-GPU-Größe und einer Multi-GPU-Konfiguration zu wählen. Sowohl die NVsv3- als auch die NVasv4-Serie verfügen über modernere Kerne und eine höhere Kapazität.  

Im folgenden Abschnitt wird der Unterschied zwischen der älteren NV-Serie und der NVsv3- und NVv4-Serie zusammengefasst.
 
 ## <a name="nvsv3-series"></a>NVSv3-Serie 

Die virtuellen Computer der NVv3-Serie verfügen über GPUs vom Typ NVIDIA Tesla M60 sowie NVIDIA GRID-Technologie mit Intel E5-2690 v4-CPUs (Broadwell) und Hyperthreadingtechnologie von Intel. Diese virtuellen Computer wurden für GPU-beschleunigte Grafikanwendungen und virtuelle Desktops entwickelt, um Kunden die Datenvisualisierung, Ergebnissimulation, CAD oder das Rendering und Streaming von Inhalten zu erleichtern. Außerdem können diese virtuellen Computer Workloads mit einfacher Genauigkeit wie Codierung und Rendering ausführen. Virtuelle Computer der NVv3-Serie unterstützen Storage Premium und verfügen im Vergleich zur NV-Serie über doppelt so viel Systemarbeitsspeicher (RAM). Die aktuellsten Spezifikationen finden Sie unter [GPU-beschleunigte Compute-VM-Größen: NVsv3-Serie](nvv3-series.md)

| Aktuelle VM-Größe | Größe der Ziel-VM | Unterschied bei der Spezifikation  |
|---|---|---|
|Standard_NV6 <br> Standard_NV6_Promo |Standard_NV12s_v3  | vCPU: 12 (+6) <br> Arbeitsspeicher: GiB 112 (+56) <br> Temporärer Speicher (SSD) GiB: 320 (-20) <br> Max. Anzahl Datenträger: 12 (-12) <br> Beschleunigter Netzwerkbetrieb: Ja <br> Storage Premium: Ja  |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV24s_v3  | vCPU: 24 (+12) <br>Arbeitsspeicher: GiB 224 (+112) <br>Temporärer Speicher (SSD) GiB: 640 (-40)<br>Max. Anzahl Datenträger: 24 (-24)<br>Beschleunigter Netzwerkbetrieb: Ja <br>Storage Premium: Ja   |
|Standard_NV24 <br> Standard_NV24_Promo |Standard_NV48s_v3  | vCPU: 48 (+24) <br>Arbeitsspeicher: GiB 448 (+224) <br>Temporärer Speicher (SSD) GiB: 1280 (-160) <br>Max. Anzahl Datenträger: 32 (-32) <br>Beschleunigter Netzwerkbetrieb: Ja <br>Storage Premium: Ja   |

## <a name="nvsv4-series"></a>NVsv4-Serie 

Die virtuellen Computer der NVv4-Serie basieren auf GPUs vom Typ AMD Radeon Instinct MI25 sowie auf CPUs vom Typ AMD EPYC 7V12 (Rome). Mit der NVv4-Serie führt Azure virtuelle Computer mit partiellen GPUs ein. Wählen Sie den virtuellen Computer mit der passenden Größe für GPU-beschleunigte Grafikanwendungen und virtuelle Desktops aus – angefangen bei einer Achtel-GPU mit 2 GiB Framepuffer bis hin zu einer vollständigen GPU mit 16 GiB Framepuffer. Von virtuellen Computern der NVv4-Serie wird derzeit nur das Windows-Gastbetriebssystem unterstützt. Die aktuellsten Spezifikationen finden Sie unter [GPU-beschleunigte Compute-VM-Größen: NVsv4-Serie](nvv4-series.md)

| Aktuelle VM-Größe | Größe der Ziel-VM | Unterschied bei der Spezifikation  |
|---|---|---|
|Standard_NV6 <br> Standard_NV6_Promo |Standard_NV16as_v4  | vCPU: 16 (+10) <br>Arbeitsspeicher: GiB 56  <br>Temporärer Speicher (SSD) GiB: 352 (-12) <br>Max. Anzahl Datenträger: 16 (-8) <br>Beschleunigter Netzwerkbetrieb: Ja <br>Storage Premium: Ja   |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV32as_v4  | vCPU: 32 (+20) <br>Arbeitsspeicher: GiB 112 <br>Temporärer Speicher (SSD) GiB: 704 (+24) <br>Max. Anzahl Datenträger: 32 (+16)<br>Beschleunigter Netzwerkbetrieb: Ja <br>Storage Premium: Ja   |
|Standard_NV24 <br> Standard_NV24_Promo |–  | –  |

## <a name="migration-steps"></a>Schritte bei der Migration 
 

Allgemeine Änderungen 

1. Wählen Sie eine Reihe und eine Größe für die Migration aus. 

2. Erhalten Sie das Kontingent für die Ziel-VM-Serie 

3. Ändern Sie die Größe der aktuellen VM der NV-Serie auf die Zielgröße 

  Wenn die Zielgröße NVv4 ist, stellen Sie sicher, dass Sie den Nvidia-GPU-Treiber entfernen und den AMD-GPU-Treiber installieren. 
  
## <a name="breaking-changes"></a>Aktuelle Änderungen 

## <a name="select-target-size-for-migration"></a>Auswählen der Zielgröße für die Migration 
Nachdem Sie Ihre aktuelle Nutzung bewertet haben, können Sie entscheiden, welche GPU-VM Sie benötigen. Abhängig von den Workloadanforderungen stehen Ihnen unterschiedliche Optionen zur Verfügung. So treffen Sie Ihre Auswahl:  

Wenn es sich um eine Grafik-/Visualisierungsworkload handelt und eine harte Abhängigkeit zu der Nvidia-GPU besteht, empfehlen wir die Migration zur NVsv3-Serie.  

Wenn es sich um eine Grafik-/Visualisierungsworkload handelt und keine harte Abhängigkeit zu einer bestimmten GPU besteht, empfehlen wir die Migration zur NVsv3- oder NVVasv4-Serie. 
> [!Note]
>Die Auswahl einer VM-Größe basierend auf Kosten und Leistung hat sich als bewährte Methode erwiesen. Die Empfehlungen in diesem Leitfaden basieren auf einem 1:1-Vergleich der Leistungsmetriken von den Größen NV und NV_Promo und der nächstmöglichen Übereinstimmung in einer anderen VM-Serie.
>Bevor Sie sich für die richtige Größe entscheiden, können Sie mithilfe des Azure-Preisrechners einen Kostenvergleich durchführen.

## <a name="get-quota-for-the-target-vm-family"></a>Erhalten Sie das Kontingent für die Ziel-VM-Familie 

Befolgen Sie die Anleitung zum [Anfordern einer Erhöhung des vCPU-Kontingents nach VM-Familie](../azure-portal/supportability/per-vm-quota-requests.md). Wählen Sie „NVSv3-Serie“ oder „NVv4-Serie“ als VM-Familiennamen aus. Dies ist abhängig von der Größe der Ziel-VM, die Sie für die Migration ausgewählt haben.
## <a name="resize-the-current-virtual-machine"></a>Ändern Sie die Größe der aktuellen VM
Sie können [die Größe des virtuellen Computers über das Azure-Portal oder über PowerShell ändern](./windows/resize-vm.md). Sie können die Größe einer VM auch mit [Azure CLI](./linux/change-vm-size.md) anpassen. 

## <a name="faq"></a>Häufig gestellte Fragen
**F:** Welchen GPU-Treiber sollte ich für die Größe des virtuellen Zielcomputers verwenden? 

**A:** Verwenden Sie den [Nvidia GRID-Treiber](./windows/n-series-driver-setup.md) für die NVsv3-Serie. Verwenden Sie die [AMD GPU-Treiber](./windows/n-series-amd-driver-setup.md) für die NVv4-Serie. 

**F:** Ich verwende derzeit die NVIDIA-GPU-Treibererweiterung. Funktioniert dies auch für die Größe der Ziel-VM? 

**A:** Die aktuelle [Nvidia-Treibererweiterung](./extensions/hpccompute-gpu-windows.md) funktioniert für die NVsv3-Serie. Verwenden Sie die [AMD GPU-Treibererweiterung](./extensions/hpccompute-amd-gpu-windows.md) für Ziel-VMs der Größe NVv4. 
       
**F:** Welche VM-Serie sollte ich verwenden, wenn eine Abhängigkeit zu CUDA besteht? 

 **A:** NVv3 unterstützt CUDA. Von der NVv4-VM-Serie mit den AMD-GPUs wird CUDA nicht unterstützt.  
