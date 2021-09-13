---
title: DPDK auf einer Linux-VM in Azure | Microsoft-Dokumentation
description: Lernen Sie die Vorteile des Data Plane Development Kit (DPDK) kennen und erfahren Sie, wie das DPDK auf einem virtuellen Computer unter Linux eingerichtet wird.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2020
ms.author: labattul
ms.openlocfilehash: 10639653c00fc5e781a9edd2b49c60f659d00966
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355759"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>Einrichten des DPDK auf einem virtuellen Linux-Computer

Data Plane Development Kit (DPDK) in Azure bietet ein schnelles Paketverarbeitungsframework auf Benutzerseite für leistungsintensive Anwendungen. Dieses Framework umgeht den Kernelnetzwerkstapel des virtuellen Computers.

Der Vorgang bei der herkömmlichen Paketverarbeitung mit Kernelnetzwerkstapel wird durch Interrupts gesteuert. Wenn die Netzwerkschnittstelle eingehende Pakete empfängt, wird ein Kernelinterrupt zum Verarbeiten des Pakets erzeugt, und es findet ein Kontextwechsel vom Kernel- zum Benutzerbereich statt. Mit DPDK entfallen der Kontextwechsel und die interruptgesteuerte Methode zugunsten einer Implementierung im Benutzerbereich mit Treibern im Abrufmodus, damit Pakete schnell verarbeitet werden können.

DPDK umfasst Gruppen von Benutzerbereichsbibliotheken, die Zugriff auf Ressourcen niedrigerer Ebene bereitstellen. Diese Ressourcen schließen Hardware, logische Kerne, Speicherverwaltung und Treiber im Abrufmodus für Netzwerk-Schnittstellenkarten ein.

DPDK kann auf Azure-VMs ausgeführt werden, die mehrere Betriebssystemverteilungen unterstützen. DPDK bietet eine zentrale Leistungsdifferenzierung bei der Steuerung von Implementierungen der Netzwerksfunktionsvirtualisierung. Diese Implementierungen können die Form virtueller Netzwerkgeräte (NVAs) annehmen, z.B. virtuelle Router, Firewalls, VPNs, Lastenausgleichsmodule, hoch entwickelte Paketkerne und DDoS-Anwendungen (Denial-of-Service).

## <a name="benefit"></a>Vorteil

**Mehr Pakete pro Sekunde (pps)** : Das Umgehen des Kernels und das Steuern der Pakete auf Benutzerseite verringern die Zyklen, da keine Kontextwechsel notwendig sind. Zudem wird die Rate der pro Sekunde verarbeiteten Pakete auf Linux-VMs in Azure verbessert.


## <a name="supported-operating-systems-minimum-versions"></a>Unterstützte Mindestversionen von Betriebssystemen

Die folgenden Distributionen aus dem Azure Marketplace werden unterstützt:

| Linux-Betriebssystem     | Kernelversion               | 
|--------------|---------------------------   |
| Ubuntu 18.04 | 4.15.0-1014-azure+           |
| SLES 15 SP1  | 4.12.14-8.19-azure+          | 
| RHEL 7.5     | 3.10.0-862.11.6.el7.x86_64+  | 
| CentOS 7.5   | 3.10.0-862.11.6.el7.x86_64+  | 
| Debian 10    | 4.19.0-1-cloud und höher              |

Die aufgeführten Versionen sind die Mindestanforderungen. Neuere Versionen werden ebenfalls unterstützt.

**Benutzerdefinierte Kernelunterstützung**

Nicht aufgeführte Linux-Kernelversionen finden Sie unter [Patches zum Erstellen eines Linux-Kernels für Azure](https://github.com/microsoft/azure-linux-kernel). Weitere Informationen erhalten Sie auch bei [aznetdpdk@microsoft.com](mailto:aznetdpdk@microsoft.com). 

## <a name="region-support"></a>Unterstützung für Regionen

Alle Azure-Regionen unterstützen DPDK.

## <a name="prerequisites"></a>Voraussetzungen

Auf der Linux-VM muss der beschleunigte Netzwerkbetrieb aktiviert sein. Die VM sollte mindestens zwei Netzwerkschnittstellen haben, davon eine für die Verwaltung. Die Aktivierung des beschleunigten Netzwerks auf der Verwaltungsschnittstelle wird nicht empfohlen. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Linux-Computers mit beschleunigtem Netzwerkbetrieb](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-via-system-package-recommended"></a>Installieren von DPDK über das Systempaket (empfohlen)

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo add-apt-repository ppa:canonical-server/server-backports -y
sudo apt-get update
sudo apt-get install -y dpdk
```

### <a name="ubuntu-2004-and-newer"></a>Ubuntu 20.04 und höher

```bash
sudo apt-get install -y dpdk
```

### <a name="debian-10-and-newer"></a>Debian 10 und höher

```bash
sudo apt-get install -y dpdk
```

## <a name="install-dpdk-manually-not-recommended"></a>Manuelles Installieren von DPDK (nicht empfohlen)

### <a name="install-build-dependencies"></a>Installieren von Buildabhängigkeiten

#### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo add-apt-repository ppa:canonical-server/server-backports -y
sudo apt-get update
sudo apt-get install -y build-essential librdmacm-dev libnuma-dev libmnl-dev meson
```

#### <a name="ubuntu-2004-and-newer"></a>Ubuntu 20.04 und höher

```bash
sudo apt-get install -y build-essential librdmacm-dev libnuma-dev libmnl-dev meson
```

#### <a name="debian-10-and-newer"></a>Debian 10 und höher

```bash
sudo apt-get install -y build-essential librdmacm-dev libnuma-dev libmnl-dev meson
```

#### <a name="rhel75centos-75"></a>RHEL7.5/CentOS 7.5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel meson
```

#### <a name="sles-15-sp1"></a>SLES 15 SP1

**Azure-Kernel**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel meson
```

**Standardkernel**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel meson
```

### <a name="compile-and-install-dpdk-manually"></a>Manuelles Kompilieren und Installieren von DPDK

1. [Laden Sie das aktuellste DPDK herunter.](https://core.dpdk.org/download) Für Azure ist die Version 19.11 LTS oder höher erforderlich.
2. Erstellen Sie die Standardkonfiguration mit `meson builddir`.
3. Kompilieren Sie mit `ninja -C builddir`.
4. Installieren Sie mit `DESTDIR=<output folder> ninja -C builddir install`.

## <a name="configure-the-runtime-environment"></a>Konfigurieren der Laufzeitumgebung

Führen Sie nach einem Neustart die folgenden Befehle einmalig aus:

1. Umfangreiche Seiten

   * Konfigurieren Sie umfangreiche Seiten, indem Sie den folgenden Befehl einmal für jeden NUMA-Knoten ausführen:

     ```bash
     echo 1024 | sudo tee /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Erstellen Sie ein Verzeichnis für die Bereitstellung mit `mkdir /mnt/huge`.
   * Stellen Sie umfangreiche Seiten mit `mount -t hugetlbfs nodev /mnt/huge` bereit.
   * Vergewissern Sie sich mit `grep Huge /proc/meminfo`, dass umfangreiche Seiten reserviert sind.

     > [HINWEIS] Sie können die GRUB-Datei so ändern, dass umfangreiche Seiten beim Starten reserviert werden. Befolgen Sie dazu die [Anweisungen](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) für DPDK. Die Anweisungen befinden sich unten auf der Seite. Wenn Sie eine Linux-VM in Azure verwenden, ändern Sie Dateien stattdessen unter **/etc/config/grub.d**, um umfangreiche Seiten über Neustarts hinweg zu reservieren.

2. MAC- und IP-Adressen: Verwenden Sie `ifconfig –a`, um die MAC- und IP-Adresse der Netzwerkschnittstellen anzuzeigen. Die *VF*-Netzwerkschnittstelle und die *NETVSC*-Netzwerkschnittstelle haben dieselbe MAC-Adresse, allerdings hat nur die *NETVSC*-Netzwerkschnittstelle eine IP-Adresse. *VF*-Schnittstellen werden als untergeordnete Schnittstellen von *NETVSC*-Schnittstellen ausgeführt.

3. PCI-Adressen

   * Finden Sie mit `ethtool -i <vf interface name>` heraus, welche PCI-Adresse Sie für *VF* verwenden können.
   * Wenn für *eth0* der beschleunigte Netzwerkbetrieb aktiviert ist, vergewissern Sie sich, dass testpmd nicht versehentlich das *VF*-PCI-Gerät für *eth0* übernimmt. Wenn die DPDK-Anwendung versehentlich die Verwaltungsnetzwerkschnittstelle übernimmt und dadurch die SSH-Verbindung unterbrochen wird, beenden Sie die DPDK-Anwendung über die serielle Konsole. Sie können die serielle Konsole auch zum Beenden oder Starten des virtuellen Computers verwenden.

4. Laden Sie bei jedem Neustart *ibuverbs* mit `modprobe -a ib_uverbs`. Nur für SLES 15: Laden Sie auch *mlx4_ib* mit `modprobe -a mlx4_ib`.

## <a name="failsafe-pmd"></a>Ausfallsicherer PMD

DPDK-Anwendungen müssen über den ausfallsicheren PMD ausgeführt werden, der in Azure verfügbar gemacht wird. Wenn die Anwendung direkt über den *VF*-PMD ausgeführt wird, empfängt sie nicht **alle** Pakete, die an die VM gerichtet sind, da einige Pakete über die synthetische Schnittstelle angezeigt werden. 

Wenn Sie eine DPDK-Anwendung über den Failsafe-PMD ausführen, ist garantiert, dass die Anwendung alle Pakete empfängt, die für sie bestimmt sind. Es wird außerdem sichergestellt, dass die Anwendung weiterhin im DPDK-Modus ausgeführt wird, auch wenn VF während der Wartung des Hosts aufgehoben wird. Weitere Informationen zu ausfallsicherem PMD finden Sie unter [Fail-safe poll mode driver library](https://doc.dpdk.org/guides/nics/fail_safe.html) (Ausfallsicherheits-PMD-Bibliothek).

## <a name="run-testpmd"></a>Ausführen von testpmd

Verwenden Sie `sudo` vor dem Befehl *testpmd*, um testpmd im root-Modus auszuführen.

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Basic: Integritätsprüfung, Initialisierung des Ausfallsicherheitsadapters

1. Führen Sie die folgenden Befehle aus, um eine testpmd-Anwendung mit einem einzigen Port zu starten:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Führen Sie die folgenden Befehle aus, um eine testpmd-Anwendung mit zwei Ports zu starten:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Bei der Ausführung von testpmd mit mehr als zwei Netzwerkadaptern hat das `--vdev`-Argument dieses Muster: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`.

3.  Führen Sie nach dem Start `show port info all` aus, um die Portinformationen zu überprüfen. Es sollten ein oder zwei DPDK-Ports vom Typ „net_failsafe“ angezeigt werden (nicht *net_mlx4*).
4.  Starten Sie mit `start <port> /stop <port>` den Datenverkehr.

Die vorherigen Befehle starten *testpmd* im interaktiven Modus. Dies wird empfohlen, um einige testpmd-Befehle zu testen.

### <a name="basic-single-sendersingle-receiver"></a>Basic: einzelner Absender/einzelner Empfänger

Mit den folgenden Befehlen werden Pakete in einem regelmäßigen Sekundentakt gedruckt:

1. Führen Sie auf Absenderseite den folgenden Befehl aus:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. Führen Sie auf Empfängerseite den folgenden Befehl aus:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

Wenn Sie die vorherigen Befehle auf einer VM ausführen, ändern Sie *IP_SRC_ADDR* und *IP_DST_ADDR* in `app/test-pmd/txonly.c` vor dem Kompilieren entsprechend der tatsächlichen IP-Adresse der VM. Andernfalls werden die Pakete gelöscht, bevor sie den Empfänger erreichen.

### <a name="advanced-single-sendersingle-forwarder"></a>Erweitert: einzelner Absender/einzelne Weiterleitung
Mit den folgenden Befehlen werden Pakete in einem regelmäßigen Sekundentakt gedruckt:

1. Führen Sie auf Absenderseite den folgenden Befehl aus:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. Führen Sie auf Weiterleitungsseite den folgenden Befehl aus:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

Wenn Sie die vorherigen Befehle auf einer VM ausführen, ändern Sie *IP_SRC_ADDR* und *IP_DST_ADDR* in `app/test-pmd/txonly.c` vor dem Kompilieren entsprechend der tatsächlichen IP-Adresse der VM. Andernfalls werden die Pakete gelöscht, bevor sie die Weiterleitung erreichen. Es ist nicht möglich, dass ein dritter Computer den weitergeleiteten Datenverkehr empfängt, da die *testpmd*-Weiterleitung die Adressen der Schicht 3 nicht ändert, es sei denn, Sie nehmen einige Codeänderungen vor.

## <a name="references"></a>References

* [EAL-Optionen](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options) (in englischer Sprache)
* [Testpmd-Befehle](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options) (in englischer Sprache)
* [Befehle für Paketsicherung](https://doc.dpdk.org/guides/tools/pdump.html#pdump-tool)
