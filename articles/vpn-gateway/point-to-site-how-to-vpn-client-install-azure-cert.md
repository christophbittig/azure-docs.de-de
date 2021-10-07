---
title: Installieren eines Point-to-Site-Clientzertifikats
titleSuffix: Azure VPN Gateway
description: Hier erfahren Sie, wie Sie Clientzertifikate für die P2S-Zertifikatauthentifizierung unter Windows, Mac und Linux installieren.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2021
ms.author: cherylmc
ms.openlocfilehash: 917f60440d98924e5339f29fb99587eacf40b415
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766253"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Installieren eines Clientzertifikats für die Zertifikatauthentifizierung bei P2S-Verbindungen

Wenn ein P2S-VPN-Gateway so konfiguriert ist, dass es eine Zertifikatauthentifizierung erfordert, muss auf jedem Clientcomputer ein lokales Clientzertifikat installiert sein. In diesem Artikel wird beschrieben, wie Sie ein Clientzertifikat lokal auf einem Clientcomputer installieren. Sie können auch [Intune](/mem/intune/configuration/vpn-settings-configure) verwenden, um bestimmte VPN-Clientprofile und Zertifikate zu installieren.

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