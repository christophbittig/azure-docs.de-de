---
title: Erstellen und Verwalten von Active Directory-Verbindungen für Azure NetApp Files | Microsoft-Dokumentation
description: In diesem Artikel wird das Erstellen und Verwalten von Active Directory-Verbindungen für Azure NetApp Files erläutert.
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
ms.date: 11/02/2021
ms.author: b-juche
ms.openlocfilehash: e05850686fca42a8d21bc477e39171ff792db307
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473830"
---
# <a name="create-and-manage-active-directory-connections-for-azure-netapp-files"></a>Erstellen und Verwalten von Active Directory-Verbindungen für Azure NetApp Files

Für einige Features von Azure NetApp Files ist eine Active Directory-Verbindung erforderlich.  Sie benötigen beispielsweise eine Active Directory-Verbindung, damit Sie ein [SMB-Volume](azure-netapp-files-create-volumes-smb.md), ein [NFSv4.1-Kerberos-Volume](configure-kerberos-encryption.md) oder ein [Dual-Protokoll-Volume](create-volumes-dual-protocol.md) erstellen können.  In diesem Artikel wird das Erstellen und Verwalten von Active Directory-Verbindungen für Azure NetApp Files erläutert.

## <a name="before-you-begin"></a>Voraussetzungen  

* Sie müssen bereits einen Kapazitätspool eingerichtet haben. Informationen dazu finden Sie unter [Einrichten eines Kapazitätspools](azure-netapp-files-set-up-capacity-pool.md).   
* Ein Subnetz muss an Azure NetApp Files delegiert werden. Siehe [Delegieren eines Subnetzes an Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="requirements-and-considerations-for-active-directory-connections"></a><a name="requirements-for-active-directory-connections"></a>Anforderungen und Überlegungen zu Active Directory-Verbindungen

* Sie können nur eine Active Directory-Verbindung (AD) pro Abonnement und pro Region konfigurieren.   

    Azure NetApp Files bietet keine Unterstützung für mehrere AD-Verbindungen in einer einzelnen *Region*. Dies gilt auch dann, wenn die AD-Verbindungen zu unterschiedlichen NetApp-Konten gehören. Sie können jedoch über mehrere AD-Verbindungen in einem einzelnen Abonnement verfügen, sofern sich die AD-Verbindungen in unterschiedlichen Regionen befinden. Wenn Sie mehrere AD-Verbindungen in einer einzelnen Region benötigen, können Sie hierfür separate Abonnements verwenden.  

    Die AD-Verbindung ist nur über das NetApp-Konto sichtbar, in dem sie erstellt wird. Sie können jedoch das Shared AD-Feature (Freigegebenes AD) aktivieren, um NetApp-Konten, die sich unter demselben Abonnement und in derselben Region befinden, die Verwendung eines AD-Servers zu gestatten, der in einem der NetApp-Konten erstellt wurde. Weitere Informationen finden Sie unter [Zuordnen mehrerer NetApp-Konten im selben Abonnement und derselben Region zu einer AD-Verbindung](#shared_ad). Wenn Sie dieses Feature aktivieren, wird die AD-Verbindung in allen zu einem Abonnement und einer Region gehörenden NetApp-Konten sichtbar. 

* Es muss mit dem verwendeten Administratorkonto möglich sein, Computerkonten im angegebenen Pfad der Organisationseinheit (OU) zu erstellen.  

* Wenn Sie das Kennwort des Active Directory-Benutzerkontos ändern, das in Azure NetApp Files verwendet wird, müssen Sie das in [den Active Directory-Verbindungen](#create-an-active-directory-connection) konfigurierte Kennwort aktualisieren. Andernfalls können Sie keine neuen Volumes erstellen, und der Zugriff auf vorhandene Volumes kann abhängig von der Konfiguration ebenfalls beeinträchtigt werden.  

* Auf dem entsprechenden Windows Active Directory-Server (AD) müssen die richtigen Ports geöffnet sein.  
    Die erforderlichen Ports lauten wie folgt: 

    |     Dienst           |     Port     |     Protocol     |
    |-----------------------|--------------|------------------|
    |    AD-Webdienste    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    –       |    Echo Reply    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS-Name       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    w32time            |    123       |    UDP           |

* Die Standorttopologie für die Ziel-Active Directory Domain Services muss den Richtlinien entsprechen. Dies gilt insbesondere für das Azure-VNET, in dem Azure NetApp Files bereitgestellt wird.  

    Der Adressraum für das virtuelle Netzwerk, in dem Azure NetApp Files bereitgestellt wird, muss einem neuen oder vorhandenen Active Directory-Standort hinzugefügt werden (in dem sich ein Domänencontroller befindet, der über Azure NetApp Files erreichbar ist). 

* Die angegebenen DNS-Server müssen über das [delegierte Subnetz](./azure-netapp-files-delegate-subnet.md) von Azure NetApp Files erreichbar sein.  

    Informationen zu unterstützten Netzwerktopologien finden Sie unter [Richtlinien für die Azure NetApp Files-Netzwerkplanung](./azure-netapp-files-network-topologies.md).

    Die Netzwerksicherheitsgruppen (NSGs) und Firewalls müssen ordnungsgemäß konfigurierte Regeln aufweisen, um Active Directory- und DNS-Datenverkehrsanforderungen zuzulassen. 

* Das delegierte Azure NetApp Files-Subnetz muss in der Lage sein, alle Active Directory Domain Services-Domänencontroller (ADDS) in der Domäne zu erreichen, einschließlich aller lokalen und Remotedomänencontroller. Andernfalls kann es zu Dienstunterbrechungen kommen.  

    Wenn Sie über Domänencontroller verfügen, die über das delegierte Azure NetApp Files-Subnetz nicht erreichbar sind, können Sie während der Erstellung der Active Directory-Verbindung einen Active Directory-Standort angeben.  Azure NetApp Files muss nur mit Domänencontrollern an dem Standort kommunizieren, an dem sich der Adressraum des delegierten Azure NetApp Files-Subnetzes befindet.

    Weitere Informationen zu AD-Standorten und -Diensten finden Sie unter [Entwerfen der Standorttopologie](/windows-server/identity/ad-ds/plan/designing-the-site-topology). 
    
* Sie können AES-Verschlüsselung für die AD-Authentifizierung aktivieren, indem Sie im Fenster [Active Directory beitreten](#create-an-active-directory-connection) das Kontrollkästchen **AES-Verschlüsselung** aktivieren. Azure NetApp Files unterstützt die Verschlüsselungstypen DES, Kerberos AES 128 und Kerberos AES 256 (von der niedrigsten hin zur höchsten Sicherheitsstufe). Wenn Sie AES-Verschlüsselung aktivieren, muss für die für den Beitritt zu Active Directory verwendeten Anmeldeinformationen die höchste entsprechende Kontooption aktiviert sein, die den für Ihr Active Directory aktivierten Funktionen entspricht.    

    Wenn Ihr Active Directory beispielsweise nur über die AES-128-Funktion verfügt, müssen Sie die AES-128-Kontooption für die Anmeldeinformationen des Benutzers aktivieren. Wenn Ihr Active Directory die AES-256-Funktion aufweist, müssen Sie die AES-256-Kontooption aktivieren (die auch AES-128 unterstützt). Wenn Ihr Active Directory keine Kerberos-Verschlüsselungsfunktion besitzt, verwendet Azure NetApp Files standardmäßig DES.  

    Sie können die Kontooptionen in den Eigenschaften von „Active Directory-Benutzer und -Computer“ in der Microsoft Management Console (MMC) aktivieren:   

    ![„Active Directory-Benutzer und -Computer“ (MMC)](../media/azure-netapp-files/ad-users-computers-mmc.png)

* Azure NetApp Files unterstützt [LDAP-Signatur](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server), was eine sichere Übertragung des LDAP-Datenverkehrs zwischen dem Azure NetApp Files-Dienst und den [Active Directory-Zieldomänencontrollern](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) ermöglicht. Wenn Sie den Leitfaden von Microsoft Advisory [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) für LDAP-Signatur befolgen, sollten Sie das LDAP-Signaturfeature in Azure NetApp Files aktivieren, indem Sie das Kontrollkästchen **LDAP-Signatur** im Fenster [Active Directory beitreten](#create-an-active-directory-connection) aktivieren. 

    Die Konfiguration der [LDAP-Kanalbindung](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) hat allein keine Auswirkungen auf den Azure NetApp Files-Dienst. Wenn Sie jedoch sowohl die LDAP-Kanalbindung als auch sicheres LDAP verwenden (z. B. LDAPS oder `start_tls`), tritt bei der Erstellung des SMB-Volumes ein Fehler auf.

* Für nicht in AD integriertes DNS sollten Sie einen A/PTR-DNS-Eintrag hinzufügen, um Azure NetApp Files die Funktion mit einem „Anzeigenamen“ zu ermöglichen. 

* In der folgenden Tabelle wird die Gültigkeitsdauer (Time to Live, TTL) für den LDAP-Cache beschrieben. Sie müssen warten, bis der Cache aktualisiert wird, bevor Sie versuchen, über einen Client auf eine Datei oder ein Verzeichnis zuzugreifen. Andernfalls wird auf dem Client eine Meldung vom Typ „Zugriff oder Berechtigung verweigert“ angezeigt. 

    |     Fehlerzustand    |     Lösung    |
    |-|-|
    | Cache |  Standardzeitlimit |
    | Gruppenmitgliedschaftsliste  | 24 Stunden Gültigkeitsdauer  |
    | Unix-Gruppen  | 24 Stunden Gültigkeitsdauer, 1 Minute negative Gültigkeitsdauer  |
    | UNIX-Benutzer  | 24 Stunden Gültigkeitsdauer, 1 Minute negative Gültigkeitsdauer  |

    Caches haben einen bestimmten Timeoutzeitraum namens *Gültigkeitsdauer*. Nach Ablauf des Timeouts werden Einträge als veraltet eingestuft, damit veraltete Einträge nicht gesperrt werden. Der *negative TTL* -Wert ist der Ort, an dem die Suche fehlgeschlagen ist, um Leistungsprobleme aufgrund von LDAP-Abfragen für Objekte zu vermeiden, die möglicherweise nicht vorhanden sind.“   

## <a name="decide-which-domain-services-to-use"></a>Festlegen der zu verwendenden Domänendienste 

Azure NetApp Files unterstützt sowohl [Active Directory Domain Services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) als auch Azure Active Directory Domain Services (AADDS) für AD-Verbindungen.  Vor der Erstellung einer AD-Verbindung müssen Sie festlegen, ob ADDS oder AADDS verwendet werden soll.  

Weitere Informationen finden Sie unter [Vergleichen von selbstverwalteten Active Directory Domain Services, Azure Active Directory und verwalteten Azure Active Directory Domain Services](../active-directory-domain-services/compare-identity-solutions.md). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Sie können den Bereich Ihrer bevorzugten [Active Directory-Standorte und -Dienste](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) für Azure NetApp Files verwenden. Mit dieser Option werden Lese- und Schreibvorgänge für Active Directory Domain Services-Domänencontroller (ADDS) ermöglicht, auf die über [Azure NetApp Files](azure-netapp-files-network-topologies.md) zugegriffen werden kann. Außerdem wird verhindert, dass der Dienst mit Domänencontrollern kommuniziert, die sich nicht am angegebenen Standort für Active Directory-Standorte und -Dienste befinden. 

Um bei Verwendung von ADDS den Standortnamen zu ermitteln, können Sie sich an die administrative Gruppe Ihrer Organisation wenden, die für Active Directory Domain Services zuständig ist. Im folgenden Beispiel wird das Plug-In „Active Directory-Standorte und -Dienste“ gezeigt, in dem der Standortname angezeigt wird: 

![Active Directory-Standorte und -Dienste](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Wenn Sie eine AD-Verbindung für Azure NetApp Files konfigurieren, geben Sie den Standortnamen im Bereich für das Feld **Active Directory-Standortname** an.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Informationen zur Konfiguration und Anleitungen für Azure Active Directory Domain Services (AADDS) finden Sie in der [Dokumentation zu Azure AD Domain Services](../active-directory-domain-services/index.yml).

Weitere Überlegungen zu Azure NetApp Files im Hinblick auf AADDS: 

* Stellen Sie sicher, dass sich das VNET oder Subnetz, in dem AADDS bereitgestellt wird, in der gleichen Azure-Region wie die Azure NetApp Files-Bereitstellung befindet.
* Wenn Sie ein anderes VNET in der Region verwenden, in der Azure NetApp Files bereitgestellt wird, sollten Sie ein Peering zwischen den beiden VNETs erstellen.
* Azure NetApp Files unterstützt die Typen `user` und `resource forest`.
* Für den Synchronisierungstyp können Sie `All` oder `Scoped` auswählen.   
    Wenn Sie `Scoped` auswählen, stellen Sie sicher, dass die richtige Azure AD-Gruppe für den Zugriff auf SMB-Freigaben ausgewählt ist.  Wenn Sie unsicher sind, können Sie den Synchronisierungstyp `All` verwenden.
* Wenn Sie AADDS mit einem Dual-Protokoll-Volume verwenden, müssen Sie sich in einer benutzerdefinierten Organisationseinheit befinden, um POSIX-Attribute anwenden zu können. Details finden Sie unter [Verwalten von LDAP-POSIX-Attributen](create-volumes-dual-protocol.md#manage-ldap-posix-attributes).

Beachten Sie beim Erstellen einer Active Directory-Verbindung die folgenden Besonderheiten für AADDS:

* Im Menü „AADDS“ finden Sie Informationen zu **Primäres DNS**, **Sekundäres DNS** und **AD-DNS-Domänenname**.  
Für DNS-Server werden zwei IP-Adressen für die Konfiguration der Active Directory-Verbindung verwendet. 
* Der **Pfad der Organisationseinheit** lautet `OU=AADDC Computers`.  
Diese Einstellung wird in **Active Directory-Verbindungen** unter **NetApp-Konto** konfiguriert:

  ![Pfad der Organisationseinheit](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* Als Anmeldeinformationen kann für **Benutzername** ein beliebiger Benutzer angegeben werden, der Mitglied der Azure AD-Gruppe **Azure AD DC-Administratoren** ist.


## <a name="create-an-active-directory-connection"></a>Erstellen einer Active Directory-Verbindung

1. Klicken Sie über Ihr NetApp-Konto auf **Active Directory-Verbindungen**, und klicken Sie dann auf **Verbinden**.  

    Azure NetApp Files unterstützt nur eine Active Directory-Verbindung innerhalb derselben Region und desselben Abonnements. Wenn Active Directory bereits von einem anderen NetApp-Konto im selben Abonnement und in derselben Region konfiguriert wurde, können Sie kein anderes Active Directory aus Ihrem NetApp-Konto konfigurieren und diesem beitreten. Sie können jedoch das Shared AD-Feature (Freigegebenes AD)aktivieren, damit eine Active Directory-Konfiguration von mehreren NetApp-Konten innerhalb desselben Abonnements und derselben Region gemeinsam genutzt werden kann. Weitere Informationen finden Sie unter [Zuordnen mehrerer NetApp-Konten im selben Abonnement und derselben Region zu einer AD-Verbindung](#shared_ad).

    ![Active Directory-Verbindungen](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Geben Sie im Fenster „Active Directory beitreten“ basierend auf den zu verwendenden Domänendiensten die folgenden Informationen an:  

    Spezifische Informationen zu den verwendeten Domänendiensten finden Sie unter [Festlegen der zu verwendenden Domänendienste](#decide-which-domain-services-to-use). 

    * **Primäres DNS**  
        Das DNS, das für den Active Directory-Domänenbeitritt und SMB-Authentifizierungsvorgänge erforderlich ist 
    * **Sekundäres DNS**   
        Der sekundäre DNS-Server für das Sicherstellen redundanter Namensdienste 
    * **AD-DNS-Domänenname**  
        Dies ist der Domänenname Ihrer Active Directory Domain Services, denen Sie beitreten möchten.
    * **AD-Standortname**  
        Dies ist der Name des Standorts, auf den die Domänencontrollerermittlung beschränkt wird. Sollte dem Standortnamen in Active Directory-Standorte und -Dienste entsprechen.
    * **Präfix des SMB-Servers (Computerkonto)**  
        Dies ist das Namenspräfix für das Computerkonto in Active Directory, das Azure NetApp Files für die Erstellung von neuen Konten verwendet.

        Wenn beispielsweise in Ihrer Organisation der Benennungsstandard für Dateiserver „NAS-01“, „NAS-02“ … „NAS-045“ verwendet wird, geben Sie „NAS“ als Präfix ein. 

        Der Dienst erstellt dann bei Bedarf zusätzliche Computerkonten in Active Directory.

        > [!IMPORTANT] 
        > Das Umbenennen des SMB-Serverpräfixes nach dem Erstellen der Active Directory-Verbindung sorgt für Unterbrechungen. Sie müssen vorhandene SMB-Freigaben nach dem Umbenennen des SMB-Serverpräfixes erneut einbinden.

    * **Pfad der Organisationseinheit**  
        Dies ist der LDAP-Pfad für die Organisationseinheit, in der Computerkonten für SMB-Server erstellt werden. Das bedeutet, Organisationseinheit = zweite Ebene, Organisationseinheit = erste Ebene. 

        Bei Verwendung von Azure NetApp Files mit Azure Active Directory Domain Services lautet der Pfad der Organisationseinheit `OU=AADDC Computers`, wenn Sie Active Directory für Ihr NetApp-Konto konfigurieren.

        ![Beitreten zu Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **AES-Verschlüsselung**   
        Aktivieren Sie dieses Kontrollkästchen, wenn Sie die AES-Verschlüsselung für die AD-Authentifizierung aktivieren möchten, oder wenn Sie [Verschlüsselung für SMB-Volumes](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) benötigen.   
        
        Informationen zu Anforderungen finden Sie unter [Anforderungen für Active Directory-Verbindungen](#requirements-for-active-directory-connections).  
  
        ![Active Directory: AES-Verschlüsselung](../media/azure-netapp-files/active-directory-aes-encryption.png)

        Die Funktion **AES-Verschlüsselung** befindet sich zurzeit in der Vorschauphase. Wenn Sie dieses Feature zum ersten Mal verwenden, registrieren Sie es vor der Verwendung: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        Überprüfen Sie den Status der Funktionsregistrierung: 

        > [!NOTE]
        > Der **RegistrationState** kann für bis zu 60 Minuten den Status `Registering` aufweisen, bevor der Wechsel in `Registered` erfolgt. Warten Sie, bis der Status `Registered` lautet, bevor Sie fortfahren.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        Sie können auch die [Azure CLI-Befehle](/cli/azure/feature) `az feature register` und `az feature show` verwenden, um das Feature zu registrieren und den Registrierungsstatus anzuzeigen. 

    * **LDAP-Signatur**   
        Aktivieren Sie dieses Kontrollkästchen, um LDAP-Signatur zu aktivieren. Diese Funktionalität ermöglicht sichere LDAP-Suchvorgänge zwischen dem Azure NetApp Files-Dienst und den benutzerseitig angegebenen [Active Directory Domain Services-Domänencontrollern](/windows/win32/ad/active-directory-domain-services). Weitere Informationen finden Sie unter [ADV190023 | Microsoft-Leitfaden zum Aktivieren der LDAP-Kanalbindung und der LDAP-Signatur](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).  

        ![Active Directory: LDAP-Signatur](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        Die Funktion **LDAP-Signatur** befindet sich derzeit in der Vorschauphase. Wenn Sie dieses Feature zum ersten Mal verwenden, registrieren Sie es vor der Verwendung: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        Überprüfen Sie den Status der Funktionsregistrierung: 

        > [!NOTE]
        > Der **RegistrationState** kann für bis zu 60 Minuten den Status `Registering` aufweisen, bevor der Wechsel in `Registered` erfolgt. Warten Sie, bis der Status `Registered` lautet, bevor Sie fortfahren.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        Sie können auch die [Azure CLI-Befehle](/cli/azure/feature) `az feature register` und `az feature show` verwenden, um das Feature zu registrieren und den Registrierungsstatus anzuzeigen. 

     * **Benutzer mit Sicherheitsberechtigungen**   <!-- SMB CA share feature -->   
        Sie können Benutzern, die erhöhte Rechte für den Zugriff auf die Azure NetApp Files-Volumes benötigen, Sicherheitsberechtigungen (`SeSecurityPrivilege`) zuweisen. Die angegebenen Benutzerkonten dürfen bestimmte Aktionen auf Azure NetApp Files-SMB-Freigaben durchführen, für die Sicherheitsberechtigungen erforderlich sind, die Domänenbenutzern nicht standardmäßig zugewiesen sind.   

        So müssen z. B. Benutzerkonten, die für die Installation von SQL Server in bestimmten Szenarios verwendet werden, mit erhöhten Sicherheitsrechten ausgestattet werden. Wenn Sie ein Nicht-Administratorkonto bzw. Nicht-Administratordomänenkonto für die Installation von SQL Server verwenden und dem Konto keine Sicherheitsberechtigung zugewiesen ist, sollten Sie dem Konto die Sicherheitsberechtigung hinzufügen.  

        > [!IMPORTANT]
        > Wenn Sie das Feature **Benutzer mit Sicherheitsberechtigungen** verwenden möchten, müssen Sie über die Seite **[Azure NetApp Files SMB Continuous Availability Shares Public Preview](https://aka.ms/anfsmbcasharespreviewsignup)** eine Wartelistenanforderung für den Zugriff auf das Feature übermitteln. Warten Sie auf eine offizielle Bestätigungs-E-Mail des Azure NetApp Files-Teams, bevor Sie dieses Feature verwenden.        
        > 
        > Die Verwendung dieses Features ist optional und wird nur für SQL Server unterstützt. Das Domänenkonto, das für die Installation von SQL Server verwendet wird, muss bereits vorhanden sein, bevor Sie es zum Feld **Benutzer mit Sicherheitsberechtigungen** hinzufügen. Wenn Sie das Konto des SQL Server-Installers zu **Benutzer mit Sicherheitsberechtigungen** hinzufügen, überprüft der Azure NetApp Files-Dienst möglicherweise das Konto, indem er eine Verbindung zum Domänencontroller herstellt. Wenn eine Verbindung mit dem Domänencontroller nicht möglich ist, kann der Befehl fehlschlagen.  

        Weitere Informationen zu `SeSecurityPrivilege` und SQL Server finden Sie unter [SQL Server-Installation schlägt fehl, wenn das Einrichtungskonto nicht über bestimmte Benutzerrechte verfügt](/troubleshoot/sql/install/installation-fails-if-remove-user-right).

        ![Screenshot, der das Feld „Benutzer mit Sicherheitsberechtigungen“ des Fensters „Active Directory Domain Services-Verbindungen“ zeigt.](../media/azure-netapp-files/security-privilege-users.png) 

     * **Sicherungsrichtlinienbenutzer**  
        Sie können weitere Konten einschließen, die erhöhte Rechte für das für Azure NetApp Files erstellte Computerkonto erfordern. Die angegebenen Konten dürfen die NTFS-Berechtigungen auf Datei- oder Ordnerebene ändern. Beispielsweise können Sie ein nicht privilegiertes Dienstkonto angeben, das zum Migrieren von Daten zu einer SMB-Dateifreigabe in Azure NetApp Files verwendet wird.  

        ![Active Directory: Sicherungsrichtlinienbenutzer](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        Das Feature **Sicherungsrichtlinienbenutzer** steht derzeit als Vorschau zur Verfügung. Wenn Sie dieses Feature zum ersten Mal verwenden, registrieren Sie es vor der Verwendung: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        Überprüfen Sie den Status der Funktionsregistrierung: 

        > [!NOTE]
        > Der **RegistrationState** kann für bis zu 60 Minuten den Status `Registering` aufweisen, bevor der Wechsel in `Registered` erfolgt. Warten Sie, bis der Status `Registered` lautet, bevor Sie fortfahren.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        Sie können auch die [Azure CLI-Befehle](/cli/azure/feature) `az feature register` und `az feature show` verwenden, um das Feature zu registrieren und den Registrierungsstatus anzuzeigen.  

    * **Administratoren** 

        Sie können Benutzer oder Gruppen angeben, denen Administratorrechte auf dem Volume erteilt werden sollen. 

        ![Screenshot des Felds „Administratoren“ im Fenster „Active Directory-Verbindungen“.](../media/azure-netapp-files/active-directory-administrators.png) 
        
        Das Feature **Administratoren** befindet sich derzeit in der Vorschauphase. Wenn Sie dieses Feature zum ersten Mal verwenden, registrieren Sie es vor der Verwendung: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAdAdministrators
        ```

        Überprüfen Sie den Status der Funktionsregistrierung: 

        > [!NOTE]
        > Der **RegistrationState** kann für bis zu 60 Minuten den Status `Registering` aufweisen, bevor der Wechsel in `Registered` erfolgt. Warten Sie, bis der Status `Registered` lautet, bevor Sie fortfahren.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAdAdministrators
        ```
        
        Sie können auch die [Azure CLI-Befehle](/cli/azure/feature) `az feature register` und `az feature show` verwenden, um das Feature zu registrieren und den Registrierungsstatus anzuzeigen. 

    * Anmeldeinformationen, einschließlich **Benutzername** und **Kennwort**

        ![Active Directory-Anmeldeinformationen](../media/azure-netapp-files/active-directory-credentials.png)

3. Klicken Sie auf **Verknüpfen**.  

    Die von Ihnen erstellte Active Directory-Verbindung wird angezeigt.

    ![Erstellte Active Directory-Verbindungen](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="map-multiple-netapp-accounts-in-the-same-subscription-and-region-to-an-ad-connection"></a><a name="shared_ad"></a>Zuordnen mehrerer NetApp-Konten im selben Abonnement und derselben Region zu einer AD-Verbindung  

Dank des Shared AD-Features (Freigegebenes AD) kann von allen NetApp-Konten, die über eines der zu einem Abonnement und einer Region gehörenden NetApp-Konten erstellt wurden, eine Active Directory-Verbindung (AD) gemeinsam genutzt werden. Bei Verwendung dieses Features kann beispielsweise von allen zu einem Abonnement und einer Region gehörenden NetApp-Konten die allgemeine AD-Konfiguration verwendet werden, um ein [SMB-Volume](azure-netapp-files-create-volumes-smb.md), ein [NFSv4.1-Kerberos-Volume](configure-kerberos-encryption.md) oder ein [Dual-Protokoll-Volume](create-volumes-dual-protocol.md) zu erstellen. Wenn Sie dieses Feature verwenden, ist die AD-Verbindung in allen zu einem Abonnement und einer Region gehörenden NetApp-Konten sichtbar.   

Diese Funktion steht derzeit als Vorschau zur Verfügung. Sie müssen das Feature registrieren, bevor Sie sie zum ersten Mal verwenden. Nach der Registrierung ist das Feature aktiviert und funktioniert im Hintergrund. Es ist keine Benutzeroberflächensteuerung erforderlich. 

1. Registrieren Sie die Funktion: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSharedAD
    ```

2. Überprüfen Sie den Status der Funktionsregistrierung: 

    > [!NOTE]
    > Der **RegistrationState** kann für bis zu 60 Minuten den Status `Registering` aufweisen, bevor der Wechsel in `Registered` erfolgt. Warten Sie, bis der Status **Registriert** lautet, bevor Sie fortfahren.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSharedAD
    ```
Sie können auch die [Azure CLI-Befehle](/cli/azure/feature) `az feature register` und `az feature show` verwenden, um das Feature zu registrieren und den Registrierungsstatus anzuzeigen. 
 
## <a name="next-steps"></a>Nächste Schritte  

* [Erstellen eines SMB-Volumes](azure-netapp-files-create-volumes-smb.md)
* [Erstellen eines Dual-Protokoll-Volumes](create-volumes-dual-protocol.md)
* [Konfigurieren der NFSv4.1-Kerberos-Verschlüsselung](configure-kerberos-encryption.md)
* [Installieren einer neuen Active Directory-Gesamtstruktur mit der Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm) 
