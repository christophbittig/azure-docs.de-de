---
title: Überwachen von Logik-Apps mit Azure Monitor-Protokollen
description: Behandeln von Problemen mit Logik-Apps durch Einrichten von Azure Monitor-Protokollen und Sammeln von Diagnosedaten für Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 09/24/2020
ms.openlocfilehash: 5375649d8bf5bdf1a6a24babc6f05d30d40c93b9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215182"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Einrichten von Azure Monitor-Protokollen und Sammeln von Diagnosedaten für Azure Logic Apps

Um während der Laufzeit umfangreichere Debuginformationen zu ihren Logik-Apps zu erhalten, können Sie [Azure Monitor-Protokolle](../azure-monitor/logs/data-platform-logs.md) einrichten und verwenden, um Informationen zu Laufzeitdaten und -ereignissen aufzuzeichnen und zu speichern, z. B. Triggerereignisse, Ausführungsereignisse und Aktionsereignisse in einem [Log Analytics-Arbeitsbereich](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). [Azure Monitor](../azure-monitor/overview.md) hilft Ihnen bei der Überwachung Ihrer Cloud- und lokalen Umgebungen, sodass Sie deren Verfügbarkeit und Leistung leichter sicherstellen können. Durch die Verwendung von Azure Monitor-Protokollen können Sie [Protokollabfragen](../azure-monitor/logs/log-query-overview.md) erstellen, die Ihnen helfen, diese Informationen zu sammeln und zu überprüfen. Sie können [diese Diagnosedaten auch mit anderen Azure-Diensten verwenden](#extend-data), z. B. Azure Storage und Azure Event Hubs.

Zum Einrichten der Protokollierung für Ihre Logik-App können Sie [beim Erstellen Ihrer Logik-App Log Analytics aktivieren](#logging-for-new-logic-apps), oder Sie können die [Logic Apps-Verwaltungslösung](#install-management-solution) in Ihrem Log Analytics-Arbeitsbereich für vorhandene Logik-Apps installieren. Diese Lösung bietet aggregierte Informationen für Ihre Logik-App-Ausführungen und umfasst spezifische Details wie Status, Ausführungszeit, Status der erneuten Übermittlung und Korrelations-IDs. Um dann die Protokollierung zu aktivieren und Abfragen für diese Informationen zu erstellen, [richten Sie Azure Monitor-Protokolle ein](#set-up-resource-logs).

In diesem Artikel erfahren Sie, wie Sie Log Analytics für neue und bereits vorhandene Logik-Apps aktivieren, wie Sie die Logic Apps-Verwaltungslösung installieren und einrichten und wie Sie Abfragen für Azure Monitor-Protokolle einrichten und erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, benötigen Sie einen [Log Analytics-Arbeitsbereich](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). Falls Sie keinen Arbeitsbereich besitzen, lesen Sie die Informationen zum [Erstellen eines Log Analytics-Arbeitsbereichs](../azure-monitor/logs/quick-create-workspace.md).

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Aktivieren von Log Analytics für neue Logik-Apps

Sie können Log Analytics aktivieren, wenn Sie Ihre Logik-App erstellen.

1. Führen Sie im [Azure-Portal](https://portal.azure.com) in dem Bereich, in dem Sie die Informationen zum Erstellen Ihrer Logik-App bereitstellen, die folgenden Schritte aus:

   1. Wählen Sie unter **Log Analytics** die Option **Ein** aus.

   1. Wählen Sie in der Liste der **Log Analytics-Arbeitsbereiche** den Arbeitsbereich aus, an den Sie Daten für Ihre Logik-App-Ausführungen senden möchten.

      ![Angeben von Informationen zur Logik-App](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      Nach Abschluss dieses Schritts erstellt Azure Ihre Logik-App, die jetzt Ihrem Log Analytics-Arbeitsbereich zugeordnet ist. Mit diesem Schritt wird die Lösung für die Logic Apps-Verwaltung automatisch in Ihrem Arbeitsbereich installiert.

1. Wählen Sie **Erstellen**, wenn Sie fertig sind.

1. Fahren Sie, nachdem Sie Ihre Logik-App ausgeführt haben, mit [diesen Schritten](#view-logic-app-runs) fort, um Ihre Logik-App-Ausführungen anzuzeigen.

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Installieren der Lösung Logic Apps-Verwaltung

Überspringen Sie diesen Schritt, falls Sie Log Analytics beim Erstellen Ihrer Logik-App aktiviert haben. Sie haben die Logic Apps-Verwaltungslösung bereits in Ihrem Log Analytics-Arbeitsbereich installiert.

1. Geben Sie in das Suchfeld des [Azure-Portals](https://portal.azure.com) `log analytics workspaces` ein, und wählen Sie dann **Log Analytics-Arbeitsbereiche** aus.

   ![Auswählen von „Log Analytics-Arbeitsbereiche“](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. Wählen Sie unter **Log Analytics-Arbeitsbereiche** Ihren Arbeitsbereich aus.

   ![Auswählen Ihres Log Analytics-Arbeitsbereichs](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. Wählen Sie im Bereich **Übersicht** unter **Erste Schritte mit Log Analytics** > **Überwachungslösungen konfigurieren** die Option **Lösungen anzeigen** aus.

   ![Auswählen von „Lösungen anzeigen“ im Bereich „Übersicht“](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. Wählen Sie unter **Übersicht** die Option **Hinzufügen** aus.

   ![Hinzufügen einer neuen Lösung im Bereich „Übersicht“](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. Geben Sie, nachdem der **Marketplace** geöffnet wurde, in das Suchfeld `logic apps management` ein, und wählen Sie **Logic Apps-Verwaltung** aus.

   ![Im Marketplace „Logic Apps-Verwaltung“ auswählen](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. Wählen Sie im Bereich mit der Lösungsbeschreibung **Erstellen** aus.

   ![„Erstellen“ auswählen, um die Lösung „Logic Apps-Verwaltung“ hinzuzufügen](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Überprüfen und bestätigen Sie den Log Analytics-Arbeitsbereich, in dem Sie die Lösung installieren möchten, und wählen Sie erneut **Erstellen** aus.

   ![Auswählen von „Erstellen“ für „Logic Apps-Verwaltung“](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Nachdem Azure die Lösung in der Azure-Ressourcengruppe bereitgestellt hat, die den Log Analytics Arbeitsbereich enthält, wird die Lösung im Zusammenfassungsbereich Ihres Arbeitsbereichs angezeigt.

   ![Zusammenfassungsbereich des Arbeitsbereichs](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Einrichten von Azure Monitor-Protokollen

Wenn Sie Informationen zu Laufzeitereignissen und -daten in [Azure Monitor-Protokollen](../azure-monitor/logs/data-platform-logs.md) speichern, können Sie [Protokollabfragen](../azure-monitor/logs/log-query-overview.md) erstellen, die Ihnen helfen, diese Informationen zu finden und zu überprüfen.

> [!NOTE]
> Nach Aktivierung der Diagnoseeinstellungen kann es bis zu 30 Minuten dauern, bis Diagnosedaten an die Protokolle am angegebenen Ziel (beispielsweise Log Analytics, Event Hub oder Speicherkonto) übermittelt werden. Diese Verzögerung bedeutet, dass Diagnosedaten aus diesem Zeitraum möglicherweise nicht überprüft werden können. Abgeschlossene Ereignisse und [nachverfolgte Eigenschaften](#extend-data) werden unter Umständen erst nach zehn bis 15 Minuten in Ihrem Log Analytics-Arbeitsbereich angezeigt.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach Ihrer Logik-App, und wählen Sie sie aus.

1. Wählen Sie im Menü Ihrer Logik-App unter **Überwachung** die Option **Diagnoseeinstellungen** > **Diagnoseeinstellung hinzufügen** aus.

   ![Unter „Überwachung“ die Option „Diagnoseeinstellungen“ > „Diagnoseeinstellung hinzufügen“ auswählen](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Führen Sie die folgenden Schritte aus, um die Einstellung zu erstellen:

   1. Geben Sie einen Namen für die Einstellung an.

   1. Wählen Sie **An Log Analytics senden** aus.

   1. Wählen Sie für **Abonnement** das Azure-Abonnement aus, das Ihrem Log Analytics-Arbeitsbereich zugeordnet ist.

   1. Wählen Sie für **Log Analytics-Arbeitsbereich** den Arbeitsbereich aus, den Sie verwenden möchten.

   1. Wählen Sie unter **Protokoll** die Kategorie **WorkflowRuntime** aus, die die Ereigniskategorie angibt, die Sie aufzeichnen möchten.

   1. Um alle Metriken auszuwählen, wählen Sie unter **Metrik** die Option **AllMetrics** aus.

   1. Klicken Sie auf **Speichern**, wenn Sie fertig sind.

   Beispiel:

   ![Auswählen des Log Analytics-Arbeitsbereichs und der Daten für die Protokollierung](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Anzeigen des Ausführungsstatus der Logik-App

Im Anschluss an die Ausführung Ihrer Logik-App können Sie die Daten zu diesen Ausführungen in Ihrem Log Analytics-Arbeitsbereich anzeigen.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) Ihren Log Analytics-Arbeitsbereich, und öffnen Sie ihn.

1. Wählen Sie im Menü Ihres Arbeitsbereichs **Arbeitsbereichszusammenfassung** > **Logic Apps-Verwaltung** aus.

   ![Ausführungsstatus und -anzahl für die Logik-App](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Wenn auf der Kachel „Logic Apps-Verwaltung“ nicht sofort nach einer Ausführung Ergebnisse angezeigt werden, wählen Sie **Aktualisieren** aus, oder warten Sie einen kurzen Moment, bevor Sie es noch mal versuchen.

   Hier sind Ihre Logik-App-Ausführungen nach Name oder Ausführungsstatus gruppiert. Auf dieser Seite finden Sie auch Details zu Fehlern in Aktionen oder Triggern für die Logik-App-Ausführungen.

   ![Statuszusammenfassung für Ihre Logik-App-Ausführungen](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Wählen Sie die Zeile für eine Logik-App oder einen Status aus, um alle Ausführungen für eine bestimmte Logik-App bzw. einen Status anzuzeigen.

   Hier ist ein Beispiel angegeben, bei dem alle Ausführungen für eine bestimmte Logik-App aufgeführt werden:

   ![Anzeigen von Logik-App-Ausführungen und des Status](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   Für Aktionen, bei denen Sie [nachverfolgte Eigenschaften einrichten](#extend-data), können Sie diese Eigenschaften auch anzeigen, indem Sie in der Spalte **Nachverfolgte Eigenschaften** den Befehl **Anzeigen** auswählen. Sie können die nachverfolgten Eigenschaften mit dem Spaltenfilter durchsuchen.

   ![Anzeigen der überwachten Eigenschaften für eine Logik-App](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

1. Zum Filtern Ihrer Ergebnisse können Sie sowohl die clientseitige als auch die serverseitige Filterung durchführen.

   * **Clientseitiger Filter**: Wählen Sie für jede Spalte die gewünschten Filter aus. Beispiel:

     ![Beispiel für Spaltenfilter](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Serverseitiger Filter**: Verwenden Sie das Steuerelement für die Bereichsauswahl oben auf der Seite, um ein bestimmtes Zeitfenster auszuwählen oder die Anzahl von angezeigten Ausführungen zu begrenzen. Standardmäßig werden nur jeweils 1.000 Datensätze angezeigt.

     ![Ändern des Zeitfensters](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Um alle Aktionen und deren Details für eine bestimmte Ausführung anzuzeigen, wählen Sie die Zeile für eine Logik-App-Ausführung aus.

   Hier ist ein Beispiel mit allen Aktionen und Triggern für eine bestimmte Logik-App-Ausführung dargestellt:

   ![Anzeigen von Aktionen für eine Logik-App-Ausführung](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Senden von Diagnosedaten an Azure Storage und Azure Event Hubs

In Verbindung mit Azure Monitor-Protokollen können Sie die Nutzung der Diagnosedaten Ihrer Logik-App um andere Azure-Dienste erweitern, z.B.:

* [Archivieren von Azure-Ressourcenprotokollen in einem Speicherkonto](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)
* [Streamen von Protokollen der Azure-Plattform an Azure Event Hubs](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

Durch die Verwendung von Telemetriedaten und Analysen anderer Dienste (beispielsweise [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) und [Power BI](../azure-monitor/logs/log-powerbi.md)) können Sie dann eine Überwachung in Echtzeit implementieren. Beispiel:

* [Streamen von Daten von Event Hubs zu Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analysieren von Streamingdaten mit Stream Analytics und Erstellen eines Dashboards für die Echtzeitanalyse in Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Stellen Sie auf Grundlage der Orte, an die Sie Diagnosedaten senden möchten, sicher, dass Sie zuerst [ein Azure Storage-Konto](../storage/common/storage-account-create.md) oder [einen Azure Event Hub](../event-hubs/event-hubs-create.md) erstellen. Sie können dann die Ziele auswählen, an die Sie diese Daten senden möchten. Werte für die Beibehaltungsdauer gelten nur, wenn Sie ein Speicherkonto verwenden.

![Senden von Daten an das Azure-Speicherkonto oder einen Event Hub](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Azure Monitor-Diagnoseereignisse

Jedes Diagnoseereignis enthält Details zu Ihrer Logik-App und zum Ereignis, z.B. Status, Startzeit, Endzeit usw. Für die programmgesteuerte Einrichtung der Überwachung, Nachverfolgung und Protokollierung können Sie diese Informationen mit der [REST-API für Azure Logic Apps](/rest/api/logic) und der [REST-API für Azure Monitor](../azure-monitor/essentials/metrics-supported.md#microsoftlogicworkflows) verwenden. Sie können außerdem die Eigenschaften `clientTrackingId` und `trackedProperties` verwenden, die angezeigt werden in 

* `clientTrackingId`: Wenn sie nicht angegeben ist, wird diese ID von Azure automatisch generiert, und Ereignisse werden für eine Ausführung der Logik-App korreliert. Dies gilt auch für geschachtelte Workflows, die über die Logik-App aufgerufen werden. Sie können diese ID manuell in einem Trigger angeben, indem Sie einen `x-ms-client-tracking-id`-Header mit Ihrem benutzerdefinierten ID-Wert in der Triggeranforderung übergeben. Sie können einen Anforderungstrigger, HTTP-Trigger oder Webhooktrigger verwenden.

* `trackedProperties`: Zum Nachverfolgen der Ein- oder Ausgaben in Diagnosedaten können Sie einer Aktion einen `trackedProperties`-Abschnitt hinzufügen, indem Sie entweder den Logik-App-Designer verwenden, oder direkt in der JSON-Definition Ihrer Logik-App. Nachverfolgte Eigenschaften können nur die Ein- und Ausgaben einer einzelnen Aktion nachverfolgen. Mit den `correlation`-Eigenschaften der Ereignisse ist aber eine aktionsübergreifende Korrelation innerhalb einer Ausführung möglich. Fügen Sie den Abschnitt `trackedProperties` und die gewünschten Eigenschaften der Aktionsdefinition hinzu, um mehr als eine Eigenschaft nachzuverfolgen.

  Hier sehen Sie ein Beispiel, das zeigt, wie die Definition der Aktion **Variable initialisieren** nachverfolgte Eigenschaften aus der Eingabe der Aktion umfasst, wobei die Eingabe ein Array und kein Datensatz ist.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  In diesem Beispiel werden mehrere nachverfolgte Eigenschaften angezeigt:

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

Dieses Beispiel zeigt, wie das `ActionCompleted`-Ereignis die Attribute `clientTrackingId` und `trackedProperties` einschließt:

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
   "category": "WorkflowRuntime",
   "level": "Information",
   "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
   "properties": {
      "$schema": "2016-06-01",
      "startTime": "2016-07-09T17:09:53.4336305Z",
      "endTime": "2016-07-09T17:09:53.5430281Z",
      "status": "Succeeded",
      "code": "OK",
      "resource": {
         "subscriptionId": "<subscription-ID>",
         "resourceGroupName": "MyResourceGroup",
         "workflowId": "<logic-app-workflow-ID>",
         "workflowName": "MyLogicApp",
         "runId": "08587361146922712057",
         "location": "westus",
         "actionName": "Http"
      },
      "correlation": {
         "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
         "clientTrackingId": "<my-custom-tracking-ID>"
      },
      "trackedProperties": {
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von Überwachungs- und Nachverfolgungsabfragen](../logic-apps/create-monitoring-tracking-queries.md)
* [Überwachen von B2B-Nachrichten mit Azure Monitor-Protokollen](../logic-apps/monitor-b2b-messages-log-analytics.md)