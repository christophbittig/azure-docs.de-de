---
title: Datei einfügen
description: include file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/14/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b2ed6b2b47ead64eac664f789aa8eba7887e68e7
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228794"
---
SMB Multichannel für Azure-Dateifreigaben weist derzeit die folgenden Einschränkungen auf:
- Wird nur für [Windows-](../articles/storage/files/storage-how-to-use-files-windows.md) und [Linux](../articles/storage/files/storage-how-to-use-files-linux.md)-Clients unterstützt, die SMB 3.1.1 verwenden.
- Die maximale Anzahl von Kanälen ist vier. Einzelheiten finden Sie [hier](../articles/storage/files/storage-troubleshooting-files-performance.md#cause-4-number-of-smb-channels-exceeds-four).
- SMB Direct wird nicht unterstützt.
- Private Endpunkte für Speicherkonten werden nicht unterstützt.