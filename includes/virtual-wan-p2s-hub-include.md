---
ms.author: cherylmc
author: cherylmc
ms.date: 08/19/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 6e09f3d115ad11bc6876092b8c4e87cc56c3c22a
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779898"
---
1. Wählen Sie auf der Seite für Ihr **virtuelles WAN** im linken Bereich **Hubs** aus. Wählen Sie auf der Seite **Hubs** die Option **+Neuer Hub** aus.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.png" alt-text="Screenshot des neuen Hubs":::

1. Sehen Sie sich auf der Seite **Virtuellen Hub erstellen** die Registerkarte **Grundlagen** an.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.png" alt-text="Screenshot: Erstellen eines virtuellen Hubs":::

1. Konfigurieren Sie auf der Registerkarte **Grundlegende Einstellungen** die folgenden Einstellungen:

   * **Region**: Wählen Sie die Region aus, in der Sie den virtuellen Hub bereitstellen möchten.
   * **Name**: Der Name, der für den virtuellen Hub verwendet werden soll.
   * **Privater Adressraum des Hubs**: Der Adressbereich des Hubs in CIDR-Notation.

1. Klicken Sie auf die Registerkarte **Point-to-Site**, um die Konfigurationsseite für „Point-to-Site“ zu öffnen. Klicken Sie auf **Ja,** um die Point-to-Site-Einstellungen anzuzeigen.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-point-to-site.png" alt-text="Screenshot der Konfiguration des virtuellen Hubs mit ausgewählter Point-to-Site-Konfiguration":::

1. Konfigurieren Sie die folgenden Einstellungen:

   * **Gatewayskalierungseinheiten**: Steht für die Aggregatkapazität des Benutzer-VPN-Gateways. Wenn Sie 40 oder mehr Gatewayskalierteinheiten auswählen, müssen Sie Ihren Clientadresspool entsprechend planen. Informationen dazu, wie sich diese Einstellung auf den Clientadresspool auswirkt, finden Sie unter [Informationen zu Clientadresspools](../articles/virtual-wan/about-client-address-pools.md). Informationen zu Gatewayskalierungseinheiten finden Sie in den [häufig gestellten Fragen](../articles/virtual-wan/virtual-wan-faq.md#for-user-vpn-point-to-site--how-many-clients-are-supported).
   * **Point-to-Site-Konfiguration**: Wählen Sie die Benutzer-VPN-Konfiguration aus, die Sie in einem der vorherigen Schritte erstellt haben.
   * **Routingpräferenz**: Die Azure-Routingpräferenz ermöglicht es Ihnen zu wählen, wie Ihr Datenverkehr zwischen Azure und dem Internet geleitet wird. Sie können auswählen, ob der Datenverkehr entweder über das Microsoft-Netzwerk oder über das ISP-Netzwerk (öffentliches Internet) geleitet werden soll. Diese Optionen werden auch als Cold Potato-Routing bzw. Hot Potato-Routing bezeichnet. Die öffentliche IP-Adresse in Virtual WAN wird vom Dienst basierend auf der ausgewählten Routingoption zugewiesen. Weitere Informationen zur Routingpräferenz über das Microsoft-Netzwerk oder ISP finden Sie im Artikel [Routingpräferenz](../articles/virtual-network/routing-preference-overview.md).
   * **Clientadresspool**: Der Adresspool, aus dem VPN-Clients automatisch IP-Adressen zugewiesen werden. Weitere Informationen finden Sie unter [Informationen zu Clientadresspools](../articles/virtual-wan/about-client-address-pools.md).
   * **Benutzerdefinierte DNS-Server**: Die IP-Adresse des/der DNS-Server(s), die von den Clients verwendet werden. Sie können bis zu 5 angeben.

1. Wählen Sie zum Überprüfen Ihrer Einstellungen **Überprüfen + erstellen** aus.

1. Klicken Sie nach der Validierung auf **Erstellen**. Das Erstellen eines Hubs kann 30 Minuten oder länger dauern.