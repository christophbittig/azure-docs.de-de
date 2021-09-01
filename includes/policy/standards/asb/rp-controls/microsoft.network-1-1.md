---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8f16966ece655df04f16df28c9f57b5ebe2c42f4
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123122729"
---
|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Gesamten Internetdatenverkehr über Ihre bereitgestellte Azure Firewall-Instanz leiten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure Security Center hat festgestellt, dass einige Ihrer Subnetze nicht durch eine Firewall der nächsten Generation geschützt werden. Schützen Ihrer Subnetze vor möglichen Bedrohungen durch Einschränken des Zugriffs auf die Subnetze mit Azure Firewall oder einer unterstützten Firewall der nächsten Generation |AuditIfNotExists, Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[Subnetze sollten einer Netzwerksicherheitsgruppe zugeordnet werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Schützen Sie Ihr Subnetz vor potenziellen Bedrohungen, indem Sie den Zugriff auf das Subnetz mit einer Netzwerksicherheitsgruppe (NSG) einschränken. NSGs enthalten eine Liste der ACL-Regeln (Access Control List), die den Netzwerkdatenverkehr an Ihr Subnetz zulassen oder verweigern. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuelle Computer müssen mit einem genehmigten virtuellen Netzwerk verbunden sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Diese Richtlinie überwacht alle virtuellen Computer darauf, ob sie mit einem nicht genehmigten virtuellen Netzwerk verbunden sind. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Virtuelle Netzwerke müssen ein angegebenes Gateway für virtuelle Netzwerke verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff1776c76-f58c-4245-a8d0-2b207198dc8b) |Diese Richtlinie überwacht alle virtuellen Netzwerke, wenn die Standardroute nicht auf das angegebene Gateway für virtuelle Netzwerke zeigt. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetwork_ApprovedVirtualNetworkGateway_AuditIfNotExists.json) |
