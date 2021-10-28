---
title: Erstellen eines Volumes mit Dual-Protokoll für Azure NetApp Files| Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie ein Volume erstellen, das ein Dual-Protokoll (NFSv3 und SMB oder NFSv4.1 und SMB) mit Unterstützung der LDAP-Benutzerzuordnung verwendet.
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
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: 266ff9dd24ac01347f359ea44f46e7950f8b432f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252476"
---
# <a name="create-a-dual-protocol-volume-for-azure-netapp-files"></a>Erstellen eines Volumes mit dualem Protokoll für Azure NetApp Files

Azure NetApp Files unterstützt das Erstellen von Volumes unter Verwendung von NFS (NFSv3 oder NFSv4.1), SMB3 oder einem Dual-Protokoll (NFSv3 und SMB oder NFSv4.1 und SMB). In diesem Artikel wird beschrieben, wie Sie ein Volume erstellen, das ein Dual-Protokoll mit Unterstützung der LDAP-Benutzerzuordnung verwendet. 

Informationen zum Erstellen von NFS-Volumes finden Sie unter [Erstellen eines NFS-Volumes für Azure NetApp Files](azure-netapp-files-create-volumes.md). Informationen zum Erstellen von SMB-Volumes finden Sie unter [Erstellen eines SMB-Volumes für Azure NetApp Files](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Voraussetzungen 

* Sie müssen bereits einen Kapazitätspool erstellt haben.  
    Informationen hierzu finden Sie unter [Einrichten eines Kapazitätspools](azure-netapp-files-set-up-capacity-pool.md).   
* Ein Subnetz muss an Azure NetApp Files delegiert werden.  
    Siehe [Delegieren eines Subnetzes an Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Überlegungen

* Achten Sie darauf, die [Anforderungen für Active Directory-Verbindungen](create-active-directory-connections.md#requirements-for-active-directory-connections) zu erfüllen. 
* Erstellen Sie das Konto `pcuser` in Ihrer Active Directory-Instanz (AD), und stellen Sie sicher, dass das Konto aktiviert ist. Dieses Konto dient als Standardbenutzer. Es wird zum Zuordnen von UNIX-Benutzern verwendet, damit diese mit dem Stil „NTFS-Sicherheit“ auf ein mit zwei Protokollen konfiguriertes Volume zugreifen können. Das Konto `pcuser` wird nur verwendet, wenn kein Benutzer in AD vorhanden ist. Wenn ein Benutzer in AD über ein Konto mit festgelegten POSIX-Attributen verfügt, wird dieses Konto für die Authentifizierung verwendet, und es wird nicht dem Konto `pcuser` zugeordnet. 
* Erstellen Sie eine Reverse-Lookup-Zone auf dem DNS-Server, und fügen Sie dann einen Zeigereintrag (PTR) des AD-Hostcomputers in dieser Reverse-Lookup-Zone hinzu. Andernfalls kann das Volume mit dualem Protokoll nicht erstellt werden.
* Die Option **Lokale NFS-Benutzer mit LDAP zulassen** für Active Directory-Verbindungen dient dazu, lokalen Benutzern gelegentlichen und temporären Zugriff zu bieten. Wenn diese Option aktiviert ist, funktionieren Benutzerauthentifizierung und -suche vom LDAP-Server nicht mehr. Daher sollten Sie diese Option für Active Directory-Verbindungen *deaktivieren*, außer wenn ein lokaler Benutzer auf LDAP-fähige Volumes zugreifen muss. In diesem Fall sollten Sie diese Option deaktivieren, sobald der lokale Benutzer keinen Zugriff mehr auf das Volume benötigt. Informationen zum Verwalten lokaler Benutzer finden Sie unter [Lokalen NFS-Benutzern mit LDAP den Zugriff auf ein Doppelprotokollvolume gestatten](#allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume).
* Stellen Sie sicher, dass der NFS-Client auf dem neuesten Stand ist, und führen Sie die neuesten Updates für das Betriebssystem aus.
* Volumes mit zwei Protokollen unterstützen sowohl Active Directory Domain Services (AD DS) als auch Azure Active Directory Domain Services (AAD DS). 
* Volumes mit zwei Protokollen unterstützen die Verwendung von LDAP über TLS mit AAD DS nicht. Weitere Informationen finden Sie unter [Überlegungen zu LDAP über TLS](configure-ldap-over-tls.md#considerations).
* Für ein Volume mit Dual-Protokoll kann als NFS-Version entweder NFSv3 oder NFSv4.1 verwendet werden. Es gelten die folgenden Bedingungen:
    * Das duale Protokoll unterstützt die erweiterten Attribute `set/get` von Windows-ACLs von NFS-Clients nicht.
    * NFS-Clients können keine Berechtigungen für den NTFS-Sicherheitsstil ändern, und Windows-Clients können keine Berechtigungen für Doppelprotokollvolumes im UNIX-Format ändern.   

        In der folgenden Tabelle werden die Sicherheitsstile und deren Auswirkungen beschrieben:  
        
        | Sicherheitsstil    | Clients, die Berechtigungen ändern können   | Berechtigungen, die von Clients verwendet werden können  | Resultierender effektiver Sicherheitsstil    | Clients, die auf Dateien zugreifen können     |
        |-  |-  |-  |-  |-  |
        | `Unix`    | NFS   | NFSv3- oder NFSv4.1-Modusbits    | UNIX  | NFS und Windows   |
        | `Ntfs`    | Windows   | NTFS-ACLs     | NTFS  |NFS und Windows|

    * Die Richtung, in der die Namenszuordnung erfolgt (Windows zu UNIX oder UNIX zu Windows), hängt davon ab, welches Protokoll verwendet wird und welcher Sicherheitsstil auf ein Volume angewendet wird. Ein Windows-Client erfordert immer eine Windows-zu-UNIX-Namenszuordnung. Ob ein Benutzer zur Überprüfung von Berechtigungen zugewiesen ist, hängt vom Sicherheitsstil ab. Umgekehrt muss ein NFS-Client nur dann eine UNIX-zu-Windows-Namenszuordnung verwenden, wenn der NTFS-Sicherheitsstil verwendet wird. 

        Die folgende Tabelle beschreibt die Namenszuordnungen und Sicherheitsstile:  
    
        |     Protokoll          |     Sicherheitsstil          |     Richtung der Namenszuordnung          |     Angewendete Berechtigungen          |
        |-|-|-|-|
        |  SMB  |  `Unix`  |  Windows zu UNIX  |  UNIX (Modusbits oder NFSv4.x-ACLs)  |
        |  SMB  |  `Ntfs`  |  Windows zu UNIX  |  NTFS-ACLs (basierend auf der Windows-SID, die auf die Freigabe zugreift)  |
        |  NFSv3  |  `Unix`  |  Keine  |  UNIX (Modusbits oder NFSv4.x-ACLs) <br><br>  Beachten Sie, dass NFSv4.x-ACLs mit einem NFSv4.x-Administratorclient angewendet und von NFSv3-Clients berücksichtigt werden können.  |
        |  NFS  |  `Ntfs`  |  UNIX zu Windows  |  NTFS-ACLs (basierend auf der zugeordneten Windows-Benutzer-SID)  |

* Wenn Sie über große Topologien verfügen und den `Unix`-Sicherheitsstil mit einem Doppelprotokollvolume oder LDAP mit erweiterten Gruppen verwenden, kann Azure NetApp Files möglicherweise nicht auf alle Server in Ihren Topologien zugreifen.  Wenn diese Situation eintritt, wenden Sie sich wegen Unterstützung an Ihr Kontoteam.  <!-- NFSAAS-15123 --> 
* Sie benötigen zum Erstellen eines Volumes mit dualem Protokoll kein Serverzertifikat von einer Stammzertifizierungsstelle. Dies ist nur erforderlich, wenn LDAP über TLS aktiviert ist.

## <a name="create-a-dual-protocol-volume"></a>Erstellen eines Dual-Protokoll-Volumes

1.  Klicken Sie auf dem Blatt „Kapazitätspools“ auf das Blatt **Volumes**. Klicken Sie auf **+ Volume hinzufügen**, um ein Volume zu erstellen. 

    ![Navigieren zu Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  Klicken Sie im Fenster „Volume erstellen“ auf **Erstellen**, und geben Sie auf der Registerkarte „Grundlagen“ Informationen in den folgenden Feldern an:   
    * **Volumename**      
        Geben Sie den Namen für das Volume an, das Sie erstellen möchten.   

        Ein Volumename muss innerhalb der einzelnen Kapazitätspools eindeutig sein. Er muss mindestens drei Zeichen lang sein. Der Name muss mit einem Buchstaben beginnen. Er darf nur Buchstaben, Ziffern, Unterstriche („_“), und Bindestriche („-“) enthalten.  

        Sie können `default` oder `bin` nicht als Volumename verwenden.

    * **Kapazitätspool**  
        Geben Sie den Kapazitätspool an, in dem das Volume erstellt werden soll.

    * **Kontingent**  
        Geben Sie die Menge an logischem Speicherplatz an, die dem Volume zugewiesen wird.  

        Das Feld **Verfügbares Kontingent** zeigt den ungenutzten Speicherplatz im ausgewählten Kapazitätspool an, den Sie beim Erstellen eines neuen Volumes verwenden können. Die Größe des neuen Volumes darf das verfügbare Kontingent nicht überschreiten.  

    * **Durchsatz (MiB/s)**    
        Wenn das Volume in einem manuellen QoS-Kapazitätspool erstellt wird, geben Sie den für das Volume gewünschten Durchsatz an.   

        Wenn das Volume in einem automatischen QoS-Kapazitätspool erstellt wird, lautet der in diesem Feld angezeigte Wert (Kontingent x Serviceleveldurchsatz).   

    * **Virtuelles Netzwerk**  
        Geben Sie das virtuelle Azure-Netzwerk (VNET) an, von dem aus Sie auf das Volume zugreifen möchten.  

        Das von Ihnen angegebene VNET muss über ein an Azure NetApp Files delegiertes Subnetz verfügen. Auf den Azure NetApp Files-Dienst kann nur vom gleichen VNET aus oder per VNET-Peering von einem VNET aus zugegriffen werden, das sich in der gleichen Region befindet wie das Volume. Sie können auch über ExpressRoute von Ihrem lokalen Netzwerk aus auf das Volume zugreifen.   

    * **Subnetz**  
        Geben Sie das Subnetz an, das Sie für das Volume verwenden möchten.  
        Das von Ihnen angegebene Subnetz muss an Azure NetApp Files delegiert werden. 
        
        Wenn Sie kein Subnetz delegiert haben, klicken Sie auf der Seite „Volume erstellen“ auf **Neu erstellen**. Geben Sie dann auf der Seite „Subnetz erstellen“ die Subnetzinformationen an, und wählen Sie **Microsoft.NetApp/volumes** aus, um das Subnetz für Azure NetApp Files zu delegieren. In jedem VNET kann nur ein Subnetz an Azure NetApp Files delegiert werden.   
 
        ![Erstellen eines Volumes](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Erstellen eines Subnetzes](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * **Netzwerkfunktionen**  
        In unterstützten Regionen können Sie angeben, ob Sie für das Volume **Basic**- oder **Standard**-Netzwerkfeatures verwenden möchten. Ausführlichere Informationen finden Sie unter [Konfigurieren von Netzwerkfeatures für ein Volume](configure-network-features.md) und [Richtlinien für die Azure NetApp Files-Netzwerkplanung](azure-netapp-files-network-topologies.md).

    * Wenn Sie eine vorhandene Momentaufnahmerichtlinie auf das Volume anwenden möchten, klicken Sie auf **Abschnitt „Erweitert“ anzeigen**, um den Bereich zu erweitern, geben Sie an, ob Sie den Momentaufnahmepfad ausblenden möchten, und wählen Sie im Pulldownmenü eine Momentaufnahmerichtlinie aus. 

        Informationen zum Erstellen einer Momentaufnahmenrichtlinie finden Sie unter [Verwalten von Momentaufnahmenrichtlinien](snapshots-manage-policy.md).

        ![Abschnitt „Erweitert“ anzeigen](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Klicken Sie auf die Registerkarte **Protokoll**, und führen Sie anschließend die folgenden Aktionen aus:  
    * Wählen Sie **Dual-Protokoll** als Protokolltyp für das Volume aus.   

    * Geben Sie die zu verwendende **Active Directory**-Verbindung an.

    * Geben Sie einen eindeutigen **Volumepfad** an. Dieser Pfad wird verwendet, wenn Sie Einbindungsziele erstellen. Für den Pfad gelten die folgenden Anforderungen:  

        - Er muss innerhalb jedes Subnetzes in der Region eindeutig sein. 
        - Er muss mit einem Buchstaben beginnen.
        - Er darf nur Buchstaben, Ziffern oder Gedankenstriche (`-`) enthalten. 
        - Er darf höchstens 80 Zeichen lang sein.

    * Geben Sie die zu verwendenden **Versionen** für das Dual-Protokoll an: **NFSv4.1 und SMB** oder **NFSv3 und SMB**.

        Das Feature zur Verwendung des Dual-Protokolls **NFSv4.1 und SMB** befindet sich derzeit in der Vorschauphase. Wenn Sie dieses Feature zum ersten Mal verwenden, müssen Sie es registrieren:  

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFDualProtocolNFSv4AndSMB
        ```

        Überprüfen Sie den Status der Funktionsregistrierung: 

        > [!NOTE]
        > Der **RegistrationState** kann bis zu 60 Minuten lang den Wert `Registering` aufweisen, bevor er sich in `Registered` ändert. Warten Sie, bis der Status **Registriert** lautet, bevor Sie fortfahren.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFDualProtocolNFSv4AndSMB
        ```
        Sie können auch die [Azure CLI-Befehle](/cli/azure/feature) `az feature register` und `az feature show` verwenden, um das Feature zu registrieren und den Registrierungsstatus anzuzeigen. 

    * Geben Sie den zu verwendenden **Sicherheitsstil** an: NTFS (Standard) oder UNIX.

    * Wenn Sie die SMB3-Protokollverschlüsselung für das Volume mit dualem Protokoll aktivieren möchten, wählen Sie **SMB3-Protokollverschlüsselung aktivieren** aus.   

        Durch dieses Feature wird die Verschlüsselung nur für In-Flight-SMB3-Daten aktiviert. NfSv3-In-Flight-Daten werden nicht verschlüsselt. SMB-Clients ohne Verwendung der SMB3-Verschlüsselung können nicht auf dieses Volume zugreifen. Ruhende Daten werden unabhängig von dieser Einstellung verschlüsselt. Weitere Informationen finden Sie unter [SMB-Verschlüsselung](azure-netapp-files-smb-performance.md#smb-encryption). 

        Das Feature **SMB3-Protokollverschlüsselung** befindet sich zurzeit in der Vorschauphase. Wenn Sie dieses Feature zum ersten Mal verwenden, registrieren Sie es vor der Verwendung: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```

        Überprüfen Sie den Status der Funktionsregistrierung: 

        > [!NOTE]
        > Der **RegistrationState** kann für bis zu 60 Minuten den Status `Registering` aufweisen, bevor der Wechsel in `Registered` erfolgt. Warten Sie, bis der Status `Registered` lautet, bevor Sie fortfahren.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```
        
        Sie können auch die [Azure CLI-Befehle](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` und `az feature show` verwenden, um das Feature zu registrieren und den Registrierungsstatus anzuzeigen.  

    * Wenn Sie NFSv4.1 und SMB als Versionen für das Volume mit Dual-Protokoll ausgewählt haben, geben Sie an, ob Sie die **Kerberos**-Verschlüsselung für das Volume aktivieren möchten.

        Für Kerberos sind zusätzliche Konfigurationen erforderlich. Befolgen Sie die Anweisungen unter [Konfigurieren der NFSv4.1-Kerberos-Verschlüsselung](configure-kerberos-encryption.md).

    *  Passen Sie nach Bedarf die **Unix-Berechtigungen** an, um Änderungsberechtigungen für den Bereitstellungspfad anzugeben. Die Einstellung gilt nicht für die Dateien unter dem Bereitstellungspfad. Die Standardeinstellung ist `0770`. Diese Standardeinstellung gewährt dem Besitzer und der Gruppe Lese-, Schreib- und Ausführungsberechtigungen, anderen Benutzern werden jedoch keine Berechtigungen gewährt.     
        Für das Festlegen von **Unix-Berechtigungen** gelten Registrierungsanforderungen und -überlegungen. Befolgen Sie die Anweisungen unter [Konfigurieren von Unix-Berechtigungen und des „Besitz ändern“-Modus](configure-unix-permissions-change-ownership-mode.md).  

    * Optional können Sie [die Exportrichtlinie für das Volume konfigurieren](azure-netapp-files-configure-export-policy.md).

    ![Angeben eines dualen Protokolls](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Klicken Sie auf **Bewerten + erstellen**, um die Volumedetails zu überprüfen. Klicken Sie dann auf **Erstellen**, um das Volume zu erstellen.

    Das von Ihnen erstellte Volume wird auf der Seite „Volumes“ angezeigt. 
 
    Ein Volume erbt Abonnement-, Ressourcengruppen- und Standortattribute aus dem Kapazitätspool. Den Volumebereitstellungsstatus können Sie auf der Benachrichtigungsregisterkarte überwachen.

## <a name="allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume"></a>Lokalen NFS-Benutzern mit LDAP den Zugriff auf ein Doppelprotokollvolume gestatten 

Mit der Option **Lokale NFS-Benutzer mit LDAP zulassen** für Active Directory-Verbindungen können lokale NFS-Clientbenutzer, die auf dem Windows-LDAP-Server nicht vorhanden sind, auf ein Volume mit zwei Protokollen zugreifen, für das LDAP mit aktivierten erweiterten Gruppen aktiviert ist. 

> [!NOTE] 
> Bevor Sie diese Option aktivieren, sollten Sie sich diese [Überlegungen](#considerations) ansehen.   
> Die Option **Lokale NFS-Benutzer mit LDAP zulassen** ist Teil des Features **LDAP mit erweiterten Gruppen** und erfordert eine Registrierung. Weitere Informationen finden Sie unter [Konfigurieren von ADDS LDAP mit erweiterten Gruppen für NFS-Volume-Zugriff](configure-ldap-extended-groups.md) .

1. Klicken Sie auf **Active Directory-Verbindungen**.  Klicken Sie in einer vorhandenen Active Directory-Verbindung auf das Kontextmenü (die drei Punkte `…`), und wählen Sie **Bearbeiten** aus.  

2. Wählen Sie im angezeigten Fenster **Active Directory-Einstellungen bearbeiten** die Option **lokale NFS-Benutzer mit LDAP zulassen** aus.  

    ![Screenshot, der die Option „Lokale NFS-Benutzer mit LDAP zulassen“ zeigt](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  


## <a name="manage-ldap-posix-attributes"></a>Verwalten von LDAP-POSIX-Attributen

Sie können POSIX-Attribute wie z. B. UID, Basisverzeichnis und andere Werte über das MMC-Snap-In „Active Directory-Benutzer und -Computer“ verwalten.  Im folgenden Beispiel ist der Active Directory-Attribut-Editor dargestellt: 

![Active Directory-Attribut-Editor](../media/azure-netapp-files/active-directory-attribute-editor.png) 

Sie müssen die folgenden Attribute für LDAP-Benutzer und LDAP-Gruppen festlegen: 
* Erforderliche Attribute für LDAP-Benutzer:   
    `uid: Alice`,  
    `uidNumber: 139`,  
    `gidNumber: 555`,  
    `objectClass: user, posixAccount`
* Erforderliche Attribute für LDAP-Gruppen:   
    `objectClass: group, posixGroup`,  
    `gidNumber: 555`
* Alle Benutzer und Gruppen müssen über eindeutige Werte für `uidNumber` und `gidNumber` verfügen. 

Die für `objectClass` angegebenen Werte sind separate Einträge. Beispielsweise hätte im mehrwertigen String Editor `objectClass` getrennte Werte (`user` und `posixAccount`), für Benutzer von LDAP wie folgt bestimmt:   

![Screenshot: Editor für mehrwertige Zeichenfolgen, der mehrere für objectClass angegebene Werte zeigt.](../media/azure-netapp-files/multi-valued-string-editor.png) 

Azure Active Directory Domain Services (AADDS) ermöglicht es Ihnen nicht, POSIX-Attribute für Benutzer und Gruppen zu ändern, die in der Organisationseinheit AADDC-Benutzer erstellt wurden. Zur Umgehung können Sie eine benutzerdefinierte Organisationseinheit einrichten und Benutzer und Gruppen in dieser benutzerdefinierten Organisationseinheit erstellen.

Wenn Sie die Benutzer und Gruppen in Ihrem Azure AD-Mandanten mit Benutzern und Gruppen in der Organisationseinheit für AADDC-Benutzer synchronisieren, können Sie Benutzer und Gruppen nicht in eine benutzerdefinierte Organisationseinheit verschieben. Benutzer und Gruppen, die in der benutzerdefinierten Organisationseinheit erstellt wurden, werden nicht mit Ihrem AD-Mandanten synchronisiert. Weitere Informationen finden Sie unter [Überlegungen und Einschränkungen zu benutzerdefinierten Organisationseinheiten](../active-directory-domain-services/create-ou.md#custom-ou-considerations-and-limitations).

### <a name="access-active-directory-attribute-editor"></a>Zugreifen auf den Active Directory-Attribut-Editor 

Auf einem Windows-System können Sie wie folgt auf den Active Directory-Attribut-Editor zugreifen:  

1. Klicken Sie auf **Start**, navigieren Sie zu **Windows-Verwaltungsprogramme**, und klicken Sie auf **Active Directory-Benutzer und -Computer,** um das Fenster „Active Directory-Benutzer und -Computer“ zu öffnen.  
2.  Klicken Sie auf den Domänennamen, den Sie anzeigen möchten, und erweitern Sie dann den Inhalt.  
3.  Um den erweiterten Attribut-Editor anzuzeigen, aktivieren Sie die Option **Erweiterte Features** im Menü **Ansicht** des Fensters „Active Directory-Benutzer und -Computer“.   
    ![Screenshot: Zugriff auf das Menü „Erweiterte Features“ des Attribut-Editors.](../media/azure-netapp-files/attribute-editor-advanced-features.png) 
4. Doppelklicken Sie im linken Bereich auf **Benutzer**, um die Liste der Benutzer anzuzeigen.
5. Doppelklicken Sie auf einen bestimmten Benutzer, um die zugehörige Registerkarte **Attribut-Editor** anzuzeigen.
 
## <a name="configure-the-nfs-client"></a>Konfigurieren des NFS-Clients 

Befolgen Sie die Anweisungen unter [Konfigurieren eines NFS-Clients für Azure NetApp Files](configure-nfs-clients.md), um den NFS-Client zu konfigurieren.  

## <a name="next-steps"></a>Nächste Schritte  

* [Konfigurieren der NFSv4.1-Kerberos-Verschlüsselung](configure-kerberos-encryption.md)
* [Konfigurieren eines NFS-Clients für Azure NetApp Files](configure-nfs-clients.md)
* [Konfigurieren von Unix-Berechtigungen und des „Besitz ändern“-Modus](configure-unix-permissions-change-ownership-mode.md) 
* [Konfigurieren von ADDS LDAP über TLS für Azure NetApp Files](configure-ldap-over-tls.md)
* [Konfigurieren Sie ADDS LDAP mit erweiterten Gruppen für den NFS-Volume-Zugriff](configure-ldap-extended-groups.md)
* [Behandeln von Volumefehlern für Azure NetApp Files](troubleshoot-volumes.md)
