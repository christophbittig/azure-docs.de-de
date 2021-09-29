---
title: Bereitstellen und Konfigurieren von Azure VMware Solution
description: Hier erfahren Sie, wie Sie die in der Planungsphase gesammelten Informationen verwenden, um die private Azure VMware Solution-Cloud bereitzustellen und zu konfigurieren.
ms.topic: tutorial
ms.custom: contperf-fy22q1, devx-track-azurecli
ms.date: 07/28/2021
ms.openlocfilehash: 0df1634d047dfe6abfaa717fd10b75b99f100076
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623929"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Bereitstellen und Konfigurieren von Azure VMware Solution

Nachdem Sie [Ihre Bereitstellung geplant haben](plan-private-cloud-deployment.md), stellen Sie Ihre private Azure VMware Solution-Cloud bereit und konfigurieren sie. 

Das Diagramm zeigt den Bereitstellungsworkflow von Azure VMware Solution. 

:::image type="content" source="media/deploy-azure-vmware-solution-workflow.png" alt-text="Diagramm des Bereitstellungsworkflows von Azure VMware Solution." lightbox="media/deploy-azure-vmware-solution-workflow.png" border="false":::

In dieser Anleitung führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Registrieren des Ressourcenanbieters und Erstellen einer privaten Cloud
> * Herstellen der Verbindung mit einem neuen oder vorhandenen ExpressRoute-Gateway für virtuelle Netzwerke
> * Überprüfen der Netzwerkverbindung

Wenn Sie fertig sind, folgen Sie den empfohlenen nächsten Schritten am Ende, um mit den Schritten dieses Leitfadens für erste Schritte fortzufahren.

## <a name="register-the-microsoftavs-resource-provider"></a>Registrieren des Microsoft.AVS-Ressourcenanbieters

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]

## <a name="create-an-azure-vmware-solution-private-cloud"></a>Erstellen einer privaten Cloud von Azure VMware Solution

[!INCLUDE [create-private-cloud-azure-portal-steps](includes/create-private-cloud-azure-portal-steps.md)]

>[!NOTE]
>Eine umfassende Übersicht über diesen Schritt vermittelt das Video [Azure VMware Solution: Bereitstellen](https://www.youtube.com/embed/gng7JjxgayI).


## <a name="connect-to-azure-virtual-network-with-expressroute"></a>Herstellen einer Verbindung mit Azure Virtual Network über ExpressRoute

In der Planungsphase haben Sie definiert, ob ein *vorhandenes* oder *neues* ExpressRoute-VNET-Gateway verwendet wird.  

Das :::image type="content" source="media/connect-expressroute-vnet-workflow.png" alt-text="Diagramm, zeigt den Workflow zum Verbinden von Azure Virtual Network mit ExpressRoute in Azure VMware Solution." border="false":::

>[!IMPORTANT]
>[!INCLUDE [disk-pool-planning-note](includes/disk-pool-planning-note.md)] 

### <a name="use-a-new-expressroute-virtual-network-gateway"></a>Verwenden eines neuen ExpressRoute-VNET-Gateways

>[!IMPORTANT]
>Sie benötigen ein virtuelles Netzwerk mit einem Gatewaysubnetz, das noch **nicht** über ein VNET-Gateway verfügt.

| Wenn | Then  |
| --- | --- |
| Sie noch kein virtuelles Netzwerk besitzen...     |  Erstellen Sie Folgendes:<ol><li><a href="tutorial-configure-networking.md#create-a-vnet-manually">Virtuelles Netzwerk</a></li><li><a href="../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet">GatewaySubnet</a></li><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">Gateway für virtuelle Netzwerke</a></li><li><a href="tutorial-configure-networking.md#connect-expressroute-to-the-virtual-network-gateway">Verbinden von ExpressRoute zum Gateway</a></li></ol>        |
| Sie bereits ein virtuelles Netzwerk **ohne** Gatewaysubnetz besitzen...   | Erstellen Sie Folgendes: <ol><li><a href="../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet">GatewaySubnet</a></li><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">Gateway für virtuelle Netzwerke</a></li><li><a href="tutorial-configure-networking.md#connect-expressroute-to-the-virtual-network-gateway">Verbinden von ExpressRoute zum Gateway</a></li></ol>          |
| Sie bereits ein virtuelles Netzwerk **mit** Gatewaysubnetz besitzen... | Erstellen Sie Folgendes: <ol><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">Gateway für virtuelle Netzwerke</a></li><li><a href="tutorial-configure-networking.md#connect-expressroute-to-the-virtual-network-gateway">Verbinden von ExpressRoute zum Gateway</a></li></ol>    |

### <a name="use-an-existing-virtual-network-gateway"></a>Verwenden eines vorhandenen VNET-Gateways

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="validate-the-connection"></a>Überprüfen der Verbindung

Zwischen der Azure Virtual Network-Instanz, in der die ExpressRoute-Leitung endet, und der privaten Azure VMware Solution-Cloud muss eine Verbindung bestehen. 

1. Verwenden Sie einen [virtuellen Computer](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) in der Azure Virtual Network-Instanz, in der die ExpressRoute-Leitung von Azure VMware Solution endet. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure Virtual Network über ExpressRoute](#connect-to-azure-virtual-network-with-expressroute).  

   1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

   1. Navigieren Sie zu einem ausgeführten virtuellen Computer, und wählen Sie unter **Einstellungen** die Option **Netzwerk** und dann die Netzwerkschnittstellenressource aus.

      :::image type="content" source="../virtual-network/media/diagnose-network-routing-problem/view-nics.png" alt-text="Ein Screenshot, der die Einstellungen für virtuelle Netzwerkschnittstellen zeigt":::.

   1. Wählen Sie auf der linken Seite **Effektive Routen** aus. Eine Liste der Adresspräfixe wird angezeigt, die in dem CIDR-Block vom Typ `/22` enthalten sind, den Sie während der Bereitstellungsphase eingegeben haben.

1. Wenn Sie sich sowohl bei vCenter als auch bei NSX-T Manager anmelden möchten, öffnen Sie einen Webbrowser, und melden Sie sich bei demselben virtuellen Computer an, der auch für die Netzwerkroutenüberprüfung verwendet wird.  

   Sie können die IP-Adressen und Anmeldeinformationen von vCenter und NSX-T Manager im Azure-Portal ermitteln.  Wählen Sie Ihre private Cloud und dann **Verwalten** > **Identität** aus.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Screenshot: URLs und Anmeldeinformationen für vCenter und NSX-T Manager in der privaten Cloud" border="true":::


## <a name="next-steps"></a>Nächste Schritte

Im nächsten Tutorial verbinden Sie Azure VMware Solution über ExpressRoute mit Ihrem lokalen Netzwerk.

> [!div class="nextstepaction"]
> [Tutorial: Zusammenfassen lokaler Umgebungen mittels Peering zu einer privaten Cloud](tutorial-expressroute-global-reach-private-cloud.md)
