---
title: Erstellen eines Chatraums mit Azure Functions und SignalR Service mithilfe von Java
description: Eine Schnellstartanleitung für die Verwendung von Azure Functions und dem SignalR-Service mithilfe von Java zum Erstellen einer App, die die Anzahl der GitHub-Sterne anzeigt.
author: sffamily
ms.author: zhshang
ms.date: 06/09/2021
ms.topic: quickstart
ms.service: signalr
ms.devlang: java
ms.custom: devx-track-java, mode-api
ms.openlocfilehash: 5cea13b3f6410df1d5a63efe4acdd080b21f2f5c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069575"
---
# <a name="quickstart-use-java-to-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service"></a>Schnellstart: Erstellen Sie eine App, die die Anzahl der GitHub-Sterne mit Azure Functions und dem SignalR-Service mithilfe von Java anzeigt

Mit Azure SignalR Service können Sie Ihrer Anwendung ganz einfach Echtzeitfunktionen hinzufügen. Azure Functions ist eine serverlose Plattform, mit der Sie Ihren Code ohne Verwaltung von Infrastruktur ausführen können. In dieser Schnellstartanleitung erfahren Sie, wie Sie mit SignalR Service und Azure Functions eine serverlose Anwendung mit Java erstellen, um Nachrichten an Clients zu übertragen.

> [!NOTE]
> Sie können alle im Artikel erwähnten Codes von [GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/java) abrufen

## <a name="prerequisites"></a>Voraussetzungen

- Ein Code-Editor wie [Visual Studio Code](https://code.visualstudio.com/)
- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing). Dient zum lokalen Ausführen von Azure-Funktions-Apps.

   > [!NOTE]
   > Die erforderlichen SignalR Service-Bindungen in Java werden nur in Version 2.4.419 von Azure Function Core Tools (Hostversion 2.0.12332) oder höher unterstützt.

   > [!NOTE]
   > Für die Installation von Erweiterungen erfordert Azure Functions Core Tools die Installation des [.NET Core SDK](https://dotnet.microsoft.com/download). Allerdings sind keine Kenntnisse von .NET erforderlich, um JavaScript Azure Functions-Apps zu erstellen.

- [Java Developer Kit](https://www.azul.com/downloads/zulu/), Version 11
- [Apache Maven](https://maven.apache.org), Version 3.0 oder höher

> [!NOTE]
> Dieser Schnellstart kann unter MacOS, Windows oder Linux ausgeführt werden.

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsjava).

## <a name="log-in-to-azure"></a>Anmelden an Azure

Melden Sie sich unter <https://portal.azure.com/> mit Ihrem Azure-Konto beim Azure-Portal an.

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsjava).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsjava).


## <a name="configure-and-run-the-azure-function-app"></a>Konfigurieren und Ausführen der Azure Functions-App

1. Stellen Sie sicher, dass Azure Function Core Tools, Java (Version 11 im Beispiel) und Maven installiert sind.
    
    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=11
    ```

    Maven fordert Sie zur Eingabe der Werte auf, die erforderlich sind, um die Generierung des Projekts abzuschließen. Sie können die folgenden Werte angeben.

    | Prompt | Wert | BESCHREIBUNG |
    | ------ | ----- | ----------- |
    | **groupId** | `com.signalr` | Ein Wert, der Ihr Projekt projektübergreifend eindeutig identifiziert. Für den Wert müssen die [Paketbenennungsregeln](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) für Java eingehalten werden. |
    | **artifactId** | `java` | Der Name des Behälters (ohne Versionsnummer). |
    | **version** | `1.0-SNAPSHOT` | Wählen Sie den Standardwert aus. |
    | **package** | `com.signalr` | Das Java-Paket für den generierten Funktionscode. Verwenden Sie den Standardwert. |   

2. Nachdem Sie ein Projekt initialisiert haben. Wechseln Sie zum Ordner `src/main/java/com/signalr` und kopieren Sie die folgenden Codes in `Function.java`

    ```java
    package com.signalr;
  
    import com.google.gson.Gson;
    import com.microsoft.azure.functions.ExecutionContext;
    import com.microsoft.azure.functions.HttpMethod;
    import com.microsoft.azure.functions.HttpRequestMessage;
    import com.microsoft.azure.functions.HttpResponseMessage;
    import com.microsoft.azure.functions.HttpStatus;
    import com.microsoft.azure.functions.annotation.AuthorizationLevel;
    import com.microsoft.azure.functions.annotation.FunctionName;
    import com.microsoft.azure.functions.annotation.HttpTrigger;
    import com.microsoft.azure.functions.annotation.TimerTrigger;
    import com.microsoft.azure.functions.signalr.*;
    import com.microsoft.azure.functions.signalr.annotation.*;
    
    import org.apache.commons.io.IOUtils;
    
    
    import java.io.IOException;
    import java.io.InputStream;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.net.http.HttpResponse.BodyHandlers;
    import java.nio.charset.StandardCharsets;
    import java.util.Optional;
    
    public class Function {
        @FunctionName("index")
        public HttpResponseMessage run(
                @HttpTrigger(
                    name = "req",
                    methods = {HttpMethod.GET},
                    authLevel = AuthorizationLevel.ANONYMOUS)HttpRequestMessage<Optional<String>> request,
                final ExecutionContext context) throws IOException {
            
            InputStream inputStream = getClass().getClassLoader().getResourceAsStream("content/index.html");
            String text = IOUtils.toString(inputStream, StandardCharsets.UTF_8.name());
            return request.createResponseBuilder(HttpStatus.OK).header("Content-Type", "text/html").body(text).build();
        }
  
        @FunctionName("negotiate")
        public SignalRConnectionInfo negotiate(
                @HttpTrigger(
                    name = "req",
                    methods = { HttpMethod.POST },
                    authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
                @SignalRConnectionInfoInput(
                    name = "connectionInfo",
                    hubName = "serverless") SignalRConnectionInfo connectionInfo) {
                    
            return connectionInfo;
        }
    
        @FunctionName("broadcast")
        @SignalROutput(name = "$return", hubName = "serverless")
        public SignalRMessage broadcast(
            @TimerTrigger(name = "timeTrigger", schedule = "*/5 * * * * *") String timerInfo) throws IOException, InterruptedException {
            
            HttpClient client = HttpClient.newHttpClient();
            HttpRequest req = HttpRequest.newBuilder().uri(URI.create("https://api.github.com/repos/azure/azure-signalr")).header("User-Agent", "serverless").build();
            HttpResponse<String> res = client.send(req, BodyHandlers.ofString());
            Gson gson = new Gson();
            GitResult result = gson.fromJson(res.body(), GitResult.class);
            return new SignalRMessage("newMessage", "Current star count of https://github.com/Azure/azure-signalr is:".concat(result.stargazers_count));
        }
    
        class GitResult {
            public String stargazers_count;
        }
    }
    ```

3. Einige Abhängigkeiten müssen hinzugefügt werden. Öffnen Sie also `pom.xml` und fügen Sie eine Abhängigkeit hinzu, die in Codes verwendet wird.

    ```xml
    <dependency>
        <groupId>com.microsoft.azure.functions</groupId>
        <artifactId>azure-functions-java-library-signalr</artifactId>
        <version>1.0.0</version>
    </dependency>
    <dependency>
        <groupId>commons-io</groupId>
        <artifactId>commons-io</artifactId>
        <version>2.4</version>
    </dependency>
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
        <version>2.8.7</version>
    </dependency>
    ```

4. Die Clientschnittstelle dieses Beispiels ist eine Webseite. Wir lesen den HTML-Inhalt aus der `content/index.html` in der `index` Funktion und erstellen eine neue Datei `content/index.html` im `resources` Verzeichnis. Ihre Verzeichnisstruktur sollte wie folgt aussehen.
    
    ```
    FunctionsProject
     | - src
     | | - main
     | | | - java
     | | | | - com
     | | | | | - signalr 
     | | | | | | - Function.java
     | | | - resources
     | | | | - content
     | | | | | - index.html
     | - pom.xml
     | - host.json
     | - local.settings.json
    ```

    Öffnen sie `index.html` und Kopieren Sie den folgenden Inhalt.

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

5. Der Vorgang ist jetzt fast abgeschlossen. Der letzte Schritt besteht darin, eine Verbindungszeichenfolge des SignalR Service auf die Azure-Function Einstellungen festzulegen.

    1. Bestätigen Sie im Browser, in dem das Azure-Portal geöffnet ist, dass die von Ihnen zuvor bereitgestellte SignalR-Dienstinstanz erfolgreich erstellt wurde, indem Sie nach ihrem Namen im Suchfeld oben im Portal suchen. Wählen Sie die Instanz aus, um sie zu öffnen.

        ![Suchen nach der SignalR-Dienstinstanz](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    1. Wählen Sie **Schlüssel** aus, um die Verbindungszeichenfolgen für die SignalR-Dienstinstanz anzuzeigen.
    
        ![Screenshot: Hervorgehobene primäre Verbindungszeichenfolge](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    1. Kopieren Sie die primäre Verbindungszeichenfolge. Führen Sie den folgenden Befehl aus.
    
        ```bash
        func settings add AzureSignalRConnectionString "<signalr-connection-string>"
        # Also we need to set AzureWebJobsStorage as Azure Function's requirement
        func settings add AzureWebJobsStorage "UseDevelopmentStorage=true"
        ```
    
6. Führen Sie Azure-Function auf lokaler Ebene aus:

    ```bash
    mvn clean package
    mvn azure-functions:run
    ```

    Nachdem Azure-Function auf lokaler Ebene ausgeführt wurde. Verwenden Sie Ihren Browser, um `http://localhost:7071/api/index` besuchen, und Sie können die aktuelle Sternanzahl anzeigen. Wenn Sie auf GitHub einen Stern vergeben oder entfernen, wird die Anzahl von Sternen alle paar Sekunden aktualisiert.

    > [!NOTE]
    > Die SignalR-Bindung benötigt Azure Storage, Sie können jedoch den lokalen Speicheremulator verwenden, wenn die Funktion lokal ausgeführt wird.
    > Wenn Sie eine Fehlermeldung wie z. B. `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.` erhalten haben, müssen Sie den [Speicheremulator](../storage/common/storage-use-emulator.md) herunterladen und aktivieren
    
Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsjava).


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsjava).

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie eine lokale serverlose Echtzeitanwendung erstellt und ausgeführt. Erfahren Sie mehr darüber, wie Bindungen des SignalR-Services in Azure Functions verwendet werden.
Als Nächstes erfahren Sie mehr über die bidirektionale Kommunikation zwischen Clients und Azure Function mit dem SignalR Service.

> [!div class="nextstepaction"]
> [Bindungen des SignalR-Diensts für Azure Functions](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [Bidirektionale Kommunikation in Serverlos](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [Erstellen Ihrer ersten Funktion mit Java und Maven](../azure-functions/create-first-function-cli-csharp.md?pivots=programming-language-java%2cprogramming-language-java)
