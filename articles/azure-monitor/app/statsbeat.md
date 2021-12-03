---
title: Statsbeat in Azure Application Insights | Microsoft-Dokumentation
description: Statistiken zu Application Insights SDKs und der automatischen Instrumentierung
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: 55e727bc05007c69f0144c7f95e17e38f907742e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426884"
---
# <a name="statsbeat-in-azure-application-insights"></a>Statsbeat in Azure Application Insights

Statsbeat erfasst essenzielle und nicht essenzielle [benutzerdefinierte Metrikdaten](../essentials/metrics-custom-overview.md) zu Application Insights SDKs und der automatischen Instrumentierung. Statsbeat bietet Azure Monitor Application Insights-Kunden drei Vorteile:
-   Dienstintegrität und -zuverlässigkeit (externe Überwachung der Konnektivität mit dem Erfassungsendpunkt)
-   Supportdiagnose (Erkenntnisse zur Selbsthilfe und CSS-Erkenntnisse)
-   Produktverbesserung (Erkenntnisse für Entwurfsoptimierungen)

Statsbeat-Daten werden in einem Microsoft-Datenspeicher gespeichert.  Dies wirkt sich für die Kunden nicht auf das Volumen und die Kosten für die Überwachung insgesamt aus. 

## <a name="what-data-does-statsbeat-collect"></a>Was sammelt Statsbeat?

Statsbeat sammelt essenzielle und nicht essenzielle Metriken.

## <a name="supported-languages"></a>Unterstützte Sprachen

| C#                        | Java            | JavaScript                | Node.js         | Python          |
|---------------------------|-----------------|---------------------------|-----------------|-----------------|
| Derzeit nicht unterstützt   | Unterstützt       | Derzeit nicht unterstützt   | Unterstützt       | Unterstützt       |


### <a name="essential-statsbeat"></a>Essenzielle Statsbeat-Daten

#### <a name="network-statsbeat"></a>Netzwerk-Statsbeat

|Metrikname|Einheit|Unterstützte Dimensionen|
|-----|-----|-----|
|Anzahl von erfolgreichen Anforderungen|Anzahl| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|Anzahl von Anforderungsfehlern|Anzahl| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|Dauer der Anforderung|Anzahl| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|Wiederholungszähler|Anzahl| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|Anzahl von Drosselungen|Anzahl| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|Anzahl von Ausnahmen|Anzahl| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|

#### <a name="attach-statsbeat"></a>Anfüge-Statsbeat

|Metrikname|Einheit|Unterstützte Dimensionen|
|-----|-----|-----|
|Attach|Anzahl| `Resource Provider`, `Resource Provider Identifier`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`|

#### <a name="feature-statsbeat"></a>Feature-Statsbeat

|Metrikname|Einheit|Unterstützte Dimensionen|
|-----|-----|-----|
|Feature|Anzahl| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Feature`, `Type`, `Operating System`, `Language`, `Version`|

### <a name="non-essential-statsbeat"></a>Nicht essenzielle Statsbeat-Daten

Nachverfolgen des Datenträger-E/A-Fehlers bei Verwendung von Datenträgerpersistenz für wiederholbare Telemetrie

|Metrikname|Einheit|Unterstützte Dimensionen|
|-----|-----|-----|
|Anzahl der Lesefehler|Anzahl| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`|
|Anzahl der Schreibfehler|Anzahl| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`|

### <a name="configure-statsbeat"></a>Konfigurieren von Statsbeat

#### <a name="java"></a>[Java](#tab/java)

Um nicht essenzielle Statsbeat-Daten zu deaktivieren, fügen Sie Ihrer config-Datei die folgende Konfiguration hinzu.

```json
{
  "preview": {
    "statsbeat": {
        "disabled": "true"
    }
  }
}
```

Sie können dieses Feature auch deaktivieren, indem Sie die Umgebungsvariable `APPLICATIONINSIGHTS_STATSBEAT_DISABLED` auf TRUE festlegen (diese hat dann Vorrang vor der in der JSON-Konfiguration angegebenen Deaktivierung).

#### <a name="node"></a>[Node](#tab/node)

–

#### <a name="python"></a>[Python](#tab/python)

–

---
