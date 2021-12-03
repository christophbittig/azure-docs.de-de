---
title: Lokale Sicherheits-Agent-Konfiguration (C)
description: Erfahren Sie mehr über die lokalen Konfigurationen des Defender für IoT-Sicherheits-Agents für C.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 9ffe4ae98cd745c4c9362f09a9a29258a8d48a2e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286657"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Grundlegendes zur Datei „LocalConfiguration.json“ – C-Agent

Der Defender für IoT-Sicherheits-Agent verwendet Konfigurationen aus einer lokalen Konfigurationsdatei.
Der Sicherheits-Agent liest die Konfiguration einmal bei seinem Start.
Die Konfiguration in der lokalen Konfigurationsdatei enthält die Authentifizierungskonfiguration und andere Konfigurationen im Zusammenhang mit dem Agent.
Die Datei enthält Konfigurationen in „Schlüssel-Wert“-Paaren im JSON-Format, und die Konfigurationen werden bei der Installation des Agents aufgefüllt.

Die Datei befindet sich standardmäßig unter: „/var/ASCIoTAgent/LocalConfiguration.json“.

Änderungen an der Konfigurationsdatei erfolgen beim Neustart des Agents.

## <a name="security-agent-configurations-for-c"></a>Sicherheits-Agent-Konfigurationen für C

| Konfigurationsname | Mögliche Werte | Details |
|:-----------|:---------------|:--------|
| AgentId | GUID | Der eindeutige Agent-Bezeichner |
| TriggerdEventsInterval | ISO8601-Zeichenfolge | Scheduler-Intervall für die Sammlung von ausgelösten Ereignissen |
| ConnectionTimeout | ISO8601-Zeichenfolge | Zeitraum vor dem Ablauf der Verbindung zu IoT Hub |
| Authentifizierung | JsonObject | Authentifizierungskonfiguration. Dieses Objekt enthält alle Informationen, die für die Authentifizierung bei IoT Hub erforderlich sind. |
| Identity | „DPS“, „SecurityModule“, „Device“ | Authentifizierungsidentität: „DPS“, wenn die Authentifizierung über DPS erfolgt, „SecurityModule“, wenn die Authentifizierung über die Anmeldeinformationen des Defender-IoT-Micro-Agents erfolgt, oder „Device“, wenn die Authentifizierung über Geräteanmeldeinformationen erfolgt. |
| AuthenticationMethod | „SasToken“, „SelfSignedCertificate“ | Das Benutzergeheimnis zur Authentifizierung: Wählen Sie „SasToken“ aus, wenn das Benutzergeheimnis ein symmetrischer Schlüssel ist, und „SelfSignedCertificate“, wenn das Geheimnis ein selbstsigniertes Zertifikat ist.  |
| FilePath | Pfad zur Datei (Zeichenfolge) | Der Pfad zu der Datei, die das Authentifizierungsgeheimnis enthält. |
| HostName | Zeichenfolge | Der Hostname des Azure IoT-Hubs. Normalerweise \<my-hub\>.azure-devices.net |
| deviceId | Zeichenfolge | Die ID des Geräts (wie in Azure IoT Hub registriert) |
| DPS | JsonObject | DPS-bezogene Konfigurationen |
| IDScope | Zeichenfolge | ID-Bereich von DPS |
| RegistrationId | Zeichenfolge  | Registrierungs-ID für DPS-Geräte |
| Protokollierung | JsonObject | Konfigurationen mit Agentprotokollierung |
| SystemLoggerMinimumSeverity | 0 < = Zahl < = 4 | Protokollmeldungen gleich und über diesem Schweregrad werden in „/var/log/syslog“ protokolliert („0“ ist der niedrigste Schweregrad). |
| DiagnosticEventMinimumSeverity | 0 < = Zahl < = 4 | Protokollmeldungen gleich und über diesem Schweregrad werden als Diagnoseereignisse gesendet („0“ ist der niedrigste Schweregrad). |

## <a name="security-agent-configurations-code-example"></a>Codebeispiel für Sicherheits-Agent-Konfigurationen

```json
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

- Lesen der [Übersicht](overview.md) über den Defender für IoT-Dienst
- Informieren Sie sich ausführlicher über die [Architektur einer Agent-basierten Lösung](architecture-agent-based.md) für Defender für IoT.
- Aktivieren des Defender für IoT-[Diensts](quickstart-onboard-iot-hub.md)
- Lesen der [Häufig gestellten Fragen](resources-agent-frequently-asked-questions.md) über den Defender für IoT-Dienst
- Informieren Sie sich, wie Sie auf [Sicherheitsrohdaten](how-to-security-data-access.md) zugreifen.
- Machen Sie sich mit [Empfehlungen](concept-recommendations.md) vertraut.
- Machen Sie sich mit [Sicherheitswarnungen](concept-security-alerts.md) vertraut.
