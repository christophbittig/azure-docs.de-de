---
title: 'Azure ExpressRoute: Überwachung, Metriken und Warnungen'
description: Hier erfahren Sie mehr über die Azure ExpressRoute-Überwachung, -Metriken und -Warnungen mithilfe von Azure Monitor, der zentralen Anlaufstelle für alle Metriken, Warnungen und Diagnoseprotokolle in Azure.
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/07/2021
ms.author: duau
ms.openlocfilehash: abcec496f6bf3fdcd42dcffa66ecfb67533c7052
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123449493"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute-Überwachung, Metriken und Warnungen

Dieser Artikel macht Sie mit der ExpressRoute-Überwachung, Metriken und Warnungen in Azure Monitor vertraut. Azure Monitor ist die Zentrale für alle Metriken, Warnungen und Diagnoseprotokolle im gesamten Azure-System.
 
>[!NOTE]
>Die Verwendung **klassischer Metriken** wird nicht empfohlen.
>

## <a name="expressroute-metrics"></a>ExpressRoute-Metriken

Um **Metriken** anzuzeigen, navigieren Sie zur Seite *Azure Monitor*, und klicken Sie auf *Metriken*. Wenn Sie **ExpressRoute**-Metriken anzeigen möchten, filtern Sie nach dem Ressourcentyp *ExpressRoute-Leitungen*. Um **Global Reach**-Metriken anzuzeigen, filtern Sie nach dem Ressourcentyp *ExpressRoute-Leitungen*, und wählen Sie eine ExpressRoute-Leitungsressource aus, bei der Global Reach aktiviert ist. Um **ExpressRoute Direct**-Metriken anzuzeigen, filtern Sie nach dem Ressourcentyp *ExpressRoute-Ports*. 

Nach dem Auswählen einer Metrik wird die Standardaggregation angewendet. Optional können Sie die Teilung anwenden, wonach die Metrik mit anderen Dimensionen angezeigt wird.

### <a name="aggregation-types"></a>Aggregationstypen:

Der Metriken-Explorer unterstützt die folgenden [Aggregationstypen](../azure-monitor/essentials/metrics-charts.md#aggregation): SUM, MAX, MIN, AVG und COUNT. Sie sollten den empfohlenen Aggregationstyp verwenden, wenn Sie die Erkenntnisse für die einzelnen ExpressRoute-Metriken überprüfen.

* Sum: Die Summe aller Werte, die während des Aggregationsintervalls erfasst wurden. 
* Count: Die Anzahl der Messwerte, die während des Aggregationsintervalls erfasst wurden. 
* Average: Der Durchschnitt der Metrikwerte, die während des Aggregationsintervalls erfasst wurden. 
* Min: Der niedrigste Wert, der während des Aggregationsintervalls erfasst wurde. 
* Max: Der höchste Wert, der während des Aggregationsintervalls erfasst wurde. 

### <a name="available-metrics"></a>Verfügbare Metriken

|**Metrik**|**Kategorie**|**Dimension(en)**|**Feature(s)**|
| --- | --- | --- | --- |
|ARP-Verfügbarkeit|Verfügbarkeit|<ul><li>Peer (Primärer/Sekundärer ExpressRoute-Router)</li><li> Peeringtyp (Privat/Öffentlich/Microsoft)</li></ul>|ExpressRoute|
|BGP-Verfügbarkeit|Verfügbarkeit|<ul><li> Peer (Primärer/Sekundärer ExpressRoute-Router)</li><li> Peeringtyp</li></ul>|ExpressRoute|
|BitsInPerSecond|Verkehr|<ul><li> Peeringtyp (ExpressRoute)</li><li>Link (ExpressRoute Direct)</li></ul>|<ul><li>ExpressRoute</li><li>ExpressRoute Direct</li><li>ExpressRoute-Gatewayverbindung</li></ul>|
|BitsOutPerSecond|Verkehr| <ul><li>Peeringtyp (ExpressRoute)</li><li> Link (ExpressRoute Direct)</li></ul> |<ul><li>ExpressRoute</li><li>ExpressRoute Direct</li><li>ExpressRoute-Gatewayverbindung</li></ul>|
|CPU-Auslastung|Leistung| <ul><li>Instanz</li></ul>|ExpressRoute-Gateway für virtuelle Netzwerke|
|Pakete pro Sekunde|Leistung| <ul><li>Instanz</li></ul>|ExpressRoute-Gateway für virtuelle Netzwerke|
|Anzahl der für Peer angekündigten Routen |Verfügbarkeit| <ul><li>Instanz</li></ul>|ExpressRoute-Gateway für virtuelle Netzwerke|
|Anzahl der von Peer gelernten Routen |Verfügbarkeit| <ul><li>Instanz</li></ul>|ExpressRoute-Gateway für virtuelle Netzwerke|
|Häufigkeit der Routenänderung |Verfügbarkeit| <ul><li>Instanz</li></ul>|ExpressRoute-Gateway für virtuelle Netzwerke|
|Anzahl der virtuellen Computer im virtuellen Netzwerk |Verfügbarkeit| – |ExpressRoute-Gateway für virtuelle Netzwerke|
|GlobalReachBitsInPerSecond|Verkehr|<ul><li>Dienstschlüssel (Skey) der Peeringleitung (Skey)</li></ul>|Global Reach|
|GlobalReachBitsOutPerSecond|Verkehr|<ul><li>Dienstschlüssel (Skey) der Peeringleitung (Skey)</li></ul>|Global Reach|
|AdminState|Physische Konnektivität|Link|ExpressRoute Direct|
|LineProtocol|Physische Konnektivität|Link|ExpressRoute Direct|
|RxLightLevel|Physische Konnektivität|<ul><li>Link</li><li>Bereich</li></ul>|ExpressRoute Direct|
|TxLightLevel|Physische Konnektivität|<ul><li>Link</li><li>Bereich</li></ul>|ExpressRoute Direct|
>[!NOTE]
>Die Verwendung von *GlobalGlobalReachBitsInPerSecond* und *GlobalGlobalReachBitsOutPerSecond* ist nur sichtbar, wenn mindestens eine Global Reach-Verbindung hergestellt wird.
>

## <a name="circuits-metrics"></a>Leitungsmetriken

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Ein- und ausgehende Bits – Metriken über alle Peerings

Aggregationstyp: *Avg*

Sie können Metriken über alle Peerings hinweg für eine bestimmte ExpressRoute-Leitung anzeigen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="Metriken zu Leitungen":::

### <a name="bits-in-and-out---metrics-per-peering"></a>Ein- und ausgehende Bits – Metriken pro Peering

Aggregationstyp: *Avg*

Sie können Metriken für privates, öffentliches und Microsoft-Peering in Bits pro Sekunde anzeigen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="Peeringspezifische Metriken":::

### <a name="bgp-availability---split-by-peer"></a>BGP-Verfügbarkeit – Aufgeteilt nach Peer  

Aggregationstyp: *Avg*

Sie können die BGP-Verfügbarkeit (Layer 3-Konnektivität) nahezu in Echtzeit über Peerings und Peers (primäre und sekundäre ExpressRoute-Router) hinweg anzeigen. Dieses Dashboard zeigt, dass die primäre BGP-Sitzung für privates Peering bereit und die sekundäre BGP-Sitzung für privates Peering nicht verfügbar ist. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="BGP-Verfügbarkeit pro Peer":::

### <a name="arp-availability---split-by-peering"></a>ARP-Verfügbarkeit – Aufgeteilt nach Peering  

Aggregationstyp: *Avg*

Sie können die [ARP](./expressroute-troubleshooting-arp-resource-manager.md)-Verfügbarkeit (Layer 3-Konnektivität) nahezu in Echtzeit über Peerings und Peers (primäre und sekundäre ExpressRoute-Router) hinweg anzeigen. Dieses Dashboard zeigt, dass die ARP-Sitzung für privates Peering für beide Peers bereit, aber für das Microsoft-Peering für beide Peers nicht verfügbar ist. Die Standardaggregation (Durchschnitt) wurde für beide Peers verwendet.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="ARP-Verfügbarkeit pro Peer":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct-Metriken

### <a name="admin-state---split-by-link"></a>Administratorstatus nach Link

Aggregationstyp: *Avg*

Sie können den Administratorstatus für jede Verbindung des ExpressRoute Direct-Portpaars anzeigen. Der Administratorstatus zeigt an, ob der physische Port aktiviert oder deaktiviert ist. Dieser Zustand ist erforderlich, um Datenverkehr über die ExpressRoute Direct-Verbindung zu übergeben.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="ExpressRoute Direct: Administratorstatus":::

### <a name="bits-in-per-second---split-by-link"></a>Eingehende Bits pro Sekunde nach Link

Aggregationstyp: *Avg*

Sie können die eingehenden Bits pro Sekunde über beide Links des ExpressRoute Direct-Portpaars anzeigen. Überwachen Sie dieses Dashboard, um die Eingangsbandbreite für beide Verbindungen zu vergleichen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ExpressRoute Direct: eingehende Bits pro Sekunde":::

### <a name="bits-out-per-second---split-by-link"></a>Ausgehende Bits pro Sekunde nach Link

Aggregationstyp: *Avg*

Sie können auch die ausgehenden Bits pro Sekunde über beide Links des ExpressRoute Direct-Portpaars anzeigen. Überwachen Sie dieses Dashboard, um die Ausgangsbandbreite für beide Verbindungen zu vergleichen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ExpressRoute Direct: ausgehende Bits pro Sekunde":::

### <a name="line-protocol---split-by-link"></a>Zeilenprotokoll nach Link

Aggregationstyp: *Avg*

Sie können das Zeilenprotokoll für jeden Link des ExpressRoute Direct-Portpaars anzeigen. Das Leitungsprotokoll gibt an, ob die physische Leitung einsatzbereit ist und über ExpressRoute Direct ausgeführt wird. Überwachen Sie dieses Dashboard, und legen Sie Warnungen fest, um bei einem Ausfall der physischen Verbindung benachrichtigt zu werden.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ExpressRoute Direct: Zeilenprotokoll":::

### <a name="rx-light-level---split-by-link"></a>Rx-Lichtpegel nach Link

Aggregationstyp: *Avg*

Sie können den Rx-Lichtpegel (vom ExpressRoute Direct-Port **empfangene** Lichtintensität) für jeden Port anzeigen. Wenn keine Fehler vorliegen, liegt der Rx-Leistungspegel in der Regel innerhalb eines Bereichs von -10 dBm bis 0 dBm. Legen Sie Warnungen fest, um benachrichtigt zu werden, wenn der Rx-Leistungspegel außerhalb des fehlerfreien Bereichs liegt.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ExpressRoute Direct: Empfangslichtpegel":::

### <a name="tx-light-level---split-by-link"></a>Tx-Lichtpegel nach Link

Aggregationstyp: *Avg*

Sie können den Tx-Lichtpegel (vom ExpressRoute Direct-Port **übertragene** Lichtintensität) für jeden Port anzeigen. Wenn keine Fehler vorliegen, liegt der Tx-Leistungspegel in der Regel innerhalb eines Bereichs von -10 dBm bis 0 dBm. Legen Sie Warnungen fest, um benachrichtigt zu werden, wenn der Tx-Leistungspegel außerhalb des fehlerfreien Bereichs liegt.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ExpressRoute Direct: Sendelichtpegel":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>Metriken zum ExpressRoute-Gateway für virtuelle Netzwerke

Aggregationstyp: *Avg*

Wenn Sie ein ExpressRoute-Gateway bereitstellen, verwaltet Azure die Computeressourcen und Funktionen Ihres Gateways. Ihnen stehen sechs Gatewaymetriken zur Verfügung, um die Leistung Ihres Gateways besser zu verstehen:

* CPU-Auslastung
* Pakete pro Sekunde
* Anzahl der für Peer angekündigten Routen
* Anzahl der von Peers gelernten Routen
* Häufigkeit von Routenänderungen
* Anzahl der virtuellen Computer im virtuellen Netzwerk  

Es wird dringend empfohlen, Warnungen für jede dieser Metriken festzulegen, damit Sie über mögliche Leistungsprobleme bei Ihrem Gateway informiert werden.

### <a name="cpu-utilization---split-instance"></a>CPU-Auslastung – geteilte Instanz

Aggregationstyp: *Avg*

Sie können die CPU-Auslastung jeder Gatewayinstanz anzeigen. Die CPU-Auslastung kann während einer routinemäßigen Hostwartung kurzzeitig stark ansteigen, aber eine länger andauernde hohe CPU-Auslastung kann darauf hindeuten, dass für Ihr Gateway ein Leistungsengpass vorliegt. Dieses Problem kann möglicherweise durch eine Vergrößerung des ExpressRoute-Gateways behoben werden. Legen Sie eine Warnung dafür fest, wie häufig die CPU-Auslastung einen bestimmten Schwellenwert überschreitet.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="Screenshot: CPU-Auslastung – unterteilte Metriken":::

### <a name="packets-per-second---split-by-instance"></a>Pakete pro Sekunde – Unterteilung nach Instanz

Aggregationstyp: *Avg*

Diese Metrik erfasst die Anzahl eingehender Pakete, die das ExpressRoute-Gateway durchlaufen. Sie sollten hier einen konsistenten Datenstrom beobachten, wenn Ihr Gateway Datenverkehr von Ihrem lokalen Netzwerk empfängt. Legen Sie eine Warnung für den Fall fest, dass die Anzahl der Pakete pro Sekunde unter einen Schwellenwert fällt, der darauf hinweist, dass Ihr Gateway keinen Datenverkehr mehr empfängt.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Screenshot: Pakete pro Sekunde – unterteilte Metriken":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Anzahl der für Peer angekündigten Routen – Aufgeteilt nach Instanz

Aggregationstyp: *Count*

Diese Metrik gibt die Anzahl von Routen an, die das ExpressRoute-Gateway für die Verbindung ankündigt. Die Adressräume können virtuelle Netzwerke enthalten, die per VNet-Peering verbunden sind und ein ExpressRoute-Remotegateway verwenden. Sie sollten davon ausgehen, dass die Anzahl von Routen konsistent bleibt – es sei denn, es gibt häufige Änderungen an den Adressräumen des virtuellen Netzwerks. Legen Sie eine Warnung für den Fall fest, dass die Anzahl angekündigter Routen unter den Schwellenwert für die Anzahl von bekannten Adressräumen des virtuellen Netzwerks fällt.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Screenshot: Anzahl der für den Peer angekündigten Routen":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Anzahl der von Peer gelernten Routen – Aufgeteilt nach Instanz

Aggregationstyp: *Max*

Diese Metrik zeigt die Anzahl von Routen an, die das ExpressRoute-Gateway von Peers lernt, die mit der ExpressRoute-Leitung verbunden sind. Diese Routen können entweder aus einem anderen virtuellen Netzwerk stammen, das mit derselben Leitung verbunden ist, oder aus der lokalen Umgebung gelernt worden sein. Legen Sie eine Warnung für den Fall fest, dass die Anzahl von gelernten Routen unter einen bestimmten Schwellenwert fällt. Dies kann entweder darauf hindeuten, dass für das Gateway ein Leistungsproblem vorliegt oder Remotepeers der ExpressRoute-Verbindung keine Routen mehr ankündigen. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Screenshot: Anzahl der vom Peer gelernten Routen":::

### <a name="frequency-of-routes-change---split-by-instance"></a>Häufigkeit der Routenänderung – Aufgeteilt nach Instanz

Aggregationstyp: *Sum*

Diese Metrik zeigt die Häufigkeit an, mit der Routen von Remotepeers gelernt oder für Remotepeers angekündigt werden. Sie sollten zuerst Ihre lokalen Geräte untersuchen, um den Grund für die häufigen Netzwerkänderungen zu erfahren. Eine häufige Änderung der Routen kann auf ein Leistungsproblem auf dem ExpressRoute-Gateway hindeuten. Ein solches Problem kann möglicherweise durch das Hochskalieren der Gateway-SKU behoben werden. Legen Sie eine Warnung für einen Häufigkeitsschwellenwert fest, um über ungewöhnliche Routenänderungen für Ihr ExpressRoute-Gateway benachrichtigt zu werden.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Screenshot: Metrik zur Häufigkeit von Routenänderungen":::

### <a name="number-of-vms-in-the-virtual-network"></a>Anzahl der virtuellen Computer im virtuellen Netzwerk

Aggregationstyp: *Max*

Diese Metrik zeigt die Anzahl von VMs an, die das ExpressRoute-Gateway verwenden. Die VM-Anzahl kann virtuelle Computer aus virtuellen Netzwerken mit Peering einschließen, die dasselbe ExpressRoute-Gateway verwenden. Legen Sie eine Warnung für diese Metrik fest, wenn die Anzahl von VMs einen bestimmten Schwellenwert überschreitet, der sich auf die Gatewayleistung auswirken kann. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Screenshot: Metrik zur VM-Anzahl im virtuellen Netzwerk":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute-Gatewayverbindungen in Bits pro Sekunde

Aggregationstyp: *Avg*

Diese Metrik zeigt die Bandbreitennutzung für eine bestimmte Verbindung mit einer ExpressRoute-Leitung an.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="Screenshot: Metrik zur Bandbreitennutzung der Gatewayverbindung":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Warnungen zu ExpressRoute-Gatewayverbindungen

1. Navigieren Sie zum Konfigurieren von Warnungen zu **Azure Monitor**, und klicken Sie auf **Warnungen**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alerts":::
2. Klicken Sie auf **+ Ziel auswählen**, und wählen Sie die Ressource für die ExpressRoute-Gatewayverbindung aus.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="Ziel":::
3. Definieren Sie die Warnungsdetails.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="Aktionsgruppe":::
4. Definieren Sie die Aktionsgruppe, und fügen Sie sie hinzu.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="Hinzufügen einer Aktionsgruppe":::

## <a name="alerts-based-on-each-peering"></a>Warnungen basierend auf dem jeweiligen Peering

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="Jedes Peering":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Konfigurieren von Warnungen für Aktivitätsprotokolle zu Leitungen

In **Warnungskriterien** können Sie **Aktivitätsprotokoll** als Signaltyp und dann das Signal auswählen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="Aktivitätsprotokolle":::

## <a name="more-metrics-in-log-analytics"></a>Weitere Metriken in Log Analytics

Sie können die ExpressRoute-Metriken auch anzeigen, indem Sie zu Ihrer ExpressRoute-Leitung navigieren und die Registerkarte *Protokolle* auswählen. Für alle Metriken, die Sie abfragen, enthält die Ausgabe die nachfolgenden Spalten.

|**Spalte**|**Typ**|**Beschreibung**|
| --- | --- | --- |
|TimeGrain|Zeichenfolge|PT1M (Metrikwerte werden jede Minute gepusht)|
|Anzahl|real|Normalerweise gleich 2 (jedes MSEE pusht jede Minute einen einzelnen Metrikwert).|
|Minimum|real|Der Mindestwert der beiden Metrikwerte, die von den beiden MSEEs gepusht werden|
|Maximum|real|Der Höchstwert der beiden Metrikwerte, die von den beiden MSEEs gepusht werden|
|Average|real|Entspricht (Minimum + Maximum)/2|
|Gesamt|real|Summe der beiden Metrikwerte aus beiden MSEEs (der Hauptwert, auf den sich bei der abgefragten Metrik konzentriert werden sollte)|
  
## <a name="next-steps"></a>Nächste Schritte

Konfigurieren Sie Ihre ExpressRoute-Verbindung.
  
* [Erstellen und Ändern einer Verbindung](expressroute-howto-circuit-arm.md)
* [Erstellen und Ändern einer Peeringkonfiguration](expressroute-howto-routing-arm.md)
* [Verknüpfen eines VNet mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md)
