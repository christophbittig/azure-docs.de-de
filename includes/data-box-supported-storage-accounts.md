---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: 092484b09af008770260c64cd6b7e0ea15265811
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803384"
---
Im Folgenden finden Sie eine Liste der unterstützten Speicherkonten und Speichertypen für ein Data Box-Gerät. Eine vollständige Liste aller Funktionen für alle Typen von Speicherkonten finden Sie unter [Speicherkontotypen](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts).

Die folgende Tabelle zeigt die unterstützten Speicherkonten für Importaufträge.

| **Speicherkonto/unterstützte Speichertypen** | **Blockblob** |**Seitenblob** _ |_ *Azure-Dateien** |**Hinweise**|
| --- | --- | -- | -- | -- |
| Klassisch Standard | J | J | J |
| Universell V1 Standard  | J | J | J | Es werden sowohl „heiße“ als auch „kalte“ Blobs unterstützt.|
| Universell V1 Premium  |  | J| | |
| Universell V2 Standard  | J | J | J | Es werden sowohl „heiße“ als auch „kalte“ Blobs unterstützt.|
| Universell V2 Premium  |  |J | | |
| Azure Premium FileStorage |  |  | J |  |  
| Blobspeicher Standard |J | | |Es werden sowohl „heiße“ als auch „kalte“ Blobs unterstützt. |

\* *: In Seitenblobs hochgeladene Daten müssen einem ganzzahligen Vielfachen von 512 Byte entsprechen, z. B. VHDs.*

Die folgende Tabelle zeigt die unterstützten Speicherkonten für Exportaufträge.

| **Speicherkonto/unterstützte Speichertypen** | **Blockblob** |**Seitenblob** _ |_ *Azure-Dateien** |**Unterstützte Zugriffsebenen**|
| --- | --- | -- | -- | -- |
| Klassisch Standard | J | J | J | |
| Universell V1 Standard  | J | J | J | Heiße Ebene, kalte Ebene|
| Universell V1 Premium  |  | J| | |
| Universell V2 Standard  | J | J | J | Heiße Ebene, kalte Ebene|
| Universell V2 Premium  |  |J | | |
| Azure Premium FileStorage |  |  | J |  |
| Blobspeicher Standard |J | | |Heiße Ebene, kalte Ebene |
| Block Blob Storage Premium |J | | |Heiße Ebene, kalte Ebene |
| Page Blob Storage Premium | |J | | |

> [!IMPORTANT]
> - Data Box unterstützt Warteschlangen, Tabellen und Datenträger nicht als Speichertypen von Importaufträgen für universelle Konten. Data Box unterstützt Warteschlangen, Tabellen, Datenträger und Azure Data Lake Gen 2 nicht als Speichertypen von Exportaufträgen für universelle Konten.
> - Data Box unterstützt keine Anfügeblobs für Blob Storage- und Block Blob Storage-Konten.
> - Die Unterstützung für das Network File System 3.0-Protokoll (NFS) in Azure Blog Storage wird nicht mit Data Box unterstützt.
> - In Seitenblobs hochgeladene Daten müssen einem ganzzahligen Vielfachen von 512 Byte entsprechen, z. B. VHDs.
> - Es können maximal 80 TB exportiert werden.
> - Dateiverlauf und Blobmomentaufnahmen werden nicht exportiert.
> - Archivblobs werden für den Export nicht unterstützt. Aktivieren Sie die Blobs auf der Archivebene, bevor Sie den Export durchführen. Weitere Informationen finden Sie in der [Übersicht über die Aktivierung von Blobs aus der Archivebene](../articles/storage/blobs/storage-blob-rehydration.md#rehydrate-an-archived-blob-to-an-online-tier).
