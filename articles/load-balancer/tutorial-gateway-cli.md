---
title: 'Tutorial: Erstellen einer Gateway Load Balancer-Instanz – Azure CLI'
titleSuffix: Azure Load Balancer
description: In diesem Tutorial erfahren Sie, wie Sie eine Gateway Load Balancer-Instanz mit der Azure CLI erstellen.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-tutorial, ignite-fall-2021
ms.openlocfilehash: 13435307985f3471800a6bfc3697c7bc0fc58ee1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101462"
---
# <a name="tutorial-create-a-gateway-load-balancer-using-the-azure-cli"></a>Tutorial: Erstellen einer Gateway Load Balancer-Instanz mit der Azure CLI

Azure Load Balancer umfasst Standard-, Basic- und Gateway-SKUs. Gateway Load Balancer wird zum transparenten Einfügen von virtuellen Netzwerkgeräten (Network Virtual Appliances, NVA) verwendet. Verwenden Sie Gateway Load Balancer für Szenarien, die hohe Leistung und hohe Skalierbarkeit von NVAs benötigen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Registrieren einer Preview-Funktion.
> * Erstellen Sie ein virtuelles Netzwerk.
> * Erstellen einer Netzwerksicherheitsgruppe.
> * Erstellen einer Gateway Load Balancer-Instanz.
> * Verketten eines Load Balancer-Front-Ends mit der Gateway Load Balancer-Instanz.

> [!IMPORTANT]
> Die Azure Gateway Load Balancer-Instanz befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für dieses Tutorial ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

- Ein Azure-Konto mit einem aktiven Abonnement.[Kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Eine vorhandene öffentliche Azure Load Balancer-Instanz mit Standard-SKU. Weitere Informationen zum Erstellen einer Load Balancer-Instanz finden Sie im Artikel zum **[Erstellen einer öffentlichen Load Balancer-Instanz mit der Azure CLI](quickstart-load-balancer-standard-public-cli.md)** .
    - Für dieses Tutorial wird in den Beispielen **myLoadBalancer** als Name für die vorhandene Load Balancer-Instanz verwendet.

## <a name="register-preview-feature"></a>Registrieren einer Previewfunktion

Im Rahmen der öffentlichen Vorschau von Gateway Load Balancer muss der Anbieter in Ihrem Azure-Abonnement registriert sein.

Verwenden Sie [az feature register](/cli/azure/feature#az_feature_register), um das **AllowGatewayLoadBalancer**-Anbieter-Feature zu registrieren:

```azurecli-interactive
  az feature register \
    --name AllowGatewayLoadBalancer \
    --namespace Microsoft.Network
```

Verwenden Sie [az provider register](/cli/azure/provider#az_provider_register), um den **Microsoft.Network**-Ressourcenanbieter zu registrieren:

```azurecli-interactive
  az provider register \
    --namespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe:

```azurecli-interactive
  az group create \
    --name TutorGwLB-rg \
    --location eastus

```

## <a name="configure-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks

Für die Ressourcen, die sich im Back-End-Pool der Gateway Load Balancer-Instanz befinden, ist ein virtuelles Netzwerk erforderlich.  

### <a name="create-virtual-network"></a>Virtuelles Netzwerk erstellen

Erstellen Sie das virtuelle Netzwerk mit [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
  az network vnet create \
    --resource-group TutorGwLB-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-bastion-public-ip-address"></a>Erstellen einer öffentlichen Bastion-IP-Adresse

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create), um eine öffentliche IP-Adresse für den Azure Bastion-Host zu erstellen

```azurecli-interactive
az network public-ip create \
    --resource-group TutorGwLB-rg \
    --name myBastionIP \
    --sku Standard \
    --zone 1 2 3
```

### <a name="create-bastion-subnet"></a>Erstellen eines Bastionsubnetzes

Erstellen Sie mit [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) das Bastionsubnetz.

```azurecli-interactive
az network vnet subnet create \
    --resource-group TutorGwLB-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/27
```

### <a name="create-bastion-host"></a>Erstellen eines Bastionhosts

Verwenden Sie [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create), um einen Bastionhost für die sichere Verwaltung von Ressourcen im virtuellen Netzwerk bereitzustellen.

```azurecli-interactive
az network bastion create \
    --resource-group TutorGwLB-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Die Bereitstellung des Azure Bastion-Hosts kann einige Minuten dauern.

## <a name="configure-nsg"></a>Konfigurieren der NSG

Verwenden Sie das folgende Beispiel, um eine Netzwerksicherheitsgruppe zu erstellen. Sie konfigurieren die NSG-Regeln, die für den Netzwerkdatenverkehr in dem zuvor erstellten virtuellen Netzwerk erforderlich sind.

### <a name="create-nsg"></a>NSG erstellen

Verwenden Sie [azure network nsg create](/cli/azure/network/nsg#az_network_nsg_create) für die erste Erstellung der NSG.

```azurecli-interactive
  az network nsg create \
    --resource-group TutorGwLB-rg \
    --name myNSG
```

### <a name="create-nsg-rules"></a>Erstellen von NSG-Regeln

Verwenden Sie [azure network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) für die erste Erstellung der NSG-Regeln.

```azurecli-interactive
  az network nsg rule create \
    --resource-group TutorGwLB-rg \
    --nsg-name myNSG \
    --name myNSGRule-AllowAll \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '0.0.0.0/0' \
    --source-port-range '*' \
    --destination-address-prefix '0.0.0.0/0' \
    --destination-port-range '*' \
    --access allow \
    --priority 100

  az network nsg rule create \
    --resource-group TutorGwLB-rg \
    --nsg-name myNSG \
    --name myNSGRule-AllowAll-TCP-Out \
    --protocol 'TCP' \
    --direction outbound \
    --source-address-prefix '0.0.0.0/0' \
    --source-port-range '*' \
    --destination-address-prefix '0.0.0.0/0' \
    --destination-port-range '*' \
    --access allow \
    --priority 100
```

## <a name="configure-gateway-load-balancer"></a>Konfigurieren einer Gateway Load Balancer-Instanz

In diesem Abschnitt erstellen Sie die Konfiguration und stellen Sie die Gateway Load Balancer-Instanz bereit.  

### <a name="create-gateway-load-balancer"></a>Erstellen einer Gateway Load Balancer-Instanz

Erstellen Sie mit [az network lb create](/cli/azure/network/lb#az_network_lb_create) den Load Balancer.

```azurecli-interactive
  az network lb create \
    --resource-group TutorGwLB-rg \
    --name myLoadBalancer-gw \
    --sku Gateway \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --backend-pool-name myBackendPool \
    --frontend-ip-name myFrontEnd
```

### <a name="create-tunnel-interface"></a>Erstellen einer Tunnelschnittstelle

Eine interne Schnittstelle wird automatisch mit Azure CLI mit dem **`--identifier`** von **900** und **`--port`** **10800** erstellt.

Sie verwenden [az network lb address-pool tunnel-interface add](/cli/azure/network/lb/address-pool/tunnel-interface#az_network_lb_address_pool_tunnel_interface_add), um eine externe Tunnelschnittstelle für den Load Balancer zu erstellen. 

```azurecli-interactive
  az network lb address-pool tunnel-interface add \
    --address-pool myBackEndPool \
    --identifier '901' \
    --lb-name myLoadBalancer-gw \
    --protocol VXLAN \
    --resource-group TutorGwLB-rg \
    --type External \
    --port '10801'
```

### <a name="create-health-probe"></a>Erstellen eines Integritätstests
Um die Integrität der Back-End-Instanzen im Load Balancer zu überwachen, ist ein Integritätstest erforderlich. Zum Erstellen eines Integritätstests verwenden Sie [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create).

```azurecli-interactive
  az network lb probe create \
    --resource-group TutorGwLB-rg \
    --lb-name myLoadBalancer-gw \
    --name myHealthProbe \
    --protocol http \
    --port 80 \
    --path '/' \
    --interval '5' \
    --threshold '2'
    
```

### <a name="create-load-balancing-rule"></a>Erstellen einer Lastenausgleichsregel

Datenverkehr, der an die Back-End-Instanzen gerichtet ist, wird mit einer Lastenausgleichsregel weitergeleitet. Verwenden Sie [az network lb rule create](/cli/azure/network/lb/probe#az_network_lb_rule_create), um die Lastenausgleichsregel zu erstellen.

```azurecli-interactive
  az network lb rule create \
    --resource-group TutorGwLB-rg \
    --lb-name myLoadBalancer-gw \
    --name myLBRule \
    --protocol All \
    --frontend-port 0 \
    --backend-port 0 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```

## <a name="add-network-virtual-appliances-to-the-gateway-load-balancer-backend-pool"></a>Hinzufügen virtueller Netzwerkgeräte zum Back-End-Pool der Gateway Load Balancer-Instanz
Stellen Sie NVAs über den Azure Marketplace bereit. Fügen Sie nach der Bereitstellung mit [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add) die virtuellen Computer zum Back-End-Pool hinzu.

## <a name="chain-load-balancer-frontend-to-gateway-load-balancer"></a>Verketten eines Load Balancer-Front-Ends mit der Gateway Load Balancer-Instanz

In diesem Beispiel verketten Sie das Front-End eines Standardlastenausgleichs mit der Gateway Load Balancer-Instanz. 

Sie fügen das Front-End zur Front-End-IP eines vorhandenen Lastenausgleichs in Ihrem Abonnement hinzu.

Verwenden Sie [az network lb frontend-ip show](/cli/azure/network/lb/frontend-ip#az_az_network_lb_frontend_ip_show), um die Ressourcen-ID Ihres Gateway Load Balancer-Front-Ends in eine Variable zu platzieren.

Verwenden Sie [az network lb frontend-ip update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update), um das Front-End der Gateway Load Balancer-Instanz mit Ihrem vorhandenen Lastenausgleich zu verketten.

```azurecli-interactive
  feid=$(az network lb frontend-ip show \
    --resource-group TutorGwLB-rg \
    --lb-name myLoadBalancer-gw \
    --name myFrontend \
    --query id \
    --output tsv)

  az network lb frontend-ip update \
    --resource-group CreatePubLBQS-rg \
    --name myFrontendIP \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIP \
    --gateway-lb $feid

```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Mit dem Befehl [az group delete](/cli/azure/group#az_group_delete) können Sie die Ressourcengruppe, den Lastenausgleich und die restlichen Ressourcen entfernen, wenn Sie sie nicht mehr benötigen.

```azurecli-interactive
  az group delete \
    --name TutorGwLB-rg
```

## <a name="next-steps"></a>Nächste Schritte

Erstellen von virtuellen Netzwerkgeräten in Azure. 

Wählen Sie beim Erstellen der NVAs die in diesem Tutorial erstellten Ressourcen aus:

* Virtuelles Netzwerk

* Subnet

* Netzwerksicherheitsgruppe

* Gateway Load Balancer

Fahren Sie mit dem nächsten Artikel fort, um sich über das Erstellen einer regionsübergreifenden Azure Load Balancer-Instanz zu informieren.
> [!div class="nextstepaction"]
> [Regionsübergreifender Lastenausgleich](tutorial-cross-region-powershell.md)
