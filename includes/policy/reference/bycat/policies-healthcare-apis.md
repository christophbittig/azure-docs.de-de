---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c5ab72367fe38dca7bf3460c8db62fd128a52407
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654888"
---
|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zugriff auf Ihren FHIR-Dienst über CORS nicht allen Domänen gestatten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe1c9040-c46a-4e81-9aea-c7850fbb3aa6) |Die ursprungsübergreifende Ressourcenfreigabe (Cross-Origin Resource Sharing, CORS) sollte nicht allen Domänen den Zugriff auf Ihren FHIR-Dienst gestatten. Entfernen Sie den Zugriff für alle Domänen zum Schutz Ihres FHIR-Diensts, und definieren Sie explizit diejenigen Domänen, die eine Verbindung herstellen dürfen. |Überprüfung, deaktiviert |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Healthcare%20APIs/HealthcareAPIs_FHIR_Service_RestrictCORSAccess_Audit.json) |
