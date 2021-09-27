---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8ee74188a5735c9a0fce0cce382f320313f0a938
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909644"
---
|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Erzwingen einer SSL-Verbindung muss für MySQL-Datenbankserver aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure-Datenbank für MySQL unterstützt die Verbindung Ihres Servers mit Azure-Datenbank für MySQL mit Clientanwendungen, die Secure Sockets Layer (SSL) verwenden. Durch das Erzwingen von SSL-Verbindungen zwischen Ihrem Datenbankserver und Ihren Clientanwendungen können Sie sich vor Man-in-the-Middle-Angriffen schützen, indem Sie den Datenstrom zwischen dem Server und Ihrer Anwendung verschlüsseln. Diese Konfiguration erzwingt, dass SSL für den Zugriff auf Ihren Datenbankserver immer aktiviert ist. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
