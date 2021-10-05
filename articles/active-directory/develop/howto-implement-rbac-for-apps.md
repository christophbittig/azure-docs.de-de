---
title: Implementierung einer rollenbasierten Zugriffskontrolle in Anwendungen
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine rollenbasierte Zugriffskontrolle in Ihren Anwendungen implementieren.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 09/17/2021
ms.author: cchiedo
ms.reviewer: johngarland, mamarxen, ianbe, marsma
ms.openlocfilehash: ccbdbde261914e95470e37b0e2046a95e1fe5898
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700143"
---
# <a name="implement-role-based-access-control-in-apps"></a>Implementierung einer rollenbasierten Zugriffskontrolle in Anwendungen

Mit der rollenbasierten Zugriffskontrolle (RBAC) können Benutzer oder Gruppen bestimmte Berechtigungen erhalten, die sich darauf beziehen, auf welche Ressourcen sie Zugriff haben: was sie mit diesen Ressourcen tun können und wer welche Ressourcen verwaltet.

Wenn wir über die Implementierung von RBAC zum Schutz einer Ressource sprechen, geht es in der Regel darum, entweder eine Webanwendung, eine Single-Page-Webanwendung (SPA) oder eine API zu schützen.  Dies kann entweder für die gesamte Anwendung oder API oder für bestimmte Bereiche, Funktionen oder API-Methoden erfolgen.

In diesem Artikel wird erläutert, wie Sie eine anwendungsspezifische rollenbasierte Zugriffskontrolle implementieren.  Weitere Informationen zu den Grundlagen der Autorisierung finden Sie unter [Autorisierungsgrundlagen](./authorization-basics.md).

## <a name="implementing-rbac-using-the-microsoft-identity-platform"></a>Implementieren von RBAC unter Verwendung der Microsoft-Identitätsplattform

Wie in [Rollenbasierte Zugriffskontrolle für Anwendungsentwickler](./custom-rbac-for-developers.md) beschrieben, gibt es drei Möglichkeiten, RBAC mit der Microsoft Identitätsplattform zu implementieren:

- **App-Rollen** – Verwendung der [Funktion App-Rolle in einer Anwendungsregistrierung](./howto-add-app-roles-in-azure-ad-apps.md#declare-roles-for-an-application) in Verbindung mit einer Logik innerhalb Ihrer Anwendung, um eingehende App-Rollenzuweisungen zu interpretieren.
- **Gruppen** – Verwendung der Gruppenzuweisungen einer eingehenden Identität in Verbindung mit einer Logik innerhalb Ihrer Anwendung, um die Gruppenzuweisungen zu interpretieren. 
- **Benutzerdefinierter Datenspeicher** – Abrufen und Interpretieren von Rollenzuweisungen mithilfe von Logik innerhalb Ihrer Anwendung.

Der bevorzugte Ansatz ist die Verwendung von *App-Rollen*, da er am einfachsten zu implementieren ist. Dieser Ansatz wird direkt von den SDKs unterstützt, die bei der Erstellung von Anwendungen verwendet werden, die die Microsoft Identitätsplattform nutzen. Weitere Informationen über die Auswahl eines Ansatzes finden Sie unter [Auswählen eines Ansatzes](./custom-rbac-for-developers.md#choosing-an-approach).

Der Rest dieses Artikels zeigt Ihnen, wie Sie App-Rollen definieren und RBAC innerhalb Ihrer Anwendung mit Hilfe der App-Rollen implementieren.

## <a name="defining-roles-for-your-application"></a>Definieren von Rollen für Ihre Anwendung

Der erste Schritt zur Implementierung von RBAC für Ihre Anwendung besteht darin, die für Ihre Anwendung erforderlichen Rollen zu definieren und diesen Rollen Benutzer oder Gruppen zuzuweisen.  Dieser Prozess wird in [Hinzufügen von Anwendungsrollen zu Ihrer Anwendung und ihrer Annahme im Token](./howto-add-app-roles-in-azure-ad-apps.md). Nachdem Sie Ihre Rollen definiert und Benutzer oder Gruppen zugewiesen haben, können Sie auf die Rollenzuweisungen in den Token zugreifen, die in Ihrer Anwendung eingehen, und entsprechend handeln.

## <a name="implementing-rbac-in-aspnet-core"></a>Implementieren von RBAC in ASP.NET Core 

ASP.NET Core unterstützt das Hinzufügen von RBAC zu einer ASP.NET Core-Webanwendung oder Web-API.  Dies ermöglicht eine einfache Implementierung von RBAC unter Verwendung von [Rollenüberprüfungen](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks) mit dem ASP.NET Core Attribut *Erstellen*. Es ist auch möglich, die Unterstützung von ASP.NET Core für [richtlinienbasierte Rollenprüfungen](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#policy-based-role-checks) zu nutzen.

### <a name="aspnet-core-mvc-web-application"></a>Erstellen einer ASP.NET Core-MVC-Webanwendung 

Das Implementieren von RBAC in einer ASP.NET Core MVC Webanwendung ist unkompliziert.  Es beinhaltet hauptsächlich die Verwendung des Attributs *Erstellen*, um anzugeben, welche Rollen auf bestimmte Controller oder Aktionen in den Controllern zugreifen dürfen. Folgen Sie diesen Schritten, um RBAC in Ihrer ASP.NET Core MVC Anwendung zu implementieren:
1. Erstellen Sie eine App-Registrierung mit App-Rollen und Zuweisungen, wie es in *Definieren von Rollen für Ihre Anwendung* oben beschrieben ist.
1. Führen Sie einen der folgenden Schritte aus:
    - Erstellen Sie ein neues ASP.NET Core MVC Webanwendungsprojekt mit der **dotnet cli**.  Geben Sie das Flag *--auth* entweder mit *SingleOrg* für die Einzelmandant-Authentifizierung oder *MultiOrg* für Mehrfachmandant-Authentifizierung an, das Flag *--client-id* mit dem Client, wenn von Ihrer App-Registrierung, und das Flag *--tenant-id* mit Ihrem Mandanten, wenn von Ihrem Azure AD Mandanten:
 
      ```bash 
    
      dotnet new mvc --auth SingleOrg --client-id <YOUR-APPLICATION-CLIENT-ID> --tenant-id <YOUR-TENANT-ID>  
    
      ```
      
    - Fügen Sie die Bibliotheken Microsoft.Identity.Web und Microsoft.Identity.Web.Benutzeroberfläche zu einem bestehenden ASP.NET Core MVC-Projekt hinzu:
 
      ```bash 

      dotnet add package Microsoft.Identity.Web 

      dotnet add package Microsoft.Identity.Web.UI 

      ```

   Und folgen Sie dann den Anweisungen in [Schnellstart: Hinzufügen der Anmeldung mit Microsoft zu einer ASP.NET Core-Webanwendung](./quickstart-v2-aspnet-core-webapp.md?view=aspnetcore-5.0&preserve-view=true), um die Authentifizierung zu Ihrer Anwendung hinzuzufügen.
1. Fügen Sie Rollenprüfungen zu Ihren Controller-Aktionen hinzu, wie es in [Hinzufügen von Rollenüberprüfungen](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks) beschrieben ist.
1. Probieren Sie die Anwendung aus, indem Sie versuchen, auf eine der geschützten MVC-Routen zuzugreifen.

### <a name="aspnet-core-web-api"></a>ASP.NET Core-Web-API

Die Implementierung von RBAC in einerASP.NET Core-Web-API beinhaltet hauptsächlich die Verwendung des Attributs *Erstellen*, um festzulegen, welche Rollen auf bestimmte Controller oder Aktionen in den Controllern zugreifen dürfen. Folgen Sie diesen Schritten, um RBAC in Ihrer ASP.NET Core-Web-API zu implementieren:
1. Erstellen Sie eine App-Registrierung mit App-Rollen und Zuweisungen, wie es in *Definieren von Rollen für Ihre Anwendung* oben beschrieben ist.
1. Führen Sie einen der folgenden Schritte aus:
    - Verwenden Sie **dotnet cli**, um ein neues ASP.NET Core MVC Web API-Projekt zu erstellen.  Geben Sie das Flag *--auth* entweder mit *SingleOrg* für die Einzelmandanten-Authentifizierung oder *MultiOrg* für die Mehrfachmandanten-Authentifizierung an, das Flag *--client-id* mit dem Client, wenn es von Ihrer App-Registrierung stammt, und das Flag *--tenant-id* mit Ihrem Mandanten, wenn es von Ihrem Azure AD-Mandanten stammt:

      ```bash 
    
      dotnet new webapi --auth SingleOrg --client-id <YOUR-APPLICATION-CLIENT-ID> --tenant-id <YOUR-TENANT-ID> 
    
      ```

    - Fügen Sie die Microsoft.Identity.Web und Swashbuckle.AspNetCore-Bibliotheken zu einem bestehenden ASP.NET Core Web API-Projekt hinzu:
      
      ```bash 

      dotnet add package Microsoft.Identity.Web 

      dotnet add package Swashbuckle.AspNetCore 

      ```
    
   Folgen Sie dann den Anweisungen in [Schnellstart: Hinzufügen der Anmeldung mit Microsoft zu einer ASP.NET Core-Webanwendung](./quickstart-v2-aspnet-core-webapp.md?view=aspnetcore-5.0&preserve-view=true), um die Authentifizierung zu Ihrer Anwendung hinzuzufügen.
1. Fügen Sie Rollenüberprüfungen zu Ihren Controller-Aktionen hinzu, wie es in [Rollenüberprüfungen hinzufügen](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks) beschrieben ist.
1. Aufrufen der API von einer Client-Anwendung aus.  Siehe [Aufrufen der Angular Single-Page-Webanwendung mit .NET Core Web-API und Implementieren der rollenbasierten Zugriffssteuerung mit Anwendungsrollen](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/1-call-api-roles) für ein End-to-End-Beispiel.


## <a name="implementing-rbac-in-other-platforms"></a>Implementieren von RBAC auf anderen Plattformen

### <a name="angular-spa-using-msalguard"></a>Angular SPA mit MsalGuard
Die Implementierung von RBAC in einer Angular SPA beinhaltet die Verwendung von [msal-angular](https://www.npmjs.com/package/@azure/msal-angular) zur Autorisierung des Zugriffs auf die in der Anwendung enthaltenen Angular-Routen.  Dies wird in dem Beispiel [Aktivieren Sie Ihre Angular Single-Page-Webanwendung, um Benutzer anzumelden und APIs mit der Microsoft Identitätsplattform](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial#chapter-5-control-access-to-your-protected-api-using-app-roles-and-security-groups) gezeigt.

> [!NOTE]
> Client-seitige RBAC-Implementierungen sollten mit server-seitigem RBAC gepaart werden, um zu verhindern, dass nicht autorisierte Anwendungen auf sensible Ressourcen zugreifen.

### <a name="nodejs-with-express-application"></a>Node.js mit Express-Anwendung
Die Implementierung von RBAC in einer Node.js mit Express-Anwendung umfasst die Verwendung von MSAL zur Autorisierung des Zugriffs auf die in der Anwendung enthaltenen Express-Routen.  Dies wird gezeigt im Beispiel [Aktivieren Ihrer Node.js-Webanwendung, um Benutzer anzumelden, und Aufrufen von APIs mit dem Microsoft Identitätsplattform](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial#chapter-4-control-access-to-your-app-using-app-roles-and-security-groups).

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie mehr über [Berechtigungen und Einwilligung in der Microsoft Identitätsplattform](./v2-permissions-and-consent.md).
- Lesen Sie mehr über [rollenbasierte Zugriffskontrolle für Anwendungsentwickler](./custom-rbac-for-developers.md).
