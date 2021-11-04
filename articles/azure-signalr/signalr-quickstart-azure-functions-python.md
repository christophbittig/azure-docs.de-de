---
title: 'Schnellstartanleitung: Serverlose Anwendungen mit Azure SignalR Service – Python'
description: Eine Schnellstartanleitung für die Verwendung von Azure Functions und dem SignalR-Service mithilfe von Python zum Erstellen einer App, die die Anzahl der GitHub-Sterne anzeigt.
author: anthonychu
ms.author: antchu
ms.date: 06/09/2021
ms.topic: quickstart
ms.service: signalr
ms.devlang: python
ms.custom: devx-track-python, mode-api
ms.openlocfilehash: 0d6066e5305cf3acda350dcaef4572d6564395f2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069594"
---
# <a name="quickstart-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service-using-python"></a>Schnellstart: Erstellen Sie eine App, die die Anzahl der GitHub-Sterne mit Azure Functions und dem SignalR-Service mithilfe von Python anzeigt

Mit dem Azure SignalR-Dienst können Sie Ihrer Anwendung ganz einfach Echtzeitfunktionen hinzufügen. Azure Functions ist eine serverlose Plattform, mit der Sie Ihren Code ohne Verwaltung von Infrastruktur ausführen können. In dieser Schnellstartanleitung erfahren Sie, wie Sie mit SignalR Service und Azure Functions eine serverlose Anwendung mit Python erstellen, um Nachrichten an Clients zu übertragen.

> [!NOTE]
> Sie können alle im Artikel erwähnten Codes von [GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/python) abrufen

## <a name="prerequisites"></a>Voraussetzungen

Dieser Schnellstart kann unter MacOS, Windows oder Linux ausgeführt werden.

Stellen Sie sicher, dass Sie einen Code-Editor wie [Visual Studio Code](https://code.visualstudio.com/) installiert haben.

Installieren Sie die [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (Version 2.7.1505 oder höher), um in Python programmierte Azure Functions-Apps lokal auszuführen.

Azure Functions erfordert [mindestens Python 3.6](https://www.python.org/downloads/). (Siehe [Unterstützte Python-Versionen](../azure-functions/functions-reference-python.md#python-version))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qspython).

## <a name="log-in-to-azure"></a>Anmelden an Azure

Melden Sie sich unter <https://portal.azure.com/> mit Ihrem Azure-Konto beim Azure-Portal an.

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qspython).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qspython).


## <a name="setup-and-run-the-azure-function-locally"></a>Einrichten und Ausführen von Azure-Function auf lokaler Ebene

1. Stellen Sie sicher, dass die Azure Function Core Tools installiert sind. Erstellen Sie ein leeres Verzeichnis und navigieren Sie über die Befehlszeile zum Verzeichnis.

    ```bash
    # Initialize a function project
    func init --worker-runtime python
    ```

2. Nachdem Sie ein Projekt initialisiert haben, müssen Sie die Funktionen erstellen. In diesem Beispiel müssen wir drei Funktionen erstellen.

    1. Führen Sie den folgenden Befehl aus, um eine Funktion `index` zu erstellen, die eine Webseite für den Client hosten wird.

        ```bash
        func new -n index -t HttpTrigger
        ```
        Öffnen Sie `index/function.json` und Kopieren Sie die folgenden JSON Codes:

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

        Öffnen Sie `index/__init__.py` und Kopieren Sie die folgenden Codes.

        ```javascript
        import os
    
        import azure.functions as func
        
        
        def main(req: func.HttpRequest) -> func.HttpResponse:
            f = open(os.path.dirname(os.path.realpath(__file__)) + '/../content/index.html')
            return func.HttpResponse(f.read(), mimetype='text/html')
        ```
    
    2. Erstellen Sie eine Funktion `negotiate` für Clients, um Zugriffstoken abzurufen.
    
        ```bash
        func new -n negotiate -t SignalRNegotiateHTTPTrigger
        ```
        
        Öffnen Sie `negotiate/function.json` und Kopieren Sie die folgenden JSON Codes:
    
        ```json
        {
          "scriptFile": "__init__.py",
          "bindings": [
            {
              "authLevel": "anonymous",
              "type": "httpTrigger",
              "direction": "in",
              "name": "req",
              "methods": [
                "post"
              ]
            },
            {
              "type": "http",
              "direction": "out",
              "name": "$return"
            },
            {
              "type": "signalRConnectionInfo",
              "name": "connectionInfo",
              "hubName": "serverless",
              "connectionStringSetting": "AzureSignalRConnectionString",
              "direction": "in"
            }
          ]
        }
        ```

        Öffnen Sie `negotiate/__init__.py` und Kopieren Sie die folgenden Codes:

        ```python
        import azure.functions as func
    
        
        def main(req: func.HttpRequest, connectionInfo) -> func.HttpResponse:
            return func.HttpResponse(connectionInfo)
        ```
    
    3. Erstellen Sie eine Funktion `broadcast` zum Übertragen von Nachrichten an alle Clients. Im Beispiel verwenden wir den Zeitauslöser, um Nachrichten in regelmäßigen Abständen zu übertragen.
    
        ```bash
        func new -n broadcast -t TimerTrigger
        # install requests
        pip install requests
        ```

        Öffnen Sie `broadcast/function.json` und Kopieren Sie die folgenden Codes.

        ```json
        {
          "scriptFile": "__init__.py",
          "bindings": [
            {
              "name": "myTimer",
              "type": "timerTrigger",
              "direction": "in",
              "schedule": "*/5 * * * * *"
            },
            {
              "type": "signalR",
              "name": "signalRMessages",
              "hubName": "serverless",
              "connectionStringSetting": "AzureSignalRConnectionString",
              "direction": "out"
            }
          ]
        }
        ```
    
        Öffnen Sie `broadcast/__init__.py` und Kopieren Sie die folgenden Codes.
    
        ```python
        import requests
        import json
        
        import azure.functions as func
        
        
        def main(myTimer: func.TimerRequest, signalRMessages: func.Out[str]) -> None:
            headers = {'User-Agent': 'serverless'}
            res = requests.get('https://api.github.com/repos/azure/azure-signalr', headers=headers)
            jres = res.json()
        
            signalRMessages.set(json.dumps({
                'target': 'newMessage',
                'arguments': [ 'Current star count of https://github.com/Azure/azure-signalr is: ' + str(jres['stargazers_count']) ]
            }))
        ```

3. Die Clientschnittstelle dieses Beispiels ist eine Webseite. Wir lesen den HTML-Inhalt aus der `content/index.html` in der Funktion `index` und erstellen eine neue Datei `index.html` im Verzeichnis `content` unter dem Stammordner des Projekts. Dann kopieren wir den folgendem Inhalt.

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
    
4. Der Vorgang ist jetzt fast abgeschlossen. Der letzte Schritt besteht darin, eine Verbindungszeichenfolge des SignalR Service auf die Azure-Function Einstellungen festzulegen.

    1. Bestätigen Sie im Browser, in dem das Azure-Portal geöffnet ist, dass die von Ihnen zuvor bereitgestellte SignalR-Dienstinstanz erfolgreich erstellt wurde, indem Sie nach ihrem Namen im Suchfeld oben im Portal suchen. Wählen Sie die Instanz aus, um sie zu öffnen.

        ![Suchen nach der SignalR-Dienstinstanz](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    2. Wählen Sie **Schlüssel** aus, um die Verbindungszeichenfolgen für die SignalR-Dienstinstanz anzuzeigen.
    
        ![Screenshot: Hervorgehobene primäre Verbindungszeichenfolge](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    3. Kopieren Sie die primäre Verbindungszeichenfolge. Führen Sie den folgenden Befehl aus.
    
        ```bash
        func settings add AzureSignalRConnectionString "<signalr-connection-string>"
        ```
    
5. Führen Sie Azure-Function auf lokaler Ebene aus:

    ```bash
    func start
    ```

    Nachdem Azure-Function auf lokaler Ebene ausgeführt wurde. Verwenden Sie Ihren Browser, um `http://localhost:7071/api/index` besuchen, und Sie können die aktuelle Sternanzahl anzeigen. Wenn Sie auf GitHub einen Stern vergeben oder entfernen, wird die Anzahl von Sternen alle paar Sekunden aktualisiert.

    > [!NOTE]
    > Die SignalR-Bindung benötigt Azure Storage, Sie können jedoch den lokalen Speicheremulator verwenden, wenn die Funktion lokal ausgeführt wird.
    > Wenn Sie eine Fehlermeldung wie z. B. `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.` erhalten haben, müssen Sie den [Speicheremulator](../storage/common/storage-use-emulator.md) herunterladen und aktivieren

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
> [Bereitstellen von Azure Functions mit VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)
