---
title: Message Passing Interface (MPI) für HPC einrichten - Azure Virtual Machines | Microsoft Docs
description: Hier erfahren Sie, wie Sie MPI für HPC in Azure einrichten.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 7e468b246534fef1fab4e710d0449e785280e16a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460519"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Einrichten von Message Passing Interface für HPC

[Message Passing Interface (MPI)](https://en.wikipedia.org/wiki/Message_Passing_Interface) ist eine offene Bibliothek und der De-facto-Standard für die Parallelisierung von verteiltem Arbeitsspeicher. Sie wird für eine Vielzahl von HPC-Workloads verwendet. HPC-Workloads auf den [RDMA-fähigen](../../sizes-hpc.md#rdma-capable-instances) VMs der [H-Serie](../../sizes-hpc.md) und der [N-Serie](../../sizes-gpu.md) können MPI verwenden, um über das InfiniBand-Netzwerk zu kommunizieren, das sich durch niedrige Latenz und hohe Bandbreite auszeichnet.
- Die SR-IOV-fähigen VM-Größen in Azure gestatten die Verwendung nahezu jeder Variante von MPI mit Mellanox OFED.
- Auf nicht SR-IOV-fähigen virtuellen Computern verwenden unterstützte MPI-Implementierungen die Microsoft Network Direct-Schnittstelle (ND) für die Kommunikation zwischen virtuellen Computern. Daher werden nur die Versionen Microsoft MPI (MS-MPI) 2012 R2 oder höher und Intel MPI 5.x unterstützt. Höhere Versionen (2017, 2018) der Intel MPI-Laufzeitbibliothek sind möglicherweise mit den Azure-RDMA-Treibern kompatibel.

Für SR-IOV- und [RDMA-fähige VMs](../../sizes-hpc.md#rdma-capable-instances) sind [VM-Images von CentOS-HPC](configure.md#centos-hpc-vm-images) 7.6 und höher geeignet. Diese VM-Images sind optimiert und vorab mit den OFED-Treibern für RDMA sowie verschiedenen gängigen MPI-Bibliotheken und Paketen für wissenschaftliches Computing geladen. Damit bieten diese VMs einen sehr einfachen Einstieg.

Obwohl die hier genannten Beispiele für RHEL/CentOS gelten, sind die Schritte allgemein und können für jedes kompatible Linux-Betriebssystem wie Ubuntu (16.04, 18.04, 19.04, 20.04) und SLES (12 SP4 und 15) verwendet werden. Weitere Beispiele für das Einrichten weiterer MPI-Implementierungen auf anderen Distributionen finden Sie im [azhpc-images-Repository](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

> [!NOTE]
> Für das Ausführen von MPI-Aufträgen auf SR-IOV-fähigen VMs mit bestimmten MPI-Bibliotheken (z. B. Platform MPI) kann das Einrichten von Partitionsschlüsseln für einen Mandanten für Isolation und Sicherheit erforderlich sein. Eine ausführliche Anleitung zum Ermitteln der Partitionsschlüsselwerte und zum korrekten Festlegen dieser Werte für einen MPI-Auftrag mit dieser MPI-Bibliothek finden Sie unter [Ermitteln von Partitionsschlüsseln](#discover-partition-keys).

> [!NOTE]
> Die folgenden Codeausschnitte sind Beispiele. Es wird empfohlen, die neuesten stabilen Versionen der Pakete zu verwenden oder auf das [azhpc-images-Repository](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh) zu verweisen.

## <a name="choosing-mpi-library"></a>Auswählen der MPI-Bibliothek
Wenn eine HPC-Anwendung eine bestimmte MPI-Bibliothek empfiehlt, probieren Sie diese Version zuerst aus. Wenn Sie die MPI-Bibliothek frei wählen können und die beste Leistung erzielen möchten, probieren Sie es mit HPC-X. Insgesamt weist die MPI-Bibliothek HPC-X die beste Leistung auf, da sie das UCX-Framework für die InfiniBand-Schnittstelle verwendet und alle InfiniBand-Hardware- und Softwarefunktionen von Mellanox nutzt. Zusätzlich sind HPC-X und OpenMPI mit ABI kompatibel, sodass Sie eine HPC-Anwendung, die mit OpenMPI erstellt wurde, dynamisch mit HPC-X ausführen können. Intel MPI, MVAPICH und MPICH sind ebenfalls ABI-kompatibel.

Die folgende Abbildung veranschaulicht die Architektur für die beliebten MPI-Bibliotheken.

![Architektur für beliebte MPI-Bibliotheken](./media/mpi-architecture.png)

## <a name="ucx"></a>UCX

[Unified Communication X (UCX)](https://github.com/openucx/ucx) ist ein Framework aus Kommunikations-APIs für HPC. Es ist für die MPI-Kommunikation über InfiniBand optimiert und funktioniert mit einer Vielzahl von MPI-Implementierungen wie OpenMPI und MPICH.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

> [!NOTE]
> In aktuellen UCX-Builds wurde ein [Problem](https://github.com/openucx/ucx/pull/5965) behoben, wodurch bei mehreren vorhandenen NIC-Schnittstellen nun die richtige InfiniBand-Schnittstelle ausgewählt wird. Weitere Informationen zum Ausführen von MPI über InfiniBand, wenn der beschleunigte Netzwerkbetrieb auf der VM aktiviert ist, finden Sie unter [Beheben von bekannten Problemen bei HPC- und GPU-VMs](hb-hc-known-issues.md#accelerated-networking-on-hb-hc-hbv2-and-ndv2).

## <a name="hpc-x"></a>HPC-X

Das [HPC-X-Softwaretoolkit](https://www.mellanox.com/products/hpc-x-toolkit) enthält UCX und HCOLL und kann für UCX erstellt werden.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

Der folgende Befehl enthält einige empfohlene mpirun-Argumente für HPC-X und OpenMPI.
```bash
mpirun -n $NPROCS --hostfile $HOSTFILE --map-by ppr:$NUMBER_PROCESSES_PER_NUMA:numa:pe=$NUMBER_THREADS_PER_PROCESS -report-bindings $MPI_EXECUTABLE
```
Dabei gilt Folgendes:

|Parameter|BESCHREIBUNG                                        |
|---------|---------------------------------------------------|
|`NPROCS`   |Hier wird die Anzahl der MPI-Prozesse angegeben. Beispiel: `-n 16`.|
|`$HOSTFILE`|Hier wird eine Datei angegeben, die den Hostnamen oder die IP-Adresse enthält, um anzugeben, wo die MPI-Prozesse ausgeführt werden. Beispiel: `--hostfile hosts`.|
|`$NUMBER_PROCESSES_PER_NUMA`   |Hier wird die Anzahl der MPI-Prozesse angegeben, die in den einzelnen NUMA-Domänen ausgeführt werden. Für vier MPI-Prozesse pro NUMA-Domäne verwenden Sie z. B. `--map-by ppr:4:numa:pe=1`.|
|`$NUMBER_THREADS_PER_PROCESS`  |Hier wird die Anzahl der Threads pro MPI-Prozess angegeben. Für einen MPI-Prozess und vier Threads pro NUMA-Domäne verwenden Sie z. B. `--map-by ppr:1:numa:pe=4`.|
|`-report-bindings` |Hiermit wird die Zuordnung der MPI-Prozesse zu den Kernen ausgegeben. Dies ist nützlich, um zu überprüfen, ob Sie die MPI-Prozesse richtig angeheftet haben.|
|`$MPI_EXECUTABLE`  |Hier wird die erstellte ausführbare MPI-Datei angegeben, die die MPI-Bibliotheken einbindet. Bei MPI-Compilerwrappern erfolgt dies automatisch. Zum Beispiel: `mpicc` oder `mpif90`.|

Ein Beispiel für die Ausführung der OSU-Latenz-Microbenchmarks ist:
```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

### <a name="optimizing-mpi-collectives"></a>Optimieren von MPI-Kollektiven

Die MPI-Primitive für kollektive Kommunikation bieten eine flexible, portable Möglichkeit zur Implementierung von Gruppenkommunikationsvorgängen. Deren Verwendung ist in verschiedenen wissenschaftlichen parallelen Anwendungen weit verbreitet und hat einen erheblichen Einfluss auf die Gesamtleistung der Anwendung. Einzelheiten zu den Konfigurationsparametern zur Optimierung der Leistung der kollektiven Kommunikation mit HPC-X und der HCOLL-Bibliothek für kollektive Kommunikation finden Sie im [TechCommunity-Artikel](https://techcommunity.microsoft.com/t5/azure-compute/optimizing-mpi-collective-communication-using-hpc-x-on-azurehpc/ba-p/1356740).

Wenn Sie beispielsweise vermuten, dass übermäßig viel gemeinsame Kommunikation über Ihre eng gekoppelte MPI-Anwendung erfolgt, können Sie versuchen, Hierarchical Collectives (HCOLL) zu aktivieren. Verwenden Sie die folgenden Parameter, um diese Features zu aktivieren.
```bash
-mca coll_hcoll_enable 1 -x HCOLL_MAIN_IB=<MLX device>:<Port>
```

> [!NOTE] 
> Bei HPC-X 2.7.4 und höher kann es erforderlich sein, „LD_LIBRARY_PATH“ explizit zu übergeben, wenn sich die UCX-Version unter MOFED von der in HPC-X unterscheidet.

## <a name="openmpi"></a>OpenMPI

Installieren Sie UCX, wie oben beschrieben. HCOLL gehört zum [HPC-X-Softwaretoolkit](https://www.mellanox.com/products/hpc-x-toolkit) und erfordert keine gesonderte Installation.

OpenMPI kann aus den im Repository verfügbaren Paketen installiert werden.

```bash
sudo yum install –y openmpi
```

Es wird empfohlen, ein aktuelles stabiles Release von OpenMPI mit UCX zu erstellen.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

Führen Sie OpenMPI für eine optimale Leistung mit `ucx` und `hcoll` aus. Siehe auch das Beispiel mit [HPC-X](#hpc-x).

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Überprüfen Sie Ihren Partitionsschlüssel, wie weiter oben erwähnt.

## <a name="intel-mpi"></a>Intel MPI

Laden Sie die gewünschte Version von [Intel MPI](https://software.intel.com/mpi-library/choose-download) herunter. Mit dem Intel MPI 2019-Release wurde vom OFA-Framework (Open Fabrics Alliance) auf das OFI-Framework (Open Fabrics Interfaces) umgestellt, und libfabric wird derzeit unterstützt. Es gibt zwei Anbieter für InfiniBand-Unterstützung: mlx und verbs.
Ändern Sie die Umgebungsvariable „I_MPI_FABRICS“ abhängig von der Version.
- Intel MPI 2019 und 2021: Verwenden Sie `I_MPI_FABRICS=shm:ofi`, `I_MPI_OFI_PROVIDER=mlx`. Der `mlx`-Anbieter verwendet UCX. Die Verwendung von Verben wurde als instabil und weniger leistungsfähig eingestuft. Weitere Informationen finden Sie im [TechCommunity-Artikel](https://techcommunity.microsoft.com/t5/azure-compute/intelmpi-2019-on-azure-hpc-clusters/ba-p/1403149).
- Intel MPI 2018: Verwenden Sie `I_MPI_FABRICS=shm:ofa`.
- Intel MPI 2016: Verwenden Sie `I_MPI_DAPL_PROVIDER=ofa-v2-ib0`.

Im Folgenden sind einige Vorschläge für mpirun-Argumente für Intel MPI 2019, Update 5 und höher, aufgeführt.
```bash
export FI_PROVIDER=mlx
export I_MPI_DEBUG=5
export I_MPI_PIN_DOMAIN=numa

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
Dabei gilt Folgendes:

|Parameter|BESCHREIBUNG                                        |
|---------|---------------------------------------------------|
|`FI_PROVIDER`  |Hier wird angegeben, welcher libfabric-Anbieter verwendet werden soll. Dies wirkt sich auf die API, das Protokoll und das Netzwerk aus, die verwendet werden. verbs ist eine weitere Option, doch bietet mlx im Allgemeinen eine bessere Leistung.|
|`I_MPI_DEBUG`|Hier wird der Umfang der zusätzlichen Debugausgabe angegeben, die Details dazu bereitstellen kann, wo Prozesse angeheftet sind und welches Protokoll und Netzwerk verwendet werden.|
|`I_MPI_PIN_DOMAIN` |Hier geben Sie an, wie Sie die Prozesse anheften möchten. Sie können sie beispielsweise an Kerne, Sockets oder NUMA-Domänen anheften. In diesem Beispiel wurde diese Umgebungsvariable auf numa festgelegt. Dies bedeutet, dass Prozesse an NUMA-Knotendomänen angeheftet werden.|

### <a name="optimizing-mpi-collectives"></a>Optimieren von MPI-Kollektiven

Es gibt noch einige weitere Optionen, die Sie ausprobieren können, insbesondere dann, wenn gemeinsame Vorgänge viel Zeit beanspruchen. Intel MPI 2019, Update 5 und höher, unterstützt den Anbieter mlx und verwendet das UCX-Framework für die Kommunikation mit InfiniBand. Darüber hinaus wird HCOLL unterstützt.
```bash
export FI_PROVIDER=mlx
export I_MPI_COLL_EXTERNAL=1
```

### <a name="non-sr-iov-vms"></a>Nicht-SR-IOV-VMS

Für Nicht-SR-IOV-VMS finden Sie im Folgenden ein Beispiel für das Herunterladen der [kostenlosen Evaluierungsversion](https://registrationcenter.intel.com/en/forms/?productid=1740) der 5.x-Runtime:
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
Die Installationsschritte finden Sie im [Installationshandbuch für die Intel MPI-Bibliothek](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).
Optional können Sie ptrace für Nicht-Debugger-Prozesse ohne Root-Zugriff aktivieren (erforderlich für die neuesten Versionen von Intel MPI).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
Bei SUSE Linux Enterprise Server-VM-Imageversionen – SLES 12 SP3 für HPC, SLES 12 SP3 für HPC (Premium), SLES 12 SP1 für HPC, SLES 12 SP1 für HPC (Premium), SLES 12 SP4 und SLES 15 – werden die RDMA-Treiber auf der VM installiert, und die Intel MPI-Pakete werden bereitgestellt. Installieren Sie Intel MPI mit dem folgenden Befehl:
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mvapich"></a>MVAPICH

Das folgende Beispiel veranschaulicht das Building von MVAPICH2. Beachten Sie, dass neuere Versionen verfügbar sein können, als die unten verwendeten.
```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

Ein Beispiel für die Ausführung der OSU-Latenz-Microbenchmarks ist:
```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

In der folgenden Liste sind einige empfohlene `mpirun`-Argumente aufgeführt.
```bash
export MV2_CPU_BINDING_POLICY=scatter
export MV2_CPU_BINDING_LEVEL=numanode
export MV2_SHOW_CPU_BINDING=1
export MV2_SHOW_HCA_BINDING=1

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
Dabei gilt Folgendes:

|Parameter|BESCHREIBUNG                                        |
|---------|---------------------------------------------------|
|`MV2_CPU_BINDING_POLICY`   |Hier wird angegeben, welche Bindungsrichtlinie verwendet werden soll. Dies wirkt sich darauf aus, wie Prozesse an Kern-IDs angeheftet werden. In diesem Fall wurde Scatter angegeben, sodass die Prozesse gleichmäßig auf die NUMA-Domänen verteilt werden.|
|`MV2_CPU_BINDING_LEVEL`|Hier wird angegeben, wo Prozesse angeheftet werden sollen. In diesem Fall wurde numanode festgelegt. Dies bedeutet, dass Prozesse an NUMA-Domäneneinheiten angeheftet werden.|
|`MV2_SHOW_CPU_BINDING` |Hier wird angegeben, ob Debuginformationen dazu abgerufen werden sollen, wo die Prozesse angeheftet sind.|
|`MV2_SHOW_HCA_BINDING` |Hier wird angegeben, ob Debuginformationen dazu abgerufen werden sollen, welchen Hostkanaladapter die einzelnen Prozesse verwenden.|

## <a name="platform-mpi"></a>Platform MPI

Installieren Sie die erforderlichen Pakete für die Community-Edition von Platform MPI.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Führen Sie die Schritte des Installationsprozesses aus.

Die folgenden Befehle sind Beispiele für die Ausführung von MPI Pingpong und Allreduce mit Platform MPI auf HBv3-VMs mithilfe von VM-Images von CentOS-HPC 7.6, 7.8 und 8.1.

```bash
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:1,10.0.0.9:1 -np 2 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 pingpong
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:120,10.0.0.9:120 -np 240 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 allreduce -npmin 240
```


## <a name="mpich"></a>MPICH

Installieren Sie UCX, wie oben beschrieben. Erstellen Sie MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

Führen Sie MPICH aus.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Überprüfen Sie Ihren Partitionsschlüssel, wie weiter oben erwähnt.

## <a name="osu-mpi-benchmarks"></a>OSU-MPI-Benchmarks

Laden Sie die [OSU-MPI-Benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) herunter, und entpacken Sie sie.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Erstellen Sie Benchmarks unter Verwendung einer bestimmten MPI-Bibliothek:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI-Benchmarks befinden sich im Ordner `mpi/`.


## <a name="discover-partition-keys"></a>Ermitteln von Partitionsschlüsseln

Ermitteln Sie Partitionsschlüssel für die Kommunikation mit anderen VMs im selben Mandanten (Verfügbarkeitsgruppe oder VM-Skalierungsgruppe).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Der größere der beiden ist der Mandantenschlüssel, der mit MPI verwendet werden muss. Beispiel: Von den folgenden Partitionsschlüsseln muss „0x800b“ mit MPI verwendet werden:

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Verwenden Sie die Partition, bei der es sich nicht um den Standardpartitionsschlüssel (0x7fff) handelt. Für UCX muss das MSB des Partitionsschlüssels gelöscht werden. Legen Sie also beispielsweise „UCX_IB_PKEY“ für „0x800b“ auf „0x000b“ fest.

Beachten Sie außerdem, dass die Partitionsschlüssel unverändert bleiben, solange der Mandant (Verfügbarkeitsgruppe oder VM-Skalierungsgruppe) vorhanden ist. Dies gilt auch, wenn Knoten hinzugefügt oder gelöscht werden. Neue Mandanten erhalten unterschiedliche Partitionsschlüssel.


## <a name="set-up-user-limits-for-mpi"></a>Einrichten von Benutzergrenzwerten für MPI

Richten Sie Benutzergrenzwerte für MPI ein.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>Einrichten von SSH-Schlüsseln für MPI

Richten Sie SSH-Schlüssel für MPI-Typen ein, bei denen dies erforderlich ist.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

In der obigen Syntax wird von einem gemeinsam genutzten Basisverzeichnis ausgegangen. Andernfalls muss das SSH-Verzeichnis an jeden Knoten kopiert werden.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [InfiniBand-fähigen](../../sizes-hpc.md#rdma-capable-instances) VMs der [H-Serie](../../sizes-hpc.md) und der [N-Serie](../../sizes-gpu.md).
- Weitere Informationen finden Sie in der [Übersicht über virtuelle Computer der HBv3-Serie](hbv3-series-overview.md) und in der [Übersicht über virtuelle Computer der HC-Serie](hc-series-overview.md).
- Weitere Informationen finden Sie unter [Optimale MPI-Prozessplatzierung für VMs der HB-Serie](https://techcommunity.microsoft.com/t5/azure-global/optimal-mpi-process-placement-for-azure-hb-series-vms/ba-p/2450663).
- Informieren Sie sich über die neuesten Ankündigungen, HPC-Workloadbeispiele und Leistungsergebnisse in den [Tech Community-Blogs zu Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Eine allgemeinere Übersicht über die Architektur für die Ausführung von HPC-Workloads finden Sie unter [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
