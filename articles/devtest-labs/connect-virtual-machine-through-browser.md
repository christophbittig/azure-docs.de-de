---
title: Aktivieren des Browserzugriffs auf virtuelle Lab-Computer
description: Erfahren Sie, wie Sie über einen Browser eine Verbindung mit Ihren virtuellen Computern herstellen.
ms.topic: how-to
ms.date: 10/29/2021
ms.openlocfilehash: f712d0090defa28f673807b835dbe3642cfdface
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464362"
---
# <a name="connect-to-your-lab-virtual-machines-through-a-browser"></a>Herstellen einer Verbindung mit Ihren virtuellen Lab-Computern über einen Browser 

Die DevTest-Labs integrieren sich in [Azure Bastion](../bastion/index.yml), sodass Sie über einen Browser eine Verbindung mit Ihren virtuellen Lab-Computern herstellen können. Nach dem Aktivieren der **Browserverbindung** können Benutzer des Labs über einen Browser auf ihre virtuellen Computer zugreifen.  

In dieser Anleitung stellen Sie mithilfe der **Browser-Verbindung** eine Verbindung mit einem virtuellen Lab-Computer her.

## <a name="prerequisites"></a>Voraussetzungen

- Ein virtueller Lab-Computer mit den aktivierten Einstellungen [Durch Bastion konfiguriertes virtuelles Netzwerk und **Browserverbindung**](enable-browser-connection-lab-virtual-machines.md).

- Ein Webbrowser, der so konfiguriert ist, dass Popups von `https://portal.azure.com:443`zugelassen werden.

## <a name="launch-virtual-machine-in-a-browser"></a>Starten des virtuellen Computers in einem Browser

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie in **DevTest Labs** zu Ihrem Lab.

1. Wählen Sie einen virtuellen Computer aus.

1. Wählen Sie im oberen Menü **Browserverbindung** aus.

1. Geben Sie im Abschnitt **Browserverbindung** Ihre Anmeldeinformationen ein und wählen Sie dann **Verbinden** aus.

    :::image type="content" source="./media/connect-virtual-machine-through-browser/lab-vm-browser-connect.png" alt-text="Screenshot der Schaltfläche Browserverbindung.":::

## <a name="next-steps"></a>Nächste Schritte

[Hinzufügen eines virtuellen Computers zu einem Lab in Azure DevTest Labs](devtest-lab-add-vm.md)
