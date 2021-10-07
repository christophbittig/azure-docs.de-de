---
title: Herstellen einer Verbindung mit virtuellen Linux-Computern
description: Es wird beschrieben, wie Sie in einem Lab (Azure DevTest Labs) eine Verbindung mit Ihrer Linux-VM herstellen.
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 956c27235dcdf9d57c908ce2e840e3c77518875b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676046"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>Herstellen einer Verbindung mit einer Linux-VM im Lab (Azure DevTest Labs)
In diesem Artikel wird beschrieben, wie Sie in Ihrem Lab eine Verbindung mit einer Linux-VM herstellen. 

## <a name="connect-to-a-linux-vm"></a>Herstellen einer Verbindung mit einem virtuellen Linux-Computer
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie in der Suchleiste nach **DevTest Labs**, und wählen Sie die Option aus. 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="Suchen nach und Auswählen von DevTest Labs":::    
1. Wählen Sie in der Liste mit den Labs Ihr **Lab** aus.

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="Auswählen Ihres Labs":::            
1. Wählen Sie auf der Startseite für Ihr Lab in der Liste **Meine virtuellen Computer** Ihre Linux-VM aus. 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="Auswählen Ihrer Linux-VM":::        
5. Auf der Seite **Übersicht** wird der vollqualifizierte Domänenname (FQDN) bzw. die IP-Adresse der VM angezeigt. Darüber hinaus wird der Port wie in der folgenden Abbildung angezeigt.

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="Vollqualifizierter Domänenname für die VM":::    

    Beachten Sie, dass die Schaltfläche **Verbinden** abgeblendet ist, obwohl die VM gestartet wurde. Dies ist beabsichtigt.
6.  Verwenden Sie SSH, um eine Verbindung mit Ihrer Linux-VM herzustellen. Im folgenden Beispiel wird mit dem Benutzernamen `vmuser` und über den Port `51637` eine Verbindung mit der VM mit dem FQDN `mydtl07172452621450000.eastus.cloudapp.azure.com` hergestellt. Geben Sie das Kennwort für den Benutzer ein, um eine Verbindung mit der VM herzustellen. 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    Sie können Tools wie [PuTTY](https://www.putty.org/) oder einen anderen SSH-Client verwenden, um eine Verbindung mit der VM herzustellen. 

    Nach der Verbindungsherstellung per SSH können Sie eine Desktopumgebung ([xfce](https://www.xfce.org)) und einen Remotedesktop ([xrdp](http://xrdp.org)) installieren und konfigurieren.  Ausführlichere Informationen finden Sie unter [Installieren und Konfigurieren von Remotedesktop zum Herstellen einer Verbindung mit einem virtuellen Linux-Computer in Azure](../virtual-machines/linux/use-remote-desktop.md). 

## <a name="next-steps"></a>Nächste Schritte
[Gewusst wie: Herstellen einer Verbindung mit einem virtuellen Windows-Computer](connect-windows-virtual-machine.md)
