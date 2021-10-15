---
title: Versionshinweise zum Azure Percept DK-Container
description: Informationen zu Änderungen und Korrekturen für Azure Percept DK-Containerversionen.
author: amiyouss
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: 5b4e67197e5d43e929ca01c8c81e297e134edf47
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271610"
---
# <a name="azure-percept-dk-container-release-notes"></a>Versionshinweise zum Azure Percept DK-Container

Diese Seite bietet Informationen zu Änderungen und Korrekturen für Azure Percept DK-Containerversionen.

Navigieren Sie zum Herunterladen der Containerupdates zu [Azure Percept Studio](https://ms.portal.azure.com/#blade/AzureEdgeDevices/main/overview), wählen Sie im linken Navigationsbereich „Geräte“ und dann das bestimmte Gerät aus. Wählen Sie anschließend die Registerkarten „Vision“ und „Speech“ aus, um Containerdownloads zu starten. 

## <a name="august-2108-release"></a>Release von August (2108)

- Azureyemodule (mcr.microsoft.com/azureedgedevices/azureeyemodule:2108-1)
    - Aktualisiert auf die neueste Intel-Version (Mai) für das MyriadX Camera-Firmwareupdate. 
    - Aktivierte UVC-Kamera (USB-Videoklasse) als Eingabequelle. Wie Sie die UVC-Kamera als Eingabequelle verwenden können, erfahren Sie im [Github-Repository zur erweiterten Entwicklung](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule#using-uvcusb-video-class-camera-as-input-source). 
    - Modulabsturz bei Verwendung des unformatierten H.264-RTSP-Datenstroms behoben.

## <a name="june-2106-release"></a>Release von Juni (2106)

- Azureyemodule
    - Dieses Release bietet Unterstützung für die zeitliche Anpassung der Rückschlüsse von langsamen neuronalen Netzwerken an die Videodatenströme. Sie fügt dem Videodatenstrom eine Wartezeit hinzu, die ungefähr der Wartezeit des neuronalen Netzwerks entspricht, führt aber dazu, dass die Rückschlüsse (z. B. Begrenzungsrahmen) an den richtigen Stellen über dem Video gezeichnet werden. 
    - Um dieses Feature zu aktivieren, fügen Sie `TimeAlignRTSP: true` Ihrem Modulzwilling im Azure-Portal von IoT Hub hinzu.
- Azureearspeechclientmodule
    - Das [Speech 1.16 SDK](../cognitive-services/speech-service/devices-sdk-release-notes.md) wurde in das Speech-Modul integriert, das Korrekturen für die Unterstützung von Sprachtoken enthält und EAR SOM als standardmäßig unterstütztes Gerät in die Low-Level-Bibliothek integriert.
    - Es wurde ein PnP-Erkennungsproblem behoben, während die Sicherheits-MCU entfernt wurde, der Codec jedoch nicht.
    - Es wurden Timeouts für den Speech-Dienst mit PTT/PTS-Schaltflächenfunktionen behoben.
    - Sicherheitskorrekturen
        - Lesevorgang außerhalb des gültigen Bereichs empfängt TLS-Daten im Sprachmodul.
        - MCU- und Codec-USB-Anschlüsse werden während der DFU erneut verfügbar gemacht.
        - Ausnahmebehandlung bei der Analyse von JSON.
        - Aktivieren aller festgeschriebenen Compilersicherheitsflags.
        - Lesevorgang außerhalb des gültigen Bereichs empfängt TLS-Daten im Sprachmodul.
        - Versionsverwaltung von SSL- und libcurl-Abhängigkeiten und Verhindern anfälliger Versionen.
        - Erzwingung von HTTPS und Anheften einer TLS-Zertifizierungsstelle für curl-Verbindungen.

## <a name="april-2104-release"></a>Release von April (2104)

- Azureyemodule
    - Das IoT Hub-Nachrichtenformat wurde in ein UTF-8-codiertes JSON-Format behoben (zuvor war es ein 64-Bit-codiertes Format).
    - Ein Fehler bei der Custom Vision-Klassifizierung wurde behoben (zuvor funktionierten die Custom Vision-Klassifizierungsmodelle nicht ordnungsgemäß. Sie interpretierten die falsche Dimension des Ausgabetensors als Klassenkonfidenzen, was dazu führte, dass unabhängig von der Konfidenz immer eine einzelne Klasse vorhergesagt wurde).
    - H.264 wurde aktualisiert, um TCP anstelle von UDP für die Integration von Azure Video Analyzer zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

- [Bestimmen Ihrer Updatestrategie](./how-to-determine-your-update-strategy.md)
