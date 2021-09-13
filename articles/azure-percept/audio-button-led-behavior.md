---
title: Tasten- und LED-Zustände von Azure Percept Audio
description: Hier finden Sie weitere Informationen zu den Tasten- und LED-Zuständen von Azure Percept-Audio.
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 04ee60d6ebe9c84d77ea7e5ead140f7930b8cfe4
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224554"
---
# <a name="azure-percept-audio-button-and-led-states"></a>Tasten- und LED-Zustände von Azure Percept Audio

Dieser Leitfaden enthält Informationen zu den Tasten- und LED-Zuständen des Azure Percept-Audiogeräts.

## <a name="button-behavior"></a>Tastenverhalten

Verwenden Sie die Tasten, um das Verhalten des Geräts zu steuern.

|Tastenzustand|Verhalten|
|------------|----------|
|Mute|Drücken Sie diese Taste, um die Stummschaltung der Mikrofonkomponente zu aktivieren bzw. zu deaktivieren. Das Tastenereignis wird beim Loslassen der Taste ausgelöst.|
|PTT/PTS|Drücken Sie „PTT“, um die Schlüsselworterkennung zu umgehen und das Lauschen auf Befehle zu aktivieren. Drücken Sie die Taste erneut, um den aktiven Dialog des Agents anzuhalten und zur Schlüsselworterkennung zurückzukehren. Das Tastenereignis wird beim Loslassen der Taste ausgelöst. PTS kann nur verwendet werden, wenn die Schaltfläche gedrückt wird, während der Agent spricht, und nicht, während der Agent lauscht oder nachdenkt.|

## <a name="led-states"></a>LED-Zustände

Anhand der LED-Anzeigen können Sie nachvollziehen, in welchem Zustand sich Ihr Gerät befindet.

|LED|LED-Zustand|Status des akustischen SOM|
|---|------------|----------------|
|L02|1x Weiß, dauerhaft leuchtend|Einschalten |
|L02|1x Weiß, mit einer Frequenz von 0,5 Hz blinkend|Authentifizierung wird durchgeführt |
|L01 und L02 und L03|3x Blau, dauerhaft leuchtend|Warten auf Schlüsselwort|
|L01 und L02 und L03|Blinkendes LED-Array (20 FPS) |Lauschen oder Sprechen|
|L01 und L02 und L03|Schnell nacheinander aufblinkendes LED-Array (20 FPS)|Berechnung|
|L01 und L02 und L03|3x Rot, dauerhaft leuchtend |Mute|

## <a name="understanding-ear-som-led-indicators"></a>Grundlegendes zu den LED-Anzeigen für das akustische SOM
Anhand von LED-Anzeigen können Sie nachvollziehen, in welchem Zustand sich Ihr Gerät befindet. Es dauert etwa 4–5 Minuten, bis das Gerät eingeschaltet und das Modul vollständig initialisiert ist. Während der Initialisierungsschritte wird Folgendes angezeigt:

1. Weißes LED in der Mitte eingeschaltet (statisch): Das Gerät ist eingeschaltet.
1. Weißes LED in der Mitte eingeschaltet (blinkt): Die Authentifizierung wird durchgeführt.
1. Mittlere weiße LED ein (statisch): Das Gerät ist authentifiziert, aber das Schlüsselwort ist nicht konfiguriert.
1. Alle drei LEDs beginnen blau zu leuchten, sobald eine Demo bereitgestellt wurde und das Gerät betriebsbereit ist.


## <a name="troubleshooting-led-issues"></a>Behandeln von LED-Problemen
- **Wenn die mittlere LED dauerhaft weiß leuchtet**, versuchen Sie, [eine Vorlage zu verwenden, um einen Sprach-Assistenten zu erstellen](./tutorial-no-code-speech.md).
- **Wenn die mittlere LED dauerhaft blinkt**, deutet dies auf ein Authentifizierungsproblem hin. Probieren Sie diese Problembehandlungsschritte aus:
    1. Stellen Sie sicher, dass Ihre USB-A- und Micro-USB-Verbindungen stabil und fest sind. 
    1. Überprüfen Sie, ob das [Sprachmodul ausgeführt wird](./troubleshoot-audio-accessory-speech-module.md#checking-runtime-status-of-the-speech-module).
    1. Neustart des Geräts
    1. [Erfassen Sie Protokolle](./troubleshoot-audio-accessory-speech-module.md#collecting-speech-module-logs), und fügen Sie sie einer Supportanfrage an.
    1. Überprüfen Sie, ob auf Ihrem Dev Kit die neueste Software ausgeführt wird, und wenden Sie gegebenenfalls ein verfügbares Update an.

## <a name="next-steps"></a>Nächste Schritte

Tipps zur Problembehandlung für Ihr Azure Percept-Audiogerät finden Sie in [diesem Handbuch](./troubleshoot-audio-accessory-speech-module.md).