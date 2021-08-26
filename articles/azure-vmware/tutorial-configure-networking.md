---
title: 'Tutorial: Konfigurieren des Netzwerks für Ihre private VMware-Cloud in Azure'
description: Hier erfahren Sie, wie Sie das erforderliche Netzwerk zum Bereitstellen Ihrer privaten Cloud in Azure erstellen und konfigurieren.
ms.topic: tutorial
ms.custom: contperf-fy22q1
ms.date: 04/23/2021
ms.openlocfilehash: 10326a07e5838dd5fe2264029c857f5ad49f5811
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442018"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Tutorial: Konfigurieren des Netzwerks für Ihre private VMware-Cloud in Azure

Eine private Azure VMware Solution-Cloud erfordert ein virtuelles Azure-Netzwerk. Da Azure VMware Solution Ihre lokale vCenter-Instanz nicht unterstützt, sind zusätzliche Schritte erforderlich, um die Integration in Ihre lokale Umgebung durchführen. Die Einrichtung einer ExpressRoute-Leitung und eines Gateways für virtuelle Netzwerke ist ebenfalls erforderlich.

[!INCLUDE [disk-pool-planning-note](includes/disk-pool-planning-note.md)]

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks
> * Erstellen eines Gateways für das virtuelle Netzwerk
> * Verbinden der ExpressRoute-Leitung mit dem Gateway


## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie zu der Ressourcengruppe, die Sie im [Tutorial zum Erstellen einer privaten Cloud](tutorial-create-private-cloud.md) erstellt haben, und wählen Sie **+ Hinzufügen** aus, um eine neue Ressource zu definieren. 

1. Geben Sie **Virtuelles Netzwerk** in das Feld **Marketplace durchsuchen** ein. Suchen Sie nach der VNET-Ressource.

1. Wählen Sie auf der Seite **Virtuelles Netzwerk** die Option **Erstellen** aus, um Ihr virtuelles Netzwerk für die private Cloud einzurichten.

1. Geben Sie auf der Seite **Virtuelles Netzwerk erstellen** die Details für Ihr virtuelles Netzwerk ein.

1. Geben Sie auf der Registerkarte **Grundlagen** einen Namen für das virtuelle Netzwerk ein, und wählen Sie die entsprechende Region und dann **Weiter: IP-Adressen** aus.

1. Geben Sie auf der Registerkarte **IP-Adressen** unter **IPv4-Adressraum** den Adressraum ein, den Sie im vorherigen Tutorial erstellt haben.

   > [!IMPORTANT]
   > Sie müssen einen Adressraum verwenden, der sich **nicht** mit dem Adressraum überlappt, den Sie im vorherigen Tutorial beim Erstellen der privaten Cloud verwendet haben.

1. Wählen Sie **+ Subnetz hinzufügen** aus, und geben Sie auf der Seite **Subnetz hinzufügen** einen Namen und einen entsprechenden Adressbereich für das Subnetz an. Wählen Sie zum Abschluss **Hinzufügen** aus.

1. Klicken Sie auf **Überprüfen + erstellen**.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Ein Screenshot, der die Einstellungen für das neue virtuelle Netzwerk zeigt" border="true":::.

1. Überprüfen Sie die Angaben, und wählen Sie dann **Erstellen** aus. Nach Abschluss der Bereitstellung wird Ihr virtuelles Netzwerk in der Ressourcengruppe angezeigt.

## <a name="create-a-virtual-network-gateway"></a>Erstellen eines Gateways für das virtuelle Netzwerk

Sie haben ein virtuelles Netzwerk erstellt und erstellen nun ein Gateway für das virtuelle Netzwerk.

1. Wählen Sie in Ihrer Ressourcengruppe **+ Hinzufügen** aus, um eine neue Ressource hinzuzufügen.

1. Geben Sie **Gateway für virtuelle Netzwerke** in das Feld **Marketplace durchsuchen** ein. Suchen Sie nach der VNET-Ressource.

1. Wählen Sie auf der Seite **Gateway für virtuelle Netzwerke** die Option **Erstellen** aus.

1. Geben Sie auf der Registerkarte „Grundlagen“ der Seite **Gateway für virtuelle Netzwerke erstellen** Werte für die Felder an, und wählen Sie dann **Überprüfen und erstellen** aus. 

   | Feld | Wert |
   | --- | --- |
   | **Abonnement** | Vorgegebener Wert mit dem Abonnement, zu dem die Ressourcengruppe gehört. |
   | **Ressourcengruppe** | Vorgegebener Wert für die aktuelle Ressourcengruppe. Bei dem Wert sollte es sich die Ressourcengruppe handeln, die Sie bei einem vorherigen Test erstellt haben. |
   | **Name** | Geben Sie einen eindeutigen Namen für das Gateway des virtuellen Netzwerks ein. |
   | **Region** | Wählen Sie den geografischen Standort des Gateways des virtuellen Netzwerks aus. |
   | **Gatewaytyp** | Wählen Sie **ExpressRoute** aus. |
   | **SKU** | Übernehmen Sie den Standardwert : **Standard**. |
   | **Virtuelles Netzwerk** | Wählen Sie das zuvor erstellte virtuelle Netzwerk aus. Sollte das virtuelle Netzwerk nicht angezeigt werden, vergewissern Sie sich, dass die Region des Gateways mit der Ihres virtuellen Netzwerks übereinstimmt. |
   | **Adressbereich für Gatewaysubnetz** | Dieser Wert wird aufgefüllt, wenn Sie das virtuelle Netzwerk auswählen. Lassen Sie den Standardwert unverändert. |
   | **Öffentliche IP-Adresse** | Wählen Sie **Neu erstellen**. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Ein Screenshot, der die Details für das Gateway für virtuelle Netzwerke zeigt" border="true":::.

1. Überprüfen Sie die Details auf ihre Richtigkeit, und wählen Sie dann **Erstellen** aus, um die Bereitstellung des Gateways für virtuelle Netzwerke zu starten. 
1. Fahren Sie nach Abschluss der Bereitstellung mit dem nächsten Abschnitt fort, um Ihre ExpressRoute-Verbindung mit dem Gateway für virtuelle Netzwerke zu verbinden, das Ihre private Azure VMware Solution-Cloud enthält.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Verbinden von ExpressRoute mit dem Gateway für virtuelle Netzwerke

Sie haben ein Gateway für virtuelle Netzwerke bereitgestellt und fügen nun eine Verbindung zwischen dem Gateway und Ihrer privaten Azure VMware Solution-Cloud hinzu.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks
> * Erstellen eines Gateways für das virtuelle Netzwerk
> * Verbinden der ExpressRoute-Leitung mit dem Gateway


Im nächsten Tutorial erfahren Sie, wie Sie die NSX-T-Netzwerksegmente erstellen, die für virtuelle Computer in vCenter verwendet werden.

> [!div class="nextstepaction"]
> [Tutorial: Erstellen eines NSX-T-Netzwerksegments in Azure VMware Solution](./tutorial-nsx-t-network-segment.md)