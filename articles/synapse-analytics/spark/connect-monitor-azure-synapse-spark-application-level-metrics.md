---
title: Sammeln von Apache Spark-Anwendungsmetriken mithilfe von APIs
description: 'Tutorial: Erfahren Sie, wie Sie Ihren vorhandenen lokalen Prometheus-Server über den Synapse-Prometheus-Connector in einen Azure Synapse-Arbeitsbereich integrieren, um Azure Spark-Anwendungsmetriken zu erhalten.'
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: b2810d97651c6819996c79ce554fa2feee2a6c65
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539639"
---
#  <a name="collect-apache-spark-applications-metrics-using-apis"></a>Sammeln von Apache Spark-Anwendungsmetriken mithilfe von APIs

## <a name="overview"></a>Übersicht

In diesem Tutorial erfahren Sie, wie Sie Ihren vorhandenen lokalen Prometheus-Server über den Synapse-Prometheus-Connector in einen Azure Synapse-Arbeitsbereich integrieren, um Azure Spark-Anwendungsmetriken in Quasi-Echtzeit zu erhalten. 

Außerdem werden in diesem Tutorial die Azure Synapse-REST-Metrik-APIs vorgestellt. Sie können Daten zu Apache Spark-Anwendungsmetriken über die REST-APIs abrufen, um ein eigenes Überwachungs- und Diagnosetoolkit zu erstellen oder in Ihre Überwachungssysteme zu integrieren.

## <a name="use-azure-synapse-prometheus-connector-for-your-on-premises-prometheus-servers"></a>Verwenden des Azure Synapse-Prometheus-Connectors für Ihre lokalen Prometheus-Server

Der [Azure Synapse-Prometheus-Connector](https://github.com/microsoft/azure-synapse-spark-metrics) ist ein Open-Source-Projekt. Der Synapse-Prometheus-Connector verwendet eine dateibasierte Dienstermittlungsmethode, um Folgendes zu ermöglichen:
 - Authentifizieren beim Synapse-Arbeitsbereich über einen AAD-Dienst Prinzipal
 - Abrufen einer Liste von Apache Spark-Anwendungen im Arbeitsbereich 
 - Abrufen von Apache Spark-Anwendungsmetriken über die dateibasierte Prometheus-Konfiguration 

### <a name="1-prerequisite"></a>1. Voraussetzung

Ein Prometheus-Server muss auf einer Linux-VM bereitgestellt werden.

### <a name="2-create-a-service-principal"></a>2. Erstellen eines Dienstprinzipals

Wenn Sie den Azure Synapse-Prometheus-Connector auf Ihrem lokalen Prometheus-Server verwenden möchten, sollten Sie die folgenden Schritte ausführen, um einen Dienstprinzipal zu erstellen.

#### <a name="21-create-a-service-principal"></a>2.1 Erstellen eines Dienstprinzipals

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

Das Ergebnis sollte wie folgt aussehen:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Notieren Sie sich die appId, das Kennwort und die Mandanten-ID.

#### <a name="22-add-corresponding-permissions-to-the-service-principal-created-in-the-above-step"></a>2.2 Hinzufügen entsprechender Berechtigungen für den im obigen Schritt erstellten Dienstprinzipal

![Screenshot zum Erteilen der Berechtigung, SRBAC](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)

1. Melden Sie sich als Synapse-Administrator beim [Azure Synapse Analytics-Arbeitsbereich](https://web.azuresynapse.net/) an.

2. Wählen Sie in Synapse Studio im linken Bereich **Verwalten > Zugriffssteuerung** aus.

3. Klicken Sie links oben auf die Schaltfläche „Hinzufügen“, um **eine Rollenzuweisung hinzuzufügen**.

4. Wählen Sie als Bereich **Arbeitsbereich** aus.

5. Wählen Sie als Rolle die Option **Operator von Synapse-Computeressourcen** aus.

6. Geben Sie unter „Benutzer auswählen“ Ihren **<Dienstprinzipalnamen>** ein, und klicken Sie auf Ihren Dienstprinzipal.

7. Klicken Sie auf **Anwenden**. (Warten Sie drei Minuten, bis die Berechtigung in Kraft tritt.)


### <a name="3-download-the-azure-synapse-prometheus-connector"></a>3. Herunterladen des Azure Synapse-Prometheus-Connectors

Verwenden Sie die Befehle zum Installieren des Azure Synapse-Prometheus-Connectors.

```bash
git clone https://github.com/microsoft/azure-synapse-spark-metrics.git
cd ./azure-synapse-spark-metrics/synapse-prometheus-connector/src
python pip install -r requirements.txt
```

### <a name="4-create-a-config-file-for-azure-synapse-workspaces"></a>4. Erstellen einer Konfigurationsdatei für Azure Synapse-Arbeitsbereiche

Erstellen Sie im Konfigurationsordner eine Datei „config.yaml“, und füllen Sie die folgenden Felder aus: „workspace_name“, „tenant_id“, „service_principal_name“ und „service_principal_password“.
Sie können in der YAML-Konfiguration mehrere Arbeitsbereiche hinzufügen.

```yaml
workspaces:
  - workspace_name: <your_workspace_name>
    tenant_id: <tenant_id>
    service_principal_name: <service_principal_app_id>
    service_principal_password: "<service_principal_password>"
```

### <a name="5-update-the-prometheus-config"></a>5. Aktualisieren der Prometheus-Konfiguration

Fügen Sie im Prometheus-Abschnitt „scrape_config“ den folgenden Konfigurationsabschnitt hinzu, und ersetzen Sie die <your_workspace_name> durch den Namen Ihres Arbeitsbereichs und <path_to_synapse_connector> durch den geklonten Ordner „synapse-prometheus-connector“.

```yaml
- job_name: synapse-prometheus-connector
  static_configs:
  - labels:
      __metrics_path__: /metrics
      __scheme__: http
    targets:
    - localhost:8000
- job_name: synapse-workspace-<your_workspace_name>
  bearer_token_file: <path_to_synapse_connector>/output/workspace/<your_workspace_name>/bearer_token
  file_sd_configs:
  - files:
    - <path_to_synapse_connector>/output/workspace/<your_workspace_name>/application_discovery.json
    refresh_interval: 10s
  metric_relabel_configs:
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_application_[0-9]+_[0-9]+_(.+)
    replacement: spark_$1
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_(.+)
    replacement: spark_$1
```


### <a name="6-start-the-connector-in-the-prometheus-server-vm"></a>6. Starten des Connectors auf der Prometheus-Server-VM

Starten Sie auf der Prometheus-Server-VM wie folgt einen Connectorserver.

```
python main.py
```

Warten Sie einige Sekunden, bis der Connector betriebsbereit ist. Auf der Seite der Prometheus-Dienstermittlung wird „synapse-prometheus-connector“ angezeigt.

## <a name="use-azure-synapse-prometheus-or-rest-metrics-apis-to-collect-metrics-data"></a>Verwenden der Azure Synapse-Prometheus-API oder der REST-Metrik-APIs zum Erfassen von Metrikdaten

### <a name="1-authentication"></a>1. Authentifizierung
Sie können den Flow für Clientanmeldeinformationen verwenden, um ein Zugriffstoken zu erhalten. Für den Zugriff auf die Metrik-API sollten Sie ein Azure AD-Zugriffstoken für den Dienstprinzipal abrufen, das über eine geeignete Berechtigung für den Zugriff auf die APIs verfügt.

| Parameter     | Erforderlich | BESCHREIBUNG                                                                                                   |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------- |
| tenant_id     | True     | Die Mandanten-ID Ihres Azure-Dienstprinzipals (Anwendung)                                                          |
| grant_type    | True     | Gibt den angeforderten Gewährungstyp an. In einem Fluss zur Gewährung von Clientanmeldeinformationen muss der Wert client_credentials lauten. |
| client_id     | True     | Die Anwendungs-ID (Dienstprinzipal) der Anwendung, die Sie im Azure-Portal oder über die Azure CLI registriert haben        |
| client_secret | True     | Das für die Anwendung (Dienstprinzipal) generierte Geheimnis                                                  |
| resource      | True     | Der Synapse-Ressourcen-URI, normalerweise https://dev.azuresynapse.net                                                  |

```bash
curl -X GET -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'grant_type=client_credentials&client_id=<service_principal_app_id>&resource=<azure_synapse_resource_id>&client_secret=<service_principal_secret>' \
  https://login.microsoftonline.com/<tenant_id>/oauth2/token
```

Die Antwort sieht wie folgt aus:

```json
{
  "token_type": "Bearer",
  "expires_in": "599",
  "ext_expires_in": "599",
  "expires_on": "1575500666",
  "not_before": "1575499766",
  "resource": "2ff8...f879c1d",
  "access_token": "ABC0eXAiOiJKV1Q......un_f1mSgCHlA"
}
```

### <a name="2-list-running-applications-in-the-azure-synapse-workspace"></a>2. Auflisten von Anwendungsausführungen im Azure Synapse-Arbeitsbereich

Um eine Liste der Apache Spark-Anwendungen für einen Synapse-Arbeitsbereich zu erhalten, befolgen Sie die Informationen in diesem Dokument: [Überwachung: Abrufen einer Spark-Auftragsliste](/rest/api/synapse/data-plane/monitoring/getsparkjoblist).


### <a name="3-collect-apache-spark-application-metrics-with-the-prometheus-or-rest-apis"></a>3. Erfassen von Apache Spark-Anwendungsmetriken mit der Prometheus-API oder den REST-APIs


#### <a name="collect-apache-spark-application-metrics-with-the-prometheus-api"></a>Erfassen von Apache Spark-Anwendungsmetriken mit der Prometheus-API

Erfassen Sie die aktuellen Metriken der angegebenen Apache Spark-Anwendung mit der Prometheus-API:

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/metrics/executors/prometheus?format=html
```

| Parameter          | Erforderlich | BESCHREIBUNG                                                                                 |
| ------------------ | -------- | --------------------------------------------------------------------------------------------|
| endpoint           | True     | Der Endpunkt zur Arbeitsbereichsentwicklung, etwa `https://myworkspace.dev.azuresynapse.net.` |
| livyApiVersion     | True     | Gültige API-Version für die Anforderung, zurzeit „2019-11-01-preview“                      |
| sparkPoolName      | True     | Name des Spark-Pools                                                                     |
| sessionID          | True     | Bezeichner für die Sitzung                                                                 |
| sparkApplicationId | True     | Spark-Anwendungs-ID                                                                        |

Beispielanforderung: 

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/metrics/executors/prometheus?format=html
```

Beispielantwort:

Statuscode: Antwort 200 sieht wie folgt aus:

```
metrics_executor_rddBlocks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_memoryUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 74992
metrics_executor_diskUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_totalCores{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_maxTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_activeTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 1
metrics_executor_failedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_completedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 2
...

```

#### <a name="collect-apache-spark-application-metrics-with-the-rest-api"></a>Erfassen von Apache Spark-Anwendungsmetriken mit der REST-API

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/executors
```

| Parameter          | Erforderlich | BESCHREIBUNG                                                                                 |
| ------------------ | -------- | --------------------------------------------------------------------------------------------|
| endpoint           | True     | Der Endpunkt zur Arbeitsbereichsentwicklung, etwa `https://myworkspace.dev.azuresynapse.net.` |
| livyApiVersion     | True     | Gültige API-Version für die Anforderung, zurzeit „2019-11-01-preview“                      |
| sparkPoolName      | True     | Name des Spark-Pools                                                                     |
| sessionID          | True     | Bezeichner für die Sitzung                                                                 |
| sparkApplicationId | True     | Spark-Anwendungs-ID                                                                        |

Beispiel für eine Anforderung

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/executors
```

Beispielantwort für Statuscode: 200

```json
[
    {
        "id": "driver",
        "hostPort": "f98b8fc2aea84e9095bf2616208eb672007bde57624:45889",
        "isActive": true,
        "rddBlocks": 0,
        "memoryUsed": 75014,
        "diskUsed": 0,
        "totalCores": 0,
        "maxTasks": 0,
        "activeTasks": 0,
        "failedTasks": 0,
        "completedTasks": 0,
        "totalTasks": 0,
        "totalDuration": 0,
        "totalGCTime": 0,
        "totalInputBytes": 0,
        "totalShuffleRead": 0,
        "totalShuffleWrite": 0,
        "isBlacklisted": false,
        "maxMemory": 15845975654,
        "addTime": "2020-11-16T06:55:06.718GMT",
        "executorLogs": {
            "stdout": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stdout?start=-4096",
            "stderr": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stderr?start=-4096"
        },
        "memoryMetrics": {
            "usedOnHeapStorageMemory": 75014,
            "usedOffHeapStorageMemory": 0,
            "totalOnHeapStorageMemory": 15845975654,
            "totalOffHeapStorageMemory": 0
        },
        "blacklistedInStages": []
    },
    // ...
]
```


### <a name="4-build-your-own-diagnosis-and-monitoring-tools"></a>4. Erstellen Ihrer eigenen Diagnose- und Überwachungstools

Die Prometheus-API und die Rest-APIs stellen umfangreiche Metrikdaten zur Ausführung von Apache Spark-Anwendungen bereit. Sie können die anwendungsbezogenen Metrikdaten über die Prometheus-API und die REST-APIs erfassen. Zudem können Sie eigene Diagnose- und Überwachungstools erstellen, die für Ihre Anforderungen optimiert sind.
