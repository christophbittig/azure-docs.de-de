---
title: Senden plattformübergreifender Benachrichtigungen an Benutzer mit Azure Notification Hubs (ASP.NET)
description: Erfahren Sie, wie Sie mithilfe von Notification Hubs-Vorlagen in einer einzigen Anforderung eine plattformunabhängige Benachrichtigung senden können, die auf alle Plattformen abzielt.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: thsomasu
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 08/23/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 10/02/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 01d6d1cffaa0ae9928c24015570f882036b1e783
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771436"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Senden plattformübergreifender Benachrichtigungen mit Azure Notification Hubs

Dieses Tutorial baut auf dem vorherigen Tutorial [Senden von Benachrichtigungen an bestimmte Benutzer mit Azure Notification Hubs] auf. Das vorherige Tutorial beschreibt, wie Sie Pushbenachrichtigungen an alle Geräte eines bestimmten authentifizierten Benutzers senden können. Der dort verwendete Ansatz erfordert mehrere Anforderungen zum Senden einer Benachrichtigung an jede unterstützte Clientplattform. Azure Notification Hubs unterstützt Vorlagen, mit denen Sie angeben können, wie ein bestimmtes Gerät Benachrichtigungen empfangen soll. Auf diese Weise können Sie den Versand plattformübergreifender Benachrichtigungen vereinfachen.

Dieser Artikel zeigt, wie Sie die Vorteile von Vorlagen nutzen können, um eine Benachrichtigung zu senden, die alle Plattformen berücksichtigt. In diesem Artikel wird eine einzelne Anforderung zum Senden einer plattformneutralen Benachrichtigung verwendet. Ausführlichere Informationen zu Vorlagen finden Sie unter [Überblick über Notification Hubs][Templates].

> [!IMPORTANT]
> Windows Phone-Projekte der Version 8.1 und früher werden in Visual Studio 2019 nicht unterstützt. Weitere Informationen finden Sie unter [Visual Studio 2019 – Zielplattformen und Kompatibilität](/visualstudio/releases/2019/compatibility).

> [!NOTE]
> Mit Notification Hubs kann ein Gerät mehrere Vorlagen mit demselben Tag registrieren. In diesem Fall werden bei einer eingehenden Nachricht für das entsprechende Tag mehrere Benachrichtigungen an das Gerät übermittelt (eine pro Vorlage). Auf diese Weise können Sie dieselbe Nachricht in mehreren visuellen Darstellungen anzeigen, z.B. als Signal und als Popupbenachrichtigung in einer Windows Store-App.

## <a name="send-cross-platform-notifications-using-templates"></a>Senden von plattformübergreifenden Benachrichtigungen mit Vorlagen

> [!NOTE]
> Der Microsoft-Pushbenachrichtigungsdienst (MPNS) ist veraltet und wird nicht mehr unterstützt.

In diesem Abschnitt wird der Beispielcode verwendet, den Sie im Tutorial [Senden von Benachrichtigungen an bestimmte Benutzer mit Azure Notification Hubs] erstellt haben. Sie können das [vollständige Beispiel von GitHub herunterladen](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

Um plattformübergreifende Benachrichtigungen mit Vorlagen zu senden, gehen Sie wie folgt vor:

1. Erweitern Sie im **Projektmappen-Explorer** in Visual Studio den Ordner **Controllers**, und öffnen Sie dann die Datei *RegisterController.cs*.

1. Suchen Sie in der `Put`-Methode nach dem Codeblock, mit dem eine neue Registrierung erstellt wird, und ersetzen Sie den Inhalt von `switch` durch den folgenden Code:

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    Dieser Code ruft die plattformspezifische Methode zur Erstellung einer Vorlagenregistrierung anstelle der Systemregistrierung auf. Da Vorlagenregistrierungen von nativen Registrierungen abgeleitet sind, müssen Sie vorhandene Registrierungen nicht ändern.

1. Öffnen Sie im **Projektmappen-Explorer** im Ordner **Controllers** die Datei **NotificationsController.cs**. Ersetzen Sie die `Post`-Methode durch den folgenden Code:

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    Dieser Code sendet an alle Plattformen gleichzeitig eine Benachrichtigung. Sie geben keine native Nutzlast an. Über Notification Hubs wird die richtige Nutzlast erstellt und an alle Geräte mit dem angegebenen Tagwert übermittelt, wie in den registrierten Vorlagen festgelegt.

1. Veröffentlichen Sie das Web-API-Projekt erneut.

1. Führen Sie die Client-App erneut aus, und vergewissern Sie sich, dass die Registrierung erfolgreich war.

1. Stellen Sie die Client-App optional auf einem zweiten Gerät bereit, und führen Sie die App aus. Auf jedem Gerät wird eine Benachrichtigung angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Nach Abschluss dieses Tutorials finden Sie weitere Informationen zu Notification Hubs und Vorlagen in den folgenden Artikeln:

* Ein anderes Szenario für die Verwendung von Vorlagen finden Sie im Tutorial [Senden von Pushbenachrichtigungen an bestimmte Windows-Geräte, die UWP-Anwendungen (Universelle Windows-Plattform) ausführen][Use Notification Hubs to send breaking news].
* Ausführlichere Informationen zu Vorlagen finden Sie unter [Überblick über Notification Hubs][Templates].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Senden von Benachrichtigungen an bestimmte Benutzer mit Azure Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hub How to for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)