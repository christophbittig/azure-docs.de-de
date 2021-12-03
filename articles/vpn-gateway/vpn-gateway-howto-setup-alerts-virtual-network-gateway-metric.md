---
title: Einrichten von Warnungen zu Azure VPN Gateway-Metriken
description: Hier erfahren Sie, wie Sie das Azure-Portal verwenden, um Azure Monitor-Warnungen basierend auf Metriken für VPN-Gateways virtueller Netzwerke einzurichten.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: e17f86d08b5892c7df0a761e53e1f16dd43f127a
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158302"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Einrichten von Warnungen zu VPN Gateway-Metriken

Dieser Artikel hilft Ihnen beim Einrichten von Warnungen zu den Azure VPN Gateway-Metriken. Azure Monitor bietet die Möglichkeit, Warnungen für Azure-Ressourcen einzurichten. Sie können Warnungen für virtuelle Netzwerkgateways vom Typ „VPN“ einrichten.

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

## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Einrichten von Azure Monitor-Warnungen auf Basis von Metriken mithilfe des Azure-Portals

Die folgenden Beispielschritte erstellen für Folgendes eine Warnung für ein Gateway:

- **Metrik:** TunnelAverageBandwidth
- **Bedingung:** Bandbreite > 10 Bytes/Sekunde
- **Fenster:** 5 Minuten
- **Warnungsaktion:** Email



1. Wechseln Sie zur virtuellen Netzwerkgatewayressource, und wählen Sie **Warnungen** auf der Registerkarte **Überwachung** aus. Erstellen Sie dann eine neue Warnungsregel, oder bearbeiten Sie eine bestehende Warnungsregel.

   ![Optionen für die Erstellung einer Warnungsregel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Erstellen")

2. Wählen Sie Ihr VPN-Gateway als Ressource aus.

   ![Die Schaltfläche „Auswählen“ und das VPN-Gateway in der Liste der Ressourcen](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Select")

3. Wählen Sie eine Metrik aus, die für die Warnung konfiguriert werden soll.

   ![Ausgewählte Metrik in der Metrikliste](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Select")
4. Konfigurieren Sie die Signallogik. Es gibt drei Komponenten:

    a. **Dimensions** (Dimensionen): Wenn die Metrik über Dimensionen verfügt, können Sie bestimmte Dimensionswerte auswählen, sodass die Warnung nur Daten dieser Dimension auswertet. Diese sind optional.

    b. **Bedingung:** Dies ist der Vorgang zum Auswerten des Metrikwerts.

    c. **Time**: Geben Sie die Granularität der metrischen Daten und den Zeitraum für die Auswertung der Warnung an.

   ![Details zur Konfiguration der Signallogik](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Select")

5. Wählen Sie **Warnungsregeln verwalten** aus, um die konfigurierten Regeln anzuzeigen.

   ![Schaltfläche zum Verwalten von Warnungsregeln](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Select")

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Konfigurieren von Warnungen für Tunnelressourcenprotokolle finden Sie unter [Einrichten von Warnungen für Ressourcenprotokolle von VPN Gateway](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
