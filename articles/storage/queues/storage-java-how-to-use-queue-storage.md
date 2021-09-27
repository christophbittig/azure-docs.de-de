---
title: 'Verwenden von Queue Storage mit Java: Azure Storage'
description: Erfahren Sie, wie Sie Queue Storage zum Erstellen und Löschen von Warteschlangen verwenden. Erfahren Sie, wie Sie mit der Azure Storage-Clientbibliothek für Java Nachrichten einfügen, durchsuchen, abrufen und löschen können.
author: normesta
ms.author: normesta
ms.reviewer: dineshm
ms.date: 08/19/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-java
ms.openlocfilehash: 25734b7f4073895da832b3d020679c6f7b36a080
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665851"
---
# <a name="how-to-use-queue-storage-from-java"></a>Gewusst wie: Verwenden von Queue Storage mit Java

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Übersicht

In diesem Leitfaden wird das Codieren häufiger Szenarien mit dem Azure Queue Storage-Dienst veranschaulicht. Die Beispiele wurden in Java geschrieben und verwenden das [Azure Storage-SDK für Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage). Zu den Szenarien gehören **Einfügen**, **Einsehen**, **Abrufen** und **Löschen** von Warteschlangennachrichten. Der Code für das **Erstellen** und **Löschen** von Warteschlangen wird ebenfalls behandelt. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Erstellen einer Java-Anwendung

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

Überprüfen Sie zunächst, ob Ihr Entwicklungssystem die Voraussetzungen erfüllt, die in [Azure Queue Storage-Clientbibliothek v12 für Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue) aufgeführt sind.

So erstellen Sie eine Java-Anwendung namens `queues-how-to-v12`:

1. Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) Maven zum Erstellen einer neuen Konsolen-App mit dem Namen `queues-how-to-v12`. Geben Sie den folgenden `mvn`-Befehl ein, um ein „Hallo Welt!“-Java-Projekt zu erstellen.

   ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.howto \
        --define artifactId=queues-howto-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
   ```

   ```powershell
   mvn archetype:generate `
       --define interactiveMode=n `
       --define groupId=com.queues.howto `
       --define artifactId=queues-howto-v12 `
       --define archetypeArtifactId=maven-archetype-quickstart `
       --define archetypeVersion=1.4
   ```

1. Die Ausgabe der Erstellung des Projekts sollte in etwa wie folgt aussehen:

   ```console
   [INFO] Scanning for projects...
   [INFO]
   [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
   [INFO] Building Maven Stub Project (No POM) 1
   [INFO] --------------------------------[ pom ]---------------------------------
   [INFO]
   [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
   [INFO]
   [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
   [INFO]
   [INFO]
   [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
   [INFO] Generating project in Batch mode
   [INFO] ----------------------------------------------------------------------------
   [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
   [INFO] ----------------------------------------------------------------------------
   [INFO] Parameter: groupId, Value: com.queues.howto
   [INFO] Parameter: artifactId, Value: queues-howto-v12
   [INFO] Parameter: version, Value: 1.0-SNAPSHOT
   [INFO] Parameter: package, Value: com.queues.howto
   [INFO] Parameter: packageInPathFormat, Value: com/queues/howto
   [INFO] Parameter: version, Value: 1.0-SNAPSHOT
   [INFO] Parameter: package, Value: com.queues.howto
   [INFO] Parameter: groupId, Value: com.queues.howto
   [INFO] Parameter: artifactId, Value: queues-howto-v12
   [INFO] Project created from Archetype in dir: C:\queues\queues-howto-v12
   [INFO] ------------------------------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ------------------------------------------------------------------------
   [INFO] Total time:  6.775 s
   [INFO] Finished at: 2020-08-17T15:27:31-07:00
   [INFO] ------------------------------------------------------------------------
   ```

1. Wechseln Sie zum neu erstellten Verzeichnis `queues-howto-v12`.

   ```console
   cd queues-howto-v12
   ```

### <a name="install-the-package"></a>Installieren des Pakets

Öffnen Sie die Datei `pom.xml` in Ihrem Text-Editor. Fügen Sie der Gruppe „dependencies“ das folgende Abhängigkeitselement hinzu.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.6.0</version>
</dependency>
```

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

Überprüfen Sie zunächst, ob Ihr Entwicklungssystem die im [Azure Storage SDK für Java v8](https://github.com/azure/azure-storage-java) aufgeführten Voraussetzungen erfüllt. Befolgen Sie die Anweisungen zum Herunterladen und Installieren der Azure Storage-Bibliotheken für Java. Anschließend können Sie mithilfe der Beispiele in diesem Artikel eine Java-Anwendung erstellen.

---

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurieren Ihrer Anwendung für den Zugriff auf den Warteschlangenspeicher

Fügen Sie folgende Import-Anweisungen am Anfang der Java-Datei dort ein, wo Azure Storage-APIs auf Warteschlangen zugreifen sollen:

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Einrichten einer Azure-Speicherverbindungszeichenfolge

Ein Azure Storage-Client verwendet eine Speicherverbindungszeichenfolge für den Zugriff auf Datenverwaltungsdienste. Rufen Sie den Namen und den Primärzugriffsschlüssel für Ihr Speicherkonto im [Azure-Portal](https://portal.azure.com) ab. Verwenden Sie sie als die Werte `AccountName` und `AccountKey` in der Verbindungszeichenfolge. Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Verbindungszeichenfolge deklarieren:

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

```java
// Define the connection-string with your values.
final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Sie können diese Zeichenfolge in der Dienstkonfigurationsdatei namens `ServiceConfiguration.cscfg` speichern. Rufen Sie für eine App, die im Rahmen einer Microsoft Azure-Rolle ausgeführt wird, `RoleEnvironment.getConfigurationSettings` auf, um auf die Verbindungszeichenfolge zuzugreifen. Dieses Beispiel zeigt, wie die Verbindungszeichenfolge von einem `Setting`-Element mit der Bezeichnung `StorageConnectionString` abgerufen wird:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

---

In den folgenden Beispielen wird angenommen, dass Sie über ein `String`-Objekt verfügen, das die Speicherverbindungszeichenfolge enthält.

## <a name="how-to-create-a-queue"></a>Gewusst wie: Erstellen einer Warteschlange

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

Ein `QueueClient`-Objekt enthält die Vorgänge für die Interaktion mit einer Warteschlange. Der folgende Code erstellt ein `QueueClient`-Objekt. Verwenden Sie das Objekt `QueueClient`, um die zu verwendende Warteschlange zu erstellen.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_CreateQueue":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

Mit einem `CloudQueueClient`-Objekt können Sie Referenzobjekte für Warteschlangen abrufen. Der folgende Code erstellt ein `CloudQueueClient`-Objekt, das einen Verweis auf die Warteschlange bereitstellt, die Sie verwenden möchten. Sie können die Warteschlange erstellen, wenn sie nicht vorhanden ist.

> [!NOTE]
> Es gibt andere Möglichkeiten, `CloudStorageAccount`-Objekte zu erstellen. Weitere Informationen finden Sie unter `CloudStorageAccount` in der [Referenz zum Azure Storage-Client-SDK](https://azure.github.io/azure-sdk-for-java/storage.html).

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-add-a-message-to-a-queue"></a>Gewusst wie: Hinzufügen von Nachrichten zu einer Warteschlange

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

Um eine Nachricht in eine vorhandene Warteschlange einzufügen, rufen Sie die `sendMessage`-Methode auf. Eine Nachricht kann entweder eine Zeichenfolge (im UTF-8-Format) oder ein Bytearray sein. Dies ist der Code, der eine Zeichenfolgennachricht an die Warteschlange sendet.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_AddMessage":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

Um eine Nachricht in eine vorhandene Warteschlange einzufügen, erstellen Sie zuerst ein neues `CloudQueueMessage`-Objekt. Rufen Sie dann die Methode `addMessage` auf. Eine `CloudQueueMessage` kann aus einer Zeichenfolge (im UTF-8-Format) oder einem Bytearray erstellt werden. Mit diesem Code werden eine Warteschlange erstellt (falls noch nicht vorhanden) und die Nachricht `Hello, World` eingefügt.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-peek-at-the-next-message"></a>Gewusst wie: Einsehen der nächsten Nachricht

Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie `peekMessage` aufrufen.

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_PeekMessage":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Gewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Wenn die Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden, um den Status zu aktualisieren. Mit dem folgenden Code wird eine Warteschlangennachricht mit neuem Inhalt aktualisiert und das Sichtbarkeits-Zeitlimit um weitere 30 Sekunden verlängert. Die Verlängerung des Sichtbarkeits-Zeitlimits gibt dem Client weitere 30 Sekunden Zeit, um mit der Bearbeitung der Nachricht fortzufahren. Sie könnten auch die Anzahl der Wiederholungen beibehalten. Wenn die Nachricht mehr als *n* Mal wiederholt wird, würden Sie sie löschen. Dieses Szenario verhindert, dass eine Nachricht bei jeder Verarbeitung einen Anwendungsfehler auslöst.

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

Im folgenden Codebeispiel wird die Nachrichtenwarteschlange durchsucht, und es wird der erste Nachrichteninhalt ausfindig gemacht, der mit einer Suchzeichenfolge übereinstimmt. Anschließend wird der Nachrichteninhalt geändert, bevor sie verlassen wird.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateSearchMessage":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

Im folgenden Codebeispiel wird die Nachrichtenwartschlange durchsucht und der erste Nachrichteninhalt ausfindig gemacht, der mit `Hello, world` übereinstimmt. Anschließend wird der Nachrichteninhalt geändert und das Codebeispiel endet.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

Das folgende Codebeispiel aktualisiert einfach die erste sichtbare Nachricht in der Warteschlange.

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateFirstMessage":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-get-the-queue-length"></a>Gewusst wie: Abrufen der Warteschlangenlänge

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen.

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

Die `getProperties`-Methode gibt mehrere Werte einschließlich der Anzahl der Nachrichten zurück, die sich derzeit in einer Warteschlange befinden. Die Anzahl ist nur ein ungefährer Wert, da nach Ihrer Anforderung möglicherweise Nachrichten hinzugefügt oder gelöscht wurden. Mit der `getApproximateMessageCount`-Methode wird der letzte Wert zurückgegeben, der mit dem Aufruf von `getProperties` abgerufen wurde, ohne Queue Storage aufzurufen.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_GetQueueLength":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

Die `downloadAttributes`-Methode ruft mehrere Werte einschließlich der Anzahl der Nachrichten ab, die sich derzeit in einer Warteschlange befinden. Die Anzahl ist nur ein ungefährer Wert, da nach Ihrer Anforderung möglicherweise Nachrichten hinzugefügt oder gelöscht wurden. Mit der `getApproximateMessageCount`-Methode wird der letzte Wert zurückgegeben, der mit dem Aufruf von `downloadAttributes` abgerufen wurde, ohne Queue Storage aufzurufen.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-dequeue-the-next-message"></a>Gewusst wie: Entfernen der nächsten Nachricht aus der Warteschlange

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Wenn Sie `receiveMessage`aufrufen, wird die nächste Nachricht aus der Warteschlange abgerufen. Die für `receiveMessage` zurückgegebene Nachricht ist für anderen Code, mit dem Nachrichten aus dieser Warteschlange gelesen werden, nicht mehr sichtbar. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem `deleteMessage` aufrufen. Wenn Ihr Code eine Nachricht nicht verarbeiten kann, stellt dieser zweistufige Prozess sicher, dass Sie dieselbe Nachricht erneut erhalten und den Vorgang erneut ausführen können. In Ihrem Code wird `deleteMessage` direkt nach der Verarbeitung der Nachricht aufgerufen.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessage":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Wenn Sie `retrieveMessage`aufrufen, wird die nächste Nachricht aus der Warteschlange abgerufen. Die für `retrieveMessage` zurückgegebene Nachricht ist für anderen Code, mit dem Nachrichten aus dieser Warteschlange gelesen werden, nicht mehr sichtbar. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem `deleteMessage` aufrufen. Wenn Ihr Code eine Nachricht nicht verarbeiten kann, stellt dieser zweistufige Prozess sicher, dass Sie dieselbe Nachricht erneut erhalten und den Vorgang erneut ausführen können. In Ihrem Code wird `deleteMessage` direkt nach der Verarbeitung der Nachricht aufgerufen.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="additional-options-for-dequeuing-messages"></a>Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange

Es gibt zwei Möglichkeiten, das Abrufen von Nachrichten aus der Warteschlange anzupassen. Rufen Sie zunächst einen Batch von Nachrichten ab (bis zu 32). Dann können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt.

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

Im folgenden Codebeispiel wird `receiveMessages` verwendet, um 20 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer `for`-Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten (300 Sekunden) pro Nachricht festgelegt. Das Zeitlimit beginnt für alle Nachrichten gleichzeitig. Wenn seit dem Aufruf von `receiveMessages` fünf Minuten vergangen sind, werden alle nicht gelöschten Nachrichten wieder sichtbar.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessages":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

Im folgenden Codebeispiel wird `retrieveMessages` verwendet, um 20 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer `for`-Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten (300 Sekunden) pro Nachricht festgelegt. Das Zeitlimit beginnt für alle Nachrichten gleichzeitig. Wenn seit dem Aufruf von `retrieveMessages` fünf Minuten vergangen sind, werden alle nicht gelöschten Nachrichten wieder sichtbar.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-list-the-queues"></a>Gewusst wie: Auflisten der Warteschlangen

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

Rufen Sie zum Abrufen einer Liste der aktuellen Warteschlangen die Methode `QueueServiceClient.listQueues()` auf, die eine Sammlung von `QueueItem`-Objekten zurückgibt.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ListQueues":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

Rufen Sie zum Abrufen einer Liste der aktuellen Warteschlangen die Methode `CloudQueueClient.listQueues()` auf, die eine Sammlung von `CloudQueue`-Objekten zurückgibt.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-delete-a-queue"></a>Gewusst wie: Löschen einer Warteschlange

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode `delete` für das `QueueClient`-Objekt auf.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DeleteMessageQueue":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode `deleteIfExists` für das `CloudQueue`-Objekt auf.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Queue Storage vertraut gemacht haben, folgen Sie diesen Links, um mehr über komplexere Speicheraufgaben zu erfahren.

- [Azure Storage-SDK für Java](https://github.com/Azure/Azure-SDK-for-Java)
- [Referenz zum Azure Storage-Client-SDK](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage)
- [REST-API für Azure Storage-Dienste](/rest/api/storageservices/)
- [Azure Storage-Teamblog](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog)
