---
title: 'Tutorial: Hinzufügen von Authentifizierung und Berechtigungen zu Ihrer Anwendung bei Verwendung von Azure Web PubSub'
description: Exemplarische Vorgehensweise zum Hinzufügen von Authentifizierung und Berechtigungen zu Ihrer Anwendung bei Verwendung von Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/01/2021
ms.openlocfilehash: 027cd7197167a3667748c2c470e17b83aa3bf03d
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578723"
---
# <a name="tutorial-add-authentication-and-permissions-to-your-application-when-using-azure-web-pubsub"></a>Tutorial: Hinzufügen von Authentifizierung und Berechtigungen zu Ihrer Anwendung bei Verwendung von Azure Web PubSub

In [Erstellen einer Chat-App](./tutorial-build-chat.md) haben Sie erfahren, wie Sie WebSocket-APIs zum Senden und Empfangen von Daten mit Azure Web PubSub verwenden. Ihnen ist wahrscheinlich aufgefallen, dass der Einfachheit halber keine Authentifizierung erforderlich ist. Obwohl Azure Web PubSub für die Verbindung ein Zugriffstoken benötigt, ist für die im Tutorial verwendete `negotiate`-API zur Generierung des Zugriffstokens keine Authentifizierung erforderlich. Jeder kann diese API aufrufen, um ein Zugriffstoken zu erhalten.

In einer realen Anwendung möchten Sie normalerweise, dass sich der Benutzer zuerst anmeldet, bevor er Ihre Anwendung verwenden kann. In diesem Tutorial erfahren Sie, wie Sie Web PubSub in das Authentifizierungs- und Autorisierungssystem Ihrer Anwendung integrieren, um die Sicherheit zu erhöhen.

Das vollständige Codebeispiel dieses Tutorials finden Sie auf [GitHub][code].

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktivieren der GitHub-Authentifizierung
> * Hinzufügen von Authentifizierungsmiddleware zu Ihrer Anwendung
> * Hinzufügen von Berechtigungen zu den Clients

## <a name="add-authentication-to-the-chat-room-app"></a>Hinzufügen der Authentifizierung zur Chatroom-App

In diesem Tutorial wird die Chatanwendung wiederverwendet, die unter [Erstellen einer Chat-App](./tutorial-build-chat.md) erstellt wurde. Sie können auch das vollständige Codebeispiel für die Chat-App von [GitHub][chat-js] klonen. 

In diesem Tutorial fügen Sie der Chatanwendung eine Authentifizierung hinzu und integrieren sie in Web PubSub.

Zunächst fügen Sie dem Chatraum die GitHub-Authentifizierung hinzu, damit der Benutzer sich mit einem GitHub-Konto anmelden kann.

1.  Installieren Sie Abhängigkeiten.

    ```bash
    npm install --save cookie-parser
    npm install --save express-session
    npm install --save passport
    npm install --save passport-github2
    ```

1.  Aktivieren Sie die GitHub-Authentifizierung, indem Sie den folgenden Code zu `server.js` hinzufügen:

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

    Im vorangehenden Code wird [Passport.js](http://www.passportjs.org/) zum Aktivieren der GitHub-Authentifizierung verwendet. Hier folgt eine einfache Veranschaulichung, wie das funktioniert:

    1. `/auth/github` leitet zur Anmeldung auf „github.com“ weiter.
    1. Nach der Anmeldung leitet GitHub Sie mit einem Code zu `/auth/github/callback` weiter, damit Ihre Anwendung die Authentifizierung abschließen kann. (Informationen dazu, wie das von GitHub zurückgegebene Profil auf dem Server überprüft und dauerhaft gespeichert wird, finden Sie im Verifizierungsrückruf in `passport.use()`.)
    1. Nach Abschluss der Authentifizierung werden Sie zur Startseite (`/`) der Website umgeleitet.
 
    Weitere Informationen zu OAuth und „Passport.js“ in GitHub finden Sie in den folgenden Artikeln:

    - [Autorisieren von OAuth-Apps](https://docs.github.com/en/developers/apps/authorizing-oauth-apps)
    - [Dokument „Passport.js“](http://www.passportjs.org/docs/)

    Um dies zu testen, müssen Sie zuerst eine GitHub-OAuth-App erstellen:

    1. Wechseln Sie zu https://www.github.com, öffnen Sie Ihr Profil, und wählen Sie **Einstellungen** > **Entwicklereinstellungen** aus.
    1. Wechseln Sie zu OAuth-Apps, und wählen Sie **Neue OAuth-App** aus.
    1. Geben Sie den Namen der Anwendung und die Homepage-URL ein (die URL kann beliebig sein), und legen Sie die **Autorisierungsrückruf-URL** auf `http://localhost:8080/auth/github/callback` fest. Diese URL entspricht der Rückruf-API, die Sie auf dem Server bereitgestellt haben.
    1. Kopieren Sie nach der Registrierung der Anwendung die Client-ID, und wählen Sie **Generate a new client secret** (Neues Clientgeheimnis generieren) aus.

    Führen Sie den folgenden Befehl aus, um die Einstellungen zu testen. Ersetzen Sie dabei `<connection-string>`, `<client-id>` und `<client-secret>` durch Ihre Werte.

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    export GitHubClientId="<client-id>"
    export GitHubClientSecret="<client-secret>"
    node server
    ```
    
    Öffnen Sie jetzt `http://localhost:8080/auth/github`. Sie werden zu GitHub weitergeleitet, um sich anzumelden. Nach der Anmeldung werden Sie zur Chatanwendung umgeleitet.

1.  Aktualisieren Sie den Chatraum, um die Identität zu verwenden, die Sie von GitHub erhalten, anstatt den Benutzer zur Eingabe eines Benutzernamens aufzufordern.

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

    Wenn ein Benutzer angemeldet ist, enthält die Anforderung automatisch die Identität des Benutzers in Form eines Cookies. Daher müssen Sie nur überprüfen, ob der Benutzer im `req`-Objekt vorhanden ist, und den Benutzernamen dem Web PubSub-Zugriffstoken hinzufügen:

    ```javascript
    app.get('/negotiate', async (req, res) => {
      if (!req.user || !req.user.username) {
        res.status(401).send('missing user id');
        return;
      }
      let options = {
        userId: req.user.username
      };
      let token = await serviceClient.getClientAccessToken(options);
      res.json({
        url: token.url
      });
    });
    ```

    Führen Sie jetzt den Server erneut aus. Beim ersten Öffnen des Chatrooms wird Ihnen die Meldung „Nicht autorisiert“ angezeigt. Wählen Sie den Anmeldelink aus, um sich anzumelden, und Sie werden sehen, dass es wie zuvor funktioniert.

## <a name="work-with-permissions"></a>Arbeiten mit Berechtigungen

In den vorherigen Tutorials haben Sie gelernt, wie Sie Nachrichten mithilfe von `WebSocket.send()` anhand eines Unterprotokolls direkt auf anderen Clients veröffentlichen. In einer realen Anwendung sollte der Client keine Nachrichten ohne Berechtigungssteuerung in beliebigen Gruppen veröffentlichen oder abonnieren können. In diesem Abschnitt erfahren Sie, wie Sie Clients mithilfe des Berechtigungssystems von Web PubSub steuern.

In Web PubSub kann ein Client die folgenden Arten von Vorgängen mit Unterprotokoll ausführen:

- Senden von Ereignissen an den Server
- Veröffentlichen von Nachrichten in einer Gruppe
- Beitreten zu (Abonnieren) einer Gruppe

Das Senden eines Ereignisses an den Server ist der Standardvorgang des Clients. Es wird kein Protokoll verwendet, daher ist es immer zulässig. Um eine Gruppe zu veröffentlichen und zu abonnieren, muss der Client eine Berechtigung erhalten. Es gibt für den Server zwei Möglichkeiten zum Erteilen von Berechtigungen an Clients:

- Angeben von Rollen bei Verbindungsherstellung eines Clients (*Rolle* ist ein Konzept zur Darstellung der Anfangsberechtigungen bei der Verbindungsherstellung eines Clients).
- Verwenden einer API zum Erteilen einer Berechtigung an einen Client nach der Verbindungsherstellung.

Für die Berechtigung zum Gruppenbeitritt muss der Client der Gruppe weiterhin über die Meldung „Gruppe beitreten“ beitreten, auch nachdem er die Berechtigung erhalten hat. Alternativ kann der Server eine API verwenden, um den Client zu einer Gruppe hinzuzufügen, auch wenn er nicht über die Berechtigung zum Beitritt verfügt.

Verwenden wir jetzt dieses Berechtigungssystem, um dem Chatroom ein neues Feature hinzuzufügen. Sie fügen dem Chatraum einen neuen Benutzertyp namens *Administrator* hinzu. Sie gestatten dem Administrator, Systemmeldungen (Nachrichten, die mit „[SYSTEM]“) beginnen, direkt vom Client zu senden.

Zuerst müssen Sie System- und Benutzernachrichten in zwei verschiedene Gruppen unterteilen, damit Sie ihre Berechtigungen separat steuern können.

Ändern Sie `server.js`, sodass verschiedene Nachrichten an verschiedene Gruppen gesendet werden:

```javascript
let handler = new WebPubSubEventHandler(hubName, {
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

Der vorangehende Code verwendet `WebPubSubServiceClient.group().sendToAll()`, um die Nachricht an eine Gruppe statt an den Hub zu senden.

Da die Nachricht jetzt an Gruppen gesendet wird, müssen Sie Clients den Gruppen hinzufügen, damit sie weiterhin Nachrichten empfangen können. Verwenden Sie den `handleConnect`-Handler, um Clients zu Gruppen hinzuzufügen.

> [!Note]
> `handleConnect` wird ausgelöst, wenn ein Client versucht, eine Verbindung mit Web PubSub herzustellen. In diesem Handler können Sie Gruppen und Rollen zurückgeben, sodass der Dienst eine Verbindung zu Gruppen hinzufügen oder Rollen zuweisen kann, sobald die Verbindung hergestellt wurde. Der Dienst kann auch `res.fail()` verwenden, um die Verbindung zu verweigern.
>

Um `handleConnect` auszulösen, wechseln Sie zu den Ereignishandlereinstellungen im Azure-Portal, und wählen Sie **Verbinden** in den Systemereignissen aus.

Sie müssen auch das Client-HTML aktualisieren, da der Server jetzt JSON-Nachrichten anstelle von Nur-Text sendet:

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

Ändern Sie dann den Clientcode so, dass Nachrichten an die Systemgruppe gesendet werden, wenn Benutzer **Systemnachricht** auswählen:

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

Standardmäßig verfügt der Client nicht über die Berechtigung zum Senden an eine Gruppe. Aktualisieren Sie den Servercode, um die Berechtigung für den Administratorbenutzer zu erteilen (der Einfachheit halber wird die ID des Administrators als Befehlszeilenargument bereitgestellt).

```javascript
app.get('/negotiate', async (req, res) => {
  ...
  if (req.user.username === process.argv[2]) options.claims = { role: ['webpubsub.sendToGroup.system'] };
  let token = await serviceClient.getClientAccessToken(options);
});
```

Führen Sie jetzt `node server <admin-id>` aus. Sie stellen fest, dass Sie eine Systemnachricht an jeden Client senden können, wenn Sie sich als `<admin-id>` anmelden.

Wenn Sie sich jedoch als ein anderer Benutzer anmelden, passiert nichts, wenn Sie die **Systemmeldung** auswählen. Sie könnten erwarten, dass der Dienst eine Fehlermeldung ausgibt, um Sie darauf hinzuweisen, dass der Vorgang nicht zulässig ist. Um dieses Feedback bereitzustellen, können Sie `ackId` festlegen, wenn Sie die Nachricht veröffentlichen. Wenn `ackId` angegeben wird, gibt Web PubSub eine Nachricht mit einer entsprechenden `ackId` zurück, die angibt, ob der Vorgang erfolgreich war.

Ändern Sie den Code für das Senden einer Systemnachricht in den folgenden Code:

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

Ändern Sie außerdem den Code für die Verarbeitung von Nachrichten, um eine `ack`-Nachricht zu verarbeiten:

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

Führen Sie nun den Server erneut aus, und melden Sie sich als anderer Benutzer an. Wenn Sie versuchen, eine Systemmeldung zu senden, wird eine Fehlermeldung angezeigt.

Das vollständige Codebeispiel für dieses Tutorial finden Sie auf [GitHub][code].

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie grundlegende Informationen dazu erhalten, wie Sie über das Unterprotokoll eine Verbindung mit dem Web PubSub-Dienst herstellen und Nachrichten auf den verbundenen Clients veröffentlichen.

Weitere Informationen zur Verwendung des Web PubSub-Diensts finden Sie in den anderen Tutorials in der Dokumentation.

> [!div class="nextstepaction"]
> [Weitere Beispiele für Azure Web PubSub](https://aka.ms/awps/samples)

[code]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/githubchat/
[chat-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp
