---
title: Azure Storage-Explorer – Leitfaden zum vorläufigen Löschen | Microsoft-Dokumentation
description: Vorläufiges Löschen im Azure Storage-Explorer
services: storage
author: JasonYeMSFT
ms.service: storage
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: chuye
ms.openlocfilehash: 0d097e769bfea8ff9c65921e8f0d851372187a02
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128564902"
---
# <a name="azure-storage-explorer-soft-delete-guide"></a>Azure Storage-Explorer – Leitfaden zum vorläufigen Löschen

Vorläufiges Löschen trägt dazu bei, die Auswirkungen des versehentlichen Löschens kritischer Daten zu verringern. Dieser Leitfaden soll Ihnen helfen zu verstehen, wie Sie dieses Feature im Storage-Explorer nutzen können. Bevor Sie fortfahren, sollten Sie mehr über [vorläufiges Löschen von Blobs](../blobs/soft-delete-blob-overview.md) erfahren.

## <a name="configuring-delete-retention-policy"></a>Konfigurieren einer Aufbewahrungsrichtlinie für Löschungen

Sie können die Aufbewahrungsrichtlinie für Löschungen für jedes Speicherkonto im Storage-Explorer konfigurieren. Öffnen Sie das Kontextmenü für einen "Blobcontainer"-Knoten unter dem Speicherkonto, und wählen Sie **Configure Soft Delete Policy...** (Richtlinie für vorläufiges Löschen konfigurieren...) aus.

Das Festlegen einer neuen Richtlinie kann bis zu 30 Sekunden dauern, bis sie wirksam wird. Das Löschen von Daten, ohne darauf zu warten, dass die neue Richtlinie wirksam wird, kann zu unerwartetem Verhalten führen. Der Storage-Explorer wartet 30 Sekunden, bevor eine erfolgreich konfigurierte Richtlinie im Aktivitätsprotokoll gemeldet wird.

## <a name="soft-delete-with-hierarchical-namespace-enabled"></a>Vorläufiges Löschen mit aktivierten hierarchischen Namespaces

Das Feature „Vorläufiges Löschen“ weist grundlegende Unterschiede zwischen Blobcontainern mit oder ohne aktiviertem hierarchischen Namespace (HNS) auf.

Blobcontainer mit HNS-Aktivierung verfügen über wirkliche Verzeichnisse. Diese Verzeichnisse können auch vorläufig gelöscht werden. Wenn ein wirkliches Verzeichnis vorläufig gelöscht wird, kann auf alle aktiven Blobs oder Verzeichnisse darunter nicht mehr zugegriffen werden. Diese Blobs und Verzeichnisse werden wiederhergestellt, wenn das Verzeichnis wiederhergestellt wird, und verworfen, wenn das Verzeichnis abläuft. Blobs oder Verzeichnisse darunter, die bereits vorläufig gelöscht wurden, werden unverändert beibehalten.

Blobcontainer ohne HNS-Aktivierung lassen es nicht zu, dass vorläufig gelöschte Blobs und aktive Blobs mit demselben Namen gleichzeitig vorhanden sind. Wenn Sie ein Blob mit demselben Namen wie ein vorläufig gelöschtes Blob hochladen, wird das vorläufig gelöschte Blob zu einer Momentaufnahme des neuen Blobs. In einem Blobcontainer mit HNS-Aktivierung führt dies dazu, dass das vorläufig gelöschte Blob und das neue Blob gleichzeitig vorhanden sind. Blobcontainer mit HNS-Aktivierung lassen es auch zu, dass mehrere vorläufig gelöschte Blobs mit demselben Namen vorhanden sind.

Jedes vorläufig gelöschte Blob oder Verzeichnis in einem Blobcontainer mit HNS-Aktivierung verfügt über eine `DeletionID`-Eigenschaft. Diese Eigenschaft unterscheidet Blobs oder Verzeichnisse mit demselben Namen. Vorläufig gelöschte Blobs in Blobcontainern ohne HNS-Aktivierung verfügen nicht über eine `DeletionID`-Eigenschaft.

Blobcontainer ohne HNS-Aktivierung unterstützen zudem die „Blobversionsverwaltung“. Wenn die Blobversionsverwaltung aktiviert ist, ändert sich das Verhalten bestimmter Vorgänge, z. B. das Löschen. Weitere Informationen finden Sie unter [Azure Storage-Explorer – Leitfaden zur Blobversionsverwaltung](./storage-explorer-blob-versioning.md).

## <a name="view-soft-deleted-blobs"></a>Anzeigen von vorläufig gelöschten Blobs

Im Blob-Explorer werden vorläufig gelöschte Blobs unter bestimmten Ansichtskontexten angezeigt.

Wählen Sie für Blobcontainer ohne HNS-Aktivierung den Ansichtskontext „Aktive Blobs und vorläufig gelöschte Blobs“ oder „Alle Blobs und Blobs ohne aktuelle Version“ aus, um vorläufig gelöschte Blobs anzuzeigen.

Wählen Sie für Blobcontainer mit HNS-Aktivierung den Ansichtskontext „Aktive und vorläufig gelöschte Blobs“ oder „Nur gelöscht“ aus, um vorläufig gelöschte Blobs und Verzeichnisse anzuzeigen.

## <a name="delete-blobs"></a>Löschen von Blobs

Beim Löschen von Blobs oder Verzeichnissen überprüft der Storage-Explorer die aktuelle Aufbewahrungsrichtlinie für Löschungen des Speicherkontos. Ein Bestätigungsdialogfeld informiert Sie dann darüber, was geschieht, wenn Sie den Löschvorgang fortsetzen. Wenn das vorläufige Löschen deaktiviert ist, können Sie es über das Bestätigungsdialogfeld aktivieren, indem Sie die Schaltfläche **Vorläufiges Löschen aktivieren** auswählen.

> [!WARNING]
> Der Storage-Explorer wird eine neue Aufbewahrungsrichtlinie für Löschungen möglicherweise nicht sehen, wenn Sie sie gerade erst gespeichert haben. Es wird dringend empfohlen, mindestens 30 Sekunden zu warten, bis die neue Richtlinie wirksam wird, bevor Sie Daten löschen.

## <a name="undelete-blobs"></a>Wiederherstellen von Blobs

Der Storage-Explorer kann vorläufig gelöschte Blobs rekursiv und in Batches wiederherstellen.

Wählen Sie zum Wiederherstellen von Blobs die vorläufig gelöschten Blobs, die Sie wiederherstellen möchten, aus, und verwenden Sie **Wiederherstellen → Auswahl wiederherstellen** über die Symbolleiste oder das Kontextmenü.

Sie können Blobs auch rekursiv unter einem Verzeichnis wiederherstellen. Wenn ein aktives Verzeichnis in der Auswahl enthalten ist, stellt der Storage-Explorer alle darin enthaltenen vorläufig gelöschten Blobs oder Verzeichnisse wieder her.

In Blobcontainern mit HNS-Aktivierung ist das Wiederherstellen eines Blobs nicht möglich, wenn bereits ein aktives Blob mit demselben Namen vorhanden ist.

> [!NOTE]
> Vorläufig gelöschte Momentaufnahmen können nur wiederhergestellt werden, indem das Basisblob wiederhergestellt wird. Es gibt keine Möglichkeit, einzelne Momentaufnahmen wiederherzustellen.

## <a name="undelete-blobs-by-date-range"></a>Wiederherstellen von Blobs nach Datumsbereich

Mit dem Storage-Explorer können Sie Blobs auch basierend auf ihrem Löschzeitpunkt wiederherstellen.

Wählen Sie zum Wiederherstellen von Blobs nach Datumsbereich die vorläufig gelöschten Blobs, die Sie wiederherstellen möchten, aus, und verwenden Sie **Wiederherstellen → Nach Datum wiederherstellen...** über die Symbolleiste oder das Kontextmenü.

**Nach Datum wiederherstellen...** funktioniert genauso wie **Auswahl wiederherstellen**, mit der Ausnahme, dass Blobs oder Verzeichnisse herausgefiltert werden, deren Löschzeitpunkt außerhalb des von Ihnen angegebenen Bereichs liegt.

## <a name="see-also"></a>Weitere Informationen

- [Azure Storage-Explorer – Leitfaden zur Blobversionsverwaltung](./storage-explorer-blob-versioning.md)
- [Vorläufiges Löschen für Blobs](../blobs/soft-delete-blob-overview.md)
