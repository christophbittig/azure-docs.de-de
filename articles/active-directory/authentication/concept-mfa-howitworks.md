---
title: Azure AD Multi-Factor Authentication – Übersicht
description: Hier erfahren Sie, wie Azure AD Multi-Factor Authentication zum Schutz des Zugriffs auf Daten und Anwendungen beiträgt und gleichzeitig ein einfaches Anmeldeverfahren für Benutzer bietet.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 133ec8c46bb227f2f43cb9243ca5db4a4e9ac32b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355414"
---
# <a name="how-it-works-azure-ad-multi-factor-authentication"></a>So funktioniert's: Azure AD Multi-Factor Authentication

Multi-Factor Authentication (mehrstufige Authentifizierung) ist ein Prozess, bei dem Benutzer während des Anmeldevorgangs zur Durchführung eines weiteren Identifizierungsverfahrens aufgefordert werden, z. B. per Eingabe eines Codes auf dem Smartphone oder per Fingerabdruckscan.

Wenn Sie zum Authentifizieren von Benutzern nur ein Kennwort nutzen, kann dies einen Angriffsvektor darstellen und mit Unsicherheit verbunden sein. Falls das Kennwort nicht sicher ist oder offengelegt wurde, können Sie nicht sicher sein, ob es wirklich der Benutzer ist, der sich mit dem Benutzernamen und dem Kennwort anmeldet, oder ein Angreifer. Wenn Sie ein zweites Authentifizierungsverfahren erzwingen, wird die Sicherheit erhöht, weil dieses zusätzliche Verfahren von einem Angreifer nicht ohne Weiteres nachvollzogen bzw. dupliziert werden kann.

![Abbildung zum Konzept der unterschiedlichen Arten von mehrstufiger Authentifizierung](./media/concept-mfa-howitworks/methods.png)

Für Azure AD Multi-Factor Authentication sind mindestens zwei der folgenden Authentifizierungsverfahren obligatorisch:

* Eine dem Benutzer bekannte Information (meist ein Kennwort).
* Ein im Besitz des Benutzers befindliches Objekt, z. B. ein vertrauenswürdiges Gerät, das nicht ohne Weiteres dupliziert werden kann (Telefon oder Hardwareschlüssel).
* Ein biometrisches Merkmal des Benutzers (Fingerabdruck- oder Gesichtsscan).

Azure AD Multi-Factor Authentication kann außerdem die Kennwortzurücksetzung noch sicherer machen. Wenn sich Benutzer für Azure AD Multi-Factor Authentication registrieren, können sie sich auch gleichzeitig für die Self-Service-Kennwortzurücksetzung registrieren. Administratoren können Formen für die sekundäre Authentifizierung auswählen und Herausforderungen für MFA basierend auf Konfigurationsentscheidungen konfigurieren. 

Apps oder Dienste müssen nicht geändert werden, um Azure AD Multi-Factor Authentication verwenden zu können. Die Überprüfungsaufforderungen sind Teil der Azure AD-Anmeldung, bei der die MFA-Abfrage ggf. automatisch angefordert und verarbeitet wird.

![Verwendete Authentifizierungsmethoden auf dem Anmeldebildschirm](media/concept-authentication-methods/overview-login.png)

## <a name="available-verification-methods"></a>Verfügbare Überprüfungsmethoden

Wenn sich ein Benutzer bei einer Anwendung oder einem Dienst anmeldet und eine MFA-Aufforderung erhält, kann er eine der registrierten Formen der zusätzlichen Überprüfung wählen. Benutzer können [Mein Profil](https://myprofile.microsoft.com) auswählen, um Überprüfungsmethoden zu bearbeiten oder hinzuzufügen.

Die folgenden zusätzlichen Formen der Überprüfung können bei Azure AD Multi-Factor Authentication verwendet werden:

* Microsoft Authenticator-App
* OATH-Hardwaretoken (Vorschau)
* OATH-Softwaretoken
* sms
* Anruf

## <a name="how-to-enable-and-use-azure-ad-multi-factor-authentication"></a>Aktivieren und Verwenden von Azure AD Multi-Factor Authentication

Alle Azure AD-Mandanten können [Sicherheitsstandards](../fundamentals/concept-fundamentals-security-defaults.md) verwenden, um Microsoft Authenticator schnell für alle Benutzer zu aktivieren. Für Benutzer und Gruppen kann Azure AD Multi-Factor Authentication aktiviert werden, damit sie während der Anmeldung zur weiteren Überprüfung aufgefordert werden. 

Für die genauere Steuerung können [Richtlinien für den bedingten Zugriff](../conditional-access/overview.md) verwendet werden, um Ereignisse oder Anwendungen zu definieren, die MFA erfordern. Diese Richtlinien können reguläre Anmeldungsereignisse zulassen, wenn sich der Benutzer im Unternehmensnetzwerk befindet oder ein registriertes Gerät verwendet, jedoch zusätzliche Überprüfungsfaktoren anfordern, wenn der Benutzer remote arbeitet oder ein persönliches Gerät verwendet.

![Übersichtsdiagramm: Funktionsweise des bedingten Zugriffs zum Schutz des Anmeldevorgangs](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Lizenzierung finden Sie unter [Features und Lizenzen für Azure AD Multi-Factor Authentication](concept-mfa-licensing.md).

Weitere Informationen zu verschiedenen Authentifizierungs- und Validierungsmethoden finden Sie unter [Authentifizierungsmethoden in Azure Active Directory](concept-authentication-methods.md).

Wenn Sie MFA in Aktion erleben möchten, aktivieren Sie im folgenden Tutorial Azure AD Multi-Factor Authentication für eine Reihe von Testbenutzern:

> [!div class="nextstepaction"]
> [Aktivieren von Azure AD Multi-Factor Authentication](./tutorial-enable-azure-mfa.md)