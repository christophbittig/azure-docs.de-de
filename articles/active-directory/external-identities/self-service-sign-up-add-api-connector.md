---
title: Hinzufügen von API-Connectors zu Self-Service-Registrierungsflows – Azure AD
description: Konfigurieren Sie eine Web-API zur Verwendung in einem Benutzerflow.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 07/13/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 456e02b5d84dfde9534a62ea909da513ff8f1c81
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347123"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Hinzufügen eines API-Connectors zu einem Benutzerflow

Um einen [API-Connector](api-connectors-overview.md) zu verwenden, erstellen Sie zunächst den API-Connector und aktivieren ihn anschließend in einem Benutzerflow.

> [!IMPORTANT]
>
> - Ab dem **12. Juli 2021** gilt Folgendes: Wenn Azure AD B2B-Kunden neue Google-Integrationen für die Self-Service-Registrierung für ihre benutzerdefinierten oder branchenspezifischen Anwendungen einrichten, funktioniert die Authentifizierung mit Google-Identitäten nur, wenn Authentifizierungen in Systemwebansichten verlagert werden. [Weitere Informationen](google-federation.md#deprecation-of-web-view-sign-in-support)
> - Ab dem **30. September 2021** wird Google die [Unterstützung für die Anmeldung in der eingebetteten Webansicht einstellen](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Wenn Ihre Apps Benutzer mit einer eingebetteten Webansicht authentifizieren und Sie den Google-Verbund mit [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) oder Azure AD B2B für [externe Benutzereinladungen](google-federation.md) oder die [Self-Service-Registrierung](identity-providers.md) verwenden, können sich Google Gmail-Benutzer nicht authentifizieren. [Weitere Informationen](google-federation.md#deprecation-of-web-view-sign-in-support)

## <a name="create-an-api-connector"></a>Erstellen eines API-Connectors

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure Active Directory** aus.
3. Wählen Sie im Menü auf der linken Seite **Externe Identitäten** aus.
4. Wählen Sie **Alle API-Connectors** und dann **Neuer API-Connector** aus.

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connector-new.png" alt-text="Bereitstellen der grundlegenden Konfiguration wie Ziel-URL und Anzeigename für einen API-Connector während der Erstellung":::

5. Geben Sie einen Anzeigenamen für den Aufruf an, z. B. **Genehmigungsstatus überprüfen**.
6. Geben Sie die **Endpunkt-URL** für den API-Aufruf an.
7. Wählen Sie den **Authentifizierungstyp** aus, und konfigurieren Sie die Authentifizierungsinformationen zum Aufrufen Ihrer API. Erfahren Sie mehr über das [Schützen Ihres API-Connectors](self-service-sign-up-secure-api-connector.md).

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connector-config.png" alt-text="Bereitstellen der Authentifizierungskonfiguration für einen API-Connector während der Erstellung":::

8. Wählen Sie **Speichern** aus.

## <a name="the-request-sent-to-your-api"></a>An die API gesendete Anforderung
Ein API-Connector wird als **HTTP POST**-Anforderung dargestellt und sendet Benutzerattribute („Ansprüche“) als Schlüssel-Wert-Paare in einem JSON-Text. Attribute werden ähnlich wie [Microsoft Graph](/graph/api/resources/user#properties)-Benutzereigenschaften serialisiert. 

**Beispielanforderung**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

Nur Benutzereigenschaften und benutzerdefinierte Attribute, die unter **Azure Active Directory** > **Externe Identitäten** > **Benutzerdefinierte Benutzerattribute** aufgeführt sind, können in der Anforderung gesendet werden.

Benutzerdefinierte Attribute sind im Verzeichnis im Format **extension_\<extensions-app-id>_AttributName** vorhanden. Die API sollte den Empfang von Ansprüchen in diesem serialisierten Format erwarten. Weitere Informationen zu benutzerdefinierten Attributen finden Sie unter [Definieren von benutzerdefinierten Attributen für Self-Service-Registrierungsflows](user-flow-add-custom-attributes.md).

Darüber hinaus werden in der Regel die Ansprüche in allen Anforderungen gesendet:
- **UI-Gebietsschemas („ui_locales“)** : Die auf dem Gerät konfigurierten Gebietsschemas eines Endbenutzers. Kann von Ihrer API verwendet werden, um internationalisierte Antworten zurückzugeben.
<!-- - **Step ('step')** - The step or point on the user flow that the API connector was invoked for. Values include:
  - `PostFederationSignup` - corresponds to "After federating with an identity provider during sign-up"
  - `PostAttributeCollection` - corresponds to "Before creating the user"
- **Client ID ('client_id')** - The `appId` value of the application that an end-user is authenticating to in a user flow. This is *not* the resource application's `appId` in access tokens. -->
- **E-Mail-Adresse („email“)** oder [**Identitäten („identities“)** ](/graph/api/resources/objectidentity): Diese Ansprüche können von Ihrer API zum Identifizieren des Endbenutzers verwendet werden, der sich bei der Anwendung authentifiziert.

> [!IMPORTANT]
> Wenn ein Anspruch zum Zeitpunkt des Aufrufs des API-Endpunkts keinen Wert enthält, wird der Anspruch nicht an die API gesendet. Ihre API sollte so entworfen sein, dass explizit geprüft wird, ob ein Anspruch in der Anforderung enthalten ist. Fehlt der Anspruch, sollte eine entsprechende Verarbeitung erfolgen.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Aktivieren des API-Connectors in einem Benutzerflow

Führen Sie die folgenden Schritte aus, um einem Benutzerflow für die Self-Service-Registrierung einen API-Connector hinzuzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als Azure AD-Administrator an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure Active Directory** aus.
3. Wählen Sie im Menü auf der linken Seite **Externe Identitäten** aus.
4. Wählen Sie **Benutzerflows** und dann den Benutzerflow aus, dem Sie den API-Connector hinzufügen möchten.
5. Wählen Sie **API-Connectors** und dann die API-Endpunkte aus, die bei den folgenden Schritten im Benutzerflow aufgerufen werden sollen:

   - **Nach dem Verbund mit einem Identitätsanbieter während der Registrierung**
   - **Vor dem Erstellen des Benutzers**

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png" alt-text="Auswählen des API-Connectors, der für einen Schritt im Benutzerfluss, z. B. „Vor dem Erstellen des Benutzers“, verwendet werden soll":::

6. Wählen Sie **Speichern** aus.

## <a name="after-federating-with-an-identity-provider-during-sign-up"></a>Nach dem Verbund mit einem Identitätsanbieter während der Registrierung

Ein API-Connector in diesem Schritt des Registrierungsprozesses wird unmittelbar nach der Authentifizierung des Benutzers bei einem Identitätsanbieter (etwa Google, Facebook und Azure AD) aufgerufen. Dieser Schritt geht der ***Seite zur Attributsammlung***  voraus, die dem Benutzer zum Sammeln von Benutzerattributen angezeigt wird.

### <a name="example-request-sent-to-the-api-at-this-step"></a>In diesem Schritt an die API gesendete Beispielanforderung
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

Die genauen Ansprüche, die an die API gesendet werden, hängen von den Informationen ab, die vom Identitätsanbieter bereitgestellt werden. Der Anspruch „email“ wird immer gesendet.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Von der Web-API in diesem Schritt erwartete Antworttypen

Wenn die Web-API während eines Benutzerflows eine HTTP-Anforderung von Azure AD empfängt, kann sie folgende Antworten zurückgeben:

- Fortsetzungsantwort
- Blockierungsantwort

#### <a name="continuation-response"></a>Fortsetzungsantwort

Eine Fortsetzungsantwort gibt an, dass der Benutzerflow mit dem nächsten Schritt fortgesetzt werden soll: Attributerfassungsseite.

In einer Fortsetzungsantwort kann die API Ansprüche zurückgeben. Wenn ein Anspruch von der API zurückgegeben wird, passiert mit dem Anspruch Folgendes:

- Das Eingabefeld auf der Attributerfassungsseite wird vorab aufgefüllt.

Sehen Sie sich ein Beispiel für eine [Fortsetzungsantwort](#example-of-a-continuation-response) an.

#### <a name="blocking-response"></a>Blockierungsantwort

Eine Blockierungsantwort beendet den Benutzerflow. Sie kann von der API absichtlich ausgegeben werden, um die Fortsetzung des Benutzerflows zu beenden, indem für den Benutzer eine Blockierungsseite angezeigt wird. Auf der Blockierungsseite wird die von der API angegebene `userMessage` angezeigt.

Sehen Sie sich ein Beispiel für eine [Blockierungsantwort](#example-of-a-blocking-response) an.

## <a name="before-creating-the-user"></a>Vor dem Erstellen des Benutzers

Ein API-Connector in diesem Schritt des Registrierungsprozesses wird nach der Seite zur Attributsammlung aufgerufen, sofern vorhanden. Dieser Schritt wird stets aufgerufen, ehe ein Benutzerkonto in Azure AD erstellt wird. 

### <a name="example-request-sent-to-the-api-at-this-step"></a>In diesem Schritt an die API gesendete Beispielanforderung

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```
Die genauen Ansprüche, die an die API gesendet werden, hängen von den Informationen ab, die vom Benutzer gesammelt oder vom Identitätsanbieter bereitgestellt werden.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Von der Web-API in diesem Schritt erwartete Antworttypen

Wenn die Web-API während eines Benutzerflows eine HTTP-Anforderung von Azure AD empfängt, kann sie folgende Antworten zurückgeben:

- Fortsetzungsantwort
- Blockierungsantwort
- Überprüfungsantwort

#### <a name="continuation-response"></a>Fortsetzungsantwort
Eine Fortsetzungsantwort gibt an, dass der Benutzerflow mit dem nächsten Schritt fortgesetzt werden soll: Erstellen des Benutzers im Verzeichnis.

In einer Fortsetzungsantwort kann die API Ansprüche zurückgeben. Wenn ein Anspruch von der API zurückgegeben wird, passiert mit dem Anspruch Folgendes:

- Alle Werte, die dem Anspruch über die Attributerfassungsseite bereits zugewiesen wurden, werden überschrieben.

Sehen Sie sich ein Beispiel für eine [Fortsetzungsantwort](#example-of-a-continuation-response) an.

#### <a name="blocking-response"></a>Blockierungsantwort
Eine Blockierungsantwort beendet den Benutzerflow. Sie kann von der API absichtlich ausgegeben werden, um die Fortsetzung des Benutzerflows zu beenden, indem für den Benutzer eine Blockierungsseite angezeigt wird. Auf der Blockierungsseite wird die von der API angegebene `userMessage` angezeigt.

Sehen Sie sich ein Beispiel für eine [Blockierungsantwort](#example-of-a-blocking-response) an.

### <a name="validation-error-response"></a>Validierungsfehlerantwort
 Wenn die API eine Validierungsfehlerantwort ausgibt, wird der Benutzerflow auf der Attributsammlungsseite angehalten, und dem Benutzer wird eine `userMessage` angezeigt. Der Benutzer kann dann das Formular bearbeiten und erneut senden. Diese Antwort kann für die Eingabevalidierung verwendet werden.

Sehen Sie sich ein Beispiel für eine [Validierungsfehlerantwort](#example-of-a-validation-error-response) an.

## <a name="example-responses"></a>Beispielantworten

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
| version                                            | String            | Ja      | Die Version Ihrer API                                                                                                                                                                                                                                                                |
| action                                             | String            | Ja      | Der Wert muss `Continue` sein.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Nein       | Werte können im Verzeichnis gespeichert werden, wenn sie als **zu empfangender Anspruch** in der API-Connector-Konfiguration und als **Benutzerattribute** für einen Benutzerflow ausgewählt sind. Werte können im Token zurückgegeben werden, wenn sie als **Anwendungsanspruch** ausgewählt sind.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Nein       | Der Anspruch muss `_<extensions-app-id>_` nicht enthalten, dies ist *optional*. Zurückgegebene Werte können Werte überschreiben, die von einem Benutzer gesammelt wurden.  |

### <a name="example-of-a-blocking-response"></a>Beispiel für eine Blockierungsantwort

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| Parameter   | type   | Erforderlich | BESCHREIBUNG                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | String | Ja      | Die Version Ihrer API                                                    |
| action      | String | Ja      | Der Wert muss `ShowBlockPage` sein.                                              |
| userMessage | String | Ja      | Meldung, die für den Benutzer angezeigt wird.                                            |

**Anzeige einer Blockierungsantwort für den Endbenutzer**

:::image type="content" source="media/api-connectors-overview/blocking-page-response.png" alt-text="Eine Beispielabbildung der Endbenutzererfahrung, nachdem eine API eine Blockierungsantwort zurückgegeben hat":::

### <a name="example-of-a-validation-error-response"></a>Beispiel für eine Validierungsfehlerantwort

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
}
```

| Parameter   | type    | Erforderlich | BESCHREIBUNG                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | String  | Ja      | Die Version Ihrer API                                                    |
| action      | String  | Ja      | Der Wert muss `ValidationError` sein.                                           |
| status      | Integer / Zeichenkette | Ja      | Für eine Validierungsfehlerantwort muss der Wert `400` oder `"400"` sein.  |
| userMessage | String  | Ja      | Meldung, die für den Benutzer angezeigt wird.                                            |

> [!NOTE]
> Der HTTP-Statuscode muss „400“ lauten, und der Antworttext muss den Wert „status“ enthalten.

**Anzeige einer Validierungsfehlerantwort für den Endbenutzer**

:::image type="content" source="media/api-connectors-overview/validation-error-postal-code.png" alt-text="Eine Beispielabbildung der Endbenutzererfahrung, nachdem eine API eine Validierungsfehlerantwort zurückgegeben hat":::

## <a name="best-practices-and-how-to-troubleshoot"></a>Bewährte Methoden und Problembehandlungen

### <a name="using-serverless-cloud-functions"></a>Verwenden von serverlosen Cloudfunktionen

Serverlose Funktionen (z. B. [HTTP-Trigger in Azure Functions](../../azure-functions/functions-bindings-http-webhook-trigger.md)) bieten eine Möglichkeit zum Erstellen von API-Endpunkten, die mit dem API-Connector verwendet werden. Sie können die serverlosen Cloudfunktionen [beispielsweise](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts) verwenden, um Validierungslogik auszuführen und Registrierungen auf bestimmte E-Mail-Domänen zu beschränken. Mit den serverlosen Cloudfunktionen können auch andere Web-APIs, Datenspeicher und andere Clouddienste für komplexe Szenarien aufgerufen werden.

### <a name="best-practices"></a>Bewährte Methoden
Stellen Sie Folgendes sicher:
* Ihre API entspricht den API-Anforderungs- und -Antwortverträgen, wie dies weiter oben beschrieben ist. 
* Die **Endpunkt-URL** des API-Connectors verweist auf den richtigen API-Endpunkt.
* In Ihrer API wird explizit auf NULL-Werte der empfangenen notwendigen Ansprüche geprüft.
* Ihre API implementiert eine Authentifizierungsmethode, die unter [Schützen Ihres API-Connectors](self-service-sign-up-secure-api-connector.md) beschrieben ist.
* Ihre API antwortet so schnell wie möglich, um eine flüssige Darstellung für den Benutzer zu gewährleisten.
    * Azure AD wartet maximal *20 Sekunden* auf den Empfang einer Antwort. Wird keine empfangen, wird *ein weiterer Versuch (Wiederholung)* unternommen, die API aufzurufen.
    * Verwenden Sie in der Produktion einen Hostingplan, der dafür sorgt, dass die API aktiv bleibt, wenn Sie eine serverlose Funktion oder einen skalierbaren Webdienst verwenden. Für Azure Functions wird zumindest die Verwendung des [Premium-Plans](../../azure-functions/functions-scale.md) empfohlen.
* Stellen Sie die Hochverfügbarkeit Ihrer API sicher.
* Überwachen und optimieren Sie die Leistung von nachgelagerten APIs, Datenbanken oder anderen Abhängigkeiten Ihrer API.
* Ihre Endpunkte müssen die TLS- und Verschlüsselungsanforderungen von Azure AD erfüllen. Weitere Informationen finden Sie unter [Anforderungen an TLS und Verschlüsselungssammlungen](../../active-directory-b2c/https-cipher-tls-requirements.md). 
 
### <a name="use-logging"></a>Verwenden von Protokollierung

Üblicherweise ist es nützlich, die von Ihrem Web-API-Dienst aktivierten Protokollierungstools, etwa [Application Insights](../../azure-functions/functions-monitoring.md), zu verwenden, um Ihre API auf unerwartete Fehlercodes, Ausnahmen und schlechte Leistung zu überwachen.
* Überwachen Sie auf HTTP-Statuscodes, die weder HTTP 200 noch 400 sind.
* Der HTTP-Statuscode 401 oder 403 kennzeichnet in der Regel, dass ein Problem mit Ihrer Authentifizierung vorliegt. Überprüfen Sie die Authentifizierungsschicht Ihrer API und die entsprechende Konfiguration im API-Connector.
* Verwenden Sie in der Entwicklung ggf. höhere Protokolliergrade (z. B. „trace“ oder „debug“).
* Überwachen Sie Ihre API hinsichtlich langer Antwortzeiten. 

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie, wie Sie [der Self-Service-Registrierung einen benutzerdefinierten Genehmigungsworkflow hinzufügen](self-service-sign-up-add-approvals.md).
- Verwenden Sie unsere [Schnellstartbeispiele](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts), um direkt loszulegen.
