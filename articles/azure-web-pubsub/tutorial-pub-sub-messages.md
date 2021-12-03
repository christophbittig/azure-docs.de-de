---
title: 'Tutorial: Veröffentlichen und Abonnieren von Nachrichten mithilfe der WebSocket-API und des Azure Web PubSub-Dienst-SDK'
description: In diesem Tutorial erfahren Sie, wie Sie mit dem Azure Web PubSub-Dienst und Azure Functions eine serverlose Anwendung erstellen.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/01/2021
ms.openlocfilehash: 56314c696b58f89144d171314709b5153d250a29
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551439"
---
# <a name="tutorial-publish-and-subscribe-messages-using-websocket-api-and-azure-web-pubsub-service-sdk"></a>Tutorial: Veröffentlichen und Abonnieren von Nachrichten mithilfe der WebSocket-API und des Azure Web PubSub-Dienst-SDK

Der Azure Web PubSub-Dienst unterstützt Sie beim einfachen Erstellen von Echtzeit-Messagingwebanwendungen mithilfe von WebSockets und des Veröffentlichen-Abonnieren-Musters. In diesem Tutorial erfahren Sie, wie Sie den Dienst unter Verwendung der WebSocket-API abonnieren, Nachrichten veröffentlichen und das Dienst-SDK verwenden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Web PubSub-Dienstinstanz
> * Generieren der vollständigen URL zum Herstellen der WebSocket-Verbindung
> * Abonnieren von Nachrichten mithilfe des WebSocket-Standardprotokolls
> * Veröffentlichen von Nachrichten mithilfe des Dienst-SDK

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für dieses Setup ist mindestens Version 2.22.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

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

* [.NET Core 2.1 oder höher](https://dotnet.microsoft.com/download)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x oder höher](https://nodejs.org)

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

# <a name="java"></a>[Java](#tab/java)
- [Java Development Kit (JDK)](/java/azure/jdk/), Version 8 oder höher
- [Apache Maven](https://maven.apache.org/download.cgi)

---

## <a name="set-up-the-subscriber"></a>Einrichten des Abonnenten

Clients verbinden sich mit dem Azure Web PubSub-Dienst über das WebSocket-Standardprotokoll unter Verwendung der [JSON-Webtoken](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)-Authentifizierung. Das Dienst-SDK stellt Hilfsmethoden zum Generieren des Tokens bereit. In diesem Tutorial generiert der Abonnent das Token direkt aus *ConnectionString*. In echten Anwendungen wird üblicherweise eine serverseitige Anwendung zum Verarbeiten des Workflows für die Authentifizierung/Autorisierung verwendet. Arbeiten Sie das Tutorial [Erstellen einer Chat-App](./tutorial-build-chat.md) durch, um den Workflow besser zu verstehen.

# <a name="c"></a>[C#](#tab/csharp)

1. Erstellen Sie zunächst einen neuen Ordner `subscribe` für dieses Projekt, und installieren Sie die erforderlichen Abhängigkeiten:
    * Das Paket [Websocket.Client](https://github.com/Marfusios/websocket-client) ist ein Drittanbieterpaket, das WebSocket-Verbindungen unterstützt. Sie können für diese Aufgabe eine beliebige API/Bibliothek verwenden, die WebSocket unterstützt.
    * Das SDK-Paket `Azure.Messaging.WebPubSub` hilft beim Generieren des JWT-Tokens. 

    ```bash
    mkdir subscriber
    cd subscriber
    dotnet new console
    dotnet add package Websocket.Client --version 4.3.30
    dotnet add package Azure.Messaging.WebPubSub --version 1.0.0
    ```

2. Aktualisieren Sie die Datei `Program.cs`, um eine Verbindung mit dem Dienst herzustellen:

    ```csharp
    using System;
    using System.Threading.Tasks;
    
    using Azure.Messaging.WebPubSub;
    
    using Websocket.Client;
    
    namespace subscriber
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                if (args.Length != 2)
                {
                    Console.WriteLine("Usage: subscriber <connectionString> <hub>");
                    return;
                }
                var connectionString = args[0];
                var hub = args[1];
    
                // Either generate the URL or fetch it from server or fetch a temp one from the portal
                var serviceClient = new WebPubSubServiceClient(connectionString, hub);
                var url = serviceClient.GetClientAccessUri();
    
                using (var client = new WebsocketClient(url))
                {
                    // Disable the auto disconnect and reconnect because the sample would like the client to stay online even no data comes in
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

    Der obige Code erstellt eine WebSocket-Verbindung, um eine Verbindung mit einem Hub in Azure Web PubSub herzustellen. Ein Hub ist eine logische Einheit in Azure Web PubSub, in dem Sie Nachrichten für eine Gruppe von Clients veröffentlichen können. Unter [Wichtige Konzepte](./key-concepts.md) finden Sie ausführliche Erläuterungen der in Azure Web PubSub verwendeten Begriffe.
    
    Der Azure Web PubSub-Dienst verwendet die [JSON-Webtoken](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)-Authentifizierung (JWT). Deshalb nutzt das Codebeispiel `WebPubSubServiceClient.GetClientAccessUri()` im Web PubSub SDK, um eine URL für den Dienst zu generieren, der die vollständige URL mit einem gültigen Zugriffstoken enthält.
    
    Nachdem die Verbindung hergestellt wurde, erhalten Sie Nachrichten über die WebSocket-Verbindung. Daher verwenden wir `client.MessageReceived.Subscribe(msg => ...));`, um auf eingehende Nachrichten zu lauschen.

3. Führen Sie den folgenden Befehl aus, und ersetzen Sie `<connection_string>` durch den Wert von **ConnectionString**, den Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben:

    ```bash
    dotnet run "<connection_string>" "myHub1"
    ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. Erstellen Sie zunächst einen neuen Ordner `subscriber` für dieses Projekt, und installieren Sie die erforderlichen Abhängigkeiten:

    ```bash
    mkdir subscriber
    cd subscriber
    npm init -y
    npm install --save ws
    npm install --save @azure/web-pubsub

    ```
2. Verwenden Sie dann die WebSocket-API, um eine Verbindung mit dem Dienst herzustellen. Erstellen Sie eine Datei `subscribe.js` mit dem folgenden Code:

    ```javascript
    const WebSocket = require('ws');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    async function main() {
      const hub = "pubsub";
      let service = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, hub);
      let token = await service.getClientAccessToken();
      let ws = new WebSocket(token.url);
      ws.on('open', () => console.log('connected'));
      ws.on('message', data => console.log('Message received: %s', data));
    }

    main();
    ```
    
    Der obige Code erstellt eine WebSocket-Verbindung, um eine Verbindung mit einem Hub in Azure Web PubSub herzustellen. Ein Hub ist eine logische Einheit in Azure Web PubSub, in dem Sie Nachrichten für eine Gruppe von Clients veröffentlichen können. Unter [Wichtige Konzepte](./key-concepts.md) finden Sie ausführliche Erläuterungen der in Azure Web PubSub verwendeten Begriffe.
    
    Der Azure Web PubSub-Dienst verwendet die [JSON-Webtoken](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)-Authentifizierung (JWT). Deshalb nutzt das Codebeispiel `WebPubSubServiceClient.getClientAccessToken()` im Web PubSub SDK, um eine URL für den Dienst zu generieren, der die vollständige URL mit einem gültigen Zugriffstoken enthält.
    
    Nachdem die Verbindung hergestellt wurde, erhalten Sie Nachrichten über die WebSocket-Verbindung. Daher verwenden wir `WebSocket.on('message', ...)`, um auf eingehende Nachrichten zu lauschen.
    
3. Führen Sie den folgenden Befehl aus, und ersetzen Sie `<connection_string>` durch den Wert von **ConnectionString**, den Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben:

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node subscribe
    ```

# <a name="python"></a>[Python](#tab/python)

1. Erstellen Sie zunächst einen neuen Ordner `subscriber` für dieses Projekt, und installieren Sie die erforderlichen Abhängigkeiten:

    ```bash
    mkdir subscriber
    cd subscriber
    # Create venv
    python -m venv env
    # Activate venv
    source ./env/bin/activate

    pip install azure-messaging-webpubsubservice
    pip install websockets

    ```

2. Verwenden Sie dann die WebSocket-API, um eine Verbindung mit dem Dienst herzustellen. Erstellen Sie eine Datei `subscribe.py` mit dem folgenden Code:

    ```python
    import asyncio
    import sys
    import websockets
    
    from azure.messaging.webpubsubservice import WebPubSubServiceClient
    
    
    async def connect(url):
        async with websockets.connect(url) as ws:
            print('connected')
            while True:
                print('Received message: ' + await ws.recv())
    
    if __name__ == '__main__':
    
        if len(sys.argv) != 3:
            print('Usage: python subscribe.py <connection-string> <hub-name>')
            exit(1)
    
        connection_string = sys.argv[1]
        hub_name = sys.argv[2]
    
        service = WebPubSubServiceClient.from_connection_string(connection_string, hub=hub_name)
        token = service.get_client_access_token()
    
        try:
            asyncio.get_event_loop().run_until_complete(connect(token['url']))
        except KeyboardInterrupt:
            pass
    
    ```

    Der obige Code erstellt eine WebSocket-Verbindung, um eine Verbindung mit einem Hub in Azure Web PubSub herzustellen. Ein Hub ist eine logische Einheit in Azure Web PubSub, in dem Sie Nachrichten für eine Gruppe von Clients veröffentlichen können. Unter [Wichtige Konzepte](./key-concepts.md) finden Sie ausführliche Erläuterungen der in Azure Web PubSub verwendeten Begriffe.
    
    Der Azure Web PubSub-Dienst verwendet die [JSON-Webtoken](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)-Authentifizierung (JWT). Deshalb nutzt das Codebeispiel `service.get_client_access_token()` (vom SDK bereitgestellt), um eine URL für den Dienst zu generieren, der die vollständige URL mit einem gültigen Zugriffstoken enthält.
    
    Nachdem die Verbindung hergestellt wurde, erhalten Sie Nachrichten über die WebSocket-Verbindung. Daher verwenden wir `await ws.recv()`, um auf eingehende Nachrichten zu lauschen.

3. Führen Sie den folgenden Befehl aus, und ersetzen Sie `<connection_string>` durch den Wert von **ConnectionString**, den Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben:

    ```bash
    python subscribe.py "<connection_string>" "myHub1"
    ```

# <a name="java"></a>[Java](#tab/java)
1. Erstellen Sie für dieses Tutorial zunächst einen neuen Ordner `pubsub`.
    ```cmd
    mkdir pubsub
    cd pubsub
    ```

1. Erstellen Sie dann innerhalb dieses `pubsub`-Ordners mithilfe von Maven eine neue Konsolen-App `webpubsub-quickstart-subscriber`, und wechseln Sie in den Ordner *webpubsub-quickstart-subscriber*:

    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=webpubsub-quickstart-subscriber --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-quickstart-subscriber
    ```

2. Fügen Sie dann in `pom.xml` im Knoten `dependencies` WebSocket und die Azure Web PubSub SDK-Abhängigkeit hinzu:

    * `azure-messaging-webpubsub`: Web PubSub-Dienst-SDK für Java
    * `Java-WebSocket`: WebSocket-Client-SDK für Java

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.6</version>
    </dependency>

    <dependency>
        <groupId>org.java-websocket</groupId>
        <artifactId>Java-WebSocket</artifactId>
        <version>1.5.1</version>
    </dependency>

    ```

3. In Azure Web PubSub können Sie eine Verbindung mit dem Dienst herstellen und Nachrichten über WebSocket-Verbindungen abonnieren. WebSocket ist ein Vollduplex-Kommunikationskanal, sodass der Dienst Nachrichten in Echtzeit an Ihren Client pushen kann. Sie können für diese Aufgabe eine beliebige API/Bibliothek verwenden, die WebSocket unterstützt. Für dieses Beispiel verwenden wir das Paket [Java-WebSocket](https://github.com/TooTallNate/Java-WebSocket). Navigieren Sie zum Verzeichnis */src/main/java/com/webpubsub/quickstart*, öffnen Sie die Datei *App.java* in Ihrem Editor, und ersetzen Sie den Code durch den folgenden:

    ```java
    package com.webpubsub.quickstart;
    
    import com.azure.messaging.webpubsub.*;
    import com.azure.messaging.webpubsub.models.*;
    
    import org.java_websocket.client.WebSocketClient;
    import org.java_websocket.handshake.ServerHandshake;
    
    import java.io.IOException;
    import java.net.URI;
    import java.net.URISyntaxException;
    
    /**
    * Connect to Azure Web PubSub service using WebSocket protocol
    *
    */
    public class App 
    {
        public static void main( String[] args ) throws IOException, URISyntaxException
        {
            if (args.length != 2) {
                System.out.println("Expecting 2 arguments: <connection-string> <hub-name>");
                return;
            }
    
            WebPubSubServiceClient service = new WebPubSubServiceClientBuilder()
                .connectionString(args[0])
                .hub(args[1])
                .buildClient();
    
            WebPubSubClientAccessToken token = service.getClientAccessToken(new GetClientAccessTokenOptions());
    
            WebSocketClient webSocketClient = new WebSocketClient(new URI(token.getUrl())) {
                @Override
                public void onMessage(String message) {
                    System.out.println(String.format("Message received: %s", message));
                }
    
                @Override
                public void onClose(int arg0, String arg1, boolean arg2) {
                    // TODO Auto-generated method stub
                }
    
                @Override
                public void onError(Exception arg0) {
                    // TODO Auto-generated method stub
                }
    
                @Override
                public void onOpen(ServerHandshake arg0) {
                    // TODO Auto-generated method stub
                }
    
            };
    
            webSocketClient.connect();
            System.in.read();
        }
    }

    ```

    Der obige Code erstellt eine WebSocket-Verbindung, um eine Verbindung mit einem Hub in Azure Web PubSub herzustellen. Ein Hub ist eine logische Einheit in Azure Web PubSub, in dem Sie Nachrichten für eine Gruppe von Clients veröffentlichen können. Unter [Wichtige Konzepte](./key-concepts.md) finden Sie ausführliche Erläuterungen der in Azure Web PubSub verwendeten Begriffe.
    
    Der Azure Web PubSub-Dienst verwendet die [JSON-Webtoken](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)-Authentifizierung (JWT). Deshalb nutzt das Codebeispiel `WebPubSubServiceClient.getClientAccessToken(new GetClientAccessTokenOptions())` im Web PubSub SDK, um eine URL für den Dienst zu generieren, der die vollständige URL mit einem gültigen Zugriffstoken enthält.
    
    Nachdem die Verbindung hergestellt wurde, erhalten Sie Nachrichten über die WebSocket-Verbindung. Daher verwenden wir `onMessage(String message)`, um auf eingehende Nachrichten zu lauschen.

4. Navigieren Sie zu dem Verzeichnis, das die Datei *pom.xml* enthält, und führen Sie die App mit dem folgenden Code aus. Ersetzen Sie dabei `<connection_string>` durch den Wert von **ConnectionString**, den Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben:

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1'"
    ```

---

## <a name="publish-messages-using-service-sdk"></a>Veröffentlichen von Nachrichten mithilfe des Dienst-SDK

Verwenden Sie nun das Azure Web PubSub SDK, um eine Nachricht auf dem verbundenen Client zu veröffentlichen.

# <a name="c"></a>[C#](#tab/csharp)

1. Erstellen Sie zunächst einen neuen Ordner `publisher` für dieses Projekt, und installieren Sie die erforderlichen Abhängigkeiten:

    ```bash
    mkdir publisher
    cd publisher
    dotnet new console
    dotnet add package Azure.Messaging.WebPubSub
    ```

2. Aktualisieren Sie dann die Datei `Program.cs`, um die Klasse `WebPubSubServiceClient` zu verwenden und Nachrichten an die Clients zu senden.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.WebPubSub;
    
    namespace publisher
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                if (args.Length != 3) {
                    Console.WriteLine("Usage: publisher <connectionString> <hub> <message>");
                    return;
                }
                var connectionString = args[0];
                var hub = args[1];
                var message = args[2];
                
                // Either generate the token or fetch it from server or fetch a temp one from the portal
                var serviceClient = new WebPubSubServiceClient(connectionString, hub);
                await serviceClient.SendToAllAsync(message);
            }
        }
    }
    
    ```

    Der Aufruf `SendToAllAsync()` sendet einfach eine Nachricht an alle verbundenen Clients im Hub.

3. Führen Sie den folgenden Befehl aus, und ersetzen Sie `<connection_string>` durch den Wert von **ConnectionString**, den Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben:

    ```bash
    dotnet run "<connection_string>" "myHub1" "Hello World"
    ```

4. Sie sehen, dass der vorherige Abonnent die folgende Nachricht erhalten hat:
   
    ```
    Message received: Hello World
    ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. Erstellen Sie zunächst einen neuen Ordner (`publisher`) für dieses Projekt, und installieren Sie die erforderlichen Abhängigkeiten:

    ```bash
    mkdir publisher
    cd publisher
    npm init -y
    npm install --save @azure/web-pubsub

    ```
2. Verwenden Sie nun das Azure Web PubSub SDK, um eine Nachricht im Dienst zu veröffentlichen. Erstellen Sie eine Datei vom Typ `publish.js` mit dem folgenden Code:

    ```javascript
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    const hub = "pubsub";
    let service = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, hub);

    // by default it uses `application/json`, specify contentType as `text/plain` if you want plain-text
    service.sendToAll(process.argv[2], { contentType: "text/plain" });
    ```

    Der Aufruf `service.sendToAll()` sendet einfach eine Nachricht an alle verbundenen Clients in einem Hub.

3. Führen Sie den folgenden Befehl aus, und ersetzen Sie `<connection_string>` durch den Wert von **ConnectionString**, den Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben:

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node publish "Hello World"
    ```

4. Sie sehen, dass der vorherige Abonnent die folgende Nachricht erhalten hat:
   
    ```
    Message received: Hello World
    ```

# <a name="python"></a>[Python](#tab/python)

1. Erstellen Sie zunächst einen neuen Ordner (`publisher`) für dieses Projekt, und installieren Sie die erforderlichen Abhängigkeiten:
    ```bash
    mkdir publisher
    cd publisher
    # Create venv
    python -m venv env
    # Active venv
    source ./env/bin/activate

    pip install azure-messaging-webpubsubservice

    ```
2. Verwenden Sie nun das Azure Web PubSub SDK, um eine Nachricht im Dienst zu veröffentlichen. Erstellen Sie eine Datei vom Typ `publish.py` mit dem folgenden Code:

    ```python
    import sys
    from azure.messaging.webpubsubservice import WebPubSubServiceClient
    
    if __name__ == '__main__':
    
        if len(sys.argv) != 4:
            print('Usage: python publish.py <connection-string> <hub-name> <message>')
            exit(1)
    
        connection_string = sys.argv[1]
        hub_name = sys.argv[2]
        message = sys.argv[3]
    
        service = WebPubSubServiceClient.from_connection_string(connection_string, hub=hub_name)
        res = service.send_to_all(message, content_type='text/plain')
        print(res)
    ```

    Mit `send_to_all()` wird die Nachricht an alle verbundenen Clients in einem Hub gesendet.

3. Führen Sie den folgenden Befehl aus, und ersetzen Sie `<connection_string>` durch den Wert von **ConnectionString**, den Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben:

    ```bash
    python publish.py "<connection_string>" "myHub1" "Hello World"
    ```

4. Sie sehen, dass der vorherige Abonnent die folgende Nachricht erhalten hat:
   
    ```
    Received message: Hello World
    ```

# <a name="java"></a>[Java](#tab/java)

1.  Verwenden wir ein anderes Terminal und wechseln zurück zum Ordner `pubsub`, um eine Publisher-Konsolen-App `webpubsub-quickstart-publisher` zu erstellen und zum Ordner *webpubsub-quickstart-publisher* zu wechseln:
    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=webpubsub-quickstart-publisher --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-quickstart-publisher
    ```

2. Fügen Sie dann in `pom.xml` im Knoten `dependencies` die Azure Web PubSub SDK-Abhängigkeit hinzu:

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.6</version>
    </dependency>
    ```

3. Verwenden Sie nun das Azure Web PubSub SDK, um eine Nachricht im Dienst zu veröffentlichen. Navigieren Sie zum Verzeichnis */src/main/java/com/webpubsub/quickstart*, öffnen Sie die Datei *App.java* in Ihrem Editor, und ersetzen Sie den Code durch den folgenden:

    ```java

    package com.webpubsub.quickstart;
    
    import com.azure.messaging.webpubsub.*;
    import com.azure.messaging.webpubsub.models.*;
    
    /**
    * Publish messages using Azure Web PubSub service SDK
    *
    */
    public class App 
    {
        public static void main( String[] args )
        {
            if (args.length != 3) {
                System.out.println("Expecting 3 arguments: <connection-string> <hub-name> <message>");
                return;
            }
    
            WebPubSubServiceClient service = new WebPubSubServiceClientBuilder()
                .connectionString(args[0])
                .hub(args[1])
                .buildClient();
            service.sendToAll(args[2], WebPubSubContentType.TEXT_PLAIN);
        }
    }

    ```

    Der Aufruf `sendToAll()` sendet einfach eine Nachricht an alle verbundenen Clients in einem Hub.

4. Navigieren Sie zu dem Verzeichnis, das die Datei *pom.xml* enthält, und führen Sie das Projekt mit dem folgenden Befehl aus. Ersetzen Sie dabei `<connection_string>` durch den Wert von **ConnectionString**, den Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben:

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1' 'Hello World'"
    ```

5. Sie sehen, dass der vorherige Abonnent die folgende Nachricht erhalten hat:
   
    ```
    Message received: Hello World
    ```

---

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie grundlegende Informationen dazu erhalten, wie Sie eine Verbindung mit dem Web PubSub-Dienst herstellen und Nachrichten auf den verbundenen Clients veröffentlichen.

Bearbeiten Sie weitere Tutorials, um mehr über die Nutzung des Diensts zu erfahren.

> [!div class="nextstepaction"]
> [Tutorial: Erstellen eines Chatrooms mit Azure Web PubSub](./tutorial-build-chat.md)

> [!div class="nextstepaction"]
> [Weitere Beispiele für Azure Web PubSub](https://aka.ms/awps/samples)
