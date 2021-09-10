---
title: Überwachen von Azure VPN Gateway
description: Erfahren Sie, wie VPN Gateway-Metriken über Azure Monitor angezeigt werden.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/23/2021
ms.author: alzam
ms.openlocfilehash: a55c4ac84f218b4fe657f02cac9f10a4e9dc4a68
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2021
ms.locfileid: "112583943"
---
# <a name="monitoring-vpn-gateway"></a>Überwachen von VPN Gateway

Sie können Azure-VPN-Gateways mit Azure Monitor überwachen. In diesem Artikel werden die Metriken erläutert, die über das Portal verfügbar sind. Metriken sind einfach und können Szenarien nahezu in Echtzeit unterstützen. Dadurch lassen sie sich für Warnungen und zur schnellen Problemerkennung einsetzen.


|**Metrik**   | **Einheit** | **Granularität** | **Beschreibung** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bytes/s  | 5 Minuten| Durchschnittliche kombinierte Bandbreitennutzung aller Site-to-Site-Verbindungen für das Gateway.     |
|**P2SBandwidth**| Bytes/s  | 1 Minute  | Durchschnittliche kombinierte Bandbreitennutzung aller Point-to-Site-Verbindungen für das Gateway.    |
|**P2SConnectionCount**| Anzahl  | 1 Minute  | Die Anzahl der Point-to-Site-Verbindungen für das Gateway.   |
|**TunnelAverageBandwidth** | Bytes/s    | 5 Minuten  | Durchschnittliche Bandbreitennutzung der für das Gateway erstellten Tunnel. |
|**TunnelEgressBytes** | Byte | 5 Minuten | Ausgehender Datenverkehr in Tunneln, die für das Gateway erstellt wurden.   |
|**TunnelEgressPackets** | Anzahl | 5 Minuten | Anzahl der ausgehenden Pakete in Tunneln, die für das Gateway erstellt wurden.   |
|**TunnelEgressPacketDropTSMismatch** | Anzahl | 5 Minuten | Anzahl der ausgehenden Pakete, die in Tunneln aufgrund eines Konflikts beim Datenverkehrsselektor verworfen werden. |
|**TunnelIngressBytes** | Byte | 5 Minuten | Eingehender Datenverkehr in Tunneln, die für das Gateway erstellt wurden.   |
|**TunnelIngressPackets** | Anzahl | 5 Minuten | Anzahl der eingehenden Pakete in Tunneln, die für das Gateway erstellt wurden.   |
|**TunnelIngressPacketDropTSMismatch** | Anzahl | 5 Minuten | Anzahl der eingehenden Pakete, die in Tunneln aufgrund eines Konflikts beim Datenverkehrsselektor verworfen werden. |

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>Führen Sie die folgenden Schritte aus, um Metriken zu suchen und anzuzeigen:

1. Navigieren Sie im Portal zur Ressource des Gateways für virtuelle Netzwerke.
2. Wählen Sie **Übersicht** aus, um die Metriken für den Gesamteingang und -ausgang des Tunnels anzuzeigen.

   ![Optionen für die Erstellung einer Warnungsregel](./media/monitor-vpn-gateway/overview.png "Sicht")

3. Um die anderen oben aufgeführten Metriken anzuzeigen, klicken Sie unter Ihrer Ressource des Gateways für virtuelle Netzwerke auf den Abschnitt **Metriken**, und wählen Sie die Metrik in der Dropdownliste aus.

   ![Die Schaltfläche „Auswählen“ und das VPN-Gateway in der Liste der Ressourcen](./media/monitor-vpn-gateway/metrics.png "Select")

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Konfigurieren von Warnungen zu Tunnelmetriken finden Sie unter [Einrichten von Warnungen zu VPN Gateway-Metriken](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).

Informationen zum Konfigurieren von Warnungen für Tunnelressourcenprotokolle finden Sie unter [Einrichten von Warnungen für Ressourcenprotokolle von VPN Gateway](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
