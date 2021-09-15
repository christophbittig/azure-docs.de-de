---
title: Schnellstart – Hinzufügen von VoIP-Telefonie zu einer Web-App mithilfe von Azure Communication Services
description: In diesem Tutorial erfahren Sie, wie Sie das Calling SDK von Azure Communication Services für JavaScript verwenden
author: ddematheu
ms.author: nimag
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 1f027dd3dfb812e9a700810972d2b019810dc41c
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251530"
---
In diesem Schnellstart erfahren Sie, wie Sie einen Anruf mithilfe des Calling SDK von Azure Communication Services für JavaScript beginnen.

## <a name="sample-code"></a>Beispielcode

Sie können die Beispiel-App von [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-voice-calling) herunterladen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/), Active LTS- und Maintenance LTS-Versionen (8.11.1 und 10.14.1 empfohlen)
- Eine aktive Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../../create-communication-resource.md).
- Ein Benutzerzugriffstoken, um den Anruf-Client zu instanziieren. Erfahren Sie mehr über das [Erstellen und Verwalten von Benutzerzugriffstoken](../../../access-tokens.md).

[!INCLUDE [Calling with JavaScript](./get-started-javascript-setup.md)]

Der Code lautet wie folgt:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="token-input"
      type="text"
      placeholder="User access token"
      style="margin-bottom:1em; width: 200px;"
    />
    </div>
    <button id="token-submit" type="button">
        Submit
    </button>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px; display: block;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Erstellen Sie im Stammverzeichnis Ihres Projekts eine Datei mit dem Namen **client.js**, die die Anwendungslogik für diese Schnellstartanleitung enthalten soll. Fügen Sie den folgenden Code hinzu, um den Client für Telefonie zu importieren und Verweise auf die DOM-Elemente abzurufen, damit wir unsere Geschäftslogik anfügen können. 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
let tokenCredential = "";
const userToken = document.getElementById("token-input");
const calleeInput = document.getElementById("callee-id-input");
const submitToken = document.getElementById("token-submit");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen befassen sich mit einigen der wichtigsten Features des Azure Communication Services Calling SDK:

| Name                             | Beschreibung                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | „CallClient“ ist der Haupteinstiegspunkt des Calling SDK.                                                                       |
| CallAgent                        | CallAgent dient zum Starten und Verwalten von Anrufen.                                                                                            |
| AzureCommunicationTokenCredential | Die AzureCommunicationTokenCredential-Klasse implementiert die CommunicationTokenCredential-Schnittstelle, die zum Instanziieren von CallAgent verwendet wird. |

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Sie müssen ein für Ihre Ressource gültiges Benutzerzugriffstoken in das Textfeld eingeben und auf „Senden“ klicken. Wenn Sie noch über kein Token verfügen, finden Sie in der Dokumentation zu [Benutzerzugriffstoken](../../../access-tokens.md) weitere Informationen. Initialisieren Sie mithilfe von `CallClient` eine `CallAgent`-Instanz mit einem `CommunicationTokenCredential`-Objekt, das es uns ermöglicht, Anrufe zu tätigen und zu empfangen. 

Fügen Sie den folgenden Code zu **client.js** hinzu.

```javascript
submitToken.addEventListener("click", async () => {
  const callClient = new CallClient(); 
  const userTokenCredential = userToken.value;
    try {
      tokenCredential = new AzureCommunicationTokenCredential(userTokenCredential);
      callAgent = await callClient.createCallAgent(tokenCredential);
      callButton.disabled = false;
      submitToken.disabled = true;
    } catch(error) {
      window.alert("Please submit a valid token!");
    }
})
```

## <a name="start-a-call"></a>Beginnen eines Anrufs

Fügen Sie einen Ereignishandler hinzu, um einen Anruf zu initiieren, wenn auf `callButton` geklickt wird:

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    // To call an ACS communication user, use {communicationUserId: 'ACS_USER_ID'}.
    // To call echobot, use {id: '8:echo123'}.
    call = callAgent.startCall(
        [{ communicationUserId: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>Beenden eines Anrufs

Fügen Sie einen Ereignislistener hinzu, um den aktuellen Anruf zu beenden, wenn auf `hangUpButton` geklickt wird:

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
  submitToken.disabled = false;
});
```

Die Eigenschaft `forEveryone` beendet den Anruf für alle Anrufteilnehmer.

## <a name="run-the-code"></a>Ausführen des Codes

Verwenden Sie `webpack-dev-server`, um Ihre App zu erstellen und auszuführen. Führen Sie den folgenden Befehl aus, um den Anwendungshost auf einem lokalen Webserver zu bündeln:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Navigieren Sie in Ihrem Browser zu http://localhost:8080/. Daraufhin sollte Folgendes angezeigt werden:

:::image type="content" source="../../media/javascript/calling-javascript-app-2.png" alt-text="Screenshot der fertigen JavaScript-Anwendung":::

Sie können einen ausgehenden VoIP-Anruf tätigen, indem Sie ein gültiges Benutzerzugriffstoken und eine Benutzer-ID in die entsprechenden Textfelder eingeben und auf die Schaltfläche **Start Call** (Anruf beginnen) klicken.

Wenn Sie `8:echo123` anrufen, werden Sie mit einem Echobot verbunden. Dies eignet sich hervorragend für die ersten Schritte und zum Überprüfen, ob Ihre Audiogeräte funktionieren. Übergeben Sie `{id: '8:echo123'}` an die API „CallAgent.startCall()“, um den Echobot aufzurufen.
Übergeben Sie zum Aufrufen eines ACS-Kommunikationsbenutzers `{communicationUserId: 'ACS_USER_ID'}` an die API `CallAgent.startCall()`.
