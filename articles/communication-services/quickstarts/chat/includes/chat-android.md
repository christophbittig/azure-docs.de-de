---
title: Datei einfügen
description: Datei einfügen
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 41a25c5b92bfc91379ecf9c869a6c1dbbb7c9680
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "114201226"
---
## <a name="sample-code"></a>Beispielcode
Den fertigen Code für diese Schnellstartanleitung finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/Add-chat).

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die folgenden Schritte aus, bevor Sie beginnen:

- Erstellen Sie ein Azure-Konto mit einem aktiven Abonnement. Details finden Sie auf der [Seite zum Erstellen eines kostenloses Azure-Kontos](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installieren Sie [Android Studio](https://developer.android.com/studio). Damit erstellen Sie in diesem Schnellstart eine Android-Anwendung zum Installieren von Abhängigkeiten.
- Erstellen Sie eine Azure Communication Services-Ressource. Ausführlichere Informationen hierzu finden Sie unter [Schnellstart: Erstellen und Verwalten einer Communication Services-Ressource](../../create-communication-resource.md). Für diese Schnellstartanleitung müssen Sie den **Endpunkt** Ihrer Ressource aufzeichnen.
- Erstellen Sie **zwei** Communication Services-Benutzer und [Benutzerzugriffstoken](../../access-tokens.md) dafür. Stellen Sie sicher, dass Sie als Bereich **Chat** festlegen, und **notieren Sie sich die Tokenzeichenfolge und die userId-Zeichenfolge**. In diesem Schnellstart erstellen Sie einen Thread mit einem Teilnehmer und fügen anschließend einen zweiten Teilnehmer hinzu.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-android-application"></a>Erstellen einer neuen Android-Anwendung

1. Öffnen Sie Android Studio, und klicken Sie auf `Create a new project` (Neues Projekt erstellen). 
2. Wählen Sie im nächsten Fenster die Projektvorlage `Empty Activity` (Leere Aktivität) aus.
3. Geben Sie bei der Auswahl der Optionen `ChatQuickstart` als Projektnamen ein.
4. Klicken Sie auf „Weiter“, und wählen Sie das Verzeichnis aus, in dem Sie das Projekt erstellen möchten.

### <a name="install-the-libraries"></a>Installieren der Bibliotheken

Für die Installation der erforderlichen Communication Services-Abhängigkeiten wird in diesem Schnellstart Gradle verwendet. Navigieren Sie in der Befehlszeile zum Stammverzeichnis des Projekts `ChatQuickstart`. Öffnen Sie die Datei „build.gradle“ der App, und fügen Sie die folgenden Abhängigkeiten für das `ChatQuickstart`-Ziel hinzu:

```
implementation 'com.azure.android:azure-communication-common:1.0.1'
implementation 'com.azure.android:azure-communication-chat:1.0.0'
implementation 'org.slf4j:slf4j-log4j12:1.7.29'
```

#### <a name="exclude-meta-files-in-packaging-options-in-root-buildgradle"></a>Ausschließen von Metadatendateien in den Paketoptionen der Stammdatei „build.gradle“
```
android {
   ...
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/license'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/notice'
        exclude 'META-INF/ASL2.0'
        exclude("META-INF/*.md")
        exclude("META-INF/*.txt")
        exclude("META-INF/*.kotlin_module")
    }
}
```

#### <a name="alternative-to-install-libraries-through-maven"></a>(Alternative) So installieren Sie Bibliotheken über Maven
Wenn Sie die Bibliothek über das [Maven](https://maven.apache.org/)-Buildsystem in Ihr Projekt importieren möchten, fügen Sie sie in der Datei `pom.xml` Ihrer App im Abschnitt `dependencies` hinzu, und geben Sie dabei die Artefakt-ID und die Version an, die Sie verwenden möchten:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>Einrichten der Platzhalter

Öffnen Sie die Datei `MainActivity.java`, und bearbeiten Sie sie. In diesem Schnellstart fügen Sie den Code zu `MainActivity` hinzu und sehen sich die Ausgabe in der Konsole an. Das Erstellen einer Benutzeroberfläche wird nicht behandelt. Importieren Sie am Anfang der Datei `Communication common`, `Communication chat` und andere Systembibliotheken:

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.chat.models.*;
import com.azure.android.communication.common.*;

import android.os.Bundle;
import android.util.Log;
import android.widget.Toast;

import com.jakewharton.threetenabp.AndroidThreeTen;

import java.util.ArrayList;
import java.util.List;
```

Kopieren Sie in der Datei `MainActivity.java` den folgenden Code in die Klasse `MainActivity`:

```java
    private String endpoint = "https://<resource>.communication.azure.com";
    private String firstUserId = "<first_user_id>";
    private String secondUserId = "<second_user_id>";
    private String firstUserAccessToken = "<first_user_access_token>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0";
    private static final String APPLICATION_ID = "Chat Quickstart App";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "Chat Quickstart App";

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
   @Override
    protected void onStart() {
        super.onStart();
        try {
            AndroidThreeTen.init(this);

            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>
            
            // <RECEIVE CHAT MESSAGES>

            // <ADD A USER>

            // <LIST USERS>

            // <REMOVE A USER>
            
            // <<SEND A TYPING NOTIFICATION>>
            
            // <<SEND A READ RECEIPT>>
               
            // <<LIST READ RECEIPTS>>
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

1. Ersetzen Sie `<resource>` durch Ihre Communication Services-Ressource.
2. Ersetzen Sie `<first_user_id>` und `<second_user_id>` durch gültige Communication Services-Benutzer-IDs, die im Rahmen Vorbereitung generiert wurden.
3. Ersetzen Sie `<first_user_access_token>` durch das Communication Services-Zugriffstoken für `<first_user_id>`, das im Rahmen Vorbereitung generiert wurde.

In den folgenden Schritten werden die Platzhalter durch Beispielcode ersetzt. Dabei verwenden wir die Chatbibliothek von Azure Communication Services.


### <a name="create-a-chat-client"></a>Erstellen eines Chatclients

Ersetzen Sie den Kommentar `<CREATE A CHAT CLIENT>` durch den folgenden Code (fügen Sie die Importanweisungen oben in der Datei ein):

```java
import com.azure.android.core.http.policy.UserAgentPolicy;

ChatAsyncClient chatAsyncClient = new ChatClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationTokenCredential(firstUserAccessToken))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .buildAsyncClient();

```

## <a name="object-model"></a>Objektmodell
Die folgenden Klassen und Schnittstellen werden für einige der wichtigsten Features des JavaScript-SDKs für Chats von Azure Communication Services verwendet.

| name                                   | BESCHREIBUNG                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | Diese Klasse wird für die Chatfunktionalität benötigt. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Erstellen, Abrufen und Löschen von Threads sowie zum Abonnieren von Chatereignissen. |
| ChatThreadClient/ChatThreadAsyncClient | Diese Klasse wird für die Chatthreadfunktionalität benötigt. Sie rufen eine Instanz über den ChatClient ab und verwenden sie zum Senden/Empfangen/Aktualisieren/Löschen von Nachrichten, Hinzufügen/Entfernen/Abrufen von Benutzern und Senden von Eingabebenachrichtigungen und Lesebestätigungen. |

## <a name="start-a-chat-thread"></a>Starten eines Chatthreads

Mit `ChatAsyncClient` erstellen Sie einen neuen Thread mit zunächst einem Benutzer.

Ersetzen Sie den Kommentar `<CREATE A CHAT THREAD>` durch folgenden Code:

```java
// A list of ChatParticipant to start the thread with.
List<ChatParticipant> participants = new ArrayList<>();
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(firstUserId))
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// Optional, set a repeat request ID.
final String repeatabilityRequestID = "";
// Options to pass to the create method.
CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic(topic)
    .setParticipants(participants)
    .setIdempotencyToken(repeatabilityRequestID);

CreateChatThreadResult createChatThreadResult =
    chatAsyncClient.createChatThread(createChatThreadOptions).get();
ChatThreadProperties chatThreadProperties = createChatThreadResult.getChatThreadProperties();
threadId = chatThreadProperties.getId();

```

## <a name="get-a-chat-thread-client"></a>Abrufen eines Clients für den Chatthread

Nachdem Sie nun einen Chatthread erstellt haben, rufen Sie als Nächstes einen Chatthreadclient (`ChatThreadAsyncClient`) ab, um in diesem Thread Vorgänge ausführen zu können. Ersetzen Sie den Kommentar `<CREATE A CHAT THREAD CLIENT>` durch folgenden Code:

```
ChatThreadAsyncClient chatThreadAsyncClient = new ChatThreadClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationTokenCredential(firstUserAccessToken))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .chatThreadId(threadId)
    .buildAsyncClient();

```

## <a name="send-a-message-to-a-chat-thread"></a>Senden einer Nachricht an einen Chatthread

Als Nächstes wird eine Nachricht an diesen Thread gesendet.

Ersetzen Sie den Kommentar `<SEND A MESSAGE>` durch folgenden Code:

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageOptions chatMessageOptions = new SendChatMessageOptions()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName);

// A string is the response returned from sending a message, it is an id, which is the unique ID of the message.
chatMessageId = chatThreadAsyncClient.sendMessage(chatMessageOptions).get().getId();

```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Empfangen von Chatnachrichten aus einem Chatthread
Bei der Echtzeitsignalisierung können Sie neue eingehende Nachrichten abonnieren und die aktuellen Nachrichten im Arbeitsspeicher entsprechend aktualisiert. Azure Communication Services unterstützt eine [Liste mit Ereignissen, die Sie abonnieren können](../../../concepts/chat/concepts.md#real-time-notifications).

Ersetzen Sie den Kommentar `<RECEIVE CHAT MESSAGES>` durch den folgenden Code (fügen Sie die Importanweisungen oben in der Datei ein):

```java

// Start real time notification
chatAsyncClient.startRealtimeNotifications(firstUserAccessToken, getApplicationContext());

// Register a listener for chatMessageReceived event
chatAsyncClient.addEventHandler(ChatEventType.CHAT_MESSAGE_RECEIVED, (ChatEvent payload) -> {
    ChatMessageReceivedEvent chatMessageReceivedEvent = (ChatMessageReceivedEvent) payload;
    // You code to handle chatMessageReceived event
    
});

```

> [!IMPORTANT]
> Bekanntes Problem: Bei gemeinsamer Verwendung von Android Chat und Calling SDK in der gleichen Anwendung funktioniert das Echtzeitbenachrichtigungsfeature des Chat SDK nicht. Möglicherweise tritt ein Problem beim Auflösen der Abhängigkeit auf.
> Wir arbeiten bereits an einer Lösung. Bis diese zur Verfügung steht, können Sie das Echtzeitbenachrichtigungsfeature deaktivieren, indem Sie in der Datei „build.gradle“ der App die folgenden Abhängigkeitsinformationen hinzufügen und stattdessen die GetMessages-API abfragen, um eingehende Nachrichten für Benutzer anzuzeigen: 
> 
> ```
> implementation ("com.azure.android:azure-communication-chat:1.0.0") {
>     exclude group: 'com.microsoft', module: 'trouter-client-android'
> }
> implementation 'com.azure.android:azure-communication-calling:1.0.0'
> ```
> 
> Beachten Sie, dass bei der obigen Aktualisierung ein Laufzeitfehler auftritt, wenn die Anwendung versucht, eine der Benachrichtigungs-APIs (etwa `chatAsyncClient.startRealtimeNotifications()` oder `chatAsyncClient.addEventHandler()`) zu verwenden.


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Hinzufügen eines Benutzers als Teilnehmer des Chatthreads

Ersetzen Sie den Kommentar `<ADD A USER>` durch folgenden Code:

```java
// The display name for the thread participant.
String secondUserDisplayName = "a new participant";
ChatParticipant participant = new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(secondUserId))
    .setDisplayName(secondUserDisplayName);
        
chatThreadAsyncClient.addParticipant(participant);

```


## <a name="list-users-in-a-thread"></a>Auflisten der Benutzer in einem Thread

Ersetzen Sie den Kommentar `<LIST USERS>` durch den folgenden Code. (Platzieren Sie die Importanweisungen am Anfang der Datei.)

```java
import com.azure.android.core.rest.util.paging.PagedAsyncStream;
import com.azure.android.core.util.RequestContext;

// The maximum number of participants to be returned per page, optional.
int maxPageSize = 10;

// Skips participants up to a specified position in response.
int skip = 0;

// Options to pass to the list method.
ListParticipantsOptions listParticipantsOptions = new ListParticipantsOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedAsyncStream<ChatParticipant> participantsPagedAsyncStream =
      chatThreadAsyncClient.listParticipants(listParticipantsOptions, RequestContext.NONE);

participantsPagedAsyncStream.forEach(chatParticipant -> {
    // You code to handle participant
});

```


## <a name="remove-user-from-a-chat-thread"></a>Entfernen eines Benutzers aus einem Chatthread

In diesem Abschnitt wird der zweite Benutzer aus dem Thread entfernt.

Ersetzen Sie den `<REMOVE A USER>`-Kommentar durch folgenden Code:

```java
// Using the unique ID of the participant.
chatThreadAsyncClient.removeParticipant(new CommunicationUserIdentifier(secondUserId)).get();

```

## <a name="send-a-typing-notification"></a>Senden einer Tippbenachrichtigung

Ersetzen Sie den `<SEND A TYPING NOTIFICATION>`-Kommentar durch folgenden Code:

```java
chatThreadAsyncClient.sendTypingNotification().get();
```

## <a name="send-a-read-receipt"></a>Senden einer Lesebestätigung

Wir senden eine Lesebestätigung für die oben gesendete Nachricht.

Ersetzen Sie den `<SEND A READ RECEIPT>`-Kommentar durch folgenden Code:

```java
chatThreadAsyncClient.sendReadReceipt(chatMessageId).get();
```

## <a name="list-read-receipts"></a>Auflisten von Lesebestätigungen

Ersetzen Sie den `<READ RECEIPTS>`-Kommentar durch folgenden Code:

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
// Options to pass to the list method.
ListReadReceiptOptions listReadReceiptOptions = new ListReadReceiptOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedAsyncStream<ChatMessageReadReceipt> readReceiptsPagedAsyncStream =
      chatThreadAsyncClient.listReadReceipts(listReadReceiptOptions, RequestContext.NONE);

readReceiptsPagedAsyncStream.forEach(readReceipt -> {
    // You code to handle readReceipt
});

```

## <a name="run-the-code"></a>Ausführen des Codes

Klicken Sie in Android Studio auf die Schaltfläche „Run“ (Ausführen), um das Projekt zu erstellen und auszuführen. In der Konsole können Sie sich die Ausgabe des Codes sowie die Protokollierungsausgabe des Chatclients ansehen.
