---
title: Einrichten der Registrierung und Anmeldung mit einem ID.me-Konto
titleSuffix: Azure AD B2C
description: Bereitstellen von Registrierung und Anmeldung für Kunden mit ID.me-Konten in Ihren Anwendungen mithilfe von Azure Active Directory B2C.
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
ms.openlocfilehash: e0fd582088b2c73565b7b5aab44dbe827e792e90
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128574512"
---
# <a name="set-up-sign-up-and-sign-in-with-a-idme-account-using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einem ID.me-Konto mithilfe von Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]


## <a name="create-an-idme-application"></a>Erstellen eines ID.me-Kontos

Wenn Sie die Anmeldung für Benutzer mit einem ID.me-Konto in Azure Active Directory B2C (Azure AD B2C) aktivieren möchten, müssen Sie in den [ID.me-Entwicklerressourcen für API und SDK](https://developers.id.me/) eine Anwendung erstellen. Weitere Informationen finden Sie im [OAuth-Integrationsleitfaden](https://developers.id.me/documentation/oauth/overview/kyc). Wenn Sie noch kein ID.me-Entwicklerkonto haben, können Sie sich auf [https://developers.id.me/registration/new](https://developers.id.me/registration/new) registrieren.

1. Melden Sie sich mit den Anmeldeinformationen Ihres ID.me-Kontos bei den [ID.me-Entwicklerressourcen für API und SDK](https://developers.id.me/) an.
1. Wählen Sie **Meine Anwendungen anzeigen** und dann **Weiter** aus.
1. Wählen Sie **Neu erstellen** aus.
    1. Geben Sie einen **Namen** und einen **Anzeigenamen** ein.
    1. Geben Sie im Feld **Umleitungs-URI** die Zeichenfolge `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein. Bei Verwendung einer [benutzerdefinierten Domäne](custom-domain.md) geben Sie `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein. Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten und `your-domain-name` durch Ihre benutzerdefinierte Domäne. 
1. Klicken Sie auf **Continue**(Weiter).
1. Kopieren Sie die Werte für **Client ID** und **Client Secret**. Sie benötigen beide Angaben, um den Identitätsanbieter Ihrem Mandanten hinzuzufügen.

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen den geheimen Clientschlüssel speichern, den Sie zuvor in Ihrem Azure AD B2C-Mandanten notiert haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Klicken Sie unter **Optionen** auf `Manual`.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `IdMeSecret`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
1. Geben Sie im Feld **Geheimnis** den geheimen Clientschlüssel ein, den Sie zuvor notiert haben.
1. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
1. Klicken Sie auf **Erstellen**.

## <a name="configure-idme-as-an-identity-provider"></a>Konfigurieren von ID.me als Identitätsanbieter

Damit sich Benutzer mit einem ID.me-Konto anmelden können, müssen Sie das Konto als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde.

Sie können ein ID.me-Konto als Anspruchsanbieter definieren, indem Sie es in der Erweiterungsdatei Ihrer Richtlinie dem **ClaimsProviders**-Element hinzufügen.

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>id.me</Domain>
      <DisplayName>ID.me</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="IdMe-OAuth2">
          <DisplayName>IdMe</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">api.id.me</Item>
            <Item Key="authorization_endpoint">https://api.id.me/oauth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://api.id.me/oauth/token</Item>
            <Item Key="ClaimsEndpoint">https://api.id.me/api/public/v2/attributes.json</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="scope">openid alumni</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your ID.me application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_IdMeSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="uuid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="fname" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lname" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="me.id.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateDisplayNameFromFirstNameAndLastName" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Legen Sie **client_id** auf die Anwendungs-ID aus der Anwendungsregistrierung fest.
5. Speichern Sie die Datei .

### <a name="add-the-claims-transformations"></a>Hinzufügen von Ansprüchen von Transformationen

Als Nächstes benötigen zum Erstellen des displayName-Anspruchs eine Anspruchstransformation. Fügen Sie im `<ClaimsTransformations>`-Element in `<BuildingBlocks>` die folgenden Anspruchstransformation hinzu. 

```xml
 <ClaimsTransformations>
  <ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
    </OutputClaims>
   </ClaimsTransformation>
</ClaimsTransformations>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="IdMeExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="IdMeExchange" TechnicalProfileReferenceId="IdMe-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Wählen Sie die Richtliniendatei für die vertrauende Seite aus, z. B. `B2C_1A_signup_signin`.
1. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **ID.me** aus, um sich mit dem ID.me-Konto anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

::: zone-end