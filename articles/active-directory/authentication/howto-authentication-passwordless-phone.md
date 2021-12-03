---
title: 'Azure Active Directory: Kennwortlose Anmeldung mit der Microsoft Authenticator-App'
description: Aktivieren der kennwortlosen Anmeldung bei Azure AD mit der Microsoft Authenticator-App
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/12/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11e8af7f7e955e960644f2748087a86e1aa5b61f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484626"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app"></a>Aktivieren der kennwortlosen Anmeldung mit der Microsoft Authenticator-App (Vorschauversion) 

Mit der Microsoft Authenticator-App können sich Benutzer bei jedem beliebigen Azure AD-Konto anmelden, ohne ein Kennwort zu verwenden. Microsoft Authenticator aktiviert mit der schlüsselbasierten Authentifizierung Benutzeranmeldeinformationen, die an ein Gerät gebunden sind, wobei das Gerät eine PIN oder einen biometrischen Wert verwendet. [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) verwendet eine ähnliche Technologie.

Diese Authentifizierungstechnologie kann auf jeder Geräteplattform verwendet werden, einschließlich mobiler Geräte. Diese Technologie kann auch mit jeder App oder Website verwendet werden, die in Microsoft-Authentifizierungsbibliotheken integriert ist.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="Beispiel für eine browserbasierte Anmeldung, die der Benutzer bestätigen muss.":::

Personen, die die Anmeldung per Telefon über die Microsoft Authenticator-App aktiviert haben, sehen eine Meldung, in der sie aufgefordert werden, in ihrer App auf eine Zahl zu tippen. Es wird nicht nach Benutzername oder Kennwort gefragt. Um den Anmeldevorgang in der App abzuschließen, muss ein Benutzer als Nächstes die folgenden Aktionen ausführen:

1. Er muss die Zahl, die auf dem Anmeldebildschirm angezeigt wird, in das Dialogfeld der Microsoft Authenticator-App eingeben.
1. Klicken Sie auf **Genehmigen**.
1. Die PIN oder den biometrischen Wert bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen erfüllt sein, damit die kennwortlose Anmeldung per Telefon mit der Microsoft Authenticator-App verwendet werden kann:

- Empfohlen: Azure AD Multi-Factor Authentication ist mit Pushbenachrichtigungen als Überprüfungsmethode zulässig. Pushbenachrichtigungen an Ihr Smartphone oder Tablet tragen dazu bei, dass die Microsoft Authenticator-App nicht autorisierten Zugriff auf Konten verhindern und betrügerische Transaktionen stoppen kann. Die Authenticator-App generiert automatisch Codes, wenn sie für Pushbenachrichtigungen eingerichtet wurde. So hat der Benutzer selbst dann eine Sicherungsanmeldemethode, wenn sein Gerät keine Verbindung herstellen kann. 
- Aktuelle Version von Microsoft Authenticator auf Geräten mit iOS 8.0 oder höher oder Android 6.0 oder höher installieren.
- Das Gerät, auf dem die Microsoft Authenticator-App installiert ist, muss innerhalb des Azure AD-Tenants auf einen einzelnen Benutzer registriert sein. 

> [!NOTE]
> Wenn Sie die kennwortlose Anmeldung mit Microsoft Authenticator mithilfe von Azure AD PowerShell aktiviert haben, wurde sie für Ihr gesamtes Verzeichnis aktiviert. Wenn Sie die Aktivierung mit dieser neuen Methode vornehmen, wird die PowerShell-Richtlinie dadurch ersetzt. Es wird empfohlen, für alle Benutzer in Ihrem Mandanten die Aktivierung über das neue Menü *Authentifizierungsmethoden* vorzunehmen. Andernfalls können sich Benutzer, die nicht in der neuen Richtlinie enthalten sind, nicht mehr ohne Kennwort anmelden.

## <a name="enable-passwordless-authentication-methods"></a>Aktivieren von Methoden zur kennwortlosen Authentifizierung

Um die kennwortlose Authentifizierung in Azure AD zu verwenden, aktivieren Sie zunächst die kombinierte Registrierung und anschließend die Benutzer für die kennwortlose Methode.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Aktivieren von Authentifizierungsmethoden für die kennwortlose Anmeldung per Telefon

Mit Azure AD können Sie auswählen, welche Authentifizierungsmethoden während des Anmeldevorgangs verwendet werden können. Benutzer registrieren sich anschließend für die Methoden, die sie verwenden möchten. Bei der Authentifizierungsmethode mit **Microsoft Authenticator** sind sowohl die herkömmliche Push-MFA-Methode als auch die kennwortlose Authentifizierungsmethode möglich. 

Führen Sie die folgenden Schritte aus, um die Authentifizierungsmethode für die kennwortlose Anmeldung per Telefon zu aktivieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Konto für den *Authentifizierungsrichtlinienadministrator* an.
1. Suchen Sie nach *Azure Active Directory*, und wählen Sie die Option aus. Navigieren Sie anschließend zu **Sicherheit** > **Authentifizierungsmethoden** > **Richtlinien**.
1. Wählen Sie unter **Microsoft Authenticator** die folgenden Optionen aus:
   1. **Aktivieren**: „Ja“ oder „Nein“
   1. **Ziel**: „Alle Benutzer“ oder „Benutzer auswählen“
1. Bei jeder hinzugefügten Gruppe und jedem hinzugefügten Benutzer ist standardmäßig Microsoft Authenticator sowohl im kennwortlosen als auch im Pushbenachrichtigungsmodus („Beliebig“) aktiviert. Um dies zu ändern, führen Sie für jede Zeile folgende Schritte aus:
   1. Navigieren Sie zu **...**  > **Konfigurieren**.
   1. Wählen Sie für **Authentifizierungsmodus** die Option **Beliebig** oder **Kennwortlos** aus. Wenn Sie **Push** auswählen, wird die Verwendung der Anmeldeinformationen für die kennwortlose Anmeldung per Telefon verhindert. 
1. Wählen Sie **Speichern** aus, um die neue Richtlinie anzuwenden. 

   >[!NOTE]
   >Wenn Sie beim Speichern eine Fehlermeldung erhalten, kann die Ursache in der Anzahl der hinzugefügten Benutzer oder Gruppen liegen. Als Abhilfe können Sie die Benutzer und Gruppen, die Sie hinzufügen möchten, im selben Vorgang durch eine einzige Gruppe ersetzen und dann erneut auf **Speichern** klicken.

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Benutzerregistrierung und Verwaltung von Microsoft Authenticator

Benutzer registrieren sich mit den folgenden Schritten für die kennwortlose Authentifizierungsmethode von Azure AD:

1. Navigieren Sie zu [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Melden Sie sich an und klicken Sie dann auf **Methode hinzufügen** > **Authenticator-App** > **Hinzufügen**, um die Authenticator-App hinzuzufügen.
1. Folgen Sie den Anweisungen zum Installieren und Konfigurieren der Microsoft Authenticator-App auf Ihrem Gerät.
1. Wählen Sie **Fertig** aus, um die Authenticator-Konfiguration abzuschließen.
1. Wählen Sie in **Microsoft Authenticator** im Dropdownmenü für das registrierte Konto die Option **Anmeldung per Telefon aktivieren** aus.
1. Folgen Sie den Anweisungen in der App, um die Registrierung für die kennwortlose Anmeldung per Telefon für das Konto abzuschließen.

Eine Organisation kann ihre Benutzer so weiterleiten, dass sie sich mit ihrem Smartphone anmelden, ohne ein Kennwort zu verwenden. Weitere Unterstützung zum Konfigurieren der Microsoft Authenticator-App und Aktivieren der Anmeldung per Smartphone finden Sie unter [Anmelden bei Konten mithilfe der Microsoft Authenticator-App](https://support.microsoft.com/account-billing/sign-in-to-your-accounts-using-the-microsoft-authenticator-app-582bdc07-4566-4c97-a7aa-56058122714c).

> [!NOTE]
> Benutzer, die gemäß der Richtlinie nicht für die Verwendung der Anmeldung per Telefon berechtigt sind, können sie in der Microsoft Authenticator-App nicht mehr aktivieren.

## <a name="sign-in-with-passwordless-credential"></a>Anmelden mit kennwortlosen Anmeldeinformationen

Nachdem alle folgenden Aktionen abgeschlossen sind, kann ein Benutzer beginnen, die kennwortlose Anmeldung zu verwenden:

- Ein Administrator hat den Mandanten des Benutzers aktiviert.
- Der Benutzer hat die Microsoft Authenticator-App aktualisiert, um die Anmeldung per Telefon zu aktivieren.

Wenn ein Benutzer zum ersten Mal den Anmeldevorgang per Telefon startet, führt er die folgenden Schritte aus:

1. Eingabe des Namens auf der Anmeldeseite.
2. Auswahl von **Weiter**.
3. Wenn erforderlich, Auswahl von **Weitere Anmeldemethoden**.
4. Auswahl von **Eine Anforderung in meiner Microsoft Authenticator-App bestätigen**.

Dem Benutzer wird dann eine Zahl angezeigt. Die App fordert den Benutzer auf, sich zu authentifizieren, indem er die entsprechende Zahl eintippt, anstatt ein Kennwort einzugeben.

Nachdem der Benutzer die kennwortlose Anmeldung verwendet hat, leitet die App den Benutzer weiterhin durch diese Methode. Dem Benutzer wird jedoch die Option zum Auswählen einer anderen Methode angezeigt.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="Beispiel für eine Browseranmeldung mit der Microsoft Authenticator-App.":::

## <a name="known-issues"></a>Bekannte Probleme

Die folgenden Probleme sind bekannt.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Die Option für die kennwortlose Anmeldung per Telefon wird nicht angezeigt

In einem Szenario kann die Überprüfung der kennwortlosen Anmeldung des Benutzer ausstehen. Der Benutzer kann jedoch versuchen, sich erneut anzumelden. Wenn dies der Fall ist, wird dem Benutzer möglicherweise nur die Option zum Eingeben eines Kennworts angezeigt.

Mit folgenden Schritten kann dieses Szenario aufgelöst werden:

1. Öffnen Sie die Microsoft Authenticator-App.
2. Antworten Sie ggf. auf Benachrichtigungsaufforderungen.

Anschließend kann der Benutzer weiterhin die kennwortlose Anmeldung per Telefon verwenden.

### <a name="federated-accounts"></a>Verbundkonten

Wenn ein Benutzer kennwortlose Anmeldeinformationen aktiviert hat, verwendet der Azure AD-Anmeldevorgang nicht mehr den „login\_hint“. Darum leitet der Prozess den Benutzer nicht mehr zu einem Standort der Verbundanmeldung.

Mit dieser Logik wird in der Regel verhindert, dass ein Benutzer in einem hybriden Mandanten zur Anmeldungsüberprüfung an Active Directory-Verbunddienste (Active Directory Federated Services, AD FS) weitergeleitet wird. Der Benutzer kann jedoch weiterhin auf **Stattdessen Ihr Kennwort verwenden** klicken.

### <a name="azure-mfa-server"></a>Azure MFA-Server

Ein Endbenutzer kann über einen lokalen Azure MFA-Server für die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) aktiviert werden. Der Benutzer kann immer noch eine einzige Anmeldeinformation für die kennwortlose Anmeldung per Telefon erstellen und verwenden.

Wenn der Benutzer versucht, mehrere Installationen (5+) der Microsoft Authenticator-App mit den Anmeldeinformationen für die kennwortlose Anmeldung per Telefon zu aktualisieren, kann diese Änderung zu einem Fehler führen.

### <a name="device-registration"></a>Geräteregistrierung

Bevor Sie diese neuen starken Anmeldeinformationen erstellen können, müssen Sie Voraussetzungen erfüllen. Eine Voraussetzung ist, dass das Gerät, auf dem die Microsoft Authenticator-App installiert ist, im Azure AD-Mandanten für einen einzelnen Benutzer registriert sein muss.

Ein Gerät kann derzeit nur bei einem einzigen Mandanten registriert werden. Dies bedeutet, dass nur ein Geschäfts-, Schul- oder Unikonto in der Microsoft Authenticator-App für die Anmeldung per Smartphone aktiviert werden kann.

> [!NOTE]
> Die Geräteregistrierung ist nicht dasselbe wie die Geräteverwaltung oder mobile Geräteverwaltung (Mobile Device Management, MDM). Die Geräteregistrierung ordnet nur eine Geräte-ID und eine Benutzer-ID im Azure AD-Verzeichnis einander zu.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure AD-Authentifizierung und kennwortlosen Methoden finden Sie in den folgenden Artikeln:

- [Wie funktioniert die kennwortlose Authentifizierung?](concept-authentication-passwordless.md)
- [Informationen zur Geräteregistrierung](../devices/overview.md)
- [Informationen zu Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
