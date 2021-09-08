---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: rifox
ms.openlocfilehash: 86ab1bc1b9612c663076e5ed55ae5531317906f2
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078350"
---
## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../../create-communication-resource.md).
- Ein `User Access Token`, um den Anrufclient zu aktivieren. Weitere Informationen zum [Abrufen eines `User Access Token`](../../../access-tokens.md)
- Optional: Gehen Sie den Schnellstart [Erste Schritte beim Hinzufügen von Anruffunktionen zu einer Anwendung](../../getting-started-with-calling.md) durch.

## <a name="setting-up"></a>Einrichten

### <a name="install-the-package"></a>Installieren des Pakets

> [!NOTE]
> In diesem Dokument wird die Version 1.0.0 des Calling SDK verwendet.

Wählen Sie Ihre Datei „build.gradle“ auf Projektebene aus, und stellen Sie sicher, dass Sie `mavenCentral()` zur Liste der Repositorys unter `buildscript` und `allprojects` hinzufügen.
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Fügen Sie dann in der Datei „build.gradle“ auf Modulebene die folgenden Zeilen zum Abschnitt „dependencies“ hinzu.

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0'
    ...
}

```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen befassen sich mit einigen der wichtigsten Features des Azure Communication Services Calling SDK:

| Name                                  | Beschreibung                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| „CallClient“ ist der Haupteinstiegspunkt des Calling SDK.|
| CallAgent | CallAgent dient zum Starten und Verwalten von Anrufen. |
| CommunicationTokenCredential | „CommunicationTokenCredential“ dient als tokengestützte Anmeldeinformation zum Instanziieren von „CallAgent“.|
| CommunicationIdentifier | „CommunicationIdentifier“ wird als anderer Typ von Teilnehmer verwendet, der Teil eines Anrufs sein könnte.|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>Initialisieren von CallClient, Erstellen von CallAgent und Zugreifen auf DeviceManager

Zum Erstellen einer `CallAgent`-Instanz müssen Sie die `createCallAgent`-Methode für eine `CallClient`-Instanz aufzurufen. Dadurch wird ein `CallAgent`-Instanzobjekt asynchron zurückgegeben.
Die `createCallAgent`-Methode verwendet `CommunicationUserCredential` als Argument, womit ein [Zugriffstoken](../../../access-tokens.md) gekapselt wird.
Für den Zugriff auf `DeviceManager` muss zuerst eine CallAgent-Instanz erstellt werden. Anschließend können Sie mit der `CallClient.getDeviceManager`-Methode DeviceManager abrufen.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential).get();
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
```
Zum Festlegen eines Anzeigenamens für den Anrufer verwenden Sie diese alternative Methode:

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential, callAgentOptions).get();
```


## <a name="place-an-outgoing-call-and-join-a-group-call"></a>Tätigen eines ausgehenden Anrufs und Teilnehmen an einem Gruppenanruf

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

### <a name="place-a-11-call-with-video-camera"></a>Tätigen eines 1:1-Anrufs mit Videokamera
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

### <a name="join-a-group-call"></a>Teilnehmen an einem Gruppenanruf
Um einen neuen Gruppenanruf zu starten oder an einem laufenden Gruppenanruf teilzunehmen, müssen Sie die „join“-Methode aufrufen und ein Objekt mit einer `groupId`-Eigenschaft übergeben. Der Wert muss eine GUID sein.
```java
Context appContext = this.getApplicationContext();
GroupCallLocator groupCallLocator = new GroupCallLocator("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallLocator, joinCallOptions);
```

### <a name="accept-a-call"></a>Annehmen eines Anrufs
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


## <a name="push-notifications"></a>Pushbenachrichtigungen

### <a name="overview"></a>Übersicht
Mobile Pushbenachrichtigungen sind die Popupbenachrichtigungen, die auf mobilen Geräten angezeigt werden. Bei Anruffunktionen konzentrieren wir uns auf VoIP-Pushbenachrichtigungen (Voice over Internet Protocol). Wir werden uns für Pushbenachrichtigungen registrieren, Pushbenachrichtigungen bearbeiten und dann die Registrierung von Pushbenachrichtigungen aufheben.

### <a name="prerequisites"></a>Voraussetzungen

Ein Firebase-Konto, das mit aktiviertem Cloud Messaging (FCM) eingerichtet wurde und bei dem Ihr Firebase Cloud Messaging-Dienst mit einer Azure Notification Hub-Instanz verbunden ist. Weitere Informationen finden Sie unter [Communication Services-Benachrichtigungen](../../../../concepts/notifications.md).
Außerdem geht das Tutorial davon aus, dass Sie zur Entwicklung Ihrer Anwendung mindestens die Android Studio-Version 3.6 einsetzen.

Für die Android-Anwendung sind eine Reihe von Berechtigungen erforderlich, um Benachrichtigungen von Firebase Cloud Messaging empfangen zu können. Fügen Sie in Ihrer Datei `AndroidManifest.xml` direkt nach dem Tag *<manifest ...>* oder unter dem Tag *</application>* den folgenden Berechtigungssatz hinzu.

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notifications"></a>Registrieren für Pushbenachrichtigungen

Um sich für Pushbenachrichtigungen zu registrieren, muss die Anwendung `registerPushNotification()` für eine *CallAgent*-Instanz mit einem Geräteregistrierungstoken aufrufen.

Um das Geräteregistrierungstoken zu erhalten, fügen Sie das Firebase SDK zur Datei *build.gradle* Ihres Anwendungsmoduls hinzu, indem Sie die folgenden Zeilen im Abschnitt `dependencies` hinzufügen (sofern noch nicht vorhanden):

```
    // Add the SDK for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

Fügen Sie in der Datei *build.gradle* auf Projektebene im Abschnitt `dependencies` Folgendes hinzu, sofern noch nicht vorhanden:

```
    classpath 'com.google.gms:google-services:4.3.3'
```

Fügen Sie das folgende Plug-In am Anfang der Datei hinzu, sofern noch nicht vorhanden:

```
apply plugin: 'com.google.gms.google-services'
```

Wählen Sie auf der Symbolleiste *Jetzt synchronisieren* aus. Fügen Sie den folgenden Codeausschnitt hinzu, um das Geräteregistrierungstoken abzurufen, das vom Firebase Cloud Messaging SDK für die Clientanwendungsinstanz generiert wird. Achten Sie darauf, dass Sie die unten aufgeführten Importe zum Header der Hauptaktivität für die Instanz hinzufügen. Sie sind erforderlich, damit der Ausschnitt das Token abruft.

```java
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

Fügen Sie diesen Ausschnitt hinzu, um das Token abzurufen:

```java
        FirebaseInstanceId.getInstance().getInstanceId()
                .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
                    @Override
                    public void onComplete(@NonNull Task<InstanceIdResult> task) {
                        if (!task.isSuccessful()) {
                            Log.w("PushNotification", "getInstanceId failed", task.getException());
                            return;
                        }

                        // Get new Instance ID token
                        String deviceToken = task.getResult().getToken();
                        // Log
                        Log.d("PushNotification", "Device Registration token retrieved successfully");
                    }
                });
```
Registrieren Sie das Geräteregistrierungstoken beim Calling Services SDK für Pushbenachrichtigungen zu eingehenden Anrufen:

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>Behandlung von Pushbenachrichtigungen

Um Pushbenachrichtigungen für eingehende Anrufe zu empfangen, rufen Sie *handlePushNotification()* für eine *CallAgent*-Instanz mit Nutzdaten auf.

Um die Nutzdaten von Firebase Cloud Messaging abzurufen, erstellen Sie zunächst einen neuen Dienst („Datei“ > „Neu“ > „Dienst“ > „Dienst“), der das Firebase SDK *FirebaseMessagingService* erweitert, und überschreiben Sie dann die Methode `onMessageReceived`. Diese Methode ist der Ereignishandler, der aufgerufen wird, wenn Firebase Cloud Messaging die Pushbenachrichtigung an die Anwendung übermittelt.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageDataFromFCM;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d("PushNotification", "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageDataFromFCM = remoteMessage.getData();
        }
    }
}
```
Fügen Sie der Datei `AndroidManifest.xml` im Tag <application> die unten angegebene Dienstdefinition hinzu:

```XML
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

- Sobald die Nutzdaten abgerufen wurden, können sie an das *Communication Services* SDK übergeben werden, um sie in ein internes Objekt vom Typ *IncomingCallInformation* zu analysieren, das durch Aufrufen der Methode *handlePushNotification* für eine Instanz von *CallAgent* behandelt wird. Eine `CallAgent`-Instanz wird durch den Aufruf der Methode `createCallAgent(...)` für die Klasse `CallClient` erstellt.

```java
try {
    IncomingCallInformation notification = IncomingCallInformation.fromMap(pushNotificationMessageDataFromFCM);
    Future handlePushNotificationFuture = callAgent.handlePushNotification(notification).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

Wenn die Verarbeitung der Pushbenachrichtigung erfolgreich ist und alle Ereignishandler ordnungsgemäß registriert sind, löst die Anwendung ein Klingeln aus.

### <a name="unregister-push-notifications"></a>Aufheben der Registrierung der Pushbenachrichtigungen

Anwendungen können die Registrierung der Pushbenachrichtigung jederzeit aufheben. Rufen Sie die Methode `unregisterPushNotification()` für callAgent auf, um die Registrierung aufzuheben.

```java
try {
    callAgent.unregisterPushNotification().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>Anrufverwaltung
Sie können auf Anrufeigenschaften zugreifen und während eines Anrufs verschiedene Vorgänge ausführen, um Einstellungen für Video und Audio zu verwalten.

### <a name="call-properties"></a>Anrufeigenschaften

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

### <a name="mute-and-unmute"></a>Stummschalten und Aufheben der Stummschaltung

Zum Stummschalten oder Aufheben der Stummschaltung des lokalen Endpunkts können Sie die asynchronen APIs `mute` und `unmute` verwenden:

```java
Context appContext = this.getApplicationContext();
call.mute(appContext).get();
call.unmute(appContext).get();
```
### <a name="change-the-volume-of-the-call"></a>Ändern der Lautstärke des Anrufs
    
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

    
### <a name="start-and-stop-sending-local-video"></a>Starten und Beenden des Sendens von lokalem Video

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

## <a name="remote-participants-management"></a>Verwaltung von Remoteteilnehmern

Alle Remoteteilnehmer werden durch den Typ `RemoteParticipant` dargestellt und sind über die `remoteParticipants`-Sammlung für eine Anrufinstanz verfügbar.

### <a name="list-participants-in-a-call"></a>Auflisten der Teilnehmer an einem Anruf
Die `remoteParticipants`-Sammlung gibt eine Liste der Remoteteilnehmer an einem angegebenen Anruf zurück:
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
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


## <a name="device-management"></a>Geräteverwaltung

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

## <a name="record-calls"></a>Aufzeichnen von Anrufen
> [!WARNING]
> Bis zur Version 1.1.0 und der Betaversion 1.1.0-beta.1 des ACS Calling Android SDK wird `isRecordingActive` verwendet und `addOnIsRecordingActiveChangedListener` ist Teil des `Call`-Objekts. Für die neuen Betaversionen wurden diese APIs als erweitertes Feature von `Call` verschoben, wie unten beschrieben.

> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Beta-Release des ACS Calling Android SDK.

Die Anrufaufzeichnung ist ein erweitertes Feature der zentralen `Call`-API. Sie müssen zunächst das API-Objekt der Aufzeichnungsfunktion abrufen:

```java
RecordingFeature callRecordingFeature = call.api(RecordingFeature.class);
```

Um anschließend herauszufinden, ob der Anruf aufgezeichnet wird, prüfen Sie die Eigenschaft `isRecordingActive` von `callRecordingFeature`. Er gibt `boolean` zurück.

```java
boolean isRecordingActive = callRecordingFeature.isRecordingActive();
```

Sie können auch Aufzeichnungsänderungen abonnieren:

```java
private void handleCallOnIsRecordingChanged(PropertyChangedEvent args) {
    boolean isRecordingActive = callRecordingFeature.isRecordingActive();
}

callRecordingFeature.addOnIsRecordingActiveChangedListener(handleCallOnIsRecordingChanged);

```

Wenn Sie die Aufzeichnung aus Ihrer Anwendung heraus starten möchten, folgen Sie bitte zunächst der [Übersicht über die Anrufaufzeichnung](../../../../concepts/voice-video-calling/call-recording.md), um die Schritte zur Einrichtung der Aufzeichnung von Anrufen zu erfahren.

Sobald Sie die Anrufaufzeichnung auf Ihrem Server eingerichtet haben, müssen Sie von Ihrer Android-Anwendung aus den `ServerCallId`-Wert des Anrufs abrufen und ihn dann an Ihren Server senden, um den Aufzeichnungsprozess zu starten. Der `ServerCallId`-Wert kann mithilfe von `getServerCallId()` der `CallInfo`-Klasse verwendet werden, die im Klassenobjekt mit `getInfo()` gefunden werden kann.

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to start the call recording.
} catch (ExecutionException | InterruptedException e) {
} catch (UnsupportedOperationException unsupportedOperationException) {
}
```

Wenn die Aufzeichnung vom Server aus gestartet wird, wird das Ereignis `handleCallOnIsRecordingChanged` ausgelöst und der Wert von `callRecordingFeature.isRecordingActive()` wird `true` sein.

Genau wie beim Starten der Anrufaufzeichnung müssen Sie, wenn Sie die Aufzeichnung beenden möchten, die `ServerCallId` abrufen und an Ihren Aufzeichnungsserver senden, sodass dieser die Aufzeichnung beenden kann.

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to stop the call recording.
} catch (ExecutionException | InterruptedException e) {
} catch (UnsupportedOperationException unsupportedOperationException) {
}
```

Wenn die Aufzeichnung vom Server aus beendet wird, wird das Ereignis `handleCallOnIsRecordingChanged` ausgelöst und der Wert von `callRecordingFeature.isRecordingActive()` wird `false` sein.


## <a name="call-transcription"></a>Anruftranskription
> [!WARNING]
> Bis zur Version 1.1.0 und der Betaversion 1.1.0-beta.1 des ACS Calling Android SDK wird `isTranscriptionActive` verwendet und `addOnIsTranscriptionActiveChangedListener` ist Teil des `Call`-Objekts. Für die neuen Betaversionen wurden diese APIs als erweitertes Feature von `Call` verschoben, wie unten beschrieben.
    
> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Beta-Release des ACS Calling Android SDK.

Die Anruftranskription ist ein erweitertes Feature der zentralen `Call`-API. Sie müssen zunächst das API-Objekt des Transkriptionsfeatures abrufen:

```java
TranscriptionFeature callTranscriptionFeature = call.api(TranscriptionFeature.class);
```

Um anschließend herauszufinden, ob der Anruf transkribiert wird, prüfen Sie die Eigenschaft `isTranscriptionActive` von `callTranscriptionFeature`. Er gibt `boolean` zurück.

```java
boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
```

Sie können auch Transkriptionsänderungen abonnieren:

```java
private void handleCallOnIsTranscriptionChanged(PropertyChangedEvent args) {
    boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
}

callTranscriptionFeature.addOnIsTranscriptionActiveChangedListener(handleCallOnIsTranscriptionChanged);

```    

## <a name="eventing-model"></a>Ereignismodell
Sie können die meisten Objekte und Sammlungen abonnieren, um bei Änderungen von Werten benachrichtigt zu werden.

### <a name="properties"></a>Eigenschaften
So abonnieren Sie `property changed`-Ereignisse

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>Auflistungen
So abonnieren Sie `collection updated`-Ereignisse

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
