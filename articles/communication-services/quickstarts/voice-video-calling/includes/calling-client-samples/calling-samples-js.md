---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: rifox
ms.openlocfilehash: 1bba08e751afa5e3cd5c51401ca92e3b5bd9def1
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123453895"
---
## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../../create-communication-resource.md).
- Ein Benutzerzugriffstoken zum Aktivieren des Anrufclients. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Zugriffstoken](../../../access-tokens.md).
- Optional: Durchlaufen Sie den Schnellstart zum [Hinzufügen von Sprachanrufen zu Ihrer Anwendung](../../getting-started-with-calling.md).

## <a name="install-the-sdk"></a>Installieren des SDK

Verwenden Sie den Befehl `npm install`, um das Azure Communication Services Calling SDK sowie allgemeine SDKs für JavaScript zu installieren.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```
Das Communication Services Web Calling SDK muss über `https` verwendet werden. Für die lokale Entwicklung verwenden Sie `localhost` oder das lokale „file:“.

## <a name="documentation-support"></a>Unterstützung der Dokumentation
- [Versionshinweise](https://github.com/Azure/Communication/blob/master/releasenotes/acs-javascript-calling-library-release-notes.md)
- [Übermitteln von Problemen/Fehlern auf GitHub](https://github.com/Azure/Communication/issues)
- [Beispielanwendungen](../../../../samples/overview.md)
- [API-Referenz](/javascript/api/azure-communication-services/@azure/communication-calling/?preserve-view=true&view=azure-communication-services-js)

## <a name="models"></a>Modelle
### <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen befassen sich mit einigen der wichtigsten Features des Azure Communication Services Calling SDK:

| Name                                | BESCHREIBUNG                                                                                                                              |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                        | Der Haupteinstiegspunkt des Calling SDK.                                                                                                 |
| `AzureCommunicationTokenCredential` | Implementiert die `CommunicationTokenCredential`-Schnittstelle zum Instanziieren von `callAgent`.                                       |
| `CallAgent`                         | Dient zum Starten und Verwalten von Anrufen.                                                                                                          |
| `DeviceManager`                     | Dient zum Verwalten von Mediengeräten.                                                                                                            |
| `Call`                              | Dient zum Darstellen eines Anrufs.                                                                                                              |
| `LocalVideoStream`                  | Dient zum Erstellen eines lokalen Videodatenstroms für ein Kameragerät auf dem lokalen System.                                                          |
| `RemoteParticipant`                 | Dient zum Darstellen eines Remoteteilnehmers für den Anruf.                                                                                   |
| `RemoteVideoStream`                 | Dient zum Darstellen eines Remotevideodatenstroms von einem Remoteteilnehmer.                                                                  |

> [!NOTE]
> Die Objektinstanzen des Calling SDK sollten nicht als einfache JavaScript-Objekte betrachtet werden. Diese sind tatsächliche Instanzen verschiedener Klassen und können daher nicht serialisiert werden.

### <a name="events-model"></a>Ereignismodell
Jedes Objekt im Calling SDK verfügt über Eigenschaften und Sammlungen, deren Werte sich während der Lebensdauer des Objekts ändern.
Verwenden Sie die Methode `on()`, um Ereignisse von Objekten zu abonnieren, und verwenden Sie die Methode `off()`, um Ereignisse nicht mehr zu abonnieren.

#### <a name="properties"></a>Eigenschaften
- Sie müssen ihre Anfangswerte überprüfen und das Ereignis `'<property>Changed'` für zukünftige Wertaktualisierungen abonnieren.

#### <a name="collections"></a>Auflistungen
- Sie müssen ihre Anfangswerte überprüfen und das Ereignis `'<collection>Updated'` für zukünftige Wertaktualisierungen abonnieren.
- Die Nutzdaten des Ereignisses `'<collection>Updated'` enthalten ein `added`-Array, das Werte enthält, die der Auflistung hinzugefügt wurden.
- Die Nutzdaten des Ereignisses `'<collection>Updated'` enthalten auch ein `removed`-Array, das Werte enthält, die aus der Auflistung entfernt wurden.

```js
/*************************************
 * Example code - client.js          *
 * Convert this script into a        *
 * bundle.js that your html index    *
 * page can use.                     *
 *************************************/

// Make sure to install the necessary dependencies
const { CallClient, VideoStreamRenderer, LocalVideoStream } = require('@azure/communication-calling');
const { AzureCommunicationTokenCredential } = require('@azure/communication-common');
const { AzureLogger, setLogLevel } = require("@azure/logger");
// Set the log level and output
setLogLevel('verbose');
AzureLogger.log = (...args) => {
    console.log(...args);
};

// Calling web sdk objects
let callAgent;
let deviceManager;
let call;
let incomingCall;
let localVideoStream;
let localVideoStreamRenderer;

// UI widgets
let userAccessToken = document.getElementById('user-access-token');
let calleeAcsUserId = document.getElementById('callee-acs-user-id');
let initializeCallAgentButton = document.getElementById('initialize-call-agent');
let startCallButton = document.getElementById('start-call-button');
let acceptCallButton = document.getElementById('accept-call-button');
let startVideoButton = document.getElementById('start-video-button');
let stopVideoButton = document.getElementById('stop-video-button');
let connectedLabel = document.getElementById('connectedLabel');
let remoteVideoContainer = document.getElementById('remoteVideoContainer');
let localVideoContainer = document.getElementById('localVideoContainer');

// Instantiate the Call Agent.
initializeCallAgentButton.onclick = async () => {
    try {
        const callClient = new CallClient(); 
        tokenCredential = new AzureCommunicationTokenCredential(userAccessToken.value.trim());
        callAgent = await callClient.createCallAgent(tokenCredential)
        // Set up a camera device to use.
        deviceManager = await callClient.getDeviceManager();
        await deviceManager.askDevicePermission({ video: true });
        await deviceManager.askDevicePermission({ audio: true });
        // Listen for an incoming call to accept.
        callAgent.on('incomingCall', async (args) => {
            try {
                incomingCall = args.incomingCall;
                acceptCallButton.disabled = false;
                startCallButton.disabled = true;
            } catch (error) {
                console.error(error);
            }
        });

        startCallButton.disabled = false;
        initializeCallAgentButton.disabled = true;
    } catch(error) {
        console.error(error);
    }
}

// Start an out-going call.
startCallButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        const videoOptions = localVideoStream ? { localVideoStreams: [localVideoStream] } : undefined;
        call = callAgent.startCall([{ communicationUserId: calleeAcsUserId.value.trim() }], { videoOptions });
        // Subscribe to the call's properties and events.
        subscribeToCall(call);
    } catch (error) {
        console.error(error);
    }
}

// Accept the incoming call.
acceptCallButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        const videoOptions = localVideoStream ? { localVideoStreams: [localVideoStream] } : undefined;
        call = await incomingCall.accept({ videoOptions });
        // Subscribe to the call's properties and events.
        subscribeToCall(call);
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a call obj.
// Listen for property changes and collection udpates.
subscribeToCall = (call) => {
    try {
        // Inspect the initial call.id value.
        console.log(`Call Id: ${call.id}`);
        //Subsribe to call's 'idChanged' event for value changes.
        call.on('idChanged', () => {
            console.log(`Call Id changed: ${call.id}`); 
        });

        // Inspect the initial call.state value.
        console.log(`Call state: ${call.state}`);
        // Subscribe to call's 'stateChanged' event for value changes.
        call.on('stateChanged', async () => {
            console.log(`Call state changed: ${call.state}`);
            if(call.state === 'Connected') {
                connectedLabel.hidden = false;
                acceptCallButton.disabled = true;
                startCallButton.disabled = true;
                startVideoButton.disabled = false;
                stopVideoButton.disabled = false
            } else if (call.state === 'Disconnected') {
                connectedLabel.hidden = true;
                startCallButton.disabled = false;
                console.log(`Call ended, call end reason={code=${call.callEndReason.code}, subCode=${call.callEndReason.subCode}}`);
            }   
        });

        call.localVideoStreams.forEach(async (lvs) => {
            localVideoStream = lvs;
            await displayLocalVideoStream();
        });
        call.on('localVideoStreamsUpdated', e => {
            e.added.forEach(async (lvs) => {
                localVideoStream = lvs;
                await displayLocalVideoStream();
            });
            e.removed.forEach(lvs => {
               removeLocalVideoStream();
            });
        });
        
        // Inspect the call's current remote participants and subscribe to them.
        call.remoteParticipants.forEach(remoteParticipant => {
            subscribeToRemoteParticipant(remoteParticipant);
        })
        // Subscribe to the call's 'remoteParticipantsUpdated' event to be
        // notified when new participants are added to the call or removed from the call.
        call.on('remoteParticipantsUpdated', e => {
            // Subscribe to new remote participants that are added to the call.
            e.added.forEach(remoteParticipant => {
                subscribeToRemoteParticipant(remoteParticipant)
            });
            // Unsubscribe from participants that are removed from the call
            e.removed.forEach(remoteParticipant => {
                console.log('Remote participant removed from the call.');
            })
        });
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a remote participant obj.
// Listen for property changes and collection udpates.
subscribeToRemoteParticipant = (remoteParticipant) => {
    try {
        // Inspect the initial remoteParticipant.state value.
        console.log(`Remote participant state: ${remoteParticipant.state}`);
        // Subscribe to remoteParticipant's 'stateChanged' event for value changes.
        remoteParticipant.on('stateChanged', () => {
            console.log(`Remote participant state changed: ${remoteParticipant.state}`);
        });

        // Inspect the remoteParticipants's current videoStreams and subscribe to them.
        remoteParticipant.videoStreams.forEach(remoteVideoStream => {
            subscribeToRemoteVideoStream(remoteVideoStream)
        });
        // Subscribe to the remoteParticipant's 'videoStreamsUpdated' event to be
        // notified when the remoteParticiapant adds new videoStreams and removes video streams.
        remoteParticipant.on('videoStreamsUpdated', e => {
            // Subscribe to new remote participant's video streams that were added.
            e.added.forEach(remoteVideoStream => {
                subscribeToRemoteVideoStream(remoteVideoStream)
            });
            // Unsubscribe from remote participant's video streams that were removed.
            e.removed.forEach(remoteVideoStream => {
                console.log('Remote participant video stream was removed.');
            })
        });
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a remote participant's remote video stream obj.
// Listen for property changes and collection udpates.
// When their remote video streams become available, display them in the UI.
subscribeToRemoteVideoStream = async (remoteVideoStream) => {
    // Create a video stream renderer for the remote video stream.
    let videoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    let view;
    const renderVideo = async () => {
        try {
            // Create a renderer view for the remote video stream.
            view = await videoStreamRenderer.createView();
            // Attach the renderer view to the UI.
            remoteVideoContainer.hidden = false;
            remoteVideoContainer.appendChild(view.target);
        } catch (e) {
            console.warn(`Failed to createView, reason=${e.message}, code=${e.code}`);
        }   
    }
    
    remoteVideoStream.on('isAvailableChanged', async () => {
        // Participant has switched video on.
        if (remoteVideoStream.isAvailable) {
            await renderVideo();

        // Participant has switched video off.
        } else {
            if (view) {
                view.dispose();
                view = undefined;
            }
        }
    });

    // Participant has video on initially.
    if (remoteVideoStream.isAvailable) {
        await renderVideo();
    }
}

// Start your local video stream.
// This will send your local video stream to remote participants so they can view it.
startVideoButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        await call.startVideo(localVideoStream);
    } catch (error) {
        console.error(error);
    }
}

// Stop your local video stream.
// This will stop your local video stream from being sent to remote participants.
stopVideoButton.onclick = async () => {
    try {
        await call.stopVideo(localVideoStream);
    } catch (error) {
        console.error(error);
    }
}

// Create a local video stream for your camera device
createLocalVideoStream = async () => {
    const camera = (await deviceManager.getCameras())[0];
    if (camera) {
        return new LocalVideoStream(camera);
    } else {
        console.error(`No camera device found on the system`);
    }
}

// Display your local video stream preview in your UI
displayLocalVideoStream = async () => {
    try {
        localVideoStreamRenderer = new VideoStreamRenderer(localVideoStream);
        const view = await localVideoStreamRenderer.createView();
        localVideoContainer.hidden = false;
        localVideoContainer.appendChild(view.target);
    } catch (error) {
        console.error(error);
    } 
}

// Remove your local video stream preview from your UI
removeLocalVideoStream = async() => {
    try {
        localVideoStreamRenderer.dispose();
        localVideoContainer.hidden = true;
    } catch (error) {
        console.error(error);
    } 
}
```

Ein HTML-Beispielcode, der ein aus dem obigen JavaScript-Beispiel (`client.js`) generiertes Bundle verwenden kann.
```html
<!-- index.html -->
<!DOCTYPE html>
<html>
    <head>
        <title>Azure Communication Services - Calling Web SDK</title>
    </head>
    <body>
        <h4>Azure Communication Services - Calling Web SDK</h4>
        <input id="user-access-token"
            type="text"
            placeholder="User access token"
            style="margin-bottom:1em; width: 500px;"/>
        <button id="initialize-call-agent" type="button">Initialize Call Agent</button>
        <br>
        <br>
        <input id="callee-acs-user-id"
            type="text"
            placeholder="Enter callee's ACS user identity in format: '8:acs:resourceId_userId'"
            style="margin-bottom:1em; width: 500px; display: block;"/>
        <button id="start-call-button" type="button" disabled="true">Start Call</button>
        <button id="accept-call-button" type="button" disabled="true">Accept Call</button>
        <button id="start-video-button" type="button" disabled="true">Start Video</button>
        <button id="stop-video-button" type="button" disabled="true">Stop Video</button>
        <br>
        <br>
        <div id="connectedLabel" style="color: #13bb13;" hidden>Call is connected!</div>
        <br>
        <div id="remoteVideoContainer" style="width: 40%;" hidden>Remote participants' video streams:</div>
        <br>
        <div id="localVideoContainer" style="width: 30%;" hidden>Local video stream:</div>
        <!-- points to the bundle generated from client.js -->
        <script src="./bundle.js"></script>
    </body>
</html>
```
Beachten Sie, dass Sie nach dem Starten eines Anrufs, dem Beitreten zu einem Anruf oder dem Annehmen eines Anrufs auch das Ereignis „callsUpdated“ des „callAgent“ verwenden können, um über das neue Call-Objekt informiert zu werden und es zu abonnieren.
```js
callAgent.on('callsUpdated', e => {
    // New Call object is added to callAgent.calls collection
    e.added.forEach(call => {
        call.remoteParticipants.forEach(remoteParticipant => {
            subscribeToRemoteParticipant(remoteParticipant);
        })
        call.on('remoteParticipantsUpdated', e => {
            e.added.forEach(remoteParticipant => {
                subscribeToRemoteParticipant(remoteParticipant)
            });
            e.removed.forEach(remoteParticipant => {
                console.log('Remote participant removed from the call.');
            })
        });
    })
})
```

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>Initialisieren einer CallClient-Instanz, Erstellen einer CallAgent-Instanz und Zugreifen auf deviceManager

Erstellen Sie eine neue `CallClient`-Instanz. Sie können sie mit benutzerdefinierten Optionen wie eine Protokollierungsinstanz konfigurieren.

Wenn Sie eine `CallClient`-Instanz verwenden, können Sie eine `CallAgent`-Instanz erstellen, indem Sie die Methode `createCallAgent` für die `CallClient`-Instanz aufrufen. Durch diese Methode wird ein `CallAgent`-Instanzobjekt asynchron zurückgegeben.

Die Methode `createCallAgent` verwendet `CommunicationTokenCredential` als Argument, welches ein [Benutzerzugriffstoken](../../../access-tokens.md) akzeptiert.

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

## <a name="place-a-call"></a>Tätigen eines Anrufs

Um einen Anruf zu erstellen und zu starten, verwenden Sie eine der APIs für `callAgent`, und geben Sie einen Benutzer an, den Sie über das Communication Services Identity SDK erstellt haben.

Erstellung und Start des Anrufs erfolgen synchron. Die `call`-Instanz ermöglicht Ihnen das Abonnieren von Anrufereignissen.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Tätigen eines 1:n-Anrufs an einen Benutzer oder ein Telefonfestnetz

Um einen anderen Communication Services-Benutzer anzurufen, verwenden Sie die Methode `startCall` für `callAgent`, und übergeben Sie den `CommunicationUserIdentifier`, den Sie [mit der Communication Services-Verwaltungsbibliothek](../../../access-tokens.md) erstellt haben.

Verwenden Sie für einen 1:1-Anruf bei einem Benutzer den folgenden Code:

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Um einen Anruf an ein PSTN (Public Switched Telephone Network) zu tätigen, verwenden Sie die Methode `startCall` für `callAgent`, und übergeben Sie den `PhoneNumberIdentifier` des Empfängers. Die Communication Services-Ressource muss so konfiguriert werden, dass Anrufe über das Telefonfestnetz möglich sind.

Wenn Sie eine Telefonfestnetznummer anrufen, geben Sie Ihre alternative Anrufer-ID an. Eine alternative Anrufer-ID bezieht sich auf eine Telefonnummer (basierend auf dem E.164-Standard), die den Anrufer in einem PSTN-Anruf identifiziert. Dies ist die Telefonnummer, die dem Anrufempfänger bei einem eingehenden Anruf angezeigt wird.

> [!NOTE]
> Anrufe über das Telefonfestnetz sind derzeit in der Phase „Private Vorschau“. Um auf diese Funktion zuzugreifen, [bewerben Sie sich für das Early Adopter-Programm](https://aka.ms/ACS-EarlyAdopter).

Verwenden Sie für einen 1:1-Anruf einer PSTN-Nummer den folgenden Code:
```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

Verwenden Sie für einen 1:N-Aufruf eines Benutzers und einer PSTN-Nummer den folgenden Code:

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const pstnCallee = { phoneNumber: '<PHONE_NUMBER>'};
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Tätigen eines 1:1-Anrufs mit Videokamera

> [!IMPORTANT]
> Derzeit ist nicht mehr als ein ausgehender lokaler Videostream möglich.

Zum Tätigen eines Videoanrufs müssen Sie lokale Kameras mithilfe der Methode `getCameras()` in `deviceManager` aufzählen.

Nachdem Sie eine Kamera ausgewählt haben, verwenden Sie sie, um eine `LocalVideoStream`-Instanz zu erstellen. Übergeben Sie sie in `videoOptions` als Element innerhalb des `localVideoStream`-Arrays an die `startCall`-Methode.

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const call = callAgent.startCall([userCallee], placeCallOptions);
```

- Sobald die Anrufverbindung hergestellt ist, wird automatisch ein Videostream von der ausgewählten Kamera an den anderen Teilnehmer gesendet. Dies gilt auch für die `Call.Accept()`- und die `CallAgent.join()`-Videooptionen.


### <a name="join-a-group-call"></a>Teilnehmen an einem Gruppenanruf

> [!NOTE]
> Der `groupId`-Parameter wird als Systemmetadaten angesehen und kann von Microsoft für Vorgänge verwendet werden, die zum Ausführen des Systems erforderlich sind. Fügen Sie keine personenbezogenen Daten in den `groupId`-Wert ein. Microsoft behandelt diesen Parameter nicht wie personenbezogene Daten. Seine Inhalte sind möglicherweise für Microsoft-Mitarbeiter sichtbar oder werden langfristig gespeichert.
>
> Der `groupId`-Parameter erfordert, dass Daten im GUID-Format vorliegen. Es wird empfohlen, zufällig generierte GUIDs zu verwenden, die in Ihren Systemen nicht als personenbezogene Daten betrachtet werden.
>

Um einen neuen Gruppenanruf zu starten oder an einem aktuellen Gruppenanruf teilzunehmen, müssen Sie die Methode `join` aufrufen und ein Objekt mit einer `groupId`-Eigenschaft übergeben. Der `groupId`-Wert muss eine GUID sein.

```js

const context = { groupId: '<GUID>'}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Teilnahme an einer Teams-Besprechung
> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Betarelease des ACS Calling Web SDK.

Verwenden Sie für die Teilnahme an einer Teams-Besprechung die Methode `join`, und übergeben Sie einen Besprechungslink oder die Daten einer Besprechung.

Teilnehmen über einen Besprechungslink:

```js
const locator = { meetingLink: '<MEETING_LINK>'}
const call = callAgent.join(locator);
```

Teilnehmen über Besprechungsdaten:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>Empfangen eines eingehenden Anrufs

Die `callAgent`-Instanz gibt das Ereignis `incomingCall` aus, wenn die angemeldete Identität einen eingehenden Anruf empfängt. Um auf dieses Ereignis zu lauschen, abonnieren Sie es mithilfe einer der folgenden Optionen:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    const incomingCall = args.incomingCall; 

    // Get incoming call ID
    var incomingCallId = incomingCall.id

    // Get information about this Call. This API is provided as a preview for developers
    // and may change based on feedback that we receive. Do not use this API in a production environment.
    // To use this api please use 'beta' release of ACS Calling Web SDK
    var callInfo = incomingCall.info;

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

Das `incomingCall`-Ereignis umfasst eine `incomingCall`-Instanz, die Sie akzeptieren oder ablehnen können.

Wenn das angegebene Videokameragerät von einem anderen Prozess verwendet wird oder im System deaktiviert ist, wird der Anruf mit deaktiviertem Video gestartet und eine Anrufdiagnose „cameraStartFailed: true“ ausgelöst, wenn ein Anruf mit aktiviertem Video gestartet/beigetreten/angenommen wird.

Informationen zum Behandeln dieser Anrufdiagnose finden Sie im Abschnitt zur Anrufdiagnose.

## <a name="manage-calls"></a>Verwalten von Anrufen

Während eines Anrufs können Sie auf Anrufeigenschaften zugreifen und Video- und Audioeinstellungen verwalten.

### <a name="check-call-properties"></a>Überprüfen von Anrufeigenschaften

Abrufen der eindeutigen ID (Zeichenfolge) für einen Anruf:

   ```js
    const callId: string = call.id;
   ```
Abrufen von Informationen zum Anruf:
> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Betarelease des ACS Calling Web SDK.
   ```js
   const callInfo = call.info;
   ```

Erhalten Sie Informationen zu anderen Anrufteilnehmern, indem Sie sich die `remoteParticipants`-Sammlung für die call-Instanz ansehen:

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

Identifizieren des Anrufers eines eingehenden Anrufs:

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` ist einer der `CommunicationIdentifier`-Typen.

Abrufen des Anrufzustands:

   ```js
   const callState = call.state;
   ```

   Gibt eine Zeichenfolge zurück, die den aktuellen Zustand eines Anrufs darstellt:

  - `None`: anfänglicher Anrufzustand.
  - `Connecting`: anfänglicher Übergangszustand, wenn ein Anruf getätigt oder angenommen wird.
  - `Ringing`: Weist bei einem ausgehenden Anruf darauf hin, dass für den Anruf Remoteteilnehmer ein Klingeln ertönt. Auf deren Seite lautet der Status `Incoming`.
  - `EarlyMedia`: Gibt einen Zustand an, in dem vor dem Verbinden des Anrufs eine Ansage wiedergegeben wird.
  - `Connected`: Gibt an, dass der Anruf verbunden wurde.
  - `LocalHold`: Gibt an, dass der Anruf von einem lokalen Teilnehmer gehalten wird. Zwischen dem lokalen Endpunkt und den Remoteteilnehmern werden keine Medien übertragen.
  - `RemoteHold`: Gibt an, dass der Anruf von einem Remoteteilnehmer gehalten wird. Zwischen dem lokalen Endpunkt und den Remoteteilnehmern werden keine Medien übertragen.
  - `InLobby`: Gibt an, dass sich der Benutzer im Wartebereich befindet.
  - `Disconnecting`: Übergangszustand, bevor der Anruf in einen `Disconnected`-Zustand wechselt.
  - `Disconnected`: Abschließender Anrufzustand. Wenn die Netzwerkverbindung unterbrochen wird, ändert sich der Zustand nach zwei Minuten in `Disconnected`.

Ermitteln Sie, warum der Anruf beendet wurde, indem Sie die Eigenschaft `callEndReason` überprüfen:

   ```js
   const callEndReason = call.callEndReason;
   const callEndReasonCode = callEndReason.code // (number) code associated with the reason
   const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
   ```

Erfahren Sie, ob der aktuelle Anruf ein- oder ausgeht, indem Sie die Eigenschaft `direction` überprüfen. Er gibt `CallDirection` zurück.

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

Stellen Sie fest, ob das aktuelle Mikrofon stummgeschaltet ist. Er gibt `Boolean` zurück.

   ```js
   const muted = call.isMuted;
   ```

Ermitteln Sie, ob der Bildschirmübertragungsstream von einem bestimmten Endpunkt aus gesendet wird, indem Sie die Eigenschaft `isScreenSharingOn` überprüfen. Er gibt `Boolean` zurück.

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

Untersuchen Sie aktive Videostreams, indem Sie sich die `localVideoStreams`-Sammlung ansehen. Sie gibt `LocalVideoStream`-Objekte zurück.

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```




### <a name="mute-and-unmute"></a>Stummschalten und Aufheben der Stummschaltung

Zum Stummschalten oder Aufheben der Stummschaltung des lokalen Endpunkts können Sie die asynchronen APIs `mute` und `unmute` verwenden:

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Starten und Beenden des Sendens von lokalem Video

Um ein Video zu starten, müssen Sie Kameras mit der `getCameras`-Methode für das `deviceManager`-Objekt aufzählen. Erstellen Sie dann eine neue Instanz von `LocalVideoStream` mit der gewünschten Kamera, und übergeben Sie anschließend das Objekt `LocalVideoStream` an die Methode `startVideo`:

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

Sobald Videodaten erfolgreich gesendet werden, wird eine `LocalVideoStream`-Instanz der `localVideoStreams`-Sammlung für eine Anrufinstanz hinzugefügt.

```js
call.localVideoStreams[0] === localVideoStream;
```

Um das lokale Video anzuhalten, übergeben Sie die `localVideoStream`-Instanz, die in der `localVideoStreams`-Sammlung verfügbar ist:

```js
await call.stopVideo(localVideoStream);
// or
await call.stopVideo(call.localVideoStreams[0]);
```
Beachten Sie, dass es vier APIs gibt, in denen Sie eine localVideoStream-Instanz übergeben können, um ein Video in einem Anruf zu starten: callAgent.startCall(), callAgent.join(), call.accept() und call.startVideo(). An die call.stopVideo()-API müssen Sie dieselbe localVideoStream-Instanz übergeben, die Sie auch in diesen vier APIs übergeben haben.

Sie können während des Sendens von Videodaten auf ein anderes Kameragerät umschalten, indem Sie `switchSource` für eine `localVideoStream`-Instanz aufrufen:

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

Wenn das angegebene Videogerät von einem anderen Prozess verwendet wird oder es im System deaktiviert ist:
- Wenn während eines Anrufs das Video deaktiviert ist und Sie das Video mit der API „call.startVideo()“ starten, gibt diese API einen „SourceUnavailableError“ aus, und es wird eine Anrufdiagnose „cameraStartFiled: true“ ausgelöst.
- Ein Aufruf der API „localVideoStream.switchSource()“ löst die Anrufdiagnose „cameraStartFailed: true“ aus.
Informationen zum Behandeln der Anrufdiagnose finden Sie im Abschnitt zur Anrufdiagnose.

## <a name="manage-remote-participants"></a>Verwalten von Remoteteilnehmern

Alle Remoteteilnehmer werden durch den Typ `RemoteParticipant` dargestellt und sind über die `remoteParticipants`-Sammlung für eine Anrufinstanz verfügbar.

### <a name="list-the-participants-in-a-call"></a>Auflisten der Teilnehmer an einem Anruf

Die `remoteParticipants`-Sammlung gibt eine Liste der Remoteteilnehmer eines Anrufs zurück:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>Zugreifen auf Eigenschaften von Remoteteilnehmern

Remoteteilnehmern ist ein Satz von Eigenschaften und Sammlungen zugeordnet:

- `CommunicationIdentifier`: Ruft den Bezeichner eines Remoteteilnehmers ab. „Identity“ ist einer der `CommunicationIdentifier`-Typen:

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  Mögliche `CommunicationIdentifier`-Typen:

  - `{ communicationUserId: '<ACS_USER_ID'> }`: Objekt, das den ACS-Benutzer darstellt.
  - `{ phoneNumber: '<E.164>' }`: Objekt, das die Telefonnummer im E.164-Format darstellt.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: Objekt, das den Teams-Benutzer darstellt.
  - `{ id: string }`: Objekt, das den Bezeichner darstellt, der keinem der anderen Bezeichnertypen entspricht.

- `state`: Ruft den Zustand eines Remoteteilnehmers ab.

  ```js
  const state = remoteParticipant.state;
  ```

  Mögliche Zustände:

  - `Idle`: Anfangszustand.
  - `Connecting`: Übergangszustand, während sich der Teilnehmer mit dem Anruf verbindet.
  - `Ringing`: Für den Teilnehmeranruf ertönt ein Klingeln.
  - `Connected`: Der Teilnehmer ist mit dem Anruf verbunden.
  - `Hold`: Der Teilnehmer wird gehalten.
  - `EarlyMedia`: Ansage, die vor dem Verbinden eines Teilnehmers mit dem Anruf wiedergegeben wird.
  - `InLobby`: Gibt an, dass sich der Remoteteilnehmer im Wartebereich befindet.
  - `Disconnected`: Abschließender Zustand. Der Teilnehmer wird vom Anruf getrennt. Wenn der Remoteteilnehmer seine Netzwerkkonnektivität verliert, ändert sich der Zustand nach zwei Minuten in `Disconnected`.

- `callEndReason`: Um zu erfahren, warum ein Teilnehmer den Anruf verlassen hat, prüfen Sie die Eigenschaft `callEndReason`:

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  const callEndReasonCode = callEndReason.code // (number) code associated with the reason
  const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
  ```

- `isMuted`-Status: Um herauszufinden, ob ein Remoteteilnehmer stummgeschaltet wurde, überprüfen Sie die Eigenschaft `isMuted`. Er gibt `Boolean` zurück.

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking`-Status: Um herauszufinden, ob ein Remoteteilnehmer gerade spricht, überprüfen Sie die Eigenschaft `isSpeaking`. Er gibt `Boolean` zurück.

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`: Um alle Videostreams zu prüfen, die ein bestimmter Teilnehmer im Rahmen dieses Anrufs sendet, sehen Sie sich die `videoStreams`-Sammlung an. Sie enthält `RemoteVideoStream`-Objekte.

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```
- `displayName`: Um den Anzeigenamen für diesen Remoteteilnehmer zu erhalten, überprüfen Sie die zurückgegebene Zeichenfolge der Eigenschaft `displayName`. 

  ```js
  const displayName = remoteParticipant.displayName;
  ```

### <a name="add-a-participant-to-a-call"></a>Hinzufügen eines Teilnehmers zu einem Anruf

Wenn Sie einem Anruf einen Teilnehmer (einen Benutzer oder eine Telefonnummer) hinzufügen möchten, können Sie `addParticipant` verwenden. Geben Sie einen der `Identifier`-Typen an. Dadurch wird die Instanz `remoteParticipant` synchron zurückgegeben. Das Ereignis `remoteParticipantsUpdated` des Anrufs wird ausgelöst, wenn ein Teilnehmer erfolgreich dem Aufruf hinzugefügt wurde.

```js
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<ALTERNATE_CALLER_ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Entfernen eines Teilnehmers aus einem Anruf

Um einen Teilnehmer (einen Benutzer oder eine Telefonnummer) aus einem Anruf zu entfernen, können Sie `removeParticipant` aufrufen. Sie müssen einen der `Identifier`-Typen übergeben. Diese Methode wird asynchron aufgelöst, nachdem der Teilnehmer aus dem Anruf entfernt wurde. Der Teilnehmer wird auch aus der `remoteParticipants`-Sammlung entfernt.

```js
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Rendern von Videostreams von Remoteteilnehmern

Um die Video- und Bildschirmübertragungs-Streams der Remoteteilnehmer aufzulisten, sehen Sie sich die `videoStreams`-Sammlungen an:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

Zum Rendern von `RemoteVideoStream` muss das zugehörige Ereignis vom Typ `isAvailableChanged` abonniert werden. Wenn sich die Eigenschaft `isAvailable` in `true` ändert, sendet ein Remoteteilnehmer einen Stream. Erstellen Sie daraufhin eine neue Instanz von `VideoStreamRenderer` und anschließend mit der asynchronen Methode `createView` eine neue `VideoStreamRendererView`-Instanz.  Sie können dann `view.target` an ein beliebiges Benutzeroberflächenelement anfügen.

Immer wenn sich die Verfügbarkeit eines Remotestreams ändert, können Sie wählen, ob Sie den gesamten `VideoStreamRenderer`, eine bestimmte `VideoStreamRendererView` zerstören oder sie behalten möchten, was jedoch zur Anzeige leerer Videobilder führt.

```js
subscribeToRemoteVideoStream = async (remoteVideoStream) => {
    // Create a video stream renderer for the remote video stream.
    let videoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    let view;
    const remoteVideoContainer = document.getElementById('remoteVideoContainer');
    const renderVideo = async () => {
        try {
            // Create a renderer view for the remote video stream.
            view = await videoStreamRenderer.createView();
            // Attach the renderer view to the UI.
            remoteVideoContainer.appendChild(view.target);
        } catch (e) {
            console.warn(`Failed to createView, reason=${e.message}, code=${e.code}`);
        }   
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        // Participant has switched video on.
        if (remoteVideoStream.isAvailable) {
            await renderVideo();

        // Participant has switched video off.
        } else {
            if (view) {
                view.dispose();
                view = undefined;
            }
        }
    });

    // Participant has video on initially.
    if (remoteVideoStream.isAvailable) {
        await renderVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Eigenschaften von Remotevideostreams

Für Remotevideostreams gibt es die folgenden Eigenschaften:

- `id`: ID eines Remotevideostreams.

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `mediaStreamType`: Dies kann `Video` oder `ScreenSharing` sein.

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: Gibt an, ob der Endpunkt des Remoteteilnehmers aktiv einen Stream sendet.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="videostreamrenderer-methods-and-properties"></a>Methoden und Eigenschaften von VideoStreamRenderer
Erstellen Sie eine `VideoStreamRendererView`-Instanz, die in der Benutzeroberfläche der Anwendung angefügt werden kann, um den Remotevideostream zu rendern. Verwenden Sie die asynchrone Methode `createView()`. Sie wird aufgelöst, wenn der Stream zum Rendern bereit ist, und gibt ein Objekt mit der Eigenschaft `target` zurück. Dieses stellt ein `video`-Element dar, das an beliebiger Stelle in der DOM-Struktur angefügt werden kann.

  ```js
  await videoStreamRenderer.createView()
  ```

Löscht `videoStreamRenderer` und alle zugeordneten `VideoStreamRendererView`-Instanzen:

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>Methoden und Eigenschaften von VideoStreamRendererView

Beim Erstellen von `VideoStreamRendererView` können Sie die Eigenschaften `scalingMode` und `isMirrored` angeben. `scalingMode` kann `Stretch`, `Crop` oder `Fit` sein. Bei Angabe von `isMirrored` wird der gerenderte Stream vertikal gespiegelt.

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```

Jede `VideoStreamRendererView`-Instanz verfügt über eine `target`-Eigenschaft, die die Renderingoberfläche darstellt. Fügen Sie diese Eigenschaft in der Benutzeroberfläche der Anwendung an:

```js
htmlElement.appendChild(view.target);
```

Sie können `scalingMode` aktualisieren, indem Sie die Methode `updateScalingMode` aufrufen:

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Geräteverwaltung

In `deviceManager` können Sie lokale Geräte aufzählen, die Ihre Audio- und Videostreams in einem Anruf übertragen können. Außerdem können Sie damit die Berechtigung für den Zugriff auf die Mikrofone und Kameras des lokalen Geräts anfordern.

Sie können auf `deviceManager` zugreifen, indem Sie die Methode `callClient.getDeviceManager()` aufrufen:

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Abrufen lokaler Geräte

Für den Zugriff auf lokale Geräte können Sie Enumerationsmethoden für `deviceManager` verwenden. Die Enumeration ist eine asynchrone Aktion.

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>Festlegen des Standardmikrofons und -lautsprechers

In `deviceManager` können Sie ein Standardgerät festlegen, das zum Starten eines Anrufs verwendet wird. Wenn keine Clientstandardwerte festgelegt sind, verwendet Communication Services die Standardwerte des Betriebssystems.

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(localMicrophones[0]);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(localSpeakers[0]);
```

### <a name="local-camera-preview"></a>Vorschau auf lokaler Kamera

Sie können `deviceManager` und `VideoStreamRenderer` verwenden, um mit dem Rendern von Streams über Ihre lokale Kamera zu beginnen. Dieser Stream wird nicht an andere Teilnehmer gesendet. Es handelt sich um einen lokalen Vorschaufeed.

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>Anfordern der Berechtigung für Kamera und Mikrofon

Fordern Sie einen Benutzer auf, Berechtigungen für Kamera und/oder Mikrofon zu erteilen:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

Dies wird mit einem Objekt aufgelöst, das angibt, ob `audio`- und `video`-Berechtigungen erteilt wurden:

```js
console.log(result.audio);
console.log(result.video);
```
#### <a name="notes"></a>Notizen
- Das Ereignis „videoDevicesUpdated“ wird ausgelöst, wenn Videogeräte angeschlossen/getrennt werden.
- Das Ereignis „audioDevicesUpdated“ wird ausgelöst, wenn Audiogeräte angeschlossen werden.
- Wenn der DeviceManager erstellt wird, kennt er zunächst keine Geräte, wenn noch keine Berechtigungen erteilt wurden, und daher sind seine Gerätelisten zunächst leer. Wenn wir dann die API „DeviceManager.askPermission()“ aufrufen, wird der Benutzer aufgefordert, auf das Gerät zuzugreifen, und wenn der Benutzer auf „allow“ (Zulassen) klickt, um den Zugriff zu gewähren, erfährt der Geräte-Manager von den Geräten im System, aktualisiert seine Gerätelisten und gibt die Ereignisse „audioDevicesUpdated“ und „videoDevicesUpdated“ aus. Angenommen, wir aktualisieren die Seite und erstellen einen Geräte-Manager. Der Geräte-Manager kann sich über die Geräte informieren, da der Benutzer bereits zuvor Zugriff gewährt hat, sodass er zunächst seine Gerätelisten gefüllt hat und weder „audioDevicesUpdated“- noch „videoDevicesUpdated“-Ereignisse ausgeben wird.
- Die Enumeration/Auswahl von Sprechern wird unter Android Chrome, iOS Safari und macOS Safari nicht unterstützt.

## <a name="call-feature-extensions"></a>Erweiterungen für die Anruffunktion

### <a name="record-calls"></a>Aufzeichnen von Anrufen
> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Betarelease des ACS Calling Web SDK.

Die Anrufaufzeichnung ist ein erweitertes Feature der zentralen `Call`-API. Sie müssen zunächst das API-Objekt der Aufzeichnungsfunktion abrufen:

```js
const callRecordingApi = call.api(Features.Recording);
```

Um anschließend herauszufinden, ob der Anruf aufgezeichnet wird, prüfen Sie die Eigenschaft `isRecordingActive` von `callRecordingApi`. Er gibt `Boolean` zurück.

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

Sie können auch Aufzeichnungsänderungen abonnieren:

```js
const isRecordingActiveChangedHandler = () => {
    console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

### <a name="transfer-calls"></a>Durchstellen von Anrufen
> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Betarelease des ACS Calling Web SDK.

Die Anrufdurchstellung ist ein erweitertes Feature der zentralen `Call`-API. Sie müssen zunächst das API-Objekt der Durchstellungsfunktion abrufen:

```js
const callTransferApi = call.api(Features.Transfer);
```

Am Durchstellen von Anrufen sind drei Parteien beteiligt:

- *Durchsteller*: die Person, die die Durchstellungsanforderung auslöst.
- *Durchgestellter*: die Person, deren Anruf durchgestellt wird.
- *Durchstellungsziel*: die Person, an die der Anruf durchgestellt wird.

Beim Durchstellen werden folgende Schritt durchgeführt:

1. Es besteht bereits eine Anrufverbindung zwischen dem *Durchsteller* und dem *Durchgestellten*. Der *Durchsteller* beschließt, den Anruf vom *Durchgestellten* an das *Durchstellungsziel* zu übertragen.
1. Der *Durchsteller* ruft die `transfer`-API auf.
1. Der *Durchgestellte* entscheidet bei einem `transferRequested`-Ereignis mithilfe von `accept` oder `reject`, ob er die Durchstellungsanforderung an das *Durchstellungsziel* annimmt oder ablehnt.
1. Das *Durchstellungsziel* empfängt nur dann einen eingehenden Anruf, wenn der *Durchgestellte* die Durchstellungsanforderung akzeptiert.

Zum Durchstellen eines aktuellen Anrufs können Sie die `transfer`-API verwenden. `transfer` verwendet die optionalen `transferCallOptions`, mit denen Sie ein Flag `disableForwardingAndUnanswered` festlegen können:

- `disableForwardingAndUnanswered = false`: Wenn das *Durchstellungsziel* den durchgestellten Anruf nicht annimmt, werden die Einstellungen für Weiterleitung und Nichtannahme des *Durchstellungsziels* befolgt.
- `disableForwardingAndUnanswered = true`: Wenn das *Durchstellungsziel* den durchgestellten Anruf nicht annimmt, wird der Durchstellungsversuch beendet.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

Die `transferRequested`-API erlaubt Ihnen das Abonnieren der Ereignisse `transfer` und `transferStateChanged`. Ein `transferRequested`-Ereignis stammt aus einer `call`-Instanz. Ein `transferStateChanged`-Ereignis sowie `state` und `error` des Durchstellungsvorgangs stammen aus einer `transfer`-Instanz.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

Der *Durchgestellte* kann die vom *Durchsteller* ausgelöste Durchstellungsanforderung im Ereignis `transferRequested` über `accept()` oder `reject()` in `transferRequestedEventArgs` annehmen oder ablehnen. Sie können auf `targetParticipant`-Informationen und auf die Methoden `accept` und `reject` in `transferRequestedEventArgs` zugreifen.

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
    args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
    args.reject();
});
```

### <a name="dominant-speakers-of-a-call"></a>Dominante Sprecher eines Anrufs
> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Betarelease des ACS Calling Web SDK.

Dominante Sprecher für einen Anruf ist ein erweitertes Feature der Kern-API `Call` und ermöglicht es Ihnen, eine Liste der aktiven Sprecher des Anrufs zu erhalten. 

Es handelt sich um eine Rangliste, bei der das erste Element der Liste den letzten aktiven Sprecher des Gesprächs darstellt und so weiter.

Um die dominanten Sprecher in einem Anruf zu ermitteln, müssen Sie zunächst das API-Objekt für das Feature „Dominante Sprecher“ abrufen:

```js
const callDominantSpeakersApi = call.api(Features.CallDominantSpeakers);
```

Dann erhalten Sie die Liste der dominanten Sprecher, indem Sie `dominantSpeakers` aufrufen. Dieses hat den Typ `DominantSpeakersInfo`, der über die folgenden Member verfügt:

- `speakersList` enthält die Liste der als dominant eingestuften Sprecher des Anrufs. Diese werden durch ihre Teilnehmer-ID dargestellt.
- `timestamp` ist die letzte Aktualisierungszeit für die dominanten Sprecher in dem Anruf.

```js
let dominantSpeakers: DominantSpeakersInfo = callDominantSpeakersApi.dominantSpeakers;
```
Außerdem können Sie das `dominantSpeakersChanged`-Ereignis abonnieren, um zu erfahren, wann sich die Liste der dominanten Sprecher geändert hat.

```js
const dominantSpeakersChangedHandler = () => {
    // Get the most up to date list of dominant speakers
    let dominantSpeakers = callDominantSpeakersApi.dominantSpeakers;
};
callDominantSpeakersApi.api(Features.CallDominantSpeakers).on('dominantSpeakersChanged', dominantSpeakersChangedHandler);
``` 
#### <a name="handle-the-dominant-speakers-video-streams"></a>Verarbeiten der Videodatenströme des dominanten Sprechers

Ihre Anwendung kann das Feature `DominantSpeakers` verwenden, um einen oder mehrere Videodatenströme des dominanten Sprechers zu rendern und die Benutzeroberfläche immer dann zu aktualisieren, wenn die Liste der dominanten Sprecher aktualisiert wird. Dies kann mit dem folgenden Codebeispiel erreicht werden.

```js
// RemoteParticipant obj representation of the dominant speaker
let dominantRemoteParticipant: RemoteParticipant;
// It is recommended to use a map to keep track of a stream's associated renderer
let streamRenderersMap: new Map<RemoteVideoStream, VideoStreamRenderer>();

function getRemoteParticipantForDominantSpeaker(dominantSpeakerIdentifier) {
    let dominantRemoteParticipant: RemoteParticipant;
    switch(dominantSpeakerIdentifier.kind) {
        case 'communicationUser': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as CommunicationUserIdentifier).communicationUserId === dominantSpeakerIdentifier.communicationUserId
            });
            break;
        }
        case 'microsoftTeamsUser': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as MicrosoftTeamsUserIdentifier).microsoftTeamsUserId === dominantSpeakerIdentifier.microsoftTeamsUserId
            });
            break;
        }
        case 'unknown': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as UnknownIdentifier).id === dominantSpeakerIdentifier.id
            });
            break;
        }
    }
    return dominantRemoteParticipant;
}
// Handler function for when the dominant speaker changes
const dominantSpeakersChangedHandler = async () => {
    // Get the new dominant speaker's identifier
    const newDominantSpeakerIdentifier = currentCall.api(Features.DominantSpeakers).dominantSpeakers.speakersList[0];

     if (newDominantSpeakerIdentifier) {
        // Get the remote participant object that matches newDominantSpeakerIdentifier
        const newDominantRemoteParticipant = getRemoteParticipantForDominantSpeaker(newDominantSpeakerIdentifier);

        // Create the new dominant speaker's stream renderers
        const streamViews = [];
        for (const stream of newDominantRemoteParticipant.videoStreams) {
            if (stream.isAvailable && !streamRenderersMap.get(stream)) {
                const renderer = new VideoStreamRenderer(stream);
                streamRenderersMap.set(stream, renderer);
                const view = await videoStreamRenderer.createView();
                streamViews.push(view);
            }
        }

        // Remove the old dominant speaker's video streams by disposing of their associated renderers
        if (dominantRemoteParticipant) {
            for (const stream of dominantRemoteParticipant.videoStreams) {
                const renderer = streamRenderersMap.get(stream);
                if (renderer) {
                    streamRenderersMap.delete(stream);
                    renderer.dispose();
                }
            }
        }

        // Set the new dominant remote participant obj
        dominantRemoteParticipant = newDominantRemoteParticipant

        // Render the new dominant remote participant's streams
        for (const view of streamViewsToRender) {
            htmlElement.appendChild(view.target);
        }
     }
};

// When call is disconnected, set the dominant speaker to undefined
currentCall.on('stateChanged', () => {
    if (currentCall === 'Disconnected') {
        dominantRemoteParticipant = undefined;
    }
});

const dominantSpeakerIdentifier = currentCall.api(Features.DominantSpeakers).dominantSpeakers.speakersList[0];
dominantRemoteParticipant = getRemoteParticipantForDominantSpeaker(dominantSpeakerIdentifier);
currentCall.api(Features.DominantSpeakers).on('dominantSpeakersChanged', dominantSpeakersChangedHandler);

subscribeToRemoteVideoStream = async (stream: RemoteVideoStream, participant: RemoteParticipant) {
    let renderer: VideoStreamRenderer;

    const displayVideo = async () => {
        renderer = new VideoStreamRenderer(stream);
        streamRenderersMap.set(stream, renderer);
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }

    stream.on('isAvailableChanged', async () => {
        if (dominantRemoteParticipant !== participant) {
            return;
        }

        renderer = streamRenderersMap.get(stream);
        if (stream.isAvailable && !renderer) {
            await displayVideo();
        } else {
            streamRenderersMap.delete(stream);
            renderer.dispose();
        }
    });

    if (dominantRemoteParticipant !== participant) {
        return;
    }

    renderer = streamRenderersMap.get(stream);
    if (stream.isAvailable && !renderer) {
        await displayVideo();
    }
}
```
### <a name="call-diagnostics"></a>Anrufdiagnose
Die Anrufdiagnose ist ein erweitertes Feature der Kern-API `Call` und ermöglicht es Ihnen, einen aktiven Anruf zu diagnostizieren.

```js
const callDiagnostics = call.api(Features.Diagnostics);
```

Die folgenden benutzerorientierten Diagnosen sind verfügbar:

| type    | Name                           | BESCHREIBUNG                                                     | Mögliche Werte                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | Anwendungsfälle                                                                       |
| ------- | ------------------------------ | --------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| Netzwerk | noNetwork                      | Es ist kein Netzwerk verfügbar.                                  | - Wird auf `True` festgelegt, wenn ein Anruf nicht gestartet werden kann, weil kein Netzwerk verfügbar ist. <br/> - Wird auf `False` festgelegt, wenn ICE-Kandidaten vorhanden sind.                                                                                                                                                                                                                                                                                                                                                      | Das Gerät ist nicht mit einem Netzwerk verbunden.                                           |
| Netzwerk | networkRelaysNotReachable      | Probleme mit einem Netzwerk.                                        | - Wird auf `True` festgelegt, wenn das Netzwerk eine Einschränkung aufweist, die es Ihnen nicht erlaubt, ACS-Relais zu erreichen. <br/> - Wird bei einem neuen Anruf auf `False` festgelegt.                                                                                                                                                                                                                                                                                                                                                    | Während eines Anrufs, wenn das WLAN-Signal aktiviert und deaktiviert wird.                             |  |
| Netzwerk | networkReconnect               | Die Verbindung wurde unterbrochen und wir stellen die Verbindung zum Netzwerk wieder her. | - Wird auf `Poor` festgelegt, wenn die Medientransportkonnektivität verloren gegangen ist.                                                                                                                                 <br/> - Wird auf `Bad` festgelegt, wenn das Netzwerk getrennt ist. <br/> - Wird auf `Good` festgelegt, wenn eine neue Sitzung hergestellt wird.                                                                                                                                                                                           | Geringe Bandbreite, kein Internet                                                      |
| Netzwerk | networkReceiveQuality          | Ein Indikator für die Qualität des eingehenden Datenstroms.                 | - Wird auf `Bad` festgelegt, wenn ein schwerwiegendes Problem beim Empfang des Datenstroms vorliegt. Qualität                                                                                                                           <br/> - Wird auf `Poor` festgelegt, wenn ein leichtes Problem beim Empfang des Datenstroms vorliegt. Qualität                                                                                                                           <br/> - Wird auf `Good` festgelegt, wenn kein Problem beim Empfang des Datenstroms vorliegt. | Geringe Bandbreite                                                                   |
| Medien   | noSpeakerDevicesEnumerated     | Im System des Benutzers ist kein Audioausgabegerät (Lautsprecher) vorhanden. | - Wird auf `True` festgelegt, wenn keine Lautsprechergeräte im System vorhanden sind und die Lautsprecherauswahl unterstützt wird. <br/> - Wird auf `False` festgelegt, wenn mindestens ein Lautsprechergerät im System vorhanden ist und die Lautsprecherauswahl unterstützt wird.                                                                                                                                                                                                                                                                                | Kein Lautsprecher ist angeschlossen.                                                      |
| Medien   | speakingWhileMicrophoneIsMuted | Sprechen während der Stummschaltung.                                   | - Wird auf `True` festgelegt, wenn das lokale Mikrofon stummgeschaltet ist und der lokale Benutzer spricht. <br/> - Wird auf `False` festgelegt, wenn der lokale Benutzer entweder aufhört zu sprechen oder die Stummschaltung des Mikrofons aufhebt. <br/> * Hinweis: Derzeit wird diese Funktion noch nicht von Safari unterstützt, da die Audiopegelbeispiele von webrtc. stats übernommen werden.                                                                                                                                                                                                     | Schalten Sie Ihr Mikrofon während eines Anrufs stumm, und sprechen Sie in das Mikrofon.                          |
| Medien   | noMicrophoneDevicesEnumerated  | Keine Audioaufnahmegeräte (Mikrofon) im System des Benutzers vorhanden.      | - Wird auf `True` festgelegt, wenn keine Mikrofongeräte im System vorhanden sind. <br/> - Wird auf `False` festgelegt, wenn mindestens ein Mikrofongerät im System vorhanden ist.                                                                                                                                                                                                                                                                                                                                                 | Alle Mikrofone sind während des Gesprächs nicht angeschlossen.                                  |
| Medien   | cameraFreeze                   | Die Kamera erzeugt keine Bilder für mehr als fünf Sekunden.          | - Wird auf `True` festgelegt, wenn der lokale Videodatenstrom eingefroren ist. Dies bedeutet, dass die Remoteseite Ihr Video auf ihrem Bildschirm eingefroren sieht oder dass die Remoteteilnehmer Ihr Video nicht auf ihrem Bildschirm rendern. <br/> - Wird auf `False` festgelegt, wenn das Einfrieren beendet ist und die Benutzer Ihr Video wie gewohnt sehen können.                                                                                                                                                                                         | Die Kamera wurde während des Anrufs getrennt oder ein schlechtes Netzwerk hat zum Einfrieren der Kamera geführt. |
| Medien   | cameraStartFailed              | Generischer Kamerafehler.                                         | - Wird auf `True` festgelegt, wenn die Übertragung des lokalen Videos nicht gestartet werden kann, da das Kameragerät möglicherweise im System deaktiviert wurde oder von einem anderen Prozess verwendet wird. <br/> - Wird auf `False` festgelegt, wenn das ausgewählte Kameragerät erfolgreich ein lokales Video sendet. .                                                                                                                                                                                                                                           | Kamerafehler                                                                 |
| Medien   | cameraStartTimedOut            | Häufiges Szenario, in dem die Kamera einen fehlerhaften Zustand aufweist.                   | - Wird auf `True` festgelegt, wenn das Kameragerät einen Timeout aufweist, um mit dem Senden des Videodatenstroms zu beginnen. <br/> - Wird auf `False` festgelegt, wenn das ausgewählte Kameragerät wieder erfolgreich ein lokales Video sendet.                                                                                                                                                                                                                                                                                                                                 | Kamerafehler                                                                 |
| Medien   | microphoneNotFunctioning       | Das Mikrofon funktioniert nicht.                                  | - Wird auf `True` festgelegt, wenn die Übertragung des lokalen Audiodatenstroms nicht gestartet werden kann, da das Mikrofongerät möglicherweise im System deaktiviert wurde oder von einem anderen Prozess verwendet wird. Es dauert ungefähr 10 Sekunden, bis dieses UFD ausgelöst wird. <br/> - Wird auf `False` festgelegt, wenn das Mikrofon wieder erfolgreich einen Audiodatenstrom sendet.                                                                                                                                                                                    | Keine Mikrofone verfügbar, Mikrofonzugriff in einem System deaktiviert                |
| Medien   | microphoneMuteUnexpectedly     | Das Mikrofon ist stummgeschaltet.                                             | - Wird auf `True` festgelegt, wenn das Mikrofon unerwartet stummgeschaltet wird. <br/> - Wird auf `False` festgelegt, wenn das Mikrofon erfolgreich einen Audiodatenstrom sendet.                                                                                                                                                                                                                                                                                                                                                     | Das Mikrofon wird vom System stummgeschaltet.                                            |
| Medien   | screenshareRecordingDisabled   | Die Freigabe des Systembildschirms wurde in den Einstellungen verweigert.     | - Wird auf `True` festgelegt, wenn die Bildschirmfreigabe durch die Systemeinstellungen (Freigabe) verweigert wird. <br/> - Wird bei erfolgreicher Datenstromerfassung auf `False` festgelegt. <br/> Hinweis: Diese Diagnose funktioniert nur unter macOS.Chrome.                                                                                                                                                                                                                                                                                               | Die Bildschirmaufzeichnung ist in den Einstellungen deaktiviert.                                       |
| Medien   | microphonePermissionDenied     | Die Lautstärke des Geräts ist gering oder es ist unter macOS fast lautlos. | - Wird auf `True` festgelegt, wenn die Audioberechtigung durch die Systemeinstellungen (Audio) verweigert wird. <br/> - Wird bei erfolgreicher Datenstromerfassung auf `False` festgelegt. <br/> Hinweis: Diese Diagnose funktioniert nur unter macOS.                                                                                                                                                                                                                                                                                                                | Die Mikrofonberechtigungen sind in den Einstellungen deaktiviert.                            |
| Medien   | cameraPermissionDenied         | Die Kameraberechtigungen wurden in den Einstellungen verweigert.                     | - Wird auf `True` festgelegt, wenn die Kameraberechtigung durch die Systemeinstellungen (Video) verweigert wird. <br/> - Wird bei erfolgreicher Datenstromerfassung auf `False` festgelegt. <br> Hinweis: Diese Diagnose funktioniert nur unter macOS Chrome.                                                                                                                                                                                                                                                                                                          | Die Kameraberechtigungen sind in den Einstellungen deaktiviert.                                |

- Abonnieren Sie das Ereignis `diagnosticChanged`, um zu überwachen, wenn sich eine Anrufdiagnose ändert.
```js
/**
 *  Each diagnostic has the following data:
 * - diagnostic is the type of diagnostic, e.g. NetworkSendQuality, DeviceSpeakWhileMuted, etc...
 * - value is DiagnosticQuality or DiagnosticFlag:
 *     - DiagnosticQuality = enum { Good = 1, Poor = 2, Bad = 3 }.
 *     - DiagnosticFlag = true | false.
 * - valueType = 'DiagnosticQuality' | 'DiagnosticFlag'
 * - mediaType is the media type associated with the event, e.g. Audio, Video, ScreenShare. These are defined in `CallDiagnosticEventMediaType`.
 */
const diagnosticChangedListener = (diagnosticInfo: NetworkDiagnosticChangedEventArgs | MediaDiagnosticChangedEventArgs) => {
    console.log(`Diagnostic changed: ` +
        `Diagnostic: ${diagnosticInfo.diagnostic}` +
        `Value: ${diagnosticInfo.value}` + 
        `Value type: ${diagnosticInfo.valueType}` +
        `Media type: ${diagnosticInfo.mediaType}` +

    if (diagnosticInfo.valueType === 'DiagnosticQuality') {
        if (diagnosticInfo.value === DiagnosticQuality.Bad) {
            console.error(`${diagnosticInfo.diagnostic} is bad quality`);

        } else if (diagnosticInfo.value === DiagnosticQuality.Poor) {
            console.error(`${diagnosticInfo.diagnostic} is poor quality`);
        }

    } else if (diagnosticInfo.valueType === 'DiagnosticFlag') {
        if (diagnosticInfo.value === true) {
            console.error(`${diagnosticInfo.diagnostic}`);
        }
    }
};

callDiagnostics.network.on('diagnosticChanged', diagnosticChangedListener);
callDiagnostics.media.on('diagnosticChanged', diagnosticChangedListener);
```

- Rufen Sie die neuesten Diagnosewerte für Anrufe ab, die ausgelöst wurden. Wenn eine Diagnose nicht definiert ist, liegt dies daran, dass sie nie ausgelöst wurde.
```js
const latestNetworkDiagnostics = callDiagnostics.network.getLatest();
    
console.log(`noNetwork: ${latestNetworkDiagnostics.noNetwork.value}, ` +
    `value type = ${latestNetworkDiagnostics.noNetwork.valueType}`);
            
console.log(`networkReconnect: ${latestNetworkDiagnostics.networkReconnect.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReconnect.valueType}`);
            
console.log(`networkReceiveQuality: ${latestNetworkDiagnostics.networkReceiveQuality.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReceiveQuality.valueType}`);


const latestMediaDiagnostics = callDiagnostics.media.getLatest();
    
console.log(`speakingWhileMicrophoneIsMuted: ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.value}, ` +
    `value type = ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.valueType}`);
            
console.log(`cameraStartFailed: ${latestMediaDiagnostics.cameraStartFailed.value}, ` +
    `value type = ${latestMediaDiagnostics.cameraStartFailed.valueType}`);
            
console.log(`microphoneNotFunctioning: ${latestMediaDiagnostics.microphoneNotFunctioning.value}, ` +
    `value type = ${latestMediaDiagnostics.microphoneNotFunctioning.valueType}`);
```
## <a name="releasing-resources"></a>Freigeben von Ressourcen
1. So werden Ressourcen ordnungsgemäß freigegeben, wenn ein Anruf beendet ist:
    - Wenn der Anruf beendet ist, beendet unser SDK die Signalisierungs- und Mediensitzungen und überlässt Ihnen eine Instanz des Anrufs, die den letzten Zustand des Anrufs enthält. Sie können immer noch „callEndReason“ überprüfen. Wenn Ihre App den Verweis auf die Call-Instanz nicht enthält, wird der JavaScript-Garbage Collector alles bereinigen, sodass Ihre App in Bezug auf den Speicherverbrauch in den Ausgangszustand vor dem Anruf zurückkehren sollte.
2. Welche Ressourcentypen langlebig (Lebensdauer der App) und welche kurzlebig (Lebensdauer des Anrufs) sind:
    - Die folgenden Ressourcen werden als „langlebig“ betrachtet. Sie können sie erstellen und eine lange Zeit darauf verweisen, sie sind in Bezug auf den Ressourcenverbrauch (Arbeitsspeicher) nicht anspruchsvoll, sodass sie die Leistung nicht beeinträchtigen:
        - CallClient
        - CallAgent
        - DeviceManager
    - Die folgenden Ressourcen gelten als „kurzlebig“ und sind diejenigen, die eine Rolle beim Anruf selbst spielen, Ereignisse an die Anwendung senden oder mit lokalen Mediengeräten interagieren. Diese stellen größere Anforderungen an CPU und Arbeitsspeicher, aber sobald der Anruf beendet ist, bereinigt das SDK den Status und gibt die Ressourcen frei:
        - Anruf – Da er den aktuellen Zustand des Anrufs (sowohl Signalisierung als auch Medien) enthält.
        - RemoteParticipants – Stellen die Remoteteilnehmer im Anruf dar.
        - VideoStreamRenderer mit VideoStreamRendererViews – Verarbeitet das Videorendering.
