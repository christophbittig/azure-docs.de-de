---
title: Bereitstellen von Fluid-Anwendungen mit Azure Static Web Apps
description: Hier wird ausführlich erläutert, wie Fluid-Anwendungen in Azure Static Web Apps gehostet werden können.
author: sdeshpande3
ms.author: sdeshpande
ms.date: 08/19/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: a5b30b3097ef3e04557473dcaf42e875470b7563
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095068"
---
# <a name="how-to-deploy-fluid-applications-using-azure-static-web-apps"></a>Bereitstellen von Fluid-Anwendungen mit Azure Static Web Apps

In diesem Artikel wird gezeigt, wie Sie mit Azure Static Web Apps Fluid-Apps bereitstellen. Das Repository [FluidHelloWorld](https://github.com/microsoft/FluidHelloWorld/tree/main-azure) enthält eine Fluid-Anwendung namens **DiceRoller**, mit der alle verbundenen Clients einen Würfel werfen und das Ergebnis anzeigen können.  In dieser Anleitung wird gezeigt, wie Sie die DiceRoller-Anwendung mithilfe der Visual Studio Code-Erweiterung in Azure Static Web Apps bereitstellen.

Falls Sie noch nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Testkonto](https://azure.microsoft.com/free) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- [GitHub](https://github.com) -Konto
- [Azure](https://portal.azure.com)-Konto
- [Visual Studio Code](https://code.visualstudio.com)
- [Azure Static Web Apps-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Installation von Git](https://www.git-scm.com/downloads)

[!INCLUDE [fork-fluidhelloworld](../includes/fork-fluidhelloworld.md)]

## <a name="connect-to-azure-fluid-relay-service"></a>Herstellen einer Verbindung mit dem Azure Fluid Relay-Dienst

Sie können eine Verbindung mit dem Azure Fluid Relay-Dienst herstellen, indem Sie die Mandanten-ID und den eindeutigen Schlüssel angeben, der beim Erstellen der Azure-Ressource für Sie generiert wurde. Sie können Ihre eigene Tokenanbieterimplementierung erstellen oder die beiden vom Fluid Framework bereitgestellten Tokenanbieterimplementierungen **InsecureTokenProvider** und **AzureFunctionTokenProvider** verwenden.

Weitere Informationen zur Verwendung von InsecureTokenProvider für die lokale Entwicklung finden Sie unter [Herstellen einer Verbindung mit dem Dienst](connect-fluid-azure-service.md#connecting-to-the-service) und [Authentifizierung und Autorisierung in Ihrer App](../concepts/authentication-authorization.md#the-token-provider).

### <a name="using-azurefunctiontokenprovider"></a>Verwenden von AzureFunctionTokenProvider

**AzureFunctionTokenProvider** ist ein Tokenanbieter, der den geheimen Schlüssel in clientseitigem Code nicht verfügbar macht und in Produktionsszenarios verwendet werden kann. Diese Tokenanbieterimplementierung kann verwendet werden, um ein Token von einem HTTPS-Endpunkt abzurufen, der für das Signieren von Zugriffstoken mit dem Mandantenschlüssel verantwortlich ist. Dies bietet eine sichere Möglichkeit, das Token zu generieren und an die Client-App zu übergeben.

```js
import { AzureClient, AzureFunctionTokenProvider } from "@fluidframework/azure-client";

const config = {
    tenantId: "myTenantId",
    tokenProvider: new AzureFunctionTokenProvider("https://myAzureAppUrl"+"/api/GetAzureToken", { userId: "test-user",userName: "Test User" }),
    orderer: "https://myOrdererUrl",
    storage: "https://myStorageUrl",
};

const clientProps = {
    connection: config,
};

const client = new AzureClient(clientProps);
```

Um diesen Tokenanbieter zu verwenden, müssen Sie einen HTTPS-Endpunkt bereitstellen, der Token signiert, und die URL Ihres Endpunkts an AzureFunctionTokenProvider übergeben.

### <a name="deploying-an-azure-function-using-azure-static-web-apps"></a>Bereitstellen einer Azure Functions-Funktion mit Azure Static Web Apps

Mit Azure Static Web Apps können Sie eine vollständige Website entwickeln, ohne sich mit der serverseitigen Konfiguration einer gesamten Webhostingumgebung befassen zu müssen. Sie können Azure Functions neben Ihrer statischen Website bereitstellen. Mit dieser Funktion können Sie eine durch einen HTTP-Trigger ausgelöste Azure Functions-Funktion bereitstellen, die Token signiert.

Weitere Informationen zur Bereitstellung von auf Azure Functions-Funktionen basierenden APIs für Ihre statische Web-App finden Sie unter [Hinzufügen einer API zu Azure Static Web Apps mit Azure Functions](../../static-web-apps/add-api.md).

> [!NOTE]
> Zum Implementieren Ihrer Funktion können Sie den Beispielcode für eine Azure Functions-Funktion unter [Implementieren einer Azure Functions-Funktion zum Signieren von Token](azure-function-token-provider.md#implement-an-azure-function-to-sign-tokens) verwenden.

Nach der Bereitstellung Ihrer Azure Functions-Funktion müssen Sie die an AzureFunctionTokenProvider übergebene URL aktualisieren.

```js
import { AzureClient } from "@fluidframework/azure-client";

const config = {
    tenantId: "myTenantId",
    tokenProvider: new AzureFunctionTokenProvider("https://myStaticWebAppUrl/api/GetAzureToken", { userId: "test-user",userName: "Test User" }),
    orderer: "https://myOrdererUrl",
    storage: "https://myStorageUrl",
};

const clientProps = {
    connection: config,
};

const client = new AzureClient(config);
```

Führen Sie im Stammverzeichnis den Befehl `npm run build` aus, um die App neu zu erstellen. Dadurch wird ein Ordner namens `dist` mit dem Anwendungscode generiert, der in der statischen Web-App bereitgestellt werden soll.

[!INCLUDE [sign-in-extensions](../includes/sign-in-extensions.md)]

## <a name="create-a-static-web-app"></a>Erstellen einer statischen Web-App

1. Wählen Sie in Visual Studio Code auf der Aktivitätsleiste das Azure-Logo aus, um das Fenster mit den Azure-Erweiterungen zu öffnen.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-azure-logo.png" alt-text="Abbildung: Azure-Logo mit weißem Hintergrund":::

    > [!NOTE]
    > Sie müssen sich in Visual Studio Code bei Azure und GitHub anmelden, um fortzufahren. Wenn Sie noch nicht authentifiziert wurden, werden Sie von der Erweiterung während des Erstellungsprozesses aufgefordert, sich bei beiden Diensten anzumelden.

1. Wählen Sie unter der Bezeichnung *Static Web Apps* das **Pluszeichen** aus.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-create-button.png" alt-text="Abbildung: Benutzeroberfläche der Static Web Apps-Erweiterung mit hervorgehobener Schaltfläche für die Erstellung":::
    
    > [!NOTE]
    > Die Visual Studio Code-Erweiterung für Azure Static Web Apps optimiert den Erstellungsprozess mithilfe einer Reihe von Standardwerten. Wenn Sie eine fein abgestufte Kontrolle über den Erstellungsprozess wünschen, öffnen Sie die Befehlspalette, und wählen Sie **Azure Static Web Apps: Static Web App erstellen... (Erweitert)** aus.

1. Die Befehlspalette wird oben im Editor geöffnet. Dort werden Sie aufgefordert, einen Abonnementnamen auszuwählen.

    Wählen Sie Ihr Abonnement aus, und drücken Sie die <kbd>EINGABETASTE</kbd>.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-subscription.png" alt-text="Abbildung: Benutzeroberfläche für die Auswahl des Azure-Abonnements mit einzelnem auszuwählendem Abonnement":::

1. Benennen Sie Ihre Anwendung als Nächstes.

    Geben Sie **my-first-static-web-app** ein, und drücken Sie die <kbd>EINGABETASTE</kbd>.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-create-app.png" alt-text="Abbildung: Benutzeroberfläche der Static Web Apps-Erweiterung mit Textfeld für die Eingabe des Anwendungsnamens":::

1. Wählen Sie eine Region in Ihrer Nähe aus.

    > [!NOTE]
    > Azure Static Web Apps verteilt die statischen Ressourcen global. Die von Ihnen ausgewählte Region bestimmt, wo sich Ihre optionalen Stagingumgebungen und die API-Funktions-App befinden werden.

1. Legen Sie weitere Bereitstellungsoptionen fest.
    
    - Wenn Sie dazu aufgefordert werden, eine Buildvoreinstellung auszuwählen, um die Standardprojektstruktur zu konfigurieren, wählen Sie **Benutzerdefiniert** aus.
    - Speicherort des Anwendungscodes: `/`
    - Speicherort des Codes der Azure Functions-Funktion: `api`

1. Nach der Erstellung der App wird eine Bestätigungsbenachrichtigung in Visual Studio Code angezeigt.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-confirmation.png" alt-text="Abbildung: Benachrichtigung, die in Visual Studio Code beim Erstellen der App angezeigt wird, mit folgendem Text: „Successfully created new static web app „my-first-static-web-app“. GitHub Actions is building and deploying your app, it will be available once the deployment completes.“ (Die neue statische Web-App „my-first-static-web-app“ wurde erfolgreich erstellt. GitHub Actions erstellt gerade Ihre App und stellt sie dann bereit. Sobald die Bereitstellung abgeschlossen ist, ist die App verfügbar.)":::

    Während der Ausführung der Bereitstellung meldet die Visual Studio Code-Erweiterung Ihnen den Buildstatus.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-waiting-for-deployment.png" alt-text="Abbildung: Benutzeroberfläche der Static Web Apps-Erweiterung mit einer Liste der statischen Web-Apps unter den einzelnen Abonnements. Neben der hervorgehobenen statischen Web-App wird der Status „Auf Bereitstellung wartend“ angezeigt.":::

    Sobald die Bereitstellung abgeschlossen ist, können Sie direkt zu Ihrer Website navigieren.

1. Wenn Sie die Website im Browser anzeigen möchten, klicken Sie in der Static Web Apps-Erweiterung mit der rechten Maustaste auf das Projekt, und wählen Sie **Browsen zur Website** aus.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-browse-site.png" alt-text="Abbildung: Menü, das angezeigt wird, wenn mit der rechten Maustaste auf eine statische Web-App geklickt wird. Die Option „Browse Site“ (Website durchsuchen) ist hervorgehoben.":::

1. Der Speicherort Ihres Anwendungscodes, Ihrer Azure Functions-Funktion und Ihrer Buildausgabe ist in der Workflowdatei `azure-static-web-apps-xxx-xxx-xxx.yml` im Verzeichnis `/.github/workflows` angegeben. Diese Datei wird beim Erstellen der statischen Web-App automatisch erstellt. In ihr wird eine GitHub Actions-Aktion zum Erstellen und Bereitstellen Ihrer statischen Web-App definiert.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht weiter nutzen möchten, können Sie die Azure Static Web Apps-Instanz über die Erweiterung löschen.

Kehren Sie im Fenster des Visual Studio Code-Explorers zum Abschnitt _Static Web Apps_ zurück, klicken Sie mit der rechten Maustaste auf **my-first-static-web-app**, und wählen Sie **Löschen** aus.

:::image type="content" source="../../static-web-apps/media/getting-started/extension-delete.png" alt-text="Abbildung: Menü, das angezeigt wird, wenn mit der rechten Maustaste auf eine statische Web-App geklickt wird. Die Option „Löschen“ ist hervorgehoben.":::
