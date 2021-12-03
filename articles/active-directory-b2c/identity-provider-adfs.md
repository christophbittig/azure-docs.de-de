---
title: Hinzufügen von AD FS als OpenID Connect-Identitätsanbieter mithilfe von benutzerdefinierten Richtlinien
titleSuffix: Azure AD B2C
description: Einrichten von AD FS 2016 mit dem OpenID Connect-Protokoll und benutzerdefinierten Richtlinien in Azure Active Directory B2C
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
ms.openlocfilehash: ad00a74871373338a2b4e8b557297a808e8186fc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007294"
---
# <a name="add-ad-fs-as-an-openid-connect-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Hinzufügen von AD FS als OpenID Connect-Identitätsanbieter mithilfe von benutzerdefinierten Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]


## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-an-ad-fs-application"></a>Erstellen einer AD FS-Anwendung

Um Benutzern mit einem AD FS-Konto die Anmeldung bei Azure Active Directory B2C (Azure AD B2C) zu ermöglichen, erstellen Sie eine Anwendungsgruppe in Ihrer AD FS-Instanz. Weitere Informationen finden Sie unter [Erstellen einer Webanwendung mithilfe von OpenID Connect mit AD FS 2016 und höher](/windows-server/identity/ad-fs/development/enabling-openid-connect-with-ad-fs).

Führen Sie die folgenden Schritte aus, um eine Anwendungsgruppe zu erstellen:

1. Wählen Sie im **Server-Manager** zunächst **Tools** und dann **AD FS-Verwaltung** aus.
1. Klicken Sie in „AD FS-Verwaltung“ mit der rechten Maustaste auf **Anwendungsgruppen**, und wählen Sie **Anwendungsgruppe hinzufügen** aus.
1. Gehen Sie auf dem Bildschirm **Willkommen** des Assistenten für Anwendungsgruppen folgendermaßen vor:
    1. Geben Sie den **Namen** Ihrer Anwendung ein. Beispiel: *Azure AD B2C-Anwendung*.
    1. Wählen Sie unter **Client/Server-Anwendungen** die Vorlage **Webbrowser mit Zugriff auf eine Webanwendung** aus.
    1. Klicken Sie auf **Weiter**.
1. Gehen Sie auf dem Bildschirm **Native Anwendung** des Assistenten für Anwendungsgruppen folgendermaßen vor:
    1. Kopieren Sie den Wert für den **Clientbezeichner**. Der Clientbezeichner ist die **Anwendungs-ID** Ihrer AD FS-Instanz. Sie benötigen die Anwendungs-ID weiter unten in diesem Artikel.
    1. Geben Sie `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` als **Umleitungs-URI** ein. Bei Verwendung einer [benutzerdefinierten Domäne](custom-domain.md) geben Sie `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein. Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten und `your-domain-name` durch Ihre benutzerdefinierte Domäne.
    1. Wählen Sie **Weiter** und dann erneut **Weiter** aus, um den Assistenten für die App-Registrierung abzuschließen. 
    1. Klicken Sie auf **Schließen**.


### <a name="configure-the-app-claims"></a>Konfigurieren der App-Ansprüche

In diesem Schritt konfigurieren Sie die Ansprüche, die eine AD FS-Anwendung an Azure AD B2C zurückgibt.  

1. Wählen Sie unter **Anwendungsgruppen** die von Ihnen erstellte Anwendung aus.
1. Wählen Sie im Fenster mit den Anwendungseigenschaften unter **Anwendungen** den Eintrag **Webanwendung** aus. Klicken Sie dann auf **Bearbeiten**.
    :::image type="content" source="./media/identity-provider-adfs/ad-fs-edit-app.png" alt-text="Screenshot: Bearbeiten einer Webanwendung":::
1. Wählen Sie die Registerkarte **Ausstellungstransformationsregeln** und dann **Regel hinzufügen** aus.
1. Wählen Sie in **Anspruchsregelvorlage** die Option **Senden von LDAP-Attributen als Ansprüche** aus.
1. Geben Sie einen **Anspruchsregelnamen** an. Wählen Sie als **Attributspeicher** den Eintrag **Active Directory auswählen** aus, und fügen Sie die folgenden Ansprüche hinzu.

    | LDAP-Attribut | Typ des ausgehenden Anspruchs |
    | -------------- | ------------------- |
    | Benutzerprinzipalname | upn |
    | Surname | family_name |
    | Vorname | given_name |
    | Anzeigename | name |

    Beachten Sie, dass einige dieser Namen in der Dropdownliste der Typen für ausgehende Ansprüche nicht angezeigt werden. Sie müssen sie manuell eingeben. (Die Dropdownliste kann bearbeitet werden.)

1. Wählen Sie **Fertig stellen** und dann **Schließen** aus.


::: zone pivot="b2c-user-flow"

## <a name="configure-ad-fs-as-an-identity-provider"></a>Konfigurieren von AD FS als Identitätsanbieter

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Identitätsanbieter** und dann **Neuer OpenID Connect-Anbieter** aus.
1. Geben Sie einen **Namen** ein. Zum Beispiel *Contoso*.
1. Geben Sie bei **Metadaten-URL** die URL des [AD FS-OpenID Connect-Konfigurationsdokuments](/windows-server/identity/ad-fs/development/ad-fs-openid-connect-oauth-concepts#ad-fs-endpoints) ein. Zum Beispiel:

    ```http
    https://adfs.contoso.com/adfs/.well-known/openid-configuration 
    ```

1. Geben Sie für **Client-ID** die zuvor notierte Anwendungs-ID ein.
1. Geben Sie für den **Bereich** das `openid` ein.
1. Wählen Sie als **Antworttyp** die Option **id_token** aus.
1. (Optional) Geben Sie als **Domänenhinweis** die Zeichenfolge `contoso.com` ein. Weitere Informationen finden Sie unter [Einrichten einer direkten Anmeldung mit Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Wählen Sie unter **Zuordnung von Identitätsanbieteransprüchen** die folgenden Ansprüche aus:

    - **Benutzer-ID**: `upn`
    - **Anzeigename**: `unique_name`
    - **Vorname**: `given_name`
    - **Nachname**: `family_name`

1. Wählen Sie **Speichern** aus.

## <a name="add-ad-fs-identity-provider-to-a-user-flow"></a>Hinzufügen des AD FS-Identitätsanbieters zu einem Benutzerflow 

Der AD FS-Identitätsanbieter (Contoso) ist jetzt eingerichtet, aber noch auf keiner der Anmeldeseiten verfügbar. So fügen Sie den AD FS-Identitätsanbieter einem Benutzerflow hinzu:

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Wählen Sie den Benutzerflow aus, dem Sie den AD FS-Identitätsanbieter (Contoso) hinzufügen möchten.
1. Wählen Sie unter **Soziales Netzwerk als Identitätsanbieter** die Option **Contoso** aus.
1. Wählen Sie **Speichern** aus.
1. Um die Richtlinie zu testen, wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *testapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Benutzerflow ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Contoso** aus, um sich mit dem Contoso-Konto anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="configure-ad-fs-as-an-identity-provider"></a>Konfigurieren von AD FS als Identitätsanbieter

Damit Benutzer sich mit einem AD FS-Konto anmelden können, müssen Sie die AD FS-Instanz als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. 

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-OpenIdConnect">
          <DisplayName>Contoso</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://your-adfs-domain/adfs/.well-known/openid-configuration</Item>
            <Item Key="response_types">id_token</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your AD FS application ID</Item>
          </Metadata>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="upn" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="unique_name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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

4. Geben Sie bei **Metadaten-URL** die URL des [AD FS-OpenID Connect-Konfigurationsdokuments](/windows-server/identity/ad-fs/development/ad-fs-openid-connect-oauth-concepts#ad-fs-endpoints) ein. Zum Beispiel:

    ```
    https://adfs.contoso.com/adfs/.well-known/openid-configuration 
    ```
5. Legen Sie **client_id** auf die Anwendungs-ID aus der Anwendungsregistrierung fest.
6. Speichern Sie die Datei .

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Wählen Sie die Richtliniendatei für die vertrauende Seite aus, z. B. `B2C_1A_signup_signin`.
1. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Contoso** aus, um sich mit dem Contoso-Konto anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.


::: zone-end

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie das [AD FS-Token an Ihre Anwendung übergeben](idp-pass-through-user-flow.md).
