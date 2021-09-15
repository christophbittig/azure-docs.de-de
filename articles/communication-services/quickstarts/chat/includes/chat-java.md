---
title: include file
description: Datei einfügen
services: azure-communication-services
author: probableprime
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: rifox
ms.openlocfilehash: b5ea5a90a6ad39404208bb9c353bb33e86201299
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967956"
---
## <a name="sample-code"></a>Beispielcode
Den fertigen Code für diese Schnellstartanleitung finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/chat-quickstart-java).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-install), Version 8 oder höher.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Eine bereitgestellte Communication Services-Ressource und eine Verbindungszeichenfolge. [Erstellen Sie eine Communication Services-Ressource](../../create-communication-resource.md).
- Ein [Benutzerzugriffstoken](../../access-tokens.md). Achten Sie darauf, dass Sie den Bereich auf „Chat“ festlegen, und notieren Sie sich die Tokenzeichenfolge und die userId-Zeichenfolge.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-java-application"></a>Erstellen einer neuen Java-Anwendung

Öffnen Sie Ihr Terminal- oder Befehlsfenster, und navigieren Sie zu dem Verzeichnis, in dem Sie Ihre Java-Anwendung erstellen möchten. Führen Sie den unten angegebenen Befehl aus, um das Java-Projekt aus der Vorlage „maven-archetype-quickstart“ zu generieren.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Sie sehen, dass über das Ziel „generate“ ein Verzeichnis mit dem gleichen Namen wie für die „artifactId“ erstellt wurde. In diesem Verzeichnis enthält `src/main/java directory` den Quellcode des Projekts, das Verzeichnis `src/test/java` enthält die Testquelle, und die Datei „pom.xml“ ist das Projektobjektmodell ([POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) des Projekts.

Aktualisieren Sie die POM-Datei Ihrer Anwendung für die Verwendung von Java 8 oder höher:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-sdk"></a>Hinzufügen der Paketverweise für das Chat-SDK

Verweisen Sie in Ihrer POM-Datei auf das Paket `azure-communication-chat` mit den Chat-APIs:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version><!-- Please refer to https://search.maven.org/artifact/com.azure/azure-communication-chat for the latest version --></version>
</dependency>
```

Für die Authentifizierung muss Ihr Client auf das Paket `azure-communication-common` verweisen:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version><!-- Please refer to https://search.maven.org/artifact/com.azure/azure-communication-common for the latest version --></version>
</dependency>
```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen werden für einige der wichtigsten Features des Chat-SDKs von Azure Communication Services für Java verwendet.

| Name                                  | Beschreibung                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Diese Klasse wird für die Chatfunktionalität benötigt. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Erstellen, Abrufen und Löschen von Threads. |
| ChatAsyncClient | Diese Klasse wird für die asynchrone Chatfunktionalität benötigt. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Erstellen, Abrufen und Löschen von Threads. |
| ChatThreadClient | Diese Klasse wird für die Chatthreadfunktionalität benötigt. Sie rufen eine Instanz über den ChatClient ab und verwenden sie zum Senden/Empfangen/Aktualisieren/Löschen von Nachrichten, Hinzufügen/Entfernen/Abrufen von Benutzern und Senden von Eingabebenachrichtigungen und Lesebestätigungen. |
| ChatThreadAsyncClient | Diese Klasse wird für die asynchrone Chatthreadfunktionalität benötigt. Sie rufen eine Instanz über den ChatAsyncClient ab und verwenden sie zum Senden/Empfangen/Aktualisieren/Löschen von Nachrichten, Hinzufügen/Entfernen/Abrufen von Benutzern und Senden von Eingabebenachrichtigungen und Lesebestätigungen. |

## <a name="create-a-chat-client"></a>Erstellen eines Chatclients
Zum Erstellen eines Chatclients verwenden Sie den Communication Services-Endpunkt und das Zugriffstoken, das im Rahmen der Schritte zur Erfüllung der Voraussetzungen generiert wurde. Mit Benutzerzugriffstoken können Sie Clientanwendungen erstellen, die gegenüber Azure Communication Services direkt authentifiziert werden. Nachdem Sie diese Token auf Ihrem Server generiert haben, übergeben Sie sie zurück an ein Clientgerät. Sie müssen die Klasse „CommunicationTokenCredential“ aus dem allgemeinen SDK verwenden, um das Token an Ihren Chatclient zu übergeben.

Weitere Informationen zur [Chatarchitektur](../../../concepts/chat/concepts.md)

Achten Sie beim Hinzufügen der Importanweisungen darauf, nur Importe aus den Namespaces „com.azure.communication.chat“ und „com.azure.communication.chat.models“ hinzuzufügen (nicht aus dem Namespace „com.azure.communication.chat.implementation“). In der Datei „App.java“, die über Maven generiert wurde, können Sie den folgenden Code verwenden, um zu beginnen:

```Java
package com.communication.quickstart;

import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.rest.PagedIterable;

import java.io.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");

        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationTokenCredential with the given access token, which is only valid until the token is valid
        CommunicationTokenCredential userCredential = new CommunicationTokenCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential);
        ChatClient chatClient = builder.buildClient();
    }
}
```

## <a name="start-a-chat-thread"></a>Starten eines Chatthreads

Verwenden Sie die `createChatThread`-Methode, um einen Chatthread zu erstellen.
`createChatThreadOptions` wird zum Beschreiben der Threadanforderung verwendet.

- Verwenden Sie `topic` des Konstruktors, um für diesen Chat ein Thema anzugeben. Nach dem Erstellen des Chatthreads kann das Thema mit der Funktion `UpdateThread` aktualisiert werden.
- Verwenden Sie `participants`, um die Threadteilnehmer aufzulisten, die dem Thread hinzugefügt werden sollen. Für `ChatParticipant` wird der Benutzer verwendet, den Sie in der Schnellstartanleitung [Benutzerzugriffstoken](../../access-tokens.md) erstellt haben.

`CreateChatThreadResult` ist die Antwort, die beim Erstellen eines Chatthreads zurückgegeben wird.
Sie enthält eine Methode `getChatThread()`, die das Objekt `ChatThread` zurückgibt, das verwendet werden kann, um den Threadclient zu erhalten, von dem Sie den `ChatThreadClient` zum Ausführen von Vorgängen mit dem erstellten Thread erhalten können: Hinzufügen von Teilnehmern, Senden einer Nachricht usw. Das `ChatThread`-Objekt enthält zudem die Methode `getId()`, die die eindeutige ID des Threads abruft.

```Java
CommunicationUserIdentifier identity1 = new CommunicationUserIdentifier("<USER_1_ID>");
CommunicationUserIdentifier identity2 = new CommunicationUserIdentifier("<USER_2_ID>");

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity1)
    .setDisplayName("Participant Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity2)
    .setDisplayName("Participant Display Name 2");

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions("Topic")
    .addParticipant(firstThreadParticipant)
    .addParticipant(secondThreadParticipant);

CreateChatThreadResult result = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = result.getChatThread().getId();
```

## <a name="list-chat-threads"></a>Auflisten von Chatthreads

Verwenden Sie die Methode `listChatThreads`, um eine Liste vorhandener Chatthreads abzurufen.

```java
PagedIterable<ChatThreadItem> chatThreads = chatClient.listChatThreads();

chatThreads.forEach(chatThread -> {
    System.out.printf("ChatThread id is %s.\n", chatThread.getId());
});
```

## <a name="get-a-chat-thread-client"></a>Abrufen eines Clients für den Chatthread

Die `getChatThreadClient`-Methode gibt einen Threadclient für einen bereits vorhandenen Thread zurück. Er kann verwendet werden, um Vorgänge wie das Hinzufügen von Teilnehmern oder das Senden einer Nachricht im erstellten Thread auszuführen. `chatThreadId` ist die eindeutige ID des vorhandenen Chatthreads.

```Java
ChatThreadClient chatThreadClient = chatClient.getChatThreadClient(chatThreadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Senden einer Nachricht an einen Chatthread

Verwenden Sie die `sendMessage`-Methode zum Senden einer Nachricht an den Thread, den Sie gerade erstellt haben und der anhand der „chatThreadId“ identifiziert wird.
`sendChatMessageOptions` wird verwendet, um die Anforderung der Chatnachricht zu beschreiben.

- Verwenden Sie `content`, um den Inhalt der Chatnachricht anzugeben.
- Verwenden Sie `type`, um den Inhaltstyp der Chatnachricht (Text oder HTML) anzugeben.
- Verwenden Sie `senderDisplayName`, um den Anzeigenamen des Absenders anzugeben.
- Verwenden Sie optional `metadata`, um alle zusätzlichen Daten einzubeziehen, die Sie zusammen mit der Nachricht senden möchten. Dieses Feld bietet Entwicklern einen Mechanismus, um die Chatnachrichtenfunktionalität zu erweitern und benutzerdefinierte Informationen für Ihren Anwendungsfall hinzuzufügen. Wenn Sie beispielsweise einen Dateilink in der Nachricht freigeben, sollten Sie „hasAttachment:true“ in den Metadaten hinzufügen, damit die Anwendung des Empfängers diese analysieren und entsprechend anzeigen kann.

Die Antwort `sendChatMessageResult` enthält eine `id`, bei der es sich um die eindeutige ID der Nachricht handelt.

```Java
Map<String, String> metadata = new HashMap<String, String>();
metadata.put("hasAttachment", "true");
metadata.put("attachmentUrl", "https://contoso.com/files/attachment.docx");

SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Please take a look at the attachment")
    .setType(ChatMessageType.TEXT)
    .setSenderDisplayName("Sender Display Name")
    .setMetadata(metadata);

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Empfangen von Chatnachrichten aus einem Chatthread

Sie können Chatnachrichten abrufen, indem Sie die `listMessages`-Methode auf dem Chatthreadclient jeweils nach dem angegebenen Intervallzeitraum abfragen.

```Java
chatThreadClient.listMessages().forEach(message -> {
    System.out.printf("Message id is %s.\n", message.getId());
});
```

`listMessages` gibt die aktuelle Version der Nachricht zurück, einschließlich aller Bearbeitungen oder Löschungen, die für die Nachricht mit „.editMessage()“ und „.deleteMessage()“ durchgeführt wurden. Für gelöschte Nachrichten wird von `chatMessage.getDeletedOn()` ein datetime-Wert zurückgegeben, mit dem der Löschzeitpunkt der Nachricht angegeben wird. Für bearbeitete Nachrichten gibt `chatMessage.getEditedOn()` einen datetime-Wert zurück, mit dem der Bearbeitungszeitpunkt der Nachricht angegeben wird. Auf den ursprünglichen Zeitpunkt der Nachrichtenerstellung kann mit `chatMessage.getCreatedOn()` zugegriffen werden. Dieses Element kann zum Sortieren der Nachrichten genutzt werden.

Weitere Informationen zu Nachrichtentypen finden Sie hier: [Nachrichtentypen](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Senden einer Lesebestätigung

Die Methode `sendReadReceipt` kann verwendet werden, um im Auftrag eines Benutzers ein Lesebestätigungsereignis in einem Thread zu veröffentlichen.
`chatMessageId` ist die eindeutige ID der Chatnachricht, die gelesen wurde.

```Java
String chatMessageId = message.getId();
chatThreadClient.sendReadReceipt(chatMessageId);
```

## <a name="list-chat-participants"></a>Auflisten von Chatteilnehmern

Verwenden Sie `listParticipants`, um eine in Seiten eingeteilte Sammlung mit den Teilnehmern des mit der chatThreadId identifizierten Chatthreads abzurufen.

```Java
PagedIterable<ChatParticipant> chatParticipantsResponse = chatThreadClient.listParticipants();
chatParticipantsResponse.forEach(chatParticipant -> {
    System.out.printf("Participant id is %s.\n", ((CommunicationUserIdentifier) chatParticipant.getCommunicationIdentifier()).getId());
});
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Hinzufügen eines Benutzers als Teilnehmer des Chatthreads

Nach der Erstellung eines Chatthreads können Sie dafür Benutzer hinzufügen und entfernen. Indem Sie Benutzer hinzufügen, gewähren Sie ihnen Zugriff zum Senden von Nachrichten an den Chatthread und zum Hinzufügen/Entfernen anderer Teilnehmer. Zunächst müssen Sie ein neues Zugriffstoken und eine Identität für den Benutzer abrufen. Stellen Sie vor dem Aufrufen der addParticipants-Methode sicher, dass Sie ein neues Zugriffstoken und eine Identität für den Benutzer beschafft haben. Der Benutzer benötigt dieses Zugriffstoken, um den Chatclient initialisieren zu können.

Verwenden Sie die Methode `addParticipants` zum Hinzufügen von Teilnehmern zum Thread.

- `communicationIdentifier` (erforderlich) ist das CommunicationIdentifier-Element, das Sie mit „CommunicationIdentityClient“ in der Schnellstartanleitung [Benutzerzugriffstoken](../../access-tokens.md) erstellt haben.
- `displayName` (optional) ist der Anzeigename für den Threadteilnehmer.
- `shareHistoryTime` (optional) ist der Zeitpunkt, ab dem der Chatverlauf für den Teilnehmer freigegeben wird. Sie können den Verlauf seit dem Beginn des Chatthreads freigeben, indem Sie diese Eigenschaft auf das Datum der Threaderstellung (oder früher) festlegen. Soll der Verlauf vor dem Hinzufügezeitpunkt des Teilnehmers nicht freigegeben werden, geben Sie das aktuelle Datum an. Geben Sie ein gewünschtes Datum an, um nur einen Teil des Verlaufs freizugeben.

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

CommunicationUserIdentifier identity3 = new CommunicationUserIdentifier("<USER_3_ID>");
CommunicationUserIdentifier identity4 = new CommunicationUserIdentifier("<USER_4_ID>");

ChatParticipant thirdThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity3)
    .setDisplayName("Display Name 3");

ChatParticipant fourthThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity4)
    .setDisplayName("Display Name 4");

participants.add(thirdThreadParticipant);
participants.add(fourthThreadParticipant);

chatThreadClient.addParticipants(participants);
```

## <a name="run-the-code"></a>Ausführen des Codes

Navigieren Sie zum Verzeichnis, das die Datei *pom.xml* enthält, und kompilieren Sie das Projekt mit dem folgenden `mvn`-Befehl.

```console
mvn compile
```

Erstellen Sie dann das Paket.

```console
mvn package
```

Führen Sie die App mit dem folgenden `mvn`-Befehl aus.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```