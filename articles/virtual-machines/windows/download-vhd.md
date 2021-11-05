---
title: Herunterladen einer Windows-VHD von Azure
description: Laden Sie eine Windows-VHD mithilfe des Azure-Portals herunter.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: 5d43ce73efa602cded23c533fefd29009998b809
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003350"
---
# <a name="download-a-windows-vhd-from-azure"></a>Herunterladen einer Windows-VHD von Azure

**Gilt für**: :heavy_check_mark: Windows VMs 

In diesem Artikel erfahren Sie, wie Sie mithilfe des Azure-Portals eine Windows-VHD-Datei (Virtual Hard Disk, virtuelle Festplatte) von Azure herunterladen.

## <a name="optional-generalize-the-vm"></a>Optional: Generalisieren des virtuellen Computers

Wenn Sie die VHD als [Image](tutorial-custom-images.md) verwenden möchten, um andere VMs zu erstellen, sollten Sie mithilfe von [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) das Betriebssystem generalisieren. Andernfalls müssen Sie den Datenträger für jeden virtuellen Computer kopieren, den Sie erstellen möchten.

Um die VHD als Image zum Erstellen von anderen VMs zu verwenden, generalisieren Sie die VM.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/)an, falls Sie dies noch nicht getan haben.
2. [Stellen Sie eine Verbindung mit der VM her](connect-logon.md). 
3. Öffnen Sie auf der VM das Eingabeaufforderungsfenster als Administrator.
4. Wechseln Sie zum Verzeichnis *%windir%\system32\sysprep*, und führen Sie „sysprep.exe“ aus.
5. Wählen Sie im Dialogfeld „Systemvorbereitungsprogramm“ die Option **Out-of-Box-Experience (OOBE) für System aktivieren** aus, und vergewissern Sie sich, dass **Verallgemeinern** ausgewählt ist.
6. Wählen Sie unter „Optionen für Herunterfahren“ die Option **Herunterfahren** aus, und klicken Sie dann auf **OK**. 

Wenn Sie Ihren aktuellen virtuellen Computer nicht generalisieren möchten, können Sie trotzdem ein generalisiertes Image erstellen, indem Sie zunächst eine [Momentaufnahme des Betriebssystemdatenträgers](#alternative-snapshot-the-vm-disk) erstellen, einen neuen virtuellen Computer aus der Momentaufnahme erstellen und dann die Kopie generalisieren.

## <a name="stop-the-vm"></a>Beenden des virtuellen Computers

Eine VHD kann nicht von Azure heruntergeladen werden, wenn sie an eine ausgeführte VM angefügt ist. Wenn Sie den virtuellen Computer weiter ausführen möchten, können Sie [eine Momentaufnahme erstellen und dann die Momentaufnahme herunterladen](#alternative-snapshot-the-vm-disk).

1. Klicken Sie im Hub-Menü im Azure-Portal auf **Virtuelle Computer**.
1. Wählen Sie die VM aus der Liste aus.
1. Klicken Sie auf dem Blatt für die VM auf **Beenden**.

### <a name="alternative-snapshot-the-vm-disk"></a>Alternative: Momentaufnahme des VM-Datenträgers

Machen Sie eine Momentaufnahme des Datenträgers, um ihn herunterzuladen.

1. Wählen Sie im [Portal](https://portal.azure.com) den virtuellen Computer aus.
2. Wählen Sie im linken Menü **Datenträger** aus und dann den Datenträger, den Sie momentaufnahmen möchten. Die Details des Datenträgers werden angezeigt.  
3. Wählen Sie im oberen Menü die Option **Momentaufnahme erstellen** aus. Die Seite **Momentaufnahme erstellen** wird geöffnet.
4. Geben Sie unter **Name** einen Namen für die Momentaufnahme ein. 
5. Wählen Sie für **Momentaufnahmetyp** die Option **Vollständig** oder **Inkrementell** aus.
6. Wählen Sie abschließend **Überprüfen + Erstellen** aus.

Ihre Momentaufnahme wird in Kürze erstellt und kann dann zum Herunterladen oder Erstellen eines anderen virtuellen Computers verwendet werden.

> [!NOTE]
> Wenn Sie den virtuellen Computer nicht zuerst beenden, ist die Momentaufnahme nicht bereinigt. Der Snapshot befindet sich in dem Zustand, als ob die VM zum Zeitpunkt der Erstellung des Snapshots heruntergefahren oder abgestürzt wäre.  Obwohl dies normalerweise sicher ist, kann dies zu Problemen führen, wenn die ausgeführten Anwendungen, die zu einem bestimmten Zeitpunkt ausgeführt werden, nicht absturzsicher waren.
>  
> Diese Methode wird nur für VMs mit einem einzelnen Betriebssystemdatenträger empfohlen. VMs mit mindestens einem Datenträger sollten vor dem Herunterladen oder vor dem Erstellen einer Momentaufnahme für den Betriebssystemdatenträger und jeden Datenträger beendet werden.

## <a name="generate-download-url"></a>Generieren der Download-URL

Um die VHD-Datei herunterzuladen, müssen Sie eine [SAS-URL (Shared Access Signature)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) generieren. Wenn die URL generiert wird, wird der URL eine Ablaufzeit zugewiesen.

1. Klicken Sie auf der Seite für den virtuellen Computer im linken Menü auf **Datenträger**.
1. Wählen Sie den Betriebssystemdatenträger für die VM aus.
1. Wählen Sie auf der Seite für den Datenträger im linken Menü die Option **Datenträgerexport** aus.
1. Die Standardablaufzeit der URL beträgt *3600* Sekunden (eine Stunde). Möglicherweise müssen Sie dies für Windows-Betriebssystemdatenträger oder große Datenträger erhöhen. **36.000** Sekunden (10 Stunden) sind in der Regel ausreichend.
1. Klicken Sie auf **URL generieren**.

> [!NOTE]
> Die Ablaufzeit wird von der Standardzeit erhöht, um genug Zeit für das Herunterladen der großen VHD-Datei für ein Windows Server-Betriebssystem bereitzustellen. Das Herunterladen großer VHDs kann je nach Verbindung und Größe des virtuellen Computers mehrere Stunden dauern. 
> 
> 

## <a name="download-vhd"></a>Herunterladen der VHD

1. Klicken Sie unter der URL, die generiert wurde, auf „VHD-Datei herunterladen“.
1. Möglicherweise müssen Sie im Browser auf **Speichern** klicken, um den Download zu starten. Der Standardname für die VHD-Datei lautet *abcd*.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie eine [VHD-Datei in Azure hochladen](upload-generalized-managed.md). 
- [Erstellen verwalteter Datenträger auf der Grundlage nicht verwalteter Datenträger in einem Speicherkonto](attach-disk-ps.md)
- [Verwalten von Azure-Datenträgern mit PowerShell](tutorial-manage-data-disk.md)
