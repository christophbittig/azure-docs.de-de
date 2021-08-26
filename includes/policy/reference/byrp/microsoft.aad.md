---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/20/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: aede5c742648369caccee9ac04b4f1456a087dd7
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122608748"
---
|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Verwaltete Azure Active Directory Domain Services-Domänen müssen den reinen TLS 1.2-Modus verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87b5a-7813-4b57-8a43-42dd9df5aaa7) |Verwenden Sie für Ihre verwalteten Domänen den reinen TLS 1.2-Modus. Azure AD Domain Services aktiviert standardmäßig die Verwendung von Verschlüsselungsverfahren wie z. B. NTLM v1 und TLS v1. Diese Verschlüsselungen sind möglicherweise für einige Legacyanwendungen erforderlich, gelten jedoch als schwach und können deaktiviert werden, wenn Sie sie nicht benötigen. Wenn der reine TLS 1.2-Modus aktiviert ist und ein Client eine Anforderung ohne Verwendung von TLS 1.2 sendet, kommt es zu einem Fehler. Weitere Informationen finden Sie unter [https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain](../../../../articles/active-directory-domain-services/secure-your-domain.md). |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Active%20Directory/AADDomainServices_TLS_Audit.json) |