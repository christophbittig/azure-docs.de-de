---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/20/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c57fd546a259c855911c7ed5df3936c6c2afeed7
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122607032"
---
|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Verwendung benutzerdefinierter RBAC-Regeln überwachen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Hiermit werden integrierte Rollen wie z.B. „Benutzer“, „Mitwirkender“ und „Leser“ anstelle benutzerdefinierter RBAC-Rollen überwacht, die fehleranfällig sind. Die Verwendung benutzerdefinierter Rollen wird als Ausnahme betrachtet und erfordert eine strenge Überprüfung und Bedrohungsmodellierung. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Benutzerdefinierte Rollen für Abonnementbesitzer dürfen nicht vorhanden sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Mit dieser Richtlinie wird sichergestellt, dass keine benutzerdefinierten Abonnementbesitzerrollen vorhanden sind. |Audit, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |
