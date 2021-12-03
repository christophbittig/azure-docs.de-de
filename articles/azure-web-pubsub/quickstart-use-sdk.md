---
title: 'Schnellstart: Veröffentlichen von Nachrichten mithilfe des Dienst-SDK für die Azure Web PubSub-Instanz'
description: Schnellstart zur Verwendung des Dienst-SDK
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/01/2021
ms.openlocfilehash: c3e422699d03e50bfd860ded2e281c8199ac7a5e
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546555"
---
# <a name="quickstart-publish-messages-using-the-service-sdk-for-the-azure-web-pubsub-instance"></a>Schnellstart: Veröffentlichen von Nachrichten mithilfe des Dienst-SDK für die Azure Web PubSub-Instanz

In dieser Schnellstartanleitung erfahren Sie, wie Sie Nachrichten mithilfe des Dienst-SDK für Clients veröffentlichen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diese Schnellstartanleitung ist mindestens Version 2.22.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

## <a name="create-a-web-pubsub-instance"></a>Erstellen einer Web PubSub-Instanz

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

## <a name="get-the-connectionstring-for-future-use"></a>Abrufen der Verbindungszeichenfolge (ConnectionString) zur späteren Verwendung

[!INCLUDE [Get the connection string](includes/cli-awps-connstr.md)]

Kopieren Sie die abgerufene **Verbindungszeichenfolge**. Sie wird später bei Verwendung des Dienst-SDK als Wert für `<connection_string>` verwendet.

## <a name="connect-to-the-instance"></a>Herstellen einer Verbindung mit der Instanz

[!INCLUDE [az webpubsub client](includes/cli-awps-client-connect.md)]

## <a name="publish-messages-using-service-sdk"></a>Veröffentlichen von Nachrichten mithilfe des Dienst-SDK

Verwenden Sie nun das Azure Web PubSub SDK, um eine Nachricht auf dem verbundenen Client zu veröffentlichen.

### <a name="prerequisites"></a>Voraussetzungen

# <a name="c"></a>[C#](#tab/csharp)

* [.NET Core 2.1 oder höher](https://dotnet.microsoft.com/download)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x oder höher](https://nodejs.org)

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

# <a name="java"></a>[Java](#tab/java)
- [Java Development Kit (JDK)](/java/azure/jdk/), Version 8 oder höher.
- [Apache Maven](https://maven.apache.org/download.cgi).

---

### <a name="set-up-the-project-to-publish-messages"></a>Einrichten des Projekts zum Veröffentlichen von Nachrichten

# <a name="c"></a>[C#](#tab/csharp)

1. Fügen Sie das neue Projekt `publisher` und das SDK-Paket `package Azure.Messaging.WebPubSub` hinzu.

    ```bash
    mkdir publisher
    cd publisher
    dotnet new console
    dotnet add package Azure.Messaging.WebPubSub
    ```

2. Aktualisieren Sie die Datei `Program.cs`, um die Klasse `WebPubSubServiceClient` zu verwenden und Nachrichten an die Clients zu senden.

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

                var service = new WebPubSubServiceClient(connectionString, hub);
                
                // Send messages to all the connected clients
                // You can also try SendToConnectionAsync to send messages to the specific connection
                await service.SendToAllAsync(message);
            }
        }
    }
    ```

    Der Aufruf `service.SendToAllAsync()` sendet einfach eine Nachricht an alle verbundenen Clients im Hub.

3. Führen Sie den folgenden Befehl aus, und ersetzen Sie `<connection_string>` durch die **Verbindungszeichenfolge**, die Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben:

    ```bash
    dotnet run "<connection_string>" "myHub1" "Hello World"
    ```

4. Sie sehen, dass der vorherige CLI-Client die Nachricht empfangen hat.
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
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

    if (process.argv.length !== 3) {
      console.log('Usage: node publish <message>');
      return 1;
    }
    const hub = "pubsub";
    let service = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, hub);
    // by default it uses `application/json`, specify contentType as `text/plain` if you want plain-text
    service.sendToAll(process.argv[2], { contentType: "text/plain" });
    ```

    Der Aufruf `sendToAll()` sendet einfach eine Nachricht an alle verbundenen Clients in einem Hub.

3. Führen Sie den folgenden Befehl aus, und ersetzen Sie `<connection_string>` durch die **Verbindungszeichenfolge**, die Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben:

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node publish "Hello World"
    ```

4. Sie sehen, dass der vorherige CLI-Client die Nachricht empfangen hat.
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
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

    Mit der Methode `service.send_to_all()` wird die Nachricht an alle verbundenen Clients in einem Hub gesendet.

3. Führen Sie den folgenden Befehl aus, und ersetzen Sie `<connection_string>` durch die **Verbindungszeichenfolge**, die Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben:

    ```bash
    python publish.py "<connection_string>" "myHub1" "Hello World"
    ```

4. Sie sehen, dass der vorherige CLI-Client die Nachricht empfangen hat.
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

# <a name="java"></a>[Java](#tab/java)

1. Verwenden Sie zunächst Maven, um eine neue Konsolen-App (`webpubsub-quickstart-publisher`) zu erstellen und in den Ordner *webpubsub-quickstart-publisher* zu wechseln:
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

3. Verwenden Sie nun das Azure Web PubSub SDK, um eine Nachricht im Dienst zu veröffentlichen. Navigieren Sie zum Verzeichnis */src/main/java/com/webpubsub/quickstart*, öffnen Sie die Datei *App.java* in Ihrem Editor, und ersetzen Sie den Code durch Folgendes:

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

    Der Aufruf `service.sendToAll()` sendet einfach eine Nachricht an alle verbundenen Clients in einem Hub.

4. Navigieren Sie zum Verzeichnis, das die Datei *pom.xml* enthält, und kompilieren Sie das Projekt mit dem folgenden `mvn`-Befehl.

    ```console
    mvn compile
    ```
5. Erstellen Sie dann das Paket.

    ```console
    mvn package
    ```
6. Führen Sie zum Ausführen der App den folgenden `mvn`-Befehl aus, und ersetzen Sie `<connection_string>` durch die **Verbindungszeichenfolge**, die Sie im [vorherigen Schritt](#get-the-connectionstring-for-future-use) abgerufen haben:

    ```console
    mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1' 'Hello World'"
    ```

7. Sie sehen, dass der vorherige CLI-Client die Nachricht empfangen hat.
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

---

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung erhalten Sie grundlegende Informationen dazu, wie Sie eine Verbindung mit dem Web PubSub-Dienst herstellen und Nachrichten auf den verbundenen Clients veröffentlichen.

In realen Anwendungen können Sie SDKs in verschiedenen Sprachen verwenden, um Ihre eigene Anwendung zu erstellen. Außerdem werden Funktionserweiterungen zur Verfügung gestellt, mit deren Hilfe Sie problemlos serverlose Anwendungen erstellen können.

[!INCLUDE [next step](includes/include-next-step.md)]
