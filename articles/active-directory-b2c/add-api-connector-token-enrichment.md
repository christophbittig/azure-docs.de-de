---
title: Tokenanreicherung – Azure Active Directory B2C
description: Anreichern von Token mit Ansprüchen aus externen Identitätsdatenquellen mithilfe von APIs oder ausgehenden Webhooks.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/09/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a6f91fd8de1f208bcbe57a9e541ad1e73340e995
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179477"
---
# <a name="enrich-tokens-with-claims-from-external-sources-using-api-connectors"></a>Anreichern von Token mit Ansprüchen aus externen Quellen unter Verwendung von API-Connectors

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Mit Azure Active Directory B2C (Azure AD B2C) können Identitätsentwickler unter Verwendung von [API-Connectors](api-connectors-overview.md) eine Interaktion mit einer RESTful-API in ihren Benutzerflow integrieren. So können Entwickler dynamisch Daten aus externen Identitätsquellen abrufen. Am Ende dieser exemplarischen Vorgehensweise können Sie einen Azure AD B2C-Benutzerflow erstellen, der mit APIs interagiert, um Token mit Informationen aus externen Quellen anzureichern.

::: zone pivot="b2c-user-flow"

Sie können API-Connectors verwenden, die auf den Schritt **Vor dem Senden des Tokens (Vorschau)** angewendet werden, um Token für Ihre Anwendungen mit Informationen aus externen Quellen anzureichern. Wenn sich ein Benutzer anmeldet oder registriert, ruft Azure AD B2C den im API-Connector konfigurierten API-Endpunkt auf, der Informationen zu einem Benutzer in Downstreamdiensten wie Clouddiensten, benutzerdefinierten Benutzerspeichern, benutzerdefinierten Berechtigungssystemen, älteren Identitätssystemen usw. abfragen kann.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Sie können anhand unserer [Beispiele](api-connector-samples.md#api-connector-rest-api-samples) einen API-Endpunkt erstellen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-api-connector"></a>Erstellen eines API-Connectors

Um einen [API-Connector](api-connectors-overview.md) zu verwenden, erstellen Sie zunächst den API-Connector und aktivieren ihn anschließend in einem Benutzerflow.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus.
4. Wählen Sie **API-Connectors** und dann **Neuer API-Connector** aus.

   ![Screenshot: grundlegende API-Connectorkonfiguration](media/add-api-connector-token-enrichment/api-connector-new.png)

5. Geben Sie einen Anzeigenamen für den Aufruf an, z. B. **Token aus externer Quelle anreichern**.
6. Geben Sie die **Endpunkt-URL** für den API-Aufruf an.
7. Wählen Sie den **Authentifizierungstyp** aus, und konfigurieren Sie die Authentifizierungsinformationen zum Aufrufen Ihrer API. Erfahren Sie mehr über das [Schützen Ihres API-Connectors](secure-rest-api.md).

   ![Screenshot der Authentifizierungskonfiguration für einen API-Connector](media/add-api-connector-token-enrichment/api-connector-config.png)

8. Wählen Sie **Speichern** aus.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Aktivieren des API-Connectors in einem Benutzerflow

Führen Sie die folgenden Schritte aus, um einem Benutzerflow für die Registrierung einen API-Connector hinzuzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus.
4. Wählen Sie **Benutzerflows** und dann den Benutzerflow aus, dem Sie den API-Connector hinzufügen möchten.
5. Wählen Sie **API-Connectors** und dann den API-Endpunkt aus, der im Schritt **Vor dem Senden des Tokens (Vorschau)** aufgerufen werden soll:

   ![Screenshot: Auswählen eines API-Connectors für einen Benutzerflowschritt](media/add-api-connector-token-enrichment/api-connectors-user-flow-select.png)

6. Wählen Sie **Speichern** aus.

Diesen Schritt gibt es nur für die Benutzerflows **Registrieren und anmelden (empfohlen)** , **Registrieren (empfohlen)** und **Anmelden (empfohlen)** .

## <a name="example-request-sent-to-the-api-at-this-step"></a>In diesem Schritt an die API gesendete Beispielanforderung

In diesem Schritt wird ein API-Connector bei Anmeldungen und Registrierungen unmittelbar vor dem Ausstellen eines Tokens aufgerufen. 

Ein API-Connector wird als **HTTP POST**-Anforderung dargestellt und sendet Benutzerattribute („Ansprüche“) als Schlüssel-Wert-Paare in einem JSON-Text. Attribute werden ähnlich wie [Microsoft Graph](/graph/api/resources/user#properties)-Benutzereigenschaften serialisiert. 

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "objectId": "ab3ec3b2-a435-45e4-b93a-56a005e88bb7",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "client_id": "231c70e8-8424-48ac-9b5d-5623b9e4ccf3",
 "step": "PreTokenIssuance",
 "ui_locales":"en-US"
}
```

Die Ansprüche, die an die API gesendet werden, hängen von den für den Benutzer definierten Informationen ab.

Nur Benutzereigenschaften und benutzerdefinierte Attribute, die unter **Azure AD B2C** > **Benutzerattribute** aufgeführt sind, können in der Anforderung gesendet werden.

Benutzerdefinierte Attribute sind im Verzeichnis im Format **extension_\<extensions-app-id>_CustomAttribute** vorhanden. Die API sollte den Empfang von Ansprüchen in diesem serialisierten Format erwarten. Weitere Informationen zu benutzerdefinierten Attributen finden Sie unter [Definieren benutzerdefinierter Attribute in Azure AD B2C](user-flow-custom-attributes.md).

Darüber hinaus werden in der Regel die folgenden Ansprüche in allen Anforderungen für diesen Schritt gesendet:
- **UI-Gebietsschemas ('ui_locales')** : Die auf dem Gerät konfigurierten Gebietsschemas eines Endbenutzers. Kann von Ihrer API verwendet werden, um internationalisierte Antworten zurückzugeben.
- **Schritt ('step')** : Der Schritt oder Punkt im Benutzerflow, für den der API-Connector aufgerufen wurde. Der Wert für diesen Schritt ist `
- **Client-ID ('client_id')** : Der `appId`-Wert der Anwendung, der für die Authentifizierung des Endbenutzers in einem Benutzerflow verwendet wird. Dabei handelt es sich *nicht* um den `appId`-Wert der Ressourcenanwendung in Zugriffstoken.
- **objectId**: Der Bezeichner des Benutzers. Damit können Sie Downstreamdienste nach Informationen zum Benutzer abfragen.
  
> [!IMPORTANT]
> Wenn ein Anspruch zum Zeitpunkt des Aufrufs des API-Endpunkts keinen Wert enthält, wird der Anspruch nicht an die API gesendet. Ihre API sollte so entworfen sein, dass explizit geprüft wird, ob ein Anspruch in der Anforderung enthalten ist. Fehlt der Anspruch, sollte eine entsprechende Verarbeitung erfolgen.

## <a name="expected-response-types-from-the-web-api-at-this-step"></a>Von der Web-API in diesem Schritt erwartete Antworttypen

Wenn die Web-API während eines Benutzerflows eine HTTP-Anforderung von Azure AD empfängt, kann sie eine „Fortsetzungsantwort“ zurückgeben.

### <a name="continuation-response"></a>Fortsetzungsantwort

Eine Fortsetzungsantwort gibt an, dass der Benutzerflow mit dem nächsten Schritt, Ausstellen des Tokens, fortgesetzt werden soll.

In einer Fortsetzungsantwort kann die API weitere Ansprüche zurückgeben. Ein von der API zurückgegebener Anspruch, der im Token zurückgegeben werden soll, muss ein vordefinierter Anspruch oder [als benutzerdefiniertes Attribut definiert](user-flow-custom-attributes.md) sein und muss in der Konfiguration der **Anwendungsansprüche** des Benutzerflows ausgewählt werden. 

Der Anspruchswert im Token ist der von der API zurückgegebene Wert, nicht der Wert im Verzeichnis. Einige Anspruchswerte können nicht von der API-Antwort überschrieben werden. Ansprüche, die von der API zurückgegeben werden können, entsprechen den Ansprüchen unter **Benutzerattribute** (mit Ausnahme von `email`).

> [!NOTE]
> Die API wird nur während einer ersten Authentifizierung aufgerufen. Wenn Aktualisierungstoken verwendet werden, um automatisch neue Zugriffs- oder ID-Token zu erhalten, enthält das Token die Werte, die bei der ersten Authentifizierung ermittelt wurden. 

## <a name="example-response"></a>Beispielantwort

### <a name="example-of-a-continuation-response"></a>Beispiel für eine Fortsetzungsantwort

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Parameter                                          | type              | Erforderlich | BESCHREIBUNG                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version     | String | Ja      | Die Version Ihrer API                                                    |
| action                                             | String            | Ja      | Der Wert muss `Continue` sein.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Nein       | Diese können bei Auswahl als **Anwendungsanspruch** auch im Token zurückgegeben werden.                                        |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Nein       | Der Anspruch muss `_<extensions-app-id>_` nicht enthalten, dies ist *optional*. Diese können bei Auswahl als **Anwendungsanspruch** auch im Token zurückgegeben werden.  |

::: zone-end

::: zone pivot="b2c-custom-policy"

In diesem Szenario reichern wir die Tokendaten des Benutzers an, indem wir sie in einen unternehmensinternen branchenspezifischen Workflow integrieren. Während der Registrierung oder Anmeldung mit einem lokalen oder Verbundkonto ruft Azure AD B2C eine REST-API auf, um die erweiterten Profildaten des Benutzers aus einer Remotedatenquelle abzurufen. In diesem Beispiel sendet Azure AD B2C den eindeutigen Bezeichner des Benutzers (objectId). Die REST-API gibt dann den Kontostand des Benutzers zurück (eine zufällige Zahl). Nutzen Sie dieses Beispiel als Ausgangspunkt für die Integration in Ihr eigenes CRM-System, Ihre Marketingdatenbank oder einen beliebigen branchenspezifischen Workflow.

Sie können die Interaktion auch als technisches Überprüfungsprofil gestalten. Dies bietet sich an, wenn die REST-API Daten auf dem Bildschirm überprüft und Ansprüche zurückgibt. Weitere Informationen finden Sie unter [Hinzufügen eines API-Connectors zu einem Benutzerflow für die Registrierung](add-api-connector.md).

## <a name="prerequisites"></a>Voraussetzungen

- Führen Sie die Schritte unter [Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy) aus. Sie sollten eine funktionierende benutzerdefinierte Richtlinie für die Registrierung und Anmeldung bei lokalen Konten besitzen.
- Weitere Informationen finden Sie unter [Integrieren von REST-API-Anspruchsaustauschvorgängen in Ihre benutzerdefinierte Azure AD B2C-Richtlinie](api-connectors-overview.md).

## <a name="prepare-a-rest-api-endpoint"></a>Vorbereiten eines REST-API-Endpunkts

Für diese exemplarische Vorgehensweise benötigen Sie eine REST-API, die überprüft, ob die Azure AD B2C-objectId eines Benutzers in Ihrem Back-End-System registriert ist. Falls ja, gibt die REST-API den Kontostand des Benutzers zurück. Andernfalls registriert die REST-API das neue Konto im Verzeichnis und gibt den Ausgangssaldo `50.00` zurück.

Der folgende JSON-Code veranschaulicht die Daten, die Azure AD B2C an Ihren REST-API-Endpunkt sendet. 

```json
{
    "objectId": "User objectId",
    "lang": "Current UI language"
}
```

Sobald Ihre REST-API die Daten überprüft, muss sie HTTP 200 (OK) mit den folgenden JSON-Daten zurückgeben:

```json
{
    "balance": "760.50"
}
```

Die Einrichtung des REST-API-Endpunkts wird in diesem Artikel nicht behandelt. Wir haben ein [Azure Functions](../azure-functions/functions-reference.md)-Beispiel erstellt. Sie können auf [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function) auf den gesamten Azure-Funktionscode zugreifen.

## <a name="define-claims"></a>Definieren von Ansprüchen

Ein Anspruch bietet eine temporäre Speicherung von Daten während der Ausführung einer Azure AD B2C-Richtlinie. Sie können Ansprüche im Abschnitt [Anspruchsschema](claimsschema.md) deklarieren. 

1. Öffnen Sie die Erweiterungsdatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Suchen Sie nach dem Element [BuildingBlocks](buildingblocks.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Suchen Sie nach dem Element [ClaimsSchema](claimsschema.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Fügen Sie dem Element **ClaimsSchema** die folgenden Ansprüche hinzu.  

```xml
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Hinzufügen des technischen Profils für die RESTful-API 

Ein [technisches RESTful-Profil](restful-technical-profile.md) bietet Unterstützung bei der Anbindung an Ihren eigenen RESTful-Dienst. Azure AD B2C sendet Daten an den RESTful-Dienst in einer `InputClaims`-Sammlung und erhält Daten in einer `OutputClaims`-Sammlung zurück. Suchen Sie das Element **ClaimsProviders** in Ihrer Datei <em> **`TrustFrameworkExtensions.xml`**</em>, und fügen Sie wie folgt einen neuen Anspruchsanbieter hinzu:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
``` 

In diesem Beispiel wird `userLanguage` als `lang` innerhalb der JSON-Nutzlast an den REST-Dienst gesendet. Der Wert des Anspruchs `userLanguage` enthält die ID der aktuellen Benutzersprache. Weitere Informationen finden Sie unter [Anspruchskonfliktlöser](claim-resolver-overview.md).

### <a name="configure-the-restful-api-technical-profile"></a>Konfigurieren des technischen Profils für die RESTful-API 

Nachdem Sie Ihre REST-API bereitgestellt haben, legen Sie die Metadaten des technischen Profils `REST-GetProfile` so fest, dass sie Ihre eigene REST-API wiedergeben, darunter:

- **ServiceUrl**. Legt die URL des REST-API-Endpunkts fest.
- **SendClaimsIn**. Gibt an, wie die Eingabeansprüche an den RESTful-Anspruchsanbieter gesendet werden.
- **AuthenticationType**. Legen Sie die Art der Authentifizierung fest, die vom RESTful-Anbieter durchgeführt wird, z. B. `Basic` oder `ClientCertificate` 
- **AllowInsecureAuthInProduction**. In einer Produktionsumgebung sollten Sie diese Metadaten auf `false` setzen.
    
Weitere Konfigurationen finden Sie in den [Metadaten für das technische RESTful-Profil](restful-technical-profile.md#metadata).

Die Kommentare `AuthenticationType` und `AllowInsecureAuthInProduction` oben geben Änderungen an, die Sie beim Wechsel zu einer Produktionsumgebung vornehmen sollten. Wie Sie Ihre RESTful-APIs für die Produktion sichern können, erfahren Sie unter [Sichern Sie Ihre RESTful-API](secure-rest-api.md).

## <a name="add-an-orchestration-step"></a>Hinzufügen eines Orchestrierungsschritts

[User Journeys](userjourneys.md) geben explizite Pfade an, über die eine Richtlinie einer Anwendung der vertrauenden Seite die gewünschten Ansprüche für einen Benutzer abrufen kann. Eine User Journey wird als Orchestrierungssequenz dargestellt, die für eine erfolgreiche Transaktion durchlaufen werden muss. Sie können Orchestrierungsschritte hinzufügen oder entfernen. In diesem Fall fügen Sie einen neuen Orchestrierungsschritt hinzu, der verwendet wird, um die Informationen zu erweitern, die der Anwendung nach der Benutzerregistrierung oder Anmeldung über den REST-API-Aufruf zur Verfügung gestellt werden.

1. Öffnen Sie die Basisdatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>.
1. Suchen Sie nach dem `<UserJourneys>`-Element. Kopieren Sie das gesamte Element, und löschen Sie es anschließend.
1. Öffnen Sie die Erweiterungsdatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Fügen Sie `<UserJourneys>` nach dem Schließen des Elements `<ClaimsProviders>` in die Erweiterungsdatei ein.
1. Suchen Sie `<UserJourney Id="SignUpOrSignIn">`, und fügen Sie den folgenden Orchestrierungsschritt vor dem letzten hinzu.

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Gestalten Sie den letzten Orchestrierungsschritt um, indem Sie `Order` in `8` ändern. Die letzten zwei Orchestrierungsschritte sollten wie folgt aussehen:

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Wiederholen Sie die letzten beiden Schritte für die User Journeys **ProfileEdit** und **PasswordReset**.


## <a name="include-a-claim-in-the-token"></a>Einschließen eines Anspruchs in das Token 

Um den Anspruch `balance` an die Anwendung der vertrauenden Seite zurückzugeben, fügen Sie der Datei <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> einen Ausgabeanspruch hinzu. Durch Hinzufügen eines Ausgabeanspruchs wird der Anspruch nach einer erfolgreichen User Journey in das Token ausgegeben und an die Anwendung gesendet. Ändern Sie im Abschnitt für die vertrauende Seite das Element des technischen Profils, um `balance` als Ausgabeanspruch hinzuzufügen.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Wiederholen Sie diesen Schritt für die User Journeys **ProfileEdit.xml** und **PasswordReset.xml**.

Speichern Sie die Dateien, die Sie geändert haben: *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* und *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis mit Ihrem Azure AD-Mandanten verwenden, indem Sie in der Portalsymbolleiste das Symbol **Verzeichnisse + Abonnements** auswählen.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse + Abonnements** das Azure AD-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **App-Registrierungen**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Framework für die Identitätsfunktion** aus.
1. Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus, und laden Sie dann die geänderten Richtliniendateien hoch: *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* und *PasswordReset.xml*. 
1. Wählen Sie die hochgeladene Registrierungs- oder Anmelderichtlinie aus, und klicken Sie auf die Schaltfläche **Jetzt ausführen**.
1. Sie sollten sich mit einer E-Mail-Adresse oder einem Facebook-Konto anmelden können.
1. Das Token, das an die Anwendung zurückgesendet wird, enthält den Anspruch `balance`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="best-practices-and-how-to-troubleshoot"></a>Bewährte Methoden und Problembehandlungen

### <a name="using-serverless-cloud-functions"></a>Verwenden von serverlosen Cloudfunktionen

Serverlose Funktionen (z. B. [HTTP-Trigger in Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md)) bieten eine Möglichkeit zum Erstellen von API-Endpunkten, die mit dem API-Connector verwendet werden. Mit den serverlosen Cloudfunktionen können auch andere Web-APIs, Datenspeicher und andere Clouddienste für komplexere Szenarien aufgerufen werden.

### <a name="best-practices"></a>Bewährte Methoden

Stellen Sie Folgendes sicher:
* Ihre API entspricht den API-Anforderungs- und -Antwortverträgen, wie dies weiter oben beschrieben ist. 
* Die **Endpunkt-URL** des API-Connectors verweist auf den richtigen API-Endpunkt.
* In Ihrer API wird explizit auf NULL-Werte der empfangenen notwendigen Ansprüche geprüft.
* Ihre API implementiert eine Authentifizierungsmethode, die unter [Schützen Ihres API-Connectors](secure-rest-api.md) beschrieben ist.
* Ihre API antwortet so schnell wie möglich, um eine flüssige Darstellung für den Benutzer zu gewährleisten.
    * Azure AD B2C wartet maximal *20 Sekunden* auf den Empfang einer Antwort. Wird keine empfangen, wird *ein weiterer Versuch (Wiederholung)* unternommen, die API aufzurufen.
    * Verwenden Sie in der Produktion einen Hostingplan, der dafür sorgt, dass die API aktiv bleibt, wenn Sie eine serverlose Funktion oder einen skalierbaren Webdienst verwenden. Für Azure Functions wird in der Produktion zumindest die Verwendung des [Premium-Plans](../azure-functions/functions-scale.md) empfohlen.
* Stellen Sie die Hochverfügbarkeit Ihrer API sicher.
* Überwachen und optimieren Sie die Leistung von nachgelagerten APIs, Datenbanken oder anderen Abhängigkeiten Ihrer API.
  
[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

### <a name="use-logging"></a>Verwenden von Protokollierung

Üblicherweise ist es nützlich, die von Ihrem Web-API-Dienst aktivierten Protokollierungstools, etwa [Application Insights](../azure-functions/functions-monitoring.md), zu verwenden, um Ihre API auf unerwartete Fehlercodes, Ausnahmen und schlechte Leistung zu überwachen.
* Überwachen Sie auf HTTP-Statuscodes, die weder HTTP 200 noch 400 sind.
* Der HTTP-Statuscode 401 oder 403 kennzeichnet in der Regel, dass ein Problem mit Ihrer Authentifizierung vorliegt. Überprüfen Sie die Authentifizierungsschicht Ihrer API und die entsprechende Konfiguration im API-Connector.
* Verwenden Sie in der Entwicklung ggf. höhere Protokolliergrade (z. B. „trace“ oder „debug“).
* Überwachen Sie Ihre API hinsichtlich langer Antwortzeiten. 

Darüber hinaus protokolliert Azure AD B2C Metadaten für API-Transaktionen, die während der Benutzerauthentifizierung über einen Benutzerflow stattfinden. So finden Sie diese:
1. Wechseln Sie zu **Azure AD B2C**.
2. Wählen Sie unter **Aktivitäten** die Option **Überwachungsprotokolle** aus.
3. Filtern Sie die Listenansicht: Wählen Sie für **Datum** das gewünschte Zeitintervall und für **Aktivität** die Option **Im Rahmen eines Benutzerflows wurde eine API aufgerufen** aus.
4. Überprüfen Sie die einzelnen Protokolle. Jede Zeile stellt einen API-Connector dar, der während eines Benutzerflows aufgerufen werden soll. Wenn ein API-Aufruf fehlschlägt und eine Wiederholung erfolgt, wird er weiterhin als einzelne Zeile dargestellt. `numberOfAttempts` gibt an, wie oft Ihre API aufgerufen wurde. Mögliche Werte sind `1` oder `2`. Weitere Informationen zum API-Aufruf finden Sie in den Protokollen.

   ![Screenshot: Beispiel für ein Überwachungsprotokoll mit API-Connectortransaktion](media/add-api-connector-token-enrichment/example-anonymized-audit-log.png)

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

::: zone pivot="b2c-user-flow"

- Erste Schritte mit den [Beispielen](api-connector-samples.md#api-connector-rest-api-samples)
- [Schützen Ihres API-Connectors](secure-rest-api.md)

::: zone-end

::: zone pivot="b2c-custom-policy"

Informationen zum Schützen Ihrer APIs finden Sie in den folgenden Artikeln:

- [Exemplarische Vorgehensweise: Integrieren von REST-API-Anspruchsaustausch-Vorgängen in Ihre Azure AD B2C User Journey als Orchestrierungsschritt](add-api-connector-token-enrichment.md)
- [Schützen Ihrer RESTful-API](secure-rest-api.md)
- [Referenz: Technisches Profil „RESTful“](restful-technical-profile.md)

::: zone-end
