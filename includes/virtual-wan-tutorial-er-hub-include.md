---
ms.author: cherylmc
author: cherylmc
ms.date: 08/19/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: f5d641a1d017f40081b311e9b9b610b7fcc7c13c
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122820816"
---
1. Suchen Sie das virtuelle WAN, das Sie erstellt haben. Wählen Sie auf der Seite „Virtual WAN“ im Abschnitt **Konnektivität** die Option **Hubs** aus. Klicken Sie auf **Neuer Hub**, um die Seite **Virtuellen Hub erstellen** zu öffnen.

   :::image type="content" source="media/virtual-wan-empty-hub/new-hub.jpg" alt-text="Der Screenshot zeigt das Konfigurationsdialogfeld „Hubs“, in dem „Neuer Hub“ ausgewählt ist.":::

1. Füllen Sie auf der Seite **Virtuellen Hub erstellen** die folgenden Felder aus:

   :::image type="content" source="media/virtual-wan-tutorial-er-hub/create-hub.png" alt-text="Screenshot: Registerkarte „Grundlagen“, auf der Sie Werte eingeben können":::

   * **Region**: Wählen Sie die Region aus, in der Sie den virtuellen Hub bereitstellen möchten.
   * **Name**: Der Name, der für den virtuellen Hub verwendet werden soll
   * **Privater Adressraum des Hubs**: Der Adressbereich des Hubs in CIDR-Notation.

1. Wählen Sie die Registerkarte **ExpressRoute** aus. Klicken Sie auf **Ja**, um die Einstellungen anzuzeigen und das Feld auszufüllen. Informationen zu Gatewayskalierungseinheiten finden Sie in den [häufig gestellten Fragen](../articles/virtual-wan/virtual-wan-faq.md#what-are-virtual-wan-gateway-scale-units).

   :::image type="content" source="media/virtual-wan-tutorial-er-hub/expressroute.png" alt-text="Screenshot: Registerkarte „ExpressRoute“, auf der Sie Werte eingeben können":::

1. Wählen Sie zum Überprüfen **Überprüfen + erstellen** aus.
1. Wählen Sie **Erstellen** aus, um den Hub mit einem ExpressRoute-Gateway zu erstellen. Die Erstellung eines Hubs kann bis zu 30 Minuten dauern. Klicken Sie nach 30 Minuten auf **Aktualisieren**, um den Hub auf der Seite **Hubs** anzuzeigen. Wählen Sie **Zu Ressource wechseln** aus, um zur Ressource zu navigieren.