---
title: Bereitstellen von VMs auf einem Azure Stack Edge Pro GPU-Gerät über das Azure-Portal
description: Erfahren Sie, wie Sie VMs auf Azure Stack Edge Pro GPU über das Azure-Portal erstellen und verwalten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: 8089666388134e8443c515d86e91ae3ba5c32b58
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349475"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Bereitstellen von VMs auf einem Azure Stack Edge Pro-GPU-Gerät über das Azure-Portal

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Sie können virtuelle Computer (Virtual Machines, VMs) auf einem Azure Stack Edge Pro GPU-Gerät über das Azure-Portal, Vorlagen und Azure PowerShell-Cmdlets sowie über die Azure CLI bzw. Python-Skripts erstellen und verwalten. In diesem Artikel wird beschrieben, wie Sie eine VM auf Ihrem Azure Stack Edge Pro GPU-Gerät über das Azure-Portal erstellen und verwalten.

> [!IMPORTANT] 
> Es wird empfohlen, für Benutzer, die auf Ihrem Gerät bereitgestellte VMs über die Cloud verwalten, die mehrstufige Authentifizierung zu aktivieren.
        
## <a name="vm-deployment-workflow"></a>VM-Bereitstellungsworkflow

Die allgemeine Zusammenfassung des Bereitstellungsworkflows lautet wie folgt:

1. Aktivieren einer Netzwerkschnittstelle für Compute auf dem Azure Stack Edge-Gerät In diesem Schritt wird ein virtueller Switch an der angegebenen Netzwerkschnittstelle erstellt.
1. Aktivieren Sie die Cloudverwaltung von virtuellen Computern über das Azure-Portal.
1. Laden Sie mit dem Azure Storage-Explorer eine VHD in ein Azure Storage-Konto hoch. 
1. Verwenden Sie die hochgeladene VHD, um diese auf das Gerät herunterzuladen und ein VM-Image von der VHD zu erstellen. 
1. Verwenden Sie die Ressourcen, die Sie in den vorherigen Schritten erstellt haben:
    1. VM-Image, das Sie erstellt haben
    1. Virtueller Switch, der der Netzwerkschnittstelle zugeordnet ist, an der Sie Compute aktiviert haben
    1. Subnetz, das dem virtuellen Switch zugeordnet ist

    Und erstellen Sie die folgenden Ressourcen inline, oder geben Sie diese inline an:
    1. VM-Name – wählen Sie eine unterstützte VM-Größe und Anmeldeinformationen für die VM aus. 
    1. Erstellen Sie neue Datenträger, oder fügen Sie vorhandene Datenträger an.
    1. Konfigurieren Sie eine statische oder dynamische IP-Adresse für die VM. Wenn Sie eine statische IP-Adresse bereitstellen, wählen Sie eine freie IP-Adresse im Subnetzbereich der für Compute aktivierten Netzwerkschnittstelle aus.

    Verwenden Sie die oben genannten Ressourcen, um einen virtuellen Computer zu erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit dem Erstellen und Verwalten von VMs auf Ihrem Gerät über das Azure-Portal beginnen, stellen Sie Folgendes sicher:

1. Sie haben die Netzwerkeinstellungen auf Ihrem Azure Stack Edge Pro GPU-Gerät wie unter [Schritt 1: Konfigurieren eines Azure Stack Edge Pro GPU-Geräts](./azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-device) beschrieben festgelegt.

    1. Sie haben eine Netzwerkschnittstelle für Compute aktiviert. Diese IP-Adresse der Netzwerkschnittstelle wird verwendet, um einen virtuellen Switch für die VM-Bereitstellung zu erstellen. Navigieren Sie auf der lokalen Benutzeroberfläche Ihres Geräts zu **Compute**. Wählen Sie die Netzwerkschnittstelle aus, die Sie verwenden möchten, um einen virtuellen Switch zu erstellen.

        > [!IMPORTANT] 
        > Sie können nur einen Port für Compute konfigurieren.

    1. Aktivieren Sie Compute für die Netzwerkschnittstelle. Azure Stack Edge Pro GPU erstellt und verwaltet einen virtuellen Switch, der dieser Netzwerkschnittstelle entspricht.

1. Sie haben Zugriff auf eine Windows- oder Linux-VHD, die Sie verwenden, um das VM-Image für den virtuellen Computer zu erstellen, den Sie erstellen möchten.

## <a name="deploy-a-vm"></a>Bereitstellen einer VM

Führen Sie die folgenden Schritte aus, um auf Ihrem Azure Stack Edge Pro GPU-Gerät eine VM zu erstellen.

### <a name="add-a-vm-image"></a>Hinzufügen eines VM-Images

1. Laden Sie eine VHD in ein Azure Storage-Konto hoch. Folgen Sie den Schritten unter [Verwenden des Storage-Explorer für den Upload](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload).

   Informationen zum Vorbereiten der VHD finden Sie unter [Vorbereiten eines generalisierten Images von einer Windows-VHD](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md).

   [Problembehandlung bei VM-Imageuploads](azure-stack-edge-gpu-troubleshoot-virtual-machine-image-upload.md)

1. Wechseln Sie im Azure-Portal zur Azure Stack Edge-Ressource für Ihr Gerät. Wechseln Sie dann zu **Edgedienste** > **Virtuelle Computer**.

    ![Screenshot: Bereich „Übersicht“ eines Azure Stack Edge-Geräts mit hervorgehobener Option „Virtuelle Computer“ unter „Edgedienste“](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. Wechseln Sie zur **Übersichtsseite**: Wählen Sie **Aktivieren** aus, um die Cloudverwaltung für virtuelle Computer zu aktivieren. 

    ![Screenshot des Bereichs „Übersicht“ in der Ansicht „Virtuelle Computer“ für ein Azure Stack Edge-Gerät. Die Schaltfläche „Aktivieren“ für die Aktivierung der VM-Cloudverwaltung ist hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. Der erste Schritt besteht darin, ein VM-Image hinzuzufügen. Sie haben im vorherigen Schritt bereits eine VHD in das Speicherkonto hochgeladen. Sie verwenden diese VHD, um ein VM-Image zu erstellen.

    Wählen Sie **+ Image hinzufügen** aus, um die VHD aus dem Speicherkonto herunterzuladen und zum Gerät hinzuzufügen. Der Downloadvorgang dauert einige Minuten und ist von der Größe der VHD und der für den Download verfügbaren Internetbandbreite abhängig. 

    ![Screenshot des Bereichs „Übersicht“ für virtuelle Computer mit hervorgehobener Schaltfläche „Image hinzufügen“](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. Geben Sie im Bereich **Image hinzufügen** die folgenden Einträge für Felder ein. Wählen Sie anschließend **Hinzufügen**.

    |Feld  |BESCHREIBUNG  |
    |---------|---------|
    |Download from storage blob (Aus Speicherblob herunterladen)    |Navigieren Sie zum Speicherort des Speicherblobs im Speicherkonto, in das Sie die VHD hochgeladen haben.         |
    |Herunterladen nach    | Wird automatisch auf das aktuelle Gerät festgelegt, auf dem Sie den virtuellen Computer bereitstellen.        |
    |Edgeressourcengruppe  |Wählen Sie die Ressourcengruppe aus, der das Image hinzugefügt werden soll. |
    |Image speichern unter      | Der Name des VM-Images, das Sie von der VHD erstellen, die Sie in das Speicherkonto hochgeladen haben.        |
    |Betriebssystemtyp     |Wählen Sie Windows oder Linux als Betriebssystem der VHD aus, die Sie zum Erstellen des VM-Images verwenden.         |
   

    ![Screenshot: Seite „Image hinzufügen“ für einen virtuellen Computer mit hervorgehobener Schaltfläche „Hinzufügen“](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. Die VHD wird heruntergeladen, und das VM-Image wird erstellt. Die Imageerstellung dauert einige Minuten. Sie werden benachrichtigt, sobald das VM-Image erfolgreich erstellt wurde.<!--There's a fleeting notification that image creation is in progress, but I didn't see any notification that image creation completed successfully.-->

    ![Screenshot der Ansicht „Übersicht“ für virtuelle Computer auf einem Azure Stack Edge-Gerät. Im Bereich „Images“ ist ein neu erstellter virtueller Computer hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. Nachdem das VM-Image erfolgreich erstellt wurde, wird es der Liste der Images im Bereich **Images** hinzugefügt.

    ![Screenshot: Bereich „Images“ in der Ansicht „virtuelle Computer“ eines Azure Stack Edge-Geräts. Der Eintrag für ein VM-Image ist hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    Der Bereich **Bereitstellungen** wird aktualisiert, um den Status der Bereitstellung widerzuspiegeln.

    ![Screenshot: Bereich „Bereitstellungen“ in der Ansicht „virtuelle Computer“ eines Azure Stack Edge-Geräts. Der Eintrag für einen bereitgestellten virtuellen Computer ist hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    Das neu hinzugefügte Image wird auch auf der Seite **Übersicht** angezeigt.

    ![Screenshot: Bereich „Übersicht“ in der Ansicht „virtuelle Computer“ eines Azure Stack Edge-Geräts. Ein neu hinzugefügtes VM-Image ist hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>Hinzufügen eines virtuellen Computers

Führen Sie diese Schritte aus, um eine VM zu erstellen, nachdem Sie ein VM-Image erstellt haben.

1. Wählen Sie auf der Seite **Übersicht** für **Virtuelle Computer** die Option **Virtuellen Computer hinzufügen** aus.

    ![Screenshot der Ansicht „Übersicht“ für virtuelle Computer auf einem Azure Stack Edge-Gerät. Die Schaltfläche „Virtuellen Computer hinzufügen“ ist hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. Geben Sie auf der Registerkarte **Grundlagen** die folgenden Parameter ein.

    |Parameter |BESCHREIBUNG  |
    |---------|---------|
    |Name des virtuellen Computers     | Geben Sie einen Namen für den neuen virtuellen Computer ein.        |
    |Edgeressourcengruppe     | Erstellen Sie eine neue Ressourcengruppe für alle Ressourcen, die der VM zugeordnet sind.        |
    |Image     | Wählen Sie aus den VM-Images aus, die auf dem Gerät verfügbar sind.        |
    |Size     | Wählen Sie aus den [Unterstützten VM-Größen](azure-stack-edge-gpu-virtual-machine-sizes.md) aus.<br>Wählen Sie für eine GPU-VM eine [VM-Größe aus der NCasT4-v3-Serie](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview) aus. |
    |Username     | Verwenden Sie den Standardbenutzernamen **azureuser** für den Administrator, um sich bei der VM anzumelden.        |
    |Authentifizierungsart    | Wählen Sie einen öffentlichen SSH-Schlüssel oder ein benutzerdefiniertes Kennwort aus.       |
    |Öffentlicher SSH-Schlüssel | Dieser wird angezeigt, wenn Sie den Authentifizierungstyp **Öffentlicher SSH-Schlüssel** auswählen. Fügen Sie den öffentlichen SSH-Schlüssel ein. |
    |Kennwort     | Dieses wird angezeigt, wenn Sie den Authentifizierungstyp **Kennwort** auswählen. Geben Sie ein Kennwort zur Anmeldung bei der VM ein. Das Kennwort muss mindestens zwölf Zeichen lang sein und die definierten [Komplexitätsanforderungen](../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-) erfüllen. |
    |Kennwort bestätigen    | Geben Sie das Kennwort erneut ein.        |

    ![Screenshot: Registerkarte „Grundlagen“ im Assistenten zum Hinzufügen virtueller Computer für Azure Stack Edge. Die Registerkarte „Grundlagen“ und die Schaltfläche „Weiter: Datenträger“ sind hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    Klicken Sie auf **Weiter: Datenträger**.

1. Auf der Registerkarte **Datenträger** fügen Sie Datenträger an Ihre VM an. 
    
    1. Sie können **Neuen Datenträger erstellen und anfügen** oder **Vorhandenen Datenträger anfügen** auswählen.

        ![Screenshot: Registerkarte „Datenträger“ im Assistenten zum Hinzufügen virtueller Computer für Azure Stack Edge. Die Option „Neuen Datenträger erstellen und anfügen“ ist hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. Wählen Sie **Neuen Datenträger erstellen und anfügen** aus. Geben Sie im Bereich **Neuen Datenträger erstellen** einen Namen für den Datenträger und die Größe in GiB an.

        ![Screenshot des Bildschirms „Neuen Datenträger erstellen“ unter „Virtuellen Computer hinzufügen“ für Azure Stack Edge. Die Schaltfläche „OK“ ist hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1. Wiederholen Sie den vorherigen Vorgang, um weitere Datenträger hinzuzufügen. Nachdem die Datenträger erstellt wurden, werden sie auf der Registerkarte **Datenträger** angezeigt. Wählen Sie **Weiter: Netzwerk** aus.

1. Auf der Registerkarte **Netzwerk** konfigurieren Sie die Netzwerkkonnektivität für die VM.

    |Parameter  |BESCHREIBUNG |
    |---------|---------|
    |Virtuelles Netzwerk    | Wählen Sie in der Dropdownliste den virtuellen Switch aus, der auf Ihrem Azure Stack Edge-Gerät erstellt wurde, als Sie Compute an der Netzwerkschnittstelle aktiviert haben.    |
    |Subnet     | Dieses Feld wird automatisch mit dem Subnetz ausgefüllt, das der Netzwerkschnittstelle zugeordnet ist, an der Sie Compute aktiviert haben.         |
    |IP-Adresse     | Geben Sie eine statische oder dynamische IP-Adresse für die VM an. Die statische IP-Adresse sollte eine verfügbare, freie IP-Adresse aus dem angegebenen Subnetzbereich sein.        |

    ![Screenshot der Registerkarte „Netzwerk“ unter „Virtuellen Computer hinzufügen“ für Azure Stack Edge. Die Registerkarte „Netzwerk“ und die Schaltfläche „Weiter: Erweitert“ sind hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    Klicken Sie auf **Weiter: Erweitert**. Auf der Registerkarte **Erweitert** können Sie eine Erweiterung auswählen, die während der VM-Bereitstellung installiert werden soll, und Sie können ein `cloud-init`-Skript angeben, um Ihren virtuellen Computer anzupassen.

1. Wenn Sie eine Erweiterung auf Ihrem virtuellen Computer installieren möchten, wenn Sie ihn erstellen, wählen Sie **Zu installierende Erweiterung auswählen** aus. Wählen Sie dann auf dem Bildschirm **Erweiterung hinzufügen** die Erweiterung aus.

    Ausführliche Schritte zum Installieren einer GPU-Erweiterung während der VM-Bereitstellung finden Sie unter [Bereitstellen von GPU-VMs](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms).

    ![Abbildung der beiden Schritte zum Hinzufügen einer GPU-Erweiterung auf der Registerkarte „Erweitert“ unter „Virtuellen Computer hinzufügen“. Die Optionen zum Auswählen und Hinzufügen einer Erweiterung sind hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-extension-01.png)

1. Wenn Sie das Hilfsprogramm `cloud-init` verwenden möchten, um den neuen virtuellen Computer beim ersten Start anzupassen, fügen Sie Ihr `cloud-init`-Skript auf der Registerkarte **Erweitert** in das Feld **Benutzerdefinierte Daten** unter **Benutzerdefinierte Daten und cloud-init** ein. 

    Weitere Informationen zur Verwendung von `cloud-init` finden Sie unter [Übersicht zu cloud-init](../virtual-machines/linux/tutorial-automate-vm-deployment.md#cloud-init-overview)

    ![Screenshot: Registerkarte „Erweitert“ des Assistenten zum Erstellen eines neuen virtuellen Computers mit hervorgehobenem cloud-init-Skript im Feld „Benutzerdefinierte Daten“](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-advanced-tab-with-cloud-init-script.png)

    Klicken Sie auf **Weiter: Überprüfen + erstellen**.

1. Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen** die Angaben für die VM. Klicken Sie anschließend auf **Erstellen**.

    ![Screenshot: Registerkarte „Überprüfen und erstellen“ des Assistenten zum Hinzufügen eines virtuellen Computers in Azure Stack Edge. Die Registerkarte „Überprüfen und erstellen“ ist hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. Die Erstellung der VM wird gestartet und kann bis zu 20 Minuten dauern. Sie können zu **Bereitstellungen** wechseln, um die VM-Erstellung zu überwachen.

    ![Screenshot: Bereich „Bereitstellungen“ in der Ansicht „virtuelle Computer“ eines Azure Stack Edge-Geräts. Der Eintrag für einen bereitgestellten virtuellen Computer ist hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

1. Nachdem die VM erfolgreich erstellt wurde, wird Ihre neue VM im Bereich **Übersicht** angezeigt.

    ![Screenshot: Bereich „Übersicht“ in der Ansicht „virtuelle Computer“ eines Azure Stack Edge-Geräts. Ein neuer virtueller Computer ist in der Liste hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. Wählen Sie die neu erstellte VM aus, um zu **Virtuelle Computer** zu wechseln.

    ![Screenshot des Bereichs „Virtuelle Computer“ eines Azure Stack Edge-Geräts. Die Bezeichnung „Virtuelle Computer“ und ein VM-Eintrag sind hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    Wählen Sie die VM aus, um die Details anzuzeigen.

    ![Screenshot: Registerkarte „Details“ im Bereich „Übersicht“ für einen virtuellen Computer in Azure Stack Edge. Die Bezeichnung „Übersicht“ und die IP-Adresse im Netzwerk sind hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

    Sie verwenden die IP-Adresse für die Netzwerkschnittstelle, um eine Verbindung mit der VM herzustellen.

## <a name="connect-to-a-vm"></a>Herstellen einer Verbindung mit einem virtuellen Computer

Je nachdem, ob Sie eine Linux- oder Windows-VM erstellt haben, können die Schritte abweichen. Sie können keine Verbindung mit den auf Ihrem Gerät bereitgestellten VMs über das Azure-Portal herstellen. Befolgen Sie die Schritte, um eine Verbindung mit Ihrer Linux- oder Windows-VM herzustellen.

### <a name="connect-to-a-linux-vm"></a>Herstellen einer Verbindung mit einem virtuellen Linux-Computer

Führen Sie diese Schritte aus, um eine Verbindung mit einer Linux-VM herzustellen.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-a-windows-vm"></a>Herstellen einer Verbindung mit einem virtuellen Windows-Computer

Führen Sie diese Schritte aus, um eine Verbindung mit einer Windows-VM herzustellen.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen einer GPU-VM](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)
- [Problembehandlung bei der VM-Bereitstellung](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [Überwachen der VM-Aktivität auf Ihrem Gerät](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [Überwachen von CPU und Arbeitsspeicher auf einer VM](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)

