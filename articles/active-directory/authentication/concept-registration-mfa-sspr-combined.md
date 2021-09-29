---
title: Kombinierte Registrierung für SSPR und Azure AD Multi-Factor Authentication – Azure Active Directory
description: Hier erfahren Sie mehr über die Umgebung für die kombinierte Registrierung für Azure Active Directory, mit der sich Benutzer sowohl für Azure AD Multi-Factor Authentication (MFA) als auch für die Self-Service-Kennwortzurücksetzung registrieren können.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52e50aa4097dbb405b0c37a96426440fdf002810
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763137"
---
# <a name="combined-security-information-registration-for-azure-active-directory-overview"></a>Übersicht über die kombinierte Registrierung von Sicherheitsinformationen in Azure Active Directory

Vor der kombinierten Registrierung registrierten Benutzer Authentifizierungsmethoden für Azure AD Multi-Factor Authentication (MFA) und die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) getrennt voneinander. Benutzer waren verwirrt, dass ähnliche Methoden für Multi-Factor Authentication und für SSPR verwendet wurden, sie sich jedoch für beide Funktionen registrieren mussten. Mit der kombinierten Registrierung können sich Benutzer jetzt einmalig registrieren und die Vorteile von mehrstufiger Authentifizierung und SSPR nutzen. Sehen Sie sich dieses Video zum [Aktivieren und Konfigurieren von SSPR in Azure AD](https://www.youtube.com/watch?v=rA8TvhNcCvQ) an.

> [!NOTE]
> Ab dem 15. August 2020 werden alle neuen Azure AD-Mandanten automatisch für die kombinierte Registrierung aktiviert. 

In diesem Artikel wird beschrieben, was die kombinierte Registrierung von Sicherheitsinformationen ist. Erste Informationen zur kombinierten Registrierung von Sicherheitsinformationen finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Aktivieren der kombinierten Registrierung von Sicherheitsinformationen](howto-registration-mfa-sspr-combined.md)

![„Mein Konto“ mit registrierten Sicherheitsinformationen für einen Benutzer](media/concept-registration-mfa-sspr-combined/combined-security-info-defaults-registered.png)

Lesen Sie vor dem Aktivieren der neuen Funktion diese an Administratoren gerichtete Dokumentation und die benutzerorientierte Dokumentation, um sich mit der Funktionalität und den Auswirkungen dieser Funktion vertraut zu machen. Ziehen Sie für Ihre Schulungen die [Benutzerdokumentation](https://support.microsoft.com/account-billing/set-up-your-security-info-from-a-sign-in-prompt-28180870-c256-4ebf-8bd7-5335571bf9a8) heran, um Ihre Benutzer auf die neue Oberfläche vorzubereiten und so einen erfolgreichen Rollout sicherzustellen.

Die kombinierte Registrierung von Azure AD-Sicherheitsinformationen ist für Azure US Government, nicht aber für Azure Deutschland oder Azure China 21Vianet verfügbar.

> [!IMPORTANT]
> Benutzern, die für die ursprüngliche Vorschau und für die erweiterte kombinierte Registrierungsumgebung aktiviert sind, wird das neue Verhalten angezeigt. Benutzern, die für beide Umgebungen aktiviert sind, wird nur die Oberfläche „Mein Konto“ angezeigt. *Mein Konto* entspricht dem Aussehen und Verhalten der kombinierten Registrierung und bietet Benutzern eine einheitliche Oberfläche. Benutzer können „Mein Konto“ anzeigen, indem sie zu [https://myaccount.microsoft.com](https://myaccount.microsoft.com) navigieren.
>
> Möglicherweise erhalten Sie eine Fehlermeldung (z. B. „Anmeldung nicht möglich“), wenn Sie versuchen, auf die Option „Sicherheitsinformation“ zuzugreifen. Vergewissern Sie sich in diesem Fall, dass Cookies von Drittanbietern im Webbrowser nicht durch ein Konfigurations- oder Gruppenrichtlinienobjekt blockiert werden.

Die Seiten von *Mein Konto* sind entsprechend den Spracheinstellungen auf dem Computer lokalisiert, der für den Zugriff auf die Seite verwendet wird. Microsoft speichert die aktuell verwendete Sprache im Browsercache, sodass nachfolgende Zugriffsversuche auf die Seiten weiterhin in der zuletzt verwendeten Sprache angezeigt werden. Wenn Sie den Cache löschen, werden die Seiten neu gerendert.

Wenn Sie eine bestimmte Sprache erzwingen möchten, können Sie am Ende der URL `?lng=<language>` hinzufügen. Dabei ist `<language>` der Code der Sprache, in der Sie rendern möchten.

![Einrichten von SSPR oder anderen Sicherheitsüberprüfungsmethoden](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Verfügbare Methoden bei der kombinierten Registrierung

Bei der kombinierten Registrierung werden die folgenden Authentifizierungsmethoden und -aktionen unterstützt:

| Methode | Register | Change | Löschen |
| --- | --- | --- | --- |
| Microsoft Authenticator | Ja (maximal 5) | Nein | Ja |
| Andere Authenticator-App | Ja (maximal 5) | Nein | Ja |
| Hardwaretoken | Nein | Nein | Ja |
| Phone | Ja | Ja | Ja |
| Alternatives Telefon | Ja | Ja | Ja |
| Bürotelefon | Ja | Ja | Ja |
| Email | Ja | Ja | Ja |
| Sicherheitsfragen | Ja | Nein | Ja |
| App-Kennwörter | Ja | Nein | Ja |
| FIDO2-Sicherheitsschlüssel<br />*Verwalteter Modus nur auf der Seite [Sicherheitsinformation](https://mysignins.microsoft.com/security-info)“*| Ja | Ja | Ja |

> [!NOTE]
> App-Kennwörter sind nur für Benutzer verfügbar, für die Multi-Factor Authentication erzwungen wurde. App-Kennwörter sind nicht verfügbar für Benutzer, für die Multi-Factor Authentication über eine Richtlinie für bedingten Zugriff aktiviert wurde.

Benutzer können eine der folgenden Optionen als Standardmethode für die mehrstufige Authentifizierung festlegen:

- Microsoft Authenticator (Pushbenachrichtigung)
- Authenticator-App oder Hardwaretoken – Code
- Telefonanruf
- Textnachricht

Authentifikator-Apps von Drittanbietern bieten keine Pushbenachrichtigungen. Da auch weiterhin Authentifizierungsmethoden zu Azure AD hinzugefügt werden, werden diese bei der kombinierten Registrierung verfügbar.

## <a name="combined-registration-modes"></a>Modi der kombinierten Registrierung

Zwei Modi der kombinierten Registrierung sind verfügbar: Interruptmodus und Verwaltungsmodus.

- Beim **Interruptmodus** handelt es sich um eine assistentenähnliche Oberfläche, die für Benutzer angezeigt wird, wenn sie ihre Sicherheitsinformationen bei der Anmeldung registrieren oder aktualisieren.
- Der **Verwaltungsmodus** ist Teil des Benutzerprofils und ermöglicht Benutzern die Verwaltung ihrer Sicherheitsinformationen.

In beiden Modi müssen Benutzer, die bereits eine Methode registriert haben, die für die mehrstufige Authentifizierung verwendet werden kann, eine mehrstufige Authentifizierung ausführen, bevor sie auf ihre Sicherheitsinformationen zugreifen können. Benutzer müssen ihre Informationen bestätigen, bevor sie ihre zuvor registrierten Methoden weiterhin verwenden können. 



### <a name="interrupt-mode"></a>Interruptmodus

Bei der kombinierten Registrierung werden sowohl Richtlinien für die mehrstufige Authentifizierung als auch für die Self-Service-Kennwortzurücksetzung eingehalten, wenn beide für Ihren Mandanten aktiviert sind. Über diese Richtlinien wird gesteuert, ob die Anmeldung eines Benutzers unterbrochen und dieser aufgefordert wird, die Registrierung durchzuführen, und welche Methoden für die Registrierung verfügbar sind. Wenn nur eine SSPR-Richtlinie aktiviert ist, können Benutzer die Registrierungsunterbrechung überspringen und zu einem späteren Zeitpunkt abschließen.

Im Folgenden sind mehrere Beispielszenarien aufgeführt, in denen Benutzer möglicherweise aufgefordert werden, ihre Sicherheitsinformationen zu registrieren oder zu aktualisieren:

- *Durch Identity Protection erzwungene MFA-Registrierung:* Benutzer werden bei der Anmeldung aufgefordert, die Registrierung durchzuführen. Sie registrieren MFA-Methoden (Multi-Factor Authentication) und SSPR-Methoden (sofern sie für SSPR aktiviert sind).
- *Durch benutzerspezifische mehrstufige Authentifizierung erzwungene MFA-Registrierung:* Benutzer werden bei der Anmeldung aufgefordert, die Registrierung durchzuführen. Sie registrieren MFA-Methoden (Multi-Factor Authentication) und SSPR-Methoden (sofern sie für SSPR aktiviert sind).
- *Durch Richtlinien für bedingten Zugriff oder andere Richtlinien erzwungene MFA-Registrierung:* Benutzer werden zur Registrierung aufgefordert, wenn sie eine Ressource verwenden, die Multi-Factor Authentication erfordert. Sie registrieren MFA-Methoden (Multi-Factor Authentication) und SSPR-Methoden (sofern sie für SSPR aktiviert sind).
- *Erzwungene SSPR-Registrierung:* Benutzer werden bei der Anmeldung aufgefordert, die Registrierung durchzuführen. Sie registrieren nur SSPR-Methoden.
- *Erzwungene SSPR-Aktualisierung:* Benutzer werden in einem vom Administrator festgelegten Intervall aufgefordert, ihre Sicherheitsinformationen zu überprüfen. Die Benutzer prüfen ihre Informationen und können diese bestätigen oder bei Bedarf ändern.

Wenn die Registrierung erzwungen wird, wird den Benutzern die Mindestanzahl der Methoden angezeigt (angefangen bei der sichersten bis zur am wenigsten sicheren Methode), die erforderlich sind, um den MFA- (Multi-Factor Authentication) sowie den SSPR-Richtlinien zu entsprechen.

Betrachten Sie das folgende Beispielszenario:

- Ein Benutzer ist für SSPR aktiviert. Die SSPR-Richtlinie erfordert zwei Methoden für das Zurücksetzen, und die Authenticator-App sowie die E-Mail-Adresse und das Telefon sind aktiviert.
- Wenn sich der Benutzer registrieren möchte, sind zwei Methoden erforderlich:
   - Standardmäßig werden dem Benutzer die Authenticator-App und das Telefon angezeigt.
   - Der Benutzer hat die Möglichkeit, statt der Authenticator-App oder des Telefons die E-Mail-Adresse zu registrieren.

Im folgenden Flussdiagramm sind die Methoden beschrieben, die einem Benutzer angezeigt werden, wenn seine Anmeldung unterbrochen und er aufgefordert wird, die Registrierung durchzuführen:

![Flussdiagramm für kombinierte Sicherheitsinformationen](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Wenn Sie Multi-Factor Authentication und SSPR aktiviert haben, sollten Sie die MFA-Registrierung erzwingen.

Wenn Benutzer über die SSPR-Richtlinie in regelmäßigen Abständen aufgefordert werden, ihre Sicherheitsinformationen zu überprüfen, wird die Anmeldung der Benutzer unterbrochen, und es werden alle registrierten Methoden angezeigt. Sie können die aktuellen Informationen bestätigen, wenn diese auf dem neuesten Stand sind, oder ggf. Änderungen vornehmen. Benutzer müssen eine mehrstufige Authentifizierung ausführen, wenn sie auf diese Seite zugreifen.

### <a name="manage-mode"></a>Verwaltungsmodus

Benutzer können auf den Verwaltungsmodus zugreifen, indem sie zu [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) navigieren oder unter „Mein Konto“ die Option **Sicherheitsinformation** auswählen. Dort können Benutzer Methoden hinzufügen, vorhandene Methoden löschen oder ändern, die verwendete Standardmethode ändern und vieles mehr.

## <a name="key-usage-scenarios"></a>Wichtige Verwendungsszenarien

### <a name="set-up-security-info-during-sign-in"></a>Einrichten der Sicherheitsinformationen bei der Anmeldung

Ein Administrator hat die Registrierung erzwungen.

Ein Benutzer hat nicht alle erforderlichen Sicherheitsinformationen eingerichtet und navigiert zum Azure-Portal. Nach der Eingabe des Benutzernamens und Kennworts wird der Benutzer aufgefordert, die Sicherheitsinformationen einzurichten. Der Benutzer führt dann die im Assistenten angezeigten Schritte zum Einrichten der erforderlichen Sicherheitsinformationen aus. Wenn es Ihre Einstellungen zulassen, kann der Benutzer auch andere Methoden als die standardmäßig angezeigten einrichten. Nach Abschluss des Assistenten überprüfen die Benutzer die eingerichteten Methoden sowie ihre Standardmethode für die mehrstufige Authentifizierung. Um den Einrichtungsvorgang abzuschließen, bestätigt der Benutzer die Informationen und navigiert dann zum Azure-Portal.

### <a name="set-up-security-info-from-my-account"></a>Einrichten der Sicherheitsinformationen unter „Mein Konto“

Ein Administrator hat die Registrierung nicht erzwungen.

Ein Benutzer, der noch nicht alle erforderlichen Sicherheitsinformationen eingerichtet hat, navigiert zu [https://myaccount.microsoft.com](https://myaccount.microsoft.com). Der Benutzer wählt im linken Bereich **Sicherheitsinformation** aus. Dort gibt der Benutzer das Hinzufügen einer Methode an, wählt eine der verfügbaren Methoden aus und führt die Schritte zum Einrichten dieser Methode aus. Nach Abschluss des Vorgangs wird dem Benutzer die eingerichtete Methode auf der Seite „Sicherheitsinformation“ angezeigt.

### <a name="delete-security-info-from-my-account"></a>Löschen der Sicherheitsinformationen unter „Mein Konto“

Ein Benutzer, der zuvor mindestens eine Methode eingerichtet hat, navigiert zu [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Der Benutzer löscht eine der zuvor registrierten Methoden. Anschließend wird diese Methode nicht mehr auf der Seite „Sicherheitsinformation“ angezeigt.

### <a name="change-the-default-method-from-my-account"></a>Ändern der Standardmethode unter „Mein Konto“

Ein Benutzer, der zuvor mindestens eine Methode eingerichtet hat, die für die mehrstufige Authentifizierung verwendet werden kann, navigiert zu [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Der Benutzer ändert die aktuell verwendete Standardmethode in eine andere Standardmethode. Anschließend wird die neue Standardmethode auf der Seite „Sicherheitsinformation“ angezeigt.

### <a name="switch-directory"></a>Wechseln des Verzeichnisses

Eine externe Identität (z. B. ein B2B-Benutzer) muss das Verzeichnis wechseln, um die Sicherheitsregistrierungsinformationen für einen Drittanbietermandanten zu ändern. Außerdem sind Benutzer, die auf einen Ressourcenmandanten zugreifen, möglicherweise irritiert, wenn sie Änderungen in ihrem Basismandanten vornehmen, diese Änderungen aber nicht im Ressourcenmandanten angezeigt werden. 

Beispielsweise legt ein Benutzer die Pushbenachrichtigung der Microsoft Authenticator-App als primäre Authentifizierungsmethode für die Anmeldung beim Basismandanten fest, hat aber auch die SMS/Text-Option eingerichtet. Dieser Benutzer hat auch die SMS/Text-Option für einen Ressourcenmandanten konfiguriert. Wenn er die SMS/Text-Option als eine der Authentifizierungsoptionen für den Basismandanten entfernt, ist er vermutlich irritiert, wenn er sich beim Zugriff auf den Ressourcenmandanten mit der SMS/Text-Option authentifizieren soll. 

Klicken Sie auf den Benutzerkontonamen in der Ecke rechts oben und dann auf **Verzeichnis wechseln**, um das Verzeichnis im Azure-Portal zu wechseln.

![Externe Benutzer können das Verzeichnis wechseln.](media/concept-registration-mfa-sspr-combined/switch-directory.png)

## <a name="next-steps"></a>Nächste Schritte

Erste Informationen finden Sie unter [Aktivieren der Self-Service-Kennwortzurücksetzung](tutorial-enable-sspr.md) und [Aktivieren der Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

Erfahren Sie, wie Sie die [kombinierte Registrierung in Ihrem Mandanten aktivieren](howto-registration-mfa-sspr-combined.md) oder [die erneute Registrierung von Authentifizierungsmethoden durch Benutzer erzwingen](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Sie können auch den Artikel [Verfügbare Methoden für Azure AD Multi-Factor Authentication und SSPR](concept-authentication-methods.md) lesen.