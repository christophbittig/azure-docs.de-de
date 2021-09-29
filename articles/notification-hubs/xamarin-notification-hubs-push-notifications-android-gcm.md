---
title: Senden von Pushbenachrichtigungen an Xamarin.Android-Apps mit Azure Notification Hubs | Microsoft-Dokumentation
description: In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Xamarin.Android-App senden.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 08/27/2021
ms.author: matthewp
ms.reviewer: jowargo
ms.lastreviewed: 08/01/2019
ms.openlocfilehash: 9fcb5ef3e1759188f8ce18e645de8344d37c0e06
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638746"
---
# <a name="tutorial-send-push-notifications-to-xamarinandroid-apps-using-notification-hubs"></a>Tutorial: Senden von Pushbenachrichtigungen an Xamarin.Android-Apps mit Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Übersicht

In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Xamarin.Android-App senden. Sie erstellen eine leere Xamarin.Android-App, die Pushbenachrichtigungen mithilfe von Firebase Cloud Messaging (FCM) empfängt. Sie können über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen. Der fertige Code steht in der [NotificationHubs-Beispiel-App](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid) zur Verfügung.

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen eines Firebase-Projekts und Aktivieren von Firebase Cloud Messaging
> * Erstellen eines Notification Hubs
> * Erstellen einer Xamarin.Android-App und Herstellen einer Verbindung mit dem Notification Hub
> * Senden von Testbenachrichtigungen im Azure-Portal

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement**. Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
* [Visual Studio mit Xamarin] unter Windows oder [Visual Studio für Mac] unter OS X.
* Ein aktives Google-Konto

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Erstellen eines Firebase-Projekts und Aktivieren von Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>Erstellen eines Notification Hubs

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcmfcm-settings-for-the-notification-hub"></a>Konfigurieren von GCM/FCM-Einstellungen für den Notification Hub

1. Wählen Sie im linken Menü im Abschnitt **Settings** (Einstellungen) die Option **Google (GCM/FCM)** aus.
2. Geben Sie den **Serverschlüssel** ein, den Sie in der Google Firebase-Konsole notiert haben.
3. Wählen Sie auf der Symbolleiste **Speichern** aus.

    ![Screenshot von Notification Hub im Azure-Portal mit hervorgehobener und rot markierter Option „Google GCM/FCM“.](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Der Notification Hub ist für die Arbeit mit FCM konfiguriert, und Sie besitzen die Verbindungszeichenfolgen, um die App für den Empfang von Benachrichtigungen zu registrieren und Pushbenachrichtigungen zu versenden.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Erstellen einer Xamarin.Android-App und Herstellen einer Verbindung mit dem Notification Hub

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Erstellen eines Visual Studio-Projekts und Hinzufügen von NuGet-Paketen

> [!NOTE]
> Die in diesem Tutorial dokumentierten Schritte gelten für Visual Studio 2017. 

1. Öffnen Sie in Visual Studio das Menü **Datei**, und wählen Sie die Optionen **Neu** und **Projekt**. Gehen Sie im Fenster **Neues Projekt** wie folgt vor:
    1. Erweitern Sie **Installiert** > **Visual C#** , und klicken Sie auf **Android**.
    2. Wählen Sie in der Liste die Option **Android-App (Xamarin)** aus.
    3. Geben Sie einen **Namen** für das Projekt ein.
    4. Wählen Sie einen **Speicherort** für das Projekt aus.
    5. Klicken Sie auf **OK**.

        ![Dialogfeld "Neues Projekt"](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. Wählen Sie im Dialogfeld **Neue Android-App** die Option **Leere App** und anschließend **OK** aus.

    ![Screenshot: Hervorgehobene Vorlage „Leere App“](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. Erweitern Sie im **Projektmappen-Explorer**-Fenster **Eigenschaften**, und klicken Sie auf **AndroidManifest.xml**. Aktualisieren Sie den Paketnamen entsprechend dem Paketnamen, den Sie eingegeben haben, als Sie Ihrem Projekt in der Google Firebase-Konsole Firebase Cloud Messaging hinzugefügt haben.

    ![Paketname in GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. Führen Sie die folgenden Schritte aus, um die Android-Zielversion für das Projekt auf **Android 10.0** festzulegen: 
    1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Eigenschaften** aus. 
    1. Wählen Sie für das Feld **Compile using Android version: (Target framework)** (Mit Android-Version kompilieren: Zielframework)) die Option **Android 10.0** aus. 
    1. Wählen Sie im Meldungsfeld **Ja** aus, um die Änderung des Zielframeworks fortzusetzen.
1. Fügen Sie mit den folgenden Schritten dem Projekt die erforderlichen NuGet-Pakete hinzu:
    1. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten...** aus.
    1. Wechseln Sie zur Registerkarte **Installiert**, und wählen Sie **Xamarin.Android.Support.Design** und dann im rechten Bereich **Aktualisieren** aus, um das Paket auf die aktuelle Version zu aktualisieren.
    1. Wechseln Sie zur Registerkarte **Durchsuchen**. Suchen Sie nach **Xamarin.GooglePlayServices.Base**. Wählen Sie **Xamarin.GooglePlayServices.Base** in der Ergebnisliste aus. Wählen Sie dann **Installieren** aus.

        ![Google Play Services NuGet](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. Suchen Sie im Fenster **NuGet-Paket-Manager** nach **Xamarin.Firebase.Messaging**. Wählen Sie **Xamarin.Firebase.Messaging** in der Ergebnisliste aus. Wählen Sie dann **Installieren** aus.
    7. Suchen Sie jetzt nach **Xamarin.Azure.NotificationHubs.Android**. Wählen Sie **Xamarin.Azure.NotificationHubs.Android** in der Ergebnisliste aus. Wählen Sie dann **Installieren** aus.

### <a name="add-the-google-services-json-file"></a>Hinzufügen der JSON-Datei von Google Services

1. Kopieren Sie die Datei `google-services.json`, die Sie aus der Google Firebase-Konsole in den Projektordner heruntergeladen haben.
2. Fügen Sie `google-services.json` zum Projekt hinzu.
3. Wählen Sie im **Projektmappen-Explorer** die Datei `google-services.json` aus.
4. Legen Sie im Bereich **Eigenschaften** die Buildaktion auf **GoogleServicesJson** fest. Wenn **GoogleServicesJson** nicht angezeigt wird, schließen Sie Visual Studio, starten Sie es neu, öffnen Sie das Projekt erneut, und versuchen Sie es erneut.

    ![Buildaktion GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Einrichten von Notification Hubs in Ihrem Projekt

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrieren bei Firebase Cloud Messaging

1. Bei der Migration von Google Cloud Messaging zu Firebase enthält die Datei `AndroidManifest.xml` Ihres Projekts unter Umständen eine veraltete GCM-Konfiguration. Dies kann zu doppelten Benachrichtigungen führen. Bearbeiten Sie die Datei, und entfernen Sie die folgenden Zeilen im Abschnitt `<application>` (sofern vorhanden):

    ```xml
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
        android:exported="false" />
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
        android:exported="true"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

2. Fügen Sie die folgenden Anweisungen **vor dem Anwendungselement** hinzu.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```

3. Erfassen Sie für Ihre Android-App und den Notification Hub folgende Informationen:

   * **Verbindungszeichenfolge für Überwachung:** Wählen Sie im [Azure portal] auf dem Dashboard die Option **Verbindungszeichenfolgen anzeigen**. Kopieren Sie die Verbindungszeichenfolge `DefaultListenSharedAccessSignature` für diesen Wert.
   * **Hub-Name:** Der Name des Hubs aus dem [Azure portal]. Beispiel: *mynotificationhub2*.
4. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das **Projekt**, und wählen Sie **Hinzufügen** und anschließend **Klasse** aus.
5. Erstellen Sie eine `Constants.cs`-Klasse für Ihr Xamarin-Projekt, und definieren Sie die folgenden Konstantenwerte in der Klasse. Ersetzen Sie die Platzhalter durch Ihre Werte.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. Fügen Sie `MainActivity.cs` die folgenden using-Anweisungen hinzu:

    ```csharp
    using WindowsAzure.Messaging.NotificationHubs;
    ```

7. Fügen Sie der MainActivity-Klasse die folgenden Eigenschaften hinzu:

    ```csharp
    internal static readonly string CHANNEL_ID = "my_notification_channel";

8. In `MainActivity.cs`, add the following code to `OnCreate` after `base.OnCreate(savedInstanceState)`:

    ```csharp
    // Listen for push notifications
    NotificationHub.SetListener(new AzureListener());

    // Start the SDK
    NotificationHub.Start(this.Application, HubName, ConnectionString);
    ```

9. Fügen Sie dem Projekt eine Klasse namens `AzureListener` hinzu.
10. Fügen Sie `AzureListener.cs` die folgenden using-Anweisungen hinzu:

    ```csharp
    using Android.Content;
    using WindowsAzure.Messaging.NotificationHubs;
    ```

11. Fügen Sie oberhalb Ihrer Klassendeklaration Folgendes hinzu, und legen Sie fest, dass Ihre Klasse von `Java.Lang.Object` erbt und `INotificationListener` implementiert:

    ```csharp
    public class AzureListener : Java.Lang.Object, INotificationListener
    ```

12. Fügen Sie den folgenden Code in der `AzureListener`-Klasse hinzu, um die empfangenen Nachrichten zu verarbeiten.

    ```csharp
        public void OnPushNotificationReceived(Context context, INotificationMessage message)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle(message.Title)
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(message.Body)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

1. **Erstellen** Sie Ihr Projekt.
1. **Ausführen** Ihrer App auf Ihrem Gerät oder im geladenen Emulator

## <a name="send-test-notification-from-the-azure-portal"></a>Senden einer Testbenachrichtigung im Azure-Portal

Mit der Option **Testsendevorgang** im [Azure portal] können Sie den Empfang von Benachrichtigungen in Ihrer App testen. Diese Option sendet zu Testzwecken eine Pushbenachrichtigung an Ihr Gerät.

![Azure-Portal – Testsendung](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Pushbenachrichtigungen werden normalerweise in einem Back-End-Dienst, z.B. Mobile Services oder ASP.NET, über eine kompatible Bibliothek gesendet. Falls für Ihr Back-End keine Bibliothek verfügbar ist, können Sie Benachrichtigungen auch direkt über die REST-API senden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Broadcastbenachrichtigungen an alle Android-Geräte gesendet, die beim Back-End registriert sind. Um zu erfahren, wie Sie Pushbenachrichtigungen an bestimmte Android-Geräte senden, fahren Sie mit dem folgenden Tutorial fort:

> [!div class="nextstepaction"]
>[Senden von Pushbenachrichtigungen an bestimmte Geräte](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio mit Xamarin]: /visualstudio/install/install-visual-studio
[Visual Studio für Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Azure portal]: https://portal.azure.com/
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Android]: /previous-versions/azure/dn282661(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android
