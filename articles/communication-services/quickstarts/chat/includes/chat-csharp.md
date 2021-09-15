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
ms.openlocfilehash: e1962ebc42c688adfecd18f7d46ce4957147ac7e
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967914"
---
## <a name="sample-code"></a>Beispielcode
Den fertigen Code für diese Schnellstartanleitung finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/add-chat).

## <a name="prerequisites"></a>Voraussetzungen
Führen Sie die folgenden Schritte aus, bevor Sie beginnen:
- Erstellen Sie ein Azure-Konto mit einem aktiven Abonnement. Details finden Sie auf der [Seite zum Erstellen eines kostenloses Azure-Kontos](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installieren von [Visual Studio](https://visualstudio.microsoft.com/downloads/)
- Erstellen Sie eine Azure Communication Services-Ressource. Ausführlichere Informationen hierzu finden Sie unter [Schnellstart: Erstellen und Verwalten einer Communication Services-Ressource](../../create-communication-resource.md). Für diese Schnellstartanleitung müssen Sie den **Endpunkt** Ihrer Ressource aufzeichnen.
- Ein [Benutzerzugriffstoken](../../access-tokens.md). Achten Sie darauf, dass Sie den Bereich auf „Chat“ festlegen, und notieren Sie sich die Tokenzeichenfolge und die userId-Zeichenfolge.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-c-application"></a>Erstellen einer neuen C#-Anwendung

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `ChatQuickstart`. Dieser Befehl erstellt ein einfaches „Hallo Welt“-C#-Projekt mit einer einzigen Quelldatei: **Program.cs**.

```console
dotnet new console -o ChatQuickstart
```

Wechseln Sie zum neu erstellten App-Ordner, und verwenden Sie den Befehl `dotnet build`, um Ihre Anwendung zu kompilieren.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installieren des Pakets

Installieren des .NET SDK für Chats von Azure Communication Services

```PowerShell
dotnet add package Azure.Communication.Chat
```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen werden für einige der wichtigsten Features des C#-SDK für Chats von Azure Communication Services verwendet.

| name                                  | Beschreibung                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Diese Klasse wird für die Chatfunktionalität benötigt. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Erstellen, Abrufen und Löschen von Threads. |
| ChatThreadClient | Diese Klasse wird für die Chatthreadfunktionalität benötigt. Sie rufen eine Instanz über den Chatclient ab und verwenden sie zum Senden/Empfangen/Aktualisieren/Löschen von Nachrichten, zum Hinzufügen/Entfernen/Abrufen von Teilnehmern und zum Senden von Eingabebenachrichtigungen und Lesebestätigungen. |

## <a name="create-a-chat-client"></a>Erstellen eines Chatclients

Zum Erstellen eines Chatclients verwenden Sie Ihren Communication Services-Endpunkt und das Zugriffstoken, das im Rahmen der erforderlichen Schritte zur Vorbereitung generiert wurde. Sie müssen die `CommunicationIdentityClient`-Klasse über das Identitäts-SDK verwenden, um einen Benutzer zu erstellen und ein Token auszustellen, das Sie an Ihren Chatclient übergeben können.

Informieren Sie sich über [Benutzerzugriffstoken](../../access-tokens.md).

Die Erstellung einer Dienstebene zum Verwalten von Token für Ihre Chatanwendung wird zwar empfohlen, aber in dieser Schnellstartanleitung nicht behandelt. Weitere Informationen zur Chatarchitektur finden Sie [hier](../../../concepts/chat/concepts.md).

Kopieren Sie die folgenden Codeausschnitte, und fügen Sie sie in die Quelldatei **Program.cs** ein:
```csharp
using Azure;
using Azure.Communication;
using Azure.Communication.Chat;
using System;

namespace ChatQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            // Your unique Azure Communication service endpoint
            Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

            CommunicationTokenCredential communicationTokenCredential = new CommunicationTokenCredential(<Access_Token>);
            ChatClient chatClient = new ChatClient(endpoint, communicationTokenCredential);
        }
    }
}
```

## <a name="start-a-chat-thread"></a>Starten eines Chatthreads

Verwenden Sie die Methode `createChatThread` für den Chatclient, um einen Chatthread zu erstellen.
- Verwenden Sie `topic`, um für diesen Chat ein Thema anzugeben. Nach der Erstellung des Chatthreads kann das Thema mit der Funktion `UpdateTopic` aktualisiert werden.
- Verwenden Sie die `participants`-Eigenschaft zum Übergeben einer Liste mit `ChatParticipant`-Objekten, die dem Chatthread hinzugefügt werden sollen. Das `ChatParticipant`-Objekt wird mit einem `CommunicationIdentifier`-Objekt initialisiert. Mögliche Typen für `CommunicationIdentifier` sind `CommunicationUserIdentifier`, `MicrosoftTeamsUserIdentifier` und `PhoneNumberIdentifier`. Wenn Sie beispielsweise ein `CommunicationIdentifier`-Objekt abrufen möchten, müssen Sie eine Zugriffs-ID übergeben, die Sie gemäß der Anleitung zum [Erstellen eines Benutzers](../../access-tokens.md#create-an-identity) erstellt haben.

Das Antwortobjekt der Methode `createChatThread` enthält die `chatThread`-Details. Um mit Chatthreadvorgängen wie dem Hinzufügen von Teilnehmern, dem Senden einer Nachricht oder dem Löschen einer Nachricht interagieren zu können, muss eine `chatThreadClient`-Clientinstanz mithilfe der Methode `GetChatThreadClient` im `ChatClient`-Client instanziiert werden.

```csharp
var chatParticipant = new ChatParticipant(identifier: new CommunicationUserIdentifier(id: "<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
CreateChatThreadResult createChatThreadResult = await chatClient.CreateChatThreadAsync(topic: "Hello world!", participants: new[] { chatParticipant });
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: createChatThreadResult.ChatThread.Id);
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Abrufen eines Clients für den Chatthread
Die `GetChatThreadClient`-Methode gibt einen Threadclient für einen bereits vorhandenen Thread zurück. Sie kann zum Durchführen von Vorgängen im erstellten Thread verwendet werden: Hinzufügen von Mitgliedern, Senden einer Nachricht usw. „threadId“ ist die eindeutige ID des vorhandenen Chatthreads.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: threadId);
```

## <a name="list-all-chat-threads"></a>Auflisten aller Chatthreads
Verwenden Sie `GetChatThreads`, um alle Chatthreads abzurufen, zu denen der Benutzer gehört.

```csharp
AsyncPageable<ChatThreadItem> chatThreadItems = chatClient.GetChatThreadsAsync();
await foreach (ChatThreadItem chatThreadItem in chatThreadItems)
{
    Console.WriteLine($"{ chatThreadItem.Id}");
}
```

## <a name="send-a-message-to-a-chat-thread"></a>Senden einer Nachricht an einen Chatthread

Verwenden Sie `SendMessage`, um eine Nachricht an einen Thread zu senden.

- Verwenden Sie `content`, um den Inhalt für die Chatnachricht anzugeben. Dies ist erforderlich.
- Verwenden Sie `type`, um den Inhaltstyp der Nachricht anzugeben (beispielsweise „Text“ oder „Html“). Ohne Angabe wird „Text“ festgelegt.
- Verwenden Sie `senderDisplayName`, um den Anzeigenamen des Absenders anzugeben. Ohne Angabe wird eine leere Zeichenfolge festgelegt.
- Verwenden Sie optional `metadata`, um alle zusätzlichen Daten einzubeziehen, die Sie zusammen mit der Nachricht senden möchten. Dieses Feld bietet Entwicklern einen Mechanismus, um die Chatnachrichtenfunktionalität zu erweitern und benutzerdefinierte Informationen für Ihren Anwendungsfall hinzuzufügen. Wenn Sie beispielsweise einen Dateilink in der Nachricht freigeben, sollten Sie „hasAttachment:true“ in den Metadaten hinzufügen, damit die Anwendung des Empfängers diese analysieren und entsprechend anzeigen kann.

```csharp
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
{
    Content = "Please take a look at the attachment",
    MessageType = ChatMessageType.Text
};
sendChatMessageOptions.Metadata["hasAttachment"] = "true";
sendChatMessageOptions.Metadata["attachmentUrl"] = "https://contoso.com/files/attachment.docx";

SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(sendChatMessageOptions);

string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Empfangen von Chatnachrichten aus einem Chatthread

Sie können Chatnachrichten abrufen, indem Sie die `GetMessages`-Methode auf dem Chatthreadclient jeweils nach dem angegebenen Intervallzeitraum abfragen.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Content.Message}");
}
```

Für `GetMessages` wird der optionale Parameter `DateTimeOffset` verwendet. Bei Angabe dieses Offsetwerts erhalten Sie Nachrichten, die nach diesem Datum empfangen, aktualisiert oder gelöscht wurden. Beachten Sie hierbei Folgendes: Es werden auch Nachrichten zurückgegeben, die vor dem Offsetdatum empfangen, aber erst danach bearbeitet oder entfernt wurden.

`GetMessages` gibt die aktuelle Version der Nachricht zurück, einschließlich aller Bearbeitungen oder Löschungen, die für die Nachricht mit `UpdateMessage` und `DeleteMessage` durchgeführt wurden. Für gelöschte Nachrichten wird von `chatMessage.DeletedOn` ein datetime-Wert zurückgegeben, mit dem der Löschzeitpunkt der Nachricht angegeben wird. Für bearbeitete Nachrichten gibt `chatMessage.EditedOn` einen datetime-Wert zurück, mit dem der Bearbeitungszeitpunkt der Nachricht angegeben wird. Auf den ursprünglichen Zeitpunkt der Nachrichtenerstellung kann mit `chatMessage.CreatedOn` zugegriffen werden. Dieses Element kann zum Sortieren der Nachrichten genutzt werden.

Mit `GetMessages` werden unterschiedliche Nachrichtentypen zurückgegeben, die mit `chatMessage.Type` identifiziert werden können. Diese Typen lauten:

- `Text`: Reguläre Chatnachricht, die von einem Threadmitglied gesendet wurde.

- `Html`: Eine formatierte Textnachricht. Beachten Sie, dass Communication Services-Benutzer derzeit keine RichText-Nachrichten senden können. Dieser Nachrichtentyp wird von Nachrichten unterstützt, die in Teams-Interoperabilitätsszenarien von Teams-Benutzern an Communication Services-Benutzer gesendet werden.

- `TopicUpdated`: Systemnachricht, die angibt, dass das Thema aktualisiert wurde (schreibgeschützt)

- `ParticipantAdded`: Systemnachricht mit dem Hinweis, dass dem Chatthread mindestens ein Teilnehmer hinzugefügt wurde (schreibgeschützt)

- `ParticipantRemoved`: Systemnachricht mit dem Hinweis, dass ein Teilnehmer aus dem Chatthread entfernt wurde

Weitere Details finden Sie unter [Nachrichtentypen](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Hinzufügen eines Benutzers als Teilnehmer des Chatthreads

Nach der Erstellung eines Threads können Sie dafür Benutzer hinzufügen und entfernen. Durch Hinzufügen von Benutzern gewähren Sie ihnen Zugriff zum Senden von Nachrichten an den Thread und zum Hinzufügen/Entfernen anderer Teilnehmer. Stellen Sie vor dem Aufrufen von `AddParticipants` sicher, dass Sie ein neues Zugriffstoken und eine Identität für den Benutzer beschafft haben. Der Benutzer benötigt dieses Zugriffstoken, um den Chatclient initialisieren zu können.

Verwenden Sie `AddParticipants`, um dem Chatthread einen oder mehrere Teilnehmer hinzuzufügen. Für die einzelnen Threadteilnehmer werden jeweils folgende Attribute unterstützt:
- `communicationUser` (erforderlich) ist die Identität des Threadteilnehmers.
- `displayName` (optional) ist der Anzeigename für den Threadteilnehmer.
- `shareHistoryTime` (optional) ist der Zeitpunkt, ab dem der Chatverlauf für den Teilnehmer freigegeben wird.

```csharp
var josh = new CommunicationUserIdentifier(id: "<Access_ID_For_Josh>");
var gloria = new CommunicationUserIdentifier(id: "<Access_ID_For_Gloria>");
var amy = new CommunicationUserIdentifier(id: "<Access_ID_For_Amy>");

var participants = new[]
{
    new ChatParticipant(josh) { DisplayName = "Josh" },
    new ChatParticipant(gloria) { DisplayName = "Gloria" },
    new ChatParticipant(amy) { DisplayName = "Amy" }
};

await chatThreadClient.AddParticipantsAsync(participants: participants);
```

## <a name="get-thread-participants"></a>Abrufen der Threadteilnehmer

Verwenden Sie `GetParticipants`, um die Teilnehmer des Chatthreads abzurufen.

```csharp
AsyncPageable<ChatParticipant> allParticipants = chatThreadClient.GetParticipantsAsync();
await foreach (ChatParticipant participant in allParticipants)
{
    Console.WriteLine($"{((CommunicationUserIdentifier)participant.User).Id}:{participant.DisplayName}:{participant.ShareHistoryTime}");
}
```

## <a name="send-read-receipt"></a>Senden einer Lesebestätigung

Verwenden Sie `SendReadReceipt`, um andere Teilnehmer darüber zu informieren, dass die Nachricht vom Benutzer gelesen wurde.

```csharp
await chatThreadClient.SendReadReceiptAsync(messageId: messageId);
```

## <a name="run-the-code"></a>Ausführen des Codes

Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```console
dotnet run
```
