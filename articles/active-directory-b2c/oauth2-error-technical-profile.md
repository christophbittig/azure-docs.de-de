---
title: Definieren eines technischen Profils für benutzerdefinierte OAuth2-Fehler in einer benutzerdefinierten Richtlinie
titleSuffix: Azure AD B2C
description: Definieren Sie ein technisches Profil für benutzerdefinierte OAuth2-Fehler in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/26/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 1126ce99c660860f7ee0f4abc9afcb9ed5cd1afc
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036244"
---
# <a name="define-an-oauth2-custom-error-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines technischen Profils für benutzerdefinierte OAuth2-Fehler in einer benutzerdefinierten Azure Active Directory B2C-Richtlinie

In diesem Artikel wird beschrieben, wie Sie einen benutzerdefinierten OAuth2-Fehler mit Azure Active Directory B2C (Azure AD B2C) behandeln. Verwenden Sie dieses technische Profil, wenn innerhalb Ihrer Richtlinie ein Logikfehler auftritt. Das technische Profil gibt einen Fehler an Ihre OAuth2- oder OpenId Connect-Anwendung der vertrauenden Seite zurück.

So behandeln Sie eine Meldung zu einem benutzerdefinierten OAuth2-Fehler:

1. Definieren Sie ein technisches Profil für OAuth2-Fehler.
1. Legen Sie den Fehlercode und die Fehlermeldungsansprüche fest.
1. Rufen Sie in der User Journey das technische Profil für OAuth2-Fehler auf.

## <a name="oauth2-error"></a>OAuth2-Fehler

Der Fehler wird mit den folgenden Daten zurückgegeben:

- **error** - `access_denied`
- **error_description**: Die Fehlermeldung gemäß der Konvention `AAD_Custom_<errorCode>: <errorMessage>`
- **Correlation ID**: Die Korrelations-ID von Azure AD B2C
- **Timestamp**: Der Zeitstempel des Fehlers

Im folgenden Beispiel wird eine benutzerdefinierte Fehlermeldung veranschaulicht, die an die App https://jwt.ms zurückgegeben wird:

```http
https://jwt.ms/#error=access_denied&error_description=AAD_Custom_1234%3a+My+custom+error+message%0d%0aCorrelation+ID%3a+233bf9bd-747a-4800-9062-6236f3f69a47%0d%0aTimestamp%3a+2021-03-25+14%3a01%3a23Z%0d%0a
```
  
## <a name="protocol"></a>Protocol

Das **Name**-Attribut des **Protocol**-Elements muss auf `None` festgelegt werden. Legen Sie das **OutputTokenFormat**-Element auf `OAuth2Error` fest.

Das folgende Beispiel zeigt ein technisches Profil für `ReturnOAuth2Error`:

```xml
<!--
 <ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ReturnOAuth2Error">
        <DisplayName>Return OAuth2 error</DisplayName>
        <Protocol Name="None" />
        <OutputTokenFormat>OAuth2Error</OutputTokenFormat>
        <CryptographicKeys>
          <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
        </CryptographicKeys>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="errorCode" />
          <InputClaim ClaimTypeReferenceId="errorMessage" />
        </InputClaims>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!--
</ClaimsProviders> -->
```

## <a name="input-claims"></a>Eingabeansprüche

Das Element **InputClaims** enthält eine Liste der Ansprüche, die zum Zurückgeben des OAuth2-Fehlers erforderlich sind. 

| ClaimReferenceId | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| errorCode | Ja | Der Fehlercode. | 
| errorMessage | Ja | Die Fehlermeldung. |

## <a name="cryptographic-keys"></a>Kryptografische Schlüssel

Das CryptographicKeys-Element enthält den folgenden Schlüssel:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| issuer_secret | Ja  | Ein X509-Zertifikat (RSA-Schlüsselsatz). Verwenden Sie den `B2C_1A_TokenSigningKeyContainer`-Schlüssel, den Sie unter [Tutorial: Erstellen von Benutzerflows und benutzerdefinierten Richtlinien in Azure Active Directory B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy) konfigurieren.|
|

## <a name="invoke-the-technical-profile"></a>Aufrufen des technischen Profils

Sie können das technische Profil für OAuth2-Fehler über eine User Journey oder untergeordnete Journey aufrufen. Legen Sie den Typ [OrchestrationStep](userjourneys.md#orchestrationsteps) mit einem Verweis auf Ihr technisches Profil für OAuth2-Fehler auf `SendClaims` fest.

Wenn Ihre User Journey oder untergeordnete Journey bereits über einen anderen `SendClaims`-Orchestrierungsschritt verfügt, legen Sie das Attribut `DefaultCpimIssuerTechnicalProfileReferenceId` auf das technische Profil des Tokenausstellers fest.

Im folgenden Beispiel:

-  Die User Journey `SignUpOrSignIn-Custom` legt `DefaultCpimIssuerTechnicalProfileReferenceId` auf das technische Profil des Tokenausstellers (`JwtIssuer`) fest. 
- Der achte Orchestrierungsschritt überprüft, ob `errorCode` vorhanden ist. Wenn ja, rufen Sie das technische Profil `ReturnOAuth2Error` auf, um den Fehler zurückzugeben.
- Ist `errorCode` nicht vorhanden, wird im neunten Orchestrierungsschritt das Token ausgegeben.

```xml
<UserJourney Id="SignUpOrSignIn-Custom" DefaultCpimIssuerTechnicalProfileReferenceId="JwtIssuer">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="ReturnOAuth2Error">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>errorCode</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
    </OrchestrationStep>

    <OrchestrationStep Order="9" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />

  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu [UserJourneys](userjourneys.md).