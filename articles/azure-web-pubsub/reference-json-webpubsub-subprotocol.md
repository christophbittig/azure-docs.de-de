---
title: 'Referenz: Von Azure Web PubSub unterstütztes JSON-WebSocket-Unterprotokoll `json.webpubsub.azure.v1`'
description: Die Referenz beschreibt das Azure Web PubSub unterstützte WebSocket-Unterprotokoll `json.webpubsub.azure.v1`
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/06/2021
ms.openlocfilehash: 2208c41ea49bae4ad3791a7e26f694e4cf4fbbd3
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997854"
---
#  <a name="azure-web-pubsub-supported-json-websocket-subprotocol"></a>Azure Web PubSub unterstütztes JSON-WebSocket-Unterprotokoll
     
In diesem Dokument wird das Unterprotokoll `json.webpubsub.azure.v1` beschrieben.

Wenn der Client dieses Unterprotokoll verwendet, wird erwartet, dass sowohl der ausgehende als auch der eingehende Datenframe **JSON**-Nutzlasten sind.

## <a name="overview"></a>Übersicht

Unterprotokoll `json.webpubsub.azure.v1` ermöglicht es den Clients, direkt Veröffentlichungen/Abonnements anstatt eines Roundtrips zum Upstreamserver durchzuführen. Die WebSocket-Verbindung mit dem `json.webpubsub.azure.v1`-Unterprotokoll wird auch „PubSub WebSocket-Client“ genannt.

In JS kann ein PubSub WebSocket-Client z. B. mit folgendem Code erstellt werden:
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```
Für einen einfachen WebSocket-Client muss der *Server* über eine MUST HAVE-Rolle verfügen, um die Ereignisse von Clients zu verarbeiten. Eine einfache WebSocket-Verbindung löst beim Versenden von Nachrichten immer ein `message` Ereignis aus und verlässt sich immer auf den Server, um Nachrichten zu verarbeiten und andere Vorgänge durchzuführen. Mithilfe des `json.webpubsub.azure.v1` Unterprotokolls kann ein autorisierter Client einer Gruppe mithilfe von [Joinanforderungen](#join-groups) beitreten und Nachrichten mithilfe von [Veröffentlichungsanforderungen](#publish-messages) direkt in einer Gruppe veröffentlichen. Er kann Nachrichten auch an verschiedene Upstreamkomponenten (Ereignishandler) weiterleiten, indem das *Ereignis*, zu dem die Nachricht gehört, mithilfe von [Ereignisanforderungen](#send-custom-events) angepasst wird.

## <a name="permissions"></a>Berechtigungen

Möglicherweise haben Sie bemerkt, dass - wenn wir über PubSub-WebSocket-Clients reden - ein Client nur dann auf anderen Clients veröffentlichen kann, wenn er dazu *autorisiert* ist. Die Rollen (`role`) des Clients bestimmen die *anfänglichen* Berechtigungen, über die der Client verfügt:

| Rolle | Berechtigung |
|---|---|
| Nicht angegeben | Der Client kann Ereignisanforderungen senden.
| `webpubsub.joinLeaveGroup` | Der Client kann einer beliebigen Gruppe beitreten/diese verlassen.
| `webpubsub.sendToGroup` | Der Client kann Nachrichten in einer beliebigen Gruppe veröffentlichen.
| `webpubsub.joinLeaveGroup.<group>` | Der Client kann Gruppe `<group>` beitreten/verlassen.
| `webpubsub.sendToGroup.<group>` | Der Client kann Nachrichten in Gruppe `<group>` veröffentlichen.

Serverseitig können Berechtigungen des Clients auch dynamisch über REST-APIs oder Server-SDKs erteilt oder widerrufen werden.

## <a name="requests"></a>Requests

### <a name="join-groups"></a>Gruppen beitreten

Format:

```json
{
    "type": "joinGroup",
    "group": "<group_name>",
    "ackId" : 1
}
```

* `ackId` ist die Identität jeder Anforderung und sollte eindeutig sein. Der Dienst sendet eine [ack-Antwortnachricht](#ack-response), um den Prozess über das Ergebnis der Anforderung zu informieren. Weitere Informationen finden Sie unter [AckId- und Ack-Antwort](./concept-client-protocols.md#ackid-and-ack-response).

### <a name="leave-groups"></a>Gruppen verlassen

Format:

```json
{
    "type": "leaveGroup",
    "group": "<group_name>",
    "ackId" : 1
}
```

* `ackId` ist die Identität jeder Anforderung und sollte eindeutig sein. Der Dienst sendet eine [ack-Antwortnachricht](#ack-response), um den Prozess über das Ergebnis der Anforderung zu informieren. Weitere Informationen finden Sie unter [AckId- und Ack-Antwort](./concept-client-protocols.md#ackid-and-ack-response).

### <a name="publish-messages"></a>Veröffentlichen von Nachrichten

Format:

```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "ackId" : 1,
    "noEcho": true|false,
    "dataType" : "json|text|binary",
    "data": {}, // data can be string or valid json token depending on the dataType 
}
```

* `ackId` ist die Identität jeder Anforderung und sollte eindeutig sein. Der Dienst sendet eine [ack-Antwortnachricht](#ack-response), um den Prozess über das Ergebnis der Anforderung zu informieren. Weitere Informationen finden Sie unter [AckId- und Ack-Antwort](./concept-client-protocols.md#ackid-and-ack-response).
* `noEcho` ist optional. Wenn diese Meldung auf TRUE festgelegt ist, wird sie nicht an dieselbe Verbindung zurückgesendet. Wenn keine Festlegung erfolgt, ist der Standardwert FALSE.
* `dataType` kann `json`, `text` oder `binary` sein.
     * `json`: `data` kann ein beliebiger Typ sein, der von JSON unterstützt und in seinem jeweiligen Zustand veröffentlicht wird. Wenn `dataType` nicht angegeben ist, wird dieser Wert standardmäßig auf `json` festgelegt.
     * `text`: `data` sollte im Zeichenfolgenformat vorliegen, und die Zeichenfolgendaten werden veröffentlicht.
     * `binary`: `data` sollte im Base64-Format vorliegen, und die Binärdaten werden veröffentlicht.

#### <a name="case-1-publish-text-data"></a>Fall 1: Veröffentlichen von Textdaten:
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "text",
    "data": "text data",
    "ackId": 1
}
```

* Was der Unterprotokoll-Client in dieser Gruppe `<group_name>` empfängt:
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "text",
    "data" : "text data"
}
```
* Was der unformatierte Client in dieser Gruppe `<group_name>` empfängt, sind Zeichenfolgendaten `text data`.

#### <a name="case-2-publish-json-data"></a>Fall 2: Veröffentlichen von JSON-Daten:
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "json",
    "data": {
        "hello": "world"
    }
}
```

* Was der Unterprotokoll-Client in dieser Gruppe `<group_name>` empfängt:
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "json",
    "data" : {
        "hello": "world"
    }
}
```
* Was der unformatierte Client in dieser Gruppe `<group_name>` empfängt, sind serialisierte Zeichenfolgendaten `{"hello": "world"}`.


#### <a name="case-3-publish-binary-data"></a>Fall 3: Veröffentlichen von Binärdaten:
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "binary",
    "data": "<base64_binary>",
    "ackId": 1
}
```

* Was der Unterprotokoll-Client in dieser Gruppe `<group_name>` empfängt:
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "binary",
    "data" : "<base64_binary>", 
}
```
* Was der unformatierte Client in dieser Gruppe `<group_name>` empfängt sind **binäre** Daten im Binär-Frame.

### <a name="send-custom-events"></a>Versenden von benutzerdefinierten Ereignissen

Format:

```json
{
    "type": "event",
    "event": "<event_name>",
    "ackId": 1,
    "dataType" : "json|text|binary",
    "data": {}, // data can be string or valid json token depending on the dataType 
}
```

* `ackId` ist die Identität jeder Anforderung und sollte eindeutig sein. Der Dienst sendet eine [ack-Antwortnachricht](#ack-response), um den Prozess über das Ergebnis der Anforderung zu informieren. Weitere Informationen finden Sie unter [AckId- und Ack-Antwort](./concept-client-protocols.md#ackid-and-ack-response).

`dataType` kann `text`, `binary` oder `json` sein:
* `json`: Die Daten können ein beliebiger Typ sein, der von JSON unterstützt und in seinem jeweiligen Zustand veröffentlicht wird. Wenn `dataType` nicht angegeben ist, wird dieser Wert standardmäßig auf `json` festgelegt.
* `text`: Die Daten sollten im Zeichenfolgenformat vorliegen, und die Zeichenfolgendaten werden veröffentlicht.
* `binary`: Die Daten sollten im Base64-Format vorliegen, und die Binärdaten werden veröffentlicht.

#### <a name="case-1-send-event-with-text-data"></a>Fall 1: Ereignis mit Textdaten versenden:
```json
{
    "type": "event",
    "event": "<event_name>",
    "ackId": 1,
    "dataType" : "text",
    "data": "text data", 
}
```

Was der Upstream-Ereignishandler empfängt (siehe unten). Der `Content-Type` der CloudEvents-HTTP-Anforderung ist `text/plain` für `dataType`=`text`.

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: text/plain
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

text data

```

#### <a name="case-2-send-event-with-json-data"></a>Fall 2: Ereignis mit JSON-Daten versenden:
```json
{
    "type": "event",
    "event": "<event_name>",
    "ackId": 1,
    "dataType" : "json",
    "data": {
        "hello": "world"
    }, 
}
```

Was der Upstream-Ereignishandler empfängt (siehe unten); der `Content-Type` der CloudEvents-HTTP-Anforderung ist `application/json` für `dataType`=`json`.

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

{
    "hello": "world"
}

```

#### <a name="case-3-send-event-with-binary-data"></a>Fall 3: Ereignis mit Binärdaten versenden:
```json
{
    "type": "event",
    "event": "<event_name>",
    "ackId": 1,
    "dataType" : "binary",
    "data": "base64_binary", 
}
```

Was der Upstream-Ereignishandler empfängt (siehe unten). Der `Content-Type` der CloudEvents-HTTP-Anforderung ist `application/octet-stream` für `dataType`=`binary`.

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/octet-stream
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

binary

```

Der WebSocket-Frame kann entweder das `text`-Format für Textnachricht-Frames aufweisen oder UTF8-codierte Binärdateien für `binary`-Nachrichtframes enthalten.

Der Dienst lehnt den Client ab, wenn die Nachricht nicht dem beschriebenen Format entspricht.

## <a name="responses"></a>Antworten

Vom Client empfangene Nachrichten können verschiedene Typen aufweisen: `ack`, `message` und `system`: 

### <a name="ack-response"></a>Ack-Antwort

Wenn die Anforderung `ackId` enthält, gibt der Dienst eine Ack-Antwort für diese Anforderung zurück. Die Clientimplementierung sollte diesen Ack-Mmechanismus verarbeiten. Dazu gehört das Warten auf die Ack-Antwort für einen `async` `await` Vorgang und das Ausführen einer Timeoutüberprüfung, wenn die Ack-Antwort während eines bestimmten Zeitraums nicht empfangen wird.

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

Die Clientimplementierung SOLLTE immer zuerst überprüfen, ob `success` `true` oder `false` ist. Nur wenn `success` `false` ist, liest der Client aus `error`.

### <a name="message-response"></a>Reaktion auf die Nachricht

Clients können Nachrichten entweder von einer Gruppe empfangen, in die der Client eingebunden ist, oder von der Serververwaltungsrolle, über die der Server Nachrichten an einen bestimmten Client oder einen bestimmten Benutzer sendet.

1. Wenn die Nachricht aus einer Gruppe stammt

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "<group_name>",
        "dataType": "json|text|binary",
        "data" : {} // The data format is based on the dataType
        "fromUserId": "abc"
    }
    ```

1. Wenn die Nachricht vom Server stammt.

    ```json
    {
        "type": "message",
        "from": "server",
        "dataType": "json|text|binary",
        "data" : {} // The data format is based on the dataType
    }
    ```

#### <a name="case-1-sending-data-hello-world-to-the-connection-through-rest-api-with-content-typetextplain"></a>Fall 1: Senden der Daten `Hello World` an die Verbindung über die REST-API mit `Content-Type`=`text/plain` 
* Ein einfacher WebSocket-Client empfängt einen WebSocket-Textframe mit Daten: `Hello World`;
* Ein PubSub-WebSocket-Client empfängt Folgendes:
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "text",
        "data": "Hello World", 
    }
    ```

#### <a name="case-2-sending-data--hello--world-to-the-connection-through-rest-api-with-content-typeapplicationjson"></a>Fall 2: Senden der Daten `{ "Hello" : "World"}` an die Verbindung über die REST-API mit `Content-Type`=`application/json`
* Ein einfacher WebSocket-Client empfängt einen WebSocket-Textframe mit Daten im Zeichenfolgenformat: `{ "Hello" : "World"}`;
* Ein PubSub-WebSocket-Client empfängt Folgendes:
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "json",
        "data": {
            "Hello": "World"
        }
    }
    ```

Wenn die REST-API eine Zeichenfolge `Hello World` mit dem Inhaltstyp `application/json` sendet, empfängt der einfache WebSocket-Client eine JSON-Zeichenfolge, d. h. `"Hello World"`, die die Zeichenfolge mit `"` umschließt.

#### <a name="case-3-sending-binary-data-to-the-connection-through-rest-api-with-content-typeapplicationoctet-stream"></a>Fall 3: Senden der von Binärdaten an die Verbindung über die REST-API mit `Content-Type`=`application/octet-stream`
* Ein einfacher WebSocket-Client empfängt einen binären WebSocket-Frame mit den Binärdaten.
* Ein PubSub-WebSocket-Client empfängt Folgendes:
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "binary",
        "data": "<base64_binary>"
    }
    ```

### <a name="system-response"></a>Systemantwort

Der Web PubSub-Dienst kann auch systembezogene Antworten an den Client senden. 

#### <a name="connected"></a>Verbunden

Wenn die Verbindung mit dem Dienst verbunden ist.

```json
{
    "type": "system",
    "event": "connected",
    "userId": "user1",
    "connectionId": "abcdefghijklmnop",
}
```

#### <a name="disconnected"></a>Getrennt

Wenn der Server die Verbindung schließt oder wenn der Dienst den Client ablehnt.

```json
{
    "type": "system",
    "event": "disconnected",
    "message": "reason"
}
```

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]
