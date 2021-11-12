---
title: NP-Serie – Azure Virtual Machines
description: Spezifikationen für die VMs der NP-Serie.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 4a366d704e942c74bfa32e9a0819cfda2126b2d1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456611"
---
# <a name="np-series"></a>NP-Serie 

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Die virtuellen Computer der NP-Serie basieren auf [Xilinx U250](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html)-FPGAs zum Beschleunigen von Workloads einschließlich Machine Learning-Rückschluss, Videotranscodierung und Datenbanksuche und Analysen. VMs der NP-Serie werden auch mit Intel Xeon 8171m-CPUs (Skylake) mit einem Turbotakt von 3,2 GHz für alle Kerne betrieben.

[Storage Premium](premium-storage-performance.md): Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Livemigration:](maintenance-and-updates.md) Nicht unterstützt<br>
[Updates mit Speicherbeibehaltung:](maintenance-and-updates.md) Nicht unterstützt<br>
Unterstützung von VM-Generationen: Generation 1<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Unterstützt<br>
[Kurzlebige Betriebssystemdatenträger](ephemeral-os-disks.md): Unterstützt<br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | FPGA | FPGA-Speicher: GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBit/s) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1/7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2/15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4/30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**F:** Wie kann ein Kontingent für NP-VMs angefordert werden?

**A:** Befolgen Sie die Informationen auf der Seite [Erhöhen der Grenzwerte nach VM-Serie](../azure-portal/supportability/per-vm-quota-requests.md). NP-VMs sind in „USA, Osten“, „USA, Westen 2“, „Europa, Westen“ und „Asien, Südosten“ verfügbar.

**F:** Welche Version von Vitis sollte ich verwenden? 

**A:** Xilinx empfiehlt [Vitis 2020.2](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html). Sie können auch die Optionen für den Development VM Marketplace verwenden (Vitis 2020.2 Development VM für Ubuntu 18.04 und Centos 7.8)

**F:** Muss ich NP VMs verwenden, um meine Lösung zu entwickeln? 

**A:** Nein, Sie können lokal entwickeln und in der Cloud bereitstellen! Stellen Sie sicher, dass Sie für die Bereitstellung auf NP-VMs die [Nachweisdokumentation](./field-programmable-gate-arrays-attestation.md) befolgen. 

**F:** Welche vom Nachweis zurückgegebene Datei sollte ich beim Programmieren meines FPGA auf einer NP-VM verwenden?

**A:** Der Nachweis gibt zwei Dateien vom Typ „xclbin“ zurück: **design.bit.xclbin** und **design.azure.xclbin**. Verwenden Sie **design.azure.xclbin**.

**F:** Wo sollte ich alle XRT-/Plattformdateien abrufen?

**A:** Sie finden alle Dateien auf der Website [Microsoft-Azure](https://www.xilinx.com/microsoft-azure.html) von Xilinx.

**F:** Welche Version von XRT sollte ich verwenden?

**A:** xrrt_202020.2.8.832 

**F:** Was ist die Ziel-Bereitsstellungsplattform?

**A:** Verwenden Sie die folgenden Plattformen.
- xilinx-u250-gen3x16-xdma-platform-2.1-3_alle
- xilinx-u250-gen3x16-xdma-validiert_2.1-3005608.1 

**F:** Welche Plattform sollte ich für die Entwicklung anvisieren?

**A:** xilinx-u250-gen3x16-xdma-2.1-202010-1-dev_1-2954688_alle 

**F:** Welche sind die unterstützten OS (Betriebssysteme)? 

**A:** Xilinx und Microsoft haben Ubuntu 18.04 LTS und CentOS 7.8 validiert.

 Xilinx hat die folgenden Marketplace-Images erstellt, um die Bereitstellung dieser VMs zu vereinfachen. 

[Xilinx Alveo U250 Bereitstellung VM – Ubuntu18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Xilinx Alveo U250 Bereitstellung VM – CentOS7.8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

**V:** Kann ich meine eigenen Ubuntu/CentOS-VMs bereitstellen und XRT/Deployment Target Platform installieren? 

**A:** Ja.

**F:** Wenn ich meine eigene Ubuntu18.04 VM bereitstellen, was sind die erforderlichen Pakete und Schritte?

**A:** Verwenden Sie Kernel 4.1X per [Xilinx XRT Dokumentation](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf)
       
Installieren Sie die folgenden Pakete.
- xrrt_202020.2.8.832_18.04-amd64-xrt.deb
       
- xrrt_202020.2.8.832_18.04-amd64-azure.deb
       
- xilinx-u250-gen3x16-xdma-platform-2.1-3_alle_18.04.deb.tar.gz
       
- xilinx-u250-gen3x16-xdma-validiert_2.1-3005608.1_all.deb  

**F:** Unter Ubuntu kann ich nach dem Neustart meiner VM meine FPGA(s) nicht finden: 

**A:** Vergewissern Sie sich, dass Ihr Kernel nicht aktualisiert wurde (uname-a). Wenn dies der Fall ist, führen Sie ein Downgrade auf Kernel 4.1X durch. 

**V:** Wenn ich meine eigene CentOS7.8 VM bereitstelle, was sind dann die erforderlichen Pakete und Schritte?

**A:** Kernel Version verwenden: 3.10.0-1160.15.2.el7.x86_64

 Installieren Sie die folgenden Pakete.
   
 - xrt_202020.2.8.832_7.4.1708-x86_64-xrt.rpm 
      
 - xrrt_202020.2.8.832_7.4.1708-x86_64-azure.rpm 
     
 - xilinx-u250-gen3x16-xdma-plattform-2.1-3.noarch.rpm.tar.gz 
      
 - xilinx-u250-gen3x16-xdma-validiert-2.1-3005608.1.noarch.rpm  

**Host_Mem(SB): sudo xbutil host_mem --disable:** Wenn ich xbutil validate auf CentOS ausführe, erhalte ich diese Warnung: "WARNUNG: Kernel-Version 3.10.0-1160.15.2.el7.x86_64 wird nicht offiziell unterstützt. 4.18.0-193 ist die neueste unterstützte Version.” 

**A:** Dies kann getrost ignoriert werden. 

**F:** Was sind die Unterschiede zwischen OnPrem (lokal) und NP VMs?

**A:**  
<br>
<b>- In Bezug auf XOCL/XCLMGMT: </b>
<br>
Auf Azure-NP-VMs ist nur der Rollenendpunkt (Geräte-ID 5005) vorhanden, der den XOCL-Treiber verwendet.

Auf OnPrem-FPGA sind sowohl Verwaltungsendpunkt (Geräte-ID 5004) als auch Rollenendpunkt (Geräte-ID 5005) vorhanden, die die XCLMGMT- bzw. XOCL-Treiber verwenden.

<br>
<b>- In Bezug auf XRT: </b>
<br>
Auf Azure-NP-VMs unterstützt die XDMA 2.1-Plattform nur „Host_Mem (SB)“- und DDR-Datenaufbewahrungsfunktionen. 
<br>
Aktivieren von Host_Mem (SB) (bis zu 1 GB RAM): sudo xbutil host_mem --enable --size 1 g 

Deaktivieren von Host_Mem(SB): sudo xbutil host_mem --disable 

**F:** Kann ich xbmgmt-Befehle ausführen? 

**A:** Nein, auf Azure-VMS gibt es keine direkte Management-Unterstützung von der Azure-VM. 

 **F:** Muss ich eine PLP laden? 

**A:** Nein, das PLP wird automatisch für Sie geladen, sodass es nicht erforderlich ist, über xbmgmt-Befehle zu laden. 

 
**F:** Unterstützt Azure verschiedene plps? 

**A:** Derzeit leider nicht. Wir unterstützen nur die in den Bereitstellungs-Plattform-Paketen bereitgestellte PLP. 

**F:** Wie kann ich die PLP-Informationen Abfragen? 

**A:** Sie müssen die xbutil-Abfrage ausführen und den unteren Teil überprüfen. 



## <a name="other-sizes-and-information"></a>Weitere Größen und Informationen

- [Allgemeiner Zweck](sizes-general.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [GPU-optimiert](sizes-gpu.md)
- [High Performance Computing](sizes-hpc.md)
- [Vorherige Generationen](sizes-previous-gen.md)

Preisrechner: [Preisrechner](https://azure.microsoft.com/pricing/calculator/)

Weitere Informationen zu Datenträgertypen finden Sie unter [Welche Datenträgertypen stehen in Azure zur Verfügung?](disks-types.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.
