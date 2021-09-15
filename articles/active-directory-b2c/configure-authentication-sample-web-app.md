---
title: Konfigurieren der Authentifizierung in einer Beispielwebanwendung mithilfe von Azure Active Directory B2C
description: In diesem Artikel wird erörtert, wie Sie Azure Active Directory B2C für die Anmeldung und Registrierung von Benutzern in einer ASP.NET-Webanwendung verwenden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/23/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: b05aa59f465eafd65fa111fff811d2f0909f601d
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123471856"
---
# <a name="configure-authentication-in-a-sample-web-app-by-using-azure-ad-b2c"></a>Konfigurieren der Authentifizierung in einem Beispiel einer ASP.NET-Webanwendung mithilfe von Azure AD B2C

In diesem Artikel wird anhand einer ASP.NET-Beispielwebanwendung veranschaulicht, wie Sie Ihren Webanwendungen die Authentifizierung von Azure Active Directory B2C (Azure AD B2C) hinzufügen.

> [!IMPORTANT]
> Auf die in diesem Artikel verwiesene ASP.NET-Beispiel-Web-App kann nicht zum Aufrufen einer REST-API verwendet werden, weil sie ein ID-Token, aber kein Zugriffstoken zurückgibt. Informationen zu einer Web-App, die eine REST-API aufrufen kann, finden Sie unter [Sichern einer Web-API, die mit ASP.NET Core mithilfe von Azure AD B2C erstellt wurde](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).  

## <a name="overview"></a>Übersicht

OpenID Connect (OIDC) ist ein Authentifizierungsprotokoll, das auf OAuth 2.0 basiert. Sie können OIDC verwenden, um Benutzer sicher bei einer Anwendung anzumelden. Für dieses Web-App-Beispiel wird [Microsoft Identity Web](https://www.nuget.org/packages/Microsoft.Identity.Web) verwendet. Bei Microsoft Identity Web handelt es sich um eine Reihe von ASP.NET Core-Bibliotheken, die das Hinzufügen von Authentifizierungs- und Autorisierungsunterstützung zu Web-Apps vereinfachen. 

Der Anmeldeflow umfasst die folgenden Schritte:

1. Benutzer navigieren zur Web-App und wählen **Anmelden** aus. 
1. Die App löst eine Authentifizierungsanforderung aus und leitet Benutzer zu Azure AD B2C um.
1. Benutzer [registrieren oder melden sich an](add-sign-up-and-sign-in-policy.md) und [setzen das Kennwort](add-password-reset-policy.md) zurück. Alternativ können sie sich mit einem [Social Media-Konto](add-identity-provider.md) anmelden.
1. Nachdem die Benutzer sich erfolgreicher angemeldet haben, gibt Azure AD B2C ein ID-Token an die App zurück.
1. Die App überprüft das ID-Token, liest die Ansprüche und gibt eine sichere Seite an die Benutzer zurück.

Wenn das ID-Token abgelaufen ist oder die App-Sitzung ungültig wird, initiiert die App eine neue Authentifizierungsanforderung und leitet die Benutzer zu Azure AD B2C um. Wenn die Azure AD B2C-[SSO-Sitzung](session-behavior.md) aktiv ist, gibt Azure AD B2C ein Zugriffstoken aus, ohne die Benutzer zur erneuten Anmeldung aufzufordern. Wenn die Azure AD B2C-Sitzung abläuft oder ungültig wird, werden die Benutzer aufgefordert, sich erneut anzumelden.

### <a name="sign-out"></a>Abmeldung

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)] 

## <a name="prerequisites"></a>Voraussetzungen

Ein Computer mit einem der folgenden Betriebssysteme ausgeführt wird: 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 Version 16.8 oder höher](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload für ASP.NET und Webentwicklung
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# für Visual Studio Code (neueste Version)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

---

## <a name="step-1-configure-your-user-flow"></a>Schritt 1: Konfigurieren Ihres Benutzerflows

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-a-web-application"></a>Schritt 2: Registrieren einer Webanwendung

Damit sich Ihre Anwendung mit Azure AD B2C anmelden kann, müssen Sie Ihre App im Azure AD B2C-Verzeichnis registrieren. Durch das Registrieren Ihrer App wird eine Vertrauensstellung zwischen der App und Azure AD B2C eingerichtet.  

Bei der App-Registrierung geben Sie den *Umleitungs-URI* an. Der Umleitungs-URI ist der Endpunkt, an den die Benutzer von Azure AD B2C umgeleitet werden, nachdem sie sich mit Azure AD B2C authentifiziert haben. Beim App-Registrierungsprozess wird eine *Anwendungs-ID* generiert, die auch als *Client-ID* bezeichnet wird und Ihre App eindeutig identifiziert. Nachdem Ihre App registriert wurde, verwendet Azure AD B2C sowohl die Anwendungs-ID als auch den Umleitungs-URI, um Authentifizierungsanforderungen zu erstellen. 

### <a name="step-21-register-the-app"></a>Schritt 2.1: Registrieren der App

Gehen Sie zum Erstellen der Web-App-Registrierung folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Suchen Sie nach **Azure AD B2C**, und wählen Sie diese Option aus.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein, (z. B. *webapp1*).
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Identitätsanbieter oder Organisationsverzeichnis (zum Authentifizieren von Benutzern mit Benutzerflows)** aus. 
1. Wählen Sie unter **Umleitungs-URI** die Option **Web** aus, und geben Sie `https://localhost:5001/signin-oidc` in das URL-Feld ein.
1. Aktivieren Sie unter **Berechtigungen** das Kontrollkästchen **Administratoreinwilligung für OpenID- und Offlinezugriffsberechtigungen erteilen**.
1. Wählen Sie **Registrieren**.
1. Wählen Sie **Übersicht**.
1. Notieren Sie sich die **Anwendungs- bzw. Client-ID**, die Sie später beim Konfigurieren der Webanwendung verwenden.

    ![Screenshot der Seite „Übersicht“ der Web-App zum Aufzeichnen Ihrer Webanwendungs-ID.](./media/configure-authentication-sample-web-app/get-azure-ad-b2c-app-id.png)  


### <a name="step-22-enable-id-tokens"></a>Schritt 2.2: Aktivieren von ID-Token

Aktivieren Sie für Web-Apps, die ein ID-Token direkt von Azure AD B2C anfordern, in der App-Registrierung den Flow zur impliziten Genehmigung.

1. Wählen Sie im linken Bereich unter **Verwalten** die Option **Authentifizierung** aus.
1. Aktivieren Sie unter **Implizite Gewährung** die Kontrollkästchen **ID-Token (für implizite und hybride Flüsse verwendet)** und **Zugriffstoken (für implizite Flüsse verwendet)** .
1. Wählen Sie **Speichern** aus.

## <a name="step-3-get-the-web-app-sample"></a>Schritt 3: Herunterladen des Web-App-Beispiels

[Laden Sie die ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/refs/heads/master.zip), oder klonen Sie die Beispielwebanwendung von GitHub. 

```bash
git clone https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2
```

Extrahieren Sie die Beispieldatei in einen Ordner, bei dem die Gesamtlänge des Pfads 260 oder weniger Zeichen oder umfasst.

## <a name="step-4-configure-the-sample-web-app"></a>Schritt 4: Konfigurieren der Beispiel-Web-App

Öffnen Sie im Beispielordner in dem Ordner *1-WebApp-OIDC/1-5-B2C/*  das Projekt **WebApp-OpenIDConnect-DotNet.csproj** mit Visual Studio oder Visual Studio Code. 

Öffnen Sie die Datei *appsettings.json* in dem Projektstammordner. Diese Datei enthält Informationen zu Ihrem Azure AD B2C-Identitätsanbieter. Aktualisieren Sie die folgenden Eigenschaften der App-Einstellungen: 

|`Section`  |Key  |Wert  |
|---------|---------|---------|
|AzureAdB2C|Instanz| Der erste Teil Ihres Azure AD B2C-[Mandantennamens](tenant-management.md#get-your-tenant-name) (z. B. `https://contoso.b2clogin.com`).|
|AzureAdB2C|Domain| Der vollständige Name Ihres Azure AD B2C-[Mandanten](tenant-management.md#get-your-tenant-name) (z. B. `contoso.onmicrosoft.com`).|
|AzureAdB2C|ClientId| Die Web-API-Anwendungs-ID aus [Schritt 2](#step-2-register-a-web-application).|
|AzureAdB2C|SignUpSignInPolicyId|Die Benutzerflows oder die benutzerdefinierte Richtlinie, die Sie in [Schritt 1](#step-1-configure-your-user-flow) erstellt haben.|

Die endgültige Konfigurationsdatei sollte wie die folgende JSON aussehen:

```JSON
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "contoso.onmicrosoft.com",
  "ClientId": "<web-app-application-id>",
  "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
  "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
}
```

## <a name="step-5-run-the-sample-web-app"></a>Schritt 5: Ausführen der Beispiel-Web-App

1. Erstellen Sie das Projekt, und führen Sie es aus.
1. Navigieren Sie zu `https://localhost:5001`. 
1. Wählen Sie **Registrieren/Anmelden** aus.

    ![Ein Screenshot, der die Schaltfläche „Registrieren/Anmelden“ auf der Willkommensseite des Projekts zeigt.](./media/configure-authentication-sample-web-app/web-app-sign-in.png)

1. Schließen Sie den Registrierungs- oder Anmeldevorgang ab.

Nach der erfolgreichen Authentifizierung wird Ihr Anzeigename in der Navigationsleiste angezeigt. Wenn Sie die Ansprüche anzeigen möchten, die das Azure AD B2C-Token an Ihre App zurückgibt, wählen Sie **Ansprüche** aus.

![Ein Screenshot, der die Token-Ansprüche der Web-App zeigt.](./media/configure-authentication-sample-web-app/web-app-token-claims.png)

## <a name="deploy-your-application"></a>Bereitstellen der Anwendung 

In einer Produktionsanwendung handelt es sich bei dem Umleitungs-URI der App-Registrierung in der Regel um einen öffentlich zugänglichen Endpunkt, an dem Ihre App ausgeführt wird, z. B. `https://contoso.com/signin-oidc`. 

Sie können Umleitungs-URIs in Ihren registrierten Anwendungen jederzeit hinzufügen und ändern. Für Umleitungs-URIs gelten die folgenden Einschränkungen:

* Die Antwort-URL muss mit dem Schema `https` beginnen.
* Bei der Antwort-URL muss die Groß-/Kleinschreibung beachtet werden. Die Groß-/Kleinschreibung muss der Groß-/Kleinschreibung des URL-Pfads Ihrer ausgeführten Anwendung entsprechen. 

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum [Codebeispiel](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C#about-the-code).
* Erfahren Sie wie Sie die[ Authentifizierung in Ihrer eigenen Web-App mit Azure Active Directory B2C aktivieren](enable-authentication-web-application.md).
