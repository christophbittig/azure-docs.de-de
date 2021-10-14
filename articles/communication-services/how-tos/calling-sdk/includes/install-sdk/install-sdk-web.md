---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: a671eb00f2108b96220e72a32f8dd9f5654a4224
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700122"
---
## <a name="install-the-sdk"></a>Installieren des SDK

Verwenden Sie den Befehl `npm install`, um das Azure Communication Services Calling SDK sowie allgemeine SDKs für JavaScript zu installieren.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

## <a name="initialize-required-objects"></a>Initialisieren erforderlicher Objekte

Eine CallClient-Instanz ist für die meisten Anrufvorgänge erforderlich. Lassen Sie uns eine neue `CallClient`-Instanz erstellen. Sie können sie mit benutzerdefinierten Optionen wie eine Protokollierungsinstanz konfigurieren.

Wenn Sie eine `CallClient`-Instanz verwenden, können Sie eine `CallAgent`-Instanz erstellen, indem Sie die Methode `createCallAgent` für die `CallClient`-Instanz aufrufen. Durch diese Methode wird ein `CallAgent`-Instanzobjekt asynchron zurückgegeben.

Die Methode `createCallAgent` verwendet `CommunicationTokenCredential` als Argument, welches ein [Benutzerzugriffstoken](../../../../quickstarts/access-tokens.md) akzeptiert.

Sie können die Methode `getDeviceManager` für die Instanz `CallClient` verwenden, um auf `deviceManager` zuzugreifen.

```js
const { CallClient } = require('@azure/communication-calling');
const { AzureCommunicationTokenCredential} = require('@azure/communication-common');
const { AzureLogger, setLogLevel } = require("@azure/logger");

// Set the logger's log level
setLogLevel('verbose');

// Redirect log output to wherever desired. To console, file, buffer, REST API, etc...
AzureLogger.log = (...args) => {
    console.log(...args); // Redirect log output to console
};

const userToken = '<USER_TOKEN>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```