---
title: Vorbereiten eines generalisierten Images aus einer Windows-VHD zum Bereitstellen von VMs auf Azure Stack Edge Pro-GPU-Geräten
description: Erfahren Sie, wie ein generalisiertes VM-Image über eine Windows-VHD oder -VHDX erstellt wird. Verwenden Sie dieses generalisierte VM-Image, um virtuelle Computer auf Ihrem Azure Stack Edge Pro GPU-Gerät bereitzustellen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/18/2021
ms.author: alkohli
ms.openlocfilehash: e60f7e7195ef81b3bf17a85c5662824df945ef91
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968399"
---
# <a name="prepare-generalized-image-from-windows-vhd-to-deploy-vms-on-azure-stack-edge-pro-gpu"></a>Vorbereiten eines generalisierten Images aus einer Windows-VHD zum Bereitstellen von VMs auf Azure Stack Edge Pro-GPU-Geräten

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Wenn Sie virtuelle Computer auf Ihrem Azure Stack Edge Pro-GPU-Gerät bereitstellen möchten, müssen Sie in der Lage sein, benutzerdefinierte VM-Images zu erstellen, mit denen Sie wiederum virtuelle Computer erstellen können. In diesem Artikel wird beschrieben, wie Sie ein generalisiertes Image aus einer Windows-VHD oder -VHDX vorbereiten, mit dem Sie virtuelle Computer auf Windows Stack Edge Pro-GPU-Geräten bereitstellen können.

Informationen zum Vorbereiten eines generalisierten VM-Images mithilfe einer ISO-Datei finden Sie unter [Vorbereiten eines generalisierten Images aus einer ISO-Datei zum Bereitstellen von VMs auf Azure Stack Edge Pro-GPU-Geräten](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md).

## <a name="about-vm-images"></a>Informationen zu VM-Images

Sie können eine Windows-VHD oder -VHDX verwenden, um ein *spezialisiertes* Image oder ein *generalisiertes* Image zu erstellen. In der folgenden Tabelle sind die wichtigsten Unterschiede zwischen *spezialisierten* und *generalisierten* Images zusammengefasst:

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>Workflow

Der allgemeine Workflow zum Vorbereiten einer Windows-VHD für die Verwendung als generalisiertes Image, beginnend mit der VHD oder VHDX eines vorhandenen virtuellen Computers, weist die folgenden Schritte auf:

1. Bereiten Sie den virtuellen Quellcomputer über eine Windows-VHD vor:
   1. Konvertieren Sie die Quell-VHD oder -VHDX in eine VHD mit fester Größe.
   1. Verwenden Sie diese VHD zum Erstellen eines neuen virtuellen Computers.<!--Can this procedure be generalized and moved to an include file?-->
1. Starten Sie den virtuellen Computer, und installieren Sie das Windows-Betriebssystem.
1. Generalisieren der VHD mit dem Hilfsprogramm *sysprep*
1. Kopieren des generalisierten Images in Blob Storage

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor der Vorbereitung einer Windows-VHD zur Verwendung als generalisiertes Image auf einem Azure Stack Edge Pro-GPU-Gerät Folgendes sicher:

- Die verwendete VHD oder VHDX enthält eine unterstützte Version von Windows. 
- Sie haben Zugriff auf einen Windows-Client mit installiertem Hyper-V-Manager. 
- Sie haben Zugriff auf ein Azure Blob Storage-Konto, um die VHD nach der Vorbereitung zu speichern.

## <a name="prepare-source-vm-from-windows-vhd"></a>Vorbereiten des virtuellen Quellcomputers über die Windows-VHD

Wenn ihre VM-Quelle eine Windows-VHD oder -VHDX ist, müssen Sie zuerst die Windows-VHD in eine VHD mit fester Größe konvertieren. Sie verwenden die VHD mit fester Größe, um einen neuen virtuellen Computer zu erstellen.

> [!IMPORTANT]
> Diese Schritte beziehen sich nicht auf Fälle, bei denen die Quell-VHD mit benutzerdefinierten Konfigurationen und Einstellungen konfiguriert ist. Beispielsweise können zusätzliche Aktionen erforderlich sein, um eine VHD zu generalisieren, die benutzerdefinierte Firewallregeln oder Proxyeinstellungen beinhaltet. Weitere Informationen zu diesen zusätzlichen Aktionen finden Sie unter [Vorbereiten einer Windows-VHD für das Hochladen in Azure – Azure Virtual Machines](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

#### <a name="convert-source-vhd-to-a-fixed-size-vhd"></a>Konvertieren der Quell-VHD in eine VHD mit fester Größe

Für Ihr Gerät benötigen Sie zum Erstellen von VM-Images VHDs mit fester Größe. Sie müssen die Windows-Quell-VHD oder -VHDX in eine VHD mit fester Größe konvertieren. 

Führen Sie die folgenden Schritte aus:

1. Öffnen Sie Hyper-V-Manager auf dem Clientsystem. Navigieren Sie zu **Datenträger bearbeiten**.

    ![Öffnen von Hyper-V-Manager](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-1.png)

1. Wählen Sie auf der Seite **Vorbereitung** die Option **Weiter >** aus.

1. Wechseln Sie auf der Seite **Virtuelle Festplatte suchen** zum Speicherort der zu konvertierenden Windows-Quell-VHD oder -VHDX. Wählen Sie **Weiter >** aus.

    ![Seite „Virtuelle Festplatte suchen“](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-2.png)

1. Wählen Sie auf der Seite **Aktion auswählen** die Option **Konvertieren** und dann **Weiter >** aus.

    ![Seite „Aktion auswählen“](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-3.png)

1. Wählen Sie auf der Seite **Datenträgerformat auswählen** das Format **VHD** und dann **Weiter >** aus.

   ![Seite „Datenträgerformat auswählen“](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-4.png)

1. Wählen Sie auf der Seite **Datenträgertyp auswählen** die Option **Feste Größe** und dann **Weiter >** aus.

   ![Seite „Datenträgertyp auswählen“](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-5.png)

1. Navigieren Sie auf der Seite **Datenträger konfigurieren** zum Speicherort, und geben Sie einen Namen für den VHD-Datenträger mit fester Größe an. Wählen Sie **Weiter >** aus.

   ![Seite „Datenträger konfigurieren“](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-6.png)

1. Überprüfen Sie die Zusammenfassung, und wählen Sie **Fertig stellen** aus. Die Konvertierung der VHD oder VHDX dauert einige Minuten. Die Konvertierungszeit hängt von der Größe des Quelldatenträgers ab.

<!--
1. Run PowerShell on your Windows client.
1. Run the following command:

    ```powershell
    Convert-VHD -Path <source VHD path> -DestinationPath <destination-path.vhd> -VHDType Fixed 
    ```
-->
Diese VHD mit fester Größe wird für alle nachfolgenden Schritte in diesem Artikel verwendet.

#### <a name="create-hyper-v-vm-from-the-fixed-size-vhd"></a>Erstellen eines virtuellen Hyper-V-Computers aus der VHD mit fester Größe

1. Klicken Sie im **Hyper-V-Manager** im Bereichsfenster mit der rechten Maustaste auf den Systemknoten, um das Kontextmenü zu öffnen. Wählen Sie dann **Neu** > **Virtueller Computer** aus.

    ![Auswählen einer neuen VM im Bereichsfenster](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-1.png)

1. Wählen Sie auf der Seite **Vorbereitung** des Assistenten für neue VMs die Option **Weiter** aus.

1. Geben Sie auf der Seite **Namen und Speicherort angeben** einen **Namen** und einen **Speicherort** für die VM an. Klicken Sie auf **Weiter**.

    ![Angeben des Namens und Speicherorts für die VM](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-2.png)

1. Wählen Sie auf der Seite **Generation angeben** die Option **Generation 1** für den VHD-Imagetyp des Geräts und dann **Weiter** aus.    

    ![Angeben der Generation](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-3.png)

1. Weisen Sie die gewünschten Speicher- und Netzwerkkonfigurationen zu.

1. Wählen Sie auf der Seite **Virtuelle Festplatte verbinden** die Option **Vorhandene virtuelle Festplatte verwenden** aus. Geben Sie den Speicherort der zuvor erstellten Windows-VHD mit fester Größe an, und wählen Sie dann **Weiter** aus.

    ![Seite „Virtuelle Festplatte verbinden“](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-4.png)

1. Überprüfen Sie die **Zusammenfassung**, und wählen Sie dann **Fertig stellen** aus, um die VM zu erstellen.

Die Erstellung der VM dauert einige Minuten.

Die VM wird in der Liste der VMs in Ihrem Clientsystem angezeigt.

## <a name="start-vm-and-install-operating-system"></a>Starten des virtuellen Computers und Installieren des Betriebssystems

Um die Erstellung des virtuellen Computers abzuschließen, müssen Sie den virtuellen Computer starten und die Installation des Betriebssystems durchlaufen.

[!INCLUDE [Connect to Hyper-V VM](../../includes/azure-stack-edge-connect-to-hyperv-vm.md)]

Nachdem die Verbindung mit der VM hergestellt wurde, schließen Sie den Assistenten zum Einrichten des Computers ab, und melden Sie sich bei der VM an.<!--It's not clear what they are doing here. Where does the Machine setup wizard come in?-->

## <a name="generalize-the-vhd"></a>Generalisieren der VHD

Verwenden Sie das Hilfsprogramm *sysprep*, um die VHD zu generalisieren. 

[!INCLUDE [Generalize the VHD](../../includes/azure-stack-edge-generalize-vhd.md)]

Die VHD kann nun dazu verwendet werden, ein generalisiertes Image zur Verwendung auf dem Azure Stack Edge Pro-GPU-Gerät zu erstellen.

## <a name="upload-generalized-vhd-to-azure-blob-storage"></a>Hochladen der generalisierten VHD in Azure Blob Storage

[!INCLUDE [Upload VHD to Blob storage](../../includes/azure-stack-edge-upload-vhd-to-blob-storage.md)]

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->

## <a name="next-steps"></a>Nächste Schritte

Abhängig von der Art der Bereitstellung können Sie eine der folgenden Vorgehensweisen wählen.

- [Bereitstellen einer VM über ein generalisiertes Image über das Azure-Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Vorbereiten eines generalisierten Images aus einer ISO-Datei zum Bereitstellen von VMs auf Azure Stack Edge Pro-GPU-Geräten](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)
- [Bereitstellen einer VM auf der Grundlage eines spezialisierten Images auf Ihrem Azure Stack Edge Pro-GPU-Gerät über Azure PowerShell](azure-stack-edge-gpu-deploy-vm-specialized-image-powershell.md) 
