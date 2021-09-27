---
title: Herstellen einer Verbindung mit Ihren virtuellen Computern über einen Browser
description: Erfahren Sie, wie Sie über einen Browser eine Verbindung mit Ihren virtuellen Computern herstellen.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: dbbaa4286aac91e362a2024f1705add8f48d566d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644997"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Herstellen einer Verbindung mit Ihren virtuellen Computern über einen Browser 

DevTest Labs und [Azure Bastion](../bastion/index.yml) sind integriert, sodass Sie über einen Browser eine Verbindung mit Ihren virtuellen Computern herstellen können. Informationen zum Aktivieren dieses Features in DevTest Labs finden Sie unter [Aktivieren der Browserverbindung auf Lab-VMs](enable-browser-connection-lab-virtual-machines.md).

Nach dem Aktivieren der *Browserverbindung* können Benutzer des Labs über einen Browser auf virtuelle Computer zugreifen.  

## <a name="create-a-lab-virtual-machine"></a>Erstellen einer Lab-VM

Sie müssen zuerst den virtuellen Labcomputer in einem VNET erstellen, für das Bastion konfiguriert ist. Wählen Sie das zweite **Subnetz** aus, das Sie erstellt haben, nicht AzureBastionSubnet. Sie können ein virtuelles Netzwerk während der Erstellung eines virtuellen Computers auswählen, indem Sie zur Registerkarte **Erweiterte Einstellungen** navigieren.

![Erstellen eines virtuellen Computers](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Starten des virtuellen Computers in einem Browser

Nachdem der virtuelle Computer erstellt wurde, können Sie ihn in einem Browser starten. Klicken Sie dazu auf die Schaltfläche *Browser verbinden*, und geben Sie Ihren Benutzernamen und Ihr Kennwort für die VM ein.  

![In einem Browser starten](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Nächste Schritte

[Hinzufügen eines virtuellen Computers zu einem Lab in Azure DevTest Labs](devtest-lab-add-vm.md)
