---
title: Verwenden von Logback zum Schreiben von Protokollen in benutzerdefinierten persistenten Speicher in Azure Spring Cloud | Microsoft-Dokumentation
description: Verwenden von Logback zum Schreiben von Protokollen in benutzerdefinierten persistenten Speicher in Azure Spring Cloud
author: karlerickson
ms.author: xuycao
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: devx-track-java
ms.openlocfilehash: 558ca29f8faa03857d4debe760c4859ab43b17e5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404337"
---
# <a name="how-to-use-logback-to-write-logs-to-custom-persistent-storage"></a>Verwenden von Logback zum Schreiben von Protokollen in benutzerdefinierten persistenten Speicher

**Dieser Artikel gilt für:** ✔️ Java

In diesem Artikel erfahren Sie, wie Sie Logback laden und Protokolle in benutzerdefinierten persistenten Speicher in Azure Spring Cloud schreiben.

> [!NOTE]
> Wenn eine Datei im Klassenpfad der Anwendung einen der folgenden Namen hat, wird sie von Spring Boot automatisch über die Standardkonfiguration für Logback geladen:
> - *logback-spring.xml*
> - *logback.xml*
> - *logback-spring.groovy*
> - *logback.groovy*

## <a name="prerequisites"></a>Voraussetzungen

* Eine vorhandene Speicherressource, die an eine Azure Spring Cloud-Instanz gebunden ist. Informationen zum Binden einer Speicherressource finden Sie unter [Aktivieren des eigenen persistenten Speichers in Azure Spring Cloud](./how-to-custom-persistent-storage.md).
* Die Logback-Abhängigkeit, die in Ihrer Anwendung enthalten ist. Weitere Informationen zu Logback finden Sie unter [A Guide To Logback](https://www.baeldung.com/logback) (Leitfaden für Logback).
* Die [Azure Spring Cloud-Erweiterung](/cli/azure/azure-cli-extensions-overview) für die Azure-Befehlszeilenschnittstelle (Azure CLI)

## <a name="edit-the-logback-configuration-to-write-logs-into-a-specific-path"></a>Bearbeiten der Logback-Konfiguration zum Schreiben von Protokollen in einen bestimmten Pfad

Sie können den Pfad zum Schreiben von Protokollen mithilfe der Beispieldatei „logback-spring.xml“ festlegen.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <appender name="Console"
              class="ch.qos.logback.core.ConsoleAppender">
        <!-- please feel free to customize the log layout -->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <Pattern>
                %black(%d{ISO8601}) %highlight(%-5level) [%blue(%t)] %yellow(%C{1.}): %msg%n%throwable
            </Pattern>
        </layout>
    </appender>

    <appender name="RollingFile"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 'LOGS' here is a value to be read from the application's environment variable -->
        <file>${LOGS}/spring-boot-logger.log</file>
        <!-- please feel free to customize the log layout pattern -->
        <encoder
                class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <Pattern>%d %p %C{1.} [%t] %m%n</Pattern>
        </encoder>

        <rollingPolicy
                class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- rollover daily and when the file reaches 10 MegaBytes -->
            <fileNamePattern>${LOGS}/archived/spring-boot-logger-%d{yyyy-MM-dd}.%i.log
            </fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy
                    class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
    </appender>

    <!-- LOG everything at the INFO level -->
    <root level="info">
        <appender-ref ref="RollingFile" />
        <appender-ref ref="Console" />
    </root>

    <!-- LOG "com.baeldung*" at the TRACE level -->
    <logger name="com.baeldung" level="trace" additivity="false">
        <appender-ref ref="RollingFile" />
        <appender-ref ref="Console" />
    </logger>

</configuration>
```

Im vorherigen Beispiel gibt es zwei Platzhalter namens `{LOGS}` im Pfad zum Schreiben der Anwendungsprotokolle. Der Umgebungsvariablen `LOGS` muss ein Wert zugewiesen werden, damit das Protokoll sowohl in die Konsole als auch in Ihren persistenten Speicher geschrieben werden kann. 

## <a name="use-the-azure-cli-to-create-and-deploy-logback-to-write-logs-to-persistent-storage"></a>Verwenden Sie die Azure CLI, um Logback zu erstellen und bereitzustellen, damit Protokolle in persistenten Speicher geschrieben werden.

1. Verwenden Sie den folgenden Befehl, um eine Anwendung in Azure Spring Cloud mit aktivierten persistenten Speicher und festgelegter Umgebungsvariablen zu erstellen:

   ```azurecli
   az spring-cloud app create \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name> \
        --persistent-storage <path-to-json-file> \
        --env LOGS=/byos/logs
   ```

    Sie müssen Ihren persistenten Speicher unter demselben Pfad einbinden, den Sie in Ihrer Anwendung verwenden. In diesem Beispiel sehen sie die JSON-Datei, die im Create-Befehl an den Parameter `--persistent-storage` übergeben wird. Stellen Sie sicher, dass Sie den gleichen Wert für die Umgebungsvariable im obigen CLI-Befehl und in der folgenden `mountPath`-Eigenschaft übergeben: 

    ```json
    {
        "customPersistentDisks": [
            {
                "storageName": "<Storage-Resource-Name>",
                "customPersistentDiskProperties": {
                    "type": "AzureFileVolume",
                    "shareName": "<Azure-File-Share-Name>",
                    "mountPath": "/byos/logs",
                    "readOnly": false
                }
            }
        ]
    }
    ```
  
1. Verwenden Sie den folgenden Befehl, um Ihre Anwendung bereitzustellen:

   ```azurecli
   az spring-cloud app deploy \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name> \
        --jar-path <path-to-jar-file>
   ```

1. Verwenden Sie den folgenden Befehl, um das Konsolenprotokoll Ihrer Anwendung zu überprüfen:

   ```azurecli
   az spring-cloud app logs \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name>
   ```

    Wechseln Sie zu der Azure Storage-Kontoressource, die Sie gebunden haben, und suchen Sie die Azure-Dateifreigabe, die als persistenter Speicher angefügt wurde. In diesem Beispiel werden die Protokolle in die Datei *spring-boot-logger.log* im Basispfad Ihrer Azure-Dateifreigabe geschrieben. Alle rotierten Protokolldateien werden im Ordner */archived* in Ihrer Azure-Dateifreigabe gespeichert.

## <a name="next-steps"></a>Nächste Schritte

* [Strukturiertes Anwendungsprotokoll für Azure Spring Cloud](./structured-app-log.md)
* [Analysieren von Protokollen und Metriken mit Diagnoseeinstellungen](./diagnostic-services.md)