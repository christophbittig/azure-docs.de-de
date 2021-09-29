---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ae64413016e3738a46749e92c2c768ed3868cba9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654790"
---
|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure API for FHIR muss einen kundenseitig verwalteten Schlüssel zum Verschlüsseln ruhender Daten verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |Verwenden Sie einen kundenseitig verwalteten Schlüssel, um die Verschlüsselung der in Azure API for FHIR gespeicherten ruhenden Daten zu steuern, wenn dies gesetzlich vorgeschrieben ist oder als Konformitätsanforderung gilt. Kundenseitig verwaltete Schlüssel bieten außerdem eine doppelte Verschlüsselung, indem eine zweite Verschlüsselungsebene zusätzlich zur Standardverschlüsselung mit dienstseitig verwalteten Schlüsseln hinzugefügt wird. |Überprüfung, deaktiviert |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[Azure API for FHIR sollte einen privaten Link verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR sollte über mindestens eine genehmigte private Endpunktverbindung verfügen. Clients in einem virtuellen Netzwerk können über Private Link-Instanzen sicher auf Ressourcen mit Verbindungen mit privaten Endpunkten zugreifen. Weitere Informationen finden Sie unter [https://aka.ms/fhir-privatelink](../../../../articles/healthcare-apis/azure-api-for-fhir/configure-private-link.md). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[Zugriff auf API for FHIR über CORS nicht allen Domänen gestatten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |CORS (Cross-Origin Resource Sharing) darf nicht allen Domänen Zugriff auf Ihre API for FHIR-Instanz erteilen. Entfernen Sie zum Schützen Ihrer API for FHIR-Instanz den Zugriff für alle Domänen, und definieren Sie explizit die Domänen, für die die Verbindungsherstellung zulässig ist. |Überprüfung, deaktiviert |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |