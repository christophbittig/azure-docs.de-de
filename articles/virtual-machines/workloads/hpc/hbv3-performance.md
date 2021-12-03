---
title: Leistung und Skalierbarkeit der VM-Größen der HBv3-Serie
description: Hier erfahren Sie mehr über Leistung und Skalierbarkeit von VM-Größen der HBv3-Serie in Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: bb777c952e4166b764032317f859419ebc3c94dd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312018"
---
# <a name="hbv3-series-virtual-machine-performance"></a>VM-Leistung der HBv3-Serie

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Gemäß allgemeiner HPC-Mikrobenchmarks lauten die Leistungserwartungen wie folgt:

| Workload                                        | HBv3                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| STREAM Triad                                    | 330–350 GB/s (~82–86 GB/s pro NUMA)                                     |
| High-Performance Linpack (HPL)                  | 4 TF (Rpeak, FP64), 8 TF (Rpeak, FP32) für VM-Größen mit 120 Kernen               |
| RDMA-Wartezeit und -Bandbreite                        | 1,2 Mikrosekunden (1 Byte), 192 GB/s (unidirektional)                                        |
| FIO auf lokalen NVMe-SSDs (RAID0)                  | 7 GB/s Lesevorgänge, 3 GB/s Schreibvorgänge; 186k IOPS-Lesevorgänge, 201k IOPS-Schreibvorgänge |

## <a name="process-pinning"></a>Feste Prozesszuordnung

Die [feste Prozesszuordnung](compiling-scaling-applications.md#process-pinning) eignet sich gut für VMs der HBv3-Serie, da der zugrunde liegende Chip unverändert für die Gast-VM zur Verfügung gestellt wird. Aus Leistungs- und Konsistenzgründen wird dringend empfohlen, Prozesse fest zuzuordnen.

## <a name="mpi-latency"></a>MPI-Latenz

Der MPI-Latenztest der OSU-Mikrobenchmarksuite kann wie unten gezeigt ausgeführt werden. Beispielskripts finden Sie auf [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
```

## <a name="mpi-bandwidth"></a>MPI-Bandbreite
Der MPI-Bandbreitentest der OSU-Mikrobenchmarksuite kann wie unten gezeigt ausgeführt werden. Beispielskripts finden Sie auf [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).
```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```
## <a name="mellanox-perftest"></a>Mellanox Perftest
Das Paket [Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) enthält viele InfiniBand-Tests, z. B. Latenz (ib_send_lat) und Bandbreite (ib_send_bw). Im Folgenden finden Sie einen Beispielbefehl.
```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```
## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über das [Skalieren von MPI-Anwendungen](compiling-scaling-applications.md).
- Die Leistungs- und Skalierbarkeitsergebnisse von HPC-Anwendungen auf HBv3-VMs finden Sie in diesem [TechCommunity-Artikel](https://techcommunity.microsoft.com/t5/azure-compute/hpc-performance-and-scalability-results-with-azure-hbv3-vms/bc-p/2235843).
- Informieren Sie sich über die neuesten Ankündigungen, HPC-Workloadbeispiele und Leistungsergebnisse in den [Tech Community-Blogs zu Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Eine allgemeinere Übersicht über die Architektur für die Ausführung von HPC-Workloads finden Sie unter [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
