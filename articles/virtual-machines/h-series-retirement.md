---
title: Ausmusterung der H-Serie
description: Die Ausmusterung der H-Serie beginnt am 1. September 2021
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 023fb8a352e1e02023fe0ce16afba9531625e370
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515962"
---
# <a name="migrate-your-h-and-h_promo-series-virtual-machines-by-august-31-2022"></a>Migrieren Sie Ihre VM der H- und H_Promo-Serie bis zum 31. August 2022
Da Microsoft Azure neuere Generationen von high Performance Computing (HPC), universelle und speicheroptimierte VMs eingeführt hat, empfehlen wir, Workloads von ursprünglichen VMs der H-Serie (einschließlich H-Serie-Promo) zu unseren neueren Angeboten zu migrieren.

Azure [HC-,](hc-series.md)[HBv2-,](hbv2-series.md)[HBv3-,](hbv3-series.md)[Dv4-,](dv4-dsv4-series.md)[Dav4-,](dav4-dasv4-series.md)[Ev4-](ev4-esv4-series.md) und [Eav4-VMs](eav4-easv4-series.md) verfügen über eine höhere Arbeitsspeicherbandbreite, verbesserte Netzwerkfunktionen und bessere Kosten/Leistung für eine Vielzahl von HPC-Workloads. Aus diesem Grund werden wir die Größen unserer Azure VMs der H-Serie (H8, H8m, H16, H16r, H16m, H16mr, H8 Promo, H8m Promo, H16 Promo, H16r Promo, H16m Promo und H16mr Promo) am 31. August 2022 ausmustern.

## <a name="how-does-the-h-series-migration-affect-me"></a>Wie wirkt sich die Migration der H-Serie auf mich aus?  

Nach dem 31. August 2022 werden alle verbleibenden oben genannten Abonnements für VMs der H-Serie auf den Status „Zuordnung aufgehoben“ gesetzt, funktionieren nicht mehr und es fallen keine Abrechnungsgebühren mehr dafür an. 

Die aktuelle Ausmusterung von VM-Größen wirkt sich nur auf die VM-Größen in der [H-Serie](h-series.md) aus, einschließlich der H-Serie-Promo. 

## <a name="what-actions-should-i-take"></a>Welche Aktionen zieht das für mich nach sich?  

Sie müssen die Größe Ihrer VMs der H-Serie ändern oder sie freigeben. Es wird empfohlen, Workloads von ursprünglichen VMs der H-Serie (einschließlich der H-Serie-Promo) zu unseren neueren Angeboten zu migrieren.

Für HPC-Workloads bieten [HC-](hc-series.md), [HBv2-](hbv2-series.md) und [HBv3-VMs](hbv3-series.md) aufgrund umfangreicher Verbesserungen der CPU-Kernarchitektur, einer höheren Arbeitsspeicherbandbreite, größerer L3-Caches und der verbesserten InfiniBand-Netzwerkhardware und -softwareunterstützung im Vergleich zur H-Serie erheblich höhere Leistungs- und Kosteneffizienz für HPC-Workloads. Daher bieten die HC-, HBv2- und HBv3-Serien im Allgemeinen eine wesentlich bessere Leistung pro Kosteneinheit (Maximierung der Leistung für einen festen Betrag) und Kosten pro Leistung (Minimierung der Kosten für einen festen Betrag). 

Für Universell Workloads bieten [dv4-](dv4-dsv4-series.md), [Dav4-](dav4-dasv4-series.md) und Dv5-VMs die gleiche oder eine bessere CPU-Leistung bei identischer oder größerer Kernanzahl, eine vergleichbare Menge an Arbeitsspeicher pro physischem CPU-Kern, bessere Azure-Netzwerk Funktionen und niedrigere Gesamtkosten. 

Für arbeitsspeicheroptimierte Workloads bieten [Ev4-](ev4-esv4-series.md), [Eav4-](eav4-easv4-series.md) und Ev5-VMs die gleiche oder eine bessere CPU-Leistung bei identischer oder größerer Kernanzahl, eine vergleichbare Menge an Arbeitsspeicher pro physischem CPU-Kern, bessere Azure-Netzwerk Funktionen und niedrigere Gesamtkosten. 

[H-Serie](h-series.md) VMs (einschließlich der H-Serie Promo) werden erst im September 2022 eingestellt. Daher stellen wir diesen Leitfaden im Voraus bereit, um Kunden ein großes Zeitfenster zum Bewerten, Planen und Ausführen ihrer Migration zu geben. 


### <a name="migration-steps"></a>Schritte bei der Migration 
1. Wählen Sie eine Reihe und eine Größe für die Migration aus. 
2. Erhalten Sie das Kontingent für die Ziel-VM-Serie 
3. Ändern Sie die Größe der aktuellen VM der H-Serie auf die Zielgröße 


### <a name="breaking-changes"></a>Aktuelle Änderungen 
Wenn Sie VM-Größen der H-Serie verwenden, die eine InfiniBand-Netzwerkschnittstelle verfügbar machen (z. B. solche mit einem „r“ im VM-Größennamen), und Sie möchten, dass Ihre neuen VM-Größen auch InfiniBand-Netzwerke unterstützen, können Sie keine Legacy-Betriebssystemimages mehr mit integrierter InfiniBand-Treiberunterstützung (CentOS 7.4 und früher, Windows Server 2012) verwenden. Verwenden Sie stattdessen moderne Betriebssystemimages, z. B. die auf den Azure Marketplace, die moderne Betriebssysteme (CentOS 7.5 und neuer, Windows Server 2016 und neuer) und standardmäßige OFED-Treiber unterstützen. Weitere Informationen finden Sie unter [Unterstützter Softwarestapel](hbv3-series.md#get-started), einschließlich unterstützter Betriebssysteme für die jeweiligen VM-Größen. 


### <a name="get-quota-for-the-target-vm-family"></a>Erhalten Sie das Kontingent für die Ziel-VM-Familie 

Befolgen Sie die Anleitung zum [Anfordern einer Erhöhung des vCPU-Kontingents nach VM-Familie](../azure-portal/supportability/per-vm-quota-requests.md).


### <a name="resize-the-current-virtual-machine"></a>Ändern Sie die Größe des aktuellen VM.
Sie können [die Größe des VM mithilfe von PowerShell](./windows/resize-vm.md), [Azure CLI](./linux/change-vm-size.md) oder über das Azure-Portal ändern.
