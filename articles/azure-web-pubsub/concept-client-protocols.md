---
title: WebSocket-Clientprotokolle für Azure Web PubSub
description: Dieser Artikel bietet eine Übersicht über Clientprotokolle für Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 58fef1f2ada19af2ff81ef27d5ff5ab2ad4cebf7
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997947"
---
#  <a name="websocket-client-protocols-for-azure-web-pubsub"></a>WebSocket-Clientprotokolle für Azure Web PubSub

Clients stellen mithilfe des [WebSocket](https://datatracker.ietf.org/doc/html/rfc6455)-Standardprotokolls eine Verbindung mit Azure Web PubSub her.

## <a name="service-endpoints"></a>Dienstendpunkte
Der Web PubSub-Dienst stellt zwei Arten von Endpunkten bereit, mit dem Clients eine Verbindung herstellen können:
* `/client/hubs/{hub}`
* `/client/?hub={hub}`

`{hub}` ist ein obligatorischer Parameter, der als Isolation für verschiedene Anwendungen fungiert. Sie können ihn entweder im Pfad oder in der Abfrage festlegen.

## <a name="authorization"></a>Autorisierung

Clients stellen eine Verbindung mit dem Dienst über ein JSON Web Token (JWT) her. Das Token kann entweder in der Abfragezeichenfolge als `/client/?hub={hub}&access_token={token}` oder im `Authorization`-Header als `Authorization: Bearer {token}` enthalten sein.

Hier folgt ein allgemeiner Autorisierungsworkflow:

1. Der Client führt einen Aushandlungsvorgang mit Ihrem Anwendungsserver aus. Der Anwendungsserver enthält die Middleware für die Autorisierung, die die Anforderungen des Clients bearbeitet und ein JWT signiert, damit der Client eine Verbindung mit dem Dienst herstellen kann.
1. Der Anwendungsserver gibt das JWT und die Dienst-URL an den Client zurück.
1. Der Client versucht, eine Verbindung mit dem Web PubSub-Dienst herzustellen, indem er die URL und das JWT verwendet, das vom Anwendungsserver zurückgegeben wird.

<a name="simple_client"></a>

## <a name="the-simple-websocket-client"></a>Der einfache WebSocket-Client

Ein **einfacher WebSocket-Client** ist, wie der Name schon sagt, eine einfache WebSocket-Verbindung. Er kann zudem über ein eigenes benutzerdefiniertes Unterprotokoll verfügen. 

In JavaScript können Sie z. B. mithilfe des folgenden Codes einen einfachen WebSocket-Client erstellen:

```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')
```

## <a name="the-pubsub-websocket-client"></a>PubSub-WebSocket-Client

Ein **PubSub-WebSocket-Client** ist der WebSocket-Client, der vom Azure Web PubSub-Dienst definierte Unterprotokolle verwendet:

* `json.webpubsub.azure.v1`
* `protobuf.webpubsub.azure.v1`

Mit dem vom Dienst unterstützten Unterprotokoll können die **PubSub-WebSocket-Clients** Nachrichten direkt in Gruppen veröffentlichen, wenn sie über die [Berechtigungen](#permissions) verfügen.

### <a name="the-jsonwebpubsubazurev1-subprotocol"></a>`json.webpubsub.azure.v1`-Unterprotokoll

[Hier finden Sie ausführliche Informationen zum JSON-Unterprotokoll](reference-json-webpubsub-subprotocol.md).

#### <a name="create-a-pubsub-websocket-client"></a>Erstellen eines PubSub-WebSocket-Clients

```js
var pubsubClient = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

#### <a name="join-a-group-from-the-client-directly"></a>Direktes Beitreten zu einer Gruppe über den Client

```js
let ackId = 0;
pubsubClient.send(    
    JSON.stringify({
        type: 'joinGroup',
        group: 'group1',
        ackId: ++ackId
    }));
```

#### <a name="send-messages-to-a-group-from-the-client-directly"></a>Direktes Senden von Nachrichten vom Client an eine Gruppe

```js
let ackId = 0;
pubsubClient.send(    
    JSON.stringify({
        type: 'sendToGroup',
        group: 'group1',
        ackId: ++ackId,
        dataType: "json",
        data: {
            "hello": "world"
        }
    }));
```

### <a name="the-protobufwebpubsubazurev1-subprotocol"></a>`protobuf.webpubsub.azure.v1`-Unterprotokoll

Protokollpuffer (protobuf) ist ein sprachneutrales, plattformneutrales, auf Binärdaten basierendes Protokoll, das das Senden von Binärdaten vereinfacht. Protobuf stellt Tools zum Generieren von Clients für viele Sprachen wie z. B. Java, Python, Objective-C, C# und C++ zur Verfügung. [Weitere Informationen zu Protobuf](https://developers.google.com/protocol-buffers).

In JavaScript können Sie beispielsweise einen **PubSub-WebSocket-Client** mit einem protobuf-Unterprotokoll erstellen, indem Sie den folgenden Code verwenden:

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

[Hier finden Sie ausführliche Informationen zum protobuf-Unterprotokoll](reference-protobuf-webpubsub-subprotocol.md).


### <a name="ackid-and-ack-response"></a>AckId- und Ack-Antwort

Der **PubSub-WebSocket-Client** unterstützt die `ackId`-Eigenschaft für `joinGroup`-, `leaveGroup`-, `sendToGroup`- und `event`-Nachrichten. Wenn Sie `ackId` verwenden, können Sie eine ack-Antwortnachricht erhalten, wenn Ihre Anforderung verarbeitet wird. Sie können `ackId` in Fire-and-Forget-Szenarien (Auslösen und Vergessen) auslassen. In diesem Artikel beschreiben wir die Unterschiede im Verhalten, wenn `ackId` angegeben oder ausgelassen wurde.

#### <a name="behavior-when-no-ackid-specified"></a>Verhalten, wenn `ackId` nicht angegeben ist.

Wenn `ackId` nicht angegeben ist, wird Fire-and-Forget (Auslösen und Vergessen) verwendet. Auch wenn beim Übermitteln von Nachrichten Fehler auftreten, haben Sie keine Möglichkeit, benachrichtigt zu werden.

#### <a name="behavior-when-ackid-specified"></a>Verhalten, wenn `ackId` angegeben ist.

##### <a name="idempotent-publish"></a>Idempotente Veröffentlichung

`ackId` ist eine uint64-Zahl und sollte innerhalb eines Clients mit der gleichen Verbindungs-ID eindeutig sein. Der Web-PubSub-Dienst zeichnet die `ackId` auf und Nachrichten mit der gleichen `ackId` werden als identische Nachricht behandelt. Der Dienst lehnt es ab, dieselbe Nachricht mehr als einmal zu übermitteln, was bei Wiederholungsversuchen nützlich ist, um doppelte Nachrichten zu vermeiden. Wenn ein Client z. B. eine Nachricht mit `ackId=5` sendet und keine ack-Antwort mit `ackId=5` erhält, versucht er es erneut und sendet dieselbe Nachricht noch einmal. In einigen Fällen, in denen die Nachricht bereits übermittelt wurde und die ack-Antwort aus irgendeinem Grund verloren gegangen ist, lehnt der Dienst den erneuten Versuch ab und gibt eine ack-Antwort mit `Duplicate`-Ursache zurück.

#### <a name="ack-response"></a>Ack-Antwort

Der Web-PubSub-Dienst sendet eine ack-Antwort für jede Anforderung mit `ackId`.

Format:
```json
{
    "type": "ack",
    "ackId": 1, // The ack id for the request to ack
    "success": false, // true or false
    "error": {
        "name": "Forbidden|InternalServerError|Duplicate",
        "message": "<error_detail>"
    }
}
```

* Die `ackId` ordnet die Anforderung zu.

* `success` ist ein boolescher Wert und gibt an, ob die Anforderung vom Dienst erfolgreich verarbeitet wurde. Wenn er `false` ist, müssen Clients den `error` überprüfen.

* `error` ist nur vorhanden, wenn `success` entsprechend `false` ist und Clients unterschiedliche Logik für verschiedene `name`-Instanzen aufweisen sollten. Sie sollten davon ausgehen, dass es in Zukunft möglicherweise mehr `name`-Typen gibt.
    - `Forbidden`: Der Client verfügt nicht über die Berechtigung für die Anforderung. Dem Client müssen relevante Rollen hinzugefügt werden.
    - `InternalServerError`: Im Dienst ist ein interner Fehler aufgetreten. Eine Wiederholung ist erforderlich.
    - `Duplicate`: Die Nachricht mit derselben `ackId` wurde bereits vom Dienst verarbeitet.

### <a name="permissions"></a>Berechtigungen

Wie Sie wahrscheinlich in der früheren Beschreibung des PubSub WebSocket-Clients gesehen haben, kann ein Client nur dann für andere Clients veröffentlichen, wenn er dazu *autorisiert* ist. Die Berechtigungen eines Clients können beim Herstellen der Verbindung oder für die Dauer der Verbindung erteilt werden.

| Rolle | Berechtigung |
|---|---|
| Nicht angegeben | Der Client kann Ereignisanforderungen senden. |
| `webpubsub.joinLeaveGroup` | Der Client kann einer beliebigen Gruppe beitreten oder diese verlassen. |
| `webpubsub.sendToGroup` | Der Client kann Nachrichten in einer beliebigen Gruppe veröffentlichen. |
| `webpubsub.joinLeaveGroup.<group>` | Der Client kann der Gruppe `<group>` beitreten oder diese verlassen. |
| `webpubsub.sendToGroup.<group>` | Der Client kann Nachrichten in Gruppe `<group>` veröffentlichen. |
| | |

Die Berechtigung eines Clients kann auf verschiedene Weise erteilt werden:

#### <a name="1-assign-the-role-to-the-client-when-generating-the-access-token"></a>1. Zuweisen der Rolle zum Client beim Generieren des Zugriffstokens

Der Client kann mithilfe eines JWT-Tokens eine Verbindung mit dem Dienst herstellen. Die Tokennutzdaten können Informationen wie die `role` des Clients enthalten. Wenn Sie das JWT-Token beim Client signieren, können Sie dem Client Berechtigungen erteilen, indem Sie dem Client bestimmte Rollen zuweisen.

Signieren Sie z. B. ein JWT-Token, das über die Berechtigung zum Senden von Nachrichten an `group1` und `group2` verfügt: 

```js
let token = await serviceClient.getClientAccessToken({
    roles: [ "webpubsub.sendToGroup.group1", "webpubsub.sendToGroup.group2" ]
});
```

#### <a name="2-assign-the-role-to-the-client-with-the-connect-event-handler"></a>2. Zuweisen der Rolle zum Client mit dem `connect`-Ereignishandler

Die Rollen der Clients können auch festgelegt werden, wenn der `connect`-Ereignishandler registriert wird, und der Upstreamereignishandler kann die `roles` des Clients an den Web-PubSub-Dienst zurückgeben, wenn die `connect`-Ereignisse behandelt werden.

In JavaScript können Sie z. B. das `handleConnect`-Ereignis dafür konfigurieren:

```js
let handler = new WebPubSubEventHandler("hub1", {
  handleConnect: (req, res) => {
    // auth the connection and set the userId of the connection
    res.success({
      roles: [ "webpubsub.sendToGroup.group1", "webpubsub.sendToGroup.group2" ]
    });
  },
});
```

#### <a name="3-assign-the-role-to-the-client-through-rest-apis-or-server-sdks-during-runtime"></a>3. Zuweisen der Rolle zum Client über REST-APIs oder Server-SDKs während der Laufzeit

```js
let service = new WebPubSubServiceClient("<your_connection_string>", "test-hub");
await service.grantPermission("<connection_id>", "joinLeaveGroup", { targetName: "group1" });
```

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]
