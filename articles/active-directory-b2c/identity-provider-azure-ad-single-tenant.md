---
title: Einrichten der Anmeldung für eine Azure AD-Organisation
titleSuffix: Azure AD B2C
description: Einrichten der Anmeldung für eine bestimmte Azure Active Directory-Organisation in Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a63c89174103613c722d463e32085600019e9ea0
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026005"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Einrichten der Anmeldung für eine bestimmte Azure Active Directory-Organisation in Azure Active Directory B2C

In diesem Artikel erfahren Sie, wie Sie die Anmeldung für Benutzer einer bestimmten Azure AD-Organisation über einen Benutzerflow in Azure AD B2C aktivieren.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="verify-the-applications-publisher-domain"></a>Überprüfen der Herausgeberdomäne einer Anwendung
Ab November 2020 werden neue Anwendungsregistrierungen in der Benutzer-Zustimmungsaufforderung als nicht überprüft angezeigt, es sei denn, [die Herausgeberdomäne der Anwendung wurde überprüft](../active-directory/develop/howto-configure-publisher-domain.md) ***und*** die Identität des Unternehmens wurde durch das Microsoft Partner Network überprüft und der Anwendung zugeordnet. ([Erfahren Sie mehr](../active-directory/develop/publisher-verification-overview.md) über diese Änderung.) Beachten Sie, dass für Azure AD B2C-Benutzerflows die Domäne des Herausgebers nur angezeigt wird, wenn ein Microsoft-Konto oder ein anderer [Azure AD](../active-directory-b2c/identity-provider-microsoft-account.md)-Mandant als Identitätsanbieter verwendet wird. Gehen Sie wie folgt vor, um diese neuen Anforderungen zu erfüllen:

1. [Überprüfen Sie Ihre Unternehmensidentität mit Ihrem Microsoft Partner Network-Konto (MPN-Konto)](/partner-center/verification-responses). Bei diesem Prozess werden Informationen zu Ihrem Unternehmen und zum primären Kontakt Ihres Unternehmens überprüft.
1. Schließen Sie mithilfe einer der folgenden Optionen die Herausgeberüberprüfung ab, um Ihr MPN-Konto Ihrer App-Registrierung zuzuordnen:
   - Wenn sich die App-Registrierung für den Identitätsanbieter „Microsoft-Konto“ in einem Azure AD-Mandanten befindet, [überprüfen Sie Ihre App im App-Registrierungsportal](../active-directory/develop/mark-app-as-publisher-verified.md).
   - Wenn sich Ihre App-Registrierung für den Identitätsanbieter „Microsoft-Konto“ in einem Azure AD B2C-Mandanten befindet, [markieren Sie Ihre App mithilfe einer Microsoft Graph-API als vom Herausgeber verifiziert](../active-directory/develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) (z. B. mithilfe von Graph Explorer). Die Benutzeroberfläche zum Festlegen des verifizierten Herausgebers einer App ist derzeit für Azure AD B2C-Mandanten deaktiviert.

## <a name="register-an-azure-ad-app"></a>Registrieren einer Azure AD-App

Wenn Sie die Anmeldung für Benutzer mit einem Azure AD-Konto einer bestimmten Azure AD-Organisation in Azure Active Directory B2C (Azure AD B2C) aktivieren möchten, müssen Sie im [Azure-Portal](https://portal.azure.com) eine Anwendung erstellen. Weitere Informationen finden Sie unter [Registrieren einer Anwendung bei Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD-Mandanten Ihrer Organisation (z. B. Contoso) enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse + Abonnements** das Azure AD-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie unter **Azure-Dienste** **App-Registrierungen** aus, oder suchen Sie nach  **App-Registrierungen** und wählen Sie diese aus.
1. Wählen Sie **Neue Registrierung** aus.
1. Geben Sie einen **Namen** für Ihre Anwendung ein. Beispiel: `Azure AD B2C App`.
1. Übernehmen Sie für diese Anwendung die Standardauswahl von **Nur Konten in diesem Organisationsverzeichnis (Nur Standardverzeichnis – einzelner Tenant**).
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

1. Melden Sie sich unter Verwendung des Azure AD-Mandanten Ihrer Organisation beim [Azure-Portal](https://portal.azure.com) an. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie im Abschnitt **Verwalten** die Option **App-Registrierungen** aus.
1. Wählen Sie in der Liste die Anwendung aus, für die Sie optionale Ansprüche konfigurieren möchten.
1. Wählen Sie im Abschnitt **Verwalten** die Option **Tokenkonfiguration** aus.
1. Wählen Sie **Optionalen Anspruch hinzufügen** aus.
1. Wählen Sie als **Tokentyp** die Option **ID** aus.
1. Wählen Sie die hinzuzufügenden optionalen Ansprüche (`family_name` und `given_name`) aus.
1. Wählen Sie **Hinzufügen**. Wenn die Option **Microsoft Graph-E-Mail-Berechtigungen aktivieren (erforderlich zum Anzeigen von Ansprüchen in Token)** angezeigt wird, aktivieren Sie diese, und wählen Sie dann erneut **Hinzufügen** aus.

## <a name="optional-verify-your-app-authenticity"></a>[Optional] Überprüfen der Authentizität Ihrer App

Anhand der [Herausgeberüberprüfung](../active-directory/develop/publisher-verification-overview.md) können Ihre Benutzer die Authentizität der von Ihnen [registrierten](#register-an-azure-ad-app) App überprüfen. „Überprüfte App“ bedeutet, dass die Identität des Herausgebers der App über das Microsoft Partner Network (MPN) [überprüft](/partner-center/verification-responses) wurde. Informieren Sie sich darüber, wie Sie [Ihre App als vom Herausgeber verifiziert markieren](../active-directory/develop/mark-app-as-publisher-verified.md). 

::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurieren von Azure AD als Identitätsanbieter

1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das den Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Identitätsanbieter** und dann **Neuer OpenID Connect-Anbieter** aus.
1. Geben Sie einen **Namen** ein. Geben Sie beispielsweise *Contoso Azure AD* ein.
1. Geben Sie für **Metadaten-URL** die folgende URL ein, und ersetzen Sie dabei `{tenant}` durch den Domänennamen Ihres Azure AD-Mandanten.

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

 Beispiel: `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`. Wenn Sie eine benutzerdefinierte Domäne verwenden, ersetzen Sie `contoso.com` in `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration` durch Ihren benutzerdefinierten Domänennamen.

1. Geben Sie für **Client-ID** die zuvor notierte Anwendungs-ID ein.
1. Geben Sie im Feld **Geheimer Clientschlüssel** den zuvor notierten geheimen Clientschlüssel ein.
1. Geben Sie `openid profile` als **Scope** ein.
1. Übernehmen Sie die Standardwerte für **Antworttyp** und **Antwortmodus**.
1. (Optional) Geben Sie als **Domänenhinweis** die Zeichenfolge `contoso.com` ein. Weitere Informationen finden Sie unter [Einrichten einer direkten Anmeldung mit Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Wählen Sie unter **Zuordnung von Identitätsanbieteransprüchen** die folgenden Ansprüche aus:

    - **Benutzer-ID**: *oid*
    - **Anzeigename**: *name*
    - **Vorname**: *given_name*
    - **Nachname**: *family_name*
    - **E-Mail**: *email*

1. Wählen Sie **Speichern** aus.

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>Hinzufügen von Azure AD als Identitätsanbieter zu einem Benutzerflow 

Der Azure AD-Identitätsanbieter wurde nun eingerichtet, er ist jedoch noch auf keiner der Anmeldeseiten verfügbar. Hinzufügen von Azure AD-Identitätsanbieter zu einem Benutzerflow:

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Klicken Sie auf den Benutzerflow, dem Sie Azure AD als Identitätsanbieter hinzufügen möchten.
1. Wählen Sie unter **Einstellungen** die Option **Identitätsanbieter** aus.
1. Wählen Sie unter **Benutzerdefinierte Identitätsanbieter** die Option **Contoso Azure AD** aus.
1. Wählen Sie **Speichern** aus.
1. Um die Richtlinie zu testen, wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden. 
1. Wählen Sie die Schaltfläche **Benutzerflow ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Contoso Azure AD** aus, um sich mit dem Azure AD-Konto für „Contoso“ anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen den von Ihnen erstellten Anwendungsschlüssel in Ihrem Azure AD B2C-Mandanten speichern.

1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie unter **Richtlinien** die Option **Identity Experience Framework** aus.
1. Wählen Sie **Richtlinienschlüssel** und dann **Hinzufügen** aus.
1. Klicken Sie unter **Optionen** auf `Manual`.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `ContosoAppSecret`.  Das Präfix `B2C_1A_` wird dem Namen Ihres Schlüssels bei der Erstellung automatisch hinzugefügt. Im XML-Code im folgenden Abschnitt lautet der entsprechende Verweis darauf daher *B2C_1A_ContosoAppSecret*.
1. Geben Sie im Feld **Geheimnis** den zuvor notierten geheimen Clientschlüssel ein.
1. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
1. Klicken Sie auf **Erstellen**.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurieren von Azure AD als Identitätsanbieter

Um Benutzern zu ermöglichen, sich mit einem Azure AD-Konto anzumelden, müssen Sie Azure AD als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde.

Sie können Azure AD als Anspruchsanbieter definieren, indem Sie Azure AD in der Erweiterungsdatei Ihrer Richtlinie dem Element **ClaimsProvider** hinzufügen.

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADContoso-OpenIdConnect">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
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

4. Ändern Sie unter dem Element **ClaimsProvider** den Wert für **Domain** in einen eindeutigen Wert, der eine Unterscheidung von anderen Identitätsanbietern ermöglicht. Beispiel: `Contoso`. Am Ende dieser Domäneneinstellung wird nicht `.com` angefügt.
5. Aktualisieren Sie unter dem Element **ClaimsProvider** den Wert für **DisplayName** in einen Anzeigenamen für den Anspruchsanbieter. Dieser Wert wird derzeit nicht verwendet.

### <a name="update-the-technical-profile"></a>Aktualisieren des technischen Profils

Um ein Token vom Azure AD-Endpunkt zu erhalten, müssen Sie die Protokolle definieren, die Azure AD B2C zur Kommunikation mit Azure AD verwenden soll. Dies erfolgt im **TechnicalProfile**-Element von **ClaimsProvider**.

1. Aktualisieren Sie die ID des **TechnicalProfile**-Elements. Diese ID wird als Verweis auf dieses technische Profil aus anderen Teilen der Richtlinie verwendet, z. B. `AADContoso-OpenIdConnect`.
1. Aktualisieren Sie den Wert für **DisplayName**. Dieser Wert wird auf dem Anmeldebildschirm auf der Anmeldeschaltfläche angezeigt.
1. Aktualisieren Sie den Wert von **Beschreibung**.
1. Azure AD verwendet das OpenID Connect-Protokoll. Stellen Sie daher sicher, dass der Wert für **Protokoll**`OpenIdConnect` lautet.
1. Legen Sie den Wert von **METADATA** auf `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration` fest, wobei `tenant-name` der Name Ihres Azure AD-Mandanten ist. Zum Beispiel, `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. Legen Sie **client_id** auf die Anwendungs-ID aus der Anwendungsregistrierung fest.
1. Aktualisieren Sie unter **CryptographicKeys** den Wert von **StorageReferenceId** auf den Namen des zuvor erstellten Richtlinienschlüssels. Beispiel: `B2C_1A_ContosoAppSecret`.


[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADContosoExchange" TechnicalProfileReferenceId="AADContoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Wählen Sie die Richtliniendatei für die vertrauende Seite aus, z. B. `B2C_1A_signup_signin`.
1. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Contoso Employee** aus, um sich mit dem Azure AD-Konto für „Contoso“ anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich darüber, wie Sie [das Azure AD-Token an Ihre Anwendung übergeben](idp-pass-through-user-flow.md).
