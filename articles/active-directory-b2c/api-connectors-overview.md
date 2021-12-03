---
title: Informationen zu API-Connectors in Azure AD B2C
description: Verwenden Sie Azure Active Directory (Azure AD)-API-Connectors, um Ihre Benutzerflows mithilfe von REST-APIs oder ausgehenden Webhooks auf externe Identitätsdatenquellen anzupassen und zu erweitern.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 11/02/2021
ms.author: kengaderdus
author: kengaderdus
manager: CelesteDG
ms.custom: it-pro
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 9b5d3c49019d1953dd0deb5d498291d92af4aba1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131436933"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows-with-external-identity-data-sources"></a>Verwenden von API-Connectors zum Anpassen und Erweitern von Benutzerflows für die Registrierung mit externen Identitätsdatenquellen 

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

## <a name="overview"></a>Übersicht 

Mithilfe von API-Konnektoren können Sie als Entwickler oder IT-Administrator Ihre Anmelde-Benutzerströme mit REST-APIs integrieren, um das Anmeldeverfahren anzupassen und mit externen Systemen zu integrieren. API-Connectors bieten beispielsweise folgende Möglichkeiten:

- **Überprüfen von Benutzereingabedaten**. Überprüfen Sie auf fehlerhafte oder ungültige Benutzerdaten. Beispielsweise können Sie vom Benutzer bereitgestellte Daten im Vergleich mit vorhandenen Daten in einem externen Datenspeicher oder einer Liste zulässiger Werte überprüfen. Falls ungültig, können Sie einen Benutzer auffordern, gültige Daten anzugeben, oder den Benutzer daran hindern, den Registrierungsflow fortzusetzen.
- **Benutzeridentität verifizieren**. Verwenden Sie einen Identitätsüberprüfungsdienst oder externe Identitätsdatenquellen, um Entscheidungen bei der Kontoerstellung eine zusätzliche Sicherheitsstufe hinzuzufügen.
- **Integrieren in einen benutzerdefinierten Genehmigungsworkflow**. Verbinden mit einem benutzerdefinierten Genehmigungssystem zum Verwalten und Einschränken der Kontoerstellung.
- **Erweitern Sie Token mit Attributen aus externen Quellen**. Reichern Sie Token mit Attributen für den Benutzer aus Quellen außerhalb von Azure AD B2C an, z. B. Cloudsystemen, benutzerdefinierten Benutzerspeichern, benutzerdefinierten Berechtigungssystemen, Legacyidentitätsdiensten usw.
- **Überschreiben von Benutzerattributen**. Sie können ein vom Benutzer erfasstes Attribut neu formatieren oder ihm einen Wert zuweisen. Wenn z.B. ein Benutzer den Vornamen vollständig in Kleinbuchstaben oder Großbuchstaben eingibt, können Sie den Namen so formatieren, dass nur der erste Buchstabe groß geschrieben wird. 
- **Ausführen von benutzerdefinierter Geschäftslogik**. Sie können nachgelagerte Ereignisse in Ihren Cloudsystemen auslösen, um Pushbenachrichtigungen zu senden, Unternehmensdatenbanken zu aktualisieren, Berechtigungen zu verwalten, Datenbanken zu überwachen und andere benutzerdefinierte Aktionen auszuführen.

Ein API-Konnektor versorgt Azure AD B2C mit den Informationen, die für den Aufruf des API-Endpunkts erforderlich sind, indem er die HTTP-Endpunkt-URL und die Authentifizierung für den API-Aufruf definiert. Sobald Sie einen API-Connector konfiguriert haben, können Sie ihn für einen bestimmten Schritt in einem Benutzerflow aktivieren. Wenn ein Benutzer diesen Schritt im Registrierungsflow erreicht, wird der API-Connector aufgerufen und als HTTP-POST-Anforderung an Ihre API materialisiert, wobei Benutzerinformationen („Ansprüche“) als Schlüssel-Wert-Paare in einem JSON-Text gesendet werden. Die API-Antwort kann die Ausführung des Benutzerflows beeinträchtigen. Beispielsweise kann die API-Antwort einen Benutzer für die Anmeldung sperren, den Benutzer auffordern, Informationen erneut einzugeben, oder Benutzerattribute überschreiben.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Wann ein API-Connector in einem Benutzerflow aktiviert werden kann

Es gibt drei Stellen in einem Benutzerflow, an denen Sie einen API-Connector aktivieren können:

- **Nach dem Verbund mit einem Identitätsanbieter während der Registrierung** (gilt nur für Registrierungen)
- **Vor dem Erstellen des Benutzers** (gilt nur für Registrierungen)
- **Vor dem Senden des Tokens (Vorschau)** (gilt für Registrierungen und Anmeldungen)

### <a name="after-federating-with-an-identity-provider-during-sign-up"></a>Nach dem Verbund mit einem Identitätsanbieter während der Registrierung

Ein API-Connector in diesem Schritt des Registrierungsprozesses wird unmittelbar nach der Authentifizierung des Benutzers bei einem Identitätsanbieter (etwa Google, Facebook und Azure AD) aufgerufen. Dieser Schritt geht der ***Seite zur Attributsammlung***  voraus, die dem Benutzer zum Sammeln von Benutzerattributen angezeigt wird. Dieser Schritt wird nicht aufgerufen, wenn sich ein Benutzer mit einem lokalen Konto registriert. Im Folgenden finden Sie Beispiele für Szenarien mit API-Connectors, die Sie in diesem Schritt aktivieren können:

- Verwenden Sie die E-Mail- oder Verbundidentität, die der Benutzer angegeben hat, um Ansprüche in einem bestehenden System nachzuschlagen. Geben Sie diese Ansprüche aus dem bestehenden System zurück, füllen Sie die Seite zur Attributsammlung vorab aus, und stellen Sie sie zur Rückgabe im Token zur Verfügung.
- Implementieren Sie eine Zulassungs- oder Sperrliste basierend auf der sozialen Identität.

### <a name="before-creating-the-user"></a>Vor dem Erstellen des Benutzers

Ein API-Connector in diesem Schritt des Registrierungsprozesses wird nach der Seite zur Attributsammlung aufgerufen, sofern vorhanden. Dieser Schritt wird immer aufgerufen, bevor ein Benutzerkonto erstellt wird. Es folgen Beispiele für Szenarien, die Sie an dieser Stelle während der Registrierung aktivieren könnten:

- Überprüfen eingegebener Benutzerdaten und Aufforderung an einen Benutzer, Daten erneut zu übermitteln
- Sperren einer Benutzerregistrierung auf Grundlage der vom Benutzer eingegebenen Daten
- Verifizieren Sie die Benutzeridentität.
- Abfragen externer Systeme nach vorhandenen Daten zum Benutzer, um diese im Anwendungstoken zurückzugeben oder in Azure AD zu speichern

### <a name="before-sending-the-token-preview"></a>Vor dem Senden des Tokens (Vorschau)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

In diesem Schritt wird ein API-Connector im Registrierungs- oder Anmeldeprozess aufgerufen, bevor ein Token ausgestellt wird. Im Folgenden finden Sie Beispiele für Szenarien, die Sie in diesem Schritt aktivieren können:
- Anreicherung des Tokens mit Attributen für den Benutzer aus anderen Quellen als dem Verzeichnis, z. B. Legacyidentitätssystemen, Systemen für das Personalwesen, externen Benutzerspeichern usw.
- Anreicherung des Tokens mit Gruppen- oder Rollenattributen, die Sie in Ihrem eigenen Berechtigungssystem speichern und verwalten. 
- Anwenden von Anspruchstransformationen oder -manipulationen auf Werte von Ansprüchen im Verzeichnis.

::: zone-end

::: zone pivot="b2c-custom-policy"

Das Identity Experience Framework (IEF), das Azure Active Directory B2C (Azure AD B2C) zugrunde liegt, kann im Rahmen einer User Journey mit RESTful-APIs integriert werden. In diesem Artikel wird gezeigt, wie Sie eine User Journey erstellen, die mithilfe eines [technischen RESTful-Profils](restful-technical-profile.md) mit einem RESTful-Dienst interagiert.

Mit Azure AD B2C können Sie einer User Journey Ihre eigene Geschäftslogik hinzufügen, indem Sie Ihren eigenen RESTful-Dienst aufrufen. Das Identity Experience Framework kann Daten an Ihren RESTful-Dienst senden und von ihm empfangen, um Ansprüche auszutauschen. Beispielsweise können Sie folgende Aktionen ausführen:

- **Verwenden einer externen Identitätsdatenquelle zum Überprüfen von Benutzereingabedaten**. Sie können beispielsweise überprüfen, ob die vom Benutzer angegebene E-Mail-Adresse in Ihrer Kundendatenbank vorhanden ist. Und wenn dies nicht der Fall ist, können Sie einen Fehler ausgeben. Sie können sich API-Connectors auch als eine Möglichkeit vorstellen, ausgehende Webhooks zu unterstützen, da der Aufruf beim Auftreten eines Ereignisses (z. B. Registrierung) erfolgt.
- **Verarbeiten von Ansprüchen**. Wenn ein Benutzer seinen Vornamen vollständig in Kleinbuchstaben oder Großbuchstaben eingibt, kann die REST-API den Namen so formatieren, dass nur der erste Buchstabe groß geschrieben wird, und den Namen in dieser Form an Azure AD B2C zurückgeben. Wenn Sie jedoch eine benutzerdefinierte Richtlinie verwenden, wird [ClaimsTransformations](claimstransformations.md) gegenüber dem Aufrufen einer RESTful-API bevorzugt. 
- **Dynamisches Anreichern von Benutzerdaten durch eine stärkere Integration in die Branchenanwendungen des Unternehmens**. Ihr RESTful-Dienst kann die E-Mail-Adresse des Benutzers empfangen, die Kundendatenbank abfragen und die Treuenummer des Benutzers an Azure AD B2C zurückgeben. Die Rückgabeansprüche können dann im Azure AD-Konto des Benutzers gespeichert, in den nächsten Orchestrierungsschritten ausgewertet oder in das Zugriffstoken eingebunden werden.
- **Ausführen von benutzerdefinierter Geschäftslogik**. Sie können Pushbenachrichtigungen senden, Unternehmensdatenbanken aktualisieren, einen Benutzermigrationsprozess ausführen, Berechtigungen verwalten, Datenbanken überwachen und andere Workflows ausführen.

![Diagramm eines Anspruchsaustauschs über den RESTful-Dienst](media/api-connectors-overview/restful-service-claims-exchange.png)

> [!NOTE]
> Erfolgt eine langsame oder keine Antwort vom RESTful-Dienst an Azure AD B2C, so beträgt der Timeout 30 Sekunden und die Anzahl der Wiederholungsversuche zwei (d. h. es gibt insgesamt 3 Versuche). Die Einstellungen für das Timeout und die Anzahl der Wiederholungsversuche können derzeit nicht konfiguriert werden.

## <a name="calling-a-restful-service"></a>Aufrufen eines RESTful-Diensts

Die Interaktion umfasst einen Anspruchsaustausch von Informationen zwischen den REST-API-Ansprüchen und Azure AD B2C. Sie können die Integration in die RESTful-Dienste auf folgende Weise entwerfen:

- **Technisches Validierungsprofil**. Der Anruf des RESTful-Diensts erfolgt innerhalb eines [technischen Validierungsprofils](validation-technical-profile.md) des angegebenen [selbstbestätigten technischen Profils](self-asserted-technical-profile.md) oder über ein [Anzeigesteuerelement zur Überprüfung](display-control-verification.md) einer [Anzeigesteuerung](display-controls.md). Mit dem technischen Validierungsprofil werden die vom Benutzer bereitgestellten Daten überprüft, bevor die User Journey fortgesetzt wird. Beim technischen Validierungsprofil haben Sie folgende Möglichkeiten:

  - Senden von Ansprüchen an Ihre REST-API
  - Überprüfen von Ansprüchen und Auslösen von benutzerdefinierten Fehlermeldungen, die dem Benutzer angezeigt werden
  - Senden Sie Forderungen von der REST-API an die nächsten Orchestrierungs Schritte zurück.

- **Anspruchsaustausch**. Ein direkter Anspruchsaustausch kann durch Aufrufen eines technischen REST-API-Profils direkt aus einem Orchestrierungsschritt in einer [User Journey](userjourneys.md) konfiguriert werden. Diese Definition ist auf folgende Vorgänge beschränkt:

  - Senden von Ansprüchen an Ihre REST-API
  - Überprüfen von Ansprüchen und Auslösen von benutzerdefinierten Fehlermeldungen, die an die Anwendung zurückgegeben werden
  - Senden Sie Ansprüche von der REST-API an die nächsten Orchestrierungs Schritte zurück.

Sie können einen Rest-API-Aufruf in jedem Schritt der User Journey hinzufügen, die durch eine benutzerdefinierte Richtlinie definiert ist. Beispielsweise können Sie in folgenden Schritten eine Rest-API aufrufen:

- Während der Anmeldung, kurz bevor Azure AD B2C die Anmeldeinformationen überprüft
- Unmittelbar nach der Anmeldung
- Bevor Azure AD B2C ein neues Konto im Verzeichnis erstellt
- Nachdem Azure AD B2C ein neues Konto im Verzeichnis erstellt hat
- Bevor Azure AD B2C ein Zugriffstoken ausgibt

![Erfassung über das technische Validierungsprofil](media/api-connectors-overview/validation-technical-profile.png)

## <a name="sending-data"></a>Senden von Daten

Das `InputClaims`-Element im [technischen RESTful-Profil](restful-technical-profile.md) enthält eine Liste der Ansprüche, die an den RESTful-Dienst gesendet werden sollen. Sie können den Namen Ihres Anspruchs dem im RESTful-Dienst definierten Namen zuordnen, einen Standardwert festlegen und [Anspruchskonfliktlöser](claim-resolver-overview.md) verwenden.

Mit dem Attribut „SendClaimsIn“ können Sie konfigurieren, wie die Eingabeansprüche an den RESTful-Anspruchsanbieter gesendet werden. Mögliche Werte:

- **Body**: Wird im JSON-Format im HTTP POST-Anforderungstext gesendet.
- **Form**: Wird im HTTP POST-Anforderungstext in einem durch kaufmännische Und-Zeichen (&) getrennten Schlüsselwertformat gesendet.
- **Header**: Wird im HTTP GET-Anforderungsheader gesendet.
- **QueryString**: Wird in der Abfragezeichenfolge der HTTP GET-Anforderung gesendet.

Wenn die Option **Body** konfiguriert wird, können Sie mit dem technischen REST-API-Profil eine komplexe JSON-Nutzlast an einen Endpunkt senden. Weitere Informationen finden Sie unter [Senden einer JSON-Nutzlast](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Empfangen von Daten

Das `OutputClaims`-Element des [technischen RESTful-Profils](restful-technical-profile.md) enthält eine Liste von Ansprüchen, die von der REST-API zurückgegeben werden. Sie müssen den Namen des Anspruchs, der in Ihrer Richtlinie definiert ist, dem Namen, der in der REST-API definiert wurde, zuordnen. Sie können auch Ansprüche einfügen, die nicht vom REST-API-Identitätsanbieter zurückgegeben werden, wenn Sie das Attribut „DefaultValue“ festlegen.

Bei den Ausgabeansprüchen, die vom RESTful-Anspruchsanbieter analysiert werden, wird immer die Analyse einer einfachen JSON-Textantwort erwartet. Beispiel:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

Die ausgegebenen Ansprüche sollten wie das folgende xml-Snippet aussehen:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

### <a name="handling-null-values"></a>Behandlung von NULL-Werten 

Ein NULL-Wert wird in einer Datenbank verwendet, wenn der Wert in einer Spalte unbekannt ist oder fehlt.  Fügen Sie keine JSON-Schlüssel mit einem `null`-Wert ein. Im folgenden Beispiel gibt die E-Mail einen `null`-Wert zurück:

```json
{
  "name": "Emily Smith",
  "email": null,
  "loyaltyNumber":  1234
}
```

Wenn ein Element NULL ist, haben Sie folgende Möglichkeiten:

- Lassen Sie das Schlüssel-Wert-Paar im JSON-Code aus.
- Geben einen Wert zurück, der dem Azure AD B2C-Anspruchsdatentyp entspricht. Geben Sie beispielsweise für einen `string`-Datentyp eine leere Zeichenfolge, `""`, zurück. Geben Sie für einen `integer`-Datentyp einen Nullwert, `0`, zurück. Geben Sie für einen `dateTime`-Datentyp einen Mindestwert, `1970-00-00T00:00:00.0000000Z`, zurück.

Das folgende Beispiel zeigt, wie ein NULL-Wert verarbeitet wird. Die E-Mail wird im JSON ausgelassen:

```json
{
  "name": "Emily Smith",
  "loyaltyNumber":  1234
}
```

### <a name="parse-a-nested-json-body"></a>Analysieren eines geschachtelten JSON-Textkörpers

Zum Analysieren einer geschachtelten JSON-Textantwort müssen Sie die Metadaten „ResolveJsonPathsInJsonTokens“ auf „true“ festlegen. Legen Sie im Ausgabeanspruch den Wert für „PartnerClaimType“ auf das auszugebende JSON-Pfadelement fest.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


Die ausgegebenen Ansprüche sollten wie das folgende xml-Snippet aussehen:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="localize-the-rest-api"></a>Lokalisieren der REST-API

In einem technischen RESTful-Profil empfiehlt es sich, die Sprache bzw. das Gebietsschema der aktuellen Sitzung zu senden und bei Bedarf eine lokalisierte Fehlermeldung auszugeben. Mit dem [Anspruchskonfliktlöser](claim-resolver-overview.md) können Sie einen kontextbezogenen Anspruch (z. B. die Benutzersprache) senden. Das folgende Beispiel zeigt ein technisches RESTful-Profil mit diesem Szenario.

```xml
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Verarbeiten von Fehlermeldungen

Ihre REST-API muss möglicherweise eine Fehlermeldung zurückgeben (z. B. „Der Benutzer wurde nicht im CRM-System gefunden“). Wenn ein Fehler auftritt, sollte die REST-API die HTTP-Fehlermeldung 409 (Antwortstatuscode „Konflikt“) zurückgeben. Weitere Informationen finden Sie unter [Technisches RESTful-Profil](restful-technical-profile.md#returning-validation-error-message).

Ein solches Verhalten kann nur durch den Aufruf eines technischen REST-API-Profils aus einem technischen Validierungsprofil erreicht werden. Dadurch kann der Benutzer die Daten auf der Seite korrigieren und die Validierung bei der Seitenübermittlung erneut ausführen.

Wenn Sie direkt aus einer User Journey auf ein technisches REST-API-Profil verweisen, wird der Benutzer mit der entsprechenden Fehlermeldung wieder an die Anwendung der vertrauenden Seite zurückgeleitet.

::: zone-end

## <a name="development-of-your-rest-api"></a>Entwicklung Ihrer REST-API 

Ihre REST-API kann auf beliebigen Plattformen entwickelt werden und in beliebigen Programmiersprachen geschrieben sein, solange sie sicher ist und Forderungen im JSON-Format senden und empfangen kann.

Die Anforderung an Ihren REST-API-Dienst kommt von Azure AD B2C-Servern. Der REST-API-Dienst muss auf einem öffentlich zugänglichen HTTPS-Endpunkt veröffentlicht werden. Die REST-API-Aufrufe gehen von der IP-Adresse eines Azure-Rechenzentrums ein.

Sie können serverlose Cloudfunktionen wie [HTTP-Trigger in Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md) verwenden, um die Entwicklung zu vereinfachen.

Sie sollten beim Entwurf Ihres REST-API-Diensts und der zugrundeliegenden Komponenten (z. B. Datenbank und Dateisystem) auf Hochverfügbarkeit achten.

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="next-steps"></a>Nächste Schritte

::: zone pivot="b2c-user-flow"

- Erfahren Sie, wie Sie [einen API-Connector zum Ändern der Registrierung hinzufügen](add-api-connector.md).
- Erfahren Sie, wie Sie [einen API-Connector hinzufügen, um Token mit externen Ansprüchen anzureichern](add-api-connector-token-enrichment.md).
- Erfahren Sie mehr über das [Schützen Ihres API-Connectors](secure-rest-api.md).
- Erste Schritte mit den [Beispielen](api-connector-samples.md#api-connector-rest-api-samples)

::: zone-end

::: zone pivot="b2c-custom-policy"

In den folgenden Artikeln finden Sie Beispiele für die Verwendung eines technischen RESTful-Profils:

- [Exemplarische Vorgehensweise: Hinzufügen eines API-Connectors zu einem Benutzerflow für die Registrierung](add-api-connector.md)
- [Exemplarische Vorgehensweise: Hinzufügen von REST-API-Anspruchsaustauschvorgängen zu benutzerdefinierten Richtlinien in Azure Active Directory B2C](add-api-connector-token-enrichment.md)
- [Sichern von REST-API-Diensten](secure-rest-api.md)
- [Referenz: Technisches Profil „RESTful“](restful-technical-profile.md)

::: zone-end
