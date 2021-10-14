---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 386109e6a28514c6d57f201bdc6097f4d969cdad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909713"
---
|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtuelle Computer sollten temporäre Datenträger, Caches und Datenflüsse zwischen Compute- und Speicherressourcen verschlüsseln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Standardmäßig werden die Datenträger für Betriebssystem und Daten für eine VM im Ruhezustand mithilfe plattformseitig verwalteter Schlüssel verschlüsselt. Temporäre Datenträger, Datencaches und zwischen Compute- und Speicherressourcen übertragene Daten werden nicht verschlüsselt. In folgenden Fällen sollten Sie diese Empfehlung ignorieren: 1. Verwenden der Verschlüsselung auf dem Host, oder 2. Die serverseitige Verschlüsselung in Managed Disks erfüllt Ihre Sicherheitsanforderungen. Weitere Informationen erhalten Sie unter [Serverseitige Verschlüsselung von Azure Disk Storage](../../../../../articles/virtual-machines/disk-encryption.md). und [Übersicht über Verschlüsselungsoptionen für verwaltete Datenträger](../../../../../articles/virtual-machines/disk-encryption-overview.md#comparison). |AuditIfNotExists, Disabled |[2.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |