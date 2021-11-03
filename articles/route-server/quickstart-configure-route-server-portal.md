---
title: 'Schnellstart: Erstellen und Konfigurieren einer Route Server-Instanz mithilfe des Azure-Portals'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe des Azure-Portals eine Route Server-Instanz erstellen und konfigurieren.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 09/08/2021
ms.author: duau
ms.openlocfilehash: 7dfded652c14ec6805b7fa48004cf5dfe8d72b5f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131061103"
---
# <a name="quickstart-create-and-configure-route-server-using-the-azure-portal"></a>Schnellstart: Erstellen und Konfigurieren einer Route Server-Instanz mithilfe des Azure-Portals

In diesem Artikel wird beschrieben, wie Sie Azure Route Server mithilfe des Azure-Portals für das Peering mit einem virtuellen Netzwerkgerät (Network Virtual Appliance, NVA) in Ihrem virtuellen Netzwerk konfigurieren. Azure Route Server lernt Routen vom NVA und programmiert sie auf den virtuellen Computern im virtuellen Netzwerk. Azure Route Server kündigt die virtuellen Netzwerkrouten auch für das NVA an. Weitere Informationen finden Sie unter [Azure Route Server](overview.md).

:::image type="content" source="media/quickstart-configure-route-server-portal/environment-diagram.png" alt-text="Diagramm: Route Server-Bereitstellungsumgebung mit dem Azure-Portal" border="false":::

> [!IMPORTANT]
> Wenn Sie vor dem 1. September eine Azure Route Server-Instanz erstellt haben und ihr keine öffentliche IP-Adresse zugeordnet ist, müssen Sie die Route Server-Instanz neu erstellen, damit sie zu Verwaltungszwecken eine IP-Adresse abrufen kann.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Beachten Sie die [Diensteinschränkungen für Azure Route Server](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Erstellen einer Route Server-Instanz

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Melden Sie sich bei Ihrem Azure-Konto an, und wählen Sie Ihr Abonnement aus.

Navigieren Sie in einem Browser zum [Azure-Portal](https://portal.azure.com) , und melden Sie sich mit Ihrem Azure-Konto an.

### <a name="create-a-route-server"></a>Erstellen einer Route Server-Instanz

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, suchen Sie **Routenserver**, und wählen Sie die Option dann aus.

1. Wählen Sie **+ Create new route server** (+ Neuen Routenserver erstellen) aus.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="Screenshot: Landing Page von Route Server"::: 

1. Geben Sie auf der Seite **Routenserver erstellen** die erforderlichen Informationen ein, oder wählen Sie sie aus.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/create-route-server-page.png" alt-text="Screenshot: Seite „Routenserver erstellen“":::     

    | Einstellungen | Wert |
    |----------|-------|
    | Subscription | Wählen Sie das Azure-Abonnement aus, das Sie zum Bereitstellen der Route Server-Instanz verwenden möchten. |
    | Ressourcengruppe | Wählen Sie eine Ressourcengruppe aus, in der die Route Server-Instanz erstellt werden soll. Erstellen Sie eine Ressourcengruppe, wenn noch keine vorhanden ist. |
    | Name | Geben Sie einen Namen für die Route Server-Instanz ein. |
    | Region | Wählen Sie die Region aus, in der die Route Server-Instanz erstellt werden soll. Wählen Sie dieselbe Region wie für das virtuelle Netzwerk aus, das Sie zuvor erstellt haben, damit das virtuelle Netzwerk in der Dropdownliste angezeigt wird. |
    | Virtual Network | Wählen Sie das virtuelle Netzwerk aus, in dem die Route Server-Instanz erstellt werden soll. Sie können ein neues virtuelles Netzwerk erstellen oder ein bereits vorhandenes virtuelles Netzwerk auswählen. Stellen Sie bei Verwendung eines vorhandenen virtuellen Netzwerks sicher, dass es über ausreichend Platz für mindestens ein /27-Subnetz verfügt, um die Anforderung an das Route Server-Subnetz zu erfüllen. Sollte Ihr virtuelles Netzwerk nicht in der Dropdownliste angezeigt werden, überprüfen Sie, ob Sie die richtige Ressourcengruppe oder Region ausgewählt haben. |
    | Subnet | Nach dem Erstellen oder Auswählen eines virtuellen Netzwerks wird das Subnetzfeld angezeigt. Dieses Subnetz ist nur für Route Server vorgesehen. Wählen Sie **Subnetzkonfiguration verwalten** aus, und erstellen Sie das Azure Route Server-Subnetz. Wählen Sie **+ Subnetz** aus, und erstellen Sie ein Subnetz unter Verwendung der folgenden Richtlinien:</br><br>- Das Subnetz muss *RouteServerSubnet* heißen.</br><br>- Das Subnetz muss mindestens die Größe „/27“ haben.</br> |
    | Öffentliche IP-Adresse | Erstellen Sie eine neue öffentliche IP-Ressource, oder wählen Sie eine vorhandene aus, um sie dem Route Server zuzuweisen. Um die Konnektivität mit dem Back-End-Dienst sicherzustellen, der die Route Server-Konfiguration verwaltet, ist eine öffentliche IP-Adresse erforderlich. |

1. Wählen Sie **Überprüfen und erstellen** aus, überprüfen Sie die Zusammenfassung, und wählen Sie dann die Option **Erstellen** aus. 

    > [!NOTE]
    > Die Bereitstellung von Route Server dauert etwa 20 Minuten.

## <a name="set-up-peering-with-nva"></a>Einrichten des Peerings mit NVA

Der Abschnitt unterstützt Sie beim Konfigurieren des BGP-Peerings mit Ihrem NVA.

1. Navigieren Sie im Azure-Portal zu [Route Server](./overview.md), und wählen Sie die Route Server-Instanz aus, die Sie konfigurieren möchten.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/select-route-server.png" alt-text="Screenshot: Route Server-Liste"::: 

1. Wählen Sie im linken Navigationsbereich unter *Einstellungen* die Option **Peers** aus. Wählen Sie anschließend **+ Hinzufügen** aus, um einen neuen Peer hinzuzufügen.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/peers-landing-page.png" alt-text="Screenshot: Landing Page für Peers"::: 

1. Geben Sie die folgenden Informationen zum NVA-Peer ein:

    :::image type="content" source="./media/quickstart-configure-route-server-portal/add-peer-page.png" alt-text="Screenshot: Seite zum Hinzufügen von Peers":::

    | Einstellungen | Wert |
    |----------|-------|
    | Name | Geben Sie einen Namen für das Peering zwischen Route Server und NVA ein. |
    | ASN |  Geben Sie die ASN (Autonomous Systems Number, autonome Systemnummer) Ihrer NVA ein. |
    | IPv4-Adresse | Geben Sie die IP-Adresse des NVA ein, mit dem die Route Server-Instanz zum Einrichten von BGP kommunizieren wird. |

1. Wählen Sie **Hinzufügen** aus, um diesen Peer hinzuzufügen.

## <a name="complete-the-configuration-on-the-nva"></a>Abschließen der Konfiguration auf dem NVA

Sie benötigen die Peer-IP-Adressen und die ASN von Azure Route Server, um die Konfiguration auf Ihrem NVA abzuschließen und eine BGP-Sitzung einzurichten. Diese Informationen erhalten Sie auf der Übersichtsseite Ihrer Route Server-Instanz.

:::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-overview.png" alt-text="Screenshot: Übersichtsseite von Route Server":::

## <a name="configure-route-exchange"></a>Konfigurieren des Routenaustauschs

Wenn Sie über ein ExpressRoute-Gateway und/oder ein VPN-Gateway verfügen und diese Routen mit Route Server austauschen sollen, können Sie den Routenaustausch aktivieren.

1. Navigieren Sie im Azure-Portal zu [Route Server](./overview.md), und wählen Sie die Route Server-Instanz aus, die Sie konfigurieren möchten.

1. Wählen Sie im linken Navigationsbereich unter *Einstellungen* die Option **Konfiguration** aus.

1. Wählen Sie für die Einstellung **Branch-to-Branch** die Option **Aktivieren** und anschließend **Speichern** aus.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/enable-route-exchange.png" alt-text="Screenshot: Aktivieren des Routenaustauschs":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie Azure Route Server nicht mehr benötigen, wählen Sie auf der Übersichtsseite **Löschen** aus, um die Bereitstellung von Route Server aufzuheben.

:::image type="content" source="./media/quickstart-configure-route-server-portal/delete-route-server.png" alt-text="Screenshot: Löschen von Route Server":::

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich nach dem Erstellen der Azure Route Server-Instanz darüber, wie Azure Route Server mit ExpressRoute- und VPN-Gateways interagiert: 

> [!div class="nextstepaction"]
> [Unterstützung von Azure ExpressRoute und Azure VPN](expressroute-vpn-support.md)