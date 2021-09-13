---
title: Benutzeridentitäten in AuthN/AuthZ
description: Hier erfahren Sie, wie Sie auf Benutzeridentitäten zugreifen, wenn Sie die integrierte Authentifizierung und Autorisierung in App Service verwenden.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: ae483e4213ec17cfa3a4532fbf615a40c760ad6e
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114230430"
---
# <a name="work-with-user-identities-in-azure-app-service-authentication"></a>Arbeiten mit Benutzeridentitäten bei der Authentifizierung in Azure App Service

In diesem Artikel wird erläutert, wie Sie Benutzeridentitäten verwenden können, wenn Sie die integrierte [Authentifizierung und Autorisierung in App Service](overview-authentication-authorization.md) nutzen. 

## <a name="access-user-claims-in-app-code"></a>Zugreifen auf Benutzeransprüche in App-Code

App Service stellt Ihrem Code für alle Sprachenframeworks die Ansprüche im eingehenden Token (ob dieses von einem authentifizierten Endbenutzer oder einer Clientanwendung stammt) zur Verfügung, indem sie in die Anforderungsheader eingefügt werden. Externe Anforderungen sind nicht zum Festlegen dieser Header berechtigt, sie sind also nur vorhanden, wenn sie von App Service festgelegt wurden. Beispiele für solche Header wären etwa:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Anhand dieser Header kann jeder Code unabhängig von Programmiersprache oder Framework die benötigten Informationen ermitteln. 

> [!NOTE]
> Verschiedene Sprachenframeworks zeigen diese Header im App-Code möglicherweise in verschiedenen Formaten an (klein geschrieben oder mit großen Anfangsbuchstaben).

Für ASP.NET 4.6-Apps füllt App Service die [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current)-Eigenschaft mit Ansprüchen des authentifizierten Benutzers, sodass Sie dem standardmäßigen .NET Codemuster einschließlich des `[Authorize]`-Attributs folgen können. Auf ähnliche Weise füllt App Service die `_SERVER['REMOTE_USER']`-Variable für PHP-Apps. Für Java-Apps sind die Ansprüche [über das Tomcat-Servlet zugänglich](configure-language-java.md#authenticate-users-easy-auth).

Für [Azure Functions](../azure-functions/functions-overview.md) wird `ClaimsPrincipal.Current` nicht für .NET Code aufgefüllt, Sie können die Benutzeransprüche jedoch weiterhin in den Anforderungsheadern finden oder das `ClaimsPrincipal`-Objekt aus dem Anforderungskontext oder selbst durch einen Bindungsparameter abrufen. Weitere Informationen finden Sie unter [Arbeiten mit Clientidentitäten](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities).

Für .NET Core unterstützt [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) das Auffüllen des aktuellen Benutzers mit der App Service-Authentifizierung. Weitere Informationen finden Sie im [Microsoft.Identity.Web-Wiki](https://github.com/AzureAD/microsoft-identity-web/wiki/1.2.0#integration-with-azure-app-services-authentication-of-web-apps-running-with-microsoftidentityweb), oder sehen Sie sich eine Demonstration dazu in diesem [Tutorial für eine Web-App, die auf Microsoft Graph zugreift](./scenario-secure-app-access-microsoft-graph-as-user.md?tabs=command-line#install-client-library-packages), an.

## <a name="access-user-claims-using-the-api"></a>Zugreifen auf Benutzeransprüche mithilfe der API

Wenn der [Tokenspeicher](overview-authentication-authorization.md#token-store) für Ihre App aktiviert ist, können Sie auch zusätzliche Details zum authentifizierten Benutzer abrufen, indem Sie `/.auth/me` aufrufen. Die Mobile Apps-Server-SDKs enthalten Hilfsmethoden für die Verwendung dieser Daten. Weitere Informationen finden Sie unter [Verwenden des Azure Mobile Apps SDK für Node.js](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity) sowie unter [Arbeiten mit dem .NET-Back-End-Server-SDK für Azure Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service](tutorial-auth-aad.md)