---
title: Installieren eines Point-to-Site-Clientzertifikats
titleSuffix: Azure VPN Gateway
description: Hier erfahren Sie, wie Sie Clientzertifikate für die P2S-Zertifikatauthentifizierung unter Windows, Mac und Linux installieren.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/27/2021
ms.author: cherylmc
ms.openlocfilehash: 4643502a16982fc3b3c2a659a4dbc127e515d349
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346147"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Installieren eines Clientzertifikats für die Zertifikatauthentifizierung bei P2S-Verbindungen

Wenn ein P2S-VPN-Gateway so konfiguriert ist, dass es eine Zertifikatauthentifizierung erfordert, muss auf jedem Clientcomputer ein lokales Clientzertifikat installiert sein. Dieser Artikel hilft Ihnen, ein Clientzertifikat zu installieren.

Wenn Sie ein Clientzertifikat aus einem selbstsignierten Stammzertifikat generieren möchten, lesen Sie einen der folgenden Artikel:

* [Generieren von Zertifikaten – PowerShell](vpn-gateway-certificates-point-to-site.md)
* [Generieren von Zertifikaten – MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
* [Generieren von Zertifikaten – Linux](vpn-gateway-certificates-point-to-site-linux.md) 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Mac-VPN-Clients werden nur für das [Resource Manager-Bereitstellungsmodell](../azure-resource-manager/management/deployment-models.md) unterstützt. Für das klassische Bereitstellungsmodell werden sie nicht unterstützt.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

Das Linux-Clientzertifikat wird auf dem Client als Teil der Clientkonfiguration installiert. Anweisungen finden Sie unter [Clientkonfiguration – Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit den Point-to-Site-Konfigurationsschritten zum [Erstellen und Installieren von VPN-Clientkonfigurationsdateien](point-to-site-vpn-client-configuration-azure-cert.md) fort.
