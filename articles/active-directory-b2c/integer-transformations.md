---
title: Beispiele für die Transformation von Integeransprüchen für benutzerdefinierte Richtlinien
titleSuffix: Azure AD B2C
description: Hier finden Sie Beispiele für die Transformation von Integeransprüchen für das Schema des Frameworks für die Identitätsfunktion (Identity Experience Framework, IEF) von Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/10/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: dc637e6369a1dbaaa9a25ad5d7b91b7b5d95ffd6
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131036394"
---
# <a name="integer-claims-transformations"></a>Transformationen von Integer-Ansprüchen

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dieser Artikel enthält Beispiele für die Verwendung von Integer-Anspruchstransformationen des Identity Experience Framework-Schemas in Azure Active Directory B2C (Azure AD B2C). Weitere Informationen finden Sie unter [ClaimsTransformations](claimstransformations.md).

## <a name="adjustnumber"></a>AdjustNumber

Erhöht oder verringert einen numerischen Anspruch und gibt einen neuen Anspruch zurück.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | INT | Der Anspruchstyp, der die Zahl enthält, die erhöht oder verringert werden soll. Wenn der `inputClaim`-Anspruchswert NULL ist, wird der Standardwert 0 verwendet. |
| InputParameter | Operator | Zeichenfolge | Mögliche Werte: `INCREMENT` (Standard) oder `DECREMENT`.|
| OutputClaim | outputClaim | INT | Dies ist der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. |

Verwenden Sie diese Anspruchstransformation, um einen numerischen Anspruchswert zu erhöhen oder zu verringern. 

```xml
<ClaimsTransformation Id="UpdateSteps" TransformationMethod="AdjustNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="steps" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="INCREMENT" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="steps" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-1"></a>Beispiel 1

- Eingabeansprüche:
    - **inputClaim**: 1
- Eingabeparameter:
    - **Operator**: INCREMENT
- Ausgabeansprüche:
    - **outputClaim**: 2

### <a name="example-2"></a>Beispiel 2

- Eingabeansprüche:
    - **inputClaim**: NULL
- Eingabeparameter:
    - **Operator**: INCREMENT
- Ausgabeansprüche:
    - **outputClaim**: 1


## <a name="assertnumber"></a>AssertNumber

Bestimmt, ob ein numerischer Anspruch größer, kleiner, gleich oder ungleich einer Zahl ist. 

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | INT | Der erste numerische Anspruch, der verglichen werden soll, um zu ermitteln, ob er größer, kleiner, gleich oder ungleich der zweiten Zahl ist. Ein Null-Wert löst eine Ausnahme aus. |
| InputParameter | CompareToValue | INT | Die zweite Zahl, die verglichen werden soll, um zu ermitteln, ob sie größer, kleiner, gleich oder ungleich der ersten Zahl ist. |
| InputParameter | Operator | Zeichenfolge | Mögliche Werte: `LESSTHAN`, `GREATERTHAN`, `GREATERTHANOREQUAL`, `LESSTHANOREQUAL`, `EQUAL`, `NOTEQUAL`. |
| InputParameter | throwError | boolean | Gibt an, ob diese Assertion einen Fehler auslösen soll, wenn das Vergleichsergebnis `true` ist. Mögliche Werte: `true` (Standard) oder `false`. <br />&nbsp;<br />Wenn der Wert `true` (Assertionsmodus) und das Vergleichsergebnis `true` sind, wird eine Ausnahme ausgelöst. Wenn der Wert `false` (Auswertungsmodus) ist, ist das Ergebnis ein neuer boolescher Anspruchstyp mit dem Wert `true` oder `false`.| 
| OutputClaim | outputClaim | boolean | Wenn `ThrowError` auf `false` festgelegt ist, enthält dieser Ausgabeanspruch je nach Vergleichsergebnis `true` oder `false`. |

### <a name="assertion-mode"></a>Assertionsmodus

Wenn der `throwError`-Eingabeparameter `true` ist (Standard), wird die Transformation von **AssertNumber**-Ansprüchen immer aus einem [technischen Validierungsprofil](validation-technical-profile.md) ausgeführt, das von einem [selbstbestätigten technischen Profil](self-asserted-technical-profile.md) aufgerufen wird. 

Die Metadaten des selbstbestätigten technischen Profils **AssertNumberError** steuern die Fehlermeldung, die das technische Profil dem Benutzer anzeigt. Die Fehlermeldungen können [lokalisiert](localization-string-ids.md#claims-transformations-error-messages) werden.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="AssertNumberError">You've reached the maximum logon attempts</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

Weitere Informationen zum Aufrufen von Anspruchstransformationen in einem Assertionsmodus finden Sie unter den Anspruchstransformationen [AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal), [AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) und [AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan).

### <a name="assertion-mode-example"></a>Beispiel zum Assertionsmodus

Im folgenden Beispiel wird bestätigt, dass die Anzahl der Versuche über fünf liegt. Die Anspruchstransformation löst bei entsprechendem Vergleichsergebnis einen Fehler aus. 

```xml
<ClaimsTransformation Id="isOverLimit" TransformationMethod="AssertNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="attempts" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="GREATERTHAN" />
    <InputParameter Id="CompareToValue" DataType="int" Value="5" />
    <InputParameter Id="throwError" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```

- Eingabeansprüche:
    - **inputClaim**: 10
- Eingabeparameter:
    - **Operator**: GREATERTHAN
    - **CompareToValue**: 5
    - **throwError**: true
- Ergebnis: Fehler wird ausgelöst.

### <a name="evaluation-mode-example"></a>Beispiel zum Auswertungsmodus

Im folgenden Beispiel wird ausgewertet, ob die Anzahl der Versuche über fünf liegt. Der Ausgabeanspruch enthält einen booleschen Wert, der dem Vergleichsergebnis entspricht. Die Anspruchstransformation löst keinen Fehler aus. 

```xml
<ClaimsTransformation Id="isOverLimit" TransformationMethod="AssertNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="attempts" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="GREATERTHAN" />
    <InputParameter Id="CompareToValue" DataType="int" Value="5" />
    <InputParameter Id="throwError" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="attemptsCountExceeded" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- Eingabeansprüche:
    - **inputClaim**: 10
- Eingabeparameter:
    - **Operator**: GREATERTHAN
    - **CompareToValue**: 5
    - **throwError**: false
- Ausgabeansprüche:
    - **outputClaim**: true


## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Konvertiert einen long-Datentyp in einen string-Datentyp.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | Der Anspruchstyp, der in eine Zeichenfolge konvertiert werden soll. |
| OutputClaim | outputClaim | Zeichenfolge | Dies ist der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. |

In diesem Beispiel wird der Anspruch `numericUserId` mit dem Werttyp long in einen `UserId`-Anspruch mit dem Werttyp string konvertiert.

```xml
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **inputClaim:** 12334 (long)
- Ausgabeansprüche:
    - **outputClaim:** „12334“ (string)

