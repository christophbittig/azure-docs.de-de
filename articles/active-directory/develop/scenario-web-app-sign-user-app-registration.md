---
title: Registrieren einer Web-App, die Benutzer anmeldet | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine Web-App zum Anmelden von Benutzern registrieren.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0a32bef26db276b8858614ae3c2b86586f040aad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630355"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Web-App für Benutzeranmeldungen: App-Registrierung

In diesem Artikel werden die Schritte zur App-Registrierung für eine Web-App erläutert, mit der Benutzer angemeldet werden.

Für das Registrieren Ihrer Anwendung können Sie Folgendes verwenden:

- [Web-App-Schnellstarts](#register-an-app-by-using-the-quickstarts). Schnellstarts im Azure-Portal vermitteln nicht nur erste Erfahrungen beim Erstellen einer Anwendung, sondern umfassen auch die Schaltfläche **Diese Änderung für mich vornehmen**. Sie können diese Schaltfläche verwenden, um die benötigten Eigenschaften festzulegen. Dies gilt auch für eine bereits vorhandene App. Passen Sie die Werte dieser Eigenschaften an Ihren konkreten Fall an. Insbesondere unterscheidet sich die Web-API-URL für Ihre App wahrscheinlich vom vorgeschlagenen Standardwert, was sich auch auf den Abmelde-URI auswirkt.
- Das Azure-Portal für die [manuelle Registrierung Ihrer Anwendung](#register-an-app-by-using-the-azure-portal)
- PowerShell und Befehlszeilentools

## <a name="register-an-app-by-using-the-quickstarts"></a>Registrieren einer App mithilfe der Schnellstarts

Sie können diese Links für den Einstieg in die Erstellung Ihrer Webanwendung verwenden:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Registrieren einer App über das Azure-Portal

> [!NOTE]
> Welches Portal Sie verwenden müssen, hängt davon ab, ob Ihre Anwendung in der öffentlichen Cloud von Microsoft Azure oder in einer nationalen oder Sovereign Cloud ausgeführt wird. Weitere Informationen finden Sie unter [Nationale Clouds](./authentication-national-cloud.md#app-registration-endpoints).


1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an. 
1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie die Anwendung registrieren möchten.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen für Ihre Anwendung ein:
   1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein (beispielsweise `AspNetCore-WebApp`). Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
   1. Wählen Sie die unterstützten Kontotypen für Ihre Anwendung aus. (Weitere Informationen finden Sie unter [Unterstützte Kontotypen](./v2-supported-account-types.md).)
   1. Fügen Sie in **Umleitungs-URI** den Anwendungstyp und das URI-Ziel hinzu, das nach einer erfolgreichen Authentifizierung zurückgegebene Tokenantworten akzeptiert. Geben Sie beispielsweise `https://localhost:44321` ein.
   1. Wählen Sie **Registrieren**.
1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus, und geben Sie dann die folgenden Informationen an:
   1. Fügen Sie im Abschnitt **Web** als **Umleitungs-URI** den Wert `https://localhost:44321/signin-oidc` hinzu.
   1. Geben Sie in **URL für Front-Channel-Abmeldung** die URL `https://localhost:44321/signout-oidc` ein.
   1. Wählen Sie für **Implizite Genehmigung und Hybridflows** die Option **ID-Token** aus.
   1. Wählen Sie **Speichern** aus.
   
# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen Ihrer Anwendung ein:
   1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein (beispielsweise `MailApp-openidconnect-v2`). Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
   1. Wählen Sie die unterstützten Kontotypen für Ihre Anwendung aus. (Weitere Informationen finden Sie unter [Unterstützte Kontotypen](./v2-supported-account-types.md).)
   1. Wählen Sie im Abschnitt **Umleitungs-URI (optional)** im Kombinationsfeld die Option **Web** aus, und geben Sie `https://localhost:44326/` als **Umleitungs-URI** ein.
   1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen.
1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.
1. Wählen Sie im Abschnitt **Implizite Genehmigung und Hybridflows** die Option **ID-Token** aus. Für dieses Beispiel muss der [Flow zur impliziten Gewährung](v2-oauth2-implicit-grant-flow.md) aktiviert werden, damit Benutzer angemeldet werden.
1. Wählen Sie **Speichern** aus.

# <a name="java"></a>[Java](#tab/java)

1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen Ihrer Anwendung ein: 
    1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein (beispielsweise `java-webapp`). Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern. 
    1. Wählen Sie die Option **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten (z. B. Skype, Xbox, Outlook.com)** aus.
    1. Wählen Sie **Registrieren** aus, um die Anwendung zu registrieren.
1. Wählen Sie unter **Verwalten** die Optionen **Authentifizierung** > **Plattform hinzufügen** aus.
1. Wählen Sie **Web** aus.
1. Geben Sie als **Umleitungs-URI** denselben Host und dieselbe Portnummer gefolgt von `/msal4jsample/secure/aad` als Anmeldeseite ein. 
1. Wählen Sie **Konfigurieren** aus.
1. Verwenden Sie im Abschnitt **Web** den Host und die Portnummer gefolgt von `/msal4jsample/graph/me` als **Umleitungs-URI** für die Seite „Benutzerinformationen“.
Im Beispiel wird standardmäßig Folgendes verwendet:
   - `http://localhost:8080/msal4jsample/secure/aad`
   - `http://localhost:8080/msal4jsample/graph/me`

1. Wählen Sie **Speichern** aus.
1. Wählen Sie unter **Verwalten** die Option **Zertifikate und Geheimnisse** aus.
1. Wählen Sie im Abschnitt **Geheime Clientschlüssel** die Option **Neuer geheimer Clientschlüssel** aus, und führen Sie dann folgende Schritte aus:

   1. Geben Sie eine Beschreibung für den Schlüssel ein.
   1. Wählen Sie als Schlüsseldauer die Option **In 1 Jahr** aus.
   1. Wählen Sie **Hinzufügen**.
   1. Wenn der Schlüsselwert angezeigt wird, kopieren Sie ihn für später. Dieser Wert wird nicht noch einmal angezeigt, und er kann nicht auf andere Weise abgerufen werden.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen Ihrer Anwendung ein:
   1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein (beispielsweise `node-webapp`). Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
   1. Ändern Sie **Unterstützte Kontotypen** in **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten (z. B. Skype, Xbox, Outlook.com)** .
   1. Wählen Sie im Abschnitt **Umleitungs-URI (optional)** im Kombinationsfeld die Option **Web** aus, und geben Sie den folgenden Umleitungs-URI ein: `http://localhost:3000/redirect` .
   1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen.
1. Suchen Sie auf der Seite **Übersicht** der App den Wert **Anwendungsclient-ID**, und notieren Sie ihn zur späteren Verwendung. Sie benötigen diesen Wert, um die Konfigurationsdatei für dieses Projekt zu konfigurieren.
1. Wählen Sie unter **Verwalten** die Option **Zertifikate und Geheimnisse** aus.
1. Wählen Sie im Abschnitt **Geheime Clientschlüssel** die Option **Neuer geheimer Clientschlüssel** aus, und führen Sie dann folgende Schritte aus:
   1. Geben Sie eine Beschreibung für den Schlüssel ein.
   1. Wählen Sie als Schlüsseldauer die Option **In 1 Jahr** aus.
   1. Wählen Sie **Hinzufügen**.
   1. Wenn der Schlüsselwert angezeigt wird, kopieren Sie ihn für die spätere Verwendung. Sie benötigen die Information später.

# <a name="python"></a>[Python](#tab/python)

1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen Ihrer Anwendung ein:
   1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein (beispielsweise `python-webapp`). Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
   1. Ändern Sie **Unterstützte Kontotypen** in **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten (z. B. Skype, Xbox, Outlook.com)** .
   1. Wählen Sie im Abschnitt **Umleitungs-URI (optional)** im Kombinationsfeld die Option **Web** aus, und geben Sie den folgenden Umleitungs-URI ein: `http://localhost:5000/getAToken` .
   1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen.
1. Suchen Sie auf der Seite **Übersicht** der App den Wert **Anwendungsclient-ID**, und notieren Sie ihn zur späteren Verwendung. Sie benötigen diesen Wert, um die Visual Studio-Konfigurationsdatei für dieses Projekt zu konfigurieren.
1. Wählen Sie unter **Verwalten** die Option **Zertifikate und Geheimnisse** aus.
1. Wählen Sie im Abschnitt **Geheime Clientschlüssel** die Option **Neuer geheimer Clientschlüssel** aus, und führen Sie dann folgende Schritte aus:
   1. Geben Sie eine Beschreibung für den Schlüssel ein.
   1. Wählen Sie als Schlüsseldauer die Option **In 1 Jahr** aus.
   1. Wählen Sie **Hinzufügen**.
   1. Wenn der Schlüsselwert angezeigt wird, kopieren Sie ihn für die spätere Verwendung. Sie benötigen die Information später.
---

## <a name="register-an-app-by-using-powershell"></a>Registrieren einer App mit PowerShell

> [!NOTE]
> Derzeit erstellt Azure AD PowerShell nur Anwendungen mit den folgenden unterstützten Kontotypen:
>
> - MyOrg (nur Konten in diesem Organisationsverzeichnis)
> - AnyOrg (Konten in einem beliebigen Organisationsverzeichnis)
>
> Sie können eine Anwendung erstellen, die Benutzer mit ihren persönlichen Microsoft-Konten (z. B. Skype, Xbox oder Outlook.com) anmeldet. Erstellen Sie zunächst eine mehrinstanzenfähige Anwendung. Zu den unterstützten Kontotypen gehören Konten in einem beliebigen Organisationsverzeichnis. Ändern Sie dann über das Azure-Portal im [Anwendungsmanifest](./reference-app-manifest.md) die [`accessTokenAcceptedVersion`](./reference-app-manifest.md#accesstokenacceptedversion-attribute)-Eigenschaft in **2** und die [`signInAudience`](./reference-app-manifest.md#signinaudience-attribute)-Eigenschaft in `AzureADandPersonalMicrosoftAccount`. Weitere Informationen finden Sie im ASP.NET Core-Tutorial unter [Schritt 1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant). Sie können diesen Schritt in einer beliebigen Sprache für Web-Apps generalisieren.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Codekonfiguration der App](scenario-web-app-sign-user-app-configuration.md).
