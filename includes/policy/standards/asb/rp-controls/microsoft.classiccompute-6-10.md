---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/20/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7dcb488e28bdbb2e3fea113ee834e73a69144902
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122636463"
---
|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adaptive Anwendungssteuerung zum Definieren sicherer Anwendungen muss auf Computern aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Hiermit wird die Anwendungssteuerung aktiviert, um die Liste der als sicher bekannten Anwendungen zu definieren, die auf Ihren Computern ausgeführt werden. Außerdem werden Sie bei Ausführung anderer Anwendungen benachrichtigt. Dies verstärkt den Schutz Ihrer Computer vor Schadsoftware. Um die Konfiguration und Wartung Ihrer Regeln zu vereinfachen, nutzt Security Center Machine Learning, um die auf den einzelnen Computern ausgeführten Anwendungen zu analysieren und eine Liste der als sicher bekannten Anwendungen vorzuschlagen. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
