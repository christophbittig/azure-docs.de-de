---
title: Beheben von bekannten Problemen bei HPC- und GPU-VMs – Azure Virtual Machines | Microsoft-Dokumentation
description: Erfahren Sie mehr über das Beheben bekannter Probleme bei HPC- und GPU-VM-Größen in Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/28/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: b9277e8ce7c9c3fff748efff42517c6c0baed57a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691474"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Bekannte Probleme bei virtuellen Computern der H-Serie und der N-Serie

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Dieser Artikel versucht, die jüngsten häufigen Probleme und ihre Lösungen bei der Verwendung der HPC- und GPU-VMs der [H-Serie](../../sizes-hpc.md) und [N-Serie](../../sizes-gpu.md) aufzulisten.

## <a name="cache-topology-on-standard_hb120rs_v3"></a>Cachetopologie auf Standard_HB120rs_v3
`lstopo` zeigt eine falsche Cachetopologie auf der VM-Größe „Standard_HB120rs_v3 VM“ an. Möglicherweise wird angezeigt, dass pro NUMA nur 32 MB L3 verfügbar sind. In der Praxis gibt es jedoch tatsächlich wie erwartet 120 MB L3 pro NUMA, da die gleichen 480 MB L3 für den gesamten virtuellen Computer verfügbar sind, wie bei den anderen HBv3-VM-Größen mit eingeschränkten Kernen. Dies ist ein kosmetischer Fehler beim Anzeigen des richtigen Werts, der sich nicht auf Workloads auswirken sollte.

## <a name="qp0-access-restriction"></a>qp0-Zugriffseinschränkung
Um Zugriffe auf Low-Level-Hardware zu verhindern, die zu Sicherheitsrisiken führen können, haben virtuelle Gastcomputer keinen Zugriff auf das Warteschlangenpaar 0. Dies ist in der Regel nur für Aktionen im Zusammenhang mit der Verwaltung der ConnectX InfiniBand NIC oder mit der Ausführung einiger InfiniBand-Diagnosen (beispielsweise „ibdiagnet“) relevant, nicht aber für Endbenutzeranwendungen.

## <a name="mofed-installation-on-ubuntu"></a>MOFED-Installation auf Ubuntu
Auf Ubuntu-18.04-basierten Marketplace-VM-Images mit Kernelversion `5.4.0-1039-azure #42` und neuer sind einige ältere Mellanox OFED-Versionen inkompatibel, was in einigen Fällen zu einer Erhöhung der VM-Startzeit von bis zu 30 Minuten führt. Dies wurde für die beiden Mellanox-OFED-Versionen 5.2-1.0.4.0 und 5.2-2.2.0.0 gemeldet. Das Problem wurde mit Mellanox OFED 5.3-1.0.0.1 behoben.
Wenn es erforderlich ist, den inkompatiblen OFED zu verwenden, besteht eine Lösung in der Verwendung des Marketplace-VM-Images **Canonical:UbuntuServer:18_04-lts-gen2:18.04.202101290** oder älter, damit der Kernel nicht aktualisiert werden muss.

## <a name="mpi-qp-creation-errors"></a>MPI-QP-Erstellungsfehler
Wenn bei der Ausführung von MPI-Workloads InfiniBand QP-Erstellungsfehler wie unten gezeigt auftreten, empfehlen wir, die VM neu zu starten und den Workload erneut zu versuchen. Dieses Problem wird zukünftig behoben.

```bash
ib_mlx5_dv.c:150  UCX  ERROR mlx5dv_devx_obj_create(QP) failed, syndrome 0: Invalid argument
```

Sie können die Werte der maximalen Anzahl von Warteschlangenpaaren überprüfen, wenn das Problem wie folgt beobachtet wird.
```bash
[user@azurehpc-vm ~]$ ibv_devinfo -vv | grep qp
max_qp: 4096
```

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>Beschleunigter Netzwerkbetrieb auf HB, HC, HBv2 und NDv2

[Beschleunigter Netzwerkbetrieb in Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) ist jetzt für die RDMA-, InfiniBand- und SR-IOV-fähigen VM-Größen [HB](../../hb-series.md), [HC](../../hc-series.md), [HBv2](../../hbv2-series.md) und [NDv2](../../ndv2-series.md) verfügbar. Diese Funktion ermöglicht jetzt einen höheren Durchsatz (bis zu 30 GBits/s) und bessere Wartezeiten über das Azure Ethernet-Netzwerk. Obwohl dies von den RDMA-Funktionen über das InfiniBand-Netzwerk getrennt ist, können einige Plattformänderungen für diese Funktionalität das Verhalten bestimmter MPI-Implementierungen beeinflussen, wenn Aufträge über InfiniBand ausgeführt werden. Insbesondere kann die InfiniBand-Schnittstelle auf einigen VMs einen etwas anderen Namen aufweisen (mlx5_1 im Gegensatz zum vorherigen mlx5_0) und dies kann eine Anpassung der MPI-Befehlszeilen erfordern, insbesondere mithilfe der UCX-Schnittstelle (üblicherweise mit OpenMPI und HPC-X). Die einfachste Lösung ist möglicherweise derzeit die Verwendung des neuesten HPC-X auf den CentOS-HPC-VM-Images oder die Deaktivierung von Accelerated Networking, falls nicht benötigt.
Weitere diesbezügliche Details finden Sie in diesem [TechCommunity-Artikel](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965) mit Anweisungen zur Behebung jeglicher beobachteter Probleme.

## <a name="infiniband-driver-installation-on-non-sr-iov-vms"></a>InfiniBand-Treiberinstallation auf nicht-SR-IOV-VMs

Derzeit sind H16r, H16mr und NC24r nicht SR-IOV-fähig. Weitere Informationen über die InfiniBand-Stapelverzweigung finden Sie unter [Azure-VM-Größen – HPC](../../sizes-hpc.md#rdma-capable-instances).
InfiniBand kann für die SR-IOV-fähigen VM-Größen mit den OFED-Treibern konfiguriert werden, während die nicht SR-IOV-fähigen VM-Größen ND-Treiber erfordern. Diese Unterstützung von IB ist für [CentOS, RHEL und Ubuntu](configure.md)angemessen verfügbar.

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>Doppelte MAC-Adresse mit cloud-init bei Ubuntu auf virtuellen Computern der H-Serie und der N-Serie

Es gibt ein bekanntes Problem mit cloud-init für Ubuntu-VM-Images, wenn versucht wird, die IB-Schnittstelle zu aktivieren. Dieses Problem kann entweder beim Neustart eines virtuellen Computers oder beim Erstellen eines VM-Images nach der Generalisierung auftreten. In den VM-Startprotokollen wird möglicherweise ein Fehler wie der folgende angezeigt:
```console
“Starting Network Service...RuntimeError: duplicate mac found! both 'eth1' and 'ib0' have mac”.
```

Diese „doppelte MAC-Adresse mit cloud-init bei Ubuntu“ ist ein bekanntes Problem. Dieses Problem wird in neueren Kernels gelöst. Wenn das Problem auftritt, umgehen Sie das Problem wie folgt:
1) Stellen Sie das Marketplace-VM-Image (Ubuntu 18.04) bereit.
2) Installieren Sie die erforderlichen Softwarepakete, um IB zu aktivieren ([Anleitung hier](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)).
3) Bearbeiten Sie „waagent.conf“, um Folgendes zu ändern: EnableRDMA=y.
4) Deaktivieren Sie die Netzwerkkonfiguration in cloud-init.
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) Bearbeiten Sie die von cloud-init generierte Netzwerkkonfigurationsdatei für netplan, um die MAC-Adresse zu entfernen.
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="dram-on-hb-series-vms"></a>DRAM in den VMs der HB-Serie

Virtuelle Computer der HB-Serie können momentan nur 228 GB RAM für virtuelle Gastcomputer verfügbar machen. Entsprechend können auf HBv2-VMs 458 GB und auf HBv3-VMs 448 GB verfügbar gemacht werden. Der Grund ist eine bekannte Einschränkung des Azure-Hypervisors, die verhindert, dass Seiten dem lokalen DRAM der AMD CCXs (NUMA-Domänen) zugewiesen werden, die für den virtuellen Gastcomputer reserviert sind.

## <a name="gss-proxy"></a>GSS Proxy

Für GSS Proxy liegt in CentOS/RHEL 7.5 ein bekannter Fehler vor, der bei Verwendung mit NFS zu erheblichen Leistungs- und Reaktionseinbußen führen kann. Die Auswirkungen können auf folgende Weise gemindert werden:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Cachebereinigung

Auf HPC-Systemen empfiehlt es sich häufig, nach Abschluss eines Auftrags den Arbeitsspeicher zu bereinigen, bevor dem nächsten Benutzer der gleiche Knoten zugewiesen wird. Nach der Ausführung von Anwendungen unter Linux ist unter Umständen ein Rückgang des verfügbaren Arbeitsspeichers sowie eine Zunahme des Pufferspeichers zu beobachten, obwohl gar keine Anwendungen ausgeführt werden.

![Screenshot der Eingabeaufforderung vor Bereinigung](./media/known-issues/cache-cleaning-1.png)

`numactl -H` zeigt, mit welchen NUMA-Knoten der Arbeitsspeicher gepuffert wird. (Das können unter Umständen alle sein.) Unter Linux können Benutzer die Caches auf drei Arten bereinigen, um gepufferten Arbeitsspeicher oder CPU-Cache freizugeben. Dazu sind root- oder sudo-Berechtigungen erforderlich.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Screenshot der Eingabeaufforderung nach Bereinigung](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Kernelwarnungen

Wenn Sie einen virtuellen Computer der HB-Serie unter Linux starten, können Sie die folgenden Kernelwarnmeldungen ignorieren. Sie ist auf eine bekannte Einschränkung des Azure-Hypervisors zurückzuführen, die im Laufe der Zeit behandelt wird.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```


## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Übersicht über virtuelle Computer der HB-Serie](hb-series-overview.md) und die [Übersicht über virtuelle Computer der HC-Serie](hc-series-overview.md) an, um zu erfahren, wie Sie Workloads optimal konfigurieren, um die bestmögliche Leistung und Skalierbarkeit zu erzielen.
- Informieren Sie sich über die neuesten Ankündigungen, HPC-Workloadbeispiele und Leistungsergebnisse in den [Tech Community-Blogs zu Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Eine allgemeinere Übersicht über die Architektur für die Ausführung von HPC-Workloads finden Sie unter [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
