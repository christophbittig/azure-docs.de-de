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
ms.openlocfilehash: 4f07659381c852e35585b5127d97c932a05088c8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355295"
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

### <a name="aggregation-types"></a>Aggregationstypen

Der Metrik-Explorer unterstützt die folgenden [Aggregationstypen](../azure-monitor/essentials/metrics-charts.md#aggregation): SUM, MAX, MIN, AVG und COUNT. Verwenden Sie den empfohlenen Aggregationstyp, wenn Sie die Erkenntnisse für die einzelnen ExpressRoute-Metriken überprüfen.

* Sum: Die Summe aller Werte, die während des Aggregationsintervalls erfasst wurden. 
* Count: Die Anzahl der Messwerte, die während des Aggregationsintervalls erfasst wurden. 
* Average: Der Durchschnitt der Metrikwerte, die während des Aggregationsintervalls erfasst wurden. 
* Min: Der niedrigste Wert, der während des Aggregationsintervalls erfasst wurde. 
* Max: Der höchste Wert, der während des Aggregationsintervalls erfasst wurde.

>[!NOTE]
>Die Verwendung **klassischer Metriken** wird nicht empfohlen.
>

### <a name="circuits-metrics"></a>Leitungsmetriken

#### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Ein- und ausgehende Bits – Metriken über alle Peerings

Aggregationstyp: *Avg*

Sie können Metriken über alle Peerings hinweg für eine bestimmte ExpressRoute-Leitung anzeigen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="Metriken zu Leitungen":::

#### <a name="bits-in-and-out---metrics-per-peering"></a>Ein- und ausgehende Bits – Metriken pro Peering

Aggregationstyp: *Avg*

Sie können Metriken für privates, öffentliches und Microsoft-Peering in Bits pro Sekunde anzeigen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="Peeringspezifische Metriken":::

#### <a name="bgp-availability---split-by-peer"></a>BGP-Verfügbarkeit – Aufgeteilt nach Peer  

Aggregationstyp: *Avg*

Sie können die BGP-Verfügbarkeit (Layer 3-Konnektivität) nahezu in Echtzeit über Peerings und Peers (primäre und sekundäre ExpressRoute-Router) hinweg anzeigen. Dieses Dashboard zeigt, dass die primäre BGP-Sitzung für privates Peering bereit und die sekundäre BGP-Sitzung für privates Peering nicht verfügbar ist. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="BGP-Verfügbarkeit pro Peer":::

### <a name="arp-availability---split-by-peering"></a>ARP-Verfügbarkeit – Aufgeteilt nach Peering  

Aggregationstyp: *Avg*

Sie können die [ARP](./expressroute-troubleshooting-arp-resource-manager.md)-Verfügbarkeit (Layer 3-Konnektivität) nahezu in Echtzeit über Peerings und Peers (primäre und sekundäre ExpressRoute-Router) hinweg anzeigen. Dieses Dashboard zeigt, dass die ARP-Sitzung für privates Peering für beide Peers bereit, aber für das Microsoft-Peering für beide Peers nicht verfügbar ist. Die Standardaggregation (Durchschnitt) wurde für beide Peers verwendet.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="ARP-Verfügbarkeit pro Peer":::

### <a name="expressroute-direct-metrics"></a>ExpressRoute Direct-Metriken

#### <a name="admin-state---split-by-link"></a>Administratorstatus nach Link

Aggregationstyp: *Avg*

Sie können den Administratorstatus für jede Verbindung des ExpressRoute Direct-Portpaars anzeigen. Der Administratorstatus zeigt an, ob der physische Port aktiviert oder deaktiviert ist. Dieser Zustand ist erforderlich, um Datenverkehr über die ExpressRoute Direct-Verbindung zu übergeben.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="ExpressRoute Direct: Administratorstatus":::

#### <a name="bits-in-per-second---split-by-link"></a>Eingehende Bits pro Sekunde – Aufgeteilt nach Link

Aggregationstyp: *Avg*

Sie können die eingehenden Bits pro Sekunde über beide Links des ExpressRoute Direct-Portpaars anzeigen. Überwachen Sie dieses Dashboard, um die Eingangsbandbreite für beide Verbindungen zu vergleichen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ExpressRoute Direct: eingehende Bits pro Sekunde":::

#### <a name="bits-out-per-second---split-by-link"></a>Ausgehende Bits pro Sekunde – Aufgeteilt nach Link

Aggregationstyp: *Avg*

Sie können auch die ausgehenden Bits pro Sekunde über beide Links des ExpressRoute Direct-Portpaars anzeigen. Überwachen Sie dieses Dashboard, um die Ausgangsbandbreite für beide Verbindungen zu vergleichen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ExpressRoute Direct: ausgehende Bits pro Sekunde":::

#### <a name="line-protocol---split-by-link"></a>Zeilenprotokoll – Aufgeteilt nach Link

Aggregationstyp: *Avg*

Sie können das Zeilenprotokoll für jeden Link des ExpressRoute Direct-Portpaars anzeigen. Das Leitungsprotokoll gibt an, ob die physische Leitung einsatzbereit ist und über ExpressRoute Direct ausgeführt wird. Überwachen Sie dieses Dashboard, und legen Sie Warnungen fest, um bei einem Ausfall der physischen Verbindung benachrichtigt zu werden.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ExpressRoute Direct: Zeilenprotokoll":::

#### <a name="rx-light-level---split-by-link"></a>Rx-Lichtpegel – Aufgeteilt nach Link

Aggregationstyp: *Avg*

Sie können den Rx-Lichtpegel (vom ExpressRoute Direct-Port **empfangene** Lichtintensität) für jeden Port anzeigen. Wenn keine Fehler vorliegen, liegt der Rx-Leistungspegel in der Regel innerhalb eines Bereichs von -10 dBm bis 0 dBm. Legen Sie Warnungen fest, um benachrichtigt zu werden, wenn der Rx-Leistungspegel außerhalb des fehlerfreien Bereichs liegt.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ExpressRoute Direct: Empfangslichtpegel":::

#### <a name="tx-light-level---split-by-link"></a>Tx-Lichtpegel – Aufgeteilt nach Link

Aggregationstyp: *Avg*

Sie können den Tx-Lichtpegel (vom ExpressRoute Direct-Port **übertragene** Lichtintensität) für jeden Port anzeigen. Wenn keine Fehler vorliegen, liegt der Tx-Leistungspegel in der Regel innerhalb eines Bereichs von -10 dBm bis 0 dBm. Legen Sie Warnungen fest, um benachrichtigt zu werden, wenn der Tx-Leistungspegel außerhalb des fehlerfreien Bereichs liegt.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ExpressRoute Direct: Sendelichtpegel":::

### <a name="expressroute-virtual-network-gateway-metrics"></a>Metriken zum ExpressRoute-Gateway für virtuelle Netzwerke

Aggregationstyp: *Avg*

Wenn Sie ein ExpressRoute-Gateway bereitstellen, verwaltet Azure die Computeressourcen und Funktionen Ihres Gateways. Ihnen stehen sechs Gatewaymetriken zur Verfügung, um die Leistung Ihres Gateways besser zu verstehen:

* CPU-Auslastung
* Pakete pro Sekunde
* Anzahl der für Peer angekündigten Routen
* Anzahl der von Peers gelernten Routen
* Häufigkeit von Routenänderungen
* Anzahl der virtuellen Computer im virtuellen Netzwerk  

Es wird dringend empfohlen, Warnungen für jede dieser Metriken festzulegen, damit Sie über mögliche Leistungsprobleme bei Ihrem Gateway informiert werden.

#### <a name="cpu-utilization---split-instance"></a>CPU-Auslastung – Aufgeteilt nach Instanz

Aggregationstyp: *Avg*

Sie können die CPU-Auslastung jeder Gatewayinstanz anzeigen. Die CPU-Auslastung kann während einer routinemäßigen Hostwartung kurzzeitig stark ansteigen, aber eine länger andauernde hohe CPU-Auslastung kann darauf hindeuten, dass für Ihr Gateway ein Leistungsengpass vorliegt. Dieses Problem kann möglicherweise durch eine Vergrößerung des ExpressRoute-Gateways behoben werden. Legen Sie eine Warnung dafür fest, wie häufig die CPU-Auslastung einen bestimmten Schwellenwert überschreitet.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="Screenshot: CPU-Auslastung – unterteilte Metriken":::

#### <a name="packets-per-second---split-by-instance"></a>Pakete pro Sekunde – Aufgeteilt nach Instanz

Aggregationstyp: *Avg*

Diese Metrik erfasst die Anzahl eingehender Pakete, die das ExpressRoute-Gateway durchlaufen. Sie sollten hier einen konsistenten Datenstrom beobachten, wenn Ihr Gateway Datenverkehr von Ihrem lokalen Netzwerk empfängt. Legen Sie eine Warnung für den Fall fest, dass die Anzahl der Pakete pro Sekunde unter einen Schwellenwert fällt, der darauf hinweist, dass Ihr Gateway keinen Datenverkehr mehr empfängt.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Screenshot: Pakete pro Sekunde – unterteilte Metriken":::

#### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Anzahl der für Peer angekündigten Routen – Aufgeteilt nach Instanz

Aggregationstyp: *Count*

Diese Metrik gibt die Anzahl von Routen an, die das ExpressRoute-Gateway für die Verbindung ankündigt. Die Adressräume können virtuelle Netzwerke enthalten, die per VNet-Peering verbunden sind und ein ExpressRoute-Remotegateway verwenden. Sie sollten davon ausgehen, dass die Anzahl von Routen konsistent bleibt – es sei denn, es gibt häufige Änderungen an den Adressräumen des virtuellen Netzwerks. Legen Sie eine Warnung für den Fall fest, dass die Anzahl angekündigter Routen unter den Schwellenwert für die Anzahl von bekannten Adressräumen des virtuellen Netzwerks fällt.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Screenshot: Anzahl der für den Peer angekündigten Routen":::

#### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Anzahl der von Peer gelernten Routen – Aufgeteilt nach Instanz

Aggregationstyp: *Max*

Diese Metrik zeigt die Anzahl von Routen an, die das ExpressRoute-Gateway von Peers lernt, die mit der ExpressRoute-Leitung verbunden sind. Diese Routen können entweder aus einem anderen virtuellen Netzwerk stammen, das mit derselben Leitung verbunden ist, oder aus der lokalen Umgebung gelernt worden sein. Legen Sie eine Warnung für den Fall fest, dass die Anzahl von gelernten Routen unter einen bestimmten Schwellenwert fällt. Eine hohe Anzahl von verworfenen Routen könnte darauf hindeuten, dass für das Gateway ein Leistungsproblem vorliegt oder Remotepeers der ExpressRoute-Verbindung keine Routen mehr ankündigen. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Screenshot: Anzahl der vom Peer gelernten Routen":::

#### <a name="frequency-of-routes-change---split-by-instance"></a>Häufigkeit der Routenänderung – Aufgeteilt nach Instanz

Aggregationstyp: *Sum*

Diese Metrik zeigt die Häufigkeit an, mit der Routen von Remotepeers gelernt oder für Remotepeers angekündigt werden. Sie sollten zuerst Ihre lokalen Geräte untersuchen, um den Grund für die häufigen Netzwerkänderungen zu erfahren. Eine häufige Änderung der Routen kann auf ein Leistungsproblem auf dem ExpressRoute-Gateway hindeuten. Ein solches Problem kann möglicherweise durch das Hochskalieren der Gateway-SKU behoben werden. Legen Sie eine Warnung für einen Häufigkeitsschwellenwert fest, um über ungewöhnliche Routenänderungen für Ihr ExpressRoute-Gateway benachrichtigt zu werden.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Screenshot: Metrik zur Häufigkeit von Routenänderungen":::

#### <a name="number-of-vms-in-the-virtual-network"></a>Anzahl der virtuellen Computer im virtuellen Netzwerk

Aggregationstyp: *Max*

Diese Metrik zeigt die Anzahl von VMs an, die das ExpressRoute-Gateway verwenden. Die VM-Anzahl kann virtuelle Computer aus virtuellen Netzwerken mit Peering einschließen, die dasselbe ExpressRoute-Gateway verwenden. Legen Sie eine Warnung für diese Metrik fest, wenn die Anzahl von VMs einen bestimmten Schwellenwert überschreitet, der sich auf die Gatewayleistung auswirken kann. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Screenshot: Metrik zur VM-Anzahl im virtuellen Netzwerk":::

#### <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute-Gatewayverbindungen in Bits pro Sekunde

Aggregationstyp: *Avg*

Diese Metrik zeigt die Bandbreitennutzung für eine bestimmte Verbindung mit einer ExpressRoute-Leitung an.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="Screenshot: Metrik zur Bandbreitennutzung der Gatewayverbindung":::

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
