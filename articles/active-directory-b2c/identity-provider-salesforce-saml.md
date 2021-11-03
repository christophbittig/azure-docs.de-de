---
title: Einrichten der Anmeldung mit einem Salesforce-SAML-Anbieter mithilfe des SAML-Protokolls
titleSuffix: Azure AD B2C
description: Einrichten der Anmeldung mit einem Salesforce-SAML-Anbieter mithilfe des SAML-Protokolls in Azure Active Directory B2C.
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
ms.openlocfilehash: 911ca309da9df2631e08ff353636877403be710d
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007176"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-saml-protocol-in-azure-active-directory-b2c"></a>Einrichten der Anmeldung mit einem Salesforce-SAML-Anbieter mithilfe des SAML-Protokolls in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"
[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In diesem Artikel wird beschrieben, wie Sie die Anmeldung für Benutzer einer Salesforce-Organisation mithilfe [benutzerdefinierter Richtlinien](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C) aktivieren. Sie ermöglichen die Anmeldung, indem Sie einer benutzerdefinierten Richtlinie einen [SAML-Idenditätsanbieter](identity-provider-generic-saml.md) hinzufügen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]
- Falls Sie dies noch nicht getan haben, registrieren Sie sich für ein [kostenloses Developer Edition-Konto](https://developer.salesforce.com/signup). In diesem Artikel wird die [Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ) verwendet.
- [Einrichtung einer eigenen Domäne](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) für Ihre Salesforce-Organisation.

## <a name="set-up-salesforce-as-an-identity-provider"></a>Einrichten von Salesforce als Identitätsanbieter

1. [Anmelden Sie sich bei Salesforce an](https://login.salesforce.com/).
2. Erweitern Sie im Menü auf der linken Seite unter **Einstellungen** die Option **Identität**, und wählen Sie dann **Identitätsanbieter** aus.
3. Wählen Sie **Enable Identity Provider** (Identitätsanbieter aktivieren) aus.
4. Wählen Sie unter **Select the certificate** (Zertifikat auswählen) das Zertifikat aus, das Salesforce für die Kommunikation mit Azure AD B2C verwendet soll. Sie können das Standardzertifikat verwenden.
5. Klicken Sie auf **Speichern**.

### <a name="create-a-connected-app-in-salesforce"></a>Erstellen einer verbundenen App in Salesforce

1. Wählen Sie auf der Seite **Identitätsanbieter** folgende Option aus: **Service Providers are now created via Connected Apps. Click here.** (Dienstanbieter werden jetzt mit verbundenen Apps erstellt. Hier klicken.).
2. Geben Sie unter **Grundlegende Informationen** die erforderlichen Werte für die verbundene App ein.
3. Aktivieren Sie unter **Web-App-Einstellungen** das Kontrollkästchen **SAML aktivieren**.
4. Geben Sie im Feld **Entitäts-ID** die folgende URL ein. Ersetzen Sie den Wert von `your-tenant` durch den Namen Ihres Azure AD B2C-Mandanten.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

      Verwenden Sie bei einer [benutzerdefinierten Domäne](custom-domain.md) das folgende Format:

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. Geben Sie im Feld **ACS-URL** die folgende URL ein. Ersetzen Sie den Wert von `your-tenant` durch den Namen Ihres Azure AD B2C-Mandanten.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

      Verwenden Sie bei einer [benutzerdefinierten Domäne](custom-domain.md) das folgende Format:

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

7. Scrollen Sie zum Ende der Liste, und klicken Sie dann auf **Speichern**.

### <a name="get-the-metadata-url"></a>Abrufen der Metadaten-URL

1. Klicken Sie auf der Übersichtsseite der verbundenen App auf **Verwalten**.
2. Kopieren Sie den Wert für **Metadata Discovery Endpoint** (Metadaten-Ermittlungsendpunkt), und speichern Sie ihn. Sie werden ihn später in diesem Artikel brauchen.

### <a name="set-up-salesforce-users-to-federate"></a>Einrichten von Salesforce-Benutzern für einen Verbund

1. Klicken Sie auf der Seite **Verwalten** der verbundenen App auf **Profile verwalten**.
2. Wählen Sie die Profile (oder Benutzergruppen) aus, für die ein Verbund mit Azure AD B2C hergestellt werden soll. Wählen Sie als Systemadministrator das Kontrollkästchen **Systemadministrator** aus, damit Sie den Verbund mit Ihrem Salesforce-Konto herstellen können.

## <a name="create-a-self-signed-certificate"></a>Erstellen eines selbstsignierten Zertifikats

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen das erstellte Zertifikat in Ihrem Azure AD B2C-Mandanten speichern.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Klicken Sie unter **Optionen** auf `Upload`.
1. Geben Sie einen **Namen** für die Richtlinie ein. Beispiel: SAMLSigningCert. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
1. Navigieren Sie zum Zertifikat „B2CSigningCert.pfx“, das Sie erstellt haben, und wählen Sie es aus.
1. Geben Sie das **Kennwort** für das Zertifikat ein.
1. Klicken Sie auf **Erstellen**.

## <a name="add-a-claims-provider"></a>Hinzufügen eines Anspruchsanbieters

Wenn Sie möchten, dass sich Benutzer mit einem Salesforce-Konto anmelden, müssen Sie das Konto als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde.

Sie können ein Salesforce-Konto als Anspruchsanbieter definieren, indem Sie es in der Erweiterungsdatei Ihrer Richtlinie dem **ClaimsProviders**-Element hinzufügen. Weitere Informationen finden Sie unter [Definieren eines SAML-Identitätsanbieters](identity-provider-generic-saml.md).

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
1. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
1. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-SAML2">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Ändern Sie den Wert von **PartnerEntity** in die Salesforce-Metadaten-URL, die Sie zuvor kopiert haben.
1. Ändern Sie den Wert beider Instanzen von **StorageReferenceId** in den Namen des Schlüssels Ihres Signaturzertifikats. Beispiel: B2C_1A_SAMLSigningCert.
1. Suchen Sie den Abschnitt `<ClaimsProviders>`, und fügen Sie den folgenden XML-Codeausschnitt hinzu. Wenn Ihre Richtlinie das technische `SM-Saml-idp`-Profil bereits enthält, fahren Sie mit dem nächsten Schritt fort. Weitere Informationen finden Sie unter [Sitzungsverwaltung für einmaliges Anmelden](custom-policy-reference-sso.md).

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Speichern Sie die Datei .

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Wählen Sie die Richtliniendatei für die vertrauende Seite aus, z. B. `B2C_1A_signup_signin`.
1. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Salesforce** aus, um sich mit dem Salesforce-Konto anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

::: zone-end