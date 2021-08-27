---
title: Konfigurieren von Azure Active Directory B2C als SAML-Identitätsanbieter für Ihre Anwendungen
title-suffix: Azure Active Directory B2C
description: Hier erfahren Sie, wie Sie Azure Active Directory B2C für die Bereitstellung von SAML-Protokollassertionen für Ihre Anwendungen (Dienstanbieter) konfigurieren. Azure AD B2C fungiert als einzelner Identitätsanbieter (Identity Provider, IdP) für Ihre SAML-Anwendung.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/30/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 96a7cbbc1e872a9b84644cf64405e7b6d2bb7bf2
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2021
ms.locfileid: "122639653"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registrieren einer SAML-Anwendung in Azure AD B2C

In diesem Artikel erfahren Sie, wie Sie Ihre Security Assertion Markup Language-Anwendungen (SAML-Anwendungen) (Dienstanbieter) mit Azure Active Directory B2C (Azure AD B2C) für die Authentifizierung verbinden.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>Übersicht

Organisationen, die Azure AD B2C als Lösung für die Identitäts- und Zugriffsverwaltung von Kunden nutzen, müssen möglicherweise Anwendungen integrieren, die das SAML-Protokoll für die Authentifizierung verwenden. Im folgenden Diagramm wird dargestellt, wie Azure AD B2C als *Identitätsanbieter* (Identity Provider, IdP) fungiert, um einmaliges Anmelden (Single Sign-On, SSO) mit SAML-basierten Anwendungen zu erreichen.

![Diagramm mit B2C als Identitätsanbieter auf der linken Seite und B2C als Dienstanbieter auf der rechten Seite.](media/saml-service-provider/saml-service-provider-integration.png)

1. Die Anwendung erstellt eine SAML-AuthN-Anforderung, die an den SAML-Anmeldungsendpunkt von Azure AD B2C gesendet wird.
2. Der Benutzer kann ein lokales Azure AD B2C-Konto oder einen anderen Verbundidentitätsanbieter (sofern konfiguriert) für die Authentifizierung verwenden.
3. Wenn sich der Benutzer mit einem Verbundidentitätsanbieter anmeldet, wird eine Tokenantwort an Azure AD B2C gesendet.
4. Azure AD B2C generiert eine SAML-Assertion und sendet sie an die Anwendung.

## <a name="prerequisites"></a>Voraussetzungen

* Führen Sie die Schritte unter [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy) aus. Sie benötigen die benutzerdefinierte Richtlinie *SocialAndLocalAccounts* aus dem Starter Pack für benutzerdefinierte Richtlinien, die in diesem Artikel erläutert wird.
* Grundlegendes Verständnis des SAML-Protokolls und Vertrautheit mit der SAML-Implementierung der Anwendung.
* Eine als SAML-Anwendung konfigurierte Webanwendung. Für dieses Tutorial können Sie eine [SAML-Testanwendung][samltest] verwenden, die wir bereitstellen.

## <a name="components"></a>Komponenten

Für dieses Szenario sind drei Hauptkomponenten erforderlich:

* Eine SAML-**Anwendung**, die SAML-AuthN-Anforderungen senden und SAML-Antworten von Azure AD B2C empfangen, decodieren und überprüfen kann. Die SAML-Anwendung wird auch als Anwendung der vertrauenden Seite oder als Dienstanbieter bezeichnet.
* Der öffentlich verfügbare SAML-**Metadatenendpunkt** oder das XML-Dokument der SAML-Anwendung.
* Ein [Azure AD B2C-Mandant](tutorial-create-tenant.md)

Wenn Sie noch nicht über eine SAML-Anwendung und einen zugehörigen Metadatenendpunkt verfügen, können Sie diese SAML-Beispielanwendung verwenden, die wir zum Testen zur Verfügung stellen:

[SAML-Testanwendung][samltest]

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="set-up-certificates"></a>Einrichten von Zertifikaten

Damit eine Vertrauensstellung zwischen Ihrer Anwendung und Azure AD B2C hergestellt werden kann, müssen beide Dienste die Signaturen des jeweils anderen erstellen und überprüfen können. Sie konfigurieren ein X509-Zertifikat in Azure AD B2C und in Ihrer Anwendung.

**Anwendungszertifikate**

| Verwendung | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| SAML-Anforderungssignatur  | Nein | Ein Zertifikat mit einem privaten Schlüssel, das in Ihrer Web-App gespeichert ist, wird von Ihrer Anwendung verwendet, um an Azure AD B2C gesendete SAML-Anforderungen zu signieren. Die Web-App muss den öffentlichen Schlüssel über den SAML-Metadatenendpunkt verfügbar machen. Azure AD B2C überprüft die SAML-Anforderungssignatur mithilfe des öffentlichen Schlüssels aus den Anwendungsmetadaten.|
| Verschlüsselung von SAML-Assertionen  | Nein | Ein in Ihrer Web-App gespeichertes Zertifikat mit einem privaten Schlüssel. Die Web-App muss den öffentlichen Schlüssel über den SAML-Metadatenendpunkt verfügbar machen. Azure AD B2C kann Assertionen für Ihre Anwendung mithilfe des öffentlichen Schlüssels verschlüsseln. Die Anwendung verwendet den privaten Schlüssel, um die Assertion zu entschlüsseln.|

**Azure AD B2C-Zertifikate**

| Verwendung | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| SAML-Antwortsignatur | Ja  | Ein in Azure AD B2C gespeichertes Zertifikat mit einem privaten Schlüssel. Dieses Zertifikat wird von Azure AD B2C zum Signieren der SAML-Antwort verwendet, die an Ihre Anwendung gesendet wird. Ihre Anwendung liest den öffentlichen Azure AD B2C-Metadatenschlüssel, um die Signatur der SAML-Antwort zu überprüfen. |
| Signieren von SAML-Assertionen | Ja | Ein in Azure AD B2C gespeichertes Zertifikat mit einem privaten Schlüssel. Dieses Zertifikat wird von Azure AD B2C zum Signieren der Assertion der SAML-Antwort verwendet. Der Teil `<saml:Assertion>` der SAML-Antwort.  |

In einer Produktionsumgebung empfiehlt sich die Verwendung von Zertifikaten, die von einer öffentlichen Zertifizierungsstelle ausgestellt wurden. Sie können dieses Verfahren jedoch auch mit selbstsignierten Zertifikaten ausführen.

### <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Erstellen Sie ein SAML-Antwortsignaturzertifikat, um eine Vertrauensstellung zwischen Ihrer Anwendung und Azure AD B2C einzurichten. Dieses Zertifikat wird von Azure AD B2C zum Signieren der SAML-Antwort verwendet, die an Ihre Anwendung gesendet wird. Ihre Anwendung liest den öffentlichen Azure AD B2C-Metadatenschlüssel, um die Signatur der SAML-Antwort zu überprüfen. 

> [!TIP]
> Sie können den in diesem Abschnitt erstellten Richtlinienschlüssel für andere Zwecke verwenden, etwa für das Anmelden der [SAML-Assertion](saml-service-provider-options.md#saml-assertions-signature). 

### <a name="obtain-a-certificate"></a>Beschaffung eines Zertifikats

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>Hochladen des Zertifikats

Sie müssen Ihr Zertifikat in Ihrem Azure AD B2C-Mandanten speichern.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Mandanten enthält.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Klicken Sie unter **Optionen** auf `Upload`.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `SamlIdpCert`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
1. Navigieren Sie zur PFX-Datei Ihres Zertifikats mit dem privaten Schlüssel, und wählen Sie sie aus.
1. Klicken Sie auf **Erstellen**.

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>Aktivieren Ihrer Richtlinie für das Herstellen einer Verbindung mit einer SAML-Anwendung

Damit eine Verbindung mit Ihrer SAML-Anwendung hergestellt werden kann, muss Azure AD B2C SAML-Antworten erstellen können.

Öffnen Sie `SocialAndLocalAccounts\`**`TrustFrameworkExtensions.xml`** im Starter Pack für benutzerdefinierte Richtlinien.

Suchen Sie den Abschnitt `<ClaimsProviders>`, und fügen Sie den folgenden XML-Codeausschnitt hinzu, um Ihren SAML-Antwortgenerator zu implementieren.

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

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="configure-the-issueruri-of-the-saml-response"></a>Konfigurieren des IssuerUri-Elements der SAML-Antwort

Sie können den Wert des `IssuerUri`-Metadatenelements im technischen Profil des SAML-Tokenausstellers ändern. Diese Änderung wird in dem `issuerUri`-Attribut berücksichtigt, das in der SAML-Antwort von Azure AD B2C zurückgegeben wird. Ihre Anwendung muss so konfiguriert werden, dass sie diesen `issuerUri`-Wert während der Überprüfung der SAML-Antwort akzeptiert.

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

1. Erstellen Sie eine Kopie der Datei *SignUpOrSignin.xml* in Ihrem Starter Pack-Arbeitsverzeichnis, und speichern Sie diese unter einem neuen Namen. Beispielsweise unter dem Namen *SignUpOrSigninSAML.xml*. Bei dieser Datei handelt es sich um die Richtliniendatei der vertrauenden Seite. Sie ist so konfiguriert, dass standardmäßig eine JWT-Antwort ausgegeben wird.

1. Öffnen Sie die Datei *SignUpOrSigninSAML.xml* in Ihrem bevorzugten Editor.

1. Ändern Sie `PolicyId` und `PublicPolicyUri` der Richtlinie in _B2C_1A_signup_signin_saml_ und `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml` wie unten gezeigt.

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

1. Am Ende der User Journey enthält Azure AD B2C einen `SendClaims`-Schritt. Dieser Schritt verweist auf das technische Profil des Tokenausstellers. Wenn statt der standardmäßigen JWT-Antwort eine SAML-Antwort ausgegeben werden soll, ändern Sie den Schritt `SendClaims` so, dass auf das technische Profil des neuen SAML-Tokenausstellers (`Saml2AssertionIssuer`) verwiesen wird.

Fügen Sie den folgenden XML-Codeausschnitt direkt vor dem `<RelyingParty>`-Element hinzu. Dieser XML-Code überschreibt den Orchestrierungsschritt Nummer 7 in der User Journey _SignUpOrSignIn_. Wenn Sie aus einem anderen Ordner im Starter Pack gestartet sind oder die User Journey durch Hinzufügen oder Entfernen von Orchestrierungsschritten angepasst haben, stellen Sie sicher, dass die Schrittnummer im `order`-Element dem in der User Journey für den Tokenausstellerschritt angegebenen Wert entspricht. In den anderen Starter Pack-Ordnern wird beispielsweise Schrittnummer 4 für `LocalAccounts`, 6 für `SocialAccounts` und 9 für `SocialAndLocalAccountsWithMfa` verwendet.

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

Die endgültige Richtliniendatei der vertrauenden Seite sollte wie im folgenden XML-Code aussehen:

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

Speichern Sie die Änderungen, und laden Sie die neuen Richtliniendateien **TrustFrameworkExtensions.xml** und **SignUpOrSigninSAML.xml** in das Azure-Portal hoch.

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>Testen der SAML-IdP-Metadaten von Azure AD B2C

Nachdem die Richtliniendateien hochgeladen wurden, generiert Azure AD B2C das von der Anwendung zu verwendende SAML-Metadatendokument des Identitätsanbieters anhand der Konfigurationsinformationen. Das SAML-Metadatendokument enthält die Speicherorte von Diensten, z. B. Anmelde- und Abmeldemethoden, Zertifikate usw.

Die Azure AD B2C-Richtlinienmetadaten sind unter der folgenden URL verfügbar:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

Ersetzen Sie `<tenant-name>` durch den Namen Ihres Azure AD B2C-Mandanten und `<policy-name>` durch den Namen (ID) der Richtlinie. Beispiel:

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>Registrieren Ihrer SAML-Anwendung in Azure AD B2C

Damit Azure AD B2C Ihre Anwendung als vertrauenswürdig einstuft, müssen Sie eine Azure AD B2C-Anwendungsregistrierung erstellen, die Konfigurationsinformationen wie den Metadatenendpunkt der Anwendung enthält.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein. Beispielsweise *SAMLApp1*.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Nur Konten in diesem Organisationsverzeichnis** aus.
1. Wählen Sie unter **Umleitungs-URIs** die Option **Web** aus, und geben Sie dann `https://localhost` ein. Diesen Wert ändern Sie später im Manifest der Anwendungsregistrierung.
1. Wählen Sie **Registrieren**.

### <a name="configure-your-application-in-azure-ad-b2c"></a>Konfigurieren Ihrer Anwendung in Azure AD B2C

Für SAML-Apps müssen Sie im Manifest der Anwendungsregistrierung mehrere Eigenschaften konfigurieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu der Anwendungsregistrierung, die Sie im vorherigen Abschnitt erstellt haben.
1. Wählen Sie unter **Verwalten** die Option **Manifest** aus, um den Manifest-Editor zu öffnen, und ändern Sie dann die Eigenschaften, die in den folgenden Abschnitten beschrieben werden.

#### <a name="add-the-identifier"></a>Hinzufügen des Bezeichners

Wenn Ihre SAML-Anwendung eine Anforderung an Azure AD B2C sendet, enthält die SAML-AuthN-Anforderung ein `Issuer`-Attribut, dessen Wert in der Regel dem Wert der Metadaten-`entityID` der Anwendung entspricht. Azure AD B2C verwendet diesen Wert, um die Anwendungsregistrierung im Verzeichnis zu suchen und die Konfiguration zu lesen. Damit diese Suche erfolgreich ist, muss der `identifierUri` in der Anwendungsregistrierung mit einem Wert aufgefüllt werden, der mit dem `Issuer`-Attribut übereinstimmt.

Suchen Sie im Registrierungsmanifest den Parameter `identifierURIs`, und fügen Sie den entsprechenden Wert hinzu. Dieser Wert entspricht dem Wert, der in den SAML-AuthN-Anforderungen für „EntityId“ bei der Anwendung konfiguriert ist, und dem `entityID`-Wert in den Metadaten der Anwendung.

Im folgenden Beispiel sehen Sie die `entityID` in den SAML-Metadaten:

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

Die `identifierUris`-Eigenschaft akzeptiert nur URLs in der Domäne `tenant-name.onmicrosoft.com`.

```json
"identifierUris":"https://samltestapp2.azurewebsites.net",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>Freigeben der Metadaten der Anwendung für Azure AD B2C

Nachdem die Anwendungsregistrierung vom zugehörigen `identifierUri` geladen wurde, verwendet Azure AD B2C die Metadaten der Anwendung, um die SAML-AuthN-Anforderung zu überprüfen und die entsprechende Antwort zu bestimmen.

Es empfiehlt sich, dass Ihre Anwendung einen öffentlich zugänglichen Metadatenendpunkt verfügbar macht.

Wenn Eigenschaften *sowohl* in der SAML-Metadaten-URL als auch im Manifest der Anwendungsregistrierung angegeben sind, werden sie *zusammengeführt*. Die in der Metadaten-URL angegebenen Eigenschaften werden zuerst verarbeitet und besitzen Vorrang.

Wenn Sie die SAML-Testanwendung als Beispiel verwenden, würden Sie im Anwendungsmanifest für `samlMetadataUrl` den folgenden Wert angeben:

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>Überschreiben oder Festlegen der Assertionsverbraucher-URL (optional)

Sie können die Antwort-URL konfigurieren, an die Azure AD B2C SAML-Antworten sendet. Antwort-URLs können im Anwendungsmanifest konfiguriert werden. Diese Konfiguration ist sinnvoll, wenn Ihre Anwendung keinen öffentlich zugänglichen Metadatenendpunkt verfügbar macht.

Die Antwort-URL für eine SAML-Anwendung ist der Endpunkt, an dem die Anwendung den Empfang von SAML-Antworten erwartet. Die Anwendung stellt diese URL in der Regel im Metadatendokument unter dem `AssertionConsumerServiceUrl`-Attribut bereit, wie unten dargestellt:

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

Wenn Sie die im `AssertionConsumerServiceUrl`-Attribut angegebenen Metadaten überschreiben möchten oder die URL nicht im Metadatendokument vorhanden ist, können Sie die URL im Manifest unter der `replyUrlsWithType`-Eigenschaft konfigurieren. Der `BindingType` wird auf `HTTP POST` festgelegt.

Wenn Sie die SAML-Testanwendung als Beispiel verwenden, würden Sie die `url`-Eigenschaft von `replyUrlsWithType` auf den im folgenden JSON-Codeausschnitt angegebenen Wert festlegen.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>Überschreiben oder Festlegen der Abmelde-URL (optional)

Sie können die Abmelde-URL konfigurieren, an die Azure AD B2C den Benutzer nach einer Abmeldeanforderung weiterleitet. Antwort-URLs können im Anwendungsmanifest konfiguriert werden.

Wenn Sie die im `SingleLogoutService`-Attribut angegebenen Metadaten überschreiben möchten oder die URL nicht im Metadatendokument vorhanden ist, können Sie diese im Manifest unter der `Logout`-Eigenschaft konfigurieren. Der `BindingType` wird auf `Http-Redirect` festgelegt.

Die Anwendung stellt diese URL in der Regel im Metadatendokument unter dem `AssertionConsumerServiceUrl`-Attribut bereit, wie unten dargestellt:

```xml
<IDPSSODescriptor WantAuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</IDPSSODescriptor>
```

Wenn Sie die SAML-Testanwendung als Beispiel verwenden, bleibt `logoutUrl` auf `https://samltestapp2.azurewebsites.net/logout` festgelegt:

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> Wenn Sie die Antwort-URL und die Abmelde-URL im Anwendungsmanifest konfigurieren, ohne den Metadatenendpunkt der Anwendung über die `samlMetadataUrl`-Eigenschaft aufzufüllen, wird von Azure AD B2C weder die SAML-Anforderungssignatur überprüft noch die SAML-Antwort verschlüsselt.

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>Konfigurieren von Azure AD B2C als SAML-IdP in Ihrer SAML-Anwendung

Der letzte Schritt besteht darin, Azure AD B2C als SAML-IdP in Ihrer SAML-Anwendung zu aktivieren. Jede Anwendung ist anders, und daher sind auch die auszuführenden Schritte unterschiedlich. Weitere Informationen finden Sie in der Dokumentation zu Ihrer App.

Die Metadaten können in Ihrer Anwendung als *statische Metadaten* oder als *dynamische Metadaten* konfiguriert werden. Im statischen Modus kopieren Sie alle oder einen Teil der Metadaten aus den Metadaten der Azure AD B2C-Richtlinie. Im dynamischen Modus stellen Sie die URL für die Metadaten bereit, damit Ihre Anwendung die Metadaten dynamisch lesen kann.

In der Regel sind einige oder alle der folgenden Angaben erforderlich:

* **Metadaten**: Verwenden Sie das Format `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata`.
* **Aussteller:**  Der `issuer`-Wert der SAML-Anforderung muss mit einem der URIs übereinstimmen, die im Element `identifierUris` des Anwendungsregistrierungsmanifests konfiguriert wurden. Wenn der Name `issuer` der SAML-Anforderung im Element `identifierUris` nicht vorhanden ist, [fügen Sie ihn zum Anwendungsregistrierungsmanifest hinzu](#add-the-identifier). Beispiel: `https://contoso.onmicrosoft.com/app-name`. 
* **Anmelde-URL/SAML-Endpunkt/SAML-URL**: Überprüfen Sie den Wert in der Metadatendatei der Azure AD B2C-SAML-Richtlinie für das XML-Element `<SingleSignOnService>`.
* **Zertifikat**: Dieses Zertifikat ist *B2C_1A_SamlIdpCert*, aber ohne den privaten Schlüssel. So rufen Sie den öffentlichen Schlüssel des Zertifikats ab:

    1. Navigieren Sie zur Metadaten-URL, die oben angegeben wurde.
    1. Kopieren Sie den Wert in das `<X509Certificate>`-Element.
    1. Fügen Sie ihn in eine Textdatei ein.
    1. Speichern Sie die Textdatei als *CER*-Datei.

### <a name="test-with-the-saml-test-app"></a>Testen der Konfiguration mit der SAML-Test-App

Sie können unsere [SAML-Testanwendung][samltest] verwenden, um Ihre Konfiguration zu testen:

* Aktualisieren Sie den Mandantennamen.
* Aktualisieren Sie den Richtliniennamen, z. B. *B2C_1A_signup_signin_saml*.
* Geben Sie den Aussteller-URI an. Verwenden Sie einen der URIs, die im Element `identifierUris` des Anwendungsregistrierungsmanifests gefunden werden, z. B. `https://contoso.onmicrosoft.com/app-name`.

Wählen Sie **Login** (Anmelden) aus. Ein Bildschirm für die Benutzeranmeldung sollte angezeigt werden. Bei der Anmeldung wird eine SAML-Antwort an die Beispielanwendung zurückgegeben.

## <a name="supported-and-unsupported-saml-modalities"></a>Unterstützte und nicht unterstützte SAML-Modalitäten

Die folgenden SAML-Anwendungsszenarien werden über Ihren eigenen Metadatenendpunkt unterstützt:

* Mehrere Abmelde-URLs oder POST-Bindung für die Abmelde-URL im Anwendungs-/Dienstprinzipalobjekt.
* Angeben eines Signaturschlüssels zum Überprüfen von Anforderungen der vertrauenden Seite (Relying Party, RP) im Anwendungs-/Dienstprinzipalobjekt.
* Angeben eines Tokenverschlüsselungsschlüssels im Anwendungs-/Dienstprinzipalobjekt.
* Vom Identitätsanbieter initiierte Anmeldung, bei der Azure AD B2C der Identitätsanbieter ist.

## <a name="next-steps"></a>Nächste Schritte

- Rufen Sie die SAML-Test-Web-App im [Repository der Azure AD B2C-GitHub-Community](https://github.com/azure-ad-b2c/saml-sp-tester) ab.
- Informieren Sie sich über die [Optionen zum Registrieren einer SAML-Anwendung in Azure AD B2C](saml-service-provider-options.md).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
