---
title: Kostenmodell für Azure NetApp Files | Microsoft-Dokumentation
description: Erfahren Sie mehr zum Kostenmodell für Azure NetApp Files für die Verwaltung von auf den Dienst bezogenen Ausgaben.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: b-juche
ms.openlocfilehash: 8b2fa319237c31db2e40cc602c17691e60384d13
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265148"
---
# <a name="cost-model-for-azure-netapp-files"></a>Kostenmodell für Azure NetApp Files 

Das Verstehen des Kostenmodells für Azure NetApp Files hilft Ihnen bei der Verwaltung von auf den Dienst bezogenen Ausgaben. 

Informationen zu speziellen Kostenmodellen für die regionsübergreifende Replikation finden Sie unter [Kostenmodell für die regionsübergreifende Replikation](cross-region-replication-introduction.md#cost-model-for-cross-region-replication).

## <a name="calculation-of-capacity-consumption"></a>Berechnung der Kapazitätsnutzung

Azure NetApp Files wird nach bereitgestellter Speicherkapazität in Rechnung gestellt.  Die Zuweisung bereitgestellter Kapazität erfolgt durch Anlegen von Kapazitätspools.  Kapazitätspools werden basierend auf EUR/bereitgestellte GiB/Monat in stündlichen Schritten abgerechnet. Die Mindestgröße eines einzelnen Kapazitätspools beträgt 4 TiB, und Kapazitätspools können nachträglich in 1-TiB-Schritten vergrößert werden. Innerhalb von Kapazitätspools werden Volumes erstellt.  Jedem Volume ist ein Kontingent zugeordnet, das sich entsprechend der bereitgestellten Poolkapazität verringert. Das Kontingent, das den Volumes zugeordnet werden kann, reicht von mindestens 100 GiB bis maximal 100 TiB.  

Bei einem aktiven Volume basiert die Kapazitätsnutzung im Vergleich zum Kontingent auf der logischen (effektiven) Kapazität (aktives Dateisystem oder Momentaufnahmedaten). Ein Volume kann nur so viele Daten wie die festgelegte Größe (Kontingent) enthalten.

Die gesamte genutzte Kapazität in einem Kapazitätspool im Vergleich zu seinem bereitgestellten Umfang ist die Summe der tatsächlichen Nutzung aller Volumes innerhalb des Pools: 

   ![Ausdruck, der die Berechnung der insgesamt genutzten Kapazität zeigt](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

## <a name="capacity-consumption-of-snapshots"></a>Kapazitätsnutzung von Momentaufnahmen 

Die Kapazitätsnutzung von Momentaufnahmen in Azure NetApp Files wird mit dem Kontingent des übergeordneten Volumes verrechnet.  Daher gilt der gleiche Abrechnungstarif wie für den Kapazitätspool, zu dem das Volume gehört.  Im Gegensatz zum aktiven Volume wird die Nutzung von Momentaufnahmen jedoch anhand der genutzten inkrementellen Kapazität gemessen.  Azure NetApp Files-Momentaufnahmen sind standardmäßig differenziell. Je nach Änderungsrate der Daten belegen die Momentaufnahmen oft deutlich weniger Kapazität als die logische Kapazität des aktiven Volumes. Nehmen wir zum Beispiel an, dass Sie eine Momentaufnahme eines 500-GiB-Volumes haben, das nur 10 GiB differenzieller Daten enthält. Die Kapazitätsnutzung, die auf das Volumekontingent für das aktive Dateisystem und die Momentaufnahme angerechnet wird, beträgt 510 GiB, nicht 1.000 GiB. Sie können im Allgemeinen davon ausgehen, dass für die Speicherung der Momentaufnahmedaten einer Woche eine empfohlene Kapazität von 20 Prozent beansprucht wird (abhängig von der Momentaufnahmehäufigkeit und den täglichen Änderungsraten auf Blockebene der Anwendung). 

Das folgende Diagramm veranschaulicht die Konzepte. 

* Angenommen, es wird ein Kapazitätspool mit einer bereitgestellten Kapazität von 40 TiB verwendet. Der Pool enthält drei Volumes:    
    * Volume 1 hat ein Kontingent von 20 TiB bei einer Nutzung von 13 TiB (12 TiB aktiv, 1 TiB Momentaufnahmen).
    * Volume 2 hat ein Kontingent von 1 TiB bei einer Nutzung von 450 GiB.
    * Volume 3 hat ein Kontingent von 14 TiB bei einer Nutzung von 8,8 TiB (8 TiB aktiv, 800 GiB Momentaufnahmen).   
* Der Kapazitätspool hat eine bereitgestellte Größe von 40 TiB. 22,25 TiB der Kapazität werden genutzt (13 TiB, 450 GiB und 8,8 TiB der Kontingente der Volumes 1, 2 und 3). Der Kapazitätspool verfügt noch über eine Kapazität von 17,75 TiB.   

![Diagramm: Kapazitätspool mit drei Volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="next-steps"></a>Nächste Schritte

* [Seite mit den Preisen von Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Dienstebenen für Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Kostenmodell für die regionsübergreifende Replikation](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
* [Grundlegendes zum Volumekontingent](volume-quota-introduction.md)
* [Überwachen der Kapazität eines Volumes](monitor-volume-capacity.md)
* [Ändern der Größe eines Kapazitätspools oder Volumes](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [Verwalten der Abrechnung mithilfe von Tags](manage-billing-tags.md)
* [Häufig gestellte Fragen zur Kapazitätsverwaltung](faq-capacity-management.md)
