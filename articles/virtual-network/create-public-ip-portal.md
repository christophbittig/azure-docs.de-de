---
title: 'Erstellen einer öffentlichen IP-Adresse: Azure-Portal'
titleSuffix: Azure Virtual Network
description: Hier erfahren Sie, wie Sie eine öffentliche IP-Adresse im Azure-Portal erstellen.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/04/2021
ms.author: allensu
ms.openlocfilehash: 6cf9e86222d98835ea5355440343df96794dbfd4
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2021
ms.locfileid: "113435054"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>Erstellen einer öffentlichen IP-Adresse im Azure-Portal

In diesem Artikel wird gezeigt, wie Sie mithilfe des Azure-Portals eine öffentliche IP-Adressressource erstellen. 

Weitere Informationen zu den Ressourcen, denen diese öffentliche IP-Adresse zugeordnet werden kann, sowie zu den Unterschieden zwischen SKUs des Typs „Basic“ und „Standard“ finden Sie unter [Öffentliche IP-Adressen](./public-ip-addresses.md). 

## <a name="create-a-standard-sku-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse der SKU „Standard“

Verwenden Sie die folgenden Schritte, um eine öffentliche IPv4-Adresse der SKU „Standard“ namens **myStandardPublicIP** zu erstellen.  

> [!NOTE]
>Wählen Sie **IPv6** für den Parameter **IP-Version** aus, um eine IPv6-Adresse zu erstellen. Wenn Ihre Bereitstellung eine Dual Stack-Konfiguration (IPv4- und IPv6-Adresse) erfordert, wählen Sie **Beide** aus.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **Öffentliche IP-Adresse** in das Suchfeld ein.

3. Wählen Sie in den Suchergebnissen **Öffentliche IP-Adressen** aus.

4. Wählen Sie **+ Erstellen** aus.

5. Geben Sie in **Öffentliche IP-Adresse erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung                 | Wert                       |
    | ---                     | ---                         |
    | IP-Version              | Wählen Sie „IPv4“ aus.              |    
    | SKU                     | Wählen Sie **Standard** aus.         |
    | Tarif                   | Wählen Sie **Regional** aus.    </br> Weitere Informationen finden Sie unter [Routing Preference and Tier](#routing-preference-and-tier) (Routingpräferenz und -ebene).     |
    | Name                    | Geben Sie **myStandardPublicIP** ein.          |
    | IP-Adresszuweisung   | Gesperrt als **Statisch**                |
    | Routingpräferenz     | Wählen Sie **Microsoft-Netzwerk** aus. </br> Weitere Informationen finden Sie unter [Routing Preference and Tier](#routing-preference-and-tier) (Routingpräferenz und -ebene). |
    | Leerlaufzeitüberschreitung (Minuten)  | Übernehmen Sie den Standardwert **4**.        |
    | DNS-Namensbezeichnung          | Lassen Sie den Wert leer.    |
    | Subscription            | Wählen Sie Ihr Abonnement aus.   |
    | Ressourcengruppe          | Wählen Sie **Neue erstellen** aus, und geben Sie **myResourceGroup** ein. </br> Klicken Sie auf **OK**. |
    | Standort                | Wählen Sie **(USA) USA, Osten 2** aus.     |
    | Verfügbarkeitszone       | Wählen Sie **Keine Zone** aus. |

6. Klicken Sie auf **Erstellen**.

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Erstellen einer Standard-IP-Adresse im Azure-Portal" border="false":::

> [!NOTE]
> In Regionen mit [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) haben Sie die Möglichkeit, „Keine Zone“ (Standardoption), eine bestimmte Zone oder die Option „Zonenredundant“ auszuwählen. Die Auswahl hängt von Ihren spezifischen Domänenfehleranforderungen ab. In Regionen ohne Verfügbarkeitszonen wird dieses Feld nicht angezeigt. </br> Weitere Informationen zu Verfügbarkeitszonen finden Sie in der [Übersicht über Verfügbarkeitszonen](../availability-zones/az-overview.md).

> [!NOTE]
> IPv6 wird derzeit nicht mit Routingpräferenz oder regionsübergreifendem Lastenausgleich (globale Ebene) unterstützt.

## <a name="create-a-basic-sku-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse der SKU „Basic“

Erstellen Sie in diesem Abschnitt eine öffentliche IPv4-Adresse der SKU „Basic“ namens **myBasicPublicIP**. 

> [!NOTE]
> Öffentliche IP-Adressen des Typs „Basic“ unterstützen keine Verfügbarkeitszonen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie **Ressource erstellen**. 
3. Geben Sie im Suchfeld **Öffentliche IP-Adresse** ein. Wählen Sie in den Suchergebnissen **Öffentliche IP-Adresse** aus.
4. Wählen Sie auf der Seite **Öffentliche IP-Adresse** die Option **Erstellen** aus.
5. Geben Sie auf der Seite **Öffentliche IP-Adresse erstellen** die folgenden Informationen ein, oder wählen Sie sie aus: 

    | Einstellung                 | Wert                       |
    | ---                     | ---                         |
    | IP-Version              | Wählen Sie „IPv4“ aus.                 |    
    | SKU                     | Wählen Sie **Basic** aus.         |
    | Name                    | Geben Sie **myBasicPublicIP** ein.          |
    | IP-Adresszuweisung   | Wählen Sie **Statisch** aus.            |
    | Leerlaufzeitüberschreitung (Minuten)  | Übernehmen Sie den Standardwert **4**.       |
    | DNS-Namensbezeichnung          | Lassen Sie den Wert leer.    |
    | Subscription            | Wählen Sie Ihr Abonnement aus.   |
    | Ressourcengruppe          | Wählen Sie **Neue erstellen** aus, und geben Sie **myResourceGroup** ein. </br> Klicken Sie auf **OK**. |
    | Standort                | Wählen Sie **(USA) USA, Osten 2** aus.      |

6. Klicken Sie auf **Erstellen**.

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Erstellen einer IP-Adresse der SKU „Basic“ im Azure-Portal" border="false":::

Wenn es akzeptabel ist, dass sich die IP-Adresse im Laufe der Zeit ändert, kann als IP-Zuweisung **Dynamic** ausgewählt werden, indem „AllocationMethod“ in **Dynamic** geändert wird. 

---

## <a name="routing-preference-and-tier"></a>Routingpräferenz und -ebene

Statische öffentliche IPv4-Adressen der SKU „Standard“ unterstützen die Routingpräferenz oder das Feature „Globale Ebene“. Wählen Sie die Routingpräferenz und die Ebene aus, wenn Sie eine neue öffentliche IP-Adresse erstellen.

### <a name="routing-preference"></a>Routingpräferenz

Standardmäßig ist die Routingpräferenz für öffentliche IP-Adressen auf „Microsoft-Netzwerk“ festgelegt. Damit wird der Datenverkehr über das globale Microsoft-WAN an den Benutzer übertragen.  

Durch Auswahl von **Internet** wird der Datenverkehr im Microsoft-Netzwerk minimiert, anstatt den Datenverkehr über das Transit-ISP-Netzwerk zu optimierten Kosten zu übertragen.  

Weitere Informationen zu den Routingpräferenzen finden Sie unter [Was ist Routingpräferenz (Vorschau)?](./routing-preference-overview.md)

:::image type="content" source="./media/create-public-ip-portal/routing-preference.png" alt-text="Konfigurieren der Routingpräferenz im Azure-Portal" border="false":::

### <a name="tier"></a>Tarif

Öffentliche IP-Adressen sind einer einzelnen Region zugeordnet. Bei der Ebene **Global** erstreckt sich eine IP-Adresse über mehrere Regionen. Die Ebene **Global** ist für die Front-Ends des regionsübergreifenden Lastenausgleichs erforderlich.  

Weitere Informationen finden Sie unter [Regionsübergreifender Lastenausgleich](../load-balancer/cross-region-overview.md).

:::image type="content" source="./media/create-public-ip-portal/tier.png" alt-text="Konfigurieren der Ebene im Azure-Portal" border="false":::

## <a name="additional-information"></a>Zusätzliche Informationen 

Weitere Informationen zu den oben aufgeführten einzelnen Feldern finden Sie unter [Verwalten öffentlicher IP-Adressen](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Nächste Schritte
- Zuordnen einer [öffentlichen IP-Adresse zu einem virtuellen Computer](./associate-public-ip-address-vm.md#azure-portal)
- Lesen Sie mehr über [öffentliche IP-Adressen](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Erfahren Sie mehr über alle [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).