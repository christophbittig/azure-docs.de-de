---
title: Definieren eines technischen Profils für die Anspruchstransformation
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie ein technisches Profil zur Anspruchstransformation in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C definieren.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: d0712780ab8ab1bd74c0acaa1564311b106871cf
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131008064"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines technischen Profils zur Anspruchstransformation in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Mit einem technischen Profil zur Anspruchstransformation können Sie Transformationen für Ausgabeansprüche aufrufen, um Anspruchswerte zu ändern, Ansprüche zu überprüfen oder Standardwerte für eine Gruppe von Ausgabeansprüchen festzulegen.

## <a name="protocol"></a>Protocol

Das **Name**-Attribut des **Protocol**-Elements muss auf `Proprietary` festgelegt werden. Das **handler**-Attribut muss den vollqualifizierten Namen der Protokollhandlerassembly, die von Azure AD B2C verwendet wird, enthalten: `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Das folgende Beispiel zeigt ein technisches Profil für die Anspruchstransformation:

```xml
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>Ausgabeansprüche

Das **OutputClaims**-Element ist erforderlich. Sie sollten mindestens einen Ausgabeanspruch, der vom technischen Profil zurückgegeben wird, angeben. Das folgende Beispiel zeigt, wie Sie die Standardwerte in den Ausgabeansprüchen festlegen:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Transformationen von Ausgabeansprüchen

Das **OutputClaimsTransformations**-Element darf eine Sammlung von **OutputClaimsTransformation**-Elementen, die zum Ändern von Ansprüchen oder zum Generieren neuer verwendet werden, enthalten. Das folgende technische Profil ruft die Anspruchstransformation **RemoveAlternativeSecurityIdByIdentityProvider** auf. Diese Anspruchstransformation entfernt eine Social Media-ID aus der Sammlung der **AlternativeSecurityIds**. Die Ausgabeansprüche dieses technischen Profils sind **identityProvider2** (auf `facebook.com` festgelegt) und **AlternativeSecurityIds**, der die Liste der Social Media-IDs, die dem Benutzer nach dem Entfernen der facebook.com-Identität zugeordnet sind, enthält.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider"
TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="IdentityProvider2"
TransformationClaimType="identityProvider" />
      <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
...
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider2" DefaultValue="facebook.com" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="RemoveAlternativeSecurityIdByIdentityProvider" />
    </OutputClaimsTransformations>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Sie können mit dem technischen Profil für die Anspruchstransformation eine Anspruchstransformation für jeden beliebigen Orchestrierungsschritt der User Journey ausführen. Im folgenden Beispiel ruft der Orchestrierungsschritt ein technisches Profil zum Aufheben von Verknüpfungen (z.B. **UnLink-Facebook-OAUTH**) auf. Dieses technische Profil ruft das technische Profil für die Anspruchstransformation **RemoveAlternativeSecurityIdByIdentityProvider** auf, das einen neuen **AlternativeSecurityIds2**-Anspruch generiert, der die Liste der Social Media-IDs des Benutzers enthält und die Facebook-Identität aus den Sammlungen entfernt.

```xml
<UserJourney Id="AccountUnLink">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="UnLinkFacebookExchange" TechnicalProfileReferenceId="UnLink-Facebook-OAUTH" />
        <ClaimsExchange Id="UnLinkMicrosoftExchange" TechnicalProfileReferenceId="UnLink-Microsoft-OAUTH" />
        <ClaimsExchange Id="UnLinkGitHubExchange" TechnicalProfileReferenceId="UnLink-GitHub-OAUTH" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ...
  </OrchestrationSteps>
</UserJourney>
```

## <a name="metadata"></a>Metadaten

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| IncludeClaimResolvingInClaimsHandling  | Nein | Gibt bei Eingabe- und Ausgabeansprüchen an, ob die [Anspruchsauflösung](claim-resolver-overview.md) im technischen Profil enthalten ist. Mögliche Werte sind `true` oder `false` (Standardwert). Wenn Sie im technischen Profil eine Anspruchsauflösung verwenden möchten, legen Sie für diese Einstellung den Wert `true` fest. |

## <a name="use-a-validation-technical-profile"></a>Verwenden eines technischen Validierungsprofils

Ein technisches Profil für die Anspruchstransformation kann zum Überprüfen von Informationen verwendet werden. Im folgenden Beispiel fordert das [technische Profil mit Selbstbestätigung](self-asserted-technical-profile.md) namens **LocalAccountSignUpWithLogonEmail** den Benutzer auf, die E-Mail-Adresse zweimal einzugeben. Anschließend ruft es das [technische Validierungsprofil](validation-technical-profile.md) mit dem Namen **Validate-Email** auf, um die E-Mail-Adressen zu überprüfen. Das technische Profil **Validate-Email** ruft die Anspruchstransformation **AssertEmailAreEqual** auf, um die beiden Ansprüche **email** und **emailRepeat** zu vergleichen, und löst eine Ausnahme aus, wenn sie nicht gemäß dem angegebenen Vergleich identisch sind.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="AssertEmailAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="emailRepeat" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    </InputParameters>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Das technische Profil für die Anspruchstransformation ruft die Anspruchstransformation **AssertEmailAreEqual** auf, die bestätigt, dass die vom Benutzer angegebenen E-Mail-Adressen identisch sind.

```xml
<TechnicalProfile Id="Validate-Email">
  <DisplayName>Unlink Facebook</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailRepeat" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAreEqual" />
  </OutputClaimsTransformations>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Ein selbstbestätigtes technisches Profil kann das technische Validierungsprofil aufrufen und die Fehlermeldung, die in den **UserMessageIfClaimsTransformationStringsAreNotEqual**-Metadaten angegeben ist, anzeigen.

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>User ID signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    ...
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">The email addresses you provided are not the same</Item>
  </Metadata>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="emailRepeat" />
    ...
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="Validate-Email" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```
