---
title: Von Azure AD B2C unterstützte Anmeldeoptionen
titleSuffix: Azure AD B2C
description: Erfahren Sie mehr über die Optionen für die Registrierung und Anmeldung, die Sie mit Azure Active Directory B2C verwenden können, darunter Benutzername und Kennwort, E-Mail-Adresse, Telefon oder Verbund mit sozialen Netzwerken als Identitätsanbieter oder externen Identitätsanbietern.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: efb526f9a7418c0d7d2316ec91f355a7c4431404
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112204745"
---
# <a name="sign-in-options-in-azure-ad-b2c"></a>Anmeldeoptionen in Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) bietet mehrere Registrierungs- und Anmeldemethoden für Benutzer Ihrer Anwendungen. Wenn sich Benutzer für Ihre Anwendung registrieren, bestimmen Sie, ob sie einen Benutzernamen, eine E-Mail-Adresse oder eine Telefonnummer verwenden, um lokale Konten in Ihrem Azure AD B2C-Mandanten zu erstellen. Sie können auch einen Verbund mit sozialen Netzwerken (z. B. Facebook, LinkedIn und Twitter) als Identitätsanbieter und Standardidentitätsprotokollen (z. B. OAuth 2.0, OpenID Connect usw.) einrichten.

Dieser Artikel bietet eine Übersicht über die Anmeldeoptionen von Azure AD B2C.

## <a name="email-sign-in"></a>Anmeldung per E-Mail-Adresse

In den Einstellungen des Identitätsanbieters „Lokales Konto“ ist standardmäßig die Registrierung mit E-Mail-Adresse aktiviert. Bei Verwendung dieser Option können sich Benutzer mit ihrer E-Mail-Adresse und ihrem Kennwort anmelden/registrieren.

- **Anmelden**: Benutzer werden aufgefordert, ihre E-Mail-Adresse und ihr Kennwort anzugeben.
- **Registrieren**: Benutzer werden zur Eingabe einer E-Mail-Adresse aufgefordert, die bei der Registrierung überprüft (optional) und als Anmelde-ID verwendet wird. Der Benutzer gibt dann auf der Registrierungsseite alle weiteren erforderlichen Informationen ein, z. B. Anzeigename, Vorname und Nachname. Anschließend wählt er **Weiter** aus, um das Konto zu erstellen.
- **Kennwortzurücksetzung**: Nach der Eingabe und Bestätigung ihrer E-Mail-Adresse können Benutzer das Kennwort zurücksetzen.

![Registrierung oder Anmeldung per E-Mail-Adresse](./media/sign-in-options/local-account-email-experience.png)

Erfahren Sie, wie Sie die Anmeldung per E-Mail-Adresse in Ihrem Identitätsanbieter „Lokales Konto“ konfigurieren.
## <a name="username-sign-in"></a>Anmeldung mit Benutzername

Der Identitätsanbieter „Lokales Konto“ enthält eine Option „Benutzername“, mit der sich Benutzer mit einem Benutzernamen und Kennwort bei Ihrer Anwendung registrieren und anmelden können.

- **Anmelden**: Benutzer werden aufgefordert, ihren Benutzernamen und ihr Kennwort anzugeben.
- **Registrieren**: Benutzer werden aufgefordert, einen Benutzernamen einzugeben, der zu ihrer Anmelde-ID wird. Benutzer werden außerdem aufgefordert, eine E-Mail-Adresse anzugeben, die bei der Registrierung überprüft wird. Die E-Mail-Adresse wird dann in einem Kennwortzurücksetzungsflow verwendet. Der Benutzer gibt auf der Registrierungsseite alle weiteren erforderlichen Informationen ein, z. B. „Anzeigename“, „Vorname“ und „Nachname“. Der Benutzer wählt dann zum Erstellen des Kontos „Fortfahren“ aus.
- **Kennwortzurücksetzung**: Benutzer müssen ihren Benutzernamen und die zugehörige E-Mail-Adresse eingeben. Die E-Mail-Adresse muss überprüft werden. Danach kann der Benutzer das Kennwort zurücksetzen.

![Registrierung oder Anmeldung mit Benutzername](./media/sign-in-options/local-account-username-experience.png)

## <a name="phone-sign-in"></a>Anmeldung per Telefon

Die Anmeldung per Telefon ist eine kennwortlose Option in den Einstellungen des Identitätsanbieters „Lokales Konto“. Bei dieser Methode können sich Benutzer für Ihre App registrieren, indem sie eine Telefonnummer als primären Bezeichner verwenden. Einmalkennwörter werden per SMS an Ihre Benutzer gesendet. Die Benutzererfahrung bei der Registrierung und Anmeldung ist wie folgt:

- **Anmelden**: Wenn der Benutzer über ein bestehendes Konto mit der Telefonnummer als Bezeichner verfügt, gibt er seine Telefonnummer ein und wählt *Anmelden* aus. Der Benutzer bestätigt das Land/die Region und die Telefonnummer, indem er *Weiter* auswählt. Daraufhin wird ein Einmalprüfcode an die Telefonnummer des Benutzers gesendet. Der Benutzer gibt den Prüfcode ein und wählt *Weiter* aus, um sich anzumelden.
- **Registrieren**: Wenn der Benutzer noch nicht über ein Konto für Ihre Anwendung verfügt, kann er durch Klicken auf den Link *Jetzt registrieren* ein Konto erstellen.
    1. Daraufhin wird eine Registrierungsseite angezeigt. Auf dieser Seite muss der Benutzer *Land/Region* auswählen, seine Telefonnummer eingeben und *Code senden* auswählen. 
    1. Ein Einmalprüfcode wird an die Telefonnummer des Benutzers gesendet. Der Benutzer gibt auf der Registrierungsseite den *Prüfcode* ein und wählt dann *Code überprüfen* aus. (Wenn der Benutzer den Code nicht abrufen konnte, kann er die Option *Neuen Code senden* auswählen.)
    1. Der Benutzer gibt auf der Registrierungsseite alle weiteren erforderlichen Informationen ein, z. B. „Anzeigename“, „Vorname“ und „Nachname“. Klicken Sie anschließend auf Weiter.
    1. Als Nächstes wird der Benutzer aufgefordert, eine **E-Mail-Adresse für die Wiederherstellung** anzugeben. Der Benutzer gibt seine E-Mail-Adresse ein und wählt dann *Prüfcode senden* ein. An den E-Mail-Posteingang des Benutzers wird ein Code gesendet, den der Benutzer abrufen und in das Feld Prüfcode eingeben kann. Anschließend wählt der Benutzer die Option Code überprüfen aus.
    1. Nachdem der Code überprüft wurde, wählt der Benutzer die Option *Erstellen* aus, um sein Konto zu erstellen.

![Registrierung oder Anmeldung per Telefon](./media/sign-in-options/local-account-phone-experience.png)

### <a name="pricing-for-phone-sign-in"></a>Preise für die Anmeldung per Telefon

Einmalkennwörter werden mithilfe von SMS-Textnachrichten an Ihre Benutzer gesendet. Abhängig von Ihrem Mobilfunkanbieter fallen möglicherweise für jede gesendete Nachricht Gebühren an. Preisinformationen finden Sie im Abschnitt **Separate Gebühren** unter [Azure Active Directory B2C – Preise](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

> [!NOTE]
> Die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) ist standardmäßig deaktiviert, wenn Sie einen Benutzerflow mit telefonischer Registrierung konfigurieren. In Benutzerflows mit telefonischer Registrierung ist es zwar möglich, MFA zu aktivieren. Als zweiter Authentifizierungsfaktor steht jedoch nur die Einmalkennung per E-Mail zur Verfügung, da die Telefonnummer als primärer Bezeichner verwendet wird.

### <a name="phone-recovery"></a>Telefonwiederherstellung

Wenn Sie für Benutzerflows die telefonische Registrierung und Anmeldung aktivieren, empfiehlt es sich, auch das Feature zum Senden von Wiederherstellungs-E-Mails zu aktivieren. Mit dieser Funktion kann ein Benutzer eine E-Mail-Adresse angeben, die zum Wiederherstellen seines Kontos verwendet werden kann, wenn sein Telefon nicht verfügbar ist. Diese E-Mail-Adresse wird ausschließlich zur Kontowiederherstellung verwendet. Sie kann nicht zum Anmelden verwendet werden.

- Wenn die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse auf **Ein** eingestellt ist, wird ein Benutzer bei der erstmaligen Registrierung aufgefordert, eine E-Mail-Adresse als Sicherung zu verifizieren. Ein Benutzer, der noch keine Wiederherstellungs-E-Mail-Adresse angegeben hat, wird bei der nächsten Anmeldung aufgefordert, eine E-Mail-Adresse als Sicherung zu verifizieren.

- Wenn die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse auf **Aus** eingestellt ist, wird dem Benutzer, der sich registriert oder anmeldet, keine Aufforderung zur Eingabe einer E-Mail-Adresse angezeigt.

Die folgenden Screenshots veranschaulichen den Ablauf der Telefonwiederherstellung:

![Benutzerflow für Telefonwiederherstellung](./media/sign-in-options/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in"></a>Anmeldung per Telefon oder E-Mail

Sie können die [Anmeldung per Telefon](#phone-sign-in) und die [Anmeldung per E-Mail-Adresse](#email-sign-in) in den Einstellungen des Identitätsanbieters „Lokales Konto“ kombinieren. Auf der Registrierungs- oder Anmeldeseite kann der Benutzer eine Telefonnummer oder E-Mail-Adresse eingeben. Basierend auf der Benutzereingabe leitet Azure AD B2C den Benutzer zum entsprechenden Flow.

![Registrierung oder Anmeldung per Telefon oder E-Mail-Adresse](./media/sign-in-options/local-account-phone-and-email-experience.png)

## <a name="next-steps"></a>Nächste Schritte

- Beschäftigen Sie sich ausführlicher mit den integrierten Richtlinien, die von [Benutzerflows in Azure Active Directory B2C](user-flow-overview.md) bereitgestellt werden.
- [Konfigurieren des Identitätsanbieters „Lokales Konto“](identity-provider-local.md)