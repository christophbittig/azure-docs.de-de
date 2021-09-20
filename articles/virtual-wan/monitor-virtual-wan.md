---
title: Überwachen von Azure Virtual WAN
description: Erfahren Sie mehr über die Nutzung von Azure Virtual WAN-Protokolle und -Metriken unter Verwendung von Azure Monitor.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/30/2021
ms.author: cherylmc
ms.openlocfilehash: d3cffbe9ebaa71ca5c4dfd8681159f83ff06eb38
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821464"
---
# <a name="monitoring-virtual-wan"></a>Überwachen von Virtual WAN

Sie können Azure Virtual WAN mit Azure Monitor überwachen. Virtual WAN ist ein Netzwerkdienst, der viele Netzwerk-, Sicherheits- und Routingfunktionen auf einer einzigen Bedienoberfläche vereint. Für Virtual WAN-VPN-Gateways, ExpressRoute-Gateways und Azure Firewall sind über Azure Monitor Protokollierungsfunktionen und Metriken verfügbar.

In diesem Artikel werden die Metriken und Diagnosen erläutert, die über das Portal verfügbar sind. Metriken sind einfach und können Szenarien nahezu in Echtzeit unterstützen. Dadurch lassen sie sich für Warnungen und zur schnellen Problemerkennung einsetzen.

### <a name="monitoring-secured-hub-azure-firewall"></a>Überwachen des gesicherten Hubs (Azure Firewall) 

Sie können den gesicherten Hub mithilfe von Azure Firewall-Protokollen überwachen. Sie können aber auch Aktivitätsprotokolle verwenden, um Vorgänge für Azure Firewall-Ressourcen zu überwachen.

Wenn Sie sich entschieden haben, den virtuellen Hub mithilfe der Azure-Firewall zu schützen, finden Sie hier relevante Protokolle und Metriken: [Azure Firewall-Protokolle und -Metriken](../firewall/logs-and-metrics.md).

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
| **Angekündigte BGP-Routen** | Anzahl der pro Peer und pro Instanz angekündigten Routen|
| **Gelernte BGP-Routen** | Anzahl der pro Peer und pro Instanz gelernten Routen|
| **Anzahl der VNet-Adresspräfixe** | Anzahl der VNet-Adresspräfixe, die vom Gateway verwendet/angekündigt wurden|

Sie können die Metriken pro Peer und Instanz überprüfen, indem Sie **Aufteilen anwenden** auswählen und den bevorzugten Wert auswählen. 

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

2. Wählen Sie **VPN (Site-to-Site)** aus, um ein Site-to-Site-Gateway zu suchen, **ExpressRoute**, um ein ExpressRoute-Gateway zu suchen, oder **Benutzer-VPN (Point-to-Site)** , um ein Point-to-Site-Gateway zu suchen. Auf der Seite werden die Gatewayinformationen angezeigt. Kopieren Sie diese Informationen. Sie werden diese später verwenden, um die Diagnose mithilfe von Azure Monitor anzuzeigen.

3. Klicken Sie auf **Metriken**.

   :::image type="content" source="./media/monitor-virtual-wan/metrics.png" alt-text="Screenshot zeigt den Bereich „VPN (Site-to-Site)“ mit ausgewähltem „In Azure Monitor anzeigen“.":::

4. Auf der Seite **Metriken** können Sie die Metriken anzeigen, an denen Sie interessiert sind.

   :::image type="content" source="./media/monitor-virtual-wan/metrics-page.png" alt-text="Screenshot der Seite „Metriken“ mit hervorgehobenen Kategorien.":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>Diagnoseprotokolle

### <a name="site-to-site-vpn-gateways"></a>Site-to-Site-VPN-Gateways

Die folgenden Diagnosen sind für Site-to-Site-VPN-Gateways in Azure verfügbar:

| Metrik | BESCHREIBUNG|
| --- | --- |
| **Gatewaydiagnoseprotokolle** | Gatewayspezifische Diagnosen, z. B. Integrität, Konfiguration, Dienstaktualisierungen sowie zusätzliche Diagnosen.|
| **Tunneldiagnoseprotokolle** | Protokolle für IPsec-Tunnel, die z. B. Verbindungs-und Trennungsereignisse für einen Site-to-Site-IPsec-Tunnel, ausgehandelte SAs, Gründe für die Verbindungstrennung sowie zusätzliche Diagnoseinformationen enthalten.|
| **Routendiagnoseprotokolle** | Protokolle in Bezug auf Ereignisse für statische Routen, BGP, Routenaktualisierungen und zusätzliche Diagnosen. |
| **IKE-Diagnoseprotokolle** | IKE-spezifische Diagnosen für IPsec-Verbindungen. |

### <a name="point-to-site-vpn-gateways"></a>Point-to-Site-VPN-Gateways

Die folgenden Diagnosen sind für Point-to-Site-VPN-Gateways in Azure verfügbar:

| Metrik | BESCHREIBUNG|
| --- | --- |
| **Gatewaydiagnoseprotokolle** | Gatewayspezifische Diagnosen, z. B. Integrität, Konfiguration, Dienstaktualisierungen sowie zusätzliche Diagnosen. |
| **IKE-Diagnoseprotokolle** | IKE-spezifische Diagnosen für IPsec-Verbindungen.|
| **P2S-Diagnoseprotokolle** | P2S-Konfigurations- und -Clientereignisse für Benutzer-VPNs (Point-to-Site). Dazu zählen die Verbindung/Trennung von Clients, VPN-Clientadresszuweisung sowie andere Diagnosen.|

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>Anzeigen von Diagnoseprotokollen

Führen Sie die folgenden Schritte aus, um Diagnosen zu suchen und anzuzeigen:

1. Navigieren Sie im Portal zu Ihrer Virtual WAN-Ressource. Wählen Sie im Portal im Abschnitt **Übersicht** der Virtual WAN-Seite **Zusammenfassung** aus, um die Ansicht zu erweitern und Informationen zu Ressourcengruppen abzurufen. Kopieren Sie die Ressourcengruppeninformationen.

   :::image type="content" source="./media/monitor-virtual-wan/3.png" alt-text="Screenshot des Abschnitts „Übersicht“ mit einem Pfeil, der auf die Schaltfläche „Kopieren“ zeigt.":::

2. Navigieren Sie in der Suchleiste zu **Überwachung**, und wählen Sie im Abschnitt mit den Einstellungen die Option **Diagnoseeinstellungen** aus. Geben Sie dann die Ressourcengruppe, den Ressourcentyp und die Ressourceninformationen ein. Dies sind die Ressourcengruppeninformationen, die Sie in Schritt 2 im Abschnitt [Anzeigen von Gatewaymetriken](#metrics-steps) weiter oben in diesem Artikel kopiert haben.

   :::image type="content" source="./media/monitor-virtual-wan/4.png" alt-text="Screenshot des Abschnitts „Überwachung“ mit einem Pfeil, der auf die Dropdownliste „Ressource“ zeigt.":::

3. Wählen Sie auf der Seite mit den Ergebnissen **+ Diagnoseeinstellung hinzufügen** und dann eine Option aus. Sie können die Daten an Log Analytics senden, an einen Event Hub streamen oder einfach in einem Speicherkonto archivieren.

   :::image type="content" source="./media/monitor-virtual-wan/5.png" alt-text="Seite „Metriken“":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Log Analytics-Beispielabfrage

Die Protokolle befinden sich im **Azure Log Analytics-Arbeitsbereich**. Sie können in Log Analytics eine Abfrage einrichten. Das folgende Beispiel enthält eine Abfrage zum Abrufen von Site-to-Site-Routendiagnosen.

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

Ersetzen Sie die folgenden Werte hinter **= =** je nach Bedarf.

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog”
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>Aktivitätsprotokolle

Einträge im **Aktivitätsprotokoll** werden standardmäßig erfasst und können im Azure-Portal angezeigt werden. Mit Azure-Aktivitätsprotokollen (ehemals *Betriebsprotokolle* und *Überwachungsprotokolle*) können Sie alle an Ihr Azure-Abonnement übermittelten Vorgänge anzeigen.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Überwachen von Azure Firewall-Protokollen und Metriken finden Sie unter [Tutorial: Überwachen von Azure Firewall-Protokollen](../firewall/firewall-diagnostics.md).
* Weitere Informationen zu Metriken in Azure Monitor finden Sie unter [Metriken in Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md).
