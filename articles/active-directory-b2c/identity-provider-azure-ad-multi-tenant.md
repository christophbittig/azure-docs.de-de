---
title: Einrichten der Anmeldung für mehrinstanzenfähiges Azure AD durch benutzerdefinierte Richtlinien
titleSuffix: Azure AD B2C
description: Hinzufügen eines mehrinstanzenfähigen Azure AD-Identitätsanbieters mithilfe von benutzerdefinierten Richtlinien in Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 553608a5574edaf904e9c9ac0986a3d0f8af9278
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227960"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Einrichten der Anmeldung für einen mehrinstanzenfähigen Azure Active Directory-Identitätsanbieter mithilfe von benutzerdefinierten Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

In diesem Artikel wird erläutert, wie Sie die Anmeldung für Benutzer ermöglichen, die den mehrinstanzenfähigen Endpunkt für Azure Active Directory (Azure AD) verwenden. Dadurch können sich Benutzer aus mehreren Azure AD-Mandanten mit Azure AD B2C anmelden, ohne dass Sie einen Identitätsanbieter für jeden Mandanten konfigurieren müssen. Allerdings können sich Gastmitglieder in diesen Mandanten **nicht** anmelden. Dazu müssen Sie [jeden Mandanten einzeln konfigurieren](identity-provider-azure-ad-single-tenant.md).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

> [!NOTE]
> In diesem Artikel wird vorausgesetzt, dass im vorherigen, unter Voraussetzungen erwähnten Schritt der Starter Pack **SocialAndLocalAccounts** verwendet wird.  

## <a name="register-an-azure-ad-app"></a>Registrieren einer Azure AD-App

Wenn Sie die Anmeldung für Benutzer mit einem Azure AD-Konto in Azure Active Directory B2C (Azure AD B2C) aktivieren möchten, müssen Sie eine Anwendung im [Azure-Portal](https://portal.azure.com) erstellen. Weitere Informationen finden Sie unter [Registrieren einer Anwendung bei Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD-Mandanten Ihrer Organisation (z. B. Contoso) enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse + Abonnements** das Azure AD-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **App-Registrierungen**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Neue Registrierung** aus.
1. Geben Sie einen **Namen** für Ihre Anwendung ein. Beispiel: `Azure AD B2C App`.
1. Wählen Sie für diese Anwendung die Option **Konten in einem beliebigen Organisationsverzeichnis (beliebiges Azure AD-Verzeichnis – mehrinstanzenfähig)** aus.
1. Übernehmen Sie den Wert **Web** als **Umleitungs-URI**, geben Sie die folgende URL in Kleinbuchstaben ein, und ersetzen Sie dabei `your-B2C-tenant-name` durch den Namen Ihres Azure AD B2C-Mandanten.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Beispiel: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

    Bei Verwendung einer [benutzerdefinierten Domäne](custom-domain.md) geben Sie `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein. Ersetzen Sie `your-domain-name` durch Ihre benutzerdefinierte Domäne und `your-tenant-name` durch den Namen Ihres Mandanten.

1. Wählen Sie **Registrieren**. Notieren Sie sich die **Anwendungs-ID (Client)** zur Verwendung in einem späteren Schritt.
1. Wählen Sie **Zertifikate & Geheimnisse** und dann **Neuer geheimer Clientschlüssel** aus.
1. Geben Sie eine **Beschreibung** für das Geheimnis ein, wählen Sie ein Ablaufdatum aus, und wählen Sie dann **Hinzufügen** aus. Notieren Sie sich den **Wert** des Geheimnisses zur Verwendung in einem späteren Schritt.

### <a name="configuring-optional-claims"></a>Konfigurieren optionaler Ansprüche

Wenn Sie die Ansprüche `family_name` und `given_name` von Azure AD erhalten möchten, können Sie optionale Ansprüche für Ihre Anwendung im Azure-Portal oder im Anwendungsmanifest konfigurieren. Weitere Informationen finden Sie unter [Bereitstellen optionaler Ansprüche für Ihre Azure AD-App](../active-directory/develop/active-directory-optional-claims.md).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie im Abschnitt **Verwalten** die Option **App-Registrierungen** aus.
1. Wählen Sie in der Liste die Anwendung aus, für die Sie optionale Ansprüche konfigurieren möchten.
1. Wählen Sie im Abschnitt **Verwalten** die Option **Tokenkonfiguration** aus.
1. Wählen Sie **Optionalen Anspruch hinzufügen** aus.
1. Wählen Sie als **Tokentyp** die Option **ID** aus.
1. Wählen Sie die hinzuzufügenden optionalen Ansprüche `family_name` und `given_name` aus.
1. Wählen Sie **Hinzufügen** aus. Wenn die Option **Microsoft Graph-E-Mail-Berechtigungen aktivieren (erforderlich zum Anzeigen von Ansprüchen in Token)** angezeigt wird, aktivieren Sie diese, und wählen Sie dann erneut **Hinzufügen** aus.

## <a name="optional-verify-your-app-authenticity"></a>[Optional] Überprüfen der Authentizität Ihrer App

Anhand der [Herausgeberüberprüfung](../active-directory/develop/publisher-verification-overview.md) können Ihre Benutzer die Authentizität der von Ihnen [registrierten](#register-an-azure-ad-app) App überprüfen. „Überprüfte App“ bedeutet, dass die Identität des Herausgebers der App über das Microsoft Partner Network (MPN) [überprüft](/partner-center/verification-responses) wurde. Informieren Sie sich darüber, wie Sie [Ihre App als durch den Herausgeber verifiziert markieren](../active-directory/develop/mark-app-as-publisher-verified.md). 

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen den von Ihnen erstellten Anwendungsschlüssel in Ihrem Azure AD B2C-Mandanten speichern.

1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie unter **Richtlinien** die Option **Identity Experience Framework** aus.
1. Wählen Sie **Richtlinienschlüssel** und dann **Hinzufügen** aus.
1. Klicken Sie unter **Optionen** auf `Manual`.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `AADAppSecret`.  Das Präfix `B2C_1A_` wird dem Namen Ihres Schlüssels bei der Erstellung automatisch hinzugefügt. Im XML-Code im folgenden Abschnitt lautet der entsprechende Verweis darauf daher *B2C_1A_AADAppSecret*.
1. Geben Sie im Feld **Geheimnis** den zuvor notierten geheimen Clientschlüssel ein.
1. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
1. Klicken Sie auf **Erstellen**.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurieren von Azure AD als Identitätsanbieter

Um Benutzern zu ermöglichen, sich mit einem Azure AD-Konto anzumelden, müssen Sie Azure AD als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde.

Sie können Azure AD als Anspruchsanbieter definieren, indem Sie Azure AD in der Erweiterungsdatei Ihrer Richtlinie dem Element **ClaimsProvider** hinzufügen.

1. Öffnen Sie die Datei *SocialAndLocalAccounts/**TrustFrameworkExtensions.xml***.
1. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
1. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADCommon-OpenIdConnect">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Ändern Sie unter dem Element **ClaimsProvider** den Wert für **Domain** in einen eindeutigen Wert, der eine Unterscheidung von anderen Identitätsanbietern ermöglicht.
1. Aktualisieren Sie unter dem Element **TechnicalProfile** den Wert für **DisplayName**, z. B. `Multi-Tenant AAD`. Dieser Wert wird auf die Anmeldeschaltfläche auf der Anmeldeseite angezeigt.
1. Legen Sie **client_id** auf die Anwendungs-ID der mehrinstanzenfähigen Azure AD-Anwendung fest, die Sie zuvor registriert haben.
1. Aktualisieren Sie unter **CryptographicKeys** den Wert von **StorageReferenceId** auf den Namen des zuvor erstellten Richtlinienschlüssels. Beispiel: `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Beschränken des Zugriffs

Wenn `https://login.microsoftonline.com/` als Wert für **ValidTokenIssuerPrefixes** verwendet wird, können sich alle Azure AD-Benutzer bei Ihrer Anwendung anmelden. Aktualisieren Sie die Liste der gültigen Tokenaussteller, und beschränken Sie den Zugriff auf eine bestimmte Liste von Azure AD-Mandanten, über die sich Benutzer anmelden können.

Zum Abrufen der Werte müssen Sie sich die OpenID Connect-Ermittlungsmetadaten für jeden einzelnen Azure AD-Mandanten ansehen, über den sich die Benutzer anmelden können sollen. Das Format der Metadaten-URL lautet in etwa `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`, wobei `your-tenant` der Name Ihres Azure AD-Mandanten ist. Beispiel:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Führen Sie die folgenden Schritte für jeden Azure AD-Mandanten aus, der für die Anmeldung verwendet werden soll:

1. Öffnen Sie Ihren Browser, und navigieren Sie zur OpenID Connect-Metadaten-URL für den Mandanten. Suchen Sie nach dem **issuer**-Objekt, und notieren Sie sich dessen Wert. Dies sollte in etwa wie folgt aussehen: `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/.well-known/openid-configuration`.
1. Kopieren Sie den Wert, und fügen Sie ihn für den Schlüssel **ValidTokenIssuerPrefixes** ein. Trennen Sie mehrere Aussteller durch ein Komma. Ein Beispiel mit zwei Ausstellern finden Sie im vorherigen `ClaimsProvider`-XML-Beispiel.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADCommonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADCommonExchange" TechnicalProfileReferenceId="AADCommon-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Wählen Sie die Richtliniendatei für die vertrauende Seite aus, z. B. `B2C_1A_signup_signin`.
1. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Common AAD** aus, um sich mit dem Azure AD-Konto anzumelden.

Wenn Sie die Funktion der mehrinstanzenfähigen Anmeldung testen möchten, führen Sie die letzten beiden Schritte unter Verwendung der Anmeldeinformationen für einen Benutzer aus, der in einem anderen Azure AD-Mandanten vorhanden ist. Kopieren Sie den **Endpunkt für sofortige Ausführung**, und öffnen Sie ihn in einem privaten Browserfenster, z. B. in Google Chrome im Inkognitomodus oder in Microsoft Edge in einem InPrivate-Fenster. Durch das Öffnen in einem privaten Browserfenster können Sie die vollständige User Journey testen, indem Sie keine aktuell zwischengespeicherten Azure AD-Anmeldeinformationen verwenden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich darüber, wie Sie [das Azure AD-Token an Ihre Anwendung übergeben](idp-pass-through-user-flow.md).

::: zone-end
