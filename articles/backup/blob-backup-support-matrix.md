---
title: Supportmatrix für Azure-Blobsicherung
description: Dieser Artikel bietet eine Zusammenfassung der Unterstützungseinstellungen und Einschränkungen bei der Sicherung von Azure-Blobs.
ms.topic: conceptual
ms.date: 10/07/2021
ms.custom: references_regions
ms.openlocfilehash: d0b524ba2865aa72f6fce88e326fffde435f71c3
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714161"
---
# <a name="support-matrix-for-azure-blobs-backup"></a>Supportmatrix für Azure-Blobsicherung

In diesem Artikel erhalten Sie einen Überblick über die regionale Verfügbarkeit, die unterstützten Szenarien und die Einschränkungen der operativen Sicherung von Blobs.

## <a name="supported-regions"></a>Unterstützte Regionen

Die operative Sicherung für Blobs ist in allen öffentlichen Cloudregionen verfügbar, mit Ausnahme von „Frankreich, Süden“ und „Südafrika, Westen“. Sie ist auch in Sovereign Cloud-Regionen verfügbar – allen Azure Government-Regionen und China-Regionen (mit Ausnahme von „China, Osten“).

## <a name="limitations"></a>Einschränkungen

Die operative Sicherung von Blobs verwendet die Zeitpunktwiederherstellung von Blobs, Blobversionierung, vorläufiges Löschen für Blobs, einen Änderungsfeed für Blobs und eine Löschsperre, um eine lokale Sicherungslösung bereitzustellen. Daher gelten Einschränkungen dieser einzelnen Funktionen auch für die operative Sicherung.

**Unterstützte Szenarien:** Die operative Sicherung unterstützt nur Blockblobs in Standardspeicherkonten vom Typ „Allgemein v2“. Storage Konten mit aktivierten hierarchischen Namespaces (d. h. ADLS Gen2-Konten) werden nicht unterstützt.   <br><br>   Außerdem werden Seitenblobs, Anfügeblobs und Premium-Blobs in Ihrem Speicherkonto nicht wiederhergestellt. Nur Blockblobs werden wiederhergestellt.

**Weitere Einschränkungen:**

- Wenn Sie während der Beibehaltungsdauer einen Container gelöscht haben, wird dieser Container bei einer Zeitpunktwiederherstellung nicht wiederhergestellt. Bei dem Versuch, einen Bereich von Blobs wiederherzustellen, der Blobs in einem gelöschten Container beinhaltet, tritt bei der Zeitpunktwiederherstellung ein Fehler auf. Weitere Informationen zum Schutz von Containern vor dem Löschen finden Sie unter [Vorläufiges Löschen für Container (Vorschau)](../storage/blobs/soft-delete-container-overview.md).
- Wenn ein Blob innerhalb des Zeitraums zwischen dem aktuellen Moment und dem Wiederherstellungspunkt zwischen den heißen und kalten Speicherebenen verschoben wurde, wird das Blob auf der vorherigen Ebene wiederhergestellt. Das Wiederherstellen von Blockblobs auf Archivebene wird nicht unterstützt. Wenn ein Blob aus der heißen Speicherebene beispielsweise vor zwei Tagen in die Archivebene verschoben wurde und ein Wiederherstellungsvorgang für einen Zeitpunkt vor drei Tagen erfolgt, wird das Blob nicht in der heißen Zugriffsebene wiederhergestellt. Um ein archiviertes Blob wiederherzustellen, verschieben Sie es zuerst aus der Archivebene. Weitere Informationen finden Sie unter [Aktivieren von Blobdaten aus der Archivspeicherebene](../storage/blobs/archive-rehydrate-overview.md).
- Ein Block, der über [Put Block](/rest/api/storageservices/put-block) oder [Put Block from URL](/rest/api/storageservices/put-block-from-url) hochgeladen, aber nicht über [Put Block List](/rest/api/storageservices/put-block-list) committet wurde, ist nicht Teil eines Blobs und wird daher nicht als Teil eines Wiederherstellungsvorgangs wiederhergestellt.
- Ein Blob mit einer aktiven Leasedauer kann nicht wiederhergestellt werden. Wenn ein Blob mit einer aktiven Leasedauer in den Bereich der wiederherzustellenden Blobs eingeschlossen ist, schlägt der Wiederherstellungsvorgang automatisch fehl. Unterbrechen Sie alle aktiven Leases, bevor Sie den Wiederherstellungsvorgang starten.
- Momentaufnahmen werden im Rahmen eines Wiederherstellungsvorgangs nicht erstellt oder gelöscht. Nur das Basisblob wird in seinem vorherigen Zustand wiederhergestellt.
- Wenn die wiederhergestellten Blobs [unveränderliche Blobs](../storage/blobs/immutable-storage-overview.md#about-immutable-storage-for-blobs) enthalten, werden diese unveränderlichen Blobs nicht in ihrem Zustand gemäß dem ausgewählten Wiederherstellungspunkt wiederhergestellt. Andere Blobs, die nicht als unveränderlich konfiguriert sind, werden jedoch wie erwartet am ausgewählten Wiederherstellungspunkt wiederhergestellt.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über die operative Sicherung für Azure-Blobs](blob-backup-overview.md)