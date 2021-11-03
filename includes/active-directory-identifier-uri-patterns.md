---
author: madansr7
ms.author: saumadan
ms.date: 10/06/2021
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 133b4d8c1d3c294943ad6224c3d1436b63921462
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050571"
---
Für Anwendungs-IDs auf Basis von API- oder HTTP-Schemas werden die folgenden URI-Formate unterstützt. Ersetzen Sie die Platzhalterwerte anhand der Liste, die im Anschluss an die Tabelle aufgeführt ist.

| Unterstützte Anwendungs-ID <br/> URI-Formate | URIs für Beispiel-App-IDs |
|--|--|
| _api://\<appId\>_ | _api://fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _api://\<tenantId\>/\<appId\>_ | _api://a8573488-ff46-450a-b09a-6eca0c6a02dc/fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _api://\<tenantId\>/\<string\>_ | _api://a8573488-ff46-450a-b09a-6eca0c6a02dc/api_ |
| _api://\<string\>/<appId\>_ | _api://productapi/fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _https://\<tenantIdDomain\>.onmicrosoft.com/\<string\>_ | _`https://contoso.onmicrosoft.com/productsapi`_  |
| _https://\<verifiedCustomDomain\>/\<string\>_ |  _`https://contoso.onmicrosoft.com/productsapi`_ |
| _https://\<string\>.\<verifiedCustomDomain\>_ |  _`https://product.contoso.onmicrosoft.com`_ |
| _https://\<string\>.\<verifiedCustomDomain\>/\<string\>_ | _`https://product.onmicrosoft.com/productsapi`_   |


- _\<appId\>_ : Die AppId-Eigenschaft (Anwendungsbezeichner) des Anwendungsobjekts.
- _\<string\>_ : Der Zeichenfolgenwert für den Host oder das API-Pfadsegment.
- _\<tenantId\>_ : Jeder Azure AD-Mandant verfügt über zwei anfängliche Domänen in Form von _\<tenantId\>.onmicrosoft.com_, wobei _\<tenantID\>_ der anfängliche Domänenname ist, den der Mandantenersteller bei der Mandantenerstellung angegeben hat, und eine von Azure generierte GUID, die den Mandanten in Azure repräsentiert.
- _\<verifiedCustomDomain\>_ : Eine [überprüfte benutzerdefinierte Domäne](../articles/active-directory/fundamentals/add-custom-domain.md), die für Ihren Azure AD-Mandanten konfiguriert ist.

