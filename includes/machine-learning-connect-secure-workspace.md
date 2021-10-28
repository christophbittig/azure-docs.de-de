---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/26/2021
ms.author: larryfr
ms.openlocfilehash: 966232f893ef404c3c4d6e5d3bc4929f74d7bd62
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215133"
---
Verwenden Sie eine der folgenden Methoden, um eine Verbindung mit einem Arbeitsbereich herzustellen, der hinter einem VNet geschützt ist:

* [Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md): Verbindet lokale Netzwerke über eine private Verbindung mit dem VNet. Die Verbindung wird über das öffentliche Internet hergestellt. Es gibt zwei Arten von VPN Gateways, die Sie verwenden können:

    * [Point-to-Site](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md): Jeder Clientcomputer verwendet einen VPN-Client, um eine Verbindung mit dem VNet herzustellen.
    * [Site-to-Site](../articles/vpn-gateway/tutorial-site-to-site-portal.md): Ein VPN-Gerät verbindet das VNet mit Ihrem lokalen Netzwerk.

* [ExpressRoute](https://azure.microsoft.com/services/expressroute/): Verbindet lokale Netzwerke über eine private Verbindung mit der Cloud. Die Verbindung wird über einen Konnektivitätsanbieter hergestellt.
* [Azure Bastion](../articles/bastion/bastion-overview.md): In diesem Szenario erstellen Sie einen virtuellen Azure-Computer (auch als Jumpbox bezeichnet) im VNet. Anschließend stellen Sie mit Azure Bastion eine Verbindung mit dem virtuellen Computer her. Bastion ermöglicht es Ihnen, über eine RDP- oder SSH-Sitzung in Ihrem lokalen Webbrowser eine Verbindung mit dem virtuellen Computer herzustellen. Anschließend verwenden Sie die Jumpbox als Entwicklungsumgebung. Da sie sich innerhalb des VNet befindet, kann sie direkt auf den Arbeitsbereich zugreifen. Ein Beispiel für die Verwendung einer Jumpbox finden Sie unter [Tutorial: Erstellen eines sicheren Arbeitsbereichs](../articles/machine-learning/tutorial-create-secure-workspace.md).

> [!IMPORTANT]
> Wenn Sie ein __VPN-Gateway__ oder __ExpressRoute__ verwenden, müssen Sie planen, wie die Namensauflösung zwischen Ihren lokalen Ressourcen und denen im VNet funktioniert. Weitere Informationen finden Sie unter [Verwenden eines benutzerdefinierten DNS-Servers](../articles/machine-learning/how-to-custom-dns.md).