---
title: Verwenden von Zertifikaten mit Azure Stack Edge Pro-GPU | Microsoft-Dokumentation
description: Hier wird die Verwendung von Zertifikaten mit Azure Stack Edge Pro-GPU-Geräten beschrieben. Dabei werden die Gründe für die Verwendung und die möglichen Typen erörtert, und Sie erfahren, wie Sie Zertifikate auf Ihr Gerät hochladen können.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/01/2021
ms.author: alkohli
ms.openlocfilehash: 15cfd2b7188f84e14aa12824f8d5fab06d226330
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363999"
---
# <a name="upload-import-and-export-certificates-on-azure-stack-edge-pro-gpu"></a>Hochladen, Importieren und Exportieren von Zertifikaten auf Azure Stack Edge Pro GPU-Geräte

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Um eine sichere und vertrauenswürdige Kommunikation zwischen Ihrem Azure Stack Edge-Gerät und den Clients, die eine Verbindung damit herstellen, sicherzustellen, können Sie selbstsignierte Zertifikate oder eigene Zertifikate verwenden. In diesem Artikel wird beschrieben, wie Sie diese Zertifikate verwalten, einschließlich Hochladen, Importieren und Exportieren dieser Zertifikate oder Anzeigen ihrer Ablaufdaten.

Weitere Informationen zum Erstellen dieser Zertifikate finden Sie unter [Erstellen von Zertifikaten mit Azure PowerShell](azure-stack-edge-gpu-create-certificates-powershell.md).


## <a name="upload-certificates-on-your-device"></a>Hochladen von Zertifikaten auf Ihr Gerät

Wenn Sie Ihre eigenen Zertifikate verwenden, befinden sich die Zertifikate, die Sie für Ihr Gerät erstellt haben, standardmäßig im **persönlichen Speicher** auf Ihrem Client. Diese Zertifikate müssen auf dem Client in entsprechende Formatdateien exportiert werden, die dann auf das Gerät hochgeladen werden können.


### <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Ihre Stammzertifikate und Endpunktzertifikate auf das Gerät hochladen, stellen Sie sicher, dass die Zertifikate im entsprechenden Format exportiert wurden.

- Das Stammzertifikat muss als DER-Format mit der `.cer`-Erweiterung exportiert werden. Ausführliche Schritte finden Sie unter [Exportieren von Zertifikaten im DER-Format](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-der-format).
- Die Endpunktzertifikate müssen als *PFX*-Dateien mit privaten Schlüsseln exportiert werden. Ausführliche Schritte finden Sie unter [Exportieren von Zertifikaten als *PFX*-Datei mit privaten Schlüsseln](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-pfx-format-with-private-key). 

### <a name="upload-certificates"></a>Hochladen von Zertifikaten 

Um das Stamm- und die Endpunktzertifikate auf das Gerät hochzuladen, verwenden Sie die Option **+ Zertifikat hinzufügen** auf der Seite **Zertifikate** der lokalen Webbenutzeroberfläche. Führen Sie die folgenden Schritte aus:

1. Laden Sie zuerst die Stammzertifikate hoch. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Zertifikate**.
1. Wählen Sie **+ Zertifikat hinzufügen** aus.

    ![Hinzufügen des Signaturkettenzertifikats 1](media/azure-stack-edge-gpu-manage-certificates/add-cert-1.png)

1. Speichern Sie das Zertifikat.

#### <a name="upload-endpoint-certificate"></a>Hochladen des Endpunktzertifikats

1. Laden Sie als nächstes die Endpunktzertifikate hoch. 

    ![Hinzufügen des Signaturkettenzertifikats 2](media/azure-stack-edge-gpu-manage-certificates/add-cert-2.png)

    Wählen Sie die Zertifikatsdateien im *PFX*-Format aus, und geben Sie das Kennwort ein, das Sie beim Exportieren des Zertifikats angegeben haben. Das Anwenden des Azure Resource Manager-Zertifikats kann einige Minuten dauern.

    Wenn die Signaturkette nicht zuerst aktualisiert wird und Sie versuchen, die Endpunktzertifikate hochzuladen, erhalten Sie einen Fehler.

    ![Anwenden von Zertifikatfehlern](media/azure-stack-edge-gpu-manage-certificates/apply-cert-error-1.png)

    Wechseln Sie zurück, und laden Sie das Signaturkettenzertifikat hoch. Anschließend laden Sie die Endpunktzertifikate hoch und wenden sie an.

> [!IMPORTANT]
> Wenn der Gerätename oder die DNS-Domäne geändert wird, müssen neue Zertifikate erstellt werden. Die Clientzertifikate und die Gerätezertifikate sollten dann mit dem neuen Gerätenamen und der DNS-Domäne aktualisiert werden. 

#### <a name="upload-kubernetes-certificates"></a>Hochladen von Kubernetes-Zertifikaten

Kubernetes-Zertifikate können für Edge-Containerregistrierung oder für Kubernetes-Dashboards verwendet werden. In jedem Fall müssen ein Zertifikat und eine Schlüsseldatei hochgeladen werden. Führen Sie die folgenden Schritte aus, um Kubernetes-Zertifikate zu erstellen und hochzuladen:


1. Sie verwenden `openssl`, um das Kubernetes-Dashboardzertifikat oder Edge-Containerregistrierung zu erstellen. Stellen Sie sicher, dass Sie openssl auf dem System installieren, das Sie zum Erstellen der Zertifikate verwenden würden. Auf einem Windows-System können Sie Chocolatey verwenden, um `openssl` zu installieren. Nachdem Sie Chocolatey installiert haben, öffnen Sie PowerShell, und geben Sie Folgendes ein:
    
    ```powershell
    choco install openssl
    ```
1. Verwenden Sie `openssl`, um diese Zertifikate zu erstellen. Eine `cert.pem`-Zertifikatsdatei und eine `key.pem`-Schlüsseldatei werden erstellt.  

    - Verwenden Sie für die Edge-Containerregistrierung den folgenden Befehl:
    
        ```powershell
        openssl req -newkey rsa:4096 -nodes -sha256 -keyout key.pem -x509 -days 365 -out cert.pem -subj "/CN=<ecr.endpoint-suffix>"
        ``` 
        Beispielausgabe: 

        ```powershell
        PS C:\WINDOWS\system32> openssl req -newkey rsa:4096 -nodes -sha256 -keyout key.pem -x509 -days 365 -out cert.pem -subj "/CN=ecr.dbe-1d6phq2.microsoftdatabox.com"
        Generating a RSA private key
        .....................++++....++++
        writing new private key to 'key.pem'
        -----
        PS C:\WINDOWS\system32>
        ```    
    - Verwenden Sie für das Kubernetes-Dashboardzertifikat den folgenden Befehl:  
     
        ```powershell
        openssl req -newkey rsa:4096 -nodes -sha256 -keyout key.pem -x509 -days 365 -out cert.pem -subj "/CN=<<kubernetes-dashboard.endpoint-suffix> OR <endpoint-suffix>>"
        ```
        Beispielausgabe: 

        ```powershell
        PS C:\WINDOWS\system32> openssl req -newkey rsa:4096 -nodes -sha256 -keyout key.pem -x509 -days 365 -out cert.pem -subj "/CN=kubernetes-dashboard.dbe-1d8phq2.microsoftdatabox.com"
        Generating a RSA private key
        .....................++++....++++
        writing new private key to 'key.pem'
        -----
        PS C:\WINDOWS\system32>
        ```          
1. Laden Sie das Kubernetes-Zertifikat und die entsprechende Schlüsseldatei hoch, die Sie zuvor generiert haben.
    
    - Für Edge-Containerregistrierung
    
        ![Screenshot: Hinzufügen eines Edge-Containerregistrierungszertifikats und einer Schlüsseldatei](media/azure-stack-edge-gpu-manage-certificates/add-cert-3.png)      

    - Für Kubernetes-Dashboard     

        ![Screenshot: Hinzufügen eines Kubernetes-Dashboardzertifikats und einer Schlüsseldatei](media/azure-stack-edge-gpu-manage-certificates/add-cert-4.png) 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>Importieren von Zertifikaten auf den Client, der auf das Gerät zugreift

Sie können die von Geräten generierten Zertifikate verwenden oder Ihre eigenen Zertifikate bereitstellen. Wenn Sie vom Gerät generierte Zertifikate verwenden, müssen Sie die Zertifikate auf Ihren Client herunterladen, bevor Sie sie in den entsprechenden Zertifikatspeicher importieren können. Weitere Informationen finden Sie unter [Herunterladen von Zertifikaten auf Ihren Client, der auf das Gerät zugreift](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates).

In beiden Fällen müssen die Zertifikate, die Sie erstellt und auf Ihr Gerät hochgeladen haben, auf Ihrem Windows-Client (der auf das Gerät zugreift) in den entsprechenden Zertifikatspeicher importiert werden. 

- Das Stammzertifikat, das Sie im DER-Format exportiert haben, sollte nun in die **vertrauenswürdigen Stammzertifizierungsstellen** auf dem Clientsystem importiert werden. Ausführliche Schritte finden Sie unter [Importieren von Zertifikaten in den Speicher „Vertrauenswürdige Stammzertifizierungsstellen“](#import-certificates-as-der-format).

- Die Endpunktzertifikate, die Sie als `.pfx` exportiert haben, müssen im DER-Format mit der Erweiterung `.cer` exportiert werden. Diese `.cer` wird dann in den **Zertifikatspeicher „Persönlich“** des Systems importiert. Ausführliche Schritte finden Sie unter [Importieren von Zertifikaten in den Zertifikatspeicher „Persönlich“](#import-certificates-as-der-format).

### <a name="import-certificates-as-der-format"></a>Importieren von Zertifikaten im DER-Format 

Führen Sie die folgenden Schritte aus, um Zertifikate auf einem Windows-Client zu importieren:

1. Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie **Zertifikat installieren** aus. Mit dieser Aktion wird der Zertifikatimport-Assistent gestartet.

    ![Importieren des Zertifikats 1](media/azure-stack-edge-gpu-manage-certificates/import-cert-1.png)

2. Wählen Sie für **Speicherort** die Option **Lokaler Computer** und dann **Weiter** aus.

    ![Importieren des Zertifikats 2](media/azure-stack-edge-gpu-manage-certificates/import-cert-2.png)

3. Wählen Sie **Alle Zertifikate in folgendem Speicher speichern** und dann **Durchsuchen** aus. 

    - Um in den persönlichen Speicher zu importieren, navigieren Sie zum persönlichen Speicher Ihres Remotehosts, und wählen Sie dann **Weiter** aus.

        ![Importieren des Zertifikats 4](media/azure-stack-edge-gpu-manage-certificates/import-cert-4.png)


    - Um in einen vertrauenswürdigen Speicher zu importieren, navigieren Sie zu der vertrauenswürdigen Stammzertifizierungsstelle, und wählen Sie dann **Weiter** aus.

        ![Importieren des Zertifikats 3](media/azure-stack-edge-gpu-manage-certificates/import-cert-3.png)

 
4. Wählen Sie **Fertig stellen** aus. Eine Meldung wird angezeigt, die besagt, dass der Import erfolgreich war.


## <a name="view-certificate-expiry"></a>Anzeigen des Zertifikatablaufs

Wenn Sie Ihre eigenen Zertifikate bereitstellen, laufen die Zertifikate in der Regel nach einem Jahr oder sechs Monaten ab. Wenn Sie das Ablaufdatum auf Ihrem Zertifikat anzeigen möchten, wechseln Sie zur Seite **Zertifikate** auf der lokalen Webbenutzeroberfläche Ihres Geräts. Wenn Sie ein bestimmtes Zertifikat auswählen, können Sie das Ablaufdatum auf Ihrem Zertifikat anzeigen.


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Zertifikatprobleme behandeln](azure-stack-edge-gpu-certificate-troubleshooting.md).
