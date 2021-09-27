---
title: Azure Communication Services – Bewährte Methoden
description: Hier erfahren Sie mehr bewährte Methoden für Azure Communication Services.
author: srahaman
manager: akania
services: azure-communication-services
ms.author: srahaman
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 7b0ac0fdb6ee5b734d642612c1fea16665e07684
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435508"
---
# <a name="best-practices-azure-communication-services-calling-sdks"></a>Bewährte Methoden: Azure Communication Services Calling SDKs
Dieser Artikel enthält Informationen zu bewährten Methoden im Zusammenhang mit Azure Communication Services (ACS) Calling SDKs.

## <a name="acs-web-javascript-sdk-best-practices"></a>Bewährte Methoden für das ACS-Web-JavaScript SDK
Dieser Abschnitt enthält Informationen zu bewährten Methoden im Zusammenhang mit dem Azure Communication Services JavaScript SDK für Sprach-und Videoanrufe.

## <a name="javascript-voice-and-video-calling-sdk"></a>JavaScript-SDK für Sprach- und Videoanrufe

### <a name="plug-in-microphone-or-enable-microphone-from-device-manager-when-acs-call-in-progress"></a>Anschließen oder Aktivieren des Mikrofons über den Geräte-Manager, wenn der ACS-Anruf läuft
Wenn zu Beginn eines Anrufs kein Mikrofon verfügbar ist, und dann ein Mikrofon verfügbar wird, wird das Anrufdiagnoseereignis „noMicrophoneDevicesEnumerated“ ausgelöst.
In diesem Fall sollte Ihre Anwendung `askDevicePermission` aufrufen, um die Zustimmung des Benutzers zum Aufzählen von Geräten zu erhalten. Anschließend kann der Benutzer das Mikrofon stummschalten/die Stimmschaltung aufheben.

### <a name="stop-video-on-page-hide"></a>Video beim Ausblenden der Seite beenden
Wenn der Benutzer von der Anrufregisterkarte weg navigiert, wird das Videostreaming beendet. Einige Geräte streamen weiterhin den letzten Frame. Um dieses Problem zu vermeiden, wird Entwicklern empfohlen, das Videostreaming zu beenden, wenn Benutzer von einem aktiven Videoanruf weg navigieren. Das Video kann durch Aufrufen der `call.stopVideo`-API beendet werden.
```JavaScript
document.addEventListener("visibilitychange", function() {
    if (document.visibilityState === 'visible') {
        // Start Video if it was stopped on visibility change (flag true)
    } else {
        // Stop Video if it's on and set flag = true to keep track
    }
});
```

### <a name="dispose-video-stream-renderer-view"></a>Löschen der Rendereransicht des Videostreams
Communication Services-Anwendungen müssen `VideoStreamRendererView` oder die übergeordnete `VideoStreamRenderer`-Instanz löschen, wenn sie nicht mehr benötigt werden.

### <a name="hang-up-the-call-on-onbeforeunload-event"></a>Beenden des Aufrufs beim Ereignis onbeforeunload
Ihre Anwendung sollte `call.hangup` aufrufen, wenn das Ereignis `onbeforeunload` ausgegeben wird.

### <a name="handling-multiple-calls-on-multiple-tabs-on-mobile"></a>Umgang mit mehreren Anrufen auf mehreren Registerkarten auf Mobilgeräten
Ihre Anwendung sollte eine Verbindung mit Anrufen von mehreren Browserregisterkarten nicht gleichzeitig herstellen, da dies aufgrund der Ressourcenzuordnung für Mikrofon und Kamera auf dem Gerät zu undefiniertem Verhalten führen kann. Entwicklern wird empfohlen, Anrufe grundsätzlich zu beenden, wenn sie im Hintergrund durchgeführt werden, bevor sie einen neuen Anruf starten.
```JavaScript 
document.addEventListener("visibilitychange", function() {
    if (document.visibilityState != 'visible') {
            // call.hangUp
    }
});
 ```

### <a name="hang-up-the-call-on-microphonemuteunexpectedly-ufd"></a>Beenden des Anrufs für UFD microphoneMuteUnexpectedly
Wenn ein iOS-/Safari-Benutzer einen PSTN-Anruf empfängt, verliert Azure Communication Services den Mikrofonzugriff. Azure Communication Services gibt das Anrufdiagnoseereignis `microphoneMuteUnexpectedly` aus, und an diesem Punkt kann Communication Services den Zugriff auf das Mikrofon nicht wiedererlangen.
Es wird empfohlen, den Aufruf in dieser Situation zu beenden ( `call.hangUp` ).

### <a name="device-management"></a>Geräteverwaltung
Sie können das Azure Communication Services SDK verwenden, um Ihre Geräte und Medienvorgänge zu verwalten.
- Ihre Anwendung sollte keine nativen Browser-APIs wie `getUserMedia` oder `getDisplayMedia` verwenden, um Streams außerhalb des SDK zu erhalten. Wenn Sie dies tun, müssen Sie Ihre Medienstreams manuell löschen, bevor Sie `DeviceManager` oder andere Geräteverwaltungs-APIs über das Communication Services SDK verwenden.

### <a name="request-device-permissions"></a>Anfordern von Geräteberechtigungen
Sie können Geräteberechtigungen mithilfe des SDK anfordern:
- Ihre Anwendung sollte `DeviceManager.askDevicePermission` zum Anfordern des Zugriffs auf Audio- und/oder Videogeräte verwenden.
- Wenn der Benutzer den Zugriff verweigert, gibt `DeviceManager.askDevicePermission` bei nachfolgenden Anrufen „false“ für einen bestimmten Gerätetyp (Audio oder Video) zurück, auch nachdem die Seite aktualisiert wurde. In diesem Szenario muss Ihre Anwendung erkennen, dass der Benutzer den Zugriff zuvor verweigert hat, und den Benutzer anweisen, den Zugriff auf einen bestimmten Gerätetyp manuell zurückzusetzen oder explizit zu gewähren.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den folgenden Artikeln:

- [Hinzufügen von Chatfunktionen zu Ihrer App](../quickstarts/chat/get-started.md)
- [Hinzufügen von Sprachanrufen zu Ihrer App](../quickstarts/voice-video-calling/getting-started-with-calling.md)
- [Referenzdokumentation](reference.md)
