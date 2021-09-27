---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: c2deff9146d0f6ec28e3e37981bcb82d3e056947
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700128"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

> [!NOTE]
> Bei Implementierung von Ereignisdelegaten muss die Anwendung einen eindeutigen Verweis auf die Objekte enthalten, die Ereignisabonnements erfordern. Wenn beispielsweise ein `RemoteParticipant`-Objekt beim Aufrufen der `call.addParticipant`-Methode zurückgegeben wird und die Anwendung den Delegaten auf das Lauschen auf `RemoteParticipantDelegate` festlegt, muss die Anwendung einen eindeutigen Verweis auf das `RemoteParticipant`-Objekt enthalten. Andernfalls löst der Delegat, wenn dieses Objekt erfasst wird, eine schwerwiegende Ausnahme aus, sobald das Calling SDK versucht, das Objekt aufzurufen.

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

## <a name="join-a-group-call"></a>Teilnehmen an einem Gruppenanruf
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

## <a name="subscribe-to-an-incoming-call"></a>Abonnieren eines eingehenden Anrufs
Abonnieren Sie ein Ereignis für einen eingehenden Anruf.

```swift
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func callAgent(_ callAgent: CallAgent, didReceiveIncomingCall incomingcall: IncomingCall) {
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

## <a name="manage-remote-participants"></a>Verwalten von Remoteteilnehmern

Alle Remoteteilnehmer werden durch den Typ `RemoteParticipant` dargestellt und sind über die Sammlung `remoteParticipants` für eine Anrufinstanz verfügbar.

### <a name="list-participants-in-a-call"></a>Auflisten der Teilnehmer an einem Anruf

```swift
call.remoteParticipants
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