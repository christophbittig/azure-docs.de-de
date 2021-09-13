---
title: Redundanzoptionen für verwaltete Azure-Datenträger
description: Erfahren Sie mehr über zonenredundanten Speicher und lokal redundanten Speicher für verwaltete Azure-Datenträger.
author: roygara
ms.author: rogarana
ms.date: 07/12/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 1875f43203735707a1bf49ac4e2d008abf898828
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2021
ms.locfileid: "113664471"
---
# <a name="redundancy-options-for-managed-disks"></a>Redundanzoptionen für verwaltete Datenträger

Verwaltete Azure-Datenträger bieten zwei Optionen für die Speicherredundanz: zonenredundanten Speicher (ZRS) als Vorschau und lokal redundanten Speicher. Zonenredundanter Speicher (ZRS) bietet eine höhere Verfügbarkeit für verwaltete Datenträger als lokal redundanter Speicher (LRS). Allerdings ist die Wartezeit beim Schreiben bei LRS-Datenträgern besser als bei ZRS-Datenträgern, da LRS-Datenträger Daten synchron auf drei Kopien in einem einzelnen Rechenzentrum schreiben.

## <a name="locally-redundant-storage-for-managed-disks"></a>Lokal redundanter Speicher für verwaltete Datenträger

Bei lokal redundantem Speicher (LRS) werden die Daten innerhalb eines einzelnen Rechenzentrums in der ausgewählten Region repliziert. LRS schützt Ihre Daten vor Serverrack- und Laufwerkfehlern. Führen Sie die folgenden Schritte aus, um einen LRS-Datenträger vor einem Zonenausfall wie einer Naturkatastrophe oder anderen Problemen zu schützen:

- Verwenden Sie Anwendungen, die Daten synchron in zwei Zonen schreiben können und im Notfall ein automatisches Failover in eine andere Zone durchführen.
    - Ein Beispiel wäre SQL Server AlwaysOn.
- Führen Sie regelmäßige Sicherungen von LRS-Datenträgern mit ZRS-Momentaufnahmen durch.
- Aktivieren Sie die zonenübergreifende Notfallwiederherstellung für LRS-Datenträger über [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md). Eine zonenübergreifende Notfallwiederherstellung bietet jedoch kein Null-RPO (Recovery Point Objective).

Wenn Ihr Workflow keine zonenübergreifenden synchronen Schreibvorgänge auf Anwendungsebene unterstützt oder Ihre Anwendung ein Null-RPO erfüllen muss, dann wären ZRS-Datenträger ideal.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>Zonenredundanter Speicher für verwaltete Datenträger (Vorschau)

Bei zonenredundantem Speicher (ZRS) wird Ihr verwalteter Azure-Datenträger synchron in drei Azure-Verfügbarkeitszonen in der von Ihnen ausgewählten Region repliziert. Jede Verfügbarkeitszone ist ein getrennter physischer Standort mit unabhängigen Stromversorgungs-, Kühlungs- und Netzwerkgeräten. 

Ein ZRS-Datenträger (Vorschau) ermöglicht die Wiederherstellung nach Ausfällen in Verfügbarkeitszonen. Wenn eine Zone ausgefallen ist, kann ein ZRS-Datenträger an einen virtuellen Computer (VM) in einer anderen Zone angefügt werden. ZRS-Datenträger können auch von VMs gemeinsam genutzt werden, um die Verfügbarkeit mit gruppierten oder verteilten Anwendungen wie SQL FCI, SAP ASCS/SCS oder GFS2 zu verbessern. Ein freigegebener ZRS-Datenträger kann an primäre und sekundäre VMs in verschiedenen Zonen angefügt werden, um sowohl ZRS als auch [Verfügbarkeitszonen](../availability-zones/az-overview.md) zu nutzen. Wenn bei der primären Zone ein Fehler auftritt, können Sie mithilfe von [permanenter SCSI-Reservierung](disks-shared-enable.md#supported-scsi-pr-commands) schnell ein Failover auf den sekundären virtuellen Computer durchführen.

### <a name="billing-implications"></a>Hinweise zur Gebührenberechnung

Details finden Sie auf der Seite [Azure-Preise](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="comparison-with-other-disk-types"></a>Vergleich mit anderen Datenträgertypen

Abgesehen von einer höheren Wartezeit bei Schreibvorgängen sind Datenträger, die zonenredundanten Speicher verwenden, identisch mit Datenträgern, die lokal redundanten Speicher nutzen. Zudem verfügen Sie über die gleichen Skalierungsziele. [Führen Sie Benchmarktests für Ihre Datenträger durch](disks-benchmarks.md), um die Workload Ihrer Anwendung zu simulieren und die Wartezeiten von LRS- und ZRS-Datenträgern zu vergleichen. 

### <a name="limitations"></a>Einschränkungen

[!INCLUDE [disk-storage-zrs-limitations](../../includes/disk-storage-zrs-limitations.md)]

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Erstellen eines ZRS-Datenträgers finden Sie unter [Bereitstellen eines verwalteten ZRS-Datenträgers](disks-deploy-zrs.md).
