---
title: 'Tutorial: Verwenden von GitHub Actions für die Bereitstellung in App Service und die Verbindungsherstellung mit einer Datenbank'
description: Bereitstellen einer datenbankbasierten ASP.NET Core-App in Azure mit GitHub Actions
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/13/2021
ms.author: jukullam
ms.custom: github-actions-azure
ms.openlocfilehash: 01d4293b33eb0756dec8bc9ae870a972959ced89
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128701969"
---
# <a name="tutorial-use-github-actions-to-deploy-to-app-service-and-connect-to-a-database"></a>Tutorial: Verwenden von GitHub Actions für die Bereitstellung in App Service und die Verbindungsherstellung mit einer Datenbank

Hier wird beschrieben, wie Sie einen GitHub Actions-Workflow für die Bereitstellung einer ASP.NET Core-Anwendung mit einem [Azure SQL-Datenbank](../azure-sql/database/sql-database-paas-overview.md)-Back-End einrichten. Nach Abschluss verfügen Sie über eine ASP.NET-App, die in Azure ausgeführt wird und mit SQL-Datenbank verbunden ist. Sie verwenden zunächst eine [ARM-Vorlage](/azure/azure-resource-manager/templates/overview), um Ressourcen zu erstellen.

In diesem Tutorial werden keine Container verwendet. Falls Sie die Bereitstellung in einer containerisierten ASP.NET Core-Anwendung durchführen möchten, helfen Ihnen die Informationen zum Thema [Verwenden von GitHub Actions für die Bereitstellung in App Service für Container und die Verbindungsherstellung mit einer Datenbank](app-service-sql-github-actions.md) weiter.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Verwenden eines GitHub Actions-Workflows zum Hinzufügen von Ressourcen zu Azure mit einer ARM-Vorlage (Azure Resource Manager-Vorlage)
> - Verwenden eines GitHub Actions-Workflows zum Erstellen einer ASP.NET Core-Anwendung

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Um dieses Tutorial abzuschließen, benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ein GitHub-Konto. Falls Sie noch nicht über ein Konto verfügen, können Sie sich [kostenlos](https://github.com/join) registrieren.
  - Ein GitHub-Repository, in dem Sie Ihre Resource Manager-Vorlagen und Ihre Workflowdateien speichern können. Informationen zum Erstellen eines neuen Repositorys finden Sie [in diesem Hilfeartikel](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository).

## <a name="download-the-sample"></a>Herunterladen des Beispiels

[Verzweigen Sie das Beispielprojekt](https://github.com/Azure-Samples/dotnetcore-sqldb-ghactions) im Repository mit den Azure-Beispielen.

```
https://github.com/Azure-Samples/dotnetcore-sqldb-ghactions
```

## <a name="create-the-resource-group"></a>Ressourcengruppe erstellen

Öffnen Sie Azure Cloud Shell unter https://shell.azure.com. Sie können alternativ die Azure-Befehlszeilenschnittstelle verwenden, wenn Sie sie lokal installiert haben. (Weitere Informationen zu Cloud Shell finden Sie in der [Übersicht zu Cloud Shell](../cloud-shell/overview.md).)

```azurecli-interactive
az group create --name {resource-group-name} --location {resource-group-location}
```

## <a name="generate-deployment-credentials"></a>Generieren von Anmeldeinformationen für die Bereitstellung

Sie müssen sich mit einem Dienstprinzipal authentifizieren, damit das Ressourcenbereitstellungsskript funktioniert. Sie können mit dem Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) in der [Azure CLI](/cli/azure/) einen [Dienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) erstellen. Führen Sie diesen Befehl mit [Azure Cloud Shell](https://shell.azure.com/) im Azure-Portal oder durch Klicken auf die Schaltfläche **Ausprobieren** aus.

```azurecli-interactive
    az ad sp create-for-rbac --name "{service-principal-name}" --sdk-auth --role contributor --scopes /subscriptions/{subscription-id}
```

Ersetzen Sie im Beispiel die Platzhalter durch Ihre Abonnement-ID, den Ressourcengruppennamen und den Dienstprinzipalnamen. Die Ausgabe ist ein JSON-Objekt mit den Anmeldeinformationen für die Rollenzuweisung, die Zugriff auf Ihre App Service-App gewähren. Kopieren Sie dieses JSON-Objekt zur späteren Verwendung. Hilfe dazu finden Sie unter [Konfigurieren von Anmeldeinformationen für die Bereitstellung](https://github.com/Azure/login#configure-deployment-credentials).

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret-for-authentication"></a>Konfigurieren des GitHub-Geheimnisses für die Authentifizierung

Navigieren Sie in [GitHub](https://github.com/) zu Ihrem Repository, und wählen Sie **Einstellungen > Geheimnisse > Neues Geheimnis hinzufügen** aus.

Um [Anmeldeinformationen auf Benutzerebene](#generate-deployment-credentials) zu verwenden, fügen Sie die gesamte JSON-Ausgabe aus dem Azure CLI-Befehl in das Wertfeld des Geheimnisses ein. Geben Sie dem Geheimnis den Namen `AZURE_CREDENTIALS`.

## <a name="add-github-secrets-for-your-build"></a>Hinzufügen von GitHub-Geheimnissen für Ihren Build

1. Erstellen Sie in Ihrem GitHub-Repository für `SQLADMIN_PASS` und `SQLADMIN_LOGIN` [zwei neue Geheimnisse](https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository). Achten Sie darauf, dass Sie ein komplexes Kennwort auswählen. Andernfalls tritt beim Erstellungsschritt für den SQL-Datenbank-Server ein Fehler auf. Sie können nicht noch einmal auf dieses Kennwort zugreifen. Speichern Sie es daher separat.

2. Erstellen Sie ein Geheimnis vom Typ `AZURE_SUBSCRIPTION_ID` für Ihre Azure-Abonnement-ID. Falls Sie Ihre Abonnement-ID nicht kennen, können Sie diesen Befehl in der Azure Shell verwenden, um sie zu ermitteln. Kopieren Sie den Wert in der Spalte `SubscriptionId`.
    ```azurecli
    az account list -o table
    ```
 
## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

Der Workflow zum Erstellen von Azure-Ressourcen führt eine [ARM-Vorlage](/azure/azure-resource-manager/templates/overview) aus, um Ressourcen in Azure bereitzustellen. Der Workflow:

- Checkt den Quellcode mithilfe der [Check-Out-Aktion](https://github.com/marketplace/actions/checkout) aus.
- Meldet sich mit der [Azure-Anmeldeaktion](https://github.com/marketplace/actions/azure-login) bei Azure an und sammelt Umgebungs- und Azure-Ressourceninformationen.
- Stellt Ressourcen mit der [Aktion „Bereitstellen“ aus Azure Resource Manager](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) bereit.

So führen Sie den Workflow zum Erstellen von Azure-Ressourcen aus:

1. Öffnen Sie die Datei `infraworkflow.yml` in `.github/workflows` in Ihrem Repository.

1. Aktualisieren Sie den Wert von `AZURE_RESOURCE_GROUP` auf den Namen Ihrer Ressourcengruppe.

1. Legen Sie die Eingabe für `region` in Ihren Aktionen für die ARM-Bereitstellung auf Ihre Region fest. 
    1. Öffnen Sie `templates/azuredeploy.resourcegroup.parameters.json`, und aktualisieren Sie die Eigenschaft`rgLocation`, indem Sie Ihre Region angeben.
 
1. Navigieren Sie zu **Aktionen**, und wählen Sie **Workflow ausführen** aus.

   :::image type="content" source="media/github-actions-workflows/github-actions-run-workflow.png" alt-text="Führen Sie den GitHub Actions-Workflow aus, um Ressourcen hinzuzufügen.":::

1. Vergewissern Sie sich, dass Ihrer Aktion erfolgreich ausgeführt wurde, indem Sie auf der Seite **Aktionen** nach einem grünen Häkchen suchen.

   :::image type="content" source="media/github-actions-workflows/create-resources-success.png" alt-text="Erfolgreiche Ausführung von „Ressourcen erstellen“.":::

1. Navigieren Sie nach der Erstellung Ihrer Ressourcen zu **Aktionen**, wählen Sie die Option „Azure-Ressourcen erstellen“ aus, und deaktivieren Sie den Workflow. 
 
    :::image type="content" source="media/github-actions-workflows/disable-workflow-github-actions.png" alt-text="Deaktivieren des Workflows „Azure-Ressourcen erstellen“":::

## <a name="create-a-publish-profile-secret"></a>Erstellen eines Geheimnisses für ein Veröffentlichungsprofil

1. Öffnen Sie im Azure-Portal Ihren neuen App Service-Stagingslot, der über den Workflow `Create Azure Resources` erstellt wurde.

1. Wählen Sie **Veröffentlichungsprofil abrufen** aus.

1. Öffnen Sie die Veröffentlichungsprofildatei in einem Text-Editor, und kopieren Sie ihren Inhalt. 

1. Erstellen Sie ein neues GitHub-Geheimnis für `AZURE_WEBAPP_PUBLISH_PROFILE`. 

## <a name="build-and-deploy-your-app"></a>Erstellen und Bereitstellen Ihrer App

Gehen Sie wie folgt vor, um den Workflow zu erstellen und bereitzustellen:

1. Öffnen Sie Ihre `workflow.yaml`-Datei in `.github/workflows` in Ihrem Repository.

1. Überprüfen Sie, ob die Umgebungsvariablen für `AZURE_RESOURCE_GROUP`, `AZURE_WEBAPP_NAME`, `SQLSERVER_NAME` und `DATABASE_NAME` mit denen in `infraworkflow.yml` übereinstimmen.

1. Überprüfen Sie die erfolgreiche Bereitstellung Ihrer App, indem Sie auf die URL zugreifen, die in der Ausgabe für den Wechsel zum Produktionsslot angegeben ist. Die Beispiel-App „My TodoList“ sollte angezeigt werden. 
 
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie Ihr Beispielprojekt nicht mehr benötigen, sollten Sie Ihre Ressourcengruppe im Azure-Portal und Ihr Repository auf GitHub löschen. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu Azure und zur GitHub-Integration](/azure/developer/github/)
