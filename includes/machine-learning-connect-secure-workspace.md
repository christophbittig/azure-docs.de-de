---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/26/2021
ms.author: larryfr
ms.openlocfilehash: 3bd72c4930c510df03856ad31bbea51d21829c1e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646104"
---
Verwenden Sie eine der folgenden Methoden, um eine Verbindung mit einem Arbeitsbereich herzustellen, der hinter einem VNet geschützt ist:

* [Azure VPN Gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways): Verbindet lokale Netzwerke über eine private Verbindung mit dem VNet. Die Verbindung wird über das öffentliche Internet hergestellt. Es gibt zwei Arten von VPN Gateways, die Sie verwenden können:

    * [Point-to-Site](/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal): Jeder Clientcomputer verwendet einen VPN-Client, um eine Verbindung mit dem VNet herzustellen.
    * [Site-to-Site](/azure/vpn-gateway/tutorial-site-to-site-portal): Ein VPN-Gerät verbindet das VNet mit Ihrem lokalen Netzwerk.

* [ExpressRoute](https://azure.microsoft.com/services/expressroute/): Verbindet lokale Netzwerke über eine private Verbindung mit der Cloud. Die Verbindung wird über einen Konnektivitätsanbieter hergestellt.
* [Azure Bastion](/azure/bastion/bastion-overview): In diesem Szenario erstellen Sie einen virtuellen Azure-Computer (auch als Jumpbox bezeichnet) im VNet. Anschließend stellen Sie mit Azure Bastion eine Verbindung mit dem virtuellen Computer her. Bastion ermöglicht es Ihnen, über eine RDP- oder SSH-Sitzung in Ihrem lokalen Webbrowser eine Verbindung mit dem virtuellen Computer herzustellen. Anschließend verwenden Sie die Jumpbox als Entwicklungsumgebung. Da sie sich innerhalb des VNet befindet, kann sie direkt auf den Arbeitsbereich zugreifen. Ein Beispiel für die Verwendung einer Jumpbox finden Sie unter [Tutorial: Erstellen eines sicheren Arbeitsbereichs](/azure/machine-learning/tutorial-create-secure-workspace).

> [!IMPORTANT]
> Wenn Sie ein __VPN-Gateway__ oder __ExpressRoute__ verwenden, müssen Sie planen, wie die Namensauflösung zwischen Ihren lokalen Ressourcen und denen im VNet funktioniert. Weitere Informationen finden Sie unter [Verwenden eines benutzerdefinierten DNS-Servers](/azure/machine-learning/how-to-custom-dns).
