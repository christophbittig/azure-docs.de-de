---
author: cherylmc
ms.author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 563de3b6c57f38d63f2759c62fccc5a17d17dae9
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778473"
---
1. Navigieren Sie zu Ihrem **virtuellen WAN**.

1. Wählen Sie **Virtuelle Netzwerkverbindungen** aus.

1. Wählen Sie auf der Seite für die VNET-Verbindung **+Add connection** (+Verbindung hinzufügen) aus.

   :::image type="content" source="./media/virtual-wan-connect-vnet-hub/add.png" alt-text="Screenshot: Schaltfläche zum Hinzufügen":::

1. Konfigurieren Sie auf der Seite **Verbindung hinzufügen** die erforderlichen Einstellungen. Weitere Informationen zu Routingeinstellungen finden Sie unter [Informationen zum Routing virtueller Hubs](../articles/virtual-wan/about-virtual-hub-routing.md).
 
   :::image type="content" source="./media/virtual-wan-connect-vnet-hub/connection.png" alt-text="Screenshot: Seite für die VNet-Verbindung":::

   * **Verbindungsname**: Benennen Sie Ihre Verbindung.
   * **Hubs**: Wählen Sie den Hub aus, den Sie dieser Verbindung zuordnen möchten.
   * **Abonnement**: Überprüfen Sie das Abonnement.
   * **Ressourcengruppe**: Die Ressourcengruppe, die das VNet enthält
   * **Virtuelles Netzwerk**: Wählen Sie das virtuelle Netzwerk aus, das Sie mit diesem Hub verbinden möchten. Für das von Ihnen gewählte virtuelle Netzwerk kann nicht bereits ein Gateway für virtuelle Netzwerke vorhanden sein.
   * **An keine verteilen**: Diese Einstellung ist standardmäßig auf **Nein** festgelegt. Wenn Sie den Schalter auf **Ja** festlegen, stehen die Konfigurationsoptionen für **An Routingtabellen weitergeben** und **Propagate to labels** (An Bezeichnungen weitergeben) nicht mehr für die Konfiguration zur Verfügung.
   * **Associate Route Table** (Routingtabelle zuordnen): Sie können die Routingtabelle auswählen, die Sie zuordnen möchten.
   * **Statische Routen**: Mit dieser Einstellung können Sie den nächsten Hop angeben.

1. Wenn Sie die Einstellungen vorgenommen haben, die Sie konfigurieren möchten, wählen Sie **Erstellen** aus, um die Verbindung zu erstellen.