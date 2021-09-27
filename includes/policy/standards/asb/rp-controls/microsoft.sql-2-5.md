---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5e73564010d4a78c14c87ed402d2aa2fc2501d84
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909678"
---
|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Für SQL Server-Instanzen mit Überwachung im Speicherkontoziel muss die Datenaufbewahrung auf mindestens 90 Tage festgelegt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Zum Zweck der Untersuchung von Incidents wird empfohlen, die im Rahmen der SQL Server-Überwachung im Speicherkontoziel erfassten Daten für mindestens 90 Tage aufzubewahren. Stellen Sie sicher, dass Sie die erforderlichen Aufbewahrungsregeln für die Regionen einhalten, in denen Sie tätig sind. Dies ist gelegentlich zur Einhaltung gesetzlicher Standards erforderlich. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
