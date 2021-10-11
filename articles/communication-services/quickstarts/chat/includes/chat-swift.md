---
title: Datei einfügen
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
ms.openlocfilehash: d61120d3f4e1256b9da50b8128b23fd73b36a5ef
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2021
ms.locfileid: "129406803"
---
## <a name="sample-code"></a>Beispielcode
Den fertigen Code für diese Schnellstartanleitung finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/add-chat).

## <a name="prerequisites"></a>Voraussetzungen
Führen Sie die folgenden Schritte aus, bevor Sie beginnen:

- Erstellen Sie ein Azure-Konto mit einem aktiven Abonnement. Details finden Sie auf der [Seite zum Erstellen eines kostenloses Azure-Kontos](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installieren Sie [Xcode](https://developer.apple.com/xcode/) und [CocoaPods](https://cocoapods.org/). Sie verwenden Xcode zum Erstellen einer iOS-Anwendung für die Schnellstartanleitung und CocoaPods zum Installieren der Abhängigkeiten.
- Erstellen Sie eine Azure Communication Services-Ressource. Ausführlichere Informationen finden Sie unter [Schnellstart: Erstellen und Verwalten einer Communication Services-Ressource](../../create-communication-resource.md). Für diese Schnellstartanleitung müssen Sie Ihren Ressourcenendpunkt aufzeichnen.
- Erstellen Sie zwei Benutzer in Azure Communication Services, und stellen Sie ein [Benutzerzugriffstoken](../../access-tokens.md) für diese Benutzer aus. Achten Sie darauf, dass Sie den Bereich auf `chat` festlegen und sich die Zeichenfolgen für `token` und `userId` notieren. In dieser Schnellstartanleitung erstellen Sie einen Thread mit einem ersten Teilnehmer und fügen anschließend einen zweiten Teilnehmer hinzu.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-ios-application"></a>Erstellen einer neuen iOS-Anwendung

Starten Sie Xcode, und wählen Sie **Create a new Xcode project** (Neues Xcode-Projekt erstellen) aus. Wählen Sie anschließend **iOS** als Plattform und **App** für die Vorlage aus.

Geben Sie **ChatQuickstart** als Projektnamen ein. Wählen Sie anschließend **Storyboard** als Schnittstelle, **UIKit App Delegate** als Lebenszyklus und **Swift** als Sprache aus.

Wählen Sie die Option **Weiter** und dann das Verzeichnis aus, in dem Sie das Projekt erstellen möchten.

### <a name="install-the-libraries"></a>Installieren der Bibliotheken

Verwenden Sie CocoaPods, um die erforderlichen Communication Services-Abhängigkeiten zu installieren.

Navigieren Sie in der Befehlszeile zum Stammverzeichnis des iOS-Projekts `ChatQuickstart`. Erstellen Sie mit dem folgenden Befehl eine Podfile-Datei: `pod init`.

Öffnen Sie die Podfile-Datei, und fügen Sie dem Ziel `ChatQuickstart` die folgenden Abhängigkeiten hinzu:

```
pod 'AzureCommunicationCommon', '~> 1.0.2'
pod 'AzureCommunicationChat', '~> 1.1.0-beta.2'
```

Installieren Sie die Abhängigkeiten mit dem folgenden Befehl: `pod install`. Beachten Sie, dass hierbei auch ein Xcode-Arbeitsbereich erstellt wird.

Öffnen Sie das Projekt nach dem Ausführen von `pod install` erneut in Xcode, indem Sie den neu erstellten Arbeitsbereich `.xcworkspace` auswählen.

### <a name="set-up-the-placeholders"></a>Einrichten der Platzhalter

Öffnen Sie den Arbeitsbereich `ChatQuickstart.xcworkspace` in Xcode und anschließend `ViewController.swift`.

In dieser Schnellstartanleitung fügen Sie Ihren Code dem `viewController`-Element hinzu und sehen sich die Ausgabe in der Xcode-Konsole an. Das Erstellen einer Benutzeroberfläche unter iOS wird in dieser Schnellstartanleitung nicht beschrieben. 

Importieren Sie im oberen Bereich von `viewController.swift` die Bibliotheken `AzureCommunication` und `AzureCommunicatonChat`:

```
import AzureCommunicationCommon
import AzureCommunicationChat
```

Kopieren Sie den folgenden Code in die Methode `viewDidLoad()` von `ViewController`:

```
override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let semaphore = DispatchSemaphore(value: 0)
        DispatchQueue.global(qos: .background).async {
            do {
                // <CREATE A CHAT CLIENT>
                
                // <CREATE A CHAT THREAD>

                // <LIST ALL CHAT THREADS>

                // <GET A CHAT THREAD CLIENT>

                // <SEND A MESSAGE>

                // <SEND A READ RECEIPT >

                // <RECEIVE MESSAGES>

                // <ADD A USER>
                
                // <LIST USERS>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

Zu Demonstrationszwecken wird Ihr Code mithilfe eines Semaphors synchronisiert. In den folgenden Schritten ersetzen Sie die Platzhalter durch Beispielcode, indem Sie die Chatbibliothek von Azure Communication Services verwenden.


### <a name="create-a-chat-client"></a>Erstellen eines Chatclients

Zum Erstellen eines Chatclients verwenden Sie Ihren Communication Services-Endpunkt und das Zugriffstoken, das im Rahmen der erforderlichen Schritte zur Vorbereitung generiert wurde.

Informieren Sie sich über [Benutzerzugriffstoken](../../access-tokens.md).

Die Erstellung einer Dienstebene zum Verwalten von Token für Ihre Chatanwendung wird zwar empfohlen, aber in dieser Schnellstartanleitung nicht behandelt. Weitere Informationen zur Chatarchitektur finden Sie [hier](../../../concepts/chat/concepts.md).

Ersetzen Sie den Kommentar `<CREATE A CHAT CLIENT>` durch den folgenden Codeausschnitt:

```
let endpoint = "<ACS_RESOURCE_ENDPOINT>"
let credential =
try CommunicationTokenCredential(
    token: "<ACCESS_TOKEN>"
)
let options = AzureCommunicationChatClientOptions()

let chatClient = try ChatClient(
    endpoint: endpoint,
    credential: credential,
    withOptions: options
)
```

Ersetzen Sie `<ACS_RESOURCE_ENDPOINT>` durch den Endpunkt Ihrer Azure Communication Services-Ressource. Ersetzen Sie `<ACCESS_TOKEN>` durch ein gültiges Communication Services-Zugriffstoken.

## <a name="object-model"></a>Objektmodell 

Die folgenden Klassen und Schnittstellen werden für einige der wichtigsten Features des Chat SDK von Azure Communication Services für iOS verwendet.

| Name                                   | BESCHREIBUNG                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ChatClient` | Diese Klasse wird für die Chatfunktionalität benötigt. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Erstellen, Abrufen und Löschen von Threads sowie zum Abonnieren von Chatereignissen. |
| `ChatThreadClient` | Diese Klasse wird für die Chatthreadfunktionalität benötigt. Sie erhalten über `ChatClient` eine Instanz und verwenden sie zum Senden, Empfangen, Aktualisieren und Löschen von Nachrichten. Darüber hinaus können Sie sie auch verwenden, um Benutzer hinzuzufügen, zu entfernen und abzurufen sowie Eingabebenachrichtigungen und Lesebestätigungen zu senden. |

## <a name="start-a-chat-thread"></a>Starten eines Chatthreads

`CreateChatThreadResult` ist die Antwort, die beim Erstellen eines Chatthreads zurückgegeben wird.
Sie enthält die `chatThread`-Eigenschaft, bei der es sich um das `ChatThreadProperties`-Objekt handelt. Dieses Objekt enthält die threadId, mit der ein `ChatThreadClient` zum Ausführen von Vorgängen für den erstellten Thread (Teilnehmende hinzufügen, Nachricht senden usw.) abgerufen werden kann.

Ersetzen Sie den Kommentar `<CREATE A CHAT THREAD>` durch den folgenden Codeausschnitt:

```
let request = CreateChatThreadRequest(
    topic: "Quickstart",
    participants: [
        ChatParticipant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.chatThread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

Ersetzen Sie `<USER_ID>` durch eine gültige Communication Services-Benutzer-ID.

Sie verwenden hier einen Semaphor, um auf den Fertigstellungshandler zu warten, bevor der Vorgang fortgesetzt wird. Die Thread-ID (`threadId`) aus der Antwort an den Fertigstellungshandler verwenden Sie in späteren Schritten.

## <a name="list-all-chat-threads"></a>Auflisten aller Chatthreads

Nach dem Erstellen eines Chatthreads können wir alle Chatthreads auflisten, indem wir für `ChatClient` die `listChatThreads`-Methode aufrufen. Ersetzen Sie den Kommentar `<LIST ALL CHAT THREADS>` durch folgenden Code:

```
chatClient.listThreads { result, _ in
    switch result {
    case let .success(threads):
        guard let chatThreadItems = threads.pageItems else {
            print("No threads returned.")
            return
        }

        for chatThreadItem in chatThreadItems {
            print("Thread id: \(chatThreadItem.id)")
        }
    case .failure:
        print("Failed to list threads")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="get-a-chat-thread-client"></a>Abrufen eines Clients für den Chatthread

Die `createClient`-Methode gibt einen `ChatThreadClient` für einen bereits vorhandenen Thread zurück. Er kann verwendet werden, um Vorgänge wie das Hinzufügen von Teilnehmern oder das Senden einer Nachricht im erstellten Thread auszuführen. „threadId“ ist die eindeutige ID des vorhandenen Chatthreads.

Ersetzen Sie den Kommentar `<GET A CHAT THREAD CLIENT>` durch folgenden Code:

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>Senden einer Nachricht an einen Chatthread

Verwenden Sie die `send`-Methode, um eine Nachricht an einen Thread zu senden, der durch die threadId identifiziert wird.

`SendChatMessageRequest` wird verwendet, um die Nachrichtenanforderung zu beschreiben:

- Verwenden Sie `content`, um den Inhalt der Chatnachricht anzugeben.
- Verwenden Sie `senderDisplayName`, um den Anzeigenamen des Absenders anzugeben.
- Verwenden Sie `type`, um den Nachrichtentyp anzugeben, etwa Text oder HTML.
- Verwenden Sie optional `metadata`, um alle zusätzlichen Daten einzubeziehen, die Sie zusammen mit der Nachricht senden möchten. Dieses Feld bietet Entwicklern einen Mechanismus, um die Chatnachrichtenfunktionalität zu erweitern und benutzerdefinierte Informationen für Ihren Anwendungsfall hinzuzufügen. Wenn Sie beispielsweise einen Dateilink in der Nachricht freigeben, sollten Sie „hasAttachment:true“ in den Metadaten hinzufügen, damit die Anwendung des Empfängers diese analysieren und entsprechend anzeigen kann.

`SendChatMessageResult` ist die Antwort, die nach dem Senden einer Nachricht zurückgegeben wird. Sie enthält eine ID, bei der es sich um die eindeutige ID der Nachricht handelt.

Ersetzen Sie den Kommentar `<SEND A MESSAGE>` durch den folgenden Codeausschnitt:

```
let message = SendChatMessageRequest(
                        content: "Hello!",
                        senderDisplayName: "Jack",
                        type: .text,
                        metadata: [
                            "hasAttachment": "true",
                            "attachmentUrl": "https://contoso.com/files/attachment.docx"
                        ]
                    )

var messageId: String?

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
        messageId = result.id
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="send-a-read-receipt"></a>Senden einer Lesebestätigung

Die Methode `sendReadReceipt` kann verwendet werden, um im Auftrag eines Benutzers ein Lesebestätigungsereignis in einem Thread zu veröffentlichen.
`messageId` ist die eindeutige ID der Chatnachricht, die gelesen wurde.

Ersetzen Sie den Kommentar `<SEND A READ RECEIPT>` durch den folgenden Code:

```
if let id = messageId {
    chatThreadClient.sendReadReceipt(forMessage: id) { result, _ in
        switch result {
        case .success:
            print("Read receipt sent")
        case .failure:
            print("Failed to send read receipt")
        }
        semaphore.signal()
    }
    semaphore.wait()
} else {
    print("Cannot send read receipt without a message id")
}
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Empfangen von Chatnachrichten aus einem Chatthread

Bei der Echtzeitsignalisierung können Sie eine Funktion abonnieren, mit der auf neue eingehende Nachrichten gelauscht wird und die aktuellen Nachrichten im Arbeitsspeicher entsprechend aktualisiert werden. Azure Communication Services unterstützt eine [Liste mit Ereignissen, die Sie abonnieren können](../../../concepts/chat/concepts.md#real-time-notifications).

Ersetzen Sie den Kommentar `<RECEIVE MESSAGES>` durch den folgenden Code. Versuchen Sie nach dem Aktivieren von Benachrichtigungen, neue Nachrichten zu senden, um ChatMessageReceivedEvents anzuzeigen.

```
chatClient.startRealTimeNotifications { result in
    switch result {
    case .success:
        print("Real-time notifications started.")
    case .failure:
        print("Failed to start real-time notifications.")
    }
    semaphore.signal()
}
semaphore.wait()

chatClient.register(event: .chatMessageReceived, handler: { response in
    switch response {
    case let .chatMessageReceivedEvent(event):
        print("Received a message: \(event.message)")
    default:
        return
    }
})
```

Alternativ können Sie Chatnachrichten auch abrufen, indem Sie die `listMessages`-Methode jeweils nach dem angegebenen Intervallzeitraum abfragen. Sehen Sie sich den folgenden Codeausschnitt für `listMessages` an.

```
chatThreadClient.listMessages { result, _ in
    switch result {
    case let .success(messagesResult):
        guard let messages = messagesResult.pageItems else {
            print("No messages returned.")
            return
        }

        for message in messages {
            print("Received message with id: \(message.id)")
        }

    case .failure:
        print("Failed to receive messages")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Hinzufügen eines Benutzers als Teilnehmer des Chatthreads

Nach der Erstellung eines Threads können Sie dafür Benutzer hinzufügen und entfernen. Durch Hinzufügen von Benutzern gewähren Sie ihnen Zugriff zum Senden von Nachrichten an den Thread und zum Hinzufügen/Entfernen anderer Teilnehmer. Stellen Sie vor dem Aufrufen von `add` sicher, dass Sie ein neues Zugriffstoken und eine Identität für den Benutzer beschafft haben. Der Benutzer benötigt dieses Zugriffstoken, um den Chatclient initialisieren zu können.

Verwenden Sie die `add`-Methode von `ChatThreadClient`, um dem Chatthread einzelne oder mehrere Teilnehmende hinzuzufügen. Für die einzelnen Threadteilnehmer werden jeweils folgende Attribute unterstützt:
- `id` (erforderlich) ist die Identität des Threadteilnehmers.
- `displayName` (optional) ist der Anzeigename für den Threadteilnehmer.
- `shareHistoryTime` (optional) ist der Zeitpunkt, ab dem der Chatverlauf für den Teilnehmer freigegeben wird.

Ersetzen Sie den Kommentar `<ADD A USER>` durch folgenden Code:

```
let user = ChatParticipant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.invalidParticipants != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to add the participant")
    }
    semaphore.signal()
}
semaphore.wait()
```

Ersetzen Sie `<USER_ID>` durch die Communication Services-Benutzer-ID des Benutzers, der hinzugefügt werden soll.

## <a name="list-users-in-a-thread"></a>Auflisten der Benutzer in einem Thread

Verwenden Sie die `listParticipants`-Methode, um alle Teilnehmenden eines bestimmten Chatthreads abzurufen.

Ersetzen Sie den `<LIST USERS>`-Kommentar durch folgenden Code:

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participantsResult):
        guard let participants = participantsResult.pageItems else {
            print("No participants returned.")
            return
        }

        for participant in participants {
            let user = participant.id as! CommunicationUserIdentifier
            print("User with id: \(user.identifier)")
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="run-the-code"></a>Ausführen des Codes

Klicken Sie in Xcode auf die Ausführungsschaltfläche, um das Projekt zu erstellen und auszuführen. In der Konsole können Sie sich die Ausgabe des Codes sowie die Protokollierungsausgabe des Chatclients ansehen.

**Hinweis:** Legen Sie `Build Settings > Build Options > Enable Bitcode` auf `No` fest. Derzeit unterstützt das AzureCommunicationChat SDK für iOS die Aktivierung von Bitcode nicht. Im folgenden [GitHub-Problem](https://github.com/Azure/azure-sdk-for-ios/issues/787) wird dies nachverfolgt.
