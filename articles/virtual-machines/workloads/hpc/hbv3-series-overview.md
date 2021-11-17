---
title: 'Übersicht über virtuelle Computer der HBv3-Serie: Architektur, Topologie, Azure Virtual Machines | Microsoft-Dokumentation'
description: Hier erfahren Sie mehr über die Größe virtueller Computer der HBv3-Serie in Azure.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: fb7604476fb323d829f0c70253dee1138c8bd7ee
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277161"
---
# <a name="hbv3-series-virtual-machine-overview"></a>Übersicht über virtuelle Computer der HBv3-Serie 

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Ein [Server der HBv3-Serie](../../hbv3-series.md) verfügt über 2 EPYC 7V13-CPUs mit je 64 Kernen für eine Gesamtanzahl von 128 physischen Zen3-Kernen. Das simultane Multithreading (SMT) ist bei HBv3 deaktiviert. Diese 128 Kerne sind in 16 Abschnitte (8 pro Socket) unterteilt, wobei jeder Abschnitt 8 Prozessorkerne mit einheitlichem Zugriff auf einen 32 MB-L3-Cache enthält. Außerdem gelten für Azure-Server der HBv3-Serie die folgenden AMD-BIOS-Einstellungen:

```bash
Nodes per Socket (NPS) = 2
L3 as NUMA = Disabled
NUMA domains within VM OS = 4
C-states = Enabled
```

Folglich startet der Server mit vier NUMA-Domänen (2 pro Socket) mit einer Größe von je 32 Kernen. Jede NUMA-Domäne verfügt über direkten Zugriff auf vier Kanäle mit physischem DRAM und einer Geschwindigkeit von 3200 MT/s.

Damit der Azure-Hypervisor über genügend Platz verfügt, um ohne Beeinträchtigung des virtuellen Computers agieren zu können, werden 8 physische Kerne pro Server reserviert.

## <a name="vm-topology"></a>Topologie des virtuellen Computers

In der folgenden Abbildung wird die Topologie für diesen Server veranschaulicht: Wir halten diese 8 Hypervisor-Host Kerne (gelb) symmetrisch über beide CPU-Sockets hinweg frei, wobei die ersten 2 Kerne aus bestimmten Core Complex-Matrizen (CCDs) für jede NUMA-Domäne mit den verbleibenden Kernen für den virtuellen Computer der HBv3-Serie (grün) genommen werden.

![Topologie des Servers der HBv3-Serie](./media/architecture/hbv3/hbv3-topology-server.png)

Beachten Sie, dass die CCD-Begrenzung nicht mit einer NUMA-Grenze übereinstimmt. Auf HBv3 wird eine Gruppe von vier aufeinander folgenden (4) CCDs als NUMA-Domäne konfiguriert, sowohl auf der Host-Serverebene als auch innerhalb eines virtuellen Gast-Computers. Folglich machen alle HBv3-Größen des virtuellen Computers vier NUMA-Domänen verfügbar, die für ein Betriebssystem und eine Anwendung angezeigt werden, wie unten dargestellt, 4 einheitliche NUMA-Domänen mit jeweils einer unterschiedlichen Anzahl von Kernen, je nach der spezifischen [HBv3-Größe des virtuellen Computers](../../hbv3-series.md).

![Topologie der HBv3-Serie der virtuellen Computer](./media/architecture/hbv3/hbv3-topology-vm.png)

Jede HBv3-Größe der virtuellen Computer ähnelt dem physischen Layout, den Features und der Leistung einer anderen CPU von der AMD EPYC 7003-Serie wie folgt:

| HBv3-Serie der virtuellen Computer             | NUMA-Domänen | Kerne pro NUMA-Domäne  | Ähnlichkeit mit AMD EPYC         |
|---------------------------------|--------------|------------------------|----------------------------------|
Standard_HB120rs_v3               | 4            | 30                     | Dual-Socket EPYC 7713            |
Standard_HB120r-96s_v3            | 4            | 24                     | Dual-Socket EPYC 7643            |
Standard_HB120r-64s_v3            | 4            | 16                     | Dual-Socket EPYC 7543            |
Standard_HB120r-32s_v3            | 4            | 8                      | Dual-Socket EPYC 7313            |
Standard_HB120r-16s_v3            | 4            | 4                      | Dual-Socket EPYC 72F3            |

> [!NOTE]
> Bei VM-Größen mit eingeschränkten Kernen wird nur die Anzahl der physischen Kerne verringert, die für den virtuellen Computer verfügbar sind. Alle global freigegebenen Ressourcen (RAM, Arbeitsspeicher-Bandbreite, L3-Cache, GMI- und xGMI-Konnektivität, InfiniBand, Azure Ethernet-Netzwerk, lokales SSD) bleiben konstant. Dadurch können Kunden die am besten auf eine bestimmte Gruppe von Workloads oder bestimmte Softwarelizenzierungsanforderungen zugeschnittene VM-Größe auswählen.

Die virtuelle NUMA-Zuordnung der einzelnen HBv3-VM-Größen wird der zugrunde liegenden physischen NUMA-Topologie zugeordnet. Es gibt keine möglicherweise irreführende Abstraktion der Hardware-Topologie. 

Die genaue Topologie für die verschiedenen [HBv3-VM-Größen](../../hbv3-series.md) gemäß der Verwendung der Ausgabe von Istopo sieht wie folgt [aus:](https://linux.die.net/man/1/lstopo)
```bash
lstopo-no-graphics --no-io --no-legend --of txt
```
<br>
<details>
<summary>Klicken Sie, um die lstopo-Ausgabe für Standard_HB120rs_v3 anzuzeigen</summary>

![lstopo-Ausgabe für virtuellen Computer HBv3-120](./media/architecture/hbv3/hbv3-120-lstopo.png)
</details>

<details>
<summary>Klicken Sie, um die lstopo-Ausgabe für Standard_HB120rs-96_v3 anzuzeigen.</summary>

![lstopo-Ausgabe für virtuellen Computer HBv3-96](./media/architecture/hbv3/hbv3-96-lstopo.png)
</details>

<details>
<summary>Klicken Sie, um die lstopo-Ausgabe für Standard_HB120rs-64_v3 anzuzeigen.</summary>

![lstopo-Ausgabe für virtuellen Computer HBv3-64](./media/architecture/hbv3/hbv3-64-lstopo.png)
</details>

<details>
<summary>Klicken Sie, um die lstopo-Ausgabe für Standard_HB120rs-32_v3 anzuzeigen.</summary>

![lstopo-Ausgabe für virtuellen Computer HBv3-32](./media/architecture/hbv3/hbv3-32-lstopo.png)
</details>

<details>
<summary>Klicken Sie, um die lstopo-Ausgabe für Standard_HB120rs-16_v3 anzuzeigen.</summary>

![lstopo-Ausgabe für virtuellen Computer HBv3-16](./media/architecture/hbv3/hbv3-16-lstopo.png)
</details>

## <a name="infiniband-networking"></a>InfiniBand-Netzwerk
HBv3-VMS verfügen auch über Nvidia Mellanox-Netzwerkadapter (ConnectX-6) mit InfiniBand (HDR) mit bis zu 200 Gigabits/Sek. Die NIC wird über SR-IOV an die VM übermittelt und ermöglicht dem Netzwerkdatenverkehr so, den Hypervisor zu umgehen. Demzufolge laden Kunden Mellanox-OFED-Standardtreiber wie eine Bare-Metal-Umgebung auf HBv3-VMs.

HBv3-VMs unterstützen adaptives Routing, DCT (Dynamic Connected Transport, zusätzlich zu RC- und UD-Standardtransporten) und die hardwarebasierte Auslagerung von MPI Collectives (Sammelvorgängen) an den Onboarding-Prozessor des ConnectX-6-Adapters. Diese Features verbessern die Anwendungsleistung, Skalierbarkeit und Konsistenz, und ihre Verwendung wird dringend empfohlen.

## <a name="temporary-storage"></a>Temporärer Speicher
HBv3-VMs verfügen über 3 physisch lokale SSD-Geräte. Ein Gerät wird vorformatiert, um als Auslagerungsdatei zu dienen, und in Ihrer VM als generisches SSD-Gerät angezeigt.

Zwei weitere größere SSDs werden als unformatierte NVMe-Blockgeräte über NVMeDirect bereitgestellt. Da das NVMe-Blockgerät den Hypervisor umgeht, weist es eine höhere Bandbreite, höheren IOPS und eine geringere Latenz pro IOP auf.

Bei einer Kombination in einem verteilten Array bietet das NVMe-SSD-Gerät bis zu 7 GB/s für Lesevorgänge und 3 GB/s für Schreibvorgänge sowie 186.000 IOPS (Lesevorgänge) und 201.000 IOPS (Schreibvorgänge) für große Warteschlangentiefen.

## <a name="hardware-specifications"></a>Hardwarespezifikationen 

| Hardwarespezifikationen          | VMs der HBv3-Serie              |
|----------------------------------|----------------------------------|
| Kerne                            | 120, 96, 64, 32 oder 16 (SMT deaktiviert)               | 
| CPU                              | AMD EPYC 7V13                   | 
| CPU-Frequenz (ohne AVX)          | 3,1 GHz (alle Kerne), 3,675 GHz (bis zu 10 Kerne)    | 
| Arbeitsspeicher                           | 448 GB (RAM pro Kern je nach VM-Größe)         | 
| Lokaler Datenträger                       | 2 × 960 GB NVMe (Block), 480 GB SSD (Auslagerungsdatei) | 
| InfiniBand                       | 200 GB/s Mellanox ConnectX-6 HDR InfiniBand | 
| Netzwerk                          | 50 GBit/s Ethernet (davon 40 GBit/s nutzbar); Azure-SmartNIC der zweiten Generation | 

## <a name="software-specifications"></a>Softwarespezifikationen 

| Softwarespezifikationen        | VMs der HBv3-Serie                                            | 
|--------------------------------|-----------------------------------------------------------|
| Maximale MPI-Auftragsgröße               | 36.000 Kerne (300 VMs in einer einzelnen VM-Skalierungsgruppe mit singlePlacementGroup=true) |
| MPI-Unterstützung                    | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH  |
| Zusätzliche Frameworks          | UCX, libfabric und PGAS                  |
| Azure Storage-Unterstützung          | Standard- und Premium-Datenträger (maximal 32 Datenträger)              |
| Betriebssystemunterstützung für SR-IOV/RDMA      | CentOS/RHEL 7.6+, Ubuntu 18.04+, SLES 12 SP4+, WinServer 2016+           |
| Empfohlenes Betriebssystem für die Leistung | CentOS 8.1, Windows Server 2019 und höher
| Orchestratorunterstützung           | Azure CycleCloud, Azure Batch, AKS; [Cluster-Konfigurationsoptionen](../../sizes-hpc.md#cluster-configuration-options)                      | 

> [!NOTE] 
> Windows Server 2012 R2 wird auf HBv3 und anderen VMs mit mehr als 64 (virtuellen oder physischen) Kernen nicht unterstützt. Weitere Informationen finden Sie unter [Unterstützte Windows-Gastbetriebssysteme für Hyper-V in Windows Server](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die neuesten Ankündigungen, HPC-Workloadbeispiele und Leistungsergebnisse in den [Tech Community-Blogs zu Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Eine allgemeinere Übersicht über die Architektur für die Ausführung von HPC-Workloads finden Sie unter [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
