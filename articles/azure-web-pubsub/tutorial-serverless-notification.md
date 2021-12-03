---
title: 'Tutorial: Erstellen einer serverlosen Benachrichtigungs-App mit dem Azure Web PubSub-Dienst und Azure Functions'
description: In diesem Tutorial erfahren Sie, wie Sie mit dem Azure Web PubSub-Dienst und Azure Functions eine serverlose Benachrichtigungsanwendung erstellen.
author: JialinXin
ms.author: jixin
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/01/2021
ms.openlocfilehash: 3fb4c5dbbc8ea073962cd7e0edb3e53c4c9920d5
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494024"
---
# <a name="tutorial-create-a-serverless-notification-app-with-azure-functions-and-azure-web-pubsub-service"></a>Tutorial: Erstellen einer serverlosen Benachrichtigungs-App mit Azure Functions und dem Azure Web PubSub-Dienst

Der Azure Web PubSub-Dienst ermöglicht die Erstellung von Webanwendungen für Echtzeit-Messaging mithilfe von WebSockets. Azure Functions ist eine serverlose Plattform, mit der Sie Ihren Code ohne Verwaltung von Infrastruktur ausführen können. In diesem Tutorial erfahren Sie, wie Sie den Azure Web PubSub-Dienst und Azure Functions verwenden, um eine serverlose Anwendung mit Echtzeit-Messaging für Benachrichtigungszenarien zu erstellen. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer serverlosen Benachrichtigungs-App
> * Verwenden von Eingabe- und Ausgabebindungen für Web PubSub-Funktionen
> * Lokales Ausführen der Beispielfunktionen
> * Bereitstellen der Funktion in der Azure-Funktions-App

## <a name="prerequisites"></a>Voraussetzungen

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* Ein Code-Editor wie [Visual Studio Code](https://code.visualstudio.com/)

* [Node.js](https://nodejs.org/en/download/), Version 10.x
   > [!NOTE]
   > Weitere Informationen zu den unterstützten Versionen von Node.js finden Sie in der [Übersicht über die Runtimeversionen von Azure Functions](../azure-functions/functions-versions.md#languages).

* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (v3 oder höher bevorzugt), um Azure-Funktions-Apps lokal auszuführen und in Azure bereitstellen zu können.

* [Azure-Befehlszeilenschnittstelle (Azure CLI)](/cli/azure) zum Verwalten von Azure-Ressourcen.

# <a name="c"></a>[C#](#tab/csharp)

* Ein Code-Editor wie [Visual Studio Code](https://code.visualstudio.com/).

* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (v3 oder höher bevorzugt), um Azure-Funktions-Apps lokal auszuführen und in Azure bereitstellen zu können.

* [Azure-Befehlszeilenschnittstelle (Azure CLI)](/cli/azure) zum Verwalten von Azure-Ressourcen.

---

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="create-and-run-the-functions-locally"></a>Erstellen und lokales Ausführen der Funktionen

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
                    "hub": "notification",
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
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            [WebPubSubConnection(Hub = "notification")] WebPubSubConnection connection,
            ILogger log)
        {
            log.LogInformation("Connecting...");

            return connection;
        }
        ```

5. Erstellen Sie eine `notification`-Funktion zum Generieren von Benachrichtigungen mit `TimerTrigger`.
   ```bash
    func new -n notification -t TimerTrigger
    ```
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - Aktualisieren Sie `notification/function.json` und kopieren Sie den folgenden JSON-Code.
        ```json
        {
            "bindings": [
                {
                "name": "myTimer",
                "type": "timerTrigger",
                "direction": "in",
                "schedule": "*/10 * * * * *"
                },
                {
                "type": "webPubSub",
                "name": "actions",
                "hub": "notification",
                "direction": "out"
                }
            ]
        }
        ```
   - Aktualisieren Sie `notification/index.js` und kopieren Sie den folgenden Code.
        ```js
        module.exports = function (context, myTimer) {
            context.bindings.actions = {
                "actionName": "sendToAll",
                "data": `[DateTime: ${new Date()}] Temperature: ${getValue(22, 1)}\xB0C, Humidity: ${getValue(40, 2)}%`,
                "dataType": "text"
            }
            context.done();
        };

        function getValue(baseNum, floatNum) {
            return (baseNum + 2 * floatNum * (Math.random() - 0.5)).toFixed(3);
        }
        ```
   # <a name="c"></a>[C#](#tab/csharp)
   - Aktualisieren Sie `notification.cs` und ersetzen Sie die `Run`-Funktion durch den folgenden Code.
        ```c#
        [FunctionName("notification")]
        public static async Task Run([TimerTrigger("*/10 * * * * *")]TimerInfo myTimer, ILogger log,
            [WebPubSub(Hub = "notification")] IAsyncCollector<WebPubSubAction> actions)
        {
            await actions.AddAsync(new SendToAllAction
            {
                Data = BinaryData.FromString($"[DateTime: {DateTime.Now}] Temperature: {GetValue(23, 1)}{'\xB0'}C, Humidity: {GetValue(40, 2)}%"),
                DataType = WebPubSubDataType.Text
            });
        }

        private static string GetValue(double baseNum, double floatNum)
        {
            var rng = new Random();
            var value = baseNum + floatNum * 2 * (rng.NextDouble() - 0.5);
            return value.ToString("0.000");
        }
        ``` 

6. Fügen Sie die Client-Einzelseite `index.html` im Stammordner des Projekts hinzu, und kopieren Sie den unten gezeigten Inhalt.
    ```html
    <html>
        <body>
        <h1>Azure Web PubSub Notification</h1>
        <div id="messages"></div>
        <script>
            (async function () {
                let messages = document.querySelector('#messages');
                let res = await fetch(`${window.location.origin}/api/negotiate`);
                let url = await res.json();
                let ws = new WebSocket(url.url);
                ws.onopen = () => console.log('connected');

                ws.onmessage = event => {
                    let m = document.createElement('p');
                    m.innerText = event.data;
                    messages.appendChild(m);
                };
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
    ```

7. Konfigurieren und Ausführen der Azure Functions-App

    - Öffnen Sie im Browser das **Azure-Portal**, und vergewissern Sie sich, dass die zuvor bereitgestellte Instanz des Web PubSub-Diensts erfolgreich erstellt wurde. Navigieren Sie zu der Instanz.
    - Wählen Sie **Schlüssel** aus, und kopieren Sie die Verbindungszeichenfolge.

    :::image type="content" source="media/quickstart-serverless/copy-connection-string.png" alt-text="Screenshot: Kopieren der Web PubSub-Verbindungszeichenfolge":::

    Führen Sie den folgenden Befehl im Funktionsordner aus, um die Dienstverbindungszeichenfolge festzulegen. Ersetzen Sie `<connection-string`> durch den jeweils erforderlichen Wert.

    ```bash
    func settings add WebPubSubConnectionString "<connection-string>"
    ```

    > [!NOTE]
    > Das im Beispiel verwendete `TimerTrigger` ist von Azure Storage abhängig. Sie können jedoch den lokalen Speicheremulator verwenden, wenn die Funktion lokal ausgeführt wird. Wird eine Fehlermeldung wie `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.` angezeigt, müssen Sie den [Speicheremulator](../storage/common/storage-use-emulator.md) herunterladen und aktivieren.

    Jetzt können Sie Ihre lokale Funktion mit dem folgenden Befehl ausführen.

    ```bash
    func start
    ```

    Wenn Sie die ausgeführten Protokolle überprüfen, können Sie die statische Seite Ihres lokalen Hosts über `https://localhost:7071/api/index` aufrufen.

## <a name="deploy-function-app-to-azure"></a>Bereitstellen der Funktions-App in Azure

Zum Bereitstellen Ihres Funktionscodes in Azure müssen Sie 3 Ressourcen erstellen:
* Eine Ressourcengruppe als logischen Container für verwandte Ressourcen.
* Ein Speicherkonto, das verwendet wird, um den Status und andere Informationen zu Ihren Funktionen zu verwalten.
* Eine Funktions-App, die als Umgebung zum Ausführen Ihres Funktionscodes dient. Eine Funktions-App ist Ihrem lokalen Funktionsprojekt zugeordnet und ermöglicht Ihnen das Gruppieren von Funktionen als logische Einheit, um die Verwaltung, Bereitstellung und Freigabe von Ressourcen zu vereinfachen.

Verwenden Sie die folgenden Befehle, um diese Elemente zu erstellen. 

1. Melden Sie sich bei Azure an, falls dies noch nicht geschehen ist:

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

    Nachdem Sie Ihre Funktions-App in Azure erfolgreich erstellt haben, können Sie nun Ihr lokales Funktionsprojekt bereitstellen, indem Sie den Befehl [func azure functionapp publish](../azure-functions/functions-run-local.md) verwenden.

    ```bash
    func azure functionapp publish <FUNCIONAPP_NAME> --publish-local-settings
    ```

    > [!NOTE]
    > Hier werden die lokalen Einstellungen von `local.settings.json` zusammen mit dem Befehlsparameter `--publish-local-settings` bereitgestellt. Wenn Sie den Microsoft Azure-Speicheremulator verwenden, können Sie `no` für die folgende Eingabeaufforderung eingeben, damit dieser Wert in Azure nicht überschrieben wird: `App setting AzureWebJobsStorage is different between azure and local.settings.json, Would you like to overwrite value in azure? [yes/no/show]`. Darüber hinaus können Sie die Einstellungen für die Funktions-App in **Azure-Portal** ->  **Einstellungen** -> **Aktualisieren** aktualisieren.

1. Nun können Sie Ihre Website über die Azure-Funktions-App überprüfen, indem Sie zur URL `https://<FUNCIONAPP_NAME>.azurewebsites.net/api/index` navigieren.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese App nicht mehr benötigen, löschen Sie alle im Rahmen dieser Dokumentation erstellten Ressourcen, damit keine Gebühren anfallen. Gehen Sie dazu wie folgt vor:

1. Klicken Sie ganz links im Azure-Portal auf **Ressourcengruppen** und anschließend auf die erstellte Ressourcengruppe. Sie können auch das Suchfeld verwenden, um nach dem Namen der Ressourcengruppe zu suchen.

1. Wählen im daraufhin angezeigten Fenster die Ressourcengruppe und anschließend die Option **Ressourcengruppe löschen** aus.

1. Geben Sie im neuen Fenster den Namen der Ressourcengruppe ein, die Sie löschen möchten, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie eine serverlose Benachrichtigungsanwendung ausführen. Sie können nun damit beginnen, Ihre eigene Anwendung zu erstellen. 

> [!div class="nextstepaction"]
> [Tutorial: Erstellen eines einfachen Chatrooms mit Azure Web PubSub](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [Azure Web PubSub-Bindungen für Azure Functions](https://azure.github.io/azure-webpubsub/references/functions-bindings)

> [!div class="nextstepaction"]
> [Weitere Beispiele für Azure Web PubSub](https://github.com/Azure/azure-webpubsub/tree/main/samples)