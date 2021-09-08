---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: mikben
ms.openlocfilehash: 45123517d1f2a53a6bf023d1af6fafc528cde856
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114339762"
---
## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Azure Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../../create-communication-resource.md).
- Ein Benutzerzugriffstoken zum Aktivieren des Anrufclients. [Rufen Sie ein Benutzerzugriffstoken ab.](../../../access-tokens.md)
- Optional: Durchlaufen Sie die Schnellstartanleitung [Hinzufügen von Sprachanrufen zu Ihrer App](../../getting-started-with-calling.md).

## <a name="set-up-your-system"></a>Einrichten des Systems

### <a name="create-the-xcode-project"></a>Erstellen des Xcode-Projekts

Erstellen Sie in Xcode ein neues iOS-Projekt, und wählen Sie die Vorlage **Single View App** aus. In dieser Schnellstartanleitung wird das [SwiftUI-Framework](https://developer.apple.com/xcode/swiftui/) verwendet. Legen Sie daher **Language** (Sprache) auf **Swift** und **User Interface** (Benutzeroberfläche) auf **SwiftUI** fest. 

Während dieses Schnellstarts werden keine Komponenten- oder Benutzeroberflächentests erstellt. Die Textfelder **Include Unit Tests** (Komponententests einbeziehen) und **Include UI Tests** (Benutzeroberflächentests einbeziehen) können daher geleert werden.

:::image type="content" source="../../media/ios/xcode-new-ios-project.png" alt-text="Screenshot: Fenster zum Erstellen eines Projekts in Xcode":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installieren des Pakets und der Abhängigkeiten mit CocoaPods

1. Erstellen Sie wie folgt eine Podfile für die Anwendung:

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0'
   end
   ```

2. Führen Sie `pod install` aus.
3. Öffnen Sie `.xcworkspace` mit Xcode.

### <a name="request-access-to-the-microphone"></a>Anfordern des Zugriffs auf das Mikrofon

Um auf das Mikrofon des Geräts zugreifen zu können, müssen Sie die Liste der Informationseigenschaften Ihrer App mit `NSMicrophoneUsageDescription` aktualisieren. Legen Sie den zugehörigen Wert auf eine Zeichenfolge (`string`) fest. Diese wird in das Dialogfeld eingeschlossen, mit dem das System Zugriff vom Benutzer anfordert.

Klicken Sie mit der rechten Maustaste auf den Eintrag `Info.plist` der Projektstruktur, und wählen Sie anschließend **Open As** (Öffnen als)  > **Source Code** (Quellcode) aus. Fügen Sie im Abschnitt `<dict>` der obersten Ebene die folgenden Zeilen hinzu, und speichern Sie dann die Datei.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Öffnen Sie die Datei *ContentView.swift* Ihres Projekts, und fügen Sie am Anfang der Datei eine Deklaration vom Typ `import` hinzu, um die Bibliothek `AzureCommunicationCalling` zu importieren. Importieren Sie außerdem `AVFoundation`. Dies ist für Audioberechtigungsanforderungen im Code erforderlich.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="learn-the-object-model"></a>Kennenlernen des Objektmodells

Die folgenden Klassen und Schnittstellen befassen sich mit einigen der wichtigsten Features des Azure Communication Services Calling SDK für iOS.

> [!NOTE]
> In dieser Schnellstartanleitung wird die Version 1.0.0-beta.8 des Calling SDK verwendet.


| Name                                  | BESCHREIBUNG                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| `CallClient` | `CallClient` ist der Haupteinstiegspunkt des Calling SDK.|
| `CallAgent` | `CallAgent` dient zum Starten und Verwalten von Anrufen. |
| `CommunicationTokenCredential` | `CommunicationTokenCredential` wird als Tokenanmeldeinformation zum Instanziieren von `CallAgent` verwendet.| 
| `CommunicationIdentifier` | `CommunicationIdentifier` dient zur Darstellung der Identität des Benutzers. Die Identität kann `CommunicationUserIdentifier`, `PhoneNumberIdentifier` oder `CallingApplication` sein. |

> [!NOTE]
> Bei Implementierung von Ereignisdelegaten muss die Anwendung einen eindeutigen Verweis auf die Objekte enthalten, die Ereignisabonnements erfordern. Wenn beispielsweise ein `RemoteParticipant`-Objekt beim Aufrufen der `call.addParticipant`-Methode zurückgegeben wird und die Anwendung den Delegaten auf das Lauschen auf `RemoteParticipantDelegate` festlegt, muss die Anwendung einen eindeutigen Verweis auf das `RemoteParticipant`-Objekt enthalten. Andernfalls löst der Delegat, wenn dieses Objekt erfasst wird, eine schwerwiegende Ausnahme aus, sobald das Calling SDK versucht, das Objekt aufzurufen.

## <a name="initialize-callagent"></a>Initialisieren von „CallAgent“

Um eine `CallAgent`-Instanz auf der Grundlage von `CallClient` zu erstellen, müssen Sie eine Methode vom Typ `callClient.createCallAgent` verwenden, die asynchron ein Objekt vom Typ `CallAgent` zurückgibt, nachdem es initialisiert wurde.

Für die Erstellung eines Anrufclients muss ein Objekt vom Typ `CommunicationTokenCredential` übergeben werden.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
do {
    let options = CommunicationTokenRefreshOptions(initialToken: token, refreshProactively: true, tokenRefresher: self.fetchTokenSync)
    userCredential = try CommunicationTokenCredential(withOptions: options)
} catch {
    updates("Couldn't created Credential object", false)
    initializationDispatchGroup!.leave()
    return
}

// tokenProvider needs to be implemented by Contoso, which fetches a new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Übergeben Sie das von Ihnen erstellte Objekt vom Typ `CommunicationTokenCredential` an `CallClient`, und legen Sie den Anzeigenamen fest.

```swift

self.callClient = CallClient()
let callAgentOptions = CallAgentOptions()
options.displayName = " iOS ACS User"

self.callClient!.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Tätigen eines ausgehenden Anrufs

Um einen Anruf zu erstellen und zu starten, müssen Sie eine der APIs für `CallAgent` aufrufen und die Communication Services-Identität eines Benutzers angeben, den Sie unter Verwendung des Verwaltungs-SDK von Communication Services bereitgestellt haben.

Erstellung und Start des Anrufs erfolgen synchron. Sie erhalten eine Anrufinstanz, die es Ihnen ermöglicht, alle Ereignisse im Zusammenhang mit dem Anruf zu abonnieren.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Tätigen eines 1:1-Anrufs eines Benutzers oder eines 1:n-Anrufs mit Benutzern und Festnetznummern

```swift

let callees = [CommunicationUser(identifier: 'UserId')]
self.callAgent?.startCall(participants: callees, options: StartCallOptions()) { (call, error) in
     if error == nil {
         print("Successfully started outgoing call")
         self.call = call
     } else {
         print("Failed to start outgoing call")
     }
}

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>1:n-Anruf mit Benutzern und Festnetznummern
Um den Anruf über das Telefonfestnetz zu tätigen, müssen Sie die Telefonnummer angeben, die Sie mit Communication Services bezogen haben.

```swift

let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier('UserId')
self.callAgent?.startCall(participants: [pstnCallee, callee], options: StartCallOptions()) { (groupCall, error) in
     if error == nil {
         print("Successfully started outgoing call to multiple participants")
         self.call = groupCall
     } else {
         print("Failed to start outgoing call to multiple participants")
     }
}

```

### <a name="place-a-11-call-with-video"></a>Tätigen eines 1:1-Anrufs mit Video
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

### <a name="join-a-group-call"></a>Teilnehmen an einem Gruppenanruf
Um an einem Anruf teilzunehmen, müssen Sie eine der APIs für `CallAgent` aufrufen.

```swift

let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string")!)
self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions()) { (call, error) in
     if error == nil {
         print("Successfully joined group call")
         self.call = call
     } else {
         print("Failed to join group call")
     }
}

```

### <a name="subscribe-to-an-incoming-call"></a>Abonnieren eines eingehenden Anrufs
Abonnieren Sie ein Ereignis für einen eingehenden Anruf.

```
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func callAgent(_ callAgent: CallAgent, didRecieveIncomingCall incomingcall: IncomingCall) {
        self.incomingCall = incomingcall
        // Subscribe to get OnCallEnded event
        self.incomingCall?.delegate = self
    }

    // Event raised when incoming call was not answered
    public func incomingCall(_ incomingCall: IncomingCall, didEnd args: PropertyChangedEventArgs) {
        print("Incoming call was not answered")
        self.incomingCall = nil
    }
}
```

### <a name="accept-an-incoming-call"></a>Annehmen eines eingehenden Anrufs
Rufen Sie zum Annehmen eines Anrufs die Methode `accept` für ein `IncomingCall`-Objekt auf.

```swift
self.incomingCall!.accept(options: AcceptCallOptions()) { (call, error) in
   if (error == nil) {
       print("Successfully accepted incoming call")
       self.call = call
   } else {
       print("Failed to accept incoming call")
   }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras.first
localVideoStreams = [LocalVideoStream]()
localVideoStreams!.append(LocalVideoStream(camera: firstCamera!))
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions.videoOptions = VideoOptions(localVideoStreams: localVideoStreams!)
if let incomingCall = self.incomingCall {
  incomingCall.accept(options: acceptCallOptions) { (call, error) in
        if error == nil {
            print("Incoming call accepted")
        } else {
            print("Failed to accept incoming call")
        }
    }
} else {
  print("No incoming call found to accept")
}
```

## <a name="set-up-push-notifications"></a>Einrichten von Pushbenachrichtigungen

Eine mobile Pushbenachrichtigung ist die Popupbenachrichtigung, die Sie auf dem mobilen Gerät erhalten. Bei Anrufen konzentrieren wir uns auf VoIP-Pushbenachrichtigungen (Voice over Internet Protocol). 

In den folgenden Abschnitten erfahren Sie, wie Sie sich für Pushbenachrichtigungen registrieren, sie behandeln und die Registrierung für sie aufheben. Bevor Sie mit diesen Aufgaben beginnen, sind jedoch folgende Schritte erforderlich:

1. Navigieren Sie in Xcode zur Registerkarte **Signing & Capabilities** (Signieren und Funktionen). Fügen Sie eine Funktion hinzu, indem Sie **+ Capability** (+ Funktion) und anschließend **Push Notifications** (Pushbenachrichtigungen) auswählen.
2. Fügen Sie eine weitere Funktion hinzu, indem Sie **+ Capability** (+ Funktion) und anschließend **Background Modes** (Hintergrundmodi) auswählen.
3. Aktivieren Sie unter **Background Modes** (Hintergrundmodi) die Kontrollkästchen **Voice over IP** und **Remote notifications** (Remotebenachrichtigungen) aus.

:::image type="content" source="../../media/ios/xcode-push-notification.png" alt-text="Screenshot: Hinzufügen von Funktionen in Xcode" lightbox="../../media/ios/xcode-push-notification.png":::

### <a name="register-for-push-notifications"></a>Registrieren für Pushbenachrichtigungen

Rufen Sie zum Registrieren für Pushbenachrichtigungen `registerPushNotification()` für eine `CallAgent`-Instanz mit einem Geräteregistrierungstoken auf.

Die Registrierung für Pushbenachrichtigungen muss nach erfolgreicher Initialisierung erfolgen. Wenn das Objekt `callAgent` zerstört wurde, wird `logout` aufgerufen, wodurch die Registrierung von Pushbenachrichtigungen automatisch aufgehoben wird.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken!) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}

```

### <a name="handle-push-notifications"></a>Behandeln von Pushbenachrichtigungen
Rufen Sie `handlePushNotification()` für eine `CallAgent`-Instanz mit Wörterbuchnutzdaten auf, um Pushbenachrichtigungen für eingehende Anrufe zu erhalten.

```swift

let callNotification = PushNotificationInfo.fromDictionary(pushPayload.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error == nil) {
        print("Handling of push notification was successful")
    } else {
        print("Handling of push notification failed")
    }
}

```
### <a name="unregister-push-notifications"></a>Aufheben der Registrierung der Pushbenachrichtigungen

Anwendungen können die Registrierung der Pushbenachrichtigung jederzeit aufheben. Rufen Sie einfach die `unregisterPushNotification`-Methode für `CallAgent` auf.

> [!NOTE]
> Die Registrierung von Anwendungen für Pushbenachrichtigungen wird bei der Abmeldung nicht automatisch aufgehoben.

```swift

callAgent.unregisterPushNotification { (error) in
    if (error == nil) {
        print("Unregister of push notification was successful")
    } else {
       print("Unregister of push notification failed, please try again")
    }
}

```

## <a name="perform-mid-call-operations"></a>Ausführen von Vorgängen während eines Anrufs

Sie können während eines Anrufs verschiedene Vorgänge ausführen, um Einstellungen für Video und Audio zu verwalten.

### <a name="mute-and-unmute"></a>Stummschalten und Aufheben der Stummschaltung

Zum Stummschalten oder Aufheben der Stummschaltung des lokalen Endpunkts können Sie die asynchronen APIs `mute` und `unmute` verwenden.

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}

```

Verwenden Sie den folgenden Code, um die Stummschaltung des lokalen Endpunkts asynchron aufzuheben:

```swift
call!.unmute { (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
}
```

### <a name="start-and-stop-sending-local-video"></a>Starten und Beenden des Sendens von lokalem Video

Verwenden Sie die API `startVideo`, und übergeben Sie `localVideoStream` mit `camera`, um das Senden lokaler Videodaten an andere Anrufteilnehmer zu starten.

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras.first
let localVideoStream = LocalVideoStream(camera: firstCamera!)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    } else {
        print("Local video failed to start")
    }
}

```

Sobald Sie mit dem Senden von Videodaten begonnen haben, wird die `LocalVideoStream`-Instanz der Sammlung `localVideoStreams` für eine Anrufinstanz hinzugefügt.

```swift

call.localVideoStreams

```

Übergeben Sie zum Beenden des Sendens lokaler Videodaten die `localVideoStream`-Instanz, die beim Aufrufen von `call.startVideo` zurückgegeben wurde. Dies ist ein asynchroner Vorgang.

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="manage-remote-participants"></a>Verwalten von Remoteteilnehmern

Alle Remoteteilnehmer werden durch den Typ `RemoteParticipant` dargestellt und sind über die Sammlung `remoteParticipants` für eine Anrufinstanz verfügbar.

### <a name="list-participants-in-a-call"></a>Auflisten der Teilnehmer an einem Anruf

```swift

call.remoteParticipants

```

### <a name="get-remote-participant-properties"></a>Abrufen von Eigenschaften von Remoteteilnehmern

```swift

// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision a token for another user
var identity = remoteParticipant.identifier

// ParticipantStateIdle = 0, ParticipantStateEarlyMedia = 1, ParticipantStateConnecting = 2, ParticipantStateConnected = 3, ParticipantStateOnHold = 4, ParticipantStateInLobby = 5, ParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [Error] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// RemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [RemoteVideoStream, RemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Hinzufügen eines Teilnehmers zu einem Anruf

Wenn Sie einen Teilnehmer einem Anruf hinzufügen möchten (entweder als Benutzer oder Telefonnummer), können Sie `addParticipant` aufrufen. Durch diesen Befehl wird synchron die Instanz eines Remoteteilnehmers zurückgegeben.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Entfernen eines Teilnehmers aus einem Anruf
Wenn Sie einen Teilnehmer aus einem Anruf entfernen möchten (entweder als Benutzer oder Telefonnummer), können Sie die API `removeParticipant` aufrufen. Dieser Vorgang wird asynchron aufgelöst.

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
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

## <a name="manage-devices"></a>Verwalten von Geräten

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

## <a name="record-calls"></a>Aufzeichnen von Anrufen
> [!WARNING]
> Bis zur Version 1.1.0 und der Betaversion 1.1.0-beta.1 des ACS Calling iOS SDK wird `isRecordingActive` als Teil des `Call`-Objekts verwendet und `didChangeRecordingState` ist Teil des `CallDelegate`-Delegaten. Für die neuen Betaversionen wurden diese APIs als erweitertes Feature von `Call` verschoben, wie unten beschrieben.
> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Beta-Release des ACS Calling iOS SDK.

Die Anrufaufzeichnung ist ein erweitertes Feature der zentralen `Call`-API. Sie müssen zunächst das API-Objekt der Aufzeichnungsfunktion abrufen:

```swift
let callRecordingFeature = call.api(RecordingFeature.self)
```

Um anschließend herauszufinden, ob der Anruf aufgezeichnet wird, prüfen Sie die Eigenschaft `isRecordingActive` von `callRecordingFeature`. Er gibt `Bool` zurück.

```swift
let isRecordingActive = callRecordingFeature.isRecordingActive;
```

Sie können die Aufzeichnung von Änderungen auch abonnieren, indem Sie den `RecordingFeatureDelegate`-Delegaten für Ihre Klasse mit dem Ereignis `didChangeRecordingState` implementieren:

```swift
callRecordingFeature.delegate = self

// didChangeRecordingState is a member of RecordingFeatureDelegate
public func recordingFeature(_ recordingFeature: RecordingFeature, didChangeRecordingState args: PropertyChangedEventArgs) {
    let isRecordingActive = recordingFeature.isRecordingActive
}
```

Wenn Sie die Aufzeichnung aus Ihrer Anwendung heraus starten möchten, folgen Sie bitte zunächst der [Übersicht über die Anrufaufzeichnung](../../../../concepts/voice-video-calling/call-recording.md), um die Schritte zur Einrichtung der Aufzeichnung von Anrufen zu erfahren.

Sobald Sie die Anrufaufzeichnung auf Ihrem Server eingerichtet haben, müssen Sie von Ihrer iOS-Anwendung aus den `ServerCallId`-Wert des Anrufs abrufen und ihn dann an Ihren Server senden, um den Aufzeichnungsprozess zu starten. Der `ServerCallId`-Wert kann mithilfe von `getServerCallId()` der `CallInfo`-Klasse verwendet werden, die im Klassenobjekt mit `getInfo()` gefunden werden kann.

```swift
let serverCallId = call.info.getServerCallId(){ (serverId, error) in }
// Send serverCallId to your recording server to start the call recording.
```

Wenn die Aufzeichnung vom Server aus gestartet wird, wird das Ereignis `didChangeRecordingState` ausgelöst und der Wert von `recordingFeature.isRecordingActive` wird `true` sein.

Genau wie beim Starten der Anrufaufzeichnung müssen Sie, wenn Sie die Aufzeichnung beenden möchten, die `ServerCallId` abrufen und an Ihren Aufzeichnungsserver senden, sodass dieser die Aufzeichnung beenden kann.

```swift
let serverCallId = call.info.getServerCallId(){ (serverId, error) in }
// Send serverCallId to your recording server to stop the call recording.
```

Wenn die Aufzeichnung vom Server aus beendet wird, wird das Ereignis `didChangeRecordingState` ausgelöst und der Wert von `recordingFeature.isRecordingActive` wird `false` sein.

## <a name="call-transcription"></a>Anruftranskription
> [!WARNING]
> Bis zur Version 1.1.0 und der Betaversion 1.1.0-beta.1 des ACS Calling iOS SDK wird `isTranscriptionActive` als Teil des `Call`-Objekts verwendet und `didChangeTranscriptionState` ist Teil des `CallDelegate`-Delegaten. Für die neuen Betaversionen wurden diese APIs als erweitertes Feature von `Call` verschoben, wie unten beschrieben.
> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Beta-Release des ACS Calling iOS SDK.

Die Anruftranskription ist ein erweitertes Feature der zentralen `Call`-API. Sie müssen zunächst das API-Objekt des Transkriptionsfeatures abrufen:

```swift
let callTranscriptionFeature = call.api(TranscriptionFeature.self)
```

Um anschließend herauszufinden, ob der Anruf transkribiert wird, prüfen Sie die Eigenschaft `isTranscriptionActive` von `callTranscriptionFeature`. Er gibt `Bool` zurück.

```swift
let isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive;
```

Sie können die Transkriptionsänderungen auch abonnieren, indem Sie den `TranscriptionFeatureDelegate`-Delegaten für Ihre Klasse mit dem Ereignis `didChangeTranscriptionState` implementieren:

```swift
callTranscriptionFeature.delegate = self

// didChangeTranscriptionState is a member of TranscriptionFeatureDelegate
public func transcriptionFeature(_ transcriptionFeature: TranscriptionFeature, didChangeTranscriptionState args: PropertyChangedEventArgs) {
    let isTranscriptionActive = transcriptionFeature.isTranscriptionActive
}
```

## <a name="subscribe-to-notifications"></a>Abonnieren von Benachrichtigungen

Sie können die meisten Objekte und Sammlungen abonnieren, um bei Änderungen von Werten benachrichtigt zu werden.

### <a name="properties"></a>Eigenschaften
Verwenden Sie den folgenden Code, um Ereignisse vom Typ `property changed` zu abonnieren:

```swift
call.delegate = self
// Get the property of the call state by getting on the call's state member
public func call(_ call: Call, didChangeState args: PropertyChangedEventArgs) {
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Sammlungen
Verwenden Sie den folgenden Code, um Ereignisse vom Typ `collection updated` zu abonnieren:

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func call(_ call: Call, didUpdateLocalVideoStreams args: LocalVideoStreamsUpdatedEventArgs) {
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
