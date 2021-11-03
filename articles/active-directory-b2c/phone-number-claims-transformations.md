---
title: Anspruchstransformationen von Telefonnummern in benutzerdefinierten Richtlinien
titleSuffix: Azure AD B2C
description: Referenz zu benutzerdefinierten Richtlinien für Anspruchstransformationen von Telefonnummern in Azure AD B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: e22bb4ebc3a115bdde874b88675547ef2db1cd23
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131044472"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Definieren von Anspruchstransformationen von Telefonnummern in Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dieser Artikel enthält eine Referenz und Beispiele für die Verwendung von Telefonnummern-Anspruchstransformationen des Identity Experience Framework-Schemas in Azure Active Directory B2C (Azure AD B2C). Weitere Informationen zu Anspruchstransformationen im Allgemeinen finden Sie unter [ClaimsTransformations](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertPhoneNumberClaimToString

Konvertiert einen `phoneNumber`-Datentyp in einen `string`-Datentyp.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | phoneNumber |  Der Anspruchstyp, der in eine Zeichenfolge konvertiert werden soll. |
| OutputClaim | phoneNumberString | Zeichenfolge | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. |

In diesem Beispiel wird der „cellPhoneNumber“-Anspruch mit dem Werttyp `phoneNumber` in einen „cellPhone“-Anspruch mit dem Werttyp `string` konvertiert.

```xml
<ClaimsTransformation Id="PhoneNumberToString" TransformationMethod="ConvertPhoneNumberClaimToString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="cellPhoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="cellPhone" TransformationClaimType="phoneNumberString" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
  - **phoneNumber**: +11234567890 (Werttyp „phoneNumber“)
- Ausgabeansprüche:
  - **phoneNumberString**: +11234567890 (Werttyp „string“)


## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

Diese Anspruchstransformation überprüft das Format der Telefonnummer. Wenn ein gültiges Format vorliegt, wird es in ein Standardformat geändert, das von Azure AD B2C verwendet wird. Wenn die angegebene Telefonnummer nicht in einem gültigen Format vorliegt, wird eine Fehlermeldung zurückgegeben.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumberString | Zeichenfolge |  Der Zeichenfolgenanspruch für die Telefonnummer. Die Telefonnummer muss im internationalen Format vorliegen. Ihr muss ein führendes Zeichen „+“ und der Länder-/Regionscode vorangestellt werden. Wenn der Eingabeanspruch `country` bereitgestellt wird, liegt die Telefonnummer im lokalen Format vor (ohne Länder-/Regionscode). |
| InputClaim | country | Zeichenfolge | [Optional] Der Zeichenfolgenanspruch für den Länder-/Regionscode der Telefonnummer im ISO3166-Format (der aus zwei Buchstaben bestehende ISO-3166-Länder-/Regionscode). |
| OutputClaim | outputClaim | phoneNumber | Das Ergebnis dieser Anspruchstransformation. |

Die Anspruchstransformation **ConvertStringToPhoneNumberClaim** wird immer über ein [technisches Validierungsprofil](validation-technical-profile.md) ausgeführt, das von einem [selbstbestätigten technischen Profil](self-asserted-technical-profile.md) oder einem [Anzeigesteuerelement](display-controls.md) aufgerufen wird. Die Metadaten des selbstbestätigten technischen Profils **UserMessageIfClaimsTransformationInvalidPhoneNumber** steuern die Fehlermeldung, die dem Benutzer anzeigt wird.

![Abbildung des Ausführungspfads der Fehlermeldung](./media/phone-authentication/assert-execution.png)

Mit dieser Anspruchstransformation können Sie sicherstellen, dass der bereitgestellte Zeichenfolgenanspruch eine gültige Telefonnummer ist. Ist dies nicht der Fall, wird eine Fehlermeldung ausgelöst. Im folgenden Beispiel wird überprüft, ob der ClaimType **phoneString** tatsächlich eine gültige Telefonnummer ist, dann wird die Telefonnummer im Azure AD B2C-Standardformat zurückgegeben. Andernfalls wird eine Fehlermeldung ausgelöst.

```xml
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

Das selbstbestätigte technische Profil, das das technische Validierungsprofil aufruft, das diese Anspruchstransformation enthält, kann die Fehlermeldung definieren.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Beispiel 1

- Eingabeansprüche:
  - **phoneNumberString**: 033 456-7890
  - **country**: DK
- Ausgabeansprüche:
  - **outputClaim**: +450334567890

### <a name="example-2"></a>Beispiel 2

- Eingabeansprüche:
  - **phoneNumberString**: +1 (123) 456-7890
- Ausgabeansprüche:
  - **outputClaim**: +11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

Hierdurch werden der Länder-/Regionscode und die nationale Rufnummer aus dem Eingabeanspruch extrahiert. Optional wird eine Ausnahme ausgelöst, wenn die angegebene Telefonnummer ungültig ist.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | Zeichenfolge | Der Zeichenfolgenanspruch der Telefonnummer. Die Telefonnummer muss im internationalen Format vorliegen. Ihr muss ein führendes Zeichen „+“ und der Länder-/Regionscode vorangestellt werden. |
| InputParameter | throwExceptionOnFailure | boolean | [Optional] Ein Parameter, der angibt, ob eine Ausnahme ausgelöst wird, wenn die Telefonnummer ungültig ist. Der Standardwert ist „false“. |
| InputParameter | countryCodeType | Zeichenfolge | [Optional] Ein Parameter, der den Typ des Länder-/Regionscodes im Ausgabeanspruch angibt. Verfügbare Werte sind **CallingCode** (der internationale Aufrufcode für ein Land bzw. eine Region, z. B. +1) oder **ISO3166** (der aus zwei Buchstaben bestehende ISO-3166-Länder-/Regionscode). |
| OutputClaim | nationalNumber | Zeichenfolge | Der Zeichenfolgenanspruch für die nationale Rufnummer der Telefonnummer. |
| OutputClaim | countryCode | Zeichenfolge | Der Zeichenfolgenanspruch für den Länder-/Regionscode der Telefonnummer. |


Wenn die Anspruchstransformation **GetNationalNumberAndCodeFromPhoneNumberString** aus einem [technischen Validierungsprofil](validation-technical-profile.md) ausgeführt wird, das von einem [selbstbestätigten technischen Profil](self-asserted-technical-profile.md) oder einer [Anzeigesteuerelementaktion](display-controls.md#display-control-actions) aufgerufen wird, dann steuern die **UserMessageIfPhoneNumberParseFailure**-Metadaten des selbstbestätigten technischen Profils die Fehlermeldung, die dem Benutzer angezeigt wird.

![Abbildung des Ausführungspfads der Fehlermeldung](./media/phone-authentication/assert-execution.png)

Mit dieser Anspruchstransformation können Sie eine vollständige Telefonnummer in den Länder-/Regionscode und die nationale Rufnummer aufteilen. Wenn die angegebene Telefonnummer ungültig ist, können Sie auswählen, ob eine Fehlermeldung ausgegeben werden soll.

Im folgenden Beispiel wird versucht, die Telefonnummer in die nationale Rufnummer und den Länder-/Regionscode aufzuteilen. Wenn die Telefonnummer gültig ist, wird die Telefonnummer von der nationalen Rufnummer überschrieben. Wenn die Telefonnummer ungültig ist, wird keine Ausnahme ausgelöst, und die Telefonnummer weist weiterhin den ursprünglichen Wert auf.

```xml
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

Das selbstbestätigte technische Profil, das das technische Validierungsprofil aufruft, das diese Anspruchstransformation enthält, kann die Fehlermeldung definieren.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Beispiel 1

- Eingabeansprüche:
  - **phoneNumber**: +49 (123) 456-7890
- Eingabeparameter:
  - **throwExceptionOnFailure**: FALSE
  - **countryCodeType**: ISO3166
- Ausgabeansprüche:
  - **nationalNumber**: 1234567890
  - **countryCode**: DE

### <a name="example-2"></a>Beispiel 2

- Eingabeansprüche:
  - **phoneNumber**: +49 (123) 456-7890
- Eingabeparameter
  - **throwExceptionOnFailure**: FALSE
  - **countryCodeType**: CallingCode
- Ausgabeansprüche:
  - **nationalNumber**: 1234567890
  - **countryCode**: +49
