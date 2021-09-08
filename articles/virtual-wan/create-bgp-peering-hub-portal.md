---
title: Erstellen eines BGP-Peerings mit einem virtuellem Hub (Vorschauversion) in Azure-Portal
titleSuffix: Azure Virtual WAN
description: Erfahren Sie, wie Sie ein BGP-Peering mit einem Virtual WAN Hubrouter erstellen.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/06/2021
ms.author: cherylmc
ms.openlocfilehash: 6c1d845e4f4ad3a61e3131f6451e12070f2af48c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355929"
---
# <a name="how-to-create-bgp-peering-with-virtual-hub-preview---azure-portal"></a>Erstellen eines BGP-Peerings mit einem virtuellem Hub (Vorschauversion) in Azure-Portal

In diesem Artikel erfahren Sie, wie Sie einen Azure Virtual WAN Hubrouter für das Peer mit einem Network Virtual Appliance (NVA) in Ihrem virtuellen Netzwerk mithilfe des Azure Portals konfigurieren. Der Router des virtuellen Hubs lernt das Routen aus dem virtuellen Netzwerkgerät in einem Spoke-VNET, das mit einem Virtual WAN-Hub verbunden ist. Der Router des virtuellen Hubs kündigt auch die Routen des virtuellen Netzwerks an das virtuelle Netzwerkgerät an. Weitere Informationen finden Sie unter [Szenario: BGP-Peering mit einem virtuellen Hub](scenario-bgp-peering-hub.md).

[!INCLUDE [Gated public preview SLA link](../../includes/virtual-wan-gated-public-preview-sla.md)]

:::image type="content" source="./media/create-bgp-peering-hub-portal/diagram.png" alt-text="Diagramm der Konfiguration":::.

## <a name="prerequisites"></a>Voraussetzungen

> [!IMPORTANT]
> Das BGP-Peering mit Virtual WAN Hub-Feature befindet sich derzeit in der geschlossenen öffentlichen Vorschau. Wenn Sie dieses Feature ausprobieren möchten, senden Sie zusammen mit der Ressourcen-ID Ihrer Virtual WAN Ressource eine E-Mail an **previewbgpwithvhub@microsoft.com** . 
>
> Um die Ressourcen-ID zu finden, öffnen Sie das Azure-Portal, navigieren Sie zu Ihrer Virtual WAN-Ressource und klicken Sie auf **Einstellungen > Eigenschaften > Ressourcen-ID**.<br> Beispiel: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualWans/<virtualWANname>`
>

Vergewissern Sie sich vor Beginn der Konfiguration, dass die folgenden Voraussetzungen erfüllt sind bzw. Folgendes vorhanden ist:

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Erstellen eines Virtual WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Erstellen eines Hubs

Ein Hub ist ein virtuelles Netzwerk, das Gateways für Verbindungen vom Typ „Site-to-Site“, „ExpressRoute“ oder „Point-to-Site“ enthalten kann. Nachdem der Hub erstellt wurde, werden Ihnen für den Hub auch dann Kosten berechnet, wenn Sie keine Websites zuordnen.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Herstellen einer Verbindung zwischen VNET und Hub

In diesem Abschnitt erstellen Sie eine Verbindung zwischen Ihrem Hub und einem VNET.

[!INCLUDE [Connect a VNet to a hub](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="configure-a-bgp-peer"></a>Konfigurieren eines BGP-Peers

1.  Öffnen Sie das [Azure-Vorschauportal](https://aka.ms/azurecortexv2) mithilfe von [https://aka.ms/azurecortexv2](https://aka.ms/azurecortexv2). Das BGP-Peering mit der Virtual WAN Hub-Funktion befindet sich derzeit in der verwalteten Vorschau und die Konfigurationsseiten sind in dem regulären Azure-Portal nicht verfügbar.

1.  Klicken Sie auf der Portalseite für Ihr virtuelles WAN im Abschnitt **Konnektivität** auf **Hubs**, um die Seite mit den Hubs anzuzeigen. Klicken Sie auf einen Hub, um einen BGP-Peer zu konfigurieren.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/hubs.png" alt-text="Ein Screenshot, der die Hubs zeigt":::.

1.  Wählen Sie auf der **Virtual Hub**-Seite unter dem Abschnitt **Routing** die Option **BGP-Peers** und klicken Sie auf **+ Hinzufügen**, um einen BGP-Peer hinzuzufügen.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/bgp-peers.png" alt-text="3.":::

1.  Füllen Sie auf der Seite **BGP-Peer** alle Felder aus.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/add-peer.png" alt-text="4.":::

    * **Name** – Der Ressourcenname zur Identifizierung eines bestimmten BGP-Peers. 
    * **ASN** – Die ASN für den BGP-Peer.
    * **IPv4 Adresse** – Die IPv4-Adresse des BGP-Peers.
    * **Virtual Network Verbindung** – Wählen Sie den Verbindungsbezeichner aus, der dem virtuellen Netzwerk entspricht, das den BGP-Peer hostet.

1.  Klicken Sie auf **Hinzufügen**, um die BGP-Peer-Konfiguration abzuschließen und den Peer anzuzeigen.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/view-peer.png" alt-text="Ein Screenshot, der den hinzugefügten Peer zeigt":::.

## <a name="modify-a-bgp-peer"></a>Ändern eines BGP-Peers

1. Klicken Sie in der Ressource **Virtueller Hub** auf **BGP-Peers** und wählen Sie den BGP-Peer aus. Klicken Sie auf **…** und wählen Sie dann **Bearbeiten** aus.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/modify.png" alt-text="Ein Screenshot, der „Bearbeiten“ zeigt.":::

1. Wenn der BGP-Peer geändert ist, klicken Sie zum Speichern auf **Hinzufügen**.

## <a name="delete-a-bgp-peer"></a>Löschen eines BGP-Peers

1. Klicken Sie in der Ressource **Virtueller Hub** auf **BGP-Peers** und wählen Sie den BGP-Peer aus. Klicken Sie auf **…** und wählen sie dann **Löschen** aus.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/delete.png" alt-text="Ein Screenshot, der das Löschen eines Peers zeigt":::.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu BGP-Szenarien finden Sie unter [Szenario: BGP-Peering mit einem virtuellen Hub](scenario-bgp-peering-hub.md).
