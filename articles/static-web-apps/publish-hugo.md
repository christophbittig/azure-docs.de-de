---
title: 'Tutorial: Veröffentlichen einer Hugo-Website in Azure Static Web Apps'
description: Hier erfahren Sie, wie Sie eine Hugo-Anwendung in Azure Static Web Apps bereitstellen.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: aapowell
ms.openlocfilehash: 243caef4a1f2cd1d543e7bbb9f3bc127d703296e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774802"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps"></a>Tutorial: Veröffentlichen einer Hugo-Website in Azure Static Web Apps

In diesem Artikel wird gezeigt, wie Sie eine [Hugo](https://gohugo.io/)-Webanwendung erstellen und in [Azure Static Web Apps](overview.md) bereitstellen. Das Ergebnis ist eine neue Azure Static Web Apps-Instanz mit zugehöriger GitHub Actions-Instanz, mit der Sie steuern können, wie die App erstellt und veröffentlicht wird.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Erstellen einer Hugo-App
> - Einrichten einer Azure Static Web Apps-Instanz
> - Bereitstellen der Hugo-App in Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Falls Sie über keins verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).
- Ein GitHub-Konto. Falls Sie über keins verfügen, können Sie ein [kostenloses Konto erstellen](https://github.com/join).

## <a name="create-a-hugo-app"></a>Erstellen einer Hugo-App

Erstellen Sie mithilfe der Hugo-Befehlszeilenschnittstelle (Command Line Interface, CLI) eine Hugo-App:

1. Befolgen Sie die [Installationsanweisungen](https://gohugo.io/getting-started/installing/) für Hugo unter Ihrem Betriebssystem.

1. Öffnen Sie ein Terminal.

1. Führen Sie die Hugo-Befehlszeilenschnittstelle aus, um eine neue App zu erstellen.

   ```bash
   hugo new site static-app
   ```

1. Navigieren Sie zu der neu erstellten App.

   ```bash
   cd static-app
   ```

1. Initialisieren Sie ein Git-Repository.

   ```bash
   git init
   ```

1. Stellen Sie sicher, dass der Name Ihres Branches `main` lautet.

   ```bash
   git branch -M main
   ```

1. Fügen Sie als Nächstes der Website ein Design hinzu, indem Sie ein Design als Git-Submodul installieren und es dann in der Hugo-Konfigurationsdatei angeben.

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. Führen Sie für die Änderungen einen Commit aus.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Übertragen Ihrer Anwendung auf GitHub

Sie benötigen ein Repository auf GitHub, um eine Verbindung mit Azure Static Web Apps herzustellen. In den folgenden Schritten wird veranschaulicht, wie Sie ein Repository für Ihre Website erstellen.

1. Erstellen Sie über [https://github.com/new](https://github.com/new) ein leeres GitHub-Repository mit dem Namen **hugo-static-app**. (Erstellen Sie keine README-Datei.)

1. Fügen Sie das GitHub-Repository als Remoteressource zu Ihrem lokalen Repository hinzu. Fügen Sie unbedingt den GitHub-Benutzernamen anstelle des Platzhalters `<YOUR_USER_NAME>` im folgenden Befehl hinzu:

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. Übertragen Sie Ihr lokales Repository auf GitHub.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Bereitstellen Ihrer Web-App

In den folgenden Schritten wird gezeigt, wie Sie eine neue statische Website-App erstellen und in einer Produktionsumgebung bereitstellen.

### <a name="create-the-application"></a>Erstellen der Anwendung

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Wählen Sie **Ressource erstellen** aus.
1. Suchen Sie nach **Static Web Apps**.
1. Wählen Sie **Static Web Apps** aus.
1. Klicken Sie auf **Erstellen**
1. Geben Sie auf der Registerkarte _Grundlagen_ die folgenden Werte ein.

    | Eigenschaft | Wert |
    | --- | --- |
    | _Abonnement_ | Den Namen Ihres Azure-Abonnements |
    | _Ressourcengruppe_ | **my-hugo-group**  |
    | _Name_ | **hugo-static-app** |
    | _Plantyp_ | **Free** |
    | _Region für Azure Functions-API und Stagingumgebungen_ | Wählen Sie die Region aus, die Ihnen am nächsten ist. |
    | _Quelle_ | **GitHub** |

1. Wählen Sie **Mit GitHub anmelden** aus, und authentifizieren Sie sich bei GitHub.

1. Geben Sie die folgenden GitHub-Werte ein.

    | Eigenschaft | Wert |
    | --- | --- |
    | _Organisation_ | Wählen Sie Ihre gewünschte GitHub-Organisation aus. |
    | _Repository_ | Wählen Sie **hugo-static-app** aus. |
    | _Branch_ | Wählen Sie **main** aus. |

1. Wählen Sie im Abschnitt _Builddetails_ in der Dropdownliste _Buildvoreinstellungen_ die Option **Hugo** aus, und übernehmen Sie die Standardwerte.

### <a name="review-and-create"></a>Überprüfen und Erstellen

1. Wählen Sie die Schaltfläche **Überprüfen und erstellen** aus, um sicherzustellen, dass alle Details stimmen.

1. Wählen Sie **Erstellen** aus, um mit der Erstellung der statischen App Service-Web-App zu beginnen und einen GitHub Action-Vorgang für die Bereitstellung anzugeben.

1. Wenn die Bereitstellung abgeschlossen ist, klicken Sie auf **Zu Ressource wechseln**.

1. Klicken Sie auf dem Ressourcenbildschirm auf den Link _URL_, um Ihre bereitgestellte Anwendung zu öffnen. Sie müssen ggf. ein bis zwei Minuten warten, bis der GitHub Actions-Vorgang abgeschlossen ist.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="Bereitgestellte Anwendung":::

#### <a name="custom-hugo-version"></a>Benutzerdefinierte Hugo-Version

Beim Generieren einer statischen Web-App wird eine [Workflowdatei](./build-configuration.md) generiert, die die Konfigurationseinstellungen der Anwendung für die Veröffentlichung enthält. Sie können eine bestimmte Hugo-Version in der Workflowdatei festlegen, indem Sie im Abschnitt `env` einen Wert für `HUGO_VERSION` angeben. In der folgenden Beispielkonfiguration wird veranschaulicht, wie Sie Hugo auf eine bestimmte Version festlegen.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "/" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######
        env:
          HUGO_VERSION: 0.58.0
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen einer benutzerdefinierten Domäne](custom-domain.md)
