---
title: Informationen über Geräte in bestimmten Zonen
description: Verwenden Sie die lokale Verwaltungskonsole, um umfassende Ansichtsinformationen für jede Zone zu erhalten.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/09/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 511c3b5f66b84490380507c5106775cd6cc6b255
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318881"
---
# <a name="view-information-per-zone"></a>Anzeigen von Informationen nach Zone


## <a name="view-a-device-map-for-a-zone"></a>Anzeigen der Gerätezuordnung für eine Zone

Rufen Sie Informationen zu einer Gerätezuordnung für eine ausgewählte Zone auf einem Sensor ab. Diese Ansicht umfasst alle Netzwerkelemente an, die sich auf die ausgewählte Zone beziehen, einschließlich der Sensoren, der Geräte, die mit ihnen verbunden sind, und anderer Informationen.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Screenshot: Zonenzuordnung":::


- Wählen Sie im Fenster **Standortverwaltung** die Option **Zonenzuordnung anzeigen** in der Liste mit dem Zonennamen aus.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Standardregion für Standardgeschäftseinheit":::

Das Fenster **Gerätezuordnung** wird angezeigt. Die folgenden Tools sind verfügbar, um Geräte und Geräteinformationen aus der Zuordnung anzuzeigen. Ausführliche Informationen zu den einzelnen Features finden Sie im *Benutzerleitfaden zur Defender für IoT-Plattform*.

- **Vergrößerung von Zuordnungsansichten:** Vereinfachte Ansicht, Verbindungsansicht und detaillierte Ansicht. Die angezeigte Zuordnungsansicht variiert abhängig von der Zoomstufe. Durch Anpassen der Zoomstufen können Sie zwischen Zuordnungsansichten wechseln.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **Such- und Layouttools für die Zuordnung:** Tools für die Anzeige verschiedener Netzwerksegmente, Geräte, Gerätegruppen oder Ebenen.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="Screenshot: Ansicht „Such- und Layouttools“":::

- **Bezeichnungen und Indikatoren auf Geräten:** Beispielsweise die Anzahl der Geräte, die in einem Subnetz in einem IT-Netzwerk gruppiert sind. In diesem Beispiel lautet sie 8.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="Screenshot: Bezeichnungen und Indikatoren":::

- **Anzeigen von Geräteeigenschaften:** Beispielsweise der Sensor, der die Geräte- und grundlegenden Geräteeigenschaften überwacht. Klicken Sie mit der rechten Maustaste auf das Gerät, um die Geräteeigenschaften anzuzeigen.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="Screenshot: Ansicht „Geräteeigenschaften“.":::

- **Einem Gerät zugeordnete Warnungen:** Klicken Sie mit der rechten Maustaste auf das Gerät, um die damit verbundenen Warnungen anzuzeigen.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="Screenshot: Ansicht „Warnungen anzeigen“":::

## <a name="view-alerts-associated-with-a-zone"></a>Anzeigen von Warnungen, die einer Zone zugeordnet sind

So zeigen Sie Warnungen an, die einer bestimmten Zone zugeordnet sind

- Wählen Sie das Warnungssymbol im Fenster **Zone** aus. 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="Die Ansicht „Standardgeschäftseinheit“ mit Beispielen":::

Weitere Informationen finden Sie unter [Übersicht: Arbeiten mit Warnungen](how-to-work-with-alerts-on-premises-management-console.md).

### <a name="view-the-device-inventory-of-a-zone"></a>Anzeigen des Gerätebestands einer Zone

So zeigen Sie den einer bestimmten Zone zugeordneten Gerätebestand an

- Wählen Sie im Fenster **Zone** die Option **Gerätebestand anzeigen** aus.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="Der Bildschirm „Gerätebestand“ wird angezeigt.":::

Weitere Informationen finden Sie unter [Untersuchen aller Unternehmenssensorerkennungen in einem Gerätebestand](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md).

## <a name="view-additional-zone-information"></a>Anzeigen weiterer Zoneninformationen

Folgende zusätzliche Zoneninformationen sind verfügbar:

- **Zonendetails:** Anzeigen der Anzahl von Geräten, Warnungen und Sensoren, die der Zone zugeordnet sind

- **Sensordetails:** Zeigen Sie den Namen, die IP-Adresse und die Version der einzelnen Sensoren an, die der Zone zugewiesen sind.

- **Konnektivitätsstatus:** Wenn ein Sensor getrennt ist, stellen Sie eine Verbindung mit diesem Sensor her. Weitere Informationen finden Sie unter [Verbinden von Sensoren mit der lokalen Verwaltungskonsole](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console). 

- **Aktualisierungsstatus:** Wenn der verbundene Sensor aktualisiert wird, werden die Upgradestatus angezeigt. Während des Upgradevorgangs empfängt die lokale Verwaltungskonsole keine Geräteinformationen des Sensors.

## <a name="next-steps"></a>Nächste Schritte

[Gewinnen von Erkenntnissen zu globalen, regionalen und lokalen Bedrohungen](how-to-gain-insight-into-global-regional-and-local-threats.md)
