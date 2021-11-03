---
title: Konfigurieren der Authentifizierung in einer Beispielwebanwendung, die eine Web-API mithilfe von Azure Active Directory B2C aufruft
description: In diesem Artikel wird die Verwendung von Azure Active Directory B2C für die Anmeldung und Registrierung von Benutzern in einer ASP.NET-Webanwendung besprochen, die eine Web-API aufruft.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: f2f0f00bfc3b5f551d60817e3ea52924c78d98d8
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007781"
---
# <a name="configure-authentication-in-a-sample-web-app-that-calls-a-web-api-by-using-azure-ad-b2c"></a>Konfigurieren der Authentifizierung in einer Beispiel-Web-App, die eine Web-API mithilfe von Azure AD B2C aufruft

In diesem Artikel wird anhand einer ASP.NET-Beispielwebanwendung, die eine Web-API aufruft, veranschaulicht, wie Sie Ihren Webanwendungen die Authentifizierung von Azure Active Directory B2C (Azure AD B2C) hinzufügen.

> [!IMPORTANT]
> Die beispielhafte ASP.NET-Web-App, auf die in diesem Artikel verwiesen wird, wird genutzt, um eine Web-API mit einem Bearertoken aufzurufen. Informationen zu einer Web-App, die keine Web-API aufruft, finden Sie unter [Konfigurieren der Authentifizierung in einem Beispiel einer ASP.NET-Webanwendung mithilfe von Azure AD B2C](configure-authentication-sample-web-app.md).  

## <a name="overview"></a>Übersicht

OpenID Connect (OIDC) ist ein Authentifizierungsprotokoll, das auf OAuth 2.0 basiert. Sie können mit OIDC einen Benutzer sicher bei einer Anwendung anmelden. Für dieses Web-App-Beispiel wird [Microsoft Identity Web](https://www.nuget.org/packages/Microsoft.Identity.Web) verwendet. Microsoft Identity Web ist ein Satz von ASP.NET Core-Bibliotheken, die das Hinzufügen von Authentifizierungs- und Autorisierungsunterstützung zu Web-Apps vereinfachen, die eine sichere Web-API aufrufen können. 

Der Anmeldeflow umfasst die folgenden Schritte:

1. Benutzer navigieren zur Web-App und wählen **Anmelden** aus.
1. Die App löst eine Authentifizierungsanforderung aus und leitet Benutzer an Azure AD B2C um.
1. Benutzer [registrieren oder melden sich an](add-sign-up-and-sign-in-policy.md) und [setzen das Kennwort](add-password-reset-policy.md) zurück. Alternativ können sie sich mit einem [Social Media-Konto](add-identity-provider.md) anmelden.
1. Nach erfolgreicher Benutzeranmeldung gibt Azure AD B2C einen Autorisierungscode an die App zurück.
1. Die App führt anschließend folgende Aufgaben aus:
 
   a. Sie tauscht den Autorisierungscode gegen ein ID-Token, ein Zugriffstoken und ein Aktualisierungstoken ein.  
   b. Sie liest die ID-Tokenansprüche und behält ein Anwendungsautorisierungscookie bei.  
   c. Sie speichert das Aktualisierungstoken zur späteren Verwendung in einem In-Memory-Cache.

### <a name="app-registration-overview"></a>Übersicht über die App-Registrierung

Damit sich Ihre App bei Azure AD B2C anmelden und eine Web-API aufrufen kann, registrieren Sie im Azure AD B2C-Verzeichnis zwei Anwendungen.  

- Durch die *Webanwendungsregistrierung* kann sich Ihre App bei Azure AD B2C anmelden. Während der Registrierung geben Sie den *Umleitungs-URI* an. Der Umleitungs-URI ist der Endpunkt, an den Benutzer von Azure AD B2C umgeleitet werden, nachdem die Authentifizierung mithilfe von Azure AD B2C abgeschlossen ist. Beim App-Registrierungsprozess wird eine *Anwendungs-ID* generiert, die auch als *Client-ID* bezeichnet wird und Ihre App eindeutig identifiziert. Außerdem erstellen Sie einen *geheimen Clientschlüssel*, der von Ihrer App zum sicheren Abrufen der Token verwendet wird.

- Durch die *Web-API*-Registrierung kann Ihre App eine sichere Web-API aufrufen. Die Registrierung umfasst die Web-API-*Bereiche*. Die Bereiche bieten eine Möglichkeit, Berechtigungen für geschützte Ressourcen wie Ihre Web-API zu verwalten. Sie erteilen der Webanwendung Berechtigungen für die Bereiche der Web-API. Wenn ein Zugriffstoken angefordert wird, gibt Ihre App im Bereichsparameter der Anforderung die gewünschten Berechtigungen an.  

App-Architektur und -Registrierungen sind im folgenden Diagramm dargestellt:

![Diagramm einer Web-App mit Web-API-Aufrufregistrierungen und Token.](./media/configure-authentication-sample-web-app-with-api/web-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Aufrufen einer Web-API

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>Abmeldung

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Voraussetzungen

Ein Computer, auf dem eine der folgenden Optionen ausgeführt wird: 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 Version 16.8 oder höher](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET und Webentwicklung**
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# für Visual Studio Code (neueste Version)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

---

## <a name="step-1-configure-your-user-flow"></a>Schritt 1: Konfigurieren Ihres Benutzerflows

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-web-applications"></a>Schritt 2: Registrieren von Webanwendungen

In diesem Schritt erstellen Sie die Web-App und die Web-API-Anwendungsregistrierung und geben die Bereiche Ihrer Web-API an.

### <a name="step-21-register-the-web-api-app"></a>Schritt 2.1: Registrieren der Web-API-App

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-22-configure-web-api-app-scopes"></a>Schritt 2.2: Konfigurieren von Bereichen für die Web-API-App

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="step-23-register-the-web-app"></a>Schritt 2.3: Registrieren der Web-App

Gehen Sie zum Erstellen der Web-App-Registrierung folgendermaßen vor:

1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein, (z. B. *webapp1*).
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Identitätsanbieter oder Organisationsverzeichnis (zum Authentifizieren von Benutzern mit Benutzerflows)** aus. 
1. Wählen Sie unter **Umleitungs-URI** die Option **Web** aus, und geben Sie `https://localhost:5000/signin-oidc` in das URL-Feld ein.
1. Aktivieren Sie unter **Berechtigungen** das Kontrollkästchen **Administratoreinwilligung für OpenID- und Offlinezugriffsberechtigungen erteilen**.
1. Wählen Sie **Registrieren**.
1. Wählen Sie nach Abschluss der App-Registrierung **Übersicht** aus.
1. Notieren Sie sich die **Anwendungs- bzw. Client-ID**, die Sie später beim Konfigurieren der Webanwendung verwenden.

    ![Screenshot: Seite „Übersicht“ der Web-App zum Aufzeichnen Ihrer Webanwendungs-ID](./media/configure-authentication-sample-web-app-with-api/get-azure-ad-b2c-app-id.png)  

### <a name="step-24-create-a-web-app-client-secret"></a>Schritt 2.4: Erstellen eines geheimen Clientschlüssels für die Web-App

[!INCLUDE [active-directory-b2c-app-integration-client-secret](../../includes/active-directory-b2c-app-integration-client-secret.md)]


### <a name="step-25-grant-the-web-app-permissions-for-the-web-api"></a>Schritt 2.5: Erteilen von Berechtigungen für die Web-App zur Verwendung der Web-API

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-web-app-sample"></a>Schritt 3: Herunterladen des Web-App-Beispiels

[Laden Sie die ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/refs/heads/master.zip), oder führen Sie den folgenden Bash-Befehl aus, um die Beispielwebanwendung aus GitHub zu klonen. 

```bash
git clone https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2
```

Extrahieren Sie die Beispieldatei in einen Ordner, bei dem der Pfad eine Gesamtlänge von maximal 260 Zeichen aufweist.

## <a name="step-4-configure-the-sample-web-api"></a>Schritt 4: Konfigurieren der Beispiel-Web-API

Öffnen Sie im Beispielordner unter dem Ordner *4-WebApp-your-API/4-2-B2C/TodoListService* das Projekt **TodoListService.csproj** mit Visual Studio oder Visual Studio Code. 

Öffnen Sie im Projektstammordner die Datei *appsettings.json*. Diese Datei enthält Informationen zu Ihrem Azure AD B2C-Identitätsanbieter. Die Web-API-App überprüft anhand dieser Informationen das Zugriffstoken, das die Web-App als Bearertoken übergibt. Aktualisieren Sie die folgenden Eigenschaften der App-Einstellungen:

| `Section` | Key | Wert |
| --- | --- | --- |
|AzureAdB2C|Instanz| Der erste Teil Ihres Azure AD B2C-[Mandantennamens](tenant-management.md#get-your-tenant-name). Beispielsweise `https://contoso.b2clogin.com`.|
|AzureAdB2C|Domain| Der vollständige [Mandantenname](tenant-management.md#get-your-tenant-name) Ihres Azure AD B2C-Mandanten. Beispielsweise `contoso.onmicrosoft.com`.|
|AzureAdB2C|ClientId| Die Web-API-Anwendungs-ID aus [Schritt 2.1](#step-21-register-the-web-api-app).|
|AzureAdB2C|SignUpSignInPolicyId|Die Benutzerflows oder die benutzerdefinierte Richtlinie, die Sie in [Schritt 1](#step-1-configure-your-user-flow) erstellt haben.|
| | | |

Die endgültige Konfigurationsdatei sollte wie das folgende JSON-Beispiel aussehen:

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com",
    "Domain": "contoso.onmicrosoft.com",
    "ClientId": "<web-api-app-application-id>",
    "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
    "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
  },
  // More settings here
}
```

### <a name="step-41-set-the-permission-policy"></a>Schritt 4.1: Festlegen der Berechtigungsrichtlinie

Die Web-API überprüft, ob sich der Benutzer mit dem Bearertoken authentifiziert hat, und das Bearertoken verfügt über die konfigurierten akzeptierten Bereiche. Wenn das Bearertoken über keinen dieser akzeptierten Bereiche verfügt, gibt die Web-API den HTTP-Statuscode 403 (Verboten) zurück und schreibt eine Meldung in den Antworttext, welche Bereiche im Token erwartet werden. 

Öffnen Sie zum Konfigurieren der akzeptierten Bereiche die `Controller/TodoListController.cs`-Klasse, und legen Sie den Bereichsnamen ohne vollständigen URI fest.

```csharp
[RequiredScope("tasks.read")]
```

### <a name="step-42-run-the-sample-web-api-app"></a>Schritt 4.2: Ausführen der Beispiel-Web-API-App

Damit die Web-App das Web-API-Beispiel aufrufen kann, führen Sie die Web-API wie folgt aus:

1. Wenn Sie dazu aufgefordert werden, stellen Sie Abhängigkeiten wieder her.
1. Erstellen Sie das Projekt, und führen Sie es aus.
1. Nachdem das Projekt erstellt wurde, startet Visual Studio oder Visual Studio Code die Web-API in den Browsern mit der folgenden Adresse: https://localhost:44332.

## <a name="step-5-configure-the-sample-web-app"></a>Schritt 5: Konfigurieren der Beispiel-Web-App

Öffnen Sie im Beispielordner unter dem Ordner `4-WebApp-your-API/4-2-B2C/Client` das Projekt **TodoListClient.csproj** mit Visual Studio oder Visual Studio Code. 

Öffnen Sie im Projektstammordner die Datei `appsettings.json`. Diese Datei enthält Informationen zu Ihrem Azure AD B2C-Identitätsanbieter. Die Web-App verwendet diese Informationen, um eine Vertrauensstellung mit Azure AD B2C einzurichten, den Benutzer anzumelden und abzumelden sowie Token abzurufen und zu überprüfen. Aktualisieren Sie die folgenden Eigenschaften der App-Einstellungen:

| `Section` | Key | Wert |
| --- | --- | --- |
| AzureAdB2C | Instanz | Der erste Teil Ihres Azure AD B2C-[Mandantennamens](tenant-management.md#get-your-tenant-name) (z. B. `https://contoso.b2clogin.com`).|
|AzureAdB2C|Domain| Der vollständige Name Ihres Azure AD B2C-[Mandanten](tenant-management.md#get-your-tenant-name) (z. B. `contoso.onmicrosoft.com`).|
|AzureAdB2C|ClientId| Die Webanwendungs-ID aus [Schritt 2.3](#step-23-register-the-web-app).|
|AzureAdB2C | ClientSecret | Das Geheimnis der Webanwendung aus [Schritt 2.4](#step-24-create-a-web-app-client-secret). | 
|AzureAdB2C|SignUpSignInPolicyId|Die Benutzerflows oder die benutzerdefinierte Richtlinie, die Sie in [Schritt 1](#step-1-configure-your-user-flow) erstellt haben.|
| Aufgabenliste | TodoListScope | Die Web-API-Bereiche, die Sie in [Schritt 2.5](#step-25-grant-the-web-app-permissions-for-the-web-api) erstellt haben.|
| Aufgabenliste | TodoListBaseAddress | Der Basis-URI Ihrer Web-API (z. B. `https://localhost:44332`). |
| | | |

Die endgültige Konfigurationsdatei sollte wie die folgende JSON aussehen:

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com",
    "Domain": "contoso.onmicrosoft.com",
    "ClientId": "<web-app-application-id>",
    "ClientSecret": "<web-app-application-secret>",  
    "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
    "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
  },
  "TodoList": {
    "TodoListScope": "https://contoso.onmicrosoft.com/api/demo.read",
    "TodoListBaseAddress": "https://localhost:44332"
  }
}
```


## <a name="step-6-run-the-sample-web-app"></a>Schritt 6: Ausführen der Beispiel-Web-App

1. Erstellen Sie das Projekt, und führen Sie es aus.
1. Navigieren Sie zu `https://localhost:5000`. 
1. Schließen Sie den Registrierungs- oder Anmeldevorgang ab.

Nach erfolgreicher Authentifizierung wird Ihr Anzeigename in der Navigationsleiste angezeigt. Wählen Sie **TodoList** aus, um die Ansprüche anzuzeigen, die das Azure AD B2C-Token an Ihre App zurückgibt.

![Screenshot: Tokenansprüche der Web-App](./media/configure-authentication-sample-web-app-with-api/web-api-to-do-list.png)


## <a name="deploy-your-application"></a>Bereitstellen der Anwendung

In einer Produktionsanwendung handelt es sich bei dem Umleitungs-URI der App-Registrierung in der Regel um einen öffentlich zugänglichen Endpunkt, an dem Ihre App ausgeführt wird, wie beispielsweise `https://contoso.com/signin-oidc`. 

Sie können Umleitungs-URIs in Ihren registrierten Anwendungen jederzeit hinzufügen und ändern. Für Umleitungs-URIs gelten die folgenden Einschränkungen:

* Die Antwort-URL muss mit dem Schema `https` beginnen.
* Bei der Antwort-URL muss die Groß-/Kleinschreibung beachtet werden. Die Groß-/Kleinschreibung muss der Groß-/Kleinschreibung des URL-Pfads Ihrer ausgeführten Anwendung entsprechen. 

### <a name="token-cache-for-a-web-app"></a>Tokencache für eine Web-App

Das Web-App-Beispiel verwendet In-Memory-Tokencacheserialisierung. Diese Implementierung eignet sich hervorragend für Beispiele. Sie eignet sich auch für Produktionsanwendungen, sofern es keine Rolle spielt, dass der Tokencache beim Neustart der Web-App verloren geht. 

Für Produktionsumgebungen wird empfohlen, einen verteilten Speichercache zu verwenden, z. B. Redis Cache, NCache oder einen SQL Server-Cache. Ausführliche Informationen zu den Implementierungen des verteilten Speichercaches finden Sie unter [Serialisierung des Tokencaches](../active-directory/develop/msal-net-token-cache-serialization.md).


## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum [Codebeispiel](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C#about-the-code)
* Weitere Informationen zum [Aktivieren der Authentifizierung in Ihrer eigenen Webanwendung mit Azure Active Directory B2C](enable-authentication-web-application.md)
* Weitere Informationen zum [Aktivieren der Authentifizierung in Ihrer eigenen Web-API](enable-authentication-web-api.md)
