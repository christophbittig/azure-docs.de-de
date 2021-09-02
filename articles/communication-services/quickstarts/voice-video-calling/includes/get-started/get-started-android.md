---
title: Schnellstart – Hinzufügen von VoIP-Telefonie zu einer Android-App mithilfe von Azure Communication Services
description: In diesem Tutorial erfahren Sie, wie Sie das Calling SDK von Azure Communication Services für Android verwenden
author: chpalm
ms.author: rifox
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 3953bdeaed6a20417b321ce89cd6b72ee052058a
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122970242"
---
In diesem Schnellstart erfahren Sie, wie Sie einen Anruf mithilfe des Calling SDK von Azure Communication Services für Android beginnen.

## <a name="sample-code"></a>Beispielcode

Sie können die Beispiel-App von [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/Add%20Voice%20Calling) herunterladen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio) zum Erstellen Ihrer Android-Anwendung
- Eine bereitgestellte Communication Services-Ressource. [Erstellen einer Communication Services-Ressource](../../../create-communication-resource.md)
- Ein [Benutzerzugriffstoken](../../../access-tokens.md) für Ihren Azure Communication Service

## <a name="setting-up"></a>Einrichten

### <a name="create-an-android-app-with-an-empty-activity"></a>Erstellen Sie eine Android-App mit einer leeren Aktivität.

Wählen Sie in Android Studio „Start a new Android Studio project“ (Neues Android Studio-Projekt starten) aus.

:::image type="content" source="../../media/android/studio-new-project.png" alt-text="Ein Screenshot, der die Schaltfläche „Start a new Android Studio project“ (Neues Android Studio-Projekt starten) zeigt, die in Android Studio ausgewählt ist":::

Wählen Sie unter „Phone and Tablet“ (Telefon und Tablet) die Projektvorlage „Empty Activity“ (Leere Aktivität) aus.

:::image type="content" source="../../media/android/studio-blank-activity.png" alt-text="Screenshot, der die Option „Empty Activity“ (Leere Aktivität) zeigt, die auf dem Bildschirm für die Projektvorlage ausgewählt ist":::

Wählen Sie das SDK „API 26: Android 8.0 (Oreo)“ oder höher aus.

:::image type="content" source="../../media/android/studio-calling-min-api.png" alt-text="Screenshot, der die Option „Empty Activity“ (Leere Aktivität) zeigt, die auf dem Bildschirm für die Projektvorlage 2 ausgewählt ist":::


### <a name="install-the-package"></a>Installieren des Pakets

<!-- TODO: update with instructions on how to download, install and add package to project -->
Suchen Sie die Datei „build.gradle“ auf Projektebene, und fügen Sie `mavenCentral()` zur Liste der Repositorys unter `buildscript` und `allprojects` hinzu.
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
Fügen Sie anschließend in der Datei „build.gradle“ auf Modulebene die folgenden Zeilen zu den Abschnitten „dependencies“ und „android“ hinzu.

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
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.8'
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
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />

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

Zwei Eingaben sind erforderlich: eine Texteingabe für die Angerufenen-ID und eine Schaltfläche zum Tätigen des Anrufs. Diese können über den Designer oder durch Bearbeiten der Layout-XML-Datei hinzugefügt werden. Erstellen Sie eine Schaltfläche mit der ID von `call_button` und einer Texteingabe für `callee_id`. Navigieren Sie zu (`app/src/main/res/layout/activity_main.xml`), und ersetzen Sie den Inhalt der Datei durch Folgendes:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/call_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:text="Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <EditText
        android:id="@+id/callee_id"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="Callee Id"
        android:inputType="textPersonName"
        app:layout_constraintBottom_toTopOf="@+id/call_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>Erstellen des Hauptaktivitätsgerüsts und der Bindungen

Nachdem das Layout erstellt wurde, können die Bindungen und das Grundgerüst der Aktivität hinzugefügt werden. Die Aktivität verarbeitet das Anfordern von Runtimeberechtigungen, erstellt den Anruf-Agent und platziert den Anruf, wenn die Schaltfläche gedrückt wird. Jede dieser Optionen wird in einem eigenen Abschnitt behandelt. Die Methode `onCreate` wird überschrieben, um `getAllPermissions` und `createAgent` aufzurufen und die Bindungen für die Anrufschaltfläche hinzuzufügen. Dies ist nur einmal der Fall, wenn die Aktivität erstellt wird. Weitere Informationen zu `onCreate` finden Sie im Handbuch mit [grundlegenden Informationen zum Aktivitätslebenszyklus](https://developer.android.com/guide/components/activities/activity-lifecycle).

Navigieren Sie zu **MainActivity.java**, und ersetzen Sie den Inhalt durch den folgenden Code:

```java
package com.contoso.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;
import android.media.AudioManager;
import android.Manifest;
import android.content.pm.PackageManager;

import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.StartCallOptions;


import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {
    
    private CallAgent callAgent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();
        
        // Bind call button to call `startCall`
        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
        
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

Mit einem Benutzertoken kann ein authentifizierter Anruf-Agent instanziiert werden. In der Regel wird dieses Token von einem Dienst mit einer für die Anwendung spezifischen Authentifizierung generiert. Weitere Informationen zu Benutzerzugriffstoken finden Sie im Handbuch zu [Benutzerzugriffstoken](../../../access-tokens.md). 

Ersetzen Sie für den Schnellstart `<User_Access_Token>` durch ein Benutzerzugriffstoken, das für Ihre Azure Communication Service-Ressource generiert wurde.

```java

/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    String userToken = "<User_Access_Token>";

    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(userToken);
        callAgent = new CallClient().createCallAgent(getApplicationContext(), credential).get();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}

```

## <a name="start-a-call-using-the-call-agent"></a>Beginnen eines Anrufs mithilfe eines Anruf-Agents

Ein Anruf kann über den Anruf-Agent getätigt werden. Dafür müssen lediglich eine Liste von Angerufenen-IDs und die Anrufoptionen bereitgestellt werden. Im Schnellstart werden die standardmäßigen Anrufoptionen ohne Video und eine einzelne Angerufenen-ID aus der Texteingabe verwendet.

```java
/**
 * Place a call to the callee id provided in `callee_id` text input.
 */
private void startCall() {
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    
    StartCallOptions options = new StartCallOptions();

    callAgent.startCall(
        getApplicationContext(),
        new CommunicationUserIdentifier[] {new CommunicationUserIdentifier(calleeId)},
        options);
}
```


## <a name="launch-the-app-and-call-the-echo-bot"></a>Starten der App und Anrufen des Echobots

Die App kann jetzt mithilfe der Schaltfläche „Run app“ (App ausführen) auf der Symbolleiste gestartet werden (UMSCHALT + F10). Überprüfen Sie, ob Sie Anrufe tätigen können, indem Sie `8:echo123` anrufen. Eine vorab aufgezeichnete Nachricht wird wiederholt und gibt anschließend Ihre Nachricht an Sie selbst wieder.

:::image type="content" source="../../media/android/quickstart-android-call-echobot.png" alt-text="Screenshot, der die fertige Anwendung zeigt":::
