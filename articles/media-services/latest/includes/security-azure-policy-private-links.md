---
author: johndeu
ms.service: media-services
ms.topic: include
ms.date: 08/17/2021
ms.author: johndeu
ms.openlocfilehash: d6641a3ca8181f7b3c8538179623549cc92a098a
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429167"
---
## <a name="azure-policy-definitions-for-private-links"></a>Azure Policy-Definitionen für Private Links

Azure Media Services unterstützt verschiedene integrierte, auf Anwendungsfällen basierende [Azure Policy](../../../governance/policy/overview.md)-Definitionen. 

Die folgenden Richtliniendefinitionen stehen zur Verwendung mit Private Links zur Verfügung:

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Media Services-Konten sollten ein API verwenden, welches Private Link unterstützt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa77d8bb4-8d22-4bc1-a884-f582a705b480) |Media Services-Konten sollten mit einem API erstellt werden, welches Private Link unterstützt. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_RequirePrivateLinkSupport_Audit.json) |
|[Azure Media Services muss Private Link verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a591bf5-918e-4a5f-8dad-841863140d61) |Mit Azure Private Link können Sie Ihre virtuellen Netzwerke mit Azure-Diensten verbinden, ohne dass eine öffentliche IP-Adresse an der Quelle oder am Ziel vorhanden ist. Die Private Link-Plattform verarbeitet die Konnektivität zwischen dem Consumer und den Diensten über das Azure-Backbone-Netzwerk. Durch Zuordnen privater Endpunkte zu Media Services können Sie das Risiko von Datenlecks verringern. Weitere Informationen zu privaten Verbindungen finden Sie unter [https://aka.ms/mediaservicesprivatelinkdocs](../../../../articles/media-services/latest/security-private-link-how-to.md). |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_PrivateLink_AuditIfNotExists.json) |
|[Azure Media Services für die Verwendung privater DNS-Zonen konfigurieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4a7f6c1-585e-4177-ad5b-c2c93f4bb991) |Verwenden Sie private DNS-Zonen, um die DNS-Auflösung für einen privaten Endpunkt außer Kraft zu setzen. Eine private DNS-Zone wird mit Ihrem virtuellen Netzwerk verknüpft, um Media Services-Konten aufzulösen. Weitere Informationen finden Sie unter [https://aka.ms/mediaservicesprivatelinkdocs](../../../../articles/media-services/latest/security-private-link-how-to.md). |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_PrivateLinkDns_DeployIfNotExists.json) |
|[Azure Media Services mit privaten Endpunkten konfigurieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5632066-946d-4766-9544-cd79bcc1286e) |Private Endpunkte verbinden Ihre virtuellen Netzwerke ohne eine öffentliche IP-Adresse an Quelle oder Ziel mit Azure-Diensten. Durch Zuordnen privater Endpunkte zu Media Services können Sie das Risiko von Datenlecks verringern. Weitere Informationen zu privaten Verbindungen finden Sie unter [https://aka.ms/mediaservicesprivatelinkdocs](../../../../articles/media-services/latest/security-private-link-how-to.md). |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_PrivateLink_DeployIfNotExists.json) |

Eine vollständige Liste aller integrierten Richtliniendefinitionen und Anwendungsfälle finden Sie unter [Azure Policy für Media Services](../security-azure-policy.md).

Ausführliche Informationen zum Bereitstellen und Verwenden von Azure Policy-Definitionen finden Sie im Artikel [Was ist Azure Policy?](../../../governance/policy/overview.md).