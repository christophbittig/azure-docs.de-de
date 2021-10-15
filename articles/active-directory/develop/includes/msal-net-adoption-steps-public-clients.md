---
title: Allgemeine Schritte für die Migration öffentlicher Clients zu MSAL
description: Schließen Sie eine Datei ein, in der die allgemeinen Schritte erläutert werden, die Sie für alle öffentlichen Client-Apps bei der Migration von ADAL zu MSAL ausführen müssen.
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: include
ms.date: 09/08/2021
ms.author: sahmalik
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 419b038f89df0d7fb5185fe2ef8ae43c76db7f7f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838197"
---
Die folgenden Schritte zum Aktualisieren von Code gelten für alle Szenarien mit vertraulichen Clients:

1. Fügen Sie den Namespace MSAL.NET Ihrem Quellcode hinzu: `using Microsoft.Identity.Client;`.
2. Anstatt `AuthenticationContext` zu instanziieren, verwenden Sie `PublicClientApplicationBuilder.Create`, um `IPublicClientApplication` zu instanziieren.
3. Anstelle der Zeichenfolge `resourceId` verwendet MSAL.NET Bereiche. Da Anwendungen, die ADAL.NET verwenden, vorab autorisiert sind, können Sie stets die folgenden Bereiche verwenden: `new string[] { $"{resourceId}/.default" }`.
4. Ersetzen Sie den Aufruf von `AuthenticationContext.AcquireTokenAsync` durch einen Aufruf von `IPublicClientApplication.AcquireTokenXXX`, wobei *XXX* von Ihrem Szenario abhängt.