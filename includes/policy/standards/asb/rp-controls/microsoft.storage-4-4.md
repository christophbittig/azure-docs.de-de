---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 321a922b617bec09b5135e8c580fe63cda7fb7c3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128908513"
---
|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sichere Übertragung in Speicherkonten sollte aktiviert werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Hiermit wird die Anforderung sicherer Übertragungen in Ihren Speicherkonto überwacht. Sichere Übertragung ist eine Option, die erzwingt, dass Ihr Storage-Konto nur Anforderungen von sicheren Verbindungen (HTTPS) akzeptiert. Durch die Verwendung von HTTPS wird eine Authentifizierung zwischen dem Server und dem Dienst sichergestellt, und die übertragenen Daten werden vor Angriffen auf Netzwerkebene geschützt, z. B. Man-in-the-Middle-Angriffe, Abhörangriffe und Session Hijacking. |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
