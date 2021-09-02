---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 577e0d6f7b08c5ba5957e0225689cac3ce0f93ac
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123106879"
---
|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[HPC Cache-Konten müssen für die Verschlüsselung einen kundenseitig verwalteten Schlüssel verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Verwalten Sie die Verschlüsselung ruhender Azure HPC Cache-Daten mithilfe von kundenseitig verwalteten Schlüsseln. Standardmäßig werden Kundendaten mit dienstseitig verwalteten Schlüsseln verschlüsselt. Kundenseitig verwaltete Schlüssel sind jedoch häufig zur Einhaltung gesetzlicher Bestimmungen erforderlich. Mit kundenseitig verwalteten Schlüsseln können die Daten mit einem Azure Key Vault-Schlüssel verschlüsselt werden, der von Ihnen erstellt wird und sich in Ihrem Besitz befindet. Sie verfügen über die volle Kontrolle über und Verantwortung für den Schlüssellebenszyklus, einschließlich Rotation und Verwaltung. |Audit, Disabled, Deny |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
