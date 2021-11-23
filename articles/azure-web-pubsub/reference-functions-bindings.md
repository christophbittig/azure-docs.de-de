---
title: 'Referenz: Azure Web PubSub-Trigger und -Bindungen für Azure Functions'
description: In dieser Referenz werden Azure Web PubSub-Trigger und -Bindungen für Azure Functions beschrieben.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 40904b087d58ee6a07ca7acecdd2b2927348799e
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493246"
---
#  <a name="azure-web-pubsub-trigger-and-bindings-for-azure-functions"></a>Azure Web PubSub-Trigger und -Bindungen für Azure Functions

In dieser Referenz wird die Verarbeitung von Web PubSub-Ereignissen in Azure Functions erläutert.

Web PubSub ist ein von Azure verwalteter Dienst, der Entwicklern die einfache Erstellung von Webanwendungen mit Echtzeitfeatures und einem Veröffentlichen-Abonnieren-Muster ermöglicht.

| Aktion | type |
|---------|---------|
| Ausführen einer Funktion, wenn Nachrichten vom Dienst eingehen | [Triggerbindung](#trigger-binding) |
| Binden der Anforderung an das Zielobjekt unter dem HTTP-Trigger für Aushandlungs- und Upstreamanforderungen | [Eingabebindung](#input-binding)
| Aufrufen von Dienstaktionen | [Ausgabebindung](#output-binding) |

[Quellcode](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/webpubsub/) |
[Paket](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub) |
[API-Referenzdokumentation](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Microsoft.Azure.WebJobs.Extensions.WebPubSub/api/Microsoft.Azure.WebJobs.Extensions.WebPubSub.netstandard2.0.cs) |
[Produktdokumentation](./index.yml) |
[Beispiele][samples_ref]

## <a name="add-to-your-functions-app"></a>Hinzufügen zu Ihrer Funktions-App

Für das Arbeiten mit Triggern und Bindungen müssen Sie auf das richtige Paket verweisen. Das NuGet-Paket wird für .NET-Klassenbibliotheken verwendet, während das Erweiterungspaket für alle anderen Anwendungstypen verwendet wird.

| Sprache                                        | Hinzufügen nach...                                   | Bemerkungen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installieren des[NuGet-Pakets], Vorabversion | |
| C#-Skript, JavaScript, Python, PowerShell       | [Explizites Installieren von Erweiterungen], [Verwenden von Erweiterungsbündeln] | Die [Erweiterung für Azure-Tools] wird zur Verwendung mit Visual Studio Code empfohlen. |
| C#-Skript (nur online im Azure-Portal)         | Hinzufügen einer Bindung                                   | Informationen zum Aktualisieren vorhandener Bindungserweiterungen, ohne Ihre Funktions-App erneut veröffentlichen zu müssen, finden Sie unter [Aktualisieren Ihrer Erweiterungen]. |

> [!NOTE]
> Installieren Sie die Clientbibliothek von [NuGet](https://www.nuget.org/) mit dem angegebenen Paket und der angegebenen Version.
> 
> ```bash
> func extensions install --package Microsoft.Azure.WebJobs.Extensions.WebPubSub --version 1.0.0
> ```

[NuGet-Paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub
[Verwenden von Erweiterungsbündeln]: ../azure-functions/functions-bindings-register.md#extension-bundles
[Explizites Installieren von Erweiterungen]: ../azure-functions/functions-bindings-register.md#explicitly-install-extensions 
[Azure-Tools-Erweiterung]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack
[Aktualisieren Ihrer Erweiterungen]: ../azure-functions/functions-bindings-register.md

## <a name="key-concepts"></a>Wichtige Begriffe

![Diagramm: Workflow des Azure Web PubSub-Diensts mit Nutzung von Funktions-Apps.](./media/reference-functions-bindings/functions-workflow.png)

(1)–(2): `WebPubSubConnection`-Eingabebindung mit HttpTrigger zum Generieren einer Clientverbindung.

(3)–(4): `WebPubSubTrigger`-Triggerbindung oder `WebPubSubContext`-Eingabebindung mit HttpTrigger zum Verarbeiten von Dienstanforderungen.

(5)–(6): `WebPubSub`-Ausgabebindung zum Anfordern einer Aktion eines Diensts.

## <a name="trigger-binding"></a>Triggerbindung

Verwenden Sie den Funktionstrigger, um Anforderungen vom Azure Web PubSub-Dienst zu verarbeiten. 

`WebPubSubTrigger` wird verwendet, wenn Sie dienstseitige Anforderungen verarbeiten müssen. Das Muster des Triggerendpunkts, das im Web PubSub-Dienst festgelegt werden sollte, würde wie folgt aussehen (Portal: Settings -> Event Handler -> URL Template (Einstellungen -> Ereignishandler -> URL-Vorlage)). Im Endpunktmuster ist der Abfrageteil `code=<API_KEY>` aus [Sicherheitsgründen](../azure-functions/security-concepts.md#system-key) **ERFORDERLICH**, wenn Sie eine Azure-Funktions-App verwenden. Den Schlüssel finden Sie im **Azure-Portal**. Suchen Sie nach dem Bereitstellen der Funktions-App in Azure nach Ihrer Funktions-App-Ressource, und navigieren Sie zu **Functions** -> **App-Schlüssel** -> **Systemschlüssel** -> **webpubsub_extension**. Wenn Sie mit lokalen Funktionen arbeiten, wird dieser Schlüssel jedoch nicht benötigt.

```
<Function_App_Url>/runtime/webhooks/webpubsub?code=<API_KEY>
```

:::image type="content" source="media/quickstart-serverless/func-keys.png" alt-text="Screenshot des Abrufens der Systemschlüssel der Funktion.":::

### <a name="example"></a>Beispiel


# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubTrigger")]
public static void Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)]
    UserEventRequest request,
    WebPubSubConnectionContext context,
    string data,
    WebPubSubDataType dataType)
{
    Console.WriteLine($"Request from: {context.userId}");
    Console.WriteLine($"Request message data: {data}");
    Console.WriteLine($"Request message dataType: {dataType}");
}
```

Die `WebPubSubTrigger`-Bindung unterstützt in Synchronisierungsszenarien auch Rückgabewerte, z. B. bei `Connect`-Ereignissen vom System oder von Benutzer*innen, bei denen der Server die Clientanforderung überprüfen und ablehnen oder Nachrichten direkt an den Aufrufer senden kann. Das `Connect`-Ereignis berücksichtigt `ConnectEventResponse` und `EventErrorResponse`, das Benutzerereignis `UserEventResponse` und `EventErrorResponse`. Die übrigen Typen, die nicht zum aktuellen Szenario passen, werden ignoriert. Wenn `EventErrorResponse` zurückgegeben wird, wird die Clientverbindung vom Dienst getrennt.

```cs
[FunctionName("WebPubSubTriggerReturnValue")]
public static MessageResponse Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)]
    UserEventRequest request,
    ConnectionContext context,
    string data,
    WebPubSubDataType dataType)
{
    return new UserEventResponse
    {
        Data = BinaryData.FromString("ack"),
        DataType = WebPubSubDataType.Text
    };
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Definieren Sie die Triggerbindung in `function.json`.

```json
{
  "disabled": false,
  "bindings": [
    {
      "type": "webPubSubTrigger",
      "direction": "in",
      "name": "data",
      "hub": "<hub>",
      "eventName": "message",
      "eventType": "user"
    }
  ]
}
```

Definieren Sie die Funktion in `index.js`.

```js
module.exports = function (context, data) {
  console.log('Request from: ', context.bindingData.request.connectionContext.userId);
  console.log('Request message data: ', data);
  console.log('Request message dataType: ', context.bindingData.request.dataType);
}
```

Die `WebPubSubTrigger`-Bindung unterstützt in Synchronisierungsszenarien auch Rückgabewerte, z. B. bei `Connect`-Ereignissen vom System oder von Benutzer*innen, bei denen der Server die Clientanforderung überprüfen und ablehnen oder Nachrichten direkt an den Anforderungsclient senden kann. In der schwach typisierten Sprache JavaScript wird die Bindung in Bezug auf die Objektschlüssel deserialisiert. Außerdem genießt `EventErrorResponse` die höchste Priorität im Vergleich zu Restobjekten, sodass wenn `code` zurückgegeben wird, das Ergebnis der Analyse `EventErrorResponse` ist und die Clientverbindung getrennt wird.

```js
module.exports = async function (context) {
  return { 
    "data": "ack",
    "dataType" : "text"
  };
}
```

---


### <a name="attributes-and-annotations"></a>Attribute und Anmerkungen

Verwenden Sie in [C#-Klassenbibliotheken](../azure-functions/functions-dotnet-class-library.md) das Attribut `WebPubSubTrigger`.

Dies ist ein `WebPubSubTrigger`-Attribut in einer Methodensignatur:

```csharp
[FunctionName("WebPubSubTrigger")]
public static void Run([WebPubSubTrigger("<hub>", "<event-Name>", <WebPubSubEventType>)] 
WebPubSubConnectionContext context, ILogger log)
{
    ...
}
```

Ein vollständiges Beispiel finden Sie unter „C#-Beispiel“.

### <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* festlegen.

| Eigenschaft von „function.json“ | Attributeigenschaft | BESCHREIBUNG |
|---------|---------|---------|
| **type** | – |Erforderlich – muss auf `webPubSubTrigger` festgelegt sein. |
| **direction** | – | Erforderlich – muss auf `in` festgelegt sein. |
| **name** | – | Erforderlich – der Variablenname, der im Funktionscode für den Parameter verwendet wird, der die Ereignisdaten empfängt. |
| **hub** | Hub | Dieser Wert ist erforderlich und muss auf den Namen des Web PubSub-Hubs festgelegt werden, damit die Funktion ausgelöst werden kann. Das Festlegen des Werts als einer mit höherer Priorität wird im Attribut unterstützt. Alternativ kann dies in den App-Einstellungen als globaler Wert festgelegt werden. |
| **eventType** | EventType | Dieser Wert ist erforderlich und muss auf den Ereignistyp Nachrichten festgelegt werden, damit die Funktion ausgelöst wird. Der Wert muss `user` oder `system` sein. |
| **eventName** | EventName | Dieser Wert ist erforderlich und muss auf das Nachrichtenereignis festgelegt werden, damit die Funktion ausgelöst wird. </br> Für den Ereignistyp `system` muss der Ereignisname in `connect`, `connected` oder `disconnected` festgelegt werden. </br> Bei benutzerdefinierten Unterprotokollen ist der Ereignisname `message`. </br> Bei dem vom System unterstützten Unterprotokoll `json.webpubsub.azure.v1.` ist der Ereignisname benutzerdefiniert. |
| **connection** | Verbindung | Optional: Name von App-Einstellungen oder einer Einstellungssammlung, der den Upstream-Azure Web PubSub-Dienst angibt. Der Wert wird für die Signaturüberprüfung verwendet. Außerdem wird der Wert standardmäßig automatisch mit den App-Einstellungen von WebPubSubConnectionString aufgelöst. `null` bedeutet, dass die Überprüfung nicht erforderlich und immer erfolgreich ist. |

### <a name="usages"></a>Verwendungen

In C# ist `WebPubSubEventRequest` ein vom Typ erkannter Bindungsparameter, und Restparameter sind an den Parameternamen gebunden. Sehen Sie sich die folgende Tabelle mit den verfügbaren Parametern und Typen an.

Bei schwach typisierten Programmiersprachen wie JavaScript wird `name` in `function.json` verwendet, um das Triggerobjekt in Bezug auf die folgende Zuordnungstabelle zu binden. Hierbei wird `dataType` in `function.json` berücksichtigt, damit die Nachricht richtig konvertiert wird, wenn `name` auf `data` als Bindungsobjekt für die Triggereingabe festgelegt ist. Alle Parameter können aus `context.bindingData.<BindingName>` gelesen werden und werden in `JObject` konvertiert. 

| Bindungsname | Bindungstyp | BESCHREIBUNG | Eigenschaften |
|---------|---------|---------|---------|
|request|`WebPubSubEventRequest`|Beschreibt die Upstreamanforderung|Die Eigenschaft unterscheidet sich durch verschiedene Ereignistypen, einschließlich der abgeleiteten Klassen `ConnectEventRequest`, `ConnectedEventRequest`, `UserEventRequest` und `DisconnectedEventRequest`. |
|connectionContext|`WebPubSubConnectionContext`|Allgemeine Anforderungsinformationen| EventType, EventName, Hub, ConnectionId, UserId, Headers, Origin, Signature, States |
|data|`BinaryData`,`string`,`Stream`,`byte[]`| Anfordern von Nachrichtendaten vom Client im `message`-Benutzerereignis | -|
|dataType|`WebPubSubDataType`| Der Datentyp für Anforderungsnachrichten, unterstützt `binary`, `text` und `json` | -|
|claims|`IDictionary<string, string[]>`|Benutzeransprüche in der `connect`-Systemanforderung | -|
|Abfrage|`IDictionary<string, string[]>`|Benutzerabfrage in der `connect`-Systemanforderung | -|
|subprotocols|`IList<string>`|Verfügbare Unterprotokolle in einer `connect`-Systemanforderung | -|
|clientCertificates|`IList<ClientCertificate>`|Eine Liste der Zertifikatfingerabdrücke von Clients in der `connect`-Systemanforderung|-|
|reason|`string`|Grund in der `disconnected`-Systemanforderung|-|

### <a name="return-response"></a>Antwort zurückgeben

`WebPubSubTrigger` berücksichtigt die von den Kund*innen zurückgegebene Antwort für synchrone Ereignisse für `connect` und Benutzerereignisse. Nur eine passende Antwort wird an den Dienst zurückgesendet, ansonsten wird die Antwort ignoriert. Darüber hinaus unterstützt das Rückgabeobjekt `WebPubSubTrigger` Benutzer*innen für `SetState()` und `ClearStates()`, um die Metadaten für die Verbindung zu verwalten. Und die Erweiterung führt die Ergebnisse des Rückgabewerts mit den ursprünglichen Ergebnissen aus der Anforderung `WebPubSubConnectionContext.States` zusammen. Der Wert im vorhandenen Schlüssel wird überschrieben, und der Wert im neuen Schlüssel wird hinzugefügt.

| Rückgabetyp | BESCHREIBUNG | Eigenschaften |
|---------|---------|---------|
|`ConnectEventResponse`| Antwort auf ein `connect`-Ereignis | Groups, Roles, UserId, Subprotocol |
|`UserEventResponse`| Antwort auf ein Benutzerereignis | DataType, Data |
|`EventErrorResponse`| Fehlerantwort für das Synchronisierungsereignis | Code, ErrorMessage |
|`*WebPubSubEventResponse`| Basisantworttyp der unterstützten Typen, der bei unsicheren Rückgaben verwendet wird | - |

## <a name="input-binding"></a>Eingabebindung

Unsere Erweiterung bietet zwei Eingabebindungen für unterschiedliche Anforderungen.

- `WebPubSubConnection`

  Damit ein Client eine Verbindung mit dem Azure Web PubSub-Dienst herstellen kann, muss er über die Endpunkt-URL des Diensts und ein gültiges Zugriffstoken verfügen. Die `WebPubSubConnection`-Eingabebindung erstellt die erforderlichen Informationen, sodass der Client diese Tokengenerierung nicht selbst übernehmen muss. Da das Token nur für einen bestimmten Zeitraum gültig ist und nur für die Authentifizierung eines bestimmten Benutzers bzw. einer bestimmten Benutzerin für eine Verbindung verwendet werden kann, sollten Sie das Token nicht zwischenspeichern oder für mehrere Clients verwenden. Ein HTTP-Trigger, der diese Eingabebindung nutzt, kann für Clients verwendet werden, um die Verbindungsinformationen abzurufen.

- `WebPubSubContext`

  Wenn Sie Static Web Apps verwenden, ist `HttpTrigger` der einzige unterstützte Trigger. Im Web PubSub-Szenario wird die `WebPubSubContext`-Eingabebindung zur Verfügung gestellt, die Benutzer*innen dabei hilft, dienstseitige Upstream-HTTP-Anforderungen unter Web PubSub-Protokollen zu deserialisieren. So können Kund*innen für eine einfache Verarbeitung in Funktionen ähnliche Ergebnisse wie mit `WebPubSubTrigger` erzielen. Sehen Sie sich hierzu die [Beispiele](#example---webpubsubcontext) unten an.
  Bei Verwendung mit `HttpTrigger` müssen Kund*innen die an HttpTrigger weitergegebene URL im Ereignishandler entsprechend konfigurieren.

### <a name="example---webpubsubconnection"></a>Beispiel – `WebPubSubConnection`

Das folgende Beispiel zeigt eine C#-Funktion, die mithilfe der Eingabebindung Web PubSub-Verbindungsinformationen abruft und über HTTP zurückgibt. Im folgenden Beispiel wird die `UserId` über den Abfrageteil der Clientanforderung übergeben, z. B. mit `?userid={User-A}`.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubConnectionInputBinding")]
public static WebPubSubConnection Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubConnection(Hub = "<hub>", UserId = "{query.userid}")] WebPubSubConnection connection)
{
    Console.WriteLine("login");
    return connection;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Definieren Sie Eingabebindungen in `function.json`.

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "webPubSubConnection",
      "name": "connection",
      "userId": "{query.userid}",
      "hub": "<hub>",
      "direction": "in"
    }
  ]
}
```

Definieren Sie die Funktion in `index.js`.

```js
module.exports = function (context, req, connection) {
  context.res = { body: connection };
  context.done();
};
```

---

### <a name="authenticated-tokens"></a>Authentifizierte **Token**

Wenn die Funktion von einem authentifizierten Client ausgelöst wird, können Sie dem erzeugten Token einen Benutzer-ID-Anspruch hinzufügen. Mithilfe der App Service-Authentifizierung können Sie einer Funktions-App problemlos eine Authentifizierung hinzufügen.

App Service-Authentifizierung legt HTTP-Header mit den Namen `x-ms-client-principal-id` und `x-ms-client-principal-name` fest, die die Clientprinzipal-ID bzw. den Namen des authentifizierten Benutzers enthalten.

Sie können die Eigenschaft UserId der Bindung mithilfe eines Bindungsausdrucks auf den Wert eines der beiden Header festlegen: `{headers.x-ms-client-principal-id}` oder `{headers.x-ms-client-principal-name}`.

```cs
[FunctionName("WebPubSubConnectionInputBinding")]
public static WebPubSubConnection Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubConnection(Hub = "<hub>", UserId = "{headers.x-ms-client-principal-name}")] WebPubSubConnection connection)
{
    Console.WriteLine("login");
    return connection;
}
```

### <a name="example---webpubsubcontext"></a>Beispiel – `WebPubSubContext`

Das folgende Beispiel zeigt eine C#-Funktion, die mithilfe der Eingabebindung im Ereignistyp `connect` Informationen zur Web PubSub-Upstreamanforderung abruft und über HTTP zurückgibt.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubContextInputBinding")]
public static object Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubContext] WebPubSubContext wpsContext)
{
    if (wpsContext.IsPreflight || !wpsContext.HasError)
    {
        return wpsReq.Response;
    }
    var request = wpsReq.Request as ConnectEventRequest;
    var response = new ConnectEventResponse
    {
        UserId = wpsContext.Request.ConnectionContext.UserId
    };
    return response;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Definieren Sie Eingabebindungen in `function.json`.

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "webPubSubContext",
      "name": "wpsContext",
      "direction": "in"
    }
  ]
}
```

Definieren Sie die Funktion in `index.js`.

```js
module.exports = async function (context, req, wpsContext) {
  if (!wpsContext.hasError || wpsContext.isPreflight)
  {
    console.log(`invalid request: ${wpsContext.response.message}.`);
    return wpsReq.response;
  }
  console.log(`user: ${wpsContext.connectionContext.userId} is connecting.`);
  return { body: {"userId": wpsContext.connectionContext.userId} };
};
```

---

### <a name="configuration"></a>Konfiguration

#### <a name="webpubsubconnection"></a>WebPubSubConnection

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei function.json und im Attribut `WebPubSubConnection` festlegen:

| Eigenschaft von „function.json“ | Attributeigenschaft | BESCHREIBUNG |
|---------|---------|---------|
| **type** | – | Muss auf `webPubSubConnection` festgelegt werden. |
| **direction** | – | Muss auf `in` festgelegt werden. |
| **name** | – | Variablenname, der im Funktionscode für das Bindungsobjekt für die Eingabeverbindung verwendet wird |
| **hub** | Hub | Erforderlich: Dieser Wert muss auf den Namen des Web PubSub-Hubs festgelegt werden, damit die Funktion ausgelöst werden kann. Das Festlegen des Werts als einer mit höherer Priorität wird im Attribut unterstützt. Alternativ kann dies in den App-Einstellungen als globaler Wert festgelegt werden. |
| **userId** | UserId | Der optionale Wert des Benutzer-ID-Anspruchs, der im Zugriffsschlüsseltoken festgelegt wird |
| **connection** | Verbindung | Erforderlich: Name der App-Einstellung, die die Verbindungszeichenfolge des Web PubSub-Diensts enthält (standardmäßig WebPubSubConnectionString) |

#### <a name="webpubsubcontext"></a>WebPubSubContext

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei „function.json“ und im Attribut `WebPubSubContext` festlegen:

| Eigenschaft von „function.json“ | Attributeigenschaft | BESCHREIBUNG |
|---------|---------|---------|
| **type** | – | Muss auf `webPubSubContext` festgelegt sein. |
| **direction** | – | Muss auf `in` festgelegt sein. |
| **name** | – | Variablenname, der im Funktionscode für die Web PubSub-Eingabeanforderung verwendet wird |
| **connection** | Verbindung | Optional: Name von App-Einstellungen oder einer Einstellungssammlung, der den Upstream-Azure Web PubSub-Dienst angibt. Der Wert wird für den [Missbrauchsschutz](https://github.com/cloudevents/spec/blob/v1.0.1/http-webhook.md#4-abuse-protection) und die Signaturüberprüfung verwendet. Der Wert wird automatisch mit WebPubSubConnectionString aufgelöst. `null` bedeutet, dass die Überprüfung nicht erforderlich und immer erfolgreich ist. |

### <a name="usage"></a>Verwendung

#### <a name="webpubsubconnection"></a>WebPubSubConnection

# <a name="c"></a>[C#](#tab/csharp)

`WebPubSubConnection` verfügt über die unten aufgeführten Eigenschaften.

| Bindungsname | Bindungstyp | BESCHREIBUNG |
|---------|---------|---------|
| BaseUri | Uri | Verbindungs-URI für den Web PubSub-Client |
| Uri | Uri | Absoluter URI der Web PubSub-Verbindung, enthält das basierend auf der Anforderung generierte `AccessToken` |
| AccessToken | Zeichenfolge | Basierend auf der UserId der Anforderung und Dienstinformationen generiertes `AccessToken` |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`WebPubSubConnection` verfügt über die unten aufgeführten Eigenschaften.

| Bindungsname | Beschreibung |
|---------|---------|
| baseUrl | Verbindungs-URI für den Web PubSub-Client |
| url | Absoluter URI der Web PubSub-Verbindung, enthält das basierend auf der Anforderung generierte `AccessToken` |
| accessToken | Basierend auf der UserId der Anforderung und Dienstinformationen generiertes `AccessToken` |

---

#### <a name="webpubsubcontext"></a>WebPubSubContext

`WebPubSubContext` verfügt über die unten aufgeführten Eigenschaften.

| Bindungsname | Bindungstyp | BESCHREIBUNG | Eigenschaften |
|---------|---------|---------|---------|
| request | `WebPubSubEventRequest` | Anforderung vom Client. Weitere Informationen finden Sie in der nachstehenden Tabelle. | `WebPubSubConnectionContext` aus dem Anforderungsheader und weitere Eigenschaften, die aus dem Anforderungstext deserialisiert wurden, zur Beschreibung der Anforderung, z. B. `Reason` für `DisconnectedEventRequest`. |
| Antwort | `HttpResponseMessage` | Antwort von Erweiterungsbuilds, die hauptsächlich für `AbuseProtection` und Fehlerfälle verwendet wird. | - |
| errorMessage | Zeichenfolge | Beschreibt Fehlerdetails beim Verarbeiten der Upstreamanforderung. | - |
| hasError | bool | Dieses Flag gibt an, ob es sich um eine gültige Web PubSub-Upstreamanforderung handelt. | - |
| isPreflight | bool | Dieses Flag gibt an, ob es sich um eine Preflightanforderung von `AbuseProtection` handelt. | - |

Für `WebPubSubEventRequest` wird sie in verschiedene Klassen deserialisiert, die unterschiedliche Informationen zum Anforderungsszenario bereitstellen. Für `PreflightRequest` oder in ungültigen Fällen können die Benutzer*innen dazu die Flags `IsPreflight` und `HasError` überprüfen. Es wird empfohlen, die Systembuildantwort `WebPubSubContext.Response` direkt zurückzugegeben. Alternativ können Kund*innen Fehler bei Bedarf protokollieren. In verschiedenen Szenarios können Kund*innen die Anforderungseigenschaften wie folgt lesen.

| Abgeleitete Klasse | BESCHREIBUNG | Eigenschaften |
| -- | -- | -- |
| `PreflightRequest` | Wird in `AbuseProtection` verwendet, wenn `IsPreflight` den Wert **true** hat | - |
| `ConnectEventRequest` | Wird im Systemereignistyp `Connect` verwendet | Claims, Query, Subprotocols, ClientCertificates |
| `ConnectedEventRequest` | Wird im Systemereignistyp `Connected` verwendet | - |
| `UserEventRequest` | Wird im Benutzerereignistyp verwendet | Data, DataType |
| `DisconnectedEventRequest` | Wird im Systemereignistyp `Disconnected` verwendet | `Reason` |

> [!NOTE]
> Obwohl es sich bei `WebPubSubContext` um eine Eingabebindung handelt, die eine ähnliche Anforderungsdeserialisierung im `HttpTrigger` wie `WebPubSubTrigger` bietet, gibt es Einschränkungen, z. B. wird der Verbindungsstatus nach der Zusammenführung unterstützt. Die Rückgabeantwort wird weiterhin vom Dienst berücksichtigt, aber Benutzer*innen müssen die Antwort selbst erstellen. Wenn Benutzer*innen die Ereignisantwort festlegen müssen, sollten Sie eine `HttpResponseMessage` mit `ConnectEventResponse` oder Nachrichten für das Benutzerereignis als **Antworttext** zurückgeben und den Verbindungszustand mit dem Schlüssel `ce-connectionstate` in den **Antwortheader** einfügen.

## <a name="output-binding"></a>Ausgabebindung

Verwenden Sie die Web PubSub-Ausgabebindung, um eine Aktion des Azure Web PubSub-Diensts aufzurufen. Sie können eine Nachricht senden an:

* Alle verbundenen Clients
* Verbundene Clients, die für einen bestimmten Benutzer authentifiziert sind
* Verbundene Clients, die in einer bestimmten Gruppe miteinander verknüpft sind
* Eine bestimmte Clientverbindung

Mit der Ausgabebindung können Sie außerdem Clients und Gruppen verwalten und Berechtigungen für eine bestimmte connectionId für die Gruppe erteilen oder widerrufen.

* Hinzufügen einer Verbindung zu einer Gruppe
* Hinzufügen von Benutzern zur Gruppe
* Entfernen einer Verbindung aus einer Gruppe
* Entfernen eines Benutzers aus einer Gruppe
* Entfernen eines Benutzers aus allen Gruppen
* Schließen aller Clientverbindungen
* Schließen einer bestimmten Clientverbindung
* Schließen von Verbindungen in einer Gruppe
* Zuweisen der Berechtigung für eine Verbindung
* Widerrufen der Berechtigung für eine Verbindung

Informationen zu Setup- und Konfigurationsdetails finden Sie in der Übersicht.

### <a name="example"></a>Beispiel

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubOutputBinding")]
public static async Task RunAsync(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSub(Hub = "<hub>")] IAsyncCollector<WebPubSubAction> actions)
{
    await actions.AddAsync(WebPubSubAction.CreateSendToAllAction("Hello Web PubSub!", WebPubSubDataType.Text));
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Definieren Sie Bindungen in `functions.json`.

```json
{
  "disabled": false,
  "bindings": [
    {
      "type": "webPubSub",
      "name": "actions",
      "hub": "<hub>",
      "direction": "out"
    }
  ]
}
```

Definieren Sie die Funktion in `index.js`.

```js
module.exports = async function (context) {
  context.bindings.actions = {
    "actionName": "sendToAll",
    "data": "hello",
    "dataType": "text"
  };
  context.done();
}
```

---

### <a name="webpubsubaction"></a>WebPubSubAction 

`WebPubSubAction` ist der abstrakte Basistyp von Ausgabebindungen. Die abgeleiteten Typen stellen die Aktion dar, die der Server vom Dienst aufrufen möchte. 

# <a name="c"></a>[C#](#tab/csharp)

In der Programmiersprache C# geben Sie einige statische Methoden unter `WebPubSubAction` an, um die verfügbaren Aktionen zu ermitteln. Sie können Benutzer*innen beispielsweise die `SendToAllAction` erstellen, indem sie `WebPubSubAction.CreateSendToAllAction()` aufrufen.

| Abgeleitete Klasse | Eigenschaften |
| -- | -- |
| `SendToAllAction`|Data, DataType, Excluded |
| `SendToGroupAction`|Group, Data, DataType, Excluded |
| `SendToUserAction`|UserId, Data, DataType |
| `SendToConnectionAction`|ConnectionId, Data, DataType |
| `AddUserToGroupAction`|UserId, Group |
| `RemoveUserFromGroupAction`|UserId, Group |
| `RemoveUserFromAllGroupsAction`|UserId |
| `AddConnectionToGroupAction`|ConnectionId, Group |
| `RemoveConnectionFromGroupAction`|ConnectionId, Group |
| `CloseAllConnectionsAction`|Excluded, Reason |
| `CloseClientConnectionAction`|ConnectionId, Reason |
| `CloseGroupConnectionsAction`|Group, Excluded, Reason |
| `GrantPermissionAction`|ConnectionId, Permission, TargetName |
| `RevokePermissionAction`|ConnectionId, Permission, TargetName |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In schwach typisierten Programmiersprachen wie `javascript` ist **`actionName`** der Schlüsselparameter zum Auflösen des Typs. Die verfügbaren Aktionen sind unten aufgeführt.

| ActionName | Eigenschaften |
| -- | -- |
| `SendToAll`|Data, DataType, Excluded |
| `SendToGroup`|Group, Data, DataType, Excluded |
| `SendToUser`|UserId, Data, DataType |
| `SendToConnection`|ConnectionId, Data, DataType |
| `AddUserToGroup`|UserId, Group |
| `RemoveUserFromGroup`|UserId, Group |
| `RemoveUserFromAllGroups`|UserId |
| `AddConnectionToGroup`|ConnectionId, Group |
| `RemoveConnectionFromGroup`|ConnectionId, Group |
| `CloseAllConnections`|Excluded, Reason |
| `CloseClientConnection`|ConnectionId, Reason |
| `CloseGroupConnections`|Group, Excluded, Reason |
| `GrantPermission`|ConnectionId, Permission, TargetName |
| `RevokePermission`|ConnectionId, Permission, TargetName |

> [!IMPORTANT]
> Die Data-Eigenschaft der Nachricht in den Aktionen zum Senden von Nachrichten muss `string` sein, wenn der Datentyp auf `json` oder `text` festgelegt ist, um Mehrdeutigkeiten bei der Datenkonvertierung zu vermeiden. Verwenden Sie `JSON.stringify()`, um das benötigte JSON-Objekt zu konvertieren. Dies wird an jeder Stelle mithilfe der Nachrichteneigenschaft angewandt, z. B. durch `UserEventResponse.Data` bei Verwendung von `WebPubSubTrigger`. 
> 
> Wenn der Datentyp auf `binary` festgelegt wurde, ist es zulässig, einen nativ unterstützten `dataType` als `binary` zu binden, wie in `function.json` konfiguriert. Weitere Informationen finden Sie unter [Trigger- und Bindungsdefinitionen](/azure/azure-functions/functions-triggers-bindings?tabs=csharp#trigger-and-binding-definitions).

---

### <a name="configuration"></a>Konfiguration

#### <a name="webpubsub"></a>WebPubSub

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei function.json und im Attribut `WebPubSub` festlegen:

| Eigenschaft von „function.json“ | Attributeigenschaft | BESCHREIBUNG |
|---------|---------|---------|
| **type** | – | Muss auf `webPubSub` festgelegt werden. |
| **direction** | – | Muss auf `out` festgelegt werden. |
| **name** | – | Variablenname, der im Funktionscode für das Ausgabebindungsobjekt verwendet wird |
| **hub** | Hub | Dieser Wert muss auf den Namen des Web PubSub-Hubs festgelegt werden, damit die Funktion ausgelöst werden kann. Das Festlegen des Werts als einer mit höherer Priorität wird im Attribut unterstützt. Alternativ kann dies in den App-Einstellungen als globaler Wert festgelegt werden. |
| **connection** | Verbindung | Der Name der App-Einstellung, die die Verbindungszeichenfolge des Web PubSub-Diensts enthält (standardmäßig WebPubSubConnectionString) |

## <a name="troubleshooting"></a>Problembehandlung

### <a name="setting-up-console-logging"></a>Einrichten der Konsolenprotokollierung
Sie können auch einfach die [Konsolenprotokollierung aktivieren](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#logging), wenn Sie ausführliche Informationen zu den von Ihnen an den Dienst gesendeten Anforderungen erhalten möchten.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/functions

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]