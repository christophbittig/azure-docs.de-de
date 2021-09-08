---
title: 'Referenz: Der Azure Web PubSub-Dienst für das JavaScript-SDK'
description: In der Referenz wird der Azure Web PubSub-Dienst für das JavaScript-SDK beschrieben
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 1d507f29b184403cb9ef31d84111af60f75c1584
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115253"
---
# <a name="javascript-sdk-for-the-azure-web-pubsub-service"></a>Der Azure Web PubSub-Dienst für das JavaScript-SDK

Es werden zwei Bibliotheken für JavaScript angeboten:
- Verwenden Sie den [Clientbibliothek-Dienst](#service-client-library) für die folgenden Aktionen
    - Das Versenden von Nachrichten an Hubs und Gruppen.
    - Das Versenden von Nachrichten an bestimmte Benutzer und Verbindungen.
    - Das Organisieren von Benutzern und Verbindungen in Gruppen.
    - Das Schließen von Verbindungen
    - Das Erteilen, Widerrufen und Prüfen von Berechtigungen für eine vorhandene Verbindung
- [Express-Middleware](#express) für die Verarbeitung eingehender Clientereignisse
  - Anfragen von Missbrauchsüberprüfungen verarbeiten
  - Anfragen von den eingehenden Clientereignissen verarbeiten

<a name="service-client-library"></a>

## <a name="azure-web-pubsub-service-client-library-for-javascript"></a>Die Clientbibliothek des Azure Web PubSub-Diensts für JavaScript
Verwenden Sie die Clientbibliothek für:

- Das Versenden von Nachrichten an Hubs und Gruppen.
- Das Versenden von Nachrichten an bestimmte Benutzer und Verbindungen.
- Das Organisieren von Benutzern und Verbindungen in Gruppen.
- Das Schließen von Verbindungen
- Das Erteilen, Widerrufen und Prüfen von Berechtigungen für eine vorhandene Verbindung

[Quellcode](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub) |
[Paket (NPM)](https://www.npmjs.com/package/@azure/web-pubsub) |
[API-Referenzdokumentation](/javascript/api/@azure/web-pubsub/) |
[Produktdokumentation](https://aka.ms/awps/doc) |
[Beispiele][samples_ref]

### <a name="getting-started"></a>Erste Schritte

#### <a name="currently-supported-environments"></a>Die derzeitig unterstützten Umgebungen

- [Node.js](https://nodejs.org/) Version 8.x.x oder höher

#### <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Abonnement][azure_sub].
- Eine vorhandene Azure Web PubSub-Dienstinstanz.

#### <a name="1-install-the-azureweb-pubsub-package"></a>1. Installieren Sie das Paket `@azure/web-pubsub`

```bash
npm install @azure/web-pubsub
```

#### <a name="2-create-and-authenticate-a-webpubsubserviceclient"></a>2. Erstellen und Authentifizieren Sie einen WebPubSubServiceClient

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
```

Sie können die `WebPubSubServiceClient` auch mithilfe eines Endpunkts und eines `AzureKeyCredential` authentifizieren:

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

#### <a name="enable-logs"></a>Aktivieren von Protokollen

Sie können die folgende Umgebungsvariable festlegen, um Debugprotokolle anzuzeigen, wenn Sie diese Bibliothek verwenden.

- Abrufen von Debugprotokollen aus der SignalR-Clientbibliothek

```bash
export AZURE_LOG_LEVEL=verbose
```

Ausführlichere Anweisungen zum Aktivieren von Protokollen finden Sie in der [@azure/logger Paketdokumentation](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).

<a name="express"></a>

## <a name="azure-web-pubsub-cloudevents-handlers-for-express"></a>Azure Web PubSub CloudEvents-Handler für Express

Verwenden Sie die Expressbibliothek für Folgendes:
- Hinzufügen von Azure Web PubSub CloudEvents-Middleware zum Verarbeiten eingehender Clientereignisse
  - Anfragen von Missbrauchsüberprüfungen verarbeiten
  - Anfragen von den eingehenden Clientereignissen verarbeiten

[Quellcode](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub-express) |
[Paket (NPM)](https://www.npmjs.com/package/@azure/web-pubsub-express) |
[API-Referenzdokumentation](/javascript/api/@azure/web-pubsub-express/) |
[Produktdokumentation](https://aka.ms/awps/doc) |
[Beispiele][samples_ref]

### <a name="getting-started"></a>Erste Schritte

#### <a name="currently-supported-environments"></a>Die derzeitig unterstützten Umgebungen

- [Node.js](https://nodejs.org/) Version 8.x.x oder höher
- [Express](https://expressjs.com/) Version 4.x.x oder höher

#### <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Abonnement][azure_sub].
- Ein vorhandener Azure Web PubSub-Endpunkt.

#### <a name="1-install-the-azureweb-pubsub-express-package"></a>1. Installieren Sie das `@azure/web-pubsub-express`-Paket

```bash
npm install @azure/web-pubsub-express
```

#### <a name="2-create-a-webpubsubeventhandler"></a>2. Erstellen Sie ein WebPubSubEventHandler

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

#### <a name="client-events"></a>Clientereignisse

Ereignisse werden während des Lebenszyklus einer Clientverbindung erstellt. Eine einfache WebSocket-Clientverbindung erstellt z. B. ein `connect`-Ereignis, wenn versucht wird, eine Verbindung mit dem Dienst herzustellen, ein `connected`-Ereignis, wenn erfolgreich eine Verbindung mit dem Dienst hergestellt wurde, ein `message`-Ereignis, wenn Nachrichten an den Dienst gesendet werden und ein `disconnected`-Ereignis, wenn die Verbindung mit dem Dienst getrennt wird.

#### <a name="event-handler"></a>Ereignishandler

Der Event-Handler enthält die Logik zum Behandeln der Clientereignisse. Der Ereignishandler muss im Dienst über das Portal oder die Azure CLI im Voraus registriert und konfiguriert werden. Der Ort zum Hosten der Ereignishandlerlogik wird im Allgemeinen als serverseitig betrachtet.

### <a name="troubleshooting"></a>Problembehandlung

#### <a name="dump-request"></a>Dump-Anforderung

Legen Sie `dumpRequest` auf `true` fest, um die eingehenden Anforderungen anzuzeigen.

#### <a name="live-trace"></a>Live-Ablaufverfolgung

Verwenden Sie **Live Trace** aus dem Web PubSub-Dienstportal, um den Live-Datenverkehr anzuzeigen.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript


## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]
