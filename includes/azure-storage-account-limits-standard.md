---
title: include file
description: Datei einfügen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/04/2021
ms.author: tamram
ms.custom: include file, references_regions
ms.openlocfilehash: 300a216e02182e3e2fe675795ff64942d8e98ed8
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132096908"
---
In der folgenden Tabelle werden die für Azure Universell V2 (General-Purpose v2, GPv2), Universell V1 (General-Purpose v1, GPv1) und Blob-Speicherkonten geltenden Standardgrenzwerte beschrieben. Der Grenzwert für *eingehend* bezieht sich auf alle Daten, die an ein Speicherkonto gesendet werden. Der Grenzwert für *ausgehend* bezieht sich auf alle Daten, die von einem Speicherkonto empfangen werden.

Microsoft empfiehlt, für die meisten Szenarien ein GPv2-Speicherkonto zu verwenden. Sie können ein Upgrade von einem GPv1-Konto oder einem Blob-Speicherkonto auf ein GPv2-Konto ganz einfach durchführen. Dabei kommt es zu keinen Ausfallzeiten, und Sie müssen keine Daten kopieren. Weitere Informationen finden Sie unter [Upgraden auf ein GPv2-Speicherkonto](../articles/storage/common/storage-account-upgrade.md).

> [!NOTE]
> Sie können höhere Grenzwerte für die Kapazität und für eingehende Daten anfordern. Wenden Sie sich dazu an den [Azure-Support](https://azure.microsoft.com/support/faq/).

| Resource | Begrenzung |
|--|--|
| Anzahl von Speicherkonten pro Region und Abonnement, einschließlich Standard- und Premium-Speicherkonten. | 250 |
| Maximale Kapazität des Standardspeicherkontos | 5 PiB<sup>1</sup> |
| Maximale Anzahl an Blobcontainern, Blobs, Dateifreigaben, Tabellen, Warteschlangen, Entitäten oder Nachrichten pro Speicherkonto. | Keine Begrenzung |
| Maximale Anforderungsrate pro Standardspeicherkonto | 20.000 Anforderungen pro Sekunde <sup>1</sup> |
| Standardmäßig maximal eingehende Daten pro GPv2-Konto und Blob-Speicherkonto in den folgenden Regionen (LRS/GRS):<br /><ul><li>Australien (Osten)</li><li>USA (Mitte)</li><li>Asien, Osten</li><li>USA (Ost) 2</li><li>Japan, Osten</li><li>Korea, Mitte</li><li>Nordeuropa</li><li>USA Süd Mitte</li><li>Asien, Südosten</li><li>UK, Süden</li><li>Europa, Westen</li><li>USA (Westen)</li></ul> | 60 GBit/s<sup>1</sup> |
| Standardmäßig maximal eingehende Daten pro GPv2-Konto und Blob-Speicherkonto in den folgenden Regionen (ZRS):<br /><ul><li>Australien (Osten)</li><li>USA (Mitte)</li><li>East US</li><li>USA (Ost) 2</li><li>Japan, Osten</li><li>Nordeuropa</li><li>USA Süd Mitte</li><li>Asien, Südosten</li><li>UK, Süden</li><li>Europa, Westen</li><li>USA, Westen 2</li></ul> | 60 GBit/s<sup>1</sup> |
| Standardmäßig maximal eingehende Daten pro GPv2-Konto und Blob-Speicherkonto in Regionen, die in der vorherigen Zeile nicht aufgeführt sind. | 25 GBit/s<sup>1</sup> |
| Standardmäßig maximal eingehende Daten für GPv1-Speicherkonten (alle Regionen) | 10 GBit/s<sup>1</sup> |
| Standardmäßig maximal ausgehende Daten für GPv2-Konten und Blob-Speicherkonten in den folgenden Regionen (LRS/GRS):<br /><ul><li>Australien (Osten)</li><li>USA (Mitte)</li><li>Asien, Osten</li><li>USA (Ost) 2</li><li>Japan, Osten</li><li>Korea, Mitte</li><li>Nordeuropa</li><li>USA Süd Mitte</li><li>Asien, Südosten</li><li>UK, Süden</li><li>Europa, Westen</li><li>USA (Westen)</li></ul> | 120 GBit/s<sup>1</sup> |
| Standardmäßig maximal ausgehende Daten für GPv2-Konten und Blob-Speicherkonten in den folgenden Regionen (ZRS): <ul><li>Australien (Osten)</li><li>USA (Mitte)</li><li>East US</li><li>USA (Ost) 2</li><li>Japan, Osten</li><li>Nordeuropa</li><li>USA Süd Mitte</li><li>Asien, Südosten</li><li>UK, Süden</li><li>Europa, Westen</li><li>USA, Westen 2</li></ul> | 120 GBit/s<sup>1</sup> |
| Standardmäßig maximal ausgehende Daten für GPv2-Konten und Blob-Speicherkonten in Regionen, die in der vorherigen Zeile nicht aufgeführt sind. | 50 GBit/s<sup>1</sup> |
| Maximaler Ausgang für universelle v1-Speicherkonten (US-Regionen) | 20 GBit/s bei aktiviertem RA-GRS/GRS, 30 GBit/s für LRS/ZRS<sup>2</sup> |
| Maximaler Ausgang für universelle v1-Speicherkonten (US-fremde Regionen) | 10 GBit/s bei aktiviertem RA-GRS/GRS, 15 GBit/s für LRS/ZRS<sup>2</sup> |
| Maximale Anzahl von Regeln für IP-Adressen pro Speicherkonto | 200 |
| Maximale Anzahl von VNET-Regeln pro Speicherkonto | 200 |
| Maximale Anzahl von Regeln für Ressourceninstanzen pro Speicherkonto | 200 |
| Maximale Anzahl privater Endpunkte pro Speicherkonto | 200 |

<sup>1</sup> Azure Storage-Standardkonten unterstützen höhere Grenzwerte für die Kapazität sowie höhere Grenzwerte für eingehende und ausgehende Daten auf Anforderung. Wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/faq/), um eine Erhöhung der Kontogrenzwerte anzufordern.

<sup>2</sup> Wenn bei Ihrem Speicherkonto Lesezugriff mit georedundantem Speicher (RA-GRS) oder geozonenredundantem Speicher (RA-GZRS) aktiviert wurde, sind die Ziele für ausgehende Daten für den sekundären Standort mit denen des primären Standorts identisch. Weitere Informationen finden Sie unter [Azure Storage-Replikation](../articles/storage/common/storage-redundancy.md).
