---
title: Aktivieren der Browserverbindung zu Azure DevTest Labs-VMs
description: Integrieren Sie Azure Bastion in DevTest Labs, um den Zugriff auf virtuelle Labcomputer (VMs) über einen Browser zu ermöglichen.
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 876a72548c70f3e7717c75973edee802e584fca2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131442986"
---
# <a name="enable-browser-connection-to-devtest-labs-vms"></a>Aktivieren der Browserverbindung zu DevTest Labs-VMs

Azure DevTest Labs und [Azure Bastion](../bastion/index.yml) sind integriert, sodass Sie über einen Browser eine Verbindung mit Ihren virtuellen Labcomputern herstellen können. Als Labbesitzer können Sie den Browserzugriff auf alle Ihre Lab-VMs über Azure Bastion aktivieren.

Azure Bastion bietet über das Remotedesktopprotokoll (RDP) und Secure Shell (SSH) sichere und nahtlose Konnektivität mittels Transport Layer Security (TLS), direkt über das Azure-Portal. Sie benötigen keinen anderen Client, Agent oder Software, um über Ihren Browser eine Verbindung mit Ihren Lab-VMs herzustellen. Ihre VMs benötigen keine öffentlichen IP-Adressen.

In diesem Artikel werden zwei verschiedene Möglichkeiten zum Aktivieren der Azure Bastion-Browserkonnektivität mit DevTest Labs-VMs beschrieben:

- Sie können ein neues Azure Bastion-aktiviertes virtuelles Netzwerk für Ihr Lab und die zugehörigen VMs erstellen.
- Sie können Azure Bastion in Ihrem vorhandenen virtuellen Labnetzwerk bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Erhalten oder [Erstellen eines Labs](tutorial-create-custom-lab.md#create-a-lab) in DevTest Labs
- Um Azure Bastion Browserzugriff verwenden zu können, müssen Labbenutzer auf dem Azure Bastion Host und im virtuellen Labnetzwerk, in dem Azure Bastion konfiguriert ist, über die Rolle **Leser** verfügen.

## <a name="option-1-connect-a-lab-to-an-azure-bastion-enabled-virtual-network"></a>Option 1: Verbinden eines Labs mit einem Azure Bastion-aktivierten virtuellen Netzwerk

Erstellen Sie zunächst ein neues virtuelles Netzwerk mit einem Azure Bastion-Subnetz und einem anderen Subnetz darin. Ein Azure Bastion-Subnetz lässt das Erstellen von Nicht-Azure Bastion-Ressourcen im Subnetz nicht zu. Daher benötigen Sie das andere Subnetz zum Erstellen von Lab-VMs.

1. Suchen Sie im Azure-Portal nach der Option **Virtuelle Netzwerke** und wählen Sie sie aus.
1. Wählen Sie oben auf der Seite **Virtuelle Netzwerke** die Option **+ Erstellen** aus.
1. Geben Sie auf dem Bildschirm **Virtuelles Netzwerk erstellen** einen **Namen** für das neue virtuelle Netzwerk ein, und wählen Sie das gleiche **Abonnement**, die gleiche **Ressourcengruppe** und die gleiche **Region** wie für Ihr Lab aus.
1. Klicken Sie auf **Weiter: IP-Adressen**.
1. Auf der Registerkarte **IP-Adressen** gibt es bereits ein Subnetz, **Standard**. Wählen Sie **Subnetz hinzufügen** aus.
1. Geben Sie im Bereich **Subnetz hinzufügen** unter *Name* den Namen **AzureBastionSubnet** ein.
1. Geben Sie unter **Subnetzadressbereich** einen Adressbereich ein, der sich innerhalb des Adressraums des virtuellen Netzwerks befindet, sich jedoch nicht mit dem Standardsubnetz überschneidet. Bei Bedarf können Sie neue Adressräume in den leeren Feldern auf der Seite **Virtuelles Netzwerk erstellen** hinzufügen.
1. Wählen Sie **Hinzufügen**.

   ![Screenshot, der das Erstellen des Subnetzes "AzureBastionSubnet" zeigt.](media/enable-browser-connection-lab-virtual-machines/create-subnet.png)

1. Klicken Sie auf **Überprüfen + erstellen** und nach der Überprüfung auf **Erstellen**.
1. Nachdem das neue virtuelle Netzwerk erstellt wurde, wechseln Sie zur entsprechenden Seite, wählen Sie im linken Navigationsbereich **Subnetze** aus, und vergewissern Sie sich, dass zwei Subnetze vorhanden sind: **Standard** und **AzureBastionSubnet**.

   ![Screenshot, der zwei Subnetze im virtuellen Azure Bastion-Netzwerk zeigt.](media/enable-browser-connection-lab-virtual-machines/second-subnet.png)

Verbinden Sie als Nächstes Ihr Lab mit dem neuen virtuellen Netzwerk:

1. Wählen Sie auf der Seite **Übersicht** Ihres Labs im linken Navigationsbereich **Konfiguration und Richtlinien** aus.
1. Wählen Sie auf der Seite **Konfiguration und Richtlinien** im linken Navigationsbereich unter **Externe Ressourcen** die Option **Virtuelle Netzwerke** aus.
1. Wählen Sie auf der Seite **| Konfiguration und Richtlinien | Virtuelle Netzwerke**  oben auf der Seite die Option **Hinzufügen** aus.
1. Wählen Sie im Bereich **Virtuelles Netzwerk** die Option **Virtuelles Netzwerk auswählen** aus.
1. Wählen Sie auf der Seite **Virtuelles Netzwerk auswählen** das soeben erstellte Azure Bastion-aktivierte virtuelle Netzwerk aus.
1. Wählen Sie oben auf der Seite **Virtuelles Netzwerk** die Option **Speichern** aus.
1. Entfernen Sie auf der Seite **Konfiguration und Richtlinien | Virtuelle Netzwerke** alle vorherigen virtuellen Netzwerke aus dem Lab. Wählen Sie **...** neben diesem virtuellen Netzwerk aus, wählen Sie **Löschen** und dann **Ja** aus. 

   ![Screenshot, der das Löschen des alten virtuellen Labnetzwerks zeigt.](media/enable-browser-connection-lab-virtual-machines/add-virtual-network.png)

Aktivieren der VM-Erstellung im Nicht-Azure Bastion-Subnetz:

1. Wählen Sie auf der Seite **Konfiguration und Richtlinien | Virtuelle Netzwerke** das Azure Bastion-aktivierte virtuelle Netzwerk aus.
1. Stellen Sie auf der Seite **Virtuelles Netzwerk** sicher, dass sowohl das Subnetz **AzureBastionSubnet** als auch das Subnetz **Standard** angezeigt werden. Wenn nicht beide Subnetze angezeigt werden, schließen Sie die Seite, und öffnen Sie sie erneut.
1. Wählen Sie das Subnetz **Standard** aus.
1. Wählen Sie auf dem Bildschirm **Labsubnetz** unter **Bei Erstellung virtueller Computer verwenden** die Option **Ja** und dann **Speichern** aus. Sie können jetzt VMs im Standardsubnetz Ihres virtuellen Labnetzwerks erstellen.

   ![Screenshot, der das Aktivieren der VM-Erstellung im Standardsubnetz zeigt.](./media/enable-browser-connection-lab-virtual-machines/enable-vm-creation-subnet.png)

## <a name="option-2-deploy-azure-bastion-in-a-labs-existing-virtual-network"></a>Option 2: Bereitstellen von Azure Bastion im vorhandenen virtuellen Netzwerk eines Labs

Erstellen Sie zunächst ein neues Azure Bastion-Subnetz im vorhandenen virtuellen Netzwerk Ihres Labs:

1. Suchen Sie im Azure-Portal nach der Option **Virtuelle Netzwerke** und wählen Sie sie aus.
1. Wählen Sie in der Liste auf der Seite **Virtuelle Netzwerke** das vorhandene virtuelle Netzwerk Ihres Labs aus.
1. Wählen Sie auf der Seite des virtuellen Netzwerks im linken Navigationsbereich **Subnetze** aus.
1. Wählen Sie auf der Seite **Subnetze** die Option **+Subnetz** aus.
1. Geben Sie im Bereich **Subnetz hinzufügen** unter *Name* den Namen **AzureBastionSubnet** ein.
1. Geben Sie unter **Subnetzadressbereich** einen Adressbereich ein, der sich innerhalb des Adressraums des virtuellen Netzwerks befindet, sich jedoch nicht mit dem bestehenden Subnetz überschneidet.
   >[!TIP]
   >Möglicherweise müssen Sie dieses Dialogfeld abbrechen, im linken Navigationsbereich des virtuellen Netzwerks **Adressraum** auswählen und einen neuen Adressraum für das Subnetz erstellen.
1. Wählen Sie **Speichern** aus.

   ![Screenshot, der das Hinzufügen eines Subnetzes im vorhandenen virtuellen Netzwerk zeigt](./media/enable-browser-connection-lab-virtual-machines/add-subnet.png)

Stellen Sie als Nächstes den Azure Bastion-Host im neuen Azure Bastion-Subnetz bereit:

1. Suchen Sie im Azure-Portal nach **Bastionhosts**, und wählen Sie diese Option aus.
1. Wählen Sie auf der Seite **Bastions** oben die Option **+ Erstellen** aus.
1. Geben Sie auf der Seite **Bastion erstellen** einen **Namen** ein, und wählen Sie das gleiche **Abonnement** die gleiche **Ressourcengruppe** und **die gleiche Region** wie für Ihr Lab aus.
1. Wählen Sie unter **Virtuelle Netzwerke** in der Dropdownliste das virtuelle Netzwerk Ihres Labs aus, und stellen Sie sicher, dass **AzureBastionSubnet** unter **Subnetz** ausgewählt ist.
1. Klicken Sie auf **Überprüfen + erstellen** und nach der Überprüfung auf **Erstellen**.

   ![Screenshot, der das Bereitstellen von Azure Bastion im vorhandenen virtuellen Netzwerk zeigt.](./media/enable-browser-connection-lab-virtual-machines/create-bastion.png)

## <a name="connect-to-lab-vms-through-azure-bastion"></a>Verbinden zu Lab-VMs über Azure Bastion

Nachdem Sie Azure Bastion in Ihrem virtuellen Labnetzwerk bereitgestellt haben, aktivieren Sie Browserverbindungen für das Lab:

1. Wählen Sie auf der Seite **Übersicht** des Labs die Option **Konfiguration und Richtlinien** und dann unter **Einstellungen** die Option **Browserverbindung** aus.
1. Wählen Sie auf der Seite **Browserverbindung** die Option **Ein** aus.
1. Wählen Sie im oberen Bereich der Seite **Speichern** aus.

   ![Screenshot, der das Aktivieren der Browserverbindung zeigt.](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

So stellen Sie über Azure Bastion eine Verbindung mit einem virtuellen Labcomputer her:

1. Wählen Sie auf der Seite **Übersicht** des Labs unter **Meine virtuellen Computer** eine Lab-VM aus.
1. Wählen Sie oben auf der Seite des virtuellen Computers die Option **Browserverbindung** aus.
1. Geben Sie im **Bereich Browserverbindung** den Benutzernamen und das Kennwort Ihres virtuellen Computers ein, und wählen Sie **Verbinden** aus.

## <a name="next-steps"></a>Nächste Schritte
- [Was ist Azure Bastion?](../bastion/bastion-overview.md)
- [Hinzufügen eines virtuellen Computers zum Lab](devtest-lab-add-vm.md)
