---
title: Voraussetzungen für die Azure AD Connect-Cloudsynchronisierung in Azure AD
description: In diesem Artikel werden die Voraussetzungen und Hardwareanforderungen für die Cloudsynchronisierung beschrieben.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31850f855cdf109e2337898736d273237ff65058
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018199"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-sync"></a>Voraussetzungen für die Azure AD Connect-Cloudsynchronisierung
Dieser Artikel enthält Anleitungen zur Auswahl und Verwendung der Azure Active Directory Connect-Cloudsynchronisierung (Azure AD) als Identitätslösung.

## <a name="cloud-provisioning-agent-requirements"></a>Anforderungen des Agents für die Cloudbereitstellung
Für die Verwendung der Azure AD Connect-Cloudsynchronisierung benötigen Sie Folgendes:

- Anmeldeinformationen eines Domänenadministrators oder Unternehmensadministrators zum Erstellen des gMSA (group Managed Service Account, gruppenverwaltetes Dienstkonto) für die Azure AD Connect-Cloudsynchronisierung zum Ausführen des-Agent-Diensts 
- Ein Hybrididentität-Administratorkonto für Ihren Azure AD-Mandanten, das kein Gastbenutzer ist.
- Einen lokalen Server für den Bereitstellungs-Agent mit Windows 2016 oder höher.  Bei diesem Server sollte es sich um einen Server der Ebene 0 im [Active Directory-Verwaltungsebenenmodell](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) handeln.  Die Installation des Agents auf einem Domänencontroller wird unterstützt.
- Hochverfügbarkeit bezieht sich auf die Fähigkeit von Azure AD Connect-Cloudsynchronisierung, über einen längeren Zeitraum kontinuierlich ohne Fehler zu arbeiten.  Durch die Installation und Ausführung mehrerer aktiver Agents kann Azure AD Connect-Cloudsynchronisierung auch dann weiterhin funktionieren, wenn ein Agent ausfällt.  Microsoft empfiehlt, für Hochverfügbarkeit drei aktive Agents zu installieren.
- lokale Firewallkonfigurationen

## <a name="group-managed-service-accounts"></a>Gruppenverwaltete Dienstkonten
Ein gruppenverwaltetes Dienstkonto ist ein verwaltetes Domänenkonto, das eine automatische Kennwortverwaltung, eine vereinfachte Verwaltung von Dienstprinzipalnamen (Service Principal Name, SPN) und die Möglichkeit bietet, die Verwaltung an andere Administratoren zu delegieren, wobei diese Funktionalität auch auf mehrere Server erweitert wird.  Die Azure AD Connect-Cloudsynchronisierung unterstützt und verwendet ein gMSA zum Ausführen des Agents.  Sie werden während des Setups zur Eingabe administrativer Anmeldeinformationen aufgefordert, um das Konto zu erstellen.  Das Konto wird als (domain\provAgentgMSA$) angezeigt.  Weitere Informationen zu einem gruppenverwalteten Dienstkonto (Group Managed Service Account, GMSA) finden Sie unter [Gruppenverwaltete Dienstkonten](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview). 

### <a name="prerequisites-for-gmsa"></a>Voraussetzungen für das gMSA:
1.  Das Active Directory-Schema in der Gesamtstruktur der gMSA-Domäne muss auf Windows Server 2012 oder höher aktualisiert werden.
2.  [PowerShell-RSAT-Module](/windows-server/remote/remote-server-administration-tools) auf einem Domänencontroller
3.  Auf mindestens einem Domänencontroller in der Domäne muss Windows Server 2012 oder höher ausgeführt werden.
4.  Ein in eine Domäne eingebundener Server, auf dem der Agent installiert ist, muss Windows Server 2016 oder höher aufweisen.

### <a name="custom-gmsa-account"></a>Benutzerdefiniertes gMSA-Konto
Wenn Sie ein benutzerdefiniertes gMSA-Konto erstellen, müssen Sie sicherstellen, dass das Konto über die folgenden Berechtigungen verfügt.

|type |Name |Zugriff |Gilt für| 
|-----|-----|-----|-----|
|Allow |gMSA-Konto |Alle Eigenschaften lesen |Nachfolger-Geräteobjekte| 
|Allow |gMSA-Konto|Alle Eigenschaften lesen |Nachfolger-InetOrgPerson-Objekte| 
|Allow |gMSA-Konto |Alle Eigenschaften lesen |Nachfolger-Computerobjekte| 
|Allow |gMSA-Konto |Alle Eigenschaften lesen |Nachfolger-foreignSecurityPrincipal-Objekte| 
|Allow |gMSA-Konto |Vollzugriff |Nachfolger-Gruppenobjekte| 
|Allow |gMSA-Konto |Alle Eigenschaften lesen |Nachfolger-Benutzerobjekte| 
|Allow |gMSA-Konto |Alle Eigenschaften lesen |Nachfolger-Kontaktobjekte| 
|Allow |gMSA-Konto |Benutzerobjekte erstellen/löschen|Dieses Objekt und alle Nachfolgerobjekte| 

Die Schritte zum Aktualisieren eines vorhandenen Agents für die Verwendung eines gMSA-Kontos finden Sie unter [Gruppenverwaltete Dienstkonten](how-to-install.md#group-managed-service-accounts).

#### <a name="create-gmsa-account-with-powershell"></a>Erstellen eines gMSA-Kontos mithilfe von PowerShell
Sie können das folgende PowerShell-Skript verwenden, um ein benutzerdefiniertes gMSA-Konto zu erstellen.  Anschließend können Sie die [gMSA-Cmdlets für die Cloudsynchronisierung](how-to-gmsa-cmdlets.md) verwenden, um präzisere Berechtigungen anzuwenden.

```powershell
# Filename:    1_SetupgMSA.ps1
# Description: Creates and installs a custom gMSA account for use with Azure AD Connect cloud sync.
#
# DISCLAIMER:
# Copyright (c) Microsoft Corporation. All rights reserved. This 
# script is made available to you without any express, implied or 
# statutory warranty, not even the implied warranty of 
# merchantability or fitness for a particular purpose, or the 
# warranty of title or non-infringement. The entire risk of the 
# use or the results from the use of this script remains with you.
#
#
#
#
# Declare variables
$Name = 'provAPP1gMSA'
$Description = "Azure AD Cloud Sync service account for APP1 server"
$Server = "APP1.contoso.com"
$Principal = Get-ADGroup 'Domain Computers'

# Create service account in Active Directory
New-ADServiceAccount -Name $Name `
-Description $Description `
-DNSHostName $Server `
-ManagedPasswordIntervalInDays 30 `
-PrincipalsAllowedToRetrieveManagedPassword $Principal `
-Enabled $True `
-PassThru

# Install the new service account on Azure AD Cloud Sync server
Install-ADServiceAccount -Identity $Name
```

Weitere Informationen zur Verwendung dieser Cmdlets finden Sie unter [Erste Schritte mit gruppenverwalteten Dienstkonten](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)?redirectedfrom=MSDN).

### <a name="in-the-azure-active-directory-admin-center"></a>Im Azure Active Directory Admin Center

1. Erstellen Sie in Ihrem Azure AD-Mandanten ein auf die Cloud beschränktes Hybrididentität-Administratorkonto. Auf diese Weise können Sie die Konfiguration Ihres Mandanten verwalten, wenn Ihre lokalen Dienste ausfallen oder nicht verfügbar sind. Erfahren Sie, wie Sie ein [auf die Cloud beschränktes Hybrididentität-Administratorkonto hinzufügen](../fundamentals/add-users-azure-active-directory.md). Die Ausführung dieses Schritts ist äußerst wichtig, damit sichergestellt ist, dass Sie für Ihren Mandanten nicht gesperrt werden.
1. Fügen Sie Ihrem Azure AD-Mandanten mindestens einen [benutzerdefinierten Domänennamen](../fundamentals/add-custom-domain.md) hinzu. Ihre Benutzer können sich mit einem dieser Domänennamen anmelden.

### <a name="in-your-directory-in-active-directory"></a>In Ihrem Verzeichnis in Active Directory

Führen Sie das [IdFix-Tool](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) aus, um die Verzeichnisattribute für die Synchronisierung vorzubereiten.

### <a name="in-your-on-premises-environment"></a>In Ihrer lokalen Umgebung

1. Geben Sie einen in die Domäne eingebundenen Hostserver unter Windows Server 2016 oder höher mit mindestens 4 GB RAM und .NET-Runtime (ab 4.7.1) an.

2. Die PowerShell-Ausführungsrichtlinie auf dem lokalen Server muss auf „Nicht definiert“ oder „RemoteSigned“ festgelegt werden.

3. Wenn zwischen Ihren Servern und Azure AD eine Firewall eingerichtet wurde, konfigurieren Sie die folgenden Elemente:
    - Stellen Sie sicher, dass Agents über die folgenden Ports *ausgehende* Anforderungen an Azure AD senden können:

      | Portnummer | Wie diese verwendet wird |
      | --- | --- |
      | **80** | Herunterladen der Zertifikatsperrlisten (CRLs) bei der Überprüfung des TLS/SSL-Zertifikats.  |
      | **443** | Verarbeiten der gesamten ausgehenden Kommunikation mit dem Dienst |
      | **8080** (optional) | Agents melden ihren Status alle zehn Minuten über den Port 8080, wenn der Port 443 nicht verfügbar ist. Dieser Status wird im Azure AD-Portal angezeigt. |

    - Wenn Ihre Firewall Regeln gemäß Ursprungsbenutzern erzwingt, öffnen Sie diese Ports für den Datenverkehr aus Windows-Diensten, die als Netzwerkdienst ausgeführt werden.
    - Wenn Ihre Firewall oder Ihr Proxy das Angeben sicherer Suffixe zulässt, fügen Sie Verbindungen zu \*.msappproxy.net\* und „.servicebus.windows.net“ hinzu. Aktivieren Sie andernfalls den Zugriff auf die [IP-Adressbereiche für das Azure-Rechenzentrum](https://www.microsoft.com/download/details.aspx?id=41653), die wöchentlich aktualisiert werden.
    - Ihre Agents benötigen für die Erstregistrierung Zugriff auf „login.windows.net“ und „login.microsoftonline.com“. Öffnen Sie Ihre Firewall auch für diese URLs.
    - Geben Sie für die Zertifikatüberprüfung folgende URLs frei: „mscrl.microsoft.com:80“, „crl.microsoft.com:80“, „ocsp.msocsp.com:80“ und „www\.microsoft.com:80“. Da diese URLs für die Zertifikatüberprüfung in Verbindung mit anderen Microsoft-Produkten verwendet werden, haben Sie diese möglicherweise bereits freigegeben.

    >[!NOTE]
    > Die Installation des Agents für die Cloudbereitstellung unter Windows Server Core wird nicht unterstützt.

### <a name="additional-requirements"></a>Zusätzliche Anforderungen

- [Microsoft .NET Framework 4.7.1](https://dotnet.microsoft.com/download/dotnet-framework/net471) 

#### <a name="tls-requirements"></a>TLS-Anforderungen

> [!NOTE]
> Transport Layer Security (TLS) ist ein Protokoll, das für eine sichere Kommunikation sorgt. Das Ändern der TLS-Einstellungen wirkt sich auf die Gesamtstruktur aus. Weitere Informationen finden Sie unter [Update zur Aktivierung von TLS 1.1 und TLS 1.2 als sichere Standardprotokolle in WinHTTP unter Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

Auf dem Windows-Server, auf dem der Agent für die Azure AD Connect-Cloudbereitstellung gehostet wird, muss TLS 1.2 aktiviert sein, bevor Sie den Agent installieren.

Führen Sie diese Schritte aus, um TLS 1.2 zu aktivieren.

1. Legen Sie die folgenden Registrierungsschlüssel fest:

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Starten Sie den Server neu.
## <a name="ntlm-requirement"></a>NTLM-Anforderung

Sie sollten die integrierte Windows-Authentifizierung (NTLM) nicht auf dem Windows-Server aktivieren, auf dem der Azure AD Connect-Bereitstellungs-Agent ausgeführt wird. Wenn sie aktiviert ist, müssen Sie sie deaktivieren. 

## <a name="known-limitations"></a>Bekannte Einschränkungen

Es gelten die folgenden bekannten Einschränkungen:

### <a name="delta-synchronization"></a>Deltasynchronisierung

- Bei der Gruppenbereichsfilterung für die Deltasynchronisierung werden nicht mehr als 50.000 Mitglieder unterstützt.
- Wenn Sie eine Gruppe löschen, die für einen Gruppenbereichsfilter verwendet wird, werden Benutzer, die Mitglieder der Gruppe sind, nicht gelöscht. 
- Wenn Sie die im Bereich befindliche Organisationseinheit oder Gruppe umbenennen, werden die Benutzer bei der Deltasynchronisierung nicht entfernt.

### <a name="provisioning-logs"></a>Bereitstellungsprotokolle
- In den Bereitstellungsprotokollen wird nicht eindeutig zwischen Erstellungs- und Aktualisierungsvorgängen unterschieden.  Es kann sein, dass ein Erstellungsvorgang für eine Aktualisierung und ein Aktualisierungsvorgang für eine Erstellung angezeigt wird.

### <a name="group-re-naming-or-ou-re-naming"></a>Umbenennen von Gruppen oder Organisationseinheiten
- Wenn Sie eine Gruppe oder Organisationseinheit in AD umbenennen, die sich im Bereich einer bestimmten Konfiguration befindet, wird die Namensänderung in AD vom Cloudsynchronisierungsauftrag nicht erkannt. Der Auftrag wird nicht unter Quarantäne gestellt und bleibt im fehlerfreien Zustand.

### <a name="scoping-filter"></a>Bereichsfilter
Bei Verwendung des Bereichsfilters für Organisationseinheiten
- Sie können für eine bestimmte Konfiguration nur bis zu 59 separate Organisationseinheiten synchronisieren. 
- Geschachtelte Organisationseinheiten werden unterstützt (d. h., Sie **können** eine Organisationseinheit mit 130 geschachtelten Organisationseinheiten synchronisieren, aber Sie **können nicht** 60 separate Organisationseinheiten in derselben Konfiguration synchronisieren). 

### <a name="password-hash-sync"></a>Kennworthashsynchronisierung
- Die Verwendung der Kennworthashsynchronisierung mit InetOrgPerson wird nicht unterstützt.


## <a name="next-steps"></a>Nächste Schritte 

- [Was ist die Identitätsbereitstellung?](what-is-provisioning.md)
- [Worum handelt es sich bei der Azure AD Connect-Cloudsynchronisierung?](what-is-cloud-sync.md)
