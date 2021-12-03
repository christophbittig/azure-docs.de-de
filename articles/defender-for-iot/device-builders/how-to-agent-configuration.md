---
title: Konfigurieren von Sicherheits-Agents
description: Erfahren Sie, wie Sie Defender für IoT-Sicherheits-Agents für die Verwendung mit dem Sicherheitsdienst Defender für IoT konfigurieren.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 8cccb5efc76f0a451cc0de992a0521d5b12978e4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335611"
---
# <a name="tutorial-configure-security-agents"></a>Tutorial: Konfigurieren von Sicherheits-Agents

Dieser Artikel enthält Informationen zu den Sicherheits-Agents in Defender für IoT sowie Details zum Ändern und Konfigurieren dieser Agents.

> [!div class="checklist"]
> * Konfigurieren von Sicherheits-Agents
> * Ändern des Agent-Verhaltens durch Bearbeiten von Zwillingseigenschaften
> * Erkennen der Standardkonfiguration

## <a name="agents"></a>Agents

Defender für IoT-Sicherheits-Agents sammeln Daten aus IoT-Geräten und führen Sicherheitsaktionen aus, um die erkannten Sicherheitsrisiken zu verringern. Die Konfiguration von Sicherheits-Agents erfolgt durch die Anpassung verschiedener Modulzwillingeigenschaften. Im Allgemeinen kommen sekundäre Updates an diesen Eigenschaften nur selten vor.

Bei dem Objekt für die Zwillingskonfiguration des Defender für IoT-Sicherheits-Agents handelt es sich um ein Objekt im JSON-Format. Das Konfigurationsobjekt besteht aus mehreren steuerbaren Eigenschaften, über deren Definition Sie das Verhalten des Agents steuern können.

Mithilfe dieser Konfigurationen können Sie den Agent für jedes erforderliche Szenario anpassen. Sie können zum Beispiel einige Ereignisse automatisch ausschließen oder den Stromverbrauch auf ein Minimum reduzieren, indem Sie diese Eigenschaften konfigurieren.

Verwenden Sie das [Konfigurationsschema](https://aka.ms/iot-security-github-module-schema) des Defender für IoT-Sicherheits-Agents, um Änderungen vorzunehmen.

## <a name="configuration-objects"></a>Konfigurationsobjekte

Die mit einem Defender für IoT-Sicherheits-Agent verknüpften Eigenschaften befinden sich in einem Agent-Konfigurationsobjekt (innerhalb des Abschnitts der gewünschten Eigenschaften) des **azureiotsecurity**-Moduls.

Wenn Sie die Konfiguration ändern möchten, erstellen und ändern Sie dieses Objekt in der Identität des **azureiotsecurity**-Modulzwillings.

Ist das Agent-Konfigurationsobjekt im **azureiotsecurity**-Modulzwilling nicht vorhanden, werden alle Eigenschaftswerte des Sicherheits-Agents auf die Standardeinstellungen gesetzt.

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  }
}
```

## <a name="configuration-schema-and-validation"></a>Konfigurationsschema und Validierung

Stellen Sie sicher, dass Sie Ihre Agent-Konfiguration anhand dieses [Schemas](https://aka.ms/iot-security-github-module-schema) validieren. Wenn das Konfigurationsobjekt nicht dem Schema entspricht, kann der Agent nicht gestartet werden.

Wenn das Konfigurationsobjekt während der Ausführung des Agents in eine ungültige Konfiguration geändert wird (die Konfiguration stimmt nicht mit dem Schema überein), ignoriert der Agent die ungültige Konfiguration und verwendet weiterhin die aktuelle Konfiguration.

### <a name="configuration-validation"></a>Konfigurationsüberprüfung

Der Defender für IoT-Sicherheits-Agent meldet seine aktuelle Konfiguration im Abschnitt mit den gemeldeten Eigenschaften der Identität des **azureiotsecurity**-Modulzwillings.
Der Agent meldet alle verfügbaren Eigenschaften. Wurde eine Eigenschaft vom Benutzer nicht festgelegt, meldet der Agent die Standardkonfiguration.

Wenn Sie Ihre Konfiguration überprüfen möchten, vergleichen Sie die für den gewünschten Abschnitt festgelegten Werte mit den Werten, die im gemeldeten Abschnitt angegeben wurden.

Stimmen die gewünschten und die gemeldeten Eigenschaften nicht überein, konnte der Agent die Konfiguration nicht analysieren.

Überprüfen Sie die gewünschten Eigenschaften anhand des [Schemas](https://aka.ms/iot-security-github-module-schema), beheben Sie die Fehler, und legen Sie die gewünschten Eigenschaften erneut fest.

> [!NOTE]
> Konnte der Agent die gewünschte Konfiguration nicht analysieren, wird von ihm eine Konfigurationsfehlermeldung ausgelöst.
> Vergleichen Sie den gemeldeten mit dem gewünschten Abschnitt, um zu ermitteln, ob die Warnung weiterhin zutrifft.

## <a name="editing-a-property"></a>Bearbeiten einer Eigenschaft

Alle benutzerdefinierten Eigenschaften müssen innerhalb des Agent-Konfigurationsobjekts im **azureiotsecurity**-Modulzwilling festgelegt werden.
Um einen Standardeigenschaftswert zu verwenden, entfernen Sie die Eigenschaft aus dem Konfigurationsobjekt.

### <a name="setting-a-property"></a>Festlegen einer Eigenschaft

1. Suchen und wählen Sie in Ihrem IoT Hub das Gerät aus, das Sie ändern möchten.

1. Klicken Sie auf Ihr Gerät und dann auf das Modul **azureiotsecurity**.

1. Klicken Sie auf **Modulidentitätszwilling**.

1. Bearbeiten Sie die gewünschten Eigenschaften für den Defender für IoT-Micro-Agent.

   Verwenden Sie z. B. die folgende Konfiguration, um Verbindungsereignisse mit hoher Priorität zu konfigurieren und Ereignisse mit hoher Priorität alle 7 Minuten zu sammeln.

    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. Klicken Sie auf **Speichern**.

### <a name="using-a-default-value"></a>Verwendung eines Standardwerts

Um einen Standardeigenschaftswert zu verwenden, entfernen Sie die Eigenschaft aus dem Konfigurationsobjekt.

## <a name="default-properties"></a>Standardeigenschaften

Die folgende Tabelle enthält die steuerbaren Eigenschaften von Defender für IoT-Sicherheits-Agents.

Standardwerte sind im entsprechenden Schema auf [GitHub](https\://aka.ms/iot-security-module-default) verfügbar.

| Name| Status | Gültige Werte| Standardwerte| BESCHREIBUNG |
|----------|--------|--|-------|----|
|highPriorityMessageFrequency|Erforderlich: „false“ |Gültige Werte: Dauer im ISO 8601-Format |Standardwert: PT7M |Maximales Zeitintervall, bevor Nachrichten mit hoher Priorität gesendet werden|
|lowPriorityMessageFrequency |Erforderlich: „false“|Gültige Werte: Dauer im ISO 8601-Format |Standardwert: PT5H |Maximale Zeit, bevor Nachrichten mit niedriger Priorität gesendet werden|
|snapshotFrequency |Erforderlich: „false“|Gültige Werte: Dauer im ISO 8601-Format |Standardwert PT13H |Zeitintervall für die Erstellung von Momentaufnahmen des Gerätstatus.|
|maxLocalCacheSizeInBytes |Erforderlich: „false“ |Gültige Werte: |Standardwert: 2560000, größer als 8192 | Maximaler, für den Nachrichtencache eines Agents zulässiger Speicher (in Byte). Maximale Größe des zum Speichern von Nachrichten auf dem Gerät verwendeten Speicherplatzes, bevor Nachrichten gesendet werden.|
|maxMessageSizeInBytes |Erforderlich: „false“ |Gültige Werte: Eine positive Zahl, größer als 8192 und kleiner als 262144 |Standardwert: 204800 |Maximal zulässige Größe einer Agent-an-Cloud-Nachricht. Diese Einstellung steuert die maximal gesendete Datenmenge in den einzelnen Nachrichten. |
|eventPriority${EventName} |Erforderlich: „false“ |Gültige Werte: Hoch, Niedrig, Aus |Standardwerte: |Priorität jedes von einem Agent generierten Ereignisses |

### <a name="supported-security-events"></a>Unterstützte Sicherheitsereignisse

|Ereignisname| PropertyName | Standardwert| Momentaufnahmenereignis| Detailstatus  |
|----------|-|---------|----|----|
|Diagnoseereignis|eventPriorityDiagnostic| Aus| False| Diagnoseereignisse im Zusammenhang mit dem Agent. Verwenden Sie dieses Ereignis für die ausführliche Protokollierung.|
|Konfigurationsfehler |eventPriorityConfigurationError |Niedrig |False |Der Agent konnte die Konfiguration nicht analysieren. Überprüfen Sie die Übereinstimmung der Konfiguration mit dem Schema.|
|Statistik der gelöschten Ereignisse |eventPriorityDroppedEventsStatistics |Niedrig |True|Statistiken zu Ereignissen im Zusammenhang mit dem Agent. |
|Angeschlossene Hardware|eventPriorityConnectedHardware |Niedrig |True |Momentaufnahme der gesamten mit dem Gerät verbundenen Hardware.|
|Überwachungsports|eventPriorityListeningPorts |High |True |Momentaufnahme aller offenen Überwachungsports auf dem Gerät.|
|Prozesserstellung |eventPriorityProcessCreate |Niedrig |False |Überwachung der Prozesserstellung auf dem Gerät.|
|Prozessbeendigung|eventPriorityProcessTerminate |Niedrig |False |Überwachung der Prozessbeendigung auf dem Gerät.|
|Systeminformationen |eventPrioritySystemInformation |Niedrig |True |Momentaufnahme von Systeminformationen (Beispiel: Betriebssystem oder CPU).|
|Lokale Benutzer| eventPriorityLocalUsers |High |True|Momentaufnahme der registrierten lokalen Benutzer innerhalb des Systems. |
|Anmeldename|  eventPriorityLogin |High|False|Überwachung der Anmeldeereignisse beim Gerät (lokale und Remoteanmeldungen).|
|Verbindungserstellung |eventPriorityConnectionCreate|Niedrig|False|Überwachung der zum und vom Gerät hergestellten TCP-Verbindungen. |
|Firewallkonfiguration| eventPriorityFirewallConfiguration|Niedrig|True|Momentaufnahme der Gerätefirewallkonfiguration (Firewallregeln). |
|Betriebssystembaseline| eventPriorityOSBaseline| Niedrig|True|Momentaufnahme der Überprüfung der Betriebssystembaseline des Geräts.|
|

## <a name="next-steps"></a>Nächste Schritte

- [Grundlegendes zu Defender für IoT-Empfehlungen](concept-recommendations.md)
- [Erkunden von Defender für IoT-Warnungen](concept-security-alerts.md)
- [Access raw security data (Zugreifen auf Sicherheitsrohdaten)](how-to-security-data-access.md)
