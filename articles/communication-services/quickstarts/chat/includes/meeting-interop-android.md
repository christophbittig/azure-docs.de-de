---
title: 'Schnellstart: Beitreten zu einer Teams-Besprechung'
author: agiurg
ms.author: agiurg
ms.date: 07/20/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: bb5ccb022b4bfeb90f7c50f27ce2a76d43386571
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378260"
---
In diesem Schnellstart erfahren Sie, wie Sie mit dem Chat SDK für Android von Azure Communication Services an einer Teams-Besprechung teilnehmen.

## <a name="prerequisites"></a>Voraussetzungen 

- Eine  [Teams-Bereitstellung](/deployoffice/teams-install). 
- Eine funktionierende [Anruf-App](../../voice-video-calling/get-started-teams-interop.md). 

## <a name="enable-teams-interoperability"></a>Aktivieren der Teams-Interoperabilität 

Ein Communication Services-Benutzer, der einer Teams-Besprechung als Gastbenutzer hinzugefügt wird, kann nur auf den Chat der Besprechung zugreifen, wenn er der Teams-Telefonkonferenz beitritt. Weitere Informationen zum Hinzufügen eines Communication Services-Benutzers zu einer Teams-Telefonkonferenz finden Sie in der Dokumentation zur [Teams-Interoperabilität](../../voice-video-calling/get-started-teams-interop.md).

Sie müssen Mitglied der besitzenden Organisation beider Entitäten sein, um dieses Feature verwenden zu können.

## <a name="joining-the-meeting-chat"></a>Beitritt zum Besprechungschat 

Sobald die Teams-Interoperabilität aktiviert ist, können Communication Services-Benutzer über die Callin SDK als externe Benutzer am Teams-Anruf teilnehmen. Durch das Beitreten zur Anrufkonferenz wird er dem Besprechungschat als Teilnehmer hinzugefügt und kann Nachrichten mit anderen Benutzern der Anrufkonferenz austauschen. Der Benutzer besitzt keinen Zugriff auf Chatnachrichten, die gesendet wurden, bevor er der Anrufkonferenz beigetreten ist. Führen Sie die folgenden Schritte aus, um an der Besprechung teilzunehmen und mit dem Chatten zu beginnen.

## <a name="add-chat-to-the-teams-calling-app"></a>Hinzufügen von Chat zur Anruf-App von Teams

Fügen Sie in „build.gradle“ auf Modulebene die Abhängigkeit vom Chat SDK hinzu.

> [!IMPORTANT]
> Bekanntes Problem: Bei gemeinsamer Verwendung von Android Chat SDK und Calling SDK in der gleichen Anwendung funktioniert das Echtzeitbenachrichtigungsfeature des Chat SDK nicht. Es wird ein Problem mit der Abhängigkeitsauflösung angezeigt. Während wir an einer Lösung arbeiten, können Sie das Feature für Echtzeitbenachrichtigungen deaktivieren, indem Sie der Chat SDK-Abhängigkeit in der `build.gradle`-Datei der App die folgenden Ausschlüsse hinzufügen:
> 
> ```groovy
> implementation ("com.azure.android:azure-communication-chat:1.0.0") {
>     exclude group: 'com.microsoft', module: 'trouter-client-android'
> }
> ```


## <a name="add-the-teams-ui-layout"></a>Hinzufügen des Layouts der Teams-Benutzeroberfläche

Ersetzen Sie den Code in „activity_main.xml“ durch den folgenden Codeausschnitt. Dadurch werden Eingaben für die Thread-ID und für das Senden von Nachrichten, eine Schaltfläche zum Senden der eingegebenen Nachricht und ein einfaches Chatlayout hinzugefügt.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/teams_meeting_thread_id"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="128dp"
        android:ems="10"
        android:hint="Meeting Thread Id"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <EditText
        android:id="@+id/teams_meeting_link"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="64dp"
        android:ems="10"
        android:hint="Teams meeting link"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <LinearLayout
        android:id="@+id/button_layout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="30dp"
        android:gravity="center"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.0"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/teams_meeting_thread_id">

        <Button
            android:id="@+id/join_meeting_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Join Meeting" />

        <Button
            android:id="@+id/hangup_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hangup" />

    </LinearLayout>

    <TextView
        android:id="@+id/call_status_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="40dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <TextView
        android:id="@+id/recording_status_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="20dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <ScrollView
        android:id="@+id/chat_box"
        android:layout_width="374dp"
        android:layout_height="294dp"
        android:layout_marginTop="40dp"
        android:layout_marginBottom="20dp"
        app:layout_constraintBottom_toTopOf="@+id/send_message_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/button_layout"
        android:orientation="vertical"
        android:gravity="bottom"
        android:layout_gravity="bottom"
        android:fillViewport="true">

        <LinearLayout
            android:id="@+id/chat_box_layout"
            android:orientation="vertical"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:gravity="bottom"
            android:layout_gravity="top"
            android:layout_alignParentBottom="true"/>
    </ScrollView>

    <EditText
        android:id="@+id/message_body"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="588dp"
        android:ems="10"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        tools:text="Type your message here..."
        tools:visibility="invisible" />

    <Button
        android:id="@+id/send_message_button"
        android:layout_width="138dp"
        android:layout_height="45dp"
        android:layout_marginStart="133dp"
        android:layout_marginTop="48dp"
        android:layout_marginEnd="133dp"
        android:text="Send Message"
        android:visibility="invisible"
        app:layout_constraintBottom_toTopOf="@+id/recording_status_bar"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.428"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/chat_box" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

## <a name="enable-the-teams-ui-controls"></a>Aktivieren der Steuerelemente der Teams-Benutzeroberfläche

### <a name="import-packages-and-define-state-variables"></a>Importieren von Paketen und Definieren von Zustandsvariablen

Fügen Sie dem Inhalt von `MainActivity.java` die folgenden zusätzlichen Importe hinzu:

```
import android.graphics.Typeface;
import android.graphics.Color;
import android.text.Html;
import android.os.Handler;
import android.view.Gravity;
import android.view.View;
import android.widget.LinearLayout;
import java.util.Collections;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.TimeUnit;
import com.azure.android.communication.chat.ChatThreadAsyncClient;
import com.azure.android.communication.chat.ChatThreadClientBuilder;
import com.azure.android.communication.chat.models.ChatMessage;
import com.azure.android.communication.chat.models.ChatMessageType;
import com.azure.android.communication.chat.models.ChatParticipant;
import com.azure.android.communication.chat.models.ListChatMessagesOptions;
import com.azure.android.communication.chat.models.SendChatMessageOptions;
import com.azure.android.communication.common.CommunicationIdentifier;
import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.core.rest.util.paging.PagedAsyncStream;
import com.azure.android.core.util.AsyncStreamHandler;
```

Fügen Sie der Klasse `MainActivity` die folgenden Variablen hinzu:

```
    // InitiatorId is used to differentiate incoming messages from outgoing messages
    private static final String InitiatorId = "<USER_ID>";
    private static final String ResourceUrl = "<COMMUNICATION_SERVICES_RESOURCE_ENDPOINT>";
    private String threadId;
    private ChatThreadAsyncClient chatThreadAsyncClient;
    
    // The list of ids corresponsding to messages which have already been processed
    ArrayList<String> chatMessages = new ArrayList<>();
```

Ersetzen Sie `<USER_ID>` durch die ID des Benutzers, der den Chat initiiert.
Ersetzen Sie `<COMMUNICATION_SERVICES_RESOURCE_ENDPOINT>` durch den Endpunkt Ihrer Communication Services-Ressource. 

### <a name="initialize-the-chatthreadclient"></a>Initialisieren von ChatThreadClient

Instanziieren Sie nach dem Beitritt zur Besprechung den `ChatThreadClient`, und machen Sie die Chatkomponenten sichtbar.

Aktualisieren Sie das Ende der `MainActivity.joinTeamsMeeting()`-Methode mit dem folgenden Code:

```
    private void joinTeamsMeeting() {
        ...
        EditText threadIdView = findViewById(R.id.teams_meeting_thread_id);
        threadId = threadIdView.getText().toString();
        // Initialize Chat Thread Client
        chatThreadAsyncClient = new ChatThreadClientBuilder()
                .endpoint(ResourceUrl)
                .credential(new CommunicationTokenCredential(UserToken))
                .chatThreadId(threadId)
                .buildAsyncClient();
        Button sendMessageButton = findViewById(R.id.send_message_button);
        EditText messageBody = findViewById(R.id.message_body);
        // Register the method for sending messages and toggle the visibility of chat components
        sendMessageButton.setOnClickListener(l -> sendMessage());
        sendMessageButton.setVisibility(View.VISIBLE);
        messageBody.setVisibility(View.VISIBLE);
        
        // Start the polling for chat messages immediately
        handler.post(runnable);
    }
```

### <a name="enable-sending-messages"></a>Aktivieren des Sendens von Nachrichten

Fügen Sie die `sendMessage()`-Methode zu `MainActivity` hinzu. Diese Funktion verwendet `ChatThreadClient`, um Nachrichten im Namen des Benutzers zu senden.

```
    private void sendMessage() {
        // Retrieve the typed message content
        EditText messageBody = findViewById(R.id.message_body);
        // Set request options and send message
        SendChatMessageOptions options = new SendChatMessageOptions();
        options.setContent(messageBody.getText().toString());
        options.setSenderDisplayName("ACS User");
        chatThreadAsyncClient.sendMessage(options);
        // Clear the text box
        messageBody.setText("");
    }
```

### <a name="enable-polling-for-messages-and-rendering-them-in-the-application"></a>Aktivieren des Abrufs für Nachrichten und Rendern von Nachrichten in der Anwendung

> [!IMPORTANT]
> Bekanntes Problem: Da das Echtzeitbenachrichtigungsfeature des Chat SDK nicht mit den Calling SDKs funktioniert, muss die `GetMessages`-API in vordefinierten Intervallen abgefragt werden. In unserem Beispiel verwenden wir Intervalle von 3 Sekunden.

Wir können die folgenden Daten aus der von der `GetMessages`-API zurückgegebenen Nachrichtenliste abrufen: 
 - Die `text`- und `html`-Nachrichten im Thread seit dem Beitritt
 - Änderungen an der Threadliste
 - Aktualisierungen des Threadthemas


Fügen Sie der `MainActivity`-Klasse einen Handler und eine ausführbare Aufgabe hinzu, die in Intervallen von 3 Sekunden ausgeführt wird:

```
    private Handler handler = new Handler();
    private Runnable runnable = new Runnable() {
        @Override
        public void run() {
            try {
                retrieveMessages();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            // Repeat every 3 seconds
            handler.postDelayed(runnable, 3000);
        }
    };
```

Beachten Sie, dass die Aufgabe bereits am Ende der `MainActivity.joinTeamsMeeting()`-Methode gestartet wurde, die im Initialisierungsschritt aktualisiert wurde.

Abschließend fügen wir die Methode hinzu, um alle zugänglichen Nachrichten im Thread abzufragen, sie anhand des Nachrichtentyps zu analysieren und die `html`- und `text`-Nachrichten anzuzeigen:

```
    private void retrieveMessages() throws InterruptedException {
        // Initialize the list of messages not yet processed
        ArrayList<ChatMessage> newChatMessages = new ArrayList<>();
        
        // Retrieve all messages accessible to the user
        PagedAsyncStream<ChatMessage> messagePagedAsyncStream
                = this.chatThreadAsyncClient.listMessages(new ListChatMessagesOptions(), null);
        // Set up a lock to wait until all returned messages have been inspected
        CountDownLatch latch = new CountDownLatch(1);
        // Traverse the returned messages
        messagePagedAsyncStream.forEach(new AsyncStreamHandler<ChatMessage>() {
            @Override
            public void onNext(ChatMessage message) {
                // Messages that should be displayed in the chat
                if ((message.getType().equals(ChatMessageType.TEXT)
                    || message.getType().equals(ChatMessageType.HTML))
                    && !chatMessages.contains(message.getId())) {
                    newChatMessages.add(message);
                    chatMessages.add(message.getId());
                }
                if (message.getType().equals(ChatMessageType.PARTICIPANT_ADDED)) {
                    // Handle participants added to chat operation
                    List<ChatParticipant> participantsAdded = message.getContent().getParticipants();
                    CommunicationIdentifier participantsAddedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
                if (message.getType().equals(ChatMessageType.PARTICIPANT_REMOVED)) {
                    // Handle participants removed from chat operation
                    List<ChatParticipant> participantsRemoved = message.getContent().getParticipants();
                    CommunicationIdentifier participantsRemovedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
                if (message.getType().equals(ChatMessageType.TOPIC_UPDATED)) {
                    // Handle topic updated
                    String newTopic = message.getContent().getTopic();
                    CommunicationIdentifier topicUpdatedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
            }
            @Override
            public void onError(Throwable throwable) {
                latch.countDown();
            }
            @Override
            public void onComplete() {
                latch.countDown();
            }
        });
        // Wait until the operation completes
        latch.await(1, TimeUnit.MINUTES);
        // Returned messages should be ordered by the createdOn field to be guaranteed a proper chronological order
        // For the purpose of this demo we will just reverse the list of returned messages
        Collections.reverse(newChatMessages);
        for (ChatMessage chatMessage : newChatMessages)
        {
            LinearLayout chatBoxLayout = findViewById(R.id.chat_box_layout);
            // For the purpose of this demo UI, we don't need to use HTML formatting for displaying messages
            // The Teams client always sends html messages in meeting chats 
            String message = Html.fromHtml(chatMessage.getContent().getMessage(), Html.FROM_HTML_MODE_LEGACY).toString().trim();
            TextView messageView = new TextView(this);
            messageView.setText(message);
            // Compare with sender identifier and align LEFT/RIGHT accordingly
            // ACS users are of type CommunicationUserIdentifier
            CommunicationIdentifier senderId = chatMessage.getSenderCommunicationIdentifier();
            if (senderId instanceof CommunicationUserIdentifier
                && InitiatorId.equals(((CommunicationUserIdentifier) senderId).getId())) {
                messageView.setTextColor(Color.GREEN);
                messageView.setGravity(Gravity.RIGHT);
            } else {
                messageView.setTextColor(Color.BLUE);
                messageView.setGravity(Gravity.LEFT);
            }
            // Note: messages with the deletedOn property set to a timestamp, should be marked as deleted
            // Note: messages with the editedOn property set to a timestamp, should be marked as edited
            messageView.setTypeface(Typeface.SANS_SERIF, Typeface.BOLD);
            chatBoxLayout.addView(messageView);
        }
    }
```

Die Anzeigenamen der Chatthread-Teilnehmer werden nicht vom Teams-Client festgelegt. Beim Ereignis `participantsAdded` und beim Ereignis `participantsRemoved` werden die Namen in der API für die Auflistung von Teilnehmern als NULL zurückgegeben. Die Anzeigenamen der Chatteilnehmer können aus dem `remoteParticipants`-Feld des `call`-Objekts abgerufen werden.

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Abrufen eines Teams-Besprechungschatthreads für einen Communication Services-Benutzer

Die Details für die Teams-Besprechung können über Graph-APIs abgerufen werden, wie in der [Graph-Dokumentation](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) ausführlich erläutert. Das Communication Services Calling SDK akzeptiert einen vollständigen Teams-Besprechungslink oder eine Besprechungs-ID. Beide werden als Teil der `onlineMeeting`-Ressource zurückgegeben, auf die unter der [`joinWebUrl`-Eigenschaft](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) zugegriffen werden kann.

Sie können die `threadID` auch mit den [Graph-APIs](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) abrufen. Die Antwort enthält ein `chatInfo`-Objekt, das die `threadID` enthält.

Sie können die erforderlichen Besprechungsinformationen und die Thread-ID auch über die URL unter **An Besprechung teilnehmen** in der Einladung zur Teams-Besprechung abrufen.
Ein Teams-Besprechungslink sieht wie folgt aus: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. Die `threadId` befindet sich dort, wo im Link `meeting_chat_thread_id` steht. Stellen Sie sicher, dass vor der Verwendung Escapezeichen aus der `meeting_chat_thread_id` entfernt werden. Die ID sollte das folgende Format aufweisen: `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`.


## <a name="run-the-code"></a>Ausführen des Codes

Die App kann jetzt mithilfe der Schaltfläche „Run app“ (App ausführen) auf der Symbolleiste gestartet werden (UMSCHALT + F10).

Um an der Teams-Besprechung und am Chat teilzunehmen, geben Sie den Besprechungslink aus Teams und die Thread-ID ein.

Nachdem Sie der Teams-Besprechung beigetreten sind, müssen Sie den Benutzer in Ihrem Teams-Client zur Besprechung zulassen. Sobald der Benutzer zugelassen wurde und dem Chat beigetreten ist, können Sie Nachrichten senden und empfangen.

:::image type="content" source="../join-teams-meeting-chat-quickstart-android.png" alt-text="Screenshot der fertigen Android-Anwendung.":::

> [!NOTE] 
> Derzeit werden für Interoperabilitätsszenarios mit Teams nur das Senden, Empfangen und Bearbeiten von Nachrichten unterstützt. Andere Features wie das Eingeben von Indikatoren oder das Hinzufügen oder Entfernen anderer Benutzer aus der Teams-Besprechung durch Communication Services-Benutzer werden noch nicht unterstützt.
