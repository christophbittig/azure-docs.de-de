---
author: cherylmc
ms.author: cherylmc
ms.date: 08/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: c52c00dd8fa834775a01162e2506f1821b0dcdae
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130173298"
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

   Beachten Sie, dass das Verwenden von Tastenkombinationen während der Verbindung mit einer VM möglicherweise nicht das selbe Verhalten zeigt wie das Verwenden von Tastenkombinationen auf einem lokalen Computer. Von einem Windows-Clientcomputer, der mit einer Windows-VM verbunden ist, stellt STRG+ALT+ENDE beispielsweise die Tastenkombination für STRG+ALT+ENTF dar. Von einem Mac-Clientcomputer, der mit einer Windows-VM verbunden ist, lautet die Tastenkombination Fn+STRG+ALT+RÜCKTASTE.
