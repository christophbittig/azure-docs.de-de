---
title: 'Tutorial: Veröffentlichen und Abonnieren von Nachrichten zwischen WebSocket-Clients mit einem Unterprotokoll im Azure Web PubSub-Dienst'
description: In diesem Tutorial erfahren Sie, wie Sie den Azure Web PubSub-Dienst und in unterstütztes WebSocket-Unterprotokoll für die Synchronisierung zwischen Clients verwenden.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/01/2021
ms.openlocfilehash: 8a181f48bcdf7ec186aac1b05d3aaf135fa35c09
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550474"
---
# <a name="tutorial-publish-and-subscribe-messages-between-websocket-clients-using-subprotocol"></a>Tutorial: Veröffentlichen und Abonnieren von Nachrichten zwischen WebSocket-Clients mit einem Unterprotokoll

Im [Tutorial zum Erstellen einer Chat-App](./tutorial-build-chat.md) haben Sie gelernt, wie Sie WebSocket-APIs zum Senden und Empfangen von Daten mit Azure Web PubSub verwenden. Sie sehen, dass kein Protokoll erforderlich ist, wenn der Client mit dem Dienst kommuniziert. Sie können z. B. `WebSocket.send()` verwenden, um beliebige Daten zu senden, und der Server erhält die Daten, wie sie gesendet wurden. Die Verwendung ist einfach, allerdings ist auch die Funktionalität eingeschränkt. Sie können z. B. nicht den Ereignisnamen angeben, wenn Sie das Ereignis an den Server senden, oder Nachrichten an andere Clients veröffentlichen, anstatt sie an den Server zu senden. In diesem Tutorial erfahren Sie, wie Sie ein Unterprotokoll verwenden, um die Funktionalität des Clients zu erweitern.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Erstellen einer Web PubSub-Dienstinstanz
> * Generieren der vollständigen URL zum Herstellen der WebSocket-Verbindung
> * Veröffentlichen von Nachrichten zwischen WebSocket-Clients mit einem Unterprotokoll

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für dieses Setup ist mindestens Version 2.22.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-an-azure-web-pubsub-instance"></a>Erstellen einer Azure Web PubSub-Instanz

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

### <a name="create-a-web-pubsub-instance"></a>Erstellen einer Web PubSub-Instanz

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

### <a name="get-the-connectionstring-for-future-use"></a>Abrufen der Verbindungszeichenfolge zur späteren Verwendung

[!INCLUDE [Get the connection string](includes/cli-awps-connstr.md)]

Kopieren Sie den abgerufenen **ConnectionString**-Wert. Diese Zeichenfolge wird später im Tutorial als Wert für `<connection_string>` verwendet.

## <a name="set-up-the-project"></a>Einrichten des Projekts

### <a name="prerequisites"></a>Voraussetzungen

# <a name="c"></a>[C#](#tab/csharp)

* [ASP.NET Core 3.1 oder höher](/aspnet/core)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x oder höher](https://nodejs.org)

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

# <a name="java"></a>[Java](#tab/java)
- [Java Development Kit (JDK)](/java/azure/jdk/), Version 8 oder höher
- [Apache Maven](https://maven.apache.org/download.cgi)

---

## <a name="using-a-subprotocol"></a>Verwenden eines Unterprotokolls

Der Client kann mithilfe eines bestimmten [Unterprotokolls](https://datatracker.ietf.org/doc/html/rfc6455#section-1.9) eine WebSocket-Verbindung starten. Der Azure Web PubSub-Dienst unterstützt ein Unterprotokoll namens `json.webpubsub.azure.v1`, um es Clients zu ermöglichen, Nachrichten direkt zu veröffentlichen/abonnieren, ohne einen Roundtrip zum Upstreamserver durchführen zu müssen. Details zum Unterprotokoll finden Sie im Artikel zur [Unterstützung des JSON WebSocket-Unterprotokolls für Azure Web PubSub](./reference-json-webpubsub-subprotocol.md).

> Wenn Sie andere Protokollnamen verwenden, werden diese vom Dienst ignoriert und im Connect-Ereignishandler an den Server durchgelassen, sodass Sie eigene Protokolle erstellen können.

Lassen Sie uns jetzt eine Webanwendung mit dem Unterprotokoll `json.webpubsub.azure.v1` erstellen.

1.  Installieren von Abhängigkeiten

    # <a name="c"></a>[C#](#tab/csharp)
    ```bash
    mkdir logstream
    cd logstream
    dotnet new web
    dotnet add package Microsoft.Extensions.Azure
    dotnet add package Azure.Messaging.WebPubSub
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    
    ```bash
    mkdir logstream
    cd logstream
    npm init -y
    npm install --save express
    npm install --save ws
    npm install --save node-fetch
    npm install --save @azure/web-pubsub
    ```

    # <a name="python"></a>[Python](#tab/python)
    
    ```bash
    mkdir logstream
    cd logstream
    # Create venv
    python -m venv env
    # Active venv
    source ./env/bin/activate

    pip install azure-messaging-webpubsubservice
    ```
    
    # <a name="java"></a>[Java](#tab/java)
    
    Sie verwenden das Webframework [Javalin](https://javalin.io/), um die Webseiten zu hosten.
    
    1. Verwenden Sie zunächst Maven, um die neue App `logstream-webserver` zu erstellen und zum Ordner *logstream-webserver* zu wechseln:
    
        ```console
        mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.tutorial --define artifactId=logstream-webserver --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
        cd logstream-webserver
        ```
    
    2. Jetzt fügen Sie das Azure Web PubSub SDK und die Webframeworkabhängigkeit `javalin` dem Knoten `dependencies` in `pom.xml` hinzu:
    
        * `javalin`: einfaches Webframework für Java
        * `slf4j-simple`: Protokollierung für Java
        * `azure-messaging-webpubsub`: Das Dienstclient-SDK für die Verwendung von Azure Web PubSub

        ```xml
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-messaging-webpubsub</artifactId>
            <version>1.0.0-beta.6</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/io.javalin/javalin -->
        <dependency>
            <groupId>io.javalin</groupId>
            <artifactId>javalin</artifactId>
            <version>3.13.6</version>
        </dependency>
    
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-simple</artifactId>
            <version>1.7.30</version>
        </dependency>
        ```
    ---
    
2.  Erstellen Sie die Serverseite zum Hosten der `/negotiate`-API und der Webseite.

    # <a name="c"></a>[C#](#tab/csharp)

    Aktualisieren Sie `Startup.cs` mit dem folgenden Code.
    - Aktualisieren Sie die `ConfigureServices`-Methode, um den Dienstclient hinzuzufügen, und lesen Sie die Verbindungszeichenfolge aus der Konfiguration. 
    - Aktualisieren Sie die `Configure`-Methode, um `app.UseStaticFiles();` vor `app.UseRouting();` hinzuzufügen, damit statische Dateien unterstützt werden. 
    - Aktualisieren Sie außerdem `app.UseEndpoints` zum Generieren des Clientzugriffstokens mit `/negotiate`-Anforderungen.

    ```csharp
    using Azure.Messaging.WebPubSub;

    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Extensions.Azure;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Hosting;
    
    namespace logstream
    {
        public class Startup
        {
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }
    
            public IConfiguration Configuration { get; }
    
            public void ConfigureServices(IServiceCollection services)
            {
                services.AddAzureClients(builder =>
                {
                    builder.AddWebPubSubServiceClient(Configuration["Azure:WebPubSub:ConnectionString"], "stream");
                });
            }
    
            public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
            {
                if (env.IsDevelopment())
                {
                    app.UseDeveloperExceptionPage();
                }
    
                app.UseStaticFiles();
    
                app.UseRouting();
    
                app.UseEndpoints(endpoints =>
                {
                    endpoints.MapGet("/negotiate", async context =>
                    {
                        var service = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
                        var response = new
                        {
                            url = service.GetClientAccessUri(roles: new string[] { "webpubsub.sendToGroup.stream", "webpubsub.joinLeaveGroup.stream" }).AbsoluteUri
                        };
                        await context.Response.WriteAsJsonAsync(response);
                    });
                });
            }
        }
    }
    
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    Erstellen Sie eine `server.js`, und fügen Sie den folgenden Code hinzu:

    ```javascript
    const express = require('express');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    let service = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, 'stream');
    const app = express();

    app.get('/negotiate', async (req, res) => {
      let token = await service.getClientAccessToken({
        roles: ['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream']
      });
      res.send({
        url: token.url
      });
    });

    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

    # <a name="python"></a>[Python](#tab/python)
    
    Erstellen Sie eine `server.py` zum Hosten der `/negotiate`-API und der Webseite.

    ```python
    import json
    import sys
    
    from http.server import HTTPServer, SimpleHTTPRequestHandler
    
    from azure.messaging.webpubsubservice import WebPubSubServiceClient
    
    service = WebPubSubServiceClient.from_connection_string(sys.argv[1], hub='stream')
    
    class Resquest(SimpleHTTPRequestHandler):
        def do_GET(self):
            if self.path == '/':
                self.path = 'public/index.html'
                return SimpleHTTPRequestHandler.do_GET(self)
            elif self.path == '/negotiate':
                roles = ['webpubsub.sendToGroup.stream',
                         'webpubsub.joinLeaveGroup.stream']
                token = service.get_client_access_token(roles=roles)
                self.send_response(200)
                self.send_header('Content-Type', 'application/json')
                self.end_headers()
                self.wfile.write(json.dumps({
                    'url': token['url']
                }).encode())
    
    if __name__ == '__main__':
        if len(sys.argv) != 2:
            print('Usage: python server.py <connection-string>')
            exit(1)
    
        server = HTTPServer(('localhost', 8080), Resquest)
        print('server started')
        server.serve_forever()
    
    ```
    
    # <a name="java"></a>[Java](#tab/java)

    Jetzt navigieren wir zum Verzeichnis */src/main/java/com/webpubsub/tutorial*, öffnen die Datei *App.java* im Editor und verwenden `Javalin.create` zum Verarbeiten von statischen Dateien:

    ```java
    package com.webpubsub.tutorial;
    
    import com.azure.messaging.webpubsub.WebPubSubServiceClient;
    import com.azure.messaging.webpubsub.WebPubSubServiceClientBuilder;
    import com.azure.messaging.webpubsub.models.GetClientAccessTokenOptions;
    import com.azure.messaging.webpubsub.models.WebPubSubClientAccessToken;
    
    import io.javalin.Javalin;
    
    public class App {
        public static void main(String[] args) {
            
            if (args.length != 1) {
                System.out.println("Expecting 1 arguments: <connection-string>");
                return;
            }
    
            // create the service client
            WebPubSubServiceClient service = new WebPubSubServiceClientBuilder()
                    .connectionString(args[0])
                    .hub("chat")
                    .buildClient();
    
            // start a server
            Javalin app = Javalin.create(config -> {
                config.addStaticFiles("public");
            }).start(8080);
    
            
            // Handle the negotiate request and return the token to the client
            app.get("/negotiate", ctx -> {
                GetClientAccessTokenOptions option = new GetClientAccessTokenOptions();
                option.addRole("webpubsub.sendToGroup.stream");
                option.addRole("webpubsub.joinLeaveGroup.stream");
                WebPubSubClientAccessToken token = service.getClientAccessToken(option);
    
                // return JSON string
                ctx.result("{\"url\":\"" + token.getUrl() + "\"}");
                return;
            });
        }
    }
    ```

    Je nach Setup müssen Sie die Sprachebene möglicherweise explizit auf Java 8 festlegen. Diese Festlegung kann in „pom.xml“ durchgeführt werden. Fügen Sie den folgenden Codeausschnitt hinzu:
    ```xml
    <build>
        <plugins>
            <plugin>
              <artifactId>maven-compiler-plugin</artifactId>
              <version>3.8.0</version>
              <configuration>
                <source>1.8</source>
                <target>1.8</target>
              </configuration>
            </plugin>
        </plugins>
    </build>
    ```
    ---
    
3.  Erstellen der Webseite

    # <a name="c"></a>[C#](#tab/csharp)
    Erstellen Sie eine HTML-Seite mit folgendem Inhalt, und speichern Sie sie als `wwwroot/index.html`:
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    Erstellen Sie eine HTML-Seite mit folgendem Inhalt, und speichern Sie sie als `public/index.html`:

    # <a name="python"></a>[Python](#tab/python)

    Erstellen Sie eine HTML-Seite mit folgendem Inhalt, und speichern Sie sie als `public/index.html`:
    
    # <a name="java"></a>[Java](#tab/java)

    <a name="create-an-html-page-with-below-content-and-save-it-to-srcmainresourcespublicindexhtml"></a>Erstellen Sie eine HTML-Seite mit folgendem Inhalt, und speichern Sie sie unter */src/main/resources/public/index.html*:
    ---
    
    ```html
    <html>

    <body>
      <div id="output"></div>
      <script>
        (async function () {
          let res = await fetch('/negotiate')
          let data = await res.json();
          let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
          ws.onopen = () => {
            console.log('connected');
          };

          let output = document.querySelector('#output');
          ws.onmessage = event => {
            let d = document.createElement('p');
            d.innerText = event.data;
            output.appendChild(d);
          };
        })();
      </script>
    </body>

    </html>
    ```

    Sie stellt lediglich eine Verbindung mit dem Dienst her und gibt alle auf der Seite empfangenen Nachrichten aus. Die Hauptänderung besteht darin, dass wir das Unterprotokoll beim Erstellen der WebSocket-Verbindung angeben.

4. Ausführen des Servers
    # <a name="c"></a>[C#](#tab/csharp)
    Wir verwenden das Tool [Secret Manager](/aspnet/core/security/app-secrets#secret-manager) für .NET Core, um die Verbindungszeichenfolge festzulegen. Führen Sie den folgenden Befehl aus, ersetzen Sie `<connection_string>` darin durch den Wert, den Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben, und öffnen Sie http://localhost:5000/index.html im Browser:

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set Azure:WebPubSub:ConnectionString "<connection-string>"
    dotnet run
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    
    Führen Sie den folgenden Befehl aus, ersetzen Sie `<connection-string>` darin durch den Wert von **ConnectionString**, den Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben, und öffnen Sie http://localhost:8080 im Browser:

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node server
    ```
    
    # <a name="python"></a>[Python](#tab/python)

    Führen Sie den folgenden Befehl aus, ersetzen Sie `<connection-string>` darin durch den Wert von **ConnectionString**, den Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben, und öffnen Sie http://localhost:8080 im Browser:

    ```bash
    python server.py "<connection-string>"
    ```

    # <a name="java"></a>[Java](#tab/java)

    Führen Sie den folgenden Befehl aus, ersetzen Sie `<connection-string>` darin durch den Wert von **ConnectionString**, den Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben, und öffnen Sie http://localhost:8080 im Browser:

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>'"
    ```
    ---

    Wenn Sie Chrome verwenden, können Sie F12 drücken oder im Kontextmenü **Überprüfen** -> **Entwicklertools** auswählen. Wählen Sie dann die Registerkarte **Netzwerk** aus. Laden Sie die Webseite, und Sie können sehen, dass die WebSocket-Verbindung hergestellt wird. Klicken Sie, um die WebSocket-Verbindung zu überprüfen. Im Client wird die folgende `connected`-Ereignismeldung empfangen. Sie sehen, dass Sie die für diesen Client generierte `connectionId` abrufen können.
    
    ```json
    {"type":"system","event":"connected","userId":null,"connectionId":"<the_connection_id>"}
    ```

Sie sehen, dass Sie mithilfe des Unterprotokolls einige Metadaten der Verbindung abrufen können, wenn der Verbindungsstatus `connected` ist.

Beachten Sie außerdem, dass der Client anstelle von Klartext jetzt eine JSON-Nachricht empfängt, die mehr Informationen enthält, z. B. den Nachrichtentyp und den Absender. Sie können diese Informationen verwenden, um die Nachricht weiter zu verarbeiten. Sie können die Nachricht z. B. in einem anderen Format anzeigen, wenn sie von einer anderen Quelle stammt. Dies wird in späteren Abschnitten behandelt.

## <a name="publish-messages-from-client"></a>Veröffentlichen von Nachrichten vom Client

Wenn der Client im Tutorial zum [Erstellen einer Chat-App](./tutorial-build-chat.md) eine Nachricht über eine WebSocket-Verbindung sendet, wird auf der Serverseite ein Benutzerereignis ausgelöst. Beim Unterprotokoll ist die Funktionalität des Clients größer, da eine JSON-Nachricht gesendet wird. Sie können beispielsweise Sie Nachrichten direkt vom Client auf anderen Clients veröffentlichen.

Dies ist nützlich, wenn Sie umfangreiche Daten in Echtzeit an andere Clients streamen möchten. Wir verwenden dieses Feature, um eine Protokollstreaminganwendung zu erstellen, die Konsolenprotokolle in Echtzeit an den Browser streamen kann.

1. Erstellen des Streamingprogramms
 
    # <a name="c"></a>[C#](#tab/csharp)
    Erstellen Sie ein `stream`-Programm:
    ```bash
    mkdir stream
    cd stream
    dotnet new console
    ```
    
    Aktualisieren Sie `Program.cs` mit dem folgenden Inhalt:
    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.WebSockets;
    using System.Text;
    using System.Text.Json;
    using System.Threading.Tasks;
    
    namespace stream
    {
        class Program
        {
            private static readonly HttpClient http = new HttpClient();
            static async Task Main(string[] args)
            {
                // Get client url from remote
                var stream = await http.GetStreamAsync("http://localhost:5000/negotiate");
                var url = (await JsonSerializer.DeserializeAsync<ClientToken>(stream)).url;
                var client = new ClientWebSocket();
                client.Options.AddSubProtocol("json.webpubsub.azure.v1");
    
                await client.ConnectAsync(new Uri(url), default);
    
                Console.WriteLine("Connected.");
                var streaming = Console.ReadLine();
                while (streaming != null)
                {
                    if (!string.IsNullOrEmpty(streaming))
                    {
                        var message = JsonSerializer.Serialize(new
                        {
                            type = "sendToGroup",
                            group = "stream",
                            data = streaming + Environment.NewLine,
                        });
                        Console.WriteLine("Sending " + message);
                        await client.SendAsync(Encoding.UTF8.GetBytes(message), WebSocketMessageType.Text, true, default);
                    }
    
                    streaming = Console.ReadLine();
                }
    
                await client.CloseAsync(WebSocketCloseStatus.NormalClosure, null, default);
            }
    
            private sealed class ClientToken
            {
                public string url { get; set; }
            }
        }
    }
    
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    Erstellen Sie eine `stream.js` mit dem folgenden Inhalt.
    
    ```javascript
    const WebSocket = require('ws');
    const fetch = require('node-fetch');

    async function main() {
      let res = await fetch(`http://localhost:8080/negotiate`);
      let data = await res.json();
      let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
      let ackId = 0;
      ws.on('open', () => {
        process.stdin.on('data', data => {
          ws.send(JSON.stringify({
            type: 'sendToGroup',
            group: 'stream',
            ackId: ++ackId,
            dataType: 'text',
            data: data.toString()
          }));
        });
      });
      ws.on('message', data => console.log("Received: %s", data));
      process.stdin.on('close', () => ws.close());
    }

    main();
    ```

    Der obige Code stellt eine WebSocket-Verbindung zum Dienst her. Wenn er danach Daten empfängt, wird `ws.send()` verwendet, um die Daten zu veröffentlichen. Wenn die Veröffentlichung an andere erfolgen soll, müssen Sie lediglich `type` auf `sendToGroup` festlegen und einen Gruppennamen in der Nachricht angeben.

    # <a name="python"></a>[Python](#tab/python)

    Öffnen Sie ein weiteres bash-Fenster für das `stream`-Programm, und installieren Sie die `websockets`-Abhängigkeit:

    ```bash
    mkdir stream
    cd stream

    # Create venv
    python -m venv env
    # Active venv
    source ./env/bin/activate

    pip install websockets
    ```

    Erstellen Sie eine `stream.py` mit dem folgenden Inhalt.

    ```python
    import asyncio
    import sys
    import threading
    import time
    import websockets
    import requests
    import json
    
    async def connect(url):
        async with websockets.connect(url, subprotocols=['json.webpubsub.azure.v1']) as ws:
            print('connected')
            id = 1
            while True:
                data = input()
                payload = {
                    'type': 'sendToGroup',
                    'group': 'stream',
                    'dataType': 'text',
                    'data': str(data + '\n'),
                    'ackId': id
                }
                id = id + 1
                await ws.send(json.dumps(payload))
                await ws.recv()
    
    if __name__ == '__main__':
        res = requests.get('http://localhost:8080/negotiate').json()
    
        try:
            asyncio.get_event_loop().run_until_complete(connect(res['url']))
        except KeyboardInterrupt:
            pass
    
    ```

    Der obige Code stellt eine WebSocket-Verbindung zum Dienst her. Wenn er danach Daten empfängt, wird `ws.send()` verwendet, um die Daten zu veröffentlichen. Wenn die Veröffentlichung an andere erfolgen soll, müssen Sie lediglich `type` auf `sendToGroup` festlegen und einen Gruppennamen in der Nachricht angeben.
    
    # <a name="java"></a>[Java](#tab/java)

    1.  Verwenden Sie ein anderes Terminal, und wechseln Sie zurück zum Stammordner, um die Streaming-Konsolen-App `logstream-streaming` zu erstellen und zum Ordner *logstream-streaming* zu wechseln:
        ```console
        mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=logstream-streaming --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
        cd logstream-streaming
        ```
    
    2. Fügen Sie dem Knoten `dependencies` in `pom.xml` HttpClient-Abhängigkeiten hinzu:
    
        ```xml
        <!-- https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient -->
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient</artifactId>
            <version>4.5.13</version>
        </dependency>
        <dependency>
          <groupId>com.google.code.gson</groupId>
          <artifactId>gson</artifactId>
          <version>2.8.9</version>
        </dependency>
        ```
    
    3. Nun verwenden Sie WebSocket, um eine Verbindung mit dem Dienst herzustellen. Navigieren Sie zum Verzeichnis */src/main/java/com/webpubsub/quickstart*, öffnen Sie die Datei *App.java* in Ihrem Editor, und ersetzen Sie den Code durch den folgenden:
    ```java
    package com.webpubsub.quickstart;
    
    import java.io.BufferedReader;
    import java.io.IOException;
    import java.io.InputStreamReader;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.net.http.WebSocket;
    import java.util.concurrent.CompletionStage;
    
    import com.google.gson.Gson;
    
    public class App 
    {
        public static void main( String[] args ) throws IOException, InterruptedException
        {
            HttpClient client = HttpClient.newHttpClient();
            HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create("http://localhost:8080/negotiate"))
                .build();
    
            HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
            
            Gson gson = new Gson();
    
            String url = gson.fromJson(response.body(), Entity.class).url;
    
            WebSocket ws = HttpClient.newHttpClient().newWebSocketBuilder().subprotocols("json.webpubsub.azure.v1")
                    .buildAsync(URI.create(url), new WebSocketClient()).join();
            int id = 0;
            BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
            String streaming = reader.readLine();
            App app = new App();
            while (streaming != null && !streaming.isEmpty()){
                String frame = gson.toJson(app.new GroupMessage(streaming + "\n", ++id));
                System.out.println("Sending: " + frame);
                ws.sendText(frame, true);
                streaming = reader.readLine();
            }
        }
    
        private class GroupMessage{
            public String data;
            public int ackId;
            public final String type = "sendToGroup";
            public final String group = "stream";
            
            GroupMessage(String data, int ackId){
                this.data = data;
                this.ackId = ackId;
            }
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
    
        private static final class Entity {
            public String url;
        }
    }
    
    ```

    4. Navigieren Sie zum Verzeichnis mit der Datei *pom.xml*, und führen Sie das Projekt mithilfe des folgenden Befehls aus:
    
      ```console
      mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false
      ```
    
    ---
    
    Sie können sehen, dass es hier das neue Konzept einer „Gruppe“ gibt. Bei einer Gruppe handelt es sich um ein logisches Konzept in einem Hub, in dem Sie Nachrichten für eine Gruppe von Verbindungen veröffentlichen können. In einem Hub kann es mehrere Gruppen geben, und ein Client kann mehrere Gruppen gleichzeitig abonnieren. Bei Verwendung des Unterprotokolls können Sie die Veröffentlichung nur in einer Gruppe vornehmen, anstatt sie an den gesamten Hub zu übertragen. Details zu den Begriffen finden Sie in des [grundlegenden Konzepten](./key-concepts.md).

2.  Da wir hier die Gruppe verwenden, müssen wir auch die Webseite `index.html` aktualisieren, um der Gruppe beizutreten, wenn die WebSocket-Verbindung im `ws.onopen`-Rückruf hergestellt wird.
    
    ```javascript
    let ackId = 0;
    ws.onopen = () => {
      console.log('connected');
      ws.send(JSON.stringify({
        type: 'joinGroup',
        group: 'stream',
        ackId: ++ackId
      }));
    };
    ```

    Sie können sehen, dass der Client der Gruppe beitritt, indem eine Nachricht mit dem `joinGroup`-Typ gesendet wird.

3.  Aktualisieren Sie weiterhin die `ws.onmessage`-Rückruflogik geringfügig, um die JSON-Antwort zu analysieren und lediglich Nachrichten der `stream`-Gruppe auszugeben, sodass sie als Livestreamausgabe fungiert.

    ```javascript
    ws.onmessage = event => {
      let message = JSON.parse(event.data);
      if (message.type === 'message' && message.group === 'stream') {
        let d = document.createElement('span');
        d.innerText = message.data;
        output.appendChild(d);
        window.scrollTo(0, document.body.scrollHeight);
      }
    };
    ```

4.  Aus Sicherheitsgründen kann ein Client eine Gruppe standardmäßig nicht selbst veröffentlichen oder abonnieren. Sie haben also vielleicht bemerkt, dass wir beim Generieren des Tokens `roles` auf den Client festgelegt haben:

    # <a name="c"></a>[C#](#tab/csharp)
    Legen Sie ungefähr wie folgt `roles` in `Startup.cs` mit `GenerateClientAccessUri` fest:
    ```csharp
    service.GenerateClientAccessUri(roles: new string[] { "webpubsub.sendToGroup.stream", "webpubsub.joinLeaveGroup.stream" })
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    Fügen Sie ungefähr wie folgt `roles` in `server.js` mit `getClientAccessToken` hinzu:

    ```javascript
    app.get('/negotiate', async (req, res) => {
      let token = await service.getClientAccessToken({
        roles: ['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream']
      });
      ...
    });
    
    ```
    
    # <a name="python"></a>[Python](#tab/python)
    
    Beachten Sie, dass beim Generieren des Zugriffstokens die richtigen Rollen für den Client in `server.py` festgelegt werden:

    ```python
    roles = ['webpubsub.sendToGroup.stream',
              'webpubsub.joinLeaveGroup.stream']
    token = service.get_client_access_token(roles=roles)
    ```
    
    # <a name="java"></a>[Java](#tab/java)
    
    Beachten Sie, dass beim Generieren des Zugriffstokens die richtigen Rollen für den Client in `App.java` festgelegt werden:

    ```java
    GetClientAccessTokenOptions option = new GetClientAccessTokenOptions();
    option.addRole("webpubsub.sendToGroup.stream");
    option.addRole("webpubsub.joinLeaveGroup.stream");
    WebPubSubClientAccessToken token = service.getClientAccessToken(option);

    ```
    ---

5.  Wenden Sie schließlich eine Formatierung in `index.html` an, damit die Seite ansprechend angezeigt wird.

    ```html
    <html>

      <head>
        <style>
          #output {
            white-space: pre;
            font-family: monospace;
          }
        </style>
      </head>
    ```

Führen Sie nun den folgenden Code aus, und geben Sie beliebigen Text ein. Dieser wird im Browser in Echtzeit angezeigt:

# <a name="c"></a>[C#](#tab/csharp)

```bash
ls -R | dotnet run

# Or call `dir /s /b | dotnet run` when you are using CMD under Windows

```

Sie können die Anzeige auch verlangsamen, damit Sie sehen können, wie die Daten in Echtzeit an den Browser gestreamt werden:

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | dotnet run
```

Das vollständige Codebeispiel für dieses Tutorial finden Sie [hier][code-csharp].

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`node stream`

Sie können diese App auch verwenden, um eine beliebige Ausgabe einer anderen Konsolen-App zu übernehmen und an den Browser zu streamen. Zum Beispiel:

```bash
ls -R | node stream

# Or call `dir /s /b | node stream` when you are using CMD under Windows
```

Sie können die Anzeige auch verlangsamen, damit Sie sehen können, wie die Daten in Echtzeit an den Browser gestreamt werden:

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | node stream
```

Das vollständige Codebeispiel für dieses Tutorial finden Sie [hier][code-js].

# <a name="python"></a>[Python](#tab/python)

Jetzt können Sie `python stream.py` ausführen und beliebigen Text eingeben. Dieser wird im Browser in Echtzeit angezeigt.

Sie können diese App auch verwenden, um eine beliebige Ausgabe einer anderen Konsolen-App zu übernehmen und an den Browser zu streamen. Zum Beispiel:

```bash
ls -R | python stream.py

# Or call `dir /s /b | python stream.py` when you are using CMD under Windows
```

Sie können die Anzeige auch verlangsamen, damit Sie sehen können, wie die Daten in Echtzeit an den Browser gestreamt werden:

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | python stream.py
```

Das vollständige Codebeispiel für dieses Tutorial finden Sie [hier][code-python].


# <a name="java"></a>[Java](#tab/java)

Jetzt können Sie den folgenden Code ausführen und beliebigen Text eingeben. Dieser wird im Browser in Echtzeit angezeigt.

```console
mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Das vollständige Codebeispiel für dieses Tutorial finden Sie [hier][code-java].

---


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie grundlegende Informationen dazu erhalten, wie Sie mit dem Unterprotokoll eine Verbindung mit dem Web PubSub-Dienst herstellen und Nachrichten auf den verbundenen Clients veröffentlichen.

Bearbeiten Sie weitere Tutorials, um mehr über die Nutzung des Diensts zu erfahren.

> [!div class="nextstepaction"]
> [Weitere Beispiele für Azure Web PubSub](https://aka.ms/awps/samples)

[code-csharp]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp/logstream/

[code-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/logstream/

[code-python]: https://github.com/Azure/azure-webpubsub/tree/main/samples/python/logstream/

[code-java]: https://github.com/Azure/azure-webpubsub/tree/main/samples/java/logstream/
