---
title: 'Schnellstart: Einrichten der Anmeldung für eine ASP.NET-Web-App'
titleSuffix: Azure AD B2C
description: In dieser Schnellstartanleitung führen Sie ein Beispiel für eine ASP.NET-Web-App aus, bei der die Kontoanmeldung über Azure Active Directory B2C erfolgt.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: quickstart
ms.custom: devx-track-csharp, mvc
ms.date: 10/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3c39780f1f8e84ed3fe58973f46274bad727d10e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351376"
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>Schnellstart: Einrichten der Anmeldung für eine ASP.NET-Anwendung unter Verwendung von Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) ermöglicht die Cloudidentitätsverwaltung zum Schützen Ihrer Anwendung, Ihres Unternehmens und Ihrer Kunden. Mit Azure AD B2C können sich Ihre Anwendungen über offene Standardprotokolle bei Konten für soziale Netzwerke und bei Unternehmenskonten authentifizieren. 

In dieser Schnellstartanleitung verwenden Sie eine ASP.NET-Anwendung und ein soziales Netzwerk als Identitätsanbieter, um sich anzumelden und eine durch Azure AD B2C geschützte Web-API aufzurufen.



## <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) mit der Workload **ASP.NET- und Webentwicklung**
- Ein Social Media-Konto von Facebook, Google oder Microsoft
- [Laden Sie eine ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip), oder klonen Sie die Beispielwebanwendung von GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
    ```

    Die Beispielprojektmappe enthält zwei Projekte:

    - **TaskWebApp**: Eine Webanwendung zum Erstellen und Bearbeiten einer Aufgabenliste. Die Webanwendung verwendet den **Registrierungs- oder Anmeldebenutzerflow** für die Registrierung oder Anmeldung von Benutzern.
    - **TaskService**: Eine Web-API, die die Funktionen zum Erstellen, Lesen, Aktualisieren und Löschen der Aufgabenliste unterstützt. Die Web-API wird durch Azure AD B2C geschützt und von der Webanwendung aufgerufen.

## <a name="run-the-application-in-visual-studio"></a>Ausführen der Anwendung in Visual Studio

1. Öffnen Sie in Visual Studio die Projektmappe **B2C-WebAPI-DotNet.sln** aus dem Projektordner der Beispielanwendung.
2. Führen Sie für diese Schnellstartanleitung die Projekte **TaskWebApp** und **TaskService** gleichzeitig aus. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe **B2C-WebAPI-DotNet**, und wählen Sie **Startprojekte festlegen** aus.
3. Wählen Sie **Mehrere Startprojekte** aus, und ändern Sie die **Aktion** für beide Projekte in **Starten**.
4. Klicken Sie auf **OK**.
5. Drücken Sie **F5**, um beide Anwendungen zu debuggen. Jede Anwendung wird in einer eigenen Browserregisterkarte geöffnet:

    - `https://localhost:44316/`: Die ASP.NET-Webanwendung. Im Rahmen des Schnellstarts interagieren Sie direkt mit dieser Anwendung.
    - `https://localhost:44332/`: Die Web-API, die von der ASP.NET-Webanwendung aufgerufen wird.

## <a name="sign-in-using-your-account"></a>Anmelden mit Ihrem Konto

1. Wählen Sie in der ASP.NET-Webanwendung **Registrieren/Anmelden** aus, um den Workflow zu starten.

    ![Beispiel für ASP.NET-Web-App im Browser mit hervorgehobenem Link „Registrieren/Anmelden“](./media/quickstart-web-app-dotnet/web-app-sign-in.png)

    In der Beispielanwendung werden mehrere Registrierungsoptionen unterstützt, z.B. die Nutzung eines sozialen Netzwerks als Identitätsanbieter oder die Erstellung eines lokalen Kontos per E-Mail-Adresse. Verwenden Sie für diese Schnellstartanleitung ein Konto bei einem sozialen Netzwerk als Identitätsanbieter (etwa Facebook, Google oder Microsoft).

2. Azure AD B2C zeigt für die Beispielwebanwendung eine Anmeldeseite für ein fiktives Unternehmen namens „Fabrikam“ an. Um sich mit einem Social Media-Konto als Identitätsanbieter zu registrieren, wählen Sie die Schaltfläche des Identitätsanbieters aus, den Sie verwenden möchten.

    ![Anmelde-oder Registrierungsseite mit Schaltflächen für die Auswahl von Identitätsanbietern](./media/quickstart-web-app-dotnet/sign-in-or-sign-up-web.png)

    Sie authentifizieren sich mit den Anmeldeinformationen Ihres Social Media-Kontos und autorisieren die Anwendung dazu, Informationen aus Ihrem Social Media-Konto zu lesen. Wenn Sie der Anwendung Zugriff auf diese gewähren, kann die Anwendung Profilinformationen aus dem Social Media-Konto abrufen, z.B. Ihren Namen und Ihre Stadt.

3. Schließen Sie den Anmeldevorgang für den Identitätsanbieter ab.

## <a name="edit-your-profile"></a>Bearbeiten des Profils

Azure Active Directory B2C bietet Funktionen, mit denen Benutzer ihre Profile aktualisieren können. Die Beispiel-Web-App nutzt einen Azure AD B2C-Bearbeitungsprofil-Benutzerflow für den Workflow.

1. Wählen Sie auf der Menüleiste der Anwendung Ihren Profilnamen und dann **Profil bearbeiten** aus, um das von Ihnen erstellte Profil zu bearbeiten.

    ![Beispiel für ASP.NET-Web-App im Browser mit hervorgehobenem Link „Profil bearbeiten“](./media/quickstart-web-app-dotnet/edit-profile-web.png)

2. Ändern Sie den Wert für **Anzeigename** oder **Ort**, und wählen Sie dann **Weiter** aus, um Ihr Profil zu aktualisieren.

    Die Änderung wird rechts oben auf der Startseite der Webanwendung angezeigt.

## <a name="access-a-protected-api-resource"></a>Zugreifen auf eine geschützte API-Ressource

1. Wählen Sie **Aufgabenliste** aus, um Ihre Aufgabenlistenelemente einzugeben und zu ändern.

2. Geben Sie Text in das Textfeld **Neues Element** ein. Wählen Sie **Hinzufügen** aus, um die mit Azure AD B2C geschützte Web-API aufzurufen, die ein Aufgabenlistenelement hinzufügt.

    ![Beispiel für Web-App in einem Browser mit „Hinzufügen“ für ein Aufgabenlistenelement](./media/quickstart-web-app-dotnet/add-todo-item-web.png)

    Die ASP.NET-Webanwendung schließt ein Azure AD-Zugriffstoken in die Anforderung an die geschützte Web-API-Ressource ein, um Vorgänge für die Aufgabenlistenelemente des Benutzers durchzuführen.

Sie haben Ihr Azure AD B2C-Benutzerkonto verwendet, um einen autorisierten Aufruf einer geschützten Azure AD B2C-Web-API durchzuführen.


## <a name="next-steps"></a>Nächste Schritte

[Erstellen eines Azure Active Directory B2C-Mandanten im Azure-Portal](tutorial-create-tenant.md)
