---
title: Suchen und Beheben von Lücken in der Abdeckung der starken Authentifizierung für Administratoren in Azure Active Directory
description: Erfahren Sie, wie Sie nach Lücken in der Abdeckung der starken Authentifizierung für Administratoren in Azure Active Directory suchen und diese beheben
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/03/2021
ms.author: justinha
author: inbarckMS
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81d3ce6564c0ed4233f34bc43d661b2d9e1c1388
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505401"
---
# <a name="find-and-address-gaps-in-strong-authentication-coverage-for-your-administrators"></a>Suchen und Beheben von Lücken in der Abdeckung der starken Authentifizierung für Administratoren

Die Anforderung der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) für die Administratoren in Ihrem Mandanten ist einer der ersten Schritte, die Sie ausführen können, um die Sicherheit Ihres Mandanten zu erhöhen. In diesem Artikel erfahren Sie, wie Sie sicherstellen, dass alle Ihre Administratoren von der mehrstufigen Authentifizierung abgedeckt werden.

## <a name="detect-current-usage-for-azure-ad-built-in-administrator-roles"></a>Ermitteln der aktuellen Nutzung für in Azure AD integrierte Administratorrollen

[Azure AD Secure Score](../fundamentals/identity-secure-score.md) stellt eine Bewertung für die Funktion **Anfordern der MFA für Administratorrollen** in Ihrem Mandanten bereit. Mit dieser Optimierungsaktion wird die MFA-Nutzung für die Rollen „Globaler Administrator“, „Sicherheitsadministrator“, „Exchange-Administrator“ und „SharePoint-Administrator“ verfolgt. 

Es gibt verschiedene Möglichkeiten, zu überprüfen, ob Ihre Administratoren durch eine MFA-Richtlinie abgedeckt sind. 

- Zum Beheben von Problemen bei der Anmeldung für einen bestimmten Administrator können Sie die Anmeldeprotokolle verwenden. Mit den Anmeldeprotokollen können Sie die **Authentifizierungsanforderung** für bestimmte Benutzer filtern. Jede Anmeldung, bei der die **Authentifizierungsanforderung** dem Wert **Einstufige Authentifizierung** entspricht, bedeutet, dass für die Anmeldung keine MFA-Richtlinie erforderlich war.

  ![Screenshot: Anmeldeprotokoll.](./media/how-to-authentication-find-coverage-gaps/auth-requirement.png)

  Klicken Sie auf **Authentifizierungsdetails**, um [Details zu den MFA-Anforderungen ](../reports-monitoring/concept-sign-ins.md#authentication-details) anzuzeigen.
  
  ![Screenshot: Details der Authentifizierungsaktivitäten.](./media/how-to-authentication-find-coverage-gaps/details.png)

- Um auszuwählen, welche Richtlinie auf Basis Ihrer Benutzerlizenzen aktiviert werden soll, haben wir einen neuen MFA-Aktivierungs-Assistenten entwickelt, mit dem Sie [MFA-Richtlinien vergleichen](concept-mfa-licensing.md#compare-multi-factor-authentication-policies) und sehen können, welche Schritte für Ihre Organisation geeignet sind. Der Assistent zeigt die Administratoren an, die in den letzten 30 Tagen durch MFA geschützt waren.

  ![Screenshot: MFA-Aktivierungs-Assistent.](./media/how-to-authentication-find-coverage-gaps/wizard.png)

- Sie können ein [PowerShell-Skript](https://github.com/microsoft/AzureADToolkit/blob/main/src/Find-UnprotectedUsersWithAdminRoles.ps1) ausführen, um programmgesteuert einen Bericht zu erstellen, in dem alle Benutzer mit Administratorrollen in Ihrem Mandanten und deren Status für die starke Authentifizierung aufgelistet sind. Dieses Skript listet alle permanenten und berechtigten, integrierten und benutzerdefinierten Rollenzuweisungen sowie Gruppen mit zugewiesenen Rollen auf und ermittelt Benutzer, die entweder nicht für MFA registriert sind oder sich nicht mit MFA anmelden, indem ihre Authentifizierungsmethoden und Anmeldeaktivitäten ausgewertet werden.

## <a name="enforce-multi-factor-authentication-on-your-administrators"></a>Erzwingen der mehrstufigen Authentifizierung für Ihre Administratoren

Anhand der gefundenen Lücken muss die Verwendung der mehrstufigen Authentifizierung durch die Administratoren auf eine der folgenden Arten erzwungen werden:

- Wenn Ihre Administratoren für Azure AD Premium lizenziert sind, können Sie den Befehl [Richtlinie für bedingten Zugriff erstellen](tutorial-enable-azure-mfa.md) ausführen und damit die MFA für Administratoren erzwingen. Sie können diese Richtlinie auch aktualisieren, um MFA von Benutzern mit benutzerdefinierten Rollen anzufordern.  

- Führen Sie den [MFA-Aktivierungs-Assistenten](https://aka.ms/MFASetupGuide) aus, um Ihre MFA-Richtlinie auszuwählen.

- Wenn Sie in [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) benutzerdefinierte oder integrierte Administratorrollen zuweisen, fordern Sie bei der Rollenaktivierung die mehrstufige Authentifizierung an.

## <a name="use-passwordless-and-phishing-resistant-authentication-methods-for-your-administrators"></a>Verwenden von kennwortlosen und phishingsicheren Authentifizierungsmethoden für Administratoren

Nachdem für Ihre Administratoren die mehrstufige Authentifizierung erzwungen wurde und diese schon seit einiger Zeit verwendet wird, ist es an der Zeit, die Latte für die sichere Authentifizierung höher zu legen und eine kennwortlose und phishingsichere Authentifizierungsmethode zu verwenden: 

- [Telefonische Anmeldung (mit Microsoft Authenticator)](concept-authentication-authenticator-app.md)
- [FIDO2](concept-authentication-passwordless.md#fido2-security-keys)
- [Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-overview)

Weitere Informationen zu diesen Authentifizierungsmethoden und den entsprechenden Sicherheitsüberlegungen finden Sie unter [Azure AD-Authentifizierungsmethoden](concept-authentication-methods.md).


