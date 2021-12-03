---
title: 'Tutorial: Umfassendes Authentifizieren von Benutzern'
description: Hier wird beschrieben, wie Sie mit App Service-Authentifizierung und -Autorisierung Ihre App Service-Apps umfassend schützen (einschließlich des Zugriffs auf Remote-APIs).
keywords: App Service, Azure App Service, AuthN, AuthZ, sicher, Sicherheit, mehrstufig, Azure Active Directory, Azure AD
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 09/23/2021
ms.custom: devx-track-csharp, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 37bc8bdad9a066bb3988cfd03e3c1f857246e5c6
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136884"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service"></a>Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service

::: zone pivot="platform-windows"  

Von [Azure App Service](overview.md) wird ein hochgradig skalierbarer Webhostingdienst mit Self-Patching bereitgestellt. Außerdem verfügt App Service über integrierte Unterstützung für die [Benutzerauthentifizierung und -autorisierung](overview-authentication-authorization.md). In diesem Tutorial wird beschrieben, wie Sie Ihre Apps per App Service-Authentifizierung und -Autorisierung schützen. Es wird eine ASP.NET Core-App mit einem Angular.js-Front-End als Beispiel verwendet. Für die App Service-Authentifizierung und -Autorisierung werden alle Sprachlaufzeiten unterstützt, und Sie erfahren im Tutorial, wie Sie diese auf Ihre bevorzugte Sprache anwenden.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) bietet einen hochgradig skalierbaren Webhostingdienst mit Self-Patching unter dem Linux-Betriebssystem. Außerdem verfügt App Service über integrierte Unterstützung für die [Benutzerauthentifizierung und -autorisierung](overview-authentication-authorization.md). In diesem Tutorial wird beschrieben, wie Sie Ihre Apps per App Service-Authentifizierung und -Autorisierung schützen. Es wird eine ASP.NET Core-App mit einem Angular.js-Front-End als Beispiel verwendet. Für die App Service-Authentifizierung und -Autorisierung werden alle Sprachlaufzeiten unterstützt, und Sie erfahren im Tutorial, wie Sie diese auf Ihre bevorzugte Sprache anwenden.

::: zone-end

![Einfache Authentifizierung und Autorisierung](./media/tutorial-auth-aad/simple-auth.png)

Außerdem wird veranschaulicht, wie Sie eine mehrstufige App schützen, indem Sie im Namen des authentifizierten Benutzers auf eine geschützte Back-End-API zugreifen – sowohl [über Servercode](#call-api-securely-from-server-code) als auch [über Browsercode](#call-api-securely-from-browser-code).

![Erweiterte Authentifizierung und Autorisierung](./media/tutorial-auth-aad/advanced-auth.png)

Dies sind nur einige mögliche Authentifizierungs- und Autorisierungsszenarien in App Service. 

Hier ist eine umfassendere Liste mit den Kenntnissen angegeben, die im Tutorial vermittelt werden:

> [!div class="checklist"]
> * Aktivieren der integrierten Authentifizierung und Autorisierung
> * Schützen von Apps vor nicht authentifizierten Anforderungen
> * Verwenden von Azure Active Directory als Identitätsanbieter
> * Zugreifen auf eine Remote-App im Namen des angemeldeten Benutzers
> * Schützen von Dienst-zu-Dienst-Aufrufen per Tokenauthentifizierung
> * Verwenden von Zugriffstoken aus Servercode
> * Verwenden von Zugriffstoken aus Clientcode (Browser)

Die Schritte in diesem Tutorial können unter macOS, Linux und Windows ausgeführt werden.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- <a href="https://git-scm.com/" target="_blank">Installation von Git</a>
- <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Installation des aktuellen .NET Core 3.1 SDK</a>
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-local-net-core-app"></a>Erstellen einer lokalen .NET Core-App

In diesem Schritt richten Sie das lokale .NET Core-Projekt ein. Sie verwenden dasselbe Projekt, um eine Back-End-API-App und eine Front-End-Web-App bereitzustellen.

### <a name="clone-and-run-the-sample-application"></a>Klonen und Ausführen der Beispielanwendung

1. Führen Sie die folgenden Befehle aus, um das Beispielrepository zu klonen und auszuführen.

    ```bash
    git clone https://github.com/Azure-Samples/dotnet-core-api
    cd dotnet-core-api
    dotnet run
    ```
    
1. Navigieren Sie zu `http://localhost:5000`, und versuchen Sie, Todo-Elemente hinzuzufügen, zu bearbeiten und zu entfernen. 

    ![Lokal ausgeführte ASP.NET Core-API](./media/tutorial-auth-aad/local-run.png)

1. Drücken Sie zum Beenden von ASP.NET Core im Terminal `Ctrl+C`.

1. Stellen Sie sicher, dass der Standardbranch `main` ist.

    ```bash
    git branch -m main
    ```
    
    > [!TIP]
    > Die Änderung des Branchnamens ist für App Service nicht erforderlich. Da aber viele Repositorys ihren Standardbranch in `main` ändern, zeigt Ihnen dieses Tutorial auch, wie Sie ein Repository aus `main` bereitstellen. Weitere Informationen finden Sie unter [Ändern des Bereitstellungsbranches](deploy-local-git.md#change-deployment-branch).

## <a name="deploy-apps-to-azure"></a>Bereitstellen von Apps für Azure

In diesem Schritt stellen Sie das Projekt für zwei App Service-Apps bereit. Eine ist das Front-End-App und die andere ist die Back-End-App.

### <a name="configure-a-deployment-user"></a>Konfigurieren eines Bereitstellungsbenutzers

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

::: zone pivot="platform-windows"  

Führen Sie in Cloud Shell die folgenden Befehle aus, um zwei Windows-Web-Apps zu erstellen. Ersetzen Sie _\<front-end-app-name>_ und _\<back-end-app-name>_ durch zwei global eindeutige App-Namen (gültige Zeichen sind `a-z`, `0-9` und `-`). Weitere Informationen zu den einzelnen Befehlen finden Sie unter [Hosten einer RESTful-API mit CORS in Azure App Service](app-service-web-tutorial-rest-api.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

::: zone pivot="platform-linux"

Führen Sie in der Cloud Shell die folgenden Befehle aus, um zwei Web-Apps zu erstellen. Ersetzen Sie _\<front-end-app-name>_ und _\<back-end-app-name>_ durch zwei global eindeutige App-Namen (gültige Zeichen sind `a-z`, `0-9` und `-`). Weitere Informationen zu den einzelnen Befehlen finden Sie unter [Schnellstart: Erstellen einer ASP.NET Core-Web-App in Azure](quickstart-dotnetcore.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE --is-linux
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

> [!NOTE]
> Speichern Sie die URLs der Git-Remotespeicherorte für Ihre Front-End-App und Back-End-App, die in der Ausgabe von `az webapp create` angezeigt werden.
>

### <a name="push-to-azure-from-git"></a>Übertragen von Git an Azure mithilfe von Push

1. Da Sie den Branch `main` bereitstellen, müssen Sie den Standardbereitstellungsbranch für Ihre beiden App Service-Apps auf `main` festlegen (siehe [Ändern des Bereitstellungsbranchs](deploy-local-git.md#change-deployment-branch)). Legen Sie die App-Einstellung `DEPLOYMENT_BRANCH` in Cloud Shell mit dem Befehl [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) fest. 

    ```azurecli-interactive
    az webapp config appsettings set --name <front-end-app-name> --resource-group myAuthResourceGroup --settings DEPLOYMENT_BRANCH=main
    az webapp config appsettings set --name <back-end-app-name> --resource-group myAuthResourceGroup --settings DEPLOYMENT_BRANCH=main
    ```

1. Führen Sie im _lokalen Terminalfenster_ die folgenden Git-Befehle aus, um die Bereitstellung für die Back-End-App durchzuführen. Ersetzen Sie _\<deploymentLocalGitUrl-of-back-end-app>_ durch die URL des Git-Remotespeicherorts, die Sie unter [Erstellen von Azure-Ressourcen](#create-azure-resources) gespeichert haben. Wenn Sie von der Git-Anmeldeinformationsverwaltung zur Eingabe von Anmeldeinformationen aufgefordert werden, müssen Sie [Ihre Anmeldeinformationen für die Bereitstellung](deploy-configure-credentials.md) eingeben (nicht die Anmeldeinformationen, die Sie für die Anmeldung beim Azure-Portal verwenden).

    ```bash
    git remote add backend <deploymentLocalGitUrl-of-back-end-app>
    git push backend main
    ```

1. Führen Sie im lokalen Terminalfenster die folgenden Git-Befehle aus, um den gleichen Code für die Front-End-App bereitzustellen. Ersetzen Sie _\<deploymentLocalGitUrl-of-front-end-app>_ durch die URL des Git-Remotespeicherorts, die Sie unter [Erstellen von Azure-Ressourcen](#create-azure-resources) gespeichert haben.

    ```bash
    git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
    git push frontend main
    ```

### <a name="browse-to-the-apps"></a>Navigieren zu den Apps

Navigieren Sie in einem Browser zu den folgenden URLs, um verfolgen zu können, wie die beiden Apps ausgeführt werden.

```
http://<back-end-app-name>.azurewebsites.net
http://<front-end-app-name>.azurewebsites.net
```

:::image type="content" source="./media/tutorial-auth-aad/azure-run.png" alt-text="Screenshot: Azure App Service-REST-API-Beispiel in einem Browserfenster mit einer Aufgabenlisten-App":::

> [!NOTE]
> Wenn Ihre App neu gestartet wird, fällt Ihnen ggf. auf, dass neue Daten gelöscht wurden. Dieses Verhalten ist erwünscht, da für die ASP.NET Core-Beispiel-App eine In-Memory Database verwendet wird.
>
>

## <a name="call-back-end-api-from-front-end"></a>Aufrufen der Back-End-API vom Front-End

In diesem Schritt legen Sie für den Servercode der App fest, dass auf die Back-End-API zugegriffen werden soll. Später können Sie den authentifizierten Zugriff vom Front-End auf das Back-End aktivieren.

### <a name="modify-front-end-code"></a>Ändern des Front-End-Codes

1. Öffnen Sie _Controllers/TodoController.cs_ im lokalen Repository. Fügen Sie am Anfang der `TodoController`-Klasse die folgenden Zeilen hinzu, und ersetzen Sie _\<back-end-app-name>_ durch den Namen Ihrer Back-End-App:

    ```cs
    private static readonly HttpClient _client = new HttpClient();
    private static readonly string _remoteUrl = "https://<back-end-app-name>.azurewebsites.net";
    ```

1. Suchen Sie nach der Methode mit `[HttpGet]`, und ersetzen Sie den Code in den geschweiften Klammern durch Folgendes:

    ```cs
    var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo");
    return JsonConvert.DeserializeObject<List<TodoItem>>(data);
    ```

    In der ersten Zeile wird für die Back-End-API-App der Aufruf `GET /api/Todo` durchgeführt.

1. Suchen Sie anschließend nach der Methode mit `[HttpGet("{id}")]`, und ersetzen Sie den Code in den geschweiften Klammern durch Folgendes:

    ```cs
    var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}");
    return Content(data, "application/json");
    ```

    In der ersten Zeile wird für die Back-End-API-App der Aufruf `GET /api/Todo/{id}` durchgeführt.

1. Suchen Sie anschließend nach der Methode mit `[HttpPost]`, und ersetzen Sie den Code in den geschweiften Klammern durch Folgendes:

    ```cs
    var response = await _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", todoItem);
    var data = await response.Content.ReadAsStringAsync();
    return Content(data, "application/json");
    ```

    In der ersten Zeile wird für die Back-End-API-App der Aufruf `POST /api/Todo` durchgeführt.

1. Suchen Sie anschließend nach der Methode mit `[HttpPut("{id}")]`, und ersetzen Sie den Code in den geschweiften Klammern durch Folgendes:

    ```cs
    var res = await _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", todoItem);
    return new NoContentResult();
    ```

    In der ersten Zeile wird für die Back-End-API-App der Aufruf `PUT /api/Todo/{id}` durchgeführt.

1. Suchen Sie anschließend nach der Methode mit `[HttpDelete("{id}")]`, und ersetzen Sie den Code in den geschweiften Klammern durch Folgendes:

    ```cs
    var res = await _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}");
    return new NoContentResult();
    ```

    In der ersten Zeile wird für die Back-End-API-App der Aufruf `DELETE /api/Todo/{id}` durchgeführt.

1. Speichern Sie alle Änderungen. Stellen Sie Ihre Änderungen im lokalen Terminalfenster für die Front-End-App bereit, indem Sie die folgenden Git-Befehle verwenden:

    ```bash
    git add .
    git commit -m "call back-end API"
    git push frontend main
    ```

### <a name="check-your-changes"></a>Überprüfen der Änderungen

1. Navigieren Sie zu `http://<front-end-app-name>.azurewebsites.net`, und fügen Sie einige Elemente hinzu, z.B. `from front end 1` und `from front end 2`.

1. Navigieren Sie zu `http://<back-end-app-name>.azurewebsites.net`, um die aus der Front-End-App hinzugefügten Elemente anzuzeigen. Fügen Sie außerdem einige Elemente hinzu, z.B. `from back end 1` und `from back end 2`. Aktualisieren Sie anschließend die Front-End-App, um zu prüfen, ob die Änderungen widergespiegelt werden.

    :::image type="content" source="./media/tutorial-auth-aad/remote-api-call-run.png" alt-text="Screenshot: Azure App Service-REST-API-Beispiel in einem Browserfenster mit einer Aufgabenlisten-App und Elementen, die aus der Front-End-App hinzugefügt werden":::

## <a name="configure-auth"></a>Konfigurieren der Authentifizierung

In diesem Schritt aktivieren Sie die Authentifizierung und Autorisierung für die beiden Apps. Außerdem konfigurieren Sie die Front-End-App so, dass ein Zugriffstoken generiert wird, mit dem Sie authentifizierte Aufrufe für die Back-End-App durchführen können.

Sie verwenden Azure Active Directory als Identitätsanbieter. Weitere Informationen finden Sie unter [Konfigurieren der Azure Active Directory-Authentifizierung für die App Services-Anwendung](configure-authentication-provider-aad.md).

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Aktivieren der Authentifizierung und Autorisierung für die Back-End-App

1. Wählen Sie im Menü [Azure-Portal](https://portal.azure.com) den Eintrag **Ressourcengruppen** aus, oder suchen und wählen Sie auf einer beliebigen Seite *Ressourcengruppen* aus.

1. Navigieren Sie in **Ressourcengruppen** zu Ihrer Ressourcengruppe, und wählen Sie sie aus. Wählen Sie in **Übersicht** die Verwaltungsseite Ihrer Back-End-App aus.

    :::image type="content" source="./media/tutorial-auth-aad/portal-navigate-back-end.png" alt-text="Screenshot: Fenster „Ressourcengruppen“ mit der Übersicht für eine Beispielressourcengruppe und der für Ihre Back-End-App ausgewählten Verwaltungsseite":::

1. Wählen Sie im linken Menü der Back-End-App die Option **Authentifizierung** aus, und klicken Sie dann auf **Identitätsanbieter hinzufügen**.

1. Wählen Sie auf der Seite **Identitäts Anbieter hinzufügen die Option** **Microsoft** als **Identitäts Anbieter** aus, um Microsoft-und Azure AD Identitäten anzumelden.

1. Übernehmen Sie die Standardeinstellungen, und wählen Sie **OK** aus:

    :::image type="content" source="./media/tutorial-auth-aad/configure-auth-back-end.png" alt-text="Screenshot: linkes Menü der Back-End-App mit der ausgewählten Option „Authentifizierung/Autorisierung“ und im rechten Menü ausgewählten Einstellungen":::

1. Die Seite **Authentifizierung** wird geöffnet. Kopieren Sie die **Client-ID** der Azure AD-Anwendung in Editor. Sie benötigen diesen Wert später noch.

    :::image type="content" source="./media/tutorial-auth-aad/get-application-id-back-end.png" alt-text="Screenshot: Fenster der Azure Active Directory-Einstellungen mit der Azure AD-App und dem Fenster der Azure AD-Anwendungen mit der zu kopierenden Client-ID":::

Wenn Sie den Vorgang hier abbrechen, verfügen Sie über eine eigenständige App, die bereits durch die App Service-Authentifizierung und -Autorisierung geschützt ist. In den übrigen Abschnitten erfahren Sie, wie Sie eine Multi-App-Lösung durch einen authentifizierten Benutzerflow vom Front-End zum Back-End schützen. 

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Aktivieren der Authentifizierung und Autorisierung für die Front-End-App

Führen Sie die gleichen Schritte für die Front-End-App aus, aber lassen Sie den letzten Schritt weg. Für die Front-End-App ist die Client-ID nicht erforderlich. Bleiben Sie jedoch auf der Seite **Authentifizierung** für die Front-End-App, da Sie sie im nächsten Schritt verwenden werden.

Wenn Sie möchten, können Sie zu `http://<front-end-app-name>.azurewebsites.net` navigieren. Sie sollten auf eine sichere Anmeldeseite geleitet werden. Nachdem Sie sich angemeldet haben, *können Sie über die Back-End-App immer noch nicht auf die Daten zugreifen*, da für die Back-End-App nun die Azure Active Directory-Anmeldung über die Front-End-App erforderlich ist. Sie müssen drei Schritte ausführen:

- Gewähren des Zugriffs auf das Back-End für das Front-End
- Konfigurieren von App Service für die Rückgabe eines verwendbaren Tokens
- Verwenden des Tokens im Code

> [!TIP]
> Falls Fehler auftreten und Sie die Einstellungen für die Authentifizierung/Autorisierung Ihrer App neu konfigurieren, können die Token im Tokenspeicher aus den neuen Einstellungen ggf. nicht neu generiert werden. Um sicherzustellen, dass Ihre Token neu generiert werden können, müssen Sie sich abmelden und neu an der App anmelden. Eine einfache Möglichkeit hierfür ist die Verwendung Ihres Browsers im privaten Modus und das Schließen und erneute Öffnen des Browsers im privaten Modus, nachdem Sie die Einstellungen in Ihren Apps geändert haben.

### <a name="grant-front-end-app-access-to-back-end"></a>Gewähren des Zugriffs auf das Back-End für die Front-End-App

Nachdem Sie die Authentifizierung und Autorisierung für beide Apps aktiviert haben, verfügen beide über Unterstützung durch eine AD-Anwendung. In diesem Schritt gewähren Sie für die Front-End-App Berechtigungen zum Zugreifen auf das Back-End im Namen des Benutzers. (In technischer Hinsicht erteilen Sie der _AD-Anwendung_ des Front-Ends die Berechtigungen zum Zugreifen auf die _AD-Anwendung_ des Back-Ends im Namen des Benutzers.)

1. Wählen Sie auf der Seite **Authentifizierung** für die Front-End-App unter **Identitätsanbieter** den Namen Ihrer Front-End-App aus. Diese App-Registrierung wurde automatisch für Sie generiert. Wählen Sie im linken Menü **API-Berechtigungen** aus.

1. Wählen Sie die Option **Berechtigung hinzufügen** und dann **Meine APIs** >  **\<back-end-app-name>** .

1. Wählen Sie auf der Seite **API-Berechtigungen anfordern** für die Back-End-App **Delegierte Berechtigungen** und **user_impersonation** und anschließend **Berechtigungen hinzufügen** aus.

    :::image type="content" source="./media/tutorial-auth-aad/select-permission-front-end.png" alt-text="Screenshot: Seite „API-Berechtigungen anfordern“ mit den ausgewählten Optionen „Delegierte Berechtigungen“ und „user_impersonation“ und der ausgewählten Schaltfläche „Berechtigungen hinzufügen“":::

### <a name="configure-app-service-to-return-a-usable-access-token"></a>Konfigurieren von App Service für die Rückgabe eines verwendbaren Zugriffstokens

Die Front-End-App verfügt jetzt über die erforderlichen Berechtigungen für den Zugriff auf die Back-End-App als angemeldeter Benutzer. In diesem Schritt konfigurieren Sie die App Service-Authentifizierung und -Autorisierung, um ein verwendbares Zugriffstoken für den Zugriff auf das Back-End zu erhalten. Für diesen Schritt benötigen Sie die Client-ID des Back-Ends, die Sie unter [Aktivieren der Authentifizierung und Autorisierung für die Back-End-App](#enable-authentication-and-authorization-for-back-end-app) kopiert haben.

Führen Sie in Cloud Shell die folgenden Befehle für die Front-End-App aus, um den Parameter `scope` der Authentifizierungseinstellung `identityProviders.azureActiveDirectory.login.loginParameters` hinzuzufügen. Ersetzen Sie *\<front-end-app-name>* und *\<back-end-client-id>* .

```azurecli-interactive
authSettings=$(az webapp auth show -g myAuthResourceGroup -n <front-end-app-name>)
authSettings=$(echo "$authSettings” | jq '.properties' | jq '.identityProviders.azureActiveDirectory.login += {"loginParameters":["scope=openid profile email offline_access api://<back-end-client-id>/user_impersonation"]}')
az webapp auth set --resource-group myAuthResourceGroup --name <front-end-app-name> --body "$authSettings"
```

Die Befehle fügen effektiv eine `loginParameters`-Eigenschaft mit zusätzlichen benutzerdefinierten Bereichen hinzu. Hier ist eine Beschreibung der angeforderten Bereiche angegeben:

- `openid`, `profile` und `email` werden von App Service bereits standardmäßig angefordert. Weitere Informationen finden Sie unter [OpenID Connect-Bereiche](../active-directory/develop/v2-permissions-and-consent.md#openid-connect-scopes).
- `api://<back-end-client-id>/user_impersonation` ist eine API, die unter Ihrer Back-End-App-Registrierung verfügbar gemacht wird. Dies ist der Bereich, über den Sie ein JWT-Token erhalten, in dem die Back-End-App als [Tokenzielgruppe](https://wikipedia.org/wiki/JSON_Web_Token) enthalten ist. 
- [offline_access](../active-directory/develop/v2-permissions-and-consent.md#offline_access) ist hier als Hilfe angegeben (falls Sie [Token aktualisieren](#when-access-tokens-expire) möchten).

> [!TIP]
> - Um den Bereich `api://<back-end-client-id>/user_impersonation` im Azure-Portal anzuzeigen, wechseln Sie zur Seite **Authentifizierung** für die Back-End-App, klicken auf den Link unter **Identitätsanbieter** und klicken dann im linken Menü auf **API verfügbar machen**.
> - Falls Sie die erforderlichen Bereiche stattdessen per Weboberfläche konfigurieren möchten, helfen Ihnen die Microsoft-Schritte unter [Aktualisieren von Authentifizierungstoken](configure-authentication-oauth-tokens.md#refresh-auth-tokens) weiter.
> - Für einige Bereiche ist die Einwilligung des Administrators oder Benutzers erforderlich. Diese Anforderung bewirkt, dass die Zustimmungsanforderungsseite angezeigt wird, wenn sich ein Benutzer bei der Front-End-App im Browser anknappt. Um diese Zustimmungsseite zu vermeiden, fügen Sie die App-Registrierung des Front-End als autorisierte Clientanwendung auf der Seite **API verfügbar machen** hinzu, indem Sie auf **Clientanwendung** hinzufügen klicken und die Client-ID der App-Registrierung des Front-Ends übergeben.

::: zone pivot="platform-linux"

> [!NOTE]
> Für Linux-Apps ist es vorübergehend erforderlich, eine Einstellung für die Versionseinstellungen für die Back-End-App-Registrierung zu konfigurieren. Konfigurieren Sie Cloud Shell mit den folgenden Befehlen. Achten Sie darauf, dass *\<back-end-client-id>* Sie durch die Client-ID Ihres Back-End ersetzen.
>
> ```azurecli-interactive
> id=$(az ad app show --id <back-end-client-id> --query objectId --output tsv)
> az rest --method PATCH --url https://graph.microsoft.com/v1.0/applications/$id --body "{'api':{'requestedAccessTokenVersion':2}}" 
> ```    

::: zone-end
    
Ihre Apps sind nun konfiguriert. Das Front-End kann jetzt mit einem geeigneten Zugriffstoken auf das Back-End zugreifen.

Informationen dazu, wie Sie das Zugriffstoken für andere Anbieter konfigurieren, finden Sie unter [Erweiterte Verwendung der Authentifizierung und Autorisierung in Azure App Service](configure-authentication-oauth-tokens.md#refresh-auth-tokens).

## <a name="call-api-securely-from-server-code"></a>Sicheres Aufrufen der API aus Servercode

In diesem Schritt passen Sie den zuvor geänderten Servercode so an, dass authentifizierte Aufrufe der Back-End-API durchgeführt werden können.

Ihre Front-End-App verfügt jetzt über die erforderliche Berechtigung und fügt den Anmeldeparametern die Client-ID des Back-Ends hinzu. So kann ein Zugriffstoken für die Authentifizierung bei der Back-End-App abgerufen werden. App Service stellt dieses Token für Ihren Servercode bereit, indem in jede authentifizierte Anforderung ein `X-MS-TOKEN-AAD-ACCESS-TOKEN`-Header eingefügt wird (siehe [Retrieve tokens in app code](configure-authentication-oauth-tokens.md#retrieve-tokens-in-app-code) (Abrufen von Token im App-Code)).

> [!NOTE]
> Diese Header werden für alle unterstützten Sprachen eingefügt. Sie greifen darauf zu, indem Sie jeweils das Standardmuster für eine Sprache verwenden.

1. Öffnen Sie _Controllers/TodoController.cs_ erneut im lokalen Repository. Fügen Sie unter dem Konstruktor `TodoController(TodoContext context)` den folgenden Code hinzu:

    ```cs
    public override void OnActionExecuting(ActionExecutingContext context)
    {
        base.OnActionExecuting(context);
    
        _client.DefaultRequestHeaders.Accept.Clear();
        _client.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
    }
    ```

    Mit diesem Code wird der HTTP-Standardheader `Authorization: Bearer <access-token>` allen API-Remoteaufrufen hinzugefügt. In der ASP.NET Core-Pipeline für die MVC-Anforderungsausführung wird `OnActionExecuting` direkt vor der jeweiligen Aktion ausgeführt, sodass alle ausgehenden API-Aufrufe jetzt über das Zugriffstoken verfügen.

1. Speichern Sie alle Änderungen. Stellen Sie Ihre Änderungen im lokalen Terminalfenster für die Front-End-App bereit, indem Sie die folgenden Git-Befehle verwenden:

    ```bash
    git add .
    git commit -m "add authorization header for server code"
    git push frontend main
    ```

1. Führen Sie die Anmeldung an `https://<front-end-app-name>.azurewebsites.net` erneut durch. Klicken Sie auf der Seite mit der Vereinbarung zur Nutzung der Benutzerdaten auf **Akzeptieren**.

    Es sollte nun möglich sein, dass Sie wie zuvor Daten für die Back-End-App erstellen, lesen, aktualisieren und löschen. Der einzige Unterschied ist, dass beide Apps jetzt per App Service-Authentifizierung und -Autorisierung geschützt sind (einschließlich Dienst-zu-Dienst-Aufrufe).

Glückwunsch! Ihr Servercode greift jetzt im Namen des authentifizierten Benutzers auf die Back-End-Daten zu.

## <a name="call-api-securely-from-browser-code"></a>Sicheres Aufrufen der API aus Browsercode

In diesem Schritt legen Sie fest, dass die Angular.js-App des Front-Ends auf die Back-End-API verweist. Auf diese Weise erfahren Sie, wie Sie das Zugriffstoken abrufen und damit API-Aufrufe für die Back-End-App durchführen.

Der Servercode hat Zugriff auf die Anforderungsheader, aber der Clientcode kann auf `GET /.auth/me` zugreifen, um die gleichen Zugriffstoken abzurufen (siehe [Retrieve tokens in app code](configure-authentication-oauth-tokens.md#retrieve-tokens-in-app-code) (Abrufen von Token im App-Code)).

> [!TIP]
> In diesem Abschnitt werden die HTTP-Standardmethoden verwendet, um sichere HTTP-Aufrufe zu demonstrieren. Sie können aber die [Microsoft-Authentifizierungsbibliothek für JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js) verwenden, um das Angular.js-Anwendungsmuster zu vereinfachen.
>

### <a name="configure-cors"></a>Konfigurieren von CORS

Aktivieren Sie CORS in Cloud Shell mithilfe des Befehls [`az webapp cors add`](/cli/azure/webapp/cors#az_webapp_cors_add) für die URL Ihres Clients. Ersetzen Sie die Platzhalter _\<back-end-app-name>_ und _\<front-end-app-name>_ .

```azurecli-interactive
az webapp cors add --resource-group myAuthResourceGroup --name <back-end-app-name> --allowed-origins 'https://<front-end-app-name>.azurewebsites.net'
```

Dieser Schritt bezieht sich nicht auf die Authentifizierung und Autorisierung. Er ist aber erforderlich, damit Ihr Browser die domänenübergreifenden API-Aufrufe aus Ihrer Angular.js-App zulässt. Weitere Informationen finden Sie unter [Hinzufügen der CORS-Funktion](app-service-web-tutorial-rest-api.md#add-cors-functionality).

### <a name="point-angularjs-app-to-back-end-api"></a>Verweisen auf die Back-End-API für die Angular.js-App

1. Öffnen Sie _wwwroot/index.html_ im lokalen Repository.

1. Legen Sie die Variable `apiEndpoint` in Zeile 51 auf die HTTPS-URL Ihrer Back-End-App (`https://<back-end-app-name>.azurewebsites.net`) fest. Ersetzen Sie _\<back-end-app-name>_ durch den Namen Ihrer App in App Service.

1. Öffnen Sie _wwwroot/app/scripts/todoListSvc.js_ im lokalen Repository, und stellen Sie sicher, dass `apiEndpoint` allen API-Aufrufen vorangestellt wird. Ihre Angular.js-App ruft jetzt die Back-End-APIs auf. 

### <a name="add-access-token-to-api-calls"></a>Hinzufügen des Zugriffstokens zu API-Aufrufen

1. Fügen Sie der Liste in _wwwroot/app/scripts/todoListSvc.js_ oberhalb der Liste mit den API-Aufrufen (über der Zeile `getItems : function(){`) die folgende Funktion hinzu:

    ```javascript
    setAuth: function (token) {
        $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
    },
    ```

    Diese Funktion wird aufgerufen, um den Standardheader `Authorization` mit dem Zugriffstoken festzulegen. Sie rufen sie im nächsten Schritt auf.

1. Öffnen Sie _wwwroot/app/scripts/app.js_ im lokalen Repository, und suchen Sie nach dem folgenden Code:

    ```javascript
    $routeProvider.when("/Home", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
    }).otherwise({ redirectTo: "/Home" });
    ```

1. Ersetzen Sie den gesamten Codeblock durch den folgenden Code:

    ```javascript
    $routeProvider.when("/Home", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
        resolve: {
            token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
                return $http.get('/.auth/me').then(function (response) {
                    todoListSvc.setAuth(response.data[0].access_token);
                    return response.data[0].access_token;
                });
            }]
        },
    }).otherwise({ redirectTo: "/Home" });
    ```

    Mit der neuen Änderung wird die Zuordnung `resolve` hinzugefügt, die `/.auth/me` aufruft, und das Zugriffstoken wird festgelegt. Es wird sichergestellt, dass Sie über das Zugriffstoken verfügen, bevor Sie den Controller `todoListCtrl` instanziieren. Auf diese Weise ist das Token in allen API-Aufrufen vom Controller enthalten.

### <a name="deploy-updates-and-test"></a>Bereitstellen von Updates und Durchführen von Tests

1. Speichern Sie alle Änderungen. Stellen Sie Ihre Änderungen im lokalen Terminalfenster für die Front-End-App bereit, indem Sie die folgenden Git-Befehle verwenden:

    ```bash
    git add .
    git commit -m "add authorization header for Angular"
    git push frontend main
    ```

1. Navigieren Sie erneut zu `https://<front-end-app-name>.azurewebsites.net`. Es sollte nun möglich sein, dass Sie Daten für die Back-End-App direkt in der Angular.js-App erstellen, lesen, aktualisieren und löschen.

Glückwunsch! Ihr Clientcode greift jetzt im Namen des authentifizierten Benutzers auf die Back-End-Daten zu.

## <a name="when-access-tokens-expire"></a>Ablauf von Zugriffstoken

Das Zugriffstoken läuft nach einiger Zeit ab. Informationen dazu, wie Sie Zugriffstoken aktualisieren, ohne dass sich Benutzer erneut bei Ihrer App authentifizieren müssen, finden Sie unter [Erweiterte Verwendung der Authentifizierung und Autorisierung in Azure App Service](configure-authentication-oauth-tokens.md#refresh-auth-tokens).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie Azure-Ressourcen in einer Ressourcengruppe erstellt. Wenn Sie diese Ressourcen in Zukunft nicht mehr benötigen, löschen Sie die Ressourcengruppe, indem Sie den folgenden Befehl in Cloud Shell ausführen:

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

Die Ausführung dieses Befehls kann eine Minute in Anspruch nehmen.

<a name="next"></a>
## <a name="next-steps"></a>Nächste Schritte

Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Aktivieren der integrierten Authentifizierung und Autorisierung
> * Schützen von Apps vor nicht authentifizierten Anforderungen
> * Verwenden von Azure Active Directory als Identitätsanbieter
> * Zugreifen auf eine Remote-App im Namen des angemeldeten Benutzers
> * Schützen von Dienst-zu-Dienst-Aufrufen per Tokenauthentifizierung
> * Verwenden von Zugriffstoken aus Servercode
> * Verwenden von Zugriffstoken aus Clientcode (Browser)

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihrer App einen benutzerdefinierten DNS-Namen zuordnen.

> [!div class="nextstepaction"]
> [Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure App Service](app-service-web-tutorial-custom-domain.md)
