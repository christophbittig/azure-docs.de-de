---
title: Einrichtung von AMD-GPU-Treibern für die Azure N-Serie unter Windows
description: Einrichten von AMD-GPU-Treibern für virtuelle Computer der N-Serie unter Windows Server oder Windows in Azure
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: ec4f09249bda14eb29d7dc9704a6096848a78557
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122343131"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Installieren von AMD-GPU-Treibern für virtuelle Computer der N-Serie unter Windows

Um die GPU-Funktionen von virtuellen Computern der neuen Azure NVv4-Serie unter Windows nutzen zu können, müssen AMD-GPU-Treiber installiert werden. Die [AMD-GPU-Treibererweiterung](../extensions/hpccompute-amd-gpu-windows.md) installiert AMD-GPU-Treiber auf einer VM der NVv4-Reihe. Installieren oder verwalten Sie die Erweiterung mithilfe des Azure-Portals oder mit Tools wie Azure PowerShell oder Azure Resource Manager-Vorlagen. Informationen zu unterstützten Betriebssystemen und Bereitstellungsschritten finden Sie in der [Dokumentation zur AMD-GPU-Treibererweiterung](../extensions/hpccompute-amd-gpu-windows.md).

Wenn Sie AMD-GPU-Treiber manuell installieren möchten, finden Sie in diesem Artikel Informationen zu unterstützten Betriebssystemen und Treibern sowie Schritte zur Installation und Überprüfung.

Auf NVv4-VMs werden nur von Microsoft veröffentlichte GPU-Treiber unterstützt. Installieren Sie KEINE GPU-Treiber aus einer anderen Quelle.

Informationen zu grundlegenden Spezifikationen, Speicherkapazitäten und Details zu den Datenträgern finden Sie unter [GPU-optimierte Größen von virtuellen Windows-Computern](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json).



## <a name="supported-operating-systems-and-drivers"></a>Unterstützte Betriebssysteme und Treiber

| OS | Treiber |
| -------- |------------- |
| Windows 10 – Build 2009, 2004, 1909 <br/><br/>Windows 10 Enterprise mit mehreren Sitzungen – Build 2009, 2004, 1909 <br/><br/>Windows Server 2016 (Version 1607)<br/><br/>Windows Server 2019 (Version 1909) | [21.Q2](https://download.microsoft.com/download/3/4/8/3481cf8d-1706-49b0-aa09-08c9468305ab/AMD-Azure-NVv4-Windows-Driver-21Q2.exe) (EXE-Datei) |

Die bisher unterstützte Treiberversion für Windows-Builds bis Version 1909 lautet [20.Q4](https://download.microsoft.com/download/f/1/6/f16e6275-a718-40cd-a366-9382739ebd39/AMD-Azure-NVv4-Driver-20Q4.exe) (EXE-Datei)

 > [!NOTE]
   >  Wenn Sie Build 1903/1909 verwenden, müssen Sie möglicherweise die folgende Gruppenrichtlinie aktualisieren, um eine optimale Leistung zu erzielen. Diese Änderungen sind für andere Windows-Builds nicht erforderlich.
   >  
   >  [Computerkonfiguration->Richtlinien->Windows-Einstellungen->Administrative Vorlagen->Windows-Komponenten->Remotedesktopdienste >-Remotedesktop-Sitzungshost->Umgebung für Remotesitzung], legen Sie für die Richtlinie [Verwenden des WDDM-Grafikanzeigetreibers für Remotedesktopverbindungen] „Deaktiviert“ fest.
   >  

 
## <a name="driver-installation"></a>Treiberinstallation

1. Stellen Sie über Remotedesktop eine Verbindung mit den einzelnen virtuellen Computern der NVv4-Serie her.

2. Wenn Sie die vorherige Treiberversion deinstallieren müssen, laden Sie das [AMD-Bereinigungsprogramm](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe) herunter. Verwenden Sie nicht das im Lieferumfang des vorherigen Treibers enthaltene Programm.

3. Laden Sie den neuesten Treiber herunter, und installieren Sie ihn.

4. Starten Sie die VM neu.

## <a name="verify-driver-installation"></a>Überprüfen der Treiberinstallation

Sie können die Treiberinstallation im Geräte-Manager überprüfen. Das folgende Beispiel zeigt die erfolgreiche Konfiguration der Radeon Instinct MI25-Karte auf einem virtuellen Azure-NVv4-Computer.
<br />

![Screenshot: Erfolgreiche Konfiguration der Radeon Instinct MI25-Karte auf einem virtuellen Azure-NVv4-Computer](./media/n-series-amd-driver-setup/device-manager.png)

Mit dxdiag können Sie die GPU-Anzeigeeigenschaften einschließlich Video-RAM überprüfen. Das folgende Beispiel zeigt eine 1/2-Partition der Radeon Instinct MI25-Karte auf einem virtuellen Azure-NVv4-Computer.
<br />
![Screenshot: 1/2-Partition der Radeon Instinct MI25-Karte auf einem virtuellen Azure-NVv4-Computer](./media/n-series-amd-driver-setup/dxdiag-output-new.png)

Wenn Sie Windows 10 Build 1903 oder höher ausführen, zeigt „dxdiag“ keine Informationen auf der Registerkarte „Anzeige“. Verwenden Sie die Option „Alle Informationen speichern“ im unteren Bereich, dann zeigt die Ausgabedatei die Informationen zur AMD-MI25-GPU.

![Eigenschaften des GPU-Treibers](./media/n-series-amd-driver-setup/dxdiag-details.png)
