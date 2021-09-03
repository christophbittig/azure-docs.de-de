---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: 3d4bb5ff840a2f4ee5c4c33e2cc51dfe440866a7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122338912"
---
Nach Abschluss der Authentifizierung interagieren Benutzer mit der App, die eine geschützte Web-API aufruft. Die Web-API verwendet die Authentifizierung per [Bearertoken](https://datatracker.ietf.org/doc/html/rfc6750). Das Bearertoken ist das Zugriffstoken, das von Azure AD B2C abgerufen wird. Die App übergibt das Token im Autorisierungsheader der HTTPS-Anforderung. 
    
```http
Authorization: Bearer <token>
```

Wenn der Zugriffstokenbereich nicht mit den Web-API-Bereichen übereinstimmt, ruft die Authentifizierungsbibliothek ein neues Zugriffstoken mit den richtigen Bereichen ab.
