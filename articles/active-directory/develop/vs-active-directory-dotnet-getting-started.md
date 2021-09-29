---
title: Erste Schritte mit Azure AD in .NET-MVC-Projekten | Azure
description: Erfahren Sie etwas über die ersten Schritte mit Azure Active Directory in .NET MVC-Projekten nach dem Herstellen einer Verbindung oder dem Erstellen einer Azure AD-Instanz mithilfe von verbundenen Visual Studio-Diensten.
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: 292ce2171fe2e3dabb7aa691bc44596e2b4e1abe
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124786311"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Erste Schritte mit Azure Active Directory (ASP.NET MVC-Projekte)

> [!div class="op_single_selector"]
> - [Erste Schritte](vs-active-directory-dotnet-getting-started.md)
> - [Was ist passiert?](vs-active-directory-dotnet-what-happened.md)

Dieser Artikel enthält weiterführende Informationen, nachdem Sie Active Directory in Visual Studio über **Projekt > Verbundene Dienste** einem ASP.NET MVC-Projekt hinzugefügt haben. Sollten Sie den Dienst Ihrem Projekt noch nicht hinzugefügt haben, können Sie dies jederzeit nachholen.

Informationen zu den Änderungen, die an Ihrem Projekt vorgenommen werden, wenn der verbundene Dienst hinzugefügt wird, finden Sie unter [Was ist mit meinem MVC-Projekt passiert?](vs-active-directory-dotnet-what-happened.md).

## <a name="requiring-authentication-to-access-controllers"></a>Erfordern von Authentifizierung für den Zugriff auf Controller

Alle Controller in Ihrem Projekt wurden mit dem Attribut `[Authorize]` versehen. Dieses Attribut erfordert, dass der Benutzer vor dem Zugriff auf diese Controller authentifiziert werden muss. Wenn Sie anonymen Zugriff auf diesen Controller erlauben möchten, entfernen Sie dieses Attribut vom Controller. Wenn Sie die Berechtigungen präziser festlegen möchten, wenden Sie das Attribut auf jede Methode an, die Autorisierung erfordert, anstatt es auf die Controllerklasse anzuwenden.

## <a name="adding-signin--signout-controls"></a>Hinzufügen von SignIn-/SignOut-Steuerelementen

Wenn Sie Ihrer Ansicht SignIn-/SignOut-Steuerelemente hinzufügen möchten, können Sie die Teilansicht `_LoginPartial.cshtml` zum Hinzufügen der Funktion zu einer Ihrer Ansichten verwenden. Das folgende Beispiel zeigt die Funktion, die der Standardansicht `_Layout.cshtml` hinzugefügt wurde. (Beachten Sie das letzte Element in div mit der Klasse navbar-collapse):

```html
<!DOCTYPE html>
 <html>
 <head>
     <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>Nächste Schritte

- [Authentifizierungsszenarien für Azure Active Directory](./authentication-vs-authorization.md)
- [Hinzufügen von „Mit Microsoft anmelden“ zu einer ASP.NET-Web-App](quickstart-v2-aspnet-webapp.md)