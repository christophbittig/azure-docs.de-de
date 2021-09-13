---
title: Datei einfügen
description: Datei einfügen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/22/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 528b3243a7c7d8ca1f96d9b4a311393820e19685
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801755"
---
- Unterstützt keine Ultra Disks.
- Kann nicht aktiviert werden, wenn Azure Disk Encryption (Verschlüsselung der Gast-VM mit BitLocker/DM-Crypt) in Ihren VMs oder VM-Skalierungsgruppen aktiviert ist.
- Azure Disk Encryption kann nicht auf Datenträgern aktiviert werden, auf denen die Verschlüsselung auf dem Host aktiviert ist.
- Die Verschlüsselung kann für vorhandene VM-Skalierungsgruppen aktiviert werden. Es werden jedoch nur neue VMs automatisch verschlüsselt, die nach dem Aktivieren der Verschlüsselung erstellt wurden.
- Bei vorhandenen VMs muss deren Zuordnung aufgehoben und sie müssen erneut zugeordnet werden, damit sie verschlüsselt werden können.
- Unterstützt kurzlebige Betriebssystemdatenträger, aber nur mit plattformseitig verwalteten Schlüsseln.
