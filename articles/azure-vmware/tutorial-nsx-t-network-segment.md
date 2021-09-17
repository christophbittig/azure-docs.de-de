---
title: 'Tutorial: Hinzufügen eines Netzwerksegments in Azure VMware Solution'
description: Hier erfahren Sie, wie Sie ein Netzwerksegment für die Verwendung für VMs in vCenter hinzufügen.
ms.topic: tutorial
ms.custom: contperf-fy22q1
ms.date: 07/16/2021
ms.openlocfilehash: a678e29591bc3ca17a1ec6638e111ffeb8c264b0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734208"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>Tutorial: Hinzufügen eines Netzwerksegments in Azure VMware Solution 

Nach der Bereitstellung von Azure VMware Solution können Sie ein NSX-T-Netzwerksegment über NSX-T Manager oder das Azure-Portal konfigurieren. Nach der Konfiguration werden die Segmente in Azure VMware Solution, NSX-T Manger und vCenter angezeigt. NSX-T wird vorab mit einem NSX-T Tier-0-Gateway im **Aktiv/Aktiv-Modus** und einem NSX-T Tier-1-Standardgateway im **aktiven Standbymodus** bereitgestellt.  Mit diesen Gateways können Sie eine Verbindung der Segmente herstellen (logische Switches) und Ost-West- und Nord-Süd-Konnektivität bereitstellen. 

>[!TIP]
>Im Azure-Portal wird eine vereinfachte Ansicht von NSX-T-Vorgängen angezeigt, die ein VMware-Administrator regelmäßig benötigt und die für Benutzer bestimmt ist, die mit NSX-T Manager nicht vertraut sind. 


In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Hinzufügen von Netzwerksegmenten mithilfe von NSX-T Manager oder des Azure-Portals
> * Überprüfen des neuen Netzwerksegments 

## <a name="prerequisites"></a>Voraussetzungen

Eine private Azure VMware Solution-Cloud mit Zugriff auf die vCenter- und NSX-T Manager-Verwaltungsschnittstellen Weitere Informationen finden Sie im Tutorial zum [Konfigurieren des Netzwerks](tutorial-configure-networking.md).

## <a name="use-nsx-t-manager-to-add-network-segment"></a>Verwenden von NSX-T Manager zum Hinzufügen eines Netzwerksegments 

Die in vCenter erstellten virtuellen Computer (VMs) werden in den unter NSX-T erstellten Netzwerksegmenten platziert und sind in vCenter sichtbar.

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="use-azure-portal-to-add-an-nsx-t-segment"></a>Verwenden des Azure-Portals zum Hinzufügen eines NSX-T-Segments

[!INCLUDE [create-nsxt-segment-azure-portal-steps](includes/create-nsxt-segment-azure-portal-steps.md)]


## <a name="verify-the-new-network-segment"></a>Überprüfen des neuen Netzwerksegments

Überprüfen Sie, ob das neue Netzwerksegment vorhanden ist. In diesem Beispiel ist **ls01** das neue Netzwerksegment.

1. Wählen Sie in NSX-T Manager die Optionen **Networking** > **Segments** (Netzwerk > Segmente) aus. 

    :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Screenshot: Bestätigung und Status des neuen Netzwerksegments in NSX-T":::

1. Klicken Sie in vCenter auf **Networking** > **SDDC-Datacenter** (Netzwerk > SDDC-Datacenter).

    :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="Screenshot: Bestätigung des neuen Netzwerksegments in vCenter":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein NSX-T-Netzwerksegment erstellt, das für VMs in vCenter verwendet wird. 

Sie können nun: 

- [Konfigurieren und Verwalten von DHCP in Azure VMware Solution](configure-dhcp-azure-vmware-solution.md)
- [Erstellen einer Inhaltsbibliothek zum Bereitstellen von VMs in Azure VMware Solution](deploy-vm-content-library.md) 
- [Zusammenfassen lokaler Umgebungen mittels Peering zu einer privaten Cloud](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
