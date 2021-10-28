---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 4ce308871a14ee972290fca47bda8f2f677ebf0c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130217606"
---
* Da für die Konvertierung ein Neustart der VM durchgeführt wird, sollten Sie die Migration Ihrer VMs während eines bereits vorhandenen Wartungsfensters durchführen. 

* Die Konvertierung kann nicht rückgängig gemacht werden. 

* Beachten Sie, dass Benutzer mit der Rolle [Mitwirkender von virtuellen Computern](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) die Größe des virtuellen Computers nicht mehr ändern können (was vor dem Wechsel möglich war). Der Grund dafür ist, dass virtuelle Computer mit verwalteten Datenträgern erfordern, dass der Benutzer über die Berechtigung „Microsoft.Compute/disks/write“ für die Betriebssystemdatenträger verfügt.

* Testen Sie die Konvertierung unbedingt. Migrieren Sie einen virtuellen Testcomputer, bevor Sie die Migration in der Produktion durchführen.

* Bei der Konvertierung heben Sie die Zuordnung der VM auf. Die VM erhält eine neue IP-Adresse, wenn sie nach der Konvertierung gestartet wird. Bei Bedarf können Sie dem virtuellen Computer [eine statische IP-Adresse zuweisen](../articles/virtual-network/ip-services/public-ip-addresses.md).

* Überprüfen Sie die Mindestversion des Azure-VM-Agents, der zur Unterstützung des Konvertierungsprozesses erforderlich ist. Informationen zum Überprüfen und Aktualisieren der Agent-Version finden Sie unter [Minimum version support for VM agents in Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) (Unterstützte Mindestversion für VM-Agents in Azure).