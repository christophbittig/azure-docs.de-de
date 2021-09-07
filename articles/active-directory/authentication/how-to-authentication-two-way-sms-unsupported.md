---
title: 'Die bidirektionale SMS wird nicht mehr unterstützt: Azure Active Directory'
description: Erläutert das Aktivieren einer anderen Methode für Benutzer, die weiterhin die bidirektionale SMS verwenden.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/19/2021
ms.author: justinha
author: rhicock
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c92095449132b790c80cf6396a3608f802d7e9a7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458502"
---
# <a name="two-way-sms-unsupported"></a>Die bidirektionale SMS nicht unterstützt

Die bidirektionale SMS für den Azure AD-MFA-Server (Multi-Factor Authentication) wurde ursprünglich im Jahr 2018 als veraltet eingestuft und wird nach dem 24. Februar 2021 nicht mehr unterstützt. Ausgenommen hiervon sind Organisationen, die eine Verlängerung des Supports bis zum 2. August 2021 erhalten haben. Administratoren sollten eine andere Methode für Benutzer aktivieren, die immer noch die bidirektionale SMS verwenden.

E-Mail-Benachrichtigungen und Service Health-Benachrichtigungen des Azure-Portals (Portalpopup) wurden am 8. Dezember 2020 und 28. Januar 2021 an betroffene Administratoren gesendet. Die Warnungen wurden an die RBAC-Rollen „Besitzer“, „Mitbesitzer“, „Administrator“ und „Dienstadministrator“ gesendet. Wenn Sie die folgenden Schritte bereits ausgeführt haben, ist keine Aktion erforderlich.

## <a name="required-actions"></a>Erforderliche Aktionen

1. Aktivieren Sie die mobile App für Ihre Benutzer, falls Sie dies noch nicht getan haben. Weitere Informationen finden Sie unter [Aktivieren der Mobile App-Authentifizierung mit dem Azure Multi-Factor Authentication-Server](howto-mfaserver-deploy-mobileapp.md).
1. Fordern Sie Ihre Endbenutzer auf, Ihr MFA-Server-[Benutzerportal](howto-mfaserver-deploy-userportal.md) zu besuchen, um die mobile App zu aktivieren. Die [Microsoft Authenticator-App](https://www.microsoft.com/en-us/account/authenticator) wird als Überprüfungsoption empfohlen, da sie sicherer als die bidirektionale SMS ist. Weitere Informationen finden Sie unter [It's Time to Hang Up on Phone Transports for Authentication](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752) (Die Zeit der Authentifizierung per Telefon ist vorbei).
1. Ändern Sie die Benutzereinstellungen von der bidirektionalen SMS als Standardmethode zur mobilen App.

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="what-if-i-dont-change-the-default-method-from-two-way-sms-to-the-mobile-app"></a>Was geschieht, wenn ich die Standardmethode nicht von der bidirektionalen SMS in die mobile App ändere?
Die bidirektionale SMS funktioniert nach dem 24. Februar 2021 nicht mehr. Benutzer erhalten eine Fehlermeldung, wenn sie versuchen, sich anzumelden und die MFA zu durchlaufen.

### <a name="how-do-i-change-the-user-settings-from-two-way-text-message-to-mobile-app"></a>Wie ändere ich die Benutzereinstellungen von der bidirektionalen SMS zur mobilen App?

Sie sollten die Benutzereinstellungen mit folgenden Schritten ändern:

1. Filtern Sie in MFA-Server die Benutzerliste nach der bidirektionalen SMS.
1. Wählen Sie alle Benutzer aus.
1. Öffnen Sie das Dialogfeld „Benutzer bearbeiten“.
1. Ändern Sie die Benutzer von „SMS“ in „Mobile App“.

   ![Bildschirm der Endbenutzer](media/how-to-authentication-two-way-sms-unsupported/end-users.png)

### <a name="do-my-users-need-to-take-any-action-if-yes-how"></a>Müssen meine Benutzer Maßnahmen ergreifen? Wenn ja, welche?
Ja. Ihre Endbenutzer müssen Ihr spezifisches MFA-Server-Benutzerportal besuchen, um die mobile App zu aktivieren, sofern sie dies noch nicht getan haben. Nachdem Sie Schritt 3 ausgeführt haben, können alle Benutzer, die nicht das Benutzerportal zum Einrichten der mobilen App besucht haben, sich nicht anmelden, bis sie das Benutzerportal besuchen, um sich erneut zu registrieren.

### <a name="what-if-my-users-cant-install-the-mobile-app-what-other-options-do-they-have"></a>Was ist, wenn meine Benutzer die mobile App nicht installieren können? Welche anderen Optionen haben sie?
Die Alternative zur bidirektionalen SMS oder der mobilen App ist ein Telefonanruf. Die Microsoft Authenticator-App ist jedoch die empfohlene Überprüfungsmethode.

### <a name="will-one-way-sms-be-deprecated-as-well"></a>Ist die unidirektionale SMS ebenfalls veraltet?
Nein, nur die bidirektionale SMS ist veraltet. In MFA-Server funktioniert eine unidirektionale SMS für eine Teilmenge von Szenarien:

- AD FS-Adapter
- IIS-Authentifizierung (erfordert Benutzerportal und Konfiguration)
- RADIUS (erfordert, dass RADIUS-Clients die Zugriffsanforderung unterstützen und das PAP-Protokoll verwendet wird)

Für den Fall, dass die unidirektionale SMS verwendet werden kann, gelten Einschränkungen, die die mobile App zur besseren Alternative machen, da sie keine Aufforderung zur Eingabe des Überprüfungscodes erfordert.
Wenn Sie in einigen Szenarien weiterhin die unidirektionale SMS verwenden möchten, können Sie diese aktiviert lassen, aber **Benutzer-Standard-SMS** im Abschnitt **Unternehmenseinstellungen** auf der Registerkarte **Allgemein** von **Bidirektional** in **Unidirektional** ändern. Wenn Sie schließlich die Verzeichnissynchronisierung verwenden, die standardmäßig SMS verwendet, müssen Sie dies von „Unidirektional“ in „Bidirektional“ ändern.

### <a name="how-can-i-check-which-users-are-still-using-two-way-sms"></a>Wie kann ich überprüfen, welche Benutzer weiterhin die bidirektionale SMS verwenden?
Um diese Benutzer aufzulisten, starten Sie **MFA-Server**, wählen Sie den Abschnitt **Benutzer** aus, klicken Sie auf **Benutzerliste filtern**, und filtern Sie nach **Bidirektionale SMS**.

### <a name="how-do-we-hide-two-way-sms-as-an-option-in-the-mfa-portal-to-prevent-users-from-selecting-it-in-the-future"></a>Wie wird die bidirektionale SMS als Option im MFA-Portal ausgeblendet, um Benutzer daran zu hindern, sie in Zukunft auszuwählen?
Klicken Sie im MFA-Server-Benutzerportal auf **Einstellungen**. Sie können **SMS** löschen, sodass diese Option nicht verfügbar ist. Das gleiche gilt auch für den **AD FS**-Abschnitt, wenn Sie AD FS für die Benutzerregistrierung verwenden.

