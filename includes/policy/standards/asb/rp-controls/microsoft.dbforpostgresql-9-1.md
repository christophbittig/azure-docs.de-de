---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 65a2d35eaf606273d7c58abaf65255b1c7ac6708
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123483969"
---
|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Georedundante Sicherung muss für Azure Database for PostgreSQL aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Mit Azure Database for PostgreSQL können Sie die Redundanzoption für Ihren Datenbankserver auswählen. Sie kann auf einen georedundanten Sicherungsspeicher festgelegt werden, in dem die Daten nicht nur in der Region gespeichert werden, in der Ihr Server gehostet wird, sondern auch in eine gekoppelte Region repliziert werden, um die Wiederherstellungsoption bei einem Regionsausfall bereitzustellen. Das Konfigurieren von georedundantem Speicher für die Sicherung ist nur während der Erstellung des Servers zulässig. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
