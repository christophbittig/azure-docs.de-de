---
title: RelyingParty – Azure Active Directory B2C
description: Erfahren Sie, wie Sie das RelyingParty-Element einer benutzerdefinierten Richtlinie in Azure Active Directory B2C angeben.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/09/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 83a4c80b809d2ea127b28d5f562d6c837a16d891
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136163"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Das **RelyingParty**-Element legt die User Journey fest, die für die aktuelle Anforderung an Azure Active Directory B2C (Azure AD B2C) erzwungen werden soll. Es legt außerdem die Liste von Ansprüchen fest, die die Anwendung der vertrauenden Seite als Teil des ausgestellten Tokens benötigt. Eine Anwendung der vertrauenden Seite, z.B. eine Web-, mobile oder Desktopanwendung, ruft die Richtliniendatei der vertrauenden Seite auf. Die Richtliniendatei der vertrauenden Seite führt eine bestimmte Aufgabe aus, z.B. eine Anmeldung, das Zurücksetzen eines Kennworts oder eine Profilbearbeitung. Mehrere Anwendungen können die gleiche Anwendung der vertrauenden Seite verwenden, und eine einzelne Anwendung kann mehrere Richtlinien verwenden. Alle Anwendungen der vertrauenden Seite erhalten das gleiche Token mit Ansprüchen, und der Benutzer durchläuft die gleiche User Journey.

Im folgenden Beispiel wird ein **RelyingParty**-Element in der Richtliniendatei *B2C_1A_signup_signin* gezeigt:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description>
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

Das optionale **RelyingParty**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Die standardmäßige User Journey für die Anwendung der vertrauenden Seite. |
| Endpunkte | 0:1 | Eine Liste mit Endpunkten. Weitere Informationen finden Sie unter [UserInfo-Endpunkt](userinfo-endpoint.md). |
| UserJourneyBehaviors | 0:1 | Der Bereich für die Verhalten der User Journey. |
| TechnicalProfile | 1:1 | Ein technisches Profil, das von der Anwendung der vertrauenden Seite unterstützt wird. Das technische Profil stellt einen Vertrag für das Kontaktieren von Azure AD B2C durch die Anwendung der vertrauenden Seite bereit. |

## <a name="endpoints"></a>Endpunkte

Das Element **Endpoints** enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Endpunkt | 1:1 | Ein Verweis auf einen Endpunkt.|

Das Element **Endpoint** enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Id | Ja | Ein eindeutiger Bezeichner des Endpunkts|
| UserJourneyReferenceId | Ja | Ein Bezeichner der User Journey in der Richtlinie. Weitere Informationen finden Sie unter [User Journeys](userjourneys.md).  | 

Das folgende Beispiel zeigt eine vertrauende Seite mit [UserInfo-Endpunkt](userinfo-endpoint.md):

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
  ...
```

## <a name="defaultuserjourney"></a>DefaultUserJourney

Durch das Element `DefaultUserJourney` wird ein Verweis auf den Bezeichner der User Journey angegeben, die in der Basis- oder Erweiterungsrichtlinie definiert ist. In den folgenden Beispielen wird die im **RelyingParty**-Element angegebene User Journey für die Registrierung oder Anmeldung dargestellt:

*B2C_1A_signup_signin*-Richtlinie:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* oder *B2C_1A_TrustFrameworkExtensionPolicy*:

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Das **DefaultUserJourney**-Element enthält das folgende Attribut:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Ein Bezeichner der User Journey in der Richtlinie. Weitere Informationen finden Sie unter [User Journeys](userjourneys.md). |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

Das **UserJourneyBehaviors**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Der Bereich des SSO-Sitzungsverhaltens (Einmaliges Anmelden, Single Sign-On) einer User Journey. |
| SessionExpiryType |0:1 | Das Authentifizierungsverhalten der Sitzung. Mögliche Werte: `Rolling` oder `Absolute`. Der (Standard-) Wert `Rolling` gibt an, dass der Benutzer angemeldet bleibt, solange der Benutzer ständig in der Anwendung aktiv ist. Der Wert `Absolute` gibt an, dass der Benutzer zur erneuten Authentifizierung gezwungen wird, nachdem der Zeitraum abgelaufen ist, der von der Sitzungsdauer der Anwendung festgelegt wird. |
| SessionExpiryInSeconds | 0:1 | Die Lebensdauer des als Integer angegebenen Azure AD B2C-Sitzungscookies, das bei erfolgreicher Authentifizierung im Benutzerbrowser gespeichert wird. Der Standardwert ist 86.400 Sekunden (24 Stunden). Der Mindestwert ist 900 Sekunden (15 Minuten). Der Höchstwert ist 86.400 Sekunden (24 Stunden). |
| JourneyInsights | 0:1 | Kopieren des Azure Application Insights-Instrumentierungsschlüssels, der verwendet werden soll. |
| ContentDefinitionParameters | 0:1 | Die Liste von Schlüssel-Wert-Paaren, die an der LoadUri-Parameter der ContentDefinition angefügt wird. |
|ScriptExecution| 0:1| Die unterstützten [JavaScript](javascript-and-page-layout.md)-Ausführungsmodi. Mögliche Werte: `Allow` und `Disallow` (Standardwert).
| JourneyFraming | 0:1| Ermöglicht das Laden der Benutzeroberfläche dieser Richtlinie in einem iFrame. |

### <a name="singlesignon"></a>SingleSignOn

Das **SingleSignOn**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| `Scope` | Ja | Der Bereich des SSO-Verhaltens. Mögliche Werte: `Suppressed`, `Tenant`, `Application` oder `Policy`. Der Wert `Suppressed` gibt an, dass das Verhalten unterdrückt wird, und der Benutzer wird immer aufgefordert, einen Identitätsanbieter auszuwählen.  Der Wert `Tenant` gibt an, dass das Verhalten auf alle Richtlinien im Mandanten angewendet wird. Beispielsweise wird ein Benutzer, der durch zwei User Journeys für Richtlinien eines Mandanten navigiert nicht, dazu aufgefordert, einen Identitätsanbieter auszuwählen. Der Wert `Application` gibt an, dass das Verhalten auf alle Richtlinien für die Anwendung angewendet wird, die die Anforderung stellt. Beispielsweise wird ein Benutzer, der durch zwei User Journeys für Richtlinien einer Anwendung navigiert, nicht dazu aufgefordert, einen Identitätsanbieter auszuwählen. Der Wert `Policy` gibt an, dass das Verhalten nur auf eine Richtlinie angewendet wird. Beispielsweise wird ein Benutzer, der durch zwei User Journeys für Richtlinien eines Vertrauensframeworks navigiert, dazu aufgefordert, einen Identitätsanbieter auszuwählen, wenn er zwischen Richtlinien wechselt. |
| KeepAliveInDays | No | Steuert, wie lange der Benutzer angemeldet bleibt. Durch Festlegen des Werts auf 0 wird die Funktion „Angemeldet bleiben“ deaktiviert. Der Standardwert ist `0` (deaktiviert). Der Mindestwert ist `1` Tag. Der Höchstwert ist `90` Tage. Weitere Informationen finden Sie unter [Angemeldet bleiben](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi). |
|EnforceIdTokenHintOnLogout| Nein|  Erzwingt, dass ein zuvor ausgestelltes ID-Token als Hinweis bezüglich der aktuellen authentifizierten Sitzung des Endbenutzers mit dem Client an den Abmeldeendpunkt übergeben wird. Mögliche Werte: `false` (Standard) oder `true`. Weitere Informationen finden Sie unter [Webanmeldung mit OpenID Connect](openid-connect.md).  |


## <a name="journeyinsights"></a>JourneyInsights

Das **JourneyInsights**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| TelemetryEngine | Ja | Der Wert muss `ApplicationInsights` sein. |
| InstrumentationKey | Ja | Die Zeichenfolge, die den Instrumentierungsschlüssel für das Application Insights-Element enthält. |
| DeveloperMode | Ja | Mögliche Werte: `true` oder `false`. Wenn `true`, beschleunigt Application Insights die Telemetriedaten durch die Verarbeitungspipeline. Diese Einstellung eignet sich gut für die Entwicklung, bei hohem Datenvolumen jedoch nur eingeschränkt. Die ausführlichen Aktivitätsprotokolle dienen lediglich zur Entwicklung von benutzerdefinierten Richtlinien. Verwenden Sie den Entwicklungsmodus nicht in der Produktion. Protokolle erfassen alle Ansprüche, die während der Entwicklung an die Identitätsanbieter und von Ihnen gesendet werden. Bei Verwendung in der Produktion übernehmen Entwickler die Verantwortung für personenbezogene Daten, die in dem App Insights-Protokoll gesammelt werden, das sie besitzen. Diese detaillierten Protokolle werden nur gesammelt, wenn dieser Wert auf `true` festgelegt ist.|
| ClientEnabled | Ja | Mögliche Werte: `true` oder `false`. Wenn `true`, wird das clientseitige Application Insights-Skript zum Nachverfolgen der Seitenansicht und von clientseitigen Fehlern gesendet. |
| ServerEnabled | Ja | Mögliche Werte: `true` oder `false`. Wenn `true`, wird die vorhandene JSON-Datei „UserJourneyRecorder“ als benutzerdefiniertes Ereignis an Application Insights übermittelt. |
| TelemetryVersion | Ja | Der Wert muss `1.0.0` sein. |

Weitere Informationen finden Sie unter [Erfassen von Protokollen](troubleshoot-with-application-insights.md).

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Mithilfe benutzerdefinierter Richtlinien in Azure AD B2C können Sie einen Parameter in einer Abfragezeichenfolge senden. Durch Übergeben des Parameters an Ihren HTML-Endpunkt können Sie den Seiteninhalt dynamisch ändern. Sie können z.B. das Hintergrundbild auf der Azure AD B2C-Registrierungs- oder Anmeldeseite auf der Basis eines Parameters ändern, den Sie von der Web- oder Mobilanwendung übergeben. Azure AD B2C übergibt die Abfragezeichenfolgenparameter an Ihre dynamische HTML-Datei, z.B. eine ASPX-Datei.

Im folgenden Beispiel wird ein Parameter namens `campaignId` mit dem Wert `hawaii` in der Abfragezeichenfolge übergeben:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

Das **ContentDefinitionParameters**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0:n | Eine Zeichenfolge, die das Schlüssel-Wert-Paar enthält, das der Abfragezeichenfolge einer Inhaltsdefinition des URI zum Laden angefügt wird. |

Das **ContentDefinitionParameter**-Element enthält das folgende Attribut:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Name | Ja | Der Name des Schlüssel-Wert-Paars. |

Weitere Informationen finden Sie unter [Konfigurieren der Benutzeroberfläche mit dynamischen Inhalten mithilfe von benutzerdefinierten Richtlinien](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri).

### <a name="journeyframing"></a>JourneyFraming

Das **JourneyFraming**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| Aktiviert | Ja | Ermöglicht das Laden dieser Richtlinie innerhalb eines iFrame. Mögliche Werte: `false` (Standard) oder `true`. |
| Quellen | Ja | Enthält die Domänen, die den iFrame laden. Weitere Informationen finden Sie unter [Laden von Azure B2C in einem iFrame](embedded-login.md). |

## <a name="technicalprofile"></a>TechnicalProfile

Das **TechnicalProfile**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Id | Ja | Der Wert muss `PolicyProfile` sein. |

**TechnicalProfile** enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | Die Zeichenfolge, die den Namen des technischen Profils enthält. |
| BESCHREIBUNG | 0:1 | Die Zeichenfolge, die die Beschreibung des technischen Profils enthält. |
| Protocol | 1:1 | Das Protokoll, das für den Verbund verwendet wird. |
| Metadaten | 0:1 | Die Sammlung von *Elementen* von Schlüssel-Wert-Paaren, die vom Protokoll für die Kommunikation mit dem Endpunkt im Verlauf einer Transaktion verwendet wird, um die Interaktion zwischen der vertrauenden Seite und anderen Teilnehmern der Community zu konfigurieren. |
| InputClaims | 1:1 | Eine Liste von Anspruchstypen, die im technischen Profil als Eingabe verwendet werden. Jedes dieser Elemente enthält einen Verweis auf ein **ClaimType**-Element, das bereits im **ClaimsSchema**-Abschnitt oder in einer Richtlinie, die diese Richtliniendatei erbt, definiert wurde. |
| OutputClaims | 1:1 | Eine Liste von Anspruchstypen, die im technischen Profil als Ausgabe verwendet werden. Jedes dieser Elemente enthält einen Verweis auf ein **ClaimType**-Element, das bereits im **ClaimsSchema**-Abschnitt oder in einer Richtlinie, die diese Richtliniendatei erbt, definiert wurde. |
| SubjectNamingInfo | 1:1 | Der Antragstellername, der in Tokens verwendet wird. |

Das **Protocol**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| Name | Ja | Der Name eines gültigen Protokolls, das von Azure AD B2C unterstützt und als Teil des technischen Profils verwendet wird. Mögliche Werte: `OpenIdConnect` oder `SAML2`. Der Wert `OpenIdConnect` stellt den Protokollstandard „OpenID Connect 1.0“ gemäß der Vorgaben der OpenID Foundation dar. Der Wert `SAML2` stellt den Protokollstandard „SAML 2.0“ gemäß der Vorgaben von OASIS dar. |

### <a name="metadata"></a>Metadaten

Wenn `SAML` das Protokoll ist, enthält ein Metadatenelement die folgenden Elemente: Weitere Informationen finden Sie in den [Optionen zum Registrieren einer SAML-Anwendung in Azure AD B2C](saml-service-provider-options.md).

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| IdpInitiatedProfileEnabled | Nein | Gibt an, ob der IdP-initiierte Fluss unterstützt wird. Mögliche Werte: `true` und `false` (Standardwert). | 
| XmlSignatureAlgorithm | Nein | Die Methode, die Azure AD B2C zur Signierung der SAML-Antwort verwendet. Mögliche Werte: `Sha256`, `Sha384`, `Sha512` oder `Sha1`. Vergewissern Sie sich, dass Sie den Signaturalgorithmus auf beiden Seiten mit demselben Wert konfigurieren. Verwenden Sie nur den Algorithmus, den Ihr Zertifikat unterstützt. Informationen zum Konfigurieren der SAML-Assertion finden Sie unter [Metadaten für das technische Profil des SAML-Ausstellers](saml-issuer-technical-profile.md#metadata). |
| DataEncryptionMethod | Nein | Gibt die Methode an, die von Azure AD B2C zum Verschlüsseln der Daten mit dem AES-Algorithmus (Advanced Encryption Standard) verwendet wird. Die Metadaten bestimmen den Wert des `<EncryptedData>`-Elements in der SAML-Antwort. Mögliche Werte: `Aes256` (Standard), `Aes192`, `Sha512` oder ` Aes128`. |
| KeyEncryptionMethod| Nein | Gibt die Methode an, die von Azure AD B2C zum Verschlüsseln der Kopie des Schlüssels verwendet wird, der zum Verschlüsseln der Daten verwendet wurde. Die Metadaten bestimmen den Wert des `<EncryptedKey>`-Elements in der SAML-Antwort. Mögliche Werte: ` Rsa15` (Standardwert) – RSA PKCS-Algorithmus (Public Key Cryptography Standard), Version 1.5, ` RsaOaep` – RSA OAEP-Verschlüsselungsalgorithmus (Optimal Asymmetric Encryption Padding). |
| UseDetachedKeys | Nein |  Mögliche Werte sind `true` oder `false` (Standardwert). Wenn der Wert auf `true` festgelegt wird, wird das Format der verschlüsselten Assertionen von Azure AD B2C geändert. Durch die Verwendung von getrennten Schlüsseln wird die verschlüsselte Assertion als untergeordnetes Element der EncrytedAssertion und nicht den EncryptedData hinzugefügt. |
| WantsSignedResponses| Nein | Gibt an, ob Azure AD B2C den Abschnitt `Response` der SAML-Antwort signiert. Mögliche Werte: `true` (Standard) oder `false`.  |
| RemoveMillisecondsFromDateTime| Nein | Gibt an, ob die Millisekunden aus DateTime-Werten in der SAML-Antwort entfernt werden sollen (dazu gehören „IssueInstant“, „NotBefore“, „NotOnOrAfter“ und „AuthnInstant“). Mögliche Werte: `false` (Standard) oder `true`.  |
| RequestContextMaximumLengthInBytes| No | Gibt die maximale Länge der [SAML-Anwendungen](saml-service-provider.md) an `RelayState` Parameters an. Der Standardwert lautet 1000. Das Maximum ist 2048.| 

### <a name="inputclaims"></a>InputClaims

Das **InputClaims**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| InputClaim | 0:n | Ein erwarteter Eingabeanspruchstyp. |

Das **InputClaim**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Ein Verweis auf ein **ClaimType**-Element, das bereits im **ClaimsSchema**-Abschnitt der Richtliniendatei definiert wurde. |
| DefaultValue | Nein | Ein Standardwert, der verwendet werden kann, wenn der Wert des Anspruchs leer ist. |
| PartnerClaimType | Nein | Sendet den Anspruch wie in der ClaimType-Definition konfiguriert über einen anderen Namen. |

### <a name="outputclaims"></a>OutputClaims

Das **OutputClaims**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | Der Name eines erwarteten Anspruchstyps in der Liste der unterstützten Richtlinien, die die vertrauende Seite abonniert. Dieser Anspruch dient dem technischen Profil als Ausgabe. |

Das **OutputClaim**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Ein Verweis auf ein **ClaimType**-Element, das bereits im **ClaimsSchema**-Abschnitt der Richtliniendatei definiert wurde. |
| DefaultValue | Nein | Ein Standardwert, der verwendet werden kann, wenn der Wert des Anspruchs leer ist. |
| PartnerClaimType | Nein | Sendet den Anspruch wie in der ClaimType-Definition konfiguriert über einen anderen Namen. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Mit dem **SubjectNameingInfo**-Element steuern Sie den Wert des Tokenantragstellers:

- **JWT-Token:** Der `sub`-Anspruch. Dies ist ein Prinzipal, für den das Token Informationen zusichert, z.B. der Benutzer einer Anwendung. Dieser Wert ist unveränderlich und kann nicht erneut zugewiesen oder wiederverwendet werden. Er kann für die Durchführung von sicheren Autorisierungsüberprüfungen verwendet werden, z.B. wenn das Token verwendet wird, um auf eine Ressource zuzugreifen. Der Anspruch „Antragsteller“ wird standardmäßig mit der Objekt-ID des Benutzers im Verzeichnis aufgefüllt. Weitere Informationen finden Sie unter [Token, Sitzung und einmaliges Anmelden – Konfiguration](session-behavior.md).
- **SAML-Token:** Das Element `<Subject><NameID>`, das das Antragstellerelement identifiziert. Das NameID-Format kann geändert werden.

Das **SubjectNamingInfo**-Element enthält das folgende Attribut:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ClaimType | Ja | Ein Verweis auf das **PartnerClaimType**-Element eines Ausgabeanspruchs. Die Ausgabeansprüche müssen in der Richtlinie der **OutputClaims**-Sammlung der vertrauenden Seite definiert werden. |
| Format | Nein | Wird bei vertrauenden SAML-Seiten zum Festlegen des **NameID-Formats** verwendet, das in der SAML-Assertion zurückgegeben wird. |

Im folgenden Beispiel wird gezeigt, wie eine vertrauende OpenID Connect-Seite definiert wird. Die Informationen zum Namen des Antragstellers werden als `objectId` konfiguriert:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
Das JWT-Token enthält den `sub`-Anspruch mit der Objekt-ID des Benutzers:

```json
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```

Im folgenden Beispiel wird gezeigt, wie eine vertrauende SAML-Seite definiert wird. Die Informationen zum Namen des Antragstellers werden als `objectId` konfiguriert, und die „NameID“ `format` wurde angegeben:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient"/>
  </TechnicalProfile>
</RelyingParty>
```
