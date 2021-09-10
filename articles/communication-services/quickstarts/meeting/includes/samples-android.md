---
title: Verwenden von Azure Communication Services Teams Embed für Android
description: In dieser Übersicht erfahren Sie, wie Sie die Azure Communication Services-Bibliothek zum Einbetten von Teams für Android verwenden.
author: palatter
ms.author: palatter
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5f74ab05517c2859cecb9913c19a9ab4c454c85c
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2021
ms.locfileid: "113215125"
---
## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../create-communication-resource.md).
- Ein `User Access Token`, um den Anrufclient zu aktivieren. Weitere Informationen zum [Abrufen eines `User Access Token`](../../access-tokens.md)
- Gehen Sie den Schnellstart [Erste Schritte beim Hinzufügen von Teams Embed zu Ihrer Anwendung](../getting-started-with-teams-embed.md) durch.

## <a name="joining-a-group-call"></a>Teilnehmen an einem Gruppenanruf

Die Teilnahme an Gruppenanrufen ist durch die Bereitstellung von `MeetingUIClientGroupCallLocator` und `MeetingUIClientJoinOptions` für die `meetingUIClient.join`-API möglich. Der Gruppenanruf gibt kein Signal für die anderen Teilnehmer aus. Der Benutzer tritt dem Anruf lautlos bei.

Navigieren Sie zur Hauptlayoutdatei (`app/src/main/res/layout/activity_main.xml`), um die Schaltfläche zum Teilnehmen an einem Gruppenanruf einzurichten. Erstellen Sie eine Schaltfläche mit der ID `join_groupCall`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_groupCall"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Group Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

Navigieren Sie zu **MainActivity.java**, und fügen Sie die Aktion beim Klicken auf eine Schaltfläche in der `onCreate`-Methode hinzu:

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import com.azure.android.communication.ui.meetings.MeetingUIClient;
import com.azure.android.communication.ui.meetings.MeetingUIClientCall;
import com.azure.android.communication.ui.meetings.MeetingUIClientGroupCallLocator;
import com.azure.android.communication.ui.meetings.MeetingUIClientJoinOptions;

import java.util.UUID;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        Button joinGroupCallButton = findViewById(R.id.join_groupCall);
        joinGroupCallButton.setOnClickListener(l -> joinGroupCall());
    }
}
```

Das Einrichten des Clients und das Bereitstellen des Tokens erfolgt auf die gleiche Weise wie bei der Besprechungsbeitritts-API, die in der [Schnellstartanleitung](../getting-started-with-teams-embed.md) beschrieben wird.

Die Methode `joinGroupCall` wird als die Aktion festgelegt, die bei Tippen auf die Schaltfläche *An Gruppenanruf teilnehmen* ausgeführt wird. Die Teilnahme an einem Gruppenanruf kann über den `MeetingUIClient` erfolgen. Erstellen Sie einen `MeetingUIClientGroupCallLocator`, und konfigurieren Sie die Teilnahmeoptionen mithilfe von `MeetingUIClientJoinOptions`.
Beachten Sie, dass `<GROUP_ID>` durch eine UUID-Zeichenfolge ersetzt werden muss. Die Zeichenfolge der Gruppen-ID muss im GUID- oder UUID-Format vorliegen.

```java
/**
 * Join a group call with a groupID.
 */
private void joinGroupCall() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        UUID groupUUID = UUID.fromString("<GROUP_ID>");
        MeetingUIClientGroupCallLocator meetingUIClientGroupCallLocator = new MeetingUIClientGroupCallLocator(groupUUID);
        meetingUIClientCall = meetingUIClient.join(meetingUIClientGroupCallLocator, meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join group call: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="teams-embed-call-or-meeting-status-events-capturing"></a>Erfassen des Status von Teams Embed-Anrufen oder -Besprechungen

Der Status der Teilnahme an Gruppenanrufen oder Besprechungen kann von der Klasse `MeetingUIClientCallEventListener` erfasst werden. Der Status umfasst den Verbindungsstatus, die Teilnehmeranzahl sowie Modalitäten wie den Mikrofon- oder Kamerastatus.

Fügen Sie `MainActivity.java` den folgenden Code hinzu.

```java
import androidx.core.content.ContextCompat;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;
import com.azure.android.communication.ui.meetings.MeetingUIClientCall;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallState;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallEventListener;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallIdentityProvider;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallIdentityProviderCallback;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallUserEventListener;
import com.azure.android.communication.ui.meetings.MeetingUIClientGroupCallLocator;
import com.azure.android.communication.ui.meetings.MeetingUIClientIconType;
import com.azure.android.communication.ui.meetings.MeetingUIClientJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClientTeamsMeetingLinkLocator;
```

Fügen Sie Ihrer Klasse den `MeetingUIClientCallEventListener` hinzu.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientEventListener {
```

Rufen Sie `setMeetingUIClientCallEventListener` mit dem Parameter `this` auf, nachdem die Teilnahme an dem Anruf oder der Besprechung erfolgreich gestartet wurde.

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);
    
    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);
    
    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Implementieren Sie die für Ihre App erforderlichen `MeetingUIClientCallEventListener`-Methoden, und fügen Sie für nicht benötigte Methoden Stubs hinzu.

```java
@Override
public void onCallStateChanged(MeetingUIClientCallState callState) {
    switch(callState) {
        case CONNECTING:
            System.out.println("Call state changed to Connecting");
            break;
        case CONNECTED:
            System.out.println("Call state changed to Connected");
            break;
        case WAITING_IN_LOBBY:
            System.out.println("Call state changed to Waiting in Lobby");
            break;
        case ENDED:
            System.out.println("Call state changed to Ended");
            break;
    }
}

@Override
public void onRemoteParticipantCountChanged(int newCount) {
    System.out.println("Remote participant count changed: " + newCount);
}

@Override
public void onIsMutedChanged() {
}

@Override
public void onIsSendingVideoChanged() {
}

@Override
public void onIsHandRaisedChanged(List<String> participantIds) {
}
```

## <a name="bring-your-own-identity-from-the-app-to-the-participants-in-the-sdk-call"></a>Verwenden der eigenen Identität in der App für die Teilnehmer des SDK-Anrufs

Die App kann den Teilnehmern des Anrufs oder der Besprechung Identitätswerte zuweisen und die Standardwerte überschreiben. Die Werte beinhalten Avatar, Name und Untertitel.

### <a name="assigning-avatars-for-call-participants"></a>Zuweisen von Avataren für Teilnehmer des Anrufs

Fügen Sie Ihrer Klasse den `MeetingUIClientCallIdentityProvider` hinzu.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientCallIdentityProvider {
```

Rufen Sie `setMeetingUIClientCallIdentityProvider` mit dem Parameter `this` auf.

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallIdentityProvider(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Implementieren Sie die Methode `provideAvatarFor`, und ordnen Sie jeden `userIdentifier` dem entsprechenden Avatar zu.

```java
@Override
public void provideAvatarFor(CommunicationIdentifier communicationIdentifier, MeetingUIClientAvatarSize avatarSize, MeetingUIClientCallIdentityProviderCallback callback) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        System.out.println("MeetingUIClientIdentityProvider.provideAvatarFor called for userIdentifier: " + userIdentifier.getId());
        if (userIdentifier.getId().startsWith("8:teamsvisitor:")) {
            callback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_avatar_placeholder_large_pink));
        } else if (userIdentifier.getId().startsWith("8:orgid:")) {
            callback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_doctor_avatar));
        } else if (userIdentifier.getId().startsWith("8:acs:")) {
            callback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_avatar_placeholder_large_green));
        }
    }
}
```

Fügen Sie der Klasse weitere obligatorische MeetingUIClientCallIdentityProvider-Schnittstellenmethoden hinzu, diese können leere Implementierungen aufweisen.

```java
@Override
public void provideDisplayNameFor(CommunicationIdentifier communicationIdentifier, MeetingUIClientCallIdentityProviderCallback callback) {
}

@Override
public void provideSubTitleFor(CommunicationIdentifier communicationIdentifier, MeetingUIClientCallIdentityProviderCallback callback) {
}
```
## <a name="receive-information-about-user-actions-in-the-ui-and-add-your-own-custom-functionalities"></a>Empfangen von Informationen zu Benutzeraktionen in der Benutzeroberfläche und Hinzufügen eigener benutzerdefinierter Funktionen

### <a name="call-screen"></a>Anrufbildschirm

Die `MeetingUIClientCallUserEventListener`-Schnittstellenmethoden werden bei Benutzeraktionen im Profil des Remoteteilnehmers aufgerufen.

Fügen Sie Ihrer Klasse den `MeetingUIClientCallUserEventListener` hinzu.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientCallUserEventListener {
```

Rufen Sie `setMeetingUIClientCallUserEventListener` mit dem Parameter `this` auf.

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallUserEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
Fügen Sie die Methode `onNamePlateOptionsClicked` hinzu, und implementieren Sie sie. Ordnen Sie jeden `userIdentifier` dem entsprechenden Anrufteilnehmer zu.
Diese Methode wird beim einfachen Tippen auf den Benutzertiteltext im Hauptbildschirm des Anrufs aufgerufen.

```java
@Override
public void onNamePlateOptionsClicked(@NonNull Activity activity, @NonNull CommunicationIdentifier communicationIdentifier) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        if (userIdentifier.getId().startsWith("8:acs:")) {
            // Custom behavior based on the user here.
            System.out.println("On user name plate clicked");
        }
    }
}
```

Fügen Sie die Methode `onParticipantViewLongPressed` hinzu, und implementieren Sie sie. Ordnen Sie jeden `userIdentifier` dem entsprechenden Anrufteilnehmer zu.
Drücken Sie im Hauptbildschirm des Anrufs länger auf die Benutzerkachel, um diese Methode aufzurufen. Geben Sie `true` für eine benutzerdefinierte Verarbeitung oder `false` für die Standardverarbeitung zurück, wenn länger auf das Element gedrückt wird.

```java
@Override
public boolean onParticipantViewLongPressed(@NonNull Activity activity, @NonNull CommunicationIdentifier communicationIdentifier) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        if (userIdentifier.getId().startsWith("8:acs:")) {
            // Custom behavior based on the user here.
            System.out.println("On participant tile long pressed");
            return true;
        }
        return false;
    }
}
```

### <a name="call-roster"></a>Anrufliste

Die `MeetingUIClientCallRosterDelegate`-Schnittstellenmethoden werden bei Benutzeraktionen in der Anrufliste aufgerufen.

Fügen Sie Ihrer Klasse den `MeetingUIClientCallRosterDelegate` hinzu.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientCallRosterDelegate {
```

Rufen Sie `setMeetingUIClientCallRosterDelegate` mit dem Parameter `this` auf.

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallRosterDelegate(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Fügen Sie die Methode `onCallParticipantCellTapped` hinzu, und implementieren Sie sie. Ordnen Sie jeden `userIdentifier` dem entsprechenden Anrufteilnehmer zu.
Diese Methode wird beim einfachen Tippen auf die Zelle eines Remoteteilnehmers im Anruflistenbildschirm aufgerufen. Geben Sie `true` für eine benutzerdefinierte Verarbeitung oder `false` für die Standardverarbeitung eines einfachen Tippens zurück.

```java
@Override
public boolean onCallParticipantCellTapped(@NonNull Activity activity, @NonNull CommunicationIdentifier communicationIdentifier) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        if (userIdentifier.getId().startsWith("8:acs:")) {
            // Custom behavior based on the user here.
            System.out.println("On call participant cell tapped");
            return true;
        }
        return false;
    }
}
```
## <a name="user-experience-customization"></a>Anpassung der Benutzererfahrung

Sie können die Benutzeroberfläche im SDK anpassen, indem Sie App-spezifische Symbole bereitstellen. 

### <a name="customize-ui-icons-in-a-call-or-meeting"></a>Anpassen der bei einem Anruf oder einer Besprechung angezeigten UI-Symbole
Die bei einem Anruf oder einer Besprechung angezeigten Symbole können mithilfe der Methode `public void setIconConfig(Map<MeetingUIClientIconType, Integer> iconConfig)` angepasst werden, die in `MeetingUIClient` verfügbar gemacht wird.

Legen Sie nach der Erstellung des meetingUIClient die Symbolkonfiguration `meetingUIClient.setIconConfig(getIconConfig())` für die in `MeetingUIClientIconType` unterstützten Anrufsymbole fest.

```java
private MeetingUIClient createMeetingUIClient() {
    MeetingUIClient meetingUIClient = new MeetingUIClient(credential);
    meetingUIClient.setIconConfig(getIconConfig());
}

private Map<IconType, Integer> getIconConfig() {
    Map<IconType, Integer> iconConfig = new HashMap<>();
    iconConfig.put(MeetingUIClientIconType.VIDEO_OFF, R.drawable.video_camera_off);
    iconConfig.put(MeetingUIClientIconType.VIDEO_ON, R.drawable.video_camera);
    iconConfig.put(MeetingUIClientIconType.MIC_ON, R.drawable.microphone_fill);
    iconConfig.put(MeetingUIClientIconType.MIC_OFF, R.drawable.microphone_off);
    iconConfig.put(MeetingUIClientIconType.MIC_PROHIBITED, R.drawable.mic_none);
    iconConfig.put(MeetingUIClientIconType.DEVICE_AUDIO, R.drawable.device_filled);
    iconConfig.put(MeetingUIClientIconType.SPEAKER, R.drawable.volume_high);
    iconConfig.put(MeetingUIClientIconType.SPEAKER_OFF, R.drawable.speaker_off);
    iconConfig.put(MeetingUIClientIconType.HEADSET, R.drawable.headset);
    iconConfig.put(MeetingUIClientIconType.BLUETOOTH, R.drawable.bluetooth_audio);
    iconConfig.put(MeetingUIClientIconType.HANGUP, R.drawable.close_app_bar);
    return iconConfig;
}
```

## <a name="perform-operations-with-the-call"></a>Ausführen von Vorgängen für Anrufe

Die Steuerelemente für Anrufe werden über die Methoden in `MeetingUIClientCall` verfügbar gemacht.
Diese Methoden sind sehr nützlich bei der Steuerung der Anrufaktionen.

Hinzufügen einer Variable für meetingUIClientCall

```java
public class MainActivity extends AppCompatActivity {
    private MeetingUIClientCall meetingUIClientCall;
```
Zuweisen der meetingUIClientCall-Variable aus dem Rückgabewert der join-Methode

```java
private void joinGroupCall() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        UUID groupUUID = UUID.fromString("<GROUP_ID>");
        MeetingUIClientGroupCallLocator meetingUIClientGroupCallLocator = new MeetingUIClientGroupCallLocator(groupUUID);
        meetingUIClientCall = meetingUIClient.join(meetingUIClientGroupCallLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join group call: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
### <a name="mute-and-unmute"></a>Stummschalten und Aufheben der Stummschaltung

Rufen Sie die `mute`-Methode auf, um das Mikrofon für einen aktiven Anruf, sofern vorhanden, stummzuschalten.
Der Status des Mikrofons wird in der `onIsMutedChanged`-Methode von `MeetingUIClientCallEventListener` in „Benachrichtigt“ geändert.

```java
// Mute the microphone for an active call.
public void mute() {
    try {
        meetingUIClientCall.mute();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Mute call failed: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Rufen Sie die `unmute`-Methode auf, um die Stummschaltung des Mikrofons für einen aktiven Anruf, sofern vorhanden, aufzuheben.

```java
// Unmute the microphone for an active call.
public void unmute() {
    try {
        meetingUIClientCall.unmute();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Unmute call failed: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
### <a name="other-operations-available-in-from-the--meetinguiclientcall-class"></a>Weitere in der Klasse `MeetingUIClientCall` verfügbare Vorgänge

```java
// Start the video for an active call.
public void startVideo()

// Stop the video for an active call.
public void stopVideo()

// Set the preferred audio route in the call for self user.
public void setAudioRoute(MeetingUIClientAudioRoute audioRoute)

// Raise the hand of current user for an active call.
public void raiseHand()

// Lower the hand of current user for an active call.
public void lowerHand()

// Change the layout in the call for self user.
public List<MicrosoftTeamsSDKLayoutMode> getSupportedLayoutModes()
public void changeLayout(MeetingUIClientLayoutMode microsoftTeamsSDKLayoutMode)

// Hang up the call or leave the meeting.
public void hangUp()
```

## <a name="use-teams-embed-sdk-and-azure-communication-calling-sdk-in-the-same-app"></a>Verwenden des Teams Embed SDK und des Azure Communication Calling SDK in derselben App

Das Teams Embed SDK stellt auch das Azure Communication Calling SDK (ACS) bereit, mit dem beide SDK-Features in derselben App verwendet werden können. Es kann immer nur ein SDK initialisiert und verwendet werden. Eine Initialisierung und gleichzeitige Verwendung beider SDKs führt zu unerwartetem Verhalten. 

Fügen Sie Ihrer Klasse die folgenden Importe hinzu.
```java
import com.azure.android.communication.calling.Call;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.CallState;
import com.azure.android.communication.calling.GroupCallLocator;
import com.azure.android.communication.calling.JoinCallOptions;
import com.azure.android.communication.common.CommunicationTokenCredential;
```

Deklarieren Sie Variablen für die ACS-Verwendung.
```java
public class MainActivity extends AppCompatActivity {
    private CallAgent agent;
    private CallClient mCallClient;
    private Call mCall;
```

Die Initialisierung erfolgt durch Erstellen eines neuen `CallClient`. Fügen Sie die Erstellung einer `joinAcsCall`-Methode oder einer anderen Methode hinzu.

```java
private void joinAcsCall() {
    if (mCallClient == null) {
        mCallClient = new CallClient();
    }
}
```
Verwenden Sie alle ACS-APIs entsprechend der Beschreibung in der Dokumentation. Eine Beschreibung der API-Nutzung ist nicht Teil dieser Dokumentation. 

Löschen Sie das ACS SDK, und legen Sie `null` auf die Variablen des SDK fest, wenn Sie es nicht mehr benötigen oder die App das Teams Embed SDK verwenden muss.
```java
private void stopAcs() {
    mCall = null;
    agent.dispose();
    agent = null;
    mCallClient.dispose();
    mCallClient = null;
}
```

Die Initialisierung des Teams Embed SDK erfolgt auch während der Erstellung von `MeetingUIClient`. Fügen Sie die Erstellung einer `createMeetingUIClient`-Methode oder einer anderen Methode hinzu.
```java
private MeetingUIClient createMeetingUIClient() { 
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        return new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting ui client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
Verwenden Sie die APIs des Teams Embed SDK entsprechend der Beschreibung in der Dokumentation. Eine Beschreibung der API-Nutzung ist nicht Teil dieser Dokumentation. 

Löschen Sie das Teams Embed SDK, und legen Sie `null` auf die Variablen des SDK fest, wenn Sie es nicht mehr benötigen oder die App das ACS SDK verwenden muss.
```java
private void disposeTeamsSdk() {
    try {
        meetingUIClient.dispose();
        meetingUIClientCall = null;
        meetingUIClient = null;
    } catch (Exception ex) {
        Toast.makeText(getContext(), "Failed to teardown Teams Sdk: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Das Teams Embed SDK kann nur gelöscht werden, wenn keine aktiven Anrufe vorhanden sind. `meetingUIClient.dispose()` löst eine Ausnahme aus, wenn ein aktiver Anruf vorhanden ist. Beenden Sie den aktiven Anruf, und rufen Sie dann `disposeTeamsSdk()` auf.

```java
private void endMeeting() {
    try {
        meetingUIClientCall.hangUp();
        disposeTeamsSdk();
    } catch (Exception ex) {
        Toast.makeText(getContext(), "Failed to end meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Implementieren Sie die `MeetingUIClientCallEventListener`-Schnittstellenmethode `onCallStateChanged(MeetingUIClientCallState callState)`, um ein Statusupdate zum Beenden des aktiven Anrufs und zum anschließenden Löschen des Teams Embed SDK abzurufen.
```java
@Override
public void onCallStateChanged(MeetingUIClientCallState callState) {
    switch(callState) {
        case ENDED:
            disposeTeamsSdk();
            break;
        default:
        System.out.println("Call state changed to: " + callState.toString());
    }
}
```

Fügen Sie der Klasse weitere obligatorische `MeetingUIClientCallEventListener`-Schnittstellenmethoden hinzu.

```java
@Override
public void onRemoteParticipantCountChanged(int newCount) {
    System.out.println("Remote participant count has changed to: " + newCount);
}

@Override
public void onIsMutedChanged() {
    System.out.println("Mute state changed to: " + meetingUIClientCall.isMuted());
}

@Override
public void onIsSendingVideoChanged() {
    System.out.println("Sending video state changed to: " + meetingUIClientCall.isSendingVideo());
}

@Override
public void onIsHandRaisedChanged(List<String> participantIds) {
    System.out.println("Self participant raise hand status changed to: " + meetingUIClientCall.isHandRaised());
}
```
