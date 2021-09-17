---
title: 'Schnellstart: Serverlose Anwendungen mit Azure SignalR Service (C#)'
description: Hier finden Sie eine Schnellstartanleitung für die Verwendung von Azure SignalR Service und Azure Functions zum Erstellen einer App mithilfe von C#, die die Anzahl der GitHub-Sterne anzeigt.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 06/09/2021
ms.author: zhshang
ms.openlocfilehash: 3a3fa958ac6ad1cb440f30b5c680ae3f9139d29a
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444922"
---
# <a name="quickstart-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service-using-c"></a>Schnellstart: Erstellen einer App, die die Anzahl der GitHub-Sterne anzeigt, mit Azure Functions und SignalR Service unter Verwendung von C\#

Mit dem Azure SignalR-Dienst können Sie Ihrer Anwendung ganz einfach Echtzeitfunktionen hinzufügen. Azure Functions ist eine serverlose Plattform, mit der Sie Ihren Code ohne Verwaltung von Infrastruktur ausführen können. In dieser Schnellstartanleitung erfahren Sie, wie Sie mit SignalR Service und Azure Functions eine serverlose Anwendung mit C# erstellen, um Nachrichten an Clients zu übertragen.

> [!NOTE]
> Sie finden den gesamten im Artikel erwähnten Code auf [GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/csharp).

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie Visual Studio Code noch nicht installiert haben, können Sie die Anwendung kostenlos herunterladen und verwenden (https://code.visualstudio.com/Download).

Sie können dieses Tutorial auch über die Befehlszeile (macOS, Windows oder Linux) ausführen und dabei [Azure Functions Core Tools](../azure-functions/functions-run-local.md?tabs=windows%2Ccsharp%2Cbash#v2) verwenden. Sie können außerdem das [.NET Core SDK](https://dotnet.microsoft.com/download) und Ihren bevorzugten Code-Editor nutzen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/dotnet), bevor Sie beginnen.

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qscsharp).

## <a name="log-in-to-azure-and-create-signalr-service-instance"></a>Anmelden bei Azure und Erstellen einer SignalR Service-Instanz

Melden Sie sich unter <https://portal.azure.com/> mit Ihrem Azure-Konto beim Azure-Portal an.

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qscsharp).

## <a name="setup-and-run-the-azure-function-locally"></a>Einrichten und Ausführen von Azure-Function auf lokaler Ebene

1. Stellen Sie sicher, dass die Azure Function Core Tools installiert sind. Erstellen Sie außerdem über die Befehlszeile ein leeres Verzeichnis, und navigieren Sie zu diesem Verzeichnis.

    ```bash
    # Initialize a function project
    func init --worker-runtime dotnet
    
    # Add SignalR Service package reference to the project
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService
    ```

2. Nachdem Sie ein Projekt initialisiert haben, erstellen Sie eine neue Datei mit dem Namen *Function.cs*. Fügen Sie *Function.cs* den folgenden Code hinzu.
   
    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Http;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.Azure.WebJobs.Extensions.SignalRService;
    using Newtonsoft.Json;
    
    namespace CSharp
    {
        public static class Function
        {
            private static HttpClient httpClient = new HttpClient();
    
            [FunctionName("index")]
            public static IActionResult Index([HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, ExecutionContext context)
            {
                var path = Path.Combine(context.FunctionAppDirectory, "content", "index.html");
                return new ContentResult
                {
                    Content = File.ReadAllText(path),
                    ContentType = "text/html",
                };
            }
    
            [FunctionName("negotiate")]
            public static SignalRConnectionInfo Negotiate( 
                [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req,
                [SignalRConnectionInfo(HubName = "serverlessSample")] SignalRConnectionInfo connectionInfo)
            {
                return connectionInfo;
            }
    
            [FunctionName("broadcast")]
            public static async Task Broadcast([TimerTrigger("*/5 * * * * *")] TimerInfo myTimer,
            [SignalR(HubName = "serverlessSample")] IAsyncCollector<SignalRMessage> signalRMessages)
            {
                var request = new HttpRequestMessage(HttpMethod.Get, "https://api.github.com/repos/azure/azure-signalr");
                request.Headers.UserAgent.ParseAdd("Serverless");
                var response = await httpClient.SendAsync(request);
                var result = JsonConvert.DeserializeObject<GitResult>(await response.Content.ReadAsStringAsync());
                await signalRMessages.AddAsync(
                    new SignalRMessage
                    {
                        Target = "newMessage",
                        Arguments = new[] { $"Current star count of https://github.com/Azure/azure-signalr is: {result.StarCount}" }
                    });
            }
    
            private class GitResult
            {
                [JsonRequired]
                [JsonProperty("stargazers_count")]
                public string StarCount { get; set; }
            }
        }
    }
    ```
    Diese Codes verfügen über drei Funktionen. `Index` wird zum Abrufen einer Website als Client verwendet. `Negotiate` wird für den Client verwendet, um ein Zugriffstoken abzurufen. `Broadcast` ruft in regelmäßigen Abständen die Anzahl von Sternen von GitHub ab und sendet Nachrichten an alle Clients.

3. Die Clientschnittstelle dieses Beispiels ist eine Webseite. Wir lesen den HTML-Inhalt aus der `content/index.html` in der Funktion `GetHomePage` und erstellen eine neue Datei `index.html` im Verzeichnis `content` unter dem Stammordner des Projekts. Dann kopieren wir den folgendem Inhalt.
    ```html
    <html>
    
    <body>
      <h1>Azure SignalR Serverless Sample</h1>
      <div id="messages"></div>
      <script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.7/signalr.min.js"></script>
      <script>
        let messages = document.querySelector('#messages');
        const apiBaseUrl = window.location.origin;
        const connection = new signalR.HubConnectionBuilder()
            .withUrl(apiBaseUrl + '/api')
            .configureLogging(signalR.LogLevel.Information)
            .build();
          connection.on('newMessage', (message) => {
            document.getElementById("messages").innerHTML = message;
          });
    
          connection.start()
            .catch(console.error);
      </script>
    </body>
    
    </html>
    ```

4. Aktualisieren Sie die `*.csproj`, um die Inhaltsseite im Ordner für die Buildausgabe zu erstellen.

    ```html
    <ItemGroup>
      <None Update="content/index.html">
        <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
      </None>
    </ItemGroup>
    ```

5. Der Vorgang ist jetzt fast abgeschlossen. Der letzte Schritt besteht darin, eine Verbindungszeichenfolge des SignalR Service auf die Azure-Function Einstellungen festzulegen.

    1. Bestätigen Sie im Browser, in dem das Azure-Portal geöffnet ist, dass die von Ihnen zuvor bereitgestellte SignalR-Dienstinstanz erfolgreich erstellt wurde, indem Sie nach ihrem Namen im Suchfeld oben im Portal suchen. Wählen Sie die Instanz aus, um sie zu öffnen.

        ![Suchen nach der SignalR-Dienstinstanz](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    2. Wählen Sie **Schlüssel** aus, um die Verbindungszeichenfolgen für die SignalR-Dienstinstanz anzuzeigen.
    
        ![Screenshot: Hervorgehobene primäre Verbindungszeichenfolge](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    3. Kopieren Sie die primäre Verbindungszeichenfolge. Führen Sie den folgenden Befehl aus.
    
        ```bash
        func settings add AzureSignalRConnectionString "<signalr-connection-string>"
        ```
    
6. Führen Sie Azure-Function auf lokaler Ebene aus:

    ```bash
    func start
    ```

    Nachdem Azure-Function auf lokaler Ebene ausgeführt wurde. Verwenden Sie Ihren Browser, um `http://localhost:7071/api/index` besuchen, und Sie können die aktuelle Sternanzahl anzeigen. Wenn Sie auf GitHub einen Stern vergeben oder entfernen, wird die Anzahl von Sternen alle paar Sekunden aktualisiert.

    > [!NOTE]
    > Die SignalR-Bindung benötigt Azure Storage, Sie können jedoch den lokalen Speicheremulator verwenden, wenn die Funktion lokal ausgeführt wird.
    > Wenn Sie eine Fehlermeldung wie z. B. `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.` erhalten haben, müssen Sie den [Speicheremulator](../storage/common/storage-use-emulator.md) herunterladen und aktivieren

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qspython).

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie eine lokale serverlose Echtzeitanwendung erstellt und ausgeführt. Erfahren Sie mehr darüber, wie Bindungen des SignalR-Services in Azure Functions verwendet werden.
Als Nächstes erfahren Sie mehr über die bidirektionale Kommunikation zwischen Clients und Azure Function mit dem SignalR Service.

> [!div class="nextstepaction"]
> [Bindungen des SignalR-Diensts für Azure Functions](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [Bidirektionale Kommunikation in Serverlos](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [Bereitstellen in der Azure-Funktions-App mit Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure)

