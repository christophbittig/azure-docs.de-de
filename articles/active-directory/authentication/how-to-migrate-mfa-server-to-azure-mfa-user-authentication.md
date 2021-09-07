---
title: Migrieren zu Azure AD MFA und Azure AD-Benutzerauthentifizierung – Azure Active Directory
description: Schritt-für-Schritt-Anleitung für den Umstieg von einem lokalen Azure MFA-Server zu Azure AD MFA und Azure AD-Benutzerauthentifizierung
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f10052f6bcb458b98535d54a9b2fa5deb749c38
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339267"
---
# <a name="migrate-to-azure-ad-mfa-and-azure-ad-user-authentication"></a>Migrieren zu Azure AD MFA und Azure AD-Benutzerauthentifizierung

Die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) schützt Ihre Infrastruktur und Ressourcen vor böswilligen Akteuren. Microsoft-Multi-Factor Authentication-Server (MFA-Server) wird für neue Bereitstellungen nicht mehr angeboten. Kunden, die MFA-Server verwenden, sollten zu Azure AD Multi-Factor Authentication (Azure AD MFA) wechseln. 

Es gibt mehrere Optionen für die Migration Ihrer mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) von MFA-Server zu Azure Active Directory (Azure AD). Dazu gehören:

* Gut: Ausschließliche Verschiebung des [MFA-Diensts nach Azure AD](how-to-migrate-mfa-server-to-azure-mfa.md). 
* Besser: Verschiebung des MFA-Diensts und der Benutzerauthentifizierung nach Azure AD (wird in diesem Artikel behandelt).
* Optimal: Verschiebung aller Anwendungen, des MFA-Diensts und der Benutzerauthentifizierung nach Azure AD. Informationen zum Verschieben von Anwendungen nach Azure AD finden Sie in diesem Artikel im Abschnitt zum Verschieben von Anwendungen nach Azure AD. 

Informationen zum Auswählen der geeigneten MFA-Migrationsoption für Ihre Organisation finden Sie unter [Migrieren von MFA-Server zu Azure Active Directory MFA](how-to-migrate-mfa-server-to-azure-mfa.md). 

Das folgende Diagramm veranschaulicht den Prozess für die Migration zu Azure AD MFA und Cloudauthentifizierung, während einige Anwendungen in AD FS verbleiben. Dieser Prozess ermöglicht die iterative Migration von Benutzern von MFA-Server zu Azure MFA basierend auf der Gruppenmitgliedschaft.

Die einzelnen Schritte werden in den nachfolgenden Abschnitten dieses Artikels erläutert.

>[!NOTE]
>Wenn Sie planen, Anwendungen im Rahmen dieser Migration nach Azure Active Directory zu verschieben, sollten Sie dies vor der MFA-Migration tun. Wenn Sie alle Apps verschieben, können Sie Teile des MFA-Migrationsprozesses überspringen. Weitere Informationen finden Sie im Abschnitt zum Verschieben von Anwendungen am Ende dieses Artikels.

## <a name="process-to-migrate-to-azure-ad-and-user-authentication"></a>Prozess zum Migrieren zu Azure AD und Benutzerauthentifizierung

![Prozess zum Migrieren zu Azure AD und Benutzerauthentifizierung.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/mfa-cloud-authentication-flow.png)

## <a name="prepare-groups-and-conditional-access"></a>Vorbereiten von Gruppen und bedingtem Zugriff

Gruppen werden in drei Kapazitäten für die MFA-Migration verwendet.
* **Für die iterative Verschiebung von Benutzern nach Azure AD MFA mit gestaffeltem Rollout.**
Verwenden Sie eine Gruppe, die in Azure AD erstellt wurde (wird auch als cloudbasierte Gruppe bezeichnet). Sie können Azure AD-Sicherheitsgruppen oder Microsoft 365-Gruppen für das Verschieben von Benutzern nach MFA und für Richtlinien für bedingten Zugriff verwenden.  Weitere Informationen finden Sie im Artikel zum Erstellen von Azure AD-Sicherheitsgruppe und der Übersicht über Microsoft 365-Gruppen für Administratoren.
  >[!IMPORTANT]
  >Geschachtelte und dynamische Gruppen werden im gestaffelten Rolloutprozess nicht unterstützt. Verwenden Sie diese Gruppentypen nicht bei einem gestaffelten Rollout.
* **Richtlinien für bedingten Zugriff**. Sie können entweder Azure AD-Gruppen oder lokale Gruppen für den bedingten Zugriff verwenden.
* **Zum Aufrufen von Azure AD MFA für AD FS mit Anspruchsregeln.**
Dies gilt nur, wenn Sie über Anwendungen in AD FS verfügen.
Hierbei muss es sich um eine Windows Server Active Directory-Sicherheitsgruppe handeln. Sobald Azure AD MFA als zusätzliche Authentifizierungsmethode verfügbar ist, können Sie Gruppen von Benutzern festlegen, die diese Methode für alle Vertrauensstellungen der vertrauenden Seite verwenden sollen. Sie können beispielsweise Azure AD MFA für Benutzer, die Sie bereits migriert haben, und MFA-Server für noch nicht migrierte Benutzer aufrufen. Dies ist für Tests und bei der Migration hilfreich. 

>[!NOTE] 
>Es wird nicht empfohlen, Gruppen wiederzuverwenden, die für Sicherheitszwecke verwendet werden. Wenn Sie eine Sicherheitsgruppe verwenden, um eine Gruppe von wertvollen Apps über eine Richtlinie für bedingten Zugriff zu schützen, sollte die betreffende Gruppe ausschließlich für diesen Zweck verwendet werden.

### <a name="configure-conditional-access-policies"></a>Konfigurieren von Richtlinien für bedingten Zugriff

Wenn Sie bereits den bedingten Zugriff verwenden, um zu bestimmen, wann Benutzer zur MFA aufgefordert werden, sind keine Änderungen an Ihren Richtlinien erforderlich. Wenn Benutzer zur Cloudauthentifizierung migriert werden, verwenden diese Azure AD MFA gemäß Definition in Ihren vorhandenen Richtlinien für bedingten Zugriff. Sie werden nicht mehr an AD FS und MFA-Server umgeleitet.

Wenn für Ihre Verbunddomänen „SupportsMFA“ auf „false“ festgelegt ist, erzwingen Sie wahrscheinlich MFA für AD FS mit Anspruchsregeln. In diesem Fall müssen Sie Ihre Anspruchsregeln für die Vertrauensstellung der Azure AD vertrauenden Seite analysieren und Richtlinien für bedingten Zugriff erstellen, die die gleichen Sicherheitsziele unterstützen.

Wenn Sie Richtlinien für bedingten Zugriff konfigurieren müssen, müssen Sie diese konfigurieren, bevor Sie den gestaffelten Rollout aktivieren. Weitere Informationen finden Sie in den folgenden Ressourcen:
* [Planen einer Bereitstellung für bedingten Zugriff](../conditional-access/plan-conditional-access.md)
* [Allgemeine Richtlinien für bedingten Zugriff](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="prepare-ad-fs"></a>Vorbereiten von AD FS 

Wenn Sie über keine Anwendungen in AD FS verfügen, die MFA erfordern, können Sie diesen Abschnitt überspringen und zum Abschnitt „Vorbereiten des gestaffelten Rollouts“ wechseln.

### <a name="upgrade-ad-fs-server-farm-to-2019-fbl-4"></a>Upgrade der AD FS-Serverfarm auf 2019, FBL 4

Im AD FS 2019 hat Microsoft neue Funktionen veröffentlicht, die die Möglichkeit bieten, zusätzliche Authentifizierungsmethoden für eine vertrauende Seite, z. B. eine Anwendung, anzugeben. Dies erfolgt über die Gruppenmitgliedschaft, um den Authentifizierungsanbieter zu bestimmen. Wenn Sie eine zusätzliche Authentifizierungsmethode angeben, können Sie zu Azure AD MFA wechseln und die andere Authentifizierung während des Übergangs beibehalten. 

Weitere Informationen finden Sie unter [Aktualisieren auf AD FS unter Windows Server 2016 unter Verwendung einer WID-Datenbank](/windows-server/identity/ad-fs/deployment/upgrading-to-ad-fs-in-windows-server). Der Artikel behandelt sowohl das Upgrade Ihrer Farm auf AD FS 2019 als auch das Upgrade Ihrer FBL auf 4.

### <a name="configure-claims-rules-to-invoke-azure-ad-mfa"></a>Konfigurieren von Anspruchsregeln zum Aufrufen von Azure AD MFA

Da Azure AD MFA jetzt als zusätzliche Authentifizierungsmethode verfügbar ist, können Sie Gruppen von Benutzern zuweisen, die Azure AD MFA verwenden sollen. Dazu konfigurieren Sie Anspruchsregeln, die auch als *Vertrauensstellungen der vertrauenden Seite* bezeichnet werden. Mithilfe von Gruppen können Sie steuern, welcher Authentifizierungsanbieter global oder von einer Anwendung aufgerufen wird. Sie können beispielsweise Azure AD MFA für Benutzer aufrufen, die sich für kombinierte Sicherheitsinformationen registriert haben oder deren Telefonnummern migriert wurden, während für andere Benutzer der MFA-Server aufgerufen wird. 

>[!NOTE]
>Anspruchsregeln erfordern eine lokale Sicherheitsgruppe. 

#### <a name="back-up-existing-rules"></a>Sichern vorhandener Regeln 

Sichern Sie ihre vorhandenen Regeln, bevor Sie neue Anspruchsregeln konfigurieren. Sie müssen diese im Rahmen Ihrer Bereinigungsschritte wiederherstellen. 

Je nach Konfiguration müssen Sie möglicherweise auch die vorhandene Regel kopieren und die neuen Regeln anfügen, die für die Migration erstellt werden.

Zum Anzeigen vorhandener globaler Regeln führen Sie folgenden Befehl aus:  
```powershell
Get-AdfsAdditionalAuthenticationRule
```

Führen Sie zum Anzeigen vorhandener Vertrauensstellungen der vertrauenden Seite den folgenden Befehl aus, und ersetzen Sie „RPTrustName“ durch den Namen der Anspruchsregel für die Vertrauensstellung der vertrauenden Seite: 

```powershell
(Get-AdfsRelyingPartyTrust -Name “RPTrustName”).AdditionalAuthenticationRules
```

#### <a name="access-control-policies"></a>Zugriffssteuerungsrichtlinien

>[!NOTE]
>Zugriffssteuerungsrichtlinien können nicht so konfiguriert werden, dass auf Grundlage der Gruppenmitgliedschaft ein bestimmter Authentifizierungsanbieter aufgerufen wird. 

Um von Zugriffssteuerungsrichtlinien zu zusätzlichen Authentifizierungsregeln zu wechseln, führen Sie den folgenden Befehl für jede Vertrauensstellung der vertrauenden Seite unter Verwendung des MFA-Server-Authentifizierungsanbieters aus:

```powershell
Set-AdfsRelyingPartyTrust -**TargetName AppA -AccessControlPolicyName $Null**
```

Mit diesem Befehl wird die Logik aus Ihrer aktuellen Zugriffssteuerungsrichtlinie in zusätzliche Authentifizierungsregeln verschoben.

#### <a name="set-up-the-group-and-find-the-sid"></a>Einrichten der Gruppe und Ermitteln der SID

Sie benötigen eine bestimmte Gruppe, in der Sie die Benutzer einfügen, für die Azure AD MFA aufgerufen werden soll. Sie benötigen die Sicherheits-ID (SID) für diese Gruppe.
Um die Gruppen-SID zu ermitteln, verwenden Sie den folgenden Befehl mit Ihrem Gruppennamen: `Get-ADGroup “GroupName”`.

![PowerShell-Befehl zum Abrufen der Gruppen-SID.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/find-the-sid.png)

#### <a name="setting-the-claims-rules-to-call-azure-mfa"></a>Festlegen der Anspruchsregeln zum Aufrufen von Azure MFA

Die folgenden PowerShell-Cmdlets rufen Azure AD MFA für diejenigen in der Gruppe auf, wenn diese sich nicht im Unternehmensnetzwerk befinden. Sie müssen „YourGroupSid“ durch die ermittelte SID ersetzen, indem Sie das oben genannte Cmdlet ausführen.

Lesen Sie unbedingt die Informationen unter [Auswählen zusätzlicher Authentifizierungsanbieter in 2019](/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server#how-to-choose-additional-auth-providers-in-2019). 

>[!IMPORTANT] 
>Sichern Sie Ihre vorhandenen Anspruchsregeln, bevor Sie fortfahren.

##### <a name="set-global-claims-rule"></a>Festlegen einer globalen Anspruchsregel 

Führen Sie den folgenden Befehl aus, und ersetzen Sie „RPTrustName“ durch den Namen der Anspruchsregel für die Vertrauensstellung der vertrauenden Seite: 

```powershell
(Get-AdfsRelyingPartyTrust -Name “RPTrustName”).AdditionalAuthenticationRules
```

Der Befehl gibt Ihre aktuellen zusätzlichen Authentifizierungsregeln für die Vertrauensstellung der vertrauenden Seite zurück.  
Sie müssen die folgenden Regeln an Ihre aktuellen Anspruchsregeln anfügen:

```console
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```

Im folgenden Beispiel wird davon ausgegangen, dass Ihre aktuellen Anspruchsregeln so konfiguriert sind, dass sie zur MFA aufgefordert werden, wenn Benutzer eine Verbindung von außerhalb Ihres Netzwerks herstellen. Dieses Beispiel enthält die zusätzlichen Regeln, die Sie anfügen müssen.

```PowerShell
Set-AdfsAdditionalAuthenticationRule -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
 c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
“YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value==“YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```

##### <a name="set-per-application-claims-rule"></a>Festlegen einer anwendungsspezifischen Anspruchsregel

In diesem Beispiel werden Anspruchsregeln für eine bestimmte Vertrauensstellung der vertrauenden Seite (Anwendung) geändert. Es umfasst die zusätzlichen Regeln, die Sie anfügen müssen.

```PowerShell
Set-AdfsRelyingPartyTrust -TargetName AppA -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
“YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value==“YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```

### <a name="configure-azure-ad-mfa-as-an-authentication-provider-in-ad-fs"></a>Konfigurieren von Azure AD MFA als Authentifizierungsanbieter in AD FS

Um Azure AD MFA für AD FS zu konfigurieren, müssen Sie jeden einzelnen AD FS-Server konfigurieren. Wenn Ihre Farm über mehrere AD FS-Server verfügt, können Sie diese remote mithilfe von Azure AD PowerShell konfigurieren.

Eine Schritt-für-Schritt-Anleitung für diesen Prozess finden Sie unter [Konfigurieren der AD FS-Server](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Nachdem Sie die Server konfiguriert haben, können Sie Azure AD MFA als zusätzliche Authentifizierungsmethode hinzufügen. 

![Screenshot: Hinzufügen von Azure AD MFA als zusätzliche Authentifizierungsmethode](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/edit-authentication-methods.png)


## <a name="prepare-staged-rollout"></a>Vorbereiten des gestaffelten Rollouts 

Jetzt können Sie das Feature für den gestaffelten Rollout aktivieren. Gestaffelte Rollouts ermöglichen eine iterativ Verschiebung von Benutzer zu PHS oder PTA. 

* Informieren Sie sich über die [unterstützten Szenarien](../hybrid/how-to-connect-staged-rollout.md#supported-scenarios). 
* Zuerst müssen Sie die [Vorarbeiten für PHS](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-password-hash-sync) oder die [Vorarbeiten für PTA](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-pass-through-authentication) erledigen. Wir empfehlen PHS. 
* Danach müssen Sie die [Vorarbeiten für nahtloses einmaliges Anmelden (SSO)](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-seamless-sso) erledigen. 
* [Ermöglichen Sie den gestaffelten Rollout der Cloudauthentifizierung](../hybrid/how-to-connect-staged-rollout.md#enable-a-staged-rollout-of-a-specific-feature-on-your-tenant) für die ausgewählte Authentifizierungsmethode. 
* Fügen Sie die Gruppe(n) hinzu, die Sie für den gestaffelten Rollout erstellt haben. Denken Sie daran, dass Sie Benutzer iterativ zu Gruppen hinzufügen und dass es sich nicht um dynamische Gruppen oder geschachtelte Gruppen handeln darf. 

## <a name="register-users-for-azure-mfa"></a>Registrieren von Benutzern für Azure MFA

Es gibt zwei Möglichkeiten, Benutzer für Azure MFA zu registrieren: 

* Registrieren für kombinierte Sicherheit (MFA und Self-Service-Kennwortzurücksetzung) 
* Migrieren von Telefonnummern von MFA-Server

Bei beiden Methoden kann die Microsoft Authenticator-App als kennwortlose Anmeldemethode sowie als zweiter Faktor für MFA verwendet werden.

### <a name="register-for-combined-security-registration-recommended"></a>Registrieren für die kombinierte Sicherheitsregistrierung (empfohlen)

Es wird empfohlen, dass sich Ihre Benutzer für kombinierte Sicherheitsinformationen registrieren. Dabei handelt es sich um eine zentrale Anlaufstelle zum Registrieren ihrer Authentifizierungsmethoden und Geräte für MFA und SSPR. Obwohl es möglich ist, Daten von MFA-Server zu Azure AD MFA zu migrieren, gibt es dabei folgende Schwierigkeiten:

* Es können nur Telefonnummern migriert werden.
* Authenticator-Apps müssen erneut registriert werden.
* Es ist möglich, dass veraltete Daten migriert werden.

Microsoft stellt Kommunikationsvorlagen bereit, die Sie Ihren Benutzern zur Verfügung stellen können, um sie durch den kombinierten Registrierungsprozess zu führen. Dazu gehören Vorlagen für E-Mails, Poster, Tischaufsteller und verschiedene andere Ressourcen. Benutzer registrieren ihre Informationen unter `https://aka.ms/mysecurityinfo`, wodurch sie zum Bildschirm für die kombinierte Sicherheitsregistrierung weitergeleitet werden. 

Es wird empfohlen, den [Sicherheitsregistrierungsprozess mit bedingtem Zugriff zu schützen](../conditional-access/howto-conditional-access-policy-registration.md), sodass die Registrierung von einem vertrauenswürdigen Gerät oder Standort aus erfolgen muss. Informationen zum Nachverfolgen des Registrierungsstatus finden Sie unter [Aktivität für Authentifizierungsmethoden für Azure Active Directory](howto-authentication-methods-activity.md).
> [!NOTE]
> Wenn Benutzer ihre kombinierten Sicherheitsinformationen von einem nicht vertrauenswürdigen Standort oder Gerät registrieren MÜSSEN, kann ein befristeter Zugriffspass ausgestellt werden. Alternativ können Benutzer vorübergehend aus der Richtlinie ausgeschlossen werden.

### <a name="migrate-phone-numbers-from-mfa-server"></a>Migrieren von Telefonnummern von MFA-Server

Sie können zwar die registrierten MFA-Telefonnummern und Hardwaretoken von Benutzern, nicht aber Geräteregistrierungen wie deren Microsoft Authenticator App-Einstellungen migrieren. Die Migration von Telefonnummern kann auch dazu führen, dass veraltete Nummern migriert werden, wodurch die Wahrscheinlichkeit steigt, dass Benutzer bei der telefonbasierten MFA bleiben, anstatt sicherere Methoden wie die [kennwortlose Anmeldung mit Microsoft Authenticator](howto-authentication-passwordless-phone.md) einzurichten. Daher wird empfohlen, dass sich alle Benutzer unabhängig vom gewählten Migrationspfad für [kombinierte Sicherheitsinformationen](howto-registration-mfa-sspr-combined.md) registrieren. Kombinierte Sicherheitsinformationen bieten Benutzern die Möglichkeit, sich auch für die Self-Service-Kennwortzurücksetzung zu registrieren.

Wenn eine Registrierung der Benutzer für kombinierte Sicherheitsinformationen nicht infrage kommt, ist es möglich, die Benutzer mit ihren Telefonnummern von MFA-Server zu exportieren und die Telefonnummern in Azure AD zu importieren. 

#### <a name="export-user-phone-numbers-from-mfa-server"></a>Exportieren von Benutzertelefonnummern von MFA-Server 

1. Öffnen Sie die Multi-Factor Authentication-Server-Verwaltungskonsole auf dem MFA-Server. 
1. Wählen Sie **Datei** > **Benutzer exportieren** aus.
3)  Speichern Sie die CSV-Datei. Der Standarddateiname ist „Multi-Factor Authentication Users.csv“.

#### <a name="interpret-and-format-the-csv-file"></a>Interpretieren und Formatieren der CSV-Datei

Die CSV-Datei enthält mehrere Felder, die für die Migration nicht erforderlich sind, sodass sie vor dem Importieren der Telefonnummern in Azure AD bearbeitet und formatiert werden muss. 

Beim Öffnen der CSV-Datei sind folgende Spalten von Interesse: Username, Primary Phone, Primary Country Code, Backup Country Code, Backup Phone, Backup Extension. Sie müssen diese Daten interpretieren und ggf. formatieren.

#### <a name="tips-to-avoid-errors-during-import"></a>Tipps, um Fehler während des Imports zu vermeiden

* Die CSV-Datei muss vor der Verwendung der Authentifizierungsmethoden-API geändert werden, um die Telefonnummern in Azure AD zu importieren. 
* Es wird empfohlen, die CSV-Datei auf drei Spalten zu reduzieren: UPN, PhoneType und PhoneNumber. 

  ![Screenshot: CSV-Beispiel.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/csv-example.png)

* Stellen Sie sicher, dass der exportierte MFA-Server-Benutzername dem Azure AD-UserPrincipalName entspricht. Aktualisieren Sie andernfalls den Benutzernamen in der CSV-Datei so, dass er mit dem in Azure AD übereinstimmt, da sonst der Benutzer nicht gefunden wird.

Benutzer haben möglicherweise bereits Telefonnummern in Azure AD registriert. Wenn Sie die Telefonnummern mithilfe der Authentifizierungsmethoden-API importieren, müssen Sie entscheiden, ob die vorhandene Telefonnummer überschrieben oder die importierte Nummer als alternative Telefonnummer hinzugefügt werden soll.

Die folgenden PowerShell-Cmdlets verwenden die von Ihnen bereitgestellte CSV-Datei und fügen die exportierten Telefonnummern mithilfe der Authentifizierungsmethoden-API als Telefonnummer für jeden UPN hinzu. Sie müssen „myPhones“ durch den Namen Ihrer CSV-Datei ersetzen.


```powershell
$csv = import-csv myPhones.csv
$csv|% { New-MgUserAuthenticationPhoneMethod -UserId $_.UPN -phoneType $_.PhoneType -phoneNumber $_.PhoneNumber} 
```

Weitere Informationen zum Verwalten der Authentifizierungsmethoden von Benutzern finden Sie unter [Verwalten von Authentifizierungsmethoden für Azure AD Multi-Factor Authentication](howto-mfa-userdevicesettings.md).

### <a name="add-users-to-the-appropriate-groups"></a>Hinzufügen von Benutzern zu den entsprechenden Gruppen 

* Wenn Sie neue Richtlinien für bedingten Zugriff erstellt haben, fügen Sie diesen Gruppen die entsprechenden Benutzer hinzu. 
* Wenn Sie lokale Sicherheitsgruppen für Anspruchsregeln erstellt haben, fügen Sie diesen Gruppen die entsprechenden Benutzer hinzu. 
* Fügen Sie der Gruppe, die Sie für den gestaffelten Rollout erstellt haben, Benutzer erst dann hinzu, nachdem Sie den entsprechenden Regeln für bedingten Zugriff Benutzer hinzugefügt haben. Anschließend verwenden die Benutzer die ausgewählte Azure-Authentifizierungsmethode (PHS oder PTA) und Azure AD MFA, wenn sie eine mehrstufige Authentifizierung durchführen müssen.

> [!IMPORTANT] 
> Geschachtelte und dynamische Gruppen werden im gestaffelten Rolloutprozess nicht unterstützt. Verwenden Sie keine derartigen Gruppen. 

Es wird nicht empfohlen, Gruppen wiederzuverwenden, die für Sicherheitszwecke verwendet werden. Wenn Sie also eine Sicherheitsgruppe verwenden, um eine Gruppe von wertvollen Apps über eine Richtlinie für bedingten Zugriff zu schützen, sollte die Gruppe ausschließlich zu diesem Zweck verwendet werden.

## <a name="monitoring"></a>Überwachung

Zur Überwachung Ihrer Bereitstellung stehen mehrere [Azure Monitor-Arbeitsmappen](../reports-monitoring/howto-use-azure-monitor-workbooks.md) sowie Nutzungs- und Erkenntnisberichte zur Verfügung. Diese finden Sie in Azure AD im Navigationsbereich unter **Überwachung**. 

### <a name="monitoring-staged-rollout"></a>Überwachen des gestaffelten Rollouts

Wählen Sie im Abschnitt [Arbeitsmappen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Workbooks) die Option **Öffentliche Vorlagen** aus. Wählen Sie im Abschnitt **Hybridauthentifizierung** die Arbeitsmappe **Gruppen, Benutzer und Anmeldungen im gestaffeltem Rollout** aus.

Mit dieser Arbeitsmappe kann Folgendes überwacht werden: 
* Benutzer und Gruppen, die dem gestaffelten Rollout hinzugefügt wurden
* Benutzer und Gruppen, die aus dem gestaffelten Rollout entfernt wurden
* Anmeldefehler für Benutzer im gestaffelten Rollout und die jeweiligen Fehlerursachen

### <a name="monitoring-azure-mfa-registration"></a>Überwachen der Azure MFA-Registrierung
Die Azure MFA-Registrierung kann mithilfe des Berichts [Authentifizierungsmethoden: Nutzung und Erkenntnisse](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthenticationMethodsMenuBlade/AuthMethodsActivity/menuId/AuthMethodsActivity) überwacht werden. Diesen Bericht finden Sie in Azure AD. Wählen Sie **Überwachung** und dann **Nutzung & Erkenntnisse** aus. 

![Screenshot: Suchen des Berichts zu Nutzung und Erkenntnissen.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/usage-report.png)

Wählen Sie unter „Nutzung und Erkenntnisse“ die Option **Authentifizierungsmethoden** aus. 

Ausführliche Informationen zur Azure MFA-Registrierung finden Sie auf der Registerkarte „Registrierung“. Sie können einen Drilldown zum Anzeigen einer Liste registrierter Benutzer ausführen, indem Sie den Link **Registrierte Benutzer für Azure MFA** auswählen.

![Screenshot: Registerkarte „Registrierung“.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/registration-tab.png)

### <a name="monitoring-app-sign-in-health"></a>Überwachen der Integrität von App-Anmeldungen

Überwachen Sie Anwendungen, die Sie nach Azure AD verschoben haben, mit der Arbeitsmappe für die Integrität von App-Anmeldungen oder dem Nutzungsbericht zu Anwendungsaktivitäten.

* **Arbeitsmappe für die Integrität von App-Anmeldungen**. Ausführliche Anleitungen zur Verwendung dieser Arbeitsmappe finden Sie unter [Überwachen der Anwendungsanmeldeintegrität für mehr Resilienz](../fundamentals/monitor-sign-in-health-for-resilience.md).
* **Azure AD-Nutzungsbericht zu Anwendungsaktivitäten**. Dieser [Bericht](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsageAndInsightsMenuBlade/Azure%20AD%20application%20activity) kann verwendet werden, um die erfolgreichen und fehlgeschlagenen Anmeldungen für einzelne Anwendungen verwendet werden. Er ermöglicht außerdem das Ausführen von Drilldowns und das Anzeigen von Anmeldeaktivitäten für eine bestimmte Anwendung. 

## <a name="clean-up-tasks"></a>Bereinigungsaufgaben

Nachdem Sie alle Ihre Benutzer in die Azure AD-Cloudauthentifizierung und Azure MFA verschoben haben, sollten Sie in der Lage sein, Ihren MFA-Server außer Betrieb zu setzen. Es wird empfohlen, die Protokolle von MFA-Server zu überprüfen, um sicherzustellen, dass der Server von keinen Benutzern oder Anwendungen verwendet wird, bevor Sie den Server entfernen.

### <a name="convert-your-domains-to-managed-authentication"></a>Konvertieren Ihrer Domänen in die verwaltete Authentifizierung

Sie sollten jetzt [Ihre Verbunddomänen in Azure AD in verwaltete konvertieren](../hybrid/migrate-from-federation-to-cloud-authentication.md#convert-domains-from-federated-to-managed) und die gestaffelte Rolloutkonfiguration entfernen. Dadurch wird sichergestellt, dass neue Benutzer die Cloudauthentifizierung verwenden, ohne dass sie den Migrationsgruppen hinzugefügt werden müssen.

### <a name="revert-claims-rules-on-ad-fs-and-remove-mfa-server-authentication-provider"></a>Wiederherstellen der Anspruchsregeln für AD FS und Entfernen des MFA-Server-Authentifizierungsanbieters

Führen Sie die Schritte unter [Konfigurieren von Anspruchsregeln zum Aufrufen von Azure AD MFA](#configure-claims-rules-to-invoke-azure-ad-mfa) aus, um die gesicherten Anspruchsregeln wiederherzustellen und alle AzureMFAServerAuthentication-Anspruchsregeln zu entfernen. 

Entfernen Sie beispielsweise Folgendes aus den Regeln: 

```console
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value ==
“**YourGroupSID**"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders",
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
Value=="YourGroupSid"]) => issue(Type =
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value =
"AzureMfaServerAuthentication");’
```


### <a name="disable-mfa-server-as-an-authentication-provider-in-ad-fs"></a>Deaktivieren von MFA-Server als Authentifizierungsanbieter in AD FS

Durch diese Änderung wird sichergestellt, dass nur Azure MFA als Authentifizierungsanbieter verwendet wird.

1. Öffnen Sie die **AD FS-Verwaltungskonsole**.
1. Klicken Sie unter **Dienste** mit der rechten Maustaste auf **Authentifizierungsmethoden**, und wählen Sie **Methoden für die mehrstufige Authentifizierung bearbeiten** aus. 
1. Deaktivieren Sie das Kontrollkästchen **Azure Multi-Factor Authentication-Server**. 

### <a name="decommission-the-mfa-server"></a>Außerbetriebnahme des MFA-Servers

Führen Sie den Außerbetriebnahmeprozess ihres Unternehmensservers aus, um die MFA-Server in Ihrer Umgebung zu entfernen.

Berücksichtigen Sie folgende Punkte bei der Außerbetriebnahme des MFA-Servers: 

* Es wird empfohlen, die Protokolle von MFA-Server zu überprüfen, um sicherzustellen, dass der Server von keinen Benutzern oder Anwendungen verwendet wird, bevor Sie den Server entfernen.
* Deinstallieren Sie Multi-Factor Authentication-Server über die Systemsteuerung auf dem Server.
* Bereinigen Sie optional verbliebene Protokolle und Datenverzeichnisse, nachdem Sie sie zunächst gesichert haben. 
* Deinstallieren Sie ggf. das Multi-Factor Authentication-Webserver-SDK, einschließlich aller Dateien, die sich noch in den Verzeichnissen „inetpub\wwwroot\MultiFactorAuthWebServiceSdk“ und/oder „MultiFactorAuth“ befinden.
* Bei Versionen von MFA-Server vor 8.0.x kann es auch erforderlich sein, den Multi-Factor Auth Phone App-Webdienst zu entfernen.

## <a name="move-application-authentication-to-azure-active-directory"></a>Verschieben der Anwendungsauthentifizierung nach Azure Active Directory

Wenn Sie Ihre gesamte Anwendungsauthentifizierung zusammen mit Ihrer MFA- und Benutzerauthentifizierung migrieren, können Sie wesentliche Teile Ihrer lokalen Infrastruktur entfernen und so Kosten sowie Risiken reduzieren. Wenn Sie die gesamte Anwendungsauthentifizierung verschieben, können Sie die Phase [Vorbereiten von AD FS](#prepare-ad-fs) überspringen und die MFA-Migration vereinfachen.

Der Prozess zum Verschieben der gesamten Anwendungsauthentifizierung ist im folgenden Diagramm dargestellt.

![Prozess zum Migrieren von Anwendungen zu Azure AD MFA.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/mfa-app-migration-flow.png)

Wenn es nicht möglich ist, alle Anwendungen vor der Migration zu verschieben, verschieben Sie die Anwendungen, für die eine Verschiebung möglich ist, bevor Sie die Migration beginnen.
Weitere Informationen zum Migrieren von Anwendungen zu Azure finden Sie unter [Ressourcen zum Migrieren von Anwendungen zu Azure Active Directory](../manage-apps/migration-resources.md).

## <a name="next-steps"></a>Nächste Schritte

- [Migrieren von Azure MFA-Server zu Azure Multi-Factor Authentication (Übersicht)](how-to-migrate-mfa-server-to-azure-mfa.md)
- [Migrieren von Anwendungen von Windows Active Directory zu Azure Active Directory](../manage-apps/migrate-application-authentication-to-azure-active-directory.md)
- [Planen der Cloudauthentifizierungsstrategie](../fundamentals/active-directory-deployment-plans.md)