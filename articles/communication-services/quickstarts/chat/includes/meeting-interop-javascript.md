---
title: 'Schnellstart: Beitreten zu einer Teams-Besprechung'
author: askaur
ms.author: askaur
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 616741c97b1e133dd9027b8622c181a9ca622b72
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378226"
---
In dieser Schnellstartanleitung wird beschrieben, wie Sie während einer Teams-Besprechung chatten, indem Sie das Chat SDK für JavaScript von Azure Communication Services verwenden.

## <a name="sample-code"></a>Beispielcode
Den fertigen Code für diese Schnellstartanleitung finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/join-chat-to-teams-meeting).

## <a name="prerequisites"></a>Voraussetzungen 

* Eine  [Teams-Bereitstellung](/deployoffice/teams-install). 
* Eine funktionierende [Chat-App](../get-started.md). 

## <a name="joining-the-meeting-chat"></a>Beitritt zum Besprechungschat 

Ein Communication Services-Benutzer kann mithilfe des Calling SDK einer Teams-Besprechung als anonymer Benutzer beitreten. Durch das Beitreten zur Besprechung wird er dem Besprechungschat als Teilnehmer hinzugefügt und kann Nachrichten mit anderen Benutzern der Besprechung austauschen. Der Benutzer hat keinen Zugriff auf Chatnachrichten, die vor seiner Teilnahme an der Besprechung gesendet wurden, und er kann nach dem Ende der Besprechung keine Nachrichten senden oder empfangen. Führen Sie die folgenden Schritte aus, um an der Besprechung teilzunehmen und mit dem Chatten zu beginnen.

## <a name="create-a-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Öffnen Sie Ihr Terminal- oder Befehlsfenster, erstellen Sie ein neues Verzeichnis für Ihre App, und navigieren Sie zu diesem Verzeichnis.

```console
mkdir chat-interop-quickstart && cd chat-interop-quickstart
```

Führen Sie `npm init -y` aus, um die Datei **package.json** mit den Standardeinstellungen zu erstellen.

```console
npm init -y
```

## <a name="install-the-chat-packages"></a>Installieren der Chatpakete

Verwenden Sie den Befehl `npm install`, um die notwendigen Communication Services-SDKs für JavaScript zu installieren.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

Mit der Option `--save` wird die Bibliothek als Abhängigkeit in Ihrer Datei **package.json** aufgelistet.

## <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

In dieser Schnellstartanleitung wird Webpack verwendet, um die Anwendungsressourcen zu bündeln. Führen Sie den folgenden Befehl aus, um die npm-Pakete „webpack“, „webpack-cli“ und „webpack-dev-server“ zu installieren und als Entwicklungsabhängigkeiten in Ihrer Datei **package.json** aufzulisten:

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Erstellen Sie im Stammverzeichnis Ihres Projekts die Datei **index.html**. Diese Datei wird zum Konfigurieren eines grundlegenden Layouts verwendet, das es dem Benutzer ermöglicht, einer Besprechung beizutreten und einen Chat zu beginnen.

## <a name="add-the-teams-ui-controls"></a>Hinzufügen der Steuerelemente der Teams-Benutzeroberfläche

Ersetzen Sie den Code in „index.html“ durch den folgenden Codeausschnitt.
In die Textfelder oben auf der Seite werden der Teams-Besprechungskontext und die Besprechungsthread-ID eingegeben. Über die Schaltfläche „An Teams-Besprechung teilnehmen“ nehmen Sie an der angegebenen Besprechung teil.
Unten auf der Seite wird ein Chatpopup angezeigt. Über dieses Popup können Sie Nachrichten im Besprechungsthread senden. Sofern der Communication Services-Benutzer Mitglied ist, werden alle im Thread gesendeten Nachrichten in Echtzeit angezeigt.

```html
<!DOCTYPE html>
<html>
   <head>
      <title>Communication Client - Calling and Chat Sample</title>
      <style>
         body {box-sizing: border-box;}
         /* The popup chat - hidden by default */
         .chat-popup {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #f1f1f1;
         z-index: 9;
         }
         .message-box {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #FFFACD;
         z-index: 9;
         }
         .form-container {
         max-width: 300px;
         padding: 10px;
         background-color: white;
         }
         .form-container textarea {
         width: 90%;
         padding: 15px;
         margin: 5px 0 22px 0;
         border: none;
         background: #e1e1e1;
         resize: none;
         min-height: 50px;
         }
         .form-container .btn {
         background-color: #4CAF40;
         color: white;
         padding: 14px 18px;
         margin-bottom:10px;
         opacity: 0.6;
         border: none;
         cursor: pointer;
         width: 100%;
         }
         .container {
         border: 1px solid #dedede;
         background-color: #F1F1F1;
         border-radius: 3px;
         padding: 8px;
         margin: 8px 0;
         }
         .darker {
         border-color: #ccc;
         background-color: #ffdab9;
         margin-left: 25px;
         margin-right: 3px;
         }
         .lighter {
         margin-right: 20px;
         margin-left: 3px;
         }
         .container::after {
         content: "";
         clear: both;
         display: table;
         }
      </style>
   </head>
   <body>
      <h4>Azure Communication Services</h4>
      <h1>Calling and Chat Quickstart</h1>
          <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
          <input id="thread-id-input" type="text" placeholder="Chat thread id"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
      <div>
        <button id="join-meeting-button" type="button">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
      </div>
      <div class="chat-popup" id="chat-box">
         <div id="messages-container"></div>
         <form class="form-container">
            <textarea placeholder="Type message.." name="msg" id="message-box" required></textarea>
            <button type="button" class="btn" id="send-message">Send</button>
         </form>
      </div>
      <script src="./bundle.js"></script>
   </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Aktivieren der Steuerelemente der Teams-Benutzeroberfläche

Ersetzen Sie den Inhalt der Datei „client.js“ durch den folgenden Codeausschnitt.

Ersetzen Sie in diesem Codeausschnitt 
- `SECRET_CONNECTION_STRING` durch die Verbindungszeichenfolge für Ihre Communication Services-Instanz 
- `ENDPOINT_URL` durch die Endpunkt-URL für Ihre Communication Services-Instanz

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { ChatClient } from "@azure/communication-chat";

let call;
let callAgent;
let chatClient;
let chatThreadClient;

const meetingLinkInput = document.getElementById('teams-link-input');
const threadIdInput = document.getElementById('thread-id-input');
const callButton = document.getElementById("join-meeting-button");
const hangUpButton = document.getElementById("hang-up-button");
const callStateElement = document.getElementById('call-state');

const messagesContainer = document.getElementById("messages-container");
const chatBox = document.getElementById("chat-box");
const sendMessageButton = document.getElementById("send-message");
const messagebox = document.getElementById("message-box");

var userId = '';
var lastMessage = '';
var previousMessage = '';

async function init() {
  const connectionString = "<SECRET_CONNECTION_STRING>";
  const endpointUrl = "<ENDPOINT_URL>";

  const identityClient = new CommunicationIdentityClient(connectionString);

  let identityResponse = await identityClient.createUser();
  userId = identityResponse.communicationUserId;
  console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);

  let tokenResponse = await identityClient.getToken(identityResponse, [
    "voip",
    "chat",
  ]);

  const { token, expiresOn } = tokenResponse;
  console.log(`\nIssued an access token that expires at: ${expiresOn}`);
  console.log(token);

  const callClient = new CallClient();
  const tokenCredential = new AzureCommunicationTokenCredential(token);
  callAgent = await callClient.createCallAgent(tokenCredential);
  callButton.disabled = false;

  chatClient = new ChatClient(
    endpointUrl,
    new AzureCommunicationTokenCredential(token)
  );

  console.log('Azure Communication Chat client created!');
}

init();

callButton.addEventListener("click", async () => {
  // join with meeting link
  call = callAgent.join({meetingLink: meetingLinkInput.value}, {});

  call.on('stateChanged', () => {
      callStateElement.innerText = call.state;
  })
  // toggle button and chat box states
  chatBox.style.display = "block";
  hangUpButton.disabled = false;
  callButton.disabled = true;

  messagesContainer.innerHTML = "";

  console.log(call);

  // open notifications channel
  await chatClient.startRealtimeNotifications();

  // subscribe to new message notifications
  chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");

      // check whether the notification is intended for the current thread
    if (threadIdInput.value != e.threadId) {
      return;
    }

    if (e.sender.communicationUserId != userId) {
       renderReceivedMessage(e.message);
    }
    else {
       renderSentMessage(e.message);
    }
  });

  chatThreadClient = await chatClient.getChatThreadClient(threadIdInput.value);
});

async function renderReceivedMessage(message) {
  previousMessage = lastMessage;
  lastMessage = '<div class="container lighter">' + message + '</div>';
  messagesContainer.innerHTML = previousMessage + lastMessage;
}

async function renderSentMessage(message) {
  previousMessage = lastMessage;
  lastMessage = '<div class="container darker">' + message + '</div>';
  messagesContainer.innerHTML = previousMessage + lastMessage;
}

hangUpButton.addEventListener("click", async () => 
  {
    // end the current call
    await call.hangUp();

    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    callStateElement.innerText = '-';

    // toggle chat states
    chatBox.style.display = "none";
    messages = "";
  });

sendMessageButton.addEventListener("click", async () =>
  {
    let message = messagebox.value;

    let sendMessageRequest = { content: message };
    let sendMessageOptions = { senderDisplayName : 'Jack' };
    let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
    let messageId = sendChatMessageResult.id;

    messagebox.value = '';
    console.log(`Message sent!, message id:${messageId}`);
  });
```

Die Anzeigenamen der Chatthread-Teilnehmer werden nicht vom Teams-Client festgelegt. Beim Ereignis `participantsAdded` und beim Ereignis `participantsRemoved` werden die Namen in der API für die Auflistung von Teilnehmern als NULL zurückgegeben. Die Anzeigenamen der Chatteilnehmer können aus dem `remoteParticipants`-Feld des `call`-Objekts abgerufen werden. Wenn Sie eine Benachrichtigung über eine Listenänderung erhalten, können Sie den folgenden Code verwenden, um den Namen des Benutzers abzurufen, der hinzugefügt oder entfernt wurde:

```
var displayName = call.remoteParticipants.find(p => p.identifier.communicationUserId == '<REMOTE_USER_ID>').displayName;
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Abrufen eines Teams-Besprechungschatthreads für einen Communication Services-Benutzer

Die Details für die Teams-Besprechung können über Graph-APIs abgerufen werden, wie in der [Graph-Dokumentation](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) ausführlich erläutert. Das Communication Services Calling SDK akzeptiert einen vollständigen Teams-Besprechungslink oder eine Besprechungs-ID. Beide werden als Teil der `onlineMeeting`-Ressource zurückgegeben, auf die unter der [`joinWebUrl`-Eigenschaft](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) zugegriffen werden kann.

Sie können die `threadID` auch mit den [Graph-APIs](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) abrufen. Die Antwort enthält ein `chatInfo`-Objekt, das die `threadID` enthält. 

Sie können die erforderlichen Besprechungsinformationen und die Thread-ID auch über die URL unter **An Besprechung teilnehmen** in der Einladung zur Teams-Besprechung abrufen.
Ein Teams-Besprechungslink sieht wie folgt aus: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. Die `threadId` befindet sich dort, wo im Link `meeting_chat_thread_id` steht. Stellen Sie sicher, dass vor der Verwendung Escapezeichen aus der `meeting_chat_thread_id` entfernt werden. Die ID sollte das folgende Format aufweisen: `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`.


## <a name="run-the-code"></a>Ausführen des Codes

Webpack-Benutzer können `webpack-dev-server` verwenden, um Ihre App zu erstellen und auszuführen. Führen Sie den folgenden Befehl aus, um Ihren Anwendungshost auf einem lokalen Webserver zu bündeln:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Navigieren Sie in Ihrem Browser zu http://localhost:8080/. Daraufhin sollte Folgendes angezeigt werden:

:::image type="content" source="../join-teams-meeting-chat-quickstart.png" alt-text="Screenshot der fertigen JavaScript-Anwendung":::

Fügen Sie den Teams-Besprechungslink und die Thread-ID in die Textfelder ein. Klicken Sie auf *An Teams-Besprechung teilnehmen*, um an der Teams-Besprechung teilzunehmen. Nachdem der Communication Services-Benutzer zur Besprechung zugelassen wurde, können Sie über Ihre Communication Services-Anwendung chatten. Navigieren Sie zu dem Feld unten auf der Seite, um mit dem Chatten zu beginnen. Der Einfachheit halber zeigt die Anwendung nur die letzten beiden Nachrichten im Chat an.

> [!NOTE] 
> Derzeit werden für Interoperabilitätsszenarios mit Teams nur das Senden, Empfangen und Bearbeiten von Nachrichten sowie das Senden von Eingabebenachrichtigungen unterstützt. Andere Features wie Lesebestätigungen oder das Hinzufügen oder Entfernen anderer Benutzer*innen aus der Teams-Besprechung durch Communication Services-Benutzer*innen werden nicht unterstützt.
