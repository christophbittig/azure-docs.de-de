---
title: Kennwortlose Anmeldung mit Sicherheitsschlüsseln – Azure Active Directory
description: Aktivieren der kennwortlosen Anmeldung mit FIDO2-Sicherheitsschlüsseln bei Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/12/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 033bc34d82e497f7de7b63d8e69a606e9a9501ee
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486811"
---
# <a name="enable-passwordless-security-key-sign-in"></a>Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln 

Sicherheitsschlüssel bieten Unternehmen, die aktuell Kennwörter verwenden und über eine freigegebene PC-Umgebung verfügen, eine nahtlose Möglichkeit für Mitarbeiter, sich ohne Eingabe eines Benutzernamens oder Kennworts zu authentifizieren. Sicherheitsschlüssel ermöglichen den Mitarbeitern eine bessere Produktivität und bieten eine höhere Sicherheit.

In diesem Dokument liegt der Schwerpunkt auf der Aktivierung der auf Sicherheitsschlüsseln basierenden kennwortlosen Authentifizierung. Am Ende dieses Artikels können Sie sich mit Ihrem Azure AD-Konto mithilfe eines FIDO2-Sicherheitsschlüssels bei webbasierten Anwendungen anmelden.

## <a name="requirements"></a>Requirements (Anforderungen)

- [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md)
- Aktivieren der [kombinierten Registrierung von Sicherheitsinformationen](concept-registration-mfa-sspr-combined.md)
- Kompatible [FIDO2-Sicherheitsschlüssel](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN erfordert Windows 10, Version 1903 oder höher**

Um Sicherheitsschlüssel für die Anmeldung bei Web-Apps und-Diensten verwenden zu können, benötigen Sie einen Browser, der das WebAuthN-Protokoll unterstützt. Hierzu zählen Microsoft Edge, Chrome, Firefox und Safari.

## <a name="prepare-devices"></a>Vorbereiten von Geräten

Für in Azure AD eingebundene Geräte eignet sich besonders Windows 10, Version 1903 oder höher.

Auf hybrid in Azure AD eingebundenen Geräten muss Windows 10, Version 2004 oder höher, ausgeführt werden.

## <a name="enable-passwordless-authentication-method"></a>Aktivieren von Methoden zur kennwortlosen Authentifizierung

### <a name="enable-the-combined-registration-experience"></a>Aktivieren der kombinierten Registrierung

Die Registrierungsfunktionen für Methoden zur kennwortlosen Authentifizierung sind von der kombinierten Registrierungsfunktion abhängig. Führen Sie die im Artikel [Aktivieren der kombinierten Registrierung von Sicherheitsinformationen](howto-registration-mfa-sspr-combined.md) aufgeführten Schritte aus, um die kombinierte Registrierung zu aktivieren.

### <a name="enable-fido2-security-key-method"></a>Aktivieren der Methode „FIDO2-Sicherheitsschlüssel“

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Authentifizierungsmethoden** > **Authentifizierungsmethodenrichtlinie**.
1. Wählen Sie unter der Methode **FIDO2-Sicherheitsschlüssel** die folgenden Optionen aus:
   1. **Aktivieren**: „Ja“ oder „Nein“
   1. **Ziel**: „Alle Benutzer“ oder „Benutzer auswählen“
1. **Speichern** Sie die Konfiguration.

   >[!NOTE]
   >Wenn Sie beim Speichern eine Fehlermeldung erhalten, kann die Ursache in der Anzahl der hinzugefügten Benutzer oder Gruppen liegen. Als Abhilfe können Sie die Benutzer und Gruppen, die Sie hinzufügen möchten, im selben Vorgang durch eine einzige Gruppe ersetzen und dann erneut auf **Speichern** klicken.


### <a name="fido-security-key-optional-settings"></a>Optionale Einstellungen für FIDO-Sicherheitsschlüssel 

Es gibt einige optionale Einstellungen für die Verwaltung von Sicherheitsschlüsseln pro Mandant.  

![Screenshot: FIDO2-Sicherheitsschlüsseloptionen](media/howto-authentication-passwordless-security-key/optional-settings.png) 

**Allgemein**

- **Self-Service-Einrichtung zulassen** sollte auf **Ja** festgelegt bleiben. Wenn diese Einstellung auf „Nein“ festgelegt ist, können Ihre Benutzer keinen FIDO-Schlüssel über das MySecurityInfo-Portal registrieren, auch wenn dies durch die Richtlinie für Authentifizierungsmethoden aktiviert ist.  
- Die **Nachweis erzwingen**-Einstellung auf **Ja** erfordert, dass die FIDO-Sicherheitsschlüsselmetadaten mit der FIDO Alliance Metadata Service veröffentlicht und überprüft werden, und sie müssen auch den zusätzlichen Überprüfungstest von Microsoft bestehen. Weitere Informationen finden Sie unter [Was ist ein Microsoft-kompatibler Sicherheitsschlüssel?](/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key)

**Schlüsseleinschränkungsrichtlinie**

- **Erzwingung von Schlüsseleinschränkungen** sollte nur auf **Ja** festgelegt werden, wenn Ihre Organisation nur bestimmte FIDO-Sicherheitsschlüssel zulassen oder nicht zulassen möchte, die durch ihre AAGuids identifiziert werden. Sie können mit Ihrem Sicherheitsschlüsselanbieter zusammenarbeiten, um die AAGuids ihrer Geräte zu bestimmen. Wenn der Schlüssel bereits registriert ist, kann AAGUID auch durch Anzeigen der Details der Authentifizierungsmethode des Schlüssels pro Benutzer gefunden werden. 


## <a name="disable-a-key"></a>Deaktivieren eines Schlüssels 

Um einen FIDO2-Schlüssel zu entfernen, der einem Benutzerkonto zugeordnet ist, löschen Sie den Schlüssel aus den Authentifizierungsmethoden des Benutzers.

1. Melden Sie sich beim Azure AD-Portal an, und suchen Sie nach dem Benutzerkonto, aus dem der FIDO-Schlüssel entfernt werden soll.
1. Wählen Sie **Authentifizierungsmethoden** aus, klicken Sie mit der rechten Maustaste auf **FIDO2-Sicherheitsschlüssel**, und klicken Sie dann auf **Löschen**. 

    ![Anzeigen von Details zu Authentifizierungsmethoden](media/howto-authentication-passwordless-deployment/security-key-view-details.png)

## <a name="security-key-authenticator-attestation-guid-aaguid"></a>AAGUID (Authenticator Attestation GUID, Authenticator-Nachweis-GUID) als Sicherheitsschlüssel

Gemäß FIDO2-Spezifikation muss jeder Sicherheitsschlüsselanbieter während des Nachweises eine AAGUID (Authenticator Attestation GUID) bereitstellen. Die AAGUID ist eine 128-Bit-ID, die den Schlüsseltyp angibt (z. B. Aussteller und Modell). 

>[!NOTE]
>Der Hersteller muss sicherstellen, dass die AAGUID für alle ähnlichen Schlüssel dieses Herstellers identisch ist und sich gleichzeitig (mit hoher Wahrscheinlichkeit) von den AAGUIDs aller anderen Schlüsseltypen unterscheidet. Um dies sicherzustellen, sollte die AAGUID für einen bestimmten Sicherheitsschlüsseltyp nach dem Zufallsprinzip generiert werden. Weitere Informationen finden Sie unter [Web Authentication: An API for accessing Public Key Credentials – Level 3](https://w3c.github.io/webauthn/) (Webauthentifizierung: Eine API für den Zugriff auf Anmeldeinformationen für öffentliche Schlüssel – Ebene 3 (w3.org)).

Es gibt zwei Möglichkeiten für das Abrufen Ihrer AAGUID. Sie können entweder Ihren Sicherheitsschlüsselanbieter fragen oder die Details zu den Authentifizierungsmethoden des Schlüssels pro Benutzer anzeigen.

![Anzeigen der AAGUID für einen Sicherheitsschlüssel](media/howto-authentication-passwordless-deployment/security-key-aaguid-details.png)

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Benutzerregistrierung und Verwaltung von FIDO2-Sicherheitsschlüsseln

1. Navigieren Sie zu [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Melden Sie sich an, falls Sie dies noch nicht getan haben.
1. Klicken Sie auf **Sicherheitsinformation**.
   1. Wenn der Benutzer bereits mindestens eine Azure AD Multi-Factor Authentication-Methode registriert hat, kann er sofort einen FIDO2-Sicherheitsschlüssel registrieren.
   1. Wenn der Benutzer keine Azure AD Multi-Factor Authentication-Methode registriert hat, muss er eine solche hinzufügen.
1. Fügen Sie einen FIDO2-Sicherheitsschlüssel hinzu, indem Sie auf **Methode hinzufügen** klicken und dann **Sicherheitsschlüssel** auswählen.
1. Wählen Sie **USB-Gerät** oder **NFC-Gerät** aus.
1. Halten Sie Ihren Schlüssel bereit, und wählen Sie **Weiter** aus.
1. Im daraufhin angezeigten Feld wird der Benutzer aufgefordert, eine PIN für den Sicherheitsschlüssel zu erstellen/einzugeben und dann die erforderliche Geste für den Schlüssel biometrisch oder per Touch auszuführen.
1. Der Benutzer gelangt wieder zurück zur Oberfläche für die kombinierte Registrierung und wird aufgefordert, einen aussagekräftigen Namen für den Schlüssel anzugeben, damit der Benutzer diesen von den anderen unterscheiden kann. Klicken Sie auf **Weiter**.
1. Klicken Sie auf **Fertig**, um den Vorgang abzuschließen.

## <a name="sign-in-with-passwordless-credential"></a>Anmelden mit kennwortlosen Anmeldeinformationen

Im nachfolgenden Beispiel hat der Benutzer bereits einen FIDO2-Sicherheitsschlüssel bereitgestellt. Der Benutzer kann sich im Web mit seinem FIDO2-Sicherheitsschlüssel in einem unterstützten Browser unter Windows 10, Version 1903 oder höher, anmelden.

![Anmeldung mit Sicherheitsschlüssel in Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Problembehandlung und Feedback

Wenn Sie Feedback geben möchten oder Probleme mit diesem Feature auftreten, teilen Sie uns dies in folgenden Schritten über die Windows-Feedback-Hub-App mit:

1. Starten Sie **Feedback-Hub**, und stellen Sie sicher, dass Sie angemeldet sind.
1. Senden Sie Ihr Feedback unter der folgenden Kategorisierung:
   - Kategorie: Sicherheit und Datenschutz
   - Unterkategorie: FIDO
1. Verwenden Sie die Option **Problem reproduzieren**, um Protokolle zu erfassen.

## <a name="known-issues"></a>Bekannte Probleme

### <a name="security-key-provisioning"></a>Bereitstellung von Sicherheitsschlüsseln

Die Administratorbereitstellung und Bereitstellungsaufhebung von Sicherheitsschlüsseln ist nicht verfügbar.


### <a name="upn-changes"></a>Änderungen des Benutzerprinzipalnamens

Wenn der Benutzerprinzipalname eines Benutzers geändert wird, können Sie die FIDO2-Sicherheitsschlüssel nicht mehr ändern, um dies zu berücksichtigen. Die Lösung für einen Benutzer mit einem FIDO2-Sicherheitsschlüssel besteht darin, sich bei MySecurityInfo anzumelden, den alten Schlüssel zu löschen und einen neuen hinzuzufügen.

## <a name="next-steps"></a>Nächste Schritte

[Anmelden mit FIDO2-Sicherheitsschlüsseln bei Windows 10-Geräten](howto-authentication-passwordless-security-key-windows.md)

[Aktivieren der FIDO2-Authentifizierung für lokale Ressourcen](howto-authentication-passwordless-security-key-on-premises.md)

[Erfahren Sie mehr über die Geräteregistrierung](../devices/overview.md)

[Erfahren Sie mehr über Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)