---
title: Einrichten der Registrierung und Anmeldung mit einem Facebook-Konto
titleSuffix: Azure AD B2C
description: Bereitstellen von Registrierung und Anmeldung für Kunden mit Facebook-Konten in Ihren Anwendungen mithilfe von Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d9fb37bd05c6cddf1509d40433783692864e588d
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137837"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einem Facebook-Konto mithilfe von Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-facebook-application"></a>Erstellen einer Facebook-Anwendung

Wenn Sie die Anmeldung für Benutzer mit einem Facebook-Konto in Azure Active Directory B2C (Azure AD B2C) aktivieren möchten, müssen Sie eine Anwendung im [Facebook-App-Dashboard](https://developers.facebook.com/) erstellen. Weitere Informationen finden Sie unter [App-Entwicklung](https://developers.facebook.com/docs/development).

Wenn Sie noch nicht über ein Facebook-Konto verfügen, können Sie sich unter [https://www.facebook.com](https://www.facebook.com) registrieren. Starten Sie nach der Registrierung oder Anmeldung mit Ihrem Facebook-Konto den [Registrierungsvorgang für Facebook-Entwicklerkonten](https://developers.facebook.com/async/registration). Weitere Informationen finden Sie unter [Register as a Facebook Developer](https://developers.facebook.com/docs/development/register) (Registrieren als Facebook-Entwickler).

1. Melden Sie sich auf der [Facebook-Entwicklerwebsite](https://developers.facebook.com/apps) mit den Anmeldeinformationen für Ihr Facebook-Entwicklerkonto an.
1. Wählen Sie **App erstellen** aus.
1. Wählen Sie unter **App-Typ auswählen** die Option **Consumer** und dann **Weiter** aus.
1. Geben Sie einen App-Anzeigenamen (**App Display Name**) und eine gültige Kontakt-E-Mail-Adresse für die App (**App Contact Email**) an.
1. Wählen Sie **App erstellen** aus. Für diesen Schritt müssen Sie möglicherweise die Richtlinien für die Facebook-Plattform akzeptieren und eine Online-Sicherheitsüberprüfung durchführen.
1. Wählen Sie **Settings** > **Basic** (Einstellungen > Allgemeines) aus.
    1. Kopieren Sie den Wert unter **App ID**.
    1. Wählen Sie **Show** (Anzeigen) aus, und kopieren Sie den Wert unter **App Secret** (App-Geheimnis). Sie benötigen beide Angaben, um Facebook als Identitätsanbieter in Ihrem Mandanten zu konfigurieren. **App Secret** handelt es sich um eine wichtige Sicherheitsinformation für die Anmeldung.
    1. Geben Sie unter **Privacy Policy URL** (Datenrichtlinien-URL) eine URL ein, z.B. `https://www.contoso.com/privacy`. Die Richtlinien-URL ist eine von Ihnen verwaltete Seite, die Datenschutzinformationen zu Ihrer Anwendung enthält.
    1. Geben Sie eine URL für **Terms of Service URL** (URL der Nutzungsbedingungen) ein, z. B. `https://www.contoso.com/tos`. Die Richtlinien-URL ist eine von Ihnen verwaltete Seite, die Nutzungsbedingungen Ihrer Anwendung enthält.
    1. Geben Sie eine URL für das **Löschen von Benutzerdaten** ein, z. B. `https://www.contoso.com/delete_my_data`. Die URL zum Löschen von Benutzerdaten ist eine Seite, die Sie verwalten, um Benutzern die Möglichkeit zu geben, ihre Daten löschen zu lassen. 
    1. Wählen Sie eine **Kategorie**, z.B. `Business and Pages`. Dieser Wert ist für Facebook erforderlich, wird aber nicht für Azure AD B2C verwendet.
1. Wählen Sie unten auf der Seite die Option **Add Platform** (Plattform hinzufügen) und dann **Website** aus.
1. Geben Sie im Feld **Website-URL** die Adresse Ihrer Website (z. B. `https://contoso.com`) ein. 
1. Klicken Sie auf **Save changes** (Änderungen speichern).
1. Wählen Sie im Menü das **Pluszeichen** oder den Link **Produkt hinzufügen** neben **PRODUKTE** aus. Wählen Sie unter **Add Products to Your App** (Produkte Ihrer App hinzufügen) unter **Facebook Login** (Facebook-Anmeldung) **Set up** (Einrichten) aus.
1. Wählen Sie im Menü **Facebook Login** (Facebook-Anmeldung) und dann **Settings** (Einstellungen) aus.
1. Geben Sie `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` unter **Valid OAuth redirect URIs** (Gültige OAuth-Umleitungs-URIs) ein. Bei Verwendung einer [benutzerdefinierten Domäne](custom-domain.md) geben Sie `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein. Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten und `your-domain-name` durch Ihre benutzerdefinierte Domäne. 
1. Wählen Sie unten auf der Seite die Option **Save Changes** (Änderungen speichern) aus.
1. Klicken Sie rechts oben auf der Seite auf den Statusselektor, und legen Sie ihn auf **On** (Ein) fest, um die Anwendung öffentlich und Ihre Facebook-Anwendung für Azure AD B2C verfügbar zu machen. Wählen Sie anschließend **Switch Mode** (Modus wechseln) aus. An diesem Punkt sollte sich der Status von **Entwicklung** in **Live** ändern. Weitere Informationen finden Sie unter [Facebook App Development](https://developers.facebook.com/docs/development/release) (App-Entwicklung mit Facebook).

::: zone pivot="b2c-user-flow"

## <a name="configure-facebook-as-an-identity-provider"></a>Konfigurieren von Facebook als Identitätsanbieter

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
1. Wählen Sie **Identitätsanbieter** und dann **Facebook** aus.
1. Geben Sie einen **Namen** ein. Beispiel: *Facebook*.
1. Geben Sie für die **Client-ID** die App-ID der Facebook-Anwendung ein, die Sie zuvor erstellt haben.
1. Geben Sie das zuvor notierte App-Geheimnis als **Geheimer Clientschlüssel** ein.
1. Wählen Sie **Speichern** aus.

## <a name="add-facebook-identity-provider-to-a-user-flow"></a>Hinzufügen von Facebook als Identitätsanbieter zu einem Benutzerflow 

Der Facebook-Identitätsanbieter wurde nun eingerichtet, er ist jedoch noch auf keiner der Anmeldeseiten verfügbar. So fügen Sie den Facebook-Identitätsanbieter einem Benutzerflow hinzu

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Klicken Sie auf den Benutzerflow, dem Sie Facebook als Identitätsanbieter hinzufügen möchten.
1. Wählen Sie unter **Soziales Netzwerk als Identitätsanbieter** die Option **Facebook** aus.
1. Wählen Sie **Speichern** aus.
1. Um die Richtlinie zu testen, wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *testapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Benutzerflow ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Facebook** aus, um sich mit dem Facebook-Konto anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen das zuvor notierte App-Geheimnis in Ihrem Azure AD B2C-Mandanten speichern.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Klicken Sie unter **Optionen** auf `Manual`.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `FacebookSecret`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
1. Geben Sie im Feld **Geheimnis** das zuvor notierte App-Geheimnis ein.
1. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
1. Klicken Sie auf **Erstellen**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurieren eines Facebook-Kontos als Identitätsanbieter

1. Ersetzen Sie in der Datei `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** den Wert `client_id` durch die ID der Facebook-Anwendung:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

## <a name="upload-and-test-the-policy"></a>Hochladen und Testen der Richtlinie

Aktualisieren Sie als Nächstes die Datei der vertrauenden Seite, mit der die erstellte User Journey initiiert wird.

1. Laden Sie die Datei *TrustFrameworkExtensions.xml* in Ihren Mandanten hoch.
1. Wählen Sie unter **Benutzerdefinierte Richtlinien** die Richtlinie **B2C_1A_signup_signin** aus.
1. Wählen Sie für **Anwendung auswählen** die Webanwendung *testapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Facebook** aus, um sich mit dem Facebook-Konto anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie das [Facebook-Token an Ihre Anwendung übergeben](idp-pass-through-user-flow.md).
