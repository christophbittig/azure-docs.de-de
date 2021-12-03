---
title: Kennwortlose Anmeldung mit Sicherheitsschlüsseln bei lokalen Ressourcen – Azure Active Directory
description: Hier erfahren Sie, wie Sie die kennwortlose Anmeldung mit Sicherheitsschlüsseln bei lokalen Ressourcen mithilfe von Azure Active Directory aktivieren.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce8869a15a1c94ab2f3227780de8eb37bbf52b6f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505420"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-by-using-azure-ad"></a>Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln bei lokalen Ressourcen mithilfe von Azure Active Directory 

In diesem Artikel wird erläutert, wie Sie die kennwortlose Authentifizierung bei lokalen Ressourcen für Umgebungen mit sowohl *Azure Active Directory-eingebundenen (Azure AD)* als auch *hybriden, in Azure AD eingebundenen* Windows 10-Geräten aktivieren. Diese Funktion zur kennwortlosen Authentifizierung ermöglicht die nahtlose einmalige Anmeldung (SSO) bei lokalen Ressourcen, wenn Sie Microsoft-kompatible Sicherheitsschlüssel verwenden.

## <a name="use-sso-to-sign-in-to-on-premises-resources-by-using-fido2-keys"></a>Verwenden von SSO zum Anmelden bei lokalen Ressourcen mithilfe von FIDO2-Schlüsseln

Azure AD kann Kerberos-Ticket-Granting Tickets (TGTs) für eine oder mehrere Ihrer Active Directory-Domänen ausstellen. Mit dieser Funktion können Benutzer*innen sich mit modernen Anmeldeinformationen, wie z. B. FIDO2-Sicherheitsschlüsseln, bei Windows anmelden und dann auf traditionelle, Active Directory-basierte Ressourcen zugreifen. Kerberos-Diensttickets und die Autorisierung werden weiterhin von Ihren lokalen Active Directory-Domänencontrollern (DCs) gesteuert.

Ein Kerberos-Serverobjekt für Azure AD wird in Ihrer lokalen Active Directory-Instanz erstellt und dann sicher in Azure Active Directory veröffentlicht. Das Objekt ist keinem physischen Server zugeordnet. Es handelt sich dabei einfach um eine Ressource, die von Azure Active Directory verwendet werden kann, um Kerberos-TGTs für Ihre Active Directory-Domäne zu generieren.

:::image type="Image" source="./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png" alt-text="Diagramm: Erhalten eines TGT von Azure AD und den Active Directory Domain Services" lightbox="./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png":::

1. Ein*e Benutzer*in meldet sich mit einem FIDO2-Sicherheitsschlüssel bei seinem bzw. ihrem Windows 10-Gerät an und authentifiziert sich bei Azure AD.
1. Azure AD überprüft das Verzeichnis auf einen Kerberos-Serverschlüssel, der mit der lokalen Active Directory-Domäne des Benutzers bzw. der Benutzerin übereinstimmt.

   Azure AD generiert ein Kerberos-TGT für die lokale Active Directory-Domäne des Benutzers bzw. der Benutzerin. Das TGT enthält lediglich die SID des Benutzers bzw. der Benutzerin und enthält keine Autorisierungsdaten.

1. Das TGT wird zusammen mit dem primären Azure AD-Aktualisierungstoken (PRT) an den Client zurückgegeben.
1. Der Clientcomputer kontaktiert den lokalen Azure AD-Domänencontroller und tauscht das partielle TGT durch ein vollständiges TGT.
1. Der Clientcomputer verfügt jetzt über ein Azure AD-PRT und ein vollständiges Active Directory-TGT und kann auf Cloud- und lokale Ressourcen zugreifen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit der in diesem Artikel beschriebenen Prozedur beginnen, muss Ihre Organisation die Anweisungen in [Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln bei Windows 10-Geräten](howto-authentication-passwordless-security-key.md) befolgen.

Außerdem müssen Sie die folgenden Systemanforderungen erfüllen:

- Auf Geräten muss Windows 10, Version 2004 oder höher ausgeführt werden.

- Sie müssen [Azure AD Connect, Version 1.4.32.0 oder höher](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) ausführen.
  - Weitere Informationen zu den verfügbaren Azure AD-Hybridauthentifizierungsoptionen finden Sie in den folgenden Artikeln:
    - [Auswählen der richtigen Authentifizierungsmethode für Ihre Azure AD-Hybrididentitätslösung](../hybrid/choose-ad-authn.md)
    - [Auswählen des Installationstyps für Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md)

- Ihre Windows Server-Domänencontroller müssen Patches für die folgenden Server installiert haben:
    - [Windows Server 2016](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
    - [Windows Server 2019](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="supported-scenarios"></a>Unterstützte Szenarien

In dem Szenario in diesem Artikel wird SSO in beiden der folgenden Instanzen unterstützt:

- Cloudressourcen wie z. B. Microsoft 365 und andere Security Assertion Markup Language-fähige (SAML) Anwendungen
- Lokale Ressourcen und die Windows-integrierte Authentifizierung bei Websites Zu den Ressourcen können Websites und SharePoint-Websites zählen, die eine IIS-Authentifizierung und bzw. oder Ressourcen erfordern, die die NTLM-Authentifizierung verwenden.

### <a name="unsupported-scenarios"></a>Nicht unterstützte Szenarien

Folgende Szenarios werden nicht unterstützt:

- In Active Directory Domain Services (AD DS) eingebundene Windows Server-Bereitstellung nur auf lokalen Geräten
- Das Remotedesktopprotokoll (RDP), Virtual Desktop Infrastructure (VDI) und Citrix-Szenarios mithilfe eines Sicherheitsschlüssels
- S/MIME mithilfe eines Sicherheitsschlüssel
- *Ausführen als* mithilfe eines Sicherheitsschlüssels
- Die Anmeldung bei einem Server mithilfe eines Sicherheitsschlüssels

## <a name="install-the-azure-ad-kerberos-powershell-module"></a>Installieren des Azure AD Kerberos-Moduls für PowerShell

Das [Azure AD Kerberos-Modul für PowerShell](https://www.powershellgallery.com/packages/AzureADHybridAuthenticationManagement) bietet FIDO2-Managementfeatures für Administrator*innen.

1. Öffnen Sie eine PowerShell-Eingabeaufforderung, indem Sie die Option „Als Administrator ausführen“ verwenden.
1. Installieren Sie das Azure AD Kerberos-Modul für PowerShell:

   ```powershell
   # First, ensure TLS 1.2 for PowerShell gallery access.
   [Net.ServicePointManager]::SecurityProtocol = [Net.ServicePointManager]::SecurityProtocol -bor [Net.SecurityProtocolType]::Tls12

   # Install the Azure AD Kerberos PowerShell Module.
   Install-Module -Name AzureADHybridAuthenticationManagement -AllowClobber
   ```

> [!NOTE]
> - Das Azure AD Kerberos-Modul für PowerShell verwendet das [AzureADPreview-Modul für PowerShell](https://www.powershellgallery.com/packages/AzureADPreview), um erweiterte Azure Active Directory-Managementfeatures bereitzustellen. Wenn das [Azure AD-Modul für PowerShell](https://www.powershellgallery.com/packages/AzureAD) bereits auf Ihrem lokalen Computer installiert ist, könnte die hier beschriebene Installation aufgrund eines Konflikts möglicherweise fehlschlagen. Schließen Sie das Optionsflag „-AllowClobber“ ein, um Konflikte bei der Installation zu vermeiden.
> - Sie können das Azure AD Kerberos-Modul für PowerShell auf allen Computern installieren, auf die Sie von Ihrem lokalen Active Directory-Domänencontroller zugreifen können, ohne Abhängigkeit von der Azure AD Connect-Lösung.
> - Das Azure AD Kerberos-Modul für PowerShell wird über den [PowerShell-Katalog](https://www.powershellgallery.com/) vertrieben. Der PowerShell-Katalog ist das zentrale Repository für PowerShell-Inhalte. Sie finden darin nützliche PowerShell-Module, die PowerShell-Befehle und Desired State Configuration-Ressourcen (DSC) enthalten.

## <a name="create-a-kerberos-server-object"></a>Erstellen eines Kerberos-Serverobjekts

Administrator*innen verwenden das Azure AD Kerberos-Modul für PowerShell, um ein Kerberos-Serverobjekt für Azure AD in ihrem lokalen Verzeichnis zu erstellen.

Führen Sie die folgenden Schritte in jeder Domäne und Gesamtstruktur in Ihrer Organisation aus, die Azure AD-Benutzer enthält:

1. Öffnen Sie eine PowerShell-Eingabeaufforderung, indem Sie die Option „Als Administrator ausführen“ verwenden.
1. Führen Sie die folgenden PowerShell-Befehle aus, um ein neues Kerberos-Serverobjekt für Azure AD sowohl in Ihrer lokalen Active Directory-Domäne als auch in Ihrem Azure Active Directory-Mandanten zu erstellen.

   > [!NOTE]
   > Ersetzen Sie `contoso.corp.com` im folgenden Beispiel durch den Namen Ihrer lokalen Active Directory-Domäne.

   ```powershell
   # Specify the on-premises Active Directory domain. A new Azure AD
   # Kerberos Server object will be created in this Active Directory domain.
   $domain = "contoso.corp.com"

   # Enter an Azure Active Directory global administrator username and password.
   $cloudCred = Get-Credential

   # Enter a domain administrator username and password.
   $domainCred = Get-Credential

   # Create the new Azure AD Kerberos Server object in Active Directory
   # and then publish it to Azure Active Directory.
   Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
   ```

   > [!NOTE]
   > Wenn Sie an einem in eine Domäne eingebundenen Computer arbeiten, der über ein Konto mit Domänenadministratorberechtigungen verfügt, können Sie den Parameter „-DomainCredential“ überspringen. Wenn der Parameter „-DomainCredential“ nicht angegeben wird, werden die aktuellen Windows-Anmeldeinformationen verwendet, um auf Ihren lokalen Active Directory-Domänencontroller zuzugreifen.

   ```powershell
   # Specify the on-premises Active Directory domain. A new Azure AD
   # Kerberos Server object will be created in this Active Directory domain.
   $domain = "contoso.corp.com"

   # Enter an Azure Active Directory global administrator username and password.
   $cloudCred = Get-Credential

   # Create the new Azure AD Kerberos Server object in Active Directory
   # and then publish it to Azure Active Directory.
   # Use the current windows login credential to access the on-prem AD.
   Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred
   ```

   > [!NOTE]
   > Wenn sich Ihre Organisation durch die kennwortbasierte Anmeldung schützt und moderne Authentifizierungsmethoden wie z. B. die Multi-Faktor-Authentifizierung, FIDO2, oder die Smartcard-Technologie erzwingt, müssen Sie den Parameter `-UserPrincipalName` mit dem Benutzerprinzipalnamen (UPN) eines globalen Administrators verwenden.
   > - Ersetzen Sie `contoso.corp.com` im folgenden Beispiel durch den Namen Ihrer lokalen Active Directory-Domäne.
   > - Ersetzen Sie `administrator@contoso.onmicrosoft.com` im folgenden Beispiel mit der UPN des globalen Administrators.

   ```powershell
   # Specify the on-premises Active Directory domain. A new Azure AD
   # Kerberos Server object will be created in this Active Directory domain.
   $domain = "contoso.corp.com"

   # Enter a UPN of an Azure Active Directory global administrator
   $userPrincipalName = "administrator@contoso.onmicrosoft.com"

   # Enter a domain administrator username and password.
   $domainCred = Get-Credential

   # Create the new Azure AD Kerberos Server object in Active Directory
   # and then publish it to Azure Active Directory.
   # Open an interactive sign-in prompt with given username to access the Azure AD.
   Set-AzureADKerberosServer -Domain $domain -UserPrincipalName $userPrincipalName -DomainCredential $domainCred
   ```

### <a name="view-and-verify-the-azure-ad-kerberos-server"></a>Anzeigen und Überprüfen des Kerberos-Servers für Azure AD

Sie können den neu erstellten Kerberos-Server für Azure AD mit dem folgenden Befehl anzeigen und überprüfen:

```powershell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Mit diesem Befehl werden die Eigenschaften des Azure AD Kerberos-Servers ausgegeben. Sie können die Eigenschaften überprüfen, um sich zu vergewissern, dass alles in Ordnung ist.

> [!NOTE]
> Bei der Ausführung für eine andere Domäne durch das Angeben der entsprechenden Anmeldeinformationen wird eine Verbindung über NTLM hergestellt, und dann tritt ein Fehler auf. Wenn die Benutzer*innen der Sicherheitsgruppe „Geschützte Benutzer“ in Active Directory angehören, führen Sie diese Schritte aus, um das Problem zu beheben: Melden Sie sich in **ADConnect** als ein*e andere*r Domänenbenutzer*in an, und geben Sie nicht „-domainCredential“ an. Das Kerberos-Ticket des Benutzers bzw. der Benutzerin, der bzw. die aktuell angemeldet ist, wird verwendet. Sie können es durch das Ausführen von `whoami /groups` bestätigen, um zu überprüfen, ob der*die Benutzer*in über die erforderlichen Berechtigungen in Active Directory verfügt, um den folgenden Befehl ausführen.
 
| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| id | Die eindeutige ID des AD DS DC-Objekts. Diese ID wird manchmal als dessen *Slot-ID* oder *Branch-ID* bezeichnet. |
| DomainDnsName | Der DNS-Domänenname der Active Directory-Domäne |
| ComputerAccount | Das Computerkontoobjekt des Azure AD Kerberos-Serverobjekts (der DC). |
| UserAccount | Das deaktivierte Benutzerkontoobjekt, das den TGT-Verschlüsselungsschlüssel des Azure AD Kerberos-Servers enthält. Der Domänenname dieses Kontos ist `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>`. |
| KeyVersion | Die Schlüsselversion des TGT-Verschlüsselungsschlüssels des Azure AD Kerberos-Servers. Die Version wird beim Erstellen des Schlüssels zugewiesen. Die Version wird dann bei jeder Rotation des Schlüssels inkrementiert. Die Inkremente basieren auf Replikationsmetadaten und werden wahrscheinlich größer als ein Inkrement sein. Die anfängliche Schlüsselversion (*KeyVersion*) könnte beispielsweise *192272* lauten. Bei der ersten Rotation des Schlüssels könnte die Version auf *212621* erhöht werden. Sie müssen unbedingt überprüfen, ob die *KeyVersion* für das lokale Objekt und die *CloudKeyVersion* für das Cloudobjekt identisch sind. |
| KeyUpdatedOn | Das Datum und die Uhrzeit der Aktualisierung oder Erstellung des TGT-Verschlüsselungsschlüssels des Azure AD Kerberos-Servers. |
| KeyUpdatedFrom | Der DC, auf dem der TGT-Verschlüsselungsschlüssel des Azure AD Kerberos-Servers zuletzt aktualisiert wurde. |
| CloudId | Die ID aus dem Azure AD-Objekt. Diese muss der ID aus der ersten Zeile der Tabelle entsprechen. |
| CloudDomainDnsName | Der *DomainDnsName* aus dem Azure AD-Objekt. Dieser muss dem *DomainDnsName* aus der zweiten Zeile der Tabelle entsprechen. |
| CloudKeyVersion | Die *KeyVersion* aus dem Azure AD-Objekt. Diese muss der *KeyVersion* aus der fünften Zeile der Tabelle entsprechen. |
| CloudKeyUpdatedOn | Der *KeyUpdatedOn*-Wert aus dem Azure AD-Objekt. Dieser muss dem *KeyUpdatedOn*-Wert aus der sechsten Zeile der Tabelle entsprechen. |
| | |

### <a name="rotate-the-azure-ad-kerberos-server-key"></a>Rotieren des Kerberos-Serverschlüssels für Azure AD

Die *KRBTGT*-Verschlüsselungsschlüssel des Kerberos-Servers für Azure AD sollten in regelmäßigen Abständen rotiert werden. Es wird empfohlen, dass Sie in denselben Abständen rotiert werden wie alle anderen *KRBTGT*-Schlüssel für Active Directory-DC.

> [!WARNING]
> Es gibt andere Tools, mit denen die *KRBTGT*-Schlüssel rotiert werden können. Jedoch müssen Sie die in diesem Dokument genannten Tools verwenden, um die *KRBTGT*-Schlüssel Ihres Kerberos-Servers für Azure AD zu rotieren. Dadurch wird sichergestellt, dass die Schlüssel sowohl im lokalen Active Directory und Azure AD aktualisiert werden.

```powershell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="remove-the-azure-ad-kerberos-server"></a>Entfernen des Kerberos-Servers für Azure AD

Wenn Sie das Szenario wiederherstellen und den Kerberos-Server für Azure AD sowohl von dem lokalen Active Directory als auch von Azure AD entfernen möchten, führen Sie den folgenden Befehl aus:

```powershell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multiforest-and-multidomain-scenarios"></a>Szenarios mit mehreren Gesamtstrukturen und Domänen

Das Kerberos-Serverobjekt für Azure AD wird in Azure AD als *KerberosDomain*-Objekt dargestellt. Jede lokale Active Directory-Domäne wird in Azure AD als einzelnes *KerberosDomain*-Objekt dargestellt.

Beispiel: Ihre Organisation verfügt über eine Active Directory-Gesamtstruktur mit zwei Domänen, `contoso.com` und `fabrikam.com`. Wenn Sie Azure AD erlauben, Kerberos-TGTS für die vollständige Gesamtstruktur auszustellen, gibt es zwei *KerberosDomain*-Objekte in Azure AD, ein *KerberosDomain*-Objekt für `contoso.com` und ein anderes für `fabrikam.com`. Wenn Sie über mehrere Active Directory-Gesamtstrukturen verfügen, ist für jede Domäne in jeder Gesamtstruktur ein *KerberosDomain*-Objekt vorhanden.

Befolgen Sie die Anweisungen in [Erstellen eines Kerberos-Serverobjekts](#create-a-kerberos-server-object) in jeder Domäne und jeder Gesamtstruktur in Ihrer Organisation, die Azure AD-Benutzer*innen enthält.

## <a name="known-behavior"></a>Bekanntes Verhalten

Wenn Ihr Kennwort abgelaufen ist, wird die Anmeldung mit FIDO blockiert. Es wird erwartet, dass Benutzer*innen ihre Kennwörter zurücksetzen, bevor sie sich mithilfe von FIDO anmelden können.

## <a name="troubleshooting-and-feedback"></a>Problembehandlung und Feedback

Wenn Probleme auftreten oder Sie Feedback zum kennwortlosen Sicherheitsschlüssel-Anmeldefeature geben möchten, können Sie das wie folgt über die Windows Feedback Hub-App tun:

1. Öffnen Sie **Feedback Hub**, und stellen Sie sicher, dass Sie angemeldet sind.
1. Senden Sie Feedback, indem Sie die folgenden Kategorien auswählen:
   - Kategorie: Sicherheit und Datenschutz
   - Unterkategorie: FIDO
1. Verwenden Sie die Option **Recreate my Problem** (Mein Problem reproduzieren), um Protokolle aufzuzeichnen.

## <a name="passwordless-security-key-sign-in-faq"></a>Häufig gestellte Fragen zur Anmeldung mit Sicherheitsschlüsseln

Hier finden Sie einige häufig gestellte Fragen zur kennwortlosen Anmeldung:

### <a name="does-passwordless-security-key-sign-in-work-in-my-on-premises-environment"></a>Funktioniert die kennwortlose Anmeldung mit Sicherheitsschlüsseln in meiner lokalen Umgebung?

Das Feature funktioniert in einer rein lokalen AD DS-Umgebung nicht.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Meine Organisation verlangt die Verwendung der zweistufigen Authentifizierung für den Zugriff auf Ressourcen. Was kann ich tun, um diese Anforderung zu unterstützen?

Sicherheitsschlüssel sind in einer Vielzahl von Formfaktoren verfügbar. Wenden Sie sich an den eingetragenen Gerätehersteller, um zu erfahren, wie die Geräte mit einer PIN oder mit biometrischen Daten als zweitem Faktor aktiviert werden können.

### <a name="can-administrators-set-up-security-keys"></a>Können Administrator*innen Sicherheitsschlüssel einrichten?

Zurzeit wird an dieser Funktion für das Release für allgemeine Verfügbarkeit (GA) dieses Features gearbeitet.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Wo kann ich kompatible Sicherheitsschlüssel finden?

Informationen zu konformen Sicherheitsschlüsseln finden Sie unter [FIDO2-Sicherheitsschlüssel](concept-authentication-passwordless.md#fido2-security-keys).

### <a name="what-can-i-do-if-i-lose-my-security-key"></a>Was kann ich tun, wenn ich meinen Schlüssel verliere?

Um einen Sicherheitsschlüssel abzurufen, melden Sie sich im Azure-Portal an, und gehen Sie dann auf die Seite **Sicherheitsinformationen**.

### <a name="what-can-i-do-if-im-unable-to-use-the-fido-security-key-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Was kann ich tun, wenn ich den FIDO-Sicherheitsschlüssel direkt nach dem Erstellen eines hybriden, in Azure AD eingebundenen Computers nicht verwenden kann?

Wenn Sie einen hybriden, in Azure AD-eingebundenen Computer neu installieren, müssen Sie sich nach dem Domänenbeitritt und dem Neustartprozess mit einem Kennwort anmelden und darauf warten, dass die Richtlinie synchronisiert wird, bevor Sie den FIDO-Sicherheitsschlüssel zum Anmelden verwenden können.

- Überprüfen Sie Ihren aktuellen Status, indem Sie `dsregcmd /status` in einem Eingabeaufforderungsfenster ausführen, und überprüfen Sie, dass die Status **AzureAdJoined** und **DomainJoined** als *JA* angezeigt werden.
- Diese Verzögerung beim Synchronisieren ist eine bekannte Beschränkung von in Domänen eingebundenen Geräten und ist nicht FIDO-spezifisch.

### <a name="what-if-im-unable-to-get-single-sign-on-to-my-ntlm-network-resource-after-i-sign-in-with-fido-and-get-a-credential-prompt"></a>Was ist, wenn ich nach dem Anmelden mit FIDO und dem Erhalten einer Administratoranmeldeaufforderung kein SSO bei meiner NTLM-Netzwerkressource erhalten kann?

Stellen Sie sicher, dass genügend DCs gepatcht werden, damit sie rechtzeitig reagieren können, um Ihre Ressourcenanforderung zu bearbeiten. Um anzuzeigen, ob eine DC-Instanz ein Feature ausführt, führen Sie `nltest /dsgetdc:contoso /keylist /kdc` aus und überprüfen Sie dann die Ausgabe.

> [!NOTE]
> Der Parameter`/keylist` im Befehl `nltest` ist im v2004-Client von Windows 10 und höher verfügbar.


### <a name="do-fido2-security-keys-work-in-a-windows-login-with-rodc-present-in-the-hybrid-environment"></a>Funktionieren FIDO2-Sicherheitsschlüssel bei einer Windows-Anmeldung, wenn der RODC in der Hybridumgebung vorhanden ist?

Eine Windows-Anmeldung mit FIDO2 sucht nach einem beschreibbaren DC, um die Benutzer-TGT auszutauschen. So lange Sie mindestens einen beschreibbaren DC pro Website haben, sollte die Anmeldung funktionieren. 

## <a name="next-steps"></a>Nächste Schritte

[Hier erfahren Sie mehr über die kennwortlose Authentifizierung](concept-authentication-passwordless.md).
