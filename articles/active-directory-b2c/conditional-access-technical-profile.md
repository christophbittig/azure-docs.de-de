---
title: Technische Profile für den bedingten Zugriff in benutzerdefinierten Richtlinien
titleSuffix: Azure AD B2C
description: Referenz zu benutzerdefinierten Richtlinien für technische Profile für den bedingten Zugriff in Azure AD B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/18/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: d44ae627279131004bdac2f755eb26b59e375381
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007950"
---
# <a name="define-a-conditional-access-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines technischen Profils für den bedingten Zugriff in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Über den bedingten Zugriff in Azure Active Directory (Azure AD) werden in Azure AD B2C Signale zusammengeführt, Entscheidungen getroffen und Organisationsrichtlinien erzwungen. Die Automatisierung der Risikobewertung mit Richtlinienbedingungen bedeutet, dass riskante Anmeldungen sofort erkannt und behoben oder blockiert werden.

## <a name="protocol"></a>Protocol

Das **Name**-Attribut des **Protocol**-Elements muss auf `Proprietary` festgelegt werden. Das **handler**-Attribut muss den vollqualifizierten Namen der Protokollhandlerassembly, die von Azure AD B2C verwendet wird, enthalten:

```
Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Das folgende Beispiel zeigt ein technisches Profil für den bedingten Zugriff:

```xml
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
```

## <a name="conditional-access-evaluation"></a>Auswertung des bedingten Zugriffs

Bei jeder Anmeldung werden in Azure AD B2C alle Richtlinien ausgewertet, und es wird sichergestellt, dass alle Anforderungen erfüllt sind, bevor dem Benutzer der Zugriff gewährt wird. „Zugriff blockieren“ setzt alle übrigen Konfigurationseinstellungen außer Kraft. Im Modus **Evaluation** des technischen Profils für den bedingten Zugriff werden die bei der Anmeldung mit einem lokalen Konto in Azure AD B2C erfassten Signale ausgewertet. Das Ergebnis des technischen Profils für den bedingten Zugriff ist eine Reihe von Ansprüchen, die sich aus der Auswertung des bedingten Zugriffs ergeben. In der Azure AD B2C-Richtlinie werden diese Ansprüche in einem nächsten Orchestrierungsschritt verwendet, um eine Aktion auszuführen, z. B. den Benutzer zu blockieren oder den Benutzer zur mehrstufigen Authentifizierung aufzufordern. Für diesen Modus können folgende Optionen konfiguriert werden:

### <a name="metadata"></a>Metadaten

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| OperationType | Ja | Muss **Evaluation** sein.  |

### <a name="input-claims"></a>Eingabeansprüche

Das Element **InputClaims** enthält eine Liste von Ansprüchen, die an den bedingten Zugriff gesendet werden. Sie können auch den Namen des Anspruchs dem im technischen Profil für den bedingten Zugriff definierten Namen zuordnen.

| ClaimReferenceId | Erforderlich | Datentyp | BESCHREIBUNG |
| --------- | -------- | ----------- |----------- |
| UserId | Ja | Zeichenfolge | Der Bezeichner des Benutzers, der sich anmeldet. |
| AuthenticationMethodsUsed | Ja |stringCollection | Die Liste der Methoden, die der Benutzer für die Anmeldung verwendet hat. Mögliche Werte: `Password` und `OneTimePasscode`. |
| IsFederated | Ja |boolean | Gibt an, ob ein Benutzer mit einem Verbundkonto angemeldet ist oder nicht. Der Wert muss `false` sein. |
| IsMfaRegistered | Ja |boolean | Gibt an, ob der Benutzer bereits eine Telefonnummer für die mehrstufige Authentifizierung registriert hat. |

Das Element **InputClaimsTransformations** enthält ggf. eine Sammlung von Elementen vom Typ **InputClaimsTransformation**, die vor dem Senden an den Dienst für den bedingten Zugriff zum Ändern der Eingabeansprüche oder zum Generieren neuer Eingabeansprüche verwendet werden.

### <a name="output-claims"></a>Ausgabeansprüche

Das Element **OutputClaims** enthält eine Liste der Ansprüche, die von ConditionalAccessProtocolProvider generiert werden. Sie können auch den Namen Ihres Anspruchs dem unten definierten Namen zuordnen.

| ClaimReferenceId | Erforderlich | Datentyp | BESCHREIBUNG |
| --------- | -------- | ----------- |----------- |
| Herausforderungen | Ja |stringCollection | Liste der Aktionen zum Korrigieren der ermittelten Bedrohung. Mögliche Werte: `block`, `mfa` und `chg_pwd`. |
| MultiConditionalAccessStatus | Ja | stringCollection | Der Status der Auswertung des bedingten Zugriffs.  |

Das **OutputClaimsTransformations**-Element darf eine Sammlung von **OutputClaimsTransformation**-Elementen, die zum Ändern der Ausgabeansprüche oder zum Generieren neuer verwendet werden, enthalten.

### <a name="example-evaluation"></a>Beispiel: Auswertung

Das folgende Beispiel zeigt ein technisches Profil für den bedingten Zugriff, das zur Auswertung der Anmeldebedrohung verwendet wird.

```xml
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="IsMfaRegisteredCT" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="UserId" />
    <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" />
    <InputClaim ClaimTypeReferenceId="IsFederated" DefaultValue="false" />
    <InputClaim ClaimTypeReferenceId="IsMfaRegistered" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="Challenges" />
    <OutputClaim ClaimTypeReferenceId="ConditionalAccessStatus" PartnerClaimType="MultiConditionalAccessStatus" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="remediation"></a>Wartung

Im Modus **Remediation** des technischen Profils für den bedingten Zugriff wird in Azure AD B2C angegeben, dass die bei der Anmeldung identifizierte Bedrohung korrigiert wurde. Für den Modus „Remediation“ können folgende Optionen konfiguriert werden.

### <a name="metadata"></a>Metadaten

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| OperationType | Ja | Muss **Remediation** sein.  |

### <a name="input-claims"></a>Eingabeansprüche

Das Element **InputClaims** enthält eine Liste von Ansprüchen, die an den bedingten Zugriff gesendet werden. Sie können auch den Namen des Anspruchs dem im technischen Profil für den bedingten Zugriff definierten Namen zuordnen.

| ClaimReferenceId | Erforderlich | Datentyp | BESCHREIBUNG |
| --------- | -------- | ----------- |----------- |
| ChallengesSatisfied | Ja | stringCollection| Die im Modus „Evaluation“ im Rahmen des Anforderungsanspruchs zurückgegebene Liste der erfüllten Anforderungen zum Korrigieren der identifizierten Bedrohung.|

Das Element **InputClaimsTransformations** enthält ggf. eine Sammlung von Elementen vom Typ **InputClaimsTransformation**, die vor dem Aufrufen des Diensts für den bedingten Zugriff zum Ändern der Eingabeansprüche oder zum Generieren neuer Eingabeansprüche verwendet werden.

### <a name="output-claims"></a>Ausgabeansprüche

Da vom Protokollanbieter für den bedingten Zugriff keine Elemente vom Typ **OutputClaims** zurückgegeben werden, müssen auch keine Ausgabeansprüche angegeben werden. Sie können jedoch Ansprüche einfügen, die nicht vom Protokollanbieter für den bedingten Zugriff zurückgegeben werden (vorausgesetzt, Sie legen das Attribut `DefaultValue` fest).

Das **OutputClaimsTransformations**-Element darf eine Sammlung von **OutputClaimsTransformation**-Elementen, die zum Ändern der Ausgabeansprüche oder zum Generieren neuer verwendet werden, enthalten.

### <a name="example-remediation"></a>Beispiel: Wiederherstellung

Das folgende Beispiel zeigt ein technisches Profil für den bedingten Zugriff, das zur Auflösung der Anmeldebedrohung verwendet wird.

```xml
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="ChallengesSatisfied" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über das [Hinzufügen von bedingtem Zugriff zu Benutzerflows in Azure Active Directory B2C](conditional-access-user-flow.md).
