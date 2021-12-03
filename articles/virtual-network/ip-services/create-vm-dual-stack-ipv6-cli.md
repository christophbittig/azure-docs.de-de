---
title: Erstellen einer virtuellen Azure-Maschine mit einem Dual-Stack-Netzwerk - Azure CLI
titleSuffix: Azure Virtual Network
description: In diesem Artikel erfahren Sie, wie Sie die Azure-Befehlszeilenschnittstelle verwenden, um eine virtuelle Maschine mit einem virtuellen Dual-Stack-Netzwerk in Azure zu erstellen.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 11/11/2021
ms.custom: template-how-to
ms.openlocfilehash: ae8f7fef465b8ba42a058b43ca95fd2334f437c8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495353"
---
# <a name="create-an-azure-virtual-machine-with-a-dual-stack-network-using-the-azure-cli"></a>Erstellen einer virtuellen Azure-Maschine mit einem Dual-Stack-Netzwerk mithilfe der Azure-Befehlszeilenschnittstelle

In diesem Artikel erstellen Sie eine virtuelle Maschine in Azure mit der Azure CLI. Die virtuelle Maschine wird zusammen mit dem Dual-Stack-Netzwerk als Teil der Verfahren erstellt.  Nach der Fertigstellung unterstützt die virtuelle Maschine die IPv4- und IPv6-Kommunikation.  

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Für dieses Tutorial ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe namens **myResourceGroup** am Speicherort **eastus2**.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

In diesem Abschnitt werden Sie ein virtuelles Dual-Stack-Netzwerk für die virtuelle Maschine erstellen.

Verwenden Sie [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create), um ein virtuelles Netzwerk zu erstellen.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 2404:f800:8000:122::/63 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24 2404:f800:8000:122::/64
```

## <a name="create-public-ip-addresses"></a>Erstellen öffentlicher IP-Adressen

In diesem Abschnitt werden Sie zwei öffentliche IP-Adressen erstellen, IPv4 und IPv6. 

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create), um die öffentlichen IP-Adressen zu erstellen.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP-Ipv4 \
    --sku Standard \
    --version IPv4

  az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP-Ipv6 \
    --sku Standard \
    --version IPv6

```
## <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

In diesem Abschnitt werden Sie eine Netzwerksicherheitsgruppe für die virtuelle Maschine und das virtuelle Netzwerk erstellen.

Verwenden Sie [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create), um die Netzwerksicherheitsgruppe zu erstellen.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroup \
    --name myNSG
```

### <a name="create-network-security-group-rules"></a>Erstellen von Netzwerksicherheitsgruppen-Regeln

Sie erstellen eine Regel, die Verbindungen zur virtuellen Maschine auf Port 22 für SSH zulässt. Es wird eine zusätzliche Regel erstellt, um alle Ports für ausgehende Verbindungen zuzulassen.

Verwenden Sie [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create), um die Regeln für die Netzwerksicherheitsgruppe zu erstellen.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNSG \
    --name myNSGRuleSSH \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow \
    --priority 200

  az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNSG \
    --name myNSGRuleAllOUT \
    --protocol '*' \
    --direction outbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range '*' \
    --access allow \
    --priority 200
```

## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

In diesem Abschnitt erstellen Sie die virtuelle Maschine und ihre unterstützenden Ressourcen.

### <a name="create-network-interface"></a>Erstellen einer Netzwerkschnittstelle

Sie verwenden [az network nic create](/cli/azure/network/nic#az_network_nic_create), um die Netzwerkschnittstelle für die virtuelle Maschine zu erstellen. Die öffentlichen IP-Adressen und die zuvor erstellte NSG sind mit der Netzwerkkarte verbunden. Die Netzwerkschnittstelle ist mit dem virtuellen Netzwerk verbunden, das Sie zuvor erstellt haben.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroup \
    --name myNIC1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --public-ip-address myPublicIP-IPv4
```

### <a name="create-ipv6-ip-configuration"></a>IPv6-IP-Konfiguration erstellen

Verwenden Sie [az network nic ip-config create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create), um die IPv6-Konfiguration für die NIC zu erstellen.

```azurecli-interactive
  az network nic ip-config create \
    --resource-group myResourceGroup \
    --name myIPv6config \
    --nic-name myNIC1 \
    --private-ip-address-version IPv6 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --public-ip-address myPublicIP-IPv6
```

### <a name="create-vm"></a>Erstellen eines virtuellen Computers

Verwenden Sie [az vm create](/cli/azure/vm#az_vm_create), um die virtuelle Maschine zu erstellen.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNIC1 \
    --image UbuntuLTS \
    --admin-username azureuser \
    --authentication-type ssh \
    --generate-ssh-keys
```

## <a name="test-ssh-connection"></a>SSH-Verbindung testen

Verwenden Sie [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show), um die IP-Adressen der virtuellen Maschine anzuzeigen.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP-IPv4 \
    --query ipAddress \
    --output tsv
```

```bash
user@Azure:~$ az network public-ip show \
>     --resource-group myResourceGroup \
>     --name myPublicIP-IPv4 \
>     --query ipAddress \
>     --output tsv
20.119.201.208
```

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP-IPv6 \
    --query ipAddress \
    --output tsv
```

```bash
user@Azure:~$ az network public-ip show \
>     --resource-group myResourceGroup \
>     --name myPublicIP-IPv6 \
>     --query ipAddress \
>     --output tsv
2603:1030:408:6::9d
```

Öffnen Sie mit dem folgenden Befehl eine SSH-Verbindung zur virtuellen Maschine. Ersetzen Sie die IP-Adresse durch die IP-Adresse Ihrer virtuellen Maschine.

```bash
  ssh azureuser@20.119.201.208
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie nicht mehr benötigt werden, verwenden Sie den Befehl [az group delete](/cli/azure/group#az_group_delete), um die Ressourcengruppe, die virtuelle Maschine und alle zugehörigen Ressourcen zu entfernen.

```azurecli-interactive
  az group delete \
    --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie eine virtuelle Azure-Maschine mit einem Dual-Stack-Netzwerk erstellen.

Weitere Informationen zu IPv6 und IP-Adressen in Azure finden Sie unter:

- [Übersicht über IPv6 für Azure Virtual Network.](ipv6-overview.md)

- [Worum handelt es sich bei Azure Virtual Network-IP-Diensten?](ip-services-overview.md)


