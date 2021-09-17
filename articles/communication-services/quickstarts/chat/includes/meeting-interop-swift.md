---
title: 'Schnellstart: Beitreten zu einer Teams-Besprechung'
author: askaur
ms.author: askaur
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 06d7b776b49411fece14356f24a91f1a5df2b364
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722561"
---
In diesem Schnellstart erfahren Sie, wie Sie mit dem Chat SDK für iOS für Azure Communication Services an einer Teams-Besprechung teilnehmen.

## <a name="prerequisites"></a>Voraussetzungen 

- Eine  [Teams-Bereitstellung](/deployoffice/teams-install). 
- Eine funktionierende [Anruf-App](../../voice-video-calling/get-started-teams-interop.md). 

## <a name="joining-the-meeting-chat"></a>Beitritt zum Besprechungschat 

Ein Communication Services-Benutzer kann mithilfe des Calling SDK einer Teams-Besprechung als anonymer Benutzer beitreten. Durch das Beitreten zur Besprechung wird er dem Besprechungschat als Teilnehmer hinzugefügt und kann Nachrichten mit anderen Benutzern der Besprechung austauschen. Der Benutzer hat keinen Zugriff auf Chatnachrichten, die vor seiner Teilnahme an der Besprechung gesendet wurden, und er kann nach dem Ende der Besprechung keine Nachrichten senden oder empfangen. Führen Sie die folgenden Schritte aus, um an der Besprechung teilzunehmen und mit dem Chatten zu beginnen.

## <a name="add-chat-to-the-teams-calling-app"></a>Hinzufügen von Chat zur Anruf-App von Teams

Löschen Sie `Podfile.lock`, und ersetzen Sie den Inhalt der Podfile-Datei durch Folgendes:

```
platform :ios, '13.0'
use_frameworks!

target 'AzureCommunicationCallingSample' do
  pod 'AzureCommunicationCalling', '~> 1.0.0-beta.12'
  pod 'AzureCommunicationChat', '~> 1.0.0-beta.11'
end
```

## <a name="install-the-chat-packages"></a>Installieren der Chatpakete

Führen Sie `pod install` aus, um das AzureCommunicationChat-Paket zu installieren.
Öffnen Sie nach der Installation die Datei `.xcworkspace`.

## <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Importieren Sie das AzureCommunicationChat-Paket in `ContentView.swift`, indem Sie den folgenden Codeausschnitt hinzufügen:

```
import AzureCommunicationChat
```

## <a name="add-the-teams-ui-controls"></a>Hinzufügen der Steuerelemente der Teams-Benutzeroberfläche

Fügen Sie in `ContentView.swift` den folgenden Ausschnitt unter den vorhandenen Zustandsvariablen ein. 

```
    // Chat
    @State var chatClient: ChatClient?
    @State var chatThreadClient: ChatThreadClient?
    @State var chatMessage: String = ""
    @State var meetingMessages: [MeetingMessage] = []

    let displayName: String = "<YOUR_DISPLAY_NAME_HERE>"
```

Ersetzen `<YOUR_DISPLAY_NAME_HERE>` Sie durch den im Chat gewünschten Anzeigenamen.

Als Nächstes ändern wir das Formular `Section` so, dass es unsere Chatnachrichten anzeigt, und fügen der Benutzeroberfläche Steuerelemente für den Chat hinzu.

Fügen Sie dem vorhandenen Formular den folgenden Codeausschnitt hinzu. Direkt nach der Textansicht `Text(recordingStatus)` für den Aufzeichnungsstatus.

```
VStack(alignment: .leading) {
    ForEach(meetingMessages, id: \.id) { message in
        let currentUser: Bool = (message.displayName == self.displayName)
        let foregroundColor = currentUser ? Color.white : Color.black
        let background = currentUser ? Color.blue : Color(.systemGray6)
        let alignment = currentUser ? HorizontalAlignment.trailing : HorizontalAlignment.leading
        VStack {
            Text(message.displayName).font(Font.system(size: 10))
            Text(message.content)
        }
        .alignmentGuide(.leading) { d in d[alignment] }
        .padding(10)
        .foregroundColor(foregroundColor)
        .background(background)
        .cornerRadius(10)
    }
}.frame(minWidth: 0, maxWidth: .infinity)
TextField("Enter your message...", text: $chatMessage)
Button(action: sendMessage) {
    Text("Send Message")
}.disabled(chatThreadClient == nil)
```

Ändern Sie als Nächstes den Titel in `Chat Teams Quickstart`. Ändern Sie die folgende Zeile in diesen Titel.

```
.navigationBarTitle("Chat Teams Quickstart")
```

## <a name="enable-the-teams-ui-controls"></a>Aktivieren der Steuerelemente der Teams-Benutzeroberfläche

### <a name="initialize-the-chatclient"></a>Initialisieren von ChatClient

Instanziieren Sie `ChatClient`, und aktivieren Sie Echtzeitbenachrichtigungen. Wir verwenden Echtzeitbenachrichtigungen für den Empfang von Chatnachrichten.

Fügen Sie innerhalb von `NavigationView` `.onAppear` den folgenden Codeausschnitt nach dem vorhandenen Code ein, der `CallAgent` initialisiert.

```
// Initialize the ChatClient
do {
    let endpoint = "COMMUNICATION_SERVICES_RESOURCE_ENDPOINT_HERE>"
    let credential = try CommunicationTokenCredential(token: "<USER_ACCESS_TOKEN_HERE>")

    self.chatClient = try ChatClient(
        endpoint: endpoint,
        credential: credential,
        withOptions: AzureCommunicationChatClientOptions()
    )

    self.message = "ChatClient successfully created"

    // Start real-time notifications
    self.chatClient?.startRealTimeNotifications() { result in
        switch result {
        case .success:
            print("Real-time notifications started")
            // Receive chat messages
            self.chatClient?.register(event: ChatEventId.chatMessageReceived, handler: receiveMessage)
        case .failure:
            print("Failed to start real-time notifications")
            self.message = "Failed to enable chat notifications"
        }
    }
} catch {
    print("Unable to create ChatClient")
    self.message = "Please enter a valid endpoint and Chat token in source code"
    return
}
```

Ersetzen Sie `<COMMUNICATION_SERVICES_RESOURCE_ENDPOINT_HERE>` durch den Endpunkt Ihrer Communication Services-Ressource.
Ersetzen `<USER_ACCESS_TOKEN_HERE>` Sie durch ein Zugriffstoken, das den Bereich „Chat“ hat. 

Weitere Informationen zu Benutzerzugriffstoken: [Benutzerzugriffstoken](../../access-tokens.md)

### <a name="initialize-the-chatthreadclient"></a>Initialisieren von ChatThreadClient

Innerhalb der vorhandenen Funktion `joinTeamsMeeting()` wird die Funktion `ChatThreadClient` initialisiert, nachdem der Benutzer der Besprechung beigetreten ist.

Fügen Sie innerhalb des Fertigstellungshandlers für den Aufruf von `self.callAgent?.join()` den Code unterhalb des Kommentars `// Initialize the ChatThreadClient` ein. Der vollständige Code ist unten dargestellt.

```
self.callAgent?.join(with: teamsMeetingLinkLocator, joinCallOptions: joinCallOptions) { (call, error) in
    if (error == nil) {
        self.call = call
        self.callObserver = CallObserver(self)
        self.call!.delegate = self.callObserver
        self.message = "Teams meeting joined successfully"

        // Initialize the ChatThreadClient
        do {
            guard let threadId = getThreadId(from: self.meetingLink) else {
                self.message = "Failed to join meeting chat"
                return
            }
            self.chatThreadClient = try chatClient?.createClient(forThread: threadId)
            self.message = "Joined meeting chat successfully"
        } catch {
            print("Failed to create ChatThreadClient")
            self.message = "Failed to join meeting chat"
            return
        }
    } else {
        print("Failed to join Teams meeting")
    }
}
```

Fügen Sie `ContentView` die folgende Hilfsfunktion hinzu, die zum Abrufen der Thread-ID des Chats aus dem Besprechungslink von Teams dient.

```
func getThreadId(from meetingLink: String) -> String? {
    if let range = self.meetingLink.range(of: "meetup-join/") {
        let thread = self.meetingLink[range.upperBound...]
        if let endRange = thread.range(of: "/")?.lowerBound {
            return String(thread.prefix(upTo: endRange))
        }
    }
    return nil
}
```

### <a name="enable-sending-messages"></a>Aktivieren des Sendens von Nachrichten

Fügen Sie die `sendMessage()`-Funktion `ContentView` hinzu. Diese Funktion verwendet `ChatThreadClient`, um Nachrichten des Benutzers zu senden.

```
func sendMessage() {
    let message = SendChatMessageRequest(
        content: self.chatMessage,
        senderDisplayName: self.displayName
    )
    
    self.chatThreadClient?.send(message: message) { result, _ in
        switch result {
        case .success:
            print("Chat message sent")
        case .failure:
            print("Failed to send chat message")
        }

        self.chatMessage = ""
    }
}
```

### <a name="enable-receiving-messages"></a>Aktivieren des Empfangens von Nachrichten

Um Nachrichten zu empfangen, implementieren wir den Handler für `ChatMessageReceived`-Ereignisse. Wenn neue Nachrichten an den Thread gesendet werden, fügt dieser Handler die Nachrichten der Variablen `meetingMessages` hinzu, damit sie auf der Benutzeroberfläche angezeigt werden können.

Fügen Sie zunächst `ContentView.swift` die folgende Struktur hinzu. Die Benutzeroberfläche verwendet die Daten in der Struktur, um unsere Chatnachrichten anzuzeigen.

```
struct MeetingMessage {
    let id: String
    let content: String
    let displayName: String
}
```

Fügen Sie als Nächstes die `receiveMessage()`-Funktion `ContentView` hinzu. Dies ist der Handler, der bei jedem Auftreten eines `ChatMessageReceived`-Ereignisses aufgerufen wird.

```
func receiveMessage(response: Any, eventId: ChatEventId) {
    let chatEvent: ChatMessageReceivedEvent = response as! ChatMessageReceivedEvent

    let displayName: String = chatEvent.senderDisplayName ?? "Unknown User"
    let content: String = chatEvent.message.replacingOccurrences(of: "<[^>]+>", with: "", options: String.CompareOptions.regularExpression)

    self.meetingMessages.append(
        MeetingMessage(
            id: chatEvent.id,
            content: content,
            displayName: displayName
        )
    )
}
```

### <a name="leave-the-chat"></a>Verlassen des Chats

Wenn der Benutzer die Teams-Besprechung verlässt, wird der Chat auf der Benutzeroberfläche geleert. Der vollständige Code ist unten dargestellt.

```
func leaveMeeting() {
    if let call = call {
        call.hangUp(options: nil, completionHandler: { (error) in
            if error == nil {
                self.message = "Leaving Teams meeting was successful"
                // Clear the chat
                self.meetingMessages.removeAll()
            } else {
                self.message = "Leaving Teams meeting failed"
            }
        })
    } else {
        self.message = "No active call to hanup"
    }
}
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Abrufen eines Teams-Besprechungschatthreads für einen Communication Services-Benutzer

Der Link und der Chat für die Teams-Besprechung können über Graph-APIs abgerufen werden, wie in der [Graph-Dokumentation](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) ausführlich erläutert. Das Communication Services-SDK für Telefonie akzeptiert einen vollständigen Teams-Besprechungslink. Dieser Link wird als Teil der `onlineMeeting`-Ressource zurückgegeben, auf die unter der [Eigenschaft `joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) zugegriffen werden kann. Mit den [Graph-APIs](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) können Sie auch die `threadId` abrufen. Die Antwort enthält ein `chatInfo`-Objekt, das die `threadID` enthält. 

Sie können die erforderlichen Besprechungsinformationen und die Thread-ID auch über die URL unter **An Besprechung teilnehmen** in der Einladung zur Teams-Besprechung abrufen.
Ein Teams-Besprechungslink sieht wie folgt aus: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. Die `threadId` befindet sich dort, wo im Link `meeting_chat_thread_id` steht. Stellen Sie sicher, dass vor der Verwendung Escapezeichen aus der `meeting_chat_thread_id` entfernt werden. Die ID sollte das folgende Format aufweisen: `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`.


## <a name="run-the-code"></a>Ausführen des Codes

Führen Sie die Anwendung aus. 

Um an der Teams-Besprechung teilzunehmen, geben Sie auf der Benutzeroberfläche den Besprechungslink Ihres Teams ein.

Nachdem Sie der Teams-Besprechung beigetreten sind, müssen Sie den Benutzer in Ihrem Teams-Client zur Besprechung zulassen. Sobald der Benutzer zugelassen wurde und dem Chat beigetreten ist, können Sie Nachrichten senden und empfangen.

:::image type="content" source="../join-teams-meeting-chat-quickstart-ios.png" alt-text="Screenshot der fertigen iOS-Anwendung":::

> [!NOTE] 
> Derzeit werden für Interoperabilitätsszenarios mit Teams nur das Senden, Empfangen und Bearbeiten von Nachrichten unterstützt. Andere Features wie das Eingeben von Indikatoren oder das Hinzufügen oder Entfernen anderer Benutzer aus der Teams-Besprechung durch Communication Services-Benutzer werden noch nicht unterstützt.
