---
title: Häufigkeit von VMware-Softwareupdates
description: Unterstützte Häufigkeit von VMware-Softwareupdates für Azure VMware Solution.
ms.topic: include
ms.date: 04/23/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 3295ab9da93b1e1ec1cace62653670de366c880d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324314"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

Die Hostwartung und Lebenszyklusverwaltung haben keine Auswirkungen auf die Kapazität oder Leistung von Clustern in privaten Clouds. Upgrades der Software der privaten Cloud erfolgen nach einem Zeitplan, der sich nach der Veröffentlichung des Softwarepakets von VMware richtet.  Ihre private Cloud erfordert daher keine Downtime für Upgrades.

Die Upgrades für die Softwarepakete der privaten Cloud behalten eine Version der Software des neuesten Release des Softwarepakets von VMware bei. Die Softwareversionen der privaten Cloud können sich von den aktuellen Versionen der einzelnen Softwarekomponenten (ESXi, NSX-T, vCenter und vSAN) unterscheiden. Updates umfassen auch Treiber, Software auf den Netzwerkswitches und Firmware auf den Bare-Metal-Knoten.

Sie werden benachrichtigt, bevor und nachdem Patches auf Ihre privaten Clouds angewendet werden. Wir arbeiten auch mit Ihnen zusammen, um ein Wartungsfenster zu planen, bevor wir Updates oder Upgrades auf Ihre private Cloud anwenden. 

Softwareupdates umfassen:

- **Patches**: Sicherheitspatches oder Fehlerbehebungen, die von VMware freigegeben werden
- **Updates**: Änderungen der Nebenversion einer VMware-Stack-Komponente
- **Upgrades**: Änderungen der Hauptversion einer VMware-Stack-Komponente

>[!NOTE]
>Microsoft testet einen wichtigen Sicherheitspatch, sobald er von VMware zur Verfügung gestellt wird.

Bis die Bereitstellung der nächsten geplanten Updates erfolgt, werden dokumentierte VMware-Problemumgehungen implementiert, statt einen entsprechenden Patch zu installieren.
