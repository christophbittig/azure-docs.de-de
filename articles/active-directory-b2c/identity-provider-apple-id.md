---
title: Einrichten der Registrierung und Anmeldung mit einer Apple-ID
titleSuffix: Azure AD B2C
description: Stellen Sie für Kunden die Registrierung und Anmeldung mit einer Apple-ID bei Ihren Anwendungen mithilfe von Azure Active Directory B2C bereit.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c3d3fa84e615a60092d0f42acd401be0421bc3f3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448631"
---
# <a name="set-up-sign-up-and-sign-in-with-an-apple-id--using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einer Apple-ID mithilfe von Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-apple-id-application"></a>Erstellen einer Apple-ID-Anwendung

Wenn Sie für Benutzer die Anmeldung mit einer Apple-ID in Azure Active Directory B2C (Azure AD B2C) aktivieren möchten, müssen Sie eine Anwendung in [https://developer.apple.com](https://developer.apple.com) erstellen. Weitere Informationen finden Sie unter [Anmelden mit Apple](https://developer.apple.com/sign-in-with-apple/get-started/). Wenn Sie noch nicht über ein Apple Developer-Konto verfügen, können Sie sich beim [Apple Developer Program](https://developer.apple.com/programs/enroll/) registrieren.

1. Melden Sie sich mit Ihren Kontoanmeldeinformationen beim [Apple Developer-Portal](https://developer.apple.com/account) an.
1. Wählen Sie im Menü die Option **Zertifikate, IDs und Profile** aus, und wählen Sie dann **(+)** aus.
1. Wählen Sie für **Neue ID registrieren** die Option **App-IDs** aus, und wählen Sie dann **Weiter** aus.
1. Wählen Sie für **Typ auswählen** die Option **App** aus, und wählen Sie dann **Weiter** aus.
1. **Eine App-ID registrieren**:
    1. Geben Sie eine **Beschreibung** ein. 
    1. Geben Sie die **Paket-ID** (z. B. `com.contoso.azure-ad-b2c`) ein. 
    1. Wählen Sie für **Funktionen** in der Liste der Funktionen die Option **Mit Apple anmelden** aus. 
    1. Notieren Sie sich Ihre **Team-ID** (App-ID-Präfix) aus diesem Schritt. Sie benötigen die Information später.
    1. Klicken Sie auf **Continue** (Weiter) und dann auf **Registrieren**.
1. Wählen Sie im Menü die Option **Zertifikate, IDs und Profile** aus, und wählen Sie dann **(+)** aus.
1. Wählen Sie für **Neue ID registrieren** die Option **Dienst-IDs** aus, und wählen Sie dann **Weiter** aus.
1. **Eine Dienst-ID registrieren**:
    1. Geben Sie eine **Beschreibung** ein. Die Beschreibung wird dem Benutzer auf dem Zustimmungsbildschirm angezeigt.
    1. Geben Sie die **ID** (z. B. `com.consoto.azure-ad-b2c-service`) ein. Notieren Sie sich Ihre **Dienst-ID**. Bei dieser ID handelt es sich um die **Client-ID** für den OpenID Connect-Flow.
    1. Wählen Sie **Weiter** aus, und wählen Sie dann **Registrieren** aus.
1. Wählen Sie unter **IDs** die von Ihnen erstellte ID aus.
1. Wählen Sie **Mit Apple anmelden** aus, und wählen Sie dann **Konfigurieren** aus.
    1. Wählen Sie die **primäre App-ID** aus, die Sie für die Anmeldung mit Apple konfigurieren möchten.
    1. Geben Sie im Feld **Domänen und Unterdomänen** die Zeichenfolge `your-tenant-name.b2clogin.com` ein. Ersetzen Sie „your-tenant-name“ durch den Namen Ihres Mandanten. Bei Verwendung einer [benutzerdefinierten Domäne](custom-domain.md) geben Sie `https://your-domain-name` ein.
    1. Geben Sie im Feld **Rückgabe-URLs** die Zeichenfolge `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein. Bei Verwendung einer [benutzerdefinierten Domäne](custom-domain.md) geben Sie `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein. Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten und `your-domain-name` durch Ihre benutzerdefinierte Domäne. Die Rückgabe-URL darf nur Kleinbuchstaben enthalten.
    1. Wählen Sie **Weiter** aus, und wählen Sie dann **Fertig** aus.
    1. Wählen Sie nach dem Schließen des Popupfensters die Option **Weiter** aus, und wählen Sie dann **Speichern** aus.

## <a name="creating-an-apple-client-secret"></a>Erstellen eines geheimen Apple-Clientschlüssels

1. Wählen Sie im Menü des Apple Developer-Portals die Option **Schlüssel** aus, und wählen Sie dann **(+)** aus.
1. **Einen neuen Schlüssel registrieren**:
    1. Geben Sie einen **Schlüsselnamen** ein.
    1. Wählen Sie **Mit Apple anmelden** aus, und wählen Sie dann **Konfigurieren** aus.
    1. Wählen Sie für die Option **Primäre App-ID** die zuvor von Ihnen erstellte App aus, und wählen Sie dann **Speichern** aus.
    1. Wählen Sie **Konfigurieren** aus, und wählen Sie dann **Registrieren** aus, um den Schlüsselregistrierungsprozess abzuschließen. Notieren Sie sich die **Schlüssel-ID**. Dieser Schlüssel wird beim Konfigurieren von Benutzerflows benötigt.
1. Wählen Sie zum **Herunterladen Ihres Schlüssels** die Option **Herunterladen** aus, um eine P8-Datei herunterzuladen, die Ihren Schlüssel enthält.


::: zone pivot="b2c-user-flow"

## <a name="configure-apple-as-an-identity-provider"></a>Konfigurieren von Apple als Identitätsanbieter

1. Melden Sie sich als globaler Administrator Ihres Azure AD B2C-Mandanten beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus. Oder verwenden Sie das Suchfeld, um nach **Azure AD B2C** zu suchen und diese Option auszuwählen.
1. Wählen Sie **Identitätsanbieter** und dann **Apple** aus.
1. Geben Sie als **Name** den Wert **Mit Apple anmelden** ein. 
1. Geben Sie die **Apple Developer-ID (Team-ID)** ein.
1. Geben Sie die **Apple-Dienst-ID (Client-ID)** ein.
1. Geben Sie die **Apple-Schlüssel-ID** aus dem Schritt [Erstellen eines geheimen Apple-Clientschlüssels](#creating-an-apple-client-secret) ein.
1. Wählen Sie die **Apple-Zertifikatdaten** aus, und laden Sie diese hoch.
1. Wählen Sie **Speichern** aus.


> [!IMPORTANT] 
> - Zum Anmelden mit Apple muss der Administrator seinen geheimen Clientschlüssel alle 6 Monate erneuern. 
> - Der geheime Apple-Clientschlüssel wird automatisch verlängert, wenn er abläuft. Wenn Sie den geheimen Clientschlüssel manuell erneuern müssen, öffnen Sie Azure AD B2C im Azure-Portal, navigieren Sie zu **Identitätsanbieter** > **Apple**, und wählen Sie **Geheimnis erneuern** aus.
> - Befolgen Sie die Richtlinien zum [Anbieten einer Schaltfläche „Mit Apple anmelden“](#customize-your-user-interface).

## <a name="add-the-apple-identity-provider-to-a-user-flow"></a>Hinzufügen von Apple als Identitätsanbieter zu einem Benutzerflow

Damit sich Benutzer mit einer Apple-ID anmelden können, müssen Sie einem Benutzerflow Apple als Identitätsanbieter hinzufügen. Das Anmelden mit Apple kann nur für die **empfohlene** Version der Benutzerflows konfiguriert werden. Gehen Sie wie folgt vor, um einem Benutzerflow Apple als Identitätsanbieter hinzuzufügen:

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Wählen Sie einen Benutzerflow aus, dem Sie Apple als Identitätsanbieter hinzufügen möchten. 
1. Wählen Sie unter **Soziales Netzwerk als Identitätsanbieter** die Option **Apple** aus.
1. Wählen Sie **Speichern** aus.
1. Um die Richtlinie zu testen, wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *testapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Benutzerflow ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Apple** aus, um sich mit einer Apple-ID anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="signing-the-client-secret"></a>Signieren des geheimen Clientschlüssels

Verwenden Sie die zuvor heruntergeladene P8-Datei, um den geheimen Clientschlüssel in einem JWT-Token zu signieren. Das JWT-Token kann in vielen verfügbaren Bibliotheken erstellt und signiert werden. Verwenden Sie die [Azure-Funktion, die ein Token für Sie erstellt](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-with-apple/azure-function). 

1. Erstellen Sie eine [Azure-Funktion](../azure-functions/functions-create-function-app-portal.md).
1. Wählen Sie unter **Developer** die Option **Programmieren und testen** aus. 
1. Kopieren Sie den Inhalt der Datei [run.csx](https://github.com/azure-ad-b2c/samples/blob/master/policies/sign-in-with-apple/azure-function/run.csx), und fügen Sie ihn in den Editor ein.
1. Wählen Sie **Speichern** aus.
1. Senden Sie eine HTTP `POST`-Anforderung, und geben Sie die folgenden Informationen an:

    - **appleTeamId**: Ihre Apple Developer-Team-ID
    - **appleServiceId**: Die Apple-Dienst-ID (Client-ID)
    - **appleKeyId**: Die zehnstellige Schlüssel-ID, die im JWT-Header gespeichert ist (von Apple benötigt)
    - **p8key**: Der Schlüssel im PEM-Format. Sie können diesen Schlüssel erhalten, indem Sie die P8-Datei in einem Text-Editor öffnen und alles zwischen `-----BEGIN PRIVATE KEY-----` und `-----END PRIVATE KEY-----` ohne Zeilenumbrüche kopieren.
 
Der folgende JSON-Code ist ein Beispiel für einen Aufruf der Azure-Funktion:

```json
{
    "appleTeamId": "ABC123DEFG",
    "appleServiceId": "com.yourcompany.app1",
    "appleKeyId": "URKEYID001",
    "p8key": "MIGTAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBHkwdwIBAQQg+s07NiAcuGEu8rxsJBG7ttupF6FRe3bXdHxEipuyK82gCgYIKoZIzj0DAQehRANCAAQnR1W/KbbaihTQayXH3tuAXA8Aei7u7Ij5OdRy6clOgBeRBPy1miObKYVx3ki1msjjG2uGqRbrc1LvjLHINWRD"
}
```

Die Azure-Funktion antwortet mit einem ordnungsgemäß formatierten und signierten geheimen Clientschlüssel-JWT in einer Antwort. Beispiel:

```json
{
    "token": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDI2OTY3NSwiZXhwIjoxNTYwMzU2MDc1LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.Dt9qA9NmJ_mk6tOqbsuTmfBrQLFqc9BnSVKR6A-bf9TcTft2XmhWaVODr7Q9w1PP3QOYShFXAnNql5OdNebB4g"
}
```

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen den geheimen Clientschlüssel speichern, den Sie zuvor in Ihrem Azure AD B2C-Mandanten notiert haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus. Oder verwenden Sie das Suchfeld, um nach **Azure AD B2C** zu suchen und diese Option auszuwählen.
1. Wählen Sie auf der Seite **Übersicht** die Option **Identity Experience Framework** aus.
1. Wählen Sie **Richtlinienschlüssel** aus, und wählen Sie dann **Hinzufügen** aus.
1. Wählen Sie unter **Optionen** die Option **Manuell** aus.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispielsweise „AppleSecret“. Dem Namen Ihres Schlüssels wird automatisch das Präfix „B2C_1A_“ hinzugefügt.
1. Geben Sie im Feld **Geheimnis** den Wert eines von der Azure-Funktion zurückgegebenen Tokens (ein JWT-Token) ein.
1. Wählen Sie unter **Schlüsselverwendung** **Signatur** aus.
1. Klicken Sie auf **Erstellen**.

> [!IMPORTANT] 
> - Zum Anmelden mit Apple muss der Administrator seinen geheimen Clientschlüssel alle 6 Monate erneuern.
> - Sie müssen den geheimen Apple-Clientschlüssel manuell erneuern, wenn er abläuft, und den neuen Wert im Richtlinienschlüssel speichern.
> - Wir empfehlen Ihnen, eine eigene Erinnerung innerhalb von 6 Monaten festzulegen, damit Sie rechtzeitig einen neuen geheimen Clientschlüssel generieren können. 
> - Befolgen Sie die Richtlinien zum [Anbieten einer Schaltfläche „Mit Apple anmelden“](#customize-your-user-interface).

## <a name="configure-apple-as-an-identity-provider"></a>Konfigurieren von Apple als Identitätsanbieter

Damit sich Benutzer mit einer Apple-ID anmelden können, müssen Sie das Konto als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde.

Sie können eine Apple-ID als Anspruchsanbieter definieren, indem Sie in der Erweiterungsdatei Ihrer Richtlinie dem **ClaimsProviders**-Element die Apple-ID hinzufügen.

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>apple.com</Domain>
      <DisplayName>Apple</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Apple-OIDC">
          <DisplayName>Sign in with Apple</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">apple</Item>
            <Item Key="authorization_endpoint">https://appleid.apple.com/auth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://appleid.apple.com/auth/token</Item>
            <Item Key="JWKS">https://appleid.apple.com/auth/keys</Item>
            <Item Key="issuer">https://appleid.apple.com</Item>
            <Item Key="scope">name email openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="response_types">code</Item>
            <Item Key="external_user_identity_claim_id">sub</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="ReadBodyClaimsOnIdpRedirect">user.name.firstName user.name.lastName user.email</Item>
            <Item Key="client_id">You Apple ID</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AppleSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="https://appleid.apple.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="user.name.firstName"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="user.name.lastName"/>
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Legen Sie **client_id** auf die Dienst-ID fest. Beispiel: `com.consoto.azure-ad-b2c-service`.
5. Speichern Sie die Datei .

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AppleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AppleExchange" TechnicalProfileReferenceId="Apple-OIDC" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Wählen Sie die Richtliniendatei für die vertrauende Seite aus, z. B. `B2C_1A_signup_signin`.
1. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Apple** aus, um sich mit einer Apple-ID anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

::: zone-end

## <a name="customize-your-user-interface"></a>Anpassen der Benutzeroberfläche

Befolgen Sie die Richtlinien zum [Anbieten einer Anmeldung mit Apple](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/introduction/). Apple stellt verschiedene Schaltflächen **Mit Apple anmelden** bereit, die Sie verwenden können, damit Benutzer ein Konto einrichten und sich anmelden können. Erstellen Sie bei Bedarf eine benutzerdefinierte Schaltfläche, um eine Anmeldung mit Apple zu ermöglichen. Erfahren Sie mehr über das [Anzeigen einer Schaltfläche „Mit Apple anmelden“](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/buttons/).

So sorgen Sie für die Einhaltung der Richtlinien für Apple-Benutzeroberflächen:

- [Anpassen der Benutzeroberfläche mit HTML-Vorlagen](customize-ui-with-html.md)
- [Lokalisieren](language-customization.md) des Identitätsanbieternamens

