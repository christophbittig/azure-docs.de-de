---
title: Bereitstellen über ein lokales Git-Repository
description: Erfahren Sie, wie Sie die lokale Git-Bereitstellung in Azure App Service aktivieren. Es handelt sich um einen der einfachsten Wege, Code von Ihrem lokalen Computer bereitzustellen.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 90acf43471e0213b801e4d147fe4e8a8abbd0394
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122338890"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Lokale Git-Bereitstellung in Azure App Service

In dieser Anleitung erfahren Sie, wie Sie Ihre App aus einem Git-Repository auf dem lokalen Computer in [Azure App Service](overview.md) bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

Ausführen der Schritte in dieser Anleitung:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Git installieren](https://www.git-scm.com/downloads).

- Über ein lokales Git-Repository mit dem Code verfügen, den Sie bereitstellen möchten. Führen Sie zum Herunterladen eines Beispielrepositorys den folgenden Befehl in Ihrem lokalen Terminalfenster aus:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>Konfigurieren eines Bereitstellungsbenutzers

Weitere Informationen finden Sie unter [Konfigurieren von Anmeldeinformationen für die Azure App Service-Bereitstellung](deploy-configure-credentials.md). Sie können Anmeldeinformationen für den Benutzerbereich oder Anmeldeinformationen für den Anwendungsbereich verwenden.

## <a name="create-a-git-enabled-app"></a>Erstellen einer Git-fähigen App

Wenn Sie bereits über eine App Service-App verfügen und dafür die lokale Git-Bereitstellung konfigurieren möchten, lesen Sie stattdessen unter [Konfigurieren einer vorhandenen App](#configure-an-existing-app) nach.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Führen Sie [`az webapp create`](/cli/azure/webapp#az_webapp_create) mit der Option `--deployment-local-git` aus. Beispiel:

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

Die Ausgabe enthält eine URL wie `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Verwenden Sie diese URL zum Bereitstellen Ihrer App im nächsten Schritt.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Führen Sie [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) im Stammverzeichnis Ihres Git-Repository aus. Beispiel:

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

Wenn Sie dieses Cmdlet in einem Verzeichnis ausführen, das ein Git-Repository ist, wird automatisch ein Git-Remoterepository für Ihre App Service-App mit dem Namen `azure` erstellt.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Sie müssen zunächst im Portal eine App erstellen und anschließend die Bereitstellung für diese App konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren einer vorhandenen App](#configure-an-existing-app).

-----

## <a name="configure-an-existing-app"></a>Konfigurieren einer vorhandenen App

Wenn Sie noch keine App erstellt haben, lesen Sie stattdessen unter [Erstellen einer Git-fähigen App](#create-a-git-enabled-app) nach.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Führen Sie [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_local_git) aus. Beispiel:

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Die Ausgabe enthält eine URL wie `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Verwenden Sie diese URL zum Bereitstellen Ihrer App im nächsten Schritt.

> [!TIP]
> Diese URL enthält den Benutzernamen für die Bereitstellung im Benutzerbereich. Auf Wunsch können Sie stattdessen auch die [Anmeldeinformationen für den Anwendungsbereich](deploy-configure-credentials.md#appscope) verwenden. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Legen Sie den `scmType` Ihrer App fest, indem Sie das Cmdlet [Set-AzResource](/powershell/module/az.resources/set-azresource) ausführen.

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur Verwaltungsseite Ihrer App.

1. Wählen Sie im Menü auf der linken Seite **Bereitstellungscenter** > **Einstellungen** aus. Wählen Sie in **Quelle** die Option **Lokales Git** aus, und klicken Sie dann auf **Speichern**.

    ![Aktivieren der lokalen Git-Bereitstellung für App Service im Azure-Portal](./media/deploy-local-git/enable-portal.png)

1. Kopieren Sie im Abschnitt „Lokales Git“ den **Git Clone-URI** für die spätere Verwendung. Dieser URI enthält keine Anmeldeinformationen.

-----

## <a name="deploy-the-web-app"></a>Bereitstellen der Web-App

1. Wechseln Sie in einem lokalen Terminalfenster zum Stammverzeichnis Ihres Git-Repositorys, und fügen Sie mithilfe der URL, die Sie aus Ihrer App abgerufen haben, ein Git-Remoterepository hinzu. Wenn Sie mit der ausgewählten Methode keine URL abrufen können, verwenden Sie `https://<app-name>.scm.azurewebsites.net/<app-name>.git` mit dem Namen Ihrer App in `<app-name>`.
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > Wenn Sie eine [Git-fähige App in PowerShell mithilfe von „New-AzWebApp“ erstellt haben](#create-a-git-enabled-app), wurde das Remoterepository bereits für Sie erstellt.
   
1. Führen Sie einen Pushvorgang an den Azure-Remotecomputer mit `git push azure master` durch. (Informationen hierzu finden Sie unter [Ändern des Bereitstellungsbranchs](#change-deployment-branch).) 
   
1. Geben Sie im Fenster **Git Credential Manager** Ihre [Anmeldeinformationen für den Benutzerbereich oder den Anwendungsbereich](#configure-a-deployment-user) ein. Verwenden Sie hier nicht Ihre Anmeldeinformationen für die Azure-Anmeldung.

    Wenn Ihre Git-Remote-URL bereits den Benutzernamen und das Kennwort enthält, werden Sie zu deren Eingabe aufgefordert. 
   
1. Überprüfen Sie die Ausgabe. Sie enthält möglicherweise eine laufzeitspezifische Automatisierung wie MSBuild für ASP.NET, `npm install` für Node.js und `pip install` für Python. 
   
1. Navigieren Sie im Azure-Portal zu Ihrer App, um zu überprüfen, ob der Inhalt bereitgestellt wurde.

## <a name="change-deployment-branch"></a>Ändern des Bereitstellungsbranchs

Wenn Sie Commits per Push an Ihr App Service-Repository übertragen, stellt App Service die Dateien standardmäßig im `master`-Branch bereit. Da viele Git-Repositorys von `master` in `main` verschoben werden, müssen Sie mit einer von zwei Methoden sicherstellen, dass der Pushvorgang in den richtigen Branch im App Service-Repository erfolgt:

- Führen Sie die Bereitstellung in `master` explizit durch einen Befehl wie den folgenden durch:

    ```bash
    git push azure main:master
    ```

- Ändern Sie den Bereitstellungsbranch, indem Sie die App-Einstellung `DEPLOYMENT_BRANCH` festlegen und dann Commits per Push an den benutzerdefinierten Branch übertragen. Vorgehensweise über die Azure CLI:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <group-name> --settings DEPLOYMENT_BRANCH='main'
    git push azure main
    ```

## <a name="troubleshoot-deployment"></a>Problembehandlung bei der Bereitstellung

Möglicherweise werden die folgenden häufigen Fehlermeldungen angezeigt, wenn Sie eine App Service-App mithilfe von Git in Azure veröffentlichen:

|`Message`|Ursache|Lösung
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Die App wird nicht ordnungsgemäß ausgeführt.|Starten Sie die App im Azure-Portal. Die Git-Bereitstellung ist nicht verfügbar, wenn die Web-App beendet wurde.|
|`Couldn't resolve host 'hostname'`|Die Adressinformationen für die ‚azure‘-Remotewebsite sind falsch.|Verwenden Sie den Befehl `git remote -v`, um alle Remotewebsites zusammen mit der jeweils zugehörigen URL aufzulisten. Überprüfen Sie, ob die URL für die 'azure'-Remotewebsite korrekt ist. Entfernen Sie diese Remote-Website bei Bedarf und erstellen Sie sie mit der korrekten URL neu.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|Sie haben während `git push` keinen Branch angegeben, oder Sie haben den Wert `push.default` in `.gitconfig` nicht festgelegt.|Führen Sie `git push` erneut aus, und geben Sie dabei den Hauptbranch an: `git push azure main`.|
|`Error - Changes committed to remote repository but deployment to website failed.`|Sie haben einen lokalen Branch gepusht, der nicht mit dem App-Bereitstellungsbranch auf „azure“ übereinstimmt.|Vergewissern Sie sich, dass Current Branch gleich `master` ist. Um den Standardbranch zu ändern, verwenden Sie die Anwendungseinstellung `DEPLOYMENT_BRANCH`. (Informationen hierzu finden Sie unter [Ändern des Bereitstellungsbranchs](#change-deployment-branch).) |
|`src refspec [branchname] does not match any.`|Sie haben versucht, einen anderen Branch als den Hauptbranch mithilfe von Push in das „azure“-Remoterepository zu übertragen.|Führen Sie `git push` erneut aus, und geben Sie dabei den Hauptbranch an: `git push azure main`.|
|`RPC failed; result=22, HTTP code = 5xx.`|Dieser Fehler kann auftreten, wenn Sie versuchen, ein großes Git-Repository über HTTPS mithilfe von Push zu übertragen.|Ändern Sie die Git-Konfiguration auf dem lokalen Computer, um den `postBuffer` zu vergrößern. Beispiel: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Sie haben eine Node.js-App mit einer Datei von Typ _package.json_ bereitgestellt, die zusätzliche erforderliche Module angibt.|Überprüfen Sie die Fehlermeldungen vom Typ `npm ERR!` vor diesem Fehler, um mehr Kontext zu erhalten. Es folgen die bekannten Ursachen für diesen Fehler und die entsprechenden Meldungen vom Typ `npm ERR!`:<br /><br />**Falsch formatierte „package.json“-Datei**: `npm ERR! Couldn't read dependencies.`<br /><br />**Systemeigenes Modul verfügt über keine binäre Verteilung für Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />oder <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [App Service-Buildserver (Kudu-Projektdokumentation)](https://github.com/projectkudu/kudu/wiki)
- [Continuous Deployment in Azure App Service](deploy-continuous-deployment.md)
- [Beispiel: Erstellen einer Web-App und Bereitstellen von Code über ein lokales Git-Repository (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Beispiel: Erstellen einer Web-App und Bereitstellen von Code über ein lokales Git-Repository (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
