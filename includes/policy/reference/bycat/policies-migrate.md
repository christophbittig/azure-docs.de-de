---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 564572de5af1a07d7a146e90b047f9ecc9158612
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123476956"
---
|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Migrate-Ressourcen für die Verwendung privater DNS-Zonen konfigurieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7590a335-57cf-4c95-babd-ecbc8fafeb1f) |Verwenden Sie private DNS-Zonen, um die DNS-Auflösung für einen privaten Endpunkt außer Kraft zu setzen. Eine private DNS-Zone wird mit Ihrem virtuellen Netzwerk verknüpft, um Ihr Azure Migrate-Projekt aufzulösen. Weitere Informationen finden Sie unter [https://aka.ms/privatednszone](../../../../articles/private-link/private-endpoint-dns.md). |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Migrate/Migrate_PrivateDNSZone_DeployIfNotExists.json) |