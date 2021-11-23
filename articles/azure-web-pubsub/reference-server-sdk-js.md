---
title: 'Referenz: JavaScript-SDK für Azure Web PubSub'
description: In der Referenz wird das JavaScript-SDK für den Azure Web PubSub-Dienst beschrieben.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: 899f9845721412ceef9171c334383e596fd798e6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493055"
---
# <a name="javascript-sdk-for-azure-web-pubsub"></a>JavaScript-SDK für Azure Web PubSub

Der [Azure Web PubSub-Dienst](https://aka.ms/awps/doc) ist ein verwalteter Azure-Dienst, der Entwicklern die einfache Erstellung von Webanwendungen mit Echtzeitfunktionen und einem Veröffentlichen-Abonnieren-Muster ermöglicht. Der Azure Web PubSub-Dienst kann in allen Szenarien verwendet werden, für die Veröffentlichen-Abonnieren-Messaging in Echtzeit zwischen Server und Clients oder zwischen Clients erforderlich ist. Herkömmliche Echtzeitfeatures, die häufig das Abrufen vom Server oder das Übermitteln von HTTP-Anforderungen erfordern, können ebenfalls den Azure Web PubSub-Dienst verwenden.

Für JavaScript werden zwei Bibliotheken angeboten: die Dienstclientbibliothek und Express-Middleware. Die folgenden Abschnitte enthalten weitere Informationen zu diesen Bibliotheken.

- [Dienstclientbibliothek](#service-client-library)
- [Express-Middleware](#express)

<a name="service-client-library"></a>

## <a name="azure-web-pubsub-service-client-library-for-javascript"></a>Die Clientbibliothek des Azure Web PubSub-Diensts für JavaScript

Sie können diese Bibliothek auf der App-Serverseite verwenden, um die WebSocket-Clientverbindungen zu verwalten, wie in der folgenden Abbildung dargestellt:

![Das Überlaufdiagramm zeigt den Überlauf der Verwendung der Dienstclientbibliothek.](media/sdk-reference/service-client-overflow.png)

- Das Versenden von Nachrichten an Hubs und Gruppen.
- Das Versenden von Nachrichten an bestimmte Benutzer und Verbindungen.
- Das Organisieren von Benutzern und Verbindungen in Gruppen.
- Das Schließen von Verbindungen
- Das Erteilen, Widerrufen und Prüfen von Berechtigungen für eine vorhandene Verbindung

Details zu den hier verwendeten Begriffen werden im Abschnitt [Wichtige Begriffe](#key-concepts) beschrieben.

[Quellcode](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/web-pubsub/web-pubsub) |
[Paket (NPM)](https://www.npmjs.com/package/@azure/web-pubsub) |
[API-Referenzdokumentation](https://aka.ms/awps/sdk/js) |
[Produktdokumentation](https://aka.ms/awps/doc) |
[Beispiele][samples_ref]

### <a name="getting-started"></a>Erste Schritte

#### <a name="currently-supported-environments"></a>Die derzeitig unterstützten Umgebungen

- [LTS-Versionen von Node.js](https://nodejs.org/about/releases/)

#### <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Abonnement][azure_sub].
- Eine vorhandene Azure Web PubSub-Dienstinstanz.

#### <a name="1-install-the-azureweb-pubsub-package"></a>1. Installieren Sie das Paket `@azure/web-pubsub`

```bash
npm install @azure/web-pubsub
```

#### <a name="2-create-and-authenticate-a-webpubsubserviceclient"></a>2. Erstellen und Authentifizieren Sie einen WebPubSubServiceClient

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
```

Sie können die `WebPubSubServiceClient` auch mithilfe eines Endpunkts und eines `AzureKeyCredential` authentifizieren:

```js
const { WebPubSubServiceClient, AzureKeyCredential } = require("@azure/web-pubsub");

const key = new AzureKeyCredential("<Key>");
const serviceClient = new WebPubSubServiceClient("<Endpoint>", key, "<hubName>");
```

Oder authentifizieren Sie `WebPubSubServiceClient` mit [Azure Active Directory][aad_doc]

1. Installieren der `@azure/identity`-Abhängigkeit

```batch
npm install @azure/identity
```

2. Aktualisieren Sie den Quellcode für die Verwendung von `DefaultAzureCredential`:

```js
const { WebPubSubServiceClient, AzureKeyCredential } = require("@azure/web-pubsub");

const key = new DefaultAzureCredential();
const serviceClient = new WebPubSubServiceClient("<Endpoint>", key, "<hubName>");
```

### <a name="key-concepts"></a>Wichtige Begriffe

#### <a name="connection"></a>Verbindung

Eine Verbindung (auch Client oder Clientverbindung genannt) stellt eine einzelne WebSocket-Verbindung mit dem Web PubSub-Dienst dar. Wenn die Verbindung erfolgreich hergestellt wurde, wird ihr vom Web PubSub-Dienst eine eindeutige Verbindungs-ID zugewiesen.

#### <a name="hub"></a>Hub

Ein Hub ist ein logisches Konzept für eine Gruppe von Clientverbindungen. In der Regel wird jeweils ein einzelner Hub für einen einzelnen Zweck verwendet – beispielsweise ein Chathub oder ein Benachrichtigungshub. Eine Clientverbindung wird mit einem Hub hergestellt und gehört während ihrer Lebensdauer zu diesem Hub. Von verschiedenen Anwendungen können unterschiedliche Hubnamen verwendet werden, um gemeinsam einen einzelnen Azure Web PubSub-Dienst zu nutzen.

#### <a name="group"></a>Group

Eine Gruppe ist eine Teilmenge der Verbindungen mit dem Hub. Sie können einer Gruppe eine Clientverbindung hinzufügen und sie jederzeit wieder aus der Gruppe entfernen. Beispiel: Wenn ein Client einem Chatroom beitritt oder wenn ein Client den Chatroom verlässt, kann dieser Chatroom als Gruppe betrachtet werden. Ein Client kann mehreren Gruppen beitreten, und eine Gruppe kann mehrere Clients enthalten.

#### <a name="user"></a>Benutzer

Verbindungen mit Web PubSub können zu einem einzelnen Benutzer gehören. Ein Benutzer kann über mehrere Verbindungen verfügen, etwa, wenn ein einzelner Benutzer über mehrere Geräte oder mehrere Browsertabs verbunden ist.

#### <a name="message"></a>`Message`

Wenn der Client verbunden ist, kann er Nachrichten an die Upstreamanwendung senden oder Nachrichten von der Upstreamanwendung über die WebSocket-Verbindung empfangen.

### <a name="examples"></a>Beispiele

#### <a name="get-the-access-token-for-a-client-to-start-the-websocket-connection"></a>Abrufen des Zugriffstokens für einen Client zum Starten der WebSocket-Verbindung

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

// Get the access token for the WebSocket client connection to use
let token = await serviceClient.getClientAccessToken();

// Or get the access token and assign the client a userId
token = await serviceClient.getClientAccessToken({ userId: "user1" });

// return the token to the WebSocket client
```

#### <a name="broadcast-messages-to-all-connections-in-a-hub"></a>Übertragen von Nachrichten an alle Clientverbindungen in einem Hub

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

// Send a JSON message
await serviceClient.sendToAll({ message: "Hello world!" });

// Send a plain text message
await serviceClient.sendToAll("Hi there!", { contentType: "text/plain" });

// Send a binary message
const payload = new Uint8Array(10);
await serviceClient.sendToAll(payload.buffer);
```

#### <a name="send-messages-to-all-connections-in-a-group"></a>Senden von Nachrichten an alle Verbindungen in einer Gruppe

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

const groupClient = serviceClient.group("<groupName>");

// Add user to the group
await groupClient.addUser("user1");

// Send a JSON message
await groupClient.sendToAll({ message: "Hello world!" });

// Send a plain text message
await groupClient.sendToAll("Hi there!", { contentType: "text/plain" });

// Send a binary message
const payload = new Uint8Array(10);
await groupClient.sendToAll(payload.buffer);
```

#### <a name="send-messages-to-all-connections-for-a-user"></a>Senden von Nachrichten an alle Verbindungen für einen Benutzer

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

// Send a JSON message
await serviceClient.sendToUser("user1", { message: "Hello world!" });

// Send a plain text message
await serviceClient.sendToUser("user1", "Hi there!", { contentType: "text/plain" });

// Send a binary message
const payload = new Uint8Array(10);
await serviceClient.sendToUser("user1", payload.buffer);
```

#### <a name="check-if-the-group-has-any-connection"></a>Überprüfen, ob die Gruppe über eine Verbindung verfügt

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");
const WebSocket = require("ws");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

const groupClient = serviceClient.group("<groupName>");

// close all the connections in the group
await groupClient.closeAllConnections({ reason: "<closeReason>" });

// check if the group has any connections
const hasConnections = await serviceClient.groupExists("<groupName>");
```

#### <a name="access-the-raw-http-response-for-an-operation"></a>Zugreifen auf die unformatierte HTTP-Antwort für einen Vorgang

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

function onResponse(rawResponse: FullOperationResponse): void {
  console.log(rawResponse);
}
const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll({ message: "Hello world!" }, { onResponse });
```

### <a name="troubleshooting"></a>Problembehandlung

#### <a name="enable-logs"></a>Aktivieren von Protokollen

Sie können die folgende Umgebungsvariable festlegen, um Debugprotokolle anzuzeigen, wenn Sie diese Bibliothek verwenden.

- Abrufen von Debugprotokollen aus der SignalR-Clientbibliothek

```bash
export AZURE_LOG_LEVEL=verbose
```

Ausführlichere Anweisungen zum Aktivieren von Protokollen finden Sie in der [@azure/logger Paketdokumentation](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/core/logger).

#### <a name="live-trace"></a>Live-Ablaufverfolgung

Verwenden Sie **Live Trace** aus dem Web PubSub-Dienstportal, um den Live-Datenverkehr anzuzeigen.

<a name="express"></a>

## <a name="azure-web-pubsub-cloudevents-handlers-for-express"></a>Azure Web PubSub CloudEvents-Handler für Express

Wenn eine WebSocket-Verbindung hergestellt wird, transformiert der Web PubSub-Dienst den Verbindungslebenszyklus und Nachrichten in [Ereignisse im CloudEvents-Format](concept-service-internals.md#workflow). Diese Bibliothek stellt eine Express-Middleware zum Verarbeiten von Ereignissen zur Verfügung, die den Lebenszyklus und die Nachrichten der WebSocket-Verbindung darstellen, wie in der folgenden Abbildung gezeigt:

![Das Überlaufdiagramm zeigt den Überlauf der Verwendung der Ereignishandlermiddleware.](media/sdk-reference/event-handler-middleware.png)

Details zu den hier verwendeten Begriffen werden im Abschnitt [Wichtige Begriffe](#key-concepts) beschrieben.

[Quellcode](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/web-pubsub/web-pubsub-express) |
[Paket (NPM)](https://www.npmjs.com/package/@azure/web-pubsub-express) |
[API-Referenzdokumentation](https://aka.ms/awps/sdk/js) |
[Produktdokumentation](https://aka.ms/awps/doc) |
[Beispiele][samples_ref]

### <a name="getting-started"></a>Erste Schritte

#### <a name="currently-supported-environments"></a>Die derzeitig unterstützten Umgebungen

- [LTS-Versionen von Node.js](https://nodejs.org/about/releases/)
- [Express](https://expressjs.com/) Version 4.x.x oder höher

#### <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Abonnement][azure_sub].
- Ein vorhandener Azure Web PubSub-Endpunkt.

#### <a name="1-install-the-azureweb-pubsub-express-package"></a>1. Installieren Sie das `@azure/web-pubsub-express`-Paket

```bash
npm install @azure/web-pubsub-express
```

#### <a name="2-create-a-webpubsubeventhandler"></a>2. Erstellen eines `WebPubSubEventHandler`

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat");

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

### <a name="key-concepts"></a>Wichtige Begriffe

#### <a name="connection"></a>Verbindung

Eine Verbindung (auch Client oder Clientverbindung genannt) stellt eine einzelne WebSocket-Verbindung mit dem Web PubSub-Dienst dar. Wenn die Verbindung erfolgreich hergestellt wurde, wird ihr vom Web PubSub-Dienst eine eindeutige Verbindungs-ID zugewiesen.

#### <a name="hub"></a>Hub

Ein Hub ist ein logisches Konzept für eine Gruppe von Clientverbindungen. In der Regel wird jeweils ein einzelner Hub für einen einzelnen Zweck verwendet – beispielsweise ein Chathub oder ein Benachrichtigungshub. Eine Clientverbindung wird mit einem Hub hergestellt und gehört während ihrer Lebensdauer zu diesem Hub. Von verschiedenen Anwendungen können unterschiedliche Hubnamen verwendet werden, um gemeinsam einen einzelnen Azure Web PubSub-Dienst zu nutzen.

#### <a name="group"></a>Group

Eine Gruppe ist eine Teilmenge der Verbindungen mit dem Hub. Sie können einer Gruppe eine Clientverbindung hinzufügen und sie jederzeit wieder aus der Gruppe entfernen. Beispiel: Wenn ein Client einem Chatroom beitritt oder wenn ein Client den Chatroom verlässt, kann dieser Chatroom als Gruppe betrachtet werden. Ein Client kann mehreren Gruppen beitreten, und eine Gruppe kann mehrere Clients enthalten.

#### <a name="user"></a>Benutzer

Verbindungen mit Web PubSub können zu einem einzelnen Benutzer gehören. Ein Benutzer kann über mehrere Verbindungen verfügen, etwa, wenn ein einzelner Benutzer über mehrere Geräte oder mehrere Browsertabs verbunden ist.

#### <a name="client-events"></a>Clientereignisse

Ereignisse werden während des Lebenszyklus einer Clientverbindung erstellt. Eine einfache WebSocket-Clientverbindung erstellt z. B. ein `connect`-Ereignis, wenn versucht wird, eine Verbindung mit dem Dienst herzustellen, ein `connected`-Ereignis, wenn erfolgreich eine Verbindung mit dem Dienst hergestellt wurde, ein `message`-Ereignis, wenn Nachrichten an den Dienst gesendet werden und ein `disconnected`-Ereignis, wenn die Verbindung mit dem Dienst getrennt wird.

#### <a name="event-handler"></a>Ereignishandler

Der Event-Handler enthält die Logik zum Behandeln der Clientereignisse. Der Ereignishandler muss im Dienst über das Portal oder die Azure CLI im Voraus registriert und konfiguriert werden. Der Ort zum Hosten der Ereignishandlerlogik wird im Allgemeinen als serverseitig betrachtet.

### <a name="examples"></a>Beispiele

#### <a name="handle-the-connect-request-and-assign-userid"></a>Verarbeiten der `connect`-Anforderung und Zuweisen von `<userId>`

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat", {
  handleConnect: (req, res) => {
    // auth the connection and set the userId of the connection
    res.success({
      userId: "<userId>"
    });
  },
  allowedEndpoints: ["https://<yourAllowedService>.webpubsub.azure.com"]
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

#### <a name="only-allow-specified-endpoints"></a>Nur angegebene Endpunkte zulassen

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat", {
  allowedEndpoints: [
    "https://<yourAllowedService1>.webpubsub.azure.com",
    "https://<yourAllowedService2>.webpubsub.azure.com"
  ]
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

#### <a name="set-custom-event-handler-path"></a>Festlegen des benutzerdefinierten Ereignishandlerpfads

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat", {
  path: "customPath1"
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  // Azure WebPubSub Upstream ready at http://localhost:3000/customPath1
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

#### <a name="set-and-read-connection-state"></a>Festlegen und Lesen des Verbindungsstatus

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");

const handler = new WebPubSubEventHandler("chat", {
  handleConnect(req, res) {
    // You can set the state for the connection, it lasts throughout the lifetime of the connection
    res.setState("calledTime", 1);
    res.success();
  },
  handleUserEvent(req, res) {
    var calledTime = req.context.states.calledTime++;
    console.log(calledTime);
    // You can also set the state here
    res.setState("calledTime", calledTime);
    res.success();
  }
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

### <a name="troubleshooting"></a>Problembehandlung

#### <a name="enable-logs"></a>Aktivieren von Protokollen

Sie können die folgende Umgebungsvariable festlegen, um Debugprotokolle anzuzeigen, wenn Sie diese Bibliothek verwenden.

- Abrufen von Debugprotokollen aus der SignalR-Clientbibliothek

```bash
export AZURE_LOG_LEVEL=verbose
```

Ausführlichere Anweisungen zum Aktivieren von Protokollen finden Sie in der [@azure/logger Paketdokumentation](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/core/logger).

#### <a name="live-trace"></a>Live-Ablaufverfolgung

Verwenden Sie **Live Trace** aus dem Web PubSub-Dienstportal, um den Live-Datenverkehr anzuzeigen.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]