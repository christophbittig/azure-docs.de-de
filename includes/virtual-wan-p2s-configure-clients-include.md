---
ms.author: cherylmc
author: cherylmc
ms.date: 07/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 06b4d9f2830bfd9599aee30c68235d3078eb32fa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734621"
---
**OpenVPN**

* [Konfigurieren eines OpenVPN-Clients für Azure Virtual WAN](../articles/virtual-wan/howto-openvpn-clients.md)
* [Azure AD-Authentifizierung – Windows 10](../articles/virtual-wan/openvpn-azure-ad-client.md)
* [Azure AD-Authentifizierung – macOS](../articles/virtual-wan/openvpn-azure-ad-client-mac.md)

**IKEv2**

1. Wählen Sie die VPN-Clientkonfigurationsdateien, die der Architektur des Windows-Computers entsprechen. Wählen Sie für eine 64-Bit-Prozessorarchitektur das Installer-Paket „VpnClientSetupAmd64“ aus. Wählen Sie für eine 32-Bit-Prozessorarchitektur das Installer-Paket „VpnClientSetupX86“ aus.

1. Doppelklicken Sie auf das Paket, um es zu installieren. Sollte eine SmartScreen-Popupmeldung angezeigt werden, wählen Sie **Weitere Informationen** und anschließend **Trotzdem ausführen** aus.

1. Navigieren Sie auf dem Clientcomputer zu **Netzwerkeinstellungen**, und wählen Sie **VPN** aus. Die VPN-Verbindung zeigt den Namen des virtuellen Netzwerks an, mit dem eine Verbindung hergestellt wird.

1. Bevor Sie versuchen, eine Verbindung herzustellen, überprüfen Sie, ob Sie auf dem Clientcomputer ein Clientzertifikat installiert haben. Ein Clientzertifikat ist für die Authentifizierung erforderlich, wenn Sie den Typ „native Azure-Zertifikatauthentifizierung“ verwenden. Weitere Informationen zum Generieren von Zertifikaten finden Sie unter [Generieren von Zertifikaten](../articles/virtual-wan/certificates-point-to-site.md). Weitere Informationen zum Installieren eines Clientzertifikats finden Sie unter [Installieren eines Clientzertifikats](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).
