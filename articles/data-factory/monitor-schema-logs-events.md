---
title: Schema von Protokollen und Ereignissen
description: Erfahren Sie mehr über das Schema, das von Azure Data Factory-Protokollen und -Ereignissen für die Überwachung verwendet wird.
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 0245d988ab9b116a515ec76ecb9d7031936918be
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853926"
---
# <a name="schema-of-logs-and-events"></a>Schema von Protokollen und Ereignissen

In diesem Artikeln wird das Schema beschrieben, das von Azure Data Factory-Protokollen und -Ereignissen für die Überwachung verwendet wird.

## <a name="monitor-schema"></a>Monitor-Schema
Die folgenden Listen mit Attributen werden für die Überwachung verwendet.

### <a name="activity-run-log-attributes"></a>Attribute des Aktivitätsausführungsprotokolls

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Eigenschaft | type | BESCHREIBUNG | Beispiel |
| --- | --- | --- | --- |
| **Level** |String | Die Ebene der Diagnoseprotokolle. Legen Sie für Aktivitätsausführungsprotokolle den Eigenschaftswert auf „4“ fest. | `4` |
| **correlationId** |String | Die eindeutige ID für die Nachverfolgung einer bestimmten Anforderung. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Die Zeitpunkt des Ereignisses im TimeSpan-UTC-Format `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| Die ID der Aktivitätsausführung. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| Die ID der Pipelineausführung. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Ressourcen-ID**| String | Die mit der Data Factory-Ressource verknüpfte ID. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | Die Kategorie der Diagnoseprotokolle. Legen Sie den Eigenschaftswert auf `ActivityRuns` fest. | `ActivityRuns` |
|**level**| String | Die Ebene der Diagnoseprotokolle. Legen Sie den Eigenschaftswert auf `Informational` fest. | `Informational` |
|**operationName**| String | Der Name der Aktivität zusammen mit ihrem Status. Wenn die Aktivität der Startheartbeat ist, lautet der Eigenschaftswert `MyActivity -`. Wenn die Aktivität der Endheartbeat ist, lautet der Eigenschaftswert `MyActivity - Succeeded`. | `MyActivity - Succeeded` |
|**pipelineName**| String | Der Name der Pipeline. | `MyPipeline` |
|**activityName**| String | Der Name der Aktivität. | `MyActivity` |
|**start**| String | Die Startzeit der Aktivitätsausführungen im TimeSpan-UTC-Format. | `2017-06-26T20:55:29.5007959Z`|
|**end**| String | Die Endzeit der Aktivitätsausführungen im TimeSpan-UTC-Format. Wenn das Diagnoseprotokoll anzeigt, dass eine Aktivität gestartet, aber noch nicht beendet wurde, lautet der Eigenschaftswert `1601-01-01T00:00:00Z`. | `2017-06-26T20:55:29.5007959Z` |

### <a name="pipeline-run-log-attributes"></a>Attribute des Pipelineausführungsprotokolls

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Eigenschaft | type | BESCHREIBUNG | Beispiel |
| --- | --- | --- | --- |
| **Level** |String | Die Ebene der Diagnoseprotokolle. Legen Sie für Aktivitätsausführungsprotokolle den Eigenschaftswert auf „4“ fest. | `4` |
| **correlationId** |String | Die eindeutige ID für die Nachverfolgung einer bestimmten Anforderung. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Die Zeitpunkt des Ereignisses im TimeSpan-UTC-Format `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| Die ID der Pipelineausführung. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Ressourcen-ID**| String | Die mit der Data Factory-Ressource verknüpfte ID. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | Die Kategorie der Diagnoseprotokolle. Legen Sie den Eigenschaftswert auf `PipelineRuns` fest. | `PipelineRuns` |
|**level**| String | Die Ebene der Diagnoseprotokolle. Legen Sie den Eigenschaftswert auf `Informational` fest. | `Informational` |
|**operationName**| String | Der Name der Pipeline zusammen mit ihrem Status. Nachdem die Pipelineausführung abgeschlossen ist, lautet der Eigenschaftswert `Pipeline - Succeeded`. | `MyPipeline - Succeeded`. |
|**pipelineName**| String | Der Name der Pipeline. | `MyPipeline` |
|**start**| String | Die Startzeit der Aktivitätsausführungen im TimeSpan-UTC-Format. | `2017-06-26T20:55:29.5007959Z`. |
|**end**| String | Die Endzeit der Aktivitätsausführungen im TimeSpan-UTC-Format. Wenn das Diagnoseprotokoll anzeigt, dass eine Aktivität gestartet, aber noch nicht beendet wurde, lautet der Eigenschaftswert `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |
|**status**| String | Der abschließende Status der Pipelineausführung. Mögliche Eigenschaftswerte sind `Succeeded` und `Failed`. | `Succeeded`|

### <a name="trigger-run-log-attributes"></a>Attribute des Triggerausführungsprotokolls

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| Eigenschaft | type | BESCHREIBUNG | Beispiel |
| --- | --- | --- | --- |
| **Level** |String | Die Ebene der Diagnoseprotokolle. Legen Sie für Aktivitätsausführungsprotokolle den Eigenschaftswert auf „4“ fest. | `4` |
| **correlationId** |String | Die eindeutige ID für die Nachverfolgung einer bestimmten Anforderung. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Die Zeitpunkt des Ereignisses im TimeSpan-UTC-Format `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| Die ID der Triggerausführung. | `08587023010602533858661257311` |
|**Ressourcen-ID**| String | Die mit der Data Factory-Ressource verknüpfte ID. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | Die Kategorie der Diagnoseprotokolle. Legen Sie den Eigenschaftswert auf `PipelineRuns` fest. | `PipelineRuns` |
|**level**| String | Die Ebene der Diagnoseprotokolle. Legen Sie den Eigenschaftswert auf `Informational` fest. | `Informational` |
|**operationName**| String | Der Name des Triggers mit seinem abschließenden Status, der angibt, ob der Trigger erfolgreich ausgelöst wurde. Wenn der Heartbeat erfolgreich war, lautet der Eigenschaftswert `MyTrigger - Succeeded`. | `MyTrigger - Succeeded` |
|**triggerName**| String | Der Name des Triggers. | `MyTrigger` |
|**triggerType**| String | Der Typ des Triggers. Mögliche Eigenschaftswerte sind `Manual Trigger` und `Schedule Trigger`. | `ScheduleTrigger` |
|**triggerEvent**| String | Das Ereignis des Triggers. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | Die Startzeit der Triggerauslösung im TimeSpan-UTC-Format. | `2017-06-26T20:55:29.5007959Z`|
|**status**| String | Der abschließende Status, der anzeigt, ob der Trigger erfolgreich ausgelöst wurde. Mögliche Eigenschaftswerte sind `Succeeded` und `Failed`. | `Succeeded`|

### <a name="ssis-integration-runtime-log-attributes"></a>Protokollattribute der SSIS Integration Runtime

Im Folgenden finden Sie die Protokollattribute der SQL Server Integration Services (SSIS) Integration Runtime (IR) für den Start, das Beenden und die Wartungsvorgänge.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| Eigenschaft                   | type   | BESCHREIBUNG                                                   | Beispiel                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | Ereigniszeitpunkt im UTC-Format: `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Name Ihres SSIS IR-Vorgangs                            | `Start/Stop/Maintenance/Heartbeat` |
| **category**               | String | Kategorie der Diagnoseprotokolle                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | String | Eindeutige ID für die Nachverfolgung eines bestimmten Vorgangs             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | String | Der Name Ihrer Data Factory                                          | `MyADFv2` |
| **integrationRuntimeName** | String | Name Ihrer SSIS IR                                      | `MySSISIR` |
| **level**                  | String | Ebene mit Diagnoseprotokollen                                  | `Informational` |
| **resultType**             | String | Ergebnis Ihres SSIS IR-Vorgangs                          | `Started/InProgress/Succeeded/Failed/Healthy/Unhealthy` |
| **Nachricht**                | String | Ausgabemeldung Ihres SSIS IR-Vorgangs                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **Ressourcen-ID**             | String | Die eindeutige ID Ihrer Data Factory-Ressource                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-event-message-context-log-attributes"></a>Protokollattribute des Meldungskontexts von SSIS-Ereignissen

Nachstehend finden Sie die Protokollattribute von Bedingungen der Ereignismeldungen, die von SSIS-Paketausführungen Ihrer SSIS IR generiert werden. Sie liefern ähnliche Informationen wie ein [Tabellen oder Ansichten zum Kontext von Ereignismeldungen im SSIS-Katalog (SSISDB)](/sql/integration-services/system-views/catalog-event-message-context), in denen Runtimewerte für viele SSIS-Paketeigenschaften angezeigt werden. Sie werden generiert, wenn Sie den Protokolliergrad `Basic/Verbose` auswählen und sind hilfreich beim Debuggen und Überprüfen der Konformität.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| Eigenschaft                   | type   | BESCHREIBUNG                                                          | Beispiel                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | Ereigniszeitpunkt im UTC-Format: `YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Legen Sie den Wert `YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **category**               | String | Kategorie der Diagnoseprotokolle                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | String | Eindeutige ID für die Nachverfolgung eines bestimmten Vorgangs                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | Der Name Ihrer Data Factory                                                 | `MyADFv2` |
| **integrationRuntimeName** | String | Name Ihrer SSIS IR                                             | `MySSISIR` |
| **level**                  | String | Ebene mit Diagnoseprotokollen                                         | `Informational` |
| **operationId**            | String | Eindeutige ID für die Nachverfolgung eines bestimmten Vorgangs in der SSISDB          | `1` (1 steht für Vorgänge von Paketen, die *nicht* in der SSISDB gespeichert sind oder über T-SQL aufgerufen werden.) |
| **contextDepth**           | String | Tiefe Ihres Kontexts von Ereignismeldungen                              | `0` (0 steht für den Kontext vor dem Start der Paketausführung, und 1 steht für den Kontext beim Auftreten eines Fehlers. Dieser Wert wird erhöht, je weiter der Kontext vom Fehler entfernt ist.) |
| **packagePath**            | String | Pfad des Paketobjekts als Quelle des Kontexts Ihrer Ereignismeldung      | `\Package` |
| **contextType**            | String | Typ des Paketobjekts als Quelle des Kontexts Ihrer Ereignismeldung      | `60` (Siehe [weitere Kontexttypen](/sql/integration-services/system-views/catalog-event-message-context#remarks).) |
| **contextSourceName**      | String | Name des Paketobjekts als Quelle des Kontexts Ihrer Ereignismeldung      | `MyPackage` |
| **contextSourceId**        | String | Eindeutige ID des Paketobjekts als Quelle des Kontexts Ihrer Ereignismeldung | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | String | Name der Paketeigenschaft für die Quelle des Kontexts Ihrer Ereignismeldung   | `DelayValidation` |
| **propertyValue**          | String | Wert der Paketeigenschaft für die Quelle des Kontexts Ihrer Ereignismeldung  | `False` |
| **Ressourcen-ID**             | String | Die eindeutige ID Ihrer Data Factory-Ressource                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-event-messages-log-attributes"></a>Protokollattribute von SSIS-Ereignismeldungen

Nachstehend finden Sie die Protokollattribute von Ereignismeldungen, die von SSIS-Paketausführungen Ihrer SSIS IR generiert werden. Sie liefern ähnliche Informationen wie [Tabellen oder Ansichten für SSISDB-Ereignismeldungen](/sql/integration-services/system-views/catalog-event-messages), in denen die ausführlichen Text- bzw. Metadaten von Ereignismeldungen angezeigt werden. Sie werden für alle Protokolliergrade mit Ausnahme von `None` generiert.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| Eigenschaft                   | type   | BESCHREIBUNG                                                        | Beispiel                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                   | String | Ereigniszeitpunkt im UTC-Format: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Legen Sie den Wert `YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **category**               | String | Kategorie der Diagnoseprotokolle                                    | `SSISPackageEventMessages` |
| **correlationId**          | String | Eindeutige ID für die Nachverfolgung eines bestimmten Vorgangs                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | Der Name Ihrer Data Factory                                               | `MyADFv2` |
| **integrationRuntimeName** | String | Name Ihrer SSIS IR                                           | `MySSISIR` |
| **level**                  | String | Ebene mit Diagnoseprotokollen                                       | `Informational` |
| **operationId**            | String | Eindeutige ID für die Nachverfolgung eines bestimmten Vorgangs in der SSISDB        | `1` (1 steht für Vorgänge von Paketen, die *nicht* in der SSISDB gespeichert sind oder über T-SQL aufgerufen werden.) |
| **messageTime**            | String | Zeitpunkt, zum dem Ihre Ereignismeldung im UTC-Format erstellt wird          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | String | Typ Ihrer Ereignismeldung                                     | `70` (Siehe [weitere Nachrichtentypen](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks).) |
| **messageSourceType**      | String | Typ Ihrer Quelle für Ereignismeldungen                              | `20` (Siehe [weitere Typen von Meldungsquellen](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks).) |
| **Nachricht**                | String | Text Ihrer Ereignismeldung                                     | `MyPackage:Validation has started.` |
| **packageName**            | String | Name Ihrer ausgeführten Paketdatei                             | `MyPackage.dtsx` |
| **eventName**              | String | Name des zugehörigen Runtimeereignisses                                 | `OnPreValidate` |
| **messageSourceName**      | String | Name der Paketkomponente als Ihre Quelle für Ereignismeldungen         | `Data Flow Task` |
| **messageSourceId**        | String | Eindeutige ID der Paketkomponente als Ihre Quelle für Ereignismeldungen    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponentName**       | String | Name der Datenflusskomponente als Ihre Quelle für Ereignismeldungen       | `SSIS.Pipeline` |
| **packagePath**            | String | Pfad des Paketobjekts als Ihre Quelle für Ereignismeldungen            | `\Package\Data Flow Task` |
| **executionPath**          | String | Vollständiger Pfad vom übergeordneten Paket zur ausgeführten Komponente            | `\Transformation\Data Flow Task` (Über diesen Pfad werden auch Komponenteniterationen erfasst.) |
| **threadId**               | String | Eindeutige ID des Threads, der bei der Ausführung Ihrer Ereignismeldung protokolliert wird | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

### <a name="ssis-executable-statistics-log-attributes"></a>Protokollattribute der Statistik zu ausführbaren SSIS-Dateien

Nachstehend finden Sie die Protokollattribute der Statistiken von ausführbaren Dateien, die über SSIS-Paketausführungen Ihrer SSIS IR generiert werden. Hierbei sind ausführbare Dateien Container oder Tasks in Paketablaufsteuerungen. Sie liefern ähnliche Informationen wie [Tabellen oder Ansichten für Statistiken zu ausführbaren SSISDB-Dateien](/sql/integration-services/system-views/catalog-executable-statistics), in denen eine Zeile für jede aktive ausführbare Datei angezeigt wird, einschließlich Iterationen. Sie werden bei allen Protokolliergraden mit Ausnahme von `None` generiert und sind nützlich zum Identifizieren von Engpässen bzw. Fehlern auf der Taskebene.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| Eigenschaft                   | type   | BESCHREIBUNG                                                      | Beispiel                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | Ereigniszeitpunkt im UTC-Format: `YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Legen Sie den Wert `YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **category**               | String | Kategorie der Diagnoseprotokolle                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | String | Eindeutige ID für die Nachverfolgung eines bestimmten Vorgangs                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | Der Name Ihrer Data Factory                                             | `MyADFv2` |
| **integrationRuntimeName** | String | Name Ihrer SSIS IR                                         | `MySSISIR` |
| **level**                  | String | Ebene mit Diagnoseprotokollen                                     | `Informational` |
| **executionId**            | String | Eindeutige ID für die Nachverfolgung einer bestimmten Ausführung in der SSISDB      | `1` (1 steht für Ausführungen von Paketen, die *nicht* in der SSISDB gespeichert sind oder über T-SQL aufgerufen werden.) |
| **executionPath**          | String | Vollständiger Pfad vom übergeordneten Paket zur ausgeführten Komponente          | `\Transformation\Data Flow Task` (Über diesen Pfad werden auch Komponenteniterationen erfasst.) |
| **startTime**              | String | Zeitpunkt im UTC-Format, zu dem die ausführbare Datei in die Phase vor der Ausführung eintritt  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | Zeitpunkt im UTC-Format, zu dem die ausführbare Datei in die Phase nach der Ausführung eintritt | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | String | Ausführungszeit der ausführbaren Datei in Millisekunden                   | `1,125` |
| **executionResult**        | String | Ergebnis der Ausführung der ausführbaren Datei                                 | `0` (0 steht für „Erfolg“, 1 für „Fehler“, 2 für „Abschluss“ und 3 für „Abbruch“) |
| **executionValue**         | String | Von der aktiven ausführbaren Datei zurückgegebener benutzerdefinierter Wert            | `1` |
| **Ressourcen-ID**             | String | Die eindeutige ID Ihrer Data Factory-Ressource                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-execution-component-phases-log-attributes"></a>Protokollattribute der Komponentenphasen von SSIS-Ausführungen

Nachstehend finden Sie die Protokollattribute von den Runtimestatistiken für die Datenflusskomponenten, die von SSIS-Paketausführungen Ihrer SSIS IR generiert werden. Sie liefern ähnliche Informationen wie [Tabellen und Ansichten der Komponentenphasen von SSISDB-Ausführungen](/sql/integration-services/system-views/catalog-execution-component-phases), in denen die Zeit angezeigt wird, die die Datenflusskomponenten in den einzelnen Ausführungsphasen verbringen. Sie werden generiert, wenn Sie den Protokolliergrad `Performance/Verbose` auswählen, und sind hilfreich zum Erfassen von Statistiken zur Datenflussausführung.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Eigenschaft                   | type   | BESCHREIBUNG                                                         | Beispiel                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | Ereigniszeitpunkt im UTC-Format: `YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Legen Sie den Wert `YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **category**               | String | Kategorie der Diagnoseprotokolle                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | String | Eindeutige ID für die Nachverfolgung eines bestimmten Vorgangs                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | Der Name Ihrer Data Factory                                                | `MyADFv2` |
| **integrationRuntimeName** | String | Name Ihrer SSIS IR                                            | `MySSISIR` |
| **level**                  | String | Ebene mit Diagnoseprotokollen                                        | `Informational` |
| **executionId**            | String | Eindeutige ID für die Nachverfolgung einer bestimmten Ausführung in der SSISDB         | `1` (1 steht für Ausführungen von Paketen, die *nicht* in der SSISDB gespeichert sind oder über T-SQL aufgerufen werden.) |
| **packageName**            | String | Name Ihrer ausgeführten Paketdatei                              | `MyPackage.dtsx` |
| **taskName**               | String | Name des ausgeführten Datenflusstasks                                 | `Data Flow Task` |
| **subcomponentName**       | String | Name der Datenflusskomponente                                     | `Derived Column` |
| **phase**                  | String | Name der Ausführungsphase                                         | `AcquireConnections` |
| **startTime**              | String | Zeitpunkt im UTC-Format, zu dem die Ausführungsphase gestartet wird                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | Zeitpunkt im UTC-Format, zu dem die Ausführungsphase beendet wird                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | String | Ausführungspfad für den Datenflusstask                            | `\Transformation\Data Flow Task` |
| **Ressourcen-ID**             | String | Die eindeutige ID Ihrer Data Factory-Ressource                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-execution-data-statistics-log-attributes"></a>Protokollattribute für Statistiken von SSIS-Ausführungsdaten

Nachstehend finden Sie die Protokollattribute von Datenverschiebungen durch die einzelnen Abschnitte von Datenflusspipelines (von Upstream- zu Downstream-Komponenten), die von SSIS-Paketausführungen Ihrer SSIS IR generiert werden. Sie liefern ähnliche Informationen wie [Tabellen und Ansichten mit Statistiken für SSISDB-Ausführungsdaten](/sql/integration-services/system-views/catalog-execution-data-statistics), in denen die Zeilenanzahl für Daten angezeigt wird, die Datenflusstasks durchlaufen. Sie werden generiert, wenn Sie den Protokolliergrad `Verbose` auswählen, und sind hilfreich zum Berechnen des Datenflussdurchsatzes.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Eigenschaft                     | type   | BESCHREIBUNG                                                        | Beispiel                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                     | String | Ereigniszeitpunkt im UTC-Format: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | String | Legen Sie den Wert `YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **category**                 | String | Kategorie der Diagnoseprotokolle                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | String | Eindeutige ID für die Nachverfolgung eines bestimmten Vorgangs                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | String | Der Name Ihrer Data Factory                                               | `MyADFv2` |
| **integrationRuntimeName**   | String | Name Ihrer SSIS IR                                           | `MySSISIR` |
| **level**                    | String | Ebene mit Diagnoseprotokollen                                       | `Informational` |
| **executionId**              | String | Eindeutige ID für die Nachverfolgung einer bestimmten Ausführung in der SSISDB        | `1` (1 steht für Ausführungen von Paketen, die *nicht* in der SSISDB gespeichert sind oder über T-SQL aufgerufen werden.) |
| **packageName**              | String | Name Ihrer ausgeführten Paketdatei                             | `MyPackage.dtsx` |
| **taskName**                 | String | Name des ausgeführten Datenflusstasks                                | `Data Flow Task` |
| **dataflowPathIdString**     | String | Eindeutige ID für die Nachverfolgung des Datenflusspfads                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | String | Name des Datenflusspfads                                         | `ADO NET Source Output` |
| **sourceComponentName**      | String | Name der Datenflusskomponente, die Daten sendet                    | `SQLDB Table3` |
| **destinationComponentName** | String | Name der Datenflusskomponente, die Daten empfängt                 | `Derived Column` |
| **rowsSent**                 | String | Anzahl von Zeilen, die von der Quellkomponente gesendet wurden                        | `500` |
| **createdTime**              | String | Zeitpunkt im UTC-Format, zu dem Zeilenwerte abgerufen werden                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | String | Ausführungspfad für den Datenflusstask                           | `\Transformation\Data Flow Task` |
| **Ressourcen-ID**               | String | Die eindeutige ID Ihrer Data Factory-Ressource                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

## <a name="log-analytics-schema"></a>Log Analytics-Schema

Log Analytics erbt das Schema von Azure Monitor. Dabei gelten jedoch folgende Ausnahmen:

* Der erste Buchstabe in den einzelnen Spaltennamen wird in Großbuchstaben geschrieben. Der Spaltenname „correlationId“ in Monitor lautet in Log Analytics beispielsweise „CorrelationId“.
* Es gibt keine Spalte „Level“.
* Die dynamische Spalte „Eigenschaften“ wird als der folgende dynamische JSON-Blob-Typ beibehalten:

    | Azure Monitor-Spalte | Log Analytics-Spalte | type |
    | --- | --- | --- |
    | $.properties.UserProperties | UserProperties | Dynamisch |
    | $.properties.Annotations | Anmerkungen | Dynamisch |
    | $.properties.Input | Eingabe | Dynamisch |
    | $.properties.Output | Output | Dynamisch |
    | $.properties.Error.errorCode | ErrorCode | INT |
    | $.properties.Error.message | ErrorMessage | Zeichenfolge |
    | $.properties.Error | Fehler | Dynamisch |
    | $.properties.Predecessors | Predecessors | Dynamisch |
    | $.properties.Parameters | Parameter | Dynamisch |
    | $.properties.SystemParameters | SystemParameters | Dynamisch |
    | $.properties.Tags | `Tags` | Dynamisch |

## <a name="next-steps"></a>Nächste Schritte

[Installieren der Lösung „Azure Data Factory-Analyse“ über den Azure Marketplace](monitor-install-analytics.md)
