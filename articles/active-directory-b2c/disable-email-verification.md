---
title: Deaktivieren der E-Mail-Überprüfung während der Kundenregistrierung
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie die E-Mail-Überprüfung bei der Benutzerregistrierung in Azure Active Directory B2C deaktivieren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 5d7d332d0ce74151ff42bc689b0222f7020dc50d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128575207"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Deaktivieren der E-Mail-Überprüfung bei der Benutzerregistrierung in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Standardmäßig überprüft Azure Active Directory B2C (Azure AD B2C) die E-Mail-Adresse Ihres Kunden für lokale Konten (Konten für Benutzer, die sich mit E-Mail-Adresse oder Benutzername registrieren). Azure AD B2C stellt sicher, dass gültige E-Mail-Adressen verwendet werden, indem Kunden diese während des Registrierungsvorgangs verifizieren müssen. Außerdem wird verhindert, dass böswillige Akteure mit automatisierten Prozessen betrügerische Konten in Ihren Anwendungen generieren.

Einige Anwendungsentwickler ziehen es vor, die E-Mail-Überprüfung beim Registrierungsvorgang zu überspringen und diese erst später vom Kunden anzufordern. Um dies zu unterstützen, kann Azure AD B2C so konfiguriert werden, dass die E-Mail-Überprüfung deaktiviert wird. Diese Vorgehensweise sorgt für einen einfacheren Registrierungsvorgang und bietet Entwicklern die Flexibilität, zwischen Kunden zu unterscheiden, die ihre E-Mail-Adresse bereits verifiziert haben, und solchen, die dies noch nicht getan haben.

> [!WARNING]
> Die Deaktivierung der E-Mail-Überprüfung während des Registrierungsvorgangs kann zu Spam führen. Wenn Sie die standardmäßige von Azure AD B2C bereitgestellte E-Mail-Überprüfung deaktivieren, empfiehlt es sich, als Ersatz ein anderes Überprüfungssystem zu implementieren.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]
## <a name="disable-email-verification"></a>Deaktivieren der E-Mail-Überprüfung

::: zone pivot="b2c-user-flow"

Führen Sie die folgenden Schritte aus, um die E-Mail-Überprüfung zu deaktivieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie **Benutzerflows** aus.
1. Wählen Sie den Benutzerflow aus, dessen E-Mail-Überprüfung deaktiviert werden soll,
1. Wählen Sie **Seitenlayouts** aus.
1. Wählen Sie **Registrierungsseite für lokales Konto** aus.
1. Wählen Sie unter **Benutzerattribute** die Option **E-Mail-Adresse** aus.
1. Wählen Sie in der Dropdownliste **Verifizierung erforderlich** die Option **Nein** aus.
1. Wählen Sie **Speichern** aus. Die E-Mail-Überprüfung ist jetzt für diesen Benutzerflow deaktiviert.

::: zone-end

::: zone pivot="b2c-custom-policy"
Das technische Profil **LocalAccountSignUpWithLogonEmail** ist ein [sebstbestätigtes Profil](self-asserted-technical-profile.md), das beim Registrierungsflow aufgerufen wird. Um die E-Mail-Überprüfung zu deaktivieren, stellen Sie die Metadaten `EnforceEmailVerification` auf „false“ ein. Überschreiben Sie die technischen Profile „LocalAccountSignUpWithLogonEmail“ in der Erweiterungsdatei. 

1. Öffnen Sie die Erweiterungsdatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`**</em>.
1. Suchen Sie das Element `ClaimsProviders`. Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Fügen Sie dem `ClaimsProviders`-Element die folgenden Anspruchsanbieter hinzu:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-policy"></a>Testen Ihrer Richtlinie 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie **Benutzerflows** aus.
1. Wählen Sie den Benutzerflow aus, dessen E-Mail-Überprüfung deaktiviert werden soll, beispielsweise *B2C_1_signinsignup*.
1. Um die Richtlinie zu testen, wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *testapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Klicken Sie auf **Benutzerflow ausführen**.
1. Sie sollten sich ohne Überprüfung mit einer E-Mail-Adresse registrieren können.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Aktualisieren und Testen der Datei der vertrauenden Seite

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse + Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse + Abonnements** das Azure AD-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **App-Registrierungen**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Framework für die Identitätsfunktion** aus.
1. Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus, und laden Sie dann die beiden geänderten Richtliniendateien hoch.
1. Wählen Sie die hochgeladene Registrierungs- oder Anmelderichtlinie aus, und klicken Sie auf die Schaltfläche **Jetzt ausführen**.
1. Sie sollten sich ohne Überprüfung mit einer E-Mail-Adresse registrieren können.

::: zone-end


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Anpassen der Benutzeroberfläche in Azure Active Directory B2C](customize-ui-with-html.md).

