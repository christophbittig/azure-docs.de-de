---
title: 'Azure VMware Solution by CloudSimple: Verwenden von Azure AD als Identitätsquelle für die private Cloud'
description: Beschreibt, wie Sie Azure AD als Identitätsanbieter in Ihrer privaten CloudSimple-Cloud hinzufügen, um Benutzer zu authentifizieren, die aus Azure auf CloudSimple zugreifen.
author: suzizuber
ms.author: v-szuber
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a462e8606a9ab6be88052c45a3110682d188449f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308008"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Verwenden von Azure AD als Identitätsanbieter für vCenter in einer privaten CloudSimple-Cloud

Sie können Ihr vCenter für die private CloudSimple-Cloud für die Authentifizierung mit Azure Active Directory (Azure AD) einrichten, damit Ihre VMware-Administratoren auf vCenter zugreifen können. Nachdem die Identitätsquelle für einmaliges Anmelden eingerichtet wurde, kann der **cloudowner**-Benutzer vCenter Benutzer aus der Identitätsquelle hinzufügen.  

Sie können Ihre Active Directory-Domäne und Domänencontroller auf eine der folgenden Arten einrichten:

* Lokal ausgeführte Active Directory-Domäne und -Domänencontroller
* In Azure als virtuelle Computer im Azure-Abonnement ausgeführte Active Directory-Domänen und -Domänencontroller
* Neue Active Directory-Domäne und -Domänencontroller, die in Ihrer privaten CloudSimple-Cloud ausgeführt werden
* Azure Active Directory-Dienst

In diesem Leitfaden werden die Aufgaben erläutert, die zum Einrichten von Azure AD als Identitätsquelle erforderlich sind.  Informationen zur Verwendung von lokalem Active Directory oder in Azure ausgeführtem Active Directory finden Sie unter [Einrichten von vCenter-Identitätsquellen für die Verwendung von Active Directory](set-vcenter-identity.md). Dieses Thema enthält ausführliche Anweisungen zum Einrichten der Identitätsquelle.

## <a name="about-azure-ad"></a>Informationen zu Azure AD

Azure AD ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft.  Azure AD bietet einen skalierbaren, konsistenten und zuverlässigen Authentifizierungsmechanismus für Benutzer zum Authentifizieren und Zugreifen auf verschiedene Dienste in Azure.  Außerdem werden sichere LDAP-Dienste für Drittanbieterdienste zur Verwendung von Azure AD als Authentifizierungs-/Identitätsquelle bereitstellt.  Azure AD kombiniert Kernverzeichnisdienste, erweiterte Identitätsgovernance und Anwendungszugriffsverwaltung, mit denen Sie Benutzern, die die private Cloud verwalten, Zugriff auf Ihre private Cloud gewähren können.

Wenn Sie Azure AD als Identitätsquelle mit vCenter verwenden möchten, müssen Sie Azure AD und Azure AD Domain Services einrichten. Befolgen Sie diese Anweisungen:

1. [Einrichten von Azure AD und Azure AD Domain Services](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Einrichten einer Identitätsquelle für vCenter in Ihrer privaten Cloud](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Einrichten von Azure AD und Azure AD Domain Services

Bevor Sie beginnen, benötigen Sie Zugriff auf Ihr Azure-Abonnement mit globalen Administratorberechtigungen.  In den folgenden Schritten werden allgemeine Richtlinien erläutert. Details finden Sie in der Azure-Dokumentation.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Wenn Sie bereits über Azure AD verfügen, können Sie diesen Abschnitt überspringen.

1. Richten Sie Azure AD für Ihr Abonnement ein, wie in der [Azure AD-Dokumentation](../active-directory/fundamentals/active-directory-whatis.md) beschrieben.
2. Aktivieren Sie Azure Active Directory Premium für Ihr Abonnement, wie unter [Registrieren für Azure Active Directory Premium](../active-directory/fundamentals/active-directory-get-started-premium.md) beschrieben.
3. Richten Sie einen benutzerdefinierten Domänennamen ein, und überprüfen Sie den benutzerdefinierten Domänennamen, wie unter [Hinzufügen eines benutzerdefinierten Domänennamens zu Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md) beschrieben.
    1. Richten Sie einen DNS-Eintrag für Ihre Domänenregistrierungsstelle mit den in Azure bereitgestellten Informationen ein.
    2. Legen Sie den Namen der benutzerdefinierten Domäne als primäre Domäne fest.

Optional können Sie auch andere Azure AD-Features konfigurieren.  Diese sind nicht erforderlich, um die vCenter-Authentifizierung mit Azure AD zu aktivieren.

### <a name="azure-ad-domain-services"></a>Azure AD Domain Services

> [!NOTE]
> Dies ist ein wichtiger Schritt zum Aktivieren von Azure AD als Identitätsquelle für vCenter.  Stellen Sie sicher, dass alle Schritte ordnungsgemäß ausgeführt werden, um Probleme zu vermeiden.

1. Aktivieren Sie Azure AD Domain Services wie unter [Aktivieren von Azure Active Directory Domain Services mit dem Azure-Portal](../active-directory-domain-services/tutorial-create-instance.md) beschrieben.
2. Richten Sie das Netzwerk ein, das von den Azure AD Domain Services verwendet wird, wie unter [Aktivieren von Azure Active Directory Domain Services mit dem Azure-Portal](../active-directory-domain-services/tutorial-create-instance.md) beschrieben.
3. Konfigurieren Sie die Administratorgruppe zum Verwalten der Azure AD Domain Services wie unter [Aktivieren von Azure Active Directory Domain Services mit dem Azure-Portal](../active-directory-domain-services/tutorial-create-instance.md) beschrieben.
4. Aktualisieren Sie die DNS-Einstellungen für Ihre Azure AD Domain Services wie unter [Aktivieren von Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md) beschrieben.  Wenn Sie über das Internet eine Verbindung mit AD herstellen möchten, richten Sie den DNS-Eintrag für die öffentliche IP-Adresse der Azure AD Domain Services mit dem Domänennamen ein.
5. Aktivieren Sie die Kennworthashsynchronisierung für Benutzer.  In diesem Schritt wird die Synchronisierung der Kennworthashes, die für die NTLM- (NT LAN Manager) und Kerberos-Authentifizierung erforderlich sind, mit Azure AD Domain Services aktiviert. Nach der Einrichtung der Kennworthashsynchronisierung können sich Benutzer mit ihren Unternehmensanmeldeinformationen bei der verwalteten Domäne anmelden. Weitere Informationen finden Sie unter [Aktivieren der Kennworthashsynchronisierung für Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md).
    1. Wenn reine Cloudbenutzer vorhanden sind, müssen diese ihr Kennwort über den <a href="https://myapps.microsoft.com/" target="_blank">Azure AD-Zugriffsbereich</a> ändern, um sicherzustellen, dass Kennworthashes in dem Format gespeichert werden, das von NTLM oder Kerberos benötigt wird.  Befolgen Sie die Anleitungen unter [Aktivieren der Kennworthashsynchronisierung mit Ihrer verwalteten Domäne für reine Cloudbenutzerkonten](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).  Dieser Schritt muss für einzelne Benutzer und alle neuen Benutzer durchgeführt werden, die in Ihrem Azure AD-Verzeichnis mithilfe des Azure-Portals oder mit Azure AD-PowerShell-Cmdlets erstellt werden. Benutzer, die Zugriff auf Azure AD Domain Services benötigen, müssen den <a href="https://myapps.microsoft.com/" target="_blank">Azure AD-Zugriffsbereich</a> verwenden und auf ihr Profil zugreifen, um das Kennwort zu ändern.

        > [!NOTE]
        > Falls Ihr Unternehmen über reine Cloudbenutzerkonten verfügt, müssen alle Benutzer, die Azure Active Directory Domain Services verwenden möchten, ihre Kennwörter ändern. Ein reines Cloudbenutzerkonto ist ein Konto, das in Ihrem Azure AD-Verzeichnis über das Azure-Portal oder mithilfe von Azure AD PowerShell-Cmdlets erstellt wurde. Solche Benutzerkonten werden nicht von einem lokalen Verzeichnis aus synchronisiert.

    2. Wenn Sie Kennwörter aus Ihrem lokalen Active Directory synchronisieren, führen Sie die Schritte aus, die in der [Active Directory-Dokumentation](../active-directory-domain-services/tutorial-configure-password-hash-sync.md) beschrieben werden.

6.  Konfigurieren Sie Secure LDAP für Ihre Azure Active Directory Domain Services wie unter [Konfigurieren von Secure LDAP (LDAPS) für eine von Azure AD Domain Services verwaltete Domäne](../active-directory-domain-services/tutorial-configure-ldaps.md) beschrieben.
    1. Laden Sie ein Zertifikat für die Verwendung durch Secure LDAP hoch, wie im Azure-Thema [Abrufen eines Zertifikats für Secure LDAP](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap) beschrieben wird.  CloudSimple empfiehlt die Verwendung eines signierten Zertifikats, das von einer Zertifizierungsstelle ausgestellt wurde, um sicherzustellen, dass vCenter dem Zertifikat vertrauen kann.
    2. Aktivieren Sie Secure LDAP wie unter [Konfigurieren von Secure LDAP (LDAPS) für eine durch Azure AD Domain Services verwaltete Domäne](../active-directory-domain-services/tutorial-configure-ldaps.md) beschrieben.
    3. Speichern Sie den öffentlichen Teil des Zertifikats (ohne den privaten Schlüssel) im CER-Format für die Verwendung mit vCenter, während Sie die Identitätsquelle konfigurieren.
    4. Wenn Internetzugriff auf Azure AD Domain Services erforderlich ist, aktivieren Sie die Option „Sicheren Zugriff auf LDAP über das Internet zulassen“.
    5. Fügen Sie die Sicherheitsregel für eingehenden Datenverkehr für die Azure AD Domain Services-NSG für TCP-Port 636 hinzu.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Einrichten einer Identitätsquelle für vCenter in Ihrer privaten Cloud

1. [Eskalieren Sie die Berechtigungen](escalate-private-cloud-privileges.md) für Ihr vCenter in der privaten Cloud.
2. Erfassen Sie die Konfigurationsparameter, die zum Einrichten der Identitätsquelle erforderlich sind.

    | **Option** | **Beschreibung** |
    |------------|-----------------|
    | **Name** | Der Name der Identitätsquelle. |
    | **Basis-DN für Benutzer** | Der Distinguished Basis-Name für Benutzer.  Verwenden Sie für Azure AD Folgendes: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`Beispiel: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`.|
    | **Domänenname** | Der FQDN der Domäne, z. B. „example.com“. Geben Sie in diesem Textfeld keine IP-Adresse an. |
    | **Domänenalias** | *(optional)* : Der NetBIOS-Name der Domäne. Fügen Sie den NetBIOS-Namen der Active Directory-Domäne als Alias der Identitätsquelle hinzu, wenn Sie SSPI-Authentifizierung verwenden. |
    | **Basis-DN für Gruppen** | Der Distinguished Basis-Name für Gruppen. Verwenden Sie für Azure AD Folgendes: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`Beispiel: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **URL des primären Servers** | LDAP-Server des primären Domänencontrollers für die Domäne.<br><br>Verwenden Sie das Format `ldaps://hostname:port`. Der Port ist in der Regel 636 für LDAPS-Verbindungen. <br><br>Ein Zertifikat, das eine Vertrauensstellung für den LDAPS-Endpunkt des Active Directory-Servers einrichtet, ist erforderlich, wenn Sie `ldaps://` in der primären oder sekundären LDAP-URL verwenden |
    | **URL des sekundären Servers** | Die Adresse eines LDAP-Servers für einen sekundären Domänencontroller, der für Failover verwendet wird. |
    | **Zertifikat auswählen** | Wenn Sie LDAPS mit dem Active Directory LDAP-Server oder der OpenLDAP-Server-Identitätsquelle verwenden möchten, wird eine Schaltfläche „Zertifikat auswählen“ nach dem Eingeben von `ldaps://` in das URL-Textfeld angezeigt. Eine sekundäre URL ist nicht erforderlich. |
    | **Benutzername** | Die ID eines Benutzers in der Domäne, der mindestens über Lesezugriff auf den Basis-DN für Benutzer und Gruppen verfügt. |
    | **Kennwort** | Das Kennwort des Benutzers, der durch „Benutzername“ angegeben wird. |

3. Melden Sie sich bei Ihrem vCenter in der privaten Cloud an, nachdem die Berechtigungen eskaliert wurden.
4. Befolgen Sie die Anweisungen unter [Hinzufügen einer Identitätsquelle für vCenter](set-vcenter-identity.md#add-an-identity-source-on-vcenter), und verwenden Sie dabei die Werte aus dem vorherigen Schritt, um Azure Active Directory als Identitätsquelle einzurichten.
5. Fügen Sie Benutzer/Gruppen aus Azure AD vCenter-Gruppen hinzu, wie im VMware-Thema [Hinzufügen von Mitgliedern zu einer vCenter-Gruppe für einmaliges Anmelden](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html) beschrieben wird.

> [!CAUTION]
> Neue Benutzer müssen lediglich *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* oder *Cloud-Global-VM-Admin-Group* hinzugefügt werden.  Benutzer, die der Gruppe *Administratoren* hinzugefügt wurden, werden automatisch entfernt.  Der Gruppe *Administratoren* dürfen nur Dienstkonten hinzugefügt werden.

## <a name="next-steps"></a>Nächste Schritte

* [Kennenlernen des Berechtigungsmodells für private Clouds](learn-private-cloud-permissions.md)
