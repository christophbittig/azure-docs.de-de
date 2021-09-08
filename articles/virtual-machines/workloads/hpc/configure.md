---
title: Konfiguration und Optimierung von InfiniBand-fähigen Azure-VMs der H- und N-Serie
description: Informieren Sie sich über das Konfigurieren und Optimieren der InfiniBand-fähigen virtuellen Computer der H-Serie und der N-Serie für HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 06/02/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 0ef3b00fe3ae68ddecc7ab93ee612e0698329c43
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460490"
---
# <a name="configure-and-optimize-vms"></a>Konfigurieren und Optimieren von virtuellen Computern

In diesem Artikel finden Sie einen Leitfaden zum Konfigurieren und Optimieren der InfiniBand-fähigen VMs der [H-Serie](../../sizes-hpc.md) und der [N-Serie](../../sizes-gpu.md) für HPC.

## <a name="vm-images"></a>VM-Images
Auf InfiniBand-fähigen (IB) VMs sind die entsprechenden Treiber erforderlich, um RDMA zu aktivieren.
- Die [CentOS-HPC-VM-Images](#centos-hpc-vm-images) im Marketplace werden mit den entsprechenden IB-Treibern vorkonfiguriert bereitgestellt.
  - Das VM-Image CentOS-HPC Version 7.9 ist zusätzlich mit den Nvidia-GPU-Treibern vorkonfiguriert. 
- Die [Ubuntu-HPC-VM-Images](#ubuntu-hpc-vm-images) im Marketplace werden mit den entsprechenden IB- und GPU-Treibern vorkonfiguriert bereitgestellt.

Diese VM-Images (VMI) basieren auf den Marketplace-VM-Images mit CentOS bzw. Ubuntu. Skripts, die bei der Erstellung dieser VM-Images aus ihrem CentOS-Basisimage im Marketplace verwendet werden, finden Sie im Repository [azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos).

Bei GPU-fähigen VMs der [N-Serie](../../sizes-gpu.md) sind zusätzlich die entsprechenden GPU-Treiber erforderlich. Diese können mit den folgenden Methoden verfügbar gemacht werden:
- Verwenden Sie die [Ubuntu-HPC-VM-Images](#ubuntu-hpc-vm-images) und das [CentOS-HPC VM-Image](#centos-hpc-vm-images), Version 7.9, auf denen die Nvidia-GPU-Treiber und der GPU-Computesoftwarestapel (CUDA, NCCL) bereits vorkonfiguriert sind.
- Fügen Sie die GPU-Treiber über [VM-Erweiterungen](../../extensions/hpccompute-gpu-linux.md) hinzu.
- Installieren Sie die GPU-Treiber [manuell](../../linux/n-series-driver-setup.md).
- Bei einigen VM-Images im Marketplace (einschließlich einiger VM-Images von Nvidia) sind die Nvidia-GPU-Treiber bereits vorinstalliert.

Abhängig von den Anforderungen an die Linux-Distribution und -Version der Workloads stellen die [CentOS-HPC-VM-Images](#centos-hpc-vm-images) oder die [Ubuntu-HPC-VM-Images](#ubuntu-hpc-vm-images) im Marketplace die einfachsten Möglichkeiten für den Einstieg in HPC- und KI-Workloads in Azure dar.
Außerdem sollten [benutzerdefinierte VM-Images](../../linux/tutorial-custom-images.md) mit entsprechenden Anpassungen und Konfigurationen für die jeweilige Workload erstellt und mehrfach wiederverwendet werden.

### <a name="vm-sizes-supported-by-the-hpc-vm-images"></a>Von HPC-VM-Images unterstützte VM-Größen

#### <a name="infiniband-ofed-support"></a>InfiniBand OFED-Unterstützung
Die neuesten HPC-Marketplace-Images von Azure bieten Mellanox OFED 5.1 und höher. Diese Images unterstützen keine ConnectX-3 Pro-InfiniBand-Karten. Sie unterstützen nur InfiniBand-Karten mit ConnextX-5 und höher. Daraus ergibt sich die folgende Unterstützungsmatrix für VM-Größen bei InfiniBand OFED in diesen HPC-VM-Images:
- [H-Serie:](../../sizes-hpc.md) HB, HC, HBv2, HBv3
- [N-Serie:](../../sizes-gpu.md) NDv2, NDv4

#### <a name="gpu-driver-support"></a>GPU-Treiberunterstützung
Derzeit sind nur die [Ubuntu-HPC-VM-Images](#ubuntu-hpc-vm-images) und das [CentOS-HPC VM-Image](#centos-hpc-vm-images), Version 7.9, mit den Nvidia-GPU-Treibern und dem GPU-Computesoftwarestapel (CUDA, NCCL) vorkonfiguriert.

Die Unterstützungsmatrix für die VM-Größe für die GPU-Treiber in unterstützten HPC-VM-Images lautet wie folgt:
- [N-Serie](../../sizes-gpu.md): Die VM-Größen NDv2 und NDv4 werden mit den Nvidia-GPU-Treibern und dem GPU-Computesoftwarestapel (CUDA, NCCL) unterstützt.
- Die anderen VM-Größen „NC“ und „ND“ der [N-Serie](../../sizes-gpu.md) werden mit den Nvidia-GPU-Treibern unterstützt.

Beachten Sie außerdem, dass alle oben genannten VM-Größen Gen2-VMs unterstützen, obwohl einige ältere auch Gen1-VMs unterstützen. Die Unterstützung für „Gen 2“ wird auch mit „01“ am Ende des VMI-URN oder der Version angegeben.

### <a name="centos-hpc-vm-images"></a>CentOS-HPC-VM-Images

#### <a name="sr-iov-enabled-vms"></a>SR-IOV unterstützende VMs
Für SR-IOV- und [RDMA-fähige VMs](../../sizes-hpc.md#rdma-capable-instances) sind VM-Images von CentOS-HPC 7.6 und höher geeignet. Diese VM-Images sind optimiert und vorab mit den Mellanox-OFED-Treibern für RDMA sowie verschiedenen gängigen MPI-Bibliotheken und Paketen für wissenschaftliches Computing geladen. Weitere Informationen finden Sie in der obigen [Unterstützungsmatrix für die VM-Größe](#vm-sizes-supported-by-the-hpc-vm-images).
- Sie können die verfügbaren oder neuesten Versionen der VM-Images über die [Befehlszeilenschnittstelle](/cli/azure/vm/image#az_vm_image_list) oder im [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) mit den folgenden Informationen auflisten.
   ```bash
   "publisher": "OpenLogic",
   "offer": "CentOS-HPC",
   ```
- Skripts, die bei der Erstellung der VM-Images der CentOS-HPC-Version ab 7.6 aus einem CentOS-Basis-Image im Marketplace verwendet werden, finden Sie im [Repository „azhpc-images“](https://github.com/Azure/azhpc-images/tree/master/centos).
- Weitere Einzelheiten zum Funktionsumfang und zur Bereitstellung der CentOS-HPC-VM-Images ab Version 7.6 finden Sie in einem [TechCommunity-Artikel](https://techcommunity.microsoft.com/t5/azure-compute/azure-hpc-vm-images/ba-p/977094).

> [!NOTE] 
> Von den CentOS-HPC-VM-Images ist derzeit nur das VM-Image der Version 7.9 zusätzlich mit den Nvidia-GPU-Treibern und dem GPU-Computesoftwarestapel (CUDA, NCCL) vorkonfiguriert.

> [!NOTE] 
> Für VM-Größen mit Aktivierung von SR-IOV der N-Serie mit FDR InfiniBand (z. B. bis NCv3) können die folgenden CentOS-HPC-VM-Imageversionen oder niedrigere Versionen aus dem Marketplace verwendet werden:
>- OpenLogic:CentOS-HPC:7.6:7.6.2020062900
>- OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
>- OpenLogic:CentOS-HPC:7.7:7.7.2020062600
>- OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
>- OpenLogic:CentOS-HPC:8_1:8.1.2020062400
>- OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401

#### <a name="non-sr-iov-enabled-vms"></a>SR-IOV nicht unterstützende VMs
Für [RDMA-fähige VMs](../../sizes-hpc.md#rdma-capable-instances) ohne Aktivierung von SR-IOV ist eine CentOS-HPC-Version von 6.5 bis 7.4 im Marketplace geeignet. Für [VMs der H16-Serie](../../h-series.md) werden z. B. die Versionen 7.1 bis 7.4 empfohlen. Bei diesen VM-Images sind die Network Direct-Treiber für RDMA und Intel MPI Version 5.1 vorab geladen.

> [!NOTE]
> Bei den CentOS-basierten HPC-Images für SR-IOV nicht unterstützende VMs sind Kernel-Updates in der **yum**-Konfigurationsdatei deaktiviert. Der Grund: Die Network Direct-Linux RDMA-Treiber werden als RPM-Paket verteilt, und Treiberupdates funktionieren möglicherweise nicht, wenn der Kernel aktualisiert wird.

### <a name="ubuntu-hpc-vm-images"></a>Ubuntu-HPC-VM-Images
Für SR-IOV- und [RDMA-fähige VMs](../../sizes-hpc.md#rdma-capable-instances) sind Ubuntu-HPC-VM-Images mit den Versionen 18.04 und 20.04 geeignet. Diese VM-Images sind optimiert und vorab mit den Mellanox-OFED-Treibern für RDMA, mit Nvidia-GPU-Treibern, dem GPU-Computesoftwarestapel (CUDA, NCCL) sowie verschiedenen gängigen MPI-Bibliotheken und Paketen für wissenschaftliches Computing geladen. Weitere Informationen finden Sie in der obigen [Unterstützungsmatrix für die VM-Größe](#vm-sizes-supported-by-the-hpc-vm-images).
- Sie können die verfügbaren oder neuesten Versionen der VM-Images über die [Befehlszeilenschnittstelle](/cli/azure/vm/image#az_vm_image_list) oder im [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-hpc?tab=overview) mit den folgenden Informationen auflisten.
   ```bash
   "publisher": "Microsoft-DSVM",
   "offer": "Ubuntu-HPC",
   ```
- Skripts, die bei der Erstellung der Ubuntu-HPC-VM-Images aus einem Ubuntu-Basisimage aus dem Marketplace verwendet werden, befinden sich im Repository [azhpc-images](https://github.com/Azure/azhpc-images/tree/master/ubuntu).
- Weitere Einzelheiten zum Funktionsumfang und zur Bereitstellung der Ubuntu-HPC-VM-Images finden Sie in einem [TechCommunity-Artikel](https://techcommunity.microsoft.com/t5/azure-compute/azure-hpc-vm-images/ba-p/977094).

### <a name="rhelcentos-vm-images"></a>RHEL/CentOS-VM-Images
Die RHEL- oder CentOS-basierten Nicht-HPC-VM-Basisimages im Marketplace können für die Verwendung auf SR-IOV-aktivierten, [RDMA-fähigen VMs](../../sizes-hpc.md#rdma-capable-instances) konfiguriert werden. Informieren Sie sich ausführlicher über die [Aktivierung von InfiniBand](enable-infiniband.md) und [MPI-Einrichtung](setup-mpi.md) auf den VMs.
- Skripts, die bei der Erstellung der VM-Images der CentOS-HPC-Version ab 7.6 aus einem CentOS-Basis-Image im Marketplace verwendet werden, finden Sie im [Repository „azhpc-images“](https://github.com/Azure/azhpc-images/tree/master/centos).
 
### <a name="ubuntu-vm-images"></a>Ubuntu-VM-Images
Die VM-Basisimages mit Ubuntu Server 16.04 LTS, 18.04 LTS und 20.04 LTS im Marketplace werden für [RDMA-fähige VMs](../../sizes-hpc.md#rdma-capable-instances) mit und ohne SR-IOV unterstützt. Informieren Sie sich ausführlicher über die [Aktivierung von InfiniBand](enable-infiniband.md) und [MPI-Einrichtung](setup-mpi.md) auf den VMs.
- Anweisungen zur Aktivierung von InfiniBand in den Ubuntu-VM-Images finden Sie in einem [TechCommunity-Artikel](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351).
- Skripts, die bei der Erstellung der auf Ubuntu 18.04 und 20.04 LTS basierenden HPC VM-Images aus einem Ubuntu-Basis-Image aus dem Marketplace verwendet werden, befinden sich im [ Repository „azhpc-images“](https://github.com/Azure/azhpc-images/tree/master/ubuntu).

> [!NOTE]
> Ab Mellanox OFED 5.1 werden keine ConnectX3-Pro-InfiniBand-Karten auf für SR-IOV aktivierten VM-Größen der N-Serie mit FDR-InfiniBand (z. B. NCv3) unterstützt. Verwenden Sie LTS Mellanox OFED ab Version 4.9-0.1.7.0 auf den VMs der N-Serie mit ConnectX3-Pro-Karten. Weitere Informationen finden Sie unter [Linux InfiniBand-Treiber](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server-VM-Images
SLES 12 SP3 for HPC-, SLES 12 SP3 for HPC (Premium)-, SLES 12 SP1 for HPC-, SLES 12 SP1 for HPC (Premium)-, SLES 12 SP4- und SLES 15-VM-Images im Marketplace werden unterstützt. Bei diesen VM-Images sind die Network Direct-Treiber für RDMA (bei VM-Größen ohne SR-IOV) und Intel MPI Version 5.1 vorab geladen. Informieren Sie sich über das [Einrichten von MPI](setup-mpi.md) auf den VMs.

## <a name="optimize-vms"></a>Optimieren von VMs

Im folgenden finden Sie einige optionale Optimierungseinstellungen für eine verbesserte Leistung des virtuellen Computers.

### <a name="update-lis"></a>Aktualisieren von LIS

Sofern für Funktionalität oder Leistung erforderlich, können [LIS-Treiber (Linux Integration Services)](../../linux/endorsed-distros.md) auf unterstützten Betriebssystemdistributionen installiert oder aktualisiert werden. Dies gilt insbesondere für die Bereitstellung mit einem benutzerdefinierten Image oder einer älteren Betriebssystemversion wie CentOS/RHEL 6.x oder einer früheren Version von 7.x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Freigeben von Arbeitsspeicher

Steigern Sie die Leistung durch die automatische Freigabe von Arbeitsspeicher, um den Remotezugriff auf den Speicher zu vermeiden.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

So legen Sie fest, dass diese Einstellung auch nach VM-Neustarts gilt:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Deaktivieren von Firewall und SELinux

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>Deaktivieren von cpupower

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>Konfigurieren von WALinuxAgent

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
Optional kann der WALinuxAgent zur maximalen Verfügbarkeit der VM-Ressource für die HPC-Workload als Vor-Auftrags-Schritt deaktiviert und nach dem Auftrag wieder aktiviert werden.


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [InfiniBand-Aktivierung](enable-infiniband.md) auf der InfiniBand-fähigen [H-Serie](../../sizes-hpc.md) und [N-Serie](../../sizes-gpu.md) virtueller Computer.
- Erfahren Sie mehr über die Installation und Ausführung verschiedener [unterstützter MPI-Bibliotheken](setup-mpi.md) auf den VMs.
- Weitere Informationen finden Sie in der [Übersicht über virtuelle Computer der HBv3-Serie](hbv3-series-overview.md) und in der [Übersicht über virtuelle Computer der HC-Serie](hc-series-overview.md).
- Informieren Sie sich über die neuesten Ankündigungen, HPC-Workloadbeispiele und Leistungsergebnisse in den [Tech Community-Blogs zu Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Eine allgemeinere Übersicht über die Architektur für die Ausführung von HPC-Workloads finden Sie unter [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
