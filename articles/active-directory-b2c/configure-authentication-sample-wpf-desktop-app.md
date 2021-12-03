---
title: Konfigurieren der Authentifizierung in einer WPF-Beispieldesktopanwendung mit Azure Active Directory B2C
description: In diesem Artikel wird erläutert, wie Sie Azure Active Directory B2C für die Anmeldung und Registrierung von Benutzer*innen in einer WPF-Desktopanwendung verwenden.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: d10fd1586ef60bb0c216e8284fa7e6bf44804217
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007613"
---
# <a name="configure-authentication-in-a-sample-wpf-desktop-app-by-using-azure-ad-b2c"></a>Konfigurieren der Authentifizierung in einem Beispiel einer WPF-Desktopanwendung mithilfe von Azure AD B2C

In diesem Artikel wird anhand einer [WPF-Beispieldesktopanwendung (Windows Presentation Foundation)](/visualstudio/designers/getting-started-with-wpf) veranschaulicht, wie Sie die Azure AD B2C-Authentifizierung für Ihre Desktop-Apps einrichten.

## <a name="overview"></a>Übersicht

OpenID Connect (OIDC) ist ein Authentifizierungsprotokoll, das auf OAuth 2.0 basiert. Sie können OIDC verwenden, um Benutzer sicher bei einer Anwendung anzumelden. In diesem Beispiel für eine Desktop-App wird die [Microsoft Authentication Library (MSAL)](../active-directory/develop/msal-overview.md) mit dem OIDC-Autorisierungscode Proof Key for Code Exchange (PKCE) verwendet. MSAL ist eine von Microsoft bereitgestellte Bibliothek, die das Hinzufügen von Authentifizierungs- und Autorisierungsunterstützung zu Desktop-Apps vereinfacht. 

Der Anmeldeflow umfasst die folgenden Schritte:

1. Benutzer*innen öffnen die App und wählen **Anmelden** aus.
1. Die App öffnet den Systembrowser des Desktopgeräts und sendet eine Authentifizierungsanforderung an Azure AD B2C.
1. Benutzer [registrieren oder melden sich an](add-sign-up-and-sign-in-policy.md), [setzen das Passwort zurück](add-password-reset-policy.md), oder melden sich mit einem [Social Account](add-identity-provider.md) an.
1. Nach erfolgreicher Benutzeranmeldung gibt Azure AD B2C einen Autorisierungscode an die App zurück.
1. Die App führt die folgenden Aktionen aus:
    1. Sie tauscht den Autorisierungscode gegen ein ID-Token, ein Zugriffstoken und ein Aktualisierungstoken ein.
    1. Sie liest die ID-Tokenansprüche.
    1. Sie speichert die Token zur späteren Verwendung in einem In-Memory-Cache.

### <a name="app-registration-overview"></a>Übersicht über die App-Registrierung

Damit sich Ihre App bei Azure AD B2C anmelden und eine Web-API aufrufen kann, registrieren Sie im Azure AD B2C-Verzeichnis zwei Anwendungen.  

- Durch die Registrierung der **Desktopanwendung** kann sich Ihre App bei Azure AD B2C anmelden. Bei der App-Registrierung geben Sie den *Umleitungs-URI* an. Der Umleitungs-URI ist der Endpunkt, an den die Benutzer*innen von Azure AD B2C umgeleitet werden, nachdem sie sich mit Azure AD B2C authentifiziert haben. Beim App-Registrierungsprozess wird eine *Anwendungs-ID* generiert, die auch als *Client-ID* bezeichnet wird und Ihre Desktop-App eindeutig identifiziert (z. B. *App-ID: 1*).

- Durch die **Web-API**-Registrierung kann Ihre App eine geschützte Web-API aufrufen. Die Registrierung macht die Web-API-Berechtigungen (Bereiche) verfügbar. Beim App-Registrierungsprozess wird eine *Anwendungs-ID* generiert, die Ihre Web-API eindeutig identifiziert (Beispiel: *App-ID: 2*). Erteilen Sie Ihrer Desktop-App (App-ID: 1) Berechtigungen für die Web-API-Bereiche (App-ID: 2). 

Die Anwendungsregistrierung und -architektur werden in den folgenden Diagrammen dargestellt:

![Diagramm für eine Desktop-App mit Web-API, Registrierungen und Token](./media/configure-authentication-sample-wpf-desktop-app/desktop-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Aufrufen einer Web-API

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="the-sign-out-flow"></a>Abmeldeflow

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Voraussetzungen

Ein Computer, auf dem [Visual Studio 2019](https://www.visualstudio.com/downloads/) mit der Workload „.NET-Desktopentwicklung“ ausgeführt wird

## <a name="step-1-configure-your-user-flow"></a>Schritt 1: Konfigurieren Ihres Benutzerflows

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-applications"></a>Schritt 2: Registrieren Ihrer Anwendungen

Erstellen Sie die Desktop-App und die Web-API-Anwendungsregistrierung, und geben die Bereiche Ihrer Web-API an.

### <a name="step-21-register-the-web-api-app"></a>Schritt 2.1: Registrieren der Web-API-App

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-22-configure-web-api-app-scopes"></a>Schritt 2.2: Konfigurieren von Bereichen für die Web-API-App

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="step-23-register-the-desktop-app"></a>Schritt 2.3: Registrieren der Desktop-App

Gehen Sie zum Registrieren der Desktop-App folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein (z. B. *desktop-app1*).
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Identitätsanbieter oder Organisationsverzeichnis (zum Authentifizieren von Benutzern mit Benutzerflows)** aus. 
1. Wählen Sie unter **Umleitungs-URI** die Option **Öffentlicher Client/nativ (Desktop und Desktop)** aus, und geben Sie dann in das URL-Feld `https://your-tenant-name.b2clogin.com/oauth2/nativeclient` ein. Ersetzen Sie `your-tenant-name` durch den [Namen Ihres Mandanten](tenant-management.md#get-your-tenant-name). Weitere Optionen finden Sie unter [Konfigurieren des Umleitungs-URI](enable-authentication-wpf-desktop-app-options.md#configure-the-redirect-uri).
1. Wählen Sie **Registrieren**.
1. Wählen Sie nach Abschluss der App-Registrierung **Übersicht** aus.
1. Notieren Sie sich die **Anwendungs- bzw. Client-ID**, da Sie diese später beim Konfigurieren der Desktopanwendung benötigen.
    ![Screenshot mit hervorgehobener ID für die Desktopanwendung](./media/configure-authentication-sample-wpf-desktop-app/get-azure-ad-b2c-app-id.png)  

### <a name="step-24-grant-the-desktop-app-permissions-for-the-web-api"></a>Schritt 2.4: Erteilen von Berechtigungen für die Desktop-App zur Verwendung der Web-API

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-configure-the-sample-web-api"></a>Schritt 3: Konfigurieren der Beispiel-Web-API

In diesem Beispiel wird ein Zugriffstoken mit den relevanten Bereichen bezogen, die die Desktop-App für eine Web-API verwenden kann.  Gehen Sie wie folgt vor, um eine Web-API aus Code aufzurufen:

1. Verwenden Sie eine vorhandene Web-API, oder erstellen Sie eine neue. Weitere Informationen finden Sie unter [Aktivieren der Authentifizierung in Ihrer eigenen Web-API mit Azure AD B2C](enable-authentication-web-api.md).
1. Nachdem Sie die Web-API konfiguriert haben, kopieren Sie den URI des Web-API-Endpunkts. In den nächsten Schritten verwenden Sie den Web-API-Endpunkt.

> [!TIP]
> Wenn Sie keine Web-API haben, können Sie dieses Beispiel trotzdem ausführen. In diesem Fall gibt die App das Token für den Zugriff zurück, kann aber die Web-API nicht aufrufen. 

## <a name="step-4-get-the-wpf-desktop-app-sample"></a>Schritt 4: Herunterladen des Beispiels für die WPF-Desktop-App

1. [Laden Sie die ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git), oder klonen Sie die mobile Beispielwebanwendung aus dem [GitHub-Repository](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git). 

    ```bash
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ``` 

1. Öffnen Sie die Projektmappe **active-directory-b2c-wpf** (Datei: *active-directory-b2c-wpf.sln*) in Visual Studio.



## <a name="step-5-configure-the-sample-desktop-app"></a>Schritt 5: Konfigurieren der Beispiel-Web-App

Öffnen Sie im Projekt **active-directory-b2c-wpf** die Datei *App.xaml.cs*. Die Member der `App.xaml.cs`-Klasse enthalten Informationen zu Ihrem Azure AD B2C-Identitätsanbieter. Die Desktop-App verwendet diese Informationen, um eine Vertrauensstellung mit Azure AD B2C herzustellen, Benutzer*innen an- und abzumelden sowie Token zu beziehen und zu validieren. 

Aktualisieren Sie die folgenden Klassenmember:

|Schlüssel  |Wert  |
|---------|---------|
|`TenantName`|Der erste Teil Ihres Azure AD B2C-[Mandantennamens](tenant-management.md#get-your-tenant-name) (z. B. `contoso.b2clogin.com`).|
|`ClientId`|Die ID der Desktopanwendung aus [Schritt 2.3](#step-23-register-the-desktop-app).|
|`PolicySignUpSignIn`| Der Benutzerflow für die Registrierung oder Anmeldung oder die benutzerdefinierte Richtlinie, den bzw. die Sie in [Schritt 1](#step-1-configure-your-user-flow) erstellt haben|
|`PolicyEditProfile`|Der Benutzerflow zur Profilbearbeitung oder die benutzerdefinierte Richtlinie, den bzw. die Sie in [Schritt 1](#step-1-configure-your-user-flow) erstellt haben|
|`ApiEndpoint`| (Optional) Der Web-API-Endpunkt, den Sie in [Schritt 3](#step-3-configure-the-sample-web-api) erstellt haben (z. B. `https://contoso.azurewebsites.net/hello`)|
| `ApiScopes` | Die Web-API-Bereiche, die Sie in [Schritt 2.4](#step-24-grant-the-desktop-app-permissions-for-the-web-api) erstellt haben|
| | | 

Die endgültige *App.xaml.cs*-Datei sollte wie der folgende C#-Code aussehen:

```csharp
public partial class App : Application
{

private static readonly string TenantName = "contoso";
private static readonly string Tenant = $"{TenantName}.onmicrosoft.com";
private static readonly string AzureAdB2CHostname = $"{TenantName}.b2clogin.com";
private static readonly string ClientId = "<web-api-app-application-id>";
private static readonly string RedirectUri = $"https://{TenantName}.b2clogin.com/oauth2/nativeclient";

public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string[] ApiScopes = { $"https://{Tenant}//api/tasks.read" };
public static string ApiEndpoint = "https://contoso.azurewebsites.net/hello";
```

## <a name="step-6-run-and-test-the-desktop-app"></a>Schritt 6: Ausführen und Testen der Desktop-App

1. [Stellen Sie die NuGet-Pakete wieder her.](/nuget/consume-packages/package-restore)
1. Drücken Sie F5, um das Beispiel zu kompilieren und auszuführen.
1. Wählen Sie **Registrieren**, und melden Sie sich mit Ihrem lokalen oder Social Media-Konto für Azure AD B2C an.

    ![Screenshot: Starten des Anmeldeflows](./media/configure-authentication-sample-wpf-desktop-app/sign-in.png)

1. Nach einer erfolgreichen Registrierung oder Anmeldung werden die Tokendetails im unteren Bereich der WPF-App angezeigt.

    ![Screenshot: Azure AD B2C-Zugriffstoken und -Benutzer-ID](./media/configure-authentication-sample-wpf-desktop-app/post-signin.png) 

1. Wählen Sie **API aufrufen** aus, um Ihre Web-API aufzurufen.


## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren von Authentifizierungsoptionen in einer WPF-Desktop-App mithilfe von Azure AD B2C](enable-authentication-wpf-desktop-app-options.md)
