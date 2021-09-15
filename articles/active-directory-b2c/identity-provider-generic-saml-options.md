---
title: Optionen zum Festlegen der Anmeldung mit einem SAML-Identitätsanbieter
titleSuffix: Azure Active Directory B2C
description: Hier finden Sie Informationen zu den Optionen zum Konfigurieren der Anmeldung mit einem SAML-Identitätsanbieter (Identity Provider, IdP) in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c245fef005be5937887a3b8af1a5264e6520a6e8
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967782"
---
# <a name="configure-saml-identity-provider-options-with-azure-active-directory-b2c"></a>Konfigurieren der SAML-Identitätsanbieteroptionen mit Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) unterstützt den Verbund mit SAML 2.0-Identitätsanbietern. In diesem Artikel werden die Konfigurationsoptionen beschrieben, die beim Aktivieren der Anmeldung mit einem SAML-Identitätsanbieter verfügbar sind.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="claims-mapping"></a>Anspruchszuordnung

Das Element **OutputClaims** enthält eine Liste von Ansprüchen, die vom SAML-Identitätsanbieter zurückgegeben werden. Sie müssen den Namen des in Ihrer Richtlinie definierten Anspruchs dem Namen zuordnen, der für den Identitätsanbieter definiert wurde. Überprüfen Sie Ihren Identitätsanbieter bezüglich der Liste der Ansprüche (Assertionen). Sie können auch den Inhalt der von Ihrem Identitätsanbieter zurückgegebenen SAML-Antwort überprüfen. Weitere Informationen finden Sie unter [Debuggen der SAML-Nachrichten](#debug-saml-protocol). Wenn Sie einen Anspruch hinzufügen möchten, [definieren Sie zuerst einen Anspruch](claimsschema.md), und fügen Sie ihn dann der Ausgabeanspruchssammlung hinzu.

Sie können auch Ansprüche, die nicht vom Identitätsanbieter zurückgegeben wurden, einfügen, sofern Sie das `DefaultValue`-Attribut festlegen. Der Standardwert kann statisch oder dynamisch sein, wobei [Kontextansprüche](#enable-use-of-context-claims) verwendet werden.

Das Ausgabeanspruchselement enthält die folgenden Attribute:

- **ClaimTypeReferenceId** ist der Verweis auf einen Anspruchstyp. 
- **PartnerClaimType** ist der Name der Eigenschaft, die in der SAML-Assertion angezeigt wird. 
- **DefaultValue** ist ein vordefinierter Standardwert. Wenn kein Anspruch aufgeführt wird, wird der Standardwert verwendet. Sie können auch einen [Anspruchskonfliktlöser](claim-resolver-overview.md) mit einem Kontextwert (wie z. B. der Korrelations-ID oder der Benutzer-IP-Adresse) verwenden.

### <a name="subject-name"></a>Antragstellername

Zum Lesen der SAML-Assertion **NameId** in **Subject** als normalisierten Anspruch legen Sie den Anspruch **PartnerClaimType** auf den Wert des `SPNameQualifier`-Attributs fest. Wenn das `SPNameQualifier`-Attribut nicht angezeigt wird, legen Sie den Anspruch **PartnerClaimType** auf den Wert des `NameQualifier`-Attributs fest.

SAML-Assertion:

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://<your-tenant>.b2clogin.com/<your-tenant>.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Ausgabeanspruch:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Wenn keines der Attribute `SPNameQualifier` und `NameQualifier` in der SAML-Assertion angezeigt wird, legen Sie den Anspruch **PartnerClaimType** auf `assertionSubjectName` fest. Stellen Sie sicher, dass die **NameId** der erste Wert im XML-Code der Assertion ist. Wenn Sie mehrere Assertionen definieren, verwendet Azure AD B2C den Antragstellerwert aus der letzten Assertion.


## <a name="configure-saml-protocol-bindings"></a>Konfigurieren von SAML-Protokollbindungen

Die SAML-Anforderungen werden an den Identitätsanbieter gesendet, wie im `SingleSignOnService`-Metadatenelement des Identitätsanbieters angegeben. Die meisten Autorisierungsanforderungen von Identitätsanbietern werden direkt in die URL-Abfragezeichenfolge einer HTTP GET-Anforderung übernommen (da die Nachrichten relativ kurz sind). Informationen zum Konfigurieren der Bindungen für beide SAML-Anforderungen finden Sie in der Dokumentation Ihres Identitätsanbieters.

Nachfolgend finden Sie ein Beispiel für einen Azure AD-Metadatendienst für einmaliges Anmelden mit zwei-Bindungen. Die `HTTP-Redirect`-Bindung hat Vorrang vor der `HTTP-POST`-Bindung, weil sie zuerst in den Metadaten des SAML-Identitätsanbieters aufgeführt ist.

```xml
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
</IDPSSODescriptor>
```

### <a name="assertion-consumer-service"></a>Assertionsverbraucherdienst

Der Assertionsverbraucherdienst (Assertion Consumer Service oder ACS) wird genutzt, SAML-Antworten vom Identitätsanbieter von Azure AD B2C gesendet oder empfangen werden. SAML-Antworten werden über die HTTP-POST-Bindung an Azure AD B2C übertragen. Der ACS-Speicherort verweist auf die Basisrichtlinie Ihrer vertrauenden Seite. Wenn die vertrauende Seite beispielsweise *B2C_1A_signup_signin* ist, stellt ACS die Basisrichtlinie von *B2C_1A_signup_signin* dar, zum Beispiel *B2C_1A_TrustFrameworkBase*.

Nachfolgend finden Sie ein Beispiel für ein Assertionsverbraucherdienst-Element der Azure AD B2C-Richtlinienmetadaten. 

```xml
<SPSSODescriptor AuthnRequestsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://your-tenant.b2clogin.com/your-tenant/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" index="0" isDefault="true"/>
</SPSSODescriptor>
```

## <a name="configure-the-saml-request-signature"></a>Konfigurieren der SAML-Anforderungssignatur

Azure AD B2C signiert alle ausgehenden Authentifizierungsanforderungen mithilfe des kryptografischen Schlüssels **SamlMessageSigning**. Um die SAML-Anforderungssignatur zu deaktivieren, legen Sie **WantsSignedRequests** auf `false` fest. Wenn die Metadaten auf `false` festgelegt sind, werden die Parameter **SigAlg** und **Signature** (Abfragezeichenfolgen- oder POST-Paramater) in der Anforderung ausgelassen.

Diese Metadaten steuern auch das **AuthnRequestsSigned**-Attribut, das in den Metadaten des technischen Azure AD B2C-Profils enthalten ist, das für den Identitätsanbieter freigegeben wird. Azure AD B2C signiert die Anforderung nicht, wenn der Wert von **WantsSignedRequests** in den Metadaten des technischen Profils auf `false` und in den Metadaten des Identitätsanbieters **WantAuthnRequestsSigned** auf `false` festgelegt ist oder nicht angegeben wurde.

Im folgenden Beispiel wird die Signatur aus der SAML-Anforderung entfernt.

```xml
<Metadata>
  ...
  <Item Key="WantsSignedRequests">false</Item>
</Metadata>
```

### <a name="signature-algorithm"></a>Signaturalgorithmus

Azure AD B2C verwendet `Sha1` zum Signieren der SAML-Anforderung. Verwenden Sie die **XmlSignatureAlgorithm**-Metadaten, um den zu verwendenden Algorithmus zu konfigurieren. Mögliche Werte sind `Sha256`, `Sha384`, `Sha512` oder `Sha1` (Standardwert). Diese Metadaten steuern den Wert des **SigAlg**-Parameters (Abfragezeichenfolgen- oder POST-Parameter) in der SAML-Anforderung. Vergewissern Sie sich, dass Sie den Signaturalgorithmus auf beiden Seiten mit demselben Wert konfigurieren. Verwenden Sie nur den Algorithmus, den Ihr Zertifikat unterstützt.

### <a name="include-key-info"></a>Einschließen der Schlüsselinformationen

Wenn der Identitätsanbieter angibt, dass die Azure AD B2C-Bindung auf `HTTP-POST` festgelegt ist, schließt Azure AD B2C die Signatur und den Algorithmus in den Text der SAML-Anforderung ein. Sie können Azure AD auch so konfigurieren, dass der öffentliche Schlüssel des Zertifikats einbezogen wird, wenn die Bindung auf `HTTP-POST` festgelegt ist. Legen Sie die **IncludeKeyInfo**-Metadaten auf `true` oder `false` fest. Im folgenden Beispiel bezieht Azure AD den öffentlichen Schlüssel des Zertifikats nicht ein.

```xml
<Metadata>
  ...
  <Item Key="IncludeKeyInfo">false</Item>
</Metadata>
```

## <a name="configure-saml-request-name-id"></a>Konfigurieren der Namens-ID der SAML-Anforderung

Das `<NameID>`-Element der SAML-Autorisierungsanforderung gibt das Format des SAML-Namensbezeichners an. In diesem Abschnitt wird die Standardkonfiguration beschrieben, und es wird erörtert, wie Sie das Namens-ID-Element anpassen.

## <a name="preferred-username"></a>Bevorzugter Benutzername

Während einer User Journey zur Anmeldung kann eine Anwendung der vertrauenden Seite auf einen bestimmten Benutzer ausgerichtet werden. Azure AD B2C ermöglicht Ihnen das Senden eines bevorzugten Benutzernamens an den SAML-Identitätsanbieter. Das **InputClaims**-Element dient zum Senden einer **NameId** im **Subject** der SAML-Autorisierungsanforderung.

Fügen Sie das folgende `<InputClaims>`-Element direkt nach dem `<CryptographicKeys>`-Element ein, um die Antragstellernamens-ID innerhalb der Autorisierungsanforderung einzufügen. Der **PartnerClaimType** muss auf `subject` festgelegt werden.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" />
</InputClaims>
```

In diesem Beispiel sendet Azure AD B2C den Wert des Anspruchs **signInName** mit der **NameId** im **Subject** der SAML-Autorisierungsanforderung.

```xml
<samlp:AuthnRequest ... >
  ...
  <saml:Subject>
    <saml:NameID>sam@contoso.com</saml:NameID>
  </saml:Subject>
</samlp:AuthnRequest>
```

Sie können [Kontextansprüche](claim-resolver-overview.md) (z. B. `{OIDC:LoginHint}`) verwenden, um den Anspruchswert aufzufüllen.

```xml
<Metadata>
  ...
  <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
</Metadata>
  ...
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" DefaultValue="{OIDC:LoginHint}" AlwaysUseDefaultValue="true" />
</InputClaims>
```

### <a name="name-id-policy-format"></a>Namens-ID-Richtlinienformat

Standardmäßig gibt die SAML-Autorisierungsanforderung die `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`-Richtlinie an. Damit wird angegeben, dass jeder ID-Typ, der vom Identitätsanbieter für den angeforderten Antragsteller unterstützt wird, verwendet werden kann.

Wenn Sie dieses Verhalten ändern möchten, lesen Sie in der Dokumentation Ihres Identitätsanbieters nach, welche Namens-ID-Richtlinien unterstützt werden. Fügen Sie dann die `NameIdPolicyFormat`-Metadaten im entsprechenden Richtlinienformat hinzu. Beispiel:

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
</Metadata>
```

Die folgende SAML-Autorisierungsanforderung enthält die Namens-ID-Richtlinie.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" />
</samlp:AuthnRequest>
```

### <a name="allow-creating-new-accounts"></a>Erstellung neuer Konten zulassen

Wenn Sie das [Namens-ID-Richtlinienformat](#name-id-policy-format) angeben, können Sie auch die `AllowCreate`-Eigenschaft des **NameIDPolicy**-Elements festlegen, um anzugeben, ob der Identitätsanbieter während des Anmeldeflows ein neues Konto erstellen darf. Anleitungen hierzu finden Sie in der Dokumentation Ihres Identitätsanbieters.

Standardmäßig lässt Azure AD B2C die `AllowCreate`-Eigenschaft aus. Sie können dieses Verhalten mithilfe der `NameIdPolicyAllowCreate`-Metadaten ändern. Der Wert dieser Metadaten lautet `true` oder `false`.

Im folgenden Beispiel wird veranschaulicht, wie Sie die `AllowCreate`-Eigenschaft des `NameIDPolicy`-Elements auf `true` festlegen.

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
  <Item Key="NameIdPolicyAllowCreate">true</Item>
</Metadata>
```


Im folgenden Beispiel ist eine Autorisierungsanforderung mit der **AllowCreate**-Eigenschaft des **NameIDPolicy**-Elements dargestellt.


```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy 
      Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" 
      AllowCreate="true" />
</samlp:AuthnRequest>
```

### <a name="force-authentication"></a>Erzwingen der Authentifizierung

Sie können erzwingen, dass der externe SAML-Identitätsanbieter den Benutzer zur Authentifizierung auffordert, indem Sie die Eigenschaft `ForceAuthN` in der SAML-Authentifizierungsanforderung übergeben. Ihr Identitätsanbieter muss diese Eigenschaft ebenfalls unterstützen.

Die Eigenschaft `ForceAuthN` ist ein boolescher Wert (`true` oder `false`). Standardmäßig legt Azure AD B2C den ForceAuthN-Wert auf `false` fest. Wenn die Sitzung dann zurückgesetzt wird (z. B. mithilfe von `prompt=login` in OIDC), wird der ForceAuthN-Wert auf `true` festgelegt. Wenn Sie das Metadatenelement wie unten gezeigt einstellen, wird der Wert für alle Anforderungen an den externen Identitätsanbieter erzwungen.

Im folgenden Beispiel wird die Festlegung der Eigenschaft `ForceAuthN` auf `true` gezeigt:

```xml
<Metadata>
  ...
  <Item Key="ForceAuthN">true</Item>
  ...
</Metadata>
```

Das folgende Beispiel zeigt die Eigenschaft `ForceAuthN` in einer Autorisierungsanforderung:


```xml
<samlp:AuthnRequest AssertionConsumerServiceURL="https://..."  ...
                    ForceAuthN="true">
  ...
</samlp:AuthnRequest>
```

### <a name="include-authentication-context-class-references"></a>Einschließen von Authentifizierungskontext-Klassenverweisen

Eine SAML-Autorisierungsanforderung kann ein **AuthnContext**-Element enthalten, das den Kontext einer Autorisierungsanforderung angibt. Das Element kann einen Authentifizierungskontext-Klassenverweis enthalten, der dem SAML-Identitätsanbieter mitteilt, welcher Authentifizierungsmechanismus dem Benutzer präsentiert werden soll.

Um die Authentifizierungskontext-Klassenverweise zu konfigurieren, fügen Sie **IncludeAuthnContextClassReferences**-Metadaten hinzu. Geben Sie im Wert mindestens einen URI-Verweis an, der die Authentifizierungskontextklassen identifiziert. Geben Sie mehrere URIs als eine durch Kommas getrennte Liste an. Lesen Sie in der Dokumentation Ihres Identitätsanbieters nach, welche **AuthnContextClassRef**-URIs unterstützt werden.

Im folgenden Beispiel wird Benutzern die Anmeldung mit Benutzername und Kennwort sowie die Anmeldung mit Benutzername und Kennwort über eine geschützte Sitzung (SSL/TLS) ermöglicht.

```xml
<Metadata>
  ...
  <Item Key="IncludeAuthnContextClassReferences">urn:oasis:names:tc:SAML:2.0:ac:classes:Password,urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</Item>
</Metadata>
```

Die folgende SAML-Autorisierungsanforderung enthält die Authentifizierungskontext-Klassenverweise.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:RequestedAuthnContext>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</saml:AuthnContextClassRef>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</saml:AuthnContextClassRef>
  </samlp:RequestedAuthnContext>
</samlp:AuthnRequest>
```

## <a name="include-custom-data-in-the-authorization-request"></a>Einschließen benutzerdefinierter Daten in die Autorisierungsanforderung

Sie können optional Protokollnachrichten-Erweiterungselemente einschließen, die zwischen Azure AD BC und Ihrem Identitätsanbieter vereinbart wurden. Die Erweiterung wird im XML-Format angezeigt. Sie schließen Erweiterungselemente ein, indem Sie dem CDATA-Element `<![CDATA[Your Custom XML]]>` XML-Daten hinzufügen. Sehen Sie in der Dokumentation Ihres Identitätsanbieters nach, ob das Erweiterungselement unterstützt wird.

Im folgenden Beispiel wird die Verwendung von Erweiterungsdaten veranschaulicht:

```xml
<Metadata>
  ...
  <Item Key="AuthenticationRequestExtensions"><![CDATA[
            <ext:MyCustom xmlns:ext="urn:ext:custom">
              <ext:AssuranceLevel>1</ext:AssuranceLevel>
              <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
            </ext:MyCustom>]]></Item>
</Metadata>
```

> [!NOTE]
> Gemäß der SAML-Spezifikation müssen die Erweiterungsdaten namespacequalifizierte XML-Daten sein (z. B. „urn:ext:custom“, wie im obigen Beispiel gezeigt). Es darf sich dabei nicht um einen der SAML-spezifischen Namespaces handeln.

Bei Verwendung der SAML-Protokollnachrichtenerweiterung sieht die SAML-Antwort wie im folgenden Beispiel aus:

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:Extensions>
    <ext:MyCustom xmlns:ext="urn:ext:custom">
      <ext:AssuranceLevel>1</ext:AssuranceLevel>
      <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
    </ext:MyCustom>
  </samlp:Extensions>
</samlp:AuthnRequest>
```

## <a name="require-signed-saml-responses"></a>Anfordern von signierten SAML-Antworten

Azure AD B2C erfordert, dass alle eingehenden Assertionen signiert sind. Sie können diese Anforderung entfernen, indem Sie das **WantsSignedAssertions**-Element auf `false` festlegen. In diesem Fall sollte der Identitätsanbieter die Assertionen nicht signieren. Wenn er dies allerdings doch tut, überprüft Azure AD B2C die Signatur nicht.

Die **WantsSignedAssertions**-Metadaten steuern das SAML-Metadatenflag **WantAssertionsSigned**, das in den Metadaten des technischen Azure AD B2C-Profils enthalten ist, das für den Identitätsanbieter freigegeben wird.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```

Wenn Sie die Assertionsüberprüfung deaktivieren, möchten Sie möglicherweise auch die Überprüfung der Antwortnachrichtensignatur deaktivieren. Legen Sie die **ResponsesSigned**-Metadaten auf `false` fest. In diesem Fall sollte der Identitätsanbieter die SAML-Antwortnachricht nicht signieren. Wenn er dies allerdings doch tut, überprüft Azure AD B2C die Signatur nicht.

Im folgenden Beispiel werden die Nachrichtensignatur und die Assertionssignatur entfernt:

```xml
<Metadata>
  ...
  <Item Key="WantsSignedAssertions">false</Item>
  <Item Key="ResponsesSigned">false</Item>
</Metadata>
```

## <a name="require-encrypted-saml-responses"></a>Anfordern von verschlüsselten SAML-Antworten

Wenn alle eingehenden Assertionen verschlüsselt werden sollen, legen Sie die **WantsEncryptedAssertions**-Metadaten fest. Wenn eine Verschlüsselung erforderlich ist, verwendet der Identitätsanbieter einen öffentlichen Schlüssel eines Verschlüsselungszertifikats in einem technischen Azure AD B2C-Profil. Azure AD B2C entschlüsselt die Antwortassertion mithilfe des privaten Teils des Verschlüsselungszertifikats.

Wenn Sie die Assertionsverschlüssung aktivieren, müssen Sie möglicherweise die Überprüfung der Antwortsignatur deaktivieren (weitere Informationen finden Sie unter [Anfordern von signierten SAML-Antworten](#require-signed-saml-responses)).

Wenn das Metadatenelement **WantsEncryptedAssertions** auf `true` festgelegt ist, umfassen die Metadaten des technischen Azure AD B2C-Profils den **Verschlüsselungsbereich**. Der Identitätsanbieter liest die Metadaten und verschlüsselt die SAML-Antwortassertion mit dem öffentlichen Schlüssel, der in den Metadaten des technischen Azure AD B2C-Profils enthalten ist.

Das folgende Beispiel zeigt den Abschnitt „Schlüsseldeskriptor“ der SAML-Metadaten, die für die Verschlüsselung verwendet werden:

```xml
<SPSSODescriptor AuthnRequestsSigned="true"  protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  <KeyDescriptor use="encryption">
    <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
      <X509Data>
        <X509Certificate>valid certificate</X509Certificate>
      </X509Data>
    </KeyInfo>
  </KeyDescriptor>
  ...
</SPSSODescriptor>
```

So entschlüsseln Sie die SAML-Antwortassertion:

1. [Erstellen Sie einen Richtlinienschlüssel](identity-provider-generic-saml.md#create-a-policy-key) mit einem eindeutigen Bezeichner. Beispiel: `B2C_1A_SAMLEncryptionCert`.
2. In der **CryptographicKeys**-Sammlung des technischen SAML-Profils. Legen Sie **StorageReferenceId** auf den Namen des Richtlinienschlüssels fest, den Sie im ersten Schritt erstellt haben. Die `SamlAssertionDecryption`-ID gibt die Verwendung des kryptografischen Schlüssels zum Verschlüsseln und Entschlüsseln der Assertion der SAML-Antwort an.

    ```xml
    <CryptographicKeys>
            ...
      <Key Id="SamlAssertionDecryption" StorageReferenceId="B2C_1A_SAMLEncryptionCert"/>
    </CryptographicKeys>
    ```

3. Legen Sie die **WantsEncryptedAssertions**-Metadaten des technischen Profils auf `true` fest.
    
    ```xml
    <Metadata>
      ...
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
    ```

4. Aktualisieren Sie Ihren Identitätsanbieter mit den neuen Metadaten des technischen Azure AD B2C-Profils. Dann sollte **KeyDescriptor** mit der auf `encryption` festgelegten **use**-Eigenschaft mit dem öffentlichen Zertifikatschlüssel angezeigt werden.

## <a name="enable-use-of-context-claims"></a>Aktivieren der Verwendung von Kontextansprüchen

Sie können in die Eingabe- und Ausgabeanspruchssammlung Ansprüche einschließen, die nicht vom Identitätsanbieter zurückgegeben werden, sofern Sie das `DefaultValue`-Attribut festlegen. Sie können auch [Kontextansprüche](claim-resolver-overview.md) im technischen Profil verwenden. Gehen Sie wie folgt vor, um einen Kontextanspruch zu verwenden:

1. Fügen Sie dem [ClaimsSchema](claimsschema.md)-Element in [BuildingBlocks](buildingblocks.md) einen Anspruchstyp hinzu.
2. Fügen Sie der Eingabe- oder Ausgabesammlung einen Ausgabeanspruch hinzu. Im folgenden Beispiel legt der erste Anspruch den Wert des Identitätsanbieters fest. Der zweite Anspruch verwendet die [Kontextansprüche](claim-resolver-overview.md) der Benutzer-IP-Adresse.
    
    ```xml
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" AlwaysUseDefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="IpAddress" DefaultValue="{Context:IPAddress}" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    ```

## <a name="disable-single-logout"></a>Deaktivieren des einmaligen Abmeldens

Bei einer Abmeldeanforderung einer Anwendung versucht Azure AD B2C, sich von Ihrem SAML-Identitätsanbieter abzumelden. Weitere Informationen finden Sie unter [Abmelden von der Azure AD B2C-Sitzung](session-behavior.md#sign-out).  Um das Verhalten des einmaligen Abmeldens zu deaktivieren, legen Sie die **SingleLogoutEnabled**-Metadaten auf `false` fest.

```xml
<Metadata>
  ...
  <Item Key="SingleLogoutEnabled">false</Item>
</Metadata>
```

## <a name="debug-saml-protocol"></a>Debuggen des SAML-Protokolls

Um das Konfigurieren und Debuggen des Verbunds mit einem SAML-Identitätsanbieter zu vereinfachen, können Sie eine Browsererweiterung für das SAML-Protokoll (z. B. die [SAML DevTools-Erweiterung](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) für Chrome, [SAML-tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) für FireFox oder [Microsoft Edge- oder IE-Entwicklertools](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957)) verwenden.

Mithilfe dieser Tools können Sie die Integration zwischen Azure AD B2C und Ihrem SAML-Identitätsanbieter überprüfen. Beispiel:

* Überprüfen Sie, ob die SAML-Anforderung eine Signatur enthält, und bestimmen Sie, welcher Algorithmus zum Anmelden für die Autorisierungsanforderung verwendet wird.
* Rufen Sie die Ansprüche (Assertionen) unter dem Abschnitt `AttributeStatement` ab.
* Überprüfen Sie, ob der Identitätsanbieter eine Fehlermeldung zurückgibt.
* Überprüfen Sie, ob der Assertionsabschnitt verschlüsselt ist.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie Probleme mit Ihren benutzerdefinierten Richtlinien mithilfe von [Application Insights](troubleshoot-with-application-insights.md) diagnostizieren. 

::: zone-end
