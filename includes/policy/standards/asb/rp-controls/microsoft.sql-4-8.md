---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d9fc26542163628cf5109bab8a9f9080ee339810
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123483925"
---
|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Verwaltete SQL-Instanzen sollten kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Die Implementierung von TDE (Transparent Data Encryption) mit Ihrem eigenen Schlüssel bietet mehr Transparenz und eine bessere Kontrolle über den TDE-Schutz, erhöht die Sicherheit durch einen HSM-basierten externen Dienst und fördert die Aufgabentrennung. Diese Empfehlung gilt für Organisationen mit entsprechenden Complianceanforderungen. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL Server-Instanzen müssen kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Die Implementierung von TDE (Transparent Data Encryption) mit Ihrem eigenen Schlüssel bietet mehr Transparenz und eine bessere Kontrolle über den TDE-Schutz, erhöht die Sicherheit durch einen HSM-basierten externen Dienst und fördert die Aufgabentrennung. Diese Empfehlung gilt für Organisationen mit entsprechenden Complianceanforderungen. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Transparent Data Encryption für SQL-Datenbanken aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |TDE (Transparent Data Encryption) sollte aktiviert werden, um ruhende Daten zu schützen und Konformitätsanforderungen zu erfüllen. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
