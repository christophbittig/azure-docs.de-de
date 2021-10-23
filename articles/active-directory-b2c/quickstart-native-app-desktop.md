---
title: 'Schnellstart: Einrichten der Anmeldung für eine Desktop-App'
titleSuffix: Azure AD B2C
description: In dieser Schnellstartanleitung führen Sie eine WPF-Desktopbeispielanwendung aus, bei der die Kontoanmeldung über Azure Active Directory B2C erfolgt.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/16/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 964de300a9a37a4a2d5248778fe0e81d3d05fd3c
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130035579"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Schnellstart: Einrichten der Anmeldung für eine Desktop-App mit Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) ermöglicht die Cloudidentitätsverwaltung zum Schützen Ihrer Anwendung, Ihres Unternehmens und Ihrer Kunden. Mit Azure AD B2C können sich Ihre Anwendungen über offene Standardprotokolle bei Konten für soziale Netzwerke und bei Unternehmenskonten authentifizieren. In dieser Schnellstartanleitung verwenden Sie eine WPF-Desktopanwendung (Windows Presentation Foundation) zum Anmelden, indem Sie ein soziales Netzwerk als Identitätsanbieter nutzen und eine per Azure AD B2C geschützte Web-API aufrufen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) mit der Workload **ASP.NET- und Webentwicklung**
- Ein Social Media-Konto von Facebook, Google oder Microsoft
- [Laden Sie eine ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip), oder klonen Sie das GitHub-Repository [Azure-Samples/active-directory-b2c-dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop).

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Ausführen der Anwendung in Visual Studio

1. Öffnen Sie in Visual Studio im Projektordner der Beispielanwendung die Projektmappe **active-directory-b2c-wpf.sln**.
2. [Stellen Sie die NuGet-Pakete wieder her.](/nuget/consume-packages/package-restore)
3. Drücken Sie **F5**, um die Anwendung zu debuggen.

## <a name="sign-in-using-your-account"></a>Anmelden mit Ihrem Konto

1. Klicken Sie auf **Anmelden**, um den Workflow für das **Registrieren oder Anmelden** zu starten.

    ![Screenshot der WPF-Beispielanwendung](./media/quickstart-native-app-desktop/wpf-sample-application.png)

    Das Beispiel unterstützt mehrere Registrierungsoptionen. Diese Optionen umfassen die Nutzung eines sozialen Netzwerks als Identitätsanbieter oder die Erstellung eines lokalen Kontos per E-Mail-Adresse. Verwenden Sie für diese Schnellstartanleitung ein Konto bei einem sozialen Netzwerk als Identitätsanbieter (etwa Facebook, Google oder Microsoft).


2. Azure AD B2C zeigt für die Beispielwebanwendung eine Anmeldeseite für ein fiktives Unternehmen namens „Fabrikam“ an. Um sich mit einem Social Media-Konto als Identitätsanbieter zu registrieren, klicken Sie auf die Schaltfläche des Identitätsanbieters, den Sie verwenden möchten.

    ![Anmelde-oder Registrierungsseite mit Identitätsanbietern](./media/quickstart-native-app-desktop/sign-in-or-sign-up-wpf.png)

    Sie authentifizieren sich mit den Anmeldeinformationen Ihres Social Media-Kontos und autorisieren die Anwendung dazu, Informationen aus Ihrem Social Media-Konto zu lesen. Wenn Sie der Anwendung Zugriff auf diese gewähren, kann die Anwendung Profilinformationen aus dem Social Media-Konto abrufen, z.B. Ihren Namen und Ihre Stadt.

2. Schließen Sie den Anmeldevorgang für den Identitätsanbieter ab.

    Die Profildetails für Ihr neues Konto werden mit Informationen aus Ihrem Social Media-Konto aufgefüllt.

## <a name="edit-your-profile"></a>Bearbeiten des Profils

Azure AD B2C umfasst Funktionen, mit denen Benutzer ihre Profile aktualisieren können. Die Beispiel-Web-App nutzt einen Azure AD B2C-Bearbeitungsprofil-Benutzerflow für den Workflow.

1. Klicken Sie in der Menüleiste der Anwendung auf **Profil bearbeiten**, um das von Ihnen erstellte Profil zu bearbeiten.

    ![Hervorgehobene Schaltfläche „Profil bearbeiten“ in der WPF-Beispielanwendung](./media/quickstart-native-app-desktop/edit-profile-wpf.png)

2. Wählen Sie den Identitätsanbieter aus, der dem erstellten Konto zugeordnet ist. Wenn Sie beim Erstellen Ihres Kontos beispielsweise Facebook als Identitätsanbieter verwendet haben, wählen Sie Facebook aus, um die Details des verknüpften Profils zu ändern.

3. Ändern Sie Ihren **Anzeigenamen** oder den **Ort**, und klicken Sie anschließend auf **Weiter**.

    Im Textfeld *Tokeninformationen* wird ein neues Zugriffstoken angezeigt. Wenn Sie die Änderungen an Ihrem Profil überprüfen möchten, kopieren Sie das Zugriffstoken und fügen es in den Tokendecoder https://jwt.ms ein.

## <a name="access-a-protected-api-resource"></a>Zugreifen auf eine geschützte API-Ressource

Klicken Sie auf **API aufrufen**, um eine Anforderung an die geschützte Ressource zu übermitteln.

![API aufrufen](./media/quickstart-native-app-desktop/call-api-wpf.png)

Die Anwendung enthält das Azure AD-Zugriffstoken in der Anforderung, die an die geschützte Web-API-Ressource gesendet wird. Die Web-API sendet den Anzeigenamen zurück, der im Zugriffstoken enthalten ist.

Sie haben Ihr Azure AD B2C-Benutzerkonto verwendet, um einen autorisierten Aufruf einer geschützten Azure AD B2C-Web-API durchzuführen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können Ihren Azure AD B2C-Mandanten für weitere Azure AD B2C-Schnellstarts oder -Tutorials verwenden. Wenn Sie ihn nicht mehr benötigt, können Sie [Ihren Azure AD B2C-Mandanten löschen](faq.yml#how-do-i-delete-my-azure-ad-b2c-tenant-).

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie mithilfe einer Desktop-Beispielanwendung folgende Aktionen durchgeführt:

* Anmelden mit einer benutzerdefinierten Anmeldeseite
* Anmelden mit einem sozialen Netzwerk als Identitätsanbieter
* Erstellen eines Azure AD B2C-Kontos
* Aufrufen einer durch Azure AD B2C geschützten Web-API

Machen Sie sich als Nächstes mit der Erstellung Ihres eigenen Azure AD B2C-Mandanten vertraut.

> [!div class="nextstepaction"]
> [Erstellen eines Azure Active Directory B2C-Mandanten im Azure-Portal](tutorial-create-tenant.md)
