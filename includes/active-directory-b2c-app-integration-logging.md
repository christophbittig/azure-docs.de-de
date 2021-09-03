---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/29/2021
ms.author: mimart
ms.openlocfilehash: 991883785974648597141f0333679b39477a6ff9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122338903"
---
## <a name="configure-logging"></a>Konfigurieren der Protokollierung

Die MSAL-Bibliothek generiert Protokollmeldungen, die bei der Diagnose von Problemen helfen können. Eine App kann die Protokollierung konfigurieren und hat benutzerdefinierte Kontrolle über den Detailgrad und darüber, ob personenbezogene und Organisationsdaten protokolliert werden oder nicht. 

Es wird empfohlen, einen MSAL-Protokollierungsrückruf festzulegen und Benutzern eine Möglichkeit zu bieten, Protokolle zu übermitteln, wenn Authentifizierungsprobleme auftreten. MSAL bietet mehrere Grade für Protokollierungsdetails:

- Error: Gibt an, dass ein Problem aufgetreten ist und ein Fehler generiert wurde. Wird zum Debuggen und Identifizieren von Problemen verwendet.
- Warnung: Es muss nicht unbedingt zu einem Fehler oder Ausfall gekommen sein. Dieser Grad ist für das Diagnostizieren und Ermitteln von Problemen vorgesehen.
- Info: MSAL protokolliert Ereignisse, die für Informationszwecke und nicht unbedingt für das Debuggen vorgesehen sind.
- Verbose: Standard. Msal protokolliert die vollständigen Details des Bibliotheksverhaltens.

Die MSAL-Protokollierung erfasst standardmäßig keine personenbezogenen oder Organisationsdaten. Die Bibliothek bietet jedoch die Option, die Protokollierung von personenbezogenen Daten und Organisationsdaten zu aktivieren.



