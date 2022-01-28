---
title: 'Schnellstart: Abrufen eines Tokens und Aufrufen von Microsoft Graph in einer Konsolen-App | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie eine .NET Core-Beispiel-App den Clientanmeldeinformations-Flow zum Abrufen eines Tokens und zum Aufrufen von Microsoft Graph verwenden kann.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 01/10/2022
ROBOTS: NOINDEX
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, "scenarios:getting-started", "languages:aspnet-core", mode-api
ms.openlocfilehash: 417fb1f2c11822b8c475324c15c27a24a1ff49f0
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2022
ms.locfileid: "137803929"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>Schnellstart: Abrufen eines Tokens und Aufrufen der Microsoft Graph-API über die Identität einer Konsolen-App

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine .NET Core-Konsolenanwendung ein Zugriffstoken abrufen kann, um die Microsoft Graph-API aufzurufen und eine [Liste mit Benutzern](/graph/api/user-list) im Verzeichnis anzuzeigen. Das Codebeispiel veranschaulicht auch, wie ein Auftrag oder ein Windows-Dienst mit einer Anwendungsidentität anstelle der Identität eines Benutzers ausgeführt werden kann. Die Beispielkonsolenanwendung in dieser Schnellstartanleitung ist außerdem eine Daemonanwendung, daher handelt es sich dabei um eine vertrauliche Clientanwendung.

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung ist das [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download) erforderlich, es kann jedoch auch das .NET 5.0 SDK verwendet werden.

> [!div class="sxs-lookup"]
### <a name="download-and-configure-your-quickstart-app"></a>Herunterladen und Konfigurieren Ihrer Schnellstart-App

#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal
Damit das Codebeispiel in dieser Schnellstartanleitung funktioniert, müssen Sie einen geheimen Clientschlüssel erstellen und die Anwendungsberechtigung **User.Read.All** aus der Graph-API hinzufügen.
> [!div class="nextstepaction"]
> [Make these changes for me]() (Diese Änderungen für mich vornehmen)

> [!div class="alert alert-info"]
> ![Bereits konfiguriert](media/quickstart-v2-netcore-daemon/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-your-visual-studio-project"></a>Schritt 2: Herunterladen des Visual Studio-Projekts

> [!div class="sxs-lookup"]
> Führen Sie das Projekt mit Visual Studio 2019 aus.
> [!div class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

#### <a name="step-3-admin-consent"></a>Schritt 3: Administratorzustimmung

Wenn Sie zu diesem Zeitpunkt versuchen, die Anwendung auszuführen, erhalten Sie den Fehler *HTTP 403: unzulässig*: „Nicht genügend Berechtigungen zum Abschließen des Vorgangs.“ Dieser Fehler tritt auf, da für eine nur für die App geltende Berechtigung die Einwilligung eines globalen Administrators zu Ihrer Anwendung erforderlich ist. Wählen Sie je nach Ihrer Rolle eine der folgenden Optionen aus:

##### <a name="global-tenant-administrator"></a>Globaler Mandantenadministrator

Gehen Sie als globaler Administrator wie folgt vor: Navigieren Sie zur Seite **API-Berechtigungen**, und wählen Sie **Administratorzustimmung für Enter_the_Tenant_Name_Here erteilen** aus.
> [!div id="apipermissionspage"]
> [Navigieren Sie zur Seite „API-Berechtigungen“]().

##### <a name="standard-user"></a>Standardbenutzer

Wenn Sie ein Standardbenutzer Ihres Mandanten sind, bitten Sie einen globalen Administrator, die Administratoreinwilligung für Ihre Anwendung zu erteilen. Übermitteln Sie hierzu die folgende URL an Ihren Administrator:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

Unter Umständen wird der Fehler „AADSTS50011: No reply address is registered for the application“ (AADSTS50011: Für die Anwendung ist keine Antwortadresse registriert.) angezeigt, nachdem Sie über die obige URL die Einwilligung für die App erteilt haben. Dieser Fehler tritt auf, weil diese Anwendung und die URL nicht über einen Umleitungs-URI verfügen. Sie kann daher ignoriert werden.

#### <a name="step-4-run-the-application"></a>Schritt 4: Ausführen der Anwendung

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

> [!div class="sxs-lookup"]
### <a name="how-the-sample-works"></a>Funktionsweise des Beispiels

![Diagramm: Funktionsweise der in dieser Schnellstartanleitung generierten Beispiel-App](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

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
