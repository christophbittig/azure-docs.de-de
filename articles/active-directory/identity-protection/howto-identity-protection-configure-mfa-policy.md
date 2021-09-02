---
title: Konfigurieren der MFA-Registrierungsrichtlinie – Azure Active Directory Identity Protection
description: Es wird beschrieben, wie Sie die Registrierungsrichtlinie für die mehrstufige Authentifizierung in Azure AD Identity Protection konfigurieren.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d2bfaef8f00f6246dfef17145666c817ed26d9d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355861"
---
# <a name="how-to-configure-the-azure-ad-multi-factor-authentication-registration-policy"></a>Anleitung: Konfigurieren der Registrierungsrichtlinie für Azure AD Multi-Factor Authentication

Azure AD Identity Protection unterstützt Sie beim Verwalten des Rollouts der Registrierung für Azure AD Multi-Factor Authentication (MFA): Es wird eine Richtlinie für bedingten Zugriff zur Anforderung der MFA-Registrierung konfiguriert – unabhängig davon, bei welcher App mit moderner Authentifizierung Sie sich anmelden möchten.

## <a name="what-is-the-azure-ad-multi-factor-authentication-registration-policy"></a>Was ist die Registrierungsrichtlinie für die mehrstufige Authentifizierung in Azure (Azure AD Multi-Factor Authentication)?

Mit Azure AD Multi-Factor Authentication können Sie über die Verwendung eines Benutzernamens und Kennworts hinaus Ihre Identität verifizieren. Sie stellt eine zweite Sicherheitsebene für Benutzeranmeldungen dar. Damit Benutzer auf MFA-Aufforderungen reagieren können, müssen sie sich zuerst bei Azure AD Multi-Factor Authentication registrieren.

Die obligatorische Verwendung von Azure AD Multi-Factor Authentication für Benutzeranmeldungen empfiehlt sich aus folgenden Gründen:

- Sie bietet eine sichere Authentifizierung mit einer Auswahl von Überprüfungsoptionen.
- Sie spielt eine wichtige Rolle bei der Vorbereitung Ihrer Organisation auf die Eigenwartung bei Risikoerkennungen in Identity Protection.

Weitere Informationen zu Azure AD Multi-Factor Authentication finden Sie unter [Was ist Azure AD Multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md).

## <a name="policy-configuration"></a>Richtlinienkonfiguration

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Identity Protection** > **MFA-Registrierungsrichtlinie**.
   1. Unter **Zuweisungen**
      1. **Benutzer**: Wählen Sie **Alle Benutzer** oder **Einzelne Benutzer und Gruppen auswählen** aus, wenn Sie den Rollout einschränken.
         1. Optional können Sie Benutzer aus der Richtlinie ausschließen.
   1. **Richtlinie erzwingen** - **Ein**
   1. **Speichern**

## <a name="user-experience"></a>Benutzererfahrung

Azure Active Directory Identity Protection fordert Ihre Benutzer auf, sich bei der nächsten interaktiven Anmeldung zu registrieren, und gibt ihnen 14 Tage Zeit, um die Registrierung vorzunehmen. Während dieses Zeitraums von 14 Tagen kann die Registrierung umgangen werden, Wenn MFA nicht als Bedingung vorausgesetzt wird, aber am Ende des Zeitraums müssen die Benutzer sich registrieren, um den Anmeldevorgang abschließen zu können.

Eine Übersicht über die zugehörige Benutzeroberfläche finden Sie unter:

- [Anmeldeverfahren von Azure AD Identity Protection](concept-identity-protection-user-experience.md)  

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren von Anmelde- und Benutzerrisikorichtlinien](howto-identity-protection-configure-risk-policies.md)

- [Aktivieren der Self-Service-Kennwortzurücksetzung in Azure AD](../authentication/howto-sspr-deployment.md)

- [Aktivieren von Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
