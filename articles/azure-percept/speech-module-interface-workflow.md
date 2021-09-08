---
title: Workflow für die Schnittstelle des Azure Percept-Sprachmoduls
description: Beschreibt den Workflow und die verfügbaren Methoden für das Azure Percept-Sprachmodul.
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 7/19/2021
ms.custom: template-concept
ms.openlocfilehash: 8dec6a6c4162225b4f5e9ddf3ce74b739fd39562
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222834"
---
# <a name="azure-percept-speech-module-interface-workflow"></a>Workflow für die Schnittstelle des Azure Percept-Sprachmoduls

In diesem Artikel wird beschrieben, wie das Azure Percept-Sprachmodul mit IoT Hub interagiert. Dies erfolgt über die Methoden „Modulzwilling“ und „Modul“. Darüber hinaus werden die direkten Methodenaufrufe aufgelistet, die zum Aufrufen des Sprachmoduls verwendet werden.

## <a name="speech-module-interaction-with-iot-hub-via-module-twin-and-module-method"></a>Interaktion des Sprachmoduls mit IoT Hub über die Methode „Modulzwilling“ und „Modul“
- IoT Hub verwendet die Methode „Modulzwilling“ zum Bereitstellen von Sprachmoduleinstellungen, und die Einstellungen werden in den Eigenschaften gespeichert. Das Sprachmodul kann Geräteinformationen und Telemetriedaten für IoT Hub anhand der von der Methode „Modulzwilling“ gemeldeten Eigenschaften aktualisieren.
- IoT Hub kann Steuerungsanforderungen über die Methode „Modul“ an das Sprachmodul senden.
- IoT Hub kann den Status des Sprachmoduls über die Methode „Modul“ abrufen.

Weitere Informationen finden Sie unter [Verstehen und Verwenden von Modulzwillingen in IoT Hub](../iot-hub/iot-hub-devguide-module-twins.md).


## <a name="speech-module-states"></a>Sprachmodulzustände
- **IoTInitialized**: Gibt an, dass das IoT-Modul initialisiert und das Netzwerk zwischen dem Sprachmodul und dem Edge-Hub-Modul verbunden ist.
- **Authenticating**: Die Azure Audio-Geräteauthentifizierung wird verarbeitet.
- **Authenticated**: Die Azure Audio-Geräteauthentifizierung ist abgeschlossen. Bei einem Fehler erhält IoT Hub eine Fehlermeldung.
- **MicDiscovering**: Start der Enumeration des Mikrofonarrays über die ALSA-Schnittstelle.
- **MicDiscovered**: Die Enumeration des Mikrofonarrays ist abgeschlossen. Bei einem Fehler erhält IoT Hub eine Fehlermeldung.
- **SpeechConfigured**: Die CC-Konfiguration ist abgeschlossen. Bei einem Fehler erhält IoT Hub eine Fehlermeldung.
- **SpeechStarted**: Gibt an, dass der Bot konfiguriert ist und ausgeführt wird.
- **SpeechStopped**: Gibt an, dass der Bot beendet wurde.
- **DeviceRemoved**: Gibt an, dass das Azure Audio-Gerät entfernt wurde.


## <a name="speech-bot-states"></a>Sprachbotzustände
Das Abfragen von Sprachbotzuständen wird nur unter dem **SpeechStarted**-Sprachmodulzustand unterstützt.
- **Ready**: KWS ist bereit und wartet auf die Sprachaktivierung.
- **Listening**: Bot lauscht der Spracheingabe.
- **Thinking**: Bot wartet auf Antwort.
- **Speaking**: Bot erhält Antwort und spricht die Antwort.

## <a name="interaction-between-iot-hub-and-the-speech-module"></a>Interaktion zwischen IoT Hub und dem Sprachmodul 
In diesem Abschnitt wird beschrieben, wie IoT Hub mit dem Sprachmodul interagiert. Wie das Diagramm zeigt, gibt es drei Arten von Nachrichten.
- Bereitstellung mit erforderlichen Eigenschaften und Aktualisierung mit gemeldeten Eigenschaften
- Aufruf der Methode „Modul“
- Aktualisieren von Telemetriedaten

:::image type="content" source="media/speech-module-interface-workflow/speech-module-diagram.png" alt-text="Diagramm: Interaktion zwischen IoT Hub und dem Sprachmodul":::

IoT Hub ruft die Methode „Modul“ mit zwei Parametern auf:
- Der Name der Methode „Modul“ (Groß-/Kleinschreibung wird beachtet)
- Die Methodennutzdaten

Das Sprachmodul antwortet mit:
- Statuscode
    - **0** = Im Leerlauf
    - **102** = Wird verarbeitet
    - **200** = Erfolg
    - **202** = Ausstehend
    - **500** = Fehler
    - **501** = Nicht vorhanden
- Statusnutzdaten

Hier folgt ein Beispiel mit der Modulmethode „GetModuleState“:
1. Rufen Sie die Methode mit den folgenden Parametern auf:
    - Zeichenfolge: „GetModuleState“
    - Nicht angegeben.
1. Antwort:
    - Statuscode: 200
    - Nutzdaten: „DeviceRemoved“

## <a name="next-steps"></a>Nächste Schritte
Versuchen Sie, diese Konzepte anzuwenden, wenn Sie eine [Sprach-Assistent-Anwendung mit Azure IoT Hub konfigurieren](./how-to-configure-voice-assistant.md).