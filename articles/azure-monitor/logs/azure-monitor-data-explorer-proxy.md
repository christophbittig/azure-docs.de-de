---
title: 'Ressourcenübergreifende Abfrage: Azure Data Explorer mithilfe von Azure Monitor'
description: Verwenden Sie Azure Monitor, um produktübergreifende Abfragen zwischen Azure Data Explorer, Log Analytics-Arbeitsbereichen und klassischen Application Insights-Anwendungen in Azure Monitor auszuführen.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 498fc101f257b05d24826cead8906b513ec34ccd
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122343666"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>Ressourcenübergreifende Abfrage: Azure Data Explorer mithilfe von Azure Monitor
Azure Monitor unterstützt dienstübergreifende Abfragen zwischen Azure Data Explorer, [Application Insights](../app/app-insights-overview.md) und [Log Analytics](../logs/data-platform-logs.md). Anschließend können Sie den Azure Data Explorer-Cluster mit Log Analytics/Application Insights-Tools abfragen und in einer dienstübergreifenden Abfrage darauf verweisen. Der Artikel zeigt, wie eine dienstübergreifende Abfrage durchgeführt wird.

Das folgende Diagramm zeigt den dienstübergreifenden Azure Monitor-Flow:

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Diagramm des Abfrageflows zwischen einem Benutzer, Azure Monitor, einem Proxy und Azure Data Explorer":::

>[!NOTE]
> Die dienstübergreifende Azure Monitor-Abfrage befindet sich in der öffentlichen Vorschau. Wenden Sie sich mit Fragen an das [Service Team](mailto:ADXProxy@microsoft.com).

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Übergreifende Abfrage von Log Analytics- oder Application Insights-Ressourcen und Azure Data Explorer

Sie können ressourcenübergreifende Abfragen mithilfe von Clienttools ausführen, die Kusto-Abfragen unterstützen. Beispiele für diese Tools sind die Log Analytics-Webbenutzeroberfläche, Workbooks, PowerShell und die REST-API.

Geben Sie den Bezeichner für einen Azure Data Explorer-Cluster in einer Abfrage innerhalb des `adx`-Musters gefolgt vom Datenbanknamen und der Tabelle ein.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Screenshot mit einem Beispiel einer dienstübergreifenden Abfrage":::

> [!NOTE]
>* Bei Datenbanknamen wird die Groß-/Kleinschreibung berücksichtigt.
>* Eine ressourcenübergreifende Abfrage als Warnung wird nicht unterstützt.
>* Das Identifizieren der Spalte „Timestamp“ im Cluster wird nicht unterstützt, die Log Analytics-Abfrage-API übergibt den Zeitfilter nicht.

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>Kombinieren von Azure Data Explorer-Clustertabellen mit einem Log Analytics-Arbeitsbereich

Verwenden Sie den Befehl `union`, um Clustertabellen mit einem Log Analytics-Arbeitsbereich zu kombinieren.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Screenshot mit einem Beispiel einer dienstübergreifenden Abfrage mit „union“-Befehl":::

> [!Tip]
> Kurzform ist zulässig: *ClusterName*/*InitialCatalog*. Beispielsweise wird `adx('help/Samples')` in `adx('help.kusto.windows.net/Samples')` übersetzt.

>[!Note]
> 
>* Wenn Sie anstelle von „union“ den [`join`-Operator](/azure/data-explorer/kusto/query/joinoperator) nutzen, müssen Sie einen [`hint`](/azure/data-explorer/kusto/query/joinoperator#join-hints) zum Kombinieren der Daten im Azure Data Explorer-Cluster mit dem Log Analytics-Arbeitsbereich verwenden.
>* Verwenden Sie „hint.remote={Richtung des Log Analytics-Arbeitsbereichs}“. Beispiel:
>```kusto
>AzureDiagnostics
>| join hint.remote=left adx("cluster=ClusterURI").AzureDiagnostics on (ColumnName)
>```

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Verknüpfen von Daten eines Azure Data Explorer-Clusters in einem Mandanten mit einer Azure Monitor-Ressource in einem anderen Mandanten

Mandantenübergreifende Abfragen zwischen den Diensten werden nicht unterstützt. Sie sind nur bei einem Mandanten angemeldet, um die Abfrage für beide Ressourcen auszuführen.

Wenn sich die Azure Data Explorer-Ressource im Mandanten A und der Log Analytics-Arbeitsbereich im Mandanten B befindet, sollten Sie eine der folgenden Methoden verwenden:

*  Mit Azure Data Explorer können Sie Rollen für Prinzipale auf unterschiedlichen Mandanten hinzufügen. Fügen Sie Ihre Benutzer-ID in Mandant B als autorisierten Benutzer im Azure Data Explorer-Cluster hinzu. Vergewissern Sie sich, dass die Eigenschaft [TrustedExternalTenant](/powershell/module/az.kusto/update-azkustocluster) im Azure Data Explorer-Cluster den Mandanten B enthält. Führen Sie die übergreifende Abfrage vollständig in Mandant B aus.
*  Verwenden Sie [Lighthouse](../../lighthouse/index.yml), um die Azure Monitor-Ressource in Mandant A zu projizieren.

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Herstellen einer Verbindung mit Azure Data Explorer-Clustern von unterschiedlichen Mandanten

Bei Kusto-Explorer werden Sie automatisch bei dem Mandanten angemeldet, zu dem das Benutzerkonto ursprünglich gehört. Für den Zugriff auf Ressourcen in anderen Mandanten mit demselben Benutzerkonto müssen Sie `TenantId` explizit in der Verbindungszeichenfolge angeben:

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>Nächste Schritte
* [Write queries](/azure/data-explorer/write-queries) (Schreiben von Abfragen)
* [Abfragen von Daten in Azure Monitor mit Azure Data Explorer](/azure/data-explorer/query-monitor-data)
* [Ausführen ressourcenübergreifender Protokollabfragen in Azure Monitor](../logs/cross-workspace-query.md)