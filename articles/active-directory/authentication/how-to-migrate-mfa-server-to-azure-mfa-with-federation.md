---
title: Migrieren zu Azure AD MFA mit Verbunden – Azure Active Directory
description: Detaillierte Anleitung für den Wechsel vom lokalen Azure MFA-Server zu Azure AD MFA mit Verbund
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c5d92d16f5ac9fcd8aa69ce9fd71f4844a77d28
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352717"
---
# <a name="migrate-to-azure-ad-mfa-with-federation"></a>Migrieren zu Azure AD MFA mit Verbund

Das Verschieben Ihrer MFA-Lösung (Multi-Factor Authentication) zu Azure Active Directory (Azure AD) ist ein idealer erster Schritt beim Wechsel zur Cloud. Für die Zukunft sollten Sie auch eine Umstallung auf Azure AD für die Benutzerauthentifizierung in Betracht ziehen. Weitere Informationen finden Sie im Prozess für die Migration zu Azure AD MFA mit Cloudauthentifizierung.

Bei der Migration zu Azure AD MFA mit Verbund wird der Azure MFA-Authentifizierungsanbieter in AD FS installiert. Die Azure AD-Vertrauensstellung der vertrauenden Seite und andere Vertrauensstellungen der vertrauenden Seite sind für die Verwendung von Azure MFA für migrierte Benutzer konfiguriert.

Das folgende Diagramm zeigt den Prozess dieser Migration.

![Diagramm mit den Schritten des Prozesses. Diese entsprechen den Überschriften und der Reihenfolge in diesem Dokument.](./media/how-to-migrate-mfa-server-to-azure-mfa-with-federation/mfa-federation-flow.png)

## <a name="create-migration-groups"></a>Erstellen von Migrationsgruppen

Um neue Richtlinien für bedingten Zugriff zu erstellen, müssen Sie diese Richtlinien Gruppen zuweisen. Hierzu können Sie vorhandene Azure AD-Sicherheitsgruppen oder Microsoft 365-Gruppen verwenden. Sie können auch neue erstellen oder synchronisieren.

Außerdem benötigen Sie eine Azure AD-Sicherheitsgruppe, um Benutzer iterativ zu Azure AD MFA migrieren zu können. Diese Gruppen werden in Ihren Anspruchsregeln verwendet.

Verwenden Sie keine Gruppen, die zu Sicherheitszwecken erstellt wurden. Wenn Sie eine Sicherheitsgruppe verwenden, um eine Gruppe von wertvollen Apps über eine Richtlinie für bedingten Zugriff zu schützen, sollte die Gruppe ausschließlich zu diesem Zweck verwendet werden.

## <a name="prepare-ad-fs"></a>Vorbereiten von AD FS

### <a name="upgrade-ad-fs-server-farm-to-2019-fbl-4"></a>Upgrade der AD FS-Serverfarm auf 2019, FBL 4

In AD FS 2019 können Sie zusätzliche Authentifizierungsmethoden für eine vertrauende Partei angeben, z. B. eine Anwendung. Sie verwenden die Gruppenmitgliedschaft, um den Authentifizierungsanbieter zu bestimmen. Wenn Sie eine zusätzliche Authentifizierungsmethode angeben, können Sie zu Azure AD MFA wechseln und die andere Authentifizierung während des Übergangs beibehalten. Weitere Informationen finden Sie unter [Aktualisieren auf AD FS unter Windows Server 2016 unter Verwendung einer WID-Datenbank](/windows-server/identity/ad-fs/deployment/upgrading-to-ad-fs-in-windows-server). Der Artikel behandelt sowohl das Upgrade Ihrer Farm auf AD FS 2019 als auch das Upgrade Ihrer FBL auf 4.

### <a name="configure-claims-rules-to-invoke-azure-ad-mfa"></a>Konfigurieren von Anspruchsregeln zum Aufrufen von Azure AD MFA

Da Azure AD MFA eine zusätzliche Authentifizierungsmethode ist, können Sie Gruppen von Benutzern zuweisen, die diese verwenden sollen. Konfigurieren Sie hierzu Anspruchsregeln, die auch als Vertrauensstellungen der vertrauenden Seite bezeichnet werden. Mithilfe von Gruppen können Sie steuern, welcher Authentifizierungsanbieter global oder je nach Anwendung aufgerufen wird. Beispielsweise können Sie Azure AD MFA für Benutzer aufrufen, die sich für kombinierte Sicherheitsinformationen registriert haben oder deren Telefonnummern migriert wurden, während für andere Benutzer der MFA-Server aufgerufen wird.

> [!NOTE]
> Anspruchsregeln erfordern eine lokale Sicherheitsgruppe. Bevor Sie Änderungen an Anspruchsregeln vornehmen, erstellen Sie eine Sicherungskopie.


#### <a name="back-up-existing-rules"></a>Sichern vorhandener Regeln

Sichern Sie ihre vorhandenen Regeln, bevor Sie neue Anspruchsregeln konfigurieren. Sie müssen diese Regeln im Rahmen der Bereinigung wiederherstellen. 

Je nach Konfiguration müssen Sie möglicherweise auch die vorhandene Regel kopieren und die neuen Regeln anfügen, die für die Migration erstellt werden.  

Zum Anzeigen vorhandener globaler Regeln führen Sie folgenden Befehl aus:  

```powershell
Get-AdfsAdditionalAuthenticationRule
```

Führen Sie zum Anzeigen vorhandener Vertrauensstellungen der vertrauenden Seite den folgenden Befehl aus, und ersetzen Sie „RPTrustName“ durch den Namen der Anspruchsregel für die Vertrauensstellung der vertrauenden Seite: 

```powershell
(Get-AdfsRelyingPartyTrust -Name "RPTrustName").AdditionalAuthenticationRules 
```

#### <a name="access-control-policies"></a>Zugriffssteuerungsrichtlinien

> [!NOTE]
> Zugriffssteuerungsrichtlinien können nicht so konfiguriert werden, dass auf Grundlage der Gruppenmitgliedschaft ein bestimmter Authentifizierungsanbieter aufgerufen wird. 

 
Um von Zugriffssteuerungsrichtlinien zu zusätzlichen Authentifizierungsregeln zu wechseln, führen Sie den folgenden Befehl für jede Ihrer Vertrauensstellungen der vertrauenden Seite mithilfe des MFA-Server-Authentifizierungsanbieters aus:


```powershell
Set-AdfsRelyingPartyTrust -TargetName AppA -AccessControlPolicyName $Null
```

 

Mit diesem Befehl wird die Logik aus Ihrer aktuellen Zugriffssteuerungsrichtlinie in zusätzliche Authentifizierungsregeln verschoben.


#### <a name="set-up-the-group-and-find-the-sid"></a>Einrichten der Gruppe und Suchen der SID

Sie benötigen eine bestimmte Gruppe, in der Sie Benutzer platzieren, für die Azure AD MFA aufgerufen werden soll. Sie benötigen die Sicherheits-ID (SID) für diese Gruppe.

Um die Gruppen-SID zu ermitteln, verwenden Sie den folgenden Befehl mit dem Namen der Gruppe:

`Get-ADGroup "GroupName"`

![Screenshot mit den Ergebnissen des Get-ADGroup-Skripts.](./media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/find-the-sid.png)

#### <a name="setting-the-claims-rules-to-call-azure-mfa"></a>Festlegen der Anspruchsregeln zum Aufrufen von Azure MFA

Die folgenden PowerShell-Cmdlets rufen Azure AD MFA für Benutzer in der Gruppe auf, sofern sie sich nicht im Unternehmensnetzwerk befinden. Ersetzen Sie „YourGroupSid“ durch die SID, die Sie mit obigem Cmdlet ermittelt haben.

Lesen Sie unbedingt die Informationen unter [Auswählen zusätzlicher Authentifizierungsanbieter in 2019](/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server). 

 > [!IMPORTANT]
> Sichern vorhandener Anspruchsregeln

 

#### <a name="set-global-claims-rule"></a>Festlegen einer globalen Anspruchsregel 

Führen Sie das folgende PowerShell-Cmdlet aus: 

```powershell
(Get-AdfsRelyingPartyTrust -Name "RPTrustName").AdditionalAuthenticationRules
```

 

Der Befehl gibt Ihre aktuellen zusätzlichen Authentifizierungsregeln für die Vertrauensstellung der vertrauenden Seite zurück. Fügen Sie die folgenden Regeln an Ihre aktuellen Anspruchsregeln an:

```console
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");'
```

Im folgenden Beispiel wird davon ausgegangen, dass Ihre aktuellen Anspruchsregeln so konfiguriert sind, dass sie zur MFA aufgefordert werden, wenn Benutzer eine Verbindung von außerhalb Ihres Netzwerks herstellen. Dieses Beispiel enthält die zusätzlichen Regeln, die Sie anfügen müssen.

```PowerShell
Set-AdfsAdditionalAuthenticationRule -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
 c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");'
```


#### <a name="set-per-application-claims-rule"></a>Festlegen einer anwendungsspezifischen Anspruchsregel

In diesem Beispiel werden Anspruchsregeln für eine bestimmte Vertrauensstellung der vertrauenden Seite (Anwendung) geändert, und es sind die Informationen enthalten, die Sie anfügen müssen.

```PowerShell
Set-AdfsRelyingPartyTrust -TargetName AppA -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");'
```


### <a name="configure-azure-ad-mfa-as-an-authentication-provider-in-ad-fs"></a>Konfigurieren von Azure AD MFA als Authentifizierungsanbieter in AD FS

Um Azure AD MFA für AD FS zu konfigurieren, müssen Sie jeden einzelnen AD FS-Server konfigurieren. Wenn Ihre Farm über mehrere AD FS-Server verfügt, können Sie diese remote mithilfe von Azure AD PowerShell konfigurieren.

Eine detaillierte Anleitung zu diesem Prozess finden Sie unter [Konfigurieren der AD FS-Server](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa) im Artikel [Konfigurieren von Azure MFA als Authentifizierungsanbieter mit AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa).

Nachdem Sie die Server konfiguriert haben, können Sie Azure AD MFA als zusätzliche Authentifizierungsmethode hinzufügen. 

![Screenshot des Bildschirms „Authentifizierungsmethoden bearbeiten“, auf dem die Optionen „Azure MFA“ und „Azure Multi-Factor Authentication-Server“ aktiviert sind](./media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/edit-authentication-methods.png)

## <a name="prepare-azure-ad-and-implement"></a>Vorbereiten von Azure AD und Implementierung

### <a name="ensure-supportsmfa-is-set-to-true"></a>Sicherstellen, dass „SupportsMFA“ auf „True“ festgelegt ist

Bei Verbunddomänen kann MFA durch bedingten Azure AD-Zugriff oder durch den lokalen Verbundanbieter erzwungen werden. Jede Verbunddomäne in Azure AD verfügt über ein SupportsMFA-Flag. Wenn das SupportsMFA-Flag auf „True“ festgelegt ist, leitet Azure AD Benutzer zu MFA in AD FS oder einem anderen Verbundanbieter um. Wenn ein Benutzer beispielsweise auf eine Anwendung zugreift, für die eine Richtlinie für bedingten Zugriff konfiguriert wurde, für die MFA erforderlich ist, wird der Benutzer zu AD FS weitergeleitet. Durch Hinzufügen von Azure AD MFA als Authentifizierungsmethode in AD FS kann Azure AD MFA aufgerufen werden, sobald Ihre Konfigurationen abgeschlossen sind.

Wenn das SupportsMFA-Flag auf „False“ festgelegt ist, verwenden Sie Azure MFA wahrscheinlich nicht. Sie verwenden wahrscheinlich Anspruchsregeln auf AD FS vertrauenden Seiten, um MFA aufzurufen.

Sie können den Status Ihres SupportsMFA-Flags mit dem folgenden [Windows PowerShell-Cmdlet](/powershell/module/msonline/get-msoldomainfederationsettings) überprüfen:

```powershell
Get-MsolDomainFederationSettings –DomainName yourdomain.com
```

Wenn das SupportsMFA-Flag auf „False“ festgelegt ist oder für Ihre Verbunddomäne leer ist, legen Sie es mit dem folgenden Windows PowerShell-Cmdlet fest:

```powershell
Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true
```

Diese Konfiguration erlaubt es, in AD FS zu entscheiden, ob MFA-Server oder Azure MFA verwendet werden soll.

### <a name="configure-conditional-access-policies-if-needed"></a>Konfigurieren von ggf. erforderlichen Richtlinien für bedingten Zugriff

Wenn Sie über den bedingten Zugriff bestimmen, wann Benutzer zur MFA aufgefordert werden, brauchen Sie Ihre Richtlinien in der Regel nicht zu ändern.

Wenn für Ihre Verbunddomänen SupportsMFA auf „False“ festgelegt ist, analysieren Sie Ihre Anspruchsregeln für die Azure AD-Vertrauensstellung der vertrauenden Seite und erstellen Richtlinien für bedingten Zugriff, die die gleichen Sicherheitsziele unterstützen.

Nachdem Sie Richtlinien für bedingten Zugriff erstellt haben, um die gleichen Kontrollen wie AD FS zu erzwingen, können Sie Ihre Anpassungen der Anspruchsregeln für die Azure AD-Vertrauensstellung der vertrauenden Seite sichern und entfernen.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Planen einer Bereitstellung für bedingten Zugriff](../conditional-access/plan-conditional-access.md)

* [Allgemeine Richtlinien für bedingten Zugriff](../conditional-access/concept-conditional-access-policy-common.md)


## <a name="register-users-for-azure-mfa"></a>Registrieren von Benutzern für Azure MFA

Es gibt zwei Möglichkeiten, Benutzer für Azure MFA zu registrieren: 

* Registrieren für kombinierte Sicherheit (MFA und Self-Service-Kennwortzurücksetzung) 

* Migrieren von Telefonnummern vom MFA-Server

Die Microsoft Authenticator-App kann für den kennwortlosen Modus verwendet werden. Sie kann auch bei beiden Registrierungsverfahren als zweiter Faktor für MFA verwendet werden.

### <a name="register-for-combined-security-registration-recommended"></a>Registrieren für die kombinierte Sicherheitsregistrierung (empfohlen)

Fordern Sie Benutzer auf, sich für kombinierte Sicherheitsinformationen registrieren. Dabei handelt es sich um eine zentrale Anlaufstelle zum Registrieren ihrer Authentifizierungsmethoden und Geräte für MFA und SSPR. Obwohl es möglich ist, Daten vom MFA-Server zu Azure AD MFA zu migrieren, ergeben sich dabei die folgenden Herausforderungen:

* Es können nur Telefonnummern migriert werden.

* Authenticator-Apps müssen erneut registriert werden.

* Es ist möglich, dass veraltete Daten migriert werden.

Microsoft stellt Kommunikationsvorlagen bereit, die Sie Ihren Benutzern zur Verfügung stellen können, um sie durch den kombinierten Registrierungsprozess zu führen. Dazu gehören Vorlagen für E-Mails, Poster, Tischaufsteller und andere Ressourcen. Benutzer registrieren ihre Informationen unter [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo), wodurch sie zum Bildschirm für die kombinierte Sicherheitsregistrierung weitergeleitet werden. 

Es wird empfohlen, den [Sicherheitsregistrierungsprozess mit bedingtem Zugriff zu schützen](../conditional-access/howto-conditional-access-policy-registration.md), sodass die Registrierung von einem vertrauenswürdigen Gerät oder Standort aus erfolgen muss.

> [!NOTE]
> Wenn Benutzer ihre kombinierten Sicherheitsinformationen von einem nicht vertrauenswürdigen Standort oder Gerät registrieren MÜSSEN, kann ein befristeter Zugriffspass für die Benutzer ausgestellt werden, oder sie werden vorübergehend von der Richtlinie ausgeschlossen.

### <a name="migrate-phone-numbers-from-mfa-server"></a>Migrieren von Telefonnummern vom MFA-Server

Die Migration von Telefonnummern kann auch dazu führen, dass veraltete Nummern migriert werden, wodurch die Wahrscheinlichkeit steigt, dass Benutzer bei der telefonbasierten MFA bleiben, anstatt sicherere Methoden wie die [kennwortlose Anmeldung mit Microsoft Authenticator](howto-authentication-passwordless-phone.md) einzurichten. Sie können keine Geräteregistrierungen migrieren, also auch nicht deren Microsoft Authenticator-App-Einstellungen. Es wird empfohlen, dass sich alle Benutzer für [kombinierte Sicherheitsinformationen](howto-registration-mfa-sspr-combined.md) registrieren. Im Rahmen der kombinierten Sicherheitsinformationen registrieren sich Benutzer auch für die Self-Service-Kennwortzurücksetzung.

Wenn die Registrierung für die kombinierten Sicherheitsinformationen durch die Benutzer nicht infrage kommt, ist es möglich, die Benutzer und ihre Telefonnummern vom MFA-Server zu exportieren und die Telefonnummern in Azure AD zu importieren.
 

#### <a name="export-user-phone-numbers-from-mfa-server"></a>Exportieren von Benutzertelefonnummern vom MFA-Server 

1. Öffnen Sie die Multi-Factor Authentication-Server-Verwaltungskonsole auf dem MFA-Server. 

1. Wählen Sie **Datei** und dann **Benutzer exportieren** aus.

1. **Speichern** Sie die CSV-Datei. Der Standarddateiname ist „Multi-Factor Authentication Users.csv“.

#### <a name="interpret-and-format-the-csv--file"></a>Interpretieren und Formatieren der CSV-Datei

Die CSV-Datei enthält viele Felder, die für die Migration nicht erforderlich sind. Bearbeiten und formatieren Sie sie, bevor Sie die Telefonnummern in Azure AD importieren. 

Beim Öffnen der CSV-Datei sind die folgenden Spalten von Interesse:

* Username
* Primary Phone
* Primary Country Code
* Landeskennzahl der Ersatzrufnummer
* Ersatzanschluss
* Durchwahl der Ersatzrufnummer

Sie müssen die Daten interpretieren, bereinigen und formatieren.

#### <a name="tips-to-avoid-errors-during-import"></a>Tipps, um Fehler während des Imports zu vermeiden

* Ändern Sie die CSV-Datei, bevor Sie die Authentifizierungsmethoden-API verwenden, um die Telefonnummern in Azure AD zu importieren. 

* Reduzieren Sie die CSV-Datei auf drei Spalten: UPN, PhoneType und PhoneNumber. 

* Stellen Sie sicher, dass der exportierte MFA-Server-Benutzername dem Azure AD-UserPrincipalName entspricht. Andernfalls aktualisieren Sie den Benutzernamen in der CSV-Datei so, dass er mit dem in Azure AD übereinstimmt, da sonst der Benutzer nicht gefunden wird.

Benutzer haben möglicherweise bereits Telefonnummern in Azure AD registriert. Wenn Sie die Telefonnummern mithilfe der Authentifizierungsmethoden-API importieren, müssen Sie entscheiden, ob die vorhandene Telefonnummer überschrieben oder die importierte Nummer als alternative Telefonnummer hinzugefügt werden soll.

Die folgenden PowerShell-Cmdlets verwenden die von Ihnen bereitgestellte CSV-Datei und fügen die exportierten Telefonnummern mithilfe der Authentifizierungsmethoden-API als Telefonnummer für jeden UPN hinzu. Ersetzen Sie „myPhones“ durch den Namen Ihrer CSV-Datei.

```powershell

$csv = import-csv myPhones.csv

$csv|% { New-MgUserAuthenticationPhoneMethod -UserId $_.UPN -phoneType $_.PhoneType -phoneNumber $_.PhoneNumber} 

```

### <a name="add-users-to-the-appropriate-groups"></a>Hinzufügen von Benutzern zu den entsprechenden Gruppen

* Wenn Sie neue Richtlinien für bedingten Zugriff erstellt haben, fügen Sie diesen Gruppen die entsprechenden Benutzer hinzu. 

* Wenn Sie lokale Sicherheitsgruppen für Anspruchsregeln erstellt haben, fügen Sie diesen Gruppen die entsprechenden Benutzer hinzu.

Es wird nicht empfohlen, Gruppen wiederzuverwenden, die zu Sicherheitszwecken verwendet werden. Wenn Sie also eine Sicherheitsgruppe verwenden, um eine Gruppe von wertvollen Apps über eine Richtlinie für bedingten Zugriff zu schützen, sollte die Gruppe ausschließlich zu diesem Zweck verwendet werden.

## <a name="monitoring"></a>Überwachung

Die Azure MFA-Registrierung kann mithilfe des Berichts [Authentifizierungsmethoden: Nutzung und Erkenntnisse](https://portal.azure.com/) überwacht werden. Diesen Bericht finden Sie in Azure AD. Wählen Sie **Überwachung** und dann **Nutzung & Erkenntnisse** aus. 

Wählen Sie unter „Nutzung und Erkenntnisse“ **Authentifizierungsmethoden** aus. 

Ausführliche Informationen zur Azure MFA-Registrierung finden Sie auf der Registerkarte „Registrierung“. Sie können einen Drilldown zum Anzeigen einer Liste registrierter Benutzer ausführen, indem Sie den Link **Benutzer bereit für Azure MFA** auswählen.

![Abbildung des Aktivitätsbildschirms „Authentifizierungsmethoden“ mit Benutzerregistrierungen bei MFA](./media/how-to-migrate-mfa-server-to-azure-mfa-with-federation/authentication-methods.png)

   

## <a name="clean-up-steps&quot;></a>Bereinigungsschritte

Nachdem Sie die Migration zu Azure MFA abgeschlossen haben und den MFA-Server außer Betrieb nehmen können, führen Sie die folgenden drei Schritte aus: 

1. Stellen Sie die Konfiguration Ihrer Anspruchsregeln für AD FS vor der Migration wieder her, und entfernen Sie den MFA-Server-Authentifizierungsanbieter.

1. Entfernen Sie den MFA-Server als Authentifizierungsanbieter in AD FS. Dadurch wird sichergestellt, dass alle Benutzer Azure MFA verwenden, da dies die einzige aktivierte zusätzliche Authentifizierungsmethode ist. 

1. Nehmen Sie den MFA-Server außer Betrieb.

### <a name=&quot;revert-claims-rules-on-ad-fs-and-remove-mfa-server-authentication-provider&quot;></a>Wiederherstellen der Anspruchsregeln für AD FS und Entfernen des MFA-Server-Authentifizierungsanbieters

Führen Sie die Schritte unter „Konfigurieren von Anspruchsregeln zum Aufrufen von Azure AD MFA“ aus, um die gesicherten Anspruchsregeln wiederherzustellen und alle AzureMFAServerAuthentication-Anspruchsregeln zu entfernen. 

Entfernen Sie beispielsweise Folgendes aus den Regeln: 

 
```console
c:[Type == &quot;https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid&quot;, Value ==
&quot;**YourGroupSID**&quot;] => issue(Type = &quot;https://schemas.microsoft.com/claims/authnmethodsproviders&quot;,
Value = &quot;AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
Value=="YourGroupSid"]) => issue(Type =
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value =
"AzureMfaServerAuthentication");'
```

### <a name="disable-mfa-server-as-an-authentication-provider-in-ad-fs"></a>Deaktivieren von MFA-Server als Authentifizierungsanbieter in AD FS

Durch diese Änderung wird sichergestellt, dass nur Azure MFA als Authentifizierungsanbieter verwendet wird.

1. Öffnen Sie die **AD FS-Verwaltungskonsole**.

1. Klicken Sie unter **Dienste**, mit der rechten Maustaste auf **Authentifizierungsmethoden**, und wählen Sie **Methoden für die mehrstufige Authentifizierung bearbeiten** aus. 

1. Deaktivieren Sie das Kontrollkästchen neben **Azure Multi-Factor Authentication-Server**. 

### <a name="decommission-the-mfa-server"></a>Außerbetriebnahme des MFA-Servers

Führen Sie den Außerbetriebsetzungsprozess ihres Unternehmensservers aus, um die MFA-Server in Ihrer Umgebung zu entfernen.

Berücksichtigen Sie ggf. Folgendes bei der Außerbetriebnahme des MFA-Servers: 

* Überprüfen Sie die Protokolle der MFA-Server, um sicherzustellen, dass sie von keinen Benutzern oder Anwendungen verwendet werden, bevor Sie den Server entfernen.

* Deinstallieren von Multi-Factor Authentication-Server aus der Systemsteuerung auf dem Server

* Bereinigen Sie optional verbliebene Protokolle und Datenverzeichnisse, nachdem Sie sie zunächst gesichert haben. 

* Deinstallieren Sie ggf. das Multi-Factor Authentication-Webserver-SDK, einschließlich aller Dateien, die sich noch in den Verzeichnissen „etpub\wwwroot\MultiFactorAuthWebServiceSdk“ und/oder „MultiFactorAuth“ befinden.

* Bis Version 8.0 des MFA-Server kann es auch erforderlich sein, den Multi-Factor Auth Phone App-Webdienst zu entfernen.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen der Kennworthashsynchronisierung](../hybrid/whatis-phs.md)
- [Weitere Informationen zum bedingten Zugriff](../conditional-access/overview.md)
- [Migrieren von Anwendungen zu Azure AD](../manage-apps/migrate-application-authentication-to-azure-active-directory.md)

 

 

 
