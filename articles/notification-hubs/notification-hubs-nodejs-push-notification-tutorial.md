---
title: Senden von Pushbenachrichtigungen mit Azure Notification Hubs und Node.js
description: Erfahren Sie, wie Sie Notification Hubs verwenden, um Pushbenachrichtigungen aus einer Node.js-Anwendung zu senden.
keywords: Pushbenachrichtigung, Pushbenachrichtigungen, Node.js-Push, iOS-Push
services: notification-hubs
documentationcenter: nodejs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 08/23/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-js
ms.openlocfilehash: 44e6156d472200c818520fbba7e0618100f4344b
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769152"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Senden von Pushbenachrichtigungen mit Azure Notification Hubs und Node.js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Übersicht

> [!IMPORTANT]
> Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie kein Konto haben, können Sie mit der [kostenlosen Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs) in nur wenigen Minuten ein kostenloses Testkonto erstellen.

In diesem Leitfaden erfahren Sie, wie Sie Pushbenachrichtigungen mithilfe von Azure Notification Hubs direkt aus einer [Node.js](https://nodejs.org)-Anwendung senden.

Die behandelten Szenarios umfassen das Senden von Pushbenachrichtigungen an Anwendungen auf den folgenden Plattformen:

- Android
- iOS
- Universelle Windows-Plattform
- Windows Phone

## <a name="notification-hubs"></a>Notification Hubs

Azure Notification Hubs bieten eine einfache, plattformübergreifende und skalierbare Infrastruktur für den Versand von Pushbenachrichtigungen an mobile Geräte. Ausführliche Informationen zur Dienstinfrastruktur finden Sie auf der Seite [Übersicht über Benachrichtigungshubs](/previous-versions/azure/azure-services/jj927170(v=azure.100)) .

## <a name="create-a-nodejs-application"></a>Erstellen einer Node.js-Anwendung

Der erste Schritt in diesem Tutorial besteht im Erstellen einer neuen leeren Node.js-Anwendung. Anweisungen zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website][nodejswebsite], [Node.js-Clouddienst][Node.js Cloud Service] mithilfe von Windows PowerShell oder [Website mit WebMatrix][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Konfigurieren der Anwendung für Notification Hubs

Um Azure Notification Hubs verwenden zu können, müssen Sie das Node.js-Paket [azure](https://www.npmjs.com/package/azure) herunterladen und verwenden. Dieses Paket enthält eine Reihe von Bibliotheken, die mit den REST-Pushbenachrichtigungsdiensten kommunizieren.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Verwenden von Node Package Manager (NPM) zum Beziehen des Pakets

1. Verwenden Sie eine Befehlszeilenschnittstelle, z.B. **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Linux), und navigieren Sie zu dem Ordner, in dem Sie die leere Anwendung erstellt haben.
2. Führen Sie `npm install azure-sb` im Befehlsfenster aus.
3. Sie können den Befehl `ls` oder `dir` manuell ausführen, um zu überprüfen, ob der Ordner `node_modules` erstellt wurde.
4. In diesem Ordner finden Sie das **azure**-Paket, das die für den Zugriff auf den Notification Hub benötigten Bibliotheken enthält.

> [!NOTE]
> Weitere Informationen zur Installation von NPM finden Sie im offiziellen [NPM-Blog](https://blog.npmjs.org/post/85484771375/how-to-install-npm).

### <a name="import-the-module"></a>Importieren des Moduls
Verwenden Sie einen Texteditor, um den folgenden Code am Anfang der Datei `server.js` der Anwendung hinzuzufügen:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Einrichten einer Azure Notification Hub-Verbindung

Das `NotificationHubService`-Objekt ermöglicht Ihnen das Arbeiten mit Notification Hubs. Der folgende Code erstellt ein `NotificationHubService`-Objekt für einen Notification Hub mit dem Namen `hubname`. Fügen Sie ihn am Anfang der Datei `server.js` hinzu, nach der Anweisung zum Importieren des Azure-Moduls:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Führen Sie die folgenden Schritte aus, um den Wert für `connectionstring` aus dem [Azure-Portal] abzurufen:

1. Klicken Sie im linken Navigationsbereich auf **Durchsuchen**.
2. Wählen Sie **Notification Hubs**, und wählen Sie den gewünschten Hub für das Beispiel aus. Hilfe zum Erstellen eines neuen Notification Hubs finden Sie im [Tutorial für die ersten Schritte mit dem Windows Store](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
3. Wählen Sie **Settings** aus.
4. Klicken Sie auf **Zugriffsrichtlinien**. Beide Verbindungszeichenfolgen für Freigabe und Vollzugriff werden angezeigt.

![Azure-Portal – Notification Hubs](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Sie können die Verbindungszeichenfolge auch mit dem Cmdlet **Get-AzureSbNamespace** in [Azure PowerShell](/powershell/azure/) oder dem Befehl **azure sb namespace show** in der [Azure-Befehlszeilenschnittstelle (Azure CLI)](/cli/azure/install-classic-cli) abrufen.

## <a name="general-architecture"></a>Allgemeine Architektur

Das `NotificationHubService`-Objekt enthält die folgenden Objektinstanzen für den Versand von Pushbenachrichtigungen an bestimmte Geräte und Anwendungen:

- **Android**: Verwenden Sie das `GcmService`-Objekt unter `notificationHubService.gcm`.
- **iOS**: Verwenden Sie das `ApnsService`-Objekt unter `notificationHubService.apns`.
- **Windows Phone**: Verwenden Sie das `MpnsService`-Objekt unter `notificationHubService.mpns`.
- **Universelle Windows-Plattform**: Verwenden Sie das `WnsService`-Objekt unter `notificationHubService.wns`.

> [!NOTE]
> Der Microsoft-Pushbenachrichtigungsdienst (MPNS) ist veraltet und wird nicht mehr unterstützt.

### <a name="how-to-send-push-notifications-to-android-applications"></a>Gewusst wie: Senden von Pushbenachrichtigungen an Android-Anwendungen

Das `GcmService`-Objekt enthält eine `send`-Methode, mit der Sie Pushbenachrichtigungen an Android-Apps senden können. Die `send`-Methode nimmt die folgenden Parameter entgegen:

- **Tags:** die Tag-ID. Falls kein Tag angegeben ist, wird die Benachrichtigung an alle Clients gesendet.
- **Payload:** die Nutzlast der Nachricht als JSON oder unformatierte Zeichenfolge.
- **Callback:** die Rückruffunktion.

Weitere Informationen zum Nutzlastformat finden Sie in der [Nutzlastdokumentation](https://payload.readthedocs.io/en/latest/).

Der folgende Code verwendet die `GcmService`-Instanz, die vom `NotificationHubService` verfügbar gemacht wurde, um eine Pushbenachrichtigung an alle registrierten Clients zu senden.

```javascript
var payload = {
  data: {
    message: 'Hello!'
  }
};
notificationHubService.gcm.send(null, payload, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Gewusst wie: Senden von Pushbenachrichtigungen an iOS-Apps

Wie auch bei den oben beschriebenen Android-Apps enthält das `ApnsService`-Objekt eine `send`-Methode, mit der Pushbenachrichtigungen an iOS-Apps gesendet werden können. Die `send`-Methode nimmt die folgenden Parameter entgegen:

- **Tags:** die Tag-ID. Falls kein Tag angegeben ist, wird die Benachrichtigung an alle Clients gesendet.
- **Payload:** die Nutzlast der Nachricht als JSON oder Zeichenfolge.
- **Callback:** die Rückruffunktion.

Weitere Informationen zum Nutzlastformat finden Sie im Abschnitt **Benachrichtigungsinhalt** des [UserNotifications-Leitfadens](https://developer.apple.com/documentation/usernotifications).

Der folgende Code verwendet die `ApnsService`-Instanz, die vom `NotificationHubService` verfügbar gemacht wurde, um eine Warnmeldung an alle Clients zu schicken:

```javascript
var payload={
    alert: 'Hello!'
  };
notificationHubService.apns.send(null, payload, function(error){
  if(!error){
      // notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Gewusst wie: Senden von Pushbenachrichtigungen an Windows Phone-Apps

Das `MpnsService`-Objekt enthält eine `send`-Methode, mit der Pushbenachrichtigungen an Windows Phone-Apps gesendet werden können. Die `send`-Methode nimmt die folgenden Parameter entgegen:

- **Tags:** die Tag-ID. Falls kein Tag angegeben ist, wird die Benachrichtigung an alle Clients gesendet.
- **Payload:** die Nutzlast der Nachricht als XML.
- **TargetName** - `toast` für Popupbenachrichtigungen. `token` für Kachelbenachrichtigungen.
- **NotificationClass:** die Priorität der Benachrichtigung. Eine Liste der gültigen Werte finden Sie im Abschnitt **HTTP Header Elements** (HTTP-Headerelemente) im Dokument [Push notifications from a server](/previous-versions/windows/xna/bb200104(v=xnagamestudio.41)) (Pushbenachrichtigungen von einem Server).
- **Options:** optionale Anforderungsheader.
- **Callback:** die Rückruffunktion.

Eine Liste der gültigen Optionen für `TargetName`, `NotificationClass` und Header finden Sie auf der Seite [Push notifications from a server](/previous-versions/windows/xna/bb200104(v=xnagamestudio.41)) (Pushbenachrichtigungen von einem Server).

Der folgende Beispielcode verwendet die `MpnsService`-Instanz, die vom `NotificationHubService` verfügbar gemacht wurde, um eine Popuppushbenachrichtigung zu senden:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Gewusst wie: Senden von Pushbenachrichtigungen an universelle Windows-Plattform-Anwendungen (UWP)

Das `WnsService`-Objekt enthält eine `send`-Methode, mit der Pushbenachrichtigungen an Apps der universellen Windows-Plattform gesendet werden können.  Die `send`-Methode nimmt die folgenden Parameter entgegen:

- **Tags:** die Tag-ID. Falls kein Tag angegeben ist, wird die Benachrichtigung an alle registrierten Clients gesendet.
- **Payload:** die Nutzlast der Nachricht als XML.
- **Type:** der Benachrichtigungstyp.
- **Options:** optionale Anforderungsheader.
- **Callback:** die Rückruffunktion.

Eine Liste der gültigen Typen und Anforderungsheader finden Sie unter [Anforderungs- und Antwortheader des Pushbenachrichtigungsdiensts (Windows-Runtime-Apps)](/previous-versions/windows/apps/hh465435(v=win.10)).

Der folgende Beispielcode verwendet die `WnsService`-Instanz, die vom `NotificationHubService` verfügbar gemacht wurde, um eine Popuppushbenachrichtigung an eine UWP-App zu senden:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Nächste Schritte

Mit den obigen Beispielcodeausschnitten können Sie problemlos eine Dienstinfrastruktur zum Übermitteln von Pushbenachrichtigungen an eine Vielzahl von Geräten erstellen. Nachdem Sie mit den Grundlagen der Verwendung von Notification Hubs mit Node.js vertraut sind, erfahren Sie unter den folgenden Links mehr darüber, wie Sie diese Funktionen erweitern können.

- Weitere Informationen finden Sie in der MSDN-Referenz zu [Azure Notification Hubs](/previous-versions/azure/azure-services/jj927170(v=azure.100)).
- Besuchen Sie das Repository zum [Azure SDK für Node] auf GitHub, das weitere Beispiele und Implementierungsdetails enthält.

[Azure SDK für Node]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter_sqlexpression
[Azure Service Bus Notification Hubs]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: ../app-service/quickstart-nodejs.md
[webmatrix]: /aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure-Portal]: https://portal.azure.com
