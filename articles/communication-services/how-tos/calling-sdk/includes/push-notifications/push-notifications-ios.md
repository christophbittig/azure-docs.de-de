---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: c1d03c13ac4d2f4911baf0315358003dd43e0b3b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838212"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

## <a name="set-up-push-notifications"></a>Einrichten von Pushbenachrichtigungen

Eine mobile Pushbenachrichtigung ist die Popupbenachrichtigung, die Sie auf dem mobilen Gerät erhalten. Bei Anrufen konzentrieren wir uns auf VoIP-Pushbenachrichtigungen (Voice over Internet Protocol). 

In den folgenden Abschnitten erfahren Sie, wie Sie sich für Pushbenachrichtigungen registrieren, sie behandeln und die Registrierung für sie aufheben. Bevor Sie mit diesen Aufgaben beginnen, sind jedoch folgende Schritte erforderlich:

1. Navigieren Sie in Xcode zur Registerkarte **Signing & Capabilities** (Signieren und Funktionen). Fügen Sie eine Funktion hinzu, indem Sie **+ Capability** (+ Funktion) und anschließend **Push Notifications** (Pushbenachrichtigungen) auswählen.
2. Fügen Sie eine weitere Funktion hinzu, indem Sie **+ Capability** (+ Funktion) und anschließend **Background Modes** (Hintergrundmodi) auswählen.
3. Aktivieren Sie unter **Background Modes** (Hintergrundmodi) die Kontrollkästchen **Voice over IP** und **Remote notifications** (Remotebenachrichtigungen) aus.

:::image type="content" source="../../../../quickstarts/voice-video-calling/media/ios/xcode-push-notification.png" alt-text="Screenshot: Hinzufügen von Funktionen in Xcode" lightbox="../../../../quickstarts/voice-video-calling/media/ios/xcode-push-notification.png":::

### <a name="register-for-push-notifications"></a>Registrieren für Pushbenachrichtigungen

Rufen Sie zum Registrieren für Pushbenachrichtigungen `registerPushNotification()` für eine `CallAgent`-Instanz mit einem Geräteregistrierungstoken auf.

Die Registrierung für Pushbenachrichtigungen muss nach erfolgreicher Initialisierung erfolgen. Wenn das Objekt `callAgent` zerstört wurde, wird `logout` aufgerufen, wodurch die Registrierung von Pushbenachrichtigungen automatisch aufgehoben wird.

```swift
let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken!) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}
```

## <a name="handle-push-notifications"></a>Behandeln von Pushbenachrichtigungen
Rufen Sie `handlePushNotification()` für eine `CallAgent`-Instanz mit Wörterbuchnutzdaten auf, um Pushbenachrichtigungen für eingehende Anrufe zu erhalten.

```swift
let callNotification = PushNotificationInfo.fromDictionary(pushPayload.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error == nil) {
        print("Handling of push notification was successful")
    } else {
        print("Handling of push notification failed")
    }
}
```
## <a name="unregister-push-notifications"></a>Aufheben der Registrierung der Pushbenachrichtigungen

Anwendungen können die Registrierung der Pushbenachrichtigung jederzeit aufheben. Rufen Sie einfach die `unregisterPushNotification`-Methode für `CallAgent` auf.

> [!NOTE]
> Die Registrierung von Anwendungen für Pushbenachrichtigungen wird bei der Abmeldung nicht automatisch aufgehoben.

```swift
callAgent.unregisterPushNotification { (error) in
    if (error == nil) {
        print("Unregister of push notification was successful")
    } else {
       print("Unregister of push notification failed, please try again")
    }
}
```