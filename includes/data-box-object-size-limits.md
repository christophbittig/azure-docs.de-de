---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 2978fff57cfc7ae3dd4629450f573758978058d5
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130077907"
---
Hier sind die Größen der Azure-Objekte, die geschrieben werden können. Stellen Sie sicher, dass alle Dateien, die hochgeladen werden, diesen Einschränkungen entsprechen.

| Azure-Objekttyp | Standardlimit                                             |
|-------------------|-----------------------------------------------------------|
| Blockblob        | Ca. 4,75 TiB                                                 |
| Seitenblob         | 8 TiB <br> Jede Datei, die im Seitenblobformat hochgeladen wird, muss einem ganzzahligen Vielfachen von 512 Bytes entsprechen. Andernfalls tritt beim Hochladen ein Fehler auf. <br> VHD- und VHDX-Dateien entsprechen einem ganzzahligen Vielfachen von 512 Bytes. |
| Azure Files        | 1 TiB                                                      |
| Verwaltete Datenträger     | 4 TiB <br> Weitere Informationen zu den Größen und Grenzwerten finden Sie unter: <li>[Skalierbarkeitsziele bei SSD Standard](../articles/virtual-machines/disks-types.md#standard-ssds)</li><li>[Skalierbarkeitsziele bei SSD Premium](../articles/virtual-machines/disks-types.md#standard-hdds)</li><li>[Skalierbarkeitsziele bei HDD Standard](../articles/virtual-machines/disks-types.md#premium-ssds)</li><li>[Managed Disks – Preise und Abrechnung](../articles/virtual-machines/disks-types.md#billing)</li>  
