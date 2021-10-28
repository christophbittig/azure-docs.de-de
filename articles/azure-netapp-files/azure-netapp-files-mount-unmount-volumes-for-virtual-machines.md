---
title: Einbinden von Azure NetApp Files-Volumes auf virtuellen Computern
description: Erfahren Sie, wie ein Volume auf virtuellen Windows- oder Linux-Computern eingebunden oder die Einbindung aufgehoben wird.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 05/17/2021
ms.openlocfilehash: 78932885ee7c1ec3650de08efe340e998a54acf7
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240032"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Einbinden oder Aufheben der Einbindung eines Volumes auf virtuellen Windows- oder Linux-Computern 

Volumes für virtuelle Windows- oder Linux-Computer lassen sich nach Bedarf einbinden. Dies kann jederzeit wieder rückgängig gemacht werden.  Die Einbindungsanweisungen für virtuelle Linux-Computer stehen in Azure NetApp Files zur Verfügung.  

## <a name="requirements"></a>Requirements (Anforderungen) 

* Sie benötigen mindestens eine Exportrichtlinie, um auf ein NFS-Volume zugreifen zu können.
* Um ein NFS-Volume erfolgreich einzubinden, stellen Sie sicher, dass die folgenden NFS-Ports zwischen den Client- und NFS-Volumes geöffnet sind:
    * 111 TCP/UDP = `RPCBIND/Portmapper`
    * 635 TCP/UDP = `mountd`
    * 2049 TCP/UDP = `nfs`
    * 4045 TCP/UDP = `nlockmgr` (nur NFSv3)
    * 4046 TCP/UDP = `status` (nur NFSv3)

## <a name="steps"></a>Schritte

1. Klicken Sie auf das Blatt **Volumes**, und wählen Sie dann das Volume aus, für das die Einbindung erfolgen soll. 
2. Klicken Sie für das ausgewählte Volume auf **Einbindungsanweisungen**, und führen Sie die Schritte aus, um das Volume einzubinden. 

    ![Einbindungsanweisungen: NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Einbindungsanweisungen: SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * Wenn Sie ein NFS-Volume einbinden, stellen Sie sicher, dass Sie die Option `vers` im `mount`-Befehl verwenden, um die NFS-Protokollversion anzugeben, die dem Volume entspricht, das Sie einbinden möchten. 
    * Wenn Sie mit NFSv4.1 arbeiten, binden Sie Ihr Dateisystem mit dem folgenden Befehl ein: `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  
        > [!NOTE]
        > Wenn Sie NFSv4.1 verwenden und Ihr Anwendungsfall die Nutzung von VMs mit demselben Hostnamen umfasst (z. B. bei einem DR-Test), sehen Sie sich den Abschnitt [Konfigurieren von zwei VMs mit demselben Hostnamen für den Zugriff auf NFSv4.1-Volumes](configure-nfs-clients.md#configure-two-vms-with-the-same-hostname-to-access-nfsv41-volumes) an.

3. Wenn Sie möchten, dass ein NFS-Volume automatisch bereitgestellt wird, wenn eine Azure-VM gestartet oder neu gestartet wird, fügen Sie der Datei `/etc/fstab` auf dem Host einen Eintrag hinzu. 

    Beispiel: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` ist die IP-Adresse des Azure NetApp Files-Volumes, die Sie auf dem Eigenschaftenblatt für das Volume finden.
    * `$FILEPATH` ist der Exportpfad zum Azure NetApp Files-Volume.
    * `$MOUNTPOINT` ist das auf dem Linux-Host erstellte Verzeichnis, das zum Einbinden des NFS-Exports verwendet wird.

4. Wenn Sie das Volume mithilfe von NFS in Windows einbinden möchten:

    a. Binden Sie das Volume zuerst auf einer Unix- oder Linux-VM ein.  
    b. Führen Sie einen der Befehle `chmod 777` oder `chmod 775` für das Volume aus.  
    c. Binden Sie das Volume über den NFS-Client unter Windows ein.
    
5. Wenn Sie ein NFS-Kerberos-Volume einbinden möchten, finden Sie weitere Informationen unter [Konfigurieren der NFSv4.1-Kerberos-Verschlüsselung](configure-kerberos-encryption.md). 

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren der NFSv4.1-Fehlerdomäne für Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Häufig gestellte Fragen zu NFS](faq-nfs.md)
* [Network File System (Übersicht)](/windows-server/storage/nfs/nfs-overview)
* [Einbinden eines NFS-Kerberos-Volumes](configure-kerberos-encryption.md#kerberos_mount)
* [Konfigurieren von zwei VMs mit demselben Hostnamen für den Zugriff auf NFSv4.1-Volumes](configure-nfs-clients.md#configure-two-vms-with-the-same-hostname-to-access-nfsv41-volumes) 
