---
title: Ausmusterung der HB-Serie
description: Die Einstellung der HB-Serie beginnt am 1. September 2021.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: f582aad0b5af8053ff7accfac2917d4d5191ce5d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712154"
---
# <a name="migrate-your-hb-series-virtual-machines-by-august-31-2024"></a>Migrieren Sie Ihre virtuellen Computer der HB-Serie bis zum 31. August 2024

Microsoft Azure hat virtuelle Computer (VMs) der HBv2- und HBv3-Serie für High Performance Computing (HPC) eingeführt. Aus diesem Grund wird empfohlen, Workloads von ursprünglichen VMs der HB-Serie zu unseren neueren Angeboten zu migrieren.

Azure [HBv2](hbv2-series.md)- und [HBv3](hbv3-series.md)-VMs verfügen über eine größere Speicherbandbreite, verbesserte RDMA-Netzwerkfunktionen (Remote Direct Memory Access), größere und schnellere lokale Solid State Drives sowie ein besseres Preis-Leistungs-Verhältnis bei einer Vielzahl von HPC-Workloads. Daher stellen wir unsere Azure-VM-Größen der HB-Serie am 31. August 2024 ein.

## <a name="how-does-the-hb-series-migration-affect-me"></a>Wie wirkt sich die Migration der HB-Serie auf mich aus?

Nach dem 31. August 2024 werden alle verbleibenden VM-Abonnements der HB-Größe auf den Zustand „Nicht zugewiesen“ festgelegt. Sie funktionieren dann nicht mehr, und es fallen keine Gebühren mehr an.

> [!NOTE]
> Die aktuelle Einstellung von VM-Größen wirkt sich nur auf die VM-Größen in der HB-Serie aus. Diese Einstellungsankündigung gilt nicht für die neueren VMs der HBv2-, HBv3- und HC-Serie.

## <a name="what-actions-should-i-take"></a>Welche Aktionen zieht das für mich nach sich?

Sie müssen die Größe Ihrer VMs der H-Serie ändern oder deren Zuordnung aufheben. Es wird empfohlen, Workloads von den ursprünglichen VMs der H- und H-Promo-Serie zu unseren neueren Angeboten zu migrieren.

[HBv2](hbv2-series.md)- und [HBv3](hbv3-series.md)-VMs bieten ein wesentlich höheres Niveau an HPC-Workloadleistung und Kosteneffizienz aufgrund der folgenden Faktoren:

- Umfangreiche Verbesserungen bei der CPU-Kernarchitektur.
- Höhere Arbeitsspeicherbandbreite.
- Größere L3-Caches.
- Verbesserte InfiniBand-Netzwerke im Vergleich zur HB-Serie.

Daher bieten die HBv2- und HBv3-Serien im Allgemeinen eine wesentlich bessere Leistung pro Kosteneinheit (Maximierung der Leistung für einen festen Betrag) und Kosten pro Leistung (Minimierung der Kosten für einen festen Betrag).

Alle Regionen, die VMs der HB-Serie enthalten, enthalten VMs der HBv2- und HBv3-Serie. Vorhandene Workloads, die auf VMs der HB-Serie ausgeführt werden, können migriert werden, ohne dass die geografische Platzierung oder der Zugriff auf weitere Dienste in diesen Regionen berücksichtigt werden muss.

VMs der [HB-Serie](hb-series.md) werden erst im September 2024 eingestellt. Wir stellen diesen Leitfaden im Voraus bereit, um Ihnen ein großes Zeitfenster für die Bewertung, Planung und Durchführung Ihrer Migration zu ermöglichen.

### <a name="recommendations-for-workload-migration-from-hb-series-vms"></a>Empfehlungen zur Workloadmigration von VMs der HB-Serie

| Aktuelle VM-Größe | Größe der Ziel-VM | Unterschied bei der Spezifikation  |
|---|---|---|
|Standard_HB60rs |Standard_HB120rs_v2 <br> Standard_HB120rs_v3 <br> Standard_HB120-64rs_v3 |Neuere CPU: AMD Rome und MiIan (+20–30 % IPC) <br> Arbeitsspeicher: Bis zu 2x mehr RAM  <br> Arbeitsspeicherbandbreite: Bis zu 30 % mehr Arbeitsspeicherbandbreite <br> InfiniBand: 200 Gb HDR (2x höhere Bandbreite) <br> Max. Datenträger: bis zu 32 (+8x) |
|Standard_HB60-45rs |Standard_HB120-96rs_v3 <br> Standard_HB120-64rs_v3 <br> Standard_HB120-32rs_v3 |Neuere CPU: AMD Rome und MiIan (+20–30 % IPC) <br> Arbeitsspeicher: Bis zu 2x mehr RAM  <br>  Arbeitsspeicherbandbreite: Bis zu 30 % mehr Arbeitsspeicherbandbreite <br> InfiniBand: 200 Gb HDR (2x höhere Bandbreite) <br> Max. Datenträger: bis zu 32 (+8x) |
|Standard_HB60-30rs |Standard_HB120-32rs_v3 <br> Standard_HB120-16rs_v3 |Neuere CPU: AMD Rome und MiIan (+20–30 % IPC) <br> Arbeitsspeicher: Bis zu 2x mehr RAM <br> Arbeitsspeicherbandbreite: Bis zu 30 % mehr Arbeitsspeicherbandbreite <br> InfiniBand: 200 Gb HDR (2x höhere Bandbreite) <br> Max. Datenträger: bis zu 32 (+8x) |
|Standard_HB60-15rs |Standard_HB120-16rs_v3 |Neuere CPU: AMD Rome und MiIan (+20–30 % IPC) <br> Arbeitsspeicher: Bis zu 2x mehr RAM <br> Arbeitsspeicherbandbreite: Bis zu 30 % mehr Arbeitsspeicherbandbreite <br> InfiniBand: 200 Gb HDR (2x höhere Bandbreite) <br> Max. Datenträger: bis zu 32 (+8x) |

### <a name="migration-steps"></a>Schritte bei der Migration

1. Wählen Sie eine Reihe und eine Größe für die Migration aus.
1. Erhalten Sie ein Kontingent für die Ziel-VM-Serie.
1. Ändern Sie die aktuelle Größe der VM der HB-Serie in die Zielgröße.

### <a name="get-a-quota-for-the-target-vm-family"></a>Erhalten eines Kontingents für die VM-Zielfamilie

Befolgen Sie die Anleitung zum [Anfordern einer Erhöhung des vCPU-Kontingents nach VM-Familie](../azure-portal/supportability/per-vm-quota-requests.md).

### <a name="resize-the-current-vm"></a>Ändern der Größe der aktuellen VM

Sie können [die Größe des Virtuellen Computers ändern](resize-vm.md).
