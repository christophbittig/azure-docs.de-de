---
title: Strukturiertes Anwendungsprotokoll für Azure Spring Cloud | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie strukturierte Anwendungsprotokolldaten in Azure Spring Cloud generiert und erfasst werden.
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 8d84462d38c00e3788e424bd7cac6742d8b0e408
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122350083"
---
# <a name="structured-application-log-for-azure-spring-cloud"></a>Strukturiertes Anwendungsprotokoll für Azure Spring Cloud

In diesem Artikel wird erläutert, wie strukturierte Anwendungsprotokolldaten in Azure Spring Cloud generiert und erfasst werden. Mit der richtigen Konfiguration bietet Azure Spring Cloud nützliche Anwendungsprotokollabfragen und -analysen über Log Analytics.

## <a name="log-schema-requirements"></a>Protokollschemaanforderungen

Um die Protokollabfrage zu verbessern, muss ein Anwendungsprotokoll im JSON-Format vorliegen und einem Schema entsprechen. Azure Spring Cloud verwendet dieses Schema, um Ihre Anwendung zu analysieren und in Log Analytics zu streamen.

> [!NOTE]
> Wenn Sie das JSON-Protokollformat aktivieren, ist es schwierig, die Protokollstreamingausgabe aus der Konsole zu lesen. Um eine von Menschen lesbare Ausgabe zu erhalten, fügen Sie das `--format-json`-Argument an den CLI-Befehl `az spring-cloud app logs` an. Weitere Informationen finden Sie unter [Formatieren strukturierter JSON-Protokolle](./how-to-log-streaming.md#format-json-structured-logs).

**JSON-Schemaanforderungen:**

| JSON-Schlüssel      | JSON-Werttyp|  Erforderlich | Spalte in Log Analytics| BESCHREIBUNG |
| --------------| ------------|-----------|-----------------|--------------------------|
| timestamp     | Zeichenfolge      |     Ja   | AppTimestamp    | Zeitstempel im UTC-Format  |
| Protokollierungstool        | Zeichenfolge      |     Nein    | Protokollierungstool          | Protokollierungstool                   |
| Level         | Zeichenfolge      |     Nein    | CustomLevel     | log level                |
| Thread        | Zeichenfolge      |     Nein    | Thread          | Thread                   |
| message       | Zeichenfolge      |     Nein    | `Message`         | Protokollnachricht              |
| stackTrace    | Zeichenfolge      |     Nein    | StackTrace      | Ausnahme Stapelüberwachung    |
| exceptionClass| Zeichenfolge      |     Nein    | exceptionClass  | Name der Ausnahmeklasse     |
| mdc           | Geschachteltes JSON |     Nein    |                 | Zugeordneter Diagnosekontext|
| mdc.traceId   | Zeichenfolge      |     Nein    | TraceId         |Ablaufverfolgungs-ID für verteilte Ablaufverfolgung|
| mdc.spanId    | Zeichenfolge      |     Nein    | SpanId          |Spannen-ID für verteilte Ablaufverfolgung |
|               |             |           |                 |                          |

* Das Feld „timestamp“ ist erforderlich und sollte im UTC-Format vorliegen. Alle anderen Felder sind optional.
* „traceid“ und „spanid“ im Feld „mdc“ werden für die Ablaufverfolgungszwecke verwendet.
* Protokolliert jeden JSON-Datensatz in einer Zeile.

**Beispiel für Protokolldatensatz**

```log
{"timestamp":"2021-01-08T09:23:51.280Z","logger":"com.example.demo.HelloController","level":"ERROR","thread":"http-nio-1456-exec-4","mdc":{"traceId":"c84f8a897041f634","spanId":"c84f8a897041f634"},"stackTrace":"java.lang.RuntimeException: get an exception\r\n\tat com.example.demo.HelloController.throwEx(HelloController.java:54)\r\n\","message":"Got an exception","exceptionClass":"RuntimeException"}
```

## <a name="limitations"></a>Einschränkungen

Jede Zeile von JSON-Protokollen darf max. **16.000 Bytes** enthalten. Wenn die JSON-Ausgabe eines einzelnen Protokolldatensatzes diesen Grenzwert überschreitet, wird er in mehrere Zeilen unterteilt, und jede unformatierte Zeile wird in der `Log` Spalte erfasst, ohne strukturell analysiert zu werden.

Im Allgemeinen geschieht dies bei der Ausnahmeprotokollierung mit „deep stacktrace“, insbesondere wenn der [AppInsights-In-Process-Agent](./how-to-application-insights.md) aktiviert ist.  Wenden Sie Grenzwerteinstellungen auf die Stacktrace-Ausgabe an (siehe die folgenden Konfigurationsbeispiele), um sicherzustellen, dass die endgültige Ausgabe ordnungsgemäß analysiert wird.

## <a name="generate-schema-compliant-json-log"></a>Generieren eines schemakompatiblen JSON-Protokolls

Für Spring-Anwendungen können Sie das erwartete JSON-Protokollformat mithilfe allgemeiner [Protokollierungsframeworks](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-configuration) generieren, z. B. mit [logback](http://logback.qos.ch/) und [log4j2](https://logging.apache.org/log4j/2.x/).

### <a name="log-with-logback"></a>Protokollieren mit logback

Bei Verwendung von Spring Boot Starter wird standardmäßig logback verwendet. Verwenden Sie für logback-Apps [logstash-encoder](https://github.com/logstash/logstash-logback-encoder), um ein JSON-formatiertes Protokoll zu generieren. Diese Methode wird in Spring Boot, Version 2.1 und höher, unterstützt.

Die Prozedur:

1. Fügen Sie eine logstash-Abhängigkeit in Ihrer Datei `pom.xml` hinzu.

    ```xml
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>6.5</version>
    </dependency>
    ```

1. Aktualisieren Sie die Konfigurationsdatei `logback-spring.xml`, um das JSON-Format festzulegen.

    ```xml
    <configuration>
        <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
                <providers>
                    <timestamp>
                        <fieldName>timestamp</fieldName>
                        <timeZone>UTC</timeZone>
                    </timestamp>
                    <loggerName>
                        <fieldName>logger</fieldName>
                    </loggerName>
                    <logLevel>
                        <fieldName>level</fieldName>
                    </logLevel>
                    <threadName>
                        <fieldName>thread</fieldName>
                    </threadName>
                    <nestedField>
                        <fieldName>mdc</fieldName>
                        <providers>
                            <mdc />
                        </providers>
                    </nestedField>
                    <stackTrace>
                        <fieldName>stackTrace</fieldName>
                        <!-- maxLength - limit the length of the stack trace -->
                        <throwableConverter class="net.logstash.logback.stacktrace.ShortenedThrowableConverter">
                            <maxDepthPerThrowable>200</maxDepthPerThrowable>
                            <maxLength>14000</maxLength>
                            <rootCauseFirst>true</rootCauseFirst>
                        </throwableConverter>
                    </stackTrace>
                    <message />
                    <throwableClassName>
                        <fieldName>exceptionClass</fieldName>
                    </throwableClassName>
                </providers>
            </encoder>
        </appender>
        <root level="info">
            <appender-ref ref="stdout" />
        </root>
    </configuration>
    ```

1. Wenn Sie die Protokollierungskonfigurationsdatei mit einem `-spring`-Suffix wie `logback-spring.xml` verwenden, können Sie die Protokollierungskonfiguration basierend auf dem aktiven Spring-Profil festlegen.

    ```xml
    <configuration>
        <springProfile name="dev">
            <!-- JSON appender definitions for local development, in human readable format -->
            <include resource="org/springframework/boot/logging/logback/defaults.xml" />
            <include resource="org/springframework/boot/logging/logback/console-appender.xml" />
            <root level="info">
                <appender-ref ref="CONSOLE" />
            </root>
        </springProfile>

        <springProfile name="!dev">
            <!-- JSON appender configuration from previous step, used for staging / production -->
            ...
        </springProfile>
    </configuration>
    ```

    Führen Sie für die lokale Entwicklung die Spring Cloud-Anwendung mit dem JVM-Argument `-Dspring.profiles.active=dev` aus. Anschließend werden lesbare Protokolle anstelle von Zeilen im JSON-Format angezeigt.

### <a name="log-with-log4j2"></a>Protokollieren mit log4j2

Verwenden Sie für log4j2-Apps [json-template-layout](https://logging.apache.org/log4j/2.x/manual/json-template-layout.html), um das JSON-formatierte Protokoll zu generieren. Diese Methode wird in Spring Boot, Version 2.1 und höher, unterstützt.

Die Prozedur:

1. Schließen Sie `spring-boot-starter-logging` aus `spring-boot-starter` aus, und fügen Sie die Abhängigkeiten `spring-boot-starter-log4j2`, `log4j-layout-template-json` in der Datei `pom.xml` hinzu.

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <exclusions>
            <exclusion>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-logging</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-log4j2</artifactId>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-layout-template-json</artifactId>
        <version>2.14.0</version>
    </dependency>
    ```

2. Bereiten Sie eine JSON-Layoutvorlageandatei `jsonTemplate.json` in Ihrem Klassenpfad vor.

    ```json
    {
        "mdc": {
            "$resolver": "mdc"
        },
        "exceptionClass": {
            "$resolver": "exception",
            "field": "className"
        },
        "stackTrace": {
            "$resolver": "exception",
            "field": "stackTrace",
            "stringified": true
        },
        "message": {
            "$resolver": "message",
            "stringified": true
        },
        "thread": {
            "$resolver": "thread",
            "field": "name"
        },
        "timestamp": {
            "$resolver": "timestamp",
            "pattern": {
                "format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'",
                "timeZone": "UTC"
            }
        },
        "level": {
            "$resolver": "level",
            "field": "name"
        },
        "logger": {
            "$resolver": "logger",
            "field": "name"
        }
    }
    ```

3. Verwenden Sie diese JSON-Layoutvorlage in Ihrer Konfigurationsdatei `log4j2-spring.xml`.

    ```xml
    <configuration>
        <appenders>
            <console name="Console" target="SYSTEM_OUT">
                <!-- maxStringLength - limit the length of the stack trace -->
                <JsonTemplateLayout eventTemplateUri="classpath:jsonTemplate.json" maxStringLength="14000" />
            </console>
        </appenders>
        <loggers>
            <root level="info">
                <appender-ref ref="Console" />
            </root>
        </loggers>
    </configuration>
    ```

## <a name="analyze-the-logs-in-log-analytics"></a>Analysieren der Protokolle in Log Analytics

Nachdem Ihre Anwendung ordnungsgemäß eingerichtet wurde, wird das Anwendungskonsolenprotokoll an Log Analytics gestreamt. Die Struktur ermöglicht eine effiziente Abfrage in Log Analytics.

### <a name="check-log-structure-in-log-analytics"></a>Überprüfen der Protokollstruktur in Log Analytics

Gehen Sie dazu wie folgt vor:

1. Navigieren Sie zur Dienstübersichtsseite Ihrer Dienstinstanz.
2. Wählen Sie den Eintrag **Protokolle** im Abschnitt **Überwachung** aus.
3. Führen Sie die folgende Abfrage aus.

   ```query
   AppPlatformLogsforSpring
   | where TimeGenerated > ago(1h)
   | project AppTimestamp, Logger, CustomLevel, Thread, Message, ExceptionClass, StackTrace, TraceId, SpanId
   ```

4. Die Rückgabe von Anwendungsprotokollen wird in der folgenden Abbildung dargestellt:

   ![Anzeigen des JSON-Protokolls](media/spring-cloud-structured-app-log/json-log-query.png)

### <a name="show-log-entries-containing-errors"></a>Anzeigen von Protokolleinträgen mit Fehlern

Um Protokolleinträge mit einem Fehler zu überprüfen, führen Sie die folgende Abfrage aus:

```query
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h) and CustomLevel == "ERROR"
| project AppTimestamp, Logger, ExceptionClass, StackTrace, Message, AppName
| sort by AppTimestamp
```

Verwenden Sie diese Abfrage, um Fehler zu finden, oder ändern Sie die Abfragebedingungen, um eine bestimmte Ausnahmeklasse oder Fehlercodes zu ermitteln.

### <a name="show-log-entries-for-a-specific-traceid"></a>Anzeigen von Protokolleinträgen für eine bestimmte traceId

Zum Überprüfen von Protokolleinträgen für eine bestimmte Ablaufverfolgungs-ID „trace_id“ führen Sie die folgende Abfrage aus:

```query
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where TraceId == "trace_id"
| project AppTimestamp, Logger, TraceId, SpanId, StackTrace, Message, AppName
| sort by AppTimestamp
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Protokollabfrage finden Sie unter [Erste Schritte mit Protokollabfragen in Azure Monitor](../azure-monitor/logs/get-started-queries.md).
