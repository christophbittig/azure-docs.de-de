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
ms.openlocfilehash: 63653bb72c278a330101503dbaf2959b7e0eb39f
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "114201114"
---
## <a name="sample-code"></a>Beispielcode
Den fertigen Code für diese Schnellstartanleitung finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-chat).

## <a name="prerequisites"></a>Voraussetzungen
Führen Sie die folgenden Schritte aus, bevor Sie beginnen:

- Erstellen Sie ein Azure-Konto mit einem aktiven Abonnement. Details finden Sie auf der [Seite zum Erstellen eines kostenloses Azure-Kontos](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installieren Sie die Active LTS- und Maintenance LTS-Versionen von [Node.js](https://nodejs.org/en/download/).
- Erstellen Sie eine Azure Communication Services-Ressource. Ausführlichere Informationen hierzu finden Sie unter [Schnellstart: Erstellen und Verwalten einer Communication Services-Ressource](../../create-communication-resource.md). Für diese Schnellstartanleitung müssen Sie den **Endpunkt** Ihrer Ressource aufzeichnen.
- Erstellen Sie *drei* ACS-Benutzer, und stellen Sie ein [Benutzerzugriffstoken](../../access-tokens.md) für sie aus. Legen Sie den Bereich auf **Chat** fest, und **notieren Sie sich die Tokenzeichenfolge und die userId-Zeichenfolge**. In der vollständigen Demo wird ein Thread mit zwei Teilnehmern erstellt und dann ein dritter Teilnehmer hinzugefügt.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-web-application"></a>Erstellen einer neuen Webanwendung

Öffnen Sie zunächst Ihr Terminal- bzw. Befehlsfenster, erstellen Sie ein neues Verzeichnis für Ihre App, und greifen Sie auf das Verzeichnis zu.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Führen Sie `npm init -y` aus, um die Datei **package.json** mit den Standardeinstellungen zu erstellen.

```console
npm init -y
```

### <a name="install-the-packages"></a>Installieren der Pakete

Verwenden Sie den Befehl `npm install`, um die unten angegebenen Communication Services-SDKs für JavaScript zu installieren.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

Durch die Option `--save` wird die Bibliothek als Abhängigkeit in der Datei **package.json** aufgeführt.

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

In dieser Schnellstartanleitung wird Webpack verwendet, um die Anwendungsressourcen zu bündeln. Führen Sie den folgenden Befehl aus, um die npm-Pakete „webpack“, „webpack-cli“ und „webpack-dev-server“ zu installieren und als Entwicklungsabhängigkeiten in Ihrer Datei **package.json** aufzulisten:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Erstellen Sie im Stammverzeichnis eine `webpack.config.js`-Datei. Kopieren Sie die folgende Konfiguration in diese Datei:

```
module.exports = {
  entry: "./client.js",
  output: {
    filename: "bundle.js"
  },
  devtool: "inline-source-map",
  mode: "development"
}
```

Fügen Sie zu Ihrem `package.json` ein `start`-Skript hinzu. Es wird zum Ausführen der App verwendet. Fügen Sie im Abschnitt `scripts` von `package.json` Folgendes hinzu:

```
"scripts": {
  "start": "webpack serve --config ./webpack.config.js"
}
```

Erstellen Sie im Stammverzeichnis Ihres Projekts die Datei **index.html**. Wir verwenden diese Datei als Vorlage zum Hinzufügen der Chatfunktion mit dem JavaScript-SDK für Chats von Azure Communication Services.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Erstellen Sie im Stammverzeichnis Ihres Projekts eine Datei mit dem Namen **client.js**, die die Anwendungslogik für diese Schnellstartanleitung enthalten soll.

### <a name="create-a-chat-client"></a>Erstellen eines Chatclients

Zum Erstellen eines Chatclients in Ihrer Web-App benötigen Sie den Communication Services-**Endpunkt** und das **Zugriffstoken**, das im Rahmen der Vorbereitung generiert wurde.

Mit Benutzerzugriffstoken können Sie Clientanwendungen erstellen, die gegenüber Azure Communication Services direkt authentifiziert werden. Die Erstellung einer Dienstebene zum Verwalten von Token für Ihre Chatanwendung wird in dieser Schnellstartanleitung nicht behandelt. Unter [Chatkonzepte](../../../concepts/chat/concepts.md) finden Sie weitere Informationen zur Chatarchitektur und unter [Benutzerzugriffstoken](../../access-tokens.md) weitere Informationen zu Zugriffstoken.

Verwenden Sie in **client.js** den Endpunkt und das Zugriffstoken im folgenden Code, um Chatfunktionen über das JavaScript-SDK für Chats von Azure Communication Services hinzuzufügen.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
// The user access token generated as part of the pre-requisites
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationTokenCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
- Ersetzen Sie die **endpointUrl** durch den Communication Azure Services-Ressourcenendpunkt. Informationen zum [Erstellen einer Azure Communication Services-Ressource finden Sie bei Bedarf hier](../../create-communication-resource.md).
- Ersetzen Sie **userAccessToken** durch das von Ihnen ausgestellte Token.


### <a name="run-the-code"></a>Ausführen des Codes

Führen Sie den folgenden Befehl aus, um den Anwendungshost auf einem lokalen Webserver zu bündeln:
```console
npm run start
```
Navigieren Sie in Ihrem Browser zu http://localhost:8080/.
In Ihrem Browser in der Konsole mit den Entwicklertools sollte Folgendes angezeigt werden:

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>Objektmodell
Die folgenden Klassen und Schnittstellen werden für einige der wichtigsten Features des JavaScript-SDKs für Chats von Azure Communication Services verwendet.

| name                                   | Beschreibung                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Diese Klasse wird für die Chatfunktionalität benötigt. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Erstellen, Abrufen und Löschen von Threads sowie zum Abonnieren von Chatereignissen. |
| ChatThreadClient | Diese Klasse wird für die Chatthreadfunktionalität benötigt. Sie rufen eine Instanz über den ChatClient ab und verwenden sie zum Senden/Empfangen/Aktualisieren/Löschen von Nachrichten, Hinzufügen/Entfernen/Abrufen von Benutzern und Senden von Eingabebenachrichtigungen und Lesebestätigungen. |


## <a name="start-a-chat-thread"></a>Starten eines Chatthreads

Verwenden Sie die `createThread`-Methode, um einen Chatthread zu erstellen.

`createThreadRequest` wird zum Beschreiben der Threadanforderung verwendet:

- Verwenden Sie `topic`, um für diesen Chat ein Thema anzugeben. Themen können nach der Erstellung des Chatthreads mit der Funktion `UpdateThread` aktualisiert werden.
- Verwenden Sie `participants`, um die Teilnehmer aufzulisten, die dem Chatthread hinzugefügt werden sollen.

Nach der Auflösung wird von der Methode `createChatThread` eine Antwort vom Typ `CreateChatThreadResult` zurückgegeben. Dieses Modell enthält eine Eigenschaft vom Typ `chatThread`. Dort können Sie auf die ID (`id`) des neu erstellten Threads zugreifen. Mithilfe der ID (`id`) können Sie anschließend eine Instanz eines Chatthreadclients (`ChatThreadClient`) abrufen. Mit dem Chatthreadclient (`ChatThreadClient`) können dann innerhalb des Threads Vorgänge wie das Senden von Nachrichten oder das Auflisten von Teilnehmer ausgeführt werden.

```JavaScript
async function createChatThread() {
  const createChatThreadRequest = {
    topic: "Hello, World!"
  };
  const createChatThreadOptions = {
    participants: [
      {
        id: { communicationUserId: '<USER_ID>' },
        displayName: '<USER_DISPLAY_NAME>'
      }
    ]
  };
  const createChatThreadResult = await chatClient.createChatThread(
    createChatThreadRequest,
    createChatThreadOptions
  );
  const threadId = createChatThreadResult.chatThread.id;
  return threadId;
}

createChatThread().then(async threadId => {
  console.log(`Thread created:${threadId}`);
  // PLACEHOLDERS
  // <CREATE CHAT THREAD CLIENT>
  // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
  // <SEND MESSAGE TO A CHAT THREAD>
  // <LIST MESSAGES IN A CHAT THREAD>
  // <ADD NEW PARTICIPANT TO THREAD>
  // <LIST PARTICIPANTS IN A THREAD>
  // <REMOVE PARTICIPANT FROM THREAD>
  });
```

Wenn Sie Ihren Browsertab aktualisieren, sollte in der Konsole Folgendes angezeigt werden:
```console
Thread created: <thread_id>
```

## <a name="get-a-chat-thread-client"></a>Abrufen eines Clients für den Chatthread

Die `getChatThreadClient`-Methode gibt einen `chatThreadClient` für einen bereits vorhandenen Thread zurück. Er kann verwendet werden, um Vorgänge wie das Hinzufügen von Teilnehmern oder das Senden einer Nachricht im erstellten Thread auszuführen. „threadId“ ist die eindeutige ID des vorhandenen Chatthreads.

```JavaScript
let chatThreadClient = chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${threadId}`);

```
Fügen Sie diesen Code anstelle des Kommentars `<CREATE CHAT THREAD CLIENT>` in **client.js** hinzu, aktualisieren Sie Ihre Browserregisterkarte, und sehen Sie in Ihrer Konsole nach. Folgendes sollte angezeigt werden:
```console
Chat Thread client for threadId: <threadId>
```

## <a name="list-all-chat-threads"></a>Auflisten aller Chatthreads

Die Methode `listChatThreads` gibt ein `PagedAsyncIterableIterator` von Typ `ChatThreadItem` zurück. Sie kann zum Auflisten aller Chatthreads verwendet werden.
Beim Auflisten von Threads wird als Antwort ein Iterator vom Typ `[ChatThreadItem]` zurückgegeben.

```JavaScript
const threads = chatClient.listChatThreads();
for await (const thread of threads) {
   // your code here
}
```

## <a name="send-a-message-to-a-chat-thread"></a>Senden einer Nachricht an einen Chatthread

Verwenden Sie die `sendMessage`-Methode, um eine Nachricht an einen Thread zu senden, der anhand von „threadId“ identifiziert wird.

`sendMessageRequest` wird verwendet, um die Nachrichtenanforderung zu beschreiben:

- Verwenden Sie `content`, um den Inhalt der Chatnachricht anzugeben.

`sendMessageOptions` wird verwendet, um die optionalen Vorgangsparameter zu beschreiben:

- Verwenden Sie `senderDisplayName`, um den Anzeigenamen des Absenders anzugeben.
- Verwenden Sie `type`, um den Nachrichtentyp anzugeben, etwa Text oder HTML.

`SendChatMessageResult` ist die Antwort, die nach dem Senden einer Nachricht zurückgegeben wird. Sie enthält eine ID, bei der es sich um die eindeutige ID der Nachricht handelt.

```JavaScript
const sendMessageRequest =
{
  content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
  senderDisplayName : 'Jack',
  type: 'text'
};
const sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
const messageId = sendChatMessageResult.id;
console.log(`Message sent!, message id:${messageId}`);
```

Fügen Sie diesen Code anstelle des Kommentars `<SEND MESSAGE TO A CHAT THREAD>` in **client.js** hinzu, aktualisieren Sie Ihre Browserregisterkarte, und sehen Sie in Ihrer Konsole nach.
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Empfangen von Chatnachrichten aus einem Chatthread

Bei der Echtzeitsignalisierung können Sie eine Funktion abonnieren, mit der auf neue eingehende Nachrichten gelauscht wird und die aktuellen Nachrichten im Arbeitsspeicher entsprechend aktualisiert werden. Azure Communication Services unterstützt eine [Liste mit Ereignissen, die Sie abonnieren können](../../../concepts/chat/concepts.md#real-time-notifications).

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
  console.log("Notification chatMessageReceived!");
  // your code here
});

```
Fügen Sie diesen Code anstelle des Kommentars `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` in **client.js** hinzu.
Wenn Sie die Registerkarte in Ihrem Browser aktualisieren, sollte in der Konsole die Meldung `Notification chatMessageReceived` angezeigt werden.

Alternativ können Sie Chatnachrichten auch abrufen, indem Sie die `listMessages`-Methode jeweils nach dem angegebenen Intervallzeitraum abfragen.

```JavaScript

const messages = chatThreadClient.listMessages();
for await (const message of messages) {
   // your code here
}

```
Fügen Sie diesen Code anstelle des Kommentars `<LIST MESSAGES IN A CHAT THREAD>` in **client.js** hinzu.
Aktualisieren Sie Ihren Tab. Daraufhin sollte in der Konsole die Liste mit den Nachrichten angezeigt werden, die in diesem Chatthread gesendet wurden.

Mit `listMessages` werden unterschiedliche Nachrichtentypen zurückgegeben, die mit `chatMessage.type` identifiziert werden können. 

Weitere Details finden Sie unter [Nachrichtentypen](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Hinzufügen eines Benutzers als Teilnehmer des Chatthreads

Nach der Erstellung eines Chatthreads können Sie dafür Benutzer hinzufügen und entfernen. Indem Sie Benutzer hinzufügen, gewähren Sie ihnen Zugriff zum Senden von Nachrichten an den Chatthread und zum Hinzufügen/Entfernen anderer Teilnehmer.

Vergewissern Sie sich vor dem Aufrufen der Methode `addParticipants`, dass Sie über ein neues Zugriffstoken und über eine Identität für den Benutzer verfügen. Der Benutzer benötigt dieses Zugriffstoken, um den Chatclient initialisieren zu können.

`addParticipantsRequest` dient zum Beschreiben des Anforderungsobjekts, und darin wird `participants` verwendet, um die hinzuzufügenden Teilnehmer für den Chatthread aufzulisten.
- `id` (erforderlich) ist der Kommunikationsbezeichner, der dem Chatthread hinzugefügt werden soll.
- `displayName` (optional) ist der Anzeigename für den Threadteilnehmer.
- `shareHistoryTime` (optional) ist der Zeitpunkt, ab dem der Chatverlauf für den Teilnehmer freigegeben wird. Sie können den Verlauf seit dem Beginn des Chatthreads freigeben, indem Sie diese Eigenschaft auf das Datum der Threaderstellung (oder früher) festlegen. Soll der Verlauf vor dem Hinzufügezeitpunkt des Teilnehmers nicht freigegeben werden, geben Sie das aktuelle Datum an. Geben Sie ein Datum Ihrer Wahl an, um einen Teil des Verlaufs freizugeben.

```JavaScript

const addParticipantsRequest =
{
  participants: [
    {
      id: { communicationUserId: '<NEW_PARTICIPANT_USER_ID>' },
      displayName: 'Jane'
    }
  ]
};

await chatThreadClient.addParticipants(addParticipantsRequest);

```
Ersetzen Sie **NEW_PARTICIPANT_USER_ID** durch eine [neue Benutzer-ID](../../access-tokens.md). Fügen Sie diesen Code anstelle des Kommentars `<ADD NEW PARTICIPANT TO THREAD>` in der Datei **client.js** hinzu.

## <a name="list-users-in-a-chat-thread"></a>Auflisten von Benutzern in einem Chatthread
```JavaScript
const participants = chatThreadClient.listParticipants();
for await (const participant of participants) {
   // your code here
}
```
Fügen Sie diesen Code anstelle des Kommentars `<LIST PARTICIPANTS IN A THREAD>` in **client.js** hinzu, aktualisieren Sie Ihre Browserregisterkarte, und sehen Sie in Ihrer Konsole nach. Es sollten Informationen zu den Benutzern eines Threads angezeigt werden.

## <a name="remove-user-from-a-chat-thread"></a>Entfernen eines Benutzers aus einem Chatthread

Teilnehmer können auch aus einem Chatthread entfernt werden. Die Vorgehensweise ist dabei ähnlich wie beim Hinzufügen. Zum Entfernen benötigen Sie die IDs der von Ihnen hinzugefügten Teilnehmer.

Verwenden Sie die `removeParticipant`-Methode. Hierbei ist `participant` der Kommunikationsbenutzer, der aus dem Thread entfernt werden soll.

```JavaScript

await chatThreadClient.removeParticipant({ communicationUserId: <PARTICIPANT_ID> });
await listParticipants();
```
Ersetzen Sie **PARTICIPANT_ID** durch eine Benutzer-ID aus dem vorherigen Schritt (<NEW_PARTICIPANT_USER_ID>).
Fügen Sie diesen Code anstelle des Kommentars `<REMOVE PARTICIPANT FROM THREAD>` in **client.js** hinzu.
