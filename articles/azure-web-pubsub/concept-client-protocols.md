---
title: WebSocket-Clientprotokolle für Azure Web PubSub
description: Dieser Artikel bietet eine Übersicht über Clientprotokolle für Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 8f78dbcdecc552e94c3d871f610ef227a1795f8e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576258"
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

Ein einfacher WebSocket-Client ist, wie die Name schon sagt, eine einfache WebSocket-Verbindung. Er kann zudem über ein eigenes benutzerdefiniertes Unterprotokoll verfügen. 

In JavaScript können Sie z. B. mithilfe des folgenden Codes einen einfachen WebSocket-Client erstellen:

```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')
```

## <a name="the-pubsub-websocket-client"></a>PubSub-WebSocket-Client

Der PubSub-WebSocket-Client unterstützt zwei Unterprotokolle:
* `json.webpubsub.azure.v1`
* `protobuf.webpubsub.azure.v1`

#### <a name="the-json-subprotocol"></a>JSON-Unterprotokoll

In JavaScript können Sie beispielsweise einen PubSub-WebSocket-Client mit einem JSON-Unterprotokoll erstellen, indem Sie Folgendes verwenden:

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

#### <a name="the-protobuf-subprotocol"></a>Protobuf-Unterprotokoll

Protokollpuffer (protobuf) ist ein sprachneutrales, plattformneutrales, auf Binärdaten basierendes Protokoll, das das Senden von Binärdaten vereinfacht. Protobuf stellt Tools zum Generieren von Clients für viele Sprachen wie z. B. Java, Python, Objective-C, C# und C++ zur Verfügung. [Weitere Informationen zu Protobuf](https://developers.google.com/protocol-buffers).

In JavaScript können Sie beispielsweise einen PubSub-WebSocket-Client mit einem protobuf-Unterprotokoll erstellen, indem Sie den folgenden Code verwenden:

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

Wenn der Client ein Unterprotokoll verwendet, wird erwartet, dass sowohl der ausgehende als auch der eingehende Datenrahmen JSON-Nutzdaten sind. Ein autorisierter Client kann Nachrichten direkt über den Azure Web PubSub-Dienst für andere Clients veröffentlichen.

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

Weitere Informationen zum JSON-Unterprotokoll finden Sie unter [JSON-Unterprotokoll](./reference-json-webpubsub-subprotocol.md).

Weitere Informationen zum protobuf-Unterprotokoll finden Sie unter [Protobuf-Unterprotokoll](./reference-protobuf-webpubsub-subprotocol.md).

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]
