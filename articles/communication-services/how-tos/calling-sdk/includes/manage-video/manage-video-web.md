---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 97e28db07e950fde42033ef57c76b2734b73f7cc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700125"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

## <a name="device-management"></a>Geräteverwaltung
Um Video mit Calling verwenden zu können, müssen Sie wissen, wie Geräte verwaltet werden. Mit den Geräten können Sie die Übertragung von Audio und Video an den Anruf steuern.

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

## <a name="start-and-stop-sending-local-video"></a>Starten und Beenden des Sendens von lokalem Video

Um ein Video zu starten, müssen Sie Kameras mit der `getCameras`-Methode für das `deviceManager`-Objekt aufzählen. Erstellen Sie dann eine neue Instanz von `LocalVideoStream` mit der gewünschten Kamera, und übergeben Sie anschließend das Objekt `LocalVideoStream` an die Methode `startVideo` eines vorhandenen Anrufobjekts:

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

Es gibt vier Methoden, in denen Sie eine `localVideoStream`-Instanz übergeben können, um ein Video in einem Anruf zu starten: `callAgent.startCall()`, `callAgent.join()`, `call.accept()` und `call.startVideo()`. Um `call.stopVideo()` zu verwenden, müssen Sie dieselbe `localVideoStream`-Instanz übergeben, die Sie der ursprünglichen Methode zum Starten des Videos übergeben haben.

Sie können während des Sendens von Videodaten auf ein anderes Kameragerät umschalten, indem Sie `switchSource` für eine `localVideoStream`-Instanz aufrufen:

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

Wenn das angegebene Videogerät von einem anderen Prozess verwendet wird oder es im System deaktiviert ist:
- Wenn Ihr Video während eines Anrufs deaktiviert ist und Sie das Video mit `call.startVideo()` starten, wird diese Methode mit einem `SourceUnavailableError` ausgelöst und `cameraStartFiled` wird auf „True“ festgelegt.
- Ein Aufruf der `localVideoStream.switchSource()`-Methode führt dazu, dass `cameraStartFailed` auf „True“ festgelegt wird.
Unser Leitfaden zur Anrufdiagnose enthält zusätzliche Informationen zum Diagnostizieren von Problemen im Zusammenhang mit Anrufen.

## <a name="place-a-11-call-with-video-camera"></a>Tätigen eines 1:1-Anrufs mit Videokamera

> [!IMPORTANT]
> Derzeit wird nur ein ausgehender lokaler Videostream unterstützt.

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

## <a name="videostreamrenderer-methods-and-properties"></a>Methoden und Eigenschaften von VideoStreamRenderer
Erstellen Sie eine `VideoStreamRendererView`-Instanz, die in der Benutzeroberfläche der Anwendung angefügt werden kann, um den Remotevideostream zu rendern. Verwenden Sie die asynchrone Methode `createView()`. Sie wird aufgelöst, wenn der Stream zum Rendern bereit ist, und gibt ein Objekt mit der Eigenschaft `target` zurück. Dieses stellt ein `video`-Element dar, das an beliebiger Stelle in der DOM-Struktur angefügt werden kann.

```js
await videoStreamRenderer.createView();
```

Löscht `videoStreamRenderer` und alle zugeordneten `VideoStreamRendererView`-Instanzen:
```js
videoStreamRenderer.dispose();
```

## <a name="videostreamrendererview-methods-and-properties"></a>Methoden und Eigenschaften von VideoStreamRendererView

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
view.updateScalingMode('Crop');
```