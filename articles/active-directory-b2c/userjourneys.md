---
title: UserJourneys
description: Erfahren Sie, wie Sie das UserJourneys-Element einer benutzerdefinierten Richtlinie in Azure Active Directory B2C angeben.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/31/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: d297f2c9e32de22beba8c8bf63d4b62e73e8690c
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131057493"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

User Journeys geben explizite Pfade an, über die eine Richtlinie einer Anwendung der vertrauenden Seite die gewünschten Ansprüche für einen Benutzer abrufen kann. Der Benutzer wird über diese Pfade weitergeleitet, um die Ansprüche abzurufen, die an die vertrauenden Seite übermittelt werden sollen. Das heißt, User Journeys definieren die Geschäftslogik, die ein Endbenutzer durchläuft, während das Framework für die Identitätsfunktion von Azure AD B2C die Anforderung verarbeitet.

Diese User Journeys können als Vorlagen gesehen werden, die verfügbar sind, um die Grundbedürfnisse der verschiedenen vertrauenden Seiten der Interessengemeinschaft abzudecken. User Journeys unterstützen die Definition des Teils der vertrauenden Seite einer Richtlinie. Eine Richtlinie kann mehrere User Journeys definieren. Jede User Journey besteht aus einer Sequenz von Orchestrierungsschritten.

Ein **UserJourneys**-Element wird unter dem allgemeinsten Element der Richtliniendatei hinzugefügt, um die von der Richtlinie unterstützten User Journeys zu definieren.

Das **UserJourneys**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | Eine User Journey, die alle für den Benutzerflow erforderlichen Konstrukte definiert. |

Das **UserJourney**-Element enthält das folgende Attribut:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Id | Ja | Ein Bezeichner einer User Journey, der verwendet werden kann, um über andere Elemente in der Richtlinie auf sie zu verweisen. Das **DefaultUserJourney**-Element der [Richtlinie der vertrauenden Seite](relyingparty.md) zeigt auf dieses Attribut. |
| DefaultCpimIssuerTechnicalProfileReferenceId| Nein | Die Standardverweis-ID für das technische Profil des Tokenausstellers. Beispiel: [JWT-Tokenaussteller](userjourneys.md), [SAML-Tokenaussteller](saml-issuer-technical-profile.md) oder [benutzerdefinierter OAuth2-Fehler](oauth2-error-technical-profile.md). Wenn Ihre User Journey oder untergeordnete Journey bereits über einen anderen `SendClaims`-Orchestrierungsschritt verfügt, legen Sie das Attribut `DefaultCpimIssuerTechnicalProfileReferenceId` auf das technische Profil des Tokenausstellers fest. |

Das **UserJourney**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfiles | 0:1 | Liste der technischen Profile für die Autorisierung | 
| OrchestrationSteps | 1:n | Eine Orchestrierungssequenz, die für eine erfolgreiche Transaktion durchlaufen werden muss. Jede User Journey besteht aus einer geordneten List von Orchestrierungsschritten, die nacheinander ausgeführt werden. Wenn ein Schritt fehlschlägt, schlägt die Transaktion fehl. |

## <a name="authorizationtechnicalprofiles"></a>AuthorizationTechnicalProfiles

Angenommen, ein Benutzer hat eine User Journey abgeschlossen und ein Zugriffs- oder ID-Token erhalten. Um weitere Ressourcen zu verwalten (z. B. den [UserInfo-Endpunkt](userinfo-endpoint.md)), muss der Benutzer identifiziert werden. Um diesen Vorgang zu starten, muss der Benutzer das zuvor ausgegebene Zugriffstoken als Nachweis präsentieren, dass er ursprünglich durch eine gültige Azure AD B2C-Richtlinie authentifiziert wurde. Während dieses Vorgangs muss stets ein gültiges Token für den Benutzer vorhanden sein, um sicherzustellen, dass er diese Anforderung ausführen darf. Die technischen Profile für die Autorisierung überprüfen das eingehende Token und extrahieren daraus Ansprüche.

Das **AuthorizationTechnicalProfiles**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfile | 0:1 | Liste der technischen Profile für die Autorisierung | 

Das **AuthorizationTechnicalProfile**-Element enthält das folgende Attribut:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| TechnicalProfileReferenceId | Ja | Der Bezeichner des technischen Profils, das ausgeführt werden soll. |

Das folgende Beispiel zeigt ein User Journey-Element mit technischen Profilen für die Autorisierung:

```xml
<UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
  <Authorization>
    <AuthorizationTechnicalProfiles>
      <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
    </AuthorizationTechnicalProfiles>
  </Authorization>
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsExchange">
     ...
```

## <a name="orchestrationsteps"></a>OrchestrationSteps

Eine User Journey wird als Orchestrierungssequenz dargestellt, die für eine erfolgreiche Transaktion durchlaufen werden muss. Wenn ein Schritt fehlschlägt, schlägt die Transaktion fehl. Diese Orchestrierungsschritte verweisen sowohl auf die Bausteine als auch auf die Anspruchsanbieter, die in der Richtliniendatei zugelassen werden. Jeder Orchestrierungsschritt, der für das Anzeigen oder Rendern einer Benutzeroberfläche verantwortlich ist, verfügt auch über einen Verweis auf den Bezeichner für die entsprechende Inhaltsdefinition.

Orchestrierungsschritte können anhand von Voraussetzungen, die im OrchestrationSteps-Element definiert werden, bedingungsabhängig ausgeführt werden. Beispielsweise können Sie konfigurieren, dass ein Orchestrierungsschritt nur ausgeführt wird, wenn ein bestimmter Anspruch vorhanden ist oder ein Anspruch dem angegebenen Wert entspricht (oder nicht).

Ein **OrchestrationSteps**-Element wird als Teil der Richtlinie hinzugefügt, um die geordnete Liste der Orchestrierungsschritte festzulegen. Dieses Element ist erforderlich.

Das **OrchestrationSteps**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1:n | Ein geordneter Orchestrierungsschritt. |

Das **OrchestrationStep**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| `Order` | Ja | Die Reihenfolge der Orchestrierungsschritte. |
| `Type` | Ja | Der Typ des Orchestrierungsschritts. Mögliche Werte: <ul><li>**ClaimsProviderSelection:** Gibt an, dass der Orchestrierungsschritt verschiedene Anspruchsanbieter darstellt, von denen der Benutzer einen auswählen kann.</li><li>**CombinedSignInAndSignUp:** Gibt an, dass der Orchestrierungsschritt eine kombinierte Seite für die Anmeldung eines Social Media-Anbieters und die Registrierung eines lokalen Kontos darstellt.</li><li>**ClaimsExchange:** Gibt an, dass der Orchestrierungsschritt Ansprüche mit einem Anspruchsanbieter austauscht.</li><li>**GetClaims:** Gibt an, dass der Orchestrierungsschritt Anspruchsdaten verarbeiten soll, die von der vertrauenden Seite über ihre `InputClaims`-Konfiguration an Azure AD B2C gesendet werden.</li><li>**InvokeSubJourney**: Gibt an, dass der Orchestrierungsschritt Ansprüche mit einer [untergeordneten Journey](subjourneys.md) (in der öffentlichen Vorschau) austauscht.</li><li>**SendClaims:** Gibt an, dass der Orchestrierungsschritt die Ansprüche an die vertrauende Seite mit einem Token übermittelt, das von einem Anspruchsaussteller ausgestellt wurde.</li></ul> |
| ContentDefinitionReferenceId | Nein | Der Bezeichner der [Inhaltsdefinition](contentdefinitions.md), die diesem Orchestrierungsschritt zugeordnet ist. In der Regel wird der Bezeichner für den Verweis auf die Inhaltsdefinition im selbstbestätigten technischen Profil definiert. Jedoch gibt es einige Fälle, in denen Azure AD B2C etwas ohne technisches Profil anzeigen muss. Hier sind zwei Beispiele: Wenn der Orchestrierungsschritt vom Typ `ClaimsProviderSelection` oder `CombinedSignInAndSignUp` ist, muss Azure AD B2C die Auswahl des Identitätsanbieters auch ohne technisches Profil anzeigen. |
| CpimIssuerTechnicalProfileReferenceId | Nein | Der Typ des Orchestrierungsschritts ist `SendClaims`. Diese Eigenschaft definiert den Bezeichner für das technische Profil des Anspruchsanbieters, der das Token für die vertrauende Seite ausstellt.  Wenn sie nicht vorhanden ist, wird kein Token für die vertrauende Seite erstellt. |

Das **OrchestrationStep**-Element kann die folgenden Elemente enthalten:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Preconditions | 0:n | Eine Liste von Voraussetzungen, die für die Ausführung des Orchestrierungsschritts erfüllt sein müssen. |
| ClaimsProviderSelections | 0:n | Eine Liste von Auswahloptionen für Anspruchsanbieter für den Orchestrierungsschritts. |
| ClaimsExchanges | 0:n | Eine Liste von Anspruchsaustauschvorgängen für den Orchestrierungsschritt. |
| JourneyList | 0:1 | Eine Liste der möglichen untergeordneten Journeys für den Orchestrierungsschritt. |

### <a name="preconditions"></a>Preconditions

Orchestrierungsschritte können anhand von Voraussetzungen, die im Orchestrierungsschritt definiert werden, bedingungsabhängig ausgeführt werden. Das `Preconditions`-Element enthält eine Liste der auszuwertenden Vorbedingungen. Wenn die Vorbedingungsauswertung erfüllt ist, wird der zugeordnete Orchestrierungsschritt mit dem nächsten Orchestrierungsschritt übersprungen. 

Azure AD B2C wertet die Vorbedingungen in Listenreihenfolge aus. Mit den auf der Reihenfolge basierenden Vorbedingungen können Sie die Reihenfolge festlegen, in der die Vorbedingungen angewendet werden. Die erste erfüllte Vorbedingung überschreibt alle nachfolgenden Vorbedingungen. Der Orchestrierungsschritt wird nur ausgeführt, wenn nicht alle Vorbedingungen erfüllt sind. 

Das **Preconditions**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Precondition | 1:n | Eine auszuwertende Vorbedingung. |

#### <a name="precondition"></a>Precondition

Das **Precondition**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| `Type` | Ja | Der Typ der Überprüfung oder Abfrage, die für diese Voraussetzung ausgeführt werden soll. Dieser Wert kann **ClaimsExist** sein, wodurch festgelegt wird, dass die Aktionen durchgeführt werden sollen, wenn die angegebenen Ansprüche in den aktuellen Ansprüchen des Benutzers vorhanden sind. Alternativ kann der Wert **ClaimEquals** sein, wodurch festgelegt wird, dass die Aktionen durchgeführt werden sollen, wenn der angegebene Anspruch vorhanden ist und sein Wert dem angegebenen Wert gleicht. |
| `ExecuteActionsIf` | Ja | Entscheidet, wie die Vorbedingung als erfüllt betrachtet wird. Mögliche Werte: `true` (Standard) oder `false`. Wenn der Wert auf `true` festgelegt ist, wird sie als erfüllt betrachtet, wenn der Anspruch mit der Vorbedingung übereinstimmt.  Wenn der Wert auf `false` festgelegt ist, wird sie als erfüllt betrachtet, wenn der Anspruch nicht mit der Vorbedingung übereinstimmt.  |

Das **Precondition**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Wert | 1:2 | Der Bezeichner eines Anspruchstyps. Der Anspruch ist bereits im Abschnitt für das Anspruchsschema in der Richtliniendatei oder der übergeordneten Richtliniendatei definiert. Bei Verwendung einer Vorbedingung vom Typ `ClaimEquals` enthält ein zweites Element vom Typ `Value` den zu überprüfenden Wert. |
| Aktion | 1:1 | Die Aktion, die ausgeführt werden soll, wenn die Auswertung der Vorbedingung erfüllt ist. Möglicher Wert: `SkipThisOrchestrationStep`. Der zugeordnete Orchestrierungsschritt wird mit dem nächsten Schritt übersprungen. |
  
Jede Vorbedingung wertet einen einzelnen Anspruch aus. Es gibt zwei Arten von Vorbedingungen:
 
- **ClaimsExist**: Gibt an, dass die Aktionen ausgeführt werden sollen, wenn der aktuelle Anspruchsbehälter des Benutzers die angegebenen Ansprüche enthält.
- **ClaimEquals**: Gibt an, dass die Aktionen ausgeführt werden sollen, wenn der angegebene Anspruch vorhanden ist und dessen Wert dem angegebenen Wert entspricht. Bei der Überprüfung wird ein Ordinalvergleich unter Beachtung der Groß-/Kleinschreibung durchgeführt. Verwenden Sie bei der Überprüfung eines booleschen Anspruchstyps `True` oder `False`. 

    Wenn der Anspruch NULL oder nicht initialisiert ist, wird die Vorbedingung ignoriert, unabhängig davon, ob `ExecuteActionsIf` den Wert `true` oder `false` aufweist. Als bewährte Methode sollten Sie überprüfen, ob der Anspruch vorhanden ist und einem bestimmten Wert entspricht.

Ein Beispielszenario wäre, den Benutzer zur MFA aufzufordern, wenn der Benutzer `MfaPreference` auf `Phone` festgelegt hat. Um diese bedingte Logik durchzuführen, überprüfen Sie, ob der Anspruch `MfaPreference`vorhanden ist und überprüfen Sie außerdem, ob der Anspruchswert gleich `Phone` ist. Im folgenden XML-Code wird veranschaulicht, wie diese Logik mit Vorbedingungen implementiert wird. 
  
```xml
<Preconditions>
  <!-- Skip this orchestration step if MfaPreference doesn't exist. -->
  <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
    <Value>MfaPreference</Value>
    <Action>SkipThisOrchestrationStep</Action>
  </Precondition>
  <!-- Skip this orchestration step if MfaPreference doesn't equal to Phone. -->
  <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
    <Value>MfaPreference</Value>
    <Value>Phone</Value>
    <Action>SkipThisOrchestrationStep</Action>
  </Precondition>
</Preconditions>
```

#### <a name="preconditions-examples"></a>Beispiele für Voraussetzungen

Die folgenden Voraussetzungen überprüfen, ob der objectId-Wert des Benutzers vorhanden ist. Der Benutzer hat in der User Journey die Anmeldung mit einem lokalen Konto ausgewählt. Überspringen Sie diesen Orchestrierungsschritt, wenn der objectID-Wert vorhanden ist.

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Die folgenden Voraussetzungen überprüfen, ob der Benutzer sich mit einem Social Media-Konto angemeldet hat. Es wird versucht, das Benutzerkonto im Verzeichnis zu finden. Überspringen Sie diesen Orchestrierungsschritt, wenn sich der Benutzer mit einem lokalen Konto anmeldet oder registriert.

```xml
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Mit einem Preconditions-Element können mehrere Voraussetzungen überprüft werden. Im folgenden Beispiel wird überprüft, ob die Werte „objectId“ und „email“ vorhanden sind. Wenn die erste Bedingung TRUE lautet, springt die User Journey zum nächsten Orchestrierungsschritt.

```xml
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claims-provider-selection"></a>Anspruchsanbieterauswahl

Mit der Identitätsanbieterauswahl können Benutzer eine Aktion aus einer Liste von Optionen auswählen. Die Identitätsanbieterauswahl besteht aus einem Paar von Orchestrierungsschritten: 

1. **Schaltflächen:** Sie beginnt mit dem Typ `ClaimsProviderSelection` oder mit dem Typ `CombinedSignInAndSignUp`, der eine Liste von Optionen enthält, aus denen der Benutzer wählen kann. Die Reihenfolge der Optionen innerhalb des Elements `ClaimsProviderSelections` steuert die Reihenfolge der Schaltflächen, die dem Benutzer angezeigt werden.
2. **Aktionen:** Danach folgt der Typ `ClaimsExchange`. „ClaimsExchange“ enthält eine Liste von Aktionen. Die Aktion ist ein Verweis auf ein technisches Profil wie [OAuth2](oauth2-technical-profile.md), [OpenID Connect](openid-connect-technical-profile.md), [Anspruchstransformation](claims-transformation-technical-profile.md) oder [Selbstbestätigt](self-asserted-technical-profile.md). Wenn ein Benutzer auf eine der Schaltflächen klickt, wird die entsprechende Aktion ausgeführt.

Das **ClaimsProviderSelections**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 1:n | Stellt die Liste von Anspruchsanbietern bereit, die ausgewählt werden können.|

Das **ClaimsProviderSelections**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| DisplayOption| Nein | Steuert das Verhalten in dem Fall, wenn nur eine Anspruchsanbieterauswahl verfügbar ist. Mögliche Werte: `DoNotShowSingleProvider` (Standardwert) – der Benutzer wird sofort an den Verbundidentitätsanbieter umgeleitet. Ein weiterer möglicher Wert ist `ShowSingleProvider` – Azure AD B2C zeigt die Anmeldeseite mit der Auswahl eines einzelnen Identitätsanbieters an. Damit dieses Attribut verwendet werden kann, muss die [Version der Inhaltsdefinition](page-layout.md) `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` oder höher lauten.|

Das **ClaimsProviderSelection**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | Nein  | Der Bezeichner des Anspruchsaustauschs, der im nächsten Orchestrierungsschritt der Auswahl des Anspruchsanbieters ausgeführt wird. Dieses oder das ValidationClaimsExchangeId-Attribut muss angegeben werden, aber nicht beide. |
| ValidationClaimsExchangeId | Nein | Der Bezeichner des Anspruchsaustauschs, der im aktuellen Orchestrierungsschritt zur Validierung der Auswahl des Anspruchsanbieters ausgeführt wird. Dieses oder das TargetClaimsExchangeId-Attribut muss angegeben werden, aber nicht beide. |

### <a name="claims-provider-selection-example"></a>Beispiel für die Anspruchsanbieterauswahl

Im folgenden Orchestrierungsschritt kann der Benutzer auswählen, ob er sich über Facebook, LinkedIn, Twitter, Google oder ein lokales Konto anmelden möchte. Wenn der Benutzer einen der Social Media-Identitätsanbieter auswählt, wird der zweite Orchestrierungsschritt mit dem ausgewählten Anspruchsaustausch ausgeführt, der im `TargetClaimsExchangeId`-Attribut angegeben wurde. Der zweite Orchestrierungsschritt leitet den Benutzer an den Social Media-Identitätsanbieter weiter, um den Anmeldevorgang abzuschließen. Wenn der Benutzer sich dazu entscheidet, sich mit einem lokalen Konto anzumelden, bleibt Azure AD B2C beim gleichen Orchestrierungsschritt (die gleiche Registrierungs- oder Anmeldeseite) und überspringt den zweiten Orchestrierungsschritt.

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
  </ClaimsProviderSelections>
  <ClaimsExchanges>
  <ClaimsExchange Id="LocalAccountSigninEmailExchange"
        TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
  </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

Das **ClaimsExchanges**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1:n | Abhängig vom verwendeten technischen Profil, wird der Client entweder gemäß des ClaimsProviderSelection-Elements weitergeleitet oder ein Serveraufruf wird zum Austauschen von Ansprüchen ausgeführt. |

Das **ClaimsExchange**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Id | Ja | Ein Bezeichner für den Schritt mit dem Anspruchsaustausch. Der Bezeichner wird verwendet, um in der Richtlinie auf den Anspruchsaustausch eines Schritts für die Auswahl des Anspruchsanbieters zu verweisen. |
| TechnicalProfileReferenceId | Ja | Der Bezeichner des technischen Profils, das ausgeführt werden soll. |

## <a name="journeylist"></a>JourneyList

Das **JourneyList**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Kandidat | 1:1 | Ein Verweis auf eine untergeordnete Journey, die aufgerufen werden soll. |

### <a name="candidate"></a>Kandidat

Das **Candidate**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| SubJourneyReferenceId | Ja | Der Bezeichner der [untergeordneten Journey](subjourneys.md), die ausgeführt werden soll. |
