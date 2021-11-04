---
title: 'Schnellstart: Erstellen Ihrer ersten statischen Web-App mit Azure Static Web Apps und dem Azure-Portal'
description: Erfahren Sie, wie Sie mit dem Azure-Portal eine statische Website für Azure Static Web Apps bereitstellen.
services: static-web-apps
author: craigshoemaker
ms.author: cshoe
ms.date: 05/07/2021
ms.topic: quickstart
ms.service: static-web-apps
ms.custom: mode-portal
ms.openlocfilehash: a1f193ded9424361a0ee4afcdbd3566b6e990fe4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008881"
---
# <a name="quickstart-building-your-first-static-site-in-the-azure-portal"></a>Schnellstart: Erstellen Ihrer ersten statischen Website im Azure-Portal

Azure Static Web Apps veröffentlicht eine Website in einer Produktionsumgebung, indem Apps aus einem GitHub-Repository erstellt werden. In dieser Schnellstartanleitung stellen Sie über das Azure-Portal eine Webanwendung in Azure Static Web Apps bereit.

Falls Sie noch nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Testkonto](https://azure.microsoft.com/free) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- [GitHub](https://github.com) -Konto
- [Azure](https://portal.azure.com)-Konto

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>Erstellen einer statischen Web-App

Nachdem das Repository nun erstellt wurde, können Sie im Azure-Portal eine statische Web-App erstellen.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Klicken Sie auf **Ressource erstellen**.
1. Suchen Sie nach **Static Web Apps**.
1. Klicken Sie auf **statische Web-Apps**.
1. Klicken Sie auf **Erstellen**.

Konfigurieren Sie im Abschnitt _Grundlagen_ zunächst Ihre neue App, und verknüpfen Sie sie mit einem GitHub-Repository.

:::image type="content" source="media/getting-started-portal/quickstart-portal-basics.png" alt-text="Abschnitt „Grundlagen“":::

1. Wählen Sie Ihr Azure-_Abonnement_ aus.
1. Wählen Sie neben _Ressourcengruppe_ den Link **Neu erstellen** aus.
1. Geben Sie **static-web-apps-test** in das Textfeld ein.
1. Geben Sie unter _Details zur statischen Web-App_ den Text **my-first-static-web-app** in das Textfeld ein.
1. Wählen Sie unter _Details zu Azure Functions und Staging_ eine Region aus, die in Ihrer Nähe liegt.
1. Wählen Sie unter _Bereitstellungsdetails_ die Option **GitHub** aus.
1. Klicken Sie auf die Schaltfläche **Mit GitHub anmelden**, und führen Sie die Authentifizierung mit GitHub durch.

Geben Sie nach der Anmeldung mit GitHub die Informationen zum Repository ein.

:::image type="content" source="media/getting-started-portal/quickstart-portal-source-control.png" alt-text="Details zum Repository":::

1. Wählen Sie den Namen Ihrer bevorzugten _Organisation_ aus.
1. Wählen Sie in der Dropdownliste _Repository_ den Eintrag **my-first-web-static-app** aus.
1. Wählen Sie in der Dropdownliste _Branch_ den Eintrag **Hauptbranch** aus.

   > [!NOTE]
   > Wenn Sie keine Repositorys sehen, müssen Sie möglicherweise den Azure Static Web Apps in GitHub autorisieren. Navigieren Sie zu Ihrem GitHub-Repository, und wechseln Sie zu **Einstellungen > Anwendungen > Autorisierte OAuth-Apps**, wählen Sie **Azure Static Web Apps** und dann **Erteilen** aus. Bei Organisationsrepositorys müssen Sie Besitzer der Organisation sein, um die Berechtigungen erteilen zu können.

1. Fügen Sie im Abschnitt _Builddetails_ die für Ihr bevorzugtes Front-End-Framework spezifischen Konfigurationsdetails hinzu.

    # <a name="no-framework"></a>[Kein Framework](#tab/vanilla-javascript)

    1. Wählen Sie **Benutzerdefiniert** aus der Dropdownliste _Buildvoreinstellungen_ aus.
    1. Geben Sie **./src** in das Feld _App-Speicherort_ ein.
    1. Lassen Sie das Feld _API-Speicherort_ leer.
    1. Geben Sie **./src** in das Feld _Speicherort für App-Artefakt_ ein.

    # <a name="angular"></a>[Angular](#tab/angular)

    1. Wählen Sie **Angular** aus der Dropdownliste _Buildvoreinstellungen_ aus.
    1. Behalten Sie den Standardwert im Feld _App-Speicherort_ bei.
    1. Lassen Sie das Feld _API-Speicherort_ leer.
    1. Geben Sie im Feld _Speicherort für App-Artefakte_ den Speicherort **dist/angular-basic** ein.

    # <a name="react"></a>[React](#tab/react)

    1. Wählen Sie **React** aus der Dropdownliste _Buildvoreinstellungen_ aus.
    1. Behalten Sie den Standardwert im Feld _App-Speicherort_ bei.
    1. Lassen Sie das Feld _API-Speicherort_ leer.
    1. Geben Sie im Feld _Speicherort für App-Artefakte_ den Speicherort **build** ein.

    # <a name="vue"></a>[Vue](#tab/vue)

    1. Wählen Sie **Vue.js** aus der Dropdownliste _Buildvoreinstellungen_ aus.
    1. Behalten Sie den Standardwert im Feld _App-Speicherort_ bei.
    1. Lassen Sie das Feld _API-Speicherort_ leer.
    1. Behalten Sie den Standardwert im Feld _Speicherort für App-Artefakte_ bei.

    ---

1. Klicken Sie auf **Überprüfen + erstellen**.

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="Schaltfläche „Bewerten + erstellen“":::

    > [!NOTE]
    > Sie können die [Workflowdatei](build-configuration.md) bearbeiten, um diese Werte nach der Erstellung der App zu ändern.

1. Klicken Sie auf **Erstellen**.

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="Schaltfläche „Erstellen“":::

1. Wählen Sie **Zu Ressource wechseln** aus.

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="Schaltfläche „Zu Ressource wechseln“":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht weiter nutzen möchten, können Sie die Azure Static Web Apps-Instanz mit den folgenden Schritten löschen:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Suchen Sie über die obere Suchleiste nach **my-first-web-static-app**.
1. Wählen Sie den App-Namen aus.
1. Klicken Sie auf die Schaltfläche **Löschen**.
1. Klicken Sie auf **Ja**, um die Löschaktion zu bestätigen (diese Aktion kann einige Minuten dauern).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen einer API zu Azure Static Web Apps (Vorschauversion) mit Azure Functions](add-api.md)
