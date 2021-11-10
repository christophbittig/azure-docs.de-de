---
title: 'Referenz: CloudEvents-Erweiterung für Azure Web PubSub'
description: In der Referenz wird die CloudEvents-Erweiterung beschrieben, die für den Azure Web PubSub-Dienst definiert ist.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 52c9d3f303b657a437beba31e6e6945346397e54
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997360"
---
#  <a name="cloudevents-extension-for-azure-web-pubsub"></a>CloudEvents-Erweiterung für Azure Web PubSub

Der Dienst übergibt Clientereignisse mithilfe des [CloudEvents-HTTP-Protokolls](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md) an den Upstreamwebhook.

Die Daten, die vom Dienst an den Server gesendet werden, haben immer das CloudEvents-Format `binary`.

- [Webhookvalidierung](#protection)
- [Web PubSub CloudEvents-Attributerweiterung](#extension)
- [Ereignisse](#events)
    - Blockierende Ereignisse
        - [System`connect`ereignis](#connect)
        - [Benutzerereignisse](#message)
    - Nicht-blockierende Ereignisse
        - [System`connected`ereignis](#connected)
        - [System`disconnected`ereignis](#disconnected)

## <a name="webhook-validation"></a>Webhookvalidierung

<a name="protection"></a>

Die Webhookvalidierung folgt [CloudEvents](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Die Anforderung enthält immer `WebHook-Request-Origin: xxx.webpubsub.azure.com` im Header.

Wenn und nur dann, wenn das Übermittlungsziel die Übermittlung der Ereignisse gestattet, MUSS es auf die Anforderung antworten, indem es den `WebHook-Allowed-Origin`-Header mit einschließt, z. B.:

`WebHook-Allowed-Origin: *`

Oder:

`WebHook-Allowed-Origin: xxx.webpubsub.azure.com`

Vorerst werden [WebHook-Request-Rate](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#414-webhook-request-rate) und [WebHook-Request-Callback](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#413-webhook-request-callback) nicht unterstützt.


## <a name="web-pubsub-cloudevents-attribute-extension"></a>Web PubSub CloudEvents-Attributerweiterung
<a name="extension"></a>

> Es wurde auch erwähnt, dass die HTTP-Spezifikation jetzt einem ähnlichen Muster folgt, d. h. es wird nicht mehr vorgeschlagen, dass Erweiterungs-HTTP-Headern das Präfix X- vorangestellt wird.

Diese Erweiterung definiert Attribute, die von der Web PubSub-Anwendung für jedes von ihr erzeugte Ereignis verwendet werden.

### <a name="attributes"></a>Attributes

| Name | type | BESCHREIBUNG | Beispiel|
|--|--|--|--|
| `userId` | `string` | Der Benutzer, für den die Verbindung authentifiziert wurde | |
| `hub` | `string` | Der Hub, zu dem die Verbindung gehört | |
| `connectionId` | `string` | Die connectionId ist für die Clientverbindung eindeutig definiert. | |
| `eventName` | `string` | Der Name des Ereignisses ohne Präfix | |
| `subprotocol` | `string` | Das Unterprotokoll, das der Client verwendet, sofern vorhanden. | |
| `connectionState` | `string` | Legt den Status der Verbindung fest. Sie können denselben Antwort-Header verwenden, um den Wert des Status zurückzusetzen. Mehrere `connectionState`-Header sind nicht zulässig. Kodieren Sie den String-Wert base64, wenn er komplexe Zeichen enthält, z. B. können Sie `base64(jsonString)` verwenden, um ein komplexes Objekt mit diesem Attribut zu übergeben.| |
| `signature` | `string` | Die Signatur für den Upstreamwebhook, um zu überprüfen, ob die eingehende Anforderung vom erwarteten Ursprung stammt. Der Dienst berechnet den Wert mithilfe des primären Zugriffsschlüssels und des sekundären Zugriffsschlüssels als HMAC-Schlüssel: `Hex_encoded(HMAC_SHA256(accessKey, connectionId))`. Der Upstream sollte vor der Verarbeitung überprüfen, ob die Anforderung gültig ist. | |

## <a name="events"></a>Events

Es gibt zwei Arten von Ereignissen: Eine Art sind *blockierende* Ereignisse, d. h. der Dienst wartet und wird erst fortgesetzt, wenn die Antwort des Ereignisses vorliegt. Die andere Art sind *nicht-blockierende* Ereignisse, d. h. der Dienst wartet nicht auf die Antwort des Ereignisses, bevor die nächste Nachricht verarbeitet wird.

- Blockierende Ereignisse
    - [System`connect`ereignis](#connect)
    - [Benutzerereignisse](#message)
- Nicht-blockierende Ereignisse
    - [System`connected`ereignis](#connected)
    - [System`disconnected`ereignis](#disconnected)

### <a name="system-connect-event"></a>System`connect`ereignis
<a name="connect"></a>

* `ce-type`: `azure.webpubsub.sys.connect`
* `Content-Type`: `application/json`

#### <a name="request-format"></a>Anforderungsformat:

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.connect
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: connect

{
    "claims": {},
    "query": {},
    "headers": {},
    "subprotocols": [],
    "clientCertificates": [
        {
            "thumbprint": "ABC"
        }
    ]
}

```

#### <a name="success-response-format"></a>Format der Antwort bei Erfolg:
* Statuscode:
    * `204`: Erfolg; ohne Inhalt.
    * `200`: Erfolg; der Inhalt SOLLTE ein JSON-Format mit den folgenden zulässigen Eigenschaften aufweisen:
* Kopfzeile `ce-connectionState`: Wenn dieser Header existiert, wird der Verbindungsstatus dieser Verbindung auf den Wert des Headers aktualisiert. Bitte beachten Sie, dass nur *blockierende* Ereignisse den Verbindungsstatus aktualisieren können. Das folgende Beispiel verwendet einen base64-kodierten JSON-String, um den komplexen Status der Verbindung zu speichern.
* 
```HTTP
HTTP/1.1 200 OK
ce-connectionState: eyJrZXkiOiJhIn0=

{
    "groups": [],
    "userId": "",
    "roles": [],
    "subprotocol": ""
}

```

* `subprotocols`

    Das `connect`-Ereignis gibt das Unterprotokoll und die Authentifizierungsinformationen an Upstream vom Client weiter. Der Azure SignalR Service verwendet den Statuscode, um zu bestimmen, ob die Anforderung auf das WebSocket-Protokoll aktualisiert wird.

    Wenn die Anforderung die Eigenschaft `subprotocols` enthält, sollte der Server ein von ihm unterstütztes Unterprotokoll zurückgeben. Wenn der Server keine Unterprotokolle verwenden möchte, sollte er die `subprotocol`-Eigenschaft **nicht** als Antwort senden. [Das Senden eines leeren Headers ist ungültig](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers#Subprotocols).

* `userId`: `{auth-ed user ID}`

    Da der Dienst anonyme Verbindungen zulässt, liegt es in der Verantwortung des `connect` Ereignisses, dem Dienst die Benutzer-ID der Clientverbindung mitzuteilen. Der Dienst liest die Benutzer-ID aus den Antwortnutzdaten `userId`, sofern diese existiert. Die Verbindung wird getrennt, wenn die Benutzer-ID weder aus den Anforderungsansprüchen noch aus der Antwortnutzdaten des `connect`-Ereignisses gelesen werden kann.

<a name="connect_response_header_group"></a>
 
* `groups`: `{groups to join}`

    Die Eigenschaft bietet dem Benutzer eine praktische Möglichkeit, diese Verbindung zu einer oder mehreren Gruppen hinzuzufügen. Auf diese Weise ist kein weiterer Aufruf erforderlich, um diese Verbindung zu einer Gruppe hinzuzufügen.

* `roles`: `{roles the client has}`
    
    Die Eigenschaft bietet dem Upstream eine Möglichkeit, den Client zu autorisieren. Verschiedene Rollen definieren verschiedene anfängliche Berechtigungen des Clients. Dies kann nützlich sein, wenn der Client ein PubSub WebSocket-Client ist. Weitere Informationen zu den Berechtigungen finden Sie unter [Clientberechtigungen](./concept-client-protocols.md#permissions).

#### <a name="error-response-format"></a>Format von Fehlerantworten:

* `4xx`: Fehler; die Antwort von Upstream wird als Antwort für die Clientanforderung zurückgegeben.

```HTTP
HTTP/1.1 401 Unauthorized
```

### <a name="system-connected-event"></a>System`connected`ereignis
<a name="connected"></a> Der Dienst ruft Upstream auf, wenn der Client den WebSocket-Handshake abschließt und erfolgreich verbunden ist.

* `ce-type`: `azure.webpubsub.sys.connected`
* `Content-Type`: `application/json`
* `ce-connectionState`: `eyJrZXkiOiJhIn0=`

Der Anforderungstext ist ein leerer JSON-Code.

#### <a name="request-format"></a>Anforderungsformat:

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.connected
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: connect
ce-subprotocol: abc
ce-connectionState: eyJrZXkiOiJhIn0=

{}

```

#### <a name="response-format"></a>Antwortformat:

`2xx`: Antwort bei Erfolg

`connected` ist ein asynchrones Ereignis. Wenn der Antwortstatuscode nicht erfolgreich ist, protokolliert der Dienst einen Fehler.

```HTTP
HTTP/1.1 200 OK
```


### <a name="system-disconnected-event"></a>System`disconnected`ereignis
Das <a name="disconnected"></a>
`disconnected`-Ereignis wird **immer** ausgelöst, sofern die Clientanforderung abgeschlossen wird, wenn das **Verbindungs** ereignis den Statuscode `2xx` zurückgibt.

* `ce-type`: `azure.webpubsub.sys.disconnected`
* `Content-Type`: `application/json`

#### <a name="request-format"></a>Anforderungsformat:

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.disconnected
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: disconnect
ce-subprotocol: abc
ce-connectionState: eyJrZXkiOiJhIn0=

{
    "reason": "{Reason}"
}

```

* `reason`

    `reason` beschreibt den Grund für die getrennten Clientverbindungen.


#### <a name="response-format"></a>Antwortformat:

`2xx`: Antwort bei Erfolg

`disconnected` ist ein asynchrones Ereignis. Wenn der Antwortstatuscode nicht erfolgreich ist, protokolliert der Dienst einen Fehler.

```HTTP
HTTP/1.1 200 OK
```


### <a name="user-event-message-for-the-simple-websocket-clients"></a>Benutzerereignis `message` für einfache WebSocket-Clients
<a name="message"></a> Der Dienst ruft den Ereignishandler upstream für jeden WebSocket-Nachrichtenframe auf.

* `ce-type`: `azure.webpubsub.user.message`
* `Content-Type`: `application/octet-stream` für binären Frame; `text/plain` für Textframe; 

UserPayload ist das, was der Client sendet.

#### <a name="request-format"></a>Anforderungsformat:

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/octet-stream | text/plain | application/json
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.message
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: message
ce-connectionState: eyJrZXkiOiJhIn0=

UserPayload

```

#### <a name="success-response-format"></a>Format der Antwort bei Erfolg

* Statuscode
    * `204`: Erfolg; ohne Inhalt.
    * `200`: Erfolg; das Format von `UserResponsePayload` hängt von `Content-Type` der Antwort ab.
* `Content-Type`: `application/octet-stream` für binären Frame; `text/plain` für Textframe; 
* Kopfzeile `Content-Type`: `application/octet-stream` für Binärrahmen; `text/plain` für Textrahmen; 
* Kopfzeile `ce-connectionState`: Wenn dieser Header existiert, wird der Verbindungsstatus dieser Verbindung auf den Wert des Headers aktualisiert. Bitte beachten Sie, dass nur *blockierende* Ereignisse den Verbindungsstatus aktualisieren können. Das folgende Beispiel verwendet einen base64-kodierten JSON-String, um den komplexen Status der Verbindung zu speichern.

Wenn `Content-Type` `application/octet-stream` ist, sendet der Dienst `UserResponsePayload` mithilfe des `binary` WebSocket-Frames an den Client. Wenn `Content-Type` `text/plain` ist, sendet der Dienst `UserResponsePayload` mithilfe des `text` WebSocket-Frames `UserResponsePayload` an den Client. 

```HTTP
HTTP/1.1 200 OK
Content-Type: application/octet-stream (for binary frame) or text/plain (for text frame)
Content-Length: nnnn
ce-connectionState: eyJrZXkiOiJhIn0=

UserResponsePayload
```

#### <a name="error-response-format"></a>Format von Fehlerantworten
Wenn der Statuscode „nicht erfolgreich“ lautet, wird er als Fehlerantwort betrachtet. Die Verbindung wird **getrennt**, wenn der `message` Antwortstatuscode „nicht erfolgreich“ lautet.

### <a name="user-custom-event-custom_event-for-pubsub-websocket-clients"></a>Benutzerdefiniertes Ereignis `{custom_event}` für PubSub WebSocket-Clients
<a name="custom_event"></a>

Der Dienst ruft den Ereignishandler-Webhook für jede gültige benutzerdefinierte Ereignismeldung auf.

#### <a name="case-1-send-event-with-text-data"></a>Fall 1: Ereignis mit Textdaten versenden:
```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "text",
    "data": "text data"
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
ce-subprotocol: json.webpubsub.azure.v1
ce-connectionState: eyJrZXkiOiJhIn0=

text data

```

#### <a name="case-2-send-event-with-json-data"></a>Fall 2: Ereignis mit JSON-Daten versenden:
```json
{
    "type": "event",
    "event": "<event_name>",
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
ce-subprotocol: json.webpubsub.azure.v1
ce-connectionState: eyJrZXkiOiJhIn0=

{
    "hello": "world"
}

```

#### <a name="case-3-send-event-with-binary-data"></a>Fall 3: Ereignis mit Binärdaten versenden:
```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "binary",
    "data": "aGVsbG8gd29ybGQ=" // base64 encoded binary
}
```

Was der Upstream-Ereignishandler empfängt (siehe unten); der `Content-Type` der CloudEvents-HTTP-Anforderung ist `application/octet-stream` für `dataType`=`binary`.

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
ce-subprotocol: json.webpubsub.azure.v1

<binary data>

```

#### <a name="success-response-format"></a>Format der Antwort bei Erfolg

```HTTP
HTTP/1.1 200 OK
Content-Type: application/octet-stream | text/plain | application/json
Content-Length: nnnn

UserResponsePayload
```
* Statuscode
    * `204`: Erfolg; ohne Inhalt.
    * `200`: Erfolg; daten, die an den PubSub WebSocket-Client gesendet werden, hängen von `Content-Type` ab. 
* Kopfzeile `ce-connectionState`: Wenn dieser Header existiert, wird der Verbindungsstatus dieser Verbindung auf den Wert des Headers aktualisiert. Bitte beachten Sie, dass nur *blockierende* Ereignisse den Verbindungsstatus aktualisieren können. Das folgende Beispiel verwendet einen base64-kodierten JSON-String, um den komplexen Status der Verbindung zu speichern.
* Wenn Kopfzeile `Content-Type` gleich `application/octet-stream` ist, sendet der Dienst `UserResponsePayload` unter Verwendung von `dataType` als `binary` mit base64-kodierter Nutzlast an den Client zurück. Eine Beispielantwort:
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType": "binary",
        "data" : "aGVsbG8gd29ybGQ="
    }
    ```
* Wenn `Content-Type` `text/plain` ist, sendet der Dienst mit der Nutzdatenzeichenfolge `UserResponsePayload` mit `dataType` als `text` an den Client.
* Wenn `Content-Type` `application/json` ist, sendet der Dienst mit `data`-Werttoken als Antwortnutzdatenkörper `UserResponsePayload` mit `dataType`=`json` an den Client.


#### <a name="error-response-format"></a>Format von Fehlerantworten
Wenn der Statuscode „nicht erfolgreich“ lautet, wird er als Fehlerantwort betrachtet. Die Verbindung wird **getrennt**, wenn der Antwortstatuscode `{custom_event}` „nicht erfolgreich“ lautet.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]
