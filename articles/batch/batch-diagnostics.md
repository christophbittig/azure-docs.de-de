---
title: Metriken, Warnungen und Diagnoseprotokolle
description: Zeichnen Sie Diagnoseprotokollereignisse für Azure Batch-Kontoressourcen wie Pools und Tasks auf, und analysieren Sie sie.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: seodec18
ms.openlocfilehash: 3bbaf01b71a2256416112f5e365f70d563a9f36d
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2021
ms.locfileid: "109753942"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch-Metriken, -Warnungen und -Protokolle für die Diagnoseauswertung und -überwachung

Azure Monitor erfasst [Metriken](../azure-monitor/essentials/data-platform-metrics.md) und [Diagnoseprotokolle](../azure-monitor/essentials/platform-logs-overview.md) für Ressourcen in Ihrem Azure Batch-Konto.

Sie können diese Daten mithilfe verschiedener Methoden erfassen und nutzen, um Ihr Batch-Konto zu überwachen und Probleme zu diagnostizieren. Sie können auch [Metrikwarnungen](../azure-monitor/alerts/alerts-overview.md) konfigurieren, um Benachrichtigungen zu erhalten, wenn eine Metrik einen angegebenen Wert erreicht.

## <a name="batch-metrics"></a>Batch-Metriken

Bei [Metriken](../azure-monitor/essentials/data-platform-metrics.md) handelt es sich um von Azure-Ressourcen ausgegebene Azure-Telemetriedaten (auch als Leistungsindikatoren bezeichnet), die vom Azure Monitor-Dienst verwendet werden. Zu den Beispielmetriken in einem Batch-Konto zählen „Poolerstellungsereignisse“, „Anzahl der Knoten mit niedriger Priorität“ und „Taskabschlussereignisse“. Diese Metriken können dabei helfen, Trends zu identifizieren, und lassen sich zudem für die Datenanalyse verwenden.

Sehen Sie sich die [Liste der unterstützten Batch-Metriken](../azure-monitor/essentials/metrics-supported.md#microsoftbatchbatchaccounts) an.

Für Metriken gilt Folgendes:

- Sie sind in jedem Batch-Konto ohne zusätzliche Konfiguration standardmäßig aktiviert.
- Sie werden minütlich generiert.
- Sie werden nicht automatisch dauerhaft gespeichert, sondern decken jeweils nur die letzten 30 Tage ab. Sie können Aktivitätsmetriken im Rahmen der Diagnoseprotokollierung dauerhaft speichern.

## <a name="view-batch-metrics"></a>Anzeigen von Batch-Metriken

Im Azure-Portal auf der Seite **Übersicht** für das Batch-Konto werden standardmäßig wichtige Metriken zu Knoten, Kernen und Aufgaben angezeigt.

So zeigen Sie weitere Metriken für ein Batch-Konto an:

1. Wählen Sie im Azure-Portal **Alle Dienste** > **Batch-Konten** und anschließend den Namen Ihres Batch-Kontos aus.
1. Wählen Sie unter **Überwachung** die Option **Metriken**.
1. Wählen Sie **Metrik hinzufügen** und dann in der Dropdownliste eine Metrik aus.
1. Wählen Sie eine Option für die **Aggregation** für die Metrik aus. Verwenden Sie für anzahlbasierte Metriken (z. B. „Dedizierte Anzahl von Kernen“ oder „Anzahl der Knoten mit niedriger Priorität“) die Aggregation **Durchschnitt**. Verwenden Sie für ereignisbasierte Metriken (z. B. „Ereignisse zum Abschluss der Größenänderung von Pools“) die Aggregation **Anzahl**. Vermeiden Sie die Verwendung der Aggregation **Summe**, mit der die Werte aller über den Zeitraum des Diagramms empfangenen Datenpunkte addiert werden.
1. Um zusätzliche Metriken hinzuzufügen, wiederholen Sie die Schritte 3 und 4.

Sie können Metriken mit den Azure Monitor-APIs auch programmgesteuert abrufen. Sehen Sie sich beispielsweise die Informationen unter [Retrieve Azure Monitor metrics with .NET](/samples/azure-samples/monitor-dotnet-metrics-api/monitor-dotnet-metrics-api/) (Abrufen von Azure Monitor-Metriken mit .NET) an.

> [!NOTE]
> Metriken, die in den letzten drei Minuten ausgegeben wurden, werden möglicherweise immer noch aggregiert, sodass Werte während dieses Zeitraums möglicherweise zu niedrig angegeben werden. Die Metrikbereitstellung ist nicht garantiert und kann durch Bereitstellung in falscher Reihenfolge, Datenverlust oder Datenduplizierung beeinflusst werden.

## <a name="batch-metric-alerts"></a>Batch-Metrikwarnungen

Sie können Metrikwarnungen nahezu in Echtzeit konfigurieren, die ausgelöst werden, wenn der Wert für eine bestimmte Metrik einen von Ihnen definierten Schwellenwert überschreitet. Die Warnung generiert eine Benachrichtigung, wenn die Warnung den Status „Aktiviert“ (nach Überschreitung des Schwellenwerts und Erfüllung der Warnungsbedingung) oder „Gelöst“ (nach der erneuten Überschreitung des Schwellenwerts und Nichterfüllung der Bedingung) hat.

Da bei der Metrikbereitstellung Inkonsistenzen durch Bereitstellung in falscher Reihenfolge, Datenverlust oder Datenduplizierung auftreten können, sollten Warnungen vermieden werden, die für einen einzelnen Datenpunkt ausgelöst werden. Verwenden Sie stattdessen Schwellenwerte, um Inkonsistenzen durch Bereitstellung in falscher Reihenfolge, Datenverlust und Datenduplizierung über einen bestimmten Zeitraum zu berücksichtigen.

Beispiel: Es empfiehlt sich, eine Metrikwarnung für den Fall zu konfigurieren, dass die Anzahl für Kerne mit niedriger Priorität auf einen bestimmten Wert sinkt, sodass Sie die Zusammensetzung der Pools anpassen können. Die besten Ergebnisse werden erzielt, indem Sie einen Zeitraum von mindestens 10 Minuten festlegen, in dem Warnungen ausgelöst werden, wenn die durchschnittliche Anzahl von Kernen mit niedriger Priorität unter den Schwellenwert für den gesamten Zeitraum fällt. Dadurch bleibt Zeit für die Aggregation von Metriken, sodass Sie genauere Ergebnisse erhalten.

So konfigurieren Sie eine Metrikwarnung im Azure-Portal

1. Wählen Sie **Alle Dienste** > **Batch-Konten** und anschließend den Namen Ihres Batch-Kontos aus.
1. Wählen Sie unter **Überwachen** **Warnungen** und dann **Neue Warnungsregel** aus.
1. Wählen Sie **Bedingung hinzufügen** und dann eine Metrik aus.
1. Wählen Sie die gewünschten Werte für **Diagrammzeitraum**, **Schwellenwert**, **Operator** und **Aggregationstyp** aus.
1. Geben Sie einen **Schwellenwert** ein, und wählen Sie die **Einheit** für den Schwellenwert aus.  Wählen Sie dann **Fertig** aus.
1. Fügen Sie der Warnung eine [Aktionsgruppe](../azure-monitor/alerts/action-groups.md) hinzu, indem Sie entweder eine bestehende Aktionsgruppe auswählen oder eine neue Aktionsgruppe erstellen.
1. Geben Sie im Abschnitt **Details zur Warnungsregel** einen **Namen der Warnungsregel** und eine **Beschreibung** ein. Wenn die Warnung sofort aktiviert werden soll, muss das Kontrollkästchen **Warnungsregel bei Erstellung aktivieren** aktiviert werden.
1. Wählen Sie **Warnungsregel erstellen** aus.

Weitere Informationen zum Erstellen von Metrikwarnungen finden Sie unter [Informationen zur Funktionsweise von Metrikwarnungen in Azure Monitor](../azure-monitor/alerts/alerts-metric-overview.md) und [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](../azure-monitor/alerts/alerts-metric.md).

Sie können auch mithilfe der [REST-API von Azure Monitor](/rest/api/monitor/) eine Warnung nahezu in Echtzeit konfigurieren. Weitere Informationen finden Sie unter [Überblick über Warnungen in Microsoft Azure](../azure-monitor/alerts/alerts-overview.md). Wenn Sie auftrags-, task- oder poolspezifische Informationen in Ihre Warnungen einschließen möchten, finden Sie weitere Informationen unter [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../azure-monitor/alerts/alerts-log.md).

## <a name="batch-diagnostics"></a>Batch-Diagnose

[Diagnoseprotokolle](../azure-monitor/essentials/platform-logs-overview.md) enthalten von Azure-Ressourcen ausgegebene Informationen, die die Vorgänge der einzelnen Ressourcen beschreiben. In Batch können Sie die folgenden Protokolle erfassen:

- **ServiceLog**: Ereignisse, die während der Lebensdauer einer einzelnen Ressource, z. B. eines Pools oder einer Task, [vom Batch-Dienst ausgegeben wurden](#service-log-events).
- **AllMetrics**: Metriken auf Batch-Kontoebene.

Sie müssen die Diagnoseeinstellungen für jedes Batch-Konto, das Sie überwachen möchten, explizit aktivieren.

### <a name="log-destination-options"></a>Optionen für das Protokollziel

Ein häufiges Szenario ist die Auswahl eines Azure Storage-Kontos als Protokollziel. Erstellen Sie zum Speichern von Protokollen in Azure Storage das Konto, bevor Sie die Erfassung von Protokollen erstellen. Wenn Sie Ihrem Batch-Konto ein Speicherkonto zugewiesen haben, können Sie das Konto als Protokollziel auswählen.

Alternativ haben Sie folgende Möglichkeiten:

- Streamen Sie Batch-Diagnoseprotokolle an einen [Azure Event Hub](../event-hubs/event-hubs-about.md). Event Hubs können mit einem beliebigen Echtzeitanalyse-Anbieter Millionen Ereignisse pro Sekunde erfassen und anschließend transformieren und speichern.
- Senden Sie Diagnoseprotokolle an [Azure Monitor-Protokolle](../azure-monitor/logs/log-query-overview.md). Hier können Sie sie analysieren oder zur Analyse in Power BI oder Excel exportieren.

> [!NOTE]
> Beim Speichern oder Verarbeiten von Diagnoseprotokolldaten mit Azure-Diensten fallen unter Umständen zusätzliche Kosten an.

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Aktivieren der Erfassung von Batch-Diagnoseprotokollen

Führen Sie zum Erstellen einer neuen Diagnoseeinstellung mit dem Azure-Portal die folgenden Schritte aus.

1. Wählen Sie im Azure-Portal **Alle Dienste** > **Batch-Konten** und anschließend den Namen Ihres Batch-Kontos aus.
2. Wählen Sie unter **Überwachung** die Option **Diagnoseeinstellungen** aus.
3. Wählen Sie auf der Seite **Diagnoseeinstellungen** den Eintrag **Diagnoseeinstellung hinzufügen** aus.
4. Geben Sie einen Namen für die Einstellung ein.
5. Wählen Sie ein Ziel aus: **An Log Analytics senden**, **In einem Speicherkonto archivieren** und **An einen Event Hub streamen**. Wenn Sie ein Speicherkonto auswählen, können Sie für jedes Protokoll optional die Anzahl der Tage auswählen, für die Daten aufbewahrt werden sollen. Wenn Sie keine Anzahl von Tagen für die Aufbewahrung angeben, werden die Daten für die Lebensdauer des Speicherkontos aufbewahrt.
6. Wählen Sie **ServiceLog**, **AllMetrics** oder beides aus.
7. Wählen Sie **Speichern** aus, um die Diagnoseeinstellungen zu erstellen.

Sie können die Protokollerfassung auch durch [Erstellen von Diagnoseeinstellungen im Azure-Portal](../azure-monitor/essentials/diagnostic-settings.md) mit einer [Resource Manager-Vorlage](../azure-monitor/essentials/resource-manager-diagnostic-settings.md) oder mithilfe von Azure PowerShell bzw. der Azure CLI aktivieren. Weitere Informationen finden Sie in der [Übersicht über Protokolle der Azure-Plattform](../azure-monitor/essentials/platform-logs-overview.md).

### <a name="access-diagnostics-logs-in-storage"></a>Zugreifen auf Diagnoseprotokolle im Speicher

Wenn Sie [Batch-Diagnoseprotokolle in einem Speicherkonto archivieren](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage), wird ein Speichercontainer im Speicherkonto erstellt, sobald ein verwandtes Ereignis auftritt. Blobs werden gemäß dem folgenden Benennungsmuster erstellt:

```json
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Beispiel:

```json
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```

Jede Blobdatei vom Typ `PT1H.json` enthält Ereignisse im JSON-Format, die innerhalb der in der Blob-URL angegebenen Stunde (Beispiel: `h=12`) aufgetreten sind. Während der aktuellen Stunde werden Ereignisse an die Datei `PT1H.json` angefügt, sobald sie auftreten. Der Minutenwert (`m=00`) ist immer `00`, da Diagnoseprotokollereignisse stundenweise in einzelne Blobs unterteilt werden. (Alle Zeitangaben sind in UTC.)

Unten sehen Sie ein Beispiel für einen `PoolResizeCompleteEvent`-Eintrag in einer `PT1H.json`-Protokolldatei. Er umfasst Informationen über die aktuelle und die vorgegebene Anzahl von dedizierten Knoten und Knoten mit niedriger Priorität sowie die Start- und Endzeit des Vorgangs:

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Verwenden Sie zum programmgesteuerten Zugriff auf die Protokolle in Ihrem Speicherkonto die Storage-APIs.

### <a name="service-log-events"></a>Dienstprotokollereignisse

Azure Batch-Dienstprotokolle enthalten Ereignisse, die während der Lebensdauer einer einzelnen Batch-Ressource wie eines Pools oder einer Task ausgegeben werden. Jedes von Batch ausgegebene Ereignis wird im JSON-Format protokolliert. Dies ist beispielsweise der Text eines Beispiel für ein **Poolerstellungsereignis**:

```json
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Die vom Batch-Dienst ausgegebenen Dienstprotokollereignisse umfassen Folgendes:

- [Erstellung eines Pools](batch-pool-create-event.md)
- [Start des Löschvorgangs eines Pools](batch-pool-delete-start-event.md)
- [Abschluss des Löschvorgangs eines Pools](batch-pool-delete-complete-event.md)
- [Start der Größenänderung eines Pools](batch-pool-resize-start-event.md)
- [Abschluss der Größenänderung eines Pools](batch-pool-resize-complete-event.md)
- [Autoskalierung von Pools](batch-pool-autoscale-event.md)
- [Taskstart](batch-task-start-event.md)
- [Taskabschluss](batch-task-complete-event.md)
- [Taskfehler](batch-task-fail-event.md)
- [Taskzeitplan-Fehler](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Batch-APIs und Tools](batch-apis-tools.md), die für die Erstellung von Batch-Lösungen verfügbar sind.
- Weitere Informationen finden Sie unter [Monitor Batch solutions](monitoring-overview.md) (Überwachen von Batch-Lösungen).
