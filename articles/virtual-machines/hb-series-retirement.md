---
title: Auslaufen der HB-Serie
description: Das Auslaufen der HB-Serie beginnt am 1. September 2021
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 2d14b9b3adb9905cc87408db33018746e1479a1b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128680089"
---
# <a name="migrate-your-hb-series-virtual-machines-by-august-31-2024"></a>Migrieren Sie Ihre virtuellen Computer der HB-Serie bis zum 31. August 2024
Da Microsoft Azure virtuelle Computer der HBv2- und HBv3-Serie für High Performance Computing (HPC) eingeführt haben, empfehlen wir die Migration von Workloads von ursprünglichen virtuellen Computern der HB-Serie zu unseren neueren Angeboten.  

Virtuelle Azure [HBv2](hbv2-series.md) und [HBv3-Computer](hbv3-series.md) verfügen über eine größere Speicherbandbreite, verbesserte RDMA-Netzwerkfunktionen (Remote Direct Memory Access), größere und schnellere lokale Solid-State-Festplatten sowie ein besseres Preis-Leistungs-Verhältnis bei einer Vielzahl von HPC-Workloads. Daher lassen wir unsere Azure Virtual Machine-Größen der HB-Serie am 31. August 2024 auslaufen.

## <a name="how-does-the-hb-series-migration-affect-me"></a>Wie wirkt sich die Migration der HB-Serie auf mich aus?  

Nach dem 31. August 2024 werden alle verbleibenden Abonnements für virtuelle Maschinen der Größe HB in den Status "freigegeben" gesetzt, funktionieren nicht mehr und führen zu keinen Abrechnungsgebühren mehr.  
> [!NOTE]
> Das aktuelle Auslaufen von VM-Größen wirkt sich nur auf die VM-Größen in der NV-Serie aus. Diese Auslauf-Ankündigung gilt nicht für die neueren virtuellen Computer der HBv2-, HBv3- und HC-Serie. 

## <a name="what-actions-should-i-take"></a>Welche Aktionen zieht das für mich nach sich?  

Sie müssen die Größe Ihrer VMs der H-Serie ändern oder sie freigeben. Es wird empfohlen, Workloads von ursprünglichen VMs der H-Serie (einschließlich der H-Serie-Promo) zu unseren neueren Angeboten zu migrieren.

[HBv2](hbv2-series.md)- und [HBv3](hbv3-series.md)-VMs bieten im Vergleich zur H-Serie erheblich höhere Leistungs- und Kosteneffizienz für HPC-Workloads aufgrund umfangreicher Verbesserungen der CPU-Kernarchitektur, einer höheren Arbeitsspeicherbandbreite, größerer L3-Caches und der verbesserten InfiniBand-Netzwerkhardware. Daher werden die HBv2- und HBv3-Serien im Allgemeinen eine wesentlich bessere Leistung pro Kosteneinheit (Maximierung der Leistung für einen festen Ausgabenbetrag) und Kosten pro Leistung (Minimierung der Kosten für eine festen Leistungsmenge) bieten.

Alle Regionen, die VMs der HB-Serie enthalten, enthalten auch virtuelle Computer der HBv2- und HBv3-Serie, sodass vorhandene Workloads, die auf virtuellen Computern der HB-Serie ausgeführt werden, ohne Bedenken bezüglich der geografischen Platzierung oder des Zugriffs auf zusätzliche Dienste in diesen Regionen migriert werden können. 

VMs der [HB-Serie](hb-series.md) laufen erst im September 2024 aus. Daher stellen wir diese Führungslinie im Voraus bereit, um Kunden ein großes Zeitfenster zum Bewerten, Planen und Ausführen ihrer Migration zu geben. 

### <a name="recommendations-for-workload-migration-from-hb-series-virtual-machines"></a>Empfehlungen zur Workloadmigration von Virtuellen Computern der HB-Serie 

| Aktuelle VM-Größe | Zielgröße des virtuellen Computers | Unterschied bei der Spezifikation  |
|---|---|---|
|Standard_HB60rs |Standard_HB120rs_v2 <br> Standard_HB120rs_v3 <br> Standard_HB120-64rs_v3 |Neuere CPU: AMD Rome und MiIan (+20–30 % IPC) <br> Arbeitsspeicher: Bis zu 2x mehr RAM  <br> Arbeitsspeicherbandbreite: Bis zu 30 % mehr Arbeitsspeicherbandbreite <br> InfiniBand: 200 Gb HDR (2x höhere Bandbreite) <br> Max. Datenträger: bis zu 32 (+8x) |
|Standard_HB60-45rs |Standard_HB120-96rs_v3 <br> Standard_HB120-64rs_v3 <br> Standard_HB120-32rs_v3 |Neuere CPU: AMD Rome und MiIan (+20–30 % IPC) <br> Arbeitsspeicher: Bis zu 2x mehr RAM  <br>  Arbeitsspeicherbandbreite: Bis zu 30 % mehr Arbeitsspeicherbandbreite <br> InfiniBand: 200 Gb HDR (2x höhere Bandbreite) <br> Max. Datenträger: bis zu 32 (+8x) |
|Standard_HB60-30rs |Standard_HB120-32rs_v3 <br> Standard_HB120-16rs_v3 |Neuere CPU: AMD Rome und MiIan (+20–30 % IPC) <br> Arbeitsspeicher: Bis zu 2x mehr RAM <br> Arbeitsspeicherbandbreite: Bis zu 30 % mehr Arbeitsspeicherbandbreite <br> InfiniBand: 200 Gb HDR (2x höhere Bandbreite) <br> Max. Datenträger: bis zu 32 (+8x) |
|Standard_HB60-15rs |Standard_HB120-16rs_v3 |Neuere CPU: AMD Rome und MiIan (+20–30 % IPC) <br> Arbeitsspeicher: Bis zu 2x mehr RAM <br> Arbeitsspeicherbandbreite: Bis zu 30 % mehr Arbeitsspeicherbandbreite <br> InfiniBand: 200 Gb HDR (2x höhere Bandbreite) <br> Max. Datenträger: bis zu 32 (+8x) |


### <a name="migration-steps"></a>Schritte bei der Migration 
1. Wählen Sie eine Reihe und eine Größe für die Migration aus. 
2. Erhalten Sie das Kontingent für die Ziel-VM-Serie 
3. Ändern Sie die aktuelle Größe der VM der HB-Serie auf die Zielgröße 


### <a name="get-quota-for-the-target-vm-family"></a>Erhalten Sie das Kontingent für die Ziel-VM-Familie 

Befolgen Sie die Anleitung zum [Anfordern einer Erhöhung des vCPU-Kontingents nach VM-Familie](../azure-portal/supportability/per-vm-quota-requests.md).


### <a name="resize-the-current-virtual-machine"></a>Ändern Sie die Größe des aktuellen VM.
Sie können [die Größe des Virtuellen Computers ändern](resize-vm.md).
