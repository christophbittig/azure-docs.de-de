---
title: Aktivieren von OpenVPN für P2S-VPN-Gateways
titleSuffix: Azure VPN Gateway
description: Hier erfahren Sie, wie Sie das OpenVPN-Protokoll in VPN-Gateways für Point-to-Site-Konfigurationen aktivieren.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/28/2021
ms.author: cherylmc
ms.openlocfilehash: ab7826a89dc879c1bad62e8c56415047d164c6f9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355041"
---
# <a name="configure-openvpn-for-point-to-site-vpn-gateways"></a>Konfigurieren von OpenVPN-Clients für Point-to-Site-VPN-Gateways

In diesem Artikel erfahren Sie, wie Sie **OpenVPN® Protocol** für Azure VPN Gateway einrichten. Sie können die Anweisungen für das Portal oder PowerShell nutzen.

## <a name="prerequisites"></a>Voraussetzungen

* In diesem Artikel wird davon ausgegangen, dass Sie in einer Point-to-Site-Umgebung arbeiten. Erstellen Sie andernfalls mithilfe einer der folgenden Methoden eine. Beachten Sie beim Erstellen Ihres Gateways, dass die **Basic**-SKU den OpenVPN-Tunneltyp nicht unterstützt.

  * [Portal: Erstellen einer Point-to-Site-Verbindung](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell: Erstellen einer Point-to-Site-Verbindung](vpn-gateway-howto-point-to-site-rm-ps.md)

* Wenn Sie bereits über ein VPN-Gateway verfügen, stellen Sie sicher, dass es nicht die **Basic**-SKU verwendet. Die Basic-SKU wird für OpenVPN nicht unterstützt. Weitere Informationen zu SKUs finden Sie unter [Konfigurationseinstellungen für VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md). Informationen zum Ändern der Größe einer Basic-SKU finden Sie unter [Ändern der Größe eines Legacygateways](vpn-gateway-about-skus-legacy.md#resource-manager). 

## <a name="portal"></a>Portal

1. Navigieren Sie im Portal zu **Gateway für virtuelle Netzwerke > Point-to-Site-Konfiguration**.
1. Wählen Sie für **Tunneltyp** in der Dropdownliste **OpenVPN (SSL)** aus.

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="Auswählen von „OpenVPN (SSL)“ in der Dropdownliste":::
1. Speichern Sie Ihre Änderungen, und fahren Sie mit den Anweisungen unter **Nächste Schritte** fort.

## <a name="powershell"></a>PowerShell

1. Aktivieren Sie OpenVPN auf Ihrem Gateway mithilfe des folgenden Beispiels, und passen Sie die Werte nach Bedarf an.

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName TestRG1 -name VNet1GW
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Fahren Sie mit **Nächste Schritte** fort.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Konfigurieren von OpenVPN finden Sie unter [Konfigurieren von OpenVPN-Clients](vpn-gateway-howto-openvpn-clients.md).

**„OpenVPN“ ist eine Marke von OpenVPN Inc.**
