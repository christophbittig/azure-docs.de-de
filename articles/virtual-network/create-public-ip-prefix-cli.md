---
title: Erstellen eines Präfix für öffentliche IP-Adressen – Azure CLI
titlesuffix: Azure Virtual Network
description: Erfahren Sie, wie Sie mithilfe der Azure CLI ein Präfix für eine öffentliche IP-Adresse erstellen.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 07/13/2021
ms.author: allensu
ms.openlocfilehash: 1b95c4ac4a6dcf6b3f065a6d5952eeff122855f3
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2021
ms.locfileid: "114652869"
---
# <a name="create-a-public-ip-address-prefix-using-azure-cli"></a>Erstellen eines Präfix für öffentliche IP-Adressen – Azure CLI

Sie erhalten Informationen über Präfixe öffentlicher IP-Adressen und darüber, wie Sie diese erstellen, ändern und löschen können. Das Präfix einer öffentlichen IP-Adresse ist ein zusammenhängender Bereich öffentlicher IP-Adressen mit Standard-SKU. 

Wenn Sie eine öffentliche IP-Adressressource erstellen, können Sie eine statische öffentliche IP-Adresse aus dem Präfix zuweisen und diese Adresse VMs, Lastenausgleichsmodulen oder anderen Ressourcen zuordnen. Weitere Informationen finden Sie in der Übersicht [Präfix für öffentliche IP-Adressen](public-ip-address-prefix.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für dieses Tutorial ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe namens **myResourceGroup** am Speicherort **eastus2**.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-public-ip-address-prefix"></a>Erstellen des Präfix einer öffentlichen IP-Adresse

In diesem Abschnitt erstellen Sie mithilfe von Azure PowerShell ein zonenredundantes, zonenbasiertes und nicht zonenbasiertes öffentliches IP-Präfix. 

Die Präfixe in den Beispielen lauten wie folgt:

* **IPv4**: /28 (16 Adressen)

* **IPv6**: /124 (16 Adressen)

Weitere Informationen zu verfügbaren Präfixgrößen finden Sie unter [Präfixgrößen](public-ip-address-prefix.md#prefix-sizes).

Erstellen Sie mit [az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create) ein Präfix für öffentliche IP-Adressen namens **myPublicIpPrefix** in der Region **eastus2**.

### <a name="zone-redundant-ipv4-prefix"></a>Zonenredundantes IPv4-Präfix

Um ein IPv4-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv4** in den Parameter **IpAddressVersion** ein. Um ein zonenredundantes IPv4-Präfix zu erstellen, geben Sie **1,2,3** in den Parameter **Zone** ein.

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv4 \
    --zone 1 2 3
```

### <a name="zonal-ipv4-prefix"></a>Zonenbasiertes IPv4-Präfix

Um ein IPv4-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv4** in den Parameter **IpAddressVersion** ein. Geben Sie im Parameter **Zone** den Wert **2** ein, um ein zonenbasiertes Präfix für IP-Adressen in Zone 2 zu erstellen.

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv4 \
    --zone 2
```

>[!NOTE]
>Die oben genannten Optionen für Zonen sind nur in Regionen mit [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) gültig.

### <a name="non-zonal-ipv4-prefix"></a>Nicht zonenbasiertes IPv4-Präfix

Um ein IPv4-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv4** in den Parameter **IpAddressVersion** ein. Entfernen Sie den Parameter **Zone**, um ein nicht zonenbasiertes IP-Präfix zu erstellen.

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv4
```

Das Entfernen des Parameters **Zone** im Befehl ist für alle Regionen gültig.  

Das Entfernen des Parameters **Zone** ist die Standardauswahl für standardmäßige öffentliche IP-Adressen in Regionen ohne [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

### <a name="zone-redundant-ipv6-prefix"></a>Zonenredundantes IPv6-Präfix

Um ein IPv6-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv6** in den Parameter **IpAddressVersion** ein. Um ein zonenredundantes IPv6-Präfix zu erstellen, geben Sie **1,2,3** in den Parameter **Zone** ein.

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv6 \
    --zone 1 2 3
```

### <a name="zonal-ipv6-prefix"></a>Zonenbasiertes IPv6-Präfix

Um ein IPv6-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv6** in den Parameter **IpAddressVersion** ein. Geben Sie im Parameter **Zone** den Wert **2** ein, um ein zonenbasiertes Präfix für IP-Adressen in Zone 2 zu erstellen.

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv6 \
    --zone 2
```

>[!NOTE]
>Die oben genannten Optionen für Zonen sind nur in Regionen mit [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) gültig.

### <a name="non-zonal-ipv6-prefix"></a>Nicht zonenbasiertes IPv6-Präfix

Um ein IPv6-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv6** in den Parameter **IpAddressVersion** ein. Entfernen Sie den Parameter **Zone**, um ein nicht zonenbasiertes IP-Präfix zu erstellen.

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv6
```

Das Entfernen des Parameters **Zone** im Befehl ist für alle Regionen gültig.  

Das Entfernen des Parameters **Zone** ist die Standardauswahl für standardmäßige öffentliche IP-Adressen in Regionen ohne [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Erstellen einer statischen öffentlichen IP-Adresse aus einem Präfix

Sobald Sie ein Präfix erstellt haben, müssen Sie statische IP-Adressen aus dem Präfix erstellen. In diesem Abschnitt erstellen Sie eine statische IP-Adresse aus dem Präfix, das Sie zuvor erstellt haben.

Erstellen Sie mit **az network public-ip create** eine öffentliche IP-Adresse im Präfix [myPublicIpPrefix](/cli/azure/network/public-ip#az_network_public_ip_create).

### <a name="ipv4-address"></a>IPv4-Adresse

Um eine öffentliche IPv4-IP-Adressen zu erstellen, geben Sie **IPv4** in den Parameter **IpAddressVersion** ein.

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group myResourceGroup \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv4
```

### <a name="ipv6-address"></a>IPv6-Adresse

Um ein IPv6-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv6** in den Parameter **IpAddressVersion** ein.

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group myResourceGroup \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv6
```

>[!NOTE]
>Nur statische öffentliche IP-Adressen, die mit der Standard-SKU erstellt wurden, können aus dem Bereich des Präfix zugewiesen werden. Weitere Informationen zu SKUs für öffentliche IP-Adressen finden Sie unter [Öffentliche IP-Adressen](./public-ip-addresses.md#public-ip-addresses).

## <a name="delete-a-prefix"></a>Löschen eines Präfix

In diesem Abschnitt erfahren Sie, wie Sie ein Präfix löschen können.

Verwenden Sie [az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete), um ein Präfix für öffentliche IP-Adressen zu löschen.

```azurecli-interactive
  az network public-ip prefix delete \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup
```

>[!NOTE]
>Wenn Adressen innerhalb des Präfix mit öffentlichen IP-Adressressourcen verknüpft sind, müssen Sie zuerst die öffentlichen IP-Adressressourcen löschen. Informationen dazu finden Sie unter [Löschen einer öffentlichen IP-Adresse](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Artikel haben Sie ein Präfix für öffentliche IP-Adressen und eine öffentliche IP-Adresse aus diesem Präfix erstellt. 


Nachdem Sie das Präfix für öffentliche IP-Adressen nach Wunsch bearbeitet haben, löschen Sie mit [az group delete](/cli/azure/group#az_group_delete) die Ressourcengruppe und alle darin enthaltenen Ressourcen.

```azurecli-interactive
  az group delete \
    --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen über Szenarien und die Vorteile der Verwendung finden Sie unter [Präfix für öffentliche IP-Adressen](public-ip-address-prefix.md).
