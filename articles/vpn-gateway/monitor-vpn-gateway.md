---
title: Überwachen von Azure VPN Gateway
description: Erfahren Sie, wie VPN Gateway-Metriken über Azure Monitor angezeigt werden.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/08/2021
ms.author: alzam
ms.openlocfilehash: 8ae6519881dd0e41cde8ed0fa4d7ffc64f35bdf2
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058787"
---
# <a name="monitoring-vpn-gateway"></a>Überwachen von VPN Gateway

Sie können Azure-VPN-Gateways mit Azure Monitor überwachen. In diesem Artikel werden die Metriken erläutert, die über das Portal verfügbar sind. Metriken sind einfach und können Szenarien nahezu in Echtzeit unterstützen. Dadurch lassen sie sich für Warnungen und zur schnellen Problemerkennung einsetzen.


| **Metrik**                                 | **Einheit**     | **Granularität**     | **Beschreibung**                                                                         |
| -------------------------------------------| ------------ | ------------------- | --------------------------------------------------------------------------------------- |
| **Status des BGP-Peers**                        | Anzahl        | 5 Minuten           | Durchschnittlicher BGP-Konnektivitätsstatus pro Peer und pro Instanz.                              |
| **Angekündigte BGP-Routen**                  | Anzahl        | 5 Minuten           | Anzahl von Routen, die pro Peer und Instanz angekündigt wurden.                                  |
| **Gelernte BGP-Routen**                     | Anzahl        | 5 Minuten           | Anzahl von Routen, die pro Peer und Instanz gelernt wurden.                                     |
| **P2S-Gatewaybandbreite**                  | Bytes/s      | 1 Minute            | Durchschnittliche kombinierte Bandbreitennutzung aller Point-to-Site-Verbindungen für das Gateway. |
| **S2S-Gatewaybandbreite**                  | Bytes/s      | 5 Minuten           | Durchschnittliche kombinierte Bandbreitennutzung aller Site-to-Site-Verbindungen für das Gateway.  |
| **Anzahl der P2S-Verbindungen**                   | Anzahl        | 1 Minute            | Die Anzahl der Point-to-Site-Verbindungen für das Gateway.                                      |
| **Bandbreite des Tunnels**                       | Bytes/s      | 5 Minuten           | Durchschnittliche Bandbreitennutzung der für das Gateway erstellten Tunnel.                        |
| **Vom Tunnel ausgehende Bytes**                    | Byte        | 5 Minuten           | Anzahl ausgehender Bytes aus einem Tunnel.                                                 |
| **Anzahl ausgehender gelöschter Pakete eines Tunnels**        | Anzahl        | 5 Minuten           | Anzahl der ausgehenden von einem Tunnel verworfenen Pakete.                                         |
| **Vom Tunnel ausgehende Pakete**                  | Anzahl        | 5 Minuten           | Anzahl ausgehender Pakete aus einem Tunnel.                                               |
| **Ausgehende gelöschte Pakete eines Tunnels durch einen TS-Konflikt**  | Anzahl        | 5 Minuten           | Anzahl der ausgehenden Pakete, die in Tunneln aufgrund eines Konflikts beim Datenverkehrsselektor verworfen werden.      |
| **In Tunnel eingehende Bytes**                   | Byte        | 5 Minuten           | Anzahl der eingehenden Bytes in einem Tunnel.                                                   |
| **Anzahl eingehender gelöschter Pakete eines Tunnels**       | Anzahl        | 5 Minuten           | Anzahl der eingehenden von einem Tunnel verworfenen Pakete.                                         |
| **In Tunnel eingehende Pakete**                 | Anzahl        | 5 Minuten           | Anzahl der eingehenden Pakete in einem Tunnel.                                                 |
| **Eingehende gelöschte Pakete eines Tunnels durch einen TS-Konflikt** | Anzahl        | 5 Minuten           | Anzahl der eingehenden Pakete, die in Tunneln aufgrund eines Konflikts beim Datenverkehrsselektor verworfen werden.      |
| **MMSA-Anzahl des Tunnels**                      | Anzahl        | 5 Minuten           | Anzahl der vorhandenen Sicherheitszuordnungen im Hauptmodus.                                      |
| **PPS-Spitzenwert eines Tunnels**                        | Anzahl        | 5 Minuten           | Maximale Anzahl von Paketen pro Sekunde pro Tunnel.                                            |
| **QMSA-Anzahl des Tunnels**                      | Anzahl        | 5 Minuten           | Anzahl der vorhandenen Sicherheitszuordnungen im Schnellmodus.                                     |
| **Gesamtanzahl von Flows für einen Tunnel**                | Anzahl        | 5 Minuten           | Anzahl der pro Tunnel erstellten unterschiedlichen Flows.                                            |
| **Anzahl der Benutzer-VPN-Routen**                   | Anzahl        | 5 Minuten           | Anzahl der Benutzer-VPN-Routen, die auf dem VPN Gateway konfiguriert sind.                                |
| **Anzahl der VNET-Adresspräfixe**              | Anzahl        | 5 Minuten           | Anzahl der VNet-Adresspräfixe, die vom Gateway verwendet/angekündigt wurden.                |

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>Mit den folgenden Schritten suchen Sie nach Metriken und zeigen sie an:

1. Navigieren Sie im Portal zur Ressource des Gateways für virtuelle Netzwerke.
2. Wählen Sie **Übersicht** aus, um die Metriken für den Gesamteingang und -ausgang des Tunnels anzuzeigen.

   ![Optionen für die Erstellung einer Warnungsregel](./media/monitor-vpn-gateway/overview.png "Sicht")

3. Um die anderen oben aufgeführten Metriken anzuzeigen, klicken Sie unter Ihrer Ressource des Gateways für virtuelle Netzwerke auf den Abschnitt **Metriken**, und wählen Sie die Metrik in der Dropdownliste aus.

   ![Die Schaltfläche „Auswählen“ und das VPN-Gateway in der Liste der Ressourcen](./media/monitor-vpn-gateway/metrics.png "Select")

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Konfigurieren von Warnungen zu Tunnelmetriken finden Sie unter [Einrichten von Warnungen zu VPN Gateway-Metriken](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).

Informationen zum Konfigurieren von Warnungen für Tunnelressourcenprotokolle finden Sie unter [Einrichten von Warnungen für Ressourcenprotokolle von VPN Gateway](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
