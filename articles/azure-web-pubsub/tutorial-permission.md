---
title: 'Tutorial: Hinzufügen von Authentifizierung und Berechtigungen zu Ihrer Anwendung bei Verwendung des Azure Web PubSub-Diensts'
description: In diesem Tutorial wird Ihnen gezeigt, wie Sie Ihrer Anwendung eine Authentifizierungsfunktion und Berechtigungen hinzufügen, wenn Sie den Azure Web PubSub-Dienst verwenden.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 08/26/2021
ms.openlocfilehash: cca4951aac8844c13f36ebb58f8c326cec7b6104
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117510"
---
# <a name="tutorial-add-authentication-and-permissions-to-your-application-when-using-azure-web-pubsub-service"></a>Tutorial: Hinzufügen von Authentifizierung und Berechtigungen zu Ihrer Anwendung bei Verwendung des Azure Web PubSub-Diensts

Im [Tutorial zum Erstellen einer Chat-App](./tutorial-build-chat.md) haben Sie gelernt, wie Sie WebSocket-APIs zum Senden und Empfangen von Daten mit Azure Web PubSub verwenden. Ihnen ist wahrscheinlich aufgefallen, dass der Einfachheit halber keine Authentifizierung erforderlich ist. Obwohl Azure Web PubSub zum Herstellen einer Verbindung ein Zugriffstoken benötigt, ist für die `negotiate`-API, die wir im Tutorial zum Generieren des Zugriffstokens verwendet haben, keine Authentifizierung erforderlich. Somit kann jeder diese API aufrufen, um ein Zugriffstoken zu erhalten.

In einer realen Anwendung ist es zum Schutz vor Missbrauch üblich, dass sich der Benutzer zuerst anmeldet, bevor er Ihre Anwendung verwenden kann. In diesem Tutorial erfahren Sie, wie Sie Azure Web PubSub in das Authentifizierungs-/Autorisierungssystem Ihrer Anwendung integrieren, um die Sicherheit zu erhöhen.

Das vollständige Codebeispiel für dieses Tutorial finden Sie [hier][code].

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktivieren der GitHub-Authentifizierung
> * Hinzufügen von Authentifizierungsmiddleware zu Ihrer Anwendung
> * Hinzufügen von Berechtigungen zu den Clients

## <a name="add-authentication-to-the-chat-room-app"></a>Hinzufügen der Authentifizierung zur Chatroom-App

In diesem Tutorial wird die Chatanwendung wiederverwendet, die im [Tutorial zum Erstellen einer Chat-App](./tutorial-build-chat.md) erstellt wurde. [Hier][chat-js] können Sie auch das vollständige Codebeispiel für die Chat-App klonen. 

In diesem Tutorial fügen wir der Chatanwendung eine Authentifizierung hinzu und integrieren sie in den Azure Web PubSub-Dienst.

Zunächst fügen wir dem Chatraum die GitHub-Authentifizierung hinzu, damit Benutzer sich mit ihrem GitHub-Konto anmelden können.

1.  Installieren von Abhängigkeiten

    ```bash
    npm install --save cookie-parser
    npm install --save express-session
    npm install --save passport
    npm install --save passport-github2
    ```

2.  Fügen Sie in `server.js` den folgenden Code hinzu, um die GitHub-Authentifizierung zu aktivieren.

    ```javascript
    const app = express();

    const users = {};
    passport.use(
      new GitHubStrategy({
        clientID: process.argv[3],
        clientSecret: process.argv[4]
      },
      (accessToken, refreshToken, profile, done) => {
        users[profile.id] = profile;
        return done(null, profile);
      }
    ));

    passport.serializeUser((user, done) => {
      done(null, user.id);
    });

    passport.deserializeUser((id, done) => {
      if (users[id]) return done(null, users[id]);
      return done(`invalid user id: ${id}`);
    });

    app.use(cookieParser());
    app.use(session({
      resave: false,
      saveUninitialized: true,
      secret: 'keyboard cat'
    }));
    app.use(passport.initialize());
    app.use(passport.session());
    app.get('/auth/github', passport.authenticate('github', { scope: ['user:email'] }));
    app.get('/auth/github/callback', passport.authenticate('github', { successRedirect: '/' }));
    ```

    Im obigen Code wird [Passport.js](http://www.passportjs.org/) zum Aktivieren der GitHub-Authentifizierung verwendet. Hier finden Sie eine einfache Darstellung der Funktionsweise:

    1. `/auth/github` leitet Sie zur Anmeldung an github.com um.
    2. Nachdem die Anmeldung abgeschlossen ist, leitet GitHub Sie mit einem Code zu `/auth/github/callback` weiter, damit Ihre Anwendung die Authentifizierung abschließen kann. (Informationen dazu, wie das von GitHub zurückgegebene Profil auf dem Server überprüft und dauerhaft gespeichert wird, finden Sie im Verifizierungsrückruf in `passport.use()`.)
    3. Nach Abschluss der Authentifizierung werden Sie zur Startseite (`/`) der Website umgeleitet.
 
    Weitere Informationen zu OAuth und „Passport.js“ in GitHub finden Sie in den folgenden Artikeln:

    - [Autorisieren von OAuth-Apps](https://docs.github.com/en/developers/apps/authorizing-oauth-apps)
    - [Dokument „Passport.js“](http://www.passportjs.org/docs/)

    Um dies zu testen, müssen Sie zuerst eine GitHub-OAuth-App erstellen:

    1. Wechseln Sie zu https://www.github.com, und öffnen Sie Ihr Profil > „Einstellungen“ > „Entwicklereinstellungen“.
    2. Wechseln Sie zu OAuth-Apps, und klicken Sie auf „Neue OAuth-App“.
    3. Geben Sie den Anwendungsnamen und eine beliebige Homepage-URL ein, und legen Sie die URL für den Autorisierungsrückruf auf `http://localhost:8080/auth/github/callback` fest. (Dies entspricht der Rückruf-API, die Sie auf dem Server verfügbar gemacht haben.)
    4. Nach dem Registrieren der Anwendung kopieren Sie die Client-ID und klicken auf „Neuen geheimen Clientschlüssel generieren“, um einen neuen geheimen Clientschlüssel zu generieren.

    Führen Sie dann `node server <connection-string> <client-id> <client-secret>` aus, und öffnen Sie `http://localhost:8080/auth/github`. Sie werden zur Anmeldung an GitHub umgeleitet. Nachdem die Anmeldung erfolgreich war, werden Sie wieder zur Chatanwendung umgeleitet.

3.  Aktualisieren wir jetzt den Chatroom, damit die von GitHub erhaltene Identität genutzt wird, anstatt ein Dialogfeld zum Abfragen des Benutzernamens anzuzeigen.

    Aktualisieren Sie `public/index.html`, sodass `/negotiate` direkt und ohne Übergabe einer Benutzer-ID aufgerufen wird.

    ```javascript
    let messages = document.querySelector('#messages');
    let res = await fetch(`/negotiate`);
    if (res.status === 401) {
      let m = document.createElement('p');
      m.innerHTML = 'Not authorized, click <a href="/auth/github">here</a> to login';
      messages.append(m);
      return;
    }
    let data = await res.json();
    let ws = new WebSocket(data.url);
    ```

    Wenn ein Benutzer angemeldet ist, weist die Anforderung über ein Cookie automatisch die Identität des Benutzers auf. Daher müssen wir nur überprüfen, ob der Benutzer im `req`-Objekt vorhanden ist, und den Benutzernamen dem Web PubSub-Zugriffstoken hinzufügen:

    ```javascript
    app.get('/negotiate', async (req, res) => {
      if (!req.user || !req.user.username) {
        res.status(401).send('missing user id');
        return;
      }
      let options = {
        userId: req.user.username
      };
      let token = await serviceClient.getAuthenticationToken(options);
      res.json({
        url: token.url
      });
    });
    ```

    Führen Sie jetzt den Server erneut aus. Beim ersten Öffnen des Chatrooms wird Ihnen die Meldung „Nicht autorisiert“ angezeigt. Klicken Sie auf den Anmeldelink, um sich anzumelden. Sie stellen fest, dass die Anmeldung genau wie zuvor funktioniert.

## <a name="working-with-permissions"></a>Arbeiten mit Berechtigungen

In den vorherigen Tutorials haben Sie gelernt, wie Sie Nachrichten mithilfe von `WebSocket.send()` anhand eines Unterprotokolls direkt auf anderen Clients veröffentlichen. In einer realen Anwendung sollte der Client keine Nachrichten ohne Berechtigungssteuerung in beliebigen Gruppen veröffentlichen/abonnieren können. In diesem Abschnitt erfahren Sie, wie Sie Clients mithilfe des Berechtigungssystems von Azure Web PubSub steuern.

In Azure Web PubSub gibt es drei Arten von Vorgängen, die ein Client mit dem Unterprotokoll durchführen kann:

- Senden von Ereignissen an den Server
- Veröffentlichen von Nachrichten in einer Gruppe
- Beitreten zu (Abonnieren) einer Gruppe

Das Senden eines Ereignisses an den Server ist der Standardvorgang des Clients, der auch dann zulässig ist, wenn kein Protokoll verwendet wird. Um eine Gruppe zu veröffentlichen und zu abonnieren, muss der Client eine Berechtigung erhalten. Es gibt für den Server zwei Möglichkeiten zum Erteilen von Berechtigungen an Clients:

- Angeben von Rollen bei Verbindungsherstellung eines Clients (Rolle ist ein Konzept zur Darstellung der Anfangsberechtigungen bei der Verbindungsherstellung eines Clients)
- Verwenden der API zum Erteilen einer Berechtigung an einen Client nach der Verbindungsherstellung

Für die Berechtigung zum Gruppenbeitritt muss der Client der Gruppe weiterhin über die Gruppenbeitrittsmeldung beitreten, auch nachdem er die Berechtigung erhalten hat. Alternativ dazu kann der Server auch die API verwenden, um den Client selbst dann einer Gruppe hinzuzufügen, wenn dieser nicht über die Beitrittsberechtigung verfügt.

Verwenden wir jetzt dieses Berechtigungssystem, um dem Chatroom ein neues Feature hinzuzufügen. Wir fügen dem Chatroom einen neuen Benutzertyp namens „Administrator“ hinzu. Dem Administrator wird gestattet, Systemnachrichten (Nachricht beginnt mit „[SYSTEM]“) direkt vom Client aus zu senden.

Zuerst müssen wir System- und Benutzernachrichten in zwei verschiedene Gruppen unterteilen, damit ihre Berechtigungen separat gesteuert werden können.

Ändern Sie `server.js`, sodass verschiedene Nachrichten an verschiedene Gruppen gesendet werden:

```javascript
let handler = new WebPubSubEventHandler(hubName, ['*'], {
  path: '/eventhandler',
  handleConnect: (req, res) => {
    res.success({
      groups: ['system', 'message'],
    });
  },
  onConnected: req => {
    console.log(`${req.context.userId} connected`);
    serviceClient.group('system').sendToAll(`${req.context.userId} joined`, { contentType: 'text/plain' });
  },
  handleUserEvent: (req, res) => {
    if (req.context.eventName === 'message') {
      serviceClient.group('message').sendToAll({
        user: req.context.userId,
        message: req.data
      });
    }
    res.success();
  }
});
```

Sie sehen, dass im obigen Code `WebPubSubServiceClient.group().sendToAll()` verwendet wird, um Nachrichten an die Gruppe statt an den Hub zu senden.

Da die Nachricht jetzt an Gruppen gesendet wird, müssen wir Clients den Gruppen hinzufügen, damit sie weiterhin Nachrichten empfangen können. Verwenden Sie dazu die `handleConnect`-Methode.

> [!Note]
> `handleConnect` wird ausgelöst, wenn ein Client versucht, eine Verbindung mit Azure Web PubSub herzustellen. In diesem Handler können Sie Gruppen und Rollen zurückgeben, sodass der Dienst Verbindungen zu Gruppen hinzufügen oder Rollen zuweisen kann, sobald die Verbindung hergestellt wurde. Der Dienst kann auch `res.fail()` verwenden, um die Verbindung zu verweigern.
>

Damit `handleConnect` ausgelöst wird, wechseln Sie im Azure-Portal zu den Ereignishandlereinstellungen, und aktivieren Sie `connect` in den Systemereignissen.

Wir müssen auch das Client-HTML aktualisieren, weil der Server jetzt JSON-Nachrichten anstelle von Nur-Text sendet:

```javascript
let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
ws.onopen = () => console.log('connected');

ws.onmessage = event => {
  let m = document.createElement('p');
  let message = JSON.parse(event.data);
  switch (message.type) {
    case 'message':
      if (message.group === 'system') m.innerText = `[SYSTEM] ${message.data}`;
      else if (message.group === 'message') m.innerText = `[${message.data.user}] ${message.data.message}`;
      break;
  }
  messages.appendChild(m);
};

let message = document.querySelector('#message');
message.addEventListener('keypress', e => {
  if (e.charCode !== 13) return;
  ws.send(JSON.stringify({
    type: 'event',
    event: 'message',
    dataType: 'text',
    data: message.value
  }));
  message.value = '';
});
```

Ändern Sie dann den Clientcode so, dass Nachrichten an die Systemgruppe gesendet werden, wenn Benutzer auf „Systemnachricht“ klicken:

```html
<button id="system">system message</button>
...
<script>
  (async function() {
    ...
    let system = document.querySelector('#system');
    system.addEventListener('click', e => {
      ws.send(JSON.stringify({
        type: 'sendToGroup',
        group: 'system',
        dataType: 'text',
        data: message.value
      }));
      message.value = '';
    });
  })();
</script>
```

Standardmäßig verfügt der Client nicht über die Berechtigung zum Senden an eine beliebige Gruppe. Aktualisieren Sie den Servercode, um Administratorbenutzern die Berechtigung zu erteilen. (Der Einfachheit halber wird die ID des Administrators als Befehlszeilenargument bereitgestellt.)

```javascript
app.get('/negotiate', async (req, res) => {
  ...
  if (req.user.username === process.argv[5]) options.claims = { role: ['webpubsub.sendToGroup.system'] };
  let token = await serviceClient.getAuthenticationToken(options);
});
```

Führen Sie jetzt `node server <connection-string> <client-id> <client-secret> <admin-id>` aus. Sie stellen fest, dass Sie eine Systemnachricht an jeden Client senden können, wenn Sie sich als `<admin-id>` anmelden.

Wenn Sie sich jedoch als ein anderer Benutzer anmelden und auf „Systemnachricht“ klicken, geschieht nichts. Vielleicht sollte der Dienst Sie in einer Fehlermeldung darüber informieren, dass der Vorgang nicht zulässig ist. Dies ist durch Festlegen einer `ackId` beim Veröffentlichen der Nachricht möglich. Wenn `ackId` angegeben wird, gibt Azure Web PubSub eine Bestätigungsnachricht mit einer entsprechenden `ackId` zurück, die angibt, ob der Vorgang erfolgreich war.

Ändern Sie den Code für das Senden von Systemnachrichten in den folgenden Code:

```javascript
let ackId = 0;
system.addEventListener('click', e => {
  ws.send(JSON.stringify({
    type: 'sendToGroup',
    group: 'system',
    ackId: ++ackId,
    dataType: 'text',
    data: message.value
    }));
  message.value = '';
});
```

Ändern Sie außerdem den Code für die Verarbeitung von Nachrichten, um eine Bestätigungsnachricht zu verarbeiten:

```javascript
ws.onmessage = event => {
  ...
  switch (message.type) {
    case 'ack':
      if (!message.success && message.error.name === 'Forbidden') m.innerText = 'No permission to send system message';
      break;
  }
};
```

Führen Sie nun den Server erneut aus, und melden Sie sich als anderer Benutzer an. Bei dem Versuch, eine Systemnachricht zu senden, wird eine Fehlermeldung angezeigt.

Das vollständige Codebeispiel für dieses Tutorial finden Sie [hier][code].

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie grundlegende Informationen dazu erhalten, wie Sie über das Unterprotokoll eine Verbindung mit dem Web PubSub-Dienst herstellen und Nachrichten auf den verbundenen Clients veröffentlichen.

Bearbeiten Sie weitere Tutorials, um mehr über die Nutzung des Diensts zu erfahren.

> [!div class="nextstepaction"]
> [Weitere Beispiele für Azure Web PubSub](https://aka.ms/awps/samples)

[code]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/githubchat/
[chat-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp
