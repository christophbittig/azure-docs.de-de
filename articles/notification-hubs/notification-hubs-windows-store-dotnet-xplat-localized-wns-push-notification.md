---
title: Senden von lokalisierten Benachrichtigungen an Windows-Apps mit Azure Notification Hubs | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Verwendung von Azure Notification Hubs zum Senden von Benachrichtigungen zu lokalisierten aktuellen Nachrichten.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 08/23/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 9cd34c85f938a6deade8316577714d836fd021df
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769126"
---
# <a name="tutorial-send-localized-push-notifications-to-windows-apps-using-azure-notification-hubs"></a>Tutorial: Senden von lokalisierten Pushbenachrichtigungen an Windows-Apps mit Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

## <a name="overview"></a>Übersicht

> [!NOTE]
> Der Microsoft-Pushbenachrichtigungsdienst (Microsoft Push Notification Service, MPNS) ist veraltet und wird nicht mehr unterstützt.

In diesem Tutorial wird gezeigt, wie Sie lokalisierte Pushbenachrichtigungen an mobile Geräte senden, die beim Notification Hubs-Dienst registriert wurden. Im Tutorial aktualisieren Sie Anwendungen, die im Tutorial zum [Senden von Benachrichtigungen an bestimmte Geräte (Universelle Windows-Plattform)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) erstellt wurden, um die folgenden Szenarien zu unterstützen:

- In der Windows Store-App können Clientgeräte eine Sprache angeben und verschiedene Nachrichtenkategorien abonnieren.
- Die Back-End-App überträgt Benachrichtigungen mithilfe der Features für **Tags** und **Vorlagen** von Azure Notification Hubs.

Nach Abschluss des Tutorials können Sie sich für Kategorien registrieren, die Sie interessieren, und zudem eine Sprache angeben, in der Sie die Benachrichtigungen empfangen möchten. Die Back-End-Anwendung sendet Benachrichtigungen, die je nach Sprache und Gerät lokalisiert werden.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Aktualisieren der Windows-App zur Unterstützung von Gebietsschemainformationen
> * Aktualisieren einer Back-End-App zum Senden von lokalisierten Benachrichtigungen
> * Testen der App

## <a name="prerequisites"></a>Voraussetzungen

Schließen Sie das [Tutorial zum Senden von Benachrichtigungen an bestimmte Geräte (Universelle Windows-Plattform)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) ab.

Im [Tutorial zum Senden von Benachrichtigungen an bestimmte Geräte (Universelle Windows-Plattform)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) haben Sie eine App erstellt, die mithilfe von **Tags** Benachrichtigungen für verschiedene **Kategorien** von Nachrichten abonniert. In diesem Tutorial verwenden Sie das Feature für **Vorlagen** von Notification Hubs, um **lokalisierte** Benachrichtigungen für Nachrichten zu verschicken.

Mit Vorlagen können Sie auf einer hohen Ebene das Format festlegen, in dem ein bestimmtes Gerät eine Benachrichtigung empfangen soll. Die Vorlage gibt das exakte Format der Nutzlast anhand von Eigenschaften an, die Teil der von Ihrem Back-End verschickten Nachricht sind. In diesem Tutorial sendet die Back-End-Anwendung eine vom Gebietsschema unabhängige Nachricht, die alle unterstützten Sprachen enthält:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Die Geräte registrieren sich mit einer Vorlage, die auf die korrekte Eigenschaft verweist. Eine Windows Store-App, die Popupmeldungen auf Englisch empfangen möchte, registriert sich beispielsweise mit beliebigen zugehörigen Tags für die folgende Vorlage:

```xml
<toast>
    <visual>
    <binding template=\"ToastText01\">
        <text id=\"1\">$(News_English)</text>
    </binding>
    </visual>
</toast>
```

Weitere Informationen zu Vorlagen finden Sie im Artikel zu [Vorlagen](notification-hubs-templates-cross-platform-push-messages.md).

## <a name="update-windows-app-to-support-locale-information"></a>Aktualisieren der Windows-App zur Unterstützung von Gebietsschemainformationen

1. Öffnen Sie die Visual Studio-Projektmappe, die Sie für das [Tutorial zum Senden von Benachrichtigungen an bestimmte Geräte (Universelle Windows-Plattform)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) erstellt haben.
2. Fügen Sie in die Datei `MainPage.xaml` der Projektmappe ein Gebietsschema-Kombinationsfeld ein:

    ```xml
    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
3. Fügen Sie in der Klasse `Notifications` einen Gebietsschemaparameter zu den Methoden `StoreCategoriesAndSubscribe` und `SubscribeToCategories` hinzu.

    ```csharp
    public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        ApplicationData.Current.LocalSettings.Values["locale"] = locale;
        return await SubscribeToCategories(locale, categories);
    }

    public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration. This makes supporting notifications across other platforms much easier.
        // Using the localized tags based on locale selected.
        string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
    }
    ```

    Rufen Sie `RegisterTemplateAsync` anstelle der Methode `RegisterNativeAsync` auf. Sie registrieren ein bestimmtes Benachrichtigungsformat, in dem die Vorlage vom Gebietsschema abhängt. Sie geben auch einen Vorlagennamen (simpleWNSTemplateExample) an, da Sie unter Umständen mehrere Vorlagen (etwa eine für Popupbenachrichtigungen und eine für Kacheln) registrieren möchten. Sie müssen ihnen ebenfalls einen Namen geben, um sie aktualisieren oder löschen zu können.

    Wenn sich ein Gerät für mehrere Vorlagen mit demselben Tag registriert, werden bei einer eingehenden Nachricht für das entsprechende Tag mehrere Benachrichtigungen an das Gerät verschickt (eine pro Vorlage). Dies ist hilfreich, um dieselbe logische Nachricht in mehreren visuellen Darstellungen anzuzeigen, z. B. als Signal und als Popupbenachrichtigung in einer Windows Store-App.
4. Fügen Sie die folgende Methode hinzu, um die gespeicherte Sprache abzurufen:

    ```csharp
    public string RetrieveLocale()
    {
        var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
        return locale != null ? locale : "English";
    }
    ```

5. Aktualisieren Sie in der Datei `MainPage.xaml.cs` den Klickhandler für die Schaltfläche, um den aktuellen Wert des Gebietsschema-Kombinationsfelds abzurufen und an den Aufruf der Klasse `Notifications` zu übergeben:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var locale = (string)Locale.SelectedItem;

        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                categories);

        var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
            string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```
6. Aktualisieren Sie abschließend in der Datei `App.xaml.cs` die Methode `InitNotificationsAsync`, um das Gebietsschema abzurufen und es beim Abonnieren zu verwenden:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```



## <a name="run-the-uwp-application"></a>Ausführen der UWP-Anwendung

1. Führen Sie die UWP-Anwendung (Universelle Windows-Plattform) aus. Warten Sie, bis die Meldung **Registrierung war erfolgreich.** angezeigt wird.

    ![Mobile Anwendung und Registrierung](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/registration-successful.png)
2. Wählen Sie die **Kategorien** und das **Gebietsschema** aus, und klicken Sie auf **Abonnieren**. Die App konvertiert die ausgewählten Kategorien in Tags, und fordert eine neue Geräteregistrierung für die ausgewählten Tags vom Notification Hub an.

    ![Mobile Anwendung](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/mobile-app.png)
3. Eine **Bestätigungsmeldung** zu den **Abonnements** wird angezeigt.

    ![Abonnementmeldung](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/subscription-message.png)

## <a name="update-console-app-to-send-localized-notifications"></a>Aktualisieren einer Konsolen-App zum Senden von lokalisierten Benachrichtigungen

Wenn Sie Vorlagenbenachrichtigungen senden, müssen Sie nur eine Reihe von Eigenschaften angeben. In diesem Tutorial sendet die Back-End-Anwendung die Eigenschaften, die die lokalisierte Version der aktuellen Nachrichten enthalten. Beispiel:

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

In diesem Abschnitt aktualisieren Sie das Konsolenanwendungsprojekt in der Projektmappe. Ändern Sie die `SendTemplateNotificationAsync`-Methode in der zuvor erstellten Konsolen-App mit dem folgenden Code:

> [!IMPORTANT]
> Geben Sie den Namen und die Verbindungszeichenfolge mit Vollzugriff für Ihre Notification Hubs-Instanz im Code an.

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(
        "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications.
    // This includes APNS, FCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

Dieser einfache Aufruf sendet die lokalisierten Nachrichten unabhängig von der Plattform an **alle** Ihre Geräte, da Ihre Notification Hubs-Instanz die korrekte native Nutzlast erstellt und an alle Geräte sendet, die ein bestimmtes Tag abonniert haben.

## <a name="run-console-app-to-send-localized-notification"></a>Ausführen der Konsolen-App zum Senden von lokalisierten Benachrichtigungen
Führen Sie die **Konsolen-App** aus, um Benachrichtigungen für die einzelnen Kategorien und in allen unterstützten Sprachen zu senden. Stellen Sie sicher, dass Sie nur Benachrichtigungen für die abonnierten Kategorien in der ausgewählten Sprache erhalten.

![Benachrichtigungsmeldungen](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/notifications.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie lokalisierte Pushbenachrichtigungen an bestimmte Geräte senden, deren Registrierungen Tags zugeordnet sind. Um zu erfahren, wie Sie Pushbenachrichtigungen an bestimmte Benutzer senden, die möglicherweise mehrere Geräte verwenden, fahren Sie mit dem folgenden Tutorial fort:

> [!div class="nextstepaction"]
>[Senden von Pushbenachrichtigungen an bestimmte Benutzer](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)

<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)