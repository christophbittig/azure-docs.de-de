---
title: 'Schnellstart: Abrufen eines Tokens und Aufrufen von Microsoft Graph in einer Konsolen-App | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie eine .NET Core-Beispiel-App den Clientanmeldeinformations-Flow zum Abrufen eines Tokens und zum Aufrufen von Microsoft Graph verwenden kann.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: d5b463156116b675c2220d1c1b1278d7552eadb0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619304"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>Schnellstart: Abrufen eines Tokens und Aufrufen der Microsoft Graph-API über die Identität einer Konsolen-App

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine .NET Core-Konsolenanwendung ein Zugriffstoken abrufen kann, um die Microsoft Graph-API aufzurufen und eine [Liste mit Benutzern](/graph/api/user-list) im Verzeichnis anzuzeigen. Das Codebeispiel veranschaulicht auch, wie ein Auftrag oder ein Windows-Dienst mit einer Anwendungsidentität anstelle der Identität eines Benutzers ausgeführt werden kann. Die Beispielkonsolenanwendung in dieser Schnellstartanleitung ist außerdem eine Daemonanwendung, daher handelt es sich dabei um eine vertrauliche Clientanwendung.

> [!div renderon="docs"]
> Das folgende Diagramm zeigt die Funktionsweise der Beispiel-App:
>
> ![Diagramm: Funktionsweise der in dieser Schnellstartanleitung generierten Beispiel-App](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)
>

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung ist das [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download) erforderlich, es kann jedoch auch das .NET 5.0 SDK verwendet werden.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-app"></a>Registrieren und Herunterladen der App

> [!div renderon="docs" class="sxs-lookup"]
>
> Sie haben zwei Möglichkeiten, mit der Anwendungserstellung zu beginnen: automatische oder manuelle Konfiguration.
>
> ### <a name="automatic-configuration"></a>Automatische Konfiguration
>
> Wenn Sie die App registrieren und automatisch konfigurieren und dann das Codebeispiel herunterladen möchten, führen Sie die folgenden Schritte aus:
>
> 1. Navigieren Sie zur <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">Azure-Portal-Seite für die App-Registrierung</a>.
> 1. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Registrieren** aus.
> 1. Befolgen Sie die Anweisungen, um Ihre neue Anwendung mit einem Klick herunterzuladen und automatisch zu konfigurieren.
>
> ### <a name="manual-configuration"></a>Manuelle Konfiguration
>
> Wenn Sie die Anwendung und das Codebeispiel manuell konfigurieren möchten, verwenden Sie die folgenden Verfahren.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren
> Führen Sie die folgenden Schritte aus, um Ihre Anwendung zu registrieren und Ihrer Projektmappe manuell die Registrierungsinformationen Ihrer App hinzuzufügen:
>
> 1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</span></a> an.
> 1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, in dem Sie die Anwendung registrieren möchten.
> 1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
> 1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
> 1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein. Geben Sie beispielsweise **Daemon-console** ein. Benutzern Ihrer App wird dieser Name angezeigt. Sie können ihn später ändern.
> 1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen.
> 1. Wählen Sie unter **Verwalten** die Option **Zertifikate und Geheimnisse** aus.
> 1. Wählen Sie unter **Geheimer Clientschlüssel** die Option **Neuer geheimer Clientschlüssel** aus, geben Sie einen Namen ein, und wählen Sie dann **Hinzufügen** aus. Notieren Sie den Wert für den geheimen Schlüssel an einem sicheren Ort, damit Sie ihn in einem späteren Schritt verwenden können.
> 1. Wählen Sie unter **Verwalten** die Optionen **API-Berechtigungen** > **Berechtigung hinzufügen** aus. Wählen Sie **Microsoft Graph**.
> 1. Wählen Sie **Anwendungsberechtigungen**.
> 1. Wählen Sie unter dem Knoten **Benutzer** die Option **User.Read.All** und dann **Berechtigungen hinzufügen** aus.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Herunterladen und Konfigurieren Ihrer Schnellstart-App
>
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal
> Damit das Codebeispiel in dieser Schnellstartanleitung funktioniert, müssen Sie einen geheimen Clientschlüssel erstellen und die Anwendungsberechtigung **User.Read.All** aus der Graph-API hinzufügen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Make these changes for me]() (Diese Änderungen für mich vornehmen)
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Bereits konfiguriert](media/quickstart-v2-netcore-daemon/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-your-visual-studio-project"></a>Schritt 2: Herunterladen des Visual Studio-Projekts

> [!div renderon="docs"]
> [Laden Sie das Visual Studio-Projekt herunter](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> Sie können das bereitgestellte Projekt entweder in Visual Studio oder Visual Studio für Mac ausführen.


> [!div class="sxs-lookup" renderon="portal"]
> Führen Sie das Projekt mit Visual Studio 2019 aus.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Schritt 3: Konfigurieren des Visual Studio-Projekts
>
> 1. Extrahieren Sie die ZIP-Datei in einem lokalen Ordner in der Nähe des Datenträger-Stammverzeichnisses. Extrahieren Sie es beispielsweise in *C:\Azure-Samples*.
>
>    Es wird empfohlen, das Archiv in ein Verzeichnis in der Nähe des Stammverzeichnisses Ihres Laufwerks zu extrahieren, um Fehler zu vermeiden, die durch Beschränkungen der Pfadlänge unter Windows verursacht werden.
>
> 1. Öffnen Sie die Projektmappe in Visual Studio: *1-Call-MSGraph\daemon-console.sln* (optional).
> 1. Ersetzen Sie in *appsettings.json* die Werte von `Tenant`, `ClientId` und `ClientSecret`:
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>    Informationen zu diesem Code:
>    - `Enter_the_Application_Id_Here` ist die Anwendungs-ID (Client) für die von Ihnen registrierte Anwendung.
        Die Werte für die Anwendungs-ID (Client) und die Verzeichnis-ID (Mandant) finden Sie im Azure-Portal auf der Seite **Übersicht** der App.
>    - Ersetzen Sie `Enter_the_Tenant_Id_Here` durch die Mandanten-ID oder den Mandanten Namen (z. B.`contoso.microsoft.com`).
>    - Ersetzen Sie `Enter_the_Client_Secret_Here` durch den geheimen Clientschlüssel, den Sie in Schritt 1 erstellt haben.
    Navigieren Sie zum Generieren eines neuen Schlüssels zur Seite **Zertifikate und Geheimnisse**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Schritt 3: Administratorzustimmung

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Schritt 4: Administratorzustimmung

Wenn Sie zu diesem Zeitpunkt versuchen, die Anwendung auszuführen, erhalten Sie den Fehler *HTTP 403: unzulässig*: „Nicht genügend Berechtigungen zum Abschließen des Vorgangs.“ Dieser Fehler tritt auf, da für eine nur für die App geltende Berechtigung die Einwilligung eines globalen Administrators zu Ihrer Anwendung erforderlich ist. Wählen Sie je nach Ihrer Rolle eine der folgenden Optionen aus:

##### <a name="global-tenant-administrator"></a>Globaler Mandantenadministrator

> [!div renderon="docs"]
> Wenn Sie globaler Mandantenadministrator sind, navigieren Sie im Azure-Portal zu **Unternehmensanwendungen**. Wählen Sie Ihre App-Registrierung und dann im linken Bereich im Abschnitt **Sicherheit** die Option **Berechtigungen** aus. Wählen Sie anschließend die große Schaltfläche mit der Bezeichnung **Administratorzustimmung für "{Mandantenname}" erteilen** aus. (Dabei ist **{Mandantenname}** der Name Ihres Verzeichnisses.)

> [!div renderon="portal" class="sxs-lookup"]
> Gehen Sie als globaler Administrator wie folgt vor: Navigieren Sie zur Seite **API-Berechtigungen**, und wählen Sie **Administratorzustimmung für Enter_the_Tenant_Name_Here erteilen** aus.
> > [!div id="apipermissionspage"]
> > [Navigieren Sie zur Seite „API-Berechtigungen“]().

##### <a name="standard-user"></a>Standardbenutzer

Wenn Sie ein Standardbenutzer Ihres Mandanten sind, bitten Sie einen globalen Administrator, die Administratoreinwilligung für Ihre Anwendung zu erteilen. Übermitteln Sie hierzu die folgende URL an Ihren Administrator:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
> Für diese URL gilt:
> * Ersetzen Sie `Enter_the_Tenant_Id_Here` durch die Mandanten-ID oder den Mandanten Namen (z. B.`contoso.microsoft.com`).
> * `Enter_the_Application_Id_Here` ist die Anwendungs-ID (Client) für die von Ihnen registrierte Anwendung.

Unter Umständen wird der Fehler „AADSTS50011: No reply address is registered for the application“ (AADSTS50011: Für die Anwendung ist keine Antwortadresse registriert.) angezeigt, nachdem Sie über die obige URL die Einwilligung für die App erteilt haben. Dieser Fehler tritt auf, weil diese Anwendung und die URL nicht über einen Umleitungs-URI verfügen. Sie kann daher ignoriert werden.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Schritt 4: Ausführen der Anwendung

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Schritt 5: Ausführen der Anwendung

Wenn Sie Visual Studio oder Visual Studio für Mac verwenden, drücken Sie **F5**, um die Anwendung auszuführen. Führen Sie die Anwendung andernfalls über eine Eingabeaufforderung, die Konsole oder ein Terminal aus:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
Informationen zu diesem Code:
* `{ProjectFolder}` ist der Ordner, in den Sie die ZIP-Datei extrahieren. z. B. `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2`.

Es sollte eine Liste mit Benutzern in Azure Active Directory angezeigt werden.

Für die Anwendung in dieser Schnellstartanleitung wird ein Clientgeheimnis verwendet, um sich selbst als vertraulicher Client zu identifizieren. Der geheime Clientschlüssel wird Ihren Projektdateien als reine Textdatei hinzugefügt. Es empfiehlt sich aus Sicherheitsgründen, ein Zertifikat anstelle eines geheimen Clientschlüssels zu verwenden, bevor die Anwendung in der Produktion genutzt wird. Weitere Informationen zur Verwendung eines Zertifikats finden Sie im GitHub-Repository für dieses Beispiel in [diesen Anweisungen](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates).

## <a name="more-information"></a>Weitere Informationen
In diesem Abschnitt erhalten Sie eine Übersicht über den erforderlichen Code für die Benutzeranmeldung. Diese Übersicht kann hilfreich sein, um die Funktionsweise des Codes und die Hauptargumente zu verstehen und zu ermitteln, wie Sie einer vorhandenen .NET Core-Konsolenanwendung eine Anmeldung hinzufügen.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Funktionsweise des Beispiels
>
> ![Diagramm: Funktionsweise der in dieser Schnellstartanleitung generierten Beispiel-App](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

Zum Anmelden von Benutzern und Anfordern von Token für den Zugriff auf eine durch Microsoft Identity Platform geschützte API wird die Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL; im Paket [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) verwendet. In dieser Schnellstartanleitung werden Token unter Verwendung der eigenen Identität der Anwendung anstelle der delegierten Berechtigungen angefordert. Der Authentifizierungsflow wird in diesem Fall als [OAuth 2.0-Clientanmeldeinformations-Flow](v2-oauth2-client-creds-grant-flow.md) bezeichnet. Weitere Informationen dazu, wie Sie MSAL.NET mit einem Clientanmeldeinformations-Flow verwenden, finden Sie in [diesem Artikel](https://aka.ms/msal-net-client-credentials).

 Sie können MSAL.NET installieren, indem Sie den folgenden Befehl in der Paket-Manager-Konsole von Visual Studio ausführen:

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL-Initialisierung

Sie können den Verweis auf MSAL hinzufügen, indem Sie den folgenden Code hinzufügen:

```csharp
using Microsoft.Identity.Client;
```

Initialisieren Sie MSAL anschließend mit dem folgenden Code:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

 | Element | Beschreibung |
 |---------|---------|
 | `config.ClientSecret` | Der geheime Clientschlüssel, der für die Anwendung im Azure-Portal erstellt wird |
 | `config.ClientId` | Die Anwendungs-ID (Client) für die im Azure-Portal registrierte Anwendung. Dieser Wert befindet sich im Azure-Portal auf der Seite **Übersicht** der App. |
 | `config.Authority`    | (Optional) STS-Endpunkt (Security Token Service, Sicherheitstokendienst) für den zu authentifizierenden Benutzer. Normalerweise `https://login.microsoftonline.com/{tenant}` für die öffentliche Cloud, wobei `{tenant}` der Name Ihres Mandanten bzw. Ihre Mandanten-ID ist.|

Weitere Informationen finden Sie in der [Referenzdokumentation für `ConfidentialClientApplication`](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication).

### <a name="requesting-tokens"></a>Anfordern von Token

Verwenden Sie die `AcquireTokenForClient`-Methode, um ein Token mit der App-Identität anzufordern:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|Element| Beschreibung |
|---------|---------|
| `scopes` | Enthält die angeforderten Bereiche. Bei vertraulichen Clients sollte dieser Wert etwa folgendes Format aufweisen: `{Application ID URI}/.default`. Mit diesem Format wird angegeben, dass die angeforderten Bereiche diejenigen sind, die im App-Objekt, das im Azure-Portal festgelegt ist, statisch definiert sind. Für Microsoft Graph wird für `{Application ID URI}` auf `https://graph.microsoft.com` verwiesen. Für benutzerdefinierte Web-APIs wird `{Application ID URI}` im Azure-Portal unter **Anwendungsregistrierung (Vorschau)**  > **Eine API verfügbar machen** definiert. |

Weitere Informationen finden Sie in der [Referenzdokumentation für `AcquireTokenForClient`](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Daemon-Anwendungen finden Sie in der Szenarioübersicht:

> [!div class="nextstepaction"]
> [Daemon-App zum Aufrufen von Web-APIs](scenario-daemon-overview.md)
