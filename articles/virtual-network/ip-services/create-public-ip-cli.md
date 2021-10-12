---
title: 'Schnellstart: Erstellen einer öffentlichen IP-Adresse – Azure CLI'
titleSuffix: Azure Virtual Network
description: Hier erfahren Sie, wie Sie eine öffentliche IP-Adresse mithilfe der Azure CLI erstellen.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 8d8b1525c910592cb9cc35ae1e08da7e9e24e30b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369403"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-cli"></a>Schnellstart: Erstellen einer öffentlichen IP-Adresse mithilfe der Azure CLI

In dieser Schnellstartanleitung erfahren Sie, wie Sie eine öffentliche Azure-IP-Adresse erstellen. Öffentliche IP-Adressen in Azure werden für öffentliche Verbindungen mit Azure-Ressourcen verwendet. Öffentliche IP-Adressen sind in zwei SKUs verfügbar: „Basic“ und „Standard“. Zwei Ebenen öffentlicher IP-Adressen sind verfügbar: regional und global. Die Routingpräferenz einer öffentlichen IP-Adresse wird bei der Erstellung festgelegt. Die verfügbaren Optionen sind Internetrouting und Microsoft-Netzwerkrouting.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für dieses Tutorial ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe namens **QuickStartCreateIP-rg** am Standort **eastus2**.

```azurecli-interactive
  az group create \
    --name QuickStartCreateIP-rg \
    --location eastus2
```

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/create-public-ip-standard)

>[!NOTE]
>Für Produktionsworkloads wird eine öffentliche IP-Adresse der SKU „Standard“ empfohlen.  Weitere Informationen zu SKUs finden Sie unter **[Öffentliche IP-Adressen](public-ip-addresses.md)** .
>
>Der folgende Befehl funktioniert für die API-Version **2020-08-01** oder **höher**.  Weitere Informationen zur derzeit verwendeten API-Version finden Sie unter [Ressourcenanbieter und -typen](../../azure-resource-manager/management/resource-providers-and-types.md).

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create), um in **QuickStartCreateIP-rg** eine zonenredundante öffentliche IPv4-Adresse der SKU „Standard“ namens **myStandardPublicIP** zu erstellen.  

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **`--version`** in **IPv6**.

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 1 2 3
```
> [!IMPORTANT]
> Führen Sie für Versionen der API, die älter als 2020-08-01 sind, den Befehl ohne Angabe des Parameters **`--zone`** aus, um eine zonenredundante IP-Adresse zu erstellen. 
>

# <a name="basic-sku"></a>[**Basic-SKU**](#tab/create-public-ip-basic)

In diesem Abschnitt erstellen Sie eine IP-Adresse der SKU „Basic“. Öffentliche IP-Adressen des Typs „Basic“ unterstützen keine Verfügbarkeitszonen.

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create), um in **QuickStartCreateIP-rg** eine statische öffentliche IPv4-Adresse der SKU „Basic“ namens **myBasicPublicIP** zu erstellen.

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **`--version`** in **IPv6**. 

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myBasicPublicIP \
    --version IPv4 \
    --sku Basic \
    --allocation-method Static
```
Wenn es akzeptabel ist, dass sich die IP-Adresse im Laufe der Zeit ändert, kann als IP-Zuweisung **Dynamic** ausgewählt werden, indem **`--allocation-method`** in **Dynamic** geändert wird. 

>[!NOTE]
> Eine IPv6-Adresse der SKU „Basic“ muss immer dynamisch sein.

---

## <a name="create-a-zonal-or-no-zone-ip-address"></a>Erstellen einer zonalen oder zonenfreien IP-Adresse

In diesem Abschnitt erfahren Sie, wie Sie eine zonale oder zonenfreie öffentliche IP-Adresse erstellen.

# <a name="zonal"></a>[**Zonal**](#tab/create-public-ip-zonal)

Verwenden Sie den folgenden Befehl, um in **QuickStartCreateIP-rg** eine zonale öffentliche IPv4-Adresse der SKU „Standard“ namens **myStandardPublicIP** in Zone 2 zu erstellen.

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **`--version`** in **IPv6**.

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rgLB \
    --name myStandardPublicIP-zonal \
    --version IPv4 \
    --sku Standard \
    --zone 2
```

>[!NOTE]
>Die oben genannten Optionen für Zonen sind nur in Regionen mit [Verfügbarkeitszonen](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) gültig.

# <a name="non-zonal"></a>[**Nicht zonal**](#tab/create-public-ip-non-zonal)

In diesem Abschnitt erstellen Sie eine nicht zonenbezogene IP-Adresse.  

>[!NOTE]
>Der folgende Befehl funktioniert für die API-Version 2020-08-01 oder höher.  Weitere Informationen zur derzeit verwendeten API-Version finden Sie unter [Ressourcenanbieter und -typen](../../azure-resource-manager/management/resource-providers-and-types.md).

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create), um in **QuickStartCreateIP-rg** eine öffentliche IPv4-Adresse der SKU „Standard“ als nicht zonale Ressource namens **myStandardPublicIP** zu erstellen. 

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **`--version`** in **IPv6**.

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-nozone \
    --version IPv4 \
    --sku Standard
```
Das Entfernen des Parameters **`--zone`** im Befehl gilt für alle Regionen.  

Das Entfernen des Parameters **`--zone`** ist die Standardauswahl für öffentliche IP-Adressen der SKU „Standard“ in Regionen ohne [Verfügbarkeitszonen](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

---

## <a name="routing-preference-and-tier"></a>Routingpräferenz und -ebene

Statische öffentliche IPv4-Adressen der SKU „Standard“ unterstützen die Routingpräferenz bzw. eine globale Ebene.

# <a name="routing-preference"></a>[**Routingpräferenz**](#tab/routing-preference)

Standardmäßig ist die Routingpräferenz für öffentliche IP-Adressen auf „Microsoft-Netzwerk“ festgelegt. Damit wird der Datenverkehr über das globale Microsoft-WAN an den Benutzer übertragen.  

Durch Auswahl von **Internet** wird der Datenverkehr im Microsoft-Netzwerk minimiert, anstatt den Datenverkehr über das Transit-ISP-Netzwerk zu optimierten Kosten zu übertragen.  

Weitere Informationen zu den Routingpräferenzen finden Sie unter [Was ist Routingpräferenz (Vorschau)?](routing-preference-overview.md)

Der Befehl erstellt eine neue zonenredundante öffentliche IPv4-Adresse der SKU „Standard“ mit einer Routingpräferenz vom Typ **Internet**:

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-RP \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```

# <a name="tier"></a>[**Tarif**](#tab/tier)

Öffentliche IP-Adressen sind einer einzelnen Region zugeordnet. Bei der Ebene **Global** erstreckt sich eine IP-Adresse über mehrere Regionen. Die Ebene **Global** ist für die Front-Ends des regionsübergreifenden Lastenausgleichs erforderlich.  

Weitere Informationen finden Sie unter [Regionsübergreifender Lastenausgleich](../../load-balancer/cross-region-overview.md).

Mit dem folgenden Befehl wird eine globale IPv4-Adresse erstellt. Diese Adresse kann dem Front-End eines regionsübergreifenden Lastenausgleichs zugeordnet werden.

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-Global \
    --version IPv4 \
    --tier global \
    --sku Standard \
```
>[!NOTE]
>Adressen der globalen Ebene unterstützen keine Verfügbarkeitszonen.

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht mehr benötigen, löschen Sie die öffentliche IP-Adresse wie folgt:

1. Geben Sie oben im Portal im Suchfeld **Ressourcengruppe** ein.

2. Wählen Sie in den Suchergebnissen **Ressourcengruppen** aus.

3. Wählen Sie **QuickStartCreateIP-rg** aus.

4. Wählen Sie die Option **Ressourcengruppe löschen**.

5. Geben Sie unter **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN** den Namen **QuickStartCreateIP-rg** ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie ein Präfix für öffentliche IP-Adressen erstellen:
> [!div class="nextstepaction"]
> [Schnellstart: Erstellen eines Präfixes für öffentliche IP-Adressen mithilfe der Azure-Befehlszeilenschnittstelle](create-public-ip-prefix-cli.md)
