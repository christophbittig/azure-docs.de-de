---
title: Konfigurieren von Continuous Deployment
description: Erfahren Sie, wie Sie aus GitHub, Bitbucket, Azure Repos und anderen Repositorys CI/CD in Azure App Service aktivieren. Wählen Sie die Buildpipeline aus, die Ihren Anforderungen entspricht.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bff93e0dcfb49871cefa68d16d7a7db85c0f69ce
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122638377"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Continuous Deployment in Azure App Service

[Azure App Service](overview.md) ermöglicht durch das Abrufen der neuesten Updates eine kontinuierliche Bereitstellung aus [GitHub](https://help.github.com/articles/create-a-repo)-, [Bitbucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html)- und [Azure Repos](/azure/devops/repos/git/creatingrepo)-Repositorys.

> [!NOTE]
> Die Seite **Development Center (klassisch)** im Azure-Portal, bei der es sich um eine frühere Version der Bereitstellungsfunktionalität handelt, wurde im März 2021 als veraltet gekennzeichnet. Diese Änderung wirkt sich nicht auf vorhandene Bereitstellungseinstellungen in Ihrer App aus, und Sie können die App-Bereitstellung weiterhin über die Seite **Bereitstellungscenter** im Portal verwalten.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-the-deployment-source"></a>Konfigurieren der Bereitstellungsquelle

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zur Verwaltungsseite für Ihre App Service-App.

1. Wählen Sie im linken Bereich **Bereitstellungscenter** aus. Wählen Sie dann **Einstellungen** aus. 

1. Wählen Sie im Feld **Quelle** eine der CI/CD-Optionen aus:

    ![Screenshot: Auswählen der Bereitstellungsquelle](media/app-service-continuous-deployment/choose-source.png)

Wählen Sie die Registerkarte für Ihren Buildanbieter aus, um den Vorgang fortzusetzen.

# <a name="github"></a>[GitHub](#tab/github)

4. [GitHub Actions](#how-the-github-actions-build-provider-works) sind der Standardbuildanbieter. Um den Anbieter zu ändern, klicken Sie auf **Anbieter ändern** > **App Service-Builddienst** (Kudu) > **OK**.

    > [!NOTE]
    > Um Azure Pipelines als Buildanbieter für Ihre App Service-App zu verwenden, konfigurieren Sie CI/CD direkt aus Azure Pipelines. Führen Sie die Konfiguration nicht in App Service durch. Die Option **Azure Pipelines** weist Sie lediglich in die richtige Richtung.

1. Wenn Sie zum ersten Mal eine Bereitstellung aus GitHub durchführen, klicken Sie auf **Autorisieren**, und befolgen Sie die Anweisungen für die Autorisierung. Wenn Sie die Bereitstellung aus dem Repository eines anderen Benutzers durchführen möchten, klicken Sie auf **Konto ändern**.

1. Nachdem Sie Ihr Azure-Konto bei GitHub autorisiert haben, wählen Sie die **Organisation**, das **Repository** und den **Branch** für die CI/CD-Konfiguration aus. Wenn Sie eine Organisation oder ein Repository nicht finden, müssen Sie möglicherweise zusätzliche Berechtigungen für GitHub aktivieren. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf Repositorys Ihrer Organisation](https://docs.github.com/organizations/managing-access-to-your-organizations-repositories).

1. Wenn GitHub Actions als Buildanbieter ausgewählt ist, können Sie die gewünschte Workflowdatei über die Dropdownlisten **Runtimestapel** und **Version** auswählen. Azure committet diese Workflowdatei in Ihr ausgewähltes GitHub-Repository, um Build- und Bereitstellungsaufgaben zu verarbeiten. Wenn Sie die Datei vor dem Speichern Ihrer Änderungen anzeigen möchten, klicken Sie auf **Dateivorschau**.

    > [!NOTE]
    > App Service erkennt die [Sprachstapeleinstellung](configure-common.md#configure-language-stack-settings) Ihrer App und wählt die geeignetste Workflowvorlage aus. Wenn Sie eine andere Vorlage auswählen, wird möglicherweise eine App bereitgestellt, die nicht ordnungsgemäß ausgeführt wird. Weitere Informationen finden Sie unter [Funktionsweise des GitHub Actions-Buildanbieters](#how-the-github-actions-build-provider-works).

1. Wählen Sie **Speichern** aus.
   
    Neue Commits im ausgewählten Repository und Branch werden nun fortlaufend in Ihrer App Service-App bereitgestellt. Sie können die Commits und Bereitstellungen auf der Registerkarte **Protokolle** nachverfolgen.

# <a name="bitbucket"></a>[Bitbucket](#tab/bitbucket)

Die Bitbucket-Integration verwendet die App Service-Builddienste (Kudu) zur Buildautomatisierung.

4. Wenn Sie zum ersten Mal eine Bereitstellung aus Bitbucket durchführen, klicken Sie auf **Autorisieren**, und befolgen Sie die Anweisungen für die Autorisierung. Wenn Sie die Bereitstellung aus dem Repository eines anderen Benutzers durchführen möchten, klicken Sie auf **Konto ändern**.

1. Wählen Sie für Bitbucket **„Team“** aus, dann **„Repository“** und die **„Branch“** , die Sie fortlaufend nutzen möchten.

1. Wählen Sie **Speichern** aus.
   
    Neue Commits im ausgewählten Repository und Branch werden nun fortlaufend in Ihrer App Service-App bereitgestellt. Sie können die Commits und Bereitstellungen auf der Registerkarte **Protokolle** nachverfolgen.
   
# <a name="local-git"></a>[Git (lokal)](#tab/local)

Siehe [Lokale Git-Bereitstellung in Azure App Service](deploy-local-git.md).

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)
   
   > [!NOTE]
   > Azure Repos wird als Bereitstellungsquelle für Windows-Apps unterstützt.
   >

4. App Service-Builddienst (Kudu) ist der Standardbuildanbieter.

    > [!NOTE]
    > Um Azure Pipelines als Buildanbieter für Ihre App Service-App zu verwenden, konfigurieren Sie CI/CD direkt aus Azure Pipelines. Führen Sie die Konfiguration nicht in App Service durch. Die Option **Azure Pipelines** weist Sie lediglich in die richtige Richtung.

1. Wählen Sie die **Azure DevOps-Organisation**, das **Projekt**, das **Repository** und den **Branch** aus, die Sie fortlaufend bereitstellen möchten. 

    Wenn Ihre DevOps-Organisation nicht aufgeführt ist, ist sie noch nicht mit Ihrem Azure-Abonnement verknüpft. Weitere Informationen finden Sie unter [Erstellen einer Azure-Dienstverbindung](/azure/devops/pipelines/library/connect-to-azure).

-----

## <a name="disable-continuous-deployment"></a>Deaktivieren der fortlaufenden Bereitstellung

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zur Verwaltungsseite für Ihre App Service-App.

1. Wählen Sie im linken Bereich **Bereitstellungscenter** aus. Klicken Sie dann auf **Einstellungen** > **Trennen**:

    ![Screenshot: Trennen der Cloudordnersynchronisierung mit Ihrer App Service-App im Azure-Portal](media/app-service-continuous-deployment/disable.png)

1. Standardmäßig bleibt die GitHub Actions-Workflowdatei in Ihrem Repository erhalten, aber sie löst weiterhin die Bereitstellung für Ihre App aus. Um die Datei aus Ihrem Repository zu löschen, wählen Sie **Workflowdatei löschen** aus.

1. Klicken Sie auf **OK**.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>Funktionsweise des GitHub Actions-Buildanbieters

Der GitHub Actions-Buildanbieter ist eine Option für [CI/CD aus GitHub](#configure-the-deployment-source). Der Buildanbieter führt zum Einrichten von CI/CD diese Aktionen aus:

- Legt eine GitHub Actions-Workflowdatei in Ihrem GitHub-Repository ab, um Build- und Bereitstellungsaufgaben für App Service zu verarbeiten.
- Fügt das Veröffentlichungsprofil für Ihre App als GitHub-Geheimnis hinzu. Die Workflowdatei verwendet dieses Geheimnis für die Authentifizierung bei App Service.
- Es werden Informationen aus den [Protokollen zur Workflowausführung](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) erfasst und auf der Registerkarte **Protokolle** im Bereitstellungscenter Ihrer App angezeigt.

Sie können den GitHub Actions-Buildanbieter auf folgende Weise anpassen:

- Anpassen der Workflowdatei, nachdem Sie in Ihrem GitHub-Repository generiert wurde. Weitere Informationen finden Sie unter [Workflowsyntax für GitHub Actions](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Stellen Sie lediglich sicher, dass der Workflow in App Service bereitgestellt wird, mit der Aktion [azure/webapps-deploy](https://github.com/Azure/webapps-deploy).
- Wenn der ausgewählte Branch geschützt ist, können Sie eine Vorschau der Workflowdatei anzeigen, ohne die Konfiguration zu speichern, und die Datei dann manuell in Ihr Repository einfügen. Bei dieser Methode erfolgt keine Protokollintegration in das Azure-Portal.
- Anstatt ein Veröffentlichungsprofil zu verwenden, führen Sie die Bereitstellung mithilfe eines [Dienstprinzipals](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) in Azure Active Directory durch.

#### <a name="authenticate-by-using-a-service-principal"></a>Authentifizierung mithilfe eines Dienstprinzipals

Diese optionale Konfiguration ersetzt die Standardauthentifizierung mit Veröffentlichungsprofilen in der generierten Workflowdatei.

1. Sie können mit dem Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) in der [Azure CLI](/cli/azure/) einen Dienstprinzipal generieren. Ersetzen Sie im folgenden Beispielen \<subscription-id> , \<group-name> und \<app-name> durch Ihre eigenen Werte:

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > Erteilen Sie aus Sicherheitsgründen dem Dienstprinzipal den minimal erforderlichen Zugriff. Der Bereich im vorherigen Beispiel ist auf die spezifische App Service-App und nicht auf die gesamte Ressourcengruppe beschränkt.
    
1. Speichern Sie die gesamte JSON-Ausgabe für den nächsten Schritt, einschließlich `{}` der obersten Ebene.

1. Wählen Sie in [GitHub](https://github.com/) in Ihrem Repository **Einstellungen** > **Geheimnisse** > **Neues Geheimnis hinzufügen** aus.

1. Fügen Sie die gesamte JSON-Ausgabe aus dem Azure CLI-Befehl in das Wertfeld des Geheimnisses ein. Geben Sie dem Geheimnis einen Namen wie `AZURE_CREDENTIALS`.

1. Überarbeiten Sie in der Workflowdatei, die über das Bereitstellungscenter generiert wurde, den Schritt `azure/webapps-deploy` so ab, dass er dem folgenden Beispiel entspricht (es handelt sich um eine geänderte Node.js-Workflowdatei):

    ```yaml
    - name: Sign in to Azure 
    # Use the GitHub secret you added.
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Deploy to Azure Web App
    # Remove publish-profile.
    - uses: azure/webapps-deploy@v2
      with:
        app-name: '<app-name>'
        slot-name: 'production'
        package: .
    - name: Sign out of Azure.
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>Bereitstellen aus anderen Repositorys

Für Windows-Apps können Sie die Continuous Deployment-Bereitstellung aus einem Git- oder Mecurial-Cloudrepository ohne direkte Portalunterstützung manuell konfigurieren (beispielsweise [GitLab](https://gitlab.com/)). Wählen Sie hierzu in der Dropdownliste **Quelle** die Option **Externes Git** aus. Weitere Informationen finden Sie unter [Einrichten von Continuous Deployment mit manuellen Schritten](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="more-resources"></a>Weitere Ressourcen

* [Bereitstellen aus Azure Pipelines in Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [Untersuchen häufiger Probleme mit Continuous Deployment](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Verwenden von Azure PowerShell](/powershell/azure/)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)
