---
title: Wie man eine WebSocket-Verbindung zu Azure Web PubSub startet
description: Erfahren Sie, wie Sie eine WebSocket-Verbindung zum Azure Web PubSub-Dienst in verschiedenen Sprachen starten können.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 895e79b649d23ec1330784a179f3f36b2f0f73ad
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131996070"
---
#  <a name="start-a-websocket-connection-to-azure-web-pubsub"></a>Starten einer WebSocket-Verbindung zu Azure Web PubSub

Clients verbinden sich mit dem Azure Web PubSub-Dienst über das Standardprotokoll [WebSocket](https://tools.ietf.org/html/rfc6455). Sie können Sprachen verwenden, die WebSocket-Client-Unterstützung haben, um einen Client für den Dienst zu schreiben. In diesem Artikel sehen Sie mehrere WebSocket-Client-Beispiele in verschiedenen Sprachen.

## <a name="authorization"></a>Authorization

Web PubSub verwendet ein [JSON Web Token (JWT)](https://tools.ietf.org/html/rfc7519.html) zur Validierung und Autorisierung von Kunden. Clients können das Token entweder in den `access_token` Query-Parameter oder in den `Authorization` Header bei der Verbindung mit dem Dienst einfügen.

Normalerweise kommuniziert der Client zuerst mit seinem App-Server, um die URL des Dienstes und das Token zu erhalten. Dann öffnet der Client die WebSocket-Verbindung zum Dienst unter Verwendung der URL und des Tokens, die er erhält.

Das Portal bietet auch ein Tool, um die Client-URL mit dem Token dynamisch zu generieren. Dieses Tool kann nützlich sein, um einen schnellen Test durchzuführen.

:::image type="content" source="./media/howto-websocket-connect/generate-client-url.png" alt-text="Screenshot: Position des Client-URL-Generators":::

> [!NOTE]
> Achten Sie darauf, dass Sie bei der Erstellung des Tokens nur die erforderlichen Rollen angeben.
>

In den folgenden Abschnitten verwenden wir zur Vereinfachung des Beispiel-Workflows diese vorübergehend vom Portal generierte URL für die Verbindung des Clients. Wir verwenden `<Client_URL_From_Portal>`, um den Wert darzustellen. Das generierte Token läuft standardmäßig nach 60 Minuten ab. Vergessen Sie daher nicht, ein neues Token zu generieren, wenn das Token abläuft.

Es werden zwei Arten von WebSocket-Clients unterstützt: ein einfacher WebSocket-Client und ein PubSub-WebSocket-Client. Hier wird gezeigt, wie diese beiden Clienttypen eine Verbindung mit dem Dienst herstellen. Weitere Informationen zu diesen Clients finden Sie unter [WebSocket-Clientprotokolle für Azure Web PubSub](./concept-client-protocols.md).

## <a name="dependency"></a>Abhängigkeit

# <a name="in-browser"></a>[Im Browser](#tab/browser)
In den meisten modernen Browsern wird die `WebSocket`-API nativ unterstützt. 

# <a name="nodejs"></a>[Node.js](#tab/javascript)

* [Node.js 12.x oder höher](https://nodejs.org)
* `npm install ws`

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)
* `pip install websockets`

# <a name="c"></a>[C#](#tab/csharp)

* [.NET Core 2.1 oder höher](https://dotnet.microsoft.com/download)
* `dotnet add package Websocket.Client`
    * [Websocket.Client](https://github.com/Marfusios/websocket-client) ist ein WebSocket-Client eines Drittanbieters mit integrierter Wiederverbindungs- und Fehlerbehandlung.

# <a name="java"></a>[Java](#tab/java)
- [Java Development Kit (JDK)](/java/azure/jdk/) Version 8 oder höher.
- [Apache Maven](https://maven.apache.org/download.cgi).

---

## <a name="simple-websocket-client"></a>Einfacher WebSocket-Client

# <a name="in-browser"></a>[Im Browser](#tab/browser)

Innerhalb des `script`-Blocks der HTML-Seite:
```html
<script>
    // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    let ws = new WebSocket("<Client_URL_From_Portal>");
    ws.onopen = () => {
        // Do things when the WebSocket connection is established
    };

    ws.onmessage = event => {
        // Do things when messages are received.
    };
</script>
```

# <a name="nodejs"></a>[Node.js](#tab/javascript)

```js
const WebSocket = require('ws');
// Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
const client = new WebSocket("<Client_URL_From_Portal>");
client.on('open', () => {
     // Do things when the WebSocket connection is established
});
client.on('message', msg => {
     // Do things when messages are received.
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import asyncio
import websockets

async def hello():
    # Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    uri = '<Client_URL_From_Portal>'
    async with websockets.connect(uri) as ws:
        while True:
            await ws.send('hello')
            greeting = await ws.recv()
            print(greeting)

asyncio.get_event_loop().run_until_complete(hello())
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Websocket.Client;

namespace subscriber
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
            using (var client = new WebsocketClient(new Uri("<Client_URL_From_Portal>")))
            {
                // Disable the auto disconnect and reconnect because the sample would like the client to stay online even if no data comes in
                client.ReconnectTimeout = null;
                client.MessageReceived.Subscribe(msg => Console.WriteLine($"Message received: {msg}"));
                await client.Start();
                Console.WriteLine("Connected.");
                Console.Read();
            }
        }
    }
}
```

# <a name="java"></a>[Java](#tab/java)

```java
package client;

import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.WebSocket;
import java.util.concurrent.CompletionStage;

/**
 * A simple WebSocket Client.
 *
 */
public final class SimpleClient {
    private SimpleClient() {
    }

    /**
     * Starts a simple WebSocket connection.
     * @param args The arguments of the program.
     */
    public static void main(String[] args) throws Exception {
        // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
        WebSocket ws = HttpClient.newHttpClient().newWebSocketBuilder()
                .buildAsync(URI.create("<Client_URL_From_Portal>"), new WebSocketClient()).join();
        System.in.read();
    }

    private static final class WebSocketClient implements WebSocket.Listener {
        private WebSocketClient() {
        }

        @Override
        public void onOpen(WebSocket webSocket) {
            System.out.println("onOpen using subprotocol " + webSocket.getSubprotocol());
            WebSocket.Listener.super.onOpen(webSocket);
        }

        @Override
        public CompletionStage<?> onText(WebSocket webSocket, CharSequence data, boolean last) {
            System.out.println("onText received " + data);
            return WebSocket.Listener.super.onText(webSocket, data, last);
        }

        @Override
        public void onError(WebSocket webSocket, Throwable error) {
            System.out.println("Bad day! " + webSocket.toString());
            WebSocket.Listener.super.onError(webSocket, error);
        }
    }
}

```


---

## <a name="pubsub-websocket-client"></a>PubSub WebSocket-Klient

# <a name="in-browser"></a>[Im Browser](#tab/browser)

Innerhalb des `script`-Blocks der HTML-Seite:
```html
<script>
    // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    let ws = new WebSocket("<Client_URL_From_Portal>", 'json.webpubsub.azure.v1');
    ws.onopen = () => {
        // Do things when the WebSocket connection is established
    };

    ws.onmessage = event => {
        // Do things when messages are received.
    };
</script>
```

# <a name="nodejs"></a>[Node.js](#tab/javascript)

```js
const WebSocket = require('ws');
// Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
const client = new WebSocket("<Client_URL_From_Portal>", "json.webpubsub.azure.v1");
client.on('open', () => {
     // Do things when the WebSocket connection is established
});
client.on('message', msg => {
     // Do things when messages are received.
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import asyncio
import websockets

async def join_group():
    # Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    uri = '<Client_URL_From_Portal>'
    async with websockets.connect(uri, subprotocols=['json.webpubsub.azure.v1']) as ws:
        await ws.send('{"type":"joinGroup","ackId":1,"group":"group1"}')
        return await ws.recv()

print(asyncio.get_event_loop().run_until_complete(join_group()))
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.IO;
using System.Net.WebSockets;
using System.Text;
using System.Threading.Tasks;
namespace subscriber
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
            using (var client = new WebsocketClient(new Uri("<Client_URL_From_Portal>"), () =>
            {
                var inner = new ClientWebSocket();
                inner.Options.AddSubProtocol("json.webpubsub.azure.v1");
                return inner;
            }))
            {
                // Disable the auto disconnect and reconnect because the sample would like the client to stay online even if no data comes in
                client.ReconnectTimeout = null;
                client.MessageReceived.Subscribe(msg => Console.WriteLine($"Message received: {msg}"));
                await client.Start();
                Console.WriteLine("Connected.");
                Console.Read();
            }
        }
    }
}
```

# <a name="java"></a>[Java](#tab/java)

```java
package client;

import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.WebSocket;
import java.util.concurrent.CompletionStage;

/**
 * A PubSub WebSocket Client.
 *
 */
public final class SubprotocolClient {
    private SubprotocolClient() {
    }

    /**
     * Starts a PubSub WebSocket connection.
     * @param args The arguments of the program.
     */
    public static void main(String[] args) throws Exception {
        // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
        WebSocket ws = HttpClient.newHttpClient().newWebSocketBuilder().subprotocols("json.webpubsub.azure.v1")
                .buildAsync(URI.create("<Client_URL_From_Portal>"), new WebSocketClient()).join();

        ws.sendText("{\"type\":\"joinGroup\",\"ackId\":1,\"group\":\"group1\"}", true);
        System.in.read();
    }

    private static final class WebSocketClient implements WebSocket.Listener {
        private WebSocketClient() {
        }

        @Override
        public void onOpen(WebSocket webSocket) {
            System.out.println("onOpen using subprotocol " + webSocket.getSubprotocol());
            WebSocket.Listener.super.onOpen(webSocket);
        }

        @Override
        public CompletionStage<?> onText(WebSocket webSocket, CharSequence data, boolean last) {
            System.out.println("onText received " + data);
            return WebSocket.Listener.super.onText(webSocket, data, last);
        }

        @Override
        public void onError(WebSocket webSocket, Throwable error) {
            System.out.println("Bad day! " + webSocket.toString());
            WebSocket.Listener.super.onError(webSocket, error);
        }
    }
}
```

---

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie eine Verbindung zum Dienst herstellen, indem Sie die vom Portal generierte URL verwenden. Um zu sehen, wie die Clients mit dem App-Server kommunizieren, um die URL in realen Anwendungen zu erhalten, lesen Sie diese Tutorials und sehen Sie sich die Beispiele an.

> [!div class="nextstepaction"]
> [Tutorial: Erstellen einer Chat-App mit Azure Web PubSub](./tutorial-build-chat.md)

> [!div class="nextstepaction"]
> [Tutorial: Clientstreaming mit einem vom Dienst unterstützten Unterprotokoll](./tutorial-subprotocol.md)

> [!div class="nextstepaction"]
> [Weitere Beispiele für Azure Web PubSub](https://aka.ms/awps/samples)

