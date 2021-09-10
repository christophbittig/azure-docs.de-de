---
title: 'Schnellstart: Erstellen und Konfigurieren einer Route Server-Instanz mithilfe der Azure-Befehlszeilenschnittstelle'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle eine Route Server-Instanz erstellen und konfigurieren.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 08/17/2021
ms.author: duau
ms.openlocfilehash: 91ad4ce80a8e61b06b3597fe8088a0eb7f002d5e
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122968230"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>Schnellstart: Erstellen und Konfigurieren einer Route Server-Instanz mithilfe der Azure-Befehlszeilenschnittstelle 

In diesem Artikel wird beschrieben, wie Sie Azure Route Server mithilfe von Azure PowerShell für das Peering mit einem virtuellen Netzwerkgerät (Network Virtual Appliance, NVA) in Ihrem virtuellen Netzwerk konfigurieren. Route Server lernt Routen von Ihrem NVA und programmiert sie auf den virtuellen Computern im virtuellen Netzwerk. Azure Route Server kündigt die virtuellen Netzwerkrouten auch für das NVA an. Weitere Informationen finden Sie unter [Azure Route Server](overview.md).

:::image type="content" source="media/quickstart-configure-route-server-portal/environment-diagram.png" alt-text="Diagramm: Route Server-Bereitstellungsumgebung mit der Azure CLI" border="false":::

> [!IMPORTANT]
> Azure Route Server (Vorschau) befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

##  <a name="prerequisites"></a>Voraussetzungen 

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Stellen Sie sicher, dass Sie über die aktuelle Azure-Befehlszeilenschnittstelle verfügen, oder verwenden Sie Azure Cloud Shell im Portal. 
* Beachten Sie die [Diensteinschränkungen für Azure Route Server](route-server-faq.md#limitations). 

##  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Melden Sie sich bei Ihrem Azure-Konto an, und wählen Sie Ihr Abonnement aus. 

Um mit der Konfiguration zu beginnen, melden Sie sich bei Ihrem Azure-Konto an. Wenn Sie die Cloud Shell-Option zum Ausprobieren verwenden, werden Sie automatisch angemeldet. Verwenden Sie die folgenden Beispiele, um eine Verbindung herzustellen:

```azurecli-interactive
az login
```

Überprüfen Sie die Abonnements für das Konto.

```azurecli-interactive
az account list
```

Wählen Sie das Abonnement aus, für das eine ExpressRoute-Verbindung erstellt werden soll.

```azurecli-interactive
az account set \
    --subscription "<subscription ID>"
```

## <a name="create-a-resource-group-and-a-virtual-network"></a>Erstellen einer Ressourcengruppe und eines virtuellen Netzwerks 

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Bevor Sie einen Azure Route Server erstellen können, müssen Sie eine Ressourcengruppe zum Hosten des Route Servers erstellen. Erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe. In diesem Beispiel wird eine Ressourcengruppe namens **myRouteServerRG** am Standort **westus** erstellt:

```azurecli-interactive
az group create \
    --name myRouteServerRG \
    --location westus
```

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) ein virtuelles Netzwerk. Dieses Beispiel erstellt ein virtuelles Standardnetzwerk namens **myVirtualNetwork**. Wenn Sie bereits über ein virtuelles Netzwerk verfügen, können Sie mit dem nächsten Abschnitt fortfahren.

```azurecli-interactive
az network vnet create \
    --name myVirtualNetwork \
    --resource-group myRouteServerRG \
    --address-prefix 10.0.0.0/16 
``` 

### <a name="add-a-dedicated-subnet"></a>Hinzufügen eines dedizierten Subnetzes 

Azure Route Server erfordert ein dediziertes Subnetz namens *RouteServerSubnet*. Die Subnetzgröße muss mindestens /27 oder ein kurzes Präfix (z. B. /26 oder /25) sein, oder Sie erhalten eine Fehlermeldung, wenn Sie den Route Server bereitstellen. Erstellen Sie eine Subnetzkonfiguration namens **RouteServerSubnet** mit [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create):

1. Führen Sie den folgenden Befehl aus, um das *RouteServerSubnet* Ihrem virtuellen Netzwerk hinzuzufügen.

    ```azurecli-interactive 
    az network vnet subnet create \
        --name RouteServerSubnet \
        --resource-group myRouteServerRG \
        --vnet-name myVirtualNetwork \
        --address-prefix 10.0.0.0/24)
    ``` 

1. Notieren Sie sich die RouteServerSubnet-ID. Um die Ressourcen-ID des *RouteServerSubnet* abzurufen und in der Variablen `subnet_id` zu speichern, verwenden Sie [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show):

    ```azurecli-interactive 
    subnet_id=$(az network vnet subnet show \
        --name RouteServerSubnet \
        --resource-group myRouteServerRG \
        --vnet-name myVirtualNetwork \
        --query id -o tsv) 

    echo $subnet_id
    ```

## <a name="create-the-route-server"></a>Erstellen der Route Server-Instanz 

1. Um die Konnektivität mit dem Back-End-Dienst sicherzustellen, der die Route Server-Konfiguration verwaltet, ist das Zuweisen einer öffentlichen IP-Adresse erforderlich. Erstellen Sie mit [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) eine öffentliche Standard-IP-Adresse namens **RouteServerIP**:

    ```azurecli-interactive
    az network public-ip create \
        --name RouteServerIP \
        --resource-group myRouteServerRG \
        --version IPv4 \
        --sku Standard
    ```

2. Erstellen Sie den Azure Route Server mit [az network routeserver create](/cli/azure/network/routeserver#az_network_routeserver_create). In diesem Beispiel wird ein Azure Route Server namens **myRouteServer** erstellt. Bei *hosted-subnet* handelt es sich um die Ressourcen-ID des im vorherigen Abschnitt erstellten RouteServerSubnet.

    ```azurecli-interactive
    az network routeserver create \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --hosted-subnet $subnet_id \
        --public-ip-address RouteServerIP
    ``` 

## <a name="create-bgp-peering-with-an-nva"></a>Erstellen von BGP-Peering mit einem NVA 

Verwenden Sie [az network routeserver peering create](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_create), um ein BGP-Peering zwischen dem Route Server und dem NVA einzurichten: 

Die `peer-ip` ist die dem NVA zugewiesene IP-Adresse des virtuellen Netzwerks. Die `peer-asn` ist die im NVA konfigurierte ASN (Autonomous System Number, autonome Systemnummer). Die ASN kann eine beliebige 16-Bit-Zahl sein, die nicht im Bereich von 65515-65520 liegt. Die ASNs in diesem Bereich sind von Microsoft reserviert.

```azurecli-interactive 
az network routeserver peering create \
    --name myNVA \
    --peer-ip 192.168.0.1 \
    --peer-asn 65501 \
    --routeserver myRouteServer \
    --resource-group myRouteServerRG
``` 

Wenn Sie das Peering mit einem anderen NVA oder einer anderen Instanz desselben NVAs zwecks Redundanz einrichten möchten, verwenden Sie denselben Befehl wie oben mit anderem *PeerName*, anderer *PeerIp* und anderer *PeerAsn*.

## <a name="complete-the-configuration-on-the-nva"></a>Abschließen der Konfiguration auf dem NVA 

Um die Konfiguration auf dem NVA abzuschließen und die BGP-Sitzungen zu aktivieren, benötigen Sie die IP-Adresse und die ASN von Azure Route Server. Sie können diese Informationen mithilfe von [az network routeserver show](/cli/azure/network/routeserver#az_network_routeserver_show) abrufen:

```azurecli-interactive 
az network routeserver show \
    --name myRouteServer \
    --resource-group myRouteServerRG 
``` 

Die Ausgabe sieht dann wie folgt aus:

``` 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>Konfigurieren des Routenaustauschs 

Wenn Sie über ein ExpressRoute- und Azure VPN-Gateway in demselben virtuellen Netzwerk verfügen und diese die Routen austauschen sollen, können Sie den Routenaustausch auf der Azure Route Server-Instanz aktivieren.

> [!IMPORTANT]
> Stellen Sie bei Bereitstellungen ohne bestehende Infrastruktur sicher, dass Sie zuerst das Azure VPN-Gateway erstellen, bevor Sie die Azure Route Server-Instanz erstellen. Andernfalls schlägt die Bereitstellung von Azure VPN Gateway fehl.
> 

1. Um den Routenaustausch zwischen Azure Route Server und den Gateways zu aktivieren, verwenden Sie [az network routeserver update](/cli/azure/network/routeserver#az_network_routeserver_update) mit dem Flag „--allow-b2b-traffic“, das auf **true** festgelegt ist:

    ```azurecli-interactive 
    az network routeserver update \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --allow-b2b-traffic true 
    ``` 

2. Um den Routenaustausch zwischen Azure Route Server und den Gateways zu deaktivieren, verwenden Sie [az network routeserver update](/cli/azure/network/routeserver#az_network_routeserver_update) mit dem Flag „--allow-b2b-traffic“, das auf **false** festgelegt ist:

    ```azurecli-interactive
    az network routeserver update \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --allow-b2b-traffic false 
    ``` 

## <a name="troubleshooting"></a>Problembehandlung 

Verwenden Sie [az network routeserver peering list-advertised-routes](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_list_advertised_routes), um vom Azure Route Server angekündigte Routen anzuzeigen:

```azurecli-interactive 
az network routeserver peering list-advertised-routes \
    --name myNVA \
    --routeserver myRouteServer \
    --resource-group myRouteServerRG
```

Verwenden Sie [az network routeserver peering list-learned-routes](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_list_learned_routes), um vom Azure Route Server gelernte Routen anzuzeigen:

```azurecli-interactive
az network routeserver peering list-learned-routes \
    --name myNVA \
    --routeserver myRouteServer
    --resource-group myRouteServerRG \
``` 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Azure Route Server nicht mehr benötigen, verwenden Sie den ersten Befehl, um das BGP-Peering zu entfernen, und dann den zweiten Befehl, um den Route Server zu entfernen. 

1. Entfernen Sie das BGP-Peering zwischen Azure Route Server und einem NVA mit [az network routeserver peering delete](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_delete):

    ```azurecli-interactive
    az network routeserver peering delete \
        --name myNVA \
        --routeserver myRouteServer \
        --resource-group myRouteServerRG
    ``` 

2. Entfernen Sie den Azure Route Server mit [az network routeserver delete](/cli/azure/network/routeserver#az_network_routeserver_delete): 

    ```azurecli-interactive 
    az network routeserver delete \
        --name myRouteServer \
        --resource-group myRouteServerRG
    ``` 

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich nach dem Erstellen der Azure Route Server-Instanz darüber, wie Azure Route Server mit ExpressRoute- und VPN-Gateways interagiert: 

> [!div class="nextstepaction"]
> [Unterstützung von Azure ExpressRoute und Azure VPN](expressroute-vpn-support.md)
 
