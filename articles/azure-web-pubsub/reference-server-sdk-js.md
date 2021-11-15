---
title: 'Referenz: JavaScript-SDK für Azure Web PubSub'
description: In der Referenz wird das JavaScript-SDK für den Azure Web PubSub-Dienst beschrieben.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/01/2021
ms.openlocfilehash: 4571bb8581892525785c3f08e0c627bf20f511aa
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578343"
---
# <a name="javascript-sdk-for-azure-web-pubsub"></a>JavaScript-SDK für Azure Web PubSub

Für JavaScript werden zwei Bibliotheken angeboten: die Dienstclientbibliothek und Express-Middleware. Die folgenden Abschnitte enthalten weitere Informationen zu diesen Bibliotheken.

<a name="service-client-library"></a>

## <a name="azure-web-pubsub-service-client-library-for-javascript"></a>Die Clientbibliothek des Azure Web PubSub-Diensts für JavaScript

Sie können diese Bibliothek für folgende Zwecke verwenden:
- Das Versenden von Nachrichten an Hubs und Gruppen. 
- Das Versenden von Nachrichten an bestimmte Benutzer und Verbindungen.
- Das Organisieren von Benutzern und Verbindungen in Gruppen.
- Das Schließen von Verbindungen
- Das Erteilen, Widerrufen und Prüfen von Berechtigungen für eine vorhandene Verbindung

[Quellcode](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub) |
[Paket (NPM)](https://www.npmjs.com/package/@azure/web-pubsub) |
[API-Referenzdokumentation](/javascript/api/@azure/web-pubsub/) |
[Produktdokumentation](./index.yml) |
[Beispiele][samples_ref]

### <a name="get-started"></a>Erste Schritte

Verwenden Sie [Node.js](https://nodejs.org/) ab Version 8.x.x. Stellen Sie außerdem sicher, dass die folgenden Voraussetzungen erfüllt sind:

- Ein [Azure-Abonnement][azure_sub].
- Eine vorhandene Azure Web PubSub-Dienstinstanz.

#### <a name="install-the-azureweb-pubsub-package"></a>Installieren Sie das Paket `@azure/web-pubsub`.

```bash
npm install @azure/web-pubsub
```

#### <a name="create-and-authenticate-webpubsubserviceclient"></a>Erstellen und Authentifizieren von `WebPubSubServiceClient`

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
```

Sie können `WebPubSubServiceClient` auch mithilfe eines Endpunkts und eines `AzureKeyCredential` authentifizieren:

```js
const { WebPubSubServiceClient, AzureKeyCredential } = require("@azure/web-pubsub");

const key = new AzureKeyCredential("<Key>");
const serviceClient = new WebPubSubServiceClient("<Endpoint>", key, "<hubName>");
```

### <a name="key-concepts"></a>Wichtige Begriffe

[!INCLUDE [Terms](includes/terms.md)]

### <a name="examples"></a>Beispiele

#### <a name="broadcast-a-json-message-to-all-users"></a>Übertragen Sie eine JSON-Nachricht an alle Benutzer

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll({ message: "Hello world!" });
```

#### <a name="broadcast-a-plain-text-message-to-all-users"></a>Übertragen Sie eine Nur-Text-Nachricht an alle Benutzer

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll("Hi there!", { contentType: "text/plain" });
```

#### <a name="broadcast-a-binary-message-to-all-users"></a>Übertragen Sie eine binäre Nachricht an alle Benutzer

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

const payload = new Uint8Array(10);
await serviceClient.sendToAll(payload.buffer);
```

### <a name="troubleshooting"></a>Problembehandlung

Sie können die folgende Umgebungsvariable festlegen, um Debugprotokolle anzuzeigen, wenn Sie diese Bibliothek verwenden:

- Abrufen von Debugprotokollen aus der SignalR-Clientbibliothek

```bash
export AZURE_LOG_LEVEL=verbose
```

Ausführlichere Anweisungen zum Aktivieren von Protokollen finden Sie in der [Dokumentation zum Paket @azure/logger](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).

<a name="express"></a>

## <a name="azure-web-pubsub-cloudevents-handlers-for-express"></a>Azure Web PubSub CloudEvents-Handler für Express

Sie können die Expressbibliothek für Folgendes verwenden:
- Hinzufügen von Azure Web PubSub CloudEvents-Middleware zum Verarbeiten eingehender Clientereignisse
  - Anfragen von Missbrauchsüberprüfungen verarbeiten
  - Anfragen von den eingehenden Clientereignissen verarbeiten

[Quellcode](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub-express) |
[Paket (NPM)](https://www.npmjs.com/package/@azure/web-pubsub-express) |
[API-Referenzdokumentation](/javascript/api/@azure/web-pubsub-express/) |
[Produktdokumentation](./index.yml) |
[Beispiele][samples_ref]

### <a name="get-started"></a>Erste Schritte

Verwenden Sie [Node.js](https://nodejs.org/) ab Version 8.x.x oder [Express](https://expressjs.com/) ab Version 4.x.x. Stellen Sie außerdem sicher, dass die folgenden Voraussetzungen erfüllt sind:

- Ein [Azure-Abonnement][azure_sub].
- Ein vorhandener Azure Web PubSub-Endpunkt.

#### <a name="install-the-azureweb-pubsub-express-package"></a>Installieren Sie das Paket `@azure/web-pubsub-express`.

```bash
npm install @azure/web-pubsub-express
```

#### <a name="create-webpubsubeventhandler"></a>Erstellen Sie `WebPubSubEventHandler`.

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler(
  "chat",
  ["https://<yourAllowedService>.webpubsub.azure.com"],
  {
    handleConnect: (req, res) => {
      // auth the connection and set the userId of the connection
      res.success({
        userId: "<userId>"
      });
    }
  }
);

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

### <a name="key-concepts"></a>Wichtige Begriffe

- **Clientereignisse**: Ein Client erstellt Ereignisse während des Lebenszyklus einer Verbindung erstellt. Eine einfache WebSocket-Clientverbindung erstellt beispielsweise die folgenden Ereignisse:
  - Ein `connect`-Ereignis, wenn versucht wird, eine Verbindung mit dem Dienst herzustellen.
  - Ein `connected`-Ereignis, wenn erfolgreich eine Verbindung mit dem Dienst hergestellt wurde.
  - Ein `message`-Ereignis, wenn Nachrichten an den Dienst gesendet werden.
  - Ein `disconnected`-Ereignis, wenn die Verbindung mit demDienst getrennt wurde.

- **Ereignishandler:** Der Ereignishandler enthält die Logik zum Behandeln der Clientereignisse. Der Ereignishandler muss im Dienst über das Azure-Portal oder die Azure CLI im Voraus registriert und konfiguriert werden. Der Server hostet in der Regel die Ereignishandlerlogik.

### <a name="troubleshooting"></a>Problembehandlung

#### <a name="dump-request"></a>Dump-Anforderung

Legen Sie `dumpRequest` auf `true` fest, um die eingehenden Anforderungen anzuzeigen.

#### <a name="live-trace"></a>Live-Ablaufverfolgung

Verwenden Sie **Live Trace** aus dem Web PubSub-Dienstportal, um den Live-Datenverkehr anzuzeigen.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript


## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]