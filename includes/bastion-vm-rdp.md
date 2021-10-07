---
title: include file
description: Datei einfügen
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 08/27/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4561159b143f7cbaa9908aabb48e8a0dd08578be
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627157"
---
1. Navigieren Sie im [Microsoft Azure-Portal](https://portal.azure.com) zu dem virtuellen Computer, mit der Sie eine Verbindung herstellen möchten. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** und dann von dem Dropdownmenü **Bastion** aus.

   :::image type="content" source="./media/bastion-vm-rdp/connect.png" alt-text="Screenshot: „Verbinden“" lightbox="./media/bastion-vm-rdp/connect.png":::

1. Nachdem Sie „Bastion“ aus dem Dropdown ausgewählt haben, wird eine Seitenleiste mit drei Registerkarten angezeigt: RDP, SSH und Bastion. Wenn Bastion für das virtuelle Netzwerk bereitgestellt wurde, ist die Registerkarte „Bastion“ standardmäßig aktiv. Klicken Sie auf **Bastion verwenden**.

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Screenshot: Auswahl von „Bastion verwenden“":::

1. Geben Sie auf der Seite **Verbindung über Azure Bastion herstellen** den Benutzernamen und das Kennwort für Ihre VM ein, und klicken Sie dann auf **Verbinden**.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="Screenshot: Schaltfläche „Verbinden“":::

1. Die RDP-Verbindung zu diesem virtuellen Computer über Bastion wird direkt im Azure-Portal (über HTML5) über Port 443 und den Bastion-Dienst geöffnet. 

   * Wenn Sie eine Verbindung herstellen, sieht der Desktop der VM möglicherweise anders aus als im Beispielscreenshot. 
   * Tastenkombinationen während der Verbindung mit einer VM weisen möglicherweise ein anderes Verhalten als Tastenkombinationen auf einem lokalen Computer auf. Wenn Sie beispielsweise über einen Windows-Client eine Verbindung mit einer Windows-VM hergestellt haben, ist STRG+ALT+ENDE die Tastenkombination für STRG+ALT+ENTF auf einem lokalen Computer. Wenn Sie dieselbe Aktion bei einer Verbindung von einem Mac aus mit einer Windows-VM ausführen möchten, lautet die Tastenkombination Fn+STRG+ALT+RÜCKTASTE.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Screenshot: Verbinden mit Port 443":::
