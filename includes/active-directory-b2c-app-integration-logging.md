---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/29/2021
ms.author: mimart
ms.openlocfilehash: 720b464c02b929590df46123e0caf9a9e83fe2f4
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220249"
---
## <a name="configure-logging"></a>Konfigurieren der Protokollierung

Die MSAL-Bibliothek generiert Protokollmeldungen, die bei der Diagnose von Problemen helfen können. Die App kann die Protokollierung konfigurieren. Die App kann Ihnen auch eine benutzerdefinierte Kontrolle über den Detaillierungsgrad geben und darüber, ob persönliche und Organisationsdaten protokolliert werden. 

Sie sollten einen MSAL-Protokollierungsrückruf erstellen und Benutzern eine Möglichkeit bieten, Protokolle zu übermitteln, wenn Authentifizierungsprobleme auftreten. MSAL bietet mehrere Protokollierungsdetailgrade:

- **Fehler**: Gibt an, dass ein Problem aufgetreten ist und ein Fehler generiert wurde. Dieser Grad wird zum Debuggen und Identifizieren von Problemen verwendet.
- **Warnung**: Es muss nicht unbedingt zu einem Fehler oder Ausfall gekommen sein. Diese Informationen sind zum Diagnostizieren und Ermitteln von Problemen vorgesehen.
- **Info**: MSAL protokolliert Ereignisse, die zu Informationszwecken und nicht notwendigerweise zum Debuggen bestimmt sind.
- **Ausführlich**: Dies ist die Standardstufe. Msal protokolliert die vollständigen Details des Bibliotheksverhaltens.

Die MSAL-Protokollierung erfasst standardmäßig keine personenbezogenen oder Organisationsdaten. Die Bibliothek bietet Ihnen jedoch die Option, die Protokollierung von personenbezogenen Daten und Organisationsdaten zu aktivieren.
