---
title: Datei einfügen
description: Datei einfügen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/03/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 78721aa407977262747c43be90001da2343322aa
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123483860"
---
Vorerst können nur Disk Ultra, SSD Premium und SSD Standard freigegebene Datenträger ermöglichen. Unterschiedliche Datenträgergrößen haben möglicherweise verschiedene `maxShares`-Limits, die Sie beim Festlegen des `maxShares`-Werts nicht überschreiten können. Bei SSD Premium sind die Datenträgergrößen, die die Freigabe ihrer Datenträger unterstützen, P15 und größer.

Sie können für jeden Datenträger einen `maxShares`-Wert definieren, der die maximale Anzahl von Knoten darstellt, die den Datenträger gleichzeitig nutzen können. Wenn Sie z. B. einen Failovercluster mit zwei Knoten einrichten möchten, legen Sie `maxShares=2` fest. Der Maximalwert ist eine Obergrenze. Knoten können dem Cluster beitreten oder ihn verlassen (Einbindung des Datenträgers oder Aufheben der Einbindung), solange die Anzahl der Knoten kleiner als der angegebene `maxShares`-Wert ist.

> [!NOTE]
> Der `maxShares`-Wert kann nur festgelegt oder bearbeitet werden, wenn der Datenträger von allen Knoten getrennt wird.

### <a name="premium-ssd-ranges"></a>SSD Premium-Bereiche

In der folgenden Tabelle werden die zulässigen maximalen Werte für `maxShares` nach SSD Premium-Größe veranschaulicht:

|Datenträgergrößen  |maxShares-Limit  |
|---------|---------|
|P1,P2,P3,P4,P6,P10,P15,P20     |3         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Die IOPS- und Bandbreitenlimits für einen Datenträger sind vom `maxShares`-Wert nicht betroffen. Beispielsweise beträgt der maximale IOPS eines P15-Datenträgers 1100, unabhängig davon, ob maxShares = 1 oder maxShares > 1 ist.

### <a name="standard-ssd-ranges"></a>SSD Standard-Bereiche

In der folgenden Tabelle werden die zulässigen maximalen Werte für `maxShares` nach SSD Standard-Größe veranschaulicht:

|Datenträgergrößen  |maxShares-Limit  |
|---------|---------|
|E1,E2,E3,E4,E6,E10,E15,E20     |3         |
|E30, E40, E50     |5         |
|E60, E70, E80     |10         |

Die IOPS- und Bandbreitenlimits für einen Datenträger sind vom `maxShares`-Wert nicht betroffen. Beispielsweise beträgt der maximale IOPS eines E15-Datenträgers 500, unabhängig davon, ob maxShares = 1 oder maxShares > 1 ist.

### <a name="ultra-disk-ranges"></a>Disk Ultra-Bereiche

Der minimale `maxShares`-Wert ist 1, während der maximale `maxShares`-Wert 5 beträgt. Es gibt keine Größenbeschränkungen für Disk Ultra. Disk Ultra-Instanzen beliebiger Größe können jeden Wert für `maxShares` verwenden, bis einschließlich des Maximalwerts.