---
author: cherylmc
ms.author: cherylmc
ms.date: 10/15/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 5cac86b599e7e52cbc6018de6d97392bf28e4608
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520222"
---
1. Öffnen Sie die Virtual WAN-Instanz, die Sie erstellt haben.

1. Wählen Sie einen virtuellen Hub aus, der der Virtual WAN-Instanz zugeordnet ist, um die Seite für den Hub zu öffnen.

1. Löschen Sie alle Gatewayentitäten in der unten angegebenen Reihenfolge für den Gatewaytyp. Dieser Vorgang kann bis zu 30 Minuten dauern.

    **VPN:**  
   1. VPN-Standorte trennen  
   1. VPN-Verbindungen löschen  
   1. VPN-Gateways löschen  

    **ExpressRoute:**  
   1. ExpressRoute-Verbindungen löschen  
   1. ExpressRoute Gateways löschen  

1. Sie können den Hub entweder jetzt oder zu einem späteren Zeitpunkt löschen, wenn Sie die Ressourcengruppe löschen.

1. Wiederholen Sie diesen Schritt für alle Hubs, die der Virtual WAN-Instanz zugeordnet sind.

1. Navigieren Sie im Azure-Portal zur Ressourcengruppe.

1. Wählen Sie die Option **Ressourcengruppe löschen**. Hierdurch werden alle in der Ressourcengruppe enthaltenen Elemente gelöscht, einschließlich der Hubs und der Virtual WAN-Instanz.