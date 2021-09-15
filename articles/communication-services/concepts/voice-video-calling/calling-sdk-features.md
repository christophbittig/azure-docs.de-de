---
title: Übersicht über das Azure Communication Services Calling SDK
titleSuffix: An Azure Communication Services concept document
description: Hier finden Sie eine Übersicht über das Calling SDK.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 7069d5089142f4fc5a6fff6492726b6f949c8f18
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259016"
---
# <a name="calling-sdk-overview"></a>Übersicht über das Calling SDK

[!INCLUDE [SDP Plan B Deprecation Notice](../../includes/plan-b-sdp-deprecation.md)]

Das Calling SDK ermöglicht Endbenutzergeräten die Steuerung der Sprach- und Videokommunikation. Diese Seite enthält ausführliche Beschreibungen von Anruffeatures sowie Informationen zur Plattform- und Browserunterstützung. Für den sofortigen Einstieg empfehlen wir die [Schnellstartanleitungen zu Anrufen](../../quickstarts/voice-video-calling/getting-started-with-calling.md) sowie das [Hero-Beispiel für Anrufe](../../samples/calling-hero-sample.md). 

Nach Beginn der Entwicklung können Sie sich auf der [Seite mit bekannten Problemen](../known-issues.md) über Fehler informieren, um die wir uns bereits kümmern.

Zentrale Features des Calling SDK:

- **Adressierung:** Azure Communication Services bietet generische [Identitäten](../identity-model.md) zur Adressierung von Kommunikationsendpunkten. Clients nutzen diese Identitäten, um sich bei dem Dienst zu authentifizieren und miteinander zu kommunizieren. Diese Identitäten werden in Anruf-APIs verwendet, die es Clients ermöglichen, die Anrufteilnehmer (Teilnehmerliste) zu ermitteln.
- **Verschlüsselung:** Datenverkehr wird durch das Calling SDK verschlüsselt, um Manipulationen bei der Übertragung zu verhindern. 
- **Geräteverwaltung und Medien:** Das Calling SDK ermöglicht die Bindung an Audio- und Videogeräte, codiert Inhalte für eine effiziente Übertragung über die Kommunikationsdatenebene und rendert Inhalte für von Ihnen angegebene Ausgabegeräte und Ansichten. Darüber hinaus stehen APIs für die Bildschirm- und Anwendungsfreigabe zur Verfügung.
- **Telefonfestnetz:** Das Calling SDK kann Sprachanrufe über das herkömmliche Telefonfestnetz empfangen und initiieren – entweder [unter Verwendung von Telefonnummern, die über das Azure-Portal beschafft wurden](../../quickstarts/telephony-sms/get-phone-number.md), oder programmgesteuert.
- **Teams-Besprechungen:** Das Calling SDK kann [Teams-Besprechungen beitreten](../../quickstarts/voice-video-calling/get-started-teams-interop.md) und mit der Sprach- und Videodatenebene von Teams interagieren. 
- **Benachrichtigungen:** Das Calling SDK stellt APIs bereit, die es ermöglichen, Clients über einen eingehenden Anruf zu informieren. Für Situationen, in denen Ihre App nicht im Vordergrund ausgeführt wird, stehen Muster zum [Auslösen von Popupbenachrichtigungen](../notifications.md) (Popups) zur Verfügung, um Endbenutzer auf einen eingehenden Anruf aufmerksam zu machen. 

## <a name="detailed-capabilities"></a>Ausführliche Funktionen 

Die folgende Liste enthält die Features, die aktuell in den Azure Communication Services Calling SDKs verfügbar sind:


| Featuregruppe | Funktion                                                                                                          | JS  | Windows | Java (Android) | Objective-C (iOS) |
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | --- | ------- | -------------- | ----------------- |
| Grundlegende Funktionen | Tätigen eines 1:1-Anrufs zwischen zwei Benutzern                                                                           | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Tätigen eines Gruppenanrufs zwischen mehr als zwei Benutzern (bis zu 350 Benutzer)                                                       | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Höherstufen eines 1:1-Anrufs mit zwei Benutzern zu einem Gruppenanruf mit mehr als zwei Benutzern                                 | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Beitreten zu einem bereits gestarteten Gruppenanruf                                                                              | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Einladen eines weiteren VoIP-Teilnehmers zu einem laufenden Gruppenanruf                                                       | ✔️   | ✔️       | ✔️              | ✔️                 |
| Steuerung während des Anrufs  | Aktivieren/Deaktivieren Ihres Videos                                                                                              | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Stummschalten des Mikrofons/Aufheben der Stummschaltung                                                                                                     | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Wechseln zwischen Kameras                                                                                              | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Lokales Halten/Aufheben des Haltens                                                                                                  | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Aktiver Lautsprecher                                                                                                      | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Auswählen des Lautsprechers für Anrufe                                                                                            | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Auswählen des Mikrofons für Anrufe                                                                                         | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Anzeigen des Status eines Teilnehmers<br/>*Beschäftigt, Early Media, Verbindungsaufbau, Verbunden, Gehalten, Im Wartebereich, Getrennt*         | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Anzeigen des Zustands eines Anrufs<br/>*Early Media, Eingehend, Verbindungsaufbau, Klingeln, Verbunden, Halten, Trennung, Getrennt* | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Anzeigen, ob ein Teilnehmer stummgeschaltet ist                                                                                      | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Anzeigen des Grunds, warum ein Teilnehmer einen Anruf verlassen hat                                                                       | ✔️   | ✔️       | ✔️              | ✔️                 |
| Bildschirmfreigabe    | Freigeben des gesamten Bildschirms innerhalb der Anwendung                                                                 | ✔️   | ❌       | ❌              | ❌                 |
|                   | Freigeben einer bestimmten Anwendung (aus der Liste aktiver Anwendungen)                                                | ✔️   | ❌       | ❌              | ❌                 |
|                   | Freigeben eines Webbrowsertabs aus der Liste geöffneter Tabs                                                                  | ✔️   | ❌       | ❌              | ❌                 |
|                   | Anzeigen von Remotebildschirmfreigabe durch Teilnehmer                                                                            | ✔️   | ✔️       | ✔️              | ✔️                 |
| Liste            | Auflisten von Teilnehmern                                                                                                   | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Entfernen eines Teilnehmers                                                                                                | ✔️   | ✔️       | ✔️              | ✔️                 |
| PSTN              | Tätigen eines 1:1-Anrufs mit einem PSTN-Teilnehmer                                                                     | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Tätigen eines Gruppenanrufs mit PSTN-Teilnehmern                                                                           | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Höherstufen eines 1:1-Anrufs mit einem PSTN-Teilnehmer zu einem Gruppenanruf                                                 | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Verlassen eines Gruppenanrufs als PSTN-Teilnehmer                                                                    | ✔️   | ✔️       | ✔️              | ✔️                 |
| Allgemein           | Testen von Mikrofon, Lautsprecher und Kamera mit einem Audiotestdienst (verfügbar durch Anrufen von 8:echo123)                   | ✔️   | ✔️       | ✔️              | ✔️                 |
| -Geräteverwaltung | Bitten um Erlaubnis zur Verwendung der Audio-/Videofunktion                                                                       | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Abrufen einer Kameraliste                                                                                                     | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Festlegen der Kamera                                                                                                          | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Abrufen der ausgewählten Kamera                                                                                                 | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Abrufen der Mikrofonliste                                                                                                 | ✔️   | ✔️       | ❌              | ❌                 |
|                   | Festlegen des Mikrofons                                                                                                      | ✔️   | ✔️       | ❌              | ❌                 |
|                   | Abrufen des ausgewählten Mikrofons                                                                                             | ✔️   | ✔️       | ❌              | ❌                 |
|                   | Abrufen der Lautsprecherliste                                                                                                   | ✔️   | ✔️       | ❌              | ❌                 |
|                   | Festlegen des Lautsprechers                                                                                                         | ✔️   | ✔️       | ❌              | ❌                 |
|                   | Abrufen des ausgewählten Lautsprechers                                                                                                | ✔️   | ✔️       | ❌              | ❌                 |
| Videorendering   | Rendern eines Videos an mehreren Orten (lokale Kamera oder Remotedatenstrom)                                                  | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Festlegen/Aktualisieren des Skalierungsmodus                                                                                           | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | Rendern des Remote-Videodatenstroms                                                                                          | ✔️   | ✔️       | ✔️              | ✔️                 |


## <a name="calling-sdk-streaming-support"></a>Streamingunterstützung des Calling SDK
Das Communication Services Calling SDK unterstützt folgende Streamingkonfigurationen:

| Begrenzung                                                         | Web                         | Windows/Android/iOS        |
| ------------------------------------------------------------- | --------------------------- | -------------------------- |
| **Anzahl der ausgehenden Datenströme, die gleichzeitig gesendet werden können.**     | 1 Video- oder 1 Bildschirmfreigabe | 1 Video- und 1 Bildschirmfreigabe |
| **Anzahl der eingehenden Datenströme, die gleichzeitig gerendert werden können.** | 1 Video- oder 1 Bildschirmfreigabe | 6 Video- und 1 Bildschirmfreigabe |

Obwohl das Calling SDK diese Grenzwerte nicht erzwingt, kann es bei Ihren Benutzern zur Leistungsbeeinträchtigung führen, wenn sie überschritten werden.

## <a name="calling-sdk-timeouts"></a>Timeouts des Calling SDK

Für die Communication Services Calling SDKs gelten folgende Timeouts:

| Aktion                                                                      | Zeitlimit in Sekunden |
| --------------------------------------------------------------------------- | ------------------ |
| Erneutes Verbinden/Entfernen des Teilnehmers                                               | 120                |
| Hinzufügen oder Entfernen einer neuen Modalität aus einem Anruf (Video oder Bildschirmfreigabe starten/beenden) | 40                 |
| Timeout beim Vorgang zur Anrufübertragung                                             | 60                 |
| Timeout bei 1:1-Verbindungsherstellung                                              | 85                 |
| Timeout bei Gruppenverbindungsherstellung                                            | 85                 |
| Timeout bei PSTN-Verbindungsherstellung                                             | 115                |
| Timeout beim Heraufstufen von 1:1-Anrufen auf Gruppenverbindungen                                    | 115                |

## <a name="javascript-calling-sdk-support-by-os-and-browser"></a>JavaScript Calling SDK: Unterstützung nach Betriebssystem und Browser

Die folgende Tabelle enthält die unterstützten Browser, die derzeit verfügbar sind. Sofern nichts anderes angegeben ist, **unterstützen wir die letzten drei Versionen des Browsers**.

| Plattform     | Chrome | Safari | Edge (Chromium)  |
| ------------ | ------ | ------ | --------------   |
| Android      | ✔️      | ❌      | ❌           | 
| iOS          | ❌      | ✔️      | ❌           |
| macOS        | ✔️      | ✔️      | ❌           | 
| Windows      | ✔️      | ❌      | ✔️           |
| Ubuntu/Linux | ✔️      | ❌      | ❌           |    

* 1:1-Aufrufe werden für Safari nicht unterstützt.
* Die ausgehende Bildschirmfreigabe wird unter iOS oder Android nicht unterstützt.
* [Mit einer iOS-App in Safari können keine Mikrofon- und Lautsprechergeräte (z. B. Bluetooth) aufgelistet bzw. ausgewählt werden.](../known-issues.md#enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados) Dies ist eine Einschränkung des Betriebssystems, und es ist immer nur ein Gerät vorhanden. Die Standardgeräteauswahl wird vom Betriebssystem gesteuert.

## <a name="android-calling-sdk-support"></a>Android Calling SDK-Unterstützung

* Unterstützung für Android-API Level 21 oder höher

* Unterstützung für Java 7 oder höher

* Unterstützung für Android Studio 2.0

## <a name="ios-calling-sdk-support"></a>iOS Calling SDK-Unterstützung

* Unterstützung für iOS 10.0 und höher zur Erstellungszeit und iOS 12.0 und höher zur Laufzeit

* Xcode 12.0 oder höher

## <a name="calling-client---browser-security-model"></a>Anrufclient: Browsersicherheitsmodell

### <a name="user-webrtc-over-https"></a>Verwenden von WebRTC über HTTPS

WebRTC-APIs wie `getUserMedia` setzen voraus, dass die App, von der diese APIs aufgerufen werden, über HTTPS bedient wird.

Für die lokale Entwicklung können Sie `http://localhost` verwenden.

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>Einbetten des Anruf-SDK für Communication Services in ein iframe-Element

Von verschiedenen Browsern wird eine neue [Berechtigungsrichtlinie (auch Featurerichtlinie genannt)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute) eingeführt. Durch diese Richtlinie wird über ein ursprungsübergreifendes iframe-Element gesteuert, wie Anwendungen auf die Kamera und das Mikrofon eines Geräts zugreifen können, was Auswirkungen auf Anrufszenarien hat.

Wenn Sie ein iframe-Element verwenden möchten, um einen Teil der App über eine andere Domäne zu hosten, müssen Sie Ihrem iframe-Element das Attribut `allow` mit dem korrekten Wert hinzufügen.

Durch das folgende iframe-Element wird beispielsweise sowohl Kamera- als auch Mikrofonzugriff gewährt:

```html
<iframe allow="camera *; microphone *">
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte mit Anrufen](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Weitere Informationen finden Sie in den folgenden Artikeln:
- Machen Sie sich mit allgemeinen [Anrufflows](../call-flows.md) vertraut.
- Informieren Sie sich über [Anruftypen](../voice-video-calling/about-call-types.md).
- [Planen Sie Ihre PSTN-Lösung.](../telephony-sms/plan-solution.md)
