---
title: Vorbereiten eines generalisierten Images aus einer ISO-Datei zum Bereitstellen von VMs auf Azure Stack Edge Pro-GPU-Geräten
description: Hier erfahren Sie, wie Sie ein generalisiertes Windows-VM-Image aus einer ISO-Datei erstellen. Verwenden Sie dieses generalisierte Image, um VMs auf Ihrem Azure Stack Edge Pro-GPU-Gerät bereitzustellen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/25/2021
ms.author: alkohli
ms.openlocfilehash: 22f9c86342428d3d011eb787745f74ac6ea026f8
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970319"
---
# <a name="prepare-generalized-image-from-iso-to-deploy-vms-on-azure-stack-edge-pro-gpu"></a>Vorbereiten eines generalisierten Images aus einer ISO-Datei zum Bereitstellen von VMs auf Azure Stack Edge Pro-GPU-Geräten 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Wenn Sie VMs auf Ihrem Azure Stack Edge Pro-GPU-Gerät bereitstellen möchten, müssen Sie in der Lage sein, benutzerdefinierte VM-Images zu erstellen, mit denen Sie wiederum VMs erstellen können. In diesem Artikel wird beschrieben, wie Sie ein Windows-VM-Image mithilfe eines ISO-Installationsmediums vorbereiten und anschließend generalisieren, damit Sie es zum Bereitstellen mehrerer neuer VMs auf Ihrem Azure Stack Edge Pro-GPU-Gerät verwenden können.

Informationen zum Vorbereiten eines generalisierten Images aus einer Windows-VHD oder -VHDX finden Sie unter [Vorbereiten eines generalisierten Images aus einer Windows-VHD zum Bereitstellen von VMs auf Azure Stack Edge Pro-GPU-Geräten](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md).

## <a name="about-vm-images"></a>Informationen zu VM-Images

Sie können eine Windows-VHD oder -VHDX verwenden, um ein *spezialisiertes* Image oder ein *generalisiertes* Image zu erstellen. In der folgenden Tabelle sind die wichtigsten Unterschiede zwischen *spezialisierten* und *generalisierten* Images zusammengefasst:

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>Workflow 

Der allgemeine Workflow zum Erstellen einer generalisierten virtuellen Windows-Festplatte (Virtual Hard Disk, VHD) mithilfe einer ISO-Datei ist wie folgt:

1. Vorbereiten der Quell-VM mit einem ISO-Image:
   1. Erstellen Sie in Hyper-V-Manager eine neue, leere VHD mit fester Größe.
   1. Verwenden Sie diese VHD, um eine neue VM zu erstellen.
   1. Binden Sie Ihr ISO-Image auf dem DVD-Laufwerk der neuen VM ein.
1. Starten der VM und Installieren des Windows-Betriebssystems
1. Generalisieren der VHD mit dem Hilfsprogramm *sysprep*
1. Kopieren des generalisierten Images in Azure Blob Storage

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie eine generalisierte Windows-VHD anhand eines ISO-Images erstellen können, müssen Sie Folgendes sicherstellen:

- Sie verfügen über ein ISO-Image für die unterstützte Windows-Version, aus dem Sie eine generalisierte VHD erstellen möchten. Windows-ISO-Images können aus dem [Microsoft Evaluation Center](https://www.microsoft.com/en-us/evalcenter/) heruntergeladen werden.

- Sie haben Zugriff auf einen Windows-Client mit installiertem Hyper-V-Manager.

- Sie haben Zugriff auf ein Azure Blob Storage-Konto, in dem Sie die VHD nach der Vorbereitung speichern.

## <a name="prepare-source-vm-using-an-iso"></a>Vorbereiten der Quell-VM mithilfe einer ISO-Datei

Wenn Sie ein ISO-Image verwenden, um das Betriebssystem auf Ihrem VM-Image zu installieren, erstellen Sie zunächst in Hyper-V-Manager eine leere VHD mit fester Größe. Anschließend verwenden Sie diese VHD, um eine VM zu erstellen. Danach fügen Sie das ISO-Image an die VM an.

#### <a name="create-new-vhd-in-hyper-v-manager"></a>Erstellen einer neuen VHD in Hyper-V-Manager

Der erste Schritt besteht darin, in Hyper-V-Manager eine neue VHD der Generation 1 zu erstellen, die als Quell-VHD für eine neue VM dient.

Führen Sie zum Erstellen der VHD die folgenden Schritte aus:

1. Öffnen Sie Hyper-V-Manager auf dem Clientsystem. Wählen Sie im Menü **Aktion** die Option **Neu** und dann **Festplatte** aus.

   ![Auswählen von „Neu“ > „Festplatte“](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-01.png)

1. Wählen Sie unter **Datenträgerformat auswählen** die Option **VHD** aus. Wählen Sie anschließend **Weiter >** aus. 

   ![Auswählen von „VHD“ als Datenträgerformat](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-02.png)

2. Wählen Sie unter **Datenträgertyp auswählen** die Option **Feste Größe** aus. Wählen Sie anschließend **Weiter >** aus.

   ![Auswählen von „Feste Größe“ als Datenträgertyp](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-03.png)

3. Geben Sie unter **Name und Speicherort angeben** einen Namen und Speicherort für die neue VHD ein. Wählen Sie anschließend **Weiter >** aus.

   ![Eingeben des Namens und Speicherorts für die VHD](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-04.png)

4. Wählen Sie unter **Datenträger konfigurieren** die Option **Neue virtuelle Festplatte ohne Inhalt erstellen** aus, und geben Sie die gewünschte Datenträgergröße ein (in der Regel min. 20 GB für Windows Server). Wählen Sie anschließend **Weiter >** aus.

   ![Einstellungen zum Erstellen einer neuen leeren VHD und Angeben der Größe](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-05.png)

5. Überprüfen Sie unter **Zusammenfassung** Ihre Auswahl, und wählen Sie **Fertig stellen** aus, um die neue VHD zu erstellen. Der Prozess dauert je nach Größe der erstellten VHD mindestens fünf Minuten.

   ![Zusammenfassung der VHD-Einstellungen](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-06.png)

#### <a name="create-hyper-v-vm-from-vhd"></a>Erstellen einer Hyper-V-VM anhand der VHD

Nun verwenden Sie die soeben erstellte VHD, um eine neue VM zu erstellen.

Führen Sie die folgenden Schritte aus, um Ihre neue VM zu erstellen:

1. Öffnen Sie Hyper-V-Manager auf Ihrem Windows-Client.

2. Wählen Sie im Bereich **Aktionen** die Option **Neu** und dann **Virtueller Computer** aus.

   ![Auswählen von „Neu“ > „Virtueller Computer“ im Menü auf der rechten Seite](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-07.png)

3. Geben Sie im Assistenten für neue VMs den Namen und Speicherort Ihrer VM an.

   ![Assistent für neue VMs: Angeben des Namens und Speicherorts](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-08.png)

4. Wählen Sie unter **Generation angeben** die Option **Generation 1** aus. Wählen Sie anschließend **Weiter >** aus.

   ![Assistent für neue VMs: Auswählen der zu erstellenden VM-Generation](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-09.png)

5. Weisen Sie der VM unter **Arbeitsspeicher zuweisen** den gewünschten Arbeitsspeicher zu. Wählen Sie anschließend **Weiter >** aus.

   ![Assistent für neue VMs: Zuweisen von Arbeitsspeicher](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-10.png)

6. Geben Sie unter **Netzwerk konfigurieren** Ihre Netzwerkkonfiguration ein. Wählen Sie anschließend **Weiter >** aus.

   ![Assistent für neue VMs: Konfigurieren des Netzwerks](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-11.png)

7. Wählen Sie auf der Seite **Virtuelle Festplatte verbinden** die Option **Vorhandene virtuelle Festplatte verwenden** aus, und navigieren Sie zu der VHD mit fester Größe, die Sie im vorherigen Verfahren erstellt haben. Wählen Sie anschließend **Weiter >** aus. 

   ![Assistent für neue VMs: Auswählen einer vorhandenen VHD als Quelle](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-12.png)

8. Überprüfen Sie die Zusammenfassung, und wählen Sie **Fertig stellen** aus, um die VM zu erstellen. 

#### <a name="mount-iso-image-on-dvd-drive-of-vm"></a>Einbinden des ISO-Images auf dem DVD-Laufwerk der VM

Führen Sie nach dem Erstellen der neuen VM die folgenden Schritte aus, um Ihr ISO-Image auf dem DVD-Laufwerk der VM einzubinden:

1. Wählen Sie in Hyper-V-Manager die von Ihnen erstellte VM und dann **Einstellungen** aus.
 
   ![Hyper-V-Manager: Öffnen der Einstellungen für Ihre VM](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-13.png)

2. Vergewissern Sie sich, dass unter **BIOS** die Option **CD** ganz oben in der Liste für die **Startreihenfolge** steht.

   ![„CD“ als erster Eintrag für die Startreihenfolge in den BIOS-Einstellungen](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-14.png)


3. Wählen Sie unter **DVD-Laufwerk** die Option **Imagedatei** aus, und navigieren Sie zu Ihrem ISO-Image.  

   ![Auswählen der Imagedatei für Ihre VHD in den Einstellungen des DVD-Laufwerks](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-15.png)

4. Wählen Sie **OK** aus, um Ihre VM-Einstellungen zu speichern.

## <a name="start-vm-and-complete-os-installation"></a>Starten der VM und Abschließen der Betriebssysteminstallation

Um die Erstellung der VM abzuschließen, müssen Sie die VM starten und die Schritte zum Installieren des Betriebssystems durchführen.

[!INCLUDE [Connect to Hyper-V VM](../../includes/azure-stack-edge-connect-to-hyperv-vm.md)]

> [!NOTE]
> Wenn Sie das Betriebssystem Windows Server 2019 Standard auf Ihrer VM installiert haben, müssen Sie die Einstellung **BIOS** in **IDE** ändern, bevor Sie die [VHD generalisieren](#generalize-the-vhd). 

## <a name="generalize-the-vhd"></a>Generalisieren der VHD

Verwenden Sie das Hilfsprogramm *sysprep*, um die VHD zu generalisieren.

1. Wenn Sie eine Windows Server 2019 Standard-VM generalisieren, müssen Sie vor dem Generalisieren der VHD „IDE“ als erste **BIOS**-Einstellung für die VM festlegen.

    1. Wählen Sie in Hyper-V-Manager die VM und dann **Einstellungen** aus.
 
       ![Screenshot zum Öffnen der Einstellungen für eine ausgewählte VM in Hyper-V-Manager](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-01.png)

     1. Vergewissern Sie sich, dass unter **BIOS** die Option **IDE** ganz oben in der Liste für die **Startreihenfolge** steht. Wählen Sie dann **OK** aus, um die Einstellung zu speichern.

        ![Screenshot der Option „IDE“ am Anfang der Startreihenfolge in den BIOS-Einstellungen für eine VM in Hyper-V-Manager](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-02.png)

1. Öffnen Sie in der VM eine Eingabeaufforderung.

1. Führen Sie den folgenden Befehl aus, um die VM zu generalisieren. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    
    Weitere Informationen finden Sie unter [Übersicht über Sysprep (Systemvorbereitung)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

1.  Nachdem der Befehl ausgeführt wurde, wird die VM heruntergefahren. **Starten Sie den virtuellen Computer nicht neu**.

<!--[!INCLUDE [Generalize the VHD](../../includes/azure-stack-edge-generalize-vhd.md)]-->

Die VHD kann nun dazu verwendet werden, ein generalisiertes Image zur Verwendung auf dem Azure Stack Edge Pro-GPU-Gerät zu erstellen.

## <a name="upload-generalized-vhd-to-azure-blob-storage"></a>Hochladen der generalisierten VHD in Azure Blob Storage

[!INCLUDE [Upload VHD to Blob storage](../../includes/azure-stack-edge-upload-vhd-to-blob-storage.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen einer VM über ein generalisiertes Image über das Azure-Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Vorbereiten eines generalisierten Images aus einer Windows-VHD zum Bereitstellen von VMs auf Azure Stack Edge Pro-GPU-Geräten](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [Vorbereiten eines spezialisierten Images und Bereitstellen von VMs anhand des Images](azure-stack-edge-gpu-deploy-vm-specialized-image-powershell.md) 
