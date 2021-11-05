---
title: Skalieren von HPC-Anwendungen – Azure Virtual Machines | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie HPC-Anwendungen auf virtuellen Azure-Computern skalieren.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 408f55592ee7a192172c85e7a417db0b277f0f46
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054512"
---
# <a name="scaling-hpc-applications"></a>Skalieren von HPC-Anwendungen

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Um beim zentralen und horizontalen Skalieren von HPC-Anwendungen in Azure eine optimale Leistung zu erzielen, müssen für die betreffende Workload Experimente zur Leistungsoptimierung durchgeführt werden. In diesem Abschnitt sowie auf den spezifischen Seiten der jeweiligen VM-Serie finden Sie allgemeine Informationen zum Skalieren Ihrer Anwendungen.

## <a name="application-setup"></a>Anwendungseinrichtung
Das [azurehpc](https://github.com/Azure/azurehpc)-Repository enthält viele Beispiele für:
- Optimale Einrichtung und Ausführung von [Anwendungen](https://github.com/Azure/azurehpc/tree/master/apps).
- Konfiguration von [Dateisystemen und Clustern](https://github.com/Azure/azurehpc/tree/master/examples).
- [Tutorials](https://github.com/Azure/azurehpc/tree/master/tutorials) zum einfachen Einstieg in einige gängige Anwendungs-Workflows.

## <a name="optimally-scaling-mpi"></a>Optimale MPI-Skalierung 

Die folgenden Vorschläge dienen zur Optimierung der Skalierungseffizienz, Leistung und Konsistenz von Anwendungen:

- Verwenden Sie für kleinere Aufträge (d. h. weniger als 256.000 Verbindungen) die folgende Option:
   ```bash
   UCX_TLS=rc,sm
   ```

- Verwenden Sie für größere Aufträge (d. h. mehr als 256.000 Verbindungen) die folgende Option:
   ```bash
   UCX_TLS=dc,sm
   ```

- Berechnen Sie die Anzahl der Verbindungen für Ihren MPI-Auftrag wie folgt:
   ```bash
   Max Connections = (processes per node) x (number of nodes per job) x (number of nodes per job) 
   ```

## <a name="adaptive-routing"></a>Adaptives Routing
Adaptives Routing (AR) ermöglicht Azure Virtual Machines (VMs), EDR und auszuführen, und HDR InfiniBand das automatische Erkennen und Vermeiden von Netzwerküberlastung durch dynamisches Auswählen optimaler Netzwerkpfade. Demzufolge bietet AR eine verbesserte Latenz und Bandbreite im InfiniBand-Netzwerk, das wiederum höhere Leistung und Skalierbarkeit ermöglicht. Ausführlichere Informationen finden Sie im [Artikel TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/adaptive-routing-on-azure-hpc/ba-p/1205217).

## <a name="process-pinning"></a>Feste Prozesszuordnung

- Ordnen Sie Prozesse Kernen mithilfe eines Ansatzes für sequenzielle Zuordnung zu (anstelle eines Ansatzes für einen automatischen Ausgleich). 
- Die Bindung nach NUMA/Kern/HW-Thread ist besser als die Standardbindung.
- Verwenden Sie für parallele Hybridanwendungen (OpenMP + MPI) vier Threads und einen MPI-Rang pro CCX auf VM-Größen der HB- und HBv2-Serien.
- Experimentieren Sie bei reinen MPI-Anwendungen mit einem bis vier MPI-Rängen pro CCX auf VM-Größen der HB- und HBv2-Serien, um eine optimale Leistung zu erzielen.
- Anwendungen, die in puncto Speicherbandbreite besonders empfindlich sind, profitieren ggf. von der Verwendung einer geringeren Anzahl von Kernen pro CCX. Bei diesen Anwendungen lässt sich durch die Verwendung von drei oder zwei Kernen pro CCX unter Umständen das Ringen um Speicherbandbreite minimieren und in der Praxis eine höhere Leistung oder eine höhere Skalierungskonsistenz erzielen. Von diesem Ansatz kann insbesondere MPI Allreduce profitieren.
- Bei deutlich umfangreicheren Ausführungen empfiehlt sich die Verwendung von UD-Transporten oder Hybridtransporten (RC + UD). Dies wird von vielen MPI-/Runtimebibliotheken intern gehandhabt (beispielsweise bei UCX oder MVAPICH2). Überprüfen Sie Ihre Transportkonfigurationen für umfangreiche Ausführungen.

## <a name="compiling-applications"></a>Kompilieren von Anwendungen
<br>
<details>
<summary>Zum Erweitern klicken</summary>

Anwendungen müssen zwar nicht unbedingt mit entsprechenden Optimierungsflags kompiliert werden, diese Vorgehensweise bietet jedoch die beste Leistung beim zentralen Hochskalieren von virtuellen Computern der HB- und HC-Serie.

### <a name="amd-optimizing-cc-compiler"></a>AMD Optimizing C/C++ Compiler

Das AOCC-Compilersystem (AMD Optimizing C/C++ Compiler) zeichnet sich durch umfangreiche erweiterte Optimierungen, Multithreading und eine Prozessorunterstützung aus, die sowohl die globale Optimierung als auch Vektorisierung, prozessübergreifende Analysen, Schleifentransformationen und Codegenerierung umfasst. AOCC-Binärdateien eignen sich für Linux-Systeme mit der GNU C-Bibliothek (glibc) ab Version 2.17. Die Compilersuite besteht aus einem C/C++-Compiler (Clang), einem Fortran-Compiler (FLANG) und einem Fortran-Front-End für Clang (DragonEgg).

### <a name="clang"></a>Clang

Clang ist ein C-, C++- und Objective-C-Compiler für die Verarbeitung, Analyse, Optimierung, Codegenerierung, Assembly und Verknüpfung. Clang unterstützt das Flag `-march=znver1`, um die bestmögliche Codegenerierung und Optimierung für die Zen-basierte x86-Architektur von AMD zu ermöglichen.

### <a name="flang"></a>FLANG

Der FLANG-Compiler wurde der AOCC erst vor Kurzem (im April 2018) hinzugefügt und kann derzeit von Entwicklern als Vorabversion heruntergeladen und getestet werden. AMD erweitert die GitHub-Version von FLANG (https://github.com/flang-compiler/flang) auf der Grundlage von Fortran 2008. Der FLANG-Compiler unterstützt alle Clang-Compileroptionen sowie zahlreiche FLANG-spezifische Compileroptionen.

### <a name="dragonegg"></a>DragonEgg

DragonEgg ist ein GCC-Plug-In, das die GCC-Optimierer und -Codegeneratoren durch Optimierer und Codegeneratoren aus dem LLVM-Projekt ersetzt. DragonEgg ist in AOCC enthalten. Das Plug-In kann mit gcc-4.8.x verwendet werden, wurde für Ziele vom Typ x86-32/x86-64 getestet und wird bereits erfolgreich auf verschiedenen Linux-Plattformen eingesetzt.

GFortran ist das eigentliche Front-End für Fortran-Programme und dient zur Vorverarbeitung und (semantischen) Analyse beim Generieren der GCC GIMPLE-IR (Intermediate Representation). DragonEgg ist ein GNU-Plug-In für den GFortran-Kompilierungsfluss. Es implementiert die GNU-Plug-In-API. Mit der Plug-In-Architektur wird DragonEgg zum Compilertreiber, der die verschiedenen Kompilierungsphasen steuert.  Nach Ausführung der Download- und Installationsanweisungen kann DragonEgg wie folgt aufgerufen werden: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI-Compiler
Es wurde bestätigt, dass PGI Community Edition 17 mit AMD EPYC-Prozessoren funktioniert. Eine PGI-kompilierte Version von STREAM stellt die vollständige Speicherbandbreite der Plattform bereit. Die neuere Community Edition 18.10 (November 2018) sollte ebenso problemlos funktionieren. Hier sehen Sie ein CLI-Beispiel zur optimalen Kompilierung mit dem Intel-Compiler:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel-Compiler
Es wurde bestätigt, dass Intel Compiler 18 mit AMD EPYC-Prozessoren funktioniert. Hier sehen Sie ein CLI-Beispiel zur optimalen Kompilierung mit dem Intel-Compiler:

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC-Compiler 
Für HPC empfiehlt AMD mindestens die Version 7.3 des GCC-Compilers. Ältere Versionen (etwa die in RHEL/CentOS 7.4 enthaltene Version 4.8.5) werden nicht empfohlen. Mit GCC 7.3 und neueren Versionen wird bei HPL-, HPCG- und DGEMM-Tests eine deutlich höhere Leistung erzielt.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```
</details>

## <a name="next-steps"></a>Nächste Schritte

- Testen Sie Ihr Wissen mit einem [Lernmodul zur Optimierung von HPC-Anwendungen in Azure](/learn/modules/optimize-tightly-coupled-hpc-apps/).
- Weitere Informationen finden Sie in der [Übersicht über virtuelle Computer der HBv3-Serie](hbv3-series-overview.md) und in der [Übersicht über virtuelle Computer der HC-Serie](hc-series-overview.md).
- Informieren Sie sich über die neuesten Ankündigungen, HPC-Workloadbeispiele und Leistungsergebnisse in den [Tech Community-Blogs zu Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Informieren Sie sich ausführlicher über [HPC](/azure/architecture/topics/high-performance-computing/) in Azure.