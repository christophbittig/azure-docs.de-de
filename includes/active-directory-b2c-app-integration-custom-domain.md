---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: e6ab23e9d98d2bb1dfea21a7b4f681dbd5be6e14
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803550"
---
## <a name="use-a-custom-domain"></a>Verwenden einer benutzerdefinierten Domäne

Durch Verwenden einer [benutzerdefinierten Domäne](../articles/active-directory-b2c/custom-domain.md) können Sie die Authentifizierungs-URL vollständig mit Branding versehen. Aus Sicht des Benutzers bleibt dieser während des Authentifizierungsprozesses in Ihrer Domäne und wird nicht zum Azure AD B2C-Domänennamen b2clogin.com umgeleitet.

Sie können auch den Namen Ihres B2C-Mandanten (contoso.onmicrosoft.com) in der URL der Authentifizierungsanforderung durch die GUID Ihrer Mandanten-ID ersetzen, um alle Verweise auf „b2c“ in der URL zu entfernen. Sie können z. B. `https://fabrikamb2c.b2clogin.com/contoso.onmicrosoft.com/` in `https://account.contosobank.co.uk/<tenant ID GUID>/` ändern.
