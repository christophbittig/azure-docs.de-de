---
title: UserInfo-Endpunkt | Microsoft-Dokumentation
description: Definieren eines UserInfo-Endpunkts in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/20/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 209d2aafbecc81a3c06866569f66edec6e3f43a4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128566575"
---
# <a name="userinfo-endpoint"></a>UserInfo-Endpunkt

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Der UserInfo-Endpunkt ist Teil der Spezifikation des [OpenID Connect-Standards](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC) und für die Rückgabe von Ansprüchen über den authentifizierenden Benutzer konzipiert. Der UserInfo-Endpunkt wird in der Richtlinie der vertrauenden Seite mithilfe des Elements [EndPoint](relyingparty.md#endpoints) definiert.  

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="userinfo-endpoint-overview"></a>Übersicht über den UserInfo-Endpunkt

Die Benutzerinformationen „UserJourney“ geben Folgendes an:

- **Autorisierung:** Der UserInfo-Endpunkt wird durch ein Bearertoken geschützt. Ein ausgestelltes Zugriffstoken wird dem UserInfo-Endpunkt im Autorisierungsheader präsentiert. Die Richtlinie gibt das technische Profil an, das das eingehende Token überprüft und Ansprüche extrahiert, z. B. die Objekt-ID (objectId) des Benutzers. Die Objekt-ID des Benutzers wird zum Abrufen der Ansprüche verwendet, die in der Antwort der UserInfo-Endpunktjourney zurückgegeben werden sollen. 
- **Orchestrierungsschritt:** 
  - Ein Orchestrierungsschritt wird zum Erfassen von Informationen zum Benutzer verwendet. Basierend auf den Ansprüchen innerhalb des eingehenden Zugriffstokens ruft die User Journey ein [technisches Azure Active Directory-Profil](active-directory-technical-profile.md) auf, um Daten zum Benutzer abzurufen (etwa Lesen des Benutzers nach objectId). 
  - **Optionale Orchestrierungsschritte:** Sie können weitere Orchestrierungsschritte hinzufügen, etwa ein technisches REST-API-Profil, um weitere Informationen zum Benutzer abzurufen. 
  - **UserInfo-Aussteller:** Gibt die Liste der Ansprüche an, die vom UserInfo-Endpunkt zurückgegeben werden.

## <a name="create-a-userinfo-endpoint"></a>Erstellen eines UserInfo-Endpunkts

### <a name="1-add-the-token-issuer-technical-profile"></a>1. Hinzufügen des technischen Profils für Tokenaussteller

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
1. Falls es nicht bereits vorhanden ist, fügen Sie ein ClaimsProvider-Element und seine untergeordneten Elemente als erstes Element unter dem BuildingBlocks-Element hinzu.
1. Fügen Sie den folgenden Anspruchsanbieter hinzu:

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Token Issuer</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="UserInfoIssuer">
            <DisplayName>JSON Issuer</DisplayName>
            <Protocol Name="None" />
            <OutputTokenFormat>JSON</OutputTokenFormat>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <!-- The Below claims are what will be returned on the UserInfo Endpoint if in the Claims Bag-->
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId"/>
              <InputClaim ClaimTypeReferenceId="givenName"/>
              <InputClaim ClaimTypeReferenceId="surname"/>
              <InputClaim ClaimTypeReferenceId="displayName"/>
              <InputClaim ClaimTypeReferenceId="signInNames.emailAddress"/>
            </InputClaims>
          </TechnicalProfile>
          <TechnicalProfile Id="UserInfoAuthorization">
            <DisplayName>UserInfo authorization</DisplayName>
            <Protocol Name="None" />
            <InputTokenFormat>JWT</InputTokenFormat>
            <Metadata>
              <!-- Update the Issuer and Audience below -->
              <!-- Audience is optional, Issuer is required-->
              <Item Key="issuer">https://yourtenant.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/</Item>
              <Item Key="audience">[ "22222222-2222-2222-2222-222222222222", "33333333-3333-3333-3333-333333333333" ]</Item>
              <Item Key="client_assertion_type">urn:ietf:params:oauth:client-assertion-type:jwt-bearer</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
              <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" PartnerClaimType="email"/>
              <!-- Optional claims to read from the access token. -->
              <!-- <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
                 <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
                 <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/> -->
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ``` 

1. Der Abschnitt „InputClaims“ im technischen Profil **UserInfoIssuer** gibt die Attribute an, die Sie zurückgeben möchten. Das technische Profil „UserInfoIssuer“ wird am Ende der User Journey aufgerufen. 
1. Das technische Profil **UserInfoAuthorization** überprüft die Signatur, den Ausstellernamen und die Tokenzielgruppe und extrahiert den Anspruch aus dem eingehenden Token. Ändern Sie die folgenden Metadaten entsprechend Ihrer Umgebung:
    1. **issuer**: Dieser Wert muss mit dem `iss`-Anspruch innerhalb des Zugriffstokenanspruchs identisch sein. Von Azure AD B2C ausgestellte Token verwenden einen Aussteller im Format `https://yourtenant.b2clogin.com/your-tenant-id/v2.0/`. Informieren Sie sich ausführlicher über die [Tokenanpassung](configure-tokens.md).
    1. **IdTokenAudience**: Dieser Wert muss mit dem `aud`-Anspruch innerhalb des Zugriffstokenanspruchs identisch sein. In Azure AD B2C ist der `aud`-Anspruch die ID der anspruchsbasierten Anwendung. Dieser Wert ist eine Auflistung und unterstützt mehrere Werte mit Kommatrennzeichen.

        Im folgenden Zugriffstoken lautet der Wert des `iss`-Anspruchs `https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/`. Der Wert des `aud`-Anspruchs lautet `22222222-2222-2222-2222-222222222222`.

        ```json
        {
          "exp": 1605549468,
          "nbf": 1605545868,
          "ver": "1.0",
          "iss": "https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/",
          "sub": "44444444-4444-4444-4444-444444444444",
          "aud": "22222222-2222-2222-2222-222222222222",
          "acr": "b2c_1a_signup_signin",
          "nonce": "defaultNonce",
          "iat": 1605545868,
          "auth_time": 1605545868,
          "name": "John Smith",
          "given_name": "John",
          "family_name": "Smith",
          "tid": "11111111-1111-1111-1111-111111111111"
        }
        ```
    
1.  Das Element „OutputClaims“ des technischen Profils **UserInfoAuthorization** gibt die Attribute an, die Sie aus dem Zugriffstoken lesen möchten. **ClaimTypeReferenceId** ist der Verweis auf einen Anspruchstyp. **PartnerClaimType** (optional) ist der Name des im Zugriffstoken definierten Anspruchs.



### <a name="2-add-the-userjourney-element"></a>2. Hinzufügen des UserJourney-Elements 

Das [UserJourney](userjourneys.md)-Element definiert den Pfad, den der Benutzer beim Interagieren mit Ihrer Anwendung durchläuft. Fügen Sie das **UserJourneys**-Element hinzu (sofern es noch nicht vorhanden ist), und identifizieren Sie dabei die **UserJourney** als `UserInfoJourney`:

```xml
<!-- 
<UserJourneys> -->
  <UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
    <Authorization>
      <AuthorizationTechnicalProfiles>
        <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
      </AuthorizationTechnicalProfiles>
    </Authorization>
    <OrchestrationSteps >
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>objectId</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges UserIdentity="false">
          <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="UserInfoIssuer" />
    </OrchestrationSteps>
  </UserJourney>
<!-- 
</UserJourneys> -->
```

### <a name="3-include-the-endpoint-to-the-relying-party-policy"></a>3. Aufnehmen des Endpunkts in die Richtlinie der vertrauenden Seite

Fügen Sie der Datei *SocialAndLocalAccounts/SignUpOrSignIn.xml* ein Element vom Typ [Endpoint](relyingparty.md#endpoints) hinzu, um den UserInfo-Endpunkt in die anspruchsbasierte Anwendung aufzunehmen. 

```xml
<!--
<RelyingParty> -->
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
<!-- 
</RelyingParty> -->
```

Das fertige Element der vertrauenden Seite sieht wie folgt aus:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="yourtenant.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <Endpoints>
      <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
    </Endpoints>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="4-upload-the-files"></a>4. Hochladen der Dateien

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Framework für die Identitätsfunktion** aus.
1. Klicken Sie auf der Seite **Benutzerdefinierte Richtlinien** auf **Upload custom policy** (Benutzerdefinierte Richtlinie hochladen).
1. Aktivieren Sie **Benutzerdefinierte Richtlinie überschreiben, sofern bereits vorhanden**, suchen Sie nach der Datei *TrustframeworkExtensions.xml*, und wählen Sie die Datei aus.
1. Klicken Sie auf **Hochladen**.
1. Wiederholen Sie die Schritte 5 bis 7 für die Datei der vertrauenden Seite, z.B. *SignUpOrSignIn.xml*.

## <a name="calling-the-userinfo-endpoint"></a>Aufrufen des UserInfo-Endpunkts

Der UserInfo-Endpunkt verwendet die standardmäßige OAuth2-Bearertoken-API, die mithilfe des Zugriffstokens aufgerufen wird, das beim Abrufen eines Tokens für die Anwendung empfangen wurde. Sie gibt eine JSON-Antwort zurück, die Ansprüche über den Benutzer enthält. Der UserInfo-Endpunkt wird in Azure AD B2C unter folgender URL gehostet:

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/openid/v2.0/userinfo
```

Mit dem Konfigurationsendpunkt „/.well-known“ (OpenID Connect Discovery-Dokument) wird das Feld `userinfo_endpoint` aufgeführt. Sie können den UserInfo-Endpunkt mithilfe des Konfigurationsendpunkts „/.well-known“ unter folgender URL programmgesteuert ermitteln: 

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/v2.0/.well-known/openid-configuration 
```

### <a name="test-the-policy"></a>Testen der Richtlinie

1. Wählen Sie unter **Benutzerdefinierte Richtlinien** die Richtlinie aus, mit der Sie den UserInfo-Endpunkt integriert haben. Beispiel: *B2C_1A_SignUpOrSignIn*
1. Wählen Sie **Jetzt ausführen** aus. 
1. Wählen Sie unter **Anwendung auswählen** Ihre Anwendung aus, die Sie zuvor registriert haben. Wählen Sie für **Antwort-URL** die Option `https://jwt.ms` aus. Weitere Informationen finden Sie unter [Tutorial: Registrieren einer Webanwendung in Azure Active Directory B2C](tutorial-register-applications.md).
1. Sie können sich mit einer E-Mail-Adresse oder einem Social Media-Konto registrieren oder anmelden.
1. Kopieren Sie auf der Website [https://jwt.ms](https://jwt.ms) „id_token“ im codierten Format. Damit können Sie eine GET-Anforderung an den UserInfo-Endpunkt senden und die Benutzerinformationen abrufen.
1. Senden Sie eine GET-Anforderung an den UserInfo-Endpunkt, und rufen Sie die Benutzerinformationen ab.

```http
GET /yourtenant.onmicrosoft.com/b2c_1a_signup_signin/openid/v2.0/userinfo
Host: b2cninja.b2clogin.com
Authorization: Bearer <your access token>
```

Eine erfolgreiche Antwort sieht wie folgt aus:

```json
{
    "objectId": "44444444-4444-4444-4444-444444444444",
    "givenName": "John",
    "surname": "Smith",
    "displayName": "John Smith",
    "signInNames.emailAddress": "john.s@contoso.com"
}
```

## <a name="provide-optional-claims"></a>Bereitstellen optionaler Ansprüche

Führen Sie die folgenden Schritte aus, um weitere Ansprüche bereitzustellen:

1. [Fügen Sie Benutzerattribute hinzu und passen Sie die Benutzereingabe an](configure-user-input.md).
1. Ändern Sie das OutputClaims-Element des [technischen Profils der Richtlinie für die vertrauende Seite](relyingparty.md#technicalprofile) mit den Ansprüchen, die Sie bereitstellen möchten. Verwenden Sie das `DefaultValue`-Attribut, um einen Standardwert festzulegen. Sie können den Standardwert auch auf einen [Anspruchskonfliktlöser](claim-resolver-overview.md) wie `{Context:CorrelationId}` festlegen. Um die Verwendung des Standardwerts zu erzwingen, setzen Sie das `AlwaysUseDefaultValue`-Attribut auf `true`. Das folgende Beispiel fügt den Anspruch „city“ mit einem Standardwert hinzu.
    
    ```xml
    <RelyingParty>
      ...
      <TechnicalProfile Id="PolicyProfile">
        ...
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="city" DefaultValue="Berlin" />
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```
  
1. Ändern Sie das InputClaims-Element des technischen Profils von UserInfoIssuer mit den Ansprüchen, die Sie bereitstellen möchten. Verwenden Sie das `PartnerClaimType`-Attribut, um den Namen der Anspruchsrückgabe an Ihre App zu ändern. Im folgenden Beispiel wird der Anspruch „city“ hinzugefügt und der Name einiger Ansprüche geändert.

    ```xml
    <TechnicalProfile Id="UserInfoIssuer">
      ...
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="city" />
        <InputClaim ClaimTypeReferenceId="givenName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="familyName" />
        <InputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <InputClaim ClaimTypeReferenceId="signInNames.emailAddress" PartnerClaimType="email" />
      </InputClaims>
      ...
    </TechnicalProfile>
    ```

## <a name="next-steps"></a>Nächste Schritte

- Ein Beispiel für eine Richtlinie für den UserInfo-Endpunkt finden Sie auf [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/user-info-endpoint).

::: zone-end
