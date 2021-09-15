---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d2e8140223d3c69dcfba865068db84bda5d56007
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479530"
---
|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Verwaltete Azure Active Directory Domain Services-Domänen müssen den reinen TLS 1.2-Modus verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87b5a-7813-4b57-8a43-42dd9df5aaa7) |Verwenden Sie für Ihre verwalteten Domänen den reinen TLS 1.2-Modus. Azure AD Domain Services aktiviert standardmäßig die Verwendung von Verschlüsselungsverfahren wie z. B. NTLM v1 und TLS v1. Diese Verschlüsselungen sind möglicherweise für einige Legacyanwendungen erforderlich, gelten jedoch als schwach und können deaktiviert werden, wenn Sie sie nicht benötigen. Wenn der reine TLS 1.2-Modus aktiviert ist und ein Client eine Anforderung ohne Verwendung von TLS 1.2 sendet, kommt es zu einem Fehler. Weitere Informationen finden Sie unter [https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain](../../../../articles/active-directory-domain-services/secure-your-domain.md). |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Active%20Directory/AADDomainServices_TLS_Audit.json) |