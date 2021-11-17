---
title: 'Tutorial: Erstellen einer Chat-App mit dem Azure Web PubSub-Dienst'
description: Tutorial zum Erstellen einer Chat-App mit dem Azure Web PubSub-Dienst
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/01/2021
ms.openlocfilehash: 38890a5bf968bf88678e0665847fb067708291c3
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547258"
---
# <a name="tutorial-create-a-chat-app-with-azure-web-pubsub-service"></a>Tutorial: Erstellen einer Chat-App mit dem Azure Web PubSub-Dienst

Im [Tutorial zum Veröffentlichen und Abonnieren von Nachrichten](./tutorial-pub-sub-messages.md) haben Sie die Grundlagen des Veröffentlichens und Abonnierens von Nachrichten mit Azure Web PubSub kennengelernt. In diesem Tutorial lernen Sie das Ereignissystem von Azure Web PubSub kennen und nutzen es für die Erstellung einer vollständigen Webanwendung mit Echtzeitkommunikationsfunktionalität. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Web PubSub-Dienstinstanz
> * Konfigurieren von Ereignishandlereinstellungen für Azure Web PubSub
> * Erstellen eines Echtzeit-Chat-Bots

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

* [ASP.NET Core 3.1 oder höher](/aspnet/core)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x oder höher](https://nodejs.org)

# <a name="java"></a>[Java](#tab/java)

- [Java Development Kit (JDK)](/java/azure/jdk/), Version 8 oder höher
- [Apache Maven](https://maven.apache.org/download.cgi)

---

## <a name="create-the-application"></a>Erstellen der Anwendung

In Azure Web PubSub gibt es zwei Rollen: Server und Client. Dieses Konzept ähnelt den Server- und Clientrollen in einer Webanwendung. Der Server ist verantwortlich für die Verwaltung der Clients, lauscht und antwortet auf Clientnachrichten, während die Rolle des Clients darin besteht, die Nachrichten des Benutzers an den Server zu senden, Nachrichten vom Server zu empfangen und diese für den Endbenutzer zu visualisieren.

In diesem Tutorial erstellen wir eine Echtzeitchat-Webanwendung. In einer echten Webanwendung umfasst die Verantwortung des Servers auch die Authentifizierung von Clients und die Bereitstellung statischer Webseiten für die Benutzeroberfläche der Anwendung. 

# <a name="c"></a>[C#](#tab/csharp)

Wir verwenden [ASP.NET Core](/aspnet/core), um die Webseiten zu hosten und eingehende Anforderungen zu verarbeiten.

Erstellen wir zunächst eine leere ASP.NET Core-App.

1.  Erstellen einer Web-App

    ```bash
    dotnet new web
    dotnet add package Azure.Messaging.WebPubSub
    ```

2.  Fügen Sie dann `app.UseStaticFiles();` vor `app.UseRouting();` in `Startup.cs` ein, um statische Dateien zu unterstützen. Entfernen Sie das standardmäßige `endpoints.MapGet` in `app.UseEndpoints`.

    ```csharp
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
        });
    }
    ```

3.  Erstellen Sie außerdem eine HTML-Datei, und speichern Sie sie als `wwwroot/index.html`. Wir verwenden sie später für die Benutzeroberfläche der Chat-App.

    ```html
    <html>
    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

Sie können den Server testen, indem Sie `dotnet run --urls http://localhost:8080` ausführen und im Browser auf http://localhost:8080/index.html zugreifen.

Sie erinnern sich vielleicht, dass der Abonnent im [Tutorial zum Veröffentlichen und Abonnieren von Nachrichten](./tutorial-pub-sub-messages.md) eine API im Web PubSub SDK verwendet, um ein Zugriffstoken aus der Verbindungszeichenfolge zu generieren und damit eine Verbindung mit dem Dienst herzustellen. Dies ist für eine reale Anwendung im Allgemeinen keine sichere Vorgehensweise, da die Verbindungszeichenfolge über weitreichende Berechtigungen zum Ausführen von Vorgängen für den Dienst verfügt und deshalb für keinen Client freigegeben werden sollte. Wir ändern diesen Prozess zur Generierung von Zugriffstoken in eine REST-API auf Serverseite. So kann der Client diese API immer dann zur Anforderung eines Zugriffstokens aufrufen, wenn eine Verbindung hergestellt werden muss, ohne die Verbindungszeichenfolge speichern zu müssen.

1.  Installieren Sie Abhängigkeiten, und verwenden Sie das [Geheimnis-Manager-Tool](/aspnet/core/security/app-secrets#secret-manager) für .NET Core, um die Verbindungszeichenfolge festzulegen. Führen Sie den folgenden Befehl aus, und ersetzen Sie `<connection_string>` darin durch den Wert, den Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben:

    ```bash
    dotnet add package Microsoft.Extensions.Azure
    dotnet user-secrets init
    dotnet user-secrets set Azure:WebPubSub:ConnectionString "<connection-string>"
    ```

2. Registrieren Sie den Dienstclient per DI in `ConfigureServices`, und vergessen Sie nicht, `<connection_string>` durch die Verbindungszeichenfolge für Ihre Dienste zu ersetzen.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAzureClients(builder =>
        {
            builder.AddWebPubSubServiceClient(Configuration["Azure:WebPubSub:ConnectionString"], "chat");
        });
    }
    ```
2.  Fügen Sie dem Server in `app.UseEndpoints` eine `/negotiate`-API hinzu, um das Token zu generieren.

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGet("/negotiate", async context =>
        {
            var id = context.Request.Query["id"];
            if (id.Count != 1)
            {
                context.Response.StatusCode = 400;
                await context.Response.WriteAsync("missing user id");
                return;
            }
            var serviceClient = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
            await context.Response.WriteAsync(serviceClient.GetClientAccessUri(userId: id).AbsoluteUri);
        });
    });
    ```

    Dieser Code zum Generieren des Tokens ähnelt dem Code, den wir im [Tutorial zum Veröffentlichen und Abonnieren von Nachrichten](./tutorial-pub-sub-messages.md) verwendet haben – abgesehen davon, dass wir beim Generieren des Tokens ein weiteres Argument (`userId`) übergeben. Mithilfe der Benutzer-ID kann der Client identifiziert werden, damit Sie beim Empfang einer Nachricht wissen, woher die Nachricht stammt.

    Sie können diese API testen, indem Sie `dotnet run --urls http://localhost:8080` ausführen und auf `http://localhost:8080/negotiate?id=<user-id>` zugreifen. Dadurch erhalten Sie die vollständige URL der Azure Web PubSub-Instanz mit einem Zugriffstoken.

3.  Aktualisieren Sie dann `index.html`, indem Sie das folgende Skript einfügen, um das Token vom Server abzurufen und eine Verbindung mit dem Dienst herzustellen.
 
    ```html
    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    Wenn Sie Chrome verwenden, können Sie folgenden Test ausführen: Öffnen Sie die Startseite, und geben Sie Ihren Benutzernamen ein. Drücken Sie F12, um das Fenster mit den Entwicklertools zu öffnen, und wechseln Sie zur Tabelle **Console**. In der Browserkonsole sollte `connected` ausgegeben werden.


# <a name="javascript"></a>[JavaScript](#tab/javascript)

Wir verwenden hierfür [express.js](https://expressjs.com/), ein beliebtes Webframework für node.js.

Erstellen Sie zunächst eine leere express-App.

1.  Installieren Sie express.js.

    ```bash
    npm init -y
    npm install --save express
    ```

2.  Erstellen Sie dann einen express-Server, und speichern Sie ihn unter `server.js`.

    ```javascript
    const express = require('express');

    const app = express();
    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

3.  Erstellen Sie außerdem eine HTML-Datei, und speichern Sie sie als `public/index.html`. Wir verwenden sie später für die Benutzeroberfläche der Chat-App.

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

Sie können den Server testen, indem Sie `node server` ausführen und im Browser auf http://localhost:8080 zugreifen.


Sie erinnern sich vielleicht, dass der Abonnent im [Tutorial zum Veröffentlichen und Abonnieren von Nachrichten](./tutorial-pub-sub-messages.md) eine API im Web PubSub SDK verwendet, um ein Zugriffstoken aus der Verbindungszeichenfolge zu generieren und damit eine Verbindung mit dem Dienst herzustellen. Dies ist für eine reale Anwendung im Allgemeinen keine sichere Vorgehensweise, da die Verbindungszeichenfolge über weitreichende Berechtigungen zum Ausführen von Vorgängen für den Dienst verfügt und deshalb für keinen Client freigegeben werden sollte. Wir ändern diesen Prozess zur Generierung von Zugriffstoken in eine REST-API auf Serverseite. So kann der Client diese API immer dann zur Anforderung eines Zugriffstokens aufrufen, wenn eine Verbindung hergestellt werden muss, ohne die Verbindungszeichenfolge speichern zu müssen.

1.  Installieren Sie das Azure Web PubSub SDK.

    ```bash
    npm install --save @azure/web-pubsub
    ```

2.  Fügen Sie dem Server eine `/negotiate`-API hinzu, um das Token zu generieren.

    ```javascript
    const express = require('express');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    const app = express();
    const hubName = 'chat';
    const port = 8080;

    let serviceClient = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, hubName);

    app.get('/negotiate', async (req, res) => {
      let id = req.query.id;
      if (!id) {
        res.status(400).send('missing user id');
        return;
      }
      let token = await serviceClient.getClientAccessToken({ userId: id });
      res.json({
        url: token.url
      });
    });

    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

    Dieser Code zum Generieren des Tokens ähnelt dem Code, den wir im [Tutorial zum Veröffentlichen und Abonnieren von Nachrichten](./tutorial-pub-sub-messages.md) verwendet haben – abgesehen davon, dass wir beim Generieren des Tokens ein weiteres Argument (`userId`) übergeben. Mithilfe der Benutzer-ID kann der Client identifiziert werden, damit Sie beim Empfang einer Nachricht wissen, woher die Nachricht stammt.

    Führen Sie den folgenden Befehl aus, um diese API zu testen:

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node server
    ```

    Greifen Sie auf `http://localhost:8080/negotiate?id=<user-id>` zu. Dadurch erhalten Sie die vollständige URL der Azure Web PubSub-Instanz mit einem Zugriffstoken.

3.  Aktualisieren Sie dann `index.html` mit dem folgenden Skript, um das Token vom Server abzurufen und eine Verbindung mit dem Dienst herzustellen.
 
    ```html

    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let data = await res.json();
          let ws = new WebSocket(data.url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    Wenn Sie Chrome verwenden, können Sie folgenden Test ausführen: Öffnen Sie die Startseite, und geben Sie Ihren Benutzernamen ein. Drücken Sie F12, um das Fenster mit den Entwicklertools zu öffnen, und wechseln Sie zur Tabelle **Console**. In der Browserkonsole sollte `connected` ausgegeben werden.

# <a name="java"></a>[Java](#tab/java)

Wir verwenden das Webframework [Javalin](https://javalin.io/), um die Webseiten zu hosten und eingehende Anforderungen zu verarbeiten.

1. Verwenden wir zunächst Maven, um eine neue App `webpubsub-tutorial-chat` zu erstellen und zum Ordner *webpubsub-tutorial-chat* zu wechseln:

    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.tutorial --define artifactId=webpubsub-tutorial-chat --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-tutorial-chat
    ```

2. Jetzt fügen wir die Webframeworkabhängigkeit `javalin` dem Knoten `dependencies` in `pom.xml` hinzu:

    * `javalin`: einfaches Webframework für Java
    * `slf4j-simple`: Protokollierung für Java

    ```xml
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

3. Jetzt navigieren wir zum Verzeichnis */src/main/java/com/webpubsub/tutorial*, öffnen die Datei *App.java* im Editor und verwenden `Javalin.create` zum Verarbeiten von statischen Dateien:

    ```java
    package com.webpubsub.tutorial;
    
    import io.javalin.Javalin;
    
    public class App {
        public static void main(String[] args) {
            // start a server
            Javalin app = Javalin.create(config -> {
                config.addStaticFiles("public");
            }).start(8080);
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

4. Wir erstellen eine HTML-Datei und speichern sie in */src/main/resources/public/index.html*. Diese verwenden wir später für die Benutzeroberfläche der Chat-App.

    ```html
    <html>
    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

Sie können den Server testen, indem Sie den folgenden Befehl unter dem Verzeichnis ausführen, das die Datei *pom.xml* enthält, und im Browser auf http://localhost:8080 zugreifen.

```console
mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false
```

Sie erinnern sich vielleicht, dass der Abonnent im [Tutorial zum Veröffentlichen und Abonnieren von Nachrichten](./tutorial-pub-sub-messages.md) eine API im Web PubSub SDK verwendet, um ein Zugriffstoken aus der Verbindungszeichenfolge zu generieren und damit eine Verbindung mit dem Dienst herzustellen. Dies ist für eine reale Anwendung im Allgemeinen keine sichere Vorgehensweise, da die Verbindungszeichenfolge über weitreichende Berechtigungen zum Ausführen von Vorgängen für den Dienst verfügt und deshalb für keinen Client freigegeben werden sollte. Wir ändern diesen Prozess zur Generierung von Zugriffstoken in eine REST-API auf Serverseite. So kann der Client diese API immer dann zur Anforderung eines Zugriffstokens aufrufen, wenn eine Verbindung hergestellt werden muss, ohne die Verbindungszeichenfolge speichern zu müssen.

1. Fügen Sie in `pom.xml` im Knoten `dependencies` die Azure Web PubSub SDK-Abhängigkeit hinzu:

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.6</version>
    </dependency>
    ```

2. Fügen Sie der Datei `App.java` eine `/negotiate`-API hinzu, um das Token zu generieren:

    ```java
    package com.webpubsub.tutorial;
    
    import com.azure.messaging.webpubsub.WebPubSubServiceClient;
    import com.azure.messaging.webpubsub.WebPubSubServiceClientBuilder;
    import com.azure.messaging.webpubsub.models.GetClientAccessTokenOptions;
    import com.azure.messaging.webpubsub.models.WebPubSubClientAccessToken;
    import com.azure.messaging.webpubsub.models.WebPubSubContentType;
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
                String id = ctx.queryParam("id");
                if (id == null) {
                    ctx.status(400);
                    ctx.result("missing user id");
                    return;
                }
                GetClientAccessTokenOptions option = new GetClientAccessTokenOptions();
                option.setUserId(id);
                WebPubSubClientAccessToken token = service.getClientAccessToken(option);
    
                ctx.result(token.getUrl());
                return;
            });
        }
    }
    ```

    Dieser Code zum Generieren des Tokens ähnelt dem Code, den wir im [Tutorial zum Veröffentlichen und Abonnieren von Nachrichten](./tutorial-pub-sub-messages.md) verwendet haben – abgesehen davon, dass wir beim Generieren des Tokens die Methode `setUserId` zum Festlegen der Benutzer-ID aufrufen. Mithilfe der Benutzer-ID kann der Client identifiziert werden, damit Sie beim Empfang einer Nachricht wissen, woher die Nachricht stammt.

    Sie können diese API testen, indem Sie den folgenden Befehl ausführen und dabei `<connection_string>` durch den **ConnectionString**-Wert ersetzen, den Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben. Anschließend greifen Sie auf `http://localhost:8080/negotiate?id=<user-id>` zu. Dadurch erhalten Sie die vollständige URL der Azure Web PubSub-Instanz mit einem Zugriffstoken.

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>'"
    ```

3. Aktualisieren Sie dann `index.html` mit dem folgenden Skript, um das Token vom Server abzurufen und eine Verbindung mit dem Dienst herzustellen.

    ```html
    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    Wenn Sie Chrome verwenden, können Sie folgenden Test ausführen: Öffnen Sie die Startseite, und geben Sie Ihren Benutzernamen ein. Drücken Sie F12, um das Fenster mit den Entwicklertools zu öffnen, und wechseln Sie zur Tabelle **Console**. In der Browserkonsole sollte `connected` ausgegeben werden.

---

## <a name="handle-events"></a>Behandeln von Ereignissen

Wenn in Azure Web PubSub bestimmte clientseitige Aktivitäten ausgeführt werden (wenn z. B. ein Client verbunden oder getrennt wird), sendet der Dienst Benachrichtigungen an den Server, damit dieser auf die Ereignisse reagieren kann.

Ereignisse werden in Form eines Webhooks an den Server übermittelt. Der Webhook wird vom Anwendungsserver verarbeitet und verfügbar gemacht und auf der Dienstseite von Azure Web PubSub registriert. Der Dienst ruft die Webhooks immer dann auf, wenn ein Ereignis eintritt.

Azure Web PubSub beschreibt die Ereignisdaten gemäß [CloudEvents](./reference-cloud-events.md). 

# <a name="c"></a>[C#](#tab/csharp)
Im Moment müssen Sie den Ereignishandler selbst in C# implementieren. Die Schritte sind einfach und folgen [der Protokollspezifikation](./reference-cloud-events.md), siehe unten.

1. Fügen Sie Ereignishandler in `UseEndpoints` ein. Geben Sie den Endpunktpfad für die Ereignisse an, beispielsweise `/eventhandler`. 

2. Zuerst möchten wir die OPTIONS-Anforderungen für den Missbrauchsschutz verarbeiten. Wir überprüfen, ob der Header `WebHook-Request-Origin` enthält und geben den Header `WebHook-Allowed-Origin` zurück. Der Einfachheit halber geben wir in diesem Beispiel `*` zurück, um alle Ursprünge zuzulassen.
    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler/{*path}", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

3. Anschließend möchten wir überprüfen, ob es sich bei den eingehenden Anforderungen um die erwarteten Ereignisse handelt. Angenommen, uns interessiert das Systemereignis `connected`, das für den `ce-type`-Header `azure.webpubsub.sys.connected` enthalten soll. Wir fügen die Logik nach dem Missbrauchsschutz hinzu:
    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler/{*path}", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

Im obigen Code geben wir einfach eine Nachricht an die Konsole aus, wenn ein Client verbunden ist. Hierbei verwenden wir `context.Request.Headers["ce-userId"]`, um die Identität des verbundenen Clients anzuzeigen.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Wenn Sie das Web PubSub SDK verwenden, ist bereits eine Implementierung zum Analysieren und Verarbeiten des CloudEvents-Schemas vorhanden, sodass Sie sich nicht mit diesen Details befassen müssen.

Fügen Sie den folgenden Code hinzu, um (durch die express-Middleware, die vom Web PubSub SDK bereitgestellt wird) eine REST-API unter `/eventhandler` verfügbar zu machen, um das mit dem Client verbundene Ereignis zu verarbeiten:

```bash
npm install --save @azure/web-pubsub-express@1.0.0-alpha.20211102.4
```

```javascript
const { WebPubSubEventHandler } = require('@azure/web-pubsub-express');

let handler = new WebPubSubEventHandler(hubName, {
  path: '/eventhandler',
  onConnected: async req => {
    console.log(`${req.context.userId} connected`);
  }
});

app.use(handler.getMiddleware());
```

Im obigen Code geben wir einfach eine Nachricht an die Konsole aus, wenn ein Client verbunden ist. Hierbei verwenden wir `req.context.userId`, um die Identität des verbundenen Clients anzuzeigen.

# <a name="java"></a>[Java](#tab/java)
Im Moment müssen Sie den Ereignishandler selbst in Java implementieren. Die Schritte sind einfach und folgen [der Protokollspezifikation](./reference-cloud-events.md), siehe unten.

1. Fügen Sie einen HTTP-Handler für den Ereignishandlerpfad hinzu, z. B. `/eventhandler`. 

2. Zuerst möchten wir die OPTIONS-Anforderungen für den Missbrauchsschutz verarbeiten. Wir überprüfen, ob der Header `WebHook-Request-Origin` enthält und geben den Header `WebHook-Allowed-Origin` zurück. Der Einfachheit halber geben wir in diesem Beispiel `*` zurück, um alle Ursprünge zuzulassen.
    ```java
    
    // validation: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#validation
    app.options("/eventhandler", ctx -> {
        ctx.header("WebHook-Allowed-Origin", "*");
    });
    ```

3. Anschließend möchten wir überprüfen, ob es sich bei den eingehenden Anforderungen um die erwarteten Ereignisse handelt. Angenommen, uns interessiert das Systemereignis `connected`, das für den `ce-type`-Header `azure.webpubsub.sys.connected` enthalten soll. Wir fügen die Logik nach dem Missbrauchsschutz hinzu:
    ```java
    // validation: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#validation
    app.options("/eventhandler", ctx -> {
        ctx.header("WebHook-Allowed-Origin", "*");
    });

    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            System.out.println(id + " connected.");
        }
        ctx.status(200);
    });

    ```

Im obigen Code geben wir einfach eine Nachricht an die Konsole aus, wenn ein Client verbunden ist. Hierbei verwenden wir `ctx.header("ce-userId")`, um die Identität des verbundenen Clients anzuzeigen.

---

## <a name="set-up-the-event-handler"></a>Einrichten des Ereignishandlers

### <a name="expose-localhost"></a>Verfügbarmachen des Localhost

Anschließend müssen wir im Dienst die Webhook-URL festlegen, die im Falle eines neuen Ereignisses aufgerufen werden soll. Unser Server wird jedoch auf dem Localhost ausgeführt, deshalb ist kein Endpunkt vorhanden, über den auf das Internet zugegriffen werden kann. In diesem Beispiel verwenden wir [ngrok](https://ngrok.com/), um unseren Localhost für das Internet verfügbar zu machen.

1.  Laden Sie ngrok zunächst über https://ngrok.com/download herunter, und extrahieren Sie die ausführbare Datei in Ihrem lokalen Ordner oder im Systemordner „bin“.
2.  ngrok starten
    
    ```bash
    ngrok http 8080
    ```

ngrok gibt eine URL (`https://<domain-name>.ngrok.io`) aus, auf die über das Internet zugegriffen werden kann.

### <a name="set-event-handler"></a>Einrichten des Ereignishandlers

Anschließend aktualisieren wir den Dienstereignishandler und legen die Webhook-URL fest.

Verwenden Sie den Azure CLI-Befehl [az webpubsub hub create](/cli/azure/webpubsub/hub#az_webpubsub_hub_update), um die Ereignishandlereinstellungen für den Chathub zu aktualisieren:

  > [!Important]
  > Ersetzen Sie &lt;your-unique-resource-name&gt; durch den Namen Ihrer Web PubSub-Instanz, die in den vorherigen Schritten erstellt wurde.
  > Ersetzen Sie &lt;domain-name&gt; durch den von ngrok ausgegebenen Namen.

```azurecli-interactive
az webpubsub hub create -n "<your-unique-resource-name>" -g "myResourceGroup" --hub-name "chat" --event-handler url-template="https://<domain-name>.ngrok.io/eventHandler" user-event-pattern="*" system-event="connected"
```

Öffnen Sie nach Abschluss der Aktualisierung die Startseite http://localhost:8080/index.html, und geben Sie Ihren Benutzernamen ein. Daraufhin wird in der Konsole die Meldung zur Verbindungsherstellung angezeigt.

## <a name="handle-message-events"></a>Verarbeiten von Nachrichtenereignissen

Neben Systemereignissen wie `connected` oder `disconnected` kann der Client auch Nachrichten über die WebSocket-Verbindung senden, und diese Nachrichten werden als spezieller Ereignistyp namens `message` an den Server übermittelt. Wir können dieses Ereignis verwenden, um Nachrichten von einem Client zu empfangen und an alle Clients zu übertragen, sodass diese miteinander kommunizieren können.

# <a name="c"></a>[C#](#tab/csharp)

Der `ce-type` des `message`-Ereignisses lautet immer `azure.webpubsub.user.message`, siehe Details unter [Ereignis „message“](./reference-cloud-events.md#message).

1. Verarbeiten Sie das Nachrichtenereignis.

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler/{*path}", async context =>
        {
            var serviceClient = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    context.Response.StatusCode = 200;
                    return;
                }
                else if (context.Request.Headers["ce-type"] == "azure.webpubsub.user.message")
                {
                    using var stream = new StreamReader(context.Request.Body);
                    await serviceClient.SendToAllAsync($"[{userId}] {await stream.ReadToEndAsync()}");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

    Dieser Ereignishandler verwendet `WebPubSubServiceClient.SendToAllAsync()`, um die empfangene Nachricht an alle Clients zu übertragen.

2.  Aktualisieren Sie `index.html`, um die Logik zum Senden von Nachrichten vom Benutzer an den Server und zum Anzeigen empfangener Nachrichten auf der Seite hinzuzufügen.

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
      <input id="message" placeholder="Type to chat...">
      <div id="messages"></div>
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');

          let messages = document.querySelector('#messages');
          ws.onmessage = event => {
            let m = document.createElement('p');
            m.innerText = event.data;
            messages.appendChild(m);
          };

          let message = document.querySelector('#message');
          message.addEventListener('keypress', e => {
            if (e.charCode !== 13) return;
            ws.send(message.value);
            message.value = '';
          });
        })();
      </script>
    </body>

    </html>
    ```

    Sie können im obigen Code sehen, dass wir `WebSocket.send()` zum Senden von Nachrichten und `WebSocket.onmessage` zum Lauschen auf Nachrichten vom Dienst verwenden.

3.  Aktualisieren Sie abschließend den `onConnected`-Handler, um das connected-Ereignis an alle Clients zu übertragen. So wird angezeigt, wer dem Chatraum beigetreten ist.

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler/{*path}", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    await serviceClient.SendToAllAsync($"[SYSTEM] {userId} joined.");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

Führen Sie nun den Server mithilfe von `dotnet run --urls http://localhost:8080` aus, und öffnen Sie mehrere Browserinstanzen, um auf http://localhost:8080/index.html zuzugreifen. Anschließend können Sie miteinander chatten.

Das vollständige Codebeispiel für dieses Tutorial finden Sie [hier][code-csharp].

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. Fügen Sie einen neuen `handleUserEvent`-Handler hinzu.

    ```javascript
    let handler = new WebPubSubEventHandler(hubName, {
      path: '/eventhandler',
      onConnected: async req => {
        console.log(`${req.context.userId} connected`);
      },
      handleUserEvent: async (req, res) => {
        if (req.context.eventName === 'message') 
            await serviceClient.sendToAll(`[${req.context.userId}] ${req.data}`, { contentType: 'text/plain' });
        res.success();
      }
    });
    ```

    Dieser Ereignishandler verwendet `WebPubSubServiceClient.sendToAll()`, um die empfangene Nachricht an alle Clients zu übertragen.

    `handleUserEvent` verwendet außerdem ein `res`-Objekt, mit dem Sie eine Nachricht an den Absender des Ereignisses zurücksenden können. Hier rufen wir einfach `res.success()` auf, damit der WebHook 200 zurückgibt. (Beachten Sie, dass dieser Aufruf selbst dann erforderlich ist, wenn Sie nichts an den Client zurückgeben möchten, da der Webhook andernfalls keine Rückgabe durchführt und die Clientverbindung geschlossen wird.)

2.  Aktualisieren Sie `index.html`, um die Logik zum Senden von Nachrichten vom Benutzer an den Server und zum Anzeigen empfangener Nachrichten auf der Seite hinzuzufügen.

    ```html
    <html>

      <body>
        <h1>Azure Web PubSub Chat</h1>
        <input id="message" placeholder="Type to chat...">
        <div id="messages"></div>
        <script>
          (async function () {
            let id = prompt('Please input your user name');
            let res = await fetch(`/negotiate?id=${id}`);
            let data = await res.json();
            let ws = new WebSocket(data.url);
            ws.onopen = () => console.log('connected');
  
            let messages = document.querySelector('#messages');
            ws.onmessage = event => {
              let m = document.createElement('p');
              m.innerText = event.data;
              messages.appendChild(m);
            };
  
            let message = document.querySelector('#message');
            message.addEventListener('keypress', e => {
              if (e.charCode !== 13) return;
              ws.send(message.value);
              message.value = '';
            });
          })();
        </script>
      </body>
  
      </html>
    ```

    Sie können im obigen Code sehen, dass wir `WebSocket.send()` zum Senden von Nachrichten und `WebSocket.onmessage` zum Lauschen auf Nachrichten vom Dienst verwenden.

3. `sendToAll` akzeptiert ein Objekt als Eingabe und sendet JSON-Text an die Clients. In einem realen Szenario benötigen wir wahrscheinlich ein komplexes Objekt, um mehr Informationen über die Nachricht zu erhalten. Aktualisieren Sie abschließend die Handler, um JSON-Objekte an alle Clients zu übertragen:

    ```javascript
    let handler = new WebPubSubEventHandler(hubName, {
      path: '/eventhandler',
      onConnected: async req => {
        console.log(`${req.context.userId} connected`);
        await serviceClient.sendToAll({
          type: "system",
          message: `${req.context.userId} joined`
        });
      },
      handleUserEvent: async (req, res) => {
        if (req.context.eventName === 'message') {
          await serviceClient.sendToAll({
            from: req.context.userId,
            message: req.data
          });
        }
        res.success();
      }
    });
    ```

4. Aktualisieren Sie den Client, um JSON-Daten zu analysieren:
    ```html
    <html>

      <body>
        <h1>Azure Web PubSub Chat</h1>
        <input id="message" placeholder="Type to chat...">
        <div id="messages"></div>
        <script>
          (async function () {
            let id = prompt('Please input your user name');
            let res = await fetch(`/negotiate?id=${id}`);
            let data = await res.json();
            let ws = new WebSocket(data.url);
            ws.onopen = () => console.log('connected');
  
            let messages = document.querySelector('#messages');
            
            ws.onmessage = event => {
              let m = document.createElement('p');
              let data = JSON.parse(event.data);
              m.innerText = `[${data.type || ''}${data.from || ''}] ${data.message}`;
              messages.appendChild(m);
            };
  
            let message = document.querySelector('#message');
            message.addEventListener('keypress', e => {
              if (e.charCode !== 13) return;
              ws.send(message.value);
              message.value = '';
            });
          })();
        </script>
      </body>
  
    </html>
    ```

Führen Sie nun den Server aus, und öffnen Sie mehrere Browserinstanzen. Anschließend können Sie miteinander chatten.

Das vollständige Codebeispiel für dieses Tutorial finden Sie [hier][code-js].

# <a name="java"></a>[Java](#tab/java)

Der `ce-type` des `message`-Ereignisses lautet immer `azure.webpubsub.user.message`, siehe Details unter [Ereignis „message“](./reference-cloud-events.md#message).

1. Verarbeiten Sie das Nachrichtenereignis.

    ```java
    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            System.out.println(id + " connected.");
        } else if ("azure.webpubsub.user.message".equals(event)) {
            String id = ctx.header("ce-userId");
            String message = ctx.body();
            service.sendToAll(String.format("[%s] %s", id, message), WebPubSubContentType.TEXT_PLAIN);
        }
        ctx.status(200);
    });
    ```

    Dieser Ereignishandler verwendet `client.sendToAll()`, um die empfangene Nachricht an alle Clients zu übertragen.

2.  Aktualisieren Sie `index.html`, um die Logik zum Senden von Nachrichten vom Benutzer an den Server und zum Anzeigen empfangener Nachrichten auf der Seite hinzuzufügen.

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
      <input id="message" placeholder="Type to chat...">
      <div id="messages"></div>
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');

          let messages = document.querySelector('#messages');
          ws.onmessage = event => {
            let m = document.createElement('p');
            m.innerText = event.data;
            messages.appendChild(m);
          };

          let message = document.querySelector('#message');
          message.addEventListener('keypress', e => {
            if (e.charCode !== 13) return;
            ws.send(message.value);
            message.value = '';
          });
        })();
      </script>
    </body>

    </html>
    ```

    Sie können im obigen Code sehen, dass wir `WebSocket.send()` zum Senden von Nachrichten und `WebSocket.onmessage` zum Lauschen auf Nachrichten vom Dienst verwenden.

3.  Aktualisieren Sie abschließend den `connected`-Ereignishandler, um das connected-Ereignis an alle Clients zu übertragen. So wird angezeigt, wer dem Chatraum beigetreten ist.

    ```java

    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            service.sendToAll(String.format("[SYSTEM] %s joined", id), WebPubSubContentType.TEXT_PLAIN);
        } else if ("azure.webpubsub.user.message".equals(event)) {
            String id = ctx.header("ce-userId");
            String message = ctx.body();
            service.sendToAll(String.format("[%s] %s", id, message), WebPubSubContentType.TEXT_PLAIN);
        }
        ctx.status(200);
    });

    ```

Führen Sie nun mit dem nachstehenden Befehl den Server aus, und öffnen Sie mehrere Browserinstanzen. Anschließend können Sie miteinander chatten.

```console
mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>'"
```

Das vollständige Codebeispiel für dieses Tutorial finden Sie [hier][code-java].

---

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine grundlegende Vorstellung davon erhalten, wie das Ereignissystem im Azure Web PubSub-Dienst funktioniert.

Bearbeiten Sie weitere Tutorials, um mehr über die Nutzung des Diensts zu erfahren.

> [!div class="nextstepaction"]
> [Weitere Beispiele für Azure Web PubSub](https://aka.ms/awps/samples)


[code-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp/
[code-java]: https://github.com/Azure/azure-webpubsub/tree/main/samples/java/chatapp/
[code-csharp]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp/chatapp/
