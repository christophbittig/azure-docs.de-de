---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: rifox
ms.openlocfilehash: f2006869868a94b36078734756fd5e6992cd08b2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253878"
---
Steigen Sie in Azure Communication Services ein, indem Sie das Communication Services-SDK „Calling“ nutzen, um Ihrer App 1:1-Videoanrufe hinzuzufügen. Hier erfahren Sie, wie Sie einen Videoanruf mithilfe des Calling SDK von Azure Communication Services für iOS beginnen und beantworten.

## <a name="sample-code"></a>Beispielcode

Wenn Sie direkt zum Ende springen möchten, können Sie diese Schnellstartanleitung als Beispiel auf [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Video%20Calling) herunterladen.

## <a name="prerequisites"></a>Voraussetzungen
- Rufen Sie ein Azure-Konto mit einem aktiven Abonnement ab. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Einen Mac mit [Xcode](https://developer.apple.com/xcode/) zusammen mit einem gültigen in Ihrer Keychain installierten Entwicklerzertifikat.
- Erstellen Sie eine aktive Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../../create-communication-resource.md?tabs=windows&pivots=platform-azp).
- Erstellen Sie ein Benutzerzugriffstoken, um den Anrufclient zu instanziieren. Lernen Sie mehr über das [Erstellen und Verwalten von Benutzerzugriffstoken](../../../access-tokens.md?pivots=programming-language-csharp).

## <a name="setting-up"></a>Einrichten
### <a name="creating-the-xcode-project"></a>Erstellen des Xcode-Projekts
Erstellen Sie in Xcode ein neues iOS-Projekt, und wählen Sie die Vorlage Single View App (Einzelansicht-App) aus. In diesem Tutorial wird das [SwiftUI-Framework](https://developer.apple.com/xcode/swiftui/) verwendet – legen Sie daher „Sprache“ auf „Swift“ und „Benutzeroberfläche“ auf „SwiftUI“ fest. Im Rahmen dieses Schnellstarts werden keine Tests erstellt. Sie können die Option Tests einschließen daher deaktivieren.

:::image type="content" source="../../media/ios/xcode-new-ios-project.png" alt-text="Screenshot des Fensters „Neues Projekt“ in Xcode":::

### <a name="installing-cocoapods"></a>Installieren von CocoaPods
Verwenden Sie diese Anleitung, um [CocoaPods](https://guides.cocoapods.org/using/getting-started.html) auf Ihrem Mac zu installieren. 

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installieren des Pakets und der Abhängigkeiten mit CocoaPods
1. Um eine Podfile-Datei für Ihre Anwendung zu erstellen, öffnen Sie das Terminal, navigieren Sie zum Projektordner und führen Sie „pod init“ aus.

2. Fügen Sie der Podfile-Datei den folgenden Code hinzu und speichern Sie diese:

```
platform :ios, '13.0'
use_frameworks!

target 'VideoCallingQuickstart' do
  pod 'AzureCommunicationCalling', '~> 1.0.0'
end
```

3. Führen Sie „pod install“ aus.

4. Öffnen Sie mit Xcode Dateien vom Typ „.xcworkspace“ aus.


### <a name="request-access-to-the-microphone-and-camera"></a>Anfordern des Zugriffs auf Mikrofon und Kamera
Um auf Mikrofon und Kamera des Geräts zuzugreifen, müssen Sie die Liste der Informationseigenschaften Ihrer App mit `NSMicrophoneUsageDescription` und `NSCameraUsageDescription` aktualisieren. Legen Sie den zugehörigen Wert auf eine Zeichenfolge fest. Diese wird in den Dialog aufgenommen, mit dem das System den Zugriff beim Benutzer anfordert.

Klicken Sie mit der rechten Maustaste auf den Eintrag `Info.plist` der Projektstruktur, und wählen Sie anschließend „Öffnen als ...“ und anschließend „Quellcode“. Fügen Sie die folgenden Zeilen im Abschnitt `<dict>` der obersten Ebene hinzu, und speichern anschließend Sie die Datei.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
<key>NSCameraUsageDescription</key>
<string>Need camera access for video calling</string>
```

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks
Öffnen Sie die Datei `ContentView.swift` Ihres Projekts und fügen Sie am Anfang der Datei eine Deklaration vom Typ „Importieren“ hinzu, um die Bibliothek `AzureCommunicationCalling` und `AVFoundation` zu importieren. AVFoundation wird verwendet, um Audioberechtigungen aus Code zu erfassen.

```Swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>Objektmodell
Die folgenden Klassen und Schnittstellen befassen sich mit einigen der wichtigsten Features des Azure Communication Services Calling SDK für iOS.

| Name                         | Beschreibung                                                                                                                                                                        |
| :--------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                   | „CallClient“ ist der Haupteinstiegspunkt des Calling SDK.                                                                                                                         |
| CallAgent                    | CallAgent dient zum Starten und Verwalten von Anrufen.                                                                                                                                   |
| CommunicationTokenCredential | „CommunicationTokenCredential“ dient als tokengestützte Anmeldeinformation zum Instanziieren von „CallAgent“.                                                                                     |
| CommunicationIdentifier      | „CommunicationIdentifier“ wird zur Darstellung der Identität des Benutzers verwendet, wie u. a. die folgenden: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="create-the-call-agent"></a>Erstellen des Anruf-Agents
Ersetzen Sie die Implementierung der ContentView-Struktur durch einige einfache Benutzeroberflächen-Steuerelemente, die einem Benutzer das Initiieren und Beenden eines Anrufs ermöglichen. In diesem Schnellstart fügen wir eine Geschäftslogik an diese Steuerelemente an.

```Swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?
    @State var deviceManager: DeviceManager?
    @State var localVideoStream:[LocalVideoStream]?
    @State var incomingCall: IncomingCall?
    @State var sendingVideo:Bool = false
    @State var errorMessage:String = "Unknown"

    @State var remoteVideoStreamData:[Int32:RemoteVideoStreamData] = [:]
    @State var previewRenderer:VideoStreamRenderer? = nil
    @State var previewView:RendererView? = nil
    @State var remoteRenderer:VideoStreamRenderer? = nil
    @State var remoteViews:[RendererView] = []
    @State var remoteParticipant: RemoteParticipant?
    @State var remoteVideoSize:String = "Unknown"
    @State var isIncomingCall:Bool = false
    
    @State var callObserver:CallObserver?
    @State var remoteParticipantObserver:RemoteParticipantObserver?

    var body: some View {
        NavigationView {
            ZStack{
                Form {
                    Section {
                        TextField("Who would you like to call?", text: $callee)
                        Button(action: startCall) {
                            Text("Start Call")
                        }.disabled(callAgent == nil)
                        Button(action: endCall) {
                            Text("End Call")
                        }.disabled(call == nil)
                        Button(action: toggleLocalVideo) {
                            HStack {
                                Text(sendingVideo ? "Turn Off Video" : "Turn On Video")
                            }
                        }
                    }
                }
                // Show incoming call banner
                if (isIncomingCall) {
                    HStack() {
                        VStack {
                            Text("Incoming call")
                                .padding(10)
                                .frame(maxWidth: .infinity, alignment: .topLeading)
                        }
                        Button(action: answerIncomingCall) {
                            HStack {
                                Text("Answer")
                            }
                            .frame(width:80)
                            .padding(.vertical, 10)
                            .background(Color(.green))
                        }
                        Button(action: declineIncomingCall) {
                            HStack {
                                Text("Decline")
                            }
                            .frame(width:80)
                            .padding(.vertical, 10)
                            .background(Color(.red))
                        }
                    }
                    .frame(maxWidth: .infinity, alignment: .topLeading)
                    .padding(10)
                    .background(Color.gray)
                }
                ZStack{
                    VStack{
                        ForEach(remoteViews, id:\.self) { renderer in
                            ZStack{
                                VStack{
                                    RemoteVideoView(view: renderer)
                                        .frame(width: .infinity, height: .infinity)
                                        .background(Color(.lightGray))
                                }
                            }
                            Button(action: endCall) {
                                Text("End Call")
                            }.disabled(call == nil)
                            Button(action: toggleLocalVideo) {
                                HStack {
                                    Text(sendingVideo ? "Turn Off Video" : "Turn On Video")
                                }
                            }
                        }
                        
                    }.frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topLeading)
                    VStack{
                        if(sendingVideo)
                        {
                            VStack{
                                PreviewVideoStream(view: previewView!)
                                    .frame(width: 135, height: 240)
                                    .background(Color(.lightGray))
                            }
                        }
                    }.frame(maxWidth:.infinity, maxHeight:.infinity,alignment: .bottomTrailing)
                }
            }
     .navigationBarTitle("Video Calling Quickstart")
        }.onAppear{
            // Authenticate the client
            
            // Initialize the CallAgent and access Device Manager
            
            // Ask for permissions
        }
    }
}

//Functions and Observers

struct PreviewVideoStream: UIViewRepresentable {
    let view:RendererView
    func makeUIView(context: Context) -> UIView {
        return view
    }
    func updateUIView(_ uiView: UIView, context: Context) {}
}

struct RemoteVideoView: UIViewRepresentable {
    let view:RendererView
    func makeUIView(context: Context) -> UIView {
        return view
    }
    func updateUIView(_ uiView: UIView, context: Context) {}
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

### <a name="authenticate-the-client"></a>Authentifizieren des Clients
Um eine CallAgent-Instanz zu initialisieren, benötigen wir ein Benutzerzugriffstoken, mit dem wir Anrufe tätigen und empfangen können. Wenn Sie noch über kein Token verfügen, finden Sie unter [Benutzerzugriffstoken](../../../access-tokens.md?pivots=programming-language-csharp) weitere Informationen.

Sobald Sie über ein Token verfügen, fügen Sie den folgenden Code zum `onAppear`-Rückruf in `ContentView.swift` hinzu: Sie müssen `<USER ACCESS TOKEN>` durch ein gültiges Benutzerzugriffstoken** für Ihre Ressource ersetzen:

```Swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}
```

### <a name="initialize-the-callagent-and-access-device-manager"></a>Initialisieren des CallAgent und Zugreifen auf Geräte-Manager
Um eine CallAgent-Instanz anhand eines CallClient zu erzeugen, müssen Sie die `callClient.createCallAgent`-Methode verwenden, die ein CallAgent-Objekt asynchron zurückgibt, nachdem es initialisiert wurde. DeviceManager ermöglicht Ihnen das Aufzählen lokaler Geräte, die in einem Anruf zur Übertragung von Audio- bzw. Videostreams verwendet werden können. Mithilfe des Geräte-Managers können Sie auch beim Benutzer die Berechtigung für den Zugriff auf Mikrofon/Kamera anfordern. 

```Swift
self.callClient = CallClient()
self.callClient?.createCallAgent(userCredential: userCredential!) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }

    else {
        self.callAgent = agent
        print("Call agent successfully created.")
        self.callAgent!.delegate = incomingCallHandler
        self.callClient?.getDeviceManager { (deviceManager, error) in
            if (error == nil) {
                print("Got device manager instance")
                self.deviceManager = deviceManager
            } else {
                print("Failed to get device manager instance")
            }
        }
    }
}
```

### <a name="ask-for-permissions"></a>Anfordern von Berechtigungen
Wir müssen dem `onAppear` Rückruf den folgenden Code hinzufügen, um Berechtigungen für Audio und Video anzufordern. 

```Swift
AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
    if granted {
        AVCaptureDevice.requestAccess(for: .video) { (videoGranted) in
            /* NO OPERATION */
        }
    }
}
```

## <a name="display-local-video"></a>Lokales Video anzeigen
Vor dem Starten eines Anrufs können Sie Einstellungen im Zusammenhang mit Videos verwalten. In dieser Schnellstartanleitung wird die Implementierung des Umstiegs auf lokale Videos vor oder während eines Anrufs vorgestellt. 

Zuerst müssen wir mit `deviceManager` auf lokale Kameras zugreifen. Sobald die gewünschte Kamera ausgewählt ist, können wir eine `LocalVideoStream` sowie `VideoStreamRenderer` erstellen und dann an `previewView` anfügen. Während des Anrufs können wir `startVideo` oder `stopVideo` verwenden, um das Senden von `LocalVideoStream` an Remote-Teilnehmer zu starten oder zu beenden. Diese Funktion funktioniert auch mit der Verarbeitung eingehender Aufrufe. 

```Swift
func toggleLocalVideo() {
    // toggling video before call starts
    if (call == nil)
    {
        if(!sendingVideo)
        {
            self.callClient = CallClient()
            self.callClient?.getDeviceManager { (deviceManager, error) in
                if (error == nil) {
                    print("Got device manager instance")
                    self.deviceManager = deviceManager
                } else {
                    print("Failed to get device manager instance")
                }
            }
            guard let deviceManager = deviceManager else {
                return
            }
            let camera = deviceManager.cameras.first
            let scalingMode = ScalingMode.fit
            if (self.localVideoStream == nil) {
                self.localVideoStream = [LocalVideoStream]()
            }
            localVideoStream!.append(LocalVideoStream(camera: camera!))
            previewRenderer = try! VideoStreamRenderer(localVideoStream: localVideoStream!.first!)
            previewView = try! previewRenderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
            self.sendingVideo = true
        }
        else{
            self.sendingVideo = false
            self.previewView = nil
            self.previewRenderer!.dispose()
            self.previewRenderer = nil
        }
    }
    // toggle local video during the call
    else{
        if (sendingVideo) {
            call!.stopVideo(stream: localVideoStream!.first!) { (error) in
                if (error != nil) {
                    print("cannot stop video")
                }
                else {
                    self.sendingVideo = false
                    self.previewView = nil
                    self.previewRenderer!.dispose()
                    self.previewRenderer = nil
                }
            }
        }
        else {
            guard let deviceManager = deviceManager else {
                return
            }
            let camera = deviceManager.cameras.first
            let scalingMode = ScalingMode.fit
            if (self.localVideoStream == nil) {
                self.localVideoStream = [LocalVideoStream]()
            }
            localVideoStream!.append(LocalVideoStream(camera: camera!))
            previewRenderer = try! VideoStreamRenderer(localVideoStream: localVideoStream!.first!)
            previewView = try! previewRenderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
            call!.startVideo(stream:(localVideoStream?.first)!) { (error) in
                if (error != nil) {
                    print("cannot start video")
                }
                else {
                    self.sendingVideo = true
                }
            }
        }
    }
}
```

## <a name="place-an-outgoing-call"></a>Tätigen eines ausgehenden Anrufs
Die Methode `startCall` wird als die Aktion festgelegt, die ausgeführt wird, wenn auf die Schaltfläche Start Call (Anruf beginnen) getippt wird. In dieser Schnellstartanleitung sind ausgehende Aufrufe standardmäßig nur Audiodaten. Um einen Anruf per Video zu starten, müssen wir `VideoOptions` mit `LocalVideoStream` festlegen und an `startCallOptions` übergeben, um anfängliche Optionen für den Anruf festzulegen.

```Swift
func startCall() {
        let startCallOptions = StartCallOptions()
        if(sendingVideo)
        {
            if (self.localVideoStream == nil) {
                self.localVideoStream = [LocalVideoStream]()
            }
            let videoOptions = VideoOptions(localVideoStreams: localVideoStream!)
            startCallOptions.videoOptions = videoOptions
        }
        let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(self.callee)]
        self.callAgent?.startCall(participants: callees, options: startCallOptions) { (call, error) in
            setCallAndObersever(call: call, error: error)
        }
    }
```

`CallObserver` und `RemotePariticipantObserver` werden verwendet, um Mid-Call-Ereignisse und Remote-Teilnehmer zu verwalten. Wir legen die Beobachter in der `setCallAndOberserver` Funktion fest. 

```Swift
func setCallAndObersever(call:Call!, error:Error?) {
    if (error == nil) {
        self.call = call
        self.callObserver = CallObserver(self)
        self.call!.delegate = self.callObserver
        self.remoteParticipantObserver = RemoteParticipantObserver(self)
    } else {
        print("Failed to get call object")
    }
}
```

## <a name="answer-an-incoming-call"></a>Annehmen eines eingehenden Anrufs
Implementieren Sie zum Beantworten eines eingehenden Anrufs ein `IncomingCallHandler`, um das Banner für eingehende Anrufe anzuzeigen, um den Anruf zu beantworten oder zu ablehnen. Legen Sie die folgende Implementierung in `IncomingCallHandler.swift` ab. 

```Swift
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate {
    public var contentView: ContentView?
    private var incomingCall: IncomingCall?

    private static var instance: IncomingCallHandler?
    static func getOrCreateInstance() -> IncomingCallHandler {
        if let c = instance {
            return c
        }
        instance = IncomingCallHandler()
        return instance!
    }

    private override init() {}
    
    public func callAgent(_ callAgent: CallAgent, didRecieveIncomingCall incomingCall: IncomingCall) {
        self.incomingCall = incomingCall
        self.incomingCall?.delegate = self
        contentView?.showIncomingCallBanner(self.incomingCall!)
    }
    
    public func callAgent(_ callAgent: CallAgent, didUpdateCalls args: CallsUpdatedEventArgs) {
        if let removedCall = args.removedCalls.first {
            contentView?.callRemoved(removedCall)
            self.incomingCall = nil
        }
    }
}
```

Wir müssen eine Instanz von `IncomingCallHandler` erstellen, indem wir den folgenden Code zum `onAppear` Rückruf in `ContentView.swift` hinzufügen:

```Swift
let incomingCallHandler = IncomingCallHandler.getOrCreateInstance()
incomingCallHandler.contentView = self
```

Legen Sie einen Delegaten auf den CallAgent fest, nachdem der CallAgent erfolgreich erstellt wurde:

```Swift
self.callAgent!.delegate = incomingCallHandler
```

Sobald es einen eingehenden Aufruf gibt, wird `IncomingCallHandler` die Funktion `showIncomingCallBanner` zum Anzeigen der Schaltfläche `answer` und `decline` aufrufen. 

```Swift
func showIncomingCallBanner(_ incomingCall: IncomingCall?) {
    isIncomingCall = true
    self.incomingCall = incomingCall
}
```

Die Aktionen, die an `answer` und `decline` angefügt sind, werden wie folgt implementiert. Um den Anruf mit Video zu beantworten, müssen wir das lokale Video aktivieren und die Optionen von `AcceptCallOptions` mit `localVideoStream` festlegen. 

```Swift
func answerIncomingCall() {
    isIncomingCall = false
    let options = AcceptCallOptions()
    if (self.incomingCall != nil) {
        guard let deviceManager = deviceManager else {
            return
        }        
        if (self.localVideoStream == nil) {
            self.localVideoStream = [LocalVideoStream]()
        }
        if(sendingVideo)
        {
            let camera = deviceManager.cameras.first
            localVideoStream!.append(LocalVideoStream(camera: camera!))
            let videoOptions = VideoOptions(localVideoStreams: localVideoStream!)
            options.videoOptions = videoOptions
        }
        self.incomingCall!.accept(options: options) { (call, error) in
            setCallAndObersever(call: call, error: error)
        }
    }
}

func declineIncomingCall(){
    self.incomingCall!.reject { (error) in }
    isIncomingCall = false
}
```

## <a name="remote-participant-video-streams"></a>Videostreams von Remote-Teilnehmern
Wir können eine `RemoteVideoStreamData` Klasse zum Verarbeiten von Rendering-Videostreams von Remote-Teilnehmern erstellen. 

```Swift
public class RemoteVideoStreamData : NSObject, RendererDelegate {
    public func videoStreamRenderer(didFailToStart renderer: VideoStreamRenderer) {
        owner.errorMessage = "Renderer failed to start"
    }
    
    private var owner:ContentView
    let stream:RemoteVideoStream
    var renderer:VideoStreamRenderer? {
        didSet {
            if renderer != nil {
                renderer!.delegate = self
            }
        }
    }
    
    var views:[RendererView] = []
    init(view:ContentView, stream:RemoteVideoStream) {
        owner = view
        self.stream = stream
    }
    
    public func videoStreamRenderer(didRenderFirstFrame renderer: VideoStreamRenderer) {
        let size:StreamSize = renderer.size
        owner.remoteVideoSize = String(size.width) + " X " + String(size.height)
    }
}
```

## <a name="subscribe-to-events"></a>Abonnieren von Ereignissen
Wir können eine `CallObserver` Klasse implementieren, um eine Sammlung von Ereignissen zu abonnieren, über die wir benachrichtigt werden, wenn sich Werte während des Anrufs ändern. 

```Swift
public class CallObserver: NSObject, CallDelegate, IncomingCallDelegate {
    private var owner: ContentView
    init(_ view:ContentView) {
            owner = view
    }
        
    public func call(_ call: Call, didChangeState args: PropertyChangedEventArgs) {
        if(call.state == CallState.connected) {
            initialCallParticipant()
        }
    }

    // render remote video streams when remote participant changes
    public func call(_ call: Call, didUpdateRemoteParticipant args: ParticipantsUpdatedEventArgs) {
        for participant in args.addedParticipants {
            participant.delegate = owner.remoteParticipantObserver
            for stream in participant.videoStreams {
                if !owner.remoteVideoStreamData.isEmpty {
                    return
                }
                let data:RemoteVideoStreamData = RemoteVideoStreamData(view: owner, stream: stream)
                let scalingMode = ScalingMode.fit
                data.renderer = try! VideoStreamRenderer(remoteVideoStream: stream)
                let view:RendererView = try! data.renderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
                data.views.append(view)
                self.owner.remoteViews.append(view)
                owner.remoteVideoStreamData[stream.id] = data
            }
            owner.remoteParticipant = participant
        }
    }

    // Handle remote video streams when the call is connected
    public func initialCallParticipant() {
        for participant in owner.call!.remoteParticipants {
            participant.delegate = owner.remoteParticipantObserver
            for stream in participant.videoStreams {
                renderRemoteStream(stream)
            }
            owner.remoteParticipant = participant
        }
    }
    
    //create render for RemoteVideoStream and attach it to view
    public func renderRemoteStream(_ stream: RemoteVideoStream!) {
        if !owner.remoteVideoStreamData.isEmpty {
            return
        }
        let data:RemoteVideoStreamData = RemoteVideoStreamData(view: owner, stream: stream)
        let scalingMode = ScalingMode.fit
        data.renderer = try! VideoStreamRenderer(remoteVideoStream: stream)
        let view:RendererView = try! data.renderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
        self.owner.remoteViews.append(view)
        owner.remoteVideoStreamData[stream.id] = data
    }
}
```

## <a name="remote-participant-management"></a>Verwaltung von Remote-Teilnehmern
Alle Remoteteilnehmer werden durch den Typ `RemoteParticipant` dargestellt und sind über die Sammlung `remoteParticipants` für eine Anrufinstanz verfügbar. Wir können eine Klasse `RemoteParticipantObserver` implementieren, um Updates für Remote-Videostreams von Remote-Teilnehmern zu abonnieren. 

```Swift
public class RemoteParticipantObserver : NSObject, RemoteParticipantDelegate {
    private var owner:ContentView
    init(_ view:ContentView) {
        owner = view
    }

    public func renderRemoteStream(_ stream: RemoteVideoStream!) {
        let data:RemoteVideoStreamData = RemoteVideoStreamData(view: owner, stream: stream)
        let scalingMode = ScalingMode.fit
        data.renderer = try! VideoStreamRenderer(remoteVideoStream: stream)
        let view:RendererView = try! data.renderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
        self.owner.remoteViews.append(view)
        owner.remoteVideoStreamData[stream.id] = data
    }
    
    // render RemoteVideoStream when remote participant turns on the video, dispose the renderer when remote video is off
    public func remoteParticipant(_ remoteParticipant: RemoteParticipant, didUpdateVideoStreams args: RemoteVideoStreamsEventArgs) {
        for stream in args.addedRemoteVideoStreams {
            renderRemoteStream(stream)
        }
        for stream in args.removedRemoteVideoStreams {
            for data in owner.remoteVideoStreamData.values {
                data.renderer?.dispose()
            }
            owner.remoteViews.removeAll()
        }
    }
}
```

## <a name="run-the-code"></a>Ausführen des Codes
Sie können Ihre App auf dem iOS-Simulator erstellen und ausführen, indem Sie „Produkt“ und anschließend „Ausführen“ auswählen oder die Tastenkombination „⌘-R“ verwenden.
