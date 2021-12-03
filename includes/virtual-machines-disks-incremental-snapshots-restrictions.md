---
title: Datei einfügen
description: include file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c6f83c1590057b8b46b19f3100947bd838666792
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022297"
---
- Inkrementelle Momentaufnahmen können derzeit nicht zwischen Abonnements verschoben werden.
- Sie können zurzeit nur SAS-URIs mit jeweils bis zu fünf Momentaufnahmen einer bestimmten Momentaufnahmefamilie generieren.
- Sie können keine inkrementelle Momentaufnahme für einen bestimmten Datenträger außerhalb des Abonnements dieses Datenträgers erstellen.
- Bis zu sieben inkrementelle Momentaufnahmen pro Datenträger können alle fünf Minuten erstellt werden.
- Für einen einzelnen Datenträger können insgesamt 200 inkrementelle Momentaufnahmen erstellt werden.
- Sie können die Änderungen zwischen erstellten Momentaufnahmen nicht abrufen, wenn zwischen diesen eine Änderung der Größe des übergeordneten Datenträgers vorgenommen wurde, die die Grenze von 4 TB überschreitet. Sie haben beispielsweise eine inkrementelle Momentaufnahme `snapshot-a` erstellt, während die Größe eines Datenträgers 2 TB betrug. Nun haben Sie die Größe des Datenträgers auf 6 TB erweitert und dann eine weitere inkrementelle Momentaufnahme `snapshot-b` erstellt. Die Änderungen zwischen `snapshot-a` und `snapshot-b` können nicht abgerufen werden. Sie müssen die vollständige Kopie von `snapshot-b` herunterladen, die nach der Größenänderung erstellt wurde. Anschließend können Sie die Änderungen zwischen `snapshot-b` und nach `snapshot-b` erstellten Momentaufnahmen abrufen.
