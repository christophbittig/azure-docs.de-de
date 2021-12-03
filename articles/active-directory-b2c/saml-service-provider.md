---
title: Konfigurieren von Azure Active Directory B2C als SAML-Identitätsanbieter für Ihre Anwendungen
title-suffix: Azure Active Directory B2C
description: Erfahren Sie, wie Sie Azure Active Directory B2C für die Bereitstellung von SAML-Protokollassertionen für Ihre Anwendungen (Dienstanbieter) konfigurieren.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/12/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4dee0d3730c74218e236c5d6b51afedc496e02fe
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522800"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registrieren einer SAML-Anwendung in Azure AD B2C

In diesem Artikel erfahren Sie, wie Sie Ihre Security Assertion Markup Language-Anwendungen (SAML-Anwendungen) (Dienstanbieter) mit Azure Active Directory B2C (Azure AD B2C) für die Authentifizierung verbinden.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>Übersicht

Organisationen, die Azure AD B2C als Lösung für die Identitäts- und Zugriffsverwaltung ihrer Kundschaft nutzen, müssen möglicherweise Anwendungen integrieren, die für die Authentifizierung das SAML-Protokoll verwenden. Im folgenden Diagramm wird dargestellt, wie Azure AD B2C als *Identitätsanbieter* (Identity Provider, IdP) fungiert, um einmaliges Anmelden (Single Sign-On, SSO) mit SAML-basierten Anwendungen zu erreichen.

![Abbildung mit Azure Active Directory B2C als Identitätsanbieter auf der linken Seite und als Dienstanbieter auf der rechten Seite](media/saml-service-provider/saml-service-provider-integration.png)

1. Die Anwendung erstellt eine SAML-AuthN-Anforderung, die an den SAML-Anmeldeendpunkt von Azure AD B2C gesendet wird.
2. Der Benutzer kann ein lokales Azure AD B2C-Konto oder einen anderen Verbundidentitätsanbieter (sofern konfiguriert) für die Authentifizierung verwenden.
3. Wenn sich Benutzer mit einem Verbundidentitätsanbieter anmelden, wird eine Tokenantwort an Azure AD B2C gesendet.
4. Azure AD B2C generiert eine SAML-Assertion und sendet sie an die Anwendung.

Sehen Sie sich dieses Video an, um zu erfahren, wie Sie SAML-Anwendungen in Azure AD B2C integrieren. 

>[!Video https://www.youtube.com/embed/r2TIVBCm7v4]

## <a name="prerequisites"></a>Voraussetzungen

Für das Szenario in diesem Artikel benötigen Sie Folgendes:

* Die benutzerdefinierte Richtlinie *SocialAndLocalAccounts* aus dem Starter Pack für benutzerdefinierte Richtlinien. Führen Sie die Schritte unter [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy) aus. 
* Ein grundlegendes Verständnis des SAML-Protokolls und Vertrautheit mit der SAML-Implementierung der Anwendung
* Eine als SAML-Anwendung konfigurierte Webanwendung. Sie muss in der Lage sein, die SAML-AuthN-Anforderungen zu senden und SAML-Antworten von Azure AD B2C empfangen, decodieren und überprüfen zu können. Die SAML-Anwendung wird auch als Anwendung der vertrauenden Seite oder als Dienstanbieter bezeichnet. 
* Der öffentlich verfügbare SAML-*Metadatenendpunkt* oder das XML-Dokument der SAML-Anwendung.
* Einen [Azure AD B2C-Mandanten](tutorial-create-tenant.md).

Wenn Sie noch nicht über eine SAML-Anwendung und einen zugehörigen Metadatenendpunkt verfügen, können Sie die [SAML-Beispielanwendung][samltest] verwenden, die für Tests bereitsteht.

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="set-up-certificates"></a>Einrichten von Zertifikaten

Damit eine Vertrauensstellung zwischen Ihrer Anwendung und Azure AD B2C hergestellt werden kann, müssen beide Dienste die Signaturen des jeweils anderen erstellen und überprüfen können. Konfigurieren Sie X509-Zertifikate in Ihrer Anwendung und in Azure AD B2C.

**Anwendungszertifikate**

| Verwendung | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| SAML-Anforderungssignatur  | Nein | Ein in Ihrer Web-App gespeichertes Zertifikat mit einem privaten Schlüssel. Ihre Anwendung verwendet das Zertifikat, um SAML-Anforderungen zu signieren, die an Azure AD B2C gesendet werden. Die Web-App muss den öffentlichen Schlüssel über den SAML-Metadatenendpunkt verfügbar machen. Azure AD B2C überprüft die SAML-Anforderungssignatur mithilfe des öffentlichen Schlüssels aus den Anwendungsmetadaten.|
| Verschlüsselung von SAML-Assertionen  | Nein | Ein in Ihrer Web-App gespeichertes Zertifikat mit einem privaten Schlüssel. Die Web-App muss den öffentlichen Schlüssel über den SAML-Metadatenendpunkt verfügbar machen. Azure AD B2C kann Assertionen für Ihre Anwendung mithilfe des öffentlichen Schlüssels verschlüsseln. Die Anwendung verwendet den privaten Schlüssel, um die Assertion zu entschlüsseln.|

**Azure AD B2C-Zertifikate**

| Verwendung | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| SAML-Antwortsignatur | Ja  | Ein in Azure AD B2C gespeichertes Zertifikat mit einem privaten Schlüssel. Dieses Zertifikat wird von Azure AD B2C zum Signieren der SAML-Antwort verwendet, die an Ihre Anwendung gesendet wird. Ihre Anwendung liest den öffentlichen Metadatenschlüssel in Azure AD B2C, um die Signatur der SAML-Antwort zu überprüfen. |
| Signieren von SAML-Assertionen | Ja | Ein in Azure AD B2C gespeichertes Zertifikat mit einem privaten Schlüssel. Dieses Zertifikat wird von Azure AD B2C zum Signieren des Abschnitts `<saml:Assertion>` in der SAML-Antwort verwendet.  |

In einer Produktionsumgebung empfiehlt sich die Verwendung von Zertifikaten, die von einer öffentlichen Zertifizierungsstelle ausgestellt wurden. Sie können dieses Verfahren jedoch auch mit selbstsignierten Zertifikaten ausführen.

### <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Erstellen Sie ein Signaturzertifikat für die SAML-Antwort, um eine Vertrauensstellung zwischen Ihrer Anwendung und Azure AD B2C einzurichten. Dieses Zertifikat wird von Azure AD B2C zum Signieren der SAML-Antwort verwendet, die an Ihre Anwendung gesendet wird. Ihre Anwendung liest den öffentlichen Metadatenschlüssel für Azure AD B2C, um die Signatur der SAML-Antwort zu überprüfen. 

> [!TIP]
> Sie können diesen Richtlinienschlüssel für andere Zwecke verwenden, z. B. zum Signieren der [SAML-Assertion](saml-service-provider-options.md#check-the-saml-assertion-signature). 

### <a name="obtain-a-certificate"></a>Beschaffung eines Zertifikats

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>Hochladen des Zertifikats

Sie müssen Ihr Zertifikat in Ihrem Azure AD B2C-Mandanten speichern.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite **Übersicht** die Option **Identity Experience Framework** aus.
1. Wählen Sie **Richtlinienschlüssel** aus, und wählen Sie dann **Hinzufügen** aus.
1. Wählen Sie unter **Optionen** den Eintrag **Hochladen** aus.
1. Geben Sie in **Name** einen Namen für den Richtlinienschlüssel ein. Geben Sie z. B. **SamlIdpCert** ein. Dem Namen Ihres Schlüssels wird automatisch das Präfix **B2C_1A_** hinzugefügt.
1. Navigieren Sie zur PFX-Datei Ihres Zertifikats mit dem privaten Schlüssel, und wählen Sie sie aus.
1. Klicken Sie auf **Erstellen**.

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>Aktivieren Ihrer Richtlinie für das Herstellen einer Verbindung mit einer SAML-Anwendung

Damit eine Verbindung mit Ihrer SAML-Anwendung hergestellt werden kann, muss Azure AD B2C SAML-Antworten erstellen können.

Öffnen Sie die Datei *SocialAndLocalAccounts\TrustFrameworkExtensions.xml* im Starter Pack für benutzerdefinierte Richtlinien.

Suchen Sie den Abschnitt `<ClaimsProviders>`, und fügen Sie den folgenden XML-Codeausschnitt hinzu, um Ihren SAML-Antwortgenerator zu implementieren:

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML-based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="configure-the-issuer-uri-of-the-saml-response"></a>Konfigurieren des Aussteller-URI der SAML-Antwort

Sie können den Wert des Metadatenelements `IssuerUri` im technischen Profil des SAML-Tokenausstellers ändern. Diese Änderung wird in dem `issuerUri`-Attribut berücksichtigt, das in der SAML-Antwort von Azure AD B2C zurückgegeben wird. Konfigurieren Sie Ihre Anwendung so, dass sie denselben `IssuerUri`-Wert während der Überprüfung der SAML-Antwort akzeptiert.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="configure-your-policy-to-issue-a-saml-response"></a>Konfigurieren Ihrer Richtlinie für das Ausstellen einer SAML-Antwort

Da Ihre Richtlinie nun SAML-Antworten erstellen kann, müssen Sie die Richtlinie so konfigurieren, dass statt der standardmäßigen JWT-Antwort eine SAML-Antwort an Ihre Anwendung ausgegeben wird.

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>Erstellen einer für SAML konfigurierten Registrierungs- oder Anmelderichtlinie

1. Erstellen Sie eine Kopie der Datei *SignUpOrSignin.xml* im Arbeitsverzeichnis Ihres Starter Packs, und speichern Sie sie unter einem neuen Namen. In diesem Artikel wird als Beispiel *SignUpOrSigninSAML.xml* verwendet. Diese Datei ist Ihre Richtliniendatei für die vertrauende Seite. Sie ist so konfiguriert, dass standardmäßig eine JWT-Antwort ausgestellt wird.

1. Öffnen Sie die Datei *SignUpOrSigninSAML.xml* in Ihrem bevorzugten Editor.

1. Ändern Sie die Werte von `PolicyId` und `PublicPolicyUri` in der Richtlinie in `B2C_1A_signup_signin_saml` und `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml`.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Am Ende der User Journey enthält Azure AD B2C einen Schritt `SendClaims`. Dieser Schritt verweist auf das technische Profil des Tokenausstellers. Wenn anstelle der standardmäßigen JWT-Antwort eine SAML-Antwort ausgegeben werden soll, ändern Sie den Schritt `SendClaims` so, dass auf das technische Profil des neuen SAML-Tokenausstellers (`Saml2AssertionIssuer`) verwiesen wird.

Fügen Sie den folgenden XML-Codeausschnitt direkt vor dem `<RelyingParty>`-Element hinzu. Dieser XML-Code überschreibt den Orchestrierungsschritt 7 in der User Journey _SignUpOrSignIn_. 

Wenn Sie aus einem anderen Ordner im Starter Pack gestartet sind oder die User Journey durch Hinzufügen oder Entfernen von Orchestrierungsschritten angepasst haben, stellen Sie sicher, dass die Schrittnummer im `order`-Element dem in der User Journey für den Tokenausstellerschritt angegebenen Wert entspricht. In den anderen Starter Pack-Ordnern wird beispielsweise Schrittnummer 4 für `LocalAccounts`, 6 für `SocialAccounts` und 9 für `SocialAndLocalAccountsWithMfa` verwendet.

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

Das Element der vertrauenden Seite bestimmt, welches Protokoll von Ihrer Anwendung verwendet wird. Der Standardwert lautet `OpenId`. Das `Protocol`-Element muss in `SAML` geändert werden. Die Ausgabeansprüche erstellen die Anspruchszuordnung für die SAML-Assertion.

Ersetzen Sie das gesamte `<TechnicalProfile>`-Element im `<RelyingParty>`-Element durch das folgende technische Profil-XML. Ersetzen Sie `tenant-name` durch den Namen Ihres Azure AD B2C-Mandanten.

```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
```

Die endgültige Richtliniendatei für die vertrauende Seite sollte wie der folgende XML-Code aussehen:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

> [!NOTE]
> Auf die gleiche Weise können Sie auch andere Arten von Benutzerflows implementieren (z. B. Flows für die Anmeldung, Kennwortzurücksetzung oder Profilbearbeitung).

### <a name="upload-your-policy"></a>Hochladen Ihrer Richtlinie

Speichern Sie die Änderungen, und laden Sie die neuen Richtliniendateien *TrustFrameworkExtensions.xml* und *SignUpOrSigninSAML.xml* in das Azure-Portal hoch.

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>Testen der IdP-SAML-Metadaten von Azure AD B2C

Nachdem die Richtliniendateien hochgeladen wurden, generiert Azure AD B2C das von der Anwendung zu verwendende SAML-Metadatendokument des Identitätsanbieters anhand der Konfigurationsinformationen. Das SAML-Metadatendokument enthält die Speicherorte von Diensten, z. B. Anmelde- und Abmeldemethoden, Zertifikate usw.

Die Azure AD B2C-Richtlinienmetadaten sind unter der folgenden URL verfügbar:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

Ersetzen Sie `<tenant-name>` durch den Namen des Azure AD B2C-Mandanten. Ersetzen Sie `<policy-name>` durch den Namen (ID) der Richtlinie. Hier sehen Sie ein Beispiel:

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>Registrieren Ihrer SAML-Anwendung in Azure AD B2C

Damit Azure AD B2C Ihre Anwendung als vertrauenswürdig einstuft, müssen Sie eine Azure AD B2C-Anwendungsregistrierung erstellen. Die Registrierung enthält Konfigurationsinformationen, z. B. den Metadatenendpunkt der Anwendung.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein. Geben Sie z. B. **SAMLApp1** ein.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Nur Konten in diesem Organisationsverzeichnis** aus.
1. Wählen Sie unter **Umleitungs-URIs** die Option **Web** aus, und geben Sie dann `https://localhost` ein. Diesen Wert ändern Sie später im Manifest der Anwendungsregistrierung.
1. Wählen Sie **Registrieren**.

### <a name="configure-your-application-in-azure-ad-b2c"></a>Konfigurieren Ihrer Anwendung in Azure AD B2C

Für SAML-Apps müssen Sie im Manifest der Anwendungsregistrierung mehrere Eigenschaften konfigurieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu der Anwendungsregistrierung, die Sie im vorherigen Abschnitt erstellt haben.
1. Wählen Sie unter **Verwalten** die Option **Manifest** aus, um den Manifest-Editor zu öffnen. Ändern Sie dann die in den folgenden Abschnitten beschriebenen Eigenschaften.

#### <a name="add-the-identifier"></a>Hinzufügen des Bezeichners

Wenn Ihre SAML-Anwendung eine Anforderung an Azure AD B2C stellt, enthält die SAML-AuthN-Anforderung ein `Issuer`-Attribut. Der Wert dieses Attributs ist in der Regel mit dem Metadatenwert `entityID` der Anwendung identisch. Azure AD B2C verwendet diesen Wert, um die Anwendungsregistrierung im Verzeichnis zu suchen und die Konfiguration zu lesen. Damit diese Suche erfolgreich ist, muss der `identifierUri` in der Anwendungsregistrierung mit einem Wert aufgefüllt werden, der mit dem `Issuer`-Attribut übereinstimmt.

Suchen Sie im Registrierungsmanifest den `identifierURIs`-Parameter, und fügen Sie den entsprechenden Wert hinzu. Dieser Wert entspricht dem Wert, der in den SAML-AuthN-Anforderungen für `EntityId` bei der Anwendung konfiguriert ist, und dem `entityID`-Wert in den Metadaten der Anwendung. Außerdem müssen Sie den Parameter `accessTokenAcceptedVersion` suchen und den Wert auf `2` festlegen.

> [!IMPORTANT]
> Wenn Sie `accessTokenAcceptedVersion` nicht auf `2` aktualisieren, erhalten Sie eine Fehlermeldung mit dem Hinweis, dass eine überprüfte Domäne erforderlich ist.

Im folgenden Beispiel sehen Sie den Wert von `entityID` in den SAML-Metadaten:

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

Die `identifierUris`-Eigenschaft akzeptiert nur URLs in der Domäne `tenant-name.onmicrosoft.com`.

```json
"identifierUris":"https://tenant-name.onmicrosoft.com",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>Freigeben der Metadaten der Anwendung für Azure AD B2C

Nachdem die Anwendungsregistrierung über den zugehörigen `identifierUri`-Wert geladen wurde, verwendet Azure AD B2C die Metadaten der Anwendung, um die SAML-AuthN-Anforderung zu überprüfen und die richtige Antwort zu bestimmen.

Es wird empfohlen, einen öffentlich zugänglichen Metadatenendpunkt für die Anwendung verfügbar zu machen.

Wenn Eigenschaften in der SAML-Metadaten-URL *und* im Manifest der Anwendungsregistrierung angegeben sind, werden sie *zusammengeführt*. Die in der Metadaten-URL angegebenen Eigenschaften werden zuerst verarbeitet und besitzen Vorrang.

Wenn Sie die SAML-Testanwendung als Beispiel verwenden, würden Sie im Anwendungsmanifest für `samlMetadataUrl` den folgenden Wert angeben:

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>Überschreiben oder Festlegen der Assertionsverbraucher-URL (optional)

Sie können die Antwort-URL konfigurieren, an die Azure AD B2C SAML-Antworten sendet. Antwort-URLs können im Anwendungsmanifest konfiguriert werden. Diese Konfiguration ist sinnvoll, wenn Ihre Anwendung keinen öffentlich zugänglichen Metadatenendpunkt verfügbar macht.

Die Antwort-URL für eine SAML-Anwendung ist der Endpunkt, an dem die Anwendung den Empfang von SAML-Antworten erwartet. Die Anwendung stellt diese URL in der Regel im Metadatendokument als `Location`-Attribut des `AssertionConsumerService`-Elements bereit, wie in diesem Beispiel dargestellt:

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

Wenn das Metadatenelement `AssertionConsumerService` der Anwendung fehlt oder Sie es überschreiben möchten, konfigurieren Sie die Manifesteigenschaft `replyUrlsWithType` der Anwendungsregistrierung. Azure AD B2C verwendet `replyUrlsWithType`, um Benutzer umzuleiten, nachdem sie mit dem `HTTP-POST`-Bindungstyp angemeldet wurden.

Wenn Sie die SAML-Testanwendung als Beispiel verwenden, würden Sie die `url`-Eigenschaft von `replyUrlsWithType` auf den im folgenden JSON-Codeausschnitt angegebenen Wert festlegen:

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>Überschreiben oder Festlegen der Abmelde-URL (optional)

Die Abmelde-URL legt fest, wohin der Benutzer nach einer Abmeldeanforderung umgeleitet werden soll. Die Anwendung stellt diese URL in der Regel im Metadatendokument als `Location`-Attribut des `SingleLogoutService`-Elements bereit, wie im folgenden Beispiel dargestellt:

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</SPSSODescriptor>
```

Wenn das Metadatenelement `SingleLogoutService` der Anwendung fehlt, konfigurieren Sie die Manifesteigenschaft `logoutUrl` der Anwendungsregistrierung. Azure AD B2C verwendet `logoutURL`, um Benutzer umzuleiten, nachdem sie mit dem `HTTP-Redirect`-Bindungstyp abgemeldet wurden.

In der SAML-Testanwendung als Beispiel würde die `logoutUrl`-Eigenschaft auf `https://samltestapp2.azurewebsites.net/logout` festgelegt:

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> Wenn Sie die Antwort-URL und die Abmelde-URL im Anwendungsmanifest konfigurieren, ohne den Metadatenendpunkt der Anwendung über die `samlMetadataUrl`-Eigenschaft aufzufüllen, validiert Azure AD B2C die SAML-Anforderungssignatur nicht. Die SAML-Antwort wird ebenfalls nicht verschlüsselt.

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>Konfigurieren von Azure AD B2C als SAML-IdP in Ihrer SAML-Anwendung

Der letzte Schritt besteht darin, Azure AD B2C als SAML-IdP in Ihrer SAML-Anwendung zu aktivieren. Jede Anwendung ist anders, und daher sind auch die auszuführenden Schritte unterschiedlich. Weitere Informationen finden Sie in der Dokumentation zu Ihrer App.

Die Metadaten können in Ihrer Anwendung als *statische Metadaten* oder als *dynamische Metadaten* konfiguriert werden. Im statischen Modus kopieren Sie alle oder einen Teil der Metadaten aus den Metadaten der Azure AD B2C-Richtlinie. Im dynamischen Modus stellen Sie die URL zu den Metadaten bereit, damit Ihre Anwendung die Metadaten dynamisch lesen kann.

In der Regel sind einige oder alle der folgenden Angaben erforderlich:

* **Metadaten**: Verwenden Sie das Format `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata`.
* **Aussteller:** Der `issuer`-Wert der SAML-Anforderung muss mit einem der URIs übereinstimmen, die im `identifierUris`-Element des Anwendungsregistrierungsmanifests konfiguriert wurden. Wenn der `issuer`-Name der SAML-Anforderung im `identifierUris`-Element nicht vorhanden ist, [fügen Sie ihn im Anwendungsregistrierungsmanifest hinzu](#add-the-identifier). Beispiel: `https://contoso.onmicrosoft.com/app-name`. 
* **Anmelde-URL/SAML-Endpunkt/SAML-URL:** Überprüfen Sie den Wert in der Metadatendatei der Azure AD B2C-SAML-Richtlinie für das XML-Element `<SingleSignOnService>`.
* **Zertifikat**: Dieses Zertifikat ist *B2C_1A_SamlIdpCert*, aber ohne den privaten Schlüssel. So rufen Sie den öffentlichen Schlüssel des Zertifikats ab:

    1. Navigieren Sie zur Metadaten-URL, die oben angegeben wurde.
    1. Kopieren Sie den Wert in das `<X509Certificate>`-Element.
    1. Fügen Sie ihn in eine Textdatei ein.
    1. Speichern Sie die Textdatei als *CER*-Datei.

### <a name="test-with-the-saml-test-app"></a>Testen der Konfiguration mit der SAML-Test-App

Sie können unsere [SAML-Testanwendung][samltest] verwenden, um Ihre Konfiguration zu testen:

* Aktualisieren Sie den Mandantennamen.
* Aktualisieren Sie den Richtliniennamen. Verwenden Sie z. B. *B2C_1A_signup_signin_saml*.
* Geben Sie den Aussteller-URI an. Verwenden Sie einen der URIs, die im `identifierUris`-Element des Anwendungsregistrierungsmanifests enthalten sind. Verwenden Sie z. B. `https://contoso.onmicrosoft.com/app-name`.

Wählen Sie **Login** (Anmelden) aus. Ein Bildschirm für die Benutzeranmeldung sollte angezeigt werden. Nach der Anmeldung wird eine SAML-Antwort an die Beispielanwendung zurückgegeben.

## <a name="supported-and-unsupported-saml-modalities"></a>Unterstützte und nicht unterstützte SAML-Modalitäten

Die folgenden SAML-Anwendungsszenarien werden über Ihren eigenen Metadatenendpunkt unterstützt:

* Angeben mehrerer Abmelde-URLs oder POST-Bindungen für die Abmelde-URL im Anwendungs- oder Dienstprinzipalobjekt
* Angeben eines Signaturschlüssels zum Überprüfen von Anforderungen der vertrauenden Seite im Anwendungs- oder Dienstprinzipalobjekt
* Angeben eines Tokenverschlüsselungsschlüssels im Anwendungs- oder Dienstprinzipalobjekt
* Angeben der vom Identitätsanbieter initiierten Anmeldung, bei der Azure AD B2C der Identitätsanbieter ist

## <a name="next-steps"></a>Nächste Schritte

- Rufen Sie die SAML-Test-Web-App im [GitHub-Repository der Community in Azure AD B2C](https://github.com/azure-ad-b2c/saml-sp-tester) ab.
- Informieren Sie sich über die [Optionen zum Registrieren einer SAML-Anwendung in Azure AD B2C](saml-service-provider-options.md).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
