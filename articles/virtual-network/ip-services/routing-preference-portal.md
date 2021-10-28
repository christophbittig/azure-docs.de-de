---
title: 'Konfigurieren der Routingpräferenz für eine öffentliche IP-Adresse: Azure-Portal'
description: Hier erfahren Sie, wie Sie eine öffentliche IP-Adresse mit der Routingpräferenz für Internetdatenverkehr erstellen.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: f45784af9d1642ce4339745d3102743cdbe327bc
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224838"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Konfigurieren der Routingpräferenz für eine öffentliche IP-Adresse mithilfe des Azure-Portals

In diesem Artikel erfahren Sie, wie Sie die [Routingpräferenz](routing-preference-overview.md) über das ISP-Netzwerk (Option **Internet**) für eine öffentliche IP-Adresse konfigurieren. Nach der Erstellung der öffentlichen IP-Adresse können Sie sie den folgenden Azure-Ressourcen für eingehenden und ausgehenden Internetdatenverkehr zuordnen:

* Virtueller Computer
* VM-Skalierungsgruppe
* Azure Kubernetes Service (AKS)
* Lastenausgleich mit Internetzugriff
* Application Gateway
* Azure Firewall

Die Routingpräferenz für die öffentliche IP-Adresse ist für alle Azure-Dienste standardmäßig auf das globale Netzwerk von Microsoft festgelegt und kann jedem Azure-Dienst zugeordnet werden.

Wenn Sie kein Azure-Abonnement besitzen, können Sie jetzt ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Erstellen einer öffentlichen IP-Adresse mit einer Routingpräferenz
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie **Ressource erstellen**.
3. Geben Sie in das Suchfeld *Öffentliche IP-Adresse* ein.
3. Wählen Sie in den Suchergebnissen **Öffentliche IP-Adresse** aus. Wählen Sie anschließend auf der Seite **Öffentliche IP-Adressen** die Option **Erstellen** aus.
1. Wählen Sie unter „SKU“ die Option **Standard** aus.
1. Wählen Sie unter **Routingpräferenz** die Option **Internet** aus.

      ![Erstellen einer öffentlichen IP-Adresse](./media/routing-preference-portal/public-ip-new.png)
1. Geben Sie im Abschnitt **IPv4-IP-Adresskonfiguration** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Subscription | Wählen Sie Ihr Abonnement aus.|
    | Resource group | Wählen Sie **Neu erstellen** aus, geben Sie *RoutingPreferenceResourceGroup* ein, und wählen Sie dann **OK** aus. |
    | Standort | Wählen Sie **USA, Osten** aus.|
    | Verfügbarkeitszone | Behalten Sie den Standardwert **Zonenredundant** bei. |
1. Wählen Sie **Erstellen** aus.

    > [!NOTE]
    > Öffentliche IP-Adressen werden mit einer IPv4- oder IPv6-Adresse erstellt. Die Routingpräferenz unterstützt derzeit jedoch nur IPv4.

Sie können die oben erstellte öffentliche IP-Adresse einem virtuellen [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Computer zuordnen. Lesen Sie den Abschnitt zur CLI auf der Tutorialseite [Zuordnen einer öffentlichen IP-Adresse zu einem virtuellen Computer](./associate-public-ip-address-vm.md#azure-cli), um die öffentliche IP-Adresse Ihrem virtuellen Computer zuzuordnen. Sie können die oben erstellte öffentliche IP-Adresse auch einer [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)-Instanz zuordnen, indem Sie sie der **Front-End**-Konfiguration des Lastenausgleichs zuweisen. Die öffentliche IP-Adresse fungiert als virtuelle IP-Adresse (VIP) mit Lastenausgleich.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [öffentliche IP-Adressen mit Routingpräferenz](routing-preference-overview.md).
- [Konfigurieren der Routingpräferenz für einen virtuellen Computer](./tutorial-routing-preference-virtual-machine-portal.md)
- [Konfigurieren der Routingpräferenz für eine öffentliche IP-Adresse mithilfe von PowerShell](routing-preference-powershell.md)
- Lesen Sie mehr über [öffentliche IP-Adressen](public-ip-addresses.md#public-ip-addresses) in Azure.
- Erfahren Sie mehr über alle [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).