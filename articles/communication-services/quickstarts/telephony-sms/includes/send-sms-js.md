---
title: Datei einfügen
description: include file
services: azure-communication-services
author: bertong
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: bertong
ms.openlocfilehash: c63a3859f9ea2fcec88ee220c0abca8429d9b86e
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132529927"
---
Steigen Sie ein in Azure Communication Services, indem Sie das JavaScript-SDK für SMS von Communication Services nutzen, um SMS-Nachrichten zu senden.

Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

> [!NOTE]
> Den fertigen Code für diesen Schnellstart finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/send-sms).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/): Active LTS- und Maintenance LTS-Versionen (8.11.1 und 10.14.1 empfohlen).
- Eine aktive Communication Services-Ressource und eine Verbindungszeichenfolge. [Erstellen Sie eine Communication Services-Ressource.](../../create-communication-resource.md)
- Eine für SMS-Nachrichten geeignete Telefonnummer. [Beziehen Sie eine Telefonnummer.](../get-phone-number.md)

### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

- Führen Sie in einem Terminal- oder Befehlsfenster `node --version` aus, um sich zu vergewissern, dass Node.js installiert ist.
- Melden Sie sich zum Anzeigen der Telefonnummern für Ihre Communication Services-Ressource beim [Azure-Portal](https://portal.azure.com/) an, suchen Sie nach Ihrer Communication Services-Ressource, und öffnen Sie im linken Navigationsbereich die Registerkarte **Telefonnummern**.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Öffnen Sie zunächst Ihr Terminal- oder Befehlsfenster, erstellen Sie ein neues Verzeichnis für Ihre App, und navigieren Sie zu diesem Verzeichnis.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Führen Sie `npm init -y` aus, um die Datei **package.json** mit den Standardeinstellungen zu erstellen.

```console
npm init -y
```

Verwenden Sie einen Text-Editor, um im Stammverzeichnis des Projekts die Datei **send-sms.js** zu erstellen. In den folgenden Abschnitten wird dieser Datei der gesamte Quellcode für diese Schnellstartanleitung hinzugefügt.

### <a name="install-the-package"></a>Installieren des Pakets

Verwenden Sie den Befehl `npm install`, um das Azure Communication Services SMS SDK für JavaScript zu installieren.

```console
npm install @azure/communication-sms --save
```

Durch die Option `--save` wird die Bibliothek als Abhängigkeit in der Datei **package.json** aufgeführt.

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen dienen zur Behandlung einiger der wichtigsten Features des SMS SDK für Node.js von Azure Communication Services.

| name                                  | Beschreibung                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Diese Klasse ist für alle SMS-Funktionen erforderlich. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Senden von SMS-Nachrichten. |
| SmsSendRequest | Bei dieser Schnittstelle handelt es sich um das Modell für die Erstellung der SMS-Anforderung (beispielsweise zum Konfigurieren der Ausgangs- und Zieltelefonnummer und des SMS-Inhalts). |
| SmsSendOptions | Diese Schnittstelle bietet Optionen zum Konfigurieren von Zustellberichten. Ist `enableDeliveryReport` auf `true` festgelegt, wird bei erfolgreicher Zustellung ein Ereignis ausgegeben. |
| SmsSendResult               | Diese Klasse enthält das Ergebnis des SMS-Diensts.                                          |

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Importieren Sie **SmsClient** aus dem SDK, und instanziieren Sie ihn mit Ihrer Verbindungszeichenfolge. Im folgenden Code wird die Verbindungszeichenfolge für die Ressource aus einer Umgebungsvariablen namens `COMMUNICATION_SERVICES_CONNECTION_STRING` abgerufen. Informationen zur [Verwaltung der Verbindungszeichenfolge Ihrer Ressource](../../create-communication-resource.md#store-your-connection-string).

Erstellen und öffnen Sie eine Datei mit dem Namen **send-sms.js**, und fügen Sie folgenden Code hinzu:

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-a-1n-sms-message"></a>Senden einer SMS: 1:N

Zum Senden einer SMS-Nachricht an eine Empfängerliste rufen Sie über SmsClient die Funktion `send` mit einer Liste der Telefonnummern der Empfänger auf. (Wenn Sie eine Nachricht an einen einzelnen Empfänger senden möchten, fügen Sie nur eine Telefonnummer in die Liste ein.) Fügen Sie am Ende von **send-sms.js** den folgenden Code hinzu:

```javascript
async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Hello World 👋🏻 via SMS"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```
Ersetzen Sie `<from-phone-number>` durch eine für SMS geeignete Telefonnummer, die Ihrer Communication Services-Ressource zugeordnet ist, und ersetzen Sie `<to-phone-number-1>` und `<to-phone-number-2>` durch die Telefonnummern, an die Sie eine Nachricht senden möchten.

> [!WARNING]
> Beachten Sie, dass Telefonnummern im internationalen Standardformat E.164 angegeben werden sollten (z. B. +14255550123). Die Telefonnummer vom Typ **From** (Anrufertelefonnummer) kann auch ein kurzer Code sein (z. B. 23456).

## <a name="send-a-1n-sms-message-with-options"></a>Senden einer SMS mit Optionen: 1:N

Sie können auch ein options-Objekt übergeben, um anzugeben, ob der Zustellbericht aktiviert werden soll, und um benutzerdefinierte Tags festzulegen.

```javascript

async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Weekly Promotion!"
  }, {
    //Optional parameters
    enableDeliveryReport: true,
    tag: "marketing"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```

Ersetzen Sie `<from-phone-number>` durch eine für SMS geeignete Telefonnummer, die Ihrer Communication Services-Ressource zugeordnet ist, und ersetzen Sie `<to-phone-number-1>` und `<to-phone-number-2>` durch die Telefonnummern, an die Sie eine Nachricht senden möchten.

> [!WARNING]
> Beachten Sie, dass Telefonnummern im internationalen Standardformat E.164 angegeben werden sollten (z. B. +14255550123). Die Telefonnummer vom Typ **From** (Anrufertelefonnummer) kann auch ein kurzer Code sein (z. B. 23456).

Der Parameter `enableDeliveryReport` ist ein optionaler Parameter zum Konfigurieren von Zustellberichten. Dies ist in Szenarien hilfreich, in denen Ereignisse ausgegeben werden sollen, wenn SMS-Nachrichten zugestellt wurden. Informationen zum Konfigurieren von Zustellberichten für SMS-Nachrichten finden Sie in der Schnellstartanleitung [Behandeln von SMS-Ereignissen](../handle-sms-events.md).
`tag` ist ein optionaler Parameter zum Anwenden eines Tags auf den Zustellbericht.

## <a name="run-the-code"></a>Ausführen des Codes

Verwenden Sie den Befehl `node`, um den Code auszuführen, den Sie der Datei **send-sms.js** hinzugefügt haben.

```console

node ./send-sms.js

```
