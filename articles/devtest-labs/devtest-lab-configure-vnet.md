---
title: Konfigurieren eines virtuellen Netzwerks
description: Erfahren Sie, wie Sie mit Azure DevTest Labs ein vorhandenes virtuelles Netzwerk und ein Subnetz konfigurieren und auf einem virtuellen Computer verwenden.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: afc3b3077aa5430d2c624d51bcd22118cb52355e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600644"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Konfigurieren eines virtuellen Netzwerks in Azure DevTest Labs
Wie im Artikel [Hinzufügen eines virtuellen Computers zu einem Lab](devtest-lab-add-vm.md) erläutert, können Sie beim Erstellen eines virtuellen Computers in einem Lab ein konfiguriertes virtuelles Netzwerk angeben. Dies ist z.B. erforderlich, wenn Sie von Ihren virtuellen Computern aus auf Ihre Unternehmensnetzwerkressourcen zugreifen müssen, und zwar über ein virtuelles Netzwerk, das mit ExpressRoute oder Site-to-Site-VPN konfiguriert wurde.

In diesem Artikel wird veranschaulicht, wie Sie Ihr vorhandenes virtuelles Netzwerk in den Einstellungen für virtuelle Netzwerke eines Labs hinzufügen, sodass es beim Erstellen Ihrer virtuellen Computer zur Auswahl steht.

> [!NOTE]
> Weitere Informationen zu den Kosten für den Azure Virtual Network-Dienst finden Sie unter [Preise für Azure Virtual Network](../virtual-network/virtual-networks-overview.md#pricing).

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Konfigurieren eines virtuellen Netzwerks für ein Lab über das Azure-Portal
Die folgenden Schritte führen Sie durch das Hinzufügen eines vorhandenen virtuellen Netzwerks (und Subnetzes) zu einem Lab, sodass es beim Erstellen eines virtuellen Computers in dem gleichen Lab verwendet werden kann. 

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
1. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
1. Wählen Sie in der Liste der Labs das gewünschte Lab aus. 
1. Wählen Sie im Hauptbereich des Labs die Option **Konfiguration und Richtlinien** aus.

    ![Zugreifen auf die Konfiguration und die Richtlinien des Labs](./media/devtest-lab-configure-vnet/policies-menu.png)
1. Wählen Sie im Abschnitt **EXTERNE RESSOURCEN** die Option **Virtuelle Netzwerke** aus. Es wird eine Liste der virtuellen Netzwerke angezeigt, die für das aktuelle Lab konfiguriert wurden, und das für Ihr Lab erstellte virtuelle Standardnetzwerk wird ebenfalls angezeigt. 
1. Klicken Sie auf **+ Hinzufügen**.
   
    ![Fügen Sie Ihrem Lab ein vorhandenes virtuelles Netzwerk hinzu.](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. Wählen Sie im Bereich **Virtuelles Netzwerk** die Option **[Virtuelles Netzwerk auswählen]** aus.
   
    ![Wählen Sie ein vorhandenes virtuelles Netzwerk aus.](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. Wählen Sie im Bereich **Virtuelles Netzwerk auswählen** das gewünschte virtuelle Netzwerk aus. Es wird eine Liste mit allen virtuellen Netzwerken, die sich im Abonnement in derselben Region wie das Lab befinden, angezeigt.
1. Nachdem Sie ein virtuelles Netzwerk ausgewählt haben, wird wieder der Bereich **Virtuelles Netzwerk** angezeigt. Wählen Sie das Subnetz in der Liste unten aus.

    ![Subnetzliste](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Der Bereich mit dem Labsubnetz wird angezeigt.

    ![Bereich mit Labsubnetz](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Geben Sie unter **Name des Labsubnetzes** einen Namen an.
   - Damit ein Subnetz bei der Lab-VM-Erstellung verwendet werden kann, aktivieren Sie die Option **Bei Erstellung virtueller Computer verwenden**.
   - Zum Aktivieren einer [freigegebenen öffentlichen IP-Adresse](devtest-lab-shared-ip.md) wählen Sie die Option **Freigegebene öffentliche IP-Adresse aktivieren**.
   - Um die Verwendung öffentlicher IP-Adressen in einem Subnetz zu ermöglichen, wählen Sie die Option **Erstellung öffentlicher IP-Adressen zulassen**.
   - Geben Sie im Feld **Max. Anzahl virtueller Computer pro Benutzer** die maximale Anzahl von virtuellen Computern pro Benutzer für jedes Subnetz an. Wenn eine unbeschränkte Anzahl von VMs verwendet werden soll, lassen Sie dieses Feld leer.
1. Wählen Sie **OK** aus, um den Bereich mit dem Labsubnetz zu schließen.
1. Wählen Sie **Speichern** aus, um den Bereich mit dem virtuellen Netzwerk zu schließen.

Jetzt ist das virtuelle Netzwerk konfiguriert und kann beim Erstellen eines virtuellen Computers ausgewählt werden. Informationen zum Erstellen eines virtuellen Computers und zum Angeben eines virtuellen Netzwerks finden Sie im Artikel [Hinzufügen eines virtuellen Computers zu einem Lab in Azure DevTest Labs](devtest-lab-add-vm.md). 

Die [Dokumentation zu Azure Virtual Network](../virtual-network/index.yml) enthält weitere Informationen zur Verwendung von VNETs, einschließlich Informationen zum Einrichten und Verwalten eines VNET und zum Verbinden von diesem mit Ihrem lokalen Netzwerk.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie Ihrem Lab das gewünschte virtuelle Netzwerk hinzugefügt haben, besteht der nächste Schritt darin, [Ihrem Lab einen virtuellen Computer hinzuzufügen](devtest-lab-add-vm.md).
