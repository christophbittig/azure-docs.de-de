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
ms.openlocfilehash: a32b462b17a96eacb3858e7a22a27262046b8589
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491260"
---
# <a name="known-issues-in-the-sdks-and-apis"></a>Bekannte Probleme in SDK- und API-Instanzen

Dieser Artikel enthält Informationen zu Einschränkungen und bekannten Problemen im Zusammenhang mit Azure Communication Services Calling SDKs und Communication Services-APIs für die Anrufautomatisierung.

> [!IMPORTANT]
> Es gibt mehrere Faktoren, die sich auf die Qualität von Anrufen auswirken können. Weitere Informationen zur Netzwerkkonfiguration und zu bewährten Testmethoden für Communication Services finden Sie unter [Netzwerkempfehlungen](./voice-video-calling/network-requirements.md).

## <a name="javascript-sdk"></a>JavaScript SDK

Die folgenden Abschnitte enthalten Informationen zu bekannten Problemen im Zusammenhang mit den Communication Services JavaScript SDKs für Sprach-und Videoanrufe.

### <a name="ios-with-safari-crashes-and-refreshes-the-page-if-a-user-tries-to-send-video-in-a-call"></a>iOS mit Safari stürzt ab und aktualisiert die Seite, wenn ein Benutzer versucht, während eines Anrufs ein Video zu senden.

Mit iOS 15.1 wurde ein Fehler eingeführt, der sich auf die meisten Communication Services-Anrufe mit Video auswirkt, die in iOS mit Safari getätigt werden. Das Problem tritt insbesondere auf, wenn ein Benutzer einem Communication Services-Anruf oder einer Besprechung in Microsoft Teams beitritt, indem er Communication Services unter iOS 15.1 in einem beliebigen Browser mit aktiviertem Video verwendet. Unter diesen Umständen stürzt der Safari-Browser ab.

Dies ist ein [bekannter Fehler unter iOS 15.1 mit Safari](https://bugs.webkit.org/show_bug.cgi?id=231505). Sie sollten Benutzer daran hindern, die Videofunktion zu verwenden (zu aktivieren), wenn sie iOS 15.1 mit Safari für Communication Services-Videoanrufe verwenden. Sie sollten diese Aktivität auch bei Besprechungen mit Videoanrufen zwischen Microsoft Teams und Communication Services verhindern.

### <a name="refreshing-a-page-doesnt-immediately-remove-the-user-from-their-call"></a>Beim Aktualisieren einer Seite wird der Benutzer nicht sofort aus seinem Anruf entfernt.

Wenn ein Benutzer während eines Anrufs beschließt, die Seite zu aktualisieren, wird dieser Benutzer durch den Communication Services-Mediendienst nicht sofort aus dem Anruf entfernt. Stattdessen wird auf den erneuten Beitritt des Benutzers gewartet. Der Benutzer wird erst nach dem Timeout des Mediendiensts aus dem Anruf entfernt.

Es empfiehlt sich, Benutzeroberflächen zu erstellen, bei denen Endbenutzer die Seite Ihrer Anwendung nicht während eines Anrufs aktualisieren müssen. Wenn ein Benutzer die Seite aktualisiert, verwenden Sie die gleiche Communication Services-Benutzer-ID, nachdem dieser Benutzer zur Anwendung zurückgekehrt ist. Durch den erneuten Beitritt mit derselben Benutzer-ID wird der Benutzer als dasselbe vorhandene Objekt in der `remoteParticipants`-Sammlung dargestellt. Aus der Sicht der anderen Gesprächsteilnehmer bleibt der Benutzer während der Zeit, die für die Aktualisierung der Seite benötigt wird, bis zu ein oder zwei Minuten im Gespräch.

Wenn der Benutzer vor der Aktualisierung Videodaten übertragen hat, behält die Sammlung `videoStreams` die vorherigen Streaminformationen bei, bis für den Dienst ein Timeout auftritt und die Informationen entfernt werden. In diesem Szenario kann die Anwendung ggf. prüfen, ob der Auflistung neue Streams hinzugefügt wurden, und einen Stream mit der höchsten `id` rendern. 

### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Es ist nicht möglich, mehrere Vorschauen von mehreren Geräten im Web zu rendern.

Dies ist eine bekannte Einschränkung. Weitere Informationen finden Sie in der [Übersicht über das Calling SDK](./voice-video-calling/calling-sdk-features.md).

### <a name="enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>In Safari können keine Geräte aufgezählt werden, wenn die Anwendung unter iOS oder iPadOS ausgeführt wird.

Anwendungen können keine Mikrofon- oder Lautsprechergeräte (z. B. Bluetooth) in Safari für iOS oder iPadOS auflisten oder auswählen. Dies ist eine bekannte Einschränkung dieser Betriebssysteme.

Wenn Sie Safari unter macOS verwenden, kann Ihre App über den Geräte-Manager von Communication Services keine Lautsprecher auflisten bzw. auswählen. In diesem Szenario müssen Sie Geräte über das Betriebssystem auswählen. Wenn Sie Chrome unter macOS verwenden, kann die App Geräte über den Geräte-Manager von Communication Services aufzählen bzw. auswählen.

### <a name="device-mutes-and-incoming-video-stops-rendering-when-certain-interruptions-occur"></a>Das Gerät wird stummgeschaltet und das Rendering des eingehenden Videos wird bei bestimmten Unterbrechungen beendet.

Dieses Problem kann auftreten, wenn eine andere Anwendung oder das Betriebssystem die Steuerung des Mikrofons oder der Kamera übernimmt. Im Folgenden finden Sie einige Beispiele, die während eines Anrufs eines Benutzers auftreten können:

- Ein eingehender Anruf erfolgt über das Telefonfestnetz (Public Switched Telephone Network, PSTN) und übernimmt den Zugriff auf das Mikrofongerät.
- Ein Benutzer gibt z. B. ein YouTube-Video wieder oder startet einen FaceTime-Anruf. Der Wechsel zu einer anderen nativen Anwendung kann den Zugriff auf das Mikrofon oder die Kamera übernehmen.
- Ein Benutzer aktiviert Siri, wodurch der Zugriff auf das Mikrofon übernommen wird.

In all diesen Fällen muss der Benutzer zurück zur Anwendung wechseln, um die Stummschaltung aufzuheben. Bei einem Video muss der Benutzer das Video starten, damit die Wiedergabe der Audio- und Videodaten nach der Unterbrechung fortgesetzt werden kann.

Gelegentlich werden Mikrofon- oder Kamerageräte nicht rechtzeitig freigeschaltet, was zu Problemen mit dem ursprünglichen Anruf führen kann. Wenn der Benutzer beispielsweise versucht, die Stummschaltung aufzuheben, während er ein YouTube-Video anschaut, oder wenn gleichzeitig einen Anruf über das Telefonfestnetz aktiv ist. 

Dieses Problem tritt in der folgenden Umgebung auf:

- Clientbibliothek: Calling (JavaScript)
- Browser: Safari
- Betriebssystem: iOS

### <a name="repeatedly-switching-video-devices-might-cause-video-streaming-to-stop-temporarily"></a>Ein wiederholter Wechsel von Videogeräten kann dazu führen, dass das Videostreaming vorübergehend angehalten wird.

Das Wechseln zwischen Videogeräten kann dazu führen, dass Ihr Videostream angehalten wird, während der Stream vom ausgewählten Gerät erfasst wird. Ein häufiger Wechsel zwischen Geräten kann zu Leistungsbeeinträchtigungen führen. Entwicklern wird empfohlen, einen Gerätestream zu beenden, bevor sie einen anderen starten.

### <a name="bluetooth-headset-microphone-isnt-detected-or-audible-during-the-call-on-safari-on-ios"></a>Das Mikrofon eines Bluetooth-Headsets wird während eines Anrufs in Safari unter iOS nicht erkannt oder ist nicht hörbar.

Bluetooth-Headsets werden von Safari unter iOS nicht unterstützt. Ihr Bluetooth-Gerät wird in den verfügbaren Mikrofonoptionen nicht aufgeführt, und andere Teilnehmer können Sie nicht hören, wenn Sie versuchen, Bluetooth über Safari zu verwenden.

Dies ist eine bekannte Betriebssystemeinschränkung. Mit Safari unter macOS und iOS/iPadOS ist es nicht möglich, Lautsprechergeräte über den Geräte-Manager von Communication Services aufzulisten oder auszuwählen. Das liegt daran, dass Safari die Enumeration oder Auswahl von Lautsprechern nicht unterstützt. Verwenden Sie in diesem Szenario das Betriebssystem, um Ihre Geräteauswahl zu aktualisieren.

### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>Es kann vorkommen, dass sich die Videoqualität nach dem Drehen eines Geräts verschlechtert.

Wenn Benutzer ein Gerät drehen, kann diese Bewegung die Qualität von Videos beeinträchtigen, die gestreamt werden.

Dieses Problem tritt in der folgenden Umgebung auf:

- Betroffene Geräte: Google Pixel 5, Google Pixel 3a, Apple iPad 8, and Apple iPad X
- Clientbibliothek: Calling (JavaScript)
- Browser: Safari, Chrome
- Betriebssysteme: iOS, Android

### <a name="camera-switching-makes-the-screen-freeze"></a>Wenn die Kamera umgeschaltet wird, friert der Bildschirm ein. 

Wenn ein Communication Services-Benutzer einem Anruf mithilfe des JavaScript Calling SDK beitritt und dann die Schaltfläche zum Umschalten der Kamera auswählt, reagiert die Benutzeroberfläche möglicherweise nicht mehr. Der Benutzer muss dann die Anwendung aktualisieren oder den Browser in den Hintergrund pushen.

Dieses Problem tritt in der folgenden Umgebung auf:

- Betroffene Geräte: Google Pixel 4a
- Clientbibliothek: Calling (JavaScript)
- Browser: Chrome
- Betriebssysteme: iOS, Android

### <a name="video-signal-problem-when-the-call-is-in-connecting-state"></a>Problem mit dem Videosignal, wenn die Verbindung für den Anruf hergestellt wird. 

Wenn ein Benutzer das Video schnell ein- und ausschaltet, während die *Verbindung* für den Anruf hergestellt wird, kann dies zu einem Problem mit dem für den Anruf erfassten Stream führen. Am besten erstellen die Entwickler ihre Apps so, dass das Video nicht ein- und ausgeschaltet werden muss, während die *Verbindung* für den Anruf hergestellt wird. In den folgenden Szenarien kann die Videoleistung beeinträchtigt werden:

 - Der Benutzer beginnt den Anruf mit Audio und startet und beendet die Videoübertragung, während die *Verbindung* für den Anruf hergestellt wird.
 - Der Benutzer beginnt den Anruf mit Audio und startet und beendet die Videoübertragung, während der Anruf den Status *Wartebereich* aufweist.

### <a name="enumerating-or-accessing-devices-for-safari-on-macos-and-ios"></a>Aufzählen von Geräten für Safari unter macOS und iOS bzw. Zugreifen auf solche Geräte 

In bestimmten Umgebungen werden Sie möglicherweise feststellen, dass Geräteberechtigungen nach einiger Zeit zurückgesetzt werden. Unter macOS und iOS werden Berechtigungen von Safari nicht für einen längeren Zeitraum beibehalten, sofern kein Stream erfasst wird. Am einfachsten lässt sich dies umgehen, indem die `DeviceManager.askDevicePermission()`-API aufgerufen wird, bevor die APIs des Geräte-Managers zur Enumeration der Geräte aufgerufen werden. Zu diesen APIs für die Enumeration gehören `DeviceManager.getCameras()`, `DeviceManager.getSpeakers()` und `DeviceManager.getMicrophones()`. Wenn die Berechtigungen vorhanden sind, wird dem Benutzer nichts angezeigt. Wenn die Berechtigungen nicht vorhanden sind, wird der Benutzer erneut zur Eingabe der Berechtigungen aufgefordert.

Dieses Problem tritt in der folgenden Umgebung auf:

- Betroffenes Gerät: iPhone
- Clientbibliothek: Calling (JavaScript)
- Browser: Safari
- Betriebssystem: iOS

### <a name="delay-in-rendering-remote-participant-videos"></a>Verzögerung beim Rendern der Videostreams von Remoteteilnehmern

Angenommen, während eines laufenden Gruppenanrufs sendet _Benutzer A_ Videodaten, und _Benutzer B_ tritt dem Anruf bei. Manchmal wird Benutzer B das Video von Benutzer A nicht angezeigt, oder das Video von Benutzer A wird erst nach einer langen Verzögerung gerendert. Ein Problem mit der Konfiguration der Netzwerkumgebung kann zu dieser Verzögerung führen. Weitere Informationen finden Sie unter [Netzwerkempfehlungen](./voice-video-calling/network-requirements.md).

### <a name="using-third-party-libraries-during-the-call-might-result-in-audio-loss"></a>Die Verwendung von Drittanbieterbibliotheken während des Anrufs kann zu Audioverlusten führen.

Wenn Sie `getUserMedia` in der Anwendung separat verwenden, geht der Audiostream verloren. Dies liegt daran, dass eine Drittanbieterbibliothek den Gerätezugriff von der Azure Communication Services-Bibliothek übernimmt.

- Verwenden Sie während des Anrufs keine Drittanbieterbibliotheken, die die `getUserMedia`-API intern verwenden.
- Wenn Sie dennoch eine Drittanbieterbibliothek verwenden müssen, besteht die einzige Möglichkeit zur Wiederherstellung des Audiostreams darin, entweder das ausgewählte Gerät zu wechseln (wenn der Benutzer über mehrere verfügt) oder den Anruf neu zu starten.

Dieses Problem tritt in der folgenden Umgebung auf:

- Browser: Safari
- Betriebssystem: iOS

Die Ursache für dieses Problem könnte sein, dass das Abrufen eines eigenen Datenstroms vom selben Gerät den Nebeneffekt hat, dass Racebedingungen eintreten. Das Abrufen von Datenströmen von anderen Geräten kann dazu führen, dass der Benutzer nicht über genügend USB-/E/A-Bandbreite verfügt und die `sourceUnavailableError`-Rate extrem steigt.  

### <a name="support-for-simulcast"></a>Unterstützung für Simulcast

Simulcast ist ein Verfahren, bei dem ein Client denselben Videostream zweimal in unterschiedlichen Auflösungen und Bitraten codiert. Der Client lässt dann Communication Services entscheiden, welchen Stream ein Client empfangen soll. Das Communication Services-Anrufbibliothek-SDK für Windows, Android oder iOS unterstützt das Senden von Simulcast-Streams. Das Communication Services Web SDK unterstützt derzeit nicht das Senden von Simulcast-Streams.

## <a name="communication-services-call-automation-apis"></a>Communication Services-APIs für die Anrufautomatisierung

Im Anschluss finden Sie Informationen zu den bekannten Problemen in den Communication Services-APIs für die Anrufautomatisierung:

- Die einzige Authentifizierung, die derzeit für Serveranwendungen unterstützt wird, ist die Verwendung einer Verbindungszeichenfolge.

- Tätigen Sie Anrufe nur zwischen Entitäten derselben Communication Services-Ressource. Die ressourcenübergreifende Kommunikation ist blockiert.

- Anrufe zwischen Mandantenbenutzern von Microsoft Teams- und Communication Services-Benutzern oder Serveranwendungsentitäten sind nicht zulässig.

- Wenn eine Anwendung zwei oder mehr Telefonfestnetzidentitäten anruft und dann den Aufruf verlässt, wird der Anruf zwischen den anderen Telefonfestnetzentitäten beendet.

