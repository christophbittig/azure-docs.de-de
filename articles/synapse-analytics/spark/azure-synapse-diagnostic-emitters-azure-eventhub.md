---
title: Sammeln Ihrer Apache Spark-Anwendungsprotokolle und -Metriken mithilfe von Azure Event Hubs
description: In diesem Tutorial erfahren Sie, wie Sie die Diagnoseemittererweiterung von Synapse Apache Spark verwenden, um Protokolle, Ereignisprotokolle und Metriken von Apache Spark-Anwendungen an Ihre Azure Event Hubs-Instanz auszugeben.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 08/31/2021
ms.openlocfilehash: 2370e3895cc70a4303c9d91d300b47b32913ac7a
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131996317"
---
# <a name="collect-your-apache-spark-applications-logs-and-metrics-using-azure-event-hubs"></a>Sammeln Ihrer Apache Spark-Anwendungsprotokolle und -Metriken mithilfe von Azure Event Hubs 

Die Diagnoseemittererweiterung von Synapse Apache Spark ist eine Bibliothek, die es der Apache Spark-Anwendung ermöglicht, Protokolle, Ereignisprotokolle und Metriken an ein einzelnes Ziel oder an mehrere Ziele wie Azure Log Analytics, Azure Storage und Azure Event Hubs auszugeben. 

In diesem Tutorial erfahren Sie, wie Sie die Diagnoseemittererweiterung von Synapse Apache Spark verwenden, um Protokolle, Ereignisprotokolle und Metriken von Apache Spark-Anwendungen an Ihre Azure Event Hubs-Instanz auszugeben.

## <a name="collect-logs-and-metrics-to-azure-event-hubs"></a>Sammeln von Protokollen und Metriken in Azure Event Hubs

### <a name="step-1-create-an-azure-event-hub-instance"></a>Schritt 1: Erstellen einer Azure Event Hubs-Instanz

Zum Sammeln von Diagnoseprotokollen und Metriken für Azure Event Hubs kann eine bereits vorhandene Azure Event Hubs-Instanz verwendet werden.
Sollten Sie über keine verfügen, können Sie [einen Event Hub erstellen](../../event-hubs/event-hubs-create.md).

### <a name="step-2-create-an-apache-spark-configuration-file"></a>Schritt 2: Erstellen einer Apache Spark-Konfigurationsdatei

Erstellen Sie eine Datei namens `diagnostic-emitter-azure-event-hub-conf.txt`, und kopieren Sie den folgenden Inhalt in die Datei. Alternativ können Sie eine [Beispielvorlagendatei](https://go.microsoft.com/fwlink/?linkid=2169375) für die Apache Spark-Poolkonfiguration herunterladen.

```
spark.synapse.diagnostic.emitters MyDestination1
spark.synapse.diagnostic.emitter.MyDestination1.type AzureEventHub
spark.synapse.diagnostic.emitter.MyDestination1.categories Log,EventLog,Metrics
spark.synapse.diagnostic.emitter.MyDestination1.secret <connection-string>
```

Geben Sie in der Konfigurationsdatei den folgenden Parameter an: `<connection-string>`.
Eine ausführlichere Beschreibung der Parameter finden Sie unter [Azure EventHub-Konfigurationen](#available-configurations).

### <a name="step-3-upload-the-apache-spark-configuration-file-to-apache-spark-pool"></a>Schritt 3: Hochladen der Apache Spark-Konfigurationsdatei in den Apache Spark-Pool

1. Navigieren Sie in Synapse Studio über **Manage > Apache Spark pools** („Verwalten“ > „Apache Spark-Pools“) zu Ihrem Apache Spark-Pool.
2. Klicken Sie rechts neben Ihrem Apache Spark-Pool auf die Schaltfläche **...** , und wählen Sie **Apache Spark configuration** (Apache Spark-Konfiguration) aus.
3. Klicken Sie auf **Upload** (Hochladen), wählen Sie die TXT-Konfigurationsdatei aus, und klicken Sie anschließend auf **Apply** (Anwenden).

## <a name="available-configurations"></a>Verfügbare Konfigurationen

| Konfiguration                                                               | BESCHREIBUNG                                                                                                                                                                                          |
| --------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `spark.synapse.diagnostic.emitters`                                         | Erforderlich. Kommagetrennte Liste der Zielnamen von Diagnoseemittern.                                                                                                                              |
| `spark.synapse.diagnostic.emitter.<destination>.type`                       | Erforderlich. Integrierter Zieltyp. Zum Aktivieren des Azure Event Hubs-Ziels muss der Wert `AzureEventHub` lauten.                                                                                    |
| `spark.synapse.diagnostic.emitter.<destination>.categories`                 | Optional. Kommagetrennte Liste der ausgewählten Protokollkategorien. Verfügbare Werte: `DriverLog`, `ExecutorLog`, `EventLog`, `Metrics`. Ist diese Option nicht festgelegt, werden standardmäßig **alle** Kategorien verwendet.              |
| `spark.synapse.diagnostic.emitter.<destination>.secret`                     | Optional. Die Verbindungszeichenfolge der Azure Eventhub-Instanz. Dieses Feld muss dem folgenden Muster entsprechen: `Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>;EntityPath=<PathName>`. |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault`            | Erforderlich, wenn `.secret` nicht angegeben wird. Der Name der [Azure Key Vault-Instanz](../../key-vault/general/overview.md), in der das Geheimnis (Verbindungszeichenfolge) gespeichert ist.                                                                  |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault.secretName` | Erforderlich, wenn `.secret.keyVault` angegeben wird. Der Name des Azure Key Vault-Geheimnisses, in dem das Geheimnis (Verbindungszeichenfolge) gespeichert ist.                                                                         |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault.linkedService` | Optional. Der Name des mit Azure Key Vault verknüpften Diensts. Bei Aktivierung in der Synapse-Pipeline ist dieser Name erforderlich, um das Geheimnis aus AKV abzurufen. (Stellen Sie sicher, dass MSI über Leseberechtigung für AKV verfügt.) |
| `spark.synapse.diagnostic.emitter.<destination>.filter.eventName.match`     | Optional. Kommagetrennte Liste mit Spark-Ereignisnamen, um anzugeben, welche Ereignisse gesammelt werden sollen. Beispiel: `SparkListenerApplicationStart,SparkListenerApplicationEnd` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.loggerName.match`    | Optional. Kommagetrennte Liste mit log4j-Protokollierungsnamen, um anzugeben, welche Protokolle gesammelt werden sollen. Beispiel: `org.apache.spark.SparkContext,org.example.Logger` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.metricName.match`    | Optional. Kommagetrennte Liste mit Spark-Metriknamensuffixen, um anzugeben, welche Metriken gesammelt werden sollen. Beispiel: `jvm.heap.used` |


> [!NOTE]
>
> Die Verbindungszeichenfolge der Azure Eventhub-Instanz muss immer den Entitätspfad (`EntityPath`) enthalten. Dies ist der Name der Azure Event Hubs-Instanz.

## <a name="log-data-sample"></a>Beispiel für Protokolldaten

Hier sehen Sie einen exemplarischen Protokolldatensatz im JSON-Format:

```json
{
    "timestamp": "2021-01-02T12:34:56.789Z",
    "category": "Log|EventLog|Metrics",
    "workspaceName": "<my-workspace-name>",
    "sparkPool": "<spark-pool-name>",
    "livyId": "<livy-session-id>",
    "applicationId": "<application-id>",
    "applicationName": "<application-name>",
    "executorId": "<driver-or-executor-id>",
    "properties": {
        // The message properties of logs, events and metrics.
        "timestamp": "2021-01-02T12:34:56.789Z",
        "message": "Registering signal handler for TERM",
        "logger_name": "org.apache.spark.util.SignalUtils",
        "level": "INFO",
        "thread_name": "main"
        // ...
    }
}
```

## <a name="synapse-workspace-with-data-exfiltration-protection-enabled"></a>Synapse-Arbeitsbereich mit aktiviertem Schutz vor Datenexfiltration

Azure Synapse Analytics-Arbeitsbereiche unterstützen die Aktivierung des Schutzes vor Datenexfiltration für Arbeitsbereiche. Mit Exfiltrationsschutz können die Protokolle und Metriken nicht direkt an die Zielendpunkte gesendet werden. In diesem Szenario können Sie entsprechende [verwaltete private Endpunkte](../../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md) für verschiedene Zielendpunkte erstellen oder [IP-Firewallregeln erstellen](../../synapse-analytics/security/synapse-workspace-ip-firewall.md).




