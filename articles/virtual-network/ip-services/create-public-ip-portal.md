---
title: 'Schnellstart: Erstellen einer öffentlichen IP-Adresse – Azure-Portal'
titleSuffix: Azure Virtual Network
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie eine öffentliche IP-Adresse der Standard- oder Basic-SKU erstellen. Außerdem erfahren Sie mehr über die Routingpräferenz und -ebene.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.custom: template-quickstart
ms.openlocfilehash: ea18353a787918cbf0abe491b1a2437b9a7d3d4a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369660"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Schnellstart: Erstellen einer öffentlichen IP-Adresse im Azure-Portal

In dieser Schnellstartanleitung erfahren Sie, wie Sie eine öffentliche Azure-IP-Adresse erstellen. Öffentliche IP-Adressen in Azure werden für öffentliche Verbindungen mit Azure-Ressourcen verwendet. Öffentliche IP-Adressen sind in zwei SKUs verfügbar: „Basic“ und „Standard“. Zwei Ebenen öffentlicher IP-Adressen sind verfügbar: regional und global.  Die Routingpräferenz einer öffentlichen IP-Adresse wird bei der Erstellung festgelegt. Die verfügbaren Optionen sind Internetrouting und Microsoft-Netzwerkrouting.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

---

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-1-create-public-ip-standard)

>[!NOTE]
>Für Produktionsworkloads wird eine öffentliche IP-Adresse der SKU „Standard“ empfohlen.  Weitere Informationen zu SKUs finden Sie unter **[Öffentliche IP-Adressen](public-ip-addresses.md)** .

## <a name="create-a-standard-sku-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse der SKU „Standard“

Verwenden Sie die folgenden Schritte, um eine öffentliche IPv4-Adresse der SKU „Standard“ namens **myStandardPublicIP** zu erstellen. 

> [!NOTE]
>Wählen Sie **IPv6** für den Parameter **IP-Version** aus, um eine IPv6-Adresse zu erstellen. Wenn Ihre Bereitstellung eine Dual Stack-Konfiguration (IPv4- und IPv6-Adresse) erfordert, wählen Sie **Beide** aus.

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Öffentliche IP-Adresse** in das Suchfeld ein.

2. Wählen Sie in den Suchergebnissen **Öffentliche IP-Adressen** aus.

3. Wählen Sie **+ Erstellen** aus.

4. Geben Sie in **Öffentliche IP-Adresse erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung                 | Wert                       |
    | ---                     | ---                         |
    | IP-Version              | Wählen Sie „IPv4“ aus.              |    
    | SKU                     | Wählen Sie **Standard** aus.         |
    | Tarif                   | Wählen Sie **Regional** aus.     |
    | Name                    | Geben Sie **myStandardPublicIP** ein.          |
    | IP-Adresszuweisung   | Gesperrt als **Statisch**                |
    | Routingpräferenz     | Wählen Sie **Microsoft-Netzwerk** aus. |
    | Leerlaufzeitüberschreitung (Minuten)  | Übernehmen Sie den Standardwert **4**.        |
    | DNS-Namensbezeichnung          | Lassen Sie den Wert leer.    |
    | Subscription            | Wählen Sie Ihr Abonnement aus.   |
    | Resource group          | Wählen Sie **Neu erstellen** aus, und geben Sie **QuickStartCreateIP-rg** ein. </br> Klicken Sie auf **OK**. |
    | Standort                | Wählen Sie **(USA) USA, Osten 2** aus.     |
    | Verfügbarkeitszone       | Wählen Sie **Keine Zone** aus. |

5. Klicken Sie auf **Erstellen**.

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Screenshot: Erstellen einer Standard-IP-Adresse im Azure-Portal" border="false":::

> [!NOTE]
> In Regionen mit [Verfügbarkeitszonen](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) haben Sie die Möglichkeit, „Keine Zone“ (Standardoption), eine bestimmte Zone oder die Option „Zonenredundant“ auszuwählen. Die Auswahl hängt von Ihren spezifischen Domänenfehleranforderungen ab. In Regionen ohne Verfügbarkeitszonen wird dieses Feld nicht angezeigt. </br> Weitere Informationen zu Verfügbarkeitszonen finden Sie in der [Übersicht über Verfügbarkeitszonen](../../availability-zones/az-overview.md).

Sie können die oben erstellte öffentliche IP-Adresse einem virtuellen [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Computer zuordnen. Lesen Sie den Abschnitt zur CLI auf der Tutorialseite [Zuordnen einer öffentlichen IP-Adresse zu einem virtuellen Computer](../../virtual-network/associate-public-ip-address-vm.md#azure-cli), um die öffentliche IP-Adresse Ihrem virtuellen Computer zuzuordnen. Sie können die oben erstellte öffentliche IP-Adresse auch einer [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)-Instanz zuordnen, indem Sie sie der **Front-End**-Konfiguration des Lastenausgleichs zuweisen. Die öffentliche IP-Adresse fungiert als virtuelle IP-Adresse (VIP) mit Lastenausgleich.

# <a name="basic-sku"></a>[**Basic-SKU**](#tab/option-1-create-public-ip-basic)

>[!NOTE]
>Für Produktionsworkloads wird eine öffentliche IP-Adresse der SKU „Standard“ empfohlen.  Weitere Informationen zu SKUs finden Sie unter **[Öffentliche IP-Adressen](public-ip-addresses.md)** .

## <a name="create-a-basic-sku-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse der SKU „Basic“

Erstellen Sie in diesem Abschnitt eine öffentliche IPv4-Adresse der SKU „Basic“ namens **myBasicPublicIP**. 

> [!NOTE]
> Öffentliche IP-Adressen des Typs „Basic“ unterstützen keine Verfügbarkeitszonen.

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Öffentliche IP-Adresse** in das Suchfeld ein.

2. Wählen Sie in den Suchergebnissen **Öffentliche IP-Adressen** aus.

3. Wählen Sie **+ Erstellen** aus.

4. Geben Sie auf der Seite **Öffentliche IP-Adresse erstellen** die folgenden Informationen ein, oder wählen Sie sie aus: 

    | Einstellung                 | Wert                       |
    | ---                     | ---                         |
    | IP-Version              | Wählen Sie **IPv4** aus.                |    
    | SKU                     | Wählen Sie **Basic** aus.         |
    | Name                    | Geben Sie **myBasicPublicIP** ein.          |
    | IP-Adresszuweisung   | Wählen Sie **Statisch** aus.            |
    | Leerlaufzeitüberschreitung (Minuten)  | Übernehmen Sie den Standardwert **4**.       |
    | DNS-Namensbezeichnung          | Lassen Sie den Wert leer.    |
    | Subscription            | Wählen Sie Ihr Abonnement aus.   |
    | Resource group          | Wählen Sie **Neu erstellen** aus, und geben Sie **QuickStartCreateIP-rg** ein. </br> Klicken Sie auf **OK**. |
    | Standort                | Wählen Sie **(USA) USA, Osten 2** aus.      |

5. Klicken Sie auf **Erstellen**.

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Screenshot: Erstellen einer IP-Adresse der SKU „Basic“ im Azure-Portal" border="true":::

Wenn es akzeptabel ist, dass sich die IP-Adresse im Laufe der Zeit ändert, kann als IP-Zuweisung **Dynamic** ausgewählt werden, indem „AllocationMethod“ in **Dynamic** geändert wird. 

# <a name="routing-preference"></a>[**Routingpräferenz**](#tab/option-1-create-public-ip-routing-preference)

In diesem Abschnitt erfahren Sie, wie Sie die [Routingpräferenz](routing-preference-overview.md) über das ISP-Netzwerk (Option **Internet**) für eine öffentliche IP-Adresse konfigurieren. Nach dem Erstellen der öffentlichen IP-Adresse können Sie diese den folgenden Azure-Ressourcen zuordnen:

* Virtueller Computer
* VM-Skalierungsgruppe
* Azure Kubernetes Service (AKS)
* Lastenausgleich mit Internetzugriff
* Application Gateway
* Azure Firewall

Die Routingpräferenz für die öffentliche IP-Adresse ist für alle Azure-Dienste standardmäßig auf das globale Netzwerk von Microsoft festgelegt und kann jedem Azure-Dienst zugeordnet werden.

> [!NOTE]
>Wählen Sie **IPv6** für den Parameter **IP-Version** aus, um eine IPv6-Adresse zu erstellen. Wenn Ihre Bereitstellung eine Dual Stack-Konfiguration (IPv4- und IPv6-Adresse) erfordert, wählen Sie **Beide** aus.

## <a name="create-a-public-ip-with-internet-routing"></a>Erstellen einer öffentlichen IP-Adresse mit Internetrouting

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Öffentliche IP-Adresse** in das Suchfeld ein.

2. Wählen Sie in den Suchergebnissen **Öffentliche IP-Adressen** aus.

3. Wählen Sie **+ Erstellen** aus.

4. Geben Sie in **Öffentliche IP-Adresse erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung                 | Wert                       |
    | ---                     | ---                         |
    | IP-Version              | Wählen Sie „IPv4“ aus.              |    
    | SKU                     | Wählen Sie **Standard** aus.         |
    | Tarif                   | Wählen Sie **Regional** aus.     |
    | Name                    | Geben Sie **myStandardPublicIP-RP** ein.          |
    | IP-Adresszuweisung   | Gesperrt als **Statisch**                |
    | Routingpräferenz     | Wählen Sie **Internet** aus. |
    | Leerlaufzeitüberschreitung (Minuten)  | Übernehmen Sie den Standardwert **4**.        |
    | DNS-Namensbezeichnung          | Lassen Sie den Wert leer.    |
    | Subscription            | Wählen Sie Ihr Abonnement aus.   |
    | Resource group          | Wählen Sie **Neu erstellen** aus, und geben Sie **QuickStartCreateIP-rg** ein. </br> Klicken Sie auf **OK**. |
    | Standort                | Wählen Sie **(USA) USA, Osten 2** aus.     |
    | Verfügbarkeitszone       | Wählen Sie **Zonenredundant** aus. |

5. Wählen Sie **Erstellen** aus.

:::image type="content" source="./media/create-public-ip-portal/routing-preference.png" alt-text="Screenshot: Konfigurieren der Routingpräferenz im Azure-Portal" border="true":::

> [!NOTE]
> Öffentliche IP-Adressen werden mit einer IPv4- oder IPv6-Adresse erstellt. Die Routingpräferenz unterstützt derzeit jedoch nur IPv4.

> [!NOTE]
> In Regionen mit [Verfügbarkeitszonen](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) haben Sie die Möglichkeit, „Keine Zone“ (Standardoption), eine bestimmte Zone oder die Option „Zonenredundant“ auszuwählen. Die Auswahl hängt von Ihren spezifischen Domänenfehleranforderungen ab. In Regionen ohne Verfügbarkeitszonen wird dieses Feld nicht angezeigt. </br> Weitere Informationen zu Verfügbarkeitszonen finden Sie in der [Übersicht über Verfügbarkeitszonen](../../availability-zones/az-overview.md).

Sie können die oben erstellte öffentliche IP-Adresse einem virtuellen [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Computer zuordnen. Lesen Sie den Abschnitt zur CLI auf der Tutorialseite [Zuordnen einer öffentlichen IP-Adresse zu einem virtuellen Computer](../../virtual-network/associate-public-ip-address-vm.md#azure-cli), um die öffentliche IP-Adresse Ihrem virtuellen Computer zuzuordnen. Sie können die oben erstellte öffentliche IP-Adresse auch einer [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)-Instanz zuordnen, indem Sie sie der **Front-End**-Konfiguration des Lastenausgleichs zuweisen. Die öffentliche IP-Adresse fungiert als virtuelle IP-Adresse (VIP) mit Lastenausgleich.

# <a name="tier"></a>[**Tarif**](#tab/option-1-create-public-ip-tier)

Öffentliche IP-Adressen sind einer einzelnen Region zugeordnet. Bei der Ebene **Global** erstreckt sich eine IP-Adresse über mehrere Regionen. Die Ebene **Global** ist für die Front-Ends des regionsübergreifenden Lastenausgleichs erforderlich.  

Weitere Informationen finden Sie unter [Regionsübergreifender Lastenausgleich](../../load-balancer/cross-region-overview.md).

## <a name="create-a-global-tier-public-ip"></a>Erstellen einer öffentlichen IP-Adresse der globalen Ebene

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Öffentliche IP-Adresse** in das Suchfeld ein.

2. Wählen Sie in den Suchergebnissen **Öffentliche IP-Adressen** aus.

3. Wählen Sie **+ Erstellen** aus.

4. Geben Sie in **Öffentliche IP-Adresse erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung                 | Wert                       |
    | ---                     | ---                         |
    | IP-Version              | Wählen Sie „IPv4“ aus.              |    
    | SKU                     | Wählen Sie **Standard** aus.         |
    | Tarif                   | Wählen Sie **Global** aus.     |
    | Name                    | Geben Sie **myStandardPublicIP-Global** ein.          |
    | IP-Adresszuweisung   | Gesperrt als **Statisch**                |
    | Routingpräferenz     | Wählen Sie **Microsoft** aus. |
    | Leerlaufzeitüberschreitung (Minuten)  | Übernehmen Sie den Standardwert **4**.        |
    | DNS-Namensbezeichnung          | Lassen Sie den Wert leer.    |
    | Subscription            | Wählen Sie Ihr Abonnement aus.   |
    | Resource group          | Wählen Sie **Neu erstellen** aus, und geben Sie **QuickStartCreateIP-rg** ein. </br> Klicken Sie auf **OK**. |
    | Standort                | Wählen Sie **(USA) USA, Osten 2** aus.     |
    | Verfügbarkeitszone       | Wählen Sie **Zonenredundant** aus. |

5. Wählen Sie **Erstellen** aus.

:::image type="content" source="./media/create-public-ip-portal/tier.png" alt-text="Screenshot: Konfigurieren der Ebene im Azure-Portal" border="true":::

Sie können die oben erstellte IP-Adresse einem regionsübergreifenden Lastenausgleich zuordnen. Weitere Informationen finden Sie unter [Tutorial: Erstellen eines regionsübergreifenden Lastenausgleichs mithilfe des Azure-Portals](../../load-balancer/tutorial-cross-region-portal.md).

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht mehr benötigen, löschen Sie die öffentliche IP-Adresse wie folgt:

1. Geben Sie oben im Portal im Suchfeld **Ressourcengruppe** ein.

2. Wählen Sie in den Suchergebnissen **Ressourcengruppen** aus.

3. Wählen Sie **QuickStartCreateIP-rg** aus.

4. Wählen Sie die Option **Ressourcengruppe löschen**.

5. Geben Sie **myResourceGroup** für **RESSOURCENGRUPPENNAMEN EINGEBEN** ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie ein Präfix für öffentliche IP-Adressen erstellen:
> [!div class="nextstepaction"]
> [Schnellstart: Erstellen eines Präfixes für eine öffentliche IP-Adresse mithilfe des Azure-Portals](create-public-ip-prefix-portal.md)
