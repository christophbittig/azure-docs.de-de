---
title: Vorbereiten von Zertifikaten für das Hochladen auf Ihr Azure Stack Edge Pro GPU-/Pro R-/Mini R-Gerät
description: Beschreibt, wie Sie Zertifikate für das Hochladen auf Azure Stack Edge Pro GPU-/Pro R/Mini R-Geräte vorbereiten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/30/2021
ms.author: alkohli
ms.openlocfilehash: 2db11b9a1d159e292140901b50c2d7b659c88235
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2021
ms.locfileid: "113364054"
---
# <a name="prepare-certificates-to-upload-on-your-azure-stack-edge-pro-gpu"></a>Vorbereiten von Zertifikaten für das Hochladen auf Ihr Azure Stack Edge Pro GPU-Gerät

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In diesem Artikel wird beschrieben, wie Sie die Zertifikate in ein geeignetes Format konvertieren, damit diese auf Ihr Azure Stack Edge-Gerät hochgeladen werden können. Dieses Verfahren ist in der Regel erforderlich, wenn Sie Ihre eigenen Zertifikate verwenden (BYOC).

Weitere Informationen zum Erstellen dieser Zertifikate finden Sie unter [Erstellen von Zertifikaten mit Azure PowerShell](azure-stack-edge-gpu-create-certificates-powershell.md).


## <a name="prepare-certificates"></a>Vorbereiten von Zertifikaten

Wenn Sie Ihre eigenen Zertifikate verwenden, befinden sich die Zertifikate, die Sie für Ihr Gerät erstellt haben, standardmäßig im **persönlichen Speicher** auf Ihrem Client. Diese Zertifikate müssen auf dem Client in entsprechende Formatdateien exportiert werden, die dann auf das Gerät hochgeladen werden können.

- **Stammzertifikate vorbereiten**: Das Stammzertifikat muss im DER-Format mit der `.cer`-Erweiterung exportiert werden. Ausführliche Schritte finden Sie unter [Exportieren von Zertifikaten im DER-Format](#export-certificates-as-der-format).

- **Endpunktzertifikate vorbereiten**: Die Endpunktzertifikate müssen als *PFX*-Dateien mit privaten Schlüsseln exportiert werden. Ausführliche Schritte finden Sie unter [Exportieren von Zertifikaten als *PFX*-Datei mit privaten Schlüsseln](#export-certificates-as-pfx-format-with-private-key). 


## <a name="export-certificates-as-der-format"></a>Exportieren von Zertifikaten im DER-Format

1. Führen Sie *certlm.msc* aus, um den lokalen Computerzertifikatspeicher zu starten.

1. Wählen Sie im Zertifikatspeicher „Persönlich“ das Stammzertifikat aus. Klicken Sie mit der rechten Maustaste, und wählen Sie **Alle Aufgaben > Exportieren** aus.

    ![Exportieren des Zertifikats DER 1](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. Der Zertifikat-Assistent wird geöffnet. Wählen Sie das Format als **DER-codiertes binäres X.509 (.cer)** aus. Wählen Sie **Weiter** aus.

    ![Exportieren des Zertifikats DER 2](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. Durchsuchen und wählen Sie den Speicherort, an den Sie die Datei im CER-Format exportieren möchten.

    ![Exportieren des Zertifikats DER 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)

4. Wählen Sie **Fertig stellen** aus.

    ![Exportieren des Zertifikats DER 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="export-certificates-as-pfx-format-with-private-key"></a>Exportieren von Zertifikaten als PFX-Format mit privatem Schlüssel

Führen Sie die folgenden Schritte aus, um ein SSL-Zertifikat mit privatem Schlüssel auf einem Windows-Computer zu exportieren. 

> [!IMPORTANT]
> Führen Sie diese Schritte auf demselben Computer aus, auf dem Sie das Zertifikat erstellt haben. 

1. Führen Sie *certlm.msc* aus, um den lokalen Computerzertifikatspeicher zu starten.

1. Doppelklicken Sie auf den Ordner **Persönlich** und dann auf **Zertifikate**.

    ![Exportieren des Zertifikats 1](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. Klicken Sie mit der rechten Maustaste auf das Zertifikat, das Sie sichern möchten, und wählen Sie **Alle Aufgaben > Exportieren** aus.

    ![Exportieren des Zertifikats 2](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. Folgen Sie dem Zertifikatexport-Assistenten, um Ihr Zertifikat in einer PFX-Datei zu sichern.

    ![Exportieren des Zertifikats 3](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. Wählen Sie **Ja, privaten Schlüssel exportieren** aus.

    ![Exportieren des Zertifikats 4](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. Wählen Sie **Wenn möglich, alle Zertifikate in den Zertifikatpfad einbeziehen**, **Alle erweiterten Eigenschaften exportieren** und **Zertifikatdatenschutz aktivieren** aus. 

    > [!IMPORTANT]
    > Wählen Sie NICHT die Option **Privaten Schlüssel löschen** aus, wenn der Export erfolgreich ist.

    ![Exportieren des Zertifikats 5](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. Geben Sie ein Kennwort ein, das Sie sich merken können. Bestätigen Sie das Kennwort. Das Kennwort schützt den privaten Schlüssel.

    ![Exportieren des Zertifikats 6](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. Wählen Sie diese Option aus, um die Datei an einem bestimmten Ort zu speichern.

    ![Exportieren des Zertifikats 7](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. Wählen Sie **Fertig stellen** aus.

    ![Exportieren des Zertifikats 8](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. Sie erhalten die Meldung, dass der Export erfolgreich war. Klicken Sie auf **OK**.

    ![Exportieren des Zertifikats 9](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

Die Sicherungskopie der PFX-Datei wird jetzt an dem von Ihnen gewählten Ort gespeichert und kann zu Ihrer Sicherheit verschoben oder aufbewahrt werden.


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Zertifikate auf Ihr Gerät hochladen](azure-stack-edge-gpu-manage-certificates.md).
