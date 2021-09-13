---
title: Konfigurieren der NFSv4.1-Kerberos-Verschlüsselung für Azure NetApp Files | Microsoft-Dokumentation
description: In diesem Artikel werden die Konfiguration der NFSv4.1-Kerberos-Verschlüsselung für Azure NetApp Files und die daraus resultierenden Auswirkung auf die Leistung beschrieben.
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
ms.date: 07/15/2021
ms.author: b-juche
ms.openlocfilehash: a12df50ac6fe2e1dc2bf793316538a90ba0f901d
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114361333"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>Konfigurieren der NFSv4.1-Kerberos-Verschlüsselung für Azure NetApp Files

Azure NetApp Files unterstützt die Verschlüsselung des NFS-Clients in Kerberos-Modi (krb5, krb5i und krb5p) mit AES-256-Verschlüsselung. In diesem Artikel werden die erforderlichen Konfigurationen für die Verwendung eines NFSv4.1-Volumes mit Kerberos-Verschlüsselung beschrieben.

## <a name="requirements"></a>Requirements (Anforderungen)

Für die Verschlüsselung des NFSv4.1-Clients gelten die folgenden Anforderungen: 

* Verbindung mit Active Directory Domain Services (AD DS) oder Azure Active Directory Domain Services (AADDS) zur Vereinfachung der Kerberos-Ticketausstellung 
* Erstellung von DNS-A/PTR-Einträgen für die IP-Adressen des Clients sowie des NFS-Servers in Azure NetApp Files
* Linux-Client: Dieser Artikel enthält Informationen zu RHEL- und Ubuntu-Clients.  Für andere Clients gelten ähnliche Konfigurationsschritte. 
* NTP-Serverzugriff: Sie können einen der häufig verwendeten Active Directory-Domänencontroller (AD DC) verwenden.
* Stellen Sie sicher, dass Benutzerprinzipalnamen für Benutzerkonten *nicht* mit einem `$`-Symbol enden (z. B. „user$@REALM.COM“). <!-- Not using 'contoso.com' in this example; per Mark, A customers REALM namespace may be different from their AD domain name space. -->   
    Bei [von Gruppen verwalteten Dienstkonten](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts) (gMSA) müssen Sie das nachstehende `$` vom Benutzerprinzipalnamen entfernen, bevor das Konto mit dem Azure NetApp Files-Kerberos-Feature genutzt werden kann.


## <a name="create-an-nfs-kerberos-volume"></a>Erstellen eines NFS-Kerberos-Volumes

1.  Führen Sie die unter [Erstellen eines NFS-Volumes für Azure NetApp Files](azure-netapp-files-create-volumes.md) angegebenen Schritte aus, um das NFSv4.1-Volume zu erstellen.   

    Legen Sie auf der Seite „Volume erstellen“ die NFS-Version auf **NFSv4.1** und Kerberos auf **Aktiviert** fest.

    > [!IMPORTANT] 
    > Nachdem das Volume erstellt wurde, können Sie die Auswahl der Kerberos-Aktivierung nicht mehr ändern.

    ![Erstellen des NFSv4.1-Kerberos-Volumes](../media/azure-netapp-files/create-kerberos-volume.png)  

2. Wählen Sie **Exportrichtlinie** aus, um die gewünschte Zugriffsebene und Sicherheitsoption (Kerberos 5, Kerberos 5i oder Kerberos 5p) für das Volume festzulegen.   

    Informationen zu den Leistungsauswirkungen von Kerberos finden Sie unter [Leistungsauswirkungen von Kerberos auf NFSv4.1](#kerberos_performance).  

    Sie können außerdem die Kerberos-Sicherheitsmethoden für das Volume ändern, indem Sie im Azure NetApp Files-Navigationsbereich auf „Exportrichtlinie“ klicken.

3.  Klicken Sie zum Erstellen des NFSv4.1-Volumes auf **Überprüfen + erstellen**.

## <a name="configure-the-azure-portal"></a>Konfigurieren des Azure-Portals 

1.  Befolgen Sie die Anweisungen unter [Erstellen einer Active Directory-Verbindung](create-active-directory-connections.md).  

    Kerberos erfordert, dass Sie mindestens ein Computerkonto in Active Directory erstellen. Die angegebenen Kontoinformationen werden für die Erstellung der Konten für SMB- *sowie* NFSv4.1-Kerberos-Volumes verwendet. Dieses Computerkonto wird bei der Volumeerstellung automatisch erstellt.

2.  Geben Sie unter **Kerberos Realm** (Kerberos-Bereich) den **AD-Servernamen** und die **KDC-IP-Adresse** ein.

    AD-Server und KDC-IP können derselbe Server sein. Diese Informationen werden verwendet, um das in Azure NetApp Files verwendete SPN-Computerkonto zu erstellen. Nachdem das Computerkonto erstellt wurde, werden in Azure NetApp Files DNS-Servereinträge verwendet, um bei Bedarf weitere KDC-Server zu suchen. 

    ![Kerberos-Bereich](../media/azure-netapp-files/kerberos-realm.png)
 
3.  Klicken Sie auf **Beitreten**, um die Konfiguration zu speichern.

## <a name="configure-active-directory-connection"></a>Konfigurieren der Active Directory-Verbindung 

Bei der Konfiguration von NFSv4.1-Kerberos werden zwei Computerkonten in Active Directory erstellt:
* Ein Computerkonto für SMB-Freigaben
* Ein Computerkonto für NFSv4.1: Dieses Konto können Sie am Präfix `NFS-` erkennen. 

Legen Sie nach dem Erstellen des ersten NFSv4.1-Kerberos-Volumes den Verschlüsselungstyp für das Computerkonto durch Ausführen des folgenden PowerShell-Befehls fest:

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>Konfigurieren des NFS-Clients 

Befolgen Sie die Anweisungen unter [Konfigurieren eines NFS-Clients für Azure NetApp Files](configure-nfs-clients.md), um den NFS-Client zu konfigurieren.  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>Einbinden des NFS-Kerberos-Volumes

1. Wählen Sie auf der Seite **Volumes** das NFS-Volume aus, das Sie einbinden möchten.

2. Wählen Sie **Einbindungsanweisungen** in dem Volume aus, um die Anweisungen anzuzeigen.

    Beispiel: 

    ![Einstellungsanweisungen für Kerberos-Volumes](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. Erstellen Sie das Verzeichnis (Bereitstellungspunkt) für das neue Volume.  

4. Legen Sie den Standardverschlüsselungstyp für das Computerkonto auf AES 256 fest:  
    `Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * Sie müssen diesen Befehl für jedes Computerkonto nur einmal ausführen.
    * Sie können diesen Befehl über einen Domänencontroller oder über einen PC ausführen, auf dem [RSAT](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) installiert ist. 
    * Die Variable `$NFSCOMPUTERACCOUNT` entspricht dem beim Bereitstellen des Kerberos-Volumes in Active Directory erstellten Computerkonto. Dabei handelt es sich um das Konto mit dem Präfix `NFS-`. 
    * Die Variable `$ANFSERVICEACCOUNT` entspricht einem Active Directory-Benutzerkonto ohne Berechtigungen mit delegierter Steuerung der Organisationseinheit, in der das Computerkonto erstellt wurde. 

5. Binden Sie das Volume auf dem Host ein: 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * Die Variable `$ANFEXPORT` entspricht dem Pfad `host:/export` aus den Einbindungsanweisungen.
    * Die Variable `$ANFMOUNTPOINT` entspricht dem vom Benutzer erstellten Ordner auf dem Linux-Host.

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>Leistungsauswirkungen von Kerberos auf NFSv4.1 

Sie sollten sich mit den für NFSv4.1-Volumes verfügbaren Sicherheitsoptionen, den getesteten Leistungsvektoren und den erwarteten Leistungseinbußen von Kerberos vertraut machen. Weitere Informationen finden Sie unter [Auswirkungen von Kerberos auf die Leistung von NFSv4.1-Volumes](performance-impact-kerberos.md).  

## <a name="next-steps"></a>Nächste Schritte  

* [Auswirkungen von Kerberos auf die Leistung von NFSv4.1-Volumes](performance-impact-kerberos.md)
* [Problembehandlung für NFSv4.1-Kerberos-Volumes](troubleshoot-nfsv41-kerberos-volumes.md)
* [Häufig gestellte Fragen zu Azure NetApp Files](azure-netapp-files-faqs.md)
* [Erstellen eines NFS-Volumes für Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Erstellen einer Active Directory-Verbindung](create-active-directory-connections.md)
* [Konfigurieren eines NFS-Clients für Azure NetApp Files](configure-nfs-clients.md) 
