---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: be7eee7c8de5cae201660b4d165ca51bb40b3c19
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114339708"
---
Steigen Sie in Azure Communication Services ein, indem Sie die Communication Services-Clientbibliothek für Telefonie nutzen, um Ihrer App 1:1-Videoanrufe hinzuzufügen. Hier erfahren Sie, wie Sie einen Videoanruf mithilfe des Calling SDK von Azure Communication Services für Android beginnen und beantworten.

## <a name="sample-code"></a>Beispielcode

Wenn Sie direkt zum Ende springen möchten, können Sie die Beispiel-App auf [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/videoCallingQuickstart) herunterladen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio) zum Erstellen Ihrer Android-Anwendung
- Eine bereitgestellte Communication Services-Ressource. [Erstellen einer Communication Services-Ressource](../../../create-communication-resource.md)
- Ein [Benutzerzugriffstoken](../../../access-tokens.md) für Ihren Azure Communication Service

### <a name="create-an-android-app-with-an-empty-activity"></a>Erstellen Sie eine Android-App mit einer leeren Aktivität.

Wählen Sie in Android Studio „Start a new Android Studio project“ (Neues Android Studio-Projekt starten) aus.

:::image type="content" source="../../media/android/studio-new-project.png" alt-text="Ein Screenshot, der die Schaltfläche „Start a new Android Studio project“ (Neues Android Studio-Projekt starten) zeigt, die in Android Studio ausgewählt ist":::

Wählen Sie unter „Phone and Tablet“ (Telefon und Tablet) die Projektvorlage „Empty Activity“ (Leere Aktivität) aus.

:::image type="content" source="../../media/android/studio-blank-activity.png" alt-text="Screenshot, der die Option „Empty Activity“ (Leere Aktivität) zeigt, die auf dem Bildschirm für die Projektvorlage ausgewählt ist":::

Wählen Sie das SDK „API 26: Android 8.0 (Oreo)“ oder höher aus.

:::image type="content" source="../../media/android/studio-calling-min-api.png" alt-text="Screenshot, der die Option „Empty Activity“ (Leere Aktivität) zeigt, die auf dem Bildschirm für die Projektvorlage 2 ausgewählt ist":::

### <a name="install-the-package"></a>Installieren des Pakets

Wählen Sie Ihre Datei auf Projektebene `build.gradle` aus, und stellen Sie sicher, dass Sie `mavenCentral()` zur Liste der Repositorys unter `buildscript` und `allprojects` hinzufügen.
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
Fügen Sie anschließend in der Datei auf Modulebene `build.gradle` die folgenden Zeilen zu den Abschnitten „dependencies“ und „android“ hinzu:

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0'
    ...
}
```
### <a name="add-permissions-to-application-manifest"></a>Hinzufügen von Berechtigungen zum Anwendungsmanifest

Damit Sie Berechtigungen anfordern können, die für einen Anruf erforderlich sind, müssen diese zunächst im Anwendungsmanifest (`app/src/main/AndroidManifest.xml`) deklariert werden. Ersetzen Sie den Inhalt der Datei durch Folgendes:

```xml
    <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.contoso.acsquickstart">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <!--Our Calling SDK depends on the Apache HTTP SDK.
When targeting Android SDK 28+, this library needs to be explicitly referenced.
See https://developer.android.com/about/versions/pie/android-9.0-changes-28#apache-p-->
        <uses-library android:name="org.apache.http.legacy" android:required="false"/>
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
    
```
### <a name="set-up-the-layout-for-the-app"></a>Festlegen des Layouts für die App

Wir benötigen eine Texteingabe für die Aufgerufenen-ID, eine Schaltfläche zum Platzieren des Anrufs und eine weitere zum Beenden des Anrufs. Außerdem benötigen wir zwei Schaltflächen, um das lokale Video zu aktivieren und zu deaktivieren. Wir müssen zwei Container für lokale und Remote-Videostreams platzieren. Diese können über den Designer oder durch Bearbeiten der Layout-XML-Datei hinzugefügt werden. Navigieren Sie zu `app/src/main/res/layout/activity_main.xml` und ersetzen Sie den Inhalt der Datei durch Folgendes:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical">

            <EditText
                android:id="@+id/callee_id"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ems="10"
                android:gravity="center"
                android:hint="Callee Id"
                android:inputType="textPersonName"
                app:layout_constraintBottom_toTopOf="@+id/call_button"
                app:layout_constraintEnd_toEndOf="parent"
                app:layout_constraintStart_toStartOf="parent"
                app:layout_constraintTop_toTopOf="parent"
                app:layout_constraintVertical_bias="0.064" />
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content">
                <Button
                    android:id="@+id/call_button"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    android:gravity="center"
                    android:text="Call"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toStartOf="parent" />
                <Button
                    android:id="@+id/show_preview"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    android:gravity="center"
                    android:text="Show Video"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toStartOf="parent" />
                <Button
                    android:id="@+id/hide_preview"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    android:gravity="center"
                    android:text="Hide Video"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toStartOf="parent" />
                <Button
                    android:id="@+id/hang_up"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    android:gravity="center"
                    android:text="Hang Up"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toStartOf="parent" />
            </LinearLayout>
            <FrameLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent">
                <LinearLayout
                    android:id="@+id/remotevideocontainer"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:gravity="end"
                    android:orientation="horizontal"
                    android:padding="10dp"></LinearLayout>
                <LinearLayout
                    android:id="@+id/localvideocontainer"
                    android:layout_width="180dp"
                    android:layout_height="320dp"
                    android:layout_gravity="right|bottom"
                    android:orientation="horizontal"
                    android:padding="10dp"></LinearLayout>

            </FrameLayout>
        </LinearLayout>
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>Erstellen des Hauptaktivitätsgerüsts und der Bindungen

Nachdem das Layout erstellt wurde, können die Bindungen und das Grundgerüst der Aktivität hinzugefügt werden. Die Aktivität verarbeitet das Anfordern von Runtimeberechtigungen, erstellt den Anruf-Agent und platziert den Anruf, wenn die Schaltfläche gedrückt wird. Jede dieser Optionen wird in einem eigenen Abschnitt behandelt. Die Methode `onCreate` wird überschrieben, um `getAllPermissions` und `createAgent` aufzurufen und die Bindungen für die Anrufschaltfläche hinzuzufügen. Dies ist nur einmal der Fall, wenn die Aktivität erstellt wird. Weitere Informationen zu `onCreate` finden Sie im Handbuch mit [grundlegenden Informationen zum Aktivitätslebenszyklus](https://developer.android.com/guide/components/activities/activity-lifecycle).

Navigieren Sie zu **MainActivity.java**, und ersetzen Sie den Inhalt durch den folgenden Code:

```java
package com.example.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.media.AudioManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
import android.widget.LinearLayout;
import android.content.Context;
import com.azure.android.communication.calling.CallState;
import com.azure.android.communication.calling.CallingCommunicationException;
import com.azure.android.communication.calling.CameraFacing;
import com.azure.android.communication.calling.PropertyChangedEvent;
import com.azure.android.communication.calling.VideoDeviceInfo;
import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.communication.common.CommunicationIdentifier;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.StartCallOptions;
import com.azure.android.communication.calling.DeviceManager;
import com.azure.android.communication.calling.VideoOptions;
import com.azure.android.communication.calling.LocalVideoStream;
import com.azure.android.communication.calling.VideoStreamRenderer;
import com.azure.android.communication.calling.VideoStreamRendererView;
import com.azure.android.communication.calling.CreateViewOptions;
import com.azure.android.communication.calling.ScalingMode;
import com.azure.android.communication.calling.IncomingCall;
import com.azure.android.communication.calling.Call;
import com.azure.android.communication.calling.AcceptCallOptions;
import com.azure.android.communication.calling.ParticipantsUpdatedEvent;
import com.azure.android.communication.calling.RemoteParticipant;
import com.azure.android.communication.calling.RemoteVideoStream;
import com.azure.android.communication.calling.RemoteVideoStreamsEvent;
import com.azure.android.communication.calling.RendererListener;

import java.util.ArrayList;
import java.util.List;
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;

public class MainActivity extends AppCompatActivity {

    private CallAgent callAgent;
    private LocalVideoStream currentVideoStream;
    private DeviceManager deviceManager;
    private IncomingCall incomingCall;
    private Call call;
    VideoStreamRenderer previewRenderer;
    VideoStreamRendererView preview;
    final Map<Integer, StreamData> streamData = new HashMap<>();
    private boolean renderRemoteVideo = true;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();

        handleIncomingCall();

        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
        Button hangupButton = findViewById(R.id.hang_up);
        hangupButton.setOnClickListener(l -> hangUp());
        Button startVideo = findViewById(R.id.show_preview);
        startVideo.setOnClickListener(l -> turnOnLocalVideo());
        Button stopVideo = findViewById(R.id.hide_preview);
        stopVideo.setOnClickListener(l -> turnOffLocalVideo());
        
        setVolumeControlStream(AudioManager.STREAM_VOICE_CALL);
    }

    /**
     * Request each required permission if the app doesn't already have it.
     */
    private void getAllPermissions() {
        // See section on requesting permissions
    }

    /**
      * Create the call agent for placing calls
      */
    private void createAgent() {
        // See section on creating the call agent
    }

    /**
     * Place a call to the callee id provided in `callee_id` text input.
     */
    private void startCall() {
        // See section on starting the call
    }

    /**
     * Handle incoming calls
     */
    private void handleIncomingCall() {
        // See section on answering incoming call
    }

    /**
     * Mid-call operations
     */
    public void turnOnLocalVideo() {
        // See setion on 
    }
    public void turnOffLocalVideo() {
        
    }

    /**
     * End calls
     */
    private void hangUp() {
        // See section on ending the call
    }    
}
```

### <a name="request-permissions-at-runtime"></a>Anfordern von Berechtigungen zur Runtime

Für Android 6.0 und höher (API-Ebene 23) und `targetSdkVersion` 23 oder höher werden Berechtigungen zur Runtime gewährt und nicht bei der Installation der App. Damit dies unterstützt wird, kann `getAllPermissions` implementiert werden, um für jede erforderliche Berechtigung `ActivityCompat.checkSelfPermission` und `ActivityCompat.requestPermissions` aufzurufen.

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO, Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_PHONE_STATE};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 1);
    }
}
```

> [!NOTE]
> Berücksichtigen Sie beim Entwerfen der App, wann diese Berechtigungen angefordert werden sollen. Berechtigungen müssen angefordert werden, wenn sie benötigt werden, und nicht vorher. Weitere Informationen finden Sie in der [Android-Dokumentation zu Berechtigungen](https://developer.android.com/training/permissions/requesting).

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen befassen sich mit einigen der wichtigsten Features des Azure Communication Services Calling SDK:

| Name                                  | Beschreibung                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| „CallClient“ ist der Haupteinstiegspunkt des Calling SDK.|
| CallAgent | CallAgent dient zum Starten und Verwalten von Anrufen. |
| CommunicationTokenCredential | „CommunicationTokenCredential“ dient als tokengestützte Anmeldeinformation zum Instanziieren von „CallAgent“.|
| CommunicationIdentifier | „CommunicationIdentifier“ wird als anderer Typ von Teilnehmer verwendet, der Teil eines Anrufs sein könnte.|

## <a name="create-an-agent-from-the-user-access-token"></a>Erstellen eines Agents aus dem Benutzerzugriffstoken

Sie benötigen ein Benutzertoken, um einen authentifizierten Aufruf-Agent zu erstellen. In der Regel wird dieses Token von einem Dienst mit einer für die Anwendung spezifischen Authentifizierung generiert. Weitere Informationen zu Benutzerzugriffstoken finden Sie im Handbuch zu [Benutzerzugriffstoken](../../../access-tokens.md). 

Ersetzen Sie für den Schnellstart `<User_Access_Token>` durch ein Benutzerzugriffstoken, das für Ihre Azure Communication Service-Ressource generiert wurde.

```java
/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    Context context = this.getApplicationContext();
    String userToken = "<User_Access_Token>";
    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(userToken);
        CallClient callClient = new CallClient();
        deviceManager = callClient.getDeviceManager(context).get();
        callAgent = callClient.createCallAgent(getApplicationContext(), credential).get(); 
    } catch (Exception ex) {
        Toast.makeText(context, "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}
```
## <a name="start-a-video-call-using-the-call-agent"></a>Beginnen eines Anrufs mithilfe eines Anruf-Agents

Ein Anruf kann über den Anruf-Agent getätigt werden. Dafür müssen lediglich eine Liste von Angerufenen-IDs und die Anrufoptionen bereitgestellt werden. 

Um einen Videoanruf zu tätigen, müssen Sie die lokalen Kameras mit der `deviceManager` `getCameras` API auflisten. Sobald Sie eine gewünschte Kamera ausgewählt haben, erzeugen Sie damit eine `LocalVideoStream`-Instanz und übergeben diese an `videoOptions` als Element im `localVideoStream`-Array als Anrufmethode. Sobald die Anrufverbindung hergestellt ist, wird automatisch ein Videostream von der ausgewählten Kamera an andere Teilnehmer gesendet.

```java
private void startCall() {
    Context context = this.getApplicationContext();
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    ArrayList<CommunicationIdentifier> participants = new ArrayList<CommunicationIdentifier>();
    StartCallOptions options = new StartCallOptions();
    List<VideoDeviceInfo> cameras = deviceManager.getCameras();
    if(!cameras.isEmpty()) {
        VideoDeviceInfo camera = chooseCamera(cameras);
        currentVideoStream = new LocalVideoStream(camera, context);
        LocalVideoStream[] videoStreams = new LocalVideoStream[1];
        videoStreams[0] = currentVideoStream;
        VideoOptions videoOptions = new VideoOptions(videoStreams);
        options.setVideoOptions(videoOptions);
        showPreview(currentVideoStream);
    }
    participants.add(new CommunicationUserIdentifier(calleeId));

    call = callAgent.startCall(
            context,
            participants,
            options);
    
    //Subcribe to events on updates of call state and remote participants
    call.addOnRemoteParticipantsUpdatedListener(this::handleRemoteParticipantsUpdate);
    call.addOnStateChangedListener(this::handleCallOnStateChanged);
}
```

In dieser Schnellstartanleitung bevorzugen wir die Verwendung der Kamera an der Vorderseite des Geräts. Die Funktion `chooseCamera` nimmt die Enumeration von Kameras als Eingabe an. Wenn die Kamera an der Vorderseite nicht verfügbar ist, verwenden wir die erste verfügbare Kamera. Wenn beim Tippen auf `Start Call` keine Kameras verfügbar sind, wird ein Audioanruf gestartet. Wenn der Remote-Teilnehmer jedoch mit Video geantwortet hat, wird der Remote-Videostream weiterhin angezeigt. 

```java
VideoDeviceInfo chooseCamera(List<VideoDeviceInfo> cameras) {
    for (VideoDeviceInfo camera : cameras) {
        if (camera.getCameraFacing() == CameraFacing.FRONT) {
            return camera;
        }
    }
    return cameras.get(0);
}
```

Nachdem wir eine `LocalVideoStream`-Instanz erstellt haben, können wir einen Renderer erstellen, um ihn auf der Benutzeroberfläche anzuzeigen. 

```java
private void showPreview(LocalVideoStream stream) {
    previewRenderer = new VideoStreamRenderer(stream, this);
    LinearLayout layout = ((LinearLayout)findViewById(R.id.localvideocontainer));
    preview = previewRenderer.createView(new CreateViewOptions(ScalingMode.FIT));
    runOnUiThread(() -> {
        layout.addView(preview);
    });
}
```

## <a name="accept-an-incoming-call"></a>Annehmen eines eingehenden Anrufs

Der eingehende Anruf kann durch Abonnieren von `addOnIncomingCallListener` auf `callAgent` abgerufen werden. 

```java
private void handleIncomingCall() {
    callAgent.addOnIncomingCallListener((incomingCall) -> {
        this.incomingCall = incomingCall;
        Executors.newCachedThreadPool().submit(this::answerIncomingCall);
    });
}
```

Um einen Anruf mit eingeschalteter Videokamera zu akzeptieren, müssen Sie lokale Kameras mithilfe der getCameras-API von deviceManager aufzählen, eine Kamera auswählen und eine `LocalVideoStream`-Instanz erstellen und an `acceptCallOptions` übergeben, bevor Sie die Methode „accept“ für ein `call`-Objekt aufrufen. 
```java
private void answerIncomingCall() {
    Context context = this.getApplicationContext();
    if (incomingCall == null){
        return;
    }
    AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
    List<VideoDeviceInfo> cameras = deviceManager.getCameras();
    if(!cameras.isEmpty()) {
        VideoDeviceInfo camera = chooseCamera(cameras);
        currentVideoStream = new LocalVideoStream(camera, context);
        LocalVideoStream[] videoStreams = new LocalVideoStream[1];
        videoStreams[0] = currentVideoStream;
        VideoOptions videoOptions = new VideoOptions(videoStreams);
        acceptCallOptions.setVideoOptions(videoOptions);
        showPreview(currentVideoStream);
    }
    try {
        call = incomingCall.accept(context, acceptCallOptions).get();
    } catch (InterruptedException e) {
        e.printStackTrace();
    } catch (ExecutionException e) {
        e.printStackTrace();
    }

    //Subcribe to events on updates of call state and remote participants
    call.addOnRemoteParticipantsUpdatedListener(this::handleRemoteParticipantsUpdate);
    call.addOnStateChangedListener(this::handleCallOnStateChanged);
}
```

## <a name="remote-participant-and-remote-video-streams"></a>Remote-Teilnehmer- und -Videostreams

Wenn wir einen Anruf starten oder einen eingehenden Anruf beantworten, müssen wir das `addOnRemoteParticipantsUpdatedListener` Ereignis abonnieren, um Remote-Teilnehmer zu handhaben. 

```java
call.addOnRemoteParticipantsUpdatedListener(this::handleRemoteParticipantsUpdate);
```

### <a name="remote-participant-and-remote-video-stream-update"></a>Remote-Teilnehmer und -Videostreamupdate

In dieser Schnellstartanleitung behandeln wir nur hinzugefügte Teilnehmer, da wir 1:1-Aufrufe implementieren. Wenn der Remote-Teilnehmer entfernt wird, wird der Anruf beendet. Für hinzugefügte Teilnehmer abonnieren wir `addOnVideoStreamsUpdatedListener` für die Verarbeitung von Videostreamupdates. 

```java
public void handleRemoteParticipantsUpdate(ParticipantsUpdatedEvent args) {
    LinearLayout participantVideoContainer = findViewById(R.id.remotevideocontainer);
    handleAddedParticipants(args.getAddedParticipants(),participantVideoContainer);
}

private void handleAddedParticipants(List<RemoteParticipant> participants, LinearLayout participantVideoContainer) {
    for (RemoteParticipant remoteParticipant : participants) {
        remoteParticipant.addOnVideoStreamsUpdatedListener(videoStreamsEventArgs -> videoStreamsUpdated(videoStreamsEventArgs));
    }
}

private void videoStreamsUpdated(RemoteVideoStreamsEvent videoStreamsEventArgs) {
    for(RemoteVideoStream stream : videoStreamsEventArgs.getAddedRemoteVideoStreams()) {
        StreamData data = new StreamData(stream, null, null);
        streamData.put(stream.getId(), data);
        if (renderRemoteVideo) {
            startRenderingVideo(data);
        }
    }

    for(RemoteVideoStream stream : videoStreamsEventArgs.getRemovedRemoteVideoStreams()) {
        stopRenderingVideo(stream);
    }
}
```

### <a name="render-remote-videos"></a>Rendern von Remote-Videos

Erstellen Sie einen Renderer des Remote-Videostreams und fügen Sie ihn an die Ansicht an, um mit dem Rendern der Remote-Ansicht zu beginnen. Verwerfen Sie die Ansicht, um das Rendern zu beenden. 

```java
void startRenderingVideo(StreamData data){
    if (data.renderer != null) {
        return;
    }
    LinearLayout layout = ((LinearLayout)findViewById(R.id.remotevideocontainer));
    data.renderer = new VideoStreamRenderer(data.stream, this);
    data.renderer.addRendererListener(new RendererListener() {
        @Override
        public void onFirstFrameRendered() {
            String text = data.renderer.getSize().toString();
            Log.i("MainActivity", "Video rendering at: " + text);
        }

        @Override
        public void onRendererFailedToStart() {
            String text = "Video failed to render";
            Log.i("MainActivity", text);
        }
    });
    data.rendererView = data.renderer.createView(new CreateViewOptions(ScalingMode.FIT));
    runOnUiThread(() -> {
        layout.addView(data.rendererView);
    });
}

void stopRenderingVideo(RemoteVideoStream stream) {
    StreamData data = streamData.get(stream.getId());
    if (data == null || data.renderer == null) {
        return;
    }
    runOnUiThread(() -> {
        ((LinearLayout) findViewById(R.id.remotevideocontainer)).removeAllViews();
    });
    data.rendererView = null;
    // Dispose renderer
    data.renderer.dispose();
    data.renderer = null;
}

static class StreamData {
    RemoteVideoStream stream;
    VideoStreamRenderer renderer;
    VideoStreamRendererView rendererView;
    StreamData(RemoteVideoStream stream, VideoStreamRenderer renderer, VideoStreamRendererView rendererView) {
        this.stream = stream;
        this.renderer = renderer;
        this.rendererView = rendererView;
    }
}
```

## <a name="call-state-update"></a>Zustandsaktualisierung aufrufen
In dieser Schnellstartanleitung behandeln wir die Änderungen von `CallState`. Wenn der Anruf verbunden ist, verarbeiten wir den Remote-Teilnehmer, und wenn der Anruf getrennt wird, veräußern wir das `previewRenderer`, um das lokale Video zu beenden. 

```groovy
private void handleCallOnStateChanged(PropertyChangedEvent args) {
    if (call.getState() == CallState.CONNECTED) {
        LinearLayout participantVideoContainer = findViewById(R.id.remotevideocontainer);
        handleAddedParticipants(call.getRemoteParticipants(),participantVideoContainer);
    }
    if (call.getState() == CallState.DISCONNECTED) {
        if (previewRenderer != null) {
            previewRenderer.dispose();
        }
    }
}
```

## <a name="end-a-call"></a>Beenden eines Anrufs
Beenden Sie den Anruf, indem Sie die `hangUp()` Funktion für die Anrufinstanz aufrufen und den `previewRenderer` zum Beenden des lokalen Videos veräußern. 
```java
private void hangUp() {
    try {
        call.hangUp().get();
    } catch (ExecutionException | InterruptedException e) {
        e.printStackTrace();
    }
    if (previewRenderer != null) {
        previewRenderer.dispose();
    }
}
```

## <a name="run-the-code"></a>Ausführen des Codes

Die App kann jetzt mithilfe der Schaltfläche `Run 'App'` auf der Symbolleiste von Android Studio gestartet werden. 
