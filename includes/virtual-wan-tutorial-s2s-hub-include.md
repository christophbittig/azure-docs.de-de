---
ms.author: cherylmc
author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: c0fdadad76480880ac930e1f472def26f8f1e904
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122321919"
---
1. Suchen Sie das virtuelle WAN, das Sie erstellt haben. Wählen Sie auf der Seite „Virtual WAN“ im Abschnitt **Konnektivität** die Option **Hubs** aus.
1. Wählen Sie auf der Seite **Hubs** die Option **+ Neuer Hub** aus, um die Seite **Virtuellen Hub erstellen** zu öffnen.

   :::image type="content" source="./media/virtual-wan-tutorial-hub-include/basics.png" alt-text="Screenshot: Bereich „Virtuellen Hub erstellen“ mit ausgewählter Registerkarte „Grundlagen“" border="false":::
1. Füllen Sie auf der Seite **Virtuellen Hub erstellen** auf der Registerkarte **Grundlagen** die folgenden Felder aus:

   * **Region**: Diese Einstellung wurde früher als Standort bezeichnet. Dies ist die Region, in der Sie Ihren virtuellen Hub erstellen möchten.
   * **Name**: Der Name, unter dem der virtuelle Hub bekannt sein soll.
   * **Privater Adressraum des Hubs**: Der minimale Adressraum ist „/24“ zum Erstellen eines Hubs. Wenn Sie eine Adresse im Bereich von „/25“ bis „/32“ verwenden, tritt bei der Erstellung ein Fehler auf. Sie müssen den Adressraum des Subnetzes für die Dienste im virtuellen Hub nicht explizit planen. Azure Virtual WAN ist ein verwalteter Dienst und erstellt daher die geeigneten Subnetze im virtuellen Hub für die verschiedenen Gateways/Dienste (z. B. VPN-Gateways, ExpressRoute-Gateways, Benutzer-VPN-Point-to-Site-Gateways, Firewall, Routing usw.).
