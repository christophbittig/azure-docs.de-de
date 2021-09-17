---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 8547dac26c8d3a6a286a49a6b6e12919199a994b
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220317"
---
## <a name="use-a-custom-domain"></a>Verwenden einer benutzerdefinierten Domäne

Durch Verwenden einer [benutzerdefinierten Domäne](../articles/active-directory-b2c/custom-domain.md) können Sie die Authentifizierungs-URL vollständig mit Branding versehen. Aus seiner Sicht bleibt der Benutzer während des Authentifizierungsprozesses in Ihrer Domäne und wird nicht zum Azure AD B2C-Domänennamen b2clogin.com umgeleitet.

Um alle Verweise auf „b2c“ in der URL zu entfernen, können Sie auch den Namen Ihres B2C-Mandanten, contoso.onmicrosoft.com, in der URL der Authentifizierungsanforderung durch die GUID Ihrer Mandanten-ID ersetzen. Sie können z. B. `https://fabrikamb2c.b2clogin.com/contoso.onmicrosoft.com/` in `https://account.contosobank.co.uk/<tenant ID GUID>/` ändern.
