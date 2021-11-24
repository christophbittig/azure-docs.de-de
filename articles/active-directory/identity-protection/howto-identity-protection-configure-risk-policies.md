---
title: Risikorichtlinien – Azure Active Directory Identity Protection
description: Aktivieren und Konfigurieren von Risikorichtlinien in Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e191603d9508ab921c28e60ae13096e7283dc332
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401960"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Anleitung: Konfigurieren und Aktivieren von Risikorichtlinien

Wie im vorherigen Artikel [Identity Protection-Richtlinien](concept-identity-protection-policies.md) beschrieben, verfügen Sie über zwei Risikorichtlinien, die Sie in Ihrem Verzeichnis aktivieren können. 

- Richtlinie zum Anmelderisiko
- Richtlinie zum Benutzerrisiko

![Sicherheitsübersichtsseite zum Aktivieren der Richtlinien zum Benutzerrisiko und zum Anmelderisiko](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Beide Richtlinien dienen dazu, die Reaktion auf Risikoerkennungen in Ihrer Umgebung zu automatisieren und Benutzern die Eigenwartung erkannter Risiken zu ermöglichen. 

## <a name="choosing-acceptable-risk-levels"></a>Auswählen akzeptabler Risikostufen

Organisationen müssen entscheiden, welche Stufe von Risiken sie zu akzeptieren bereit sind, und dabei zwischen Benutzererfahrung und Sicherheitsstatus abzuwägen. 

Die Empfehlung von Microsoft lautet, den Schwellenwert für die Benutzerrisikorichtlinie auf **Hoch** und für die Anmelderisikorichtlinie auf **Mittel und höher** festzulegen sowie Selbstwartungsoptionen zuzulassen. Die Entscheidung, den Zugriff zu blockieren, anstatt Selbstwartungsoptionen wie Kennwortänderung und mehrstufige Authentifizierung zuzulassen, wirkt sich auf Ihre Benutzer und Administratoren aus. Wägen Sie diese Auswahl bei der Konfiguration Ihrer Richtlinien ab.

Wenn Sie den Schwellenwert **Hoch** auswählen, wird die Richtlinie weniger häufig ausgelöst, und die Auswirkungen für Benutzer verringern sich. Hierbei sind aber Risikoerkennungen der Stufen **Niedrig** und **Mittel** aus der Richtlinie ausgeschlossen. Für einen Angreifer wird das Ausnutzen einer gefährdeten Identität so ggf. nicht blockiert. Die Auswahl eines **niedrigen** Schwellenwerts führt zu mehr Unterbrechungen für die Benutzer.

Konfigurierte vertrauenswürdige [Netzwerkadressen](../conditional-access/location-condition.md) werden von Identity Protection bei einigen Risikoerkennungen verwendet, um falsch positive Ergebnisse zu reduzieren.

### <a name="risk-remediation"></a>Risikowartung

Organisationen können den Zugriff blockieren, wenn ein Risiko erkannt wird. Das Blockieren hindert berechtigte Benutzer manchmal daran, das zu tun, was sie tun müssen. Eine bessere Lösung besteht darin, Selbstwartung mithilfe von Azure AD Multi-Factor Authentication (MFA) und Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) zuzulassen.

- Wenn eine Benutzerrisikorichtlinie ausgelöst wird: 
   - Administratoren können eine sichere Kennwortzurücksetzung verlangen, die Azure AD MFA erforderlich macht, bevor der Benutzer ein neues Kennwort mit SSPR erstellt, um das Benutzerrisiko zurückzusetzen. 
- Wenn eine Anmelderisiko-Richtlinie ausgelöst wird: 
   - Azure AD MFA kann ausgelöst werden, sodass Benutzer mithilfe einer ihrer registrierten Authentifizierungsmethoden ihre Identität nachweisen können, wodurch das Anmelderisiko zurückgesetzt wird. 

> [!WARNING]
> Benutzer müssen sich für Azure AD MFA und SSPR registrieren, bevor sie mit einer wartungsbedürftigen Situation konfrontiert werden. Nicht registrierte Benutzer werden blockiert und erfordern einen Administratoreingriff.
> 
> Die Kennwortänderung (die Änderung eines bekannten Kennworts in ein neues) außerhalb des Wartungsflows für riskante Benutzerrichtlinien erfüllt nicht die Anforderung der sicheren Kennwortzurücksetzung.

## <a name="exclusions"></a>Ausschlüsse

Richtlinien ermöglichen das Ausschließen von Benutzern, wie [Benutzer- oder Administratorkonten für den Notfallzugriff](../roles/security-emergency-access.md). Organisationen müssen unter Umständen andere Konten basierend auf der Art und Weise, wie die Konten verwendet werden, aus bestimmten Richtlinien ausschließen. Ausschlüsse sollten regelmäßig überprüft werden, um festzustellen, ob sie weiterhin angewendet werden sollen.

## <a name="enable-policies"></a>Aktivieren von Richtlinien

Es gibt zwei Orte, an denen diese Richtlinien konfiguriert werden können: bedingter Zugriff und Identity Protection. Die Konfiguration mithilfe von Richtlinien für bedingten Zugriff ist die bevorzugte Methode, da sie mehr Kontext bietet, wie: 

   - Erweiterte Diagnosedaten
   - Integration des Modus „Nur melden“
   - Graph-API-Unterstützung
   - Verwendung von mehr Attributen für bedingten Zugriff in der Richtlinie

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

Vor dem Aktivieren von Korrekturrichtlinien sollten Organisationen eventuell alle aktiven Risiken [untersuchen](howto-identity-protection-investigate-risk.md) und [korrigieren](howto-identity-protection-remediate-unblock.md).

### <a name="user-risk-with-conditional-access"></a>Benutzerrisiko bei bedingtem Zugriff

1. Melden Sie sich beim **Azure-Portal** als globaler Administrator, Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Benennen Sie Ihre Richtlinie. Es wird empfohlen, dass Unternehmen einen aussagekräftigen Standard für die Namen ihrer Richtlinien erstellen.
1. Klicken Sie unter **Zuweisungen** auf **Benutzer und Gruppen**.
   1. Wählen Sie unter **Einschließen** die Option **Alle Benutzer** aus.
   1. Wählen Sie unter **Ausschließen** die Option **Benutzer und Gruppen** und dann die Konten für den Notfallzugriff Ihres Unternehmens aus. 
   1. Wählen Sie **Fertig** aus.
1. Wählen Sie unter **Cloud-Apps oder -aktionen** > **Einschließen** die Option **Alle Cloud-Apps** aus.
1. Legen Sie unter **Bedingungen** > **Benutzerrisiko** die Option **Konfigurieren** auf **Ja** fest. 
   1. Unter  **konfigurieren Sie die Benutzerrisikostufen, die für die Erzwingung der Richtlinie erforderlich sind** wählen Sie **hoch**.
   1. Wählen Sie **Fertig** aus.
1. Unter **Zugriffssteuerungen** > **Erteilen**:
   1. Wählen **Sie Zugriff gewähren und**, **Kennwortänderung erforderlich aus.**
   1. Wählen Sie **Auswählen**.
1. Bestätigen Sie die Einstellungen, und legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Wählen Sie **Erstellen** aus, um die Richtlinie zu erstellen und zu aktivieren.

### <a name="sign-in-risk-with-conditional-access"></a>Anmelderisiko bei bedingtem Zugriff

1. Melden Sie sich beim **Azure-Portal** als globaler Administrator, Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Benennen Sie Ihre Richtlinie. Es wird empfohlen, dass Unternehmen einen aussagekräftigen Standard für die Namen ihrer Richtlinien erstellen.
1. Klicken Sie unter **Zuweisungen** auf **Benutzer und Gruppen**.
   1. Wählen Sie unter **Einschließen** die Option **Alle Benutzer** aus.
   1. Wählen Sie unter **Ausschließen** die Option **Benutzer und Gruppen** und dann die Konten für den Notfallzugriff Ihres Unternehmens aus. 
   1. Wählen Sie **Fertig** aus.
1. Wählen Sie unter **Cloud-Apps oder -aktionen** > **Einschließen** die Option **Alle Cloud-Apps** aus.
1. Legen Sie unter **Bedingungen** > **Anmelderisiko** die Option **Konfigurieren** auf **Ja** fest. Wählen Sie unter **Anmelderisikostufe auswählen, auf die diese Richtlinie angewendet werden soll** 
   1. entweder **Hoch** oder **Mittel** aus.
   1. Wählen Sie **Fertig** aus.
1. Unter **Zugriffssteuerungen** > **Erteilen**:
   1. Wählen Sie **Zugriff gewähren**, **Authentifizierung unter mehreren Gesichtspunkten erforderlich**.
   1. Wählen Sie **Auswählen**.
1. Bestätigen Sie die Einstellungen und legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Wählen Sie **Erstellen** aus, um die Richtlinie zu erstellen und zu aktivieren.

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren der Registrierungsrichtlinie für Azure AD Multi-Factor Authentication](howto-identity-protection-configure-mfa-policy.md)

- [Was bedeutet Risiko?](concept-identity-protection-risks.md)

- [Untersuchen von Risikoerkennungen](howto-identity-protection-investigate-risk.md)

- [Simulieren von Risikoerkennungen](howto-identity-protection-simulate-risk.md)
