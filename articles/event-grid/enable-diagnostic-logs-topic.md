---
title: 'Azure Event Grid: Aktivieren von Diagnoseprotokollen für Event Grid-Ressourcen'
description: Dieser Artikel enthält detaillierte Anleitungen zum Aktivieren von Diagnoseprotokollen für Event Grid-Ressourcen.
ms.topic: how-to
ms.date: 11/11/2021
ms.openlocfilehash: 06999716c884dc1c18ce41be288ebfba2b2298e6
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556283"
---
# <a name="enable-diagnostic-logs-for-event-grid-resources"></a>Aktivieren von Diagnoseprotokollen für Event Grid-Ressourcen

Dieser Artikel enthält detaillierte Anleitungen zum Aktivieren von Diagnoseeinstellungen für Event Grid-Ressourcen.

Mit diesen Einstellungen können Sie die Informationen erfassen und anzeigen. In der folgenden Tabelle werden die Kompatibilität der Einstellungen in den Event Grid-Ressourcen angezeigt.

| Diagnoseeinstellung | Beschreibung | Event Grid-Themen | Event Grid-Systemthemen | Event Grid-Domänen |
| ------------- | ------------- | --------- | ----------- | ----------- |
| DeliveryFailures | Übermittlungsfehler | Ja | Ja | Ja |
| PublishFailures | Veröffentlichungsfehler | Ja | Nein | Ja |
| DataPlaneRequests| Anforderungen auf Datenebene | Ja | Nein | Ja |

> [!IMPORTANT]
> Das Schema für Diagnoseprotokolle finden Sie unter [Diagnoseprotokolle](diagnostic-logs.md).

## <a name="prerequisites"></a>Voraussetzungen

- Eine bereitgestellte Event Grid-Ressource
- Ein bereitgestelltes Ziel zum Erfassen von Diagnoseprotokollen:
  - Log Analytics-Arbeitsbereich
  - Speicherkonto
  - Event Hub
  - Partnerlösung

## <a name="enable-diagnostic-logs-for-event-grid-topics-and-event-grid-domains"></a>Aktivieren von Diagnoseprotokollen für Event Grid-Themen und -Domänen

> [!NOTE]
> Der folgende Prozess enthält eine detaillierte Anleitung zum Aktivieren von Diagnoseprotokollen für Event Grid-Themen. Schritte zum Aktivieren von Diagnoseprotokollen für eine Domäne sind sehr ähnlich. Navigieren Sie in Schritt 2 zu den Event Grid-**Domänen** im Azure-Portal.  

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie zum Event Grid-Thema, für das Sie Diagnoseprotokolleinstellungen aktivieren möchten.
    1. Suchen Sie oben in der Suchleiste nach **Event Grid-Themen**.
        ![Suchen nach benutzerdefinierten Themen](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    2. Wählen Sie in der Liste, für die Sie Diagnoseeinstellungen konfigurieren möchten, das **Thema** aus.
3. Wählen Sie unter **Überwachung** im linken Menü die Option **Diagnoseeinstellungen** aus.
4. Wählen Sie auf der Seite **Diagnoseeinstellungen** den Eintrag **Diagnoseeinstellung hinzufügen** aus.
    ![Schaltfläche „Diagnoseeinstellung hinzufügen“](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Geben Sie einen **Namen** für die Diagnoseeinstellung an.
6. Wählen Sie den Eintrag **allLogs** im Abschnitt **Protokolle** aus.
    ![Auswählen der Fehler](./media/enable-diagnostic-logs-topic/log-failures.png)
7. Aktivieren Sie mindestens eines der Erfassungsziele für die Protokolle, und konfigurieren Sie diese dann, indem Sie eine zuvor erstellte Erfassungsressource auswählen.
    - Wenn Sie **In ein Speicherkonto archivieren** auswählen, wählen Sie **Speicherkonto - Konfigurieren** aus, und wählen Sie dann das Speicherkonto in Ihrem Azure-Abonnement aus.
        ![Screenshot, der die Seite „Diagnoseeinstellungen“ mit aktivierter Option „In einem Azure-Speicherkonto archivieren“ sowie ein ausgewähltes Speicherkonto zeigt.](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Wenn Sie **An einen Event Hub streamen** auswählen, wählen Sie **Event Hub – Konfigurieren** aus, und wählen Sie dann den Event Hubs-Namespace, den Event Hub und die Zugriffsrichtlinie aus.
        ![Screenshot, der die Seite „Diagnoseeinstellungen“ mit aktivierter Option „An einen Event Hub streamen“ zeigt.](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Wenn Sie **An Log Analytics senden** auswählen, wählen Sie den Log Analytics-Arbeitsbereich aus.
        ![Screenshot, der die Seite „Diagnoseeinstellungen“ mit aktivierter Option „An Log Analytics senden“ zeigt.](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. Wählen Sie **Speichern** aus. Wählen Sie dann in der rechten oberen Ecke **X** aus, um die Seite zu schließen.
9. Vergewissern Sie sich nun auf der Seite **Diagnoseeinstellungen**, dass ein neuer Eintrag in der Tabelle **Diagnoseeinstellungen** angezeigt wird.
    ![Screenshot, der die Seite „Diagnoseeinstellungen“ mit einem neuen Eintrag zeigt, der in der Tabelle „Diagnoseeinstellungen“ hervorgehoben ist.](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

Sie können auch die Erfassung aller Metriken für das Thema aktivieren.

## <a name="enable-diagnostic-logs-for-event-grid-system-topics"></a>Aktivieren von Diagnoseprotokollen für Event Grid-Systemthemen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie zum Event Grid-Thema, für das Sie Diagnoseprotokolleinstellungen aktivieren möchten.
    1. Suchen Sie oben in der Suchleiste nach **Event Grid-Systemthemen**.
        ![Suchen nach Systemthemen](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. Wählen Sie das **Systemthema** aus, für das Sie Diagnoseeinstellungen konfigurieren möchten.
        ![Auswählen des Systemthemas](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. Wählen Sie im linken Menü **Diagnoseeinstellungen** unter **Überwachung** aus, und wählen Sie dann **Diagnoseeinstellung hinzufügen** aus.
    ![Schaltfläche „Diagnoseeinstellungen hinzufügen“](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. Geben Sie einen **Namen** für die Diagnoseeinstellung an.
5. Wählen Sie den Eintrag **allLogs** im Abschnitt **Protokolle** aus.
    ![Auswählen von Übermittlungsfehlern](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. Aktivieren Sie mindestens eines der Erfassungsziele für die Protokolle, und konfigurieren Sie diese dann, indem Sie eine zuvor erstellte Erfassungsressource auswählen.
    - Wenn Sie **An Log Analytics senden** auswählen, wählen Sie den Log Analytics-Arbeitsbereich aus.
        ![An Log Analytics senden](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png)
    - Wenn Sie **In ein Speicherkonto archivieren** auswählen, wählen Sie **Speicherkonto - Konfigurieren** aus, und wählen Sie dann das Speicherkonto in Ihrem Azure-Abonnement aus.
        ![Archivieren in ein Azure-Speicherkonto](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - Wenn Sie **An einen Event Hub streamen** auswählen, wählen Sie **Event Hub – Konfigurieren** aus, und wählen Sie dann den Event Hubs-Namespace, den Event Hub und die Zugriffsrichtlinie aus.
        ![An einen Event Hub streamen](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
7. Wählen Sie **Speichern** aus. Wählen Sie dann in der rechten oberen Ecke **X** aus, um die Seite zu schließen.
8. Vergewissern Sie sich nun auf der Seite **Diagnoseeinstellungen**, dass ein neuer Eintrag in der Tabelle **Diagnoseeinstellungen** angezeigt wird.
    ![Diagnoseeinstellung in der Liste](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

Sie können auch die Erfassung aller **Metriken** für das Systemthema aktivieren.
    ![Systemthema: alle Metriken aktivieren](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>Anzeigen von Diagnoseprotokollen in Azure Storage

1. Nachdem Sie ein Speicherkonto als Erfassungsziel aktiviert haben, beginnt Event Grid mit der Ausgabe von Diagnoseprotokollen. Es sollten im Speicherkonto neue Container mit den Namen **insights-logs-deliveryfailures** und **insights-logs-publishfailures** angezeigt werden.

    ![Speicher: Container für Diagnoseprotokolle](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. Wenn Sie durch einen der Container navigieren, gelangen Sie zu einem Blob im JSON-Format. Die Datei enthält Protokolleinträge entweder für einen Zustellungs- oder Veröffentlichungsfehler. Der Navigationspfad stellt die **Ressourcen-ID** des Event Grid-Themas und den Zeitstempel (auf Minutenebene) dar, wann die Protokolleinträge ausgegeben wurden. Die Blob/JSON-Datei, die heruntergeladen werden kann, hält sich letztlich an das im nächsten Abschnitt beschriebene Schema.

    ![JSON-Datei im Speicher](./media/enable-diagnostic-logs-topic/select-json.png)(./media/enable-diagnostic-logs-topic/select-json.png)
3. Sie sollten den Inhalt in der JSON-Datei ähnlich wie im folgenden Beispiel sehen:

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```

## <a name="use-azure-resource-manager-template"></a>Verwenden von Azure Resource Manager-Vorlagen

Hier finden Sie eine exemplarische Azure Resource Manager-Vorlage zum Aktivieren von Diagnoseeinstellungen für ein Event Grid-Thema. Wenn Sie diese Beispielvorlage bereitstellen, werden die folgenden Ressourcen erstellt:

- Ein Event Grid-Thema
- Ein Log Analytics-Arbeitsbereich

Anschließend wird eine Diagnoseeinstellung für das Thema erstellt, um Diagnoseinformationen an den Log Analytics-Arbeitsbereich zu senden.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "topic_name": {
            "defaultValue": "spegrid0917topic",
            "type": "String"
        },
        "log_analytics_workspace_name": {
            "defaultValue": "splogaw0625",
            "type": "String"
        },
        "location": {
            "defaultValue": "eastus",
            "type": "String"
        },
        "sku": {
            "defaultValue": "Free",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.EventGrid/topics",
            "apiVersion": "2020-10-15-preview",
            "name": "[parameters('topic_name')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Basic"
            },
            "kind": "Azure",
            "identity": {
                "type": "None"
            },
            "properties": {
                "inputSchema": "EventGridSchema",
                "publicNetworkAccess": "Enabled"
            }
        },
        {
            "apiVersion": "2017-03-15-preview",
            "name": "[parameters('log_analytics_workspace_name')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.OperationalInsights/workspaces",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]"
                }
            }
        },
        {
            "type": "Microsoft.EventGrid/topics/providers/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[concat(parameters('topic_name'), '/', 'Microsoft.Insights/', parameters('log_analytics_workspace_name'))]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.EventGrid/topics', parameters('topic_name'))]",
                "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('log_analytics_workspace_name'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('log_analytics_workspace_name'))]",
                "metrics": [
                    {
                        "category": "AllMetrics",
                        "enabled": true
                    }
                ],
                "logs": [
                    {
                        "category": "DeliveryFailures",
                        "enabled": true
                    },
                    {
                        "category": "PublishFailures",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```

## <a name="enable-diagnostic-logs-for-audit-traces"></a>Aktivieren von Diagnoseprotokollen für Überwachungsablaufverfolgungen

Event Grid kann die Überwachungsablaufverfolgungen für Datenebenenvorgänge veröffentlichen, wenn die Kategorie **Gruppenüberwachung** oder die Kategorie **DataPlaneRequests** unter **Protokolle** ausgewählt ist. Die Überwachungsablaufverfolgung kann verwendet werden, um sicherzustellen, dass der Datenzugriff nur für autorisierte Zwecke zulässig ist, und dass nur Informationen zur Sicherheitssteuerung erfasst werden, wie z. B. der Ressourcenname, Vorgangstyp, Netzwerkzugriff, die Ebene und Region. Weitere Informationen zum Aktivieren der Diagnoseeinstellung finden Sie unter [Diagnoseprotokolle in Event Grid-Themen und -Domänen](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-event-grid-topics-and-event-grid-domains).
![Auswählen der Überwachungsablaufverfolgungen](./media/enable-diagnostic-logs-topic/enable-audit-logs.png)

> [!IMPORTANT]
> Weitere Informationen zum `DataPlaneRequests`-Schema finden Sie unter [Diagnoseprotokolle](diagnostic-logs.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Protokollschema und andere konzeptionelle Informationen zu Diagnoseprotokollen für Themen oder Domänen finden Sie unter [Diagnoseprotokolle](diagnostic-logs.md).
