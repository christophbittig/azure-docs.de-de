---
title: Unterstützung der Blob Storage-Funktion in Azure-Speicherkonten
description: Bestimmen Sie die Ebene der Unterstützung für jedes Speicherkonto-Feature nach Speicherkontotyp und die Einstellungen, die für das Konto aktiviert sind.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: normesta
ms.openlocfilehash: e22e7dcfde1782600cdb53814014e6080ffbbfa1
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271629"
---
# <a name="blob-storage-feature-support-in-azure-storage-accounts"></a>Unterstützung von Blob Storage-Features in Azure Storage-Konten

Dieser Artikel zeigt, ob eine Funktion vollständig unterstützt wird (allgemein verfügbar), auf der Vorschauebene unterstützt wird oder noch nicht unterstützt wird. Die Unterstützungsebenen hängen vom Speicherkontotyp ab und davon, ob bestimmte Funktionen oder Protokolle im Konto aktiviert sind.

Die in diesen Tabellen enthaltenen Elemente ändern sich im Laufe der Zeit, da die Unterstützung laufend erweitert wird.

## <a name="standard-general-purpose-v2-accounts"></a>Standardmäßige allgemein v2 Konten

| Storage-Feature | Blob Storage (Standardunterstützung) | Data Lake Storage Gen2 <sup>1</sup>   | NFS 3.0 <sup>1</sup>  |
|---------------|-------------------|---|---|
| [Zugriffsebene – Archiv](access-tiers-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Zugriffsebene – kalt](access-tiers-overview.md)    | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)|
| [Zugriffsebene – heiß](access-tiers-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Anonymer öffentlicher Zugriff](anonymous-read-access-configure.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)|
| [Azure Active Directory Sicherheit](authorize-access-azure-active-directory.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Blob-Inventar](blob-inventory.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Blobindextags](storage-manage-find-blobs.md) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Blobmomentaufnahmen](snapshots-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> | ![Nein](../media/icons/no-icon.png) |
| [Blob Storage APIs](reference.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)  <sup>3</sup> | ![Ja](../media/icons/yes-icon.png) <sup>3</sup>|
| [Blob Storage Azure CLI Befehle](storage-quickstart-blobs-cli.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Blob Storage-Ereignisse](storage-blob-event-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)  <sup>3</sup>  | ![Nein](../media/icons/no-icon.png) |
| [Blob Storage PowerShell Befehle](storage-quickstart-blobs-powershell.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Blobversionsverwaltung](versioning-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [blobfuse](storage-how-to-mount-container-linux.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Änderungsfeed](storage-blob-change-feed.md) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Benutzerdefinierte Domänen](storage-custom-domain-name.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Failover für kundenseitig verwaltetes Konto](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Kundenverwaltete Schlüssel (Verschlüsselung)](../common/customer-managed-keys-overview.md?toc=/azure/storage/blobs/toc.json) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Vom Kunden bereitgestellte Schlüssel (Verschlüsselung)](encryption-customer-provided-keys.md) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Datenredundanzoptionen](../common/storage-redundancy.md?toc=/azure/storage/blobs/toc.json) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Verschlüsselungsbereiche](encryption-scope-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Unveränderlicher Speicher](immutable-storage-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Nachverfolgung des Zeitpunkts des letzten Zugriffs für die Lebenszyklusverwaltung](lifecycle-management-overview.md#move-data-based-on-last-accessed-time) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Richtlinien für die Lebenszyklusverwaltung (Blob löschen)](./lifecycle-management-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Lebenszyklusverwaltung (Tiering)](./lifecycle-management-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Protokollierung in Azure Monitor](./monitor-blob-storage.md) | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Metriken in Azure Monitor](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Objektreplikation für Blockblobs](object-replication-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Seitenblobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) | ![Ja](../media/icons/yes-icon.png) |![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Point-in-Time-Wiederherstellung für Blockblobs](point-in-time-restore-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Vorläufiges Löschen für Blobs](./soft-delete-blob-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup>   <sup>3</sup> | ![Nein](../media/icons/no-icon.png) |
| [Vorläufiges Löschen für Container](soft-delete-container-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Statische Websites](storage-blob-static-website.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Storage Analytics-Protokolle (klassisch)](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)  <sup>3</sup> | ![Nein](../media/icons/no-icon.png) |
| [Metriken von Storage Analytics (klassisch)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |

<sup>1</sup>    Für Data Lake Storage Gen2 und das NFS 3.0-Protokoll (Network File System) ist ein Speicherkonto mit aktiviertem hierarchischem Namespace erforderlich.

<sup>2</sup>    Die Funktion wird auf der Vorschauebene unterstützt.

<sup>3</sup>    Siehe [Bekannte Probleme mit Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md). Diese Probleme gelten für alle Konten, für die das Feature für hierarchische Namespaces aktiviert ist.

## <a name="premium-block-blob-accounts"></a>Premium-Blockblobkonten

| Storage-Feature | Blob Storage (Standardunterstützung) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> |
|---------------|-------------------|---|---|
| [Zugriffsebene – Archiv](access-tiers-overview.md)  | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Zugriffsebene – kalt](access-tiers-overview.md) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Zugriffsebene – heiß](access-tiers-overview.md) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Anonymer öffentlicher Zugriff](anonymous-read-access-configure.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Azure Active Directory Sicherheit](authorize-access-azure-active-directory.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Blob-Inventar](blob-inventory.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Blobindextags](storage-manage-find-blobs.md) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Blobmomentaufnahmen](snapshots-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> | ![Nein](../media/icons/no-icon.png) |
| [Blob Storage APIs](reference.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)  <sup>3</sup> | ![Ja](../media/icons/yes-icon.png) <sup>3</sup>|
| [Blob Storage Azure CLI Befehle](storage-quickstart-blobs-cli.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Blob Storage-Ereignisse](storage-blob-event-overview.md) | ![Ja](../media/icons/yes-icon.png)  <sup>3</sup>  | ![Ja](../media/icons/yes-icon.png) <sup>3</sup>| ![Nein](../media/icons/no-icon.png) |
| [Blob Storage PowerShell Befehle](storage-quickstart-blobs-powershell.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Blobversionsverwaltung](versioning-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [blobfuse](storage-how-to-mount-container-linux.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Änderungsfeed](storage-blob-change-feed.md) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Benutzerdefinierte Domänen](storage-custom-domain-name.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Failover für kundenseitig verwaltetes Konto](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Kundenverwaltete Schlüssel (Verschlüsselung)](../common/customer-managed-keys-overview.md?toc=/azure/storage/blobs/toc.json)  | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Vom Kunden bereitgestellte Schlüssel (Verschlüsselung)](encryption-customer-provided-keys.md) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Datenredundanzoptionen](../common/storage-redundancy.md?toc=/azure/storage/blobs/toc.json) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Verschlüsselungsbereiche](encryption-scope-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Unveränderlicher Speicher](immutable-storage-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Nachverfolgung des Zeitpunkts des letzten Zugriffs für die Lebenszyklusverwaltung](lifecycle-management-overview.md#move-data-based-on-last-accessed-time) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Richtlinien für die Lebenszyklusverwaltung (Blob löschen)](./lifecycle-management-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Lebenszyklusverwaltung (Tiering)](./lifecycle-management-overview.md) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Protokollierung in Azure Monitor](./monitor-blob-storage.md) | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Metriken in Azure Monitor](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Objektreplikation für Blockblobs](object-replication-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Seitenblobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Point-in-Time-Wiederherstellung für Blockblobs](point-in-time-restore-overview.md) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Vorläufiges Löschen für Blobs](./soft-delete-blob-overview.md)   | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup>   <sup>3</sup> | ![Nein](../media/icons/no-icon.png) |
| [Vorläufiges Löschen für Container](soft-delete-container-overview.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| [Statische Websites](storage-blob-static-website.md) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| [Storage Analytics-Protokolle (klassisch)](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) <sup>2</sup> <sup>3</sup> | ![Nein](../media/icons/no-icon.png)|
| [Metriken von Storage Analytics (klassisch)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |

<sup>1</sup>    Für Data Lake Storage Gen2 und das NFS 3.0-Protokoll (Network File System) ist ein Speicherkonto mit aktiviertem hierarchischem Namespace erforderlich.

<sup>2</sup>    Die Funktion wird auf der Vorschauebene unterstützt.

<sup>3</sup>    Siehe [Bekannte Probleme mit Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md). Diese Probleme gelten für alle Konten, für die das Feature für hierarchische Namespaces aktiviert ist.

## <a name="see-also"></a>Weitere Informationen

- [Bekannte Probleme mit Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)

- [Bekannte Probleme bei der Unterstützung des Network File System 3.0-Protokolls (NFS) in Azure Blob Storage](network-file-system-protocol-known-issues.md)
