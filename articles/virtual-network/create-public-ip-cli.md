---
title: 'Erstellen einer öffentlichen IP-Adresse: Azure CLI'
titleSuffix: Azure Virtual Network
description: Hier erfahren Sie, wie Sie mithilfe der Azure CLI eine öffentliche IP-Adresse erstellen.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 05/05/2021
ms.author: allensu
ms.openlocfilehash: 8b68597abaf4a715dc55a92f2445000c1aaed0d0
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2021
ms.locfileid: "113435126"
---
# <a name="create-a-public-ip-address-using-azure-cli"></a>Erstellen einer öffentlichen IP-Adresse mithilfe der Azure CLI

In diesem Artikel wird gezeigt, wie Sie mithilfe der Azure CLI eine öffentliche IP-Adressressource erstellen. 

Weitere Informationen zu Ressourcen, die öffentliche IP-Adressen unterstützen, finden Sie unter [Öffentliche IP-Adressen](./public-ip-addresses.md).

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

## <a name="create-standard-sku-public-ip-with-zones"></a>Erstellen einer öffentlichen IP-Adresse der SKU „Standard“ mit Zonen

In diesem Abschnitt erstellen Sie eine öffentliche IP-Adresse mit Zonen. Öffentliche IP-Adressen können zonenredundant oder zonengebunden sein.

### <a name="zone-redundant"></a>Zonenredundant

>[!NOTE]
>Der folgende Befehl funktioniert für die API-Version 2020-08-01 oder höher.  Weitere Informationen zur derzeit verwendeten API-Version finden Sie unter [Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md).

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create), um in **myResourceGroup** eine zonenredundante öffentliche IPv4-Adresse der SKU „Standard“ namens **myStandardZRPublicIP** zu erstellen.  

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **version** in **IPv6**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 1 2 3
```
> [!IMPORTANT]
> Führen Sie für Versionen der API, die älter als 2020-08-01 sind, den Befehl aus, ohne einen Zonenparameter anzugeben, um eine zonenredundante IP-Adresse zu erstellen. 
>

### <a name="zonal"></a>Zonal

Verwenden Sie den folgenden Befehl, um eine zonenbezogene öffentliche IPv4-Adresse der SKU „Standard“ namens **myStandardZonalPublicIP** in Zone 2 in **myResourceGroup** zu erstellen.

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **version** in **IPv6**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 2
```

>[!NOTE]
>Die oben genannten Optionen für Zonen sind nur in Regionen mit [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) gültig.


## <a name="create-standard-public-ip-without-zones"></a>Erstellen einer öffentlichen IP-Adresse der SKU „Standard“ ohne Zonen

In diesem Abschnitt erstellen Sie eine nicht zonenbezogene IP-Adresse.  

>[!NOTE]
>Der folgende Befehl funktioniert für die API-Version 2020-08-01 oder höher.  Weitere Informationen zur derzeit verwendeten API-Version finden Sie unter [Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md).

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create), um eine öffentliche IPv4-Adresse der SKU „Standard“ als nicht zonenbezogene Ressource namens **myStandardPublicIP** in **myResourceGroup** zu erstellen. 

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **version** in **IPv6**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --version IPv4 \
    --sku Standard
```
Das Entfernen des Parameters **zone** im Befehl gilt für alle Regionen.  

Das Entfernen des Parameters **zone** ist die Standardauswahl für öffentliche IP-Adressen der SKU „Standard“ in Regionen ohne [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

## <a name="create-a-basic-public-ip"></a>Erstellen einer öffentlichen IP-Adresse der SKU „Basic“

In diesem Abschnitt erstellen Sie eine IP-Adresse der SKU „Basic“. Öffentliche IP-Adressen des Typs „Basic“ unterstützen keine Verfügbarkeitszonen.

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create), um in **myResourceGroupLB** eine statische öffentliche IPv4-Adresse der SKU „Basic“ namens **myBasicPublicIP** zu erstellen.

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **version** in **IPv6**. 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --version IPv4 \
    --sku Basic \
    --allocation-method Static
```
Wenn es akzeptabel ist, dass sich die IP-Adresse im Laufe der Zeit ändert, kann als IP-Zuweisung **Dynamic** ausgewählt werden, indem „AllocationMethod“ in **Dynamic** geändert wird. 

>[!NOTE]
> Eine IPv6-Adresse der SKU „Basic“ muss immer dynamisch sein.

## <a name="routing-preference-and-tier"></a>Routingpräferenz und -ebene

Statische öffentliche IPv4-Adressen der SKU „Standard“ unterstützen die Routingpräferenz bzw. eine globale Ebene.

### <a name="routing-preference"></a>Routingpräferenz

Standardmäßig ist die Routingpräferenz für öffentliche IP-Adressen auf „Microsoft-Netzwerk“ festgelegt. Damit wird der Datenverkehr über das globale Microsoft-WAN an den Benutzer übertragen.  

Durch Auswahl von **Internet** wird der Datenverkehr im Microsoft-Netzwerk minimiert, anstatt den Datenverkehr über das Transit-ISP-Netzwerk zu optimierten Kosten zu übertragen.  

Weitere Informationen zu den Routingpräferenzen finden Sie unter [Was ist Routingpräferenz (Vorschau)?](./routing-preference-overview.md)

Der Befehl erstellt eine neue zonenredundante öffentliche IPv4-Adresse der SKU „Standard“ mit einer Routingpräferenz vom Typ **Internet**:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP-RP \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```

### <a name="tier"></a>Tarif

Öffentliche IP-Adressen sind einer einzelnen Region zugeordnet. Bei der Ebene **Global** erstreckt sich eine IP-Adresse über mehrere Regionen. Die Ebene **Global** ist für die Front-Ends des regionsübergreifenden Lastenausgleichs erforderlich.  

Weitere Informationen finden Sie unter [Regionsübergreifender Lastenausgleich](../load-balancer/cross-region-overview.md).

Mit dem folgenden Befehl wird eine globale IPv4-Adresse erstellt. Diese Adresse kann dem Front-End eines regionsübergreifenden Lastenausgleichs zugeordnet werden.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP-Global \
    --version IPv4 \
    --tier global \
    --sku Standard \
```
>[!NOTE]
>Adressen der globalen Ebene unterstützen keine Verfügbarkeitszonen.

## <a name="additional-information"></a>Zusätzliche Informationen 

Weitere Informationen zu den einzelnen Parametern, die in dieser Anleitung aufgeführt sind, finden Sie unter [Verwalten öffentlicher IP-Adressen](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Nächste Schritte
- Zuordnen einer [öffentlichen IP-Adresse zu einem virtuellen Computer](./associate-public-ip-address-vm.md#azure-portal)
- Lesen Sie mehr über [öffentliche IP-Adressen](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Erfahren Sie mehr über alle [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).
