---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d0155591aa3e27fc1d0ab831c842223421f3d6f3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595695"
---
|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[HPC Cache-Konten müssen für die Verschlüsselung einen kundenseitig verwalteten Schlüssel verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Verwalten Sie die Verschlüsselung ruhender Azure HPC Cache-Daten mithilfe von kundenseitig verwalteten Schlüsseln. Standardmäßig werden Kundendaten mit dienstseitig verwalteten Schlüsseln verschlüsselt. Kundenseitig verwaltete Schlüssel sind jedoch häufig zur Einhaltung gesetzlicher Bestimmungen erforderlich. Mit kundenseitig verwalteten Schlüsseln können die Daten mit einem Azure Key Vault-Schlüssel verschlüsselt werden, der von Ihnen erstellt wird und sich in Ihrem Besitz befindet. Sie verfügen über die volle Kontrolle über und Verantwortung für den Schlüssellebenszyklus, einschließlich Rotation und Verwaltung. |Audit, Disabled, Deny |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
