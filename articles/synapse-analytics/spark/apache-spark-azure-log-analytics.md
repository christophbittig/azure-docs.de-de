---
title: Erfassen und Visualisieren von Metriken und Protokollen mithilfe von Log Analytics (Vorschau)
description: Hier erfahren Sie, wie Sie den Synapse Studio-Connector aktivieren, um die Anwendungsmetriken und -protokolle von Apache Spark zu erfassen und an Ihren Log Analytics-Arbeitsbereich zu senden.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 03/25/2021
ms.custom: references_regions
ms.openlocfilehash: d5052b7a36f3eacb96097b8d9268579ec56ea222
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768124"
---
# <a name="tutorial-use-log-analytics-to-collect-and-visualize-metrics-and-logs-preview"></a>Tutorial: Erfassen und Visualisieren von Metriken und Protokollen mithilfe von Log Analytics (Vorschau)

In diesem Tutorial erfahren Sie, wie Sie den in Log Analytics integrierten Synapse Studio-Connector aktivieren. Anschließend können Sie Anwendungsmetriken und -protokolle von Apache Spark erfassen und an Ihren [Log Analytics-Arbeitsbereich](../../azure-monitor/logs/quick-create-workspace.md) senden. Abschließend können Sie eine Azure Monitor-Arbeitsmappe verwenden, um die Metriken und Protokolle zu visualisieren.

## <a name="configure-workspace-information"></a>Konfigurieren von Arbeitsbereichsinformationen

Führen Sie die folgenden Schritte aus, um die erforderlichen Informationen in Synapse Studio zu konfigurieren.

### <a name="step-1-create-a-log-analytics-workspace"></a>Schritt 1: Erstellen eines Log Analytics-Arbeitsbereichs

Informationen zum Erstellen dieses Arbeitsbereichs finden Sie in den folgenden Ressourcen:
- [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../../azure-monitor/logs/quick-create-workspace.md)
- [Beispiele für Resource Manager-Vorlagen für Log Analytics-Arbeitsbereiche in Azure Monitor](../../azure-monitor/logs/quick-create-workspace-cli.md)
- [Erstellen und Konfigurieren eines Log Analytics-Arbeitsbereichs in Azure Monitor mithilfe von PowerShell](../../azure-monitor/logs/powershell-workspace-configuration.md)

### <a name="step-2-prepare-a-spark-configuration-file"></a>Schritt 2: Vorbereiten einer Spark-Konfigurationsdatei

Verwenden Sie eine der folgenden Optionen, um die Datei vorzubereiten.

#### <a name="option-1-configure-with-log-analytics-workspace-id-and-key"></a>Option 1: Konfigurieren mit der ID und dem Schlüssel des Log Analytics-Arbeitsbereichs 

Kopieren Sie die folgende Spark-Konfiguration, speichern Sie sie als *spark_loganalytics_conf.txt*, und füllen Sie die folgenden Parameter aus:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: ID des Log Analytics-Arbeitsbereichs
   - `<LOG_ANALYTICS_WORKSPACE_KEY>`: Log Analytics-Schlüssel. Diesen Schlüssel finden Sie im Azure-Portal unter **Azure Log Analytics-Arbeitsbereich** > **Agent-Verwaltung** > **Primärschlüssel**.

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.secret <LOG_ANALYTICS_WORKSPACE_KEY>
```

#### <a name="option-2-configure-with-azure-key-vault"></a>Option 2: Konfigurieren mit Azure Key Vault

> [!NOTE]
> Benutzern, die Spark-Anwendungen übermitteln, muss die Berechtigung zum Lesen von Geheimnissen erteilt werden. Weitere Informationen finden Sie unter [Gewähren des Zugriffs auf Key Vault-Schlüssel, -Zertifikate und -Geheimnisse mit der rollenbasierten Zugriffssteuerung in Azure](../../key-vault/general/rbac-guide.md).

Führen Sie die folgenden Schritte aus, um Azure Key Vault zum Speichern des Arbeitsbereichsschlüssels zu konfigurieren:

1. Erstellen Sie im Azure-Portal einen Schlüsseltresor, und navigieren Sie dorthin.
2. Wählen Sie auf der Seite mit den Einstellungen des Schlüsseltresors **Geheimnisse** aus.
3. Wählen Sie die Option **Generieren/Importieren** aus.
4. Wählen Sie auf dem Bildschirm **Geheimnis erstellen** folgende Werte aus:
   - **Name**: Geben Sie einen Namen für das Geheimnis ein. Geben Sie als Standardeinstellung `SparkLogAnalyticsSecret` ein.
   - **Wert**: Geben Sie als Geheimnis `<LOG_ANALYTICS_WORKSPACE_KEY>` ein.
   - Behalten Sie bei den anderen Optionen die Standardwerte bei. Klicken Sie anschließend auf **Erstellen**.
5. Kopieren Sie die folgende Spark-Konfiguration, speichern Sie sie als *spark_loganalytics_conf.txt*, und füllen Sie die folgenden Parameter aus:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: Die ID des Log Analytics-Arbeitsbereichs
   - `<AZURE_KEY_VAULT_NAME>`: Der von Ihnen konfigurierte Schlüsseltresorname
   - `<AZURE_KEY_VAULT_SECRET_KEY_NAME>` (optional): Der Geheimnisname im Schlüsseltresor für den Arbeitsbereichsschlüssel. Der Standardwert lautet `SparkLogAnalyticsSecret`.

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
```

> [!NOTE]
> Sie können auch die Arbeitsbereichs-ID in Key Vault speichern. Gehen Sie wie oben beschrieben vor, und speichern Sie die Arbeitsbereichs-ID mit dem Geheimnisnamen `SparkLogAnalyticsWorkspaceId`. Alternativ können Sie die Konfiguration `spark.synapse.logAnalytics.keyVault.key.workspaceId` verwenden, um den Geheimnisnamen der Arbeitsbereichs-ID in Key Vault anzugeben.

#### <a name="option-3-configure-with-a-linked-service"></a>Option 3. Konfigurieren mit einem verknüpften Dienst

> [!NOTE]
> Benutzern, die Spark-Anwendungen übermitteln, muss die Berechtigung zum Lesen von Geheimnissen erteilt werden. Weitere Informationen finden Sie unter [Gewähren des Zugriffs auf Key Vault-Schlüssel, -Zertifikate und -Geheimnisse mit der rollenbasierten Zugriffssteuerung in Azure](../../key-vault/general/rbac-guide.md).

Führen Sie die folgenden Schritte aus, um in Synapse Studio einen mit Key Vault verknüpften Dienst zum Speichern des Arbeitsbereichsschlüssels zu konfigurieren:

1. Führen Sie alle Schritte im vorherigen Abschnitt unter „Option 2“ aus.
2. Erstellen Sie in Synapse Studio einen mit Key Vault verknüpften Dienst:

    a. Navigieren Sie zu **Synapse Studio** > **Verwalten** > **Verknüpfte Dienste**, und wählen Sie dann **Neu** aus.

    b. Suchen Sie mithilfe des Suchfelds nach **Azure Key Vault**.

    c. Geben Sie einen Namen für den verknüpften Dienst ein.

    d. Wählen Sie Ihren Schlüsseltresor und dann **Erstellen** aus.

3. Fügen Sie der Spark-Konfiguration ein Element vom Typ `spark.synapse.logAnalytics.keyVault.linkedServiceName` hinzu.

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
spark.synapse.logAnalytics.keyVault.linkedServiceName <LINKED_SERVICE_NAME>
```

#### <a name="available-spark-configuration"></a>Verfügbare Spark-Konfiguration

| Konfigurationsname                                  | Standardwert                | Beschreibung                                                                                                                                                                                                |
| --------------------------------------------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| spark.synapse.logAnalytics.enabled                  | false                        | „true“, um die Log Analytics-Senke für die Spark-Anwendungen zu aktivieren. Andernfalls „false“.                                                                                                                  |
| spark.synapse.logAnalytics.workspaceId              | -                            | Die ID des Log Analytics-Zielarbeitsbereichs                                                                                                                                                          |
| spark.synapse.logAnalytics.secret                   | -                            | Das Geheimnis des Log Analytics-Zielarbeitsbereichs                                                                                                                                                      |
| spark.synapse.logAnalytics.keyVault.linkedServiceName   | -                            | Der Name des mit Key Vault verknüpften Diensts für die ID und den Schlüssel des Log Analytics-Arbeitsbereichs                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.name            | -                            | Der Name der Key Vault-Instanz für die ID und den Schlüssel von Log Analytics                                                                                                                                                |
| spark.synapse.logAnalytics.keyVault.key.workspaceId | SparkLogAnalyticsWorkspaceId | Der Name des Key Vault-Geheimnisses für die ID des Log Analytics-Arbeitsbereichs                                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.key.secret      | SparkLogAnalyticsSecret      | Der Name des Key Vault-Geheimnisses für den Schlüssel des Log Analytics-Arbeitsbereichs                                                                                                                                      |
| spark.synapse.logAnalytics.uriSuffix       | ods.opinsights.azure.com     | Das [URI-Suffix][uri_suffix] des Log Analytics-Zielarbeitsbereichs. Befindet sich Ihr Arbeitsbereich nicht in der globalen Azure-Region, muss das URI-Suffix an die entsprechende Cloud angepasst werden. |

> [!NOTE]  
> - Für Azure China muss der Parameter `spark.synapse.logAnalytics.uriSuffix` auf `ods.opinsights.azure.cn` festgelegt werden. 
> - Für Azure Government muss der Parameter `spark.synapse.logAnalytics.uriSuffix` auf `ods.opinsights.azure.us` festgelegt werden. 

[uri_suffix]: ../../azure-monitor/logs/data-collector-api.md#request-uri


### <a name="step-3-upload-your-spark-configuration-to-a-spark-pool"></a>Schritt 3: Hochladen Ihrer Spark-Konfiguration in einen Spark-Pool
Sie können die Konfigurationsdatei in Ihren Spark-Pool in Azure Synapse Analytics hochladen. In Synapse Studio:

   1. Wählen Sie **Verwalten** > **Apache Spark-Pools** aus.
   2. Wählen Sie neben Ihrem Apache Spark-Pool die Schaltfläche **...** aus.
   3. Wählen Sie **Apache Spark-Konfiguration** aus. 
   4. Wählen Sie **Hochladen** und dann die Datei *spark_loganalytics_conf.txt* aus.
   5. Wählen Sie **Hochladen** und dann **Anwenden** aus.

      > [!div class="mx-imgBorder"]
      > ![Screenshot: Konfiguration des Spark-Pools](./media/apache-spark-azure-log-analytics/spark-pool-configuration.png)

> [!NOTE] 
>
> Von allen an den Spark-Pool übermittelten Spark-Anwendungen wird die Konfigurationseinstellung verwendet, durch die Metriken und Protokolle der Spark-Anwendung an den angegebenen Arbeitsbereich gepusht werden.

## <a name="submit-a-spark-application-and-view-the-logs-and-metrics"></a>Übermitteln einer Spark-Anwendung und Anzeigen der Protokolle und Metriken

Gehen Sie dabei folgendermaßen vor:

1. Übermitteln Sie eine Spark-Anwendung an den im vorherigen Schritt konfigurierten Spark-Pool. Sie können dazu eine der folgenden Methoden verwenden:
    - Führen Sie ein Notebook in Synapse Studio aus. 
    - Übermitteln Sie in Synapse Studio einen Apache Spark-Batchauftrag über eine Spark-Auftragsdefinition.
    - Führen Sie eine Pipeline mit einer Spark-Aktivität aus.

1. Navigieren Sie zum angegebenen Log Analytics-Arbeitsbereich, und sehen Sie sich die Anwendungsmetriken und -protokolle an, wenn die Spark-Anwendung gestartet wird.

## <a name="use-the-sample-workbook-to-visualize-the-metrics-and-logs"></a>Visualisieren der Metriken und Protokolle unter Verwendung der Beispielarbeitsmappe

1. [Laden Sie die Arbeitsmappe herunter.](https://aka.ms/SynapseSparkLogAnalyticsWorkbook)
2. Öffnen Sie die Arbeitsmappendatei, und kopieren Sie den Inhalt.
3. Wählen Sie im [Azure-Portal](https://portal.azure.com/) die Option **Log Analytics-Arbeitsbereich** > **Arbeitsmappen** aus. 
4. Öffnen Sie die Arbeitsmappe namens **Leer**. Verwenden Sie den Modus **Erweiterter Editor**, indem Sie das Symbol **</>** auswählen.
5. Fügen Sie den gesamten vorhandenen JSON-Code ein.
6. Wählen Sie **Anwenden** und dann **Bearbeitung abgeschlossen** aus.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Neue Arbeitsmappe](./media/apache-spark-azure-log-analytics/new-workbook.png)

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Importieren einer Arbeitsmappe](./media/apache-spark-azure-log-analytics/import-workbook.png)

Übermitteln Sie als Nächstes Ihre Apache Spark-Anwendung an den konfigurierten Spark-Pool. Wenn sich die Anwendung im Ausführungszustand befindet, können Sie die ausgeführte Anwendung in der Dropdownliste der Arbeitsmappen auswählen.

> [!div class="mx-imgBorder"]
> ![Screenshot: Arbeitsmappe](./media/apache-spark-azure-log-analytics/workbook.png)

Sie können die Arbeitsmappe anpassen. Beispielsweise können Sie Kusto-Abfragen verwenden und Warnungen konfigurieren.

> [!div class="mx-imgBorder"]
> ![Screenshot: Anpassen einer Arbeitsmappe mit einer Abfrage und Warnungen](./media/apache-spark-azure-log-analytics/kusto-query-and-alerts.png)

## <a name="sample-kusto-queries"></a>Kusto-Beispielabfragen

Hier sehen Sie ein Beispiel für das Abfragen von Spark-Ereignissen:

```kusto
SparkListenerEvent_CL
| where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
| order by TimeGenerated desc
| limit 100 
```

Hier sehen Sie ein Beispiel für das Abfragen von Treiber- und Executor-Protokollen der Spark-Anwendung:

```kusto
SparkLoggingEvent_CL
| where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
| order by TimeGenerated desc
| limit 100
```

Und hier sehen Sie ein Beispiel für das Abfragen von Spark-Metriken:

```kusto
SparkMetrics_CL
| where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
| where name_s endswith "jvm.total.used"
| summarize max(value_d) by bin(TimeGenerated, 30s), executorId_s
| order by TimeGenerated asc
```

## <a name="write-custom-application-logs"></a>Schreiben von benutzerdefinierten Anwendungsprotokollen

Sie können die Apache Log4j-Bibliothek verwenden, um benutzerdefinierte Protokolle zu schreiben.

Ein Beispiel für Scala:

```scala
%%spark
val logger = org.apache.log4j.LogManager.getLogger("com.contoso.LoggerExample")
logger.info("info message")
logger.warn("warn message")
logger.error("error message")
```

Ein Beispiel für PySpark:

```python
%%pyspark
logger = sc._jvm.org.apache.log4j.LogManager.getLogger("com.contoso.PythonLoggerExample")
logger.info("info message")
logger.warn("warn message")
logger.error("error message")
```

## <a name="create-and-manage-alerts"></a>Erstellen und Verwalten von Warnungen

Benutzer können Abfragen ausführen, um Metriken und Protokolle mit einer bestimmten Häufigkeit auszuwerten und basierend auf den Ergebnissen eine Warnung auszulösen. Weitere Informationen finden Sie unter [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../../azure-monitor/alerts/alerts-log.md).

## <a name="limitation"></a>Einschränkung

Azure Synapse Analytics-Arbeitsbereiche mit aktiviertem [verwaltetem virtuellem Netzwerk](../security/synapse-workspace-managed-vnet.md) werden nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

 - [Schnellstart: Erstellen eines serverlosen Apache Spark-Pools mithilfe von Synapse Studio](../quickstart-create-apache-spark-pool-studio.md)
 - [Erstellen, Entwickeln und Verwalten von Synapse Studio-Notebooks in Azure Synapse Analytics](./apache-spark-development-using-notebooks.md)
 - [Tutorial: Erstellen einer Apache Spark-Auftragsdefinition in Synapse Studio](./apache-spark-job-definitions.md)
