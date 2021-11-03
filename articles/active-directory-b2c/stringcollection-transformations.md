---
title: Beispiele für die Transformation von StringCollection-Ansprüchen für benutzerdefinierte Richtlinien
titleSuffix: Azure AD B2C
description: Hier finden Sie Beispiele für die Transformation von StringCollection-Ansprüchen für das Schema des Frameworks für die Identitätsfunktion (Identity Experience Framework, IEF) von Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 26c4eb195275fe7bf48a188df3f15d0f2cb61db8
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131046411"
---
# <a name="stringcollection-claims-transformations"></a>Transformationen von StringCollection-Ansprüchen

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In diesem Artikel werden Beispiele für die Verwendung von Transformationen von StringCollection-Ansprüchen für das Identity Experience Framework-Schema in Azure Active Directory B2C (Azure AD B2C) veranschaulicht. Weitere Informationen finden Sie unter [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Fügt einen Zeichenfolgenanspruch zu einem neuen stringCollection-Anspruch mit eindeutigen Werten hinzu.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | Zeichenfolge | Der Anspruchstyp, der dem Ausgabeanspruch hinzugefügt werden soll. |
| InputClaim | collection | stringCollection | Die Zeichenfolgensammlung, die dem Ausgabeanspruch hinzugefügt werden soll. Wenn die Sammlung Elemente enthält, werden die Elemente von der Anspruchstransformation kopiert, und das Element wird am Ende des Ausgabensammlungsanspruchs hinzugefügt. |
| OutputClaim | collection | stringCollection | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation ausgelöst wurde, und zwar mit dem Wert, der im Eingabeparameter angegeben wurde. |

Verwenden Sie diese Anspruchstransformation, um eine Zeichenfolge zu einer neuen oder einer vorhandenen Zeichenfolgensammlung hinzuzufügen. Sie wird häufig in einem technischen **AAD-UserWriteUsingAlternativeSecurityId**-Profil verwendet. Bevor ein Social Media-Konto erstellt wird, liest die **CreateOtherMailsFromEmail**-Anspruchstransformation den Anspruchstyp und fügt den Wert zum Anspruchstyp **otherMails** hinzu.

Die folgende Anspruchstransformation fügt den Anspruchstyp **email** zu **otherMails** hinzu.

```xml
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
  - **collection**: [„someone@outlook.com“]
  - **item**: „admin@contoso.com“
- Ausgabeansprüche:
  - **collection**: [„someone@outlook.com“, „admin@contoso.com“]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Fügt einen Zeichenfolgenparameter zu einem neuen stringCollection-Anspruch mit eindeutigen Werten hinzu.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | Die Zeichenfolgensammlung, die dem Ausgabeanspruch hinzugefügt werden soll. Wenn die Sammlung Elemente enthält, werden die Elemente von der Anspruchstransformation kopiert, und das Element wird am Ende des Ausgabensammlungsanspruchs hinzugefügt. |
| InputParameter | item | Zeichenfolge | Der Wert, der dem Ausgabeanspruch hinzugefügt werden soll. |
| OutputClaim | collection | stringCollection | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation ausgelöst wurde. Es handelt sich um den Wert, der im Eingabeparameter angegeben ist. |

Verwenden Sie diese Anspruchstransformation, um einen Zeichenfolgenwert zu einer neuen oder einer vorhandenen Zeichenfolgensammlung hinzuzufügen. Im folgenden Beispiel wird eine konstante E-Mail-Adresse (admin@contoso.com) zum Anspruch **otherMails** hinzugefügt.

```xml
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
  - **collection**: [„someone@outlook.com“]
- Eingabeparameter
  - **item**: „admin@contoso.com“
- Ausgabeansprüche:
  - **collection**: [„someone@outlook.com“, „admin@contoso.com“]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Ruft das erste Element aus der angegebenen Zeichenfolgensammlung ab.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | Die Anspruchstypen, die von der Anspruchstransformation verwendet werden, um das Element abzurufen |
| OutputClaim | extractedItem | Zeichenfolge | Die Anspruchstypen, die erstellt werden, nachdem diese Anspruchstransformation aufgerufen wurde. Das erste Element in der Sammlung. |

Im folgenden Beispiel wird der Anspruch **otherMails** gelesen, und das erste Element wird im Anspruch **email** zurückgegeben.

```xml
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
  - **collection**: [„someone@outlook.com“, „someone@contoso.com“]
- Ausgabeansprüche:
  - **extractedItem**: „someone@outlook.com“


## <a name="stringcollectioncontains"></a>StringCollectionContains

Überprüft, ob ein StringCollection-Anspruchstyp ein Element enthält.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | Der Anspruchstyp, der gesucht werden soll. |
|InputParameter|item|Zeichenfolge|Der zu suchende Wert.|
|InputParameter|ignoreCase|Zeichenfolge|Gibt an, ob bei diesem Vergleich die Groß-/Kleinschreibung in den Zeichenfolgen, die miteinander verglichen werden, ignoriert werden soll.|
| OutputClaim | outputClaim | boolean | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. Ein boolescher Indikator, wenn die Auflistung eine derartige Zeichenfolge enthält |

Im folgenden Beispiel wird überprüft, ob der stringCollection-Anspruchstyp `roles` den Wert **admin** enthält.

```xml
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

- Eingabeansprüche:
    - **inputClaim**: ["reader", "author", "admin"]
- Eingabeparameter:
    - **item**: "Admin"
    - **ignoreCase**: "true"
- Ausgabeansprüche:
    - **outputClaim**: "true"

## <a name="stringcollectioncontainsclaim"></a>StringCollectionContainsClaim

Überprüft, ob ein StringCollection-Anspruchstyp einen Anspruchswert enthält.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | Der Anspruchstyp, der gesucht werden soll. |
| InputClaim | item|Zeichenfolge| Der Anspruchstyp, der den zu suchenden Wert enthält.|
|InputParameter|ignoreCase|Zeichenfolge|Gibt an, ob bei diesem Vergleich die Groß-/Kleinschreibung in den Zeichenfolgen, die miteinander verglichen werden, ignoriert werden soll.|
| OutputClaim | outputClaim | boolean | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. Ein boolescher Indikator, wenn die Auflistung eine derartige Zeichenfolge enthält |

Im folgenden Beispiel wird überprüft, ob der stringCollection-Anspruchstyp `roles` den Wert des Anspruchstyps `role` enthält.

```xml
<ClaimsTransformation Id="HasRequiredRole" TransformationMethod="StringCollectionContainsClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="collection" />
    <InputClaim ClaimTypeReferenceId="role" TransformationClaimType="item" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hasAccess" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

- Eingabeansprüche:
    - **collection**: ["reader", "author", "admin"]
    - **item**: "Admin"
- Eingabeparameter:
    - **ignoreCase**: "true"
- Ausgabeansprüche:
    - **outputClaim**: "true"
