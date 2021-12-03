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
ms.date: 11/08/2021
ms.author: b-juche
ms.openlocfilehash: 56091fb0c6ddfa205fc60f6aef06397a0314369e
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053158"
---
# <a name="cost-model-for-azure-netapp-files"></a>Kostenmodell für Azure NetApp Files 

Das Verstehen des Kostenmodells für Azure NetApp Files hilft Ihnen bei der Verwaltung von auf den Dienst bezogenen Ausgaben. 

Informationen zu speziellen Kostenmodellen für die regionsübergreifende Replikation finden Sie unter [Kostenmodell für die regionsübergreifende Replikation](cross-region-replication-introduction.md#cost-model-for-cross-region-replication).

## <a name="calculation-of-capacity-consumption"></a>Berechnung der Kapazitätsnutzung

Azure NetApp Files wird nach bereitgestellter Speicherkapazität abgerechnet, die durch das Erstellen von Kapazitätspools zugeordnet wird. Kapazitätspools werden monatlich auf Grundlage der festgelegten Kosten pro zugeordnetem GiB pro Stunde abgerechnet. Die Kapazitätspoolzuordnung wird stündlich gemessen.  

Kapazitätspools müssen mindestens 4 TiB groß sein und können in Intervallen von 1 TiB vergrößert oder verkleinert werden. Kapazitätspools enthalten Volumes mit einer Größe von mindestens 100 GiB bis maximal 100 TiB. Volumes werden Kontingente zugewiesen, die von der bereitgestellten Größe des Kapazitätspools abgezogen werden. Bei einem aktiven Volume basiert die auf das Kontingent bezogene Kapazitätsnutzung auf der logischen (effektiven) Kapazität, wobei es sich um aktive Dateisystemdaten oder Momentaufnahmedaten handelt. Weitere Informationen finden Sie unter [Funktionsweise von Azure NetApp Files-Momentaufnahmen](snapshots-introduction.md). 

### <a name="pricing-examples"></a>Preisbeispiele

In diesem Abschnitt finden Sie Beispiele, die Ihnen helfen, das Kostenmodell von Azure NetApp Files zu verstehen.

#### <a name="example-1-one-month-cost-with-static-versus-dynamic-capacity-pool-provisioning"></a>Beispiel 1: Kosten für einen Monat bei statischer gegenüber dynamischer Kapazitätspoolbereitstellung 

Wenn die Größenanforderungen Ihres Kapazitätspools schwanken (z. B. aufgrund variabler Kapazitäts- oder Leistungsanforderungen), sollten Sie eine [dynamische Größenänderung Ihrer Volumes und Kapazitätspools](azure-netapp-files-resize-capacity-pools-or-volumes.md) in Betracht ziehen, um die Kosten mit den Kapazitäts- und Leistungsanforderungen ins Gleichgewicht zu bringen.

Angenommen, Sie verwenden beispielsweise die Premium-Kapazität 24 Stunden (1 Tag) mit 10 TiB, 96 Stunden (4 Tage) mit 24 TiB, viermal mit 6 Stunden (1 Tag) mit 5 TiB, 480 Stunden (20 Tage) mit 6 TiB und die verbleibenden Stunden des Monats mit 0 TiB. Ein dynamisches Bereitstellungsprofil für die Cloudnutzung unterscheidet sich von einem herkömmlichen statischen, lokalen Nutzungsprofil: 

[ ![Balkendiagramm mit dynamischer gegenüber statischer Kapazitätspoolbereitstellung.](../media/azure-netapp-files/cost-model-example-one-capacity.png) ](../media/azure-netapp-files/cost-model-example-one-capacity.png#lightbox)

Wenn Kosten von 0,000403 US-Dollar pro GiB/Stunde abgerechnet werden ([Preise sind regionsabhängig](https://azure.microsoft.com/pricing/details/netapp/)), sieht die monatliche Kostenaufschlüsselung wie folgt aus:

*Statische Bereitstellung auf Premium (Spitzenkapazität/-leistung)*

* 24 TiB x 720 Stunden x 0,000403 USD pro GiB/Stunde = 7.130,97 USD pro Monat (237,70 USD pro Tag) 

*Dynamische Bereitstellung mit Größenänderung von Volumes und Kapazitätspools* 

* 10 TiB x 24 Stunden x 0,000403 USD pro GiB/Stunde = 99,04 USD
* 24 TiB x 96 Stunden x 0,000403 USD pro GiB/Stunde = 950,80 USD
* 6 TiB x 480 Stunden x 0,000403 USD pro GiB/Stunde = 1.188,50 USD
* Gesamt = **2.238,33 USD**

[ ![Balkendiagramm mit statischem gegenüber dynamischem Servicelevel-Kostenmodell.](../media/azure-netapp-files/cost-model-example-one-pricing.png) ](../media/azure-netapp-files/cost-model-example-one-pricing.png#lightbox)

Dieses Szenario ergibt im Vergleich zur statischen Bereitstellung monatliche Einsparungen von 4.892,64 USD.

#### <a name="example-2-one-month-cost-with-and-without-dynamic-service-level-change"></a>Beispiel 2: Kosten für einen Monat mit und ohne dynamische Änderung des Servicelevels

Wenn Ihre Anforderungen an die Größe des Kapazitätspools gleich bleiben, aber die Anforderungen an die Leistung schwanken, sollten Sie erwägen, [den Servicelevel eines Volumes dynamisch zu ändern](dynamic-change-volume-service-level.md). Sie können im Laufe des Monats Kapazitätspools unterschiedlicher Typen bereitstellen bzw. deren Bereitstellung aufheben, um so Just-In-Time-Leistung bereitzustellen und die Kosten in Zeiträumen zu senken, während derer keine Leistung erforderlich ist. 

Stellen Sie sich ein Szenario vor, in dem die Kapazitätsanforderung konstant 24 TiB beträgt. Ihre Leistungsanforderungen schwanken aber zwischen 384 Stunden (16 Tage) des Standard-Servicelevels, 120 Stunden (5 Tage) des Premium-Servicelevels, 168 Stunden (7 Tage) des Ultra-Servicelevels und dann wieder zurück auf 48 Stunden (2 Tage) der Leistung des Standard-Servicelevels. In diesem Szenario unterscheidet sich ein dynamisches Bereitstellungsprofil für die Cloudnutzung gegenüber einem herkömmlichen statischen, lokalen Nutzungsprofil: 

[ ![Balkendiagramm, das die Bereitstellung mit und ohne dynamische Änderung des Servicelevels zeigt.](../media/azure-netapp-files/cost-model-example-two-capacity.png) ](../media/azure-netapp-files/cost-model-example-two-capacity.png#lightbox)

In diesem Fall, wenn die Kosten mit 0,000202 US-Dollar pro GiB/Stunde (Standard), 0,000403 US-Dollar pro GiB/Stunde (Premium) bzw. 0,000538 US-Dollar pro GiB/Stunde (Ultra) ([Preise sind regionsabhängig](https://azure.microsoft.com/pricing/details/netapp/)) abgerechnet werden, sieht die monatliche Kostenaufschlüsselung wie folgt aus: 

*Statische Bereitstellung auf Ultra-Servicelevel (Spitzenleistung)*

* 24 TiB x 720 Stunden x 0,000538 USD pro GiB/Stunde = 9.519,76 USD pro Monat (317,33 USD pro Tag) 
 
*Dynamische Bereitstellung mit dynamischer Änderung des Servicelevels*

* 24 TiB x 384 Stunden x 0,000202 USD pro GiB/Stunde = 1.901,31 USD  
* 24 TiB x 120 Stunden x 0,000403 USD pro GiB/Stunde = 1.188,50 USD  
* 24 TiB x 168 Stunden x 0,000538 USD pro GiB/Stunde = 2.221,28 USD  
* 24 TiB x 48 Stunden x 0,000202 USD pro GiB/Stunde = 238,29 USD 
* Gesamt = **5.554,37 USD** 

[ ![Balkendiagramm mit Kostenmodell für statische gegenüber dynamischer Serviceleveländerung.](../media/azure-netapp-files/cost-model-example-two-pricing.png) ](../media/azure-netapp-files/cost-model-example-two-pricing.png#lightbox)

Dieses Szenario ergibt im Vergleich zur statischen Bereitstellung monatliche Einsparungen von 3.965,39 USD.

## <a name="capacity-consumption-of-snapshots"></a>Kapazitätsnutzung von Momentaufnahmen 

Die Kapazitätsnutzung von Momentaufnahmen in Azure NetApp Files wird mit dem Kontingent des übergeordneten Volumes verrechnet.  Daher gilt der gleiche Abrechnungstarif wie für den Kapazitätspool, zu dem das Volume gehört.  Im Gegensatz zum aktiven Volume wird die Nutzung von Momentaufnahmen jedoch anhand der genutzten inkrementellen Kapazität gemessen.  Azure NetApp Files-Momentaufnahmen sind standardmäßig differenziell. Je nach Änderungsrate der Daten belegen die Momentaufnahmen oft deutlich weniger Kapazität als die logische Kapazität des aktiven Volumes. Nehmen wir zum Beispiel an, dass Sie eine Momentaufnahme eines 500-GiB-Volumes haben, das nur 10 GiB differenzieller Daten enthält. 

Die Kapazitätsnutzung, die auf das Volumekontingent für das aktive Dateisystem und die Momentaufnahme angerechnet wird, beträgt 510 GiB, nicht 1.000 GiB. Sie können im Allgemeinen davon ausgehen, dass für die Speicherung der Momentaufnahmedaten einer Woche eine empfohlene Kapazität von 20 Prozent beansprucht wird (abhängig von der Momentaufnahmehäufigkeit und den täglichen Änderungsraten auf Blockebene der Anwendung). 

Das folgende Diagramm veranschaulicht die Konzepte. 

* Angenommen, es wird ein Kapazitätspool mit einer bereitgestellten Kapazität von 40 TiB verwendet. Der Pool enthält drei Volumes:    
    * Volume 1 hat ein Kontingent von 20 TiB bei einer Nutzung von 13 TiB (12 TiB aktiv, 1 TiB Momentaufnahmen).
    * Volume 2 hat ein Kontingent von 1 TiB bei einer Nutzung von 450 GiB.
    * Volume 3 hat ein Kontingent von 14 TiB bei einer Nutzung von 8,8 TiB (8 TiB aktiv, 800 GiB Momentaufnahmen).   
* Der Kapazitätspool hat eine bereitgestellte Größe von 40 TiB. 22,25 TiB der Kapazität werden genutzt (13 TiB, 450 GiB und 8,8 TiB der Kontingente der Volumes 1, 2 und 3). Der Kapazitätspool verfügt noch über eine Kapazität von 17,75 TiB.   

[ ![Diagramm eines Kapazitätspools mit drei Volumes.](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png) ](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png#lightbox)

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
* [Preisrechner für Azure NetApp Files](https://azure.microsoft.com/pricing/calculator/?service=netapp)
