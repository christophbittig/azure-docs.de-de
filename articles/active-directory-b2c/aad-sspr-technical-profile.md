---
title: Technische Profile für die Azure AD-Self-Service-Kennwortzurücksetzung in benutzerdefinierten Richtlinien
titleSuffix: Azure AD B2C
description: Referenz zu benutzerdefinierten Richtlinien für technische Profile für die Azure AD-Self-Service-Kennwortzurücksetzung in Azure AD B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/23/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 8c1bac41a0c70a2d9dff2a8ce1ac5544ad687fe5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008368"
---
# <a name="define-an-azure-ad-sspr-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definieren eines technischen Profils für die Azure AD-Self-Service-Kennwortzurücksetzung in einer benutzerdefinierten Azure AD B2C-Richtlinie

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Von Azure Active Directory B2C (Azure AD B2C) wird die Überprüfung einer E-Mail-Adresse für die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) unterstützt. Verwenden Sie das technische Profil für die Azure AD-Self-Service-Kennwortzurücksetzung, um einen Code zu generieren, an eine E-Mail-Adresse zu senden und anschließend zu überprüfen. Vom technischen Profil für die Azure AD-Self-Service-Kennwortzurücksetzung kann auch eine Fehlermeldung zurückgegeben werden. Mit dem technischen Validierungsprofil werden die vom Benutzer bereitgestellten Daten überprüft, bevor die User Journey fortgesetzt wird. Mit dem technischen Überprüfungsprofil wird eine Fehlermeldung auf einer Seite mit Selbstbestätigung angezeigt.

Dieses technische Profil hat folgende Eigenschaften:

- Es bietet keine Benutzeroberfläche für die Interaktion mit dem Benutzer. Stattdessen wird die Benutzeroberfläche von einem [ selbstbestätigten](self-asserted-technical-profile.md) technischen Profil oder einem [Anzeigesteuerelement](display-controls.md) als [ technisches Überprüfungsprofil](validation-technical-profile.md) aufgerufen.
- Es verwendet den Azure AD-SSPR-Dienst, um einen Code zu generieren und an eine E-Mail-Adresse zu senden, und überprüft ihn anschließend.
- Es überprüft eine E-Mail-Adresse mithilfe eines Überprüfungscodes.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol

Das **Name**-Attribut des **Protocol**-Elements muss auf `Proprietary` festgelegt werden. Das **handler**-Attribut muss den vollqualifizierten Namen der Protokollhandlerassembly, die von Azure AD B2C verwendet wird, enthalten:

```
Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Das folgende Beispiel zeigt ein technisches Profil für die Azure AD-Self-Service-Kennwortzurücksetzung:

```xml
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-email"></a>Senden von E-Mail

Der erste Modus dieses technischen Profils besteht darin, einen Code zu generieren und zu senden. Für diesen Modus können folgende Optionen konfiguriert werden:

### <a name="input-claims"></a>Eingabeansprüche

Das Element **InputClaims** enthält eine Liste von Ansprüchen, die an die Azure AD-Self-Service-Kennwortzurücksetzung gesendet werden sollen. Sie können auch den Namen Ihres Anspruchs dem im technischen SSPR-Profil definierten Namen zuordnen.

| ClaimReferenceId | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| emailAddress | Ja | Der Bezeichner für den Benutzer, dem die E-Mail-Adresse gehört. Die Eigenschaft `PartnerClaimType` des Eingabeanspruchs muss auf `emailAddress` festgelegt werden. |

Das Element **InputClaimsTransformations** enthält ggf. eine Sammlung von Elementen vom Typ **InputClaimsTransformation**, die vor dem Senden an den Azure AD-SSPR-Dienst zum Ändern der Eingabeansprüche oder zum Generieren neuer Eingabeansprüche verwendet werden.

### <a name="output-claims"></a>Ausgabeansprüche

Da vom Azure AD-SSPR-Protokollanbieter keine Elemente vom Typ **OutputClaims** zurückgegeben werden, müssen auch keine Ausgabeansprüche angegeben werden. Sie können jedoch Ansprüche einschließen, die nicht vom Azure AD-SSPR-Protokollanbieter zurückgegeben werden (vorausgesetzt, Sie legen das Attribut `DefaultValue` fest).

Das **OutputClaimsTransformations**-Element darf eine Sammlung von **OutputClaimsTransformation**-Elementen, die zum Ändern der Ausgabeansprüche oder zum Generieren neuer verwendet werden, enthalten.

### <a name="metadata"></a>Metadaten

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Vorgang | Ja | Muss **SendCode** lauten.  |

#### <a name="ui-elements"></a>Benutzeroberflächenelemente

Die folgenden Metadaten können verwendet werden, um die Fehlermeldungen zu konfigurieren, die bei einem SMS-Sendefehler angezeigt wird. Die Metadaten sollten im [selbstbestätigten](self-asserted-technical-profile.md) technischen Profil konfiguriert werden. Die Fehlermeldungen können [lokalisiert](localization-string-ids.md#azure-ad-sspr) werden.

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| UserMessageIfInternalError | Nein | Fehlermeldung für den Benutzer, wenn der Server einen internen Fehler festgestellt hat. |
| UserMessageIfThrottled| Nein | Fehlermeldung für den Benutzer, wenn eine Anforderung gedrosselt wurde.|

### <a name="example-send-an-email"></a>Beispiel: Senden einer E-Mail

Das folgende Beispiel zeigt ein technisches Profil für die Azure AD-Self-Service-Kennwortzurücksetzung, das verwendet wird, um einen Code per E-Mail zu senden.

```xml
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">SendCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Code überprüfen

Der zweite Modus dieses technischen Profils besteht darin, einen Code zu überprüfen. Für diesen Modus können folgende Optionen konfiguriert werden:

### <a name="input-claims"></a>Eingabeansprüche

Das Element **InputClaims** enthält eine Liste von Ansprüchen, die an die Azure AD-Self-Service-Kennwortzurücksetzung gesendet werden sollen. Sie können auch den Namen Ihres Anspruchs dem im technischen SSPR-Profil definierten Namen zuordnen.

| ClaimReferenceId | Erforderlich | Beschreibung |
| --------- | -------- | ----------- | ----------- |
| emailAddress| Ja | Die gleiche E-Mail-Adresse, die zuvor zum Senden eines Codes verwendet wurde. Wird auch für die Suche nach einer E-Mail-Überprüfungssitzung verwendet. Die Eigenschaft `PartnerClaimType` des Eingabeanspruchs muss auf `emailAddress` festgelegt werden.|
| verificationCode  | Ja | Der vom zu überprüfenden Benutzer angegebene Prüfcode. Die Eigenschaft `PartnerClaimType` des Eingabeanspruchs muss auf `verificationCode` festgelegt werden. |

Das Element **InputClaimsTransformations** enthält ggf. eine Sammlung von Elementen vom Typ **InputClaimsTransformation**, die vor dem Aufrufen des Azure AD-SSPR-Diensts zum Ändern der Eingabeansprüche oder zum Generieren neuer Eingabeansprüche verwendet werden.

### <a name="output-claims"></a>Ausgabeansprüche

Da vom Azure AD-SSPR-Protokollanbieter keine Elemente vom Typ **OutputClaims** zurückgegeben werden, müssen auch keine Ausgabeansprüche angegeben werden. Sie können jedoch Ansprüche einschließen, die nicht vom Azure AD-SSPR-Protokollanbieter zurückgegeben werden (vorausgesetzt, Sie legen das Attribut `DefaultValue` fest).

Das **OutputClaimsTransformations**-Element darf eine Sammlung von **OutputClaimsTransformation**-Elementen, die zum Ändern der Ausgabeansprüche oder zum Generieren neuer verwendet werden, enthalten.

### <a name="metadata"></a>Metadaten

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Vorgang | Ja | Muss **VerifyCode** lauten. |

#### <a name="ui-elements"></a>Benutzeroberflächenelemente

Die folgenden Metadaten können verwendet werden, um die Fehlermeldungen zu konfigurieren, die bei einem Codeüberprüfungsfehler angezeigt wird. Die Metadaten sollten im [selbstbestätigten](self-asserted-technical-profile.md) technischen Profil konfiguriert werden. Die Fehlermeldungen können [lokalisiert](localization-string-ids.md#azure-ad-sspr) werden.

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
|UserMessageIfChallengeExpired | Die Meldung, die dem Benutzer angezeigt werden soll, wenn die Codeüberprüfungssitzung abgelaufen ist. Der Code ist entweder abgelaufen oder wurde nie für einen bestimmten Bezeichner generiert.|
|UserMessageIfInternalError | Fehlermeldung für den Benutzer, wenn der Server einen internen Fehler festgestellt hat.|
|UserMessageIfThrottled | Fehlermeldung für den Benutzer, wenn eine Anforderung gedrosselt wurde.|
|UserMessageIfVerificationFailedNoRetry | Die Meldung, die dem Benutzer angezeigt werden soll, wenn ein ungültiger Code angegeben wurde und der Benutzer keine Gelegenheit erhält, den richtigen Code anzugeben.|
|UserMessageIfVerificationFailedRetryAllowed | Die Meldung, die dem Benutzer angezeigt werden soll, wenn ein ungültiger Code angegeben wurde und der Benutzer die Gelegenheit erhält, den richtigen Code anzugeben.|

### <a name="example-verify-a-code"></a>Beispiel: Überprüfen eines Codes

Das folgende Beispiel zeigt ein technisches Profil für die Azure AD-Self-Service-Kennwortzurücksetzung, das zum Überprüfen des Codes verwendet wird:

```xml
<TechnicalProfile Id="AadSspr-VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="verificationCode" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```
