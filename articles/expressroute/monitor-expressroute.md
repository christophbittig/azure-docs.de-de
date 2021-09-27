---
title: Überwachen von Azure ExpressRoute
description: Beginnen Sie hier, um zu erfahren, wie Sie Azure ExpressRoute überwachen können.
services: expressroute
author: duongau
ms.author: duau
ms.service: expressroute
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/22/2021
ms.openlocfilehash: 2e54166f71071a21b84e5f58e27c4ebc8a7df03a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587856"
---
# <a name="monitoring-azure-expressroute"></a>Überwachen von Azure ExpressRoute

Wenn Sie über unternehmenskritische Anwendungen und Geschäftsprozesse verfügen, die auf Azure-Ressourcen beruhen, sollten Sie Verfügbarkeit, Leistung und Betrieb dieser Ressourcen überwachen. 

Dieser Artikel beschreibt die von Azure ExpressRoute generierten Überwachungsdaten. Azure ExpressRoute verwendet [Azure Monitor](../azure-monitor/overview.md). Wenn Sie nicht mit den Features von Azure Monitor vertraut sind, die Bestandteil aller Azure-Dienste sind, für die Azure Monitor verwendet wird, lesen Sie den Artikel [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
 
## <a name="expressroute-insights"></a>ExpressRoute-Erkenntnisse

Für einige Dienste in Azure enthält das Azure-Portal ein spezielles, vorgefertigtes Überwachungsdashboard, das als Ausgangspunkt für die Überwachung Ihres Diensts dient. Diese speziellen Dashboards werden als „Insights“ (Erkenntnisse) bezeichnet.

ExpressRoute verwendet Netzwerkerkenntnisse, um eine detaillierte Topologiezuordnung aller ExpressRoute-Komponenten (Peerings, Verbindungen, Gateways) im Verhältnis zueinander zu erstellen. Netzwerkerkenntnisse für ExpressRoute verfügen auch über ein vorab geladenes Metrikdashboard für Verfügbarkeit, Durchsatz, Paketverluste und Gatewaymetriken. Weitere Informationen finden Sie unter [Azure ExpressRoute-Erkenntnisse mithilfe von Netzwerkerkenntnissen](expressroute-network-insights.md).

## <a name="monitoring-data"></a>Überwachungsdaten 

Azure ExpressRoute sammelt dieselben Arten von Überwachungsdaten wie andere Azure-Ressourcen, die in [Überwachungsdaten von Azure-Ressourcen](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) beschrieben werden. 

Ausführliche Informationen zu den Metriken und Protokollen, die von Azure ExpressRoute erstellt werden, finden Sie in der [Referenz zum Überwachen von Azure ExpressRoute-Daten](monitor-expressroute-reference.md).

## <a name="collection-and-routing"></a>Sammlung und Routing

Plattformmetriken und das Aktivitätsprotokoll werden automatisch erfasst und gespeichert, können jedoch mithilfe einer Diagnoseeinstellung an andere Speicherorte weitergeleitet werden.  

Ressourcenprotokolle werden erst erfasst und gespeichert, nachdem Sie eine Diagnoseeinstellung erstellt und die Protokolle an mindestens einen Speicherort weitergeleitet haben.

Ausführliche Informationen zum Erstellen einer Diagnoseeinstellung über das Azure-Portal, die Befehlszeilenschnittstelle oder PowerShell finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Plattformprotokollen und Metriken in Azure](../azure-monitor/essentials/diagnostic-settings.md). Wenn Sie eine Diagnoseeinstellung erstellen, legen Sie fest, welche Kategorien von Protokollen gesammelt werden sollen. Die Kategorien für *Azure ExpressRoute* sind in [Referenz zum Überwachen von Azure ExpressRoute-Daten](monitor-expressroute-reference.md#resource-logs) aufgeführt.

> [!IMPORTANT]
> Ein Aktivieren dieser Einstellungen erfordert zusätzliche Azure-Dienste (Speicherkonto, Event Hub oder Log Analytics). Dadurch können sich Ihre Kosten erhöhen. Um geschätzte Kosten zu berechnen, wechseln Sie zum [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator).

In den folgenden Abschnitten werden die Metriken und Protokolle behandelt, die Sie erfassen können.

## <a name="analyzing-metrics"></a>Analysieren von Metriken

Sie können Metriken für *Azure ExpressRoute* mit Metriken aus anderen Azure-Diensten mithilfe des Metrik-Explorers analysieren, indem Sie im Menü **Azure Monitor** die Option **Metriken** öffnen. Ausführliche Informationen zur Verwendung dieses Tools finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](../azure-monitor/essentials/metrics-getting-started.md). 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/metrics-page.png" alt-text="Screenshot: Metrikdashboard für ExpressRoute":::

Sie können zur Referenz auf eine Liste [aller in Azure Monitor unterstützter Ressourcenmetriken](../azure-monitor/essentials/metrics-supported.md) anzeigen.

* Wenn Sie **ExpressRoute**-Metriken anzeigen möchten, filtern Sie nach dem Ressourcentyp *ExpressRoute-Leitungen*. 
* Um **Global Reach**-Metriken anzuzeigen, filtern Sie nach dem Ressourcentyp *ExpressRoute-Leitungen*, und wählen Sie eine ExpressRoute-Leitungsressource aus, bei der Global Reach aktiviert ist. 
* Um **ExpressRoute Direct**-Metriken anzuzeigen, filtern Sie nach dem Ressourcentyp *ExpressRoute-Ports*. 

Nach dem Auswählen einer Metrik wird die Standardaggregation angewendet. Optional können Sie die Teilung anwenden, wonach die Metrik mit anderen Dimensionen angezeigt wird.

## <a name="analyzing-logs"></a>Analysieren von Protokollen

Daten in Azure Monitor-Protokollen werden in Tabellen gespeichert, wobei jede Tabelle ihren eigenen Satz an eindeutigen Eigenschaften hat.  

Alle Ressourcenprotokolle in Azure Monitor enthalten dieselben Felder, gefolgt von dienstspezifischen Feldern. Das allgemeine Schema wird in [Azure Monitor-Ressourcenprotokollschema](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema) beschrieben. Das Schema für ExpressRoute-Ressourcenprotokolle finden Sie in der [Azure ExpressRoute-Datenreferenz](monitor-expressroute-reference.md#schemas). 

Das [Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md) ist eine Plattformprotokollierung, die einen Einblick in Ereignisse auf Abonnementebene ermöglicht. Sie können es unabhängig anzeigen oder an Azure Monitor-Protokolle weiterleiten, in denen Sie mithilfe von Log Analytics viel komplexere Abfragen durchführen können.

In ExpressRoute werden Daten in den folgenden Tabellen gespeichert.

| Tabelle | BESCHREIBUNG |
| ----- | ----------- |
| AzureDiagnostics | Allgemeine Tabelle, die von mehreren Diensten verwendet wird, um Ressourcenprotokolle zu speichern. Ressourcenprotokolle aus ExpressRoute können mit `MICROSOFT.NETWORK` identifiziert werden. |
| AzureMetrics | Von ExpressRoute ausgegebene Metrikdaten zur Messung von Integrität und Leistung. 

Navigieren Sie zum Anzeigen dieser Tabellen zu Ihrer ExpressRoute-Leitungsressource, und wählen Sie unter *Überwachung* die Option **Protokolle** aus.

### <a name="sample-kusto-queries"></a>Kusto-Beispielabfragen

Hier sind einige Abfragen, die Sie in die Protokollsuchleiste eingeben können, um die Überwachung Ihrer Azure ExpressRoute-Ressourcen zu vereinfachen. Diese Abfragen arbeiten mit der [neuen Sprache](../azure-monitor/logs/log-query-overview.md).

* So fragen Sie die BGP-Routingtabelle ab, die in den letzten 12 Stunden gelernt wurde.

    ```Kusto
    AzureDiagnostics
    | where TimeGenerated > ago(12h)
    | where ResourceType == "EXPRESSROUTECIRCUITS"
    | project TimeGenerated, ResourceType , network_s, path_s, OperationName
    ```

* So fragen Sie BGP-Informationsmeldungen nach Ebene, Ressourcentyp und Netzwerk ab.

    ```Kusto
    AzureDiagnostics
    | where Level == "Informational"
    | where ResourceType == "EXPRESSROUTECIRCUITS"
    | project TimeGenerated, ResourceId , Level, ResourceType , network_s, path_s
    ```

* So fragen Sie nach dem Datenverkehrsgraphen „BitInPerSeconds“ in der letzten Stunde ab.
    
    ```Kusto
    AzureMetrics
    | where MetricName == "BitsInPerSecond"
    | summarize by Average, bin(TimeGenerated, 1h), Resource
    | render timechart
    ```

* So fragen Sie nach dem Datenverkehrsgraphen „BitOutPerSeconds“ in der letzten Stunde ab.
    
    ```Kusto
    AzureMetrics
    | where MetricName == "BitsOutPerSecond"
    | summarize by Average, bin(TimeGenerated, 1h), Resource
    | render timechart
    ```

* So fragen Sie den Graphen von ArpAvailability in Intervallen von 5 Minuten ab.
    
    ```Kusto
    AzureMetrics
    | where MetricName == "ArpAvailability"
    | summarize by Average, bin(TimeGenerated, 5m), Resource
    | render timechart
    ```

* So fragen Sie den Graphen der BGP-Verfügbarkeit in Intervallen von 5 Minuten ab.

    ```Kusto
    AzureMetrics
    | where MetricName == "BGPAvailability"
    | summarize by Average, bin(TimeGenerated, 5m), Resource
    | render timechart
    ```

## <a name="alerts"></a>Alerts

Azure Monitor-Warnungen informieren Sie proaktiv, wenn wichtige Bedingungen in Ihren Überwachungsdaten gefunden werden. Sie ermöglichen Ihnen, Probleme in Ihrem System zu identifizieren und zu beheben, bevor Ihre Kunden sie bemerken. Sie können Warnungen für [Metriken](../azure-monitor/alerts/alerts-metric-overview.md), [Protokolle](../azure-monitor/alerts/alerts-unified-log.md) und das [Aktivitätsprotokoll](../azure-monitor/alerts/activity-log-alerts.md) festlegen. Verschiedene Arten von Warnungen haben Vor- und Nachteile.

In der folgenden Tabelle sind allgemeine und empfohlene Warnungsregeln für ExpressRoute aufgeführt:

| Warnungstyp | Bedingung | BESCHREIBUNG  |
|:---|:---|:---|
| ARP-Verfügbarkeit ist nicht verfügbar | Dimensionsname: Peeringtyp, Aggregationstyp: Avg, Operator: Kleiner als, Schwellenwert: 100 % | Wenn die ARP-Verfügbarkeit für einen Peeringtyp nicht verfügbar ist. |
| BGP-Verfügbarkeit ist nicht verfügbar | Dimensionsname: Peer, Aggregationstyp: Avg, Operator: Kleiner als, Schwellenwert: 100 % | Wenn die BGP-Verfügbarkeit für einen Peer nicht verfügbar ist. |

### <a name="alerts-for-expressroute-gateway-connections"></a>Warnungen zu ExpressRoute-Gatewayverbindungen

1. Navigieren Sie zum Konfigurieren von Warnungen zu **Azure Monitor**, und klicken Sie auf **Warnungen**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alerts":::
2. Klicken Sie auf **+ Ziel auswählen**, und wählen Sie die Ressource für die ExpressRoute-Gatewayverbindung aus.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="Ziel":::
3. Definieren Sie die Warnungsdetails.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="Aktionsgruppe":::
4. Definieren Sie die Aktionsgruppe, und fügen Sie sie hinzu.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="Hinzufügen einer Aktionsgruppe":::


### <a name="alerts-based-on-each-peering"></a>Warnungen basierend auf dem jeweiligen Peering

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="Jedes Peering":::

### <a name="configure-alerts-for-activity-logs-on-circuits"></a>Konfigurieren von Warnungen für Aktivitätsprotokolle zu Leitungen

In **Warnungskriterien** können Sie **Aktivitätsprotokoll** als Signaltyp und dann das Signal auswählen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="Aktivitätsprotokolle":::

## <a name="next-steps"></a>Nächste Schritte

* Eine Referenz zu den Metriken, Protokollen und anderen wichtigen Werten, die von ExpressRoute erstellt werden, finden Sie in der [Referenz zu ExpressRoute -Überwachungsdaten](monitor-expressroute-reference.md).
* Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/overview.md).
