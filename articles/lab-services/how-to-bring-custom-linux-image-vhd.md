---
title: 'Azure Lab Services: Hochladen eines benutzerdefinierten Linux-Images aus einer physischen Laborumgebung'
description: Beschreibt, wie ein benutzerdefiniertes Linux-Image aus einer physischen Laborumgebung importiert wird.
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: 9a8591d383ac5230085bc83d1d791e9de830a99e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771363"
---
# <a name="bring-a-linux-custom-image-from-your-physical-lab-environment"></a>Importieren eines benutzerdefinierten Linux-Images aus einer physischen Laborumgebung

Die Schritte in diesem Absatz zeigen, wie Sie ein benutzerdefiniertes Linux-Image importieren, das aus Ihrer physischen Laborumgebung gestartet wird. Bei diesem Ansatz erstellen Sie eine VHD aus Ihrer physischen Umgebung und importieren die VHD in eine Shared Image Gallery, damit sie in Azure Lab Services verwendet werden kann. Bevor Sie diesen Ansatz zum Erstellen eines benutzerdefinierten Images verwenden, lesen Sie [Empfohlene Ansätze zum Erstellen benutzerdefinierter Images](approaches-for-custom-image-creation.md), um den für Ihr Szenario am besten geeigneten Ansatz zu ermitteln.

Azure unterstützt eine Vielzahl von [Distributionen und Versionen](../virtual-machines/linux/endorsed-distros.md#supported-distributions-and-versions). Die Schritte zum Verwenden eines benutzerdefinierten Linux-Images aus einer VHD variieren je nach Distribution. Jede Distribution ist anders, da jede über eindeutige Voraussetzungen verfügt, die für die Ausführung in Azure eingerichtet werden müssen.

In diesem Artikel werden die Schritte zum Importieren eines benutzerdefinierten Ubuntu-Images für die Distributionen 16.04/18.04/20.04 aus einer VHD erläutert. Informationen zur Verwendung einer VHD zum Erstellen benutzerdefinierter Images für andere Distributionen finden Sie unter [Generische Schritte für Linux-Distributionen](../virtual-machines/linux/create-upload-generic.md).

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Berechtigungen für das Erstellen eines [verwalteten Azure-Datenträgers](../virtual-machines/managed-disks-overview.md) im Azure-Abonnement Ihrer Bildungseinrichtung, um die in diesem Artikel beschriebenen Schritte ausführen zu können.

Wenn Sie Images aus einer physischen Laborumgebung in Lab Services verschieben, sollten Sie jedes Image so umstrukturieren, dass es nur Software enthält, die für das Lab erforderlich ist. Weitere Informationen finden Sie im Blogbeitrag [Moving from a Physical Lab to Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) (Der Weg von einem physischen Lab zu Azure Lab Services).

## <a name="prepare-a-custom-image-by-using-hyper-v-manager"></a>Vorbereiten eines benutzerdefinierten Images mit Hyper-V-Manager

Die folgenden Schritte zeigen, wie Sie ein Ubuntu-Image für die Distributionen 16.04/18.04/20.04 aus einer Hyper-V-VM mithilfe des Windows Hyper-V-Managers erstellen.

1. Laden Sie das offizielle Image für [Linux Ubuntu Server](https://ubuntu.com/server/docs) auf Ihren Windows-Hostcomputer herunter, den Sie zum Einrichten des benutzerdefinierten Images auf einer Hyper-V-VM verwenden.

   Es wird empfohlen, ein Ubuntu-Image zu verwenden, auf dem der [GNOME](https://www.gnome.org/)-GUI-Desktop *nicht* installiert ist. GNOME weist derzeit einen Konflikt mit dem Azure Linux-Agent auf, der erforderlich ist, damit das Image in Lab Services ordnungsgemäß funktioniert. Verwenden Sie beispielsweise das Ubuntu Server-Image, und installieren Sie einen anderen GUI-Desktop wie XFCE oder MATE.

   Ubuntu veröffentlicht auch vordefinierte [Azure-VHDs für den Download](https://cloud-images.ubuntu.com/). Diese VHDs sind für die Erstellung benutzerdefinierter Images von einem Linux-Hostcomputer und Hypervisor (etwa KVM) vorgesehen. Diese VHDs erfordern, dass Sie zuerst das Standardbenutzerkennwort festlegen. Dies kann nur mit Linux-Tools wie QEMU durchgeführt werden, die für Windows nicht verfügbar sind. Wenn Sie ein benutzerdefiniertes Image mit Windows Hyper-V erstellen, können Sie daher keine Verbindung mit diesen VHDs herstellen, um Imageanpassungen vorzunehmen. Weitere Informationen zu den vordefinierten Azure-VHDs finden Sie in der [Dokumentation zu Ubuntu](https://help.ubuntu.com/community/UEC/Images?_ga=2.114783623.1858181609.1624392241-1226151842.1623682781#QEMU_invocation).

1. Verwenden Sie als Ausgangspunkt eine Hyper-V-VM in Ihrer physischen Laborumgebung, die aus Ihrem Image erstellt wurde. Weitere Informationen finden Sie im Artikel zum [Erstellen einer VM in Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v). Legen Sie die Einstellungen wie hier gezeigt fest:
    - Die VM muss als VM der **Generation 1** erstellt werden.
    - Verwenden Sie die Netzwerkkonfigurationsoption **Standardswitch**, damit die VM eine Verbindung mit dem Internet herstellen kann.
    - In den Einstellungen unter **Virtuelle Festplatte verbinden** darf die **Größe** des Datenträgers *nicht* größer als 128 GB sein, wie in der folgenden Abbildung gezeigt.

        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="Screenshot des Bildschirms „Virtuelle Festplatte verbinden“":::

    - Wählen Sie in den Einstellungen der **Installationsoptionen** die **ISO**-Datei aus, die Sie zuvor aus Ubuntu heruntergeladen haben.

    Images mit einer Datenträgergröße über 128 GB werden von Lab Services *nicht* unterstützt.

1. Stellen Sie eine Verbindung mit der Hyper-V-VM her, und bereiten Sie sie anhand der Schritte unter [Manuelle Schritte zum Erstellen und Hochladen einer Ubuntu-VHD](../virtual-machines/linux/create-upload-ubuntu.md#manual-steps) für Azure vor.

    Die Schritte zum Vorbereiten eines Linux-Images für Azure unterscheiden sich je nach Distribution. Weitere Informationen und spezifische Schritte für einzelne Distributionen finden Sie unter [Distributionen und Versionen](../virtual-machines/linux/endorsed-distros.md#supported-distributions-and-versions).

    Wenn Sie die oben genannten Schritte ausführen, müssen Sie einige wichtige Punkte beachten:
    - Mit diesen Schritten wird ein [generalisiertes](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) Image erstellt, wenn Sie den Befehl **deprovision+user** ausführen. Dies garantiert jedoch nicht, dass alle vertraulichen Informationen aus dem Image gelöscht werden oder dass es für eine erneute Verteilung genutzt werden kann.
    - Der letzte Schritt besteht darin, die **VHDX**-Datei in eine **VHD**-Datei zu konvertieren. Im Folgenden finden Sie entsprechende Schritte, die zeigen, wie dies mit dem **Hyper-V-Manager** ausgeführt wird:

        1. Navigieren Sie zu **Hyper-V-Manager** > **Aktion** > **Datenträger bearbeiten**.
        1. **Konvertieren** Sie nun den Datenträger von einer VHDX in eine VHD.
        1. Wählen Sie für **Datenträgertyp** die Option **Feste Größe** aus.
            - Wenn Sie die Datenträgergröße an diesem Punkt erweitern, stellen Sie sicher, dass Sie 128 GB *nicht* überschreiten.
            :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="Screenshot des Bildschirms „Aktion auswählen“":::

Um die Größe der VHD zu ändern und die Konvertierung in eine VHDX auszuführen, können Sie auch die folgenden PowerShell-Cmdlets verwenden:

- [Resize-VHD](/powershell/module/hyper-v/resize-vhd)
- [Convert-VHD](/powershell/module/hyper-v/convert-vhd)

## <a name="upload-the-custom-image-to-a-shared-image-gallery"></a>Hochladen des benutzerdefinierten Images in eine Shared Image Gallery

1. Laden Sie die VHD-Datei in Azure hoch, um einen verwalteten Datenträger zu erstellen.
    1. Sie können entweder Storage-Explorer oder AzCopy in der Befehlszeile verwenden, wie unter [Hochladen einer VHD in Azure oder Kopieren eines verwalteten Datenträgers in eine andere Region](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md) beschrieben.

    1. Nachdem Sie die VHD hochgeladen haben, sollten Sie nun über einen verwalteten Datenträger verfügen, den Sie im Azure-Portal anzeigen können.

    Wenn Ihr Computer in den Energiesparmodus wechselt oder gesperrt wird, wird der Uploadvorgang möglicherweise unterbrochen, und es tritt ein Fehler auf. Stellen Sie außerdem sicher, dass Sie nach Abschluss von AzCopy den SAS-Zugriff auf den Datenträger widerrufen. Andernfalls wird beim Versuch, ein Image aus dem Datenträger zu erstellen, dieser Fehler angezeigt: „Der Vorgang ‚Image erstellen‘ wird für den Datenträger ‚Ihr Datenträgername‘ im Status ‚Aktiver Upload‘ nicht unterstützt. Fehlercode: OperationNotAllowed*“.

    Auf der Registerkarte **Größe und Leistung** des verwalteten Datenträgers im Azure-Portal können Sie die Datenträgergröße ändern. Wie bereits erwähnt, darf die Größe *nicht* größer als 128 GB sein.

1. Erstellen Sie in einer Shared Image Gallery eine Imagedefinition und -version:
    1. [Erstellen Sie eine Imagedefinition:](../virtual-machines/image-version.md)
        - Wählen Sie **Gen 1** als **VM-Generation** aus.
        - Wählen Sie unter **Betriebssystem** die Option **Linux** aus.
        - Wählen Sie unter **Betriebssystemstatus** die Option **Generalisiert** aus.

    Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](../virtual-machines/shared-image-galleries.md#image-definitions).

    Sie können auch eine vorhandene Imagedefinition verwenden und eine neue Version für Ihr benutzerdefiniertes Image erstellen.

1. [Erstellen Sie eine Imageversion:](../virtual-machines/image-version.md)
    - Die Eigenschaft **Versionsnummer** verwendet das folgende Format: *MajorVersion.MinorVersion.Patch*. Wenn Sie Lab Services verwenden, um ein Lab zu erstellen und ein benutzerdefiniertes Image auswählen, wird automatisch die neueste Version des Images verwendet. Die aktuellste Version wird basierend auf dem höchsten Wert von MajorVersion, MinorVersion und Patch ausgewählt.
    - Wählen Sie als **Quelle** in der Dropdownliste die Option **Datenträger und/oder Momentaufnahmen** aus.
    - Wählen Sie für die Eigenschaft **Betriebssystemdatenträger** den Datenträger aus, den Sie in den vorherigen Schritten erstellt haben.

    Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imageversionen](../virtual-machines/shared-image-galleries.md#image-versions).

## <a name="create-a-lab"></a>Erstellen eines Labs

[Erstellen Sie das Lab](tutorial-setup-classroom-lab.md) in Azure Lab Services, und wählen Sie das benutzerdefinierte Image in Shared Image Gallery aus.

Wenn Sie den Datenträger *nach* der Installation des Betriebssystems auf der ursprünglichen Hyper-V-VM erweitert haben, müssen Sie ggf. auch die Partition im Dateisystem von Linux so erweitern, dass der nicht zugeordnete Datenträgerspeicherplatz genutzt wird.  Melden Sie sich bei der Vorlagen-VM des Labs an, und führen Sie ähnliche Schritte wie die unter [Erweitern einer Datenträgerpartition und eines Dateisystems](../virtual-machines/linux/expand-disks.md#expand-a-disk-partition-and-filesystem) beschriebenen aus.

Der Betriebssystemdatenträger befindet sich in der Regel auf der Partition **/dev/sad2**. Verwenden Sie den Befehl **df -h**, um die aktuelle Größe der Partition des Betriebssystemdatenträgers anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Shared Image Gallery](../virtual-machines/shared-image-galleries.md)
- [Anfügen oder Trennen einer Shared Image Gallery](how-to-attach-detach-shared-image-gallery.md)
- [Verwenden eines Katalogs mit freigegebenen Images](how-to-use-shared-image-gallery.md)