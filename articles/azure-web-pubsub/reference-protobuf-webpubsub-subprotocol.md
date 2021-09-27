---
title: 'Referenz: Von Azure Web PubSub unterstütztes Protobuf-WebSocket-Unterprotokoll `protobuf.webpubsub.azure.v1`'
description: Die Referenz beschreibt das Azure Web PubSub unterstützte WebSocket-Unterprotokoll `protobuf.webpubsub.azure.v1`
author: chenyl
ms.author: chenyl
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/31/2021
ms.openlocfilehash: 9b54f219f52c9fa19134841488fcdf7f2bbc844d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440724"
---
#  <a name="azure-web-pubsub-supported-protobuf-websocket-subprotocol"></a>Von Azure Web PubSub unterstütztes Protobuf-WebSocket-Unterprotokoll
     
In diesem Dokument wird das Unterprotokoll `protobuf.webpubsub.azure.v1` beschrieben.

Wenn der Client dieses Unterprotokoll verwendet, wird erwartet, dass sowohl der ausgehende als auch der eingehende Datenframe **Protobuf**-Nutzdaten sind.

## <a name="overview"></a>Übersicht

Unterprotokoll `protobuf.webpubsub.azure.v1` ermöglicht es den Clients, direkt Veröffentlichungen/Abonnements anstatt eines Roundtrips zum Upstreamserver durchzuführen. Die WebSocket-Verbindung mit dem `protobuf.webpubsub.azure.v1`-Unterprotokoll wird auch „PubSub WebSocket-Client“ genannt.

In JS kann ein PubSub-WebSocket-Client mit dem Protobuf-Unterprotokoll beispielsweise folgendermaßen erstellt werden:
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```
Für einen einfachen WebSocket-Client muss der *Server* über eine MUST HAVE-Rolle verfügen, um die Ereignisse von Clients zu verarbeiten. Eine einfache WebSocket-Verbindung löst beim Versenden von Nachrichten immer ein `message` Ereignis aus und verlässt sich immer auf den Server, um Nachrichten zu verarbeiten und andere Vorgänge durchzuführen. Mithilfe des `protobuf.webpubsub.azure.v1` Unterprotokolls kann ein autorisierter Client einer Gruppe mithilfe von [Joinanforderungen](#join-groups) beitreten und Nachrichten mithilfe von [Veröffentlichungsanforderungen](#publish-messages) direkt in einer Gruppe veröffentlichen. Er kann Nachrichten auch an verschiedene Upstreamkomponenten (Ereignishandler) weiterleiten, indem das *Ereignis*, zu dem die Nachricht gehört, mithilfe von [Ereignisanforderungen](#send-custom-events) angepasst wird.

> [!NOTE]
> Derzeit unterstützt der WebPubSub-Dienst nur [proto3](https://developers.google.com/protocol-buffers/docs/proto3).

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

Alle Anforderungsnachrichten folgen dem folgenden Protobuf-Format.

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
        optional int32 ack_id = 2;
        MessageData data = 3;
    }

    message EventMessage {
        string event = 1;
        MessageData data = 2;
    }
    
    message JoinGroupMessage {
        string group = 1;
        optional int32 ack_id = 2;
    }

    message LeaveGroupMessage {
        string group = 1;
        optional int32 ack_id = 2;
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

* `ackId` ist optional, es handelt sich um eine inkrementelle ganze Zahl für diese Befehlsmeldung. Wenn die `ackId` angegeben ist, sendet der Dienst eine [ack-Antwortnachricht](#ack-response) zurück an den Client, wenn der Befehl ausgeführt wird.

### <a name="leave-groups"></a>Gruppen verlassen

Format:

Legen Sie `leave_group_message.group` auf den Gruppennamen fest.

* `ackId` ist optional, es handelt sich um eine inkrementelle ganze Zahl für diese Befehlsmeldung. Wenn die `ackId` angegeben ist, sendet der Dienst eine [ack-Antwortnachricht](#ack-response) zurück an den Client, wenn der Befehl ausgeführt wird.

### <a name="publish-messages"></a>Veröffentlichen von Nachrichten

Format:

* `ackId` ist optional, es handelt sich um eine inkrementelle ganze Zahl für diese Befehlsmeldung. Wenn die `ackId` angegeben ist, sendet der Dienst eine [ack-Antwortnachricht](#ack-response) zurück an den Client, wenn der Befehl ausgeführt wird.

Es gibt einen impliziten `dataType`, der `protobuf`, `text` oder `binary` sein kann und auf den von Ihnen festgelegten `data` in `MessageData` basiert. Die Empfängerclients können den `dataType` nutzen, um den Inhalt ordnungsgemäß zu verarbeiten.

* `protobuf`: Wenn Sie `send_to_group_message.data.protobuf_data` festlegen, bedeutet dies implizit `protobuf`. `protobuf_data` kann einen [beliebigen](https://developers.google.com/protocol-buffers/docs/proto3#any) Typ aufweisen. Alle anderen Clients erhalten Protobuf-codierte Binärdateien, die vom Protobuf-SDK deserialisiert werden können. Auf Clients, die nur textbasierten Inhalt unterstützen (z. B. `json.webpubsub.azure.v1`), erhalten sie Base64-codierte Binärdateien.

* `text`: Wenn Sie `send_to_group_message.data.text_data` festlegen, bedeutet dies implizit `text`. `text_data` sollte eine Zeichenfolge sein. Alle Clients mit einem anderen Protokoll erhalten eine UTF-8-codierte Zeichenfolge.

* `binary`: Wenn Sie `send_to_group_message.data.binary_data` festlegen, bedeutet dies implizit `binary`. `binary_data` sollte ein Bytearray sein. Alle Clients mit einem anderen Protokoll erhalten eine unformatierte Binärdatei ohne Protobuf-Codierung. Auf Clients, die nur textbasierten Inhalt unterstützen (z. B. `json.webpubsub.azure.v1`), erhalten sie Base64-codierte Binärdateien.

#### <a name="case-1-publish-text-data"></a>Fall 1: Veröffentlichen von Textdaten:

Legen Sie `send_to_group_message.group` auf `group` und `send_to_group_message.data.text_data` auf `"text data"` fest.

* Welcher Protobuf-Unterprotokollclient in dieser Gruppe `group` den binären Frame empfängt und die [DownstreamMessage](#responses) zum Deserialisieren verwenden kann.

* Was der Unterprotokollclient in dieser Gruppe `group` empfängt:

```json
{
    "type": "message",
    "from": "group",
    "group": "group",
    "dataType" : "text",
    "data" : "text data"
}
```

* Was der unformatierte Client in dieser Gruppe `group` empfängt, sind Zeichenfolgendaten `text data`.

#### <a name="case-2-publish-protobuf-data"></a>Fall 2: Veröffentlichen von Protobuf-Daten:

Angenommen, Sie erhalten eine Kundennachricht:

```
message MyMessage {
    int32 value = 1;
}
```

Legen Sie `send_to_group_message.group` auf `group` und `send_to_group_message.data.protobuf_data` auf `Any.pack(MyMessage)` mit `value = 1` fest.

* Welcher Protobuf-Unterprotokollclient in dieser Gruppe `group` den binären Frame empfängt und die [DownstreamMessage](#responses) zum Deserialisieren verwenden kann.

* Was der Unterprotokoll-Client in dieser Gruppe `group` empfängt:

```json
{
    "type": "message",
    "from": "group",
    "group": "G",
    "dataType" : "protobuf",
    "data" : "Ci90eXBlLmdvb2dsZWFwaXMuY29tL2F6dXJlLndlYnB1YnN1Yi5UZXN0TWVzc2FnZRICCAE=" // Base64 encoded bytes
}
```

Beachten Sie, dass es sich bei den Daten um eine Base64-codierte deserialisierbare Protobuf-Binärdatei handelt. Sie können die folgende Protobuf-Definition verwenden und sie mit `Any.unpack()` deserialisieren:

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

* Was der unformatierte Client in dieser Gruppe `group` empfängt ist der Binärframe.

```
# Show in Hex
0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
```

#### <a name="case-3-publish-binary-data"></a>Fall 3: Veröffentlichen von Binärdaten:

Legen Sie `send_to_group_message.group` auf `group` und `send_to_group_message.data.binary_data` auf `[1, 2, 3]` fest.

* Welcher Protobuf-Unterprotokollclient in dieser Gruppe `group` den binären Frame empfängt und die [DownstreamMessage](#responses) zum Deserialisieren verwenden kann.

* Was der Unterprotokollclient in dieser Gruppe `group` empfängt:

```json
{
    "type": "message",
    "from": "group",
    "group": "group",
    "dataType" : "binary",
    "data" : "AQID", // Base64 encoded [1,2,3]
}
```

Da der JSON-Unterprotokollclient nur textbasierte Nachrichten unterstützt, wird die Binärdatei immer mit Base64 codiert.

* Was der unformatierte Client in dieser Gruppe `group` empfängt sind **binäre** Daten im Binär-Frame.

```
# Show in Hex
01 02 03
```

### <a name="send-custom-events"></a>Versenden von benutzerdefinierten Ereignissen

Es gibt einen impliziten `dataType`, der `protobuf`, `text` oder `binary` sein kann und auf den von Ihnen festgelegten dataType basiert. Die Empfängerclients können den `dataType` nutzen, um den Inhalt ordnungsgemäß zu verarbeiten.

* `protobuf`: Wenn Sie `event_message.data.protobuf_data` festlegen, bedeutet dies implizit `protobuf`. `protobuf_data` kann ein beliebiger unterstützter Protobuf-Typ sein. Ereignishandler erhalten Protobuf-codierte Binärdateien, die von jedem Protobuf-SDK deserialisiert werden können.

* `text`: Wenn Sie `event_message.data.text_data` festlegen, bedeutet dies implizit `text`. `text_data` sollte eine Zeichenfolge sein. Der Ereignishandler erhält eine UTF-8-codierte Zeichenfolge.

* `binary`: Wenn Sie `event_message.data.binary_data` festlegen, bedeutet dies implizit `binary`. `binary_data` sollte ein Bytearray sein. Der Ereignishandler erhält einen unformatierten Binärframe.

#### <a name="case-1-send-event-with-text-data"></a>Fall 1: Ereignis mit Textdaten versenden:

Legen Sie `event_message.data.text_data` auf `"text data"` fest.

Was der Upstream-Ereignishandler empfängt (siehe unten); der `Content-Type` der CloudEvents-HTTP-Anforderung ist `text/plain` für `dataType`=`text`.

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

#### <a name="case-2-send-event-with-protobuf-data"></a>Fall 2: Senden eines Ereignisses mit Protobuf-Daten:

Angenommen, Sie erhalten eine Kundennachricht:

```
message MyMessage {
    int32 value = 1;
}
```

Legen Sie `event_message.data.protobuf_data` auf `any.pack(MyMessage)` mit `value = 1` fest.

Was der Upstream-Ereignishandler empfängt (siehe unten). Beachten Sie, dass der `Content-Type` der CloudEvents-HTTP-Anforderung `application/x-protobuf` ist für `dataType`=`protobuf`.

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

// Just show in hex, you need to read as binary
0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
```

Die Daten sind eine gültige Protobuf-Binärdatei. Sie können `proto` und `any.unpack()` wie folgt für die Deserialisierung verwenden:

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

#### <a name="case-3-send-event-with-binary-data"></a>Fall 3: Ereignis mit Binärdaten versenden:

Legen Sie `send_to_group_message.binary_data` auf `[1, 2, 3]` fest.

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

// Just show in hex, you need to read as binary
01 02 03 
```

Der WebSocket-Frame kann entweder das `text`-Format für Textnachricht-Frames aufweisen oder UTF8-codierte Binärdateien für `binary`-Nachrichtframes enthalten.

Der Dienst lehnt den Client ab, wenn die Nachricht nicht dem beschriebenen Format entspricht.

## <a name="responses"></a>Antworten

Alle Antwortnachrichten folgen dem folgenden Protobuf-Format:

```protobuf
message DownstreamMessage {
    oneof message {
        AckMessage ack_message = 1;
        DataMessage data_message = 2;
        SystemMessage system_message = 3;
    }
    
    message AckMessage {
        int32 ack_id = 1;
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

Vom Client empfangene Nachrichten können verschiedene Typen aufweisen: `ack`, `message` und `system`: 

### <a name="ack-response"></a>Ack-Antwort

Wenn die Anforderung `ackId` enthält, gibt der Dienst eine Ack-Antwort für diese Anforderung zurück. Die Clientimplementierung sollte diesen Ack-Mmechanismus verarbeiten. Dazu gehört das Warten auf die Ack-Antwort für einen `async` `await` Vorgang und das Ausführen einer Timeoutüberprüfung, wenn die Ack-Antwort während eines bestimmten Zeitraums nicht empfangen wird.

Die Clientimplementierung SOLLTE immer zuerst überprüfen, ob `success` `true` oder `false` ist. Nur wenn `success` `false` ist, liest der Client aus `error`.

### <a name="message-response"></a>Reaktion auf die Nachricht

Clients können Nachrichten entweder von einer Gruppe empfangen, in die der Client eingebunden ist, oder von der Serververwaltungsrolle, über die der Server Nachrichten an einen bestimmten Client oder einen bestimmten Benutzer sendet.

Sie erhalten immer eine `DownstreamMessage.DataMessage`.

- Wenn die Nachricht von einer Gruppe stammt, ist `from` `group`. Wenn die Nachricht vom Server stammt, ist `from` `server`.

- Wenn die Nachricht von einer Gruppe stammt, ist `group` der Gruppenname.

- Der `dateType` des Absenders verursacht das Festlegen einer der Nachrichten. Wenn `dateType` `text` ist, sollten Sie `message_response_message.data.text_data` verwenden. Wenn `dateType` `binary` ist, sollten Sie `message_response_message.data.binary_data` verwenden. Wenn `dateType` `protobuf` ist, sollten Sie `message_response_message.data.protobuf_data` verwenden. Wenn `dateType` `json` ist, sollten Sie `message_response_message.data.text_data` verwenden, und der Inhalt wird als JSON-Zeichenfolge serialisiert.

### <a name="system-response"></a>Systemantwort

Der Web PubSub-Dienst kann auch systembezogene Antworten an den Client senden. 

#### <a name="connected"></a>Verbunden

Wenn die Verbindung mit dem Dienst verbunden ist. Sie erhalten eine `DownstreamMessage.SystemMessage.ConnectedMessage`.

#### <a name="disconnected"></a>Getrennt

Wenn der Server die Verbindung schließt oder wenn der Dienst den Client ablehnt. Sie erhalten eine `DownstreamMessage.SystemMessage.DisconnectedMessage`.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]