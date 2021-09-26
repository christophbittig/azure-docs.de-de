---
title: Einrichten des Azure AD B2C-Identitätsanbieters „Lokales Konto“
titleSuffix: Azure AD B2C
description: Definieren Sie die Identitätstypen, mit denen Benutzer sich bei Ihrem Azure Active Directory B2C-Mandanten registrieren oder anmelden können (E-Mail, Benutzername, Telefonnummer).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f5f72fa4dc126074177b96a484c10ade06724f83
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128574441"
---
# <a name="set-up-the-local-account-identity-provider"></a>Einrichten des Identitätsanbieters „Lokales Konto“

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

In diesem Artikel wird beschrieben, wie Sie Anmeldemethoden für Ihre lokalen Azure AD B2C-Konten festlegen. Ein lokales Konto ist ein Konto, das in Ihrem Azure AD B2C-Verzeichnis von einem Administrator oder durch die Registrierung eines Benutzers für Ihre Anwendung erstellt wird. Benutzernamen und Kennwörter werden lokal gespeichert, und Azure AD B2C fungiert als Identitätsanbieter für lokale Konten.

Für lokale Konten sind mehrere Anmeldemethoden verfügbar:

- **E-Mail**: Benutzer können sich mit ihrer E-Mail-Adresse und ihrem Kennwort bei Ihrer App registrieren und anmelden. In den Einstellungen des Identitätsanbieters „Lokales Konto“ ist standardmäßig die Registrierung mit E-Mail-Adresse aktiviert.
- **Benutzername**: Benutzer können sich mit einem Benutzernamen und einem Kennwort bei Ihrer App registrieren und anmelden.
- **Telefon (oder „kennwortlose Authentifizierung“)** : Benutzer können sich bei Ihrer App registrieren und anmelden, indem sie eine Telefonnummer als primäre Anmelde-ID verwenden. Sie müssen keine Kennwörter erstellen. Einmalkennwörter werden per SMS an Ihre Benutzer gesendet.
- **Telefon oder E-Mail**: Benutzer können sich registrieren oder anmelden, indem sie eine Telefonnummer oder eine E-Mail-Adresse eingeben. Basierend auf der Benutzereingabe leitet Azure AD B2C den Benutzer zum entsprechenden Flow auf der Registrierungs- oder Anmeldeseite weiter.
- **Telefonwiederherstellung**: Wenn Sie die Registrierung oder Anmeldung per Telefon aktiviert haben, können Benutzer über die Telefonwiederherstellung eine E-Mail-Adresse zum Wiederherstellen ihres Kontos für den Fall angeben, dass sie ihr Telefon nicht zur Hand haben.

Weitere Informationen zu diesen Methoden finden Sie unter [Anmeldeoptionen](sign-in-options.md). 

Informationen zum Konfigurieren von Einstellungen für Identitäten bei sozialen Netzwerken oder Unternehmen, bei denen die Identität des Benutzers von einem Verbundidentitätsanbieter wie Facebook und Google verwaltet wird, finden Sie unter [Hinzufügen eines Identitätsanbieters zu Ihrem Azure Active Directory B2C-Mandanten](add-identity-provider.md).

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>Konfigurieren der Einstellungen des Identitätsanbieters „Lokales Konto“


Sie können die Anmeldemethoden für lokale Konten (E-Mail, Benutzername oder Telefonnummer) auswählen, die Sie in Ihrem Mandanten zur Verfügung stellen möchten, indem Sie den Anbieter **Lokales Konto** in Ihrer Liste der Azure AD B2C-**Identitätsanbieter** konfigurieren. Wenn Sie anschließend einen Benutzerflow einrichten, können Sie eine der Anmeldemethoden für lokale Konten auswählen, die Sie mandantenweit aktiviert haben. Sie können für einen Benutzerflow nur eine Anmeldemethode für lokale Konten auswählen, aber Sie können für jeden Benutzerflow eine andere Option auswählen.

So legen Sie die Anmeldeoptionen für lokale Konten auf Mandantenebene fest 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus. Oder verwenden Sie das Suchfeld, um nach **Azure AD B2C** zu suchen und diese Option auszuwählen.
1. Wählen Sie unter **Verwalten** die Option **Identitätsanbieter** aus.
1. Wählen Sie in der Liste der Identitätsanbieter die Option **Lokales Konto** aus.
1. Wählen Sie auf der Seite **Lokalen IDP konfigurieren** mindestens einen Identitätstyp aus, der für Benutzerflows in Ihrem Azure AD B2C-Mandanten aktiviert werden soll. Wenn Sie hier eine Option auswählen, wird sie mandantenweit zur Verfügung gestellt. Beim Erstellen oder Ändern eines Benutzerflows können Sie eine der hier aktivierten Optionen auswählen.

   - **Telefon**: Benutzer werden aufgefordert, eine Telefonnummer einzugeben, die bei der Registrierung überprüft und als Benutzer-ID festgelegt wird.
   - **Benutzername**: Benutzer können ihre eigene eindeutige Benutzer-ID erstellen. Eine E-Mail-Adresse des Benutzers wird erfasst und überprüft.
   - **E-Mail**: Benutzer werden zur Eingabe einer E-Mail-Adresse aufgefordert, die bei der Registrierung überprüft und als Benutzer-ID festgelegt wird.
1. Wählen Sie **Speichern** aus.

## <a name="configure-your-user-flow"></a>Konfigurieren des Benutzerflows

1. Wählen Sie im Azure-Portal im linken Menü die Option **Azure AD B2C** aus.
1. Wählen Sie unter **Richtlinien** die Option **Benutzerflows (Richtlinien)** aus.
1. Wählen Sie den Benutzerflow aus, für den Sie die Registrierung und Anmeldung konfigurieren möchten.
1. Wählen Sie **Identitätsanbieter** aus.
1. Wählen Sie unter **Lokale Konten** eine der folgenden Optionen aus: **E-Mail-Registrierung**,  **Benutzer-ID-Registrierung**, **Telefonregistrierung**, **Telefon-/E-Mail-Registrierung** oder **Keine**.

### <a name="enable-the-recovery-email-prompt"></a>Aktivieren der Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse

Wenn Sie die Option **Telefonregistrierung** oder **Telefon-/E-Mail-Registrierung** auswählen, aktivieren Sie die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie in Azure AD B2C unter **Richtlinien** die Option **Benutzerflows** aus.
1. Wählen Sie den Benutzerflow in der Liste aus.
1. Wählen Sie unter **Einstellungen** die Option **Eigenschaften** aus.
1. Wählen Sie neben **Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse für die telefonische Registrierung und Anmeldung aktivieren (Vorschau)** Folgendes aus:
   - **Ein**: Die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse wird bei der Registrierung und Anmeldung angezeigt.
   - **Aus**: Die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse wird ausgeblendet.
1. Wählen Sie **Speichern** aus.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>Abrufen des Starter Packs

Benutzerdefinierte Richtlinien bestehen aus mehreren XML-Dateien, die Sie in den Azure AD B2C-Mandanten hochladen, um User Journeys zu definieren. Wir stellen Starter Packs mit mehreren vordefinierten Richtlinien bereit. Laden Sie das relevante Starter Pack herunter: 

- [Anmeldung per E-Mail-Adresse](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [Anmeldung mit Benutzername](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [Anmeldung per Telefon](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless) Wählen Sie die [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml)-Richtlinie der vertrauenden Seite aus. 
- [Anmeldung per Telefon oder E-Mail](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). Wählen Sie die [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml)-Richtlinie der vertrauenden Seite aus.

Was zu tun ist, wenn Sie das Starter Pack heruntergeladen haben.

1. Ersetzen Sie in jeder Datei die Zeichenfolge `yourtenant` durch den Namen Ihres Azure AD B2C-Mandanten. Wenn der Name des B2C-Mandanten z.B. *contosob2c* lautet, werden alle Instanzen von `yourtenant.onmicrosoft.com` zu `contosob2c.onmicrosoft.com`.

1. Führen Sie die Schritte im Abschnitt [Hinzufügen von Anwendungs-IDs zur benutzerdefinierten Richtlinie](tutorial-create-user-flows.md?pivots=b2c-custom-policy#add-application-ids-to-the-custom-policy) von [Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy) aus. Aktualisieren Sie z. B. `/phone-number-passwordless/`**`Phone_Email_Base.xml`** mit den **Anwendungs-IDs (Client-IDs)** der beiden Anwendungen, die Sie beim Abschließen der Voraussetzungen registriert haben: *IdentityExperienceFramework* und *ProxyIdentityExperienceFramework*.
1. Hochladen der Richtliniendateien

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen externer Identitätsanbieter](add-identity-provider.md)
- [Erstellen eines Benutzerflows](tutorial-create-user-flows.md)
