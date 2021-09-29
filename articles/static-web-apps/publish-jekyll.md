---
title: 'Tutorial: Veröffentlichen einer Jekyll-Website in Azure Static Web Apps'
description: Hier erfahren Sie, wie Sie eine Jekyll-Anwendung in Azure Static Web Apps bereitstellen.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: cshoe
ms.openlocfilehash: fa97100e670e1d96bdd33c362b2e133d78d8dae7
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155437"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps"></a>Tutorial: Veröffentlichen einer Jekyll-Website in Azure Static Web Apps

In diesem Artikel wird gezeigt, wie Sie eine [Jekyll](https://jekyllrb.com/)-Webanwendung erstellen und in [Azure Static Web Apps](overview.md) bereitstellen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Erstellen einer Jekyll-Website
> - Einrichten einer Azure Static Web Apps-Ressource
> - Bereitstellen der Jekyll-App in Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- [Jekyll](https://jekyllrb.com/docs/installation/)-Installation
  - Sie können das Windows-Subsystem für Linux verwenden und bei Bedarf die Ubuntu-Anweisungen befolgen.
- Ein Azure-Konto mit einem aktiven Abonnement. Falls Sie über keins verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).
- Ein GitHub-Konto. Falls Sie über keins verfügen, können Sie ein [kostenloses Konto erstellen](https://github.com/join).

## <a name="create-jekyll-app"></a>Erstellen einer Jekyll-App

Erstellen Sie mithilfe der Jekyll-Befehlszeilenschnittstelle (Command Line Interface, CLI) eine Jekyll-App:

1. Führen Sie auf dem Terminal die Jekyll-Befehlszeilenschnittstelle aus, um eine neue App zu erstellen.

   ```bash
   jekyll new static-app
   ```

1. Navigieren Sie zu der neu erstellten App.

   ```bash
   cd static-app
   ```

1. Initialisieren Sie ein neues Git-Repository.

   ```bash
    git init
   ```

1. Führen Sie für die Änderungen einen Commit aus.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Übertragen Ihrer Anwendung auf GitHub

Azure Static Web Apps verwendet GitHub zum Veröffentlichen Ihrer Website. In den folgenden Schritten wird veranschaulicht, wie Sie ein GitHub-Repository erstellen.

1. Erstellen Sie über [https://github.com/new](https://github.com/new) ein leeres GitHub-Repository mit dem Namen **jekyll-azure-static**. (Erstellen Sie keine README-Datei.)

1. Fügen Sie das GitHub-Repository als Remoteressource zu Ihrem lokalen Repository hinzu. Fügen Sie unbedingt den GitHub-Benutzernamen anstelle des Platzhalters `<YOUR_USER_NAME>` im folgenden Befehl hinzu:

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/jekyll-azure-static
   ```

1. Übertragen Sie Ihr lokales Repository auf GitHub.

   ```bash
   git push --set-upstream origin main
   ```

   > [!NOTE]
   > Ihre Git-Verzweigung hat unter Umständen einen anderen Namen als `main`. Ersetzen Sie `main` in diesem Befehl durch den richtigen Wert.

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
    | _Ressourcengruppe_ | **jekyll-static-app**  |
    | _Name_ | **jekyll-static-app** |
    | _Plantyp_ | **Free** |
    | _Region für Azure Functions-API und Stagingumgebungen_ | Wählen Sie die Region aus, die Ihnen am nächsten ist. |
    | _Quelle_ | **GitHub** |

1. Wählen Sie **Mit GitHub anmelden** aus, und authentifizieren Sie sich bei GitHub.

1. Geben Sie die folgenden GitHub-Werte ein.

    | Eigenschaft | Wert |
    | --- | --- |
    | _Organisation_ | Wählen Sie Ihre gewünschte GitHub-Organisation aus. |
    | _Repository_ | Wählen Sie **jekyll-static-app** aus. |
    | _Branch_ | Wählen Sie **main** aus. |

1. Wählen Sie im Abschnitt _Builddetails_ in der Dropdownliste _Buildvoreinstellungen_ die Option **Benutzerdefiniert** aus, und übernehmen Sie die Standardwerte.

1. Geben Sie im Feld _App-Speicherort_ die Zeichen **./** ein.

1. Lassen Sie das Feld _API-Speicherort_ leer.

1. Geben Sie im Feld _Ausgabespeicherort_ **_site** ein.

### <a name="review-and-create"></a>Überprüfen und Erstellen

1. Wählen Sie die Schaltfläche **Überprüfen und erstellen** aus, um sicherzustellen, dass alle Details stimmen.

1. Wählen Sie **Erstellen** aus, um mit der Erstellung der statischen App Service-Web-App zu beginnen und einen GitHub Action-Vorgang für die Bereitstellung anzugeben.

1. Wenn die Bereitstellung abgeschlossen ist, klicken Sie auf **Zu Ressource wechseln**.

1. Klicken Sie auf dem Ressourcenbildschirm auf den Link _URL_, um Ihre bereitgestellte Anwendung zu öffnen. Sie müssen ggf. ein bis zwei Minuten warten, bis der GitHub Actions-Vorgang abgeschlossen ist.

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="Bereitgestellte Anwendung":::

#### <a name="custom-jekyll-settings"></a>Benutzerdefinierte Jekyll-Einstellungen

Beim Generieren einer statischen Web-App wird eine [Workflowdatei](./build-configuration.md) generiert, die die Konfigurationseinstellungen der Anwendung für die Veröffentlichung enthält.

Fügen Sie zum Konfigurieren von Umgebungsvariablen (etwa `JEKYLL_ENV`) einen Abschnitt vom Typ `env` zur GitHub-Aktion für Azure Static Web Apps im Workflow hinzu.

```yaml
- name: Build And Deploy
   id: builddeploy
   uses: Azure/static-web-apps-deploy@v1
   with:
      azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
      repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
      action: "upload"
      ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
      # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
      app_location: "/" # App source code path
      api_location: "" # Api source code path - optional
      output_location: "_site" # Built app content directory - optional
      ###### End of Repository/Build Configurations ######
   env:
      JEKYLL_ENV: production
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen einer benutzerdefinierten Domäne](custom-domain.md)
