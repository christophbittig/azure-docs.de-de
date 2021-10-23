---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 10/07/2021
ms.author: alkohli
ms.openlocfilehash: 8c823b6709dbd5010ec48c2fbca185d9a211cb40
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000070"
---
Im Folgenden finden Sie eine Liste der unterstützten Speicherkonten und Speichertypen für ein Data Box-Gerät. Eine vollständige Liste aller Funktionen für alle Typen von Speicherkonten finden Sie unter [Speicherkontotypen](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts).

#### <a name="supported-storage-accounts-for-imports"></a>Unterstützte Speicherkonten für Importe

Die folgende Tabelle zeigt die unterstützten Speicherkonten für Importaufträge.

| **Speicherkonto/unterstützte Speichertypen** | **Blockblob** |**Seitenblob**<sup>1</sup> |**Azure Files** |**Unterstützte Zugriffsebenen**|
| --- | --- | -- | -- | -- |
| Klassisch Standard | J | J | J |
| Universell V1 Standard  | J | J | J | Heiße Ebene, kalte Ebene |
| Universell V1 Premium  |  | J| | |
| Universell V2 Standard<sup>2</sup>  | J | J | J | Heiße Ebene, kalte Ebene|
| Universell V2 Premium  |  |J | |  |
| Azure Premium FileStorage |  |  | J |  |  
| Blobspeicher Standard | J | | | Heiße Ebene, kalte Ebene |


<sup>1</sup> *In Seitenblobs hochgeladene Daten müssen einem ganzzahligen Vielfachen von 512 Byte entsprechen, z. B. VHDs.*

<sup>2</sup> *Azure Data Lake Storage Gen2 (ADLS Gen2) wird für Importe, nicht für Exporte, unterstützt.*


#### <a name="supported-storage-accounts-for-exports"></a>Unterstützte Speicherkonten für Exporte

Die folgende Tabelle zeigt die unterstützten Speicherkonten für Exportaufträge.

| **Speicherkonto/unterstützte Speichertypen** | **Blockblob** |**Seitenblob** _ |_ *Azure-Dateien** |**Unterstützte Zugriffsebenen**|
| --- | --- | -- | -- | -- |
| Klassisch Standard | J | J | J | |
| Universell V1 Standard  | J | J | J | Heiße Ebene, kalte Ebene |
| Universell V1 Premium  |  | J| | |
| Universell V2 Standard  | J | J | J | Heiße Ebene, kalte Ebene |
| Universell V2 Premium  |  |J | | |
| Azure Premium FileStorage |  |  | J |  |
| Blobspeicher Standard |J | | | Heiße Ebene, kalte Ebene |
| Block Blob Storage Premium |J | | | Heiße Ebene, kalte Ebene |
| Page Blob Storage Premium | |J | | |

#### <a name="caveats-for-storage-accounts"></a>Einschränkungen bei Speicherkonten

- Bei allgemeinen Konten:
  - Bei Importaufträgen unterstützt Data Box nicht die Speichertypen „Warteschlange“, „Tabelle“ und „Datenträger“.
  - Bei Exportaufträgen unterstützt Data Box nicht die Speichertypen „Warteschlange“, „Tabelle“, „Datenträger“ und „Azure Data Lake Gen2“.
- Data Box unterstützt keine Anfügeblobs für Blob Storage- und Block Blob Storage-Konten.
- Die Unterstützung für das Network File System 3.0-Protokoll (NFS) in Azure Blog Storage wird nicht mit Data Box unterstützt.
- In Seitenblobs hochgeladene Daten müssen einem ganzzahligen Vielfachen von 512 Byte entsprechen, z. B. VHDs.
- Bei Exporten:
  - Es können maximal 80 TB exportiert werden.
  - Dateiverlauf und Blobmomentaufnahmen werden nicht exportiert.
  - Archivblobs werden für den Export nicht unterstützt. Aktivieren Sie die Blobs auf der Archivebene, bevor Sie den Export durchführen. Weitere Informationen finden Sie in der [Übersicht über die Aktivierung von Blobs aus der Archivebene](../articles/storage/blobs/archive-rehydrate-overview.md).