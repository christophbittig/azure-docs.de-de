---
title: 'Schnellstart: Erstellen einer öffentlichen IP-Adresse mithilfe einer Resource Manager-Vorlage'
titleSuffix: Azure Virtual Network
description: Hier erfahren Sie, wie Sie mit einer Resource Manager-Vorlage eine öffentliche IP-Adresse erstellen.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: cb90b716351802271a12fc72c1ead9e4fac2968e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456421"
---
# <a name="quickstart-create-a-public-ip-address-using-a-resource-manager-template"></a>Schnellstart: Erstellen einer öffentlichen IP-Adresse mithilfe einer Resource Manager-Vorlage

In diesem Artikel erfahren Sie, wie Sie innerhalb einer Resource Manager-Vorlage eine öffentliche IP-Adressressource erstellen.

Weitere Informationen zu den Ressourcen, denen diese öffentliche IP-Adresse zugeordnet werden kann, sowie zu den Unterschieden zwischen SKUs des Typs „Basic“ und „Standard“ finden Sie unter [Öffentliche IP-Adressen](public-ip-addresses.md). 

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
* Eine Ressourcengruppe in Ihrem Azure-Abonnement.
* Eine Azure Resource Manager-Vorlage für die Abschnitte der öffentlichen IP-Adresse.

## <a name="create-standard-sku-public-ip-with-zones"></a>Erstellen einer öffentlichen IP-Adresse der Standard-SKU mit Zonen

In diesem Abschnitt erstellen Sie eine öffentliche IP-Adresse mit Zonen. Öffentliche IP-Adressen können zonenredundant oder zonengebunden sein.

### <a name="zone-redundant"></a>Zonenredundant

Mit dem Code in diesem Abschnitt wird eine zonenredundante öffentliche IPv4-Adresse der SKU „Standard“ mit dem Namen **myStandardPublicIP** erstellt.

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **`publicIPAddressVersion`** in **IPv6**.

Hinzuzufügender Vorlagenabschnitt:

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myStandardPublicIP",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Standard"
  },
  "zones": [
    "1",
    "2",
    "3"
  ],
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4"
  }
```
> [!IMPORTANT]
> Für frühere API-Versionen als 2020-08-01 führen Sie den oben genannten Code aus, ohne einen Zonenparameter für eine Standard-SKU anzugeben. So erstellen Sie eine zonenredundante IP-Adresse. 
>

>[!NOTE]
>Die oben genannten Optionen für Zonen sind nur in Regionen mit [Verfügbarkeitszonen](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) gültig.

### <a name="zonal"></a>Zonal

Mit dem Code in diesem Abschnitt wird eine zonale öffentliche IPv4-Adresse der SKU „Standard“ mit dem Namen **myStandardPublicIP-zonal** erstellt. 

Zum Erstellen einer zonengebundenen öffentlichen IP-Adresse der SKU „Standard“ in Zone 2 enthält die Eigenschaft **zones** die Ziffer „2“.

Hinzuzufügender Vorlagenabschnitt:

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myStandardPublicIP-zonal",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Standard"
  },
  "zones": [
    "2"
  ],
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4"
  }
```

>[!NOTE]
>Die oben genannten Optionen für Zonen sind nur in Regionen mit [Verfügbarkeitszonen](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) gültig.

## <a name="create-standard-public-ip-without-zones"></a>Erstellen einer öffentlichen IP-Adresse der SKU „Standard“ ohne Zonen

In diesem Abschnitt erstellen Sie eine nicht zonengebundene IP-Adresse. 

Mit dem Code in diesem Abschnitt wird eine nicht zonengebundene öffentliche IPv4-Adresse mit dem Namen **myStandardPublicIP** erstellt. Der Codeabschnitt gilt für alle Regionen mit oder ohne [Verfügbarkeitszonen](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **publicIPAddressVersion** in **IPv6**.

Hinzuzufügender Vorlagenabschnitt:

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myStandardPublicIP-nozone",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Standard"
  },
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4"
  }
```
> [!IMPORTANT]
> Bei früheren API-Versionen als 2020-08-01 führt das Auslassen eines Zonenparameters für eine Standard-SKU zur Erstellung einer zonenredundanten IP-Adresse. 
>


## <a name="create-a-basic-public-ip"></a>Erstellen einer öffentlichen IP-Adresse der SKU „Basic“

In diesem Abschnitt erstellen Sie eine IP-Adresse der SKU „Basic“. Öffentliche IP-Adressen des Typs „Basic“ unterstützen keine Verfügbarkeitszonen. 

Der Code in diesem Abschnitt erstellt eine öffentliche IPv4-Adresse der SKU „Basic“ mit dem Namen **myBasicPublicIP**.

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **publicIPAddressVersion** in **IPv6**. 

Hinzuzufügender Vorlagenabschnitt:

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myBasicPublicIP",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Basic"
  },
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4"
  }
```

Wenn es akzeptabel ist, dass sich die IP-Adresse im Laufe der Zeit ändert, kann als IP-Zuweisung **publicIPAllocationMethod** ausgewählt werden, indem „AllocationMethod“ in **Dynamic** geändert wird. 

>[!NOTE]
> Eine IPv6-Adresse der SKU „Basic“ muss immer dynamisch sein.

## <a name="routing-preference-and-tier"></a>Routingpräferenz und -ebene

Statische öffentliche IPv4-Adressen der Standard-SKU unterstützen die Routingpräferenz oder das Feature „Globale Ebene“.

### <a name="routing-preference"></a>Routingpräferenz

Standardmäßig ist die Routingpräferenz für öffentliche IP-Adressen auf „Microsoft-Netzwerk“ festgelegt. Damit wird der Datenverkehr über das globale Microsoft-WAN an den Benutzer übertragen.  

Durch Auswahl von **Internet** wird der Datenverkehr im Microsoft-Netzwerk minimiert, anstatt den Datenverkehr über das Transit-ISP-Netzwerk zu optimierten Kosten zu übertragen.  

Weitere Informationen zu den Routingpräferenzen finden Sie unter [Was ist Routingpräferenz (Vorschau)?](routing-preference-overview.md)

Um die Internetroutingpräferenz für eine zonenredundante öffentliche IPv4-Adresse der SKU „Standard“ zu verwenden, sollte der Vorlagenabschnitt in etwa folgendermaßen aussehen:

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myStandardZRPublicIP-RP",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Standard"
  },
  "zones": [
    "1",
    "2",
    "3"
  ],
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4",
    "ipTags": [
      {
        "ipTagType": "RoutingPreference",
        "tag": "Internet"
      }
    ]
  }
}
```

### <a name="tier"></a>Tarif

Öffentliche IP-Adressen sind einer einzelnen Region zugeordnet. Bei der Ebene **Global** erstreckt sich eine IP-Adresse über mehrere Regionen. Die Ebene **Global** ist für die Front-Ends des regionsübergreifenden Lastenausgleichs erforderlich.  

Weitere Informationen finden Sie unter [Regionsübergreifender Lastenausgleich](../../load-balancer/cross-region-overview.md).

Um eine globale öffentliche IPv4-Adresse der SKU „Standard“ zu verwenden, sollte der Vorlagenabschnitt in etwa folgendermaßen aussehen:

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myStandardPublicIP-Global",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Standard",
    "tier": "Global"
  },
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4"
  }
```

## <a name="additional-information"></a>Zusätzliche Informationen 

Weitere Informationen zu den in diesem Artikel aufgeführten Eigenschaften öffentlicher IP-Adressen finden Sie unter [Verwalten öffentlicher IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Nächste Schritte
- Zuordnen einer [öffentlichen IP-Adresse zu einem virtuellen Computer](./associate-public-ip-address-vm.md#azure-portal)
- Lesen Sie mehr über [öffentliche IP-Adressen](public-ip-addresses.md#public-ip-addresses) in Azure.
- Erfahren Sie mehr über alle [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).
