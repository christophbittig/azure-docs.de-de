---
title: Senden von Pushbenachrichtigungen an Android mit Azure Notification Hubs und Google Cloud Messaging | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Notification Hubs und Google Firebase Cloud Messaging Pushbenachrichtigungen an Android-Geräte senden.
services: notification-hubs
documentationcenter: android
keywords: Pushbenachrichtigungen,Pushbenachrichtigung,Android-Pushbenachrichtigung
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 4781bd532402b1f92d1027f3aab2a9ae938d47d5
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042900"
---
# <a name="tutorial-send-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-cloud-messaging-deprecated"></a>Tutorial: Senden von Pushbenachrichtigungen an Android-Geräte mit Azure Notification Hubs und Google Cloud Messaging (veraltet)

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

> [!WARNING]
> Seit dem 10. April 2018 wird Google Cloud Messaging (GCM) von Google nicht mehr unterstützt. Der GCM-Server und die Client-APIs sind veraltet und werden am 29. Mai 2019 entfernt. Weitere Informationen finden Sie unter [GCM and FCM Frequently Asked Questions](https://developers.google.com/cloud-messaging/faq) (Häufig gestellte Fragen zu GCM und FCM).

## <a name="overview"></a>Übersicht

In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Android-App senden.
Sie erstellen eine leere Android-App, die Pushbenachrichtigungen mithilfe von Google Cloud Messaging (GCM) empfängt.

> [!IMPORTANT]
> Google Cloud Messaging (GCM) ist veraltet und steht [in Kürze](https://developers.google.com/cloud-messaging/faq) nicht mehr zur Verfügung.

> [!IMPORTANT]
> In diesem Thema werden Pushbenachrichtigungen mit Google Cloud Messaging (GCM) veranschaulicht. Falls Sie Google Firebase Cloud Messaging (FCM) verwenden, helfen Ihnen die Informationen unter [Sending push notifications to Android with Azure Notification Hubs and FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)(Senden von Pushbenachrichtigungen an Android mit Azure Notification Hubs und FCM) weiter.

Den vollständigen Code für dieses Tutorial können Sie [hier](https://github.com/Azure/azure-notificationhubs-android/tree/master/FCMTutorialApp)bei GitHub herunterladen.

In diesem Tutorial führen Sie die folgenden Aktionen aus:

> [!div class="checklist"]
> * Erstellen eines Projekts, das Google Cloud Messaging unterstützt
> * Erstellen eines Notification Hubs
> * Verbinden Ihrer App mit dem Notification Hub
> * Testen der App

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement**. Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.
* [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)

## <a name="creating-a-project-that-supports-google-cloud-messaging"></a>Erstellen eines Projekts, das Google Cloud Messaging unterstützt

[!INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>Erstellen eines Notification Hubs

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-setting-for-the-notification-hub"></a>Konfigurieren von GCM-Einstellungen für den Benachrichtigungshub

1. Klicken Sie unter **BENACHRICHTIGUNGSEINSTELLUNGEN** auf **Google (GCM)** .
2. Geben Sie den **API-Schlüssel** ein, den Sie aus Google Cloud Console abgerufen haben.
3. Wählen Sie auf der Symbolleiste **Speichern** aus.

    ![Azure Notification Hubs – Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Der Notification Hub ist jetzt für die Arbeit mit GCM konfiguriert, und Sie besitzen die Verbindungszeichenfolge, um die App für den Empfang und das Senden von Pushbenachrichtigungen zu registrieren.

## <a name="connect-your-app-to-the-notification-hub"></a><a id="connecting-app"></a>Verbinden Ihrer App mit dem Notification Hub

### <a name="create-a-new-android-project"></a>Erstellen eines neuen Android-Projekts

1. Starten Sie in Android Studio ein neues Android Studio-Projekt.

   ![Android Studio – Neues Projekt][13]
2. Wählen Sie den Formfaktor **Phone and Tablet** und das **Minimum SDK** aus, das unterstützt werden soll. Klicken Sie dann auf **Weiter**.

   ![Android Studio – Projekterstellungsworkflow][14]
3. Wählen Sie **Empty Activity** als Hauptaktivität aus, und klicken Sie auf **Weiter** und anschließend auf **Fertig stellen**.

### <a name="add-google-play-services-to-the-project"></a>Hinzufügen von Google Play Services zum Projekt

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Hinzufügen von Azure Notification Hubs-Bibliotheken

1. Fügen Sie in der Datei `Build.Gradle` für die **App** die folgenden Zeilen im Abschnitt **dependencies** hinzu.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Fügen Sie das folgende Repository nach dem Abschnitt **dependencies** hinzu.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="updating-the-projects-androidmanifestxml"></a>Aktualisieren Sie die Datei „AndroidManifest.xml“ des Projekts.

1. Für die Unterstützung von GCM müssen Sie im Code einen Instanz-ID-Listenerdienst implementieren, der zum [Beschaffen von Registrierungstoken](https://developers.google.com/cloud-messaging/) mit der [Instanz-ID-API von Google](https://developers.google.com/instance-id/) verwendet wird. In diesem Tutorial lautet der Name der Klasse `MyInstanceIDService`.

    Fügen Sie der Datei „AndroidManifest.xml“ im Tag `<application>` die unten angegebene Dienstdefinition hinzu. Ersetzen Sie den Platzhalter `<your package>` durch Ihren tatsächlichen Namen des Pakets, der oben in der Datei `AndroidManifest.xml` angegeben ist.
  
    ```xml
    <service android:name="<your package>.MyInstanceIDService" android:exported="false">
        <intent-filter>
            <action android:name="com.google.android.gms.iid.InstanceID"/>
        </intent-filter>
    </service>
    ```

2. Nachdem die Anwendung das GCM-Registrierungstoken von der Instanz-ID-API erhalten hat, verwendet sie das Token zum [Registrieren bei Azure Notification Hub](notification-hubs-push-notification-registration-management.md). Die Registrierung wird im Hintergrund mit einem `IntentService`-Element mit dem Namen `RegistrationIntentService` unterstützt. Dieser Dienst ist für das [Aktualisieren des GCM-Registrierungstokens](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) zuständig.

    Fügen Sie der Datei „AndroidManifest.xml“ im Tag `<application>` die unten angegebene Dienstdefinition hinzu. Ersetzen Sie den Platzhalter `<your package>` durch Ihren tatsächlichen Namen des Pakets, der oben in der Datei `AndroidManifest.xml` angegeben ist.

    ```xml
    <service
        android:name="<your package>.RegistrationIntentService"
        android:exported="false">
    </service>
    ```

3. Definieren Sie einen Empfänger für die Benachrichtigungen. Fügen Sie der Datei „AndroidManifest.xml“ im Tag `<application>` die unten angegebene Empfängerdefinition hinzu. Ersetzen Sie den Platzhalter `<your package>` durch Ihren tatsächlichen Namen des Pakets, der oben in der Datei `AndroidManifest.xml` angegeben ist.

    ```xml
    <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <category android:name="<your package name>" />
        </intent-filter>
    </receiver>
    ```

4. Fügen Sie unterhalb des Tags `<application>` die folgenden erforderlichen GCM-bezogenen Berechtigungen hinzu. Ersetzen Sie dabei `<your package>` durch den oben in der Datei `AndroidManifest.xml` angegebenen Paketnamen.

    Weitere Informationen zu diesen Berechtigungen finden Sie unter [Einrichten einer GCM-Client-App für Android](https://developers.google.com/cloud-messaging/).

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

    <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>
    ```

### <a name="adding-code"></a>Hinzufügen von Code

1. Erweitern Sie in der Projektansicht die Knoten **app** > **src** > **main** > **java**. Klicken Sie mit der rechten Maustaste auf Ihren Paketordner unter **java**, klicken Sie auf **Neu** und dann auf **Java Class**. Fügen Sie eine neue Klasse namens `NotificationSettings`hinzu.

    ![Android Studio – Neue Java-Klasse][6]

    Aktualisieren Sie die drei folgenden Platzhalter im weiter unten angegebenen Code für die `NotificationSettings`-Klasse:

   * `SenderId`: Dies ist die Projektnummer, die Sie zuvor über die [Google Cloud Console](https://cloud.google.com/console) abgerufen haben.
   * `HubListenConnectionString`: Die **DefaultListenAccessSignature**-Verbindungszeichenfolge für Ihren Hub. Sie können diese Verbindungszeichenfolge kopieren, indem Sie im [Azure portal] auf der Seite **Einstellungen** Ihres Hub auf **Zugriffsrichtlinien** klicken.
   * `HubName`: Verwenden Sie den Namen Ihres Benachrichtigungshubs, der im [Azure portal] auf der Hub-Seite angezeigt wird.

     `NotificationSettings` -Code:

     ```java
     public class NotificationSettings {
        public static String SenderId = "<Your project number>";
        public static String HubName = "<Your HubName>";
        public static String HubListenConnectionString = "<Your default listen connection string>";
     }
     ```

2. Fügen Sie eine weitere neue Klasse namens `MyInstanceIDService` hinzu. Diese Klasse ist die Implementierung des Instanz-ID-Listenerdiensts.

    Mit dem Code für diese Klasse wird Ihr `IntentService`-Element aufgerufen, um im Hintergrund [das GCM-Token zu aktualisieren](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).

    ```java
    import android.content.Intent;
    import android.util.Log;
    import com.google.android.gms.iid.InstanceIDListenerService;

    public class MyInstanceIDService extends InstanceIDListenerService {

        private static final String TAG = "MyInstanceIDService";

        @Override
        public void onTokenRefresh() {

            Log.i(TAG, "Refreshing GCM Registration Token");

            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    };
    ```

3. Fügen Sie dem Projekt eine weitere neue Klasse namens `RegistrationIntentService` hinzu. Diese Klasse implementiert `IntentService` zum [Aktualisieren des GCM-Tokens](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) und [Registrieren beim Benachrichtigungshub](notification-hubs-push-notification-registration-management.md).

    Verwenden Sie für diese Klasse den folgenden Code.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.google.android.gms.iid.InstanceID;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {
            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;

            try {
                InstanceID instanceID = InstanceID.getInstance(this);
                String token = instanceID.getToken(NotificationSettings.SenderId,
                        GoogleCloudMessaging.INSTANCE_ID_SCOPE);
                Log.i(TAG, "Got GCM Registration Token: " + token);

                // Storing the registration id that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server,
                // otherwise your server should have already received the token.
                if ((regID=sharedPreferences.getString("registrationID", null)) == null) {
                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.i(TAG, "Attempting to register with NH using token : " + token);

                    regID = hub.register(token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1", "tag2").getRegistrationId();

                    resultString = "Registered Successfully - RegId : " + regID;
                    Log.i(TAG, resultString);
                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                } else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete token refresh", e);
                // If an exception happens while fetching the new token or updating the registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

4. Fügen Sie in der `MainActivity`-Klasse am Anfang der Klasse die folgenden `import`-Anweisungen hinzu.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import com.google.android.gms.gcm.*;
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    import android.content.Intent;
    ```

5. Fügen Sie folgende private Member oben in der Klasse hinzu. Dieser Code [überprüft die Verfügbarkeit von Google Play Services gemäß Empfehlung von Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private GoogleCloudMessaging gcm;
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    private static final String TAG = "MainActivity";
    ```

6. Fügen Sie in der `MainActivity` -Klasse die folgende Methode zur Verfügbarkeit von Google Play Services hinzu.

    ```java
    /**
        * Check the device to make sure it has the Google Play Services APK. If
        * it doesn't, display a dialog that allows users to download the APK from
        * the Google Play Store or enable it in the device's system settings.
        */
    private boolean checkPlayServices() {
        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (apiAvailability.isUserResolvableError(resultCode)) {
                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                        .show();
            } else {
                Log.i(TAG, "This device is not supported by Google Play Services.");
                ToastNotify("This device is not supported by Google Play Services.");
                finish();
            }
            return false;
        }
        return true;
    }
    ```

7. Fügen Sie in der `MainActivity`-Klasse den folgenden Code hinzu, mit dem eine Überprüfung auf Google Play Services durchgeführt wird, bevor das `IntentService`-Element aufgerufen wird, um Ihr GCM-Registrierungstoken abzurufen und die Registrierung beim Benachrichtungshub vorzunehmen.

    ```java
    public void registerWithNotificationHubs()
    {
        Log.i(TAG, " Registering with Notification Hubs");

        if (checkPlayServices()) {
            // Start IntentService to register this application with GCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

8. Fügen Sie in der `OnCreate`-Methode der `MainActivity`-Klasse den folgenden Code hinzu, um den Registrierungsprozess zu starten, wenn die Aktivität erstellt wird.

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
        registerWithNotificationHubs();
    }
    ```

9. Fügen Sie diese zusätzlichen Methoden dem `MainActivity` -Element hinzu, um den App-Zustand zu überprüfen und den Status in Ihrer App zu melden.

    ```java
    @Override
    protected void onStart() {
        super.onStart();
        isVisible = true;
    }

    @Override
    protected void onPause() {
        super.onPause();
        isVisible = false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        isVisible = true;
    }

    @Override
    protected void onStop() {
        super.onStop();
        isVisible = false;
    }

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```

10. Für die `ToastNotify`-Methode wird das `TextView`-Steuerelement *„Hello World“* verwendet, um Status und Benachrichtigungen dauerhaft in der App zu melden. Fügen Sie im Layout von „activity_main.xml“ die folgende ID für dieses Steuerelement hinzu.

    ```xml
    android:id="@+id/text_hello"
    ```

11. Fügen Sie eine Unterklasse für den Empfänger hinzu, der in „AndroidManifest.xml“ definiert wurde. Fügen Sie dem Projekt eine weitere neue Klasse namens `MyHandler`hinzu.

12. Fügen Sie am Anfang von `MyHandler.java` die folgenden Importanweisungen hinzu:

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.os.Bundle;
    import android.support.v4.app.NotificationCompat;
    import com.microsoft.windowsazure.notifications.NotificationsHandler;
    import android.net.Uri;
    import android.media.RingtoneManager;
    ```

13. Fügen Sie für die `MyHandler`-Klasse den folgenden Code hinzu, um sie als Unterklasse von `com.microsoft.windowsazure.notifications.NotificationsHandler` festzulegen.

    Mit diesem Code wird die `OnReceive`-Methode überschrieben, sodass der Handler empfangene Benachrichtigungen meldet. Der Handler sendet die Pushbenachrichtigung mit der `sendNotification()` -Methode auch an den Android-Benachrichtigungs-Manager. Die `sendNotification()` -Methode muss ausgeführt werden, wenn die App nicht ausgeführt wird und eine Benachrichtigung eingeht.

    ```java
    public class MyHandler extends NotificationsHandler {
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");
            sendNotification(nhMessage);
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
        }

        private void sendNotification(String msg) {

            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);

            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);

            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder mBuilder =
                    new NotificationCompat.Builder(ctx)
                            .setSmallIcon(R.mipmap.ic_launcher)
                            .setContentTitle("Notification Hub Demo")
                            .setStyle(new NotificationCompat.BigTextStyle()
                                    .bigText(msg))
                            .setSound(defaultSoundUri)
                            .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }
    }
    ```

14. Klicken Sie in Android Studio auf der Menüleiste auf **Build** > **Rebuild Project**, um sicherzustellen, dass im Code keine Fehler enthalten sind.

## <a name="testing-your-app"></a>Testen der App

### <a name="run-the-mobile-application"></a>Ausführen der mobilen Anwendung

1. Führen Sie die App aus, und prüfen Sie, ob die Registrierungs-ID als erfolgreiche Registrierung gemeldet wird.

      ![Testen unter Android – Kanalregistrierung][18]
2. Geben Sie eine Benachrichtigungsmeldung ein, die an alle für den Hub registrierten Android-Geräte gesendet werden soll.

      ![Testen unter Android – Senden einer Nachricht][19]

3. Tippen Sie auf **Send Notification**. Auf allen Geräten, auf denen die App ausgeführt wird, wird eine `AlertDialog`-Instanz mit der Pushbenachrichtigungsnachricht angezeigt. Geräte, auf denen die App nicht ausgeführt wird, die aber zuvor für Pushbenachrichtigungen registriert wurden, erhalten im Android-Benachrichtigungs-Manager eine Benachrichtigung. Die Benachrichtigungsmeldungen können durch Wischen von der linken oberen Ecke nach unten angezeigt werden.

      ![Testen unter Android – Benachrichtigungen][21]

### <a name="test-send-push-notifications-from-the-azure-portal"></a>Senden von Test-Pushbenachrichtigungen im Azure-Portal

Sie können den Empfang von Pushbenachrichtigungen in Ihrer App testen, indem Sie sie über das [Azure portal] senden.

1. Klicken Sie im Abschnitt **Problembehandlung** auf **Testsendevorgang**.
2. Wählen Sie für **Plattformen** die Option **Android** aus.
3. Klicken Sie auf **Senden**, um die Testbenachrichtigung zu senden.
4. Vergewissern Sie sich, dass die Benachrichtigungsmeldung auf dem Android-Gerät angezeigt wird.

    ![Azure Notification Hubs – Testsendung](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

#### <a name="push-notifications-in-the-emulator"></a>Pushbenachrichtigungen im Emulator

Wenn Sie Pushbenachrichtigungen in einem Emulator testen möchten, müssen Sie sicherstellen, dass das Emulatorabbild die Google-API-Ebene unterstützt, die Sie für die App ausgewählt haben. Wenn das Image keine nativen Google-APIs unterstützt, wird die Ausnahme **SERVICE\_NOT\_AVAILABLE** ausgegeben.

Stellen Sie zusätzlich sicher, dass Ihr Google-Konto dem ausgeführten Emulator unter **Einstellungen** > **Konten** hinzugefügt wurde. Andernfalls führt die Registrierung bei GCM möglicherweise zur Ausnahme **AUTHENTICATION\_FAILED**.

## <a name="optional-send-push-notifications-directly-from-the-app"></a>(Optional) Senden von Pushbenachrichtigungen direkt aus der App

In der Regel werden Benachrichtigungen über einen Back-End-Server versendet. In einigen Fällen ist es jedoch wünschenswert, wenn Sie Pushbenachrichtigungen direkt aus der Clientanwendung senden können. In diesem Abschnitt wird erläutert, wie Sie mithilfe der [Azure Notification Hub-REST-API](/previous-versions/azure/reference/dn223264(v=azure.100))Benachrichtigungen vom Client senden können.

1. Erweitern Sie in der Projektansicht von Android Studio **App** > **src** > **main** > **res** > **layout**. Öffnen Sie die Layoutdatei `activity_main.xml`, und klicken Sie auf die Registerkarte **Text**, um den Textinhalt der Datei zu aktualisieren. Aktualisieren Sie ihn mit dem folgenden Code. Dadurch werden die neuen Steuerelemente `Button` und `EditText` hinzugefügt, um Nachrichten mit Pushbenachrichtigungen an den Notification Hub zu senden. Fügen Sie diesen Code am Ende der Datei unmittelbar vor `</RelativeLayout>` hinzu.

    ```xml
    <Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/send_button"
    android:id="@+id/sendbutton"
    android:layout_centerVertical="true"
    android:layout_centerHorizontal="true"
    android:onClick="sendNotificationButtonOnClick" />

    <EditText
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:id="@+id/editTextNotificationMessage"
    android:layout_above="@+id/sendbutton"
    android:layout_centerHorizontal="true"
    android:layout_marginBottom="42dp"
    android:hint="@string/notification_message_hint" />
    ```

2. Erweitern Sie in der Projektansicht von Android Studio **App** > **src** > **main** > **res** > **values**. Öffnen Sie die Datei `strings.xml`, und fügen Sie die Zeichenfolgenwerte hinzu, auf die von den neuen Steuerelementen `Button` und `EditText` verwiesen wird. Fügen Sie die folgenden Zeilen am Ende der Datei unmittelbar vor `</resources>` ein.

    ```xml
    <string name="send_button">Send Notification</string>
    <string name="notification_message_hint">Enter notification message text</string>
    ```

3. Fügen Sie in der Datei `NotificationSetting.java` der `NotificationSettings`-Klasse die folgende Einstellung hinzu.

    Aktualisieren Sie `HubFullAccess` mit der Verbindungszeichenfolge **DefaultFullSharedAccessSignature** für Ihren Hub. Sie können diese Verbindungszeichenfolge aus dem [Azure portal] kopieren, indem Sie auf der Seite **Einstellungen** für den Benachrichtigungshub auf **Zugriffsrichtlinien** klicken.

    ```java
    public static String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";
    ```

4. Fügen Sie in der Datei `MainActivity.java` am Anfang der Datei die folgenden `import`-Anweisungen hinzu.

    ```java
    import java.io.BufferedOutputStream;
    import java.io.BufferedReader;
    import java.io.InputStreamReader;
    import java.io.OutputStream;
    import java.net.HttpURLConnection;
    import java.net.URL;
    import java.net.URLEncoder;
    import javax.crypto.Mac;
    import javax.crypto.spec.SecretKeySpec;
    import android.util.Base64;
    import android.view.View;
    import android.widget.EditText;
    ```

5. Fügen Sie in der Datei `MainActivity.java` im oberen Bereich der `MainActivity`-Klasse die folgenden Member hinzu.

    ```java
    private String HubEndpoint = null;
    private String HubSasKeyName = null;
    private String HubSasKeyValue = null;
    ```

6. Erstellen Sie ein SaS-Token (Shared Access Signature), um eine POST-Anforderung zum Senden von Nachrichten an den Benachrichtigungshub zu authentifizieren. Analysieren Sie die wichtigsten Daten aus der Verbindungszeichenfolge, und erstellen Sie anschließend das SaS-Token (siehe dazu die REST-API-Referenz unter [Allgemeine Konzepte](/previous-versions/azure/reference/dn495627(v=azure.100))). Der folgende Code ist ein Beispiel für eine Implementierung.

    Fügen Sie der `MainActivity`-Klasse in `MainActivity.java` die folgende Methode hinzu, um die Verbindungszeichenfolge zu analysieren.

    ```java
    /**
        * Example code from https://msdn.microsoft.com/library/azure/dn495627.aspx
        * to parse the connection string so a SaS authentication token can be
        * constructed.
        *
        * @param connectionString This must be the DefaultFullSharedAccess connection
        *                         string for this example.
        */
    private void ParseConnectionString(String connectionString)
    {
        String[] parts = connectionString.split(";");
        if (parts.length != 3)
            throw new RuntimeException("Error parsing connection string: "
                    + connectionString);

        for (int i = 0; i < parts.length; i++) {
            if (parts[i].startsWith("Endpoint")) {
                this.HubEndpoint = "https" + parts[i].substring(11);
            } else if (parts[i].startsWith("SharedAccessKeyName")) {
                this.HubSasKeyName = parts[i].substring(20);
            } else if (parts[i].startsWith("SharedAccessKey")) {
                this.HubSasKeyValue = parts[i].substring(16);
            }
        }
    }
    ```

7. Fügen Sie der `MainActivity`-Klasse in `MainActivity.java` die folgende Methode hinzu, um ein SAS-Authentifizierungstoken zu erstellen.

    ```java
    /**
        * Example code from https://msdn.microsoft.com/library/azure/dn495627.aspx to
        * construct a SaS token from the access key to authenticate a request.
        *
        * @param uri The unencoded resource URI string for this operation. The resource
        *            URI is the full URI of the Service Bus resource to which access is
        *            claimed. For example,
        *            "http://<namespace>.servicebus.windows.net/<hubName>"
        */
    private String generateSasToken(String uri) {

        String targetUri;
        String token = null;
        try {
            targetUri = URLEncoder
                    .encode(uri.toString().toLowerCase(), "UTF-8")
                    .toLowerCase();

            long expiresOnDate = System.currentTimeMillis();
            int expiresInMins = 60; // 1 hour
            expiresOnDate += expiresInMins * 60 * 1000;
            long expires = expiresOnDate / 1000;
            String toSign = targetUri + "\n" + expires;

            // Get an hmac_sha1 key from the raw key bytes
            byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

            // Get an hmac_sha1 Mac instance and initialize with the signing key
            Mac mac = Mac.getInstance("HmacSHA256");
            mac.init(signingKey);

            // Compute the hmac on input data bytes
            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

            // Using android.util.Base64 for Android Studio instead of
            // Apache commons codec
            String signature = URLEncoder.encode(
                    Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");

            // Construct authorization string
            token = "SharedAccessSignature sr=" + targetUri + "&sig="
                    + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
        } catch (Exception e) {
            if (isVisible) {
                ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
            }
        }

        return token;
    }
    ```

8. Fügen Sie der `MainActivity`-Klasse in `MainActivity.java` die folgende Methode hinzu, um den Klick auf die Schaltfläche **Benachrichtigung senden** zu verarbeiten und die Nachricht mit der Pushbenachrichtigung über die integrierte REST-API an den Hub zu senden.

    ```java
    /**
        * Send Notification button click handler. This method parses the
        * DefaultFullSharedAccess connection string and generates a SaS token. The
        * token is added to the Authorization header on the POST request to the
        * notification hub. The text in the editTextNotificationMessage control
        * is added as the JSON body for the request to add a GCM message to the hub.
        *
        * @param v
        */
    public void sendNotificationButtonOnClick(View v) {
        EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
        final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";

        new Thread()
        {
            public void run()
            {
                try
                {
                    // Based on reference documentation...
                    // https://msdn.microsoft.com/library/azure/dn223273.aspx
                    ParseConnectionString(NotificationSettings.HubFullAccess);
                    URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                            "/messages/?api-version=2015-01");

                    HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();

                    try {
                        // POST request
                        urlConnection.setDoOutput(true);

                        // Authenticate the POST request with the SaS token
                        urlConnection.setRequestProperty("Authorization",
                            generateSasToken(url.toString()));

                        // Notification format should be GCM
                        urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");

                        // Include any tags
                        // Example below targets 3 specific tags
                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                        // urlConnection.setRequestProperty("ServiceBusNotification-Tags",
                        //        "tag1 || tag2 || tag3");

                        // Send notification message
                        urlConnection.setFixedLengthStreamingMode(json.length());
                        OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                        bodyStream.write(json.getBytes());
                        bodyStream.close();

                        // Get response
                        urlConnection.connect();
                        int responseCode = urlConnection.getResponseCode();
                        if ((responseCode != 200) && (responseCode != 201)) {
                            BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                            String line;
                            StringBuilder builder = new StringBuilder("Send Notification returned " +
                                    responseCode + " : ")  ;
                            while ((line = br.readLine()) != null) {
                                builder.append(line);
                            }

                            ToastNotify(builder.toString());
                        }
                    } finally {
                        urlConnection.disconnect();
                    }
                }
                catch(Exception e)
                {
                    if (isVisible) {
                        ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                    }
                }
            }
        }.start();
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Broadcastbenachrichtigungen an alle Android-Geräte gesendet, die beim Back-End registriert sind. Um zu erfahren, wie Sie Pushbenachrichtigungen an bestimmte Android-Geräte senden, fahren Sie mit dem folgenden Tutorial fort:  

 > [!div class="nextstepaction"]
 > [Senden von Pushbenachrichtigungen an bestimmte Geräte](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png
[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png
[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png
[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure portal]: https://portal.azure.com
