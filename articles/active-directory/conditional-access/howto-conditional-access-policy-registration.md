---
title: 'Bedingter Zugriff: Kombinierte Sicherheitsinformationen – Azure Active Directory'
description: Erstellen einer benutzerdefinierten Richtlinie für bedingten Zugriff zur Registrierung von Sicherheitsinformationen
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/28/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6abc633191ab07460dc2efb3dde7b21642683ff
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596446"
---
# <a name="conditional-access-securing-security-info-registration"></a>Bedingter Zugriff: Sichere Registrierung von Sicherheitsinformationen

Mit Benutzeraktionen in der Richtlinie für bedingten Zugriff kann sichergestellt werden, wann und wie sich Benutzer für Azure AD Multi-Factor Authentication registrieren, und auch die Self-Service-Kennwortzurücksetzung ist möglich. Dieses Feature ist für Organisationen verfügbar, die die [kombinierte Registrierung](../authentication/concept-registration-mfa-sspr-combined.md) aktiviert haben. Diese Funktion ermöglicht es Organisationen, den Registrierungsprozess wie jede beliebige Anwendung in einer Richtlinie für bedingten Zugriff zu behandeln und die volle Leistungsfähigkeit bedingter Zugriffe zu nutzen, um die Funktionalität zu schützen. Benutzer, die sich bei der Microsoft Authenticator-App anmelden oder die kennwortlose Anmeldung per Telefon aktivieren, unterliegen dieser Richtlinie.

Einige Organisationen haben in der Vergangenheit möglicherweise vertrauenswürdige Netzwerkadressen oder Gerätekonformität zum Schützen der Registrierung verwendet. Durch das Hinzufügen eines [befristeten Zugriffspasses](../authentication/howto-authentication-temporary-access-pass.md) in Azure AD können Administratoren zeitlich begrenzte Anmeldeinformationen für Benutzer bereitstellen, damit diese sich an jedem Gerät oder Standort registrieren können. Die Anmeldeinformationen eines befristeten Zugriffspasses erfüllen die Anforderungen für bedingte Zugriffe für eine mehrstufige Authentifizierung.

## <a name="create-a-policy-to-secure-registration"></a>Erstellen einer Richtlinie für eine sichere Registrierung

Die folgende Richtlinie wird auf die ausgewählten Benutzer angewendet, die versuchen, sich mit der kombinierten Registrierung anzumelden. Die Richtlinie setzt voraus, dass Benutzer eine mehrstufige Authentifizierung durchführen oder Anmeldeinformationen eines befristeten Zugriffspasses verwenden.

1. Navigieren Sie im **Azure-Portal** zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Geben Sie unter „Name“ einen Namen für diese Richtlinie ein. Beispiel: **Kombinierte Registrierung mit Sicherheitsinformationen mit TAP**.
1. Wählen Sie unter **Zuweisungen** die Option **Benutzer und Gruppen** und dann die Benutzer und Gruppen aus, auf die Sie diese Richtlinie anwenden möchten.
   1. Wählen Sie unter **Einschließen** die Option **Alle Benutzer** aus.

      > [!WARNING]
      > Benutzer müssen für die [kombinierte Registrierung](../authentication/howto-registration-mfa-sspr-combined.md) aktiviert sein.

   1. Führen Sie unter **Ausschließen** die folgenden Schritte aus.
      1. Wählen Sie **Alle Gastbenutzer und externen Benutzer** aus.
      
         > [!NOTE]
         > Der befristete Zugriffspass funktioniert nicht für Gastbenutzer.

      1. Wählen Sie **Benutzer und Gruppen** und dann die Konten für den Notfallzugriff Ihres Unternehmens aus. 
1. Wählen Sie unter **Cloud-Apps oder -aktionen** die Option **Benutzeraktionen** aus, und aktivieren Sie **Sicherheitsinformationen registrieren**.
1. Unter **Zugriffssteuerungen** > **Erteilen**:
   1. Wählen Sie **Zugriff gewähren** aus.
   1. Wählen Sie **Mehrstufige Authentifizierung erforderlich** aus.
   1. Klicken Sie auf **Auswählen**.
1. Legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Klicken Sie anschließend auf **Erstellen**.

Administratoren müssen jetzt Anmeldeinformationen für befristete Zugriffspässe für neue Benutzer ausstellen, damit die Anforderungen für die mehrstufige Authentifizierung bei der Registrierung erfüllt werden können. Die Schritte zum Ausführen dieser Aufgabe finden Sie im Abschnitt zum [Erstellen eines befristeten Zugriffspasses im Azure AD-Portal](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass).

Organisationen können zusätzlich zur oder anstelle der **mehrstufigen Authentifizierung** in Schritt 6b weitere erforderliche Steuerelemente für die Erteilung von Berechtigungen festlegen. Bei Auswahl mehrerer Steuerelemente muss der entsprechende Optionsumschalter aktiviert werden, um festzulegen, ob bei dieser Änderung **alle** oder **ein** ausgewähltes Steuerelement erforderlich ist.

### <a name="guest-user-registration"></a>Gastbenutzerregistrierung

Für [Gastbenutzer](../external-identities/what-is-b2b.md), die sich für die mehrstufige Authentifizierung im Verzeichnis registrieren müssen, können Sie die Registrierung von nicht [vertrauenswürdigen Netzwerkadressen](concept-conditional-access-conditions.md#locations) wie nachstehend beschrieben blockieren.

1. Navigieren Sie im **Azure-Portal** zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Geben Sie unter „Name“ einen Namen für diese Richtlinie ein. Beispiel: **Kombinierte Registrierung mit Sicherheitsinformationen in vertrauenswürdigen Netzwerken**.
1. Wählen Sie unter **Zuweisungen** die Option **Benutzer und Gruppen** und dann die Benutzer und Gruppen aus, auf die Sie diese Richtlinie anwenden möchten.
   1. Wählen Sie unter **Einschließen** die Option **Alle Gastbenutzer und externen Benutzer** aus.
1. Wählen Sie unter **Cloud-Apps oder -aktionen** die Option **Benutzeraktionen** aus, und aktivieren Sie **Sicherheitsinformationen registrieren**.
1. Unter **Bedingungen** > **Standorte**:
   1. Konfigurieren Sie **Ja**.
   1. Schließen Sie **Alle Standorte** ein.
   1. Schließen Sie **Alle vertrauenswürdigen Standorte** aus.
   1. Wählen Sie auf dem Blatt „Standorte“ die Option **Fertig** aus.
   1. Wählen Sie auf dem Blatt „Bedingungen“ die Option **Fertig** aus.
1. Unter **Zugriffssteuerungen** > **Erteilen**:
   1. Wählen Sie **Zugriff blockieren** aus.
   1. Klicken Sie dann auf **Auswählen**.
1. Legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Klicken Sie dann auf **Speichern**.

## <a name="next-steps"></a>Nächste Schritte

[Allgemeine Richtlinien für bedingten Zugriff](concept-conditional-access-policy-common.md)

[Bestimmen der Auswirkung durch Verwendung des reinen Berichtsmodus des bedingten Zugriffs](howto-conditional-access-insights-reporting.md)

[Simulieren des Anmeldeverhaltens mit dem Was-wäre-wenn-Tool für den bedingten Zugriff](troubleshoot-conditional-access-what-if.md)

[Benutzer müssen Authentifizierungsinformationen erneut bestätigen](../authentication/concept-sspr-howitworks.md#reconfirm-authentication-information)
