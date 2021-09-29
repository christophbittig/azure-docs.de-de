---
title: Frontline-Worker-Verwaltung - Azure Active Directory
description: Erfahren Sie mehr über die Funktionen zur Verwaltung von Frontline-Mitarbeitern, die über das Meine Mitarbeiter-Portal bereitgestellt werden.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: cchiedo
author: Chrispine-Chiedo
manager: CelesteDG
ms.reviewer: stevebal
ms.openlocfilehash: 26d7e967ceb0208d6e852db9946ac0c260751dd3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124732568"
---
# <a name="frontline-worker-management"></a>Verwaltung von Frontline-Worker

Frontline-Mitarbeitern machen mehr als 80 Prozent der globalen Mitarbeiter. Aufgrund hochskalierbarer, schneller Umsätze und fragmentierter Prozesse fehlen an vorderster Front-Worker oftmals nicht die Tools, um Ihre anspruchsvollen Aufträge etwas einfacher zu machen. Die an Verwalter der Fronline-Worker bringt eine digitale Transformation in die gesamte Workforce der Frontline. Die Mitarbeiter können Führungskräfte, Frontline-Worker, Vorgänge und IT einschließen.

Die Verwaltung von Frontline-Worker ermöglicht den Frontline-Workern, die folgenden Aktivitäten zu vereinfachen:
- Optimieren allgemeiner IT-Aufgaben mit „Meine Mitarbeiter“
- Einfaches Onboarding von Frontline-Workern durch vereinfachte Authentifizierung
- Nahtlose Bereitstellung von freigegebenen Geräten und sicheres Abmelden von Frontline-Workern

## <a name="delegated-user-management-through-my-staff"></a>Delegierte Benutzerverwaltung durch Meine Mitarbeiter

Azure Active Directory (Azure AD) bietet die Möglichkeit, die Benutzerverwaltung über das [Portal Meine Mitarbeiter](../roles/my-staff-configure.md) an Frontline-Verwalter zu delegieren, um wertvolle Zeit zu sparen und Risiken zu verringern. Durch die Möglichkeit der vereinfachten Passwortrücksetzung und Telefonverwaltung direkt im Geschäft oder in der Fabrikhalle können Manager Mitarbeitern Zugang gewähren, ohne die Anfrage über den Helpdesk, die IT oder den Betrieb zu leiten.

![Delegierte Benutzerverwaltung im Portal Meine Mitarbeiter](media/concept-fundamentals-frontline-worker/delegated-user-management.png)

## <a name="accelerated-onboarding-with-simplified-authentication"></a>Beschleunigtes Onboarding mit vereinfachter Authentifizierung

Meine Mitarbeiter ermöglicht es Frontline-Managern außerdem, die Telefonnummern der Teammitglieder für die [SMS-Anmeldung](../authentication/howto-authentication-sms-signin.md)zu registrieren. In vielen Branchen pflegen die Mitarbeiter an der Front eine lokale Kombination aus Benutzername und Passwort, eine Lösung, die oft umständlich, teuer und fehleranfällig ist. Wenn die Authentifizierung mithilfe der SMS-Anmeldung aktiviert wird, können sich Frontline-Worker mit [Single Sign-on (SSO)](../manage-apps/what-is-single-sign-on.md) für Microsoft Teams und andere Apps anmelden, indem Sie nur Ihre Telefonnummer und eine einmal Kennung (Single-Time Kennung, OTP) per SMS senden. Dies macht die Anmeldung für Frontline-Mitarbeiter einfach und sicher und bietet schnellen Zugriff auf die Apps, die sie am meisten benötigen.

![SMS-Anmeldung](media/concept-fundamentals-frontline-worker/sms-signin.png)

Frontline-Manager können außerdem die verwaltete Startbildschirm Anwendung (MHS) verwenden, um Worker den Zugriff auf einen bestimmten Satz von Anwendungen auf Ihren mit InTune registrierten Android Dedicated-Geräten zu ermöglichen. Die dedizierten Geräte werden bei [Azure AD Modus für gemeinsame Geräte](../develop/msal-shared-devices.md)registriert. Wenn die Konfiguration im Kioskmodus für mehrere Apps in der Microsoft Endpoint Manager-Konsole (G4) erfolgt, wird MHS automatisch als Standard-Startbildschirm auf dem Gerät gestartet und dem Endbenutzer als *einziger* Startbildschirm angezeigt. Weitere Informationen finden Sie unter [Konfigurieren der Managed Home Screen-App von Microsoft für Android Enterprise](/mem/intune/apps/app-configuration-managed-home-screen-app).

## <a name="secure-sign-out-of-frontline-workers-from-shared-devices"></a>Sicheres Abmelden von an vorderster Frontline-Workern von gemeinsam genutzten Geräten

Viele Unternehmen verwenden gemeinsam genutzte Geräte, damit die Frontline-Worker die Bestandsverwaltung und Point-of-Sale-Transaktionen durchführen können, ohne den IT-Aufwand für die Bereitstellung und Verfolgung einzelner Geräte. Mit der Abmeldung von gemeinsam genutzten Geräten ist es für einen Frontline-Worker einfach, sich sicher von allen Apps auf einem gemeinsam genutzten Gerät abzumelden, bevor er es an einen Hub zurückgibt oder an einen Teamkollegen in der nächsten Schicht weitergibt. Microsoft Teams ist eine der Apps, die derzeit auf gemeinsam genutzten Geräten unterstützt wird, und sie ermöglicht es Frontline-Mitarbeitern, die ihnen zugewiesenen Aufgaben anzuzeigen. Sobald sich ein Mitarbeiter von einem gemeinsam genutzten Gerät abmeldet, löschen Intune und Azure AD alle Unternehmensdaten, sodass das Gerät sicher an den nächsten Mitarbeiter weitergegeben werden kann. Sie können diese Funktion mithilfe der [Microsoft-Authentifizierungs Bibliothek](../develop/msal-overview.md)in all Ihre branchenspezifischen [IOS](../develop/msal-ios-shared-devices.md) -und [Android](../develop/msal-android-shared-devices.md) -apps integrieren.

![Abmelden von gemeinsam genutzten Geräten](media/concept-fundamentals-frontline-worker/shared-device-signout.png)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur delegierten Benutzerverwaltung finden Sie in der Benutzerdokumentation von [Meine Mitarbeiter](https://support.microsoft.com/account-billing/manage-front-line-users-with-my-staff-c65b9673-7e1c-4ad6-812b-1a31ce4460bd).
- Informationen zur eingehenden Benutzerbereitstellung von SAP-erfolgreichen Faktoren finden Sie im Tutorial zum [Konfigurieren von SAP-Erfolgsfaktoren für die Active Directory Benutzer Bereitstellung](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
- Informationen zur eingehenden Benutzerbereitstellung über Workday finden Sie im Tutorial zum [Konfigurieren von Workday für die automatische Benutzerbereitstellung](../saas-apps/workday-inbound-tutorial.md).