---
title: Erfassen von Apache Spark-Anwendungsprotokollen und -Metriken mithilfe eines Azure Storage-Kontos
description: In diesem Artikel wird gezeigt, wie Sie die Diagnoseemittererweiterung von Synapse Spark verwenden, um Protokolle, Ereignisprotokolle und Metriken zu sammeln, und Sie erfahren, wie Sie die Grafana-Dashboards integrieren.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 08/31/2021
ms.openlocfilehash: de21862295e0a27937b8dc7e9552ce9530ec217d
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997873"
---
# <a name="collect-your-apache-spark-applications-logs-and-metrics-using-azure-storage-account"></a>Erfassen von Apache Spark-Anwendungsprotokollen und -Metriken mithilfe eines Azure Storage-Kontos

Die Diagnoseemittererweiterung von Synapse Apache Spark ist eine Bibliothek, die es der Apache Spark-Anwendung ermöglicht, Protokolle, Ereignisprotokolle und Metriken an ein einzelnes Ziel oder an mehrere Ziele wie Azure Log Analytics, Azure Storage und Azure Event Hubs auszugeben. 

In diesem Tutorial erfahren Sie, wie Sie die Diagnoseemittererweiterung von Synapse Apache Spark verwenden, um Protokolle, Ereignisprotokolle und Metriken von Apache Spark-Anwendungen an Ihr Azure-Speicherkonto auszugeben.

## <a name="collect-logs-and-metrics-to-storage-account"></a>Sammeln von Protokollen und Metriken im Speicherkonto

### <a name="step-1-create-a-storage-account"></a>Schritt 1: Erstellen eines Speicherkontos

Diagnoseprotokolle und Metriken können in bereits vorhandenen Azure Storage-Konten gesammelt werden. Sollten Sie über kein Konto verfügen, können Sie ein [Azure Blob Storage-Konto](../../storage/common/storage-account-create.md) oder ein [Speicherkonto für die Verwendung mit Azure Data Lake Storage Gen2](../../storage/blobs/create-data-lake-storage-account.md) erstellen.

### <a name="step-2-create-an-apache-spark-configuration-file"></a>Schritt 2: Erstellen einer Apache Spark-Konfigurationsdatei

Erstellen Sie eine Datei namens `diagnostic-emitter-azure-storage-conf.txt`, und kopieren Sie den folgenden Inhalt in die Datei. Alternativ können Sie eine [Beispielvorlagendatei](https://go.microsoft.com/fwlink/?linkid=2169375) für die Apache Spark-Poolkonfiguration herunterladen.

```
spark.synapse.diagnostic.emitters MyDestination1
spark.synapse.diagnostic.emitter.MyDestination1.type AzureStorage
spark.synapse.diagnostic.emitter.MyDestination1.categories Log,EventLog,Metrics
spark.synapse.diagnostic.emitter.MyDestination1.uri https://<my-blob-storage>.blob.core.windows.net/<container-name>/<folder-name>
spark.synapse.diagnostic.emitter.MyDestination1.auth AccessKey
spark.synapse.diagnostic.emitter.MyDestination1.secret <storage-access-key>
```

Geben Sie in der Konfigurationsdatei die folgenden Parameter an: `<my-blob-storage>`, `<container-name>`, `<folder-name>`, `<storage-access-key>`.
Eine ausführlichere Beschreibung der Parameter finden Sie unter [Azure Storage-Konfigurationen](#available-configurations).

### <a name="step-3-upload-the-apache-spark-configuration-file-to-spark-pool"></a>Schritt 3: Hochladen der Apache Spark-Konfigurationsdatei in den Spark-Pool

1. Navigieren Sie in Synapse Studio über **Manage > Apache Spark pools** („Verwalten“ > „Apache Spark-Pools“) zu Ihrem Apache Spark-Pool.
1. Klicken Sie rechts neben Ihrem Apache Spark-Pool auf die Schaltfläche **...** , und wählen Sie **Apache Spark configuration** (Apache Spark-Konfiguration) aus.
1. Klicken Sie auf **Upload** (Hochladen), und wählen Sie die TXT-Konfigurationsdatei aus. Klicken Sie anschließend auf **Apply** (Anwenden).

### <a name="step-4-view-the-logs-files-in-azure-storage-account"></a>Schritt 4: Anzeigen der Protokolldateien im Azure-Speicherkonto


Nachdem Sie einen Auftrag an den konfigurierten Apache Spark-Pool übermittelt haben, sollten die Protokolle und Metrikdateien im Zielspeicherkonto angezeigt werden.
Die Protokolle werden für verschiedene Anwendungen nach dem Muster `<workspaceName>.<sparkPoolName>.<livySessionId>` in entsprechenden Pfaden platziert.
Alle Protokolldateien liegen im JSON-Zeilenformat – auch „newline-delimited JSON“ (durch Zeilenvorschübe getrennter JSON-Code) oder kurz „ndjson“ genannt – vor, was für die Datenverarbeitung praktisch ist.

## <a name="available-configurations"></a>Verfügbare Konfigurationen

| Konfiguration | BESCHREIBUNG |
| --- | --- |
| `spark.synapse.diagnostic.emitters`                                         | Erforderlich. Kommagetrennte Liste der Zielnamen von Diagnoseemittern. Zum Beispiel, `MyDest1,MyDest2` |
| `spark.synapse.diagnostic.emitter.<destination>.type`                       | Erforderlich. Integrierter Zieltyp. Um das Azure-Speicherziel zu aktivieren, muss `AzureStorage` in dieses Feld eingeschlossen werden. |
| `spark.synapse.diagnostic.emitter.<destination>.categories`                 | Optional. Kommagetrennte Liste der ausgewählten Protokollkategorien. Verfügbare Werte: `DriverLog`, `ExecutorLog`, `EventLog`, `Metrics`. Ist diese Option nicht festgelegt, werden standardmäßig **alle** Kategorien verwendet. |
| `spark.synapse.diagnostic.emitter.<destination>.auth`                       | Erforderlich. `AccessKey` für die Verwendung der Speicherkontoautorisierung per [Zugriffsschlüssel](../../storage/common/storage-account-keys-manage.md). `SAS` für die Autorisierung mit [Shared Access Signatures (SAS)](../../storage/common/storage-sas-overview.md). |
| `spark.synapse.diagnostic.emitter.<destination>.uri`                        | Erforderlich. Der Ordner-URI des Zielblobcontainers. Muss dem folgenden Muster entsprechen: `https://<my-blob-storage>.blob.core.windows.net/<container-name>/<folder-name>`. |
| `spark.synapse.diagnostic.emitter.<destination>.secret`                     | Optional. Der Inhalt des Geheimnisses: Zugriffsschlüssel (AccessKey) oder SAS. |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault`            | Erforderlich, wenn `.secret` nicht angegeben wird. Der Name der [Azure Key Vault-Instanz](../../key-vault/general/overview.md), in der das Geheimnis (Zugriffsschlüssel oder SAS) gespeichert ist. |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault.secretName` | Erforderlich, wenn `.secret.keyVault` angegeben wird. Der Name des Azure Key Vault-Geheimnisses, in dem das Geheimnis (Zugriffsschlüssel oder SAS) gespeichert ist. |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault.linkedService` | Optional. Der Name des mit Azure Key Vault verknüpften Diensts. Bei Aktivierung in der Synapse-Pipeline ist dieser Name erforderlich, um das Geheimnis aus AKV abzurufen. (Stellen Sie sicher, dass MSI über Leseberechtigung für AKV verfügt.) |
| `spark.synapse.diagnostic.emitter.<destination>.filter.eventName.match`     | Optional. Kommagetrennte Liste mit Spark-Ereignisnamen, um anzugeben, welche Ereignisse gesammelt werden sollen. Beispiel: `SparkListenerApplicationStart,SparkListenerApplicationEnd` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.loggerName.match`    | Optional. Kommagetrennte Liste mit log4j-Protokollierungsnamen, um anzugeben, welche Protokolle gesammelt werden sollen. Beispiel: `org.apache.spark.SparkContext,org.example.Logger` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.metricName.match`    | Optional. Kommagetrennte Liste mit Spark-Metriknamensuffixen, um anzugeben, welche Metriken gesammelt werden sollen. Beispiel: `jvm.heap.used` |

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


1. Navigieren Sie zu **Synapse Studio > Manage > Managed private endpoints** („Synapse Studio“ > „Verwalten“ > „Verwaltete private Endpunkte“). Klicken Sie auf die Schaltfläche **New** (Neu), und wählen Sie **Azure Blob Storage** oder **Azure Data Lake Storage Gen2** und anschließend **Continue** (Weiter) aus.
   > [!div class="mx-imgBorder"]
   > ![Erstellen eines verwalteten privaten Endpunkts 1](./media/azure-synapse-diagnostic-emitters-azure-storage/create-private-endpoint-1.png)
2. Wählen Sie unter **Storage account name** (Speicherkontoname) Ihr Azure Storage-Konto aus, und klicken Sie auf die Schaltfläche **Create** (Erstellen).
   > [!div class="mx-imgBorder"]
   > ![Erstellen eines verwalteten privaten Endpunkts 2](./media/azure-synapse-diagnostic-emitters-azure-storage/create-private-endpoint-2.png)
3. Die Bereitstellung des privaten Endpunkts dauert einige Minuten.
4. Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto. Wählen Sie unter **Netzwerk** > **Private Endpunktverbindungen** die bereitgestellte Verbindung und anschließend **Genehmigen** aus.


