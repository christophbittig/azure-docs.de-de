---
title: Ausführliche Informationen zum Azure Web PubSub-Dienst
description: Hier erfahren Sie mehr über die Details, Architektur, Verbindungen und Datenübertragung beim Azure Web PubSub-Dienst.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: ec1a0bfcc883e9434c8c80264c574c3630e395b0
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995084"
---
#  <a name="azure-web-pubsub-service-internals"></a>Ausführliche Informationen zum Azure Web PubSub-Dienst

Der Azure Web PubSub-Dienst bietet eine einfache Möglichkeit, um Nachrichten über [WebSocket](https://tools.ietf.org/html/rfc6455)-Verbindungen zu veröffentlichen und zu abonnieren.

- Der Client kann in einer beliebigen Sprache geschrieben werden, die WebSocket unterstützt.
- Sowohl Text- als auch Binärnachrichten werden innerhalb einer Verbindung unterstützt.
- Ein einfaches Protokoll zur direkten Veröffentlichung von Nachrichten zwischen Clients.
- Der Dienst verwaltet die WebSocket-Verbindungen für Sie.

## <a name="terms"></a>Begriffe
* **Dienst**: Azure Web PubSub.

[!INCLUDE [Terms](includes/terms.md)]

* **Clientverbindung** und **Verbindungs-ID**: Ein Client stellt eine Verbindung mit dem `/client`-Endpunkt her. Nachdem die Verbindung hergestellt wurde, generiert der Dienst einen eindeutigen `connectionId`-Wert zur eindeutigen Identifizierung der Clientverbindung. Benutzer können die Clientverbindung dann mit diesem `connectionId`-Wert verwalten. Einzelheiten finden Sie im Abschnitt [Clientprotokoll](#client_protocol).

* **Clientereignisse**: Ereignisse werden während des Lebenszyklus einer Clientverbindung erstellt. Eine einfache WebSocket-Clientverbindung erstellt z. B. ein `connect`-Ereignis, wenn versucht wird, eine Verbindung mit dem Dienst herzustellen, ein `connected`-Ereignis, wenn erfolgreich eine Verbindung mit dem Dienst hergestellt wurde, ein `message`-Ereignis, wenn Nachrichten an den Dienst gesendet werden und ein `disconnected`-Ereignis, wenn die Verbindung mit dem Dienst getrennt wird. Einzelheiten zu *Clientereignissen* finden Sie im Abschnitt [Clientprotokoll](#client_protocol).

* **Ereignishandler**: Der Ereignishandler enthält die Logik zum Behandeln der Clientereignisse. Ereignishandler sollten vorab über das Portal oder über die Azure CLI im Dienst registriert und konfiguriert werden. Einzelheiten finden Sie im Abschnitt [Ereignishandler](#event_handler). Der Ort zum Hosten der Ereignishandlerlogik wird als Serverseite betrachtet.

* **Server**: Der Server kann Clientereignisse verarbeiten, Clientverbindungen verwalten und Nachrichten in Gruppen veröffentlichen. Gegenüber dem Client ist der Server vertrauenswürdig. Einzelheiten zum **Server** finden Sie im Abschnitt [Serverprotokoll](#server_protocol).

<a name="workflow"></a>

## <a name="workflow"></a>Workflow

![Darstellung des Web PubSub-Dienstworkflows.](./media/concept-service-internals/workflow.png)

Das obige Workflowdiagramm zeigt Folgendes:
1. Ein *Client* stellt über den WebSocket-Transport eine Verbindung mit dem `/client`-Endpunkt des Diensts her. Der Dienst leitet sämtliche WebSocket-Frames an den konfigurierten Upstream(server) weiter. Über die WebSocket-Verbindung kann eine Verbindung mit einem beliebigen benutzerdefinierten Unterprotokoll hergestellt werden, das der Server verarbeiten soll. Alternativ kann eine Verbindung mit dem vom Dienst unterstützten Unterprotokoll `json.webpubsub.azure.v1` hergestellt werden, wodurch die Clients Nachrichten direkt veröffentlichen oder abonnieren können. Einzelheiten finden Sie unter [Clientprotokoll](#client_protocol).
2. Der Dienst ruft den Server über das **CloudEvents-HTTP-Protokoll** für unterschiedliche Clientereignisse auf. [**CloudEvents**](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md) ist eine standardisierte und protokollunabhängige Definition der Struktur und Metadatenbeschreibung von Ereignissen, die von der Cloud Native Computing Foundation (CNCF) gehostet werden. Einzelheiten finden Sie unter [Serverprotokoll](#server_protocol).
3. Der Server kann den Dienst mithilfe der REST-API aufrufen, um Nachrichten an Clients zu senden oder die verbundenen Clients zu verwalten. Einzelheiten finden Sie unter [Serverprotokoll](#server_protocol).

<a name="client_protocol"></a>

## <a name="client-protocol"></a>Clientprotokoll

Über eine Clientverbindung wird unter Verwendung des [WebSocket-Protokolls](https://tools.ietf.org/html/rfc6455) eine Verbindung mit dem `/client`-Endpunkt des Diensts hergestellt. Das WebSocket-Protokoll bietet Vollduplex-Kommunikationskanäle über eine einzelne TCP-Verbindung und wurde im Jahr 2011 durch die IETF als RFC 6455 standardisiert. Die meisten Sprachen bieten native Unterstützung für das Starten von WebSocket-Verbindungen. 

Unser Dienst unterstützt zwei Arten von Clients:
- den [einfachen WebSocket-Client](#simple_client)
- und den [PubSub-WebSocket-Client](#pubsub_client)

<a name="simple_client"></a>

### <a name="the-simple-websocket-client"></a>Einfacher WebSocket-Client
Ein einfacher WebSocket-Client ist, wie die Name schon sagt, eine einfache WebSocket-Verbindung. Er kann auch sein eigenes Unterprotokoll besitzen. 

Beispielsweise kann in JS ein einfacher WebSocket-Client mithilfe folgender Komponenten erstellt werden:
```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')

```

Bei einem einfachen WebSocket-Client wird eine Client-Server-Architektur verwendet, wie im folgenden Ablaufdiagramm gezeigt: ![Ablaufdiagramm für eine Clientverbindung.](./media/concept-service-internals/simple-client-sequence.png)


1. Wenn der Client den WebSocket-Handshake startet, versucht der Dienst, den `connect`-Ereignishandler (den Server) für den WebSocket-Handshake aufzurufen. Benutzer können diesen Handler verwenden, um den WebSocket-Handshake zu verarbeiten, das zu verwendende Unterprotokoll zu bestimmen, den Client zu authentifizieren und den Client mit einigen Gruppen zu verknüpfen.
2. Wenn die Verbindung mit dem Client erfolgreich hergestellt wurde, ruft der Dienst einen `connected`-Ereignishandler auf. Dieser wird als eine Art Benachrichtigung eingesetzt und verhindert nicht, dass der Client Nachrichten senden kann. Benutzer können diesen Handler zum Speichern von Daten verwenden und Nachrichten an den Client senden.
2. Wenn der Client Nachrichten sendet, lösen die Dienste das `message`-Ereignis an den Ereignishandler (den Server) aus, um die gesendeten Nachrichten zu verarbeiten. Dabei handelt es sich um ein allgemeines Ereignis, das die in einem WebSocket-Frame gesendeten Nachrichten enthält. Benutzer müssen die Nachrichten innerhalb dieses Ereignishandlers selbst senden.
3. Wenn der Client die Verbindung trennt, versucht der Dienst, das `disconnected`-Ereignis an den Ereignishandler (den Server) auszulösen, sobald er die Trennung erkennt.

Die Ereignisse werden in zwei Kategorien unterteilt:
* Synchrone Ereignisse (blockierend): Synchrone Ereignisse blockieren den Clientworkflow. Wenn bei einem solchen Ereignisauslöser ein Fehler auftritt, trennt der Dienst die Clientverbindung.
    * `connect`
    * `message`
* Asynchrone Ereignisse (nicht blockierend): Durch asynchrone Ereignisse wird der Clientworkflow nicht blockiert. Diese Ereignisse dienen vielmehr als Benachrichtigung an den Upstream-Ereignishandler. Wenn bei einem solchen Ereignisauslöser ein Fehler auftritt, protokolliert der Dienst die Fehlerdetails.
    * `connected`
    * `disconnected`
    
#### <a name="scenarios"></a>Szenarien:
Diese Verbindung kann in einer typischen Client-Server-Architektur verwendet werden, bei der der Client Nachrichten an den Server sendet und der Server eingehende Nachrichten mithilfe von [Ereignishandlern](#event_handler) verarbeitet. Sie kann auch verwendet werden, wenn Kunden vorhandene [Unterprotokolle](https://www.iana.org/assignments/websocket/websocket.xml) in ihrer Anwendungslogik anwenden.

<a name="pubsub_client"></a>

### <a name="the-pubsub-websocket-client"></a>PubSub-WebSocket-Client
Der Dienst unterstützt auch das Unterprotokoll `json.webpubsub.azure.v1`, das es den Clients ermöglicht, Nachrichten direkt zu veröffentlichen/abonnieren, ohne einen Roundtrip zum Upstreamserver durchführen zu müssen. Die WebSocket-Verbindung mit dem `json.webpubsub.azure.v1`-Unterprotokoll wird als PubSub-WebSocket-Client bezeichnet.

Beispielsweise kann in JS ein PubSub-WebSocket-Client mithilfe folgender Komponenten erstellt werden:
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

Ein PubSub-WebSocket-Client kann Folgendes:
* Beitreten zu einer Gruppe, z. B.:
    ```json
    {
        "type": "joinGroup",
        "group": "<group_name>"
    }
    ```
* Verlassen einer Gruppe, z. B.:
    ```json
    {
        "type": "leaveGroup",
        "group": "<group_name>"
    }
    ```
* Veröffentlichen von Nachrichten für eine Gruppe, z. B.:
    ```json
    {
        "type": "sendToGroup",
        "group": "<group_name>",
        "data": { "hello": "world" }
    }
    ```
* Senden von benutzerdefinierten Ereignissen an den Upstreamserver, z. B.:

    ```json
    {
        "type": "event",
        "event": "<event_name>",
        "data": { "hello": "world" }
    }
    ```

Das [PubSub-WebSocket-Unterprotokoll](./reference-json-webpubsub-subprotocol.md) beinhaltet die Details des `json.webpubsub.azure.v1`-Unterprotokolls.

Wie Sie gesehen haben, ist bei einem [einfachen WebSocket-Client](#simple_client) der *Server* eine erforderliche Rolle, um die Ereignisse von Clients zu verarbeiten. Eine einfache WebSocket-Verbindung löst beim Senden von Nachrichten immer ein `message`-Ereignis aus, und die Verarbeitung von Nachrichten und andere Vorgänge werden immer serverseitig ausgeführt. Mithilfe des `json.webpubsub.azure.v1`-Unterprotokolls kann ein autorisierter Client einer Gruppe beitreten und Nachrichten direkt in einer Gruppe veröffentlichen. Außerdem können Nachrichten an verschiedene Upstream-Ereignishandler gesendet werden, indem das *Ereignis* angepasst wird, zu dem die Nachricht gehört. 

#### <a name="scenarios"></a>Szenarien:
Diese Clients können für die Kommunikation zwischen Clients verwendet werden. Nachrichten werden von `client1` an den Dienst gesendet, und der Dienst sendet die Nachricht direkt an `client2` (sofern die Clients dazu autorisiert sind).

Client1:

```js
var client1 = new WebSocket("wss://xxx.webpubsub.azure.com/client/hubs/hub1", "json.webpubsub.azure.v1");
client1.onmessage = e => {
    if (e.data) {
        var message = JSON.parse(e.data);
        if (message.type === "message" 
        && message.group === "Group1"){
            // Only print messages from Group1
            console.log(message.data);
        }
    }
};

client1.onopen = e => {
    client1.send(JSON.stringify({
        type: "joinGroup",
        group: "Group1"
    }));
};
```

Client2:

```js
var client2 = new WebSocket("wss://xxx.webpubsub.azure.com/client/hubs/hub1", "json.webpubsub.azure.v1");
client2.onopen = e => {
    client2.send(JSON.stringify({
        type: "sendToGroup",
        group: "Group1",
        data: "Hello Client1"
    });
};
```

Wie im obigen Beispiel gezeigt, sendet `client2` Daten direkt an `client1`, indem Nachrichten für die Gruppe `Group1` veröffentlicht werden, zu der `client1` gehört.

<a name="client_message_limit"></a>

### <a name="client-message-limit"></a>Maximale Größe für Clientnachrichten
Die maximal zulässige Nachrichtengröße für einen WebSocket-Frame beträgt **1 MB**.

<a name="client_auth"></a>

### <a name="client-auth"></a>Clientauthentifizierung

#### <a name="auth-workflow"></a>Authentifizierungsworkflow

Der Client verwendet ein signiertes JWT-Token, um eine Verbindung mit dem Dienst herzustellen. Der Upstream kann den Client auch ablehnen, wenn es sich um einen `connect`-Ereignishandler des eingehenden Clients handelt. Der Ereignishandler authentifiziert den Client, indem er die `userId`- und `role`-Werte des Clients in der Webhookantwort angibt, oder der Client wird mit dem Fehler 401 abgelehnt. Einzelheiten zu diesem Vorgang finden Sie im Abschnitt [Ereignishandler](#event_handler).

Im folgenden Diagramm wird der Workflow beschrieben:

![Darstellung des Workflows zur Clientauthentifizierung.](./media/concept-service-internals/client-connect-workflow.png)

Wie Sie bei der Beschreibung der PubSub-WebSocket-Clients erfahren haben, kann ein Client nur dann Nachrichten für andere Clients veröffentlichen, wenn er dazu *autorisiert* ist. Die `role`-Werte des Clients bestimmen seine *anfänglichen* Berechtigungen:

| Rolle | Berechtigung |
|---|---|
| Nicht angegeben | Der Client kann Ereignisse senden.
| `webpubsub.joinLeaveGroup` | Der Client kann einer beliebigen Gruppe beitreten/diese verlassen.
| `webpubsub.sendToGroup` | Der Client kann Nachrichten in einer beliebigen Gruppe veröffentlichen.
| `webpubsub.joinLeaveGroup.<group>` | Der Client kann Gruppe `<group>` beitreten/verlassen.
| `webpubsub.sendToGroup.<group>` | Der Client kann Nachrichten in der Gruppe `<group>` veröffentlichen.

Auch serverseitig können über das [Serverprotokoll](#connection_manager) Berechtigungen des Clients dynamisch erteilt oder widerrufen werden, wie in einem späteren Abschnitt gezeigt.

<a name="server_protocol"></a>

## <a name="server-protocol"></a>Serverprotokoll

Das Serverprotokoll bietet die Funktionalität für den Server, um die Clientverbindungen und Gruppen zu verwalten.

Im Allgemeinen enthält das Serverprotokoll zwei Rollen:
1. [Ereignishandler](#event_handler)
2. [Connection manager](#connection_manager)

<a name="event_handler"></a>

### <a name="event-handler"></a>Ereignishandler
Der Ereignishandler verarbeitet die eingehenden Clientereignisse. Ereignishandler werden über das Portal oder die Azure CLI vorab im Dienst registriert und konfiguriert. Sobald ein Clientereignis ausgelöst wird, kann der Dienst erkennen, ob das Ereignis behandelt werden soll oder nicht. Für die öffentliche Vorschauversion verwenden wir den `PUSH`-Modus zum Aufrufen des Ereignishandlers: Der Ereignishandler macht als Serverseite einen öffentlich zugänglichen Endpunkt für den Dienst verfügbar, der aufgerufen werden soll, wenn das Ereignis ausgelöst wird. Er fungiert als **Webhook**. 

Der Dienst übergibt Clientereignisse mithilfe des [CloudEvents-HTTP-Protokolls](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md) an den Upstreamwebhook.

Für jedes Ereignis formuliert er eine HTTP-POST-Anforderung an den registrierten Upstream und erwartet eine HTTP-Antwort. 

Die Daten, die vom Dienst an den Server gesendet werden, weisen immer das CloudEvents-Format `binary` auf.

![Darstellung des Pushmodus für Web PubSub-Dienstereignisse.](./media/concept-service-internals/event-push.png)

#### <a name="upstream-and-validation"></a>Upstream und Überprüfung

Ereignishandler müssen über das Portal oder die Azure CLI vorab im Dienst registriert und konfiguriert werden. Sobald ein Clientereignis ausgelöst wird, kann der Dienst dann erkennen, ob das Ereignis behandelt werden soll oder nicht. Für die öffentliche Vorschauversion verwenden wir den `PUSH`-Modus zum Aufrufen des Ereignishandlers: Der Ereignishandler macht als Serverseite einen öffentlich zugänglichen Endpunkt für den Dienst verfügbar, der aufgerufen werden soll, wenn das Ereignis ausgelöst wird. Er fungiert als **Webhook**-**Upstream**. 

Beim Konfigurieren des Webhookendpunkts kann die URL den Parameter `{event}` verwenden, um eine URL-Vorlage zu definieren. Der Dienst berechnet den Wert der Webhook-URL dynamisch, sobald er die Clientanforderung empfängt. Beispiel: Wenn die Anforderung `/client/hubs/chat` mit dem konfigurierten Ereignishandler-URL-Muster `http://host.com/api/{event}` für den Hub `chat` empfangen wird, wird beim Herstellen der Clientverbindung zunächst eine Nachricht für diese URL veröffentlicht: `http://host.com/api/connect`. Dies kann nützlich sein, wenn ein PubSub-WebSocket-Client benutzerdefinierte Ereignisse sendet und der Ereignishandler unterschiedliche Ereignisse an verschiedene Upstreams sendet. Beachten Sie, dass der `{event}`-Parameter im URL-Domänennamen nicht zulässig ist.

Beim Einrichten des Ereignishandler-Upstreams über das Azure-Portal oder die Azure CLI überprüft der Dienst den Upstreamwebhook mithilfe des [CloudEvents-Schutzes gegen Missbrauch](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Der `WebHook-Request-Origin`-Anforderungsheader ist auf den Dienstdomänennamen `xxx.webpubsub.azure.com` festgelegt, und es wird erwartet, dass der Antwortheader `WebHook-Allowed-Origin` diesen Domänennamen enthält.

Bei der Überprüfung wird der Parameter `{event}` in `validate` aufgelöst. Beim Versuch, die URL auf `http://host.com/api/{event}` festzulegen, versucht der Dienst z. B., einen **OPTIONS**-Vorgang für eine Anforderung an `http://host.com/api/validate` auszuführen. Nur wenn die Antwort gültig ist, kann die Konfiguration erfolgreich festgelegt werden.

Aktuell werden [WebHook-Request-Rate](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#414-webhook-request-rate) und [WebHook-Request-Callback](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#413-webhook-request-callback) nicht unterstützt.

#### <a name="authentication-between-service-and-webhook"></a>Authentifizierung zwischen Dienst und Webhook
- Anonymer Modus
- Einfache Authentifizierung, `code` wird über die konfigurierte Webhook-URL bereitgestellt.
- Azure Active Directory-Authentifizierung 
   - Fügen Sie in den App-Registrierungen von AAD einen geheimen Clientschlüssel hinzu, und stellen Sie den geheimen Clientschlüssel über das Portal oder die CLI für Azure Web PubSub bereit.
   - Bereitstellen der [Identität](../app-service/overview-managed-identity.md?tabs=dotnet) für Azure Web PubSub über das Portal/die CLI

<a name="connection_manager"></a>

### <a name="connection-manager"></a>Ziel-Editor für Dimensionsverarbeitung

Der Server ist naturgemäß ein autorisierter Benutzer. Durch die *Ereignishandlerrolle* kennt der Server die Metadaten der Clients (z. B. `connectionId` und `userId`) und kann mit diesen Informationen folgende Aufgaben ausführen:
   - Schließen einer Clientverbindung
   - Senden von Nachrichten an einen Client
   - Senden von Nachrichten an Clients, die demselben Benutzer gehören
   - Hinzufügen eines Clients zu einer Gruppe
   - Hinzufügen von Clients, die als derselbe Benutzer authentifiziert sind, zu einer Gruppe
   - Entfernen eines Clients aus einer Gruppe
   - Entfernen von Clients, die als derselbe Benutzer authentifiziert sind, aus einer Gruppe
   - Veröffentlichen von Nachrichten in einer Gruppe

Es können auch Berechtigungen zum Veröffentlichen/Beitreten für einen PubSub-Client erteilt oder widerrufen werden:
   - Erteilen von Berechtigungen zum Beitreten/Veröffentlichen für eine bestimmte Gruppe oder für alle Gruppen
   - Widerrufen von Berechtigungen zum Beitreten/Veröffentlichen für eine bestimmte Gruppe oder für alle Gruppen
   - Überprüfen, ob der Client über die Berechtigung zum Beitreten/Veröffentlichen für eine bestimmte Gruppe oder für alle Gruppen verfügt
   
Bei der öffentlichen Vorschau stellt der Dienst REST-APIs zur Verbindungsverwaltung für den Server zur Verfügung:

![Darstellung des Verbindungs-Manager-Workflows des Web PubSub-Diensts.](./media/concept-service-internals/manager-rest.png)

Einzelheiten des REST-API-Protokolls finden Sie [hier][rest].

### <a name="summary"></a>Zusammenfassung
Wie Sie gesehen haben, ist die *Ereignishandlerrolle* für die Kommunikation vom Dienst zum Server und die *Managerrolle* für die Kommunikation vom Server zum Dienst verantwortlich. Werden die beiden Rollen kombiniert, lässt sich der Datenfluss zwischen Dienst und Server über das HTTP-Protokoll in etwa wie folgt abbilden:

![Darstellung des bidirektionalen Workflows beim Web PubSub-Dienst.](./media/concept-service-internals/http-service-server.png)

[rest]: /rest/api/webpubsub/

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]