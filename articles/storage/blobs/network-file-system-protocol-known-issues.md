---
title: Bekannte Probleme mit NFS 3.0 in Azure Blob Storage | Microsoft-Dokumentation
description: Erfahren Sie mehr über Einschränkungen und bekannte Probleme bei der Unterstützung des Protokolls NFS 3.0 (Network File System) in Azure Blob Storage.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 8af0b4a5c26d70f9fdedc30d07c8953bfee76fb4
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727128"
---
# <a name="known-issues-with-network-file-system-nfs-30-protocol-support-in-azure-blob-storage"></a>Bekannte Probleme bei der Unterstützung des Network File System 3.0-Protokolls (NFS) in Azure Blob Storage

In diesem Artikel werden Einschränkungen und bekannte Probleme bei der Unterstützung des Protokolls NFS 3.0 (Network File System) in Azure Blob Storage beschrieben.

## <a name="nfs-30-support"></a>Unterstützung von NFS 3.0

- Die Unterstützung von NFS 3.0 kann für vorhandene Speicherkonten nicht aktiviert werden.

- Die Unterstützung von NFS 3.0 kann in einem Speicherkonto nicht mehr deaktiviert werden, nachdem Sie sie aktiviert haben.

## <a name="nfs-30-features"></a>Funktionen von NFS 3.0

Die folgenden Features von NFS 3.0 werden noch nicht unterstützt.

- NFS 3.0 über UDP. Nur NFS 3.0 über TCP wird unterstützt.

- Sperren von Dateien mithilfe des Netzwerksperrungs-Managers (NLM). Einbindungsbefehle müssen den `-o nolock`-Parameter enthalten.

- Einbinden von Unterverzeichnissen Sie können nur das Stammverzeichnis (Container) einbinden.

- Auflisten von Einbindungen (z. B. mit dem Befehl `showmount -a`)

- Auflisten von Exporten (z. B. mit dem Befehl `showmount -e`)

- Fester Link

- Schreibgeschütztes Exportieren eines Containers

## <a name="nfs-30-clients"></a>NFS 3.0-Clients

Windows-Client für NFS wird noch nicht unterstützt

## <a name="blob-storage-features"></a>Blob Storage-Features

Die folgenden Blob Storage-Features werden noch nicht unterstützt.

- [Azure Active Directory (AD)-Sicherheit](../common/authorize-data-access.md?toc=/azure/storage/blobs/toc.json)
- [Azure Backup-Integration](../../backup/blob-backup-overview.md)
- [Azure Storage Analytics-Protokollierung](../common/storage-analytics-logging.md?toc=/azure/storage/blobs/toc.json)
- [Blobindextags](storage-blob-index-how-to.md)
- [Blob Storage-Ereignisse](storage-blob-event-overview.md)
- [Blobversionsverwaltung](versioning-enable.md)
- [Änderungsfeed](storage-blob-change-feed.md)
- [Failover für kundenseitig verwaltetes Konto](../common/storage-disaster-recovery-guidance.md?toc=/azure/storage/blobs/toc.json)
- [Kundenseitig angegebene Schlüssel für die Azure Storage-Verschlüsselung](encryption-customer-provided-keys.md) 
- [Verschlüsselungsbereiche](encryption-scope-overview.md)
- [Nachverfolgung des Zeitpunkts des letzten Zugriffs für die Lebenszyklusverwaltung](storage-lifecycle-management-concepts.md#move-data-based-on-last-accessed-date-preview)
- [Objektreplikation für Blockblobs](object-replication-overview.md)
- [Seitenblobs](storage-blobs-introduction.md#blobs)
- [Point-in-Time-Wiederherstellung für Blockblobs](point-in-time-restore-overview.md)
- [Vorläufiges Löschen für Blobs](soft-delete-blob-overview.md)
- [Vorläufiges Löschen für Container](soft-delete-container-overview.md)
- [Blobmomentaufnahmen](snapshots-overview.md)
- [Hosten von statischen Websites](storage-blob-static-website.md)

## <a name="blob-storage-apis"></a>Blob Storage-APIs

NFS 3.0, Blob-APIs und Data Lake Storage Gen2-APIs können mit denselben Daten arbeiten. 

In diesem Abschnitt werden Probleme und Einschränkungen bei der Verwendung von NFS 3.0, Blob-APIs und Data Lake Storage Gen2-APIs für dieselben Daten beschrieben. 

- Sie können nicht NFS 3.0, Blob-APIs und Data Lake Storage-APIs zum Schreiben in dieselbe Instanz einer Datei verwenden. Wenn Sie per NFS in eine Datei schreiben, sind die Blöcke dieser Datei für Aufrufe der  [Get Block List](/rest/api/storageservices/get-block-list)-Blob-API nicht sichtbar. Die einzige Ausnahme ist, wenn Sie bei der Verwendung überschreiben. Sie können eine Datei/ein Blob mithilfe einer der beiden APIs überschreiben. Sie können mit NFS 3.0 auch überschreiben, indem Sie die Option „zero-truncate“ verwenden.

- Wenn Sie den Vorgang [List Blobs](/rest/api/storageservices/list-blobs) verwenden, ohne ein Trennzeichen anzugeben, enthalten die Ergebnisse sowohl Verzeichnisse als auch Blobs. Wenn Sie sich für Trennzeichen entscheiden, sollten Sie nur einen Schrägstrich (`/`) verwenden. Dies ist das einzige Trennzeichen, das unterstützt wird.

- Wenn Sie die [Delete Blob](/rest/api/storageservices/delete-blob)-API zum Löschen eines Verzeichnisses verwenden, wird es nur gelöscht, sofern es leer ist. Dies bedeutet, dass Sie die Blob-API zum Löschen von Verzeichnissen nicht rekursiv verwenden können.

Diese Blob-Rest-APIs werden nicht unterstützt:

* [Put Blob (Page)](/rest/api/storageservices/put-blob)
* [Put Page](/rest/api/storageservices/put-page)
* [Get Page Ranges](/rest/api/storageservices/get-page-ranges)
* [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob)
* [Put Page from URL](/rest/api/storageservices/put-page-from-url)
* [Put Block List](/rest/api/storageservices/put-block-list)

Nicht verwaltete VM-Datenträger werden für Konten, die über einen hierarchischen Namespace verfügen, nicht unterstützt. Wenn Sie einen hierarchischen Namespace für ein Speicherkonto aktivieren möchten, sollten Sie verwaltete VM-Datenträger in einem Speicherkonto anordnen, für das die Funktion für hierarchische Namespaces nicht aktiviert ist. 

## <a name="see-also"></a>Weitere Informationen

- [Unterstützung für Network File System 3.0 (NFS) in Azure Blob Storage](network-file-system-protocol-support.md)
- [Einbinden von Azure Blob Storage mithilfe des NFS 3.0-Protokolls (Network File System)](network-file-system-protocol-support-how-to.md)