---
title: 'Schnellstart: Erstellen eines Präfixes für öffentliche IP-Adressen – Azure-Befehlszeilenschnittstelle'
titlesuffix: Azure Virtual Network
description: Erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle ein Präfix für eine öffentliche IP-Adresse erstellen.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 0b72eb4e70fb80602b352c32d6ffbde1cf4336da
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368921"
---
# <a name="quickstart-create-a-public-ip-address-prefix-using-the-azure-cli"></a>Schnellstart: Erstellen eines Präfixes für öffentliche IP-Adressen mithilfe der Azure-Befehlszeilenschnittstelle

Sie erhalten Informationen über Präfixe öffentlicher IP-Adressen und darüber, wie Sie diese erstellen, ändern und löschen können. Das Präfix einer öffentlichen IP-Adresse ist ein zusammenhängender Bereich öffentlicher IP-Adressen mit Standard-SKU. 

Wenn Sie eine öffentliche IP-Adressressource erstellen, können Sie eine statische öffentliche IP-Adresse aus dem Präfix zuweisen und diese Adresse VMs, Lastenausgleichsmodulen oder anderen Ressourcen zuordnen. Weitere Informationen finden Sie in der Übersicht [Präfix für öffentliche IP-Adressen](public-ip-address-prefix.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für dieses Tutorial ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe namens **QuickStartCreateIPPrefix-rg** am Standort **eastus2**.

```azurecli-interactive
  az group create \
    --name QuickStartCreateIPPrefix-rg \
    --location eastus2
```

## <a name="create-a-public-ip-address-prefix"></a>Erstellen des Präfix einer öffentlichen IP-Adresse

In diesem Abschnitt erstellen Sie mit Azure PowerShell ein zonenredundantes, zonenbasiertes und nicht zonenbasiertes öffentliches IP-Adressenpräfix. 

Die Präfixe in den Beispielen sind wie folgt:

* **IPv4**: /28 (16 Adressen)

* **IPv6**: /124 (16 Adressen)

Weitere Informationen zu verfügbaren Präfixgrößen finden Sie unter [Präfixgrößen](public-ip-address-prefix.md#prefix-sizes).

Erstellen Sie mit [az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create) ein Präfix für öffentliche IP-Adressen namens **myPublicIpPrefix** in der Region **eastus2**.

## <a name="ipv4"></a>IPv4

# <a name="zone-redundant-ipv4-prefix"></a>[**Zonenredundantes IPv4-Präfix**](#tab/ipv4-zone-redundant)

Um ein IPv4-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv4** in den Parameter **`--version`** ein. Um ein zonenredundantes IPv4-Präfix zu erstellen, geben Sie **1,2,3** in den Parameter **`--zone`** ein.

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4 \
    --zone 1 2 3
```

# <a name="zonal-ipv4-prefix"></a>[**Zonenbasiertes IPv4-Präfix**](#tab/ipv4-zonal)

Um ein IPv4-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv4** in den Parameter **`--version`** ein. Geben Sie im Parameter **`--zone`** den Wert **2** ein, um ein zonenbasiertes Präfix für IP-Adressen in Zone 2 zu erstellen.

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix-zonal \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4 \
    --zone 2
```

>[!NOTE]
>Die oben genannten Optionen für Zonen sind nur in Regionen mit [Verfügbarkeitszonen](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) gültig.

# <a name="non-zonal-ipv4-prefix"></a>[**Nicht zonenbasiertes IPv4-Präfix**](#tab/ipv4-non-zonal)

Um ein IPv4-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv4** in den Parameter **`--version`** ein. Entfernen Sie den Parameter **`--zone`** , um ein nicht zonenbasiertes IP-Präfix zu erstellen.

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix-nozone \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4
```

Das Entfernen des Parameters **`--zone`** im Befehl gilt für alle Regionen.  

Das Entfernen des Parameters **`--zone`** ist die Standardauswahl für öffentliche IP-Adressen der SKU „Standard“ in Regionen ohne [Verfügbarkeitszonen](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

---



## <a name="ipv6"></a>IPv6

# <a name="zone-redundant-ipv6-prefix"></a>[**Zonenredundantes IPv6-Präfix**](#tab/ipv6-zone-redundant)

Um ein IPv6-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv6** in den Parameter **`--version`** ein. Um ein zonenredundantes IPv6-Präfix zu erstellen, geben Sie **1,2,3** in den Parameter **`--zone`** ein.

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6 \
    --zone 1 2 3
```

# <a name="zonal-ipv6-prefix"></a>[**Zonenbasiertes IPv6-Präfix**](#tab/ipv6-zonal)

Um ein IPv6-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv6** in den Parameter **`--version`** ein. Geben Sie im Parameter **`--zone`** den Wert **2** ein, um ein zonenbasiertes Präfix für IP-Adressen in Zone 2 zu erstellen.

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix-zonal \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6 \
    --zone 2
```

>[!NOTE]
>Die oben genannten Optionen für Zonen sind nur in Regionen mit [Verfügbarkeitszonen](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) gültig.

# <a name="non-zonal-ipv6-prefix"></a>[**Nicht zonenbasiertes IPv6-Präfix**](#tab/ipv6-non-zonal)

Um ein IPv6-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv6** in den Parameter **`--version`** ein. Entfernen Sie den Parameter **`--zone`** , um ein nicht zonenbasiertes IP-Präfix zu erstellen.

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix-nozone \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6
```

Das Entfernen des Parameters **`--zone`** im Befehl gilt für alle Regionen.  

Das Entfernen des Parameters **`--zone`** ist die Standardauswahl für öffentliche IP-Adressen der SKU „Standard“ in Regionen ohne [Verfügbarkeitszonen](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

---

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Erstellen einer statischen öffentlichen IP-Adresse aus einem Präfix

Sobald Sie ein Präfix erstellt haben, müssen Sie statische IP-Adressen aus dem Präfix erstellen. In diesem Abschnitt erstellen Sie eine statische IP-Adresse aus dem Präfix, das Sie zuvor erstellt haben.

Erstellen Sie mit **az network public-ip create** eine öffentliche IP-Adresse im Präfix [myPublicIpPrefix](/cli/azure/network/public-ip#az_network_public_ip_create).

# <a name="ipv4-address"></a>[**IPv4-Adresse**](#tab/ipv4-address)

Um eine öffentliche IPv4-IP-Adressen zu erstellen, geben Sie **IPv4** in den Parameter **`--version`** ein.

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group QuickStartCreateIPPrefix-rg \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv4
```

# <a name="ipv6-address"></a>[**IPv6-Adresse**](#tab/ipv6-address)

Um ein IPv6-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv6** in den Parameter **`--version`** ein.

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group QuickStartCreateIPPrefix-rg \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv6
```

---

>[!NOTE]
>Nur statische öffentliche IP-Adressen, die mit der Standard-SKU erstellt wurden, können aus dem Bereich des Präfix zugewiesen werden. Weitere Informationen zu SKUs für öffentliche IP-Adressen finden Sie unter [Öffentliche IP-Adressen](public-ip-addresses.md#public-ip-addresses).

## <a name="delete-a-prefix"></a>Löschen eines Präfix

In diesem Abschnitt erfahren Sie, wie Sie ein Präfix löschen können.

Verwenden Sie [az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete), um ein Präfix für öffentliche IP-Adressen zu löschen.

```azurecli-interactive
  az network public-ip prefix delete \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg
```

>[!NOTE]
>Wenn Adressen innerhalb des Präfix mit öffentlichen IP-Adressressourcen verknüpft sind, müssen Sie zuerst die öffentlichen IP-Adressressourcen löschen. Informationen dazu finden Sie unter [Löschen einer öffentlichen IP-Adresse](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Artikel haben Sie ein Präfix für öffentliche IP-Adressen und eine öffentliche IP-Adresse aus diesem Präfix erstellt. 


Nachdem Sie das Präfix für öffentliche IP-Adressen nach Wunsch bearbeitet haben, löschen Sie mit [az group delete](/cli/azure/group#az_group_delete) die Ressourcengruppe und alle darin enthaltenen Ressourcen.

```azurecli-interactive
  az group delete \
    --name QuickStartCreateIPPrefix-rg
```

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie ein Präfix für öffentliche IP-Adressen mit Azure PowerShell erstellen:
> [!div class="nextstepaction"]
> [Erstellen einer öffentlichen IP-Adressen mithilfe der Azure-Befehlszeilenschnittstelle](create-public-ip-cli.md)
