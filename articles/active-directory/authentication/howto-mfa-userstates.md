---
title: Benutzerspezifisches Aktivieren von Multi-Factor Authentication – Azure Active Directory
description: Erfahren Sie, wie Sie benutzerspezifische Azure AD Multi-Factor Authentication durch Ändern des Benutzerstatus aktivieren.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2e2212171f0be8d754ac1a86567641c2bad8a9a0
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602778"
---
# <a name="enable-per-user-azure-ad-multi-factor-authentication-to-secure-sign-in-events"></a>Aktivieren von benutzerspezifischer Azure AD Multi-Factor Authentication zum Schutz von Anmeldeereignissen

Um Benutzeranmeldeereignisse in Azure AD zu schützen, können Sie mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) anfordern. Der empfohlene Ansatz für den Schutz der Benutzer ist das Aktivieren von Azure AD Multi-Factor Authentication mit Richtlinien für bedingten Zugriff. Bedingter Zugriff ist ein Azure AD Premium P1- oder P2-Feature, mit dem Sie Regeln anwenden können, die in bestimmten Szenarien MFA erforderlich machen. Informationen zu den ersten Schritten mit bedingtem Zugriff finden Sie im [Tutorial: Schützen von Benutzeranmeldeereignissen mit Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

Für kostenlose Azure AD-Mandanten ohne bedingten Zugriff können Sie [Sicherheitsstandards zum Schützen von Benutzern verwenden](../fundamentals/concept-fundamentals-security-defaults.md). Benutzer werden ggf. zur Durchführung von MFA aufgefordert. Sie können jedoch keine eigenen Regeln definieren, um das Verhalten zu steuern.

Bei Bedarf können Sie stattdessen für die einzelnen Konten Azure AD Multi-Factor Authentication pro Benutzer aktivieren. Wenn Benutzer individuell aktiviert werden, führen sie die mehrstufige Authentifizierung bei jeder Anmeldung durch (bis auf einige Ausnahmen, beispielsweise wenn sie sich von vertrauenswürdigen IP-Adressen aus anmelden oder wenn das Feature zum _Speichern von MFA auf vertrauenswürdigen Geräten_ aktiviert ist).

Das Ändern der [Benutzerstatus](#azure-ad-multi-factor-authentication-user-states) wird nur empfohlen, wenn Ihre Azure AD-Lizenzen keinen bedingten Zugriff enthalten und Sie keine Standardeinstellungen für die Sicherheit verwenden möchten. Weitere Informationen zu den verschiedenen Möglichkeiten zum Aktivieren von MFA finden Sie unter [Features und Lizenzen für Azure AD Multi-Factor Authentication](concept-mfa-licensing.md).

> [!IMPORTANT]
>
> In diesem Artikel wird erläutert, wie Sie den Status für benutzerspezifische Azure AD Multi-Factor Authentication anzeigen und ändern. Wenn Sie bedingten Zugriff oder Sicherheitsstandardeinstellungen verwenden, führen Sie nicht die folgenden Schritte aus, um Benutzerkonten zu überprüfen oder zu aktivieren.
>
> Wenn Sie Azure AD Multi-Factor Authentication über eine Richtlinie für bedingten Zugriff aktivieren, wird dadurch der Status des Benutzers nicht geändert. Keine Sorge, falls Benutzer als deaktiviert angezeigt werden. Der Status wird durch bedingten Zugriff nicht geändert.
>
> **Aktivieren oder erzwingen Sie keine benutzerspezifische Azure AD Multi-Factor Authentication, wenn Sie Richtlinien für bedingten Zugriff verwenden.**

## <a name="azure-ad-multi-factor-authentication-user-states"></a>Benutzerstatus in Azure AD Multi-Factor Authentication

Der Status eines Benutzers gibt an, ob ein Administrator den Benutzer bei benutzerspezifischer Azure AD Multi-Factor Authentication registriert hat. Es gibt drei verschiedene Status für Benutzerkonten in Azure AD Multi-Factor Authentication:

| State | BESCHREIBUNG | Legacyauthentifizierung betroffen | Browser-Apps betroffen | Moderne Authentifizierung betroffen |
|:---:| --- |:---:|:--:|:--:|
| Disabled | Der Standardstatus eines Benutzers, der nicht bei benutzerspezifischer Azure AD Multi-Factor Authentication registriert ist. | Nein | Nein | Nein |
| Aktiviert | Der Benutzer ist bei benutzerspezifischer Azure AD Multi-Factor Authentication registriert, kann jedoch weiterhin sein Kennwort für die Legacyauthentifizierung verwenden. Wenn der Benutzer noch keine MFA-Authentifizierungsmethoden registriert hat, wird er beim nächsten Anmelden mit moderner Authentifizierung (z. B. über einen Webbrowser) zum Registrieren aufgefordert. | Nein. Legacyauthentifizierung wird weiterhin ausgeführt, bis die Registrierung abgeschlossen ist. | Ja. Nach Ablauf der Sitzung ist eine Registrierung bei Azure AD Multi-Factor Authentication erforderlich.| Ja. Nach Ablauf des Zugriffstokens ist eine Registrierung bei Azure AD Multi-Factor Authentication erforderlich. |
| Erzwungen | Der Benutzer wird bei Azure AD Multi-Factor Authentication (benutzerspezifisch) registriert. Wenn der Benutzer noch keine Authentifizierungsmethoden registriert hat, wird er beim nächsten Anmelden mit moderner Authentifizierung (z. B. über einen Webbrowser) zum Registrieren aufgefordert. Benutzern, die beim Durchführen der Registrierung den Status *Aktiviert* aufweisen, wird automatisch der Status *Erzwungen* zugewiesen. | Ja. Für Apps sind App-Kennwörter erforderlich. | Ja. Azure AD Multi-Factor Authentication ist bei der Anmeldung erforderlich. | Ja. Azure AD Multi-Factor Authentication ist bei der Anmeldung erforderlich. |

Der Anfangsstatus aller Benutzer lautet *Deaktiviert*. Wenn Sie Benutzer bei benutzerspezifischer Azure AD Multi-Factor Authentication registrieren, ändert sich der Status der Benutzer in *Aktiviert*. Wenn aktivierte Benutzer sich anmelden und den Registrierungsprozess abschließen, ändert sich ihr Status in *Erzwungen*. Administratoren können die Status der Benutzer ändern, z. B. von *Erzwungen* in *Aktiviert* oder *Deaktiviert*.

> [!NOTE]
> Wenn MFA pro Benutzer für einen Benutzer erneut aktiviert wird und sich der Benutzer nicht erneut registriert, ändert sich der MFA-Status auf der MFA-Verwaltungsoberfläche nicht von *Aktiviert* in *Erzwungen*. Der Administrator muss dem Benutzer *Erzwungen* direkt zuweisen.

## <a name="view-the-status-for-a-user"></a>Anzeigen des Status eines Benutzers

Führen Sie zum Anzeigen und Verwalten der Benutzerstatus die folgenden Schritte aus, um auf das Azure-Portal zuzugreifen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Administrator an.
1. Suchen und wählen Sie *Azure Active Directory* und dann **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie **Multi-Factor Authentication** aus. Scrollen Sie bei Bedarf nach rechts, um diese Menüoption anzuzeigen. Wählen Sie den unten gezeigten Beispielscreenshot aus, um das gesamte Fenster des Azure-Portals sowie die Menüposition anzuzeigen: [![Auswählen von „Multi-Factor Authentication“ im Fenster „Benutzer“ in Azure AD](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Eine neue Seite wird geöffnet, auf der, wie im folgenden Beispiel gezeigt, der Benutzerstatus angezeigt wird.
   ![Screenshot mit Beispielinformationen zum Benutzerstatus für Azure AD Multi-Factor Authentication](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Ändern des Status eines Benutzers

Führen Sie die folgenden Schritte aus, um den Status der benutzerspezifischen Azure AD Multi-Factor Authentication für einen Benutzer zu ändern:

1. Führen Sie die vorstehenden Schritte zum [Anzeigen des Status eines Benutzers](#view-the-status-for-a-user) aus, um zur Seite mit den **Benutzern** von Azure AD Multi-Factor Authentication zu gelangen.
1. Suchen Sie den Benutzer, für den Sie benutzerspezifische Azure AD Multi-Factor Authentication aktivieren möchten. Sie müssen möglicherweise oben die Ansicht in **Benutzer** ändern.
   ![Benutzer, für den der Status geändert werden soll, auf der Registerkarte „Benutzer“ auswählen](./media/howto-mfa-userstates/enable1.png)
1. Aktivieren Sie das Kontrollkästchen neben den Namen der Benutzer, deren Status geändert werden soll.
1. Wählen Sie auf der rechten Seite unter **QuickSteps** die Option **Aktivieren** oder **Deaktivieren** aus. Im folgenden Beispiel hat der Benutzer *John Smith* ein Häkchen neben seinem Namen und ist für die Verwendung aktiviert: ![Ausgewählten Benutzer durch Klicken auf „Aktivieren“ (im Menü „QuickSteps“) aktivieren](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Aktivierte* Benutzer werden automatisch in *Erzwungen* geändert, wenn sie sich für Azure AD Multi-Factor Authentication registrieren. Ändern Sie den Benutzerstatus nicht manuell in *Erzwungen*, es sei denn, der Benutzer ist bereits registriert, oder die Unterbrechung der Verbindung mit den Legacyauthentifizierungsprotokollen ist für den Benutzer akzeptabel.

1. Bestätigen Sie Ihre Auswahl im Popupfenster, das geöffnet wird.

Benachrichtigen Sie die Benutzer per E-Mail, nachdem Sie die Benutzer aktiviert haben. Teilen Sie den Benutzern mit, dass eine Aufforderung angezeigt wird, sich bei der nächsten Anmeldung zu registrieren. Und wenn Ihre Organisation auch nicht auf Browsern basierende Apps verwendet, die die moderne Authentifizierung nicht unterstützen, müssen die Benutzer App-Kennwörter erstellen. Weitere Informationen finden Sie im [Leitfaden zu Azure AD Multi-Factor Authentication für Endbenutzer](../user-help/multi-factor-authentication-end-user-first-time.md).

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Konvertieren von Benutzern von „MFA pro Benutzer“ zu „MFA mit bedingtem Zugriff“

Wenn für Ihre Benutzer die Azure AD Multi-Factor Authentication pro Benutzer aktiviert und erzwungen wurde, können Sie mithilfe des folgenden PowerShell-Codes eine Konvertierung in die Azure AD Multi-Factor Authentication mit bedingtem Zugriff vornehmen.

Führen Sie diese PowerShell-Instanz in einem ISE-Fenster aus, oder speichern Sie sie zur lokalen Ausführung als `.PS1`-Datei. Der Vorgang kann nur mithilfe des Moduls [MSOnline](/powershell/module/msonline/?view=azureadps-1.0#msonline) ausgeführt werden. 

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )
    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }
        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}
# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Konfigurieren der Einstellungen von Azure AD Multi-Factor Authentication finden Sie unter [Konfigurieren von Azure AD Multi-Factor Authentication-Einstellungen](howto-mfa-mfasettings.md).

Informationen zum Verwalten von Benutzereinstellungen für Azure AD Multi-Factor Authentication finden Sie unter [Verwalten von Benutzereinstellungen mit Azure AD Multi-Factor Authentication](howto-mfa-userdevicesettings.md).

Lesen Sie [Azure AD Multi-Factor Authentication-Berichte](howto-mfa-reporting.md), um zu verstehen, warum ein Benutzer zur Ausführung von MFA aufgefordert bzw. nicht aufgefordert wurde.