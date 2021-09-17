---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: ebc113e993eadc41c2b1c58c9130908727101a6f
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227722"
---
Nach Abschluss der Authentifizierung interagieren Benutzer mit der App, die eine geschützte Web-API aufruft. Die Web-API verwendet die Authentifizierung per [Bearertoken](https://datatracker.ietf.org/doc/html/rfc6750). Das Bearertoken ist das Zugriffstoken, das von Azure AD B2C abgerufen wird. Die App übergibt das Token im Autorisierungsheader der HTTPS-Anforderung. 
    
```http
Authorization: Bearer <token>
```

Wenn der Zugriffstokenbereich nicht mit den Web-API-Bereichen übereinstimmt, ruft die Authentifizierungsbibliothek ein neues Zugriffstoken mit den richtigen Bereichen ab.
