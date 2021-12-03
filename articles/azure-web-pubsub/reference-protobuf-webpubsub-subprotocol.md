---
title: 'Referenz: Von Azure Web PubSub unterstütztes protobuf-WebSocket-Unterprotokoll `protobuf.webpubsub.azure.v1`'
description: In dieser Referenz wird das von Azure Web PubSub unterstützte WebSocket-Unterprotokoll `protobuf.webpubsub.azure.v1` beschrieben.
author: chenyl
ms.author: chenyl
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 8e801391e5ac2ebe2a4dee276f525885ff84f449
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997797"
---
#  <a name="the-azure-web-pubsub-supported-protobuf-websocket-subprotocol"></a>Von Azure Web PubSub unterstütztes protobuf-WebSocket-Unterprotokoll
     
In diesem Dokument wird das Unterprotokoll `protobuf.webpubsub.azure.v1` beschrieben.

Wenn ein Client dieses Unterprotokoll verwendet, wird erwartet, dass es sich sowohl bei den ausgehenden als auch den eingehenden Dataframes um Protokollpuffernutzlasten (protobuf) handelt.

## <a name="overview"></a>Übersicht

Das Unterprotokoll `protobuf.webpubsub.azure.v1` ermöglicht es den Clients, direkt Veröffentlichen-Abonnieren-Vorgänge (Publish-Subscribe, PubSub) anstelle eines Roundtrips zum Upstreamserver durchzuführen. Die WebSocket-Verbindung mit dem Unterprotokoll `protobuf.webpubsub.azure.v1` wird auch „PubSub-WebSocket-Client“ genannt.

In JavaScript können Sie beispielsweise einen PubSub-WebSocket-Client mit dem protobuf-Unterprotokoll erstellen, indem Sie Folgendes verwenden:

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

Für einen einfachen WebSocket-Client verfügt der Server über die *erforderliche* Rolle für die Behandlung von Clientereignissen. Eine einfache WebSocket-Verbindung löst beim Senden von Nachrichten immer ein `message`-Ereignis aus, und die Verarbeitung von Nachrichten und andere Vorgänge werden immer serverseitig ausgeführt. Mithilfe des Unterprotokolls `protobuf.webpubsub.azure.v1` kann ein autorisierter Client unter Verwendung von [Joinanforderungen](#join-groups) einer Gruppe beitreten und Nachrichten mit [Veröffentlichungsanforderungen](#publish-messages) direkt in einer Gruppe veröffentlichen. Der Client kann mithilfe von [Ereignisanforderungen](#send-custom-events) Nachrichten auch an verschiedene Upstreamereignishandler weiterleiten, um das *Ereignis* anzupassen, zu dem die Nachricht gehört.

> [!NOTE]
> Derzeit unterstützt der Web PubSub-Dienst nur [proto3](https://developers.google.com/protocol-buffers/docs/proto3).

## <a name="permissions"></a>Berechtigungen

In der früheren Beschreibung des PubSub-WebSocket-Clients haben Sie möglicherweise bemerkt, dass ein Client nur dann für andere Clients veröffentlichen kann, wenn er dazu *autorisiert* ist. Die Rollen des Clients bestimmen seine *anfänglichen* Berechtigungen, wie in der folgenden Tabelle aufgeführt:

| Rolle | Berechtigung |
|---|---|
| Nicht angegeben | Der Client kann Ereignisanforderungen senden. |
| `webpubsub.joinLeaveGroup` | Der Client kann einer beliebigen Gruppe beitreten oder diese verlassen. |
| `webpubsub.sendToGroup` | Der Client kann Nachrichten in einer beliebigen Gruppe veröffentlichen. |
| `webpubsub.joinLeaveGroup.<group>` | Der Client kann der Gruppe `<group>` beitreten oder diese verlassen. |
| `webpubsub.sendToGroup.<group>` | Der Client kann Nachrichten in Gruppe `<group>` veröffentlichen. |
| | |

Serverseitig können Berechtigungen des Clients auch dynamisch über REST-APIs oder Server-SDKs erteilt oder widerrufen werden.

## <a name="requests"></a>Requests

Alle Anforderungsnachrichten entsprechen dem folgenden protobuf-Format:

```protobuf
syntax = "proto3";

import "google/protobuf/any.proto";

message UpstreamMessage {
    oneof message {
        SendToGroupMessage send_to_group_message = 1;
        EventMessage event_message = 5;
        JoinGroupMessage join_group_message = 6;
        LeaveGroupMessage leave_group_message = 7;
    }

    message SendToGroupMessage {
        string group = 1;
        optional uint64 ack_id = 2;
        MessageData data = 3;
    }

    message EventMessage {
        string event = 1;
        MessageData data = 2;
        optional uint64 ack_id = 3;
    }
    
    message JoinGroupMessage {
        string group = 1;
        optional uint64 ack_id = 2;
    }

    message LeaveGroupMessage {
        string group = 1;
        optional uint64 ack_id = 2;
    }
}

message MessageData {
    oneof data {
        string text_data = 1;
        bytes binary_data = 2;
        google.protobuf.Any protobuf_data = 3;
    }
}
```

### <a name="join-groups"></a>Gruppen beitreten

Format:

Legen Sie `join_group_message.group` auf den Gruppennamen fest.

* `ackId` ist die Identität jeder Anforderung und sollte eindeutig sein. Der Dienst sendet eine [ack-Antwortnachricht](#ack-response), um den Prozess über das Ergebnis der Anforderung zu informieren. Weitere Informationen finden Sie unter [AckId- und Ack-Antwort](./concept-client-protocols.md#ackid-and-ack-response).

### <a name="leave-groups"></a>Gruppen verlassen

Format:

Legen Sie `leave_group_message.group` auf den Gruppennamen fest.

* `ackId` ist die Identität jeder Anforderung und sollte eindeutig sein. Der Dienst sendet eine [ack-Antwortnachricht](#ack-response), um den Prozess über das Ergebnis der Anforderung zu informieren. Weitere Informationen finden Sie unter [AckId- und Ack-Antwort](./concept-client-protocols.md#ackid-and-ack-response).

### <a name="publish-messages"></a>Veröffentlichen von Nachrichten

Format:

* `ackId` ist die Identität jeder Anforderung und sollte eindeutig sein. Der Dienst sendet eine [ack-Antwortnachricht](#ack-response), um den Prozess über das Ergebnis der Anforderung zu informieren. Weitere Informationen finden Sie unter [AckId- und Ack-Antwort](./concept-client-protocols.md#ackid-and-ack-response).

Es gibt einen impliziten `dataType`, der in Abhängigkeit von den von Ihnen in `MessageData` festgelegten `data` entweder `protobuf`, `text` oder `binary` sein kann. Die Empfängerclients können den `dataType` nutzen, um den Inhalt ordnungsgemäß zu verarbeiten.

* `protobuf`: Wenn Sie `send_to_group_message.data.protobuf_data` festlegen, entspricht der implizite `dataType` `protobuf`. `protobuf_data` kann dem Nachrichtentyp [Beliebig](https://developers.google.com/protocol-buffers/docs/proto3#any) entsprechen. Alle anderen Clients erhalten protobuf-codierte Binärdateien, die vom protobuf-SDK deserialisiert werden können. Clients, die nur textbasierten Inhalt unterstützen (z. B. `json.webpubsub.azure.v1` ), erhalten eine Base64-codierte Binärdatei.

* `text`: Wenn Sie `send_to_group_message.data.text_data` festlegen, entspricht der implizite `dataType` `text`. `text_data` sollte eine Zeichenfolge sein. Alle Clients mit anderen Protokollen erhalten eine UTF-8-codierte Zeichenfolge.

* `binary`: Wenn Sie `send_to_group_message.data.binary_data` festlegen, entspricht der implizite `dataType` `binary`. `binary_data` sollte ein Bytearray sein. Alle Clients mit anderen Protokollen erhalten eine unformatierte Binärdatei ohne protobuf-Codierung. Clients, die nur textbasierten Inhalt unterstützen (z. B. `json.webpubsub.azure.v1` ), erhalten eine Base64-codierte Binärdatei.

#### <a name="case-1-publish-text-data"></a>Fall 1: Veröffentlichen von Textdaten

Legen Sie `send_to_group_message.group` auf `group` und `send_to_group_message.data.text_data` auf `"text data"` fest.

* Der protobuf-Unterprotokollclient in der Gruppe `group` empfängt den Binärframe und kann die [DownstreamMessage](#responses) zum Deserialisieren verwenden.

* Der JSON-Unterprotokollclient in der Gruppe `group` empfängt Folgendes:

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "group",
        "dataType" : "text",
        "data" : "text data"
    }
    ```

* Der unformatierte Client in der Gruppe `group` empfängt die Zeichenfolge `text data`.

#### <a name="case-2-publish-protobuf-data"></a>Fall 2: Veröffentlichen von protobuf-Daten

Angenommen, Sie haben eine Kundennachricht empfangen:

```
message MyMessage {
    int32 value = 1;
}
```

Legen Sie `send_to_group_message.group` auf `group` und `send_to_group_message.data.protobuf_data` auf `Any.pack(MyMessage)` mit `value = 1` fest.

* Der protobuf-Unterprotokollclient in der Gruppe `group` empfängt den Binärframe und kann die [DownstreamMessage](#responses) zum Deserialisieren verwenden.

* Der Unterprotokollclient in der Gruppe `group` empfängt Folgendes:

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "G",
        "dataType" : "protobuf",
        "data" : "Ci90eXBlLmdvb2dsZWFwaXMuY29tL2F6dXJlLndlYnB1YnN1Yi5UZXN0TWVzc2FnZRICCAE=" // Base64-encoded bytes
    }
    ```

    > [!NOTE]
    > Bei den Daten handelt es sich um eine Base64-codierte, deserialisierbare protobuf-Binärdatei. 

Sie können die folgende Protobuf-Definition verwenden und sie mit `Any.unpack()` deserialisieren:

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

* Der unformatierte Client in der Gruppe `group` empfängt den Binärframe:

    ```
    # Show in hexadecimal
    0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
    ```

#### <a name="case-3-publish-binary-data"></a>Fall 3: Veröffentlichen von Binärdaten

Legen Sie `send_to_group_message.group` auf `group` und `send_to_group_message.data.binary_data` auf `[1, 2, 3]` fest.

* Der protobuf-Unterprotokollclient in der Gruppe `group` empfängt den Binärframe und kann die [DownstreamMessage](#responses) zum Deserialisieren verwenden.

* Der JSON-Unterprotokollclient in der Gruppe `group` empfängt Folgendes:

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "group",
        "dataType" : "binary",
        "data" : "AQID", // Base64-encoded [1,2,3]
    }
    ```

    Da der JSON-Unterprotokollclient nur textbasiertes Messaging unterstützt, ist die Binärdatei immer Base64-codiert.

* Der unformatierte Client in der Gruppe `group` empfängt die Binärdaten im Binärframe:

    ```
    # Show in hexadecimal
    01 02 03
    ```

### <a name="send-custom-events"></a>Versenden von benutzerdefinierten Ereignissen

Es gibt einen impliziten `dataType`, der in Abhängigkeit von dem von Ihnen festgelegten `dataType` entweder `protobuf`, `text` oder `binary` sein kann. Die Empfängerclients können den `dataType` nutzen, um den Inhalt ordnungsgemäß zu verarbeiten.

* `protobuf`: Wenn Sie `event_message.data.protobuf_data` festlegen, entspricht der implizite `dataType` `protobuf`. `protobuf_data` kann ein beliebiger unterstützter Protobuf-Typ sein. Der Ereignishandler empfängt die protobuf-codierten Binärdateien, die von jedem protobuf-SDK deserialisiert werden können.

* `text`: Wenn Sie `event_message.data.text_data` festlegen, bedeutet dies implizit `text`. `text_data` sollte eine Zeichenfolge sein. Der Ereignishandler empfängt eine UTF-8-codierte Zeichenfolge.

* `binary`: Wenn Sie `event_message.data.binary_data` festlegen, bedeutet dies implizit `binary`. `binary_data` sollte ein Bytearray sein. Der Ereignishandler empfängt den unformatierten Binärframe.

#### <a name="case-1-send-an-event-with-text-data"></a>Fall 1: Senden eines Ereignisses mit Textdaten

Legen Sie `event_message.data.text_data` auf `"text data"` fest.

Der Upstreamereignishandler empfängt eine Anforderung, die der folgenden ähnelt. Beachten Sie, dass `Content-Type` für die CloudEvents-HTTP-Anforderung `text/plain` lautet, wobei `dataType`=`text` ist.

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

#### <a name="case-2-send-an-event-with-protobuf-data"></a>Fall 2: Senden eines Ereignisses mit protobuf-Daten

Angenommen, Sie haben die folgende Nachricht von einer Kundin oder einem Kunden erhalten:

```
message MyMessage {
    int32 value = 1;
}
```

Legen Sie `event_message.data.protobuf_data` auf `any.pack(MyMessage)` mit `value = 1` fest.

Der Upstreamereignishandler empfängt eine Anforderung, die der folgenden ähnelt. Beachten Sie, dass `Content-Type` für die CloudEvents-HTTP-Anforderung `application/x-protobuf` lautet, wobei `dataType`=`protobuf` ist.

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

// Just show in hexadecimal; read it as binary
0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
```

Die Daten sind eine gültige Protobuf-Binärdatei. Sie können `proto` und `any.unpack()` wie folgt für die Deserialisierung verwenden:

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

#### <a name="case-3-send-an-event-with-binary-data"></a>Fall 3: Senden eines Ereignisses mit Binärdaten

Legen Sie `send_to_group_message.binary_data` auf `[1, 2, 3]` fest.

Der Upstreamereignishandler empfängt eine Anforderung ähnlich der folgenden. Für `dataType`=`binary` ist der `Content-Type` für die CloudEvents-HTTP-Anforderung `application/octet-stream`. 

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

// Just show in hexadecimal; you need to read it as binary
01 02 03 
```

Der WebSocket-Frame kann entweder im `text`-Format für Textnachrichtframes oder UTF-8-codierten Binärdateien für `binary`-Nachrichtframes vorliegen.

Der Dienst lehnt den Client ab, wenn die Nachricht nicht dem vorgeschriebenen Format entspricht.

## <a name="responses"></a>Antworten

Alle Antwortnachrichten entsprechen dem folgenden protobuf-Format:

```protobuf
message DownstreamMessage {
    oneof message {
        AckMessage ack_message = 1;
        DataMessage data_message = 2;
        SystemMessage system_message = 3;
    }
    
    message AckMessage {
        uint64 ack_id = 1;
        bool success = 2;
        optional ErrorMessage error = 3;
    
        message ErrorMessage {
            string name = 1;
            string message = 2;
        }
    }

    message DataMessage {
        string from = 1;
        optional string group = 2;
        MessageData data = 3;
    }

    message SystemMessage {
        oneof message {
            ConnectedMessage connected_message = 1;
            DisconnectedMessage disconnected_message = 2;
        }
    
        message ConnectedMessage {
            string connection_id = 1;
            string user_id = 2;
        }

        message DisconnectedMessage {
            string reason = 2;
        }
    }
}
```

Vom Client empfangene Nachrichten können einen beliebigen von drei Typen aufweisen: `ack`, `message` oder `system`. 

### <a name="ack-response"></a>Ack-Antwort

Wenn die Anforderung `ackId` enthält, gibt der Dienst eine Ack-Antwort für diese Anforderung zurück. Die Clientimplementierung sollte diesen Ack-Mechanismus verarbeiten, einschließlich:
* Warten auf die Ack-Antwort für einen `async` `await`-Vorgang 
* Timeoutüberprüfung, wenn eine Ack-Antwort innerhalb eines bestimmten Zeitraums nicht empfangen wird

Die Clientimplementierung sollte immer zuerst überprüfen, ob der `success`-Status `true` oder `false` entspricht. Wenn der `success`-Status `false` entspricht, kann der Client Fehlerdetails aus der `error`-Eigenschaft lesen.

### <a name="message-response"></a>Reaktion auf die Nachricht

Clients können Nachrichten empfangen, die von einer Gruppe veröffentlicht wurden, der der Client beigetreten ist. Alternativ können sie Nachrichten von der Serververwaltungsrolle empfangen, wenn der Server Nachrichten an einen bestimmten Client oder bestimmte Benutzer*innen sendet.

In den folgenden Szenarios erhalten Sie immer eine `DownstreamMessage.DataMessage`-Nachricht:

- Wenn die Nachricht von einer Gruppe stammt, wird für `from` `group` angegeben. Wenn die Nachricht vom Server stammt, wird für `from` `server` angegeben.
- Wenn die Nachricht von einer Gruppe stammt, ist `group` der Gruppenname.

Der `dataType` von Sender*innen verursacht, dass eine der folgenden Nachrichten gesendet wird: 
* Wenn der `dataType` `text` lautet, verwenden Sie `message_response_message.data.text_data`. 
* Wenn der `dataType` `binary` lautet, verwenden Sie `message_response_message.data.binary_data`. 
* Wenn der `dataType` `protobuf` lautet, verwenden Sie `message_response_message.data.protobuf_data`. 
* Wenn der `dataType` `json` lautet, verwenden Sie `message_response_message.data.text_data`, und der Inhalt ist eine serialisierte JSON-Zeichenfolge.

### <a name="system-response"></a>Systemantwort

Der Web PubSub-Dienst kann auch systembezogene Antworten an den Client senden. 

#### <a name="connected"></a>Verbunden

Wenn der Client eine Verbindung mit dem Dienst herstellt, erhalten Sie eine `DownstreamMessage.SystemMessage.ConnectedMessage`-Nachricht.

#### <a name="disconnected"></a>Getrennt

Wenn der Server die Verbindung schließt oder der Dienst den Client ablehnt, erhalten Sie eine `DownstreamMessage.SystemMessage.DisconnectedMessage`-Nachricht.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]
