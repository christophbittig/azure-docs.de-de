---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 4c95736394cd112daa48f3bfa6a47ae4ba0f147d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700121"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

## <a name="manage-devices"></a>Verwalten von Geräten
Um Video mit Calling verwenden zu können, müssen Sie wissen, wie Geräte verwaltet werden. Mit den Geräten können Sie die Übertragung von Audio und Video an den Anruf steuern.

`DeviceManager` ermöglicht das Aufzählen lokaler Geräte, die in einem Anruf zur Übertragung von Audio- oder Videostreams verwendet werden können. Außerdem können Sie damit beim Benutzer auch Zugriff auf ein Mikrofon oder auf eine Kamera anfordern. Auf `deviceManager` kann im Objekt `callClient` zugegriffen werden.

```swift
self.callClient!.getDeviceManager { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    }
```

### <a name="enumerate-local-devices"></a>Aufzählen lokaler Geräte

Für den Zugriff auf lokale Geräte können Enumerationsmethoden für den Geräte-Manager verwendet werden. Enumeration ist ein synchroner Vorgang.

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras // [VideoDeviceInfo, VideoDeviceInfo...]
```

### <a name="get-a-local-camera-preview"></a>Abrufen einer Vorschau der lokalen Kamera

Sie können mit `Renderer` mit dem Rendern eines Streams von Ihrer lokalen Kamera beginnen. Dieser Stream wird nicht an andere Teilnehmer gesendet. Es handelt sich um einen lokalen Vorschaufeed. Dies ist ein asynchroner Vorgang.

```swift
let camera: VideoDeviceInfo = self.deviceManager!.cameras.first!
let localVideoStream = LocalVideoStream(camera: camera)
let localRenderer = try! VideoStreamRenderer(localVideoStream: localVideoStream)
self.view = try! localRenderer.createView()
```

### <a name="get-local-camera-preview-properties"></a>Abrufen von Eigenschaften der Vorschau der lokalen Kamera

Der Renderer verfügt über eine Reihe von Eigenschaften und Methoden zum Steuern des Renderings.

```swift
// Constructor can take in LocalVideoStream or RemoteVideoStream
let localRenderer = VideoStreamRenderer(localVideoStream:localVideoStream)
let remoteRenderer = VideoStreamRenderer(remoteVideoStream:remoteVideoStream)

// [StreamSize] size of the rendering view
localRenderer.size

// [VideoStreamRendererDelegate] an object you provide to receive events from this Renderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer!.createView(withOptions: CreateViewOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()
```

## <a name="place-a-11-call-with-video"></a>Tätigen eines 1:1-Anrufs mit Video
Wie Sie eine Geräte-Manager-Instanz erhalten, erfahren Sie im [Abschnitt zur Geräteverwaltung](#manage-devices).

```swift
let firstCamera = self.deviceManager!.cameras.first
self.localVideoStreams = [LocalVideoStream]()
self.localVideoStreams!.append(LocalVideoStream(camera: firstCamera!))
let videoOptions = VideoOptions(localVideoStreams: self.localVideoStreams!)

let startCallOptions = StartCallOptions()
startCallOptions.videoOptions = videoOptions

let callee = CommunicationUserIdentifier('UserId')
self.callAgent?.startCall(participants: [callee], options: startCallOptions) { (call, error) in
    if error == nil {
        print("Successfully started outgoing video call")
        self.call = call
    } else {
        print("Failed to start outgoing video call")
    }
}
```

## <a name="render-remote-participant-video-streams"></a>Rendern von Videostreams von Remoteteilnehmern

Remoteteilnehmer können während eines Anrufs eine Video- oder Bildschirmfreigabe initiieren.

### <a name="handle-video-sharing-or-screen-sharing-streams-of-remote-participants"></a>Behandeln von Video- oder Bildschirmfreigabestreams von Remoteteilnehmern

Untersuchen Sie die Sammlungen vom Typ `videoStreams`, um die Streams von Remoteteilnehmern aufzulisten.

```swift
var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]
```

### <a name="get-remote-video-stream-properties"></a>Abrufen von Eigenschaften von Remotevideostreams

```swift
var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'
var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available
var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream
```

### <a name="render-remote-participant-streams"></a>Rendern von Streams von Remoteteilnehmern

Verwenden Sie den folgenden Code, um das Rendern von Streams von Remoteteilnehmern zu starten:

```swift
let renderer = VideoStreamRenderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView = renderer?.createView(withOptions: CreateViewOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)
```

### <a name="get-remote-video-renderer-methods-and-properties"></a>Abrufen von Methoden und Eigenschaften des Remotevideo-Renderers

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```