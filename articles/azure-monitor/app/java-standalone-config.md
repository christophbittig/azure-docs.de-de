---
title: Konfigurationsoptionen – Azure Monitor Application Insights für Java
description: Konfigurieren von Azure Monitor Application Insights für Java
ms.topic: conceptual
ms.date: 11/04/2020
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 9c6fcc3fd0bff46bb3c1665b26502be188a3f83e
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158695"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>Konfigurationsoptionen – Azure Monitor Application Insights für Java

> [!WARNING]
> **Bei einem Upgrade von Vorschauversion 3.0**
>
> Lesen Sie sich unten sorgfältig alle Konfigurationsoptionen durch, da neben dem Dateinamen, der nun komplett in Kleinbuchstaben geschrieben wird, auch die JSON-Struktur vollständig geändert wurde.

## <a name="connection-string-and-role-name"></a>Verbindungszeichenfolge und Rollenname

Die Verbindungszeichenfolge und der Rollenname sind die gängigsten Einstellungen, die für den Einstieg erforderlich sind:

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

Die Verbindungszeichenfolge ist erforderlich, und der Rollenname ist immer dann relevant, wenn Sie Daten unterschiedlicher Anwendungen an dieselbe Application Insights-Ressource senden.

Weitere Informationen und zusätzliche Konfigurationsoptionen finden Sie unten.

## <a name="configuration-file-path"></a>Pfad der Konfigurationsdatei

Standardmäßig erwartet Application Insights Java 3.x eine Konfigurationsdatei mit dem Namen `applicationinsights.json`, die sich im gleichen Verzeichnis wie `applicationinsights-agent-3.2.3.jar` befindet.

Verwenden Sie eines der folgenden Elemente, um einen eigenen Pfad für Ihre Konfigurationsdatei anzugeben:

* Umgebungsvariable `APPLICATIONINSIGHTS_CONFIGURATION_FILE` oder
* Java-Systemeigenschaft `applicationinsights.configuration.file`

Wenn Sie einen relativen Pfad angeben, wird dieser relativ zum Verzeichnis von `applicationinsights-agent-3.2.3.jar` aufgelöst.

## <a name="connection-string"></a>Verbindungszeichenfolge

Eine Verbindungszeichenfolge ist erforderlich. Die Verbindungszeichenfolge finden Sie in der Application Insights-Ressource:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights-Verbindungszeichenfolge":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Sie können die Verbindungszeichenfolge auch über die Umgebungsvariable `APPLICATIONINSIGHTS_CONNECTION_STRING` festlegen (diese hat dann Vorrang vor der Verbindungszeichenfolge, die in der JSON-Konfiguration angegeben ist).

Wird die Verbindungszeichenfolge nicht festgelegt, wird der Java-Agent deaktiviert.

## <a name="cloud-role-name"></a>Cloudrollenname

Der Name der Cloudrolle wird verwendet, um die Komponente in der Anwendungsübersicht zu beschriften.

Wenn Sie den Cloudrollennamen festlegen möchten:

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

Falls der Cloudrollenname nicht festgelegt ist, wird der Name der Application Insights-Ressource zur Beschriftung der Komponente in der Anwendungsübersicht verwendet.

Sie können den Cloudrollennamen auch über die Umgebungsvariable `APPLICATIONINSIGHTS_ROLE_NAME` festlegen (dieser hat dann Vorrang vor dem Cloudrollennamen, der in der JSON-Konfiguration angegeben ist).

## <a name="cloud-role-instance"></a>Cloudrolleninstanz

Der Name der Cloudrolleninstanz entspricht standardmäßig dem Computernamen.

Wenn Sie für die Cloudrolleninstanz einen anderen Namen festlegen möchten:

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

Sie können die Cloudrolleninstanz auch über die Umgebungsvariable `APPLICATIONINSIGHTS_ROLE_INSTANCE` festlegen (diese hat dann Vorrang vor der Cloudrolleninstanz, die in der JSON-Konfiguration angegeben ist).

## <a name="sampling"></a>Stichproben

Stichproben sind nützlich, wenn Sie die Kosten senken müssen.
Stichproben werden als Funktion für die Vorgangs-ID (auch als Nachverfolgungs-ID bezeichnet) ausgeführt, sodass dieselbe Vorgangs-ID immer zur selben Stichprobenentscheidung führt. So wird sichergestellt, dass Stichproben nicht nur für Teile einer verteilten Transaktion erfasst werden, während andere Teile ausgelassen werden.

Wenn Sie beispielsweise die Stichprobenentnahme auf 10 % festlegen, werden nur 10 % Ihrer Transaktionen angezeigt, aber in diesen 10 % sind vollständige End-to-End-Transaktionsdetails enthalten.

Hier sehen Sie ein Beispiel für das Festlegen der Stichprobenentnahme, um ungefähr **1/3 aller Transaktionen** zu erfassen. Stellen Sie sicher, dass Sie eine Stichprobenhäufigkeit festlegen, die sich für Ihren Anwendungsfall eignet:

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

Sie können den Prozentsatz der Stichprobenentnahme auch über die Umgebungsvariable `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` festlegen (dieser hat dann Vorrang vor dem Prozentsatz der Stichprobenentnahme, der in der JSON-Konfiguration angegeben ist).

> [!NOTE]
> Für den Prozentsatz der Stichprobenerstellung wählen Sie einen Prozentsatz, der sich als Bruch darstellen lässt (100/N, wobei N eine Ganzzahl ist). Andere Werte werden bei der Stichprobenerstellung gegenwärtig nicht unterstützt.

## <a name="sampling-overrides-preview"></a>Stichprobenüberschreibungen (Vorschau)

Dieses Feature befindet sich ab 3.0.3 in der Vorschauphase.

Mithilfe von Stichprobenüberschreibungen können Sie den [Standardprozentsatz der Stichprobenentnahme](#sampling) überschreiben. Hier einige Beispiele:
* Legen Sie den Prozentsatz der Stichprobenentnahme für überflüssige Integritätsprüfungen auf 0 (oder einen kleinen Wert) fest.
* Legen Sie den Prozentsatz der Stichprobenentnahme für überflüssige Abhängigkeitsaufrufe auf 0 (oder einen kleinen Wert) fest.
* Legen Sie den Prozentsatz der Stichprobenentnahme für einen wichtigen Anforderungstyp (z. B. `/login`) auf 100 fest, auch wenn die Standardstichprobenentnahme auf einen niedrigeren Wert festgelegt ist.

Weitere Informationen finden Sie in der Dokumentation zu [Stichprobenüberschreibungen](./java-standalone-sampling-overrides.md).

## <a name="jmx-metrics"></a>JMX-Metriken

Verwenden Sie folgenden JSON-Code, wenn Sie einige zusätzliche JMX-Metriken erfassen möchten:

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name` entspricht dem Metriknamen, der dieser JMX-Metrik zugewiesen wird (ein beliebiger Name kann verwendet werden).

`objectName` entspricht dem [Objektnamen](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) des verwalteten Beans (MBean) von JMX, das Sie erfassen möchten.

`attribute` entspricht dem Attributnamen innerhalb des verwalteten Beans (MBean) von JMX, das Sie erfassen möchten.

Numerische und boolesche JMX-Metrikwerte werden unterstützt. Boolesche JMX-Metriken werden `0` für „false“ und `1` für „true“ zugeordnet.

## <a name="custom-dimensions"></a>Benutzerdefinierte Dimensionen

Verwenden Sie den folgenden JSON-Code, wenn Sie benutzerdefinierte Dimensionen zu all Ihren Telemetriedaten hinzufügen möchten:

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` kann zum Lesen des Werts aus der angegebenen Umgebungsvariable beim Start verwendet werden.

> [!NOTE]
> Wenn Sie ab Version 3.0.2 eine benutzerdefinierte Dimension mit dem Namen `service.version` hinzufügen, wird der Wert in der Spalte `application_Version` in der Tabelle „Application Insights-Protokolle“ und nicht als benutzerdefinierte Dimension gespeichert.

## <a name="inherited-attribute-preview"></a>Vererbtes Attribut (Vorschau)

Ab Version 3.2.0 können Sie eine benutzerdefinierte Dimension programmatisch in Ihrer Anforderungstelemetrie festlegen und sie an die nachfolgende Abhängigkeitstelemetrie vererben lassen:

```json
{
  "inheritedAttributes": [
    {
      "key": "mycustomer",
      "type": "string"
    }
  ]
}
```

## <a name="telemetry-processors-preview"></a>Telemetrieprozessoren (Vorschauversion)

Mit diesem Feature können Sie Regeln konfigurieren, die auf Anforderungs-, Abhängigkeits- und Überwachungstelemetriedaten angewendet werden. Zum Beispiel:
 * Maskieren vertraulicher Daten
 * Bedingtes Hinzufügen benutzerdefinierter Dimensionen
 * Aktualisieren des Namens des span-Elements, das zum Aggregieren ähnlicher Telemetriedaten im Azure-Portal verwendet wird
 * Verwerfen bestimmter span-Attribute zur Senkung von Erfassungskosten

Weitere Informationen finden Sie in der [Dokumentation zu Telemetrieprozessoren](./java-standalone-telemetry-processors.md).

> [!NOTE]
> Wenn Sie bestimmte (ganze) Span-Elemente zur Senkung von Erfassungskosten verwerfen möchten, finden Sie Informationen dazu unter [Stichprobenüberschreibungen](./java-standalone-sampling-overrides.md).

## <a name="auto-collected-logging"></a>Automatisch gesammelte Protokolle

„Log4j“, „Logback“ und „java.util.logging“ werden automatisch instrumentiert, und die Protokollierung dieser Protokollierungsframeworks wird automatisch erfasst.

Eine Protokollierung erfolgt nur, wenn erstens der für das Protokollierungsframework konfigurierte Schwellenwert und zweitens auch der in Application Insights konfigurierte Schwellenwert erreicht wird.

Wenn Ihr Protokollierungsframework z. B. so konfiguriert ist, dass es `WARN` (und höher) aus Paket `com.example` protokolliert, und Application Insights so konfiguriert ist, dass es `INFO` (und höher) aufzeichnet, dann wird Application Insights nur `WARN` (und höher) aus Paket `com.example` aufzeichnen.

Die für Application Insights konfigurierte Standardebene ist `INFO`. Wenn Sie diesen Wert ändern möchten, ist das folgendermaßen möglich:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

Sie können die Ebene auch über die Umgebungsvariable `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` festlegen (diese hat dann Vorrang vor der Ebene, die in der JSON-Konfiguration angegeben ist).

Nachfolgend werden die gültigen `level`-Werte, die Sie in der Datei `applicationinsights.json` angeben können, und deren Zuordnung zu den Protokolliergraden in verschiedenen Protokollierungsframeworks aufgeführt:

| Level             | Log4j  | Logback | JUL     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| FATAL             | FATAL  | ERROR   | SEVERE  |
| ERROR (oder SEVERE) | ERROR  | ERROR   | SEVERE  |
| WARN (oder WARNING) | WARN   | WARN    | WARNING |
| INFO              | INFO   | INFO    | INFO    |
| CONFIG            | DEBUG  | DEBUG   | CONFIG  |
| DEBUG (oder FINE)   | DEBUG  | DEBUG   | FINE    |
| FINER             | DEBUG  | DEBUG   | FINER   |
| TRACE (oder FINEST) | TRACE  | TRACE   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

> [!NOTE]
> Wenn ein Ausnahmeobjekt an die Protokollierung übergeben wird, wird die Protokollmeldung (zusammen mit Details zum Ausnahmeobjekt) im Azure-Portal in der Tabelle `exceptions` und nicht in der Tabelle `traces`angezeigt.

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>Automatisch gesammelte Micrometer-Metriken (einschließlich Spring Boot Actuator-Metriken)

Wenn Ihre Anwendung [Micrometer](https://micrometer.io) verwendet, werden Metriken, die an die globale Micrometer-Registrierung gesendet werden, automatisch erfasst.

Wenn Ihre Anwendung auch [Spring Boot Actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html) verwendet, werden mit Spring Boot Actuator konfigurierte Metriken ebenfalls automatisch erfasst.

So deaktivieren Sie die automatische Erfassung von Micrometer-Metriken (einschließlich Spring Boot Actuator-Metriken):

> [!NOTE]
> Benutzerdefinierte Metriken werden separat in Rechnung gestellt und können zu zusätzlichen Kosten führen. Es wird dringend empfohlen, dass Sie die ausführlichen [Preisinformationen](https://azure.microsoft.com/pricing/details/monitor/) überprüfen. Fügen Sie die folgende Konfiguration zu ihrer Konfigurationsdatei hinzu, um die Micrometer- und Spring Actuator-Metriken zu deaktivieren.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="suppressing-specific-auto-collected-telemetry"></a>Unterdrücken bestimmter automatisch erfasster Telemetriedaten

Ab Version 3.0.3 können bestimmte automatisch erfasste Telemetriedaten mithilfe der folgenden Konfigurationsoptionen unterdrückt werden:

```json
{
  "instrumentation": {
    "azureSdk": {
      "enabled": false
    },
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
      "enabled": false
    },
    "jms": {
      "enabled": false
    },
    "kafka": {
      "enabled": false
    },
    "micrometer": {
      "enabled": false
    },
    "mongo": {
      "enabled": false
    },
    "rabbitmq": {
      "enabled": false
    },
    "redis": {
      "enabled": false
    },
    "springScheduling": {
      "enabled": false
    }
  }
}
```

Sie können diese Instrumentierungen auch unterdrücken, indem Sie folgende Umgebungsvariablen auf `false` festlegen:

* `APPLICATIONINSIGHTS_INSTRUMENTATION_AZURE_SDK_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_CASSANDRA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JDBC_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JMS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_KAFKA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MICROMETER_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MONGO_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_RABBITMQ_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_REDIS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_SPRING_SCHEDULING_ENABLED`

(dies hat dann Vorrang vor der Aktivierung, die in der JSON-Konfiguration angegeben ist).

> [!NOTE]
> Wenn Sie eine detailliertere Steuerung wünschen, z. B. um einige, aber nicht alle Redis-Aufrufe zu unterdrücken, finden Sie Informationen dazu unter [Stichprobenüberschreibungen](./java-standalone-sampling-overrides.md).

## <a name="preview-instrumentations"></a>Vorschau-Instrumentierungen

Ab Version 3.2.0 können die folgenden Vorschauminstrumentierungen aktiviert werden:

```
{
  "preview": {
    "instrumentation": {
      "apacheCamel": {
        "enabled": true
      },
      "grizzly": {
        "enabled": true
      },
      "quartz": {
        "enabled": true
      },
      "springIntegration": {
        "enabled": true
      },
      "akka": { 
        "enabled": true
      },
    }
  }
}
```
> [!NOTE]
> Die Akka-Instrumentierung ist ab Version 3.2.2 verfügbar.

## <a name="heartbeat"></a>Heartbeat

Application Insights Java 3.x sendet standardmäßig alle 15 Minuten eine Heartbeatmetrik.
Wenn Sie die Taktmetrik zum Auslösen von Warnungen verwenden, können Sie die Frequenz für den Takt erhöhen:

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> Sie können das Intervall nicht auf mehr als 15 Minuten erhöhen, da die Heartbeat-Daten auch zur Nachverfolgung der Application Insights-Nutzung verwendet werden.

## <a name="http-proxy"></a>HTTP-Proxy

Wenn sich Ihre Anwendung hinter einer Firewall befindet und nicht direkt mit Application Insights verbunden werden kann (siehe [Von Application Insights verwendete IP-Adressen](./ip-addresses.md)), können Sie Application Insights Java 3.x für die Verwendung eines HTTP-Proxys konfigurieren:

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

Application Insights Java 3.x respektiert auch die globalen Systemeigenschaften `https.proxyHost` und `https.proxyPort`, wenn diese festgelegt sind (sowie `http.nonProxyHosts`, falls erforderlich).

## <a name="metric-interval"></a>Metrikintervall

Dieses Feature befindet sich in der Vorschauphase.

Standardmäßig werden Metriken alle 60 Sekunden aufgezeichnet.

Ab Version 3.0.3 können Sie dieses Intervall ändern:

```json
{
  "preview": {
    "metricIntervalSeconds": 300
  }
}
```

Diese Einstellung gilt für alle folgenden Metriken:

* Standardleistungsindikatoren, z. B. CPU und Arbeitsspeicher
* Standardmäßige benutzerdefinierte Metriken, z. B. die zeitliche Steuerung der automatischen Speicherbereinigung
* Konfigurierte JMX-Metriken ([siehe oben](#jmx-metrics))
* Mikrometermetriken ([siehe oben](#auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics))


[//]: # "HINWEIS: Die OpenTelemetry-Unterstützung befindet sich in der privaten Vorschau, bis die OpenTelemetry-API Version 1.0 erreicht."

[//]: # "## Unterstützung von Releases der OpenTelemetry-API vor Version 1.0"

[//]: # "Die Unterstützung von Versionen vor 1.0 der OpenTelemetry-API ist aktivierbar, da die OpenTelemetry-API noch nicht stabil ist,"
[//]: # "daher unterstützt jede Version des Agents nur spezifische Versionen der OpenTelemetry-API, die unter Version 1.0 liegen."
[//]: # "(Diese Einschränkung gilt nicht mehr, sobald Version 1.0 der OpenTelemetry-API veröffentlicht wird.)"

[//]: # "```json"
[//]: # "{"
[//]: # "  \"preview\": {"
[//]: # "    \"openTelemetryApiSupport\": true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="authentication-preview"></a>Authentifizierung (Vorschau)
> [!NOTE]
> Das Authentifizierungsfeature ist ab Version 3.2.0-BETA verfügbar.

Sie können den Agent so konfigurieren, dass [Tokenanmeldeinformationen](/java/api/overview/azure/identity-readme#credentials) generiert werden, die für die Azure Active Directory-Authentifizierung erforderlich sind.
Weitere Informationen finden Sie in der Dokumentation zur [Authentifizierung](./azure-ad-authentication.md).

## <a name="instrumentation-keys-overrides-preview"></a>Instrumentierungstasten überschreiben (Vorschau)

Diese Funktion ist in der Vorschau, beginnend mit 3.2.3.

Instrumentierungsschlüsselüberschreibungen ermöglichen es Ihnen, den [Standardinstrumentierungsschlüssel](#connection-string) außer Kraft zu setzen, zum Beispiel:
* Setzen Sie einen Instrumentierungsschlüssel für einen http-Pfadpräfix `/myapp1`.
* Legen Sie einen weiteren Instrumentierungsschlüssel für ein anderes http-Pfadpräfix `/myapp2/` fest.

```json
{
  "preview": {
    "instrumentationKeyOverrides": [
      {
        "httpPathPrefix": "/myapp1",
        "instrumentationKey": "12345678-0000-0000-0000-0FEEDDADBEEF"
      },
      {
        "httpPathPrefix": "/myapp2",
        "instrumentationKey": "87654321-0000-0000-0000-0FEEDDADBEEF"
      }
    ]
  }
}
```

## <a name="self-diagnostics"></a>Selbstdiagnose

„Selbstdiagnose“ bezeichnet die interne Protokollierung von Application Insights Java 3.x.

Diese Funktionalität kann hilfreich sein, um Probleme mit Application Insights selbst zu identifizieren und zu diagnostizieren.

Application Insights Java 3.x protokolliert standardmäßig auf Ebene `INFO` in der Datei `applicationinsights.log` und der Konsole entsprechend der folgenden Konfiguration:

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination` kann `file`, `console` oder `file+console` sein.

`level` kann `OFF`, `ERROR`, `WARN`, `INFO`, `DEBUG` oder `TRACE` sein.

`path` kann ein absoluter oder ein relativer Pfad sein. Relative Pfade werden anhand des Verzeichnisses aufgelöst, in dem sich `applicationinsights-agent-3.2.3.jar` befindet.

`maxSizeMb` entspricht der maximalen Größe der Protokolldatei, bevor ein Rollover durchgeführt wird.

`maxHistory` entspricht der Anzahl der beibehaltenen Protokolldateien, für die ein Rollover durchgeführt wurde (zusätzlich zur aktuellen Protokolldatei).

Ab Version 3.0.2 können Sie auch das `level` der Selbstdiagnose über die Umgebungsvariable `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL` festlegen (das dann Vorrang vor der Ebene der Selbstdiagnose, die in der JSON-Konfiguration angegeben ist).

## <a name="an-example"></a>Beispiel

Hierbei handelt es sich lediglich um ein Beispiel zur Veranschaulichung einer Konfigurationsdatei mit mehreren Komponenten.
Konfigurieren Sie spezifische Optionen basierend auf Ihren Anforderungen.

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  },
  "sampling": {
    "percentage": 100
  },
  "jmxMetrics": [
  ],
  "customDimensions": {
  },
  "instrumentation": {
    "logging": {
      "level": "INFO"
    },
    "micrometer": {
      "enabled": true
    }
  },
  "proxy": {
  },
  "preview": {
    "processors": [
    ]
  },
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```