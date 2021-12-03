---
title: Azure Monitor Application Insights Java
description: Überwachung der Anwendungsleistung ohne Codeänderungen für Java-Anwendungen, die in einer beliebigen Umgebung ausgeführt werden. Verteilte Ablaufverfolgung und Anwendungszuordnung.
ms.topic: conceptual
ms.date: 06/24/2021
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 6fbc2c38431cc638873ed4a93ce80f16d789c1de
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158958"
---
# <a name="azure-monitor-opentelemetry-based-auto-instrumentation-for-java-applications"></a>Azure Monitor OpenTelemetry-basierte automatische Instrumentierung für Java-Anwendungen

In diesem Artikel wird beschrieben, wie Sie das OpenTelemetry-basierte Java-Angebot von Azure Monitor aktivieren und konfigurieren. Wenn Sie die Anweisungen in diesem Artikel befolgen, können Sie Azure Monitor Application Insights zum Überwachen Ihrer Anwendung verwenden.

## <a name="get-started"></a>Erste Schritte
Die automatische Java-Instrumentierung kann ohne Codeänderungen aktiviert werden.

### <a name="prerequisites"></a>Voraussetzungen
- Java-Anwendung ab Version 8
- Azure-Abonnement ([kostenloses Azure-Abonnement erstellen](https://azure.microsoft.com/free/))
- Application Insights-Ressource ([Application Insights-Ressource erstellen](create-workspace-resource.md#create-workspace-based-resource))

### <a name="enable-azure-monitor-application-insights"></a>Aktivieren von Azure Monitor Application Insights
**1. Herunterladen der JAR-Datei für die automatische Instrumentierung**

#### <a name="1-download-jar-file"></a>1. Herunterladen der JAR-Datei

Laden Sie die Datei [applicationinsights-agent-3.2.3.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.2.3/applicationinsights-agent-3.2.3.jar) herunter.

> [!WARNING]
> 
> - **Bei einem Upgrade von Vorschauversion 3.0**
>
>    Überprüfen Sie gründlich alle [Konfigurationsoptionen](./java-standalone-config.md): Die JSON-Struktur wurde vollständig geändert. Der Dateiname ist jetzt nur noch in Kleinbuchstaben angegeben.
> 
> - **Bei einem Upgrade von 3.0.x**
> 
>    Die Vorgangs- und Anforderungstelemetrienamen enthalten jetzt die HTTP-Methode (`GET`, `POST` usw.) als Präfix.
>    Dies kann sich auf benutzerdefinierte Dashboards oder Warnungen auswirken, wenn diese auf den vorherigen Werten basieren.
>    Weitere Informationen finden Sie in den [Versionshinweisen zu Version 3.1.0](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.1.0).
>
> - **Bei einem Upgrade von 3.1.x**
> 
>    Datenbankabhängigkeitsnamen sind jetzt präziser, und die vollständige (bereinigte) Abfrage ist weiterhin im Feld `data` vorhanden. HTTP-Abhängigkeitsnamen sind jetzt aussagekräftiger.
>    Dies kann sich auf benutzerdefinierte Dashboards oder Warnungen auswirken, wenn diese auf den vorherigen Werten basieren.
>    Weitere Informationen finden Sie in den [Versionshinweisen zu Version 3.2.0](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.2.0).

#### <a name="2-point-the-jvm-to-the-jar-file"></a>2. Verweisen der JVM auf die JAR-Datei

Fügen Sie den JVM-Argumenten Ihrer Anwendung den Eintrag `-javaagent:path/to/applicationinsights-agent-3.2.3.jar` hinzu. 

> [!TIP]
> Informationen zum Konfigurieren der JVM-Argumente Ihrer Anwendung finden Sie unter [Tipps für das Updaten Ihrer JVM-Argumente](./java-standalone-arguments.md).

#### <a name="3-set-application-insights-connection-string"></a>3. Festlegen der Application Insights-Verbindungszeichenfolge

Verweisen Sie die JAR-Datei auf Ihre Application Insights-Ressource, indem Sie eine Umgebungsvariable festlegen:

```console
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Sie können aber auch eine Konfigurationsdatei mit dem Namen `applicationinsights.json` erstellen und sie im gleichen Verzeichnis wie `applicationinsights-agent-3.2.3.jar` mit folgendem Inhalt ablegen:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Suchen Sie die Verbindungszeichenfolge in Ihrer Application Insights-Ressource.

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights-Verbindungszeichenfolge":::

#### <a name="4-confirm-data-is-flowing"></a>4. Bestätigen des Datenflusses

Führen Sie Ihre Anwendung aus, und öffnen Sie die Registerkarte der Application Insights-Ressource im Azure-Portal. Es kann einige Minuten dauern, bis Daten im Portal angezeigt werden.

> [!NOTE]
> Wenn Sie die Anwendung nicht ausführen oder Daten nicht wie erwartet abrufen können, finden Sie weitere Informationen im Bereich [Problembehandlung](#troubleshooting).

:::image type="content" source="media/opentelemetry/server-requests.png" alt-text="Screenshot der Registerkarte „Übersicht“ von Application Insights mit hervorgehobenen Serveranforderungen und der Serverantwortzeit":::

> [!IMPORTANT]
> Wenn Sie über zwei oder mehr Dienste verfügen, die Telemetriedaten an dieselbe Application Insights-Ressource ausgeben, müssen Sie [Cloudrollennamen festlegen](java-standalone-config.md#cloud-role-name), um sie ordnungsgemäß in der Anwendungszuordnung darzustellen.

> [!NOTE]
> Im Rahmen der Verwendung der Application Insights-Instrumentierung sammeln und senden wir Diagnosedaten an Microsoft. Diese Daten helfen uns, Application Insights auszuführen und zu verbessern. Sie haben die Möglichkeit, die Sammlung nicht wesentlicher Daten zu deaktivieren. [Weitere Informationen](./statsbeat.md).

## <a name="configuration-options"></a>Konfigurationsoptionen

In der `applicationinsights.json` Datei können Sie auch folgende Einstellungen konfigurieren:

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

Ausführliche Informationen finden Sie in den [Konfigurationsoptionen](./java-standalone-config.md).

## <a name="instrumentation-libraries"></a>Instrumentierungsbibliotheken

Java 3.X enthält die folgenden Instrumentierungsbibliotheken.

### <a name="auto-collected-requests"></a>Automatisch gesammelte Anforderungen

* JMS-Consumer
* Kafka-Consumer
* Netty/WebFlux
* Servlets
* Spring-Zeitplanung

### <a name="auto-collected-dependencies"></a>Automatisch gesammelte Abhängigkeiten

Automatisch gesammelte Abhängigkeiten plus verteilte Downstream-Ablaufverfolgungsweitergabe:

* Apache HttpClient
* Apache HttpAsyncClient
* AsyncHttpClient
* Google HttpClient
* gRPC
* java.net.HttpURLConnection
* Java 11 HttpClient
* JAX-RS Client
* Jetty HttpClient
* JMS
* Kafka
* Netty-Client
* OkHttp

Automatisch gesammelte Abhängigkeiten (ohne Weitergabe der nachgelagerten verteilten Ablaufverfolgung):

* Cassandra
* JDBC
* MongoDB (asynchron und synchron)
* Redis (Lettuce und Jedis)

### <a name="auto-collected-logs"></a>Automatisch gesammelte Protokolle

* java.util.logging
* Log4j (einschließlich MDC-Eigenschaften)
* SLF4J/Logback (einschließlich MDC-Eigenschaften)

### <a name="auto-collected-metrics"></a>Automatisch gesammelte Metriken

* Micrometer (einschließlich Metriken des Spring Boot-Aktors)
* JMX-Metriken

### <a name="azure-sdks"></a>Azure SDKs

Die von diesen Azure SDKs ausgegebenen Telemetriedaten werden standardmäßig automatisch erfasst:

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

## <a name="modify-telemetry"></a>Ändern der Telemetrie

### <a name="add-span-attributes"></a>Hinzufügen von span-Attributen
Sie können `opentelemetry-api` verwenden, um Attribute zu Span-Elementen hinzuzufügen. Zu diesen Attributen kann das Hinzufügen einer benutzerdefinierten Geschäftsdimension zu Ihrer Telemetrie gehören. Sie können auch Attribute verwenden, um optionale Felder im Application Insights-Schema festzulegen, z. B. Benutzer-ID oder Client-IP.

#### <a name="add-custom-dimension"></a>Hinzufügen einer benutzerdefinierten Dimension
Wenn Sie eine oder mehrere benutzerdefinierte Dimensionen hinzufügen, wird das Feld _customDimensions_ in der Tabelle mit Anforderungen, Abhängigkeiten oder Ausnahmen aufgefüllt.

> [!NOTE]
> Dieses Feature ist erst ab Version 3.2.0 verfügbar.

Fügen Sie `opentelemetry-api-1.6.0.jar` zu Ihrer Anwendung hinzu:

```xml
<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-api</artifactId>
  <version>1.6.0</version>
</dependency>
```

Fügen Sie benutzerdefinierte Dimensionen in Ihrem Code hinzu:

```java
import io.opentelemetry.api.trace.Span;

Span.current().setAttribute("mycustomdimension", "myvalue1");
```

#### <a name="set-user-id"></a>Festlegen der Benutzer-ID
Füllen Sie das Feld für die Benutzer-ID in der Tabelle mit Anforderungen, Abhängigkeiten oder Ausnahmen aus.

> [!IMPORTANT]
> Informieren Sie sich über die geltenden Datenschutzgesetze, bevor Sie die authentifizierte Benutzer-ID festlegen.

> [!NOTE]
> Dieses Feature ist erst ab Version 3.2.0 verfügbar.

Fügen Sie `opentelemetry-api-1.6.0.jar` zu Ihrer Anwendung hinzu:

```xml
<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-api</artifactId>
  <version>1.6.0</version>
</dependency>
```

Legen Sie `user_Id` in Ihrem Code fest:

```java
import io.opentelemetry.api.trace.Span;

Span.current().setAttribute("enduser.id", "myuser");
```

### <a name="get-trace-id-or-span-id"></a>Abrufen der Ablaufverfolgungs-ID oder Span-ID

Sie können `opentelemetry-api` verwenden, um die Ablaufverfolgungs-ID oder Span-ID abzurufen. Diese Aktion kann ausgeführt werden, um diese Bezeichner der vorhandenen Protokollierungstelemetrie hinzuzufügen und dadurch die Korrelation beim Debuggen und Diagnostizieren von Problemen zu verbessern.

> [!NOTE]
> Dieses Feature ist erst ab Version 3.2.0 verfügbar.

Fügen Sie `opentelemetry-api-1.6.0.jar` zu Ihrer Anwendung hinzu:

```xml
<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-api</artifactId>
  <version>1.6.0</version>
</dependency>
```

Rufen Sie die Ablaufverfolgungs-ID und Span-ID der Anforderung in Ihrem Code ab:

```java
import io.opentelemetry.api.trace.Span;

String traceId = Span.current().getSpanContext().getTraceId();
String spanId = Span.current().getSpanContext().getSpanId();
```

## <a name="custom-telemetry"></a>Benutzerdefinierte Telemetrie

Unser Ziel in Application Insights Java 3.x besteht darin, Ihnen das Senden benutzerdefinierter Telemetriedaten mithilfe von Standard-APIs zu ermöglichen.

Wir unterstützen aktuell Micrometer, beliebte Protokollierungsframeworks und das Application Insights Java 2.x SDK. Application Insights Java 3.x erfasst automatisch die über diese APIs gesendeten Telemetriedaten und korreliert sie mit automatisch gesammelten Telemetriedaten.

### <a name="supported-custom-telemetry"></a>Unterstützte benutzerdefinierte Telemetrie

Die folgende Tabelle zeigt die derzeit unterstützten benutzerdefinierten Telemetrietypen, die Sie zur Ergänzung des Java 3.x-Agents aktivieren können. Zusammenfassend lässt sich Folgendes sagen: Benutzerdefinierte Metriken werden über Micrometer unterstützt, benutzerdefinierte Ausnahmen und Ablaufverfolgungen können durch Protokollierungsframeworks aktiviert werden, benutzerdefinierte Anforderungen, Abhängigkeiten und Ausnahmen können über `opentelemetry-api` aktiviert werden und jede Art von benutzerdefinierter Telemetrie wird über das [Application Insights Java 2.x SDK](#send-custom-telemetry-using-the-2x-sdk) unterstützt.

|                     | Mikrometer | Log4j, logback, JUL | 2.x SDK | opentelemetry-api |
|---------------------|------------|---------------------|---------|-------------------|
| **Benutzerdefinierte Ereignisse**   |            |                     |  Ja    |                   |
| **Benutzerdefinierte Metriken**  |  Ja       |                     |  Ja    |                   |
| **Abhängigkeiten**    |            |                     |  Ja    |  Ja              |
| **Ausnahmen**      |            |  Ja                |  Ja    |  Ja              |
| **Seitenansichten**      |            |                     |  Ja    |                   |
| **Anforderungen**        |            |                     |  Ja    |  Ja              |
| **Traces**          |            |  Ja                |  Ja    |                   |

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

Verwenden Sie den Zähler, um Metriken zu erfassen:

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Senden benutzerdefinierter Ablaufverfolgungen und Ausnahmen mit Ihrem bevorzugten Protokollierungsframework

Log4j, Logback und java.util.logging sind automatisch instrumentiert, und die Protokollierung dieser Protokollierungsframeworks wird automatisch als Telemetrie für Ablaufverfolgung und Ausnahmen erfasst.

Die Protokollierung wird standardmäßig nur gesammelt, wenn diese ab der Ebene INFO erfolgt.
Um diese Ebene zu ändern, lesen Sie die [Konfigurationsoptionen](./java-standalone-config.md#auto-collected-logging).

Wenn Sie Ihren Protokollen benutzerdefinierte Dimensionen anfügen möchten, können Sie [Log4j 1.2 MDC,](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html), [Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html) oder [Logback MDC](http://logback.qos.ch/manual/mdc.html) verwenden. Application Insights Java 3.x erfasst diese MDC-Eigenschaften automatisch als benutzerdefinierte Dimensionen für Ihre Ablaufverfolgungs- und Ausnahmetelemetrie.

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

Verwenden Sie den Client, um benutzerdefinierte Telemetriedaten zu senden:

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

## <a name="troubleshooting"></a>Problembehandlung

Weitere Informationen finden Sie im Artikel zur [Problembehandlung](java-standalone-troubleshoot.md).

## <a name="support"></a>Support
- Sehen Sie sich die [Schritte zur Problembehandlung](java-standalone-troubleshoot.md) an.
- Wenn Azure-Supportprobleme vorliegen, öffnen Sie ein [Azure-Supportticket](https://azure.microsoft.com/support/create-ticket/).
- Wenden Sie sich bei OpenTelemetry-Problemen direkt an die [OpenTelemetry-Community](https://opentelemetry.io/community/).

## <a name="opentelemetry-feedback"></a>OpenTelemetry-Feedback
- Füllen Sie die [Kundenfeedbackumfrage](https://docs.google.com/forms/d/e/1FAIpQLScUt4reClurLi60xyHwGozgM9ZAz8pNAfBHhbTZ4gFWaaXIRQ/viewform) der OpenTelemetry-Community aus.
- Erzählen Sie Microsoft ein wenig von sich, indem Sie unserer [OpenTelemetry Early Adopter-Community](https://aka.ms/AzMonOTel/) beitreten.
- Interagieren Sie mit anderen Azure Monitor-Benutzern in der [Technischen Community von Microsoft](https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor).

## <a name="next-steps"></a>Nächste Schritte

- Überprüfen Sie den Quellcode im [GitHub-Repository für automatische Java-Instrumentierung in Azure Monitor](https://github.com/Microsoft/ApplicationInsights-Java).
- Weitere Informationen zu OpenTelemetry und seiner Community finden Sie im [OpenTelemetry Java GitHub-Repository](https://github.com/open-telemetry/opentelemetry-java-instrumentation).
- [Aktivieren Sie die Web-/Browserbenutzerüberwachung](javascript.md), um Nutzungsfunktionen zu aktivieren.
