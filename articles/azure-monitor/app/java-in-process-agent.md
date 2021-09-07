---
title: Azure Monitor Application Insights Java
description: Überwachung der Anwendungsleistung ohne Codeänderungen für Java-Anwendungen, die in einer beliebigen Umgebung ausgeführt werden. Verteilte Ablaufverfolgung und Anwendungszuordnung.
ms.topic: conceptual
ms.date: 06/24/2021
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 83cccb8e586973cf575cf1fcd3c70a3166aeb905
ms.sourcegitcommit: fd83264abadd9c737ab4fe85abdbc5a216467d8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112913723"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Java-Anwendungsüberwachung ohne Code mit Azure Monitor Application Insights

> [!NOTE]
> Die alte 2.x-Dokumentation finden Sie [hier](./java-2x-get-started.md).

Bei der Java-Anwendungsüberwachung ohne Code geht es um Einfachheit. Es gibt keine Codeänderungen, und der Java-Agent kann durch nur wenige Konfigurationsänderungen aktiviert werden.

Der Java-Agent kann in jeder Umgebung verwendet werden und ermöglicht Ihnen die Überwachung aller Ihrer Java-Anwendungen. Anders ausgedrückt: Ganz gleich, ob Sie Ihre Java-Apps auf VMs, lokal, in AKS, unter Windows oder unter Linux ausführen – Sie nennen die App, und der Application Insights Java-Agent überwacht sie.

Das Java 2.x-SDK von Application Insights muss nicht mehr zu Ihrer Anwendung hinzugefügt werden, da der Application Insights Java 3.x-Agent Anforderungen, Abhängigkeiten und Protokolle eigenständig und automatisch sammelt.

Sie können weiterhin benutzerdefinierte Telemetriedaten aus Ihrer Anwendung senden.
Der 3.x-Agent überwacht und korreliert diese zusammen mit der gesamten automatisch gesammelten Telemetrie.

Der 3.x-Agent unterstützt Java 8 und höher.

## <a name="quickstart"></a>Schnellstart

**1. Herunterladen des Agents**

> [!WARNING]
> **Bei einem Upgrade von Vorschauversion 3.0**
>
> Lesen Sie sich sorgfältig alle [Konfigurationsoptionen](./java-standalone-config.md) durch, da neben dem Dateinamen, der nun komplett in Kleinbuchstaben geschrieben wird, auch die JSON-Struktur vollständig geändert wurde.

> [!WARNING]
> **Bei einem Upgrade von 3.0.x**
>
> Die Vorgangs- und Anforderungstelemetrienamen enthalten jetzt die HTTP-Methode (`GET`, `POST` usw.) als Präfix.
> Dies kann sich auf benutzerdefinierte Dashboards oder Warnungen auswirken, wenn sie die vorherigen, keinen Präfix aufweisenden Werte verwenden.
> Weitere Informationen finden Sie in den [Versionshinweisen zu Version 3.1.0](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.1.0).

Laden Sie [applicationinsights-agent-3.1.1.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.1.1/applicationinsights-agent-3.1.1.jar) herunter.

**2. Verweisen der JVM auf den Agent**

Fügen Sie den JVM-Argumenten Ihrer Anwendung den Eintrag `-javaagent:path/to/applicationinsights-agent-3.1.1.jar` hinzu. 

Informationen zum Konfigurieren der JVM-Argumente Ihrer Anwendung finden Sie unter [Tipps für das Updaten Ihrer JVM-Argumente](./java-standalone-arguments.md).

**3. Verweisen des Agents auf die Application Insights-Ressource**

Wenn Sie noch nicht über eine Application Insights-Ressource verfügen, können Sie eine neue Ressource erstellen, indem Sie die Schritte im [Leitfaden zum Erstellen einer Ressource](./create-new-resource.md) ausführen.

Verweisen Sie den Agent auf Ihre Application Insights-Ressource, indem Sie eine Umgebungsvariable festlegen:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Sie können aber auch eine Konfigurationsdatei mit dem Namen `applicationinsights.json` erstellen und sie im gleichen Verzeichnis wie `applicationinsights-agent-3.1.1.jar` mit folgendem Inhalt ablegen:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Die Verbindungszeichenfolge finden Sie in der Application Insights-Ressource:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights-Verbindungszeichenfolge":::

**4. Das war's schon!**

Starten Sie nun Ihre Anwendung, und wechseln Sie zur Application Insights-Ressource im Azure-Portal, um die Überwachungsdaten anzuzeigen.

> [!NOTE]
> Es kann einige Minuten dauern, bis die Überwachungsdaten im Portal angezeigt werden.


## <a name="configuration-options"></a>Konfigurationsoptionen

In der Datei `applicationinsights.json` können Sie zusätzlich Folgendes konfigurieren:

* Cloudrollenname
* Cloudrolleninstanz
* Stichproben
* JMX-Metriken
* Benutzerdefinierte Dimensionen
* Telemetrieprozessoren (Vorschauversion)
* Automatisch gesammelte Protokolle
* Automatisch gesammelte Micrometer-Metriken (einschließlich Spring Boot Actuator-Metriken)
* Heartbeat
* HTTP-Proxy
* Selbstdiagnose

Ausführliche Informationen finden Sie unter [Konfigurationsoptionen](./java-standalone-config.md).

## <a name="auto-collected-requests"></a>Automatisch gesammelte Anforderungen

* JMS-Consumer
* Kafka-Consumer
* Netty/WebFlux
* Servlets
* Spring-Zeitplanung

## <a name="auto-collected-dependencies"></a>Automatisch gesammelte Abhängigkeiten

Automatisch gesammelte Abhängigkeiten plus verteilte Downstream-Ablaufverfolgungsweitergabe:

* Apache HttpClient und HttpAsyncClient
* gRPC
* java.net.HttpURLConnection
* JMS
* Kafka
* Netty-Client
* OkHttp

Automatisch gesammelte Abhängigkeiten (ohne Weitergabe der nachgelagerten verteilten Ablaufverfolgung):

* Cassandra
* JDBC
* MongoDB (asynchron und synchron)
* Redis (Lettuce und Jedis)

## <a name="auto-collected-logs"></a>Automatisch gesammelte Protokolle

* java.util.logging
* Log4j (einschließlich MDC-Eigenschaften)
* SLF4J/Logback (einschließlich MDC-Eigenschaften)

## <a name="auto-collected-metrics"></a>Automatisch gesammelte Metriken

* Micrometer (einschließlich Metriken des Spring Boot-Aktors)
* JMX-Metriken

## <a name="azure-sdks-preview"></a>Azure SDKs (Vorschau)

Sehen Sie sich die [Konfigurationsoptionen](./java-standalone-config.md#auto-collected-azure-sdk-telemetry-preview) an, um diese Previewfunktion zu aktivieren und die von diesen Azure SDKs ausgegebenen Telemetriedaten automatisch zu sammeln:

* [App Configuration](/java/api/overview/azure/data-appconfiguration-readme) 1.1.10 und höher
* [Cognitive Search](/java/api/overview/azure/search-documents-readme) 11.3.0 und höher
* [Communication Chat](/java/api/overview/azure/communication-chat-readme) 1.0.0 und höher
* [Communication Common](/java/api/overview/azure/communication-common-readme) 1.0.0 und höher
* [Communication Identity](/java/api/overview/azure/communication-identity-readme) 1.0.0 und höher
* [Communication Phone Numbers](/java/api/overview/azure/communication-phonenumbers-readme) 1.0.0 und höher
* [Communication SMS](/java/api/overview/azure/communication-sms-readme) 1.0.0 und höher
* [Cosmos DB](/java/api/overview/azure/cosmos-readme) 4.13.0 und höher
* [Digital Twins - Core](/java/api/overview/azure/digitaltwins-core-readme) 1.1.0 und höher
* [Event Grid](/java/api/overview/azure/messaging-eventgrid-readme) 4.0.0 und höher
* [Event Hubs](/java/api/overview/azure/messaging-eventhubs-readme) 5.6.0 und höher
* [Event Hubs: Azure Blob Storage-Prüfpunktspeicher](/java/api/overview/azure/messaging-eventhubs-checkpointstore-blob-readme) 1.5.1 und höher
* [Formularerkennung](/java/api/overview/azure/ai-formrecognizer-readme) 3.0.6 und höher
* [Identity](/java/api/overview/azure/identity-readme) 1.2.4 und höher
* [Key Vault: Zertifikate](/java/api/overview/azure/security-keyvault-certificates-readme) 4.1.6 und höher
* [Key Vault: Schlüssel ](/java/api/overview/azure/security-keyvault-keys-readme) 4.2.6 und höher
* [Key Vault: Geheimnisse](/java/api/overview/azure/security-keyvault-secrets-readme) 4.2.6 und höher
* [Service Bus](/java/api/overview/azure/messaging-servicebus-readme) 7.1.0 und höher
* [Storage - Blobs](/java/api/overview/azure/storage-blob-readme) 12.11.0 und höher
* [Storage - Blobs Batch](/java/api/overview/azure/storage-blob-batch-readme) 12.9.0 und höher
* [Storage - Blobs Cryptography](/java/api/overview/azure/storage-blob-cryptography-readme) 12.11.0 und höher
* [Storage - Common](/java/api/overview/azure/storage-common-readme) 12.11.0 und höher
* [Storage - Files Data Lake](/java/api/overview/azure/storage-file-datalake-readme) 12.5.0 und höher
* [Storage - Files Shares](/java/api/overview/azure/storage-file-share-readme) 12.9.0 und höher
* [Storage - Queues](/java/api/overview/azure/storage-queue-readme) 12.9.0 und höher
* [Textanalyse](/java/api/overview/azure/ai-textanalytics-readme) 5.0.4 und höher

[//]: # "Die obigen Namen und Links wurden aus https://azure.github.io/azure-sdk/releases/latest/java.html" zusammengestellt,
[//]: # "und die Versionssynchronisierung wurde manuell anhand der ältesten Version in Maven Central durchgeführt, die auf azure-core 1.14.0 basiert."
[//]: # ""
[//]: # "var table = document.querySelector('#tg-sb-content > div > table')"
[//]: # "var str = ''"
[//]: # "for (var i = 1, row; row = table.rows[i]; i++) {"
[//]: # "  var name = row.cells[0].getElementsByTagName('div')[0].textContent.trim()"
[//]: # "  var stableRow = row.cells[1]"
[//]: # "  var versionBadge = stableRow.querySelector('.badge')"
[//]: # "  if (!versionBadge) {"
[//]: # "    continue"
[//]: # "  }"
[//]: # "  var version = versionBadge.textContent.trim()"
[//]: # "  var link = stableRow.querySelectorAll('a')[2].href"
[//]: # "  str += '* [' + name + '](' + link + ') ' + version + '\n'"
[//]: # "}"
[//]: # "console.log(str)"

## <a name="send-custom-telemetry-from-your-application"></a>Senden benutzerdefinierter Telemetriedaten aus Ihrer Anwendung

Unser Ziel in Application Insights Java 3.x besteht darin, Ihnen das Senden benutzerdefinierter Telemetriedaten mithilfe von Standard-APIs zu ermöglichen.

Wir unterstützen bisher Micrometer, beliebte Protokollierungsframeworks und das Application Insights Java 2.x SDK.
Application Insights Java 3.x erfasst automatisch die über diese APIs gesendeten Telemetriedaten und korreliert sie mit automatisch gesammelten Telemetriedaten.

### <a name="supported-custom-telemetry"></a>Unterstützte benutzerdefinierte Telemetrie

Die folgende Tabelle zeigt die derzeit unterstützten benutzerdefinierten Telemetrietypen, die Sie zur Ergänzung des Java 3.x-Agents aktivieren können. Zusammenfassung für das [Application Insights Java 2.x SDK](#send-custom-telemetry-using-the-2x-sdk): Benutzerdefinierte Metriken werden über Micrometer unterstützt, benutzerdefinierte Ausnahmen und Ablaufverfolgungen können durch Protokollierungsframeworks aktiviert werden, und jede Art von benutzerdefinierter Telemetrie wird unterstützt.

|                     | Mikrometer | Log4j, logback, JUL | 2.x SDK |
|---------------------|------------|---------------------|---------|
| **Benutzerdefinierte Ereignisse**   |            |                     |  Ja    |
| **Benutzerdefinierte Metriken**  |  Ja       |                     |  Ja    |
| **Abhängigkeiten**    |            |                     |  Ja    |
| **Ausnahmen**      |            |  Ja                |  Ja    |
| **Seitenansichten**      |            |                     |  Ja    |
| **Anforderungen**        |            |                     |  Ja    |
| **Traces**          |            |  Ja                |  Ja    |

Wir planen derzeit kein SDK mit Application Insights 3.x.

Application Insights Java 3.x lauscht bereits auf Telemetriedaten, die an das Application Insights Java 2.x-SDK gesendet werden. Diese Funktionalität ist ein wichtiger Bestandteil der Upgrades für vorhandene 2.x-Benutzer und füllt eine wichtige Lücke in der Unterstützung benutzerdefinierter Telemetriedaten bis zur allgemeinen Verfügbarkeit der OpenTelemetry-API.

### <a name="send-custom-metrics-using-micrometer"></a>Senden benutzerdefinierter Metriken mithilfe von Micrometer

Fügen Sie der Anwendung Micrometer hinzu:

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Verwenden Sie die [globale Registrierung](https://micrometer.io/docs/concepts#_global_registry) von Micrometer, um eine Verbrauchseinheit zu erstellen:

```java
static final Counter counter = Metrics.counter("test_counter");
```

und zeichnen Sie damit Metriken auf:

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Senden benutzerdefinierter Ablaufverfolgungen und Ausnahmen mit Ihrem bevorzugten Protokollierungsframework

Log4j, Logback und java.util.logging sind automatisch instrumentiert, und die Protokollierung dieser Protokollierungsframeworks wird automatisch als Telemetrie für Ablaufverfolgung und Ausnahmen erfasst.

Die Protokollierung wird standardmäßig nur gesammelt, wenn diese ab der Ebene INFO erfolgt.
Weitere Informationen zum Ändern dieser Ebene finden Sie in unter [Konfigurationsoptionen](./java-standalone-config.md#auto-collected-logging).

Wenn Sie benutzerdefinierte Dimensionen an Ihre Protokolle anfügen möchten, können Sie [Log4j 1.2 MDC](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html), [Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html) oder [Logback MDC](http://logback.qos.ch/manual/mdc.html) verwenden, damit Application Insights Java 3.x diese MDC-Eigenschaften automatisch als benutzerdefinierte Dimensionen in Ihrer Telemetrie für Ablaufverfolgung und Ausnahmen erfasst.

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>Senden benutzerdefinierter Telemetriedaten mit dem 2.x SDK

Fügen Sie `applicationinsights-core-2.6.3.jar` zu Ihrer Anwendung hinzu (alle 2.x-Versionen werden von Application Insights Java 3.x unterstützt, aber es lohnt sich, die neueste Version zu verwenden, wenn Sie die Wahl haben):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.3</version>
</dependency>
```

Erstellen Sie einen TelemetryClient:

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

und senden Sie damit benutzerdefinierte Telemetriedaten:

##### <a name="events"></a>Ereignisse

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>Metriken

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>Abhängigkeiten

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setSuccess(success);
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>Protokolle

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>Ausnahmen

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>Hinzufügen benutzerdefinierter Anforderungsdimensionen mit dem 2.x SDK

> [!NOTE]
> Dieses Feature ist erst ab Version 3.0.2 verfügbar.

Fügen Sie `applicationinsights-web-2.6.3.jar` zu Ihrer Anwendung hinzu (alle 2.x-Versionen werden von Application Insights Java 3.x unterstützt, aber es lohnt sich, die neueste Version zu verwenden, wenn Sie die Wahl haben):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.3</version>
</dependency>
```

Fügen Sie anschließend benutzerdefinierte Dimensionen in Ihrem Code hinzu:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>Festlegen der Benutzer-ID für die Telemetriedatenanforderung mit dem 2.x SDK

> [!NOTE]
> Dieses Feature ist erst ab Version 3.0.2 verfügbar.

Fügen Sie `applicationinsights-web-2.6.3.jar` zu Ihrer Anwendung hinzu (alle 2.x-Versionen werden von Application Insights Java 3.x unterstützt, aber es lohnt sich, die neueste Version zu verwenden, wenn Sie die Wahl haben):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.3</version>
</dependency>
```

Legen Sie anschließend die Benutzer-ID (`user_Id`) in Ihrem Code fest:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getContext().getUser().setId("myuser");
```

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>Überschreiben des Namens für die Telemetriedatenanforderung mit dem 2.x SDK

> [!NOTE]
> Dieses Feature ist erst ab Version 3.0.2 verfügbar.

Fügen Sie `applicationinsights-web-2.6.3.jar` zu Ihrer Anwendung hinzu (alle 2.x-Versionen werden von Application Insights Java 3.x unterstützt, aber es lohnt sich, die neueste Version zu verwenden, wenn Sie die Wahl haben):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.3</version>
</dependency>
```

Legen Sie anschließend den Namen in Ihrem Code fest:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.setName("myname");
```

### <a name="get-the-request-telemetry-id-and-the-operation-id-using-the-2x-sdk"></a>Abrufen der Anforderungstelemetrie-ID und der Vorgangs-ID mithilfe des 2.x-SDKs

> [!NOTE]
> Dieses Feature ist erst ab Version 3.0.3 verfügbar

Fügen Sie `applicationinsights-web-2.6.3.jar` zu Ihrer Anwendung hinzu (alle 2.x-Versionen werden von Application Insights Java 3.x unterstützt, aber es lohnt sich, die neueste Version zu verwenden, wenn Sie die Wahl haben):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.3</version>
</dependency>
```

Rufen Sie außerdem die Anforderungstelemetrie-ID und die Vorgangs-ID in Ihrem Code ab:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
String requestId = requestTelemetry.getId();
String operationId = requestTelemetry.getContext().getOperation().getId();
```
