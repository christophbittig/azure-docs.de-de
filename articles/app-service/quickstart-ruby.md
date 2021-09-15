---
title: 'Schnellstart: Erstellen einer Ruby-App'
description: Führen Sie erste Schritte mit Azure App Service aus, indem Sie Ihre erste Ruby-App in einem Linux-Container in App Service bereitstellen.
keywords: Azure App Service, Linux, OSS, Ruby, Rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 04/27/2021
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ms.openlocfilehash: dffb2634b59c54632364d8469244edb6840d7b9d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435418"
---
# <a name="create-a-ruby-on-rails-app-in-app-service"></a>Erstellen einer Ruby on Rails-App in App Service

[Azure App Service für Linux](overview.md#app-service-on-linux) bietet einen hochgradig skalierbaren Webhostingdienst mit Self-Patching unter dem Linux-Betriebssystem. In diesem Schnellstarttutorial wird erläutert, wie Sie mit [Cloud Shell](../cloud-shell/overview.md) eine Ruby on Rails-App in App Service für Linux bereitstellen.

> [!NOTE]
> Der Ruby-Entwicklungsstapel unterstützt derzeit nur Ruby on Rails. Wenn Sie eine andere Plattform, z. B. Sinatra, oder eine nicht unterstützte Ruby-Version verwenden möchten, müssen Sie diese [in einem benutzerdefinierten Container ausführen](./quickstart-custom-container.md?pivots=platform-linux%3fpivots%3dplatform-linux).

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Installation von Ruby 2.6 oder höher</a>
* <a href="https://git-scm.com/" target="_blank">Installation von Git</a>

## <a name="download-the-sample"></a>Herunterladen des Beispiels

1. Klonen Sie in einem Terminalfenster die Beispielanwendung auf Ihren lokalen Computer, und navigieren Sie zu dem Verzeichnis mit dem Beispielcode. 

    ```bash
    git clone https://github.com/Azure-Samples/ruby-docs-hello-world
    cd ruby-docs-hello-world
    ```

1. Stellen Sie sicher, dass der Standardbranch `main` ist.

    ```bash
    git branch -m main
    ```
    
    > [!TIP]
    > Die Änderung des Branchnamens ist für App Service nicht erforderlich. Da aber viele Repositorys ihren Standardbranch in `main` ändern, erfahren Sie in diesem Tutorial auch, wie Sie ein Repository aus `main` bereitstellen. Weitere Informationen finden Sie unter [Ändern des Bereitstellungsbranches](deploy-local-git.md#change-deployment-branch).

## <a name="run-the-application-locally"></a>Lokales Ausführen der Anwendung

1. Installieren Sie die erforderlichen Gems. Im Beispiel ist eine `Gemfile` enthalten, führen Sie daher einfach den folgenden Befehl aus:

    ```bash
    bundle install
    ```

1. Nachdem die Gems installiert sind, starten Sie die App:

    ```bash
    bundle exec rails server
    ```

1. Navigieren Sie in Ihrem Webbrowser zu `http://localhost:3000`, um die App lokal zu testen.

    ![Hallo Welt (konfiguriert)](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Erstellen einer Web-App

1. Erstellen Sie eine [Web-App](overview.md#app-service-on-linux) im App Service-Plan `myAppServicePlan`. 

    In Cloud Shell können Sie den Befehl [`az webapp create`](/cli/azure/webapp) verwenden. Ersetzen Sie im folgenden Beispiel `<app-name>` durch einen global eindeutigen App-Namen (gültige Zeichen sind `a-z`, `0-9` und `-`). Die Runtime ist auf `RUBY|2.6` festgelegt. Führen Sie [`az webapp list-runtimes --linux`](/cli/azure/webapp) aus, um alle unterstützten Laufzeiten anzuzeigen. 

    ```azurecli-interactive
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime 'RUBY|2.6' --deployment-local-git
    ```

    Nach Erstellung der Web-App zeigt die Azure CLI eine Ausgabe wie im folgenden Beispiel an:

    <pre>
    Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
      "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
      "enabled": true,
      &lt; JSON data removed for brevity. &gt;
    }
    </pre>
    
    Sie haben eine leere neue Web-App mit aktivierter Git-Bereitstellung erstellt.

    > [!NOTE]
    > Die URL des Git-Remotespeicherorts wird in der `deploymentLocalGitUrl`-Eigenschaft im Format `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git` angezeigt. Speichern Sie diese URL, da Sie sie später noch benötigen.
    >

1. Navigieren Sie zu der App, um Ihre neu erstellte Web-App mit integriertem Image anzuzeigen. Ersetzen Sie _&lt;app_name>_ durch Ihren Web-App-Namen.

    ```bash
    http://<app_name>.azurewebsites.net
    ```

    Ihre neue Web-App sollte nun wie folgt aussehen:

    ![Begrüßungsseite](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Bereitstellen der Anwendung

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure-no-h.md)] 

   <pre>
   remote: Using turbolinks 5.2.0
   remote: Using uglifier 4.1.20
   remote: Using web-console 3.7.0
   remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
   remote: Bundled gems are installed into `/tmp/bundle`
   remote: Zipping up bundle contents
   remote: .......
   remote: ~/site/repository
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   remote: App container will begin restart within 10 seconds.
   To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
      a6e73a2..ae34be9  main -> main
   </pre>

## <a name="browse-to-the-app"></a>Navigieren zur App

Nachdem die Bereitstellung abgeschlossen ist, warten Sie etwa 10 Sekunden, bis die Web-App neu gestartet wird. Navigieren Sie dann zur Web-App, und überprüfen Sie die Ergebnisse.

```bash
http://<app-name>.azurewebsites.net
```

![Aktualisierte Web-App](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> Während die App neu gestartet wird, können Sie den HTTP-Statuscode `Error 503 Server unavailable` im Browser oder die Standardseite `Hey, Ruby developers!`beobachten. Der vollständige Neustart der App kann einige Minuten in Anspruch nehmen.
>

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Ruby on Rails mit Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Konfigurieren der Ruby-App](configure-language-ruby.md)
