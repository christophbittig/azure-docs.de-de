---
title: 'Stichprobenüberschreibungen (Vorschau): Azure Monitor Application Insights für Java'
description: Hier erfahren Sie, wie Sie Stichprobenüberschreibungen in Azure Monitor Application Insights für Java konfigurieren.
ms.topic: conceptual
ms.date: 03/22/2021
author: trask
ms.custom: devx-track-java
ms.author: trstalna
ms.openlocfilehash: c0f6c1b0fce97bc835cb63a47d8827d7fab8ed56
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599827"
---
# <a name="sampling-overrides-preview---azure-monitor-application-insights-for-java"></a>Stichprobenüberschreibungen (Vorschau): Azure Monitor Application Insights für Java

> [!NOTE]
> Die Funktion für Stichprobenüberschreibungen liegt in der Vorschauversion vor, beginnend ab Version 3.0.3.

Mithilfe von Stichprobenüberschreibungen können Sie den [Standardprozentsatz der Stichprobenentnahme](./java-standalone-config.md#sampling) überschreiben. Hier einige Beispiele:
 * Legen Sie den Prozentsatz der Stichprobenentnahme für überflüssige Integritätsprüfungen auf 0 (oder einen kleinen Wert) fest.
 * Legen Sie den Prozentsatz der Stichprobenentnahme für überflüssige Abhängigkeitsaufrufe auf 0 (oder einen kleinen Wert) fest.
 * Legen Sie den Prozentsatz der Stichprobenentnahme für einen wichtigen Anforderungstyp (z. B. `/login`) auf 100 fest, auch wenn die Standardstichprobenentnahme auf einen niedrigeren Wert festgelegt ist.

## <a name="terminology"></a>Begriff

Bevor wir uns mit Stichprobenüberschreibungen befassen, sollten wir zunächst die Benennung *span* erläutern. Ein span ist eine allgemeine Bezeichnung für Folgendes:

* Eine eingehende Anforderung
* Eine ausgehende Abhängigkeit (z. B. ein Remoteaufruf eines anderen Diensts)
* Eine In-Process-Abhängigkeit (z. B. Vorgänge, die von Unterkomponenten des Diensts durchgeführt werden)

Bei Stichprobenüberschreibungen sind die folgenden span-Komponenten wichtig:

* Attribute

Die span-Attribute stehen sowohl für die standardmäßigen als auch die benutzerdefinierten Eigenschaften einer bestimmten Anforderung oder Abhängigkeit.

## <a name="getting-started"></a>Erste Schritte

Zunächst erstellen Sie eine Konfigurationsdatei namens *applicationinsights.json*. Speichern Sie diese im gleichen Verzeichnis, in dem sich auch *applicationinsights-agent-\*.jar* befindet. Verwenden Sie hierfür die folgende Vorlage.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            ...
          ],
          "percentage": 0
        },
        {
          "attributes": [
            ...
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="how-it-works"></a>Funktionsweise

Wenn ein span-Element gestartet wird, werden die zu diesem Zeitpunkt im span-Element vorhandenen Attribute verwendet, um zu überprüfen, ob eine der Stichprobenüberschreibungen übereinstimmt.

Wenn eine der Stichprobenüberschreibungen übereinstimmt, wird die Stichprobenentnahme in Prozent verwendet, um zu entscheiden, ob vom span-Element eine Stichprobe entnommen werden soll.

Nur die erste übereinstimmende Stichprobenüberschreibung wird verwendet.

Wenn keine Stichprobenüberschreibungen übereinstimmen:

* Wenn es sich um das erste Span-Element in der Ablaufverfolgung handelt, wird die [standardmäßige Stichprobenentnahme in Prozent](./java-standalone-config.md#sampling) verwendet.
* Wenn es sich nicht um das erste span-Element in der Ablaufverfolgung handelt, wird die übergeordnete Entscheidung bezüglich einer Stichprobenentnahme verwendet.

> [!WARNING]
> Wenn die Entscheidung getroffen wurde, keine span-Elemente zu erfassen, wird auch kein nachgelagertes span-Element erfasst. Das gilt auch, wenn Stichprobenüberschreibungen vorhanden sind, die mit dem nachgelagerten span-Element übereinstimmen.
> Dieses Verhalten ist erforderlich, da andernfalls unterbrochene Ablaufverfolgungen entstehen, bei denen nachgelagerte span-Elemente zwar erfasst, aber den nicht erfassten span-Elementen nicht übergeordnet werden.

> [!NOTE]
> Die Entscheidung bezüglich der Stichprobenentnahme basiert auf dem Hashing der Ablaufverfolgungs-ID (auch als Vorgangs-ID bezeichnet) in eine Zahl zwischen 0 und 100. Dieser Hash wird dann mit der Stichprobenentnahme in Prozent verglichen.
> Da alle span-Elemente in einer bestimmten Ablaufverfolgung dieselbe Ablaufverfolgungs-ID aufweisen, weisen sie auch denselben Hash auf. Somit ist die Entscheidung bezüglich der Stichprobenentnahme in der gesamten Ablaufverfolgung konsistent.

## <a name="example-suppress-collecting-telemetry-for-health-checks"></a>Beispiel: Unterdrücken der Erfassung von Telemetriedaten für Integritätsprüfungen

Damit wird die Erfassung von Telemetriedaten für alle Anforderungen an `/health-checks` unterdrückt.

Zudem wird damit auch die Erfassung von allen nachgelagerten span-Elementen (Abhängigkeiten) unterdrückt, die normalerweise unter `/health-checks` erfasst werden.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/health-check",
              "matchType": "regexp"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-suppress-collecting-telemetry-for-a-noisy-dependency-call"></a>Beispiel: Unterdrücken der Erfassung von Telemetriedaten für einen störenden Abhängigkeitsaufruf

Damit wird die Erfassung von Telemetriedaten für alle `GET my-noisy-key`-redis-Aufrufe unterdrückt.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "db.system",
              "value": "redis",
              "matchType": "strict"
            },
            {
              "key": "db.statement",
              "value": "GET my-noisy-key",
              "matchType": "strict"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-collect-100-of-telemetry-for-an-important-request-type"></a>Beispiel: 100%iges Erfassen von Telemetriedaten für einen wichtigen Anforderungstyp

Damit werden für `/login` Telemetriedaten zu 100 % erfasst.

Da bei nachgelagerten span-Elementen (Abhängigkeiten) die Entscheidung des übergeordneten Elements bezüglich der Stichprobenentnahme berücksichtigt wird (keine Stichprobenüberschreibung für dieses nachgelagerte span-Element), werden diese nachgelagerten span-Elemente auch für alle „/login“-Anforderungen erfasst.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/login",
              "matchType": "regexp"
            }
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="common-span-attributes"></a>Gängige span-Attribute

In diesem Abschnitt sind einige gängige span-Attribute aufgeführt, die bei Stichprobenüberschreibungen verwendet werden können.

### <a name="http-spans"></a>HTTP-span-Elemente

| attribute  | type | BESCHREIBUNG | 
|---|---|---|
| `http.method` | Zeichenfolge | HTTP-Anforderungsmethode.|
| `http.url` | Zeichenfolge | Vollständige Anforderungs-URL in HTTP in Form von `scheme://host[:port]/path?query[#fragment]`. Das Fragment wird üblicherweise nicht über HTTP übertragen. Wenn das Fragment jedoch bekannt ist, sollte es eingeschlossen werden.|
| `http.flavor` | Zeichenfolge | Der Typ des HTTP-Protokolls. |
| `http.user_agent` | Zeichenfolge | Der Wert des vom Client gesendeten [HTTP User-Agent](https://tools.ietf.org/html/rfc7231#section-5.5.3)-Headers. |

Beachten Sie, dass `http.status_code` nicht für Stichprobenentscheidungen verwendet werden kann, da das Element zu Beginn der Spanne nicht verfügbar ist.

### <a name="jdbc-spans"></a>JDBC-span-Elemente

| attribute  | type | Beschreibung  |
|---|---|---|
| `db.system` | Zeichenfolge | Der Bezeichner für das verwendete DBMS-Produkt (Datenbank-Managementsystem). |
| `db.connection_string` | Zeichenfolge | Verbindungszeichenfolge, mit der die Verbindung zur Datenbank hergestellt wird. Es empfiehlt sich, eingebettete Anmeldeinformationen zu entfernen.|
| `db.user` | Zeichenfolge | Der Benutzername für den Zugriff auf die Datenbank. |
| `db.name` | Zeichenfolge | Die Zeichenfolge, die den Namen der Datenbank angibt, auf die zugegriffen wird. Bei Befehlen für einen Datenbankwechsel sollte dieses Attribut auf die Zieldatenbank festgelegt werden, auch wenn der Befehl nicht erfolgreich ausgeführt wird.|
| `db.statement` | Zeichenfolge | Die Datenbankanweisung, die ausgeführt wird.|
