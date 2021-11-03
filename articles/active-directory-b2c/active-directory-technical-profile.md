---
title: Definieren eines technischen Azure AD-Profils in einer benutzerdefinierten Richtlinie
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie ein technisches Azure Active Directory-Profil in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C definieren.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 08c2def39cf3fc673d83f1f1e371544eb679c45e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131008330"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines technischen Azure Active Directory-Profils in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) bietet Unterstützung für die Azure Active Directory-Benutzerverwaltung. In diesem Artikel werden die Einzelheiten eines technischen Profils für die Interaktion mit einem Anspruchsanbieter thematisiert, der dieses standardisierte Protokoll unterstützt.

## <a name="protocol"></a>Protocol

Das **Name**-Attribut des **Protocol**-Elements muss auf `Proprietary` festgelegt werden. Das **handler**-Attribut muss den vollqualifizierten Namen der Protokollhandlerassembly `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` enthalten.

Entsprechend dem [Starter Pack für benutzerdefinierte Richtlinien](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack) enthalten alle technischen Azure AD-Profile das technische Profil **AAD-Common**. Die folgenden technischen Azure AD-Profile geben das Protokoll nicht an, da es im technischen Profil **AAD-Common** konfiguriert ist:
 
- **AAD-UserReadUsingAlternativeSecurityId** und **AAD-UserReadUsingAlternativeSecurityId-NoError**: Suchen eines Social Media-Kontos im Verzeichnis.
- **AAD-UserWriteUsingAlternativeSecurityId:** Erstellen eines neuen Social Media-Kontos.
- **AAD-UserReadUsingEmailAddress:** Suchen eines lokalen Kontos im Verzeichnis.
- **AAD-UserWriteUsingLogonEmail:** Erstellen eines neuen lokalen Kontos.
- **AAD-UserWritePasswordUsingObjectId:** Aktualisieren des Kennworts eines lokalen Kontos.
- **AAD-UserWriteProfileUsingObjectId:** Aktualisieren eines Benutzerprofils eines lokalen oder Social Media-Kontos.
- **AAD-UserReadUsingObjectId:** Lesen eines Benutzerprofils eines lokalen oder Social Media-Kontos.
- **AAD-UserWritePhoneNumberUsingObjectId:** Schreiben der MFA-Telefonnummer eines lokalen oder Social Media-Kontos

Das folgende Beispiel zeigt das technische Profil **AAD-Common**:

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="inputclaims"></a>InputClaims

Das „Input Claims“-Element enthält einen Anspruch, der verwendet wird, um im Verzeichnis nach einem Konto zu suchen oder ein neues Konto zu erstellen. In der Sammlung von Eingabeansprüchen für alle technischen Azure AD-Profile muss genau ein „InputClaim“-Element vorhanden sein. Sie müssen den Namen des Anspruchs, der in Ihrer Richtlinie definiert ist, dem Namen, der in Azure Active Directory definiert wurde, zuordnen.

Beim Lesen, Aktualisieren oder Löschen eines vorhandenen Benutzerkontos ist der Eingabeanspruch ein Schlüssel, der das Konto in Azure AD-Verzeichnis eindeutig identifiziert. Zum Beispiel: **objectId**, **userPrincipalName**, **signInNames.emailAddress**, **signInNames.userName** oder **alternativeSecurityId**. 

Beim Erstellen eines neuen Benutzerkontos ist der Eingabeanspruch ein Schlüssel, der ein lokales oder Verbundkonto eindeutig identifiziert. Zum Beispiel bei einem lokalen Konto: **signInNames.emailAddress** oder **signInNames.userName**. Bei einem Verbundkonto: **alternativeSecurityId**.

Das [InputClaimsTransformations](technicalprofiles.md#input-claims-transformations)-Element kann eine Sammlung von Eingabeanspruchstransformations-Elementen enthalten, die zum Ändern des Eingabeanspruchs oder zum Generieren eines neuen Eingabeanspruchs verwendet werden.

## <a name="outputclaims"></a>OutputClaims

Das **OutputClaims**-Element enthält eine Liste von Ansprüchen, die vom technischen Azure AD-Profil zurückgegeben wurden. Sie müssen den Namen des Anspruchs, der in Ihrer Richtlinie definiert ist, dem Namen, der in Azure Active Directory definiert wurde, zuordnen. Sie können auch Ansprüche, die nicht von Azure Active Directory zurückgegeben wurden, einfügen, sofern Sie das `DefaultValue`-Attribut festlegen.

Das [OutputClaimsTransformations](technicalprofiles.md#output-claims-transformations)-Element darf eine Sammlung von **OutputClaimsTransformation**-Elementen, die zum Ändern der Ausgabeansprüche oder zum Generieren neuer verwendet werden, enthalten.

Das technische Profil **AAD-UserWriteUsingLogonEmail** erstellt z.B. ein lokales Konto und gibt die folgenden Ansprüche zurück:

- **objectId** ist der Bezeichner des neuen Kontos.
- **newUser** gibt an, ob der Benutzer neu ist.
- **authenticationSource** legt die Authentifizierung auf `localAccountAuthentication` fest.
- **userPrincipalName** ist der Benutzerprinzipalname des neuen Kontos.
- **signInNames.emailAddress** ist der Anmeldename des Kontos, ähnlich dem **email**-Eingabeanspruch.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

Das **PersistedClaims**-Element enthält alle Werte, die von Azure AD beibehalten werden sollen, zusammen mit möglichen Zuordnungsinformationen zwischen einem Anspruchstyp, der bereits im Abschnitt [ClaimsSchema](claimsschema.md) in der Richtlinie definiert ist, und dem Azure AD-Attributnamen.

Das technische Profil **AAD-UserWriteUsingLogonEmail** erstellt ein lokales Konto und behält die folgenden Ansprüche bei:

```xml
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

Der Name des Anspruchs ist der Name des Azure AD-Attributs, sofern nicht das **PartnerClaimType**-Attribut angegeben wurde, das den Namen des Azure AD-Attribut enthält.

## <a name="requirements-of-an-operation"></a>Anforderungen für einen Vorgang

- Es muss genau ein **InputClaim**-Element im Anspruchsbehälter für alle technischen Azure AD-Profile vorhanden sein.
- Im Artikel [Benutzerprofilattribute](user-profile-attributes.md) werden die unterstützten Azure AD B2C-Benutzerprofilattribute beschrieben, die Sie in den Eingabe-, Ausgabe- und persistenten Ansprüchen verwenden können. 
- Wenn der Vorgang `Write` oder `DeleteClaims` ist, muss er auch im **PersistedClaims**-Element enthalten sein.
- Der Wert des **userPrincipalName**-Anspruchs muss das Format `user@tenant.onmicrosoft.com` aufweisen.
- Der **displayName**-Anspruch ist erforderlich und darf keine leere Zeichenfolge sein.

## <a name="azure-ad-technical-provider-operations"></a>Vorgänge des technischen Azure AD-Anbieters

### <a name="read"></a>Lesen

Der **Read**-Vorgang liest die Daten zu einem einzelnen Benutzerkonto. Das folgende technische Profil liest Daten über ein Benutzerkonto mithilfe der Objekt-ID des Benutzers:

```xml
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>Schreiben

Der **Write**-Vorgang erstellt oder aktualisiert ein einzelnes Benutzerkonto. Das folgende technische Profil erstellt neues Social Media-Konto:

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

Der **DeleteClaims**-Vorgang löscht die Informationen aus einer angegebenen Liste von Ansprüchen. Das folgende technische Profil löscht Ansprüche:

```xml
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

Der **DeleteClaimsPrincipal**-Vorgang löscht ein einzelnes Benutzerkonto aus dem Verzeichnis. Das folgende technische Profil löscht ein Benutzerkonto anhand des Benutzerprinzipalnamens aus dem Verzeichnis:

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Das folgende technische Profil löscht ein Social Media-Benutzerkonto mit **alternativeSecurityId**:

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Metadaten

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Vorgang | Ja | Der Vorgang, der ausgeführt werden soll. Mögliche Werte: `Read`, `Write`, `DeleteClaims` oder `DeleteClaimsPrincipal`. |
| RaiseErrorIfClaimsPrincipalDoesNotExist | Nein | Es wird ein Fehler ausgelöst, wenn das Benutzerobjekt im Verzeichnis nicht vorhanden ist. Mögliche Werte: `true` oder `false`. |
| RaiseErrorIfClaimsPrincipalAlreadyExists | Nein | Es wird ein Fehler ausgelöst, wenn das Benutzerobjekt bereits vorhanden ist. Mögliche Werte: `true` oder `false`.|
| ApplicationObjectId | Nein | Die Anwendungsobjekt-ID für vorhandene Attribute. Wert: ObjectId einer Anwendung. Weitere Informationen finden Sie unter [Benutzerdefinierte Attribute](user-flow-custom-attributes.md?pivots=b2c-custom-policy). |
| ClientId | Nein | Die Client-ID für den Zugriff auf den Mandanten als Drittanbieter. Weitere Informationen finden Sie unter [Verwenden von benutzerdefinierten Attributen in einer benutzerdefinierten Richtlinie für die Profilbearbeitung](user-flow-custom-attributes.md?pivots=b2c-custom-policy). |
| IncludeClaimResolvingInClaimsHandling  | Nein | Gibt bei Eingabe- und Ausgabeansprüchen an, ob die [Anspruchsauflösung](claim-resolver-overview.md) im technischen Profil enthalten ist. Mögliche Werte sind `true` oder `false` (Standardwert). Wenn Sie im technischen Profil eine Anspruchsauflösung verwenden möchten, legen Sie für diese Einstellung den Wert `true` fest. |

### <a name="ui-elements"></a>Benutzeroberflächenelemente
 
Die folgenden Einstellungen können verwendet werden, um die Fehlermeldung zu konfigurieren, die bei einem Fehler angezeigt wird. Die Metadaten sollten im [selbstbestätigten](self-asserted-technical-profile.md) technischen Profil konfiguriert werden. Die Fehlermeldungen können [lokalisiert](localization.md) werden.

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists | Nein | Wenn ein Fehler ausgelöst werden soll (siehe Beschreibung zum RaiseErrorIfClaimsPrincipalAlreadyExists-Attribut), geben Sie die Meldung an, die dem Benutzer angezeigt werden soll, wenn das Benutzerobjekt bereits vorhanden ist. |
| UserMessageIfClaimsPrincipalDoesNotExist | Nein | Wenn ein Fehler ausgelöst werden soll (siehe Beschreibung zum RaiseErrorIfClaimsPrincipalDoesNotExist-Attribut), geben Sie die Meldung an, die dem Benutzer angezeigt werden soll, wenn das Benutzerobjekt nicht vorhanden ist. |


## <a name="next-steps"></a>Nächste Schritte

Im folgenden Artikel finden Sie ein Beispiel für die Verwendung eines technischen Azure AD-Profils:

- [Hinzufügen von Ansprüchen und Anpassen von Benutzereingaben mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](configure-user-input.md)














