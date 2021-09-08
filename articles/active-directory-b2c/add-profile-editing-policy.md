---
title: Einrichten eines Profilbearbeitungsflows
titleSuffix: Azure AD B2C
description: Hier erfahren Sie, wie Sie in Azure Active Directory B2C einen Profilbearbeitungsflow einrichten.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/07/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f019fdc64ca30954017afc34267dcef998cf5fb8
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112198834"
---
# <a name="set-up-a-profile-editing-flow-in-azure-active-directory-b2c"></a>Einrichten eines Profilbearbeitungsflows in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="profile-editing-flow"></a>Profilbearbeitungsflow

Mithilfe der Richtlinie für die Profilbearbeitung können Benutzer ihre Profilattribute wie Anzeigename, Nachname, Vorname, Stadt und andere Informationen verwalten. Der Profilbearbeitungsflow umfasst die folgenden Schritte: 

1. Registrieren Sie sich, oder melden Sie sich mit einem lokalen Konto oder einem Social-Media-Konto an. Wenn die Sitzung noch aktiv ist, autorisiert Azure AD B2C den Benutzer und fährt mit dem nächsten Schritt fort.
1. Azure AD B2C liest das Benutzerprofil aus dem Verzeichnis und ermöglicht dem Benutzer das Bearbeiten der Attribute.

![Profilbearbeitungsflow](./media/add-profile-editing-policy/profile-editing-flow.png)


## <a name="prerequisites"></a>Voraussetzungen

Wenn dies noch nicht erfolgt ist, [registrieren Sie eine Webanwendung in Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-profile-editing-user-flow"></a>Erstellen eines Benutzerflows für die Profilbearbeitung

Wenn Sie Benutzern die Profilbearbeitung in Ihrer Anwendung ermöglichen möchten, verwenden Sie einen Benutzerflow für die Profilbearbeitung.

1. Wählen Sie auf der Übersichtsseite des Azure AD B2C-Mandanten im Menü die Option **Benutzerflows** und dann **Neuer Benutzerflow** aus.
1. Wählen Sie auf der Seite **Benutzerflow erstellen** den Benutzerflow **Profilbearbeitung** aus. 
1. Wählen Sie unter **Version auswählen** die Option **Empfohlen** und dann **Erstellen** aus.
1. Geben Sie unter **Name** einen Namen für den Benutzerflow ein. Beispiel: *profileediting1*.
1. Wählen Sie unter **Identitätsanbieter** mindestens einen Identitätsanbieter aus:

   * Wählen Sie unter **Lokale Konten** eine der folgenden Optionen aus: **E-Mail-Anmeldung**, **Benutzer-ID-Anmeldung**, **Telefonanmeldung**, **Telefon-/E-Mail-Anmeldung**, **Benutzer-ID-/E-Mail-Anmeldung** oder **Keine**. [Weitere Informationen](sign-in-options.md)
   * Wählen Sie unter **Soziale Identitätsanbieter** einen der externen sozialen oder Unternehmensidentitätsanbieter aus, die Sie eingerichtet haben. [Weitere Informationen](add-identity-provider.md)
1. Wenn Sie unter **Mehrstufige Authentifizierung** festlegen möchten, dass Benutzer ihre Identität mit einer zweiten Authentifizierungsmethode überprüfen müssen, wählen Sie den Methodentyp aus und den Zeitpunkt, an dem die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) erzwungen werden soll. [Weitere Informationen](multi-factor-authentication.md)
1. Wenn Sie unter **Bedingter Zugriff** Richtlinien für den bedingten Zugriff für Ihren Azure AD B2C-Mandanten konfiguriert haben und ihn für diesen Benutzerfluss aktivieren möchten, aktivieren Sie das Kontrollkästchen **Richtlinien für bedingten Zugriff erzwingen**. Sie müssen keinen Richtliniennamen angeben. [Weitere Informationen](conditional-access-user-flow.md?pivots=b2c-user-flow)
1. Wählen Sie unter **Benutzerattribute** die Attribute aus, die der Kunde in seinem Profil bearbeiten darf. Um eine vollständige Liste der Werte zu erhalten, wählen Sie **Mehr anzeigen** aus, wählen Sie die Werte aus, und wählen Sie dann **OK**.
1. Wählen Sie **Erstellen** aus, um den Benutzerflow hinzuzufügen. Dem Namen wird automatisch das Präfix *B2C_1* vorangestellt.

### <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Wählen Sie den von Ihnen erstellten Benutzerflow aus, um die entsprechende Übersichtsseite zu öffnen, und wählen Sie dann **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Klicken Sie auf **Benutzerflow ausführen**, und melden Sie sich mit dem zuvor erstellten Konto an.
1. Sie haben jetzt die Möglichkeit, den Anzeigenamen und den Stellentitel für den Benutzer zu ändern. Klicken Sie auf **Weiter**. Das Token wird an `https://jwt.ms` zurückgegeben und sollte Ihnen angezeigt werden.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>Erstellen einer Richtlinie für die Profilbearbeitung

Benutzerdefinierte Richtlinien bestehen aus mehreren XML-Dateien, die Sie in den Azure AD B2C-Mandanten hochladen, um User Journeys zu definieren. Es sind Starter Packs mit mehreren integrierten Richtlinien verfügbar, z. B. für die Registrierung und Anmeldung, für die Kennwortzurücksetzung und für die Profilbearbeitung. Weitere Informationen finden Sie unter [Erste Schritte für benutzerdefinierte Richtlinien in Azure Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy).

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen eines Identitätsanbieters zu Ihrem Azure Active Directory B2C-Mandanten](add-identity-provider.md)