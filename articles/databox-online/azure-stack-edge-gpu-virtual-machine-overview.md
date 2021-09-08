---
title: Übersicht über VMs auf Ihrem Azure Stack Edge-Gerät
description: Hier werden virtuelle Computer auf Ihrem Azure Stack Edge-Gerät beschrieben.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 07/09/2021
ms.author: alkohli
ms.openlocfilehash: 73cb44dd3564f56f8edd5304a344a50f1e4b5c9d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346785"
---
# <a name="virtual-machines-on-your-azure-stack-edge-pro-gpu-device"></a>Virtuelle Computer auf Ihrem Azure Stack Edge Pro-Gerät mit GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Dieser Artikel bietet eine kurze Übersicht über virtuelle Computer (VMs), die auf Ihren Azure Stack Edge-Geräten ausgeführt werden, und unterstützte VM-Größen. Außerdem werden die verschiedenen Möglichkeiten zum Erstellen von VM-Images sowie zum Bereitstellen und anschließenden Verwalten von VMs zusammengefasst. 

## <a name="about-vms"></a>Informationen zu virtuellen Computern

Die Azure Stack Edge-Lösung bietet speziell entwickelte Hardware-as-a-Service-Geräte von Microsoft, mit denen Edgecomputing-Workloads bereitgestellt und schnelle, verwertbare Erkenntnisse in dem Edgebereich abgerufen werden können, in dem die Daten generiert werden. 

Je nach Umgebung und Typ der ausgeführten Anwendungen können Sie eine der folgenden Edgecomputing-Workloads auf diesen Geräten bereitstellen: 

- **Containerisiert**: Verwenden Sie IoT Edge oder Kubernetes, um Ihre containerisierten Anwendungen auszuführen.
- **Nicht containerisiert**: Stellen Sie virtuelle Windows- und Linux-Computer auf Ihren Geräten bereit, um nicht containerisierte Anwendungen auszuführen. 

Sie stellen eine VM auf Ihrem Gerät bereit, wenn Sie mehr Kontrolle über die Computingumgebung benötigen. Sie können VMs auf Ihrem Gerät auf verschiedene Arten verwenden, vom Entwickeln und Testen bis hin zum Ausführen von Anwendungen auf dem Edge-Gerät.

## <a name="before-you-create-a-vm"></a>Vor dem Erstellen einer VM

Bevor Sie beginnen, sollten Sie die folgenden Aspekte Ihres virtuellen Computers überprüfen:

- Die Größe des verwendeten virtuellen Computers
- Die maximal erstellbare Anzahl virtueller Computer auf Ihrem Gerät
- Das Betriebssystem für den virtuellen Computer
- Die Konfiguration des virtuellen Computers nach dem Start


### <a name="vm-size"></a>Größe des virtuellen Computers

Wenn Sie die Bereitstellung virtueller Computer planen, müssen Sie deren Größen kennen. Es sind mehrere Größen für die virtuellen Computer verfügbar, die Sie zum Ausführen von Apps und Workloads auf Ihrem Gerät verwenden können. Von der gewählten Größe hängen Faktoren wie Rechenleistung, Arbeitsspeicher und Speicherplatz ab. Weitere Informationen finden Sie unter [Unterstützte VM-Größen](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes).

Um die Größe und Anzahl von VMs zu berechnen, die Sie auf Ihrem Gerät bereitstellen können, berücksichtigen Sie die nutzbare Computeleistung auf Ihrem Gerät und andere ausgeführte Workloads. Bei Ausführung von Kubernetes beachten Sie auch die Computeanforderungen für die Kubernetes-Master- und -Worker-VMs.

|Kubernetes-VM-Typ|CPU- und Arbeitsspeicheranforderung|
|---------|---------|
|Master-VM|4 Kerne, 4 GB RAM|
|Worker-VM|12 Kerne, 32 GB RAM|

Informationen zur nutzbaren Computeleistung und zum Arbeitsspeicher auf Ihrem Gerät finden Sie in den [Spezifikationen zu Compute- und Arbeitsspeicherressourcen](azure-stack-edge-gpu-technical-specifications-compliance.md#compute-and-memory-specifications) für Ihr Gerätemodell. 

Für einen virtuellen GPU-Computer müssen Sie eine [VM-Größe aus der NCasT4-v3-Serie](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview) verwenden.


### <a name="vm-limits"></a>Grenzwerte für virtuelle Computer

Sie können maximal 24 VMs auf Ihrem Gerät ausführen. Dies ist ein weiterer Faktor, der bei der Bereitstellung Ihrer Workload zu beachten ist.

### <a name="operating-system-disks-and-images"></a>Betriebssystem-Datenträger und Images

Auf Ihrem Gerät können Sie nur VMs der 1. Generation mit einem festen VHD-Format (Virtual Hard Disk, virtuelle Festplatte) verwenden. VHDs werden zum Speichern des Computerbetriebssystems und von Daten verwendet. VHDs werden auch für die Images verwendet, die Sie zum Installieren eines Betriebssystems verwenden. 

Die Images, die Sie zum Erstellen von VM-Images verwenden, können generalisiert oder spezialisiert werden. Beim Erstellen von Images für Ihre VMs müssen Sie die Images vorbereiten. Sehen Sie sich die verschiedenen Möglichkeiten zum Vorbereiten und Verwenden von VM-Images auf Ihrem Gerät an:

- [Vorbereiten eines generalisierten Windows-Images von einer VHD](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [Vorbereiten eines generalisierten Images aus einer ISO-Datei](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)
- [Erstellen benutzerdefinierter VM-Images von einer Azure-VM](azure-stack-edge-gpu-create-virtual-machine-image.md)
- [Verwenden eines spezialisierten Images](azure-stack-edge-gpu-deploy-vm-specialized-image-powershell.md)

### <a name="extensions"></a>Erweiterungen

Für die VMs auf Ihrem Gerät sind benutzerdefinierte Skripterweiterungen verfügbar, mit denen Sie Workloads konfigurieren können, indem Ihr Skript bei der Bereitstellung der VM ausgeführt wird.

Weitere Informationen finden Sie unter [Bereitstellen von benutzerdefinierten Skripterweiterungen auf VMs, die auf Ihrem Gerät ausgeführt werden](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md).

Sie können auch GPU-Erweiterungen für Ihre VM verwenden, falls Sie GPU-Treiber bei der Bereitstellung der GPU-VMs installieren möchten. Weitere Informationen finden Sie unter [Erstellen von GPU-VMs](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms) und [Installieren von GPU-Erweiterungen](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md).

## <a name="create-a-vm"></a>Erstellen einer VM

Zum Bereitstellen einer VM müssen Sie zunächst alle Ressourcen erstellen, die zum Erstellen einer VM erforderlich sind. Unabhängig von der Methode, die zum Erstellen einer VM verwendet wird, führen Sie die folgenden Schritte aus: 

1. Stellen Sie eine Verbindung mit der lokalen Azure Resource Manager-Instanz auf Ihrem Gerät her. 
1. Ermitteln Sie das integrierte Abonnement auf dem Gerät.
1. Stellen Sie Ihr VM-Image bereit.
    1. Erstellen Sie eine Ressourcengruppe im integrierten Abonnement. Die Ressourcengruppe enthält die VM und alle zugehörigen Ressourcen.
    2. Erstellen Sie ein lokales Speicherkonto auf dem Gerät, um die VHD zu speichern, die zum Erstellen eines VM-Images verwendet wird.
    3. Laden Sie ein Windows/Linux-Quellimage in das Speicherkonto hoch, um einen verwalteten Datenträger zu erstellen.
    4. Verwenden Sie den verwalteten Datenträger, um ein VM-Image zu erstellen.
1. Aktivieren Sie Compute an einem Geräteport, um einen virtuellen Switch zu erstellen.
    1. Dadurch wird ein virtuelles Netzwerk mit dem virtuellen Switch erstellt, der an den Port angefügt ist, an dem Sie Compute aktiviert haben.  
1. Erstellen Sie eine VM mithilfe des zuvor erstellten VM-Images, des virtuellen Netzwerks und der virtuellen Netzwerkschnittstellen, um innerhalb des virtuellen Netzwerks zu kommunizieren, und weisen Sie eine öffentliche IP-Adresse für den Remotezugriff auf die VM zu. Schließen Sie optional Datenträger ein, um mehr Speicher für Ihre VM bereitzustellen.
 
Der Bereitstellungsworkflow wird im folgenden Diagramm gezeigt:

![Diagramm des VM-Bereitstellungsworkflows.](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

Es gibt mehrere Möglichkeiten, eine VM auf Ihrem Gerät bereitzustellen. Welche Möglichkeit Sie auswählen, richtet sich nach Ihrer Umgebung. In der folgenden Tabelle sind die verschiedenen Möglichkeiten zum Bereitstellen einer VM auf Ihrem Gerät zusammengefasst:

|Methode|Artikel|
|---------|---------|
|Azure-Portal|[Bereitstellen einer VM auf Ihrem Gerät über das Azure-Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)|
|Vorlagen|[Bereitstellen einer VM auf Ihrem Gerät über Vorlagen](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)|
|PowerShell|[Bereitstellen einer VM auf Ihrem Gerät über Azure PowerShell-Cmdlets](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)<br>[Bereitstellen einer VM auf Ihrem Gerät mithilfe eines Azure PowerShell-Skripts](azure-stack-edge-gpu-deploy-virtual-machine-powershell-script.md)|
|CLI/Python|[Bereitstellen einer VM auf Ihrem Gerät über die Azure CLI/Python](azure-stack-edge-gpu-deploy-virtual-machine-cli-python.md)|
|GPU|[Bereitstellen einer VM auf Ihrem Gerät mithilfe von GPUs](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)|


## <a name="manage-your-vm"></a>Verwalten Ihres virtuellen Computers

Sie können die VMs auf Ihrem Gerät über das Azure-Portal, die PowerShell-Schnittstelle des Geräts oder direkt über die APIs verwalten. Zu den typischen Verwaltungsaufgaben zählen unter anderem:

- Abrufen von Informationen zu einer VM
- Herstellen einer Verbindung mit einer VM; Starten, Beenden und Löschen von VMs
- Verwalten von Datenträgern, VM-Größen, Netzwerkschnittstellen und virtuellen Switches
- Sichern von VM-Datenträgern

### <a name="get-information-about-your-vm"></a>Abrufen von Informationen zu Ihrem virtuellen Computer

Führen Sie die folgenden Schritte aus, um über das Azure-Portal weitere Informationen zu Ihrer VM abzurufen:

1. Navigieren Sie zur Azure Stack Edge-Ressource für Ihr Gerät und dann zu **Virtuelle Computer > Übersicht**. 
1. Navigieren Sie auf der Seite **Übersicht** zu **Virtuelle Computer**, und wählen Sie den virtuellen Computer aus, an dem Sie interessiert sind. Sie können dann die Details der VM anzeigen. 

### <a name="connect-to-your-vm"></a>Herstellen einer Verbindung mit Ihrer VM

Je nach Betriebssystem, das auf Ihrer VM ausgeführt wird, können Sie wie folgt eine Verbindung mit der VM herstellen: 

- [Herstellen einer Verbindung mit einer Windows-VM auf Ihrem Gerät](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#connect-to-windows-vm)
- [Herstellen einer Verbindung mit einer Linux-VM auf Ihrem Gerät](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#connect-to-linux-vm)

### <a name="start-stop-delete-vms"></a>Starten, Beenden und Löschen von VMs

Sie können die [VM einschalten](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#turn-on-the-vm), oder die [VM anhalten oder herunterfahren](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm). Schließlich können Sie die [VMs löschen](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#delete-the-vm), sobald Sie sie nicht mehr verwenden.

### <a name="manage-network-interfaces-virtual-switches"></a>Verwalten von Netzwerkschnittstellen und virtuellen Switches

Sie können [Netzwerkschnittstellen für Ihre VMs hinzufügen, ändern und trennen](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md). Sie können auch [neue virtuelle Switches auf Ihrem Gerät erstellen](azure-stack-edge-gpu-create-virtual-switch-powershell.md), um VMs bereitzustellen. 

### <a name="manage-data-disks-vm-size"></a>Verwalten von Datenträgern und VM-Größe

Sie können über das Azure-Portal [einer vorhandenen VM einen Datenträger hinzufügen](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md#add-a-data-disk), [einen vorhandenen Datenträger anfügen](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md#add-a-data-disk), [einen Datenträger trennen](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md#detach-a-data-disk) und schließlich [die Größe der VM selbst ändern](azure-stack-edge-gpu-manage-virtual-machine-resize-portal.md#resize-a-vm).

### <a name="back-up-vms"></a>Sichern virtueller Computer

Sie können die VM-Datenträger sichern und bei einem Geräteausfall die Daten aus den Sicherungen wiederherstellen. Weitere Informationen finden Sie unter [Sichern von VM-Datenträgern](azure-stack-edge-gpu-back-up-virtual-machine-disks.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [VM-Größen und -Typen für Azure Stack Edge Pro-Geräte mit GPU](azure-stack-edge-gpu-virtual-machine-sizes.md).


