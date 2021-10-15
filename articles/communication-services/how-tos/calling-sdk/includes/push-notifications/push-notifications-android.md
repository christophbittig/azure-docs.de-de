---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 9ecf53a84a804cd312f4d3a98661f4e688726577
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838210"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

### <a name="additional-prerequisites-for-push-notifications"></a>Zusätzliche Voraussetzungen für Pushbenachrichtigungen

Ein Firebase-Konto, das mit aktiviertem Cloud Messaging (FCM) eingerichtet wurde und bei dem Ihr Firebase Cloud Messaging-Dienst mit einer Azure Notification Hub-Instanz verbunden ist. Weitere Informationen finden Sie unter [Communication Services-Benachrichtigungen](../../../../concepts/notifications.md).
Außerdem geht das Tutorial davon aus, dass Sie zur Entwicklung Ihrer Anwendung mindestens die Android Studio-Version 3.6 einsetzen.

Für die Android-Anwendung sind eine Reihe von Berechtigungen erforderlich, um Benachrichtigungen von Firebase Cloud Messaging empfangen zu können. Fügen Sie in Ihrer Datei `AndroidManifest.xml` direkt nach `<manifest ...>` oder unter dem Tag `</application>` den folgenden Berechtigungssatz hinzu.

```xml
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

## <a name="overview"></a>Übersicht
Mobile Pushbenachrichtigungen sind die Popupbenachrichtigungen, die auf mobilen Geräten angezeigt werden. Bei Anruffunktionen konzentrieren wir uns auf VoIP-Pushbenachrichtigungen (Voice over Internet Protocol). Wir werden uns für Pushbenachrichtigungen registrieren, Pushbenachrichtigungen bearbeiten und dann die Registrierung von Pushbenachrichtigungen aufheben.

## <a name="register-for-push-notifications"></a>Registrieren für Pushbenachrichtigungen

Um sich für Pushbenachrichtigungen zu registrieren, muss die Anwendung `registerPushNotification()` für eine `CallAgent`-Instanz mit einem Geräteregistrierungstoken aufrufen.

Um das Geräteregistrierungstoken zu erhalten, fügen Sie das Firebase SDK zur Datei `build.gradle` Ihres Anwendungsmoduls hinzu, indem Sie die folgenden Zeilen im Abschnitt `dependencies` hinzufügen (sofern noch nicht vorhanden):

```groovy
// Add the SDK for Firebase Cloud Messaging
implementation 'com.google.firebase:firebase-core:16.0.8'
implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

Fügen Sie in der Datei *build.gradle* auf Projektebene im Abschnitt `dependencies` Folgendes hinzu, sofern noch nicht vorhanden:

```groovy
classpath 'com.google.gms:google-services:4.3.3'
```

Fügen Sie das folgende Plug-In am Anfang der Datei hinzu, sofern noch nicht vorhanden:

```groovy
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

## <a name="push-notification-handling"></a>Behandlung von Pushbenachrichtigungen

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
Fügen Sie der Datei `AndroidManifest.xml` im Tag `<application>` die unten angegebene Dienstdefinition hinzu:

```xml
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

## <a name="unregister-push-notifications"></a>Aufheben der Registrierung der Pushbenachrichtigungen

Anwendungen können die Registrierung der Pushbenachrichtigung jederzeit aufheben. Rufen Sie die Methode `unregisterPushNotification()` für callAgent auf, um die Registrierung aufzuheben.

```java
try {
    callAgent.unregisterPushNotification().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```