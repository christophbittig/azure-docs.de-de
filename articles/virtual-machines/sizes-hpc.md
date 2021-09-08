---
title: 'Azure-VM-Größen: HPC | Microsoft-Dokumentation'
description: Liste der verschiedenen verfügbaren Größen für virtuelle HPC-Computer (High Performance Computing) in Azure. Dieser Artikel listet Informationen zur Anzahl von vCPUs, Datenträgern und Netzwerkschnittstellenkarten sowie zum Speicherdurchsatz und zur Netzwerkbandbreite für Größen dieser Serie auf.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/19/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 11c3fadd7f95f07bbcbf095e8d6a126022bbea0b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697921"
---
# <a name="high-performance-computing-vm-sizes"></a>High-Performance Computing (HPC): VM-Größen

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

> [!TIP]
> Probieren Sie das **[Auswahltool für virtuelle Computer](https://aka.ms/vm-selector)** aus, um andere Größen zu ermitteln, die für Ihre Workload optimal sind.

Virtuelle Azure-Computer (Azure-VMs) der H-Serie bieten eine herausragende Leistung, Skalierbarkeit und Kosteneffizienz für verschiedene HPC-Workloads in der Praxis.

VMs der [HBv3-Serie](hbv3-series.md) sind für HPC-Anwendungen optimiert, wie z. B. Strömungssimulationen, explizite und implizite Finite-Elemente-Analysen, Wettermodellierung, seismische Erkundung, Lagerstätten- und RTL-Simulationen. HBv3-VMs verfügen über bis zu 120 CPU-Kerne der AMD EPYC™ 7003-Serie (Milan), 448 GB RAM und kein Hyperthreading. Außerdem bieten VMs der HBv3-Serie eine Arbeitsspeicherbandbreite von 350 GB/s, bis zu 32 MB L3-Cache pro Kern, bis zu 7 GB/s der Blockgeräte-SSD-Leistung und Taktfrequenzen von bis zu 3,675 GHz. 

Alle VMs der HBv3-Serie bieten 200 GBit/s HDR InfiniBand aus dem NVIDIA-Netzwerk für MPI-Workloads wie bei Supercomputern. Diese VMs sind für eine optimierte und konsistente RDMA-Leistung in einer FAT-Struktur ohne Blocks verbunden. Das HDR InfiniBand-Fabric unterstützt auch adaptives Routing und DCT (Dynamic Connected Transport, zusätzlich zum standardmäßigen RC- und UD-Transport). Diese Features verbessern die Anwendungsleistung, Skalierbarkeit und Konsistenz, und ihre Verwendung wird dringend empfohlen.

VMs der [HBv2-Serie](hbv2-series.md) sind für Anwendungen mit hohen Anforderungen an die Speicherbandbreite optimiert, z. B. Strömungssimulationen, Finite-Element-Analysen oder Lagerstättensimulationen. HBv2-VMs bieten 120 AMD-Prozessorkerne des Modells EPYC 7742, 4 GB RAM pro CPU-Kern und kein gleichzeitiges Multithreading. Jede HBv2-VM bietet eine Speicherbandbreite von bis zu 340 Gbit/s und eine FP64-Computeleistung von bis zu 4 TeraFLOP.

HBv2-VMs unterstützen Mellanox HDR InfiniBand mit 200 GBit/s, und VMs der Serien HB und HC unterstützen Mellanox EDR InfiniBand mit 100 GBit/s. Jeder dieser VM-Typen ist für eine optimierte und konsistente RDMA-Leistung in einer FAT-Struktur ohne Blocks verbunden. HBv2-VMs unterstützen adaptives Routing und DCT (Dynamic Connected Transport, zusätzlich zum standardmäßigen RC- und UD-Transport). Diese Features verbessern die Anwendungsleistung, Skalierbarkeit und Konsistenz, und ihre Verwendung wird dringend empfohlen.

VMs der [HB-Serie](hb-series.md) sind für Anwendungen mit hohen Anforderungen an die Speicherbandbreite optimiert, z. B. Strömungssimulationen, explizite Finite-Elemente-Analysen oder Wettermodelle. HB-VMs bieten 60 AMD-Prozessorkerne des Modells EPYC 7551, 4 GB RAM pro CPU-Kern und kein Hyperthreading. Die AMD EPYC-Plattform bietet eine Speicherbandbreite von mehr als 260 GB/Sekunde.

VMs der [HC-Serie](hc-series.md) sind für Anwendungen mit hohen Anforderungen an die Rechenleistung optimiert, z. B. implizite Finite-Elemente-Analysen, Molekulardynamik oder Chemoinformatik. HC-VMs bieten 44 Intel-Prozessorkerne des Modells Xeon Platinum 8168, 8 GB RAM pro CPU-Kern und kein Hyperthreading. Die Intel Xeon Platinum-Plattform unterstützt Intels umfangreiches Ökosystem von Softwaretools, etwa die Intel Math Kernel Library.

VMs der [H-Serie](h-series.md) sind für Anwendungen optimiert, für die Anforderungen hinsichtlich hoher CPU-Frequenzen oder großem Arbeitsspeicher pro Kern gelten. Virtuelle Computer der H-Serie bieten 8 oder 16 Intel-Prozessorkerne des Modells Xeon E5 2667 v3, 7 oder 14 GB RAM pro CPU-Kern und kein Hyperthreading. Die H-Serie unterstützt Mellanox EDR InfiniBand mit 56 GBit/s in einer FAT-Strukturkonfiguration ohne Blocks für einheitliche RDMA-Leistung. Virtuelle Computer der H-Serie unterstützen Intel MPI 5.x und MS-MPI.

> [!NOTE]
> Alle VMs der HBv3-, HBv2-, HB- und HC-Serie haben exklusiven Zugriff auf die physischen Server. Es ist nur ein virtueller Computer pro physischem Server vorhanden, und für diese VM-Größen gibt es keine freigegebene Mehrinstanzenfähigkeit mit anderen VMs.

> [!NOTE]
> Die [A8- bis A11-VMs](./sizes-previous-gen.md#a-series---compute-intensive-instances) werden ab März 2021 außer Betrieb genommen. Es sind keine neuen VM-Bereitstellungen dieser Größen mehr möglich. Wenn Sie über vorhandene VMs verfügen, finden Sie in den E-Mail-Benachrichtigungen Informationen zu den nächsten Schritten und im [HPC-Migrationsleitfaden](https://azure.microsoft.com/resources/hpc-migration-guide/) Informationen zur Migration zu anderen VM-Größen.

## <a name="rdma-capable-instances"></a>RDMA-fähige Instanzen

Die meisten HPC-VM-Größen verfügen über eine Netzwerkschnittstelle für RDMA-Verbindungen (Remote Direct Memory Access). Ausgewählte Größen der [N-Serie](./nc-series.md) mit dem Zusatz „r“ sind ebenfalls RDMA-fähig. Diese Schnittstelle steht zusätzlich zur standardmäßigen Azure-Ethernet-Netzwerkschnittstelle in anderen VM-Größen zur Verfügung.

Mithilfe dieser zweiten Schnittstelle können die RDMA-fähigen Instanzen über ein InfiniBand-Netzwerk (IB) kommunizieren, das mit HDR-Raten für virtuelle Computer der Größen HBv3, HBv2, EDR-Raten für virtuelle Computer der Größen HB, HC, NDv2 sowie mit FDR-Raten für virtuelle Computer der Größen H16r und H16mr sowie andere für RDMA-fähige virtuelle Computer der N-Serie betrieben wird. Durch diese RDMA-Funktionen können Skalierbarkeit und Leistung von MPI-basierten Anwendungen (Message Passing Interface) gesteigert werden.

> [!NOTE]
> **SR-IOV-Unterstützung**: In Azure HPC gibt es derzeit zwei Klassen von virtuellen Computern, je nachdem, ob sie SR-IOV-fähig für InfiniBand sind. Derzeit sind fast alle für RDMA oder InfiniBand geeigneten VMs der neueren Generation in Azure SR-IOV-fähig – mit Ausnahme von H16r, H16mr und NC24r.
> RDMA kann nur über das IB-Netzwerk (InfiniBand) verwendet werden und wird für alle RDMA-fähigen virtuellen Computer unterstützt.
> „IP over IB“ wird nur auf den SR-IOV-fähigen virtuellen Computern unterstützt.
> RDMA kann nicht über das Ethernet-Netzwerk verwendet werden.

- **Betriebssystem**: Häufig werden Linux-Distributionen wie CentOS, RHEL, Ubuntu und SUSE verwendet. Windows Server 2016 oder höher wird auf allen virtuellen Computern der HPC-Serie unterstützt. Windows Server 2012 R2 und Windows Server 2012 werden auch auf virtuellen Computern unterstützt, die nicht SR-IOV-fähig sind. Beachten Sie, dass [Windows Server 2012 R2 ab HBv2 als VM-Größe mit mehr als 64 (virtuellen oder physischen) Kernen nicht unterstützt wird](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows). Unter [Optimierung für Linux](./workloads/hpc/configure.md) finden Sie eine Liste der unterstützten VM-Images im Marketplace und Informationen dazu, wie diese entsprechend konfiguriert werden. Auf den jeweiligen Seiten zur VM-Größe ist auch die Unterstützung des Softwarestapels aufgeführt.

- **InfiniBand und Treiber**: Auf InfiniBand-fähigen virtuellen Computern sind die entsprechenden Treiber erforderlich, um RDMA zu aktivieren. Unter [Optimierung für Linux](./workloads/hpc/configure.md) finden Sie eine Liste der unterstützten VM-Images im Marketplace und Informationen dazu, wie diese entsprechend konfiguriert werden. Weitere Informationen zu VM-Erweiterungen oder zur manuellen Installation von InfiniBand-Treibern finden Sie auch unter [Aktivieren von InfiniBand](./workloads/hpc/enable-infiniband.md).

- **MPI**: Die SR-IOV-fähigen VM-Größen in Azure gestatten die Verwendung nahezu jeder Variante von MPI mit Mellanox OFED. Auf nicht SR-IOV-fähigen virtuellen Computern verwenden unterstützte MPI-Implementierungen die Microsoft Network Direct-Schnittstelle (ND) für die Kommunikation zwischen virtuellen Computern. Daher werden nur die Versionen ab Intel MPI 5.x und Microsoft MPI (MS-MPI) 2012 R2 unterstützt. Höhere Versionen der Intel MPI-Laufzeitbibliothek sind möglicherweise nicht mit den Azure-RDMA-Treibern kompatibel. Details zum Einrichten von MPI (Message Passing Interface) auf HPC-VMs in Azure finden Sie unter [Einrichten von Message Passing Interface für HPC](./workloads/hpc/setup-mpi.md).

  > [!NOTE]
  > **RDMA-Netzwerkadressbereich**: Das RDMA-Netzwerk in Azure reserviert sich den Adressbereich 172.16.0.0/16. Wenn Sie MPI-Anwendungen auf Instanzen ausführen möchten, die in einem virtuellen Azure-Netzwerk bereitgestellt wurden, vergewissern Sie sich, dass der Adressraum des virtuellen Netzwerks sich nicht mit dem RDMA-Netzwerk überschneidet.

## <a name="cluster-configuration-options"></a>Konfigurationsoptionen für Cluster

Azure bietet mehrere Optionen zum Erstellen von Clustern von HPC-VMs, die über das RDMA-Netzwerk kommunizieren können, darunter Folgende: 

- **Virtuelle Computer**: Stellen Sie die RDMA-fähigen HPC-VMs in derselben Skalierungsgruppe oder Verfügbarkeitsgruppe bereit (wenn Sie das Azure Resource Manager-Bereitstellungsmodell verwenden). Stellen Sie die VMs bei Verwendung des klassischen Bereitstellungsmodells im gleichen Clouddienst bereit.

- **VM-Skalierungsgruppen**: Stellen Sie bei Verwendung einer VM-Skalierungsgruppe sicher, dass Sie die Bereitstellung auf eine einzelne Platzierungsgruppe für InfiniBand-Kommunikation in der Skalierungsgruppe beschränken. Legen Sie z. B. in einer Resource Manager-Vorlage die Eigenschaft `singlePlacementGroup` auf `true` fest. Beachten Sie, dass die maximale Skalierungsgruppengröße, die mit `singlePlacementGroup=true` hochgefahren werden kann, standardmäßig auf 100 VMs begrenzt ist. Wenn Ihre Anforderung hinsichtlich der HPC-Aufträge mehr als 100 virtuelle Computer in einem einzelnen Mandanten umfasst, können Sie eine Erhöhung anfordern [und eine kostenlose Anfrage für den Onlinekundensupport](../azure-portal/supportability/how-to-create-azure-support-request.md) öffnen. Der Grenzwert für die Anzahl der virtuellen Computer in einer einzelnen Skalierungsgruppe kann auf 300 erhöht werden. Beachten Sie, dass bei der Bereitstellung von VMs mit Verfügbarkeitsgruppen der Höchstwert bei 200 VMs pro Verfügbarkeitsgruppe liegt.

  > [!NOTE]
  > **MPI zwischen virtuellen Computern**: Wenn RDMA (z. B. mithilfe der MPI-Kommunikation) zwischen virtuellen Computern (VMs) erforderlich ist, sollten Sie sicherstellen, dass sich die VMs in derselben VM-Skalierungsgruppe oder Verfügbarkeitsgruppe befinden.

- **Azure CycleCloud**: Erstellen Sie in [Azure CycleCloud](/azure/cyclecloud/) einen HPC-Cluster zum Ausführen von MPI-Aufträgen.

- **Azure Batch**: Erstellen Sie einen [Azure Batch](../batch/index.yml)-Pool, um MPI-Workloads auszuführen. Informationen zur Verwendung rechenintensiver Instanzen zum Ausführen von MPI-Anwendungen mit Azure Batch finden Sie unter [Verwendung von Tasks mit mehreren Instanzen zum Ausführen von MPI-Anwendungen (Message Passing Interface) in Azure Batch](../batch/batch-mpi.md).

- **Microsoft HPC Pack** - [HPC Pack](/powershell/high-performance-computing/overview) enthält eine Laufzeitumgebung für MS-MPI, die das Azure-RDMA-Netzwerk bei der Bereitstellung auf RDMA-fähigen Linux-VMs verwendet. Beispielbereitstellungen finden Sie unter [Einrichten eines Linux-RDMA-Clusters mit HPC Pack zum Ausführen von MPI-Anwendungen](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Überlegungen zur Bereitstellung

- **Azure-Abonnement:** Um eine größere Anzahl von rechenintensiven Instanzen bereitzustellen, sollten Sie ein Abonnement mit nutzungsbasierter Bezahlung oder andere Kaufoptionen in Erwägung ziehen. Bei Verwendung eines [kostenlosen Azure-Kontos](https://azure.microsoft.com/free/)können Sie nur eine begrenzte Anzahl von Azure-Compute-Kernen nutzen.

- **Preise und Verfügbarkeit:** Informationen nach Azure-Regionen finden Sie unter [VM-Preise](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) und [Verfügbarkeit](https://azure.microsoft.com/global-infrastructure/services/).

- **Kontingent Speicherkerne:** Es kann sein, dass Sie das Kontingent für die Speicherkerne in Ihrem Azure-Abonnement anpassen müssen, indem Sie den Standardwert erhöhen. Möglicherweise ist bei Ihrem Abonnement auch die Anzahl von Kernen beschränkt, die in bestimmten VM-Größenkategorien bereitgestellt werden können. In diesem Fall können Sie kostenlos [eine Anfrage an den Onlinekundensupport richten](../azure-portal/supportability/how-to-create-azure-support-request.md) und eine Erhöhung des Kontingents anfordern. (Standardgrenzwerte variieren unter Umständen je nach Abonnementkategorie.)

  > [!NOTE]
  > Wenn Sie einen umfangreichen Kapazitätsbedarf haben, wenden Sie sich an den Azure-Support. Azure-Kontingente sind Angebotsbeschränkungen, keine Kapazitätsgarantien. Unabhängig von Ihrem Kontingent werden nur die tatsächlich verwendeten Kerne in Rechnung gestellt.
  
- **Virtuelles Netzwerk:** Ein [virtuelles Azure-Netzwerk](https://azure.microsoft.com/documentation/services/virtual-network/) ist nicht erforderlich, um die rechenintensiven Instanzen zu verwenden. Für viele Bereitstellungen benötigen Sie jedoch mindestens ein cloudbasiertes virtuelles Azure-Netzwerk oder auch eine Site-to-Site-Verbindung für den Zugriff auf lokale Ressourcen. Erstellen Sie ggf. ein neues virtuelles Netzwerk zum Bereitstellen der Instanzen. Das Hinzufügen rechenintensiver virtueller Computer zu einem virtuellen Netzwerk in einer Affinitätsgruppe wird nicht unterstützt.

- **Größenanpassung:** Aufgrund der speziellen Hardware können Sie die Größe rechenintensiver Instanzen nur innerhalb der gleichen Größenfamilie (H-Serie oder N-Serie) anpassen. So können Sie beispielsweise die Größe eines virtuellen Computers der H-Serie nur auf eine andere Größe der H-Serie festlegen. Für bestimmte virtuelle Computer müssen möglicherweise zusätzliche Überlegungen zur Unterstützung von InfiniBand-Treibern und NVMe-Datenträgern in Betracht gezogen werden.


## <a name="other-sizes"></a>Andere Größen

- [Allgemeiner Zweck](sizes-general.md)
- [Computeoptimiert](sizes-compute.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [GPU-optimiert](sizes-gpu.md)
- [Vorherige Generationen](sizes-previous-gen.md)

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über das [Konfigurieren Ihrer VMs](./workloads/hpc/configure.md), [Aktivieren von InfiniBand](./workloads/hpc/enable-infiniband.md), [Einrichten von MPI](./workloads/hpc/setup-mpi.md) und Optimieren von HPC-Anwendungen für Azure unter [High Performance Computing auf InfiniBand-fähigen virtuellen Computern der H- und N-Serie](./workloads/hpc/overview.md).
- Weitere Informationen finden Sie in der [Übersicht über virtuelle Computer der HBv3-Serie](./workloads/hpc/hbv3-series-overview.md) und in der [Übersicht über virtuelle Computer der HC-Serie](./workloads/hpc/hc-series-overview.md).
- Informieren Sie sich über die neuesten Ankündigungen, HPC-Workloadbeispiele und Leistungsergebnisse in den [Tech Community-Blogs zu Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Eine allgemeinere Übersicht über die Architektur für die Ausführung von HPC-Workloads finden Sie unter [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
