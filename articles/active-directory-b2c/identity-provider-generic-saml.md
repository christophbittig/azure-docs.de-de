---
title: Einrichten von Registrierung und Anmeldung mit SAML-Identitätsanbieter
titleSuffix: Azure Active Directory B2C
description: Hier erfahren Sie, wie Sie in Azure Active Directory B2C Registrierung und Anmeldung mit einem SAML-Identitätsanbieter einrichten.
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
ms.openlocfilehash: e76176c74405422fc871bc2bfb05e4dada01774d
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "132133785"
---
# <a name="set-up-sign-up-and-sign-in-with-saml-identity-provider-using-azure-active-directory-b2c"></a>Einrichten von Registrierung und Anmeldung mit SAML-Identitätsanbieter und Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) unterstützt den Verbund mit SAML 2.0-Identitätsanbietern. In diesem Artikel erfahren Sie, wie Sie die Anmeldung mit einem SAML-Identitätsanbieterkonto ermöglichen, damit Benutzer sich mit ihren vorhandenen Social Media- oder Unternehmensidentitäten wie [ADFS](./identity-provider-adfs.md) und [Salesforce](identity-provider-salesforce-saml.md) anmelden können.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="scenario-overview"></a>Übersicht über das Szenario

Sie können Azure AD B2C so konfigurieren, dass Benutzer sich mit Anmeldeinformationen von externen SAML-Identitätsanbietern für soziale Netzwerke oder Unternehmen bei Ihrer Anwendung anmelden können. Wenn Azure AD B2C mit einem SAML-Identitätsanbieter einen Verbund bildet, fungiert dieser als **Dienstanbieter**, der eine SAML-Anforderung an den SAML-**Identitätsanbieter** sendet und auf eine SAML-Antwort wartet. Das Diagramm weiter unten zeigt Folgendes:

1. Die Anwendung initiiert eine Autorisierungsanforderung an Azure AD B2C. Die Anwendung kann eine [OAuth 2.0](protocols-overview.md)-oder [OpenID Connect](openid-connect.md)-Anwendung oder ein [SAML-Dienstanbieter](saml-service-provider.md) sein. 
1. Auf der Azure AD B2C-Anmeldeseite wählt der Benutzer die Anmeldung mit einem SAML-Identitätsanbieterkonto (z. B. *Contoso*) aus. Azure AD B2C initiiert eine SAML-Autorisierungsanforderung und leitet den Benutzer an den SAML-Identitätsanbieter weiter, um die Anmeldung abzuschließen.
1. Der SAML-Identitätsanbieter gibt eine SAML-Antwort zurück.
1. Azure AD B2C überprüft das SAML-Token, extrahiert die Ansprüche, gibt ein eigenes Token aus und führt den Benutzer zurück zur Anwendung.  

![Anmelden mit dem SAML-Identitätsanbieter-Flow](./media/identity-provider-generic-saml/sign-in-with-saml-identity-provider-flow.png)

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="components-of-the-solution"></a>Komponenten der Lösung

Für dieses Szenario sind die folgenden Komponenten erforderlich:

* Ein SAML-**Identitätsanbieter** mit der Möglichkeit, SAML-Anforderungen von Azure AD B2C zu empfangen, zu decodieren und darauf zu antworten.
* Öffentlich verfügbarer SAML-**Metadatenendpunkt** für Ihren Identitätsanbieter.
* Ein [Azure AD B2C-Mandant](tutorial-create-tenant.md).

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen ein gültiges X509-Zertifikat mit dem privaten Schlüssel bereitstellen, um eine Vertrauensstellung zwischen Azure AD B2C und Ihrem SAML-Identitätsanbieter einzurichten. Azure AD B2C signiert die SAML-Anforderungen und verwendet dafür den privaten Schlüssel des Zertifikats. Der Identitätsanbieter überprüft die Anforderung mithilfe des öffentlichen Schlüssels des Zertifikats. Auf den öffentlichen Schlüssel kann über die Metadaten im technischen Profil zugegriffen werden. Stattdessen können Sie aber auch manuell die CER-Datei in Ihren SAML-Identitätsanbieter hochladen.

In den meisten Szenarios genügt ein selbstsigniertes Zertifikat. In Produktionsumgebungen wird die Verwendung eines X509-Zertifikats empfohlen, das von einer Zertifizierungsstelle ausgestellt wird. Wie später in diesem Dokument beschrieben können Sie die SAML-Signierung für Umgebungen, bei denen es sich nicht um Produktionsumgebungen handelt, auf beiden Seiten deaktivieren.

### <a name="obtain-a-certificate"></a>Beschaffung eines Zertifikats

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>Hochladen des Zertifikats

Sie müssen Ihr Zertifikat in Ihrem Azure AD B2C-Mandanten speichern.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Klicken Sie unter **Optionen** auf `Upload`.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `SAMLSigningCert`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
1. Navigieren Sie zur PFX-Datei Ihres Zertifikats mit dem privaten Schlüssel, und wählen Sie sie aus.
1. Klicken Sie auf **Erstellen**.

## <a name="configure-the-saml-technical-profile"></a>Konfigurieren des technischen SAML-Profils

Definieren Sie den SAML-Identitätsanbieter, indem Sie ihn dem Element **ClaimsProviders** in der Erweiterungsdatei Ihrer Richtlinie hinzufügen. Die Anspruchsanbieter enthalten ein technisches SAML-Profil, das die für die Kommunikation mit dem SAML-Identitätsanbieter erforderlichen Endpunkte und Protokolle vorgibt. So fügen Sie einen Anspruchsanbieter mit einem technischen SAML-Profil hinzu:

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
1. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
1. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>Contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your SAML identity provider account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="http://schemas.microsoft.com/identity/claims/displayname" />
            <OutputClaim ClaimTypeReferenceId="email"  />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

Aktualisieren Sie die folgenden XML-Elemente mit dem entsprechenden Wert:

|XML-Element  |Wert  |
|---------|---------|
|ClaimsProvider\Domain  | Der Name der Domäne, die für die [direkte Anmeldung](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider) verwendet wird. Geben Sie den Namen der Domäne ein, die Sie für die direkte Anmeldung verwenden möchten. Beispiel: *Contoso.com*. |
|TechnicalProfile\DisplayName|Dieser Wert wird auf dem Anmeldebildschirm auf der Anmeldeschaltfläche angezeigt. Beispiel: *Contoso*. |
|Metadata\PartnerEntity|URL zu den Metadaten des SAML-Identitätsanbieters. Alternativ können Sie die Metadaten des Identitätsanbieters kopieren und in das CDATA-Element `<![CDATA[Your IDP metadata]]>` einfügen.|

## <a name="map-the-claims"></a>Zuordnen der Ansprüche

Das Element **OutputClaims** enthält eine Liste von Ansprüchen, die vom SAML-Identitätsanbieter zurückgegeben werden. Ordnen Sie den Namen des in Ihrer Richtlinie definierten Anspruchs dem Namen der Assertion zu, die für den Identitätsanbieter definiert wurde. Überprüfen Sie Ihren Identitätsanbieter bezüglich der Liste der Ansprüche (Assertions). Weitere Informationen finden Sie unter [Anspruchszuordnung](identity-provider-generic-saml-options.md#claims-mapping).

Im obigen Beispiel enthält *Contoso-SAML2* die Ansprüche, die von einem SAML-Identitätsanbieter zurückgegeben werden:

* Der Anspruch **issuerUserId** wird dem Anspruch **assertionSubjectName** zugeordnet.
* Der Anspruch **first_name** wird dem Anspruch **givenName** zugeordnet.
* Der Anspruch **last_name** wird dem Anspruch **surname** zugeordnet.
* Der Anspruch **displayName** wird dem Anspruch `http://schemas.microsoft.com/identity/claims/displayname` zugeordnet.
* Dem Anspruch **email** wird kein Name zugeordnet.

Das technische Profil gibt auch Ansprüche zurück, die vom Identitätsanbieter nicht zurückgegeben werden:

* Der Anspruch **identityProvider** enthält den Namen des Identitätsanbieters.
* Der Anspruch **authenticationSource** enthält als Standardwert **socialIdpAuthentication**.
 
### <a name="add-the-saml-session-technical-profile"></a>Hinzufügen des technischen SAML-Sitzungsprofils

Wenn Sie noch kein technisches SAML-Sitzungsprofil `SM-Saml-idp` haben, fügen Sie Ihrer Erweiterungsrichtlinie ein Profil hinzu. Suchen Sie den Abschnitt `<ClaimsProviders>`, und fügen Sie den folgenden XML-Codeausschnitt hinzu. Wenn Ihre Richtlinie das technische `SM-Saml-idp`-Profil bereits enthält, fahren Sie mit dem nächsten Schritt fort. Weitere Informationen finden Sie unter [Sitzungsverwaltung für einmaliges Anmelden](custom-policy-reference-sso.md).

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
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-create-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-your-saml-identity-provider"></a>Konfigurieren des SAML-Identitätsanbieters

Nachdem Sie Ihre Richtlinie konfiguriert haben, müssen Sie Ihren SAML-Identitätsanbieter mit den SAML-Metadaten von Azure AD B2C konfigurieren. Die SAML-Metadaten sind Informationen, die im SAML-Protokoll verwendet werden, um die Konfiguration Ihrer Richtlinie, den **Dienstanbieter**, verfügbar zu machen. Sie definieren den Ort der Dienste, z. B. Anmelden und Abmelden, Zertifikate, Anmeldemethode usw.

Für jeden SAML-Identitätsanbieter sind unterschiedliche Schritte zum Einrichten eines Dienstanbieters erforderlich. Einige SAML-Identitätsanbieter fragen nach Azure AD B2C-Metadaten, bei anderen müssen Sie die Metadatendatei manuell durchsuchen und die Informationen bereitstellen. Anleitungen hierzu finden Sie in der Dokumentation Ihres Identitätsanbieters.

Das folgende Beispiel zeigt eine URL der SAML-Metadaten in einem technischen Azure AD B2C-Profil:

```
https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

Verwenden Sie bei einer [benutzerdefinierten Domäne](custom-domain.md) das folgende Format:

```
https://your-domain-name/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

Ersetzen Sie die folgenden Werte:

- **your-tenant-name** durch den Namen Ihres Mandanten, z. B. Ihr-Mandant.onmicrosoft.com.
- **your-domain-name** durch den Namen Ihrer benutzerdefinierten Domäne, z. B. login.contoso.com.
- **your-policy** durch den Namen Ihrer Richtlinie. Beispiel: B2C_1A_signup_signin_adfs.
- **your-technical-profile** durch den Namen des technische Profil des SAML-Identitätsanbieters. Beispiel: Contoso-SAML2

Öffnen Sie einen Browser, und browsen Sie zu dieser URL. Stellen Sie sicher, dass Sie die richtige URL eingeben und dass Sie Zugriff auf die XML-Metadatendatei haben.

## <a name="test-your-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie unter **Richtlinien** die Option **Identity Experience Framework** aus.
1. Wählen Sie die Richtliniendatei für die vertrauende Seite aus, z. B. `B2C_1A_signup_signin`.
1. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Contoso** aus, um sich mit dem Contoso-Konto anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren der SAML-Identitätsanbieteroptionen mit Azure Active Directory B2C](identity-provider-generic-saml-options.md)

::: zone-end