---
title: Azure Communication Services-Anrufdiagnose
titleSuffix: An Azure Communication Services concept document
description: Hier finden Sie eine Übersicht über das Feature für die Anrufdiagnose.
author: probableprime
ms.author: rifox
manager: chpalm
services: azure-communication-services
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 23307fbfccdce2faa1b6c5808095f0f97c7a0e66
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635886"
---
# <a name="call-diagnostics"></a>Anrufdiagnose

Wenn Sie mit Anrufen in Azure Communication Services arbeiten, können Probleme auftreten, die Probleme für Ihre Kunden verursachen. Zur Unterstützung in diesem Fall verfügen wir über ein Feature namens „Anrufdiagnose“, mit dem Sie verschiedene Eigenschaften eines Anrufs untersuchen können, um zu ermitteln, worin das Problem bestehen könnte.

**Die Anrufdiagnose wird derzeit nur für unser JS-/Web-SDK unterstützt.**

## <a name="accessing-diagnostics"></a>Zugreifen auf die Diagnose

Die Anrufdiagnose ist ein erweitertes Feature der Kern-API `Call` und ermöglicht es Ihnen, einen aktiven Anruf zu diagnostizieren.

```js
const callDiagnostics = call.api(Features.Diagnostics);
```

## <a name="diagnostic-values"></a>Diagnosewerte

Die folgenden benutzerorientierten Diagnosen sind verfügbar:

### <a name="network-values"></a>Netzwerkwerte

| Name                      | BESCHREIBUNG                                                     | Mögliche Werte                                                                                                                                                                                                                                  | Anwendungsfälle                                           |
| ------------------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------- |
| noNetwork                 | Es ist kein Netzwerk verfügbar.                                  | - Wird auf `True` festgelegt, wenn ein Anruf nicht gestartet werden kann, weil kein Netzwerk verfügbar ist. <br/> - Wird auf `False` festgelegt, wenn ICE-Kandidaten vorhanden sind.                                                                                                  | Das Gerät ist nicht mit einem Netzwerk verbunden.               |
| networkRelaysNotReachable | Probleme mit einem Netzwerk.                                        | - Wird auf `True` festgelegt, wenn das Netzwerk eine Einschränkung aufweist, die es Ihnen nicht erlaubt, ACS-Relais zu erreichen. <br/> - Wird bei einem neuen Anruf auf `False` festgelegt.                                                                                                | Während eines Anrufs, wenn das WLAN-Signal aktiviert und deaktiviert wird. |
| networkReconnect          | Die Verbindung wurde unterbrochen und wir stellen die Verbindung zum Netzwerk wieder her. | - Wird auf `Poor` festgelegt, wenn die Medientransportkonnektivität verloren gegangen ist. <br/> - Wird auf `Bad` festgelegt, wenn das Netzwerk getrennt ist. <br/> - Wird auf `Good` festgelegt, wenn eine neue Sitzung hergestellt wird.                                                                       | Geringe Bandbreite, kein Internet                          |
| networkReceiveQuality     | Ein Indikator für die Qualität des eingehenden Datenstroms.                 | - Wird auf `Bad` festgelegt, wenn ein schwerwiegendes Problem beim Empfang des Datenstroms vorliegt. Qualität <br/> - Wird auf `Poor` festgelegt, wenn ein leichtes Problem beim Empfang des Datenstroms vorliegt. Qualität <br/> - Wird auf `Good` festgelegt, wenn kein Problem beim Empfang des Datenstroms vorliegt. | Geringe Bandbreite                                       |

### <a name="audio-values"></a>Audiowerte

| Name                           | BESCHREIBUNG                                                     | Mögliche Werte                                                                                                                                                                                                                                                                                                   | Anwendungsfälle                                                        |
| ------------------------------ | --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| noSpeakerDevicesEnumerated     | Im System des Benutzers ist kein Audioausgabegerät (Lautsprecher) vorhanden. | - Wird auf `True` festgelegt, wenn keine Lautsprechergeräte im System vorhanden sind und die Lautsprecherauswahl unterstützt wird. <br/> - Wird auf `False` festgelegt, wenn mindestens ein Lautsprechergerät im System vorhanden ist und die Lautsprecherauswahl unterstützt wird.                                                                                             | Kein Lautsprecher ist angeschlossen.                                       |
| speakingWhileMicrophoneIsMuted | Sprechen während der Stummschaltung.                                   | - Wird auf `True` festgelegt, wenn das lokale Mikrofon stummgeschaltet ist und der lokale Benutzer spricht. <br/> - Wird auf `False` festgelegt, wenn der lokale Benutzer entweder aufhört zu sprechen oder die Stummschaltung des Mikrofons aufhebt. <br/> \* Hinweis: Derzeit wird diese Funktion noch nicht von Safari unterstützt, da die Audiopegelbeispiele von webrtc. stats übernommen werden.                 | Schalten Sie Ihr Mikrofon während eines Anrufs stumm, und sprechen Sie in das Mikrofon.           |
| noMicrophoneDevicesEnumerated  | Keine Audioaufnahmegeräte (Mikrofon) im System des Benutzers vorhanden.      | - Wird auf `True` festgelegt, wenn keine Mikrofongeräte im System vorhanden sind. <br/> - Wird auf `False` festgelegt, wenn mindestens ein Mikrofongerät im System vorhanden ist.                                                                                                                                                              | Alle Mikrofone sind während des Gesprächs nicht angeschlossen.                   |
| microphoneNotFunctioning       | Das Mikrofon funktioniert nicht.                                  | - Wird auf `True` festgelegt, wenn die Übertragung des lokalen Audiodatenstroms nicht gestartet werden kann, da das Mikrofongerät möglicherweise im System deaktiviert wurde oder von einem anderen Prozess verwendet wird. Es dauert ungefähr 10 Sekunden, bis dieses UFD ausgelöst wird. <br/> - Wird auf `False` festgelegt, wenn das Mikrofon wieder erfolgreich einen Audiodatenstrom sendet. | Keine Mikrofone verfügbar, Mikrofonzugriff in einem System deaktiviert |
| microphoneMuteUnexpectedly     | Das Mikrofon ist stummgeschaltet.                                             | - Wird auf `True` festgelegt, wenn das Mikrofon unerwartet stummgeschaltet wird. <br/> - Wird auf `False` festgelegt, wenn das Mikrofon erfolgreich einen Audiodatenstrom sendet.                                                                                                                                                                  | Das Mikrofon wird vom System stummgeschaltet.                             |
| microphonePermissionDenied     | Die Lautstärke des Geräts ist gering oder es ist unter macOS fast lautlos. | - Wird auf `True` festgelegt, wenn die Audioberechtigung durch die Systemeinstellungen (Audio) verweigert wird. <br/> - Wird bei erfolgreicher Datenstromerfassung auf `False` festgelegt. <br/> Hinweis: Diese Diagnose funktioniert nur unter macOS.                                                                                                                             | Die Mikrofonberechtigungen sind in den Einstellungen deaktiviert.             |

### <a name="camera-values"></a>Kamerawerte

| Name                   | BESCHREIBUNG                                            | Mögliche Werte                                                                                                                                                                                                                                                                                              | Anwendungsfälle                                                                       |
| ---------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------- |
| cameraFreeze           | Die Kamera erzeugt keine Bilder für mehr als fünf Sekunden. | - Wird auf `True` festgelegt, wenn der lokale Videodatenstrom eingefroren ist. Dies bedeutet, dass die Remoteseite Ihr Video auf ihrem Bildschirm eingefroren sieht oder dass die Remoteteilnehmer Ihr Video nicht auf ihrem Bildschirm rendern. <br/> - Wird auf `False` festgelegt, wenn das Einfrieren beendet ist und die Benutzer Ihr Video wie gewohnt sehen können. | Die Kamera wurde während des Anrufs getrennt oder ein schlechtes Netzwerk hat zum Einfrieren der Kamera geführt. |
| cameraStartFailed      | Generischer Kamerafehler.                                | - Wird auf `True` festgelegt, wenn die Übertragung des lokalen Videos nicht gestartet werden kann, da das Kameragerät möglicherweise im System deaktiviert wurde oder von einem anderen Prozess verwendet wird. <br/> - Wird auf `False` festgelegt, wenn das ausgewählte Kameragerät erfolgreich ein lokales Video sendet. .                                                  | Kamerafehler                                                                 |
| cameraStartTimedOut    | Häufiges Szenario, in dem die Kamera einen fehlerhaften Zustand aufweist.          | - Wird auf `True` festgelegt, wenn das Kameragerät einen Timeout aufweist, um mit dem Senden des Videodatenstroms zu beginnen. <br/> - Wird auf `False` festgelegt, wenn das ausgewählte Kameragerät wieder erfolgreich ein lokales Video sendet.                                                                                                                                         | Kamerafehler                                                                 |
| cameraPermissionDenied | Die Kameraberechtigungen wurden in den Einstellungen verweigert.            | - Wird auf `True` festgelegt, wenn die Kameraberechtigung durch die Systemeinstellungen (Video) verweigert wird. <br/> - Wird bei erfolgreicher Datenstromerfassung auf `False` festgelegt. <br> Hinweis: Diese Diagnose funktioniert nur unter macOS Chrome.                                                                                                                  | Die Kameraberechtigungen sind in den Einstellungen deaktiviert.                                |

### <a name="misc-values"></a>Sonstige Werte

| Name                         | BESCHREIBUNG                                                  | Mögliche Werte                                                                                                                                                                                         | Anwendungsfälle                                 |
| ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| screenshareRecordingDisabled | Die Freigabe des Systembildschirms wurde in den Einstellungen verweigert. | - Wird auf `True` festgelegt, wenn die Bildschirmfreigabe durch die Systemeinstellungen (Freigabe) verweigert wird. <br/> - Wird bei erfolgreicher Datenstromerfassung auf `False` festgelegt. <br/> Hinweis: Diese Diagnose funktioniert nur unter macOS.Chrome. | Die Bildschirmaufzeichnung ist in den Einstellungen deaktiviert. |

## <a name="diagnostic-events"></a>Diagnoseereignisse

- Abonnieren Sie das Ereignis `diagnosticChanged`, um zu überwachen, wenn sich eine Anrufdiagnose ändert.

```js
/**
 *  Each diagnostic has the following data:
 * - diagnostic is the type of diagnostic, e.g. NetworkSendQuality, DeviceSpeakWhileMuted, etc...
 * - value is DiagnosticQuality or DiagnosticFlag:
 *     - DiagnosticQuality = enum { Good = 1, Poor = 2, Bad = 3 }.
 *     - DiagnosticFlag = true | false.
 * - valueType = 'DiagnosticQuality' | 'DiagnosticFlag'
 * - mediaType is the media type associated with the event, e.g. Audio, Video, ScreenShare. These are defined in `CallDiagnosticEventMediaType`.
 */
const diagnosticChangedListener = (diagnosticInfo: NetworkDiagnosticChangedEventArgs | MediaDiagnosticChangedEventArgs) => {
    console.log(`Diagnostic changed: ` +
        `Diagnostic: ${diagnosticInfo.diagnostic}` +
        `Value: ${diagnosticInfo.value}` +
        `Value type: ${diagnosticInfo.valueType}` +
        `Media type: ${diagnosticInfo.mediaType}` +

    if (diagnosticInfo.valueType === 'DiagnosticQuality') {
        if (diagnosticInfo.value === DiagnosticQuality.Bad) {
            console.error(`${diagnosticInfo.diagnostic} is bad quality`);

        } else if (diagnosticInfo.value === DiagnosticQuality.Poor) {
            console.error(`${diagnosticInfo.diagnostic} is poor quality`);
        }

    } else if (diagnosticInfo.valueType === 'DiagnosticFlag') {
        if (diagnosticInfo.value === true) {
            console.error(`${diagnosticInfo.diagnostic}`);
        }
    }
};

callDiagnostics.network.on('diagnosticChanged', diagnosticChangedListener);
callDiagnostics.media.on('diagnosticChanged', diagnosticChangedListener);
```

## <a name="get-the-latest-diagnostics"></a>Abrufen der neuesten Diagnosen

- Rufen Sie die neuesten Diagnosewerte für Anrufe ab, die ausgelöst wurden. Wenn eine Diagnose nicht definiert ist, liegt dies daran, dass sie nie ausgelöst wurde.

```js
const latestNetworkDiagnostics = callDiagnostics.network.getLatest();

console.log(
  `noNetwork: ${latestNetworkDiagnostics.noNetwork.value}, ` +
    `value type = ${latestNetworkDiagnostics.noNetwork.valueType}`
);

console.log(
  `networkReconnect: ${latestNetworkDiagnostics.networkReconnect.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReconnect.valueType}`
);

console.log(
  `networkReceiveQuality: ${latestNetworkDiagnostics.networkReceiveQuality.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReceiveQuality.valueType}`
);

const latestMediaDiagnostics = callDiagnostics.media.getLatest();

console.log(
  `speakingWhileMicrophoneIsMuted: ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.value}, ` +
    `value type = ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.valueType}`
);

console.log(
  `cameraStartFailed: ${latestMediaDiagnostics.cameraStartFailed.value}, ` +
    `value type = ${latestMediaDiagnostics.cameraStartFailed.valueType}`
);

console.log(
  `microphoneNotFunctioning: ${latestMediaDiagnostics.microphoneNotFunctioning.value}, ` +
    `value type = ${latestMediaDiagnostics.microphoneNotFunctioning.valueType}`
);
```
