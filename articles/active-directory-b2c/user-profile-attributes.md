---
title: Benutzerprofilattribute in Azure Active Directory B2C
description: Hier finden Sie Informationen zu den Attributen von Benutzerressourcentypen, die vom Azure AD B2C-Verzeichnisbenutzerprofil unterstützt werden. Informieren Sie sich über integrierte Attribute, Erweiterungen und darüber, wie Attribute Microsoft Graph zugeordnet werden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/16/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 76a7fa3cfeafa20dd81f043d67388bdfb281b6d4
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112232519"
---
# <a name="user-profile-attributes"></a>Benutzerprofilattribute

Ihr Azure Active Directory (Azure AD) B2C-Verzeichnisbenutzerprofil umfasst einen integrierten Satz von Attributen wie Vorname, Nachname, Ort, Postleitzahl und Telefonnummer. Sie können das Benutzerprofil mit Ihren eigenen Anwendungsdaten erweitern, ohne dass ein externer Datenspeicher erforderlich ist.

Die meisten Attribute, die in Azure AD B2C-Benutzerprofilen verwendet werden können, werden auch von Microsoft Graph unterstützt. In diesem Artikel werden die unterstützten Azure AD B2C-Benutzerprofilattribute beschrieben. Außerdem werden die Attribute angeführt, die nicht von Microsoft Graph unterstützt werden, und es wird auf die Microsoft Graph-Attribute hingewiesen, die nicht in Azure AD B2C verwendet werden sollten.

> [!IMPORTANT]
> Sie sollten keine integrierten Attribute oder Erweiterungsattribute verwenden, um vertrauliche personenbezogene Daten zu speichern, wie z. B. Kontoanmeldeinformationen, amtliche Identifikationsnummern, Karteninhaberdaten, Finanzkontodaten, Gesundheitsdaten oder vertrauliche Hintergrundinformationen.

Sie können auch externe Systeme integrieren. Sie können z. B. Azure AD B2C für die Authentifizierung verwenden und eine externe Datenbank für Kundenbeziehungsmanagement (Customer Relationship Management, CRM) oder Kundendatenbank als autoritative Quelle der Kundendaten nutzen. Weitere Informationen finden Sie unter [Lösung für Remoteprofile](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile).

## <a name="azure-ad-user-resource-type"></a>Azure AD-Benutzerressourcentyp

In der nachstehenden Tabelle sind die Attribute von [Benutzerressourcentypen](/graph/api/resources/user) aufgeführt, die vom Azure AD B2C-Verzeichnisbenutzerprofil unterstützt werden. Darin sind die folgenden Informationen zu den einzelnen Attributen enthalten:

- Der von Azure AD B2C verwendete Attributname (gefolgt vom Microsoft Graph-Namen in Klammern, falls dieser anders lautet)
- Attributdatentyp
- Attributbeschreibung
- Ob das Attribut im Azure-Portal verfügbar ist
- Ob das Attribut in einem Benutzerflow verwendet werden kann
- Ob das Attribut in einem [technischen Azure AD-Profil](active-directory-technical-profile.md) in einer benutzerdefinierten Richtlinie verwendet werden kann und in welchem Abschnitt (&lt;InputClaims&gt;, &lt;OutputClaims&gt; oder &lt;PersistedClaims&gt;)

|Name     |type     |BESCHREIBUNG|Azure-Portal|Benutzerabläufe|Benutzerdefinierte Richtlinie|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|Gibt an, ob das Benutzerkonto aktiviert oder deaktiviert ist: **true**, wenn das Konto aktiviert ist, andernfalls **false**.|Ja|Nein|Persistent, Ausgabe|
|ageGroup        |String|Die Altersgruppe des Benutzers. Mögliche Werte: „Null“, „Undefined“, „Minor“, „Adult“ und „NotAdult“.|Ja|Nein|Persistent, Ausgabe|
|alternativeSecurityId ([Identities](#identities-attribute))|String|Eine einzelne Benutzeridentität vom externen Identitätsanbieter.|Nein|Nein|Eingabe, Persistent, Ausgabe|
|alternativeSecurityIds ([Identities](#identities-attribute))|Sammlung alternativer Sicherheits-IDs|Eine Sammlung von Benutzeridentitäten von externen Identitätsanbietern.|Nein|Nein|Persistent, Ausgabe|
|city            |String|Die Stadt, in der sich der Benutzer befindet. Maximale Länge: 128 Zeichen.|Ja|Ja|Persistent, Ausgabe|
|consentProvidedForMinor|String|Ob die Zustimmung für einen Minderjährigen erteilt wurde. Zulässige Werte: „Null“, „Granted“, „Denied“ oder „notRequired“.|Ja|Nein|Persistent, Ausgabe|
|country         |String|Das Land oder die Region, in dem bzw. der der Benutzer ansässig ist. Beispiel: „US“ oder „UK“. Maximale Länge: 128 Zeichen.|Ja|Ja|Persistent, Ausgabe|
|createdDateTime|Datetime|Das Datum, an dem das Benutzerobjekt erstellt wurde. Schreibgeschützt.|Nein|Nein|Persistent, Ausgabe|
|creationType    |String|Wenn das Benutzerkonto als lokales Konto für einen Azure Active Directory B2C-Mandanten erstellt wurde, lautet der Wert „LocalAccount“ oder „nameCoexistence“. Schreibgeschützt.|Nein|Nein|Persistent, Ausgabe|
|dateOfBirth     |Date|Das Geburtsdatum.|Nein|Nein|Persistent, Ausgabe|
|department      |String|Der Name der Abteilung, in der der Benutzer arbeitet. Maximale Länge: 64 Zeichen.|Ja|Nein|Persistent, Ausgabe|
|displayName     |String|Der Anzeigename für den Benutzer. Maximale Länge: 256 Zeichen.|Ja|Ja|Persistent, Ausgabe|
|facsimileTelephoneNumber<sup>1</sup>|String|Die geschäftliche Faxnummer des Benutzers.|Ja|Nein|Persistent, Ausgabe|
|givenName       |String|Der Vorname des Benutzers. Maximale Länge: 64 Zeichen.|Ja|Ja|Persistent, Ausgabe|
|jobTitle        |String|Die Position des Benutzers. Maximale Länge: 128 Zeichen.|Ja|Ja|Persistent, Ausgabe|
|immutableId     |String|Ein Bezeichner, der in der Regel für Benutzer verwendet wird, die von einer lokalen Active Directory-Instanz migriert werden.|Nein|Nein|Persistent, Ausgabe|
|legalAgeGroupClassification|String|Rechtliche Altersgruppenklassifizierung. Dieses Attribut ist schreibgeschützt und wird basierend auf den Eigenschaften „ageGroup“ und „consentProvidedForMinor“ berechnet. Zulässige Werte: „Null“, „minorWithOutParentalConsent“, „minorWithParentalConsent“, „minorNoParentalConsentRequired“, „notAdult“ und „adult“.|Ja|Nein|Persistent, Ausgabe|
|legalCountry<sup>1</sup>  |String|Land/Region für rechtliche Zwecke.|Nein|Nein|Persistent, Ausgabe|
|mailNickName    |String|Der E-Mail-Alias für den Benutzer. Maximale Länge: 64 Zeichen.|Nein|Nein|Persistent, Ausgabe|
|mobile (mobilePhone) |String|Die primäre Mobiltelefonnummer des Benutzers. Maximale Länge: 64 Zeichen.|Ja|Nein|Persistent, Ausgabe|
|netId           |String|Die Netz-ID.|Nein|Nein|Persistent, Ausgabe|
|objectId        |String|Ein global eindeutiger Bezeichner (Globally Unique Identifier, GUID), bei dem es sich um den eindeutigen Bezeichner für den Benutzer handelt. Beispiel: 12345678-9abc-def0-1234-56789abcde. Schreibgeschützt, unveränderlich.|Nur Lesezugriff|Ja|Eingabe, Persistent, Ausgabe|
|otherMails      |Zeichenfolgensammlung|Eine Liste weiterer E-Mail-Adressen für den Benutzer. Beispiel: [„bob@contoso.com“, „Robert@fabrikam.com“]. HINWEIS: Akzentzeichen sind nicht zulässig.|Ja (Alternative E-Mail-Adresse)|Nein|Persistent, Ausgabe|
|password        |String|Das Kennwort für das lokale Konto bei der Benutzererstellung.|Nein|Nein|Persistent|
|passwordPolicies     |String|Die Richtlinie des Kennworts. Dabei handelt sich um eine Zeichenfolge, die aus verschiedenen, durch Komma getrennten Richtliniennamen besteht. Beispiel: „DisablePasswordExpiration, DisableStrongPassword“.|Nein|Nein|Persistent, Ausgabe|
|physicalDeliveryOfficeName (officeLocation)|String|Position des Büros am Unternehmensstandort des Benutzers. Maximale Länge: 128 Zeichen.|Ja|Nein|Persistent, Ausgabe|
|postalCode      |String|Die Postleitzahl der Anschrift des Benutzers. Die Postleitzahl ist spezifisch für das Land oder die Region des Benutzers. In den USA enthält dieses Attribut die Postleitzahl. Maximale Länge: 40 Zeichen.|Ja|Nein|Persistent, Ausgabe|
|preferredLanguage    |String|Die bevorzugte Sprache des Benutzers. Das bevorzugte Sprachformat basiert auf RFC 4646. Der Name setzt sich zusammen aus dem aus zwei Kleinbuchstaben bestehenden ISO 639-Kulturcode, der einer Sprache zugeordnet ist, und dem aus zwei Großbuchstaben bestehenden ISO 3166-Subkulturcode, der einem Land oder einer Region zugeordnet ist. Beispiel: „en-US“ oder „de-DE“.|Nein|Nein|Persistent, Ausgabe|
|refreshTokensValidFromDateTime (signInSessionsValidFromDateTime)|Datetime|Alle vor diesem Zeitpunkt ausgestellten Aktualisierungstoken sind ungültig, und Anwendungen erhalten eine Fehlermeldung, wenn ein ungültiges Aktualisierungstoken zum Abrufen eines neuen Zugriffstokens verwendet wird. Wenn dieser Fall eintritt, muss die Anwendung ein neues Aktualisierungstoken abrufen, indem eine Anforderung an den Autorisierungsendpunkt gesendet wird. Schreibgeschützt.|Nein|Nein|Output|
|signInNames ([Identities](#identities-attribute)) |String|Der eindeutige Anmeldename des Benutzers des lokalen Kontos eines beliebigen Typs im Verzeichnis. Verwenden Sie dieses Attribut, um einen Benutzer mit einem Anmeldewert abzurufen, ohne den lokalen Kontotyp anzugeben.|Nein|Nein|Eingabe|
|signInNames.userName ([Identities](#identities-attribute)) |String|Der eindeutige Benutzername des Benutzers des lokalen Kontos im Verzeichnis. Verwenden Sie dieses Attribut, um einen Benutzer mit einem bestimmten Anmeldebenutzernamen zu erstellen oder abzurufen. Wenn Sie nur dieses Attribut in „PersistedClaims“ während eines Patchvorgangs angeben, werden andere Typen von „signInNames“ entfernt. Wenn Sie einen neuen Typ von „signInNames“ hinzufügen möchten, müssen Sie auch vorhandene „signInNames“ beibehalten. HINWEIS: Akzentzeichen sind im Benutzernamen nicht zulässig.|Nein|Nein|Eingabe, Persistent, Ausgabe|
|signInNames.phoneNumber ([Identities](#identities-attribute)) |String|Die eindeutige Telefonnummer des Benutzers des lokalen Kontos im Verzeichnis. Verwenden Sie dieses Attribut, um einen Benutzer mit einer bestimmten Telefonnummer für die Anmeldung zu erstellen oder abzurufen. Wenn Sie nur dieses Attribut in „PersistedClaims“ während eines Patchvorgangs angeben, werden andere Typen von „signInNames“ entfernt. Wenn Sie einen neuen Typ von „signInNames“ hinzufügen möchten, müssen Sie auch vorhandene „signInNames“ beibehalten.|Nein|Nein|Eingabe, Persistent, Ausgabe|
|signInNames.emailAddress ([Identities](#identities-attribute))|String|Die eindeutige E-Mail-Adresse des Benutzers des lokalen Kontos im Verzeichnis. Verwenden Sie dieses Attribut, um einen Benutzer mit einer bestimmten E-Mail-Adresse für die Anmeldung zu erstellen oder abzurufen. Wenn Sie nur dieses Attribut in „PersistedClaims“ während eines Patchvorgangs angeben, werden andere Typen von „signInNames“ entfernt. Wenn Sie einen neuen Typ von „signInNames“ hinzufügen möchten, müssen Sie auch vorhandene „signInNames“ beibehalten.|Nein|Nein|Eingabe, Persistent, Ausgabe|
|state           |String|Das Bundesland oder der Kanton in der Adresse des Benutzers. Maximale Länge: 128 Zeichen.|Ja|Ja|Persistent, Ausgabe|
|streetAddress   |String|Die Straße in der geschäftlichen Anschrift des Benutzers. Maximale Länge: 1024 Zeichen.|Ja|Ja|Persistent, Ausgabe|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|String|Die sekundäre Telefonnummer des Benutzers, die für die mehrstufige Authentifizierung verwendet wird.|Ja|Nein|Persistent, Ausgabe|
|strongAuthenticationEmailAddress<sup>1</sup>|String|Die SMTP-Adresse für den Benutzer. Beispiel: „bob@contoso.com“. Dieses Attribut wird für die Anmeldung mit der Benutzernamenrichtlinie verwendet, um die E-Mail-Adresse des Benutzers zu speichern. Die E-Mail-Adresse wird dann in einem Kennwortzurücksetzungsflow verwendet. Akzentzeichen sind in diesem Attribut nicht zulässig.|Ja|Nein|Persistent, Ausgabe|
|strongAuthenticationPhoneNumber<sup>2</sup>|String|Die primäre Telefonnummer des Benutzers, die für die mehrstufige Authentifizierung verwendet wird.|Ja|Nein|Persistent, Ausgabe|
|surname         |String|Der Nachname (oder Familienname) des Benutzers. Maximale Länge: 64 Zeichen.|Ja|Ja|Persistent, Ausgabe|
|telephoneNumber (erster Eintrag von „businessPhones“)|String|Die primäre geschäftliche Telefonnummer des Benutzers.|Ja|Nein|Persistent, Ausgabe|
|userPrincipalName    |String|Der Benutzerprinzipalname (User Principal Name, UPN) des Benutzers. Der UPN ist ein Anmeldename im Internetformat für den Benutzer basierend auf dem Internetstandard RFC 822. Die Domäne muss in der Sammlung der überprüften Domänen des Mandanten enthalten sein. Diese Eigenschaft ist beim Erstellen eines Kontos erforderlich. Unveränderlich.|Nein|Nein|Eingabe, Persistent, Ausgabe|
|usageLocation   |String|Erforderlich für Benutzer, denen aufgrund gesetzlicher Anforderungen Lizenzen zugewiesen werden, um die Verfügbarkeit von Diensten in den jeweiligen Ländern/Regionen zu überprüfen. Lässt keine NULL-Werte zu. Ein aus zwei Buchstaben bestehender Länder-/Regionscode (ISO-Standard 3166). Beispiele: „US“, „JP“ und „GB“.|Ja|Nein|Persistent, Ausgabe|
|userType        |String|Ein Zeichenfolgenwert, der zum Klassifizieren von Benutzertypen in Ihrem Verzeichnis verwendet werden kann. Der Wert muss „Member“ (Mitglied) lauten. Schreibgeschützt.|Nur Lesezugriff|Nein|Persistent, Ausgabe|
|userState (externalUserState)<sup>3</sup>|String|Gilt nur für das Azure AD B2B-Konto. Gibt an, ob der Status der Einladung „PendingAcceptance“ (Annahme ausstehend) oder „Accepted“ (Angenommen) lautet.|Nein|Nein|Persistent, Ausgabe|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|Datetime|Zeigt den Zeitstempel der letzten Änderung der „UserState“-Eigenschaft an.|Nein|Nein|Persistent, Ausgabe|

<sup>1 </sup>Wird von Microsoft Graph nicht unterstützt<br><sup>2 </sup>Weitere Informationen finden Sie unter [MFA-Attribut „Telefonnummer“](#mfa-phone-number-attribute)<br><sup>3 </sup>Sollte mit Azure AD B2C nicht verwendet werden

## <a name="required-attributes"></a>Erforderliche Attribute

Geben Sie die folgenden erforderlichen Attribute an, um im Azure AD B2C-Verzeichnis ein Benutzerkonto zu erstellen: 

- [Anzeigename](#display-name-attribute)

- [Identitäten](#display-name-attribute): Mindestens eine vorhandene Entität (lokales oder Verbundkonto).

- [Kennwortprofil](#password-policy-attribute): Geben Sie das Kennwortprofil an, wenn Sie ein lokales Konto erstellen.

## <a name="display-name-attribute"></a>Anzeigenamenattribut

Der `displayName` ist der Name, der im Azure-Portal in der Benutzerverwaltung für den Benutzer und im Zugriffstoken, das Azure AD B2C an die Anwendung zurückgibt, angezeigt werden soll. Diese Eigenschaft ist obligatorisch.

## <a name="identities-attribute"></a>Attribut „identities“/Identitätenattribut

Einem Kundenkonto (von einem Endbenutzer, einem Partner oder einem anderen Benutzer) kann einer der folgenden Identitätstypen zugeordnet werden:

- **Lokale** Identität: Der Benutzername und das Kennwort werden lokal im Azure AD B2C-Verzeichnis gespeichert. Diese Identitäten werden häufig als „lokale Konten“ bezeichnet.
- **Verbundidentität:** Die Identitäten dieser auch als *Konten für soziale Netzwerke* oder *Unternehmenskonten* bezeichneten Benutzer werden von einem Verbundidentitätsanbieter wie Facebook, Microsoft, AD FS oder Salesforce verwaltet.

Ein Benutzer mit einem Kundenkonto kann sich mit mehreren Identitäten anmelden. Er kann z. B. den Benutzernamen, die E-Mail-Adresse, die Mitarbeiter-ID, eine Behördenkennung und andere Daten verwenden. Ein einzelnes Konto kann über mehrere Identitäten verfügen. Dabei kann es sich um lokale Identitäten und Identitäten für soziale Netzwerke handeln, die auch dasselbe Kennwort nutzen können.

In der Microsoft Graph-API werden sowohl lokale als auch Verbundidentitäten im Benutzerattribut `identities` gespeichert, das den Typ [objectIdentity](/graph/api/resources/objectidentity) aufweist. Die Sammlung `identities` stellt einen Satz von Identitäten dar, die für die Anmeldung bei einem Benutzerkonto verwendet werden. Diese Sammlung ermöglicht dem Benutzer, sich mit den zugehörigen Identitäten beim Benutzerkonto anzumelden. Das „identities“-Attribut kann bis zu zehn [objectIdentity](/graph/api/resources/objectidentity)-Objekte enthalten. Jedes Objekt enthält die folgenden Eigenschaften:

| Name   | type |BESCHREIBUNG|
|:---------------|:--------|:----------|
|signInType|Zeichenfolge| Gibt die Benutzeranmeldetypen in Ihrem Verzeichnis an. Für ein lokales Konto: `emailAddress`, `emailAddress1`, `emailAddress2`, `emailAddress3`, `userName` oder ein beliebiger anderer Typ. Bei einem Konto für soziale Netzwerke muss dies auf `federated` festgelegt werden.|
|Issuer (Aussteller)|Zeichenfolge|Gibt den Aussteller der Identität an. Bei lokalen Konten (bei denen **signInType** nicht `federated` ist) ist diese Eigenschaft der Standarddomänenname des lokalen B2C-Mandanten, z. B. `contoso.onmicrosoft.com`. Bei einer Identität für ein soziales Netzwerk (bei der der **signInType** `federated` ist) entspricht der Wert dem Namen des Ausstellers, z. B. `facebook.com`.|
|issuerAssignedId|Zeichenfolge|Gibt den dem Benutzer vom Aussteller zugewiesenen eindeutigen Bezeichner an. Die Kombination aus **issuer** und **issuerAssignedId** muss innerhalb Ihres Mandanten eindeutig sein. Wenn für das lokale Konto **signInType** auf `emailAddress` oder `userName` festgelegt ist, stellt dies den Anmeldenamen für den Benutzer dar.<br>Wenn **signInType** auf <ul><li>`emailAddress` festgelegt ist (oder mit `emailAddress` beginnt wie bei `emailAddress1`), muss die **issuerAssignedId** eine gültige E-Mail-Adresse sein.</li><li>`userName` festgelegt (oder einen beliebigen anderen Wert) ist, muss die **issuerAssignedId** ein gültiger [lokaler Teil einer E-Mail-Adresse](https://tools.ietf.org/html/rfc3696#section-3) sein.</li><li>`federated` festgelegt ist, stellt die **issuerAssignedId** den eindeutigen Bezeichner des Verbundkontos dar.</li></ul>|

Das folgende **Identities**-Attribut mit einer lokalen Kontoidentität mit einem Anmeldenamen, einer E-Mail-Adresse für die Anmeldung und einer Identität für ein soziales Netzwerk.

```json
"identities": [
  {
    "signInType": "userName",
    "issuer": "contoso.onmicrosoft.com",
    "issuerAssignedId": "johnsmith"
  },
  {
    "signInType": "emailAddress",
    "issuer": "contoso.onmicrosoft.com",
    "issuerAssignedId": "jsmith@yahoo.com"
  },
  {
    "signInType": "federated",
    "issuer": "facebook.com",
    "issuerAssignedId": "5eecb0cd"
  }
]
```

Bei Verbundidentitäten handelt es sich abhängig vom Identitätsanbieter bei der **issuerAssignedId** um einen eindeutigen Wert für einen bestimmten Benutzer pro Anwendung oder ein Entwicklungskonto. Konfigurieren Sie die Azure AD B2C-Richtlinie mit der gleichen Anwendungs-ID, die zuvor vom Anbieter des sozialen Netzwerks zugewiesen wurde, oder mit der einer anderen Anwendung im selben Entwicklungskonto.

## <a name="password-profile-property"></a>Eigenschaft „passwordProfile“ (Kennwortprofil)

Für eine lokale Identität ist das Attribut **passwordProfile** erforderlich, und es enthält das Kennwort des Benutzers. Das Attribut `forceChangePasswordNextSignIn` gibt an, ob ein Benutzer bei der nächsten Anmeldung das Kennwort zurücksetzen muss. Zur Behandlung der erzwungenen Kennwortzurücksetzung [richten Sie den Flow für die erzwungene Kennwortzurücksetzung ein](force-password-reset.md).

Für eine Verbundidentität (Identität für ein soziales Netzwerk) ist das Attribut **passwordProfile** nicht erforderlich.

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

## <a name="password-policy-attribute"></a>Attribut „passwordPolicies“/Kennwortrichtlinienattribut

Die Kennwortrichtlinie von Azure AD B2C (für lokale Konten) basiert auf der Richtlinie für [hohe Kennwortsicherheit](../active-directory/authentication/concept-sspr-policy.md) von Azure Active Directory. Die Azure AD B2C-Richtlinien zur Registrierung, Anmeldung und Kennwortzurücksetzung erfordern sichere Kennwörter, die nicht ablaufen.

Wenn bei Benutzermigrationsvorgängen die Konten, die Sie migrieren möchten, über eine geringere Kennwortsicherheit als die [hohe Kennwortsicherheit](../active-directory/authentication/concept-sspr-policy.md) unter Azure AD B2C verfügen, können Sie die Erzwingung sicherer Kennwörter deaktivieren. Um die Standardkennwortrichtlinie zu ändern, legen Sie das Attribut `passwordPolicies` auf `DisableStrongPassword` fest. Beispielsweise können Sie die Anforderung zum Erstellen eines Benutzers wie folgt ändern:

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="mfa-phone-number-attribute"></a>MFA-Attribut „Telefonnummer“

Wenn Sie für die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) ein Telefon verwenden, wird die Benutzeridentität anhand des Mobiltelefons überprüft. Wenn Sie programmgesteuert eine Telefonnummer [neu hinzufügen](/graph/api/authentication-post-phonemethods), [aktualisieren](/graph/api/b2cauthenticationmethodspolicy-update), [abrufen](/graph/api/b2cauthenticationmethodspolicy-get) oder [löschen](/graph/api/phoneauthenticationmethod-delete) möchten, verwenden Sie die [Authentifizierungsmethode per Telefon](/graph/api/resources/phoneauthenticationmethod) der Microsoft Graph-API.

In den [benutzerdefinierten Richtlinien](custom-policy-overview.md) von Azure AD B2C ist die Telefonnummer über den Anspruchstyp `strongAuthenticationPhoneNumber` verfügbar.

## <a name="extension-attributes"></a>Erweiterungsattribute

Für jede kundenorientierte Anwendung gelten spezifische Anforderungen im Hinblick auf die Informationen, die erfasst werden sollen. Ihr Azure AD B2C-Mandant umfasst einen integrierten Satz von in Eigenschaften gespeicherten Informationen, z. B. Vorname, Nachname und Postleitzahl. Mit Azure AD B2C haben Sie die Möglichkeit, den für die einzelnen Kundenkonten gespeicherten Satz von Eigenschaften zu erweitern. Weitere Informationen finden Sie unter [Hinzufügen von Benutzerattributen und Anpassen der Benutzereingabe in Azure Active Directory B2C](configure-user-input.md).

Erweiterungsattribute [erweitern das Schema](/graph/extensibility-overview#schema-extensions) der Benutzerobjekte im Verzeichnis. Die Erweiterungsattribute können nur für ein Anwendungsobjekt registriert werden, obwohl sie Daten für einen Benutzer enthalten können. Das Erweiterungsattribut wird an die Anwendung namens `b2c-extensions-app` angefügt. Nehmen Sie keine Änderungen an dieser Anwendung vor. Sie wird von Azure AD B2C zum Speichern von Benutzerdaten verwendet. Sie finden diese Anwendung in Azure Active Directory unter „App-Registrierungen“.

> [!NOTE]
> - Für jedes Benutzerkonto können bis zu 100 Erweiterungsattribute geschrieben werden.
> - Beim Löschen der Anwendung b2c-extensions-app werden diese Erweiterungsattribute zusammen mit allen darin enthaltenen Daten von allen Benutzern entfernt.
> - Wird ein Erweiterungsattribut von der Anwendung gelöscht, wird es aus allen Benutzerkonten entfernt, und die Werte werden gelöscht.

Erweiterungsattribute in den Graph-API werden gemäß der Konvention `extension_ApplicationClientID_AttributeName` benannt, wobei `ApplicationClientID` die **Anwendungs-ID (Client)** der Anwendung `b2c-extensions-app` ist (im Azure-Portal unter **App-Registrierungen** > **Alle Anwendungen**). Beachten Sie, dass die **Anwendungs-ID (Client)** in der Darstellung im Namen des Erweiterungsattributs keine Bindestriche enthält. Beispiel:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

Für das Definieren eines Attributs in einer Schemaerweiterung werden die folgenden Datentypen unterstützt:

|Typ |Hinweise  |
|--------------|---------|
|Boolean    | Mögliche Werte sind **true** und **false**. |
|Datetime   | Muss im ISO 8601-Format angegeben werden. Wird in UTC gespeichert.   |
|Integer    | 32-Bit-Wert.               |
|String     | Maximal 256 Zeichen.     |

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Erweiterungsattribute:

- [Schemaerweiterungen](/graph/extensibility-overview#schema-extensions)
- [Definieren von benutzerdefinierten Attributen](user-flow-custom-attributes.md)
