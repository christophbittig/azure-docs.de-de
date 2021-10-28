---
title: Einrichten der Registrierung und Anmeldung mit einem Amazon-Konto
titleSuffix: Azure AD B2C
description: Bereitstellen von Registrierung und Anmeldung für Kunden mit Amazon-Konten in Ihren Anwendungen mithilfe von Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.custom: project-no-code
ms.date: 09/16/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c986f76d812c42caeb03ed3548d03552b8ef1fd3
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130228090"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einem Amazon-Konto mithilfe von Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-app-in-the-amazon-developer-console"></a>Erstellen einer App in der Amazon Developer Konsole

Um die Benutzeranmeldung mit einem Amazon-Konto in Azure Active Directory B2C (Azure AD B2C) zu ermöglichen, müssen Sie unter [Amazon Developer Services and Technologies](https://developer.amazon.com) eine Anwendung erstellen. Weitere Informationen finden Sie unter [Register for Login with Amazon](https://developer.amazon.com/docs/login-with-amazon/register-web.html) (Registrieren für die Anmeldung mit Amazon). Wenn Sie noch kein Amazon-Konto haben, können Sie sich auf [https://www.amazon.com/](https://www.amazon.com/) registrieren.

1. Melden Sie sich mit den Anmeldeinformationen für Ihr Amazon-Konto bei der [Amazon Developer Konsole](https://developer.amazon.com/dashboard) an.
1. Falls nicht bereits erfolgt, klicken Sie auf **Sign Up** (Registrieren). Führen Sie die Schritte zur Registrierung von Entwicklern aus, und akzeptieren Sie die Richtlinie.
1. Klicken Sie im Dashboard auf **Login with Amazon** (Anmeldung mit Amazon).
1. Wählen Sie **Create a New Security Profile**.
1. Geben Sie Werte für **Security Profile Name** (Sicherheitsprofilname), **Security Profile Description** (Sicherheitsprofilbeschreibung) und **Consent Privacy Notice URL** (URL zur Zustimmung zum Datenschutzhinweis) an, z. B. `https://www.contoso.com/privacy`. Bei der URL für den Datenschutzhinweis handelt es sich um eine von Ihnen verwaltete Seite, auf der Datenschutzinformationen für Benutzer bereitgestellt werden. Klicken Sie anschließend auf **Speichern**.
1. Wählen Sie im Abschnitt **Login with Amazon Configurations** (Anmeldung mit Amazon-Konfigurationen) den von Ihnen erstellten **Sicherheitsprofilnamen** und das Symbol **Manage** (Verwalten) und dann **Web Settings** (Webeinstellungen) aus.
1. Kopieren Sie im Abschnitt **Web Settings** (Webeinstellungen) den Wert für **Client ID** (Client-ID). Wählen Sie **Show Secret** (Geheimnis anzeigen) aus, um den geheimen Clientschlüssel abzurufen, und kopieren Sie ihn dann. Sie benötigen beide Werte, um in Ihrem Mandanten ein Amazon-Konto als Identitätsanbieter zu konfigurieren. **geheime Clientschlüssel** ist eine wichtige Sicherheitsanmeldeinformation.
1. Klicken Sie im Abschnitt **Web Settings** (Webeinstellungen) auf **Bearbeiten**. 
    1. Geben Sie für **Allowed Origins** (Zulässige Ursprünge) den Wert `https://your-tenant-name.b2clogin.com` ein. Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten. Bei Verwendung einer [benutzerdefinierten Domäne](custom-domain.md) geben Sie `https://your-domain-name` ein.
    1.  Geben Sie für **Allowed Return URLs** (Zulässige Rückgabe-URLs) den Wert `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein.  Bei Verwendung einer [benutzerdefinierten Domäne](custom-domain.md) geben Sie `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein.  Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten und `your-domain-name` durch Ihre benutzerdefinierte Domäne.
1. Wählen Sie **Speichern** aus.

::: zone pivot="b2c-user-flow"

## <a name="configure-amazon-as-an-identity-provider"></a>Konfigurieren von Amazon als Identitätsanbieter

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
1. Wählen Sie **Identitätsanbieter** und dann **Amazon** aus.
1. Geben Sie einen **Namen** ein. Beispiel: *Amazon*.
1. Geben Sie für die **Client-ID** die Client-ID der Amazon-Anwendung ein, die Sie zuvor erstellt haben.
1. Geben Sie für **Geheimer Clientschlüssel** den zuvor notierten geheimen Clientschlüssel ein.
1. Wählen Sie **Speichern** aus.

## <a name="add-amazon-identity-provider-to-a-user-flow"></a>Hinzufügen von Amazon als Identitätsanbieter zu einem Benutzerflow 

Der Amazon-Identitätsanbieter wurde nun eingerichtet, er ist jedoch noch auf keiner der Anmeldeseiten verfügbar. So fügen Sie Amazon als Identitätsanbieter einem Benutzerflow hinzu:

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Klicken Sie auf den Benutzerflow, dem Sie Amazon als Identitätsanbieter hinzufügen möchten.
1. Wählen Sie unter **Soziales Netzwerk als Identitätsanbieter** die Option **Amazon** aus.
1. Wählen Sie **Speichern** aus.
1. Um die Richtlinie zu testen, wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *testapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Benutzerflow ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Amazon** aus, um sich mit dem Amazon-Konto anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen den geheimen Clientschlüssel speichern, den Sie zuvor in Ihrem Azure AD B2C-Mandanten notiert haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Klicken Sie unter **Optionen** auf `Manual`.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `AmazonSecret`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
1. Geben Sie im Feld **Geheimnis** den geheimen Clientschlüssel ein, den Sie zuvor notiert haben.
1. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
1. Klicken Sie auf **Erstellen**.

## <a name="configure-amazon-as-an-identity-provider"></a>Konfigurieren von Amazon als Identitätsanbieter

Um Benutzern die Anmeldung mit einem Amazon-Konto zu ermöglichen, müssen Sie das Konto als Anspruchsanbieter definieren. Diese Azure AD B2C-Instanz kann über einen Endpunkt kommunizieren. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde.

Sie können ein Amazon-Konto als Anspruchsanbieter definieren, indem Sie es in der Erweiterungsdatei Ihrer Richtlinie dem **ClaimsProviders**-Element hinzufügen.


1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAuth2">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
          <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Legen Sie **client_id** auf die Anwendungs-ID aus der Anwendungsregistrierung fest.
5. Speichern Sie die Datei .

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Wählen Sie die Richtliniendatei für die vertrauende Seite aus, z. B. `B2C_1A_signup_signin`.
1. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Amazon** aus, um sich mit dem Amazon-Konto anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

::: zone-end
