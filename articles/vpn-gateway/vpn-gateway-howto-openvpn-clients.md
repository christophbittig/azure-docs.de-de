---
title: Konfigurieren von OpenVPN-Clients für P2S-VPN-Gateways
titleSuffix: Azure VPN Gateway
description: Hier erfahren Sie, wie Sie OpenVPN-Clients für Azure VPN Gateway konfigurieren. Dieser Artikel unterstützt Sie beim Konfigurieren von Windows-, Linux-, iOS- und Mac-Clients.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/27/2021
ms.author: cherylmc
ms.openlocfilehash: 8c9ddff536c74182e1c13d51dde2900f07fb7470
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355042"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurieren von OpenVPN-Clients für Azure VPN Gateway

Dieser Artikel hilft Ihnen beim Konfigurieren von Clients mit dem **OpenVPN&reg;-Protokoll**.

## <a name="before-you-begin"></a>Voraussetzungen

Vergewissern Sie sich, dass Sie die Schritte zum Konfigurieren von OpenVPN für Ihr VPN-Gateway abgeschlossen haben. Einzelheiten finden Sie unter [Konfigurieren von OpenVPN für Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

## <a name="vpn-client-configuration-files"></a>Dateien für die VPN-Clientkonfiguration

Sie können die Dateien für die VPN-Clientkonfiguration über das Portal oder mithilfe von PowerShell generieren und herunterladen. Mit beiden Methoden wird die gleiche ZIP-Datei zurückgegeben. Entzippen Sie die Datei, um den Ordner „OpenVPN“ anzuzeigen.

:::image type="content" source="./media/howto-openvpn-clients/download.png" alt-text="Screenshot: Hervorgehobene Option „VPN-Client herunterladen“" :::

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Nächste Schritte

Wenn die VPN-Clients Zugriff auf Ressourcen in einem anderen VNET haben sollen, führen Sie die Anweisungen im Artikel [Konfigurieren einer VNET-zu-VNET-VPN-Gatewayverbindung](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) aus, um eine VNET-zu-VNET-Verbindung einzurichten. Stellen Sie sicher, dass BGP (Border Gateway Protocol) für die Gateways und die Verbindungen aktiviert ist, da andernfalls kein Datenverkehr fließt.

**„OpenVPN“ ist eine Marke von OpenVPN Inc.**
