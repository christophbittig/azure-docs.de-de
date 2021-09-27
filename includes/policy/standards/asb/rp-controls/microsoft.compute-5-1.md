---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d587f84c35c49c8a5841ca0be36f65a4838ef56f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910202"
---
|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auf Ihren virtuellen Computern muss eine Lösung zur Sicherheitsrisikobewertung installiert werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Überwacht VMs, um zu ermitteln, ob eine unterstützte Lösung zur Sicherheitsrisikobewertung ausgeführt wird. Eine Kernkomponente jedes Cyberrisikos und jedes Sicherheitsprogramms ist die Identifizierung und Analyse von Sicherheitsrisiken. Der Standard-Tarif von Azure Security Center umfasst das Überprüfen von Sicherheitsrisiken für Ihre virtuellen Computer ohne zusätzliche Kosten. Darüber hinaus kann Azure Security Center dieses Tool automatisch für Sie bereitstellen. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
