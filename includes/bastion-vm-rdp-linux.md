---
author: cherylmc
ms.author: cherylmc
ms.date: 08/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: ebaa86a60ae11c50658bfcfe58bbd0184bf15576
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700390"
---
1. Navigieren Sie im [Microsoft Azure-Portal](https://portal.azure.com) zu dem virtuellen Computer, mit der Sie eine Verbindung herstellen möchten. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** und dann von dem Dropdownmenü **Bastion** aus.

   :::image type="content" source="./media/bastion-vm-rdp/connect.png" alt-text="Screenshot: „Verbinden“" lightbox="./media/bastion-vm-rdp/connect.png":::

1. Wählen Sie nach der Auswahl von „Bastion“ die Option **Bastion verwenden** aus. Wenn Sie Bastion nicht für das virtuelle Netzwerk bereitgestellt haben, lesen Sie bitte den Abschnitt zum [Konfigurieren von Bastion](../articles/bastion/quickstart-host-portal.md).

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Screenshot. Verwendung von Bastion":::

1. Erweitern Sie auf der Seite **Verbindung über Azure Bastion herstellen** den Abschnitt **Verbindungseinstellungen**, und wählen Sie **RDP** aus. Wenn Sie einen anderen eingehenden Port als den RDP-Standardport (3389) verwenden möchten, geben Sie den **Port** ein.

   :::image type="content" source="./media/bastion-connect-vm-rdp-linux/connection-settings.png" alt-text="Screenshot: Port" lightbox="./media/bastion-connect-vm-rdp-linux/connection-settings.png":::

1. Geben Sie **Benutzername** und **Kennwort** ein, und wählen Sie dann **Verbinden** aus, um eine Verbindung mit dem virtuellen Computer herzustellen.

   :::image type="content" source="./media/bastion-connect-vm-rdp-linux/username-password.png" alt-text="Screenshot: Klicken auf „Verbinden“" lightbox="./media/bastion-connect-vm-rdp-linux/username-password.png":::

1. Die RDP-Verbindung zu diesem virtuellen Computer über Bastion wird direkt im Azure-Portal (über HTML5) über Port 443 und den Bastion-Dienst geöffnet. 

   Tastenkombinationen während der Verbindung mit einer VM weisen möglicherweise ein anderes Verhalten als Tastenkombinationen auf einem lokalen Computer auf. Von einem Windows-Clientcomputer, der mit einer Windows-VM verbunden ist, stellt STRG+ALT+ENDE beispielsweise die Tastenkombination für STRG+ALT+ENTF dar. Von einem Mac-Clientcomputer, der mit einer Windows-VM verbunden ist, lautet die Tastenkombination Fn+STRG+ALT+RÜCKTASTE.