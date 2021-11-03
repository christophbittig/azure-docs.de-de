---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: kengaderdus
ms.openlocfilehash: 9de7912b426d659a03e8a7653c690cb16f83010e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007600"
---
Nach Abschluss der Authentifizierung interagieren Benutzer mit der App, die eine geschützte Web-API aufruft. Die Web-API verwendet die Authentifizierung per [Bearertoken](https://datatracker.ietf.org/doc/html/rfc6750). Das Bearertoken ist das Zugriffstoken, das von Azure AD B2C abgerufen wird. Die App übergibt das Token im Autorisierungsheader der HTTPS-Anforderung. 
    
```http
Authorization: Bearer <token>
```

Wenn der Zugriffstokenbereich nicht mit den Web-API-Bereichen übereinstimmt, ruft die Authentifizierungsbibliothek ein neues Zugriffstoken mit den richtigen Bereichen ab.
