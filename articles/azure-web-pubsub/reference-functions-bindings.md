---
title: 'Referenz: Azure Web PubSub-Trigger und -Bindungen für Azure Functions'
description: In dieser Referenz werden Azure Web PubSub-Trigger und -Bindungen für Azure Functions beschrieben.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 43fcc70bf88b7e037167a936ebd655b8877b394d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820370"
---
#  <a name="azure-web-pubsub-trigger-and-bindings-for-azure-functions"></a>Azure Web PubSub-Trigger und -Bindungen für Azure Functions

In dieser Referenz wird die Verarbeitung von Web PubSub-Ereignissen in Azure Functions erläutert.

Web PubSub ist ein von Azure verwalteter Dienst, der Entwicklern die einfache Erstellung von Webanwendungen mit Echtzeitfeatures und einem Veröffentlichen-Abonnieren-Muster ermöglicht.

| Aktion | type |
|---------|---------|
| Ausführen einer Funktion, wenn Nachrichten vom Dienst eingehen | [Triggerbindung](#trigger-binding) |
| Zurückgeben der Dienstendpunkt-URL und des Zugriffstokens | [Eingabebindung](#input-binding)
| Senden von Web PubSub-Nachrichten |[Ausgabebindung](#output-binding) |

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
| C#-Skript, JavaScript, Python, PowerShell       | [Explizites Installieren von Erweiterungen]                    | Die [Erweiterung für Azure-Tools] wird zur Verwendung mit Visual Studio Code empfohlen. |
| C#-Skript (nur online im Azure-Portal)         | Hinzufügen einer Bindung                                   | Informationen zum Aktualisieren vorhandener Bindungserweiterungen, ohne Ihre Funktions-App erneut veröffentlichen zu müssen, finden Sie unter [Aktualisieren Ihrer Erweiterungen]. |

Installieren Sie die Clientbibliothek von [NuGet](https://www.nuget.org/) mit dem angegebenen Paket und der angegebenen Version.

```bash
func extensions install --package Microsoft.Azure.WebJobs.Extensions.WebPubSub --version 1.0.0-beta.3
```

[NuGet-Paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub
[Explizites Installieren von Erweiterungen]: ../azure-functions/functions-bindings-register.md#explicitly-install-extensions 
[Azure-Tools-Erweiterung]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack
[Aktualisieren Ihrer Erweiterungen]: ../azure-functions/functions-bindings-register.md

## <a name="key-concepts"></a>Wichtige Begriffe

![Diagramm: Workflow des Azure Web PubSub-Diensts mit Nutzung von Funktions-Apps.](./media/reference-functions-bindings/functions-workflow.png)

(1)–(2): `WebPubSubConnection`-Eingabebindung mit HttpTrigger zum Generieren einer Clientverbindung.

(3)–(4): `WebPubSubTrigger`-Triggerbindung oder `WebPubSubRequest`-Eingabebindung mit HttpTrigger zum Verarbeiten von Dienstanforderungen.

(5)–(6): `WebPubSub`-Ausgabebindung zum Anfordern einer Aktion eines Diensts.

## <a name="trigger-binding"></a>Triggerbindung

Verwenden Sie den Funktionstrigger, um Anforderungen vom Azure Web PubSub-Dienst zu verarbeiten. 

`WebPubSubTrigger` wird verwendet, wenn Sie dienstseitige Anforderungen verarbeiten müssen. Das Muster des Triggerendpunkts, das im Web PubSub-Dienst festgelegt werden sollte, würde wie folgt aussehen (Portal: Settings -> Event Handler -> URL Template (Einstellungen -> Ereignishandler -> URL-Vorlage)). Im Endpunktmuster ist der Abfrageteil `code=<API_KEY>` aus [Sicherheitsgründen](../azure-functions/security-concepts.md#system-key) **ERFORDERLICH**, wenn Sie eine Azure-Funktions-App verwenden. Den Schlüssel finden Sie im **Azure-Portal**. Suchen Sie nach dem Bereitstellen der Funktions-App in Azure nach Ihrer Funktions-App-Ressource, und navigieren Sie zu **Functions** -> **App-Schlüssel** -> **Systemschlüssel** -> **webpubsub_extension**. Wenn Sie mit lokalen Funktionen arbeiten, wird dieser Schlüssel jedoch nicht benötigt.

```
<Function_App_Url>/runtime/webhooks/webpubsub?code=<API_KEY>
```

### <a name="example"></a>Beispiel


# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubTrigger")]
public static void Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)] 
    ConnectionContext context,
    string message,
    MessageDataType dataType)
{
    Console.WriteLine($"Request from: {context.userId}");
    Console.WriteLine($"Request message: {message}");
    Console.WriteLine($"Request message DataType: {dataType}");
}
```

Die `WebPubSubTrigger`-Bindung unterstützt in einigen Szenarios auch Rückgabewerte, z. B. bei `Connect`- und `Message`-Ereignissen, bei denen der Server die Clientanforderung überprüfen und ablehnen oder Nachrichten direkt an den Anforderungsclient senden kann. Das `Connect`-Ereignis berücksichtigt `ConnectResponse` und `ErrorResponse`, das `Message`-Ereignis `MessageResponse` und `ErrorResponse`. Die Resttypen, die nicht zum aktuellen Szenario passen, werden ignoriert. Wenn `ErrorResponse` zurückgegeben wird, wird die Clientverbindung vom Dienst getrennt.

```cs
[FunctionName("WebPubSubTriggerReturnValue")]
public static MessageResponse Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)] 
    ConnectionContext context,
    string message,
    MessageDataType dataType)
{
    return new MessageResponse
    {
        Message = BinaryData.FromString("ack"),
        DataType = MessageDataType.Text
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
      "name": "message",
      "hub": "<hub>",
      "eventName": "message",
      "eventType": "user"
    }
  ]
}
```

Definieren Sie die Funktion in `index.js`.

```js
module.exports = function (context, message) {
  console.log('Request from: ', context.userId);
  console.log('Request message: ', message);
  console.log('Request message dataType: ', context.bindingData.dataType);
}
```

Die `WebPubSubTrigger`-Bindung unterstützt in einigen Szenarios auch Rückgabewerte, z. B. bei `Connect`- und `Message`-Ereignissen, bei denen der Server die Clientanforderung überprüfen und ablehnen oder Nachrichten direkt an den Anforderungsclient senden kann. In der typenlosen Sprache JavaScript wird die Bindung in Bezug auf die Objektschlüssel deserialisiert. Außerdem genießt `ErrorResponse` die höchste Priorität im Vergleich zu Restobjekten, sodass wenn `code` zurückgegeben wird, das Ergebnis der Analyse `ErrorResponse` ist und die Clientverbindung getrennt wird.

```js
module.exports = async function (context) {
  return { 
    "message": "ack",
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
public static void Run([WebPubSubTrigger("<hub>", "<eventName>", <eventType>)] 
ConnectionContext context, ILogger log)
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
| **eventName** | EventName | Dieser Wert ist erforderlich und muss auf das Nachrichtenereignis festgelegt werden, damit die Funktion ausgelöst wird. </br> Für den Ereignistyp `system` muss der Ereignisname in `connect`, `connected` oder `disconnect` festgelegt werden. </br> Bei dem vom System unterstützten Unterprotokoll `json.webpubsub.azure.v1.` ist der Ereignisname benutzerdefiniert. </br> Bei benutzerdefinierten Unterprotokollen ist der Ereignisname `message`. |

### <a name="usages"></a>Verwendungen

In C# ist `ConnectionContext` ein vom Typ erkannter Bindungsparameter, und Restparameter sind an den Parameternamen gebunden. Sehen Sie sich die folgende Tabelle mit den verfügbaren Parametern und Typen an.

Bei typenlosen Programmiersprachen wie JavaScript wird `name` in `function.json` verwendet, um das Triggerobjekt in Bezug auf die folgende Zuordnungstabelle zu binden. Hierbei wird `dataType` in `function.json` berücksichtigt, damit die Nachricht richtig konvertiert wird, wenn `name` auf `message` als Bindungsobjekt für die Triggereingabe festgelegt ist. Alle Parameter können aus `context.bindingData.<BindingName>` gelesen werden und werden in `JObject` konvertiert. 

| Bindungsname | Bindungstyp | BESCHREIBUNG | Eigenschaften |
|---------|---------|---------|---------|
|connectionContext|`ConnectionContext`|Allgemeine Anforderungsinformationen| EventType, EventName, Hub, ConnectionId, UserId, Headers, Signature |
|message|`BinaryData`,`string`,`Stream`,`byte[]`| Anforderungsnachricht vom Client | -|
|dataType|`MessageDataType`| Der Datentyp für Anforderungsnachrichten, unterstützt `binary`, `text` und `json` | -|
|claims|`IDictionary<string, string[]>`|Benutzeransprüche in einer `connect`-Anforderung | -|
|Abfrage|`IDictionary<string, string[]>`|Benutzerabfragen in einer `connect`-Anforderung | -|
|subprotocols|`string[]`|Verfügbare Unterprotokolle in einer `connect`-Anforderung | -|
|clientCertificates|`ClientCertificate[]`|Eine Liste der Zertifikatfingerabdrücke von Clients in der `connect`-Anforderung|-|
|reason|`string`|Ursache in der Anforderung zum Trennen der Verbindung|-|

### <a name="return-response"></a>Antwort zurückgeben

`WebPubSubTrigger` berücksichtigt die von den Kund*innen zurückgegebene Antwort für synchrone Ereignisse für `connect` und Benutzerereignisse für `message`. Nur eine passende Antwort wird an den Dienst zurückgesendet, ansonsten wird die Antwort ignoriert. 

| Rückgabetyp | BESCHREIBUNG | Eigenschaften |
|---------|---------|---------|
|`ConnectResponse`| Antwort auf ein `connect`-Ereignis | Groups, Roles, UserId, Subprotocol |
|`MessageResponse`| Antwort auf ein Benutzerereignis | DataType, Message |
|`ErrorResponse`| Fehlerantwort für das Synchronisierungsereignis | Code, ErrorMessage |
|`ServiceResponse`| Basisantworttyp der unterstützten Typen, der bei unsicheren Rückgaben verwendet wird | - |

## <a name="input-binding"></a>Eingabebindung

Unsere Erweiterung bietet zwei Eingabebindungen für unterschiedliche Anforderungen.

- `WebPubSubConnection`

  Damit ein Client eine Verbindung mit dem Azure Web PubSub-Dienst herstellen kann, muss er über die Endpunkt-URL des Diensts und ein gültiges Zugriffstoken verfügen. Die `WebPubSubConnection`-Eingabebindung erstellt die erforderlichen Informationen, sodass der Client diese Tokengenerierung nicht selbst übernehmen muss. Da das Token nur für einen bestimmten Zeitraum gültig ist und nur für die Authentifizierung eines bestimmten Benutzers bzw. einer bestimmten Benutzerin für eine Verbindung verwendet werden kann, sollten Sie das Token nicht zwischenspeichern oder für mehrere Clients verwenden. Ein HTTP-Trigger, der diese Eingabebindung nutzt, kann für Clients verwendet werden, um die Verbindungsinformationen abzurufen.

- `WebPubSubRequest`

  Wenn Sie Static Web Apps verwenden, ist `HttpTrigger` der einzige unterstützte Trigger. Im Web PubSub-Szenario wird die `WebPubSubRequest`-Eingabebindung zur Verfügung gestellt, die Benutzer*innen dabei hilft, dienstseitige Upstream-HTTP-Anforderungen unter Web PubSub-Protokollen zu deserialisieren. So können Kund*innen für eine einfache Verarbeitung in Funktionen ähnliche Ergebnisse wie mit `WebPubSubTrigger` erzielen. Sehen Sie sich hierzu die [Beispiele](#example---webpubsubrequest) unten an.
  Bei Verwendung mit `HttpTrigger` müssen Kund*innen die an HttpTrigger weitergegebene URL im Upstream entsprechend konfigurieren.

### <a name="example---webpubsubconnection"></a>Beispiel – `WebPubSubConnection`

Das folgende Beispiel zeigt eine C#-Funktion, die mithilfe der Eingabebindung Web PubSub-Verbindungsinformationen abruft und über HTTP zurückgibt.

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

### <a name="example---webpubsubrequest"></a>Beispiel – `WebPubSubRequest`

Das folgende Beispiel zeigt eine C#-Funktion, die mithilfe der Eingabebindung im Ereignistyp „connect“ Web PubSub-Anforderungsinformationen abruft und über HTTP zurückgibt.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubRequestInputBinding")]
public static object Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubRequest] WebPubSubRequest wpsReq)
{
    if (wpsReq.Request.IsValidationRequest || !wpsReq.Request.Valid)
    {
        return wpsReq.Response;
    }
    var request = wpsReq.Request as ConnectEventRequest;
    var response = new ConnectResponse
    {
        UserId = wpsReq.ConnectionContext.UserId
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
      "type": "webPubSubRequest",
      "name": "wpsReq",
      "direction": "in"
    }
  ]
}
```

Definieren Sie die Funktion in `index.js`.

```js
module.exports = async function (context, req, wpsReq) {
  if (!wpsReq.request.valid || wpsReq.request.isValidationRequest)
  {
    console.log(`invalid request: ${wpsReq.response.message}.`);
    return wpsReq.response;
  }
  console.log(`user: ${context.bindings.wpsReq.connectionContext.userId} is connecting.`);
  return { body: {"userId": context.bindings.wpsReq.connectionContext.userId} };
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
| **hub** | Hub | Dieser Wert muss auf den Namen des Web PubSub-Hubs festgelegt werden, damit die Funktion ausgelöst werden kann. Das Festlegen des Werts als einer mit höherer Priorität wird im Attribut unterstützt. Alternativ kann dies in den App-Einstellungen als globaler Wert festgelegt werden. |
| **userId** | UserId | Der optionale Wert des Benutzer-ID-Anspruchs, der im Zugriffsschlüsseltoken festgelegt wird |
| **connectionStringSetting** | ConnectionStringSetting | Der Name der App-Einstellung, die die Verbindungszeichenfolge des Web PubSub-Diensts enthält (standardmäßig „WebPubSubConnectionString“) |

#### <a name="webpubsubrequest"></a>WebPubSubRequest

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei „function.json“ und im Attribut `WebPubSubRequest` festlegen:

| Eigenschaft von „function.json“ | Attributeigenschaft | BESCHREIBUNG |
|---------|---------|---------|
| **type** | – | Muss auf `webPubSubRequest` festgelegt werden. |
| **direction** | – | Muss auf `in` festgelegt werden. |
| **name** | – | Variablenname, der im Funktionscode für die Web PubSub-Eingabeanforderung verwendet wird |

### <a name="usage"></a>Verwendung

#### <a name="webpubsubconnection"></a>WebPubSubConnection

`WebPubSubConnection` verfügt über die unten aufgeführten Eigenschaften.

Bindungsname | Bindungstyp | BESCHREIBUNG
---------|---------|---------
BaseUrl | Zeichenfolge | Verbindungs-URL für den Web PubSub-Client
url | Zeichenfolge | Absoluter URI der Web PubSub-Verbindung, enthält das basierend auf der Anforderung generierte `AccessToken`
AccessToken | Zeichenfolge | Basierend auf der UserId der Anforderung und Dienstinformationen generiertes `AccessToken`

#### <a name="webpubsubrequest"></a>WebPubSubRequest

`WebPubSubRequest` verfügt über die unten aufgeführten Eigenschaften.

Bindungsname | Bindungstyp | BESCHREIBUNG | Eigenschaften
---------|---------|---------|---------
connectionContext | `ConnectionContext` | Allgemeine Anforderungsinformationen| EventType, EventName, Hub, ConnectionId, UserId, Headers, Signature
request | `ServiceRequest` | Anforderung vom Client. Weitere Informationen finden Sie in der nachstehenden Tabelle. | IsValidationRequest, Valid, Unauthorized, BadRequest, ErrorMessage, Name usw.
Antwort | `HttpResponseMessage` | Antwort von Erweiterungsbuilds, die hauptsächlich für `AbuseProtection` und Fehlerfälle verwendet wird. | -

Für `ServiceRequest` wird sie in verschiedene Klassen deserialisiert, die unterschiedliche Informationen zum Anforderungsszenario bieten. Für `ValidationRequest` oder `InvalidRequest` wird empfohlen, dass die Systembuildantwort `WebPubSubRequest.Response` direkt zurückgegeben wird. Alternativ können Kund*innen Fehler bei Bedarf protokollieren. In verschiedenen Szenarios können Kund*innen die Anforderungseigenschaften wie folgt lesen.

Abgeleitete Klasse | BESCHREIBUNG | Eigenschaften
--|--|--
`ValidationRequest` | Dies wird in `AbuseProtection` verwendet, wenn der Wert für `IsValidationRequest` **true** ist. | -
`ConnectEventRequest` | Dies wird im Ereignistyp `Connect` verwendet. | Claims, Query, Subprotocols, ClientCertificates
`ConnectedEventRequest` | Dies wird im Ereignistyp `Connected` verwendet. | -
`MessageEventRequest` | Dies wird im Benutzerereignistyp verwendet. | Message, DataType
`DisconnectedEventRequest` | Dies wird im Ereignistyp `Disconnected` verwendet. | `Reason`
`InvalidRequest` | Verwenden Sie dies, wenn die Anforderung ungültig ist. | -

## <a name="output-binding"></a>Ausgabebindung

Verwenden Sie die Web PubSub-Ausgabebindung, um eine oder mehrere Nachrichten mithilfe des Azure Web PubSub-Diensts zu senden. Sie können eine Nachricht senden an:

* Alle verbundenen Clients
* Verbundene Clients, die für einen bestimmten Benutzer authentifiziert sind
* Verbundene Clients, die in einer bestimmten Gruppe miteinander verknüpft sind

Mit der Ausgabebindung können Sie außerdem Gruppen verwalten und Berechtigungen für eine bestimmte connectionId mit „group“ erteilen oder widerrufen.

Informationen zu Setup- und Konfigurationsdetails finden Sie in der Übersicht.

### <a name="example"></a>Beispiel

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubOutputBinding")]
public static async Task RunAsync(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSub(Hub = "<hub>")] IAsyncCollector<WebPubSubOperation> operations)
{
    await operations.AddAsync(new SendToAll
    {
        Message = BinaryData.FromString("Hello Web PubSub"),
        DataType = MessageDataType.Text
    });
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
      "name": "webPubSubOperation",
      "hub": "<hub>",
      "direction": "out"
    }
  ]
}
```

Definieren Sie die Funktion in `index.js`.

```js
module.exports = async function (context) {
  context.bindings.webPubSubOperation = {
    "operationKind": "sendToAll",
    "message": "hello",
    "dataType": "text"
  };
  context.done();
}
```

---

### <a name="webpubsuboperation"></a>WebPubSubOperation 

`WebPubSubOperation` ist der abstrakte Basistyp von Ausgabebindungen. Die abgeleiteten Typen stellen den Vorgang dar, bei dem der Server möchte, dass die Dienste ihn aufrufen. Bei typenlosen Sprachen wie `javascript` ist `OperationKind` der Schlüsselparameter für das Auflösen des Typs. Bei stark typisierten Sprachen wie `csharp` könnten Benutzer*innen den Zielvorgangstyp direkt erstellen. In diesem Fall würde der kundenseitig zugewiesene Wert `OperationKind` ignoriert.

Abgeleitete Klasse|Eigenschaften
--|--
`SendToAll`|Message, DataType, Excluded
`SendToGroup`|Group, Message, DataType, Excluded
`SendToUser`|UserId, Message, DataType
`SendToConnection`|ConnectionId, Message, DataType
`AddUserToGroup`|UserId, Group
`RemoveUserFromGroup`|UserId, Group
`RemoveUserFromAllGroups`|UserId
`AddConnectionToGroup`|ConnectionId, Group
`RemoveConnectionFromGroup`|ConnectionId, Group
`CloseClientConnection`|ConnectionId, Reason
`GrantGroupPermission`|ConnectionId, Group, Permission, TargetName
`RevokeGroupPermission`|ConnectionId, Group, Permission, TargetName

### <a name="configuration"></a>Konfiguration

#### <a name="webpubsub"></a>WebPubSub

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei function.json und im Attribut `WebPubSub` festlegen:

| Eigenschaft von „function.json“ | Attributeigenschaft | BESCHREIBUNG |
|---------|---------|---------|
| **type** | – | Muss auf `webPubSub` festgelegt werden. |
| **direction** | – | Muss auf `out` festgelegt werden. |
| **name** | – | Variablenname, der im Funktionscode für das Ausgabebindungsobjekt verwendet wird |
| **hub** | Hub | Dieser Wert muss auf den Namen des Web PubSub-Hubs festgelegt werden, damit die Funktion ausgelöst werden kann. Das Festlegen des Werts als einer mit höherer Priorität wird im Attribut unterstützt. Alternativ kann dies in den App-Einstellungen als globaler Wert festgelegt werden. |
| **connectionStringSetting** | ConnectionStringSetting | Der Name der App-Einstellung, die die Verbindungszeichenfolge des Web PubSub-Diensts enthält (standardmäßig „WebPubSubConnectionString“) |

## <a name="troubleshooting"></a>Problembehandlung

### <a name="setting-up-console-logging"></a>Einrichten der Konsolenprotokollierung
Sie können auch einfach die [Konsolenprotokollierung aktivieren](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#logging), wenn Sie ausführliche Informationen zu den von Ihnen an den Dienst gesendeten Anforderungen erhalten möchten.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/functions

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]