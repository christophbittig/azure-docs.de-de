---
title: Hochladen eines benutzerdefinierten Windows-Images aus einer physischen Laborumgebung
description: Beschreibt,wie ein benutzerdefiniertes Windows-Image aus einer physischen Laborumgebung hochgeladen wird.
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: 36a6c8f920ebada3d5cd4110e48fe8544e4c7ae3
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179011"
---
# <a name="bring-a-windows-custom-image-from-a-physical-lab-environment"></a>Hochladen eines benutzerdefinierten Images aus einer physischen Laborumgebung

Die Schritte in diesem Absatz zeigen, wie Sie ein benutzerdefiniertes Image importieren, das aus Ihrer physischen Laborumgebung gestartet wird.  Bei diesem Ansatz erstellen Sie eine VHD aus Ihrer physischen Umgebung und importieren die VHD in eine Shared Image Gallery, damit sie in Lab Services verwendet werden kann.  Bevor Sie diesen Ansatz zum Erstellen eines benutzerdefinierten Images verwenden, lesen Sie den Artikel [Empfohlene Ansätze zum Erstellen benutzerdefinierter Images](approaches-for-custom-image-creation.md), um den besten Ansatz für Ihr Szenario zu ermitteln.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Berechtigungen zum Erstellen eines [verwalteten Azure-Datenträgers](../virtual-machines/managed-disks-overview.md) im Azure-Abonnement Ihrer Bildungseinrichtung, um die in diesem Artikel beschriebenen Schritte ausführen zu können.

Wenn Sie Images aus einer physischen Laborumgebung in Lab Services verschieben, sollten Sie jedes Image so umstrukturieren, dass es nur Software enthält, die für den Unterricht eines Labs erforderlich ist.  Weitere Informationen finden Sie im Blogbeitrag [Moving from a Physical Lab to Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) (Der Weg von einem physischen Lab zu Azure Lab Services).

## <a name="prepare-a-custom-image-using-hyper-v-manager"></a>Vorbereiten eines benutzerdefinierten Images mit Hyper-V-Manager

Die folgenden Schritte zeigen, wie Sie ein Windows-Image aus einem virtuellen Windows Hyper-V-Computer (VM) mithilfe des Hyper-V-Managers erstellen:

1. Beginnen Sie mit einer Hyper-V-VM in ihrer physischen Laborumgebung, die aus dem Image erstellt wurde.  Weitere Informationen finden Sie im Artikel zum [Erstellen eines virtuellen Computers in Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v).
    - Die VM muss als VM der **Generation 1** erstellt werden.
    - Verwenden Sie die Netzwerkkonfigurationsoption **Standardswitch**, damit die VM eine Verbindung mit dem Internet herstellen kann.
    - Der virtuelle Datenträger der VM muss eine VHD fester Größe sein.  Die Datenträgergröße darf *nicht* über 128 GB liegen. Geben Sie beim Erstellen der VM die Größe des Datenträgers ein, wie in der folgenden Abbildung dargestellt.

        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="Virtuelle Festplatte verbinden":::

    Images mit einer Datenträgergröße über 128 GB werden von Azure Lab Services *nicht* unterstützt.

1. Führen Sie die folgenden Schritte aus, um eine Verbindung mit der Hyper-V-VM herzustellen und sie [für Azure vorzubereiten](../virtual-machines/windows/prepare-for-upload-vhd-image.md):
    1. [Legen Sie die Windows-Konfigurationen für Azure fest](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure).
    1. [Überprüfen Sie die Windows-Dienste, die zum Sicherstellen der VM-Konnektivität erforderlich sind](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services).
    1. [Aktualisieren Sie die Remotedesktop-Registrierungseinstellungen](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
    1. [Konfigurieren Sie die Windows-Firewallregeln](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules).
    1. [Installieren Sie Windows-Updates](../virtual-machines/windows/prepare-for-upload-vhd-image.md).
    1. [Abschließen empfohlener Konfigurationen](../virtual-machines/windows/prepare-for-upload-vhd-image.md#complete-the-recommended-configurations)

    Sie können spezialisierte oder generalisierte Images in eine Shared Image Gallery hochladen und diese zum Erstellen von Labs verwenden.  Mit den oben beschriebenen Schritten wird ein spezialisiertes Image erstellt. Wenn Sie stattdessen ein generalisiertes Image erstellen müssen, müssen Sie auch [SysPrep ausführen](../virtual-machines/windows/prepare-for-upload-vhd-image.md#determine-when-to-use-sysprep).  

    Sie sollten ein spezialisiertes Image erstellen, wenn Sie computerspezifische Informationen und Benutzerprofile verwalten möchten.  Weitere Informationen zu den Unterschieden zwischen generalisierten und spezialisierten Images finden Sie unter [Generalisierte und spezialisierte Images](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).

1. Da **Hyper-V** standardmäßig eine **VHDX**-Datei erstellt, müssen Sie diese in eine VHD-Datei konvertieren.
    1. Navigieren Sie zu **Hyper-V-Manager** -> **Aktion** -> **Datenträger bearbeiten**.
    1. **Konvertieren** Sie nun den Datenträger von einer VHDX in eine VHD.  
     - Wenn Sie die Datenträgergröße erweitern, stellen Sie sicher, dass Sie 128 GB *nicht* überschreiten.
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="Aktion auswählen":::

    Weitere Informationen finden Sie im Artikel zum [Konvertieren des virtuellen Datenträgers in eine VHD mit fester Größe](../virtual-machines/windows/prepare-for-upload-vhd-image.md#convert-the-virtual-disk-to-a-fixed-size-vhd).

Um die Größe der VHD zu ändern und die Konvertierung in eine VHDX auszuführen, können Sie auch die folgenden PowerShell-Cmdlets verwenden:

- [Resize-VHD](/powershell/module/hyper-v/resize-vhd)
- [Convert-VHD](/powershell/module/hyper-v/convert-vhd)

## <a name="upload-the-custom-image-to-a-shared-image-gallery"></a>Hochladen des benutzerdefinierten Images in eine Shared Image Gallery

1. Laden Sie die VHD-Datei in Azure hoch, um einen verwalteten Datenträger zu erstellen.
    1. Sie können entweder Storage-Explorer oder AzCopy in der Befehlszeile verwenden, wie unter [Hochladen einer VHD in Azure oder Kopieren eines verwalteten Datenträgers in eine andere Region](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md) beschrieben.

    1. Nachdem Sie die VHD hochgeladen haben, sollten Sie nun über einen verwalteten Datenträger verfügen, den Sie im Azure-Portal anzeigen können.
    Wenn Ihr Computer in den Standbymodus wechselt oder gesperrt wird, wird der Uploadvorgang möglicherweise unterbrochen und ein Fehler tritt auf.  Stellen Sie außerdem sicher, dass Sie nach Abschluss von AzCopy den SAS-Zugriff auf den Datenträger widerrufen.  Andernfalls wird beim Versuch, ein Image auf dem Datenträger zu erstellen, ein Fehler angezeigt: **Der Vorgang "Image erstellen" wird für den Datenträger "Ihr Datenträgername" im Status "Aktiver Upload" nicht unterstützt. Fehlercode OperationNotAllowed**

    Auf Registerkarte **Größe und Leistung** des verwalteten Datenträgers im Azure-Portal können Sie die Datenträgergröße ändern. Wie bereits erwähnt, darf die Größe *nicht* größer als 128 GB sein.

1. Erstellen Sie in einer Shared Image Gallery eine Imagedefinition und -version:
    1. [Erstellen Sie eine Imagedefinition](../virtual-machines/image-version.md).  
     - Wählen Sie **Gen 1** als **VM-Generation** aus.
     - Wählen Sie aus, ob Sie ein **spezialisiertes** oder **generalisiertes** Image für den **Betriebssystemstatus** erstellen.

    Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](../virtual-machines/shared-image-galleries.md#image-definitions).

    Sie können auch eine vorhandene Imagedefinition verwenden und eine neue Version für Ihr benutzerdefiniertes Image erstellen.

1. [Erstellen Sie eine Imageversion](../virtual-machines/image-version.md).
    - Die Eigenschaft **Versionsnummer** verwendet das folgende Format: *MajorVersion.MinorVersion.Patch*.   Wenn Sie Lab Services verwenden, um ein Lab zu erstellen und ein benutzerdefiniertes Image auswählen, wird automatisch die neueste Version des Images verwendet.  Die neueste Version wird basierend auf dem höchsten Wert von MajorVersion, MinorVersion und Patch ausgewählt.
    - Wählen Sie als **Quelle** die Option **Datenträger und/oder Momentaufnahmen** aus der Dropdownliste aus.
    - Wählen Sie für die Eigenschaft **Betriebssystemdatenträger** den Datenträger aus, den Sie in den vorherigen Schritten erstellt haben.

    Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imageversionen](../virtual-machines/shared-image-galleries.md#image-versions).

## <a name="create-a-lab"></a>Erstellen eines Labs

1. [Erstellen Sie das Lab](tutorial-setup-classroom-lab.md) in Azure Lab Services, und wählen Sie das benutzerdefinierte Image in Shared Image Gallery aus.

    Wenn Sie den Datenträger *nach* der Installation des Betriebssystems auf der ursprünglichen Hyper-V-VM erweitert haben, müssen Sie ggf. auch das Laufwerk „C:“ in Windows so erweitern, dass der nicht zugeordnete Datenträgerspeicherplatz verwendet wird:
    - Melden Sie sich bei der Vorlagen-VM des Labs an, und führen Sie ähnliche Schritte wie unter [Erweitern eines Basisvolumes](/windows-server/storage/disk-management/extend-a-basic-volume) beschrieben aus.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Shared Image Gallery](../virtual-machines/shared-image-galleries.md)
- [Anfügen oder Trennen eines Katalogs mit freigegebenen Images](how-to-attach-detach-shared-image-gallery.md)
- [Verwenden einer Shared Image Gallery](how-to-use-shared-image-gallery.md)
