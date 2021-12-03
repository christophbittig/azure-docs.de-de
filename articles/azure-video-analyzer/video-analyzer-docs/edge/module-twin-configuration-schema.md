---
title: JSON-Schema des Modulzwillings von Azure Video Analyzer
description: Dieser Artikel enthält eine Übersicht über das JSON-Schema des Modulzwillings für das Azure Video Analyzer-Edgemodul
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 360102a551ea6321a4b40063123176fc13672929
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131559388"
---
# <a name="module-twin-configuration-schema"></a>Konfigurationsschema für Modulzwillinge

[!INCLUDE [header](includes/edge-env.md)]

Gerätezwillinge sind JSON-Dokumente, in denen Gerätestatusinformationen gespeichert werden, z. B. Metadaten, Konfigurationen und Bedingungen. Azure IoT Hub pflegt einen Gerätezwilling für jedes Gerät, das Sie mit IoT Hub verbinden. Eine ausführliche Beschreibung finden Sie unter [Verstehen und Verwenden von Modulzwillingen in IoT Hub.](../../../iot-hub/iot-hub-devguide-module-twins.md)

In diesem Thema wird das JSON-Schema des Modulzwillings für das Azure Video Analyzer-Edgemodul beschrieben.

## <a name="module-twin-properties"></a>Eigenschaften von Modulzwillingen

Das Azure Video Analyzer-Edgemodul macht die folgenden Eigenschaften des Modulzwillings verfügbar.

| Eigenschaft                    | Erforderlich | Dynamisch | BESCHREIBUNG                                                  |
| :-------------------------- | :------- | :------ | :----------------------------------------------------------- |
| `ProvisioningToken`          | Ja      | Nein      | Authentifizierungstoken zum Überprüfen des Edgemoduls und Bereitstellen von Clouddiensten (einschließlich Zugriff auf das Video Analyzer-Konto) |
| `ApplicationDataDirectory`    | Ja      | Nein      | Pfad im Dateisystem des Moduls, der einem bereitgestellten Volume für die persistente Konfiguration zuordnet.       |
| `DiagnosticsEventsOutputName` | Nein       | Ja     | Hub-Ausgabe für Diagnoseereignisse. (Leere Ausgabe bedeutet, dass keine Diagnosedaten veröffentlicht wurden.) |
| `OperationalEventsOutputName` | Nein       | Ja     | Hub-Ausgabe für Betriebsereignisse. (Leere Ausgabe bedeutet, dass keine Betriebsereignisse veröffentlicht wurden.) |
| `LogLevel`                    | Nein       | Ja     | Eine der folgenden: · Ausführlich · Information (Standard) · Warnung · Fehler · nichts |
| `LogCategories`               | Nein       | Ja     | Eine durch Trennzeichen getrennte Liste mit folgenden Einträgen: Anwendung, MediaPipeline, Ereignisse Standard: Anwendung, Ereignisse |
| `AllowUnsecuredEndpoints`     | Nein       | Ja     | Boolesche Einstellung, um die Erstellung von Topologien mit ungesicherten Endpunkten wie `#Microsoft.VideoAnalyzer.UnsecuredEndpoint`zu ermöglichen, Standardwert: TRUE        |
| `TelemetryOptOut`             | Nein       | Nein     | Boolesche Einstellung zum Deaktivieren der Übermittlung von Telemetriedaten, Standardwert: FALSE       |
| `DebugLogsDirectory`          | Nein       | Ja     | Verzeichnis für Debugprotokolle. Wenn es vorhanden ist, werden Protokolle generiert, und wenn es nicht vorhanden ist, werden Debugprotokolle deaktiviert.       |

Dynamische Eigenschaften können aktualisiert werden, ohne das Modul neu zu starten. 

Weitere Informationen zur Rolle der optionalen Diagnoseeinstellungen finden Sie im Artikel zur [Überwachung und Protokollierung](monitor-log-edge.md).

```json
{
    "properties.desired": {
        "ProvisioningToken": "$AVA_PROVISIONING_TOKEN",
        "ApplicationDataDirectory": "/var/lib/videoanalyzer",
        "DiagnosticsEventsOutputName": "diagnostics",
        "OperationalEventsOutputName": "operational",
        "LogLevel": "information",
        "LogCategories": "Application,Events",
        "DebugLogsDirectory": "/tmp/logs",
        "AllowUnsecuredEndpoints": true,
        "TelemetryOptOut": false    
     
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

[Direkte Methoden](direct-methods.md)
