---
title: Anspruchszuordnungsrichtlinie
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über den Anspruchszuordnungsrichtlinientyp, mit dem Sie die Ansprüche ändern können, die in für bestimmte Anwendungen ausgegebenen Token ausgestellt werden.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: reference
ms.date: 07/01/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 52de257461c796f7a477091801f4bdc52f46f5c8
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101531"
---
# <a name="claims-mapping-policy-type"></a>Richtlinientyp einer Anspruchszuordnung

Ein **Richtlinienobjekt** stellt in Azure AD eine Reihe von Regeln dar, die für einzelne Anwendungen oder alle Anwendungen in einer Organisation erzwungen werden. Jede Art von Richtlinie verfügt über eine eindeutige Struktur mit einem Satz von Eigenschaften, die auf Objekte angewendet werden, denen sie zugewiesen sind.

Eine Anspruchszuordnungsrichtlinie ist eine Art von **Richtlinienobjekt**, das die [ausgestellten Ansprüche in Token ändert](active-directory-claims-mapping.md), die für bestimmte Anwendungen ausgegeben wurden.

## <a name="claim-sets"></a>Anspruchsätze

Es gibt bestimmte Sätze von Ansprüchen, die definieren, wie und wann sie in Token verwendet werden.

| Satz von Ansprüchen | BESCHREIBUNG |
|---|---|
| Hauptsatz von Ansprüchen | Sind in jedem Token unabhängig von der Richtlinie vorhanden. Diese Ansprüche sind zudem als eingeschränkt anzusehen und können nicht geändert werden. |
| Grundlegender Anspruchssatz | Enthält die Ansprüche, die standardmäßig für Token ausgegeben werden (zusätzlich zum Hauptanspruchssatz). Mit den Richtlinien für die Anspruchszuordnung können Sie [grundlegende Ansprüche weglassen oder ändern](active-directory-claims-mapping.md#omit-the-basic-claims-from-tokens). |
| Eingeschränkter Anspruchssatz | Kann nicht mit der Richtlinie geändert werden. Die Datenquelle kann nicht geändert werden, und beim Generieren der Ansprüche wird keine Transformation angewendet. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabelle 1: Eingeschränkter Anspruchssatz der JSON Web Token (JWT)

| Anspruchstyp (Name) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| actor |
| actortoken |
| aio |
| altsecid |
| amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| appid |
| appidacr |
| assertion |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| cnf |
| code |
| controls |
| credential_keys |
| csr |
| csr_type |
| deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| email |
| endpoint |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| graph |
| group_sids |
| groups |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| iat |
| identityprovider |
| idp |
| in_corp |
| instance |
| ipaddr |
| isbrowserhostedapp |
| iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid |
| nbf |
| netbios_name |
| nonce |
| oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| password |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| resource |
| Rolle (role) |
| roles |
| scope |
| scp |
| sid |
| signature |
| signin_state |
| src1 |
| src2 |
| sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| tid |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| upn |
| user_setting_sync_url |
| username |
| uti |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tabelle 2: Eingeschränkter SAML-Anspruchssatz

| Anspruchstyp (URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>Eigenschaften der Anspruchszuordnungsrichtlinie

Um zu steuern, welche Ansprüche ausgegeben werden und woher die Daten stammen, verwenden Sie die Eigenschaften einer Anspruchszuordnungsrichtlinie. Wenn keine Richtlinie festgelegt ist, stellt das System Token aus, die den Hauptanspruchssatz, den grundlegenden Anspruchssatz und alle [optionalen Ansprüche](active-directory-optional-claims.md) enthält, die die Anwendung empfängt.

> [!NOTE]
> Ansprüche im Hauptanspruchssatz sind in jedem Token vorhanden, unabhängig davon, wie diese Eigenschaft festgelegt wird.

### <a name="include-basic-claim-set"></a>Einschließen des grundlegenden Anspruchssatzes

**Zeichenfolge:** IncludeBasicClaimSet

**Datentyp:** Boolesch („True“ oder „False“)

**Zusammenfassung:** Diese Eigenschaft bestimmt, ob der grundlegende Anspruchssatz in von dieser Richtlinie betroffenen Token enthalten ist.

- Wenn der Wert auf TRUE festgelegt wird, werden alle Ansprüche im grundlegenden Anspruchssatz in Token ausgegeben, die von der Richtlinie betroffen sind.
- Wenn der Wert auf FALSE festgelegt wird, werden die Ansprüche im grundlegenden Anspruchssatz nicht in Token ausgegeben, es sei denn, sie werden einzeln in der Ansprücheschemaeigenschaft derselben Richtlinie hinzugefügt.



### <a name="claims-schema"></a>Anspruchsschema

**Zeichenfolge:** ClaimsSchema

**Datentyp:** JSON-Blob mit mindestens einem Anspruchsschemaeintrag

**Zusammenfassung:** Diese Eigenschaft definiert, welche Ansprüche zusätzlich zum grundlegenden und zum Hauptanspruchssatz in den von der Richtlinie betroffenen Token vorhanden sind.
Für jeden Anspruchsschemaeintrag in dieser Eigenschaft definiert sind bestimmte Informationen erforderlich. Geben Sie an, woher die Daten stammen (**Wert** oder **Quelle-ID-Paar** oder **Quelle-Erweiterungs-ID-Paar**) und als welcher Anspruch die Daten ausgegeben werden (**Anspruchstyp**).

### <a name="claim-schema-entry-elements"></a>Elemente eines Anspruchsschemaeintrags

**Wert:** Das Wertelement definiert die Daten, die im Anspruch ausgegeben werden sollen, als statischen Wert.

**Quelle/ID-Paar:** Die Quell- und ID-Elemente definieren, woher die Daten im Anspruch stammen.

**Quelle-Erweiterungs-ID-Paar:** Die Quell- und Erweiterungs-ID-Elemente definieren das Verzeichnisschema-Erweiterungsattribut, aus dem die Daten im Anspruch stammen. Weitere Informationen finden Sie unter [Verwenden von Verzeichnisschema-Erweiterungsattributen in Ansprüchen](active-directory-schema-extensions.md).

Legen Sie das Quellelement auf einen der folgenden Werte fest:

- „user“: Die Daten im Anspruch sind eine Eigenschaft des User-Objekts.
- „application“: Die Daten im Anspruch sind eine Eigenschaft des Dienstprinzipals der Anwendung (Client).
- „resource“: Die Daten im Anspruch sind eine Eigenschaft des Dienstprinzipals der Ressource.
- „audience“: Die Daten im Anspruch sind eine Eigenschaft des Dienstprinzipals, der die Zielgruppe des Token ist (Client- oder Ressourcendienstprinzipal).
- „company“: Die Daten im Anspruch sind eine Eigenschaft des „Company“-Objekts des Ressourcenmandanten.
- „transformation“: Die Daten im Anspruch stammen aus Anspruchtransformationen (siehe Abschnitt „Transformation von Ansprüchen“ weiter unten in diesem Artikel).

Wenn die Quelle „transformation“ ist, muss das **TransformationID**-Element in dieser Anspruchsdefinition auch enthalten sein.

Das ID-Element identifiziert, welche Eigenschaft in der Quelle den Wert für den Anspruch bereitstellt. Die folgende Tabelle listet die ID-Werte auf, die für jeden Wert der Quelle gültig sind.

> [!WARNING]
> Derzeit sind die einzigen verfügbaren mehrwertigen Anspruchsquellen für ein Benutzerobjekt mehrwertige Erweiterungsattribute, die von AADConnect synchronisiert wurden.  Andere Eigenschaften (z. B. „othermail“ und „tags“) sind mehrwertige Eigenschaften, aber es wird nur ein Wert ausgegeben, wenn sie als Quelle ausgewählt werden.

#### <a name="table-3-valid-id-values-per-source"></a>Tabelle 3: Gültige ID-Werte pro Quelle

| `Source` | id | BESCHREIBUNG |
|-----|-----|-----|
| Benutzer | surname | Familienname |
| Benutzer | givenname | Vorname |
| Benutzer | displayname | Anzeigename |
| Benutzer | objectid | ObjectID |
| Benutzer | mail | E-Mail-Adresse |
| Benutzer | userprincipalname | Benutzerprinzipalname |
| Benutzer | department|Department|
| Benutzer | onpremisessamaccountname | Name des lokalen SAM-Kontos |
| Benutzer | netbiosname| NetBIOS-Name |
| Benutzer | dnsdomainname | DNS-Domänenname |
| Benutzer | onpremisesecurityidentifier | Lokale Sicherheits-ID |
| Benutzer | companyname| Name der Organisation |
| Benutzer | streetaddress | Anschrift |
| Benutzer | postalcode | Postleitzahl |
| Benutzer | preferredlanguage | Bevorzugte Sprache |
| Benutzer | onpremisesuserprincipalname | Lokaler UPN |
| Benutzer | mailNickname | E-Mail-Kontoname |
| Benutzer | extensionattribute1 | Erweiterungsattribut 1 |
| Benutzer | extensionattribute2 | Erweiterungsattribut 2 |
| Benutzer | extensionattribute3 | Erweiterungsattribut 3 |
| Benutzer | extensionattribute4 | Erweiterungsattribut 4 |
| Benutzer | extensionattribute5 | Erweiterungsattribut 5 |
| Benutzer | extensionattribute6 | Erweiterungsattribut 6 |
| Benutzer | extensionattribute7 | Erweiterungsattribut 7 |
| Benutzer | extensionattribute8 | Erweiterungsattribut 8 |
| Benutzer | extensionattribute9 | Erweiterungsattribut 9 |
| Benutzer | extensionattribute10 | Erweiterungsattribut 10 |
| Benutzer | extensionattribute11 | Erweiterungsattribut 11 |
| Benutzer | extensionattribute12 | Erweiterungsattribut 12 |
| Benutzer | extensionattribute13 | Erweiterungsattribut 13 |
| Benutzer | extensionattribute14 | Erweiterungsattribut 14 |
| Benutzer | extensionattribute15 | Erweiterungsattribut 15 |
| Benutzer | othermail | Andere E-Mail-Nachrichten |
| Benutzer | country | Land/Region |
| Benutzer | city | City |
| Benutzer | state | State |
| Benutzer | jobtitle | Position |
| Benutzer | employeeid | Mitarbeiter-ID |
| Benutzer | facsimiletelephonenumber | Faxnummer |
| Benutzer | assignetzdroles | Liste der dem Benutzer zugewiesenen App-Rollen|
| Anwendung, Ressourcen, Zielgruppe | displayname | Anzeigename |
| Anwendung, Ressourcen, Zielgruppe | objectid | ObjectID |
| Anwendung, Ressourcen, Zielgruppe | tags | Dienstprinzipal-Tag |
| Company | tenantcountry | Land/Region des Mandanten |

**TransformationID:** Das „TransformationID“-Element muss nur bereitgestellt werden, wenn das Quellelement auf „transformation“ festgelegt ist.

- Dieses Element muss mit dem ID-Element des Transformationseintrags in der Eigenschaft **ClaimsTransformation** entsprechen, die definiert, wie die Daten für diesen Anspruch generiert werden.

**Anspruchstyp:** Die Elemente **JwtClaimType** und **SamlClaimType** definieren, auf welchen Anspruch sich dieser Anspruchsschemaeintrag bezieht.

- „JwtClaimType“ muss den Namen des Anspruchs enthalten, der in JWTs ausgegeben werden soll.
- SamlClaimType muss den URI des Anspruchs enthalten, der in den SAML-Token ausgegeben werden soll.

* **onPremisesUserPrincipalName-Attribut:** Bei Verwendung einer alternativen ID wird das lokale Attribut userPrincipalName mit dem Azure AD-Attribut onPremisesUserPrincipalName synchronisiert. Dieses Attribut ist nur verfügbar, wenn eine alternative ID konfiguriert ist, aber auch über MS Graph Beta verfügbar: https://graph.microsoft.com/beta/me/.

> [!NOTE]
> Namen und URIs der Ansprüche im eingeschränkten Anspruchssatz können nicht für die Anspruchstypelemente verwendet werden. Weitere Informationen finden Sie im Abschnitt „Ausnahmen und Einschränkungen“ weiter unten in diesem Artikel.

### <a name="claims-transformation"></a>Transformation von Ansprüchen

**Zeichenfolge:** ClaimsTransformation

**Datentyp:** JSON-Blob mit mindestens einem Transformationseintrag

**Zusammenfassung:** Mit dieser Eigenschaft können Sie allgemeine Transformationen auf Quelldaten anwenden, um Ausgabedaten für Ansprüche zu generieren, die im Anspruchsschema angegeben wurden.

**ID:** Verwenden Sie das ID-Element, um im Eintrag des TransformationID-Anspruchsschemas auf diesen Transformationseintrag zu verweisen. Dieser Wert muss für jeden Transformationseintrag innerhalb dieser Richtlinie eindeutig sein.

**TransformationMethod:** Das TransformationMethod-Element identifiziert, welcher Vorgang ausgeführt wird, um die Daten für den Anspruch zu generieren.

Auf der Grundlage der ausgewählten Methode wird eine Reihe von Eingaben und Ausgaben erwartet. Definieren Sie die Eingaben und Ausgaben mithilfe von **InputClaims**, **InputParameters** und **OutputClaims**.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabelle 4: Transformationsmethoden und erwartete Ein- und Ausgaben

|Transformationsmethode|Erwartete Eingabe|Erwartete Ausgabe|BESCHREIBUNG|
|-----|-----|-----|-----|
|Join|string1, string2, separator|outputClaim|Verknüpft Eingabezeichenfolgen mit einer eingefügten Trennzeichen. Zum Beispiel: Zeichenfolge1:"foo@bar.com" , Zeichenfolge2:"sandbox" , Trennzeichen:"." ergibt outputClaim:"foo@bar.com.sandbox"|
|ExtractMailPrefix|E-Mail oder Benutzerprinzipalname (UPN)|Extrahierte Zeichenfolge|Erweiterungsattribute 1-15 oder sonstige Schemaerweiterungen, die einen UPN- oder E-Mail-Adress-Wert für den Benutzer speichern, z. B. johndoe@contoso.com. Extrahiert den lokalen Teil einer E-Mail-Adresse. Zum Beispiel: mail:"foo@bar.com" ergibt outputClaim: "foo". Wenn kein \@-Zeichen vorhanden ist, wird die ursprüngliche Eingabezeichenfolge unverändert zurückgegeben.|

**InputClaims:** Verwenden Sie ein InputClaims-Element, um die Daten aus einem Anspruchsschemaeintrag an eine Transformation zu übergeben. Es verfügt über zwei Attribute: **ClaimTypeReferenceId** und **TransformationClaimType**.

- **ClaimTypeReferenceId** ist mit dem ID-Element des Anspruchsschemaeintrags verknüpft, um den entsprechenden Eingabeanspruch zu suchen.
- **TransformationClaimType** wird verwendet, um dieser Eingabe einen eindeutigen Namen zu geben. Dieser Name muss einem der erwarteten Eingaben für die Transformationsmethode entsprechen.

**InputParameters:** Verwenden Sie ein InputParameters-Element, um einen konstanten Wert an eine Transformation zu übergeben. Es verfügt über zwei Attribute: **Value** und **ID**.

- **Value** ist der tatsächliche, konstante Wert, der übergeben werden soll.
- **ID** wird verwendet, um der Eingabe einen eindeutigen Namen zu geben. Der Name muss einem der erwarteten Eingaben für die Transformationsmethode entsprechen.

**OutputClaims:** Verwenden Sie ein OutputClaims-Element, um die von einer Transformation generierten Daten zu verwenden und es an einen Anspruchsschemaeintrag zu binden. Es verfügt über zwei Attribute: **ClaimTypeReferenceId** und **TransformationClaimType**.

- **ClaimTypeReferenceId** ist mit der ID des Anspruchsschemaeintrags verknüpft, um die entsprechende Ausgabenspalte zu suchen.
- **TransformationClaimType** wird verwendet, um der Ausgabe einen eindeutigen Namen zu geben. Der Name muss einer der erwarteten Ausgaben für die Transformationsmethode entsprechen.

### <a name="exceptions-and-restrictions"></a>Ausnahmen und Einschränkungen

**SAML-NameID und -UPN:** Die Attribute, von denen Sie die Werte für „NameID“ und „UPN“ beziehen, und die zulässigen Anspruchstransformationen sind begrenzt. In Tabelle 5 und Tabelle 6 finden Sie die zulässigen Werte.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabelle 5: Attribute, die als Datenquelle für SAML-NameID zulässig sind

|`Source`|id|BESCHREIBUNG|
|-----|-----|-----|
| Benutzer | mail|E-Mail-Adresse|
| Benutzer | userprincipalname|Benutzerprinzipalname|
| Benutzer | onpremisessamaccountname|Lokaler SAM-Kontoname|
| Benutzer | employeeid|Mitarbeiter-ID|
| Benutzer | extensionattribute1 | Erweiterungsattribut 1 |
| Benutzer | extensionattribute2 | Erweiterungsattribut 2 |
| Benutzer | extensionattribute3 | Erweiterungsattribut 3 |
| Benutzer | extensionattribute4 | Erweiterungsattribut 4 |
| Benutzer | extensionattribute5 | Erweiterungsattribut 5 |
| Benutzer | extensionattribute6 | Erweiterungsattribut 6 |
| Benutzer | extensionattribute7 | Erweiterungsattribut 7 |
| Benutzer | extensionattribute8 | Erweiterungsattribut 8 |
| Benutzer | extensionattribute9 | Erweiterungsattribut 9 |
| Benutzer | extensionattribute10 | Erweiterungsattribut 10 |
| Benutzer | extensionattribute11 | Erweiterungsattribut 11 |
| Benutzer | extensionattribute12 | Erweiterungsattribut 12 |
| Benutzer | extensionattribute13 | Erweiterungsattribut 13 |
| Benutzer | extensionattribute14 | Erweiterungsattribut 14 |
| Benutzer | extensionattribute15 | Erweiterungsattribut 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabelle 6: Transformationsmethoden für SAML-NameID

| Transformationsmethode | Beschränkungen |
| ----- | ----- |
| ExtractMailPrefix | Keine |
| Join | Bei dem zu verknüpfenden Suffix muss es sich um eine überprüfte Domäne des Ressourcenmandanten handeln. |

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Anpassen der Ansprüche, die in Token für eine bestimmte Anwendung in ihrem Mandanten mithilfe von PowerShell ausgestellt werden, finden Sie unter [Gewusst wie: Anpassen von in Token ausgegebenen Ansprüchen für eine bestimmte App in einem Mandanten](active-directory-claims-mapping.md)
- Weitere Informationen dazu, wie Sie über das Azure-Portal die im SAML-Token ausgestellten Ansprüche anpassen, finden Sie unter [Vorgehensweise: Anpassen von Ansprüchen im SAML-Token für Unternehmensanwendungen](active-directory-saml-claims-customization.md)
- Weitere Informationen zu Erweiterungsattributen finden Sie unter [Verwenden von Erweiterungsattributen des Verzeichnisschemas in Ansprüchen](active-directory-schema-extensions.md).
