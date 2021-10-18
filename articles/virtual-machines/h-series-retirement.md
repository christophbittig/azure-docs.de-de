---
title: Ausmusterung der H-Serie
description: Die Einstellung der H-Serie beginnt am 1. September 2021.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 1ad9a9384d00ec0961235c167379d9984ec417fe
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705248"
---
# <a name="migrate-your-h-and-h-series-promo-virtual-machines-by-august-31-2022"></a>Migrieren Sie Ihre VMs der H- und H-Promo-Serie bis zum 31. August 2022.

Microsoft Azure hat neue Generationen von virtuellen HPC- (High-Performance-Computing), universellen und speicheroptimierten Computern (VMs) eingeführt. Aus diesem Grund wird empfohlen, Workloads von den ursprünglichen VMs der H- und H-Promo-Serie zu unseren neueren Angeboten zu migrieren.

Azure [HC](hc-series.md)-, [HBv2](hbv2-series.md)-, [HBv3](hbv3-series.md)-, [Dv4](dv4-dsv4-series.md)-, [Dav4](dav4-dasv4-series.md)-, [Ev4](ev4-esv4-series.md)- und [Eav4](eav4-easv4-series.md)-VMs verfügen über eine größere Speicherbandbreite, verbesserte Netzwerkfunktionen und bessere Kosten und Leistung bei verschiedenen HPC-Workloads. Am 31. August 2022 stellen wir die folgenden Azure-VM-Größen der H-Serie ein:

- H8
- H8m
- H16
- H16r
- H16m
- H16mr
- H8 Promo
- H8m Promo
- H16 Promo
- H16r Promo
- H16m Promo
- H16mr Promo

## <a name="how-does-the-h-series-migration-affect-me"></a>Wie wirkt sich die Migration der H-Serie auf mich aus?

Nach dem 31. August 2022 werden alle verbleibenden VM-Abonnements der H-Serie in der vorherigen Liste auf den Zustand „Nicht zugewiesen“ festgelegt. Sie funktionieren dann nicht mehr, und es fallen keine Gebühren mehr an.

Die aktuelle Einstellung von VM-Größen wirkt sich nur auf die VM-Größen in der [H-Serie](h-series.md) aus, einschließlich der H-Promo-Serie.

## <a name="what-actions-should-i-take"></a>Welche Aktionen zieht das für mich nach sich?

Sie müssen die Größe Ihrer VMs der H-Serie ändern oder deren Zuordnung aufheben. Es wird empfohlen, Workloads von den ursprünglichen VMs der H- und H-Promo-Serie zu unseren neueren Angeboten zu migrieren.

**HPC-Workloads**: [HC](hc-series.md)-, [HBv2](hbv2-series.md)- und [HBv3](hbv3-series.md)-VMs bieten ein wesentlich höheres Niveau an HPC-Workloadleistung und Kosteneffizienz aufgrund der folgenden Faktoren:

- Umfangreiche Verbesserungen bei der CPU-Kernarchitektur.
- Höhere Arbeitsspeicherbandbreite.
- Größere L3-Caches.
- Verbesserte Hardware- und Softwareunterstützung für InfiniBand-Netzwerke im Vergleich zur H-Serie.

Daher bieten die HC-, HBv2- und HBv3-Serien im Allgemeinen eine wesentlich bessere Leistung pro Kosteneinheit (Maximierung der Leistung für einen festen Betrag) und Kosten pro Leistung (Minimierung der Kosten für einen festen Betrag).

**Universelle Workloads**: [Dv4](dv4-dsv4-series.md)-, [Dav4](dav4-dasv4-series.md)- und Dv5-VMs bieten dieselbe oder eine bessere CPU-Leistung bei gleicher oder größerer Kernanzahl, eine vergleichbare Menge an Arbeitsspeicher pro physischem CPU-Kern, bessere Azure-Netzwerkfunktionen und niedrigere Gesamtkosten.

**Speicheroptimierte Workloads**: [Ev4](ev4-esv4-series.md)-, [Eav4](eav4-easv4-series.md)- und Ev5-VMs bieten die gleiche oder eine bessere CPU-Leistung bei identischer oder größerer Kernanzahl, eine vergleichbare Menge an Arbeitsspeicher pro physischem CPU-Kern, bessere Azure-Netzwerkfunktionen und niedrigere Gesamtkosten.

VMs der [H-Serie](h-series.md) und der H-Promo-Serie werden erst im September 2022 eingestellt. Wir stellen diesen Leitfaden im Voraus bereit, um Ihnen ein großes Zeitfenster für die Bewertung, Planung und Durchführung Ihrer Migration zu ermöglichen.

### <a name="migration-steps"></a>Schritte bei der Migration

1. Wählen Sie eine Reihe und eine Größe für die Migration aus.
1. Erhalten Sie ein Kontingent für die Ziel-VM-Serie.
1. Ändern Sie die aktuelle Größe der VM der H-Serie in die Zielgröße.

### <a name="breaking-changes"></a>Aktuelle Änderungen

Wenn Sie VM-Größen der H-Serie verwenden, die über eine InfiniBand-Netzwerkschnittstelle verfügen (z. B. die Größen mit einem „r“ im Namen der VM-Größe), und Sie möchten, dass Ihre neuen VM-Größen auch InfiniBand-Netzwerke unterstützen, können Sie keine Legacy-Betriebssystemimages mit integrierter InfiniBand-Treiberunterstützung mehr verwenden (CentOS 7.4 und früher, Windows Server 2012).

Verwenden Sie stattdessen moderne Betriebssystemimages, wie sie in Azure Marketplace verfügbar sind, die moderne Betriebssysteme (CentOS 7.5 oder höher, Windows Server 2016 oder höher) und OFED-Standardtreiber unterstützen. Weitere Informationen finden Sie unter [Unterstützter Softwarestapel](hbv3-series.md#get-started), einschließlich unterstützter Betriebssysteme für die jeweiligen VM-Größen.

### <a name="get-a-quota-for-the-target-vm-family"></a>Erhalten eines Kontingents für die VM-Zielfamilie

Befolgen Sie die Anleitung zum [Anfordern einer Erhöhung des vCPU-Kontingents nach VM-Familie](../azure-portal/supportability/per-vm-quota-requests.md).

### <a name="resize-the-current-vm"></a>Ändern der Größe der aktuellen VM

Sie können [die Größe des Virtuellen Computers ändern](resize-vm.md).
