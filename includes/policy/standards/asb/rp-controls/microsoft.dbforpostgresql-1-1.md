---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0914a166e960d422b6ba6858141de4b7cd56e491
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128908661"
---
|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Privater Endpunkt muss für PostgreSQL-Server aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |Private Endpunktverbindungen erzwingen eine sichere Kommunikation durch das Aktivieren privater Konnektivität mit Azure Database for PostgreSQL. Konfigurieren Sie eine private Endpunktverbindung, um nur Zugriff auf Datenverkehr zu ermöglichen, der aus bekannten Netzwerken stammt, und Zugriff von allen anderen IP-Adressen, auch solchen in Azure, zu verhindern. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
