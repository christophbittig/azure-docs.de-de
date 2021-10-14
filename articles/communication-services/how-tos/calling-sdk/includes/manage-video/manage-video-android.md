---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: c8683ae7275c9cff9e3035a0ad4b0e1cdc6015d3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700124"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

## <a name="device-management"></a>Geräteverwaltung
Um Video mit Calling verwenden zu können, müssen Sie wissen, wie Geräte verwaltet werden. Mit den Geräten können Sie die Übertragung von Audio und Video an den Anruf steuern.

`DeviceManager` ermöglicht Ihnen das Aufzählen lokaler Geräte, die in einem Anruf zur Übertragung Ihrer Audio-/Videostreams verwendet werden können. DeviceManager erlaubt Ihnen auch, von einem Benutzer die Berechtigung für den Zugriff auf sein Mikrofon und seine Kamera über die native Browser-API anzufordern.

Sie können auf `deviceManager` zugreifen, indem Sie die `callClient.getDeviceManager()`-Methode aufrufen.
> [!WARNING]
> Derzeit muss zuerst ein `callAgent`-Objekt instanziiert werden, um Zugriff auf DeviceManager zu erhalten.

```java
Context appContext = this.getApplicationContext();
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
```

### <a name="enumerate-local-devices"></a>Aufzählen lokaler Geräte

Für den Zugriff auf lokale Geräte können Sie Enumerationsmethoden für den Geräte-Manager verwenden. Enumeration ist ein synchroner Vorgang.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]
```

### <a name="local-camera-preview"></a>Vorschau auf lokaler Kamera

Sie können `DeviceManager` und `Renderer` verwenden, um mit dem Rendern von Streams über Ihre lokale Kamera zu beginnen. Dieser Stream wird nicht an andere Teilnehmer gesendet. Es handelt sich um einen lokalen Vorschaufeed. Dies ist ein asynchroner Vorgang.

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
LocalVideoStream[] localVideoStreams = new LocalVideoStream[1];
localVideoStreams[0] = currentVideoStream;
videoOptions = new VideoOptions(localVideoStreams);

VideoStreamRenderer previewRenderer = new VideoStreamRenderer(currentVideoStream, appContext);
VideoStreamRendererView uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="place-a-11-call-with-video-camera"></a>Tätigen eines 1:1-Anrufs mit Videokamera
> [!WARNING]
> Derzeit wird nur ein ausgehender lokaler Videostream unterstützt. Um einen Anruf mit Video zu tätigen, müssen Sie die lokalen Kameras mit der `getCameras`-API von `deviceManager` aufzählen.
Sobald Sie eine gewünschte Kamera ausgewählt haben, erzeugen Sie damit eine `LocalVideoStream`-Instanz und übergeben diese an `videoOptions` als Element im `localVideoStream`-Array an eine `call`-Methode.
Sobald die Anrufverbindung hergestellt ist, wird automatisch ein Videostream von der ausgewählten Kamera an andere Teilnehmer gesendet.

> [!NOTE]
> Aus Gründen des Datenschutzes wird das Video nicht für den Anruf freigegeben, wenn es nicht lokal in der Vorschau angezeigt wird.
Weitere Informationen finden Sie unter [Vorschau auf lokaler Kamera](#local-camera-preview).
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager(appContext).get().getCameras().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
LocalVideoStream[] localVideoStreams = new LocalVideoStream[1];
localVideoStreams[0] = currentVideoStream;
VideoOptions videoOptions = new VideoOptions(localVideoStreams);

// Render a local preview of video so the user knows that their video is being shared
Renderer previewRenderer = new VideoStreamRenderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new CreateViewOptions(ScalingMode.FIT));
// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);

CommunicationUserIdentifier[] participants = new CommunicationUserIdentifier[]{ new CommunicationUserIdentifier("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.startCall(context, participants, startCallOptions);
```

## <a name="start-and-stop-sending-local-video"></a>Starten und Beenden des Sendens von lokalem Video

Um ein Video zu starten, müssen Sie die Kameras mit der `getCameraList`-API für das `deviceManager`-Objekt aufzählen. Erstellen Sie dann eine neue Instanz von `LocalVideoStream` zur Übergabe der gewünschten Kamera, und übergeben Sie sie in der `startVideo`-API als Argument:

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
LocalVideoStream currentLocalVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentLocalVideoStream);
Future startVideoFuture = call.startVideo(appContext, currentLocalVideoStream);
startVideoFuture.get();
```

Sobald Sie erfolgreich mit dem Senden von Video beginnen, wird eine `LocalVideoStream`-Instanz der `localVideoStreams`-Sammlung für die Anrufinstanz hinzugefügt.

```java
currentLocalVideoStream == call.getLocalVideoStreams().get(0);
```

Um das lokale Video anzuhalten, übergeben Sie die `LocalVideoStream`-Instanz, die in der `localVideoStreams`-Sammlung verfügbar ist:

```java
call.stopVideo(appContext, currentLocalVideoStream).get();
```

Sie können während des Sendens von Video auf ein anderes Kameragerät umschalten, indem Sie `switchSource` für eine `LocalVideoStream`-Instanz aufrufen:
```java
currentLocalVideoStream.switchSource(source).get();
```

## <a name="render-remote-participant-video-streams"></a>Rendern von Videostreams von Remoteteilnehmern
Um die Video- und Bildschirmübertragungs-Streams der Remoteteilnehmer aufzulisten, sehen Sie sich die `videoStreams`-Sammlungen an:
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
Um einen `RemoteVideoStream` von einem Remoteteilnehmer zu rendern, müssen Sie ein `OnVideoStreamsUpdated`-Ereignis abonnieren.

Innerhalb des Ereignisses zeigt der Wechsel der `isAvailable`-Eigenschaft zu „true“ an, dass der Remoteteilnehmer gerade einen Stream sendet. Sobald dies geschieht, erstellen Sie eine neue Instanz eines `Renderer`, dann erstellen Sie eine neue `RendererView` mithilfe der asynchronen `createView`-API, und fügen Sie `view.target` an beliebiger Stelle in der Benutzeroberfläche Ihrer Anwendung an.

Immer wenn sich die Verfügbarkeit eines Remotestreams ändert, können Sie wählen, ob Sie den gesamten Renderer, eine bestimmte `RendererView` zerstören oder sie behalten möchten, was jedoch zur Anzeige leerer Videobilder führt.

```java
VideoStreamRenderer remoteVideoRenderer = new VideoStreamRenderer(remoteParticipantStream, appContext);
VideoStreamRendererView uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.FIT));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>Eigenschaften von Remotevideostreams
Ein Remotevideostream hat verschiedene Eigenschaften.

* `Id`: ID eines Remotevideostreams
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType`: Kann „Video“ oder „ScreenSharing“ sein
```java
MediaStreamType type = remoteVideoStream.getMediaStreamType();
```

* `isAvailable`: gibt an, ob der Endpunkt des Remoteteilnehmers einen Stream aktiv sendet
```java
boolean availability = remoteVideoStream.isAvailable();
```

### <a name="renderer-methods-and-properties"></a>Methoden und Eigenschaften des Renderers
Rendererobjekt nach APIs

* Erstellt eine `VideoStreamRendererView`-Instanz, die später auf der Benutzeroberfläche der Anwendung angefügt werden kann, um Remotevideostreams zu rendern.
```java
// Create a view for a video stream
VideoStreamRendererView.createView()
```
* Löscht den Renderer und alle mit ihm verknüpften `VideoStreamRendererView`-Instanzen. Wird aufgerufen, wenn Sie alle zugehörigen Ansichten aus der Benutzeroberfläche entfernt haben.
```java
VideoStreamRenderer.dispose()
```

* `StreamSize`: Größe (Breite/Höhe) eines Remotevideostreams
```java
StreamSize renderStreamSize = VideoStreamRenderer.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```

### <a name="rendererview-methods-and-properties"></a>Methoden und Eigenschaften von RendererView
Beim Erstellen einer `VideoStreamRendererView` können Sie die Eigenschaften `ScalingMode` und `mirrored` angeben, die für diese Ansicht gelten sollen: Der Skalierungsmodus kann „CROP“ oder „FIT“ sein.

```java
VideoStreamRenderer remoteVideoRenderer = new VideoStreamRenderer(remoteVideoStream, appContext);
VideoStreamRendererView rendererView = remoteVideoRenderer.createView(new CreateViewOptions(ScalingMode.Fit));
```

Das erstellte RendererView-Objekt kann dann mit dem folgenden Ausschnitt an die Benutzeroberfläche der Anwendung angefügt werden:
```java
layout.addView(rendererView);
```

Sie können den Skalierungsmodus später durch Aufrufen der `updateScalingMode`-API für das RendererView-Objekt mit entweder ScalingMode.CROP oder ScalingMode.FIT als Argument aktualisieren.
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.CROP)
```