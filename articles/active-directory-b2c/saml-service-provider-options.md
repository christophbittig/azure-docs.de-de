---
title: Konfigurieren der Optionen für SAML-Dienstanbieter
title-suffix: Azure Active Directory B2C
description: Hier finden Sie Informationen zum Konfigurieren der Optionen für SAML-Dienstanbieter in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 51672c7757e5c747bf2b243e32506159a468f2d5
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222151"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>Optionen zum Registrieren einer SAML-Anwendung in Azure AD B2C

In diesem Artikel werden die Konfigurationsoptionen beschrieben, die verfügbar sind, wenn Sie Azure Active Directory B2C (Azure AD B2C) mit Ihrer Security Assertion Markup Language-Anwendung (SAML-Anwendung) verbinden.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="specify-a-saml-response-signature"></a>Angeben einer SAML-Antwortsignatur

Sie können ein Zertifikat angeben, das zum Signieren der SAML-Nachrichten verwendet werden soll. Die Nachricht ist das `<samlp:Response>`-Element in der SAML-Antwort, die an die Anwendung gesendet wird.

Wenn Sie noch nicht über einen Richtlinienschlüssel verfügen, [erstellen Sie einen](saml-service-provider.md#create-a-policy-key). Konfigurieren Sie dann im technischen Profil des SAML-Tokenausstellers das Metadatenelement `SamlMessageSigning`. `StorageReferenceId` muss auf den Namen des Richtlinienschlüssels verweisen.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

### <a name="signature-algorithm"></a>Signaturalgorithmus

Sie können den zum Signieren der SAML-Assertion verwendeten Signaturalgorithmus konfigurieren. Mögliche Werte sind `Sha256`, `Sha384`, `Sha512` oder `Sha1`. Stellen Sie sicher, dass für das technische Profil und die Anwendung der gleiche Signaturalgorithmus genutzt wird. Verwenden Sie nur den Algorithmus, den Ihr Zertifikat unterstützt.

Konfigurieren Sie den Signaturalgorithmus mithilfe des Metadatenschlüssels `XmlSignatureAlgorithm` im `Metadata`-Element der vertrauenden Seite.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="XmlSignatureAlgorithm">Sha256</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="check-the-saml-assertion-signature"></a>Überprüfen der SAML-Assertionssignatur

Wenn Ihre Anwendung erwartet, dass der SAML-Assertionsabschnitt signiert ist, stellen Sie sicher, dass der SAML-Dienstanbieter das `WantAssertionsSigned`-Element auf `true` festgelegt hat. Wenn das Element auf `false` festgelegt oder nicht vorhanden ist, wird der Assertionsabschnitt nicht signiert. 

Im folgenden Beispiel sind die Metadaten für einen SAML-Dienstanbieter dargestellt, in denen `WantAssertionsSigned` auf `true` festgelegt ist.

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
  <SPSSODescriptor  WantAssertionsSigned="true" AuthnRequestsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  </SPSSODescriptor>
</EntityDescriptor>
```  

### <a name="signature-certificate"></a>Signaturzertifikat

Ihre Richtlinie muss ein Zertifikat angeben, das zum Signieren des SAML-Assertionsabschnitts der SAML-Antwort verwendet werden soll. Wenn Sie noch nicht über einen Richtlinienschlüssel verfügen, [erstellen Sie einen](saml-service-provider.md#create-a-policy-key). Konfigurieren Sie dann im technischen Profil des SAML-Tokenausstellers das Metadatenelement `SamlAssertionSigning`. `StorageReferenceId` muss auf den Namen des Richtlinienschlüssels verweisen.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

## <a name="enable-encryption-in-saml-assertions"></a>Aktivieren der Verschlüsselung in SAML-Assertionen

Wenn Ihre Anwendung erwartet, dass SAML-Assertionen in einem verschlüsselten Format vorliegen, stellen Sie sicher, dass in der Azure AD B2C-Richtlinie die Verschlüsselung aktiviert ist.

In Azure AD B2C wird das auf einem öffentlichen Schlüssel basierende Zertifikat des Dienstanbieters verwendet, um die SAML-Assertion zu verschlüsseln. Der öffentliche Schlüssel muss auf dem Metadatenendpunkt der SAML-Anwendung vorhanden sein, und der Wert `KeyDescriptor` `use` muss auf `Encryption` festgelegt werden, wie im folgenden Beispiel dargestellt:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Legen Sie im [technischen Profil der vertrauenden Seite](relyingparty.md#technicalprofile) das Metadatenelement `WantsEncryptedAssertion` auf `true` fest, um Azure AD B2C das Senden von verschlüsselten Assertionen zu ermöglichen. Sie können auch den zum Verschlüsseln der SAML-Assertion verwendeten Algorithmus konfigurieren.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

### <a name="encryption-method"></a>Verschlüsselungsmethode

Um die zum Verschlüsseln der SAML-Assertionsdaten verwendete Verschlüsselungsmethode zu konfigurieren, legen Sie im technischen Profil der vertrauenden Seite den Metadatenschlüssel `DataEncryptionMethod` fest. Mögliche Werte sind `Aes256` (Standard), `Aes192`, `Sha512` oder `Aes128`. Die Metadaten bestimmen den Wert des `<EncryptedData>`-Elements in der SAML-Antwort.

Um die Methode für die Verschlüsselung der Kopie des Schlüssels zu konfigurieren, der zum Verschlüsseln der SAML-Assertionsdaten verwendet wurde, legen Sie im technischen Profil der vertrauenden Seite den Metadatenschlüssel `KeyEncryptionMethod` fest. Mögliche Werte:

- `Rsa15` (Standardwert): Algorithmus RSA Public Key Cryptography Standard (PKCS), Version 1.5.
- `RsaOaep`: Verschlüsselungsalgorithmus RSA Optimal Asymmetric Encryption Padding (OAEP).  

Die Metadaten bestimmen den Wert des `<EncryptedKey>`-Elements in der SAML-Antwort.

Im folgenden Beispiel wird der Abschnitt `EncryptedAssertion` einer SAML-Assertion veranschaulicht. Die Methode für die Datenverschlüsselung lautet `Aes128`, und als Methode für den verschlüsselten Schlüssel wird `Rsa15` verwendet.

```xml
<saml:EncryptedAssertion>
  <xenc:EncryptedData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#"
    xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" Type="http://www.w3.org/2001/04/xmlenc#Element">
    <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes128-cbc" />
    <dsig:KeyInfo>
      <xenc:EncryptedKey>
        <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#rsa-1_5" />
        <xenc:CipherData>
          <xenc:CipherValue>...</xenc:CipherValue>
        </xenc:CipherData>
      </xenc:EncryptedKey>
    </dsig:KeyInfo>
    <xenc:CipherData>
      <xenc:CipherValue>...</xenc:CipherValue>
    </xenc:CipherData>
  </xenc:EncryptedData>
</saml:EncryptedAssertion>
```

Sie können das Format der verschlüsselten Assertionen ändern. Legen Sie den Metadatenschlüssel `UseDetachedKeys` auf der vertrauenden Seite fest, um das Verschlüsselungsformat zu konfigurieren. Mögliche Werte: `true` und `false` (Standardwert). Wenn der Wert auf `true` festgelegt ist, wird die verschlüsselte Assertion von den getrennten Schlüsseln als untergeordnetes Element von `EncryptedAssertion` (anstelle von `EncryptedData`) hinzugefügt.

Konfigurieren Sie die Verschlüsselungsmethode und das Format mithilfe der Metadatenschlüssel im [technischen Profil der vertrauenden Seite](relyingparty.md#technicalprofile):

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="DataEncryptionMethod">Aes128</Item>
      <Item Key="KeyEncryptionMethod">Rsa15</Item>
      <Item Key="UseDetachedKeys">false</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="configure-idp-initiated-flow"></a>Konfigurieren des IdP-initiierten Flows

Wenn Ihre Anwendung eine SAML-Assertion erwartet, ohne dass zuerst eine SAML-Authentifizierungsanforderung an den Identitätsanbieter (Identity Provider, IdP) gesendet wird, müssen Sie Azure AD B2C für den IdP-initiierten Flow konfigurieren.

Im IdP-initiierten Flow wird der Anmeldevorgang vom Identitätsanbieter (Azure AD B2C) gestartet. Der Identitätsanbieter sendet eine nicht angeforderte SAML-Antwort an den Dienstanbieter (Ihre Anwendung der vertrauenden Seite).

Wir unterstützen derzeit keine Szenarien, in denen der initiierende Identitätsanbieter ein externer Identitätsanbieter im Verbund mit Azure AD B2C ist, wie z. B. [Active Directory-Verbunddienste (AD FS)](identity-provider-adfs.md) oder [Salesforce](identity-provider-salesforce-saml.md). Der IdP-initiierte Flow wird nur für die Authentifizierung lokaler Konten in Azure AD B2C unterstützt.

Um den IdP-initiierten Flow zu aktivieren, legen Sie im [technischen Profil der vertrauenden Seite](relyingparty.md#technicalprofile) das Metadatenelement `IdpInitiatedProfileEnabled` auf `true` fest.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

Um einen Benutzer über den IdP-initiierten Flow anzumelden oder zu registrieren, verwenden Sie die folgende URL:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

Ersetzen Sie die folgenden Werte:

* Ersetzen Sie `<tenant-name>` durch den Namen Ihres Mandanten.
* Ersetzen Sie `<policy-name>` durch den Namen Ihrer SAML-Richtlinie für die vertrauende Seite.
* Ersetzen Sie `app-identifier-uri` durch den `identifierUris`-Wert in der Metadatendatei, z. B. `https://contoso.onmicrosoft.com/app-name`.

### <a name="sample-policy"></a>Beispielrichtlinie

Sie können eine vollständige Beispielrichtlinie zum Testen mit der SAML-Test-App verwenden:

1. Laden Sie die [Anmeldebeispielrichtlinie „SAML-SP-initiated“](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated) herunter.
1. Geben Sie für `TenantId` den Namen Ihres Mandanten an. In diesem Artikel wird das Beispiel *contoso.b2clogin.com* verwendet.
1. Behalten Sie den Richtliniennamen *B2C_1A_signup_signin_saml* bei.

## <a name="configure-the-saml-response-lifetime"></a>Konfigurieren der Gültigkeitsdauer der SAML-Antwort

Sie können festlegen, wie lange die SAML-Antwort gültig bleiben soll. Legen Sie die Gültigkeitsdauer im technischen Profil des SAML-Tokenausstellers mit dem Metadatenelement `TokenLifeTimeInSeconds` fest. Mit diesem Wert wird die Anzahl von Sekunden angegeben, die ab dem bei der Tokenausstellung berechneten Zeitstempel `NotBefore` verstreichen können. Die Standardgültigkeitsdauer beträgt 300 Sekunden (5 Minuten).

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenLifeTimeInSeconds">400</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="configure-the-time-skew-of-a-saml-response"></a>Konfigurieren der Zeitabweichung einer SAML-Antwort

Sie können die Zeitabweichung konfigurieren, die auf den Zeitstempel `NotBefore` der SAML-Antwort angewendet wird. Mit dieser Konfiguration wird sichergestellt, dass die SAML-Assertion auch bei nicht synchronen Zeiten zwischen zwei Plattformen als gültig angesehen wird, wenn sie innerhalb dieser Zeitabweichung liegt.

Legen Sie die Zeitabweichung mithilfe des Metadatenelements `TokenNotBeforeSkewInSeconds` im technischen Profil des SAML-Tokenausstellers fest. Der Wert für die Abweichung wird in Sekunden angegeben (Standardwert: 0). Der Höchstwert ist 3.600 (eine Stunde).

Wenn `TokenNotBeforeSkewInSeconds` beispielsweise auf `120` Sekunden festgelegt ist, bedeutet dies Folgendes:

- Das Token wird um 13:05:10 UTC ausgestellt.
- Das Token ist ab 13:03:10 UTC gültig.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenNotBeforeSkewInSeconds">120</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="remove-milliseconds-from-the-date-and-time"></a>Entfernen von Millisekunden aus Datums- und Uhrzeitangaben

Sie können angeben, ob Millisekunden in der SAML-Antwort aus Datums- und Uhrzeitwerten entfernt werden sollen. (Zu diesen Werten gehören `IssueInstant`, `NotBefore`, `NotOnOrAfter` und `AuthnInstant`.) Legen Sie zum Entfernen der Millisekunden den Metadatenschlüssel `RemoveMillisecondsFromDateTime` im technischen Profil der vertrauenden Seite fest. Mögliche Werte: `false` (Standard) oder `true`.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="RemoveMillisecondsFromDateTime">true</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="use-an-issuer-id-to-override-an-issuer-uri"></a>Verwenden einer Aussteller-ID zum Überschreiben eines Aussteller-URIs

Wenn Sie mehrere SAML-Anwendungen verwenden, die von unterschiedlichen `entityID`-Werten abhängen, können Sie den Wert `IssuerUri` in der Datei der vertrauenden Seite überschreiben. Kopieren Sie zum Überschreiben des Aussteller-URIs das technische Profil mit der `Saml2AssertionIssuer`-ID aus der Basisdatei, und überschreiben Sie den `IssuerUri`-Wert.

> [!TIP]
> Kopieren Sie den Abschnitt `<ClaimsProviders>` aus der Basisdatei, und bewahren Sie diese Elemente im Anspruchsanbieter auf: `<DisplayName>Token Issuer</DisplayName>`, `<TechnicalProfile Id="Saml2AssertionIssuer">` und `<DisplayName>Token Issuer</DisplayName>`.
 
Beispiel:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="manage-a-session"></a>Verwalten einer Sitzung

Sie können die Sitzung zwischen Azure AD B2C und der SAML-Anwendung der vertrauenden Seite mit dem Element `UseTechnicalProfileForSessionManagement` und dem [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) verwalten.

## <a name="force-users-to-reauthenticate"></a>Erzwingen der erneuten Authentifizierung von Benutzern 

Um die erneute Authentifizierung von Benutzern zu erzwingen, kann die Anwendung das `ForceAuthn`-Attribut in die SAML-Authentifizierungsanforderung einschließen. Das `ForceAuthn`-Attribut ist ein boolescher Wert. Wenn das Attribut auf `true` festgelegt ist, wird die Benutzersitzung bei Azure AD B2C ungültig, und der Benutzer wird gezwungen, sich erneut zu authentifizieren. 

Aus der folgenden SAML-Authentifizierungsanforderung geht hervor, wie das `ForceAuthn`-Attribut auf `true` festgelegt wird. 

```xml
<samlp:AuthnRequest 
       Destination="https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_SAML2_signup_signin/samlp/sso/login"
       ForceAuthn="true" ...>
    ...
</samlp:AuthnRequest>
```

## <a name="sign-the-azure-ad-b2c-idp-saml-metadata"></a>Signieren der SAML-IdP-Metadaten von Azure AD B2C

Sie können Azure AD B2C anweisen, das zugehörige Metadatendokument für den SAML-Identitätsanbieter zu signieren, wenn die Anwendung dies erfordert. Wenn Sie noch nicht über einen Richtlinienschlüssel verfügen, [erstellen Sie einen](saml-service-provider.md#create-a-policy-key). Konfigurieren Sie dann im technischen Profil des SAML-Tokenausstellers das Metadatenelement `MetadataSigning`. `StorageReferenceId` muss auf den Namen des Richtlinienschlüssels verweisen.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlMetadataCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

## <a name="debug-the-saml-protocol"></a>Debuggen des SAML-Protokolls

Zum Konfigurieren und Debuggen der Integration Ihres Dienstanbieters können Sie eine Browsererweiterung für das SAML-Protokoll verwenden. Die Browsererweiterungen umfassen die [SAML DevTools-Erweiterung für Chrome](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio), [SAML-tracer für Firefox](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) und [Entwicklertools für Edge oder Internet Explorer](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Mit diesen Tools können Sie die Integration zwischen Ihrer Anwendung und Azure AD B2C überprüfen. Beispiel:

* Überprüfen Sie, ob die SAML-Anforderung eine Signatur enthält, und bestimmen Sie, welcher Algorithmus zum Anmelden für die Autorisierungsanforderung verwendet wird.
* Überprüfen Sie, ob Azure AD B2C eine Fehlermeldung zurückgibt.
* Überprüfen Sie, ob der Assertionsabschnitt verschlüsselt ist.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die Informationen zum SAML-Protokoll auf der [OASIS-Website](https://www.oasis-open.org/).
- Rufen Sie die SAML-Test-Web-App aus dem [GitHub-Repository der Azure AD B2C-Community](https://github.com/azure-ad-b2c/saml-sp-tester) ab.

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
