---
title: Überwachen von Azure Virtual WAN
description: Erfahren Sie mehr über die Nutzung von Azure Virtual WAN-Protokolle und -Metriken unter Verwendung von Azure Monitor.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/30/2021
ms.author: cherylmc
ms.openlocfilehash: d1ac031b79372987561651044e81da2e3d2d2779
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636481"
---
# <a name="monitoring-virtual-wan"></a>Überwachen von Virtual WAN

Sie können Azure Virtual WAN mit Azure Monitor überwachen. Virtual WAN ist ein Netzwerkdienst, der viele Netzwerk-, Sicherheits- und Routingfunktionen auf einer einzigen Bedienoberfläche vereint. Für Virtual WAN-VPN-Gateways, ExpressRoute-Gateways und Azure Firewall sind über Azure Monitor Protokollierungsfunktionen und Metriken verfügbar. 

In diesem Artikel werden die Metriken und Diagnosen erläutert, die über das Portal verfügbar sind. Metriken sind einfach und können Szenarien nahezu in Echtzeit unterstützen. Dadurch lassen sie sich für Warnungen und zur schnellen Problemerkennung einsetzen.

### <a name="monitoring-secured-hub-azure-firewall"></a>Überwachen des gesicherten Hubs (Azure Firewall) 

Wenn Sie sich entschieden haben, den virtuellen Hub mithilfe der Azure-Firewall zu schützen, finden Sie hier relevante Protokolle und Metriken: [Azure Firewall-Protokolle und -Metriken](../firewall/logs-and-metrics.md).
Sie können den geschützten Hub mithilfe von Azure Firewall-Protokollen und -Metriken überwachen. Sie können aber auch Aktivitätsprotokolle verwenden, um Vorgänge für Azure Firewall-Ressourcen zu überwachen.
Für jede Azure Virtual WAN-Instanz, die Sie schützen und in einen geschützten Hub konvertieren, wird ein explizites Firewallressourcenobjekt in der Ressourcengruppe erstellt, in der sich der Hub befindet. 

:::image type="content" source="./media/monitor-virtual-wan/firewall-resources-portal.png" alt-text="Screenshot: Firewallressource in der vWAN-Hub-Ressourcengruppe.":::

Die Diagnose- und Protokollierungskonfiguration muss erfolgen, bevor Sie auf die Registerkarte **Diagnoseeinstellung** zugreifen:

:::image type="content" source="./media/monitor-virtual-wan/firewall-diagnostic-settings.png" alt-text="Screenshot: Firewalldiagnoseeinstellungen.":::


## <a name="metrics"></a>Metriken

Metriken in Azure Monitor sind numerische Werte, die einen Aspekt eines Systems zu einem bestimmten Zeitpunkt beschreiben. Metriken werden jede Minute erfasst und eignen sich gut für Warnmeldungen, da sie häufig abgefragt werden können. Eine Warnung kann mit relativ einfacher Logik schnell ausgelöst werden.

### <a name="site-to-site-vpn-gateways"></a>Site-to-Site-VPN-Gateways

Die folgenden Metriken sind für Site-to-Site-VPN-Gateways in Azure verfügbar:

#### <a name="tunnel-packet-drop-metrics"></a>Metriken zu gelöschten Paketen von Tunneln
| Metrik | BESCHREIBUNG|
| --- | --- |
| **Anzahl ausgehender gelöschter Pakete eines Tunnels** | Anzahl der ausgehenden gelöschten Pakete nach Tunnel|
| **Anzahl eingehender gelöschter Pakete eines Tunnels** | Anzahl der eingehenden gelöschten Pakete nach Tunnel|
| **Tunnel-NAT-Paketverluste** | Anzahl der in einem Tunnel einer NAT unterzogenen gelöschten Pakete, nach Verlusttyp und NAT-Regel|
| **Ausgehende gelöschte Pakete eines Tunnels durch einen TS-Konflikt** | Anzahl der aufgrund eines Konflikts mit dem Datenverkehrsselektor eines Tunnels gelöschten ausgehenden Pakete|
| **Eingehende gelöschte Pakete eines Tunnels durch einen TS-Konflikt** | Anzahl der aufgrund eines Konflikts mit dem Datenverkehrsselektor eines Tunnels gelöschten eingehenden Pakete|

#### <a name="ipsec-metrics"></a>IPSEC-Metriken
| Metrik | BESCHREIBUNG|
| --- | --- |
| **MMSA-Anzahl des Tunnels** | Anzahl der MMSAs, die erstellt oder gelöscht wurden|
| **QMSA-Anzahl des Tunnels** | Anzahl der IPSEC-QMSAs, die erstellt oder gelöscht werden|

#### <a name="routing-metrics"></a>Routingmetriken
| Metrik | BESCHREIBUNG|
| --- | --- |
| **Status des BGP-Peers** | BGP-Konnektivitätsstatus pro Peer und pro Instanz|
| **Angekündigte BGP-Routen** | Anzahl von Routen, die pro Peer und Instanz angekündigt wurden.|
| **Gelernte BGP-Routen** | Anzahl von Routen, die pro Peer und Instanz gelernt wurden.|
| **Anzahl der VNet-Adresspräfixe** | Anzahl der VNet-Adresspräfixe, die vom Gateway verwendet/angekündigt wurden|

Sie können die Metriken pro Peer und Instanz überprüfen, indem Sie **Aufteilen anwenden** und den bevorzugten Wert auswählen. 

#### <a name="traffic-flow-metrics"></a>Flowmetriken des Datenverkehrs
| Metrik | BESCHREIBUNG|
| --- | --- |
| **Bandbreite des Gateways** | Durchschnittliche aggregierte Site-to-Site-Bandbreite eines Gateways, angegeben in Byte pro Sekunde|
| **Bandbreite des Tunnels** | Durchschnittliche Bandbreite eines Tunnels, angegeben in Byte pro Sekunde|
| **Vom Tunnel ausgehende Bytes** | Ausgehende Bytes für einen Tunnel |
| **Vom Tunnel ausgehende Pakete** | Anzahl ausgehender Pakete für einen Tunnel |
| **In Tunnel eingehende Bytes** | Eingehende Bytes für einen Tunnel|
| **In Tunnel eingehende Pakete** | Anzahl eingehender Pakete für einen Tunnel|
| **PPS-Spitzenwert eines Tunnels** | Anzahl der Pakete pro Sekunde pro Linkverbindung in der letzten Minute|
| **Tunnel-Flowanzahl** | Anzahl der pro Linkverbindung erstellten unterschiedlichen Flows|

### <a name="point-to-site-vpn-gateways"></a>Point-to-Site-VPN-Gateways

Die folgenden Metriken sind für Point-to-Site-VPN-Gateways in Azure verfügbar:

| Metrik | BESCHREIBUNG|
| --- | --- |
| **P2S-Gatewaybandbreite** | Durchschnittliche aggregierte Point-to-Site-Bandbreite eines Gateways, angegeben in Byte pro Sekunde |
| **Anzahl der P2S-Verbindungen** |Anzahl der Point-to-Site-Verbindungen eines Gateways. Anzahl der Point-to-Site-Verbindungen eines Gateways. Um sicherzustellen, dass in Azure Monitor genaue Metriken angezeigt werden, legen Sie als **Aggregationstyp** für **P2S-Verbindungsanzahl** die Option **Summe** fest. Sie können auch **Max** auswählen, wenn Sie zusätzlich eine Aufteilung nach **Instanz** festgelegt haben. |
| **Anzahl der Benutzer-VPN-Routen** | Anzahl der Benutzer-VPN-Routen, die auf dem VPN Gateway konfiguriert sind Diese Metrik kann in **statische** und **dynamische** Routen unterteilt werden.

### <a name="azure-expressroute-gateways"></a>Azure ExpressRoute-Gateways

Die folgenden Metriken sind für Azure ExpressRoute-Gateways verfügbar:

| Metrik | BESCHREIBUNG|
| --- | --- |
| **BitsInPerSecond** | Eingehende Bits für Azure pro Sekunde|
| **BitsOutPerSecond** | Ausgehende Bits für Azure pro Sekunde |

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>Anzeigen von Gatewaymetriken

Führen Sie die folgenden Schritte aus, um Metriken zu suchen und anzuzeigen:

1. Navigieren Sie im Portal zu dem virtuellen Hub, der das Gateway enthält.

2. Wählen Sie **VPN (Site-to-Site)** aus, um ein Site-to-Site-Gateway zu suchen, **ExpressRoute**, um ein ExpressRoute-Gateway zu suchen, oder **Benutzer-VPN (Point-to-Site)** , um ein Point-to-Site-Gateway zu suchen.

3. Klicken Sie auf **Metriken**.

   :::image type="content" source="./media/monitor-virtual-wan/view-metrics.png" alt-text="Screenshot: der Bereich „VPN (Site-to-Site)“ mit ausgewählter Option „In Azure Monitor anzeigen“.":::

4. Auf der Seite **Metriken** können Sie die Metriken anzeigen, an denen Sie interessiert sind.

   :::image type="content" source="./media/monitor-virtual-wan/metrics-page.png" alt-text="Screenshot der Seite „Metriken“ mit hervorgehobenen Kategorien.":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>Diagnoseprotokolle

### <a name="site-to-site-vpn-gateways"></a>Site-to-Site-VPN-Gateways

Die folgenden Diagnosen sind für Site-to-Site-VPN-Gateways in Azure verfügbar:

| Metrik | BESCHREIBUNG|
| --- | --- |
| **Gatewaydiagnoseprotokolle** | Gatewayspezifische Diagnosen (z. B. Integrität, Konfiguration, Dienstaktualisierungen) sowie zusätzliche Diagnosen.|
| **Tunneldiagnoseprotokolle** | Protokolle für IPsec-Tunnel, die z. B. Ereignisse wie das Herstellen oder Trennen der Verbindung für einen Site-to-Site-IPsec-Tunnel, ausgehandelte SAs, Gründe für das Trennen der Verbindung sowie zusätzliche Diagnoseinformationen enthalten.|
| **Routendiagnoseprotokolle** | Protokolle in Bezug auf Ereignisse für statische Routen, BGP, Routenaktualisierungen und zusätzliche Diagnoseinformationen. |
| **IKE-Diagnoseprotokolle** | IKE-spezifische Diagnosen für IPsec-Verbindungen. |

### <a name="point-to-site-vpn-gateways"></a>Point-to-Site-VPN-Gateways

Die folgenden Diagnosen sind für Point-to-Site-VPN-Gateways in Azure verfügbar:

| Metrik | BESCHREIBUNG|
| --- | --- |
| **Gatewaydiagnoseprotokolle** | Gatewayspezifische Diagnosen (z. B. Integrität, Konfiguration, Dienstaktualisierungen) sowie zusätzliche Diagnoseinformationen. |
| **IKE-Diagnoseprotokolle** | IKE-spezifische Diagnosen für IPsec-Verbindungen.|
| **P2S-Diagnoseprotokolle** | P2S-Konfigurations- und -Clientereignisse für Benutzer-VPNs (Point-to-Site). Dazu zählen Ereignisse zum Herstellen/Trennen der Verbindung mit dem Client, die VPN-Clientadresszuweisung und weitere Diagnoseinformationen.|

### <a name="express-route-gateways"></a>ExpressRoute-Gateways

Diagnoseprotokolle für ExpressRoute-Gateways in Azure Virtual WAN werden nicht unterstützt.

### <a name="view-diagnostic-logs-configuration"></a><a name="diagnostic-steps"></a>Anzeigen der Konfiguration von Diagnoseprotokollen

Über die folgenden Schritte können Sie Diagnoseeinstellungen erstellen, bearbeiten und anzeigen:

1. Navigieren Sie im Portal zu Ihrer Virtual WAN-Ressource, und wählen Sie in der Gruppe **Konnektivität** die Option **Hubs** aus. 

   :::image type="content" source="./media/monitor-virtual-wan/select-hub.png" alt-text="Screenshot: Auswahl des Hubs im vWAN-Portal.":::

2. Wählen Sie auf der linken Seite in der Gruppe **Konnektivität** das Gateway aus, dessen Diagnoseinformationen Sie untersuchen möchten:

   :::image type="content" source="./media/monitor-virtual-wan/select-hub-gateway.png" alt-text="Screenshot: der Abschnitt „Konnektivität“ für den Hub.":::

3. Klicken Sie rechts auf der Seite neben **Protokolle** auf den Link **In Azure Monitor anzeigen**, und wählen Sie eine Option aus. Sie können die Daten an Log Analytics senden, an einen Event Hub streamen oder einfach in einem Speicherkonto archivieren.

   :::image type="content" source="./media/monitor-virtual-wan/view-hub-gateway-logs.png" alt-text="Screenshot: Auswählen der Ansicht in Azure Monitor für Protokolle.":::

4. Auf dieser Seite können Sie eine neue Diagnoseeinstellung erstellen ( **+Diagnoseeinstellung hinzufügen**) oder eine vorhandene Einstellung bearbeiten (**Einstellung bearbeiten**). Sie können die Diagnoseprotokolle an Log Analytics senden (wie im folgenden Beispiel gezeigt), an einen Event Hub streamen, an eine Drittanbieterlösung senden oder in einem Speicherkonto archivieren.

    :::image type="content" source="./media/monitor-virtual-wan/select-gateway-settings.png" alt-text="Screenshot: Auswählen der Einstellungen für das Diagnoseprotokoll.":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Log Analytics-Beispielabfrage

Wenn Sie das Senden von Diagnosedaten an einen Log Analytics-Arbeitsbereich ausgewählt haben, können Sie SQL-ähnliche Abfragen wie im folgenden Beispiel verwenden, um die Daten zu untersuchen. Weitere Informationen finden Sie unter [Log Analytics-Abfragesprache](/services-hub/health/log_analytics_query_language).

Das folgende Beispiel enthält eine Abfrage zum Abrufen von Site-to-Site-Routendiagnosen.

`AzureDiagnostics | where Category == "RouteDiagnosticLog"`

Ersetzen Sie die unten angegebenen Werte nach **= =** nach Bedarf basierend auf den Tabellen, die im vorherigen Abschnitt dieses Artikels gemeldet wurden.

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog”
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

Zum Ausführen der Abfrage müssen Sie die Log Analytics-Ressource öffnen, die für den Empfang der Diagnoseprotokolle konfiguriert wurde. Anschließend wählen Sie auf der Registerkarte **Allgemein** im linken Bereich die Option **Protokolle** aus:

:::image type="content" source="./media/monitor-virtual-wan/log-analytics-query-samples.png" alt-text="Log Analytics-Abfragebeispiele.":::

Weitere Log Analytics-Abfragebeispiele für Azure VPN Gateway (sowohl Site-to-Site als auch Point-to-Site) finden Sie auf der Seite [Problembehandlung bei Azure VPN Gateway mithilfe von Diagnoseprotokollen](../vpn-gateway/troubleshoot-vpn-with-azure-diagnostics.md). Für Azure Firewall wird zur Vereinfachung der Protokollanalyse eine [Arbeitsmappe](../firewall/firewall-workbook.md) bereitgestellt. Über die grafische Benutzeroberfläche können die Diagnosedaten untersucht werden, ohne manuell Log Analytics-Abfragen zu schreiben. 

## <a name="activity-logs"></a><a name="activity-logs"></a>Aktivitätsprotokolle

Einträge im **Aktivitätsprotokoll** werden standardmäßig erfasst und können im Azure-Portal angezeigt werden. Mit Azure-Aktivitätsprotokollen (ehemals *Betriebsprotokolle* und *Überwachungsprotokolle*) können Sie alle an Ihr Azure-Abonnement übermittelten Vorgänge anzeigen.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Überwachen von Azure Firewall-Protokollen und Metriken finden Sie unter [Tutorial: Überwachen von Azure Firewall-Protokollen](../firewall/firewall-diagnostics.md).
* Weitere Informationen zu Metriken in Azure Monitor finden Sie unter [Metriken in Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md).
