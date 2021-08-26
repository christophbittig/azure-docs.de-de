---
title: gMSA-PowerShell-Cmdlets für den Agent für die Azure AD Connect-Cloudbereitstellung
description: Erfahren Sie, wie Sie die gMSA-PowerShell-Cmdlets für den Agent für die Azure AD Connect-Cloudbereitstellung verwenden.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64eb6e15c2c53a8c82b51027471192de4e3679f2
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634321"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-gmsa-powershell-cmdlets"></a>gMSA-PowerShell-Cmdlets für den Agent für die Azure AD Connect-Cloudbereitstellung

In diesem Dokument werden die gMSA-PowerShell-Cmdlets für den Agent für die Azure AD Connect-Cloudbereitstellung beschrieben. Diese Cmdlets ermöglichen Ihnen eine feinere Granularität der Berechtigungen, die auf das gruppenverwaltete Dienstkonto (gMSA, group Managed Service Account) angewendet werden. Standardmäßig wendet die Azure AD Connect-Cloudsynchronisierung alle Berechtigungen für Azure AD Connect auf das Standard-gMSA oder ein benutzerdefiniertes gMSA an.

In diesem Dokument werden die folgenden Cmdlets behandelt:

`Set-AADCloudSyncRestrictedPermissions`

`Set-AADCloudSyncPermissions`

## <a name="how-to-use-the-cmdlets"></a>So verwenden Sie die Cmdlets:

Die folgenden Voraussetzungen müssen erfüllt sein, um diese Cmdlets zu verwenden.

1. Installieren Sie den Bereitstellungs-Agent.

2. Importieren Sie das Bereitstellungs-Agent-PS-Modul in eine PowerShell-Sitzung.

   ```powershell
   Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll"  
   ```

3. Entfernen Sie vorhandene Berechtigungen.  Um alle vorhandenen Berechtigungen für das Dienstkonto außer SELF zu entfernen, verwenden Sie: `Set-AADCloudSyncRestrictedPermission`.

   Dieses Cmdlet erfordert einen Parameter mit dem Namen `Credential`, der übergeben werden kann. Wenn es ohne ihn aufgerufen wird, fordert es seine Eingabe.

   Zum Erstellen einer Variablen verwenden Sie:

   `$credential = Get-Credential`

   Dadurch wird der Benutzer zur Eingabe von Benutzername und Kennwort aufgefordert. Es sind mindestens die Anmeldeinformationen eines Domänenadministrators erforderlich (der Domäne, in der der Agent installiert ist). Anmeldeinformationen eines Unternehmensadministrators kommen auch infrage.

4. Anschließend können Sie das Cmdlet zum Entfernen zusätzlicher Berechtigungen aufrufen:

   ```powershell
   Set-AADCloudSyncRestrictedPermissions -Credential $credential 
   ```

5. Oder rufen Sie einfach

   `Set-AADCloudSyncRestrictedPermissions` auf, wodurch Anmeldeinformationen angefordert werden.

6. Fügen Sie den bestimmten Berechtigungstyp hinzu. Die hinzugefügten Berechtigungen sind mit denen für Azure AD Connect identisch. Beispiele zum Festlegen der Berechtigungen finden Sie unten unter [Verwenden von Set-AADCloudSyncPermissions](#using-set-aadcloudsyncpermissions).

## <a name="using-set-aadcloudsyncpermissions"></a>Verwenden von Set-AADCloudSyncPermissions

`Set-AADCloudSyncPermissions` unterstützt die folgenden Berechtigungstypen, die mit den von Azure AD Connect verwendeten Berechtigungen identisch sind. Die folgenden Berechtigungstypen werden unterstützt:

|Berechtigungstyp|Beschreibung|
|-----|-----|
|BasicRead| Siehe [BasicRead](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#configure-basic-read-only-permissions)-Berechtigungen für Azure AD Connect|
|PasswordHashSync|Siehe [PasswordHashSync](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-hash-synchronization)-Berechtigungen für Azure AD Connect|
|PasswordWriteBack|Siehe [PasswordWriteBack](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-writeback)-Berechtigungen für Azure AD Connect|
|HybridExchangePermissions|Siehe [HybridExchangePermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-hybrid-deployment)-Berechtigungen für Azure AD Connect|
|ExchangeMailPublicFolderPermissions| Siehe [ExchangeMailPublicFolderPermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-mail-public-folders)-Berechtigungen für Azure AD Connect|
|CloudHR| Wendet „Vollzugriff“ auf „Nachfolger-Benutzerobjekte“ und „Benutzerobjekte erstellen/löschen“ auf „Dieses und alle untergeordneten Objekte“ an.|
|Alle|fügt alle obigen Berechtigungen hinzu.|

Sie können AADCloudSyncPermissions-Berechtigungen auf eine von zwei Arten verwenden:
- [Gewähren einer bestimmten Berechtigung für alle konfigurierten Domänen](#grant-a-certain-permission-to-all-configured-domains)
- [Gewähren einer bestimmten Berechtigung für eine spezifische Domäne](#grant-a-certain-permission-to-a-specific-domain)

## <a name="grant-a-certain-permission-to-all-configured-domains"></a>Gewähren einer bestimmten Berechtigung für alle konfigurierten Domänen

Wenn Sie für alle konfigurierten Domänen bestimmte Berechtigungen erteilen, muss ein Unternehmensadministratorkonto verwendet werden.

```powershell
Set-AADCloudSyncPermissions -PermissionType "Any mentioned above" -EACredential $credential (prepopulated same as above [$credential = Get-Credential]) 
```

## <a name="grant-a-certain-permission-to-a-specific-domain"></a>Gewähren einer bestimmten Berechtigung für eine spezifische Domäne

Wenn Sie bestimmte Berechtigungen für eine spezifische Domäne gewähren, muss mindestens ein Domänenadministratorkonto der Domäne, die Sie hinzufügen möchten, verwendet werden.

```powershell
Set-AADCloudSyncPermissions -PermissionType "Any mentioned above" -TargetDomain "FQDN of domain" (has to be already configured through wizard) -TargetDomainCredential $credential(same as above) 
```

Hinweis: zu 1. Es sind mindestens die Anmeldeinformationen eines Unternehmensadministrators erforderlich.

Geben Sie für 2. Es sind entweder die Anmeldeinformationen eines Domänen- oder Unternehmensadministrators erforderlich.
