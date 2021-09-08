---
ms.openlocfilehash: fa43176a945a01651dc768558d14604d0a15b300
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113726828"
---
Die folgenden Schritte zum Aktualisieren von Code gelten für alle Szenarien mit vertraulichen Clients:

1. Fügen Sie den Namespace MSAL.NET Ihrem Quellcode hinzu: `using Microsoft.Identity.Client;`.
2. Anstatt `AuthenticationContext` zu instanziieren, verwenden Sie `ConfidentialClientApplicationBuilder.Create`, um `IConfidentialClientApplication` zu instanziieren.
3. Anstelle der Zeichenfolge `resourceId` verwendet MSAL.NET Bereiche. Da Anwendungen, die ADAL.NET verwenden, vorab autorisiert sind, können Sie stets die folgenden Bereiche verwenden: `new string[] { $"{resourceId}/.default" }`.
4. Ersetzen Sie den Aufruf von `AuthenticationContext.AcquireTokenAsync` durch einen Aufruf von `IConfidentialClientApplication.AcquireTokenXXX`, wobei *XXX* von Ihrem Szenario abhängt.
