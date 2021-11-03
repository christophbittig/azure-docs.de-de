---
title: Definieren eines technischen PhoneFactor-Profils in einer benutzerdefinierten Richtlinie
titleSuffix: Azure AD B2C
description: Definieren eines technischen PhoneFactor-Profils in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 17e21c28961346cd10b436adaa5eab8c8160bcd4
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131012736"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines technischen PhoneFactor-Profils in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) bietet Unterstützung für das Registrieren und Überprüfen von Telefonnummern. Dieses technische Profil hat folgende Eigenschaften:

- Stellt eine Benutzeroberfläche für die Interaktion mit dem Benutzer bereit, um eine Telefonnummer zu überprüfen oder zu registrieren.
- Zum Überprüfen der Telefonnummer werden sowohl Telefonanrufe als auch Textnachrichten unterstützt.
- Mehrere Telefonnummern werden unterstützt. Der Benutzer kann eine der Telefonnummern zum Überprüfen auswählen.  
- Gibt einen Anspruch zurück, der angibt, ob der Benutzer eine neue Telefonnummer angegeben hat. Mithilfe dieses Anspruchs können Sie entscheiden, ob die Telefonnummer im Azure AD B2C-Benutzerprofil gespeichert werden soll.  
- Zum Steuern von Aussehen und Verhalten werden [Inhaltsdefinitionen](contentdefinitions.md) verwendet.

## <a name="protocol"></a>Protocol

Das **Name**-Attribut des **Protocol**-Elements muss auf `Proprietary` festgelegt werden. Das **handler**-Attribut muss den vollqualifizierten Namen der Protokollhandlerassembly enthalten, die von Azure AD B2C für PhoneFactor verwendet wird: `Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Das folgende Beispiel enthält ein technisches PhoneFactor-Profil für Registrierung und Überprüfung:

```xml
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Eingabeanspruchstransformationen

Das InputClaimsTransformations-Element kann eine Sammlung von Eingabeanspruchstransformationen enthalten, die zum Ändern des Eingabeanspruchs oder zum Generieren eines neuen Eingabeanspruchs verwendet werden. Die folgende Eingabeanspruchstransformation generiert einen `UserId`-Anspruch, der später in der Sammlung von Eingabeansprüchen verwendet wird.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Eingabeansprüche

Das InputClaims-Element muss die folgenden Ansprüche enthalten. Sie können auch den Namen Ihres Anspruchs dem im technischen PhoneFactor-Profil definierten Namen zuordnen. 

|  Datentyp| Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- | 
| Zeichenfolge| Ja | Ein eindeutiger Bezeichner für den Benutzer. Der Anspruchsname oder PartnerClaimType muss auf `UserId` eingestellt sein. Dieser Anspruch sollte keine personenbezogenen Informationen enthalten.|
| Zeichenfolge| Ja | Liste der Anspruchstypen. Jeder Anspruch enthält eine Telefonnummer. Wenn einer der Eingabeansprüche keine Telefonnummer enthält, wird der Benutzer aufgefordert, eine neue Telefonnummer zu registrieren und zu überprüfen. Die validierte Telefonnummer wird als Ausgabeanspruch zurückgegeben. Wenn einer der Eingabeansprüche eine Telefonnummer enthält, wird der Benutzer aufgefordert, sie zu überprüfen. Wenn mehrere Eingabeansprüche eine Telefonnummer enthalten, wird der Benutzer aufgefordert, eine der Telefonnummern auszuwählen und zu überprüfen. |

Das folgende Beispiel veranschaulicht die Verwendung mehrerer Telefonnummern. Weitere Informationen finden Sie unter [Beispielrichtlinie](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Ausgabeansprüche

Das OutputClaims-Element enthält eine Liste von Ansprüchen, die vom technischen PhoneFactor-Profil zurückgegeben werden.

|  Datentyp| Erforderlich | BESCHREIBUNG |
|  -------- | ----------- |----------- |
| boolean | Ja | Gibt an, ob die neue Telefonnummer vom Benutzer eingegeben wurde. Der Anspruchsname oder PartnerClaimType muss auf `newPhoneNumberEntered` eingestellt sein.|
| Zeichenfolge| Ja | Die überprüfte Telefonnummer. Der Anspruchsname oder PartnerClaimType muss auf `Verified.OfficePhone` eingestellt sein.|

Das OutputClaimsTransformations-Element enthält ggf. eine Sammlung von OutputClaimsTransformation-Elementen, die zum Ändern vorhandener oder zum Generieren neuer Ausgabeansprüche verwendet werden.

## <a name="cryptographic-keys"></a>Kryptografische Schlüssel

Das Element **CryptographicKeys** wird nicht verwendet.


## <a name="metadata"></a>Metadaten

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Ja | Der Bezeichner der [Inhaltsdefinition](contentdefinitions.md), die diesem technischen Profil zugeordnet ist. |
| ManualPhoneNumberEntryAllowed| Nein | Gibt an, ob ein Benutzer eine Telefonnummer manuell eingeben darf oder nicht. Mögliche Werte sind `true` oder `false` (Standardwert).|
| setting.authenticationMode | Nein | Die Methode zum Überprüfen der Telefonnummer. Mögliche Werte: `sms`, `phone` oder `mixed` (Standardwert).|
| setting.autodial| Nein| Gibt an, ob das technische Profil eine automatische Anwahl durchführen oder automatisch eine SMS senden soll. Mögliche Werte sind `true` oder `false` (Standardwert). Für die automatische Anwahl müssen die `setting.authenticationMode`-Metadaten auf `sms` oder `phone` eingestellt sein. Die Sammlung der Eingabeansprüche muss eine einzelne Telefonnummer haben. |

### <a name="ui-elements"></a>Benutzeroberflächenelemente

Die Benutzeroberflächenelemente auf der PhoneFactor-Authentifizierungsseite können [lokalisiert](localization-string-ids.md#phone-factor-authentication-page-user-interface-elements) werden.

## <a name="next-steps"></a>Nächste Schritte

- Überprüfen Sie [Social Media- und lokale Konten mit dem MFA Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa).
