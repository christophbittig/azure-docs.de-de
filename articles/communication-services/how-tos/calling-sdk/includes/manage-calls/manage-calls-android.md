---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 319571066bd69d1bc80414de7bdb49da27102c18
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700126"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

## <a name="place-a-call"></a>Tätigen eines Anrufs

Um einen Anruf zu erstellen und zu starten, müssen Sie die `CallAgent.startCall()`-Methode aufrufen und den `Identifier` des/der Angerufenen angeben.
Um an einem Gruppenanruf teilzunehmen, müssen Sie die `CallAgent.join()`-Methode aufrufen und die groupId angeben. Gruppen-IDs müssen das GUID- oder UUID-Format haben.

Erstellung und Start des Anrufs erfolgen synchron. Die Anrufinstanz ermöglicht Ihnen das Abonnieren aller Ereignisse im Rahmen des Anrufs.

### <a name="place-a-11-call-to-a-user"></a>Tätigen eines 1:1-Anrufs eines Benutzers
Um einen Anruf eines anderen Communication Services-Benutzers zu tätigen, rufen Sie die `call`-Methode für `callAgent` auf und übergeben ein Objekt mit `communicationUserId`-Schlüssel.
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUserIdentifier acsUserId = new CommunicationUserIdentifier(<USER_ID>);
CommunicationUserIdentifier participants[] = new CommunicationUserIdentifier[]{ acsUserId };
call oneToOneCall = callAgent.startCall(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>1:n-Anruf mit Benutzern und Festnetznummern
> [!WARNING]
> Zurzeit sind Anrufe über das Telefonfestnetz nicht möglich.

Für einen 1:n-Anruf eines Benutzers und einer Festnetznummer müssen Sie die Telefonnummer des Angerufenen angeben.
Die Communication Services-Ressource muss so konfiguriert sein, dass Anrufe über das Telefonfestnetz möglich sind:
```java
CommunicationUserIdentifier acsUser1 = new CommunicationUserIdentifier(<USER_ID>);
PhoneNumberIdentifier acsUser2 = new PhoneNumberIdentifier("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.startCall(participants, startCallOptions);
```

## <a name="accept-a-call"></a>Annehmen eines Anrufs
Zum Annehmen eines Anrufs rufen Sie die Methode „accept“ für ein Anrufobjekt auf.

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
Call call = incomingCall.accept(context).get();
```

Annehmen eines Anrufs mit eingeschalteter Videokamera:

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
acceptCallOptions.setVideoOptions(new VideoOptions(new LocalVideoStream(desiredCamera, appContext)));
Call call = incomingCall.accept(context, acceptCallOptions).get();
```

Der eingehende Anruf kann durch Abonnieren des `onIncomingCall`-Ereignisses auf dem `callAgent`-Objekt abgerufen werden:

```java
// Assuming "callAgent" is an instance property obtained by calling the 'createCallAgent' method on CallClient instance 
public Call retrieveIncomingCall() {
    IncomingCall incomingCall;
    callAgent.addOnIncomingCallListener(new IncomingCallListener() {
        void onIncomingCall(IncomingCall inboundCall) {
            // Look for incoming call
            incomingCall = inboundCall;
        }
    });
    return incomingCall;
}
```

## <a name="join-a-group-call"></a>Teilnehmen an einem Gruppenanruf
Um einen neuen Gruppenanruf zu starten oder an einem laufenden Gruppenanruf teilzunehmen, müssen Sie die „join“-Methode aufrufen und ein Objekt mit einer `groupId`-Eigenschaft übergeben. Der Wert muss eine GUID sein.
```java
Context appContext = this.getApplicationContext();
GroupCallLocator groupCallLocator = new GroupCallLocator("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallLocator, joinCallOptions);
```

## <a name="call-properties"></a>Anrufeigenschaften

Ruft die eindeutige ID für diesen Anruf ab:

```java
String callId = call.getId();
```

Um mehr über andere Anrufteilnehmer zu erfahren, sehen Sie sich die `remoteParticipant`-Sammlung für die `call`-Instanz an:

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

Die Identität des Anrufers bei einem eingehenden Anruf:

```java
CommunicationIdentifier callerId = call.getCallerInfo().getIdentifier();
```

Ruft den Zustand des Anrufs ab: 

```java
CallState callState = call.getState();
```

Gibt eine Zeichenfolge zurück, die den aktuellen Zustand eines Anrufs darstellt:
* „NONE“: ursprünglicher Anrufzustand
* „EARLY_MEDIA“: gibt einen Zustand an, bei dem eine Ansage wiedergegeben wird, bevor der Anruf verbunden wird
* „CONNECTING“: anfänglicher Übergangszustand, sobald ein Anruf getätigt oder angenommen wird
* „RINGING“: für einen ausgehenden Anruf; gibt an, dass für den Anruf bei Remoteteilnehmern ein Klingeln ertönt
* „CONNECTED“: der Anruf wurde verbunden
* „LOCAL_HOLD“: Der Anruf wird vom lokalen Teilnehmer gehalten; es werden keine Medien zwischen lokalem Endpunkt und Remoteteilnehmern übertragen.
* „REMOTE_HOLD“: Der Anruf wird von einem Remoteteilnehmer gehalten; es werden keine Medien zwischen lokalem Endpunkt und Remoteteilnehmern übertragen.
* „DISCONNECTING“: Übergangsstatus, ehe der Anruf in den Zustand „Disconnected“ wechselt
* „DISCONNECTED“: der Endzustand des Anrufs
* „IN_LOBBY“: im Gespräch für eine Teams-Besprechungsinteroperabilität

Um zu erfahren, warum ein Anruf beendet wurde, überprüfen Sie die `callEndReason`-Eigenschaft. Sie enthält Code/Subcode: 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

Um festzustellen, ob der aktuelle Anruf ein- oder ausgehend ist, untersuchen Sie die `callDirection`-Eigenschaft:

```java
CallDirection callDirection = call.getCallDirection(); 
// callDirection == CallDirection.INCOMING for incoming call
// callDirection == CallDirection.OUTGOING for outgoing call
```

Um festzustellen, ob das aktuelle Mikrofon stummgeschaltet ist, prüfen Sie die `muted`-Eigenschaft:

```java
boolean muted = call.isMuted();
```

Um aktive Videostreams zu prüfen, sehen Sie sich die `localVideoStreams`-Sammlung an:

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

## <a name="mute-and-unmute"></a>Stummschalten und Aufheben der Stummschaltung

Zum Stummschalten oder Aufheben der Stummschaltung des lokalen Endpunkts können Sie die asynchronen APIs `mute` und `unmute` verwenden:

```java
Context appContext = this.getApplicationContext();
call.mute(appContext).get();
call.unmute(appContext).get();
```
## <a name="change-the-volume-of-the-call"></a>Ändern der Lautstärke des Anrufs

Während eines Anrufs sollten die Lautstärketasten des Telefons dem Benutzer die Möglichkeit geben, die Anruflautstärke zu ändern.
Dies geschieht mithilfe der Methode `setVolumeControlStream` mit dem Streamtyp `AudioManager.STREAM_VOICE_CALL` für die Aktivität, bei der der Aufruf getätigt wird.
Damit können Sie mit den Hardwarelautstärketasten die Lautstärke des Anrufs ändern (gekennzeichnet durch ein Telefonsymbol oder etwas Ähnliches auf dem Lautstärkeregler) und verhindern, dass Sie die Lautstärke für andere Klangprofile wie Alarme, Medien oder die systemweite Lautstärke ändern. Weitere Informationen finden Sie unter [Behandeln von Änderungen an der Audioausgabe | Android-Entwickler](https://developer.android.com/guide/topics/media-apps/volume-and-earphones).

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    ...
    setVolumeControlStream(AudioManager.STREAM_VOICE_CALL);
}
```

## <a name="remote-participants-management"></a>Verwaltung von Remoteteilnehmern

Alle Remoteteilnehmer werden durch den Typ `RemoteParticipant` dargestellt und sind über die `remoteParticipants`-Sammlung für eine Anrufinstanz verfügbar.

### <a name="list-participants-in-a-call"></a>Auflisten der Teilnehmer an einem Anruf
Die `remoteParticipants`-Sammlung gibt eine Liste der Remoteteilnehmer an einem angegebenen Anruf zurück:
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="add-a-participant-to-a-call"></a>Hinzufügen eines Teilnehmers zu einem Anruf

Um einen Teilnehmer einem Anruf hinzuzufügen (entweder als Benutzer oder Telefonnummer), können Sie `addParticipant` aufrufen. Dadurch wird die Instanz des Remoteteilnehmers synchron zurückgegeben.

```java
const acsUser = new CommunicationUserIdentifier("<acs user id>");
const acsPhone = new PhoneNumberIdentifier("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
AddPhoneNumberOptions addPhoneNumberOptions = new AddPhoneNumberOptions(new PhoneNumberIdentifier("<alternate phone number>"));
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone, addPhoneNumberOptions);
```

### <a name="remove-participant-from-a-call"></a>Entfernen eines Teilnehmers aus einem Anruf
Um einen Teilnehmer aus einem Anruf zu entfernen (entweder als Benutzer oder Telefonnummer), können Sie `removeParticipant` aufrufen.
Dieser Vorgang wird asynchron aufgelöst, sobald der Teilnehmer aus dem Anruf entfernt wurde.
Der Teilnehmer wird auch aus der `remoteParticipants`-Sammlung entfernt.
```java
RemoteParticipant acsUserRemoteParticipant = call.getParticipants().get(0);
RemoteParticipant acsPhoneRemoteParticipant = call.getParticipants().get(1);
call.removeParticipant(acsUserRemoteParticipant).get();
call.removeParticipant(acsPhoneRemoteParticipant).get();
```

### <a name="remote-participant-properties"></a>Eigenschaften von Remoteteilnehmern
Einem Remoteteilnehmer sind eine Reihe von Eigenschaften und Sammlungen zugeordnet:

* Ruft den Bezeichner dieses Remoteteilnehmers ab.
„Identity“ ist einer der „Identifier“-Typen
    ```java
    CommunicationIdentifier participantIdentifier = remoteParticipant.getIdentifier();
    ```

* Ruft den Zustand dieses Remoteteilnehmers ab.
    ```java
    ParticipantState state = remoteParticipant.getState();
    ```
Folgende Werte sind hierfür möglich:
* „IDLE“: Anfangszustand
* „EARLY_MEDIA“: Ansage wird wiedergegeben, bevor der Teilnehmer mit dem Anruf verbunden ist
* „RINGING“: Der Teilnehmeranruf klingelt.
* „CONNECTING“: Übergangszustand, während sich der Teilnehmer mit dem Anruf verbindet
* „CONNECTED“: Teilnehmer ist mit dem Anruf verbunden
* „HOLD“: Teilnehmer wird gehalten
* „IN_LOBBY“: Der Teilnehmer wartet im Wartebereich darauf, zugelassen zu werden. Wird derzeit nur in Teams-Interoperabilitätsszenarien verwendet.
* „DISCONNECTED“: In diesem Endzustand wird der Teilnehmer vom Anruf getrennt.

* Um zu erfahren, warum ein Teilnehmer den Anruf verlassen hat, prüfen Sie die `callEndReason`-Eigenschaft:
    ```java
    CallEndReason callEndReason = remoteParticipant.getCallEndReason();
    ```

* Um zu prüfen, ob dieser Remoteteilnehmer stummgeschaltet ist oder nicht, prüfen Sie die `isMuted`-Eigenschaft:
    ```java
    boolean isParticipantMuted = remoteParticipant.isMuted();
    ```

* Um zu prüfen, ob dieser Remoteteilnehmer spricht oder nicht, prüfen Sie die `isSpeaking`-Eigenschaft:
    ```java
    boolean isParticipantSpeaking = remoteParticipant.isSpeaking();
    ```

* Um alle Videostreams zu prüfen, die ein bestimmter Teilnehmer im Rahmen dieses Anrufs sendet, sehen Sie sich die `videoStreams`-Sammlung an:
    ```java
    List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
    ```