---
title: Einrichten der Registrierung und Anmeldung mit einem eBay-Konto
titleSuffix: Azure AD B2C
description: Stellen Sie die Registrierung und Anmeldung für Kunden mit eBay-Konten in Ihren Anwendungen mithilfe von Azure Active Directory B2C bereit.
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
ms.openlocfilehash: ed60e8dc519bc4a23ef3814a76d729e1b875e694
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128574987"
---
# <a name="set-up-sign-up-and-sign-in-with-an-ebay-account-using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einem eBay-Konto mithilfe von Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-ebay-application"></a>Erstellen einer eBay-Anwendung

Wenn Sie die Anmeldung für Benutzer mit einem eBay-Konto in Azure Active Directory B2C (Azure AD B2C) aktivieren möchten, müssen Sie in der [eBay-Entwicklerkonsole](https://developer.ebay.com) eine Anwendung erstellen. Weitere Informationen finden Sie unter [Erstellen eines Entwicklerkontos](https://developer.ebay.com/api-docs/static/creating-edp-account.html). Wenn Sie noch nicht über ein eBay-Entwicklerkonto verfügen, können Sie sich unter [https://developer.ebay.com/signin](https://developer.ebay.com/signin?tab=register) registrieren.

Führen Sie zum Erstellen einer eBay-Anwendung die folgenden Schritte aus:

1. Melden Sie sich mit den Anmeldeinformationen Ihres eBay-Entwicklerkontos bei den [Anwendungsschlüsseln](https://developer.ebay.com/my/keys) der eBay-Entwicklerkonsole an.
1. Geben Sie einen **Anwendungstitel** ein.
1. Wählen Sie unter **Produktion** die Option **Keyset erstellen** aus. 
1. Geben Sie auf der Seite **Primären Kontakt für dieses Konto bestätigen** Ihre Kontodetails an. Wählen Sie zum Abschließen des Registrierungsprozesses die Option **Schlüsselerstellung fortsetzen** aus.
1. Kopieren Sie die Werte von **App-ID (Client-ID)** und **App-ID (Client-ID)** . Sie benötigen beide Angaben, um den Identitätsanbieter Ihrem Mandanten hinzuzufügen.
1. Wählen Sie **Benutzertoken** aus, und wählen Sie dann **Token von eBay über Ihre Anwendung abrufen** aus.
1. Wählen Sie **eBay-Umleitungs-URL hinzufügen** aus.
    1. Geben Sie eine gültige **Datenschutzrichtlinien-URL** (z. B. `https://www.contoso.com/privacy`) ein. Die Richtlinien-URL ist eine von Ihnen verwaltete Seite, die Datenschutzinformationen zu Ihrer Anwendung enthält.
    1. Geben Sie im Feld für die **akzeptierte Authentifizierungs-URL** die Zeichenfolge `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein. Bei Verwendung einer [benutzerdefinierten Domäne](custom-domain.md) geben Sie `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein. Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten und `your-domain-name` durch Ihre benutzerdefinierte Domäne.
1. Wählen Sie **Speichern** aus.

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen den geheimen Clientschlüssel speichern, den Sie zuvor in Ihrem Azure AD B2C-Mandanten notiert haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Klicken Sie unter **Optionen** auf `Manual`.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `eBaySecret`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
1. Geben Sie im Feld **Geheimnis** den geheimen Clientschlüssel ein, den Sie zuvor notiert haben.
1. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
1. Klicken Sie auf **Erstellen**.

## <a name="configure-ebay-as-an-identity-provider"></a>Konfigurieren von eBay als Identitätsanbieter

Damit sich Benutzer mit einem eBay-Konto anmelden können, müssen Sie das Konto als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde.

Sie können ein eBay-Konto als Anspruchsanbieter definieren, indem Sie es in der Erweiterungsdatei Ihrer Richtlinie dem **ClaimsProviders**-Element hinzufügen.

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <!--
    <ClaimsProviders> -->
      <ClaimsProvider>
        <Domain>ebay.com</Domain>
        <DisplayName>eBay</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="eBay-OAUTH2">
            <DisplayName>eBay</DisplayName>
            <Protocol Name="OAuth2" />
            <Metadata>
              <Item Key="ProviderName">ebay.com</Item>
              <Item Key="authorization_endpoint">https://auth.ebay.com/oauth2/authorize</Item>
              <Item Key="AccessTokenEndpoint">https://api.ebay.com/identity/v1/oauth2/token</Item>
              <Item Key="ClaimsEndpoint">https://apiz.ebay.com/commerce/identity/v1/user/</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
              <Item Key="token_endpoint_auth_method">client_secret_basic</Item>
              <Item Key="scope">https://api.ebay.com/oauth/api_scope/commerce.identity.readonly</Item>
              <Item Key="UsePolicyInRedirectUri">0</Item>
              <!-- Update the Client ID below to the Application ID -->
              <Item Key="client_id">Your eBay app ID</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="client_secret" StorageReferenceId="eBaySecret"/>
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="individualAccount.firstName"/>
              <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="individualAccount.lastName"/>
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
              <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="ebay.com" AlwaysUseDefaultValue="true" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            </OutputClaims>
            <OutputClaimsTransformations>
              <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
              <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
              <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            </OutputClaimsTransformations>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!--
    </ClaimsProviders> -->
    ```

4. Legen Sie **client_id** auf die Anwendungs-ID aus der Anwendungsregistrierung fest.
5. Speichern Sie die Datei .


[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="eBayExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="eBayExchange" TechnicalProfileReferenceId="eBay-OAUTH2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Wählen Sie die Richtliniendatei für die vertrauende Seite aus, z. B. `B2C_1A_signup_signin`.
1. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **eBay** aus, um sich mit einem eBay-Konto anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie das [Facebook-Token an Ihre Anwendung übergeben](idp-pass-through-user-flow.md).

::: zone-end
