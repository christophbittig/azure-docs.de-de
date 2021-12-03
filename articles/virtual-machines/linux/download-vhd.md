---
title: Herunterladen einer Linux-VHD von Azure
description: Laden Sie eine Linux-VHD mithilfe der Azure-Befehlszeilenschnittstelle und dem Azure-Portal herunter.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 393706ca463d8a3cc6edaede2689feae3e867bfa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074496"
---
# <a name="download-a-linux-vhd-from-azure"></a>Herunterladen einer Linux-VHD von Azure

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

In diesem Artikel erfahren Sie, wie Sie mithilfe des Azure-Portals eine Linux-VHD-Datei (Virtual Hard Disk, virtuelle Festplatte) von Azure herunterladen. 

## <a name="stop-the-vm"></a>Beenden des virtuellen Computers

Eine VHD kann nicht von Azure heruntergeladen werden, wenn sie an eine ausgeführte VM angefügt ist. Wenn Sie den virtuellen Computer weiter ausführen möchten, können Sie [eine Momentaufnahme erstellen und dann die Momentaufnahme herunterladen](#alternative-snapshot-the-vm-disk).

Den virtuellen Computer beenden:

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2.  Wählen Sie im linken Menü **Virtual Machines** aus.
3.  Wählen Sie die VM aus der Liste aus.
4.  Wählen Sie auf der Seite für den virtuellen Computer die Option **Beenden** aus.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Menüschaltfläche zum Beenden der VM":::

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
> Wenn Sie den virtuellen Computer nicht zuerst beenden, ist die Momentaufnahme nicht bereinigt. Der Snapshot befindet sich in dem Zustand, als ob die VM zum Zeitpunkt der Erstellung des Snapshots heruntergefahren oder abgestürzt wäre. Obwohl dies normalerweise sicher ist, kann dies zu Problemen führen, wenn die ausgeführten Anwendungen, die zu einem bestimmten Zeitpunkt ausgeführt werden, nicht absturzsicher waren.
>  
> Diese Methode wird nur für VMs mit einem einzelnen Betriebssystemdatenträger empfohlen. VMs mit mindestens einem Datenträger sollten vor dem Herunterladen oder vor dem Erstellen einer Momentaufnahme für den Betriebssystemdatenträger und jeden Datenträger beendet werden.

## <a name="generate-sas-url"></a>Generieren der SAS-URL

Um die VHD-Datei herunterzuladen, müssen Sie eine [SAS-URL (Shared Access Signature)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) generieren. Wenn die URL generiert wird, wird der URL eine Ablaufzeit zugewiesen.

1. Wählen Sie im Menü auf der Seite des virtuellen Computers die Option **Datenträger** aus.
2. Wählen Sie den Betriebssystem-Datenträger für den virtuellen Computer und anschließend **Datenträgerexport** aus.
1. Aktualisieren Sie bei Bedarf den Wert **URL läuft ab in (Sekunden)** , damit Sie genügend Zeit zum Abschließen des Downloads haben. Der Standardwert ist 3.600 Sekunden (eine Stunde).
3. Wählen Sie **URL generieren** aus.
 
      
## <a name="download-vhd"></a>Herunterladen der VHD

1.  Wählen Sie unter der generierten URL die Option **VHD-Datei herunterladen** aus.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Schaltfläche zum Herunterladen der VHD":::

2.  Gegebenenfalls muss im Browser die Option **Speichern** ausgewählt werden, um den Download zu starten. Der Standardname für die VHD-Datei lautet *abcd*.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum [Hochladen und Erstellen eines virtuellen Linux-Computers aus einem benutzerdefinierten Datenträger mithilfe von Azure CLI](upload-vhd.md). 
- [Verwalten von Azure-Datenträgern mit der Azure-CLI](tutorial-manage-disks.md)
