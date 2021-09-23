---
title: WebSocket-Clientprotokolle für Azure Web PubSub
description: Ein Überblick über Clientprotokolle für Azure Web PubSub
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: e08f595e2dc80abe06fa68d1a3e30ac68ff0097c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426344"
---
#  <a name="websocket-client-protocols-for-azure-web-pubsub"></a>WebSocket-Clientprotokolle für Azure Web PubSub

Clients stellen mithilfe des [WebSocket](https://datatracker.ietf.org/doc/html/rfc6455)-Standardprotokolls eine Verbindung mit Azure Web PubSub her.

## <a name="service-endpoint"></a>Dienstendpunkt
Der Dienst stellt zwei Arten von Endpunkten bereit, mit dem Clients eine Verbindung herstellen können:
1. `/client/hubs/{hub}`
2. `/client/?hub={hub}`

`{hub}` ist ein obligatorischer Parameter, der als Isolation für verschiedene Anwendungen fungiert. Er kann entweder im Pfad oder in der Abfrage festgelegt werden.

## <a name="auth"></a>Auth
1. Der Client stellt mithilfe eines JWT-Tokens eine Verbindung mit dem Dienst her.

### <a name="1-the-client-connects-using-the-jwt-token"></a>1. Der Client stellt eine Verbindung mithilfe des JWT-Tokens her.

Das JWT-Token kann sich entweder in der Abfragezeichenfolge `/client/?hub={hub}&access_token={token}` oder im `Authorization` Header befinden: `Authorization: Bearer {token}`.

Ein möglicher Workflow wäre:
1. Der Client führt einen Aushandlungsvorgang mit Ihrem Anwendungsserver aus. Der Anwendungsserver verfügt über eine Authentifizierungs-Middleware, um die Clientanforderung zu verarbeiten und ein JWT-Token zu signieren, damit der Client eine Verbindung mit dem Dienst herstellen kann.
2. Der Anwendungsserver gibt das JWT-Token und die Dienst-URL an den Client zurück.
3. Der Client versucht, eine Verbindung mit dem Web PubSub-Dienst herzustellen, indem er die URL und das JWT-Token verwendet, die vom Anwendungsserver zurückgegeben werden.

<a name="simple_client"></a>

## <a name="the-simple-websocket-client"></a>Der einfache WebSocket-Client
Ein einfacher WebSocket-Client ist, wie die Name schon sagt, eine einfache WebSocket-Verbindung. Er kann auch sein eigenes Unterprotokoll besitzen. 

Beispielsweise kann in JS ein einfacher WebSocket-Client mithilfe folgender Komponenten erstellt werden:
```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')

```

## <a name="the-pubsub-websocket-client"></a>PubSub-WebSocket-Client

Der PubSub-WebSocket-Client unterstützt zwei Unterprotokolle: `json.webpubsub.azure.v1` und `protobuf.webpubsub.azure.v1`

#### <a name="json-subprotocol"></a>JSON-Unterprotokoll

In JS kann ein PubSub-WebSocket-Client mit dem JSON-Unterprotokoll beispielsweise folgendermaßen erstellt werden:
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

#### <a name="protobuf-subprotocol"></a>Protobuf-Unterprotokoll

Protobuf ist ein sprachneutrales, plattformneutrales Protokoll im Binärformat, das zum benutzerfreundlichen Senden von Binärdaten geeignet ist. Protobuf stellt Tools zum Generieren von Clients für viele Sprachen wie z. B. Java, Python, Objective-C, C# und C++ zur Verfügung. [Weitere Informationen zu Protobuf](https://developers.google.com/protocol-buffers).

In JS kann ein PubSub-WebSocket-Client mit dem Protobuf-Unterprotokoll beispielsweise folgendermaßen erstellt werden:
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

Wenn der Client ein Unterprotokoll verwendet, wird erwartet, dass sowohl der ausgehende als auch der eingehende Datenframe JSON-Nutzdaten sind. Ein authentifizierter Client kann Nachrichten direkt über den Azure Web PubSub-Dienst für andere Clients veröffentlichen.

### <a name="permissions"></a>Berechtigungen

Möglicherweise haben Sie bemerkt, dass - wenn wir über PubSub-WebSocket-Clients reden - ein Client nur dann Nachrichten für andere Clients veröffentlichen kann, wenn er dazu *autorisiert* ist. Die Berechtigung des Clients kann erteilt werden, wenn er verbunden ist oder während der Lebensdauer der Verbindung.

| Rolle | Berechtigung |
|---|---|
| Nicht angegeben | Der Client kann Ereignisanforderungen senden.
| `webpubsub.joinLeaveGroup` | Der Client kann einer beliebigen Gruppe beitreten/diese verlassen.
| `webpubsub.sendToGroup` | Der Client kann Nachrichten in einer beliebigen Gruppe veröffentlichen.
| `webpubsub.joinLeaveGroup.<group>` | Der Client kann Gruppe `<group>` beitreten/verlassen.
| `webpubsub.sendToGroup.<group>` | Der Client kann Nachrichten in Gruppe `<group>` veröffentlichen.

* Details zum JSON-Unterprotokoll werden unter [JSON-Unterprotokoll](./reference-json-webpubsub-subprotocol.md) beschrieben.
* Details zum Protobuf-Unterprotokoll werden unter [Protobuf-Unterprotokoll](./reference-protobuf-webpubsub-subprotocol.md) beschrieben.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]
