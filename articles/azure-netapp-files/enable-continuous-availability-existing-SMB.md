---
title: Aktivieren der fortlaufenden Verfügbarkeit auf vorhandenen Azure NetApp Files SMB-Volumes | Microsoft-Dokumentation
description: Hier wird beschrieben, wie das SMB-Feature für fortlaufende Verfügbarkeit auf vorhandenen Azure NetApp Files SMB-Volumes aktiviert wird.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/28/2021
ms.author: b-juche
ms.openlocfilehash: 705cc540097ea41bc0039336d7cc9fdc025dd247
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218382"
---
# <a name="enable-continuous-availability-on-existing-smb-volumes"></a>Aktivieren der fortlaufenden Verfügbarkeit auf vorhandenen SMB-Volumes

Sie können das SMB-Feature „Fortlaufende Verfügbarkeit“ (Continuous Availability, CA) aktivieren, wenn Sie [ein neues SMB-Volume erstellen](azure-netapp-files-create-volumes-smb.md#continuous-availability). Sie können die SMB-Zertifizierungsstelle auch auf einem vorhandenen SMB-Volume aktivieren. In diesem Artikel erfahren Sie, wie Sie dabei vorgehen.

## <a name="considerations"></a>Weitere Überlegungen

* Die Option [**Momentaufnahmepfad ausblenden**](snapshots-edit-hide-path.md) hat derzeit keine Auswirkungen auf SMB-Volumes, die durch eine Zertifizierungsstelle aktiviert wurden.  

* Das `~snapshot`-Verzeichnis (das zum Durchlaufen anderer SMB-Volumes verwendet werden kann) ist für SMB-Volumes, die durch eine Zertifizierungsstelle aktiviert wurden, nicht sichtbar. Sie können weiterhin manuell `~snapshot\<snapshotName>` eingeben, um auf die Momentaufnahme zuzugreifen.

## <a name="steps"></a>Schritte

1. Stellen Sie sicher, dass Sie das Feature [SMB-Freigaben „Fortlaufende Verfügbarkeit“](https://aka.ms/anfsmbcasharespreviewsignup) registriert haben.  
2. Klicken Sie auf das SMB-Volume, für das die SMB-Zertifizierungsstelle aktiviert sein soll. Klicken Sie dann auf **Bearbeiten**.  
3. Aktivieren Sie im angezeigten Fenster „Bearbeiten“ das Kontrollkästchen **Fortlaufende Verfügbarkeit aktivieren**.   
    ![Momentaufnahme mit der Option „Fortlaufende Verfügbarkeit aktivieren“.](../media/azure-netapp-files/enable-continuous-availability.png)

4. Starten Sie die Windows-Systeme neu, die eine Verbindung mit der vorhandenen SMB-Freigabe herstellen.   

    > [!NOTE]
    > Wenn Sie die Option **Fortlaufende Verfügbarkeit aktivieren** auswählen, werden die vorhandenen SMB-Sitzungen nicht automatisch fortlaufend verfügbar. Nachdem Sie die Option ausgewählt haben, müssen Sie den Server neu starten, damit die Änderung wirksam wird.  

5. Verwenden Sie den folgenden Befehl, um zu überprüfen, ob die Zertifizierungsstelle aktiviert ist und auf dem System verwendet wird, das das Volume einbinden soll:

    ```powershell-interactive
    get-smbconnection | select -Property servername,ContinuouslyAvailable
    ```
 
    Möglicherweise müssen Sie eine neuere PowerShell-Version installieren. 

    Wenn Sie den Servernamen kennen, können Sie den `-ServerName`-Parameter mit dem Befehl verwenden. Weitere Informationen finden Sie in den PowerShell-Befehlsdetails zu [Get-SmbConnection](/powershell/module/smbshare/get-smbconnection?view=windowsserver2019-ps&preserve-view=true).

## <a name="next-steps"></a>Nächste Schritte  

* [Erstellen eines SMB-Volumes für Azure NetApp Files](azure-netapp-files-create-volumes-smb.md)
