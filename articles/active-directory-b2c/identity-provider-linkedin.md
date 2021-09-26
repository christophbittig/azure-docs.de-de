---
title: Einrichten der Registrierung und Anmeldung mit einem LinkedIn-Konto
titleSuffix: Azure AD B2C
description: Bereitstellen von Registrierung und Anmeldung für Kunden mit LinkedIn-Konten in Ihren Anwendungen mithilfe von Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 9aa6268d2fc64552ce6a4cb7ed001984cfad80f8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128574534"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einem LinkedIn-Konto mithilfe von Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-linkedin-application"></a>Erstellen einer LinkedIn-Anwendung

Wenn Sie die Anmeldung für Benutzer mit einem LinkedIn-Konto in Azure Active Directory B2C (Azure AD B2C) aktivieren möchten, müssen Sie eine Anwendung auf der [LinkedIn-Entwicklerwebsite](https://www.developer.linkedin.com/) erstellen. Weitere Informationen finden Sie unter [Autorisierungscodeflow](/linkedin/shared/authentication/authorization-code-flow). Wenn Sie noch über kein LinkedIn-Konto verfügen, können Sie sich unter [https://www.linkedin.com/](https://www.linkedin.com/) registrieren.

1. Melden Sie sich auf der [LinkedIn-Entwickler-Website](https://www.developer.linkedin.com/) mit den Anmeldeinformationen für Ihr LinkedIn-Konto an.
1. Wählen Sie **My Apps** (Meine Apps) aus, und klicken Sie dann auf **Create App** (App erstellen).
1. Geben Sie **App-Name**, **LinkedIn-Seite**, **URL der Datenschutzrichtlinie** und **App-Logo** ein.
1. Akzeptieren Sie die **API-Nutzungsbedingungen** von LinkedIn, und klicken Sie auf **Create app** (App erstellen).
1. Wählen Sie die Registerkarte **Authentifizierung** aus. Kopieren Sie unter **Authentication Keys** (Authentifizierungsschlüssel) die Werte für **Client ID** (Client-ID) und **Client Secret** (Geheimer Clientschlüssel). Sie benötigen beide Angaben, um LinkedIn als Identitätsanbieter in Ihrem Mandanten zu konfigurieren. **geheime Clientschlüssel** ist eine wichtige Sicherheitsanmeldeinformation.
1. Wählen Sie neben **Authorized redirect URLs for your app** (Autorisierte Umleitungs-URLs für Ihre App) den Bearbeitungsstift und anschließend **Add redirect URL** (Umleitungs-URL hinzufügen) aus. Geben Sie `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein. Bei Verwendung einer [benutzerdefinierten Domäne](custom-domain.md) geben Sie `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein. Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten und `your-domain-name` durch Ihre benutzerdefinierte Domäne. Bei der Eingabe Ihres Mandantennamens dürfen Sie nur Kleinbuchstaben verwenden, auch wenn der Mandant in Azure AD B2C Großbuchstaben enthält. Wählen Sie **Update** aus.
1. Standardmäßig wird Ihre LinkedIn-App nicht für Bereiche genehmigt, die mit der Anmeldung zusammenhängen. Wählen Sie zum Anfordern einer Überprüfung die Registerkarte **Products** (Produkte) und anschließend **Sign In with LinkedIn** (Mit LinkedIn anmelden) aus. Nach Abschluss der Überprüfung werden Ihrer Anwendung die erforderlichen Bereiche hinzugefügt.
   > [!NOTE]
   > Die derzeit für Ihre App zulässigen Bereiche werden auf der Registerkarte **Auth** (Authentifizierung) im Abschnitt **OAuth 2.0 scopes** (OAuth 2.0-Bereiche) angezeigt.

::: zone pivot="b2c-user-flow"

## <a name="configure-linkedin-as-an-identity-provider"></a>Konfigurieren von LinkedIn als Identitätsanbieter

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
1. Wählen Sie **Identitätsanbieter** und dann **LinkedIn** aus.
1. Geben Sie einen **Namen** ein. Beispiel: *LinkedIn*.
1. Geben Sie für die **Client-ID** die Client-ID der LinkedIn-Anwendung ein, die Sie zuvor erstellt haben.
1. Geben Sie für **Geheimer Clientschlüssel** den zuvor notierten geheimen Clientschlüssel ein.
1. Wählen Sie **Speichern** aus.

## <a name="add-linkedin-identity-provider-to-a-user-flow"></a>Hinzufügen von LinkedIn als Identitätsanbieter zu einem Benutzerflow 

Der LinkedIn-Identitätsanbieter wurde nun eingerichtet, er ist jedoch noch auf keiner der Anmeldeseiten verfügbar. So fügen Sie den LinkedIn-Identitätsanbieter einem Benutzerflow hinzu

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Klicken Sie auf den Benutzerflow, dem Sie LinkedIn als Identitätsanbieter hinzufügen möchten.
1. Wählen Sie unter **Soziales Netzwerk als Identitätsanbieter** die Option **LinkedIn** aus.
1. Wählen Sie **Speichern** aus.
1. Um die Richtlinie zu testen, wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *testapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Benutzerflow ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **LinkedIn** aus, um sich mit dem LinkedIn-Konto anzumelden.

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
1. Wählen Sie **Richtlinienschlüssel** und dann **Hinzufügen** aus.
1. Klicken Sie unter **Optionen** auf `Manual`.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `LinkedInSecret`. Dem Namen Ihres Schlüssels wird automatisch das Präfix *B2C_1A_* hinzugefügt.
1. Geben Sie im Feld **Geheimnis** den zuvor notierten geheimen Clientschlüssel ein.
1. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
1. Klicken Sie auf **Erstellen**.

## <a name="configure-linkedin-as-an-identity-provider"></a>Konfigurieren von LinkedIn als Identitätsanbieter

Um Benutzern zu ermöglichen, sich mit einem LinkedIn-Konto anzumelden, müssen Sie das Konto als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde.

Definieren Sie ein LinkedIn-Konto als Anspruchsanbieter, indem Sie es in der Erweiterungsdatei Ihrer Richtlinie dem Element **ClaimsProviders** hinzufügen.

1. Öffnen Sie im Editor die Datei *SocialAndLocalAccounts/ **TrustFrameworkExtensions.xml*** . Diese Datei ist im [Starter Pack für benutzerdefinierte Richtlinien][starter-pack] enthalten, das Sie im Rahmen der Voraussetzungen heruntergeladen haben.
1. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
1. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAuth2">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Ersetzen Sie den Wert von **client_id** durch die zuvor notierte Client-ID der LinkedIn-Anwendung.
1. Speichern Sie die Datei .

### <a name="add-the-claims-transformations"></a>Hinzufügen von Ansprüchen von Transformationen

Das technische Profil für LinkedIn erfordert das Hinzufügen von Transformationen der Ansprüche **ExtractGivenNameFromLinkedInResponse** und **ExtractSurNameFromLinkedInResponse** zur Liste „ClaimsTransformations“. Wenn in Ihrer Datei kein **ClaimsTransformations**-Element definiert ist, fügen Sie die übergeordneten XML-Elemente wie unten gezeigt hinzu. Für Transformationen von Ansprüchen muss auch ein neuer Anspruchstyp namens **nullStringClaim** definiert werden.

Fügen Sie das Element **BuildingBlocks** im oberen Bereich der Datei *TrustFrameworkExtensions.xml* hinzu. Die Datei *TrustFrameworkBase.xml* kann Ihnen hier als Beispiel dienen.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Wählen Sie die Richtliniendatei für die vertrauende Seite aus, z. B. `B2C_1A_signup_signin`.
1. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **LinkedIn** aus, um sich mit dem LinkedIn-Konto anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

## <a name="migration-from-v10-to-v20"></a>Migration von Version 1.0 zu 2.0

LinkedIn hat kürzlich [seine APIs von Version 1.0 auf 2.0 aktualisiert](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Um Ihre bestehende Konfiguration in die neue Konfiguration zu migrieren, verwenden Sie die Informationen in den folgenden Abschnitten, um die Elemente im technischen Profil zu aktualisieren.

### <a name="replace-items-in-the-metadata"></a>Ersetzen von Elementen in den Metadaten

Ändern Sie im vorhandenen **Metadata**-Element von **TechnicalProfile** die folgenden **Item**-Elemente von:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

in:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Hinzufügen von Elementen zu den Metadaten

Fügen Sie im **Metadata**-Element von **TechnicalProfile** die folgenden **Item**-Elemente hinzu:

```xml
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Ändern von OutputClaim-Elementen

Ändern Sie in den vorhandenen **OutputClaim**-Elementen von **TechnicalProfile** die folgenden **OutputClaim**-Elemente von:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

in:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Hinzufügen neuer OutputClaimsTransformation-Elemente

Ändern Sie in den vorhandenen **OutputClaimsTransformation**-Elementen von **TechnicalProfile** die folgenden **OutputClaimsTransformation**-Elemente:

```xml
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Definieren der neuen Transformationen von Ansprüchen und des Anspruchstyps

Im letzten Schritt haben Sie neue Transformationen von Ansprüchen hinzugefügt, die definiert werden müssen. Um die Transformationen von Ansprüchen zu definieren, fügen Sie sie der Liste **ClaimsTransformations** hinzu. Wenn in Ihrer Datei kein **ClaimsTransformations**-Element definiert ist, fügen Sie die übergeordneten XML-Elemente wie unten gezeigt hinzu. Für Transformationen von Ansprüchen muss auch ein neuer Anspruchstyp namens **nullStringClaim** definiert werden.

Das **BuildingBlocks**-Element muss am Anfang der Datei hinzugefügt werden. Siehe *TrustframeworkBase.xml* als Beispiel.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>Abrufen einer E-Mail-Adresse

Im Rahmen der LinkedIn-Migration von Version 1.0 zu 2.0 ist ein zusätzlicher Aufruf einer weiteren API erforderlich, um die E-Mail-Adresse abzurufen. Wenn Sie die E-Mail-Adresse während der Anmeldung abrufen müssen, gehen Sie wie folgt vor:

1. Führen Sie die vorangehenden Schritte aus, damit Azure AD B2C zum Zweck der Benutzeranmeldung einen Verbund mit LinkedIn herstellen kann. Im Rahmen des Verbunds empfängt Azure AD B2C das Zugriffstoken für LinkedIn.
1. Speichern Sie das LinkedIn-Zugriffstoken in einem Anspruch. [Anweisungen finden Sie hier](idp-pass-through-user-flow.md).
1. Fügen Sie den folgenden Anspruchsanbieter, der die Anforderung ausführt, der `/emailAddress`-API von LinkedIn hinzu. Um diese Anforderung zu autorisieren, benötigen Sie das LinkedIn-Zugriffstoken.

    ```xml
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Fügen Sie Ihrer User Journey den folgenden Orchestrierungsschritt hinzu, damit der API-Anspruchsanbieter ausgelöst wird, wenn sich ein Benutzer über LinkedIn anmeldet. Stellen Sie sicher, dass die Zahl für `Order` entsprechend aktualisiert wird. Fügen Sie diesen Schritt unmittelbar nach dem Orchestrierungsschritt ein, durch den das technische Profil für LinkedIn ausgelöst wird.

    ```xml
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

Das Abrufen der E-Mail-Adresse aus LinkedIn während der Anmeldung ist optional. Wenn Sie die E-Mail nicht von LinkedIn abrufen möchten, aber für die Registrierung eine Adresse benötigen, muss der Benutzer die E-Mail-Adresse manuell eingeben und bestätigen.

Ein vollständiges Beispiel für eine Richtlinie, die den LinkedIn-Identitätsanbieter verwendet, finden Sie unter [Starter Pack für benutzerdefinierte Richtlinien](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="migration-from-v10-to-v20"></a>Migration von Version 1.0 zu 2.0

LinkedIn hat kürzlich [seine APIs von Version 1.0 auf 2.0 aktualisiert](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Im Rahmen der Migration kann Azure AD B2C während der Registrierung nur den vollständigen Namen des LinkedIn-Benutzers abrufen. Wenn eine E-Mail-Adresse zu den Attributen gehört, die bei der Registrierung gesammelt werden, muss der Benutzer die E-Mail-Adresse manuell eingeben und validieren.

::: zone-end