---
title: Konfigurieren einer GPU für Azure Virtual Desktop – Azure
description: Erfahren Sie, wie Sie durch GPU beschleunigtes Rendering und schnellere Codierung in Azure Virtual Desktop ermöglichen.
author: gundarev
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 0bd470650ca3a2d6a8fd2d672d2eac0e5790dc89
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130241001"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-azure-virtual-desktop"></a>Konfigurieren der Beschleunigung durch Graphics Processing Units (GPUs) für Azure Virtual Desktop

>[!IMPORTANT]
>Dieser Inhalt gilt für Azure Virtual Desktop mit Azure Virtual Desktop-Objekten für Azure Resource Manager. Wenn Sie Azure Virtual Desktop (klassisch) ohne Azure Resource Manager-Objekte verwenden, finden Sie weitere Informationen in [diesem Artikel](./virtual-desktop-fall-2019/configure-vm-gpu-2019.md).

Azure Virtual Desktop unterstützt durch GPU beschleunigtes Rendern und Codieren, um die Leistung und Skalierbarkeit von Apps zu erhöhen. Die GPU-Beschleunigung ist besonders für grafikintensive Apps essentiell.

Anhand der Anweisungen in diesem Artikel können Sie eine GPU-optimierte Azure-VM erstellen, ihrem Hostpool hinzufügen und so konfigurieren, dass sie die GPU-Beschleunigung zum Rendern und Codieren verwendet. In diesem Artikel wird vorausgesetzt, dass Sie bereits einen Azure Virtual Desktop-Mandanten konfiguriert haben.

## <a name="select-an-appropriate-gpu-optimized-azure-virtual-machine-size"></a>Auswählen einer geeigneten GPU-optimierten Größe für virtuelle Azure-Computer

Wählen Sie eine der VM-Größen von Azure der [NV-Serie](../virtual-machines/nv-series.md), [NVv3-Serie](../virtual-machines/nvv3-series.md) oder [NVv4-Serie](../virtual-machines/nvv4-series.md) aus. Diese sind auf die App- und Desktopvirtualisierung ausgerichtet und ermöglichen es, die meisten Apps und die Windows-Benutzeroberfläche über die GPU zu beschleunigen. Welche VM-Größe sich am besten für Ihren Hostpool eignet, hängt von mehreren Faktoren ab, z. B. von den Workloads Ihrer App, der gewünschten Benutzerfreundlichkeit und den Kosten. Im Allgemeinen bieten größere und leistungsfähigere GPUs ein besseres Benutzererlebnis bei einer gegebenen Benutzerdichte, während kleinere und fraktionierte GPU-Größen eine differenziertere Kontrolle über Kosten und Qualität ermöglichen. Beachten Sie bei der Auswahl der VM, dass VMs der NV-Serie eingestellt werden. Details finden Sie im Artikel zur [Einstellung der NV-Serie](../virtual-machines/nv-series-retirement.md).

>[!NOTE]
>Die VMs der NC-, NCv2-, NCv3-, ND- und NDv2-Serien von Azure sind im Allgemeinen nicht für Azure Virtual Desktop-Sitzungshosts geeignet. Diese VMs sind auf spezialisierte, hochleistungsfähige Compute- oder Machine Learning-Tools ausgerichtet, wie die mit NVIDIA CUDA erstellten. Sie unterstützen keine GPU-Beschleunigung für die meisten Anwendungen oder die Windows-Benutzeroberfläche.


## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Erstellen eines Hostpools, Bereitstellen der VM und Konfigurieren einer App-Gruppe

Erstellen Sie einen neuen Hostpool mit der gewählten VM-Größe. Anweisungen dazu finden Sie unter: [Tutorial: Erstellen eines Hostpools mit dem Azure-Portal](./create-host-pools-azure-marketplace.md).

Azure Virtual Desktop unterstützt das durch GPU beschleunigte Rendern und Codieren für die folgenden Betriebssysteme:

* Windows 10, Version 1511 und höher
* Windows Server 2016 oder höher

>[!NOTE]
>Das Betriebssystem für mehrere Sitzungen wird zwar nicht speziell aufgeführt, die GRID-Lizenz unterstützt für NV-Instanzen jedoch 25 gleichzeitige Benutzer*innen. Weitere Informationen finden Sie unter [NV-Serie](../virtual-machines/nv-series.md).

Außerdem müssen Sie eine App-Gruppe konfigurieren oder die Standarddesktop-App-Gruppe „Desktop Application Group“ verwenden, die automatisch erstellt wird, wenn Sie einen neuen Hostpool erstellen. Anweisungen finden Sie unter [Tutorial: Verwalten von App-Gruppen für Azure Virtual Desktop](./manage-app-groups.md).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Installieren unterstützter Grafiktreiber auf Ihrer VM

Nach der Bereitstellung müssen Sie die entsprechenden Grafiktreiber installieren, um die GPU-Funktionen von Azure-VMs der N-Serie in Azure Virtual Desktop nutzen zu können. Folgen Sie den Anweisungen unter [Unterstützte Betriebssysteme und Treiber](../virtual-machines/sizes-gpu.md#supported-operating-systems-and-drivers), um Treiber zu installieren. Es werden nur von Azure verteilte Treiber unterstützt.

* Für Azure-VMs der NV-Serie oder NVv3-Serie unterstützen nur NVIDIA GRID-Treiber und nicht NVIDIA CUDA-Treiber die GPU-Beschleunigung für die meisten Anwendungen und die Windows-Benutzeroberfläche. Wenn Sie sich für die manuelle Installation von Treibern entscheiden, stellen Sie sicher, dass Sie GRID-Treiber installieren. Wenn Sie sich für die Installation von Treibern über die Azure VM-Erweiterung entscheiden, werden GRID-Treiber automatisch für diese VM-Größen installiert.
* Für Azure-VMs der NVv4-Serie installieren Sie die von Azure bereitgestellten AMD-Treiber. Sie können sie automatisch mit der Azure-VM-Erweiterung installieren oder manuell installieren.

Wenn die Treiber installiert wurden, muss die VM neu gestartet werden. Überprüfen Sie anhand der oben aufgelisteten Schritte, dass die Grafiktreiber erfolgreich installiert wurden.

## <a name="configure-gpu-accelerated-app-rendering"></a>Konfigurieren des durch GPU beschleunigten App-Rendering

Apps und Desktops, die mit Konfigurationen für mehrere Sitzungen ausgeführt werden, werden standardmäßig mit CPUs gerendert und nutzen nicht die verfügbaren GPUs. Konfigurieren Sie eine Gruppenrichtlinie für den Sitzungshost, um durch GPU beschleunigtes Rendering zu aktivieren:

1. Stellen Sie mit einem Konto mit lokalen Administratorrechten eine Verbindung mit dem Desktop der VM her.
2. Öffnen Sie das Startmenü, und geben Sie „gpedit.msc“ ein, um den Gruppenrichtlinien-Editor zu öffnen.
3. Navigieren Sie zu **Computer Configuration** > **Administrative Templates** > **Windows Components** > **Remote Desktop Services** > **Remote Desktop Session Host** > **Remote Session Environment** (Computerkonfiguration > Administrative Vorlagen > Windows-Komponenten > Remotedesktopdienste > Remotedesktop-Sitzungshost > Remotesitzungsumgebung).
4. Wählen Sie die Richtlinie **Hardwaregrafikadapter für alle Remotedesktopdienste-Sitzungen verwenden** aus, und legen Sie für diese Richtlinie **Aktiviert** fest, um das GPU-Rendern in der Remotesitzung zu aktivieren.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Konfigurieren der durch GPU beschleunigten Framecodierung

Der Remotedesktop codiert alle Grafiken, die von Apps und Desktops gerendert werden für die Übertragung an Remotedesktopclients (unabhängig davon, ob diese mit GPU oder CPU gerendert wurden). Wenn ein Teil des Bildschirms häufig aktualisiert wird, wird dieser mit einem Videocodec (H.264/AVC) codiert. Standardmäßig nutzt der Remotedesktop die verfügbaren GPUs nicht für diese Codierung. Konfigurieren Sie eine Gruppenrichtlinie für den Sitzungshost, um durch GPU beschleunigte Framecodierung zu aktivieren. Führen Sie ähnlich wie oben die folgenden Schritte durch:

>[!NOTE]
>Die durch GPU beschleunigte Framecodierung ist in VMs der Serie NVv4 nicht verfügbar.

1. Wählen Sie die Richtlinie **H.264/AVC-Hardwarecodierung für Remotedesktopverbindungen konfigurieren** aus, und **aktivieren Sie diese**, um die Hardwarecodierung für H.264/AVC in der Remotesitzung zu aktivieren.

    >[!NOTE]
    >Legen Sie in Windows Server 2016 die Option **AVC-Hardwarecodierung bevorzugen** auf **Immer versuchen** fest.

2. Erzwingen Sie ein Gruppenrichtlinienupdate, nachdem Sie die Gruppenrichtlinien bearbeitet haben. Öffnen Sie die Eingabeaufforderung, und geben Sie Folgendes ein:

    ```cmd
    gpupdate.exe /force
    ```

3. Melden Sie sich bei der Remotedesktopsitzung ab.

## <a name="configure-fullscreen-video-encoding"></a>Konfigurieren der Vollbildvideocodierung

>[!NOTE]
>Die Vollbildvideocodierung kann auch ohne eine GPU aktiviert werden.

Wenn Sie häufig Anwendungen verwenden, die Inhalte mit hoher Bildfrequenz herstellen, z. B. Anwendungen zur 3D-Modellierung oder CAD/CAM- und Videoanwendungen, können Sie die Vollbildvideocodierung für eine Remotesitzung aktivieren. Das Vollbildvideoprofil bietet eine höhere Bildfrequenz und mehr Benutzerfreundlichkeit für solche Anwendungen, allerdings zum Nachteil der Netzwerkbandbreite, des Sitzungshosts und der Clientressourcen. Es wird empfohlen, die durch GPU beschleunigte Framecodierung für die Codierung von Vollbildvideos zu verwenden. Konfigurieren Sie eine Gruppenrichtlinie für den Sitzungshost, um die Vollbildvideocodierung zu aktivieren. Führen Sie ähnlich wie oben die folgenden Schritte durch:

1. Wählen Sie die Richtlinie **H.264/AVC 444-Grafikmodus für Remotedesktopverbindungen priorisieren** aus, und **aktivieren Sie diese**, um den H.264/AVC 444-Codec in der Remotesitzung zu erzwingen.
2. Erzwingen Sie ein Gruppenrichtlinienupdate, nachdem Sie die Gruppenrichtlinien bearbeitet haben. Öffnen Sie die Eingabeaufforderung, und geben Sie Folgendes ein:

    ```cmd
    gpupdate.exe /force
    ```

3. Melden Sie sich bei der Remotedesktopsitzung ab.
## <a name="verify-gpu-accelerated-app-rendering"></a>Überprüfen des durch GPU beschleunigten App-Renderings

Führen Sie einen der folgenden Schritte durch, um zu überprüfen, dass Ihre Apps die GPUs für das Rendering verwenden:

* Verwenden Sie für Azure-VMs mit NVIDIA-GPUs das Hilfsprogramm `nvidia-smi`, um die GPU-Nutzung beim Ausführen Ihrer Apps zu überprüfen. Dieser Prozess wird unter [Überprüfen der Treiberinstallation](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation) beschrieben.
* In unterstützten Betriebssystemversionen können Sie die GPU-Nutzung über den Task-Manager prüfen. Wählen Sie auf der Registerkarte „Leistung“ die GPU aus, um zu prüfen, ob diese von Apps genutzt wird.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Überprüfen der durch GPU beschleunigten Framecodierung

So können Sie überprüfen, ob der Remotedesktop die durch GPU beschleunigte Codierung verwendet:

1. Stellen Sie eine Verbindung mit dem Desktop der VM mithilfe des Azure Virtual Desktop-Clients her.
2. Starten Sie die Ereignisanzeige, und navigieren Sie zum folgenden Knoten: **Applications and Services Logs** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **Operational** (Anwendungs- und Dienstprotokolle > Microsoft > Windows > RemoteDesktopServices-RdpCoreCDV > Betriebsbereit)
3. Suchen Sie nach der Ereignis-ID 170, um zu überprüfen, ob die durch GPU beschleunigte Codierung verwendet wird. Wenn „Der AVC-Hardware-Encoder ist aktiviert: 1“ angezeigt wird, wird die GPU-Codierung verwendet.

## <a name="verify-fullscreen-video-encoding"></a>Überprüfen der Vollbildvideocodierung

So überprüfen Sie, ob Remotedesktop die Vollbildvideocodierung verwendet:

1. Stellen Sie eine Verbindung mit dem Desktop der VM mithilfe des Azure Virtual Desktop-Clients her.
2. Starten Sie die Ereignisanzeige, und navigieren Sie zum folgenden Knoten: **Applications and Services Logs** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **Operational** (Anwendungs- und Dienstprotokolle > Microsoft > Windows > RemoteDesktopServices-RdpCoreCDV > Betriebsbereit)
3. Suchen Sie nach der Ereignis-ID 162, um zu ermitteln, ob die Vollbildvideocodierung verwendet wird. Wenn „AVC aktiviert: 1, Anfangsprofil: 2048“ angezeigt wird, wird AVC 444 verwendet.

## <a name="next-steps"></a>Nächste Schritte

Anhand der beschriebenen Anweisungen sollten Sie nun die GPU-Beschleunigung auf einem Sitzungshost (einer VM) verwenden können. Für das Verwenden der GPU-Beschleunigung für einen größeren Hostpool können Sie folgende Aspekte in Erwägung ziehen:

* Ziehen Sie die Verwendung einer [VM-Erweiterung](../virtual-machines/extensions/overview.md) in Betracht, um die Treiberinstallation und Updates für mehrere VMs zu vereinfachen. Verwenden Sie die [NVIDIA-GPU-Treibererweiterung](../virtual-machines/extensions/hpccompute-gpu-windows.md) für VMs mit NVIDIA-GPUs, und verwenden Sie die [AMD-GPU-Treibererweiterung](../virtual-machines/extensions/hpccompute-amd-gpu-windows.md) für VMs mit AMD-GPUs.
* Die Verwendung einer Active Directory Domain Services-Gruppenrichtlinie, um die Konfiguration von Gruppenrichtlinien für mehrere VMs zu vereinfachen. Weitere Informationen zur Bereitstellung von Gruppenrichtlinien in der AD DS-Domäne finden Sie unter [Working with Group Policy Objects (Verwenden von Gruppenrichtlinienobjekten)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731212(v=ws.11)).
