---
title: 'Tutorial: Konfigurieren des Netzwerks für Ihre private VMware-Cloud in Azure'
description: Hier erfahren Sie, wie Sie das erforderliche Netzwerk zum Bereitstellen Ihrer privaten Cloud in Azure erstellen und konfigurieren.
ms.topic: tutorial
ms.custom: contperf-fy22q1
ms.date: 07/30/2021
ms.openlocfilehash: 99389c55ab13b6c6c181a4d7d1bcf14c3b8ba08d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597795"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Tutorial: Konfigurieren des Netzwerks für Ihre private VMware-Cloud in Azure

Eine private Azure VMware Solution-Cloud erfordert ein virtuelles Azure-Netzwerk. Da Azure VMware Solution Ihre lokale vCenter-Instanz nicht unterstützt, müssen Sie zusätzliche Schritte für die Integration in Ihre lokale Umgebung ausführen. Die Einrichtung einer ExpressRoute-Leitung und eines Gateways für virtuelle Netzwerke ist ebenfalls erforderlich.

[!INCLUDE [disk-pool-planning-note](includes/disk-pool-planning-note.md)]


In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks 
> * Erstellen eines Gateways für das virtuelle Netzwerk
> * Verbinden der ExpressRoute-Leitung mit dem Gateway

>[!NOTE]
>Bevor Sie ein neues vNet erstellen, prüfen Sie, ob Sie bereits über ein vorhandenes vNet in Azure verfügen, das Sie zum Herstellen einer Verbindung mit Azure VMware Solution verwenden können, oder ob Sie ein neues vNet erstellen müssen.  
>* Wenn Sie ein vorhandenes VNet im gleichen Azure-Abonnement wie Azure VMware Solution nutzen möchten, verwenden Sie die Registerkarte **[Azure vNet-Verbindung](#select-an-existing-vnet)** unter **Konnektivität**. 
>* Wenn Sie ein vorhandenes VNet in einem anderen Azure-Abonnement als Azure VMware Solution nutzen möchten, verwenden Sie den Leitfaden unter **[Manuelles Herstellen einer Verbindung mit der privaten Cloud](#connect-to-the-private-cloud-manually)** . 
>* Wenn Sie ein neues VNet im gleichen Azure-Abonnement wie Azure VMware Solution nutzen möchten, verwenden Sie die Registerkarte **[Azure vNet-Verbindung](#create-a-new-vnet)** , oder erstellen Sie [manuell](#create-a-vnet-manually) ein VNet.

## <a name="connect-with-the-azure-vnet-connect-feature"></a>Herstellen einer Verbindung mit dem Feature „Azure vNet-Verbindung“

Mit dem Feature **Azure vNet-Verbindung** können Sie ein vorhandenes vNet verwenden oder eine neues vNet erstellen, um eine Verbindung mit Azure VMware Solution herzustellen.   

>[!NOTE]
>Der Adressraum im vNet darf sich nicht mit dem CIDR-Wert der privaten Azure VMware Solution-Cloud überschneiden.


### <a name="select-an-existing-vnet"></a>Auswählen eines vorhandenen vNet

Wenn Sie ein vorhandenes vNet auswählen, wird die ARM-Vorlage (Azure Resource Manager), die das vNet und andere Ressourcen erstellt, erneut bereitgestellt. Die Ressourcen sind in diesem Fall die öffentliche IP-Adresse, das Gateway, die Gatewayverbindung und der ExpressRoute-Autorisierungsschlüssel. Wenn alles eingerichtet ist, ändert die Bereitstellung nichts. Wenn Elemente fehlen, werden diese automatisch erstellt. Wenn beispielsweise das GatewaySubnet-Element fehlt, wird es während der Bereitstellung hinzugefügt.

1. Wählen Sie in Ihrer privaten Azure VMware Solution-Cloud unter **Verwalten** die Option **Konnektivität** aus.

2. Wählen Sie die Registerkarte **Azure vNet-Verbindung** und dann das vorhandene vNet aus.

   :::image type="content" source="media/networking/azure-vnet-connect-tab.png" alt-text="Screenshot: Registerkarte „Azure vNet-Verbindung“ unter „Konnektivität“ mit einem ausgewähltem vorhandenem vNet":::

3. Wählen Sie **Speichern** aus.

   An diesem Punkt überprüft das vNet, ob sich überschneidende IP-Adressräume zwischen Azure VMware Solution und vNet erkannt werden. Wenn eine Überschneidung erkannt wird, ändern Sie die Netzwerkadresse der privaten Cloud oder des vNet so, dass sie sich nicht überschneiden. 


### <a name="create-a-new-vnet"></a>Erstellen eines neuen vNet

Wenn Sie ein neues vNet erstellen, werden die erforderlichen Komponenten zum Herstellen einer Verbindung mit Azure VMware Solution automatisch erstellt.

1. Wählen Sie in Ihrer privaten Azure VMware Solution-Cloud unter **Verwalten** die Option **Konnektivität** aus.

2. Wählen Sie die Registerkarte **Azure vNet-Verbindung** und dann **Neu erstellen** aus.

   :::image type="content" source="media/networking/azure-vnet-connect-tab-create-new.png" alt-text="Screenshot: Registerkarte „Azure vNet-Verbindung“ unter „Konnektivität“":::

3. Geben Sie die Informationen für das neue vNet an, oder aktualisieren Sie sie, und wählen Sie dann **OK** aus.

   An diesem Punkt überprüft das vNet, ob sich überschneidende IP-Adressräume zwischen Azure VMware Solution und vNet erkannt werden. Wenn eine Überschneidung erkannt wird, ändern Sie die Netzwerkadresse der privaten Cloud oder des vNet so, dass sie sich nicht überschneiden. 

   :::image type="content" source="media/networking/create-new-virtual-network.png" alt-text="Screenshot: Fenster „Virtuelles Netzwerk erstellen“":::

Das vNet mit dem angegebenen Adressbereich und dem GatewaySubnet-Element werden in Ihrem Abonnement und Ihrer Ressourcengruppe erstellt.  


## <a name="connect-to-the-private-cloud-manually"></a>Manuelles Herstellen einer Verbindung mit der privaten Cloud

### <a name="create-a-vnet-manually"></a>Manuelles Erstellen eines vNet

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



### <a name="create-a-virtual-network-gateway"></a>Erstellen eines Gateways für das virtuelle Netzwerk

Sie haben ein virtuelles Netzwerk erstellt und erstellen nun ein Gateway für das virtuelle Netzwerk.

1. Wählen Sie in Ihrer Ressourcengruppe **+ Hinzufügen** aus, um eine neue Ressource hinzuzufügen.

1. Geben Sie im Feld **Marketplace durchsuchen** den Text **Gateway für virtuelle Netzwerke** ein. Suchen Sie nach der VNET-Ressource.

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

### <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Verbinden von ExpressRoute mit dem Gateway für virtuelle Netzwerke

Sie haben ein Gateway für virtuelle Netzwerke bereitgestellt und fügen nun eine Verbindung zwischen dem Gateway und Ihrer privaten Azure VMware Solution-Cloud hinzu.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks mithilfe des Features „vNet-Verbindung“
> * Manuelles Erstellen eines virtuellen Netzwerks
> * Erstellen eines Gateways für das virtuelle Netzwerk
> * Verbinden der ExpressRoute-Leitung mit dem Gateway


Im nächsten Tutorial erfahren Sie, wie Sie die NSX-T-Netzwerksegmente erstellen, die für virtuelle Computer in vCenter verwendet werden.

> [!div class="nextstepaction"]
> [Tutorial: Erstellen eines NSX-T-Netzwerksegments in Azure VMware Solution](./tutorial-nsx-t-network-segment.md)
