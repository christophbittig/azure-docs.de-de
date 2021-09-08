---
title: Erstellen benutzerdefinierter VM-Images für Ihr Azure Stack Edge Pro GPU-Gerät
description: Beschreibt, wie benutzerdefinierte Windows- und Linux-VM-Images für die Bereitstellung virtueller Computer auf Azure Stack Edge Pro GPU-Geräten erstellt werden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: 94ffb38c71437c8f5902866620b5ac0c2467edfd
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114462913"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-gpu-device"></a>Erstellen benutzerdefinierter VM-Images für Ihr Azure Stack Edge Pro GPU-Gerät

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Wenn Sie virtuelle Computer auf Ihrem Azure Stack Edge Pro GPU-Gerät bereitstellen möchten, müssen Sie in der Lage sein, benutzerdefinierte VM-Images zu erstellen, mit denen Sie wiederum virtuelle Computer in Azure erstellen können. In diesem Artikel werden die Schritte zum Erstellen benutzerdefinierter VM-Images in Azure für virtuelle Windows- und Linux-Computer und zum Herunterladen oder Kopieren dieser Images in ein Azure Storage-Konto beschrieben.

Für die Vorbereitung eines benutzerdefinierten VM-Images gibt es einen bestimmten Workflow. Für die Imagequelle müssen Sie eine feste VHD von einem virtuellen Gen1-Computer beliebiger Größe verwenden, die von Azure unterstützt wird. Optionen für die VM-Größe finden Sie unter [Unterstützte VM-Größen](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes).

## <a name="prerequisites"></a>Voraussetzungen

Erfüllen Sie die folgenden Voraussetzungen, bevor Sie Ihr VM-Image erstellen:

- [Laden Sie AzCopy herunter](../storage/common/storage-use-azcopy-v10.md#download-azcopy). AzCopy bietet Ihnen eine schnelle Möglichkeit, einen Betriebssystemdatenträger in ein Azure Storage-Konto zu kopieren.

---

## <a name="create-a-custom-vm-image"></a>Erstellen eines benutzerdefinierten VM-Images

Die Schritte zum Vorbereiten eines benutzerdefinierten VM-Images variieren für einen virtuellen Windows- oder Linux-Computer.


### <a name="windows"></a>[Windows](#tab/windows)

Führen Sie die folgenden Schritte aus, um ein Windows-VM-Image zu erstellen:

1. Erstellen Sie einen virtuellen Windows-Computer in Azure. Entsprechende Portalanleitungen finden Sie unter [Erstellen eines virtuellen Windows-Computers in Azure-Portal](../virtual-machines/windows/quick-create-portal.md). PowerShell-Anleitungen finden Sie unter [Tutorial: Erstellen und Verwalten von virtuellen Windows-Computern mit Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md).  

   Der virtuelle Computer darf nur VM der Generation 1 sein. Der Betriebssystemdatenträger, den Sie zum Erstellen Ihres VM-Images verwenden, muss eine VHD mit fester Größe sein, die allerdings von Azure unterstützt wird. Optionen für die VM-Größe finden Sie unter [Unterstützte VM-Größen](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes).

   Sie können jede Windows Gen1-VM mit einer VHD mit fester Größe in Azure Marketplace verwenden. Eine Liste der Azure Marketplace-Images, die funktionieren könnten, finden Sie unter [Häufig für Azure Stack Edge verwendete Azure Marketplace-Images](azure-stack-edge-gpu-create-virtual-machine-marketplace-image.md#commonly-used-marketplace-images).

2. Generalisieren Sie die VM. Stellen Sie zum Generalisieren des virtuellen Computers [eine Verbindung mit dem virtuellen Computer her](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-windows-vm), öffnen Sie eine Eingabeaufforderung und führen Sie den folgenden `sysprep` Befehl aus:

   ```dos
   c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
   ```

   > [!IMPORTANT]
   > Nachdem der Befehl ausgeführt wurde, wird die VM heruntergefahren. **Starten Sie den virtuellen Computer nicht neu**. Wenn Sie den virtuellen Computer neu starten, wird der Datenträger beschädigt, den Sie gerade vorbereitet haben.


### <a name="linux"></a>[Linux](#tab/linux)

Führen Sie die folgenden Schritte aus, um ein Linux-VM-Image zu erstellen:

1. Erstellen Sie einen virtuellen Linux-Computer in Azure. Entsprechende Portalanleitungen finden Sie unter [Schnellstart: Erstellen eines virtuellen Linux-Computers in Azure-Portal](../virtual-machines/linux/quick-create-portal.md).  PowerShell-Anleitungen finden Sie unter [Schnellstart: Erstellen eines virtuellen Linux-Computers in Azure mit PowerShell](../virtual-machines/linux/quick-create-powershell.md).

   Sie können eine beliebige Gen1-VM mit einer VHD mit fester Größe in Azure Marketplace verwenden, um benutzerdefinierte Linux-Images zu erstellen, mit Ausnahme von RHEL-Images (Red Hat Enterprise Linux), die zusätzliche Schritte erfordern. Eine Liste der Azure Marketplace-Images, die funktionieren könnten, finden Sie unter [Häufig für Azure Stack Edge verwendete Azure Marketplace-Images](azure-stack-edge-gpu-create-virtual-machine-marketplace-image.md#commonly-used-marketplace-images). Eine Anleitung zu RHEL-Images finden Sie weiter unten unter [Verwenden von RHEL-BYOS-Images.](#using-rhel-byos-images)

1. Heben Sie die Bereitstellung der VM auf. Verwenden Sie den Azure VM-Agent zum Löschen computerspezifischer Dateien und Daten. Verwenden Sie auf dem virtuellen Linux-Quellcomputer den Befehl `waagent` mit dem Parameter `-deprovision+user`. Weitere Informationen finden Sie unter [Verstehen und Verwenden des Azure Linux-Agents](../virtual-machines/extensions/agent-linux.md).

    1. [Stellen Sie mit einem SSH-Client eine Verbindung mit Ihrem virtuellen Linux-Computer her](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm).
    2. Geben Sie im SSH-Fenster den folgenden Befehl ein:
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > Führen Sie diesen Befehl nur auf einem virtuellen Computer aus, den Sie als Image erfassen möchten. Die Ausführung dieses Befehls garantiert nicht, dass alle vertraulichen Informationen aus dem Image gelöscht werden oder dass es für eine erneute Verteilung geeignet ist. Der Parameter `+user` entfernt auch das zuletzt bereitgestellte Benutzerkonto. Wenn die Anmeldeinformationen des Benutzerkontos auf dem virtuellen Computer verbleiben sollen, verwenden Sie nur `-deprovision`.
     
    3. Geben Sie **y** ein, um fortzufahren. Sie können den Parameter `-force` hinzufügen, um diesen Bestätigungsschritt zu vermeiden.
    4. Geben Sie nach der Ausführung dieses Befehls den Befehl **exit** ein, um den SSH-Client zu schließen.  Der virtuelle Computer wird zu diesem Zeitpunkt immer noch ausgeführt.


### <a name="using-rhel-byos-images"></a>Verwenden von RHEL-BYOS-Images

Bei Verwendung von RHEL-Images (Red Hat Enterprise Linux) werden nur die RHEL-BYOS-Images (Bring Your Own Subscription) unterstützt (auch als Red Hat Gold-Images bezeichnet). Diese können zum Erstellen Ihres VM-Images verwendet werden. Die RHEL-Standardimages auf Azure Marketplace mit nutzungsbasierter Bezahlung werden auf Azure Stack Edge nicht unterstützt.

Führen Sie die folgenden Schritte aus, um ein VM-Image mit dem RHEL-BYOS-Image zu erstellen:

1. Melden Sie sich bei der [Red Hat-Abonnementverwaltung](https://access.redhat.com/management) an. Navigieren Sie in der oberen Menüleiste zum [Cloud Access-Dashboard](https://access.redhat.com/management/cloud).
1. Aktivieren Sie Ihr Azure-Abonnements. Weitere Informationen finden Sie in den [Ausführlichen Anweisungen](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/getting-started-with-ca_cloud-access). Wenn Sie das Abonnement aktivieren, können Sie auf die Red Hat Gold-Images zugreifen.

1. Akzeptieren Sie die Azure-Nutzungsbedingungen (nur einmal pro Azure-Abonnement, pro Image) und erstellen Sie einen virtuellen Computer. Weitere Informationen finden Sie in den [Anweisungen](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/understanding-gold-images_cloud-access). 

Sie können jetzt den virtuellen Computer verwenden, den Sie bereitgestellt haben, um unter Linux [ein benutzerdefiniertes VM-Image zu erstellen](#create-a-custom-vm-image).

---

## <a name="download-os-disk-to-storage-account"></a>Herunterladen des Betriebssystemdatenträgers in das Speicherkonto

Um Ihr benutzerdefiniertes VM-Image zum Bereitstellen von VMs auf Ihrem Gerät zu verwenden, müssen Sie den Betriebssystemdatenträger in ein Azure Storage-Konto herunterladen. Es wird empfohlen, dasselbe Speicherkonto zu verwenden, das Sie für Ihr Gerät verwendet haben.

Um den Betriebssystemdatenträger für den virtuellen Computer in ein Azure Storage-Konto herunterzuladen, gehen Sie folgendermaßen vor:

1. [Beenden Sie den virtuellen Computer im Portal](../virtual-machines/windows/download-vhd.md#stop-the-vm). Sie müssen dies tun, um die Zuordnung des Betriebssystemdatenträgers selbst dann wieder freizugeben, wenn Ihr virtueller Windows-Computer heruntergefahren wurde, nachdem Sie `sysprep` ausgeführt haben, um ihn zu generalisieren.

1. [Generieren Sie eine Download-URL für den Betriebssystemdatenträger](../virtual-machines/windows/download-vhd.md#generate-download-url) und notieren Sie sich die URL. Standardmäßig läuft die URL nach 3.600 Sekunden (1 Stunde) ab. Sie können diese Zeit bei Bedarf erhöhen.
      
1. Laden Sie die VHD mit einer der folgenden Methoden in Ihr Azure Storage-Konto herunter:
   
   - Methode 1 zur schnellen Übertragung: Verwenden Sie AzCopy, um die VHD in Ihr Azure Storage-Konto zu kopieren. Anweisungen finden Sie weiter unten unter [Verwenden von AzCopy zum Kopieren des VM-Images in das Speicherkonto](#copy-vhd-to-storage-account-using-azcopy). 

   - Methode 2 für einfaches Herunterladen mit 1 Klick: Wählen Sie **VHD-Datei herunterladen** aus, wenn Sie eine Download-URL generieren (in Schritt 3b), um den Datenträger aus dem Portal herunterzuladen. **Wenn Sie diese Methode verwenden, kann das Kopieren des Datenträgers sehr lange dauern, und Sie müssen [die VHD in Ihr Azure Storage-Konto hochladen](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload), um VMs über das Portal erstellen zu können**.

Sie können diese VHD jetzt verwenden, um VMs auf Ihrem Azure Stack Edge Pro GPU-Gerät zu erstellen und bereitzustellen.

## <a name="copy-vhd-to-storage-account-using-azcopy"></a>Kopieren der VHD in das Speicherkonto mit AzCopy

In den folgenden Prozeduren wird beschrieben, wie Sie AzCopy verwenden, um ein benutzerdefiniertes VM-Image in ein Azure Storage-Konto zu kopieren, damit Sie das Image zum Bereitstellen von VMs auf Ihrem Azure Stack Edge Pro GPU-Gerät verwenden können. Sie sollten Ihre benutzerdefinierten VM-Images in einem vorhandenen Speicherkonto speichern, das sich in derselben Region bzw. in demselben Abonnement wie Azure Stack Edge befindet.


### <a name="create-target-uri-for-a-container"></a>Erstellen eines Ziel-URI für einen Container

AzCopy erfordert einen *Ziel-URI,* der angibt, wohin das neue Image in Ihr Speicherkonto kopiert werden soll. Bevor Sie AzCopy ausführen, generieren Sie eine SAS-URL (Shared Access Signature) für den Blob-Container, in den Sie die Datei kopieren möchten. Um den Ziel-URI zu erstellen, fügen Sie der SAS-URL den Dateinamen hinzu.

Um den Ziel-URI für Ihre vorbereitete VHD zu erstellen, gehen Sie folgendermaßen vor:

1. Führen Sie die folgenden Schritte aus, um eine SAS-URL für einen Container in einem Azure Storage-Konto zu generieren:

   1. Öffnen Sie im Azure-Portal das Speicherkonto, und wählen Sie **Container** aus. Klicken Sie mit der rechten Maustaste auf den Blob-Container, den Sie verwenden möchten, und wählen Sie **SAS generieren** aus.

      ![Screenshot der Option „SAS generieren“ für einen Blob-Container im Azure-Portal](./media/azure-stack-edge-gpu-create-virtual-machine-image/storage-container-generate-sas-option.png)

   1. Wählen Sie auf dem Bildschirm **SAS generieren** die Option **Lesen** und **Schreiben** in **Berechtigungen** aus.  

      ![Screenshot des Bildschirms „SAS generieren“ mit ausgewählten Lese- und Schreibberechtigungen](./media/azure-stack-edge-gpu-create-virtual-machine-image/generate-sas-screen-read-and-write-permissions.png)

   1. Wählen Sie **SAS-Token und URL generieren** und dann **Kopieren** aus, um die **Blob-SAS-URL** zu kopieren.

      ![Screenshot des Bildschirms „SAS generieren“ mit Optionen zum Generieren und Kopieren einer Blob-SAS-URL](./media/azure-stack-edge-gpu-create-virtual-machine-image/generate-sas-screen-copy-blob-sas-url.png)

1. Um den Ziel-URI für den `azcopy` Befehl zu erstellen, fügen Sie der SAS-URL den gewünschten Dateinamen hinzu.

   Eine Blob-SAS-URL weist das folgende Format auf: 

   ![Grafik einer Blob-SAS-URL mit Containerpfad und Ort zum Einfügen des neuen Dateinamens mit Bezeichnung](./media/azure-stack-edge-gpu-create-virtual-machine-image/blob-sas-url-format.png)

   Fügen Sie den Dateinamen im Format `/<filename>.vhd` vor dem Fragezeichen ein, das am Anfang der Abfragezeichenfolge steht. Die Dateinamenerweiterung muss VHD sein. 

   Beispielsweise kopiert die folgende Blob-SAS-URL die Datei **osdisk.vhd** in den Blob-Container **virtualmachines** in **mystorageaccount**.

   ![Grafik eines Blob-SAS-URL-Beispiels für eine VHD mit dem Namen osdisk](./media/azure-stack-edge-gpu-create-virtual-machine-image/target-uri-example-based-on-blob-sas-url.png)


### <a name="copy-vhd-to-blob-container"></a>Kopieren einer VHD in einen Blob-Container

Um Ihre VHD mit AzCopy in einen Blob-Container zu kopieren, gehen Sie wie folgt vor:

 1. [Laden Sie AzCopy herunter](../storage/common/storage-use-azcopy-v10.md#download-azcopy), wenn Sie dies noch nicht getan haben.
 
 1. Navigieren Sie in PowerShell zu dem Verzeichnis, in dem Sie die azcopy.exe gespeichert haben und führen Sie den folgenden Befehl aus:

    `.\azcopy copy <source URI> <target URI> --recursive`

    Dabei gilt:
    * `<source URI>` ist die Download-URL, die Sie zuvor generiert haben.
    * `<target URI>` gibt an, in welchen Blob-Container das neue Image in Ihrem Azure Storage-Konto kopiert werden soll. Anweisungen finden Sie weiter unten unter [Verwenden von AzCopy zum Kopieren des VM-Images in das Speicherkonto](#copy-vhd-to-storage-account-using-azcopy).

    Der folgende URI kopiert zum Bsp. eine Datei namens **windowsosdisk.vhd** in den Blob-Container für **virtuelle Computer** im Speicherkonto **mystorageaccount**:

    ```azcopy 
    .\azcopy copy "https://md-h1rvdq3wwtdp.z24.blob.storage.azure.net/gxs3kpbgjhkr/abcd?sv=2018-03-28&sr=b&si=f86003fc-a231-43b0-baf2-61dd51e3a05a&sig=o5Rj%2BNZSook%2FVNMcuCcwEwsr0i7sy%2F7gIDzak6JhlKg%3D" "https://mystorageaccount.blob.core.windows.net/virtualmachines/osdisk.vhd?sp=rw&st=2021-05-21T16:52:24Z&se=2021-05-22T00:52:24Z&spr=https&sv=2020-02-10&sr=c&sig=PV3Q3zpaQ%2FOLidbQJDKlW9nK%2BJ7PkzYv2Eczxko5k%2Bg%3D" --recursive
    ```

#### <a name="sample-output"></a>Beispielausgabe

Für den obigen AzCopy-Beispielbefehl gibt die folgende Ausgabe an, dass eine Kopie erfolgreich erstellt wurde.

   ```output
   PS C:\azcopy\azcopy_windows_amd64_10.10.0> .\azcopy copy "https://md-h1rvdq3wwtdp.z24.blob.storage.azure.net/gxs3kpbgjhkr/abcd?sv=2018-03-28&sr=b&si=f86003fc-a231-43b0-baf2-61dd51e3a05a&sig=o5Rj%2BNZSook%2FVNMcuCcwEwsr0i7sy%2F7gIDzak6JhlKg%3D" "https://mystorageaccount.blob.core.windows.net/virtualmachines/osdisk.vhd?sp=rw&st=2021-05-21T16:52:24Z&se=2021-05-22T00:52:24Z&spr=https&sv=2020-02-10&sr=c&sig=PV3Q3zpaQ%2FOLidbQJDKlW9nK%2BJ7PkzYv2Eczxko5k%2Bg%3D" --recursive
   INFO: Scanning...
   INFO: Failed to create one or more destination container(s). Your transfers may still succeed if the container already exists.
   INFO: Any empty folders will not be processed, because source and/or destination doesn't have full folder support

   Job 783f2177-8317-3e4b-7d2f-697a8f1ab63c has started
   Log file is located at: C:\Users\aseuser\.azcopy\783f2177-8317-3e4b-7d2f-697a8f1ab63c.log

   INFO: Destination could not accommodate the tier P10. Going ahead with the default tier. In case of service to service transfer, consider setting the flag --s2s-preserve-access-tier=false.
   100.0 %, 0 Done, 0 Failed, 1 Pending, 0 Skipped, 1 Total,

   Job 783f2177-8317-3e4b-7d2f-697a8f1ab63c summary
   Elapsed Time (Minutes): 1.4671
   Number of File Transfers: 1
   Number of Folder Property Transfers: 0
   Total Number of Transfers: 1
   Number of Transfers Completed: 1
   Number of Transfers Failed: 0
   Number of Transfers Skipped: 0
   TotalBytesTransferred: 136367309312
   Final Job Status: Completed

   PS C:\azcopy\azcopy_windows_amd64_10.10.0>
   ```

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen von VMs auf Ihrem Gerät mit dem Azure-Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Bereitstellen von VMs auf Ihrem Gerät über PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)