---
title: 'Azure Communication Services: Bekannte Probleme'
description: Informationen zu Azure Communication Services
author: rinarish
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 3016fb18827c0c1323cb151024303a15a2454c5a
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177933"
---
# <a name="known-issues"></a>Bekannte Probleme
Dieser Artikel enthält Informationen zu Einschränkungen und bekannten Problemen im Zusammenhang mit Azure Communication Services Calling SDKs und Azure Communication Services-APIs für die Anrufautomatisierung.

> [!IMPORTANT]
> Es gibt mehrere Faktoren, die sich auf die Qualität von Anrufen auswirken können. Weitere Informationen zur Netzwerkkonfiguration und zu bewährten Testmethoden für Communication Services finden Sie in der **[Dokumentation zu Netzwerkanforderungen](./voice-video-calling/network-requirements.md)** .

## <a name="azure-communication-services-calling-sdks"></a>Azure Communication Services Calling SDKs

### <a name="javascript-sdk"></a>JavaScript SDK

Dieser Abschnitt enthält Informationen zu bekannten Problemen im Zusammenhang mit den Azure Communication Services JavaScript SDKs für Sprach-und Videoanrufe.

#### <a name="refreshing-a-page-doesnt-immediately-remove-the-user-from-their-call"></a>Beim Aktualisieren einer Seite wird der Benutzer nicht sofort aus seinem Anruf entfernt.

Wenn ein Benutzer während eines Anrufs beschließt, die Seite zu aktualisieren, wird dieser Benutzer durch den Communication Services-Mediendienst nicht sofort aus dem Anruf entfernt. Stattdessen wird auf den erneuten Beitritt des Benutzers gewartet. Der Benutzer wird erst nach dem Timeout des Mediendiensts aus dem Anruf entfernt.

Es empfiehlt sich, Benutzeroberflächen zu erstellen, bei denen Endbenutzer die Seite Ihrer Anwendung nicht während eines Anrufs aktualisieren müssen. Wenn ein Benutzer die Seite aktualisiert, verwenden Sie die gleiche Communication Services-Benutzer-ID, nachdem er zur Anwendung zurückgekehrt ist.

Aus Sicht der anderen Teilnehmer des Anrufs bleibt der Benutzer für ein bis zwei Minuten im Anruf.

Wenn der Benutzer mit der gleichen Communication Services-Benutzer-ID erneut beitritt, wird er in der Auflistung `remoteParticipants` als das gleiche, bereits vorhandene Objekt dargestellt.

Wenn der Benutzer vor der Aktualisierung Videodaten übertragen hat, behält die Sammlung `videoStreams` die vorherigen Streaminformationen bei, bis für den Dienst ein Timeout auftritt und die Informationen entfernt werden. In diesem Szenario kann die Anwendung ggf. prüfen, ob der Auflistung neue Streams hinzugefügt wurden, und einen Stream mit der höchsten ID (`id`) rendern. 


#### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Es ist nicht möglich, mehrere Vorschauen von mehreren Geräten im Web zu rendern.
Dies ist eine bekannte Einschränkung. Weitere Informationen finden Sie in der [Übersicht über das Calling SDK](./voice-video-calling/calling-sdk-features.md).

#### <a name="enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>In Safari können keine Geräte aufgezählt werden, wenn die Anwendung unter iOS oder iPadOS ausgeführt wird.

Anwendungen können keine Mikrofon-/Lautsprechergeräte (z. B. Bluetooth) in Safari für iOS/iPad auflisten/auswählen. Dies ist eine bekannte Betriebssystemeinschränkung.

Wenn Sie Safari unter macOS verwenden, kann Ihre App über den Geräte-Manager von Communication Services keine Lautsprecher auflisten bzw. auswählen. In diesem Szenario müssen Geräte über das Betriebssystem ausgewählt werden. Wenn Sie Chrome unter macOS verwenden, kann die App Geräte über den Geräte-Manager von Communication Services aufzählen/auswählen.

#### <a name="audio-connectivity-is-lost-when-receiving-sms-messages-or-calls-during-an-ongoing-voip-call"></a>Die Audioverbindung wird unterbrochen, wenn während eines VoIP-Anrufs SMS-Nachrichten oder Anrufe eingehen.
Dieses Problem kann verschiedene Ursachen haben:

- Bei einigen mobilen Browsern geht die Konnektivität verloren, wenn sie sich im Hintergrund befinden. Dies kann zur Beeinträchtigung des Anrufs führen, wenn der VoIP-Anruf durch ein Ereignis unterbrochen wurde, das Ihre Anwendung in den Hintergrund drängt. 
- Manchmal werden die Audiodaten von einer SMS oder von einem Festnetzanruf gebunden und nicht an den VoIP-Anruf zurückgegeben. Dieses Problem wurde von Apple in iOS-Versionen ab 14.4.1 behoben. 

<br/>Clientbibliothek: Calling (JavaScript)
<br/>Browser: Safari, Chrome
<br/>Betriebssystem: iOS, Android

#### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Ein wiederholter Wechsel von Videogeräten kann dazu führen, dass das Videostreaming vorübergehend angehalten wird.

Das Wechseln zwischen Videogeräten kann dazu führen, dass Ihr Videostream angehalten wird, während der Stream vom ausgewählten Gerät erfasst wird.

##### <a name="possible-causes"></a>Mögliche Ursachen
Ein häufiger Wechsel zwischen Geräten kann zu Leistungsbeeinträchtigungen führen. Entwicklern wird empfohlen, einen Gerätestream zu beenden, bevor sie einen anderen starten.

#### <a name="bluetooth-headset-microphone-is-not-detected-therefore-is-not-audible-during-the-call-on-safari-on-ios"></a>Das Mikrofon eines Bluetooth-Headsets wird nicht erkannt, sodass während des Anrufs in Safari unter iOS kein Audio zur Verfügung steht.
Bluetooth-Headsets werden von Safari unter iOS nicht unterstützt. Ihr Bluetooth-Gerät wird in den verfügbaren Mikrofonoptionen nicht aufgeführt, und andere Teilnehmer können Sie nicht hören, wenn Sie versuchen, Bluetooth über Safari zu verwenden.

##### <a name="possible-causes"></a>Mögliche Ursachen
Dies ist eine bekannte Betriebssystemeinschränkung für macOS/iOS/iPadOS. 

Mit Safari unter **macOS** und **iOS/iPadOS** können keine Lautsprecher über den Geräte-Manager von Communication Services aufgezählt/ausgewählt werden, da das Aufzählen/Auswählen von Lautsprechern von Safari nicht unterstützt wird. In diesem Szenario muss Ihre Geräteauswahl über das Betriebssystem aktualisiert werden.

#### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>Es kann vorkommen, dass sich die Videoqualität nach dem Drehen eines Geräts verschlechtert.
Unter Umständen verschlechtert sich die Videoqualität für Benutzer, wenn Geräte gedreht werden.

<br/>Betroffene Geräte: Google Pixel 5, Google Pixel 3a, Apple iPad 8, and Apple iPad X
<br/>Clientbibliothek: Calling (JavaScript)
<br/>Browser: Safari, Chrome
<br/>Betriebssystem: iOS, Android


#### <a name="camera-switching-makes-the-screen-freeze"></a>Wenn die Kamera umgeschaltet wird, friert der Bildschirm ein. 
Wenn ein Communication Services-Benutzer einem Anruf über das JavaScript Calling SDK beitritt und dann die Schaltfläche zum Umschalten der Kamera verwendet, reagiert die Benutzeroberfläche möglicherweise erst wieder, wenn die Anwendung aktualisiert oder der Browser durch den Benutzer in den Hintergrundmodus versetzt wird.

<br/>Betroffene Geräte: Google Pixel 4a
<br/>Clientbibliothek: Calling (JavaScript)
<br/>Browser: Chrome
<br/>Betriebssystem: iOS, Android


##### <a name="possible-causes"></a>Mögliche Ursachen
Wird untersucht

#### <a name="if-the-video-signal-was-stopped-while-the-call-is-in-connecting-state-the-video-will-not-be-sent-after-the-call-started"></a>Wenn das Videosignal während der Verbindungsherstellung des Anrufs beendet wurde, wird es nach Beginn des Anrufs nicht gesendet. 
Wenn Benutzer die Videoübertragung schnell ein-/ausschalten, während sich der Anruf im Zustand `Connecting` befindet, kann dies zu Problemen mit dem Stream für den Anruf führen. Entwickler sollten Ihre Apps so erstellen, dass die Videoübertragung nicht ein-/ausgeschaltet werden muss, während sich der Anruf im Zustand `Connecting` befindet. Dieses Problem kann die Videoleistung in folgenden Szenarien beeinträchtigen:

 - Der Benutzer beginnt den Anruf mit Audio und startet und beendet die Videoübertragung, während sich der Anruf im Zustand `Connecting` befindet.
 - Der Benutzer beginnt den Anruf mit Audio und startet und beendet die Videoübertragung, während sich der Anruf im Zustand `Lobby` befindet.

##### <a name="possible-causes"></a>Mögliche Ursachen
Wird untersucht

#### <a name="enumeratingaccessing-devices-for-safari-on-macos-and-ios"></a>Aufzählen von Geräten für Safari unter macOS und iOS bzw. Zugreifen auf solche Geräte 
Wenn Zugriff auf Geräte erteilt wird, werden die Geräteberechtigungen nach einer gewissen Zeit zurückgesetzt. Safari unter MacOS und iOS hält die Berechtigungen nicht sehr lange aufrecht, es sei denn, es wurde ein Stream erlangt. Dieses Problem lässt sich am einfachsten umgehen, indem vor dem Aufrufen der Geräteaufzählungs-APIs des Geräte-Managers („DeviceManager.getCameras()“, „DeviceManager.getSpeakers()“ und „DeviceManager.getMicrophones()“) die API „DeviceManager.askDevicePermission()“ aufgerufen wird. Sind die Berechtigungen vorhanden, wird dem Benutzer nichts angezeigt. Andernfalls erfolgt eine erneute Anforderung.

<br/>Betroffene Geräte: iPhone
<br/>Clientbibliothek: Calling (JavaScript)
<br/>Browser: Safari
<br/>Betriebssystem: iOS

####  <a name="sometimes-it-takes-a-long-time-to-render-remote-participant-videos"></a>Das Rendern der Videostreams von Remoteteilnehmern dauert manchmal sehr lange.
Während eines laufenden Gruppenanrufs sendet _Benutzer A_ Videodaten, und _Benutzer B_ tritt dem Anruf bei. Manchmal wird Benutzer B das Video von Benutzer A nicht angezeigt, oder das Video von Benutzer A wird erst nach einer langen Verzögerung gerendert. Dieses Problem kann auf eine Netzwerkumgebung zurückzuführen sein, die weiter konfiguriert werden muss. Informationen zur Netzwerkkonfiguration finden Sie in der [Dokumentation zu Netzwerkanforderungen](./voice-video-calling/network-requirements.md).

#### <a name="using-3rd-party-libraries-to-access-gum-during-the-call-may-result-in-audio-loss"></a>Die Verwendung von Drittanbieterbibliotheken für den Zugriff auf GUM während des Anrufs kann zu Audioverlusten führen.
Die separate Verwendung von „getUserMedia“ innerhalb der Anwendung führt zu einem Verlust des Audiodatenstroms, da eine Drittanbieterbibliothek den Gerätezugriff von der ACS-Bibliothek übernimmt.
Entwicklern wird empfohlen, folgende Schritte zu unternehmen:
1. Verwenden Sie während des Anrufs keine Drittanbieterbibliotheken, die intern die GetUserMedia-API verwenden.
2. Wenn Sie dennoch die Drittanbieterbibliothek verwenden müssen, besteht die einzige Möglichkeit zur Wiederherstellung darin, entweder das ausgewählte Gerät zu wechseln (wenn der Benutzer über mehrere verfügt) oder den Anruf neu zu starten.

<br/>Browser: Safari
<br/>Betriebssystem: iOS

##### <a name="possible-causes"></a>Mögliche Ursachen
In einigen Browsern (z. B. Safari) hat das Abrufen Ihres eigenen Datenstroms vom selben Gerät den Nebeneffekt, dass Racebedingungen eintreten. Das Abrufen von Datenströmen von anderen Geräten kann dazu führen, dass der Benutzer nicht über genügend USB-/E/A-Bandbreite verfügt und die „SourceUnavailableError“-Rate extrem steigt.  

#### <a name="support-for-simulcast"></a>Unterstützung für Simulcast
Simulcast ist ein Verfahren, bei dem ein Client denselben Videostream zweimal in unterschiedlichen Auflösungen und Bitraten codiert und die ACS-Infrastruktur entscheiden lässt, welchen Stream ein Client empfangen soll. Das ACS-Anrufbibliothek-SDK für Windows, Android oder iOS unterstützt das Senden von Simulcast-Streams. Das ACS-Web-SDK unterstützt derzeit nicht das Senden von Simulcast-Streams.

## <a name="azure-communication-services-call-automation-apis"></a>Azure Communication Services-APIs für die Anrufautomatisierung

Im Anschluss finden Sie Informationen zu den bekannten Problemen in den Azure Communication Services-APIs für die Anrufautomatisierung.

- Als Authentifizierung für Serveranwendungen wird derzeit nur die Verwendung einer Verbindungszeichenfolge unterstützt.

- Anrufe sollten nur zwischen Entitäten der gleichen Azure Communication Services-Ressource erfolgen. Ressourcenübergreifende Kommunikation wird blockiert.

- Anrufe zwischen Mandantenbenutzern von Teams und Benutzer- oder Serveranwendungsentitäten von Azure Communication Services sind nicht zulässig.

- Wenn eine Anwendung zwei oder mehr Telefonfestnetzidentitäten anruft und dann den Aufruf verlässt, wird der Anruf zwischen den anderen Telefonfestnetzentitäten beendet.

