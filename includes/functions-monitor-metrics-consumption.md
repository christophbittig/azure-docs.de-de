---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/04/2021
ms.author: glenga
ms.openlocfilehash: 34237e64aecfcd4059a9bc9fb3ae390281b3419a
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113567338"
---
#### <a name="portal"></a>[Portal](#tab/portal)

Verwenden Sie den [Azure Monitor-Metrik-Explorer](../articles/azure-monitor/essentials/metrics-getting-started.md), um kostenbezogene Daten für Ihre Funktions-Apps im Verbrauchstarif in einem grafischen Format anzuzeigen. 

1. Navigieren Sie im [Azure-Portal] zu Ihrer Funktions-App.

1. Scrollen Sie im linken Bereich nach unten zu **Überwachung,** , und wählen Sie die Option **Metriken** aus.  

1. Wählen Sie in **Metrik** die Einträge **Ausführungsanzahl für Funktion** und **Summe** für **Aggregation** aus. Dadurch wird die Summe der Ausführungen während des gewählten Zeitraums dem Diagramm hinzugefügt.

    ![Definieren einer Funktions-App-Metrik, um sie dem Diagramm hinzuzufügen](./media/functions-monitor-metrics-consumption/monitor-metrics-add-metric.png)

1. Wählen Sie **Metrik hinzufügen** aus, und wiederholen Sie die Schritte 2–4, um dem Diagramm **Ausführungseinheiten für Funktion** hinzuzufügen. 

Das resultierende Diagramm enthält die Summen für beide Ausführungsmetriken im ausgewählten Zeitraum, der in diesem Fall zwei Stunden beträgt.

![Diagramm der Funktionsausführungen und Ausführungseinheiten](./media/functions-monitor-metrics-consumption/monitor-metrics-execution-sum.png)

Da die Anzahl der Ausführungseinheiten so viel höher ist, als die Ausführungen, zeigt das Diagramm nur die Ausführungseinheiten an.

Dieses Diagramm zeigt eine Gesamtzahl von 1,11 Mrd. `Function Execution Units`, die in einem Zeitraum von zwei Stunden verbraucht wurden, gemessen in MB-Millisekunden. Um den Wert in GB-Sekunden umzuwandeln, teilen Sie durch 1.024.000. In diesem Beispiel hat die Funktions-App `1110000000 / 1024000 = 1083.98` GB-Sekunden verbraucht. Sie können diesen Wert mit dem aktuellen Preis der Ausführungszeit auf der [Seite mit den Preisen für Azure Functions](https://azure.microsoft.com/pricing/details/functions/) multiplizieren, wodurch Sie die Kosten für diese zwei Stunden erhalten – vorausgesetzt, Sie haben bereits alle zugewiesenen kostenlosen Ausführungszeiten verbraucht. 

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azurecli)

Die [Azure CLI](/cli/azure/) enthält Befehle zum Abrufen von Metriken. Sie können die CLI aus einer lokalen Befehlsumgebung heraus oder direkt über das Portal mithilfe von [Azure Cloud Shell](../articles/cloud-shell/overview.md) verwenden. Beispielsweise gibt der folgende [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list)-Befehl stündlich Daten aus demselben zuvor verwendeten Zeitraum zurück.

Stellen Sie sicher, dass Sie `<AZURE_SUBSCRIPTON_ID>` durch Ihre Azure-Abonnement-ID ersetzen, wenn Sie den Befehl ausführen.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Dieser Befehl gibt eine JSON-Nutzlast zurück, die in etwa wie folgt aussieht:

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Diese spezifische Antwort zeigt, dass die App von `2019-09-11T21:46` bis `2019-09-11T23:18` 1.110.000.000 MB-Millisekunden (1.083,98 GB-Sekunden) verbraucht hat.

---

[Azure portal]: https://portal.azure.com