---
title: Peering lokaler Umgebungen mit Azure VMware Solution
description: Hier erfahren Sie, wie Sie ExpressRoute Global Reach-Peering mit einer privaten Cloud in Azure VMware Solution erstellen.
ms.topic: tutorial
ms.custom: contperf-fy21q4, contperf-fy22q1
ms.date: 07/28/2021
ms.openlocfilehash: b930aab15ef9af8e43919af1671f44c20e6b2075
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730230"
---
# <a name="peer-on-premises-environments-to-azure-vmware-solution"></a>Peering lokaler Umgebungen mit Azure VMware Solution

Nachdem Sie Ihre private Azure VMware Solution-Cloud bereitgestellt haben, verbinden Sie sie mit Ihrer lokalen Umgebung. Über ExpressRoute Global Reach wird Ihre lokale Umgebung mit Ihrer privaten Azure VMware Solution-Cloud verbunden. Die ExpressRoute Global Reach-Verbindung wird zwischen der ExpressRoute-Leitung der privaten Cloud und einer vorhandenen ExpressRoute-Verbindung mit Ihren lokalen Umgebungen hergestellt. 

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="Diagramm: ExpressRoute Global Reach-Netzwerkkonnektivität für die lokale Umgebung" lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

>[!NOTE]
>Sie können auch eine Verbindung per VPN herstellen, aber die Beschreibung der Vorgehensweise würde den Rahmen dieser Schnellstartanleitung sprengen.

In diesem Artikel führen Sie Folgendes durch:

> [!div class="checklist"]
> * Erstellen eines ExpressRoute-Autorisierungsschlüssels in der lokalen ExpressRoute-Leitung
> * Peering der privaten Cloud mit Ihrer lokalen ExpressRoute-Leitung
> * Überprüfen der Netzwerkkonnektivität für die lokale Umgebung

Wenn Sie fertig sind, folgen Sie den empfohlenen nächsten Schritten am Ende, um mit den Schritten dieses Leitfadens für erste Schritte fortzufahren.

## <a name="prerequisites"></a>Voraussetzungen

- Lesen Sie in der Dokumentation die Informationen zum [Aktivieren der Konnektivität in verschiedenen Azure-Abonnements](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  

- Eine separate, funktionierende ExpressRoute-Leitung für die Verbindungsherstellung zwischen lokalen Umgebungen und Azure. Dies ist _Circuit 1_ für das Peering.

- Stellen Sie sicher, dass alle Gateways mit dem Dienst des ExpressRoute-Anbieters eine ASN (Autonomous System Number, autonome Systemnummer) mit 4 Bytes unterstützen. Azure VMware Solution verwendet öffentliche ASNs mit 4 Bytes zum Ankündigen von Routen.

>[!NOTE]
>Stellen Sie beim Ankündigen einer Standardroute (0.0.0.0/0) zu Azure sicher, dass zusätzlich zur Standardroute noch eine spezifischere Route mit Ihren lokalen Netzwerken angekündigt wird, um den Verwaltungszugriff auf Azure VMware Solution zu ermöglichen. Eine einzelne Route der Art „0.0.0.0/0“ wird vom Verwaltungsnetzwerk von Azure VMware Solution verworfen, um den erfolgreichen Betrieb des Diensts sicherzustellen.

## <a name="create-an-expressroute-auth-key-in-the-on-premises-expressroute-circuit"></a>Erstellen eines ExpressRoute-Autorisierungsschlüssels in der lokalen ExpressRoute-Leitung

Der Leitungsbesitzer erstellt eine Autorisierung, die wiederum einen Autorisierungsschlüssel erstellt. Mit diesem kann ein Leitungsbenutzer seine virtuellen Netzwerkgateways mit der ExpressRoute-Leitung verbinden. Eine Autorisierung gilt nur für jeweils eine Verbindung.

> [!NOTE]
> Jede Verbindung erfordert eine separate Autorisierung.

1. Wählen Sie auf dem Blatt **ExpressRoute-Leitungen** unter „Einstellungen“ die Option **Autorisierungen** aus.

1. Geben Sie den Namen für den Autorisierungsschlüssel ein, und wählen Sie **Speichern** aus.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="Auswählen von „Autorisierungen“ und Eingeben des Namens für den Autorisierungsschlüssel":::

   Nach der Erstellung wird der neue Schlüssel in der Liste der Autorisierungsschlüssel für die Leitung angezeigt.

1. Kopieren Sie den Autorisierungsschlüssel und die ExpressRoute-ID. Diese Angaben werden im nächsten Schritt benötigt, um das Peering abzuschließen.

## <a name="peer-private-cloud-to-on-premises"></a>Peering zwischen privater Cloud und lokaler Umgebung 
Nachdem Sie einen Autorisierungsschlüssel für die ExpressRoute-Leitung der privaten Cloud erstellt haben, können Sie sie mittels Peering mit Ihrer lokalen ExpressRoute-Leitung verknüpfen. Das Peering erfolgt über die lokale ExpressRoute-Leitung im **Azure-Portal**. Sie verwenden die Ressourcen-ID (ID der ExpressRoute-Leitung) und den Autorisierungsschlüssel der ExpressRoute-Leitung Ihrer privaten Cloud dazu, das Peering abzuschließen.

1. Wählen Sie in der privaten Cloud unter „Verwalten“ die Option **Konnektivität** > **ExpressRoute Global Reach** > **Hinzufügen** aus.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Screenshot: Registerkarte „ExpressRoute Global Reach“ in der privaten Azure VMware Solution-Cloud":::

1. Geben Sie die ExpressRoute-ID und den Autorisierungsschlüssel ein, die Sie im vorherigen Abschnitt erstellt haben.

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Screenshot: Dialogfeld zum Eingeben der Verbindungsinformationen":::   

1. Klicken Sie auf **Erstellen**. Die neue Verbindung wird in der Liste „Lokale Cloudverbindungen“ angezeigt.

>[!TIP]
>Sie können eine Verbindung aus der Liste löschen oder trennen, indem Sie **Mehr** auswählen.  
>
>:::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Screenshot: Trennen oder Löschen einer lokalen Verbindung in Azure VMware Solution":::


## <a name="verify-on-premises-network-connectivity"></a>Überprüfen der Netzwerkkonnektivität für die lokale Umgebung

Sie sollten nun auf Ihrem **lokalen Edgerouter** sehen können, wie die NSX-T-Netzwerksegmente und die Azure VMware Solution-Verwaltungssegmente per ExpressRoute verbunden sind.

>[!IMPORTANT]
>Die Umgebungen von Unternehmen unterscheiden sich, und in einigen Fällen kann es erforderlich sein, dass für diese Routen die Weitergabe zurück an das lokale Netzwerk zugelassen wird.  

## <a name="next-steps"></a>Nächste Schritte
Fahren Sie mit dem nächsten Tutorial fort, um das VMware HCX-Add-On in Ihrer privaten Azure VMware Solution-Cloud zu installieren.

> [!div class="nextstepaction"]
> [Installieren von VMware HCX](install-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
