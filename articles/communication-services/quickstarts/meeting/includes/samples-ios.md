---
title: Verwenden von Azure Communication Services Teams Embed für iOS
description: In dieser Übersicht erfahren Sie, wie Sie die Azure Communication Services-Bibliothek zum Einbetten von Teams für iOS verwenden.
author: palatter
ms.author: palatter
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 246c62ec25a788c7767da0c8fdf23b42db321f0b
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2021
ms.locfileid: "113215145"
---
## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../create-communication-resource.md).
- Ein `User Access Token`, um den Anrufclient zu aktivieren. Weitere Informationen zum [Abrufen eines `User Access Token`](../../access-tokens.md)
- Gehen Sie den Schnellstart [Erste Schritte beim Hinzufügen von Teams Embed zu Ihrer Anwendung](../getting-started-with-teams-embed.md) durch.

## <a name="joining-a-group-call"></a>Teilnehmen an einem Gruppenanruf

Die Teilnahme an Gruppenanrufen ist durch die Bereitstellung von `MeetingUIClientGroupCallLocator` und `MeetingUIClientGroupCallJoinOptions` für die `meetingUIClient?.join`-API möglich. Der Gruppenanruf gibt kein Signal für die anderen Teilnehmer aus. Der Benutzer tritt dem Anruf lautlos bei.

Erstellen Sie in **ViewController.swift** eine Schaltfläche im `viewDidLoad`-Rückruf.

```swift
class ViewController: UIViewController, MeetingUIClientCallDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let joinGroupCallButton = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
        joinGroupCallButton.backgroundColor = .black
        joinGroupCallButton.setTitle("Join Group Call", for: .normal)
        joinGroupCallButton.addTarget(self, action: #selector(joinGroupCallTapped), for: .touchUpInside)
        
        joinGroupCallButton.translatesAutoresizingMaskIntoConstraints = false
        self.view.addSubview(joinGroupCallButton)
        joinGroupCallButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        joinGroupCallButton.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
    }
```

Erstellen Sie ein Outlet für die Schaltfläche in **ViewController.swift** für die Klasse `ViewController`.

```swift
@IBAction func joinGroupCallTapped(_ sender: UIButton) {
    joinGroupCall()
}

private func joinGroupCall() {
    // Add join meeting logic
}
```

Das Einrichten des Clients und das Bereitstellen des Tokens erfolgt auf die gleiche Weise wie bei der Besprechungsbeitritts-API, die in der [Schnellstartanleitung](../getting-started-with-teams-embed.md) beschrieben wird. 

Die Methode `joinGroupCall` wird als die Aktion festgelegt, die bei Tippen auf die Schaltfläche *An Gruppenanruf teilnehmen* ausgeführt wird.
Erstellen Sie einen `MeetingUIClientGroupCallLocator`, und konfigurieren Sie die Teilnahmeoptionen mithilfe von `MeetingUIClientGroupCallJoinOptions`.
Beachten Sie, dass `<GROUP_ID>` durch eine UUID-Zeichenfolge ersetzt werden muss. Die Zeichenfolge der Gruppen-ID muss im GUID- oder UUID-Format vorliegen.
```swift
private func joinGroupCall() {
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: false, enableCallStagingScreen: false, enableCallRosterDelegate: false)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: <GROUP_ID>)
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall = meetingUIClientCall
            } else {
                print("Join meeting failed: \(error!)")
            }
        }
    })
}
```

## <a name="teams-embed-call-or-meeting-status-events-capturing"></a>Erfassen des Status von Teams Embed-Anrufen oder -Besprechungen

Der Status von beigetretenen Gruppenanrufen oder Besprechungen kann vom Delegaten `MeetingUIClientCallDelegate` erfasst werden. Der Status umfasst den Verbindungsstatus, die Teilnehmeranzahl sowie Modalitäten wie den Mikrofon- oder Kamerastatus.   

Fügen Sie Ihrer Klasse den Delegaten `MeetingUIClientCallDelegate` hinzu sowie die erforderlichen Variablen.

```swift
class ViewController: UIViewController, MeetingUIClientCallDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
    
    //Rest of the UIViewController code
}
```

Nachdem Sie dem Anruf oder der Besprechung erfolgreich beigetreten sind, legen Sie `self.meetingUIClientCall?.meetingUIClientCallDelegate` auf `self` fest.

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: false, enableCallStagingScreen: false, enableCallRosterDelegate: false)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

Implementieren Sie die für Ihre App erforderlichen Protokollmethoden von `MeetingUIClientCallDelegate`, und fügen Sie für die nicht benötigten Methoden Stubs hinzu.

```swift
    func meetingUIClient(didUpdateCallState callState: MeetingUIClientCallState) {
        switch callState {
        case .connecting:
            print("Call state has changed to 'Connecting'")
        case .connected:
            print("Call state has changed to 'Connected'")
        case .waitingInLobby:
            print("Call state has changed to 'Waiting in Lobby'")
        case .ended:
            print("Call state has changed to 'Ended'")
        }
    }
    
    func meetingUIClient(didUpdateRemoteParticipantCount remoteParticipantCount: UInt) {
        print("Remote participant count has changed to: \(remoteParticipantCount)")
    }
    
    func onIsMutedChanged() {
    }
    
    func onIsSendingVideoChanged() {
    }
    
    func onIsHandRaisedChanged(_ participantIds: [Any]) {
    }
```

## <a name="bring-your-own-identity-from-the-app-to-the-participants-in-the-call"></a>Verwenden Ihrer eigenen Identität aus der App für die Teilnehmer des Anrufs

Die App kann den Teilnehmern des Anrufs oder der Besprechung Identitätswerte zuweisen und die Standardwerte überschreiben. Die Werte beinhalten Avatar, Name und Untertitel.  

### <a name="assigning-avatars-for-call-participants"></a>Zuweisen von Avataren für Teilnehmer des Anrufs

Fügen Sie Ihrer Klasse den `MeetingUIClientCallIdentityProviderDelegate` hinzu.

```swift
class ViewController: UIViewController, MeetingUIClientCallIdentityProviderDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Nachdem Sie dem Anruf oder der Besprechung erfolgreich beigetreten sind, legen Sie `self.meetingUIClientCall?.MeetingUIClientIdentityProviderDelegate` auf `self` fest.

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: false, enableCallStagingScreen: false, enableCallRosterDelegate: false)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallIdentityProviderDelegate = self
            }
        }
    })
}
```

Fügen Sie die Protokollmethode `avatarFor` hinzu, und implementieren Sie diese. Ordnen Sie `identifier` jeweils dem entsprechenden Avatar zu.

```swift
    func avatarFor(identifier: CommunicationIdentifier, size: MeetingUIClientAvatarSize, completionHandler: @escaping (UIImage?) -> Void) {
        if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:teamsvisitor:")) {
                // Anonymous teams user will start with prefix 8:teamsvistor:
                let image = UIImage (named: "avatarPink")
                completionHandler(image!)
            }
            else if (userIdentifier.identifier.starts(with: "8:orgid:")) {
                // OrgID user will start with prefix 8:orgid:
                let image = UIImage (named: "avatarDoctor")
                completionHandler(image!)
            }
            else if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // ACS user will start with prefix 8:acs:
                let image = UIImage (named: "avatarGreen")
                completionHandler(image!)
            } else {
                completionHandler(nil)
            }
        } else {
            completionHandler(nil)
        }
    }

```

Fügen Sie der Klasse weitere erforderliche MeetingUIClientCallIdentityProviderDelegate-Protokollmethoden hinzu. Diese können eine leere Implementierung aufweisen.
```swift
    func displayNameFor(identifier: CommunicationIdentifier, completionHandler: @escaping (String?) -> Void) {
    }
    
    func subTitleFor(identifier: CommunicationIdentifier, completionHandler: @escaping (String?) -> Void) {
    }
```

## <a name="receive-information-about-user-actions-in-the-ui-and-add-your-own-custom-functionalities"></a>Empfangen von Informationen zu Benutzeraktionen in der Benutzeroberfläche und Hinzufügen eigener benutzerdefinierter Funktionen

### <a name="call-screen"></a>Anrufbildschirm 
Die `MeetingUIClientCallUserEventDelegate`-Delegatmethoden werden für Benutzeraktionen im Profil des Remoteteilnehmers aufgerufen.
Wenn Sie dem Anruf oder der Besprechung beigetreten sind, legen Sie die Beitrittsoptionseigenschaft `enableNamePlateOptionsClickDelegate` auf `true` fest.
Wenn Sie diese Eigenschaft festlegen, werden die Namensschildoptionen im Profil des Remoteteilnehmers sowie `MeetingUIClientCallUserEventDelegate` aktiviert.

Fügen Sie Ihrer Klasse den `MeetingUIClientCallUserEventDelegate` hinzu.

```swift
class ViewController: UIViewController, MeetingUIClientCallUserEventDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Nachdem Sie dem Anruf oder der Besprechung erfolgreich beigetreten sind, legen Sie `self.meetingUIClientCall?.meetingUIClientCallUserEventDelegate` auf `self` fest.

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: false, enableCallRosterDelegate: false)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallUserEventDelegate = self
            }
        }
    })
}
```

Fügen Sie die Protokollmethode `onNamePlateOptionsClicked` hinzu, und implementieren Sie diese. Ordnen Sie `identifier` jeweils dem entsprechenden Anrufteilnehmer zu.
Tippen Sie im Hauptbildschirm des Anrufs auf die Benutzerkachel oder den Text des Benutzertitels, um diese Methode aufzurufen.

```swift
func onNamePlateOptionsClicked(identifier: CommunicationIdentifier) {
    if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // Custom behavior based on the user here.
                print("Acs user tile clicked")
            }
        }
}
```

Fügen Sie die Protokollmethode `onParticipantViewLongPressed` hinzu, und implementieren Sie diese. Ordnen Sie `identifier` jeweils dem entsprechenden Anrufteilnehmer zu.
Drücken Sie im Hauptbildschirm des Anrufs länger auf die Benutzerkachel, um diese Methode aufzurufen.

```swift
func onParticipantViewLongPressed(identifier: CommunicationIdentifier) {
    if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // Custom behavior based on the user here.
                print("Acs user tile clicked")
            }
        }
}
```
### <a name="call-roster"></a>Anrufliste 
Die `MeetingUIClientCallRosterDelegate`-Delegatmethoden werden für Benutzeraktionen für einen Teilnehmer in der Liste aufgerufen.
Wenn Sie dem Anruf oder der Besprechung beigetreten sind, legen Sie die Beitrittsoptionseigenschaft `enableCallRosterDelegate` auf `true` fest.
Wenn Sie diese Eigenschaft festlegen, werden die Namensschildoptionen im Profil des Remoteteilnehmers sowie `MeetingUIClientCallRosterDelegate` aktiviert.

Fügen Sie Ihrer Klasse den `MeetingUIClientCallRosterDelegate` hinzu.

```swift
class ViewController: UIViewController, MeetingUIClientCallRosterDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Nachdem Sie dem Anruf oder der Besprechung erfolgreich beigetreten sind, legen Sie `self.meetingUIClientCall?.meetingUIClientCallRosterDelegate` auf `self` fest.

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: false, enableCallStagingScreen: false, enableCallRosterDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallRosterDelegate = self
            }
        }
    })
}
```

Fügen Sie die Protokollmethode `onCallParticipantCellTapped` hinzu, und implementieren Sie diese. Ordnen Sie `identifier` jeweils dem entsprechenden Anrufteilnehmer zu.
Diese Methode wird bei einem einzelnen Tippen auf einen Teilnehmer in der Anrufliste aufgerufen.

```swift
func onCallParticipantCellTapped(identifier: CommunicationIdentifier) {
    if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // Custom behavior based on the user here.
                print("Acs user tile clicked")
            }
        }
}
```
## <a name="user-experience-customization"></a>Anpassung der Benutzererfahrung

Sie können die Benutzeroberfläche im SDK anpassen, indem Sie App-spezifische Symbole bereitstellen. 

### <a name="customize-ui-icons-in-a-call-or-meeting"></a>Anpassen der bei einem Anruf oder einer Besprechung angezeigten UI-Symbole

Die bei einem Anruf oder einer Besprechung angezeigten Symbole können mithilfe der Methode `public func set(iconConfig: Dictionary<MeetingUIClientIconType, String>)` angepasst werden, die in `MeetingUIClient` verfügbar gemacht wird.
Eine Liste der anpassbaren Symbole finden Sie in `MeetingUIClientIconType`.

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Legen Sie nach der Initialisierung von „meetingUIClient“ die Symbolkonfiguration `meetingUIClient?.set(iconConfig: self.getIconConfig())` für die in `MeetingUIClientIconType` unterstützten Anrufsymbole fest.

```swift
private func initMeetingUIClient() {
    meetingUIClient = MeetingUIClient(with: credential)
    meetingUIClient?.set(iconConfig: self.getIconConfig())
}

func getIconConfig() -> Dictionary<MeetingUIClientIconType, String> {
    var iconConfig = Dictionary<MeetingUIClientIconType, String>()
    iconConfig.updateValue("camera_fill", forKey: MeetingUIClientIconType.VideoOn)
    iconConfig.updateValue("camera_off", forKey: MeetingUIClientIconType.VideoOff)
    iconConfig.updateValue("microphone_fill", forKey: MeetingUIClientIconType.MicOn)
    iconConfig.updateValue("microphone_off", forKey: MeetingUIClientIconType.MicOff)
    iconConfig.updateValue("speaker_fill", forKey: MeetingUIClientIconType.Speaker)
    return iconConfig
}
```

## <a name="perform-operations-with-the-call"></a>Ausführen von Vorgängen für Anrufe

Die Steuerelemente für Anrufe werden über die Methoden in `MeetingUIClientCall` verfügbar gemacht.
Diese Methoden eignen sich zum Steuern von Anrufaktionen, wenn die Benutzeroberfläche mithilfe der Anpassungsdelegaten in `MeetingUIClient` angepasst wurden.

Fügen Sie den Anrufen die erforderlichen Variablen hinzu.
```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Weisen Sie der Variablen `self.meetingUIClientCall` den `meetingUIClientCall`-Wert aus der `join`-Methode `completionHandler` zu.
```swift
private func joinGroupCall() {
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true, enableCallRosterDelegate: false)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```
### <a name="mute-and-unmute"></a>Stummschalten und Aufheben der Stummschaltung

Rufen Sie die `mute`-Methode auf, um das Mikrofon für einen aktiven Anruf, sofern vorhanden, stummzuschalten.
Der Status des Mikrofons wird in der `onIsMutedChanged`-Methode von `MeetingUIClientCallDelegate` in „Benachrichtigt“ geändert.

```swift
// Mute the microphone for an active call.
public func mute(completionHandler: @escaping (Error?) -> Void)

    meetingUIClientCall?.mute { [weak self] (error) in
        if error != nil {
            print("Mute call failed: \(error!)")
        }
}
```

Rufen Sie die `unmute`-Methode auf, um die Stummschaltung des Mikrofons für einen aktiven Anruf, sofern vorhanden, aufzuheben.

```swift
// Unmute the microphone for an active call.
public func unmute(completionHandler: @escaping (Error?) -> Void)

meetingUIClientCall?.unmute { [weak self] (error) in
    if error != nil {
        print("Unmute call failed: \(error!)")
    }
}
```

### <a name="other-operations-available-in-from-the--meetinguiclientcall-class"></a>Weitere in der Klasse `MeetingUIClientCall` verfügbare Vorgänge

```swift
// Start the video for an active call.
public func startVideo(completionHandler: @escaping (Error?) -> Void)

// Stop the video for an active call.
public func startVideo(completionHandler: @escaping (Error?) -> Void)

// Set the preferred audio route in the call for self user.
public func setAudio(route: MeetingUIClientAudioRoute, completionHandler: @escaping (Error?) -> Void)

// Raise the hand of current user for an active call.
public func raiseHand(completionHandler: @escaping (Error?) -> Void)

// Lower the hand of current user for an active call.
// public func lowerHand(completionHandler: @escaping (Error?) -> Void)

// Show the call roster for an active call.
public func showCallRoster(completionHandler: @escaping (Error?) -> Void)

// Change the layout in the call for self user.
public func getSupportedLayoutModes() -> [MeetingUIClientLayoutMode]
public func changeLayout(mode: MeetingUIClientLayoutMode, completionHandler: @escaping (Error?) -> Void)

// Hang up the call or leave the meeting.
public func hangUp(completionHandler: @escaping (Error?) -> Void)
```

## <a name="use-teams-embed-sdk-and-azure-communication-calling-sdk-in-the-same-app"></a>Verwenden des Teams Embed SDK und des Azure Communication Calling SDK in derselben App

Das Teams Embed SDK stellt auch das Azure Communication Calling SDK (ACS) bereit, mit dem beide SDK-Features in derselben App verwendet werden können. Es kann immer nur ein SDK initialisiert und verwendet werden. Eine Initialisierung und gleichzeitige Verwendung beider SDKs führt zu unerwartetem Verhalten. 

Importieren Sie `TeamsAppSDK`, um über das Teams Embed SDK auf das Azure Communication Calling SDK zuzugreifen. 
```swift
import TeamsAppSDK
```

Deklarieren Sie Variablen für die ACS-Verwendung.
```swift
class ViewController: UIViewController {

    private var callClient: CallClient?
    private var callAgent: CallAgent?
    private var call: Call?
    
    //Rest of the UIViewController code
}
    
```

Die Initialisierung erfolgt durch Erstellen eines neuen `CallClient`. Fügen Sie diesen der Methode `viewDidLoad` oder einer anderen Methode hinzu.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    self.callClient = CallClient()
}
```
Verwenden Sie alle ACS-APIs entsprechend der Beschreibung in der Dokumentation. Eine Beschreibung der API-Nutzung ist nicht Teil dieser Dokumentation. 

Löschen Sie das ACS SDK, und legen Sie `nil` auf die Variablen des SDK fest, wenn Sie es nicht mehr benötigen oder die App das Teams Embed SDK verwenden muss.
```swift
    public func disposeAcsSdk()
    {
        self.call = nil
        self.callAgent?.dispose()
        self.callClient?.dispose()
        self.callAgent = nil
        self.callClient = nil
    }

```

Die Initialisierung des Teams Embed SDK erfolgt auch während der Erstellung von `MeetingUIClient`. Fügen Sie diesen der Methode `viewDidLoad` oder einer anderen Methode hinzu.
```swift
override func viewDidLoad() {
    super.viewDidLoad()
    do {
        let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
        let credential = try CommunicationTokenCredential(withOptions: communicationTokenRefreshOptions)
        meetingUIClient = MeetingUIClient(with: credential)
    }
    catch {
        print("Failed to create communication token credential")
    }
}

private func fetchTokenAsync(completionHandler: @escaping TokenRefreshHandler) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}

```
Verwenden Sie die APIs des Teams Embed SDK entsprechend der Beschreibung in der Dokumentation. Eine Beschreibung der API-Nutzung ist nicht Teil dieser Dokumentation. 

Löschen Sie das Teams Embed SDK, und legen Sie `nil` auf die Variablen des SDK fest, wenn Sie es nicht mehr benötigen oder die App das ACS SDK verwenden muss.
```swift
    private func disposeTeamsSdk() {
        self.meetingUIClient?.dispose(completionHandler: { (error: Error?) in
            if (error != nil) {
                print("Dispose failed: \(error!)")
            } else {
                self.meetingUIClient = nil
                self.meetingUIClientCall = nil
            }
        })        
    }

```

Das Teams Embed SDK kann nur gelöscht werden, wenn keine aktiven Anrufe vorhanden sind. Ist ein aktiver Anruf vorhanden, gibt `meetingUIClient?.dispose` einen Fehler im Fertigstellungshandler zurück. Beenden Sie den aktiven Anruf. Ist kein weiterer aktiver Anruf vorhanden, rufen Sie `self.disposeTeamsSdk()` auf.
```swift
    
    private var shouldDispose: Bool = false

    public func endMeeting() {
        meetingUIClientCall?.hangUp(completionHandler: { (error: Error?) in
            if (error != nil) {
                print("End meeting failed: \(error!)")
                // There are no active calls
                self.disposeTeamsSdk()
            } else {
                // Ending active call is in progress.
                self.shouldDispose = true;
            }
        })
    }

```

Implementieren Sie die `MeetingUIClientCallDelegate`-Protokollmethode `meetingUIClientCall(didUpdateCallState callState: MeetingUIClientCallState)`, um ein Statusupdate zum Beenden des aktiven Anrufs und dem anschließenden Löschen des Teams Embed SDK abzurufen.
```swift
    func meetingUIClientCall(didUpdateCallState callState: MeetingUIClientCallState) {
        switch callState {
        case .connecting:
        case .connected:
        case .waitingInLobby:
        case .ended:
            if (self.shouldDispose) {
                self.disposeTeamsSdk()
            }
        @unknown default:
            print("Unsupported state")
        }
    }
```

Fügen Sie der Klasse weitere erforderliche `MeetingUIClientCallDelegate`-Protokollmethoden hinzu.

```swift
    func meetingUIClientCall(didUpdateRemoteParticipantCount remoteParticipantCount: UInt) {
        print("Remote participant count has changed to: \(remoteParticipantCount)")
    }

    func onIsMutedChanged() {
        print("Mute state changed to: \(meetingUIClientCall?.isMuted ?? false)")
    }
    
    func onIsSendingVideoChanged() {
        print("Sending video state changed to: \(meetingUIClientCall?.isSendingVideo ?? false)")
    }
    
    func onIsHandRaisedChanged(_ participantIds: [Any]) {
        print("Self participant raise hand status changed to: \(meetingUIClientCall?.isHandRaised ?? false)")
    }
```
