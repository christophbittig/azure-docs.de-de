---
title: Hinzufügen von Active Directory-Verbunddienste (AD FS) als SAML-Identitätsanbieter mithilfe benutzerdefinierter Richtlinien
titleSuffix: Azure AD B2C
description: Einrichten von AD FS 2016 mit dem SAML-Protokoll und benutzerdefinierten Richtlinien in Azure Active Directory B2C
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
ms.openlocfilehash: 0530629871a4000e1556f353e254215e6212835d
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131036540"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Hinzufügen von AD FS als SAML-Identitätsanbieter mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In diesem Artikel wird beschrieben, wie Sie die Anmeldung für ein AD FS-Benutzerkonto mithilfe [benutzerdefinierter Richtlinien](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C) aktivieren. Sie ermöglichen die Anmeldung, indem Sie einer benutzerdefinierten Richtlinie einen [SAML-Idenditätsanbieter](identity-provider-generic-saml.md) hinzufügen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-a-self-signed-certificate"></a>Erstellen eines selbstsignierten Zertifikats

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

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

## <a name="add-a-claims-provider"></a>Hinzufügen eines Anspruchsanbieters

Wenn Sie möchten, dass sich Benutzer mit einem AD FS-Konto anmelden, müssen Sie das Konto als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde.

Sie können ein AD FS-Konto als Anspruchsanbieter definieren, indem Sie es in der Erweiterungsdatei Ihrer Richtlinie dem **ClaimsProviders**-Element hinzufügen. Weitere Informationen finden Sie unter [Definieren eines SAML-Identitätsanbieters](identity-provider-generic-saml.md).

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
1. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
1. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

1. Ersetzen Sie `your-AD-FS-domain` durch den Namen Ihrer AD FS-Domäne und den Wert des **identityProvider**-Ausgabeanspruchs durch Ihren DNS (beliebiger Wert, der Ihre Domäne angibt).

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

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-an-ad-fs-relying-party-trust"></a>Konfigurieren einer AD FS-Vertrauensstellung der vertrauenden Seite

Um AD FS als Identitätsanbieter in Azure AD B2C verwenden zu können, müssen Sie eine AD FS-Vertrauensstellung der vertrauenden Seite mit den SAML-Metadaten von Azure AD B2C erstellen. Das folgende Beispiel zeigt eine URL der SAML-Metadaten eines technischen Azure AD B2C-Profils:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Verwenden Sie bei einer [benutzerdefinierten Domäne](custom-domain.md) das folgende Format:

```
https://your-domain-name/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Ersetzen Sie die folgenden Werte:

- **your-tenant-name** durch den Namen Ihres Mandanten, z. B. Ihr-Mandant.onmicrosoft.com.
- **your-domain-name** durch den Namen Ihrer benutzerdefinierten Domäne, z. B. login.contoso.com.
- **your-policy** durch den Namen Ihrer Richtlinie. Beispiel: B2C_1A_signup_signin_adfs.
- **your-technical-profile** durch den Namen des technische Profil des SAML-Identitätsanbieters. Beispiel: Contoso-SAML2

Öffnen Sie einen Browser, und browsen Sie zu dieser URL. Stellen Sie sicher, dass Sie die richtige URL eingeben und dass Sie Zugriff auf die XML-Metadatendatei haben. Um eine neue Vertrauensstellung der vertrauenden Seite mithilfe des AD FS-Verwaltungs-Snap-Ins hinzuzufügen und die Einstellungen manuell zu konfigurieren, führen Sie das folgende Verfahren auf einem Verbundserver aus. Eine Mitgliedschaft in **Administratoren** oder Entsprechendem auf dem lokalen Computer ist die Mindestvoraussetzung, um dieses Verfahren abzuschließen.

1. Wählen Sie im Server-Manager **Tools** und dann **AD FS-Verwaltung** aus.
2. Wählen Sie **Hinzufügen der Vertrauensstellung der vertrauenden Seite**.
3. Wählen Sie auf der Seite **Willkommen** die Option **Ansprüche unterstützend** und dann **Start** aus.
4. Wählen Sie auf der Seite **Datenquelle auswählen** die Option **Import data about the relying party publish online or on a local network** (Daten über die vertrauende Seite für die Veröffentlichung online oder in einem lokalen Netzwerk importieren) aus, geben Sie Ihre Azure AD B2C-Metadaten-URL an, und wählen Sie dann **Weiter** aus.
5. Geben Sie auf der Seite **Anzeigename angeben** in **Anzeigename** einen Namen und unter **Hinweise** eine Beschreibung für diese Vertrauensstellung der vertrauenden Seite ein, und wählen Sie dann **Weiter** aus.
6. Wählen Sie auf der Seite **Zugriffssteuerungsrichtlinie auswählen** eine Richtlinie und dann **Weiter** aus.
7. Überprüfen Sie die Einstellungen auf der Seite **Bereit zum Hinzufügen der Vertrauensstellung**, und wählen Sie dann **Weiter** aus, um die Daten Ihrer Vertrauensstellung der vertrauenden Seite zu speichern.
8. Wählen Sie auf der Seite **Fertig stellen** die Option **Schließen** aus. Damit zeigen Sie automatisch das Dialogfeld **Anspruchsregeln bearbeiten** an.
9. Wählen Sie **Regel hinzufügen** aus.
10. Wählen Sie in **Anspruchsregelvorlage** die Option **Senden von LDAP-Attributen als Ansprüche** aus.
11. Geben Sie einen **Anspruchsregelnamen** an. Wählen Sie als **Attributspeicher** die Option **Active Directory** aus. Fügen Sie die folgenden Ansprüche hinzu, und wählen Sie **Fertig stellen** und **OK** aus.

    | LDAP-Attribut | Typ des ausgehenden Anspruchs |
    | -------------- | ------------------- |
    | Benutzerprinzipalname | userPrincipalName |
    | Surname | family_name |
    | Vorname | given_name |
    | E-Mail-Adresse | E-mail |
    | Anzeigename | name |

    Beachten Sie, dass einige dieser Namen in der Dropdownliste der Typen für ausgehende Ansprüche nicht angezeigt werden. Sie müssen sie manuell eingeben. (Die Dropdownliste kann bearbeitet werden.)

12.  Basierend auf Ihrem Zertifikattyp müssen Sie möglicherweise den Hashalgorithmus festlegen. Wählen Sie im Eigenschaftenfenster der Vertrauensstellung der vertrauenden Seite (B2C-Demo) die Registerkarte **Erweitert** aus, ändern Sie **Sicherer Hashalgorithmus** in `SHA-256`, und wählen Sie dann **OK** aus.
13. Wählen Sie im Server-Manager **Tools** und dann **AD FS-Verwaltung** aus.
14. Wählen Sie die Vertrauensstellung der vertrauenden Seite, die Sie erstellt haben, anschließend **Update ausführen aus Verbundmetadaten** und dann **Aktualisieren** aus.

## <a name="test-your-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie unter **Richtlinien** die Option **Identity Experience Framework** aus.
1. Wählen Sie die Richtliniendatei für die vertrauende Seite aus, z. B. `B2C_1A_signup_signin`.
1. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Contoso AD FS** aus, um sich mit dem AD FS-Identitätsanbieter für „Contoso“ anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

## <a name="troubleshooting-ad-fs-service"></a>Problembehandlung bei AD FS  

AD FS ist für die Nutzung des Anwendungsprotokolls von Windows konfiguriert. Wenn Sie Probleme beim Einrichten von AD FS als SAML-Identitätsanbieter mithilfe benutzerdefinierter Richtlinien in Azure AD B2C haben, können Sie das AD FS-Ereignisprotokoll überprüfen:

1. Geben Sie in die **Suchleiste** unter Windows **Ereignisanzeige** ein, und wählen Sie anschließend die Desktop-App **Ereignisanzeige** aus.
1. Um das Protokoll eines anderen Computers anzuzeigen, klicken Sie mit der rechten Maustaste auf **Ereignisanzeige (lokal)** . Wählen Sie **Verbindung mit anderem Computer herstellen** aus, und füllen Sie die Felder aus, um die Bearbeitung des Dialogfelds **Computer auswählen** abzuschließen.
1. Öffnen Sie in der **Ereignisanzeige** das **Anwendungs- und Dienstprotokoll**.
1. Wählen Sie **AD FS** und dann **Verwaltung** aus. 
1. Um weitere Informationen zu einem bestimmten Ereignis anzuzeigen, doppelklicken Sie auf dieses Ereignis.  

### <a name="saml-request-is-not-signed-with-expected-signature-algorithm-event"></a>Die SAML-Anforderung ist nicht mit dem erwarteten Signaturalgorithmusereignis signiert

Dieser Fehler bedeutet, dass die von Azure AD B2C gesendete SAML-Anforderung nicht mit dem erwarteten Signaturalgorithmus signiert ist, der in AD FS konfiguriert ist. Beispielsweise wird die SAML-Anforderung mit dem Signaturalgorithmus `rsa-sha256` signiert, wobei jedoch `rsa-sha1` der erwartete Signaturalgorithmus ist. Um dieses Problem zu beheben, stellen Sie sicher, dass sowohl Azure AD B2C als auch AD FS mit dem gleichen Signaturalgorithmus konfiguriert sind.

#### <a name="option-1-set-the-signature-algorithm-in-azure-ad-b2c"></a>Option 1: Festlegen des Signaturalgorithmus in Azure AD B2C  

Sie können konfigurieren, wie die SAML-Anforderung in Azure AD B2C signiert werden soll. Die Metadaten von [XmlSignatureAlgorithm](identity-provider-generic-saml.md) bestimmen in der SAML-Anforderung den Wert des Parameters `SigAlg` (Abfragezeichenfolge oder POST-Parameter). Im folgenden Beispiel wird Azure AD B2C für den Signaturalgorithmus `rsa-sha256` konfiguriert.

```xml
<Metadata>
  <Item Key="WantsEncryptedAssertions">false</Item>
  <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
  <Item Key="XmlSignatureAlgorithm">Sha256</Item>
</Metadata>
```

#### <a name="option-2-set-the-signature-algorithm-in-ad-fs"></a>Option 2: Festlegen des Signaturalgorithmus in AD FS 

Alternativ können Sie den erwarteten Signaturalgorithmus von SAML-Anforderungen in AD FS konfigurieren.

1. Wählen Sie im Server-Manager **Tools** und dann **AD FS-Verwaltung** aus.
1. Wählen Sie die **Vertrauensstellung der vertrauenden Seite** aus, die Sie zuvor erstellt haben.
1. Wählen Sie **Eigenschaften** und dann **Erweitert** aus.
1. Konfigurieren Sie **Sicherer Hashalgorithmus**, und wählen Sie **OK** aus, um die Änderungen zu speichern.

::: zone-end
