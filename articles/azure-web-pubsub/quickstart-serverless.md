---
title: 'Tutorial: Erstellen einer serverlosen Echtzeit-Chat-App mit Clientauthentifizierung'
description: In diesem Tutorial erfahren Sie, wie Sie mit dem Azure Web PubSub-Dienst und Azure Functions eine serverlose Chat-App mit Clientauthentifizierung erstellen.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/08/2021
ms.openlocfilehash: 7dc376bb84c52688e1f665501680f11f6bb317eb
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488523"
---
# <a name="tutorial-create-a-serverless-real-time-chat-app-with-azure-functions-and-azure-web-pubsub-service"></a>Tutorial: Erstellen einer serverlosen Echtzeit-Chat-App mit Azure Functions und dem Azure Web PubSub-Dienst

Der Azure Web PubSub-Dienst unterstützt Sie beim einfachen Erstellen von Echtzeit-Messagingwebanwendungen mithilfe von WebSockets und des Veröffentlichen-Abonnieren-Musters. Azure Functions ist eine serverlose Plattform, mit der Sie Ihren Code ohne Verwaltung von Infrastruktur ausführen können. In diesem Tutorial erfahren Sie, wie Sie den Azure Web PubSub-Dienst und Azure Functions verwenden, um eine serverlose Anwendung mit Echtzeitmessaging und dem Veröffentlichen-Abonnieren-Muster zu erstellen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer serverlosen Echtzeit-Chat-App
> * Arbeiten mit Web PubSub-Funktionstrigger- und -ausgabebindungen
> * Bereitstellen der Funktion in der Azure-Funktions-App
> * Konfigurieren der Azure-Authentifizierung
> * Konfigurieren des Web PubSub-Ereignishandlers zum Weiterleiten von Ereignissen und Nachrichten an die Anwendung

## <a name="prerequisites"></a>Voraussetzungen

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* Ein Code-Editor wie [Visual Studio Code](https://code.visualstudio.com/)

* [Node.js](https://nodejs.org/en/download/), Version 10.x
   > [!NOTE]
   > Weitere Informationen zu den unterstützten Versionen von Node.js finden Sie in der [Übersicht über die Runtimeversionen von Azure Functions](../azure-functions/functions-versions.md#languages).
* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (v3 oder höher bevorzugt), um Azure-Funktions-Apps lokal auszuführen und in Azure bereitstellen zu können.

* [Azure-Befehlszeilenschnittstelle (Azure CLI)](/cli/azure) zum Verwalten von Azure-Ressourcen.

* (Optional) [ngrok](https://ngrok.com/download), um die lokale Funktion als Ereignishandler für den Web PubSub-Dienst verfügbar zu machen. Dies ist nur zum lokalen Ausführen der Funktions-App optional.

# <a name="c"></a>[C#](#tab/csharp)

* Ein Code-Editor wie [Visual Studio Code](https://code.visualstudio.com/).

* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (v3 oder höher bevorzugt), um Azure-Funktions-Apps lokal auszuführen und in Azure bereitstellen zu können.

* [Azure-Befehlszeilenschnittstelle (Azure CLI)](/cli/azure) zum Verwalten von Azure-Ressourcen.

* (Optional) [ngrok](https://ngrok.com/download), um die lokale Funktion als Ereignishandler für den Web PubSub-Dienst verfügbar zu machen. Dies ist nur zum lokalen Ausführen der Funktions-App optional.

---

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="create-the-functions"></a>Erstellen der Funktionen

1. Stellen Sie sicher, dass [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) installiert ist. Erstellen Sie dann ein leeres Verzeichnis für das Projekt. Führen Sie den Befehl unter diesem Arbeitsverzeichnis aus.

    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    ```bash
    func init --worker-runtime javascript
    ```

    # <a name="c"></a>[C#](#tab/csharp)
    ```bash
    func init --worker-runtime dotnet
    ```

2. *Installieren Sie das `Microsoft.Azure.WebJobs.Extensions.WebPubSub`-Funktionserweiterungspaket.

    > [!NOTE]
    > Der Schritt ist optional, wenn [Erweiterungsbündel](/azure/azure-functions/functions-bindings-register#extension-bundles) unterstützt werden.

   a. Entfernen Sie den Abschnitt `extensionBundle` in `host.json`, um die Installation eines bestimmten Erweiterungspakets im nächsten Schritt zu ermöglichen. Sie können den JSON-Code für den Host auch so einfach wie unten gestalten.
    ```json
    {
        "version": "2.0"
    }
    ```
   b. Führen Sie den Befehl zum Installieren eines bestimmten Funktionserweiterungspakets aus.
    ```bash
    func extensions install --package Microsoft.Azure.WebJobs.Extensions.WebPubSub --version 1.0.0
    ```

3. Erstellen Sie eine `index`-Funktion zum Lesen und Hosten einer statischen Webseite für Clients.
    ```bash
    func new -n index -t HttpTrigger
    ```
   # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - Aktualisieren Sie `index/function.json` und kopieren Sie den folgenden JSON-Code.
        ```json
        {
            "bindings": [
                {
                    "authLevel": "anonymous",
                    "type": "httpTrigger",
                    "direction": "in",
                    "name": "req",
                    "methods": [
                      "get",
                      "post"
                    ]
                },
                {
                    "type": "http",
                    "direction": "out",
                    "name": "res"
                }
            ]
        }
        ```
   - Aktualisieren Sie `index/index.js` und kopieren Sie den folgenden Code.
        ```js
        var fs = require('fs');
        var path = require('path');

        module.exports = function (context, req) {
            var index = 'index.html';
            if (process.env["HOME"] != null)
            {
                index = path.join(process.env["HOME"], "site", "wwwroot", index);
            }
            context.log("index.html path: " + index);
            fs.readFile(index, 'utf8', function (err, data) {
                if (err) {
                console.log(err);
                context.done(err);
                }
                context.res = {
                status: 200,
                headers: {
                    'Content-Type': 'text/html'
                },
                body: data
                };
                context.done();
            });
        }
        ```

   # <a name="c"></a>[C#](#tab/csharp)
   - Aktualisieren Sie `index.cs` und ersetzen Sie die `Run`-Funktion durch den folgenden Code.
        ```c#
        [FunctionName("index")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
        {
            string indexFile = "index.html";
            if (Environment.GetEnvironmentVariable("HOME") != null)
            {
                indexFile = Path.Join(Environment.GetEnvironmentVariable("HOME"), "site", "wwwroot", indexFile);
            }
            log.LogInformation($"index.html path: {indexFile}.");
            return new ContentResult
            {
                Content = File.ReadAllText(indexFile),
                ContentType = "text/html",
            };
        }
        ```

4. Erstellen Sie eine `negotiate`-Funktion, damit Clients die Dienstverbindungs-URL mit Zugriffstoken abrufen können.
    ```bash
    func new -n negotiate -t HttpTrigger
    ```
    > [!NOTE]
    > In diesem Beispiel wird der [AAD](../app-service/configure-authentication-user-identities.md)-Header der Benutzeridentität `x-ms-client-principal-name` zum Abrufen von `userId` verwendet. Dies funktioniert in einer lokalen Funktion nicht. Sie können ihn leer lassen oder auf andere Weise ändern, um `userId` abzurufen oder zu generieren, wenn Sie lokal agieren. Lassen Sie den Client z. B. einen Benutzernamen eingeben und ihn in einer Abfrage wie `?user={$username}` übergeben, wenn Sie die `negotiate`-Funktion aufrufen, um die URL für die Verbindung mit dem Dienst zu erhalten. Legen Sie in der `negotiate`-Funktion `userId` auf den Wert `{query.user}` fest.
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - Aktualisieren Sie `negotiate/function.json` und kopieren Sie den folgenden JSON-Code.
        ```json
        {
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
                    "hub": "simplechat",
                    "userId": "{headers.x-ms-client-principal-name}",
                    "direction": "in"
                }
            ]
        }
        ```
   - Aktualisieren Sie `negotiate/index.js` und kopieren Sie den folgenden Code.
        ```js
        module.exports = function (context, req, connection) {
            context.res = { body: connection };
            context.done();
        };
        ```
   # <a name="c"></a>[C#](#tab/csharp)
   - Aktualisieren Sie `negotiate.cs` und ersetzen Sie die `Run`-Funktion durch den folgenden Code.
        ```c#
        [FunctionName("negotiate")]
        public static WebPubSubConnection Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            [WebPubSubConnection(Hub = "simplechat", UserId = "{headers.x-ms-client-principal-name}")] WebPubSubConnection connection,
            ILogger log)
        {
            log.LogInformation("Connecting...");
            return connection;
        }
        ```

5. Erstellen Sie eine `message`-Funktion, um Clientnachrichten über den Dienst zu übertragen.
   ```bash
   func new -n message -t HttpTrigger
   ```

   > [!NOTE]
   > Diese Funktion verwendet tatsächlich `WebPubSubTrigger`. Da sich der Dienst jedoch noch in der Vorschau befindet, ist `WebPubSubTrigger` nicht in die Funktionsvorlage integriert. Wir verwenden `HttpTrigger`, um die Funktionsvorlage zu initialisieren und den Triggertyp im Code zu ändern.

   # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - Aktualisieren Sie `message/function.json` und kopieren Sie den folgenden JSON-Code.
        ```json
        {
            "bindings": [
                {
                    "type": "webPubSubTrigger",
                    "direction": "in",
                    "name": "data",
                    "hub": "simplechat",
                    "eventName": "message",
                    "eventType": "user"
                },
                {
                    "type": "webPubSub",
                    "name": "actions",
                    "hub": "simplechat",
                    "direction": "out"
                }
            ]
        }
        ```
   - Aktualisieren Sie `message/index.js` und kopieren Sie den folgenden Code.
        ```js
        module.exports = async function (context, data) {
            context.bindings.actions = {
                "actionName": "sendToAll",
                "data": `[${context.bindingData.request.connectionContext.userId}] ${data}`,
                "dataType": context.bindingData.dataType
            };
            // UserEventResponse directly return to caller
            var response = { 
                "data": '[SYSTEM] ack.',
                "dataType" : "text"
            };
            return response;
        };
        ```

   # <a name="c"></a>[C#](#tab/csharp)
   - Aktualisieren Sie `message.cs` und ersetzen Sie die `Run`-Funktion durch den folgenden Code.
        ```c#
        [FunctionName("message")]
        public static async Task<UserEventResponse> Run(
            [WebPubSubTrigger(WebPubSubEventType.User, "message")] UserEventRequest request,
            BinaryData data,
            WebPubSubDataType dataType,
            [WebPubSub(Hub = "simplechat")] IAsyncCollector<WebPubSubAction> actions)
        {
            await actions.AddAsync(WebPubSubAction.CreateSendToAllAction(
                BinaryData.FromString($"[{request.ConnectionContext.UserId}] {message.ToString()}"),
                dataType
            );
            return new UserEventResponse
            {
                Data = BinaryData.FromString("[SYSTEM] ack"),
                DataType = WebPubSubDataType.Text
            };
        }
        ```

6. Fügen Sie die Client-Einzelseite `index.html` im Stammordner des Projekts hinzu, und kopieren Sie den unten gezeigten Inhalt.
    ```html
    <html>
        <body>
            <h1>Azure Web PubSub Serverless Chat App</h1>
            <div id="login"></div>
            <p></p>
            <input id="message" placeholder="Type to chat...">
            <div id="messages"></div>
            <script>
                (async function () {
                    let authenticated = window.location.href.includes('?authenticated=true');
                    if (!authenticated) {
                        // auth
                        let login = document.querySelector("#login");
                        let link = document.createElement('a');
                        link.href = `${window.location.origin}/.auth/login/aad?post_login_redirect_url=/api/index?authenticated=true`;
                        link.text = "login";
                        login.appendChild(link);
                    }
                    else {
                        // negotiate
                        let messages = document.querySelector('#messages');
                        let res = await fetch(`${window.location.origin}/api/negotiate`, {
                            credentials: "include"
                        });
                        let url = await res.json();
                        // connect
                        let ws = new WebSocket(url.url);
                        ws.onopen = () => console.log('connected');
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
                    }
                })();
            </script>
        </body>
    </html>
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    # <a name="c"></a>[C#](#tab/csharp)
    Da das C#-Projekt Dateien in einen anderen Ausgabeordner kompiliert, müssen Sie Ihre `*.csproj` aktualisieren, damit die Inhaltsseite enthalten ist.
    ```xml
    <ItemGroup>
        <None Update="index.html">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ``

## Create and Deploy the Azure Function App

Before you can deploy your function code to Azure, you need to create 3 resources:
* A resource group, which is a logical container for related resources.
* A storage account, which is used to maintain state and other information about your functions.
* A function app, which provides the environment for executing your function code. A function app maps to your local function project and lets you group functions as a logical unit for easier management, deployment and sharing of resources.

Use the following commands to create these items. 

1. If you haven't done so already, sign in to Azure:

    ```bash
    az login
    ```

1. Erstellen Sie eine Ressourcengruppe. Alternativ können Sie eine Ressourcengruppe des Azure Web PubSub-Diensts verwenden:

    ```bash
    az group create -n WebPubSubFunction -l <REGION>
    ```

1. Erstellen Sie in Ihrer Ressourcengruppe und Region ein universelles Speicherkonto:

    ```bash
    az storage account create -n <STORAGE_NAME> -l <REGION> -g WebPubSubFunction
    ```

1. Erstellen Sie die Funktions-App in Azure:

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    ```bash
    az functionapp create --resource-group WebPubSubFunction --consumption-plan-location <REGION> --runtime node --runtime-version 12 --functions-version 3 --name <FUNCIONAPP_NAME> --storage-account <STORAGE_NAME>
    ```

    # <a name="c"></a>[C#](#tab/csharp)

    ```bash
    az functionapp create --resource-group WebPubSubFunction --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --name <FUNCIONAPP_NAME> --storage-account <STORAGE_NAME>
    ```

1. Stellen Sie das Funktionsprojekts in Azure bereit:

    Nachdem Sie Ihre Funktions-App in Azure erfolgreich erstellt haben, können Sie nun Ihr lokales Funktionsprojekt bereitstellen, indem Sie den Befehl [func azure functionapp publish](./../azure-functions/functions-run-local.md) verwenden.

    ```bash
    func azure functionapp publish <FUNCIONAPP_NAME>
    ```
1. Konfigurieren Sie `WebPubSubConnectionString` für die Funktions-App:

   Suchen Sie zuerst Ihre Web PubSub-Ressource im **Azure-Portal**, und kopieren Sie die Verbindungszeichenfolge unter **Schlüssel**. Darüber hinaus können Sie die Einstellungen für die Funktions-App in **Azure-Portal** -> **Einstellungen** -> **Aktualisieren** aktualisieren. Fügen Sie unter **Anwendungseinstellungen** ein neues Element mit dem Namen und dem Wert `WebPubSubConnectionString` ihrer Web PubSub-Ressourcenverbindungszeichenfolge hinzu.

## <a name="configure-the-web-pubsub-service-event-handler"></a>Konfigurieren des Web PubSub-Diensts `Event Handler`

In diesem Beispiel verwenden Sie `WebPubSubTrigger`, um auf Anforderungen des Diensts für Upstreams zu lauschen. Web PubSub muss also die Endpunktinformationen der Funktion kennen, um die Clientanforderungen senden zu können. Die Azure Functions-App benötigt zudem einen Systemschlüssel für Sicherheit bei erweiterungsspezifischen Webhookmethoden. Nachdem wir im vorherigen Schritt die Funktions-App mit `message`-Funktionen bereitgestellt haben, können wir den Systemschlüssel abrufen.

Wechseln Sie zum **Azure-Portal**. Wählen Sie Ihre Funktions-App-Ressource und dann **App-Schlüssel** -> **Systemschlüssel** ->  **`webpubsub_extension`** aus. Kopieren Sie den Wert von `<APP_KEY>`.

:::image type="content" source="media/quickstart-serverless/func-keys.png" alt-text="Screenshot des Abrufens der Systemschlüssel der Funktion.":::

Legen Sie `Event Handler` im Azure Web PubSub-Dienst fest. Navigieren Sie im **Azure-Portal** zu Ihrer Web PubSub-Ressource, und wählen Sie **Einstellungen** aus. Fügen Sie eine neue Hubeinstellungszuordnung für die einzelne verwendete Funktion hinzu, und verwenden Sie dabei die folgenden Angaben. Ersetzen Sie die `<FUNCTIONAPP_NAME>` und `<APP_KEY>` durch Ihre Werte.

   - Hub-Name: `simplechat`
   - URL-Vorlage: **https://<FUNCTIONAPP_NAME>.azurewebsites.net/runtime/webhooks/webpubsub?code=<APP_KEY>**
   - Benutzerereignismuster: *
   - Systemereignisse: (bei diesem Beispiel keine Konfiguration nötig)

:::image type="content" source="media/quickstart-serverless/set-event-handler.png" alt-text="Screenshot: Festlegen des Ereignishandlers":::

> [!NOTE]
> Wenn Sie die Funktionen lokal ausführen. Sie können die Funktions-URL mit [ngrok](https://ngrok.com/download) durch den Befehl `ngrok http 7071` nach dem Start der Funktion verfügbar machen. Konfigurieren Sie den Web PubSub-Dienst `Event Handler` mit der URL `https://<NGROK_ID>.ngrok.io/runtime/webhooks/webpubsub`. 

## <a name="configure-to-enable-client-authentication"></a>Konfiguration zum Aktivieren der Clientauthentifizierung

Wechseln Sie zum **Azure-Portal**. Wählen Sie Ihre Funktions-App-Ressource und dann **Authentifizierung** aus. Klicken Sie unten auf der Seite auf **`Add identity provider`** . Legen Sie **Einstellungen für die App Service-Authentifizierung** auf **Nicht authentifizierten Zugriff zulassen** fest, damit die Indexseite Ihres Clients von anonymen Benutzern besucht werden kann, bevor sie zur Authentifizierung umgeleitet werden. **Speichern** Sie anschließend.

Hier wählen wir `Microsoft` als Bezeichner des Anbieters, der `x-ms-client-principal-name` als `userId` in der `negotiate`-Funktion verwendet. Sie können auch andere Identitätsanbieter konfigurieren, indem Sie den unten stehenden Links folgen. Vergessen Sie nicht, den Wert `userId` in der `negotiate`-Funktion entsprechend zu aktualisieren.

* [Microsoft (Azure AD)](../app-service/configure-authentication-provider-aad.md)
* [Facebook](../app-service/configure-authentication-provider-facebook.md)
* [Google](../app-service/configure-authentication-provider-google.md)
* [Twitter](../app-service/configure-authentication-provider-twitter.md)

## <a name="try-the-application"></a>Testen der Anwendung

Nun können Sie Ihre Seite in Ihrer Funktions-App `https://<FUNCTIONAPP_NAME>.azurewebsites.net/api/index` testen. Siehe die folgende Momentaufnahme.
1. Klicken Sie auf `login`, um sich zu authentifizieren.
2. Geben Sie die Nachricht in das Eingabefeld ein, um zu chatten.

In der Nachrichtenfunktion wird die Nachricht des Aufrufers an alle Clients gesendet. Der Aufrufer erhält die Nachricht `[SYSTEM] ack` zurück. In der nachstehenden Momentaufnahme des Beispielchats stammen die ersten 4 Nachrichten vom aktuellen Client und die letzten 2 Nachrichten von einem anderen Client.

:::image type="content" source="media/quickstart-serverless/chat-sample.png" alt-text="Screenshot des Chatbeispiels.":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese App nicht mehr benötigen, löschen Sie alle im Rahmen dieser Dokumentation erstellten Ressourcen, damit keine Gebühren anfallen. Gehen Sie dazu wie folgt vor:

1. Klicken Sie ganz links im Azure-Portal auf **Ressourcengruppen** und anschließend auf die erstellte Ressourcengruppe. Sie können auch das Suchfeld verwenden, um nach dem Namen der Ressourcengruppe zu suchen.

1. Wählen im daraufhin angezeigten Fenster die Ressourcengruppe und anschließend die Option **Ressourcengruppe löschen** aus.

1. Geben Sie im neuen Fenster den Namen der Ressourcengruppe ein, die Sie löschen möchten, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie eine serverlose Benachrichtigungsanwendung ausführen. Sie können nun damit beginnen, Ihre eigene Anwendung zu erstellen. 

> [!div class="nextstepaction"]
> [Azure Web PubSub-Bindungen für Azure Functions](https://azure.github.io/azure-webpubsub/references/functions-bindings)

> [!div class="nextstepaction"]
> [Schnellstart: Erstellen eines einfachen Chatrooms mit Azure Web PubSub](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [Weitere Beispiele für Azure Web PubSub](https://github.com/Azure/azure-webpubsub/tree/main/samples)
